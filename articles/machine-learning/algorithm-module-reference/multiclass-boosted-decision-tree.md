---
title: 'Árvore de decisão aumentada multiclasse: referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo árvore de decisão aumentada multiclasse no serviço Azure Machine Learning para criar um classificador usando dados rotulados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 08/22/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 09d2a637099bae414400f49bb89efc5b6652f150
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692812"
---
# <a name="multiclass-boosted-decision-tree"></a>Árvore de decisão aumentada multiclasse

Este artigo descreve um módulo da interface visual (visualização) para Azure Machine Learning serviço.

Use este módulo para criar um modelo de aprendizado de máquina baseado no algoritmo árvores de decisão aumentada.

Uma árvore de decisão aumentada é um método de aprendizado Ensemble no qual a segunda árvore corrige os erros da primeira árvore, a terceira árvore corrige os erros da primeira e segunda árvores e assim por diante. As previsões são baseadas na Ensemble de árvores juntas.

## <a name="how-to-configure"></a>Como configurar 

Este módulo cria um modelo de classificação não treinado. Como a classificação é um método de aprendizado supervisionado, você precisa de um conjunto de informações *rotulado* que inclua uma coluna de rótulo com um valor para todas as linhas.

Você pode treinar esse tipo de modelo usando o [modelo de treinamento](././train-model.md). 

1.  Adicione o módulo **árvore de decisão aumentada multiclasse** ao seu pipeline.

1.  Especifique como você deseja que o modelo seja treinado definindo a opção **criar modo de instrutor** .

    + **Parâmetro único**: se você souber como deseja configurar o modelo, poderá fornecer um conjunto específico de valores como argumentos.


    *  O **número máximo de folhas por árvore** limita o número máximo de nós de terminal (folhas) que podem ser criados em qualquer árvore.
    
        Ao aumentar esse valor, você pode aumentar o tamanho da árvore e obter uma precisão maior, com o risco de superajuste e tempo de treinamento mais longo.
  
    * O **número mínimo de amostras por nó folha** indica o número de casos necessários para criar qualquer nó de terminal (folha) em uma árvore.  

         Ao aumentar esse valor, você aumenta o limite para a criação de novas regras. Por exemplo, com o valor padrão de 1, mesmo um único caso pode fazer com que uma nova regra seja criada. Se você aumentar o valor para 5, os dados de treinamento precisarão conter pelo menos cinco casos que atendam às mesmas condições.

    * A **taxa de aprendizagem** define o tamanho da etapa durante o aprendizado. Insira um número entre 0 e 1.

         A taxa de aprendizagem determina o quão rápido ou lento o aprendiz convergi em uma solução ideal. Se o tamanho da etapa for muito grande, você poderá exceder a solução ideal. Se o tamanho da etapa for muito pequeno, o treinamento levará mais tempo para convergir na melhor solução.

    * **Número de árvores construídas** indica o número total de árvores de decisão a serem criadas no Ensemble. Ao criar mais árvores de decisão, você pode potencialmente obter uma cobertura melhor, mas o tempo de treinamento aumentará.

    *  A **semente de número aleatório** define opcionalmente um inteiro não negativo para usar como o valor de semente aleatória. A especificação de uma semente garante reprodução entre as execuções que têm os mesmos dados e parâmetros.  

         A semente aleatória é definida por padrão como 42. As execuções sucessivas usando sementes aleatórias diferentes podem ter resultados diferentes.

> [!Note]
> Se você definir **criar modo de instrutor** como um **único parâmetro**, conecte um conjunto de um DataSet marcado e o módulo [treinar modelo](./train-model.md) .

## <a name="next-steps"></a>Próximos passos

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 
