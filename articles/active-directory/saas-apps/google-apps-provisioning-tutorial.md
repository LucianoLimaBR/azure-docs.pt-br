---
title: 'Tutorial: Configurar o G Suite para provisionamento automático de usuários com o Azure Active Directory | Microsoft Docs'
description: Saiba como provisionar e descontinuar automaticamente as contas de usuário do Azure AD para o G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6dbd50b5-589f-4132-b9eb-a53a318a64e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54b158528a67dfe77f33f41f3bb4b4570eb4c508
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802210"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Tutorial: configurar o G Suite para provisionamento automático de usuários

O objetivo deste tutorial é demonstrar as etapas a serem executadas no G Suite e no Azure Active Directory (Azure AD) para configurar o Azure AD para provisionar e desprovisionar automaticamente usuários e/ou grupos para o G Suite.

> [!NOTE]
> Este tutorial descreve um conector compilado na parte superior do Serviço de Provisionamento de Usuário do Microsoft Azure AD. Para detalhes importantes sobre o que esse serviço faz, como funciona e as perguntas frequentes, consulte [Automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com o Azure Active Directory](../manage-apps/user-provisioning.md).

> [!NOTE]
> O conector do G Suite foi atualizado recentemente em outubro de 2019. As alterações feitas no conector do G Suite incluem:
- Adição de suporte para atributos adicionais de usuário e grupo do G Suite. 
- Atualizado os nomes de atributo de destino do G Suite para corresponder ao que está definido [aqui](/azure/active-directory/manage-apps/customize-application-attributes).
- Mapeamentos de atributo padrão atualizados.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Microsoft Azure AD com o G Suite, você precisará dos seguintes itens:

