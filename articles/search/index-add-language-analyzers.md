---
title: Adicionar analisadores de idioma a campos de cadeia de caracteres em um índice
titleSuffix: Azure Cognitive Search
description: Análise de texto léxico multilíngue para consultas e índices que não estão em inglês no Azure Pesquisa Cognitiva.
manager: nitinme
author: Yahnoosh
ms.author: jlembicz
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
ms.openlocfilehash: f5833da5b15c893499b0d786972eff61c7391137
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790134"
---
# <a name="add-language-analyzers-to-an-azure-cognitive-search-index"></a>Adicionar analisadores de idioma a um índice de Pesquisa Cognitiva do Azure

Um *analisador de idioma* é um tipo específico de [analisador de texto](search-analyzers.md) que executa a análise léxica usando as regras linguísticas do idioma de destino. Cada campo pesquisável tem uma propriedade **analyzer**. Se o índice contém cadeias de caracteres traduzidas, tais como campos separados para texto em inglês e em chinês, você pode especificar analisadores de idioma em cada campo para acessar funcionalidades linguísticas avançados desses analisadores.  

O Azure Pesquisa Cognitiva dá suporte a analisadores 35 apoiados por Lucene e 50 analisadores apoiados por tecnologia proprietária de processamento de linguagem natural da Microsoft usada no Office e no Bing.

## <a name="comparing-analyzers"></a>Comparar analisadores

Alguns desenvolvedores talvez prefiram a solução mais familiar, simples e aberta da Lucene. Os analisadores de idioma da Lucene são mais rápidos, mas os analisadores da Microsoft têm recursos avançados, como derivação, decomposição de palavras (em idiomas como alemão, dinamarquês, holandês, sueco, norueguês, estoniano, finlandês, húngaro, eslovaco) e reconhecimento de entidade (URLs, emails, datas, números). Se possível, você deve executar comparações entre os analisadores da Microsoft e da Lucene para decidir qual é a melhor opção. 

A indexação com analisadores da Microsoft é, em média, de duas a três vezes mais lenta do que seus equivalentes da Lucene, dependendo do idioma. O desempenho da pesquisa não dever significativamente afetado para consultas de tamanho médio. 

### <a name="english-analyzers"></a>Analisadores em inglês

O analisador padrão é Lucene Standard, que funciona bem para o inglês, mas talvez não tão bem quanto o analisador de inglês da Lucene ou o analisador de inglês da Microsoft. 
 
+ O analisador de inglês da Lucene amplia o analisador padrão. Ele remove possessivos (apóstrofos à direita) de palavras, aplica a lematização conforme o algoritmo de lematização de Porter e remove as palavras irrelevantes do inglês.  

+ O analisador de inglês da Microsoft executa a derivação em vez da lematização. Isso significa que ele pode tratar muito melhor as formas irregulares e inflexivas de palavras, o que gera resultados da pesquisa mais relevantes 

## <a name="configuring-analyzers"></a>Configurar analisadores

Os analisadores de idiomas são usados no estado em que se encontram. Para cada campo na definição do índice, você pode definir a propriedade **analyzer** para um nome de analisador que especifica a pilha de idiomas e linguística (Microsoft ou Lucene). O mesmo analisador será aplicado durante a indexação e a pesquisa desse campo. Por exemplo, você pode ter campos separados para descrições de hotéis em inglês, francês e espanhol, existentes lado a lado no mesmo índice. Como alternativa, em vez do **analyzer**, você pode usar o **indexAnalyzer** e o **searchAnalyzer** para ter regras de análises diferentes no tempo de consulta e de indexação. 

