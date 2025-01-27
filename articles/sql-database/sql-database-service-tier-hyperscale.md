---
title: Visão geral do hiperescala do banco de dados SQL do Azure | Microsoft Docs
description: Este artigo descreve a camada de serviço de hiperescala no modelo de compra baseado em vCore no banco de dados SQL do Azure e explica como ele é diferente das camadas de serviço Uso Geral e Comercialmente Crítico.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2019
ms.openlocfilehash: df6926a8f50d7ffb2765557cdf75ed6d09b3810b
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428049"
---
# <a name="hyperscale-service-tier"></a>Camada de serviço de hiperescala

O banco de dados SQL do Azure é baseado em SQL Server Mecanismo de Banco de Dados arquitetura ajustada para o ambiente de nuvem a fim de garantir 99,99% de disponibilidade mesmo nos casos de falhas de infraestrutura. Há três modelos de arquitetura que são usados no banco de dados SQL do Azure:
- Uso Geral/Standard 
-  Hiperescala
-  Comercialmente Crítico/Premium

A camada de serviço de hiperescala no banco de dados SQL do Azure é a mais nova camada de serviço no modelo de compra baseado em vCore. Essa camada de serviço é um nível de desempenho de computação e armazenamento altamente escalonável que aproveita a arquitetura do Azure para escalar horizontalmente os recursos de armazenamento e computação para um banco de dados SQL do Azure, muito além dos limites disponíveis para os Uso Geral e os negócios Camadas de serviço críticas.

> 
> [!NOTE]
> Para obter detalhes sobre as camadas de serviço Uso Geral e Comercialmente Crítico no modelo de compra baseado em vCore, consulte [uso geral](sql-database-service-tier-general-purpose.md) e [comercialmente crítico](sql-database-service-tier-business-critical.md) camadas de serviço. Para obter uma comparação do modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte [recursos e modelos de compra do banco de dados SQL do Azure](sql-database-service-tiers.md).


## <a name="what-are-the-hyperscale-capabilities"></a>Quais são os recursos de hiperescala

A camada de serviço de hiperescala no banco de dados SQL do Azure fornece os seguintes recursos adicionais:

- Suporte para até 100 TB de tamanho de banco de dados
- Backups de banco de dados quase instantâneos (com base em instantâneos de arquivo armazenados no armazenamento de BLOBs do Azure), independentemente do tamanho, sem nenhum impacto de e/s  
- Restaurações rápidas de banco de dados (com base em instantâneos de arquivo) em minutos, em vez de horas ou dias (não um tamanho de operação de dados)
- Maior desempenho geral devido à maior taxa de transferência de log e tempos de confirmação de transação mais rápidos, independentemente dos volumes de dados
- Expansão rápida-você pode provisionar um ou mais nós somente leitura para descarregar sua carga de trabalho de leitura e usá-las como Hot-standbys
- Dimensionamento rápido – você pode, em constante tempo, escalar verticalmente seus recursos de computação para acomodar cargas de trabalho pesadas quando necessário e, em seguida, dimensionar os recursos de computação de volta quando não for necessário.

A camada de serviço de hiperescala remove muitos dos limites práticos tradicionalmente vistos em bancos de dados de nuvem. Onde a maioria dos outros bancos de dados são limitados pelos recursos disponíveis em um único nó, os bancos de dados na camada de serviço de hiperescala não têm esses limites. Com sua arquitetura de armazenamento flexível, o armazenamento cresce conforme necessário. Na verdade, os bancos de dados de hiperescala não são criados com um tamanho máximo definido. Um banco de dados de hiperescala cresce conforme necessário-e você é cobrado apenas pela capacidade que usar. Para cargas de trabalho com uso intensivo de leitura, a camada de serviço de hiperescala fornece expansão rápida ao provisionar réplicas de leitura adicionais conforme necessário para descarregar cargas de trabalho de leitura.

