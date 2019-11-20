---
title: Visual Studio kullanarak Azure Işlevleri geliştirme | Microsoft Docs
description: Visual Studio 2019 için Azure Işlevleri araçlarını kullanarak Azure Işlevleri geliştirmeyi ve test yapmayı öğrenin.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: glenga
ms.openlocfilehash: ebc900735dfbb25206c4b22e3d20da62d85c61df
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773156"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Visual Studio kullanarak Azure Işlevleri geliştirme  

Visual Studio, Azure 'Da sınıf kitaplığı işlevleri geliştirmenize, C# test etmenize ve dağıtmanıza olanak sağlar. Azure Işlevleri ile ilk kez bu deneyim varsa, [Azure işlevleri 'ne giriş](functions-overview.md)konusunda daha fazla bilgi edinebilirsiniz.

Visual Studio, işlevlerinizi geliştirirken aşağıdaki avantajları sağlar: 

* Yerel geliştirme bilgisayarınızda işlevleri düzenleyin, derleyin ve çalıştırın. 
* Azure Işlevleri projenizi doğrudan Azure 'da yayımlayın ve gerektiğinde Azure kaynakları oluşturun. 
* İşlev C# bağlamalarını doğrudan C# kodda bildirmek için öznitelikleri kullanın.
* Önceden derlenmiş C# işlevler geliştirin ve dağıtın. Önceden karmaşıklu işlevler, C# komut dosyası tabanlı işlevlerden daha iyi bir soğuk başlama performansı sağlar. 
* Visual Studio geliştirmenin avantajlarından C# yararlanmak için işlevlerinizi ' de kodlayın. 

Bu makalede, Visual Studio kullanarak sınıf kitaplığı işlevleri geliştirmeye C# ve bunları Azure 'da yayımlamaya ilişkin ayrıntılar sağlanmaktadır. Bu makaleyi okuduktan sonra [Visual Studio için hızlı başlangıç işlevleri](functions-create-your-first-function-visual-studio.md)' ni tamamlamalısınız. 

Aksi belirtilmediği takdirde, yordamlar ve örnekler Visual Studio 2019 içindir. 

## <a name="prerequisites"></a>Önkoşullar

Azure Işlevleri araçları, Visual Studio 'nun Azure geliştirme iş yüküne Visual Studio 2017 ile başlayarak dahildir. **Azure geliştirme** Iş yükünü Visual Studio yüklemenize eklediğinizden emin olun.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Azure depolama hesabı gibi ihtiyacınız olan diğer kaynaklar, yayımlama işlemi sırasında aboneliğinizde oluşturulur.

