---
title: Importação de dados para ingestão de dados para um índice de pesquisa
titleSuffix: Azure Cognitive Search
description: Popular e carregar dados em um índice no Azure Pesquisa Cognitiva de fontes de dados externas.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a05291012bcf44b1a07d9b451eef1302862b2fce
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72794152"
---
# <a name="data-import-overview---azure-cognitive-search"></a>Visão geral de importação de dados-Azure Pesquisa Cognitiva

No Azure Pesquisa Cognitiva, as consultas são executadas em seu conteúdo carregado e salvo em um [índice de pesquisa](search-what-is-an-index.md). Este artigo examina as duas abordagens básicas para popular um índice: *enviar por push* seus dados para o índice programaticamente ou apontar um [indexador pesquisa cognitiva do Azure](search-indexer-overview.md) em uma fonte de dados com suporte para efetuar *pull* dos dados.

Com qualquer abordagem, o objetivo é *carregar dados* de uma fonte de dados externa em um índice de pesquisa cognitiva do Azure. O Azure Pesquisa Cognitiva permitirá que você crie um índice vazio, mas até que você envie por Push ou extraia dados para ele, ele não será passível de consulta.

## <a name="pushing-data-to-an-index"></a>Envio por push de dados para um índice
O modelo de push, usado para enviar seus dados por meio de programação para o Azure Pesquisa Cognitiva, é a abordagem mais flexível. Primeiro, ele não tem restrições de tipo de fonte de dados. Qualquer conjunto de post de documentos JSON pode ser enviado por push para um índice de Pesquisa Cognitiva do Azure, pressupondo que cada documento no conjunto de um tenha campos de mapeamento para campos definidos no seu esquema de índice. Em segundo lugar, ele não tem nenhuma restrição de frequência de execução. Você pode enviar por push as alterações para um índice sempre que desejar. Para os aplicativos com requisitos de latência muito baixos (por exemplo, se você precisar que as operações de pesquisa estejam sincronizadas com os bancos de dados de inventário), um modelo de push será sua única opção.

Essa abordagem é mais flexível do que um modelo de pull, pois você pode carregar os documentos individualmente ou em lotes (até 1.000 por lote ou 16 MB, o limite que vier em primeiro lugar). O modelo de push também permite que você carregue documentos no Azure Pesquisa Cognitiva, independentemente de onde estão seus dados.

### <a name="how-to-push-data-to-an-azure-cognitive-search-index"></a>Como enviar dados por push a um índice de Pesquisa Cognitiva do Azure

Você pode usar as APIs a seguir para carregar um ou vários documentos em um índice:

