---
title: Azure'da Service Fabric üzerindeki ASP.NET Core hizmetlerini izleme ve tanılama | Microsoft Docs
description: Bu öğreticide, Azure Service Fabric ASP.NET Core uygulaması için izleme ve tanılamayı ayarlama konusunda bilgi edinin.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/10/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 1f18aef12978b3df1ba1fd654ea4a0e9548a4b46
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68228086"
---
# <a name="tutorial-monitor-and-diagnose-an-aspnet-core-application-on-service-fabric-using-application-insights"></a>Öğretici: Bir Application Insights'ı kullanarak Service fabric'te ASP.NET Core uygulamasını izleme ve tanılama

Bu öğretici, bir serinin beşinci kısmıdır. Service Fabric kümesinde çalışan bir ASP.NET Core uygulaması için Application Insights kullanarak izleme ve tanılamayı ayarlama adımlarında yol gösterilir. Öğreticinin ilk bölümü olan [.NET Service Fabric uygulaması oluşturma](service-fabric-tutorial-create-dotnet-app.md) bölümünde geliştirilen uygulamadan telemetri toplarız.

Bu öğretici serisinin dördüncü kısmında şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> * Uygulamanız için Application Insights’ı yapılandırma
> * Hizmetler arasındaki HTTP tabanlı iletişimi izlemek için yanıt telemetrisi toplama
> * Application Insights'da Uygulama Haritası özelliğini kullanma
> * Application Insights API kullanarak özel olaylar ekleme

