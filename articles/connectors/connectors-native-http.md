---
title: Chamar pontos de extremidade HTTP e HTTPS-aplicativos lógicos do Azure
description: Enviar solicitações de saída para pontos de extremidade HTTP e HTTPS usando aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/05/2019
tags: connectors
ms.openlocfilehash: 4f585778808b133fe5c731ab6a3189594cbfc0f9
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72804221"
---
# <a name="send-outgoing-calls-to-http-or-https-endpoints-by-using-azure-logic-apps"></a>Enviar chamadas de saída para pontos de extremidade HTTP ou HTTPS usando aplicativos lógicos do Azure

Com os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e o gatilho ou ação http interno, você pode criar tarefas automatizadas e fluxos de trabalho que enviam regularmente solicitações para qualquer ponto de extremidade http ou HTTPS. Para receber e responder a chamadas HTTP ou HTTPS de entrada, use o [gatilho de solicitação interno ou a ação de resposta](../connectors/connectors-native-reqres.md).

Por exemplo, você pode monitorar o ponto de extremidade de serviço para seu site verificando esse ponto de extremidade em um agendamento especificado. Quando um evento específico acontece nesse ponto de extremidade, como seu site ficar inativo, o evento dispara o fluxo de trabalho do aplicativo lógico e executa as ações especificadas.

Para verificar ou *sondar* um ponto de extremidade em um agendamento regular, você pode usar o gatilho http como a primeira etapa no fluxo de trabalho. Em cada verificação, o gatilho envia uma chamada ou *solicitação* ao ponto de extremidade. A resposta do ponto de extremidade determina se o fluxo de trabalho do aplicativo lógico é executado. O gatilho passa todo o conteúdo da resposta para as ações no aplicativo lógico.

Você pode usar a ação HTTP como qualquer outra etapa no fluxo de trabalho para chamar o ponto de extremidade quando quiser. A resposta do ponto de extremidade determina como as ações restantes do fluxo de trabalho são executadas.

Com base na capacidade do ponto de extremidade de destino, o conector HTTP dá suporte às versões 1,0, 1,1 e 1,2 da TLS (Transport Layer Security). Os aplicativos lógicos negociam com o ponto de extremidade do usando a versão mais recente com suporte possível. Portanto, por exemplo, se o ponto de extremidade der suporte a 1,2, o conector usará a 1,2 primeiro. Caso contrário, o conector usará a próxima versão com suporte mais alta.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso você não tenha uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* A URL para o ponto de extremidade de destino que você deseja chamar

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md). Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md)?

* O aplicativo lógico do qual você deseja chamar o ponto de extremidade de destino. Para começar com o gatilho HTTP, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar a ação HTTP, inicie seu aplicativo lógico com qualquer gatilho desejado. Este exemplo usa o gatilho HTTP como a primeira etapa.

## <a name="add-an-http-trigger"></a>Adicionar um gatilho HTTP

Esse gatilho interno faz uma chamada HTTP para a URL especificada para um ponto de extremidade e retorna uma resposta.