Além disso, o tempo necessário para criar backups de banco de dados ou para escalar ou reduzir verticalmente não está mais vinculado ao volume de data no banco de dado. Os bancos de dados de hiperescala podem ser submetidos a backup virtualmente de forma instantânea. Você também pode dimensionar um banco de dados em dezenas de terabytes para cima ou para baixo em minutos. Esse recurso libera você de preocupações sobre como se encaixar em suas opções de configuração iniciais.

Para obter mais informações sobre os tamanhos de computação para a camada de serviço de hiperescala, consulte [características da camada de serviço](sql-database-service-tiers-vcore.md#service-tier-characteristics).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Quem deve considerar a camada de serviço de hiperescala

A camada de serviço de hiperescala destina-se à maioria das cargas de trabalho de negócios, pois fornece excelente flexibilidade e alto desempenho com recursos de computação e armazenamento escalonáveis de maneira independente. Com a capacidade de dimensionar automaticamente o armazenamento em até 100 TB, é uma ótima opção para os clientes que:

- Ter grandes bancos de dados locais e desejar modernizar seus aplicativos ao migrar para a nuvem
- Já estão na nuvem e são limitados pelas restrições de tamanho máximo do banco de dados de outras camadas de serviço (1-4 TB)
- Ter bancos de dados menores, mas exigir dimensionamento rápido de computação vertical e horizontal, alto desempenho, backup instantâneo e restauração rápida de banco de dados.

A camada de serviço de hiperescala dá suporte a uma ampla variedade de cargas de trabalho de SQL Server, desde OLTP puro até análise pura, mas ela é essencialmente otimizada para cargas de trabalho OLTP e HTAP (processamento analítico e de transações híbridas).

> [!IMPORTANT]
> Os pools elásticos não dão suporte à camada de serviço de hiperescala.

## <a name="hyperscale-pricing-model"></a>Modelo de preços de hiperescala

A camada de serviço de hiperescala só está disponível no [modelo vCore](sql-database-service-tiers-vcore.md). Para alinhar com a nova arquitetura, o modelo de preços é um pouco diferente das camadas de serviço Uso Geral ou Comercialmente Crítico:

- **Computação**:

  O preço unitário de computação em hiperescala é por réplica. O preço de [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) é aplicado automaticamente às réplicas de escala de leitura. Criamos uma réplica primária e uma réplica somente leitura por banco de dados de hiperescala por padrão.  Os usuários podem ajustar o número total de réplicas, incluindo a primária de 1-5.

- **Armazenamento**:

  Você não precisa especificar o tamanho máximo dos dados ao configurar um banco de dados de hiperescala. Na camada de hiperescala, você é cobrado pelo armazenamento para seu banco de dados de acordo com o uso real. O armazenamento é alocado automaticamente entre 10 GB e 100 TB, em incrementos que são dinamicamente ajustados entre 10 GB e 40 GB.  

Para obter mais informações sobre preços de hiperescala, consulte [preços do banco de dados SQL do Azure](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Arquitetura de funções distribuídas

Ao contrário dos mecanismos de banco de dados tradicionais que centralizaram todas as funções de gerenciamento em um local/processo (mesmo assim, chamados de bancos de dados distribuídos na produção atualmente têm várias cópias de um mecanismo de dado monolítico), um banco de dados de hiperescala separa o mecanismo de processamento de consultas, em que a semântica de vários mecanismos de dados diverge, dos componentes que fornecem armazenamento de longo prazo e durabilidade para os dados. Dessa forma, a capacidade de armazenamento pode ser dimensionada de maneira tranqüila até o necessário (o destino inicial é de 100 TB). Réplicas somente leitura compartilham os mesmos componentes de armazenamento, portanto, nenhuma cópia de dados é necessária para criar uma nova réplica legível. 

O diagrama a seguir ilustra os diferentes tipos de nós em um banco de dados de hiperescala:

![Arquitectura](./media/sql-database-hyperscale/hyperscale-architecture.png)

Um banco de dados de hiperescala contém os seguintes tipos diferentes de componentes:

### <a name="compute"></a>Computação

O nó de computação é onde reside o mecanismo relacional, de modo que todos os elementos de linguagem, o processamento de consulta, etc., ocorram. Todas as interações de usuário com um banco de dados de hiperescala acontecem por meio desses nós de computação. Os nós de computação têm caches baseados em SSD (rotulados com a extensão do pool de buffers RBPEX no diagrama anterior) para minimizar o número de viagens de ida e volta da rede necessárias para buscar uma página de dados. Há um nó de computação primário em que todas as cargas de trabalho de leitura/gravação e transações são processadas. Há um ou mais nós de computação secundários que atuam como nós em espera ativa para fins de failover, bem como atuam como nós de computação somente leitura para descarregar cargas de trabalho de leitura (se essa funcionalidade for desejada).

### <a name="page-server"></a>Servidor de página

Os servidores de página são sistemas que representam um mecanismo de armazenamento expandido.  Cada servidor de página é responsável por um subconjunto das páginas no banco de dados.  No mínimo, cada servidor de página controla entre 128 GB e 1 TB de dados. Nenhum dado é compartilhado em mais de um servidor de páginas (fora das réplicas que são mantidas para redundância e disponibilidade). O trabalho de um servidor de página é servir páginas de banco de dados para os nós de computação sob demanda e manter as páginas atualizadas à medida que as transações atualizam os dados. Os servidores de página são mantidos atualizados com a execução de registros de log do serviço de log. Os servidores de página também mantêm caches baseados em SSD para melhorar o desempenho. O armazenamento de longo prazo de páginas de dados é mantido no armazenamento do Azure para maior confiabilidade.

### <a name="log-service"></a>Serviço de log

O serviço de log aceita registros de log da réplica de computação primária, os mantém em um cache durável e encaminha os registros de log para o restante das réplicas de computação (para que eles possam atualizar seus caches), bem como os servidores de páginas relevantes, para que os dados possam ser atualizados existi. Dessa forma, todas as alterações de dados da réplica de computação primária são propagadas por meio do serviço de log para todas as réplicas de computação e servidores de página secundários. Por fim, os registros de log são enviados por push para o armazenamento de longo prazo no armazenamento do Azure, que é um repositório de armazenamento virtualmente infinito. Esse mecanismo elimina a necessidade de truncamento de log frequente. O serviço de log também tem cache local para acelerar o acesso aos registros de log.

### <a name="azure-storage"></a>Armazenamento do Azure

O armazenamento do Azure contém todos os arquivos de dados em um banco de dado. Os servidores de páginas mantêm os arquivos de dados no armazenamento do Azure atualizados. Esse armazenamento é usado para fins de backup, bem como para replicação entre regiões do Azure. Os backups são implementados usando instantâneos de armazenamento de arquivos de dados. As operações de restauração usando instantâneos são rápidas, independentemente do tamanho dos dados. Os dados podem ser restaurados para qualquer ponto no tempo dentro do período de retenção de backup do banco de dados.

## <a name="backup-and-restore"></a>Backup e restauração

Os backups são baseados em instantâneo de arquivo e, portanto, são quase instantâneos. O armazenamento e a separação de computação permitem o envio por push da operação de backup/restauração para a camada de armazenamento para reduzir a carga de processamento na réplica de computação primária. Como resultado, o backup do banco de dados não afeta o desempenho do nó de computação primário; da mesma forma, restaurações são feitas revertendo para instantâneos de arquivo e, como tal, não são um tamanho de operação de dados. Restore é uma operação em tempo constante e até mesmo bancos de dados com vários terabytes podem ser restaurados em minutos, em vez de horas ou dias. A criação de novos bancos de dados por meio da restauração de um backup existente também aproveita esse recurso: a criação de cópias para fins de desenvolvimento ou teste, até mesmo de bancos de dados de tamanho de terabyte, é factível em minutos.

## <a name="scale-and-performance-advantages"></a>Vantagens de escala e desempenho

Com a capacidade de acelerar/reduzir rapidamente os nós de computação somente leitura, a arquitetura de hiperescala permite recursos de escala de leitura significativos e também pode liberar o nó de computação primário para atender a mais solicitações de gravação. Além disso, os nós de computação podem ser escalados verticalmente de forma rápida, devido à arquitetura de armazenamento compartilhado da arquitetura de hiperescala.

## <a name="create-a-hyperscale-database"></a>Criar um banco de dados de hiperescala

Um banco de dados de hiperescala pode ser criado usando o [portal do Azure](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) ou [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create). Os bancos de dados de hiperescala estão disponíveis apenas usando o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).

O comando T-SQL a seguir cria um banco de dados de hiperescala. Você deve especificar a edição e o objetivo de serviço na instrução `CREATE DATABASE`. Consulte os [limites de recurso](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases#hyperscale-service-tier-for-provisioned-compute) para obter uma lista de objetivos de serviço válidos.

```sql
-- Create a HyperScale Database
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
Isso criará um banco de dados de hiperescala no hardware Gen5 com 4 núcleos.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Migrar um banco de dados SQL do Azure existente para a camada de serviço de hiperescala

Você pode mover seus bancos de dados SQL do Azure para hiperescala usando o [portal do Azure](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) ou [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update). Neste momento, essa é uma migração unidirecional. Não é possível mover bancos de dados de um subdimensionamento para outra camada de serviço, a não ser por meio da exportação e da importação. Para provas de conceito (POCs), é recomendável fazer uma cópia dos bancos de dados de produção e migrar a cópia para o hiperescala. A migração de um banco de dados SQL do Azure existente para a camada de hiperescala é um tamanho de operação.

O comando T-SQL a seguir move um banco de dados para a camada de serviço de hiperescala. Você deve especificar a edição e o objetivo de serviço na instrução `ALTER DATABASE`.

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Conectar-se a uma réplica de escala de leitura de um banco de dados de hiperescala

Em bancos de dados de hiperescala, o argumento `ApplicationIntent` na cadeia de conexão fornecida pelo cliente determina se a conexão é roteada para a réplica de gravação ou para uma réplica secundária somente leitura. Se o `ApplicationIntent` definido como `READONLY` e o banco de dados não tiver uma réplica secundária, a conexão será roteada para a réplica primária e usará como padrão `ReadWrite` comportamento.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

As réplicas secundárias de hiperescala são idênticas, usando o mesmo objetivo de nível de serviço que a réplica primária. Se mais de uma réplica secundária estiver presente, a carga de trabalho será distribuída entre todos os secundários disponíveis. Cada réplica secundária é atualizada de forma independente, portanto, réplicas diferentes podem ter latência de dados diferente em relação à réplica primária.

## <a name="database-high-availability-in-hyperscale"></a>Alta disponibilidade do banco de dados em hiperescala

Como em todas as outras camadas de serviço, a hiperescala garante a durabilidade dos dados para transações confirmadas, independentemente da disponibilidade da réplica de computação. A extensão do tempo de inatividade devido à réplica primária se tornar indisponível depende do tipo de failover (planejado versus não planejado) e da presença de pelo menos uma réplica secundária. Em um failover planejado (ou seja, um evento de manutenção), o sistema cria a nova réplica primária antes de iniciar um failover ou usa uma réplica secundária existente como o destino de failover. Em um failover não planejado (ou seja, uma falha de hardware na réplica primária), o sistema usará uma réplica secundária como um destino de failover, se houver, ou criará uma nova réplica primária do pool de capacidade de computação disponível. No último caso, a duração do tempo de inatividade é mais longa devido a etapas adicionais necessárias para criar a nova réplica primária.

Para SLA de hiperescala, consulte [SLA para o banco de dados SQL do Azure](https://azure.microsoft.com/support/legal/sla/sql-database/).

## <a name="disaster-recovery-for-hyperscale-databases"></a>Recuperação de desastre para bancos de dados de hiperescala

### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>Restaurando um banco de dados de hiperescala para uma geografia diferente
Se você precisar restaurar um BD de hiperescala do banco de dados SQL do Azure para uma região que não seja a que está hospedada no momento, como parte de uma operação de recuperação de desastre ou de análise, realocação ou qualquer outro motivo, o método principal é fazer uma restauração geográfica do banco de dados.  Isso envolve exatamente as mesmas etapas que você usaria para restaurar qualquer outro banco de BD SQL do AZURE para uma região diferente:
1. Crie um servidor de banco de dados SQL na região de destino se você ainda não tiver um servidor apropriado.  Esse servidor deve pertencer à mesma assinatura que o servidor original (origem).
2. Siga as instruções no tópico de [restauração geográfica](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) da página sobre como restaurar bancos de dados SQL do Azure de backups automáticos.

> [!NOTE]
> Como a origem e o destino estão em regiões separadas, o banco de dados não pode compartilhar o armazenamento de instantâneos com o banco de dados de origem como em restaurações não geográficas, o que é concluído de maneira extremamente rápida.  No caso de uma restauração geográfica de um banco de dados de hiperescala, ela será uma operação de tamanho de dado, mesmo que o destino esteja na região emparelhada do armazenamento replicado geograficamente.  Isso significa que fazer uma restauração geográfica levará tempo proporcional ao tamanho do banco de dados que está sendo restaurado.  Se o destino estiver na região emparelhada, a cópia estará dentro de um datacenter, que será significativamente mais rápido do que uma cópia de longa distância pela Internet, mas ainda copiará todos os bits.

## <a name=regions></a>Regiões disponíveis

No momento, a camada de hiperescala do banco de dados SQL do Azure está disponível nas seguintes regiões:

- Austrália Oriental
- Sudeste da Austrália
- Sul do Brasil
- Canadá Central
- EUA Central
- Leste da China 2
- Norte da China 2
- Ásia Oriental
- Leste dos EUA
- Leste dos EUA 2
- França Central
- Leste do Japão
- Oeste do Japão
- Coreia Central
- Sul da Coreia
- Centro-Norte dos EUA
- Europa Setentrional
- Norte da África do Sul
- Centro-Sul dos EUA
- Sudeste Asiático
- Sul do Reino Unido
- Oeste do Reino Unido
- Oeste da Europa
- Oeste dos EUA
- Oeste dos EUA 2

Se desejar criar um banco de dados de hiperescala em uma região que não esteja listada como com suporte, você poderá enviar uma solicitação de integração via portal do Azure. Estamos trabalhando para expandir a lista de regiões com suporte; portanto, verifique novamente a lista de regiões mais recente.

Para solicitar a capacidade de criar bancos de dados de hiperescala em regiões não listadas:

1. Navegue até a [folha de ajuda e suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

2. Clique em [ **nova solicitação de suporte**](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

    ![Folha de ajuda e suporte do Azure](media/sql-database-service-tier-hyperscale/request-screen-1.png)

3. Para **tipo de problema**, selecione **limites de serviço e assinatura (cotas)**

4. Escolha a assinatura que você usaria para criar os bancos de dados

5. Para **tipo de cota**, selecione **banco de dados SQL**

6. Clique em **Avançar: soluções**

1. Clique em **fornecer detalhes**

    ![Detalhes do problema](media/sql-database-service-tier-hyperscale/request-screen-2.png)

8. Escolher **tipo de cota do banco de dados SQL**: **outra solicitação de cota**

9. Preencha o seguinte modelo:

    ![Detalhes da cota](media/sql-database-service-tier-hyperscale/request-screen-3.png)

    No modelo, forneça as seguintes informações

    > Solicitação para criar o banco de dados SQL de hiperescala do Azure em uma nova região<br/> Região: [preencha sua região solicitada]  <br/>
    > Calcular SKU/total de núcleos, incluindo réplicas legíveis <br/>
    > Número de TB estimado 
    >

10. Escolha **Gravidade C**

11. Escolha o método de contato apropriado e preencha os detalhes.

12. Clique em **salvar** e **continuar**

## <a name="known-limitations"></a>Limitações conhecidas
Essas são as limitações atuais da camada de serviço de hiperescala a partir do GA.  Estamos trabalhando ativamente para remover o máximo possível de limitações.

| Problema | Descrição |
| :---- | :--------- |
| O painel gerenciar backups de um servidor lógico não mostra que os bancos de dados de hiperescala serão filtrados do SQL Server  | O hiperscale tem um método separado para gerenciar backups e, como tal, a retenção de longo prazo e as configurações de retenção de backup point-in-time não se aplicam/são invalidadas. De acordo, os bancos de dados de hiperescala não aparecem no painel gerenciar backup. |
| Restauração pontual | Depois que um banco de dados é migrado para a camada de serviço de hiperescala, não há suporte para a restauração para um ponto no tempo antes da migração.|
| Restauração de BD não hiperescala para Hypserscale e vice-versa | Você não pode restaurar um banco de dados de hiperescala em um banco de dados não hiperescala, nem pode restaurar um banco de dados que não seja de hiperescala em um banco de dados de hiperescala.|
| Se um banco de dados tiver um ou mais arquivos com mais de 1 TB, a migração falhará | Em alguns casos, pode ser possível contornar esse problema reduzindo os arquivos grandes para menos de 1 TB. Se estiver migrando um banco de dados que está sendo usado durante o processo de migração, verifique se nenhum arquivo tem mais de 1 TB. Use a consulta a seguir para determinar o tamanho dos arquivos de banco de dados. `SELECT *, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| Instância Gerenciada | No momento, não há suporte para Instância Gerenciada do Banco de Dados SQL do Azure com bancos de dados de hiperescala. |
| Pools elásticos |  Atualmente, não há suporte para pools elásticos com a hiperescala do banco de dados SQL.|
| A migração para o hiperescala é uma operação unidirecional no momento | Depois que um banco de dados é migrado para o subdimensionamento, ele não pode ser migrado diretamente para uma camada de serviço não hiperescala. No momento, a única maneira de migrar um banco de dados de hiperescala para não hiperescala é exportar/importar usando um arquivo BACPAC ou outras tecnologias de movimentação de dados (cópia em massa, Azure Data Factory, Azure Databricks, SSIS etc.)|
| Migração de bancos de dados com objetos na memória persistentes | O hiperscale dá suporte apenas a objetos não persistentes na memória (tipos de tabela, SPs nativos e funções).  As tabelas persistentes na memória e outros objetos devem ser descartados e recriados como objetos não na memória antes de migrar um banco de dados para a camada de serviço de hiperescala.|
| Alterar acompanhamento | Você ainda não pode configurar e usar Controle de Alterações com bancos de dados de hiperescala do SQL do Azure. |
| Replicação geográfica  | Você ainda não pode configurar a replicação geográfica para a hiperescala do banco de dados SQL do Azure. |
| Cópia do banco de dados | Você ainda não pode usar a cópia de banco de dados para criar um novo banco de dados na hiperescala do SQL do Azure. |
| Integração do TDE/AKV | A criptografia de banco de dados transparente usando Azure Key Vault (comumente conhecida como traga sua própria chave ou BYOK) ainda não tem suporte para a hiperescala do banco de dados SQL do Azure, no entanto, TDE com chaves gerenciadas por serviço tem suporte total. |
|Recursos de banco de dados inteligente | Com exceção da opção "forçar plano", todas as outras opções de ajuste automático ainda não têm suporte em hiperescala: as opções podem parecer estar habilitadas, mas não haverá recomendações ou ações feitas. |

## <a name="next-steps"></a>Próximos passos

- Para obter perguntas frequentes sobre o hiperescala, consulte perguntas frequentes [sobre o subscale](sql-database-service-tier-hyperscale-faq.md).
- Para obter informações sobre camadas de serviço, consulte [camadas de serviço](sql-database-service-tiers.md)
- Consulte [visão geral dos limites de recursos em um servidor lógico](sql-database-resource-limits-logical-server.md) para obter informações sobre os limites nos níveis de servidor e assinatura.
- Para os limites de modelo de compra de um único banco de dados, consulte [limites do modelo de compra baseado em VCORE do banco de dados SQL do Azure para um único banco de dados](sql-database-vcore-resource-limits-single-databases.md)
- Para obter uma lista de recursos e comparação, consulte [recursos comuns do SQL](sql-database-features.md).
