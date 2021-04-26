---
title: Visual Studio ile Azure İşlevleri geliştirme
description: Visual Studio 2019 için Azure Işlevleri araçlarını kullanarak Azure Işlevleri geliştirmeyi ve test yapmayı öğrenin.
ms.custom: vs-azure, devx-track-csharp
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: 2cba0a9ad63e319af0a5eaa1c1c018c3b285c28a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765584"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Visual Studio ile Azure İşlevleri geliştirme  

Visual Studio, C# sınıf kitaplığı işlevlerini geliştirmenize, test etmenize ve Azure 'a dağıtmanıza imkan tanır. Azure Işlevleri ile ilk kez bu deneyim varsa bkz. [Azure işlevleri 'ne giriş](functions-overview.md).

Visual Studio, işlevlerinizi geliştirirken aşağıdaki avantajları sağlar: 

* Yerel geliştirme bilgisayarınızda işlevleri düzenleyin, derleyin ve çalıştırın. 
* Azure Işlevleri projenizi doğrudan Azure 'da yayımlayın ve gerektiğinde Azure kaynakları oluşturun. 
* İşlev bağlamalarını doğrudan C# kodunda bildirmek için C# özniteliklerini kullanın.
* Önceden derlenmiş C# işlevleri geliştirin ve dağıtın. Önceden karmaşıklu işlevler C# betik tabanlı işlevlerden daha iyi bir soğuk başlama performansı sağlar. 
* Visual Studio geliştirmenin avantajlarından faydalanırken işlevlerinizi C# dilinde kodlayın. 

Bu makalede C# sınıf kitaplığı işlevleri geliştirmek ve bunları Azure 'Da yayımlamak için Visual Studio 'Nun nasıl kullanılacağına ilişkin ayrıntılar sağlanmaktadır. Bu makaleyi okumadan önce, [Visual Studio için hızlı başlangıç işlevlerini](functions-create-your-first-function-visual-studio.md)tamamlamayı düşünün. 

Aksi belirtilmediği takdirde, yordamlar ve örnekler Visual Studio 2019 içindir. 

## <a name="prerequisites"></a>Önkoşullar

- Azure Işlevleri araçları. Azure Işlev araçları eklemek için, Visual Studio yüklemenize **Azure geliştirme** iş yükünü dahil edin. Azure Işlevleri araçları, Visual Studio 2017 ile başlayan Azure geliştirme iş yükünde kullanılabilir.

- Azure depolama hesabı gibi ihtiyacınız olan diğer kaynaklar, yayımlama işlemi sırasında aboneliğinizde oluşturulur.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Visual Studio 2017 ' de Azure geliştirme iş yükü, Azure Işlevleri araçlarını ayrı bir uzantı olarak yüklüyor. Visual Studio 2017 yüklemenizi güncelleştirdiğinizde Azure Işlevleri araçlarının [en son sürümünü](#check-your-tools-version) kullandığınızdan emin olun. Aşağıdaki bölümlerde, Visual Studio 2017 ' de Azure Işlevleri araçları uzantınızı nasıl denetleyen ve (gerekirse) güncelleştiren gösterilmektedir. 
>
> Visual Studio 2019 kullanıyorsanız bu bölümleri atlayın.

### <a name="check-your-tools-version-in-visual-studio-2017"></a><a name="check-your-tools-version"></a>Visual Studio 2017 ' de Araçlar sürümünüzü denetleyin

