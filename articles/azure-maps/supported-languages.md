---
title: Suporte à localização no Azure Maps | Microsoft Docs
description: Saiba mais sobre idiomas com suporte para os serviços nos Mapas do Azure
author: walsehgal
ms.author: v-musehg
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b3382cffdc41685f8329a640aaf6c6c526375a83
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299832"
---
# <a name="localization-support-in-azure-maps"></a>Suporte à localização no Azure Maps

O Azure Maps dá suporte a várias linguagens e exibições baseadas em país/região. Este artigo fornece os idiomas e as exibições com suporte para ajudar a orientar sua implementação do Azure Maps.


## <a name="azure-maps-supported-languages"></a>Idiomas com suporte nos Mapas do Azure

Os Mapas do Azure foram traduzidos para variedade de idiomas nos serviços. A tabela a seguir fornece os códigos de idioma com suporte para cada serviço.  
  

| id         | NOME                   |  Mapas | Search | Roteamento | Incidentes de tráfego | Controle de mapa JS | Fuso horário |
|------------|------------------------|:-----:|:------:|:-------:|:-----------------:|:--------------:|:---------:|
| af-ZA      | Africâner              |       |    ✓   |    ✓    |                   |                |     ✓     |
| ar-SA      | Árabe                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| eu-ES      | Basco                 |       |    ✓   |         |                   |                |     ✓     |
| bg-BG      | Búlgaro              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| ca-ES      | Catalão                |       |    ✓   |         |                   |                |     ✓     |
| zh-HanS    | Chinês (Simplificado)   |       |  zh-CN |         |                   |                |     ✓     |
| zh-HanT    | Chinês (Tradicional)  | zh-TW |  zh-TW |  zh-TW  |                   |      Zh-TW     |     ✓     |
| hr-HR      | Croata               |       |    ✓   |         |                   |                |     ✓     |
| cs-CZ      | Tcheco                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| da-DK      | Dinamarquês                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| nl-NL      | Holandês                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| nl-BE      | Holandês (Bélgica)        |       |    ✓   |         |                   |                |     ✓     |
| en-AU      | Inglês (Austrália)    |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-NZ      | Inglês (Nova Zelândia)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-GB      | Inglês (Grã-Bretanha) |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| en-US      | Inglês (EUA)          |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| et-EE      | Estoniano               |       |    ✓   |         |         ✓         |                |     ✓     |
| fi-FI      | Finlandês                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr-FR      | Francês                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| fr-CA      | Francês (canadense)      |       |    ✓   |         |                   |                |     ✓     |
| gl-ES      | Galego               |       |    ✓   |         |                   |                |     ✓     |
| de-DE      | Alemão                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| el-GR      | Grego                  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| he-IL      | Hebraico                 |       |    ✓   |         |         ✓         |                |     ✓     |
| hi-IN      | Híndi                  |       |        |         |                   |                |     ✓     |
| hu-HU      | Húngaro              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| id-ID      | Indonésio             |   ✓   |    ✓    |    ✓    |         ✓         |        ✓       |     ✓     |
| it-IT      | Italiano                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ja-JP      | Japonês               |       |        |         |                   |                |     ✓     |
| kk-KZ      | Cazaque                 |       |    ✓   |         |                   |                |     ✓     |
| ko-KR      | Coreano                 |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| es-419     | Espanhol (América Latina) |       |    ✓   |         |                   |                |     ✓     |
| lv-LV      | Letão                |       |    ✓   |         |         ✓         |                |     ✓     |
| lt-LT      | Lituano             |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ms-MY      | Malaio (latino)          |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| nb-NO      | Norueguês bokmål       |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| NGT        | Neutral Ground Truth - idiomas oficiais para todas as regiões em scripts locais, se disponível |   ✓     |        |         |                   |      ✓          |         |
| NGT-Latn   | Neutro verdade - exônimos latinos. Script latino será usado se disponível |   ✓     |        |         |                   |        ✓         |          |
| pl-PL      | Polonês                 |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| pt-BR      | Português (Brasil)    |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| pt-PT      | Português (Portugal)  |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| ro-RO      | Romeno               |       |    ✓    |         |         ✓         |                |     ✓     |
| ru-RU      | Russo                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| sr-Cyrl-RS | Sérvio (cirílico)     |       |    Sérvio (cirílico) (Sr-RS)   |         |                   |                |     ✓     |
| sr-Latn-RS | Sérvio (latino)        |       |        |         |                   |                |     ✓     |
| sk-SK      | Eslovaco              |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| sl-SL      | Esloveno              |   ✓   |    ✓   |    ✓    |                   |        ✓       |     ✓     |
| es-ES      | Espanhol                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| es-MX      | Espanhol (México)       |   ✓   |        |    ✓    |                   |        ✓       |     ✓     |
| sv -SE     | Sueco                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| th-TH      | Tailandês                   |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| tr-TR      | Turco                |   ✓   |    ✓   |    ✓    |         ✓         |        ✓       |     ✓     |
| uk-UA      | Ucraniano               |       |    ✓   |         |                   |                |     ✓     |
| vi-VN      | Vietnamita             |       |    ✓   |         |                   |                |     ✓     |


