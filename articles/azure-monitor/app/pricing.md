---
title: Azure Application Insights kullanımı ve maliyetlerini yönetme | Microsoft Docs
description: Application Insights telemetri birimlerini yönetin ve maliyetleri izleyin.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: DaleKoetke
ms.author: dalek
ms.date: 10/28/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 1749fb4c27a1bfa3048ec0e35c8a09556b0e995b
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74007731"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Application Insights kullanım ve maliyetlerini yönetme

> [!NOTE]
> Bu makalede, Application Insights maliyetlerinizi nasıl anlayacağınızı ve denetleyebileceğinizi açıklamaktadır.  [Kullanımı ve tahmini maliyetleri izleyen](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) ilgili bir makale, farklı fiyatlandırma modelleri için birden çok Azure izleme özelliği genelinde kullanım ve tahmini maliyetlerin nasıl görüntüleneceğini açıklar.

Application Insights, Azure 'da veya şirket içinde barındırıldığından, Web uygulamalarınızın kullanılabilirliğini, performansını ve kullanımını izlemek için gereken her şeyi almak üzere tasarlanmıştır. Application Insights, .NET, Java ve Node. js gibi popüler dilleri ve çerçeveleri destekler ve Azure DevOps, Jira ve Pagerharcı gibi DevOps işlemleri ve araçları ile tümleşir. Uygulamalarınızı izlemenin maliyetlerinin ne olduğunu anlamak önemlidir. Bu makalede, uygulama izleme maliyetlerinizi ve bunları nasıl proaktif olarak izleyip denetleyebileceğinize ilişkin hangi sürücüleri gözden geçiyoruz.

