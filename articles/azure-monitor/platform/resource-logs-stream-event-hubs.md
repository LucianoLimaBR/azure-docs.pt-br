---
title: Transmitir logs de recursos do Azure para um hub de eventos
description: Saiba como transmitir logs de recursos do Azure para um hub de eventos.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 1d7a533658b6c72caae9649d7e5a9c4fad117245
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262408"
---
# <a name="stream-azure-resource-logs-to-azure-event-hubs"></a>Transmitir logs de recursos do Azure para os hubs de eventos do Azure
[Os logs de recursos](resource-logs-overview.md) no Azure fornecem dados avançados e frequentes sobre a operação interna de um recurso do Azure. Este artigo descreve os logs de recursos de streaming para os hubs de eventos para enviar dados a sistemas externos, como SIEMs de terceiros e outras soluções do log Analytics.


## <a name="what-you-can-do-with-resource-logs-sent-to-an-event-hub"></a>O que você pode fazer com os logs de recursos enviados para um hub de eventos
Transmita logs de recursos no Azure para hubs de eventos para fornecer a seguinte funcionalidade:

* **Transmita logs para sistemas de registro em log e telemetria de terceiros** – transmita todos os seus logs de recursos para um único Hub de eventos para canalizar dados de log para uma ferramenta Siem ou log Analytics de terceiros.
* **Crie uma plataforma de registro em log e telemetria personalizada** – a natureza altamente escalonável de publicação-assinatura dos hubs de eventos permite ingerir com flexibilidade os logs de recursos em uma plataforma teletry personalizada. Consulte [projetando e dimensionando uma plataforma de telemetria de escala global nos hubs de eventos do Azure](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/) para obter detalhes.

* **Exibir a integridade do serviço transmitindo dados para Power bi** – use os hubs de eventos, Stream Analytics e Power bi para transformar os dados de diagnóstico em informações quase em tempo real nos serviços do Azure. Consulte [Stream Analytics e Power bi: Um painel de análise em tempo real para dados](../../stream-analytics/stream-analytics-power-bi-dashboard.md) de streaming para obter detalhes sobre essa solução.

    O código SQL a seguir é um exemplo de consulta do Stream Analytics que você pode usar para analisar todos os dados de log em uma tabela do Power BI:
    
    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

## <a name="prerequisites"></a>Pré-requisitos
Você precisará [criar um hub de eventos](../../event-hubs/event-hubs-create.md) se ainda não tiver um. Se você tiver transmitido anteriormente os logs de recursos para esse namespace de hubs de eventos, esse Hub de eventos será reutilizado.

A política de acesso compartilhado para o namespace define as permissões que o mecanismo de streaming tem. O streaming para hubs de eventos requer permissões de gerenciar, enviar e escutar. Você pode criar ou modificar políticas de acesso compartilhado no portal do Azure na guia Configurar para seu namespace de hubs de eventos.

Para atualizar a configuração de diagnóstico para incluir streaming, você deve ter a permissão ListKey nessa regra de autorização de hubs de eventos. O namespace dos Hubs de Eventos não precisa estar na mesma assinatura que emite os logs, desde que o usuário que define a configuração tenha acesso RBAC adequado a ambas as assinaturas e as duas assinaturas estejam no mesmo locatário do ADD.

## <a name="create-a-diagnostic-setting"></a>Criar uma configuração de diagnóstico
Os logs de recursos não são coletados por padrão. Envie-os para um hub de eventos e outros destinos criando uma configuração de diagnóstico para um recurso do Azure. Consulte [criar configuração de diagnóstico para coletar logs e métricas no Azure](diagnostic-settings.md) para obter detalhes.

## <a name="stream-data-from-compute-resources"></a>Transmitir dados de recursos de computação
O processo neste artigo destina-se a recursos que não são de computação, conforme descrito em [visão geral dos logs de recursos do Azure](diagnostic-settings.md).
Transmita logs de recursos de recursos de computação do Azure usando o agente de Diagnóstico do Azure do Windows. Consulte [streaming de dados de diagnóstico do Azure no Hot Path usando os hubs de eventos](diagnostics-extension-stream-event-hubs.md) para obter detalhes.


## <a name="consuming-log-data-from-event-hubs"></a>Consumindo dados de log dos hubs de eventos
Quando você consumir logs de recursos dos hubs de eventos, será o formato JSON com os elementos na tabela a seguir.

| Nome do elemento | DESCRIÇÃO |
| --- | --- |
| records |Uma matriz de todos os eventos de log nessa carga. |
| time |A hora na qual o evento ocorreu. |
| category |Categoria do log desse evento. |
| resourceId |ID de recurso do recurso que gerou esse evento. |
| operationName |Nome da operação. |
| level |Opcional. Indica o nível do evento de log. |
| properties |Propriedades do evento. Elas irão variar para cada serviço do Azure, conforme descrito em [](). |


Veja a seguir exemplos de dados de saída dos hubs de eventos:

```json
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```



## <a name="next-steps"></a>Próximas etapas

* [Transmita logs de Azure Active Directory com Azure monitor](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
* [Leia mais sobre os logs de recursos do Azure](resource-logs-overview.md).
* [Introdução aos hubs de eventos](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

