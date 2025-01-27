---
title: Criar uma nova oferta de SaaS no Marketplace comercial
description: Como criar uma nova oferta de SaaS (software como serviço) para listagem ou venda no Azure Marketplace, AppSource ou por meio do programa CSP (provedor de soluções na nuvem) usando o portal do Marketplace comercial no Microsoft Partner Center.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: conceptual
ms.date: 10/04/2019
ms.openlocfilehash: 578904d40b1354dde99644cb2fc73e0a56223b34
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376281"
---
# <a name="create-a-new-saas-offer"></a>Criar uma nova oferta de SaaS

Para começar a criar ofertas de SaaS (software como serviço), certifique-se de primeiro [criar uma conta do Partner Center](./create-account.md) e abra o [painel do Marketplace comercial](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), com a guia **visão geral** selecionada.

![Painel do Marketplace comercial no Partner Center](./media/new-offer-overview.png)

>[!Note]
> Depois que uma oferta tiver sido publicada, edições para a oferta feita no Partner Center serão atualizadas somente no sistema e armazenará os frontais após a republicação. Certifique-se de enviar a oferta para publicação depois de fazer alterações.

Selecione a **nova oferta + novo...** , selecione o item de menu **software como um serviço** . 

Se você selecionar outro tipo de oferta, poderá ser redirecionado para o [portal do Cloud Partner](https://cloudpartner.azure.com/)mais antigo. Somente as ofertas de SaaS e Dynamics 365 estão disponíveis no portal do Marketplace comercial no Partner Center no momento.

![Criar janela de oferta no Partner Center](./media/new-offer-click.png)

A caixa de diálogo **nova oferta** é exibida. 

![Caixa de diálogo nova oferta](./media/new-offer-popup.png)


## <a name="offer-id-and-alias"></a>ID da oferta e alias

- **ID da oferta**: identificador exclusivo para cada oferta em sua conta. Essa ID será visível para os clientes no endereço URL para a oferta do Marketplace e os modelos de Azure Resource Manager (se aplicável). A ID da oferta deve estar em letras minúsculas, alfanuméricas (incluindo hifens e sublinhados, mas sem espaço em branco). Isso é limitado a 50 caracteres e não pode ser alterado depois que você seleciona *criar*.  
Exemplo: Test-offer-1
<br>Resultando na URL: `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`

- **Alias da oferta**: o nome usado para fazer referência à oferta no portal do Partner Center. Esse nome não será usado no Marketplace e será diferente do nome da *oferta* e outros valores que serão mostrados aos clientes. Esse valor não pode ser alterado depois que você seleciona *criar*.

<br>Exemplo: oferta de teste 1&#8482;

Clique em **Criar**.  Uma página de **visão geral da oferta** é criada para esta oferta.  

<!---
![Offer overview on Partner Center](./media/commercial-marketplace-offer-overview.png)
-->

## <a name="offer-overview"></a>Visão geral da oferta

A página **visão geral da oferta** inclui: 

- O **status de publicação** exibe uma representação visual das etapas necessárias para publicar essa oferta e quanto tempo cada etapa levará para ser concluída. Os ícones de etapa de publicação incompletos ficarão esmaecidos. 

- O menu **visão geral da oferta** contém uma lista de links para executar operações nessa oferta. Essa lista de operações será alterada com base na seleção feita para sua oferta.  
    - Se a oferta for um rascunho – excluir rascunho 
    - Se a oferta for dinâmica, interrompa a oferta de venda 
    - Se a oferta estiver em visualização – Go-Live 
    - Se você ainda não concluiu a saída do Publicador – cancelar publicação

## <a name="offer-setup"></a>Instalação da oferta

A guia **instalação da oferta** solicita as seguintes informações. Selecione **salvar** depois de concluir esses campos.

- **Você gostaria de vender pela Microsoft?** (Sim/não)
    - **Sim**, você gostaria de vender sua oferta pela Microsoft, com as transações do Marketplace de hospedagem da Microsoft em seu nome; or 
    - **Não**, você prefere apenas listar sua oferta por meio dos Marketplaces, processando quaisquer transações monetárias independentemente da Microsoft.    

### <a name="sell-through-microsoft"></a>Vender por meio da Microsoft

A venda pela Microsoft fornece melhor descoberta de clientes e aquisição, permite que a Microsoft hospede transações de Marketplace em seu nome e aproveita os recursos de comércio disponíveis globalmente da Microsoft.

#### <a name="saas-offer-requirements"></a>Requisitos da oferta de SaaS

Para listar ofertas de SaaS (software como serviço) com o Marketplace comercial no Partner Center, os critérios a seguir devem ser atendidos:

- Sua oferta deve usar [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) para gerenciamento de identidade e autenticação.
- Sua oferta deve usar [APIs de preenchimento de SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) para integrar com o Azure Marketplace.
- Para obter requisitos mais amplos, consulte o [Guia de publicação da oferta de SaaS](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

#### <a name="saas-pricing-and-billing-options"></a>Opções de preços e cobrança de SaaS
Com as soluções de SaaS em execução na assinatura do Azure do Publicador, as taxas de licença pagas pelos clientes incluem o custo da infraestrutura na qual o software é implantado. O uso de infraestrutura do Azure é gerenciado e cobrado para você, o parceiro, diretamente. Os valores reais de uso da infraestrutura não são vistos pelo cliente. Os editores devem agrupar as taxas de uso de infraestrutura do Azure em seus preços de licença de software. 

O SaaS oferece suporte à cobrança mensal ou anual com base em taxas fixas, por usuário ou cobranças de consumo usando o serviço de cobrança limitado. O Marketplace comercial da Microsoft opera em um modelo de agência, no qual os editores definem preços, a Microsoft Bills Customers e a Microsoft paga receita para o Publicador durante a retenção de uma tarifa de agência.

A tabela a seguir mostra um exemplo de quebra de custos e pagamentos para demonstrar o modelo de agência.

|**Seu custo de licença**|**$100 por mês**|
|:---|:---|
|Custo de uso do Azure (D1/1-Núcleo)|Cobrados diretamente para o publicador, não o cliente|
|O cliente é cobrado pela Microsoft|$100 por mês (o Publicador deve considerar os custos incorridos ou da infraestrutura de passagem na taxa de licença)|

|**Faturas da Microsoft**|**$100 por mês**|
|:---|:---|
|A Microsoft paga para você 80% do seu custo de licença <br>**para aplicativos SaaS qualificados, a Microsoft paga 90% do seu custo de licença*|US $80,00 por mês <br>*$* 90, 0 por mês *|

- Neste exemplo, a Microsoft fatura $100 para o cliente para sua licença de software e paga $80 para o Publicador.
- Os parceiros qualificados para a **taxa reduzida de serviço do Marketplace** verão uma taxa de transação reduzida nas ofertas de SaaS de maio de 2019 até junho de 2020. Nesse cenário, a Microsoft cobra $100 pela sua licença de software e paga $90 para o Publicador.

> [!NOTE]
> **Redução da taxa de serviço do Marketplace**: para determinadas ofertas de SaaS que você publicou em nosso mercado comercial, a Microsoft reduzirá sua taxa de serviço do Marketplace de 20% (conforme descrito no contrato do Microsoft Publisher) para 10%. Para que sua oferta seja qualificada, pelo menos uma de suas ofertas deve ter sido designada pela Microsoft como sendo de revenda de IP coexistente ou em uma venda de IP.  A qualificação deve ser atendida pelo menos cinco (5) dias úteis antes do final de cada mês do calendário para receber essa taxa de serviço do Marketplace reduzida para o mês.  A taxa reduzida de serviço do Marketplace não se aplica a VMs, aplicativos gerenciados ou quaisquer outros produtos disponibilizados por meio de nosso mercado comercial.  A taxa reduzida de serviço do Marketplace só estará disponível para ofertas qualificadas para encargos de licença coletados pela Microsoft entre 1º de maio de 2019 e 30 de junho de 2020.  Após esse período, a taxa de serviço do Marketplace voltará ao seu valor normal. 




#### <a name="csp-program-opt-in"></a>Aceitação do programa CSP
O programa [CSP (provedor de soluções na nuvem)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) permite que as ofertas de software alcancem milhões de clientes qualificados da Microsoft com investimento mínimo em marketing e vendas.

- **Canais: tornar minha oferta disponível no programa CSP** (caixa de seleção)

Optar por tornar sua oferta disponível no programa CSP permite que os provedores de soluções de nuvem vendam seu produto como parte de uma solução agrupada para seus clientes. 

### <a name="list-through-microsoft"></a>Listar pela Microsoft

Promova seus negócios com a Microsoft criando uma listagem do Marketplace. Selecionar para listar sua oferta somente e não realizar transações por meio da Microsoft significa que a Microsoft não participará diretamente da transação de licença de software. Não há nenhuma taxa de transação associada e o Publicador mantém 100% de quaisquer taxas de licenciamento de software coletadas do cliente. No entanto, o Publicador é responsável por dar suporte a todos os aspectos da transação de licença de software, incluindo, mas não se limitando a: preenchimento de pedido, medição, cobrança, faturamento, pagamento e coleção. 

- **Como você deseja que clientes potenciais interajam com esta oferta de listagem?**

##### <a name="get-it-now-free"></a>Obtenha agora (gratuito)
Liste sua oferta aos clientes gratuitamente fornecendo uma URL válida (começando com *http* ou *https*) em que eles podem acessar seu aplicativo.  Por exemplo: `https://contoso.com/saas-app`

##### <a name="free-trial-listing"></a>Avaliação gratuita (listagem)
Liste sua oferta aos clientes com um link para uma avaliação gratuita fornecendo uma URL válida (começando com *http* ou *https*), em que eles podem obter uma avaliação por meio de [autenticação com um clique usando Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide#using-azure-active-directory-to-enable-trials).  Por exemplo: `https://contoso.com/trial/saas-app`. As avaliações gratuitas de listagem de ofertas são criadas, gerenciadas e configuradas pelo seu serviço e não têm assinaturas gerenciadas pela Microsoft.

> [!NOTE]
> Os tokens que seu aplicativo receberá por meio do link de avaliação só podem ser usados para obter informações do usuário por meio do Azure AD para automatizar a criação de contas em seu aplicativo. As contas da Microsoft (MSA) não têm suporte para autenticação usando esse token.

##### <a name="contact-me"></a>Contate-me
Colete informações de contato do cliente conectando seu sistema de gerenciamento de relacionamento com o cliente (CRM). O cliente será solicitado a fornecer permissão para compartilhar suas informações. Esses detalhes do cliente, juntamente com o nome da oferta, a ID e a origem do Marketplace onde encontraram sua oferta, serão enviados para o sistema CRM que você configurou. Para obter mais informações sobre como configurar seu CRM, consulte [Connect Lead Management](#connect-lead-management). 

## <a name="example-marketplace-offer-listing"></a>Exemplo de listagem de oferta do Marketplace

![Exemplo de listagem de oferta do Marketplace com observações](./media/marketplace-offer.svg)

## <a name="enable-a-test-drive"></a>Habilitar um test drive

Uma test drive é uma ótima maneira de demonstrar sua oferta a clientes potenciais, fornecendo a eles a opção "tentar antes de comprar", resultando em uma maior conversão e na geração de clientes potenciais altamente qualificados. [Saiba mais sobre test drives.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

- **Habilitar um test drive** (caixa de seleção) 

Ao habilitar o test drive, você será solicitado a configurar um ambiente de demonstração para os clientes experimentarem sua oferta por um período de tempo fixo. 

### <a name="type-of-test-drive"></a>Tipo de test drive

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)** : um modelo de implantação que contém todos os recursos do Azure que compõem sua solução. Os produtos que se ajustam a esse cenário usam apenas os recursos do Azure.
- **[Dynamics 365 for Business central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)** : a Microsoft hospeda e mantém o serviço de Test Drive (incluindo provisionamento e implantação) para um sistema de planejamento de recursos empresariais do Business central (finanças, operações, Cadeia de fornecedores, CRM, etc.).  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)** : a Microsoft hospeda e mantém o serviço de Test Drive (incluindo provisionamento e implantação) para um sistema de envolvimento do cliente (vendas, serviço, serviço de projeto, serviço de campo, etc.).  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)** : a Microsoft hospeda e mantém o serviço de Test Drive (incluindo provisionamento e implantação) para um sistema de planejamento de recursos corporativos de finanças e operações (finanças, operações, manufatura, Cadeia de fornecedores, etc.). 
- **[Aplicativo lógico](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)** : um modelo de implantação que abrange todas as arquiteturas de solução complexas. Todos os produtos personalizados devem usar esse tipo de unidade de teste.
- **[Power bi](https://docs.microsoft.com/power-bi/service-template-apps-overview)** : um link inserido para um painel personalizado. Os produtos que desejam demonstrar um visual interativo Power BI devem usar esse tipo de unidade de teste. Aqui, basta fazer upload da URL do Power BI Embedded.

#### <a name="additional-test-drive-resources"></a>Recursos de test drive adicionais
- [Práticas recomendadas técnicas do Test Drive](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Práticas recomendadas de marketing do Test Drive](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Visão geral do Test Drive One pager](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Conectar gerenciamento de leads

[!INCLUDE [Connect lead management](./includes/connect-lead-management-a.md)]

#### <a name="additional-lead-management-resources"></a>Recursos adicionais de gerenciamento de leads
- [Perguntas frequentes sobre gerenciamento de Lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Erros comuns de configuração de Lead](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Visão geral do gerenciamento de leads um pager](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf)

Lembre-se de **salvar** antes de passar para a próxima seção!

## <a name="properties"></a>propriedades
A guia **Propriedades** solicita que você defina as categorias e os setores usados para agrupar sua oferta nos Marketplaces, os contratos legais que dão suporte à sua oferta e a versão do aplicativo. 

Selecione **salvar** depois de concluir esses campos. 

### <a name="category"></a>Categoria
Selecione no mínimo um (1) e no máximo três (3) categorias usadas para agrupar sua oferta nas áreas de pesquisa do Marketplace apropriadas. Descreva como sua oferta dá suporte a essas categorias na descrição da oferta. 

### <a name="industry"></a>Setor
Selecione até dois (2) setores usados para agrupar sua oferta nas áreas de pesquisa do Marketplace apropriadas. Se sua oferta não for específica para um setor, não selecione uma. Descreva como sua oferta dá suporte aos setores selecionados na descrição da oferta. 

### <a name="app-version"></a>Versão do aplicativo
Esse é um campo opcional usado no AppSource Marketplace para identificar o número de versão da sua oferta. 

### <a name="standard-contract"></a>Contrato Standard

- **Usar contrato padrão?**

Para simplificar o processo de aquisição para clientes e reduzir a complexidade legal para fornecedores de software, a Microsoft oferece um modelo de contrato padrão para ajudar a facilitar uma transação no Marketplace. 

Em vez de criar termos e condições personalizados, os editores do Azure Marketplace podem optar por oferecer seu software sob o contrato padrão, que os clientes precisam apenas examinaremos e aceitar uma vez. 

O contrato padrão pode ser encontrado aqui: https://go.microsoft.com/fwlink/?linkid=2041178.

#### <a name="terms-of-use"></a>Termos de uso

Se os termos de licença forem diferentes do contrato Standard, você poderá optar por inserir seus próprios termos legais de uso aqui. Você também pode inserir até 10.000 caracteres de texto neste campo. Se os termos de uso exigirem uma descrição mais longa, insira um link de URL único nesse campo, em que os termos de licença adicionais podem ser encontrados. Ele será exibido aos clientes como um link ativo.

Os clientes precisam aceitar esses termos antes de poderem testar o aplicativo. 

Lembre-se de **salvar** antes de passar para a próxima seção!

## <a name="offer-listing"></a>Listagem de ofertas

A guia listagem de ofertas exibe os idiomas (e os mercados) onde sua oferta está disponível, atualmente em inglês (Estados Unidos) é o único local disponível. Além disso, essa página exibe o status da listagem específica do idioma e a data/hora em que ela foi adicionada. Você precisará definir os detalhes do Marketplace (nome da oferta, descrição, termos de pesquisa, etc.) para cada idioma/mercado.

> [!NOTE]
> A oferta de conteúdo de listagem (como descrição da oferta, documentos, capturas de tela, termos de uso e política de privacidade) não precisa estar em inglês, desde que a descrição da oferta comece com a frase ", este aplicativo está disponível somente em [idioma diferente do inglês]". Também é aceitável fornecer uma URL de *Link útil* para oferecer conteúdo em um idioma diferente daquele usado no conteúdo de listagem da oferta.

### <a name="offer-listings"></a>Listagens de oferta

Forneça detalhes a serem exibidos no Marketplace, incluindo descrições de sua oferta e ativos de marketing.

- **Nome** (obrigatório): o nome definido aqui será exibido como o título da listagem de oferta no (s) Marketplace (es) que você escolheu. O nome é preenchido previamente com base na nova entrada de **oferta** anterior.  Isso pode ser marcado.  Isso não deve conter emojis (a menos que sejam a marca registrada e os símbolos de direitos autorais) e deve ser limitado a 50 caracteres.
- **Resumo** (obrigatório): forneça uma breve descrição da sua oferta a ser usada em resultados da pesquisa de listagem (s) do Marketplace. Até 100 caracteres de texto podem ser inseridos neste campo.
- **Descrição** (obrigatório): forneça uma descrição da sua oferta a ser exibida na visão geral das listagem (s) do Marketplace. Considere incluir uma proposta de valor, benefícios principais, qualquer associação de categoria ou do setor, oportunidades de compra no aplicativo, quaisquer divulgações necessárias e um link para saber mais.
Até 3.000 caracteres de texto podem ser inseridos neste campo. Para obter dicas adicionais, consulte o artigo [escrever uma excelente descrição do aplicativo](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description).
- **Pesquisar palavras-chave**: Insira até três palavras-chave de pesquisa que os clientes podem usar para localizar sua oferta no (s) Marketplace (es).
- **Instruções de introdução** (obrigatório): explique como configurar e começar a usar seu aplicativo para clientes potenciais.  Este guia de início rápido pode conter links para documentação online mais detalhada. Até 3.000 caracteres de texto podem ser inseridos neste campo. 

#### <a name="description"></a>**Descrição**

Este é um campo obrigatório. Itens a serem incluídos na descrição: 

* Descreva claramente a proposição de valor da sua oferta nas primeiras frases de sua descrição.  
* Tenha em mente que as primeiras frases podem ser exibidas nos resultados da pesquisa.  
* Não dependa de recursos e em funcionalidades para vender seu produto. Em vez disso, concentre-se no valor que você oferece.  
* Use o vocabulário específico do setor ou palavras com base no benefício tanto quanto possível. 

Os componentes principais da sua proposta de valor devem incluir informações sobre: 

* Descrição do produto. 
* Tipo de usuário que se beneficia do produto. 
* O cliente precisa ou problemático que o produto aborda. 

Para tornar sua descrição de oferta mais atraente, você pode usar marcas HTML para formatar a descrição. 

1. Se você quiser criar parágrafos, adicione `<p>` no implorando do texto e adicione `</p>` no final.

    **Exemplo**: 

    `<p>` Este é o primeiro parágrafo. `</p>` <br>
    `<p>` é o meu segundo parágrafo. `</p>` <br>

    O anterior ficaria assim:

    <p> Este é o primeiro parágrafo. </p>
    <p> Este é o meu segundo parágrafo. </p>

1. Se você quiser adicionar uma **lista de itens com marcadores**, coloque o texto dentro das marcas `<li>` abaixo. Você pode copiar e colar mais itens com marcadores (itens entre as marcas `<li>` e `</li>`) nas marcas `<ul>` e `</ul>`. Certifique-se de adicionar o `<ul></ul>`. 

    **Exemplo**:

    ```
    <ul> 
        <li>add text here</li> 
        <li> add text here </li> 
        <li> add text here </li> 
    </ul> 
    ```

    O anterior ficaria assim:
    <ul> 
        <li>Adicionar texto aqui</li> 
        <li> Adicionar texto aqui </li> 
        <li> Adicionar texto aqui </li> 
    </ul> 

1. Para conteúdo em **negrito** , adicione `<b>` no início do texto que você deseja negrito e adicione `</b>` no final do texto que você deseja colocar em negrito. 

    **Exemplo**: `<b>` avaliação gratuita `</b>`
    
    O anterior faria com que as palavras de avaliação gratuita fossem em negrito na descrição da oferta na vitrine. 

    **AVALIAÇÃO GRATUITA**

1. Para adicionar **quebras de linha** entre o conteúdo, adicione `<br>` antes do conteúdo que você deseja iniciar em uma nova linha. Se você quiser deixar um espaço e certificar-se de que o conteúdo começa em uma nova linha, adicione `<br><br>` antes do conteúdo. 

    **Exemplo**:

    Esta é uma linha de texto. `<br>` é uma linha de texto que será iniciada em uma nova linha. `<br><br>` é uma linha que iniciará duas linhas abaixo. 

    O anterior ficaria assim:

    Esta é uma linha de texto. <br> Esta é uma linha de texto que será iniciada em uma nova linha. <br><br> Esta é uma linha que iniciará duas linhas abaixo. 

1. Se você quiser **aumentar o tamanho do texto**, primeiro escolha a quantidade desejada para o texto. Use os exemplos a seguir. Depois de selecionar o tamanho do texto, adicione as marcações `<H*></H*>` correspondentes ao início e ao final do texto. 

    **Exemplo**:

    `<h1>`This é Heading 1 @ no__t-1 <br>
    `<h2>`This é heading 2 @ no__t-1 <br>
    `<h3>`This é heading 3 @ no__t-1 <br>
    `<h4>`This é heading 4 @ no__t-1 <br>
    `<h5>`This é título 5 @ no__t-1 <br>
    `<h6>`This é título 6 @ no__t-1 

    O anterior ficaria assim:

    ![Títulos de exemplo](./media/heading.png)

#### <a name="links"></a>Links

- **Política de privacidade** (obrigatória): link para a política de privacidade da sua organização. Você é responsável por garantir que seu aplicativo esteja em conformidade com as leis e regulamentos de privacidade e para fornecer uma política de privacidade válida
- **Materiais de marketing do programa CSP** (opcional): você deve fornecer um link para materiais de marketing se optar por estender sua oferta para o programa [CSP (provedor de soluções na nuvem)](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) . O CSP amplia sua oferta para uma variedade maior de clientes qualificados, permitindo que os parceiros do CSP agrupem, comercializam e revendam sua oferta. Esses revendedores precisarão de acesso aos materiais para marketing de sua oferta. Para obter mais informações, consulte [serviços de entrada no mercado](https://partner.microsoft.com/reach-customers/gtm).
- **Links úteis** (opcional): documentos online complementares opcionais sobre seu aplicativo ou serviços relacionados listados fornecendo um **título** e uma **URL**. Adicione links úteis adicionais clicando em **+ Adicionar uma URL**.

#### <a name="contact-information"></a>Informações de contato

- **Contatos**: para cada contato de cliente, forneça um **nome** de funcionário, **número de telefone**e endereço de **email** .  (Eles *não serão* exibidos publicamente). Uma **URL de suporte** também é necessária para o grupo de **contato de suporte** .  (Essas informações *serão* exibidas publicamente).

**Contato de suporte** (obrigatório): para perguntas de suporte geral.

**Contato de engenharia** (obrigatório): para perguntas técnicas.

**Contato do Gerenciador de canal** (obrigatório): para perguntas de revendedor relacionadas ao programa CSP.

#### <a name="files-and-images"></a>Arquivos e imagens

- **Documentos** (obrigatório): Adicione documentos de marketing relacionados para sua oferta, em formato PDF, fornecendo no mínimo um (1) e no máximo três (3) documentos por oferta.
- **Imagens** (opcional): há vários locais onde as imagens de logotipo da sua oferta podem aparecer em todo o Marketplace, exigindo os seguintes tamanhos--pequeno: 48 x 48 pixels _(obrigatório),_ médio: 90 x 90 pixels, grande: 216 x 216 pixels _( obrigatório),_ largo: 255 x 115 pixels e Hero: 815 x 290 pixels. Todas as imagens devem estar no. Formato PNG.
- **Capturas de tela** (obrigatórias): Adicionar capturas de tela que demonstram sua oferta. No máximo cinco (5) capturas de tela podem ser adicionadas e devem ser dimensionadas em 1280 x 720 pixels. Todas as imagens devem estar no. Formato PNG.
- **Vídeos** (opcional): adicionar links a vídeos demonstrando sua oferta. É possível usar links para vídeos do YouTube e/ou do Vimeo, que são mostrados juntamente com sua oferta aos clientes. Também será necessário inserir uma imagem em miniatura do vídeo, dimensionada para 1280 x 720 pixels no formato PNG. Você pode exibir um máximo de quatro vídeos por oferta.

Lembre-se de **salvar** antes de passar para a próxima seção!

#### <a name="additional-marketplace-listing-resources"></a>Recursos adicionais de listagem do Marketplace

- [Práticas recomendadas para listagens de ofertas do Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)


## <a name="preview"></a>Versão prévia

A guia **Visualização** permite que você defina um **público de visualização** limitado para liberar sua oferta antes de publicar sua oferta em tempo real para o público mais amplo do Marketplace.

> [!IMPORTANT]
> Você deve selecionar **entrar em ativação** antes que sua oferta seja publicada em tempo real para o público público do Marketplace depois de verificar sua oferta em versão prévia.

- **Definir um público de visualização: Adicione um único email de conta do AAD/MSA por linha, juntamente com uma descrição opcional.**

Adicione até dez (10) endereços de email manualmente ou vinte (20) se estiver carregando um arquivo CSV, para a conta da Microsoft existente (MSA) ou Azure Active Directory contas para ajudar a validar sua oferta antes de publicar em tempo real. Ao adicionar essas contas, você está definindo um público que terá permissão para visualizar o acesso à sua oferta antes que ele seja publicado no (s) Marketplace (es). Se sua oferta já estiver ativa, você ainda poderá definir um público de visualização para testar quaisquer alterações ou atualizações na sua oferta.

> [!NOTE]
> O público de visualização difere de um público privado. Um público de visualização tem permissão de acesso à sua oferta _antes_ de ser publicado em tempo real nos Marketplaces. Você também pode optar por criar um plano e torná-lo disponível somente para um público privado. Na guia **lista de planos** , você pode definir um público privado com a caixa de seleção **este é um plano privado** . Em seguida, você pode definir um público privado de até 20.000 clientes usando as IDs de locatário do Azure.

## <a name="technical-configuration"></a>Configuração técnica

A guia **configuração técnica** define os detalhes técnicos (caminho da URL, webhook, ID do locatário e ID do aplicativo) usados para se conectar à sua oferta. Essa conexão nos permite provisionar sua oferta para o cliente final se ele optar por adquiri-lo. Os diagramas que descrevem o uso dos campos coletados estão disponíveis na documentação para [APIs de preenchimento de SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2).

- **URL da página de aterrissagem** (obrigatório): defina a URL do site na qual os clientes serão acessados depois de adquirir sua oferta do Marketplace. Essa URL será o ponto de extremidade que recebe um token quando um cliente é roteado para a página. Esse token pode ser trocado para detalhes de provisionamento usando a resolução nas APIs de preenchimento. Esses detalhes e quaisquer outros que você coletar podem ser usados como parte de uma página da Web interativa pelo cliente, criada em sua experiência para concluir o registro e ativar sua compra.

- **Webhook de conexão** (obrigatório): para todos os eventos assíncronos que a Microsoft precisa enviar para você em nome do cliente (exemplo: a assinatura de SaaS deixou inválida), exigimos que você forneça um webhook de conexão. Se você ainda não tiver um sistema de webhook em vigor, a configuração mais simples é ter um aplicativo lógico de ponto de extremidade HTTP que escutará todos os eventos postados nele e, em seguida, tratá-los adequadamente (por exemplo, https: \//prod-1westus. Logic. Azure. com: 443/Work ). Para saber mais, confira [Chamar, disparar ou aninhar fluxos de trabalho com pontos de extremidade HTTP em aplicativos lógicos](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

- **ID de locatário do Azure ad** (obrigatório): dentro de portal do Azure, exigimos que você [crie um aplicativo de Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) para que possamos validar a conexão entre nossos dois serviços está por trás de uma comunicação autenticada. Para localizar a [ID do locatário](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in), vá para o Azure Active Directory e selecione **Propriedades**, em seguida, procure o número de **ID de diretório** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e).

- **ID do aplicativo do Azure ad** (obrigatório): você também precisa da [ID do aplicativo](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) e de uma chave de autenticação. Para obter esses valores, vá para o Azure Active Directory e selecione **registros de aplicativo**, em seguida, procure o número de **ID do aplicativo** listado (por exemplo, 50c464d3-4930-494c-963c-1e951d15360e). Para localizar a chave de autenticação, vá para **configurações** e selecione **chaves**. Você precisará fornecer uma descrição e uma duração e, em seguida, será fornecido um valor numérico.

 Observe que a ID do aplicativo do Azure está associada à sua ID do editor, portanto, certifique-se de que a mesma ID do aplicativo seja usada em todas as suas ofertas.

## <a name="plan-overview"></a>Visão geral do plano

A guia **visão geral do plano** permite que você forneça uma variedade de opções de plano na mesma oferta. Esses planos (às vezes chamados de SKUs) poderiam diferir em termos de versão, monetização ou camadas de serviço. Você deve configurar pelo menos um plano para vender sua oferta no Marketplace.

Depois de criado, você verá os nomes do plano, as IDs, os modelos de preços, a disponibilidade (pública ou privada), o status de publicação atual e as ações disponíveis.

As **ações** disponíveis na **visão geral do plano** variam de acordo com o status atual do seu plano e podem incluir:

- Se o status do plano for **rascunho** – excluir rascunho
- Se o status do plano for **Live** – parar vender plano ou sincronizar público privado

**Criar novo plano** (mínimo de um plano para aqueles que selecionam vender pela Microsoft)

- **ID do plano:** Crie uma ID de plano exclusiva para cada plano nesta oferta. Essa ID será visível para os clientes na URL do produto e nos modelos de Azure Resource Manager (se aplicável). Use somente letras minúsculas, caracteres alfanuméricos, traços ou sublinhados. São permitidos no máximo 50 caracteres para essa ID de plano. Observe que a ID não pode ser modificada depois de selecionar criar.
- **Nome do plano:** Os clientes verão esse nome ao decidir qual plano selecionar dentro de sua oferta. Crie um nome de oferta exclusivo para cada plano nesta oferta. O nome do plano é usado para diferenciar os planos de software que podem fazer parte da mesma oferta (por exemplo, Nome da oferta: Windows Server; planos: Windows Server 2016, Windows Server 2019).

### <a name="plan-listing"></a>Lista de planos

A guia **lista de planos** exibe os idiomas (e os mercados) em que o plano está disponível, atualmente em inglês (Estados Unidos) é o único local disponível. Além disso, essa página exibe o status da listagem específica do idioma e a data/hora em que ela foi adicionada. Você precisará definir os detalhes do Marketplace (nome da oferta, descrição, termos de pesquisa, etc.) para cada idioma/mercado.

#### <a name="plan-listing-details"></a>Detalhes da listagem de plano

A seleção de uma das linguagens do plano exibirá as informações de **listagem do plano** , incluindo o **nome** e a **Descrição.**

- **Nome**: preenchido previamente com base na entrada do **novo plano** de visualização e aparecerá como o título do "plano de software" da sua oferta exibido no Marketplace.
- **Descrição:** Essa descrição é uma oportunidade para explicar o que torna este plano de software exclusivo e quaisquer diferenças de outros planos de software dentro de sua oferta. Pode conter até 500 caracteres.

Selecione **salvar** depois de concluir esses campos.

#### <a name="plan-pricing-and-availability"></a>Planejar preços e disponibilidade

A guia **preços e disponibilidade** permite que você configure os mercados nos quais esse plano estará disponível, o modelo de monetização, o preço e o termo de cobrança desejados. Além disso, você pode indicar se deseja tornar o plano visível para todos ou apenas para clientes específicos (um público privado).

##### <a name="enabling-free-trials"></a>Habilitando avaliações gratuitas

As ofertas de SaaS por meio do Marketplace comercial permitem que você forneça uma avaliação gratuita de um mês ao vender pela Microsoft. Para todos os modelos de cobrança e termos, exceto planos medidos, há suporte para avaliações gratuitas. Essa opção permite que os clientes tenham uma barreira baixa para entrada por meio de um mês de acesso gratuito.  Se você optar por habilitar uma avaliação gratuita para os planos em sua oferta, o cliente não poderá converter para uma assinatura paga antes do final do período de um mês inicial.  Durante esse tempo, os clientes que comprarem sua oferta poderão experimentar qualquer um dos planos com suporte que tenham a avaliação gratuita habilitada e convertê-los.  A conversão para uma assinatura paga é feita automaticamente no final do prazo.

>[!Note]
>Se o cliente optar por converter em um plano sem avaliações gratuitas, a conversão ocorrerá, mas a avaliação gratuita será perdida imediatamente.  Além disso, quando um cliente começa a pagar por um plano, ele não pode mais obter uma avaliação gratuita na mesma assinatura novamente, mesmo que eles convertam em uma SKU que ofereça suporte a avaliações gratuitas.

A capacidade de configurar uma avaliação gratuita está disponível para cada plano em sua oferta. Basta navegar até os preços e a disponibilidade de cada oferta e marcar a caixa para permitir uma avaliação de um mês.

![Caixa de seleção de avaliação gratuita de um mês](./media/free-trial-enable.png)

>[!Note]
>Depois que sua oferta de transação for publicada com uma avaliação gratuita, ela não poderá ser desabilitada para esse plano. Verifique se essa configuração está correta para a primeira publicação para evitar ter que recriar o plano.

Para obter informações sobre assinaturas de clientes que participam atualmente de uma avaliação gratuita, use a nova propriedade de API `isFreeTrial`, que será marcada como true ou false. Consulte a [API obter assinatura de SaaS](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2#get-subscription) para obter mais informações.

>[!Note]
>Não há suporte para avaliações gratuitas para planos que aproveitam o serviço de medição do Marketplace.

#### <a name="markets"></a>Mercados

- **Editar mercados** (opcional)

Cada plano deve estar disponível em pelo menos um mercado. Marque a caixa de seleção de qualquer local de mercado onde você gostaria de disponibilizar esse plano. Uma caixa de pesquisa e um botão para selecionar os países "impostos remetidos", no qual a Microsoft remete as vendas e o imposto sobre o uso em seu nome, estão incluídas para ajudar. 


Se você já tiver definido preços para seu plano em dólares de Estados Unidos (USD) e adicionar outro local de mercado, o preço do novo mercado será calculado de acordo com as tarifas de câmbio atuais. Você sempre deve examinar o preço de cada mercado antes da publicação. Os preços podem ser revisados usando o link "exportar preços (xlsx)" depois de salvar as alterações.

#### <a name="pricing"></a>Preços

- **Modelo de preços**: taxa simples ou baseada em assentos

**Taxa fixa:** Habilite o acesso à sua oferta com um preço de taxa simples mensal ou anual. Isso às vezes é chamado de preços baseados em site. Com esse modelo de preços, opcionalmente, você pode definir planos medidos que usam a API do serviço de medição do Marketplace para cobrar clientes de acordo com unidades não padrão.  Para obter mais informações sobre cobrança limitada, consulte [cobrança limitada usando o serviço de medição do Marketplace](./saas-metered-billing.md).

**Por usuário:** Habilite o acesso à sua oferta com o preço com base no número de usuários que estão acessando a oferta ou ocupando estações. Esse modelo baseado no usuário permite que você defina o número mínimo e máximo de usuários permitidos com base no preço. Dessa forma, diferentes pontos de preço podem ser configurados com base no número de usuários Configurando vários planos.  Esses campos são opcionais. Se deixado desmarcado, o número de usuários será interpretado como não tendo um limite (mínimo de 1 e máximo de quantas o sistema pode oferecer suporte). Esses campos podem ser editados como parte de uma atualização para seu plano.

Depois de publicado, a opção de modelo de preços de cobrança não pode ser alterada. Além disso, todos os planos para a mesma oferta devem compartilhar o mesmo modelo de preços.

- **Termo de cobrança**: mensal ou anual

Selecione a frequência com que os clientes devem pagar o preço listado. Pelo menos um preço mensal ou anual deve ser fornecido ou ambas as opções podem ser disponibilizadas para os clientes.

- **Preço**: USD por mês ou USD por ano

Os preços definidos na moeda local (USD = Estados Unidos dólar) são convertidos na moeda local de todos os mercados selecionados usando as tarifas de câmbio atuais disponíveis durante a instalação. Valide esses preços antes de publicar exportando a planilha de preços e revisando o preço em cada mercado. Se você quiser definir preços personalizados em um mercado individual, modifique e importe a planilha de preços. Você é responsável por validar esse preço e possuir essas configurações.
*\*Você deve primeiro salvar as alterações de preços para habilitar a exportação de dados de preços.*

Examine seus preços cuidadosamente antes de publicar, pois há algumas restrições sobre o que pode ser alterado depois que um plano é publicado:

- Depois que um plano é publicado, o modelo de preços não pode ser alterado.
- Depois que um termo de cobrança for publicado para um plano, ele não poderá ser removido mais tarde.
- Depois que o preço de um mercado em seu plano for publicado, ele não poderá ser alterado posteriormente.

### <a name="plan-audience"></a>Planejar público

Você tem a opção de configurar cada plano para ser visível para todos ou apenas para um público específico de sua escolha. Você pode atribuir associação a esse público restrito usando as IDs de locatário do Azure AD.

#### <a name="privacy"></a>Privacidade

- **Este é um plano privado** (caixa de seleção opcional)

Marque esta caixa para tornar seu plano privado e visível somente para o público restrito de sua escolha. Depois de publicado como um plano privado, você pode atualizar o público ou optar por disponibilizar o plano para todos. Depois que um plano é publicado como visível para todos, ele deve permanecer visível para todos. (O plano não pode ser configurado como um plano privado novamente).

- **Público-alvo restrito (IDs de locatário)**

Atribua o público que terá acesso a este plano privado. O acesso é atribuído usando IDs de locatário com a opção de incluir uma descrição de cada ID de locatário atribuída. Um máximo de 10 IDs de locatário pode ser adicionado ou 20.000 clientes IDs de locatário se importar um arquivo de planilha. csv.

Um locatário é uma representação de uma organização, com uma ID representada como um GUID (identificador global exclusivo, um número inteiro de 128 bits usado para identificar recursos). É uma instância dedicada do Azure AD que uma organização ou um desenvolvedor de aplicativos recebe ao criar uma relação com Microsoft, como inscrever-se no Azure, no Microsoft Intune ou no Microsoft 365. Cada locatário do AD do Azure é distinto e separado de outros diretórios do AD do Azure. Para verificar o locatário, entre no portal do Azure com a conta que você deseja usar para gerenciar seu aplicativo. Se houver um locatário, você será conectado a ele automaticamente e verá o nome do locatário diretamente abaixo do nome da conta. Passe o mouse sobre o nome da conta no canto superior direito do portal do Azure para ver seu nome, email, diretório e ID do locatário (um GUID) e seu domínio. Se sua conta estiver associada a vários locatários, você pode selecionar o nome da sua conta para abrir um menu no qual você pode alternar entre locatários. Cada locatário tem sua própria ID exclusiva. Você também pode pesquisar a ID de locatário da sua organização usando uma URL de nome de domínio em: [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

Embora o SaaS ofereça usar IDs de locatário para definir um público privado, outros tipos de oferta podem usar IDs de assinatura do Azure (que também são representadas como GUIDs).

> [!NOTE]
> O público privado (ou público restrito) difere de um público de visualização. Na guia **[Visualização](#preview)** , você pode definir um público de visualização. Um público de visualização tem permissão de acesso à sua oferta *antes* que a oferta seja publicada ao vivo no Marketplace. Embora a designação de público privado se aplique apenas a um plano específico, o público-alvo da visualização pode exibir todos os planos (privado ou não), mas somente durante o período de visualização limitado, enquanto o plano é testado e validado.

## <a name="example-list-of-plans-within-a-marketplace-offer"></a>Exemplo de lista de planos em uma oferta do Marketplace

![Exemplo de listagem de planos do Marketplace com observações](./media/marketplace-plan.svg)

## <a name="test-drive"></a>Test drive

[!INCLUDE [Test drive content](./includes/commercial-marketplace-test-drive.md)]

## <a name="publish"></a>Publicar

#### <a name="submit-offer-to-preview"></a>Enviar oferta para visualização

Depois de concluir todas as seções necessárias da oferta, selecione **publicar** no canto superior direito do Portal. Você será redirecionado para a página **revisar e publicar** . 

Se esta for a primeira vez que você publica essa oferta, você pode:

- Consulte o status de conclusão de cada seção da oferta.
    - *Não iniciado* – significa que a seção não foi tocada e precisa ser concluída.
    - *Incompleto* – significa que a seção tem erros que precisam ser corrigidos ou que requer mais informações a serem fornecidas. Volte para a (s) seção (ões) e atualize-a.
    - *Concluído* – significa que a seção está concluída, todos os dados necessários foram fornecidos e não há erros. Todas as seções da oferta devem estar em um estado completo antes que você possa enviar a oferta.
- Forneça instruções de teste à equipe de certificação para garantir que seu aplicativo seja testado corretamente, além de qualquer nota suplementar útil para entender seu aplicativo.
- Envie a oferta para publicação selecionando **Enviar**. Enviaremos um email para que você saiba quando uma versão prévia da oferta está disponível para revisão e aprovação. Você deve retornar ao Partner Center e selecionar **Go-Live** para a oferta para publicar sua oferta no público (ou se uma oferta privada, para o público privado).

## <a name="next-steps"></a>Próximos passos

- [Atualizar uma oferta existente no Marketplace comercial](./update-existing-offer.md)
