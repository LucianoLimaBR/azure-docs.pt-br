---
title: Criar uma imagem do registro de contêiner do Azure de um aplicativo
description: Use o comando AZ ACR Pack Build para criar uma imagem de contêiner de um aplicativo e enviar por push para o registro de contêiner do Azure, sem usar um Dockerfile.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 10/10/2019
ms.author: danlep
ms.openlocfilehash: b544820a0c496e0814de44790ea9c28878031a7d
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293910"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Criar e enviar por push uma imagem de um aplicativo usando um Buildpack nativo de nuvem

O comando CLI do Azure `az acr pack build` usa a ferramenta CLI do [`pack`](https://github.com/buildpack/pack) , de [Buildpacks](https://buildpacks.io/), para criar um aplicativo e enviar por push sua imagem para um registro de contêiner do Azure. Esse recurso fornece uma opção para criar rapidamente uma imagem de contêiner do código-fonte do aplicativo em node. js, Java e em outras linguagens sem precisar definir um Dockerfile.

Você pode usar o Azure Cloud Shell ou uma instalação local do CLI do Azure para executar os exemplos neste artigo. Se você quiser usá-lo localmente, a versão 2.0.70 ou posterior será necessária. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

> [!IMPORTANT]
> Esse recurso está atualmente na visualização. As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="use-the-build-command"></a>Usar o comando de Build

Para criar e enviar por push uma imagem de contêiner usando a nuvem nativa Buildpacks, execute o comando [AZ ACR Pack Build][az-acr-pack-build] . Enquanto o comando [AZ ACR Build][az-acr-build] cria e envia uma imagem de uma fonte Dockerfile e de um código relacionado, com `az acr pack build` você especifica uma árvore de origem do aplicativo diretamente.

No mínimo, especifique o seguinte ao executar `az acr pack build`:

* Um registro de contêiner do Azure em que você executa o comando
* Um nome de imagem e uma marca para a imagem resultante
* Um dos [locais de contexto com suporte](container-registry-tasks-overview.md#context-locations) para tarefas ACR, como um diretório local, um repositório GitHub ou um tarball remoto
* O nome de uma imagem do Buildpack Builder, como `cloudfoundry/cnb:0.0.12-bionic`.  

o `az acr pack build` dá suporte a outros recursos de comandos de tarefas ACR, incluindo a [execução de variáveis](container-registry-tasks-reference-yaml.md#run-variables) e logs de [execução de tarefas](container-registry-tasks-overview.md#view-task-logs) que são transmitidos e também salvos para recuperação posterior.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Exemplo: Criar imagem do node. js com o construtor de Cloud Foundry

O exemplo a seguir cria uma imagem de contêiner do aplicativo node. js no repositório [Azure-Samples/NodeJS-docs-Hello-World](https://github.com/Azure-Samples/nodejs-docs-hello-world) , usando o Construtor `cloudfoundry/cnb:0.0.12-bionic`:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --pull --builder cloudfoundry/cnb:0.0.12-bionic \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

Este exemplo cria a imagem `node-app` com a marca `1.0` e a envia para o registro de contêiner *myregistry* . Aqui, o nome do registro de destino é explicitamente anexado ao nome da imagem. Se não for especificado, a URL do registro será automaticamente precedida ao nome da imagem.

O parâmetro `--pull` especifica que o comando efetua pull da imagem mais recente do construtor.

A saída do comando mostra o progresso da criação e do envio por push da imagem. 

Depois que a imagem for criada com êxito, você poderá executá-la com o Docker, se ela estiver instalada. Primeiro entre no registro:

```azurecli
az acr login --name myregistry
```

Execute a imagem:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Navegue até `localhost:1337` em seu navegador favorito para ver o aplicativo Web de exemplo. Pressione `[Ctrl]+[C]` para parar o contêiner.

## <a name="example-build-java-image-with-heroku-builder"></a>Exemplo: Criar imagem Java com o Heroku Builder

O exemplo a seguir cria uma imagem de contêiner do aplicativo Java no repositório [buildpack/Sample-java-app](https://github.com/buildpack/sample-java-app) , usando o Construtor `heroku/buildpacks:18`:

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

Este exemplo cria a imagem `java-app` marcada com a ID de execução do comando e a envia para o registro de contêiner *myregistry* .

O parâmetro `--pull` especifica que o comando efetua pull da imagem mais recente do construtor.

A saída do comando mostra o progresso da criação e do envio por push da imagem. 

Depois que a imagem for criada com êxito, você poderá executá-la com o Docker, se ela estiver instalada. Primeiro entre no registro:

```azurecli
az acr login --name myregistry
```

Execute a imagem, substituindo sua marca de imagem para *RunId*:

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Navegue até `localhost:8080` em seu navegador favorito para ver o aplicativo Web de exemplo. Pressione `[Ctrl]+[C]` para parar o contêiner.


## <a name="next-steps"></a>Próximas etapas

Depois de criar e enviar por push uma imagem de contêiner com `az acr pack build`, você pode implantá-la como qualquer imagem para um destino de sua escolha. As opções de implantação do Azure incluem executá-lo no [serviço de aplicativo](../app-service/containers/tutorial-custom-docker-image.md) ou no [serviço kubernetes do Azure](../aks/tutorial-kubernetes-deploy-cluster.md), entre outros.

Para obter mais informações sobre recursos de tarefas de ACR, consulte [automatizar compilações de imagem de contêiner e manutenção com tarefas ACR](container-registry-tasks-overview.md).


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
