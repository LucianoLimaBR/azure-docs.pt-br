---
title: Sintaxe de consulta Lucene
titleSuffix: Azure Cognitive Search
description: Referência para a sintaxe Lucene completa, conforme usado com o Azure Pesquisa Cognitiva.
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
ms.openlocfilehash: 1b94a1bbab810345ab222be9e7aba2fef0f52549
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72786279"
---
# <a name="lucene-query-syntax-in-azure-cognitive-search"></a>Sintaxe de consulta Lucene no Azure Pesquisa Cognitiva

Você pode escrever consultas no Azure Pesquisa Cognitiva com base na sintaxe do [analisador de consulta do Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) avançada para formulários de consulta especializados: curinga, pesquisa difusa, pesquisa por proximidade, expressões regulares são alguns exemplos. Grande parte da sintaxe do analisador de consulta Lucene é [implementada intacta no azure pesquisa cognitiva](search-lucene-query-architecture.md), com exceção das *pesquisas de intervalo* que são construídas no Azure pesquisa cognitiva por meio de expressões `$filter`. 

## <a name="how-to-invoke-full-parsing"></a>Como invocar a análise completa

Defina o parâmetro de pesquisa `queryType` para especificar qual análise usar. Os valores válidos incluem `simple|full`, com `simple` como o padrão, e `full` para o Lucene. 

<a name="bkmk_example"></a> 

### <a name="example-showing-full-syntax"></a>Exemplo exibindo a sintaxe completa

O exemplo a seguir localiza os documentos no índice usando a sintaxe de consulta do Lucene, que fica evidente no parâmetro `queryType=full`. Essa consulta retorna hotéis onde o campo de categoria contém o termo "orçamento" e todos os campos pesquisáveis com a frase "renovado recentemente". Os documentos com a frase "renovado recentemente" apresentam uma classificação superior como um resultado do valor de aumento de termo (3).  

O parâmetro `searchMode=all` é relevante neste exemplo. Sempre que os operadores estiverem na consulta, geralmente você deve definir `searchMode=all` para garantir que *todos* os critérios sejam correspondidos.

```
GET /indexes/hotels/docs?search=category:budget AND \"recently renovated\"^3&searchMode=all&api-version=2019-05-06&querytype=full
```

 Como alternativa, use POST:  

```
POST /indexes/hotels/docs/search?api-version=2019-05-06
{
  "search": "category:budget AND \"recently renovated\"^3",
  "queryType": "full",
  "searchMode": "all"
}
```

