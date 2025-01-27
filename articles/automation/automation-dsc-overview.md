---
title: Visão geral da Configuração de Estado da Automação do Azure
description: Uma visão geral da Configuração do Estado de Automação do Azure (DSC), seus termos e problemas conhecidos
keywords: powershell dsc, configuração de estado desejada, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b0b5e02009ddbb72bb062d341e7d233acfb0ceb3
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429395"
---
# <a name="azure-automation-state-configuration-overview"></a>Visão geral da Configuração de Estado da Automação do Azure

A Configuração do Estado de Automação do Azure é um serviço do Azure que permite gravar, gerenciar e compilar [configurações](/powershell/scripting/dsc/configurations/configurations) de DSC (Desired State Configuration) do PowerShell, importar [recursos de DSC](/powershell/scripting/dsc/resources/resources) e atribuir configurações a nós de destino, tudo na nuvem.

## <a name="why-use-azure-automation-state-configuration"></a>Por que usar a Configuração de Estado da Automação do Azure?

A Configuração do Estado de Automação do Azure oferece várias vantagens a usar o DSC fora do Azure.

### <a name="built-in-pull-server"></a>Servidor de pull interno

A Configuração do Estado Automação do Azure fornece um servidor de recepção DSC semelhante ao [Serviço de DSC do Recurso do Windows](/powershell/scripting/dsc/pull-server/pullserver) para que os nós de destino recebam automaticamente configurações, estejam em conformidade com o estado desejado e relatem sua conformidade. O servidor de pull interno na Automação do Azure elimina a necessidade de configurar e manter seu próprio servidor de pull. A Automação do Azure pode destinar computadores Windows ou Linux físicos ou virtuais, na nuvem ou localmente.

### <a name="management-of-all-your-dsc-artifacts"></a>Gerenciamento de todos os seus artefatos de DSC

A Configuração do Estado de Automação do Azure oferece a mesma camada de gerenciamento para a [Desired State Configuration do PowerShell](/powershell/scripting/dsc/overview/overview) que a Automação do Azure oferece para scripts do PowerShell.

No portal do Azure ou do PowerShell, você pode gerenciar todas as suas configurações, recursos e nós de destino da DSC.

