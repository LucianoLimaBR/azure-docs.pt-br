---
title: Como implantar modelos no serviço kubernetes do Azure
titleSuffix: Azure Machine Learning
description: Saiba como implantar seus modelos de Azure Machine Learning como um serviço Web usando o serviço kubernetes do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2019
ms.openlocfilehash: dfaa39b33839406ffdf484299cb520aebf011c7d
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299693"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Implantar um modelo em um cluster do serviço kubernetes do Azure

Saiba como usar Azure Machine Learning para implantar um modelo como um serviço Web no AKS (serviço kubernetes do Azure). O serviço kubernetes do Azure é bom para implantações de produção de grande escala. Use o serviço kubernetes do Azure se você precisar de um ou mais dos seguintes recursos:

- __Tempo de resposta rápido__.
- __Dimensionamento__ automático do serviço implantado.
- Opções de __aceleração de hardware__ , como a GPU e as FPGA (matrizes de porta programável por campo).

> [!IMPORTANT]
> O dimensionamento de cluster não é fornecido por meio do SDK do Azure Machine Learning. Para obter mais informações sobre como dimensionar os nós em um cluster AKS, consulte [dimensionar a contagem de nós em um cluster AKs](../../aks/scale-cluster.md).

Ao implantar no serviço kubernetes do Azure, você implanta em um cluster AKS que está __conectado ao seu espaço de trabalho__. Há duas maneiras de conectar um cluster AKS ao seu espaço de trabalho:

