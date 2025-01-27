---
title: Filtragem, ordenação e paginação de entidades dos serviços de mídia – Azure | Microsoft Docs
description: Este artigo aborda a filtragem, a ordenação e a paginação das entidades dos serviços de mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/11/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: d13ff3944e53f103c03a92e03d217b0066bc97df
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693320"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>Filtragem, ordenação e paginação de entidades de serviços de mídia

Este tópico discute as opções de consulta OData e o suporte à paginação disponíveis quando você está listando entidades dos serviços de mídia do Azure v3.

## <a name="considerations"></a>Considere

* As propriedades das entidades que são do tipo `Datetime` estão sempre no formato UTC.
* O espaço em branco na cadeia de caracteres de consulta deve ser codificado por URL antes de enviar uma solicitação.

## <a name="comparison-operators"></a>Operadores de comparação

Você pode usar os seguintes operadores para comparar um campo com um valor constante:

Operadores de igualdade:

- `eq`: testar se um campo é *igual a* um valor constante.
- `ne`: testar se um campo *não é igual a* um valor constante.

Operadores de intervalo:

- `gt`: testar se um campo é *maior que* um valor constante.
- `lt`: testar se um campo é *menor que* um valor constante.
- `ge`: testar se um campo é *maior ou igual a* uma constante. Valor
- `le`: testar se um campo é *menor ou igual a* um valor constante.

## <a name="filter"></a>Filtrar

Use `$filter` para fornecer um parâmetro de filtro OData para localizar somente os objetos nos quais você está interessado.

O exemplo de REST a seguir filtra o valor de `alternateId` de um ativo:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

O exemplo C# a seguir filtra na data de criação do ativo:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```    

## <a name="order-by"></a>Ordenar por

Use `$orderby` para classificar os objetos retornados pelo parâmetro especificado. Por exemplo:    

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

Para classificar os resultados em ordem crescente ou decrescente, acrescente um `asc` ou `desc` ao nome do campo, separados por um espaço. Por exemplo: `$orderby properties/created desc`.

## <a name="skip-token"></a>Ignorar token

Se uma resposta de consulta contiver muitos itens, o serviço retornará um valor de `$skiptoken` (`@odata.nextLink`) que você usará para obter a próxima página de resultados. Use-a para paginar todo o conjunto de resultados.

No Media Services V3, não é possível configurar o tamanho da página. O tamanho da página varia de acordo com o tipo de entidade. Leia as seções individuais a seguir para obter detalhes.

Se as entidades forem criadas ou excluídas enquanto você estiver paginando pela coleção, as alterações serão refletidas nos resultados retornados (se essas alterações estiverem na parte da coleção que não foi baixada). 

> [!TIP]
> Você sempre deve usar `nextLink` para enumerar a coleção e não depender de um tamanho de página específico.
>
> O valor de `nextLink` estará presente somente se houver mais de uma página de entidades.

Considere o exemplo a seguir de onde `$skiptoken` é usado. Substitua *amstestaccount* pelo nome da sua conta e defina o valor da *versão da API* para a versão mais recente.

Se você solicitar uma lista de ativos como esta:

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

Você receberá uma resposta semelhante a esta:

```
HTTP/1.1 200 OK

{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-00000000-0000-0000-0000-0000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-00000000-0000-0000-0000-000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

Em seguida, você solicitaria a próxima página enviando uma solicitação get para:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

O exemplo C# a seguir mostra como enumerar todos os localizadores de streaming na conta.

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>Usando operadores lógicos para combinar opções de consulta

Os serviços de mídia v3 oferecem suporte a operadores lógicos **or** e **and** . 

O exemplo de REST a seguir verifica o estado do trabalho:

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

Você constrói a mesma consulta C# como esta: 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>Opções de filtragem e ordenação de entidades

A tabela a seguir mostra como você pode aplicar as opções de filtragem e ordenação a diferentes entidades:

|Nome da entidade|Nome da propriedade|Filtrar|Solicite|
|---|---|---|---|
|[Comerciais](https://docs.microsoft.com/rest/api/media/assets/)|Nome|`eq`, `gt`, `lt`, `ge`, `le`|`asc` e `desc`|
||Propriedades. alternateid |`eq`||
||Propriedades. AssetID |`eq`||
||Propriedades. criado| `eq`, `gt`, `lt`| `asc` e `desc`|
|[Políticas de chave de conteúdo](https://docs.microsoft.com/rest/api/media/contentkeypolicies)|Nome|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||Propriedades. criado    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||Propriedades. Description    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`||
||Propriedades. lastModified|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||Propriedades. PolicyId|`eq`, `ne`||
|[Trabalhos](https://docs.microsoft.com/rest/api/media/jobs)| Nome  | `eq`            | `asc` e `desc`|
||Propriedades. State        | `eq`, `ne`        |                         |
||Propriedades. criado      | `gt`, `ge`, `lt` `le`| `asc` e `desc`|
||Propriedades. lastModified | `gt`, `ge`, `lt` `le` | `asc` e `desc`| 
|[Localizadores de streaming](https://docs.microsoft.com/rest/api/media/streaminglocators)|Nome|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||Propriedades. criado    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||Properties. endTime    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
|[Políticas de streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies)|Nome|`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
||Propriedades. criado    |`eq`, `ne`, `ge`, `le`, `gt`, `lt`|`asc` e `desc`|
|[Transformações](https://docs.microsoft.com/rest/api/media/transforms)| Nome | `eq`            | `asc` e `desc`|
|| Propriedades. criado      | `gt`, `ge`, `lt` `le`| `asc` e `desc`|
|| Propriedades. lastModified | `gt`, `ge`, `lt` `le`| `asc` e `desc`|

## <a name="next-steps"></a>Próximos passos

* [Listar ativos](https://docs.microsoft.com/rest/api/media/assets/list)
* [Listar políticas de conteúdo de chave](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)
* [Listar trabalhos](https://docs.microsoft.com/rest/api/media/jobs/list)
* [Listar políticas de streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)
* [Listar localizadores de streaming](https://docs.microsoft.com/rest/api/media/streaminglocators/list)
* [Transmitir um arquivo](stream-files-dotnet-quickstart.md)
* [Cotas e limitações](limits-quotas-constraints.md)
