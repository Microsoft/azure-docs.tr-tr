---
title: Azure ön kapı Standart/Premium (Önizleme) raporları
description: Bu makalede, raporlama 'nın Azure ön kapıda nasıl çalıştığı açıklanır.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 9670d8204d04fc770bf3fe98a270a3f6ccbf234b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101100594"
---
# <a name="azure-front-door-standardpremium-preview-reports"></a>Azure ön kapı Standart/Premium (Önizleme) raporları

> [!IMPORTANT]
> Azure ön kapı Standart/Premium (Önizleme) Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure ön kapısının Standart/Premium analiz raporları, Azure ön kapısının, ilişkili Web uygulaması güvenlik duvarı ölçümleriyle birlikte nasıl davranacağını gösteren yerleşik ve tam bir görünüm sağlar. Ayrıca, daha fazla sorun giderme ve hata ayıklama yapmak için erişim günlüklerinden yararlanabilirsiniz. Azure ön kapı analizi raporları, trafik raporları ve güvenlik raporları içerir.

| Raporlar | Ayrıntılar |
|---------|---------|
| Ana ölçümlere genel bakış | Azure ön kapı kenarlarından istemcilere gönderilen genel verileri gösterir<br/>Yoğun bant genişliği<br/>-İstekler <br/>-Önbellek isabet oranı<br/> -Toplam gecikme<br/>-5XX hata oranı |
| Etki alanına göre trafik | -Profil altındaki tüm etki alanlarına genel bakış sağlar<br/>-AFD kenarından istemciye aktarılan verilerin dökümünü alma<br/>-Toplam istek<br/>-3XX/4XX/5XX/, etki alanlarına göre yanıt kodu |
| Konuma göre trafik | -En popüler ülkelere göre istek ve kullanım için bir harita görünümü gösterir<br/>-En popüler ülkelerin eğilim görünümü |
| Kullanım | -Azure ön kapısı kenarından istemcilere giden veri aktarımını görüntüler<br/>-Kaynaktan AFD Edge 'e veri aktarımı<br/>-AFD kenarından istemcilere bant genişliği<br/>-Kaynaktan AFD Edge 'e bant genişliği<br/>-İstekler<br/>-Toplam gecikme<br/>-HTTP durum koduna göre istek sayısı eğilimi |
| Önbelleğe Alma | -İstek sayısına göre önbellek isabet oranını gösterir<br/>-İsabet ve isabetsizlik isteklerinin eğilimi görünümü |
| En iyi URL | -İstek sayısını gösterir <br/>-Aktarılan veri <br/>-Önbellek isabet oranı <br/>-İstenen 50 varlık için yanıt durum kodu dağıtımı. |
| En çok başvuran | -İstek sayısını gösterir <br/>-Aktarılan veri <br/>-Önbellek isabet oranı <br/>-Trafik üreten en üstteki 50 başvuranlar için yanıt durum kodu dağıtımı. |
| Üst Kullanıcı Aracısı | -İstek sayısını gösterir <br/>-Aktarılan veri <br/>-Önbellek isabet oranı <br/>-İçerik istemek için kullanılan ilk 50 Kullanıcı aracısının yanıt durum kodu dağıtımı. |

| Güvenlik raporları | Ayrıntılar |
|---------|---------|
| Ana ölçümlere genel bakış | -Eşleşen WAF kurallarını gösterir<br/>-Eşleşen OWASP kuralları<br/>-Eşleşen BOT kuralları<br/>-Eşleşen özel kurallar |
| Boyutlara göre ölçümler | -Eyleme göre eşleşen WAF kuralları eğilimi dökümü<br/>-Kural grubuna göre kural kümesi türüne ve olayına göre olayların halka grafiği<br/>-Kural KIMLIĞI, ülke, IP adresi, URL ve kullanıcı aracısına göre en iyi olay listesini Böl  |

> [!NOTE]
> Güvenlik raporları yalnızca Azure ön kapı Premium SKU 'SU ile kullanılabilir.

