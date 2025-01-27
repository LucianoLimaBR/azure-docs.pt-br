---
title: Opções de rede Azure Functions
description: Uma visão geral de todas as opções de rede disponíveis no Azure Functions
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 9fe7147325b2e14a7ae6bb4b31aa941fb4059b11
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72690820"
---
# <a name="azure-functions-networking-options"></a>Opções de rede Azure Functions

Este artigo descreve os recursos de rede disponíveis nas opções de hospedagem para Azure Functions. Todas as opções de rede a seguir fornecem alguma capacidade de acessar recursos sem usar endereços roteáveis da Internet ou restringir o acesso à Internet a um aplicativo de funções. 

Os modelos de hospedagem têm níveis diferentes de isolamento de rede disponíveis. Escolher a correta ajudará você a atender aos seus requisitos de isolamento de rede.

Você pode hospedar aplicativos de funções de duas maneiras:

* Há um conjunto de opções de plano que são executadas em uma infraestrutura multilocatário, com vários níveis de conectividade de rede virtual e opções de dimensionamento:
    * O [plano de consumo](functions-scale.md#consumption-plan), que é dimensionado dinamicamente em resposta à carga e oferece opções de isolamento de rede mínimas.
    * O [plano Premium](functions-scale.md#premium-plan), que também é dimensionado dinamicamente, ao mesmo tempo que oferece isolamento de rede mais abrangente.
    * O [plano do serviço de aplicativo](functions-scale.md#app-service-plan)do Azure, que opera em uma escala fixa e oferece isolamento de rede semelhante ao plano Premium.
* Você pode executar funções em um [ambiente do serviço de aplicativo](../app-service/environment/intro.md). Esse método implanta sua função em sua rede virtual e oferece controle e isolamento de rede total.

## <a name="matrix-of-networking-features"></a>Matriz de recursos de rede

|                |[Plano de consumo](functions-scale.md#consumption-plan)|[Plano Premium (visualização)](functions-scale.md#premium-plan)|[Plano do Serviço de Aplicativo](functions-scale.md#app-service-plan)|[Ambiente do Serviço de Aplicativo](../app-service/environment/intro.md)|
|----------------|-----------|----------------|---------|-----------------------|  
|[Restrições de IP de entrada & acesso ao site privado](#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|
|[Integração de rede virtual](#virtual-network-integration)|❌No|✅Yes (regional)|✅Yes (regional e gateway)|✅Yes|
|[Gatilhos de rede virtual (não HTTP)](#virtual-network-triggers-non-http)|❌No| ❌No|✅Yes|✅Yes|
|[Conexões híbridas](#hybrid-connections)|❌No|✅Yes|✅Yes|✅Yes|
|[Restrições de IP de saída](#outbound-ip-restrictions)|❌No| ❌No|❌No|✅Yes|


## <a name="inbound-ip-restrictions"></a>Restrições de IP de entrada

Você pode usar restrições de IP para definir uma lista ordenada de prioridade de endereços IP que têm acesso permitido/negado ao seu aplicativo. A lista pode incluir endereços IPv4 e IPv6. Quando há uma ou mais entradas, um "negar tudo" implícito existe no final da lista. As restrições de IP funcionam com todas as opções de Hospedagem de função.

> [!NOTE]
> Com as restrições de rede em vigor, você só pode usar o editor do portal de dentro de sua rede virtual ou quando tiver colocado o IP da máquina que está usando para acessar o portal do Azure. No entanto, você ainda pode acessar quaisquer recursos na guia **recursos da plataforma** de qualquer computador.

Para saber mais, consulte [Azure app restrições de acesso estático do serviço](../app-service/app-service-ip-restrictions.md).

## <a name="private-site-access"></a>Acesso ao site privado

Acesso ao site privado refere-se a tornar seu aplicativo acessível somente de uma rede privada, como de dentro de uma rede virtual do Azure. 
* O acesso ao site privado está disponível nos serviços [Premium](./functions-premium-plan.md), [consumo], (funções-escala. MD # consumo-plano) e [serviço de aplicativo](functions-scale.md#app-service-plan) quando os pontos de **extremidade de serviço** são configurados. 
    * Os pontos de extremidade de serviço podem ser configurados por aplicativo em recursos da plataforma > rede > configurar restrições de acesso > Adicionar regra. As redes virtuais podem ser selecionadas agora como o "tipo" de uma regra.
    * Para obter mais informações, consulte [pontos de extremidade de serviço de rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md)
        * Tenha em mente que, com os pontos de extremidade de serviço, sua função ainda tem acesso total de saída à Internet, mesmo com a integração de rede virtual configurada.
* O acesso ao site privado também está disponível com um Ambiente do Serviço de Aplicativo configurado com um ILB (balanceador de carga interno). Para obter mais informações, consulte [criar e usar um balanceador de carga interno com um ambiente do serviço de aplicativo](../app-service/environment/create-ilb-ase.md).

## <a name="virtual-network-integration"></a>Integração de rede virtual

A integração de rede virtual permite que seu aplicativo de funções acesse recursos dentro de uma rede virtual. Esse recurso está disponível no plano Premium e no plano do serviço de aplicativo. Se seu aplicativo estiver em um Ambiente do Serviço de Aplicativo, ele já estará em uma rede virtual e não exigirá o uso da integração de rede virtual para alcançar recursos na mesma rede virtual.

Você pode usar a integração de rede virtual para habilitar o acesso de aplicativos a bancos de dados e serviços Web em execução em sua rede virtual. Com a integração de rede virtual, você não precisa expor um ponto de extremidade público para aplicativos em sua VM. Você pode usar os endereços não roteáveis privados da Internet.

Há dois formulários para o recurso de integração de rede virtual

1. A integração de rede virtual regional permite a integração com redes virtuais na mesma região. Essa forma do recurso requer uma sub-rede em uma rede virtual na mesma região. Este recurso ainda está em visualização, mas tem suporte para cargas de trabalho de produção do aplicativo do Windows com algumas limitações indicadas abaixo.
2. A integração de rede virtual necessária do Gateway permite a integração com redes virtuais em regiões remotas ou com redes virtuais clássicas. Esta versão do recurso requer a implantação de um gateway de rede virtual em sua VNet. Esse é o recurso baseado em VPN ponto a site e só é suportado com aplicativos do Windows.

Um aplicativo pode usar apenas um formulário do recurso de integração VNet de cada vez. Em seguida, a pergunta é qual recurso você deve usar. Você pode usar o para muitas coisas. No entanto, os diferenciadores claros são:

| Persisti  | Solução | 
|----------|----------|
| Deseja alcançar um endereço RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) na mesma região | Integração de VNet regional |
| Deseja acessar recursos em uma VNet clássica ou em uma VNet em outra região | Integração VNet necessária do gateway |
| Deseja alcançar os pontos de extremidade RFC 1918 no ExpressRoute | Integração de VNet regional |
| Deseja acessar recursos entre pontos de extremidade de serviço | Integração de VNet regional |

Nenhum recurso permitirá que você alcance endereços não RFC 1918 no ExpressRoute. Para fazer isso, você precisa usar um ASE por enquanto.

O uso da integração VNet regional não conecta sua VNet ao local ou configura pontos de extremidade de serviço. Isso é uma configuração de rede separada. A integração VNet regional simplesmente permite que seu aplicativo faça chamadas entre esses tipos de conexão.

Independentemente da versão usada, a integração VNet dá ao seu aplicativo de funções acesso aos recursos em sua rede virtual, mas não concede acesso de site privado ao seu aplicativo de funções da rede virtual. Acesso ao site privado refere-se a tornar seu aplicativo acessível somente de uma rede privada, como de dentro de uma rede virtual do Azure. A integração VNet é apenas para fazer chamadas de saída de seu aplicativo para sua VNet. 

O recurso de integração VNet:

* Requer um plano do serviço de aplicativo Standard, Premium ou PremiumV2
* dá suporte a TCP e UDP
* funciona com aplicativos do serviço de aplicativo e aplicativos de funções

Há algumas coisas para as quais a integração VNet não oferece suporte, incluindo:

* montando uma unidade
* Integração ao AD 
* Output

A integração de rede virtual no functions usa a infraestrutura compartilhada com aplicativos Web do serviço de aplicativo. Para ler mais sobre os dois tipos de integração de rede virtual, consulte:
* [Integração VNET regional](../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration)
* [Integração VNet necessária do gateway](../app-service/web-sites-integrate-with-vnet.md#gateway-required-vnet-integration)

Para saber mais sobre como usar a integração de rede virtual, consulte [integrar um aplicativo de funções a uma rede virtual do Azure](functions-create-vnet.md).

## <a name="connecting-to-service-endpoint-secured-resources"></a>Conectando a recursos protegidos do ponto de extremidade de serviço

> [!note] 
> Temporariamente, pode levar até 12 horas para que novos pontos de extremidade de serviço fiquem disponíveis para seu aplicativo de funções depois que você configurar restrições de acesso no recurso downstream. Durante esse tempo, o recurso ficará completamente indisponível para seu aplicativo.

Para fornecer um nível mais alto de segurança, você pode restringir vários serviços do Azure a uma rede virtual usando pontos de extremidade de serviço. Em seguida, você deve integrar seu aplicativo de funções com essa rede virtual para acessar o recurso. Essa configuração tem suporte em todos os planos que dão suporte à integração de rede virtual.

[Leia mais sobre os pontos de extremidade de serviço de rede virtual aqui.](../virtual-network/virtual-network-service-endpoints-overview.md)

### <a name="restricting-your-storage-account-to-a-virtual-network"></a>Restringindo sua conta de armazenamento a uma rede virtual
Ao criar um aplicativo de funções, você deve criar ou vincular a uma conta de armazenamento do Azure de uso geral que dá suporte ao armazenamento de BLOBs, filas e tabelas. No momento, não é possível usar nenhuma restrição de rede virtual nesta conta. Se você configurar um ponto de extremidade de serviço de rede virtual na conta de armazenamento que você está usando para seu aplicativo de funções, ele interromperá seu aplicativo.

[Leia mais sobre os requisitos de conta de armazenamento aqui.](./functions-create-function-app-portal.md#storage-account-requirements
) 

## <a name="virtual-network-triggers-non-http"></a>Gatilhos de rede virtual (não HTTP)

Atualmente, para poder usar gatilhos de função diferentes de HTTP de dentro de uma rede virtual, você deve executar seu aplicativo de funções em um plano do serviço de aplicativo ou em um Ambiente do Serviço de Aplicativo.

Para dar um exemplo, se você configurar Azure Cosmos DB para aceitar somente o tráfego de uma rede virtual, precisaria implantar seu aplicativo de funções em um plano do serviço de aplicativo com a integração de rede virtual com essa rede virtual para configurar Azure Cosmos DB gatilhos a partir desse recurso. Enquanto estiver na visualização, a configuração da integração VNET não permitirá que o plano Premium dispare dessa Azure Cosmos DB recurso.

Verifique [esta lista para todos os gatilhos não-http](./functions-triggers-bindings.md#supported-bindings) para verificar o que tem suporte.

## <a name="hybrid-connections"></a>Conexões híbridas

[Conexões híbridas](../service-bus-relay/relay-hybrid-connections-protocol.md) é um recurso da retransmissão do Azure que você pode usar para acessar recursos do aplicativo em outras redes. Ele fornece acesso de seu aplicativo para um ponto de extremidade do aplicativo. Você não pode usá-lo para acessar seu aplicativo. Conexões Híbridas está disponível para funções em execução em todos, exceto no plano de consumo.

Conforme usado em Azure Functions, cada conexão híbrida se correlaciona com uma única combinação de host e porta de TCP. Isso significa que o ponto de extremidade da conexão híbrida pode estar em qualquer sistema operacional e qualquer aplicativo, desde que você esteja acessando uma porta de escuta TCP. O recurso Conexões Híbridas não conhece ou se preocupa com o que é o protocolo de aplicativo ou o que você está acessando. Ele simplesmente fornece acesso à rede.

Para saber mais, consulte a [documentação do serviço de aplicativo para conexões híbridas](../app-service/app-service-hybrid-connections.md), que oferece suporte a funções por meio das mesmas etapas de configuração.

## <a name="outbound-ip-restrictions"></a>Restrições de IP de saída

As restrições de IP de saída estão disponíveis somente para funções implantadas em um Ambiente do Serviço de Aplicativo. Você pode configurar as restrições de saída para a rede virtual em que sua Ambiente do Serviço de Aplicativo está implantada.

Ao integrar um aplicativo de funções em um plano Premium ou plano do serviço de aplicativo com uma rede virtual, o aplicativo ainda é capaz de fazer chamadas de saída para a Internet.

## <a name="next-steps"></a>Próximos passos
Para saber mais sobre rede e Azure Functions: 

* [Siga o tutorial sobre como começar com a integração de rede virtual](./functions-create-vnet.md)
* [Leia as perguntas frequentes sobre rede de funções](./functions-networking-faq.md)
* [Saiba mais sobre a integração de rede virtual com o serviço de aplicativo/funções](../app-service/web-sites-integrate-with-vnet.md)
* [Saiba mais sobre as redes virtuais no Azure](../virtual-network/virtual-networks-overview.md)
* [Habilitar mais recursos de rede e controle com ambientes de serviço de aplicativo](../app-service/environment/intro.md)
* [Conectar-se a recursos locais individuais sem alterações de firewall usando Conexões Híbridas](../app-service/app-service-hybrid-connections.md)
