---
title: OpenCensus Python ile Azure Application Insights gelen Istek Izleme | Microsoft Docs
description: OpenCensus Python aracılığıyla Python uygulamalarınızın istek çağrılarını izleyin.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: 3b029a9cb14a81c80072847dc17d6b71f480743f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585672"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>OpenCensus Python ile gelen istekleri izleme

Gelen istek verileri, OpenCensus Python ve çeşitli tümleştirmeler kullanılarak toplanır. Popüler Web çerçeveleri ve üzerinde oluşturulmuş Web uygulamalarınıza gönderilen gelen istek verilerini izleyin `django` `flask` `pyramid` . Veriler daha sonra Azure Izleyici altında telemetri olarak Application Insights gönderilir `requests` .

İlk olarak, Python uygulamanızı en son [Opencensus Python SDK 'sı](./opencensus-python.md)ile işaretleyin.

## <a name="tracking-django-applications"></a>Docgo uygulamalarını izleme

1. `opencensus-ext-django` [Pypı](https://pypi.org/project/opencensus-ext-django/) 'den indirip yükleyin ve uygulamayı `django` Ara yazılım ile işaretleyin. Uygulamanıza gönderilen gelen istekler `django` izlenir.

2. `opencensus.ext.django.middleware.OpencensusMiddleware` `settings.py` Altına dosyanıza ekleyin `MIDDLEWARE` .

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. AzureExporter 'in altında düzgün şekilde yapılandırıldığından emin olun `settings.py` `OPENCENSUS` . İzlemek istemediğiniz URL 'lerden gelen istekler için, onları öğesine ekleyin `EXCLUDELIST_PATHS` .

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
            'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Flask uygulamalarını izleme

1. `opencensus-ext-flask` [Pypı](https://pypi.org/project/opencensus-ext-flask/) 'den indirip yükleyin ve uygulamayı `flask` Ara yazılım ile işaretleyin. Uygulamanıza gönderilen gelen istekler `flask` izlenir.

    ```python
    
    from flask import Flask
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.ext.flask.flask_middleware import FlaskMiddleware
    from opencensus.trace.samplers import ProbabilitySampler
    
    app = Flask(__name__)
    middleware = FlaskMiddleware(
        app,
        exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"),
        sampler=ProbabilitySampler(rate=1.0),
    )
    
    @app.route('/')
    def hello():
        return 'Hello World!'
    
    if __name__ == '__main__':
        app.run(host='localhost', port=8080, threaded=True)
    
    ```

2. `flask`Uygulamanızı aracılığıyla da yapılandırabilirsiniz `app.config` . İzlemek istemediğiniz URL 'lerden gelen istekler için, onları öğesine ekleyin `EXCLUDELIST_PATHS` .

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>Piramit uygulamalarını izleme

1. `opencensus-ext-django` [Pypı](https://pypi.org/project/opencensus-ext-pyramid/) 'den indirip yükleyin ve uygulamanızı ara ile işaretleyin `pyramid` . Uygulamanıza gönderilen gelen istekler `pyramid` izlenir.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. `pyramid`Arayı doğrudan kodda yapılandırabilirsiniz. İzlemek istemediğiniz URL 'lerden gelen istekler için, onları öğesine ekleyin `EXCLUDELIST_PATHS` .

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'EXCLUDELIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="tracking-fastapi-applications"></a>Fastapı uygulamalarını izleme

OpenCensus, Fastapı için bir uzantıya sahip değildir. Kendi Fastapı ara ortamınızı yazmak için aşağıdaki adımları izleyin:

1. Aşağıdaki bağımlılıklar gereklidir: 
    - [fastapi](https://pypi.org/project/fastapi/)
    - [uvice](https://pypi.org/project/uvicorn/)

2. [Fastapı ara yazılımı](https://fastapi.tiangolo.com/tutorial/middleware/)ekleyin. Yayılma türü sunucusunu ayarladığınızdan emin olun: `span.span_kind = SpanKind.SERVER` .

3. Uygulamanızı çalıştırın. Fastapı uygulamanıza yapılan çağrılar otomatik olarak izlenmeli ve telemetri doğrudan Azure Izleyici 'ye kaydedilecek.

    ```python 
    # Opencensus imports
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    from opencensus.trace.span import SpanKind
    from opencensus.trace.attributes_helper import COMMON_ATTRIBUTES
    # FastAPI imports
    from fastapi import FastAPI, Request
    # uvicorn
    import uvicorn

    app = FastAPI()

    HTTP_URL = COMMON_ATTRIBUTES['HTTP_URL']
    HTTP_STATUS_CODE = COMMON_ATTRIBUTES['HTTP_STATUS_CODE']

    # fastapi middleware for opencensus
    @app.middleware("http")
    async def middlewareOpencensus(request: Request, call_next):
        tracer = Tracer(exporter=AzureExporter(connection_string=f'InstrumentationKey={APPINSIGHTS_INSTRUMENTATIONKEY}'),sampler=ProbabilitySampler(1.0))
        with tracer.span("main") as span:
            span.span_kind = SpanKind.SERVER

            response = await call_next(request)

            tracer.add_attribute_to_current_span(
                attribute_key=HTTP_STATUS_CODE,
                attribute_value=response.status_code)
            tracer.add_attribute_to_current_span(
                attribute_key=HTTP_URL,
                attribute_value=str(request.url))

        return response

    @app.get("/")
    async def root():
        return "Hello World!"

    if __name__ == '__main__':
        uvicorn.run("example:app", host="127.0.0.1", port=5000, log_level="info")
    ```

## <a name="next-steps"></a>Sonraki adımlar

* [Uygulama Eşlemesi](./app-map.md)
* [Kullanılabilirlik](./monitor-web-app-availability.md)
* [Ara](./diagnostic-search.md)
* [Log (Analytics) sorgusu](../logs/log-query-overview.md)
* [İşlem tanılamaları](./transaction-diagnostics.md)

