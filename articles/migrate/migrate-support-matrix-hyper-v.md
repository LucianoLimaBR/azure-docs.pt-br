---
title: Matriz de suporte para migrações para Azure para avaliação e migração do Hyper-V
description: Resume as configurações e limitações de avaliação e migração do Hyper-V usando o serviço migrações para Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: raynew
ms.openlocfilehash: 0964dac6b4f381e2ec52bd9ec078741d0ee85712
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802189"
---
# <a name="support-matrix-for-hyper-v-assessment-and-migration"></a>Matriz de suporte para avaliação e migração do Hyper-V

Você pode usar o [serviço migrações para Azure](migrate-overview.md) para avaliar e migrar computadores para a nuvem Microsoft Azure. Este artigo resume as configurações de suporte e as limitações para avaliar e migrar VMs Hyper-V locais.



## <a name="hyper-v-scenarios"></a>Cenários do Hyper-V

A tabela resume os cenários com suporte para VMs do Hyper-V.

**Implantação** | **Detalhes***
--- | ---
**Avaliar VMs do Hyper-V locais** | [Configure](tutorial-prepare-hyper-v.md) sua primeira avaliação.<br/><br/> [Execute](scale-hyper-v-assessment.md) uma avaliação em larga escala.
**Migrar VMs do Hyper-V para o Azure** | [Experimente](tutorial-migrate-hyper-v.md) a migração para o Azure.

## <a name="azure-migrate-projects"></a>Projetos de migrações para Azure

**Suporte** | **Detalhes**
--- | ---
Permissões do Azure | Você precisa de permissões de colaborador ou de proprietário na assinatura para criar um projeto de migrações para Azure.
VMs Hyper-V | Avalie até 35.000 VMs do Hyper-V em um único projeto. Você pode ter vários projetos em uma assinatura do Azure. Um projeto pode incluir VMs do VMware e VMs do Hyper-V, até os limites de avaliação.
painel Geografia do app&#39;s selecionado | Você pode criar projetos de migrações para Azure em uma série de geografias. Embora seja possível criar projetos em geografias específicos, você pode avaliar ou migrar computadores para outros locais de destino. A região geográfica do projeto é usada apenas para armazenar os metadados descobertos.

  **Geografia** | **Local de armazenamento de metadados**
  --- | ---
  Azure Governamental | US Gov - Virgínia
  Pacífico Asiático | Ásia Oriental ou sudeste asiático
  Austrália | Leste da Austrália ou sudeste da Austrália
  Brasil | Sul do Brasil
  Canadá | Canadá central ou leste do Canadá
  Europa | Europa Setentrional ou Europa Ocidental
  França | França Central
  Índia | Índia central ou sul da Índia
  Japão |  Leste do Japão ou oeste do Japão
  Coreia do Sul | Coreia central ou sul da Coreia
  Reino Unido | Sul do Reino Unido ou Oeste do Reino Unido
  Estados Unidos | EUA Central ou oeste dos EUA 2


 > [!NOTE]
 > No momento, o suporte para Azure governamental está disponível apenas para a [versão mais antiga](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions) das migrações para Azure.


## <a name="assessment-hyper-v-host-requirements"></a>Avaliação – requisitos de host do Hyper-V

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Implantação de host**       | O host Hyper-V pode ser autônomo ou implantado em um cluster. |
| **Permissões**           | Você precisa de permissões de administrador no host do Hyper-V. <br/> Como alternativa, se você não quiser atribuir permissões de administrador, crie uma conta de usuário local ou de domínio e adicione o usuário a esses grupos – usuários de gerenciamento remoto, administradores do Hyper-V e usuários de monitor de desempenho. |
| **Sistema operacional do host** | Windows Server 2019, Windows Server 2016 ou Windows Server 2012 R2.<br/> Não é possível avaliar as VMs localizadas em hosts Hyper-V que executam o Windows Server 2012. |
| **Comunicação remota do PowerShell**   | Deve ser habilitado em cada host. |
| **Réplica do Hyper-V**       | Se você usar a réplica do Hyper-V (ou se tiver várias VMs com os mesmos identificadores de VM) e descobrir as VMs originais e replicadas usando as migrações para Azure, a avaliação gerada pelas migrações para Azure pode não ser precisa. |


## <a name="assessment-hyper-v-vm-requirements"></a>Avaliação-requisitos de VM do Hyper-V

