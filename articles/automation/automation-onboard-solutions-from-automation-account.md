---
title: Saiba como integrar soluções de Gerenciamento de Atualizações, Controle de Alterações e Inventário na Automação do Azure
description: Saiba como integrar uma Máquina Virtual do Azure com soluções de Gerenciamento de Atualizações, Controle de Alterações e Inventário que fazem parte da Automação do Azure
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 4/11/2019
ms.topic: conceptual
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: e117c6f8aa8526392678f37a05ec61b55983a1c7
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374432"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Integrar soluções de Gerenciamento de Atualizações, Controle de Alterações e Inventário

A Automação do Azure fornece soluções para gerenciar atualizações de segurança do sistema operacional, controlar alterações e inventariar o que está instalado em seus computadores. Há muitas maneiras de integrar as máquinas, você pode integrar a solução [de uma máquina virtual](automation-onboard-solutions-from-vm.md), [navegando em várias máquinas](automation-onboard-solutions-from-browse.md), da sua conta de automação ou pelo [runbook](automation-onboard-solutions.md). Este artigo aborda a integração dessas soluções em sua conta de Automação.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Azure em https://portal.azure.com

## <a name="enable-solutions"></a>Habilitar soluções

Navegue até sua conta de Automação e selecione **Inventário** ou **Controle de alterações** em **GERENCIAMENTO DE CONFIGURAÇÃO**.

Escolha o espaço de trabalho Log Analytics e a conta de automação e clique em **habilitar** para habilitar a solução. A solução demora até 15 minutos para habilitar.

