---
title: Configurar servidores para um estado desejado e gerenciar o descompasso com a Automação do Azure
description: Tutorial – Gerenciar configurações de servidor com a Configuração do Estado de Automação do Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
manager: carmonm
ms.topic: conceptual
ms.date: 08/08/2018
ms.openlocfilehash: b44bcf7edeaad07fbe0b3093ba3c7100cb0c24c4
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72432069"
---
# <a name="configure-servers-to-a-desired-state-and-manage-drift"></a>Configurar servidores para um estado desejado e gerenciar dessincronização

A Configuração do Estado de Automação do Azure permite que você especifique configurações para seus servidores e garanta que esses servidores estejam no estado especificado ao longo do tempo.

> [!div class="checklist"]
> - Carregar uma VM para ser gerenciada pela DSC de Automação do Azure
> - Carregar uma configuração para Automação do Azure
> - Compilar uma configuração em uma configuração de nó
> - Atribuir uma configuração de nó a um nó gerenciado
> - Verificar o status de conformidade de um nó gerenciado

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará de:

- Uma conta de Automação do Azure. Para obter instruções sobre como criar uma conta Executar Como de Automação do Azure, consulte [Conta Executar Como do Azure](automation-sec-configure-azure-runas-account.md).
- Uma VM do Azure Resource Manager (não clássica) executando o Windows Server 2008 R2 ou posterior. Para obter instruções sobre a criação de uma VM, consulte [Criar sua primeira máquina virtual do Windows no portal do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md)
- Versão de módulo do Azure PowerShell 3.6 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).
- Familiaridade com a Configuração do Estado Desejado (DSC). Para obter informações sobre a DSC, consulte [Visão Geral da Desired State Configuration do Windows PowerShell](/powershell/scripting/dsc/overview/overview)

## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon na sua assinatura do Azure com o comando `Connect-AzureRmAccount` e siga as instruções na tela.

```powershell
Connect-AzureRmAccount
```

## <a name="create-and-upload-a-configuration-to-azure-automation"></a>Criar e carregar uma configuração na Automação do Azure

Para este tutorial, usaremos uma configuração DSC simples que garanta que o IIS seja instalado na VM.

Para obter informações sobre as configurações DSC, consulte [Configurações DSC](/powershell/scripting/dsc/configurations/configurations).

Em um editor de texto, digite o seguinte e salve localmente como `TestConfig.ps1`.

```powershell
configuration TestConfig {
   Node WebServer {
      WindowsFeature IIS {
         Ensure               = 'Present'
         Name                 = 'Web-Server'
         IncludeAllSubFeature = $true
      }
   }
}
```

> [!NOTE]
> Em cenários mais avançados em que você precisa que vários módulos sejam importados para fornecer recursos de DSC, certifique-se de que cada módulo tenha uma linha `Import-DscResource` exclusiva em sua configuração.

Chame o cmdlet `Import-AzureRmAutomationDscConfiguration` para carregar a configuração em sua conta de Automação:

```powershell
 Import-AzureRmAutomationDscConfiguration -SourcePath 'C:\DscConfigs\TestConfig.ps1' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Published
```

## <a name="compile-a-configuration-into-a-node-configuration"></a>Compilar uma configuração em uma configuração de nó

Uma configuração DSC deverá ser compilada em uma configuração de nó para que ela possa ser atribuída a um nó.

Para obter informações de como compilar configurações, consulte [Configurações DSC](/powershell/scripting/dsc/configurations/configurations).

Chame o cmdlet `Start-AzureRmAutomationDscCompilationJob` para compilar a configuração `TestConfig` em uma configuração de nó:

```powershell
Start-AzureRmAutomationDscCompilationJob -ConfigurationName 'TestConfig' -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount'
```

Isso cria uma configuração de nó chamada `TestConfig.WebServer` na conta de Automação.

## <a name="register-a-vm-to-be-managed-by-state-configuration"></a>Registrar uma VM a ser gerenciada pela Configuração do Estado

É possível usar a Configuração de Estado da Automação do Azure para gerenciar VMs do Azure (tanto Clássica quanto do Resource Manager), VMs locais, computadores Linux, VMs de AWS e computadores físicos locais. Neste tópico, abordaremos como registrar somente VMs do Azure Resource Manager. Para obter mais informações sobre como registrar outros tipos de computadores, consulte [Integrar computadores para gerenciamento por Configuração de Estado da Automação do Azure](automation-dsc-onboarding.md).

Chame o `Register-AzureRmAutomationDscNode` cmdlet para registrar a VM na Configuração do Estado de Automação do Azure.

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm'
```

Isso registra a VM especificada como um nó gerenciado na Configuração do Estado.

### <a name="specify-configuration-mode-settings"></a>Especificar definições do modo de configuração

Ao registrar uma VM como um nó gerenciado, também é possível especificar as propriedades da configuração. Por exemplo, você pode especificar que o estado do computador deve ser aplicado somente uma vez (a DSC não tentará aplicar a configuração após a verificação inicial), especificando `ApplyOnly` como o valor da propriedade **ConfigurationMode**:

```powershell
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationMode 'ApplyOnly'
```

Você também pode especificar a frequência em que a DSC verifica o estado de configuração usando a propriedade **ConfigurationModeFrequencyMins**:

```powershell
# Run a DSC check every 60 minutes
Register-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -AzureVMName 'DscVm' -ConfigurationModeFrequencyMins 60
```

Para obter mais informações de como definir as propriedades de configuração para um nó gerenciado, consulte [Register-AzureRmAutomationDscNode](/powershell/module/azurerm.automation/register-azurermautomationdscnode).

Para obter mais informações sobre as definições de configuração de DSC, consulte [Configurando o Gerenciador de Configurações Local](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="assign-a-node-configuration-to-a-managed-node"></a>Atribuir uma configuração de nó a um nó gerenciado

Agora podemos atribuir a configuração de nó compilada à VM que queremos configurar.

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Assign the node configuration to the DSC node
Set-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeConfigurationName 'TestConfig.WebServer' -NodeId $node.Id
```

