---
title: Transcrição do Call Center – serviço de fala
titleSuffix: Azure Cognitive Services
description: Um cenário comum de conversão de fala em texto é transcrever grandes volumes de dados de telefonia que podem vir de vários sistemas, como o IVR (resposta interativa de voz). O áudio pode ser estéreo ou mono e bruto com processamento pequeno para sem post executado no sinal. Usando os serviços de fala e o modelo de fala unificado, uma empresa pode obter transcrições de alta qualidade, com muitos sistemas de captura de áudio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 4f5163ba448e4cc7e18b0e794a44003ce5ab1dce
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72516773"
---
# <a name="speech-services-for-telephony-data"></a>Serviços de fala para dados de telefonia

Os dados de telefonia gerados por meio de telefones fixos, celulares e rádios normalmente são de baixa qualidade e a banda estreita no intervalo de 8 KHz, o que cria desafios ao converter a conversão de fala em texto. Os modelos de reconhecimento de fala mais recentes dos serviços de fala do Azure são o Excel para transcrever esses dados de telefonia, mesmo em casos em que os dados são difíceis de entender. Esses modelos são treinados com grandes volumes de dados de telefonia e têm a melhor precisão de reconhecimento de mercado, mesmo em ambientes com ruídos.

Um cenário comum de conversão de fala em texto é transcrever grandes volumes de dados de telefonia que podem vir de vários sistemas, como o IVR (resposta interativa de voz). O áudio que esses sistemas fornecem pode ser estéreo ou mono e brutos com processamento pequeno para sem post feito no sinal. Usando os serviços de fala e o modelo de fala unificado, uma empresa pode obter transcrições de alta qualidade, independentemente dos sistemas usados para capturar áudio.

Os dados de telefonia podem ser usados para entender melhor as necessidades de seus clientes, identificar novas oportunidades de marketing ou avaliar o desempenho de agentes de Call Center. Depois que os dados são transcritas, uma empresa pode usar a saída para uma telemetria aprimorada, identificar frases-chave ou analisar os sentimentos do cliente.

As tecnologias descritas nesta página são da Microsoft internamente para vários serviços de processamento de chamadas de suporte, tanto em modo em tempo real quanto em lote.

Vamos examinar algumas das tecnologias e recursos relacionados oferecidos pela oferta dos serviços de fala do Azure.

> [!IMPORTANT]
> O modelo unificado de serviços de fala é treinado com diversos dados e oferece uma única solução de modelo a vários cenários, desde o ditado até a análise de telefonia.

## <a name="azure-technology-for-call-centers"></a>Tecnologia do Azure para call centers

Além do aspecto funcional dos serviços de fala, sua principal finalidade – quando aplicado ao Call Center, é melhorar a experiência do cliente. Existem três domínios claros em relação a isso:

* Análise de pós-chamada, ou seja, processamento em lote de gravações de chamada
* Processamento de análise em tempo real do sinal de áudio para extrair várias informações à medida que a chamada está ocorrendo (com sentimentos sendo um caso de uso proeminente) e
* Bots (assistentes virtuais), orientando a caixa de diálogo entre o cliente e o bot em uma tentativa de resolver o problema do cliente sem participação no agente ou ser a aplicação de protocolos de ia para auxiliar o agente.

Um diagrama de arquitetura típico da implementação de um cenário de lote é descrito na figura abaixo ![Call arquitetura de transcrição do centro ](media/scenarios/call-center-transcription-architecture.png)

## <a name="speech-analytics-technology-components"></a>Componentes de tecnologia de análise de fala

Quer o domínio seja pós-atualização ou em tempo real, o Azure oferece um conjunto de tecnologias maduras e emergentes para melhorar a experiência do cliente.

### <a name="speech-to-text-stt"></a>Conversão de fala em texto (STT)

A [conversão de fala em texto](speech-to-text.md) é o recurso mais procurado depois de qualquer solução de Call Center. Como muitos dos processos de análise downstream dependem de texto transcrita, a palavra de erro do Word (WER) é de extrema importância. Um dos principais desafios da transcrição do Call Center é o ruído que é predominante no Call Center (por exemplo, outros agentes falando em segundo plano), a rica variedade de localidades de idioma e dialetos, bem como a baixa qualidade do sinal de telefone real. O WER está altamente correlacionado com o quão bem os modelos acústicos e de linguagem são treinados para uma determinada localidade, portanto, ser capaz de personalizar o modelo para sua localidade é importante. Nossos modelos mais recentes da versão 4. x unificados são a solução para a precisão e a latência da transcrição. Treinado com dezenas de milhares de horas de dados acústicos e bilhões de modelos unificados de informações lexicais são os modelos mais precisos do mercado para transcrever os dados do Call Center.

