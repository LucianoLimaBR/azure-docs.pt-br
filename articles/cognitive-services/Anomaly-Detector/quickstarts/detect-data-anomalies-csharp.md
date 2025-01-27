---
title: 'Início Rápido: Detectar anomalias nos dados de série temporal usando o C# e a API REST do Detector de Anomalias'
titleSuffix: Azure Cognitive Services
description: Use a API do Detector de Anomalias para detectar anormalidades em sua série de dados como um lote ou em dados de streaming.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 10/14/2019
ms.author: aahi
ms.openlocfilehash: 222fb5d37065bc40e9c96a9ff3487a7ea8ad0570
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554762"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>Início Rápido: Detectar anomalias nos dados de série temporal usando o C# e a API REST do Detector de Anomalias 

Use este início rápido para começar a usar os dois modos de detecção da API do Detector de Anomalias para detectar anomalias nos dados da série temporal. Este aplicativo C# envia duas solicitações de API contendo dados de série temporal formatados em JSON e obtém as respostas.

| Solicitação de API                                        | Saída do aplicativo                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| Detectar anomalias como um lote                        | A resposta JSON contendo o status de anomalias (e outros dados) para cada ponto de dados nos dados de série temporal e as posições de quaisquer anomalias detectadas. |
| Detectar o status de anomalias do último ponto de dados | A resposta JSON contendo o status de anomalias (e outros dados) para o ponto de dados mais recente nos dados da série temporal.                                                                                                                                         |

 Embora esse aplicativo seja escrito em C#, a API é um serviço Web RESTful compatível com a maioria das linguagens de programação.

## <a name="prerequisites"></a>Pré-requisitos

- Qualquer edição do [Visual Studio 2017 ou posterior](https://visualstudio.microsoft.com/downloads/),

- A estrutura [Json.NET](https://www.newtonsoft.com/json), disponível como um pacote do NuGet. Para instalar o Newtonsoft.Json como um pacote NuGet no Visual Studio:
    
    1. Clique com botão direito no seu projeto no **Gerenciador de Soluções**.
    2. Selecione **Gerenciar Pacotes NuGet**.
    3. Pesquise por *Newtonsoft.Json* e instale o pacote.

- Se você estiver usando Linux/MacOS, este aplicativo poderá ser executado usando [Mono](https://www.mono-project.com/).

- Um arquivo JSON contendo pontos de dados de série temporal. Os dados de exemplo deste Início Rápido podem ser encontrados no [GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json).

### <a name="create-an-anomaly-detector-resource"></a>Criar um recurso do Detector de Anomalias

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>Crie um novo aplicativo

1. No Visual Studio, crie uma solução de console e adicione os pacotes a seguir. 

    [!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=usingStatements)]


2. Crie variáveis para seu ponto de extremidade e sua chave de assinatura. Abaixo estão os URIs que você pode usar para a detecção de anomalias. Eles serão anexados a seu ponto de extremidade de serviço posteriormente para criar as URLs de solicitação da API.

    |Método de detecção  |URI  |
    |---------|---------|
    |Detecção em lote    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |Detecção no ponto de dados mais recente     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    
    [!code-csharp[initial variables for endpoint, key and data file](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>Criar uma função para enviar solicitações

1. Crie uma função assíncrona chamada `Request` que usa as variáveis criadas acima.

2. Defina o protocolo de segurança do cliente e as informações de cabeçalho usando um objeto `HttpClient`. Adicione a chave de assinatura ao cabeçalho `Ocp-Apim-Subscription-Key`. Em seguida, crie um objeto `StringContent` para a solicitação.

3. Envie a solicitação com `PostAsync()` e, em seguida, retorne a resposta.

    [!code-csharp[Request method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=requestMethod)]

## <a name="detect-anomalies-as-a-batch"></a>Detectar anomalias como um lote

1. Crie uma função chamada `detectAnomaliesBatch()`. Construa a solicitação e envie-a chamando a função `Request()` com seu ponto de extremidade, chave de assinatura, a URL para a detecção de anomalias em lote e os dados de série temporal.

2. Desserialize o objeto JSON e grave-o no console.

3. Se a resposta contiver o campo `code`, imprima o código de erro e a mensagem de erro. 

4. Caso contrário, localize as posições de anomalias no conjunto de dados. O campo `isAnomaly` da resposta contém uma matriz de valores boolianos, cada deles indicando se um ponto de dados é uma anomalia. Converta isso em uma matriz de cadeia de caracteres com a função `ToObject<bool[]>()` do objeto de resposta. Itere pela matriz e imprima o índice de qualquer valor `true`. Esses valores correspondem ao índice de pontos de dados anômalos, caso algum seja encontrado.

    [!code-csharp[Detect anomalies batch](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesBatch)]


## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detectar o status de anomalias do último ponto de dados

1. Crie uma função chamada `detectAnomaliesLatest()`. Construa a solicitação e envie-a chamando a função `Request()` com seu ponto de extremidade, chave de assinatura, a URL para a detecção de anomalias do ponto mais recente e os dados de série temporal.

2. Desserialize o objeto JSON e grave-o no console.

[!code-csharp[Detect anomalies latest](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesLatest)]

## <a name="load-your-time-series-data-and-send-the-request"></a>Carregar seus dados da série temporal e enviar a solicitação

1. No método principal de seu aplicativo, carregue seus dados de série temporal JSON com `File.ReadAllText()`. 

2. Chame as funções de detecção de anomalias criadas acima. Use `System.Console.ReadKey()` para manter a janela do console aberta depois de executar o aplicativo.

    [!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=main)]

### <a name="example-response"></a>Exemplo de resposta

Uma resposta bem-sucedida é retornada no formato JSON. Clique nos links abaixo para exibir a resposta JSON no GitHub:
* [Exemplo de resposta de detecção em lote](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [Exemplo de resposta de detecção do ponto mais recente](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
>[Detecção de anomalias de streaming com o Azure Databricks](../tutorials/anomaly-detection-streaming-databricks.md)

* O que é a [API do Detector de Anomalias](../overview.md)?
* [Melhores práticas](../concepts/anomaly-detection-best-practices.md) ao usar a API do Detector de Anomalias.
* O código-fonte desta amostra pode ser encontrado no [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs).
