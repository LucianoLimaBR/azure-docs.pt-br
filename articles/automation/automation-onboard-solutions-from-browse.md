---
title: Saiba como integrar as soluções de gerenciamento de atualizações, controle de alterações e inventário para várias VMs na automação do Azure
description: Saiba como integrar uma Máquina Virtual do Azure com soluções de Gerenciamento de Atualizações, Controle de Alterações e Inventário que fazem parte da Automação do Azure
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/11/2019
ms.topic: article
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 3e56b44988dc6dbfed99f339795fee6d15c7dd57
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72372795"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Habilitar o gerenciamento de atualizações, o controle de alterações e as soluções de inventário em várias VMs

A Automação do Azure fornece soluções para gerenciar atualizações de segurança do sistema operacional, controlar alterações e inventariar o que está instalado em seus computadores. Há várias maneiras para integrar máquinas, você pode integrar a solução [de uma máquina virtual](automation-onboard-solutions-from-vm.md), da sua [conta de automação](automation-onboard-solutions-from-automation-account.md), durante a navegação de máquinas virtuais ou pelo [runbook](automation-onboard-solutions.md). Este artigo aborda essas soluções de integração ao procurar máquinas virtuais no Azure.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Azure em https://portal.azure.com

## <a name="enable-solutions"></a>Habilitar soluções

No portal do Azure, navegue até **máquinas virtuais**.

Usando as caixas de seleção, selecione as máquinas virtuais que você deseja integrar com o controle de alterações e inventário ou de gerenciamento de atualização. A integração está disponível para até três grupos de recursos diferente cada vez. As VMs do Azure podem existir em qualquer região, independentemente do local da sua conta de automação.