### <a name="sentiment"></a>Sentimento
Medir se o cliente teve uma boa experiência é uma das áreas mais importantes da análise de fala quando aplicada ao espaço do Call Center. Nossa [API de transcrição do lote](batch-transcription.md) oferece análise de sentimentos por expressão. Você pode agregar o conjunto de valores obtidos como parte de uma transcrição de chamada para determinar a opinião da chamada para os agentes e o cliente.

### <a name="silence-non-talk"></a>Silêncio (não-fala)
Não é incomum que 35 por cento de uma chamada de suporte seja o que chamamos de tempo sem conversa. Alguns cenários em que a não-fala ocorre são: os agentes que procuram o histórico de casos anteriores com um cliente, agentes usando ferramentas que permitem acessar a área de trabalho do cliente e executar funções, clientes que estão em espera aguardando uma transferência e assim por diante. É extremamente importante medir quando o silêncio está ocorrendo em uma chamada, já que há um número de diferenciação de clientes importantes que ocorrem em oposição a esses tipos de cenários e onde eles ocorrem na chamada.

### <a name="translation"></a>Tradução
Algumas empresas estão experimentando o fornecimento de transcrições traduzidas de chamadas de suporte a idiomas estrangeiros para que os gerentes de entrega possam entender a experiência mundial de seus clientes. Nossos recursos de [tradução](translation.md) são insuperáveis. Podemos converter áudio em áudio ou áudio em texto de um grande número de localidades.

### <a name="text-to-speech"></a>Conversão de Texto em Fala
[Conversão de texto em fala](text-to-speech.md) é outra área importante na implementação de bots que interagem com os clientes. O caminho típico é que o cliente fala, sua voz é transcrita para texto, o texto é analisado para tentativas, uma resposta é sintetizada com base na intenção reconhecida e, em seguida, um ativo é exibido para o cliente ou uma resposta de voz sintetizada é criado. É claro que tudo isso deve ocorrer rapidamente – a latência é um componente importante no sucesso desses sistemas.

