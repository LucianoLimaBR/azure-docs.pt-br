---
title: Logs de auditoria do banco de dados do Azure para MySQL
description: Descreve os logs de auditoria disponíveis no banco de dados do Azure para MySQL e os parâmetros disponíveis para habilitar os níveis de log.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: 42881fcb12f29ec14bbdc0ec4942b2eef17c7312
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72434411"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Logs de auditoria no banco de dados do Azure para MySQL

No banco de dados do Azure para MySQL, o log de auditoria está disponível para os usuários. O log de auditoria pode ser usado para acompanhar a atividade no nível do banco de dados e é normalmente usado para conformidade.

> [!IMPORTANT]
> A funcionalidade de log de auditoria está atualmente em visualização.

## <a name="configure-audit-logging"></a>Configurar o log de auditoria

Por padrão, o log de auditoria é desabilitado. Para habilitá-lo, defina `audit_log_enabled` como ON.

Outros parâmetros que você pode ajustar incluem:

- `audit_log_events`: controla os eventos a serem registrados. Consulte a tabela abaixo para ver eventos de auditoria específicos.
- `audit_log_include_users`: usuários do MySQL a serem incluídos para registro em log. O valor padrão para esse parâmetro é vazio, o que incluirá todos os usuários para registro em log. Isso tem prioridade mais alta sobre `audit_log_exclude_users`. O comprimento máximo do parâmetro é de 512 caracteres.
> [!Note]
> `audit_log_include_users` tem prioridade mais alta sobre `audit_log_exclude_users`, por exemplo, se audit_log_include_users = `demouser` e audit_log_exclude_users = `demouser`, ele fará a auditoria dos logs porque `audit_log_include_users` tem prioridade mais alta.
- `audit_log_exclude_users`: usuários do MySQL a serem excluídos do registro em log. O comprimento máximo do parâmetro é de 512 caracteres.

> [!Note]
> Para `sql_text`, o log será truncado se exceder 2048 caracteres.

| **Evento** | **Descrição** |
|---|---|
| `CONNECTION` | -Início da conexão (bem-sucedido ou malsucedido) <br> -Reautenticação do usuário com usuário/senha diferente durante a sessão <br> -Terminação de conexão |
| `DML_SELECT`| SELECIONAR consultas |
| `DML_NONSELECT` | INSERIR/excluir/atualizar consultas |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Consultas como "remover banco de dados" |
| `DCL` | Consultas como "conceder permissão" |
| `ADMIN` | Consultas como "mostrar STATUS" |
| `GENERAL` | Tudo em DML_SELECT, DML_NONSELECT, DML, DDL, DCL e ADMIN |
| `TABLE_ACCESS` | -Disponível somente para MySQL 5,7 <br> -Instruções de leitura de tabela, como selecionar ou inserir em... Não <br> -Instruções DELETE de tabela, como DELETE ou TRUNCATE TABLE <br> -Instruções INSERT de tabela, como INSERT ou REPLACE <br> -Instruções de atualização de tabela, como UPDATE |

## <a name="access-audit-logs"></a>Acesse os logs de auditoria

Os logs de auditoria são integrados a Azure Monitor logs de diagnóstico. Depois de habilitar os logs de auditoria no servidor MySQL, você pode emiti-los para Azure Monitor logs, hubs de eventos ou armazenamento do Azure. Para saber mais sobre como habilitar os logs de diagnóstico no portal do Azure, consulte o [artigo portal de log de auditoria](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Esquemas de logs de diagnóstico

As seções a seguir descrevem as saídas dos logs de auditoria do MySQL com base no tipo de evento. Dependendo do método de saída, os campos incluídos e a ordem em que aparecem podem variar.

### <a name="connection"></a>Conexão

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua ID de locatário |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Carimbo de data/hora quando o log foi gravado, em UTC |
| `Type` | Tipo do log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID para a assinatura a que o servidor pertence |
| `ResourceGroup` | Nome do grupo de recursos ao qual o servidor pertence |
| `ResourceProvider` | Nome do provedor de recursos. Sempre `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de recurso |
| `Resource` | Nome do servidor |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nome do servidor |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT`, `CHANGE USER` (disponível somente para MySQL 5,7) |
| `connection_id_d` | ID de conexão exclusiva gerada pelo MySQL |
| `host_s` | Em branco |
| `ip_s` | Endereço IP do cliente que se conecta ao MySQL |
| `user_s` | Nome do usuário executando a consulta |
| `db_s` | Nome do banco de dados conectado a |
| `\_ResourceId` | URI de recurso |

### <a name="general"></a>Geral

O esquema a seguir se aplica aos tipos de evento geral, DML_SELECT, DML_NONSELECT, DML, DDL, DCL e ADMIN.

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua ID de locatário |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Carimbo de data/hora quando o log foi gravado, em UTC |
| `Type` | Tipo do log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID para a assinatura a que o servidor pertence |
| `ResourceGroup` | Nome do grupo de recursos ao qual o servidor pertence |
| `ResourceProvider` | Nome do provedor de recursos. Sempre `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de recurso |
| `Resource` | Nome do servidor |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nome do servidor |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR`, `RESULT` (disponível somente para MySQL 5,6) |
| `event_time` | Hora de início da consulta no carimbo de data/hora UTC |
| `error_code_d` | Código de erro se a consulta falhar. `0` significa sem erro |
| `thread_id_d` | ID do thread que executou a consulta |
| `host_s` | Em branco |
| `ip_s` | Endereço IP do cliente que se conecta ao MySQL |
| `user_s` | Nome do usuário executando a consulta |
| `sql_text_s` | Texto de consulta completo |
| `\_ResourceId` | URI de recurso |

### <a name="table-access"></a>Acesso à tabela

| **Propriedade** | **Descrição** |
|---|---|
| `TenantId` | Sua ID de locatário |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Carimbo de data/hora quando o log foi gravado, em UTC |
| `Type` | Tipo do log. Sempre `AzureDiagnostics` |
| `SubscriptionId` | GUID para a assinatura a que o servidor pertence |
| `ResourceGroup` | Nome do grupo de recursos ao qual o servidor pertence |
| `ResourceProvider` | Nome do provedor de recursos. Sempre `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | URI de recurso |
| `Resource` | Nome do servidor |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Nome do servidor |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT`, `UPDATE` ou `DELETE` |
| `connection_id_d` | ID de conexão exclusiva gerada pelo MySQL |
| `db_s` | Nome do banco de dados acessado |
| `table_s` | Nome da tabela acessada |
| `sql_text_s` | Texto de consulta completo |
| `\_ResourceId` | URI de recurso |

## <a name="next-steps"></a>Próximos passos

- [Como configurar logs de auditoria no portal do Azure](howto-configure-audit-logs-portal.md)