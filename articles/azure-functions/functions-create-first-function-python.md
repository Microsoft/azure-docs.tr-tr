---
title: Azure 'da HTTP tarafından tetiklenen bir Python işlevi oluşturma
description: Azure işlevleri çekirdek araçları ve Azure CLI kullanarak Azure'da ilk Python uygulamanızı oluşturma konusunda bilgi edinin.
author: ggailey777
ms.author: glenga
ms.date: 11/07/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: gwallace
ms.openlocfilehash: 61465177c98a31a739946097ca615382175df3d4
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082765"
---
# <a name="quickstart-create-an-http-triggered-python-function-in-azure"></a>Hızlı başlangıç: Azure 'da HTTP tarafından tetiklenen bir Python işlevi oluşturma

Bu makalede, Azure Işlevleri 'nde çalışan bir Python projesi oluşturmak için komut satırı araçlarının nasıl kullanılacağı gösterilmektedir. Ayrıca, bir HTTP isteği tarafından tetiklenen bir işlev oluşturursunuz. Yerel olarak çalıştırdıktan sonra, projenizi Azure 'da [sunucusuz bir işlev](functions-scale.md#consumption-plan) olarak çalışacak şekilde yayımlayın. 

Bu makale, Azure Işlevleri için iki Python hızlı başlangıçdan birincisi. Bu hızlı başlangıcı tamamladıktan sonra, işleviniz için [bir Azure depolama kuyruğu çıkışı bağlama ekleyebilirsiniz](functions-add-output-binding-storage-queue-python.md) .

Bu makalenin [Visual Studio Code tabanlı bir sürümü](/azure/python/tutorial-vs-code-serverless-python-01) de vardır.

## <a name="prerequisites"></a>Önkoşullar

Başlamadan önce şunları yapmanız gerekir:

+ [Python 3.7.4](https://www.python.org/downloads/)'i yükler. Python 'un bu sürümü Işlevlerle doğrulandı. Python 3,8 ve sonraki sürümler henüz desteklenmiyor.

+ [Azure Functions Core Tools](./functions-run-local.md#v2) sürüm 2.7.1846 veya sonraki bir sürümü yükler.

+ [Azure CLI](/cli/azure/install-azure-cli) sürüm 2.0.76 veya sonraki bir sürümünü yükler.

+ Etkin bir Azure aboneliğiniz olmalıdır.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>Oluşturma ve bir sanal ortam etkinleştirin

Python işlevlerini yerel olarak geliştirmek için bir Python 3,7 ortamı kullanmanız gerekir. Oluşturma ve adlı bir sanal ortam etkinleştirmek için aşağıdaki komutları çalıştırın `.venv`.

> [!NOTE]
> Python, Linux dağıtımına venv yüklemediyse, aşağıdaki komutu kullanarak yükleyebilirsiniz:
> ```command
> sudo apt-get install python3-venv

### <a name="bash"></a>Bash

```bash
python -m venv .venv
source .venv/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>PowerShell veya bir Windows komut istemi:

```powershell
py -m venv .venv
.venv\scripts\activate
```

Artık sanal ortamı etkinleştirdikten sonra, içinde kalan komutları çalıştırın. Sanal ortamdan yararlanmak için `deactivate`çalıştırın.

## <a name="create-a-local-functions-project"></a>Yerel işlevler projesi oluşturma

İşlevler projesinde, hepsi aynı yerel ve barındırma yapılandırmalarının paylaştığı birden çok işlev olabilir.

Sanal ortamda aşağıdaki komutları çalıştırın:

```console
func init MyFunctionProj --python
cd MyFunctionProj
```

`func init` komutu bir _Myfunctionproj_ klasörü oluşturur. Bu klasördeki Python projesinde hiç işlev yok. Bunları daha sonra ekleyeceksiniz.

## <a name="create-a-function"></a>İşlev oluşturma

Projenize bir işlev eklemek için aşağıdaki komutu çalıştırın:

```console
func new --name HttpTrigger --template "HTTP trigger"
```

Bu komutlar, aşağıdaki dosyaları içeren _Httptrigger_adlı bir alt klasör oluşturur:

* *function. JSON*: işlevi, tetikleyiciyi ve diğer bağlamaları tanımlayan yapılandırma dosyası. Bu dosyada, `scriptFile` değerinin işlevi içeren dosyayı işaret ettiğini ve `bindings` dizisinin çağırma tetikleyicisi ve bağlamalarını tanımladığından emin olun.

    Her bağlama bir yön, tür ve benzersiz bir ad gerektirir. HTTP tetikleyicisinin tür [`httpTrigger`](functions-bindings-http-webhook.md#trigger) ve [`http`](functions-bindings-http-webhook.md#output)türünde çıkış bağlaması olan bir giriş bağlaması vardır.

* *\_\_init\_\_. Kopyala*: http ile tetiklenen işleviniz olan betik dosyası. Bu betiğin varsayılan bir `main()`olduğunu unutmayın. Tetikleyiciden gelen HTTP verileri, `binding parameter`adlı `req` kullanarak işleve geçer. Function. json ' da tanımlanan `req`, [Azure. Functions. HttpRequest sınıfının](/python/api/azure-functions/azure.functions.httprequest)bir örneğidir. 

    *Function. JSON*içinde `$return` olarak tanımlanan Return nesnesi, [Azure. Functions. HttpResponse sınıfının](/python/api/azure-functions/azure.functions.httpresponse)bir örneğidir. Daha fazla bilgi için bkz. [Azure IŞLEVLERI http Tetikleyicileri ve bağlamaları](functions-bindings-http-webhook.md).

Şimdi yerel bilgisayarınızda yeni işlevi çalıştırabilirsiniz.

## <a name="run-the-function-locally"></a>İşlevi yerel olarak çalıştırma

Bu komut, işlev uygulamasını Azure Işlevleri çalışma zamanı (Func. exe) kullanarak başlatır:

```console
func host start
```

Çıkışa yazılan aşağıdaki bilgileri görmeniz gerekir:

```output
Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger    
```

`HttpTrigger` işlevinizin URL 'sini bu çıktıdan kopyalayın ve tarayıcınızın adres çubuğuna yapıştırın. `?name=<yourname>` sorgu dizesini bu URL’ye ekleyip isteği yürütün. Aşağıdaki ekran görüntüsünde, yerel işlevin tarayıcıya döndürdüğü GET isteğine olan yanıt gösterilmektedir:

![Tarayıcıda yerel olarak doğrula](./media/functions-create-first-function-python/function-test-local-browser.png)

İşlev uygulaması yürütmeyi kapatmak için CTRL + C tuşlarını kullanın.

İşlevinizi yerel olarak çalıştırdığınıza göre, işlev kodunuzu Azure 'a dağıtabilirsiniz.  
Uygulamanızı dağıtabilmeniz için önce bazı Azure kaynakları oluşturmanız gerekir.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Azure 'da bir işlev uygulaması oluşturma

İşlev uygulaması, işlev kodunuzun yürütülmesi için bir ortam sağlar. Bu sayede daha kolay yönetilmesi, dağıtım ve kaynakların paylaşımı için bir mantıksal birim olarak gruplandırmanıza işlevleri. 

Aşağıdaki komutu çalıştırın. `<APP_NAME>` benzersiz bir işlev uygulama adıyla değiştirin. `<STORAGE_NAME>` bir depolama hesabı adıyla değiştirin. `<APP_NAME>` aynı zamanda işlev uygulamasının varsayılan DNS etki alanıdır. Bu ad Azure'daki tüm uygulamalar arasında benzersiz olmalıdır.

> [!NOTE]
> Linux ve Windows uygulamalarını aynı kaynak grubunda barındıralamazsınız. Bir Windows işlev uygulaması veya Web uygulamasıyla `myResourceGroup` adlı mevcut bir kaynak grubunuz varsa, farklı bir kaynak grubu kullanmanız gerekir.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python --runtime-version 3.7 \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

Yukarıdaki komut, Python 3.7.4 çalıştıran bir işlev uygulaması oluşturur. Aynı zamanda aynı kaynak grubundaki ilişkili bir Azure Application Insights örneğini sağlar. Bu örneği, işlev uygulamanızı izlemek ve günlükleri görüntülemek için kullanabilirsiniz. 

Artık yerel işlevler projenizi Azure 'daki işlev uygulamasına yayımlamaya hazırsınız.

## <a name="deploy-the-function-app-project-to-azure"></a>İşlev uygulaması projesini Azure'a dağıtma

Azure 'da işlev uygulaması oluşturduktan sonra, proje kodunuzu Azure 'a dağıtmak için [Func Azure functionapp Publish](functions-run-local.md#project-file-deployment) Core araçları komutunu kullanabilirsiniz. Bu örnekte `<APP_NAME>` değerini uygulamanızın adıyla değiştirin.

```console
func azure functionapp publish <APP_NAME> --build remote
```

`--build remote` seçeneği, Python projenizi Azure 'da dağıtım paketindeki dosyalardan uzaktan oluşturur ve bu önerilir. 

Aşağıdaki iletiye benzer bir çıktı görürsünüz. Buradan daha iyi okuyabilmeniz için bu atılır:

```output
Getting site publishing info...
...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
Functions in myfunctionapp:
    HttpTrigger - [httpTrigger]
        Invoke url: https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....
```

`HttpTrigger` için `Invoke url` değerini kopyalayabilir ve Azure 'da işlevinizi doğrulamak için kullanabilirsiniz. URL, işlev anahtarınız olan `code` bir sorgu dizesi değeri içerir, bu da başkalarının Azure 'da HTTP tetikleyici uç noktanızı aramasını zorlaştırır.

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

> [!NOTE]
> Yayımlanmış bir Python uygulaması için neredeyse gerçek zamanlı günlükleri görüntülemek için [Application Insights canlı ölçüm akışı](functions-monitoring.md#streaming-logs)kullanın.

## <a name="next-steps"></a>Sonraki adımlar

HTTP ile tetiklenen bir işlev içeren bir Python işlevleri projesi oluşturdunuz, yerel makinenizde çalıştırın ve Azure 'a dağıttınız. Şimdi, işlevinizi genişletin...

> [!div class="nextstepaction"]
> [Azure depolama kuyruğu çıkış bağlaması ekleme](functions-add-output-binding-storage-queue-python.md)
