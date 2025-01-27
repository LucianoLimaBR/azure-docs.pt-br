---
title: Criar um pool de hosts da área de trabalho virtual do Windows com o PowerShell-Azure
description: Como criar um pool de hosts na área de trabalho virtual do Windows com cmdlets do PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 8ed0e1b1406eca6dd55e9e9faf184152a190da94
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757490"
---
# <a name="create-a-host-pool-with-powershell"></a>Criar um pool de host com o PowerShell

Pools de hosts são uma coleção de uma ou mais máquinas virtuais idênticas dentro dos ambientes de locatário da Área de Trabalho Virtual do Windows. Cada pool de hosts pode conter um grupo de aplicativo com o qual os usuários podem interagir como se eles estivessem em uma área de trabalho física.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Usar o cliente do PowerShell para criar um pool de hosts

Primeiro, [baixe e importe o módulo do PowerShell da Área de Trabalho Virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) para usá-lo na sessão do PowerShell, caso ainda não tenha feito isso.

Execute o seguinte cmdlet para entrar no ambiente de área de trabalho virtual do Windows

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Em seguida, execute este cmdlet para criar um novo pool de hosts em seu locatário de área de trabalho virtual do Windows:

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Execute o próximo cmdlet para criar um token de registro para autorizar um host de sessão a ingressar no pool de hosts e salvá-lo em um novo arquivo no computador local. Você pode especificar por quanto tempo o token de registro é válido usando o parâmetro-ExpirationHours.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token > <PathToRegFile>
```

Depois disso, execute este cmdlet para adicionar Azure Active Directory usuários ao grupo de aplicativos de área de trabalho padrão para o pool de hosts.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

O cmdlet **Add-RdsAppGroupUser** não dá suporte à adição de grupos de segurança e adiciona apenas um usuário por vez ao grupo de aplicativos. Se você quiser adicionar vários usuários ao grupo de aplicativos, execute novamente o cmdlet com os nomes principais de usuário apropriados.

Execute o cmdlet a seguir para exportar o token de registro para uma variável, que será usada posteriormente no [registro das máquinas virtuais no pool de hosts da área de trabalho virtual do Windows](#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool).

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Criar máquinas virtuais para o pool de hosts

Agora você pode criar uma máquina virtual do Azure que pode ser unida ao seu pool de hosts de área de trabalho virtual do Windows.

Você pode criar uma máquina virtual de várias maneiras:

- [Criar uma máquina virtual por meio de uma imagem da galeria do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Criar uma máquina virtual com base em uma imagem gerenciada](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Criar uma máquina virtual com base em uma imagem não gerenciada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

>[!NOTE]
>Se você estiver implantando uma máquina virtual usando o Windows 7 como o sistema operacional do host, o processo de criação e implantação será um pouco diferente. Para obter mais detalhes, consulte [implantar uma máquina virtual do Windows 7 na área de trabalho virtual do Windows](deploy-windows-7-virtual-machine.md).

Depois de criar as máquinas virtuais do host de sessão, [aplique uma licença do Windows a uma VM host de sessão](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) para executar suas máquinas virtuais Windows ou Windows Server sem pagar por outra licença. 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-agent-installations"></a>Preparar as máquinas virtuais para instalações do agente de área de trabalho virtual do Windows

Você precisa fazer o seguinte para preparar suas máquinas virtuais antes de instalar os agentes de área de trabalho virtual do Windows e registrar as máquinas virtuais em seu pool de hosts de área de trabalho virtual do Windows:

- Você deve ingressar no domínio no computador. Isso permite que os usuários de área de trabalho virtual do Windows sejam mapeados de sua conta de Azure Active Directory para sua conta do Active Directory e tenham acesso permitido à máquina virtual com êxito.
- Você deve instalar a função de Host da Sessão da Área de Trabalho Remota (RDSH) se a máquina virtual estiver executando um sistema operacional Windows Server. A função de RDSH permite que os agentes de área de trabalho virtual do Windows sejam instalados corretamente.

Para ingressar no domínio com êxito, execute as ações a seguir em cada máquina virtual:

1. [Conecte-se à máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) com as credenciais fornecidas ao criar a máquina virtual.
2. Na máquina virtual, inicie o **painel de controle** e selecione **sistema**.
3. Selecione **nome do computador**, selecione **alterar configurações**e, em seguida, selecione **alterar...**
4. Selecione **domínio** e, em seguida, insira o domínio Active Directory na rede virtual.
5. Autentique com uma conta de domínio que tenha privilégios para computadores de ingresso no domínio.

    >[!NOTE]
    > Se você estiver unindo suas VMs em um ambiente do Azure Active Directory Domain Services (Azure AD DS), verifique se o usuário de ingresso no domínio também é um membro do [grupo de Administradores do AAD DC](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool"></a>Registrar as máquinas virtuais no pool de hosts da área de trabalho virtual do Windows

Registrar as máquinas virtuais em um pool de hosts da área de trabalho virtual do Windows é tão simples quanto instalar os agentes de área de trabalho virtual do Windows.

Para registrar os agentes de área de trabalho virtual do Windows, faça o seguinte em cada máquina virtual:

1. [Conecte-se à máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) com as credenciais fornecidas ao criar a máquina virtual.
2. Baixe e instale o agente de área de trabalho virtual do Windows.
   - Baixe o [agente de área de trabalho virtual do Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Clique com o botão direito do mouse no instalador baixado, selecione **Propriedades**, selecione **desbloquear**e, em seguida, selecione **OK**. Isso permitirá que o sistema confie no instalador.
   - Execute o instalador. Quando o instalador solicitar o token de registro, insira o valor obtido do cmdlet **Export-RdsRegistrationInfo** .
3. Baixe e instale o carregador de janelas do agente de área de trabalho virtual do Windows.
   - Baixe o [carregador de janelas do agente de área de trabalho virtual do Windows](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Clique com o botão direito do mouse no instalador baixado, selecione **Propriedades**, selecione **desbloquear**e, em seguida, selecione **OK**. Isso permitirá que o sistema confie no instalador.
   - Execute o instalador.

>[!IMPORTANT]
>Para ajudar a proteger seu ambiente da Área de Trabalho Virtual do Windows no Azure, recomendamos que você não abra a porta de entrada 3389 nas VMs. A Área de Trabalho Virtual do Windows não exige uma porta de entrada 3389 aberta para que os usuários acessem as VMs do pool de hosts. Caso você precise abrir a porta 3389 para fins de solução de problemas, recomendamos o uso do [acesso just-in-time à VM](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).

## <a name="next-steps"></a>Próximos passos

Agora que você já fez um pool de hosts, você pode preenchê-lo com RemoteApps. Para saber mais sobre como gerenciar aplicativos na Área de Trabalho Virtual do Windows, confira o tutorial Gerenciar grupos de aplicativos.

> [!div class="nextstepaction"]
> [Tutorial Gerenciar grupos de aplicativos](./manage-app-groups.md)
