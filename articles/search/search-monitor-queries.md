---
title: Sorguları izleme
titleSuffix: Azure Cognitive Search
description: Performans ve verimlilik için sorgu ölçümlerini izleyin. Sorgu dizesi girişlerini kaynak günlüklerinde toplayın ve çözümleyin.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 169a90c12b30e0d083ce5c53ab7c6dd2495c4c23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100592387"
---
# <a name="monitor-query-requests-in-azure-cognitive-search"></a>Azure Bilişsel Arama 'de sorgu isteklerini izleme

Bu makalede, ölçüm ve kaynak günlüğü kullanarak sorgu performansının ve birimin nasıl ölçülmesi açıklanmaktadır. Ayrıca, arama corpclarınızın yardımcı programını ve verimliliğini değerlendirmenize gerek duyduğunuzda, sorgularda kullanılan giriş koşullarının nasıl toplandığını da açıklar.

Ölçümlerde akışların bulunduğu geçmiş verileri 30 gün boyunca korunur. Daha uzun bekletme için veya işletimsel verileri ve Sorgu dizelerini raporlamak için, günlüğe kaydedilen olayları ve ölçümleri kalıcı hale getiren bir depolama seçeneği belirten bir [Tanılama ayarını](search-monitor-logs.md) etkinleştirdiğinizden emin olun.

Veri ölçümünün bütünlüğünü en üst düzeye çıkaran koşullar şunlardır:

+ Faturalanabilir bir hizmet (temel veya Standart katmanda oluşturulan bir hizmet) kullanın. Ücretsiz hizmet birden çok abone tarafından paylaşılır ve bu, yükleme vardiyası olarak belirli bir miktar volame sağlar.

+ İçerilen ve yalıtılmış bir ortam oluşturmak için, mümkünse tek bir çoğaltma ve bölüm kullanın. Birden çok çoğaltma kullanıyorsanız, sorgu ölçümlerinin birden çok düğüm arasında ortalaması alınır ve bu da sonuçların duyarlılığını azaltır. Benzer şekilde, birden çok bölüm verilerin bölündüğü anlamına gelir, çünkü dizin oluşturma işlemi de devam edecekse bazı bölümlerin farklı verileri olabilir. Sorgu performansını ayarlamaya çalışırken, tek bir düğüm ve bölüm test için daha kararlı bir ortam sağlar.

> [!Tip]
> Ek istemci tarafı kodu ve Application Insights ile, uygulama kullanıcılarınızın ilgisini duyma hakkında daha derin Öngörüler için tıklama verilerini de yakalayabilirsiniz. Daha fazla bilgi için bkz. [arama trafiği analizi](search-traffic-analytics.md).

## <a name="query-volume-qps"></a>Sorgu birimi (QPS)

Birim, tek dakikalık bir pencerede yürütülen sorguların ortalama, sayı, minimum veya maksimum değerleri olarak rapor oluşturulabilecek yerleşik bir ölçüm olan, **saniyedeki arama sorgusu** (QPS) olarak ölçülür. Ölçümler için bir dakikalık aralıklar (Timegrek = "PT1M") sistem içinde düzeltilmiştir.

Sorguların milisaniye cinsinden yürütülmesi yaygındır, bu nedenle yalnızca saniyeler olarak ölçecek sorgular ölçümlerde görünür.

| Toplama Türü | Description |
|------------------|-------------|
| Ortalama | Sorgu yürütmenin gerçekleştiği bir dakika içinde ortalama saniye sayısı.|
| Count | Günlük için tek dakikalık aralıkta bulunan ölçüm sayısı. |
| Maksimum | Bir dakika içinde saniyede kayıtlı en yüksek arama sorgusu sayısı. |
| Minimum | Bir dakika içinde saniyede kaydedilen arama sorgularının en düşük sayısı.  |
| Sum | Dakika içinde yürütülen tüm sorguların toplamı.  |

Örneğin, bir dakika içinde şu şekilde bir düzene sahip olabilirsiniz: en yüksek bir ikinci yük olan SearchQueriesPerSecond için maksimum, ortalama yükün 58 saniye ve son olarak tek bir sorgu ile bir saniye, en az bir sorgu ile.

Başka bir örnek: bir düğüm 100 ölçümleri yayıyorsa, her ölçümün değeri 40, "Count 100" ise "Sum 4000", "Average" 40 ve "Max" 40.

