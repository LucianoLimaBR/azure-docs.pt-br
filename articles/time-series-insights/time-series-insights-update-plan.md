---
title: Planejar seu ambiente de Azure Time Series Insights Preview | Microsoft Docs
description: Planeje seu ambiente de visualização de Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/24/2019
ms.custom: seodec18
ms.openlocfilehash: b97db5fcebeea67cc593a4d2c1fd677a55ad8559
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72550183"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Planejar seu ambiente de Azure Time Series Insights Preview

Este artigo descreve as práticas recomendadas para planejar e começar rapidamente usando o Azure Time Series Insights Preview.

> [!NOTE]
> Para obter as práticas recomendadas para planejar uma instância de Time Series Insights de disponibilidade geral, consulte [planejar seu Azure Time Series insights ambiente de disponibilidade geral](time-series-insights-environment-planning.md).

## <a name="best-practices-for-planning-and-preparation"></a>Práticas recomendadas para planejamento e preparação

Para começar a usar o Time Series Insights, é melhor se você entender:

* O que você obtém ao [provisionar um ambiente de visualização de time Series insights](#the-preview-environment).
* Quais [são suas IDs de série temporal e propriedades de carimbo de data/hora](#configure-time-series-ids-and-timestamp-properties).
* Qual é o novo [modelo de série temporal](#understand-the-time-series-model)e como criar os seus próprios.
* Como [enviar eventos com eficiência no JSON](#shape-your-events).
* Time Series Insights [Opções de recuperação de desastres de negócios](#business-disaster-recovery).

Azure Time Series Insights emprega um modelo de negócios pago conforme o uso. Para obter mais informações sobre cobranças e capacidade, consulte [preços de time Series insights](https://azure.microsoft.com/pricing/details/time-series-insights/).

## <a name="the-preview-environment"></a>O ambiente de visualização

Ao provisionar um ambiente de visualização de Time Series Insights, você cria dois recursos do Azure:

* Um ambiente de visualização Azure Time Series Insights
* Uma conta v1 de uso geral do armazenamento do Azure

Para começar, você precisa de três itens adicionais:

* Um [modelo de série temporal](./time-series-insights-update-tsm.md)
* Uma [origem de evento conectada a Time Series insights](./time-series-insights-how-to-add-an-event-source-iothub.md)
* [Eventos que fluem para a origem do evento](./time-series-insights-send-events.md) que são mapeados para o modelo e estão em um formato JSON válido

## <a name="configure-time-series-ids-and-timestamp-properties"></a>Configurar IDs de série temporal e propriedades de carimbo de data/hora

Para criar um novo ambiente de Time Series Insights, selecione uma ID de série temporal. Fazer isso age como uma partição lógica para seus dados. Conforme observado, certifique-se de que as IDs de série temporal estão prontas.

> [!IMPORTANT]
> As IDs de série temporal *não podem ser alteradas posteriormente*. Verifique cada uma antes da seleção final e do primeiro uso.

Você pode selecionar até três chaves para diferenciar exclusivamente seus recursos. Para obter mais informações, leia [as práticas recomendadas para escolher uma ID de série temporal e o](./time-series-insights-update-how-to-id.md) [armazenamento e a entrada](./time-series-insights-update-storage-ingress.md).

A propriedade Timestamp também é importante. Você pode designar essa propriedade ao adicionar fontes de evento. Cada fonte de evento tem uma propriedade de carimbo de data/hora opcional que é usada para rastrear fontes de eventos ao longo do tempo. Os valores TIMESTAMP diferenciam maiúsculas de minúsculas e devem ser formatados para a especificação individual de cada origem do evento.

> [!TIP]
> Verifique os requisitos de formatação e análise para suas origens de eventos.

Quando deixado em branco, o tempo de enfileiramento de eventos de uma origem de evento é usado como o carimbo de data/hora do evento. Se você enviar dados históricos ou eventos em lote, a personalização da propriedade Timestamp será mais útil do que o tempo de enfileiramento de eventos padrão. Para obter mais informações, leia sobre como [adicionar fontes de eventos no Hub IOT do Azure](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="understand-the-time-series-model"></a>Entender o modelo de série temporal

Agora você pode configurar o modelo de série temporal do ambiente de Time Series Insights. O novo modelo facilita a localização e a análise de dados de IoT. Ele permite a organização, a manutenção e o enriquecimento de dados de série temporal e ajuda a preparar conjuntos de dados prontos para o consumidor. O modelo usa IDs de série temporal, que mapeiam para uma instância que associa o recurso exclusivo a variáveis, conhecidas como tipos e hierarquias. Leia sobre o novo [modelo de série temporal](./time-series-insights-update-tsm.md).

O modelo é dinâmico, portanto, pode ser criado a qualquer momento. Para começar rapidamente, crie e carregue-o antes de enviar dados por push para Time Series Insights. Para criar seu modelo, consulte [usar o modelo de série temporal](./time-series-insights-update-how-to-tsm.md).

Para muitos clientes, o modelo de série temporal é mapeado para um modelo de ativo ou sistema ERP existente já em vigor. Se você não tiver um modelo existente, uma experiência de usuário predefinida será [fornecida](https://github.com/Microsoft/tsiclient) para entrar em funcionamento rapidamente. Para prever como um modelo pode ajudá-lo, exiba o [ambiente de demonstração de exemplo](https://insights.timeseries.azure.com/preview/demo).

## <a name="shape-your-events"></a>Formate seus eventos

Você pode verificar a maneira como envia eventos para Time Series Insights. O ideal é que seus eventos sejam desnormalizados de forma eficiente e eficaz.

Uma boa regra geral:

* Armazene metadados no seu modelo de série temporal.
* O modo de série temporal, os campos de instância e os eventos incluem apenas as informações necessárias, como uma ID de série temporal ou um carimbo de data/hora.

Para obter mais informações, consulte [eventos de forma](./time-series-insights-send-events.md#supported-json-shapes).

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>Próximos passos

- Examine o [Azure Advisor](../advisor/advisor-overview.md) para planejar suas opções de configuração de recuperação de negócios.

- Leia mais sobre [armazenamento e entrada](./time-series-insights-update-storage-ingress.md) na visualização de time Series insights.

- Saiba mais sobre a [modelagem de dados](./time-series-insights-update-tsm.md) na visualização de time Series insights.