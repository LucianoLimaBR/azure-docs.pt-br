---
title: Aplicativos nativos no Azure Active Directory
description: Descreve quais são os aplicativos nativos e as noções básicas sobre fluxo de protocolo, registro e expiração de token para esse tipo de aplicativo.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0034668231d97e64602bdbdd0836bded97bb733d
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373882"
---
# <a name="native-apps"></a>Aplicativos nativos

Aplicativos nativos são aplicativos que chamam uma API Web em nome de um usuário. Este cenário se baseia no tipo de concessão de código de autorização OAuth 2.0 com um cliente público, conforme descrito na seção 4.1 da [especificação do OAuth 2.0](https://tools.ietf.org/html/rfc6749). O aplicativo nativo obtém um token de acesso para o usuário usando o protocolo OAuth 2.0. Esse token de acesso é enviado na solicitação para a API da Web, que autoriza o usuário e retorna o recurso desejado.

## <a name="diagram"></a>Diagrama

![Diagrama de aplicativo nativo para API da Web](./media/authentication-scenarios/native_app_to_web_api.png)

## <a name="protocol-flow"></a>Fluxo do protocolo

Se você estiver usando as bibliotecas de autenticação do AD, a maioria dos detalhes do protocolo descritos abaixo será manipulada para você, como o pop-up do navegador, o armazenamento em cache do token e a manipulação de tokens de atualização.

1. Usando um pop-up de navegador, o aplicativo nativo faz uma solicitação para o ponto de extremidade de autorização no Azure AD. Essa solicitação inclui a ID do Aplicativo e o URI de redirecionamento do aplicativo nativo, conforme mostrado no portal do Azure, e o URI de ID do aplicativo para a API da Web. Se o usuário ainda não tiver feito logon, ele será solicitado a fazer logon novamente
1. O Azure AD autentica o usuário. Se for um aplicativo multilocatário e for necessário um consentimento para usar o aplicativo, o usuário deverá consentir, caso ainda não tenha feito isso. Depois de conceder autorização e após a autenticação bem-sucedida, o Azure AD emite uma resposta de código de autorização para o URI de redirecionamento do aplicativo cliente.
1. Quando o Azure AD emite uma resposta de código de autorização para o URI de redirecionamento, o aplicativo cliente interrompe a interação com o navegador e extrai o código de autorização da resposta. Usando esse código de autorização, o aplicativo cliente envia uma solicitação ao ponto de extremidade do token do Azure AD que inclui o código de autorização, detalhes sobre o aplicativo cliente (ID do Aplicativo e o URI de redirecionamento) e o recurso desejado (URI de ID do aplicativo para a API da Web).
1. O código de autorização e informações sobre o aplicativo cliente e a API da Web são validados pelo Azure AD. Após a validação bem-sucedida, o Azure AD retorna dois tokens: um token de acesso JWT e um token de atualização JWT. Além disso, o Azure AD retorna informações básicas sobre o usuário, como seu nome de exibição e ID do locatário.
1. Via HTTPS, o aplicativo cliente usa o token de acesso JWT retornado para adicionar a cadeia de caracteres JWT com uma designação de "Portador" no cabeçalho de autorização da solicitação à API da Web. A API da Web, em seguida, valida o token JWT e, se a validação for bem-sucedida, retorna o recurso desejado.
1. Quando o token de acesso expira, o aplicativo cliente recebe um erro que indica que o usuário precisa se autenticar novamente. Se o aplicativo tiver um token de atualização válido, ele pode ser usado para adquirir um novo token de acesso sem que um novo logon do usuário seja solicitado. Se o token de atualização expirar, o aplicativo precisará autenticar interativamente o usuário novamente.

> [!NOTE]
> O token de atualização emitido pelo Azure AD pode ser usado para acessar vários recursos. Por exemplo, se você tiver um aplicativo cliente que tenha permissão para chamar duas APIs da Web, o token de atualização pode ser usado para obter um token de acesso para a outra API da Web também.

## <a name="code-samples"></a>Exemplos de código

Consulte os exemplos de código para os cenários de aplicativo nativo para API da Web. E volte sempre, pois novos exemplos são adicionados com frequência. [Aplicativo nativo para API da Web](sample-v1-code.md#desktop-and-mobile-public-client-applications-calling-microsoft-graph-or-a-web-api)

## <a name="app-registration"></a>Registro do aplicativo

Para registrar um aplicativo com o ponto de extremidade v 1.0 do Azure AD, consulte [registrar um aplicativo](quickstart-register-app.md).

* Locatário único – O aplicativo nativo e a API Web devem ser registrados no mesmo diretório no Azure AD. A API da Web pode ser configurada para expor um conjunto de permissões, que são usadas para limitar o acesso do aplicativo nativo a seus recursos. O aplicativo cliente seleciona as permissões desejadas no menu suspenso "Permissões para Outros Aplicativos" no portal do Azure.
* Multilocatário – Primeiro, o aplicativo nativo registrado apenas no diretório do desenvolvedor ou do editor. Em segundo lugar, o aplicativo nativo é configurado para indicar as permissões necessárias para que seja funcional. Essa lista de permissões necessárias é mostrada em uma caixa de diálogo quando um usuário ou administrador no diretório de destino dá consentimento para o aplicativo, o que o torna disponível para sua organização. Alguns aplicativos exigem apenas permissões de nível de usuário, que qualquer usuário na organização pode conceder. Outros aplicativos exigem permissões de nível administrativo, que um usuário na organização não pode conceder. Somente um administrador do diretório pode dar consentimento para aplicativos que exigem esse nível de permissões. Quando o usuário ou administrador concede sua permissão, somente a API da Web é registrada no diretório. 

## <a name="token-expiration"></a>Expiração do token

Quando o aplicativo nativo usa seu código de autorização para obter um token de acesso JWT, ele também recebe um token de atualização JWT. Quando o token de acesso expira, o token de atualização pode ser usado para autenticar o usuário novamente sem a necessidade de fazer logon novamente. Esse token de atualização é usado para autenticar o usuário, o que resulta em um novo token de acesso e token de atualização.

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre outros [Tipos de aplicativo e cenários](app-types.md)
- Saiba mais sobre as [noções básicas de autenticação](v1-authentication-scenarios.md) do Azure AD
