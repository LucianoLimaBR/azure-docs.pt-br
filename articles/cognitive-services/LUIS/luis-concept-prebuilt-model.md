---
title: Modelos predefinidos – LUIS
titleSuffix: Azure Cognitive Services
description: Modelos predefinidos fornecem domínios, intenções, enunciados e entidades. Você pode iniciar o aplicativo com um domínio predefinido ou, posteriormente adicionar um domínio relevante ao aplicativo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: b88801ded3dea7c7514ff117361feba3e95444ed
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264396"
---
# <a name="prebuilt-domain-intent-and-entity-models"></a>Modelos de entidade, intenção e domínio predefinidos

Modelos predefinidos fornecem domínios, intenções, enunciados e entidades. Você pode iniciar o aplicativo com um domínio predefinido ou, posteriormente adicionar um domínio relevante ao aplicativo. 

## <a name="types-of-prebuilt-models"></a>Tipos de modelos predefinidos

Há 3 tipos de modelos predefinidos que o LUIS fornece. Cada modelo poderá ser adicionado ao aplicativo a qualquer momento. 

|Tipo de modelo|Inclui|
|--|--|
|Domínio|Intenções, enunciados, entidades|
|Intenções|Intenções, enunciados|
|Entidades|Apenas entidades| 

## <a name="prebuilt-domains"></a>Domínios predefinidos

O Reconhecimento vocal (LUIS) fornece *domínios predefinidos*, que são conjuntos predefinidos de [intenções](luis-how-to-add-intents.md) e [entidades](luis-concept-entity-types.md) que trabalham juntas para domínios ou categorias comuns de aplicativos clientes. 

Os domínios predefinidos são treinados e prontos para serem adicionados ao aplicativo LUIS. As intenções e entidades em um domínio predefinido são totalmente personalizáveis quando você as adiciona ao aplicativo. 

Se você começar personalizando um domínio predefinido inteiro, exclua as intenções e as entidades que seu aplicativo não precisa usar. Você também pode adicionar algumas intenções ou entidades ao conjunto que o domínio predefinido já fornece. Por exemplo, se você estiver usando o domínio predefinido **Eventos** para um aplicativo de evento esportivo, pode para adicionar entidades a times. Quando você começar a [fornecer declarações](luis-how-to-add-example-utterances.md) para o LUIS, inclua termos que sejam específicos ao seu aplicativo. O LUIS aprende a reconhecê-los e personaliza as intenções e entidades do domínio predefinido para as necessidades do seu aplicativo. 

> [!TIP]
> As intenções e as entidades em um domínio predefinido funcionam melhor juntas. É melhor combinar intenções e entidades do mesmo domínio, quando for possível.
> O domínio predefinido de Utilitários tem intenções que você pode personalizar para uso em qualquer domínio. Por exemplo, você pode adicionar `Utilities.Repeat` ao seu aplicativo e treiná-lo para reconhecer qualquer ação que o usuário quiser repetir em seu aplicativo. 

### <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Alterando o comportamento de uma intenção de domínio predefinido

Você pode achar que um domínio predefinido contém uma intenção que é semelhante a uma que você deseja ter em seu aplicativo do LUIS mas quer que tenha um comportamento diferente. Por exemplo, o domínio predefinido **Locais** compilada fornece uma intenção `MakeReservation` para fazer uma reserva de restaurante, mas você quer que seu aplicativo use essa intenção para fazer reservas de hotel. Nesse caso, você pode modificar o comportamento dessa intenção fornecendo declarações para o LUIS sobre fazer reservas de hotel e rotulá-los usando a intenção `MakeReservation`, de modo que o LUIS possa ser treinado novamente para reconhecer a intenção `MakeReservation` em uma solicitação para reservar um hotel.

Você pode encontrar uma lista completa dos domínios predefinidos na [Referência de domínios predefinidos](./luis-reference-prebuilt-domains.md).

## <a name="prebuilt-intents"></a>Intenções predefinidas

O LUIS fornece intenções predefinidas e seus enunciados. As intenções podem ser adicionadas sem adicionar todo o domínio. Adicionar uma intenção é o processo de adicionar uma intenção e seus enunciados. Tanto o nome da intenção como a lista de enunciados podem ser modificados.  

## <a name="prebuilt-entities"></a>Entidades predefinidas

O LUIS inclui um conjunto de entidades predefinidas para reconhecer tipos comuns de informações como datas, horas, números, medidas e moeda. O suporte a entidade predefinida varia conforme a cultura do seu aplicativo de LUIS. Para obter uma lista completa de entidades predefinidas compatíveis com LUIS, incluindo suporte por cultura, veja a [referência de entidade predefinida](./luis-reference-prebuilt-entities.md).

Quando uma entidade predefinida é incluída em seu aplicativo, suas previsões são incluídas em seu aplicativo publicado. O comportamento das entidades predefinidas é previamente treinado e **não pode** ser modificado. 

> [!NOTE]
> **builtin.datetime** foi preterido. Ele foi substituído por [**builtin.datetimeV2**](luis-reference-prebuilt-datetimev2.md), que fornece o reconhecimento de intervalos de data e hora, bem como reconhecimento aprimorado de datas e horas ambíguas.

## <a name="next-steps"></a>Próximas etapas

Saiba como [adicionar entidades predefinidas](luis-prebuilt-entities.md) ao aplicativo.
