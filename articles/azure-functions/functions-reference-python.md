---
title: Azure Işlevleri için Python geliştirici başvurusu
description: Python ile işlev geliştirmeyi anlama
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
keywords: Azure işlevleri, işlevler, olay işleme, dinamik işlem, sunucusuz mimari, Python
ms.service: azure-functions
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/16/2018
ms.author: glenga
ms.openlocfilehash: 01d8560ee2752f21eb52c00f4c337d1dca59b8fb
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082691"
---
# <a name="azure-functions-python-developer-guide"></a>Azure Işlevleri Python Geliştirici Kılavuzu

Bu makale, Python kullanarak Azure Işlevleri geliştirmeye giriş niteliğindedir. Aşağıdaki içerik [Azure işlevleri geliştirici kılavuzunu](functions-reference.md)zaten okuduğunuzu varsayar. 

Python 'da tek başına Işlev örnek projeleri için bkz. [Python işlevleri örnekleri](/samples/browse/?products=azure-functions&languages=python). 

## <a name="programming-model"></a>Programlama modeli

Azure Işlevleri, bir işlevin girişi işleyen ve çıkış üreten Python betikinizde durum bilgisiz bir yöntem olmasını bekler. Varsayılan olarak, çalışma zamanı yönteminin `__init__.py` dosyasında `main()` adlı genel bir yöntem olarak uygulanması beklenir. Ayrıca, [alternatif bir giriş noktası da belirtebilirsiniz](#alternate-entry-point).

Tetikleyiciler ve bağlamalardan alınan veriler, Function *. JSON* dosyasında tanımlanan `name` özelliği kullanılarak Yöntem öznitelikleri aracılığıyla işleve bağlanır. Örneğin, aşağıdaki _function. JSON_ , `req`ADLı bir http isteği tarafından tetiklenen basit bir işlevi anlatmaktadır:

```json
{
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

`__init__.py` dosyası aşağıdaki işlev kodunu içerir:

```python
def main(req):
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Ayrıca öznitelik türlerini ve dönüş türünü, Python türü ek açıklamalarını kullanarak işlevde açıkça bildirebilirsiniz. Bu, birçok Python kod Düzenleyicisi tarafından sunulan IntelliSense ve otomatik tamamlama özelliklerini kullanmanıza yardımcı olur.

```python
import azure.functions


def main(req: azure.functions.HttpRequest) -> str:
    user = req.params.get('user')
    return f'Hello, {user}!'
```

Yöntemlerinizi giriş ve çıkışları bağlamak için [Azure. Functions. *](/python/api/azure-functions/azure.functions?view=azure-python) paketinde bulunan Python ek açıklamalarını kullanın.

## <a name="alternate-entry-point"></a>Alternatif giriş noktası

Function *. JSON* dosyasında `scriptFile` ve `entryPoint` özelliklerini belirterek, bir işlevin varsayılan davranışını değiştirebilirsiniz. Örneğin, aşağıdaki _function. JSON_ , çalışma zamanına Azure işlevinizin giriş noktası olarak _Main.py_ dosyasında `customentry()` yöntemini kullanmasını söyler.

```json
{
  "scriptFile": "main.py",
  "entryPoint": "customentry",
  "bindings": [
      ...
  ]
}
```

## <a name="folder-structure"></a>Klasör yapısı

Python Işlevleri projesi için önerilen klasör yapısı aşağıdaki örneğe benzer şekilde görünür:

```
 __app__
 | - MyFirstFunction
 | | - __init__.py
 | | - function.json
 | | - example.py
 | - MySecondFunction
 | | - __init__.py
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.py
 | | - mySecondHelperFunction.py
 | - host.json
 | - requirements.txt
 tests
```
Ana proje klasörü (\_\_App\_\_) aşağıdaki dosyaları içerebilir:

* *Local. Settings. JSON*: yerel olarak çalışırken uygulama ayarlarını ve bağlantı dizelerini depolamak için kullanılır. Bu dosya Azure 'da yayınlanmıyor. Daha fazla bilgi için bkz. [Local. Settings. File](functions-run-local.md#local-settings-file).
* *requirements. txt*: sistemin Azure 'a yayımlarken yüklediği paketlerin listesini içerir.
* *Host. JSON*: bir işlev uygulamasındaki tüm işlevleri etkileyen genel yapılandırma seçeneklerini içerir. Bu dosya Azure 'da yayımlanır. Yerel olarak çalışırken tüm seçenekler desteklenmez. Daha fazla bilgi için bkz. [Host. JSON](functions-host-json.md).
* *funcignore*: (isteğe bağlı) Azure 'a yayımlanmaması gereken dosyaları bildirir.
* *gitignore*: (isteğe bağlı) yerel. Settings. JSON gibi bir git deposundan dışlanan dosyaları bildirir.

Her işlevin kendi kod dosyası ve bağlama yapılandırma dosyası (Function. JSON) vardır. 

Paylaşılan kod, \_\_App\_\_ayrı bir klasörde tutulmalıdır. SharedCode klasöründeki modüllere başvurmak için aşağıdaki sözdizimini kullanabilirsiniz:

```python
from __app__.SharedCode import myFirstHelperFunction
```

Yerel modüllerle bir işleve başvurmak için göreli içeri aktarma sözdizimini aşağıdaki şekilde kullanabilirsiniz:

```python
from . import example
```

Projenizi Azure 'daki bir işlev uygulamasına dağıttığınızda, *functionapp* klasörünün tüm içeriği pakete dahil edilmelidir, ancak klasörün kendisi değil. Testlerinizi proje klasöründen ayrı bir klasörde tutmanızı öneririz, bu örnekte `tests`. Bu, uygulamanıza test kodu dağıtmanızı önler. Daha fazla bilgi için bkz. [birim testi](#unit-testing).

## <a name="triggers-and-inputs"></a>Tetikleyiciler ve girişler

Girişler Azure Işlevlerinde iki kategoriye ayrılmıştır: giriş ve ek giriş tetikleyin. `function.json` dosyasında farklı olsalar da, kullanım Python kodunda özdeştir.  Tetikleyici ve giriş kaynaklarına yönelik bağlantı dizeleri veya parolalar, yerel olarak çalıştırılırken `local.settings.json` dosyasındaki değerlerle eşlenir ve Azure 'da çalışırken uygulama ayarları. 

Örneğin, aşağıdaki kod iki arasındaki farkı göstermektedir:

```json
// function.json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "route": "items/{id}"
    },
    {
      "name": "obj",
      "direction": "in",
      "type": "blob",
      "path": "samples/{id}",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```json
// local.settings.json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "python",
    "AzureWebJobsStorage": "<azure-storage-connection-string>"
  }
}
```

```python
# __init__.py
import azure.functions as func
import logging


