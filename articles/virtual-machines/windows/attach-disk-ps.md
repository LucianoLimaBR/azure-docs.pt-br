---
title: Anexar um disco de dados a uma VM do Windows no Azure usando o PowerShell | Microsoft Docs
description: Como anexar um disco de dados novo ou existente a uma VM do Windows usando o PowerShell com o modelo de implantação do Resource Manager.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: ebdaa9aafa884a266ef8cf8d75d1d5fb326c690e
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72300660"
---
# <a name="attach-a-data-disk-to-a-windows-vm-with-powershell"></a>Anexar um disco de dados a uma VM do Windows com o PowerShell

Este artigo mostra como anexar discos novos e existentes a uma máquina virtual do Windows usando o PowerShell. 

Primeiro, leia estas dicas:

* O tamanho da máquina virtual controla quantos discos de dados você pode anexar a ela. Para obter mais informações, confira [Tamanhos das máquinas virtuais](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para usar os SSDs premium, você precisará de um [tipo de VM ativado para armazenamento premium](sizes-memory.md), como a máquina virtual da série DS ou da série GS.

Este artigo usa o PowerShell dentro do [Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), que é constantemente atualizado para a versão mais recente. Para abrir o Cloud Shell, selecione **Experimente** na parte superior de qualquer bloco de código.

## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>Adicionar um disco de dados vazio a uma máquina virtual

Este exemplo mostra como adicionar um disco de dados vazio a uma máquina virtual existente.

### <a name="using-managed-disks"></a>Usar discos gerenciados

```azurepowershell-interactive
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US' 
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-managed-disks-in-an-availability-zone"></a>Usando discos gerenciados em uma zona de disponibilidade

Para criar um disco em uma zona de disponibilidade, use [New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig) com o parâmetro `-Zone`. O exemplo a seguir cria um disco na zona *1*.

```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'East US 2'
$storageType = 'Premium_LRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $location -CreateOption Empty -DiskSizeGB 128 -Zone 1
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

### <a name="initialize-the-disk"></a>Inicializar o disco

Depois de adicionar um disco vazio, você precisará inicializá-lo. Para inicializar o disco, você pode entrar em uma VM e usar o gerenciamento de disco. Se você ativou o [WinRM](https://docs.microsoft.com/windows/desktop/WinRM/portal) e um certificado na VM ao criá-lo, poderá usar o PowerShell remoto para inicializar o disco. Você também pode usar uma extensão de script personalizado:

```azurepowershell-interactive
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```

O arquivo de script pode conter código para inicializar os discos, por exemplo:

```azurepowershell-interactive
    $disks = Get-Disk | Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { [char]$_ }
    $count = 0
    $labels = "data1","data2"

    foreach ($disk in $disks) {
        $driveLetter = $letters[$count].ToString()
        $disk | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] -Confirm:$false -Force
    $count++
    }
```

## <a name="attach-an-existing-data-disk-to-a-vm"></a>Anexar um disco de dados existente a uma VM

Você pode anexar um disco gerenciado existente a uma VM como um disco de dados.

```azurepowershell-interactive
$rgName = "myResourceGroup"
$vmName = "myVM"
$location = "East US" 
$dataDiskName = "myDisk"
$disk = Get-AzDisk -ResourceGroupName $rgName -DiskName $dataDiskName 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzVMDataDisk -CreateOption Attach -Lun 0 -VM $vm -ManagedDiskId $disk.Id

Update-AzVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>Próximas etapas

Você também pode implantar discos gerenciados usando modelos. Para obter mais informações, consulte [usando Managed disks em modelos de Azure Resource Manager](using-managed-disks-template-deployments.md) ou o [modelo de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-data-disk) para implantar vários discos de dados.
