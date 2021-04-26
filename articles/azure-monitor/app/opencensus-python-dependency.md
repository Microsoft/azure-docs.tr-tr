---
title: OpenCensus Python ile Azure Application Insights bağımlılık Izleme | Microsoft Docs
description: OpenCensus Python aracılığıyla Python uygulamalarınızın bağımlılık çağrılarını izleyin.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: a8f673295236d60ec6681bbfaee1201a4659674b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585709"
---
# <a name="track-dependencies-with-opencensus-python"></a>OpenCensus Python ile bağımlılıkları izleme

Bağımlılık, uygulamanız tarafından çağrılan bir dış bileşendir. Bağımlılık verileri, OpenCensus Python ve çeşitli tümleştirmeler kullanılarak toplanır. Veriler daha sonra Azure Izleyici altında telemetri olarak Application Insights gönderilir `dependencies` .

İlk olarak, Python uygulamanızı en son [Opencensus Python SDK 'sı](./opencensus-python.md)ile işaretleyin.

## <a name="in-process-dependencies"></a>İşlem içi bağımlılıklar

Azure Izleyici için OpenCensus Python SDK 'Sı, "işlem içi" bağımlılık telemetrisi (uygulamanızda gerçekleşen bilgi ve mantık) göndermenizi sağlar. İşlem içi bağımlılıklarda, `type` analiz içinde olduğu gibi alan olacaktır `INPROC` .

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>"İstekler" tümleştirmesi olan bağımlılıklar

OpenCensus tümleştirmesiyle giden isteklerinizi izleyin `requests` .

`opencensus-ext-requests` [Pypı](https://pypi.org/project/opencensus-ext-requests/) 'den indirip yükleyin ve izleme tümleştirmelerine ekleyin. Python [istekleri](https://pypi.org/project/requests/) kitaplığı kullanılarak gönderilen istekler izlenir.

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

OpenCensus tümleştirmesiyle giden isteklerinizi izleyin `httplib` .

`opencensus-ext-httplib` [Pypı](https://pypi.org/project/opencensus-ext-httplib/) 'den indirip yükleyin ve izleme tümleştirmelerine ekleyin. Http kullanılarak gönderilen istekler, Python3 için [istemci](https://docs.python.org/3.7/library/http.client.html) veya Python2 için [httplib](https://docs.python.org/2/library/httplib.html) izlenir.

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

OpenCensus tümleştirmesiyle giden Docgo isteklerinizi izleyin `django` .

> [!NOTE]
> İzlenen tek giden Docgo istekleri bir veritabanına yapılan çağrılardır. Docgo uygulamasına yapılan istekler için bkz. [gelen istekler](./opencensus-python-request.md#tracking-django-applications).

`opencensus-ext-django` [Pypı](https://pypi.org/project/opencensus-ext-django/) 'den Indirip yükleyin ve `MIDDLEWARE` docgo dosyasındaki bölümüne aşağıdaki satırı ekleyin `settings.py` .

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
        'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
            connection_string="InstrumentationKey=<your-ikey-here>"
        )''',
    }
}
```

## <a name="dependencies-with-mysql-integration"></a>"MySQL" tümleştirmesiyle bağımlılıklar

OpenCensus tümleştirmesiyle MYSQL bağımlılıklarınızı izleyin `mysql` . Bu tümleştirme [MySQL-Connector](https://pypi.org/project/mysql-connector-python/) kitaplığını destekler.

`opencensus-ext-mysql` [Pypı](https://pypi.org/project/opencensus-ext-mysql/) 'den indirip yükleyin ve kodunuza aşağıdaki satırları ekleyin.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>"Pymysql" tümleştirmesi içeren Bağımlılıklar

OpenCensus tümleştirmesiyle PyMySQL bağımlılıklarınızı izleyin `pymysql` .

`opencensus-ext-pymysql` [Pypı](https://pypi.org/project/opencensus-ext-pymysql/) 'den indirip yükleyin ve kodunuza aşağıdaki satırları ekleyin.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>"PostgreSQL" tümleştirmesi ile bağımlılıklar

OpenCensus tümleştirmesiyle PostgreSQL bağımlılıklarınızı izleyin `postgresql` . Bu tümleştirme [psycopg2](https://pypi.org/project/psycopg2/) kitaplığını destekler.

`opencensus-ext-postgresql` [Pypı](https://pypi.org/project/opencensus-ext-postgresql/) 'den indirip yükleyin ve kodunuza aşağıdaki satırları ekleyin.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>"Pymongo" tümleştirmesi ile bağımlılıklar

OpenCensus tümleştirmesiyle MongoDB bağımlılıklarınızı izleyin `pymongo` . Bu tümleştirme, [pymongo](https://pypi.org/project/pymongo/) kitaplığını destekler.

`opencensus-ext-pymongo` [Pypı](https://pypi.org/project/opencensus-ext-pymongo/) 'den indirip yükleyin ve kodunuza aşağıdaki satırları ekleyin.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>"Sqlalchemy" tümleştirmesiyle bağımlılıklar

OpenCensus tümleştirmesi kullanarak SQLAlchemy kullanarak bağımlılıklarınızı izleyin `sqlalchemy` . Bu tümleştirme, temel alınan veritabanından bağımsız olarak [sqlalchemy](https://pypi.org/project/SQLAlchemy/) paketinin kullanımını izler.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>Sonraki adımlar

* [Uygulama Eşlemesi](./app-map.md)
* [Kullanılabilirlik](./monitor-web-app-availability.md)
* [Ara](./diagnostic-search.md)
* [Log (Analytics) sorgusu](../logs/log-query-overview.md)
* [İşlem tanılamaları](./transaction-diagnostics.md)

