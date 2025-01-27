---
title: Análise para o mercado comercial no Partner Center
description: Saiba como acessar relatórios analíticos para monitorar vendas, avaliar o desempenho e otimizar suas ofertas do Marketplace.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: 593d43339961ab4576081aaa18a8d68716e40e9b
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332122"
---
# <a name="analytics-for-the-commercial-marketplace-in-partner-center"></a>Análise para o mercado comercial no Partner Center

Saiba como acessar relatórios analíticos no Microsoft Partner Center para monitorar vendas, avaliar o desempenho e otimizar suas ofertas no Marketplace. Como parceiro, você pode monitorar suas listagens de ofertas usando os grafos de visualização e insight de dados com suporte do Partner Center e encontrar maneiras de maximizar suas vendas. As ferramentas de análise aprimoradas permitem que você atue nos resultados de desempenho e mantenha relações melhores com seus clientes e revendedores. 

Para acessar as ferramentas de análise do Partner Center, abra o painel **[analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** em Marketplace comercial.

|**Painel**|**Conteúdo exibido**|
|:---|:---|
|[Resumo](#summary-dashboard)|Grafos, tendências e valores de dados agregados que resumem a atividade do Marketplace para suas ofertas|
|[Solicitar](#orders-dashboard)|Informações sobre seus pedidos em um formato gráfico e baixável|
|[Utilizam](#customer-dashboard)|Informações sobre seus clientes, incluindo tendências de crescimento, apresentadas em um formato gráfico e baixável|
|[Downloads](#downloads-dashboard)|Uma lista de suas solicitações de download nos últimos 30 dias|
|[Classificações e análises](#ratings-and-reviews)| Informações sobre classificações e análises que seus clientes deixam para as ofertas adquiridas.

## <a name="summary-dashboard"></a>Painel de resumo

O painel **Resumo** apresenta uma visão geral baseada em cada tipo de oferta. Os **insights** mostram informações críticas em um relance e fornecem uma visão ampla da atividade de vendas de suas ofertas. Você pode visualizar esses relatórios usando o painel de **Resumo** :

- [Total de pedidos](#totals)
- [Total de clientes](#totals)
- [Localização geográfica de seus clientes](#customers-by-geography)
- [Tendências emergentes com base em suas informações de cliente e de pedidos](#growth-trend-chart)
- [Placar do cliente com pedidos mais altos](#customer-leaderboard)
- [Número de pedidos organizados por nome de oferta](#offers-by-orders)

### <a name="date-range"></a>Intervalo de datas

Você pode encontrar uma seleção de intervalo de datas no canto superior direito de cada página. A saída dos gráficos de página de **Resumo** pode ser personalizada selecionando um intervalo de datas com base nos últimos 3, 6 ou 12 meses ou selecionando um intervalo de datas personalizado com uma duração máxima de 12 meses. O intervalo de datas padrão é de seis meses.

![Painel de análise do Partner Center](./media/analyze-dashboard.png)

### <a name="totals"></a>Totais

A seção **totais** exibe uma contagem de todos os pedidos criados ou clientes adquiridos, durante o intervalo de datas selecionado. 

- O valor percentual ao lado de **pedidos totais** e **total de clientes** representa a quantidade de crescimento em comparação com o mês anterior. 
- Um triângulo verde apontando para cima indica uma tendência de crescimento positivo. Um triângulo vermelho apontando para baixo indica uma tendência de crescimento negativo relativa ao mês anterior. 
- As tendências de crescimento de pedidos e clientes são representadas por grafos de barras e exibirão o valor de cada mês passando o mouse sobre as colunas do gráfico.

![Totais de análise do Partner Center](./media/analyze-totals.png)

### <a name="customers-by-geography"></a>Clientes por geografia

Os **clientes por geography** calor exibem uma contagem de clientes em um mapa mundial.

- Você pode mover o mapa para exibir o local exato.
- Você pode aplicar zoom em um local específico.
- O calor tem uma grade suplementar para exibir os detalhes da contagem de clientes (ou [contagem de pedidos](#orders-by-geography)) no local específico.
- Você pode pesquisar e selecionar um país na grade para aplicar zoom ao local no mapa. Reverta para a exibição original pressionando o botão **página inicial** no mapa.
- Um **novo** cliente comprou uma de suas ofertas pela primeira vez durante o mês dentro do intervalo de datas selecionado.

![Centro de parceiros analisar Geografia do cliente](./media/analyze-customer-geography.png)

### <a name="growth-trend-chart"></a>Gráfico de tendência de crescimento

Você pode exibir as tendências com base no crescimento de seus **pedidos criados** ou **clientes adquiridos**, exibidos mês a mês de acordo com o intervalo de datas selecionado. Você pode analisar ainda mais essas tendências selecionando links abaixo do gráfico, que navega até a **respectiva ou** mais páginas do **cliente** .

![O Partner Center analisa tendências de crescimento](./media/analyze-growth-trends.png)

### <a name="customer-leaderboard"></a>Placar do cliente

Os principais clientes 50 com o maior número de pedidos são exibidos em um *tabuleiro de preenchimento*, classificados por contagem de pedidos e porcentagem de pedidos mais altos. 

- Selecione um cliente para exibir seus detalhes de perfil, pedidos organizados por oferta ou pedidos organizados por tipo de licença do Azure e canal de preço. 
- O gráfico de rosca **ofertas por pedidos** apresenta as quatro principais ofertas (por contagem de pedidos) e as ofertas restantes agrupadas como ' Rest todos '.

> [!NOTE]
> As informações pessoais do cliente serão apresentadas somente se o cliente tiver fornecido consentimento. Você pode exibir essas informações se tiver feito logon com um nível de permissões de função de **proprietário** . Os usuários com a função **colaborador** não poderão exibir essas informações. [Saiba mais sobre as funções e permissões de usuário](./manage-account.md#define-user-roles-and-permissions).

![O Partner Center analisa tendências de crescimento](./media/analyze-growth-trends.png)

### <a name="offers-by-orders"></a>Ofertas por pedidos

O gráfico **ofertas por pedidos** organiza seus pedidos ativos de acordo com o nome da oferta. 

- Você pode arrastar sobre fatias do gráfico de rosca à esquerda para o gráfico de rosca correto a fim de exibir mais detalhes da oferta que você arrastou. Esses dois gráficos permitem que você compare uma oferta específica com o desempenho de todas as outras ofertas (' Rest todos '). 

![Ofertas de análise do Partner Center por pedidos](./media/analyze-offer-by-order.png)

## <a name="orders-dashboard"></a>Painel de pedidos

O painel **Orders** do menu **analisar** exibe os pedidos atuais para todas as suas ofertas de SaaS. Você pode exibir representações gráficas de:

- [Totais do pedido](#order-totals)
- [Pedidos por geografia](#orders-by-geography)
- [Tendências para pedidos ativos e cancelados](#trends-for-active-and-canceled-orders)
- [Pedidos organizados por tipo de licença do Marketplace](#orders-by-marketplace-license-type)
- [Pedidos organizados por clientes novos e existentes](#orders-by-customer-type)
- [Tabela de detalhes do pedido](#order-details-table)

> [!NOTE]
> Há diferenças entre o modo como os relatórios de análise são exibidos no Portal do Cloud Partner (CPP) e no novo programa do Marketplace comercial no Partner Center. Uma maneira específica é que o **vendedor insights** em cpp tem uma guia **Orders & Usage** , que exibe dados para ofertas baseadas em uso e ofertas não baseadas em uso. No Partner Center, a página **pedidos** tem uma guia separada para ofertas de SaaS.

### <a name="order-totals"></a>Totais do pedido

A seção **totais de pedidos** exibe uma contagem de todos os pedidos criados, incluindo pedidos **ativos** e **cancelados** , durante o [intervalo de datas](#date-range)selecionado. 

- O valor percentual ao lado de **pedidos totais** representa a quantidade de crescimento em comparação com o mês anterior. 
- Um triângulo verde apontando para cima indica uma tendência de crescimento positivo. Um triângulo vermelho apontando para baixo indica uma tendência de crescimento negativo relativa ao mês anterior. 
- As tendências de crescimento são representadas por gráficos de barras e exibirão o valor de cada mês passando o mouse sobre as colunas do gráfico.

### <a name="orders-by-geography"></a>Pedidos por geografia

O calor **Orders by geography** exibe uma contagem de seus pedidos em um mapa mundial e funciona da mesma forma que os **[clientes por geografia calor](#customers-by-geography)** .

### <a name="trends-for-active-and-canceled-orders"></a>Tendências para pedidos ativos e cancelados

O gráfico **pedidos ativos por oferece** a rosca organiza todos os seus pedidos ativos de acordo com seus nomes de oferta.

- As quatro principais ofertas são exibidas no grafo e o restante das ofertas é agrupado como ' Rest all'.
- Você pode selecionar ofertas específicas na legenda para exibir somente as ofertas no grafo. 
- Passar o mouse sobre uma fatia no grafo exibirá o número de pedidos e a porcentagem dessa oferta, em comparação com o número total de pedidos em todas as ofertas.
- **Pedidos por ofertas de tendência** exibe tendências de crescimento mês a mês. A coluna mês representa o número de pedidos por nome de oferta. O gráfico de linhas exibe a tendência de percentual de crescimento plotada em um eixo z.
- Você pode usar o controle deslizante na parte superior do gráfico para rolar para a direita e para a esquerda ao longo do eixo x e se concentrar em pontos de dados específicos.
- Você pode exibir o gráfico de tendência selecionando um item específico na legenda.
- Você também pode optar por exibir tendências e dados para **pedidos cancelados**. O grafo funcionará da mesma maneira que os pedidos ativos.

![Partner Center-analisar pedidos ativos](./media/analyze-active-orders.png)

### <a name="orders-by-marketplace-license-type"></a>Pedidos por tipo de licença do Marketplace
<!-- Section needs review and clarification!  -->
O gráfico de **tipo de licença Orders by Marketplace** exibe uma contagem de pedidos mês a mês com base no tipo de licença e no método de cobrança da oferta do Marketplace. Os tipos de licença incluem:

- **Cobrado por meio do Azure**: a Microsoft cobra os clientes em seu nome quando você opta [por vender sua oferta pela Microsoft](./create-new-saas-offer.md#sell-through-microsoft) com esse tipo de licença. Os tipos de pagamento incluem pré-pago por cartão de crédito ou faturamento empresarial.
- **Traga sua própria licença**: a Microsoft não cobra os clientes pelo uso desse tipo de oferta do Marketplace. Listado como **[obter agora (gratuito)](./create-new-saas-offer.md#get-it-now-free)** no Marketplace.
- **Gratuito**: a Microsoft não cobra os clientes pelo uso desse tipo de oferta do Marketplace. Listado como **[avaliação gratuita](./create-new-saas-offer.md##free-trial-listing)** no Marketplace.
- **Microsoft as revendedores**: representa as ofertas vendidas por revendedores da Microsoft como parte do **[programa CSP (provedor de soluções na nuvem)](./create-new-saas-offer.md#csp-program-opt-in)** .

![Partner Center analisar pedidos por tipo de licença](./media/analyze-license-type.png)

### <a name="orders-by-customer-type"></a>Pedidos por tipo de cliente

O gráfico de barras **pedidos por tipo de cliente** exibe o número de pedidos divididos entre **novos clientes** e **clientes existentes**. 

- Um **novo cliente** adquiriu uma ou mais de suas ofertas pela primeira vez no mesmo mês do calendário (eixo y). Um **cliente existente** já adquiriu uma oferta de você antes do mês do calendário relatado (no eixo y). 
- Um gráfico de pizza adicional representa todos os pedidos criados pelo cliente novo ou existente para o intervalo de datas selecionado.
- Em ambos os gráficos, você pode optar por exibir somente os clientes novos ou existentes selecionando a respectiva legenda.

![Partner Center analisar pedidos por tipo de cliente](./media/analyze-order-by-customer.png)

### <a name="order-details-table"></a>Tabela de detalhes do pedido

A **tabela detalhes do pedido** exibe uma lista numerada dos 1000 principais pedidos classificados por data de aquisição.

- Cada coluna na grade é classificável.
- Os dados podem ser extraídos para um arquivo TSV se a contagem dos registros for menor que 1000.
- Se o número de registros acima de 1000, os dados exportados serão colocados de forma assíncrona em uma página de downloads para os próximos 30 dias.
- Os filtros podem ser aplicados à **tabela detalhes do pedido** para exibir apenas os dados nos quais você está interessado. Os dados podem ser filtrados por país, tipo de licença do Azure, tipo de licença do Marketplace, tipo de oferta, status do pedido, trilhas gratuitas, ID da assinatura do Marketplace, ID do cliente e nome da empresa.

![Detalhes do pedido de análise do Partner Center](./media/analyze-order-details.png)

## <a name="customer-dashboard"></a>Painel do cliente

O painel do **cliente** do menu **analisar** exibe dados para clientes que adquiriram suas ofertas. Você pode exibir representações gráficas de:

- [Totais do cliente](#customer-totals)
- [Clientes por geografia](#customers-by-geography)
- [Tendências do cliente](#customer-trends)
- [Clientes por pedidos](#customers-by-orders)
- [Tabela de detalhes do cliente](#customer-details-table)

### <a name="customer-totals"></a>Totais do cliente

A seção **totais do cliente** exibe uma contagem de todos os clientes, incluindo novo, existente e com variação, durante o [intervalo de datas](#date-range)selecionado.

- O percentual de crescimento dos clientes em comparação com o mês anterior é indicado pelo número e indicador ascendente no indicador verde ou inferior em vermelho.
- As tendências de crescimento são representadas por gráficos de barras e exibirão o valor de cada mês passando o mouse sobre as colunas do gráfico.

#### <a name="customer-types"></a>Tipos de clientes

Há três tipos de clientes: novo, existente e com variação. 

- Um novo cliente adquiriu uma ou mais de suas ofertas pela primeira vez no mês selecionado.
- Um cliente existente adquiriu uma ou mais de suas ofertas antes do mês selecionado.
- Um cliente com rotatividade cancelou todas as ofertas adquiridas anteriormente.

### <a name="customer-trends"></a>Tendências do cliente

O gráfico de **tendências do cliente** exibe uma contagem de todos os clientes, incluindo novos, existentes e com variação, com uma tendência de crescimento mensal.

- O gráfico de linhas representa as porcentagens de crescimento geral do cliente. 
- A coluna mês representa a contagem de clientes empilhados por clientes novos, existentes e com rotatividade.
- A contagem de clientes com variação é exibida na direção negativa do eixo Y.
- Você pode selecionar itens de legenda específicos para exibir exibições mais detalhadas. Por exemplo, selecione novos clientes na legenda para exibir apenas os novos clientes.
- Você pode usar o controle deslizante na parte superior do gráfico para rolar para a direita e para a esquerda no eixo x e concentrar-se em pontos de dados específicos para exibir mais detalhadamente.
- Passar o mouse sobre uma coluna do gráfico exibirá detalhes somente para esse mês.

![Partner Center-analisar tendências do cliente](./media/analyze-customer-trends.png)

### <a name="customers-by-orders"></a>Clientes por pedidos

O gráfico **clientes por pedidos** exibe o **principal percentil do cliente** ao longo do eixo x, conforme determinado pelo número de pedidos. O eixo y exibe a contagem de pedidos do cliente. O eixo z (gráfico de linhas) exibe a porcentagem cumulativa do número total de pedidos. Você pode exibir detalhes passando o mouse sobre pontos ao longo do gráfico de linhas.

Por exemplo, você pode aprender com esses dados de pedidos do cliente que os 30% principais de seus clientes estão contribuindo para 83% dos pedidos, igual a 2.130 pedidos.

![Partner Center-analisar pedidos de clientes](./media/analyze-customer-orders.png)

### <a name="customer-details-table"></a>Tabela de detalhes do cliente

A **tabela detalhes do cliente** exibe uma lista numerada dos principais clientes 1000 classificados pela data em que adquiriram primeiro uma de suas ofertas.

- As informações pessoais do cliente só estarão disponíveis se o cliente tiver fornecido consentimento. Você só poderá exibir essas informações se tiver feito logon com um nível de função de **proprietário** de permissões. [Saiba mais sobre as funções e permissões de usuário](./manage-account.md#define-user-roles-and-permissions).
- Cada coluna na grade é classificável.
- Os dados podem ser extraídos para um arquivo TSV se a contagem dos registros for menor que 1000.
- Se o número de registros acima de 1000, os dados exportados serão colocados de forma assíncrona em uma página de downloads para os próximos 30 dias.
- Os filtros podem ser aplicados à tabela para exibir apenas os dados nos quais você está interessado. Os dados podem ser filtrados por nome da empresa, ID do cliente, ID da assinatura do Marketplace, tipo de licença do Azure, data de aquisição, data de perda, email do cliente, país/estado do cliente/cidade/zip, idioma do cliente, etc.

![Análise de detalhes do cliente do Partner Center](./media/analyze-customer-table.png)

## <a name="downloads-dashboard"></a>Painel de downloads

O painel de **downloads** do menu **analisar** exibe solicitações para quaisquer downloads que contenham mais de 1000 linhas de dados do cliente ou do pedido.

Você receberá uma notificação pop-up e um email contendo um link para o painel de **downloads** sempre que solicitar um download com mais de 1000 linhas de dados. Esses downloads de dados estarão disponíveis por um período de 30 dias e, em seguida, removidos.

![Downloads de análise do Partner Center](./media/analyze-downloads.png)

### <a name="dictionary-of-data-terms"></a>Dicionário de termos de dados

| Nome do atributo | Relatórios | Definição|
|---|---|---|
| Tipo de licença do Azure | Cliente, pedido | O tipo de contrato de licenciamento usado pelos clientes para comprar o Azure. Também conhecido como canal |
| Tipo de licença do Azure: Provedor de Soluções de Nuvem | Cliente, pedido | O cliente final adquire o Azure e sua oferta do Marketplace por meio do Provedor de Soluções de Nuvem, que atua como revendedor.|
| Tipo de licença do Azure: Enterprise | Cliente, pedido | O cliente final adquire o Azure e sua oferta do Marketplace por meio de um Contrato Enterprise, assinado diretamente com a Microsoft.|
| Tipo de licença do Azure: Enterprise por meio de Revendedor  | Cliente, pedido | O cliente final compra o Azure e sua oferta do Marketplace por meio de um revendedor que facilita seus Enterprise Agreement com a Microsoft.|  |
| Tipo de licença do Azure: Pagamento Conforme o Uso| Cliente, pedido | O cliente final compra o Azure e sua oferta do Marketplace por meio de um contrato "pré-pago", assinado diretamente com a Microsoft.||
| Nome da instância de nuvem| Solicite| A Microsoft Cloud em que ocorreu uma implantação de VM.||
| Nome da instância de nuvem: Azure global| Solicite| A nuvem global da Microsoft pública.|| |
| Nome da instância de nuvem: Azure governamental | Solicite| Nuvens Microsoft específicas do governo para um dos seguintes governos: China, Alemanha ou a Estados Unidos da América.| |
| Cidade do cliente| Cliente| O nome da cidade fornecida pelo cliente. A cidade pode ser diferente da cidade na assinatura do Azure de um cliente.||
| Idioma de comunicação do cliente  | Cliente| O idioma preferido pelo cliente para comunicação.||
| Nome da empresa do cliente | Cliente, pedido | O nome da empresa fornecido pelo cliente. O nome pode ser diferente da cidade na assinatura do Azure de um cliente.|  |
| País/Região do cliente | Cliente, pedido | O nome do país fornecido pelo cliente. O país pode ser diferente do país na assinatura do Azure de um cliente.|  |
| Email do cliente| Cliente| O endereço de email fornecido pelo cliente final. O email pode ser diferente do endereço de email na assinatura do Azure de um cliente.||
| Nome do cliente| Cliente| O nome fornecido pelo cliente. O nome pode ser diferente do nome fornecido na assinatura do Azure de um cliente.| |
| ID do cliente | Cliente, pedido | O identificador exclusivo atribuído a um cliente. Um cliente pode ter zero ou mais assinaturas do Azure Marketplace.|  |
| Código postal do cliente  | Cliente| O CEP fornecido pelo cliente. O código pode ser diferente do código postal fornecido na assinatura do Azure de um cliente.| |
| Estado do cliente| Cliente| O estado (endereço) fornecido pelo cliente. O estado pode ser diferente do estado fornecido na assinatura do Azure de um cliente.| |
| Data da aquisição| Cliente| A primeira data em que o cliente comprou qualquer oferta publicada por você.| |
| Data da perda| Cliente| A última data em que o cliente cancelou o último de todas as ofertas adquiridas anteriormente.||
| É novo cliente  | Solicite| O valor identificará um novo cliente adquirindo uma ou mais das suas ofertas pela primeira vez (ou não). O valor será "Sim" se estiver no mesmo mês do calendário para "data de aquisição". O valor será "não" se o cliente tiver comprado uma de suas ofertas antes do relatório do mês do calendário. |
| É versão prévia do SKU| Solicite| O valor informará se você marcou a SKU como "visualização". O valor será "Sim" se o SKU tiver sido marcado adequadamente e somente as assinaturas do Azure autorizadas por você puderem implantar e usar essa imagem. O valor será "no" se o SKU não tiver sido identificado como "Preview".  |
| É aceitar contato promocional| Cliente| O valor permitirá que você saiba se o cliente optou proativamente para contato promocional dos editores. Não estamos apresentando a opção aos clientes no momento; portanto, indicamos "Não" de forma generalizada. Após a implantação desse recurso, começaremos a atualizar de acordo.|
| Tipo de licença do Marketplace| Solicite| O método de cobrança da oferta do Marketplace.||
| Tipo de licença do Marketplace: cobrado por meio do Azure| Solicite| A Microsoft é seu agente para esta oferta do Marketplace e cobra os clientes em seu nome. (Cartão de crédito PAYG ou fatura Enterprise)||
| Tipo de licença do Marketplace: traga sua própria licença | Solicite| A VM requer uma chave de licença fornecida pelo cliente para implantar. A Microsoft não fatura os clientes para listar suas ofertas dessa maneira por meio do Marketplace.||
| Tipo de licença do Marketplace: gratuito| Solicite| A oferta está configurada para ser gratuita para todos os usuários. A Microsoft não cobra os clientes pelo uso dessa oferta.||
| Tipo de licença do Marketplace: Microsoft como revendedor  | Solicite| A Microsoft é o revendedor para esta oferta do Marketplace.|  |
| ID da assinatura do Marketplace | Cliente, pedido | O identificador exclusivo associado à assinatura do Azure que o cliente usou para comprar sua oferta do Marketplace. ID era anteriormente a GUID de assinatura do Azure.||
| Nome da oferta  | Solicite| O nome da oferta do Marketplace.|| |
| Tipo de oferta  | Solicite| O tipo de oferta de Microsoft Marketplace.|||
| Tipo de oferta: aplicativo gerenciado  | Ordene | Usar o tipo de oferta aplicativo do Azure: aplicativo gerenciado quando as seguintes condições são necessárias: você implanta uma solução baseada em assinatura para seu cliente usando uma VM ou uma solução inteira baseada em IaaS. Você ou seu cliente exigem que a solução seja gerenciada por um parceiro. |
| Tipo de oferta: Aplicativo Azure| Ordene | Use o tipo de oferta de modelo de solução Aplicativo Azure quando sua solução exigir implantação adicional e automação de configuração além de uma VM simples.||
| Tipo de oferta: serviço de consultoria| Solicite| Serviços de Consultoria no Azure Marketplace ajudam a conectar os clientes aos serviços para dar suporte a aumentar seu uso do Azure.| |
| Tipo de oferta: contêiner | Solicite| Use o tipo de oferta de Contêiner quando a solução é uma imagem de contêiner do Docker provisionada como um serviço de contêiner do Azure baseado em Kubernetes.||
| Tipo de oferta: Dynamics 365 Business central| Solicite| Use esse tipo de oferta quando sua solução estiver integrada com o Dynamics 365 para finanças e operações| |
| Tipo de oferta: Dynamics 365 para compromisso com o cliente | Solicite| Use esse tipo de oferta quando sua solução estiver integrada com o Dynamics 365 for Customer Engagement.||
| Tipo de oferta: módulo de IoT Edge | Solicite| Os módulos de Azure IoT Edge são as menores unidades de computação gerenciadas pelo IoT Edge e podem conter serviços da Microsoft (como Azure Stream Analytics), serviços de terceiros ou seu próprio código específico da solução. |
| Tipo de oferta: Power BI aplicativo | Solicite| Use o tipo de oferta de aplicativo Power BI ao implantar um aplicativo integrado com Power BI.|  |
| Tipo de oferta: aplicativo SaaS| Solicite| Use o tipo de oferta de aplicativo SaaS para permitir que o cliente compre a solução técnica baseada em SaaS como uma assinatura.||
| Tipo de oferta: máquina virtual | Solicite| Use o tipo de oferta de Máquina Virtual quando implantar um dispositivo virtual na assinatura associada ao cliente.||
| Tipo de oferta: extensão de Visual Studio Marketplace  | Solicite| Tipo de oferta disponível anteriormente para os desenvolvedores de extensão DevOps do Azure. Avançar os desenvolvedores da extensão DevOps do Azure podem vender sua extensão diretamente para os clientes. As ofertas de extensão podem ser configuradas como pagas ou incluindo uma avaliação. |
| Data de cancelamento do pedido| Solicite| A data em que o pedido do Marketplace foi cancelado.||
| ID do Pedido| Solicite| O identificador exclusivo da ordem do cliente para o serviço do Marketplace. As ofertas baseadas em uso da máquina virtual não estão associadas a um pedido.| |
| Data de compra do pedido| Solicite| A data em que a ordem do Marketplace foi criada.|||
| Status do pedido| Solicite| O status de um pedido do Marketplace no momento em que os dados foram atualizados pela última vez.|     |
| Status do pedido: ativo  | Solicite| O cliente comprou um pedido e não cancelou seu pedido.|         |
| Status do pedido: cancelado | Solicite| O cliente comprou um pedido anteriormente e, subsequentemente, cancelou seu pedido.||
| Email do provedor| Cliente| O endereço de email do provedor envolvido na relação entre a Microsoft e o cliente final. Se o cliente for uma empresa por meio do revendedor, esse será o revendedor. Se um CSP (provedor de soluções de nuvem) estiver envolvido, esse será o CSP.|
| Nome do provedor| Cliente| O nome do provedor envolvido na relação entre a Microsoft e o cliente final. Se o cliente for uma empresa por meio do revendedor, esse será o revendedor. Se um CSP (provedor de soluções de nuvem) estiver envolvido, esse será o CSP.|
| SKU| Solicite| Nome do SKU, conforme definido durante a publicação. Uma oferta pode ter muitas SKUs, mas uma SKU só pode ser associada a uma única oferta.||
| Data de término da avaliação gratuita| Solicite| A data em que o período de avaliação gratuita deste pedido terminará ou terminou.||

## <a name="ratings-and-reviews"></a>Classificações e análises

À medida que os clientes navegam, pesquisam e compram ofertas em ambos os Marketplaces, eles podem deixar classificações e análises para as ofertas adquiridas. Eles são capazes de enviar novas classificações ou revisões e atualizar ou excluir classificações ou revisões já enviadas. As revisões são lançadas no Azure Marketplace ou AppSource na guia revisões da página de exibição do produto da oferta. Os clientes podem incluir seus nomes para classificações ou análises, ou podem postar anonimamente.  

> [!NOTE]
> Os clientes podem fazer alterações apenas nas classificações e análises que elas possuem.

O **painel de análise de & de classificações** exibe as tendências e as ideias das atividades do cliente. Ele fornece representações gráficas de:

- Avaliações & revisões  
- Examinar comentários

### <a name="marketplace-storefront-tabs"></a>Guias de vitrine do Marketplace

Os editores podem exibir suas métricas de oferta separadamente nas guias do AppSource & Azure Marketplace. Para exibir as métricas de oferta específicas, selecione **oferta** na lista suspensa:

![Menu suspenso da oferta de análise do Partner Center](./media/analyze-offer-dropdown.png)

### <a name="ratings-and-reviews-summary"></a>Resumo de classificações e avaliações

A seção de resumo classificações & revisões exibe as métricas abaixo para um intervalo de datas selecionado:

- **Classificação média:** Classificação média ponderada em estrela de todas as classificações enviadas pelos clientes para a oferta selecionada.
- **Divisão de classificação:** Divisão da classificação por estrelas pela contagem de clientes que enviaram classificações. O gráfico de barras é empilhado com classificações reais e revisadas (contagem de classificação atualizada).
- **Total de classificações:** Contagem geral de classificações enviadas. Essa contagem também inclui classificações com e sem revisões.
- **Classificações com revisões:** Contagem de revisões enviadas.

![Resumo de análise de avaliações e avaliações do Partner Center](./media/analyze-ratings-summary.png)

### <a name="review-comments"></a>Examinar comentários

As revisões são exibidas em ordem cronológica para quando elas foram lançadas. O modo de exibição padrão exibe todas as revisões. Você pode filtrar por meio das revisões por classificação por estrelas usando o **filtro** no menu suspenso. Você também pode pesquisar palavras-chave que aparecem na revisão.  

![Comentários da análise do Partner Center analisar](./media/analyze-reviews.png)

## <a name="frequently-asked-questions"></a>Perguntas frequentes

Esta seção fornece respostas para perguntas frequentes sobre **nenhuma análise disponível ainda** em mensagens no Partner Center.

**Não consigo exibir meus dados de análise no Partner Center. Quando acesso essas páginas, vejo a mensagem abaixo. Por que isso?**

![Não há dados para suas ofertas ainda](./media/analytics-faq-no-data.png)

Por que você pode estar recebendo esta mensagem:

- Suas ofertas listadas no Marketplace não são ofertas de SaaS. Atualmente, a análise de relatórios do Partner Center para SaaS oferece apenas. As ofertas do Dynamics 365 podem ser publicadas no Partner Center, mas a análise dessas ofertas ainda não está habilitada no Partner Center.
- No momento, não existem aquisições para suas ofertas publicadas no Marketplace. Isso pode significar que suas ofertas estão ativas no Marketplace e obtendo exibições de clientes nas páginas de exibição do produto, mas os clientes ainda não tomaram medidas para comprá-las e implantá-las.
- A publicação da sua oferta pode estar em processo e ainda não está dinâmica. Somente as ofertas dinâmicas podem ser adquiridas pelos clientes. Para verificar o status de suas ofertas, consulte Visão geral no [painel analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary). Para obter mais informações, consulte [painel de resumo](#summary-dashboard) neste artigo.
- Suas ofertas podem estar listadas como **contato comigo**, que são ofertas somente de lista e não podem ser adquiridas por clientes no Marketplace. Embora essas ofertas gerem leads e sejam compartilhadas com você, as ordens não são criadas para essas ofertas, pois não podem ser adquiridas. Para verificar o tipo de listagem de oferta, vá para a página configuração.

**Sei que tenho dados de análise, mas a mensagem abaixo está aparecendo:**

![Não há dados para o intervalo de datas fornecido](./media/analytics-faq-data-range.png)

Se você estiver recebendo esta mensagem, isso significa que você tem dados de análise, mas não há dados para o intervalo de datas selecionado. Selecione um intervalo de datas diferente ou um intervalo de datas personalizado para exibir os dados desde 2010. Para obter mais informações, consulte [intervalo de datas](#date-range).
