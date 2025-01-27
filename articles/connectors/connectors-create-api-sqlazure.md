---
title: Conectar-se ao SQL Server ou banco de dados SQL do Azure-aplicativo lógico do Azure
description: Automatizar tarefas para bancos de dados SQL locais ou na nuvem usando aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam; LADocs
manager: carmonm
ms.topic: conceptual
tags: connectors
ms.date: 10/14/2019
ms.openlocfilehash: 6c86ef26bbf7bd9dbce8aa77aef2213b14b57f5f
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311962"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>Automatizar fluxos de trabalho para SQL Server ou banco de dados SQL do Azure usando aplicativos lógicos do Azure

Este artigo mostra como você pode acessar dados no banco de dados SQL de dentro de um aplicativo lógico com o conector do SQL Server. Dessa forma, você pode automatizar tarefas, processos ou fluxos de trabalho que gerenciam seus dados e recursos SQL criando aplicativos lógicos. O conector de SQL Server funciona para o [SQL Server local](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) e para o [banco de dados SQL do Azure baseado em nuvem](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).

Você pode criar Aplicativos Lógicos que são executados quando disparados por eventos no Banco de Dados SQL ou em outros sistemas, como Dynamics CRM Online. Seus aplicativos lógicos também podem obter, inserir e excluir dados juntamente com consultas SQL e procedimentos armazenados em execução. Por exemplo, você pode criar um aplicativo lógico que verifica automaticamente se há novos registros no Dynamics CRM Online, adiciona itens ao banco de dados SQL para quaisquer novos registros e, em seguida, envia alertas por email sobre os itens adicionados.

