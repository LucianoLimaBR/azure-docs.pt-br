---
title: O que são Zonas de Disponibilidade do Azure? | Microsoft Docs
description: Para criar aplicativos altamente disponíveis e resilientes no Azure, Zonas de Disponibilidade fornecer locais fisicamente separados que você pode usar para executar seus recursos.
services: ''
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/06/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 585f9f27e0562e9eabddd934a2b4f32a441b1777
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72512575"
---
# <a name="what-are-availability-zones-in-azure"></a>O que são Zonas de Disponibilidade no Azure?
Zonas de Disponibilidade é uma oferta de alta disponibilidade que protege seus aplicativos e dados de falhas do datacenter. Zonas de Disponibilidade são locais físicos exclusivos em uma região do Azure. Cada zona é composta por um ou mais data centers equipados com energia, resfriamento e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas. A separação física de Zonas de Disponibilidade dentro de uma região protege aplicativos e dados de falhas do datacenter. Os serviços com redundância de zona replicam seus aplicativos e dados em Zonas de Disponibilidade para proteger contra pontos únicos de falha. Com o Zonas de Disponibilidade, o Azure oferece um SLA de tempo de atividade de VM melhor do setor 99,99%. O [SLA completo do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) explica a disponibilidade garantida do Azure como um todo.

Uma zona de disponibilidade em uma região do Azure é uma combinação de um domínio de falha e um domínio de atualização. Por exemplo, se você criar três ou mais VMs em três zonas em uma região do Azure, suas VMs serão efetivamente distribuídas entre três domínios de falha e três domínios de atualização. A plataforma Azure reconhece essa distribuição entre domínios de atualização para garantir que as VMs em diferentes zonas não sejam atualizadas ao mesmo tempo.

Crie alta disponibilidade em sua arquitetura de aplicativos Colocalizando seus recursos de computação, armazenamento, rede e dados em uma zona e replicando em outras zonas. Os serviços do Azure que dão suporte a Zonas de Disponibilidade se enquadram em duas categorias:

- **Serviços zonais** – você fixa o recurso em uma zona específica (por exemplo, máquinas virtuais, discos gerenciados, endereços IP padrão) ou
- **Serviços com redundância de zona** – a plataforma replica automaticamente entre zonas (por exemplo, armazenamento com redundância de zona, banco de dados SQL).

Para obter uma continuidade de negócios abrangente no Azure, crie sua arquitetura de aplicativo usando a combinação de Zonas de Disponibilidade com pares de regiões do Azure. Você pode replicar de forma síncrona seus aplicativos e dados usando Zonas de Disponibilidade em uma região do Azure para alta disponibilidade e replicação assíncrona em regiões do Azure para proteção de recuperação de desastres.
 
![exibição conceitual de uma zona ficando inativa em uma região](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> Os identificadores de zona de disponibilidade (os números 1, 2 e 3 na imagem acima) são mapeados logicamente para as zonas físicas reais para cada assinatura de forma independente. Isso significa que a disponibilidade Zona 1 em uma determinada assinatura pode se referir a uma zona física diferente da Zona 1 de disponibilidade em uma assinatura diferente. Como consequência, é recomendável não retransmitir em IDs de zona de disponibilidade em assinaturas diferentes para o posicionamento da máquina virtual.

## <a name="services-support-by-region"></a>Suporte a serviços por região

As combinações de serviços e regiões do Azure que oferecem suporte a Zonas de Disponibilidade são:


|                                 |Américas |              |           |           | Europa |              |          |              | Pacífico Asiático |                 |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|
|          |EUA Central|Leste dos EUA|Leste dos EUA 2|Oeste dos EUA 2|França Central|Europa Setentrional|Sul do Reino Unido|Oeste da Europa|Leste do Japão|Sudeste Asiático|
| **Computação**                         |            |              |           |           |                |              |          |             |            |                |
| Máquinas Virtuais do Linux          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Máquinas virtuais do Windows        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Conjuntos de dimensionamento de máquina virtual      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Armazenamento**   |            |              |           |           |                |              |          |             |            |                |
| Managed Disks                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Armazenamento com redundância de zona          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Rede**                     |            |              |           |           |                |              |          |             |            |                |
| Endereço IP padrão        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Load Balancer Standard     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Gateway VPN            | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Gateway de ExpressRoute   | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Gateway de Aplicativo    | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Firewall do Azure           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |
| **Bancos de dados**                     |            |              |           |           |                |              |          |             |            |                |
| Azure Data Explorer                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;        | &#10003;       |
| Banco de dados SQL                    | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;Apresentação      | &#10003;       |
| Cache Redis do Azure           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |
| BD Cosmos do Azure                    | &#10003;   |  &#10003;  |  &#10003; | &#10003; |       |     | &#10003; |  &#10003;   |            | &#10003;       |
| **Analytics**                       |            |              |           |           |                |              |          |             |            |                |
| Hubs de Eventos                      | &#10003;   |   &#10003; | &#10003;  | &#10003;  | &#10003; | &#10003; | &#10003; | &#10003; | &#10003; | &#10003;       |
| **Integrar**                     |            |              |           |           |                |              |          |             |            |                |
| Barramento de serviço (somente camada Premium) | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |



## <a name="services-resiliency"></a>Resiliência de serviços
Todos os serviços de gerenciamento do Azure são arquitetados para serem resilientes de falhas no nível de região. No espectro de falhas, uma ou mais falhas de zona de disponibilidade em uma região têm um raio de falha menor em comparação a uma falha de região inteira. O Azure pode se recuperar de uma falha no nível de zona dos serviços de gerenciamento dentro da região ou de outra região do Azure. O Azure realiza uma manutenção crítica em uma zona por vez dentro de uma região, para evitar falhas que afetem os recursos do cliente implantados em Zonas de Disponibilidade dentro de uma região.

## <a name="pricing"></a>Preços
Não há nenhum custo adicional para máquinas virtuais implantadas em uma zona de disponibilidade. 99,99% de SLA de tempo de atividade de VM é oferecido quando duas ou mais VMs são implantadas em duas ou mais Zonas de Disponibilidade em uma região do Azure. Haverá encargos adicionais de transferência de dados da VM para a VM na zona de disponibilidade. Para obter mais informações, consulte a página de [preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/) .


## <a name="get-started-with-availability-zones"></a>Introdução ao Zonas de Disponibilidade
- [Criar uma máquina virtual](../virtual-machines/windows/create-portal-availability-zone.md)
- [Adicionar um disco gerenciado usando o PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Criar um conjunto de dimensionamento de máquinas virtuais com redundância de zona](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [VMs de balanceamento de carga entre zonas usando um Standard Load Balancer com um front-end com redundância de zona](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [VMs de balanceamento de carga em uma zona usando um Standard Load Balancer com um front-end zonal](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Armazenamento com redundância de zona](../storage/common/storage-redundancy-zrs.md)
- [Banco de Dados SQL](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)
- [Recuperação de desastre geográfica dos hubs de eventos](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Recuperação de desastre geográfica do barramento de serviço](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Criar um gateway de rede virtual com redundância de zona](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Adicionar região com redundância de zona para Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Introdução o cache do Azure para Redis Zonas de Disponibilidade](https://aka.ms/redis/az/getstarted)

## <a name="next-steps"></a>Próximos passos
- [Modelos de início rápido](https://aka.ms/azqs)
