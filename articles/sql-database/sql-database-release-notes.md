---
title: Notas de versão do banco de dados SQL do Azure | Microsoft Docs
description: Saiba mais sobre os novos recursos e aprimoramentos no serviço de banco de dados SQL do Azure e na documentação do banco de dados SQL do Azure
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: sstein
ms.openlocfilehash: f1450399dc027a6977f4c99507e2e15b301272c4
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249380"
---
# <a name="sql-database-release-notes"></a>Notas de versão do banco de dados SQL

Este artigo lista os recursos do banco de dados SQL que estão atualmente em visualização pública. Para atualizações e aprimoramentos do banco de dados SQL, consulte [atualizações do serviço de banco de dados SQL](https://azure.microsoft.com/updates/?product=sql-database). Para obter atualizações e aprimoramentos em outros serviços do Azure, consulte [atualizações de serviço](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Recursos na visualização pública

### <a name="single-databasetabsingle-database"></a>[Banco de dados individual](#tab/single-database)

| Recurso | Detalhes |
| ---| --- |
| [Link privado do Azure](https://azure.microsoft.com/updates/private-link-now-available-in-preview/)| O Link Privado simplifica a arquitetura de rede e protege a conexão entre pontos de extremidade no Azure mantendo os dados na rede Azure, o que elimina a exposição à Internet. O Link Privado também habilita a criação e a renderização de seus próprios serviços no Azure. |
| Recuperação de banco de dados acelerada com bancos de dados individuais e pools elásticos | Para obter informações, consulte [recuperação de banco de dados acelerada](sql-database-accelerated-database-recovery.md).|
|Contagem aproximada distinta|Para obter informações, veja [contagem aproximada distinta](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Modo de lote no repositório de armazenamento (no nível de compatibilidade 150)|Para obter informações, consulte [modo de lote no repositório de armazenamento](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Descoberta de dados e classificação  |Para saber mais, confira [banco de dados SQL do Azure e SQL data warehouse a descoberta de Data & classificação](sql-database-data-discovery-and-classification.md).|
| Trabalhos de banco de dados elástico | Para obter informações, consulte [criar, configurar e gerenciar trabalhos elásticos](elastic-jobs-overview.md). |
| Consultas elásticas | Para obter informações, consulte [visão geral de consulta elástica](sql-database-elastic-query-overview.md). |
| Transações elásticas | [Transações distribuídas entre bancos de dados na nuvem](sql-database-elastic-transactions-overview.md). |
|Comentários de concessão de memória (modo de linha) (em nível de compatibilidade 150)|Para obter informações, consulte [comentários de concessão de memória (modo de linha)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| Editor de consultas no portal do Azure |Para obter informações, consulte [usar o editor de consultas SQL do portal do Azure para se conectar e consultar dados](sql-database-connect-query-portal.md).|
| Serviços de R/Machine Learning com bancos de dados individuais e pools elásticos |Para obter informações, consulte [serviços de Machine Learning no banco de dados SQL do Azure](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
| Camada de computação sem servidor | Para obter informações, consulte [banco de dados SQL sem servidor (visualização)](sql-database-serverless.md).|
|Análise de SQL|Para obter informações, consulte [análise de SQL do Azure](../azure-monitor/insights/azure-sql.md).|
|Compilação adiada da variável de tabela (no nível de compatibilidade 150)|Para obter informações, consulte [compilação adiada da variável de tabela](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| &nbsp; |

### <a name="managed-instancetabmanaged-instance"></a>[Instância Gerenciada](#tab/managed-instance)

| Recurso | Detalhes |
| ---| --- |
| <a href="/azure/sql-database/sql-database-managed-instance-connectivity-architecture#service-aided-subnet-configuration-public-preview-in-east-us-and-west-us">Configuração de sub-rede auxiliada por serviço</a> | Uma maneira segura e conveniente de gerenciar a configuração de sub-rede. |
| <a href="/azure/sql-database/sql-database-instance-pools">Pools de instância</a> | Uma maneira conveniente e econômica de migrar instâncias SQL menores para a nuvem. |
| <a href="https://aka.ms/managed-instance-tde-byok">TDE (Transparent Data Encryption) com Bring Your Own Key (BYOK)</a> |Para obter informações, consulte Transparent Data Encryption do SQL [Azure com chaves gerenciadas pelo cliente no Azure Key Vault: Bring Your Own Key suporte a @ no__t-0.|
| <a href="https://aka.ms/managed-instance-aadlogins">Entidades de segurança de nível de instância do Azure AD Server (logons)</a> | Crie logons no nível de servidor usando a instrução <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Create login from external Provider</a> . |
| [Replicação Transacional](sql-database-managed-instance-transactional-replication.md) | Replique as alterações de suas tabelas em outros bancos de dados colocados em instâncias gerenciadas, bancos de dados individuais ou instâncias de SQL Server, ou atualize suas tabelas quando algumas linhas forem alteradas em outras instâncias gerenciadas ou SQL Server instância. Para obter informações, consulte [Configurar replicação em um banco de dados de instância gerenciada do banco de dados SQL do Azure](replication-with-sql-database-managed-instance.md). |
| Detecção de ameaças |Para obter informações, consulte [Configurar a detecção de ameaças na instância gerenciada do banco de dados SQL do Azure](sql-database-managed-instance-threat-detection.md).|
| Recriar bancos de dados descartados com instâncias gerenciadas |Para obter informações, consulte [recriar bancos de dados descartados no Azure SQL instância gerenciada](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266).|
| &nbsp; |

---

## <a name="new-features"></a>Novos recursos

### <a name="managed-instance-h2-2019-updates"></a>Atualizações 2019 de instância gerenciada H2

- Os [grupos de failover automático](https://azure.microsoft.com/updates/azure-sql-database-auto-failover-groups-feature-now-available-in-all-regions/) permitem replicar todos os bancos de dados da instância primária para uma instância secundária em outra região.
- Configure o comportamento da instância gerenciada com [sinalizadores de rastreamento globais](https://azure.microsoft.com/updates/global-trace-flags-are-now-available-in-azure-sql-database-managed-instance/).

### <a name="managed-instance-h1-2019-updates"></a>Instâncias gerenciadas H1 2019 atualizações

Os seguintes recursos estão habilitados no modelo de implantação de instância gerenciada em H1 2019:
  - Suporte para assinaturas com <a href="https://aka.ms/sql-mi-visual-studio-subscribers">crédito mensal do Azure para assinantes do Visual Studio</a> e [limites regionais](sql-database-managed-instance-resource-limits.md#regional-resource-limitations)maiores.
  - Suporte para <a href="https://docs.microsoft.com/sharepoint/administration/deploy-azure-sql-managed-instance-with-sharepoint-servers-2016-2019"> SharePoint 2016 e SharePoint 2019 </a> e <a href="https://docs.microsoft.com/business-applications-release-notes/october18/dynamics365-business-central/support-for-azure-sql-database-managed-instance"> Dynamics 365 Business Central </a>
  - Crie instâncias com <a href="https://aka.ms/managed-instance-collation">agrupamento de nível de servidor</a> e <a href="https://azure.microsoft.com/updates/managed-instance-time-zone-ga/">zona de tempo</a> de sua escolha.
  - As instâncias gerenciadas agora são protegidas com o <a href="sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md">firewall interno</a>.
  - Configure as instâncias para usar [pontos de extremidade públicos](sql-database-managed-instance-public-endpoint-configure.md), conexão de [substituição de proxy](sql-database-connectivity-architecture.md#connection-policy) para obter melhor desempenho de rede, <a href="https://aka.ms/four-cores-sql-mi-update">4 VCores na geração de hardware Gen5</a> ou <a href="https://aka.ms/managed-instance-configurable-backup-retention">configurar a retenção de backup de até 35 dias para a</a> restauração pontual. A retenção de backup de longo prazo (até 10 anos) ainda não está habilitada para que você possa usar <a href="https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server">backups somente cópia</a> como uma alternativa.
  - As novas funcionalidades permitem que você <a href="https://medium.com/@jocapc/geo-restore-your-databases-on-azure-sql-instances-1451480e90fa">restaure geograficamente seu banco de dados para outro Data Center usando o PowerShell</a>, [renomeie o banco de dados](https://azure.microsoft.com/updates/azure-sql-database-managed-instance-database-rename-is-supported/), [exclua o cluster virtual](sql-database-managed-instance-delete-virtual-cluster.md).
  - A nova função de [colaborador de instância](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-managed-instance-contributor) interna permite a conformidade de SOD (separação de imposto) com princípios de segurança e conformidade com os padrões corporativos.
  - A instância gerenciada está disponível nas seguintes regiões do Azure governamental para GA (US Gov Texas US Gov Arizona), bem como em Norte da China 2 e Leste da China 2. Ele também está disponível nas seguintes regiões públicas: Austrália Central, Austrália Central 2, sul do Brasil, sul da França, EAU Central, Norte dos EAU, norte da África do Sul, oeste da África do Sul.

## <a name="fixed-known-issues"></a>Correção de problemas conhecidos

- Os bancos de dados independentes de **agosto de 2019** têm suporte total na instância gerenciada.

## <a name="updates"></a>Atualizações

Para obter uma lista de atualizações e melhorias do banco de dados SQL, consulte [atualizações do serviço de banco de dados SQL](https://azure.microsoft.com/updates/?product=sql-database).

Para obter atualizações e aprimoramentos em todos os serviços do Azure, consulte [atualizações de serviço](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Contribuir para conteúdo

Para contribuir com a documentação do banco de dados SQL do Azure, consulte o [Guia do colaborador do docs](https://docs.microsoft.com/contribute/).
