---
title: Tutorial – Alta disponibilidade para VMs Linux no Azure | Microsoft Docs
description: Neste tutorial, você aprenderá como usar a CLI do Azure para implantar máquinas virtuais altamente disponíveis em Conjuntos de Disponibilidade
documentationcenter: ''
services: virtual-machines-linux
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: tutorial
ms.date: 08/24/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 10458e3c5f1e4dc9034206470fdfec19e13417fb
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299440"
---
# <a name="tutorial-create-and-deploy-highly-available-virtual-machines-with-the-azure-cli"></a>Tutorial: Criar e implantar máquinas virtuais altamente disponíveis com a CLI do Azure

Neste tutorial, você aprenderá a aumentar a disponibilidade e a confiabilidade de suas soluções de Máquina Virtual no Azure usando uma funcionalidade chamada Conjuntos de Disponibilidade. Os Conjuntos de disponibilidade garantem que as VMs implantadas no Azure sejam distribuídas entre vários clusters de hardware isolados. Isso garante que, se ocorrer uma falha de hardware ou de software no Azure, apenas um subconjunto de suas VMs será afetado e a solução geral permanecerá disponível e operacional.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um conjunto de disponibilidade
> * Criar uma VM em um conjunto de disponibilidade
> * Verificar os tamanhos de VM disponíveis

Este tutorial usa a CLI dentro do [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), que é constantemente atualizada para a versão mais recente. Para abrir o Cloud Shell, selecione **Experimentar** na parte superior de um bloco de código qualquer.

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você execute a CLI do Azure versão 2.0.30 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="high-availability-in-azure-overview"></a>Visão geral de alta disponibilidade no Azure
A alta disponibilidade no Azure pode ser criada de várias maneiras diferentes. Duas opções que você tem são conjuntos de disponibilidade e zonas de disponibilidade. Usando conjuntos de disponibilidade, suas VMs serão protegidas contra falhas que podem ocorrer em um datacenter. Isso inclui falhas de hardware e falhas de software do Azure. Usando zonas de disponibilidade, as VMs serão colocadas em infraestrutura fisicamente separada sem recursos compartilhados e, portanto, serão protegidas de falhas no datacenter inteiro.

Use Conjuntos de Disponibilidade ou Zonas de Disponibilidade quando você desejar implantar soluções confiáveis baseadas em VM no Azure.

### <a name="availability-set-overview"></a>Visão geral do conjunto de disponibilidade

Um Conjunto de disponibilidade é uma funcionalidade de agrupamento lógico que você pode usar no Azure para garantir que os recursos da VM colocados nele sejam isolados uns dos outros quando forem implantados em um datacenter do Azure. O Azure garante que as VMs colocadas em um Conjunto de disponibilidade sejam executadas em vários servidores físicos, racks de computação, unidades de armazenamento e comutadores de rede. Se uma falha de hardware ou software do Azure ocorrer, apenas um subconjunto de suas VMs será afetado e seu aplicativo geral permanecerá disponível e ativo para seus clientes. Os Conjuntos de Disponibilidade são uma funcionalidade essencial quando você deseja compilar soluções de nuvem confiáveis.

Vamos considerar uma solução comum baseada em VM na qual você pode ter quatro servidores Web front-end e usar duas VMs de back-end que hospedam um banco de dados. Com o Azure, convém definir dois conjuntos de disponibilidade antes de implantar suas VMs: um conjunto de disponibilidade para a camada "Web" e um conjunto de disponibilidade para a camada "banco de dados". Ao criar uma nova VM, você pode especificar o conjunto de disponibilidade como um parâmetro para o comando az vm create e o Azure garantirá automaticamente que as VMs criadas dentro do conjunto de disponibilidade sejam isoladas em vários recursos de hardware físico. Se o hardware físico no qual um de seus servidores Web ou VMs do servidor de banco de dados estiverem em execução enfrentar um problema, você saberá que outras instâncias de seu servidor Web e VMs de banco de dados permanecerão em execução, pois estão em um hardware diferente.

### <a name="availability-zone-overview"></a>Visão geral de zona de disponibilidade

Zonas de Disponibilidade é uma oferta de alta disponibilidade que protege os aplicativos e dados contra falhas do datacenter. As Zonas de Disponibilidade são locais físicos exclusivos em uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas. A separação física das Zonas de Disponibilidade dentro de uma região protege os aplicativos e dados contra falhas do datacenter. Serviços com redundância de zona replicam os aplicativos e dados entre Zonas de Disponibilidade para proteger dos pontos únicos de falha. Com Zonas de Disponibilidade, o Azure oferece o melhor SLA de tempo de atividade da VM de 99,99% do setor.

