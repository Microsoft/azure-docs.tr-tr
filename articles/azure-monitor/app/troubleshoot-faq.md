---
title: Azure Application Insights SSS | Microsoft Docs
description: Application Insights hakkında sık sorulan sorular.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/16/2019
ms.openlocfilehash: 55a096cd4971664e55bb2cfd17f9f8927d7c32f5
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899516"
---
# <a name="application-insights-frequently-asked-questions"></a>Application Insights: sık sorulan sorular

## <a name="configuration-problems"></a>Yapılandırma sorunları
*Şunları ayarlarken sorun yaşıyorum:*

* [.NET uygulaması](asp-net-troubleshoot-no-data.md)
* [Zaten çalışan bir uygulamayı izleme](monitor-performance-live-website-now.md#troubleshoot)
* [Azure tanılama](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)
* [Java web uygulaması](java-troubleshoot.md)

*Sunucuma veri aldım*

* [Güvenlik Duvarı özel durumlarını ayarlama](ip-addresses.md)
* [ASP.NET sunucusu kurma](monitor-performance-live-website-now.md)
* [Java sunucusu kurma](java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Application Insights,... ile birlikte kullanabilir miyim?

* [Azure VM 'de veya Azure sanal makine ölçek kümesindeki bir IIS sunucusundaki Web uygulamaları](azure-vm-vmss-apps.md)
* [Bir IIS sunucusundaki Web Apps-şirket içi veya bir VM](asp-net.md)
* [Java Web uygulamaları](java-get-started.md)
* [Node.js uygulamaları](nodejs.md)
* [Azure 'da Web Apps](azure-web-apps.md)
* [Azure üzerinde Cloud Services](cloudservices.md)
* [Docker 'da çalışan uygulama sunucuları](docker.md)
* [Tek sayfalı web uygulamaları](javascript.md)
* [SharePoint](sharepoint.md)
* [Windows masaüstü uygulaması](windows-desktop.md)
* [Diğer platformlar](platforms.md)

## <a name="is-it-free"></a>Ücretsizdir mi?

Evet, deneysel kullanım için. Temel fiyatlandırma planında, uygulamanız her ay ücretsiz olarak belirli bir veri indirimi gönderebilir. Ücretsiz kullanım, geliştirme ve az sayıda kullanıcı için bir uygulama yayımlama açısından yeterince büyük. Belirtilen miktarda verinin işlenmesini engellemek için bir Cap ayarlayabilirsiniz.

Daha büyük olan telemetri birimleri GB ile ücretlendirilir. [Ücretlerinizi nasıl sınırlayacağıyla](pricing.md)ilgili bazı ipuçları sunuyoruz.

Kurumsal plan her bir gün için her bir Web sunucusu düğümünün telemetri gönderdiği bir ücret doğurur. Büyük bir ölçekte sürekli dışarı aktarmayı kullanmak istiyorsanız uygundur.

[Fiyatlandırma planını okuyun](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="how-much-does-it-cost"></a>Ücreti ne kadardır?

* Bir Application Insights kaynağındaki **kullanım ve tahmini maliyetler sayfasını** açın. Son kullanılan kullanım grafiği. İsterseniz bir veri hacmi üst sınırı belirleyebilirsiniz.
* Tüm kaynaklarda [faturanızı görmek Için Azure faturalama dikey penceresini](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) açın.

## <a name="q14"></a>Projem 'de ne Application Insights Değiştir?
Ayrıntılar proje türüne bağlıdır. Bir Web uygulaması için:

* Bu dosyaları projenize ekler:

  * ApplicationInsights. config.
  * AI. js
* Şu NuGet paketlerini yükleme:

  * *APPLICATION INSIGHTS API* -çekirdek API 'si
  * *Web uygulamaları için APPLICATION INSIGHTS API* -sunucudan telemetri göndermek için kullanılır
  * *JavaScript uygulamaları için APPLICATION INSIGHTS API* -istemciden telemetri göndermek için kullanılır

    Paketler şu derlemeleri içerir:
  * Microsoft. ApplicationInsights
  * Microsoft. ApplicationInsights. Platform
* İçine öğe ekler:

  * Web.config
  * Packages. config
* (Yalnızca yeni projeler- [var olan bir projeye Application Insights eklerseniz][start]bunu el ile yapmanız gerekir.) , Application Insights kaynak KIMLIĞI ile başlatmak için istemci ve sunucu koduna kod parçacıkları ekler. Örneğin, bir MVC uygulamasında kod ana sayfa görünümleri/paylaşılan/_Layout. cshtml dosyasına eklenir

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Nasıl yaparım? eski SDK sürümlerinden yükseltme yapılsın mı?
Uygulama türüne uygun SDK için [sürüm notlarına](release-notes.md) bakın.

## <a name="update"></a>Projem hangi Azure kaynağını veri gönderdiğini nasıl değiştirebilirim?
Çözüm Gezgini ' de, `ApplicationInsights.config` ' a sağ tıklayın ve **güncelleştirme Application Insights**' yı seçin. Verileri Azure 'da var olan veya yeni bir kaynağa gönderebilirsiniz. Güncelleştirme Sihirbazı, ApplicationInsights. config dosyasında, sunucu SDK 'sının verilerinizi nereye göndereceğini belirleyen izleme anahtarını değiştirir. "Tümünü Güncelleştir" seçeneğinin işaretini kaldırmadığınız takdirde, Web sayfalarınızda göründüğü anahtarı da değiştirecek.

## <a name="what-is-status-monitor"></a>Durum İzleyicisi nedir?

Web Apps 'te Application Insights yapılandırmaya yardımcı olması için IIS Web sunucunuzda kullanabileceğiniz bir masaüstü uygulamasıdır. Telemetri toplamaz: bir uygulamayı yapılandırmadığınızda bunu durdurabilirsiniz. 

[Daha fazla bilgi edinin](monitor-performance-live-website-now.md#questions).

## <a name="what-telemetry-is-collected-by-application-insights"></a>Application Insights tarafından hangi telemetri toplanır?

Sunucu Web Apps 'ten:

* HTTP istekleri
* [Bağımlılıklar](asp-net-dependencies.md). Çağrıları: SQL veritabanları; Dış hizmetlere HTTP çağrıları; Azure Cosmos DB, tablo, BLOB depolama ve kuyruk. 
* [Özel durumlar](asp-net-exceptions.md) ve yığın izlemeleri.
* [Performans sayaçları](performance-counters.md) - [durum İzleyicisi](monitor-performance-live-website-now.md)kullanıyorsanız, [uygulama hizmetleri için Azure izleme](azure-web-apps.md), [VM veya sanal makine ölçek kümesi için Azure izleme](azure-vm-vmss-apps.md)veya [toplanan yazıcı Application Insights](java-collectd.md).
* Kodlarınızın [özel olayları ve ölçümleri](api-custom-events-metrics.md) .
* Uygun toplayıcıyı yapılandırırsanız [izleme günlükleri](asp-net-trace-logs.md) .

[İstemci Web sayfalarından](javascript.md):

* [Sayfa görüntüleme sayısı](usage-overview.md)
* [Ajax çağrıları](asp-net-dependencies.md) Çalışan bir betikten yapılan istekler.
* Sayfa görünümü yükleme verileri
* Kullanıcı ve oturum sayıları
* [Kimliği doğrulanmış kullanıcı kimlikleri](api-custom-events-metrics.md#authenticated-users)

Diğer kaynaklardan yapılandırırsanız:

* [Azure tanılama](../platform/diagnostics-extension-to-application-insights.md)
* [Analiz 'e aktar](../platform/data-collector-api.md)
* [Log Analytics](../platform/data-collector-api.md)
* [Logstash](../platform/data-collector-api.md)

## <a name="can-i-filter-out-or-modify-some-telemetry"></a>Bir Telemetriyi filtreleyebilir veya değiştirebilir miyim?

Evet, şu sunucuda yazabilirsiniz:

* Uygulamanıza gönderilmeden önce seçili telemetri öğelerine filtre uygulamak veya özellikleri eklemek için telemetri Işlemcisi.
* Telemetri başlatıcısı tüm telemetri öğelerine özellik eklemek için.

[ASP.net](api-filtering-sampling.md) veya [Java](java-filter-telemetry.md)hakkında daha fazla bilgi edinin.

## <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Şehir, ülke/bölge ve diğer coğrafi konum verileri nasıl hesaplanır?

[GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/)kullanarak web istemcisinin IP adresini (IPv4 veya IPv6) arar.

* Tarayıcı telemetrisi: gönderenin IP adresini topladık.
* Sunucu telemetrisi: Application Insights modülü istemci IP adresini toplar. `X-Forwarded-For` ayarlandıysa toplanmaz.
* IP adresi ve coğrafi konum verilerinin nasıl toplandığı hakkında daha fazla bilgi edinmek için Application Insights bu [makaleye](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection)bakın.


`ClientIpHeaderTelemetryInitializer`, IP adresini farklı bir üst bilgiden alacak şekilde yapılandırabilirsiniz. Bazı sistemlerde, örneğin, `X-Originating-IP`için bir proxy, yük dengeleyici veya CDN tarafından taşınır. [Daha fazla bilgi edinin](https://apmtips.com/blog/2016/07/05/client-ip-address/).

İstek telemetrinizi bir haritada göstermek için [Power BI kullanabilirsiniz](export-power-bi.md ) .


## <a name="data"></a>Veriler portalda ne kadar süreyle tutulur? Güvenli mi?
[Veri saklama ve gizliliğe][data]göz atın.

## <a name="could-personal-data-be-sent-in-the-telemetry"></a>Kişisel veriler telemetriye gönderilebilir mi?

Bu, kodunuzun bu verileri göndermesi durumunda mümkündür. Yığın izlemelerindeki değişkenler kişisel verileri içeriyorsa de bu durum oluşabilir. Geliştirme ekibiniz, kişisel verilerin düzgün şekilde işlenmesini sağlamak için risk değerlendirmeleri yürütmelidir. [Veri saklama ve gizlilik hakkında daha fazla bilgi edinin](data-retention-privacy.md).

Coğrafi konum öznitelikleri arandığında, istemci Web adresinin **Tüm** sekizliklerin her zaman 0 olarak ayarlanmış olması.

## <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Izleme anahtarım, Web sayfası kaynağı 'nda görünür. 

* Bu, izleme çözümlerinde yaygın bir uygulamadır.
* Verilerinizi çalmak için kullanılamaz.
* Verilerinizi eğmek veya uyarıları tetiklemek için kullanılabilir.
* Herhangi bir müşterinin bu soruna sahip olduğunu duymadı.

Şunları yapabilirsiniz:

* İstemci ve sunucu verileri için iki ayrı Izleme anahtarı (ayrı Application Insights kaynakları) kullanın. Veya
* Sunucunuzda çalışan bir ara sunucu yazın ve web istemcisinin bu proxy üzerinden veri göndermesini sağlayabilirsiniz.

## <a name="post"></a>Nasıl yaparım? bkz. tanılama aramasında veri gönderme?
POST verilerini otomatik olarak günlüğe kaydetme, ancak bir TrackTrace çağrısını kullanabilirsiniz: verileri ileti parametresine koy. Bu, dize özelliklerindeki sınırlardan daha uzun bir boyut sınırına sahiptir, ancak bunu filtreleyememiş olursunuz.

## <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Tek veya birden çok Application Insights kaynak kullanmalı mıyım?

Tek bir iş sistemindeki tüm bileşenler veya roller için tek bir kaynak kullanın. Geliştirme, test ve sürüm sürümleri için ve bağımsız uygulamalar için ayrı kaynaklar kullanın.

* [Tartışmaya buraya bakın](separate-resources.md)
* [Örnek-çalışan ve Web rollerine sahip bulut hizmeti](cloudservices.md)

## <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>İzleme anahtarı dinamik olarak değiştirilsin mi Nasıl yaparım??

* [Burada tartışma](separate-resources.md)
* [Örnek-çalışan ve Web rollerine sahip bulut hizmeti](cloudservices.md)

## <a name="what-are-the-user-and-session-counts"></a>Kullanıcı ve oturum sayıları nelerdir?

* JavaScript SDK 'Sı, Web istemcisinde Kullanıcı tanımlama bilgisini, döndürülen kullanıcıları ve etkinlikleri gruplandırmak için bir oturum tanımlama bilgisini belirler.
* İstemci tarafı komut dosyası yoksa, [sunucuda tanımlama bilgilerini ayarlayabilirsiniz](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Bir gerçek Kullanıcı, sitenizi farklı tarayıcılarda kullanıyorsa ya da özel/veya farklı makinelere göz atmak veya farklı makineler kullanmak için birden çok kez sayılır.
* Makineler ve tarayıcılar arasında oturum açmış bir kullanıcıyı tanımlamak için, [Setadoğrulayıcısının Catedusercontext ()](api-custom-events-metrics.md#authenticated-users)çağrısı ekleyin.

## <a name="q17"></a>Application Insights her şeyi etkinleştirmem gerekir mi?
| Neleri görmeniz gerekir | Nasıl alınır? | Neden istediğiniz |
| --- | --- | --- |
| Kullanılabilirlik grafikleri |[Web testleri](monitor-web-app-availability.md) |Web uygulamanızın çalışıyor olduğunu öğrenin |
| Sunucu uygulaması performans: yanıt süreleri,... |[Projenize Application Insights ekleyin](asp-net.md) veya [AI durum İzleyicisi sunucuya](monitor-performance-live-website-now.md) (veya [bağımlılıklarını izlemek](api-custom-events-metrics.md#trackdependency)için kendi kodunuzu yazın) |Performans sorunlarını Algıla |
| Bağımlılık telemetrisi |[AI Durum İzleyicisi sunucuya yükler](monitor-performance-live-website-now.md) |Veritabanları veya diğer dış bileşenlerle ilgili sorunları tanılayın |
| Özel durumların yığın izlemelerini al |[Kodunuzda TrackException çağrıları ekleyin](asp-net-exceptions.md) (ancak bazıları otomatik olarak bildirilir) |Özel durumları Algıla ve Tanıla |
| Arama günlüğü izlemeleri |[Günlüğe kaydetme bağdaştırıcısı ekleme](asp-net-trace-logs.md) |Özel durumları ve performans sorunlarını tanılayın |
| İstemci kullanımı temelleri: sayfa görünümleri, oturumlar,... |[Web sayfalarındaki JavaScript başlatıcısı](javascript.md) |Kullanım analizi |
| İstemci özel ölçümleri |[Web sayfalarındaki çağrıları izleme](api-custom-events-metrics.md) |Kullanıcı deneyimini geliştirme |
| Sunucu özel ölçümleri |[Sunucudaki aramalar izleniyor](api-custom-events-metrics.md) |İş zekası |

## <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Arama ve ölçüm grafiklerindeki sayımlar neden eşit değildir?

[Örnekleme](sampling.md) , gerçekten uygulamanızdan portala gönderilen telemetri öğelerinin (istek, özel olay vb.) sayısını azaltır. Arama bölümünde, gerçekten alınan öğe sayısını görürsünüz. Olayların sayısını görüntüleyen ölçüm grafiklerinde, gerçekleşen özgün olay sayısını görürsünüz. 

Aktarılan her öğe, öğenin kaç tane özgün olay temsil ettiğini gösteren bir `itemCount` özelliği taşır. Örneklemede örnekleme gözlemlemek için bu sorguyu Analytics 'te çalıştırabilirsiniz:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


## <a name="automation"></a>Otomasyon

### <a name="configuring-application-insights"></a>Application Insights yapılandırma

Azure Kaynak İzleyicisi kullanarak [PowerShell betikleri yazabilirsiniz](powershell.md) :

* Application Insights kaynaklarını oluşturun ve güncelleştirin.
* Fiyatlandırma planını ayarlayın.
* İzleme anahtarını alın.
* Ölçüm uyarısı ekleyin.
* Bir kullanılabilirlik testi ekleyin.

Ölçüm Gezgini raporu ayarlayamazsınız veya sürekli dışarı aktarmayı ayarlayabilirsiniz.

### <a name="querying-the-telemetry"></a>Telemetriyi sorgulama

[Analiz](analytics.md) sorguları çalıştırmak için [REST API](https://dev.applicationinsights.io/) kullanın.

## <a name="how-can-i-set-an-alert-on-an-event"></a>Bir olayda nasıl uyarı ayarlayabilirim?

Azure uyarıları yalnızca ölçümlerde bulunur. Olaylarınız gerçekleştiğinde bir değer eşiğine geçen özel bir ölçüm oluşturun. Ardından, ölçüm üzerinde bir uyarı ayarlayın. Unutmayın: her iki yönde de ölçüm eşiğe her geçen bir bildirim alırsınız; ilk kesişene kadar, ilk değerin yüksek veya düşük olmasından bağımsız olarak bildirim almazsınız; birkaç dakika içinde her zaman bir gecikme vardır.

## <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Bir Azure Web uygulaması ve Application Insights arasında veri aktarımı ücretleri var mı?

* Azure Web uygulamanız Application Insights koleksiyon uç noktasının bulunduğu bir veri merkezinde barındırılıyorsa, ücret alınmaz. 
* Ana bilgisayar veri merkezinizde koleksiyon uç noktası yoksa, uygulamanızın telemetrisi [Azure giden ücretlerine](https://azure.microsoft.com/pricing/details/bandwidth/)tabi olur.

Bu, Application Insights kaynağınızın barındırıldığı yere bağlı değildir. Yalnızca uç noktalarımızın dağıtımına bağlıdır.

## <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Application Insights portalına telemetri gönderebilir miyim?

SDK 'larımızı kullanmanızı ve [SDK API](api-custom-events-metrics.md)'sini kullanmanızı öneririz. Çeşitli [platformlar](platforms.md)için SDK 'nın çeşitleri vardır. Bu SDK 'lar, arabelleğe alma, sıkıştırma, azaltma, yeniden deneme gibi işlemleri gerçekleştirir. Ancak, alma [şeması](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) ve [uç nokta Protokolü](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) geneldir.

## <a name="can-i-monitor-an-intranet-web-server"></a>Bir intranet Web sunucusunu izleyebilir miyim?

Evet, ancak güvenlik duvarı özel durumları ya da ara sunucu yeniden yönlendirmeleri ile hizmetlerimize giden trafiğe izin vermeniz gerekir.
- QuickPulse `https://rt.services.visualstudio.com:443` 
- Applicationıdprovider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


Hizmetlerin ve IP adreslerinin tam listesini [burada](../../azure-monitor/app/ip-addresses.md)gözden geçirin.

### <a name="firewall-exception"></a>Güvenlik Duvarı özel durumu

Web sunucunuzun, uç noktalarımıza telemetri göndermesini sağlar. 

### <a name="gateway-redirect"></a>Ağ Geçidi yönlendirmesi

Yapılandırmanızda bulunan uç noktaların üzerine yazarak intranetteki trafiği intranetinizdeki bir ağ geçidine yönlendirin.
Bu "uç nokta" özellikleri, config uygulamanızda yoksa, bu sınıflar örnek ApplicationInsights. config örneğinde aşağıda gösterilen varsayılan değerleri kullanır. 

Ağ geçidinizin trafiği bitiş noktasının temel adresine yönlendirmelidir. Yapılandırmanızda, varsayılan değerleri `http://<your.gateway.address>/<relative path>`değiştirin.


#### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Varsayılan bitiş noktaları ile örnek ApplicationInsights. config:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

_Applicationıdprovider, v 2.6.0 'dan başlayarak kullanılabilir_

### <a name="proxy-passthrough"></a>Proxy geçişi

Proxy geçişi, bir makine düzeyi veya uygulama düzeyi proxy yapılandırılarak elde edilebilir.
Daha fazla bilgi için bkz. [defaultProxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings)üzerinde DotNet makalesi.
 
 Örnek Web. config:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

## <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Bir intranet sunucusunda kullanılabilirlik Web testlerini çalıştırabilir miyim?

[Web testleriniz](monitor-web-app-availability.md) , dünyanın dört bir yanında dağıtılan varlık noktalarında çalışır. İki çözüm vardır:

* Güvenlik Duvarı kapısı-sunucunuza, [Web testi aracılarının uzun ve değiştirilebilir listesinden](ip-addresses.md)Istek yapmasına izin verin.
* İntranetinizin içinden sunucunuza düzenli istek göndermek için kendi kodunuzu yazın. Bu amaçla Visual Studio Web testlerini çalıştırabilirsiniz. Sınayıcı, sonuçları TrackAvailability () API 'sini kullanarak Application Insights gönderebilir.

## <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Telemetri toplanması ne kadar sürer?

Çoğu Application Insights veri, 5 dakikalık bir gecikme süresine sahiptir. Bazı veriler daha uzun sürebilir; genellikle daha büyük günlük dosyaları. Daha fazla bilgi için bkz. [SLA Application Insights](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).

## <a name="more-answers"></a>Daha fazla yanıt
* [Application Insights Forumu](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: data-retention-privacy.md
[platforms]: platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md