1. **Araçlar** menüsünde **Uzantılar ve güncelleştirmeler**' i seçin. **Yüklü**  >  **Araçlar**' ı genişletin ve ardından **Azure işlevleri ve Web işleri araçları**' nı seçin.

    ![Işlevler araçları sürümünü doğrulama](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. Yüklü **sürümü** not edin ve bu sürümü sürüm [notlarında](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md)listelenen en son sürümle karşılaştırın. 

1. Sürümünüz daha eskiyse, Visual Studio 'daki araçları aşağıdaki bölümde gösterildiği gibi güncelleştirin.

### <a name="update-your-tools-in-visual-studio-2017"></a>Visual Studio 2017 ' de araçlarınızı güncelleştirme

1. **Uzantılar ve güncelleştirmeler** iletişim kutusunda **güncelleştirmeler**  >  **Visual Studio Market**' ı genişletin, **Azure işlevleri ve Web işleri araçları** ' nı seçin ve **Güncelleştir**' i seçin.

    ![Işlevler araçları sürümünü güncelleştirme](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. Araçlar güncelleştirmesi indirildikten sonra, **Kapat**' ı seçin ve ardından Visual Studio 'yu kapatın ve ardından VSIX yükleyicisi ile araçlar güncelleştirmesini tetikleyin.

1. VSıX yükleyici 'de, araçları güncelleştirmek için **Değiştir** ' i seçin. 

1. Güncelleştirme tamamlandıktan sonra **Kapat**' ı seçin ve ardından Visual Studio 'yu yeniden başlatın.

> [!NOTE]  
> Visual Studio 2019 ve üzeri sürümlerde Azure Işlevleri araçları uzantısı, Visual Studio 'nun bir parçası olarak güncelleştirilir.  

## <a name="create-an-azure-functions-project"></a>Azure İşlevleri projesi oluşturma

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Bir Azure Işlevleri projesi oluşturduktan sonra, proje şablonu bir C# projesi oluşturur, `Microsoft.NET.Sdk.Functions` NuGet paketini yüklenir ve hedef Framework 'ü ayarlar. Yeni proje aşağıdaki dosyalara sahiptir:

* **host.js**:, işlevleri ana bilgisayarını yapılandırmanıza izin verir. Bu ayarlar, hem yerel olarak hem de Azure 'da çalışırken geçerlidir. Daha fazla bilgi için bkz. [host.jsbaşvurusu](functions-host-json.md).

* **local.settings.js**:, işlevleri yerel olarak çalıştırırken kullanılan ayarları korur. Bu ayarlar, Azure 'da çalışırken kullanılmaz. Daha fazla bilgi için bkz. [yerel ayarlar dosyası](#local-settings-file).

    >[!IMPORTANT]
    >Dosyadaki local.settings.jsgizli dizileri içerebildiğinden, bunu proje kaynak denetiinizden çıkarmanız gerekir. Bu dosya için **Çıkış Dizinine Kopyala** ayarının, **daha yeniyse kopyala** olarak ayarlandığından emin olun. 

Daha fazla bilgi için bkz. [işlevler sınıf kitaplığı projesi](functions-dotnet-class-library.md#functions-class-library-project).

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Visual Studio, projeyi yayımladığınızda local.settings.jsiçindeki ayarları otomatik olarak karşıya yüklememez. Bu ayarların Azure 'daki işlev uygulamanızda da mevcut olduğundan emin olmak için, projenizi yayımladıktan sonra bu ayarları karşıya yükleyin. Daha fazla bilgi için bkz. [işlev uygulaması ayarları](#function-app-settings). Bir `ConnectionStrings` koleksiyondaki değerler hiçbir şekilde yayımlanmaz.

Kodunuz Ayrıca, işlev uygulama ayarları değerlerini ortam değişkenleri olarak okuyabilir. Daha fazla bilgi için bkz. [ortam değişkenleri](functions-dotnet-class-library.md#environment-variables).

## <a name="configure-your-build-output-settings"></a>Yapı çıkış ayarlarınızı yapılandırın

Bir Azure Işlevleri projesi oluştururken, derleme araçları çıktıyı en iyileştirir ve böylece işlevler çalışma zamanına paylaşılan derlemelerin yalnızca bir kopyasının korunması sağlanır. Sonuç, mümkün olduğunca fazla alan kaydeden, iyileştirilmiş bir derleme olur. Ancak, proje derlemelerinizin herhangi birinin daha yeni bir sürümüne geçtiğinizde, derleme araçları bu derlemelerin korunması gerektiğini bilmiyor olabilir. İyileştirme işlemi sırasında bu derlemelerin korunduğundan emin olmak için, bunları `FunctionsPreservedDependencies` Proje (. csproj) dosyasındaki öğeleri kullanarak belirtebilirsiniz:

```xml
  <ItemGroup>
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.dll" />
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.Extensions.dll" />
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.Features.dll" />
  </ItemGroup>
```

## <a name="configure-the-project-for-local-development"></a>Projeyi yerel geliştirme için yapılandırma

Işlevler çalışma zamanı, dahili olarak bir Azure depolama hesabı kullanır. HTTP ve Web kancaları dışındaki tüm tetikleyici türleri için `Values.AzureWebJobsStorage` anahtarı geçerli bir Azure depolama hesabı bağlantı dizesi olarak ayarlayın. İşlev uygulamanız Ayrıca, proje için gerekli olan bağlantı ayarı için [Azure Storage öykünücüsü](../storage/common/storage-use-emulator.md) 'nü kullanabilir `AzureWebJobsStorage` . Öykünücüsünü kullanmak için değerini `AzureWebJobsStorage` olarak ayarlayın `UseDevelopmentStorage=true` . Dağıtımdan önce bu ayarı gerçek bir depolama hesabı bağlantı dizesiyle değiştirin.

Depolama hesabı bağlantı dizesini ayarlamak için:

1. Visual Studio 'da,   >  **bulut Gezginini** görüntüle ' yi seçin.

2. **Cloud Explorer**'Da **depolama hesapları**' nı genişletin ve depolama hesabınızı seçin. **Özellikler** sekmesinde, **birincil bağlantı dizesi** değerini kopyalayın.

2. Projenizde local.settings.jsdosya üzerinde açın ve `AzureWebJobsStorage` anahtarın değerini kopyaladığınız bağlantı dizesine ayarlayın.

3. `Values`İşlevlerinizin gerektirdiği diğer tüm bağlantılar için diziye benzersiz anahtarlar eklemek için önceki adımı tekrarlayın. 

## <a name="add-a-function-to-your-project"></a>Projenize bir işlev ekleyin

C# sınıf kitaplığı işlevlerinde, işlev tarafından kullanılan bağlamalar koddaki öznitelikler uygulanarak tanımlanır. İşlev tetiklerinizi, belirtilen şablonlardan oluşturduğunuzda, tetikleyici öznitelikleri sizin için uygulanır. 

1. **Çözüm Gezgini**, proje düğümüne sağ tıklayın ve   >  **Yeni öğe** Ekle ' yi seçin. 

2. **Azure işlevi**' ni seçin, sınıf Için bir **ad** girin ve ardından **Ekle**' yi seçin.

3. Tetikleyiciyi seçin, bağlama özelliklerini ayarlayın ve ardından **Tamam**' ı seçin. Aşağıdaki örnek, bir kuyruk depolama tetikleyicisi işlevi oluşturma ayarlarını gösterir. 

    ![Kuyruk depolama tetikleyicisi işlevi oluşturma](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    Bu tetikleyici örneği, adlı bir anahtarla bağlantı dizesi kullanır `QueueStorage` . Bu bağlantı dizesi ayarını [ dosyadalocal.settings.js](functions-run-local.md#local-settings-file)tanımlayın.

4. Yeni eklenen sınıfı inceleyin. `Run()`Özniteliği ile nitelendirilen bir statik yöntem görürsünüz `FunctionName` . Bu öznitelik, yönteminin işlevin giriş noktası olduğunu gösterir.

    Örneğin, aşağıdaki C# sınıfı temel bir kuyruk depolama tetikleyicisi işlevini temsil eder:

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

Giriş noktası yöntemine sağlanan her bağlama parametresine bağlamaya özgü bir öznitelik uygulanır. Öznitelik, bağlama bilgilerini parametre olarak alır. Önceki örnekte, ilk parametrenin bir `QueueTrigger` kuyruk depolama tetikleyicisi işlevini belirten bir özniteliği uygulandı. Sıra adı ve bağlantı dizesi ayar adı özniteliğe parametre olarak geçirilir `QueueTrigger` . Daha fazla bilgi için bkz. [Azure Için Azure kuyruk depolama bağlamaları işlevleri](functions-bindings-storage-queue-trigger.md).

İşlev uygulaması projenize daha fazla işlev eklemek için yukarıdaki yordamı kullanın. Projedeki her bir işlev farklı bir tetikleyicisine sahip olabilir, ancak bir işlevin tam olarak bir tetikleyicisi olmalıdır. Daha fazla bilgi için bkz. [Azure işlevleri Tetikleyicileri ve bağlamaları kavramları](functions-triggers-bindings.md).

## <a name="add-bindings"></a>Bağlama ekleme

Tetikleyiciler 'de olduğu gibi, giriş ve çıkış bağlamaları işlevinizde bağlama öznitelikleri olarak eklenir. Bir işleve şu şekilde bağlama ekleyin:

1. [Projeyi yerel geliştirme için yapılandırdığınızdan](#configure-the-project-for-local-development)emin olun.

2. Belirli bağlama için uygun NuGet uzantı paketini ekleyin. 

   Daha fazla bilgi için bkz. [Visual Studio Ile C# sınıf kitaplığı](./functions-bindings-register.md#local-csharp). Bağlama yönelik başvuru makalesinde bağlamaya özgü NuGet paketi gereksinimlerini bulun. Örneğin, [Event Hubs bağlama başvurusu makalesindeki](functions-bindings-event-hubs.md)Event Hubs tetikleyicisinin paket gereksinimlerini bulun.

3. Bağlamanın ihtiyacı olan uygulama ayarları varsa, bunları `Values` [yerel ayar dosyasında](functions-run-local.md#local-settings-file)koleksiyona ekleyin. 

   İşlev yerel olarak çalıştırıldığında bu değerleri kullanır. İşlev, Azure 'daki işlev uygulamasında çalıştırıldığında, [işlev uygulama ayarları](#function-app-settings)' nı kullanır.

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
   Kuyruk depolama bağlantısı, ayarından elde edilir `AzureWebJobsStorage` . Daha fazla bilgi için, belirli bağlama yönelik başvuru makalesine bakın. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>İşlevleri test etme

Azure İşlevleri Temel Araçları, Azure İşlevleri projenizi yerel geliştirme bilgisayarınızda çalıştırmanıza olanak sağlar. Daha fazla bilgi için bkz. [Azure Functions Core Tools Ile çalışma](functions-run-local.md). Visual Studio 'da bir işlevi ilk kez başlattığınızda bu araçları yüklemeniz istenir. 

Visual Studio 'da işlevinizi test etmek için:

1. F5 tuşuna basın. İstenirse Visual Studio'dan gelen Azure İşlevleri Temel (CLI) araçlarını indirme ve yükleme isteğini kabul edin. Ayrıca, araçların HTTP isteklerini işleyebilmesi için bir güvenlik duvarı özel durumu etkinleştirmeniz gerekebilir.

2. Çalıştıran proje ile, dağıtılan bir işlevi test ettiğiniz için kodunuzu test edin. 

   Daha fazla bilgi için bkz. [Azure işlevlerinde kodunuzu test etme stratejileri](functions-test-a-function.md). Visual Studio 'Yu hata ayıklama modunda çalıştırdığınızda, kesme noktaları beklenen şekilde dönüştürülür.

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->


## <a name="publish-to-azure"></a>Azure’da Yayımlama

Visual Studio 'dan yayımladığınızda, iki dağıtım yönteminden birini kullanır:

* [Web dağıtımı](functions-deployment-technologies.md#web-deploy-msdeploy): Windows uygulamalarını paketler ve HERHANGI bir IIS sunucusuna dağıtır.
* [Paket Çalıştır etkin olan ZIP dağıtımı](functions-deployment-technologies.md#zip-deploy): Azure işlevleri dağıtımları için önerilir.

Projenizi Azure 'da bir işlev uygulamasına yayımlamak için aşağıdaki adımları kullanın.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>İşlev uygulaması ayarları

Projeyi yayımladığınızda Visual Studio bu ayarları otomatik olarak yüklememediğinden local.settings.jseklediğiniz tüm ayarlar Azure 'daki işlev uygulamasına da eklemeniz gerekir.

Gerekli ayarları Azure 'daki işlev uygulamanıza yüklemenin en kolay yolu, projenizi başarıyla yayımladıktan sonra görüntülenen **Azure App Service ayarlarını yönet** bağlantısını seçmesidir.

:::image type="content" source="./media/functions-develop-vs/functions-vstools-app-settings.png" alt-text="Yayımla penceresindeki ayarlar":::

Bu bağlantının seçilmesi, yeni uygulama ayarları ekleyebileceğiniz veya var olanları değiştirebileceğiniz işlev uygulaması için **uygulama ayarları** iletişim kutusunu görüntüler.

![Uygulama ayarları](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**Yerel** , local.settings.jsdosyadaki bir ayar değerini görüntüler ve **Uzaktan** Azure 'daki işlev uygulamasında bir geçerli ayar değeri görüntüler. Yeni bir uygulama ayarı oluşturmak için **Ayar ekle** ' yi seçin. Bir ayar değerini **uzak** alana kopyalamak için **Yerel bağlantıdan Ekle değerini** kullanın. Bekleyen değişiklikler, **Tamam**' ı seçtiğinizde yerel ayarlar dosyasına ve işlev uygulamasına yazılır.

> [!NOTE]
> Varsayılan olarak, dosyadaki local.settings.jskaynak denetimine iade edilmez. Bu, kaynak denetiminden bir yerel Işlevler projesini klonladığınızda, projede local.settings.jsdosya yok demektir. Bu durumda, **uygulama ayarları** iletişim kutusunun beklendiği gibi çalışması için proje kökündeki dosyasında local.settings.jsel ile oluşturmanız gerekir. 

Ayrıca, uygulama ayarlarını şu diğer yollarla da yönetebilirsiniz:

* [Azure Portal kullanın](functions-how-to-use-azure-function-app-settings.md#settings).
* [ `--publish-local-settings` Azure Functions Core Tools Yayımla seçeneğini kullanın](functions-run-local.md#publish).
* [Azure CLI 'Yi kullanın](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set).

## <a name="monitoring-functions"></a>İzleme işlevleri

İşlevlerinizin yürütülmesini izlemek için önerilen yol, işlev uygulamanızı Azure Application Insights ile tümleştirerek yapılır. Azure portal bir işlev uygulaması oluşturduğunuzda, bu tümleştirme varsayılan olarak sizin için yapılır. Ancak, Visual Studio yayımlama sırasında işlev uygulamanızı oluşturduğunuzda, Azure 'daki işlev uygulamanızda tümleştirme yapılmaz. Application Insights işlev uygulamanıza nasıl bağlayacağınızı öğrenmek için bkz. [Application Insights Tümleştirmesini Etkinleştirme](configure-monitoring.md#enable-application-insights-integration).

Application Insights kullanarak izleme hakkında daha fazla bilgi edinmek için bkz. [Azure Işlevlerini izleme](functions-monitoring.md).

## <a name="next-steps"></a>Sonraki adımlar

Azure Functions Core Tools hakkında daha fazla bilgi için bkz. [Azure Functions Core Tools Ile çalışma](functions-run-local.md).

.NET sınıf kitaplıkları olarak işlevleri geliştirme hakkında daha fazla bilgi için bkz. [Azure Işlevleri C# Geliştirici başvurusu](functions-dotnet-class-library.md). Bu makale ayrıca, Azure Işlevleri tarafından desteklenen çeşitli tür bağlamaları bildirmek üzere özniteliklerin nasıl kullanılacağına dair örneklere bağlantı sağlar.    
