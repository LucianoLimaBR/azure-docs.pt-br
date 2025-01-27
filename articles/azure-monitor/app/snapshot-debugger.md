---
title: Depurador de Instantâneos do Application Insights do Azure para aplicativos .NET | Microsoft Docs
description: Depure instantâneos são coletados automaticamente quando exceções forem geradas na produção de aplicativos .NET
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: brahmnes
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: c0d90d4eed7efb5c82ca9d61565bfe3f49e8aaa1
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809516"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Depurar instantâneos em exceções em aplicativos .NET
Quando ocorrer uma exceção, você pode coletar automaticamente um Instantâneo de Depuração de seu aplicativo web ativo. O instantâneo mostra o estado do código-fonte e variáveis no momento em que a exceção foi lançada. O Depurador de Instantâneo (visualização) no [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) monitora a telemetria de exceção de seu aplicativo Web. Ele coleta instantâneos em suas exceções com mais lançamentos para que você tenha as informações necessárias para diagnosticar problemas na produção. Inclua o [pacote NuGet do coletor de instantâneos](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) em seu aplicativo e, opcionalmente, configure os parâmetros de coleção em [ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Os instantâneos aparecem em [exceções](../../azure-monitor/app/asp-net-exceptions.md) no portal de Application insights.

Você pode exibir instantâneos de depuração no portal para ver a pilha de chamadas e inspecionar variáveis em cada quadro da pilha de chamadas. Para obter uma experiência de depuração mais potente com o código-fonte, abra instantâneos com o Visual Studio 2019 Enterprise. No Visual Studio, também é possível [configurar o Snappoints para fazer instantâneos interativamente](https://aka.ms/snappoint) sem esperar por uma exceção.

Os instantâneos de depuração são armazenados por 15 dias. Essa política de retenção é definida por aplicativo. Se for necessário aumentar esse valor, você poderá solicitar o aumento abrindo um caso de suporte no portal do Azure.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Habilitar Application Insights Depurador de Instantâneos para seu aplicativo
Coleta de instantâneo está disponível para:
* Aplicativos ASP.NET e do .NET framework com o .NET Framework 4.5 ou posterior.
* Aplicativos do .NET Core 2.0 e Núcleo do ASP.NET Core 2.0 em execução no Windows.

Os ambientes a seguir são suportados:

* [Serviço de aplicativo do Azure](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Serviços de nuvem do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) executando a família de sistemas operacionais 4 ou posterior
* [Serviços de Service Fabric do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) em execução no Windows Server 2012 R2 ou posterior
* [Máquinas virtuais do Azure e conjuntos de dimensionamento de máquinas virtuais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) que executam o Windows Server 2012 R2 ou posterior
* [Máquinas virtuais ou físicas locais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) que executam o Windows Server 2012 R2 ou posterior

> [!NOTE]
> Não há suporte para aplicativos cliente (por exemplo, WPF, Windows Forms ou UWP).

Se você tiver habilitado Depurador de Instantâneos, mas não estiver vendo instantâneos, consulte nosso [Guia de solução de problemas](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

## <a name="grant-permissions"></a>Conceder permissões

O acesso a instantâneos é protegido por RBAC (controle de acesso baseado em função). Para inspecionar um instantâneo, primeiro você deve ser adicionado à função necessária por um proprietário da assinatura.

> [!NOTE]
> Proprietários e colaboradores não têm automaticamente essa função. Se quiserem exibir instantâneos, eles deverão ser adicionados à função.

Os proprietários de assinaturas deverão atribuir a função `Application Insights Snapshot Debugger` aos usuários que inspecionarão os instantâneos. Essa função pode ser atribuída a usuários individuais ou a grupos por proprietários de assinatura para o recurso Application Insights de destino ou seu grupo de recursos ou a assinatura.

1. Navegue até o recurso Application Insights no portal do Azure.
1. Clique em **Controle de acesso (IAM)** .
1. Clique no botão **+Adicionar atribuição de função**.
1. Selecione o **Depurador de Instantâneos do Application Insights** na lista suspensa **Funções**.
1. Procure e insira um nome para o usuário a ser adicionado.
1. Clique no botão **Salvar** para adicionar o usuário à função.


> [!IMPORTANT]
> Os instantâneos potencialmente podem conter informações pessoais e outras informações confidenciais em valores de parâmetro e variável.

## <a name="view-snapshots-in-the-portal"></a>Exibir instantâneos no portal

Depois que uma exceção tiver ocorrido em seu aplicativo e um instantâneo tiver sido criado, você deverá ter instantâneos para exibir. Pode levar de 5 a 10 minutos a partir de uma exceção que ocorre em um instantâneo pronto e visível no Portal. Para exibir instantâneos, no painel **falha** , selecione o botão **operações** ao exibir a guia **operações** ou selecione o botão **exceções** ao exibir a guia **exceções** :

![Página falhas](./media/snapshot-debugger/failures-page.png)

Selecione uma operação ou exceção no painel direito para abrir o painel de **detalhes de transação de ponta a ponta** e, em seguida, selecione o evento de exceção. Se um instantâneo estiver disponível para a exceção determinada, um botão **abrir instantâneo de depuração** aparecerá no painel direito com detalhes para a [exceção](../../azure-monitor/app/asp-net-exceptions.md).

![Botão Abrir Instantâneo de Depuração na exceção](./media/snapshot-debugger/e2e-transaction-page.png)

Na exibição do Instantâneo de Depuração, você verá uma pilha de chamadas e um painel de variáveis. Quando você seleciona quadros da pilha de chamadas no painel de pilha de chamadas. permite exibir as variáveis locais e os parâmetros da chamada de função no painel de variáveis.

![Exibir Instantâneo de Depuração no portal](./media/snapshot-debugger/open-snapshot-portal.png)

Os instantâneos podem conter informações confidenciais e, por padrão, não estão visíveis. Para exibir instantâneos, você deve ter a função `Application Insights Snapshot Debugger` atribuída a você.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Exibir instantâneos no Visual Studio 2017 Enterprise ou superior
1. Clique no botão **baixar instantâneo** para baixar um arquivo de `.diagsession`, que pode ser aberto pelo Visual Studio Enterprise.

2. Para abrir o arquivo `.diagsession`, você precisa ter o componente Depurador de Instantâneos Visual Studio instalado. O componente Depurador de Instantâneos é um componente necessário da carga de trabalho ASP.net no Visual Studio e pode ser selecionado na lista de componentes individuais no instalador do Visual Studio. Se você estiver usando uma versão do Visual Studio anterior ao Visual Studio 2017 versão 15,5, será necessário instalar a extensão do [Visual Studio Marketplace](https://aka.ms/snapshotdebugger).

3. Depois de abrir o arquivo de instantâneo, será exibida a página Depuração de Minidespejo no Visual Studio. Clique em **Depurar Código Gerenciado** para iniciar a depuração de instantâneo. O instantâneo abre a linha de código em que a exceção foi lançada e você pode depurar o estado atual do processo.

    ![Exibir instantâneo de depuração no Visual Studio](./media/snapshot-debugger/open-snapshot-visualstudio.png)

O instantâneo baixado contém os arquivos de símbolo encontrados no servidor de aplicativos Web. Esses arquivos de símbolo são necessários para associar dados do instantâneo ao código-fonte. Para aplicativos do Serviço de Aplicativo, habilite a implantação de símbolo quando você publicar seus aplicativos Web.

## <a name="how-snapshots-work"></a>Como os instantâneos funcionam

O Coletor de Instantâneo é implementado como um [Processador do Application Insights Telemetry](../../azure-monitor/app/configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). Quando seu aplicativo é executado, o Processador de Telemetria do Coletor de Instantâneo será adicionado ao pipeline de telemetria do aplicativo.
Cada vez que o aplicativo chama [TrackException](../../azure-monitor/app/asp-net-exceptions.md#exceptions), o Coletor de Instantâneo calcula uma ID do problema do tipo de exceção sendo gerada e o método que a gerou.
Cada vez que o aplicativo chama TrackException, um contador é incrementado para a ID do Problema apropriada. Quando o contador alcançar o valor `ThresholdForSnapshotting`, a ID do Problema será adicionada a um Plano de Coleta.

O Snapshot Collector também monitora as exceções geradas assinando o evento [AppDomain.CurrentDomain.FirstChanceException](https://docs.microsoft.com/dotnet/api/system.appdomain.firstchanceexception). Quando esse evento for acionado, a ID do Problema da exceção será calculada e comparada com as IDs de Problema no Plano de Coleta.
Se houver uma correspondência, será criado um instantâneo do processo em execução. O instantâneo será atribuído a um identificador exclusivo e a exceção será marcada com esse identificador. Depois que o manipulador FirstChanceException retornar, a exceção lançada será processada como normal. Por fim, a exceção acessa o método TrackException novamente, em que, junto com o identificador de instantâneo, será relatada ao Application Insights.

O processo principal continuará a executar e atender o tráfego para os usuários com pouca interrupção. Enquanto isso, o instantâneo será entregue ao processo Carregador de Instantâneo. O Carregador de Instantâneo criará um minidespejo e o carregará no Application Insights junto com qualquer arquivo de símbolo (.pdb) relevante.

> [!TIP]
> - Um instantâneo do processo é um clone suspenso do processo em execução.
> - Criar o instantâneo leva cerca de 10 a 20 milissegundos.
> - O valor padrão para `ThresholdForSnapshotting` é 1. Esse também é o valor mínimo. Portanto, seu aplicativo deve disparar a mesma exceção **duas vezes** antes de um instantâneo ser criado.
> - Defina `IsEnabledInDeveloperMode` como true se desejar gerar instantâneos durante a depuração no Visual Studio.
> - A taxa de criação de instantâneos é limitada pela configuração `SnapshotsPerTenMinutesLimit`. Por padrão, o limite é um instantâneo a cada dez minutos.
> - Não é possível carregar mais de 50 instantâneos por dia.

## <a name="limitations"></a>Limitações

O período de retenção de dados padrão é de 15 dias. Para cada instância de Application Insights, um número máximo de 50 instantâneos é permitido por dia.

### <a name="publish-symbols"></a>Publicar símbolos
O Depurador de Instantâneo requer que os arquivos de símbolo no servidor de produção decodifiquem variáveis e ofereçam uma experiência de depuração no Visual Studio.
A versão 15.2 (ou superior) do Visual Studio 2017 publica símbolos para builds de versão por padrão ao publicar no Serviço de Aplicativo. Em versões anteriores, você precisa adicionar a seguinte linha ao arquivo `.pubxml` de seu perfil de publicação para que os símbolos sejam publicados no modo de versão:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Para a Computação do Azure e outros tipos, certifique-se de que os arquivos de símbolo estejam na mesma pasta que o arquivo .dll do aplicativo principal (geralmente, `wwwroot/bin`) ou que estejam disponíveis no caminho atual.

> [!NOTE]
> Para obter mais informações sobre as diferentes opções de símbolo que estão disponíveis, consulte a [documentação do Visual Studio](https://docs.microsoft.com/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019#output
). Para obter melhores resultados, recomendamos o uso de "completo", "portátil" ou "incorporado".

### <a name="optimized-builds"></a>Builds otimizados
Em alguns casos, variáveis locais não podem ser exibidas em builds de versão devido a otimizações aplicadas pelo compilador JIT.
No entanto, nos Serviços de Aplicativos do Azure, o Coletor de Instantâneo pode cancelar a otimização dos métodos que geraram a exceção que fazem parte do Plano de Coleta.

> [!TIP]
> Instale a extensão de site do Application Insights no seu Serviço de Aplicativo para obter suporte de desotimização.

## <a name="next-steps"></a>Próximos passos
Habilite Depurador de Instantâneos Application Insights para seu aplicativo:

* [Serviço de aplicativo do Azure](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Serviços de nuvem do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Serviços de Service Fabric do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas Virtuais do Azure e Conjuntos de Dimensionamento de Máquinas Virtuais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais ou físicas locais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Além Application Insights Depurador de Instantâneos:
 
* [Definir snappoints em seu código](https://docs.microsoft.com/visualstudio/debugger/debug-live-azure-applications) para obter instantâneos sem esperar por uma exceção.
* [Diagnosticar exceções em seus aplicativos Web](../../azure-monitor/app/asp-net-exceptions.md) explica como deixar as exceções mais visíveis para o Application Insights.
* A [Detecção Inteligente](../../azure-monitor/app/proactive-diagnostics.md) descobre automaticamente anomalias de desempenho.