![Lista de VMs](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> Use os controles de filtro para modificar a lista de máquinas virtuais e, em seguida, clique na caixa de seleção superior para selecionar todas as máquinas virtuais na lista.

Na barra de comandos, clique em **serviços** e selecione **controle de alterações**, **inventário**, ou **Update Management**.

> [!NOTE]
> **Controle de alterações** e **Inventário** usam a mesma solução, quando um está habilitado, o outro também está habilitado.

A imagem a seguir é para o gerenciamento de atualizações. O controle de alterações e inventário têm o mesmo layout e comportamento.

A lista de máquinas virtuais é filtrada para mostrar somente as máquinas virtuais que estão na mesma assinatura e local. Se suas máquinas virtuais estiverem em mais de três grupos de recursos, os três primeiros grupos de recursos serão selecionados.

### <a name="resource-group-limit"></a> Limitações de integração

O número de grupos de recursos que você pode usar para a integração é limitado pelos [limites de implantação do Resource Manager](../azure-resource-manager/resource-manager-cross-resource-group-deployment.md). Implantações do Gerenciador de recursos, não devem ser confundidas com implantações de atualização, são limitadas a cinco grupos de recursos por implantação. Para garantir a integridade da integração, dois desses grupos de recursos são reservados para configurar o espaço de trabalho do Log Analytics, a conta de Automação e os recursos relacionados. Isso deixa você com três grupos de recursos para selecionar para a implantação. Esse limite se aplica apenas à integração simultânea, não ao número de grupos de recursos que podem ser gerenciados por uma solução de automação.

Você também pode usar um runbook para integração, para obter mais informações, consulte [soluções integradas de atualização e controle de alterações para a automação do Azure](automation-onboard-solutions.md).

Use os controles de filtro para selecionar máquinas virtuais em assinaturas diferentes, locais e grupos de recursos.

![Solução integrada de gerenciamento de atualização](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Examine as opções para o espaço de trabalho Log Analytics e a conta de automação. Um workspace existente e a conta de Automação são selecionados por padrão. Se desejar usar um espaço de trabalho do Log Analytics diferente e a Conta de Automação, clique em **PERSONALIZAR** para selecioná-los na página **Configuração personalizada**. Quando você escolhe um espaço de trabalho do Log Analytics, é feita uma verificação para determinar se ele está vinculado a uma Conta de Automação. Se uma Conta de Automação vinculada for encontrada, você verá a tela a seguir. Quando terminar, clique em **OK**.

![Selecione workspace e conta](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Se o workspace selecionado não estiver vinculado a uma Conta de Automação, você verá a tela a seguir. Selecione uma Conta de Automação e clique em **OK** ao concluir.

![Nenhum workspace](media/automation-onboard-solutions-from-browse/no-workspace.png)

> [!NOTE]
> Ao habilitar soluções, somente determinadas regiões têm suporte para vincular um espaço de trabalho do Log Analytics e uma Conta de Automação.
>
> Para obter uma lista dos pares de mapeamento com suporte, confira [mapeamento de região para conta de automação e espaço de trabalho de log Analytics](how-to/region-mappings.md).

Desmarque a caixa de seleção ao lado de qualquer máquina virtual que você não deseja habilitar. Máquinas virtuais que não podem ser habilitadas já estão desmarcadas.

Clique em **Ativar** para ativar a solução. A solução demora até 15 minutos para habilitar.

## <a name="unlink-workspace"></a>Desvincular o workspace

As seguintes soluções são dependentes de um espaço de trabalho do Log Analytics:

* [Gerenciamento de atualizações](automation-update-management.md)
* [Controle de alterações](automation-change-tracking.md)
* [Iniciar/parar VMs durante os horários fora de pico](automation-solution-vm-management.md)

Se você decidir que não deseja mais integrar sua conta de automação a um espaço de trabalho Log Analytics, você poderá desvincular sua conta diretamente da portal do Azure. Antes de prosseguir, você precisa remover as soluções mencionadas anteriormente, caso contrário, esse processo será impedido de continuar. Examine o artigo sobre a solução específica que você importou para entender as etapas necessárias para removê-la.

Depois de remover essas soluções, você poderá executar as etapas a seguir para desvincular sua conta de Automação.

> [!NOTE]
> Algumas soluções, incluindo versões anteriores da solução de monitoramento do Azure SQL, podem ter criado ativos de automação e também podem precisar ser removidas antes de desvincular o workspace.

1. No portal do Azure, abra sua conta da Automação e, na página da conta da Automação, selecione **Workspace vinculado** na seção **Recursos Relacionados** à esquerda.

2. Na página Desvincular o workspace, clique em **Desvincular o workspace**.

   ![Página Desvincular workspace](media/automation-onboard-solutions-from-browse/automation-unlink-workspace-blade.png).

   Você receberá uma solicitação perguntando se deseja prosseguir.

3. Enquanto a Automação do Azure tenta desvincular a conta do seu espaço de trabalho do Log Analytics, você pode acompanhar o progresso no menu **Notificações**.

Se você tiver usado a solução Gerenciamento de Atualizações, como opção, convém remover os itens a seguir que não serão mais necessários após a remoção da solução.

* Atualizar agendas - Cada uma terá nomes que correspondam às implantações de atualizações que você criou)

* Grupos de trabalho híbrido criados para a solução - Cada um receberá um nome semelhante a machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Se você tiver usado a solução Iniciar/parar VMs durante os horários fora de pico, como opção, convém remover os itens a seguir que não serão mais necessários após a remoção da solução.

* Iniciar e parar agendas de runbook da VM
* Iniciar e parar runbooks da VM
* variáveis

Como alternativa, você também pode desvincular seu espaço de trabalho de sua conta de automação do seu espaço de trabalho Log Analytics. Em seu espaço de trabalho, selecione **conta de automação** em **recursos relacionados**. Na página conta de automação, selecione **desvincular conta**.

## <a name="troubleshooting"></a>Solução de Problemas

Ao integrar várias máquinas, pode haver máquinas que mostrem como **Não é possível ativar**. Há diferentes razões que algumas máquinas não podem ser ativadas. As seções a seguir mostram possíveis razões para o estado **Impossível ativar** em uma VM ao tentar integrar.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>Relatórios de VM para outro workspace: '\<workspaceName\>'.  Alterar a configuração para usá-lo para ativar

**Causa**: este erro mostra a máquina virtual que você está tentando carregar relatórios para outro workspace.

**Solução**: Clique em **Usar como configuração** para alterar o espaço de trabalho do Log Analytics e da conta de Automação.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>Relatórios VM para um workspace que não está disponível nesta assinatura

**Causa**: O workspace que se reporta a máquina virtual:

* Está em uma assinatura diferente ou
* Não existe mais, ou
* Está em um grupo de recursos que você não tem as permissões de acesso

**Solução**: localizar a conta de automação associada com o workspace que se reporta a VM e integrar a máquina virtual, alterando a configuração do escopo.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>A versão ou distribuição do sistema operacional VM não é suportada

**Causa:** A solução não é suportada em todas as distribuições do Linux ou em todas as versões do Windows.

**Solução:** consulte a [lista de clientes com suporte](automation-update-management.md#clients) para a solução.

### <a name="classic-vms-cannot-be-enabled"></a>VMs clássicas não podem ser habilitadas

**Causa**: máquinas virtuais que usam o modelo de implantação clássico não são suportadas.

**Solução**: migre a máquina virtual para o modelo de implantação do Gerenciador de recursos. Para aprender como fazer isso, consulte [ Migrar recursos do modelo de implantação clássico ](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

### <a name="vm-is-stopped-deallocated"></a>A VM está parada. (deslocada)

**Causa**: a máquina virtual não está no estado **Em Execução**.

**Solução**: para integrar uma VM a uma solução, a VM deve estar em execução. Clique no link embutido **Iniciar VM** para iniciar a VM sem sair da página.

## <a name="clean-up-resources"></a>Limpar recursos

Para remover uma VM do Gerenciamento de Atualizações:

* No espaço de trabalho do Log Analytics, remova a VM da pesquisa salva para a Configuração de Escopo `MicrosoftDefaultScopeConfig-Updates`. As pesquisas salvas podem ser encontradas em **Geral** no workspace.
* Remover o [agente do Microsoft Monitoring](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) ou o [agente do Log Analytics para Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Próximos passos

Agora que a solução está habilitada para suas máquinas virtuais, visite o artigo Gerenciamento de Atualizações visão geral para saber como criar uma **implantação de atualização** para seus computadores.

> [!div class="nextstepaction"]
> [Gerenciamento de Atualizações-gerenciar atualizações e patches para suas VMs do Azure](./automation-tutorial-update-management.md)

Tutoriais de adição sobre as soluções e como usá-los:

* [Tutorial - Gerenciar as atualizações de sua VM](automation-tutorial-update-management.md)

* [Tutorial - Identificar o software em uma VM](automation-tutorial-installed-software.md)

* [Tutorial - Solucionar problemas de alterações em uma VM](automation-tutorial-troubleshoot-changes.md)
