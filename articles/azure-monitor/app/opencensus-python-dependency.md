---
title: OpenCensus Python ile Azure Application Insights bağımlılık Izleme | Microsoft Docs
description: OpenCensus Python aracılığıyla Python uygulamalarınızın bağımlılık çağrılarını izleyin.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: c28ef627e9ef65f9985a520c8c4f3facc7a160da
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73575509"
---
# <a name="track-dependencies-with-opencensus-python"></a>OpenCensus Python ile bağımlılıkları izleme

Bağımlılık, uygulamanız tarafından çağrılan bir dış bileşendir. Bağımlılık verileri, OpenCensus Python ve çeşitli tümleştirmeler kullanılarak toplanır. Veriler daha sonra Azure Izleyici altında `dependencies` telemetri olarak Application Insights gönderilir.

İlk olarak, Python uygulamanızı en son [Opencensus Python SDK 'sı](../../azure-monitor/app/opencensus-python.md)ile işaretleyin.

## <a name="in-process-dependencies"></a>İşlem içi bağımlılıklar

Azure Izleyici için OpenCensus Python SDK 'Sı, "işlem içi" bağımlılık telemetrisi (uygulamanızda gerçekleşen bilgi ve mantık) göndermenizi sağlar. İşlem içi bağımlılıklarda `type` alanı Analize `INPROC` olacak.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>"İstekler" tümleştirmesi olan bağımlılıklar

OpenCensus `requests` tümleştirmesiyle giden isteklerinizi izleyin.

[Pypı](https://pypi.org/project/opencensus-ext-requests/) 'den `opencensus-ext-requests` indirip yükleyin ve izleme tümleştirmelerine ekleyin. Python [istekleri](https://pypi.org/project/requests/) kitaplığı kullanılarak gönderilen istekler izlenir.

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

## <a name="dependencies-with-httplib-integration"></a>"Httplib" tümleştirmesi ile bağımlılıklar

OpenCensus `httplib` tümleştirmesiyle giden isteklerinizi izleyin.

[Pypı](https://pypi.org/project/opencensus-ext-httplib/) 'den `opencensus-ext-httplib` indirip yükleyin ve izleme tümleştirmelerine ekleyin. Http kullanılarak gönderilen istekler, Python3 için [istemci](https://docs.python.org/3.7/library/http.client.html) veya Python2 için [httplib](https://docs.python.org/2/library/httplib.html) izlenir.

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

## <a name="dependencies-with-django-integration"></a>"Docgo" tümleştirmesi ile bağımlılıklar

OpenCensus `django` tümleştirmesiyle giden Docgo isteklerinizi izleyin.

[Pypı](https://pypi.org/project/opencensus-ext-django/) 'den `opencensus-ext-django` indirip yükleyin ve Docgo `settings.py` dosyasının `MIDDLEWARE` bölümüne aşağıdaki satırı ekleyin.

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

Ek yapılandırma sağlandıysa, tüm başvuru için [Özelleştirmeleri](https://github.com/census-instrumentation/opencensus-python#customization) okuyun.

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

## <a name="dependencies-with-mysql-integration"></a>"MySQL" tümleştirmesiyle bağımlılıklar

OpenCensus `mysql` tümleştirmesiyle MYSQL bağımlılıklarınızı izleyin. Bu tümleştirme [MySQL-Connector](https://pypi.org/project/mysql-connector-python/) kitaplığını destekler.

[Pypı](https://pypi.org/project/opencensus-ext-mysql/) 'den `opencensus-ext-mysql` indirip yükleyin ve kodunuza aşağıdaki satırları ekleyin.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>"Pymysql" tümleştirmesi içeren Bağımlılıklar

OpenCensus `pymysql` tümleştirmesiyle PyMySQL bağımlılıklarınızı izleyin.

[Pypı](https://pypi.org/project/opencensus-ext-pymysql/) 'den `opencensus-ext-pymysql` indirip yükleyin ve kodunuza aşağıdaki satırları ekleyin.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>"PostgreSQL" tümleştirmesi ile bağımlılıklar

OpenCensus `postgresql` tümleştirmesiyle PostgreSQL bağımlılıklarınızı izleyin. Bu tümleştirme [psycopg2](https://pypi.org/project/psycopg2/) kitaplığını destekler.

[Pypı](https://pypi.org/project/opencensus-ext-postgresql/) 'den `opencensus-ext-postgresql` indirip yükleyin ve kodunuza aşağıdaki satırları ekleyin.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>"Pymongo" tümleştirmesi ile bağımlılıklar

OpenCensus `pymongo` tümleştirmesiyle MongoDB bağımlılıklarınızı izleyin. Bu tümleştirme, [pymongo](https://pypi.org/project/pymongo/) kitaplığını destekler.

[Pypı](https://pypi.org/project/opencensus-ext-pymongo/) 'den `opencensus-ext-pymongo` indirip yükleyin ve kodunuza aşağıdaki satırları ekleyin.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>"Sqlalchemy" tümleştirmesiyle bağımlılıklar

OpenCensus `sqlalchemy` tümleştirmesini kullanarak SQLAlchemy kullanarak bağımlılıklarınızı izleyin. Bu tümleştirme, temel alınan veritabanından bağımsız olarak [sqlalchemy](https://pypi.org/project/SQLAlchemy/) paketinin kullanımını izler.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>Sonraki adımlar

* [Uygulama Eşlemesi](../../azure-monitor/app/app-map.md)
* [Kullanılabilirlik](../../azure-monitor/app/monitor-web-app-availability.md)
* [Search](../../azure-monitor/app/diagnostic-search.md)
* [Log (Analytics) sorgusu](../../azure-monitor/log-query/log-query-overview.md)
* [İşlem tanılama](../../azure-monitor/app/transaction-diagnostics.md)