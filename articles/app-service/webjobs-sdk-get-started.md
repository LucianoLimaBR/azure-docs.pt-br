---
title: Introdução ao WebJobs SDK - Azure
description: Introdução ao WebJobs SDK para o processamento em segundo plano controlado por evento. Saiba como acessar dados em serviços do Azure e serviços de terceiros.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: jeconnoc
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 2b2b8fe383ff4ee3d4b23c2c6e555b44e0cc088c
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390079"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Introdução ao SDK do Azure WebJobs para o processamento em segundo plano controlado por evento

Este artigo mostra como usar o Visual Studio 2019 para criar um projeto Azure WebJobs SDK, executá-lo localmente e, em seguida, implantá-lo no [serviço Azure app](overview.md). A versão 3. x do SDK de trabalhos Web dá suporte a aplicativos de console do .NET Core e do .NET Framework. Para saber mais sobre como trabalhar com o SDK de trabalhos Web, consulte [como usar o SDK do Azure WebJobs para processamento em segundo plano controlado por eventos](webjobs-sdk-how-to.md).

Este artigo mostra como implantar trabalhos Web como um aplicativo de console do .NET Core. Para implantar trabalhos Web como um aplicativo de console .NET Framework, consulte [webjobs como .NET Framework aplicativos de console](webjobs-dotnet-deploy-vs.md#webjobs-as-net-framework-console-apps). Se você estiver interessado no SDK de trabalhos Web versão 2. x, que só dá suporte a .NET Framework, consulte [desenvolver e implantar trabalhos Web usando o serviço de Azure app do Visual Studio](webjobs-dotnet-deploy-vs.md).

## <a name="prerequisites"></a>Pré-requisitos

* [Instale o Visual Studio 2019](/visualstudio/install/) com a carga de trabalho de **desenvolvimento do Azure** . Se você já tiver o Visual Studio, mas não tiver essa carga de trabalho, adicione a carga de trabalho selecionando **Ferramentas > Obter Ferramentas e Recursos**.

* Você deve ter [uma conta do Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) para publicar seu projeto do SDK de trabalhos Web no Azure.

## <a name="create-a-project"></a>Criar um projeto

1. No Visual Studio, selecione **criar um novo projeto**.

2. Selecione **aplicativo de console (.NET Core)** .

3. Nomeie o projeto *WebJobsSDKSample*e, em seguida, selecione **criar**.

   ![Caixa de diálogo Novo Projeto](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>Adicionar pacotes WebJobs NuGet

1. Instale a versão mais recente de 3. x estável do pacote NuGet `Microsoft.Azure.WebJobs.Extensions`, que inclui `Microsoft.Azure.WebJobs`.

     Aqui está o comando **Console do Gerenciador de Pacotes** para a versão 3.0.2:

     ```powershell
     Install-Package Microsoft.Azure.WebJobs.Extensions -version 3.0.2
     ```

## <a name="create-the-host"></a>Crie o Host

O host é o contêiner de tempo de execução para funções que escutam funções de gatilhos e chamadas. As etapas a seguir criam um host que implementa [`IHost`](/dotnet/api/microsoft.extensions.hosting.ihost), que é o host genérico no ASP.NET Core.

1. Em *Program.cs*, adicione uma instrução `using`:

    ```cs
    using Microsoft.Extensions.Hosting;
    ```

1. Substitua o método `Main` pelo seguinte código:

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

No ASP.NET Core, as configurações de host são definidas chamando métodos na instância do [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder). Para saber mais, confira [Host Genérico do .NET](/aspnet/core/fundamentals/host/generic-host). O método de extensão `ConfigureWebJobs` inicializa o host do WebJobs. No `ConfigureWebJobs`, você inicializa extensões de trabalhos Web específicas e define as propriedades dessas extensões.  

## <a name="enable-console-logging"></a>Habilitar o registro em log de console

Nesta seção, você configura o log de console que usa a [estrutura de log de ASP.NET Core](/aspnet/core/fundamentals/logging).

1. Instale a versão estável mais recente do pacote NuGet `Microsoft.Extensions.Logging.Console`, que inclui `Microsoft.Extensions.Logging`.

   Aqui está o comando **Console do Gerenciador de Pacotes** para a versão 2.2.0:

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.2.0
   ```

1. Em *Program.cs*, adicione uma instrução `using`:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. Não é possível chamar o método [`ConfigureLogging`](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) em [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder). O método [`AddConsole`](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) adiciona o log de console na configuração.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    O método `Main` agora tem esta aparência:

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

    Essa atualização faz o seguinte:

    * Desabilita o [registro em log do painel](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). O painel é um ferramenta de monitoramento herdada, e o registro em log do painel não é recomendado para cenários de produção de alta taxa de transferência.
    * Adiciona o provedor de console com [filtragem](webjobs-sdk-how-to.md#log-filtering) padrão.

Agora, você pode adicionar uma função disparada por mensagens que chegam em uma [fila de Armazenamento do Azure](../azure-functions/functions-bindings-storage-queue.md).

## <a name="install-the-storage-binding-extension"></a>Instalar a extensão de associação do Armazenamento

A partir da versão 3. x, você deve instalar explicitamente a extensão de associação de armazenamento exigida pelo SDK de trabalhos Web. Nas versões anteriores, as associações de armazenamento foram incluídas no SDK.

1. Instale a versão mais recente e estável do pacote NuGet, versão 3.x, do [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage). 

    Aqui está o comando **Package Manager Console** para a versão 3.0.4:

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.4
    ```

2. No método de extensão `ConfigureWebJobs`, chame o método `AddAzureStorage` na instância [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) ao inicializar a extensão de Armazenamento. Neste ponto, o método `ConfigureWebJobs` é semelhante ao seguinte exemplo:

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>Criar uma função

1. Clique com o botão direito do mouse no projeto, selecione **adicionar** > **novo item...** , escolha **classe**, nomeie o novo C# arquivo de classe *functions.cs*e selecione **Adicionar**.

1. Em Functions.cs, substitua o modelo gerado pelo seguinte código:

   ```cs
   using Microsoft.Azure.WebJobs;
   using Microsoft.Extensions.Logging;

   namespace WebJobsSDKSample
   {
       public class Functions
       {
           public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
           {
               logger.LogInformation(message);
           }
       }
   }
   ```

   O atributo `QueueTrigger` informa o tempo de execução para chamar esta função quando uma nova mensagem é gravada em uma fila de Armazenamento do Microsoft Azure chamada `queue`. O conteúdo da mensagem da fila é fornecido para o código do método no parâmetro `message`. O corpo do método é onde você processa os dados de gatilho. Neste exemplo, o código apenas registra a mensagem.

   O parâmetro `message` não precisa ser uma cadeia de caracteres. Você também pode associar a um objeto JSON, uma matriz de bytes ou um objeto [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage). [Consulte Uso de gatilho de fila](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Cada tipo de associação (como filas, blobs ou tabelas) tem um conjunto diferente de tipos de parâmetros que você pode associar.

## <a name="create-a-storage-account"></a>Crie uma conta de armazenamento

O emulador do Armazenamento do Microsoft Azure executado localmente não tem todos os recursos de que o WebJobs SDK precisa. Portanto, nesta seção, você cria uma conta de armazenamento no Azure e configura o projeto para usá-la. Se você já tiver uma conta de armazenamento, pule para a etapa 6.

1. Abra **Gerenciador de Servidores** no Visual studio e entre no Azure. Clique com o botão direito do mouse no nó do **Azure** e selecione **Conectar à Assinatura do Microsoft Azure**.

   ![Entrar no Azure](./media/webjobs-sdk-get-started/sign-in.png)

1. No nó do **Azure** no **Gerenciador de Servidores**, clique com o botão direito do mouse em **Armazenamento** e, em seguida, selecione **Criar Conta de Armazenamento**.

   ![Menu Criar Conta de Armazenamento](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. Na caixa de diálogo **Criar Conta de Armazenamento**, digite um nome exclusivo para a conta de armazenamento.

1. Escolha a mesma **Região** em que você criou seu aplicativo Serviço de Aplicativo ou uma região perto de você.

1. Clique em **Criar**.

   ![Criar Conta de Armazenamento](./media/webjobs-sdk-get-started/create-storage-account.png)

1. No nó **Armazenamento** no **Gerenciador de Servidores**, selecione a nova conta de armazenamento. Na janela **Propriedades**, selecione as reticências ( **...** ) à direita do campo de valor **Cadeia de Conexão**.

   ![Reticências de Cadeia de Conexão](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. Copie a cadeia de conexão e salve esse valor em algum lugar no qual você possa copiá-lo de novo imediatamente.

   ![Copiar cadeia de conexão](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-to-run-locally"></a>Configurar o armazenamento para executar localmente

O SDK do WebJobs procura a cadeia de conexão de armazenamento nas Configurações de Aplicativo no Azure. Quando você executa localmente, ele procura esse valor no arquivo de configuração local ou nas variáveis de ambiente.

1. Clique com o botão direito do mouse no projeto, selecione **adicionar** > **novo item...** , escolha o **arquivo de configuração JSON JavaScript**, nomeie o arquivo novo arquivo *appSettings. JSON* e selecione **Adicionar**. 

1. No novo arquivo, adicione um campo `AzureWebJobsStorage`, como no exemplo a seguir:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. Substitua *{storage connection string}* pela cadeia de conexão que você copiou anteriormente.

1. Selecione o arquivo *appSettings. JSON* em Gerenciador de soluções e, na janela **Propriedades** , defina **copiar para diretório de saída** como **copiar se mais recente**.

Posteriormente, você adicionará a mesma configuração de aplicativo de cadeia de conexão em seu aplicativo no Serviço de Aplicativo do Azure.

## <a name="test-locally"></a>Testar localmente

Nesta seção, você compila e executa o projeto localmente e dispara a função criando uma mensagem da fila.

1. Pressione **Ctrl + F5** para executar o projeto.

   O console mostra que o tempo de execução encontrou sua função e está aguardando as mensagens de fila o dispararem. A saída a seguir é gerada pelo host v3.x:

   ```console
    info: Microsoft.Azure.WebJobs.Hosting.JobHostService[0]
          Starting JobHost
    info: Host.Startup[0]
          Found the following functions:
          WebJobsSDKSample.Functions.ProcessQueueMessage

    info: Host.Startup[0]
          Job host started
    Application started. Press Ctrl+C to shut down.
    Hosting environment: Development
    Content root path: C:\WebJobsSDKSample\WebJobsSDKSample\bin\Debug\netcoreapp2.1\
   ```

1. Feche a janela do console.

1. No **Gerenciador de Servidores** no Visual Studio, expanda o nó para sua nova conta de armazenamento e, em seguida, clique com botão direito em **Filas**.

1. Selecione **Criar fila**.

1. Digite *queue* como o nome para a fila e selecione **OK**.

   ![Criar fila](./media/webjobs-sdk-get-started/create-queue.png)

1. Clique no botão direito do mouse para a nova fila e selecione **Exibir Fila**.

1. Selecione o ícone **Adicionar Mensagem**.

   ![Criar fila](./media/webjobs-sdk-get-started/create-queue-message.png)

1. Na caixa de diálogo **Adicionar Mensagem**, digite *Olá, Mundo!* como o **Texto da mensagem**e, em seguida, selecione **OK**. Agora há uma mensagem na fila.

   ![Criar fila](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Execute o projeto novamente.

   Como você usou o atributo `QueueTrigger` na função `ProcessQueueMessage`, o tempo de execução de WeJobs SDK escuta de mensagens da fila quando ele é iniciado. Ele localiza uma nova mensagem de fila na fila denominada *queue* e chama a função.

   Devido à [retirada do exponencial de sondagem de fila](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm), o tempo de execução poderá levar até 2 minutos para localizar a mensagem e chamar a função. Esse tempo de espera pode ser reduzido com a execução no [modo de desenvolvimento](webjobs-sdk-how-to.md#host-development-settings).

   A saída do console se assemelha a esta:

   ```console
    info: Function.ProcessQueueMessage[0]
          Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=2c319369-d381-43f3-aedf-ff538a4209b8)
    info: Function.ProcessQueueMessage[0]
          Trigger Details: MessageId: b00a86dc-298d-4cd2-811f-98ec39545539, DequeueCount: 1, InsertionTime: 1/18/2019 3:28:51 AM +00:00
    info: Function.ProcessQueueMessage.User[0]
          Hello World!
    info: Function.ProcessQueueMessage[0]
          Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=2c319369-d381-43f3-aedf-ff538a4209b8)
   ```

1. Feche a janela do console. 

1. Volte para a janela da fila e atualize-a. A mensagem foi removida, pois foi processada pela função que está sendo executada localmente. 

## <a name="add-application-insights-logging"></a>Adicionar registro em log do Application Insights

Quando o projeto é executado no Azure, você não pode monitorar a execução da função exibindo a saída do console. A solução de monitoramento que recomendamos é [Application Insights](../azure-monitor/app/app-insights-overview.md). Para saber mais, consulte [Monitorar Azure Functions](../azure-functions/functions-monitoring.md).

Nesta seção, execute as seguintes tarefas para configurar o registro em log do Application Insights antes de implantar no Azure:

* Verifique se você tem um aplicativo Serviço de Aplicativo e uma instância do Application Insights para trabalhar.
* Configure o aplicativo Serviço de Aplicativo para usar a instância do Application Insights e a conta de armazenamento que você criou anteriormente.
* Configure o projeto para registro em log no Application Insights.

### <a name="create-app-service-app-and-application-insights-instance"></a>Criar aplicativo Serviço de Aplicativo e a instância do Application Insights

1. Se você ainda não tiver um aplicativo Serviço de Aplicativo que você possa usar, [crie um](app-service-web-get-started-dotnet-framework.md). Ao criar seu aplicativo, você também pode criar um recurso do Application Insights conectado. Ao fazer isso, o `APPINSIGHTS_INSTRUMENTATIONKEY` será definido para você em seu aplicativo.

1. Se você ainda não tiver um aplicativo Serviço de Aplicativo que você possa usar, [crie um](../azure-monitor/app/create-new-resource.md ). Defina **Tipo de Aplicativo** como **Geral**e ignore as seções após **Copiar a chave de instrumentação**.

1. Se você já tiver um recurso do Application Insights que deseja usar, [copie a chave de instrumentação](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Definir configurações de aplicativo 

1. Na **Gerenciador de Servidores** no Visual Studio, expanda o **serviço de aplicativo** nó sob **Azure**.

1. Expanda o grupo de recursos em que o aplicativo Serviço de Aplicativo está e clique com o botão direito do mouse nesse aplicativo.

1. Selecione **Exibir Configurações**.

1. Na caixa **Cadeias de Conexão**, adicione a seguinte entrada.

   |name  |Cadeia de conexão  |Tipo de Banco de Dados|
   |---------|---------|------|
   |AzureWebJobsStorage | {a cadeia de conexão de armazenamento que você copiou anteriormente}|Personalizado|

1. Se a caixa **Configurações do Aplicativo** não tiver uma chave de instrumentação do Application Insights, adicione uma que você copiou anteriormente. (A chave de instrumentação já pode estar lá, dependendo de como você criou o aplicativo Serviço de Aplicativo.)

   |name  |Value  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {chave de instrumentação} |

1. Substitua *{chave de instrumentação}* pela chave de instrumentação do recurso do Application Insights que você está usando.

1. Clique em **Salvar**.

1. Adicione a conexão de Application Insights ao projeto para que você possa executá-lo localmente. No arquivo *appsettings.json* ou adicione o campo `APPINSIGHTS_INSTRUMENTATIONKEY`, como no exemplo a seguir:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    Substitua *{chave de instrumentação}* pela chave de instrumentação do recurso do Application Insights que você está usando.

1. Salve suas alterações.

### <a name="add-application-insights-logging-provider"></a>Adicionar provedor de registro em log do Application Insights

Para aproveitar o registro em log do [Application Insights](../azure-monitor/app/app-insights-overview.md), atualize seu código de registro em log para fazer o seguinte:

* Adicione um provedor de registro em log do Application Insights com [filtragem](webjobs-sdk-how-to.md#log-filtering) padrão; todas as informações e todos os logs de nível superior agora vão para o console e o Application Insights quando você estiver executando localmente.
* Coloque o objeto [LoggerFactory](./webjobs-sdk-how-to.md#logging-and-monitoring) em um bloco `using` para garantir que a saída do log seja liberada quando o host for encerrado.

1. Instale a versão 3.x estável mais recente do pacote NuGet para o provedor de registro em log do Application Insights: `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`.

   Aqui está o comando **Console do Gerenciador de Pacotes** para a versão 3.0.2:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version 3.0.2
   ```

1. Abra *Program.cs* e substitua o código no método `Main` pelo seguinte código:

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.UseEnvironment(EnvironmentName.Development);
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                    b.AddAzureStorage();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();

                    // If the key exists in settings, use it to enable Application Insights.
                    string instrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                    if (!string.IsNullOrEmpty(instrumentationKey))
                    {
                        b.AddApplicationInsights(o => o.InstrumentationKey = instrumentationKey);
                    }
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

    Isso adiciona o provedor de Application Insights ao registro em log, usando a chave que você adicionou anteriormente às configurações do aplicativo.

## <a name="test-application-insights-logging"></a>Testar registro em log do Application Insights

Nesta seção, você executa localmente de novo para verificar se os dados de registro em log agora estão indo para o Application Insights, bem como para o console.

1. Use **Gerenciador de Servidores** no Visual Studio para criar uma mensagem da fila, da mesma forma que fez [anteriores](#trigger-the-function-in-azure), exceto insira *Hello App Insights!* como o texto da mensagem.

1. Execute o projeto.

   O WebJobs SDK processa a mensagem da fila e você vê os logs na janela do console.

1. Feche a janela do console.

1. Abra o [portal do Azure](https://portal.azure.com/) e vá para o recurso do Application Insights.

1. Selecione **Pesquisar**.

   ![Selecionar Pesquisar](./media/webjobs-sdk-get-started/select-search.png)

1. Se você não vir a mensagem *Olá, App Insights!* , selecione **Atualizar** periodicamente por vários minutos. (Os logs não aparecem imediatamente porque leva algum tempo para o cliente do Application Insights liberar os logs que processa.)

   ![Logs no Application Insights](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. Feche a janela do console.

## <a name="deploy-as-a-webjob"></a>Implantar no Azure

Durante a implantação, você cria uma instância do serviço de aplicativo para executar suas funções. Quando você publica um aplicativo de console do .NET Core no serviço de aplicativo no Azure, ele automaticamente é executado como um WebJob. Para saber mais sobre a publicação, consulte [desenvolver e implantar trabalhos Web usando o Visual Studio](webjobs-dotnet-deploy-vs.md).

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

## <a name="trigger-the-function-in-azure"></a>Disparar a função no Azure

1. Verifique se você não está executando localmente (feche a janela de console se ainda estiver aberta). Caso contrário, a instância local poderá ser a primeira a processar as mensagens de fila que você criar.

1. Na página **Fila** do Visual Studio, adicione uma mensagem à fila como antes.

1. Atualize a página **Fila** e a nova mensagem desaparece porque foi processada pela função em execução no Azure.

   > [!TIP]
   > Quando você estiver testando no Azure, use o [modo de desenvolvimento](webjobs-sdk-how-to.md#host-development-settings) para garantir que uma função do gatilho de fila seja chamada imediatamente e evitar atrasos devido à [retirada do exponencial de sondagem de fila](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm).

### <a name="view-logs-in-application-insights"></a>Exibir logs no Application Insights

1. Abra o [portal do Azure](https://portal.azure.com/) e vá para o recurso do Application Insights.

1. Selecione **Pesquisar**.

1. Se você não vir a mensagem *Olá, Azure!* , selecione **Atualizar** periodicamente por vários minutos.

   Você vê os logs da função em execução em um WebJob, incluindo o texto *Hello Azure!* que digitou na seção anterior.

## <a name="add-an-input-binding"></a>Escolher uma associação de entrada

As associações de entrada simplificam o código que lê dados. Neste exemplo, a mensagem da fila será um nome de blob, e você usará o nome do blob para localizar e ler um blob no Armazenamento do Microsoft Azure.

1. Em *Functions.cs*, substitua o método `ProcessQueueMessage` pelo seguinte código:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   Nesse código, `queueTrigger` é uma [expressão de associação](../azure-functions/functions-bindings-expressions-patterns.md), o que significa que ele resolve para um valor diferente no tempo de execução.  No tempo de execução, ele tem o conteúdo da mensagem da fila.

1. Adicione um `using`:

   ```cs
   using System.IO;
   ```

1. Crie um contêiner de blob em sua conta de armazenamento.

   a. No **Gerenciador de Servidores** no Visual Studio, expanda o nó para sua conta de armazenamento, clique com botão direito **Blobs**e, em seguida, selecione **criar contêiner de Blob**.

   b. Na caixa de diálogo **Criar Contêiner de Blob**, digite *container* como o nome do contêiner e clique em **OK**.

1. Carregue o arquivo *Program.cs* para o contêiner de blob. (Esse arquivo é usado aqui como um exemplo; você pode carregar qualquer arquivo de texto e criar uma mensagem da fila com o nome do arquivo.)

   a. No **Gerenciador de Servidores**, clique duas vezes no nó para o contêiner que você acabou de criar.

   b. Na janela **Contêiner**, selecione o botão **Carregar**.

   ![Botão Carregar Blob](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Localize e selecione *Program.cs* e, em seguida, selecione **OK**.

1. Crie uma mensagem da fila na fila criada anteriormente, com *Program.cs* como o texto da mensagem.

   ![Mensagem da fila Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Execute o projeto localmente.

   A mensagem da fila dispara a função, que lê o blob e registra seu comprimento. A saída do console se assemelha a esta:

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```

## <a name="add-an-output-binding"></a>Adicionar uma associação de saída

As associações de saía simplificam o código que grava dados. Este exemplo modifica o anterior escrevendo uma cópia de blob em vez de registrar em log seu tamanho. Associações de armazenamento de blob são incluídas no pacote de extensão do Armazenamento do Azure instalados anteriormente.

1. Substitua o método `ProcessQueueMessage` pelo seguinte código:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

1. Crie outra mensagem de fila com *Program.cs* como o texto da mensagem.

1. Execute o projeto localmente.

   A mensagem da fila dispara a função, que lê o blob e registra seu comprimento e cria um novo blob. A saída do console é a mesma, mas quando você vai até a janela de contêiner de blob e seleciona **Atualizar**, é exibido um novo blob denominado *copy-Program.cs.*

## <a name="republish-the-updates-to-azure"></a>Republicar as atualizações no Azure

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto e selecione **Publicar**.

1. Na caixa de diálogo **publicar** , verifique se o perfil atual está selecionado e escolha **publicar**. Os resultados da publicação são detalhados na janela **saída** .
 
1. Verifique a função no Azure novamente carregando um arquivo para o contêiner de BLOB e adicionando uma mensagem à fila que é o nome do arquivo carregado. Você vê a mensagem ser removida da fila e uma cópia do arquivo criado no contêiner de BLOB. 

## <a name="next-steps"></a>Próximos passos

Este artigo mostrou como criar, executar e implantar um projeto webjobs SDK 3. x.

> [!div class="nextstepaction"]
> [Saiba mais sobre o SDK do WebJobs](webjobs-sdk-how-to.md)
