---
title: Atualizar para Azure Search SDK do .NET versão 10
titleSuffix: Azure Cognitive Search
description: Migre o código para o Azure Search SDK do .NET versão 10 de versões mais antigas. Conheça as novidades e quais alterações de código são necessárias.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4a8550a7f9c6a684a172da6f384039c6050797f6
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793049"
---
# <a name="upgrade-to-azure-search-net-sdk-version-10"></a>Atualizar para Azure Search SDK do .NET versão 10

Se você estiver usando a versão 9,0 ou anterior do [SDK do .net Azure Search](https://aka.ms/search-sdk), este artigo o ajudará a atualizar seu aplicativo para usar a versão 10.

Para obter uma explicação mais geral do SDK, incluindo exemplos, confira [Como usar o Azure Search de um aplicativo .NET](search-howto-dotnet-sdk.md).

A versão 10 adiciona vários recursos e correções de bugs, levando-os ao mesmo nível funcional da versão mais recente da API REST `2019-05-06`. Nos casos em que uma alteração quebra o código existente, vamos orientá-lo pelas [etapas necessárias para resolver o problema](#UpgradeSteps).

> [!NOTE]
> Se você estiver usando a versão 8,0-Preview ou mais antiga, atualize para a versão 9 primeiro e, em seguida, atualize para a versão 10. Consulte [Atualizando para o SDK do .net Azure Search versão 9](search-dotnet-sdk-migration-version-9.md) para obter instruções.
>
> Sua instância de serviço do Azure Search dá suporte a várias versões da API REST, incluindo a última. Você pode continuar usando uma versão quando ela não for mais a última, mas aconselhamos a migrar seu código para usar a versão mais recente. Ao usar a API REST, você deve especificar a versão da API em cada solicitação por meio do parâmetro api-version. Ao usar o SDK do .NET, a versão do SDK que você está usando determina a versão correspondente da API REST. Se estiver usando um SDK mais antigo, você poderá continuar executando esse código sem alterações, mesmo se o serviço for atualizado para dar suporte a uma versão de API mais recente.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>O que há de novo na versão 10
A versão 10 do SDK do .NET Azure Search tem como alvo a versão mais recente disponível da API REST do Azure Search (`2019-05-06`) com essas atualizações:

* Introdução de duas novas habilidades – habilidades [condicionais](cognitive-search-skill-conditional.md) e [habilidades de tradução de texto](cognitive-search-skill-text-translation.md).
* As entradas de [habilidade do modelador](cognitive-search-skill-shaper.md) foram reestruturadas para acomodar a consolidação de contextos aninhados. Para obter mais informações, consulte esta [definição de JSON de exemplo](https://docs.microsoft.com/azure/search/cognitive-search-skill-shaper#scenario-3-input-consolidation-from-nested-contexts).
* Adição de duas novas [funções de mapeamento de campos](search-indexer-field-mappings.md):
    - [urlEncode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urlencode-function)
    - [urlDecode](https://docs.microsoft.com/azure/search/search-indexer-field-mappings#urldecode-function)
* Em determinadas ocasiões, erros e avisos que aparecem no [status de execução do indexador](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) podem ter detalhes adicionais que ajudam na depuração. `IndexerExecutionResult` foi atualizado para refletir esse comportamento.
* As habilidades individuais definidas dentro de um configurador de [habilidades](cognitive-search-defining-skillset.md) podem, opcionalmente, ser identificadas especificando-se uma propriedade `name`.
* `ServiceLimits` mostra os limites para [tipos complexos](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) e `IndexerExecutionInfo` mostra limites/cotas pertinentes do indexador.

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Etapas da atualização

1. Atualize a referência do NuGet para `Microsoft.Azure.Search` usando o console do Gerenciador de pacotes NuGet ou clicando com o botão direito do mouse nas referências do projeto e selecionando "gerenciar pacotes NuGet..." no Visual Studio.

2. Depois que o NuGet tiver baixado os novos pacotes e suas dependências, recompile o projeto. 

3. Se sua compilação falhar, você precisará corrigir cada erro de compilação. Consulte [alterações significativas na versão 10](#ListOfChanges) para obter detalhes sobre como resolver cada erro de compilação em potencial.

4. Após corrigir todos os avisos ou erros de build, você poderá fazer alterações no aplicativo para aproveitar as novas funcionalidades, se desejar. Os novos recursos do SDK são detalhados no [que há de novo na versão 10](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>Alterações recentes na versão 10

Há várias alterações significativas na versão 10 que podem exigir alterações de código, além da recriação do aplicativo.

> [!NOTE]
> A lista de alterações abaixo não é exaustiva. Algumas alterações provavelmente não resultarão em erros de compilação, mas são tecnicamente quebradas, pois elas violam a compatibilidade binária com assemblies que dependem de versões anteriores do Azure Search assemblies do SDK do .NET. Alterações significativas que se enquadram nessa categoria também são listadas junto com as recomendações. Recompile seu aplicativo ao atualizar para a versão 10 para evitar problemas de compatibilidade binária.

### <a name="custom-web-api-skill-definition"></a>Definição de habilidade da API Web personalizada

A definição da [habilidade da API Web personalizada](cognitive-search-custom-skill-web-api.md) foi especificada incorretamente na versão 9 e mais antiga. 

O modelo para `WebApiSkill` especificado `HttpHeaders` como uma propriedade de objeto que _contém_ um dicionário. Criar um conjunto de qualificações com um `WebApiSkill` construído dessa maneira resultaria em uma exceção, pois a API REST consideraria a solicitação mal formada. Esse problema foi corrigido, fazendo `HttpHeaders` **uma propriedade de dicionário de nível superior** no próprio modelo de `WebApiSkill`, que é considerado uma solicitação válida da API REST.

Por exemplo, se você tentou anteriormente instanciar uma `WebApiSkill` da seguinte maneira:

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new WebApiHttpHeaders()
    {
        Headers = new Dictionary<string, string>()
        {
            ["header"] = "value"
        }
    }
};

```

Altere-o para o seguinte, para evitar o erro de validação da API REST:

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new Dictionary<string, string>()
    {
        ["header"] = "value"
    }
};

```

## <a name="shaper-skill-allows-nested-context-consolidation"></a>Habilidade de Modelador permite consolidação de contexto aninhada

A habilidade de Modelador agora pode permitir a consolidação de entrada de contextos aninhados. Para habilitar essa alteração, modificamos `InputFieldMappingEntry` para que possa ser instanciada especificando apenas uma propriedade `Source` ou as propriedades `SourceContext` e `Inputs`.

Provavelmente, você não precisará fazer nenhuma alteração de código; no entanto, observe que apenas uma dessas duas combinações é permitida. Isso significa que:

- Criando um `InputFieldMappingEntry` em que somente `Source` é inicializado é válido.
- Criando um `InputFieldMappingEntry` em que somente `SourceContext` e `Inputs` são inicializados são válidos.
- Todas as outras combinações que envolvem essas três propriedades são inválidas.

Se você decidir começar a usar essa nova funcionalidade, verifique se todos os seus clientes estão atualizados para usar a versão 10 primeiro, antes de distribuir essa alteração. Caso contrário, há uma possibilidade de que uma atualização por um cliente (usando uma versão mais antiga do SDK) para a habilidade do modelador possa resultar em erros de validação.

> [!NOTE]
> Embora o modelo de `InputFieldMappingEntry` subjacente tenha sido modificado para permitir a consolidação de contextos aninhados, ele é usado somente na definição de uma habilidade de forma. Usar esse recurso em outras habilidades, embora seja válido no momento da compilação, resultará em um erro de validação no tempo de execução.

## <a name="skills-can-be-identified-by-a-name"></a>As habilidades podem ser identificadas por um nome

Cada habilidade dentro de um Skill agora tem uma nova propriedade `Name`, que pode ser inicializada em seu código para ajudar a identificar a habilidade. Isso é opcional-quando não especificado (que é o padrão, se nenhuma alteração de código explícita foi feita), ele recebe um nome padrão usando o índice de base 1 da habilidade no conjunto de qualificações, prefixado com o caractere ' # '. Por exemplo, na seguinte definição de qualificable (a maioria das inicializações foi ignorada para fins de brevidade):

```csharp
var skillset = new Skillset()
{
    Skills = new List<Skill>()
    {
        new SentimentSkill(),
        new WebApiSkill(),
        new ShaperSkill(),
        ...
    }
}
```

`SentimentSkill` é atribuído um nome `#1`, `WebApiSkill` é atribuído `#2`, `ShaperSkill` é atribuído `#3` e assim por diante.

Se você optar por identificar as habilidades por um nome personalizado, atualize todas as instâncias de seus clientes para a versão 10 do SDK primeiro. Caso contrário, há uma possibilidade de que um cliente que usa uma versão mais antiga do SDK pudesse `null` a propriedade `Name` de uma habilidade, fazendo com que o cliente retorne ao esquema de nomenclatura padrão.

## <a name="details-about-errors-and-warnings"></a>Detalhes sobre erros e avisos

`ItemError` e `ItemWarning` modelos que encapsulam detalhes de erros e avisos (respectivamente) que ocorrem durante uma execução do indexador foram modificados para incluir três novas propriedades com o objetivo de auxiliar na depuração do indexador. Essas propriedades são:

- `Name`: o nome da origem na qual o erro foi originado. Por exemplo, ele pode se referir a uma habilidade em particular no Skills anexado.
- `Details`: detalhes adicionais detalhados sobre o erro ou aviso.
- `DocumentationLink`: um link para um guia de solução de problemas de erro ou aviso específico.

> [!NOTE]
> Começamos a estruturar nossos erros e avisos para incluir esses detalhes úteis sempre que possível. Estamos trabalhando para garantir que, para todos os erros e avisos, esses detalhes estejam presentes, mas é um trabalho em andamento e esses detalhes adicionais nem sempre podem ser preenchidos.

## <a name="next-steps"></a>Próximos passos

- As alterações na habilidade do modelador têm o impacto mais potencial sobre o código novo ou existente. Como uma próxima etapa, não se esqueça de revisitar este exemplo ilustrando a estrutura de entrada: [exemplo de definição de JSON de habilidade do shaper](cognitive-search-skill-shaper.md)
- Percorra a [visão geral de enriquecimento de ia](cognitive-search-concept-intro.md).
- Apreciamos os seus comentários sobre o SDK. Se você encontrar problemas, sinta-se à vontade para nos pedir ajuda sobre [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search). Se encontrar um bug, você poderá apresentar um problema no [repositório GitHub sobre o SDK do .NET do Azure](https://github.com/Azure/azure-sdk-for-net/issues). Certifique-se de prefixar o título do problema com "[Azure Pesquisa Cognitiva]".

