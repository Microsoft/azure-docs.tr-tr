---
title: Azure Işlevlerinde Python uygulamalarında bellek profili oluşturma
description: Python uygulamaları bellek kullanımının profilini oluşturmayı ve bellek performans sorunlarını belirlemeyi öğrenin.
ms.topic: how-to
author: hazhzeng
ms.author: hazeng
ms.date: 3/22/2021
ms.custom: devx-track-python
ms.openlocfilehash: f47451809a2fa2b7d22805e6415056f19c4c1d88
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258818"
---
# <a name="profile-python-apps-memory-usage-in-azure-functions"></a>Azure Işlevlerinde Python uygulamaları bellek kullanımını profil halinde kullanma

Geliştirme sırasında veya yerel Python işlevi uygulama projenizi Azure 'a dağıttıktan sonra, işlevinizdeki olası bellek performans sorunlarını analiz etmek iyi bir uygulamadır. Bu performans sorunları, işlevlerinizin performansını azaltabilir ve hatalara neden olabilir. Aşağıdaki yönergede, işlevleri yürütülürken işlevlerinizin satır içi bellek tüketimi analizini sağlayan [bellek profili Oluşturucu](https://pypi.org/project/memory-profiler) Python paketinin nasıl kullanılacağı gösterilmektedir.

> [!NOTE]
> Bellek profili oluşturma yalnızca geliştirme ortamında bellek kaplama analizi için tasarlanmıştır. Lütfen üretim işlevi uygulamalarına bellek profil oluşturucuyu uygulamayın.

## <a name="prerequisites"></a>Önkoşullar

Bir Python işlev uygulaması geliştirmeye başlamadan önce, aşağıdaki gereksinimleri karşılamanız gerekir:

