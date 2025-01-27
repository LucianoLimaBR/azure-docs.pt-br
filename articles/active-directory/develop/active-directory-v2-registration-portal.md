---
title: Tópicos de ajuda do portal de registro de aplicativo
titleSuffix: Microsoft identity platform
description: Uma descrição de vários recursos no Portal de Registro de Aplicativos da Microsoft.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: ryanwi
ms.reviewer: lenalepa
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61787e7a86a1c40ec04fb7a3d605158c85782d94
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72803402"
---
# <a name="app-registration-reference"></a>Referência de registro de aplicativo
Este documento fornece contexto e descrições de vários recursos encontrados na experiência de [registros de aplicativo](https://aka.ms/appregistrations) no portal do Azure.

## <a name="my-applications-or-converged-applications"></a>Meus aplicativos ou Aplicativos convergidos
Esta lista contém todos os seus aplicativos registrados para uso com o ponto de extremidade da plataforma Microsoft Identity (v 2.0). Esses aplicativos permitem que usuários com contas pessoais da Microsoft e contas corporativas/de estudante do Azure Active Directory façam logon. Para saber mais sobre o ponto de extremidade da plataforma de identidade, consulte a [visão geral v 2.0](active-directory-appmodel-v2-overview.md). Esses aplicativos também podem ser usados para integração com o ponto de extremidade de autenticação da conta da Microsoft, `https://login.live.com`.

## <a name="azure-ad-only-applications"></a>Aplicativos somente do Azure AD
Esta lista contém todos os aplicativos registrados para uso com o ponto de extremidade do Azure AD v1.0. Esses aplicativos têm somente a capacidade de fazer logon dos usuários com contas corporativas/de estudante do Azure Active Directory. Essa lista inclui aplicativos que foram registrados usando a experiência de **registros de aplicativo** no [portal do Azure](https://portal.azure.com).

## <a name="live-sdk-applications"></a>Aplicativos do Live SDK
Esta lista contém todos os aplicativos registrados para uso exclusivo com uma conta da Microsoft. Eles não são habilitados para uso com o Azure Active Directory. Nessa lista você encontrará todos os aplicativos registrados anteriormente com o portal do desenvolvedor MSA em `https://account.live.com/developers/applications`. Todas as funções executadas anteriormente em `https://account.live.com/developers/applications` agora podem ser executadas em [registros de aplicativo](https://aka.ms/appregistrations).

## <a name="application-secrets"></a>Segredos do aplicativo
Segredos do aplicativo são credenciais que permitem ao aplicativo executar a [autenticação de cliente](https://tools.ietf.org/html/rfc6749#section-2.3) confiável com o Azure AD. No OAuth e no OpenID Connect, o segredo do aplicativo é conhecido como `client_secret`. No protocolo v 2.0, qualquer aplicativo que receba um token de segurança em um local endereçável da Web (usando um esquema `https` ) deve usar um segredo do aplicativo para se identificar ao Azure AD durante o resgate desse token de segurança. Além disso, qualquer cliente nativo que receba os tokens em um dispositivo será proibido de usar o segredo do aplicativo para executar a autenticação do cliente. Isso desencoraja o armazenamento de segredos em ambientes inseguros.

Cada aplicativo pode conter dois segredos do aplicativo válidos em qualquer momento. Mantendo dois segredos, você pode executar a substituição de chave periódica em todo o ambiente de seu aplicativo. Depois de migrar todo o seu aplicativo para um novo segredo, você pode excluir o segredo antigo e provisionar um novo.

No momento, há permissão para apenas dois tipos de segredos do aplicativo no portal de registro do aplicativo. Escolher **Gerar Nova Senha** vai gerar e armazenar um segredo compartilhado no respectivo armazenamento de dados, que você pode usar em seu aplicativo. Escolher **Gerar Novo Par de Chaves** criará um novo par de chaves público/privado que pode ser baixado e usado para autenticação de cliente no Azure AD. Escolher **Carregar a chave pública** permite que você use seu próprio par de chaves públicas/privadas.
Você deve carregar um certificado que contenha uma chave pública.

## <a name="profile"></a>Perfil
A seção de perfil do portal de registro de aplicativos pode ser usada para personalizar a página de entrada de seu aplicativo. No momento, você pode alterar o logotipo do aplicativo, a URL dos termos de serviço e a URL da política de privacidade da página de entrada. O logotipo deve ser uma imagem transparente com 48 x 48 ou 50 x 50 pixels em um arquivo GIF, PNG ou JPEG com 15 KB ou menos. Tente alterar os valores e veja a página de entrada resultante!

## <a name="live-sdk-support"></a>Suporte ao Live SDK
Quando você habilita o "Suporte ao Live SDK", qualquer segredo do aplicativo criado por você será provisionado nos repositórios de dados do Azure AD e da conta da Microsoft. Isso permite que seu aplicativo se integre diretamente com o serviço de Conta da Microsoft (login.live.com). Se você quiser criar um aplicativo usando a conta da Microsoft diretamente (em vez de usar o ponto de extremidade v 2.0), certifique-se de que o suporte ao Live SDK esteja habilitado.

Desativar o suporte do Live SDK garante que o segredo do aplicativo seja gravado apenas nos armazenamentos de dados do Azure AD. Os armazenamentos de dados do Azure AD incorporam regulamentos de nível empresarial que lhes permitem atender a determinados padrões, como a conformidade FISMA. Se você habilitar o suporte ao Live SDK, talvez seu aplicativo não fique em conformidade com alguns desses padrões.

Se você só planeja usar o ponto de extremidade v 2.0, você pode desabilitar com segurança o suporte ao Live SDK.

