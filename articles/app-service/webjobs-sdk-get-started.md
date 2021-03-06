---
title: Web Işleri SDK 'sını kullanmaya başlama
description: Olay odaklı arka plan işleme için WebJobs SDK 'ya giriş. Azure hizmetleri ve üçüncü taraf hizmetlerindeki verilere erişme hakkında bilgi edinin.
author: ggailey777
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 4a22602dd9638b981cfe3d8bae9b5cdaacbf90dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91652049"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Olay temelli arka plan işleme için Azure Web İşleri SDK'sını kullanmaya başlama

Bu makalede, Visual Studio 2019 kullanarak bir Azure WebJobs SDK projesi oluşturma, yerel olarak çalıştırma ve sonra [Azure App Service](overview.md)dağıtma işlemleri gösterilir. WebJobs SDK 'sının 3. x sürümü hem .NET Core hem de .NET Framework Konsol uygulamalarını destekler. Web Işleri SDK 'Sı ile çalışma hakkında daha fazla bilgi edinmek için bkz. [Azure WebJobs SDK 'sını olay odaklı arka plan işleme için kullanma](webjobs-sdk-how-to.md).

Bu makalede, Web Işlerinin bir .NET Core konsol uygulaması olarak nasıl dağıtılacağı gösterilir. Web Işlerini .NET Framework konsol uygulaması olarak dağıtmak için bkz. [WebJobs as .NET Framework konsol Apps](webjobs-dotnet-deploy-vs.md#webjobs-as-net-framework-console-apps). Yalnızca .NET Framework destekleyen WebJobs SDK sürüm 2. x ile ilgileniyorsanız, bkz. [Visual Studio kullanarak Web Işleri geliştirme ve dağıtma-Azure App Service](webjobs-dotnet-deploy-vs.md).

## <a name="prerequisites"></a>Önkoşullar

* **Azure geliştirme** iş yüküyle [Visual Studio 2019 ' ü yükler](/visualstudio/install/) . Zaten Visual Studio 'Ya sahipseniz ancak iş yükünüz yoksa, araçları **ve özellikleri almak > araçlar**' ı seçerek iş yükünü ekleyin.

* WebJobs SDK projenizi Azure 'da yayımlamak için [bir Azure hesabınızın](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) olması gerekir.

## <a name="create-a-project"></a>Proje oluşturma

1. Visual Studio 'da **Yeni proje oluştur**' u seçin.

2. **Konsol uygulaması (.NET Core)** seçeneğini belirleyin.

3. Projeyi *Webjobssdksample* olarak adlandırın ve **Oluştur**' u seçin.

   ![Yeni Proje iletişim kutusu](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>Web Işleri NuGet paketleri

1. ' Yi içeren [ `Microsoft.Azure.WebJobs.Extensions` NuGet paketinin](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/)en son kararlı 3. x sürümünü yükler `Microsoft.Azure.WebJobs` .

     **Paket Yöneticisi konsol** komutu aşağıda verilmiştir:

     ```powershell
     Install-Package Microsoft.Azure.WebJobs.Extensions -version <3_X_VERSION>
     ```

    Bu komutta, yerine `<3_X_VERSION>` paketin desteklenen bir sürümü ile değiştirin. 

## <a name="create-the-host"></a>Konağı oluşturma

Konak, Tetikleyicileri ve çağrı işlevlerini dinleyen işlevlere yönelik çalışma zamanı kapsayıcısıdır. Aşağıdaki adımlarda [`IHost`](/dotnet/api/microsoft.extensions.hosting.ihost) , ASP.NET Core ' deki genel ana bilgisayar olan, uygulayan bir konak oluşturulur.

1. *Program. cs*' de şu `using` deyimleri ekleyin:

    ```cs
    using System.Threading.Tasks;
    using Microsoft.Extensions.Hosting;
    ```

1. `Main` yöntemini aşağıdaki kod ile değiştirin:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

ASP.NET Core, konak konfigürasyonları örnekteki Yöntemler çağırarak ayarlanır [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) . Daha fazla bilgi için bkz. [.NET genel ana bilgisayar](/aspnet/core/fundamentals/host/generic-host). `ConfigureWebJobs`Genişletme yöntemi, Web işleri ana bilgisayarını başlatır. `ConfigureWebJobs`' De, belirli Web işleri uzantılarını başlatır ve bu uzantıların özelliklerini ayarlarsınız.  

## <a name="enable-console-logging"></a>Konsol günlüğünü etkinleştir

Bu bölümde, [ASP.NET Core günlük çerçevesini](/aspnet/core/fundamentals/logging)kullanan konsol günlüğünü ayarlarsınız.

1. [ `Microsoft.Extensions.Logging.Console` NuGet paketinin](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)içeren en son kararlı sürümünü yükler `Microsoft.Extensions.Logging` .

   **Paket Yöneticisi konsol** komutu aşağıda verilmiştir:

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version <3_X_VERSION>
   ```
   Bu komutta, yerine `<3_X_VERSION>` paketin desteklenen 3. x sürümünü koyun.

1. *Program. cs*' de bir `using` ifade ekleyin:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. Yöntemini çağırın [`ConfigureLogging`](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) . [`AddConsole`](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole)Yöntemi, yapılandırmaya konsol günlüğü ekler.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    `Main`Yöntemi şu şekilde görünür:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

    Bu güncelleştirme şunları yapar:

    * [Pano günlüğünü](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs)devre dışı bırakır. Pano eski bir izleme aracıdır ve yüksek aktarım hızı üretim senaryolarında Pano günlüğü önerilmez.
    * Konsol sağlayıcısını varsayılan [filtrelemeye](webjobs-sdk-how-to.md#log-filtering)ekler.

Şimdi, Azure depolama kuyruğuna ulaşan iletiler tarafından tetiklenen bir işlev ekleyebilirsiniz.

## <a name="install-the-storage-binding-extension"></a>Depolama bağlama uzantısını yükleme

Sürüm 3. x ile başlayarak, Web Işleri SDK 'Sı tarafından gerekli olan depolama bağlama uzantısını açıkça yüklemelisiniz. Önceki sürümlerde, depolama bağlamaları SDK 'ya eklenmiştir.

1. [Microsoft. Azure. WebJobs. Extensions. Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet paketi, sürüm 3. x ' in en son kararlı sürümünü yükler. 

    **Paket Yöneticisi konsol** komutu aşağıda verilmiştir:

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version <3_X_VERSION>
    ```
    
    Bu komutta, yerine `<3_X_VERSION>` paketin desteklenen bir sürümü ile değiştirin. 

2. `ConfigureWebJobs`Uzantı yönteminde, `AddAzureStorage` [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) depolama uzantısını başlatmak için örneğindeki yöntemi çağırın. Bu noktada, `ConfigureWebJobs` yöntemi aşağıdaki örneğe benzer şekilde görünür:

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>İşlev oluşturma

1. Projeye sağ tıklayın,   >  **Yeni öğe Ekle...** öğesini seçin, **sınıf**' i seçin, yeni C# sınıf dosyasını adlandırın *. cs* ve **Ekle**' yi seçin.

1. Functions. cs dosyasında, oluşturulan şablonu aşağıdaki kodla değiştirin:
    
    ```cs
    using Microsoft.Azure.WebJobs;
    using Microsoft.Extensions.Logging;
    
    namespace WebJobsSDKSample
    {
        public class Functions
        {
            public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
            {
                logger.LogInformation(message);
            }
        }
    }
    ```

   `QueueTrigger`Özniteliği, adlı Azure depolama kuyruğuna yeni bir ileti yazıldığında çalışma zamanına bu işlevi çağırmasını söyler `queue` . Kuyruk iletisinin içeriği, parametresindeki Yöntem koduna sağlanır `message` . Yöntemin gövdesi, tetikleyici verilerini işletiğinizin yerdir. Bu örnekte, kod yalnızca iletiyi günlüğe kaydeder.

   `message`Parametrenin bir dize olması gerekmez. Ayrıca, bir JSON nesnesine, bir bayt dizisine veya [Cloudqueuemessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) nesnesine de bağlanabilirsiniz. [Bkz. kuyruk tetikleyicisi kullanımı](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#usage). Her bağlama türünün (kuyruklar, Bloblar veya tablolar gibi), bağlayabileceğiniz farklı parametre türleri kümesi vardır.

## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

Yerel olarak çalışan Azure depolama öykünücüsü, Web Işleri SDK 'sının ihtiyaç duyacağı tüm özelliklere sahip değildir. Bu bölümde, Azure 'da bir depolama hesabı oluşturur ve projeyi kullanmak üzere yapılandırırsınız. Zaten bir depolama hesabınız varsa adım 6 ' ya atlayın.

1. Visual Studio 'da **Sunucu Gezgini** açın ve Azure 'da oturum açın. **Azure** düğümüne sağ tıklayın ve ardından **Microsoft Azure aboneliğine Bağlan**' ı seçin.

   ![Azure'da oturum açma](./media/webjobs-sdk-get-started/sign-in.png)

1. **Sunucu Gezgini** içindeki **Azure** düğümü altında **depolama**' ya sağ tıklayın ve ardından **depolama hesabı oluştur**' u seçin.

   ![Depolama hesabı oluştur menüsü](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. **Depolama hesabı oluştur** iletişim kutusunda depolama hesabı için benzersiz bir ad girin.

1. App Service uygulamanızı oluşturduğunuz **bölgeyi** veya size yakın bir bölgeyi seçin.

1. **Oluştur**’u seçin.

   ![Depolama hesabı oluştur](./media/webjobs-sdk-get-started/create-storage-account.png)

1. **Sunucu Gezgini**' deki **depolama** düğümü altında yeni depolama hesabı ' nı seçin. **Özellikler** penceresinde, **bağlantı dizesi** değeri alanının sağ tarafındaki üç nokta (**...**) simgesini seçin.

   ![Bağlantı dizesi üç nokta](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. Bağlantı dizesini kopyalayın ve bu değeri bir yere kaydederek bir daha hazır hale getirebilirsiniz.

   ![Bağlantı dizesini Kopyala](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-to-run-locally"></a>Depolamayı yerel olarak çalışacak şekilde yapılandırma

Web Işleri SDK 'Sı, Azure 'daki uygulama ayarlarındaki depolama bağlantı dizesini arar. Yerel olarak çalıştırdığınızda bu değer yerel yapılandırma dosyasında veya ortam değişkenlerinde bu değere bakar.

1. Projeye sağ tıklayın,   >  **Yeni öğe Ekle...**' yi seçin, **JavaScript JSON yapılandırma dosyası**' nı seçin, yeni dosya *appsettings.js* dosyaya adlandırın ve **Ekle**' yi seçin. 

1. Yeni dosyada, `AzureWebJobsStorage` Aşağıdaki örnekte olduğu gibi bir alan ekleyin:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. *{Storage bağlantı dizesi}* öğesini daha önce kopyaladığınız bağlantı dizesiyle değiştirin.

1. Çözüm Gezgini dosyasında *appsettings.js* seçin ve **Özellikler** penceresinde, **daha yeniyse kopyalamak** için **Çıkış Dizinine Kopyala** ' yı ayarlayın.

Daha sonra, Azure App Service ' de uygulamanıza aynı bağlantı dizesi uygulaması ayarını eklersiniz.

## <a name="test-locally"></a>Yerel olarak test etme

Bu bölümde, projeyi yerel olarak oluşturup çalıştırın ve bir kuyruk iletisi oluşturarak işlevi tetiklersiniz.

1. Projeyi çalıştırmak için **CTRL + F5** tuşlarına basın.

   Konsol, çalışma zamanının işlevinizi bulduğu ve sıra iletilerinin bunu tetikleyeceğini beklediği gösterilmektedir. Aşağıdaki çıktı v3. x ana bilgisayarı tarafından oluşturulmuştur:

   ```console
    info: Microsoft.Azure.WebJobs.Hosting.JobHostService[0]
          Starting JobHost
    info: Host.Startup[0]
          Found the following functions:
          WebJobsSDKSample.Functions.ProcessQueueMessage

    info: Host.Startup[0]
          Job host started
    Application started. Press Ctrl+C to shut down.
    Hosting environment: Development
    Content root path: C:\WebJobsSDKSample\WebJobsSDKSample\bin\Debug\netcoreapp2.1\
   ```

1. Konsol penceresini kapatın.

1. Visual Studio 'da **Sunucu Gezgini** ' de, yeni depolama hesabınızın düğümünü genişletin ve ardından **Kuyruklar**' a sağ tıklayın.

1. **Kuyruk oluştur**' u seçin.

1. Kuyruğun adı olarak *kuyruğu* girin ve ardından **Tamam**' ı seçin.

   ![Kuyruğu nerede oluşturacağınız ve "Queue" olarak ad ettiğiniz ekran görüntüsü. ](./media/webjobs-sdk-get-started/create-queue.png)

1. Yeni sıranın düğümüne sağ tıklayın ve ardından **kuyruğu görüntüle**' yi seçin.

1. **Ileti Ekle** simgesini seçin.

   ![Ileti Ekle simgesini vurgulayan ekran görüntüsü.](./media/webjobs-sdk-get-started/create-queue-message.png)

1. **Ileti Ekle** iletişim kutusunda *Merhaba dünya girin!* **ileti metni** olarak **Tamam**' ı seçin. Kuyrukta artık bir ileti var.

   ![Sıra oluştur](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Projeyi tekrar çalıştırın.

   İşlevinde özniteliğini kullandığınız için `QueueTrigger` `ProcessQueueMessage` WEJOBS SDK çalışma zamanı, başlatıldığında kuyruk iletilerini dinler. *Queue* adlı kuyrukta yeni bir kuyruk iletisi bulur ve işlevi çağırır.

   [Sıra yoklaması üstel geri](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#polling-algorithm)alma nedeniyle, çalışma zamanının iletiyi bulması ve işlevi çağırması 2 dakika kadar sürebilir. Bu bekleme süresi, [geliştirme modunda](webjobs-sdk-how-to.md#host-development-settings)çalıştırılarak azaltılabilir.

   Konsol çıktısı şuna benzer:

   ```console
    info: Function.ProcessQueueMessage[0]
          Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=2c319369-d381-43f3-aedf-ff538a4209b8)
    info: Function.ProcessQueueMessage[0]
          Trigger Details: MessageId: b00a86dc-298d-4cd2-811f-98ec39545539, DequeueCount: 1, InsertionTime: 1/18/2019 3:28:51 AM +00:00
    info: Function.ProcessQueueMessage.User[0]
          Hello World!
    info: Function.ProcessQueueMessage[0]
          Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=2c319369-d381-43f3-aedf-ff538a4209b8)
   ```

1. Konsol penceresini kapatın. 

1. Kuyruk penceresine dönün ve yenileyin. Bu ileti, yerel olarak çalışan işleviniz tarafından işlendiği için gitti. 

## <a name="add-application-insights-logging"></a>Application Insights günlüğü ekleme

Proje Azure 'da çalıştığında, konsol çıkışını görüntüleyerek işlev yürütmeyi izleyemiyoruz. Önerdiğimiz izleme çözümü [Application Insights](../azure-monitor/app/app-insights-overview.md). Daha fazla bilgi için bkz. [Azure Işlevlerini izleme](../azure-functions/functions-monitoring.md).

Bu bölümde, Azure 'a dağıtmadan önce Application Insights günlüğü ayarlamak için aşağıdaki görevleri gerçekleştirebilirsiniz:

* Birlikte çalışmak için bir App Service uygulamanız ve Application Insights bir örnek bulunduğundan emin olun.
* App Service uygulamasını, daha önce oluşturduğunuz Application Insights örneğini ve depolama hesabını kullanacak şekilde yapılandırın.
* Projeyi Application Insights günlüğe kaydetmek için ayarlayın.

### <a name="create-app-service-app-and-application-insights-instance"></a>App Service uygulaması ve Application Insights örneği oluşturma

1. Kullanabileceğiniz bir App Service uygulaması yoksa [bir tane oluşturun](quickstart-dotnet-framework.md). Uygulamanızı oluşturduğunuzda bağlı bir Application Insights kaynağı da oluşturabilirsiniz. Bunu yaptığınızda, `APPINSIGHTS_INSTRUMENTATIONKEY` uygulamanızda sizin için ayarlanır.

1. Kullanabileceğiniz bir Application Insights kaynağınız yoksa [bir tane oluşturun](../azure-monitor/app/create-new-resource.md ). **Uygulama türünü** **genel** olarak ayarlayın ve izleme **anahtarını Kopyala**' nın altındaki bölümleri atlayın.

1. Kullanmak istediğiniz bir Application Insights kaynağınız zaten varsa, [izleme anahtarını kopyalayın](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Uygulama ayarlarını yapılandırma 

1. Visual Studio 'da **Sunucu Gezgini** ' de **Azure** altındaki **App Service** düğümünü genişletin.

1. App Service uygulamanızın bulunduğu kaynak grubunu genişletin ve ardından App Service uygulamanıza sağ tıklayın.

1. **Görünüm ayarları**' nı seçin.

1. **Bağlantı dizeleri** kutusuna aşağıdaki girişi ekleyin.

   |Name  |bağlantı dizesi  |Veritabanı türü|
   |---------|---------|------|
   |AzureWebJobsStorage | {daha önce kopyaladığınız depolama bağlantı dizesi}|Özel|

1. **Uygulama ayarları** kutusunda bir Application Insights izleme anahtarı yoksa, daha önce kopyaladığınız birini ekleyin. (İzleme anahtarı, App Service uygulamasının nasıl oluşturulduğuna bağlı olarak zaten orada olabilir.)

   |Name  |Değer  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {izleme anahtarı} |

1. *{Instrumentation Key}* değerini, kullanmakta olduğunuz Application Insights kaynaktaki izleme anahtarıyla değiştirin.

1. **Kaydet**’i seçin.

1. Application Insights bağlantısını projeye ekleyerek yerel olarak çalıştırabilirsiniz. Dosyadaki *appsettings.js* , `APPINSIGHTS_INSTRUMENTATIONKEY` Aşağıdaki örnekte olduğu gibi bir alan ekleyin:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    *{Instrumentation Key}* değerini, kullanmakta olduğunuz Application Insights kaynaktaki izleme anahtarıyla değiştirin.

1. Yaptığınız değişiklikleri kaydedin.

### <a name="add-application-insights-logging-provider"></a>Application Insights günlük sağlayıcısı ekle

Günlüğe kaydetme [Application Insights](../azure-monitor/app/app-insights-overview.md) avantajlarından yararlanmak için günlüğe kaydetme kodunuzu aşağıdaki gibi güncelleştirin:

* Varsayılan [filtrelemeye](webjobs-sdk-how-to.md#log-filtering)sahip bir Application Insights günlüğü sağlayıcısı ekleyin. Yerel olarak çalışırken, tüm bilgiler ve yüksek düzeyli Günlükler hem konsola hem de Application Insights yazılır.
* [Loggerfactory](./webjobs-sdk-how-to.md#logging-and-monitoring) nesnesini, `using` ana bilgisayardan çıkıldığında günlük çıkışının temizlendiğinden emin olmak için bir bloğa koyun.

1. [ `Microsoft.Azure.WebJobs.Logging.ApplicationInsights` NuGet paketinin](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/)en son kararlı 3. x sürümünü yükler.

   **Paket Yöneticisi konsol** komutu aşağıda verilmiştir:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <3_X_VERSION>
   ```
    Bu komutta, yerine `<3_X_VERSION>` paketin desteklenen bir sürümü ile değiştirin.

1. *Program. cs* ' i açın ve `Main` yöntemindeki kodu aşağıdaki kodla değiştirin:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.UseEnvironment(EnvironmentName.Development);
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                    b.AddAzureStorage();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();

                    // If the key exists in settings, use it to enable Application Insights.
                    string instrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                    if (!string.IsNullOrEmpty(instrumentationKey))
                    {
                        b.AddApplicationInsightsWebJobs(o => o.InstrumentationKey = instrumentationKey);
                    }
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

    Bu, daha önce uygulama ayarlarınıza eklediğiniz anahtarı kullanarak Application Insights sağlayıcısını günlüğe kaydetmeye ekler.

## <a name="test-application-insights-logging"></a>Test Application Insights günlüğü

Bu bölümde, günlüğe kaydetme verilerinin artık Application Insights ve konsoluna gittiğini doğrulamak için yerel olarak yeniden çalıştırırsınız.

1. [Daha önce](#test-locally)yaptığınız gibi bir kuyruk iletisi oluşturmak Için Visual Studio 'da **Sunucu Gezgini** kullanın, örneğin *Hello App Insights girin!* ileti metni olarak.

1. Projeyi çalıştırın.

   Web Işleri SDK 'Sı kuyruk iletisini işler ve günlükleri konsol penceresinde görürsünüz.

1. Konsol penceresini kapatın.

1. Application Insights kaynağınızı görüntülemek için [Azure Portal](https://portal.azure.com/) gidin. Arama yapın ve **Application Insights** seçin.

1. Application Insights örneğinizi seçin.

1. **Ara**' yı seçin.

   ![Arama Seç](./media/webjobs-sdk-get-started/select-search.png)

1. *Merhaba uygulama öngörülerini görmüyorsanız!* ileti, birkaç dakika için düzenli aralıklarla **Yenile** ' yi seçin. (Günlükler hemen görünmez, çünkü Application Insights istemcisinin işlediği günlükleri temizlemesi biraz zaman alır.)

   ![Application Insights oturum açar](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. Konsol penceresini kapatın.

## <a name="deploy-to-azure"></a><a name="deploy-as-a-webjob"></a>Azure’a dağıtın

Dağıtım sırasında, işlevlerinizin çalıştırılacağı bir App Service örneği oluşturursunuz. Azure 'da App Service için bir .NET Core konsol uygulaması yayımladığınızda, otomatik olarak bir WebJob olarak çalıştırılır. Yayımlama hakkında daha fazla bilgi edinmek için bkz. [Visual Studio kullanarak Web Işleri geliştirme ve dağıtma](webjobs-dotnet-deploy-vs.md).

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

## <a name="trigger-the-function-in-azure"></a>Azure 'da işlevi tetikleme

1. Yerel olarak çalıştırılmadığından emin olun (hala açıksa konsol penceresini kapatın). Aksi halde, yerel örnek, oluşturduğunuz herhangi bir kuyruk iletisini işleyen ilk işlem olabilir.

1. Visual Studio 'daki **kuyruk** sayfasında, sıraya daha önce olduğu gibi bir ileti ekleyin.

1. **Kuyruk** sayfasını yenileyin ve yeni Ileti, Azure 'da çalışan işlev tarafından işlendiği için kaybolur.

   > [!TIP]
   > Azure 'da test ederken, bir kuyruk tetikleyicisi işlevinin hemen çağrıldığından emin olmak için [geliştirme modunu](webjobs-sdk-how-to.md#host-development-settings) kullanın ve [sıra yoklaması üstel geri](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#polling-algorithm)alma nedeniyle gecikmelerden kaçının.

### <a name="view-logs-in-application-insights"></a>Günlükleri Application Insights görüntüle

1. [Azure Portal](https://portal.azure.com/)açın ve Application Insights kaynağına gidin.

1. **Ara**' yı seçin.

1. *Merhaba Azure* 'ı görmüyorsanız! ileti, birkaç dakika için düzenli aralıklarla **Yenile** ' yi seçin.

   *Hello Azure* dahil olmak üzere WebJob 'ta çalışan işlevden günlükleri görürsünüz! önceki bölümde girdiğiniz metin.

## <a name="add-an-input-binding"></a>Giriş bağlaması ekleme

Giriş bağlamaları, verileri okuyan kodu basitleştirir. Bu örnekte, kuyruk iletisi bir blob adı olacaktır ve Azure Storage 'da blob bulmak ve okumak için blob adını kullanacaksınız.

1. *Functions. cs* dosyasında, `ProcessQueueMessage` yöntemini aşağıdaki kodla değiştirin:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   Bu kodda, `queueTrigger` bir [bağlama ifadesi](../azure-functions/functions-bindings-expressions-patterns.md)olduğundan, çalışma zamanında farklı bir değere çözümlenmektedir.  Çalışma zamanında, kuyruk iletisinin içeriğine sahiptir.

1. Şunu ekleyin `using` :

   ```cs
   using System.IO;
   ```

1. Depolama hesabınızda bir blob kapsayıcısı oluşturun.

   a. Visual Studio 'da **Sunucu Gezgini** , depolama hesabınızın düğümünü genişletin, **Bloblar**' a sağ tıklayın ve ardından **BLOB kapsayıcısı oluştur**' u seçin.

   b. **BLOB kapsayıcısı oluştur** iletişim kutusunda kapsayıcı adı olarak *kapsayıcı* yazın ve ardından **Tamam**' a tıklayın.

1. *Program. cs* dosyasını blob kapsayıcısına yükleyin. (Bu dosya burada bir örnek olarak kullanılır; herhangi bir metin dosyasını karşıya yükleyebilir ve dosyanın adıyla bir kuyruk iletisi oluşturabilirsiniz.)

   a. **Sunucu Gezgini**, oluşturduğunuz kapsayıcının düğümüne çift tıklayın.

   b. **Kapsayıcı** penceresinde **karşıya yükle** düğmesini seçin.

   ![Blob karşıya yükleme düğmesi](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. *Program. cs* bulun ve seçin ve ardından **Tamam**' ı seçin.

1. Daha önce oluşturduğunuz sırada *program. cs* ile ileti metni olarak bir kuyruk iletisi oluşturun.

   ![İleti programını sıraya al. cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Projeyi yerel olarak çalıştırın.

   Kuyruk iletisi işlevini tetikler ve ardından blobu okur ve uzunluğunu günlüğe kaydeder. Konsol çıktısı şuna benzer:

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```

## <a name="add-an-output-binding"></a>Çıktı bağlaması ekleme

Çıkış bağlamaları veri yazan kodu basitleştirir. Bu örnek, boyutunu günlüğe kaydetmek yerine Blobun bir kopyasını yazarak bir öncekini değiştirir. BLOB depolama bağlamaları, daha önce yüklediğimiz Azure Storage uzantı paketine dahildir.

1. `ProcessQueueMessage` yöntemini aşağıdaki kod ile değiştirin:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

1. İleti metni olarak *program. cs* ile başka bir kuyruk iletisi oluşturun.

1. Projeyi yerel olarak çalıştırın.

   Kuyruk iletisi işlevi tetikler, sonra blobu okur, uzunluğunu günlüğe kaydeder ve yeni bir blob oluşturur. Konsol çıkışı aynıdır, ancak blob kapsayıcı penceresine gidip **Yenile**' yi seçtiğinizde, *Copy-program. cs* adlı yeni bir blob görürsünüz.

## <a name="republish-the-updates-to-azure"></a>Güncelleştirmeleri Azure 'da yeniden yayımlayın

1. **Çözüm Gezgini**'nde projeye sağ tıklayın ve **Yayımla**'yı seçin.

1. **Yayımla** iletişim kutusunda geçerli profilin seçildiğinden emin olun ve ardından **Yayımla**' yı seçin. Yayımla sonuçları **Çıkış** penceresinde ayrıntılıdır.
 
1. Blob kapsayıcısına bir dosya yükleyerek ve karşıya yüklenen dosyanın adı olan kuyruğa bir ileti ekleyerek Azure 'daki işlevi doğrulayın. İletiyi kuyruktan kaldırıldığını ve BLOB kapsayıcısında oluşturulan dosyanın bir kopyasını görürsünüz. 

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede bir WebJobs SDK 3. x projesi oluşturma, çalıştırma ve dağıtma işlemlerinin nasıl yapılacağı açıklanır.

> [!div class="nextstepaction"]
> [Web Işleri SDK 'Sı hakkında daha fazla bilgi edinin](webjobs-sdk-how-to.md)