Para obter exemplos adicionais, consulte [exemplos de sintaxe de consulta Lucene para criar consultas no Azure pesquisa cognitiva](search-query-lucene-examples.md). Para obter detalhes sobre como especificar o contingente total dos parâmetros de consulta, consulte [Pesquisar &#40;documentos&#41;Azure pesquisa cognitiva API REST](https://docs.microsoft.com/rest/api/searchservice/Search-Documents).

> [!NOTE]  
>  O Azure Pesquisa Cognitiva também dá suporte à [sintaxe de consulta simples](query-simple-syntax.md), uma linguagem de consulta simples e robusta que pode ser usada para pesquisa de palavra-chave direta.  

##  <a name="bkmk_syntax"></a> Conceitos básicos da sintaxe  
 Os conceitos básicos de sintaxe a seguir se aplicam a todas as consultas que usam a sintaxe do Lucene.  

### <a name="operator-evaluation-in-context"></a>Avaliação de operador no contexto

O posicionamento determina se um símbolo é interpretado como um operador ou apenas outro caractere em uma cadeia de caracteres.

Por exemplo, na sintaxe completa do Lucene, o til (~) é usado na busca difusa e na pesquisa de proximidade. Quando colocado depois de uma frase entre aspas, invoca a procura por proximidade. Quando colocado no final de um termo, o ~ invoca a pesquisa difusa.

Dentro de um termo, como "analista~de~negócios", o caractere não é avaliado como um operador. Nesse caso, supondo que a consulta seja um termo ou uma consulta de frase, a [pesquisa de texto completo](search-lucene-query-architecture.md) com [análise léxica](search-lucene-query-architecture.md#stage-2-lexical-analysis) retira o ~ e divide o termo "analista~de~negócios" em dois: analista OU negócios.

O exemplo acima usa o til (~), mas o mesmo princípio se aplica a todos os operadores.

### <a name="escaping-special-characters"></a>Caracteres especiais de escape

 Os caracteres especiais devem ser substituídos para ser usados como parte do texto da pesquisa. Você também poderá permitir a saída deles prefixando-os com uma barra invertida (\\). Os caracteres especiais que precisam ter a saída permitida incluem o seguinte:  
`+ - && || ! ( ) { } [ ] ^ " ~ * ? : \ /`  

 Por exemplo, para escapar de um caractere curinga, use \\\*.

### <a name="encoding-unsafe-and-reserved-characters-in-urls"></a>Codificação de caracteres reservados e não seguros em URLs

 Verifique se todos os caracteres reservados e não seguros estão codificados em uma URL. Por exemplo, '#' é um caractere que não é seguro por ser um identificador de fragmento/âncora em uma URL. O caractere deverá ser codificado como `%23` se for usado em uma URL. ' & ' e ' = ' são exemplos de caracteres reservados à medida que delimitam parâmetros e especificam valores na Pesquisa Cognitiva do Azure. Consulte [RFC1738: Uniform Resource Locators (URL)](https://www.ietf.org/rfc/rfc1738.txt) para obter mais detalhes.

 Os caracteres não seguros são ``" ` < > # % { } | \ ^ ~ [ ]``. Os caracteres reservados são `; / ? : @ = + &`.

### <a name="precedence-operators-grouping-and-field-grouping"></a>Os operadores de precedência: agrupamento e agrupamento de campo  
 Você pode usar parênteses para criar subconsultas, incluindo operadores dentro da instrução entre parênteses. Por exemplo, `motel+(wifi||luxury)` irá procurar documentos que contenham o termo "motel" e "wifi" ou "luxo" (ou ambos).

O agrupamento de campo é semelhante, mas tem como escopo o agrupamento para um único campo. Por exemplo, `hotelAmenities:(gym+(wifi||pool))` pesquisa o campo "comodidadesDoHotel" para "academia" e "wifi" ou "academia" e "piscina".  

### <a name="searchmode-parameter-considerations"></a>Considerações sobre parâmetros de SearchMode  
 O impacto de `searchMode` em consultas, conforme descrito em [sintaxe de consulta simples no Azure pesquisa cognitiva](query-simple-syntax.md), aplica-se igualmente à sintaxe de consulta Lucene. Ou seja, `searchMode` em conjunto com operadores NOT pode levar a resultados de consulta que podem parecer incomuns se você não for claro nas implicações sobre como definir o parâmetro. Se você mantiver o padrão, `searchMode=any`, e usar um operador NOT, a operação será computada como uma ação OR, de modo que "Nova Iorque" NOT "Seattle" retornará todas as cidades que não sejam Seattle.  

##  <a name="bkmk_boolean"></a>Operadores boolianos (e, ou, não) 
 Sempre especifique operadores boolianos de texto (AND, OR, NOT) com tudo em maiúsculas.  

### <a name="or-operator-or-or-"></a>Operador OR `OR` ou `||`

O operador OR é uma barra vertical ou um caractere de pipe. Por exemplo: `wifi || luxury` irá procurar documentos que contenham "wifi" ou "luxo", ou ambos. Porque OR é o operador de conjunção padrão, que também pode ser deixado de fora, de modo que `wifi luxury` é o equivalente de `wifi || luxuery`.

### <a name="and-operator-and--or-"></a>Operador AND `AND`, `&&` ou `+`

O operador AND é um e comercial ou um sinal de adição. Por exemplo: `wifi && luxury` irá procurar documentos que contenham "wifi" e "luxo". O caractere de adição (+) é usado para termos obrigatórios. Por exemplo, `+wifi +luxury` estipula que os dois termos devem aparecer em algum lugar no campo de um único documento.


### <a name="not-operator-not--or--"></a>Operador NOT `NOT`, `!` ou `-`

O operador NOT é um ponto de exclamação ou o sinal de subtração. Por exemplo: `wifi !luxury` irá procurar documentos que tenham o termo "wifi" e/ou não tenham "luxo". A opção `searchMode` controla se um termo com o operador NOT terá AND ou OR com outros termos da consulta na ausência de um operador + ou | |. Lembre-se de que `searchMode` pode ser definido como `any`(padrão) ou `all`.

Usar `searchMode=any` aumenta o cancelamento de consultas, incluindo mais resultados e, por padrão, será interpretado como "OR NOT". Por exemplo, `wifi -luxury` corresponderá a documentos que contêm o termo *wifi* ou aqueles que não contêm o termo *luxo.*

Usar `searchMode=all` aumenta a precisão de consultas, incluindo menos resultados e, por padrão, será interpretado como "AND NOT". Por exemplo, `wifi -luxury` corresponderá a documentos que contêm o termo `wifi` e não contêm o termo `luxury`. Isso é, indiscutivelmente, um comportamento mais intuitivo para o operador -. Portanto, considere escolher `searchMode=all` em vez de `searchMode=any` se quiser otimizar pesquisas por precisão em vez de recuperá-las, *além disso* seus usuários frequentemente usam o operador `-` nas pesquisas.

##  <a name="bkmk_querysizelimits"></a> Limitações de tamanho de consulta  
 Há um limite para o tamanho das consultas que você pode enviar para o Azure Pesquisa Cognitiva. Especificamente, você pode ter, no máximo, 1024 cláusulas (expressões separadas por AND, OR e assim por diante). Há também um limite de aproximadamente 32 KB em relação ao tamanho de qualquer termo individual em uma consulta. Se seu aplicativo gerar consultas de pesquisa por meio de programação, é recomendável criá-lo de forma que ele não gere consultas de tamanho ilimitado.  

##  <a name="bkmk_searchscoreforwildcardandregexqueries"></a> Classificar consultas de caractere curinga e regex
 O Azure Pesquisa Cognitiva usa a pontuação baseada em frequência ([TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf)) para consultas de texto. No entanto, para consultas curinga e regex em que o escopo de termos pode ser potencialmente amplo, o fator de frequência é ignorado para impedir que a classificação seja direcionada para correspondências de termos mais raros. Todas as correspondências são tratadas da mesma maneira para as pesquisas de caractere curinga e regex.

##  <a name="bkmk_fields"></a>Pesquisa em campo  
Você pode definir uma operação de pesquisa em campo com a sintaxe `fieldName:searchExpression`, em que a expressão de pesquisa pode ser uma única palavra ou frase, ou uma expressão mais complexa entre parênteses, opcionalmente com operadores boolianos. Alguns exemplos incluem o seguinte:  

- gênero:jazz NÃO histórico  

- artistas:("Miles Davis" "John Coltrane")

Coloque várias cadeias de caracteres entre aspas se quiser que ambas cadeias de caracteres sejam avaliadas como uma única entidade, como neste caso, pesquisar duas cidades distintas no campo `artists`.  

O campo especificado em `fieldName:searchExpression` deve ser um campo `searchable`.  Confira [Criar Índice](https://docs.microsoft.com/rest/api/searchservice/create-index) para obter detalhes sobre como os atributos de índice são usados em definições de campo.  

> [!NOTE]
> Ao usar expressões de pesquisa em campo, você não precisa usar o parâmetro `searchFields` porque cada expressão de pesquisa em campo tem um nome de campo especificado explicitamente. No entanto, você ainda poderá usar o parâmetro `searchFields` se quiser executar uma consulta em que algumas partes têm o escopo de um campo específico e o restante pode se aplicar a vários campos. Por exemplo, o `search=genre:jazz NOT history&searchFields=description` de consulta corresponderia `jazz` apenas ao campo `genre`, enquanto ele corresponderia `NOT history` com o campo `description`. O nome do campo fornecido em `fieldName:searchExpression` sempre tem precedência sobre o parâmetro `searchFields`, que é o motivo neste exemplo, não precisamos incluir `genre` no parâmetro `searchFields`.

##  <a name="bkmk_fuzzy"></a> Pesquisa difusa  
 Uma pesquisa difusa encontra correspondências em termos com uma construção semelhante. De acordo com a [documentação do Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html), as pesquisas imprecisas se baseiam na [distância de Damerau-Levenshtein](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance). A pesquisa difusa pode expandir um termo até o máximo de 50 termos que atendem aos critérios de distância. 

 Para fazer uma pesquisa difusa, use o símbolo til "~" no final de uma única palavra com um parâmetro opcional, um número entre 0 e 2 (padrão), que especifica a distância de edição. Por exemplo, "mar~" ou "mar~1" retornaria "mar", "amar" e "maré".

 A pesquisa difusa só pode ser aplicada a termos, não a frases, mas você pode acrescentar o til a cada termo individualmente em um nome ou frase de várias partes. Por exemplo, "Unviersty ~ de ~" Wshington ~ "corresponderia em" University de Washington ".
 

##  <a name="bkmk_proximity"></a> Pesquisa por proximidade  
 As pesquisas de proximidade são usadas para localizar termos que estejam próximos um do outro em um documento. Insira um símbolo til "~" no final de uma frase seguida pelo número de palavras que criam o limite de proximidade. Por exemplo, `"hotel airport"~5` encontrará os termos "hotel" e "aeroporto" em cinco palavras uma da outra em um documento.  


##  <a name="bkmk_termboost"></a> Aumento de termos  
 O aumento de termos refere-se ao aumento da classificação de um documento caso ele contenha o termo aumentado, em relação aos documentos que não contêm o termo. Isso é diferente dos perfis de pontuação, já que os perfis de pontuação aumentam determinados campos, em vez de termos específicos.  

O exemplo a seguir ajuda a ilustrar as diferenças. Considere um perfil de pontuação que aumente as correspondências em um determinado campo, como *gênero* no [exemplo de índice de loja de música](index-add-scoring-profiles.md#bkmk_ex). O aumento de termos pode ser usado para melhorar a posição de determinados termos de pesquisa. Por exemplo, `rock^2 electronic` aumentará os documentos que contêm os termos de pesquisa no campo gênero, à frente de outros campos pesquisáveis no índice. Além disso, os documentos com o termo de pesquisa *rock* serão mais bem classificados do que o outro termo de pesquisa *eletrônico* como resultado do valor de aumento de termo (2).  

 Para aumentar um termo, use o sinal de interpolação, "^", com um fator de aumento (um número) no final do termo que você está pesquisando. Você também pode aumentar as frases. Quanto maior o fator de aumento, mais relevante será o termo em relação a outros termos de pesquisa. Por padrão, o fator de aumento é 1. Embora o fator de aumento deva ser positivo, ele pode ser menor do que 1 (por exemplo, 0,20).  

##  <a name="bkmk_regex"></a> Pesquisa com expressão regular  
 Uma pesquisa de expressão regular encontra uma correspondência com base no conteúdo entre as barras "/", como documentado na [classe RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).  

 Por exemplo, para localizar documentos que contenham "motel" ou "hotel", especifique `/[mh]otel/`.  As pesquisas com expressões regulares são comparadas com palavras individuais.   

##  <a name="bkmk_wildcard"></a> Pesquisa com curinga  
 Você pode usar a sintaxe geralmente reconhecida para pesquisas com vários caracteres curinga (*) ou um caractere curinga (?). Observe que o analisador de consulta Lucene oferece suporte ao uso desses símbolos com um único termo e não uma frase.  

 Por exemplo, para localizar documentos que contêm as palavras com o prefixo "anota", como "anotações" ou "bloco de anotações", especifique "anota*".  

> [!NOTE]  
>  Não é possível usar um símbolo * ou ? como o primeiro caractere de uma pesquisa.  
>  Nenhuma análise de texto é executada em consultas de pesquisa curinga. No momento da consulta, os termos da consulta curinga são comparados com os termos analisados no índice de pesquisa e expandidos.

## <a name="see-also"></a>Consulte  

+ [Pesquisar documentos](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
+ [Sintaxe de expressão OData para filtros e classificação](query-odata-filter-orderby-syntax.md)   
+ [Sintaxe de consulta simples no Azure Pesquisa Cognitiva](query-simple-syntax.md)   
