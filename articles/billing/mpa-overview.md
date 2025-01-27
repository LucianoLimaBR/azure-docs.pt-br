---
title: Introdução à conta de cobrança do Contrato de Parceiro da Microsoft – Azure CSP
description: Entender sua conta de cobrança do Contrato de Parceiro da Microsoft (CSP)
author: bandersmsft
manager: amberbhargava
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: banders
ms.openlocfilehash: 9c6c28ef296f3b1346fd82641d8c557a500273d8
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376681"
---
# <a name="get-started-with-your-microsoft-partner-agreement-billing-account"></a>Introdução à conta de cobrança do Contrato de Parceiro da Microsoft

Uma conta de cobrança é criada quando você se inscreve para usar o Azure. Você usa a conta de cobrança para gerenciar faturas, pagamentos e acompanhar os custos. Você pode ter acesso a várias contas de cobrança. Por exemplo, você pode ter se inscrito no Azure para seus projetos pessoais. Você também pode ter acesso ao Azure por meio do Contrato Enterprise de sua organização, do Contrato de Cliente da Microsoft ou do Contrato de Parceiro da Microsoft. Para cada um desses cenários, você terá uma conta de cobrança separada.

Este artigo se aplica às contas de cobrança para Contratos de Parceiro da Microsoft. Essas contas são criadas para CSPs (Provedores de Soluções na Nuvem) para gerenciar a cobrança dos clientes na nova experiência de comércio. A nova experiência só está disponível para parceiros que têm, pelo menos, um cliente que tenha aceitado um MCA (Contrato de Cliente da Microsoft) e tenha um plano do Azure. [Verifique se você tem acesso a um Contrato de Parceiro da Microsoft](#check-access-to-a-microsoft-partner-agreement).

## <a name="your-billing-account"></a>Sua conta de cobrança

Sua conta de cobrança do Contrato de Parceiro da Microsoft contém um perfil de cobrança para cada moeda na qual você faz negócios. O perfil de cobrança permite que você gerencie as faturas nas respectivas moedas. Quando você estabelece relacionamentos com clientes, dependendo das moedas deles, as assinaturas do Azure e outras compras são cobradas nos respectivos perfis de cobrança.

O diagrama a seguir mostra o relacionamento entre uma conta de cobrança, perfis de cobrança, clientes e revendedores.

![Diagrama que mostra a hierarquia de cobrança do Contrato de Parceiro da Microsoft](./media/mpa-overview/mpa-hierarchy.svg)

Os usuários com as funções **Administrador Global** e **Agentes Administrativos** em sua organização podem gerenciar contas de cobrança, perfis de cobrança e clientes. Para saber mais, confira [Partner Center – Atribuir funções e permissões de usuários](https://docs.microsoft.com/partner-center/permissions-overview).

## <a name="billing-profiles"></a>Perfis de cobrança

Use um perfil de cobrança para gerenciar suas faturas para uma moeda. Uma fatura mensal é gerada no início do mês para cada perfil de cobrança de sua conta. A fatura contém encargos na moeda do perfil de cobrança para todas as assinaturas do Azure e outras compras do mês anterior. 

Exiba a fatura e baixe os documentos relacionados, como o arquivo de uso e a tabela de preços, no portal do Azure. Para obter mais informações, confira [Baixar faturas de um Contrato de Parceiro da Microsoft](billing-download-azure-invoice.md).

> [!IMPORTANT]
>
> As faturas dos perfis de cobrança contêm encargos para clientes com planos do Azure, bem como compras de SaaS, do Azure Marketplace e de reserva para clientes que não aceitaram o Contrato de Cliente da Microsoft e não têm planos do Azure.

## <a name="customers"></a>Clientes

Exiba e gerencie os clientes que aceitaram um Contrato de Cliente da Microsoft e têm um plano do Azure no portal do Azure. Exiba encargos e transações, além de criar e gerenciar assinaturas do Azure para esses clientes. 

### <a name="enable-policy-to-give-visibility-into-cost"></a>Habilitar a política para oferecer visibilidade do custo

Aplique a política para controlar se os usuários na organização dos clientes podem exibir e analisar o custo de acordo com taxas de Pagamento Conforme o Uso para o consumo do Azure deles. Por padrão, a política está desligada e os usuários não podem exibir o custo. Depois de habilitada, os usuários que têm o acesso do [Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) apropriado em uma assinatura podem exibir e analisar o custo da assinatura. 

Para ativar a política:

1. Entre no [Portal do Azure](https://portal.azure.com).

1. Pesquise **Gerenciamento de Custos do Azure + Cobrança**.

   ![Captura de tela que mostra a pesquisa do portal do Azure](./media/mpa-overview/search-cmb.png)

1. Selecione **Clientes** do lado esquerdo e, em seguida, selecione um cliente na lista.
   
   ![Captura de tela que mostra a seleção de cliente](./media/mpa-overview/mpa-customers.png)

1. Selecione **Políticas** no lado esquerdo.

   ![Captura de tela que mostra as políticas](./media/mpa-overview/mpa-change-policy.png)

1. Selecione **Sim**.

## <a name="resellers"></a>Revendedores

Os provedores indiretos no [modelo de duas camadas](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview#azure-csp-direct-and-azure-csp-indirect) do CSP podem selecionar um revendedor ao criar assinaturas para clientes no portal do Azure. Após a criação, eles podem exibir a lista de assinaturas, filtradas por um revendedor, além de analisar o custo de um cliente por revendedores na análise de custo do Azure.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Verificar o acesso a um Contrato de Parceiro da Microsoft
[!INCLUDE [billing-check-mpa](../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se precisar de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

## <a name="next-steps"></a>Próximas etapas

Confira os seguintes artigos para saber mais sobre a sua conta de cobrança:

- [Criar uma assinatura adicional do Azure para um Contrato de Parceiro da Microsoft](billing-create-subscription.md)
- Integre os dados de cobrança ao seu próprio sistema de relatórios usando as [APIs de Cobrança do Azure](https://docs.microsoft.com/rest/api/billing/)
- [Guia de início rápido do Gerenciamento de Custos do Azure para parceiros](https://go.microsoft.com/fwlink/?linkid=2106482)
