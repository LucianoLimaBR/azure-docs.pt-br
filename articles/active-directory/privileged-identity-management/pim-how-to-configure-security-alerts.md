---
title: Configurar alertas de segurança para funções do Azure AD no PIM-Azure Active Directory | Microsoft Docs
description: Saiba como configurar alertas de segurança para funções do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e188ef651f6fe539932cf1670f914e8b57564567
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809092"
---
# <a name="configure-security-alerts-for-azure-ad-roles-in-privileged-identity-management"></a>Configurar alertas de segurança para funções do Azure AD no Privileged Identity Management

Privileged Identity Management (PIM) gera alertas quando há atividade suspeita ou não segura em sua organização do Azure Active Directory (AD do Azure). Quando um alerta é disparado, ele aparece no painel Privileged Identity Management. Selecione o alerta para ver um relatório que lista os usuários ou as funções que dispararam o alerta.

![Funções do Azure AD – painel de alerta listando alertas e a severidade](./media/pim-how-to-configure-security-alerts/pim-directory-alerts.png)

## <a name="security-alerts"></a>Alertas de segurança

Esta seção lista todos os alertas de segurança para funções do Azure AD, juntamente com como corrigir e como evitar. Severidade tem o seguinte significado:

- **Alta**: exige ação imediata devido a uma violação da política.
- **Média**: não exige ação imediata, mas sinaliza uma possível violação da política.
- **Baixa**: não requer ação imediata, mas sugere uma alteração de política preferencial.

### <a name="administrators-arent-using-their-privileged-roles"></a>Os administradores não estão usando suas funções privilegiadas

| | |
| --- | --- |
| **Severidade** | Baixo |
| **Por que recebo este alerta?** | Os usuários que receberam papéis privilegiados que não precisam aumentam a chance de um ataque. Também é mais fácil para os invasores permanecerem despercebidos nas contas que não estão sendo ativamente usadas. |
| **Como corrigir?** | Examine os usuários na lista e remova-os das funções privilegiadas que eles não precisam. |
| **Prevenção** | Atribua funções privilegiadas somente a usuários que têm uma justificativa comercial. </br>Agende revisões de [acesso regulares](pim-how-to-start-security-review.md) para verificar se os usuários ainda precisam de acesso. |
| **Ação de mitigação no portal** | Remove a conta da sua função privilegiada. |
| **Gatilho** | Disparado se um usuário passar por um número especificado de dias sem ativar uma função. |
| **Número de dias** | Essa configuração especifica o número máximo de dias, de 0 a 100, que um usuário pode ir sem ativar uma função.|

### <a name="roles-dont-require-multi-factor-authentication-for-activation"></a>Funções não exigem autenticação multifator para ativação

| | |
| --- | --- |
| **Severidade** | Baixo |
| **Por que recebo este alerta?** | Sem a autenticação multifator, os usuários comprometidos podem ativar funções privilegiadas. |
| **Como corrigir?** | Examine a lista de funções e [exija a autenticação multifator](pim-how-to-change-default-settings.md) para cada função. |
| **Prevenção** | [Exigir MFA](pim-how-to-change-default-settings.md) para cada função.  |
| **Ação de mitigação no portal** | Torna necessária a autenticação multifator para a ativação da função com privilégios. |

### <a name="the-tenant-doesnt-have-azure-ad-premium-p2"></a>O locatário não tem o Microsoft Azure AD Premium P2

| | |
| --- | --- |
| **Severidade** | Baixo |
| **Por que recebo este alerta?** | O locatário atual não tem o Microsoft Azure AD Premium P2. |
| **Como corrigir?** | Revise informações sobre [edições do Microsoft Azure Active Directory](../fundamentals/active-directory-whatis.md). Atualizar para o Microsoft Azure Active Directory Premium P2. |

### <a name="potential-stale-accounts-in-a-privileged-role"></a>Contas obsoletas possíveis em uma função com privilégios

