---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.author: estfan
ms.custom: include file
ms.date: 05/15/2018
ms.openlocfilehash: aa1001661d8fe03855e1a28b882f674bee3606b2
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/14/2019
ms.locfileid: "72312062"
---
* Se você estiver usando o banco de dados do SQL do Microsoft Azure, siga as etapas em [Conectar-se ao Banco de Dados SQL do Azure](#connect-azure-sql-db).

* Se você estiver usando o SQL Server, siga as etapas em [Conectar ao SQL Server](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Conectar-se ao Banco de Dados SQL do Azure

Quando o gatilho ou ação do SQL solicitar informações de conexão, siga estas etapas, que funcionam para gatilhos e ações.

1. Para **nome da conexão**, crie um nome para a conexão.

1. Em **nome do SQL Server**, selecione o servidor SQL do Azure. Quando a lista **nome do banco de dados SQL** for exibida, selecione seu banco de dados. Forneça o nome de usuário e a senha para o SQL Server do Azure.

   Você também pode encontrar essas informações na portal do Azure nas propriedades do banco de dados SQL ou na cadeia de conexão:

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Criar conexão de banco de dados SQL do Azure](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Quando terminar, selecione **Criar**.

1. Depois de criar a conexão, continue com [Adicionar um gatilho SQL](#add-sql-trigger) ou [adicione uma ação SQL](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Conectar-se ao SQL Server

Quando o gatilho ou ação do SQL solicitar informações de conexão, siga estas etapas, que funcionam para gatilhos e ações. No entanto, antes de começar, certifique-se de que você já [configurou seu gateway de dados local](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection). Caso contrário, o gateway não aparecerá na lista de gateways quando você criar a conexão.

1. Para **nome da conexão**, crie um nome para a conexão.

1. No gatilho ou ação, selecione **Conectar por meio do gateway de dados locais** para que sejam exibidas as opções do SQL Server.

1. Para **nome do SQL Server** e **nome do banco de dados SQL**, forneça o endereço do seu SQL Server e o nome do seu banco de dados. Para **username** e **password**, forneça o nome de usuário e a senha para seu servidor.

   Você também pode encontrar essas informações em sua cadeia de conexão:

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Criar SQL Server conexão](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. Se o SQL Server usar a autenticação do Windows ou básica, selecione o **tipo de autenticação**.

1. Em **gateways**, selecione a assinatura do Azure associada ao gateway de dados local criado anteriormente e selecione o nome do seu gateway de dados local.

   Se o seu gateway não aparecer na lista, verifique se você configurou [corretamente seu gateway](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection).

   ![Criação de SQL Server conexão concluída](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Quando terminar, selecione **Criar**.

1. Depois de criar sua conexão, continue com [Adicionar gatilho SQL](#add-sql-trigger) ou [Adicionar ação SQL](#add-sql-action).