Se ainda não estiver familiarizado com aplicativo lógicos, consulte [O que são os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início Rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para obter informações técnicas, limitações e problemas conhecidos específicos do conector, consulte a [página de referência do conector do SQL Server](https://docs.microsoft.com/connectors/sql/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Um [banco de dados SQL Server](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) ou [banco de dados SQL do Azure](../sql-database/sql-database-get-started-portal.md)

  As tabelas devem ter dados para que seu aplicativo lógico possa retornar resultados ao chamar operações. Se você criar um Banco de Dados SQL do Azure, poderá usar bancos de dados de exemplo, que estão incluídos.

* O nome do SQL Server, o nome do banco de dados, o nome de usuário e a senha. Você precisará dessas credenciais para que possa autorizar sua lógica para acessar o SQL Server.

  * Para o SQL Server, você pode encontrar esses detalhes na cadeia de conexão:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Para o Banco de Dados SQL do Azure, você pode encontrar esses detalhes na cadeia de conexão ou no portal do Azure abaixo das propriedades do Banco de Dados SQL:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* Antes de poder conectar aplicativos lógicos a sistemas locais, como SQL Server, você precisa [configurar um gateway de dados local](../logic-apps/logic-apps-gateway-install.md). Dessa forma, você pode selecionar o gateway ao criar a conexão SQL para seu aplicativo lógico.

* O aplicativo lógico em que você precisa de acesso ao banco de dados SQL. Para iniciar seu aplicativo lógico com um gatilho SQL, é necessário um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Adicionar um gatilho SQL

Nos Aplicativos Lógicos do Azure, cada aplicativo lógico deve começar com um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts), que é disparado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o gatilho é acionado, o mecanismo de aplicativos lógicos cria uma instância de aplicativo lógico e começa a executar o fluxo de trabalho do aplicativo lógico.

1. No portal do Azure ou no Visual Studio, crie um aplicativo lógico em branco, que abre o Designer de Aplicativos Lógicos. Este exemplo usa o portal do Azure.

1. No designer, na caixa de pesquisa, digite "SQL Server" como filtro. Na lista de gatilhos, selecione o gatilho SQL desejado.

   Este exemplo usa o gatilho **quando um item é criado** .

   ![Selecione o gatilho "quando um item é criado"](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Se você for solicitado a criar uma conexão, [Crie sua conexão SQL agora](#create-connection). Se sua conexão existir, selecione um **nome de tabela**.

   ![Selecione a tabela que você deseja](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. Defina as propriedades **Intervalo** e **Frequência**, que especificam com que frequência o aplicativo lógico verifica a tabela.

   Esse gatilho retorna apenas uma linha da tabela selecionada, nada mais. Para executar outras tarefas, adicione outras ações que executam as tarefas desejadas. Por exemplo, para exibir os dados nessa linha, você pode adicionar outras ações que criam um arquivo que inclui os campos da linha retornada e, em seguida, enviar alertas por email. Para saber mais sobre outras ações disponíveis para esse conector, consulte a [página de referência do conector](/connectors/sql/).

1. Quando terminar, na barra de ferramentas do designer, selecione **salvar**.

   Esta etapa automaticamente habilita e publica o aplicativo lógico ativo no Azure.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Adicionar uma ação SQL

Em Aplicativos Lógicos do Azure, uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma etapa do fluxo de trabalho que segue um gatilho ou outra ação. Neste exemplo, o aplicativo lógico é iniciado com o [gatilho de recorrência](../connectors/connectors-native-recurrence.md) e chama uma ação que obtém uma linha de um banco de dados SQL.

1. No portal do Azure ou no Visual Studio, abra o aplicativo lógico no Designer de Aplicativos Lógicos. Este exemplo usa o portal do Azure.

1. No gatilho ou na ação em que você deseja adicionar a ação SQL, selecione **nova etapa**.

   ![Selecione "nova etapa"](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Para adicionar uma ação entre etapas existentes, mova o mouse sobre a seta de conexão. Selecione o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Em **escolher uma ação**, na caixa de pesquisa, digite "SQL Server" como filtro. Na lista ações, selecione a ação SQL desejada.

   Este exemplo usa a ação **obter linha** , que obtém um único registro.

   ![Localizar e selecionar a ação SQL "obter linha"](./media/connectors-create-api-sqlazure/select-sql-get-row.png)

   Essa ação retorna apenas uma linha da tabela selecionada, nada mais. Para exibir os dados nessa linha, você pode adicionar outras ações que criam um arquivo que inclui os campos da linha retornada e armazena esse arquivo em uma conta de armazenamento em nuvem. Para saber mais sobre outras ações disponíveis para esse conector, consulte a [página de referência do conector](/connectors/sql/).

1. Se você for solicitado a criar uma conexão, [Crie sua conexão SQL agora](#create-connection). Se sua conexão existir, selecione um **nome de tabela**e insira a **ID de linha** para o registro desejado.

   ![Insira o nome da tabela e a ID da linha](./media/connectors-create-api-sqlazure/table-row-id.png)

1. Quando terminar, na barra de ferramentas do designer, selecione **salvar**.

   Esta etapa automaticamente habilita e publica o aplicativo lógico ativo no Azure.

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Conectar-se ao seu banco de dados

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Lidar com dados em massa

Às vezes, você precisa trabalhar com conjuntos de resultados tão grandes que o conector não retorna todos os resultados ao mesmo tempo ou você deseja ter um melhor controle sobre o tamanho e a estrutura de seus conjuntos de resultados. Aqui estão algumas maneiras que você pode lidar com esses grandes conjuntos de resultados:

* Para ajudá-lo a gerenciar os resultados como conjuntos menores, ative a *paginação*. Para obter mais informações, consulte [obter dados em massa, registros e itens usando a paginação](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Crie um procedimento armazenado que organize os resultados da maneira desejada.

  Ao obter ou inserir várias linhas, seu aplicativo lógico pode iterar por meio dessas linhas usando um [*loop Until*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) dentro desses [limites](../logic-apps/logic-apps-limits-and-config.md). No entanto, quando seu aplicativo lógico tem que trabalhar com conjuntos de registros tão grandes, por exemplo, milhares ou milhões de linhas, você deseja minimizar os custos resultantes de chamadas para o banco de dados.

  Para organizar os resultados da maneira desejada, você pode criar um [*procedimento armazenado*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) que é executado em sua instância do SQL e usa a instrução **Select-order by** . Essa solução fornece a você mais controle sobre o tamanho e a estrutura de seus resultados. Seu aplicativo lógico chama o procedimento armazenado usando a ação **Executar procedimento armazenado** do conector do SQL Server.

  Para obter mais detalhes da solução, consulte estes artigos:

  * [SQL Pagination for bulk data transfer with Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx) (Paginação de SQL para transferência de dados em massa com Aplicativos Lógicos)

  * [SELECT – Cláusula ORDER BY](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Para obter informações técnicas sobre os gatilhos, as ações e os limites desse conector, consulte a [página de referência do conector](/connectors/sql/).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores para aplicativos lógicos do Azure](../connectors/apis-list.md)