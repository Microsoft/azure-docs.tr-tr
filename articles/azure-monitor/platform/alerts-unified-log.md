---
title: Azure Izleyici 'de günlük uyarıları
description: Azure uyarıları için belirttiğiniz analitik sorgu Koşulları karşılandığında e-postaları, bildirimleri, çağrı Web siteleri URL 'Lerini (Web kancaları) veya Otomasyonu tetikleyin.
author: yanivlavi
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 5/31/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: d0314e94e627a42ab55f9e91017acac0cdc8b541
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001627"
---
# <a name="log-alerts-in-azure-monitor"></a>Azure Izleyici 'de günlük uyarıları

Bu makale, [Azure uyarıları](../../azure-monitor/platform/alerts-overview.md) 'nda desteklenen uyarı türlerinden biridir ve kullanıcıların uyarı için temel olarak Azure Analytics platformunu kullanmasına izin verir.

Günlük uyarısı, [Azure Izleyici günlükleri](../../azure-monitor/learn/tutorial-viewdata.md) veya [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events)için oluşturulan günlük arama kurallarından oluşur. Kullanımı hakkında daha fazla bilgi edinmek için bkz. [Azure 'da günlük uyarıları oluşturma](../../azure-monitor/platform/alerts-log.md)

> [!NOTE]
> [Azure Izleyici günlüklerinden](../../azure-monitor/learn/tutorial-viewdata.md) popüler günlük verileri artık Azure izleyici 'de ölçüm platformunda de kullanıma sunulmuştur. Ayrıntılar görünümü için, [Günlükler Için ölçüm uyarısı](../../azure-monitor/platform/alerts-metric-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>Günlük arama uyarısı kuralı-tanım ve türler

Belirtilen günlük sorgularını düzenli aralıklarla otomatik olarak çalıştırmak için, Azure Uyarıları tarafından günlük arama kuralları oluşturulur.  Günlük sorgusunun sonuçları belirli ölçütlerle eşleşiyorsa bir uyarı kaydı oluşturulur. Ardından kural, [Eylem Grupları](../../azure-monitor/platform/action-groups.md)'nı kullanıp bir veya birden çok eylemi otomatik olarak çalıştırabilir. Günlük uyarılarını oluşturmak, değiştirmek ve güncelleştirmek için [Azure Izleme katılımcısı](../../azure-monitor/platform/roles-permissions-security.md) rolü gerekli olabilir; erişim & uyarı kuralında veya uyarı sorgusunda analiz hedeflerinin sorgu yürütme haklarıyla birlikte. Kullanıcı oluşturmanın uyarı kuralındaki veya uyarı sorgusundaki tüm analiz hedefine erişimi yoksa, kural oluşturma başarısız olabilir veya günlük uyarı kuralı kısmi sonuçlarla yürütülür.

Günlük arama kuralları aşağıdaki Ayrıntılar tarafından tanımlanır:

- **Günlük sorgusu**.  Uyarı kuralı her tetiklendiğinde çalışan sorgu.  Bu sorgu tarafından döndürülen kayıtlar, bir uyarının tetiklenip tetiklenmeyeceğini tespit etmek için kullanılır. Analiz sorgusu, belirli bir Log Analytics çalışma alanı veya Application Insights uygulaması için, hatta [birden çok Log Analytics ve Application Insights kaynak](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) arasında, hatta kullanıcının erişimi ve tüm kaynaklara yönelik sorgu haklarını de kapsayabilir. 
    > [!IMPORTANT]
    > yalnızca [scheduledQueryRules API kullanılarak yapılandırılan Log Analytics](../../azure-monitor/platform/alerts-log-api-switch.md) için Application Insights ve günlük uyarılarında [çapraz kaynak sorgu](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) desteği.

    Bazı analitik komutlar ve birleşimler, günlük uyarılarında kullanılmak üzere uyumsuzdur; daha fazla ayrıntı görünümü için, [Azure izleyici 'de uyarı sorgularını günlüğe kaydedin](../../azure-monitor/platform/alerts-log-query.md).

- **Zaman aralığı**.  Sorgu için zaman aralığını belirtir. Sorgu yalnızca bu geçerli zaman aralığı içinde oluşturulmuş olan kayıtları döndürür. Zaman aralığı, günlük sorgusu için getirilen verileri, kötüye kullanımı önlemeye ve günlük sorgusunda kullanılan zaman komutunu (kullanım gibi) atlamaya karşı kısıtlar. <br>*Örneğin, zaman aralığı 60 dakikaya ayarlanmışsa ve sorgu 1:15 PM 'de çalıştırıldığında, günlük sorgusuna yürütmek için yalnızca 12:15 PM ve 1:15 PM arasında oluşturulan kayıtlar döndürülür. Artık günlük sorgusu önce Time komutunu (7d) kullanıyorsa, günlük sorgusu yalnızca geçmiş 60 dakika boyunca veriler varsa, 12:15 PM ve 1:15 PM arasındaki veriler için çalıştırılır. Günlük sorgusunda belirtildiği gibi yedi günlük veri değildir.*

- **Sıklık**.  Sorgunun ne sıklıkta çalıştırılacağını belirtir. 5 dakika ile 24 saat arasında herhangi bir değer olabilir. Zaman dilimine eşit veya ondan küçük olmalıdır.  Değer, zaman aralığından fazlaysa, kayıtların kaçırılmakta olması risklidir.<br>*Örneğin, 30 dakikalık bir zaman aralığı ve 60 dakikalık bir sıklık göz önünde bulundurun.  Sorgu 1:00 adresinde çalışıyorsa, 12:30 ve 1:00 PM arasındaki kayıtları döndürür.  Sorgunun bir sonraki çalışması, 1:30 ve 2:00 arasındaki kayıtları döndürmesi durumunda 2:00 ' dir.  1:00 ile 1:30 arasında oluşturulan tüm kayıtlar hiçbir şekilde değerlendirilmeyecektir.*

- **Eşik**.  Günlük aramasının sonuçları, bir uyarının oluşturulması gerekip gerekmediğini belirleyecek şekilde değerlendirilir.  Eşik, farklı günlük arama uyarısı kuralları türleri için farklıdır.

Günlük arama kuralları [Azure Izleyici günlükleri](../../azure-monitor/learn/tutorial-viewdata.md) veya [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events)için değil, iki tür olabilir. Bu türlerin her biri, izleyen bölümlerde ayrıntılı olarak açıklanmıştır.

- **[Sonuç sayısı](#number-of-results-alert-rules)** . Günlük araması tarafından döndürülen sayı kayıtları belirtilen bir sayıyı aştığında oluşturulan tek uyarı.
- **[Ölçüm ölçümü](#metric-measurement-alert-rules)** .  Günlük aramasının sonuçlarında belirtilen eşiği aşan değerlerle her bir nesne için uyarı oluşturuldu.

Uyarı kuralı türleri arasındaki farklar şunlardır.

- *Sonuç sayısı* uyarı kuralları her zaman tek bir uyarı oluşturur, ancak *ölçüm ölçümü* uyarı kuralı eşiği aşan her nesne için bir uyarı oluşturur.
- Eşik tek bir kez aşıldığında, sonuç uyarı kuralları *sayısı* uyarı oluşturur. *Ölçüm ölçümü* uyarı kuralları, eşik belirli bir zaman aralığında belirli bir sayıda aşıldığında uyarı oluşturabilir.

### <a name="number-of-results-alert-rules"></a>Sonuç uyarı kuralları sayısı

Arama sorgusu tarafından döndürülen kayıt sayısı belirtilen eşiği aştığında, sonuç uyarı kuralları **sayısı** tek bir uyarı oluşturur. Bu tür uyarı kuralı, Windows olay günlükleri, syslog, WebApp yanıtı ve özel Günlükler gibi olaylarla çalışmak için idealdir.  Belirli bir hata olayı oluşturulduğunda veya belirli bir süre içinde birden çok hata olayı oluşturulduğunda bir uyarı oluşturmak isteyebilirsiniz.

**Eşik**: bir dizi sonuç uyarısı kuralı eşiği, belirli bir değerden daha büyük veya bu değerden daha küçük.  Günlük araması tarafından döndürülen kayıt sayısı bu ölçütlerle eşleşiyorsa, bir uyarı oluşturulur.

Tek bir olay üzerinde uyarı almak için, sonuç sayısını 0 ' dan büyük olarak ayarlayın ve sorgunun son çalıştırıldığı zamandan sonra oluşturulan tek bir olayın oluşumunu kontrol edin. Bazı uygulamalar, bir uyarı oluşturmaması gerekmeyen zaman zaman bir hatayı günlüğe kaydeder.  Örneğin, uygulama hata olayını oluşturan işlemi yeniden deneyebilir ve sonra bir sonraki sefer başarılı olur.  Bu durumda, belirli bir süre içinde birden çok olay oluşturulmadığı takdirde uyarı oluşturmak istemeyebilirsiniz.  

Bazı durumlarda, bir olay yokluğunda bir uyarı oluşturmak isteyebilirsiniz.  Örneğin, bir işlem düzenli olayları, düzgün çalıştığını göstermek için günlüğe kaydeder.  Belirli bir süre içinde bu olaylardan birini günlüğe kaydetmez, bir uyarının oluşturulması gerekir.  Bu durumda, eşiği **1 ' den küçük**olarak ayarlamanız gerekir.

#### <a name="example-of-number-of-records-type-log-alert"></a>Kayıt sayısı tür günlüğü uyarısı örneği

Web tabanlı uygulamanız kod 500 (yani) Iç sunucu hatası olan kullanıcılara yanıt verdiği zaman bir senaryo düşünün. Aşağıdaki ayrıntılarla bir uyarı kuralı oluşturursunuz:  

- **Sorgu:** istekler | WHERE resultCode = = "500"<br>
- **Zaman aralığı:** 30 dakika<br>
- **Uyarı sıklığı:** beş dakika<br>
- **Eşik değeri:** 0 ' dan büyük<br>

Ardından uyarı, sonuç kodu 500 olan herhangi bir kaydı aramak üzere 30 dakikalık verilerle her 5 dakikada bir sorgu çalıştırır. Bu tür bir kayıt bulunursa, uyarıyı tetikler ve yapılandırılan eylemi tetikler.

### <a name="metric-measurement-alert-rules"></a>Ölçüm ölçümü uyarı kuralları

**Ölçüm ölçümü** uyarı kuralları bir sorgudaki her bir nesne için belirtilen eşiği ve belirtilen tetikleyici koşulunu aşan bir uyarı oluşturur. **Sonuç** uyarısı kurallarından farklı olarak, analiz sonucu bir zaman serisi sağlıyorsa, **ölçüm ölçümü** uyarı kuralları çalışır. Sonuç uyarı kurallarından **oluşan** aşağıdaki farklı farklılıklara sahiptir.

- **Toplama işlevi**: gerçekleştirilen hesaplamayı ve potansiyel olarak toplanacak sayısal alanı belirler.  Örneğin, **Count ()** sorgudaki kayıt sayısını döndürür, **AVG (CounterValue)** Aralık üzerinde CounterValue alanının ortalamasını döndürür. Sorgudaki toplama işlevinin adlandırılmış/çağrılan: Aggregilev değeri olması ve sayısal bir değer sağlaması gerekir. 

- **Grup alanı**: Bu alanın her örneği için toplanmış bir değere sahip bir kayıt oluşturulur ve her biri için bir uyarı oluşturulabilir.  Örneğin, her bilgisayar için bir uyarı oluşturmak isterseniz, **bilgisayar tarafından**kullanılır. Bu durumda, uyarı sorgusunda birden çok grup alanı belirtilirse, Kullanıcı **toplama on** (metriccolumn) parametresini kullanarak sonuçları sıralamak için kullanılacak alanı belirtebilir

    > [!NOTE]
    > *Toplama on* (metriccolumn) seçeneği, yalnızca [SCHEDULEDQUERYRULES apı kullanılarak yapılandırılan Log Analytics](../../azure-monitor/platform/alerts-log-api-switch.md) için Application Insights ve günlük uyarılarına yönelik ölçüm ölçüm türü için kullanılabilir.

- **Aralık**: verilerin toplanması için gereken zaman aralığını tanımlar.  Örneğin, **beş dakika**belirlediyseniz, uyarı için belirtilen süre boyunca 5 dakikalık aralıklarla toplanan grup alanı her örneği için bir kayıt oluşturulur.

    > [!NOTE]
    > Interval işlevini belirtmek için Query 'de bin işlevi kullanılmalıdır. As bin () eşit olmayan zaman aralıklarıyla sonuçlanabilir-uyarı, bin komutunu, çalışma zamanında uygun bir zaman ile bin_at komutuna otomatik olarak dönüştürür, böylece sabit bir nokta elde edin. Günlük uyarısının Ölçüm Ölçüm türü, üç bin () komutu örneğine sahip olan sorgularla çalışacak şekilde tasarlanmıştır
    
- **Eşik**: ölçüm ölçümü uyarı kuralları için eşik, bir toplam değer ve bir dizi ihlal tarafından tanımlanır.  Günlük aramasında herhangi bir veri noktası bu değeri aşarsa, bir ihlal olarak kabul edilir.  Sonuçlarda herhangi bir nesne için içindeki ihlal sayısı belirtilen değeri aşarsa, bu nesne için bir uyarı oluşturulur.

*Toplama* veya *metriccolumn* seçeneğinin yanlış yapılandırılması, uyarı kurallarının hatalı tetiklenmesine neden olabilir. Daha fazla bilgi için bkz. [ölçüm ölçümü uyarı kuralı yanlış olduğunda sorun giderme](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect).

#### <a name="example-of-metric-measurement-type-log-alert"></a>Ölçüm Ölçüm türü günlük uyarısı örneği

Herhangi bir bilgisayar 30 dakikada bir% 90 oranında işlemci kullanımını aşarsa, uyarı istediğiniz bir senaryoyu düşünün.  Aşağıdaki ayrıntılarla bir uyarı kuralı oluşturursunuz:  

- **Sorgu:** Perf | Burada ObjectName = = "Processor" ve CounterName = = "% Işlemci zamanı" | (TimeGenerated, 5 dk), bilgisayar için Aggregx değer = Ort (CounterValue)<br>
- **Zaman aralığı:** 30 dakika<br>
- **Uyarı sıklığı:** beş dakika<br>
- **Uyarı mantığı-koşul & eşiği:** 90 'den büyük<br>
- **Grup alanı (toplama):** Bilgisayar
- **Uyarıyı temel alarak tetikle:** 2 ' den büyük toplam ihlal sayısı<br>

Sorgu, her bilgisayar için 5 dakikalık aralıklarla ortalama bir değer oluşturur.  Bu sorgu, önceki 30 dakika boyunca toplanan veriler için 5 dakikada bir çalıştırılır. Seçilen grup alanı (toplama), sütunlu ' bilgisayar ' olduğundan, Aggregdeðerli değer ' Computer ' öğesinin çeşitli değerleri için bölünür ve her bilgisayar için Ortalama işlemci kullanımı 5 dakikalık bir zaman sepetine göre belirlenir.  Üç bilgisayar için örnek sorgu sonucu aşağıdaki gibi olacaktır.


|TimeGenerated [UTC] |Bilgisayar  |AggregatedValue  |
|---------|---------|---------|
|20XX-xx-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20XX-xx-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20XX-xx-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|...     |   ...      |    ...     |
|20XX-xx-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20XX-xx-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20XX-xx-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

Sorgu sonucu çizilmek ise, olarak görünür.

![Örnek sorgu sonuçları](media/alerts-unified-log/metrics-measurement-sample-graph.png)

Bu örnekte, üç bilgisayarın her biri için 5 dakikalık depo gözlerinde, 5 dakika boyunca hesaplanan ortalama işlemci kullanımı görüyoruz. 90, SNC01 tarafından yalnızca bir kez 1:25 bin olarak ihlal ediliyor. SRV02, 1:10, 1:15 ve 1:25 sepetlerindeki 90 eşiğini aşıyor; srv03, 1:10, 1:15, 1:20 ve 1:30 90 eşiğini aşmaktadır.
Uyarı toplam ihlal temelinde tetiklenecek şekilde yapılandırıldığından, SRV02 ve srv03 'in yalnızca ölçütü karşıladığını görüyoruz. Bu nedenle, birden çok zaman sepetlerine iki kez% 90 eşiğini ulaştıkları için, SRV02 ve srv03 için ayrı uyarılar oluşturulacaktır.  *Tetikleyici uyarısı:* parametresi, *sürekli ihlal* için yapılandırılmışsa, bu durumda **yalnızca** srv03 için bir uyarı tetiklenir ve bu durum 1:10 ' den 1:20 ' e kadar art arda üç zaman sepetinin eşiğine ulaştı. SRV02 için **değil** , 1:10 ile 1:15 arasındaki iki ardışık zaman sepetinin eşiğine ulaştığı için.

## <a name="log-search-alert-rule---firing-and-state"></a>Günlük arama uyarısı kuralı-tetikleme ve durum

Günlük arama uyarı kuralları yalnızca sorguya oluşturduğunuz mantığa göre çalışır. Uyarı sistemi, herhangi bir sistem durumu bağlamı, amacı veya sorgu tarafından kapsanan kök nedeni içermez. Bu nedenle, günlük uyarıları durum-Less olarak adlandırılır. Koşullar her çalıştırıldıklarında "TRUE" veya "FALSE" olarak değerlendirilir.  Uyarı koşulunun değerlendirmesi, daha önce tetiklenmeden bağımsız olarak "TRUE" olduğunda bir uyarı tetiklenir.    

Pratik bir örnekle bu davranışı eylemde görelim. [Sonuç türü günlük uyarısı için belirtilen örnekte](#example-of-number-of-records-type-log-alert)gösterildiği gibi, *contoso-log-Alert*adlı bir günlük uyarısı kuralına sahip olduğumuz olduğunu varsayalım. Bu koşul, günlüklerde 500 sonuç kodunu aramak için tasarlanan özel bir uyarı sorgusudur. Günlüklerde bir daha fazla 500 sonuç kodu bulunursa, uyarının koşulu true olur. 

Aşağıdaki her aralıkta, Azure uyarıları sistemi *contoso-log uyarısı*için koşulu değerlendirir.


| Zaman    | Günlük arama sorgusu tarafından döndürülen kayıt sayısı | Evalution günlük koşulu | Sonuç 
| ------- | ----------| ----------| ------- 
| 1:05 PM | 0 kayıt | 0 > 0 değil, yanlış |  Uyarı başlatılmıyor. Hiçbir eylem çağrılmadı.
| 1:10 PM | 2 kayıt | 2 > 0 so doğru  | Uyarı ateşlenir ve eylem grupları çağırılır. Uyarı durumu ETKIN.
| 1:15 PM | 5 kayıt | 5 > 0 so doğru  | Uyarı ateşlenir ve eylem grupları çağırılır. Uyarı durumu ETKIN.
| 1:20 PM | 0 kayıt | 0 > 0 değil, yanlış |  Uyarı başlatılmıyor. Hiçbir eylem çağrılmadı. Uyarı durumu ETKIN kaldı.

Önceki durumu örnek olarak kullanma:

1:15 ' de Azure uyarıları, 1:10 ' de görülen temeldeki sorunların kalıcı olup olmadığını ve kayıtlar net yeni hatalara mi yoksa 1:10PM 'de eski hatalardan mi yinelendiğini belirleyemez. Kullanıcı tarafından girilen sorgu daha önceki kayıtlara sahip olabilir veya olmayabilir ve sistem bunu bilmez. Azure uyarıları sistemi, dikkatli bir şekilde oluşturulmuştur ve uyarı ve ilişkili eylemleri 1:15 PM 'de yeniden harekete geçirilir. 

1:20 ' de, 500 sonuç koduyla sıfır kayıt görüldüğünde Azure uyarıları, 1:10 PM ve 1:15 PM ' de görülen 500 sonuç kodu nedenine zarar verebilir. Bu durum, 500 hata sorunlarının aynı nedenlerden sonra gerçekleşmediğini bilmez. Bu nedenle *contoso-log-Alert* , Azure uyarı panosu 'nda **Çözümlenmiş** olarak değişmez ve/veya uyarı çözümlendiğini belirten bildirimler gönderilmez. Yalnızca siz, analiz sorgusuna gömülü mantık için tam koşulu veya nedeni anlamış olursunuz, uyarıyı gerektiği gibi [kapalı olarak işaretleyebilir](alerts-managing-alert-states.md) .

## <a name="pricing-and-billing-of-log-alerts"></a>Günlük uyarılarını fiyatlandırma ve faturalama

Günlük uyarıları için geçerli olan fiyatlandırma, [Azure Izleyici fiyatlandırma](https://azure.microsoft.com/pricing/details/monitor/) sayfasında belirtilmiştir. Azure faturaları ' nda, günlük uyarıları `microsoft.insights/scheduledqueryrules` türü olarak temsil edilir:

- Kaynak grubu ve uyarı özellikleriyle birlikte tam uyarı adıyla gösterilen Application Insights uyarıları günlüğe kaydet
- Kaynak grubu ve uyarı özellikleriyle birlikte tam uyarı adıyla gösterilen Log Analytics Uyarıları günlüğe kaydedin; [Scheduledqueryrules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) kullanılarak oluşturulduğunda

[Eski Log Analytics API 'si](../../azure-monitor/platform/api-alerts.md) , Log Analytics kayıtlı aramanın parçası olarak uyarı eylemleri ve zamanlamaları vardır ve uygun [Azure kaynakları](../../azure-resource-manager/resource-group-overview.md)değildir. Bu nedenle, [yenı API 'ye geçiş](../../azure-monitor/platform/alerts-log-api-switch.md) **yapmadan** Log Analytics Azure portal kullanılarak veya [eski Log Analytics API 'si](../../azure-monitor/platform/api-alerts.md) Ile gizlenen sahte uyarı kuralları, Azure 'da faturalandırma için `microsoft.insights/scheduledqueryrules` ' te oluşturulur. @No__t-1 ' de faturalandırma için oluşturulan gizli sahte uyarı kuralları, kaynak grubu ve uyarı özellikleriyle birlikte `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` olarak gösterilir.

> [!NOTE]
> @No__t-0 gibi geçersiz karakterler varsa, bu değerler gizli sözde uyarı kuralı adında `_` ile ve bu nedenle de Azure faturasında değiştirilirler.

[Eski log ANALYTICS API](api-alerts.md)kullanarak uyarı kurallarının faturalandırılması için oluşturulan gizli scheduleQueryRules kaynaklarını kaldırmak için, kullanıcı aşağıdakilerden herhangi birini gerçekleştirebilir:

- Kullanıcı [Log Analytics çalışma alanındaki uyarı kuralları IÇIN API tercihini](../../azure-monitor/platform/alerts-log-api-switch.md) değiştirebilir ve uyarı kurallarının veya izlemenin Azure Resource Manager uyumlu [SCHEDULEDQUERYRULES API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)'sine taşınmasını önler. Böylece faturalandırma için sahte gizli uyarı kuralları yapma gereksinimini ortadan kaldırır.
- Ya da Kullanıcı API tercihi değiştirmek istemezseniz, kullanıcının [eski log ANALYTICS API](api-alerts.md) 'sini kullanarak özgün zamanlama ve uyarı eylemini **silmesi** veya [özgün günlük uyarı kuralında Azure Portal](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal) silmesi gerekir

Ayrıca, [eski log ANALYTICS API](api-alerts.md)kullanan uyarı kurallarının faturalandırılması için oluşturulan gizli scheduleQueryRules kaynakları IÇIN, put gibi herhangi bir değiştirme işlemi başarısız olur. @No__t-0 türü sözde kurallar, [eski log ANALYTICS API](api-alerts.md)kullanılarak oluşturulan uyarı kuralları için de kullanılır. [Eski log ANALYTICS API](api-alerts.md) kullanılarak herhangi bir uyarı kuralı değişikliği yapılmalıdır (veya) Kullanıcı, [Uyarı kuralları için API tercihini,](../../azure-monitor/platform/alerts-log-api-switch.md) bunun yerine [scheduledqueryrules API 'sini](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) kullanacak şekilde değiştirebilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure 'da günlük uyarılarında oluşturma](../../azure-monitor/platform/alerts-log.md)hakkında bilgi edinin.
* [Azure 'da günlük uyarılarında Web kancalarını](alerts-log-webhook.md)anlayın.
* [Azure uyarıları](../../azure-monitor/platform/alerts-overview.md)hakkında bilgi edinin.
* [Application Insights](../../azure-monitor/app/analytics.md)hakkında daha fazla bilgi edinin.
* [Log Analytics](../../azure-monitor/log-query/log-query-overview.md)hakkında daha fazla bilgi edinin.