De modo semelhante aos conjuntos de disponibilidade, vamos considerar uma solução comum baseada em VM na qual você pode ter quatro servidores Web front-end e usar duas VMs de back-end que hospedam um banco de dados. De modo semelhante aos conjuntos de disponibilidade, você desejará implantar suas VMs em duas zonas de disponibilidade separadas: uma zona de disponibilidade para a camada "Web" e outra zona de disponibilidade para a camada "banco de dados". Quando você cria uma VM e especifica a zona de disponibilidade como um parâmetro para o comando az vm create, o Azure garante automaticamente que as VMs criadas sejam isoladas entre zonas de disponibilidade totalmente diferentes. Se todo o datacenter no qual um de seus servidores Web ou VMs do servidor de banco de dados estiverem em execução enfrentar um problema, você saberá que outras instâncias de seu servidor Web e VMs de banco de dados permanecerão em execução, pois estarão em execução em datacenters totalmente separados.

## <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade

Crie um conjunto de disponibilidade usando [az vm availability-set create](/cli/azure/vm/availability-set). Nesse exemplo, o número de domínios de atualização e de falha são definidos para *2* para o conjunto de disponibilidade chamado *myAvailabilitySet* no grupo de recursos *myResourceGroupAvailability*.

Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group#az-group-create), em seguida, crie um conjunto de disponibilidade:

```azurecli-interactive
az group create --name myResourceGroupAvailability --location eastus

az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Os Conjuntos de Disponibilidade permitem que você isole os recursos em "domínios de falha" e "domínios de atualização". Um **domínio de falha** representa uma coleção isolada de recursos de servidor + rede + armazenamento. No exemplo anterior, o conjunto de disponibilidade em pelo menos dois domínios de falha quando nossas VMs são implantadas. O conjunto de disponibilidade também é distribuído entre dois **atualizar domínios**. Dois domínios de atualização garantem que durante a atualização de software do Azure os recursos de VM estarão isolados, impedindo que todos os softwares que executem em nossa VM sejam atualizados ao mesmo tempo.


## <a name="create-vms-inside-an-availability-set"></a>Criar VMs dentro de um conjunto de disponibilidade

As VMs devem ser criadas dentro do conjunto de disponibilidade para assegurar a distribuição correta pelo hardware. Uma VM existente não pode ser adicionada a um conjunto de disponibilidade após sua criação.

Quando uma VM é criada com [az vm create](/cli/azure/vm), você usa o parâmetro `--availability-set` para especificar o nome do conjunto de disponibilidade.

```azurecli-interactive
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --vnet-name myVnet \
     --subnet mySubnet \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys
done
```

Agora há duas máquinas virtuais dentro do conjunto de disponibilidade. Como elas estão no mesmo conjunto de disponibilidade, o Azure garantirá que as VMs e todos os seus recursos (incluindo discos de dados) sejam distribuídos entre o hardware físico isolado. Essa distribuição ajuda a garantir uma disponibilidade muito maior de nossa solução de VM geral.

A distribuição do conjunto de disponibilidade pode ser exibida no portal, vá para grupos de recursos > myResourceGroupAvailability > myAvailabilitySet. As VMs são distribuídas entre as duas falhas e domínios de atualização, conforme mostrado no exemplo a seguir:

![Conjunto de disponibilidade no portal](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Conferir os tamanhos de VM disponíveis

VMs adicionais podem ser adicionadas ao conjunto de disponibilidade mais tarde, onde os tamanhos de VM estão disponíveis no hardware. Use [az vm availability-set list-sizes](/cli/azure/vm/availability-set#az-vm-availability-set-list-sizes) para listar todos os tamanhos disponíveis no cluster de hardware para o conjunto de disponibilidade:

```azurecli-interactive
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar um conjunto de disponibilidade
> * Criar uma VM em um conjunto de disponibilidade
> * Verificar os tamanhos de VM disponíveis

Avance para o próximo tutorial para saber mais sobre conjuntos de disponibilidade de máquinas virtuais.

> [!div class="nextstepaction"]
> [Criar um conjunto de dimensionamento de máquinas virtuais](tutorial-create-vmss.md)

* Para saber mais sobre as zonas de disponibilidade, confira a [Documentação das Zonas de Disponibilidade](../../availability-zones/az-overview.md).
* Mais documentação sobre conjuntos de disponibilidade e Zonas de Disponibilidade também está disponível [aqui](./manage-availability.md).
* Para experimentar zonas de disponibilidade, visite [Criar uma máquina virtual do Linux em uma zona de disponibilidade com a CLI do Azure](./create-cli-availability-zone.md)
