---
title: Azure Application Insights veri saklama ve depolama | Microsoft Docs
description: Bekletme ve Gizlilik ilkesi bildirimi
ms.topic: conceptual
ms.date: 06/30/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 6a7545d4d927418e6153ce3bf439fc8c3e2dc702
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104774924"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Application Insights'da veri toplama, saklama ve depolama

Uygulamanıza [Azure Application Insights][start] SDK 'yı yüklediğinizde, uygulamanız hakkında telemetri, buluta gönderilir. Doğal olarak, sorumlu geliştiriciler tam olarak hangi verilerin gönderildiğini, verilere ne olduğunu ve bunların denetimini nasıl tutabileceklerini bilmek ister. Özellikle gizli veriler gönderilebilir, nerede depolanıyor ve ne kadar güvenli? 

İlk olarak, kısa yanıt:

* "Kutudan çıkar" olarak çalışan standart telemetri modülleri, önemli verileri hizmete gönderemeyebilir. Telemetri, yük, performans ve kullanım ölçümleri, özel durum raporları ve diğer tanılama verileri ile ilgilidir. Tanılama raporlarında görünen ana Kullanıcı verileri URL 'lardır; ancak uygulamanız herhangi bir durumda gizli verileri bir URL 'de düz metin olarak yerleştirmemelidir.
* Tanılama ve izleme kullanımı konusunda size yardımcı olmak için ek özel telemetri gönderen bir kod yazabilirsiniz. (Bu genişletilebilirlik Application Insights harika bir özelliğidir.) Bu kod yanlışlıkla, kişisel ve diğer hassas verileri içerecek şekilde yazmak mümkündür. Uygulamanız bu tür verilerle çalışıyorsa, yazdığınız tüm koda tam bir gözden geçirme işlemi uygulamanız gerekir.
* Uygulamanızı geliştirirken ve test ederken, SDK tarafından gönderilen işlemleri kolayca inceleyebilirsiniz. Veriler, IDE ve tarayıcının hata ayıklama çıkışı penceresinde görüntülenir.
* Yeni bir Application Insights kaynağı oluştururken konum ' u seçebilirsiniz. [Burada](https://azure.microsoft.com/global-infrastructure/services/?products=all)bölge başına Application Insights kullanılabilirliği hakkında daha fazla bilgi edinebilirsiniz.
*   Toplanan verileri gözden geçirin. Bu, bazı durumlarda izin verilen ancak diğerlerini olmayan verileri de içerebilir.  Cihaz adı buna iyi bir örnektir. Bir sunucudaki cihaz adı gizlilik etkisine sahip değildir ve faydalıdır, ancak bir telefondan veya dizüstü bilgisayardan bir cihaz adının gizlilik etkisi olabilir ve daha az kullanışlı olabilir. Birincil olarak sunucuları hedeflemek için geliştirilmiş bir SDK, varsayılan olarak cihaz adı toplar ve hem normal olaylar hem de özel durumlar içinde üzerine yazılması gerekebilir.

Bu makalenin geri kalanı, bu yanıtlara daha fazla elaborates. Bu, kendi ekibinizin parçası olmayan iş arkadaşlarınıza gösterebilmeniz için kendine dahil olmak üzere tasarlanmıştır.

## <a name="what-is-application-insights"></a>Application Insights nedir?
[Azure Application Insights][start] , Microsoft tarafından sunulan ve canlı uygulamanızın performansını ve kullanılabilirliğini artırmanıza yardımcı olan bir hizmettir. Uygulamanızı, her ikisi de test sırasında ve yayımladıktan sonra ve dağıttıktan sonra çalıştığı zaman izler. Application Insights, size en fazla Kullanıcı alacağınız, uygulamanın ne kadar zaman olduğu ve bağımlı olduğu herhangi bir dış hizmet tarafından ne kadar iyi hizmet verdiğini gösteren grafikler ve tablolar oluşturur. Kilitlenmeler, sorunlar veya performans sorunları varsa, nedeni tanılamak için telemetri verilerinde ayrıntılı arama yapabilirsiniz. Uygulamanızın kullanılabilirliği ve performansı üzerinde herhangi bir değişiklik olursa, hizmet size e-posta gönderir.

