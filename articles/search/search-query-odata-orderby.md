---
title: Referência de ordem por OData
titleSuffix: Azure Cognitive Search
description: Referência de linguagem OData para sintaxe de order-by nas consultas do Azure Pesquisa Cognitiva.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: e3ca19b5696b9a7ad9b68b180313753a5c9de912
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793294"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>Sintaxe de $orderby OData no Azure Pesquisa Cognitiva

 Você pode usar o [parâmetro **$OrderBy** OData](query-odata-filter-orderby-syntax.md) para aplicar uma ordem de classificação personalizada para os resultados da pesquisa no Azure pesquisa cognitiva. Este artigo descreve a sintaxe de **$OrderBy** em detalhes. Para obter mais informações gerais sobre como usar **$OrderBy** ao apresentar os resultados da pesquisa, consulte [como trabalhar com os resultados da pesquisa no Azure pesquisa cognitiva](search-pagination-page-layout.md).

## <a name="syntax"></a>Sintaxe

O parâmetro **$OrderBy** aceita uma lista separada por vírgulas de até 32 **cláusulas de ordenação**. A sintaxe de uma cláusula order by é descrita pelo seguinte EBNF ([formulário Backus-Naur Estendido](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

Um diagrama de sintaxe interativa também está disponível:

> [!div class="nextstepaction"]
> [Diagrama de sintaxe do OData para Pesquisa Cognitiva do Azure](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Consulte [referência de sintaxe de expressão OData para pesquisa cognitiva do Azure](search-query-odata-syntax-reference.md) para o EBNF completo.

Cada cláusula tem critérios de classificação, opcionalmente seguido por uma direção de classificação (`asc` para crescente ou `desc` para decrescente). Se você não especificar uma direção, o padrão será ascendente. Os critérios de classificação podem ser o caminho de um campo `sortable` ou uma chamada para as funções [`geo.distance`](search-query-odata-geo-spatial-functions.md) ou [`search.score`](search-query-odata-search-score-function.md) .

Se vários documentos tiverem os mesmos critérios de classificação e a função `search.score` não for usada (por exemplo, se você classificar por um campo de `Rating` numérico e três documentos todos tiverem uma classificação de 4), as ligações serão rompidas pela pontuação de documento em ordem decrescente. Quando as pontuações de documento são as mesmas (por exemplo, quando não há nenhuma consulta de pesquisa de texto completo especificada na solicitação), a ordem relativa dos documentos vinculados é indeterminada.

Você pode especificar vários critérios de classificação. A ordem das expressões determina a ordem de classificação final. Por exemplo, para classificar em ordem decrescente pela pontuação, seguido por classificação, a sintaxe seria `$orderby=search.score() desc,Rating desc`.

A sintaxe para `geo.distance` em **$orderby** é a mesma que em **$filter**. Ao usar `geo.distance` em **$orderby**, o campo ao qual a operação se aplica deve ser do tipo `Edm.GeographyPoint` e também deve ser `sortable`.

A sintaxe para `search.score` em **$orderby** é `search.score()`. A função `search.score` não pega nenhum parâmetro.

## <a name="examples"></a>Exemplos

Organizar os hotéis em ordem crescente por taxa base:

    $orderby=BaseRate asc

Organizar os hotéis em ordem decrescente por classificação e, em seguida, em ordem crescente por taxa base (lembre-se de que a ordem crescente é o padrão):

    $orderby=Rating desc,BaseRate

Classifique os hotéis decrescentes por classificação e, em seguida, em ordem crescente por distância das coordenadas fornecidas:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

Classifique os hotéis em ordem decrescente por pesquisa. Pontuação e classificação e, em seguida, em ordem crescente por distância das coordenadas fornecidas. Entre dois hotéis com pontuações e classificações de relevância idênticas, o mais próximo é listado primeiro:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Próximos passos  

- [Como trabalhar com os resultados da pesquisa no Azure Pesquisa Cognitiva](search-pagination-page-layout.md)
- [Visão geral da linguagem de expressão OData para Azure Pesquisa Cognitiva](query-odata-filter-orderby-syntax.md)
- [Referência de sintaxe de expressão OData para Pesquisa Cognitiva do Azure](search-query-odata-syntax-reference.md)
- [Pesquisar documentos &#40;do Azure pesquisa cognitiva API REST&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
