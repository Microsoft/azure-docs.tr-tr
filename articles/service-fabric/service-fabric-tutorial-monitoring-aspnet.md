---
title: ASP.NET Core hizmetlerini izleme ve tanılama
description: Bu öğreticide, Azure Service Fabric ASP.NET Core uygulaması için izleme ve tanılamayı yapılandırmayı öğrenin.
ms.topic: tutorial
ms.date: 07/10/2019
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: e7fe68c2d0c51ffcc67693da722d9243ea3506f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91840804"
---
# <a name="tutorial-monitor-and-diagnose-an-aspnet-core-application-on-service-fabric-using-application-insights"></a>Öğretici: Application Insights'ı kullanarak Service Fabric'te ASP.NET Core uygulamasını izleme ve tanılama

Bu öğretici, bir serinin beşinci kısmıdır. Application Insights kullanarak Service Fabric kümesinde çalışan ASP.NET Core bir uygulama için izleme ve tanılamayı yapılandırma adımlarını adım adım açıklar. Öğreticinin ilk bölümü olan [.NET Service Fabric uygulaması oluşturma](service-fabric-tutorial-create-dotnet-app.md) bölümünde geliştirilen uygulamadan telemetri toplarız.

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
> * [ASP.NET Core ön uç hizmetine HTTPS uç noktası ekleme](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Azure Pipelines kullanarak CI/CD yapılandırma](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * Uygulama için izleme ve tanılamayı ayarlama

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiye başlamadan önce:

* Azure aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun
* [Visual Studio 2019](https://www.visualstudio.com/) ' i yükleyip **Azure geliştirme** ve **ASP.net ve Web geliştirme** iş yüklerini yüklersiniz.
* [Service Fabric SDK 'sını yükler](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Voting örnek uygulamasını indirme

[Bu öğretici serisinin birinci kısmında](service-fabric-tutorial-create-dotnet-app.md)oylama örnek uygulamasını oluşturmadıysanız, indirebilirsiniz. Komut penceresinde veya terminalde, örnek uygulama deposunu yerel makinenize kopyalamak için aşağıdaki komutu çalıştırın.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-an-application-insights-resource"></a>Application Insights kaynağını ayarlama

Application Insights, Azure'un uygulama performansı yönetim platformu olduğu gibi, Service Fabric'in de uygulama izleme ve tanılama için önerilen platformudur.

Application Insights kaynağı oluşturmak için [Azure Portal](https://portal.azure.com)'a gidin. Sol gezinti menüsünde **kaynak oluştur** ' u seçerek Azure Marketi ' ni açın. **İzleme ve yönetim** ve ardından **Application Insights** seçin.

![Yeni AI kaynağı oluşturma](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource.png)

Şimdi oluşturulacak kaynağın öznitelikleri hakkındaki bilgileri doldurmanız gerekir. Uygun *Ad*, *Kaynak Grubu* ve *Abonelik* bilgilerini girin. *Konum* olarak gelecekte Service Fabric kümenizi dağıtabileceğiniz yeri ayarlayın. Bu öğreticide, uygulamayı yerel kümede dağıtacağımız için *Konum* alanı bizim konumuzla ilgili değildir. *Uygulama Türü* "ASP.NET web uygulaması" olarak bırakılmalıdır.

![AI kaynak öznitelikleri](./media/service-fabric-tutorial-monitoring-aspnet/new-ai-resource-attrib.png)

Gerekli bilgileri doldurduktan sonra, kaynağı sağlamak için **Oluştur** ' u seçin; yaklaşık bir dakika sürer.
<!-- When completed, navigate to the newly deployed resource, and find the "Instrumentation Key" (visible in the "Essentials" drop down section). Copy it to clipboard, since we will need it in the next step. -->

## <a name="add-application-insights-to-the-applications-services"></a>Uygulamanın hizmetlerine Application Insights ekleme

Başlat menüsünde Visual Studio simgesine sağ tıklayıp **yönetici olarak çalıştır**' ı seçerek visual Studio 2019 ' i yükseltilmiş ayrıcalıklarla başlatın. **Dosya**  >  **Aç**  >  **Proje/çözüm** ' ü seçin ve oylama uygulamasına gidin (öğreticinin veya git kopyalanan bölümünde oluşturulur). *Oylama. sln*'yi açın. Uygulamanın NuGet paketlerini geri yüklemeniz istenirse **Evet**' i seçin.

Hem VotingWeb hem de VotingData Hizmetleri için Application Insights yapılandırmak için aşağıdaki adımları izleyin:

1. Hizmetin adına sağ tıklayın ve **Application Insights ile > izleme > bağlı hizmetleri Ekle**' yi seçin.

    ![AI kaynağını yapılandırma](./media/service-fabric-tutorial-monitoring-aspnet/configure-ai.png)
>[!NOTE]
>Proje türüne bağlı olarak, hizmetin adına sağ tıkladığınızda, Add-> Application Insights Telemetri... seçeneğini belirlemeniz gerekebilir.

2. **Kullanmaya** başlayın ' ı seçin.
3. Azure aboneliğiniz için kullandığınız hesapta oturum açın ve Application Insights kaynağını oluşturduğunuz aboneliği seçin. "Kaynak" açılan listesindeki *Mevcut Application Insights kaynağı*'nın altında kaynağı bulun. Hizmetinize Application Insights eklemek için **Kaydol** ' u seçin.

    ![AI kaynağını kaydetme](./media/service-fabric-tutorial-monitoring-aspnet/register-ai.png)

4. Açılan iletişim kutusu eylemi tamamladığında **Son**'a tıklayın.

> [!NOTE]
> Uygulamanızda Application Insights'ı yapılandırmayı tamamlamak için uygulamanın **her iki** hizmeti için de yukarıdaki adımları gerçekleştirdiğinizden emin olun.
> Hizmetler arasındaki gelen ve giden istekleri ve iletişimi görmek için her iki hizmette de aynı Application Insights kaynağı kullanılır.

## <a name="add-the-microsoftapplicationinsightsservicefabricnative-nuget-to-the-services"></a>Hizmetlere Microsoft.ApplicationInsights.ServiceFabric.Native NuGet'ini ekleme

Application Insights'ın senaryoya bağlı olarak kullanılabilecek Service Fabric'e özgü iki NuGet'i vardır. Biri Service Fabric'in yerel hizmetleriyle, diğeri de kapsayıcılar ve konuk yürütülebilir dosyalarıyla kullanılır. Bizim durumumuzda, getirdiği hizmet bağlamı anlayışından yararlanmak için Microsoft.ApplicationInsights.ServiceFabric.Native NuGet'ini kullanacağız. Application Insights SDK ve Service Fabric özgü NuGet paketleri hakkında daha fazla bilgi edinmek için bkz. [Microsoft Application Insights Service Fabric için](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md).

NuGet paketini ayarlama adımları aşağıda verilmiştir:

1. Çözüm Gezgini en üstündeki **' oylama ' çözümüne** sağ tıklayın ve **çözüm Için NuGet Paketlerini Yönet..**. seçeneğini belirleyin.
2. "NuGet-çözüm" penceresinin üst gezinti menüsünde **göz at** ' ı seçin ve arama çubuğunun yanındaki **ön sürümü dahil et** kutusunu işaretleyin.
>[!NOTE]
>Application Insights paketini yüklemeden önce, önceden yüklenmemişse Microsoft.ServiceFabric.Diagnostics.Internal paketini benzer şekilde yüklemeniz gerekebilir

3. `Microsoft.ApplicationInsights.ServiceFabric.Native`Uygun NuGet paketini arayın ve seçin.
4. Sağ tarafta, uygulamadaki iki hizmetin yanında bulunan iki onay kutusunu **seçin ve** sonra da **Install** **' ı** seçin.
    ![AI sdk Nuget](./media/service-fabric-tutorial-monitoring-aspnet/ai-sdk-nuget-new.png)
5. Görüntülenen *Değişiklikleri Önizle* Iletişim kutusunda **Tamam** ' ı seçin ve *Lisans kabulünü* kabul edin. Bu noktada NuGet'i hizmetlere ekleme işlemi tamamlanır.
6. Şimdi iki hizmette telemetri başlatıcısını ayarlamanız gerekir. Bunu yapmak için *Votingweb. cs* ve *votingdata. cs*' yi açın. Her ikisinde de aşağıdaki iki adımı izleyin:
    1. Bu iki *using* deyimini, var olan *using* deyimlerinden sonra her *\<ServiceName> . cs*' nin üst kısmına ekleyin:

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.ServiceFabric;
    ```

    2. Her iki dosyada, *createserviceınstancelisteners ()* veya *CreateServiceReplicaListeners ()* iç içe geçmiş *Return* ifadesinde, *ConfigureServices*  >  *Services* altında, belirtilen diğer hizmet ile, şunu ekleyin:
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

`UseApplicationInsights()`Metodun, yukarıda gösterildiği gibi *Votingweb. cs* ve *votingdata. cs* içinde çağrıldığından emin olun.

>[!NOTE]
>Bu örnek uygulama, hizmetlerin iletişim kurması için http’yi kullanır. Uzaktan İletişim V2 ile bir uygulama geliştirirseniz, yukarıdakiyle aynı yere aşağıdaki kod satırlarını da eklemeniz gerekir

```csharp
ConfigureServices(services => services
    ...
    .AddSingleton<ITelemetryModule>(new ServiceRemotingDependencyTrackingTelemetryModule())
    .AddSingleton<ITelemetryModule>(new ServiceRemotingRequestTrackingTelemetryModule())
)
```

Bu noktada uygulamayı dağıtmaya hazırsınız. En üstte (veya **F5**) **Başlat** ' ı seçin ve Visual Studio uygulamayı derleyip paketleyebilir, yerel kümenizi ayarlar ve uygulamayı buna dağıtır.

>[!NOTE]
>.NET Core SDK yüklü olan güncel bir sürümüne sahip değilseniz, derleme hatası alabilirsiniz.

Uygulamanın dağıtımı tamamlandıktan sonra, `localhost:8080` Oylama örnek tek sayfalı uygulamayı görmeniz gereken yere gidin. İstediğiniz birkaç farklı öğeye oy vererek biraz örnek veri ve telemetri oluşturun; ben burada tatlıları seçtim!

![AI örnek oyları](./media/service-fabric-tutorial-monitoring-aspnet/vote-sample.png)

Birkaç oy eklemeyi tamamladığınızda, bazı oylama seçeneklerini de rahatça *kaldırabilirsiniz*.

## <a name="view-telemetry-and-the-app-map-in-application-insights"></a>Application Insights'da telemetriyi ve Uygulama haritasını görüntüleme

Azure portalında Application Insights kaynağınıza gidin.

Kaynağınızın giriş sayfasına geri dönmek için **Genel Bakış ' ı** seçin. Ardından en üstteki **Ara** ' yı seçerek içindeki izlemeleri görüntüleyin. İzlemelerin Application Insights'da gösterilmesi birkaç dakika sürer. Hiç izleme görmüyorsanız, bir dakika bekleyin ve üst kısımdaki **Yenile** düğmesine tıklayın.
![AI izlemelere bakma](./media/service-fabric-tutorial-monitoring-aspnet/ai-search.png)

*Arama* penceresini aşağı kaydırarak Application Insights'la size hazır sağlanan tüm gelen telemetriyi görebilirsiniz. Oylama uygulamasında gerçekleştirdiğiniz her eylem için *VotingWeb* hizmetinden bir giden PUT isteği (PUT Votes/Put [ad]) ve *VotingData* hizmetinden bir gelen PUT isteği (PUT VoteData/Put [ad]) olmalı, bunları görüntülenen verileri yenilemek için bir çift GET isteği izlemelidir. Ayrıca, bunlar HTTP istekleri olduğundan bir de localhost'ta HTTP için bağımlılık izlemesi olacaktır. İşte bir oyun nasıl eklendiği hakkında bir örnek aşağıda verilmiştir:

![AI örnek isteği izlemesi](./media/service-fabric-tutorial-monitoring-aspnet/sample-request.png)

İzlemelerden birini seçerek, hakkında daha fazla ayrıntı görüntüleyebilirsiniz. İstekle ilgili Application Insights tarafından sağlanan *Yanıt Süresi* ve *İstek URL'si* gibi yararlı bilgiler vardır. Buna ek olarak, Service Fabric'e özgü NuGet'i eklediğiniz için aşağıdaki *Özel Veriler* bölümünde uygulamanız hakkında Service Fabric kümesi bağlamındaki verileri de alacaksınız. Hizmet bağlamı da bunlar arasında yer alır; dolayısıyla isteğin kaynağının *PartitionID* ve *ReplicaId* değerlerini görebilir ve uygulamanızda hataları tanılarken sorunları daha iyi yerelleştirebilirsiniz.

![AI izleme ayrıntıları](./media/service-fabric-tutorial-monitoring-aspnet/trace-details.png)

Ayrıca, Genel Bakış sayfasındaki sol menüden *uygulama Haritası* ' nı seçebilir **veya uygulama Haritası simgesini seçerek** iki hizmetlerinizin bağlı olduğunu gösteren uygulama haritasını kullanabilirsiniz.

![Sol menüdeki uygulama haritasını vurgulayan ekran görüntüsü.](./media/service-fabric-tutorial-monitoring-aspnet/app-map-new.png)

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

Bu değişiklikleri yapmayı tamamladığınızda, en son sürümünü derlemesi ve dağıtması için uygulamayı **başlatın**. Uygulamanın dağıtımı tamamlandıktan sonra, üzerine gidin `localhost:8080` ve bazı oylama seçeneklerini ekleyin ve silin. Ardından, son çalıştırmanın izlemelerini görmek için Application Insights kaynağına geri dönün (daha önce olduğu gibi, izlemelerin Application Insights görünmesi için 1-2 dakika sürebilir). Eklediğiniz ve sildiğiniz tüm oylar için, artık tüm yanıt telemetrisiyle birlikte "Özel Olay* ifadesini görüyor olmalısınız.

![özel olaylar](./media/service-fabric-tutorial-monitoring-aspnet/custom-events.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:
> [!div class="checklist"]
> * Uygulamanız için Application Insights’ı yapılandırma
> * Hizmetler arasındaki HTTP tabanlı iletişimi izlemek için yanıt telemetrisi toplama
> * Application Insights'da Uygulama Haritası özelliğini kullanma
> * Application Insights API kullanarak özel olaylar ekleme

ASP.NET uygulamanız için izleme ve tanılamayı ayarladığınıza göre, şunları deneyin:

* [Service Fabric'te izleme ve tanılamayı daha ayrıntılı inceleyin](service-fabric-diagnostics-overview.md)
* [Application Insights ile Service Fabric olay analizi](service-fabric-diagnostics-event-analysis-appinsights.md)
* Application Insights hakkında daha fazla bilgi edinmek için bkz. [Application Insights Belgeleri](/azure/application-insights/)
