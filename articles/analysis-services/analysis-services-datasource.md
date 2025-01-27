---
title: Fontes de dados com suporte no Azure Analysis Services | Microsoft Docs
description: Descreve as fontes de fonte de dados com suporte para modelos de dados no Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c6043d9e18c364073ad1ea46e26335577e0ec3b2
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72512451"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>Fontes de dados com suporte no Azure Analysis Services

As fontes de dados e conectores mostrados no Assistente para obter dados ou importação no Visual Studio são mostrados para Azure Analysis Services e SQL Server Analysis Services. No entanto, nem todas as fontes de dados e conectores mostrados têm suporte no Azure Analysis Services. Os tipos de fontes de dados aos quais você pode se conectar dependem de vários fatores, como nível de compatibilidade de modelo, conectores de dados disponíveis, tipo de autenticação, provedores e suporte a gateway de dados local. 

## <a name="azure-data-sources"></a>Fontes de dados do Azure

|Fonte de dados  |Na memória  |DirectQuery  |
|---------|---------|---------|
|Banco de dados SQL do Azure<sup>[2](#azsqlmanaged)</sup>     |   Sim      |    Sim      |
|Azure SQL Data Warehouse     |   Sim      |   Sim       |
|Armazenamento de BLOBs do Azure<sup>[1](#tab1400a)</sup>     |   Sim       |    Não      |
|Armazenamento de tabelas do Azure<sup>[1](#tab1400a)</sup>    |   Sim       |    Não      |
|Azure Cosmos DB<sup>[1](#tab1400a)</sup>     |  Sim        |  Não        |
|Azure Data Lake Store (GEN1)<sup>[1](#tab1400a)</sup>, <sup>[4](#gen2)</sup>      |   Sim       |    Não      |
|Azure HDInsight HDFS<sup>[1](#tab1400a)</sup>     |     Sim     |   Não       |
|Azure HDInsight Spark<sup>[1](#tab1400a)</sup>, <sup>[3](#databricks)</sup>     |   Sim       |   Não       |
||||

<a name="tab1400a">1</a> -somente modelos tabulares 1400 e superiores.   
Há suporte para <a name="azsqlmanaged">2</a> -instância gerenciada do banco de dados SQL do Azure. Como a instância gerenciada é executada na VNet do Azure com um endereço IP privado, o ponto de extremidade público deve ser habilitado na instância. Se não estiver habilitado, um gateway de dados local será necessário.    
Não há <a name="databricks">suporte para o</a> Azure Databricks usando o conector do Spark no momento.   
Não há suporte para <a name="gen2">4</a> -ADLS Gen2 no momento.


@No__t_1 do **provedor**  
Modelos na memória e DirectQuery que se conectam a fontes de dados do Azure usam .NET Framework Provedor de Dados para SQL Server.

## <a name="other-data-sources"></a>Outras fontes de dados

Conectar-se a fontes de dados locais do e do Azure como servidor requer um gateway local. Ao usar um gateway, são necessários provedores de 64 bits.

### <a name="in-memory-and-directquery"></a>Na memória e DirectQuery

|Fonte de dados | Provedor na memória | Provedor DirectQuery |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11,0, provedor de OLE DB da Microsoft para SQL Server, .NET Framework Provedor de Dados para SQL Server | Provedor de Dados .NET Framework para SQL Server |
| SQL Server data warehouse |SQL Server Native Client 11,0, provedor de OLE DB da Microsoft para SQL Server, .NET Framework Provedor de Dados para SQL Server | Provedor de Dados .NET Framework para SQL Server |
| Oracle | Provedor de OLE DB para Oracle, Oracle Provedor de Dados para .NET |Provedor de Dados Oracle para .NET |
| Teradata |Provedor de OLE DB para Teradata, Teradata Provedor de Dados para .NET |Provedor de Dados Teradata para .NET |
| | | |

### <a name="in-memory-only"></a>Somente na memória

|Fonte de dados  |  
|---------|
|Banco de Dados do Access     |  
|Active Directory<sup>[1](#tab1400b)</sup>     |  
|Analysis Services     |  
|Analytics Platform System     |  
|Arquivo CSV  |
|Dynamics CRM<sup>[1](#tab1400b)</sup>     |  
|Pasta de trabalho do Excel     |  
|Exchange<sup>[1](#tab1400b)</sup>     |  
|Pasta<sup>[1](#tab1400b)</sup>     |
|IBM Informix<sup>[1](#tab1400b)</sup> (beta) |
|Documento JSON<sup>[1](#tab1400b)</sup>     |  
|Linhas do binário<sup>[1](#tab1400b)</sup>     | 
|Banco de dados MySQL     | 
|Feed do OData<sup>[1](#tab1400b)</sup>     |  
|Consulta ODBC     | 
|OLE DB     |   
|Banco de dados PostgreSQL<sup>[1](#tab1400b)</sup>    | 
|Objetos do Salesforce<sup>[1](#tab1400b)</sup> |  
|Relatórios do Salesforce<sup>[1](#tab1400b)</sup> |
|SAP HANA<sup>[1](#tab1400b)</sup>    |  
|SAP Business Warehouse<sup>[1](#tab1400b)</sup>    |  
|Lista do SharePoint<sup>[1](#tab1400b)</sup>, <sup>[2](#filesSP)</sup>     |   
|Banco de dados Sybase     |  
|Arquivo TXT  |
|Tabela XML<sup>[1](#tab1400b)</sup>    |  
||
 
<a name="tab1400b">1</a> -somente modelos tabulares 1400 e superiores.   
<a name="filesSP">2</a> -não há suporte para os arquivos no SharePoint local.

## <a name="specifying-a-different-provider"></a>Especificando um provedor diferente

Os modelos de dados no Azure Analysis Services podem exigir provedores de dados diferentes ao se conectar a determinadas fontes de dados. Em alguns casos, os modelos de tabela que se conectam a fontes de dados usando provedores nativos como SQL Server Native Client (SQLNCLI11) podem retornar um erro. Se estiver usando provedores nativos diferentes de SQLOLEDB, você poderá ver a mensagem **de erro: o provedor ' sqlncli 11.1 ' não está registrado**. Ou, se você tiver um modelo DirectQuery conectando-se a fontes de dados locais e usar provedores nativos, poderá ver a mensagem de erro: **erro ao criar OLE DB conjunto de linhas. Sintaxe incorreta próxima a ' LIMIT '** .

Ao migrar um modelo de tabela local do SQL Server Analysis Services para o Azure Analysis Services, talvez seja necessário alterar o provedor.

**Para especificar um provedor**

1. No SSDT > **Gerenciador de Modelo de Tabela** > **Fontes de Dados**, clique com o botão direito em uma conexão de fonte de dados e, em seguida, clique em **Editar Fonte de Dados**.
2. Em **Editar Conexão**, clique em **Avançado** para abrir a janela Propriedades avançadas.
3. Em **Definir Propriedades Avançadas** > **Provedores**, selecione o provedor apropriado.

## <a name="impersonation"></a>Representação
Em alguns casos, pode ser necessário especificar uma conta de representação diferente. A conta de representação pode ser especificada no Visual Studio (SSDT) ou no SSMS.

Para fontes de dados locais:

* Se estiver usando a autenticação SQL, a representação deverá ser a Conta de serviço.
* Se estiver usando a autenticação do Windows, defina o usuário/senha do Windows. Para o SQL Server, há suporte para a autenticação do Windows com uma conta de representação específica apenas para modelos de dados na memória.

Para fontes de dados de nuvem:

* Se estiver usando a autenticação SQL, a representação deverá ser a Conta de serviço.

## <a name="oauth-credentials"></a>Credenciais do OAuth

Para modelos de tabela no nível de compatibilidade 1400 e superior, o banco de dados SQL do Azure, o Azure SQL Data Warehouse, o Dynamics 365 e a lista do SharePoint dão suporte a credenciais OAuth. Azure Analysis Services gerencia a atualização de token para fontes de dados OAuth para evitar tempos limite para operações de atualização de execução longa. Para gerar tokens válidos, defina as credenciais usando o SSMS.

## <a name="next-steps"></a>Próximos passos
@No__t_1 [de gateway local](analysis-services-gateway.md)  
[Gerenciar seu serviço](analysis-services-manage.md)   