- Um locatário do Azure AD
- [Um locatário do G Suite](https://gsuite.google.com/pricing.html)
- Uma conta de usuário em um G Suite com permissões de administrador.

## <a name="assign-users-to-g-suite"></a>Atribuir usuários ao G Suite

Azure Active Directory usa um conceito chamado atribuições para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de usuário, somente os usuários e/ou grupos que foram atribuídos a um aplicativo no Azure AD são sincronizados.

Antes de configurar e habilitar o provisionamento automático de usuário, você deve decidir quais usuários e/ou grupos no Azure AD precisam de acesso ao G Suite. Depois de decidir, você pode atribuir esses usuários e/ou grupos ao G Suite seguindo estas instruções:

* [Atribuir um usuário ou um grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-g-suite"></a>Dicas importantes para atribuir usuários ao G Suite

* É recomendável que um único usuário do Azure AD seja atribuído ao G Suite para testar a configuração automática de provisionamento de usuário. Outros usuários e/ou grupos podem ser atribuídos mais tarde.

* Ao atribuir um usuário ao G Suite, você deve selecionar qualquer função específica do aplicativo válida (se disponível) na caixa de diálogo de atribuição. Usuários com a função **Acesso padrão** são excluídos do provisionamento.

## <a name="setup-g-suite-for-provisioning"></a>Configurar o G Suite para provisionamento

Antes de configurar o G Suite para o provisionamento automático de usuário com o Azure AD, você precisará habilitar o provisionamento do SCIM no G Suite.

1. Entre no console de [Administração do G Suite](https://admin.google.com/) com sua conta de administrador e selecione **segurança**. Se você não vê o link, ele pode estar oculto sob o menu **Mais Controles**, na parte inferior da tela.

    ![Selecione segurança.][10]

2. Na página **Segurança**, selecione **Referência de API**.

    ![Selecione Referência de API.][15]

3. Selecione **Habilitar o acesso à API**.

    ![Selecione Referência de API.][16]

   > [!IMPORTANT]
   > Para cada usuário que você pretende provisionar para o G Suite, seu nome de usuário no Azure AD **deve** estar vinculado a um domínio personalizado. Por exemplo, nomes de usuários do tipo bob@contoso.onmicrosoft.com não são aceitos pelo G Suite. Por outro lado, bob@contoso.com é aceito. Você pode alterar o domínio de um usuário existente seguindo as instruções [aqui](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain).

4.  Depois de adicionar e verificar os domínios personalizados desejados com o Azure AD, você deve verificá-los novamente com o G Suite. Para verificar os domínios no G Suite, consulte as seguintes etapas:

    a. No [console de administração do G Suite](https://admin.google.com/), selecione **domínios**.

    ![Selecione Domínios][20]

    b. Selecione **Adicionar um domínio ou um alias de domínio**.

    ![Adicione um novo domínio][21]

    c. Selecione **Adicionar outro domínio** e digite o nome do domínio que você deseja adicionar.

    ![Digite o nome de domínio][22]

    d. Selecione **Continuar e confirmar a propriedade do domínio**. Em seguida, siga as etapas para verificar se você possui o nome de domínio. Para obter instruções abrangentes sobre como verificar seu domínio com o Google, consulte [verificar a propriedade do site](https://support.google.com/webmasters/answer/35179).

    e. Repita as etapas anteriores para todos os domínios adicionais que você pretende adicionar ao G Suite.

5. Em seguida, determine qual conta de administrador você deseja usar para gerenciar o provisionamento de usuário no G Suite. Navegue até **funções de administrador**.

    ![Selecione Google Apps][26]
    
6. Para a **função de administrador** dessa conta, edite os **privilégios** para essa função. Verifique se você habilitou todos os **Privilégios de API de Administrador**, de modo que essa conta possa ser usada para o provisionamento.

    ![Selecione Google Apps][27]

## <a name="add-g-suite-from-the-gallery"></a>Adicionar o G Suite da Galeria

Para configurar o G Suite para o provisionamento automático de usuário com o Azure AD, será necessário adicionar o G Suite da Galeria de aplicativos do Azure AD à sua lista de aplicativos SaaS gerenciados. 

1. No **[portal do Azure](https://portal.azure.com)** , no painel de navegação à esquerda, selecione **Azure Active Directory**.

    ![O botão Azure Active Directory](common/select-azuread.png)

2. Vá para **Aplicativos da empresa**, em seguida, selecione **Todos os aplicativos**.

    ![A folha Aplicativos empresariais](common/enterprise-applications.png)

3. Para adicionar um novo aplicativo, selecione o botão **novo aplicativo** na parte superior do painel.

    ![O botão Novo aplicativo](common/add-new-app.png)

4. Na caixa de pesquisa, insira **g Suite**, selecione **g Suite** no painel de resultados e, em seguida, clique no botão **Adicionar** para adicionar o aplicativo.

    ![G Suite na lista de resultados](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-g-suite"></a>Configurando o provisionamento automático de usuário para o G Suite 

Esta seção orienta você pelas etapas para configurar o serviço de provisionamento do Azure AD para criar, atualizar e desabilitar usuários e/ou grupos no G Suite com base em atribuições de usuário e/ou grupo no Azure AD.

> [!TIP]
> Você também pode optar por habilitar o logon único baseado em SAML para o G Suite, seguindo as instruções fornecidas no [tutorial de logon único do g Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-tutorial). O logon único pode ser configurado independentemente do provisionamento automático de usuário, embora esses dois recursos sejam complementares.

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Para configurar o provisionamento automático de usuário para o G Suite no Azure AD:

1. Entre no [portal do Azure](https://portal.azure.com). Selecione **aplicativos empresariais**e, em seguida, selecione **todos os aplicativos**.

    ![Folha de aplicativos empresariais](common/enterprise-applications.png)

2. Na lista de aplicativos, selecione **G Suite**.

    ![O link do G Suite na lista de Aplicativos](common/all-applications.png)

3. Selecione a guia **Provisionamento**.

    ![Guia provisionamento](common/provisioning.png)

4. Defina o **Modo de Provisionamento** como **Automático**.

    ![Guia provisionamento](common/provisioning-automatic.png)

5. Na seção **Credenciais de Administrador**, selecione **Autorizar**. Isso abre uma caixa de diálogo de autorização do Google em uma nova janela do navegador.

    ![Autorização do G Suite](media/google-apps-provisioning-tutorial/authorize.png)

6. Confirme que você deseja conceder permissões do Azure AD para fazer alterações no seu locatário do G Suite. Selecione **Aceitar**.

    ![Confirme permissões.][28]

7. No Portal do Azure, selecione **Testar Conectividade** para verificar se o Azure AD pode se conectar a seu aplicativo. Se a conexão falhar, verifique se sua conta do G Suite tem permissões de Administrador de Equipe. Em seguida, tente a etapa **Autorizar** novamente.

8. No campo **Notificação por Email**, insira o endereço de email de uma pessoa ou grupo que deverá receber as notificações de erro de provisionamento e selecione a caixa de seleção **Enviar uma notificação por email quando ocorrer uma falha**.

    ![Email de notificação](common/provisioning-notification-email.png)

8. Clique em **Save** (Salvar).

9. Na seção **mapeamentos** , selecione **sincronizar Azure Active Directory usuários para o G Suite**.

    ![Mapeamentos de usuário do G Suite](media/google-apps-provisioning-tutorial/usermappings.png)

10. Examine os atributos de usuário que são sincronizados do Azure AD para o G Suite na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder as contas de usuário no G Suite para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos de usuário do G Suite](media/google-apps-provisioning-tutorial/userattributes.png)

11. Na seção **mapeamentos** , selecione **sincronizar grupos de Azure Active Directory para o G Suite**.

    ![Mapeamentos do Grupo G Suite](media/google-apps-provisioning-tutorial/groupmappings.png)

12. Examine os atributos de grupo que são sincronizados do Azure AD para o G Suite na seção **mapeamento de atributos** . Os atributos selecionados como propriedades **correspondentes** são usados para corresponder os grupos no G Suite para operações de atualização. Selecione o botão **Salvar** para confirmar as alterações.

    ![Atributos do Grupo G Suite](media/google-apps-provisioning-tutorial/groupattributes.png)

13. Para configurar filtros de escopo, consulte as seguintes instruções fornecidas no [tutorial do Filtro de Escopo](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

14. Para habilitar o serviço de provisionamento do Azure AD para o G Suite, altere o **status de provisionamento** para **ativado** na seção **configurações** .

    ![Status de provisionamento alternado em](common/provisioning-toggle-on.png)

15. Defina os usuários e/ou grupos que você deseja provisionar para o G Suite escolhendo os valores desejados no **escopo** na seção **configurações** .

    ![Escopo de provisionamento](common/provisioning-scope.png)

16. Quando estiver pronto para provisionar, clique em **Salvar**.

    ![Salvando configuração de provisionamento](common/provisioning-configuration-save.png)

Essa operação inicia a sincronização inicial de todos os usuários e/ou grupos definidos no **Escopo** na seção **Configurações**. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço de provisionamento do Microsoft Azure Active Directory esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para o relatório de atividade de provisionamento, que descreve todas as ações executadas pelo serviço de provisionamento do Azure AD no G Suite.

Para saber mais sobre como ler os logs de provisionamento do Azure AD, consulte [Relatórios sobre o provisionamento automático de contas de usuário](../manage-apps/check-status-user-account-provisioning.md).

> [!NOTE]
> Outra opção viável para automatizar o provisionamento de usuário para o G Suite é usar a [sincronização de diretório do Google Cloud](https://support.google.com/a/answer/106368?hl=en). Essa opção provisiona suas identidades de Active Directory locais para o G Suite.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerenciamento do provisionamento de conta de usuário para Aplicativos Empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [O que é o acesso a aplicativos e logon único com o Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Próximos passos

* [Saiba como fazer revisão de logs e obter relatórios sobre atividade de provisionamento](../manage-apps/check-status-user-account-provisioning.md)


<!--Image references-->

[10]: ./media/google-apps-provisioning-tutorial/gapps-security.png
[15]: ./media/google-apps-provisioning-tutorial/gapps-api.png
[16]: ./media/google-apps-provisioning-tutorial/gapps-api-enabled.png
[20]: ./media/google-apps-provisioning-tutorial/gapps-domains.png
[21]: ./media/google-apps-provisioning-tutorial/gapps-add-domain.png
[22]: ./media/google-apps-provisioning-tutorial/gapps-add-another.png
[24]: ./media/google-apps-provisioning-tutorial/gapps-provisioning.png
[25]: ./media/google-apps-provisioning-tutorial/gapps-provisioning-auth.png
[26]: ./media/google-apps-provisioning-tutorial/gapps-admin.png
[27]: ./media/google-apps-provisioning-tutorial/gapps-admin-privileges.png
[28]: ./media/google-apps-provisioning-tutorial/gapps-auth.png
