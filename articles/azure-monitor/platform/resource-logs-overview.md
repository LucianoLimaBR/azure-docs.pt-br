---
title: Visão geral dos logs de recursos do Azure | Microsoft Docs
description: Entenda os serviços com suporte e o esquema de eventos para logs de recursos do Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 10/22/2019
ms.openlocfilehash: e1bf6a55568671ddb8f6999356cc9be43ce6a728
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72804017"
---
# <a name="azure-resource-logs-overview"></a>Visão geral dos logs de recursos do Azure
Os logs de recursos do Azure são [os logs de plataforma](platform-logs-overview.md) emitidos pelos recursos do Azure que descrevem sua operação interna. Todos os logs de recursos compartilham um esquema comum de nível superior com a flexibilidade de cada serviço para emitir propriedades exclusivas para seus próprios eventos.

> [!NOTE]
> Os logs de recursos eram anteriormente conhecidos como logs de diagnóstico.

## <a name="collecting-resource-logs"></a>Coletando logs de recursos
Os logs de recursos são gerados automaticamente por recursos do Azure com suporte, mas não são coletados a menos que você os Configure usando uma [configuração de diagnóstico](diagnostic-settings.md). Crie uma configuração de diagnóstico para cada recurso do Azure para encaminhar os logs para os seguintes destinos:

| Destino | Cenário |
|:---|:---|:---|
| [Espaço de Trabalho do Log Analytics](resource-logs-collect-storage.md) | Analise os logs com outros dados de monitoramento e aproveite Azure Monitor recursos como consultas de log e alertas de log. |
| [Armazenamento do Azure](archive-diagnostic-logs.md) | Arquive os logs para auditoria ou backup. |
| [Hub de eventos](resource-logs-stream-event-hubs.md) | Transmita os logs para sistemas de registro em log e telemetria de terceiros.  |

## <a name="compute-resources"></a>Recursos de computação
Os logs de recursos diferem dos logs no nível do sistema operacional convidado nos recursos de computação do Azure. Os recursos de computação exigem um agente para coletar logs e métricas de seu sistema operacional convidado, incluindo dados como logs de eventos, syslog e contadores de desempenho. Use a [extensão de diagnóstico](agents-overview.md#azure-diagnostic-extension) para rotear dados de log de máquinas virtuais do Azure e o [agente de log Analytics](agents-overview.md#log-analytics-agent) para coletar logs e métricas de máquinas virtuais no Azure, em outras nuvens e localmente em um espaço de trabalho log Analytics. Consulte [fontes de dados de monitoramento para obter Azure monitor](data-sources.md) para obter detalhes.

## <a name="resource-logs-schema"></a>Esquema de logs de recursos
Para obter mais informações sobre o esquema e as categorias dos logs de recursos, consulte [esquema do log de recursos](diagnostic-logs-schema.md). 

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre outros logs da plataforma Azure](platform-logs-overview.md) que você pode usar para monitorar o Azure.
