---
title: Como adicionar uma origem do evento do Hub IoT ao Azure Time Series Insights | Microsoft Docs
description: Este artigo descreve como adicionar uma origem do evento conectada a um Hub IoT ao ambiente do Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/09/2019
ms.custom: seodec18
ms.openlocfilehash: eaac2e3e2fc489b364729711da151c28cde86d6c
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274728"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>Adicionar uma origem do evento do hub IoT ao seu ambiente do Time Series Insights

Este artigo descreve como usar o portal do Azure para adicionar uma origem do evento que lê dados de um Hub IoT para o ambiente do Azure Time Series Insights.

> [!NOTE]
> As instruções neste artigo se aplicam tanto aos ambientes da Versão Prévia do Azure Time Series Insights quanto ao Time Series Insights GA.

## <a name="prerequisites"></a>Pré-requisitos

* Criar um [ambiente do Azure Time Series Insights](time-series-insights-update-create-environment.md).
* Criar um [Hub IoT usando o portal do Azure](../iot-hub/iot-hub-create-through-portal.md).
* O hub IoT precisa ter eventos de mensagem ativos enviados.
* Crie um grupo de consumidores dedicado no hub IoT para o ambiente do Time Series Insights de onde você consumirá. Cada origem do evento do Time Series Insights deve ter seu próprio grupo de consumidores dedicado que não esteja compartilhado com nenhum outro consumidor. Se vários leitores consumirem eventos do mesmo grupo de consumidores, provavelmente, todos os leitores verão falhas. Para obter detalhes, confira o [Guia do desenvolvedor do Hub IoT do Azure](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Adicionar um grupo de consumidores ao hub IoT

Aplicativos usam grupos de consumidores para efetuar pull dos dados de Hub IoT do Azure. Para ler de forma confiável dados de seu hub IoT, forneça um grupo de consumidores dedicado que é usado somente por esse ambiente de Time Series Insights.

Para adicionar um novo grupo de consumidores ao seu hub IoT:

1. Na [portal do Azure](https://portal.azure.com), localize e abra o Hub IOT.

1. Em **configurações**, selecione **pontos de extremidade internos**e, em seguida, selecione o ponto final de **eventos** .

   [![On a página de pontos de extremidade de compilação, selecione o botão eventos](media/time-series-insights-how-to-add-an-event-source-iothub/1-iot-hub.png)](media/time-series-insights-how-to-add-an-event-source-iothub/1-iot-hub.png#lightbox)

1. Em **Grupos de consumidores**, insira um nome exclusivo para o grupo de consumidores. Use esse mesmo nome em seu ambiente do Time Series Insights ao criar uma nova origem do evento.

1. Clique em **Salvar**.

## <a name="add-a-new-event-source"></a>Adicionar uma nova origem do evento

1. Entre no [Portal do Azure](https://portal.azure.com).

1. No menu do lado esquerdo, selecione **Todos os recursos**. Selecione o seu ambiente de Análise de Séries Temporais.

1. Em **Topologia do Ambiente**, selecione **Origens dos Eventos** e, em seguida, selecione **Adicionar**.

   [Fontes de evento ![Select e, em seguida, selecione o botão Adicionar](media/time-series-insights-how-to-add-an-event-source-iothub/2-add-event-source.png)](media/time-series-insights-how-to-add-an-event-source-iothub/2-add-event-source.png#lightbox)

1. No painel **Nova origem do evento**, em **Nome da origem do evento**, insira um nome que seja exclusivo para esse ambiente do Time Series Insights, como fluxo-de-evento. Por exemplo, digite **fluxo-de-evento**.

1. Para **Origem**, selecione **Hub IoT**.

1. Selecione um valor para **Importar opção**:

   * Se você já tiver um Hub IoT em uma de suas assinaturas, selecione **Usar o Hub IoT de assinaturas disponíveis**. Essa opção é a abordagem mais fácil.
   
     [opções de @no__t 1Select no novo painel de origem do evento](media/time-series-insights-how-to-add-an-event-source-iothub/3-select-an-import-option.png)](media/time-series-insights-how-to-add-an-event-source-iothub/3-select-an-import-option.png#lightbox)

    * A tabela a seguir descreve as propriedades necessárias para a opção **Usar Hub IoT de assinaturas disponíveis**:

       [painel de origem do evento ![New-Propriedades a serem definidas na opção usar o Hub IoT de assinaturas disponíveis](media/time-series-insights-how-to-add-an-event-source-iothub/4-create-button.png)](media/time-series-insights-how-to-add-an-event-source-iothub/4-create-button.png#lightbox)

       | Propriedade | DESCRIÇÃO |
       | --- | --- |
       | Assinatura | A assinatura à qual o Hub IOT desejado pertence. |
       | Nome do Hub IoT | O nome do Hub IOT selecionado. |
       | Nome da política do Hub IoT | Selecione a política de acesso compartilhado. Você pode encontrar a política de acesso compartilhado na guia de configurações do Hub IoT. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. A política de acesso compartilhado para a origem do evento *deve* ter permissões de **conexão de serviço**. |
       | Chave de política do Hub IoT | A chave é preenchida previamente. |

    * Se o hub IoT for externo às suas assinaturas ou se você quiser escolher opções avançadas, selecione **Fornecer configurações do Hub IoT manualmente**.

      A tabela a seguir descreve as propriedades necessárias para **Fornecer configurações do Hub IoT manualmente**:

       | Propriedade | DESCRIÇÃO |
       | --- | --- |
       | ID da assinatura | A assinatura à qual o Hub IOT desejado pertence. |
       | Grupo de recursos | O nome do grupo de recursos no qual o hub IoT foi criado. |
       | Nome do Hub IoT | O nome de seu hub IoT. Quando criou o hub IoT, você inseriu um nome para ele. |
       | Nome da política do Hub IoT | A política de acesso compartilhado. Você pode criar a política de acesso compartilhado na guia de configurações do hub IoT. Cada política de acesso compartilhado tem um nome, as permissões definidas por você e as chaves de acesso. A política de acesso compartilhado para a origem do evento *deve* ter permissões de **conexão de serviço**. |
       | Chave de política do Hub IoT | A chave de acesso compartilhado usada para autenticar o acesso ao namespace do Barramento de Serviço do Azure. Insira aqui a chave primária ou secundária. |

    * Ambas as opções compartilham as seguintes opções de configuração:

       | Propriedade | DESCRIÇÃO |
       | --- | --- |
       | Grupo de consumidores do Hub IoT | O grupo de consumidores que lê eventos do hub IoT. Recomendamos que você use um grupo de consumidores dedicado para a origem do evento. |
       | Formato de serialização do evento | No momento, JSON é único o formato de serialização disponível. As mensagens de evento deverão estar no formato a seguir ou nenhum dado poderá ser lido. |
       | Nome da propriedade timestamp | Para determinar esse valor, você precisa entender o formato da mensagem dos dados de mensagem enviados para o hub IoT. Esse valor é o **nome** da propriedade de evento específica nos dados da mensagem que você deseja usar como o carimbo de data/hora do evento. O valor diferencia maiúsculas de minúsculas. Se deixado em branco, o **tempo de enfileiramento do evento** na origem do evento será usado como o carimbo de data/hora do evento. |


1. Adicione o nome do grupo de consumidores do Time Series Insights dedicado que você adicionou ao seu hub IoT.

1. Selecione **Criar**.

1. Depois que a origem do evento é criada, o Time Series Insights inicia automaticamente os dados de streaming para o seu ambiente.

## <a name="next-steps"></a>Próximas etapas

* [Definir as políticas de acesso de dados](time-series-insights-data-access.md) para proteger os dados.

* [Enviar eventos](time-series-insights-send-events.md) para a origem do evento.

* Acesse seu ambiente no [explorer do Time Series Insights](https://insights.timeseries.azure.com).
