---
title: Azure Işlevlerinde Python uygulamalarının üretilen iş performansını geliştirme
description: Çok iyi performans ve yük altında ölçeklendirme olan Python kullanarak Azure Işlevleri uygulamaları geliştirmeyi öğrenin.
ms.topic: article
ms.date: 10/13/2020
ms.custom: devx-track-python
ms.openlocfilehash: e3bbdb8819062d45d071633e0208fb58a003da54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98786115"
---
# <a name="improve-throughput-performance-of-python-apps-in-azure-functions"></a>Azure Işlevlerinde Python uygulamalarının üretilen iş performansını geliştirme

Python kullanarak Azure Işlevleri için geliştirme yaparken, işlevlerinizin nasıl gerçekleştirileceğini ve bu performansın, işlev uygulamanızın nasıl ölçeklendirileceğini anlamanız gerekir. Yüksek performanslı uygulamalar tasarlarken gerek daha önemlidir. İşlevlerinizi tasarlarken, yazarken ve yapılandırırken göz önünde bulundurmanız gereken ana faktörler, yatay ölçeklendirme ve verimlilik performans yapılandırmalarıdır.

## <a name="horizontal-scaling"></a>Yatay ölçeklendirme
Varsayılan olarak, Azure Işlevleri uygulamanızdaki yükü otomatik olarak izler ve gerektiğinde Python için ek konak örnekleri oluşturur. Azure Işlevleri, QueueTrigger için ileti yaşı ve sıra boyutu gibi örneklerin ne zaman ekleneceğini belirlemek için farklı tetikleyici türleri için yerleşik eşikleri kullanır. Bu eşikler Kullanıcı tarafından yapılandırılabilir değildir. Daha fazla bilgi için bkz. [Azure Işlevlerinde olay odaklı ölçeklendirme](event-driven-scaling.md).

## <a name="improving-throughput-performance"></a>Verimlilik performansını artırma

Varsayılan yapılandırma, Azure Işlevleri uygulamalarının çoğu için uygundur. Ancak, iş yükü profilinize göre yapılandırma uygulayarak uygulamalarınızın aktarım hızı performansını artırabilirsiniz. İlk adım, çalıştırdığınız iş yükünün türünü anlamaktır.

| İş yükü türü | İşlev uygulaması özellikleri       | Örnekler                                          |
| ------------- | ---------------------------------- | ------------------------------------------------- |
| **G/ç 'ye bağlanma**     | • Uygulamanın birçok eşzamanlı çağırma işlemesi gerekir.<br>• Uygulama, ağ çağrıları ve disk okuma/yazma gibi çok sayıda g/ç olayını işler. | • Web API 'Leri                                          |
| **CPU 'ya dayalı**     | • Uygulama, görüntü yeniden boyutlandırma gibi uzun süre çalışan hesaplamalar yapar.<br>• Uygulama veri dönüştürmesi yapar.                                                | • Veri işleme<br>• Makine öğrenimi çıkarımı<br> |

 
Gerçek dünya işlevi iş yükleri genellikle g/ç ve CPU sınırının bir karışımı olduğu için, uygulamayı gerçekçi üretim yükleri altında profillendirilmelisiniz.


### <a name="performance-specific-configurations"></a>Performansa özgü yapılandırma

İşlev uygulamanızın iş yükü profilini öğrendikten sonra, işlevlerinizin aktarım hızını artırmak için kullanabileceğiniz yapılandırma işlemleri aşağıda verilmiştir.

