---
title: Elemento de interface do usuário InfoBox do Azure | Microsoft Docs
description: Descreve o elemento de interface do usuário Microsoft. Common. caixa para portal do Azure. Use para adicionar texto ou avisos ao implantar o aplicativo gerenciado.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 67ae05c2ec7b36d76e49f26d5765bbc68b952292
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331682"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Elemento de interface do usuário Microsoft.Common.InfoBox
Um controle que adiciona uma caixa de informações. A caixa contém texto ou avisos importantes que ajudam os usuários a entender os valores que estão fornecendo. Ela também pode ter um link para um URI que ofereça mais informações.

## <a name="ui-sample"></a>Exemplo de interface do usuário
![Microsoft.Common.InfoBox](./media/managed-application-elements/microsoft.common.infobox.png)


## <a name="schema"></a>Esquema
```json
{
  "name": "text1",
  "type": "Microsoft.Common.InfoBox",
  "visible": true,
  "options": {
    "icon": "None",
    "text": "Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo.",
    "uri": "https://www.microsoft.com"
  }
}
```

## <a name="remarks"></a>Comentários

* Para `icon`, use **Nenhum**, **Informações**, **Aviso**, ou **Erro**.
* A propriedade `uri` é opcional.

## <a name="sample-output"></a>Saída de exemplo

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="next-steps"></a>Próximos passos
* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
