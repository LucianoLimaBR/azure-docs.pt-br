---
title: 'Serviço de Backup do Azure: restaurar máquinas virtuais usando o portal do Azure'
description: Restaure uma máquina virtual do Azure de um ponto de recuperação usando o portal do Azure
ms.reviewer: geg
author: dcurwin
manager: carmonm
keywords: restaurar o backup; como restaurar; ponto de recuperação;
ms.service: backup
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: dacurwin
ms.openlocfilehash: 759be3691ba44c92033ec71fd031f9c6e47d6cb4
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311906"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Como restaurar dados de VM do Azure no portal do Azure

Este artigo descreve como restaurar os dados da VM do Azure a partir dos pontos de recuperação armazenados em cofres dos Serviços de Recuperação do [Backup do Azure](backup-overview.md).



## <a name="restore-options"></a>Restaurar opções

O Backup do Azure proporciona várias maneiras de restaurar uma VM.

**Opção de restauração** | **Detalhes**
--- | ---
**Criar uma nova VM** | Cria e coloca rapidamente uma VM básica em funcionamento a partir de um ponto de restauração.<br/><br/> Você pode especificar um nome para a VM, selecionar o grupo de recursos e a rede virtual (VNet) na qual ele será colocado e especificar uma conta de armazenamento para a VM restaurada. A nova VM deve ser criada na mesma região que a VM de origem.
**Restaurar disco** | Restaura um disco de VM, que pode ser usado para criar uma nova VM.<br/><br/> O Backup do Azure fornece um modelo para ajudá-lo a personalizar e criar uma VM. <br/><br> O trabalho de restauração gera um modelo que você pode baixar e usar para especificar configurações de VM personalizadas e criar uma VM.<br/><br/> Os discos são copiados para a conta de armazenamento que você especificar.<br/><br/> Como alternativa, você pode anexar o disco a uma VM existente ou criar uma nova VM usando o PowerShell.<br/><br/> Essa opção é útil se você quiser personalizar a VM, adicionar configurações que não existiam no momento do backup ou adicionar configurações que devem ser definidas usando o modelo ou o PowerShell.
**Substituir existente** | Você pode restaurar um disco e usá-lo para substituir um disco na VM existente.<br/><br/> A VM atual deve existir. Se ela tiver sido excluída, essa opção não poderá ser usada.<br/><br/> O backup do Azure tira um instantâneo da VM existente antes de substituir o disco e a armazena no local de preparo que você especificar. Os discos existentes conectados à VM são substituídos pelo ponto de restauração selecionado.<br/><br/> O instantâneo é copiado para o cofre e mantido de acordo com a política de retenção. <br/><br/> Substituir existente tem suporte para VMs gerenciadas não criptografadas. Não há suporte para discos não gerenciados, [VMs generalizadas](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource), ou para VMs [criadas usando imagens personalizadas](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/).<br/><br/> Se o ponto de restauração tem mais ou menos discos do que a VM atual, o número de discos no ponto de restauração reflete apenas a configuração da VM.<br/><br/>


> [!NOTE]
> Você também pode recuperar arquivos e pastas específicos em uma VM do Azure. [Saiba mais](backup-azure-restore-files-from-vm.md).
>
> Se você estiver executando a [versão mais recente](backup-instant-restore-capability.md) do Backup do Azure para VMs do Azure (conhecida como Restauração Instantânea), os instantâneos serão mantidos por até sete dias e você poderá restaurar uma VM a partir de instantâneos antes que os dados de backup sejam enviados para o cofre. Se você quiser restaurar uma VM a partir de um backup dos últimos sete dias, será mais rápido restaurar a partir do instantâneo e não do cofre.

## <a name="storage-accounts"></a>Contas de armazenamento

Alguns detalhes sobre contas de armazenamento:

