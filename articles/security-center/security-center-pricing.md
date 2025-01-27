---
title: Atualizar para a camada Standard da central de segurança para segurança aprimorada | Microsoft Docs
description: Este artigo fornece informações sobre os preços para a Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/17/2019
ms.author: memildin
ms.openlocfilehash: c5e457a6478fce070070ba7d0d610cbfe4f55024
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554684"
---
# <a name="upgrade-to-security-centers-standard-tier-for-enhanced-security"></a>Atualizar para a camada Standard da central de segurança para aumentar a segurança
A central de segurança do Azure fornece gerenciamento de segurança unificado e proteção avançada contra ameaças para cargas de trabalho em execução no Azure, no local e em outras nuvens. Ele fornece visibilidade e controle sobre as cargas de trabalho de nuvem híbrida, defesas ativas que reduzem a exposição a ameaças e detecção inteligente para ajudá-lo a acompanhar os ataques cibernéticos em rápida evolução.

## <a name="pricing-tiers"></a>Tipos de preço
A Central de Segurança é oferecida em duas camadas:

- A camada **gratuita** é habilitada em todas as suas assinaturas do Azure depois que você visitar o painel da central de segurança do Azure no portal do Azure pela primeira vez ou se estiver habilitado PROGREMMATICALY via API. Se você adicionar outras assinaturas aftewards, a nova assinatura não terá a central de segurança habilitada por padrão, no entanto, você só precisará iniciar o portal da central de segurança e a nova assinatura será registrada e a camada gratuita é aplicada. A camada gratuita fornece política de segurança, avaliação de segurança contínua e recomendações de segurança acionáveis para ajudá-lo a proteger seus recursos do Azure.
- A camada **Standard** estende os recursos da camada gratuita para cargas de trabalho em execução em outras nuvens públicas e privadas, fornecendo gerenciamento de segurança unificado e proteção contra ameaças em suas cargas de trabalho de nuvem híbrida. A camada Standard também adiciona recursos avançados de detecção de ameaças, que usam análise comportamental interna e aprendizado de máquina para identificar ataques e explorações de dia zero, controles de acesso e de aplicativos para reduzir a exposição a ataques de rede e malware e cada. Você pode experimentar a camada Standard gratuitamente. A central de segurança Standard dá suporte a recursos do Azure, incluindo VMs, conjuntos de dimensionamento de máquinas virtuais, serviço de aplicativo, servidores SQL e contas de armazenamento. Se você tiver a central de segurança do Azure padrão, poderá recusar o suporte com base no tipo de recurso. 

A maioria das avaliações de segurança de camada gratuita para VMs, bem como muitos dos alertas de segurança de camada Standard, requer a instalação do recurso de Microsoft Monitoring Agent (MMA). Você pode habilitar o provisionamento automático na central de segurança para implantar automaticamente o agente para suas VMs do Azure.

Para obter mais informações, confira a [página de preços](https://azure.microsoft.com/pricing/details/security-center/) da Central de Segurança.

## <a name="try-standard-free-for-30-days"></a>Experimente o Standard gratuitamente por 30 dias
A camada Standard é oferecida gratuitamente nos primeiros 30 dias. No final de 30 dias, se você optar por continuar usando o serviço, começaremos a cobrar automaticamente pelo uso.

Você pode atualizar uma assinatura inteira do Azure para a camada Standard, que é herdada por todos os recursos na assinatura.

Para obter a camada Standard:

1. Selecione **configurações de & de preços** no menu principal da **central de segurança** .
2. Selecione a assinatura que você deseja atualizar para o Padrão.
3. Selecione **tipo de preço**.
4. Selecione **Standard** para atualizar.
5. Clique em **Salvar**.

(Os preços na imagem são apenas para fins de exemplo.)  Preços do ![Security Center ](./media/security-center-pricing/get-standard.png)

> [!NOTE]
> Para habilitar todos os recursos da central de segurança, você deve aplicar o tipo de preço padrão à assinatura que contém as máquinas virtuais aplicáveis. A configuração de preços para um espaço de trabalho não permite o acesso just-in-time à VM, controles de aplicativos adaptáveis e detecções de rede para recursos do Azure.
>
>

## <a name="why-upgrade-to-standard"></a>Por que atualizar para Padrão?
A central de segurança oferece proteção avançada contra ameaças e segurança para suas cargas de trabalho de nuvem híbrida, incluindo:

- **Segurança híbrida** – obtenha uma exibição unificada de segurança em todas as suas cargas de trabalho locais e na nuvem. Aplique políticas de segurança e avalie continuamente a segurança de suas cargas de trabalho de nuvem híbrida para garantir a conformidade com os padrões de segurança. Colete, pesquise e analise dados de segurança de uma variedade de fontes, incluindo firewalls e outras soluções de parceiros.
- **Detecção avançada de ameaças** -use a análise avançada e a gráfico de segurança inteligente da Microsoft para obter uma borda sobre ataques cibernéticos em evolução.  Aproveite a análise comportamental interna e o aprendizado de máquina para identificar ataques e explorações de dia zero. Monitore redes, computadores e serviços de nuvem para ataques recebidos e para atividades pós-violação. Simplifique a investigação com ferramentas interativas e inteligência contextual contra ameaças.
- **Controles de acesso e de aplicativo** – bloqueie malware e outros aplicativos indesejados aplicando recomendações de lista de permissões adaptadas para suas cargas de trabalho específicas e alimentadas pelo Machine Learning. Reduza a superfície de ataque de rede com acesso controlado just-in-time a portas de gerenciamento em VMs do Azure, reduzindo drasticamente a exposição à força bruta e a outros ataques de rede.


## <a name="next-steps"></a>Próximos passos
Neste artigo, você foi apresentado aos preços da central de segurança. Para saber mais sobre a segurança avançada da camada Standard e a proteção avançada contra ameaças, consulte:

- [Detecção avançada de ameaças](security-center-threat-report.md)
- [Controle de acesso da VM just-in-time](security-center-just-in-time.md)

<!--Image references-->
[1]: ./media/security-center-pricing/get-standard.png
