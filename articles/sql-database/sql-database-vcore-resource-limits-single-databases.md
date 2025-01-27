---
title: Limites de recursos baseados em vCore do banco de dados SQL do Azure-banco de dados individual | Microsoft Docs
description: Esta página descreve alguns limites comuns de recursos baseados em vCore para um único banco de dados no banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/21/2019
ms.openlocfilehash: 108e31ebff5910c139bcaf83675cc8161c2a96f6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693358"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-based-purchasing-model"></a>Limites de recursos para bancos de dados individuais usando o modelo de compra baseado em vCore

Este artigo fornece os limites de recursos detalhados para bancos de dados individuais do banco de dados SQL do Azure usando o modelo de compra baseado em vCore.

Para os limites de modelo de compra baseados em DTU para bancos de dados individuais em um servidor de banco de dados SQL, consulte [visão geral dos limites de recursos em um servidor de banco de dados SQL](sql-database-resource-limits-database-server.md).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](sql-database-file-space-management.md).

Você pode definir a camada de serviço, o tamanho da computação e a quantidade de armazenamento para um único banco de dados usando o [portal do Azure](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), o [CLI do Azure](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)ou a [API REST](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Para obter diretrizes e considerações sobre o dimensionamento, consulte [dimensionar um banco de dados individual](sql-database-single-database-scale.md).

## <a name="general-purpose-service-tier-for-provisioned-compute"></a>Uso Geral camada de serviço para computação provisionada

> [!IMPORTANT]
> Novos bancos de dados Gen4 não têm mais suporte nas regiões leste da Austrália ou sul do Brasil.

### <a name="gen4-compute-generation-part-1"></a>Geração de computação Gen4 (parte 1)

|Tamanho da computação|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|Abril|28|35|42|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|1024|1024|1536|1536|1536|3072|
|Tamanho máximo do log (GB)|307|307|461|461|461|922|
|Tamanho máximo de dados de TempDB (GB)|32|64|96|128|160|192|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de e/s (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo (64 KB)|500|1\.000|1500|2000|2500|3000|
|Taxa máxima de logs (MBps)|3,75|7.5|11,25|15|18,75|22,5|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1\.000|1200|
|Máximo de sessões simultâneas|30000|30000|30000|30000|30000|30000|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Expansão de leitura|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

### <a name="gen4-compute-generation-part-2"></a>Geração de computação Gen4 (parte 2)

|Tamanho da computação|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|168|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|3072|3072|3072|3072|4\.096|4\.096|
|Tamanho máximo do log (GB)|922|922|922|922|1229|1229|
|Tamanho máximo de dados de TempDB (GB)|224|256|288|320|384|384|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de e/s (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)
|IOPS de dados máximo (64 KB)|3500|4000|4500|5000|8000|12000|
|Taxa máxima de logs (MBps)|26,25|30|30|30|30|30|
|Máximo de trabalhos simultâneos (solicitações)|1400|1600|1800|2000|3200|4800|
|Máximo de sessões simultâneas|30000|30000|30000|30000|30000|30000|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Expansão de leitura|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

### <a name="gen5-compute-generation-part-1"></a>Geração de computação Gen5 (parte 1)

|Tamanho da computação|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10,2|20,4|30,6|40,8|51|61,2|71,4|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|1024|1024|1536|1536|1536|3072|3072|
|Tamanho máximo do log (GB)|307|307|461|461|461|922|922|
|Tamanho máximo de dados de TempDB (GB)|64|128|192|256|320|384|384|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de e/s (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo (64 KB)|1\.000|2000|3000|4000|5000|6000|7000|
|Taxa máxima de logs (MBps)|3,75|7.5|11,25|15|18,75|22,5|26,25|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1\.000|1200|1400|
|Máximo de sessões simultâneas|30000|30000|30000|30000|30000|30000|30000|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Expansão de leitura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

### <a name="gen5-compute-generation-part-2"></a>Geração de computação Gen5 (parte 2)

|Tamanho da computação|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|anos|20|24|32|40|80|
|Memória (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|3072|3072|3072|4\.096|4\.096|4\.096|4\.096|
|Tamanho máximo do log (GB)|922|922|922|1229|1229|1229|1229|
|Tamanho máximo de dados de TempDB (GB)|384|384|384|384|384|384|384|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de e/s (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo (64 KB)|8000|9000|10000|12000|16000|20000|40000|
|Taxa máxima de logs (MBps)|30|30|30|30|30|30|30|
|Máximo de trabalhos simultâneos (solicitações)|1600|1800|2000|2400|3200|4000|8000|
|Máximo de sessões simultâneas|30000|30000|30000|30000|30000|30000|30000|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Expansão de leitura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

## <a name="general-purpose-service-tier-for-serverless-compute"></a>Uso Geral camada de serviço para computação sem servidor

A [camada de computação sem servidor](sql-database-serverless.md) está em versão prévia.

### <a name="gen5-compute-generation-part-1"></a>Geração de computação Gen5 (parte 1)

|Tamanho da computação|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|
|VCores mín. máx.|0,5-1|0.5-2|0,5-4|0,75 a 6|1,0 a 8|
|Memória mín. máx. (GB)|2.02-3|2.05-6|2.10-12|2,25-18|3,00-24|
|Mínimo de atraso de pausa automática (minutos)|60|60|60|60|60|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|512|1024|1024|1024|1536|
|Tamanho máximo do log (GB)|154|307|307|307|461|
|Tamanho máximo de dados de TempDB (GB)|32|64|128|192|256|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de e/s (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo (64 KB)|500|1\.000|2000|3000|4000|
|Taxa máxima de logs (MBps)|2.5|5,6|10|15|20|
|Máximo de trabalhos simultâneos (solicitações)|75|150|300|450|600|
|Máximo de sessões simultâneas|30000|30000|30000|30000|30000|
|Número de réplicas|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|
|Expansão de leitura|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

### <a name="gen5-compute-generation-part-2"></a>Geração de computação Gen5 (parte 2)

|Tamanho da computação|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|
|VCores mín. máx.|1,25-10|1,50-12|1,75-14|2,00-16|
|Memória mín. máx. (GB)|3,75-30|4.50-36|5,25-42|6.00-48|
|Mínimo de atraso de pausa automática (minutos)|60|60|60|60|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|1536|1536|1536|3072|
|Tamanho máximo do log (GB)|461|461|461|922|
|Tamanho máximo de dados de TempDB (GB)|320|384|448|512|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de e/s (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo (64 KB)|5000|6000|7000|8000|
|Taxa máxima de logs (MBps)|20|20|20|20|
|Máximo de trabalhos simultâneos (solicitações)|750|900|1050|1200|
|Máximo de sessões simultâneas|30000|30000|30000|30000|
|Número de réplicas|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|
|Expansão de leitura|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

## <a name="business-critical-service-tier-for-provisioned-compute"></a>Comercialmente Crítico camada de serviço para computação provisionada

> [!IMPORTANT]
> Novos bancos de dados Gen4 não têm mais suporte nas regiões leste da Austrália ou sul do Brasil.

### <a name="gen4-compute-generation-part-1"></a>Geração de computação Gen4 (parte 1)

|Tamanho da computação|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|Abril|28|35|42|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|1|2|3|4|5|6|
|Tipo de armazenamento|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|1024|1024|
|Tamanho máximo do log (GB)|307|307|307|307|307|307|
|Tamanho máximo de dados de TempDB (GB)|32|64|96|128|160|192|
|Latência de e/s (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo (64 KB)|5000|10000|15000|20000|25000|30000|
|Taxa máxima de logs (MBps)|8|16|24|32|40|48|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1\.000|1200|
|Máximo de logons simultâneos|200|400|600|800|1\.000|1200|
|Máximo de sessões simultâneas|30000|30000|30000|30000|30000|30000|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|
|Expansão de leitura|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

### <a name="gen4-compute-generation-part-2"></a>Geração de computação Gen4 (parte 2)

|Tamanho da computação|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|168|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|7|8|9,5|11|20|36|
|Tipo de armazenamento|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|1024|1024|
|Tamanho máximo do log (GB)|307|307|307|307|307|307|
|Tamanho máximo de dados de TempDB (GB)|224|256|288|320|384|384|
|Latência de e/s (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo (64 KB)|35000|40000|45000|50000|80000|120000|
|Taxa máxima de logs (MBps)|56|64|64|64|64|64|
|Máximo de trabalhos simultâneos (solicitações)|1400|1600|1800|2000|3200|4800|
|Máximo de logons simultâneos (solicitações)|1400|1600|1800|2000|3200|4800|
|Máximo de sessões simultâneas|30000|30000|30000|30000|30000|30000|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|
|Expansão de leitura|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

### <a name="gen5-compute-compute-part-1"></a>Computação de computação Gen5 (parte 1)

|Tamanho da computação|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10,2|20,4|30,6|40,8|51|61,2|71,4|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|1,571|3,142|4,713|6,284|8,655|11, 26|13,397|
|Tamanho máximo de dados (GB)|1024|1024|1536|1536|1536|3072|3072|
|Tamanho máximo do log (GB)|307|307|461|461|461|922|922|
|Tamanho máximo de dados de TempDB (GB)|64|128|192|256|320|384|384|
|Tipo de armazenamento|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Latência de e/s (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo (64 KB)|8000|16000|24.000|32000|40000|48000|56000|
|Taxa máxima de logs (MBps)|12|24|36|48|60|72|84|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1\.000|1200|1400|
|Máximo de logons simultâneos|200|400|600|800|1\.000|1200|1400|
|Máximo de sessões simultâneas|30000|30000|30000|30000|30000|30000|30000|
|Número de réplicas|4|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Expansão de leitura|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

### <a name="gen5-compute-generation-part-2"></a>Geração de computação Gen5 (parte 2)

|Tamanho da computação|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|anos|20|24|32|40|80|
|Memória (GB)|81,6|91,8|102|122,4|163,2|204|408|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|15,768|18,139|20,51|25,252|37,936|52,22|131,64|
|Tamanho máximo de dados (GB)|3072|3072|3072|4\.096|4\.096|4\.096|4\.096|
|Tamanho máximo do log (GB)|922|922|922|1229|1229|1229|1229|
|Tamanho máximo de dados de TempDB (GB)|384|384|384|384|384|384|384|
|Tipo de armazenamento|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|SSD local|
|Latência de e/s (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo (64 KB)|64000|72000|80000|96000|128000|160000|320000|
|Taxa máxima de logs (MBps)|96|96|96|96|96|96|96|
|Máximo de trabalhos simultâneos (solicitações)|1600|1800|2000|2400|3200|4000|8000|
|Máximo de logons simultâneos|1600|1800|2000|2400|3200|4000|8000|
|Máximo de sessões simultâneas|30000|30000|30000|30000|30000|30000|30000|
|Número de réplicas|4|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Expansão de leitura|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|tamanho do banco de BD 1X|

## <a name="hyperscale-service-tier-for-provisioned-compute"></a>Camada de serviço de hiperescala para computação provisionada

### <a name="gen5-compute-generation"></a>Geração de computação Gen5

|Nível de desempenho|HS_Gen5_2|HS_Gen5_4|HS_Gen5_8|HS_Gen5_16|HS_Gen5_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|8|16|24|32|40|80|
|Memória (GB)|10,2|20,4|40,8|81,6|122,4|163,2|204|408|
|[RBPEX](sql-database-service-tier-hyperscale.md#compute) Tamanho|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|
|Suporte a Columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (TB)|100 |100 |100 |100 |100 |100 |100 |100 |
|Tamanho máximo do log (TB)|1 |1 |1 |1 |1 |1 |1 |1 |
|Tamanho máximo de dados de TempDB (GB)|64|128|256|384|384|384|384|384|
|Tipo de armazenamento| [Observação 1](#notes) |[Observação 1](#notes)|[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) | [Observação 1](#notes) |
|IOPS de dados máximo (64 KB)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|
|Latência de e/s (aproximada)|[Observação 3](#notes)|[Observação 3](#notes)|[Observação 3](#notes)|[Observação 3](#notes)|[Observação 3](#notes)|[Observação 3](#notes)|[Observação 3](#notes)|[Observação 3](#notes)|
|Máximo de trabalhos simultâneos (solicitações)|200|400|800|1600|2400|3200|4000|8000|
|Máximo de sessões simultâneas|30000|30000|30000|30000|30000|30000|30000|30000|
|Réplicas secundárias|0-4|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Expansão de leitura|Sim|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Retenção de armazenamento de backup|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|
|||

#### <a name="notes"></a>Observações

**Observação 1**: o hiperescala é uma arquitetura de várias camadas com componentes de armazenamento e computação separados: [arquitetura de camada de serviço de hiperescala](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)

**Observação 2**: a arquitetura em várias camadas de hiperescala tem cache em vários níveis. O IOPS efetivo dependerá da carga de trabalho.

**Observação 3**: a latência é 1-2 ms para dados no cache baseado em SSD RBPEX em réplicas de computação, que armazena em cache as páginas de dados mais usadas. Maior latência de dados recuperados de servidores de páginas.

## <a name="next-steps"></a>Próximos passos

- Para os limites de recursos de DTU para um único banco de dados, consulte [limites de recursos para bancos de dados individuais usando o modelo de compra baseado em DTU](sql-database-dtu-resource-limits-single-databases.md)
- Para limites de recursos vCore para pools elásticos, consulte [limites de recursos para pools elásticos usando o modelo de compra baseado em vCore](sql-database-vcore-resource-limits-elastic-pools.md)
- Para os limites de recursos de DTU para pools elásticos, consulte [limites de recursos para pools elásticos usando o modelo de compra baseado em DTU](sql-database-dtu-resource-limits-elastic-pools.md)
- Para os limites de recurso para instâncias gerenciadas, consulte [limites de recursos de instância gerenciada](sql-database-managed-instance-resource-limits.md).
- Para obter informações sobre limites gerais do Azure, consulte [assinatura do Azure e limites de serviço, cotas e restrições](../azure-subscription-service-limits.md).
- Para obter informações sobre limites de recursos em um servidor de banco de dados, consulte [visão geral dos limites de recursos em um servidor de banco de dados SQL](sql-database-resource-limits-database-server.md) para obter informações sobre os limites nos níveis de servidor e assinatura.
