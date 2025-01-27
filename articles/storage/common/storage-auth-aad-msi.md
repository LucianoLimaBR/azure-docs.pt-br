---
title: Autorizar o acesso a BLOBs e filas com Azure Active Directory e identidades gerenciadas para recursos do Azure – armazenamento do Azure
description: Suporte ao armazenamento de BLOBs e filas do Azure autorizando o acesso a recursos com Azure Active Directory e identidades gerenciadas para recursos do Azure. Você pode usar identidades gerenciadas para recursos do Azure para autorizar o acesso a BLOBs e filas de aplicativos executados em máquinas virtuais do Azure, aplicativos de funções, conjuntos de dimensionamento de máquinas virtuais e outros.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 833aa7dcce5c429b3005a378e93e2177df1eb0d4
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595174"
---
# <a name="authorize-access-to-blobs-and-queues-with-azure-active-directory-and-managed-identities-for-azure-resources"></a>Autorizar o acesso a BLOBs e filas com Azure Active Directory e identidades gerenciadas para recursos do Azure

O armazenamento de BLOBs e filas do Azure dão suporte à autenticação Azure Active Directory (Azure AD) com [identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md). Identidades gerenciadas para recursos do Azure podem autorizar o acesso a dados de BLOB e de fila usando as credenciais do Azure AD de aplicativos em execução em VMs (máquinas virtuais) do Azure, aplicativos de funções, conjuntos de dimensionamento de máquinas virtuais e outros serviços. Usando identidades gerenciadas para recursos do Azure junto com a autenticação do Azure AD, você pode evitar o armazenamento de credenciais com seus aplicativos que são executados na nuvem.  

Este artigo mostra como autorizar o acesso a dados de BLOB ou de fila de uma VM do Azure usando identidades gerenciadas para recursos do Azure. Ele também descreve como testar seu código no ambiente de desenvolvimento.

## <a name="enable-managed-identities-on-a-vm"></a>Habilitar identidades gerenciadas em uma VM

Antes de poder usar identidades gerenciadas para recursos do Azure para autorizar o acesso a BLOBs e filas de sua VM, você deve primeiro habilitar identidades gerenciadas para recursos do Azure na VM. Para saber como habilitar identidades gerenciadas para recursos do Azure, consulte um destes artigos:

- [Portal do Azure](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo do Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliotecas de cliente Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

Para obter mais informações sobre identidades gerenciadas, consulte [identidades gerenciadas para recursos do Azure](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="authenticate-with-the-azure-identity-library-preview"></a>Autenticar com a biblioteca de identidades do Azure (versão prévia)

A biblioteca de cliente de identidade do Azure para .NET (versão prévia) autentica uma entidade de segurança. Quando seu código está em execução no Azure, a entidade de segurança é uma identidade gerenciada para recursos do Azure.

Quando seu código está em execução no ambiente de desenvolvimento, a autenticação pode ser manipulada automaticamente ou pode exigir um logon do navegador, dependendo de quais ferramentas você está usando. O Microsoft Visual Studio dá suporte ao SSO (logon único), para que a conta de usuário ativa do Azure AD seja usada automaticamente para autenticação. Para obter mais informações sobre o SSO, consulte [logon único para aplicativos](../../active-directory/manage-apps/what-is-single-sign-on.md).

Outras ferramentas de desenvolvimento podem solicitar que você faça logon por meio de um navegador da Web. Você também pode usar uma entidade de serviço para autenticar do ambiente de desenvolvimento. Para obter mais informações, consulte [criar identidade para o aplicativo do Azure no portal](../../active-directory/develop/howto-create-service-principal-portal.md).

Após a autenticação, a biblioteca de cliente de identidade do Azure Obtém uma credencial de token. Essa credencial de token é encapsulada no objeto de cliente de serviço que você cria para executar operações no armazenamento do Azure. A biblioteca lida com isso para você com perfeição obtendo a credencial de token apropriada.

Para obter mais informações sobre a biblioteca de cliente de identidade do Azure, consulte [biblioteca de cliente de identidade do Azure para .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity).

## <a name="assign-rbac-roles-for-access-to-data"></a>Atribuir funções RBAC para acesso aos dados

Quando uma entidade de segurança do Azure AD tenta acessar dados de BLOB ou fila, essa entidade de segurança deve ter permissões para o recurso. Se a entidade de segurança é uma identidade gerenciada no Azure ou uma conta de usuário do Azure AD executando código no ambiente de desenvolvimento, a entidade de segurança deve ser atribuída a uma função de RBAC que concede acesso a dados de BLOB ou de fila no armazenamento do Azure. Para obter informações sobre a atribuição de permissões via RBAC, consulte a seção intitulada **atribuir funções RBAC para direitos de acesso** em [autorizar o acesso a BLOBs e filas do Azure usando o Azure Active Directory](../common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

## <a name="install-the-preview-packages"></a>Instalar os pacotes de visualização

Os exemplos neste artigo usam a versão de visualização mais recente da biblioteca de cliente de armazenamento do Azure para armazenamento de BLOBs. Para instalar o pacote de visualização, execute o seguinte comando no console do Gerenciador de pacotes NuGet:

```powershell
Install-Package Azure.Storage.Blobs -IncludePrerelease
```

Os exemplos neste artigo também usam a versão de visualização mais recente da [biblioteca de cliente de identidade do Azure para .net](https://www.nuget.org/packages/Azure.Identity/) para autenticar com as credenciais do Azure AD. Para instalar o pacote de visualização, execute o seguinte comando no console do Gerenciador de pacotes NuGet:

```powershell
Install-Package Azure.Identity -IncludePrerelease
```

## <a name="net-code-example-create-a-block-blob"></a>Exemplo de código .NET: criar um blob de blocos

Adicione as seguintes diretivas `using` ao seu código para usar as versões de visualização da identidade do Azure e das bibliotecas de cliente de armazenamento do Azure.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Azure.Identity;
using Azure.Storage;
using Azure.Storage.Sas;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
```

Para obter uma credencial de token que seu código pode usar para autorizar solicitações para o armazenamento do Azure, crie uma instância da classe [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) . O exemplo de código a seguir mostra como obter a credencial de token autenticado e usá-la para criar um objeto de cliente de serviço e, em seguida, usar o cliente de serviço para carregar um novo blob:

```csharp
async static Task CreateBlockBlobAsync(string accountName, string containerName, string blobName)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        string blobContents = "This is a block blob.";
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (StorageRequestFailedException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

> [!NOTE]
> Para autorizar solicitações em dados de BLOB ou de fila com o Azure AD, você deve usar HTTPS para essas solicitações.

## <a name="next-steps"></a>Próximos passos

- Para saber mais sobre as funções RBAC para o armazenamento do Azure, consulte [gerenciar direitos de acesso aos dados de armazenamento com o RBAC](storage-auth-aad-rbac.md).
- Para saber como autorizar o acesso a contêineres e filas de dentro de seus aplicativos de armazenamento, consulte [usar o Azure AD com aplicativos de armazenamento](storage-auth-aad-app.md).
- Para saber como executar comandos do CLI do Azure e do PowerShell com as credenciais do Azure AD, consulte [executar CLI do Azure ou comandos do PowerShell com as credenciais do Azure ad para acessar dados de BLOB ou de fila](storage-auth-aad-script.md).