| **Suporte**                  | **Detalhes**               
| :----------------------------- | :------------------- |
| **Sistema operacional** | Todos os sistemas operacionais [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) com suporte no Azure. |
| **Integration Services**       | Os [Integration Services do Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) devem estar em execução em VMs que você avaliar, a fim de capturar informações do sistema operacional. |



## <a name="assessment-appliance-requirements"></a>Avaliação – requisitos de dispositivo

Para avaliação, as migrações para Azure executam um dispositivo leve para descobrir VMs do Hyper-V e enviar metadados da VM e dados de desempenho para migrações para Azure. O dispositivo é executado em uma VM do Hyper-V e você configura usando um VHD do Hyper-V compactado que você baixa do portal do Azure. A tabela a seguir resume os requisitos do dispositivo.

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Implantação de dispositivo**   |  Você implanta o dispositivo como uma VM do Hyper-V.<br/> A VM do dispositivo fornecida pela migração do Azure é a VM Hyper-V versão 5,0.<br/> O host Hyper-V deve estar executando o Windows Server 2012 R2 ou posterior.<br/> O host precisa de espaço suficiente para alocar 16 GB de RAM, 8 vCPUs, cerca de 80 GB de espaço de armazenamento e um comutador externo para a VM do dispositivo.<br/> O dispositivo precisa de um endereço IP estático ou dinâmico e acesso à Internet.
| **Projeto de migrações para Azure**  |  Um dispositivo pode ser associado a um único projeto.<br/> Qualquer número de dispositivos pode ser associado a um único projeto.<br/> Você pode avaliar até 35.000 VMs em um projeto.
| **Hosts Hyper-V**          | Um dispositivo pode se conectar a até 300 hosts Hyper-V.
| **Descoberta**              | Um único dispositivo pode descobrir até 5000 VMs.
| **Grupo de avaliação**       | Você pode adicionar até 35.000 computadores em um único grupo.
| **Avaliação**             | Você pode avaliar até 35.000 VMs em uma única avaliação.



## <a name="assessment-appliance-url-access"></a>Avaliação – acesso à URL do dispositivo

Para avaliar as VMs, o dispositivo de migrações para Azure precisa de conectividade com a Internet.

- Quando você implanta o dispositivo, as migrações para Azure executam uma verificação de conectividade para as URLs resumidas na tabela a seguir.
- Se você estiver usando um proxy baseado em URL, permita o acesso às URLs na tabela, certificando-se de que o proxy resolva todos os registros CNAME recebidos ao pesquisar as URLs.
- Se você tiver um proxy de interceptação, talvez seja necessário importar o certificado do servidor do servidor proxy para o dispositivo.


