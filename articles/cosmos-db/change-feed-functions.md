---
title: Como usar o feed de alterações do Azure Cosmos DB com o Azure Functions
description: Usar o feed de alterações do Azure Cosmos DB com o Azure Functions
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 95fec1ef57c1d70ea484de9ad49b3410ed8594a4
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757062"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Arquiteturas baseadas em eventos sem servidor com Azure Cosmos DB e Azure Functions

Azure Functions fornece a maneira mais simples de se conectar ao [feed de alterações](change-feed.md). Você pode criar pequenas Azure Functions reativas que serão disparadas automaticamente em cada novo evento em seu feed de alterações do contêiner Cosmos do Azure.

![Funções baseadas em evento sem servidor trabalhando com o gatilho Azure Functions para Cosmos DB](./media/change-feed-functions/functions.png)

Com o [gatilho de Azure Functions para Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger), você pode aproveitar o dimensionamento do [processador do feed de alterações](./change-feed-processor.md)e a funcionalidade de detecção de eventos confiáveis sem a necessidade de manter qualquer infraestrutura de [trabalho](./change-feed-processor.md). Concentre-se apenas na lógica da função do Azure sem se preocupar com o restante do pipeline de fornecimento de eventos. Você pode até mesmo misturar o gatilho com quaisquer outras [associações de Azure Functions](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> Atualmente, o gatilho Azure Functions para Cosmos DB tem suporte para uso somente com a API principal (SQL).

## <a name="requirements"></a>Requisitos

Para implementar um fluxo baseado em eventos sem servidor, você precisa:

* **O contêiner monitorado**: o contêiner monitorado é o contêiner Cosmos do Azure que está sendo monitorado e armazena os dados dos quais o feed de alterações é gerado. Quaisquer inserções, atualizações para o contêiner monitorado são refletidas no feed de alterações do contêiner.
* **O contêiner de concessão**: o contêiner de concessão mantém o estado entre várias e dinâmicas instâncias de função do Azure sem servidor e habilita o dimensionamento dinâmico. Esse contêiner de concessão pode ser criado manualmente ou automaticamente pelo gatilho de Azure Functions para Cosmos DB. Para criar automaticamente o contêiner de concessão, defina o sinalizador *CreateLeaseCollectionIfNotExists* na [configuração](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). Os contêineres de concessão particionados precisam ter uma definição de chave de partição `/id`.

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>Criar seu gatilho de Azure Functions para Cosmos DB

A criação de sua função do Azure com um gatilho Azure Functions para Cosmos DB agora tem suporte em todas as integrações de IDE e CLI do Azure Functions:

* [Extensão do Visual Studio](../azure-functions/functions-develop-vs.md) para usuários do Visual Studio.
* [Extensão principal do Visual Studio](/azure/javascript/tutorial-vscode-serverless-node-01) para Visual Studio Code usuários.
* E, finalmente, as [ferramentas da CLI principal](../azure-functions/functions-run-local.md#create-func) para uma experiência independente de IDE de plataforma cruzada.

## <a name="run-your-trigger-locally"></a>Executar o gatilho localmente

Você pode executar a [função do Azure localmente](../azure-functions/functions-develop-local.md) com o [emulador de Azure Cosmos DB](./local-emulator.md) para criar e desenvolver seus fluxos baseados em evento sem servidor com uma assinatura do Azure ou incorrer em custos.

Se você quiser testar cenários ao vivo na nuvem, poderá [tentar Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem qualquer cartão de crédito ou assinatura do Azure necessária.

## <a name="next-steps"></a>Próximos passos

Agora você pode continuar a saber mais sobre o feed de alterações nos seguintes artigos:

* [Visão geral do feed de alterações](change-feed.md)
* [Maneiras de ler o feed de alterações](read-change-feed.md)
* [Biblioteca do processador do feed usando a alteração](change-feed-processor.md)
* [Como trabalhar com a biblioteca do processador de feed de alterações](change-feed-processor.md)
* [Computação de banco de dados sem servidor usando o Azure Cosmos DB e o Azure Functions](serverless-computing-database.md)
