---
title: Acompanhamento de dependência no Aplicativo Azure insights com Python OpenCensus | Microsoft Docs
description: Monitore chamadas de dependência para seus aplicativos Python via OpenCensus Python.
services: application-insights
author: lzchen
manager: carmonm
ms.assetid: d15c4ca8-4c1a-47ab-a03d-c322b4bb2a9e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: lechen
ms.openlocfilehash: 4d2e1fe5a3f337c2a6c96b556f3effe0ad9748c7
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72559496"
---
# <a name="track-dependencies-with-opencensus-python"></a>Acompanhar dependências com Python OpenCensus

Uma dependência é um componente externo que é chamado pelo seu aplicativo. Os dados de dependência são coletados usando o OpenCensus Python e suas várias integrações. Os dados são enviados para Application Insights em Azure Monitor.

Primeiro, instrumentar seu aplicativo Python com o [SDK do Python OpenCensus](../../azure-monitor/app/opencensus-python.md)mais recente.

## <a name="in-process-dependencies"></a>Dependências em processo

O SDK do OpenCensus Python para Azure Monitor permite que você envie telemetria de dependências "em processo" (informações e lógica que ocorre dentro de seu aplicativo). As dependências em processo terão o campo `type` como `INPROC` no Analytics.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>Dependências com a integração de "solicitações"

Acompanhe suas solicitações de saída com a integração do OpenCensus `requests`.

Baixe e instale o `opencensus-ext-requests` do [PyPI](https://pypi.org/project/opencensus-ext-requests/) e adicione-o às integrações de rastreamento. As solicitações enviadas usando a biblioteca de [solicitações](https://pypi.org/project/requests/) do Python serão acompanhadas.

```python
import requests
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])  # <-- this line enables the requests integration

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='parent'):
    response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit') # <-- this request will be tracked
```

## <a name="dependencies-with-httplib-integration"></a>Dependências com a integração "httplib"

Acompanhe suas solicitações de saída com a integração do OpenCensus `httplib`.

Baixe e instale o `opencensus-ext-httplib` do [PyPI](https://pypi.org/project/opencensus-ext-httplib/) e adicione-o às integrações de rastreamento. As solicitações enviadas usando [http. Client](https://docs.python.org/3.7/library/http.client.html) para Python3 ou [httplib](https://docs.python.org/2/library/httplib.html) para Python2 serão rastreadas.

```python
import http.client as httplib
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['httplib'])
conn = httplib.HTTPConnection("www.python.org")

tracer = Tracer(
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(1.0)
)

conn.request("GET", "http://www.python.org", "", {})
response = conn.getresponse()
conn.close()
```

## <a name="dependencies-with-django-integration"></a>Dependências com a integração "Django"

Acompanhe suas solicitações de Django de saída com a integração do OpenCensus `django`.

Baixe e instale `opencensus-ext-django` de [PyPI](https://pypi.org/project/opencensus-ext-django/) e adicione a seguinte linha à seção `MIDDLEWARE` no arquivo Django `settings.py`.

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

É possível fornecer configuração adicional, ler [personalizações](https://github.com/census-instrumentation/opencensus-python#customization) para uma referência completa.

```python
OPENCENSUS = {
    'TRACE': {
        'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
        'EXPORTER': '''opencensus.ext.ocagent.trace_exporter.TraceExporter(
            service_name='foobar',
        )''',
    }
}
```

## <a name="dependencies-with-mysql-integration"></a>Dependências com a integração "MySQL"

Acompanhe as dependências do MYSQL com a integração de `mysql` do OpenCensus. Essa integração dá suporte à biblioteca [MySQL-Connector](https://pypi.org/project/mysql-connector-python/) .

Baixe e instale `opencensus-ext-mysql` do [PyPI](https://pypi.org/project/opencensus-ext-mysql/) e adicione as linhas a seguir ao seu código.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>Dependências com a integração "pymysql"

Acompanhe suas dependências do PyMySQL com a integração do OpenCensus `pymysql`.

Baixe e instale `opencensus-ext-pymysql` do [PyPI](https://pypi.org/project/opencensus-ext-pymysql/) e adicione as linhas a seguir ao seu código.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>Dependências com a integração "PostgreSQL"

Acompanhe suas dependências do PostgreSQL com a integração de `postgresql` do OpenCensus. Essa integração dá suporte à biblioteca [psycopg2](https://pypi.org/project/psycopg2/) .

Baixe e instale `opencensus-ext-postgresql` do [PyPI](https://pypi.org/project/opencensus-ext-postgresql/) e adicione as linhas a seguir ao seu código.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>Dependências com a integração "pymongo"

Acompanhe suas dependências do MongoDB com a integração de `pymongo` do OpenCensus. Essa integração dá suporte à biblioteca [pymongo](https://pypi.org/project/pymongo/) .

Baixe e instale `opencensus-ext-pymongo` do [PyPI](https://pypi.org/project/opencensus-ext-pymongo/) e adicione as linhas a seguir ao seu código.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>Dependências com a integração "SQLAlchemy"

Acompanhe suas dependências usando o SQLAlchemy usando a integração do OpenCensus `sqlalchemy`. Essa integração acompanha o uso do pacote [SQLAlchemy](https://pypi.org/project/SQLAlchemy/) , independentemente do banco de dados subjacente.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>Próximos passos

* [Mapa do aplicativo](../../azure-monitor/app/app-map.md)
* [Disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)
* [Pesquisar](../../azure-monitor/app/diagnostic-search.md)
* [Consulta de log (Analytics)](../../azure-monitor/log-query/log-query-overview.md)
* [Diagnóstico de transação](../../azure-monitor/app/transaction-diagnostics.md)