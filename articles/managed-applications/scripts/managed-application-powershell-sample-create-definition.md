---
title: Exemplo de script do Azure PowerShell – Criar uma definição de aplicativo gerenciado | Microsoft Docs
description: Fornece uma amostra de script do Azure PowerShell que cria uma definição de aplicativo gerenciado na assinatura do Azure.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: f0491a5025886317faf7cb4601f8db3915527b80
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332590"
---
# <a name="create-a-managed-application-definition-with-powershell"></a>Crie uma definição de aplicativo gerenciado com o PowerShell

Este script publica uma definição de aplicativo gerenciado em um catálogo de serviços.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/managed-applications/create-definition/create-definition.ps1 "Create definition")]


## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para implantar a definição de aplicativo gerenciado. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzManagedApplicationDefinition](https://docs.microsoft.com/powershell/module/az.resources/new-azmanagedapplicationdefinition) | Crie uma definição de aplicativo gerenciado. Forneça o pacote que contém os arquivos necessários. |


## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução aos aplicativos gerenciados, consulte [Visão geral de aplicativos gerenciados do Azure](../overview.md).
* Para obter mais informações sobre o PowerShell, confira a [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