Raporların çoğu, erişim günlüklerine dayalıdır ve Azure ön kapısıyla müşterilere ücretsiz olarak sunulur. Müşterinin, bu raporları görüntülemek için erişim günlüklerini etkinleştirmesi veya herhangi bir yapılandırma yapması gerekmez. Raporlara Portal ve API aracılığıyla erişilebilir. CSV indirmesi de desteklenir. 

Raporlar, önceki 90 gün içindeki seçili tarih aralığını destekler. Her 5 dakikada bir, her saat veya seçili tarih aralığı temelinde her gün veri noktalarıyla. Normal olarak, bir saat içinde ve zaman içinde birkaç saate kadar Gecikmeli olan verileri görüntüleyebilirsiniz. 

## <a name="access-reports-using-the-azure-portal"></a>Azure portal kullanarak raporlara erişin

1. [Azure Portal](https://portal.azure.com) oturum açın ve Azure ön kapısının Standart/Premium profilinizi seçin.

1. Gezinti bölmesinde, *analiz* altındaki **raporlar veya güvenlik** ' i seçin.

   :::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="Rapor giriş sayfasının ekran görüntüsü":::

1. Farklı boyutlarda yedi sekme bulunur, ilgilendiğiniz boyutu seçin.

   * Etki alanına göre trafik
   * Kullanım 
   * Konuma göre trafik
   * Önbellek
   * En iyi URL
   * En çok başvuran
   * Üst Kullanıcı Aracısı

1. Boyutu seçtikten sonra farklı filtreler seçebilirsiniz.
  
    1. **Verileri göster** -etki alanına göre trafiği görüntülemek istediğiniz tarih aralığını seçin. Kullanılabilir aralıklar şunlardır:
        
        * Son 24 saat
        * Son 7 gün
        * Son 30 gün
        * Son 90 gün
        * Bu ay
        * Geçen ay
        * Özel Tarih

         Veriler, son yedi gün için varsayılan olarak gösterilir. Çizgi grafikleri olan sekmeler için, veri ayrıntı düzeyi varsayılan davranış olarak seçtiğiniz tarih aralıklarıyla gelir. 
    
        * 5 dakika-tarih aralıkları için her 5 dakikada bir bir veri noktası 24 saatten az veya eşit.
        * Saat: 24 saat ile 30 gün arasında bir tarih aralığı için saat başı bir veri
        * Güne göre: 30 günden daha büyük tarih aralıkları için gün başına bir veri.

        Toplamayı her zaman varsayılan toplama ayrıntı düzeyini değiştirmek için kullanabilirsiniz. Note: 5 dakika, 14 günden daha uzun veri aralığı için çalışmaz. 

    1. **Konum** -ülkeye göre tek veya birden çok istemci konumu seçin. Ülkeler altı bölgede gruplandırılır: Kuzey Amerika, Asya, Avrupa, Afrika, Okyanusya ve Güney Amerika. [Bölge/ülke eşlemeyi](https://en.wikipedia.org/wiki/Subregion)inceleyin. Varsayılan olarak, tüm ülkeler seçilidir.
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-locations.png" alt-text="Konum boyutu raporlarının ekran görüntüsü.":::
   
    1. **Protokol** -trafik verilerini görüntülemek için http ya da https seçin.
 
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-protocol.png" alt-text="Protokol boyutu raporlarının ekran görüntüsü.":::
    
    1. **Etki alanları** -tek veya çok uç noktaları veya özel etki alanlarını seçin. Tüm uç noktalar ve özel etki alanları varsayılan olarak seçilidir. 
    
        * Bir uç noktayı veya özel etki alanını bir profilde siler ve ardından aynı uç noktayı veya etki alanını başka bir profilde yeniden oluşturabilirsiniz. Uç nokta ikinci bir uç nokta olarak kabul edilir.  
        * Raporları özel etki alanına göre görüntülüyorsanız, bir özel etki alanını sildiğinizde ve bunu farklı bir uç noktaya bağladığınızda. Tek bir özel etki alanı olarak değerlendirilir. Uç noktaya göre görüntüle-bunlar ayrı öğeler olarak değerlendirilir. 
    
        :::image type="content" source="../media/how-to-reports/front-door-reports-dimension-domain.png" alt-text="Etki alanı boyutu için raporların ekran görüntüsü.":::

1. Verileri bir CSV dosyasına aktarmak istiyorsanız Seçili sekmede *CSV 'Yi indir* bağlantısını seçin.

    :::image type="content" source="../media/how-to-reports/front-door-reports-download-csv.png" alt-text="Raporlar için CSV dosyası indirme ekran görüntüsü.":::

### <a name="key-metrics-for-all-reports"></a>Tüm raporlar için anahtar ölçümleri

| Metric | Açıklama |
|---------|---------|
| Aktarılan veriler | Seçilen zaman çerçevesi, istemci konumları, etki alanları ve protokoller için AFD Edge pop 'Larından istemciye aktarılan verileri gösterir. |
| En yüksek bant genişliği | Seçilen zaman çerçevesi, istemci konumları, etki alanları ve protokoller için Azure ön kapısının pop 'Larından istemciye yönelik bit/sn cinsinden en yüksek bant genişliği kullanımı. | 
| Toplam İstek Sayısı | Bu zaman dilimi, istemci konumları, etki alanları ve protokoller için, AFD Edge pop 'Ların istemciye yanıt verdiği istek sayısı. |
| Önbellek İsabet Oranı | AFD 'ın seçili zaman çerçevesi, istemci konumları, etki alanları ve protokoller için kenar Önbelleklerinden içerik sunduğunu belirten tüm önbelleklenebilir isteklerin yüzdesi. |
| 5XX hata oranı | HTTP durum kodunun istemciye ait olduğu isteklerin yüzdesi, seçilen zaman çerçevesi, istemci konumları, etki alanları ve protokoller için 5XX idi. |
| Toplam gecikme süresi | Seçilen zaman çerçevesi, istemci konumları, etki alanları ve protokoller için tüm isteklerin ortalama gecikme süresi. Her istek için gecikme süresi, Azure ön kapasitesinden istemciye gönderilen Son Yanıt baytına kadar, istemci isteğinin Azure ön kapısının aldığı sürenin toplam süresi olarak ölçülür. |

## <a name="traffic-by-domain"></a>Etki alanına göre trafik

Etki alanına göre trafik, bu Azure ön kapı profili altındaki tüm etki alanlarının kılavuz görünümünü sağlar. Bu raporda şunları görebilirsiniz: 
* İstekler
* Azure ön kapasitesinden istemciye aktarılan veriler
* Her etki alanının durum kodu (3XX, 4Xx ve 5XX) ile istekler

Etki alanları, rapor oturumunda açıklandığı gibi uç nokta ve özel etki alanlarını içerir.  

Daha fazla bilgi edinmek için diğer sekmelere gidebilir veya beklentilerinizin altındaki ölçümleri bulmanız durumunda daha fazla bilgi edinebilirsiniz. 

:::image type="content" source="../media/how-to-reports/front-door-reports-landing-page.png" alt-text="Raporlar için giriş sayfasının ekran görüntüsü":::


## <a name="usage"></a>Kullanım

Bu rapor trafik ve yanıt durum kodu eğilimlerini farklı boyutlara göre gösterir; örneğin:

* Kenardan istemciye kadar ve çizgi grafiğindeki uçtan uca aktarılan veriler. 

* Uçtan istemciye, çizgi grafiğinde protokol aracılığıyla aktarılan veriler. 

* Çizgi grafikte uçtan istemciye kadar olan istek sayısı.  

* Çizgi grafikte protokol, HTTP ve HTTPS 'ye göre uçtan istemciye kadar olan istek sayısı. 

* Çizgi grafikte uçtan istemciye kadar bant genişliği. 

* Ön kapıyı istemciye en son yanıt baytı gönderilene kadar, ön kapıdan alınan istemci isteğinden toplam süreyi ölçen toplam gecikme süresi.

* Satır grafiğinde HTTP durum koduna göre uçtan istemciye olan istek sayısı. Her istek bir HTTP durum kodu üretir. HTTP durum kodu, ham günlükte HTTPStatusCode içinde görüntülenir. Durum kodu, CDN ucunun isteği nasıl işlendiğini açıklar. Örneğin, 2xx durum kodu, isteğin bir istemciye başarıyla sunulduğunu gösterir. Bir 4xx durum kodu bir hata oluştuğunu gösterir. HTTP durum kodları hakkında daha fazla bilgi için bkz. HTTP durum kodları listesi. 

* HTTP durum koduna göre uçtan istemciye kadar olan istek sayısı. Kılavuzdaki tüm istekler arasında HTTP durum koduna göre isteklerin yüzdesi. 

:::image type="content" source="../media/how-to-reports/front-door-reports-usage.png" alt-text="Kullanıma göre raporların ekran görüntüsü" lightbox="../media/how-to-reports/front-door-reports-usage-expanded.png":::

## <a name="traffic-by-location"></a>Konuma göre trafik

Bu rapor, en çok varlığınıza erişen ziyaretçilerin ülkesine göre ilk 50 konumu görüntüler. Rapor ayrıca, ülkeye göre ölçümlerin dökümünü sağlar ve size en fazla trafiğin oluşturulduğu ülkelerin genel bir görünümünü verir. Son olarak, hangi ülkenin daha yüksek önbellek okuması oranına veya 4XX/5XX hata koduna sahip olduğunu görebilirsiniz.

:::image type="content" source="../media/how-to-reports/front-door-reports-by-location.png" alt-text="Konumlara göre raporların ekran görüntüsü" lightbox="../media/how-to-reports/front-door-reports-by-location-expanded.png":::

Raporlara şunlar dahildir:

* İlk 50 ülkenin, veri aktaran veya tercih ettiğiniz isteklerin bir dünya haritası görünümü.
* Dışarı aktarılan veriler ve tercih ettiğiniz istekler tarafından en fazla beş ülkede bulunan iki satırlık grafik eğilimi görünümü. 
* AFD 'den istemcilere aktarılan, tüm ülkelerin, isteklerin%, tüm ülkeler, önbellek isabet oranı, 4XX yanıt kodu ve 5XX yanıt kodu arasında aktarılan verileri içeren üst ülkelerin bir Kılavuzu.

## <a name="caching"></a>Önbelleğe Alma

Önbelleğe alma raporları, isteklere göre önbellek isabetlerinin/isabetsizlik ve önbellek isabet oranının grafik görünümünü sağlar. Bu anahtar ölçümleri, önbellek isabetlerinin en hızlı performans sonuçlarıyla CDN 'nin içerikleri önbelleğe alma işlemini açıklar. Önbellek isabetsizliği 'ni en aza indirerek veri teslim hızlarını iyileştirebilirsiniz. Bu rapor şunları içerir:

* Satır grafiğinde isabetli önbellek okuması ve isabetsizlik sayısı eğilimi.

* Çizgi grafiğinde isabetli önbellek okuması oranı.

Önbellek ısabetleri/Isabetsiz okuma sayısı, istemci istekleri için istek numarası önbellek isabetlerinin ve önbellek isabetlerinin sayısını anlatmaktadır

* İsabet sayısı: doğrudan Azure CDN Edge sunucularından sunulan istemci istekleri. Ham günlüklerde olan CacheStatus değerleri için Isabet, PARTIAL_HIT veya uzaktan Isabet olan isteklere başvurur. 

* İsabetsizlik: Azure CDN Edge sunucuları tarafından sunulan istemci istekleri kaynaktan içerik getiriliyor. Ham günlüklerde CacheStatus alanı için değerleri KAÇıRıLMASı gereken isteklere başvurur. 

**Önbellek isabet oranı** , uçtan doğrudan sunulan önbelleğe alınmış isteklerin yüzdesini açıklar. Önbellek isabet oranı formülü: `(PARTIAL_HIT +REMOTE_HIT+HIT/ (HIT + MISS + PARTIAL_HIT + REMOTE_HIT)*100%` . 

Bu rapor, önbelleğe alma senaryolarını dikkate alır ve aşağıdaki gereksinimleri karşılayan istekler hesaplamaya alınır. 

* İstenen içerik, istek sahibi veya kaynak Shield 'a en yakın POP üzerinde önbelleğe alındı. 

* Nesne parçalama için kısmi önbelleğe alınmış içerik.

Aşağıdaki durumların tümünü dışlar: 

* Kural kümesi nedeniyle reddedilen istekler.

* Devre dışı önbellek olarak ayarlanmış eşleşen kural kümesi içeren istekler. 

* WAF tarafından Engellenen istekler. 

* Kaynak yanıt üst bilgileri, önbelleğe alınmaması gerektiğini gösterir. Örneğin, Cache-Control: Private, Cache-Control: No-Cache veya pragma: No-Cache üstbilgileri bir varlığın önbelleğe alınmasını engeller. 

:::image type="content" source="../media/how-to-reports/front-door-reports-caching.png" alt-text="Önbelleğe alma raporlarının ekran görüntüsü.":::

## <a name="top-urls"></a>En iyi URL 'Ler

En iyi URL 'Ler belirli bir uç nokta veya özel etki alanı üzerinde gerçekleşen trafik miktarını görüntülemenize izin verir. Son 90 gün içinde herhangi bir dönemde, en fazla istenen 50 varlık için verileri görürsünüz. Popüler URL 'Ler aşağıdaki değerlerle görüntülenir. Kullanıcı, URL 'Leri istek sayısına, istek%, aktarılan verileri ve aktarılan verileri göre sıralayabilir. Tüm ölçümler saat olarak toplanır ve seçilen zaman dilimine göre farklılık gösterebilir. URL, erişim günlüğündeki RequestUri değerini ifade eder. 

:::image type="content" source="../media/how-to-reports/front-door-reports-top-url.png" alt-text="İlk URL için raporların ekran görüntüsü.":::

* URL, istenen varlığın tam yolunu biçiminde gösterir `http(s)://contoso.com/index.html/images/example.jpg` . 
* İstek sayısı. 
* Azure ön kapısının hizmet verdiği toplam istek yüzdesi. 
* Aktarılan veriler. 
* Aktarılan veri yüzdesi. 
* İsabetli önbellek okuması oranı yüzdesi
* Yanıt kodu 4XX olarak istekler
* 5 xx olarak yanıt koduna sahip istekler

> [!NOTE]
> En üstteki URL 'Ler zaman içinde değişebilir ve ilk 50 URL 'Lerin doğru bir listesini almak için Azure ön kapısının tüm URL isteklerinizi saate göre sayar ve toplam süre boyunca çalışan toplamı bir gün boyunca tutar. 500 URL 'Lerinin alt kısmındaki URL 'Ler gün boyunca listeyi açabilir veya kapatabilir, bu nedenle bu URL 'lerin toplam sayısı yaklaşık olarak gösterilir.  
>
> En üstteki 50 URL 'Ler listeyi arttığında ve listede bulunabilir, ancak en sık kullanılan URL 'Lerin numaraları genellikle güvenilirdir. Bir URL, listeyi devre dışı bırakır ve bir gün boyunca bir kez daha arttığında, bu süre içinde yer alan URL 'nin istek numarası temel alınarak, listede eksik olduğunda istek sayısı tahmini olur.  
>
> Aynı mantık, üst Kullanıcı Aracısı için de geçerlidir. 

## <a name="top-referrers"></a>En çok başvuran

En popüler başvuranlar, müşterilerin belirli bir uç nokta veya özel etki alanındaki içeriğe en çok istekte bulunan en iyi 50 başvuran ' i görüntülemesine olanak tanır. Son 90 gün içinde herhangi bir döneme ait verileri görüntüleyebilirsiniz. Başvuran, bir isteğin oluşturulduğu URL 'YI gösterir. Başvuran, bir arama altyapısından veya diğer Web sitelerinden gelebilir. Bir Kullanıcı bir URL 'YI (örneğin, http (s):/ncontoso,com/index.html) doğrudan bir tarayıcının adres satırına yazdığında, istenen başvuran "boş" olur. En çok başvuran raporu, takip değerlerini içerir. İstek sayısına göre sıralama yapabilirsiniz, istek yüzdesi, aktarılan veri ve aktarılan veri yüzdesi. Tüm ölçümler saat olarak toplanır ve seçilen zaman dilimine göre farklılık gösterebilir. 

* Başvuran, ham günlüklerde başvuran değeri 
* İstek sayısı 
* Seçilen dönemde Azure CDN tarafından sunulan toplam istek yüzdesi. 
* Aktarılan veriler 
* Aktarılan veri yüzdesi 
* İsabetli önbellek okuması oranı yüzdesi
* Yanıt kodu 4XX olarak istekler
* 5 xx olarak yanıt koduna sahip istekler

:::image type="content" source="../media/how-to-reports/front-door-reports-top-referrer.png" alt-text="En üst başvuran raporların ekran görüntüsü.":::

## <a name="top-user-agent"></a>Üst Kullanıcı Aracısı

Bu rapor, içerik istemek için kullanılan ilk 50 Kullanıcı aracısının grafik ve istatistik görünümüne sahip etmenize olanak tanır. Örneğin,
* Mozilla/5.0 (Windows NT 10,0; WOW64 
* AppleWebKit/537.36 (Gecko gibi KHTML) 
* Chrome/86.0.4240.75 
* Safari/537.36.  

Bir ızgara istek sayısını, isteği%, aktarılan verileri, aktarılan verileri, yanıt kodu 4XX ve yanıt kodu olarak 5 xx olan istekleri görüntüler. Kullanıcı Aracısı, erişim günlüklerindeki UserAgent değerini belirtir.

## <a name="security-report"></a>Güvenlik raporu

Bu rapor, farklı boyutlara göre WAF desenlerinin grafik ve istatistik görünümüne sahip etmenize olanak tanır.

| Boyutlar | Description |
|---------|---------|
| Genel Bakış ölçümleri-eşleşen WAF kuralları | Özel WAF kuralları, yönetilen WAF kuralları ve bot Manager ile eşleşen istekler. |
| Genel Bakış ölçümleri-engellenen Istekler | WAF kurallarıyla eşleşen tüm istekler arasında WAF kuralları tarafından engellenen isteklerin yüzdesi. |
| Genel Bakış ölçümleri-eşleşen yönetilen kurallar | Blok, günlük, Izin verme ve yeniden yönlendirme olan istekler için dört satırlık grafik eğilimi. |
| Genel Bakış ölçümleri-eşleşen özel kural | Özel WAF kurallarıyla eşleşen istekler. |
| Genel Bakış ölçümleri-eşleşen bot kuralı | Bot Manager ile eşleşen istekler. |
| Eyleme göre WAF istek eğilimi | Blok, günlük, Izin verme ve yeniden yönlendirme olan istekler için dört satırlık grafik eğilimi. |
| Kural türüne göre olaylar | WAF tarafından kural türüne göre dağıtım isteklerinin halka grafiği, ör. bot, özel kurallar ve yönetilen kurallar. |
| Kural grubuna göre olaylar | Kural grubuna göre WAF istekleri dağıtımının halka grafiği. |
| Eylemlere göre istekler | Eylemlere göre azalan sırada bir istek tablosu. |
| Üst kural kimliklerine göre istekler | İlk 50 kural kimliği tarafından azalan sırada bir istek tablosu. |
| Popüler ülkelere göre istekler |  İlk 50 ülkeye göre azalan sırada bir istek tablosu. |
| Üst istemci IP 'lerine göre istekler |  En önde 50 IP 'ye göre azalan sırada bir istek tablosu. |
| Üst Istek URL 'sine göre istekler |  En önde gelen 50 URL 'Lerine göre azalan sırada bir istek tablosu. |
| En üstteki ana bilgisayar adlarıyla istek | En üstteki 50 ana bilgisayar adına göre azalan sırada bir istek tablosu. |
| En üst kullanıcı aracılarına göre istekler | İlk 50 Kullanıcı aracılarından oluşan, azalan sırada bir istek tablosu. |

## <a name="cvs-format"></a>CVS biçimi

Farklı sekmeler için CSV dosyalarını, raporlarda indirebilirsiniz. Bu bölümde, her CSV dosyasındaki değerler açıklanmaktadır.

### <a name="general-information-about-the-cvs-report"></a>CVS raporu hakkında genel bilgiler

Her CSV raporu bazı genel bilgiler içerir ve bilgiler tüm CSV dosyalarında kullanılabilir. ' i yüklediğiniz rapora göre. 


| Değer | Açıklama |
|---------|---------|
| Rapor | Raporun adı. | 
| Etki Alanları | Raporun bitiş noktaları veya özel etki alanları listesi. |
| StartDateUTC | Raporu oluşturduğunuz tarih aralığının başlangıç tarihi Eşgüdümlü Evrensel Saat (UTC) |
| EndDateUTC | Raporu oluşturduğunuz tarih aralığının sonu, Eşgüdümlü Evrensel Saat (UTC) |
| GeneratedTimeUTC | Raporun oluşturulduğu tarih ve saat, Eşgüdümlü Evrensel Saat (UTC) |
| Konum | İstemci isteklerinin kaynaklandığı ülkelerin listesi. Değer varsayılan olarak tümü olur. Güvenlik raporuna uygulanamaz. |
| Protokol | İstek, HTTP veya HTTPs protokolü. Raporlar ve güvenlik raporundaki kullanıcı aracısına göre En Iyi URL ve trafiğe uygulanamaz. |
| Toplama | Her satırda veri toplama ayrıntı düzeyi, her 5 dakikada bir, her saat ve her gün. Raporlar ve güvenlik raporundaki kullanıcı aracısına göre etki alanına, en üst URL 'ye ve trafiğe göre trafiğe uygulanmaz. |

### <a name="data-in-traffic-by-domain"></a>Etki alanına göre trafikte bulunan veriler

* Etki alanı 
* Toplam Istek 
* Önbellek İsabet Oranı 
* 3XX Isteği 
* 4XX Istek 
* 5XX Istek 
* ByteTransferredFromEdgeToClient 

### <a name="data-in-traffic-by-location"></a>Konuma göre trafikte bulunan veriler

* Konum
* TotalRequests
* İsteyen
* BytesTransferredFromEdgeToClient

### <a name="data-in-usage"></a>Kullanımdaki veriler

Bu CSV dosyasında üç rapor vardır. Biri HTTPS protokolü ve diğeri HTTP durum kodu için HTTP protokolü için. 

HTTP ve HTTPs raporları aynı veri kümesini paylaşır. 

* Saat 
* Protokol 
* Aktarılan veri (bayt) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest 

HTTP durum kodu için rapor. 

* Saat 
* Aktarılan veri (bayt) 
* TotalRequest 
* bpsFromEdgeToClient 
* 2XXRequest 
* 3XXRequest 
* 4XXRequest 
* 5XXRequest

### <a name="data-in-caching"></a>Önbelleğe alma sırasında veriler 

* Saat
* CacheHitRatio 
* HitRequests 
* Hatalı Istekler 

### <a name="data-in-top-url"></a>Üstteki URL 'deki veriler 

* URL 
* TotalRequests 
* İsteyen 
* Aktarılan veri (bayt) 
* Veri aktarıldı% 

### <a name="data-in-user-agent"></a>Kullanıcı aracısındaki veriler 

* Kullanıcı 
* TotalRequests 
* İsteyen 
* Aktarılan veri (bayt) 
* Veri aktarıldı% 

### <a name="security-report"></a>Güvenlik raporu 

Aynı alanlara sahip yedi tablo vardır.  

* BlockedRequests 
* AllowedRequests 
* LoggedRequests 
* Yeniden Yönlendiriletedrequests 
* OWASPRuleRequests 
* CustomRuleRequests 
* BotRequests 

Yedi tablo zaman, kural KIMLIĞI, ülke, IP adresi, URL, ana bilgisayar adı, Kullanıcı Aracısı içindir. 

## <a name="next-steps"></a>Sonraki adımlar

[Azure ön kapısının Standart/Premium gerçek zamanlı izleme ölçümleri](how-to-monitor-metrics.md)hakkında bilgi edinin.