**URL** | **Detalhes**  
--- | ---
*. portal.azure.com | Navegação para a portal do Azure
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *. microsoft.com <br/> *. live.com  | Entre na sua assinatura do Azure
*.microsoftonline.com <br/> *.microsoftonline p.com | Criação de aplicativos de Azure Active Directory para comunicações de dispositivo para serviço.
management.azure.com | Criação de aplicativos de Azure Active Directory para comunicações de dispositivo para serviço.
dc.services.visualstudio.com | Log e monitoramento
*.vault.azure.net | Gerencie segredos em Azure Key Vault ao se comunicar entre o dispositivo e o serviço.
aka.ms/* | Permitir acesso a links conhecidos.
https://download.microsoft.com/download/* | Permite downloads do site de download da Microsoft.



## <a name="assessment-port-requirements"></a>Avaliação – requisitos de porta

A tabela a seguir resume os requisitos de porta para avaliação.

**Dispositivo** | **Conexão**
--- | ---
**Baseado** | Conexões de entrada na porta TCP 3389 para permitir conexões de área de trabalho remota para o dispositivo.<br/> Conexões de entrada na porta 44368 para acessar remotamente o aplicativo de gerenciamento de dispositivo usando a URL: ``` https://<appliance-ip-or-name>:44368 ```<br/> Conexões de saída nas portas 443, 5671 e 5672 para enviar metadados de descoberta e desempenho para migrações para Azure.
**Host/cluster do Hyper-V** | Conexões de entrada nas portas WinRM 5985 (HTTP) e 5986 (HTTPS) para efetuar pull da configuração e dos metadados de desempenho das VMs do Hyper-V usando uma sessão modelo CIM (CIM).

## <a name="migration-limitations"></a>Migração-limitações
Você pode selecionar até 10 VMs de uma só vez para replicação. Se você quiser migrar mais máquinas, faça a replicação em grupos de 10.

## <a name="migration-hyper-v-host-requirements"></a>Migração-requisitos de host do Hyper-V

| **Suporte**                | **Detalhes**               
| :-------------------       | :------------------- |
| **Implantação de host**       | O host Hyper-V pode ser autônomo ou implantado em um cluster. |
| **Permissões**           | Você precisa de permissões de administrador no host do Hyper-V. |
| **Sistema operacional do host** | Windows Server 2019, Windows Server 2016 ou Windows Server 2012 R2. |

## <a name="migration-hyper-v-vm-requirements"></a>Migração-requisitos de VM do Hyper-V

| **Suporte**                  | **Detalhes**               
| :----------------------------- | :------------------- |
| **Sistema operacional** | Todos os sistemas operacionais [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) e [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) com suporte no Azure. |
| **Permissões**           | Você precisa de permissões de administrador em cada VM do Hyper-V que deseja avaliar. |
| **Integration Services**       | Os [Integration Services do Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) devem estar em execução em VMs que você avaliar, a fim de capturar informações do sistema operacional. |
| **Alterações necessárias para o Azure** | Algumas VMs podem precisar de alterações para que possam ser executadas no Azure. As migrações para Azure fazem essas alterações automaticamente para os seguintes sistemas operacionais:<br/> -Red Hat Enterprise Linux 6.5 +, 7.0 +<br/> -CentOS 6.5 +, 7.0 +</br> -SUSE Linux Enterprise Server 12 SP1 +<br/> -Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS<br/> -Debian 7, 8<br/><br/> Para outros sistemas operacionais, você precisa fazer ajustes manualmente antes da migração. Os artigos relevantes contêm instruções sobre como fazer isso. |
| **Inicialização do Linux**                 | Se/boot estiver em uma partição dedicada, ele deverá residir no disco do sistema operacional e não poderá ser distribuído em vários discos.<br/> Se/boot fizer parte da partição raiz (/), a partição '/' deverá estar no disco do sistema operacional e não poderá abranger outros discos. |
| **Inicialização UEFI**                  | A VM migrada no Azure será convertida automaticamente em uma VM de inicialização do BIOS. A VM deve estar executando apenas o Windows Server 2012 e posterior. O disco do sistema operacional deve ter até cinco partições ou menos e o tamanho do disco do sistema operacional deve ser inferior a 300 GB.
  |
| **Tamanho do disco**                  | 2 TB para o disco do sistema operacional, 4 TB para discos de dados.
| **Número do disco** | Um máximo de 16 discos por VM.
| **Discos/volumes criptografados**    | Sem suporte para migração. |
| **Discos de RDM/PassThrough**      | Sem suporte para migração. |
| **Disco compartilhado** | As VMs que usam discos compartilhados não têm suporte para migração.
| **-**                        | Volumes NFS montados como volumes nas VMs não serão replicados. |
| **ISCSI**                      | As VMs com destinos iSCSI não têm suporte para migração.
| **Disco de destino**                | Você pode migrar para VMs do Azure somente com o Managed disks. |
| **Protocolo** | Sem suporte.
| **Agrupamento NIC** | Sem suporte.
| **Azure Site Recovery** | Não é possível replicar usando a migração de servidor de migrações para Azure se a VM estiver habilitada para replicação com Azure Site Recovery.





## <a name="migration-hyper-v-host-url-access"></a>Migração-acesso à URL do host Hyper-V

A tabela a seguir resume os requisitos de acesso à URL para hosts Hyper-V.

**URL** | **Detalhes**  
--- | ---
login.microsoftonline.com | Gerenciamento de acesso e controle de identidade usando o Active Directory.
*.backup.windowsazure.com | Transferência e coordenação de dados de replicação.
*.hypervrecoverymanager.windowsazure.com | Conecte-se às URLs de serviço de migrações para Azure.
*.blob.core.windows.net | Carregar dados para contas de armazenamento.
dc.services.visualstudio.com | Carregar logs de aplicativo usados para monitoramento interno.
time.windows.com | Verifica a sincronização de hora entre o sistema e o horário global.

## <a name="migration-port-access"></a>Migração-acesso à porta

A tabela a seguir resume os requisitos de porta em hosts Hyper-V e VMs para migração de VM.

**Dispositivo** | **Conexão**
--- | ---
Hosts/VMs do Hyper-V | Conexões de saída na porta HTTPS 443 para enviar dados de replicação de VM para migrações para Azure.




## <a name="next-steps"></a>Próximos passos

[Prepare-se para a avaliação de VM do Hyper-V](tutorial-prepare-hyper-v.md) para migração.
