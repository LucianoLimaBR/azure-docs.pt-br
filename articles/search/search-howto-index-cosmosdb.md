---
title: Indexar uma fonte de dados Azure Cosmos DB
titleSuffix: Azure Cognitive Search
description: Rastreie uma fonte de dados Azure Cosmos DB e ingerir dados em um índice pesquisável de texto completo no Azure Pesquisa Cognitiva. Os indexadores automatizam a ingestão de dados para fontes de dados selecionadas, como o Azure Cosmos DB.
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f0224905f8d3872aca9055a77c8182cb2cac67cb
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793821"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>Como indexar dados de Cosmos DB usando um indexador no Azure Pesquisa Cognitiva 

> [!Note]
> O suporte à API do MongoDB está em versão prévia e não se destina ao uso em produção. A [API REST versão 2019-05-06-versão prévia](search-api-preview.md) fornece esse recurso. Não há suporte para Portal ou SDK do .NET no momento.
>
> A API do SQL está geralmente disponível.

Este artigo mostra como configurar um [indexador](search-indexer-overview.md) Azure Cosmos DB para extrair conteúdo e torná-lo pesquisável no Azure pesquisa cognitiva. Este fluxo de trabalho cria um índice de Pesquisa Cognitiva do Azure e o carrega com o texto existente extraído do Azure Cosmos DB. 

