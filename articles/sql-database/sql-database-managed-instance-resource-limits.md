---
title: Limites de recursos do Banco de Dados SQL do Azure - instância gerenciada | Microsoft Docs
description: Este artigo fornece uma visão geral dos limites de recursos do Banco de Dados SQL do Azure para instâncias gerenciadas.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 10/02/2019
ms.openlocfilehash: 17ffc07bb5632b1b56b7bff1e843e5955d396089
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72372207"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Visão geral dos limites de recursos de instância gerenciada do banco de dados SQL

Este artigo fornece uma visão geral das características técnicas e dos limites de recursos para a instância gerenciada do banco de dados SQL do Azure e fornece informações sobre como solicitar um aumento desses limites.

> [!NOTE]
> Para diferenças em recursos suportados e instruções T-SQL, consulte [Diferenças de recursos](sql-database-features.md) e [Suporte à instrução T-SQL](sql-database-managed-instance-transact-sql-information.md). Para obter a diferença geral entre as camadas de serviço em um único banco de dados e instância gerenciada, consulte [comparação da camada de serviço](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="hardware-generation-characteristics"></a>Características de geração de hardware

A instância gerenciada tem características e limites de recursos que dependem da infraestrutura e da arquitetura subjacentes. A instância gerenciada do banco de dados SQL do Azure pode ser implantada em duas gerações de hardware: Gen4 e Gen5. As gerações de hardware têm características diferentes, conforme descrito na tabela a seguir:

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| Hardware | Processadores Intel E5-2673 v3 (Haswell) de 2,4 GHz, SSD ligado vCore = 1 PP (núcleo físico) | Processadores Intel E5-2673 v4 (Broadwell) 2,3 GHz, SSD NVMe rápido, vCore=1 LP (hyper-thread) |
| Número de vCores | 8, 16, 24 vCores | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| Memória máxima (taxa de memória/núcleo) | 7 GB por vCore<br/>Adicione mais vCores para obter mais memória. | 5.1 GB por vCore<br/>Adicione mais vCores para obter mais memória. |
| Memória máxima OLTP na memória | Limite de instância: 1 a 1,5 GB por vCore| Limite de instância: 0,8 a 1,65 GB por vCore |
| Armazenamento reservado de instância máx. |  Uso Geral: 8 TB<br/>Comercialmente Crítico: 1 TB | Uso Geral: 8 TB<br/> Comercialmente Crítico 1 TB, 2 TB ou 4 TB, dependendo do número de núcleos |

> [!IMPORTANT]
> - O hardware Gen4 está sendo desativado. É recomendável implantar novas instâncias gerenciadas em hardware Gen5.
> - O hardware do Gen4 no momento ainda está disponível apenas nas seguintes regiões: Europa Setentrional, Europa Ocidental, leste dos EUA, Sul EUA Central, norte EUA Central, oeste dos EUA 2, EUA Central, Canadá central, sul da Índia, Sudeste Asiático e Coreia central.

### <a name="in-memory-oltp-available-space"></a>Espaço disponível no OLTP na memória 

A quantidade de espaço OLTP na memória na camada de serviço [comercialmente crítico](sql-database-service-tier-business-critical.md) depende do número de vCores e da geração de hardware. Na tabela a seguir estão listados os limites de memória que podem ser usados para objetos OLTP na memória.

| Espaço OLTP na memória  | **Gen5** | **Gen4** |
| --- | --- | --- |
| 4 vCores  | 3,14 GB | |   
| 8 vCores  | 6,28 GB | 8 GB |
| 16 vCores | 15,77 GB | 20 GB |
| 24 vCores | 25,25 GB | 36 GB |
| 32 vCores | 37,94 GB | |
| 40 vCores | 52,23 GB | |
| 64 vCores | 99,9 GB    | |
| 80 vCores | 131,68 GB| |

## <a name="service-tier-characteristics"></a>Características de camada de serviço

A instância gerenciada tem duas camadas de serviço: [uso geral](sql-database-service-tier-general-purpose.md) e [comercialmente crítico](sql-database-service-tier-business-critical.md). Essas camadas fornecem [recursos diferentes](sql-database-service-tiers-general-purpose-business-critical.md), conforme descrito na tabela a seguir.

> [!Important]
> Comercialmente Crítico camada de serviço fornece uma cópia interna adicional da instância (réplica secundária) que pode ser usada para a carga de trabalho somente leitura. Se você puder separar consultas de leitura/gravação e consultas de somente leitura/análise/relatório, você receberá duas vezes vCores e memória pelo mesmo preço. A réplica secundária pode atrasar alguns segundos atrás da instância primária, portanto, ela foi projetada para descarregar a carga de trabalho de relatório/análise que não precisa de um estado de dados atual exato. Na tabela a seguir, as **consultas somente leitura** são as consultas que são executadas na réplica secundária.

| **Recurso** | **Uso geral** | **Comercialmente Crítico** |
| --- | --- | --- |
| Número de vCores\* | Gen4: 8, 16, 24<br/>Gen5:4, 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24 <br/> Gen5:4, 8, 16, 24, 32, 40, 64, 80 <br/>\*Same número de vCores é dedicado para consultas somente leitura. |
| Memória máxima | Gen4:56 GB-168 GB (7 GB/vCore)<br/>Gen5:20,4 GB-408 GB (5.1 GB/vCore)<br/>Adicione mais vCores para obter mais memória. | Gen4:56 GB-168 GB (7 GB/vCore)<br/>Gen5:20,4 GB-408 GB (5.1 GB/vCore) para consultas de leitura/gravação<br/>+ 20,4 GB adicionais-408 GB (5.1 GB/vCore) para consultas somente leitura.<br/>Adicione mais vCores para obter mais memória. |
| Tamanho máximo de armazenamento de instância (reservado) | -2 TB para 4 vCores (somente Gen5)<br/>-8 TB para outros tamanhos | Gen4:1 TB <br/> Gen5: <br/>-1 TB para 4, 8, 16 vCores<br/>- 2 TB para 24 vCores<br/>- 4 TB para 32, 40, 64, 80 vCores |
| Tamanho máximo do banco de dados | Até o tamanho da instância disponível no momento (máximo de 2 TB-8 TB, dependendo do número de vCores). | Até o tamanho da instância disponível no momento (máximo de 1 TB-4 TB, dependendo do número de vCores). |
| Tamanho máximo de TempDB | Limitado a 24 GB/vCore (96-1.920 GB) e ao tamanho do armazenamento de instância disponível no momento.<br/>Adicione mais vCores para obter mais espaço de TempDB.<br/> O tamanho do arquivo de log é limitado a 120 GB.| Até o tamanho do armazenamento de instância disponível no momento. |
| Número máximo de bancos de dados por instância | 100, a menos que o limite de tamanho do armazenamento de instância tenha sido atingido. | 100, a menos que o limite de tamanho do armazenamento de instância tenha sido atingido. |
| Número máximo de arquivos de banco de dados por instância | Até 280, a menos que o limite de tamanho do armazenamento de instância ou [do espaço de alocação do armazenamento em disco Premium do Azure](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files) tenha sido atingido. | 32.767 arquivos por banco de dados, a menos que o limite de tamanho do armazenamento de instância tenha sido atingido. |
| Tamanho máximo do arquivo de dados | Limitado ao tamanho de armazenamento da instância disponível no momento (máx. 2 TB-8 TB) e ao [espaço de alocação do armazenamento em disco Premium do Azure](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files). | Limitado ao tamanho de armazenamento de instância disponível no momento (até 1 TB-4 TB). |
| Tamanho máximo do arquivo de log | Limitado a 2 TB e tamanho de armazenamento de instância disponível no momento. | Limitado a 2 TB e tamanho de armazenamento de instância disponível no momento. |
| Dados/Log IOPS (aproximado) | Até 30-40 K IOPS por instância *, 500-7500 por arquivo<br/>\*[aumentar o tamanho do arquivo para obter mais IOPS](#file-io-characteristics-in-general-purpose-tier)| 5,5 k-110 K (1375 IOPS/vCore)<br/>Adicione mais vCores para obter melhor desempenho de e/s. |
| Limite de taxa de transferência de gravação de log (por instância) | 3 MB/s por vCore<br/>Máximo de 22 MB/s | 4 MB/s por vCore<br/>Máx. de 48 MB/s |
| Taxa de transferência de dados (aproximada) | 100 – 250 MB/s por arquivo<br/>\*[aumentar o tamanho do arquivo para obter melhor desempenho de e/s](#file-io-characteristics-in-general-purpose-tier) | Não limitado. |
| Latência de e/s de armazenamento (aproximada) | 5-10 ms | 1-2 ms |
| OLTP na memória | Sem suporte | Disponível, o [tamanho depende do número de vCore](#in-memory-oltp-available-space) |
| Máx. de sessões | 30000 | 30000 |
| [Réplicas somente leitura](sql-database-read-scale-out.md) | 0 | 1 (incluído no preço) |

> [!NOTE]
> - O **tamanho do armazenamento de instância disponível no momento** é a diferença entre o tamanho da instância reservada e o espaço de armazenamento usado.
> - O tamanho do arquivo de log e de dados nos bancos de dados de sistema e de usuário são incluídos no tamanho de armazenamento de instância que é comparado ao limite de tamanho de armazenamento máximo. Usar a exibição do sistema <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys. master_files</a> para determinar o total o espaço usado pelos bancos de dados. Logs de erros não são persistentes e não são incluídos no tamanho. Backups não são incluídos no tamanho de armazenamento.
> - A taxa de transferência e o IOPS na camada de Uso Geral também dependem do [tamanho do arquivo](#file-io-characteristics-in-general-purpose-tier) que não é explicitamente limitado pela instância gerenciada.
> - Você pode criar outra réplica legível em uma região do Azure diferente usando grupos de failover automático.
> - IOPS de instância máxima dependem do layout do arquivo e da distribuição da carga de trabalho. Por exemplo, se você criar arquivos de 7 x 1GB com IOPS de 5K máximo cada e 7 arquivos pequenos (menores que 128 GB) com 500 IOPS cada, você poderá obter 38500 IOPS por instância (7x5000 + 7x500) se sua carga de trabalho puder usar todos os arquivos. Observe que alguma quantidade de IOPS também é usada para backups automáticos.

> [!NOTE]
> Encontre mais informações sobre os [limites de recursos em pools de instâncias gerenciadas neste artigo](sql-database-instance-pools.md#instance-pools-resource-limitations).

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Características de e/s de arquivo na camada de Uso Geral

No Uso Geral camada de serviço, cada arquivo de banco de dados está obtendo IOPS dedicados e taxa de transferência que depende do tamanho do arquivo. Arquivos maiores estão obtendo mais IOPS e taxa de transferência. As características de e/s dos arquivos de banco de dados são mostradas na tabela a seguir:

| Tamanho do arquivo           | 0-128 GiB | 128-256 GiB | 256-512 GiB | 0,5-1 TiB    | 1-2 TiB    | 2-4 TiB | 4-8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| IOPS por arquivo       | 500   | 1100 | 2\.300              | 5\.000              | 7500              | 7500              | 12.500   |
| Taxa de transferência por arquivo | 100 MiB/s | 125 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s | 480 MiB/s | 

Se você notar alta latência de e/s em algum arquivo de banco de dados ou se perceber que o IOPS/taxa de transferência está atingindo o limite, você pode melhorar [o desempenho aumentando o tamanho do arquivo](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337).

Também há limites no nível da instância, como taxa de transferência máxima de gravação de log 22 MB/s, portanto, talvez você não consiga acessar o arquivo em todo o arquivo de log porque você está atingindo o limite de taxa de transferência da instância.

## <a name="supported-regions"></a>Regiões com suporte

As instâncias gerenciadas podem ser criadas somente em [regiões com suporte](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Para criar uma instância gerenciada em uma região que não tem suporte no momento, você pode [Enviar uma solicitação de suporte por meio do portal do Azure](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Tipos de assinatura com suporte

Atualmente, a instância gerenciada dá suporte à implantação somente nos seguintes tipos de assinaturas:

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pague-conforme-o-uso](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Provedor de serviços de nuvem (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Desenvolvimento/Teste Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Desenvolvimento/Teste pré-pago](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Assinaturas com crédito Azure mensal para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Limitações de recursos regionais

Os tipos de assinatura suportados podem conter um número limitado de recursos por região. A instância gerenciada tem dois limites padrão por região do Azure (que podem ser aumentadas sob demanda por meio da criação de uma [solicitação de suporte especial no portal do Azure](#obtaining-a-larger-quota-for-sql-managed-instance)), dependendo de um tipo de assinatura:

- **Limite de sub-rede**: o número máximo de sub-redes nas quais as instâncias gerenciadas são implantadas em uma única região.
- **limite de unidade VCORE**: o número máximo de unidades VCORE que podem ser implantadas em todas as instâncias em uma única região. Um vCore da GP usa uma unidade vCore e um vCore de BC usa 4 unidades vCore. O número total de instâncias não é limitado, desde que esteja dentro do limite da unidade vCore.

> [!Note]
> Esses limites são configurações padrão e não limitações técnicas. Os limites podem ser aumentados sob demanda criando uma solicitação especial [de suporte no portal do Azure](#obtaining-a-larger-quota-for-sql-managed-instance) se você precisar de mais instâncias gerenciadas na região atual. Como alternativa, você pode criar novas instâncias gerenciadas em outra região do Azure sem enviar solicitações de suporte.

A tabela a seguir mostra os **limites regionais padrão** para os tipos de assinatura com suporte (os limites padrão podem ser estendidos usando a solicitação de suporte descrita abaixo):

|Tipo de assinatura| Número máximo de sub-redes de instância gerenciada | Número máximo de unidades vCore * |
| :---| :--- | :--- |
|Pós-pago|3|320|
|CSP |8 (15 em algumas regiões * *)|960 (1440 em algumas regiões * *)|
|Desenvolvimento/Teste pago conforme o uso|3|320|
|Desenvolvimento/Teste Enterprise|3|320|
|EA|8 (15 em algumas regiões * *)|960 (1440 em algumas regiões * *)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional e Plataformas MSDN|2|32|

\* em implantações de planejamento, leve em consideração que a camada de serviço Comercialmente Crítico (BC) requer quatro (4) vezes mais capacidade de vCore que a camada de serviço de Uso Geral (GP). Por exemplo: 1 GP vCore = 1 unidade vCore e 1 BC vCore = 4 unidades vCore. Para simplificar a análise de consumo em relação aos limites padrão, resuma as unidades vCore em todas as sub-redes na região em que as instâncias gerenciadas são implantadas e compare os resultados com os limites de unidade de instância para o tipo de assinatura. O limite **máximo de unidades vCore** aplica-se a cada assinatura em uma região. Não há limite por sub-redes individuais, exceto que a soma de todos os vCores implantados em várias sub-redes deve ser menor ou igual ao **número máximo de unidades vCore**.

\* @ no__t-1 a sub-rede maior e os limites de vCore estão disponíveis nas seguintes regiões: leste da Austrália, leste dos EUA, leste dos EUA 2, Europa Setentrional, Sul EUA Central, Sudeste Asiático, Sul do Reino Unido, Europa Ocidental, oeste dos EUA 2.

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Obtendo uma cota maior para a instância gerenciada do SQL

Se você precisar de mais instâncias gerenciadas em suas regiões atuais, envie uma solicitação de suporte para estender a cota usando o portal do Azure.
Para iniciar o processo de obtenção de uma cota maior:

1. Abra **ajuda + suporte**e clique em **nova solicitação de suporte**.

   ![Ajuda e Suporte](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. Na guia Básico da nova solicitação de suporte:
   - Para **tipo de problema**, selecione **Limites de serviço e assinatura (cotas)** .
   - Em **Assinatura**, selecione sua assinatura.
   - Para **tipo de cota**, selecione **instância gerenciada do banco de dados do SQL**.
   - Para **plano de suporte**, selecione o plano de suporte.

     ![Cota de tipo de problema](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Clique em \\**Próximo**.
4. Na **guia problema** da nova solicitação de suporte:
   - Para **Gravidade**, selecione o nível de gravidade do problema.
   - Para **Detalhes**, forneça informações adicionais sobre o seu problema, incluindo mensagens de erro.
   - Para **Upload de arquivo**, anexe um arquivo com mais informações (até 4 MB).

     ![Detalhes do problema](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Uma solicitação válida deve incluir:
     > - Região na qual o limite de assinatura precisa ser aumentado.
     > - Número necessário de vCores, por camada de serviço em sub-redes existentes após o aumento da cota (se qualquer uma das sub-redes existentes precisar ser expandida.
     > - Número necessário de novas sub-redes e número total de vCores por camada de serviço nas novas sub-redes (se você precisar implantar instâncias gerenciadas em novas sub-redes).

5. Clique em \\**Próximo**.
6. Na guia Informações de contato da nova solicitação de suporte, insira o método de contato preferencial (email ou telefone) e os detalhes de contato.
7. Clique em **Criar**.

## <a name="next-steps"></a>Próximos passos

- Para obter mais informações sobre a instância gerenciada, consulte [o que é uma instância gerenciada?](sql-database-managed-instance.md).
- Para saber mais sobre preços, veja [Preços de instância gerenciada do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Para saber como criar sua primeira instância gerenciada, consulte [o guia de início rápido](sql-database-managed-instance-get-started.md).
