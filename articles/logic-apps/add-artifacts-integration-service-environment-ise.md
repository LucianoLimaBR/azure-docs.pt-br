---
title: Adicionar artefatos ao ambiente do serviço de integração-aplicativos lógicos do Azure
description: Adicionar aplicativos lógicos, contas de integração e conectores personalizados ao ISE (ambiente do serviço de integração) para acessar redes virtuais do Azure (VNETs)
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 266c942adb62b187d39d2eac4a47455482bfadaa
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680523"
---
# <a name="add-artifacts-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Adicionar artefatos ao seu ISE (ambiente do serviço de integração) nos aplicativos lógicos do Azure

Depois de criar um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), adicione artefatos como aplicativos lógicos, contas de integração e conectores para que eles possam acessar os recursos em sua rede virtual do Azure.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [Inscreva-se para obter uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O ISE que você criou para executar seus aplicativos lógicos. Se você não tiver um ISE, [crie um ISE primeiro](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps"></a>Criar aplicativos lógicos

Para criar aplicativos lógicos que são executados em seu ISE (ambiente do serviço de integração), siga estas etapas:

1. Localize e abra o ISE, se ainda não estiver aberto. No menu ISE, em **configurações**, selecione **aplicativos lógicos**  > **Adicionar**.

   ![Adicionar novo aplicativo lógico ao ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

   or

   No menu principal do Azure, selecione **criar um recurso**  > **integração**  > **aplicativo lógico**.

1. Forneça o nome, a assinatura do Azure e o grupo de recursos do Azure (novo ou existente) a serem usados para seu aplicativo lógico.

1. Na lista **local** , na seção **ambientes do serviço de integração** , selecione o ISE, por exemplo:

   ![Selecione o ambiente do serviço de integração](./media/add-artifacts-integration-service-environment-ise/create-logic-app-with-integration-service-environment.png)

   > [!IMPORTANT]
   > Se você quiser usar seus aplicativos lógicos com uma conta de integração, esses aplicativos lógicos e a conta de integração deverão usar o mesmo ISE.

1. Continue [criando seu aplicativo lógico da maneira usual](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Para obter diferenças em como os gatilhos e as ações funcionam e como eles são rotulados quando você usa um ISE em comparação com o serviço de aplicativos lógicos globais, consulte [isolado versus global na visão geral do ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference).

1. Para gerenciar aplicativos lógicos e conexões de API no ISE, consulte [gerenciar seu ambiente de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts"></a>Criar contas de integração

Com base no [SKU do ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) selecionado na criação, seu ISE inclui um uso de conta de integração específico sem custo adicional. Os aplicativos lógicos que existem em um ambiente do serviço de integração (ISE) podem referenciar somente as contas de integração que existem no mesmo ISE. Portanto, para que uma conta de integração funcione com aplicativos lógicos em um ISE, tanto a conta de integração quanto os aplicativos lógicos devem usar o *mesmo ambiente* que o local. Para obter mais informações sobre contas de integração e ISEs, consulte [contas de integração com o ISE](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment).

Para criar uma conta de integração que usa um ISE, siga estas etapas:

1. Localize e abra o ISE, se ainda não estiver aberto. No menu ISE, em **configurações**, selecione **contas de integração**  > **Adicionar**.

   ![Adicionar nova conta de integração ao ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

   or

   No menu principal do Azure, selecione **criar um recurso**  > **integração**  > **conta de integração**.

1. Forneça o nome, a assinatura do Azure, o grupo de recursos do Azure (novo ou existente) e o tipo de preço a ser usado para sua conta de integração.

1. Na lista **local** , na seção **ambientes do serviço de integração** , selecione o mesmo ISE que seus aplicativos lógicos usam, por exemplo:

   ![Selecione o ambiente do serviço de integração](./media/add-artifacts-integration-service-environment-ise/create-integration-account-with-integration-service-environment.png)

1. [Vincule seu aplicativo lógico à sua conta de integração da maneira usual](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

1. Continue adicionando artefatos à sua conta de integração, como [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) e [contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md).

1. Para gerenciar contas de integração no ISE, consulte [gerenciar seu ambiente de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md).

<a name="add-ise-connectors-environment"></a>

## <a name="add-ise-connectors"></a>Adicionar conectores do ISE

Você pode adicionar conectores gerenciados pela Microsoft que estão disponíveis para uso no ISE, mas não são implantados no ISE.

1. No menu do ISE, em **configurações**, selecione **conectores gerenciados**. Na barra de ferramentas, selecione **Adicionar**.

   ![Exibir conectores gerenciados](./media/add-artifacts-integration-service-environment-ise/ise-view-managed-connectors.png)

1. No painel **Adicionar um novo conector gerenciado** , abra a lista **Localizar conector** . Se o conector que você deseja estiver disponível, selecione esse conector e, em seguida, selecione **criar**.

   A lista mostra apenas os conectores que estão qualificados, mas não são implantados no ISE. Os conectores que já estão implantados em seu ISE parecem estar indisponíveis para seleção.

   ![Selecionar conector qualificado](./media/add-artifacts-integration-service-environment-ise/add-managed-connector.png)

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors"></a>Criar conectores personalizados

Para usar conectores personalizados no ISE, crie esses conectores personalizados diretamente dentro do ISE.

1. Localize e abra o ISE, se ainda não estiver aberto. No menu ISE, em **configurações**, selecione **conectores personalizados**  > **Adicionar**.

   ![Criar conector personalizado](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Forneça o nome, a assinatura do Azure e o grupo de recursos do Azure (novo ou existente) a serem usados para o conector personalizado.

1. Na lista **local** , na seção **ambientes do serviço de integração** , selecione o mesmo ISE que seus aplicativos lógicos usam e selecione **criar**, por exemplo:

   ![Selecione o ambiente do serviço de integração](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-with-integration-service-environment.png)

1. Selecione o novo conector personalizado e, em seguida, selecione **Editar**, por exemplo:

   ![Selecionar e editar conector personalizado](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Continue criando o conector da maneira usual de uma definição de [openapi](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) ou [SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector).

1. Para gerenciar conectores personalizados em seu ISE, consulte [gerenciar seu ambiente de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md).

## <a name="next-steps"></a>Próximos passos

* [Gerenciar ambientes de serviço de integração](../logic-apps/ise-manage-integration-service-environment.md)
