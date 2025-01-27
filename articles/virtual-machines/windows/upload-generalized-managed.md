---
title: Criar uma VM do Azure gerenciada a partir de um VHD local generalizado | Microsoft Docs
description: Carregue um VHD generalizado no Azure e use-o para criar novas VMs no modelo de implantação do Gerenciador de recursos.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/25/2018
ms.author: cynthn
ms.openlocfilehash: 6382a39e67805eb9bddb356a7b76205a82f3f7c2
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553457"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Carregar um VHD generalizado e usá-lo para criar novas VMs no Azure

Este artigo explica como usar o PowerShell para carregar um VHD de uma VM generalizada para o Azure, criar uma imagem do VHD e criar uma nova VM a partir dessa imagem. Você pode carregar um VHD exportado de uma ferramenta de virtualização local ou de outra nuvem. O uso de [Managed disks](managed-disks-overview.md) para a nova VM simplifica o gerenciamento de VM e fornece melhor disponibilidade quando a VM é colocada em um conjunto de disponibilidade. 

Para obter um script de exemplo, consulte [script de exemplo para carregar um VHD no Azure e criar uma nova VM](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md).

## <a name="before-you-begin"></a>Antes de começar

- Antes de carregar qualquer VHD no Azure, você deve seguir [preparar um VHD do Windows ou VHDX para carregar no Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Examine [o plano para a migração para Managed disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) antes de iniciar a migração para [Managed disks](managed-disks-overview.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]


## <a name="generalize-the-source-vm-by-using-sysprep"></a>Generalizar a VM de origem usando o Sysprep

O Sysprep remove todas as informações pessoais da sua conta, entre outras coisas, e prepara o computador para ser usado como uma imagem. Para obter detalhes sobre o Sysprep, consulte a [visão geral do Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Verifique se as funções de servidor em execução no computador são suportadas pelo Sysprep. Para obter mais informações, consulte [Suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

> [!IMPORTANT]
> Se você planeja executar o Sysprep antes de carregar o VHD no Azure pela primeira vez, verifique se você [preparou sua VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
> 
> 

1. Entre na máquina virtual Windows.
2. Abra a janela do prompt de comando como administrador. Altere o diretório para%WINDIR%\system32\sysprep e execute `sysprep.exe`.
3. Na caixa de diálogo **ferramenta de preparação do sistema** , selecione entrar no **OOBE (experiência inicial do sistema)** e verifique se a caixa de seleção **generalizar** está habilitada.
4. Para **Opções de desligamento**, selecione **desligar**.
5. Selecione **OK**.
   
    ![Inicie o Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Quando o Sysprep é concluído, ele desliga a máquina virtual. Não reinicie a VM.


## <a name="upload-the-vhd-to-your-storage-account"></a>Carregar o VHD em sua conta de armazenamento

Agora você pode carregar um VHD diretamente em um disco gerenciado. Para obter instruções, consulte [carregar um VHD no Azure usando Azure PowerShell](disks-upload-vhd-to-managed-disk-powershell.md).


## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Criar uma imagem gerenciada do VHD carregado 

Crie uma imagem gerenciada do seu disco de gerenciamento generalizado do sistema operacional. Substitua os seguintes valores por suas próprias informações.


Primeiro, defina alguns parâmetros:

```powershell
$location = "East US" 
$imageName = "myImage"
```

Crie a imagem usando o VHD do sistema operacional generalizado.

```powershell
$imageConfig = New-AzImageConfig `
   -Location $location
$imageConfig = Set-AzImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```


## <a name="create-the-vm"></a>Criar a VM

Agora que você tem uma imagem, você pode criar uma ou mais novas VMs a partir da imagem. Este exemplo cria uma VM chamada *myVM* a partir de *MyImage*, no *MyResource*.


```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -ImageName $imageName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Próximos passos

Entre em sua nova máquina virtual. Para obter mais informações, veja [Como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

