---
title: Application Insights do Azure para aplicativos Web JavaScript | Microsoft Docs
description: Obter contagens de sessões e exibições de páginas e dados de clientes da Web e acompanhar padrões de uso. Detecte exceções e problemas de desempenho em páginas da Web do JavaScript.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b710d09-6ab4-4004-b26a-4fa840039500
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: mbullwin
ms.openlocfilehash: b49206c677e2f1b20c154ae0c9e358e8b2b0bbd8
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430203"
---
# <a name="application-insights-for-web-pages"></a>Application Insights para páginas da Web

Saiba mais sobre o desempenho e o uso de sua página da Web ou aplicativo. Se você adicionar [Application insights](app-insights-overview.md) ao script de página, obterá intervalos de carregamentos de página e chamadas AJAX, contagens e detalhes de exceções de navegador e falhas de AJAX, bem como contagens de sessão e usuários. Todos esses itens podem ser segmentados por página, sistema operacional cliente e versão do navegador, localização geográfica e outras dimensões. Você pode definir alertas para contagens de falhas ou carregamento de páginas lento. E inserindo chamadas de rastreamento em seu código JavaScript, você pode controlar como os diferentes recursos do seu aplicativo de página da Web são usados.

O Application Insights pode ser usado com todas as páginas da Web: basta adicionar um breve trecho de JavaScript. Se o seu serviço Web for [Java](java-get-started.md) ou [ASP.net](asp-net.md), você poderá usar os SDKs do lado do servidor em conjunto com o SDK do JavaScript do lado do cliente para obter uma compreensão de ponta a ponta do desempenho do aplicativo.

## <a name="adding-the-javascript-sdk"></a>Adicionando o SDK do JavaScript

