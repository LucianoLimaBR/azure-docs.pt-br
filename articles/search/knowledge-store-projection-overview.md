---
title: Trabalhando com projeções em uma loja de conhecimento (visualização)
titleSuffix: Azure Cognitive Search
description: Salve e formate seus dados aprimorados do pipeline de indexação de enriquecimento de ia para uso em cenários diferentes da pesquisa de texto completo.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8e6c285e8917c4224e2007c565d5ac5447b20853
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790011"
---
# <a name="working-with-projections-in-a-knowledge-store-in-azure-cognitive-search"></a>Trabalhando com projeções em uma loja de conhecimento no Azure Pesquisa Cognitiva

> [!Note]
> O armazenamento de dados de conhecimento está em versão prévia e não é destinado para uso em produção. A [API REST versão 2019-05-06-versão prévia](search-api-preview.md) fornece esse recurso. Não há suporte para SDK do .NET no momento.
>

O Azure Pesquisa Cognitiva permite o enriquecimento de conteúdo por meio de habilidades cognitivas internas e habilidades personalizadas como parte da indexação. Aprimoramentos adicionam estrutura a seus documentos e tornam a pesquisa mais eficiente. Em muitos casos, os documentos aprimorados são úteis para cenários diferentes de pesquisa, como para a mineração de conhecimento.

Projeções, um componente da [loja de conhecimento](knowledge-store-concept-intro.md), são exibições de documentos aprimorados que podem ser salvos no armazenamento físico para fins de mineração de conhecimento. Uma projeção permite "projetar" seus dados em uma forma que se alinhe às suas necessidades, preservando as relações para que ferramentas como Power BI possam ler os dados sem esforço adicional.

As projeções podem ser tabulares, com dados armazenados em linhas e colunas no armazenamento de tabelas do Azure ou em objetos JSON armazenados no armazenamento de BLOBs do Azure. Você pode definir várias projeções de seus dados à medida que eles estão sendo aprimorados. Várias projeções são úteis quando você deseja que os mesmos dados sejam formatados de forma diferente para casos de uso individuais.

A loja de conhecimento dá suporte a três tipos de projeções:

+ **Tabelas**: para os dados que são mais bem representados como linhas e colunas, as projeções de tabela permitem que você defina uma forma ou projeção de esquematizados no armazenamento de tabelas.

+ **Objetos**: quando você precisa de uma representação JSON de seus dados e aprimoramentos, as projeções de objeto são salvas como BLOBs.

+ **Arquivos**: quando você precisa salvar as imagens extraídas dos documentos, as projeções de arquivo permitem salvar as imagens normalizadas.

Para ver as projeções definidas no contexto, percorra como começar a [usar a loja de conhecimento](knowledge-store-howto.md).

## <a name="projection-groups"></a>Grupos de projeção

Em alguns casos, você precisará projetar seus dados aprimorados em formas diferentes para atender a diferentes objetivos. A loja de conhecimento permite que você defina vários grupos de projeções. Os grupos de projeção têm as seguintes características principais de exclusividade mútuo e relacionados.

### <a name="mutual-exclusivity"></a>Exclusividade mútuo

Todo o conteúdo projetado em um único grupo é independente dos dados projetados em outros grupos de projeção.
Essa independência implica que você pode ter os mesmos dados formatados de forma diferente, ainda repetidos em cada grupo de projeção.

### <a name="relatedness"></a>Classificação relacionada

Os grupos de projeção agora permitem projetar seus documentos nos tipos de projeção, preservando as relações entre os tipos de projeção. Todo o conteúdo projetado em um único grupo de projeção preserva as relações dentro dos dados entre os tipos de projeção. Em tabelas, as relações são baseadas em uma chave gerada e cada nó filho retém uma referência ao nó pai. Entre tipos (tabelas, objetos e arquivos), as relações são preservadas quando um único nó é projetado entre diferentes tipos. Por exemplo, considere um cenário em que você tenha um documento contendo imagens e texto. Você pode projetar o texto para tabelas ou objetos e as imagens em arquivos em que as tabelas ou objetos têm uma propriedade que contém a URL do arquivo.

## <a name="input-shaping"></a>Shaping de entrada

Obter seus dados na forma ou estrutura corretas é a chave para uso efetivo, ser tabelas ou objetos. A capacidade de Formatar ou estruturar seus dados com base em como você planeja acessar e usá-los é um recurso-chave exposto como a habilidade do **modelador** dentro do Skill.  

As projeções são mais fáceis de definir quando você tem um objeto na árvore de enriquecimento que corresponde ao esquema da projeção. A habilidade atualizada do [modelador](cognitive-search-skill-shaper.md) permite compor um objeto de nós diferentes da árvore de enriquecimento e os pais em um novo nó. A habilidade de **modelador** permite que você defina tipos complexos com objetos aninhados.

Quando você tem uma nova forma definida que contém todos os elementos que precisa para projetar, agora você pode usar essa forma como a origem para suas projeções ou como uma entrada para outra habilidade.

## <a name="projection-slicing"></a>Fatia de projeção

Ao definir um grupo de projeção, um único nó na árvore de enriquecimento pode ser dividido em várias tabelas ou objetos relacionados. Adicionar uma projeção com um caminho de origem que é um filho de uma projeção existente fará com que o nó filho seja dividido do nó pai e projetado para a nova tabela ou objeto relacionados. Essa técnica permite que você defina um único nó em uma habilidade de forma que possa ser a origem de todas as suas projeções.