- **Criar VM**: Quando você cria uma nova VM, a VM será colocada na conta de armazenamento que você especificar.
- **Restaurar disco**: Quando você restaura um disco, o disco é copiado para a conta de armazenamento que você especificar. O trabalho de restauração gera um modelo que você pode baixar e usar para especificar configurações de VM personalizadas. Esse modelo é colocado na conta de armazenamento especificada.
- **Substituir disco**: Quando você substitui um disco em uma VM existente, o backup do Azure tira um instantâneo da VM existente antes de substituir o disco. O instantâneo é armazenado no local de preparo (conta de armazenamento) que você especificar. Essa conta de armazenamento é usada para armazenar temporariamente o instantâneo durante o processo de restauração e recomendamos que você crie uma nova conta para fazer isso, que pode ser facilmente removida posteriormente.
- **Local da conta de armazenamento**: A conta de armazenamento deve estar na mesma região que o cofre. Somente essas contas são exibidas. Se não houver nenhuma conta de armazenamento no local, você precisará criar uma.
- **Tipo de armazenamento**: Não há suporte para o armazenamento de BLOBs.
- **Redundância de armazenamento**: Não há suporte para ZRS (Armazenamento com redundância de zona). As informações de replicação e redundância para a conta são mostradas entre parênteses após o nome da conta. 
- **Armazenamento Premium**:
    - Ao restaurar VMs não Premium, as contas de armazenamento Premium não têm suporte.
    - Ao restaurar VMs gerenciadas, não há suporte para contas de armazenamento Premium configuradas com regras de rede.


## <a name="before-you-start"></a>Antes de começar

Para restaurar uma VM (criar uma nova VM), verifique se você tem as [permissões](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) de RBAC (controle de acesso baseado em função) corretas para a operação de restauração de VM.

