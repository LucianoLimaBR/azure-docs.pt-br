---
title: Início Rápido da Configuração de Aplicativo do Azure com o .NET Framework | Microsoft Docs
description: Um Início Rápido para o uso da Configuração de Aplicativo do Azure com aplicativos .NET Framework
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 10/09/2019
ms.author: lcozzens
ms.openlocfilehash: 17b2e7272d499ce99d40d2ee52de1c7a5a1d0d04
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329807"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Início Rápido: Criar um aplicativo .NET Framework com a Configuração de Aplicativo do Azure

Neste guia de início rápido, você incorpora a Configuração de Aplicativos do Azure em um aplicativo com base em .NET Framework para centralizar o armazenamento e o gerenciamento de configurações de aplicativo separadas do seu código.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Criar um repositório de configurações de aplicativo

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecione **Gerenciador de Configurações** >  **+ Criar** para adicionar os seguintes pares chave-valor:

    | Chave | Valor |
    |---|---|
    | TestApp:Settings:Message | Dados da Configuração de Aplicativo do Azure |

    Deixe **Rótulo** e **Tipo de Conteúdo** vazios por enquanto.

## <a name="create-a-net-console-app"></a>Criar um aplicativo de console do .NET

1. Inicie o Visual Studio e selecione **Arquivo** > **Novo** > **Projeto**.

1. Em **Criar um projeto**, filtre o tipo de projeto **Console** e clique em **Aplicativo de Console (.NET Framework)** . Clique em **Próximo**.

1. Em **Configurar seu novo projeto**, insira um nome de projeto. Em **Framework**, selecione **.NET Framework 4.7.1** ou superior. Clique em **Criar**.

## <a name="connect-to-an-app-configuration-store"></a>Conectar um repositório de configurações de aplicativo

1. Clique com o botão direito do mouse no projeto e selecione **Gerenciar Pacotes do NuGet**. Na guia **Navegar**, pesquise e adicione os seguintes pacotes do NuGet ao projeto. Se você não conseguir localizá-los, marque a caixa de seleção **Incluir pré-lançamento**.

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    System.Configuration.ConfigurationManager version 4.6.0 or later
    ```

1. Atualize o arquivo *App.config* do projeto, conforme a seguir:

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" connectionString="${ConnectionString}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="ConnectionString" value ="Set via an environment variable - for example, dev, test, staging, or production connection string." />
    </appSettings>
    ```

   A cadeia de conexão do repositório de configurações de aplicativo é lida a partir da variável de ambiente `ConnectionString`. Adicione o `Environment` construtor de configuração antes de `MyConfigStore` na propriedade `configBuilders` da seção `appSettings`.

1. Abra *Program.cs* e atualize o método `Main` para usar a Configuração de Aplicativo, chamando `ConfigurationManager`.

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Defina uma variável de ambiente chamada **ConnectionString** como a cadeia de conexão do repositório de configurações de aplicativo. Se você usar o prompt de comando do Windows, execute o seguinte comando:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Se você usar o Windows PowerShell, execute o seguinte comando:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Reinicie o Visual Studio para permitir que a alteração tenha efeito. Pressione Ctrl + F5 para criar e executar o aplicativo do console.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um novo repositório de configurações de aplicativo e o utilizou com um aplicativo do console do .NET Framework. Para saber mais sobre como usar a Configuração de Aplicativo, vá para o próximo tutorial que demonstra a autenticação.

> [!div class="nextstepaction"]
> [Integração de identidade gerenciada](./howto-integrate-azure-managed-service-identity.md)
