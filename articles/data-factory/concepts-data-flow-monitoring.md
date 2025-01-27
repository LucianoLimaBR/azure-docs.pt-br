---
title: Monitoramento do Visual do fluxo de dados de mapeamento de Azure Data Factory
description: Como monitorar visualmente Azure Data Factory fluxos de dados
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: e92471bd467557f3ac8eb0404d672355ccab8449
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597102"
---
# <a name="monitor-data-flows"></a>Monitorar fluxos de dados



Depois de concluir a criação e a depuração do fluxo de dados, você desejará agendar seu fluxo de dados para ser executado em um agendamento dentro do contexto de um pipeline. Você pode agendar o pipeline de Azure Data Factory usando gatilhos. Ou você pode usar a opção disparar agora do construtor de pipeline Azure Data Factory para executar uma execução de execução única para testar o fluxo de dados no contexto do pipeline.

Ao executar o pipeline, você poderá monitorar o pipeline e todas as atividades contidas no pipeline, incluindo a atividade de fluxo de dados. Clique no ícone de monitor no painel esquerdo Azure Data Factory interface do usuário. Você verá uma tela semelhante à mostrada abaixo. Os ícones realçados permitirão que você faça drill-through das atividades no pipeline, incluindo a atividade de fluxo de dados.

![Monitoramento de fluxo de dados](media/data-flow/mon001.png "Monitoramento de fluxo de dados")

Você verá estatísticas nesse nível, bem como os tempos de execução e o status. A ID de execução no nível de atividade é diferente da ID de execução no nível do pipeline. A ID de execução no nível anterior é para o pipeline. Clicar no óculos fornecerá detalhes aprofundados sobre a execução do fluxo de dados.

![Monitoramento de fluxo de dados](media/data-flow/mon002.png "Monitoramento de fluxo de dados")

Quando você estiver no modo de exibição de monitoramento de nó gráfico, verá uma versão simplificada somente para exibição do seu grafo de fluxo de dados.

![Monitoramento de fluxo de dados](media/data-flow/mon003.png "Monitoramento de fluxo de dados")

## <a name="view-data-flow-execution-plans"></a>Exibir planos de execução do fluxo de dados

Quando o fluxo de dados é executado no Spark, Azure Data Factory determina caminhos de código ideais com base na totalidade de seu fluxo de dados. Além disso, os caminhos de execução podem ocorrer em diferentes nós de expansão e partições de dados. Portanto, o grafo de monitoramento representa o design do seu fluxo, levando em conta o caminho de execução de suas transformações. Quando você clicar em nós individuais, verá "agrupamentos" que representam o código que foi executado em conjunto no cluster. Os intervalos e as contagens que você vê representam esses grupos em oposição às etapas individuais em seu design.

![Monitoramento de fluxo de dados](media/data-flow/mon004.png "Monitoramento de fluxo de dados")

* Quando você clicar no espaço aberto na janela monitoramento, as estatísticas no painel inferior exibirão contagens de tempo e linha para cada coletor e as transformações que levaram aos dados do coletor para a linhagem de transformação.

* Ao selecionar transformações individuais, você receberá comentários adicionais no painel à direita que mostra estatísticas de partição, contagens de colunas, distorção (como uniformemente os dados são distribuídos entre partições) e curtose (como os com picos são os dados).

* Quando você clica no coletor no modo de exibição de nó, verá a linhagem de coluna. Há três métodos diferentes que as colunas são acumuladas em todo o fluxo de dados para chegar ao coletor. Eles são:

  * Computado: você usa a coluna para processamento condicional ou dentro de uma expressão em seu fluxo de dados, mas não a Lande no coletor
  * Derivado: a coluna é uma nova coluna que você gerou em seu fluxo, ou seja, ela não estava presente na origem
  * Mapeado: a coluna originada da origem e sua está sendo mapeada para um campo de coletor
  * Status do fluxo de dados: o status atual de sua execução
  * Tempo de inicialização do cluster: a quantidade de tempo para adquirir o ambiente de computação do Spark JIT para a execução do fluxo de dados
  * Número de transformações: quantas etapas de transformação estão sendo executadas em seu fluxo
  
![Monitoramento do fluxo de dados do ACIT](media/data-flow/monitornew.png "Novo monitoramento de fluxo de dados")  
  
## <a name="monitor-icons"></a>Ícones de monitor

Esse ícone significa que os dados de transformação já foram armazenados em cache no cluster, portanto, os tempos e o caminho de execução levaram isso em conta:

![Monitoramento de fluxo de dados](media/data-flow/mon004.png "Monitoramento de fluxo de dados")

Você também verá ícones de círculo verdes na transformação. Elas representam uma contagem do número de coletores para os quais os dados estão fluindo.
