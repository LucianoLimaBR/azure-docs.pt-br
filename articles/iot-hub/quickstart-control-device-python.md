---
title: Início rápido de controle de um dispositivo do Hub IoT do Azure (Python) | Microsoft Docs
description: Neste início rápido, você executa dois aplicativos Python de exemplo. Um aplicativo é um aplicativo back-end que pode controlar remotamente os dispositivos conectados ao seu hub. O outro aplicativo simula um dispositivo conectado ao seu hub que pode ser controlado remotamente.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: 13652b287da94adff5bdf2235900734e5908c56f
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72516662"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-python"></a>Início Rápido: Controlar um dispositivo conectado a um hub IoT (Python)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

O Hub IoT é um serviço do Azure que habilita a ingestão de grandes volumes de telemetria de dispositivos na nuvem e o gerenciamento de seus dispositivos IoT pela nuvem para armazenamento e processamento. Neste início rápido, você usa um *método direto* para controlar um dispositivo simulado conectado ao seu hub IoT. Você pode usar métodos diretos para alterar remotamente o comportamento de um dispositivo conectado ao seu hub IoT.

O início rápido usa dois aplicativos previamente escritos em Python:

* Um aplicativo de dispositivo simulado que responde aos métodos diretos chamados de um aplicativo de back-end. Para receber as chamadas de método direto, esse aplicativo se conecta a um ponto de extremidade específico do dispositivo em seu hub IoT.

* Um aplicativo de back-end que chama os métodos diretos no dispositivo simulado. Para chamar um método direto em um dispositivo, esse aplicativo se conecta a um ponto de extremidade do lado do serviço em seu hub IoT.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Execute o comando a seguir para adicionar a Extensão do Microsoft Azure IoT para a CLI do Azure à instância do Cloud Shell. A Extensão de IoT adiciona comandos específicos do Hub IoT, do IoT Edge e do DPS (Serviço de Provisionamento de Dispositivos) no IoT à CLI do Azure.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Caso ainda não tenha feito isso, faça o download do projeto de exemplo em Python do https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip e extraia o arquivo ZIP.

## <a name="create-an-iot-hub"></a>Crie um hub IoT

Se tiver concluído o [Início Rápido: enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-python.md), pode ignorar esta etapa.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrar um dispositivo

Se tiver concluído o [Início Rápido: enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-python.md), pode ignorar esta etapa.

Um dispositivo deve ser registrado no hub IoT antes de poder se conectar. Neste início rápido, você usa o Azure Cloud Shell para registrar um dispositivo simulado.

1. Execute o comando a seguir no Azure Cloud Shell para criar a identidade do dispositivo.

    **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

    **MyPythonDevice**: esse é o nome do dispositivo que está sendo registrado. É recomendável usar o **MyPythonDevice** conforme mostrado. Se escolher um nome diferente para seu dispositivo, você também precisará usar esse nome ao longo deste artigo, bem como atualizar o nome do dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

2. Execute os seguintes comandos no Azure Cloud Shell para obter a _cadeia de conexão de dispositivo_ referente ao dispositivo que você acabou de registrar:

    **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    Tome nota da cadeia de conexão do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Você usará esse valor posteriormente no início rápido.

3. Você também precisa de uma _cadeia de conexão de serviço_ para permitir que aplicativos de back-end se conectem ao seu hub IoT e recuperem mensagens. O comando abaixo recupera a cadeia de conexão de serviço para o hub IoT:

    **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub show-connection-string \
      --policy-name service \
      --name {YourIoTHubName} \
      --output table
    ```

    Tome nota da cadeia de conexão de serviço, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    Você usará esse valor posteriormente no início rápido. A cadeia de conexão do serviço é diferente da cadeia de conexão do dispositivo que você anotou na etapa anterior.

## <a name="listen-for-direct-method-calls"></a>Escutar chamadas de método direto

O aplicativo de dispositivo simulado se conecta a um ponto de extremidade específico do dispositivo em seu hub IoT, envia telemetria simulada e escuta chamadas de método direto de seu hub. Neste início rápido, a chamada de método direto do hub informa ao dispositivo para alterar o intervalo de envio da telemetria. O dispositivo simulado envia uma confirmação para o hub depois de executar o método direto.

1. Em uma janela de terminal local, navegue até a pasta raiz do projeto Python de exemplo. Em seguida, navegue até a pasta **iot-hub\Quickstarts\simulated-device-2**.

1. Abra o arquivo **SimulatedDevice.py** em seu editor de texto preferido.

    Substitua o valor da variável `CONNECTION_STRING` pela cadeia de conexão do dispositivo que você anotou anteriormente. Salve suas alterações em **SimulatedDevice.py**.

1. Na janela de terminal local, execute os seguintes comandos para instalar as bibliotecas necessárias para o aplicativo de dispositivo simulado:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Na janela de terminal local, execute os seguintes comandos para executar o aplicativo de dispositivo simulado:

    ```cmd/sh
    python SimulatedDevice.py
    ```

    A captura de tela a seguir mostra o resultado à medida que o aplicativo de dispositivo simulado envia telemetria para o seu hub IoT:

    ![Executar o dispositivo simulado](./media/quickstart-control-device-python/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Chamar o método direto

O aplicativo de back-end se conecta a um ponto de extremidade do lado do serviço em seu Hub IoT. O aplicativo faz chamadas de método direto para um dispositivo por meio do hub IoT e escuta as confirmações. Um aplicativo de back-end do Hub IoT normalmente é executado na nuvem.

1. Em outra janela de terminal local, navegue até a pasta raiz do projeto Python de exemplo. Em seguida, navegue até a pasta **iot-hub\Quickstarts\back-end-application**.

1. Abra o arquivo **BackEndApplication.py** em seu editor de texto preferido.

    Substitua o valor da variável `CONNECTION_STRING` pela cadeia de conexão do serviço que você anotou anteriormente. Salve suas alterações em **BackEndApplication.py**.

1. Na janela de terminal local, execute os seguintes comandos para instalar as bibliotecas necessárias para o aplicativo de dispositivo simulado:

    ```cmd/sh
    pip install azure-iothub-service-client future
    ```

1. Na janela de terminal local, execute os comandos a seguir para executar o aplicativo back-end:

    ```cmd/sh
    python BackEndApplication.py
    ```

    A seguinte captura de tela mostra a saída enquanto o aplicativo faz uma chamada de método direto ao dispositivo e recebe uma confirmação:

    ![Executar o aplicativo de back-end](./media/quickstart-control-device-python/BackEndApplication.png)

    Após executar o aplicativo de back-end, você verá uma mensagem na janela do console com o dispositivo simulado em execução, e a taxa de mudança de envio das mensagens:

    ![Alteração no cliente simulado](./media/quickstart-control-device-python/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você já chamou um método direto em um dispositivo de um aplicativo de back-end e respondeu à chamada de método direto em um aplicativo de dispositivo simulado.

Para saber como rotear mensagens de dispositivo para nuvem para destinos diferentes na nuvem, continue n próximo tutorial.

> [!div class="nextstepaction"]
> [Tutorial: Encaminhar a telemetria para pontos de extremidade diferentes para processamento](tutorial-routing.md)