* [Python 3.6. x veya üzeri](https://www.python.org/downloads/release/python-374/). Azure Işlevlerinde desteklenen Python sürümlerinin tam listesini denetlemek için lütfen [Python Geliştirici Kılavuzu](functions-reference-python.md#python-version)' nu ziyaret edin.

* [Azure Functions Core Tools](functions-run-local.md#v2) sürüm 3. x.

* [Desteklenen platformlardan](https://code.visualstudio.com/docs/supporting/requirements#_platforms)birine [Visual Studio Code](https://code.visualstudio.com/) .

* Etkin bir Azure aboneliği.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="memory-profiling-process"></a>Bellek profili oluşturma işlemi

1. requirements.txt, `memory-profiler` paketin dağıtımınızla birlikte paketlenecek olduğundan emin olmak için ekleyin. Yerel makinenizde geliştirme yapıyorsanız, [bir Python sanal ortamını etkinleştirmek](create-first-function-cli-python.md#create-venv) ve tarafından bir paket çözümlemesi yapmak isteyebilirsiniz `pip install -r requirements.txt` .

2. İşlev betiğinizde (genellikle \_ \_ init \_ \_ . Kopyala), işlevin üstüne aşağıdaki satırları ekleyin `main()` . Bu, kök günlükçü 'nin alt günlükçü adlarını raporlayarak bellek profili oluşturma günlüklerinin önek tarafından ayırt edilemez olmasını sağlayacaktır `memory_profiler_logs` .

    ```python
    import logging
    import memory_profiler
    root_logger = logging.getLogger()
    root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
    profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

3. Apply the following decorator above any functions that need memory profiling. This does not work directly on the trigger entrypoint `main()` method. You need to create subfunctions and decorate them. Also, due to a memory-profiler known issue, when applying to an async coroutine, the coroutine return value will always be None.

    ```python
    @memory_profiler.profile(stream=profiler_logstream)

4. Test the memory profiler on your local machine by using azure Functions Core Tools command `func host start`. This should generate a memory usage report with file name, line of code, memory usage, memory increment, and the line content in it.

5. To check the memory profiling logs on an existing function app instance in Azure, you can query the memory profiling logs in recent invocations by pasting the following Kusto queries in Application Insights, Logs.

:::image type="content" source="media/python-memory-profiler-reference/application-insights-query.png" alt-text="Query memory usage of a Python app in Application Insights":::

```text
traces
| where timestamp > ago(1d)
| where message startswith_cs "memory_profiler_logs:"
| parse message with "memory_profiler_logs: " LineNumber "  " TotalMem_MiB "  " IncreMem_MiB "  " Occurences "  " Contents
| union (
    traces
    | where timestamp > ago(1d)
    | where message startswith_cs "memory_profiler_logs: Filename: "
    | parse message with "memory_profiler_logs: Filename: " FileName
    | project timestamp, FileName, itemId
)
| project timestamp, LineNumber=iff(FileName != "", FileName, LineNumber), TotalMem_MiB, IncreMem_MiB, Occurences, Contents, RequestId=itemId
| order by timestamp asc
```

## <a name="example"></a>Örnek

Aşağıda, sırasıyla "HttpTriggerAsync" ve "HttpTriggerSync" adlı bir zaman uyumsuz ve zaman uyumlu HTTP tetikleyicilerinde bellek profili oluşturma işlemi örneği verilmiştir. Yalnızca Microsoft 'un giriş sayfasına GET istekleri gönderen bir Python işlev uygulaması oluşturacağız.

### <a name="create-a-python-function-app"></a>Python işlev uygulaması oluşturma

Python işlev uygulaması, Azure Işlevleri belirtilen [klasör yapısını](functions-reference-python.md#folder-structure)izlemelidir. Projeyi iskele almak için aşağıdaki komutları çalıştırarak Azure Functions Core Tools kullanmanızı öneririz:

```bash
func init PythonMemoryProfilingDemo --python
cd PythonMemoryProfilingDemo
func new -l python -t HttpTrigger -n HttpTriggerAsync -a anonymous
func new -l python -t HttpTrigger -n HttpTriggerSync -a anonymous
```

### <a name="update-file-contents"></a>Dosya içeriğini güncelleştirme

requirements.txt, projemizdeki kullanılacak paketleri tanımlar. Azure Işlevleri SDK 'sının ve bellek profili oluşturucunun yanı sıra, `aiohttp` zaman uyumsuz http istekleri ve `requests` zaman uyumlu http çağrıları için tanıtıldık.

```text
# requirements.txt

azure-functions
memory-profiler
aiohttp
requests
```

Ayrıca, zaman uyumsuz HTTP tetikleyicisini yeniden yazıp `HttpTriggerAsync/__init__.py` bellek profil oluşturucuyu, kök günlükçü biçimini ve günlükçü akış bağlamasını yapılandırmanız da gerekir.

```python
# HttpTriggerAsync/__init__.py

import azure.functions as func
import aiohttp
import logging
import memory_profiler

# Update root logger's format to include the logger name. Ensure logs generated
# from memory profiler can be filtered by "memory_profiler_logs" prefix.
root_logger = logging.getLogger()
root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

async def main(req: func.HttpRequest) -> func.HttpResponse:
    await get_microsoft_page_async('https://microsoft.com')
    return func.HttpResponse(
        f"Microsoft Page Is Loaded",
        status_code=200
    )

@memory_profiler.profile(stream=profiler_logstream)
async def get_microsoft_page_async(url: str):
    async with aiohttp.ClientSession() as client:
        async with client.get(url) as response:
            await response.text()
    # @memory_profiler.profile does not support return for coroutines.
    # All returns become None in the parent functions.
    # GitHub Issue: https://github.com/pythonprofilers/memory_profiler/issues/289
```

Zaman uyumlu HTTP tetikleyicisi için lütfen şu `HttpTriggerSync/__init__.py` kod bölümüne başvurun:

```python
# HttpTriggerSync/__init__.py

import azure.functions as func
import requests
import logging
import memory_profiler

# Update root logger's format to include the logger name. Ensure logs generated
# from memory profiler can be filtered by "memory_profiler_logs" prefix.
root_logger = logging.getLogger()
root_logger.handlers[0].setFormatter(logging.Formatter("%(name)s: %(message)s"))
profiler_logstream = memory_profiler.LogFile('memory_profiler_logs', True)

def main(req: func.HttpRequest) -> func.HttpResponse:
    content = profile_get_request('https://microsoft.com')
    return func.HttpResponse(
        f"Microsoft Page Response Size: {len(content)}",
        status_code=200
    )

@memory_profiler.profile(stream=profiler_logstream)
def profile_get_request(url: str):
    response = requests.get(url)
    return response.content
```

### <a name="profile-python-function-app-in-local-development-environment"></a>Yerel geliştirme ortamında profil Python işlev uygulaması

Yukarıdaki tüm değişiklikleri yaptıktan sonra, Azure Işlevleri çalışma zamanı için Python sanal ortamı başlatmak için birkaç adım daha vardır.

1. Tercih ettiğiniz şekilde bir Windows PowerShell veya Linux kabuğu açın.
2. `py -m venv .venv`Windows 'da veya Linux 'ta bir Python sanal ortamı oluşturun `python3 -m venv .venv` .
3. `.venv\Scripts\Activate.ps1`Windows PowerShell veya Linux kabuğu 'nda bulunan Python sanal ortamını etkinleştirin `source .venv/bin/activate` .
4. Python bağımlılıklarını geri yükleme `pip install requirements.txt`
5. Azure Işlevleri çalışma zamanını yerel olarak Azure Functions Core Tools başlatın `func host start`
6. Veya ' a bir GET isteği gönderin `https://localhost:7071/api/HttpTriggerAsync` `https://localhost:7071/api/HttpTriggerSync` .
7. Azure Functions Core Tools ' deki aşağıdaki bölüme benzer bir bellek profili raporu göstermelidir.

    ```text
    Filename: <ProjectRoot>\HttpTriggerAsync\__init__.py
    Line #    Mem usage    Increment  Occurences   Line Contents
    ============================================================
        19     45.1 MiB     45.1 MiB           1   @memory_profiler.profile
        20                                         async def get_microsoft_page_async(url: str):
        21     45.1 MiB      0.0 MiB           1       async with aiohttp.ClientSession() as client:
        22     46.6 MiB      1.5 MiB          10           async with client.get(url) as response:
        23     47.6 MiB      1.0 MiB           4               await response.text()
    ```

## <a name="next-steps"></a>Sonraki adımlar

Azure Işlevleri Python geliştirmesi hakkında daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Azure Işlevleri Python Geliştirici Kılavuzu](functions-reference-python.md)
* [Azure İşlevleri için en iyi uygulamalar](functions-best-practices.md)
* [Azure İşlevleri geliştirici başvurusu](functions-reference.md)