+ [Adicionar, atualizar ou excluir documentos (API REST)](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [Classe indexAction](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) ou [Classe indexBatch](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) 

Atualmente, não há nenhum suporte de ferramenta para enviar por push dados por meio do portal.

Para obter uma introdução a cada metodologia, consulte [início rápido: criar um índice de pesquisa cognitiva do Azure usando o PowerShell](search-create-index-rest-api.md) ou [ C# o início rápido: criar um índice de pesquisa cognitiva do Azure usando o SDK do .net](search-get-started-dotnet.md).

<a name="indexing-actions"></a>

### <a name="indexing-actions-upload-merge-mergeorupload-delete"></a>Ações de indexação: upload, mesclagem, mergeOrUpload, excluir

Você pode controlar o tipo de ação de indexação em uma base por documento, especificando se o documento deve ser carregado de forma completa, mesclado com o conteúdo do documento existente ou excluído.

Na API REST, emita solicitações HTTP POST com corpos de solicitação JSON para a URL do ponto de extremidade do índice de Pesquisa Cognitiva do Azure. Cada objeto JSON na matriz "value" contém a chave do documento e especifica se uma ação de indexação adiciona, atualiza ou exclui o conteúdo do documento. Para obter um exemplo de código, consulte [carregar documentos](search-get-started-dotnet.md#load-documents).

No SDK do .NET, empacote seus dados em um objeto `IndexBatch`. Um `IndexBatch` encapsula uma coleção de objetos `IndexAction`, cada um contendo um documento e uma propriedade que informa ao Azure Pesquisa Cognitiva qual ação executar nesse documento. Para obter um exemplo de código, consulte o guia de [ C# início rápido](search-get-started-dotnet.md).


| @search.action | Descrição | Campos necessários para cada documento | Notas |
| -------------- | ----------- | ---------------------------------- | ----- |
| `upload` |Uma ação `upload` é semelhante a um "upsert", em que o documento será inserido se for novo e atualizado/substituído se existir. |chave, além de quaisquer outros campos que você quiser definir |Ao atualizar/substituir um documento existente, qualquer campo não especificado na solicitação terá seu campo definido para `null`. Isso ocorre mesmo quando o campo tiver sido definido anteriormente como um valor não nulo. |
| `merge` |Atualiza um documento existente com os campos especificados. Se o documento não existir no índice, a mesclagem falhará. |chave, além de quaisquer outros campos que você quiser definir |Qualquer campo que você especificar em uma mesclagem substituirá o campo existente no documento. No SDK do .NET, isso inclui campos do tipo `DataType.Collection(DataType.String)`. Na API REST, isso inclui campos do tipo `Collection(Edm.String)`. Por exemplo, se o documento contiver um campo `tags` com o valor `["budget"]` e você executar uma mesclagem com o valor `["economy", "pool"]` para `tags`, o valor final do campo `tags` será `["economy", "pool"]`. Ele não será `["budget", "economy", "pool"]`. |
| `mergeOrUpload` |Essa ação se comportará como `merge` se já existir um documento com a chave especificada no índice. Se o documento não existir, ele se comportará como `upload` com um novo documento. |chave, além de quaisquer outros campos que você quiser definir |- |
| `delete` |Remove o documento especificado do índice. |somente chave |Todos os campos que você especificar, exceto o campo de chave, serão ignorados. Se você quiser remover um campo individual de um documento, use `merge` e apenas defina o campo explicitamente para null. |

## <a name="decide-which-indexing-action-to-use"></a>Decidir qual ação de indexação será usada
Para importar dados usando o SDK do .NET, (carregar, mesclar, excluir e mergeOrUpload). Dependendo de qual das ações abaixo você escolher, apenas determinados campos deverão ser incluídos em cada documento:


### <a name="formulate-your-query"></a>Formular sua consulta
Há duas maneiras de [pesquisar o índice usando a API REST](https://docs.microsoft.com/rest/api/searchservice/Search-Documents). Uma delas consiste em emitir uma solicitação HTTP POST em que os parâmetros de consulta são definidos em um objeto JSON no corpo da solicitação. A outra maneira consiste em emitir uma solicitação HTTP GET em que os parâmetros de consulta são definidos na URL da solicitação. POST tem [limites mais flexíveis](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) quanto ao tamanho dos parâmetros de consulta do que GET. Por esse motivo, é recomendável usar POST, a menos que haja circunstâncias especiais em que o uso de GET seja mais conveniente.

Para POST e GET, você precisa fornecer o *nome do serviço*, o *nome do índice* e a *versão da API* adequada (a versão atual da API é `2019-05-06` no momento da publicação deste documento) na URL da solicitação. Para GET, você fornece os parâmetros de consulta na *cadeia de consulta* no fim da URL. Veja a seguir o formato da URL:

    https://[service name].search.windows.net/indexes/[index name]/docs?[query string]&api-version=2019-05-06

O formato para POST é o mesmo, mas apenas com api-version nos parâmetros da cadeia de caracteres de consulta.


## <a name="pulling-data-into-an-index"></a>Pull de dados para um índice
O modelo de pull rastreia uma fonte de dados com suporte e carrega automaticamente os dados no índice. No Azure Pesquisa Cognitiva, esse recurso é implementado por meio de *indexadores*, atualmente disponíveis para essas plataformas:

+ [Armazenamento de Blobs](search-howto-indexing-azure-blob-storage.md)
+ [Armazenamento de tabelas](search-howto-indexing-azure-tables.md)
+ [Azure Cosmos DB](https://aka.ms/documentdb-search-indexer)
+ [Banco de Dados SQL do Azure e SQL Server em VMs do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

Indexadores conectam um índice a uma fonte de dados (geralmente uma tabela, exibição ou estrutura equivalente) e mapeiam campos de origem para os campos equivalentes no índice. Durante a execução, o conjunto de linhas é automaticamente transformado em JSON e carregado no índice especificado. Todos os indexadores dão suporte à programação. Assim, você pode especificar com que frequência os dados devem ser atualizados. A maioria dos indexadores fornece controle de alterações se a fonte de dados oferecer suporte a isso. Rastreando as alterações e as exclusões para os documentos existentes, além de reconhecer novos documentos, os indexadores dispensam a necessidade de gerenciar ativamente os dados no índice. 


### <a name="how-to-pull-data-into-an-azure-cognitive-search-index"></a>Como efetuar pull de dados em um índice de Pesquisa Cognitiva do Azure

A funcionalidade de indexador é exposta no [Portal do Azure](search-import-data-portal.md), na [API REST](/rest/api/searchservice/Indexer-operations) e no [SDK do .NET](/dotnet/api/microsoft.azure.search.indexersoperationsextensions). 

Uma vantagem de usar o portal é que o Azure Pesquisa Cognitiva normalmente pode gerar um esquema de índice padrão para você lendo os metadados do conjunto de fonte de origem. Você pode modificar o índice gerado até que ele seja processado. Depois disso, as únicas edições de esquema permitidas são aquelas que não exigem reindexação. Se as alterações que você deseja fazer afetarem o esquema diretamente, será necessário recompilar o índice. 

## <a name="verify-data-import-with-search-explorer"></a>Verificar a importação de dados com o Search Explorer

Uma maneira rápida de executar uma verificação preliminar sobre o upload do documento é usar o **Search Explorer** no portal. O explorador permite consultar um índice sem precisar escrever código. A experiência de pesquisa se baseia nas configurações padrão, como a [sintaxe simples](/rest/api/searchservice/simple-query-syntax-in-azure-search) e padrão [searchMode query parameter](/rest/api/searchservice/search-documents). Os resultados são retornados em JSON para que você possa inspecionar o documento inteiro.

> [!TIP]
> Vários [exemplos de código do Azure pesquisa cognitiva](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search) incluem conjuntos de valores inseridos ou prontamente disponíveis, oferecendo uma maneira fácil de começar. O portal também fornece um indexador de exemplo e uma fonte de dados que consiste em um conjunto de dados de imóveis pequeno (denominado "realestate-us-sample"). Quando você executa o indexador pré-configurado na fonte de dados de exemplo, um índice é criado e carregado com documentos que podem ser consultados no Search Explorer ou pelo código que você escreve.

## <a name="see-also"></a>Consulte

+ [Visão geral do indexador](search-indexer-overview.md)
+ [Passo a passo do portal: criar, carregar, consultar um índice](search-get-started-portal.md)
