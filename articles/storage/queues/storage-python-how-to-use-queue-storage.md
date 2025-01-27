---
title: Como usar o armazenamento de filas do Azure do Python – armazenamento do Azure
description: Saiba como usar o serviço Fila do Azure do Python para criar e excluir filas, e inserir, obter e excluir mensagens.
author: mhopkins-msft
ms.service: storage
ms.author: mhopkins
ms.date: 09/17/2019
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.custom: seo-javascript-october2019
ms.openlocfilehash: 242ba7dbe4bfcc003899e95e76dc57d809dbc95a
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72427966"
---
# <a name="how-to-use-azure-queue-storage-from-python"></a>Como usar o armazenamento de filas do Azure do Python

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

Este artigo demonstra cenários comuns usando o serviço de armazenamento de filas do Azure. Os cenários abordados incluem inserção, inspeção, obtenção e exclusão de mensagens da fila e criação e exclusão de filas.

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Visão Geral

Os exemplos neste artigo são escritos em Python e usam o [SDK do armazenamento do Microsoft Azure para Python]. Para obter mais informações sobre filas, consulte a seção [Próximas etapas](#next-steps) .

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Baixe e instale o SDK do armazenamento do Azure para Python

O [SDK do armazenamento do Azure para Python](https://github.com/azure/azure-storage-python) requer o python versão 2,7, 3,3 ou posterior.
 
### <a name="install-via-pypi"></a>Instalar via PyPi

Para instalar por meio do índice de pacote do Python (PyPI), digite:

```bash
pip install azure-storage-queue
```

> [!NOTE]
> Se você estiver atualizando do SDK do armazenamento do Azure para o Python versão 0,36 ou anterior, desinstale o SDK mais antigo usando `pip uninstall azure-storage` antes de instalar o pacote mais recente.

Para métodos de instalação alternativos, consulte [SDK do armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Exibir o aplicativo de exemplo

Para exibir e executar um aplicativo de exemplo que mostra como usar o Python com filas do Azure, consulte [armazenamento do Azure: introdução com filas do Azure em Python](https://github.com/Azure-Samples/storage-queue-python-getting-started). 

Para executar o aplicativo de exemplo, verifique se você instalou os pacotes `azure-storage-queue` e `azure-storage-common`.

## <a name="create-a-queue"></a>Criar uma fila

O objeto [QueueService](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice) permite que você trabalhe com filas. O código a seguir cria um objeto `QueueService`. Adicione o seguinte próximo à parte superior de qualquer arquivo Python no qual você deseja acessar o armazenamento do Azure programaticamente:

```python
from azure.storage.queue import QueueService
```

O código a seguir cria um objeto `QueueService` usando o nome da conta de armazenamento e a chave de conta. Substitua *myaccount* e *MyKey* pelo nome e pela chave da sua conta.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="insert-a-message-into-a-queue"></a>Inserir uma mensagem em uma fila

Para inserir uma mensagem em uma fila, use o método [put_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) para criar uma nova mensagem e adicioná-la à fila.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

As mensagens da fila do Azure são armazenadas como texto. Se você quiser armazenar dados binários, configure as funções codificação Base64 e decodificação no objeto serviço fila antes de colocar uma mensagem na fila.

```python
# setup queue Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

## <a name="peek-at-the-next-message"></a>Espiar a próxima mensagem

Você pode inspecionar a mensagem na frente de uma fila sem removê-la da fila chamando o método [peek_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#peek-messages-queue-name--num-messages-none--timeout-none-) . Por padrão, `peek_messages` exibe uma única mensagem.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="dequeue-messages"></a>Remover mensagens da fila

Seu código remove uma mensagem de uma fila em duas etapas. Ao chamar [get_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-), você obtém a próxima mensagem em uma fila por padrão. Uma mensagem retornada de `get_messages` torna-se invisível para qualquer outra mensagem de leitura de código dessa fila. Por padrão, essa mensagem permanece invisível por 30 segundos. Para concluir a remoção da mensagem da fila, chame também [delete_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-message-queue-name--message-id--pop-receipt--timeout-none-). Este processo de duas etapas de remover uma mensagem garante que quando o código não processa uma mensagem devido à falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente. Seu código chama `delete_message` logo após a mensagem ser processada.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Há duas maneiras de personalizar a recuperação da mensagem de uma fila. Primeiro, você pode obter um lote de mensagens (até 32). Em segundo lugar, você pode definir um tempo limite de invisibilidade mais longo ou mais curto, permitindo que seu código tenha mais ou menos tempo para processar totalmente cada mensagem. O exemplo de código a seguir usa o método `get_messages` para obter 16 mensagens em uma chamada. Em seguida, ele processa cada mensagem usando um loop for. Ele também define o tempo limite de invisibilidade para cinco minutos para cada mensagem.

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="change-the-contents-of-a-queued-message"></a>Alterar o conteúdo de uma mensagem na fila

Você pode alterar o conteúdo de uma mensagem in-loco na fila. Se a mensagem representar uma tarefa de trabalho, você poderá usar esse recurso para atualizar o status da tarefa de trabalho. O código a seguir usa o método [update_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) para atualizar uma mensagem. O tempo limite de visibilidade é definido como 0, o que significa que a mensagem aparece imediatamente e o conteúdo é atualizado.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="get-the-queue-length"></a>Obter o tamanho da fila

Você pode obter uma estimativa do número de mensagens em uma fila. O método [get_queue_metadata](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-queue-metadata-queue-name--timeout-none-) solicita que o serviço fila retorne metadados sobre a fila e o `approximate_message_count`. O resultado é aproximado apenas porque as mensagens podem ser adicionadas ou removidas depois que o serviço fila responde à sua solicitação.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="delete-a-queue"></a>Excluir uma fila

Para excluir uma fila e todas as mensagens contidas nela, chame o método [delete_queue](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-queue-queue-name--fail-not-exist-false--timeout-none-) .

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Próximos passos

Agora que você aprendeu os conceitos básicos do armazenamento de filas, siga estes links para saber mais.

* [Referência da API do Python das filas do Azure](/python/api/azure-storage-queue)
* [Centro de desenvolvedores do Python](https://azure.microsoft.com/develop/python/)
* [API REST de serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[SDK do Armazenamento do Microsoft Azure para Python]: https://github.com/Azure/azure-storage-python
