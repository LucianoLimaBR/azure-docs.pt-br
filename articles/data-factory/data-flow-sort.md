---
title: Transformação de classificação de fluxo de dados de mapeamento de Azure Data Factory
description: Transformação de classificação de dados de mapeamento Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 029ce3c509d3f4d241012d3786e60f0c6e95fdc2
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387184"
---
# <a name="azure-data-factory-data-flow-sort-transformations"></a>Azure Data Factory transformações de classificação de fluxo de dados



![Configurações de classificação](media/data-flow/sort.png "Classificar")

A transformação classificação permite que você classifique as linhas de entrada no fluxo de dados atual. As linhas de saída da transformação de classificação seguirão subsequentemente as regras de ordenação que você definir. É possível escolher colunas individuais e classificá-las em ordem crescente ou decrescente usando o indicador de seta ao lado de cada campo. Se você precisar modificar a coluna antes de aplicar a classificação, clique em "Colunas computadas" para iniciar o editor de expressão. Isso fornecerá uma oportunidade de criar uma expressão para a operação de classificação, em vez de simplesmente aplicar uma coluna para a classificação.

## <a name="case-insensitive"></a>Não diferencia maiúsculas de minúsculas
É possível ativar “Não diferencia maiúsculas de minúsculas” se você deseja ignorar maiúsculas e minúsculas ao classificar uma cadeia de caracteres ou campos de texto.

"Classificar somente dentro de partições" aproveita o particionamento de dados do Spark. Ao classificar dados de entrada apenas dentro de cada partição, os Fluxos de dados podem classificar dados particionados em vez de fluxos de dados inteiros.

Cada uma das condições de classificação na Transformação de classificação pode ser reorganizada. Portanto, se você precisar mover uma coluna mais para cima na precedência de classificação, pegue essa linha com o mouse e mova-a para cima ou para baixo na lista de classificação.

Efeitos de particionamento na classificação

O fluxo de dados do ADF é executado nos clusters do Spark de Big Data com os dados distribuídos entre vários nós e partições. É importante ter isso em mente ao criar seu fluxo de dados se você depende da transformação de classificação para manter os dados nessa mesma ordem. Se você optar por reparticionar seus dados em uma transformação subsequente, poderá perder sua classificação devido a essa recombinação de dados.

## <a name="next-steps"></a>Próximos passos

Após a classificação, talvez você queira usar a [transformação Agregação](data-flow-aggregate.md)