> [!NOTE]
> Visual Studio 2017 ' de Azure geliştirme iş yükü, Azure Işlevleri araçlarını ayrı bir uzantı olarak yüklüyor. Visual Studio 2017 ' yi güncelleştirdiğinizde Azure Işlevleri araçlarının [en son sürümünü](#check-your-tools-version) kullandığınızdan da emin olun. Aşağıdaki bölümlerde, Visual Studio 2017 ' de Azure Işlevleri araçları uzantınızı nasıl denetleyen ve (gerekirse) güncelleştiren gösterilmektedir. 
>
> Visual Studio 2019 kullanırken lütfen bu bölümü atlayın.

### <a name="check-your-tools-version"></a>Visual Studio 2017 ' de Araçlar sürümünüzü denetleyin

1. Gelen **Araçları** menüsünde seçin **Uzantılar ve güncelleştirmeler**. **Yüklü** > **Araçlar** ' ı genişletin ve **Azure işlevleri ve Web işleri Araçları '** nı seçin.

    ![Işlevler araçları sürümünü doğrulama](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. Yüklü **sürümü**göz önünde edin. Bu sürümü sürüm [notlarında](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md)listelenen en son sürümle karşılaştırabilirsiniz. 

1. Sürümünüzün eski ise, Visual Studio Araçları aşağıdaki bölümde gösterildiği gibi güncelleştirin.

### <a name="update-your-tools-in-visual-studio-2017"></a>Visual Studio 2017 ' de araçlarınızı güncelleştirme

1. **Uzantılar ve güncelleştirmeler** iletişim kutusunda **güncelleştirmeler** > **Visual Studio Market**' ı genişletin, **Azure işlevleri ve Web işleri araçları** ' nı seçin ve **Güncelleştir**' i seçin.

    ![Işlevler araçları sürümünü güncelleştirme](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. Araçları güncelleştirme yüklendikten sonra Visual Studio Araçları VSIX Yükleyicisi'ni kullanarak güncelleştirme tetikleyiciye kapatın.

1. Yükleyicide başlamak için **Tamam** ' ı seçin ve ardından araçları güncelleştirmek için **değiştirin** . 

1. Güncelleştirme tamamlandıktan sonra, **Kapat** ' ı seçin ve Visual Studio 'yu yeniden başlatın.

> [!NOTE]  
Visual Studio 2019 ve üzeri sürümlerde Azure Işlevleri araçları uzantısı, Visual Studio 'nun bir parçası olarak güncelleştirilir.  

## <a name="create-an-azure-functions-project"></a>Azure İşlevleri projesi oluşturma

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Proje şablonu bir C# proje oluşturur, `Microsoft.NET.Sdk.Functions` NuGet paketini yüklerse ve hedef Framework 'ü ayarlar. Yeni proje aşağıdaki dosyalara sahiptir:

* **Host. JSON**: Işlevleri konağını yapılandırmanıza olanak tanır. Bu ayarlar, hem yerel olarak hem de Azure 'da çalışırken geçerlidir. Daha fazla bilgi için bkz. [Host. JSON başvurusu](functions-host-json.md).

* **Local. Settings. JSON**: İşlevleri yerel olarak çalıştırırken kullanılan ayarları korur. Bu ayarlar, Azure 'da çalışırken kullanılmaz. Daha fazla bilgi için bkz. [yerel ayarlar dosyası](#local-settings-file).

    >[!IMPORTANT]
    >Local. Settings. JSON dosyası gizli dizileri içerebildiğinden, bunu proje kaynak denetiinizden dışmalısınız. Bu dosya için **Çıkış Dizinine Kopyala** ayarı, **daha yeniyse**her zaman kopya olmalıdır. 

Daha fazla bilgi için bkz. [işlevler sınıf kitaplığı projesi](functions-dotnet-class-library.md#functions-class-library-project).

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Projeyi yayımladığınızda yerel. Settings. JSON ayarları otomatik olarak karşıya yüklenemez. Bu ayarların Azure 'daki işlev uygulamanızda da mevcut olduğundan emin olmak için, projenizi yayımladıktan sonra bunları karşıya yüklemeniz gerekir. Daha fazla bilgi için bkz. [işlev uygulaması ayarları](#function-app-settings).

**ConnectionString** içindeki değerler hiçbir şekilde yayımlanmaz.

İşlev uygulaması ayarları değerleri, kodunuzda ortam değişkenleri olarak da okunabilir. Daha fazla bilgi için bkz. [ortam değişkenleri](functions-dotnet-class-library.md#environment-variables).

## <a name="configure-the-project-for-local-development"></a>Projeyi yerel geliştirme için yapılandırma

Işlevler çalışma zamanı, dahili olarak bir Azure depolama hesabı kullanır. HTTP ve Web kancaları dışındaki tüm tetikleyici türleri için **values. AzureWebJobsStorage** anahtarını geçerli bir Azure depolama hesabı bağlantı dizesine ayarlamanız gerekir. İşlev uygulamanız, proje için gerekli olan **AzureWebJobsStorage** bağlantı ayarı için [Azure Storage öykünücüsü](../storage/common/storage-use-emulator.md) 'nü de kullanabilir. Öykünücüyü kullanmak için **AzureWebJobsStorage** değerini olarak `UseDevelopmentStorage=true`ayarlayın. Dağıtımdan önce bu ayarı gerçek bir depolama hesabı bağlantı dizesiyle değiştirin.

Depolama hesabı bağlantı dizesini ayarlamak için:

1. Visual Studio 'da **Cloud Explorer**'ı açın, depolama **hesabı** > **depolama hesabınızı genişletin, ardından** **Özellikler** sekmesinde **birincil bağlantı dizesi** değerini kopyalayın.

2. Projenizde, Local. Settings. json dosyasını açın ve **AzureWebJobsStorage** anahtarının değerini kopyaladığınız bağlantı dizesine ayarlayın.

3. İşlevlerinizin gerektirdiği diğer tüm bağlantılar için **Values** dizisine benzersiz anahtarlar eklemek için önceki adımı tekrarlayın. 

## <a name="add-a-function-to-your-project"></a>Projenize bir işlev ekleyin

C# Sınıf kitaplığı işlevlerinde, işlev tarafından kullanılan bağlamalar koddaki öznitelikler uygulanarak tanımlanır. İşlev tetiklerinizi, belirtilen şablonlardan oluşturduğunuzda, tetikleyici öznitelikleri sizin için uygulanır. 

1. **Çözüm Gezgini**’nde, proje düğümünüze sağ tıklayın ve **Yeni** > **Öğe Ekle**’yi seçin. **Azure işlevi**' ni seçin, sınıf Için bir **ad** yazın ve **Ekle**' ye tıklayın.

2. Tetikleyiciyi seçin, bağlama özelliklerini ayarlayın ve **Oluştur**' a tıklayın. Aşağıdaki örnek, kuyruk depolama tarafından tetiklenen bir işlev oluşturulurken ayarları gösterir. 

    ![Kuyruğa geçirilmiş bir işlev oluşturma](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    Bu tetikleyici örneği, **Queuesstorage**adlı bir anahtarla bağlantı dizesi kullanır. Bu bağlantı dizesi ayarı [yerel. Settings. json dosyasında](functions-run-local.md#local-settings-file)tanımlanmalıdır.

3. Yeni eklenen sınıfı inceleyin. **Fonksiyonadı** özniteliğiyle nitelendirilen bir statik **Run** yöntemi görürsünüz. Bu öznitelik, yönteminin işlevin giriş noktası olduğunu gösterir.

    Örneğin, aşağıdaki C# sınıf temel bir sıra depolama tetiklenen işlevi temsil eder:

    ```csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.Extensions.Logging;

    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]
            public static void Run([QueueTrigger("myqueue-items", 
                Connection = "QueueStorage")]string myQueueItem, ILogger log)
            {
                log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    }
    ```

    Giriş noktası yöntemine sağlanan her bağlama parametresine bağlamaya özgü bir öznitelik uygulanır. Öznitelik, bağlama bilgilerini parametre olarak alır. Önceki örnekte, ilk parametrenin bir **Queuetrigger** özniteliği uygulanmış ve Queue tetiklenen işlevi olduğunu gösterir. Sıra adı ve bağlantı dizesi ayarı adı, **Queuetrigger** özniteliğine parametre olarak geçirilir. Daha fazla bilgi için bkz. [Azure Için Azure kuyruk depolama bağlamaları işlevleri](functions-bindings-storage-queue.md#trigger---c-example).

İşlev uygulaması projenize daha fazla işlev eklemek için yukarıdaki yordamı kullanabilirsiniz. Projedeki her bir işlev farklı bir tetikleyicisine sahip olabilir, ancak bir işlevin tam olarak bir tetikleyicisi olmalıdır. Daha fazla bilgi için bkz. [Azure işlevleri Tetikleyicileri ve bağlamaları kavramları](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Bağlama Ekle

Tetikleyiciler 'de olduğu gibi, giriş ve çıkış bağlamaları işlevinizde bağlama öznitelikleri olarak eklenir. Bir işleve şu şekilde bağlama ekleyin:

1. [Projeyi yerel geliştirme için yapılandırdığınızdan](#configure-the-project-for-local-development)emin olun.

2. Belirli bağlama için uygun NuGet uzantı paketini ekleyin. Daha fazla bilgi için Tetikleyiciler ve bağlamalar makalesindeki [Visual Studio 'yu kullanarak C# yerel geliştirme](./functions-bindings-register.md#local-csharp) bölümüne bakın. Bağlamaya özgü NuGet paketi gereksinimleri, bağlamanın başvuru makalesinde bulunur. Örneğin, [Event Hubs bağlama başvurusu makalesindeki](functions-bindings-event-hubs.md)Event Hubs tetikleyicisinin paket gereksinimlerini bulun.

3. Bağlamanın ihtiyacı olan uygulama ayarları varsa, bunları [yerel ayar dosyasındaki](functions-run-local.md#local-settings-file) **değerler** koleksiyonuna ekleyin. Bu değerler, işlev yerel olarak çalıştırıldığında kullanılır. İşlev, Azure 'daki işlev uygulamasında çalıştırıldığında, [işlev uygulama ayarları](#function-app-settings) kullanılır.

4. Yöntem imzasına uygun bağlama özniteliğini ekleyin. Aşağıdaki örnekte, bir kuyruk iletisi işlevi tetikler ve çıkış bağlaması farklı bir kuyrukta aynı metne sahip yeni bir kuyruk iletisi oluşturur.

    ```csharp
    public static class SimpleExampleWithOutput
    {
        [FunctionName("CopyQueueMessage")]
        public static void Run(
            [QueueTrigger("myqueue-items-source", Connection = "AzureWebJobsStorage")] string myQueueItem, 
            [Queue("myqueue-items-destination", Connection = "AzureWebJobsStorage")] out string myQueueItemCopy,
            ILogger log)
        {
            log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
            myQueueItemCopy = myQueueItem;
        }
    }
    ```
   Kuyruk depolama bağlantısı, `AzureWebJobsStorage` ayarından elde edilir. Daha fazla bilgi için, belirli bağlama yönelik başvuru makalesine bakın. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>İşlevleri test etme

Azure İşlevleri Temel Araçları, Azure İşlevleri projenizi yerel geliştirme bilgisayarınızda çalıştırmanıza olanak sağlar. Visual Studio'da ilk kez bir işlev başlattığınızda bu araçları yüklemeniz istenir.

İşlevinizi test etmek için F5’e basın. İstenirse Visual Studio'dan gelen Azure İşlevleri Temel (CLI) araçlarını indirme ve yükleme isteğini kabul edin. Aracın HTTP isteklerini işleyebilmesi için bir güvenlik duvarı özel durumu etkinleştirmeniz de gerekebilir.

Çalıştıran proje ile kodunuzu test dağıtılan işlev olarak test edebilirsiniz. Daha fazla bilgi için bkz. [Azure işlevlerinde kodunuzu test etme stratejileri](functions-test-a-function.md). Hata ayıklama modunda çalışırken, kesme noktaları, Visual Studio 'Nun beklendiği şekilde isabet ediyor. 

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->

Azure Functions Core Tools kullanma hakkında daha fazla bilgi edinmek için bkz. [Azure işlevlerini yerel olarak test](functions-run-local.md)etme.

## <a name="publish-to-azure"></a>Azure'a Yayımlama

Visual Studio 'dan yayımlarken, iki dağıtım yönteminden biri kullanılır:

* [Web dağıtımı](functions-deployment-technologies.md#web-deploy-msdeploy): Windows uygulamalarını paketler ve HERHANGI bir IIS sunucusuna dağıtır.
* [Paket Çalıştır etkin olan ZIP dağıtımı](functions-deployment-technologies.md#zip-deploy): Azure işlevleri dağıtımları için önerilir.

Projenizi Azure 'da bir işlev uygulamasına yayımlamak için aşağıdaki adımları kullanın.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>İşlev uygulaması ayarları

Yerel. Settings. JSON içine eklediğiniz tüm ayarlar ayrıca Azure 'daki işlev uygulamasına eklenmelidir. Projeyi yayımladığınızda bu ayarlar otomatik olarak karşıya yüklenemez.

Gerekli ayarları Azure 'daki işlev uygulamanıza yüklemenin en kolay yolu, projenizi başarıyla yayımladıktan sonra görüntülenen **uygulama ayarlarını yönet...** bağlantısını kullanmaktır.

![](./media/functions-develop-vs/functions-vstools-app-settings.png)

Bu, yeni uygulama ayarları ekleyebileceğiniz veya var olanları değiştirebileceğiniz işlev uygulaması için **uygulama ayarları** iletişim kutusunu görüntüler.

![](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**Local** , yerel. Settings. JSON dosyasındaki bir ayar değerini temsil eder ve **uzak** , Azure 'daki işlev uygulamasındaki geçerli ayardır.  Yeni bir uygulama ayarı oluşturmak için **Ayar ekle** ' yi seçin. Bir ayar değerini **uzak** alana kopyalamak için **Yerel bağlantıdan Ekle değerini** kullanın. Bekleyen değişiklikler, **Tamam**' ı seçtiğinizde yerel ayarlar dosyasına ve işlev uygulamasına yazılır.

> [!NOTE]
> Varsayılan olarak, Local. Settings. JSON dosyası kaynak denetimine iade edilmez. Bu, kaynak denetiminden yerel bir Işlevler projesi kopyaladığınızda, projenin bir Local. Settings. JSON dosyasına sahip olmadığı anlamına gelir. Bu durumda, **uygulama ayarları** iletişim kutusunun beklendiği gibi çalışması için, proje kökünde yerel. Settings. json dosyasını el ile oluşturmanız gerekir. 

Ayrıca, uygulama ayarlarını şu diğer yollarla da yönetebilirsiniz:

* [Azure Portal kullanma](functions-how-to-use-azure-function-app-settings.md#settings).
* [Azure Functions Core Tools Yayımlaseçeneğinikullanma`--publish-local-settings` ](functions-run-local.md#publish).
* [Azure CLI 'Yı kullanma](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

## <a name="monitoring-functions"></a>İzleme işlevleri

İşlevlerinizin yürütülmesini izlemek için önerilen yol, işlev uygulamanızı Azure Application Insights ile tümleştirerek yapılır. Azure portal bir işlev uygulaması oluşturduğunuzda, bu tümleştirme varsayılan olarak sizin için yapılır. Ancak, Visual Studio yayımlama sırasında işlev uygulamanızı oluşturduğunuzda, Azure 'daki işlev uygulamanızda tümleştirme yapılmaz.

İşlev uygulamanız için Application Insights etkinleştirmek için:

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Daha fazla bilgi için bkz. [Azure Işlevlerini izleme](functions-monitoring.md).

## <a name="next-steps"></a>Sonraki adımlar

Azure Functions Core Tools hakkında daha fazla bilgi edinmek için bkz. [Azure işlevlerine yerel olarak kod ve test](functions-run-local.md)etme.

.NET sınıf kitaplıkları olarak işlevleri geliştirme hakkında daha fazla bilgi için bkz. [Azure C# işlevleri geliştirici başvurusu](functions-dotnet-class-library.md). Bu makale ayrıca, Azure Işlevleri tarafından desteklenen çeşitli tür bağlamaları bildirmek üzere özniteliklerin nasıl kullanılacağına dair örneklere bağlantı sağlar.    