def main(req: func.HttpRequest,
         obj: func.InputStream):

    logging.info(f'Python HTTP triggered function processed: {obj.read()}')
```

İşlev çağrıldığında, HTTP isteği işleve `req`olarak geçirilir. Yol URL 'sindeki _kimliğe_ göre Azure Blob depolama alanından bir giriş alınır ve işlev gövdesinde `obj` olarak kullanılabilir hale getirilir.  Burada belirtilen depolama hesabı, işlev uygulaması tarafından kullanılan depolama hesabı olan ' de bulunan bağlantı dizesidir.


## <a name="outputs"></a>Çıkışlar

Çıkış hem dönüş değeri hem de çıkış parametrelerinde ifade edilebilir. Yalnızca bir çıkış varsa, dönüş değerini kullanmanızı öneririz. Birden çok çıkış için çıkış parametrelerini kullanmanız gerekir.

Bir işlevin dönüş değerini bir çıkış bağlamasının değeri olarak kullanmak için, bağlamanın `name` özelliği, `function.json``$return` olarak ayarlanmalıdır.

Birden çok çıkış oluşturmak için, bağlamaya bir değer atamak üzere [`azure.functions.Out`](/python/api/azure-functions/azure.functions.out?view=azure-python) arabirimi tarafından sunulan `set()` yöntemi kullanın. Örneğin, aşağıdaki işlev bir kuyruğa ileti gönderebilir ve ayrıca bir HTTP yanıtı döndürebilir.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "req",
      "direction": "in",
      "type": "httpTrigger",
      "authLevel": "anonymous"
    },
    {
      "name": "msg",
      "direction": "out",
      "type": "queue",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "direction": "out",
      "type": "http"
    }
  ]
}
```

