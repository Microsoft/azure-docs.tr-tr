---
title: Azure Cloud Services için Application Insights | Microsoft Docs
description: Application Insights ile web ve çalışan rollerinizi etkili bir şekilde izleyin
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 09/05/2018
ms.openlocfilehash: 264d6d4b0b397a29b5dc1db4bb299297c1e30584
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419244"
---
# <a name="application-insights-for-azure-cloud-services"></a>Azure Cloud Services için Application Insights
[Application Insights][start] , bulut hizmetinizdeki [Azure tanılama](../agents/diagnostics-extension-overview.md) verilerle Application Insights SDK 'lardan verileri birleştirerek kullanılabilirlik, performans, başarısızlık ve kullanım için [Azure bulut hizmeti uygulamalarını](https://azure.microsoft.com/services/cloud-services/) izleyebilir. Uygulamanızın gerçek hayattaki performansı ve etkinliğine ilişkin aldığınız geri bildirimlerden yararlanarak her geliştirme yaşam döngüsünde tasarımın yönü konusunda bilinçli kararlar alabilirsiniz.

![Genel Bakış Panosu](./media/cloudservices/overview-graphs.png)

## <a name="prerequisites"></a>Önkoşullar
Başlamadan önce şunları yapmanız gerekir:

* Bir [Azure](https://azure.com) aboneliği. Windows, Xbox Live veya diğer Microsoft bulut hizmetleri için Microsoft hesabı oturum açın. 
* Microsoft Azure Tools 2,9 veya üzeri.
* Developer Analytics Tools 7,10 veya üzeri.

## <a name="get-started-quickly"></a>Hızlıca kullanmaya başlayın
Bulut hizmetinizi Application Insights ile izlemenin en hızlı ve kolay yolu, uygulamanızı Azure’da yayımlarken bu seçeneği belirlemektir.

![Örnek Tanılama ayarları sayfası](./media/cloudservices/azure-cloud-application-insights.png)

Bu seçenek, uygulamanızı çalışma zamanında gererken, Web rolünüzde istekleri, özel durumları ve bağımlılıkları izlemek için ihtiyaç duyduğunuz tüm telemetrileri sağlar. Ayrıca, çalışan rollerinizdeki performans sayaçlarını izler. Uygulamanız tarafından oluşturulan tüm tanılama izlemeleri de Application Insights gönderilir.

İhtiyacınız olan bu seçenek varsa, işiniz bitti demektir. 

Sonraki adımlarınız, [verileri analiz ile sorgulayarak](../logs/log-query-overview.md) [uygulamanızdaki ölçümleri görüntülüyor](../essentials/metrics-charts.md). 

Tarayıcıdaki performansı izlemek için, [kullanılabilirlik testlerini](./monitor-web-app-availability.md) ayarlamak ve [Web tarayıcınıza kod eklemek](./javascript.md)isteyebilirsiniz.

Sonraki bölümlerde aşağıdaki ek seçenekler ele alınmaktadır:

* Çeşitli bileşenlerden veri gönderin ve kaynakları ayırmak için yapılandırma yapılandırın.
* Uygulamanızdan özel telemetri ekleyin.

## <a name="sample-app-instrumented-with-application-insights"></a>Application Insights ile belgelenmiş örnek uygulama
Bu [örnek uygulamada](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService), Azure 'da barındırılan iki çalışan rolüne sahip bir bulut hizmetine Application Insights eklenir. 

Sonraki bölümde, kendi bulut hizmeti projenizi aynı şekilde nasıl uyarlayacağınızı öğreneceksiniz.

## <a name="plan-resources-and-resource-groups"></a>Kaynakları ve kaynak gruplarını planlama
Uygulamanızdaki telemetri, Application Insights türünde bir Azure kaynağında depolanır, analiz edilir ve görüntülenir. 

Her kaynak bir kaynak grubuna aittir. Kaynak grupları, maliyetleri yönetmek, takım üyelerine erişim vermek ve güncelleştirmeleri tek bir koordine işleminde dağıtmak için kullanılır. Örneğin, bir Azure bulut hizmetini ve Application Insights izleme kaynaklarını tek bir işlemde [dağıtmak için bir komut dosyası yazabilirsiniz](../../azure-resource-manager/templates/deploy-powershell.md) .

### <a name="resources-for-components"></a>Bileşenler için kaynaklar

Kaynak rolünü tanımlayan [her bir telemetri öğesine bir boyut özelliği eklemenizi](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) öneririz. Bu yaklaşımda, özel durumlar gibi ölçüm grafikleri, normalde çeşitli rollerden sayımların toplamını gösterir, ancak gerekli olduğu gibi, grafiği rol tanımlayıcısına göre segmentleyebilirsiniz. Aramaları aynı boyuta göre de filtreleyebilirsiniz. Bu alternatif, her şeyi aynı anda görüntülemeyi daha kolay hale getirir, ancak roller arasında bazı karışıklıklara de yol açabilir.

Tarayıcı telemetrisi genellikle ait olduğu sunucu tarafı web rolüyle aynı kaynağa dahil edilir.

Çeşitli bileşenler için Application Insights kaynaklarını tek bir kaynak grubuna yerleştirin. Bu yaklaşım, bunları birlikte yönetmeyi kolaylaştırır. 

### <a name="separate-development-test-and-production"></a>Geliştirme, test ve üretimi ayırma
Bir önceki sürümünüz yayındayken yen özelliğiniz için özel olaylar geliştiriyorsanız, geliştirme telemetrisini ayrı bir Application Insights kaynağına göndermeniz mantıklı olur. Aksi halde, canlı sitedeki tüm trafik arasında test telemetrinizi bulmak zor olabilir.

Bu durumdan kaçınmak için, sisteminizde her derleme yapılandırması veya "damga" (geliştirme, test, üretim vb.) için ayrı kaynaklar oluşturun. Her derleme yapılandırmasına ait kaynakları ayrı bir kaynak grubuna ekleyin. 

Telemetriyi uygun kaynaklara göndermek için, yapı yapılandırmasına bağlı olarak, Application Insights SDK 'sını farklı bir izleme anahtarı olacak şekilde ayarlayabilirsiniz. 

Farklı aşamalar için [izleme anahtarını dinamik olarak ayarlamayı](./separate-resources.md#dynamic-ikey) öğrenin. 

## <a name="create-an-application-insights-resource-for-each-role"></a>Her rol için bir Application Insights kaynağı oluşturma

Her rol için ayrı bir kaynak oluşturmaya karar verdiyseniz ve her derleme yapılandırması için ayrı bir küme oluşturmak isterseniz, bunların Application Insights portalında oluşturulması en kolay yoldur. Kaynakları çok büyük bir şekilde oluşturursanız, [işlemi otomatikleştirebilirsiniz](./powershell.md).

1. [Azure Portal][portal] **Yeni**  >  **Geliştirici Hizmetleri**  >  **Application Insights**' ni seçin.  

    ![Application Insights bölmesi](./media/cloudservices/01-new.png)

1. **Uygulama türü** aşağı açılan listesinde **ASP.NET Web uygulaması**' nı seçin.

Her kaynak bir izleme anahtarı ile tanımlanır. SDK yapılandırmasını el ile yapılandırmak veya doğrulamak istiyorsanız, bu anahtara daha sonra ihtiyacınız olacaktır.


## <a name="set-up-azure-diagnostics-for-each-role"></a>Her rol için Azure Tanılama ayarlama
Uygulamanızı Application Insights ile izlemek için bu seçeneği ayarlayın. Web rolleri için bu seçenek performans izleme, uyarılar, Tanılamalar ve kullanım analizi sağlar. Diğer roller için, yeniden başlatma, performans sayaçları ve System. Diagnostics. Trace çağrıları gibi Azure Tanılama arayabilir ve izleyebilirsiniz. 

1. Visual Studio Çözüm Gezgini **\<YourCloudService>**  >  **Roller** altında her rolün özelliklerini açın.

1. **Yapılandırma** bölümünde **tanılama verilerini Application Insights gönder** onay kutusunu seçin ve ardından daha önce oluşturduğunuz Application Insights kaynağını seçin.

Her derleme yapılandırması için ayrı bir Application Insights kaynağı kullanmaya karar verdiyseniz önce yapılandırmayı seçin.

![Application Insights'ı Yapılandırma](./media/cloudservices/configure-azure-diagnostics.png)

Bu, Application Insights izleme anahtarlarınızın ServiceConfiguration adlı dosyalara eklenmesi etkisine sahiptir *. \* cscfg*. [Örnek kod](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/AzureEmailService/ServiceConfiguration.Cloud.cscfg)aşağıda verilmiştir.

Application Insights gönderilen tanılama bilgileri düzeyini değiştirmek istiyorsanız, [ *. cscfg* dosyalarını doğrudan düzenleyerek](../agents/diagnostics-extension-to-application-insights.md)bunu yapabilirsiniz.

## <a name="install-the-sdk-in-each-project"></a><a name="sdk"></a>Her projede SDK’yı yükleyin
Bu seçenekle, herhangi bir role özel iş telemetrisi ekleyebilirsiniz. Bu seçenek, uygulamanızın nasıl kullanıldığı ve gerçekleştirdiği hakkında daha yakından bir analiz sağlar.

Visual Studio’da her bulut uygulaması projesi için Application Insights SDK’sını yapılandırın.

1. **Web rollerini** yapılandırmak için projeye sağ tıklayın ve ardından **Application Insights Yapılandır** ' ı veya **> Application Insights telemetri Ekle**' yi seçin.

1. **Çalışan rollerini** yapılandırmak için: 

    a. Projeye sağ tıklayın ve ardından **NuGet Paketlerini Yönet**' i seçin.

    b. [Windows Sunucuları için Application Insights](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)’ı ekleyin.

1. SDK 'Yı Application Insights kaynağına veri gönderecek şekilde yapılandırmak için:

    a. Uygun bir başlangıç işlevinde, *. cscfg* dosyasındaki yapılandırma ayarından izleme anahtarını ayarlayın:
 
    ```csharp
        TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
        configuration.InstrumentationKey = RoleEnvironment.GetConfigurationSettingValue("APPINSIGHTS_INSTRUMENTATIONKEY");
        var telemetryClient = new TelemetryClient(configuration);
    ```
   
    b. Uygulamanızdaki her bir rol için "adım a" tekrarlayın. Örneklere bakın:
   
    * [Web rolü](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Global.asax.cs#L27)
    * [Çalışan rolü](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L232)
    * [Web sayfaları için](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Views/Shared/_Layout.cshtml#L13) 

1. *ApplicationInsights.config* dosyasını her zaman çıkış dizinine kopyalanacak şekilde ayarlayın.

   *. Config* dosyasındaki bir ileti, izleme anahtarını buraya yerleştirmenizi ister. Ancak, bulut uygulamaları için *. cscfg* dosyasından ayarlamak daha iyidir. Bu yaklaşım, rolün portalda doğru şekilde tanımlanmasını sağlar.

## <a name="set-up-status-monitor-to-collect-full-sql-queries-optional"></a>Tam SQL sorguları toplamak için Durum İzleyicisi ayarlama (isteğe bağlı)

Bu adım yalnızca .NET Framework üzerinde tam SQL sorguları yakalamak istiyorsanız gereklidir. 

1. `\*.csdef`Şuna benzer her bir rol için dosya ekleme [Başlangıç görevi](../../cloud-services/cloud-services-startup-tasks.md) 

    ```xml
    <Startup>
      <Task commandLine="AppInsightsAgent\InstallAgent.bat" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="ApplicationInsightsAgent.DownloadLink" value="http://go.microsoft.com/fwlink/?LinkID=522371" />
          <Variable name="RoleEnvironment.IsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
    
2. [InstallAgent.bat](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.bat) ve [InstallAgent.ps1](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/AppInsightsAgent/InstallAgent.ps1)indirin, `AppInsightsAgent` her rol projesinde klasöre yerleştirin. Visual Studio dosya özellikleri veya derleme betikleri aracılığıyla bunları çıkış dizinine kopyalamadığınızdan emin olun.

3. Tüm çalışan rollerinde, ortam değişkenleri ekleyin: 

    ```xml
      <Environment>
        <Variable name="COR_ENABLE_PROFILING" value="1" />
        <Variable name="COR_PROFILER" value="{324F817A-7420-4E6D-B3C1-143FBED6D855}" />
        <Variable name="MicrosoftInstrumentationEngine_Host" value="{CA487940-57D2-10BF-11B2-A3AD5A13CBC0}" />
      </Environment>
    ```
    
## <a name="run-and-publish-the-app"></a>Uygulamayı çalıştırma ve yayımlama

1. Uygulamanızı çalıştırın ve Azure 'da oturum açın. 

1. Oluşturduğunuz Application Insights kaynaklarını açın.

   Tek tek veri noktaları [arama][diagnostic]bölümünde görüntülenir ve toplu veriler [Ölçüm Gezgini](../essentials/metrics-charts.md)' nde görüntülenir.

1. Daha fazla telemetri ekleyin (sonraki bölümlere bakın) ve ardından canlı tanılama ve kullanım geri bildirimi almak için uygulamanızı yayımlayın. 

Veri yoksa, şunları yapın:

1. Olayları tek tek görüntülemek için [arama][diagnostic] kutucuğunu açın.
1. Uygulamada, bazı telemetri oluşturacak şekilde çeşitli sayfalar açın.
1. Birkaç saniye bekleyip **Yenile**' ye tıklayın.  

Daha fazla bilgi için bkz. [Sorun giderme][qna].

## <a name="view-azure-diagnostics-events"></a>Azure Tanılama olaylarını görüntüle
[Azure tanılama](../agents/diagnostics-extension-overview.md) bilgilerini aşağıdaki konumlarda Application Insights bulabilirsiniz:

* Performans sayaçları özel ölçümler olarak görüntülenir. 
* Windows olay günlükleri izlemeler ve özel olaylar olarak gösterilir.
* Uygulama günlükleri, ETW günlükleri ve varsa tanılama altyapısı günlükleri izlemeler olarak görünür.

Performans sayaçlarını ve olay sayılarını görüntülemek için [Ölçüm Gezgini](../essentials/metrics-charts.md) açın ve aşağıdaki grafiği ekleyin:

![Azure Tanılama verileri](./media/cloudservices/23-wad.png)

Azure Tanılama tarafından gönderilen çeşitli izleme günlüklerinde arama yapmak için [arama](./diagnostic-search.md) veya [analiz sorgusu](../logs/log-analytics-tutorial.md)kullanın. Örneğin, bir rolün kilitlenmesine ve geri dönüştürülmesine neden olan işlenmeyen bir özel durum olduğunu varsayalım. Bu bilgi, Windows Olay Günlüğü’nün Uygulama kanalında görünür. Windows olay günlüğü hatasını görüntülemek ve özel durum için tam yığın izlemesini almak üzere ara ' yı kullanabilirsiniz. Bunun yapılması, sorunun asıl nedenini bulmanıza yardımcı olur.

![Azure Tanılama arama](./media/cloudservices/25-wad.png)

## <a name="more-telemetry"></a>Daha fazla telemetri
Sonraki bölümlerde, uygulamanızın çeşitli yönlerini nasıl daha fazla telemetri alacağınız ele alınmaktadır.

## <a name="track-requests-from-worker-roles"></a>Çalışan rollerinin isteklerini izleme
Web rollerinde, istek modülü otomatik olarak HTTP istekleriyle ilgili verileri toplar. Varsayılan koleksiyon davranışını nasıl geçersiz kılabileceğiniz hakkında örnekler için bkz. [Sample MVCWebRole](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole). 

Çalışan rollerine yapılan çağrıları HTTP istekleriyle aynı yöntemle izleyerek bunların performansını yakalayabilirsiniz. Application Insights’ta İstek telemetri türü, zamanlanabilen ve bağımsız olarak başarılı ya da başarısız olabilen adlandırılmış sunucu tarafı işin bir birimini ölçer. HTTP istekleri SDK tarafından otomatik olarak yakalansa da, çalışan rollerine yönelik istekleri izlemek için kendi kodunuzu ekleyebilirsiniz.

İstekleri raporlamak için belgelenmiş iki örnek çalışan rolüne bakın: 
* [WorkerRoleA](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleA)
* [WorkerRoleB](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/WorkerRoleB)

## <a name="exceptions"></a>Özel durumlar
Çeşitli Web uygulaması türlerinden işlenmeyen özel durumları toplama hakkında daha fazla bilgi için bkz. [Application Insights özel durumları izleme](./asp-net-exceptions.md).

Örnek web rolü, MVC5 ve Web API 2 denetleyicilerine sahiptir. Bu ikisinden toplanan işlenmemiş özel durumlar aşağıdaki işleyicilerle yakalanır:

* [Bu örnekte gösterildiği gibi](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/FilterConfig.cs#L12) , MVC5 denetleyicileri Için ayarlanan [Aihandleerrorattribute](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiHandleErrorAttribute.cs) 
* [AiWebApiExceptionLogger](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/Telemetry/AiWebApiExceptionLogger.cs) [Bu örnekte GÖSTERILDIĞI gibi](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/MvcWebRole/App_Start/WebApiConfig.cs#L25) Web API 2 denetleyicileri için ayarlanır 

Çalışan rolleri için özel durumları iki şekilde izleyebilirsiniz:

* TrackException (Ex) kullanın.
* Application Insights Trace Listener NuGet paketini eklediyseniz, [Bu örnekte gösterildiği gibi](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L107)özel durumları günlüğe kaydetmek için System. Diagnostics. Trace ' i kullanabilirsiniz.

## <a name="performance-counters"></a>Performans sayaçları
Aşağıdaki sayaçlar varsayılan olarak toplanır:

* \Process (?? APP_WIN32_PROC??) \% İşlemci zamanı
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% Processor Time

Web rolleri için aşağıdaki sayaçlar da toplanır:

* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests/Sec
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Request Execution Time
* \ASP.NET Applications(??APP_W3SVC_PROC??)\Requests In Application Queue

*ApplicationInsights.config* , [Bu örnekte gösterildiği gibi](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/ApplicationInsights.config#L14)düzenleyerek ek özel veya diğer Windows performans sayaçları belirtebilirsiniz.

  ![Performans sayaçları](./media/cloudservices/002-servers.png)

## <a name="correlated-telemetry-for-worker-roles"></a>Çalışan rolleri için bağıntılı telemetri
Zengin bir tanılama deneyimi için, başarısız veya yüksek gecikme süresine yönelik ne olduğunu görüntüleyebilirsiniz. Web rolleriyle, SDK otomatik olarak ilgili telemetri arasında bir bağıntı kurar. 

Çalışan rolleri için bu görünümü elde etmek üzere, tüm telemetri için ortak bir Operation.Id bağlamı özniteliği ayarlamak üzere özel bir telemetri başlatıcısı kullanabilirsiniz. Bunun yapılması, gecikme veya hata sorununun bir bağımlılık ya da kodunuzun kaynaklanıp kaynaklanmadığını bir bakışta görüntülemenizi sağlar. 

Aşağıdaki adımları uygulayın:

* [Bu örnekte gösterildiği gibi](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L36), bağıntıkimliği öğesini bir CallContext olarak ayarlayın. Bu durumda, Istek KIMLIĞINI CorrelationId olarak kullanıyoruz.
* Operation.Id öğesini daha önce ayarlanmış olan CorrelationId 'ye ayarlamak için özel bir Telemetryınitializer uygulamasını ekleyin. Bir örnek için bkz. [ıtemcorrelationtelemetryınitializer](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/Telemetry/ItemCorrelationTelemetryInitializer.cs#L13).
* Özel telemetri başlatıcısını ekleyin. Bunu, [Bu örnekte gösterildiği gibi](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/Samples/AzureEmailService/WorkerRoleA/WorkerRoleA.cs#L233) *ApplicationInsights.config* dosyasında veya kodda yapabilirsiniz.

## <a name="client-telemetry"></a>İstemci telemetrisi
Sayfa görüntüleme sayısı, sayfa yükleme süreleri veya betik özel durumları gibi tarayıcı tabanlı telemetri almak ve sayfa betiklerinizde özel telemetri yazmak için, bkz. [Web sayfanıza JavaScript SDK 'Sı ekleme][client].

## <a name="availability-tests"></a>Kullanılabilirlik testleri
Uygulamanızın canlı ve yanıt verdiğini cağından emin olmak için [Web testleri ayarlayın][availability].

## <a name="display-everything-together"></a>Her şeyi birlikte görüntüleme
Sisteminizin genel bir resmi için, anahtar izleme grafiklerini bir [panoda](./overview-dashboard.md)birlikte görüntüleyebilirsiniz. Örneğin, her rolün istek ve hata sayılarını sabitleyebilirsiniz. 

Sisteminiz Stream Analytics gibi diğer Azure hizmetlerini kullanıyorsa, bunların izleme grafiklerini de dahil edin. 

İstemci mobil uygulamanız varsa, [App Center](../app/mobile-center-quickstart.md) kullanın. [Analiz](../logs/log-query-overview.md)’de olay sayılarını görüntüleyecek sorgular oluşturun ve bunları panoya sabitleyin.

## <a name="example"></a>Örnek
[Örnek](https://github.com/MohanGsk/ApplicationInsights-Home/tree/master/Samples/AzureEmailService), bir web rolü ve iki çalışan rolüne sahip bir hizmeti izler.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Azure Cloud Services 'da çalışan "yöntem bulunamadı" özel durumu
.NET 4.6 için mi oluşturdunuz? .NET 4,6, Azure bulut hizmetleri rollerinde otomatik olarak desteklenmez. Uygulamanızı çalıştırmadan önce [her role .net 4,6 ' i yükler](../../cloud-services/cloud-services-dotnet-install-dotnet.md) .

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Tanılama verilerinin Application Insights’a gönderimini yapılandırma](../agents/diagnostics-extension-to-application-insights.md)
* [Application Insights kaynaklarını otomatik olarak oluştur](./powershell.md)
* [Azure Tanılama Otomatikleştir](./powershell-azure-diagnostics.md)
* [Azure İşlevleri](https://github.com/christopheranderson/azure-functions-app-insights-sample)

[api]: ./api-custom-events-metrics.md
[availability]: ./monitor-web-app-availability.md
[azure]: ./app-insights-overview.md
[client]: ./javascript.md
[diagnostic]: ./diagnostic-search.md
[netlogs]: ./asp-net-trace-logs.md
[portal]: https://portal.azure.com/
[qna]: ../faq.md
[redfield]: ./monitor-performance-live-website-now.md
[start]: ./app-insights-overview.md