---
title: Criar um ponto de extremidade privado do Azure usando CLI do Azure | Microsoft Docs
description: Saiba mais sobre o ponto de extremidade privado do Azure
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 30394ba7b71d7dcb4233e5dca341dda47fd9ffa7
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376326"
---
# <a name="create-a-private-endpoint-using-azure-cli"></a>Criar um ponto de extremidade privado usando CLI do Azure
O ponto de extremidade privado é o bloco de construção fundamental para o link privado no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), se comuniquem de forma privada com recursos de link privado. Neste guia de início rápido, você aprenderá a criar uma VM em uma rede virtual, um servidor de banco de dados SQL com um ponto de extremidade privado usando CLI do Azure. Em seguida, você pode acessar a VM para e acessar com segurança o recurso de link privado (um servidor de banco de dados SQL do Azure privado neste exemplo). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI do Azure localmente, este guia de início rápido exigirá a versão 2.0.28 ou posterior da CLI do Azure. Execute `az --version` para localizar a versão instalada. Para informações sobre como instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupos de recursos

Para poder criar qualquer recurso, você precisa criar um grupo de recursos para hospedar a rede virtual. Crie um grupo de recursos com [az group create](/cli/azure/group). Este exemplo cria um grupo de recursos chamado *MyResource* Group no local *westcentralus* :

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual
Crie uma rede virtual com [AZ Network vnet Create](/cli/azure/network/vnet). Este exemplo cria uma rede virtual padrão chamada *myVirtualNetwork* com uma sub-rede chamada *mysubnet*:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```
## <a name="disable-subnet-private-endpoint-policies"></a>Desabilitar políticas de ponto de extremidade privado de sub-rede 
O Azure implanta recursos em uma sub-rede em uma rede virtual, portanto, você precisa criar ou atualizar a sub-rede para desabilitar as políticas de rede de ponto de extremidade privadas. Atualize uma configuração de sub-rede chamada *mysubnet* Com [AZ Network vnet subnet Update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Criar a VM 
Crie uma VM com AZ VM Create. Quando solicitado, forneça uma senha a ser usada como as credenciais de entrada para a VM. Este exemplo cria uma VM chamada *myVm*: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 Anote o endereço IP público da VM. Você usará esse endereço para conectar-se à VM pela Internet na próxima etapa.

## <a name="create-a-sql-database-server"></a>Criar um servidor de banco de dados SQL 
Crie um servidor de banco de dados SQL com o comando AZ SQL Server CREATE. Lembre-se de que o nome do seu SQL Server deve ser exclusivo no Azure, portanto, substitua o valor do espaço reservado entre colchetes com seu próprio valor exclusivo: 

```azurecli-interactive
# Create a logical server in the resource group 
az sql server create \ 
    --name "myserver"\ 
    --resource-group myResourceGroup \ 
    --location WestUS \ 
    --admin-user "sqladmin" \ 
    --admin-password "CHANGE_PASSWORD_1" 
 
# Create a database in the server with zone redundancy as false 
az sql db create \ 
    --resource-group myResourceGroup  \ 
    --server myserver \ 
    --name mySampleDatabase \ 
    --sample-name AdventureWorksLT \ 
    --edition GeneralPurpose \ 
    --family Gen4 \ 
    --capacity 1 
```

Observe que a ID de SQL Server é semelhante a @ no__t-0, você usará a ID de SQL Server na próxima etapa. 

## <a name="create-the-private-endpoint"></a>Criar o ponto de extremidade privado 
Crie um ponto de extremidade privado para o servidor do banco de dados SQL em sua rede virtual: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<SQL Server ID>" \  
    --group-ids sqlServer \  
    --connection-name myConnection  
 ```
## <a name="configure-the-private-dns-zone"></a>Configurar a zona de DNS privado 
Crie uma zona de DNS privado para o domínio do servidor do banco de dados SQL e crie um link de associação com a rede virtual. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.database.windows.net" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.database.windows.net"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the SQL server name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

## <a name="connect-to-a-vm-from-the-internet"></a>Conectar uma VM a partir da Internet

Conecte-se à VM *myVm* da Internet da seguinte maneira:

1. Na barra de pesquisa do portal, insira *myVm*.

1. Selecione o botão **Conectar**. Depois de selecionar o botão **Conectar**, **Conectar-se à máquina virtual** abre.

1. Selecione **Baixar Arquivo RDP**. O Azure cria um arquivo *.rdp* (protocolo RDP) e ele é baixado no computador.

1. Abra o arquivo downloaded.rdp*.

    1. Se solicitado, selecione **Conectar**.

    1. Insira o nome de usuário e a senha que você especificou ao criar a VM.

        > [!NOTE]
        > Talvez seja necessário selecionar **Mais escolhas** > **Usar uma conta diferente** para especificar as credenciais inseridas durante a criação da VM.

1. Selecione **OK**.

1. Você pode receber um aviso do certificado durante o processo de logon. Se você receber um aviso de certificado, selecione **Sim** ou **Continuar**.

1. Depois que a área de trabalho da VM for exibida, minimize-a para voltar para sua área de trabalho local.  

## <a name="access-sql-database-server-privately-from-the-vm"></a>Acessar o servidor do banco de dados SQL privadamente da VM

Nesta seção, você se conectará ao servidor do banco de dados SQL da VM usando o ponto de extremidade privado.

 1. Na Área de Trabalho Remota de  *myVM*, abra o PowerShell.
 2. Digite nslookup meuservidor. Database. Windows. net @ no__t-0 você receberá uma mensagem semelhante a esta: 

```
      Server:  UnKnown 
      Address:  168.63.129.16 
      Non-authoritative answer: 
      Name:    myserver.privatelink.database.windows.net 
      Address:  10.0.0.5 
      Aliases:  myserver.database.windows.net 
```
 3. Instalar SQL Server Management Studio 
 4. Em conectar ao servidor, insira ou selecione estas informações: tipo de servidor: selecione Mecanismo de Banco de Dados.
 Nome do servidor: selecione myserver.database.windows.net nome de usuário: Insira um nome de usuário fornecido durante a criação.
 Senha: Insira uma senha fornecida durante a criação.
 Lembrar senha: selecione Sim.
 
 5. Selecione **Conectar**.
 6. Procurar **bancos de dados** no menu à esquerda.
 7. Opcionalmente Criar ou consultar informações de *MyDatabase*
 8. Feche a conexão de área de trabalho remota para *myVm*.

## <a name="clean-up-resources"></a>Limpar recursos 
Quando não for mais necessário, você pode usar AZ Group Delete para remover o grupo de recursos e todos os recursos que ele tem: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Próximos passos
- Saiba mais sobre o [link privado do Azure](private-link-overview.md)
 
