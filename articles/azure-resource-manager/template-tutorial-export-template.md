---
title: Tutorial – Exportar um modelo do Azure Resource Manager do portal do Azure
description: Saiba como usar um modelo exportado para concluir o desenvolvimento do modelo.
services: azure-resource-manager
author: mumian
manager: carmonmills
ms.service: azure-resource-manager
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6e4f246cac0ecc1ab5942e522595f59c3625db8f
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243203"
---
# <a name="tutorial-use-exported-template-from-the-azure-portal"></a>Tutorial: Usar o modelo exportado do portal do Azure

Nesta série de tutoriais, você criou um modelo para implantar uma conta de armazenamento do Azure. Nos próximos dois tutoriais, você adicionará um *Plano do Serviço de Aplicativo* e um *website*. Em vez de criar modelos do zero, você aprenderá a exportar modelos do portal do Azure e a usar modelos de exemplo dos [modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/). Você personalizará esses modelos para seu uso. Este tutorial se concentra na exportação de modelos e na personalização do resultado para o modelo. Esse procedimento demora cerca de **14 minutos** para ser concluído.

## <a name="prerequisites"></a>Pré-requisitos

Recomendamos que você conclua o [tutorial sobre saídas](template-tutorial-add-outputs.md), não sendo, porém, necessário.

É necessário ter o Visual Studio Code com a extensão Ferramentas do Resource Manager e o Azure PowerShell ou a CLI do Azure. Para obter mais informações, confira [Ferramentas de modelo](template-tutorial-create-first-template.md#get-tools).

## <a name="review-your-template"></a>Examinar o modelo

No final do tutorial anterior, o modelo tinha o seguinte JSON:

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json)]

Esse modelo funciona bem para a implantação de contas de armazenamento, mas talvez você queira adicionar mais recursos a ele. Exporte um modelo de um recurso existente para obter rapidamente o JSON desse recurso.

## <a name="create-app-service-plan"></a>Criar plano de Serviço de Aplicativo

1. Entre no [Portal do Azure](https://portal.azure.com).
1. Selecione **Criar um recurso**.
1. Em **Pesquisar no Marketplace**, insira **Plano do Serviço de Aplicativo** e, em seguida, selecione **Plano do Serviço de Aplicativo**.  Não selecione **Plano do Serviço de Aplicativo (clássico)**
1. Selecione **Criar**.
1. Digite:

    - **Assinatura**: selecione sua assinatura do Azure.
    - **Grupo de Recursos**: Selecione **Criar** e, em seguida, especifique um nome. Forneça um nome de grupo de recursos diferente daquele que você está usando nesta série de tutoriais.
    - **Nome**: insira um nome para o Plano do Serviço de Aplicativo.
    - **Sistema Operacional**: selecione **Linux**.
    - **Região**: selecione uma localização do Azure. Por exemplo, **Centro dos EUA**.
    - **Tipo de preço**: para reduzir os custos, altere o SKU para **Básico B1** (em Desenvolvimento/Teste).

    ![Portal do modelo de exportação do modelo do Resource Manager](./media/template-tutorial-export-template/resource-manager-template-export.png)
1. Selecione **Examinar e criar**.
1. Selecione **Criar**. São necessários alguns instantes para a criação do recurso.

## <a name="export-the-template"></a>Exportar o modelo

1. Selecione **Ir para o recurso**.

    ![Ir para o recurso](./media/template-tutorial-export-template/resource-manager-template-export-go-to-resource.png)

1. Selecione **Exportar modelo**.

    ![Modelo de exportação do modelo do Resource Manager](./media/template-tutorial-export-template/resource-manager-template-export-template.png)

   O recurso Exportar Modelo usa o estado atual de um recurso e gera um modelo para implantá-lo. A exportação de um modelo pode ser uma maneira útil de obter rapidamente o JSON de que você precisa para implantar um recurso.

1. Copie a definição de **Microsoft.Web/serverfarms** e a definição de parâmetro para o modelo.

    ![Modelo exportado do modelo de exportação do modelo do Resource Manager](./media/template-tutorial-export-template/resource-manager-template-exported-template.png)

> [!IMPORTANT]
> Normalmente, o modelo exportado é mais detalhado do que o esperado ao criar um modelo. Por exemplo, o objeto do SKU no modelo exportado tem cinco propriedades. Esse modelo funciona, mas você pode simplesmente usar a propriedade **name**. Comece com o modelo exportado e, em seguida, modifique-o como desejar de acordo com suas necessidades.

## <a name="revise-the-existing-template"></a>Revisar o modelo existente

O modelo exportado fornece a maior parte do JSON necessário, mas você precisará personalizá-lo para o modelo. Preste atenção especial nas diferenças em parâmetros e variáveis entre o modelo e o modelo exportado. Obviamente, o processo de exportação não conhece os parâmetros e as variáveis que você já definiu no modelo.

O exemplo a seguir realça as adições ao modelo. Ele contém o código exportado, além de algumas alterações. Primeiro, ele altera o nome do parâmetro para que corresponda à convenção de nomenclatura. Em segundo lugar, ele usa o parâmetro de localização para a localização do Plano do Serviço de Aplicativo. Em terceiro lugar, ele remove o **name** do objeto **properties**, porque esse valor é redundante com a propriedade **name** no nível de recurso.

Copie o arquivo inteiro e substitua o modelo pelo conteúdo.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json?range=1-77&highlight=28-31,50-69)]

## <a name="deploy-the-template"></a>Implantar o modelo

Use a CLI do Azure ou o Azure PowerShell para implantar um modelo.

Caso você não tenha criado o grupo de recursos, confira [Criar grupo de recursos](template-tutorial-create-first-template.md#create-resource-group). O exemplo pressupõe que você tenha definido a variável **templateFile** como o caminho para o arquivo de modelo, conforme mostrado no [primeiro tutorial](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addappserviceplan `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-clitabazure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addappserviceplan \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

## <a name="verify-deployment"></a>Verificar implantação

Você pode verificar a implantação explorando o grupo de recursos no portal do Azure.

1. Entre no [Portal do Azure](https://portal.azure.com).
1. No menu à esquerda, selecione **Grupos de recursos**.
1. Selecione o grupo de recursos no qual você fez a implantação.
1. O grupo de recursos contém uma conta de armazenamento e um Plano do Serviço de Aplicativo.

## <a name="clean-up-resources"></a>Limpar recursos

Se você estiver passando para o próximo tutorial, não será necessário excluir o grupo de recursos.

Se estiver parando agora, o ideal será limpar os recursos implantados excluindo o grupo de recursos.

1. No portal do Azure, escolha **Grupos de recursos** do menu à esquerda.
2. No campo **Filtrar por nome**, insira o nome do grupo de recursos.
3. Selecione o nome do grupo de recursos.
4. Escolha **Excluir grupo de recursos** no menu superior.

## <a name="next-steps"></a>Próximas etapas

Você aprendeu a exportar um modelo do portal do Azure e a usar o modelo exportado para o desenvolvimento do modelo. Use também os modelos de Início Rápido do Azure para simplificar o desenvolvimento do modelo.

> [!div class="nextstepaction"]
> [Usar modelos de Início Rápido do Azure](template-tutorial-quickstart-template.md)