## <a name="query-performance"></a>Sorgu performansı

Hizmet genelinde, sorgu performansı arama gecikmesi (bir sorgunun tamamlandığı süre) ve kaynak çekişmesinin sonucu olarak bırakılan kısıtlanmış sorgular olarak ölçülür.

### <a name="search-latency"></a>Arama gecikmesi

| Toplama Türü | Gecikme süresi | 
|------------------|---------|
| Ortalama | Milisaniye cinsinden ortalama sorgu süresi. | 
| Count | Günlük için tek dakikalık aralıkta bulunan ölçüm sayısı. |
| Maksimum | Örnekteki en uzun çalışan sorgu. | 
| Minimum | Örnekteki en kısa çalışan sorgu.  | 
| Toplam | Örnekteki tüm sorguların, Aralık içinde yürütülerek toplam yürütme süresi (bir dakika).  |

Aşağıdaki **arama gecikmesi** ölçümleri örneğini göz önünde bulundurun: 86 sorgu örneklenir ve ortalama süre 23,26 milisaniyedir. Minimum 0 değeri, bazı sorguların bırakılmakta olduğunu gösterir. En uzun çalışan sorgunun tamamlanması 1000 milisaniye sürdü. Toplam yürütme süresi 2 saniyedir.

![Gecikme süresi toplamaları](./media/search-monitor-usage/metrics-latency.png "Gecikme süresi toplamaları")

### <a name="throttled-queries"></a>Kısıtlanmış sorgular

Kısıtlanmış sorgular, işlem yerine bırakılan sorgulara başvurur. Çoğu durumda, azaltma hizmeti çalıştırmanın normal bir parçasıdır.  Yanlış bir sorun olduğunu belirten bir işaret değildir.

Şu anda işlenen istek sayısı kullanılabilir kaynakları aşarsa kısıtlama oluşur. Bir çoğaltma, döndürme sırasında veya dizin oluşturma sırasında getirildiğinde, kısıtlanmış isteklerde artış görebilirsiniz. Hem sorgu hem de dizin oluşturma istekleri aynı kaynak kümesi tarafından işlenir.

Hizmet, isteklerin kaynak tüketimine göre atılıp düşürümeyeceğini belirler. Bellek, CPU ve disk GÇ genelinde tüketilen kaynakların yüzdesi bir süre içinde ortalaması alınır. Bu yüzde bir eşiği aşarsa, istek hacmi azaltılana kadar dizine yapılan tüm istekler azaltılır. 

İstemcinizi bağlı olarak, kısıtlanmış bir istek şu yollarla belirtilebilir:

+ Bir hizmet, "çok fazla istek gönderiyorsunuz" hatası döndürüyor. Lütfen daha sonra tekrar deneyin." 
+ Hizmet, hizmetin Şu anda kullanılamadığını belirten bir 503 hata kodu döndürür. 
+ Portalı kullanıyorsanız (örneğin, arama Gezgini), sorgu sessizce bırakılır ve yeniden ara ' yı tıklamanız gerekir.

Kısıtlanmış sorguları onaylamak için, **Kısıtlanmış arama sorguları** ölçümünü kullanın. Portalda ölçümleri keşfedebilirsiniz veya bu makalede açıklandığı gibi bir uyarı ölçümü oluşturabilirsiniz. Örnekleme aralığı içinde bırakılan sorgular için, Yürütülmeyen sorguların yüzdesini almak için *Toplam* ' u kullanın.

| Toplama Türü | Azaltma |
|------------------|-----------|
| Ortalama | Aralık içinde bırakılan sorguların yüzdesi. |
| Count | Günlük için tek dakikalık aralıkta bulunan ölçüm sayısı. |
| Maksimum | Aralık içinde bırakılan sorguların yüzdesi.|
| Minimum | Aralık içinde bırakılan sorguların yüzdesi. |
| Toplam | Aralık içinde bırakılan sorguların yüzdesi. |

**Kısıtlanmış arama sorguları yüzdesi**, minimum, maksimum, ortalama ve toplam için aynı değer vardır: bir dakika boyunca toplam arama sorgusu sayısından kısıtlanmış olan arama sorgularının yüzdesi.