## <a name="azure-maps-supported-views"></a>Exibições com suporte do Azure Maps

> [!Note]
> O Azure Maps foi lançado nos seguintes países/regiões em 1º de agosto de 2019:
>  * Argentina
>  * Índia
>  * Marrocos
>  * Paquistão
>
> Após 1º de agosto de 2019, a configuração de parâmetros de **exibição** definirá o conteúdo do mapa retornado para as novas regiões/países listados acima. Incentivamos você a garantir que tenha configurado o parâmetro de exibição conforme necessário para as APIs REST e SDKs que seus serviços estão usando.
>  
>
>  **APIs REST:**
>  
>  Verifique se você configurou o parâmetro de exibição conforme necessário. O parâmetro View especifica qual conjunto de conteúdo de litígio geopolíticos é retornado por meio dos serviços do Azure Maps. 
>
>  Serviços REST do Azure Maps afetados:
>    
>    * Obter bloco de mapa
>    * Obter imagem do mapa 
>    * Obter difusa de pesquisa
>    * Obter POI de pesquisa
>    * Obter categoria de POI de pesquisa
>    * Obter pesquisa próxima
>    * Obter endereço de pesquisa
>    * Obter o endereço de pesquisa estruturado
>    * Obter reversão de endereço de pesquisa
>    * Obter endereço de cruz inverso da pesquisa
>    * Postar pesquisa dentro da geometria
>    * Versão prévia do lote de endereços de pesquisa
>    * Pós pesquisa endereço versão prévia do lote reverso
>    * Postar pesquisa ao longo do roteiro
>    * Visualização do lote difusa da pesquisa posterior
>
>    
>  **SDK**
>
>  Verifique se você configurou o parâmetro de exibição conforme necessário e se tem a versão mais recente do SDK Web e SDK do Android. SDKs afetados:
>
>    * SDK da Web do Azure Mapas
>    * SDK do Android do Azure Mapas


O parâmetro de **exibição** do Azure Maps (também conhecido como "parâmetro de região do usuário") é um código de país ISO-3166 de duas letras que mostrará os mapas corretos para esse país/região especificando qual conjunto de conteúdo georelacionado geopolíticos é retornado por meio de mapas do Azure serviços, incluindo bordas e rótulos exibidos no mapa. 

Por padrão, o parâmetro de exibição é definido como **unificado**, mesmo que você não o tenha definido na solicitação. É sua responsabilidade determinar o local dos usuários e, em seguida, definir o parâmetro de exibição corretamente para esse local. Como alternativa, você tem a opção de definir ' View = auto ', que retornará os dados do mapa com base no endereço IP da solicitação.  O parâmetro View no Azure Maps deve ser usado em conformidade com as leis aplicáveis, incluindo aquelas em relação ao mapeamento do país em que mapas, imagens e outros dados e conteúdo de terceiros que você está autorizado a acessar por meio do mapas do Azure são disponibilizados.


A tabela a seguir fornece exibições com suporte.

| Visualizar         | DESCRIÇÃO                            |  Mapas | Search | JS Controle de Mapeamento |
|--------------|----------------------------------------|:-----:|:------:|:--------------:|
| AE           | Emirados Árabes Unidos (modo de exibição árabe)    |   ✓   |        |     ✓          |
| AR           | Argentina (exibição de Argentinian)           |   ✓   |    ✓   |     ✓          |
| BH           | Bahrein (exibição em árabe)                 |   ✓   |        |     ✓          |
| IN           | Índia (exibição indiana)                    |   ✓   |   ✓     |     ✓          |
| IQ           | Iraque (exibição em árabe)                    |   ✓   |        |     ✓          |
| JO           | Jordânia (exibição em árabe)                  |   ✓   |        |     ✓          |
| KW           | Kuwait (exibição em árabe)                  |   ✓   |        |     ✓          |
| LB           | Líbano (exibição em árabe)                 |   ✓   |        |     ✓          |
| MA           | Marrocos (exibição de Tamazight)                |   ✓   |   ✓     |     ✓          |
| OM           | Omã (exibição em árabe)                    |   ✓   |        |     ✓          |
| PK           | Paquistão (exibição Rúpia)              |   ✓   |    ✓    |     ✓          |
| PS           | Autoridade Palestina (exibição em árabe)    |   ✓   |        |     ✓          |
| QA           | Catar (exibição em árabe)                   |   ✓   |        |     ✓          |
| SA           | Arábia Saudita (exibição em árabe)            |   ✓   |        |     ✓          |
| SY           | Síria (exibição em árabe)                   |   ✓   |        |     ✓          |
| YE           | Iêmen (exibição em árabe)                   |   ✓   |        |     ✓          |
| Automático         | Retornar os dados do mapa com base no endereço IP da solicitação.|   ✓   |    ✓   |     ✓          |
| Unificado      | Exibição unificada (outras)                  |   ✓   |   ✓     |     ✓          |
