---
title: Análise de Desempenho de Consultas no banco de dados do Azure para MySQL
description: Este artigo descreve o recurso Análise de Desempenho de Consultas no banco de dados do Azure para MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: b7395a8ab71e860c2e584339dcd581077a4f4020
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595470"
---
# <a name="query-performance-insight-in-azure-database-for-mysql"></a>Análise de Desempenho de Consultas no banco de dados do Azure para MySQL

**Aplica-se a:** Banco de dados do Azure para MySQL 5,7

> [!IMPORTANT]
> Análise de Desempenho de Consultas está em versão prévia.

Análise de Desempenho de Consultas ajuda a identificar rapidamente o que são suas consultas em execução mais longas, como elas mudam com o passar do tempo e quais esperas estão afetando-as.

## <a name="common-scenarios"></a>Cenários comuns

### <a name="long-running-queries"></a>Consultas de longa execução

- Identificando consultas de execução mais longa nas últimas X horas
- Identificando as N principais consultas que estão aguardando recursos
 
### <a name="wait-statistics"></a>Estatísticas de espera

- Entendendo a natureza de espera de uma consulta
- Noções básicas sobre tendências de espera de recursos e onde existe contenção de recursos

## <a name="permissions"></a>Permissões

Permissões de **proprietário** ou **colaborador** necessárias para exibir o texto das consultas no análise de desempenho de consultas. O **leitor** pode exibir gráficos e tabelas, mas não texto de consulta.

## <a name="prerequisites"></a>Pré-requisitos

Para que Análise de Desempenho de Consultas funcionem, os dados devem existir no [repositório de consultas](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Exibindo informações de desempenho

A exibição de [análise de desempenho de consultas](concepts-query-performance-insight.md) no portal do Azure orientará as visualizações de informações de chave de repositório de consultas.

Na página do portal do seu banco de dados do Azure para servidor MySQL, selecione **análise de desempenho de consultas** na seção **desempenho inteligente** da barra de menus.

### <a name="long-running-queries"></a>Consultas de longa execução

A guia **consultas de longa execução** mostra as 5 principais consultas por duração média por execução, agregadas em intervalos de 15 minutos. Você pode exibir mais consultas selecionando na lista suspensa **número de consultas** . As cores do gráfico podem ser alteradas para uma ID de consulta específica quando você fizer isso.

Você pode clicar e arrastar o gráfico para restringir a uma janela de tempo específica. Como alternativa, use os ícones ampliar e reduzir para exibir um período de tempo menor ou maior, respectivamente.

![Análise de Desempenho de Consultas consultas de longa execução](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png) 

### <a name="wait-statistics"></a>Estatísticas de espera

> [!NOTE]
> Estatísticas de espera são destinadas a solucionar problemas de desempenho de consulta. É recomendável que seja ativado apenas para fins de solução de problemas. <br>Se você receber a mensagem de erro no portal do Azure "*o problema encontrado para ' Microsoft. DBforMySQL '; Não é possível atender à solicitação. Se esse problema continuar ou for inesperado, entre em contato com o suporte com essas informações.* " ao exibir estatísticas de espera, use um período de tempo menor.

Estatísticas de espera fornece uma exibição dos eventos de espera que ocorrem durante a execução de uma consulta específica. Saiba mais sobre os tipos de evento de espera na [documentação do mecanismo MySQL](https://go.microsoft.com/fwlink/?linkid=2098206).

Selecione a guia **Estatísticas de espera** para exibir as visualizações correspondentes em esperas no servidor.

As consultas exibidas na exibição Estatísticas de espera são agrupadas pelas consultas que exibem as maiores esperas durante o intervalo de tempo especificado.

![Estatísticas de esperas de Análise de Desempenho de Consultas](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [monitoramento e ajuste](concepts-monitoring.md) no banco de dados do Azure para MySQL.