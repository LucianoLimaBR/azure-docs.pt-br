---
title: Erros de SKU do Azure não disponível | Microsoft Docs
description: Descreve como solucionar o erro SKU não disponível ao implantar recursos com o Azure Resource Manager.
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
ms.date: 10/19/2018
ms.author: tomfitz
ms.openlocfilehash: fca028412052a9a1520e1178f5d182a9987a9a85
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390226"
---
# <a name="resolve-errors-for-sku-not-available"></a>Resolva erros de SKU não disponível

Este artigo descreve como resolver o erro **SkuNotAvailable**. Caso você não consiga encontrar um SKU adequado na região ou em uma região alternativa que atende às suas necessidades de negócios, envie uma [solicitação de SKU](https://aka.ms/skurestriction) para o Suporte do Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>Sintoma

Ao implantar um recurso (normalmente, uma máquina virtual), você recebe o seguinte código de erro e a mensagem de erro:

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

## <a name="cause"></a>Causa

Você recebe esse erro quando o recurso SKU selecionado (como o tamanho da VM) não está disponível para o local escolhido.

## <a name="solution-1---powershell"></a>Solução 1: PowerShell

Para determinar quais SKUs estão disponíveis em uma região, use o comando [Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku). Filtre os resultados por local. Você deve ter a versão mais recente do PowerShell para esse comando.

```azurepowershell-interactive
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

Os resultados incluem uma lista de SKUs para o local e as restrições desse SKU. Observe que um SKU pode ser listado como `NotAvailableForSubscription`.

```powershell
ResourceType          Name        Locations   Restriction                      Capability           Value
------------          ----        ---------   -----------                      ----------           -----
virtualMachines       Standard_A0 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   20480
virtualMachines       Standard_A1 centralus   NotAvailableForSubscription      MaxResourceVolumeMB   71680
virtualMachines       Standard_A2 centralus   NotAvailableForSubscription      MaxResourceVolumeMB  138240
```

## <a name="solution-2---azure-cli"></a>Solução 2: CLI do Azure

Para determinar quais SKUs estão disponíveis em uma região, use o comando `az vm list-skus`. Use o parâmetro `--location` para filtrar a saída para o local que você está usando. Use o parâmetro `--size` para procurar por um nome de tamanho parcial.

```azurecli-interactive
az vm list-skus --location southcentralus --size Standard_F --output table
```

O comando retorna resultados como:

```azurecli
ResourceType     Locations       Name              Zones    Capabilities    Restrictions
---------------  --------------  ----------------  -------  --------------  --------------
virtualMachines  southcentralus  Standard_F1                ...             None
virtualMachines  southcentralus  Standard_F2                ...             None
virtualMachines  southcentralus  Standard_F4                ...             None
...
```


## <a name="solution-3---azure-portal"></a>Solução 3: Portal do Azure

Para determinar quais SKUs estão disponíveis em uma região, use o [portal](https://portal.azure.com). Entre no portal e adicione um recurso por meio da interface. Ao definir os valores, verá os SKUs disponíveis para esse recurso. Você não precisa concluir a implantação.

Por exemplo, inicie o processo de criação de uma máquina virtual. Para ver outro tamanho disponível, selecione **Alterar tamanho**.

![Criar VM](./media/resource-manager-sku-not-available-errors/create-vm.png)

Você pode filtrar e rolar para ver os tamanhos disponíveis.

![SKUs disponíveis](./media/resource-manager-sku-not-available-errors/available-sizes.png)

## <a name="solution-4---rest"></a>Solução 4: REST

Para determinar quais SKUs estão disponíveis em uma região, use a operação [Resource Skus - List](/rest/api/compute/resourceskus/list).

Ele retorna SKUs e regiões disponíveis no seguinte formato:

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    ...
  ]
}
```

