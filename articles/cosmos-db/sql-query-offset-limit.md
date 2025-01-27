---
title: Cláusula de limite de deslocamento no Azure Cosmos DB
description: Saiba mais sobre a cláusula de limite de deslocamento para Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 7aae56783f83f13b50321c88d69f07d910e589dd
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326884"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Cláusula de limite de deslocamento no Azure Cosmos DB

A cláusula de limite de deslocamento é uma cláusula opcional para ignorar e, em seguida, pegar um número de valores da consulta. A contagem de deslocamento e a contagem de limites são necessárias na cláusula de limite de deslocamento.

Quando o limite de deslocamento é usado em conjunto com uma cláusula ORDER BY, o conjunto de resultados é produzido ao fazer a ação de ignorar e assumir os valores ordenados. Se nenhuma cláusula ORDER BY for usada, ela resultará em uma ordem determinística de valores.

## <a name="syntax"></a>Sintaxe
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argumentos

- `<offset_amount>`

   Especifica o número inteiro de itens que os resultados da consulta devem ignorar.

- `<limit_amount>`
  
   Especifica o número inteiro de itens que os resultados da consulta devem incluir

## <a name="remarks"></a>Comentários
  
  A contagem de deslocamento e a contagem de limites são necessárias na cláusula de limite de deslocamento. Se uma cláusula opcional `ORDER BY` for usada, o conjunto de resultados será produzido fazendo o ignorar os valores ordenados. Caso contrário, a consulta retornará uma ordem fixa de valores. Atualmente, essa cláusula tem suporte para consultas em apenas uma única partição, as consultas entre partições ainda não dão suporte a ela.

## <a name="examples"></a>Exemplos

Por exemplo, aqui está uma consulta que ignora o primeiro valor e retorna o segundo valor (na ordem do nome da cidade residente):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

Os resultados são:

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Aqui está uma consulta que ignora o primeiro valor e retorna o segundo valor (sem ordenação):

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

Os resultados são:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>Próximos passos

- [Introdução](sql-query-getting-started.md)
- [Cláusula SELECT](sql-query-select.md)
- [Cláusula ORDER BY](sql-query-order-by.md)
