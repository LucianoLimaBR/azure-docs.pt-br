---
title: 'Computador de vetor de suporte de duas classes: referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo de **máquina de vetor de suporte de duas classes** no serviço Azure Machine Learning para criar um modelo baseado no algoritmo máquina de vetor de suporte.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: c58e66070f137f9c5d713a45682afac5f30bbd1e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692599"
---
# <a name="two-class-support-vector-machine-module"></a>Módulo de máquina de vetor de suporte de duas classes

Este artigo descreve um módulo da interface visual (visualização) para Azure Machine Learning serviço.

Use este módulo para criar um modelo baseado no algoritmo máquina de vetor de suporte. 

As máquinas de vetor de suporte (SVMs) são uma classe bem pesquisada de métodos de aprendizado supervisionados. Essa implementação específica é adequada à previsão de dois resultados possíveis, com base em variáveis contínuas ou categóricas.

Depois de definir os parâmetros do modelo, treine o modelo usando os módulos de treinamento e fornecendo um conjunto de um *DataSet marcado* que inclui um rótulo ou uma coluna de resultado.

## <a name="about-support-vector-machines"></a>Sobre as máquinas de vetor de suporte

Os computadores de vetor de suporte estão entre os mais antigos dos algoritmos de aprendizado de máquina, e os modelos SVM foram usados em muitos aplicativos, da recuperação de informações à classificação de texto e imagem. SVMs pode ser usado para tarefas de classificação e regressão.

Esse modelo de SVM é um modelo de aprendizado supervisionado que requer dados rotulados. No processo de treinamento, o algoritmo analisa dados de entrada e reconhece padrões em um espaço de recurso multidimensional chamado de *hiperplano*.  Todos os exemplos de entrada são representados como pontos neste espaço e são mapeados para categorias de saída de tal forma que as categorias são divididas por uma grande e limpam uma lacuna possível.

Para previsão, o algoritmo SVM atribui novos exemplos em uma categoria ou na outra, mapeando-os para esse mesmo espaço. 

## <a name="how-to-configure"></a>Como configurar 

Para esse tipo de modelo, é recomendável que você Normalize o conjunto de os antes de usá-lo para treinar o classificador.
  
1.  Adicione o módulo **máquina de vetor de suporte de duas classes** ao seu pipeline.  
  
2.  Especifique como você deseja que o modelo seja treinado, definindo a opção **criar modo de instrutor** .  
  
    -   **Parâmetro único**: se você souber como deseja configurar o modelo, poderá fornecer um conjunto específico de valores como argumentos.  

3.  Para **número de iterações**, digite um número que denota o número de iterações usadas durante a criação do modelo.  
  
     Esse parâmetro pode ser usado para controlar a compensação entre a precisão e a velocidade de treinamento.  
  
4.  Para **lambda**, digite um valor a ser usado como o peso para regularização L1.  
  
     Esse coeficiente de regularização pode ser usado para ajustar o modelo. Valores maiores penalizam modelos mais complexos.  
  
5.  Selecione a opção **normalizar recursos**, se desejar normalizar os recursos antes do treinamento.
  
     Se você aplicar a normalização, antes do treinamento, os pontos de dados serão centralizados na média e dimensionados para ter uma unidade de desvio padrão.
  
6.  Selecione a opção, **projeto para a esfera de unidade**, para normalizar coeficientes.
  
     A projeção de valores para o espaço de unidade significa que, antes do treinamento, os pontos de dados são centralizados em 0 e dimensionados para ter uma unidade de desvio padrão.
  
7.  Em **semente de número aleatório**, digite um valor inteiro para usar como semente se você quiser garantir reprodução em execuções.  Caso contrário, um valor de relógio do sistema será usado como uma semente, o que pode resultar em resultados ligeiramente diferentes entre as execuções.
  
9. Conecte um DataSet rotulado e um dos módulos de [treinamento](module-reference.md):
  
    -   Se você definir **criar modo de instrutor** como **um único parâmetro**, use o módulo [treinar modelo](train-model.md) .
  

10. Execute o pipeline.

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para ver um resumo dos parâmetros do modelo, junto com os pesos do recurso aprendidos do treinamento, clique com o botão direito do mouse na saída do [modelo](./train-model.md)de treinamento e selecione **Visualizar**.

+ Para usar os modelos treinados para fazer previsões, conecte o modelo treinado ao módulo [modelo de Pontuação](score-model.md) .


## <a name="next-steps"></a>Próximos passos

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 