```python
import azure.functions as func


def main(req: func.HttpRequest,
         msg: func.Out[func.QueueMessage]) -> str:

    message = req.params.get('body')
    msg.set(message)
    return message
```

## <a name="logging"></a>Günlüğe kaydetme

Azure Işlevleri çalışma zamanı günlükçüsü erişimi, işlev uygulamanızda bir kök [`logging`](https://docs.python.org/3/library/logging.html#module-logging) işleyicisi aracılığıyla kullanılabilir. Bu günlükçü Application Insights bağlıdır ve işlev yürütmesi sırasında uyarıları ve hataları işaretetmenize olanak tanır.

Aşağıdaki örnek, işlev bir HTTP tetikleyicisi aracılığıyla çağrıldığında bir bilgi iletisi kaydeder.

```python
import logging


def main(req):
    logging.info('Python HTTP trigger function processed a request.')
```

Konsola farklı izleme düzeylerinde yazmanıza izin veren ek günlüğe kaydetme yöntemleri vardır:

| Yöntem                 | Açıklama                                |
| ---------------------- | ------------------------------------------ |
| **`critical(_message_)`**   | Kök günlükçü üzerinde düzeyi KRITIK olan bir ileti yazar.  |
| **`error(_message_)`**   | Kök günlükçü üzerinde düzey hatası olan bir ileti yazar.    |
| **`warning(_message_)`**    | Kök günlükçü üzerinde düzey uyarısı olan bir ileti yazar.  |
| **`info(_message_)`**    | Kök günlükçü üzerinde düzey bılgısıne sahip bir ileti yazar.  |
| **`debug(_message_)`** | Kök günlükçü üzerinde düzey hata ayıklama içeren bir ileti yazar.  |

Günlüğe kaydetme hakkında daha fazla bilgi edinmek için bkz. [Azure Işlevlerini izleme](functions-monitoring.md).

## <a name="http-trigger-and-bindings"></a>HTTP tetikleyicisi ve bağlamaları

HTTP tetikleyicisi, function. Jon dosyasında tanımlanmıştır. Bağlamanın `name`, işlevdeki adlandırılmış parametreyle eşleşmelidir. Önceki örneklerde `req` bir bağlama adı kullanılır. Bu parametre bir [HttpRequest] nesnesidir ve bir [HttpResponse] nesnesi döndürülür.

[HttpRequest] nesnesinden istek üst bilgilerini, sorgu parametrelerini, yol parametrelerini ve ileti gövdesini alabilirsiniz. 

Aşağıdaki örnek, [Python Için http tetikleyici şablonundan](https://github.com/Azure/azure-functions-templates/tree/dev/Functions.Templates/Templates/HttpTrigger-Python)verilmiştir. 

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    headers = {"my-http-header": "some-value"}

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')
            
    if name:
        return func.HttpResponse(f"Hello {name}!", headers=headers)
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             headers=headers, status_code=400
        )
```

Bu işlevde, `name` sorgu parametresinin değeri, [HttpRequest] nesnesinin `params` parametresinden elde edilir. JSON kodlu ileti gövdesi `get_json` yöntemi kullanılarak okundu. 

Benzer şekilde, döndürülen [HttpResponse] nesnesindeki yanıt iletisi için `status_code` ve `headers` belirleyebilirsiniz.

## <a name="concurrency"></a>Eşzamanlılık

Varsayılan olarak, Python Runtime Işlevleri tek seferde yalnızca bir işlev çağrısını işleyebilir. Bu eşzamanlılık düzeyi aşağıdaki koşullardan biri veya birkaçı altında yeterli olmayabilir:

+ Aynı anda gerçekleştirilen bir dizi çağırmaları işlemeye çalışıyorsunuz.
+ Çok sayıda g/ç olayını işliyoruz.
+ Uygulamanız g/ç bağlıydı.

Bu durumlarda, zaman uyumsuz olarak çalıştırarak ve birden çok dil çalışan işlemini kullanarak performansı geliştirebilirsiniz.  

### <a name="async"></a>Eş

İşlevinizin zaman uyumsuz bir eş yordam olarak çalıştırılmasını sağlamak için `async def` ifadesini kullanmanızı öneririz.

```python
# Runs with asyncio directly

async def main():
    await some_nonblocking_socket_io_op()
```

`main()` işlevi zaman uyumlu olduğunda (`async` niteleyicisi olmadan), işlev bir `asyncio` iş parçacığı havuzunda otomatik olarak çalıştırılır.

```python
# Runs in an asyncio thread-pool

def main():
    some_blocking_socket_io()
```

### <a name="use-multiple-language-worker-processes"></a>Birden çok dil çalışan işlemi kullanma

Varsayılan olarak, her Işlev ana bilgisayar örneği tek bir dil çalışan işlemine sahiptir. Ancak, konak örneği başına birden çok dil çalışan işlemine sahip olmak için destek vardır. İşlev etkinleştirmeleri daha sonra bu dil çalışan süreçler arasında eşit olarak dağıtılabilir. Bu değeri değiştirmek için [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) uygulama ayarını kullanın. 

## <a name="context"></a>Bağlam

Yürütme sırasında bir işlevin çağırma bağlamını almak için, [`context`](/python/api/azure-functions/azure.functions.context?view=azure-python) bağımsız değişkenini imzasına ekleyin. 

Örneğin:

```python
import azure.functions


def main(req: azure.functions.HttpRequest,
         context: azure.functions.Context) -> str:
    return f'{context.invocation_id}'
```

[**Bağlam**](/python/api/azure-functions/azure.functions.context?view=azure-python) sınıfı aşağıdaki dize özniteliklerine sahiptir:

`function_directory`  
İşlevin çalıştığı dizin.

`function_name`  
İşlevin adı.

`invocation_id`  
Geçerli işlev çağırma KIMLIĞI.

## <a name="global-variables"></a>Genel değişkenler

Uygulamanızın durumunun gelecekteki yürütmeler için korunacağı garanti edilmez. Ancak, Azure Işlevleri çalışma zamanı, aynı uygulamanın birden fazla yürütmeleri için aynı süreci genellikle yeniden kullanır. Pahalı bir hesaplamanın sonuçlarını önbelleğe almak için, genel bir değişken olarak bildirin. 

```python
CACHED_DATA = None


def main(req):
    global CACHED_DATA
    if CACHED_DATA is None:
        CACHED_DATA = load_json()

    # ... use CACHED_DATA in code
```

## <a name="environment-variables"></a>Ortam değişkenleri

Işlevlerde, hizmet bağlantı dizeleri gibi [uygulama ayarları](functions-app-settings.md), yürütme sırasında ortam değişkenleri olarak sunulur. Bu ayarlara, `import os` bildirerek ve sonra `setting = os.environ["setting-name"]`kullanarak erişebilirsiniz.

Aşağıdaki örnekte, `myAppSetting`adlı anahtarla birlikte [uygulama ayarı](functions-how-to-use-azure-function-app-settings.md#settings)alınır:

```python
import logging
import os
import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    # Get the setting named 'myAppSetting'
    my_app_setting_value = os.environ["myAppSetting"]
    logging.info(f'My app setting value:{my_app_setting_value}')
```

Yerel geliştirme için, uygulama ayarları [yerel. Settings. json dosyasında tutulur](functions-run-local.md#local-settings-file).  

## <a name="python-version"></a>Python sürümü 

Şu anda Azure Işlevleri hem Python 3.6. x hem de 3.7. x (resmi cpi dağıtımlarını) destekler. Yerel olarak çalıştırılırken, çalışma zamanı kullanılabilir Python sürümünü kullanır. Azure 'da işlev uygulamanızı oluştururken belirli bir Python sürümü istemek için [`az functionapp create`](/cli/azure/functionapp#az-functionapp-create) komutunun `--runtime-version` seçeneğini kullanın.  

## <a name="package-management"></a>Paket yönetimi

Azure Functions Core Tools veya Visual Studio Code kullanarak yerel olarak geliştirilirken, gerekli paketlerin adlarını ve sürümlerini `requirements.txt` dosyasına ekleyin ve `pip`kullanarak bunları yüklemek. 

Örneğin, PyPI 'den `requests` paketini yüklemek için aşağıdaki gereksinimler dosyası ve PIP komutu kullanılabilir.

```txt
requests==2.19.1
```

```bash
pip install -r requirements.txt
```

## <a name="publishing-to-azure"></a>Azure 'da yayımlama

Yayımlamaya hazır olduğunuzda, tüm genel kullanıma açık bağımlılıklarınızın, proje dizininizin kökünde bulunan requirements. txt dosyasında listelendiğinden emin olun. 

Sanal ortam klasörü de dahil olmak üzere, yayımlamanın dışında tutulan proje dosyaları ve klasörler. funcignore dosyasında listelenir.

Python projenizi Azure 'da yayımlamak için desteklenen üç derleme eylemi vardır:

+ Uzak derleme: bağımlılıklar, requirements. txt dosyasının içeriğine göre uzaktan alınır. [Uzaktan derleme](functions-deployment-technologies.md#remote-build) önerilen derleme yöntemidir. Uzak Ayrıca Azure Araçları 'nın varsayılan derleme seçeneğidir. 
+ Yerel derleme: bağımlılıklar, requirements. txt dosyasının içeriğine göre yerel olarak alınır. 
+ Özel bağımlılıklar: projeniz, araçlarımız için herkese açık olarak kullanılamayan paketler kullanır. (Docker gerektirir.)

Bağımlılıklarınızı derlemek ve sürekli teslim (CD) sistemi kullanarak yayımlamak için [Azure Pipelines kullanın](functions-how-to-azure-devops.md).

### <a name="remote-build"></a>Uzak derleme

Varsayılan olarak, Python projenizi Azure 'da yayımlamak için aşağıdaki [Func Azure functionapp Publish](functions-run-local.md#publish) komutunu kullandığınızda Azure Functions Core Tools uzak bir derlemeyi ister. 

```bash
func azure functionapp publish <APP_NAME>
```

`<APP_NAME>` Azure 'daki işlev uygulamanızın adıyla değiştirmeyi unutmayın.

[Visual Studio Code Için Azure Işlevleri uzantısı](functions-create-first-function-vs-code.md#publish-the-project-to-azure) Ayrıca uzak bir derlemeyi varsayılan olarak ister. 

### <a name="local-build"></a>Yerel derleme

Bir yerel derleme ile yayımlamak için aşağıdaki [Func Azure functionapp Publish](functions-run-local.md#publish) komutunu kullanarak uzak bir derlemeyi engelleyebilirsiniz. 

```command
func azure functionapp publish <APP_NAME> --build local
```

`<APP_NAME>` Azure 'daki işlev uygulamanızın adıyla değiştirmeyi unutmayın. 

`--build local` seçeneğini kullanarak, Proje bağımlılıkları requirements. txt dosyasından okur ve bu bağımlı paketler yerel olarak indirilir ve yüklenir. Proje dosyaları ve bağımlılıklar yerel bilgisayarınızdan Azure 'a dağıtılır. Bu, daha büyük bir dağıtım paketinin Azure 'a yüklenmasına neden olur. Bir nedenden dolayı, requirements. txt dosyanızdaki bağımlılıklar temel araçlar tarafından alınamadığından, yayımlamak için özel bağımlılıklar seçeneğini kullanmanız gerekir. 

### <a name="custom-dependencies"></a>Özel bağımlılıklar

Projeniz araçlarımızda herkese açık değil paketleri kullanıyorsa, bu dosyaları \_\_App\_\_/. python_packages dizinine yerleştirerek uygulamanız için kullanılabilir hale getirebilirsiniz. Yayımlamadan önce, bağımlılıkları yerel olarak yüklemek için aşağıdaki komutu çalıştırın:

```command
pip install  --target="<PROJECT_DIR>/.python_packages/lib/site-packages"  -r requirements.txt
```

Özel bağımlılıklar kullanırken, bağımlılıkları zaten yüklemiş olduğunuz için `--no-build` yayımlama seçeneğini kullanmanız gerekir.  

```command
func azure functionapp publish <APP_NAME> --no-build
```

`<APP_NAME>` Azure 'daki işlev uygulamanızın adıyla değiştirmeyi unutmayın.

## <a name="unit-testing"></a>Birim Testi

Python 'da yazılan işlevler, standart test çerçeveleri kullanılarak diğer Python kodu gibi test edilebilir. Çoğu bağlamanın, `azure.functions` paketinden uygun bir sınıfın örneğini oluşturarak bir sahte giriş nesnesi oluşturmak mümkündür. [`azure.functions`](https://pypi.org/project/azure-functions/) paketi hemen kullanılamadığından, yukarıdaki [Paket Yönetimi](#package-management) bölümünde açıklandığı gibi `requirements.txt` dosyanız aracılığıyla yüklemeyi unutmayın. 

Örneğin, bir HTTP ile tetiklenen bir işlevin sahte testi aşağıdadır:

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

```python
# __app__/HttpTrigger/__init__.py
import azure.functions as func
import logging

def my_function(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

```python
# tests/test_httptrigger.py
import unittest

import azure.functions as func
from __app__.HttpTrigger import my_function

class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock HTTP request.
        req = func.HttpRequest(
            method='GET',
            body=None,
            url='/api/HttpTrigger',
            params={'name': 'Test'})

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp.get_body(),
            b'Hello Test',
        )
```

Sıra tarafından tetiklenen bir işlev içeren başka bir örnek aşağıda verilmiştir:

```json
{
  "scriptFile": "__init__.py",
  "entryPoint": "my_function",
  "bindings": [
    {
      "name": "msg",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "python-queue-items",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

```python
# __app__/QueueTrigger/__init__.py
import azure.functions as func

def my_function(msg: func.QueueMessage) -> str:
    return f'msg body: {msg.get_body().decode()}'
```

```python
# tests/test_queuetrigger.py
import unittest

import azure.functions as func
from __app__.QueueTrigger import my_function

class TestFunction(unittest.TestCase):
    def test_my_function(self):
        # Construct a mock Queue message.
        req = func.QueueMessage(
            body=b'test')

        # Call the function.
        resp = my_function(req)

        # Check the output.
        self.assertEqual(
            resp,
            'msg body: test',
        )
```
## <a name="temporary-files"></a>Geçici dosyalar

`tempfile.gettempdir()` yöntemi, Linux üzerinde `/tmp`olan geçici bir klasör döndürür. Uygulamanız, yürütme sırasında işlevleriniz tarafından oluşturulan ve kullanılan geçici dosyaları depolamak için bu dizini kullanabilir. 

> [!IMPORTANT]
> Geçici dizine yazılan dosyaların, etkinleştirmeleri arasında kalıcı hale getirilmesi garanti edilmez. Genişleme sırasında, geçici dosyalar örnekler arasında paylaşılmaz. 

Aşağıdaki örnek geçici dizinde (`/tmp`) adlandırılmış geçici bir dosya oluşturur:

```python
import logging
import azure.functions as func
import tempfile
from os import listdir

#---
   tempFilePath = tempfile.gettempdir()   
   fp = tempfile.NamedTemporaryFile()     
   fp.write(b'Hello world!')              
   filesDirListInTemp = listdir(tempFilePath)     
```   

Testlerinizi proje klasöründen ayrı bir klasörde tutmanızı öneririz. Bu, uygulamanıza test kodu dağıtmanızı önler. 

## <a name="known-issues-and-faq"></a>Bilinen sorunlar ve SSS

Tüm bilinen sorunlar ve özellik istekleri [GitHub sorunları](https://github.com/Azure/azure-functions-python-worker/issues) listesi kullanılarak izlenir. Bir sorunla karşılaşırsanız ve sorunu GitHub 'da bulamazsanız, yeni bir sorun açın ve sorunun ayrıntılı bir açıklamasını ekleyin.

### <a name="cross-origin-resource-sharing"></a>Çıkış noktaları arası kaynak paylaşma

Azure Işlevleri, çıkış noktaları arası kaynak paylaşımını (CORS) destekler. CORS, [portalda](functions-how-to-use-azure-function-app-settings.md#cors) ve [Azure CLI](/cli/azure/functionapp/cors)aracılığıyla yapılandırılır. CORS izin verilen kaynaklar listesi, işlev uygulaması düzeyinde geçerlidir. CORS etkinken, yanıtlar `Access-Control-Allow-Origin` üst bilgisini içerir. Daha fazla bilgi için bkz. [Çıkış noktaları arası kaynak paylaşma](functions-how-to-use-azure-function-app-settings.md#cors).

İzin verilen çıkış noktaları listesi [Şu anda](https://github.com/Azure/azure-functions-python-worker/issues/444) Python işlev uygulamaları için desteklenmemektedir. Bu sınırlama nedeniyle, aşağıdaki örnekte gösterildiği gibi HTTP işlevlerinizin `Access-Control-Allow-Origin` üst bilgisini açıkça ayarlamanız gerekir:

```python
def main(req: func.HttpRequest) -> func.HttpResponse:

    # Define the allow origin headers.
    headers = {"Access-Control-Allow-Origin": "https://contoso.com"}

    # Set the headers in the response.
    return func.HttpResponse(
            f"Allowed origin '{headers}'.",
            headers=headers, status_code=200
    )
``` 

OPTIONS HTTP yöntemini desteklemek için function. JSON dosyanızı da güncelleştirdiğinizden emin olun:

```json
    ...
      "methods": [
        "get",
        "post",
        "options"
      ]
    ...
```

Bu yöntem, Chrome tarayıcısı tarafından izin verilen çıkış noktaları listesine anlaşmak için kullanılır. 

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki kaynaklara bakın:

* [Azure Işlevleri paket API 'SI belgeleri](/python/api/azure-functions/azure.functions?view=azure-python)
* [Azure İşlevleri için en iyi uygulamalar](functions-best-practices.md)
* [Azure Işlevleri Tetikleyicileri ve bağlamaları](functions-triggers-bindings.md)
* [BLOB depolama bağlamaları](functions-bindings-storage-blob.md)
* [HTTP ve Web kancası bağlamaları](functions-bindings-http-webhook.md)
* [Kuyruk depolama bağlamaları](functions-bindings-storage-queue.md)
* [Zamanlayıcı tetikleyicisi](functions-bindings-timer.md)


[HttpRequest]: /python/api/azure-functions/azure.functions.httprequest?view=azure-python
[HttpResponse]: /python/api/azure-functions/azure.functions.httpresponse?view=azure-python
