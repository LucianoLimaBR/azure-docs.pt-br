---
title: Limites de serviço de versão prévia dos Gêmeos Digitais do Azure | Microsoft Docs
description: Entenda os limites de serviço de versão prévia pública dos Gêmeos Digitais do Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/22/2019
ms.openlocfilehash: 97bcb4e6fad9c766f2ad059469e75ffd5ab8ec8c
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72800224"
---
# <a name="public-preview-service-limits"></a>Limites do serviço de visualização pública

Durante a visualização pública, os Gêmeos Digitais do Azure tem os seguintes limites de assinatura, instância e taxa temporários.

Essas restrições existem para ajudar a simplificar o aprendizado sobre o novo serviço e seus diversos recursos.

> [!NOTE]
> Esses limites serão aumentados ou removidos pela disponibilidade geral (GA).

## <a name="per-subscription-limits"></a>Limites por assinatura

Durante a visualização pública, cada assinatura do Azure pode criar ou executar apenas uma instância dos Gêmeos Digitais do Azure por vez.

> [!TIP]
> Se você excluir sua instância, poderá criar uma nova.

## <a name="per-instance-limits"></a>Limites por instância

Por sua vez, cada instância de Gêmeos Digitais do Azure podem ter:

- Exatamente um recurso **IoTHub** inserido, que é criado automaticamente durante o provisionamento do serviço.
- Exatamente um ponto de extremidade **EventHub** para o tipo de evento **DeviceMessage**.
- Até três **EventHub**, **ServiceBus** ou **EventGrid** endpoints do tipo de evento **SensorChange**, **SpaceChange**, **TopologyOperation** ou **UdfCustom**.

> [!NOTE]
> Alguns parâmetros que geralmente são definidos ao criar as entidades de IoT do Azure acima não são necessários durante a versão prévia pública.
> - Veja a [documentação de referência do Swagger](./how-to-use-swagger.md) para as especificações de API mais recentes.

## <a name="azure-digital-twins-management-api-limits"></a>Limites de API de Gerenciamento dos Gêmeos Digitais do Azure

Os limites de taxa de solicitação para a API de Gerenciamento dos Gêmeos Digitais do Azure são:

- 100 solicitações por segundo para a API de Gerenciamento Digital dos Gêmeos Digitais do Azure.
- Até 1.000 objetos retornados por uma única consulta de API de Gerenciamento de Gêmeos Digitais do Azure.

> [!IMPORTANT]
> Se você exceder o limite de 1.000 objetos, receberá um erro e deverá simplificar sua consulta.

## <a name="user-defined-functions-rate-limits"></a>Limites de taxa de funções definidas pelo usuário

Os limites a seguir definem o número total de todas as chamadas de função definida pelo usuário feitas à sua instância de Gêmeos Digitais do Azure:

- 400 chamadas de biblioteca de cliente por segundo
- 100 **SendNotification** chamadas por segundo

> [!NOTE]
> As ações a seguir podem fazer com que limites de taxa adicionais sejam aplicados temporariamente:
> - Edições feitas aos metadados do objeto de topologia
> - Atualizações feitas à definição de função definida pelo usuário
> - Dispositivos que enviam telemetria pela primeira vez

## <a name="device-telemetry-limits"></a>Limites de telemetria do dispositivo

Os limites a seguir limitam o número total de todas as mensagens que seus dispositivos podem enviar para sua instância dos Gêmeos Digitais do Azure:

- 100 mensagens por segundo em todos os dispositivos
-   25 mensagens por segundo por dispositivo

## <a name="next-steps"></a>Próximos passos

- Para testar um exemplo de Gêmeos Digitais do Azure,vá para [Início Rápido para encontrar salas disponíveis](./quickstart-view-occupancy-dotnet.md).
