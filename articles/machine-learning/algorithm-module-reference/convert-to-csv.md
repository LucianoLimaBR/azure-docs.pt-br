---
title: 'Converter em CSV: referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo converter para CSV no serviço de Azure Machine Learning para converter um conjunto de um em um formato CSV que pode ser baixado, exportado ou compartilhado com módulos de script R ou Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 37c1653b31049dc0142cf8c0c6f6d405338b1d1f
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693189"
---
# <a name="convert-to-csv-module"></a>Converter em módulo CSV

Este artigo descreve um módulo da interface visual (visualização) para Azure Machine Learning serviço.

Use este módulo para converter um conjunto de um em um formato CSV que pode ser baixado, exportado ou compartilhado com módulos de script R ou Python.

### <a name="more-about-the-csv-format"></a>Mais sobre o formato CSV 

O formato CSV, que significa "valores separados por vírgulas", é um formato de arquivo usado por muitas ferramentas de aprendizado de máquina externas. CSV é um formato de intercâmbio comum ao trabalhar com linguagens de software livre, como R ou Python.

Mesmo que você faça a maior parte de seu trabalho no Azure Machine Learning, há ocasiões em que você pode achar útil converter seu conjunto de seus conjuntos de seus para CSV para uso em ferramentas externas. Por exemplo:

+ Baixe o arquivo CSV para abri-lo com o Excel ou importe-o para um banco de dados relacional.  
+ Salve o arquivo CSV no armazenamento em nuvem e conecte-o de Power BI para criar visualizações.  
+ Use o formato CSV para preparar dados para uso em R e Python. Basta clicar com o botão direito do mouse na saída do módulo para gerar o código necessário para acessar os dados diretamente do Python ou de um notebook Jupyter. 

Quando você converte um conjunto de um em CSV, o arquivo é salvo em seu espaço de trabalho do Azure ML. Você pode usar um utilitário de armazenamento do Azure para abrir e usar o arquivo diretamente, ou pode clicar com o botão direito do mouse na saída do módulo e baixar o arquivo CSV em seu computador ou usá-lo em código R ou Python.  

## <a name="how-to-configure-convert-to-csv"></a>Como configurar converter em CSV

1.  Adicione o módulo [converter em CSV](./convert-to-csv.md) ao seu pipeline. Você pode encontrar esse módulo no grupo de **conversões de formato de dados** na interface. 

2. Conecte-o a qualquer módulo que produza um conjunto de um DataSet.   
  
3.  Execute o pipeline.

### <a name="results"></a>Resultados
  

Clique duas vezes na saída de [converter em CSV](./convert-to-csv.md)e selecione uma destas opções.  

 + **Conjunto de dados de resultados-download de >** : abre imediatamente uma cópia do em formato CSV que você pode salvar em uma pasta local. Se você não especificar uma pasta, um nome de arquivo padrão será aplicado e o arquivo CSV será salvo na biblioteca de **downloads** local.


 + **Conjunto de resultados-> salvar como DataSet**: salva o arquivo CSV de volta para o espaço de trabalho do Azure ml como um conjunto de uma separado.

 + **Gerar código de acesso a dados**: o Azure ml gera dois conjuntos de código para você acessar os dados, usando Python ou o R. Para acessar os dados, copie o trecho de código para seu aplicativo. (*Gerar código de acesso a dados será colocado em breve.* )

## <a name="next-steps"></a>Próximos passos

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning serviço. 