Use o parâmetro de consulta **searchFields** para descrever qual campo específico a um idioma pesquisar em suas consultas. Você pode examinar os exemplos de consultas que incluem a propriedade analisador em [Pesquisar Documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Para obter mais informações sobre propriedades de índice, consulte [criar índice &#40;Azure pesquisa cognitiva&#41;API REST](https://docs.microsoft.com/rest/api/searchservice/create-index). Para obter mais informações sobre a análise no Azure Pesquisa Cognitiva, consulte [analisadores no azure pesquisa cognitiva](https://docs.microsoft.com/azure/search/search-analyzers).

<a name="language-analyzer-list"></a>

## <a name="language-analyzer-list"></a>Lista de analisador de idioma 
 Veja abaixo uma lista de idiomas com suporte juntamente com nomes de analisador da Lucene e da Microsoft.  

|Idioma|Nome do analisador da Microsoft|Nome do analisador da Lucene|  
|--------------|-----------------------------|--------------------------|  
|Árabe|ar.microsoft|ar.lucene|  
|Armênia||hy.Lucene|  
|Bangla|bn.microsoft||  
|Basco||Eu.Lucene|  
|Búlgaro|bg.microsoft|BG.Lucene|  
|Catalão|ca.microsoft|CA.Lucene|  
|Chinês (simplificado)|zh-Hans.microsoft|zh-Hans.lucene|  
|Chinês (tradicional)|zh-Hant.microsoft|zh-Hant.lucene|  
|Croata|hr.microsoft||  
|Tcheco|cs.microsoft|cs.lucene|  
|Dinamarquês|da.Microsoft|da.lucene|  
|Holandês|nl.microsoft|nl.lucene|  
|Inglês|en.Microsoft|en.lucene|  
|Estoniano|et.microsoft||  
|Finlandês|fi.microsoft|fi.lucene|  
|Francês|fr.microsoft|fr.lucene|  
|Galego||GL.Lucene|  
|Alemão|de.microsoft|de.lucene|  
|Grego|el.microsoft|el.lucene|  
|Guzerate|gu.microsoft||  
|Hebraico|he.microsoft||  
|Híndi|hi.microsoft|hi.lucene|  
|Húngaro|hu.Microsoft|hu.lucene|  
|Islandês|is.microsoft||  
|Indonésio (Bahasa)|id.microsoft|id.lucene|  
|Irlandês||GA.Lucene|  
|Italiano|it.microsoft|it.lucene|  
|Japonês|ja.microsoft|ja.lucene|  
|Canarim|kN. Microsoft||  
|Coreano|ko.Microsoft|ko.lucene|  
|Letão|lv.microsoft|lv.lucene|  
|Lituano|lt.microsoft||  
|Malaiala|ml.microsoft||  
|Malaio (latino)|ms.microsoft||  
|Marati|mr.microsoft||  
|Norueguês|nb.microsoft|no.lucene|  
|Persa||FA.Lucene|  
|Polonês|pl.Microsoft|pl.lucene|  
|Português (Brasil)|pt-Br.microsoft|pt-Br.lucene|  
|Português (Portugal)|pt-Pt.microsoft|pt-Pt.lucene|  
|Panjabi|pa.microsoft||  
|Romeno|ro.microsoft|ro.lucene|  
|Russo|ru.microsoft|ru.lucene|  
|Sérvio (cirílico)|sr-cyrillic.microsoft||  
|Sérvio (latino)|sr-latin.microsoft||  
|Eslovaco|sk.microsoft||  
|Esloveno|sl.microsoft||  
|Espanhol|es.microsoft|es.lucene|  
|Sueco|sv.microsoft|sv.lucene|  
|Tâmil|ta.microsoft||  
|Télugo|te.microsoft||  
|Tailandês|th.microsoft|th.lucene|  
|Turco|tr.microsoft|tr.lucene|  
|Ucraniano|uk.microsoft||  
|Urdu|ur.microsoft||  
|Vietnamita|vi.microsoft||  

 Todos os analisadores com nomes anotados com **Lucene** são da plataforma de [analisadores de idioma do Apache Lucene](https://lucene.apache.org/core/6_6_1/core/overview-summary.html ).

## <a name="see-also"></a>Consulte  

+ [Criar índice &#40;API REST do Azure pesquisa cognitiva&#41;](https://docs.microsoft.com/rest/api/searchservice/create-index)  

+ [Classe AnalyzerName](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername)  

