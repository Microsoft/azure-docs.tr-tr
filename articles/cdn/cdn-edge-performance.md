---
title: Azure CDN | kenar düğümü performansını çözümleme Microsoft Docs
description: Microsoft Azure CDN 'de kenar düğümü performansını analiz edin. Edge Performans Analizi, CDN için ayrıntılı bilgi trafiği ve bant genişliği kullanımı sağlar.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 8cc596a7-3e01-4f76-af7b-a05a1421517e
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: d8119c56c26ed32db136064d93775d21a71f238f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92778661"
---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Microsoft Azure CDN’de kenar düğümü performansını çözümleme
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Genel Bakış
Edge Performans Analizi, CDN için ayrıntılı bilgi trafiği ve bant genişliği kullanımı sağlar. Bu bilgiler daha sonra, Varlıklarınızın önbelleğe alınma ve istemcilerinize teslim etme hakkında fikir edinmenizi sağlayan eğilim istatistikleri oluşturmak için kullanılabilir. Bu sayede, içeriğinizin teslimini iyileştirmek ve CDN 'den daha iyi yararlanmak için hangi sorunların hangi sorunları ele almak gerektiğini belirlemek için bir strateji oluşturmanıza olanak sağlar. Sonuç olarak, yalnızca veri teslimi performansını iyileştirebileceksiniz, ancak CDN maliyetlerinizi de azaltabilirsiniz.

> [!NOTE]
> Tüm raporlar bir tarih/saat belirtirken UTC/GMT gösterimini kullanır.
> 
> 

## <a name="reports-and-log-collection"></a>Raporlar ve günlük koleksiyonu
CDN etkinlik verilerinin, üzerinde rapor oluşturulabilmesi için önce Edge performans analizi modülü tarafından toplanması gerekir. Bu koleksiyon işlemi günde bir kez gerçekleşir ve önceki gün boyunca gerçekleşen etkinliği ele alır. Bu, bir raporun istatistikleri, işlendiği sırada günün istatistikleri örneğini temsil eden ve geçerli gün için tüm veri kümesini bulundurmayan anlamına gelir. Bu raporların birincil işlevi, performansı değerlendirmek için kullanılır. Bu, fatura amaçları veya tam sayısal istatistikler için kullanılmamalıdır.

> [!NOTE]
> Edge Performans Analizi raporlarının oluşturulduğu ham veriler en az 90 gün boyunca kullanılabilir.
> 
> 

## <a name="dashboard"></a>Pano
Edge Performans Analizi panosu, bir grafik ve istatistikler aracılığıyla geçerli ve geçmiş CDN trafiğini izler. Hesabınız için CDN trafiğinin performansına ilişkin en son ve uzun süreli eğilimleri algılamak için bu panoyu kullanın.

Bu pano aşağıdakilerden oluşur:

* Anahtar ölçümlerinin ve eğilimler görselleştirmesine olanak sağlayan etkileşimli bir grafik.
* Önemli ölçümler ve eğilimler için uzun vadeli desenler sağlayan bir zaman çizelgesi.
* CDN ağımızın, genel performans, kullanım ve verimlilik ile ölçülen Site trafiğini nasıl geliştirdüğüne ilişkin temel ölçümler ve istatistiksel bilgiler.

