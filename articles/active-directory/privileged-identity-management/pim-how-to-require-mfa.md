---
title: Autenticação multifator (MFA) e Azure Active Directory de Privileged Identity Management | Microsoft Docs
description: Saiba como o PIM (Azure AD Privileged Identity Management) valida a MFA (autenticação multifator).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/31/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12b6cd17fac77361fa20d3b3e048e64228a2ed3d
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809015"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>Autenticação multifator e Privileged Identity Management

É recomendável que você exija MFA (autenticação multifator) a todos os administradores. Isso reduz o risco de um ataque devido a uma senha comprometida.

Você pode exigir que os usuários concluam um desafio de autenticação de vários fatores ao entrarem. Você também pode exigir que os usuários concluam um desafio de autenticação de vários fatores ao ativarem uma função no PIM (Azure Active Directory do Azure AD Privileged Identity Management). Dessa forma, se o usuário não concluiu um desafio de autenticação de vários fatores quando ele entrou, ele será solicitado a fazer isso por Privileged Identity Management.

> [!IMPORTANT]
> Agora, a autenticação multifator do Azure funciona apenas com contas corporativas ou de estudante, não com contas pessoais da Microsoft (geralmente uma conta pessoal que é usada para entrar nos serviços da Microsoft, como Skype, Xbox ou Outlook.com). Por isso, qualquer pessoa que usa uma conta pessoal não pode ser um administrador qualificado porque não pode usar a autenticação multifator para ativar suas funções. Se esses usuários precisarem continuar a gerenciar cargas de trabalho usando uma conta da Microsoft, eleve-os a administradores permanentes por enquanto.

## <a name="how-pim-validates-mfa"></a>Como o PIM valida MFA

Há duas opções para validar a autenticação multifator quando um usuário ativa uma função.

A opção mais simples é contar com a autenticação multifator do Azure para usuários que estão ativando uma função privilegiada. Para fazer isso, primeiro verifique se os usuários estão licenciados, se necessário, e se foram registrados para a autenticação multifator do Azure. Para obter mais informações sobre como implantar a autenticação multifator do Azure, consulte [implantar a autenticação multifator do Azure baseada em nuvem](../authentication/howto-mfa-getstarted.md). É recomendável, mas não obrigatório, que você configure o Azure AD para impor a autenticação multifator para esses usuários quando eles entram. Isso ocorre porque as verificações de autenticação multifator serão feitas Privileged Identity Management si mesma.

Como alternativa, se os usuários autenticarem no local, você poderá fazer com que seu provedor de identidade seja responsável pela autenticação multifator. Por exemplo, se você tiver configurado os Serviços de Federação do AD para exigir a autenticação baseada em cartão inteligente antes de acessar o Azure AD, [Protegendo os recursos de nuvem usando a Autenticação Multifator do Azure e o AD FS](../authentication/howto-mfa-adfs.md) inclui instruções para configurar o AD FS a fim de enviar solicitações ao Azure AD. Quando um usuário tenta ativar uma função, Privileged Identity Management aceitará que a autenticação multifator já tenha sido validada para o usuário depois de receber as declarações apropriadas.

## <a name="next-steps"></a>Próximos passos

- [Definir as configurações de função do Azure AD no Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Definir configurações de função de recurso do Azure no Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