Se você não tiver permissões, poderá [restaurar um disco](#restore-disks)e, depois que o disco for restaurado, poderá [usar o modelo](#use-templates-to-customize-a-restored-vm) que foi gerado como parte da operação de restauração para criar uma nova VM.



## <a name="select-a-restore-point"></a>Selecione um ponto de restauração

1. No cofre associado à VM que você deseja restaurar, clique em **Itens de backup** > **Máquina virtual do Azure**.
2. Clique em uma VM. Por padrão, os pontos de recuperação dos últimos 30 dias são exibidos no painel da VM. Você pode exibir pontos de recuperação com mais de 30 dias ou filtrar para encontrar pontos de recuperação com base em datas, intervalos de tempo e diferentes tipos de consistência de instantâneos.
3. Para restaurar a VM, clique em **Restaurar VM**.

    ![Ponto de restauração](./media/backup-azure-arm-restore-vms/restore-point.png)

4. Selecione um ponto de restauração para usar na recuperação.

## <a name="choose-a-vm-restore-configuration"></a>Escolha uma configuração de restauração de VM

1. Na **Configuração de restauração**, selecione uma opção de restauração:
    - **Criar novo**: Use essa opção se quiser criar uma nova VM. Você pode criar uma VM com configurações simples ou restaurar um disco e criar uma VM personalizada.
    - **Substituir existente**. Use essa opção se quiser substituir discos em uma VM existente.

        ![Assistente de configuração de restauração](./media/backup-azure-arm-restore-vms/restore-configuration.png)

2. Especifique as configurações para sua opção de restauração selecionada.

## <a name="create-a-vm"></a>Criar uma máquina virtual

Como uma das [opções de restauração](#restore-options), você pode criar uma VM rapidamente com configurações básicas a partir de um ponto de restauração.

1. Em **Restaurar configuração** > **Criar nova** > **Tipo de restauração**, selecione **Criar uma máquina virtual**.
2. Em **nome da máquina virtual**, ESPECIFIQUE uma VM que não exista na assinatura.
3. No **Grupo de recursos**, selecione um grupo de recursos existente para a nova máquina virtual ou crie um novo com um nome exclusivo globalmente. Se você atribuir um nome que já existe, o Azure atribui ao grupo o mesmo nome da VM.
4. Em **Rede virtual**, selecione a rede virtual na qual a VM será colocada. Todas as redes virtuais associadas à assinatura são exibidas. Escolher a sub-rede. A primeira sub-rede será selecionada por padrão.
5. Em **local de armazenamento**, especifique a conta de armazenamento para a VM. [Saiba mais](#storage-accounts).

    ![Assistente de configuração de restauração](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. Em **Restaurar configuração**, selecione **OK**. Em **Restaurar**, clique em **Restaurar** para disparar a operação de restauração.


## <a name="restore-disks"></a>Restaurar discos

Como uma das [opções de restauração](#restore-options), você pode criar um disco a partir de um ponto de restauração. Em seguida, com o disco, você pode fazer o seguinte:

- Use o modelo gerado durante a operação de restauração para personalizar as configurações e disparar a implantação da VM. Você edita as configurações de modelo padrão e envia o modelo para implantação da VM.
- [Anexe discos restaurados](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) a uma VM existente.
- [Crie uma nova VM](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) com base nos discos restaurados usando o PowerShell.

1. Em **Restaurar configuração** > **Criar nova** > **Tipo de restauração**, selecione **Restaurar discos**.
2. No **Grupo de recursos**, selecione um grupo de recursos existente para os discos recuperados ou crie um novo com um nome exclusivo globalmente.
3. Em **Conta de armazenamento**, especifique a conta para a qual copiar os VHDs. [Saiba mais](#storage-accounts).

    ![Configuração de recuperação concluída](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. Em **Restaurar configuração**, selecione **OK**. Em **Restaurar**, clique em **Restaurar** para disparar a operação de restauração.

Quando sua máquina virtual usa discos gerenciados e você seleciona a opção **criar máquina virtual** , o backup do Azure não usa a conta de armazenamento especificada. No caso de **restauração de discos** e **restauração instantânea**, a conta de armazenamento é usada somente para armazenar o modelo. Os discos gerenciados são criados no grupo de recursos especificado.
Quando sua máquina virtual usa discos não gerenciados, eles são restaurados como BLOBs para a conta de armazenamento.

### <a name="use-templates-to-customize-a-restored-vm"></a>Usar modelos para personalizar uma VM restaurada

Depois de restaurar o disco, use o modelo gerado como parte da operação de restauração para personalizar e criar uma nova VM:

1. Abra **Detalhes do Trabalho de Restauração** para o trabalho relevante.

2. Em **Detalhes do Trabalho de Restauração**, selecione **Implantar Modelo** para iniciar a implantação de modelo.

    ![Detalhamento do trabalho de restauração](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. Para personalizar a configuração da VM fornecida no modelo, clique em **Editar modelo**. Se você quiser adicionar mais personalizações, clique em **Editar parâmetros**.
    - [Saiba mais](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template) sobre como implantar recursos a partir de um modelo personalizado.
    - [Saiba mais](../azure-resource-manager/resource-group-authoring-templates.md) sobre como criar modelos.

   ![Carregar a implantação de modelo](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Insira os valores personalizados para a VM, aceite os **Termos e Condições** e clique em **Comprar**.

   ![Enviar a implantação de modelo](./media/backup-azure-arm-restore-vms/submitting-template1.png)


## <a name="replace-existing-disks"></a>Substituir discos existentes

Como uma das [opções de restauração](#restore-options), você pode substituir um disco de VM existente pelo ponto de restauração selecionado. [Analise](#restore-options) todas as opções de restauração.

1. Em **Restaurar configuração**, clique em **Substituir existente**.
2. Em **Tipo de Restauração**, selecione **Substituir disco**. Este é o ponto de restauração que será usado para substituir discos de VM existentes.
3. Em **local de preparo**, especifique onde os instantâneos dos discos gerenciados atuais devem ser salvos durante o processo de restauração. [Saiba mais](#storage-accounts).

   ![Restaurar o Assistente de configuração de substituir existente](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)


## <a name="restore-vms-with-special-configurations"></a>Restaurar VMs com configurações especiais

Há vários cenários comuns nos quais você pode precisar restaurar VMs.

**Cenário** | **Diretrizes**
--- | ---
**Restaurar VMs usando Benefícios de uso híbrido** | Se uma VM do Windows usa o [licenciamento do Benefício de uso híbrido (HUB)](../virtual-machines/windows/hybrid-use-benefit-licensing.md), restaure os discos e crie uma nova VM usando o modelo fornecido (com **Tipo de Licença** definido como **Windows_Server**) ou o PowerShell.  Essa configuração também pode ser aplicada após a criação da VM.
**Restaurar VMs durante um desastre de datacenter do Azure** | Se o cofre usa GRS e o datacenter principal da VM ficar inativo, o Backup do Azure oferece suporte à restauração de VMs de backup no datacenter emparelhado. Você seleciona uma conta de armazenamento no datacenter emparelhado e a restaura normalmente. O backup do Azure usa o serviço de computação na região emparelhada para criar a VM restaurada. [Saiba mais](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md) sobre a resiliência do datacenter.
**Restaurar VM do controlador de domínio único no domínio único** | Restaure a VM como qualquer outra VM. Observe que:<br/><br/> De uma perspectiva Active Directory, a VM do Azure é como qualquer outra VM.<br/><br/> O DSRM (Modo de Restauração dos Serviços de Diretório) também está disponível, portanto, todos os cenários de recuperação do Active Directory são viáveis. [Saiba mais](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/virtualized-domain-controllers-hyper-v) sobre considerações de backup e restauração para controladores de domínio virtualizados.
**Restaurar várias VMs do controlador de domínio em domínio único** | Se outros controladores de domínio no mesmo domínio puderem ser acessados pela rede, o controlador de domínio poderá ser restaurado como qualquer VM. Se for o último controlador de domínio restante no domínio, ou uma recuperação em uma rede isolada for executada, use uma [recuperação de floresta](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Restaurar vários domínios em uma floresta** | Recomendamos uma [recuperação de floresta](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Restauração bare-metal** | A principal diferença entre VMs do Azure e hipervisores locais é que não há nenhum console de VM disponível no Azure. Um console é necessário para determinados cenários, como a recuperação usando um backup do tipo BMR (recuperação bare-metal). No entanto, a restauração de VM do cofre é uma substituição completa para a BMR.
**Restaurar VMs com configurações de rede especiais** | Configurações especiais de rede incluem VMs usando balanceamento de carga interno ou externo, usando vários NICS ou vários endereços IP reservados. Restaure essas VMs usando a [opção de disco de restauração](#restore-disks). Essa opção faz uma cópia dos VHDs na conta de armazenamento especificada e, em seguida, você pode criar uma VM com um balanceador de carga [interno](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/) ou [externo](https://azure.microsoft.com/documentation/articles/load-balancer-internet-getstarted/) , [várias NICs](../virtual-machines/windows/multiple-nics.md)ou [vários endereços IP reservados](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md), de acordo com seu configuração.
**Grupo de segurança de rede (NSG) na NIC/sub-rede** | O backup de VM do Azure dá suporte a backup e restauração de informações de NSG em nível de rede virtual, sub-rede e NIC.
**VMs fixadas pela zona** | O backup do Azure dá suporte ao backup e à restauração de VMs fixadas de zona. [Saiba mais](https://azure.microsoft.com/global-infrastructure/availability-zones/)

## <a name="track-the-restore-operation"></a>Acompanhar a operação de restauração
Após disparar a operação de restauração, o serviço de backup cria um trabalho para acompanhamento. O Backup do Azure exibe notificações sobre o trabalho no portal. Se eles não estiverem visíveis, selecione o símbolo **notificações** e, em seguida, selecione **Exibir todos os trabalhos** para ver o status do processo de restauração.

![Restauração disparada](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Acompanhe a restauração da seguinte forma:

1. Para ver as operações do trabalho, clique no hiperlink de notificações. Como alternativa, no cofre, clique em **Trabalhos de Backup** e, em seguida, clique na VM relevante.

    ![Lista de VMs em um cofre](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. Para monitorar o progresso da restauração, clique em qualquer tarefa de restauração com o status **In-progress**. Isso exibe a barra de progresso, que exibe informações sobre o progresso da restauração:

    - **Tempo estimado de restauração**: inicialmente fornece o tempo necessário para concluir a operação de restauração. À medida que a operação progride, o tempo gasto reduz e chega a zero quando a operação de restauração é concluída.
    - **Porcentagem de restauração**. Mostra a porcentagem concluída da operação de restauração.
    - **Número de bytes transferidos**: se você estiver restaurando por meio da criação de uma nova VM, mostra os bytes que foram transferidos em relação ao número total de bytes a transferir.

## <a name="post-restore-steps"></a>Etapas pós-restauração

Há vários itens a observar após a restauração de uma VM:

- Extensões presentes durante a configuração do backup serão instaladas, mas não habilitadas. Se encontrar um problema, reinstale as extensões.
- Se a VM de backup tiver um endereço IP estático, a VM restaurada terá um endereço IP dinâmico para evitar conflitos. Você pode [adicionar um endereço IP estático à VM restaurada](../virtual-network/virtual-networks-reserved-private-ip.md#how-to-add-a-static-internal-ip-to-an-existing-vm).
- Uma VM restaurada não tem um conjunto de disponibilidade. Se você usar a opção restaurar disco, poderá [especificar um conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) ao criar uma VM a partir do disco usando o modelo ou o PowerShell fornecido.
- Se você usar uma distribuição Linux baseada em inicialização da nuvem, como o Ubuntu, a senha será bloqueada após a restauração por motivos de segurança. Use uma extensão VMAccess na VM restaurada para [redefinir a senha](../virtual-machines/linux/reset-password.md). Recomendamos o uso de chaves SSH nessas distribuições, para que você não precise redefinir a senha após a restauração.
- Se não for possível acessar a VM após a restauração, devido à VM que está com a relação quebrada com o controlador de domínio, siga as etapas abaixo para abrir a VM:
    - Anexe o disco do sistema operacional como um disco de dados a uma VM recuperada.
    - Instale manualmente o agente de VM se o agente do Azure for considerado sem resposta seguindo este [link](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline).
    - Habilitar o acesso ao console serial na VM para permitir o acesso de linha de comando à VM
    
  ```
    bcdedit /store <drive letter>:\boot\bcd /enum
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<<BOOT LOADER IDENTIFIER>>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
    - Quando a VM é recriada, use portal do Azure para redefinir a conta de administrador local e a senha
    - Use Console serial Access e CMD para desassociar a VM do domínio

    ```
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force" 
    ```

- Depois que a VM for desassociada e reiniciada, você poderá usar o RDP com êxito para a VM com credenciais de administrador local e reingressar a VM de volta ao domínio com êxito.

## <a name="backing-up-restored-vms"></a>Fazer backup de VMs restauradas

- Se você tiver restaurado uma VM no mesmo grupo de recursos e com o mesmo nome que a VM do backup original, o backup continuará na VM após a restauração.
- Se você tiver restaurado a VM para outro grupo de recursos ou tiver especificado um nome diferente para a VM restaurada, é preciso configurar o backup para a VM restaurada.

## <a name="next-steps"></a>Próximas etapas

- Se você tiver dificuldades durante o processo de restauração, [verifique](backup-azure-vms-troubleshoot.md#restore) os problemas e erros comuns.
- Depois de restaurar a VM, aprenda mais sobre o [gerenciamento de máquinas virtuais](backup-azure-manage-vms.md).
