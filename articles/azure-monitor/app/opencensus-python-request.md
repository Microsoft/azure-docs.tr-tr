---
title: OpenCensus Python ile Azure Application Insights gelen Istek Izleme | Microsoft Docs
description: OpenCensus Python aracılığıyla Python uygulamalarınızın istek çağrılarını izleyin.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: f6b06e7bb2bb8637ca28b2fa4185754f8686798e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587933"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>OpenCensus Python ile gelen istekleri izleme

Gelen istek verileri, OpenCensus Python ve çeşitli tümleştirmeler kullanılarak toplanır. Popüler Web çerçeveleri `django`, `flask` ve `pyramid`en üstünde oluşturulmuş Web uygulamalarınıza gönderilen gelen istek verilerini izleyin. Veriler daha sonra Azure Izleyici altında `requests` telemetri olarak Application Insights gönderilir.

İlk olarak, Python uygulamanızı en son [Opencensus Python SDK 'sı](../../azure-monitor/app/opencensus-python.md)ile işaretleyin.

## <a name="tracking-django-applications"></a>Docgo uygulamalarını izleme

1. [Pypı](https://pypi.org/project/opencensus-ext-django/) 'den `opencensus-ext-django` indirip yükleyin ve uygulamanızı `django` ara yazılım ile işaretleyin. `django` uygulamanıza gönderilen gelen istekler izlenir.

2. `MIDDLEWARE`altındaki `settings.py` dosyanıza `opencensus.ext.django.middleware.OpencensusMiddleware` ekleyin.

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. AzureExporter altındaki `settings.py` `OPENCENSUS`doğru yapılandırıldığından emin olun.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                service_name='foobar',
            )''',
        }
    }
    ```

4. Ayrıca, izlemek istemediğiniz istekler için `BLACKLIST_PATHS` altına `settings.py` URL 'ler ekleyebilirsiniz.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                service_name='foobar',
            )''',
            'BLACKLIST_PATHS': 'https://example.com',  <--- This site will not be traced if a request is sent from it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Flask uygulamalarını izleme

1. [Pypı](https://pypi.org/project/opencensus-ext-flask/) 'den `opencensus-ext-flask` indirip yükleyin ve uygulamanızı `flask` ara yazılım ile işaretleyin. `flask` uygulamanıza gönderilen gelen istekler izlenir.

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

2. `flask` ara ortamınızı doğrudan kodda yapılandırabilirsiniz. İzlemek istemediğiniz URL 'lerden gelen istekler için bunları `BLACKLIST_PATHS`ekleyin.

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                service_name='foobar',
            )''',
            'BLACKLIST_PATHS': 'https://example.com',  <--- This site will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>Piramit uygulamalarını izleme

1. [Pypı](https://pypi.org/project/opencensus-ext-pyramid/) 'den `opencensus-ext-django` indirip yükleyin ve uygulamanızı `pyramid` ara ile işaretleyin. `pyramid` uygulamanıza gönderilen gelen istekler izlenir.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. `pyramid` arasını doğrudan kodda yapılandırabilirsiniz. İzlemek istemediğiniz URL 'lerden gelen istekler için bunları `BLACKLIST_PATHS`ekleyin.

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    service_name='foobar',
                )''',
                'BLACKLIST_PATHS': 'https://example.com',  <--- This site will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="next-steps"></a>Sonraki adımlar

* [Uygulama Eşlemesi](../../azure-monitor/app/app-map.md)
* [Kullanılabilirlik](../../azure-monitor/app/monitor-web-app-availability.md)
* [Search](../../azure-monitor/app/diagnostic-search.md)
* [Log (Analytics) sorgusu](../../azure-monitor/log-query/log-query-overview.md)
* [İşlem tanılama](../../azure-monitor/app/transaction-diagnostics.md)
