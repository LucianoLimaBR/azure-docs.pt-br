---
title: Criar ou gerenciar contas de integração B2B – aplicativos lógicos do Azure
description: Criar, vincular e gerenciar contas de integração para a integração corporativa com os aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.workload: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 960733b7423ad1e22bd05a75d9b994cd85b1d30c
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680362"
---
# <a name="create-and-manage-integration-accounts-for-b2b-enterprise-integrations-in-azure-logic-apps"></a>Criar e gerenciar contas de integração para integrações corporativas B2B em aplicativos lógicos do Azure

Antes de criar [soluções de integração corporativa e B2B](../logic-apps/logic-apps-enterprise-integration-overview.md) usando os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md), você precisa criar uma conta de integração, que é um recurso separado do Azure que fornece um contêiner seguro, escalonável e gerenciável para o artefatos de integração que você define e usa com seus fluxos de trabalho de aplicativo lógico.

Por exemplo, você pode criar, armazenar e gerenciar artefatos B2B, como parceiros comerciais, contratos, mapas, esquemas, certificados e configurações de lote. Além disso, antes de seu aplicativo lógico poder trabalhar com esses artefatos e usar os conectores de Aplicativos Lógicos B2B, você deve [vincular sua conta de integração](#link-account) ao seu aplicativo lógico. A conta de integração e o aplicativo lógico devem existir no *mesmo* local ou região.

> [!TIP]
> Para criar uma conta de integração dentro de um [ambiente do serviço de integração](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), consulte [criar contas de integração em um ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment).

Este tópico mostra como executar estas tarefas:

* Crie sua conta de integração.
* Vincule sua conta de integração a um aplicativo lógico.
* Altere o tipo de preço para sua conta de integração.
* Desvincular sua conta de integração de um aplicativo lógico.
* Mova sua conta de integração para outro grupo de recursos ou assinatura do Azure.
* Exclua sua conta de integração.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [Inscreva-se para obter uma conta gratuita do Azure](https://azure.microsoft.com/free/).

## <a name="create-integration-account"></a>Criar conta de integração

Para essa tarefa, você pode usar a portal do Azure seguindo as etapas nesta seção, [Azure PowerShell](https://docs.microsoft.com//powershell/module/azurerm.logicapp/New-AzureRmIntegrationAccount)ou [CLI do Azure](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-create).

1. Entre no [portal do Azure](https://portal.azure.com) com suas credenciais de conta do Azure.

1. No menu principal do Azure, selecione **criar um recurso**. Na caixa de pesquisa, insira "conta de integração" como seu filtro e selecione **conta de integração**.

   ![Criar nova conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/create-integration-account.png)

1. Em **conta de integração**, selecione **criar**.

   ![Escolha "Adicionar" para criar a conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/add-integration-account.png)

1. Forneça estas informações sobre sua conta de integração:

   ![Fornecer detalhes da conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-details.png)

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome** | Sim | <*Integration-nome da conta* > | O nome da sua conta de integração, que pode conter apenas letras, números, hifens (`-`), sublinhados (`_`), parênteses (`(`, `)`) e pontos (`.`). Este exemplo usa "fabrikam integração". |
   | **Assinatura** | Sim | <*Azure-nome da assinatura* > | O nome da sua assinatura do Azure |
   | **Grupo de recursos** | Sim | <*Azure-Resource-Group-name* > | O nome do [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) a ser usado para organizar os recursos relacionados. Para este exemplo, crie um novo grupo de recursos com o nome "FabrikamIntegration-RG". |
   | **Camada de preços** | Sim | > <*no nível de preços* | O tipo de preço da conta de integração, que você pode alterar posteriormente. Para este exemplo, selecione **gratuito**. Para saber mais, consulte esses tópicos: <p>[modelo de preços de aplicativos lógicos](../logic-apps/logic-apps-pricing.md#integration-accounts) -  <p>[limites e configuração de -  aplicativos lógicos](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) <p>[preços de aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps/) -  |
   | **Localidade** | Sim | <*do Azure-region* > | A região onde armazenar os metadados da conta de integração. Selecione o mesmo local que seu aplicativo lógico ou crie seus aplicativos lógicos no mesmo local que sua conta de integração. Para este exemplo, use "oeste dos EUA". <p>**Observação**: para criar uma conta de integração dentro de um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), selecione esse ISE como o local. Para obter mais informações, consulte [criar contas de integração em um ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#create-integration-account-environment). |
   | **Log Analytics** | Não | Desligado, ativado | Mantenha a configuração **desativado** para este exemplo. |
   |||||

1. Quando tiver terminado, selecione **criar**.

   Após a conclusão da implantação, o Azure abrirá sua conta de integração.

   ![O Azure abre a conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-created.png)

1. Antes que seu aplicativo lógico possa usar sua conta de integração, siga as próximas etapas para vincular sua conta de integração e o aplicativo lógico juntos.

<a name="link-account"></a>

## <a name="link-to-logic-app"></a>Link para o aplicativo lógico

Para dar acesso aos seus aplicativos lógicos a uma conta de integração que contém seus artefatos B2B, você deve primeiro vincular sua conta de integração ao seu aplicativo lógico. O aplicativo lógico e a conta de integração devem existir na mesma região. Para concluir essa tarefa, você pode usar o portal do Azure. Se você usar o Visual Studio e seu aplicativo lógico estiver em um [projeto de grupo de recursos do Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md), você poderá [vincular seu aplicativo lógico a uma conta de integração usando o Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md#link-integration-account).

1. Na portal do Azure, localize e abra seu aplicativo lógico.

1. No [portal do Azure](https://portal.azure.com), abra um aplicativo lógico existente ou crie um novo aplicativo lógico.

1. No menu do aplicativo lógico, em **configurações**, selecione **configurações de fluxo de trabalho**. Em **conta de integração**, abra a lista **selecionar uma conta de integração** . Selecione a conta de integração a ser vinculada ao seu aplicativo lógico.

   ![Selecione sua conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. Para concluir a vinculação, selecione **salvar**.

   ![Selecione sua conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   Depois que sua conta de integração for vinculada com êxito, o Azure mostrará uma mensagem de confirmação.

   ![Link de confirmações bem-sucedidas do Azure](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

Agora seu aplicativo lógico pode usar os artefatos em sua conta de integração mais os conectores B2B, como validação de XML e codificação ou decodificação de arquivo simples.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>Alterar tipo de preço

Para aumentar os [limites](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits) de uma conta de integração, você pode [atualizar para um tipo de preço mais alto](#upgrade-pricing-tier), se disponível. Por exemplo, você pode atualizar da camada gratuita para a camada básica ou Standard. Você também pode [fazer downgrade para uma camada inferior](#downgrade-pricing-tier), se disponível. Para obter mais informações sobre preços, consulte estes tópicos:

* [Preços dos aplicativos lógicos](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Modelo de preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>Atualizar tipo de preço

Para fazer essa alteração, você pode usar a portal do Azure seguindo as etapas nesta seção ou na [CLI do Azure](#upgrade-tier-azure-cli).

#### <a name="azure-portal"></a>Portal do Azure

1. Entre no [portal do Azure](https://portal.azure.com) com suas credenciais de conta do Azure.

1. Na caixa principal do Azure Search, insira "contas de integração" como seu filtro e selecione **contas de integração**.

   ![Localizar conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   O Azure mostra todas as contas de integração em suas assinaturas do Azure.

1. Em **contas de integração**, selecione a conta de integração que você deseja mover. No menu conta de integração, selecione **visão geral**.

   ![No menu conta de integração, selecione "visão geral"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. No painel Visão geral, selecione o **tipo de preço de atualização**, que lista as camadas mais altas disponíveis. Quando você seleciona uma camada, a alteração entra em vigor imediatamente.

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>Azure CLI

1. Se você ainda não fez isso, [Instale os pré-requisitos de CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. No portal do Azure, abra o ambiente de [**Cloud Shell**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) do Azure.

   ![Abrir Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. No prompt de comando, insira o [comando **AZ Resource** ](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update)e defina `skuName` para a camada mais alta que você deseja.

   ```Azure CLI
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   Por exemplo, se você tiver a camada básica, poderá definir `skuName` como `Standard`:

   ```Azure CLI
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>Tipo de preço de downgrade

Para fazer essa alteração, use o [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. Se você ainda não fez isso, [Instale os pré-requisitos de CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).

1. No portal do Azure, abra o ambiente de [**Cloud Shell**](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) do Azure.

   ![Abrir Azure Cloud Shell](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. No prompt de comando, insira o [comando **AZ Resource** ](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-update) e defina `skuName` para a camada inferior que você deseja.

   ```Azure CLI
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   Por exemplo, se você tiver a camada Standard, poderá definir `skuName` como `Basic`:

   ```Azure CLI
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>Desvincular do aplicativo lógico

Se você quiser vincular seu aplicativo lógico a outra conta de integração ou não usar mais uma conta de integração com seu aplicativo lógico, exclua o link usando Azure Resource Explorer.

1. Abra a janela do navegador e vá para [Azure Resource Explorer (https://resources.azure.com)](https://resources.azure.com). Entre com as mesmas credenciais de conta do Azure.

   ![Azure Resource Manager](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. Na caixa de pesquisa, insira o nome do aplicativo lógico para que você possa encontrar e selecionar seu aplicativo lógico.

   ![Localizar e selecionar aplicativo lógico](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. Na barra de título do Explorer, selecione **leitura/gravação**.

   ![Ativar o modo de "leitura/gravação"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. Na guia **dados** , selecione **Editar**.

   ![Na guia "dados", selecione "Editar"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. No editor, localize o objeto `integrationAccount` e exclua essa propriedade, que tem este formato:

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   Por exemplo:

   ![Localizar objeto "integrationAccount"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. Na guia **dados** , selecione **Put** para salvar as alterações.

   ![Para salvar as alterações, selecione "Put"](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. Na portal do Azure, localize e selecione seu aplicativo lógico. Nas **configurações de fluxo de trabalho**do aplicativo, verifique se a propriedade conta de **integração** agora aparece vazia.

   ![Verifique se a conta de integração não está vinculada](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>Mover conta de integração

Você pode mover sua conta de integração para outro grupo de recursos do Azure ou assinatura do Azure. Quando você move recursos, o Azure cria novas IDs de recurso, portanto, certifique-se de usar as novas IDs em vez disso e atualizar quaisquer scripts ou ferramentas associadas aos recursos movidos. Se você quiser alterar a assinatura, também deverá especificar um grupo de recursos novo ou existente.

Para essa tarefa, você pode usar a portal do Azure seguindo as etapas nesta seção ou na [CLI do Azure](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-move).

1. Entre no [portal do Azure](https://portal.azure.com) com suas credenciais de conta do Azure.

1. Na caixa principal do Azure Search, insira "contas de integração" como seu filtro e selecione **contas de integração**.

   ![Localizar conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   O Azure mostra todas as contas de integração em suas assinaturas do Azure.

1. Em **contas de integração**, selecione a conta de integração que você deseja mover. No menu conta de integração, selecione **visão geral**.

   ![No menu conta de integração, selecione "visão geral"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. Ao lado de **grupo de recursos** ou **nome da assinatura**, selecione **alterar**.

   ![Alterar grupo de recursos ou assinatura](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. Selecione todos os recursos relacionados que você também deseja mover.

1. Com base na sua seleção, siga estas etapas para alterar o grupo de recursos ou a assinatura:

   * Grupo de recursos: na lista **grupo de recursos** , selecione o grupo de recursos de destino. Ou, para criar um grupo de recursos diferente, selecione **criar um novo grupo de recursos**.

   * Assinatura: na lista **assinatura** , selecione a assinatura de destino. Na lista **grupo de recursos** , selecione o grupo de recursos de destino. Ou, para criar um grupo de recursos diferente, selecione **criar um novo grupo de recursos**.

1. Para confirmar sua compreensão de que qualquer script ou ferramenta associada aos recursos movidos não funcionará até você atualizá-los com as novas IDs de recurso, selecione a caixa de confirmação e, em seguida, selecione **OK**.

1. Depois de concluir, certifique-se de atualizar qualquer e todos os scripts com as novas IDs de recurso para seus recursos movidos.  

## <a name="delete-integration-account"></a>Excluir conta de integração

Para essa tarefa, você pode usar a portal do Azure seguindo as etapas nesta seção, [CLI do Azure](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-delete)ou [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp/Remove-AzureRmIntegrationAccount).

1. Entre no [portal do Azure](https://portal.azure.com) com suas credenciais de conta do Azure.

1. Na caixa principal do Azure Search, insira "contas de integração" como seu filtro e selecione **contas de integração**.

   ![Localizar conta de integração](./media/logic-apps-enterprise-integration-create-integration-account/find-integration-account.png)

   O Azure mostra todas as contas de integração em suas assinaturas do Azure.

1. Em **contas de integração**, selecione a conta de integração que você deseja excluir. No menu conta de integração, selecione **visão geral**.

   ![No menu conta de integração, selecione "visão geral"](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. No painel Visão geral, selecione **excluir**.

   ![No painel "visão geral", selecione "excluir"](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. Para confirmar que deseja excluir sua conta de integração, selecione **Sim**.

   ![Para confirmar a exclusão, selecione "Sim"](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

## <a name="next-steps"></a>Próximos passos

* [Criar parceiros comerciais em sua conta de integração](../logic-apps/logic-apps-enterprise-integration-partners.md)
* [Criar contratos entre parceiros em sua conta de integração](../logic-apps/logic-apps-enterprise-integration-agreements.md)