* [Eş](#async)
* [Birden çok dil çalışanı](#use-multiple-language-worker-processes)
* [Bir dil çalışan işlemi içinde en fazla çalışan sayısı](#set-up-max-workers-within-a-language-worker-process)
* [Olay döngüsü](#managing-event-loop)
* [Dikey ölçeklendirme](#vertical-scaling)



#### <a name="async"></a>Zaman Uyumsuz

[Python tek iş parçacıklı bir çalışma zamanı](https://wiki.python.org/moin/GlobalInterpreterLock)olduğundan, Python için bir konak örneği varsayılan olarak bir seferde yalnızca bir işlev çağrısı işleyebilir. Çok sayıda g/ç olayını işleyen ve/veya g/ç bağlantılı uygulamalar için, işlevleri zaman uyumsuz olarak çalıştırarak performansı önemli ölçüde artırabilirsiniz.

Bir işlevi zaman uyumsuz olarak çalıştırmak için, `async def` işlevi zaman uyumsuz [CIO](https://docs.python.org/3/library/asyncio.html) ile doğrudan çalıştıran ifadesini kullanın:

```python
async def main():
    await some_nonblocking_socket_io_op()
```
[Aiohttp](https://pypi.org/project/aiohttp/) http ISTEMCISINI kullanan http tetikleyicisine sahip bir işleve örnek aşağıda verilmiştir:

```python
import aiohttp

import azure.functions as func

async def main(req: func.HttpRequest) -> func.HttpResponse:
    async with aiohttp.ClientSession() as client:
        async with client.get("PUT_YOUR_URL_HERE") as response:
            return func.HttpResponse(await response.text())

    return func.HttpResponse(body='NotFound', status_code=404)
```


Anahtar sözcüğü olmayan bir işlev, `async` bir ThreadPoolExecutor iş parçacığı havuzunda otomatik olarak çalıştırılır:

```python
# Runs in an ThreadPoolExecutor threadpool. Number of threads is defined by PYTHON_THREADPOOL_THREAD_COUNT. 
# The example is intended to show how default synchronous function are handled.

def main():
    some_blocking_socket_io()
```

İşlevleri zaman uyumsuz olarak çalıştırmanın tam avantajlarından yararlanmak için, kodunuzda kullanılan g/ç işlemi/kitaplığı, zaman uyumsuz olarak uygulanmanız gerekir. Zaman uyumsuz olarak tanımlanan işlevlerde zaman uyumlu g/ç işlemlerinin kullanılması genel **performansı düşürebilir.** Kullandığınız kitaplıkların zaman uyumsuz sürümü uygulanmışsa, uygulamanızdaki [olay döngüsünü yöneterek](#managing-event-loop) kodunuzu zaman uyumsuz olarak çalıştırmaya da yarar olabilir. 

Zaman uyumsuz model uygulayan istemci kitaplıklarına birkaç örnek aşağıda verilmiştir:
- [aiohttp](https://pypi.org/project/aiohttp/) -zaman uyumsuz CIO için http istemcisi/sunucusu 
- Ağ bağlantısıyla çalışmak için API-üst düzey zaman uyumsuz/await-Ready basit temelleri [akışlar](https://docs.python.org/3/library/asyncio-stream.html)
- [Inus kuyruğu](https://pypi.org/project/janus/) -iş parçacığı için güvenli zaman uyumsuz CIO-Python kuyruğu
- [pyzmq](https://pypi.org/project/pyzmq/) -ZeroMQ için Python bağlamaları
 
##### <a name="understanding-async-in-python-worker"></a>Python çalışanındaki zaman uyumsuz anlama

`async`Bir işlev imzasının önünde tanımladığınızda, Python işlevi bir eş yordam olarak işaretleyecek. Eş yordam çağrılırken bir olay döngüsüne bir görev olarak zamanlanabilir. `await`Zaman uyumsuz bir işlevde çağırdığınızda, olay döngüsüne bir devamlılık kaydeder ve olay döngüsünün bekleme süresi boyunca sonraki görevi işlemesini sağlar.

Python çalışanımızda çalışan, olay döngüsünü müşterinin `async` işleviyle paylaşır ve aynı anda birden çok isteği işleme yeteneğine sahiptir. Müşterilerimiz, zaman uyumsuz CIO ile uyumlu kitaplıkların (ör. [aiohttp](https://pypi.org/project/aiohttp/), [pyzmq](https://pypi.org/project/pyzmq/)) kullanılmasını kesinlikle öneririz. Bu önerilerin uygulanması, zaman uyumlu olarak uygulanan kitaplıklarla karşılaştırıldığında işlevinizin aktarım hızını önemli ölçüde artırır.

> [!NOTE]
>  İşleviniz, `async` uygulamasının içinde herhangi bir değer olmadan olarak bildirilirse `await` , Python çalışanının eşzamanlı istekleri işlemesini engelleyen olay döngüsü engellendiğinden işlevinizin performansı ciddi bir şekilde etkilenir.

#### <a name="use-multiple-language-worker-processes"></a>Birden çok dil çalışan işlemi kullanma

Varsayılan olarak, her Işlev ana bilgisayar örneği tek bir dil çalışan işlemine sahiptir. [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) uygulama ayarını kullanarak konak başına çalışan işlem sayısını (10 ' a kadar) artırabilirsiniz. Azure Işlevleri daha sonra bu çalışanlar genelinde aynı anda eşzamanlı işlev etkinleştirmeleri dağıtmaya çalışır.

CPU 'ya bağlanan uygulamalar için, dil çalışanı sayısını, işlev uygulaması başına kullanılabilir çekirdek sayısıyla aynı veya ondan daha yüksek olacak şekilde ayarlamanız gerekir. Daha fazla bilgi edinmek için bkz. [kullanılabilir örnek SKU 'ları](functions-premium-plan.md#available-instance-skus). 

G/ç bağlantılı uygulamalar, kullanılabilir çekirdek sayısının ötesinde çalışan işlem sayısını arttırmadan da yararlanabilir. Çalışan sayısını çok yüksek olarak ayarlamanın, gereken bağlam anahtarlarının sayısı arttığı için genel performansı etkilediğini aklınızda bulundurun. 

FUNCTIONS_WORKER_PROCESS_COUNT, uygulamanızın talebi karşılamak üzere ölçeklenmesi sırasında oluşturduğu her bir konak için geçerlidir.

#### <a name="set-up-max-workers-within-a-language-worker-process"></a>Bir dil çalışan işlemi içinde en fazla çalışanı ayarlama

Zaman uyumsuz [bölümünde](#understanding-async-in-python-worker)belirtildiği gibi, Python dil çalışanı [işlevleri farklı şekilde](https://docs.python.org/3/library/asyncio-task.html#coroutines) ele alır. Eş yordam, dil çalışanının üzerinde çalıştığı aynı olay döngüsü içinde çalıştırılır. Diğer taraftan, bir işlev çağırma bir [Threadpoolexecutor](https://docs.python.org/3/library/concurrent.futures.html#threadpoolexecutor)içinde çalıştırılarak, iş parçacığı olarak dil çalışanı tarafından korunur.

[PYTHON_THREADPOOL_THREAD_COUNT](functions-app-settings.md#python_threadpool_thread_count) uygulama ayarını kullanarak eşitleme işlevlerini çalıştırmaya izin verilen en fazla çalışan değerlerini ayarlayabilirsiniz. Bu değer, `max_worker` Python 'un `max_worker` çağrıları zaman uyumsuz olarak yürütmek için en çok iş parçacığı havuzu kullanmasını sağlayan ThreadPoolExecutor nesnesinin bağımsız değişkenini ayarlar. , `PYTHON_THREADPOOL_THREAD_COUNT` Ana bilgisayar tarafından oluşturulan her çalışan için geçerlidir ve Python yeni bir iş parçacığı oluşturma veya mevcut boşta iş parçacığını yeniden kullanma konusunda karar verir. Daha eski Python sürümleri (yani,, `3.8` `3.7` ve) için `3.6` , `max_worker` değeri 1 olarak ayarlanır. Python sürümü için `3.9` `max_worker` olarak ayarlanır `None` .

CPU 'ya yönelik uygulamalar için, ayarı 1 ' den başlayıp, iş yükünüzün deneyince giderek artırarak, düşük bir sayı olarak tutmanız gerekir. Bu öneri, bağlam anahtarlarında harcanan süreyi azaltmaktır ve CPU ile bağlantılı görevlerin bitmesini sağlar.

G/ç 'ye bağlı uygulamalar için, her bir çağrıdan çalışan iş parçacığı sayısını artırarak önemli kazanç görmeniz gerekir. öneri, varsayılan Python-çekirdek sayısı + 4 ve ardından gördüğünüz aktarım hızı değerlerine göre ince ayar ile başlamasıdır.

Karıştırma iş yükleri uygulamaları için, `FUNCTIONS_WORKER_PROCESS_COUNT` `PYTHON_THREADPOOL_THREAD_COUNT` aktarım hızını en üst düzeye çıkarmak üzere hem hem de yapılandırmalarının dengelenmesi gerekir. İşlev uygulamalarınızın en çok ne zaman harcadığını anlamak için, bunların profilini oluşturup bunları mevcut davranışlarına göre ayarlamanız önerilir. Ayrıca, FUNCTIONS_WORKER_PROCESS_COUNT uygulama ayarları hakkında bilgi edinmek için bu [bölüme](#use-multiple-language-worker-processes) bakın.

> [!NOTE]
>  Bu öneriler hem HTTP hem de HTTP dışı tetiklemeli işlevlere uygulansa da, işlev uygulamalarınızdan beklenen performansı almak için, HTTP ile tetiklenen işlevlere yönelik diğer tetikleyiciye özgü yapılandırma ayarlarını yapmanız gerekebilir. Bunun hakkında daha fazla bilgi için lütfen bu [makaleye](functions-best-practices.md)bakın.


#### <a name="managing-event-loop"></a>Olay döngüsünü yönetme

Zaman uyumsuz CIO uyumlu üçüncü taraf kitaplıklarını kullanmanız gerekir. Üçüncü taraf kitaplıkların hiçbiri ihtiyaçlarınıza uygun değilse, Azure Işlevlerinde olay döngülerini da yönetebilirsiniz. Olay döngülerini yönetmek, işlem kaynak yönetiminde daha fazla esneklik sağlar ve aynı zamanda zaman uyumlu g/ç kitaplıklarını eş parçalara kaydırmayı da mümkün kılar.

Yerleşik zaman **uyumsuz CIO** kitaplığını kullanarak eş yordamlar [ve görevler](https://docs.python.org/3/library/asyncio-task.html) ve [olay döngülerinde](https://docs.python.org/3.8/library/asyncio-eventloop.html) ele alınması gereken çok sayıda kullanışlı Python resmi belge vardır.

Aşağıdaki [istekler](https://github.com/psf/requests) kitaplığını bir örnek olarak alın; bu kod parçacığı, yöntemi eş zamanlı bir şekilde kaydırmak için zaman **uyumsuz CIO** kitaplığını kullanır ve `requests.get()` aynı anda SAMPLE_URL için birden çok Web isteği çalıştırır.


```python
import asyncio
import json
import logging

import azure.functions as func
from time import time
from requests import get, Response


async def invoke_get_request(eventloop: asyncio.AbstractEventLoop) -> Response:
    # Wrap requests.get function into a coroutine
    single_result = await eventloop.run_in_executor(
        None,  # using the default executor
        get,  # each task call invoke_get_request
        'SAMPLE_URL'  # the url to be passed into the requests.get function
    )
    return single_result

async def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    eventloop = asyncio.get_event_loop()

    # Create 10 tasks for requests.get synchronous call
    tasks = [
        asyncio.create_task(
            invoke_get_request(eventloop)
        ) for _ in range(10)
    ]

    done_tasks, _ = await asyncio.wait(tasks)
    status_codes = [d.result().status_code for d in done_tasks]

    return func.HttpResponse(body=json.dumps(status_codes),
                             mimetype='application/json')
```
#### <a name="vertical-scaling"></a>Dikey ölçeklendirme
Özellikle CPU bağlantılı işlemde daha fazla işleme birimi için, daha yüksek belirtimlerle Premium plana yükselterek bunu edinebilirsiniz. Daha yüksek işleme birimleri sayesinde, kullanılabilir çekirdek sayısına göre çalışan işlem sayısı sayısını ayarlayabilir ve daha yüksek bir paralellik derecesi elde edebilirsiniz. 

## <a name="next-steps"></a>Sonraki adımlar

Azure Işlevleri Python geliştirmesi hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Azure Işlevleri Python Geliştirici Kılavuzu](functions-reference-python.md)
* [Azure İşlevleri için en iyi uygulamalar](functions-best-practices.md)
* [Azure İşlevleri geliştirici başvurusu](functions-reference.md)