Como a terminologia pode ser confusa, vale a pena observar que [Azure Cosmos DB indexação](https://docs.microsoft.com/azure/cosmos-db/index-overview) e a [indexação do Azure pesquisa cognitiva](search-what-is-an-index.md) são operações distintas, exclusivas para cada serviço. Antes de iniciar a indexação de Pesquisa Cognitiva do Azure, seu banco de dados Azure Cosmos DB já deve existir e conter dados.

Você pode usar o [portal](#cosmos-indexer-portal), as APIs REST ou o SDK do .net para indexar o conteúdo do cosmos. O indexador Cosmos DB no Azure Pesquisa Cognitiva pode rastrear [os itens do Azure Cosmos](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) acessados por meio desses protocolos:

* [API do SQL](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference) 
* [API do MongoDB (versão prévia)](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)

> [!Note]
> A voz do usuário tem itens existentes para suporte adicional à API. Você pode transmitir um voto para as APIs do cosmos que você gostaria de ver com suporte no Azure Pesquisa Cognitiva: [API de tabela](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab), [API do Graph](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4) [API do Apache Cassandra](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu).
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>Usar o portal

O método mais fácil para indexação de itens de Cosmos do Azure é usar um assistente no [portal do Azure](https://portal.azure.com/). Por amostragem de dados e leitura de metadados no contêiner, o assistente de [**importação de dados**](search-import-data-portal.md) no Azure pesquisa cognitiva pode criar um índice padrão, mapear campos de origem para campos de índice de destino e carregar o índice em uma única operação. Dependendo do tamanho e complexidade da fonte de dados, é possível ter um índice de pesquisa de texto completo operando em minutos.

É recomendável usar a mesma assinatura do Azure para o Azure Pesquisa Cognitiva e Azure Cosmos DB, preferencialmente na mesma região.

### <a name="1---prepare-source-data"></a>1- Preparar dados de origem

Você deve ter uma conta Cosmos, um banco de dados Cosmos do Azure mapeado para a API do SQL ou a API do MongoDB e um contêiner de documentos JSON. 

Certifique-se de que seu banco de dados do Cosmos DB contenha dado. O [Assistente de importação de dados](search-import-data-portal.md) lê metadados e executa a amostragem de dados para inferir um esquema de índice, mas também carrega dados de Cosmos DB. Se os dados estiverem ausentes, o assistente parará com esse erro "erro ao detectar o esquema de índice da fonte de dados: não foi possível compilar um índice de protótipo porque a fonte de dados ' emptycollection ' não retornou nenhum dado".

### <a name="2---start-import-data-wizard"></a>2 - Iniciar o Assistente para Importação de Dados

Você pode [iniciar o assistente](search-import-data-portal.md) na barra de comandos na página do serviço pesquisa cognitiva do Azure ou clicando em **Adicionar Azure pesquisa cognitiva** na seção **configurações** do painel de navegação esquerdo da sua conta de armazenamento.

   ![Comando importar dados no portal](./media/search-import-data-portal/import-data-cmd2.png "Iniciar o assistente Importar dados")

### <a name="3---set-the-data-source"></a>3 - Configurar a fonte de dados

> [!NOTE] 
> No momento, não é possível criar ou editar fontes de dados do **MongoDB** usando portal do Azure ou o SDK do .net. No entanto, você **pode** monitorar o histórico de execução de indexadores do MongoDB no portal.

Na página **fonte de dados** , a origem deve ser **Cosmos DB**, com as seguintes especificações:

+ **Nome** é o nome do objeto de fonte de dados. Depois de criado, você pode escolher para outras cargas de trabalho.

+ A **conta de Cosmos DB** deve ser a cadeia de conexão primária ou secundária do cosmos DB, com um `AccountEndpoint` e um `AccountKey`. A conta determina se os dados são convertidos como API do SQL ou API do Mongo DB

+ **Banco** de dados é um banco de dados existente da conta. 

+ A **coleção** é um contêiner de documentos. Os documentos devem existir para que a importação seja realizada com sucesso. 

+ A **consulta** poderá ficar em branco se você quiser todos os documentos, caso contrário, poderá inserir uma consulta que selecione um subconjunto de documentos. 

   ![Cosmos DB definição de fonte de dados](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Cosmos DB definição de fonte de dados")

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 - Pular a página de "Adicionar a pesquisa cognitiva" no assistente

Adicionar habilidades cognitivas não é necessário para a importação de documentos. A menos que você tenha uma necessidade específica de [Adicionar o enriquecimento de ia](cognitive-search-concept-intro.md) ao seu pipeline de indexação, ignore esta etapa.

Para ignorar a etapa, primeiro vá para a próxima página.

   ![Botão próxima página para adicionar habilidades](media/search-get-started-portal/next-button-add-cog-search.png)

Nessa página, você pode pular para a personalização do índice.

   ![Ignorar etapa de habilidades cognitivas](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5- Definir atributos de índice

Na página **índice**, deve haver uma lista de campos com um tipo de dados e uma série de caixas de seleção para definir os atributos de índice. O assistente pode gerar uma lista de campos com base em metadados e por amostragem dos dados de origem. 

Você pode selecionar atributos em massa clicando na caixa de seleção na parte superior de uma coluna de atributo. Escolha **recuperável** e **pesquisável** para cada campo que deve ser retornado a um aplicativo cliente e sujeito ao processamento de pesquisa de texto completo. Você observará que inteiros não são de texto completo ou de pesquisa difusa (os números são avaliados de forma idêntica e geralmente são úteis em filtros).

Examine a descrição de [atributos de índice](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) e [analisadores de idioma](https://docs.microsoft.com/rest/api/searchservice/language-support) para obter mais informações. 

Reserve um tempo para revisar suas seleções. Depois de executar o assistente, estruturas de dados físicos são criadas e você não poderá editar esses campos sem descartar e recriar todos os objetos.

   ![Definição de índice de Cosmos DB](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Definição de índice de Cosmos DB")

### <a name="6---create-indexer"></a>6 - Criar indexador

Totalmente especificado, o assistente cria três objetos diferentes em seu serviço de pesquisa. Um objeto de fonte de dados e um objeto de índice são salvos como recursos nomeados em seu serviço de Pesquisa Cognitiva do Azure. A última etapa cria um objeto do indexador. Dar um nome ao indexador permite que ele exista como um recurso autônomo, que pode ser agendado e gerenciado independentemente do objeto de fonte de dados e índice criado na mesma sequência do assistente.

Se você não estiver familiarizado com indexadores, um *indexador* é um recurso no Azure pesquisa cognitiva que rastreia uma fonte de dados externa para conteúdo pesquisável. A saída do assistente de **importação de dados** é um indexador que rastreia sua fonte de dados Cosmos DB, extrai conteúdo pesquisável e importa-o para um índice no Azure pesquisa cognitiva.

A captura de tela a seguir mostra a configuração padrão do indexador. Você pode alternar para **uma vez** se quiser executar o indexador uma vez. Clique em **Enviar** para executar o assistente e criar todos os objetos. A indexação começa imediatamente.

   ![Cosmos DB definição do indexador](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Cosmos DB definição do indexador")

Pode-se monitorar a importação de dados nas páginas do portal. As notificações de progresso indicam o status da indexação e quantos documentos são carregados. 

Quando a indexação estiver concluída, você pode usar o [Search explorer](search-explorer.md) para consultar seu índice.

> [!NOTE]
> Se você não vir os dados esperados, talvez seja necessário definir mais atributos em mais campos. Exclua o índice e o indexador que você acabou de criar e percorra o assistente novamente, modificando suas seleções de atributos de índice na etapa 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>Usar APIs REST

Você pode usar a API REST para indexar Azure Cosmos DB dados, seguindo um fluxo de trabalho de três partes comum a todos os indexadores no Azure Pesquisa Cognitiva: criar uma fonte de dados, criar um índice, criar um indexador. A extração de dados do armazenamento Cosmos ocorre quando você envia a solicitação criar indexador. Depois que essa solicitação for concluída, você terá um índice passível de consulta. 

Se você estiver avaliando o MongoDB, deverá usar o `api-version=2019-05-06-Preview` REST para criar a fonte de dados.

Na sua conta do Cosmos DB, você pode escolher se deseja que a coleção indexe automaticamente todos os documentos. Por padrão, todos os documentos são indexados automaticamente, mas você pode desligar a indexação automática. Quando a indexação estiver desativada, os documentos poderão ser acessados somente através de self-links ou por meio de consultas usando a ID do documento. O Pesquisa Cognitiva do Azure requer que Cosmos DB indexação automática seja ativada na coleção que será indexada pelo Azure Pesquisa Cognitiva. 

> [!WARNING]
> O Azure Cosmos DB é a próxima geração do DocumentDB. Anteriormente, com a versão **2017-11-11** da API, você poderia usar a sintaxe `documentdb`. Isso significa que você pode especificar o tipo de fonte de dados como `cosmosdb` ou `documentdb`. A partir da versão de API **2019-05-06** , o portal e as APIs de pesquisa cognitiva do Azure só dão suporte à sintaxe de `cosmosdb` conforme instruído neste artigo. Isso significa que o tipo de fonte de dados deve `cosmosdb` se você deseja se conectar a um ponto de extremidade de Cosmos DB.

### <a name="1---assemble-inputs-for-the-request"></a>1-montar entradas para a solicitação

Para cada solicitação, você deve fornecer o nome do serviço e a chave de administração para o Azure Pesquisa Cognitiva (no cabeçalho da POSTAgem) e o nome da conta de armazenamento e a chave para o armazenamento de BLOBs. Você pode usar o [postmaster](search-get-started-postman.md) para enviar solicitações HTTP para o Azure pesquisa cognitiva.

Copie os quatro valores a seguir no bloco de notas para que você possa colá-los em uma solicitação:

+ Nome do serviço de Pesquisa Cognitiva do Azure
+ Chave de administração de Pesquisa Cognitiva do Azure
+ Cosmos DB cadeia de conexão

Você pode encontrar esses valores no Portal:

1. Nas páginas do portal para Pesquisa Cognitiva do Azure, copie a URL do serviço de pesquisa da página Visão geral.

2. No painel de navegação esquerdo, clique em **chaves** e copie a chave primária ou secundária (elas são equivalentes).

3. Alterne para as páginas do portal para sua conta de armazenamento do cosmos. No painel de navegação à esquerda, em **configurações**, clique em **chaves**. Esta página fornece um URI, dois conjuntos de cadeias de conexão e dois conjuntos de chaves. Copie uma das cadeias de conexão para o bloco de notas.

### <a name="2---create-a-data-source"></a>2-criar uma fonte de dados

A **fonte de dados** especifica os dados no índice, nas credenciais e nas políticas para identificação de alterações nos dados (como documentos modificados ou excluídos em sua coleção). A fonte de dados é definida como um recurso independente para que possa ser usada por vários indexadores.

Para criar uma fonte de dados, formule uma solicitação POST:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

O corpo da solicitação contém a definição da fonte de dados, que deve incluir os seguintes campos:

| Campo   | Descrição |
|---------|-------------|
| **name** | Obrigatório. Escolha qualquer nome para representar o objeto de fonte de dados. |
|**tipo**| Obrigatório. Deve ser `cosmosdb`. |
|**credentials** | Obrigatório. Deve ser uma cadeia de conexão Cosmos DB.<br/>Para coleções SQL, as cadeias de conexão estão neste formato: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/>Para coleções do MongoDB, adicione **ApiKind = MongoDB** à cadeia de conexão:<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/>Evite números de porta na url do ponto de extremidade. Se você incluir o número da porta, o Azure Pesquisa Cognitiva não poderá indexar o banco de dados Azure Cosmos DB.|
| **contêiner** | Contém os seguintes elementos: <br/>**name**: obrigatório. Especifique a ID da coleção de banco de dados a ser indexada.<br/>**query**: opcional. Você pode especificar uma consulta para mesclar um documento JSON arbitrário em um esquema simples que o Azure Pesquisa Cognitiva pode indexar.<br/>Para coleções do MongoDB, não há suporte para consultas. |
| **dataChangeDetectionPolicy** | Recomendável. Consulte a seção [Indexando documentos alterados](#DataChangeDetectionPolicy).|
|**dataDeletionDetectionPolicy** | Opcional. Consulte a seção [Indexando documentos excluídos](#DataDeletionDetectionPolicy).|

### <a name="using-queries-to-shape-indexed-data"></a>Usando consultas para formatar dados indexados
É possível especificar uma consulta do SQL para mesclar propriedades ou matrizes aninhadas, projetar propriedades JSON e filtrar os dados a serem indexados. 

> [!WARNING]
> Não há suporte para consultas personalizadas para coleções **MongoDB** : `container.query` o parâmetro deve ser definido como nulo ou omitido. Se você precisar usar uma consulta personalizada, informe em [Voz do Usuário](https://feedback.azure.com/forums/263029-azure-search).

Documento de exemplo:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }

Filtrar consulta:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Consulta de mesclagem:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Consulta de projeção:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Consulta de mesclagem de matriz:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3-criar um índice de pesquisa de destino 

[Crie um índice de pesquisa cognitiva de destino do Azure](/rest/api/searchservice/create-index) se você ainda não tiver um. O exemplo a seguir cria um índice com um campo de ID e descrição:

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

Verifique se o esquema do índice de destino é compatível com o esquema dos documentos JSON de origem ou com a saída da projeção de consulta personalizada.

> [!NOTE]
> Para coleções particionadas, a chave de documento padrão é Azure Cosmos DB `_rid` Propriedade, que o Azure Pesquisa Cognitiva automaticamente renomear como `rid` porque os nomes de campo não podem começar com um caractere sublinhado. Além disso, Azure Cosmos DB valores de `_rid` contêm caracteres inválidos nas chaves de Pesquisa Cognitiva do Azure. Por esse motivo, os valores `_rid` são codificados em Base64.
> 
> Para coleções do MongoDB, o Azure Pesquisa Cognitiva renomeia automaticamente a propriedade `_id` como `doc_id`.  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>Mapeamento entre tipos de dados JSON e tipos de dados do Azure Pesquisa Cognitiva
| Tipo de dados JSON | Tipos de campos de índice de destino compatíveis |
| --- | --- |
| Bool |Edm.Boolean, Edm.String |
| Números que se parecem com inteiros |Edm.Int32, Edm.Int64, Edm.String |
| Números que se parecem com pontos flutuantes |Edm.Double, Edm.String |
| string |Edm.String |
| Matrizes de tipos primitivos, por exemplo, [“a”, “b”, “c”] |Collection(Edm.String) |
| Cadeias de caracteres que se parecem com datas |Edm.DateTimeOffset, Edm.String |
| Objetos GeoJSON, por exemplo, { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| Outros objetos JSON |N/D |

### <a name="4---configure-and-run-the-indexer"></a>4-configurar e executar o indexador

Uma vez que o índice e a fonte de dados forem criados, será possível criar o indexador:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Esse indexador é executado a cada duas horas (o intervalo de agendamento é definido como "PT2H"). Para executar um indexador a cada 30 minutos, defina o intervalo para "PT30M". O intervalo mais curto com suporte é de 5 minutos. O agendamento é opcional – se ele for omitido, um indexador será executado apenas uma vez quando for criado. No entanto, você pode executar um indexador sob demanda a qualquer momento.   

Para saber mais sobre a API Criar Indexador, veja [Criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Para obter mais informações sobre como definir agendas do indexador, consulte [como agendar indexadores para o Azure pesquisa cognitiva](search-howto-schedule-indexers.md).

## <a name="use-net"></a>Usar o .NET

O SDK do .NET disponível para o público geral tem paridade total com a API REST disponível. É recomendável examinar a seção anterior da API REST para aprender os conceitos, fluxo de trabalho e requisitos. Consulte a seguinte documentação de referência de API do .NET para implementar um indexador JSON em código gerenciado.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Indexando documentos alterados

A finalidade de uma política de detecção de alteração de dados é identificar de maneira eficaz dados alterados. Atualmente, a única política com suporte é a política `High Water Mark` que usa a propriedade `_ts` (carimbo de data/hora) fornecida pelo Azure Cosmos DB, que é especificada da seguinte maneira:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

O uso dessa política é altamente recomendável para garantir o bom desempenho do indexador. 

Se estiver usando uma consulta personalizada, garanta que a propriedade `_ts` será projetada pela consulta.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Progresso incremental e consultas personalizadas

O progresso incremental durante a indexação garante que, se a execução do indexador é interrompida por falhas transitórias ou limite de tempo de execução, o indexador pode escolher onde ela parou próxima vez que ele é executado, em vez de toda a coleção de zero de índice novamente. Isso é especialmente importante durante a indexação de coleções grandes. 

Para habilitar o progresso incremental usando uma consulta personalizada, certifique-se de que sua consulta classifica os resultados pelo `_ts` coluna. Isso habilita o ponto de verificação periódico que o Azure Pesquisa Cognitiva usa para fornecer o progresso incremental na presença de falhas.   

Em alguns casos, mesmo que a consulta contenha uma cláusula `ORDER BY [collection alias]._ts`, o Azure Pesquisa Cognitiva pode não inferir que a consulta é ordenada pela `_ts`. Você pode informar ao Azure Pesquisa Cognitiva que os resultados são ordenados usando a propriedade de configuração `assumeOrderByHighWaterMarkColumn`. Para especificar essa dica, crie ou atualize o indexador da seguinte maneira: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Indexando documentos excluídos

Quando as linhas são excluídas da coleção, normalmente, você também deseja excluí-las do índice de pesquisa. A finalidade de uma política de detecção de exclusão de dados é identificar de maneira eficaz dados excluídos. Atualmente, a única política com suporte é a política `Soft Delete` (a exclusão recebe algum tipo de marcador), que é especificada da seguinte forma:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Se estiver usando uma consulta personalizada, garanta que a propriedade referenciada por `softDeleteColumnName` é projetada pela consulta.

O seguinte exemplo cria uma fonte de dados com uma política de exclusão reversível:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

## <a name="NextSteps"></a>Próximas etapas

Parabéns! Você aprendeu a integrar o Azure Cosmos DB com o Azure Pesquisa Cognitiva usando um indexador.

* Para saber mais sobre o Azure Cosmos DB, consulte a [página de serviço do Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
* Para saber mais sobre Pesquisa Cognitiva do Azure, consulte a [página de serviço Search](https://azure.microsoft.com/services/search/).
