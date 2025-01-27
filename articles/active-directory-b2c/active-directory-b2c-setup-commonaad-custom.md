---
title: Configurar a entrada para um provedor de identidade multilocatário do Azure AD usando políticas personalizadas no Azure Active Directory B2C
description: Adicionar um provedor de identidade multilocatário do Azure AD usando políticas personalizadas – Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: f254ebe599e64f4c48a839d9defd57e0899138a5
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755774"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar a entrada para o Azure Active Directory multilocatário usando políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar o logon de usuários usando o ponto de extremidade de multi locação do Microsoft Azure Active Directory (Azure AD) usando [políticas personalizadas](active-directory-b2c-overview-custom.md) no Azure AD B2C. Isso permite que os usuários de vários locatários do Azure AD entrem usando Azure AD B2C, sem a necessidade de configurar um provedor de identidade para cada locatário. No entanto, os membros em qualquer desses locatários **não** será capaz de entrar. Para fazer isso, você terá que [configurar individualmente cada locatário](active-directory-b2c-setup-aad-custom.md).

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em [Introdução a políticas personalizadas no Active Directory B2C do Azure](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Registrar um aplicativo

Para habilitar a entrada para usuários de uma organização específica do Azure AD, você precisa registrar um aplicativo no locatário organizacional do Azure AD.

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém seu locatário organizacional do Azure AD (por exemplo, contoso.com). Selecione o **diretório + filtro de assinatura** no menu superior e escolha o diretório que contém seu locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
1. Selecione **Novo registro**.
1. Insira um **Nome** para seu aplicativo. Por exemplo, `Azure AD B2C App`.
1. Selecione **contas em qualquer diretório organizacional** para este aplicativo.
1. Para o **URI de redirecionamento**, aceite o valor de **Web**e insira a URL a seguir em todas as letras minúsculas, em que `your-B2C-tenant-name` é substituído pelo nome do seu locatário Azure ad B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Por exemplo, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

1. Selecione **Registrar**. Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.
1. Selecione **certificados & segredos**e, em seguida, selecione **novo segredo do cliente**.
1. Insira uma **Descrição** para o segredo, selecione uma expiração e, em seguida, selecione **Adicionar**. Registre o **valor** do segredo para uso em uma etapa posterior.

## <a name="create-a-policy-key"></a>Crie uma chave de política

Você precisa armazenar a chave do aplicativo que criou em seu locatário do Azure AD B2C.

1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C. Selecione o **diretório + filtro de assinatura** no menu superior e escolha o diretório que contém seu locatário de Azure ad B2C.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Em **políticas**, selecione **estrutura de experiência de identidade**.
1. Selecione **chaves de política** e, em seguida, selecione **Adicionar**.
1. Para **Opções**, escolha `Manual`.
1. Digite um **Nome** para a chave de política. Por exemplo, `AADAppSecret`.  O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave quando ele é criado, portanto, sua referência no XML na seção a seguir é *B2C_1A_AADAppSecret*.
1. Em **segredo**, insira o segredo do cliente que você registrou anteriormente.
1. Para **Uso de chave**, selecione `Signature`.
1. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um provedor de declarações

Se você quiser que os usuários entrem usando o Azure AD, precisará definir o Azure AD como um provedor de declarações com o qual o Azure AD B2C pode se comunicar por meio de um endpoint. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Você pode definir o Azure AD como um provedor de declarações adicionando o Azure AD ao elemento **ClaimsProvider** no arquivo de extensão de sua política.

1. Abra o arquivo *TrustFrameworkExtensions.xml*.
1. Localize o elemento **ClaimsProviders**. Se ele não existir, adicione-o sob o elemento raiz.
1. Adicione um novo **ClaimsProvider** da seguinte forma:

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000,https://login.microsoftonline.com/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Sob o elemento **ClaimsProvider**, atualize o valor de **Domain** para um valor exclusivo que pode ser usado para distingui-lo de outros provedores de identidade.
1. No elemento **TechnicalProfile** , atualize o valor de **DisplayName**, por exemplo, `Contoso Employee`. Esse valor é exibido no botão de entrada em sua página de entrada.
1. Defina **client_id** como a ID do aplicativo do aplicativo multilocatário do Azure AD que você registrou anteriormente.
1. Em **CryptographicKeys**, atualize o valor de **StorageReferenceId** para o nome da chave de política criada anteriormente. Por exemplo, `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Restringir acesso

> [!NOTE]
> Usar `https://login.microsoftonline.com/` como o valor para **ValidTokenIssuerPrefixes** permite que todos os usuários do Azure AD entrem no seu aplicativo.

Você precisa atualizar a lista de emissores de token válidos e restringir o acesso a uma lista específica de locatários do Azure AD que podem entrar.

Para obter os valores, examine os metadados de descoberta do OpenID Connect para cada um dos locatários do Azure AD dos quais você deseja que os usuários se conectem. O formato da URL de metadados é semelhante a `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration`, em que `your-tenant` é o nome do locatário do Azure AD. Por exemplo:

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

Execute estas etapas para cada locatário do Azure AD que deve ser usado para entrar:

1. Abra o navegador e vá para a URL de metadados do OpenID Connect para o locatário. Localize o objeto **emissor** e Registre seu valor. Ele deve ser semelhante a `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/`.
1. Copie e cole o valor na chave **ValidTokenIssuerPrefixes** . Separe vários emissores com uma vírgula. Um exemplo com dois emissores aparece no exemplo de XML de `ClaimsProvider` anterior.

### <a name="upload-the-extension-file-for-verification"></a>Carregar o arquivo de extensão para verificação

Agora, você configurou sua política para que Azure AD B2C saiba como se comunicar com seus diretórios do Azure AD. Tente carregar o arquivo de extensão da política apenas para confirmar se ele não apresenta problemas até o momento.

1. Na página **Políticas Personalizadas** em seu locatário do Azure AD B2C, selecione **Fazer Upload de Política**.
2. Habilite **Substitua a política se ela existir** e, em seguida, navegue até o arquivo *TrustFrameworkExtensions.xml* e selecione-o.
3. Escolha **Carregar**.

## <a name="register-the-claims-provider"></a>Registrar o provedor de declarações

Neste ponto, o provedor de identidade foi definido, mas não está disponível em nenhuma das telas de inscrição/entrada. Para disponibilizá-lo, você criará uma duplicata de um modelo de percurso do usuário existente e, depois, o modificará para que ele também tenha o provedor de identidade do Azure AD.

1. Abra o arquivo *TrustFrameworkBase.xml* do pacote inicial.
2. Localize e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"`.
3. Abra o *TrustFrameworkExtensions.xml* e localize o elemento **UserJourneys**. Se o elemento não existir, adicione um.
4. Cole todo o conteúdo do elemento **UserJourney** que você copiou como filho do elemento **UserJourneys**.
5. Renomeie a ID do percurso do usuário. Por exemplo, `SignUpSignInContoso`.

### <a name="display-the-button"></a>Exibir o botão

O elemento **ClaimsProviderSelection** é análogo a um botão do provedor de identidade em uma tela de inscrição/entrada. Se você adicionar um elemento **ClaimsProviderSelection** para o Azure AD, um novo botão será exibido quando um usuário chegar na página.

1. Localize o elemento **OrchestrationStep** que inclui `Order="1"` no percurso do usuário que você criou em *TrustFrameworkExtensions. xml*.
1. Em **ClaimsProviderSelects**, adicione o elemento a seguir. Defina o valor de **TargetClaimsExchangeId** para um valor apropriado, por exemplo `AzureADExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação

Agora que implementou um botão, você precisará vinculá-lo a uma ação. Nesse caso, a ação destina-se a que o Azure AD B2C se comunique com o Azure AD para receber um token. Vincule o botão a uma ação, vinculando o perfil técnico ao provedor de declarações do Azure AD.

1. Encontre o **OrchestrationStep** que inclui `Order="2"` na jornada do usuário.
2. Adicione o seguinte elemento **ClaimsExchange** usando o mesmo valor de **ID** usado para **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    Atualize o valor de **TechnicalProfileReferenceId** para a **ID** do perfil técnico você já criou. Por exemplo, `Common-AAD`.

3. Salve o arquivo *TrustFrameworkExtensions.xml* e faça upload dele novamente para verificação.

## <a name="create-an-azure-ad-b2c-application"></a>Criar um aplicativo Azure AD B2C

A comunicação com Azure AD B2C ocorre por meio de um aplicativo que você registra em seu locatário B2C. Esta seção lista etapas opcionais que você pode concluir para criar um aplicativo de teste, caso ainda não tenha feito isso.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o arquivo de terceira parte confiável

Atualize o arquivo RP (terceira parte confiável) que inicia o percurso do usuário que você criou:

1. Faça uma cópia de *SignUpOrSignIn.xml* no seu diretório de trabalho e renomeie-o. Por exemplo, renomeie-o para *SignUpSignContoso.xml*.
1. Abra o novo arquivo e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo, `SignUpSignInContoso`.
1. Atualize o valor de **PublicPolicyUri** com o URI da política. Por exemplo, `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Atualize o valor do atributo **referenceid** em **DefaultUserJourney** para corresponder à ID da jornada do usuário que você criou anteriormente. Por exemplo, *SignUpSignInContoso*.
1. Salve as alterações e carregue o arquivo.
1. Em **políticas personalizadas**, selecione a nova política na lista.
1. Na lista suspensa **Selecionar aplicativo** , selecione o aplicativo Azure ad B2C que você criou anteriormente. Por exemplo, *testapp1*.
1. Copie o **ponto de extremidade executar agora** e abra-o em uma janela de navegador privada, por exemplo, o modo Incognito no Google Chrome ou uma janela InPrivate no Microsoft Edge. Abrir em uma janela privada do navegador permite que você teste o percurso completo do usuário não usando nenhuma credencial do Azure AD atualmente armazenada em cache.
1. Selecione o botão entrar do Azure AD, por exemplo, *funcionário contoso*e, em seguida, insira as credenciais para um usuário em um dos seus locatários organizacionais do Azure AD. Você será solicitado a autorizar o aplicativo e, em seguida, a inserir informações para seu perfil.

Se o processo de entrada for bem-sucedido, seu navegador será redirecionado para `https://jwt.ms`, que exibe o conteúdo do token retornado por Azure AD B2C.

Para testar a funcionalidade de entrada multilocatário, execute as duas últimas etapas usando as credenciais para um usuário que existe outro locatário do Azure AD.

## <a name="next-steps"></a>Próximos passos

Ao trabalhar com políticas personalizadas, às vezes você pode precisar de informações adicionais ao solucionar problemas de uma política durante seu desenvolvimento.

Para ajudar a diagnosticar problemas, você pode colocar temporariamente a política em "modo de desenvolvedor" e coletar logs com o Aplicativo Azure insights. Descubra como [Azure Active Directory B2C: coletando logs](active-directory-b2c-troubleshoot-custom.md).