Bu işlevselliği almak için, uygulamanıza bir Application Insights SDK yüklersiniz ve bu kodun bir parçası haline gelir. Uygulamanız çalışırken SDK, işlemini izler ve Application Insights hizmetine telemetri gönderir. Bu, [Microsoft Azure](https://azure.com)tarafından barındırılan bir bulut hizmetidir. (Ancak, yalnızca Azure 'da barındırılan uygulamalar değil, tüm uygulamalar için Application Insights işe yarar.)

Application Insights hizmeti telemetri depolar ve analiz eder. Analiz veya saklı Telemetriyi görmek için Azure hesabınızda oturum açıp uygulamanızın Application Insights kaynağını açarsınız. Ayrıca, veri erişimini takımınızın diğer üyeleriyle veya belirtilen Azure aboneleri ile paylaşabilirsiniz.

Application Insights hizmetinden (örneğin, bir veritabanına veya harici araçlara) veri aktarılmış verilere sahip olabilirsiniz. Her araca, hizmetten elde ettiğiniz özel bir anahtarla sağlarsınız. Gerekirse, anahtar iptal edilebilir. 

Application Insights SDK 'lar bir dizi uygulama türü için kullanılabilir: kendi Java EE veya ASP.NET sunucularınızda veya Azure 'da barındırılan Web Hizmetleri; Web istemcileri-diğer bir deyişle, bir Web sayfasında çalışan kod; Masaüstü uygulamaları ve Hizmetleri; Windows Phone, iOS ve Android gibi cihaz uygulamaları. Hepsi aynı hizmete telemetri gönderir.

## <a name="what-data-does-it-collect"></a>Hangi verileri toplar?
Üç veri kaynağı vardır:

* [Geliştirme](./asp-net.md) aşamasında veya [çalışma](./monitor-performance-live-website-now.md)ZAMANıNDA uygulamanızla tümleştirilen SDK. Farklı uygulama türleri için farklı SDK 'lar vardır. Ayrıca, sayfa ile birlikte son kullanıcının tarayıcısına yüklenen [Web sayfaları için bir SDK](./javascript.md)da vardır.
  
  * Her SDK, farklı telemetri türlerini toplamak için farklı teknikler kullanan birkaç [Modül](./configuration-with-applicationinsights-config.md)içerir.
  * SDK 'Yı geliştirmeye yüklerseniz, standart modüllere ek olarak kendi telemetrinizi göndermek için API 'sini kullanabilirsiniz. Bu özel telemetri, göndermek istediğiniz tüm verileri içerebilir.
* Bazı Web sunucularında, uygulama ile birlikte çalışan ve CPU, bellek ve ağ doluluk hakkında telemetri gönderen aracılar da vardır. Örneğin, Azure VM 'Leri, Docker konakları ve [Java EE sunucularının](./java-agent.md) böyle aracıları olabilir.
* [Kullanılabilirlik testleri](./monitor-web-app-availability.md) , Microsoft tarafından çalıştırılan ve istekleri düzenli aralıklarla Web uygulamanıza gönderen işlemlerdir. Sonuçlar Application Insights hizmetine gönderilir.

### <a name="what-kinds-of-data-are-collected"></a>Ne tür veriler toplanır?
Ana Kategoriler şunlardır:

* [Web sunucusu telemetrisi](./asp-net.md) -http istekleri.  URI, istek, yanıt kodu, istemci IP adresi işlemek için geçen süre. `Session id`.
* [Web sayfaları](./javascript.md) -sayfa, Kullanıcı ve oturum sayıları. Sayfa yükleme süreleri. Larý. Ajax çağrıları.
* Performans sayaçları-bellek, CPU, GÇ, ağ doluluk.
* İstemci ve sunucu bağlamı-işletim sistemi, yerel ayar, cihaz türü, tarayıcı, ekran çözünürlüğü.
* [Özel durumlar](./asp-net-exceptions.md) ve kilitlenmeler- **yığın dökümleri**, `build id` , CPU türü. 
* [Bağımlılıklar](./asp-net-dependencies.md) -Rest, SQL, AJAX gibi harici hizmetlere yapılan çağrılar. URI veya bağlantı dizesi, süre, başarı, komut.
* [Kullanılabilirlik testleri](./monitor-web-app-availability.md) -test ve adımların süresi, yanıtlar.
* [](./asp-net-trace-logs.md) [](./api-custom-events-metrics.md)  -  **Günlüklere veya telemetrinize kodlarınızın her şeyi** izlemek için izleme günlükleri ve özel telemetri.

[Daha fazla ayrıntı](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Toplanmakta olan öğeleri nasıl doğrulayabilirim?
Uygulamayı Visual Studio 'Yu kullanarak geliştirdiğinizi, uygulamayı hata ayıklama modunda (F5) çalıştırın. Telemetriyi çıkış penceresinde görünür. Buradan, daha kolay İnceleme için onu kopyalayabilir ve JSON olarak biçimlendirebilirsiniz. 

![Uygulamayı Visual Studio 'da hata ayıklama modunda çalıştırmayı gösteren ekran görüntüsü.](./media/data-retention-privacy/06-vs.png)

Tanılama penceresinde de daha okunabilir bir görünüm bulunur.

Web sayfaları için tarayıcınızın hata ayıklama penceresini açın.

![F12 tuşuna basın ve ağ sekmesini açın.](./media/data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Telemetriyi gönderilmeden önce filtrelemek için kod yazabilir miyim?
Bu, bir [telemetri işlemcisi eklentisi](./api-filtering-sampling.md)yazarak mümkün olacaktır.

## <a name="how-long-is-the-data-kept"></a>Veriler ne kadar süreyle tutuluyor?
Ham veri noktaları (diğer bir deyişle, analiz bölümünde sorgulama yapabilir ve aramada araştırma yapabilirsiniz) 730 güne kadar tutulur. 30, 60, 90, 120, 180, 270, 365, 550 veya 730 Days [bir saklama süresi seçebilirsiniz](./pricing.md#change-the-data-retention-period) . Verileri 730 günden daha uzun tutmanız gerekiyorsa, veri alımı sırasında depolama hesabına kopyalamak için [sürekli dışarı aktarma](./export-telemetry.md) kullanabilirsiniz. 

90 günden daha uzun süre tutulan veriler de ek ücret uygulanır. [Azure izleyici fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/monitor/)Application Insights fiyatlandırması hakkında daha fazla bilgi edinin.

Toplanan veriler (diğer bir deyişle, sayımlar, ortalamalar ve Ölçüm Gezgini 'nde gördüğünüz diğer istatistiksel veriler) 90 gün boyunca 1 dakikalık bir zaman içinde tutulur.

[Hata ayıklama anlık görüntüleri](./snapshot-debugger.md) 15 gün boyunca depolanır. Bu bekletme ilkesi, uygulama başına temelinde ayarlanır. Bu değeri artırmanız gerekiyorsa Azure portal bir destek talebi açarak artış isteyebilirsiniz.

## <a name="who-can-access-the-data"></a>Verilere kimler erişebilir?
Veriler sizin için görünür ve bir kuruluş hesabınız varsa, ekip üyeleriniz olur. 

Siz ve ekip üyeleriniz tarafından dışarıya aktarılabilir ve diğer konumlara kopyalanabilir ve diğer kişilere geçirilebilir.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>Uygulamamın Application Insights gönderdiği bilgileri Microsoft ne yapar?
Microsoft, yalnızca hizmeti sağlamak için verileri kullanır.

## <a name="where-is-the-data-held"></a>Veriler nerede tutulur?
* Yeni bir Application Insights kaynağı oluştururken konum ' u seçebilirsiniz. [Burada](https://azure.microsoft.com/global-infrastructure/services/?products=all)bölge başına Application Insights kullanılabilirliği hakkında daha fazla bilgi edinebilirsiniz.

## <a name="how-secure-is-my-data"></a>Verilerim ne kadar güvenlidir?
Application Insights bir Azure hizmetidir. Güvenlik ilkeleri, [Azure Güvenlik, gizlilik ve uyumluluk teknik incelemesi](https://go.microsoft.com/fwlink/?linkid=392408)bölümünde açıklanmıştır.

Veriler Microsoft Azure sunucularında depolanır. Azure portal hesaplar için, hesap kısıtlamaları [Azure Güvenlik, gizlilik ve uyumluluk belgesinde](https://go.microsoft.com/fwlink/?linkid=392408)açıklanır.

Microsoft personeli tarafından verilerinize erişim kısıtlıdır. Verilerinize yalnızca izninizi ve Application Insights kullanımını desteklemek için gerekliyse erişirsiniz. 

Tüm müşterilerin uygulamalarında toplanan verilerin (veri ücretleri ve ortalama izlemeler gibi) Application Insights geliştirmek için kullanılır.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>Başka birinin telemetri Application Insights verileriyle kesintiye neden olabilir mi?
Bunlar, Web sayfalarınızın kodunda bulunan izleme anahtarını kullanarak hesabınıza ek telemetri gönderebilirler. Yeterli ek veri sayesinde, ölçümleriniz uygulamanızın performansını ve kullanımını doğru şekilde temsil etmez.

Kodu diğer projelerle paylaşırsanız, izleme anahtarınızı kaldırmayı unutmayın.

## <a name="is-the-data-encrypted"></a>Veriler şifrelendi mı?
Tüm veriler, bekleyen ve veri merkezleri arasında taşındığı için şifrelenir.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Veriler uygulamamın Application Insights sunuculara iletimde mi şifrelendi?
Evet, Web sunucuları, cihazlar ve HTTPS Web sayfaları dahil olmak üzere neredeyse tüm SDK 'lardan portala veri göndermek için https kullanırız. 

## <a name="does-the-sdk-create-temporary-local-storage"></a>SDK geçici yerel depolama alanı mı oluşturur?

Evet, bir uç noktaya ulaşılırsa bazı telemetri kanalları verileri yerel olarak kalıcı hale getirilecektir. Hangi çerçeve ve telemetri kanalının etkilendiğini görmek için lütfen aşağıdaki gözden geçirin.

Yerel depolamayı kullanan telemetri kanalları TEMP veya APPDATA dizinlerinde, uygulamanızı çalıştıran belirli hesapla kısıtlanan geçici dosyalar oluşturur. Bu, bir uç noktanın geçici olarak kullanılamadığı veya azaltma sınırına ulaştığınızda meydana gelebilir. Bu sorun çözümlendikten sonra telemetri kanalı, tüm yeni ve kalıcı verileri göndermeye devam eder.

Bu kalıcı veriler yerel olarak şifrelenmez. Bu sorun varsa, verileri gözden geçirin ve özel verileri toplamayı kısıtlayın. (Daha fazla bilgi için bkz. [özel verileri dışarı aktarma ve silme](../logs/personal-data-mgmt.md#how-to-export-and-delete-private-data).)

Bir müşterinin bu dizini belirli güvenlik gereksinimleriyle yapılandırması gerekiyorsa, bu, Framework başına yapılandırılabilir. Lütfen uygulamanızı çalıştıran işlemin bu dizine yazma erişimi olduğundan emin olun, ancak Ayrıca bu dizinin, istenmeyen kullanıcılar tarafından okunmasını önlemek için bu dizinin korunduğundan emin olun.

### <a name="java"></a>Java

`C:\Users\username\AppData\Local\Temp` kalıcı veriler için kullanılır. Bu konum, yapılandırma dizininden yapılandırılamaz ve bu klasöre erişim izinleri, gerekli kimlik bilgileriyle belirli bir kullanıcıyla kısıtlıdır. (Daha fazla bilgi için bkz. [uygulama](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72).)

###  <a name="net"></a>.NET

Varsayılan olarak `ServerTelemetryChannel` , geçerli kullanıcının yerel uygulama verileri klasörünü `%localAppData%\Microsoft\ApplicationInsights` veya Temp klasörünü kullanır `%TMP%` . (Bkz. [uygulama](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) burada.)


Yapılandırma dosyası aracılığıyla:
```xml
<TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel,   Microsoft.AI.ServerTelemetryChannel">
    <StorageFolder>D:\NewTestFolder</StorageFolder>
</TelemetryChannel>
```

Kod aracılığıyla:

- Yapılandırma dosyasından ServerTelemetryChannel kaldır
- Bu kod parçacığını yapılandırmanıza ekleyin:
  ```csharp
  ServerTelemetryChannel channel = new ServerTelemetryChannel();
  channel.StorageFolder = @"D:\NewTestFolder";
  channel.Initialize(TelemetryConfiguration.Active);
  TelemetryConfiguration.Active.TelemetryChannel = channel;
  ```

### <a name="netcore"></a>NetCore

Varsayılan olarak `ServerTelemetryChannel` , geçerli kullanıcının yerel uygulama verileri klasörünü `%localAppData%\Microsoft\ApplicationInsights` veya Temp klasörünü kullanır `%TMP%` . (Bkz. [uygulama](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) burada.) 

Bir Linux ortamında, bir depolama klasörü belirtilmediği takdirde yerel depolama devre dışı bırakılır.

> [!NOTE]
> 2.15.0-Beta3 ve daha büyük yerel depolama sürümü ile artık Linux, Mac ve Windows için otomatik olarak oluşturulur. Windows dışı sistemler için SDK, aşağıdaki mantığa göre otomatik olarak yerel bir depolama klasörü oluşturacaktır:
> - `${TMPDIR}` - `${TMPDIR}` ortam değişkeni ayarlanmışsa bu konum kullanılır.
> - `/var/tmp` -önceki konum yoksa, denememiz önerilir `/var/tmp` .
> - `/tmp` -bir önceki konumların her ikisi de yoksa, denememiz önerilir `tmp` . 
> - Bu konumların hiçbiri yoksa yerel depolama alanı oluşturulmaz ve el ile yapılandırma yine de gereklidir. [Tam uygulama ayrıntıları için](https://github.com/microsoft/ApplicationInsights-dotnet/pull/1860).

Aşağıdaki kod parçacığı, sınıfınızın yönteminde nasıl ayarlanacağını gösterir `ServerTelemetryChannel.StorageFolder` `ConfigureServices()` `Startup.cs` :

```csharp
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

(Daha fazla bilgi için bkz. [Aspnetcore özel yapılandırması](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration).)

### <a name="nodejs"></a>Node.js

`%TEMP%/appInsights-node{INSTRUMENTATION KEY}`Kalıcı veriler için varsayılan olarak kullanılır. Bu klasöre erişim izinleri geçerli kullanıcı ve yöneticilerle kısıtlıdır. (Bkz. [uygulama](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts) burada.)

Dosya ön eki, `appInsights-node` `Sender.TEMPDIR_PREFIX` [Sender. TS](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384)içinde bulunan statik değişkenin çalışma zamanı değeri değiştirilerek geçersiz kılınabilir.

### <a name="javascript-browser"></a>JavaScript (tarayıcı)

[HTML5 oturum depolaması](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage) , verileri kalıcı hale getirmek için kullanılır. İki ayrı arabellek kullanılır: `AI_buffer` ve `AI_sent_buffer` . Toplu ve gönderilmek üzere bekleyen telemetri ' de depolanır `AI_buffer` . Yeni gönderilen telemetri `AI_sent_buffer` , alma sunucusu başarılı bir şekilde alındığından yanıt verene kadar yerleştirilir. Telemetri başarıyla alındığında, tüm arabelleklerden kaldırılır. Geçici hatalarda (örneğin, bir Kullanıcı ağ bağlantısını kaybeder), telemetri başarılı bir şekilde `AI_buffer` alınana kadar veya alma sunucusu Telemetriyi geçersiz hale gelinceye kadar (örneğin, hatalı şema veya çok eski) yanıt veriyor.

Telemetri arabellekleri, olarak ayarlanarak devre dışı [`enableSessionStorageBuffer`](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/JavaScript/JavaScriptSDK.Interfaces/IConfig.ts#L31) bırakılabilir `false` . Oturum depolama kapalı olduğunda, yerel bir dizi kalıcı depolama alanı olarak kullanılır. JavaScript SDK 'Sı bir istemci cihazda çalıştığı için, kullanıcının, tarayıcısının geliştirici araçları aracılığıyla bu depolama konumuna erişimi vardır.

### <a name="opencensus-python"></a>OpenCensus Python

Varsayılan olarak, OpenCensus Python SDK geçerli kullanıcı klasörünü kullanır `%username%/.opencensus/.azure/` . Bu klasöre erişim izinleri geçerli kullanıcı ve yöneticilerle kısıtlıdır. (Bkz. [uygulama](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/storage.py) burada.) Kalıcı verilerinizin bulunduğu klasör, Telemetriyi oluşturan Python dosyasından sonra adlandıralınacaktır.

`storage_path`Kullanmakta olduğunuz Dışarı Aktarıcı oluşturucusunun içindeki parametresini geçirerek depolama dosyanızın konumunu değiştirebilirsiniz.

```python
AzureLogHandler(
  connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000',
  storage_path='<your-path-here>',
)
```

## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Nasıl yaparım? TLS 1,2 kullanarak Application Insights verileri gönderiyor musunuz?

Application Insights uç noktalarına geçiş sırasındaki verilerin güvenliğini sağlamak için, müşterilerin uygulamasını en az Aktarım Katmanı Güvenliği (TLS) 1,2 kullanacak şekilde yapılandırmasını kesinlikle öneririz. TLS/Güvenli Yuva Katmanı (SSL) uygulamasının güvenlik açığı olduğu ve geriye dönük uyumlulukla hala çalışmaya devam eden daha eski sürümleri, bu sürümler **önerilmez** ve sektör bu eski protokoller için destek vermeyi hızla taşır. 

[PCI güvenlik standartları Council](https://www.pcisecuritystandards.org/) , TLS/SSL 'nin eski sürümlerini devre dışı bırakmak ve daha güvenli protokollere yükseltmek Için [30 Haziran 2018 ' nin son tarihini](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) ayarladı. Azure eski desteği düşürdüğünde, uygulamanız/istemcileriniz en az TLS 1,2 üzerinden iletişim kuramıyorsa, Application Insights veri gönderemeyebilirsiniz. Uygulamanızın TLS desteğini test etmek ve doğrulamak için gereken yaklaşım, işletim sistemine/platforma ve uygulamanızın kullandığı dil/çerçeveye bağlı olarak değişir.

Bunun gerekli olmadığı sürece uygulamanızı yalnızca TLS 1,2 ' i kullanacak şekilde açıkça ayarlamamız önerilir. Bu, TLS 1,3 gibi daha yeni bir güvenlik protokollerinden otomatik olarak algılanmasını ve bunların avantajlarından yararlanmanızı sağlayan platform düzeyi güvenlik özelliklerini bozabilir. Belirli bir TLS/SSL sürümünün sabit kodlamasını denetlemek için uygulamanızın koduna kapsamlı bir denetim gerçekleştirmenizi öneririz.

### <a name="platformlanguage-specific-guidance"></a>Platforma/dile özel kılavuz

|Platform/dil | Destek | Daha Fazla Bilgi |
| --- | --- | --- |
| Azure Uygulama Hizmetleri  | Desteklenir, yapılandırma gerekli olabilir. | Destek 2018 Nisan 'da duyuruldu. [Yapılandırma ayrıntıları](https://azure.github.io/AppService/2018/04/17/App-Service-and-Functions-hosted-apps-can-now-update-TLS-versions!)için Duyuruyu okuyun.  |
| Azure İşlev Uygulamaları | Desteklenir, yapılandırma gerekli olabilir. | Destek 2018 Nisan 'da duyuruldu. [Yapılandırma ayrıntıları](https://azure.github.io/AppService/2018/04/17/App-Service-and-Functions-hosted-apps-can-now-update-TLS-versions!)için Duyuruyu okuyun. |
|.NET | Desteklenir, yapılandırma sürüme göre farklılık gösterir. | .NET 4,7 ve önceki sürümler için ayrıntılı yapılandırma bilgileri için, [Bu yönergelere](/dotnet/framework/network-programming/tls#support-for-tls-12)bakın.  |
|Durum İzleyicisi | Desteklenir, yapılandırma gerekli | Durum İzleyicisi, TLS 1,2 ' i desteklemek için [işletim sistemi yapılandırması](/windows-server/security/tls/tls-registry-settings)  +  [.NET yapılandırmasına](/dotnet/framework/network-programming/tls#support-for-tls-12) dayanır.
|Node.js |  Desteklenen, v 10.5.0 'de yapılandırma gerekebilir. | Uygulamaya özgü herhangi bir yapılandırma için [resmi Node.js TLS/SSL belgelerini](https://nodejs.org/api/tls.html) kullanın. |
|Java | , [JDK 6 güncelleştirme 121](https://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) ve [JDK 7](https://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html)' ye desteklenen TLS 1,2 için JDK desteği eklenmiştir. | JDK 8, [Varsayılan olarak TLS 1,2](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default)kullanır.  |
|Linux | Linux dağıtımları, TLS 1,2 desteği için [OpenSSL](https://www.openssl.org) 'yi kullanır.  | OpenSSL sürümünüzü doğrulamak için [OpenSSL changelog](https://www.openssl.org/news/changelog.html) ' yı denetleyin.|
| Windows 8,0-10 | Desteklenir ve varsayılan olarak etkindir. | Hala [varsayılan ayarları](/windows-server/security/tls/tls-registry-settings)kullandığınızdan emin olun.  |
| Windows Server 2012-2016 | Desteklenir ve varsayılan olarak etkindir. | [Varsayılan ayarları](/windows-server/security/tls/tls-registry-settings) hala kullandığınızı doğrulamak için |
| Windows 7 SP1 ve Windows Server 2008 R2 SP1 | Desteklenir, ancak varsayılan olarak etkinleştirilmez. | ' Nin nasıl etkinleştirileceği hakkında ayrıntılı bilgi için bkz. [Aktarım Katmanı Güvenliği (TLS) kayıt defteri ayarları](/windows-server/security/tls/tls-registry-settings) sayfası.  |
| Windows Server 2008 SP2 | TLS 1,2 desteği için bir güncelleştirme gerekiyor. | Bkz. Windows Server 2008 SP2 'de [TLS 1,2 için destek eklemek Için güncelleştirme](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) . |
|Windows Vista | Desteklenmiyor. | Yok

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Linux dağılımının hangi OpenSSL sürümünü çalıştırdığından emin olun

Hangi OpenSSL sürümünü yüklediklerinizi denetlemek için terminali açın ve şunu çalıştırın:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Linux üzerinde bir test TLS 1,2 işlemi çalıştırma

Linux sisteminizin TLS 1,2 üzerinden iletişim kurabildiğini görmek üzere bir ön test çalıştırmak için, terminali açın ve şunu çalıştırın:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Application Insights depolanan kişisel veriler

[Application Insights kişisel veri makalemiz](../logs/personal-data-mgmt.md) bu sorunu ayrıntılı bir şekilde ele almaktadır.

#### <a name="can-my-users-turn-off-application-insights"></a>Kullanıcılarım Application Insights kapatabilir mi?
Doğrudan değil. Kullanıcılarınızın Application Insights kapatmak için çalıştırabilmesini sağlayacak bir anahtar sağlamayız.

Ancak, uygulamanızda böyle bir özelliği uygulayabilirsiniz. Tüm SDK 'lar telemetri toplamayı kapatan bir API ayarı içerir. 

## <a name="data-sent-by-application-insights"></a>Application Insights tarafından gönderilen veriler
SDK 'lar platformlar arasında farklılık gösterir ve yükleyebileceğiniz birkaç bileşen vardır. ( [Application Insights genel bakış][start]bölümüne bakın.) Her bileşen farklı veriler gönderir.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Farklı senaryolarda gönderilen veri sınıfları

| Eyleminizi | Toplanan veri sınıfları (sonraki tabloya bakın) |
| --- | --- |
| [.NET Web projesine Application Insights SDK ekleme][greenbrown] |Sunucubağlamı<br/>Temsilc<br/>Performans sayaçları<br/>İstekler<br/>**Özel durumlar**<br/>Oturum<br/>kullanıcılar |
| [Durum İzleyicisi IIS 'ye yükler][redfield] |Bağımlılıklar<br/>Sunucubağlamı<br/>Temsilc<br/>Performans sayaçları |
| [Java Web uygulamasına Application Insights SDK 'Sı ekleme][java] |Sunucubağlamı<br/>Temsilc<br/>İstek<br/>Oturum<br/>kullanıcılar |
| [Web sayfasına JavaScript SDK 'Sı ekleme][client] |ClientContext <br/>Temsilc<br/>Sayfa<br/>ClientPerf<br/>Ajax |
| [Varsayılan özellikleri tanımlama][apiproperties] |Tüm standart ve özel olaylardaki **Özellikler** |
| [TrackMetric çağrısı yapın][api] |Sayısal değerler<br/>**Özellikler** |
| [Çağrıyı Izle *][api] |Olay adı<br/>**Özellikler** |
| [TrackException çağrısı][api] |**Özel durumlar**<br/>Yığın dökümü<br/>**Özellikler** |
| SDK veri toplayamıyor. Örnek: <br/> -performans sayaçlarına erişilemiyor<br/> -Telemetri başlatıcısında özel durum |SDK tanılaması |

[Diğer platformların SDK 'ları][platforms]için belgelerine bakın.

#### <a name="the-classes-of-collected-data"></a>Toplanan verilerin sınıfları

| Toplanan veri sınıfı | İçerir (kapsamlı bir liste değil) |
| --- | --- |
| **Özellikler** |**Kodunuz tarafından belirlenen tüm veriler** |
| DeviceContext |`Id`, IP, yerel ayar, cihaz modeli, ağ, ağ türü, OEM adı, ekran çözünürlüğü, rol örneği, rol adı, cihaz türü |
| ClientContext |İşletim sistemi, yerel ayar, dil, ağ, pencere çözünürlüğü |
| Oturum |`session id` |
| Sunucubağlamı |Makine adı, yerel ayar, işletim sistemi, cihaz, Kullanıcı oturumu, Kullanıcı bağlamı, işlem |
| Temsilc |IP adresi, zaman damgası, işletim sistemi, tarayıcıdan coğrafi konum |
| Ölçümler |Ölçüm adı ve değeri |
| Ekinlikler |Olay adı ve değeri |
| PageViews |URL ve sayfa adı ya da ekran adı |
| İstemci perf |URL/sayfa adı, tarayıcı yükleme süresi |
| Ajax |Web sayfasından sunucusuna HTTP çağrıları |
| İstekler |URL, süre, yanıt kodu |
| Bağımlılıklar |Tür (SQL, HTTP,...), bağlantı dizesi veya URI, Sync/Async, Duration, Success, SQL deyimleri (Durum İzleyicisi ile) |
| **Özel durumlar** |Tür, **ileti**, çağrı yığınları, kaynak dosya, satır numarası, `thread id` |
| Çökme |`Process id`, `parent process id` ,, `crash thread id` Uygulama Düzeltme Eki, `id` , derleme;  özel durum türü, adres, neden; karıştırılmış semboller ve Yazmaçları, ikili başlangıç ve bitiş adresleri, ikili ad ve yol, CPU türü |
| İzleme |**İleti** ve önem düzeyi |
| Performans sayaçları |İşlemci süresi, kullanılabilir bellek, istek hızı, özel durum oranı, işlem özel baytları, GÇ oranı, istek süresi, istek kuyruğu uzunluğu |
| Kullanılabilirlik |Web testi yanıt kodu, her test adımının süresi, test adı, zaman damgası, başarı, yanıt süresi, test konumu |
| SDK tanılaması |Trace iletisi veya özel durumu |

[Bazı verileri düzenleyerek ApplicationInsights.config][config] yapabilirsiniz

> [!NOTE]
> İstemci IP 'si coğrafi konumu çıkarmakta kullanılır, ancak varsayılan olarak IP verileri artık depolanmaz ve tüm sıfırlardan ilişkili alana yazılır. Kişisel veri işleme hakkında daha fazla bilgi edinmek için bu [makaleyi](../logs/personal-data-mgmt.md#application-data)öneririz. IP adresi verilerini depolamanız gerekirse, [IP adresi toplama makalemiz](./ip-collection.md) , seçenekleriniz boyunca size yol gösterir.

## <a name="can-i-modify-or-update-data-after-it-has-been-collected"></a>Toplandıktan sonra verileri değiştirebilir veya güncelleştirebilir miyim?

Hayır, veri salt okunurdur ve yalnızca temizleme işlevselliği aracılığıyla silinebilir. [Log Analytics ve Application Insights depolanan kişisel veriler için](../logs/personal-data-mgmt.md#delete)daha fazla bilgi edinin.

## <a name="credits"></a>Krediler
Bu ürün, tarafından sağlanan, Maxakılda tarafından oluşturulan GeoLite2 verilerini içerir [https://www.maxmind.com](https://www.maxmind.com) .



<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiproperties]: ./api-custom-events-metrics.md#properties
[client]: ./javascript.md
[config]: ./configuration-with-applicationinsights-config.md
[greenbrown]: ./asp-net.md
[java]: ./java-get-started.md
[platforms]: ./platforms.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[redfield]: ./monitor-performance-live-website-now.md
[start]: ./app-insights-overview.md