1. Entre no [portal do Azure](https://portal.azure.com). Abra seu aplicativo lógico em branco no designer de aplicativo lógico.

1. Em **escolher uma ação**, na caixa de pesquisa, digite "http" como filtro. Na lista de **gatilhos** , selecione o gatilho **http** .

   ![Selecionar o gatilho HTTP](./media/connectors-native-http/select-http-trigger.png)

   Este exemplo renomeia o gatilho para "gatilho HTTP" para que a etapa tenha um nome mais descritivo. Além disso, o exemplo posteriormente adiciona uma ação HTTP e ambos os nomes devem ser exclusivos.

1. Forneça os valores para os [parâmetros de gatilho http](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger) que você deseja incluir na chamada para o ponto de extremidade de destino. Configure a recorrência para a frequência com que você deseja que o gatilho Verifique o ponto de extremidade de destino.

   Se você selecionar um tipo de autenticação diferente de **nenhum**, as configurações de autenticação diferem com base na sua seleção. Para obter mais informações, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

   ![Insira os parâmetros do gatilho HTTP](./media/connectors-native-http/http-trigger-parameters.png)

1. Para adicionar outros parâmetros disponíveis, abra a lista **Adicionar novo parâmetro** e selecione os parâmetros desejados.

1. Continue criando o fluxo de trabalho do aplicativo lógico com as ações que são executadas quando o gatilho é acionado.

1. Quando terminar, lembre-se de salvar seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

## <a name="add-an-http-action"></a>Adicionar uma ação HTTP

Essa ação interna faz uma chamada HTTP para a URL especificada para um ponto de extremidade e retorna uma resposta.

1. Entre no [portal do Azure](https://portal.azure.com). Abra seu aplicativo lógico no Logic App Designer.

   Este exemplo usa o gatilho HTTP como a primeira etapa.

1. Na etapa em que você deseja adicionar a ação HTTP, selecione **nova etapa**.

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. Selecione o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Em **escolher uma ação**, na caixa de pesquisa, digite "http" como filtro. Na lista **ações** , selecione a ação **http** .

   ![Selecionar a ação HTTP](./media/connectors-native-http/select-http-action.png)

   Este exemplo renomeia a ação como "ação HTTP" para que a etapa tenha um nome mais descritivo.

1. Forneça os valores para os [parâmetros de ação http](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action) que você deseja incluir na chamada para o ponto de extremidade de destino.

   Se você selecionar um tipo de autenticação diferente de **nenhum**, as configurações de autenticação diferem com base na sua seleção. Para obter mais informações, consulte [Adicionar autenticação a chamadas de saída](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

   ![Inserir os parâmetros da ação HTTP](./media/connectors-native-http/http-action-parameters.png)

1. Para adicionar outros parâmetros disponíveis, abra a lista **Adicionar novo parâmetro** e selecione os parâmetros desejados.

1. Quando terminar, lembre-se de salvar seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

## <a name="content-with-multipartform-data-type"></a>Conteúdo com tipo de dados de várias partes/formulário

Para manipular o conteúdo que tem `multipart/form-data` tipo em solicitações HTTP, você pode adicionar um objeto JSON que inclui os atributos `$content-type` e `$multipart` ao corpo da solicitação HTTP usando esse formato.

```json
"body": {
   "$content-type": "multipart/form-data",
   "$multipart": [
      {
         "body": "<output-from-trigger-or-previous-action>",
         "headers": {
            "Content-Disposition": "form-data; name=file; filename=<file-name>"
         }
      }
   ]
}
```

Por exemplo, suponha que você tenha um aplicativo lógico que envia uma solicitação HTTP POST para um arquivo do Excel para um site usando a API desse site, que dá suporte ao tipo de `multipart/form-data`. Veja como essa ação pode parecer:

![Dados de formulário com várias partes](./media/connectors-native-http/http-action-multipart.png)

Aqui está o mesmo exemplo que mostra a definição de JSON da ação HTTP na definição de fluxo de trabalho subjacente:

```json
{
   "HTTP_action": {
      "body": {
         "$content-type": "multipart/form-data",
         "$multipart": [
            {
               "body": "@trigger()",
               "headers": {
                  "Content-Disposition": "form-data; name=file; filename=myExcelFile.xlsx"
               }
            }
         ]
      },
      "method": "POST",
      "uri": "https://finance.contoso.com"
   },
   "runAfter": {},
   "type": "Http"
}
```

## <a name="connector-reference"></a>Referência de conector

Para obter mais informações sobre parâmetros de ação e gatilho, consulte estas seções:

* [Parâmetros de gatilho HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger)
* [Parâmetros de ação HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action)

### <a name="output-details"></a>Detalhes de saída

Aqui estão mais informações sobre as saídas de um gatilho ou ação HTTP, que retorna essas informações:

| Nome da propriedade | Type | Descrição |
|---------------|------|-------------|
| Cabeçalhos | objeto | Os cabeçalhos da solicitação |
| Corpo | objeto | Objeto JSON | O objeto com o conteúdo do corpo da solicitação |
| código de status | int | O código de status da solicitação |
|||

| Código de status | Descrição |
|-------------|-------------|
| 200 | OK |
| 202 | Aceita |
| 400 | Solicitação incorreta |
| 401 | Não autorizado |
| 403 | Proibido |
| 404 | Não encontrado |
| 500 | Erro interno do servidor. Ocorreu um erro desconhecido. |
|||

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
