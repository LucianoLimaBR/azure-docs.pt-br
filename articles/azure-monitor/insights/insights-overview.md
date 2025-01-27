---
title: Visão geral das informações no Azure Monitor | Microsoft Docs
description: As informações fornecem uma experiência de monitoramento Personalizada em Azure Monitor para aplicativos e serviços específicos. Este artigo fornece uma breve descrição de cada uma das informações disponíveis no momento.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: 3abf3d562b9ca4d6fc9907c065dbaf9a52d58c5f
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555351"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Visão geral das informações no Azure Monitor
As informações fornecem uma experiência de monitoramento Personalizada para aplicativos e serviços específicos. Eles armazenam dados na [plataforma de dados Azure monitor](../platform/data-platform.md) e aproveitam outros recursos de Azure monitor para análise e alertas, mas podem coletar dados adicionais e fornecer uma experiência de usuário exclusiva no portal do Azure. Acesse informações da seção **insights** do menu Azure Monitor na portal do Azure.

As seções a seguir fornecem uma breve descrição das informações que estão disponíveis no momento no Azure Monitor. Consulte a documentação detalhada para obter detalhes sobre cada um.

## <a name="application-insights"></a>Percepções sobre o Aplicativo
O Application Insights é um serviço de gerenciamento de desempenho de aplicativos (APM) extensível para desenvolvedores da Web em várias plataformas. Use-o para monitorar seu aplicativo Web em tempo real. Ele funciona para aplicativos em uma ampla variedade de plataformas, incluindo .NET, Node. js e Java EE, hospedado local, híbrido ou qualquer nuvem pública. Ele também se integra ao seu processo de DevOps e tem pontos de conexão para uma variedade de ferramentas de desenvolvimento.

Veja [o que é Application insights?](../app/app-insights-overview.md).

![Percepções sobre o Aplicativo](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Azure Monitor para contêineres
Azure Monitor para contêineres monitora o desempenho de cargas de trabalho de contêiner implantadas em instâncias de contêiner do Azure ou clusters kubernetes gerenciados hospedados no AKS (serviço kubernetes do Azure). O monitoramento de seus contêineres é essencial, especialmente quando você está executando um cluster de produção, em escala, com vários aplicativos.

Consulte [a visão geral de Azure monitor para contêineres](../insights/container-insights-overview.md).

![Azure Monitor para contêineres](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Azure Monitor para grupos de recursos (visualização)
Azure Monitor para grupos de recursos ajuda a fazer a triagem e diagnosticar quaisquer problemas encontrados por seus recursos individuais, ao mesmo tempo em que oferece contexto para a integridade e o desempenho do grupo de recursos como um todo.

Consulte [monitorar grupos de recursos com Azure monitor (versão prévia)](../insights/resource-group-insights.md).

![Azure Monitor para grupos de recursos](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Azure Monitor para VMs (versão prévia)
O Azure Monitor para VMs monitora as máquinas virtuais (VM) do Azure e os conjuntos de dimensionamento de máquinas virtuais em escala. Ele analisa o desempenho e a integridade de suas VMs Windows e Linux e monitora seus processos e dependências em outros recursos e processos externos.

Veja [o que é Azure monitor para VMs?](vminsights-overview.md)

![Azure Monitor para VMs](media/insights-overview/vm-insights.png)

## <a name="next-steps"></a>Próximos passos
* Saiba mais sobre a [plataforma de dados Azure monitor](../platform/data-platform.md) aproveitada pelo insights.
* Saiba mais sobre as diferentes [fontes de dados usadas pelo Azure monitor](../platform/data-sources.md) e os diferentes tipos de dados coletados por cada uma das informações.
