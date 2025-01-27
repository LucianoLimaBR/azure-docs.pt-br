---
title: CreateUiDefinition. JSON – aplicativo gerenciado do Azure
description: Descreve como criar definições de interface do usuário para o portal do Azure. Usado ao definir aplicativos gerenciados do Azure.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 9009d6a54c27617dd0d1d53369405555becde59d
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529324"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>CreateUiDefinition. JSON para a experiência de criação do aplicativo gerenciado do Azure

Este documento apresenta os principais conceitos do arquivo **createUiDefinition. JSON** que o portal do Azure usa para definir a interface do usuário ao criar um aplicativo gerenciado.

O modelo é o seguinte

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { },
      "resourceTypes": [ ]
   }
}
```

Um CreateUiDefinition sempre contém três propriedades: 

* cliques
* Versão
* parameters

O manipulador deve sempre ser `Microsoft.Azure.CreateUIDef` e a versão mais recente com suporte é `0.1.2-preview`.

O esquema da Propriedade Parameters depende da combinação do manipulador e da versão especificados. Para aplicativos gerenciados, as propriedades com suporte são `basics`, `steps` e `outputs`. As propriedades Basics e Steps contêm os [elementos](create-uidefinition-elements.md) , como TextBoxes e dropdowns, a serem exibidos no portal do Azure. A propriedade Outputs é usada para mapear os valores de saída dos elementos especificados para os parâmetros do modelo de implantação Azure Resource Manager.

Incluir `$schema` é recomendado, mas opcional. Se especificado, o valor de `version` deve corresponder à versão dentro do URI de `$schema`.

Você pode usar um editor de JSON para criar seu createUiDefinition e testá-lo na [área restrita do createUiDefinition](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) para visualizá-lo. Para obter mais informações sobre a área restrita, consulte [testar sua interface do portal para aplicativos gerenciados do Azure](test-createuidefinition.md).

## <a name="basics"></a>Algumas

Noções básicas é a primeira etapa gerada quando o portal do Azure analisa o arquivo. Além de exibir os elementos especificados em `basics`, o portal injeta elementos para que os usuários escolham a assinatura, o grupo de recursos e o local para a implantação. Quando possível, os elementos que consultam parâmetros de toda a implantação, como o nome de um cluster ou credenciais de administrador, devem ir nesta etapa.

## <a name="steps"></a>Etapas

A propriedade Steps pode conter zero ou mais etapas adicionais a serem exibidas após noções básicas, cada uma delas contendo um ou mais elementos. Considere adicionar etapas por função ou camada do aplicativo que está sendo implantado. Por exemplo, adicione uma etapa para entradas de nó mestre e uma etapa para os nós de trabalho em um cluster.

## <a name="outputs"></a>Saídas

O portal do Azure usa a propriedade `outputs` para mapear elementos de `basics` e `steps` para os parâmetros do modelo de implantação Azure Resource Manager. As chaves desse dicionário são os nomes dos parâmetros de modelo e os valores são propriedades dos objetos de saída dos elementos referenciados.

Para definir o nome do recurso do aplicativo gerenciado, você deve incluir um valor chamado `applicationResourceName` na propriedade Outputs. Se você não definir esse valor, o aplicativo atribuirá um GUID para o nome. Você pode incluir uma caixa de texto na interface do usuário que solicita um nome do usuário.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="resource-types"></a>Tipos de recursos

Para filtrar os locais disponíveis somente para os locais que dão suporte aos tipos de recursos a serem implantados, forneça uma matriz dos tipos de recursos. Se você fornecer mais de um tipo de recurso, somente os locais que dão suporte a todos os tipos de recursos serão retornados. Essa propriedade é opcional.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
      "resourceTypes": ["Microsoft.Compute/disks"],
      "basics": [
        ...
```  

## <a name="functions"></a>Funções

O CreateUiDefinition fornece [funções](create-uidefinition-functions.md) para trabalhar com entradas e saídas de elementos e recursos como condicionais. Essas funções são semelhantes na sintaxe e na funcionalidade para Azure Resource Manager funções de modelo.

## <a name="next-steps"></a>Próximos passos

O próprio arquivo createUiDefinition. JSON tem um esquema simples. A profundidade real é proveniente de todos os elementos e funções com suporte. Esses itens são descritos mais detalhadamente em:

- [Elementos](create-uidefinition-elements.md)
- [Funções](create-uidefinition-functions.md)

Um esquema JSON atual para createUiDefinition está disponível aqui: https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json.

Para obter um exemplo de arquivo de interface do usuário, consulte [createUiDefinition. JSON](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json).
