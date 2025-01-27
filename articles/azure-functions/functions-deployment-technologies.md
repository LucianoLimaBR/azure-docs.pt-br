---
title: Tecnologias de implantação no Azure Functions | Microsoft Docs
description: Conheça as diferentes maneiras como você pode implantar código para Azure Functions.
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 4d32a652219d48a2cc101259ea6b76fbfa910821
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2019
ms.locfileid: "72674958"
---
# <a name="deployment-technologies-in-azure-functions"></a>Tecnologias de implantação no Azure Functions

Você pode usar algumas tecnologias diferentes para implantar o código do projeto Azure Functions no Azure. Este artigo fornece uma lista completa dessas tecnologias, descreve quais tecnologias estão disponíveis para quais tipos de funções, explica o que acontece quando você usa cada método e fornece recomendações para o melhor método a ser usado em vários cenários . As várias ferramentas que dão suporte à implantação em Azure Functions são ajustadas para a tecnologia certa com base em seu contexto. Em geral, a implantação de zip é a tecnologia de implantação recomendada para Azure Functions.

## <a name="deployment-technology-availability"></a>Disponibilidade de tecnologia de implantação

O Azure Functions dá suporte ao desenvolvimento local de plataforma cruzada e à hospedagem no Windows e no Linux. Atualmente, três planos de hospedagem estão disponíveis:

