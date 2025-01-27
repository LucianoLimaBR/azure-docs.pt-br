---
title: Listar seu aplicativo na galeria de aplicativos do Azure Active Directory | Microsoft Docs
description: Saiba como listar um aplicativo que oferece suporte a logon único na galeria de aplicativos do Azure Active Directory
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2019
ms.author: ryanwi
ms.reviewer: elisol, bryanla
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: c77657101f5cd8a117b2163386f6d551b7985458
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374081"
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Listar seu aplicativo na galeria de aplicativos do Azure Active Directory

Este artigo mostra como listar um aplicativo na Galeria de aplicativos Azure Active Directory (Azure AD), implementar o SSO (logon único) e gerenciar a listagem.

## <a name="what-is-the-azure-ad-application-gallery"></a>O que é a galeria de aplicativos do Azure Active Directory?

- Os clientes encontram a melhor experiência de logon único possível.
- A configuração do aplicativo é simples e mínima.
- Uma pesquisa rápida localiza seu aplicativo na galeria.
- Clientes do Azure Active Directory Premium, Básico e Gratuito podem usar essa integração.
- Os clientes mútuos recebem um tutorial de configuração passo a passo.
- Os clientes que usam o sistema para[scim](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)(gerenciamento de identidade entre domínios) podem usar o provisionamento para o mesmo aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

- Para aplicativos federados (Open ID e SAML/WS-enalimentado), o aplicativo deve oferecer suporte ao modelo de software como serviço (SaaS) para ser listado na Galeria de aplicativos do Azure AD. Os aplicativos da Galeria empresarial devem dar suporte a várias configurações de clientes e não a nenhum cliente específico.
- Para Open ID Connect, o aplicativo deve ter multilocatário e a [estrutura de consentimento do Azure ad](consent-framework.md) deve ser implementada corretamente para o aplicativo. O usuário pode enviar a solicitação de entrada para um ponto de extremidade comum para que qualquer cliente possa fornecer consentimento para o aplicativo. É possível controlar acesso de usuário com base na ID do locatário e o UPN do usuário recebido no token.
- Para SAML 2.0/WS-enalimentado, seu aplicativo deve ter a capacidade de fazer a integração de SSO de SAML/WS-alimentada no modo SP ou IDP. Verifique se esse recurso está funcionando corretamente antes de enviar a solicitação.
- Para SSO de senha, certifique-se de que seu aplicativo dá suporte à autenticação de formulário para que o cofre de senha possa ser feito para que o logon único funcione conforme o esperado.
- Você precisa de uma conta permanente para testar com pelo menos dois usuários registrados.

## <a name="submit-the-request-in-the-portal"></a>Enviar a solicitação no portal

