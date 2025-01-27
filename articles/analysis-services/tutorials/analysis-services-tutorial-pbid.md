---
title: Tutorial – Conectar-se ao Azure Analysis Services com Power BI Desktop | Microsoft Docs
author: minewiskan
description: Saiba como se conectar ao Azure Analysis Services com o Power BI Desktop.
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: f2123b6d8b11c89735e65cb0f48d93841f49328d
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72294477"
---
# <a name="tutorial-connect-with-power-bi-desktop"></a>Tutorial: Conectar-se com o Power BI Desktop

Neste tutorial, use o Power BI Desktop para se conectar ao modelo de banco de dados do exemplo de adventureworks no seu servidor. As tarefas que você concluir simulam uma conexão típica do usuário para o modelo e criam um relatório básico de dados de modelo.

> [!div class="checklist"]
> * Obter o nome do servidor no portal
> * Conectar-se usando o Power BI Desktop
> * Criar um relatório básico

## <a name="prerequisites"></a>Pré-requisitos

- [Adicionar o modelo de banco de dados do exemplo adventureworks](../analysis-services-create-sample-model.md) ao seu servidor.
- Ter permissões de [*leitura*](../analysis-services-server-admins.md) para o modelo de banco de dados do exemplo adventureworks.
- [Instalar o mais recente Power BI Desktop](https://powerbi.microsoft.com/desktop).

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure
Neste tutorial, você entra no portal para obter apenas o nome do servidor. Normalmente, os usuários obteriam o nome do servidor do administrador do servidor.

Entre no [portal](https://portal.azure.com/).

## <a name="get-server-name"></a>Obter nome do servidor
Para se conectar ao seu servidor a partir do Power BI Desktop, primeiro é necessário ter o nome do servidor. É possível obter o nome do servidor no portal.

No **Portal do Azure** > servidor > **Visão geral** > **Nome do servidor**, copie o nome do servidor.
   
   ![Obter o nome do servidor no Azure](./media/analysis-services-tutorial-pbid/aas-copy-server-name.png)

## <a name="connect-in-power-bi-desktop"></a>Conexão no Power BI Desktop

1. No Power BI Desktop, clique em **Obter Dados** > **Azure** > **Banco de dados do Azure Analysis Services**.

   ![Conectar-se em Obter Dados](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aasserver.png)

2. No **Servidor**, cole o nome do servidor, depois, no **Banco de dados**, insira **adventureworks** e clique em **OK**.

   ![Especificar nome do servidor e o modelo de banco de dados](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aas-servername.png)

3. Insira suas credenciais quando solicitado. A conta que você inserir deve ter pelo menos permissões de leitura para o modelo de banco de dados de exemplo adventureworks.

    O modelo de adventureworks é aberto no Power BI Desktop com um relatório em branco na exibição de relatório. A lista de **Campos** exibe todos os objetos de modelo não ocultos. O status de conexão é exibido no canto inferior direito.

4. Em **VISUALIZAÇÕES**, selecione **Gráfico de barras clusterizado**, depois clique em **Formatar** (ícone de cilindro de pintura) e, em seguida, ativar **Rótulos de dados**. 

   ![Visualizações](./media/analysis-services-tutorial-pbid/aas-pbid-visualizations-report.png)

5. Na tabela em **CAMPOS** > **Vendas pela Internet**, selecione as medidas de **Total de Vendas da Internet** e **Margem**. Na tabela **Categoria de Produto**, selecione **Nome da Categoria de Produto**.

   ![Relatório completo](./media/analysis-services-tutorial-pbid/aas-pbid-complete-report.png)

    Tire alguns minutos para explorar o modelo de exemplo adventureworks por meio da criação de visualizações diferentes e a divisão em dados e métricas. Quando estiver satisfeito com seu relatório, salve-o.

## <a name="clean-up-resources"></a>Limpar recursos

Se não for mais necessário, não salve o relatório ou exclua o arquivo caso já o tenha salvado.

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a usar o Power BI Desktop para se conectar a um modelo de dados em um servidor e criar um relatório básico. Se você não está familiarizado com a criação de um modelo de dados, consulte o [Tutorial de modelagem de dados tabular de Vendas pela Internet da Adventure Works](https://docs.microsoft.com/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial) na documentação do SQL Server Analysis Services.