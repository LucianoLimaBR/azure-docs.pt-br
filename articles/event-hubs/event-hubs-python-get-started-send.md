---
title: Enviar e receber eventos usando Python-hubs de eventos do Azure
description: Este tutorial mostra como criar e executar scripts Python que enviam eventos ou recebem eventos dos hubs de eventos do Azure.
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 10/11/2019
ms.author: shvija
ms.openlocfilehash: 330a7f5dc325c707b5be7ce9f9b3242a1d4c9547
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428888"
---
# <a name="send-and-receive-events-with-event-hubs-using-python"></a>Enviar e receber eventos com hubs de eventos usando o Python

O Hubs de Eventos do Azure é uma plataforma de streaming de Big Data e um serviço de ingestão de eventos que pode receber e processar milhões de eventos por segundo. Os hubs de eventos podem processar e armazenar eventos, dados ou telemetria de software e dispositivos distribuídos. Os dados enviados para um Hub de Eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Para obter mais informações sobre os hubs de eventos, consulte [hubs de eventos do Azure](event-hubs-about.md) e [recursos e terminologia nos hubs de eventos do Azure](event-hubs-features.md).

Este guia de início rápido mostra como criar aplicativos Python que enviam eventos e recebem eventos de um hub de eventos. 

> [!NOTE]
> Em vez de trabalhar no início rápido, você pode baixar e executar os [aplicativos de exemplo](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) do github. Substitua as cadeias de caracteres `EventHubConnectionString` e `EventHubName` pelos valores do hub de eventos. 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisará dos seguintes pré-requisitos:

- Uma assinatura do Azure. Se você não tiver [uma conta gratuita](https://azure.microsoft.com/free/), crie uma antes de começar.
- Um namespace de hubs de eventos ativo e um hub de eventos, criados seguindo as instruções em [início rápido: criar um hub de eventos usando portal do Azure](event-hubs-create.md). Anote o namespace e os nomes do hub de eventos a serem usados mais adiante neste guia. 
- O nome da chave de acesso compartilhado e o valor da chave primária para seu namespace de hubs de eventos. Obtenha o nome e o valor da chave de acesso seguindo as instruções em [obter cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). O nome da chave de acesso padrão é **RootManageSharedAccessKey**. Copie o nome da chave e o valor da chave primária a ser usado posteriormente neste passo a passos. 
- Python 3,4 ou posterior, com `pip` instalado e atualizado.
- O pacote do Python para hubs de eventos. Para instalar o pacote, execute este comando em um prompt de comando que tenha o Python em seu caminho: 
  
  ```cmd
  pip install azure-eventhub
  ```
  
  > [!NOTE]
  > O código neste guia de início rápido usa a versão estável atual 1.3.1 do SDK dos hubs de eventos. Para obter o código de exemplo que usa a versão de visualização do SDK, consulte [https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples).

## <a name="send-events"></a>Enviar eventos

Para criar um aplicativo Python que envia eventos para um hub de eventos:

1. Abra seu editor de Python favorito, como [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um novo arquivo chamado *Send.py*. Esse script envia 100 eventos ao hub de eventos.
3. Cole o código a seguir em *Send.py*, substituindo os hubs de eventos \<namespace >, \<eventhub >, \<AccessKeyName > e valor de chave \<primary > com seus valores: 
   
   ```python
   import sys
   import logging
   import datetime
   import time
   import os
   
   from azure.eventhub import EventHubClient, Sender, EventData
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<namespace>.servicebus.windows.net/eventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   try:
       if not ADDRESS:
           raise ValueError("No EventHubs URL supplied.")
   
       # Create Event Hubs client
       client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
       sender = client.add_sender(partition="0")
       client.run()
       try:
           start_time = time.time()
           for i in range(100):
               print("Sending message: {}".format(i))
               message = "Message {}".format(i)
               sender.send(EventData(message))
       except:
           raise
       finally:
           end_time = time.time()
           client.stop()
           run_time = end_time - start_time
           logger.info("Runtime: {} seconds".format(run_time))
   
   except KeyboardInterrupt:
       pass
   ```
   
4. Salve o arquivo. 

Para executar o script, no diretório em que você salvou o *Send.py*, execute este comando:

```cmd
start python send.py
```

Parabéns! Agora você enviou mensagens para um hub de eventos.

## <a name="receive-events"></a>Receber eventos

Para criar um aplicativo Python que recebe eventos de um hub de eventos:

1. No seu editor de Python, crie um arquivo chamado *recv.py*.
2. Cole o código a seguir em *recv.py*, substituindo os hubs de eventos \<namespace >, \<eventhub >, \<AccessKeyName > e valor de chave \<primary > com seus valores: 
   
   ```python
   import os
   import sys
   import logging
   import time
   from azure.eventhub import EventHubClient, Receiver, Offset
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   
   CONSUMER_GROUP = "$default"
   OFFSET = Offset("-1")
   PARTITION = "0"
   
   total = 0
   last_sn = -1
   last_offset = "-1"
   client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
   try:
       receiver = client.add_receiver(
           CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
       client.run()
       start_time = time.time()
       for event_data in receiver.receive(timeout=100):
           print("Received: {}".format(event_data.body_as_str(encoding='UTF-8')))
           total += 1
   
       end_time = time.time()
       client.stop()
       run_time = end_time - start_time
       print("Received {} messages in {} seconds".format(total, run_time))
   
   except KeyboardInterrupt:
       pass
   finally:
       client.stop()
   ```
   
4. Salve o arquivo.

Para executar o script, no diretório em que você salvou o *recv.py*, execute este comando:

```cmd
start python recv.py
```

## <a name="next-steps"></a>Próximos passos
Para obter mais informações sobre os hubs de eventos, consulte os seguintes artigos:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Recursos e terminologia nos Hubs de Eventos do Azure](event-hubs-features.md)
- [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)

