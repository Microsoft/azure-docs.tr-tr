---
title: Azure Application Insights uygulama verilerini görüntüleme | Microsoft Docs
description: Application Insights Bağlayıcısı çözümünü kullanarak performans sorunlarını tanılamanıza ve Application Insights ile izlendiğinde hangi kullanıcıların uygulamanızla ne yaptığını anlayabilmeniz için kullanabilirsiniz.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 02/13/2019
ms.openlocfilehash: b956c3bc7d04908db1cc45092cf5926ecfcc305c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932740"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Application Insights Bağlayıcısı Management çözümü (kullanım dışı)

![Application Insights simgesi](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> [Çapraz kaynak sorguları](../../azure-monitor/log-query/cross-workspace-query.md)desteğiyle Application Insights Bağlayıcısı yönetim çözümü artık gerekli değildir. Azure ticari bulut için 15 Ocak 2019 tarihinde kullanımdan kaldırılan OMS portalı ile birlikte kullanımdan kaldırılmıştır ve Azure Marketi 'nden kaldırılmıştır. Azure ABD Kamu Bulutu için 30 Mart 2019 ' de kullanımdan kaldırılacaktır.
>
>Mevcut bağlantılar 30 Haziran 2019 ' e kadar çalışmaya devam edecektir.  OMS portalının kullanımdan kaldırılması sayesinde, mevcut bağlantıları portaldan yapılandırmanın ve kaldırmanın bir yolu yoktur. Mevcut bağlantıları kaldırmak için PowerShell 'i kullanma konusunda bir betik için aşağıdaki [PowerShell ile bağlayıcıyı kaldırma](#removing-the-connector-with-powershell) bölümüne bakın.
>
>Birden çok uygulama için Application Insights günlük verilerini sorgulama konusunda rehberlik için bkz. [birden çok Azure izleyicisini Application Insights kaynağı](../log-query/unify-app-resource-data.md)birleştirme. OMS portalının kullanımdan kaldırılması hakkında daha fazla bilgi için bkz. [OMS portalı Azure 'a taşınıyor](../../azure-monitor/platform/oms-portal-transition.md).
>
> 

Uygulama öngörüleri Bağlayıcısı çözümü, performans sorunlarını tanılamanıza ve [Application Insights](../../azure-monitor/app/app-insights-overview.md)ile izlendiğinde hangi kullanıcıların uygulamanızla ne yaptığını anlamanıza yardımcı olur. Application Insights ' de geliştiricilerin göreceği aynı uygulama telemetrisine ilişkin Görünümler Log Analytics sunulmaktadır. Ancak, Application Insights uygulamalarınızı Log Analytics tümleştirdiğinizde, uygulamalarınızın görünürlüğü işlem ve uygulama verileri tek bir yerde bulundurarak artar. Aynı görünümlerin olması, uygulama geliştiricilerinizle işbirliği yapmanıza yardımcı olur. Ortak görünümler, hem uygulama hem de platform sorunlarını tespit etmek ve çözümlemek için süreyi azaltmaya yardımcı olabilir.

Çözümü kullandığınızda şunları yapabilirsiniz:

- Farklı Azure aboneliklerinde olsalar dahi, tüm Application Insights uygulamalarınızı tek bir yerde görüntüleyin
- Altyapı verilerinin uygulama verileriyle ilişkilendirilmesi
- Günlük aramasında perspektiflerle uygulama verilerini görselleştirin
- Azure portal Application Insights uygulamanıza Log Analytics verilerden Özet


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>Bağlı kaynaklar

Diğer Log Analytics çözümlerinin aksine, veriler aracıları tarafından Application Insights Bağlayıcısı için toplanmaz. Çözüm tarafından kullanılan tüm veriler doğrudan Azure 'dan gelir.

| Bağlı Kaynak | Desteklenen | Açıklama |
| --- | --- | --- |
| [Windows aracıları](../../azure-monitor/platform/agent-windows.md) | Hayır | Çözüm Windows aracılarından bilgi toplamaz. |
| [Linux aracıları](../../azure-monitor/learn/quick-collect-linux-computer.md) | Hayır | Çözüm, Linux aracılarından bilgi toplamaz. |
| [SCOM yönetim grubu](../../azure-monitor/platform/om-agents.md) | Hayır | Çözüm, bağlı bir SCOM yönetim grubundaki aracılardan bilgi toplamaz. |
| [Azure depolama hesabı](collect-azure-metrics-logs.md) | Hayır | Çözüm, Azure Storage 'dan bilgi toplamaz. |

## <a name="prerequisites"></a>Önkoşullar

- Application Insights Bağlayıcısı bilgilerine erişmek için bir Azure aboneliğinizin olması gerekir
- En az bir yapılandırılmış Application Insights kaynağınız olmalıdır.
- Application Insights kaynağın sahibi veya katılımcısı olmanız gerekir.

## <a name="configuration"></a>Yapılandırma

1. Azure Web Apps Analytics çözümünü [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AppInsights?tab=Overview) 'nden veya [Çözüm Galerisi Log Analytics çözümleri ekleme](../../azure-monitor/insights/solutions.md)bölümünde açıklanan işlemi kullanarak etkinleştirin.
2. [Azure portala](https://portal.azure.com) gidin. Application Insights açmak için **tüm hizmetler** ' i seçin. Sonra, Application Insights için arama yapın. 
3. **Abonelikler**' in altında Application Insights kaynaklara sahip bir abonelik seçin ve ardından **ad**' ın altında bir veya daha fazla uygulama seçin.
4. **Kaydet** düğmesine tıklayın.

Yaklaşık 30 dakika içinde, veriler kullanılabilir hale gelir ve Application Insights kutucuğu aşağıdaki görüntüde olduğu gibi verilerle güncelleştirilir:

![Application Insights kutucuğu](./media/app-insights-connector/app-insights-tile.png)

Göz önünde bulundurmanız gereken diğer noktaları:

- Application Insights uygulamalarını yalnızca bir Log Analytics çalışma alanına bağlayabilirsiniz.
- [Temel veya kurumsal Application Insights kaynaklarını](https://azure.microsoft.com/pricing/details/application-insights) yalnızca Log Analytics bağlayabilirsiniz. Ancak, Log Analytics ücretsiz katmanını kullanabilirsiniz.

## <a name="management-packs"></a>Yönetim paketleri

Bu çözüm bağlı yönetim gruplarına herhangi bir yönetim paketi yüklemez.

## <a name="use-the-solution"></a>Çözümü kullanma

Aşağıdaki bölümlerde, uygulamalarınızdaki verileri görüntülemek ve bunlarla etkileşim kurmak için Application Insights panosunda gösterilen dikey pencereleri nasıl kullanabileceğiniz açıklanır.

### <a name="view-application-insights-connector-information"></a>Application Insights Bağlayıcısı bilgilerini görüntüle

Aşağıdaki dikey pencereleri görmek için **Application Insights** panosunu açmak üzere **Application Insights** kutucuğuna tıklayın.

![Application Insights panosu](./media/app-insights-connector/app-insights-dash01.png)

![Application Insights panosu](./media/app-insights-connector/app-insights-dash02.png)

Pano, tabloda gösterilen dikey pencereleri içerir. Her dikey pencerede, dikey pencerenin belirtilen kapsam ve zaman aralığına yönelik ölçütleriyle eşleşen en fazla 10 öğe listelenir. Dikey pencerenin en altında bulunan **Tümünü göster** ' e tıkladığınızda veya dikey pencere başlığına tıkladığınızda tüm kayıtları döndüren bir günlük araması çalıştırabilirsiniz.


| **Sütunuyla** | **Açıklama** |
| --- | --- |
| Uygulamalar-uygulama sayısı | Uygulama kaynaklarındaki uygulamaların sayısını gösterir. Ayrıca uygulama adlarını ve her biri için uygulama kaydı sayısını listeler. <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> günlük araması çalıştırmak için sayıya tıklayın <br><br>  Uygulama başına uygulama kayıtlarını, telemetri türüne göre kayıtları ve tüm verileri türe göre (son güne göre) gösteren uygulama için bir uygulama adı ' na tıklayın. |
| Veri hacmi – verileri gönderen konaklar | Veri gönderen bilgisayar ana bilgisayarlarının sayısını gösterir. Ayrıca, her bir konak için bilgisayar ana bilgisayarlarını ve kayıt sayısını listeler. <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> günlük araması çalıştırmak için sayıya tıklayın <br><br> Konak başına uygulama kayıtlarını, telemetri türüne göre kayıtları ve tüm verileri türe göre (son güne göre) gösteren bir günlük araması çalıştırmak için bir bilgisayar adına tıklayın. |
| Kullanılabilirlik – WebTest sonuçları | Pass veya fail belirten Web test sonuçları için halka grafiği gösterir. <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> günlük araması çalıştırmak için grafiğe tıklayın <br><br> Sonuçlar tüm testler için geçen geçiş sayısını ve başarısızlığı gösterir. Son dakikadaki trafikle birlikte tüm Web Apps gösterir. Başarısız Web testlerinin ayrıntılarını gösteren bir günlük aramasını görüntülemek için bir uygulama adına tıklayın. |
| Sunucu Istekleri – saat başına Istek | Çeşitli uygulamalar için saat başına sunucu isteklerinin çizgi grafiğini gösterir. Zaman içinde bir noktaya yönelik istekleri alan ilk 3 uygulamayı görmek için grafikteki bir çizginin üzerine gelin. Ayrıca, istek alan uygulama ve seçilen döneme ait istek sayısını gösteren bir liste gösterir. <br><br>Çeşitli uygulamalar için saat başına sunucu isteklerinin daha ayrıntılı bir çizgi grafiğini gösteren <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> için bir günlük araması çalıştırmak için grafiğe tıklayın. <br><br> İstek listesini, zaman içindeki isteklerin ve istek süresi ve istek yanıt kodlarının bir listesini gösteren <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> bir günlük araması çalıştırmak için listedeki bir uygulamaya tıklayın.   |
| Hata – saat başına başarısız istek | Saat başına başarısız uygulama isteklerinin çizgi grafiğini gösterir. Zaman içinde bir noktaya yönelik başarısız isteklere sahip ilk 3 uygulamayı görmek için grafiğin üzerine gelin. Ayrıca, her biri için başarısız istek sayısı olan uygulamaların listesini gösterir. Başarısız uygulama isteklerinin daha ayrıntılı bir çizgi grafiğini gösteren <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> için bir günlük araması çalıştırmak için grafiğe tıklayın. <br><br>Başarısız istekleri gösteren <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code>, zaman içinde başarısız isteklere yönelik grafikleri ve başarısız istek yanıt kodlarının listesini gösteren için bir öğe seçin. |
| Özel durumlar – saat başına özel durumlar | Saat başına özel durumların çizgi grafiğini gösterir. Zaman içinde bir noktaya yönelik özel durumlarla ilk 3 uygulamayı görmek için grafiğin üzerine gelin. Ayrıca, her biri için özel durum sayısına sahip uygulamaların listesini gösterir. Özel durumların daha ayrıntılı bir bağlantı grafiğini gösteren <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> için bir günlük araması çalıştırmak için grafiğe tıklayın. <br><br>Özel durumların bir listesini, zaman ve başarısız isteklerde özel durumlar için grafikleri ve özel durum türlerinin bir listesini gösteren <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> bir günlük araması çalıştırmak için listedeki bir öğeye tıklayın.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Günlük aramayla Application Insights perspektifini görüntüleme

Panodaki herhangi bir öğeye tıkladığınızda, aramada gösterilen Application Insights perspektifini görürsünüz. Perspektif, seçilen telemetri türüne göre genişletilmiş bir görselleştirme sağlar. Bu nedenle, görselleştirme içeriği farklı telemetri türlerine göre değişir.

Uygulamalar dikey penceresinde herhangi bir yere tıkladığınızda varsayılan **uygulamaların** perspektifini görürsünüz.

![Application Insights uygulamaları perspektifi](./media/app-insights-connector/applications-blade-drill-search.png)

Perspektif, seçtiğiniz uygulamaya bir genel bakış gösterir.

**Kullanılabilirlik** dikey penceresinde Web testi sonuçlarını ve ilgili başarısız istekleri görebileceğiniz farklı bir perspektif görünümü gösterilmektedir.

![Application Insights kullanılabilirlik perspektifi](./media/app-insights-connector/availability-blade-drill-search.png)

**Sunucu istekleri** veya **arızaların** dikey penceresinde herhangi bir yere tıkladığınızda, perspektif bileşenleri, isteklerle ilgili bir görselleştirme sağlamak üzere değişir.

![Application Insights arızalar dikey penceresi](./media/app-insights-connector/server-requests-failures-drill-search.png)

**Özel durumlar** dikey penceresinde herhangi bir yere tıkladığınızda, özel durumlara uyarlanmış bir görselleştirme görürsünüz.

![Application Insights özel durumlar dikey penceresi](./media/app-insights-connector/exceptions-blade-drill-search.png)

Tek bir **Application Insights Bağlayıcısı** panosuna tıklamasından bağımsız olarak, **arama** sayfasının kendisi içinde Application Insights verileri döndüren herhangi bir sorgu Application Insights perspektifini gösterir. Örneğin, Application Insights verileri görüntülüyorsanız, bir **&#42;** sorgu aşağıdaki görüntüde olduğu gibi perspektif sekmesini de gösterir:

![Application Insights](./media/app-insights-connector/app-insights-search.png)

Perspektif bileşenleri, arama sorgusuna göre güncelleştirilir. Bu, sonuçları, verileri görebileceğiniz bir arama alanı kullanarak filtreleyebileceğiniz anlamına gelir:

- Tüm uygulamalarınız
- Tek Seçili uygulama
- Uygulama grubu

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Azure portal bir uygulamaya Özet

Application Insights Bağlayıcısı Blade *, Azure Portal kullandığınızda*seçili Application Insights uygulamasına Özet uygulamanızı sağlamak üzere tasarlanmıştır. Çözümü, bir uygulamanın sorunlarını gidermenize yardımcı olan üst düzey bir izleme platformu olarak kullanabilirsiniz. Bağlı uygulamalarınızdan birinde olası bir sorun gördüğünüzde, Log Analytics arama ' da ayrıntısına gidebilir veya doğrudan Application Insights uygulamasına özetleyebilirsiniz.

Özet 'e, her satırın sonunda görüntülenen üç noktaya ( **...** ) tıklayın ve **Application Insights aç**' ı seçin.

>[!NOTE]
>**Application Insights Içinde aç** Azure portal kullanılamıyor.

![Application Insights aç](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Örnek düzeltilmiş veriler

Application Insights telemetri trafiğini azaltmaya yardımcı olmak için *[örnekleme düzeltmesi](../../azure-monitor/app/sampling.md)* sağlar. Application Insights uygulamanızda örnekleme etkinleştirdiğinizde, hem Application Insights hem de Log Analytics içinde depolanan daha az sayıda giriş alırsınız. **Application Insights Bağlayıcısı** sayfasında ve perspektiflerde veri tutarlılığı korunurken, özel sorgularınız için örneklenmiş verileri el ile düzeltmeniz gerekir.

Günlük arama sorgusunda örnekleme düzeltmesinin bir örneği aşağıda verilmiştir:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

**Örneklenmiş sayı** alanı tüm girdilerde bulunur ve girişin temsil ettiği veri noktalarının sayısını gösterir. Application Insights uygulamanız için örnekleme açarsanız, **örneklenmiş sayı** 1 ' den büyük olur. Uygulamanızın oluşturduğu girişlerin gerçek sayısını saymak için **örneklenmiş sayı** alanlarını toplayın.

Örnekleme yalnızca uygulamanızın oluşturduğu toplam giriş sayısını etkiler. Bu alanlar, temsil edilen girdilerin ortalamasını gösterdiği için **RequestDuration** veya **kullanılabilirlik süresi** gibi ölçüm alanları için örnekleme düzeltmeniz gerekmez.

## <a name="input-data"></a>Giriş verileri

Çözüm, bağlı Application Insights uygulamalarınızdan aşağıdaki telemetri veri türlerini alır:

- Erişilebilirlik
- Özel Durumlar
- İstekler
- Sayfa görünümleri: çalışma alanınızın sayfa görünümlerini alması için uygulamalarınızı bu bilgileri toplayacak şekilde yapılandırmanız gerekir. Daha fazla bilgi için bkz. [PageViews](../../azure-monitor/app/api-custom-events-metrics.md#page-views).
- Özel olaylar: çalışma alanınızın özel olayları alması için uygulamalarınızı bu bilgileri toplayacak şekilde yapılandırmanız gerekir. Daha fazla bilgi için bkz. [Trackevent](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

Veriler, kullanılabilir hale geldiği için Application Insights Log Analytics tarafından alınır.

## <a name="output-data"></a>Çıktı verileri

Her giriş verisi türü için, *ApplicationInsights* *türünde* bir kayıt oluşturulur. ApplicationInsights kayıtları, aşağıdaki bölümlerde gösterilen özelliklere sahiptir:

### <a name="generic-fields"></a>Genel alanlar

| Özellik | Açıklama |
| --- | --- |
| Tür | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | Kaydın zamanı |
| Uygulama | Application Insights uygulamasının izleme anahtarı |
| ApplicationName | Application Insights uygulamasının adı |
| Roleınstance | Sunucu konağının KIMLIĞI |
| deviceType | İstemci cihazı |
| Ekran çözünürlüğü |   |
| Kıta | İsteğin başlatıldığı kıta |
| Ülke | İsteğin kaynaklandığı ülke/bölge |
| İl | İsteğin kaynaklandığı bölge, eyalet veya yerel ayar |
| Şehir | İsteğin kaynaklandığı şehir veya kent |
| Isyapay | İsteğin bir kullanıcı veya otomatik yöntem tarafından oluşturulup oluşturulmayacağını gösterir. True = otomatik Yöntem veya yanlış = Kullanıcı tarafından oluşturulan |
| SamplingRate | Portala gönderilen SDK tarafından oluşturulan telemetri yüzdesi. Aralık 0.0-100.0. |
| Örnekledcount | 100/(SamplingRate). Örneğin, 4 =&gt; %25 |
| IsAuthenticated | true veya false |
| OperationId | Aynı işlem KIMLIĞINE sahip öğeler portalda Ilgili öğeler olarak gösterilir. Genellikle istek KIMLIĞI |
| Parentoperationıd | Üst işlemin KIMLIĞI |
| için abonelik sınırlarını aştıysanız Hizmet Azaltma gerçekleşir |   |
| Kimliği | İsteğin oluşturulduğu oturumu benzersiz şekilde tanımlamak için GUID |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Kullanılabilirliğine özgü alanlar

| Özellik | Açıklama |
| --- | --- |
| TelemetryType | Erişilebilirlik |
| Kullanılabilirliği olan Bilitytestname | Web testinin adı |
| Kullanılabilirliği | Http isteğinin coğrafi kaynağı |
| Kullanılabilirlik sonucu | Web testinin başarı sonucunu gösterir |
| Kullanılabilirliği olan Bilitymessage | Web testine eklenen ileti |
| Kullanılabilirlik sayısı | 100/(örnekleme hızı). Örneğin, 4 =&gt; %25 |
| DataSizeMetricValue | 1,0 veya 0,0 |
| DataSizeMetricCount | 100/(örnekleme hızı). Örneğin, 4 =&gt; %25 |
| Kullanılabilirlik süresi | Web testi süresinin milisaniye cinsinden süresi |
| AvailabilityDurationCount | 100/(örnekleme hızı). Örneğin, 4 =&gt; %25 |
| Kullanılabilirlik değeri |   |
| Kullanılabilirliği |   |
| Kullanılabilirliği | Web testi için benzersiz GUID |
| Kullanılabilirlik zaman damgası | Kullanılabilirlik testinin kesin zaman damgası |
| AvailabilityDurationMin | Örneklenmiş kayıtlar için bu alan, gösterilen veri noktaları için en düşük Web testi süresini (milisaniye) gösterir |
| AvailabilityDurationMax | Örneklenmiş kayıtlar için bu alan, gösterilen veri noktaları için en yüksek Web testi süresini (milisaniye) gösterir |
| AvailabilityDurationStdDev | Örneklenmiş kayıtlar için bu alan, gösterilen veri noktaları için tüm Web testi süreleri (milisaniye) arasındaki standart sapmayı gösterir |
| Kullanılabilirliği en düşük |   |
| Kullanılabilirliği en fazla |   |
| Kullanılabilirliği STDSAPMA StdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Özel duruma özel alanlar

| Tür | ApplicationInsights |
| --- | --- |
| TelemetryType | Özel durum |
| Türü | Özel durumun türü |
| ExceptionYöntemi | Özel durumu oluşturan yöntemi |
| ExceptionAssembly | Derleme, çerçeveyi ve sürümü ve ortak anahtar belirtecini içerir |
| ExceptionGroup | Özel durumun türü |
| ExceptionHandledAt | Özel durumu ele alan düzeyi gösterir |
| ExceptionCount | 100/(örnekleme hızı). Örneğin, 4 =&gt; %25 |
| ExceptionMessage | Özel durumun iletisi |
| ExceptionStack | Özel durumun tam yığını |
| ExceptionHasStack | Özel durumun bir yığını varsa doğru |



### <a name="request-specific-fields"></a>İsteğe özgü alanlar

| Özellik | Açıklama |
| --- | --- |
| Tür | ApplicationInsights |
| TelemetryType | İste |
| Yanıt kodu | İstemciye gönderilen HTTP yanıtı |
| RequestSuccess | Başarılı veya başarısız olduğunu gösterir. True veya false. |
| No | İsteği benzersiz olarak tanımlamak için KIMLIK |
| RequestName | Al/postala + URL tabanı |
| RequestDuration | İstek süresinin saniye cinsinden süresi |
| URL | Konağın dahil olmadığı isteğin URL 'SI |
| Ana bilgisayar | Web sunucusu Konağı |
| URL tabanı | İsteğin tam URL 'SI |
| ApplicationProtocol | Uygulama tarafından kullanılan protokol türü |
| Istek sayısı | 100/(örnekleme hızı). Örneğin, 4 =&gt; %25 |
| RequestDurationCount | 100/(örnekleme hızı). Örneğin, 4 =&gt; %25 |
| RequestDurationMin | Örneklenmiş kayıtlar için bu alan, gösterilen veri noktaları için en düşük istek süresini (milisaniye) gösterir. |
| RequestDurationMax | Örneklenmiş kayıtlar için bu alan, gösterilen veri noktaları için en fazla istek süresini (milisaniye) gösterir |
| RequestDurationStdDev | Örneklenmiş kayıtlar için bu alan, gösterilen veri noktaları için tüm istek süreleri (milisaniye) arasındaki standart sapmayı gösterir |

## <a name="sample-log-searches"></a>Örnek günlük aramaları

Bu çözüm, panoda gösterilen bir örnek günlük aramaları kümesine sahip değil. Ancak, açıklama içeren örnek günlük arama sorguları, [görüntüleme Application Insights Bağlayıcısı bilgileri](#view-application-insights-connector-information) bölümünde gösterilir.

## <a name="removing-the-connector-with-powershell"></a>Bağlayıcı PowerShell ile kaldırılıyor
OMS portalının kullanımdan kaldırılması sayesinde, mevcut bağlantıları portaldan yapılandırmanın ve kaldırmanın bir yolu yoktur. Mevcut bağlantıları aşağıdaki PowerShell betiğinden kaldırabilirsiniz. Bu işlemi gerçekleştirmek için çalışma alanı ve Application Insights kaynak okuyucu sahibi veya katılımcısı olmanız gerekir.

```powershell
$Subscription_app = "App Subscription Name"
$ResourceGroup_app = "App ResourceGroup"
$Application = "Application Name"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"

Connect-AzAccount
Set-AzContext -SubscriptionId $Subscription_app
$AIApp = Get-AzApplicationInsights -ResourceGroupName $ResourceGroup_app -Name $Application 
Set-AzContext -SubscriptionId $Subscription_workspace
Remove-AzOperationalInsightsDataSource -WorkspaceName $Workspace -ResourceGroupName $ResourceGroup_workspace -Name $AIApp.Id
```

Bir REST API çağrısını çağıran aşağıdaki PowerShell betiğini kullanarak uygulamaların listesini alabilirsiniz. 

```powershell
Connect-AzAccount
$Tenant = "TenantId"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"
$AccessToken = "AAD Authentication Token" 

Set-AzContext -SubscriptionId $Subscription_workspace
$LAWorkspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $ResourceGroup_workspace -Name $Workspace

$Headers = @{
    "Authorization" = "Bearer $($AccessToken)"
    "x-ms-client-tenant-id" = $Tenant
}

$Connections = Invoke-RestMethod -Method "GET" -Uri "https://management.azure.com$($LAWorkspace.ResourceId)/dataSources/?%24filter=kind%20eq%20'ApplicationInsights'&api-version=2015-11-01-preview" -Headers $Headers
$ConnectionsJson = $Connections | ConvertTo-Json
```
Bu betik, Azure Active Directory karşı kimlik doğrulaması için bir taşıyıcı kimlik doğrulama belirteci gerektirir. Bu belirteci almanın bir yolu [REST API belgeleri sitesindeki](https://docs.microsoft.com/rest/api/loganalytics/datasources/createorupdate)bir makaleyi kullanmaktır. **Dene** ' ye tıklayın ve Azure aboneliğinizde oturum açın. Aşağıdaki görüntüde gösterildiği gibi, **Istek önizlemeden** taşıyıcı belirtecini kopyalayabilirsiniz.


![Taşıyıcı belirteci](media/app-insights-connector/bearer-token.png)


Ayrıca, bir günlük sorgusu kullanan uygulamaların bir listesini alabilirsiniz:

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>Sonraki adımlar

- Application Insights uygulamalarınızın ayrıntılı bilgilerini görüntülemek için [günlük araması](../../azure-monitor/log-query/log-query-overview.md) 'nı kullanın.