Isso atribuirá a configuração de nó denominada `TestConfig.WebServer` ao nó de DSC registrado chamado `DscVm`.
Por padrão, o nó de DSC é verificado quanto à conformidade com a configuração do nó a cada 30 minutos.
Para obter informações de como alterar o intervalo de verificação de conformidade, consulte [Configurando System Center Configuration Manager Local](/powershell/scripting/dsc/managing-nodes/metaConfig).

## <a name="working-with-partial-configurations"></a>Trabalhando com configurações parciais

A configuração de estado da automação do Azure dá suporte ao uso de [configurações parciais](/powershell/dsc/pull-server/partialconfigs).
Nesse cenário, a DSC é configurada para gerenciar várias configurações de forma independente e cada configuração é recuperada da automação do Azure.
No entanto, apenas uma configuração pode ser atribuída a um nó por conta de automação.
Isso significa que se você estiver usando duas configurações para um nó, precisará de duas contas de automação.

Para obter detalhes sobre como registrar uma configuração parcial do serviço de pull, consulte a documentação para [configurações parciais](https://docs.microsoft.com/powershell/dsc/pull-server/partialconfigs#partial-configurations-in-pull-mode).

Para obter mais informações sobre como as equipes podem trabalhar em conjunto para gerenciar servidores de forma colaborativa usando a configuração como código, consulte [noções básicas sobre a função do DSC em um pipeline de CI/CD](/powershell/dsc/overview/authoringadvanced).

## <a name="check-the-compliance-status-of-a-managed-node"></a>Verificar o status de conformidade de um nó gerenciado

Você pode obter relatórios sobre o status de conformidade de um nó gerenciado chamando o cmdlet `Get-AzureRmAutomationDscNodeReport`:

```powershell
# Get the ID of the DSC node
$node = Get-AzureRmAutomationDscNode -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -Name 'DscVm'

# Get an array of status reports for the DSC node
$reports = Get-AzureRmAutomationDscNodeReport -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'myAutomationAccount' -NodeId $node.Id

# Display the most recent report
$reports[0]
```

## <a name="removing-nodes-from-service"></a>Removendo nós do serviço

Quando você adiciona um nó à configuração de estado da automação do Azure, as configurações no local Configuration Manager são definidas para registrar com o serviço e as configurações de pull e os módulos necessários para configurar o computador.
Se você optar por remover o nó do serviço, poderá fazer isso usando os cmdlets portal do Azure ou AZ.

> [!NOTE]
> O cancelamento do registro de um nó do serviço define apenas as configurações de Configuration Manager locais para que o nó não se conecte mais ao serviço.
> Isso não afeta a configuração atualmente aplicada ao nó.
> Para remover a configuração atual, use o [PowerShell](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/remove-dscconfigurationdocument?view=powershell-5.1) ou exclua o arquivo de configuração local (essa é a única opção para nós do Linux).

### <a name="azure-portal"></a>Portal do Azure

Na automação do Azure, clique em **configuração de estado (DSC)** no sumário.
Em seguida, clique em **nós** para exibir a lista de nós que estão registrados com o serviço.
Clique no nome do nó que você deseja remover.
Na exibição de nó que é aberta, clique em **Cancelar registro**.

### <a name="powershell"></a>PowerShell

Para cancelar o registro de um nó do serviço de configuração de estado da automação do Azure usando o PowerShell, siga a documentação do cmdlet [Unregister-AzAutomationDscNode](https://docs.microsoft.com/powershell/module/az.automation/unregister-azautomationdscnode?view=azps-2.0.0).

## <a name="next-steps"></a>Próximos passos

- Para começar, consulte [Introdução à Configuração de Estado da Automação do Azure](automation-dsc-getting-started.md)
- Para saber mai sobre nós de integração, veja [Máquinas de integração para o gerenciamento pela Configuração do Estado de Automação do Azure](automation-dsc-onboarding.md)
- Para saber como compilar configurações de DSC para que possam ser atribuídas a nós de destino, consulte [Compilar configurações na Configuração de Estado da Automação do Azure](automation-dsc-compile.md)
- Para referência de cmdlet do PowerShell, consulte [Cmdlets da Configuração de Estado da Automação do Azure](/powershell/module/azurerm.automation/#automation)
- Para obter informações sobre preços, consulte [Preço da Configuração de Estado da Automação do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo de uso da Configuração de Estado da Automação do Azure em um pipeline de implantação contínua, consulte [Implantação contínua usando Configuração de Estado da Automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md)
