---
title: Python işleviniz için bir Azure depolama kuyruğu bağlama ekleme
description: Python işlevinizin bir Azure depolama kuyruğu çıkışı bağlamayı nasıl ekleyeceğinizi öğrenin.
author: ggailey777
ms.author: glenga
ms.date: 10/02/2019
ms.topic: quickstart
ms.service: azure-functions
manager: gwallace
ms.openlocfilehash: da3fb604bfb65f67e50d56a4520620cabc292b93
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082814"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Python işleviniz için bir Azure depolama kuyruğu bağlama ekleme

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Bu makalede, [önceki hızlı başlangıç makalesinde](functions-create-first-function-python.md) oluşturduğunuz Işlevi bir Azure depolama kuyruğu ile tümleştirme işlemi gösterilmektedir. Bu işleve eklediğiniz çıkış bağlaması, verileri bir HTTP isteğinden kuyruktaki bir iletiye yazar.

Çoğu bağlamanın, bağlı hizmete erişmek için kullandığı depolanan bir bağlantı dizesi gerekir. Bu bağlantıyı daha kolay hale getirmek için, işlev uygulamanızla oluşturduğunuz depolama hesabını kullanırsınız. Bu hesap bağlantısı zaten `AzureWebJobsStorage`adlı bir uygulama ayarında depolanıyor.  

## <a name="prerequisites"></a>Önkoşullar

Bu makaleye başlamadan önce, [Python hızlı](functions-create-first-function-python.md)başlangıcının 1. bölümündeki adımları uygulayın.

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="download-the-function-app-settings"></a>İşlev uygulaması ayarlarını indirin

[!INCLUDE [functions-app-settings-download-cli](../../includes/functions-app-settings-download-local-cli.md)]

## <a name="enable-extension-bundles"></a>Uzantı paketlerini etkinleştir

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

Artık, depolama çıkış bağlamasını projenize ekleyebilirsiniz.

## <a name="add-an-output-binding"></a>Çıktı bağlaması ekleme

Işlevlerde, her bağlama türü, function. json dosyasında tanımlanması için `direction`, `type`ve benzersiz bir `name` gerektirir. Bu öznitelikleri tanımlama yöntemi, işlev uygulamanızın diline bağlıdır.

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Çıkış bağlaması kullanan kod ekleme

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

Çıkış bağlamayı kullandığınızda, kimlik doğrulaması, kuyruk başvurusu alma veya veri yazma için Azure depolama SDK kodunu kullanmanız gerekmez. Işlevler çalışma zamanı ve kuyruk çıkış bağlaması bu görevleri sizin için işler.

## <a name="run-the-function-locally"></a>İşlevi yerel olarak çalıştırma

Daha önce olduğu gibi, Işlevleri çalışma zamanını yerel olarak başlatmak için aşağıdaki komutu kullanın:

```bash
func host start
```

> [!NOTE]  
> Host. JSON içindeki uzantı paketlerini etkinleştirdiyseniz, [depolama bağlama uzantısı](functions-bindings-storage-blob.md#packages---functions-2x) diğer Microsoft bağlama uzantılarıyla birlikte başlangıç sırasında sizin için indirilir ve yüklenir.

Çalışma zamanı çıktısından `HttpTrigger` işlevinizin URL’sini kopyalayın ve tarayıcınızın adres çubuğuna yapıştırın. Sorgu dizesini bu URL 'ye `?name=<yourname>` ekleyin ve isteği çalıştırın. Önceki makalede yaptığınız gibi tarayıcıda aynı yanıtı görmeniz gerekir.

Bu kez, çıkış bağlama aynı zamanda depolama hesabınızda `outqueue` adlı bir kuyruk oluşturur ve bu dizeyi içeren bir ileti ekler.

Ardından, yeni kuyruğu görüntülemek ve bir iletinin eklendiğini doğrulamak için Azure CLı 'yi kullanırsınız. Kuyruğunuzu, [Microsoft Azure Depolama Gezgini][Azure Storage Explorer] veya [Azure Portal](https://portal.azure.com)kullanarak da görüntüleyebilirsiniz.

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

### <a name="redeploy-the-project"></a>Projeyi yeniden dağıtın 

Yayınlanan uygulamanızı güncelleştirmek için [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) çekirdek araçları komutunu kullanarak proje kodunuzu Azure 'a dağıtın. Bu örnekte `<APP_NAME>` değerini uygulamanızın adıyla değiştirin.

```command
func azure functionapp publish <APP_NAME> --build remote
```

Yine, dağıtılan işlevi test etmek için kıvrımlı veya tarayıcı kullanabilirsiniz. Daha önce olduğu gibi, aşağıdaki örnekte olduğu gibi sorgu dizesini URL 'ye `&name=<yourname>` ekleyin:

```bash
curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
```

Çıkış bağlamasının, kuyrukta beklendiği gibi yeni bir ileti üretdiğini doğrulamak için [depolama kuyruğu iletisini yeniden inceleyebilirsiniz](#query-the-storage-queue) .

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Sonraki adımlar

HTTP ile tetiklenen işlevinizi bir depolama kuyruğuna veri yazmak için güncelleştirdiniz. Python ile Azure Işlevleri geliştirme hakkında daha fazla bilgi için bkz. [Azure Işlevleri Python Geliştirici Kılavuzu](functions-reference-python.md) ve [Azure işlevleri Tetikleyicileri ve bağlamaları](functions-triggers-bindings.md). Python 'da tüm Işlev projelerinin örnekleri için bkz. [Python işlevleri örnekleri](/samples/browse/?products=azure-functions&languages=python). Fiyatlandırma hakkında daha fazla bilgi edinmek için bkz. [işlevler fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/functions/) ve [Tüketim planı maliyetlerini tahmin](functions-consumption-costs.md) etme makalesi.

Sonra, işlev uygulamanız için Application Insights izlemeyi etkinleştirmelisiniz:

> [!div class="nextstepaction"]
> [Application Insights tümleştirmesini etkinleştirme](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/