---
title: Nós de Movimentação de Fluxo de Dados do Azure Data Factory
description: Como mover nós em um diagrama de fluxo de dados de mapeamento de Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/04/2018
ms.openlocfilehash: 631a103491b70ca016b94af01995aeeb3f29c77a
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754678"
---
# <a name="mapping-data-flow-move-nodes"></a>Mapeando nós de movimentação de fluxo de dados



![Opções de transformação Agregação](media/data-flow/agghead.png "cabeçalho do agregador")

A superfície de design de Fluxo de Dados do Azure Data Factory é uma superfície de "construção" na qual você cria fluxos de dados de cima para baixo, da esquerda para a direita. Há uma caixa de ferramentas anexada a cada transformação com um símbolo de adição (+). Concentre-se na lógica de negócios em vez de conectar nós por meio de bordas em um ambiente de DAG de forma livre.

Portanto, sem um paradigma do tipo "arrastar e soltar", o modo de "mover" um nó de transformação é alterar o fluxo de entrada. Em vez disso, você moverá as transformações alterando o "fluxo de entrada".

## <a name="streams-of-data-inside-of-data-flow"></a>Fluxos de dados dentro do fluxo de dados

No Fluxo de Dados do Azure Data Factory, os fluxos declaram o fluxo de dados. No painel configurações de transformação, você verá um campo "fluxo de entrada". Isso informa qual fluxo de dados de entrada está alimentando a transformação. Você pode alterar a localização física do seu nó de transformação no gráfico clicando no nome do fluxo de entrada e selecionando outro fluxo de dados. A transformação atual, juntamente com todas as transformações subsequentes no fluxo, será movida para a nova localização.

Se você estiver movendo uma transformação com uma ou mais transformações depois dela, a nova localização no fluxo de dados será adicionado por meio de um novo branch.

Se não tiver transformações subsequentes após o nó que você selecionou, somente essa transformação será movida para a nova localização.

## <a name="next-steps"></a>Próximos passos

Depois de concluir o design do fluxo de dados, ative o botão de depuração e teste-o no modo de depuração diretamente no [Designer de fluxo de dados](concepts-data-flow-debug-mode.md) ou na depuração de [pipeline](control-flow-execute-data-flow-activity.md).