Fiyatlandırma Application Insights için nasıl çalıştığı hakkında sorularınız varsa [forumumuza](https://social.msdn.microsoft.com/Forums/home?forum=ApplicationInsights&filter=alltypes&sort=lastpostdesc)soru gönderebilirsiniz.

## <a name="pricing-model"></a>Fiyatlandırma modeli

[Azure Application Insights][start] fiyatlandırması, veri hacmine dayalı ve isteğe bağlı olarak daha uzun veri saklama Için bir **Kullandıkça Öde** modelidir. Her Application Insights kaynak ayrı bir hizmet olarak ücretlendirilir ve Azure aboneliğiniz için faturaya katkıda bulunur. Veri hacmi, uygulamanızdan Application Insights tarafından alınan sıkıştırılmamış JSON veri paketinin boyutu olarak ölçülür. [Canlı ölçüm akışı](../../azure-monitor/app/live-stream.md)kullanımı için veri hacmi ücretsizdir.

[Çok adımlı Web testleri](../../azure-monitor/app/availability-multistep.md) ek bir ücret doğurur. Çok adımlı Web testleri, bir dizi eylemi gerçekleştiren Web sınamalardır. Tek bir sayfanın *ping testlerine* yönelik ayrı ücret alınmaz. Ping sınamalarından ve çok adımlı testlerin telemetrisi, uygulamanızdan diğer telemetri ile aynı şekilde ücretlendirilir.

## <a name="estimating-the-costs-to-manage-your-application"></a>Uygulamanızı yönetme maliyetlerini tahmin etme 

Henüz Application Insights kullanmıyorsanız, Application Insights kullanım maliyetini tahmin etmek için [Azure izleyici Fiyatlandırma hesaplayıcısı](https://azure.microsoft.com/pricing/calculator/?service=monitor) ' nı kullanabilirsiniz. Arama kutusuna "Azure Izleyici" girerek ve sonuçta elde edilen Azure Izleyici kutucuğuna tıklayarak başlayın. Sayfayı Azure Izleyici 'ye kaydırın ve tür açılan menüsünden Application Insights ' yi seçin.  Buraya, her ay toplamak istediğiniz GB veri sayısını girebilirsiniz; bu nedenle, bu sorunun uygulamanızı izlemeyi Application Insights ne kadar veri toplayacağı. 

Bunu ele almak için iki yaklaşım vardır: ASP.NET SDK 'sında kullanılabilen varsayılan izleme ve Uyarlamalı örnekleme kullanımı veya benzer müşterilerin gördük göre büyük olasılıkla veri alımını tahmin etme.

### <a name="data-collection-when-using-sampling"></a>Örnekleme kullanılırken veri toplama

ASP.NET SDK 'nın [Uyarlamalı örneklenmesi](https://docs.microsoft.com/azure/azure-monitor/app/sampling#adaptive-sampling-in-your-aspnetaspnet-core-web-applications)sayesinde, veri hacmi varsayılan Application Insights izleme için belirtilen en yüksek trafik hızında tutulacak şekilde otomatik olarak ayarlanır. Uygulama, hata ayıklama sırasında veya düşük kullanım nedeniyle düşük miktarda telemetri üretirse, birim saniye başına yapılandırılan olayların altında olduğu sürece, öğeler örnekleme işlemcisi tarafından atılamaz. Saniyede beş olay olan, yüksek hacimli bir uygulama için, uyarlamalı örnekleme, günlük olayların sayısını 432.000 olarak sınırlandırır. Genellikle 1 KB 'lik ortalama bir olay boyutunu kullanarak bu, uygulamanızı barındıran düğüm başına yaklaşık 13,4 GB telemetri (örnekleme her düğüm için yerel olarak yapıldığından) karşılık gelir. 

Uyarlamalı örneklemeyi desteklemeyen SDK 'lar için, [](https://docs.microsoft.com/azure/azure-monitor/app/sampling#ingestion-sampling) [ASP.net, ASP.NET Core ve Java için sabit bir veri yüzdesine göre Application Insights tarafından verilerin ne zaman alındığını gösteren alma örnekleme örneğini kullanabilirsiniz. ](https://docs.microsoft.com/azure/azure-monitor/app/sampling#fixed-rate-sampling-for-aspnet-aspnet-core-java-websites-and-python-applications)Web sunucunuz ve Web tarayıcılardan gönderilen trafiği azaltmak için Web siteleri

### <a name="learn-from-what-similar-customers-collect"></a>Benzer müşterilerin topladıklarından öğrenin

Application Insights için Azure Izleme Fiyatlandırma Hesaplayıcı ' da, "uygulama etkinliğine göre verileri tahmin etme" işlevini etkinleştirirseniz, uygulamanız hakkında (her ay için istek ve sayfa görüntüleme istekleri) bir giriş sağlayabilirsiniz istemci tarafı Telemetriyi toplayın) ve ardından hesaplayıcı, benzer uygulamalar tarafından toplanan ortalama ve 90. yüzdebirlik veri miktarını size bildirir. Bu uygulamalar Application Insights yapılandırma aralığını yaymıştır (bazıları varsayılan [örneklemeye](../../azure-monitor/app/sampling.md)sahiptir, bazıları örnekleme içermez vb.), bu nedenle, örnekleme kullanarak ortanca düzeyin altında aldığınız verilerin hacmini azaltmak için denetime sahip olursunuz. Ancak bu, benzer müşterilerin gördüğünü anlamak için bir başlangıç noktasıdır. 

## <a name="understand-your-usage-and-estimate-costs"></a>Kullanımınız ve Tahmini maliyetlerinizi anlayın

Application Insights, en son kullanım desenlerine göre maliyetlerinizin ne kadar büyük bir süre içinde olduğunu anlamayı kolaylaştırır. Başlamak için, Azure portal Application Insights kaynak için **kullanım ve tahmini maliyetler** sayfasına gidin: 

![Fiyatlandırma seçin](./media/pricing/pricing-001.png)

A. Veri hacminin ayı için gözden geçirin. Bu, sunucunuza ve istemci uygulamalarınıza ve kullanılabilirlik testlerinden alınan ve saklanan tüm verileri (herhangi bir [örnekleme](../../azure-monitor/app/sampling.md)sonrasında) içerir.  
B. [Çok adımlı Web testleri](../../azure-monitor/app/availability-multistep.md)için ayrı bir ücret yapılır. (Bu, veri hacmi ücretine dahil olan basit kullanılabilirlik testlerini içermez.)  
C. Son ay için veri hacmi eğilimlerini görüntüleyin.  
D. Veri alımı [örneklemesi](../../azure-monitor/app/sampling.md)etkinleştirin.   
E. Günlük veri hacmi ucunu ayarlayın.  

(Bu makaledeki ekran görüntülerinde görüntülenen tüm fiyatların yalnızca örnek amaçlıdır. Para birimi ve bölgenizin geçerli fiyatları için bkz. [Application Insights fiyatlandırması][pricing].)

Application Insights kullanımınızı daha derin araştırmak için **ölçümler** sayfasını açın, "veri noktası birimi" adlı ölçümü ekleyin ve ardından verileri "Telemetri öğe türü" olarak bölmek Için *bölmeyi Uygula* seçeneğini belirleyin. 

Application Insights ücretleri Azure faturanızda eklenir. Azure faturanızın ayrıntılarını Azure portal veya [Azure Faturalandırma portalındaki](https://account.windowsazure.com/Subscriptions) **faturalandırma** bölümünde görebilirsiniz. 

![Sol menüde Faturalandırma ' i seçin.](./media/pricing/02-billing.png)

### <a name="using-data-volume-metrics"></a>Veri hacmi ölçümlerini kullanma
<a id="understanding-ingested-data-volume"></a>

Veri birimleriniz hakkında daha fazla bilgi edinmek için Application Insights kaynağınız için **ölçümleri** seçin, yeni bir grafik ekleyin. Grafik ölçümü için, **günlük tabanlı ölçümler**altında, **veri noktası birimi**' ni seçin. **Bölmeyi Uygula**' ya tıklayın ve Group By **Telemetryitem Type**' ı seçin.

![Veri hacmine bakmak için ölçümleri kullanma](./media/pricing/10-billing.png)

### <a name="queries-to-understand-data-volume-details"></a>Veri hacmi ayrıntılarını anlamak için sorgular

Örneğin, sorgu ile son 24 saat içinde alınan veri hacmi görmek için `systemEvents` tablosunu kullanabilirsiniz:

```kusto
systemEvents 
| where timestamp >= ago(24h)
| where type == "Billing" 
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

Ya da son 30 güne ait veri türüne göre veri hacmi (bayt cinsinden) grafiğini görmek için şunu kullanabilirsiniz:

```kusto
systemEvents 
| where timestamp >= startofday(ago(30d))
| where type == "Billing" 
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes) by BillingTelemetryType, bin(timestamp, 1d) | render barchart  
```

Bu sorgunun, veri birimlerinde uyarı ayarlamak için bir [Azure günlük uyarısında](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log) kullanılabileceğini unutmayın.  

Telemetri verileri değişiklikleriniz hakkında daha fazla bilgi edinmek için sorguyu kullanarak olayların sayısını türe göre kontrol edelim:

```kusto
systemEvents 
| where timestamp >= startofday(ago(30d))
| where type == "Billing" 
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| summarize count() by BillingTelemetryType, bin(timestamp, 1d) | render barchart  
```

Birimde benzer bir değişiklik varsa, baytlardaki bayt cinsinden görüldüğü gibi, artan sayıları gösteren olayların veri türlerine odaklanabiliyoruz.  Örneğin, bağımlılıkların sayısının arttığı gözlemleniyorsa, artmasından hangi işlemlerin sorumlu olduğunu anlamak için bir sorgu aşağıda verilmiştir:

```kusto
dependencies 
| where timestamp >= startofday(ago(30d))
| summarize count() by operation_Name, bin(timestamp, 1d)  
| render barchart  
```


## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>Azure faturanızda Application Insights kullanımı görüntüleme 

Azure, [Azure maliyet yönetimi + faturalandırma](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) hub 'ında yararlı bir işlevsellik sağlar. Örneğin, "maliyet analizi" işlevi, Azure kaynakları için kullandığınız süreyi görüntülemenize olanak sağlar. Kaynak türüne göre bir filtre (Microsoft. Insights/Application Insights için) ekleme, harcamalarınızı izlemenize imkan tanır.

Kullanımınız [Azure Portal kullanımınıza indirilerek](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)kullanımınız daha fazla anlama kazanılabilir.
İndirilen elektronik tabloda günde Azure kaynağı başına kullanımı görebilirsiniz. Bu Excel elektronik tablosunda, Application Insights kaynaklarınızdan kullanım, "Application Insights" ve "Log Analytics" göstermek için "ölçüm kategorisi" sütununda filtrelenebilir ve sonra "Microsoft. Insights/bileşenleri içerir" olan "örnek KIMLIĞI" sütununa bir filtre eklenerek bulunabilir.  Application Insights kullanımı, tüm Azure Izleyici bileşenleri için tek bir günlük arka ucu olduğundan, Log Analytics ölçüm kategorisiyle ölçü üzerinden raporlanır.  Yalnızca eski fiyatlandırma katmanlarında Application Insights kaynaklar ve çok adımlı Web testleri, Application Insights ölçüm kategorisiyle raporlanır.  Kullanım "tüketilen miktar" sütununda gösterilir ve her girdinin birimi "ölçü birimi" sütununda gösterilir.  [Microsoft Azure faturanızı anlamanıza](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)yardımcı olacak daha fazla ayrıntı bulabilirsiniz. 


## <a name="managing-your-data-volume"></a>Veri hacminin yönetilmesi 

Aşağıdaki teknikler kullanılarak, göndereceğiniz verilerin hacmi yönetilebilir:

* **Örnekleme**:, ölçümlerde en az deformasyon ile sunucu ve istemci uygulamalarından gönderilen telemetri miktarını azaltmak için örnekleme kullanabilirsiniz. Örnekleme, göndereceğiniz veri miktarını ayarlamak için kullanabileceğiniz birincil araçtır. [Örnekleme özellikleri](../../azure-monitor/app/sampling.md)hakkında daha fazla bilgi edinin.

* **Ajax çağrılarını sınırlandırma**: her sayfa görünümünde [bildirilemeyen Ajax çağrısı sayısını sınırlayabilir](../../azure-monitor/app/javascript.md#configuration) veya Ajax raporlamayı devre dışı bırakabilirsiniz.

* **Gereksiz modülleri devre dışı bırak**: gerekli olmayan koleksiyon modüllerini kapatmak Için [ApplicationInsights. config dosyasını düzenleyin](../../azure-monitor/app/configuration-with-applicationinsights-config.md) . Örneğin, performans sayaçları veya bağımlılık verilerinin yoksayılması olduğuna karar verebilirsiniz.

* **Ön toplama ölçümleri**: uygulamanızda trackmetric çağrıları yerleştirirseniz, bir ölçüm toplu işinin ortalama ve standart sapması hesaplamasını kabul eden aşırı yüklemeyi kullanarak trafiği azaltabilirsiniz. Ya da, [önceden toplama paketi](https://www.myget.org/gallery/applicationinsights-sdk-labs)de kullanabilirsiniz.
 
* **Günlük üst sınır**: Azure Portal Application Insights kaynak oluşturduğunuzda, günlük sınır 100 GB/gün olarak ayarlanır. Visual Studio 'da bir Application Insights kaynağı oluşturduğunuzda, varsayılan değer küçüktür (yalnızca 32,3 MB/gün). Günlük uç varsayılan, testi kolaylaştırmak için ayarlanır. Kullanıcının uygulamayı üretime dağıtmadan önce günlük üst sınırı oluşturması amaçlanmıştır. 

    Yüksek trafikli bir uygulama için daha yüksek bir en yüksek değer istemediğiniz müddetçe en büyük sınır 1.000 GB/gün olur. 
    
    Günlük Cap hakkındaki uyarı e-postaları, Application Insights kaynağınız için bu rollerin üyesi olan hesaba gönderilir: "ServiceAdmin", "AccountAdmin", "CoAdmin", "Owner".

    Günlük ucunu ayarlarken dikkatli kullanın. Amacınızı *hiçbir şekilde günlük tepesine vurmamanız*gerekir. Günlük üst sınıra ulaşırsanız, günün geri kalanı için verileri kaybeder ve uygulamanızı izleyemezsiniz. Günlük ucunu değiştirmek için, **günlük hacim Cap** seçeneğini kullanın. Bu seçeneğe **kullanım ve tahmini maliyetler** bölmesinde erişebilirsiniz (Bu, makalenin ilerleyen kısımlarında daha ayrıntılı olarak açıklanmıştır).
    
    Application Insights için kullanılamayacak kredi içeren bazı abonelik türlerinde kısıtlama kaldırdık. Daha önce, aboneliğin bir harcama limiti varsa, günlük sınır iletişim kutusunda harcama limitini kaldırma ve günlük ucunun 32,3 MB/gün dışında bir şekilde çıkarılması için yönergeler vardır.
    
* **Daraltma**: azaltma, veri hızını saniyede 32.000 olay ile sınırlandırır, izleme anahtarı başına 1 dakikadan fazla. Uygulamanızın gönderdiği veri hacmi her dakikada değerlendirilir. Dakika boyunca ortalama saniye başına oranı aşarsa, sunucu bazı istekleri reddeder. SDK verileri arabelleğe alır ve sonra yeniden göndermeyi dener. Birkaç dakika içinde bir aşırı gerilim yayın. Uygulamanız sürekli olarak verileri daraltma hızından daha fazla gönderirse, bazı veriler bırakılır. (ASP.NET, Java ve JavaScript SDK 'Ları verileri bu şekilde yeniden göndermeyi dener; diğer SDK 'lar yalnızca daraltılmış verileri de bırakabilir.) Daraltma gerçekleşirse, bunun oluştuğunu bildiren bir bildirim uyarısı görürsünüz.

## <a name="manage-your-maximum-daily-data-volume"></a>Günlük veri hacminin maksimum sayısını yönetin

Toplanan verileri sınırlandırmak için günlük hacim ucunu kullanabilirsiniz. Ancak, sınır karşılanıyorsa günün geri kalanı için uygulamanızdan gönderilen tüm Telemetriyi bir kayıp olur. Uygulamanızın günlük tepesine isabet etmek *önerilmez* . Günlük üst sınıra ulaştıktan sonra uygulamanızın sistem durumunu ve performansını izleyemezsiniz.

Günlük birim Cap 'i kullanmak yerine, veri hacmi istediğiniz düzeye ayarlamak için [örnekleme](../../azure-monitor/app/sampling.md) 'yı kullanın. Daha sonra, uygulamanızın çok daha yüksek sayıda telemetri göndermek için beklenmedik şekilde başlaması durumunda günlük ucunu yalnızca "son çare" olarak kullanın.

### <a name="identify-what-daily-data-limit-to-define"></a>Tanımlamak için hangi günlük veri sınırınızın tanımlayın

Veri alma eğilimi ve tanımlanacak günlük hacim üst sınırı olduğunu anlamak için Application Insights kullanımı ve tahmini maliyetleri gözden geçirin. Sınıra ulaşıldıktan sonra kaynaklarınızı izleyin mümkün olmayacaktır beri dikkatlice değerlendirilmelidir. 

### <a name="set-the-daily-cap"></a>Günlük ucunu ayarla

Günlük ucunu değiştirmek için, Application Insights kaynağınızın **Yapılandır** bölümünde, **kullanım ve tahmini maliyetler** sayfasında, **günlük üst sınır**' ı seçin.

![Günlük telemetri birimi ucunu ayarla](./media/pricing/pricing-003.png)

[Günlük ucunu Azure Resource Manager ile değiştirmek](../../azure-monitor/app/powershell.md)için, değiştirilecek Özellik `dailyQuota`.  Azure Resource Manager aracılığıyla `dailyQuotaResetTime` ve günlük Cap `warningThreshold`da ayarlayabilirsiniz. 

## <a name="sampling"></a>Örnekleme
[Örnekleme](../../azure-monitor/app/sampling.md) , tanılama aramaları sırasında ilgili olayları bulma özelliğini korurken, telemetrinin uygulamanıza gönderilme hızını azaltma yöntemidir. Ayrıca, doğru olay sayılarını da koruyabilirsiniz.

Örnekleme, ücretleri azaltmak ve aylık kotasında kalmak için etkili bir yoldur. Örnekleme algoritması ilgili telemetri öğelerini korur, örneğin, ara 'yı kullandığınızda belirli bir özel durumla ilgili isteği bulabilirsiniz. Algoritma Ayrıca, istek hızları, özel durum ücretleri ve diğer sayımlar için ölçüm Gezgininde doğru değerleri görmeniz için doğru sayıları de korur.

Birçok örnekleme biçimi vardır.

* [Uyarlamalı örnekleme](../../azure-monitor/app/sampling.md) , ASP.NET SDK için varsayılandır. Uyarlamalı örnekleme, uygulamanızın gönderdiği telemetri hacmine otomatik olarak ayarlanır. Ağ üzerindeki telemetri trafiğinin azaltılabilmesi için Web uygulamanızdaki SDK 'da otomatik olarak çalışır. 
* Alım *örnekleme* , uygulamanızdaki Telemetriyi Application Insights hizmetine girdiği noktada çalışan bir alternatiftir. Alım örnekleme, uygulamanızdan gönderilen telemetri hacmini etkilemez, ancak hizmet tarafından tutulan hacmi azaltır. Tarayıcıların ve diğer SDK 'lardan telemetri tarafından kullanılan kotayı azaltmak için alma örneklemesini kullanabilirsiniz.

Alım örneklemesini ayarlamak için **fiyatlandırma** bölmesine gidin:

![Kota ve fiyatlandırma bölmesinde örnekler kutucuğunu seçin ve ardından bir örnekleme kesri seçin](./media/pricing/pricing-004.png)

> [!WARNING]
> **Veri örnekleme** bölmesi yalnızca alma örneklemenin değerini denetler. Uygulamanızda Application Insights SDK tarafından uygulanan örnekleme oranını yansıtmaz. Gelen telemetri SDK 'da zaten örneklenir, Alım örnekleme uygulanmaz.
>

Gerçek örnekleme oranını öğrenmek için, nereye uygulandığını fark etmeksizin bir [analiz sorgusu](analytics.md)kullanın. Sorgu şöyle görünür:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

Her tutulan kaydında `itemCount`, temsil ettiği orijinal kayıt sayısını belirtir. 1 + önceki atılan kayıtların sayısına eşittir. 

## <a name="change-the-data-retention-period"></a>Veri saklama süresini değiştirme

Application Insights kaynakları için varsayılan saklama 90 gündür. Her bir Application Insights kaynağı için farklı saklama süreleri seçilebilir. Kullanılabilir saklama dönemlerinin tam kümesi 30, 60, 90, 120, 180, 270, 365, 550 veya 730 günleridir. 

Application Insights kaynağınız, saklama süresini değiştirmek için **kullanım ve tahmini maliyetler** sayfasına gidin ve **veri saklama** seçeneğini belirleyin:

![Günlük telemetri birimi ucunu ayarla](./media/pricing/pricing-005.png)

Saklama Ayrıca, `retentionInDays` parametresi kullanılarak [PowerShell kullanılarak program aracılığıyla de ayarlanabilir](powershell.md#set-the-data-retention) . Ayrıca, veri bekletmesini 30 güne ayarlarsanız, uyumluluk ile ilgili senaryolar için faydalı olabilecek `immediatePurgeDataOn30Days` parametresini kullanarak eski verilerin hemen temizliğini tetikleyebilirsiniz. Bu temizleme işlevi yalnızca Azure Resource Manager aracılığıyla sunulur ve çok dikkatli kullanılmalıdır. 

## <a name="data-transfer-charges-using-application-insights"></a>Application Insights kullanarak veri aktarımı ücretleri

Verilerin Application Insights gönderilmesi veri bant genişliği ücretlerine neden olabilirler. [Azure bant genişliği fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/bandwidth/)açıklandığı gibi, iki bölgede bulunan Azure hizmetleri arasındaki veri aktarımı, normal fiyata giden veri aktarımı olarak ücretlendirilir. Gelen veri aktarımı ücretsizdir. Ancak, bu ücret çok küçük (az%) Application Insights günlük verisi alma maliyetleriyle karşılaştırılır. Sonuç olarak, Log Analytics için maliyetleri denetlemek, verileri alınan veri hacminin üzerine odaklamalıdır ve [burada](https://docs.microsoft.com/azure/azure-monitor/app/pricing#managing-your-data-volume)bu konuda bilgi sağlanmasına yardımcı olacak rehberlik sunuyoruz.   

## <a name="limits-summary"></a>Limit Özeti

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Günlük uç e-postalarını devreden çıkar

Günlük birim Cap e-postalarını devre dışı bırakmak için, Application Insights kaynağınızın **Yapılandır** bölümünde, **kullanım ve tahmini maliyetler** bölmesinde **günlük üst sınır**' ı seçin. Büyük/veya ayarlanabilir bir uyarı düzeyine ulaşıldığında e-posta gönderme ayarları vardır. Tüm günlük uç hacimlerle ilgili e-postaları devre dışı bırakmak isterseniz her iki kutunun işaretini kaldırın.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Eski Kurumsal (düğüm başına) Fiyatlandırma Katmanı

Azure Application Insights 'in erken benimseme için, hala iki olası fiyatlandırma katmanı vardır: temel ve kurumsal. Temel fiyatlandırma katmanı yukarıda açıklananla aynıdır ve varsayılan katmandır. Ek bir ücret ödemeden tüm kurumsal katman özelliklerini içerir. Temel katman, birincil olarak alınan verilerin hacmi üzerinde yer alır. 

> [!NOTE]
> Bu eski fiyatlandırma katmanları yeniden adlandırıldı. Kurumsal fiyatlandırma katmanı artık **düğüm başına** çağrılır ve temel fiyatlandırma KATMANı artık **GB başına**çağırılır. Bu yeni adlar aşağıda ve Azure portal kullanılır.  

Düğüm başına (eskiden Enterprise) katmanının düğüm başına ücreti vardır ve her düğüm günlük veri indirimi alır. Düğüm başına fiyatlandırma katmanında, dahil edilen indirimin üzerinde alınan veriler için ücretlendirilirsiniz. Operations Management Suite kullanıyorsanız düğüm başına katmanını seçmeniz gerekir. 

Para birimi ve bölgenizin geçerli fiyatları için bkz. [Application Insights fiyatlandırması](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> 2018 Nisan 'da, Azure izleme için [Yeni bir fiyatlandırma](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) modeli sunuyoruz. Bu model, izleme hizmetlerinin tam portföyüne ilişkin basit bir "Kullandıkça öde" modeli benimsemektedir. [Yeni fiyatlandırma modeli](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs)hakkında daha fazla bilgi edinin, kullanım modellerinize bağlı olarak [Bu modele geçme etkisini nasıl değerlendirirsiniz](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#understanding-your-azure-monitor-costs) ve [yeni modeli nasıl kabul](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#azure-monitor-pricing-model) edebilirsiniz

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Düğüm başına katman ve Operations Management Suite abonelik yetkilendirmeleri

Operations Management Suite E1 ve E2 satın alan müşteriler, düğüm başına Application Insights [daha önce duyurulan](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/)gibi ek ücret ödemeden ek bir bileşen olarak alabilir. Özellikle, her bir Operations Management Suite E1 ve E2 birimi düğüm katmanı başına Application Insights bir düğüme yönelik bir yetkilendirme içerir. Her Application Insights düğüm, hiçbir ek ücret ödemeden, 90 günlük veri saklama ile günde en fazla 200 MB veri alımı (Log Analytics veri alma işleminden ayrı) içerir. Katman, makalenin ilerleyen bölümlerinde daha ayrıntılı olarak açıklanmıştır. 

Bu katman yalnızca Operations Management Suite aboneliği olan müşterilere uygulanabilir olduğundan, Operations Management Suite aboneliğine sahip olmayan müşteriler bu katmanı seçme seçeneği görmez.

> [!NOTE]
> Bu yetkilendirmesini aldığınızdan emin olmak için Application Insights kaynaklarınızın, düğüm başına fiyatlandırma katmanında olması gerekir. Bu yetkilendirme yalnızca düğüm olarak geçerlidir. GB başına katmanda Application Insights kaynak, hiçbir avantaj sunmaz. Bu yetkilendirme, **kullanım ve tahmini maliyet** bölmesinde gösterilen tahmini maliyetlerde görünür değildir. Ayrıca, bir aboneliği, 2018 Nisan 'da yeni Azure izleme fiyatlandırma modeline taşırsanız, kullanılabilir tek katmandır. Bir Operations Management Suite aboneliğiniz varsa, aboneliğin yeni Azure izleme fiyatlandırma modeline taşınması önerilmez.

### <a name="how-the-per-node-tier-works"></a>Düğüm başına katmanın çalışma şekli

* Düğüm başına katmandaki tüm uygulamalar için telemetri gönderen her düğüm için ödeme yaparsınız.
  * *Düğüm* , uygulamanızı barındıran bir fiziksel veya sanal sunucu makinesi veya hizmet olarak platform bir rol örneğidir.
  * Geliştirme makineleri, istemci tarayıcıları ve mobil cihazlar düğüm olarak sayılmaz.
  * Uygulamanızda, Web hizmeti ve arka uç çalışanı gibi telemetri gönderen birkaç bileşen varsa, bileşenler ayrı olarak sayılır.
  * [Canlı ölçüm akışı](../../azure-monitor/app/live-stream.md) veriler fiyatlandırma amacıyla sayılmaz. Bir abonelikte, ücretleriniz uygulama başına değil düğüm başına alınır. 12 uygulama için telemetri gönderen beş düğümünüz varsa, ücret beş düğüm için yapılır.
* Ücretler ayda tırnak içine alınmış olsa da, yalnızca bir düğümün bir uygulamadan telemetri gönderdiği saatler için ücretlendirilirsiniz. Saatlik ücret, 744 ile bölünmüş, saatlik olarak aylık ücretlendirilir (31 günlük aydaki saat sayısı).
* Algılanan her düğüm için günde 200 MB 'lık bir veri birimi ayırması verilir (saatlik ayrıntı düzeyi ile). Kullanılmayan veri ayırma bir günden sonrakine taşınmaz.
  * Düğüm başına fiyatlandırma katmanını seçerseniz, her abonelik, Bu abonelikteki Application Insights kaynaklarına telemetri gönderen düğüm sayısına bağlı olarak günlük bir veri tahsisatı alır. Bu nedenle, her gün veri gönderen beş düğümünüz varsa, Bu abonelikteki tüm Application Insights kaynaklarına 1 GB 'lik bir havuz uygulanmış olması gerekir. Dahil edilen veriler tüm düğümlerde paylaşıldığından, bazı düğümlerin diğer düğümlere kıyasla daha fazla veri gönderemediği kesin değildir. Belirli bir gün içinde Application Insights kaynakları, bu abonelik için günlük veri ayırmaya dahil olandan daha fazla veri alır, GB başına fazla kullanım ücreti uygulanır. 
  * Günlük veri tahsisatı, her bir düğümün Telemetriyi 200 MB ile ayırarak, gün içindeki saat sayısı (UTC kullanılarak) olarak hesaplanır. Bu nedenle, günde 24 saat boyunca telemetri gönderen dört düğümünüz varsa, söz konusu gün için dahil edilen veri ((4 &#215; 15)/24) &#215; 200 MB = 500 MB olur. Veriler fazla kullanım için GB başına 2,30 ABD Doları tutarında, düğümler günde 1 GB veri gönderse, ücret 1,15 ABD doları olur.
  * Düğüm başına katmanı günlük indirimi, GB başına katmanı seçtiğiniz uygulamalarla paylaşılmaz. Kullanılmayan kesinti, günde bir günden fazla taşınmaz. 

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Farklı düğüm sayısını belirleme örnekleri

| Senaryo                               | Günlük toplam düğüm sayısı |
|:---------------------------------------|:----------------:|
| 3 Azure App Service örnekleri ve 1 sanal sunucu kullanan 1 uygulama | 4 |
| 2 VM 'de çalışan 3 uygulama; Bu uygulamalar için Application Insights kaynakları aynı abonelikte ve düğüm başına katmanda bulunur | 2 | 
| Uygulama öngörüleri kaynakları aynı abonelikte olan 4 uygulama; 2 örnek çalıştıran her uygulama 16 yoğun saatler sırasında 2 örnek çalıştıran her uygulama ve 8 yoğun saat boyunca 4 örnek | 13.33 | 
| 1 çalışan rolü ve 1 Web rolü olan bulut Hizmetleri, her biri 2 örnek çalıştırıyor | 4 | 
| 50 mikro hizmet çalıştıran 5 düğümlü Azure Service Fabric kümesi; 3 örnek çalıştıran her bir mikro hizmet | 5|

* Kesin düğüm sayma, uygulamanızın kullandığı Application Insights SDK 'sına bağlıdır. 
  * SDK 2,2 ve sonraki sürümlerinde, hem Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) hem de [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 'sı her uygulama konağını bir düğüm olarak bildirir. Örnek olarak, fiziksel sunucu ve VM konakları için bilgisayar adı veya bulut hizmetleri için örnek adı verilebilir.  Tek özel durum yalnızca [.NET Core](https://dotnet.github.io/) ve APPLICATION INSIGHTS Core SDK kullanan bir uygulamadır. Bu durumda, konak adı kullanılamadığından tüm konaklar için yalnızca bir düğüm raporlanır. 
  * SDK 'nın önceki sürümlerinde [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) , daha yeni SDK sürümleri gibi davranır, ancak [çekirdek SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) , uygulama ana bilgisayarlarının sayısından bağımsız olarak yalnızca bir düğüm bildirir. 
  * Uygulamanız **Roleınstance** 'ı özel bir değere ayarlamak için SDK kullanıyorsa, varsayılan olarak, düğüm sayısını belirlemede aynı değer kullanılır. 
  * İstemci makinelerinden veya mobil cihazlardan çalışan bir uygulamayla yeni bir SDK sürümü kullanıyorsanız, düğüm sayısı büyük bir sayı döndürebilir (çok sayıda istemci makinesi veya mobil cihaz nedeniyle). 

## <a name="automation"></a>Otomasyon

Fiyatlandırma katmanını Azure Kaynak Yönetimi 'ni kullanarak ayarlamak için bir komut dosyası yazabilirsiniz. [Nasıl olduğunu öğrenin](powershell.md#price).


## <a name="next-steps"></a>Sonraki adımlar

* [Örnekleme](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
