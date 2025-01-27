---
title: Indexar BLOBs CSV com o indexador de blob Pesquisa Cognitiva do Azure
titleSuffix: Azure Cognitive Search
description: Rastreie BLOBs CSV no armazenamento de BLOBs do Azure para pesquisa de texto completo usando um índice de Pesquisa Cognitiva do Azure. Os indexadores automatizam a ingestão de dados para fontes de dados selecionadas, como o armazenamento de blobs do Azure.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 18d0eb704deba80bf83b5cae0a598f47181700f7
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793773"
---
# <a name="how-to-index-csv-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Como indexar BLOBs CSV usando um indexador de blob no Azure Pesquisa Cognitiva 

> [!Note]
> o modo de análise delimitedText está em visualização e não se destina ao uso em produção. A [API REST versão 2019-05-06-versão prévia](search-api-preview.md) fornece esse recurso. Não há suporte para SDK do .NET no momento.
>

Por padrão, o [indexador de blob pesquisa cognitiva do Azure](search-howto-indexing-azure-blob-storage.md) analisa blobs de texto delimitados como uma única parte do texto. No entanto, com blobs contendo dados CSV, o ideal é tratar cada linha no blob como um documento separado. Por exemplo, considerando o seguinte texto delimitado, você pode querer analisá-lo em dois documentos, cada um contendo os campos "id", "datePublished" e "tags": 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

Neste artigo, você aprenderá a analisar BLOBs CSV com um indexerby de blob do Azure Pesquisa Cognitiva definindo o `delimitedText` modo de análise. 

> [!NOTE]
> Siga as recomendações de configuração do indexador em [indexação de um para muitos](search-howto-index-one-to-many-blobs.md) para produzir vários documentos de pesquisa de um blob do Azure.

## <a name="setting-up-csv-indexing"></a>Configurando a indexação de CSV
Para indexar BLOBs CSV, crie ou atualize uma definição de indexador com o modo de análise de `delimitedText` em uma solicitação [criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer) :

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders` indica que a primeira linha (não vazia) de cada blob contém cabeçalhos.
Se os blobs não contêm uma linha de cabeçalho inicial, os cabeçalhos devem ser especificados na configuração do indexador: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

É possível personalizar o caractere delimitador usando a configuração `delimitedTextDelimiter`. Por exemplo:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Atualmente, apenas a codificação UTF-8 tem suporte. Se você precisar de suporte para outras codificações, vote nele em [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> Quando você usa o modo de análise de texto delimitado, o Azure Pesquisa Cognitiva pressupõe que todos os BLOBs em sua fonte de dados serão CSV. Se você precisar dar suporte a uma combinação de blobs CSV e não CSV na mesma fonte de dados, vote nela em [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Exemplos de solicitação
Reunindo tudo isso, estes são os exemplos de carga completa. 

Fonte de dados: 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indexador:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-cognitive-search-better"></a>Ajude-nos a tornar o Azure Pesquisa Cognitiva melhor
Caso você tenha solicitações de recursos ou ideias para melhorias, dê sua opinião em [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