1. Primeiro, você precisa de um recurso Application Insights. Se você ainda não tiver uma chave de recurso e de instrumentação, siga as [instruções criar um novo recurso](create-new-resource.md).
2. Copie a chave de instrumentação do recurso no qual você deseja que sua telemetria JavaScript seja enviada.
3. Adicione o SDK Application Insights JavaScript à sua página da Web ou aplicativo por meio de uma das duas opções a seguir:
    * [Configuração do NPM](#npm-based-setup)
    * [Trecho de código JavaScript](#snippet-based-setup)

> [!IMPORTANT]
> Você só precisa usar um dos métodos abaixo para adicionar o SDK Application Insights JavaScript ao seu aplicativo. Se você usar a configuração baseada em NPM, não use a configuração baseada em trechos de código. O mesmo vale para o cenário inverso ao usar a abordagem baseada em trechos de código, não use também a configuração baseada em NPM. 

### <a name="npm-based-setup"></a>configuração baseada em NPM

```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
```

### <a name="snippet-based-setup"></a>Configuração baseada em trecho

Se seu aplicativo não usar o NPM, você poderá instrumentar diretamente suas páginas da Web com Application Insights colando esse trecho de código na parte superior de cada página. Preferivelmente, ele deve ser o primeiro script na seção `<head>` para que possa monitorar possíveis problemas com todas as suas dependências. Se você estiver usando um aplicativo de servidor mais incrivelmente, adicione o trecho de código na parte superior do arquivo `_Host.cshtml` na seção `<head>`.

```html
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Enviando telemetria para o portal do Azure

Por padrão, o SDK do JavaScript Application Insights coleta a um número de itens de telemetria que são úteis para determinar a integridade do seu aplicativo e a experiência do usuário subjacente. Estão incluídas:

- **Exceções não capturadas** em seu aplicativo, incluindo informações sobre
    - Rastreamento de pilha
    - Detalhes da exceção e mensagem que acompanha o erro
    - Número de colunas de & de linha de erro
    - URL em que o erro foi gerado
- **Solicitações de dependência de rede** feitas por solicitações **XHR** de aplicativo e **busca** (coleta de busca são desabilitadas por padrão), incluem informações sobre
    - URL da fonte de dependência
    - Método de & de comando usado para solicitar a dependência
    - Duração da solicitação
    - Código de resultado e status de êxito da solicitação
    - ID (se houver) do usuário que faz a solicitação
    - Contexto de correlação (se houver) em que a solicitação é feita
- **Informações do usuário** (por exemplo, local, rede, IP)
- **Informações do dispositivo** (por exemplo, navegador, sistema operacional, versão, idioma, resolução, modelo)
- **Informações da sessão**

### <a name="telemetry-initializers"></a>Inicializadores de telemetria
Inicializadores de telemetria são usados para modificar o conteúdo da telemetria coletada antes de ser enviada do navegador do usuário. Eles também podem ser usados para impedir que determinada telemetria seja enviada, retornando `false`. Vários inicializadores de telemetria podem ser adicionados à sua instância de Application Insights e são executados para adicioná-los.

O argumento de entrada para `addTelemetryInitializer` é um retorno de chamada que usa um [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API.md#addTelemetryInitializer) como um argumento e retorna um `boolean` ou `void`. Se retornar `false`, o item de telemetria não será enviado, caso contrário, ele prosseguirá para o próximo inicializador de telemetria, se houver, ou será enviado para o ponto de extremidade da coleção de telemetria.

Um exemplo de como usar inicializadores de telemetria:
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```
## <a name="configuration"></a>Configuração
A maioria dos campos de configuração são nomeados de modo que eles podem ser padronizados como false. Todos os campos são opcionais, exceto `instrumentationKey`.

| name | Padrão | Descrição |
|------|---------|-------------|
| instrumentationKey | nulo | **Obrigatório**<br>Chave de instrumentação que você obteve do portal do Azure. |
| accountId | nulo | Uma ID de conta opcional, se seu aplicativo agrupar usuários em contas. Sem espaços, vírgulas, pontos-e-vírgulas, Equals ou barras verticais |
| sessionRenewalMs | 1,8 milhões | Uma sessão será registrada se o usuário estiver inativo por esse período de tempo em milissegundos. O padrão é 30 minutos |
| sessionExpirationMs | 86,4 milhões | Uma sessão será registrada se continuar por esse período de tempo em milissegundos. O padrão é 24 horas |
| maxBatchSizeInBytes | 10000 | Tamanho máximo do lote de telemetria. Se um lote exceder esse limite, ele será imediatamente enviado e um novo lote será iniciado |
| maxBatchInterval | 15000 | Quanto tempo para a telemetria do lote antes de enviar (milissegundos) |
| disableExceptionTracking | falso | Se for true, as exceções não serão concolhidas. O padrão é falso. |
| disableTelemetry | falso | Se for true, a telemetria não será coletada ou enviada. O padrão é falso. |
| enableDebug | falso | Se for true, os dados de depuração **internos** serão lançados como uma exceção **em vez** de serem registrados, independentemente das configurações de log do SDK. O padrão é falso. <br>***Observação:*** A habilitação dessa configuração resultará em uma telemetria descartada sempre que ocorrer um erro interno. Isso pode ser útil para identificar rapidamente problemas com sua configuração ou uso do SDK. Se você não quiser perder a telemetria durante a depuração, considere usar `consoleLoggingLevel` ou `telemetryLoggingLevel` em vez de `enableDebug`. |
| loggingLevelConsole | 0 | Registra erros **internos** de Application insights no console do. <br>0: desativado, <br>1: somente erros críticos, <br>2: tudo (erros & avisos) |
| loggingLevelTelemetry | 1 | Envia erros **internos** de Application insights como telemetria. <br>0: desativado, <br>1: somente erros críticos, <br>2: tudo (erros & avisos) |
| diagnosticLogInterval | 10000 | interno Intervalo de sondagem (em MS) para fila de log interno |
| samplingPercentage | 100 | Porcentagem de eventos que serão enviados. O padrão é 100, o que significa que todos os eventos são enviados. Defina isso se desejar preservar o limite de dados para aplicativos de grande escala. |
| autoTrackPageVisitTime | falso | Se for true, em um Pageview, o tempo de exibição da página instrumentada anterior será rastreado e enviado como telemetria e um novo temporizador será iniciado para o Pageview atual. O padrão é falso. |
| disableAjaxTracking | falso | Se for true, as chamadas AJAX não serão coletadas. O padrão é falso. |
| disableFetchTracking | true | Se for true, as solicitações de busca não serão coletadas. O padrão é true |
| overridePageViewDuration | falso | Se for true, o comportamento padrão de trackPageView será alterado para registrar o final do intervalo de duração da exibição de página quando trackPageView for chamado. Se for false e nenhuma duração personalizada for fornecida para trackPageView, o desempenho de exibição de página será calculado usando a API de tempo de navegação. O padrão é falso. |
| maxAjaxCallsPerView | 500 | Padrão 500-controla quantas chamadas AJAX serão monitoradas por exibição de página. Defina como-1 para monitorar todas as chamadas AJAX (ilimitadas) na página. |
| disableDataLossAnalysis | true | Se for falso, os buffers do remetente da telemetria interna serão verificados na inicialização para os itens ainda não enviados. |
| disableCorrelationHeaders | falso | Se for false, o SDK adicionará dois cabeçalhos (' solicitação-ID ' e ' solicitação-contexto ') a todas as solicitações de dependência para correlacioná-los com as solicitações correspondentes no lado do servidor. O padrão é falso. |
| correlationHeaderExcludedDomains |  | Desabilitar cabeçalhos de correlação para domínios específicos |
| correlationHeaderDomains |  | Habilitar cabeçalhos de correlação para domínios específicos |
| disableFlushOnBeforeUnload | falso | Padrão false. Se for true, o método Flush não será chamado quando o evento onBeforeUnload for disparado |
| enableSessionStorageBuffer | true | Padrão verdadeiro. Se for true, o buffer com todas as telemetrias não enviadas será armazenado no armazenamento de sessão. O buffer é restaurado no carregamento da página |
| isCookieUseDisabled | falso | Padrão false. Se for true, o SDK não armazenará nem lerá nenhum dado de cookies.|
| cookieDomain | nulo | Domínio de cookie personalizado. Isso será útil se você quiser compartilhar Application Insights cookies entre subdomínios. |
| isRetryDisabled | falso | Padrão false. Se for false, tente novamente 206 (êxito parcial), 408 (timeout), 429 (número excessivo de solicitações), 500 (erro interno do servidor), 503 (Serviço indisponível) e 0 (offline, somente se detectado) |
| isStorageUseDisabled | falso | Se for true, o SDK não armazenará nem lerá nenhum dado do armazenamento local e de sessão. O padrão é falso. |
| isBeaconApiDisabled | true | Se for false, o SDK enviará toda a telemetria usando a [API de Beacon](https://www.w3.org/TR/beacon) |
| onunloadDisableBeacon | falso | Padrão false. Quando a guia for fechada, o SDK enviará toda a telemetria restante usando a [API de Beacon](https://www.w3.org/TR/beacon) |
| sdkExtension | nulo | Define o nome da extensão do SDK. Somente caracteres alfabéticos são permitidos. O nome da extensão é adicionado como um prefixo à marca ' ia. Internal. sdkVersion ' (por exemplo, ' ext_javascript: 2.0.0 '). O padrão é nulo. |
| isBrowserLinkTrackingEnabled | falso | O padrão é falso. Se for true, o SDK rastreará todas as solicitações de [link do navegador](https://docs.microsoft.com/aspnet/core/client-side/using-browserlink) . |
| appId | nulo | AppId é usado para a correlação entre dependências AJAX acontecendo no lado do cliente com as solicitações do lado do servidor. Quando a API de Beacon está habilitada, ela não pode ser usada automaticamente, mas pode ser definida manualmente na configuração. O padrão é NULL |
| enableCorsCorrelation | falso | Se for true, o SDK adicionará dois cabeçalhos (' solicitação-ID ' e ' solicitação-contexto ') a todas as solicitações de CORS para correlacionar as dependências do AJAX de saída com as solicitações correspondentes no lado do servidor. O padrão é false |
| namePrefix | Indefinido | Um valor opcional que será usado como sufixo de nome para localStorage e o nome do cookie.
| enableAutoRouteTracking | falso | Rastreie automaticamente as alterações de rota em SPA (aplicativos de página única). Se for true, cada alteração de rota enviará um novo Pageview para Application Insights. As alterações de rota de hash (`example.com/foo#bar`) também são registradas como novas exibições de página.
| enableRequestHeaderTracking | falso | Se verdadeiro, os cabeçalhos de solicitação de busca do AJAX & são rastreados, o padrão é false.
| enableResponseHeaderTracking | falso | Se verdadeiro, os cabeçalhos de resposta da solicitação de busca do AJAX & são rastreados, o padrão é false.
| distributedTracingMode | `DistributedTracingModes.AI` | Define o modo de rastreamento distribuído. Se o modo AI_AND_W3C ou o modo W3C estiver definido, os cabeçalhos de contexto de rastreamento do W3C (traceparent/tracestate) serão gerados e incluídos em todas as solicitações de saída. O AI_AND_W3C é fornecido para compatibilidade com os serviços instrumentados Application Insightss herdados.

## <a name="single-page-applications"></a>Aplicativos de página única

Por padrão, esse SDK **não** tratará da alteração de rota baseada em estado que ocorre em aplicativos de página única. Para habilitar o controle de alterações de rota automático para seu aplicativo de página única, você pode adicionar `enableAutoRouteTracking: true` à sua configuração de instalação.

Atualmente, oferecemos um [plug-in reajam](#react-extensions) separado que você pode inicializar com esse SDK. Ele também realizará o controle de alterações de rota para você, além de coletar [outra telemetria específica de reagir](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md).

## <a name="react-extensions"></a>Extensões de reagir

| Extensões |
|---------------|
| [Reagir](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [React Native](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md)|

## <a name="explore-browserclient-side-data"></a>Explorar dados do navegador/cliente

Os dados do navegador/cliente podem ser exibidos acessando **métricas** e adicionando métricas individuais nas quais você está interessado: 

![](./media/javascript/page-view-load-time.png)

Você também pode exibir seus dados do SDK do JavaScript por meio da experiência do navegador no Portal.

Selecione **navegador** e, em seguida, escolha **falhas** ou **desempenho**.

![](./media/javascript/browser.png)

### <a name="performance"></a>Performance 

![](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Dependências

![](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Análises 

Para consultar sua telemetria coletada pelo SDK do JavaScript, selecione o botão **Exibir em logs (análise)** . Ao adicionar uma instrução `where` de `client_Type == "Browser"`, você verá apenas os dados do SDK do JavaScript e qualquer telemetria do lado do servidor coletada por outros SDKs será excluída.
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>Suporte ao mapa de origem

A pilha de chamadas reduzidos da sua telemetria de exceção pode ser unminified no portal do Azure. Todas as integrações existentes no painel detalhes da exceção funcionarão com o unminified pilha de chamadas recentemente. Arrastar e soltar o mapa de origem unminifying dá suporte a todos os SDKs do JS existentes e futuros (+ Node. JS), portanto, não é necessário atualizar sua versão do SDK. Para exibir o unminified pilha de chamadas,
1. Selecione um item de telemetria de exceção no portal do Azure para exibir seus "detalhes de transação de ponta a ponta"
2. Identifique quais mapas de origem correspondem a esta pilha de chamadas. O mapa de origem deve corresponder ao arquivo de origem de um quadro de pilha, mas com o sufixo `.map`
3. Arraste e solte os mapas de origem na pilha de chamadas no portal do Azure ![](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights Web Basic

Para uma experiência leve, você pode instalar a versão básica do Application Insights
```
npm i --save @microsoft/applicationinsights-web-basic
```
Essa versão vem com o número mínimo de recursos e funcionalidades e se baseia em você para compilá-lo como desejar. Por exemplo, ele não executa a autocoleção (exceções não capturadas, AJAX, etc.). As APIs para enviar determinados tipos de telemetria, como `trackTrace`, `trackException`, etc., não estão incluídas nesta versão, portanto, você precisará fornecer seu próprio wrapper. A única API que está disponível é `track`. Um [exemplo](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) está localizado aqui.

## <a name="examples"></a>Exemplos

Para obter exemplos de executáveis, consulte [Application insights exemplos de SDK do JavaScript](https://github.com/topics/applicationinsights-js-demo)

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Atualizando da versão antiga do Application Insights

Alterações recentes na versão do SDK v2:
- Para permitir melhores assinaturas de API, algumas das chamadas à API, como trackPageView, trackexception foram atualizadas. Não há suporte para execução no IE8 ou em versões anteriores do navegador.
- O envelope de telemetria tem alterações de estrutura e nome de campo devido a atualizações de esquema de dados.
- Movido `context.operation` para `context.telemetryTrace`. Alguns campos também foram alterados (`operation.id` @ no__t-1 @ no__t-2)
  - Se você quiser atualizar manualmente a ID de Pageview atual (por exemplo, em aplicativos de SPA), isso pode ser feito com `appInsights.properties.context.telemetryTrace.traceID = Util.newId()`

Se você estiver usando o 1.0.20 (SDK de produção do Application insights) atual e quiser ver se o novo SDK funciona em tempo de execução, atualize a URL dependendo do seu cenário de carregamento do SDK atual.

- Baixar por meio do cenário da CDN: Atualize o trecho de código que você usa atualmente para apontar para a seguinte URL:
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- cenário de NPM: chame `downloadAndSetup` para baixar o script ApplicationInsights completo da CDN e inicializá-lo com a chave de instrumentação:

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

Teste no ambiente interno para verificar se a telemetria de monitoramento está funcionando conforme o esperado. Se tudo funcionar, atualize suas assinaturas de API adequadamente para a versão do SDK V2 e implante em seus ambientes de produção.

## <a name="sdk-performanceoverhead"></a>Desempenho/sobrecarga do SDK

Em apenas 25 KB de gzip e levando apenas ~ 15 ms para inicializar, Application Insights adiciona uma quantidade insignificante de loadtime ao seu site. Usando o trecho de código, os componentes mínimos da biblioteca são carregados rapidamente. Enquanto isso, o script completo é baixado em segundo plano.

Enquanto o script está sendo baixado da CDN, todo o acompanhamento de sua página é enfileirado. Depois que o script baixado terminar a inicialização assíncrona, todos os eventos que foram enfileirados serão rastreados. Como resultado, você não perderá nenhuma telemetria durante todo o ciclo de vida de sua página. Esse processo de instalação fornece à sua página um sistema de análise contínuo, invisível para os usuários.

> Resumo:
> - gzip de **25 KB**
> - tempo de inicialização geral de **15 ms**
> - Controle **zero** perdido durante o ciclo de vida da página

## <a name="browser-support"></a>Suporte ao navegador

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
✔ Mais recentes | ✔ Mais recentes | mais de 9 ✔ | ✔ Mais recentes | ✔ Mais recentes |

## <a name="open-source-sdk"></a>SDK do código-fonte aberto

A Application Insights SDK do JavaScript é de software livre para exibir o código-fonte ou para contribuir com o projeto, visite o [repositório oficial do GitHub](https://github.com/Microsoft/ApplicationInsights-JS).

## <a name="next"></a> Próximas etapas
* [Acompanhar uso](usage-overview.md)
* [Eventos e métricas personalizados](api-custom-events-metrics.md)
* [Build-measure-learn](usage-overview.md)

