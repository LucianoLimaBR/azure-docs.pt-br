---
title: 'Azure Cosmos DB: API .NET do executor em massa, recursos de & do SDK'
description: Saiba tudo sobre o SDK e a API BulkExecutor .NET, incluindo datas de lançamento, datas de desativação e alterações feitas entre cada versão do SDK BulkExecutor .NET do Azure Cosmos DB.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 10/11/2019
ms.author: ramkris
ms.openlocfilehash: 9da994550609b0be44362e0f0059844b288aabbd
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388074"
---
# <a name="net-bulk-executor-library-download-information"></a>Biblioteca bulk executor .Net: informações sobre o download 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Feed de alterações do .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Provedor de recursos REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Executor em massa-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Executor em massa – Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **Descrição**| A biblioteca de executores em massa do .net permite que aplicativos cliente executem operações em massa em contas de Azure Cosmos DB. Essa biblioteca fornece os namespaces BulkImport, BulkUpdate e BulkDelete. O módulo BulkImport pode importar em massa documentos de forma otimizada, de modo que a taxa de transferência provisionada para uma coleção seja consumida até seu limite máximo. O módulo BulkUpdate pode atualizar dados existentes em massa em contêineres Cosmos do Azure como patches. O módulo BulkDelete pode excluir documentos em massa de forma otimizada, de modo que o rendimento provisionado para uma coleção seja consumido em sua extensão máxima.|
|**Baixe o SDK**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Biblioteca de executor em massa no GitHub**| [Github](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**Documentação da API**|[Documentação de referência de API .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**Introdução**|[Introdução ao SDK .NET da biblioteca bulk executor](bulk-executor-dot-net.md)|
| **Framework atualmente com suporte**| Microsoft .NET Framework 4.5.2, 4.6.1 e o .NET Standard 2.0 |

## <a name="release-notes"></a>Notas de versão

### <a name="a-name241-preview241-preview"></a><a name="2.4.1-preview"/>2.4.1-Preview

* TotalElapsedTime corrigidos na resposta de BulkDelete para medir corretamente o tempo total, incluindo quaisquer repetições.

### <a name="a-name240-preview240-preview"></a><a name="2.4.0-preview"/>2.4.0-Preview

* Dependência do SDK alterada para > = 2.5.1

### <a name="a-name230-preview2230-preview2"></a><a name="2.3.0-preview2"/>2.3.0-Preview2

* Suporte adicionado para executor em massa de grafo para aceitar TTL em vértices e bordas

### <a name="a-name220-preview2220-preview2"></a><a name="2.2.0-preview2"/>2.2.0-Preview2

* Correção de um problema, que causou exceções durante o dimensionamento elástico de Azure Cosmos DB ao ser executado no modo de gateway. Essa correção torna funcionalmente equivalente à versão 1.4.1.

### <a name="a-name210-preview2210-preview2"></a><a name="2.1.0-preview2"/>2.1.0-Preview2

* Adicionado suporte BulkDelete para contas da API do SQL para aceitar chave de partição, tuplas de ID de documento a serem excluídas. Essa alteração o torna funcionalmente equivalente à versão do 1.4.0.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Incluindo MongoBulkExecutor para suporte ao .NET Standard 2.0. Esse recurso torna funcionalmente equivalente à versão 1.3.0, com o acréscimo de suporte a .NET Standard 2.0 como a estrutura de destino.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* Adicionado .NET Standard 2,0 como uma das estruturas de destino com suporte para fazer com que a biblioteca de executores em massa funcione com aplicativos .NET Core.

### <a name="a-name184184"></a><a name="1.8.4"/>1.8.4

* Manipulação fixa de identificadores ao chamar BulkImport com uma lista de objetos POCO com valores numéricos.

### <a name="a-name183183"></a><a name="1.8.3"/>1.8.3

* TotalElapsedTime corrigidos na resposta de BulkDelete para medir corretamente o tempo total, incluindo quaisquer repetições.

### <a name="a-name182182"></a><a name="1.8.2"/>1.8.2

* Foi corrigido o alto consumo de CPU em determinados cenários.
* O rastreamento agora usa o rastreamento. Os usuários podem definir ouvintes para a fonte `BulkExecutorTrace`.
* Correção de um cenário raro que poderia causar um bloqueio ao enviar documentos perto de 2 MB de tamanho.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* Atualizado o executor em massa para agora usar a versão mais recente do SDK do .NET Azure Cosmos DB (2.4.0)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0

* Suporte adicionado para executor em massa de grafo para aceitar TTL em vértices e bordas

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

* Correção de um problema, que causou exceções durante o dimensionamento elástico de Azure Cosmos DB ao ser executado no modo de gateway.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

* Adicionado suporte BulkDelete para contas da API do SQL para aceitar chave de partição, tuplas de ID de documento a serem excluídas.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

* Correção de um problema, que causou um problema de formatação no agente do usuário usado pelo executor em massa.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Fez a melhoria nas APIs de importação e atualização de executor em massa para adaptar-se de forma transparente ao dimensionamento elástico do contêiner Cosmos quando o armazenamento excede a capacidade atual sem lançar exceções.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* E aumentado a dependência do SDK .NET do DocumentDB para a versão 2.1.3.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Corrigido um problema, que causou o executor em massa a gerar um erro JSRT ao importar para coleções fixas.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Adicionado suporte para a operação de BulkDelete para contas de API de SQL do Azure Cosmos DB.
* Adicionado suporte para a operação de BulkImport para API do Azure Cosmos DB para MongoDB.
* E aumentado a dependência do SDK .NET do DocumentDB para a versão 2.0.0. 

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2

* Adicionado suporte para a operação de BulkImport para contas de API do Gremlin do Azure Cosmos DB.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1

* Pequena correção de bug para a operação BulkImport das contas da API SQL do Azure Cosmos DB.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Adicionado suporte para operações BulkImport e BulkUpdate para contas da API SQL do Azure Cosmos DB.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre a biblioteca Java do executor em massa, consulte o seguinte artigo:

[SDK da biblioteca de executor em massa do Java e informações de versão](sql-api-sdk-bulk-executor-java.md)
