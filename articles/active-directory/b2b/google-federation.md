---
title: Adicionar o Google como um provedor de identidade para B2B-Azure Active Directory | Microsoft Docs
description: Associe ao Google para permitir que usuários convidados entrem em seus aplicativos Azure AD usando as próprias contas do Gmail
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b26679542753d5fb429c33e4220c23a3937c5cb
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430431"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users-preview"></a>Adicionar o Google como um provedor de identidade para usuários convidados B2B (visualização)

|     |
| --- |
| O Google Federation é um recurso de visualização pública do Azure Active Directory. Para saber mais sobre versões prévias, consulte os [Termos de Uso Complementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Ao configurar a Federação com o Google, você pode permitir que os usuários convidados entrem em seus aplicativos compartilhados e recursos com suas próprias contas do Gmail, sem precisar criar contas da Microsoft (MSAs) ou contas do Azure AD. O Google Federation foi projetado especificamente para usuários do gmail. Para federar com domínios do G Suite, use o [recurso de Federação direta](direct-federation.md) .
> [!NOTE]
> Os usuários convidados do Google precisam entrar usando um link que inclua o contexto do locatário (por exemplo, `https://myapps.microsoft.com/?tenantid=<tenant id>`, `https://portal.azure.com/<tenant id>` ou, no caso de um domínio padrão verificado, `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). Links diretos para aplicativos e recursos também funcionam desde que incluam o contexto do locatário. No momento, os usuários convidados não conseguem entrar usando pontos de extremidade sem contexto do locatário. Por exemplo, o uso de `https://myapps.microsoft.com`, `https://portal.azure.com` ou do ponto de extremidade comum das Equipes resultará em erro.
 
## <a name="what-is-the-experience-for-the-google-user"></a>Qual é a experiência do usuário do Google?
Quando você envia um convite para um usuário do Google Gmail, o usuário convidado deve acessar seus aplicativos ou recursos compartilhados utilizando um link que inclui o contexto do locatário. A experiência varia dependendo de a conexão ao Google já ter ocorrido ou não:
  - Se o usuário convidado não entrou no Google, a conexão ao Google será solicitada.
  - Se o usuário convidado já entrou no Google, ele poderá escolher a conta que deseja usar. Ele precisa escolher a conta que foi usada para convidá-lo.

Se o usuário convidado vir um erro de "cabeçalho muito longo", poderá tentar limpar os cookies ou abrir uma janela particular ou incógnita e tentar entrar novamente.

![Captura de tela mostrando a página de entrada do Google](media/google-federation/google-sign-in.png)

## <a name="step-1-configure-a-google-developer-project"></a>Etapa 1: Configurar um projeto de desenvolvedor do Google
Primeiramente, crie um novo projeto no Console de Desenvolvedores do Google para obter um ID do cliente e um segredo do cliente que possa adicionar posteriormente ao Azure AD. 
1. Acesse as APIs do Google em https://console.developers.google.com e entre com sua conta do Google. É recomendável que você use uma conta do Google de equipe compartilhada.
2. Crie um novo projeto: no painel, selecione **Criar projeto** e, em seguida, selecione **Criar**. Na página Novo projeto, insira um **Nome do projeto** e, em seguida, selecione **Criar**.
   
   ![Captura de tela mostrando uma nova página de projeto para o Google](media/google-federation/google-new-project.png)

3. Verifique se seu novo projeto está selecionado no menu do projeto. Em seguida, abra o menu no canto superior esquerdo e selecione **APIs e serviços** > **Credenciais**.

   ![Captura de tela mostrando a opção de credenciais da API do Google](media/google-federation/google-api.png)
 
4. Escolha a guia de **tela de consentimento do OAuth** e insira um **nome do aplicativo**. (Deixe as outras configurações.)

   ![Captura de tela mostrando a opção de ecrã de consentimento do Google OAuth](media/google-federation/google-oauth-consent-screen.png)

5. Role até a **autorizado domínios** seção e insira microsoftonline.com.

   ![Captura de tela mostrando a seção domínios autorizados](media/google-federation/google-oauth-authorized-domains.png)

6. Clique em **Salvar**.

7. Escolha a guia **credenciais** . No menu **criar credenciais** , escolha **ID do cliente OAuth**.

   ![Captura de tela mostrando a opção criar credenciais do Google APIs](media/google-federation/google-api-credentials.png)

8. Em **Tipo de aplicativo**, escolha **Aplicativo Web** e, em **URIs de redirecionamento autorizados**, insira estes URIs:
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(em que `<directory id>` é a ID do seu diretório)
   
     > [!NOTE]
     > Para localizar a ID do seu diretório, acesse https://portal.azure.com e, em **Azure Active Directory**, escolha **Propriedades** e copie o **ID do diretório**.

   ![Captura de tela mostrando a seção de URIs de redirecionamento autorizados](media/google-federation/google-create-oauth-client-id.png)

9. Clique em **Criar**. Copie a ID do cliente e o segredo do cliente, que você usará quando adicionar o provedor de identidade no portal do Azure AD.

   ![Captura de tela mostrando a ID do cliente OAuth e o segredo do cliente](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Etapa 2: Configurar a federação do Google no Azure AD 
Agora, você definirá a ID do cliente e o segredo do cliente do Google, seja inserindo-o no portal do Azure AD ou usando o PowerShell. Lembre-se de testar sua configuração da federação do Google. Convide-se usando um endereço do Gmail e tente resgatar o convite com sua conta do Google convidada. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Como configurar a federação do Google no portal do Azure AD 
1. Vá para o [Portal do Azure](https://portal.azure.com). No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **Relações organizacionais**.
3. Selecione **Provedores de identidade** e, em seguida, clique no botão **Google**.
4. Insira um nome. Em seguida, insira a ID do cliente e o segredo do cliente obtidos anteriormente. Clique em **Salvar**. 

   ![Captura de tela mostrando a página Adicionar provedor de identidade do Google](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>Como configurar a federação do Google usando o PowerShell
1. Instale a versão mais recente do módulo PowerShell for Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) do Azure AD.
2. Execute o seguinte comando: `Connect-AzureAD`.
3. No prompt de entrada, entre com a conta de Administrador Global gerenciada.  
4. Execute o comando a seguir: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Use a ID do cliente e o segredo do cliente do aplicativo criado na "Etapa 1: Configurar um projeto de desenvolvedor do Google". Para obter mais informações, consulte o artigo [New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview). 
 
## <a name="how-do-i-remove-google-federation"></a>Como fazer a remoção da federação do Google?
É possível excluir sua configuração da federação do Google. Se fizer isso, os usuários convidados do Google que já resgataram seu convite não conseguirão entrar, mas você poderá conceder novamente a eles o acesso a seus recursos se os excluir do diretório e convidá-los outra vez. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Para excluir a federação do Google no portal do Azure AD: 
1. Vá para o [Portal do Azure](https://portal.azure.com). No painel esquerdo, selecione **Azure Active Directory**. 
2. Selecione **Relações organizacionais**.
3. Selecione **provedores de identidade**.
4. Na linha do **Google** , selecione o menu de contexto ( **...** ) e, em seguida, selecione **excluir**. 
   
   ![Captura de tela mostrando a opção Excluir para o provedor de identidade social](media/google-federation/google-social-identity-providers.png)

1. Clique em **Sim** para confirmar a exclusão. 

### <a name="to-delete-google-federation-by-using-powershell"></a>Para excluir a federação do Google usando o PowerShell: 
1. Instale a versão mais recente do módulo PowerShell for Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)) do Azure AD.
2. Execute `Connect-AzureAD`.  
4. No prompt de logon, entre com a conta de Administrador Global gerenciada.  
5. Digite o seguinte comando:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Para obter mais informações, consulte [AzureADMSIdentityProvider remover](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 