### <a name="accessing-the-edge-performance-dashboard"></a>Uç performans panosuna erişme
1. CDN profili dikey penceresinde **Yönet** düğmesine tıklayın.
   
    ![CDN profili dikey penceresi Yönet düğmesi](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    CDN yönetim portalı açılır.
2. **Analiz** sekmesinin üzerine gelin ve ardından **kenar performansı Analizi** açılır öğesi üzerine gelin.  **Panoya** tıklayın.
   
    Kenar düğümü Analizi panosu görüntülenir.

### <a name="chart"></a>Grafik
Panoda, doğrudan altında görüntülenen zaman çizelgesinde seçilen zaman dilimi boyunca bir ölçümü izleyen bir grafik bulunur.  CDN etkinliğinin son iki yıla kadar grafik oluşturan bir zaman çizelgesi doğrudan grafiğin altında görüntülenir.

#### <a name="using-the-chart"></a>Grafiği kullanma
* Varsayılan olarak, son 30 güne ait önbellek verimlilik oranı alınacaktır.
* Bu grafik, günlük olarak harmanlanmış verilerden oluşturulur.
* Çizgi grafiğinde bir günün üzerine gelindiğinde, bu tarihteki bir tarih ve ölçüm değeri gösterilir.
* Hafta sonlarını grafiğe göre temsil eden açık gri dikey çubukların bir kaplamasını değiştirmek için hafta sonlarını Vurgula ' ya tıklayın. Bu tür bir kaplama, hafta sonları üzerinde trafik desenlerini belirlemek için faydalıdır.
* Önceki yılın etkinliğinin bir kaplamasını grafiğin üzerine aynı zaman diliminde değiştirmek için bir yıl önce görüntüle ' ye tıklayın. Bu tür karşılaştırma, uzun süreli CDN kullanım desenlerine yönelik öngörüler sağlar. Grafiğin sağ üst köşesi, her çizgi grafiğinin renk kodunu gösteren bir açıklama içerir.

#### <a name="updating-the-chart"></a>Grafik güncelleştiriliyor
* Zaman aralığı: aşağıdakilerden birini yapın:
  * Zaman çizelgesinde istenen bölgeyi seçin. Grafik, seçilen döneme karşılık gelen verilerle güncelleştirilir.
  * Tüm kullanılabilir geçmiş verileri en fazla iki yıla kadar göstermek için grafiğe çift tıklayın.
* Ölçüm: istenen ölçümün yanında görünen grafik simgesine tıklayın. Grafik ve zaman çizelgesi, karşılık gelen ölçüm için verilerle yenilenecektir.

### <a name="key-metrics-and-statistics"></a>Temel ölçümler ve istatistikler
#### <a name="efficiency-metrics"></a>Verimlilik ölçümleri
Bu ölçümlerin amacı, önbellek verimliliğinin iyileşip artamayacağını görsağlamaktır. Önbellek verimliliğiyle elde edilen başlıca avantajlar şunlardır:

* Kaynak sunucu üzerinde daha az yük şu şekilde olabilir:
  * Daha iyi Web sunucusu performansı.
  * Azaltılmış işletimsel maliyetler.
* Daha fazla istek CDN 'den doğrudan sunulacak olduğundan, geliştirilmiş veri teslimi hızlandırma.

| Alan | Açıklama |
| --- | --- |
| Önbellek verimliliği |Önbellekten sunulan aktarılan veri yüzdesini gösterir. Bu ölçüm, istenen içeriğin önbelleğe alınmış bir sürümü doğrudan CDN 'den (uç sunuculardan) istek veripçilerine (ör. Web tarayıcısı) sunulduğunu ölçer. |
| İsabet oranı |Önbellekten sunulan isteklerin yüzdesini gösterir. Bu ölçüm, istenen içeriğin önbelleğe alınmış bir sürümü doğrudan CDN 'den (uç sunuculardan) istekçilere (ör. Web tarayıcısı) sunulduğunda ölçer. |
| Uzak baytların yüzdesi-önbellek yapılandırması yok |Kaynak sunuculardan, önbellek özelliği (HTTP kuralları altyapısı) sonucu olarak önbelleğe alınmayacak CDN 'ye (uç sunucular) sunulan trafik yüzdesini gösterir. |
| Uzak bayt yüzdesi-süre sonu biten önbellek |Eski içerik yeniden doğrulamasının sonucu olarak, kaynak sunuculardan CDN 'ye (uç sunucular) sunulan trafik yüzdesini gösterir. |

#### <a name="usage-metrics"></a>Kullanım ölçümleri
Bu ölçümlerin amacı, aşağıdaki maliyet kesme ölçüleriyle ilgili Öngörüler sağlamaktır:

* CDN aracılığıyla işlem maliyetlerini en aza indirme.
* Önbellek verimliliği ve sıkıştırma aracılığıyla CDN harcamalarını azaltma.

> [!NOTE]
> Trafik birimi numaraları, oranlar ve yüzdeler hesaplamalarında kullanılan trafiği temsil eder ve yalnızca yüksek hacimli müşteriler için toplam trafiğin bir kısmını gösterebilir.
> 
> 

| Alan | Açıklama |
| --- | --- |
| Ortalama çıkış baytları |CDN 'den (uç sunuculardan) sunulan her istek için, istek sahibine (ör. Web tarayıcısı) aktarılan baytların ortalama sayısını belirtir. |
| Önbellek yapılandırması bayt oranı yok |CDN 'den (uç sunuculardan) sunulan trafik yüzdesini, önbelleği atlama özelliği nedeniyle önbelleğe alınmayacak olan istek sahibine (ör. Web tarayıcısına) gösterir. |
| Sıkıştırılmış bayt oranı |CDN 'den (Edge sunucuları), sıkıştırılmış bir biçimde sahiplerini (ör. Web Browser) ile gönderilen trafiğin yüzdesini gösterir. |
| Giden bayt |CDN 'den (uç sunuculardan) istek sahibine (ör. Web tarayıcısı) gönderilen veri miktarını bayt cinsinden belirtir. |
| Içindeki baytlar |Sahiplerini (ör. Web tarayıcısı) ile CDN (Edge sunucuları) arasında gönderilen veri miktarını bayt cinsinden belirtir. |
| Uzak bayt sayısı |CDN ve müşteri kaynak sunucularından CDN 'ye (uç sunucular) gönderilen veri miktarını bayt cinsinden belirtir. |

#### <a name="performance-metrics"></a>Performans Ölçümleri
Bu ölçümlerin amacı, trafiğiniz için genel CDN performansını izlemek içindir.

| Alan | Açıklama |
| --- | --- |
| Aktarım hızı |İçeriğin CDN 'den istek sahibine aktarıldığı ortalama oranı gösterir. |
| Süre |Bir varlığın bir istek sahibine (ör. Web tarayıcısı) teslim edilmesi için geçen milisaniye cinsinden ortalama süreyi belirtir. |
| Sıkıştırılan Istek hızı |CDN 'den (uç sunuculardan) istek sahibine (ör. Web tarayıcısı) sıkıştırılmış bir biçimde teslim edilen isabetlerin yüzdesini gösterir. |
| 4xx hata oranı |Bir 4xx durum kodu oluşturan isabetlerin yüzdesini gösterir. |
| 5xx hata oranı |5xx durum kodu oluşturan isabetlerin yüzdesini gösterir. |
| İsabetler |CDN içeriği için istek sayısını gösterir. |

#### <a name="secure-traffic-metrics"></a>Güvenli trafik ölçümleri
Bu ölçümlerin amacı, HTTPS trafiği için CDN performansını izlemek içindir.

| Alan | Açıklama |
| --- | --- |
| Güvenli önbellek verimliliği |Önbellekten sunulan HTTPS istekleri için aktarılan verilerin yüzdesini gösterir. Bu ölçüm, istenen içeriğin önbelleğe alınmış bir sürümü (ör. Web Browser) ile HTTPS üzerinden doğrudan istek üzerine (örn. Web tarayıcısı) sunulduğunu ölçer. |
| Güvenli aktarım hızı |İçeriğin CDN 'den (uç sunuculardan), https üzerinden sahiplerini (ör. Web sunucuları) olarak aktarıldığı ortalama oranı gösterir. |
| Ortalama güvenli süre |Bir varlığın bir istek sahibine (ör. Web tarayıcısına) HTTPS üzerinden teslim edilmesi için geçen milisaniye cinsinden ortalama süreyi belirtir. |
| Güvenli Isabet sayısı |CDN içeriği için HTTPS isteklerinin sayısını gösterir. |
| Güvenli baytlar çıkışı |CDN 'den (uç sunuculardan) istek sahibine (ör. Web tarayıcısı) gönderilen HTTPS trafik miktarını bayt cinsinden belirtir. |

## <a name="reports"></a>Raporlar
Bu modüldeki her rapor, farklı türlerde ölçümler için bant genişliği ve trafik kullanımıyla ilgili bir grafik ve istatistikler içerir (örneğin, HTTP durum kodları, önbellek durum kodları, istek URL 'SI vb.). Bu bilgiler, içeriğin istemcilerinize nasıl sunulduğunu daha ayrıntılı olarak sunmak ve veri teslimi performansını geliştirmek için CDN davranışını ince ayar yapmak üzere kullanılabilir.

### <a name="accessing-the-edge-performance-reports"></a>Uç performans raporlarına erişme
1. CDN profili dikey penceresinde **Yönet** düğmesine tıklayın.
   
    ![CDN profili dikey penceresi Yönet düğmesi](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    CDN yönetim portalı açılır.
2. **Analiz** sekmesinin üzerine gelin ve ardından **kenar performansı Analizi** açılır öğesi üzerine gelin.  **Http büyük nesne**' ye tıklayın.
   
    Edge düğümü analiz raporları ekranı görüntülenir.

| Rapor | Açıklama |
| --- | --- |
| Günlük Özet |Belirli bir süre boyunca günlük trafik eğilimlerini görüntülemenize izin verir. Bu grafikteki her çubuk belirli bir tarihi temsil eder. Çubuğun boyutu, bu tarihte gerçekleşen toplam isabet sayısını gösterir. |
| Saatlik Özet |Belirli bir süre boyunca saatlik trafik eğilimlerini görüntülemenize izin verir. Bu grafikteki her çubuk, belirli bir tarihte tek bir saati temsil eder. Çubuğun boyutu, bu saat içinde gerçekleşen toplam isabet sayısını gösterir. |
| Protokoller |HTTP ve HTTPS protokolleri arasındaki trafiğin dökümünü görüntüler. Halka grafik her protokol türü için gerçekleşen isabetlerin yüzdesini gösterir. |
| HTTP yöntemleri |Verilerinizi istemek için hangi HTTP yöntemlerinin kullanıldığını hızlı bir şekilde almanızı sağlar. Genellikle, en yaygın HTTP istek yöntemleri GET, HEAD ve POST ' lardır. Halka grafik her HTTP istek yöntemi türü için gerçekleşen isabetlerin yüzdesini gösterir. |
| URL’ler |En çok istenen 10 URL 'yi görüntüleyen bir grafik içerir. Her URL için bir çubuk görüntülenir. Çubuğun yüksekliği, belirli URL 'nin rapor kapsamındaki zaman dilimi üzerinden kaç isabetle oluşturulduğunu gösterir. En üstteki 100 istenen URL 'Lerin istatistikleri doğrudan bu grafiğin altında görüntülenir. |
| CNAME 'leri |Bir raporun zaman dilimi boyunca varlık istemek için kullanılan ilk 10 CNAMEs öğesini görüntüleyen bir grafik içerir. En üstteki 100 istenen CNAMEs istatistikleri doğrudan bu grafiğin altında görüntülenir. |
| Kadar |Belirli bir süre boyunca varlıkların istendiği ilk 10 CDN veya müşteri kaynak sunucusunu görüntüleyen bir grafik içerir. En üstteki 100 istenen CDN veya müşteri kaynak sunucuları için istatistikler doğrudan bu grafiğin altında görüntülenir. Müşteri kaynak sunucuları, dizin adı seçeneğinde tanımlanan adla tanımlanır. |
| Coğrafi pop 'Lar |Trafiğinizin belirli bir varlık noktası (POP) üzerinden ne kadarının yönlendirildiğini gösterir. Üç harfli kısaltma, CDN ağımızda bir POP 'U temsil eder. |
| İstemciler |Belirli bir süre içinde varlık isteyen ilk 10 istemciyi görüntüleyen bir grafik içerir. Bu raporun amaçları doğrultusunda, aynı IP adresinden kaynaklanan tüm istekler aynı istemciden oldukları kabul edilir. En üstteki 100 istemci istatistikleri doğrudan bu grafiğin altında görüntülenir. Bu rapor, en üst istemcileriniz için indirme etkinliği düzenlerini belirlemek için faydalıdır. |
| Önbellek durumları |Önbellek davranışının ayrıntılı bir dökümünü verir ve bu, genel Son Kullanıcı deneyimini geliştirmeye yönelik yaklaşımları açığa çıkabilir. En yüksek performans, önbellek isabetlerinin geldiği için, önbellek isabetsizliği ve zaman aşımına uğradı önbellek isabetlerinin en aza giderek veri teslim hızlarını iyileştirebilirsiniz. |
| HIÇBIRI ayrıntıları |Önbellek içeriği yeniliği belirli bir süre boyunca denetlenmedi olan varlıklar için en iyi 10 URL 'yi görüntüleyen bir grafik içerir. Bu tür varlıklar için en üstteki 100 URL 'Lerin istatistikleri doğrudan bu grafiğin altında görüntülenir. |
| CONFIG_NOCACHE ayrıntıları |Müşterinin CDN yapılandırması nedeniyle önbelleğe alınmamış varlıklar için en iyi 10 URL 'yi görüntüleyen bir grafik içerir. Bu tür varlıklar doğrudan kaynak sunucudan sunulur. Bu tür varlıklar için en üstteki 100 URL 'Lerin istatistikleri doğrudan bu grafiğin altında görüntülenir. |
| ÖNBELLEKLENEBILIR Ayrıntılar |İstek üst bilgisi verileri nedeniyle önbellekte olmayan varlıklar için en iyi 10 URL 'yi görüntüleyen bir grafik içerir. Bu tür varlıklar için en üstteki 100 URL 'Lerin istatistikleri doğrudan bu grafiğin altında görüntülenir. |
| TCP_HIT ayrıntıları |Önbellekten hemen sunulan varlıklar için en iyi 10 URL 'yi görüntüleyen bir grafik içerir. Bu tür varlıklar için en üstteki 100 URL 'Lerin istatistikleri doğrudan bu grafiğin altında görüntülenir. |
| TCP_MISS ayrıntıları |TCP_MISS önbellek durumuna sahip varlıklar için ilk 10 URL 'yi görüntüleyen bir grafik içerir. Bu tür varlıklar için en üstteki 100 URL 'Lerin istatistikleri doğrudan bu grafiğin altında görüntülenir. |
| TCP_EXPIRED_HIT ayrıntıları |Doğrudan POP 'tan sunulan eski varlıklar için en iyi 10 URL 'yi görüntüleyen bir grafik içerir. Bu tür varlıklar için en üstteki 100 URL 'Lerin istatistikleri doğrudan bu grafiğin altında görüntülenir. |
| TCP_EXPIRED_MISS ayrıntıları |Kaynak sunucudan yeni bir sürümün alınması gereken eski varlıklar için en iyi 10 URL 'yi görüntüleyen bir grafik içerir. Bu tür varlıklar için en üstteki 100 URL 'Lerin istatistikleri doğrudan bu grafiğin altında görüntülenir. |
| TCP_CLIENT_REFRESH_MISS ayrıntıları |İstemciden gelen önbellek isteği olmayan bir istek nedeniyle kaynak sunucudan alınan varlıkların ilk 10 URL 'sini görüntüleyen bir çubuk grafik içerir. Bu tür istekler için en üstteki 100 URL 'Lerin istatistikleri doğrudan bu grafiğin altında görüntülenir. |
| İstemci Istek türleri |HTTP istemcileri (ör. tarayıcılar) tarafından yapılan isteklerin türünü gösterir. Bu rapor, isteklerin nasıl işlendiği konusunda bir fikir sağlayan halka grafik içerir. Her istek türü için bant genişliği ve trafik bilgileri grafiğin altında görüntülenir. |
| Kullanıcı Aracısı |CDN 'imizde içeriğinizi istemek için ilk 10 Kullanıcı aracısının görüntülendiği bir çubuk grafik içerir. Genellikle, bir Kullanıcı Aracısı bir Web tarayıcısı, medya oynatıcı veya cep telefonu tarayıcısıdır. İlk 100 Kullanıcı aracısının istatistikleri doğrudan bu grafiğin altında görüntülenir. |
| Başvuranları |CDN 'umuzdan erişilen içeriğe en iyi 10 başvuranları görüntüleyen bir çubuk grafik içerir. Genellikle, başvuran Web sayfasının veya içeriğinizi bağlayan kaynağın URL 'sidir. En üstteki 100 başvuranlar için grafiğin altında ayrıntılı bilgiler verilmiştir. |
| Sıkıştırma türleri |İstenen varlıkları kenar sunucularımızla sıkıştırılıp sıkıştırılmadığını kesen bir halka grafik içerir. Sıkıştırılan varlıkların yüzdesi, kullanılan sıkıştırma türüne göre bölünür. Her bir sıkıştırma türü ve durumu için grafiğin altında ayrıntılı bilgiler verilmiştir. |
| Dosya türleri |Hesabınız için CDN 'umuz aracılığıyla istenen ilk 10 dosya türünü görüntüleyen bir çubuk grafik içerir. Bu raporun amaçları doğrultusunda, bir dosya türü varlığın dosya adı uzantısı ve Internet medya türü (örn. html \[ Metni/HTML \] ,. htm \[ metin/html \] ,. aspx \[ Metni/HTML vb \] .) tarafından tanımlanır. Ayrıntılı bilgiler, en üstteki 100 dosya türleri için grafiğin altında verilmiştir. |
| Benzersiz dosyalar |Belirli bir günde belirli bir süre boyunca istenen toplam benzersiz varlık sayısını çizmiş bir grafik içerir. |
| Belirteç kimlik doğrulama özeti |İstenen varlıkların Token-Based kimlik doğrulaması tarafından korunup korunmayacağı hakkında hızlı bir genel bakış sağlayan bir pasta grafik içerir. Korunan varlıklar, denendiği kimlik doğrulamasının sonuçlarına göre grafikte görüntülenir. |
| Belirteç kimlik doğrulaması reddetme ayrıntıları |Token-Based kimlik doğrulaması nedeniyle reddedilen ilk 10 isteği görüntülemenize olanak tanıyan bir çubuk grafik içerir. |
| HTTP yanıt kodları |Uç sunucularımız tarafından HTTP istemcilerinize gönderilen HTTP durum kodlarının dökümünü (ör., 200 Tamam, 403 Yasak, 404 bulunamadı vb.) sağlar. Pasta grafik, varlıklarınızın nasıl sunulduğunu hızlı bir şekilde değerlendirmenize olanak tanır. Grafiğin altındaki her yanıt kodu için ayrıntılı istatistiksel veriler sağlanır. |
| 404 hata |404 olmayan bir yanıt kodu ile sonuçlanan ilk 10 isteği görüntülemenize olanak tanıyan bir çubuk grafik içerir. |
| 403 hata |403 yasaklanmış yanıt kodu ile sonuçlanan ilk 10 isteği görüntülemenize olanak tanıyan bir çubuk grafik içerir. 403 yasaklanmış bir yanıt kodu, bir istek müşteri kaynak sunucusu veya POP 'umuza ait bir uç sunucu tarafından reddedildiğinde oluşur. |
| 4 xx hata |400 aralığında yanıt kodu ile sonuçlanan ilk 10 isteği görüntülemenize olanak tanıyan bir çubuk grafik içerir. Bu rapordan hariç tutulan 403, 404 yasak yanıt kodlarıdır. Genellikle, bir istemci hatası nedeniyle bir istek reddedildiğinde bir 4xx yanıt kodu oluşur. |
| 504 hata |504 ağ geçidi zaman aşımı yanıt kodu ile sonuçlanan ilk 10 isteği görüntülemenize olanak tanıyan bir çubuk grafik içerir. Bir HTTP proxy 'si başka bir sunucuyla iletişim kurmaya çalıştığında bir zaman aşımı oluştuğunda, 504 ağ geçidi zaman aşımı yanıt kodu oluşur. CDN 'imizde, bir uç sunucu bir müşteri kaynak sunucusuyla iletişim kuramazsa genellikle bir 504 ağ geçidi zaman aşımı yanıt kodu oluşur. |
| 502 hata |502 hatalı ağ geçidi yanıt kodu ile sonuçlanan ilk 10 isteği görüntülemenize olanak tanıyan bir çubuk grafik içerir. Bir sunucu ile HTTP proxy arasında HTTP protokol hatası oluştuğunda 502 hatalı ağ geçidi yanıt kodu oluşur. CDN 'umuz durumunda, genellikle bir müşteri kaynak sunucusu bir uç sunucuya geçersiz yanıt döndürdüğünde 502 hatalı bir ağ geçidi yanıt kodu oluşur. Bir yanıt ayrıştırılamamıyorsa veya tamamlanmamışsa geçersizdir. |
| 5 xx hata |500 aralığında yanıt kodu ile sonuçlanan ilk 10 isteği görüntülemenize olanak tanıyan bir çubuk grafik içerir.  Bu rapordan dışlanan 502 hatalı ağ geçidi ve 504 ağ geçidi zaman aşımı yanıt kodlarıdır. |

## <a name="see-also"></a>Ayrıca bkz.
* [Azure CDN'ye Genel Bakış](cdn-overview.md)
* [Microsoft Azure CDN 'de gerçek zamanlı istatistikler](cdn-real-time-stats.md)
* [Kurallar altyapısını kullanarak varsayılan HTTP davranışını geçersiz kılma](./cdn-verizon-premium-rules-engine.md)
* [Gelişmiş HTTP raporları](cdn-advanced-http-reports.md)