Depois de testar se a integração do aplicativo funciona com o Azure AD, envie sua solicitação de acesso no [portal de rede do aplicativo](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Se você tiver uma conta do Office 365, use-a para entrar nesse portal. Caso contrário, use o conta Microsoft, como o Outlook ou hotmail, para entrar.

Se a seguinte página aparecer depois que você entrar, entre em contato com a [equipe de integração de SSO do Azure ad](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Forneça a conta de email que você deseja usar para enviar a solicitação. A equipe do Azure AD adicionará a conta no portal de rede de aplicativos da Microsoft.

![Mensagem de solicitação de acesso no portal do SharePoint](./media/howto-app-gallery-listing/errorimage.png)

Depois que a conta for adicionada, você poderá entrar no portal de rede de aplicativos da Microsoft.

Se a página a seguir aparecer depois que você entrar, forneça uma justificativa de negócios para a necessidade de acesso na caixa de texto. Em seguida, selecione **solicitar acesso**.

  ![Caixa de justificativa de negócios no portal do SharePoint](./media/howto-app-gallery-listing/accessrequest.png)

Nossa equipe analisa os detalhes e fornece-lhe o acesso adequadamente. Depois que a solicitação for aprovada, você poderá entrar no portal e enviar a solicitação selecionando o bloco de **solicitação de envio (ISV)** no Home Page.

![Bloco de solicitação de envio (ISV) no home page](./media/howto-app-gallery-listing/homepage.png)

> [!NOTE]
> Se você tiver problemas com o Access, entre em contato com a [equipe de integração de SSO do Azure ad](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-federation-protocol"></a>Implementar o SSO usando o protocolo de Federação

Para listar um aplicativo na galeria de aplicativos do Azure Active Directory, primeiro é necessário implementar um dos seguintes protocolos de federação com suporte pelo Azure Active Directory. Você também precisa concordar com os termos e condições da Galeria de aplicativos do Azure AD. Leia os termos e condições da Galeria de aplicativos do Azure AD neste [site](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/).

- **OpenID Connect**: para integrar seu aplicativo ao Azure ad usando o protocolo Open ID Connect, siga as [instruções dos desenvolvedores](v1-authentication-scenarios.md).

    ![Listando um aplicativo OpenID Connect na Galeria](./media/howto-app-gallery-listing/openid.png)

    * Se você quiser adicionar seu aplicativo para listar na Galeria usando o OpenID Connect, selecione **OpenID connect & OAuth 2,0** , conforme mostrado.
    * Se você tiver problemas com o Access, entre em contato com a [equipe de integração de SSO do Azure ad](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

- **Saml 2,0** ou **WS-alimentado**: se seu aplicativo der suporte a SAML 2,0, você poderá integrá-lo diretamente com um locatário do Azure ad seguindo as [instruções para adicionar um aplicativo personalizado](../active-directory-saas-custom-apps.md).

  ![Listando um aplicativo SAML 2,0 ou WS-enalimentado na Galeria](./media/howto-app-gallery-listing/saml.png)

  * Se você quiser adicionar seu aplicativo para listar na Galeria usando o **saml 2,0** ou o **WS-enalimentado**, selecione **SAML 2.0/WS-alimentado** , conforme mostrado.
  * Se você tiver problemas com o Access, entre em contato com a [equipe de integração de SSO do Azure ad](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="implement-sso-by-using-the-password-sso"></a>Implementar o SSO usando o SSO de senha

Crie um aplicativo da Web que tenha uma página de login HTML para configurar a [conexão única baseada em senha](../manage-apps/what-is-single-sign-on.md). O SSO baseado em senha, também conhecido como armazenamento de senha em cofre, permite que você gerencie o acesso de usuários e senhas para aplicativos Web que não dão suporte a federação de identidades. Também é útil para cenários em que vários usuários precisam compartilhar uma única conta, como as contas de aplicativo de mídia social da sua organização.

![Listando um aplicativo SSO de senha na Galeria](./media/howto-app-gallery-listing/passwordsso.png)

* Se você quiser adicionar seu aplicativo para listar na Galeria usando o SSO de senha, selecione **SSO de senha** , conforme mostrado.
* Se você tiver problemas com o Access, entre em contato com a [equipe de integração de SSO do Azure ad](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="request-for-user-provisioning"></a>Solicitação de provisionamento de usuário

Siga o processo mostrado na imagem a seguir para solicitar o provisionamento de usuário.

   ![Solicitação de provisionamento de usuário](./media/howto-app-gallery-listing/user-provisioning.png)

## <a name="update-or-remove-an-existing-listing"></a>Atualizar ou remover uma listagem existente

Para atualizar ou remover um aplicativo existente na Galeria de aplicativos do Azure AD, primeiro você precisa enviar a solicitação no [portal de rede do aplicativo](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Se você tiver uma conta do Office 365, use-a para entrar nesse portal. Caso contrário, use o conta Microsoft, como o Outlook ou hotmail, para entrar.

- Selecione a opção apropriada, conforme mostrado na imagem a seguir.

    ![Listando um aplicativo SAML na Galeria](./media/howto-app-gallery-listing/updateorremove.png)

    * Para atualizar um aplicativo existente, selecione a opção apropriada de acordo com seu requisito.
    * Para remover um aplicativo existente da Galeria de aplicativos do Azure AD, selecione **remover minha listagem de aplicativos da Galeria**.
    * Se você tiver problemas com o Access, entre em contato com a [equipe de integração de SSO do Azure ad](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

## <a name="list-requests-by-customers"></a>Listar solicitações por clientes

Os clientes podem enviar uma solicitação para listar um aplicativo selecionando **solicitações de aplicativo por clientes** > **Enviar nova solicitação**.

![Mostra o bloco aplicativos solicitados pelo cliente](./media/howto-app-gallery-listing/customer-submit-request.png)

Este é o fluxo de aplicativos solicitados pelo cliente.

![Mostra o fluxo de aplicativos solicitados pelo cliente](./media/howto-app-gallery-listing/customer-request.png)

## <a name="timelines"></a>Linhas do tempo

A linha do tempo para o processo de listagem de um aplicativo SAML 2,0 ou WS-enalimentado na galeria é de 7 a 10 dias úteis.

  ![Linha do tempo para listar um aplicativo SAML na Galeria](./media/howto-app-gallery-listing/timeline.png)

A linha do tempo para o processo de listagem de um aplicativo do OpenID Connect na galeria é de 2 a 5 dias úteis.

  ![Linha do tempo para listar um aplicativo OpenID Connect na Galeria](./media/howto-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Escalonamentos

Para qualquer escalonamento, envie um email para a [equipe de integração de SSO do Azure ad](mailto:SaaSApplicationIntegrations@service.microsoft.com) em SaaSApplicationIntegrations@service.microsoft.com e responderemos assim que possível.
