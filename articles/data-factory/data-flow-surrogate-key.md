---
title: Transformação de chave substituta do fluxo de dados de mapeamento de Azure Data Factory
description: Como usar a transformação de chave substituta do fluxo de dados de mapeamento de Azure Data Factory para gerar valores de chave sequencial
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 144d6298a13d35d94a68b35c443a3a47cefcfc2a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387171"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Mapeando a transformação de chave substituta do fluxo de dados



Use a Transformação de Chave Alternativa para adicionar um valor de chave crescente, arbitrário e não comercial ao conjunto de linhas de seu fluxo de dados. Isso é útil durante a criação de tabelas de dimensões em um modelo de dados analítico de esquema em estrela em que cada membro das tabelas precise ter uma chave exclusiva que seja não comercial, parte importante da metodologia Kimball de DWs.

![Transformação de chave substituta](media/data-flow/surrogate.png "Transformação de chave substituta")

"Coluna de Chave" é o nome que você dará à sua nova coluna de chave alternativa.

"Valor Inicial" é o ponto de partida do valor incremental.

## <a name="increment-keys-from-existing-sources"></a>Incrementar chaves de fontes existentes

Se você quiser iniciar sua sequência a partir de um valor que existe em uma fonte, poderá usar uma transformação de coluna derivada imediatamente após a transformação de chave substituta e adicionar os dois valores juntos:

![SK Add Max](media/data-flow/sk006.png "Adição máxima da transformação de chave substituta")

Para propagar o valor de chave com o máximo anterior, há duas técnicas que você pode usar:

### <a name="database-sources"></a>Fontes de banco de dados

Use a opção "consulta" para selecionar MAX () de sua origem usando a transformação origem:

![Consulta de chave substituta](media/data-flow/sk002.png "Consulta de transformação chave substituta")

### <a name="file-sources"></a>Fontes de arquivo

Se o valor máximo anterior estiver em um arquivo, você poderá usar sua transformação de origem junto com uma transformação agregação e usar a função de expressão MAX () para obter o valor máximo anterior:

![Arquivo de chave substituta](media/data-flow/sk008.png "Arquivo de chave substituta")

Em ambos os casos, você deve unir seus novos dados de entrada junto com sua fonte que contém o valor máximo anterior:

![Junção de chave substituta](media/data-flow/sk004.png "Junção de chave substituta")

## <a name="next-steps"></a>Próximos passos

Esses exemplos usam as transformações de [junção](data-flow-join.md) e [coluna derivada](data-flow-derived-column.md) .