Aşağıdaki ekran görüntüsünde, ilk sayı sayı (veya günlüğe gönderilen ölçüm sayısı) sayısıdır. En üstte veya ölçüm üzerine gelindiğinde görüntülenen ek toplamalar, ortalama, en yüksek ve toplam içerir. Bu örnekte, hiçbir istek bırakılmadı.

![Kısıtlanmış toplamalar](./media/search-monitor-usage/metrics-throttle.png "Kısıtlanmış toplamalar")

## <a name="explore-metrics-in-the-portal"></a>Portalda ölçümleri keşfet

Geçerli sayılara hızlı bir şekilde bakmak için, hizmet Genel Bakış sayfasındaki **izleme** sekmesine üç ölçüm (**arama gecikme süresi**, **saniye başına arama sorguları (arama birimi başına)**, kısıtlı **arama sorguları yüzdesi**), toplama türünü değiştirme seçeneğiyle birlikte saat, gün ve hafta cinsinden ölçülen sabit aralıklar arasında gösterilir.

Daha derin araştırma için, **izleme** menüsünden Ölçüm Gezgini 'ni açın, böylece eğilimleri ve anormallikleri araştırmak üzere verileri katmanlaştırabilir, yakınlaştırabilir ve görselleştirebilirsiniz. [Ölçüm grafiği oluşturma hakkında bu öğreticiyi](../azure-monitor/essentials/tutorial-metrics-explorer.md)tamamlayarak Ölçüm Gezgini hakkında daha fazla bilgi edinin.

1. Izleme bölümünde **, ölçüm ' i seçerek ölçüm** Gezgini ' ni arama hizmetinize ayarlanmış kapsam ile açın.

1. Ölçüm altında, açılan listeden birini seçin ve tercih edilen bir tür için kullanılabilir toplamalar listesini gözden geçirin. Toplama, toplanan değerlerin her zaman aralığında nasıl örnekleneceğini tanımlar.

   ![QPS ölçümü için ölçüm Gezgini](./media/search-monitor-usage/metrics-explorer-qps.png "QPS ölçümü için ölçüm Gezgini")

1. Sağ üst köşede zaman aralığını ayarlayın.

1. Görselleştirme seçin. Varsayılan değer bir çizgi grafiğidir.

1. **Ölçüm Ekle** ve farklı toplamalar seçme seçeneklerini belirleyerek ek toplamaları katman.

1. Çizgi grafik üzerinde ilgilendiğiniz bir alana yakınlaştırın. Fare işaretçisini alanın başlangıcına koyun, sol fare düğmesine tıklayın ve basılı tutun, alanın diğer tarafına sürükleyin ve düğmeyi bırakın. Grafik bu zaman aralığında yakınlaşacaktır.

## <a name="identify-strings-used-in-queries"></a>Sorgularda kullanılan dizeleri tanımla

Kaynak günlüğünü etkinleştirdiğinizde, sistem sorgu isteklerini **AzureDiagnostics** tablosunda yakalar. Bir önkoşul olarak, bir Log Analytics çalışma alanı ya da başka bir depolama seçeneği belirterek [kaynak günlüğü](search-monitor-logs.md)'nün zaten etkinleştirilmiş olması gerekir.

1. Izleme bölümünde, Log Analytics ' de boş bir sorgu penceresi açmak için **Günlükler** ' i seçin.

1. Sorgu. Search işlemlerini aramak için aşağıdaki ifadeyi çalıştırın, işlem adından, sorgu dizesinden, sorgulanan dizinden ve bulunan belge sayısından oluşan tablolu bir sonuç kümesi döndürür. Son iki deyim, bir boş veya belirtilmemiş aramadan oluşan sorgu dizelerini, örnek bir dizin üzerinden hariç tutar ve bu, sonuçlarınızda paraziti keser.

   ```
   AzureDiagnostics
   | project OperationName, Query_s, IndexName_s, Documents_d
   | where OperationName == "Query.Search"
   | where Query_s != "?api-version=2020-06-30&search=*"
   | where IndexName_s != "realestate-us-sample-index"
   ```

1. İsteğe bağlı olarak, belirli bir söz dizimi veya dizeyi aramak için *Query_s* bir sütun filtresi ayarlayın. Örneğin *, üzerine filtre* uygulayabilir `?api-version=2020-06-30&search=*&%24filter=HotelName` ).

   ![Günlüğe kaydedilen sorgu dizeleri](./media/search-monitor-usage/log-query-strings.png "Günlüğe kaydedilen sorgu dizeleri")