Nossa latência de ponta a ponta está muito baixa considerando as várias tecnologias envolvidas, como [conversão de fala em texto](speech-to-text.md), [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), [bot Framework](https://dev.botframework.com/), conversão de [texto em fala](text-to-speech.md).

Nossas novas vozes também são indistinguíveis de vozes humanas. Você pode usar vozes para dar a seu bot sua personalidade exclusiva.

### <a name="search"></a>Pesquisa
Outro grampo da análise é identificar as interações em que ocorreu um evento ou experiência específica. Isso normalmente é feito com uma das duas abordagens, uma pesquisa ad hoc em que o usuário simplesmente digita uma frase e o sistema responde, ou uma consulta mais estruturada, em que um analista pode criar um conjunto de instruções lógicas que identificam um cenário em uma chamada e cada chamada pode ser indexada em relação a esse conjunto de consultas. Um bom exemplo de pesquisa é a declaração de conformidade onipresente "esta chamada deve ser registrada para fins de qualidade... "– como muitas empresas desejam ter certeza de que seus agentes estão fornecendo esse aviso aos clientes antes que a chamada seja realmente registrada. A maioria dos sistemas de análise tem a capacidade de analisar os comportamentos encontrados por algoritmos de/Search de consulta – pois esse relatório de tendências é, em última instância, uma das funções mais importantes de um sistema de análise. Por meio do [diretório de serviços cognitivas](https://azure.microsoft.com/services/cognitive-services/directory/search/) , sua solução de ponta a ponta pode ser significativamente aprimorada com recursos de indexação e pesquisa.

### <a name="key-phrase-extraction"></a>Extração de frases-chave
Essa área é um dos aplicativos mais desafiadores de análise e uma que se beneficia da aplicação de ia e ML. O principal cenário aqui é inferir a intenção do cliente. Por que o cliente está chamando? Qual é o problema do cliente? Por que o cliente tem uma experiência negativa? Nosso [serviço de análise de texto](https://azure.microsoft.com/services/cognitive-services/text-analytics/) fornece um conjunto de análise pronto para atualizar rapidamente sua solução de ponta a ponta para extrair essas palavras-chave ou frases importantes.

Agora, vamos dar uma olhada no processamento em lotes e nos pipelines em tempo real para o reconhecimento de fala em um pouco mais de detalhes.

## <a name="batch-transcription-of-call-center-data"></a>Transcrição em lote de dados do Call Center

Para transcrever a massa de áudio, desenvolvemos a [API de transcrição do lote](batch-transcription.md). A API de transcrição do lote foi desenvolvida para transcrever grandes quantidades de dados de áudio de forma assíncrona. Com relação à desativação de dados do Call Center, nossa solução é baseada nesses pilares:

* **Precisão**: com modelos unificados de quarta geração, oferecemos qualidade de transcrição insuperável.
* **Latência**: entendemos que, ao fazer transcrições em massa, as transcrições são necessárias rapidamente. Os trabalhos de transcrição iniciados por meio da [API de transcrição do lote](batch-transcription.md) serão colocados em fila imediatamente e, depois que o trabalho começar a ser executado, ele será executado mais rapidamente do que a transcrição em tempo real.
* **Segurança**: entendemos que as chamadas podem conter dados confidenciais. Tenha certeza de que a segurança é uma de nossas prioridades mais altas. Nosso serviço obteve as certificações ISO, SOC, HIPAA e PCI.

Os centros de chamadas geram grandes volumes de dados de áudio diariamente. Se sua empresa armazena dados de telefonia em um local central, como o armazenamento do Azure, você pode usar a [API de transcrição do lote](batch-transcription.md) para solicitar e receber transcrições de forma assíncrona.

Uma solução típica usa esses serviços:

* Os serviços de fala do Azure são usados para transcrever a conversão de fala em texto. Uma assinatura padrão (portanto) para os serviços de fala é necessária para usar a API de transcrição do lote. As assinaturas gratuitas (F0) não funcionarão.
* O [armazenamento do Azure](https://azure.microsoft.com/services/storage/) é usado para armazenar dados de telefonia e as transcrições retornadas pela API de transcrição do lote. Essa conta de armazenamento deve usar notificações, especificamente para quando novos arquivos forem adicionados. Essas notificações são usadas para disparar o processo de transcrição.
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) é usado para criar o URI de SAS (assinaturas de acesso compartilhado) para cada gravação e disparar a solicitação HTTP post para iniciar uma transcrição. Além disso, Azure Functions é usado para criar solicitações para recuperar e excluir transcrições usando a API de transcrição do lote.
* [WebHooks](webhooks.md) são usados para obter notificações quando as transcrições são concluídas.

Internamente, estamos usando as tecnologias acima para dar suporte a chamadas de clientes da Microsoft no modo de lote.
Arquitetura de ![Batch ](media/scenarios/call-center-batch-pipeline.png)

## <a name="real-time-transcription-for-call-center-data"></a>Transcrição em tempo real para dados do Call Center

Algumas empresas são obrigadas a transcrever conversas em tempo real. A transcrição em tempo real pode ser usada para identificar palavras-chave e disparar pesquisas de conteúdo e recursos relevantes para a conversa, para o monitoramento de sentimentos, para melhorar a acessibilidade ou para fornecer traduções para clientes e agentes que não são nativos falantes.

Para cenários que exigem transcrição em tempo real, é recomendável usar o [SDK de fala](speech-sdk.md). Atualmente, a conversão de fala em texto está disponível em [mais de 20 idiomas](language-support.md), e o SDK está disponível C++em C#,, Java, Python, Node. js, Objective-C e JavaScript. Os exemplos estão disponíveis em cada idioma no [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk). Para obter as últimas notícias e atualizações, consulte [notas de versão](releasenotes.md).

Internamente, estamos usando as tecnologias acima para analisar as chamadas de clientes da Microsoft em tempo real à medida que elas acontecem.

![Arquitetura do lote](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>Uma palavra no IVRs

Os serviços de fala podem ser facilmente integrados em qualquer solução usando o [SDK de fala](speech-sdk.md) ou a [API REST](rest-apis.md). No entanto, a transcrição do Call Center pode exigir tecnologias adicionais. Normalmente, uma conexão entre um sistema de IVR e o Azure é necessária. Embora não ofereçamos esses componentes, gostaríamos de descrever o que uma conexão com um IVR envolve.

Vários produtos de serviço IVR ou de telefonia (como Genesys ou AudioCodes) oferecem recursos de integração que podem ser utilizados para habilitar a passagem de áudio de entrada e saída para um serviço do Azure. Basicamente, um serviço do Azure personalizado pode fornecer uma interface específica para definir sessões de chamada telefônica (como início da chamada ou término da chamada) e expor uma API WebSocket para receber áudio de fluxo de entrada que é usado com os serviços de fala. As respostas de saída, como a transcrição de conversa ou conexões com o bot Framework, podem ser sintetizadas com o serviço de conversão de texto em fala da Microsoft e retornadas ao IVR para reprodução.

Outro cenário é a integração direta de SIP. Um serviço do Azure se conecta a um servidor SIP, obtendo, assim, um fluxo de entrada e um fluxo de saída, que é usado para as fases de conversão de texto em texto e Text-to-Speech. Para se conectar a um servidor SIP, há ofertas de software comercial, como o Ozeki SDK, ou [as equipes que chamam e API de reuniões](/graph/api/resources/communications-api-overview) (atualmente em versão beta), que foram projetadas para dar suporte a esse tipo de cenário para chamadas de áudio.

## <a name="customize-existing-experiences"></a>Personalizar experiências existentes

Os serviços de fala do Azure funcionam bem com modelos internos, no entanto, talvez você queira personalizar e ajustar a experiência para seu produto ou ambiente. As opções de personalização variam desde o ajuste do modelo acústico até fontes de voz exclusivas para sua marca. Depois de criar um modelo personalizado, você pode usá-lo com qualquer um dos serviços de fala do Azure em tempo real ou em modo de lote.

| Serviço de fala | Modelo | Descrição |
|----------------|-------|-------------|
| Conversão de fala em texto | [Modelo acústico](how-to-customize-acoustic-models.md) | Crie um modelo acústico personalizado para aplicativos, ferramentas ou dispositivos que são usados em ambientes específicos, como em um carro ou em um piso de fábrica, cada um com condições de gravação específicas. Os exemplos incluem fala acentuada, ruídos de fundo específicos ou uso de um microfone específico para gravação. |
| | [Modelo de linguagem](how-to-customize-language-model.md) | Crie um modelo de linguagem personalizado para melhorar a transcrição do vocabulário e da gramática específicos do setor, como terminologia médica ou jargão de ti. |
| | [Modelo de pronúncia](how-to-customize-pronunciation.md) | Com um modelo de pronúncia personalizada, você pode definir o formulário fonético e a exibição de uma palavra ou termo. É útil para lidar com termos personalizados, como nomes de produtos ou acrônimos. Tudo o que você precisa para começar é um arquivo de pronúncia, um arquivo. txt simples. |
| Conversão de texto em fala | [Fonte de voz](how-to-customize-voice-font.md) | Fontes de voz personalizadas permitem que você crie uma voz de um tipo reconhecível para sua marca. Ele só leva uma pequena quantidade de dados para começar. Quanto mais dados você fornecer, mais natural e humana, como a fonte de voz soará. |

## <a name="sample-code"></a>Código de exemplo

O código de exemplo está disponível no GitHub para cada um dos serviços de fala do Azure. Esses exemplos abrangem cenários comuns, como a leitura de áudio de um arquivo ou fluxo, um reconhecimento contínuo e de captura única e o trabalho com modelos personalizados. Use estes links para exibir exemplos de SDK e REST:

* [Exemplos de conversão de fala em texto e de fala (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Amostras de transcrição em lote (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Exemplos de conversão de texto em fala (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Documentos de referência

* [SDK de fala](speech-sdk-reference.md)
* [SDK de dispositivos de fala](speech-devices-sdk.md)
* [API REST: conversão de fala em texto](rest-speech-to-text.md)
* [API REST: conversão de texto em fala](rest-text-to-speech.md)
* [API REST: transcrição e personalização do lote](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Obtenha uma chave de assinatura de serviços de fala gratuitamente](get-started.md)