![Captura de tela da página de Automação do Azure](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Importar dados de relatório em logs de Azure Monitor

Nós gerenciados com a Configuração do Estado de Automação do Azure enviam dados de status de relatórios detalhados para o servidor de pull interno. É possível configurar a Configuração do Estado de Automação do Azure para enviar esses dados ao espaço de trabalho do Log Analytics. Para saber como enviar dados de status de configuração de estado para seu espaço de trabalho Log Analytics, consulte [encaminhar dados de relatório de configuração de estado da automação do Azure para Azure monitor logs](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Pré-requisitos

Considere os seguintes requisitos ao usar a DSC (configuração de estado de automação do Azure).

### <a name="operating-system-requirements"></a>Requisitos do sistema operacional

Para nós que executam o Windows, há suporte para as seguintes versões:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

O SKU do produto autônomo do [Microsoft Hyper-V Server](/windows-server/virtualization/hyper-v/hyper-v-server-2016) não contém uma implementação do estado desejado configuração, portanto, ele não pode ser gerenciado pela configuração do estado da automação do PowerShell ou da DSC do Azure.

Para nós que executam o Linux, há suporte para as seguintes distribuições/versões:

A extensão do Linux do DSC dá suporte a todas as distribuições do Linux listadas em [distribuições do Linux com suporte](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions).

### <a name="dsc-requirements"></a>Requisitos de DSC

Para todos os nós do Windows em execução no Azure, o [WMF 5,1](https://docs.microsoft.com/powershell/wmf/setup/install-configure) será instalado durante a integração.  Para nós que executam o Windows Server 2012 e o Windows 7, o [WinRM será habilitado](https://docs.microsoft.com/powershell/dsc/troubleshooting/troubleshooting#winrm-dependency).

Para todos os nós do Linux em execução no Azure, o [PowerShell DSC para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) será instalado durante a integração.

### <a name="network-planning"></a>Configurar redes privadas

Se os nós estiverem localizados em uma rede privada, a porta e as URLs a seguir serão necessárias para que a DSC (configuração de estado) se comunique com a automação:

* Porta: Somente a TCP 443 é necessária para acesso de Internet de saída.
* URL global: *.azure-automation.net
* URL global do EUA Gov Virgínia: *.azure automation.us
* Serviço de agente: https://\<workspaceId\>.agentsvc.azure-automation.net

Isso fornece conectividade de rede para o nó gerenciado se comunicar com a automação do Azure.
Se você estiver usando recursos de DSC que se comunicam entre nós, como os [recursos WAITFOR *](https://docs.microsoft.com/powershell/dsc/reference/resources/windows/waitForAllResource), também será necessário permitir o tráfego entre os nós.
Consulte a documentação para cada recurso de DSC para entender esses requisitos de rede.

#### <a name="proxy-support"></a>Suporte a proxy

O suporte de proxy para o agente DSC está disponível no Windows versão 1809 e posterior.
Para configurar essa opção, defina o valor de **ProxyURL** e **ProxyCredential** no [script de metaconfiguração](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) usado para registrar nós.
O proxy não está disponível no DSC para versões anteriores do Windows.

Para nós do Linux, o agente de DSC oferece suporte a proxy e utilizará a variável http_proxy para determinar a URL.

#### <a name="azure-state-configuration-network-ranges-and-namespace"></a>Namespace e intervalos de rede de configuração de estado do Azure

É recomendável usar os endereços listados ao definir exceções. Para endereços IP, baixe os [Intervalos de IP do Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Esse arquivo é atualizado semanalmente e tem os intervalos atualmente implantados e as alterações futuras nos intervalos de IP.

Se você tiver uma conta de Automação do Azure definida para uma região específica, você pode restringir a comunicação para esse centro de dados regional. A tabela a seguir fornece o registro DNS para cada região:

| **Região** | **Registro DNS** |
| --- | --- |
| Centro-Oeste dos EUA | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Centro-Sul dos Estados Unidos |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Leste dos Estados Unidos   | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| Leste dos EUA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Canadá Central |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Oeste da Europa |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa Setentrional |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Sudeste Asiático |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Índia Central |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Leste do Japão |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Sudeste da Austrália |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Sul do Reino Unido | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| Governo dos EUA de Virgínia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Para obter uma lista de endereços IP da região em vez de nomes da região, faça o download do arquivo XML do [Endereço IP do Centro de Dados do Azure](https://www.microsoft.com/download/details.aspx?id=41653) do Centro de Download da Microsoft.

> [!NOTE]
> O arquivo XML do endereço IP do centro de dados do Azure lista os intervalos de endereços IP que são usados nos centros de dados do Microsoft Azure. O arquivo inclui intervalos de computação, SQL e armazenamento.
>
>Um arquivo atualizado é postado semanalmente. O arquivo reflete os intervalos atualmente implantados e quaisquer alterações futuras para os intervalos de IP. Novos intervalos que aparecem no arquivo não são usados nos centros de dados por pelo menos uma semana.
>
> É uma boa ideia fazer o download do novo arquivo XML toda semana. Em seguida, atualize seu site para identificar corretamente os serviços em execução no Azure. Os usuários do Azure ExpressRoute devem observar que esse arquivo é usado para atualizar o anúncio BGP (Border Gateway Protocol) do espaço do Azure na primeira semana de cada mês.

## <a name="next-steps"></a>Próximos passos

- Para começar, consulte [Introdução à Configuração de Estado da Automação do Azure](automation-dsc-getting-started.md)
- Para saber mai sobre nós de integração, veja [Máquinas de integração para o gerenciamento pela Configuração do Estado de Automação do Azure](automation-dsc-onboarding.md)
- Para saber como compilar configurações de DSC para que possam ser atribuídas a nós de destino, consulte [Compilar configurações na Configuração de Estado da Automação do Azure](automation-dsc-compile.md)
- Para referência de cmdlet do PowerShell, consulte [Cmdlets da Configuração de Estado da Automação do Azure](/powershell/module/azurerm.automation/#automation)
- Para obter informações sobre preços, consulte [Preço da Configuração de Estado da Automação do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo de uso da Configuração de Estado da Automação do Azure em um pipeline de implantação contínua, consulte [Implantação contínua usando Configuração de Estado da Automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md)
