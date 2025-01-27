---
title: Restaurar aplicativos do serviço de aplicativo excluídos-serviço de Azure App
description: Saiba como restaurar um aplicativo do serviço de aplicativo excluído usando o PowerShell.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.service: app-service
ms.openlocfilehash: 7dc3934f486b205febd5be3c0b484dfd2c97bb8f
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755541"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Restaurar o aplicativo do serviço de aplicativo excluído usando o PowerShell

Se você tiver excluído acidentalmente seu aplicativo no serviço Azure App, poderá restaurá-lo usando os comandos do [módulo AZ PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

## <a name="list-deleted-apps"></a>Listar os aplicativos excluídos

Para obter a coleção de aplicativos excluídos, você pode usar `Get-AzDeletedWebApp`.

Para obter detalhes sobre um aplicativo específico excluído, você pode usar:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app>
```

As informações detalhadas incluem:

- **DeletedSiteId**: identificador exclusivo para o aplicativo, usado para cenários em que vários aplicativos com o mesmo nome foram excluídos
- **SubscriptionId**: assinatura que contém o recurso excluído
- **Local**: local do aplicativo original
- **ResourceGroupName**: nome do grupo de recursos original
- **Nome**: o nome do aplicativo original.
- **Slot**: o nome do slot.
- **Hora da exclusão**: quando o aplicativo foi excluído  

## <a name="restore-deleted-app"></a>Restaurar aplicativo excluído

Depois que o aplicativo que você deseja restaurar tiver sido identificado, você poderá restaurá-lo usando `Restore-AzDeletedWebApp`.

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

As entradas para o comando são:

- **Grupo de recursos**: grupo de recursos de destino em que o aplicativo será restaurado
- **Nome**: o nome do aplicativo deve ser globalmente exclusivo.
- **TargetAppServicePlanName**: plano do serviço de aplicativo vinculado ao aplicativo

Por padrão `Restore-AzDeletedWebApp` restaurará a configuração de seu aplicativo, bem como um conteúdo. Se você quiser restaurar apenas o conteúdo, use o sinalizador `-RestoreContentOnly` com esse commandlet.

> [!NOTE]
> Se o aplicativo foi hospedado em e, em seguida, excluído de um Ambiente do Serviço de Aplicativo, ele só poderá ser restaurado se o Ambiente do Serviço de Aplicativo correspondente ainda existir.
>

Você pode encontrar a referência completa do commandlet aqui: [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