## <a name="table-projections"></a>Projeções de tabela

Como torna a importação mais fácil, recomendamos projeções de tabela para a exploração de dados com Power BI. Além disso, as projeções de tabela permitem alterar a cardinalidade entre as relações de tabela. 

Você pode projetar um único documento em seu índice em várias tabelas, preservando as relações. Ao projetar em várias tabelas, a forma completa será projetada em cada tabela, a menos que um nó filho seja a origem de outra tabela dentro do mesmo grupo.

### <a name="defining-a-table-projection"></a>Definindo uma projeção de tabela

Ao definir uma projeção de tabela dentro do elemento `knowledgeStore` do seu qualificador, comece mapeando um nó na árvore de enriquecimento para a origem da tabela. Normalmente, esse nó é a saída de uma habilidade de **forma** que você adicionou à lista de habilidades para produzir uma forma específica que você precisa projetar em tabelas. O nó escolhido para o projeto pode ser dividido em um projeto em várias tabelas. A definição de tabelas é uma lista de tabelas que você deseja projetar.

Cada tabela requer três propriedades:

+ TableName: o nome da tabela no armazenamento do Azure.

+ generatedKeyName: o nome da coluna para a chave que identifica exclusivamente essa linha.

+ Fonte: o nó da árvore de enriquecimento da qual você está proenriquecendo seus aprimoramentos. Esse nó geralmente é a saída de uma forma, mas pode ser a saída de qualquer uma das habilidades.

Aqui está um exemplo de projeções de tabela.

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [
            { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
          ]
        },
        {
          "objects": [ ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

Como demonstrado neste exemplo, as frases-chave e as entidades são modeladas em tabelas diferentes e conterá uma referência de volta para o pai (MainTable) para cada linha.

A ilustração a seguir é uma referência ao exercício de lei de caso em como começar a [usar a loja de conhecimento](knowledge-store-howto.md). Em um cenário em que um caso tem várias opiniões e cada opinião é aprimorada identificando as entidades contidas nela, você pode modelar as projeções como mostrado aqui.

![Entidades e relações em tabelas](media/knowledge-store-projection-overview/TableRelationships.png "Relações de modelagem em projeções de tabela")

## <a name="object-projections"></a>Projeções de objeto

As projeções de objeto são representações de JSON da árvore de enriquecimento que podem ser originadas de qualquer nó. Em muitos casos, a mesma habilidade de **forma** que cria uma projeção de tabela pode ser usada para gerar uma projeção de objeto. 

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [
            {
              "storageContainer": "Reviews", 
              "format": "json", 
              "source": "/document/Review", 
              "key": "/document/Review/Id" 
            }
          ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

A geração de uma projeção de objeto requer alguns atributos específicos do objeto:

+ storageContainer: o contêiner em que os objetos serão salvos
+ Source: o caminho para o nó da árvore de enriquecimento que é a raiz da projeção
+ chave: um caminho que representa uma chave exclusiva para o objeto a ser armazenado. Ele será usado para criar o nome do blob no contêiner.

## <a name="file-projection"></a>Projeção de arquivo

As projeções de arquivo são semelhantes às projeções de objeto e só atuam na coleção de `normalized_images`. Semelhante às projeções de objeto, as projeções de arquivo são salvas no contêiner de blob com o prefixo de pasta do valor codificado em base64 da ID do documento. As projeções de arquivo não podem compartilhar o mesmo contêiner como projeções de objeto e precisam ser projetadas em um contêiner diferente.

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [ ]
        },
        {
            "files": [
                 {
                  "storageContainer": "ReviewImages",
                  "source": "/document/normalized_images/*"
                }
            ]
        }
      ]
    }
}
```

## <a name="projection-lifecycle"></a>Ciclo de vida da projeção

Suas projeções têm um ciclo de vida vinculado aos dados de origem em sua fonte de dados. À medida que os dados são atualizados e reindexados, suas projeções são atualizadas com os resultados dos aprimoramentos, garantindo que suas projeções sejam eventualmente consistentes com os dados em sua fonte de dados. As projeções herdam a política de exclusão que você configurou para o índice. As projeções não são excluídas quando o indexador ou o próprio serviço de pesquisa é excluído.

## <a name="using-projections"></a>Usando projeções

Depois que o indexador for executado, você poderá ler os dados projetados nos contêineres ou nas tabelas especificadas por meio de projeções.

Para análise, a exploração no Power BI é tão simples quanto configurar o armazenamento de tabelas do Azure como a fonte de dados. Você pode criar facilmente um conjunto de visualizações em seus dados usando as relações no.

Como alternativa, se você precisar usar os dados aprimorados em um pipeline de ciência de dados, poderá [carregar os dados de BLOBs em um Dataframe do pandas](../machine-learning/team-data-science-process/explore-data-blob.md).

Por fim, se você precisar exportar seus dados da loja de conhecimento, Azure Data Factory terá conectores para exportar os dados e esterrará no banco de dado de sua escolha. 

## <a name="next-steps"></a>Próximos passos

Como uma próxima etapa, crie sua primeira loja de conhecimento usando dados de exemplo e instruções.

> [!div class="nextstepaction"]
> [Como criar uma loja de conhecimento](knowledge-store-howto.md).