Bu öğretici dizisinde şunların nasıl yapıldığını öğrenirsiniz:
> [!div class="checklist"]
> * [.NET Service Fabric uygulaması oluşturma](service-fabric-tutorial-create-dotnet-app.md)
> * [Uygulamayı uzak kümeye dağıtma](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Bir ASP.NET Core ön uç hizmetine HTTPS uç noktası ekleme](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Azure Pipelines kullanarak CI/CD yapılandırma](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * Uygulama için izleme ve tanılamayı ayarlama

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce:

* Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun
* [Visual Studio 2019 yükleme](https://www.visualstudio.com/) yükleyip **Azure geliştirme** ve **ASP.NET ve web geliştirme** iş yükleri.
* [Service Fabric SDK'yı yükleyin](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Voting örnek uygulamasını indirme

[Bu öğretici serisinin birinci kısmında](service-fabric-tutorial-create-dotnet-app.md) Voting örnek uygulamasını oluşturmadıysanız, indirebilirsiniz. Komut penceresinde veya terminalde, örnek uygulama deposunu yerel makinenize kopyalamak için aşağıdaki komutu çalıştırın.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-an-application-insights-resource"></a>Application Insights kaynağını ayarlama

Application Insights, Azure'un uygulama performansı yönetim platformu olduğu gibi, Service Fabric'in de uygulama izleme ve tanılama için önerilen platformudur.

Application Insights kaynağı oluşturmak için [Azure Portal](https://portal.azure.com)'a gidin. Sol gezinti menüsünde **Kaynak oluştur**'a tıklayarak Azure Market'i açın. **İzleme ve Yönetim**'e ve ardından **Application Insights**'a tıklayın.

![Yeni AI kaynağı oluşturma](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource.png)

Şimdi oluşturulacak kaynağın öznitelikleri hakkındaki bilgileri doldurmanız gerekir. Uygun *Ad*, *Kaynak Grubu* ve *Abonelik* bilgilerini girin. *Konum* olarak gelecekte Service Fabric kümenizi dağıtabileceğiniz yeri ayarlayın. Bu öğreticide, uygulamayı yerel kümede dağıtacağımız için *Konum* alanı bizim konumuzla ilgili değildir. *Uygulama Türü* "ASP.NET web uygulaması" olarak bırakılmalıdır.

![AI kaynak öznitelikleri](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource-attrib.png)

Gerekli bilgileri doldurduktan sonra, kaynağı sağlamak için **Oluştur**'a tıklayın; bu bir dakika kadar sürecektir.
<!-- When completed, navigate to the newly deployed resource, and find the "Instrumentation Key" (visible in the "Essentials" drop down section). Copy it to clipboard, since we will need it in the next step. -->

## <a name="add-application-insights-to-the-applications-services"></a>Uygulamanın hizmetlerine Application Insights ekleme

Başlat menüsünde Visual Studio simgesine sağ tıklayıp seçerek Visual Studio 2019 yükseltilmiş ayrıcalıklarla başlatın **yönetici olarak çalıştır**. **Dosya** > **Aç** > **Proje/Çözüm**'e tıklayın ve Oylama uygulamasına gidin (öğreticinin birinci bölümde oluşturulmuştur veya git kopyasıdır). Açık *Voting.sln*. Uygulamanın NuGet paketlerini geri yüklemek isteyip istemediğiniz sorulduğunda tıklayın **Evet**.

Hem VotingWeb hem de VotingData hizmetlerinde Application Insights'ı yapılandırmak için aşağıdaki adımları izleyin:

1. Hizmetin adına sağ tıklayın ve **Ekle > bağlı hizmetler > Application Insights ile izleme**.

    ![AI kaynağını yapılandırma](./media/service-fabric-tutorial-monitoring-aspnet/configure-ai.png)
>[!NOTE]
>Proje türüne bağlı olarak, hizmet adını sağ tıklatın, tıklaymanız gerekebilir, Ekle -> Application Insights Telemetrisi...

2. Tıklayın **başlama**.
3. Azure aboneliğinizi ayarlama ve Application Insights kaynağını oluşturduğunuz aboneliği seçmek için kullanılan hesap için oturum açın. "Kaynak" açılan listesindeki *Mevcut Application Insights kaynağı*'nın altında kaynağı bulun. **Kaydet**'e tıklayarak hizmetinize Application Insights'ı ekleyin.

    ![AI kaynağını kaydetme](./media/service-fabric-tutorial-monitoring-aspnet/register-ai.png)

4. Açılan iletişim kutusu eylemi tamamladığında **Son**'a tıklayın.

> [!NOTE]
> Uygulamanızda Application Insights'ı yapılandırmayı tamamlamak için uygulamanın **her iki** hizmeti için de yukarıdaki adımları gerçekleştirdiğinizden emin olun.
> Hizmetler arasındaki gelen ve giden istekleri ve iletişimi görmek için her iki hizmette de aynı Application Insights kaynağı kullanılır.

## <a name="add-the-microsoftapplicationinsightsservicefabricnative-nuget-to-the-services"></a>Hizmetlere Microsoft.ApplicationInsights.ServiceFabric.Native NuGet'ini ekleme

Application Insights'ın senaryoya bağlı olarak kullanılabilecek Service Fabric'e özgü iki NuGet'i vardır. Biri Service Fabric'in yerel hizmetleriyle, diğeri de kapsayıcılar ve konuk yürütülebilir dosyalarıyla kullanılır. Bizim durumumuzda, getirdiği hizmet bağlamı anlayışından yararlanmak için Microsoft.ApplicationInsights.ServiceFabric.Native NuGet'ini kullanacağız. Application Insights SDK'sı ve Service Fabric'e özgü NuGet'ler hakkındaki diğer yazıları okumak için, bkz. [Service Fabric için Microsoft Application Insights](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md).

NuGet paketini adımları şunlardır:

1. Sağ **çözüm 'Oylama'** Çözüm Gezgini ve tıklatın çok üst **çözüm için NuGet paketlerini Yönet...** .
2. "NuGet - Çözüm" penceresinin üst gezinti menüsünde **Gözat**'a tıklayın ve arama çubuğunun yanındaki **Ön sürümü dahil et** kutusunu işaretleyin.
>[!NOTE]
>Application Insights paketini yüklemeden önce, önceden yüklenmemişse Microsoft.ServiceFabric.Diagnostics.Internal paketini benzer şekilde yüklemeniz gerekebilir

3. `Microsoft.ApplicationInsights.ServiceFabric.Native` için arama yapın ve uygun NuGet paketine tıklayın.
4. Sağ tarafta, uygulamadaki iki hizmet yanındaki iki onay kutularını tıklayın **VotingWeb** ve **VotingData** tıklatıp **yükleme**.
    ![AI sdk Nuget](./media/service-fabric-tutorial-monitoring-aspnet/ai-sdk-nuget-new.png)
5. Tıklayın **Tamam** üzerinde *Değişiklikleri Önizle* iletişim kutusu görünür ve kabul *lisans kabulü*. Bu noktada NuGet'i hizmetlere ekleme işlemi tamamlanır.
6. Şimdi iki hizmette telemetri başlatıcısını ayarlamanız gerekir. Bunu yapmak için açık *VotingWeb.cs* ve *VotingData.cs*. Her ikisinde de aşağıdaki iki adımı izleyin:
    1. Bu iki ekleme *kullanarak* deyimini her üst  *\<HizmetAdı > .cs*, varolan sonra *kullanarak* deyimleri:

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.ServiceFabric;
    ```

    2. İç içe her iki dosyada *dönüş* deyiminin *Createservicereplicalisteners()* veya *altındaki*altında  *Createservicereplicalisteners()*  > *Hizmetleri*, bildirilen diğer tekil hizmetleriyle ekleyin:
    ```csharp
    .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext))
    ```
    Bu, telemetrinize *Hizmet Bağlamı* ekleyerek Application Insights'da telemetrinizin kaynağını daha iyi anlamanızı sağlar. *VotingWeb.cs*'deki iç içe *return* deyiminiz şöyle görünmelidir:

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<HttpClient>(new HttpClient())
                .AddSingleton<FabricClient>(new FabricClient())
                .AddSingleton<StatelessServiceContext>(serviceContext)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext)))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
        .UseUrls(url)
        .Build();
    ```

    Benzer biçimde, *VotingData.cs*'de şu bölüm olmalıdır:

    ```csharp
    return new WebHostBuilder()
        .UseKestrel()
        .ConfigureServices(
            services => services
                .AddSingleton<StatefulServiceContext>(serviceContext)
                .AddSingleton<IReliableStateManager>(this.StateManager)
                .AddSingleton<ITelemetryInitializer>((serviceProvider) => FabricTelemetryInitializerExtension.CreateFabricTelemetryInitializer(serviceContext)))
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseStartup<Startup>()
        .UseApplicationInsights()
        .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
        .UseUrls(url)
        .Build();
    ```

İki kez kontrol `UseApplicationInsights()` yöntemi hem de çağrılır *VotingWeb.cs* ve *VotingData.cs* yukarıda da gösterildiği gibi.

>[!NOTE]
>Bu örnek uygulama, hizmetlerin iletişim kurması için http’yi kullanır. Uzaktan İletişim V2 ile bir uygulama geliştirirseniz, yukarıdakiyle aynı yere aşağıdaki kod satırlarını da eklemeniz gerekir

```csharp
ConfigureServices(services => services
    ...
    .AddSingleton<ITelemetryModule>(new ServiceRemotingDependencyTrackingTelemetryModule())
    .AddSingleton<ITelemetryModule>(new ServiceRemotingRequestTrackingTelemetryModule())
)
```

Bu noktada uygulamayı dağıtmaya hazırsınız. Üst kısımdaki **Başlat**'a (veya **F5**'e) tıklayın; Visual Studio uygulamayı derler ve paketler, yerel kümenizi ayarlar ve uygulamayı buraya dağıtır.

>[!NOTE]
>Güncel sürümü .NET Core SDK'ın yoksa bir derleme hatası alabilirsiniz.

Uygulamanın dağıtılması tamamlandığında [localhost:8080](localhost:8080) bağlantısına gidin; burada Voting Sample tek sayfalı uygulamasını görebilmelisiniz. İstediğiniz birkaç farklı öğeye oy vererek biraz örnek veri ve telemetri oluşturun; ben burada tatlıları seçtim!

![AI örnek oyları](./media/service-fabric-tutorial-monitoring-aspnet/vote-sample.png)

Birkaç oy eklemeyi tamamladığınızda, bazı oylama seçeneklerini de rahatça *kaldırabilirsiniz*.

## <a name="view-telemetry-and-the-app-map-in-application-insights"></a>Application Insights'da telemetriyi ve Uygulama haritasını görüntüleme

Azure portalında Application Insights kaynağınıza gidin.

Kaynağınızın giriş sayfasına dönmek için **Genel Bakış**'a tıklayın. Sonra üst kısımdaki **Ara**'ya tıklayarak gelen izlemelere bakın. İzlemelerin Application Insights'da gösterilmesi birkaç dakika sürer. Hiç izleme görmüyorsanız, bir dakika bekleyin ve üst kısımdaki **Yenile** düğmesine tıklayın.
![AI izlemelere bakma](./media/service-fabric-tutorial-monitoring-aspnet/ai-search.png)

*Arama* penceresini aşağı kaydırarak Application Insights'la size hazır sağlanan tüm gelen telemetriyi görebilirsiniz. Oylama uygulamasında gerçekleştirdiğiniz her eylem için *VotingWeb* hizmetinden bir giden PUT isteği (PUT Votes/Put [ad]) ve *VotingData* hizmetinden bir gelen PUT isteği (PUT VoteData/Put [ad]) olmalı, bunları görüntülenen verileri yenilemek için bir çift GET isteği izlemelidir. Ayrıca, bunlar HTTP istekleri olduğundan bir de localhost'ta HTTP için bağımlılık izlemesi olacaktır. İşte bir örnek için nasıl bir oy göreceği eklenir:

![AI örnek istek izleme](./media/service-fabric-tutorial-monitoring-aspnet/sample-request.png)

İzlemelerden birine tıklayarak bu izlemeyle ilgili diğer ayrıntıları görüntüleyebilirsiniz. İstekle ilgili Application Insights tarafından sağlanan *Yanıt Süresi* ve *İstek URL'si* gibi yararlı bilgiler vardır. Buna ek olarak, Service Fabric'e özgü NuGet'i eklediğiniz için aşağıdaki *Özel Veriler* bölümünde uygulamanız hakkında Service Fabric kümesi bağlamındaki verileri de alacaksınız. Hizmet bağlamı da bunlar arasında yer alır; dolayısıyla isteğin kaynağının *PartitionID* ve *ReplicaId* değerlerini görebilir ve uygulamanızda hataları tanılarken sorunları daha iyi yerelleştirebilirsiniz.

![AI izleme ayrıntıları](./media/service-fabric-tutorial-monitoring-aspnet/trace-details.png)

Ayrıca Genel Bakış sayfasındaki sol menüde *Uygulama haritası*’na tıklayarak veya **Uygulama haritası** simgesine tıklayarak, iki hizmetin bağlandığını gösteren Uygulama Haritası’na gidebilirsiniz.

![AI izleme ayrıntıları](./media/service-fabric-tutorial-monitoring-aspnet/app-map-new.png)

Uygulama haritası, özellikle birlikte çalışan birden çok farklı hizmet eklemeye başlarken uygulamanızın topolojisini daha iyi anlamanıza yardımcı olabilir. Ayrıca istek başarı oranlarıyla ilgili temel verileri sağlar ve başarısız isteklerde nedene sorun olduğunu anlayabilmeniz için tanılamada size yardımcı olabilir. Uygulama haritası kullanma hakkında daha fazla bilgi edinmek için bkz. [Application Insights'da Uygulama Haritası](../azure-monitor/app/app-map.md).

## <a name="add-custom-instrumentation-to-your-application"></a>Uygulamanıza özel izleme ekleme

Application Insights hazır durumda çok miktarda telemetri sağlıyor olsa da başka özel izlemeler eklemek isteyebilirsiniz. Bu iş gereksinimleriniz temelinde olabileceği gibi, uygulamanızda işler yolunda gitmediğinde tanılamayı geliştirme amacıyla da olabilir. Application Insights'ın özel olayları ve ölçümleri almak için bir API'si vardır. Bu API hakkında [burada](../azure-monitor/app/api-custom-events-metrics.md) daha fazla bilgi bulabilirsiniz.

Şimdi temel *votesDictionary* içinde oyların ne zaman eklendiğini ve silindiğini izlemek için *VoteDataController.cs*'ye (*VotingData* > *Denetleyiciler*'in altında) bazı özel olaylar ekleyelim.

1. Diğer using deyimlerinin sonuna `using Microsoft.ApplicationInsights;` ekleyin.
2. Sınıfın başında, *IReliableStateManager* oluşturmanın altında yeni bir *TelemetryClient* bildirimi ekleyin: `private TelemetryClient telemetry = new TelemetryClient();`.
3. *Put()* işlevinde, bir oy eklendiğini onaylayan bir olay ekleyin. İşlem tamamlandıktan sonra, dönüş *OkResult* deyiminin hemen altına `telemetry.TrackEvent($"Added a vote for {name}");` ekleyin.
4. *Delete()* işlevinde, *votesDictionary*'nin belirli bir oylama seçeneği için oy içermesi koşuluna dayanan bir "if/else" vardır.
    1. *if* deyimine, *await tx.CommitAsync()* öğesinden sonra silme işlemini onaylayan bir olay ekleyin: `telemetry.TrackEvent($"Deleted votes for {name}");`
    2. *else* deyimine, dönüş deyiminden önce silme işleminin yapılmadığını gösteren bir olay ekleyin: `telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");`

İşte olayları ekledikten sonra *Put()* ve *Delete()* işlevlerinizin nasıl görünebileceğini gösteren bir örnek:

```csharp
// PUT api/VoteData/name
[HttpPut("{name}")]
public async Task<IActionResult> Put(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
        await tx.CommitAsync();
    }

    telemetry.TrackEvent($"Added a vote for {name}");
    return new OkResult();
}

// DELETE api/VoteData/name
[HttpDelete("{name}")]
public async Task<IActionResult> Delete(string name)
{
    var votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

    using (ITransaction tx = this.stateManager.CreateTransaction())
    {
        if (await votesDictionary.ContainsKeyAsync(tx, name))
        {
            await votesDictionary.TryRemoveAsync(tx, name);
            await tx.CommitAsync();
            telemetry.TrackEvent($"Deleted votes for {name}");
            return new OkResult();
        }
        else
        {
            telemetry.TrackEvent($"Unable to delete votes for {name}, voting option not found");
            return new NotFoundResult();
        }
    }
}
```

Bu değişiklikleri yapmayı tamamladığınızda, en son sürümünü derlemesi ve dağıtması için uygulamayı **başlatın**. Uygulamanın dağıtımı tamamlandığında, [localhost:8080](localhost:8080) bağlantısına gidin, bazı oylama seçeneklerini ekleyin ve silin. Sonra, en son çalıştırmanın izlemelerini görmek için Application Insights kaynağınıza dönün (daha önce olduğu gibi, izlemelerin Application Insights'da gösterilmesi 1-2 dakika sürebilir). Eklediğiniz ve sildiğiniz tüm oylar için, artık tüm yanıt telemetrisiyle birlikte "Özel Olay* ifadesini görüyor olmalısınız.

![özel olaylar](./media/service-fabric-tutorial-monitoring-aspnet/custom-events.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:
> [!div class="checklist"]
> * Uygulamanız için Application Insights’ı yapılandırma
> * Hizmetler arasındaki HTTP tabanlı iletişimi izlemek için yanıt telemetrisi toplama
> * Application Insights'da Uygulama Haritası özelliğini kullanma
> * Application Insights API kullanarak özel olaylar ekleme

ASP.NET uygulamanız için izleme ve tanılama ayarlamayı tamamladığınıza göre, şimdi aşağıdakileri deneyin:

* [Service Fabric'te izleme ve tanılamayı daha ayrıntılı inceleyin](service-fabric-diagnostics-overview.md)
* [Application Insights ile Service Fabric olay analizi](service-fabric-diagnostics-event-analysis-appinsights.md)
* Application Insights hakkında daha fazla bilgi edinmek için bkz. [Application Insights Belgeleri](https://docs.microsoft.com/azure/application-insights/)
