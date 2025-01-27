---
title: Erros de cota do Azure | Microsoft Docs
description: Descreve como resolver erros de cota de recursos ao implantar recursos com Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.author: tomfitz
ms.openlocfilehash: 201ddf69f9c28b5b3a4197f91768f749152094de
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390306"
---
# <a name="resolve-errors-for-resource-quotas"></a>Resolva erros de cota de recursos

Este artigo descreve erros de cota com que você pode se deparar durante a implantação de recursos.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Sintoma

Se implantar um modelo que cria recursos que ultrapassam suas cotas do Azure, você receberá um erro de implantação que se parece com:

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

Ou você poderá ver:

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>Causa

As cotas são aplicadas por grupo de recursos, assinaturas, contas e outros escopos. Por exemplo, sua assinatura pode estar configurada para limitar o número de núcleos de uma região. Se tentar implantar uma máquina virtual com mais núcleos do que o valor permitido, você receberá um erro informando que a cota foi excedida.
Para obter informações completas sobre cotas, consulte [Limites, cotas e restrições de serviço e assinatura do Azure](../azure-subscription-service-limits.md).

## <a name="troubleshooting"></a>Solução de Problemas

### <a name="azure-cli"></a>Azure CLI

Na CLI do Azure, use o comando `az vm list-usage` para encontrar as cotas da máquina virtual.

```azurecli
az vm list-usage --location "South Central US"
```

Que retorna:

```azurecli
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

### <a name="powershell"></a>PowerShell

No PowerShell, use o comando **Get-AzVMUsage** para encontrar as cotas da máquina virtual.

```powershell
Get-AzVMUsage -Location "South Central US"
```

Que retorna:

```powershell
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>Solução

Para solicitar um aumento de cota, vá ao portal e registre um problema de suporte. No problema de suporte, solicite um aumento da sua cota para a região na qual você deseja implantar.

> [!NOTE]
> Lembre-se de que, para grupos de recursos, a cota é para cada região individual, não para a assinatura inteira. Se você precisar implantar 30 núcleos no Oeste dos EUA, será necessário pedir 30 núcleos do Gerenciador de Recursos no Oeste dos EUA. Se precisar implantar 30 núcleos em qualquer uma das regiões às quais tenha acesso, você deverá solicitar 30 núcleos do Resource Manager em todas as regiões.
>
>

1. Selecione **Assinaturas**.

   ![Assinaturas](./media/resource-manager-quota-errors/subscriptions.png)

2. Selecione a assinatura que precisa de uma cota maior.

   ![Escolha a assinatura](./media/resource-manager-quota-errors/select-subscription.png)

3. Selecione **Uso + cotas**

   ![Selecione uso e cotas](./media/resource-manager-quota-errors/select-usage-quotas.png)

4. No canto superior direito, selecione **Solicitar Aumento**.

   ![Solicitar Aumento](./media/resource-manager-quota-errors/request-increase.png)

5. Preencha os formulários para o tipo de cota que você precisa aumentar.

   ![Preencher o formulário](./media/resource-manager-quota-errors/forms.png)