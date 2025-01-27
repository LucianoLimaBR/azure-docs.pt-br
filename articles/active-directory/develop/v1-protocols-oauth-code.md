---
title: Entender o fluxo do código de autorização do OAuth 2,0 no Azure AD
description: Este artigo descreve como usar mensagens HTTP para autorizar o acesso a aplicativos Web e APIs Web em seu locatário usando Azure Active Directory e OAuth 2,0.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0184aa7bff4203f50d834f603bed5fd2af52e4c
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514431"
---
# <a name="authorize-access-to-azure-active-directory-web-applications-using-the-oauth-20-code-grant-flow"></a>Autorizar o acesso a aplicativos Web Azure Active Directory usando o fluxo de concessão de código do OAuth 2,0

> [!NOTE]
>  Se você não informar ao servidor qual recurso você planeja chamar, o servidor não irá disparar as políticas de acesso condicional para esse recurso. Portanto, para ter um gatilho MFA, você precisará incluir um recurso em sua URL. 
>

O Azure Active Directory (AD do Azure) usa o OAuth 2,0 para permitir que você autorize o acesso a aplicativos Web e APIs Web em seu locatário do Azure AD. Este guia é independente de linguagem e descreve como enviar e receber mensagens HTTP sem usar qualquer uma das nossas [bibliotecas](active-directory-authentication-libraries.md)de software livre.

O fluxo do código de autorização do OAuth 2.0 é descrito na [seção 4.1 da especificação do OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-4.1). Ele é usado para executar autenticação e autorização na maioria dos tipos de aplicativos, incluindo aplicativos Web e aplicativos instalados nativamente.

[!INCLUDE [active-directory-protocols-getting-started](../../../includes/active-directory-protocols-getting-started.md)]

## <a name="oauth-20-authorization-flow"></a>Fluxo de autorização do OAuth 2.0

Em um alto nível, todo o fluxo de autorização para um aplicativo é semelhante a este:

![Fluxo de código de autenticação OAuth](./media/v1-protocols-oauth-code/active-directory-oauth-code-flow-native-app.png)

## <a name="request-an-authorization-code"></a>Solicitar um código de autorização

O fluxo do código de autorização começa com o cliente direcionando o usuário para o ponto de extremidade `/authorize`. Nessa solicitação, o cliente indica as permissões que precisa adquirir do usuário. Você pode obter o ponto de extremidade de autorização do OAuth 2,0 para seu locatário, selecionando **registros de aplicativo pontos de extremidades de >** no portal do Azure.

```
// Line breaks for legibility only

https://login.microsoftonline.com/{tenant}/oauth2/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%3A12345
&response_mode=query
&resource=https%3A%2F%2Fservice.contoso.com%2F
&state=12345
```

