---
title: Executar scripts do PowerShell em uma VM do Windows no Azure
description: Este tópico descreve como executar scripts do PowerShell em uma máquina virtual do Windows do Azure usando o recurso executar comando
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: bbe236bd4575ffb849b6f4739fc3a27c09aa5e47
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595154"
---
# <a name="run-powershell-scripts-in-your-windows-vm-by-using-run-command"></a>Executar scripts do PowerShell em sua VM do Windows usando o comando executar

O recurso executar comando usa o agente de VM (máquina virtual) para executar scripts do PowerShell em uma VM Windows do Azure. Você pode usar esses scripts para o gerenciamento geral de computadores ou aplicativos. Eles podem ajudá-lo a diagnosticar e corrigir rapidamente problemas de rede e acesso à VM e colocar a VM de volta em um bom estado.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="benefits"></a>Benefícios

Você pode acessar suas máquinas virtuais de várias maneiras. O comando executar pode executar scripts em suas máquinas virtuais remotamente usando o agente de VM. Use o comando executar por meio do portal do Azure, da [API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)ou do [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) para VMs do Windows.

Esse recurso é útil em todos os cenários em que você deseja executar um script em uma máquina virtual. É uma das únicas maneiras de solucionar problemas e corrigir uma máquina virtual que não tem a porta RDP ou SSH aberta devido à configuração imprópria da rede ou do usuário administrativo.

## <a name="restrictions"></a>Quanto

As seguintes restrições se aplicam quando você está usando o comando executar:

* A saída é limitada aos últimos 4.096 bytes.
* O tempo mínimo para executar um script é de cerca de 20 segundos.
* Os scripts são executados como sistema no Windows.
* Um script de cada vez pode ser executado.
* Não há suporte para scripts que solicitam informações (modo interativo).
* Não é possível cancelar um script em execução.
* O tempo máximo que um script pode executar é de 90 minutos. Depois disso, ele atingirá o tempo limite.
* A conectividade de saída da VM é necessária para retornar os resultados do script.

> [!NOTE]
> Para funcionar corretamente, o comando executar requer conectividade (porta 443) para endereços IP públicos do Azure. Se a extensão não tiver acesso a esses pontos de extremidade, os scripts poderão ser executados com êxito, mas não retornar os resultados. Se você estiver bloqueando o tráfego na máquina virtual, poderá usar [marcas de serviço](../../virtual-network/security-overview.md#service-tags) para permitir o tráfego para endereços IP públicos do Azure usando a marca `AzureCloud`.

## <a name="available-commands"></a>Comandos disponíveis

Esta tabela mostra a lista de comandos disponíveis para VMs do Windows. Você pode usar o comando **RunPowerShellScript** para executar qualquer script personalizado desejado. Quando você estiver usando o CLI do Azure ou o PowerShell para executar um comando, o valor que você fornece para o parâmetro `--command-id` ou `-CommandId` deve ser um dos seguintes valores listados. Quando você especifica um valor que não é um comando disponível, você recebe esse erro:

```error
The entity was not found in this Azure location
```

|**Nome**|**Ndescrição**|
|---|---|
|**RunPowerShellScript**|Executa um script do PowerShell.|
|**EnableRemotePS**|Configura o computador para habilitar o PowerShell remoto.|
|**EnableAdminAccount**|Verifica se a conta de administrador local está desabilitada e, se for habilitada.|
|**IPConfig**| Mostra informações detalhadas para o endereço IP, a máscara de sub-rede e o gateway padrão para cada adaptador associado ao TCP/IP.|
|**RDPSettings**|Verifica as configurações do registro e as configurações de política de domínio. Sugere ações de política se o computador fizer parte de um domínio ou modificar as configurações para valores padrão.|
|**ResetRDPCert**|Remove o certificado SSL vinculado ao ouvinte RDP e restaura a segurança do ouvinte RDP para o padrão. Use esse script se você vir quaisquer problemas com o certificado.|
|**SetRDPPort**|Define o número da porta padrão ou especificada pelo usuário para conexões de Área de Trabalho Remota. Habilita regras de firewall para acesso de entrada à porta.|

## <a name="azure-cli"></a>Azure CLI

O exemplo a seguir usa o comando [AZ VM execute-Command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) para executar um script de Shell em uma VM do Windows do Azure.

```azurecli-interactive
# script.ps1
#   param(
#       [string]$arg1,
#       [string]$arg2
#   )
#   Write-Host This is a sample script with parameters $arg1 and $arg2

az vm run-command invoke  --command-id RunPowerShellScript --name win-vm -g my-resource-group \
    --scripts @script.ps1 --parameters "arg1=somefoo" "arg2=somebar"
```

## <a name="azure-portal"></a>Portal do Azure

Vá para uma VM na [portal do Azure](https://portal.azure.com) e selecione **executar comando** em **operações**. Você verá uma lista dos comandos disponíveis a serem executados na VM.

![Lista de comandos](./media/run-command/run-command-list.png)

Escolha um comando a ser executado. Alguns dos comandos podem ter parâmetros de entrada opcionais ou obrigatórios. Para esses comandos, os parâmetros são apresentados como campos de texto para que você forneça os valores de entrada. Para cada comando, você pode exibir o script que está sendo executado expandindo o **script de exibição**. O **RunPowerShellScript** é diferente dos outros comandos, pois permite que você forneça seu próprio script personalizado.

> [!NOTE]
> Os comandos internos não são editáveis.

Depois de escolher o comando, selecione **executar** para executar o script. Depois que o script for concluído, ele retornará a saída e todos os erros na janela de saída. A captura de tela a seguir mostra um exemplo de saída da execução do comando **RDPSettings** .

![Executar saída de script de comando](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

O exemplo a seguir usa o cmdlet [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) para executar um script do PowerShell em uma VM do Azure. O cmdlet espera que o script referenciado no parâmetro `-ScriptPath` seja local no qual o cmdlet está sendo executado.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Limitando o acesso ao comando executar

Listar os comandos de execução ou mostrar os detalhes de um comando requer a permissão `Microsoft.Compute/locations/runCommands/read` no nível de assinatura. A função de [leitor](../../role-based-access-control/built-in-roles.md#reader) interna e os níveis superiores têm essa permissão.

A execução de um comando requer a permissão `Microsoft.Compute/virtualMachines/runCommand/action` no nível da assinatura. A função [colaborador da máquina virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e os níveis superiores têm essa permissão.

Você pode usar uma das [funções internas](../../role-based-access-control/built-in-roles.md) ou criar uma [função personalizada](../../role-based-access-control/custom-roles.md) para usar o comando executar.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre outras maneiras de executar scripts e comandos remotamente em sua VM, consulte [executar scripts em sua VM do Windows](run-scripts-in-vm.md).
