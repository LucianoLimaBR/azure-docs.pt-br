---
title: Habilitar Azure Disk Encryption para conjuntos de dimensionamento de máquinas virtuais
description: Este artigo fornece instruções sobre como habilitar a criptografia de disco Microsoft Azure para conjuntos de dimensionamento de máquinas virtuais
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: article
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 69197b9c0d2a60e1833d03a3f12802221a8307e2
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530830"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Azure Disk Encryption para conjuntos de dimensionamento de máquinas virtuais

O Azure Disk Encryption fornece criptografia de volume para o sistema operacional e os discos de dados de suas máquinas virtuais, ajudando a proteger e proteger seus dados para atender aos compromissos de segurança e conformidade organizacionais. Para saber mais, confira [Azure Disk Encryption: VMs](../virtual-machines/linux/disk-encryption-overview.md) e [Azure Disk Encryption do Linux: VMs do Windows](../virtual-machines/windows/disk-encryption-overview.md)  

Azure Disk Encryption também pode ser aplicado a conjuntos de dimensionamento de máquinas virtuais Windows e Linux, nessas instâncias:
- Conjuntos de dimensionamento criados com discos gerenciados. Não há suporte para a criptografia de disco do Azure para conjuntos de dimensionamento de disco nativos (ou não gerenciados).
- SO e volumes de dados em conjuntos de dimensionamento do Windows.
- Volumes de dados em conjuntos de dimensionamento do Linux. A criptografia de disco do so não tem suporte no momento para conjuntos de dimensionamento do Linux.

Você pode aprender os conceitos básicos de Azure Disk Encryption para conjuntos de dimensionamento de máquinas virtuais em apenas alguns minutos com a [criptografia de um conjunto de dimensionamento de máquinas virtuais usando o CLI do Azure](disk-encryption-cli.md) ou os [conjuntos de dimensionamento de máquinas virtuais criptografados usando os tutoriais de CLI do Azure](disk-encryption-powershell.md) .

## <a name="next-steps"></a>Próximos passos

- [Criptografar conjuntos de dimensionamento de máquinas virtuais usando o Azure Resource Manager](disk-encryption-azure-resource-manager.md)
- [Criar e configurar um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md)
- [Usar Azure Disk Encryption com sequenciamento de extensão do conjunto de dimensionamento de máquinas virtuais](disk-encryption-extension-sequencing.md)