| . |  | Descrição |
| --- | --- | --- |
| locatário |obrigatório |O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são identificadores de locatário, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` para tokens independentes de locatário |
| client_id |obrigatório |A ID do aplicativo atribuída ao seu aplicativo quando você o registrou com o Azure AD. Você pode encontrá-lo no portal do Azure. Clique em **Azure Active Directory** na barra lateral serviços, clique em **registros de aplicativo**e escolha o aplicativo. |
| response_type |obrigatório |Deve incluir `code` para o fluxo do código de autorização. |
| redirect_uri |recomendável |O redirect_uri do seu aplicativo, onde as respostas de autenticação podem ser enviadas e recebidas pelo aplicativo. Ele deve corresponder exatamente a um dos redirect_uris que você registrou no portal, exceto que ele deve ser codificado por URL. Para aplicativos nativos e móveis, você deve usar o valor padrão de `urn:ietf:wg:oauth:2.0:oob`. |
| response_mode |Adicional |Especifica o método que deve ser usado para enviar o token resultante de volta ao aplicativo. Pode ser `query`, `fragment` ou `form_post`. `query` fornece o código como um parâmetro de cadeia de caracteres de consulta em seu URI de redirecionamento. Se você estiver solicitando um token de ID usando o fluxo implícito, não será possível usar `query` conforme especificado na [especificação OpenID](https://openid.net/specs/oauth-v2-multiple-response-types-1_0.html#Combinations). Se você estiver solicitando apenas o código, poderá usar `query`, `fragment` ou `form_post`. `form_post` executa uma POSTAgem que contém o código para o URI de redirecionamento. O padrão é `query` para um fluxo de código.  |
| Status |recomendável |Um valor incluído na solicitação que também retorna na resposta do token. Um valor exclusivo gerado aleatoriamente que normalmente é usado para [impedir ataques de solicitação intersite forjada](https://tools.ietf.org/html/rfc6749#section-10.12). O estado também é usado para codificar informações sobre o estado do usuário no aplicativo antes que a solicitação de autenticação ocorra, como a página ou a exibição em que eles estavam. |
| Kit | recomendável |O URI da ID do aplicativo da API Web de destino (recurso protegido). Para localizar o URI da ID do aplicativo, no portal do Azure, clique em **Azure Active Directory**, em **registros do aplicativo**, abra a página **configurações** do aplicativo e clique em **Propriedades**. Ele também pode ser um recurso externo como `https://graph.microsoft.com`. Isso é necessário em uma das solicitações de autorização ou token. Para garantir que menos prompts de autenticação o coloquem na solicitação de autorização para garantir que o consentimento seja recebido do usuário. |
| scope | **aceita** | Para aplicativos v1 do Azure AD, os escopos devem ser configurados estaticamente no portal do Azure em **configurações**de aplicativos, **permissões necessárias**. |
| prompt |Adicional |Indique o tipo de interação do usuário que é necessário.<p> Os valores válidos são: <p> *logon*: o usuário deve ser solicitado a autenticar novamente. <p> *select_account*: o usuário é solicitado a selecionar uma conta, interrompendo o logon único. O usuário pode selecionar uma conta conectada existente, inserir suas credenciais para uma conta memorizada ou optar por usar uma conta diferente completamente. <p> *consentimento*: consentimento do usuário foi concedido, mas precisa ser atualizado. O usuário deve ser solicitado a consentir. <p> *admin_consent*: o administrador deve ser solicitado a consentir em nome de todos os usuários em sua organização |
| login_hint |Adicional |Pode ser usado para preencher previamente o campo nome de usuário/endereço de email da página de entrada do usuário, se você souber seu nome de usuário antes do tempo. Geralmente, os aplicativos usam esse parâmetro durante a reautenticação, já ter extraído o nome de usuário de uma entrada anterior usando a declaração de `preferred_username`. |
| domain_hint |Adicional |Fornece uma dica sobre o locatário ou domínio que o usuário deve usar para entrar. O valor de domain_hint é um domínio registrado para o locatário. Se o locatário for federado para um diretório local, o AAD redirecionará para o servidor de federação do locatário especificado. |
| code_challenge_method | recomendável    | O método usado para codificar o `code_verifier` para o parâmetro `code_challenge`. Pode ser uma das `plain` ou `S256`. Se for excluído, `code_challenge` será considerado texto sem formatação se `code_challenge` estiver incluído. O Azure AAD v 1.0 dá suporte a `plain` e a `S256`. Para obter mais informações, consulte a [RFC PKCE](https://tools.ietf.org/html/rfc7636). |
| code_challenge        | recomendável    | Usado para proteger as concessões de código de autorização por meio da chave de prova de troca de código (PKCE) de um cliente nativo ou público. Necessário se `code_challenge_method` estiver incluído. Para obter mais informações, consulte a [RFC PKCE](https://tools.ietf.org/html/rfc7636). |

> [!NOTE]
> Se o usuário fizer parte de uma organização, um administrador da organização poderá consentir ou recusar o nome do usuário ou permitir o consentimento do usuário. O usuário terá a opção de consentir apenas quando o administrador permitir.
>
>

Neste ponto, o usuário será solicitado a inserir suas credenciais e consentir as permissões solicitadas pelo aplicativo no portal do Azure. Depois que o usuário autenticar e conceder consentimento, o Azure AD enviará uma resposta ao seu aplicativo no endereço `redirect_uri` em sua solicitação com o código.

### <a name="successful-response"></a>Resposta bem-sucedida
Uma resposta bem-sucedida poderia ser assim:

```
GET  HTTP/1.1 302 Found
Location: http://localhost:12345/?code= AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA&session_state=7B29111D-C220-4263-99AB-6F6E135D75EF&state=D79E5777-702E-4260-9A62-37F75FF22CCE
```

| . | Descrição |
| --- | --- |
| admin_consent |O valor será True se um administrador tiver consentido um prompt de solicitação de consentimento. |
| Auto-completar |O código de autorização solicitado pelo aplicativo. O aplicativo pode usar o código de autorização para solicitar um token de acesso para o recurso de destino. |
| session_state |Um valor exclusivo que identifica a sessão do usuário atual. Esse valor é um GUID, mas deve ser tratado como um valor opaco que é passado sem exame. |
| Status |Se um parâmetro de estado for incluído na solicitação, o mesmo valor deverá aparecer na resposta. É uma boa prática para o aplicativo verificar se os valores de estado na solicitação e na resposta são idênticos antes de usar a resposta. Isso ajuda a detectar [ataques de CSRF (Solicitação Intersite Forjada)](https://tools.ietf.org/html/rfc6749#section-10.12) contra o cliente. |

### <a name="error-response"></a>Resposta de erro
As respostas de erro também podem ser enviadas ao `redirect_uri` para que o aplicativo possa tratá-las adequadamente.

```
GET http://localhost:12345/?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| . | Descrição |
| --- | --- |
| error |Um valor de código de erro definido na Seção 5.2 da [Estrutura de Autorização OAuth 2.0](https://tools.ietf.org/html/rfc6749). A tabela a seguir descreve os códigos de erro retornados pelo Azure AD. |
| error_description |Uma descrição mais detalhada do erro. Esta mensagem não deve ser amigável para o usuário final. |
| Status |O valor de estado é um valor não reutilizado gerado aleatoriamente que é enviado na solicitação e retornado na resposta para evitar ataques CSRF (solicitação entre sites forjada). |

#### <a name="error-codes-for-authorization-endpoint-errors"></a>Códigos de erro para erros de ponto de extremidade de autorização
A tabela a seguir descreve os vários códigos de erro que podem ser retornados no parâmetro `error` da resposta de erro.

| Código de erro | Descrição | Ação do cliente |
| --- | --- | --- |
| invalid_request |Erro de protocolo, como um parâmetro necessário ausente. |Corrija e reenvie a solicitação. Esse é um erro de desenvolvimento e normalmente é capturado durante o teste inicial. |
| unauthorized_client |O aplicativo cliente não tem permissão para solicitar um código de autorização. |Isso geralmente ocorre quando o aplicativo cliente não está registrado no Azure AD ou não é adicionado ao locatário do Azure AD do usuário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |
| access_denied |Consentimento negado pelo proprietário do recurso |O aplicativo cliente pode notificar o usuário de que não pode continuar, a menos que o usuário consinta. |
| unsupported_response_type |O servidor de autorização não dá suporta ao tipo de resposta na solicitação. |Corrija e reenvie a solicitação. Esse é um erro de desenvolvimento e normalmente é capturado durante o teste inicial. |
| server_error |O servidor encontrou um erro inesperado. |Repita a solicitação. Esses erros podem resultar de condições temporárias. O aplicativo cliente pode explicar ao usuário que sua resposta está atrasada devido a um erro temporário. |
| temporarily_unavailable |O servidor está temporariamente muito ocupado para tratar da solicitação. |Repita a solicitação. O aplicativo cliente pode explicar ao usuário que sua resposta está atrasada devido a uma condição temporária. |
| invalid_resource |O recurso de destino é inválido porque não existe, o Azure AD não consegue encontrá-lo ou não está configurado corretamente. |Isso indica que o recurso, se ele existe, não foi configurado no locatário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |

## <a name="use-the-authorization-code-to-request-an-access-token"></a>Usar o código de autorização para solicitar um token de acesso
Agora que você já adquiriu um código de autorização e recebeu permissão do usuário, poderá resgatar o código de um token de acesso para o recurso desejado ao enviar uma solicitação POST para o ponto de extremidade `/token` :

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded
grant_type=authorization_code
&client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrqqf_ZT_p5uEAEJJ_nZ3UmphWygRNy2C3jJ239gV_DBnZ2syeg95Ki-374WHUP-i3yIhv5i-7KU2CEoPXwURQp6IVYMw-DjAOzn7C3JCu5wpngXmbZKtJdWmiBzHpcO2aICJPu1KvJrDLDP20chJBXzVYJtkfjviLNNW7l7Y3ydcHDsBRKZc3GuMQanmcghXPyoDg41g8XbwPudVh7uCmUponBQpIhbuffFP_tbV8SNzsPoFz9CLpBCZagJVXeqWoYMPe2dSsPiLO9Alf_YIe5zpi-zY4C3aLw5g9at35eZTfNd0gBRpR5ojkMIcZZ6IgAA
&redirect_uri=https%3A%2F%2Flocalhost%3A12345
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=p@ssw0rd

//NOTE: client_secret only required for web apps
```

| . |  | Descrição |
| --- | --- | --- |
| locatário |obrigatório |O valor `{tenant}` no caminho da solicitação pode ser usado para controlar quem pode entrar no aplicativo. Os valores permitidos são identificadores de locatário, por exemplo, `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` ou `contoso.onmicrosoft.com` ou `common` para tokens independentes de locatário |
| client_id |obrigatório |A ID do aplicativo atribuída ao seu aplicativo quando você o registrou com o Azure AD. Você pode encontrá-lo na portal do Azure. A ID do aplicativo é exibida nas configurações do registro do aplicativo. |
| grant_type |obrigatório |Deve ser `authorization_code` para o fluxo do código de autorização. |
| Auto-completar |obrigatório |O `authorization_code` que você adquiriu na seção anterior |
| redirect_uri |obrigatório | Um `redirect_uri`registered no aplicativo cliente. |
| client_secret |necessário para aplicativos Web, não permitido para clientes públicos |O segredo do aplicativo que você criou no portal do Azure para seu aplicativo em **chaves**. Ele não pode ser usado em um aplicativo nativo (cliente público), porque client_secrets não pode ser armazenado de forma confiável em dispositivos. Ele é necessário para aplicativos Web e APIs Web (todos os clientes confidenciais), que têm a capacidade de armazenar o `client_secret` com segurança no lado do servidor. O client_secret deve ser codificado por URL antes de ser enviado. |
| Kit | recomendável |O URI da ID do aplicativo da API Web de destino (recurso protegido). Para localizar o URI da ID do aplicativo, no portal do Azure, clique em **Azure Active Directory**, em **registros do aplicativo**, abra a página **configurações** do aplicativo e clique em **Propriedades**. Ele também pode ser um recurso externo como `https://graph.microsoft.com`. Isso é necessário em uma das solicitações de autorização ou token. Para garantir que menos prompts de autenticação o coloquem na solicitação de autorização para garantir que o consentimento seja recebido do usuário. Se estiver na solicitação de autorização e na solicitação de token, os parâmetros do recurso deverão corresponder. | 
| code_verifier | Adicional | O mesmo code_verifier que foi usado para obter o authorization_code. Obrigatório se PKCE foi usado na solicitação de concessão de código de autorização. Para obter mais informações, consulte a [RFC do PKCE](https://tools.ietf.org/html/rfc7636)   |

Para localizar o URI da ID do aplicativo, no portal do Azure, clique em **Azure Active Directory**, em **registros do aplicativo**, abra a página **configurações** do aplicativo e clique em **Propriedades**.

### <a name="successful-response"></a>Resposta bem-sucedida
O Azure AD retorna um [token de acesso](access-tokens.md) após uma resposta bem-sucedida. Para minimizar as chamadas de rede do aplicativo cliente e sua latência associada, o aplicativo cliente deve armazenar em cache os tokens de acesso durante a vida útil do token especificada na resposta do OAuth 2.0. Para determinar o tempo de vida do token, use os valores de parâmetro `expires_in` ou `expires_on`.

Se um recurso da API Web retornar um código de erro `invalid_token` , isso poderá indicar que o recurso determinou que o token expirou. Se os tempos de relógio do cliente e do recurso forem diferentes (conhecido como "distorção de tempo"), o recurso poderá considerar o token expirado antes de o token ser limpo do cache do cliente. Se isso ocorrer, remova o token do cache, mesmo se ele ainda estiver dentro de seu tempo de vida calculado.

Uma resposta bem-sucedida poderia ser assim:

```
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1388444763",
  "resource": "https://service.contoso.com/",
  "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA",
  "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
  "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ."
}

```

| . | Descrição |
| --- | --- |
| access_token |O token de acesso solicitado.  Essa é uma cadeia de caracteres opaca, depende do que o recurso espera receber e não se destina ao cliente examinar. O aplicativo pode usar esse token para se autenticar no recurso protegido, como uma API Web. |
| token_type |Indica o valor do tipo de token. O único tipo com suporte do Azure AD é Portador Para saber mais sobre os tokens de portador, confira [Estrutura de autorização do OAuth 2.0: uso do token de portador (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt) |
| expires_in |Por quanto tempo o token de acesso é válido (em segundos). |
| expires_on |A hora em que o token de acesso expira. A data é representada como o número de segundos de 1970-01-01T0:0: 0Z UTC até a hora de expiração. Esse valor é usado para determinar o tempo de vida de tokens em cache. |
| Kit |O URI da ID do aplicativo da API Web (recurso protegido). |
| scope |Permissões de representação concedidas ao aplicativo cliente. A permissão padrão é `user_impersonation`. O proprietário do recurso protegido pode registrar valores adicionais no Azure AD. |
| refresh_token |Um token de atualização do OAuth 2.0. O aplicativo pode usar esse token para adquirir tokens de acesso adicionais depois que o token de acesso atual expirar. Os tokens de atualização têm longa duração e podem ser usados para reter acesso a recursos por períodos estendidos. |
| id_token |Um JWT (token Web JSON) não assinado que representa um [token de ID](id-tokens.md). O aplicativo pode decodificar com base64Url os segmentos desse token para solicitar informações sobre o usuário que se conectou. O aplicativo pode armazenar em cache os valores e exibi-los, mas não deve depender deles para qualquer autorização ou limites de segurança. |

Para obter mais informações sobre tokens Web JSON, consulte a [especificação de rascunho IETF de JWT](https://go.microsoft.com/fwlink/?LinkId=392344).   Para saber mais sobre `id_tokens`, confira o [fluxo v 1.0 OpenID Connect](v1-protocols-openid-connect-code.md).

### <a name="error-response"></a>Resposta de erro
Os erros de ponto de extremidade de emissão de token são códigos de erro HTTP, pois o cliente chama o ponto de extremidade de emissão de token diretamente. Além do código de status HTTP, o ponto de extremidade de emissão de tokens do Azure AD também retorna um documento JSON com objetos que descrevem o erro.

Uma resposta de erro de exemplo poderia ser assim:

```
{
  "error": "invalid_grant",
  "error_description": "AADSTS70002: Error validating credentials. AADSTS70008: The provided authorization code or refresh token is expired. Send a new interactive authorization request for this user and resource.\r\nTrace ID: 3939d04c-d7ba-42bf-9cb7-1e5854cdce9e\r\nCorrelation ID: a8125194-2dc8-4078-90ba-7b6592a7f231\r\nTimestamp: 2016-04-11 18:00:12Z",
  "error_codes": [
    70002,
    70008
  ],
  "timestamp": "2016-04-11 18:00:12Z",
  "trace_id": "3939d04c-d7ba-42bf-9cb7-1e5854cdce9e",
  "correlation_id": "a8125194-2dc8-4078-90ba-7b6592a7f231"
}
```
| . | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |
| error_codes |Uma lista de códigos de erro específicos do STS que podem ajudar no diagnóstico. |
| estampa |A hora em que o erro ocorreu. |
| trace_id |Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico. |
| correlation_id |Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico entre os componentes. |

#### <a name="http-status-codes"></a>Códigos de status HTTP
A tabela a seguir lista os códigos de status HTTP que o ponto de extremidade de emissão de token retorna. Em alguns casos, o código de erro é suficiente para descrever a resposta, mas se houver erros, você precisará analisar o documento JSON que o acompanha e examinar seu código de erro.

| Código HTTP | Descrição |
| --- | --- |
| 400 |Código HTTP padrão. Usado na maioria dos casos e normalmente é devido a uma solicitação malformada. Corrija e reenvie a solicitação. |
| 401 |Falha na autenticação. Por exemplo, a solicitação não tem o parâmetro client_secret. |
| 403 |Falha na autorização. Por exemplo, o usuário não tem permissão para acessar o recurso. |
| 500 |Ocorreu um erro interno no serviço. Repita a solicitação. |

#### <a name="error-codes-for-token-endpoint-errors"></a>Códigos de erro para erros de ponto de extremidade de token
| Código de erro | Descrição | Ação do cliente |
| --- | --- | --- |
| invalid_request |Erro de protocolo, como um parâmetro necessário ausente. |Corrija e reenvie a solicitação |
| invalid_grant |O código de autorização é inválido ou expirou. |Tente uma nova solicitação para o `/authorize` ponto de extremidade |
| unauthorized_client |O cliente autenticado não está autorizado a usar esse tipo de concessão de autorização. |Isso geralmente ocorre quando o aplicativo cliente não está registrado no Azure AD ou não é adicionado ao locatário do Azure AD do usuário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |
| invalid_client |Falha na autenticação de cliente. |As credenciais do cliente não são válidas. Para corrigi-las, o administrador do aplicativo atualiza as credenciais. |
| unsupported_grant_type |O servidor de autorização não dá suporte ao tipo de concessão de autorização. |Altere o tipo de concessão na solicitação. Esse tipo de erro deve ocorrer somente durante o desenvolvimento e ser detectado durante os testes iniciais. |
| invalid_resource |O recurso de destino é inválido porque não existe, o Azure AD não consegue encontrá-lo ou não está configurado corretamente. |Isso indica que o recurso, se ele existe, não foi configurado no locatário. O aplicativo pode solicitar que o usuário instale o aplicativo e o adicione ao Azure AD. |
| interaction_required |A solicitação requer interação do usuário. Por exemplo, uma etapa de autenticação adicional é necessária. | Em vez de uma solicitação não interativa, tente novamente com uma solicitação de autorização interativa para o mesmo recurso. |
| temporarily_unavailable |O servidor está temporariamente muito ocupado para tratar da solicitação. |Repita a solicitação. O aplicativo cliente pode explicar ao usuário que sua resposta está atrasada devido a uma condição temporária. |

## <a name="use-the-access-token-to-access-the-resource"></a>Usar o token de acesso para acessar o recurso
Agora que você já adquiriu com êxito um `access_token`, você pode usar o token em solicitações para APIs Web incluindo-o no cabeçalho `Authorization`. A especificação [RFC 6750](https://www.rfc-editor.org/rfc/rfc6750.txt) explica como usar os tokens de portador em solicitações HTTP para acessar recursos protegidos.

### <a name="sample-request"></a>Exemplo de solicitação
```
GET /data HTTP/1.1
Host: service.contoso.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ
```

### <a name="error-response"></a>Resposta de erro
Recursos protegidos que implementam códigos de status HTTP de emissão RFC 6750. Se a solicitação não tem credenciais de autenticação ou o token está ausente, a resposta inclui um cabeçalho `WWW-Authenticate` . Quando uma solicitação falha, o servidor de recursos responde com o código de status HTTP e um código de erro.

Este é um exemplo de uma resposta malsucedida quando a solicitação do cliente não inclui o token de portador:

```
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer authorization_uri="https://login.microsoftonline.com/contoso.com/oauth2/authorize",  error="invalid_token",  error_description="The access token is missing.",
```

#### <a name="error-parameters"></a>Parâmetros de erro
| . | Descrição |
| --- | --- |
| authorization_uri |O URI (ponto de extremidade físico) do servidor de autorização. Esse valor também é usado como uma chave de pesquisa para obter mais informações sobre o servidor de um ponto de extremidade de descoberta. <p><p> O cliente deve validar que o servidor de autorização é confiável. Quando o recurso é protegido pelo AD do Azure, é suficiente verificar se a URL começa com https://login.microsoftonline.com ou outro nome de host ao qual o Azure AD dá suporte. Um recurso específico de locatário sempre deve retornar um URI de autorização específico de locatário. |
| error |Um valor de código de erro definido na Seção 5.2 da [Estrutura de Autorização OAuth 2.0](https://tools.ietf.org/html/rfc6749). |
| error_description |Uma descrição mais detalhada do erro. Esta mensagem não deve ser amigável para o usuário final. |
| resource_id |Retorna o identificador exclusivo do recurso. O aplicativo cliente pode usar esse identificador como o valor do parâmetro `resource` ao solicitar um token para o recurso. <p><p> É importante que o aplicativo cliente Verifique esse valor, caso contrário, um serviço mal-intencionado poderá induzir um ataque **de elevação de privilégios** <p><p> A estratégia recomendada para evitar um ataque é verificar se `resource_id` corresponde à base da URL da API Web que está sendo acessada. Por exemplo, se https://service.contoso.com/data estiver sendo acessado, o `resource_id` poderá ser https://service.contoso.com/. O aplicativo cliente deverá rejeitar um `resource_id` que não comece com a URL base, a menos que haja uma maneira alternativa confiável para verificar a ID. |

#### <a name="bearer-scheme-error-codes"></a>Códigos de erro do esquema de portador
A especificação RFC 6750 define os seguintes erros para recursos que usam o cabeçalho WWW-Authenticate e o esquema de portador na resposta.

| Código de status HTTP | Código de erro | Descrição | Ação do cliente |
| --- | --- | --- | --- |
| 400 |invalid_request |A solicitação não está bem formada. Por exemplo, pode estar faltando um parâmetro ou usar o mesmo parâmetro duas vezes. |Corrija o erro e repita a solicitação. Esse tipo de erro deve ocorrer somente durante o desenvolvimento e ser detectado no teste inicial. |
| 401 |invalid_token |O token de acesso está ausente, é inválido ou foi revogado. O valor do parâmetro error_description fornece detalhes adicionais. |Solicite um novo token do servidor de autorização. Se o novo token falhar, ocorreu um erro inesperado. Envie uma mensagem de erro para o usuário e tente novamente após os atrasos aleatórios. |
| 403 |insufficient_scope |O token de acesso não contém as permissões de representação necessárias para acessar o recurso. |Envie uma nova solicitação de autorização para o ponto de extremidade de autorização. Se a resposta contiver o parâmetro de escopo, use o valor de escopo na solicitação para o recurso. |
| 403 |insufficient_access |O assunto do token não tem as permissões necessárias para acessar o recurso. |Solicite que o usuário use uma conta diferente ou solicite permissões para o recurso especificado. |

## <a name="refreshing-the-access-tokens"></a>Atualizando tokens de acesso

Os Tokens de Acesso têm curta duração e deverão ser atualizados depois de expirados para continuarem acessando recursos. Você pode atualizar o `access_token` ao enviar outra solicitação `POST` ao ponto de extremidade `/token`, mas dessa vez fornecendo o `refresh_token` em vez do `code`.  Os tokens de atualização são válidos para todos os recursos que seu cliente já recebeu autorização para acessar-portanto, um token de atualização emitido em uma solicitação de `resource=https://graph.microsoft.com` pode ser usado para solicitar um novo token de acesso para `resource=https://contoso.com/api`. 

Os tokens de atualização não têm tempos de vida especificados. Normalmente, os tempos de vida de tokens de atualização são relativamente longos. No entanto, em alguns casos, os tokens de atualização expiram, são revogados ou não têm privilégios suficientes para a ação desejada. Seu aplicativo precisa esperar e tratar os erros retornados pelo ponto de extremidade de emissão de token corretamente.

Quando você receber uma resposta com um erro de token de atualização, descarte o token de atualização atual e solicite um novo código de autorização ou token de acesso. Em particular, quando usar um token de atualização no fluxo de Concessão de Código de Autorização, se você receber uma resposta com os códigos de erro `interaction_required` ou `invalid_grant`, descarte o token de atualização e solicite um novo código de autorização.

Uma solicitação de exemplo para o ponto de extremidade **específico do locatário** (você também pode usar o ponto de extremidade **comum**) para obter um novo token de acesso usando um token de atualização que tem esta aparência:

```
// Line breaks for legibility only

POST /{tenant}/oauth2/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&grant_type=refresh_token
&resource=https%3A%2F%2Fservice.contoso.com%2F
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

### <a name="successful-response"></a>Resposta bem-sucedida
Uma resposta de token bem-sucedida terá A seguinte aparência:

```
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1460404526",
  "resource": "https://service.contoso.com/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1THdqcHdBSk9NOW4tQSJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0MDg2MywibmJmIjoxMzg4NDQwODYzLCJleHAiOjEzODg0NDQ3NjMsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6IjY4Mzg5YWUyLTYyZmEtNGIxOC05MWZlLTUzZGQxMDlkNzRmNSIsInVwbiI6ImZyYW5rbUBjb250b3NvLmNvbSIsInVuaXF1ZV9uYW1lIjoiZnJhbmttQGNvbnRvc28uY29tIiwic3ViIjoiZGVOcUlqOUlPRTlQV0pXYkhzZnRYdDJFYWJQVmwwQ2o4UUFtZWZSTFY5OCIsImZhbWlseV9uYW1lIjoiTWlsbGVyIiwiZ2l2ZW5fbmFtZSI6IkZyYW5rIiwiYXBwaWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJhcHBpZGFjciI6IjAiLCJzY3AiOiJ1c2VyX2ltcGVyc29uYXRpb24iLCJhY3IiOiIxIn0.JZw8jC0gptZxVC-7l5sFkdnJgP3_tRjeQEPgUn28XctVe3QqmheLZw7QVZDPCyGycDWBaqy7FLpSekET_BftDkewRhyHk9FW_KeEz0ch2c3i08NGNDbr6XYGVayNuSesYk5Aw_p3ICRlUV1bqEwk-Jkzs9EEkQg4hbefqJS6yS1HoV_2EsEhpd_wCQpxK89WPs3hLYZETRJtG5kvCCEOvSHXmDE6eTHGTnEgsIk--UlPe275Dvou4gEAwLofhLDQbMSjnlV5VLsjimNBVcSRFShoxmQwBJR_b2011Y5IuD6St5zPnzruBbZYkGNurQK63TJPWmRd3mbJsGM0mf3CUQ",
  "refresh_token": "AwABAAAAv YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfcUl4VBbiSHZyd1NVZG5QTIOcbObu3qnLutbpadZGAxqjIbMkQ2bQS09fTrjMBtDE3D6kSMIodpCecoANon9b0LATkpitimVCrl PM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4rTfgV29ghDOHRc2B-C_hHeJaJICqjZ3mY2b_YNqmf9SoAylD1PycGCB90xzZeEDg6oBzOIPfYsbDWNf621pKo2Q3GGTHYlmNfwoc-OlrxK69hkha2CF12azM_NYhgO668yfmVCrl-NyfN3oyG4ZCWu18M9-vEou4Sq-1oMDzExgAf61noxzkNiaTecM-Ve5cq6wHqYQjfV9DOz4lbceuYCAA"
}
```
| . | Descrição |
| --- | --- |
| token_type |O tipo de token. O único valor com suporte é **portador**. |
| expires_in |O tempo de vida restante do token em segundos. Um valor típico é 3600 (uma hora). |
| expires_on |A data e a hora em que o token expira. A data é representada como o número de segundos de 1970-01-01T0:0: 0Z UTC até a hora de expiração. |
| Kit |Identifica o recurso protegido que o token de acesso pode ser usado para acessar. |
| scope |As permissões de representação concedidas ao aplicativo cliente nativo. A permissão padrão é **user_impersonation**. O proprietário do recurso de destino pode registrar valores alternativos no Azure AD. |
| access_token |O novo token de acesso solicitado. |
| refresh_token |Um novo OAuth 2,0 refresh_token que pode ser usado para solicitar novos tokens de acesso quando o mesmo nessa resposta expirar. |

### <a name="error-response"></a>Resposta de erro
Uma resposta de erro de exemplo poderia ser assim:

```
{
  "error": "invalid_resource",
  "error_description": "AADSTS50001: The application named https://foo.microsoft.com/mail.read was not found in the tenant named 295e01fc-0c56-4ac3-ac57-5d0ed568f872. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant. You might have sent your authentication request to the wrong tenant.\r\nTrace ID: ef1f89f6-a14f-49de-9868-61bd4072f0a9\r\nCorrelation ID: b6908274-2c58-4e91-aea9-1f6b9c99347c\r\nTimestamp: 2016-04-11 18:59:01Z",
  "error_codes": [
    50001
  ],
  "timestamp": "2016-04-11 18:59:01Z",
  "trace_id": "ef1f89f6-a14f-49de-9868-61bd4072f0a9",
  "correlation_id": "b6908274-2c58-4e91-aea9-1f6b9c99347c"
}
```

| . | Descrição |
| --- | --- |
| error |Uma cadeia de caracteres de códigos de erro que pode ser usada para classificar tipos de erro que ocorrem e pode ser usada para responder aos erros. |
| error_description |Uma mensagem de erro específica que pode ajudar um desenvolvedor a identificar a causa raiz de um erro de autenticação. |
| error_codes |Uma lista de códigos de erro específicos do STS que podem ajudar no diagnóstico. |
| estampa |A hora em que o erro ocorreu. |
| trace_id |Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico. |
| correlation_id |Um identificador exclusivo para a solicitação que pode ajudar no diagnóstico entre os componentes. |

Para obter uma descrição dos códigos de erro e a ação recomendada do cliente, consulte [códigos de erro para erros de ponto de extremidade do token](#error-codes-for-token-endpoint-errors).

## <a name="next-steps"></a>Próximos passos
Para saber mais sobre o ponto de extremidade v 1.0 do Azure AD e como adicionar autenticação e autorização para seus aplicativos Web e APIs Web, consulte [aplicativos de exemplo](sample-v1-code.md).
