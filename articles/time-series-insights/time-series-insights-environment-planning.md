---
title: Planejar a escala do ambiente do Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como seguir as práticas recomendadas ao planejar um ambiente de Azure Time Series Insights. As áreas cobertas incluem capacidade de armazenamento, retenção de dados, capacidade de entrada, monitoramento e continuidade dos negócios e recuperação de desastres (BCDR).
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 659a6357736817f4a590b97e585230ec8c2b7dae
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332915"
---
# <a name="plan-your-azure-time-series-insights-ga-environment"></a>Planeje seu ambiente Azure Time Series Insights GA

Este artigo descreve como planejar seu Azure Time Series Insights ambiente de disponibilidade geral (GA) com base em sua taxa de entrada esperada e seus requisitos de retenção de dados.

## <a name="video"></a>Vídeo

**Assista a este vídeo para saber mais sobre a retenção de dados em Azure Time Series insights e como planejar para ele**:<br /><br />

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

## <a name="best-practices"></a>Práticas recomendadas

Para começar a usar o Azure Time Series Insights, é melhor se você souber a quantidade de dados que espera enviar por minuto e por quanto tempo precisa para armazenar seus dados.  

Para saber mais sobre capacidade e retenção para ambas as SKUs do Time Series Insights, confira [Preços do Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

Para planejar melhor seu ambiente de Time Series Insights para o sucesso a longo prazo, considere os seguintes atributos:

- [Capacidade de armazenamento](#storage-capacity)
- [Período de retenção de dados](#data-retention)
- [Capacidade de entrada](#ingress-capacity)
- [Moldando seus eventos](#shape-your-events)
- [Garantindo que você tenha dados de referência em vigor](#ensure-that-you-have-reference-data)

## <a name="storage-capacity"></a>Capacidade de armazenamento

Por padrão, Time Series Insights retém dados com base na quantidade de armazenamento que você provisiona (unidade &#215; de armazenamento por unidade) e entrada.

## <a name="data-retention"></a>Retenção de dados

Você pode alterar a configuração de **tempo de retenção de dados** em seu ambiente de Azure Time Series insights. Você pode habilitar até 400 dias de retenção. 

Azure Time Series Insights tem dois modos:

* Um modo otimiza para os dados mais atualizados. Ele impõe uma política para **limpar dados antigos** , deixando dados recentes disponíveis com a instância. Por padrão, esse modo é ativado. 
* O outro otimiza os dados para permanecerem abaixo dos limites de retenção configurados. **Pausar a entrada** impede que novos dados sejam inseridos quando seu selecionado como o **limite de armazenamento excedeu o comportamento**. 

Você pode ajustar a retenção e alternar entre os dois modos na página de configuração do ambiente no portal do Azure.

> [!IMPORTANT]
> Você pode configurar um máximo de 400 dias de retenção de dados em seu ambiente Azure Time Series Insights GA.

### <a name="configure-data-retention"></a>Configurar retenção de dados

1. No [portal do Azure](https://portal.azure.com), selecione o ambiente do Time Series Insights.

1. No painel **Time Series insights ambiente** , em **configurações**, selecione **Configurar**.

1. Na caixa **tempo de retenção de dados (em dias)** , insira um valor entre 1 e 400.

   [retenção de @no__t 1Configure](media/environment-mitigate-latency/configure-retention.png)](media/environment-mitigate-latency/configure-retention.png#lightbox)

> [!TIP]
> Para saber mais sobre como implementar uma política de retenção de dados apropriada, confira [como configurar a retenção](./time-series-insights-how-to-configure-retention.md).

## <a name="ingress-capacity"></a>Capacidade de entrada

A segunda área para se concentrar ao planejar seu ambiente de Time Series Insights é a *capacidade de entrada*. A capacidade de entrada é um derivativo da alocação por minuto.

De uma perspectiva de limitação, um pacote de dados de entrada que tem um tamanho de pacote de 32 KB é tratado como 32 eventos, cada um com tamanho de 1 KB. O tamanho máximo de evento permitido é de 32 KB. Pacotes de dados maiores que 32 KB são truncados.

A tabela a seguir resume a capacidade de entrada por unidade para cada SKU Time Series Insights:

|SKU  |Contagem de eventos por mês  |Tamanho do evento por mês  |Contagem de eventos por minuto  |Tamanho do evento por minuto  |
|---------|---------|---------|---------|---------|
|S1     |   30 milhões     |  30 GB     |  720    |  720 KB   |
|S2     |   300 milhões    |   300 GB   | 7\.200   | 7\.200 KB  |

Você pode aumentar a capacidade de uma SKU S1 ou S2 para até 10 unidades em um único ambiente. Não é possível migrar de um ambiente S1 para um S2. Não é possível migrar de um ambiente S2 para um S1.

Para a capacidade de entrada, primeiro determine a entrada total que você precisa por mês. Em seguida, determine quais são suas necessidades por minuto. 

A limitação e a latência desempenham uma função na capacidade por minuto. Se você tiver um pico em sua entrada de dados que dura menos de 24 horas, Time Series Insights poderá "se acumular" em uma taxa de entrada de duas vezes as tarifas listadas na tabela anterior.

Por exemplo, se você tiver uma única SKU S1, você entrará dados em uma taxa de 720 eventos por minuto e os picos de taxa de dados por menos de uma hora a uma taxa de 1.440 eventos ou menos, não haverá latência perceptível em seu ambiente. No entanto, se você exceder 1.440 eventos por minuto por mais de uma hora, provavelmente haverá latência nos dados visualizados e disponíveis para consulta em seu ambiente.

Talvez você não saiba com antecedência a quantidade de dados que espera enviar por push. Nesse caso, você pode encontrar a telemetria de dados para o [Hub IOT do Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) e [hubs de eventos do Azure](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/05/25/using-the-azure-rest-apis-to-retrieve-event-hub-metrics/) em sua assinatura portal do Azure. A telemetria pode ajudá-lo a determinar como provisionar seu ambiente. Use o painel **métricas** na portal do Azure da respectiva origem do evento para exibir sua telemetria. Se você compreender suas métricas de origem de eventos, poderá planejar e provisionar com mais eficiência o ambiente do Time Series Insights.

### <a name="calculate-ingress-requirements"></a>Calcular os requisitos de entrada

Para calcular seus requisitos de entrada:

- Verifique se a sua capacidade de entrada está acima da taxa média por minuto e se o ambiente é grande o suficiente para lidar com a sua entrada prevista equivalente a duas vezes sua capacidade por menos de uma hora.

- Se ocorrerem picos de entrada que duram mais de uma hora, use a taxa de pico como média. Provisione um ambiente com a capacidade de lidar com a taxa de pico.

### <a name="mitigate-throttling-and-latency"></a>Reduzir a latência e a limitação

Para obter informações sobre como evitar a limitação e a latência, confira [Reduzir a latência e a limitação](time-series-insights-environment-mitigate-latency.md).

## <a name="shape-your-events"></a>Formatar os eventos

É importante garantir que a maneira como você envia eventos para Time Series Insights dá suporte ao tamanho do ambiente que você está Provisionando. (Por outro lado, você pode mapear o tamanho do ambiente para quantos eventos Time Series Insights leituras e o tamanho de cada evento.) Também é importante pensar sobre os atributos que você pode querer usar para dividir e filtrar por quando você consulta seus dados.

> [!TIP]
> Examine a documentação do JSON Shaping em [enviando eventos](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-send-events).

## <a name="ensure-that-you-have-reference-data"></a>Verifique se você tem dados de referência

Um *conjunto* de uma referência é uma coleção de itens que aumentam os eventos da origem do evento. O Time Series Insights o mecanismo de entrada une cada evento de sua origem de evento com a linha de dados correspondente no DataSet de referência. O evento aumentado fica disponível para consulta. A junção é baseada nas colunas de **chave primária** que são definidas em seu conjunto de seus conjuntos de referência.

> [!NOTE]
> Os dados de referência não são associados retroativamente. Somente os dados de entrada atuais e futuros são correspondidos e associados ao DataSet de referência depois de configurados e carregados. Se você planeja enviar uma grande quantidade de dados históricos para Time Series Insights e não carregar primeiro ou criar dados de referência no Time Series Insights, talvez seja necessário refazer seu trabalho (dica: não é divertido).  

Para saber mais sobre como criar, carregar e gerenciar seus dados de referência em Time Series Insights, consulte nossa [documentação de conjunto](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-add-reference-data-set)de dado de referência.

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Próximos passos

- Comece criando [um novo ambiente de time Series insights no portal do Azure](time-series-insights-get-started.md).

- Saiba como [Adicionar uma fonte de eventos de hubs de eventos](time-series-insights-how-to-add-an-event-source-eventhub.md) a Time Series insights.

- Leia sobre como [Configurar uma origem de evento do Hub IOT](time-series-insights-how-to-add-an-event-source-iothub.md).