+ [Utilização](functions-scale.md#consumption-plan)
+ [Premium](functions-scale.md#premium-plan)
+ [Dedicado (serviço de aplicativo)](functions-scale.md#app-service-plan)

Cada plano tem comportamentos diferentes. Nem todas as tecnologias de implantação estão disponíveis para cada tipo de Azure Functions. O gráfico a seguir mostra quais tecnologias de implantação têm suporte para cada combinação de sistema operacional e plano de hospedagem:

| Tecnologia de implantação | Consumo do Windows | Windows Premium (versão prévia) | Windows dedicado  | Consumo do Linux | Linux Premium (versão prévia) | Linux dedicado |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| URL do pacote externo<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| Implantação de zip |✔|✔|✔|✔|✔|✔|
| Contêiner do Docker | | | | |✔|✔|
| Implantação da Web |✔|✔|✔| | | |
| Controle do código-fonte |✔|✔|✔| |✔|✔|
| Git local<sup>1</sup> |✔|✔|✔| |✔|✔|
| Sincronização de nuvem<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| Edição do portal |✔|✔|✔| |✔<sup>2</sup>|✔<sup>2</sup>|

<sup>1</sup> tecnologia de implantação que requer [sincronização de gatilho manual](#trigger-syncing).  
<sup>2</sup> a edição do portal é habilitada apenas para gatilhos http e de temporizador para funções no Linux usando planos Premium e dedicados.

## <a name="key-concepts"></a>Conceitos principais

Alguns conceitos importantes são essenciais para entender como as implantações funcionam em Azure Functions.

### <a name="trigger-syncing"></a>Disparar sincronização

Quando você altera qualquer um de seus gatilhos, a infraestrutura do Functions deve estar ciente das alterações. A sincronização ocorre automaticamente para muitas tecnologias de implantação. No entanto, em alguns casos, você deve sincronizar manualmente seus gatilhos. Ao implantar suas atualizações referenciando uma URL de pacote externo, git local, sincronização de nuvem ou FTP, você deve sincronizar manualmente seus gatilhos. Você pode sincronizar gatilhos de uma destas três maneiras:

* Reinicie seu aplicativo de funções no portal do Azure
* Envie uma solicitação HTTP POST para `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` usando a [chave mestra](functions-bindings-http-webhook.md#authorization-keys).
* Enviar uma solicitação HTTP POST para `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`. Substitua os espaços reservados por sua ID de assinatura, nome do grupo de recursos e o nome do seu aplicativo de funções.

### <a name="remote-build"></a>Build remoto

Azure Functions pode executar compilações automaticamente no código que recebe após implantações de zip. Essas compilações se comportam um pouco diferente dependendo se seu aplicativo está em execução no Windows ou no Linux. As compilações remotas não são executadas quando um aplicativo tiver sido previamente definido para execução em execução no modo [de pacote](run-functions-from-deployment-package.md) . Para saber como usar a compilação remota, navegue até a [implantação de zip](#zip-deploy).

> [!NOTE]
> Se você estiver tendo problemas com a compilação remota, pode ser porque seu aplicativo foi criado antes de o recurso ser disponibilizado (1º de agosto de 2019). Tente criar um novo aplicativo de funções ou execute `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` para atualizar seu aplicativo de funções. Esse comando pode levar duas tentativas de sucesso.

#### <a name="remote-build-on-windows"></a>Build remoto no Windows

Todos os aplicativos de funções em execução no Windows têm um pequeno aplicativo de gerenciamento, o site do SCM (ou [kudu](https://github.com/projectkudu/kudu)). Esse site manipula grande parte da implantação e da lógica de compilação para Azure Functions.

Quando um aplicativo é implantado no Windows, comandos específicos de idioma, comoC#`dotnet restore` () ou `npm install` (JavaScript), são executados.

#### <a name="remote-build-on-linux"></a>Build remoto no Linux

Para habilitar a compilação remota no Linux, as seguintes [configurações de aplicativo](functions-how-to-use-azure-function-app-settings.md#settings) devem ser definidas:

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Por padrão, [Azure Functions Core Tools](functions-run-local.md) e a [extensão Azure Functions para Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) executar compilações remotas ao implantar no Linux. Por isso, ambas as ferramentas criam automaticamente essas configurações para você no Azure. 

Quando os aplicativos são criados remotamente no Linux, eles são [executados a partir do pacote de implantação](run-functions-from-deployment-package.md). 

##### <a name="consumption-plan"></a>Plano de consumo

Os aplicativos de funções do Linux em execução no plano de consumo não têm um site SCM/kudu, que limita as opções de implantação. No entanto, os aplicativos de funções no Linux em execução no plano de consumo oferecem suporte a compilações remotas.

##### <a name="dedicated-and-premium-preview-plans"></a>Planos dedicado e Premium (visualização)

Os aplicativos de funções em execução no Linux no [plano dedicado (serviço de aplicativo)](functions-scale.md#app-service-plan) e no [plano Premium](functions-scale.md#premium-plan) também têm um site do SCM/kudu limitado.

## <a name="deployment-technology-details"></a>Detalhes da tecnologia de implantação

Os métodos de implantação a seguir estão disponíveis no Azure Functions.

### <a name="external-package-url"></a>URL do pacote externo

Você pode usar uma URL de pacote externo para fazer referência a um arquivo de pacote remoto (. zip) que contém seu aplicativo de funções. O arquivo é baixado da URL fornecida e o aplicativo é executado em execução no modo [de pacote](run-functions-from-deployment-package.md) .

>__Como usá-lo:__ Adicione `WEBSITE_RUN_FROM_PACKAGE` às configurações do aplicativo. O valor dessa configuração deve ser uma URL (o local do arquivo de pacote específico que você deseja executar). Você pode adicionar configurações [no portal](functions-how-to-use-azure-function-app-settings.md#settings) ou [usando o CLI do Azure](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). 
>
>Se você usar o armazenamento de BLOBs do Azure, use um contêiner privado com uma [assinatura de acesso compartilhado (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) para dar acesso às funções ao pacote. Sempre que o aplicativo for reiniciado, ele buscará uma cópia do conteúdo. Sua referência deve ser válida durante o tempo de vida do aplicativo.

>__Quando usá-lo:__ A URL do pacote externo é o único método de implantação com suporte para Azure Functions em execução no Linux no plano de consumo, se o usuário não quiser que uma [compilação remota](#remote-build) ocorra. Ao atualizar o arquivo de pacote que um aplicativo de funções referencia, você deve [sincronizar os gatilhos manualmente](#trigger-syncing) para informar ao Azure que seu aplicativo foi alterado.

### <a name="zip-deploy"></a>Implantação de zip

Use a implantação de zip para enviar por push um arquivo. zip que contém seu aplicativo de funções para o Azure. Opcionalmente, você pode definir seu aplicativo para iniciar a [execução a partir do pacote](run-functions-from-deployment-package.md)ou especificar que ocorra uma [compilação remota](#remote-build) .

>__Como usá-lo:__ Implante usando sua ferramenta de cliente favorita: [Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure), o [Azure Functions Core Tools](functions-run-local.md)ou o [CLI do Azure](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure). Por padrão, essas ferramentas usam a implantação zip e são [executadas a partir do pacote](run-functions-from-deployment-package.md). As ferramentas principais e a extensão Visual Studio Code habilitam a [compilação remota](#remote-build) ao implantar no Linux. Para implantar manualmente um arquivo. zip em seu aplicativo de funções, siga as instruções em [implantar de um arquivo. zip ou de uma URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

>Ao implantar usando a implantação de zip, você pode definir seu aplicativo para ser [executado do pacote](run-functions-from-deployment-package.md). Para executar a partir do pacote, defina o valor de configuração `WEBSITE_RUN_FROM_PACKAGE` aplicativo como `1`. Recomendamos a implantação de zip. Ele produz tempos de carregamento mais rápidos para seus aplicativos e é o padrão para VS Code, o Visual Studio e o CLI do Azure. 

>__Quando usá-lo:__ A implantação de zip é a tecnologia de implantação recomendada para Azure Functions.

### <a name="docker-container"></a>Contêiner do Docker

Você pode implantar uma imagem de contêiner do Linux que contém seu aplicativo de funções.

>__Como usá-lo:__ Crie um aplicativo de funções do Linux no plano Premium ou dedicado e especifique a imagem de contêiner a ser executada. Você pode fazer isso de duas maneiras:
>
>* Crie um aplicativo de funções do Linux em um plano de serviço Azure App no portal do Azure. Para **publicar**, selecione **imagem do Docker**e configure o contêiner. Insira o local onde a imagem está hospedada.
>* Crie um aplicativo de funções do Linux em um plano do serviço de aplicativo usando o CLI do Azure. Para saber como, consulte [criar uma função no Linux usando uma imagem personalizada](functions-create-function-linux-custom-image.md#create-a-premium-plan).
>
>Para implantar em um aplicativo existente usando um contêiner personalizado, em [Azure Functions Core Tools](functions-run-local.md), use o comando [`func deploy`](functions-run-local.md#publish) .

>__Quando usá-lo:__ Use a opção de contêiner do Docker quando precisar de mais controle sobre o ambiente do Linux em que seu aplicativo de funções é executado. Esse mecanismo de implantação está disponível somente para funções em execução no Linux.

### <a name="web-deploy-msdeploy"></a>Implantação da Web (MSDeploy)

Implantação da Web pacotes e implanta seus aplicativos do Windows em qualquer servidor IIS, incluindo seus aplicativos de função em execução no Windows no Azure.

>__Como usá-lo:__ Use as [Ferramentas do Visual Studio para Azure Functions](functions-create-your-first-function-visual-studio.md). Desmarque a caixa **de seleção Executar do arquivo de pacote (recomendado)** .
>
>Você também pode baixar [Implantação da Web 3,6](https://www.iis.net/downloads/microsoft/web-deploy) e chamar `MSDeploy.exe` diretamente.

>__Quando usá-lo:__ Implantação da Web tem suporte e não tem problemas, mas o mecanismo preferencial é a [implantação de zip com a execução do pacote habilitado](#zip-deploy). Para saber mais, consulte o [Guia de desenvolvimento do Visual Studio](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Controle do código-fonte

Use o controle do código-fonte para conectar seu aplicativo de funções a um repositório git. Uma atualização de código nesse repositório dispara a implantação. Para obter mais informações, consulte o [wiki do kudu](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Como usá-lo:__ Use a central de implantação na área funções do portal para configurar a publicação do controle do código-fonte. Para obter mais informações, consulte [implantação contínua para Azure Functions](functions-continuous-deployment.md).

>__Quando usá-lo:__ Usar o controle do código-fonte é a melhor prática para as equipes que colaboram com seus aplicativos de funções. O controle do código-fonte é uma boa opção de implantação que permite pipelines de implantação mais sofisticados.

### <a name="local-git"></a>Git local

Você pode usar o Git local para enviar código por push do computador local para Azure Functions usando o git.

>__Como usá-lo:__ Siga as instruções em [implantação do git local para Azure app serviço](../app-service/deploy-local-git.md).

>__Quando usá-lo:__ Em geral, recomendamos que você use um método de implantação diferente. Ao publicar do git local, você deve [sincronizar os gatilhos manualmente](#trigger-syncing).

### <a name="cloud-sync"></a>Sincronização de nuvem

Use a sincronização de nuvem para sincronizar seu conteúdo do Dropbox e do OneDrive para Azure Functions.

>__Como usá-lo:__ Siga as instruções em [sincronizar conteúdo de uma pasta de nuvem](../app-service/deploy-content-sync.md).

>__Quando usá-lo:__ Em geral, recomendamos outros métodos de implantação. Ao publicar usando a sincronização de nuvem, você deve [sincronizar os gatilhos manualmente](#trigger-syncing).

### <a name="ftp"></a>FTP

Você pode usar o FTP para transferir arquivos diretamente para o Azure Functions.

>__Como usá-lo:__ Siga as instruções em [implantar conteúdo usando FTP/s](../app-service/deploy-ftp.md).

>__Quando usá-lo:__ Em geral, recomendamos outros métodos de implantação. Ao publicar usando FTP, você deve sincronizar os [gatilhos manualmente](#trigger-syncing).

### <a name="portal-editing"></a>Edição do portal

No editor baseado em portal, você pode editar diretamente os arquivos que estão em seu aplicativo de funções (essencialmente implantando sempre que você salvar suas alterações).

>__Como usá-lo:__ Para poder editar suas funções no portal do Azure, você deve ter [criado suas funções no portal](functions-create-first-azure-function.md). Para preservar uma única fonte de verdade, usar qualquer outro método de implantação torna sua função somente leitura e impede a edição continuada do Portal. Para retornar a um estado no qual você pode editar os arquivos na portal do Azure, você pode ativar manualmente o modo de edição para `Read/Write` e remover quaisquer configurações de aplicativo relacionadas à implantação (como `WEBSITE_RUN_FROM_PACKAGE`). 

>__Quando usá-lo:__ O portal é uma boa maneira de começar a usar o Azure Functions. Para um trabalho de desenvolvimento mais intenso, recomendamos que você use uma das seguintes ferramentas de cliente:
>
>* [Visual Studio Code](functions-create-first-function-vs-code.md)
>* [Azure Functions Core Tools (linha de comando)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

A tabela a seguir mostra os sistemas operacionais e idiomas que dão suporte à edição do portal:

| | Consumo do Windows | Windows Premium (versão prévia) | Windows dedicado | Consumo do Linux | Linux Premium (versão prévia)| Linux dedicado |
|-|:-----------------: |:-------------------------:|:-----------------:|:---------------------------:|:---------------:|:---------------:|
| C# | | | | | |
| C#Prescritiva |✔|✔|✔| |✔<sup> \*</sup> |✔<sup> \*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node. js) |✔|✔|✔| |✔<sup> \*</sup>|✔<sup> \*</sup>|
| Python (versão prévia) | | | | | | |
| PowerShell (visualização) |✔|✔|✔| | | |
| TypeScript (Node. js) | | | | | | |

<sup>*</sup> A edição do portal é habilitada apenas para gatilhos HTTP e de temporizador para funções no Linux usando planos Premium e dedicados.

## <a name="deployment-slots"></a>Slots de implantação

Ao implantar seu aplicativo de funções no Azure, você pode implantar em um slot de implantação separado em vez de diretamente na produção. Para obter mais informações sobre slots de implantação, consulte a documentação dos [Slots de implantação do Azure Functions](../app-service/deploy-staging-slots.md) para obter detalhes.

## <a name="next-steps"></a>Próximos passos

Leia estes artigos para saber mais sobre como implantar seus aplicativos de funções: 

+ [Implantação contínua para Azure Functions](functions-continuous-deployment.md)
+ [Entrega contínua usando o Azure DevOps](functions-how-to-azure-devops.md)
+ [Implantações zip para Azure Functions](deployment-zip-push.md)
+ [Executar o Azure Functions de um arquivo de pacote](run-functions-from-deployment-package.md)
+ [Automatizar a implantação de recursos para seu aplicativo de funções no Azure Functions](functions-infrastructure-as-code.md)
