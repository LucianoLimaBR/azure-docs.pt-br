---
title: Artefato createUiDefinition do aplicativo gerenciado do Azure
description: Mostra como criar o artefato createUiDefinition para um aplicativo gerenciado do Azure. O arquivo é denominado createUiDefinition. JSON.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 3e952476373d1692494d06f22dfeb202cab6d6d7
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528979"
---
# <a name="reference-user-interface-elements-artifact"></a>Referência: artefato dos elementos da interface do usuário

Este artigo é uma referência para um artefato *createUiDefinition. JSON* em aplicativos gerenciados do Azure. Para obter mais informações sobre a criação de elementos da interface do usuário, consulte [criar elementos da interface do usuário](create-uidefinition-elements.md).

## <a name="user-interface-elements"></a>Elementos da interface do usuário

O JSON a seguir mostra um exemplo de arquivo *createUiDefinition. JSON* para aplicativos gerenciados do Azure:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "applicationSettings",
        "label": "Application Settings",
        "subLabel": {
          "preValidation": "Configure your application settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Application Settings",
        "elements": [
          {
            "name": "funcname",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the function to be created",
            "toolTip": "Name of the function to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "storagename",
            "type": "Microsoft.Common.TextBox",
            "label": "Name of the storage to be created",
            "toolTip": "Name of the storage to be created",
            "visible": true,
            "constraints": {
              "required": true
            }
          },
          {
            "name": "zipFileBlobUri",
            "type": "Microsoft.Common.TextBox",
            "defaultValue": "https://github.com/Azure/azure-quickstart-templates/tree/master/101-custom-rp-with-function/artifacts/functionzip/functionpackage.zip",
            "label": "The Uri to the uploaded function zip file",
            "toolTip": "The Uri to the uploaded function zip file",
            "visible": true
          }
        ]
      }
    ],
    "outputs": {
      "funcname": "[steps('applicationSettings').funcname]",
      "storageName": "[steps('applicationSettings').storagename]",
      "zipFileBlobUri": "[steps('applicationSettings').zipFileBlobUri]"
    }
  }
}
```

## <a name="next-steps"></a>Próximos passos

- [Tutorial: criar um aplicativo gerenciado com ações e recursos personalizados](tutorial-create-managed-app-with-custom-provider.md)
- [Referência: artefato do modelo de implantação](reference-main-template-artifact.md)
- [Referência: Exibir artefato de definição](reference-view-definition-artifact.md)