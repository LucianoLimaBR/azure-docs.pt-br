---
title: Como usar as filas do Azure/Service-Bus no node. js
description: Saiba como usar as filas do barramento de serviço no Azure de um aplicativo node. js usando o pacote do Azure/Service-Bus.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 10/22/2019
ms.author: aschhab
ms.openlocfilehash: 58049855cc27d51134b9f76a773f32f49c6381b6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790302"
---
# <a name="how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>Como usar filas do barramento de serviço com o Node. js e o pacote do Azure/Service-Bus
> [!div class="op_multi_selector" title1="Linguagem de programação" title2="Pacote node. js"]
> - [(Node. js | Azure-SB)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node. js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

Neste tutorial, você aprende a escrever um programa NodeJS para enviar e receber mensagens de uma fila do barramento de serviço usando o novo pacote de [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) . Esse pacote usa o [protocolo AMQP 1,0](service-bus-amqp-overview.md) mais rápido, enquanto o pacote [Azure-SB](https://www.npmjs.com/package/azure-sb) mais antigo usava [APIs de tempo de execução REST do barramento de serviço](/rest/api/servicebus/service-bus-runtime-rest). Os exemplos são escritos em JavaScript.

## <a name="prerequisites"></a>Pré-requisitos
- Uma assinatura do Azure. Para concluir este tutorial, você precisa de uma conta do Azure. Você pode ativar os [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) ou inscrever-se para uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Se você não tiver uma fila com a qual trabalhar, siga as etapas no artigo [usar portal do Azure para criar uma fila do barramento de serviço](service-bus-quickstart-portal.md) para criar uma fila. Anote a cadeia de conexão para sua instância do barramento de serviço e o nome da fila que você criou. Usaremos esses valores nos exemplos.

> [!NOTE]
> - Este tutorial funciona com exemplos que você pode copiar e executar usando o [NodeJS](https://nodejs.org/). Para obter instruções sobre como criar um aplicativo node. js, consulte [criar e implantar um aplicativo node. js em um site do Azure](../app-service/app-service-web-get-started-nodejs.md)ou [serviço de nuvem do node. js usando o Windows PowerShell](../cloud-services/cloud-services-nodejs-develop-deploy-app.md).
> - O novo pacote de [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) não dá suporte à criação de filas ainda. Use o pacote [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) se desejar criá-los programaticamente.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Usar o NPM (Gerenciador de Pacotes de Nós) para obter o pacote
Para instalar o pacote NPM para o barramento de serviço, abra um prompt de comando que tenha `npm` em seu caminho, altere o diretório para a pasta onde você deseja ter seus exemplos e, em seguida, execute este comando.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>Enviar mensagens a uma fila
Interagir com uma fila do barramento de serviço começa com a instanciação da classe [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) e sua utilização para instanciar a classe [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) . Depois de ter o cliente de fila, você pode criar um remetente e usar o método [Send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) ou [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) nele para enviar mensagens.

1. Abra seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um arquivo chamado `send.js` e cole o código abaixo nele. Esse código enviará 10 mensagens para sua fila.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const sender = queueClient.createSender();
      
      try {
        for (let i = 0; i < 10; i++) {
          const message= {
            body: `Hello world! ${i}`,
            label: `test`,
            userProperties: {
                myCustomPropertyName: `my custom property value ${i}`
           }
          };
          console.log(`Sending message: ${message.body}`);
          await sender.send(message);
        }
        
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Insira a cadeia de conexão e o nome da sua fila no código acima.
4. Em seguida, execute o comando `node send.js` em um prompt de comando para executar esse arquivo.

Parabéns! Você acabou de enviar mensagens para uma fila do barramento de serviço.

As mensagens têm algumas propriedades padrão como `label` e `messageId` que você pode definir ao enviar. Se você quiser definir qualquer propriedade personalizada, use o `userProperties`, que é um objeto JSON que pode conter pares de chave-valor de seus dados personalizados.

As filas do Barramento de Serviço dão suporte ao tamanho máximo de mensagem de 256 KB na [camada Standard](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). Não há limite para o número de mensagens mantidas em uma fila, mas há uma limitação no tamanho total das mensagens mantidas por uma fila. O tamanho da fila é definido no momento da criação, com um limite superior de 5 GB. Para saber mais sobre cotas, confira [Cotas do Barramento de Serviço](service-bus-quotas.md).

## <a name="receive-messages-from-a-queue"></a>Receber mensagens de uma fila
Interagir com uma fila do barramento de serviço começa com a instanciação da classe [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) e sua utilização para instanciar a classe [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) . Depois de ter o cliente de fila, você pode criar um receptor e usar o método [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) ou [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) para receber mensagens.

1. Abra seu editor favorito, como [Visual Studio Code](https://code.visualstudio.com/)
2. Crie um arquivo chamado `recieve.js` e cole o código abaixo nele. Esse código tentará receber 10 mensagens da fila. A contagem real que você recebe depende do número de mensagens na fila e latência de rede.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.receiveAndDelete);
      try {
        const messages = await receiver.receiveMessages(10)
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
         
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. Insira a cadeia de conexão e o nome da sua fila no código acima.
4. Em seguida, execute o comando `node receiveMessages.js` em um prompt de comando para executar esse arquivo.

Parabéns! Você acabou de receber mensagens de uma fila do barramento de serviço.

O método [Createreceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) usa um `ReceiveMode` que é um enum com os valores [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) e [Peeklock](message-transfers-locks-settlement.md#settling-receive-operations). Lembre-se de [liquidar suas mensagens](message-transfers-locks-settlement.md#settling-receive-operations) se usar o modo de `PeekLock` usando qualquer um dos métodos `complete()`, `abandon()`, `defer()`ou `deadletter()` na mensagem.

> [!NOTE]
> É possível gerenciar os recursos do Barramento de Serviço com o [Gerenciador de Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador de Barramento de Serviço permite que usuários se conectem a um namespace de serviço do Barramento de Serviço e administrem entidades de mensagens de uma maneira fácil. A ferramenta fornece recursos avançados, como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Próximos passos
Para saber mais, confira os recursos a seguir.
- [Filas, tópicos e assinaturas](service-bus-queues-topics-subscriptions.md)
- Fazer check-out [de outros exemplos de NodeJS para o barramento de serviço no GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript)
- [Centro de Desenvolvedores do Node.js](https://azure.microsoft.com/develop/nodejs/)

