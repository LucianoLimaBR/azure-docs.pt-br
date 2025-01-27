---
title: Mapeamentos de campo para indexação automatizada usando indexadores
titleSuffix: Azure Cognitive Search
description: Configure mapeamentos de campo em um indexador para considerar as diferenças em nomes de campo e representações de dados.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: cc863ee3dc7f2dc8049fcd22189acac94a855352
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72786967"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Mapeamentos de campo e transformações usando indexadores do Azure Pesquisa Cognitiva

Ao usar os indexadores do Azure Pesquisa Cognitiva, às vezes, você descobre que os dados de entrada não correspondem exatamente ao esquema do índice de destino. Nesses casos, você pode usar **mapeamentos de campo** para remodelar os dados durante o processo de indexação.

Algumas situações em que os mapeamentos de campo são úteis:

* Sua fonte de dados tem um campo chamado `_id`, mas o Pesquisa Cognitiva do Azure não permite nomes de campos que começam com um sublinhado. Um mapeamento de campo permite que você renomeie efetivamente um campo.
* Você deseja preencher vários campos no índice a partir dos mesmos dados de fonte de dados. Por exemplo, talvez você queira aplicar analisadores diferentes a esses campos.
* Você deseja preencher um campo de índice com dados de mais de uma fonte de dados e as fontes de dados usam nomes de campos diferentes.
* Você precisa codificar ou decodificar Base64 seus dados. Mapeamentos de campo dão suporte a diversas **funções de mapeamento**, incluindo funções para codificação e decodificação Base64.

> [!NOTE]
> O recurso de mapeamento de campos dos indexadores do Azure Pesquisa Cognitiva fornece uma maneira simples de mapear campos de dados para campos de índice, com algumas opções de conversão de dados. Dados mais complexos podem exigir pré-processamento para reformatá-lo em um formato que seja fácil de indexar.
>
> Microsoft Azure Data Factory é uma poderosa solução baseada em nuvem para importar e transformar dados. Você também pode escrever código para transformar dados de origem antes da indexação. Para obter exemplos de código, consulte [modelos de dados relacionais de modelo](search-example-adventureworks-modeling.md) e de [vários níveis de modelo](search-example-adventureworks-multilevel-faceting.md).
>

## <a name="set-up-field-mappings"></a>Configurar mapeamentos de campo

Um mapeamento de campo é composto por três partes:

1. Um `sourceFieldName`, que representa um campo na fonte de dados. Essa propriedade é obrigatória.
2. Um `targetFieldName`opcional, que representa um campo em seu índice de pesquisa. Se omitido, o mesmo nome que aparece na fonte de dados é usado.
3. Um `mappingFunction`opcional, que pode transformar seus dados usando uma das várias funções predefinidas. A lista completa de funções está [abaixo](#mappingFunctions).

Os mapeamentos de campo são adicionados à matriz de `fieldMappings` da definição do indexador.

## <a name="map-fields-using-the-rest-api"></a>Mapear campos usando a API REST

Você pode adicionar mapeamentos de campo ao criar um novo indexador usando a solicitação criar API do [indexador](https://docs.microsoft.com/rest/api/searchservice/create-Indexer) . Você pode gerenciar os mapeamentos de campo de um indexador existente usando a solicitação atualizar API do [indexador](https://docs.microsoft.com/rest/api/searchservice/update-indexer) .

Por exemplo, veja como mapear um campo de origem para um campo de destino com um nome diferente:

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

Um campo de origem pode ser referenciado em vários mapeamentos de campo. O exemplo a seguir mostra como "bifurcar" um campo, copiando o mesmo campo de origem para dois campos de índice diferentes:

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> O Azure Pesquisa Cognitiva usa a comparação que não diferencia maiúsculas de minúsculas para resolver os nomes de campo e função em mapeamentos de campo. Isso é conveniente (você não precisa obter todas as maiúsculas e minúsculas corretas), mas isso significa que a fonte de dados ou o índice não pode ter campos que diferem somente maiúsculas e minúsculas.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>Mapear campos usando o SDK do .NET

Você define mapeamentos de campo no SDK do .NET usando a classe [FieldMapping](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.fieldmapping) , que tem as propriedades `SourceFieldName` e `TargetFieldName`e uma referência opcional `MappingFunction`.

Você pode especificar mapeamentos de campo ao construir o indexador, ou mais tarde, definindo diretamente a propriedade `Indexer.FieldMappings`.

O exemplo C# a seguir define os mapeamentos de campo ao construir um indexador.

```csharp
  List<FieldMapping> map = new List<FieldMapping> {
    // removes a leading underscore from a field name
    new FieldMapping("_custId", "custId"),
    // URL-encodes a field for use as the index key
    new FieldMapping("docPath", "docId", FieldMappingFunction.Base64Encode() )
  };

  Indexer sqlIndexer = new Indexer(
    name: "azure-sql-indexer",
    dataSourceName: sqlDataSource.Name,
    targetIndexName: index.Name,
    fieldMappings: map,
    schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

  await searchService.Indexers.CreateOrUpdateAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>Funções de mapeamento de campo

Uma função de mapeamento de campos transforma o conteúdo de um campo antes que ele seja armazenado no índice. Atualmente, há suporte para as seguintes funções de mapeamento:

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>função base64Encode

Executa codificação Base64 de *URL segura* da cadeia de caracteres de entrada. Pressupõe-se que a entrada é codificada para UTF-8.

#### <a name="example---document-key-lookup"></a>Exemplo – pesquisa de chave de documento

Somente caracteres de URL segura podem aparecer em uma chave do documento de Pesquisa Cognitiva do Azure (porque os clientes devem ser capazes de resolver o documento usando a [API de pesquisa](https://docs.microsoft.com/rest/api/searchservice/lookup-document) ). Se o campo de origem da sua chave contiver caracteres não seguros de URL, você poderá usar a função `base64Encode` para convertê-la no momento da indexação.

Quando você recupera a chave codificada no momento da pesquisa, pode usar a função `base64Decode` para obter o valor de chave original e usá-lo para recuperar o documento de origem.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

Se você não incluir uma propriedade Parameters para a função de mapeamento, ela usa como padrão o valor `{"useHttpServerUtilityUrlTokenEncode" : true}`.

O Azure Pesquisa Cognitiva dá suporte a duas codificações Base64 diferentes. Você deve usar os mesmos parâmetros ao codificar e decodificar o mesmo campo. Para obter mais informações, consulte [Opções de codificação Base64](#base64details) para decidir quais parâmetros usar.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>função base64Decode

Executa a decodificação de Base64 da cadeia de caracteres de entrada. Pressupõe-se que a entrada seja uma cadeia de caracteres codificada em base64 *segura para URL* .

#### <a name="example---decode-blob-metadata-or-urls"></a>Exemplo – decodificar metadados ou URLs de BLOB

Os dados de origem podem conter cadeias de caracteres codificadas em base64, como cadeias de caracteres de metadados de BLOB ou URLs da Web, que você deseja tornar pesquisáveis como texto sem formatação. Você pode usar a função `base64Decode` para transformar os dados codificados de volta em cadeias de caracteres regulares ao preencher o índice de pesquisa.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

Se você não incluir uma propriedade Parameters, o valor padrão será o `{"useHttpServerUtilityUrlTokenEncode" : true}`.

O Azure Pesquisa Cognitiva dá suporte a duas codificações Base64 diferentes. Você deve usar os mesmos parâmetros ao codificar e decodificar o mesmo campo. Para obter mais detalhes, consulte [Opções de codificação Base64](#base64details) para decidir quais parâmetros usar.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>opções de codificação Base64

O Azure Pesquisa Cognitiva dá suporte a duas codificações Base64 diferentes: **token de URL HttpServerUtility**e **codificação Base64 segura para URL sem preenchimento**. Uma cadeia de caracteres codificada em base64 durante a indexação deve ser decodificada posteriormente com as mesmas opções de codificação, caso contrário, o resultado não corresponderá ao original.

Se os parâmetros `useHttpServerUtilityUrlTokenEncode` ou `useHttpServerUtilityUrlTokenDecode` para codificação e decodificação respectivamente forem definidos como `true`, `base64Encode` se comporta como [HttpServerUtility. UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) e `base64Decode` se comporta como [HttpServerUtility. UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx).

Se você não estiver usando o .NET Framework completo (ou seja, estiver usando o .NET Core ou outra estrutura) para produzir os valores de chave para emular o comportamento de Pesquisa Cognitiva do Azure, defina `useHttpServerUtilityUrlTokenEncode` e `useHttpServerUtilityUrlTokenDecode` como `false`. Dependendo da biblioteca usada, as funções codificação Base64 e decodificação podem ser diferentes das usadas pelo Pesquisa Cognitiva do Azure.

A tabela a seguir compara codificações diferentes de base64 da cadeia de caracteres `00>00?00`. Para determinar o processamento adicional necessário (se houver) para suas funções de base64, aplique sua função de codificação de biblioteca na cadeia de caracteres `00>00?00` e compare a saída com a saída esperada `MDA-MDA_MDA`.

| Codificação | Saída de codificação Base64 | Processamento adicional após a codificação da biblioteca | Processamento adicional antes da decodificação da biblioteca |
| --- | --- | --- | --- |
| Base64 com preenchimento | `MDA+MDA/MDA=` | Use caracteres com URL segura e remova o preenchimento | Use caracteres base64 padrão e adicione o preenchimento |
| Base64 sem preenchimento | `MDA+MDA/MDA` | Use caracteres com URL segura | Use caracteres base64 padrão |
| Base64 com preenchimento e URL segura | `MDA-MDA_MDA=` | Remover preenchimento | Adicionar preenchimento |
| Base64 sem preenchimento e URL segura | `MDA-MDA_MDA` | Nenhum | Nenhum |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>função extractTokenAtPosition

Divide um campo de cadeia de caracteres usando o delimitador especificado e seleciona o token na posição especificada na divisão resultante.

Essa função usa os seguintes parâmetros:

* `delimiter`: uma cadeia de caracteres a ser usado como o separador ao dividir a cadeia de caracteres de entrada.
* `position`: uma posição baseada em zero do número inteiro do token para escolher depois que a cadeia de caracteres de entrada for dividida.

Por exemplo, se a entrada for `Jane Doe`, o `delimiter` for `" "`(espaço) e o `position` for 0, o resultado será `Jane`; se o `position` for 1, o resultado será `Doe`. Se a posição se refere a um token que não existe, um erro será retornado.

#### <a name="example---extract-a-name"></a>Exemplo – extrair um nome

Sua fonte de dados contém um campo `PersonName` e você deseja indexá-la como dois campos `FirstName` e `LastName` separados. Você pode usar essa função para dividir a entrada usando o caractere de espaço como o delimitador.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection-function"></a>função jsonArrayToStringCollection

Transforma uma cadeia de caracteres formatada como uma matriz JSON de cadeias de caracteres em uma matriz de cadeia de caracteres que pode ser usada para preencher um campo `Collection(Edm.String)` no índice.

Por exemplo, se a cadeia de caracteres de entrada for `["red", "white", "blue"]`, o campo de destino do tipo `Collection(Edm.String)` será preenchido com os três valores `red`, `white` e `blue`. Para valores de entrada que não podem ser analisados como matrizes de cadeia de caracteres JSON, um erro retornará.

#### <a name="example---populate-collection-from-relational-data"></a>Exemplo – preencher a coleção de dados relacionais

O banco de dados SQL do Azure não tem um tipo de dado interno que é mapeado naturalmente para `Collection(Edm.String)` campos na Pesquisa Cognitiva do Azure. Para preencher os campos de coleção de cadeia de caracteres, você pode pré-processar seus dados de origem como uma matriz de cadeia de caracteres JSON e, em seguida, usar a função de mapeamento de `jsonArrayToStringCollection`.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

Para obter um exemplo detalhado que transforma dados relacionais em campos de coleção de índice, consulte [dados relacionais de modelo](search-example-adventureworks-modeling.md).

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>função urlEncode

Essa função pode ser usada para codificar uma cadeia de caracteres de forma que ela seja "segura para a URL". Quando usado com uma cadeia de caracteres que contém caractere que não é permitido em uma URL, essa função converterá os caracteres "não seguros" em equivalentes de entidade de caracteres. Essa função usa o formato de codificação UTF-8.

#### <a name="example---document-key-lookup"></a>Exemplo – pesquisa de chave de documento

`urlEncode` função pode ser usada como uma alternativa à função `base64Encode`, se apenas caracteres não seguros de URL forem convertidos, enquanto mantêm outros caracteres como estão.

Digamos que a cadeia de caracteres de entrada seja `<hello>`-o campo de destino do tipo `(Edm.String)` será populado com o valor `%3chello%3e`

Quando você recupera a chave codificada no momento da pesquisa, pode usar a função `urlDecode` para obter o valor de chave original e usá-lo para recuperar o documento de origem.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "urlEncode"
    }
  }]
 ```

 <a name="urlDecodeFunction"></a>

 ### <a name="urldecode-function"></a>função urlDecode

 Essa função converte uma cadeia de caracteres codificada em URL em uma cadeia de caracteres decodificada usando o formato de codificação UTF-8.

 ### <a name="example---decode-blob-metadata"></a>Exemplo – decodificar metadados de BLOB

 Alguns clientes de armazenamento do Azure URL automaticamente codificam metadados de BLOB se contiverem caracteres não ASCII. No entanto, se você quiser tornar esses metadados pesquisáveis (como texto sem formatação), poderá usar a função `urlDecode` para transformar os dados codificados em cadeias de caracteres regulares ao preencher o índice de pesquisa.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "UrlEncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : {
      "name" : "urlDecode"
    }
  }]
 ```