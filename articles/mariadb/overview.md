---
title: Visão geral do Banco de Dados do Azure para o serviço de banco de dados relacional MariaDB
description: Saiba mais sobre o serviço do Banco de Dados do Azure para MariaDB, um serviço de banco de dados relacional na nuvem da Microsoft baseada no MySQL Community Edition.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: overview
ms.custom: mvc
ms.date: 08/13/2019
ms.openlocfilehash: 46ba972c489326157cbdea19a1363a30e18f72b8
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177972"
---
# <a name="what-is-azure-database-for-mariadb"></a>O que é o Banco de Dados do Azure para o MariaDB?

O Banco de Dados do Azure para MariaDB é um serviço de banco de dados relacional na nuvem da Microsoft. O Banco de Dados do Azure para MariaDB é baseado no mecanismo de banco de dados da [edição de comunidade do MariaDB](https://mariadb.org/download/) (disponível sob a licença GPLv2), versão 10.2 e 10.3.

Banco de dados do Azure para MariaDB oferece:

- Alta disponibilidade interna sem nenhum custo adicional.
- Desempenho previsível, com preços pré-pagos inclusivos.
- Escale conforme necessário em segundos.
- Seguro para proteger dados confidenciais em repouso e em movimento.
- Backups automáticos e restauração pontual por até 35 dias.
- Segurança e conformidade de nível empresarial.

Esses recursos não exigem quase nenhuma administração. Eles são fornecidos sem nenhum custo adicional. O Banco de Dados do Azure para MariaDB pode ajudar você a desenvolver rapidamente seu aplicativo e acelerar o tempo de colocação no mercado. Você não precisa alocar um tempo precioso e recursos para gerenciamento de infraestrutura e máquinas virtuais. Você também pode continuar a desenvolver seu aplicativo usando as ferramentas de software livre e a plataforma de sua escolha. Entregue com a velocidade e eficiência que sua empresa exige, tudo isso sem ter que aprender novas habilidades.

Para saber mais sobre os principais conceitos e recursos no Banco de Dados do Azure para MariaDB, incluindo desempenho, escalabilidade e gerenciabilidade, veja estes guias de início rápido:

- [Criar um servidor de Banco de Dados do Azure para MariaDB usando o portal do Azure](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Criar um servidor de Banco de Dados do Azure para MariaDB usando a CLI do Azure](quickstart-create-mariadb-server-database-using-azure-cli.md)

<!--
For a set of Azure CLI samples, see:
- [Azure CLI samples for Azure Database for MariaDB](sample-scripts-azure-cli.md) 
-->

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajustar o desempenho e a escala em segundos

O serviço Banco de Dados do Azure para MariaDB oferece várias camadas de serviço: Básico, Uso Geral e Otimizado para Memória. Cada camada oferece diferentes níveis de desempenho e recursos para dar suporte a cargas de trabalho de banco de dados leves e pesadas. Você pode criar seu primeiro aplicativo em um banco de dados pequeno por alguns dólares por mês e então ajustar a escala para atender às necessidades da sua solução. A escalabilidade dinâmica ajuda o seu banco de dados a responder de forma transparente a mudanças rápidas nos requisitos de recursos. Você paga apenas pelos recursos de que precisa, e somente quando precisa deles. Veja  [Tipos de preço](concepts-pricing-tiers.md) para obter detalhes.

## <a name="monitoring-and-alerting"></a>Monitoramento e alertas

Como você decide quando aumentar ou reduzir? É possível usar os recursos internos de alerta e monitoramento de desempenho do Banco de Dados do Azure para MariaDB, em conjunto com as classificações de desempenho baseadas nos vCores. Usando essas ferramentas, você pode avaliar rapidamente o efeito da expansão ou redução dos vCores com base nas suas necessidades de desempenho atuais ou previstas. Veja [Alertas](howto-alert-metric.md) para obter detalhes.

## <a name="keep-your-app-and-business-running"></a>Mantenha seus aplicativos e a continuidade dos negócios

O SLA de disponibilidade de 99,99%, líder no setor, do Azure é impulsionado por uma rede global de datacenters gerenciados pela Microsoft. A rede ajuda a manter seu aplicativo em execução 24 horas por dia, 7 dias por semana. Você irá beneficiar-se de segurança interna, tolerância a falhas e proteção de dados no Banco de Dados do Azure para MariaDB. Com o Banco de Dados do Azure para MariaDB, você pode usar a restauração point-in-time para recuperar um servidor para um estado anterior, em até 35 dias.

## <a name="secure-your-data"></a>Proteja seus dados

Os serviços de banco de dados do Azure têm uma tradição de segurança de dados que o Banco de Dados do Azure para MariaDB mantém. O Banco de Dados do Azure para MariaDB oferece recursos que limitam o acesso, protegem dados em repouso e em movimento e ajudam você a monitorar a atividade. Visite a [Central de Confiabilidade do Azure](https://www.microsoft.com/en-us/trustcenter/security) para obter informações sobre a segurança da plataforma do Azure.

O serviço Banco de Dados do Azure para MariaDB usa o módulo de criptografia validado por FIPS 140-2 para criptografia de armazenamento de dados em repouso. Os dados, incluindo backups, são criptografados no disco, com exceção dos arquivos temporários criados durante a execução de consultas. O serviço usa a criptografia AES de 256 bits incluída na criptografia de armazenamento do Azure. As chaves são gerenciadas pelo sistema. A criptografia de armazenamento está sempre ativada e não pode ser desabilitada.

Por padrão, o serviço de Banco de Dados do Azure para MariaDB é configurado para exigir [segurança de conexão SSL](./concepts-ssl-connection-security.md) para dados em movimento na rede. Impor conexões SSL entre seu servidor de banco de dados e os aplicativos cliente ajuda a proteger contra ataques de "intermediários" criptografando o fluxo de dados entre o servidor e seu aplicativo. Como opção, você pode desabilitar a exigência de SSL para conectar ao seu serviço de banco de dados, se seu aplicativo cliente não oferecer suporte à conectividade SSL.

## <a name="contacts"></a>Contatos

Para dúvidas ou sugestões que você possa ter sobre como trabalhar com o Banco de Dados do Azure para MariaDB, envie um email para a [Equipe do Banco de Dados do Azure MariaDB](mailto:AskAzureDBforMariaDB@service.microsoft.com) (não um alias para suporte técnico).

Use também os seguintes pontos de contato:
- Para entrar em contato com o suporte do Azure, [abra uma solicitação de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) no portal do Azure.
- Para corrigir um problema com sua conta, [abra uma solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) no portal do Azure.
- Para fornecer comentários ou solicitar novos recursos, crie uma entrada nos [Fóruns de comentários do Azure](https://feedback.azure.com/forums/915439-azure-database-for-mariadb).

## <a name="next-steps"></a>Próximas etapas

Agora que você leu a introdução ao Banco de Dados do Azure para MariaDB, você está pronto para:
- Consultar a página de [preços](https://azure.microsoft.com/pricing/details/mariadb/) para ver comparações de custo e calculadoras. 
- Comece com a [criação do seu primeiro servidor](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->
