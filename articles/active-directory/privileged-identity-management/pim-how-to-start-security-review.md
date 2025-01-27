---
title: Criar uma revisão de acesso das funções do Azure AD no PIM-Azure Active Directory | Microsoft Docs
description: Saiba como criar uma revisão de acesso das funções do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72d9f0c7df12bd6746cd5cf0336e7bd58b6cc88d
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809027"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Criar uma revisão de acesso das funções do Azure AD no Privileged Identity Management

Os requisitos para que os usuários obtenham acesso a funções com privilégios de Azure Active Directory (AD do Azure) podem mudar ao longo do tempo. Para reduzir o risco associado a atribuições de função obsoletas, você deve examinar o acesso regularmente. Você pode usar o Azure AD Privileged Identity Management (PIM) para criar revisões de acesso para funções privilegiadas do Azure AD. Você também pode configurar revisões de acesso recorrentes que ocorrem automaticamente.

Este artigo descreve como criar uma ou mais revisões de acesso para funções privilegiadas do Azure AD.

## <a name="prerequisites"></a>Pré-requisitos

[Administrador de função com privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Abrir revisões de acesso

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que seja membro da função de administrador de função com privilégios.

1. Abra o **Azure AD Privileged Identity Management**.

1. No menu à esquerda, selecione **funções do Azure ad** e, em seguida, selecione **revisões de acesso**.

1. Em gerenciar, selecione **revisões de acesso**.

    ![Funções do Azure AD – lista de revisões de acesso mostrando o status de todas as revisões](./media/pim-how-to-start-security-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>Inicie a revisão de acesso

Depois de especificar as configurações para uma revisão de acesso, selecione **Iniciar**. A revisão de acesso será exibida na sua lista com um indicador de seu status.

![Lista de revisões de acesso mostrando o status de revisões iniciadas](./media/pim-how-to-start-security-review/access-reviews-list.png)

Por padrão, o Azure AD envia um email para os revisores logo após o início da análise. Se você optar pelo não envio do email pelo Azure AD, certifique-se de informar aos revisores que eles devem concluir uma análise de acesso pendente. Você pode mostrar as instruções sobre como [revisar o acesso às funções do Azure ad](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Gerenciar a análise de acesso

Você pode acompanhar o progresso à medida que os revisores concluírem suas revisões na página **visão geral** da revisão de acesso. Nenhum direito de acesso é alterado no diretório até que a [revisão seja concluída](pim-how-to-complete-review.md).

![Página Visão geral das revisões de acesso mostrando os detalhes da revisão](./media/pim-how-to-start-security-review/access-review-overview.png)

Se esta for uma revisão única, depois que o período de revisão de acesso for concluído ou o administrador parar a revisão de acesso, siga as etapas em [concluir uma revisão de acesso das funções do Azure ad](pim-how-to-complete-review.md) para ver e aplicar os resultados.  

Para gerenciar uma série de revisões de acesso, navegue até a revisão de acesso e você encontrará ocorrências futuras nas revisões agendadas e edite a data de término ou adicione/remova revisores adequadamente.

Com base em suas seleções nas **configurações de conclusão**, a aplicação automática será executada após a data de término da revisão ou quando você interromper manualmente a revisão. O status da revisão será alterado de **concluído** por meio de Estados intermediários, como **aplicar** e, por fim, o estado **aplicado**. Você deve esperar que os usuários negados, se houver, sejam removidos das funções em alguns minutos.

## <a name="next-steps"></a>Próximos passos

- [Examinar o acesso às funções do Azure AD](pim-how-to-perform-security-review.md)
- [Concluir uma revisão de acesso das funções do Azure AD](pim-how-to-complete-review.md)
- [Criar uma revisão de acesso das funções de recurso do Azure](pim-resource-roles-start-access-review.md)
