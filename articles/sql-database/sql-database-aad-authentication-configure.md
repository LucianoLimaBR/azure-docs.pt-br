---
title: Configurar autenticação de Azure Active Directory-SQL | Microsoft Docs
description: Saiba como se conectar ao banco de dados SQL, instância gerenciada e SQL Data Warehouse usando a autenticação Azure Active Directory-depois de configurar o Azure AD.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 10/16/2019
ms.openlocfilehash: 82409bbe2f40e42a8331cd801649b93987a923d2
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72550713"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Configurar e gerenciar a autenticação de Azure Active Directory com o SQL

Este artigo mostra como criar e popular o Azure AD e, em seguida, usar o Azure AD com o [banco de dados SQL](sql-database-technical-overview.md)do Azure, [instância gerenciada](sql-database-managed-instance.md)e [SQL data warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Para obter uma visão geral, consulte [Azure Active Directory autenticação](sql-database-aad-authentication.md).

> [!NOTE]
> Este artigo aplica-se ao SQL Server do Azure e ao banco de dados SQL e SQL Data Warehouse bancos que são criados no SQL Server do Azure. Para simplificar, o banco de dados SQL é usado quando se refere ao Banco de Dados SQL e ao SQL Data Warehouse.
> [!IMPORTANT]  
> Não há suporte para a conexão com SQL Server em execução em uma VM do Azure usando uma conta de Azure Active Directory. Em vez disso, use uma conta de Active Directory de domínio.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos.

## <a name="create-and-populate-an-azure-ad"></a>Criar e popular um Azure AD

Crie um Azure AD e popule-o com usuários e grupos. O Azure AD pode ser o domínio gerenciado inicial do Azure AD. O Azure AD também pode ser um Active Directory Domain Services local federado com o Azure AD.

Para obter mais informações, consulte [integrando suas identidades locais com Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md), [Adicionar seu próprio nome de domínio ao Azure ad](../active-directory/active-directory-domains-add-azure-portal.md), [Microsoft Azure agora dá suporte à Federação com o Active Directory do Windows Server](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [administrando seu diretório do AD do Azure](../active-directory/fundamentals/active-directory-administer.md), [gerenciar o Azure ad usando o Windows PowerShell](/powershell/azure/overview)e [as portas e os protocolos de identidade híbrida necessários](../active-directory/hybrid/reference-connect-ports.md).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Associar ou adicionar uma assinatura do Azure ao Azure Active Directory

1. Associe sua assinatura do Azure ao Azure Active Directory tornando o diretório um diretório confiável para a assinatura do Azure que hospeda o banco de dados. Para obter detalhes, consulte [como as assinaturas do Azure estão associadas ao Azure ad](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
2. Use o alternador de diretório no portal do Azure para alternar para a assinatura associada ao domínio.

   **Informações adicionais:** cada assinatura do Azure tem uma relação de confiança com uma instância do AD do Azure. Isso significa que ele confia nesse diretório para autenticar usuários, serviços e dispositivos. Várias assinaturas podem confiar no mesmo diretório, mas uma assinatura confia em apenas um diretório. Essa relação de confiança que uma assinatura tem com um diretório é diferente da relação que uma assinatura tem com todos os outros recursos no Azure (sites, bancos de dados e assim por diante), que são mais semelhantes aos recursos filho de uma assinatura. Se uma assinatura expirar, o acesso a esses outros recursos associados à assinatura também será interrompido. Mas o diretório permanece no Azure, e você pode associar outra assinatura a esse diretório e continuar a gerenciar os usuários do diretório. Para obter mais informações sobre recursos, consulte [Noções básicas sobre o acesso a recursos no Azure](../active-directory/active-directory-b2b-admin-add-users.md). Para saber mais sobre essa relação confiável, consulte [como associar ou adicionar uma assinatura do Azure ao Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Criar um administrador do Azure AD para o SQL Server do Azure

Cada servidor do SQL Azure (que hospeda um banco de dados SQL ou SQL Data Warehouse) começa com uma única conta de administrador de servidor que é o administrador de todo o SQL Server do Azure. Um segundo administrador do SQL Server deve ser criado, que é uma conta do Azure AD. Essa entidade de segurança é criada como um usuário de banco de dados independente no banco de dados mestre. Como administradores, as contas de administrador de servidor são membros da função **db_owner** em cada banco de dados de usuário e inserem cada banco de dados de usuário como o usuário **dbo** . Para obter mais informações sobre as contas do administrador do servidor, veja [Gerenciando Bancos de Dados e Logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md).

Ao usar Azure Active Directory com a replicação geográfica, o administrador de Azure Active Directory deve ser configurado tanto para os servidores primários quanto para os secundários. Se um servidor não tiver um administrador do Azure Active Directory, os usuários e logons do Azure Active Directory obterão um erro de servidor "Não é possível conectar-se".

> [!NOTE]
> Os usuários que não são baseados em uma conta do Azure AD (incluindo a conta de administrador do Azure SQL Server) não podem criar usuários baseados no Azure AD, pois não têm permissão para validar os usuários de banco de dados proposto com o Azure AD.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Provisionar um administrador de Azure Active Directory para sua instância gerenciada

> [!IMPORTANT]
> Somente siga estas etapas se você estiver Provisionando uma instância gerenciada. Esta operação só pode ser executada pelo administrador global/da empresa ou por um administrador de função com privilégios no Azure AD. As etapas a seguir descrevem o processo de conceder permissões para usuários com privilégios diferentes no diretório.

Sua instância gerenciada precisa de permissões para ler o Azure AD para realizar tarefas com êxito, como a autenticação de usuários por meio da Associação de grupo de segurança ou da criação de novos usuários. Para que isso funcione, você precisa conceder permissões à instância gerenciada para ler o Azure AD. Há duas maneiras de fazer isso: do portal e do PowerShell. As etapas a seguir são os dois métodos.

1. No portal do Azure, no canto superior direito, selecione sua conexão para listar uma listagem de diretórios ativos possíveis.
2. Escolha o Active Directory correto como o Azure AD padrão.

   Esta etapa vincula a assinatura associada ao Active Directory com a instância gerenciada, certificando-se de que a mesma assinatura seja usada tanto para o Azure AD quanto para a instância gerenciada.
3. Navegue até a instância gerenciada e selecione uma que você deseja usar para a integração do Azure AD.

   ![aad](./media/sql-database-aad-authentication/aad.png)

4. Selecione a faixa na parte superior da página Active Directory administrador e conceda permissão ao usuário atual. Se você estiver conectado como administrador global/da empresa no Azure AD, poderá fazer isso no portal do Azure ou usando o PowerShell com o script abaixo.

    ![conceder permissões-Portal](./media/sql-database-aad-authentication/grant-permissions.png)

    ```powershell
    # Gives Azure Active Directory read permission to a Service Principal representing the managed instance.
    # Can be executed only by a "Company Administrator", "Global Administrator", or "Privileged Role Administrator" type of user.

    $aadTenant = "<YourTenantId>" # Enter your tenant ID
    $managedInstanceName = "MyManagedInstance"

    # Get Azure AD role "Directory Users" and create if it doesn't exist
    $roleName = "Directory Readers"
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    if ($role -eq $null) {
        # Instantiate an instance of the role template
        $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
        Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
        $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    }

    # Get service principal for managed instance
    $roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
    $roleMember.Count
    if ($roleMember -eq $null)
    {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1))
    {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null)
    {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else
    {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. Depois que a operação for concluída com êxito, a notificação a seguir será exibida no canto superior direito:

    ![Êxito](./media/sql-database-aad-authentication/success.png)

6. Agora você pode escolher o administrador do Azure AD para sua instância gerenciada. Para isso, na página Administrador de Active Directory, selecione **definir** comando de administrador.

    ![definir administrador](./media/sql-database-aad-authentication/set-admin.png)

7. Na página Administrador do AAD, procure um usuário, selecione o usuário ou grupo que será um administrador e selecione **selecionar**.

   A página de administração Active Directory mostra todos os membros e grupos de sua Active Directory. Os usuários ou grupos que estão esmaecidos não podem ser selecionados porque não têm suporte como administradores do Azure AD. Consulte a lista de administradores com suporte em [limitações e recursos do Azure ad](sql-database-aad-authentication.md#azure-ad-features-and-limitations). O RBAC (controle de acesso baseado em função) aplica-se somente ao portal do Azure e não é propagado para SQL Server.

    ![Adicionar Administrador](./media/sql-database-aad-authentication/add-admin.png)

8. Na parte superior da página do administrador do Active Directory, selecione **salvar**.

    ![Salvar](./media/sql-database-aad-authentication/save.png)

    O processo de alteração do administrador pode levar vários minutos. Em seguida, o novo administrador aparece na caixa Active Directory admin.

Depois de provisionar um administrador do Azure AD para sua instância gerenciada, você pode começar a criar entidades de segurança de servidor do Azure AD (logons) (**Visualização pública**) com a sintaxe <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Create login</a> . Para obter mais informações, consulte [visão geral da instância gerenciada](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Para remover um administrador mais tarde, na parte superior da página Administrador do Active Directory, selecione **remover administrador**e, em seguida, selecione **salvar**.

### <a name="powershell-for-sql-managed-instance"></a>PowerShell para instância gerenciada do SQL

Para executar os cmdlets do PowerShell, você precisa ter o Azure PowerShell instalado e em execução. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview). Para provisionar um administrador do Azure AD, execute os seguintes comandos de Azure PowerShell:

- Connect-AzAccount
- Select-AzSubscription

Cmdlets usados para provisionar e gerenciar o administrador do Azure AD para instância gerenciada do SQL:

| Nome do cmdlet | Descrição |
| --- | --- |
| [Set-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlinstanceactivedirectoryadministrator) |Provisiona um administrador do Azure AD para instância gerenciada do SQL na assinatura atual. (Deve ser da assinatura atual)|
| [Remove-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlinstanceactivedirectoryadministrator) |Remove um administrador do Azure AD para instância gerenciada do SQL na assinatura atual. |
| [Get-AzSqlInstanceActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlinstanceactivedirectoryadministrator) |Retorna informações sobre um administrador do Azure AD para instância gerenciada do SQL na assinatura atual.|

### <a name="powershell-examples-for-managed-instance"></a>Exemplos do PowerShell para instância gerenciada

O comando a seguir obtém informações sobre um administrador do Azure AD para uma instância gerenciada denominada ManagedInstance01 que está associada a um grupo de recursos chamado ResourceGroup01.

```powershell
Get-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01"
```

O comando a seguir provisiona um grupo de administradores do Azure AD chamado DBAs para a instância gerenciada chamada ManagedInstance01. Este servidor está associado ao grupo de recursos ResourceGroup01.

```powershell
Set-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstance01" -DisplayName "DBAs" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353b"
```

O comando a seguir remove o administrador do Azure AD da instância gerenciada denominada ManagedInstanceName01 associada ao grupo de recursos ResourceGroup01.

```powershell
Remove-AzSqlInstanceActiveDirectoryAdministrator -ResourceGroupName "ResourceGroup01" -InstanceName "ManagedInstanceName01" -Confirm -PassThru
```

### <a name="cli-for-sql-managed-instance"></a>CLI para instância gerenciada do SQL

Você também pode provisionar um administrador do Azure AD para instância gerenciada do SQL chamando os seguintes comandos da CLI:

| Comando | Descrição |
| --- | --- |
|[AZ SQL mi ad-admin Create](https://docs.microsoft.com/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-create) |Provisiona um administrador de Azure Active Directory para instância gerenciada do SQL. (Deve ser da assinatura atual) |
|[AZ SQL mi ad-admin Delete](https://docs.microsoft.com/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-delete) |Remove um administrador de Azure Active Directory para a instância gerenciada do SQL. |
|[AZ SQL mi ad-lista de administradores](https://docs.microsoft.com/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-list) |Retorna informações sobre um administrador de Azure Active Directory configurado atualmente para instância gerenciada do SQL. |
|[AZ SQL mi-atualização de administração](https://docs.microsoft.com/cli/azure/sql/mi/ad-admin#az-sql-mi-ad-admin-update) |Atualiza o administrador de Active Directory para uma instância gerenciada do SQL. |

Para obter mais informações sobre comandos da CLI, consulte [AZ SQL mi](https://docs.microsoft.com/cli/azure/sql/mi). 

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Provisionar um administrador de Azure Active Directory para seu servidor de banco de dados SQL do Azure

> [!IMPORTANT]
> Somente siga estas etapas se estiver provisionando um servidor de banco de dados SQL do Azure ou data warehouse.

Os dois procedimentos a seguir mostram como provisionar um administrador de Azure Active Directory para o SQL Server do Azure no portal do Azure e usando o PowerShell.

### <a name="azure-portal"></a>Portal do Azure

1. No [portal do Azure](https://portal.azure.com/), no canto superior direito, selecione sua conexão para listar uma listagem de diretórios ativos possíveis. Escolha o Active Directory correto como o Azure AD padrão. Esta etapa vincula o Active Directory associado à assinatura ao SQL Server do Azure, certificando-se de que a mesma assinatura seja usada tanto para o Azure AD quanto para o SQL Server. (O SQL Server do Azure pode hospedar o banco de dados SQL do Azure ou o Azure SQL Data Warehouse.)  ![choose-][8] do AD

2. Na faixa à esquerda, selecione **todos os serviços**e, no tipo de filtro no **SQL Server**. Selecione **SQL Servers**.

    ![sqlservers. png](media/sql-database-aad-authentication/sqlservers.png)

    >[!NOTE]
    > Nesta página, antes de selecionar **SQL Servers**, você pode selecionar a **estrela** ao lado do nome para a categoria *favorita* e adicionar **servidores SQL** à barra de navegação à esquerda.

3. Na página **SQL Server** , selecione **Active Directory admin**.
4. Na página **administrador do Active Directory** , selecione **definir administrador**.   ](./media/sql-database-aad-authentication/select-active-directory.png) do ![select Active Directory  

5. Na página **Adicionar Administrador** , procure um usuário, selecione o usuário ou grupo que será um administrador e selecione **selecionar**. (A página de administração Active Directory mostra todos os membros e grupos de sua Active Directory. Os usuários ou grupos que estão esmaecidos não podem ser selecionados porque não têm suporte como administradores do Azure AD. (Consulte a lista de administradores com suporte na seção **recursos e limitações do Azure ad** de [usar Azure Active Directory autenticação para autenticação com o banco de dados SQL ou SQL data warehouse](sql-database-aad-authentication.md).) O RBAC (controle de acesso baseado em função) aplica-se somente ao portal e não é propagado para SQL Server.
    ](./media/sql-database-aad-authentication/select-admin.png) do ![select admin  

6. Na parte superior da página do **administrador do Active Directory** , selecione **salvar**.
    ](./media/sql-database-aad-authentication/save-admin.png) do ![save admin

O processo de alteração do administrador pode levar vários minutos. O novo administrador aparece na caixa **Administrador do Active Directory** .

   > [!NOTE]
   > Ao configurar o administrador do Azure AD, o novo nome de administrador (usuário ou grupo) não pode já estar presente no banco de dados mestre virtual como um usuário de autenticação SQL Server. Se estiver presente, a instalação do administrador do Azure AD falhará; reverter sua criação e indicar que esse administrador (nome) já existe. Como esse usuário de autenticação do SQL Server não é parte do Azure AD, qualquer esforço para se conectar ao servidor usando a autenticação do Azure AD falhará.

Para remover um administrador mais tarde, na parte superior da página **administrador do Active Directory** , selecione **remover administrador**e, em seguida, selecione **salvar**.

### <a name="powershell-for-azure-sql-database-and-azure-sql-data-warehouse"></a>PowerShell para banco de dados SQL do Azure e Azure SQL Data Warehouse

Para executar os cmdlets do PowerShell, você precisa ter o Azure PowerShell instalado e em execução. Para obter informações detalhadas, confira [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview). Para provisionar um administrador do Azure AD, execute os seguintes comandos de Azure PowerShell:

- Connect-AzAccount
- Select-AzSubscription

Cmdlets usados para provisionar e gerenciar o administrador do Azure AD para o banco de dados SQL do Azure e o Azure SQL Data Warehouse:

| Nome do cmdlet | Descrição |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Provisiona um administrador de Azure Active Directory para o Azure SQL Server ou o Azure SQL Data Warehouse. (Deve ser da assinatura atual) |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Remove um administrador de Azure Active Directory para o Azure SQL Server ou o SQL Data Warehouse do Azure. |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Retorna informações sobre um administrador de Azure Active Directory configurado atualmente para o Azure SQL Server ou o Azure SQL Data Warehouse. |

Use o comando Get-Help do PowerShell para ver mais informações para cada um desses comandos. Por exemplo, ``get-help Set-AzSqlServerActiveDirectoryAdministrator``.

### <a name="powershell-examples-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Exemplos do PowerShell para o banco de dados SQL do Azure e o Azure SQL Data Warehouse

O script a seguir provisiona um grupo de administradores do Azure AD chamado **DBA_Group** (ID de objeto `40b79501-b343-44ed-9ce7-da4c8cc7353f`) para o servidor **demo_server** em um grupo de recursos denominado **Group-23**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group"
```

O parâmetro de entrada **DisplayName** aceita o nome de exibição do Azure AD ou o Nome UPN. Por exemplo, ``DisplayName="John Smith"`` e ``DisplayName="johns@contoso.com"``. Para grupos do Azure AD, há suporte apenas para o nome de exibição do Azure AD.

> [!NOTE]
> O ```Set-AzSqlServerActiveDirectoryAdministrator``` de comando Azure PowerShell não impede que você provisione administradores do Azure AD para usuários sem suporte. Um usuário para o qual não há suporte pode ser provisionado, mas não pode se conectar a um banco de dados.

O seguinte exemplo usa a **ObjectID**opcional:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> O **ObjectID** do Azure AD é necessário quando **DisplayName** não é exclusivo. Para recuperar os valores de **ObjectID** e **DisplayName**, use a seção do Active Directory do Portal Clássico do Azure e exiba as propriedades de um usuário ou grupo.

O exemplo a seguir retorna informações sobre o administrador atual do Azure AD para o SQL Server do Azure:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

O exemplo a seguir remove um administrador do Azure AD:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

> [!NOTE]
> Você também pode provisionar um administrador de Azure Active Directory usando as APIs REST. Para obter mais informações, consulte [referência da API REST do gerenciamento de serviços e operações para operações de banco de dados SQL do Azure para banco de dados SQL do Azure](https://docs.microsoft.com/rest/api/sql/)

### <a name="cli-for-azure-sql-database-and-azure-sql-data-warehouse"></a>CLI do banco de dados SQL do Azure e Azure SQL Data Warehouse

Você também pode provisionar um administrador do Azure AD chamando os seguintes comandos da CLI:

| Comando | Descrição |
| --- | --- |
|[AZ SQL Server ad-admin Create](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) |Provisiona um administrador de Azure Active Directory para o Azure SQL Server ou o Azure SQL Data Warehouse. (Deve ser da assinatura atual) |
|[AZ SQL Server ad-admin Delete](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) |Remove um administrador de Azure Active Directory para o Azure SQL Server ou o SQL Data Warehouse do Azure. |
|[AZ SQL Server ad-admin List](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) |Retorna informações sobre um administrador de Azure Active Directory configurado atualmente para o Azure SQL Server ou o Azure SQL Data Warehouse. |
|[AZ SQL Server ad-Admin Update](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) |Atualiza o administrador de Active Directory para um Azure SQL Server ou Azure SQL Data Warehouse. |

Para obter mais informações sobre comandos da CLI, consulte [AZ SQL Server](https://docs.microsoft.com/cli/azure/sql/server).

## <a name="configure-your-client-computers"></a>Configurar os computadores cliente

Em todos os computadores cliente, dos quais seus aplicativos ou usuários se conectam ao Banco de Dados SQL do Azure ou ao Azure SQL Data Warehouse usando identidades do Azure AD, você deve instalar o software a seguir:

- .NET Framework 4,6 ou posterior de [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- A Biblioteca de Autenticação do Active Directory do Azure para SQL Server (**ADALSQL.DLL**) está disponível em vários idiomas (tanto x86 quanto amd64) no centro de download da [Biblioteca de Autenticação do Microsoft Active Directory para Microsoft SQL Server](https://www.microsoft.com/download/details.aspx?id=48742).

Você pode atender a esses requisitos:

- A instalação do [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) ou [SQL Server Data Tools para o Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) atende aos requisitos do .NET Framework 4,6.
- O SSMS instala a versão x86 do **ADALSQL.DLL**.
- O SSDT instala a versão amd64 do **ADALSQL.DLL**.
- O Visual Studio mais recente dos [Downloads do Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) atende ao requisito do .NET Framework 4.6, mas não instala a versão amd64 necessária do **ADALSQL.DLL**.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Criar usuários de banco de dados independente em seu banco de dados, mapeados para identidades do AD do Azure

>[!IMPORTANT]
>A instância gerenciada agora dá suporte a entidades de segurança do Azure AD (logons) (**Visualização pública**), que permite que você crie logons de usuários, grupos ou aplicativos do Azure AD. As entidades de segurança do servidor do Azure AD (logons) fornecem a capacidade de autenticar para sua instância gerenciada sem exigir que os usuários do banco de dados sejam criados como um usuário de banco de dados independente. Para obter mais informações, consulte [visão geral da instância gerenciada](sql-database-managed-instance.md#azure-active-directory-integration). Para obter a sintaxe sobre a criação de entidades de segurança do servidor do Azure AD (logons), consulte <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">criar logon</a>.

A autenticação do Active Directory do Azure exige que os usuários do banco de dados sejam criados como usuários do banco de dados independente. Um usuário de banco de dados independente baseado em uma identidade do Azure AD é um usuário de banco de dados que não tem um logon no banco de dados mestre e que mapeia para uma identidade no diretório do AD do Azure que está associado ao banco de dados. A identidade do Azure AD pode ser uma conta de usuário individual ou um grupo. Para saber mais sobre usuários de bancos de dados independentes, veja [Usuários do bancos de dados independentes - Tornando seu banco de dados portátil](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Os usuários do banco de dados (com exceção dos administradores) não podem ser criados usando o portal do Azure. As funções RBAC não são propagadas para SQL Server, banco de dados SQL ou SQL Data Warehouse. As funções RBAC do Azure são usadas para gerenciar recursos do Azure e não se aplicam às permissões de banco de dados. Por exemplo, a função **Colaborador do SQL Server** não concede acesso para se conectar ao Banco de Dados SQL ou ao SQL Data Warehouse. A permissão de acesso deve ser concedida diretamente no banco de dados usando instruções Transact-SQL.
> [!WARNING]
> Caracteres especiais como dois pontos `:` ou e comercial `&` quando incluídos como nomes de usuário nas instruções CREATE LOGIN e CREATE USER do T-SQL não têm suporte.

Para criar um usuário de banco de dados independente baseado no Azure AD (que não seja o administrador do servidor proprietário do banco de dados), conecte-se ao banco de dados com uma identidade do Azure AD como um usuário com, no mínimo, a permissão **ALTER ANY USER** . Em seguida, use a sintaxe Transact-SQL a seguir:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* pode ser o nome principal de usuário de um usuário do Azure ad ou o nome de exibição de um grupo do Azure AD.

**Exemplos:** para criar um usuário de banco de dados independente que representa um usuário de domínio federado ou gerenciado pelo Azure AD:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Para criar um usuário de banco de dados independente representando um grupo de domínio federado ou do Azure AD, forneça o nome de exibição de um grupo de segurança:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Para criar um usuário de banco de dados independente que representa um aplicativo que se conectará usando um token do Azure AD:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!NOTE]
> Esse comando requer que o SQL Access do Azure AD (o "provedor externo") em nome do usuário conectado. Às vezes, ocorrerão circunstâncias que fazem com que o Azure AD retorne uma exceção para o SQL. Nesses casos, o usuário verá o erro SQL 33134, que deve conter a mensagem de erro específica do AAD. Na maioria das vezes, o erro dirá que o acesso foi negado ou que o usuário deve se registrar no MFA para acessar o recurso ou que o acesso entre aplicativos primários deve ser tratado por meio de autorização. Nos dois primeiros casos, o problema geralmente é causado pelas políticas de acesso condicional que são definidas no locatário do AAD do usuário: elas impedem que o usuário acesse o provedor externo. Atualizar as políticas de autoridade de certificação para permitir o acesso ao aplicativo ' 00000002-0000-0000-C000-000000000000 ' (a ID do aplicativo do API do Graph do AAD) deve resolver o problema. No caso de o erro dizer que o acesso entre os aplicativos primários deve ser manipulado por meio de autorização, o problema ocorre porque o usuário está conectado como uma entidade de serviço. O comando deverá ter sucesso se for executado por um usuário.

> [!TIP]
> Você não pode criar um usuário diretamente de um Azure Active Directory que não seja o Azure Active Directory associado à sua assinatura do Azure. No entanto, os membros de outros diretórios ativos que são importados por usuários no Active Directory associado (conhecido como usuários externos) podem ser adicionados a um grupo de Active Directory no Active Directory de locatário. Ao criar um usuário de banco de dados independente para esse grupo do AD, os usuários do Active Directory externo podem obter acesso ao banco de dados SQL.

Para obter mais informações sobre como criar usuários de banco de dados independente baseados em identidades do Active Directory do Azure, consulte [CRIAR USUÁRIO (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Remover o administrador de Azure Active Directory do SQL Server do Azure impede que qualquer usuário de autenticação do Azure AD se conecte ao servidor. Se for necessário, os usuários do Azure AD inutilizáveis poderão ser removidos manualmente por um administrador do Banco de Dados SQL.
> [!NOTE]
> Se você receber um **tempo limite de conexão expirado**, talvez seja necessário definir o parâmetro `TransparentNetworkIPResolution` da cadeia de conexão como false. Para obter mais informações, consulte [problema de tempo limite de conexão com .NET Framework 4.6.1-TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/).

Quando você cria um usuário de banco de dados, esse usuário recebe a permissão **Connect** e pode se conectar a esse banco de dados como um membro da função **Public** . Inicialmente, as únicas permissões disponíveis para o usuário são permissões concedidas à função **pública** ou quaisquer permissões concedidas a quaisquer grupos do Azure ad dos quais eles são membros. Depois de provisionar um usuário de banco de dados independente baseado no Azure AD, você pode conceder ao usuário permissões adicionais, da mesma maneira que concede permissão a qualquer outro tipo de usuário. Normalmente, conceda permissões a funções de banco de dados e adicione usuários a funções. Para saber mais, confira [Noções básicas sobre permissões do Mecanismo de Banco de Dados](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Para obter mais informações sobre funções especiais do Banco de dados SQL, veja [Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md).
Uma conta de usuário de domínio federado que é importada para um domínio gerenciado como um usuário externo, deve usar a identidade de domínio gerenciada.

> [!NOTE]
> Os usuários do Azure AD são marcados nos metadados do banco de dados com o tipo E (EXTERNAL_USER) e para grupos com o tipo X (EXTERNAL_GROUPS). Para obter mais informações, consulte [Sys. database_principals](https://msdn.microsoft.com/library/ms187328.aspx).
>

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Conectar-se ao banco de dados do usuário ou data warehouse usando o SSMS ou SSDT  

Para confirmar que o administrador do Azure AD está configurado corretamente, conecte-se ao banco de dados **mestre** usando a conta de administrador do Azure AD.
Para provisionar um usuário de banco de dados independente com base no Azure AD (que não seja o administrador do servidor que é o proprietário do banco de dados), conecte-se ao banco de dados com uma identidade do Azure AD que tenha acesso ao banco de dados.

> [!IMPORTANT]
> O suporte para autenticação Azure Active Directory está disponível com [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) no Visual Studio 2015. A versão de agosto de 2016 do SSMS também inclui suporte para Universal autenticação do Active Directory, que permite aos administradores exigir Autenticação Multifator utilizando uma chamada telefônica, mensagem de texto, os cartões inteligentes com PIN ou a notificação de aplicativo móvel.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Usando uma identidade do Azure AD para se conectar usando o SSMS ou o SSDT

Os procedimentos a seguir mostram como se conectar a um banco de dados SQL com uma identidade do Azure AD usando as ferramentas de banco de dados SQL Server Management Studio ou SQL Server.

### <a name="active-directory-integrated-authentication"></a>Active Directory autenticação integrada

Use este método se você efetuou logon no Windows usando suas credenciais do Azure Active Directory por meio de um domínio federado.

1. Inicie o Management Studio ou as ferramentas de dados e, na caixa de diálogo **conectar ao servidor** (ou **conectar ao mecanismo de banco de dados**), na caixa **autenticação** , selecione **integrado ao Active Directory**. Nenhuma senha é necessária ou pode ser inserida porque suas credenciais existentes serão apresentadas para a conexão.

    ![Selecionar Autenticação integrada do AD][11]
2. Selecione o botão **Opções** e, na página **Propriedades da conexão** , na caixa **conectar ao banco de dados** , digite o nome do banco de dados de usuário ao qual você deseja se conectar. (A opção **nome de domínio do AD ou ID do locatário**"só tem suporte para opções de **conexão universal com MFA** ; caso contrário, ela fica esmaecida.)  

    ![Selecione o nome do banco de dados][13]

## <a name="active-directory-password-authentication"></a>Active Directory autenticação de senha

Use esse método ao se conectar com um nome de entidade do AD do Azure usando o domínio gerenciado pelo Azure AD. Você também pode usá-lo para contas federadas sem acesso ao domínio, por exemplo, ao trabalhar remotamente.

Use este método para autenticar o banco de BD SQL/DW com o Azure AD para usuários nativos ou federados do Azure AD. Um usuário nativo é explicitamente criado no Azure AD e sendo autenticado usando o nome de usuário e a senha, enquanto um usuário federado é um usuário do Windows cujo domínio é federado com o Azure AD. O último método (usando o usuário & senha) pode ser usado quando um usuário deseja usar sua credencial do Windows, mas seu computador local não está ingressado no domínio (por exemplo, usando um acesso remoto). Nesse caso, um usuário do Windows pode indicar sua conta de domínio e senha e pode autenticar no BD/DW do SQL usando credenciais federadas.

1. Inicie o Management Studio ou o Data Tools e, na caixa de diálogo **conectar ao servidor** (ou **conectar ao mecanismo de banco de dados**), na caixa **autenticação** , selecione **Active Directory-senha**.
2. Na caixa **nome de usuário** , digite o nome de usuário do Azure Active Directory no formato nome de usuário **\@domain. com**. Os nomes de usuário devem ser uma conta do Azure Active Directory ou uma conta de um domínio federado com o Azure Active Directory.
3. Na caixa **Senha** , digite sua senha de usuário para a conta do Azure Active Directory ou conta de domínio federado.

    ![Selecionar autenticação de senha do AD][12]
4. Selecione o botão **Opções** e, na página **Propriedades da conexão** , na caixa **conectar ao banco de dados** , digite o nome do banco de dados de usuário ao qual você deseja se conectar. (Veja o gráfico na opção anterior.)

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Usando uma identidade do Azure AD para se conectar de um aplicativo cliente

Os procedimentos a seguir mostram como se conectar a um banco de dados SQL com uma identidade do Azure AD de um aplicativo cliente.

### <a name="active-directory-integrated-authentication"></a>Active Directory autenticação integrada

Para usar a autenticação integrada do Windows, o Active Directory de seu domínio deve ser federado com o Azure Active Directory. Seu aplicativo cliente (ou um serviço) conectando-se ao banco de dados deve estar em execução em um computador ingressado no domínio sob as credenciais de domínio de um usuário.

Para se conectar a um banco de dados usando a autenticação integrada e uma identidade do Azure AD, a palavra-chave de autenticação na cadeia de conexão do banco de dados deve ser definida como Active Directory integrada. O exemplo C# de código a seguir usa ADO .net.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

A palavra-chave da cadeia de conexão ``Integrated Security=True`` não tem suporte para conexão com o banco de dados SQL do Azure. Ao fazer uma conexão ODBC, você precisará remover os espaços e definir a autenticação como ' ActiveDirectoryIntegrated '.

### <a name="active-directory-password-authentication"></a>Active Directory autenticação de senha

Para conectar-se a um banco de dados usando a autenticação integrada e uma identidade do Azure AD, a palavra-chave de Autenticação deve ser definida como Senha do Active Directory. A cadeia de conexão deve conter os valores e as palavras-chave ID do usuário/UID e password/PWD. O exemplo C# de código a seguir usa ADO .net.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Saiba mais sobre métodos de autenticação do Azure AD usando os exemplos de código de demonstração disponíveis em [Demonstração do GitHub de Autenticação do Azure AD](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Token do Azure AD

Esse método de autenticação permite que os serviços de camada intermediária se conectem ao banco de dados SQL do Azure ou ao Azure SQL Data Warehouse obtendo um token de Azure Active Directory (AAD). Ele permite cenários sofisticados, incluindo a autenticação baseada em certificado. Você deve concluir quatro etapas básicas para usar a autenticação de token do Azure AD:

1. Registre seu aplicativo com Azure Active Directory e obtenha a ID do cliente para seu código.
2. Crie um usuário de banco de dados que representa o aplicativo. (Concluído anteriormente na etapa 6.)
3. Criar um certificado no computador cliente executa o aplicativo.
4. Adicione o certificado como uma chave para seu aplicativo.

Exemplo de cadeia de conexão:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Para obter mais informações, confira [Blog de segurança do SQL Server](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Para obter informações sobre como adicionar um certificado, consulte Introdução [à autenticação baseada em certificado no Azure Active Directory](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

As instruções a seguir, conexão usando a versão 13.1 do sqlcmd, que está disponível no [Centro de Download](https://go.microsoft.com/fwlink/?LinkID=825643).

> [!NOTE]
> `sqlcmd` com o comando `-G` não funciona com as identidades do sistema e requer um logon principal do usuário.

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="next-steps"></a>Próximos passos

- Para obter uma visão geral do acesso e controle no banco de dados SQL, consulte [acesso e controle do banco de dados SQL](sql-database-control-access.md).
- Para obter uma visão geral de logons, usuários e funções de banco de dados no banco de dados SQL, consulte [logons, usuários e funções de banco](sql-database-manage-logins.md)de dados.
- Para obter mais informações sobre entidades de banco de dados, consulte [entidades de segurança](https://msdn.microsoft.com/library/ms181127.aspx).
- Para obter mais informações sobre funções de banco de dados, consulte [funções de banco de dados](https://msdn.microsoft.com/library/ms189121.aspx).
- Para obter mais informações sobre as regras de firewall no Banco de Dados SQL, confira [Regras de firewall de Banco de Dados SQL](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png
