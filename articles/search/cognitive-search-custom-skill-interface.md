---
title: Definição de interface para habilidades personalizadas
titleSuffix: Azure Cognitive Search
description: Interface de extração de dados personalizada para habilidade personalizada de API Web em um pipeline de enriquecimento de ia no Azure Pesquisa Cognitiva.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f86c34dcc1276b8677e3e60514661a6d0b974005
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72787559"
---
# <a name="how-to-add-a-custom-skill-to-an-azure-cognitive-search-enrichment-pipeline"></a>Como adicionar uma habilidade personalizada a um pipeline de enriquecimento de Pesquisa Cognitiva do Azure

Um [pipeline de enriquecimento](cognitive-search-concept-intro.md) no Azure pesquisa cognitiva pode ser montado de [habilidades cognitivas internas](cognitive-search-predefined-skills.md) , bem como [habilidades personalizadas](cognitive-search-custom-skill-web-api.md) que você cria pessoalmente e adiciona ao pipeline. Neste artigo, saiba como criar uma habilidade personalizada que expõe uma interface que permite que ela seja incluída em um pipeline de enriquecimento de ia. 

Criar uma habilidade personalizada oferece a você uma maneira de adicionar transformações únicas ao seu conteúdo. Uma habilidade personalizada será executada de forma independente, aplicando qualquer etapa de enriquecimento que você precisar. Por exemplo, você poderá definir entidades personalizadas específicas por campo, compilar modelos de classificação personalizada para diferenciar contratos e documentos financeiros e corporativos, ou adicionar uma habilidade de reconhecimento de fala para ir ainda mais longe com arquios de áudio para conteúdos relevantes. Para obter um exemplo passo a passo, consulte [exemplo: criando uma habilidade personalizada para o enriquecimento de ia](cognitive-search-create-custom-skill-example.md).

 Para qualquer recurso personalizado que você precisar, haverá uma interface simples e clara para conectar uma habilidade personalizada ao resto do pipeline de enriquecimento. O único requisito para a inclusão em um [conjunto de qualificações](cognitive-search-defining-skillset.md) é a capacidade de aceitar entradas e saídas de maneiras que possam ser utilizadas no conjunto de qualificações como um todo. O foco deste artigo é sobre os formatos de entrada e saída exigidos pelo pipeline de enriquecimento.

## <a name="web-api-custom-skill-interface"></a>Interface de habilidades personalizadas da API da Web

Personalize os pontos de extremidade da habilidade da API da Web por tempo limite padrão caso eles não retornem uma resposta dentro de uma janela de 30 segundos. O pipeline de indexação é síncrono, e a indexação produzirá um erro de tempo limite se a resposta não for recebida nesse intervalo.  É possível configurar o tempo limite para até 230 segundos, definindo o parâmetro timeout:

```json
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill has a 230 second timeout",
        "uri": "https://[your custom skill uri goes here]",
        "timeout": "PT230S",
```

Atualmente, o único mecanismo para interagir com uma habilidade personalizada é por meio de uma interface da API da Web. A API da Web precisa atender aos requisitos descritos nesta seção.

### <a name="1--web-api-input-format"></a>1. formato de entrada da API Web

A API da Web deve aceitar uma matriz de registros a serem processados. Cada registro deve conter um “recipiente de propriedades” que é a entrada fornecida à API da Web. 

Suponha que você deseja criar um enriquecedor simples que identifica a primeira data mencionada no texto de um contrato. Neste exemplo, a habilidade aceita uma única entrada *contractText* como o texto do contrato. A habilidade também tem uma única saída, que é a data do contrato. Para tornar o enriquecedor mais interessante, retorne este *contractDate* na forma de um tipo complexo de várias partes.

Sua API da Web deve estar pronta para receber um lote de registros de entrada. Cada membro da matriz de *valores* representa a entrada de um registro específico. Cada registro deve ter os seguintes elementos:

+ Um membro *recordId* que é o identificador exclusivo de um registro específico. Quando seu enriquecedor retornar os resultados, ele deve fornecer este *recordId* para permitir que o chamador corresponda aos resultados do registro de entrada.

+ Um membro dos *dados*, que é essencialmente um recipiente de campos de entrada para cada registro.

Para ser mais concreto, conforme o exemplo acima, a API da Web deve esperar solicitações com esta aparência:

```json
{
    "values": [
      {
        "recordId": "a1",
        "data":
           {
             "contractText": 
                "This is a contract that was issues on November 3, 2017 and that involves... "
           }
      },
      {
        "recordId": "b5",
        "data":
           {
             "contractText": 
                "In the City of Seattle, WA on February 5, 2018 there was a decision made..."
           }
      },
      {
        "recordId": "c3",
        "data":
           {
             "contractText": null
           }
      }
    ]
}
```
Na realidade, o serviço pode ser chamado com centenas ou milhares de registros em vez de apenas os três mostrados aqui.

### <a name="2-web-api-output-format"></a>2. formato de saída da API Web

O formato da saída é um conjunto de registros que contêm um *recordId* e um recipiente de propriedades 

```json
{
  "values": 
  [
      {
        "recordId": "b5",
        "data" : 
        {
            "contractDate":  { "day" : 5, "month": 2, "year" : 2018 }
        }
      },
      {
        "recordId": "a1",
        "data" : {
            "contractDate": { "day" : 3, "month": 11, "year" : 2017 }                    
        }
      },
      {
        "recordId": "c3",
        "data" : 
        {
        },
        "errors": [ { "message": "contractText field required "}   ],  
        "warnings": [ {"message": "Date not found" }  ]
      }
    ]
}
```

Esse exemplo específico tem apenas uma saída, mas você pode gerar mais de uma propriedade. 

### <a name="errors-and-warning"></a>Erros e Aviso

Conforme mostrado no exemplo anterior, você pode retornar mensagens de erro e de aviso para cada registro.

## <a name="consuming-custom-skills-from-skillset"></a>Utilizando qualificações personalizadas do conjunto de qualificações

Ao criar um enriquecedor da API da Web, você pode descrever parâmetros e cabeçalhos HTTP como parte da solicitação. O snippet de código a seguir mostra como os parâmetros de solicitação e os cabeçalhos HTTP podem ser descritos como parte da definição do conjunto de qualificações.

```json
{
    "skills": [
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill calls an Azure function, which in turn calls TA sentiment",
        "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/DateExtractor?language=en",
        "context": "/document",
        "httpHeaders": {
            "DateExtractor-Api-Key": "foo"
        },
        "inputs": [
          {
            "name": "contractText",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "contractDate",
            "targetName": "date"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Próximos passos

+ [Habilidades de energia: um repositório de habilidades personalizadas](https://aka.ms/powerskills)
+ [Exemplo: criando uma habilidade personalizada para pesquisa cognitiva](cognitive-search-create-custom-skill-example.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Criar conjunto de habilidades (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Como mapear campos enriquecidos](cognitive-search-output-field-mapping.md)