![Integrar a solução de Inventário](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

> [!NOTE]
> Ao habilitar soluções, somente determinadas regiões têm suporte para vincular um espaço de trabalho do Log Analytics e uma Conta de Automação.
>
> Para obter uma lista dos pares de mapeamento com suporte, confira [mapeamento de região para conta de automação e espaço de trabalho de log Analytics](how-to/region-mappings.md).

A solução de Controle de Alterações e Inventário fornece a capacidade de [acompanhar alterações](automation-vm-change-tracking.md) e [inventário](automation-vm-inventory.md) em suas máquinas virtuais. Nesta etapa, você deve habilitar a solução em uma máquina virtual.

Quando a notificação da integração da solução de inventário e controle de alterações for concluída, clique em **Gerenciamento de Atualizações** sob **GERENCIAMENTO DE CONFIGURAÇÃO**.

A solução de Gerenciamento de Atualizações permite que você gerencie atualizações e patches para suas VMs do Windows do Azure. Você pode avaliar o status de atualizações disponíveis, agendar a instalação de atualizações necessárias e examinar os resultados de implantação para verificar se as atualizações foram aplicadas com êxito na VM. Esta ação habilitou a solução para a VM.

Selecione **Gerenciamento de atualizações** em **GERENCIAMENTO DE ATUALIZAÇÕES**. O espaço de trabalho Log Analytics selecionado é o mesmo espaço de trabalho usado na etapa anterior. Clique em **Habilitar** para integrar a solução de Gerenciamento de atualizações. A solução demora até 15 minutos para habilitar.

![Integrar solução de atualização](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Configuração de Escopo

Cada solução usa uma Configuração de Escopo dentro do workspace para direcionar os computadores que recebem a solução. A Configuração de Escopo é um grupo de uma ou mais pesquisas salvas utilizada para limitar o escopo da solução em computadores específicos. Para acessar as Configurações de Escopo, na sua conta de Automação, em **RECURSOS RELACIONADOS**, selecione **Workspace**. Em seguida, no workspace em **FONTES DE DADOS DO WORKSPACE**, selecione **Configurações de Escopo**.

Se o workspace selecionado ainda não tiver as soluções Gerenciamento de atualizações ou Controle de alterações, as seguintes configurações de escopo serão criadas:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Se o workspace selecionado já tiver a solução, a solução não será reimplantada e a configuração do escopo não será adicionada a ela.

## <a name="saved-searches"></a>Pesquisas salvas

Quando um computador é adicionado às soluções Gerenciamento de Atualizações ou Rastreamento de Mudanças e Inventário, elas são adicionadas a uma das duas pesquisas salvas em seu workspace. Essas pesquisas salvas são consultas que contêm os computadores segmentados para essas soluções.

Navegue para a conta de Automação e selecione **Pesquisas salvas** em **Geral**. As duas pesquisas salvas usadas por essas soluções podem ser vistas na tabela a seguir:

|name     |Categoria  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Atualizações        | Updates__MicrosoftDefaultComputerGroup         |

Selecione a pesquisa salva para visualizar a consulta usada para preencher o grupo. A imagem a seguir mostra a consulta e seus resultados:

![Pesquisas salvas](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>Integrar VMs do Azure

Na sua conta de Automação do Azure, selecione **Inventário** ou **Controle de alterações** em **GERENCIAMENTO DE CONFIGURAÇÃO** ou **Gerenciamento de atualizações** em **GERENCIAMENTO DE ATUALIZAÇÕES**.

Clique em **+Adicionar VMs do Azure**, selecione uma ou mais VMs na lista. Máquinas virtuais que não podem ser habilitadas ficam esmaecidas e não podem ser selecionadas. As VMs do Azure podem existir em qualquer região, independentemente do local da sua conta de automação. Na página **Habilitar Gerenciamento de Atualizações**, clique em **Habilitar**. Esta ação adiciona as VMs selecionadas à pesquisa salva do grupo de computadores para a solução.

![Habilitar VMs do Azure](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Integrar um computador não Azure

Computadores que não estão no Azure precisam ser adicionados manualmente. Na sua conta de Automação do Azure, selecione **Inventário** ou **Controle de alterações** em **GERENCIAMENTO DE CONFIGURAÇÃO** ou **Gerenciamento de atualizações** em **GERENCIAMENTO DE ATUALIZAÇÕES**.

Clique em **Adicionar computador não Azure**. Esta ação abre uma nova janela do navegador com as instruções [sobre como instalar e configurar o Microsoft Monitoring Agent na máquina](../azure-monitor/platform/log-analytics-agent.md) para que a máquina possa começar a gerar relatórios para a solução. Se você estiver integrando uma máquina que atualmente é gerenciada pelo System Center Operations Manager, um novo agente não é necessário, as informações do workspace são inseridas no agente existente.

## <a name="onboard-machines-in-the-workspace"></a>Integrar computadores no workspace

Máquinas ou máquinas instaladas manualmente que já estão se reportando ao seu workspace devem ser adicionadas ao Automação do Azure para que a solução seja habilitada. Na sua conta de Automação do Azure, selecione **Inventário** ou **Controle de alterações** em **GERENCIAMENTO DE CONFIGURAÇÃO** ou **Gerenciamento de atualizações** em **GERENCIAMENTO DE ATUALIZAÇÕES**.

Selecione **Gerenciar computadores**. Esta ação abre a página **Gerenciar Máquinas**. Esta página permite habilitar a solução em um conjunto selecionado de computadores, todos os computadores disponíveis ou habilitar a solução para todos os computadores atuais e habilitá-la em todos os computadores futuros. O botão **Gerenciar máquinas** pode ficar esmaecido se você escolheu anteriormente a opção **Ativar em todas as máquinas disponíveis e futuras**.

![Pesquisas salvas](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>Todos os computadores disponíveis

Para habilitar a solução a todos os computadores disponíveis, selecione **Habilitar em todos os computadores disponíveis**. Esta ação desabilita o controle para adicionar máquinas individualmente. Esta tarefa adiciona todos os nomes dos computadores que informam sobre o workspace na consulta de pesquisa salva do grupo de computadores. Quando selecionada, esta ação desabilita o botão **Gerenciar Máquinas**.

### <a name="all-available-and-future-machines"></a>Todos os computadores disponíveis e futuros

Para ativar a solução para todas as máquinas disponíveis e futuras máquinas, selecione **Ativar em todas as máquinas disponíveis e futuras**. Essa opção exclui as pesquisas salvas e as Configurações de Escopo do workspace. Esta ação abre a solução para todas as máquinas do Azure e não do Azure que estão relatando para o workspace. Quando selecionada, essa ação desabilita o botão **Gerenciar Máquinas** permanentemente, pois não há configuração de escopo à esquerda.

Você pode adicionar as configurações de escopo de volta adicionando as pesquisas iniciais salvas de volta. Para obter mais informações, consulte [pesquisas salvas](#saved-searches).

### <a name="selected-machines"></a>Computadores selecionados

Para habilitar a solução em um ou mais computadores, selecione **Habilitar nos computadores selecionados** e clique em **adicionar** próximo a cada computador que você quer adicionar à solução. Essa tarefa adiciona os nomes dos computadores selecionados à consulta de pesquisa salva do grupo de computadores para a solução.

## <a name="unlink-workspace"></a>Desvincular o workspace

As seguintes soluções são dependentes de um espaço de trabalho do Log Analytics:

* [Gerenciamento de atualizações](automation-update-management.md)
* [Controle de alterações](automation-change-tracking.md)
* [Iniciar/parar VMs durante os horários fora de pico](automation-solution-vm-management.md)

Se você decidir que não deseja mais integrar sua conta de automação a um espaço de trabalho Log Analytics, você poderá desvincular sua conta diretamente da portal do Azure.  Antes de continuar, primeiro você precisa remover as soluções mencionadas anteriormente, caso contrário, esse processo será impedido de prosseguir. Revise o artigo para a solução específica que você importou para entender as etapas necessárias para removê-lo.

Depois de remover essas soluções, você pode concluir as etapas a seguir para desvincular sua conta de automação.

> [!NOTE]
> Algumas soluções, incluindo versões anteriores da solução de monitoramento do Azure SQL, podem ter criado ativos de automação e também podem precisar ser removidas antes de desvincular o workspace.

1. No portal do Azure, abra sua conta de automação e, na página da conta de automação, selecione **Workspace vinculado** na seção rotulada **Recursos relacionados** à esquerda.

2. Na página Desvincular o workspace, clique em **Desvincular o workspace**.

   ![Página Desvincular workspace](media/automation-onboard-solutions-from-automation-account/automation-unlink-workspace-blade.png).

   Você receberá um aviso confirmando que deseja continuar.

3. Enquanto a Automação do Azure tenta desvincular a conta de seu espaço de trabalho do Log Analytics, você pode acompanhar o progresso em **Notificações** no menu.

Se você tiver usado a solução Gerenciamento de Atualizações, como opção, convém remover os itens a seguir que não serão mais necessários após a remoção da solução.

* Atualizar agendas - Cada uma terá nomes que correspondam às implantações de atualizações que você criou)

* Grupos de trabalho híbrido criados para a solução - Cada um receberá um nome semelhante a machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Se você usou as VMs Iniciar e Parar durante a solução fora do horário, opcionalmente, talvez queira remover os seguintes itens que não são mais necessários após remover a solução.

* Iniciar e parar agendas de runbook da VM
* Iniciar e parar runbooks da VM
* variáveis

Como alternativa, você também pode desvincular seu espaço de trabalho de sua conta de automação do seu espaço de trabalho Log Analytics. Em seu espaço de trabalho, selecione **conta de automação** em **recursos relacionados**. Na página conta de automação, selecione **desvincular conta**.

## <a name="clean-up-resources"></a>Limpar recursos

Para remover uma VM do Gerenciamento de Atualizações:

* No espaço de trabalho do Log Analytics, remova a VM da pesquisa salva para a Configuração de Escopo `MicrosoftDefaultScopeConfig-Updates`. As pesquisas salvas podem ser encontradas em **Geral** no workspace.
* Remover o [agente do Microsoft Monitoring](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) ou o [agente do Log Analytics para Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Próximos passos

Continue com os tutoriais sobre as soluções para aprender a utilizá-las.

* [Tutorial - Gerenciar as atualizações de sua VM](automation-tutorial-update-management.md)

* [Tutorial - Identificar o software em uma VM](automation-tutorial-installed-software.md)

* [Tutorial - Solucionar problemas de alterações em uma VM](automation-tutorial-troubleshoot-changes.md)