* Crie o cluster AKS usando o SDK do Azure Machine Learning, a CLI do Machine Learning, a página de aterrissagem do [portal do Azure](https://portal.azure.com) ou do [espaço de trabalho (versão prévia)](https://ml.azure.com). Esse processo conecta automaticamente o cluster ao espaço de trabalho.
* Anexe um cluster AKS existente ao seu espaço de trabalho Azure Machine Learning. Um cluster pode ser anexado usando o SDK do Azure Machine Learning, Machine Learning CLI ou o portal do Azure.

> [!IMPORTANT]
> O processo de criação ou anexo é uma tarefa ocasional. Depois que um cluster AKS estiver conectado ao espaço de trabalho, você poderá usá-lo para implantações. Você pode desanexar ou excluir o cluster AKS se não precisar mais dele. Depois de Detatched ou excluído, você não poderá mais implantar no cluster.

## <a name="prerequisites"></a>Pré-requisitos

- Um Workspace do Azure Machine Learning. Para obter mais informações, consulte [criar um Azure Machine Learning espaço de trabalho](how-to-manage-workspace.md).

- Um modelo de aprendizado de máquina registrado em seu espaço de trabalho. Se você não tiver um modelo registrado, consulte [como e onde implantar modelos](how-to-deploy-and-where.md).

- A [extensão CLI do Azure para o serviço Machine Learning](reference-azure-machine-learning-cli.md), o [SDK do Azure Machine Learning Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou a [extensão Azure Machine Learning Visual Studio Code](how-to-vscode-tools.md).

- Os trechos de código __Python__ neste artigo pressupõem que as seguintes variáveis sejam definidas:

    * `ws`-definir para seu espaço de trabalho.
    * `model`-definido para seu modelo registrado.
    * `inference_config`-definido como a configuração de inferência para o modelo.

    Para obter mais informações sobre como definir essas variáveis, consulte [como e onde implantar modelos](how-to-deploy-and-where.md).

- Os trechos de código da __CLI__ neste artigo pressupõem que você criou um documento `inferenceconfig.json`. Para obter mais informações sobre como criar este documento, consulte [como e onde implantar modelos](how-to-deploy-and-where.md).

## <a name="create-a-new-aks-cluster"></a>Criar um novo cluster AKS

**Tempo estimado**: Aproximadamente 20 minutos.

Criar ou anexar um cluster AKS é um processo de uma vez para seu espaço de trabalho. Você pode reutilizar esse cluster para várias implantações. Se você excluir o cluster ou o grupo de recursos que o contém, deverá criar um novo cluster na próxima vez que precisar implantá-lo. Você pode ter vários clusters AKS anexados ao seu espaço de trabalho.

> [!TIP]
> Se você quiser proteger o cluster AKS usando uma rede virtual do Azure, primeiro deverá criar a rede virtual. Para obter mais informações, consulte [experimentação segura e inferência com a rede virtual do Azure](how-to-enable-virtual-network.md#aksvnet).

Se você quiser criar um cluster AKS para __desenvolvimento__, __validação__e __teste__ em vez de produção, poderá especificar a finalidade do __cluster__ para o __teste de desenvolvimento__.

> [!WARNING]
> Se você definir `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, o cluster criado não será adequado para o tráfego de nível de produção e poderá aumentar os tempos de inferência. Os clusters de desenvolvimento/teste também não garantem a tolerância a falhas. Recomendamos pelo menos duas CPUs virtuais para clusters de desenvolvimento/teste.

Os exemplos a seguir demonstram como criar um novo cluster AKS usando o SDK e a CLI:

**Usando o SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> Para [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), se você escolher valores personalizados para `agent_count` e `vm_size` e `cluster_purpose` não for `DEV_TEST`, será necessário ter certeza de que `agent_count` multiplicado por `vm_size` é maior ou igual a 12 CPUs virtuais. Por exemplo, se você usar um `vm_size` de "Standard_D3_v2", que tem 4 CPUs virtuais, deverá escolher um `agent_count` de 3 ou superior.
>
> O SDK do Azure Machine Learning não fornece suporte para dimensionar um cluster AKS. Para dimensionar os nós no cluster, use a interface do usuário do cluster AKS no portal do Azure. Você só pode alterar a contagem de nós, não o tamanho da VM do cluster.

Para obter mais informações sobre as classes, os métodos e os parâmetros usados neste exemplo, consulte os seguintes documentos de referência:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.provisioning_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget. wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**Usando a CLI**

```azurecli
az ml computetarget create aks -n myaks
```

Para obter mais informações, consulte o [AZ ml computetarget Create Ask](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) Reference.

## <a name="attach-an-existing-aks-cluster"></a>Anexar um cluster AKS existente

**Tempo estimado:** Aproximadamente 5 minutos.

Se você já tiver o cluster AKS em sua assinatura do Azure e ele for inferior à versão 1,14, você poderá usá-lo para implantar a imagem.

> [!TIP]
> O cluster AKS existente pode estar em uma região do Azure do que seu espaço de trabalho Azure Machine Learning.
>
> Se você quiser proteger o cluster AKS usando uma rede virtual do Azure, primeiro deverá criar a rede virtual. Para obter mais informações, consulte [experimentação segura e inferência com a rede virtual do Azure](how-to-enable-virtual-network.md#aksvnet).

> [!WARNING]
> Ao anexar um cluster AKS a um espaço de trabalho, você pode definir como usará o cluster definindo o parâmetro `cluster_purpose`.
>
> Se você não definir o parâmetro `cluster_purpose` ou definir `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`, o cluster deverá ter pelo menos 12 CPUs virtuais disponíveis.
>
> Se você definir `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, o cluster não precisará ter 12 CPUs virtuais. Recomendamos pelo menos duas CPUs virtuais para desenvolvimento/teste. No entanto, um cluster configurado para desenvolvimento/teste não é adequado para o tráfego de nível de produção e pode aumentar os tempos de inferência. Os clusters de desenvolvimento/teste também não garantem a tolerância a falhas.

Para obter mais informações sobre como criar um cluster AKS usando o CLI do Azure ou o portal, consulte os seguintes artigos:

* [Criar um cluster AKS (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Criar um cluster AKS (Portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

Os exemplos a seguir demonstram como anexar um cluster AKS existente ao seu espaço de trabalho:

**Usando o SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)
```

Para obter mais informações sobre as classes, os métodos e os parâmetros usados neste exemplo, consulte os seguintes documentos de referência:

* [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute. Attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**Usando a CLI**

Para anexar um cluster existente usando a CLI, você precisa obter a ID de recurso do cluster existente. Para obter esse valor, use o comando a seguir. Substitua `myexistingcluster` pelo nome do seu cluster AKS. Substitua `myresourcegroup` pelo grupo de recursos que contém o cluster:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Esse comando retorna um valor semelhante ao texto a seguir:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Para anexar o cluster existente ao seu espaço de trabalho, use o comando a seguir. Substitua `aksresourceid` pelo valor retornado pelo comando anterior. Substitua `myresourcegroup` pelo grupo de recursos que contém seu espaço de trabalho. Substitua `myworkspace` pelo nome do espaço de trabalho.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Para obter mais informações, consulte a referência de [AKs AZ ml computetarget Attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) .

## <a name="deploy-to-aks"></a>Implantar no AKS

Para implantar um modelo no serviço kubernetes do Azure, crie uma __configuração de implantação__ que descreva os recursos de computação necessários. Por exemplo, número de núcleos e memória. Você também precisa de uma __configuração de inferência__, que descreve o ambiente necessário para hospedar o modelo e o serviço Web. Para obter mais informações sobre como criar a configuração de inferência, consulte [como e onde implantar modelos](how-to-deploy-and-where.md).

### <a name="using-the-sdk"></a>Usar o SDK

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Para obter mais informações sobre as classes, os métodos e os parâmetros usados neste exemplo, consulte os seguintes documentos de referência:

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Modelo. implantar](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)
* [WebService. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Usando a CLI

Para implantar usando a CLI, use o comando a seguir. Substitua `myaks` pelo nome do destino de computação AKS. Substitua `mymodel:1` pelo nome e versão do modelo registrado. Substitua `myservice` pelo nome para fornecer este serviço:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aks-deploy-config.md)]

Para obter mais informações, consulte a referência de [implantação do modelo AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) . 

### <a name="using-vs-code"></a>Usar o VS Code

Para obter informações sobre como usar VS Code, consulte [implantar no AKs por meio da extensão vs Code](how-to-vscode-tools.md#deploy-and-manage-models).

> [!IMPORTANT] 
> A implantação por meio do VS Code requer que o cluster AKS seja criado ou anexado ao seu espaço de trabalho com antecedência.

## <a name="web-service-authentication"></a>Autenticação do serviço Web

Ao implantar no serviço kubernetes do Azure, a autenticação __baseada em chave__ é habilitada por padrão. Você também pode habilitar __a autenticação baseada em token__ . A autenticação baseada em token exige que os clientes usem uma conta de Azure Active Directory para solicitar um token de autenticação, que é usado para fazer solicitações ao serviço implantado.

Para __desabilitar__ a autenticação, defina o parâmetro `auth_enabled=False` ao criar a configuração de implantação. O exemplo a seguir desabilita a autenticação usando o SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Para obter informações sobre como autenticar de um aplicativo cliente, consulte o [modelo consumir um Azure Machine Learning implantado como um serviço Web](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Autenticação com chaves

Se a autenticação de chave estiver habilitada, você poderá usar o método `get_keys` para recuperar uma chave de autenticação primária e secundária:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Se você precisar regenerar uma chave, use [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Autenticação com tokens

Para habilitar a autenticação de token, defina o parâmetro `token_auth_enabled=True` ao criar ou atualizar uma implantação. O exemplo a seguir habilita a autenticação de token usando o SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Se a autenticação de token estiver habilitada, você poderá usar o método `get_token` para recuperar um token JWT e o tempo de expiração desse token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Você precisará solicitar um novo token após o tempo `refresh_by` do token.
>
> A Microsoft recomenda enfaticamente que você crie seu espaço de trabalho Azure Machine Learning na mesma região que o cluster do serviço kubernetes do Azure. Para autenticar com um token, o serviço Web fará uma chamada para a região em que seu espaço de trabalho Azure Machine Learning é criado. Se a região do seu espaço de trabalho estiver indisponível, você não poderá buscar um token para o serviço Web mesmo que o cluster esteja em uma região diferente do seu espaço de trabalho. Isso efetivamente resulta na indisponibilidade da autenticação baseada em token até que a região do seu espaço de trabalho esteja disponível novamente. Além disso, quanto maior a distância entre a região do cluster e a região do seu espaço de trabalho, mais tempo será levado para buscar um token.

## <a name="update-the-web-service"></a>Atualizar o serviço Web

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Próximas etapas

* [Experimentação e inferência de segurança em uma rede virtual](how-to-enable-virtual-network.md)
* [Como implantar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Solução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Proteger serviços Web do Azure Machine Learning com SSL](how-to-secure-web-service.md)
* [Consumir um modelo de ML implantado como um serviço Web](how-to-consume-web-service.md)
* [Monitorar seus modelos do Azure Machine Learning com o Application Insights](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)