| | |
| --- | --- |
| **Severidade** | Média |
| **Por que recebo este alerta?** | As contas em uma função privilegiada não alteraram sua senha nos últimos 90 dias. Essas contas podem ser de serviço ou compartilhadas, que não estejam passando por manutenção e estejam vulneráveis aos invasores. |
| **Como corrigir?** | Examine as contas na lista. Se eles não precisarem mais de acesso, remova-os de suas funções privilegiadas. |
| **Prevenção** | Certifique-se de que as contas compartilhadas estejam girando senhas fortes quando houver uma alteração nos usuários que conhecem a senha. </br>Revise regularmente as contas com funções privilegiadas usando [ revisões de acesso ](pim-how-to-start-security-review.md) e remova as atribuições de funções que não são mais necessárias. |
| **Ação de mitigação no portal** | Remove a conta da sua função privilegiada. |
| **melhores práticas** | As contas de acesso compartilhado, de serviço e de emergência que se autenticam usando uma senha e são atribuídas a funções administrativas altamente privilegiadas, como administrador global ou administrador de segurança, devem ter suas senhas giradas para os seguintes casos:<ul><li>Após um incidente de segurança envolvendo uso indevido ou comprometimento de direitos de acesso administrativo</li><li>Depois que os privilégios de qualquer usuário são alterados para que eles não sejam mais administradores (por exemplo, depois que um funcionário que era administrador deixa a TI ou deixa a organização)</li><li>Em intervalos regulares (por exemplo, trimestral ou anual), mesmo que não haja nenhuma violação ou alteração conhecida na equipe de TI</li></ul>Como várias pessoas têm acesso às credenciais dessas contas, as credenciais devem ser rotacionadas para garantir que as pessoas que deixaram suas funções não possam mais acessar as contas. [Saiba mais](https://aka.ms/breakglass) |

### <a name="roles-are-being-assigned-outside-of-privileged-identity-management"></a>As funções estão sendo atribuídas fora do Privileged Identity Management

| | |
| --- | --- |
| **Severidade** | Alto |
| **Por que recebo este alerta?** | As atribuições de função com privilégios feitas fora do Privileged Identity Management não são monitoradas corretamente e podem indicar um ataque ativo. |
| **Como corrigir?** | Examine os usuários na lista e remova-os das funções privilegiadas atribuídas fora do Privileged Identity Management. |
| **Prevenção** | Investigue onde os usuários estão sendo atribuídos a funções privilegiadas fora do Privileged Identity Management e proíba as atribuições futuras a partir daí. |
| **Ação de mitigação no portal** | Remove o usuário de sua função privilegiada. |

### <a name="there-are-too-many-global-administrators"></a>Há muitos administradores globais

| | |
| --- | --- |
| **Severidade** | Baixo |
| **Por que recebo este alerta?** | O administrador global é a função com privilégios mais altos. Se um administrador global estiver comprometido, o invasor ganhará acesso a todas as suas permissões, o que coloca todo o sistema em risco. |
| **Como corrigir?** | Examine os usuários na lista e remova qualquer que não precise absolutamente da função de administrador global. </br>Em vez disso, atribua funções com privilégios inferiores a esses usuários. |
| **Prevenção** | Designe aos usuários a função menos privilegiada de que precisam. |
| **Ação de mitigação no portal** | Remove a conta da sua função privilegiada. |
| **Gatilho** | Acionado se dois critérios diferentes forem atendidos e você puder configurar ambos. Primeiro, você precisa alcançar um certo limite de administradores globais. Em segundo lugar, um determinado percentual de suas atribuições de função total deve ser administradores globais. Se você atender apenas a uma dessas medidas, o alerta não será exibido. |
| **Número mínimo de administradores globais** | Essa configuração especifica o número de administradores globais, de 2 a 100, que você considera muito poucos para sua organização do Azure AD. |
| **Percentual de administradores globais** | Essa configuração especifica o percentual mínimo de administradores que são administradores globais, de 0% a 100%, abaixo do qual você não deseja que sua organização do Azure AD seja DIP. |

### <a name="roles-are-being-activated-too-frequently"></a>As funções estão sendo ativadas com muita frequência

| | |
| --- | --- |
| **Severidade** | Baixo |
| **Por que recebo este alerta?** | Múltiplas ativações para o mesmo papel privilegiado pelo mesmo usuário é um sinal de um ataque. |
| **Como corrigir?** | Revise os usuários na lista e assegure-se de que a [duração da ativação](pim-how-to-change-default-settings.md) para sua função privilegiada esteja definida por tempo suficiente para que eles executem suas tarefas. |
| **Prevenção** | Assegure-se de que a [duração da ativação](pim-how-to-change-default-settings.md) para funções privilegiadas esteja definida por tempo suficiente para que os usuários executem suas tarefas.</br>[Exigir autenticação multifator](pim-how-to-change-default-settings.md) para funções privilegiadas que têm contas compartilhadas por vários administradores. |
| **Ação de mitigação no portal** | N/D |
| **Gatilho** | Disparado se um usuário ativar a mesma função privilegiada várias vezes dentro de um período especificado. Você pode configurar o período e o número de ativações. |
| **Período de tempo de renovação de ativação** | Essa configuração especifica em dias, horas, minutos e segundos o período de tempo que você deseja usar para rastrear renovações suspeitas. |
| **Número de renovações de ativação** | Essa configuração especifica o número de ativações, de 2 a 100, em que você deseja ser notificado, dentro do período de tempo escolhido. Você pode mudar essa configuração movendo o controle deslizante ou digitando um número na caixa de texto. |

## <a name="configure-security-alert-settings"></a>Definir configurações de alerta de segurança

Você pode personalizar alguns dos alertas de segurança no Privileged Identity Management para trabalhar com as necessidades e metas de segurança de sua organização. Siga estas etapas para abrir as configurações de alerta de segurança:

1. Abra **Privileged Identity Management** no Azure AD.

1. Selecione **funções do Azure ad**.

1. Selecione **configurações** e **alertas**.

    ![Funções do Azure AD – configurações com alertas selecionados](./media/pim-how-to-configure-security-alerts/settings-alerts.png)

1. Selecione um nome de alerta para definir a configuração para esse alerta.

    ![Para o alerta selecionado, painel configurações de alerta de segurança](./media/pim-how-to-configure-security-alerts/security-alert-settings.png)

## <a name="next-steps"></a>Próximos passos

- [Definir as configurações de função do Azure AD no Privileged Identity Management](pim-how-to-change-default-settings.md)
