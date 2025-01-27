---
title: Gerenciar autenticação no Azure Mapas | Microsoft Docs
description: Você pode usar o portal do Azure para gerenciar a autenticação no Azure Mapas.
author: walsehgal
ms.author: v-musehg
ms.date: 07/11/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2f4a3d791e6b5d6ff20c09408d1a0bf5995c32fd
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756571"
---
# <a name="manage-authentication-in-azure-maps"></a>Gerenciar autenticação no Azure Mapas

Depois de criar uma conta do Azure Maps, uma ID do cliente e as chaves são criadas para dar suporte a Azure Active Directory (Azure AD) ou autenticação de chave compartilhada.

## <a name="view-authentication-details"></a>Exibir detalhes de autenticação

Você pode exibir os detalhes de autenticação no portal do Azure. Vá para sua conta e selecione **autenticação** no menu **configurações** .

![Detalhes de autenticação](./media/how-to-manage-authentication/how-to-view-auth.png)

 Para saber mais, confira [autenticação com o Azure Maps](https://aka.ms/amauth).


## <a name="set-up-azure-ad-app-registration"></a>Configurar o registro de aplicativo do Azure AD

Depois de criar uma conta do Azure Maps, você precisa estabelecer um vínculo entre o locatário do Azure AD e o recurso do Azure Maps.

1. Vá para a folha do Azure AD e crie um registro de aplicativo. Forneça um nome para o registro. Na caixa **URL de logon** , forneça o home page do aplicativo Web/API (por exemplo, https: \//localhost/). Se você já tiver um aplicativo registrado, vá para a etapa 2.

    ![Registro do aplicativo](./media/how-to-manage-authentication/app-registration.png)

    ![Detalhes de registro do aplicativo](./media/how-to-manage-authentication/app-create.png)

2. Para atribuir permissões de API delegadas para mapas do Azure, vá para o aplicativo em **registros de aplicativo**e, em seguida, selecione **configurações**.  Selecione **as permissões necessárias**e, em seguida, selecione **Adicionar**. Pesquise e selecione **mapas do Azure** em **selecionar uma API**e, em seguida, selecione o botão **selecionar** .

    ![Permissões de API de aplicativo](./media/how-to-manage-authentication/app-permissions.png)

3. Em **selecionar permissões**, selecione **acessar mapas do Azure**e, em seguida, selecione o botão **selecionar** .

    ![Selecionar permissões de API de aplicativo](./media/how-to-manage-authentication/select-app-permissions.png)

4. Conclua a etapa a ou b, dependendo do seu método de autenticação.

    1. Se seu aplicativo usar a autenticação de token de usuário com o SDK da Web do Azure Maps, habilite `oauthEnableImplicitFlow` definindo-o como true na seção manifesto da página de detalhes do registro do aplicativo.
    
       ![Manifesto de aplicativo](./media/how-to-manage-authentication/app-manifest.png)

    2. Se seu aplicativo usar a autenticação de servidor/aplicativo, vá para a folha **chaves** no registro do aplicativo e crie uma senha ou carregue um certificado de chave pública para o registro do aplicativo. Se você criar uma senha, depois de selecionar **salvar**, copie a senha para mais tarde e armazene-a com segurança. Você usará essa senha para adquirir tokens do Azure AD.

       ![Chaves do aplicativo](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>Conceder RBAC ao Azure Mapas

Depois de associar uma conta do Azure Maps ao seu locatário do Azure AD, você pode conceder controle de acesso atribuindo um usuário, grupo ou aplicativo a uma ou mais funções de controle de acesso do Azure Maps.

1. Vá para **controle de acesso (iam)** , selecione **atribuições de função**e, em seguida, selecione **Adicionar atribuição de função**.

    ![Conceder RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Na janela **Adicionar atribuição de função** , em **função**, selecione **leitor de data do Azure Maps (versão prévia)** . Em **Atribuir acesso a**, selecione **Usuário, grupo ou entidade de serviço do Azure AD**. Em **selecionar**, selecione o usuário ou aplicativo. Clique em **Salvar**.

    ![Adicionar atribuição de função](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Exibir funções disponíveis de RBAC do Azure Mapas

Para exibir funções RBAC (controle de acesso baseado em função) que estão disponíveis para mapas do Azure, vá para **controle de acesso (iam)** , selecione **funções**e, em seguida, pesquise funções começando com o **Azure Maps**. Essas são as funções às quais você pode conceder acesso.

![Visualizar funções disponíveis](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Exibir RBAC do Azure Maps

O RBAC fornece controle de acesso granular.

Para exibir usuários e aplicativos que receberam o RBAC para mapas do Azure, vá para **controle de acesso (iam)** , selecione **atribuições de função**e filtre por **mapas do Azure**.

![Exibir usuários e aplicativos com o RBAC concedido](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Solicitar tokens para o Azure Mapas

Depois de registrar seu aplicativo e associo-lo ao Azure Maps, você pode solicitar tokens de acesso.

* Se seu aplicativo usar a autenticação de token de usuário com o SDK da Web do Azure Maps, você precisará configurar sua página HTML com a ID do cliente do Azure Maps e a ID do aplicativo do Azure AD.

* Se seu aplicativo usar a autenticação de servidor/aplicativo, você precisará solicitar um token do ponto de extremidade de logon do Azure AD `https://login.microsoftonline.com` com a ID de recurso do Azure AD `https://atlas.microsoft.com/`, a ID do cliente do Azure Maps, a ID do aplicativo do Azure AD e a senha de registro do aplicativo do Azure AD ou Certificate.

Para obter mais informações sobre como solicitar tokens de acesso do Azure AD para usuários e entidades de serviço, consulte [cenários de autenticação do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Próximos passos

Para saber mais sobre a autenticação do Azure AD e o SDK da Web do Azure Mapas, consulte [Azure AD e o SDK da Web do Azure Mapas](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Saiba como ver as métricas de uso da API para sua conta do Azure Mapas:
> [!div class="nextstepaction"] 
> [ Exibir métricas de uso](how-to-view-api-usage.md)

Para obter uma lista de exemplos que mostram como integrar o Azure Active Directory (AAD) com o Azure Maps, consulte:

> [!div class="nextstepaction"]
> [Exemplos de autenticação do AAD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)