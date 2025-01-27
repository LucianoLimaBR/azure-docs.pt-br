---
title: Monitorar aplicativos Python com o Azure Monitor (versão prévia) | Microsoft Docs
description: Fornece instruções para conectar o Python OpenCensus com o Azure Monitor
services: application-insights
keywords: ''
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.service: application-insights
ms.topic: conceptual
ms.reviewer: mbullwin
manager: carmonm
ms.openlocfilehash: 4cd3d048ead8b9e6ff59a17d1a8269ecdec5a11c
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72750399"
---
# <a name="set-up-azure-monitor-for-your-python-application-preview"></a>Configurar Azure Monitor para seu aplicativo Python (versão prévia)

O Azure Monitor dá suporte ao rastreamento distribuído, à coleta de métrica e ao registro em log de aplicativos Python por meio da integração com o [OpenCensus](https://opencensus.io). Este artigo explicará o processo de configuração do OpenCensus para Python e o envio dos dados de monitoramento para Azure Monitor.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Instalação do Python. Este artigo usa o [Python 3.7.0](https://www.python.org/downloads/), embora as versões anteriores provavelmente funcionem com pequenas alterações.



## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>Criar um recurso de Application Insights no Azure Monitor

Primeiro, você precisa criar um recurso de Application Insights no Azure Monitor, que irá gerar uma chave de instrumentação (iKey). Em seguida, o iKey é usado para configurar o SDK do OpenCensus para enviar dados de telemetria para Azure Monitor.

1. Selecione **Criar um recurso** > **Ferramentas de desenvolvedor** > **Application Insights**.

   ![Adicionando um recurso de Application Insights](./media/opencensus-python/0001-create-resource.png)

1. Uma caixa de configuração é exibida. Use a tabela a seguir para preencher os campos de entrada.

   | Configuração        | Value           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor global exclusivo | Nome que identifica o aplicativo que você está monitorando |
   | **Grupo de recursos**     | myResourceGroup      | Nome do novo grupo de recursos para hospedar Application Insights dados |
   | **Localidade** | Leste dos EUA | Um local perto de você ou próximo de onde seu aplicativo está hospedado |

1. Clique em **Criar**.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Instrumento com SDK OpenCensus Python para Azure Monitor

Instale o OpenCensus Azure Monitor exportadores:

```console
python -m pip install opencensus-ext-azure
```

> [!NOTE]
> O comando `python -m pip install opencensus-ext-azure` pressupõe que você tenha uma variável de ambiente `PATH` definida para a instalação do Python. Se você ainda não configurou essa variável, precisará fornecer o caminho completo do diretório onde o executável do Python está localizado. O resultado é um comando como este: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

O SDK usa três Azure Monitor exportadores para enviar tipos diferentes de telemetria para Azure Monitor: rastreamento, métricas e logs. Para obter mais informações sobre esses tipos de telemetria, consulte [visão geral da plataforma de dados](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform). Use as instruções a seguir para enviar esses tipos de telemetria por meio dos três exportadores.

### <a name="trace"></a>Rastreamento

1. Primeiro, vamos gerar alguns dados de rastreamento localmente. Em Python IDLE ou seu editor de preferência, insira o código a seguir.

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2. Executar o código solicitará repetidamente que você insira um valor. Com cada entrada, o valor será impresso no Shell e o módulo OpenCensus Python gerará uma parte correspondente do `SpanData`. O projeto OpenCensus define um [rastreamento como uma árvore de Spans](https://opencensus.io/core-concepts/tracing/).
    
    ```
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

3. Embora a inserção de valores seja útil para fins de demonstração, por fim, desejamos emitir o `SpanData` para Azure Monitor. Modifique seu código da etapa anterior com base no exemplo de código a seguir:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        ),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

4. Agora, ao executar o script Python, você ainda deve ser solicitado a inserir valores, mas apenas o valor está sendo impresso no Shell. O `SpanData` criado será enviado para Azure Monitor. Você pode encontrar os dados de span emitidos em `dependencies`.

### <a name="metrics"></a>Métricas

1. Primeiro, vamos gerar alguns dados de métrica local. Vamos criar uma métrica simples para acompanhar o número de vezes que o usuário pressiona ENTER.

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
2. A execução do código solicitará repetidamente que você pressione Enter. Uma métrica é criada para acompanhar o número de vezes que Enter é pressionado. Com cada entrada, o valor será incrementado e as informações de métrica serão exibidas no console. As informações incluem o valor atual e o carimbo de data/hora atual quando a métrica foi atualizada.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Embora a inserção de valores seja útil para fins de demonstração, por fim, desejamos emitir os dados de métrica para Azure Monitor. Modifique seu código da etapa anterior com base no exemplo de código a seguir:

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

4. O exportador enviará dados de métrica para Azure Monitor em um intervalo fixo. O padrão é a cada 15 segundos. Estamos acompanhando uma única métrica, portanto, esses dados de métrica, com qualquer valor e carimbo de data/hora que ele contém, serão enviados a cada intervalo. Você pode encontrar os dados em `customMetrics`.

### <a name="logs"></a>Logs

1. Primeiro, vamos gerar alguns dados de log locais.

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2.  O código solicitará continuamente um valor a ser inserido. Uma entrada de log é emitida para cada valor inserido.

    ```
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

3. Embora a inserção de valores seja útil para fins de demonstração, por fim, desejamos emitir os dados de métrica para Azure Monitor. Modifique seu código da etapa anterior com base no exemplo de código a seguir:

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

4. O exportador enviará dados de log para Azure Monitor. Você pode encontrar os dados em `traces`.

## <a name="start-monitoring-in-the-azure-portal"></a>Iniciar o monitoramento no Portal do Azure

1. Agora você pode reabrir o painel de **visão geral** Application Insights no portal do Azure para exibir detalhes sobre o aplicativo em execução no momento. Selecione **Live Metrics Stream**.

   ![Captura de tela do painel Visão geral com "Live Metrics Stream" selecionado em uma caixa vermelha](./media/opencensus-python/0005-overview-live-metrics-stream.png)

2. Volte para o painel **visão geral** . Selecione **mapa do aplicativo** para obter um layout visual das relações de dependência e o tempo de chamada entre os componentes do aplicativo.

   ![Captura de tela de um mapa de aplicativo básico](./media/opencensus-python/0007-application-map.png)

   Como estamos rastreando apenas uma chamada de método, nosso mapa de aplicativos não é interessante. Mas um mapa de aplicativos pode ser dimensionado para visualizar muito mais aplicativos distribuídos:

   ![Mapa do aplicativo](media/opencensus-python/application-map.png)

3. Selecione **investigar desempenho** para analisar o desempenho em detalhes e determinar a causa raiz do desempenho lento.

   ![Captura de tela de detalhes de desempenho](./media/opencensus-python/0008-performance.png)

4. Para abrir a experiência de ponta a ponta para os detalhes da transação, selecione **exemplos**e, em seguida, selecione qualquer um dos exemplos que aparecem no painel direito. 

   Embora nosso aplicativo de exemplo mostre apenas um único evento, um aplicativo mais complexo permitiria explorar a transação de ponta a ponta até o nível da pilha de chamadas de um evento individual.

   ![Captura de tela da interface de transação de ponta a ponta](./media/opencensus-python/0009-end-to-end-transaction.png)

## <a name="view-your-data-with-queries"></a>Exibir seus dados com consultas

Você pode exibir os dados de telemetria que foram enviados do seu aplicativo por meio da guia **logs (análise)** .

![Captura de tela do painel Visão geral com "logs (análise)" selecionado em uma caixa vermelha](./media/opencensus-python/0010-logs-query.png)

Na lista em **ativo**:

- Para a telemetria enviada com o exportador de rastreamento de Azure Monitor, as solicitações de entrada aparecem em `requests`. As solicitações de saída ou em andamento aparecem em `dependencies`.
- Para a telemetria enviada com a Azure Monitor o exportador de métricas, as métricas enviadas aparecem em `customMetrics`.
- Para telemetria enviada com o exportador de logs de Azure Monitor, os logs aparecem em `traces`. As exceções aparecem em `exceptions`.

Para obter informações mais detalhadas sobre como usar consultas e logs, consulte [logs em Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs).

## <a name="learn-more-about-opencensus-for-python"></a>Saiba mais sobre o OpenCensus para Python

* [OpenCensus Python no GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Personalização](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Integração do Flask](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-flask)
* [Integração do Django](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-django)
* [Integração do MySQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-mysql)
* [PostgreSQL](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-postgresql)

## <a name="next-steps"></a>Próximos passos

* [Mapa do aplicativo](./../../azure-monitor/app/app-map.md)
* [Monitoramento de desempenho de ponta a ponta](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Alertas

* [Testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md): criar testes para verificar se seu site está visível na web.
* [Inteligente diagnóstico](../../azure-monitor/app/proactive-diagnostics.md): esses testes são executados automaticamente, portanto você não precisa fazer nada para configurá-los. Eles informam se o aplicativo tem uma taxa incomum de solicitações com falha.
* [Alertas de métrica](../../azure-monitor/app/alerts.md): Defina alertas para avisá-lo se uma métrica ultrapassar um limite. Você pode defini-los em métricas personalizadas que você codifica em seu aplicativo.
