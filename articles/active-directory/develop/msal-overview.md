---
title: Saiba mais sobre a MSAL (biblioteca de autenticação da Microsoft)
titleSuffix: Microsoft identity platform
description: A MSAL (Biblioteca de Autenticação da Microsoft) permite que os desenvolvedores de aplicativos adquiram tokens para chamar APIs Web seguras. Essas APIs Web podem ser o Microsoft Graph, outras APIs da Microsoft, APIs Web de terceiros ou sua própria API Web. A MSAL dá suporte a várias arquiteturas de aplicativos e plataformas.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/4/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: e9d3cf20e7e950aed01c6a0dc5a4ea95a81f815d
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802570"
---
# <a name="overview-of-microsoft-authentication-library-msal"></a>Visão geral da MSAL (Biblioteca de Autenticação da Microsoft)
A MSAL (Biblioteca de Autenticação da Microsoft) permite que os desenvolvedores adquiram [tokens](developer-glossary.md#security-token) do ponto de extremidade da plataforma de identidade da Microsoft para acessar APIs Web seguras. Essas APIs Web podem ser o Microsoft Graph, outras APIs da Microsoft, APIs Web de terceiros ou sua própria API Web. A MSAL está disponível para .NET, JavaScript, Android e iOS, que dão suporte a várias arquiteturas de aplicativos e plataformas diferentes.

A MSAL oferece muitas maneiras de obter tokens, com uma API consistente para várias plataformas. O uso da MSAL oferece os seguintes benefícios:

* Não é necessário usar diretamente as bibliotecas OAuth ou o código no protocolo em seu aplicativo.
* Adquire tokens em nome de um usuário ou em nome de um aplicativo (quando aplicável à plataforma).
* Mantém um cache de token e atualiza tokens para você quando estão perto de expirarem. Você não precisa lidar com a expiração de token por conta própria.
* Ajuda você a especificar em qual público-alvo você deseja que o seu aplicativo entre (sua organização, várias organizações, contas corporativa, de estudante e pessoais da Microsoft, identidades sociais com o Azure AD B2C, usuários em nuvens soberanas e nacionais).
* Ajuda você a configurar seu aplicativo com base nos arquivos de configuração.
* Ajuda você a solucionar problemas do aplicativo expondo exceções, log e telemetria acionáveis.

## <a name="application-types-and-scenarios"></a>Tipos e cenários de aplicativos
Usando a MSAL, um token pode ser adquirido de diversos tipos de aplicativos: aplicativos Web, APIs Web, aplicativos de página única (JavaScript), aplicativos móveis e nativos, daemons e aplicativos do servidor. 

A MSAL pode ser usada em muitos cenários de aplicativos, incluindo os seguintes:

* [Aplicativos de página única (JavaScript)](scenario-spa-overview.md) 
* [Aplicativo Web que conecta usuários](scenario-web-app-sign-user-overview.md)
* [Aplicativo Web que conecta um usuário e chama uma API Web em nome dele](scenario-web-app-call-api-overview.md)
* [Proteção de uma API Web, de modo que somente os usuários autenticados possam acessá-la](scenario-protected-web-api-overview.md)
* [API Web que chama outra API Web downstream em nome do usuário conectado](scenario-web-api-call-api-overview.md)
* [Aplicativo da área de trabalho que chama uma API Web em nome do usuário conectado](scenario-desktop-overview.md)
* [Aplicativo móvel que chama uma API Web em nome do usuário conectado de maneira interativa](scenario-mobile-overview.md).
* [Aplicativo daemon da área de trabalho/de serviço que chama uma API Web em seu próprio nome](scenario-daemon-overview.md)

## <a name="languages-and-frameworks"></a>Linguagens e estruturas

| Biblioteca | Plataformas e estruturas compatíveis|
| --- | --- | 
| [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)| .NET Framework, .NET Core, Xamarin Android, Xamarin iOS, Plataforma Universal do Windows|
| [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)| Estruturas JavaScript/TypeScript como AngularJS, Ember.js ou Durandal.js|
| [MSAL para Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)|Android|
| [MSAL para iOS e macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|iOS e macOS|
| [Visualização do MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)|Java|

## <a name="differences-between-adal-and-msal"></a>Diferenças entre a ADAL e a MSAL

A ADAL (Biblioteca de Autenticação do Active Directory) é integrada ao ponto de extremidade do Azure AD para desenvolvedores (v1.0), enquanto a MSAL é integrada ao ponto de extremidade da plataforma de identidade da Microsoft (v2.0). O ponto de extremidade v1.0 dá suporte a contas corporativas, mas não a contas pessoais. O ponto de extremidade v2.0 é a unificação de contas pessoais e contas corporativas da Microsoft em um só sistema de autenticação. Além disso, com a MSAL, você também pode obter autenticações para o Azure AD B2C.

Para obter informações mais específicas, leia sobre [como migrar para o MSAL.NET do ADAL.NET](msal-net-migration.md) e [como migrar para o MSAL.js do ADAL.js](msal-compare-msal-js-and-adal-js.md).