Bu teknik geçici araştırma için çalışırken, bir rapor oluşturmak, Sorgu dizelerini bir düzen daha fazla şekilde analiz edilecek şekilde birleştirip sunmanıza olanak tanır.

## <a name="identify-long-running-queries"></a>Uzun süre çalışan sorguları tanımla

Yalnızca ölçüm olarak çekilen olanları değil, tüm sorguların numaralarını almak için Duration sütununu ekleyin. Bu verileri sıralamak, hangi sorguların ne kadar uzun sürme olduğunu gösterir.

1. Izleme bölümünde günlük bilgilerini sorgulamak için **Günlükler** ' i seçin.

1. Sorgu döndürmek için aşağıdaki sorguyu çalıştırın, milisaniye cinsinden süreye göre sıralanır. En uzun çalışan sorgular en üstte bulunur.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search"
   | sort by DurationMs
   ```

   ![Sorguları süreye göre sırala](./media/search-monitor-usage/azurediagnostics-table-sortby-duration.png "Sorguları süreye göre sırala")

## <a name="create-a-metric-alert"></a>Ölçüm uyarısı oluşturma

Ölçüm uyarısı, bir bildirim alacağınız veya önceden tanımladığınız bir düzeltici eylemi tetikleyen bir eşik oluşturur. 

Arama hizmeti için arama gecikmesi ve kısıtlanmış sorgular için bir ölçüm uyarısı oluşturulması yaygındır. Sorguların ne zaman bırakılacağını biliyorsanız, yükü azaltan veya kapasiteyi artıran düzeltmelere bakabilirsiniz. Örneğin, dizin oluşturma sırasında kısıtlanmış sorgular artlarsa, sorgu etkinlik alt taraflarına kadar erteleyebilirsiniz.

Belirli bir çoğaltma bölümü yapılandırmasının sınırlarını gönderirken, sorgu birimi eşikleri (QPS) için uyarıları ayarlamak da faydalı olur.

1. Izleme bölümünde **Uyarılar** ' ı seçin ve ardından **+ Yeni uyarı kuralı**' nı tıklatın. Arama hizmetinizin kaynak olarak seçildiğinden emin olun.

1. Koşul bölümünde **Ekle**' ye tıklayın.

1. Sinyal mantığını yapılandırın. Sinyal türü için **ölçümler** ' i seçin ve ardından sinyali seçin.

1. Sinyali seçtikten sonra, koşulları ayarlamaya nasıl devam edeceğine dair bilinçli bir karar için geçmiş verileri görselleştirmek üzere bir grafik kullanabilirsiniz.

1. Ardından, uyarı mantığına aşağı kaydırın. Kavram kanıtı için, test amacıyla yapay düşük bir değer belirtebilirsiniz.

   ![Uyarı mantığı](./media/search-monitor-usage/alert-logic-qps.png "Uyarı mantığı")

1. Sonra, bir eylem grubu belirtin veya oluşturun. Bu, eşik karşılandığında çağrılacak yanıttır. Anında iletme bildirimi veya otomatik bir yanıt olabilir.

1. Son olarak, uyarı ayrıntılarını belirtin. Uyarıyı adlandırın, bir önem derecesi değeri atayın ve kuralın etkin veya devre dışı durumunda oluşturulup oluşturulmayacağını belirtin.

   ![Uyarı ayrıntıları](./media/search-monitor-usage/alert-details.png "Uyarı ayrıntıları")

Bir e-posta bildirimi belirttiyseniz, "Azure: Activated önem derecesi: 3" konu satırındaki "Microsoft Azure" adresinden bir e-posta alırsınız `<your rule name>` .

<!-- ## Report query data

Power BI is an analytical reporting tool useful for visualizing data, including log information. If you are collecting data in Blob storage, a Power BI template makes it easy to spot anomalies or trends. Use this link to download the template. -->

## <a name="next-steps"></a>Sonraki adımlar

Daha önce yapmadıysanız, daha fazla bakış özelliği hakkında bilgi edinmek için arama hizmeti izlemenin temellerini gözden geçirin.

> [!div class="nextstepaction"]
> [Azure Bilişsel Arama işlemler ve etkinlikleri izleme](search-monitor-usage.md)