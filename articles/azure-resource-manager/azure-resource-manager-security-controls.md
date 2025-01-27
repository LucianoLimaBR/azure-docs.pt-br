---
title: Controles de segurança para Azure Resource Manager
description: Uma lista de verificação de controles de segurança internos para avaliar o serviço de Azure Resource Manager.
services: azure-resource-manager
author: msmbaldwin
manager: rkarlin
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 78f3f41ca2f0aa45d295fd9846cf3aa96cccea72
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390459"
---
# <a name="security-controls-for-azure-resource-manager"></a>Controles de segurança para Azure Resource Manager

Este artigo documenta os controles de segurança internos do Azure Resource Manager.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Proteção de dados

| Controle de segurança | Sim/Não | Notas |
|---|---|--|
| Criptografia no lado do servidor em repouso: chaves gerenciadas pela Microsoft | SIM |  |
| Criptografia em trânsito (como criptografia de ExpressRoute, criptografia de vnet e criptografia vnet)| SIM | HTTPS/TLS. |
| Criptografia no lado do servidor em repouso: chaves gerenciadas pelo cliente (BYOK) | N/D | O Azure Resource Manager não armazena nenhum conteúdo do cliente, só controla os dados. |
| Criptografia em nível de coluna (serviços de dados do Azure)| SIM | |
| Chamadas criptografadas à API| SIM | |

## <a name="network"></a>Rede

| Controle de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto de extremidade de serviço| Não | |
| Suporte à injeção de VNet| SIM | |
| Isolamento de rede e suporte de firewall| Não |  |
| Suporte a túnel forçado| Não |  |

## <a name="monitoring--logging"></a>Monitorando & log

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao monitoramento do Azure (log Analytics, app insights, etc.)| Não | |
| Registro e auditoria do plano de gerenciamento e controle| SIM | Os logs de atividades expõem todas as operações de gravação (PUT, POST e DELETE) executadas em seus recursos; consulte [Exibir logs de atividades para auditar ações em recursos](resource-group-audit.md). |
| Log e auditoria do plano de dados| N/D | |

## <a name="identity"></a>Identidade

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Authentication| SIM | Baseado em [Azure Active Directory](/azure/active-directory) .|
| Autorização| SIM | |

## <a name="configuration-management"></a>Gerenciamento de configuração

| Controle de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte ao gerenciamento de configuração (controle de versão de configuração, etc.)| SIM |  |

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre os [controles de segurança internos nos serviços do Azure](../security/fundamentals/security-controls.md).