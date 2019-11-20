---
title: Azure cdn'de kenar düğümü performansını çözümleme | Microsoft Docs
description: Microsoft Azure cdn'de kenar düğümü performansını analiz edin. Uç Performans Analizi ve CDN ayrıntılı bilgi trafiğinin ve bant genişliği kullanımı sağlar.
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
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: b8a65d4ae6aaac78e642c851a66b745a940fa0ad
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593910"
---
# <a name="analyze-edge-node-performance-in-microsoft-azure-cdn"></a>Microsoft Azure CDN’de kenar düğümü performansını çözümleme
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Genel Bakış
Uç Performans Analizi ve CDN ayrıntılı bilgi trafiğinin ve bant genişliği kullanımı sağlar. Bu bilgiler daha sonra nasıl varlıklarınızın hakkında öngörü sağlayan eğilim istatistikleri oluşturmak için kullanılabilir önbelleğe alınmış ve müşterilerinize teslim edilir. Sırayla bu sayede bir strateji içerik teslimini iyileştirecektir nasıl oluşturmak ve sorunları olması gerektiğini belirlemek için CDN tackled için daha iyi yararlanın. Sonuç olarak, yalnızca veri teslim performansını artırmak mümkün olacaktır, ancak ayrıca CDN maliyetlerinizi azaltmak mümkün olacaktır.

> [!NOTE]
> Tüm raporlar, bir tarih/saat belirtirken UTC/GMT gösterimini kullanın.
> 
> 

## <a name="reports-and-log-collection"></a>Raporlar ve günlük toplama
Alan raporlar oluşturmadan önce CDN etkinlik verileri uç Performans Analizi modülü tarafından toplanan gerekir. Bu toplama işlemi, önceki gün sırasında gerçekleşen etkinliği bir gün ve kapsayan bir kez gerçekleşir. Bir raporun istatistikleri işlendiği ve mutlaka yapmak zaman günün istatistikleri bir örneği temsil eden bu araç, geçerli gün için eksiksiz veri Seti içerir. Performansını değerlendirmek için bu raporları sunucunun birincil işlevi olduğu. Bunlar faturalandırma veya tam sayısal istatistikleri için kullanılmamalıdır.

> [!NOTE]
> Uç performans analizi raporları oluşturulan ham verileri en az 90 gün boyunca kullanılabilir.
> 
> 

## <a name="dashboard"></a>Pano
Uç Performans Analizi Panosu, geçerli ve geçmiş CDN trafiği istatistikleri ve grafik aracılığıyla izler. Bu pano, hesabınız için CDN trafiği performans üzerindeki son hem de uzun vadeli eğilimleri algılamak için kullanın.

Bu panoyu oluşur:

* Ana Ölçümler ve eğilimleri görselleştirme sağlayan etkileşimli bir grafik.
* Ana Ölçümler ve eğilimler için uzun vadeli desen bir fikir sağlayan bir zaman çizelgesi.
* Ana Ölçümler ve hakkında istatistiksel bilgi CDN ağımız gibi genel performansı, kullanım ve verimlilik ile site trafiğini artırır.

### <a name="accessing-the-edge-performance-dashboard"></a>Edge performans panoya erişme
1. CDN profili dikey penceresinden tıklayın **Yönet** düğmesi.
   
    ![CDN profili dikey penceresi Yönet düğmesi](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    CDN yönetim portalına açılır.
2. Üzerine **Analytics** sekmesine ve ardından üzerine **uç Performans Analizi** açılır öğesi.  Tıklayarak **Pano**.
   
    Edge düğüm analytics Panosu görüntülenir.

### <a name="chart"></a>Grafik
Panoyu doğrudan görünür zaman çizelgesi seçili süre içinde bir ölçüm izleyen bir grafik içerir.  Bir zaman çizelgesi grafikleri son iki yıl CDN etkinliği yukarı doğrudan grafiğin altına görüntülenir.

#### <a name="using-the-chart"></a>Grafik kullanma
* Varsayılan olarak, son 30 gün için önbellek verimliliğini oranı grafiğinin.
* Bu grafik, günlük olarak Harmanlanmış verilerden oluşturulur.
* Bir tarih ve ölçüm değeri geldiğinizde çizgi grafik bir günde belirli bir tarihte gösterir.
* Hafta sonları grafik üzerine temsil eden açık gri dikey çubuk bir katmana geçiş yapmak için hafta sonları vurgulayın tıklayın. Bu tür bir katmana hafta sonları trafik düzenlerini tanımlamak için yararlıdır.
* Görünüm bir yıl grafik üzerine aynı süre boyunca önceki yılın etkinliğin bir katmana geçiş yapmak için önce tıklayın. Bu karşılaştırma türünü, uzun vadeli CDN kullanım biçimlerini hakkında Öngörüler sağlar. Grafiğin sağ üst köşede, her bir çizgi grafiği için renk kodu belirten bir gösterge içerir.

#### <a name="updating-the-chart"></a>Grafik güncelleme
* Zaman aralığı: Aşağıdakilerden birini gerçekleştirin:
  * Zaman çizelgesinde istediğiniz bölgeyi seçin. Grafik, seçilen zaman aralığı için karşılık gelen verilerle güncelleştirilir.
  * Grafik en çok iki yıl kullanılabilir tüm geçmiş verileri görüntülemek için çift tıklayın.
* Ölçüm: İstenen ölçümün yanında görünen grafik simgesine tıklayın. Grafik ve zaman çizelgesi, karşılık gelen bir ölçüm için verilerle yenilenir.

### <a name="key-metrics-and-statistics"></a>Ana Ölçümler ve istatistikleri
#### <a name="efficiency-metrics"></a>Verimliliği ölçümleri
Önbellek verimliliğini geliştirilmiş olup olmadığını görmek için bu ölçümleri amacı olan. Önbellek verimliliğini türetilmiş başlıca avantajları şunlardır:

* Daha az yük kaynak sunucuda neden olabilir:
  * Daha iyi web sunucusu performans.
  * Operasyonel maliyetlerini düşürür.
* Daha fazla isteği doğrudan CDN'den hizmet alabilecektir olduğundan veri teslim hızlandırma geliştirildi.

| Alan | Açıklama |
| --- | --- |
| Önbellek verimliliğini |Aktarılan veriler, önbellekten sunulduğu yüzdesini gösterir. Bu ölçüm ölçümleri istenen içeriği önbelleğe alınmış bir sürümü, doğrudan CDN'den (edge sunucuları) sunulan kararınız istekte (örneğin, web tarayıcısı gerekir) |
| İsabet oranı |Önbellekten sunulduğunu isteklerin yüzdesini gösterir. Bu ölçüm ölçümleri istenen içeriği önbelleğe alınmış bir sürümü olduğunda hizmet doğrudan CDN'den (edge sunucuları) kararınız istekte (örneğin, web tarayıcısı gerekir). |
| % Uzak bayt - hiçbir önbellek yapılandırma |Önbellek atlama özelliği (HTTP kural altyapısı) sonucu olarak önbelleğe alınmamış CDN (edge sunucular) için kaynak sunuculardan sunulduğu trafiği yüzdesini gösterir. |
| Uzak bayt - % önbellek süresi |Eski içerik yeniden doğrulama sonucu olarak (edge sunucuları) CDN ile kaynak sunuculardan sunulduğu trafiği yüzdesini gösterir. |

#### <a name="usage-metrics"></a>Ölçümleri kullanma
Bu ölçümler amacı aşağıdaki maliyet kesme ölçüleri hakkında Öngörüler sağlamaktır:

* CDN üzerinden operasyonel maliyetlerini en aza indirme.
* Önbellek verimliliğini ve sıkıştırma aracılığıyla CDN harcamalarını azaltır.

> [!NOTE]
> Trafik birim numaralarını oranı ve yüzde hesaplamalarda kullanılan ve yüksek hacimli müşterileri için toplam trafiğin bir kısmını yalnızca gösterebilir trafik temsil eder.
> 
> 

| Alan | Açıklama |
| --- | --- |
| Giden bayt Kaydet |(Edge sunucuları) istek sahibine (örneğin, web tarayıcısı) cdn'den her istek için aktarılan bayt sayısını gösterir. |
| Önbelleği yapılandırma bayt hız |Trafik (edge sunucuları) nedeniyle önbellek atlama özelliği önbelleğe alınmamış döndürür (örneğin, web tarayıcısı) cdn'den yüzdesini gösterir. |
| Sıkıştırılmış bayt oranı |Sıkıştırılmış bir biçimde (edge sunucuları) CDN'den isteyenlere (örneğin, web tarayıcısı) için gönderilen trafik yüzdesini gösterir. |
| Giden bayt |CDN'den (edge sunucuları) (örneğin, web tarayıcısı) istemciye teslim edilen bayt veri miktarını gösterir. |
| Bayt |Bayt cinsinden veri miktarı (örneğin, web tarayıcısı) isteyenlere gönderilen CDN (edge sunucuları) gösterir. |
| Bayt uzaktan |Bayt cinsinden CDN ve müşteri kaynak sunuculardan CDN (edge sunucular) için gönderilen veri miktarını gösterir. |

#### <a name="performance-metrics"></a>Performans Ölçümleri
Bu ölçümler amacı genel CDN performansını trafiğiniz için izlemektir.

| Alan | Açıklama |
| --- | --- |
| Aktarım hızı |Başlangıçtan istek sahibine CDN'den içerik transfer ortalama hızını gösterir. |
| Duration |Ortalama süreyi belirtir (örneğin, web tarayıcısı) istek sahibine bir varlık teslim için geçen milisaniye cinsinden. |
| Sıkıştırılmış istek hızı |CDN'den (edge sunucuları) (örneğin, web tarayıcısı) istemciye teslim edilen isabet yüzdesi sıkıştırılmış biçimde gösterir. |
| 4xx hata oranı |4xx durum kodları oluşturulan isabet yüzdesini gösterir. |
| 5XX hata oranı |5xx durum kodu oluşturulan isabet yüzdesini gösterir. |
| İsabet sayısı |CDN içeriği için istek sayısını gösterir. |

#### <a name="secure-traffic-metrics"></a>Güvenli trafiği ölçümleri
Bu ölçümler amacı, HTTPS trafiği için CDN performansını izlemektir.

| Alan | Açıklama |
| --- | --- |
| Önbellek verimliliğini güvenliğini sağlama |Önbellekten sunulduğunu HTTPS isteklerinde aktarılan verilerin yüzdesini gösterir. Bu ölçüm ölçümleri istenen içeriği önbelleğe alınmış bir sürümü olduğunda hizmet doğrudan CDN'den (edge sunucuları) kararınız istekte (örneğin, web tarayıcısı) HTTPS üzerinden. |
| Güvenli aktarım hızı |Hangi kararınız istekte (örneğin, web sunucuları) (edge sunucuları) CDN'den içerik aktarılan ortalaması HTTPS üzerinden gösterir. |
| Güvenli ortalama süre |Ortalama süreyi belirten bir varlık, HTTPS üzerinden bir istemciye (örneğin, web tarayıcısı) sunmak için geçen milisaniye cinsinden. |
| Güvenli isabet sayısı |CDN içeriğini HTTPS isteklerinin sayısını gösterir. |
| Giden bayt güvenliğini sağlama |CDN'den (edge sunucuları) (örneğin, web tarayıcısı) istemciye teslim bayt cinsinden HTTPS trafiğini gösterir. |

## <a name="reports"></a>Raporlar
Bu modüldeki her rapor bir grafik ve bant genişliği ve trafiği kullanım ölçümleri farklı türleri için istatistikleri içeren (örneğin, HTTP durum kodları, önbellek durum kodları, istek URL'si, vb..). Bu bilgileri nasıl içerik istemcilerinize hizmet verilen içinde daha ayrıntılı inceleyin ve veri teslim performansını artırmak için CDN davranışı ince ayar yapmak için kullanılabilir.

### <a name="accessing-the-edge-performance-reports"></a>Edge performans raporları erişme
1. CDN profili dikey penceresinden tıklayın **Yönet** düğmesi.
   
    ![CDN profili dikey penceresi Yönet düğmesi](./media/cdn-edge-performance/cdn-manage-btn.png)
   
    CDN yönetim portalına açılır.
2. Üzerine **Analytics** sekmesine ve ardından üzerine **uç Performans Analizi** açılır öğesi.  Tıklayarak **HTTP büyük nesne**.
   
    Kenar düğümü analytics raporları ekran görüntülenir.

| Rapor | Açıklama |
| --- | --- |
| Günlük Özet |Belirli bir süre boyunca günlük trafiği eğilimleri görüntülemenize olanak sağlar. Bu grafikteki her bir çubuğun belirli bir tarihi temsil eder. Bu tarihte oluşan toplam sayısı çubuğu boyutunu gösterir. |
| Saatlik Özet |Trafik eğilimleri belirli bir süre boyunca saatlik görüntülemenize olanak sağlar. Bu grafikteki her bir çubuğun belirli bir tarihte tek bir saat temsil eder. Bu saat sırasında oluşan toplam sayısı çubuğu boyutunu gösterir. |
| Protokoller |HTTP ve HTTPS protokollerini arasındaki trafiği dökümünü gösterir. Halka grafik Protokolü her tür için oluştu isabet yüzdesini gösterir. |
| HTTP yöntemleri |HTTP yöntemleri verilerinizi istemek için kullanılan hızlı bir fikir edinmenizi sağlar. Genellikle, en yaygın HTTP isteği GET, HEAD ve sonrası yöntemlerdir. Halka grafik HTTP istek yöntemi her tür için oluştu isabet yüzdesini gösterir. |
| URL'leri |İlk 10 istenen URL görüntüleyen bir grafik içerir. Her bir URL için bir çubuk görüntülenir. Belirli URL zaman aralığı üretti kaç isabet sayısı raporun kapsadığı çubuğu yüksekliğini belirtir. İlk 100 istatistiklerini URL'leri aşağıdaki doğrudan bu grafikte görüntülenen istedi. |
| CNAME'ler |10 CNAME'ler varlıkları zamanla istemek için kullanılan bir rapor span üst görüntüleyen bir grafik içerir. İlk 100 istatistiklerini CNAME'ler aşağıdaki doğrudan bu grafikte görüntülenen istedi. |
| Çıkış noktaları |En çok 10 CDN görüntüleyen bir grafik veya varlıklar belirtilen bir süredeki istenen müşteri kaynak sunucuları içerir. İlk 100 istatistikleri, CDN veya müşteri kaynak sunucusu aşağıdaki doğrudan bu grafikte görüntülenen istedi. Müşteri kaynak sunucuları, dizin adı seçeneği tanımlanan ad tarafından tanımlanır. |
| Coğrafi POP'ları |Trafiğiniz ne kadarının yönlendirilmek bir belirli noktası bulunma (POP) gösterir. Üç harfli kısaltması bizim CDN ağındaki POP temsil eder. |
| İstemciler |Belirtilen bir süredeki varlıklar istenen üst 10 istemciyi görüntüleyen bir grafik içerir. Bu raporun amacı doğrultusunda, aynı IP adresinden kaynaklanan tüm istekler aynı istemciden olarak değerlendirilir. En çok 100 istemciler için istatistikleri, doğrudan bu grafiğin altında görüntülenir. Bu rapor, üst istemcileriniz için indirme etkinliği desenlerini belirlemek için yararlıdır. |
| Önbellek durumları |Genel son kullanıcı deneyimini iyileştirmeye yönelik yaklaşımları gösterebilir önbellek davranışının ayrıntılı bir dökümünü sağlar. En hızlı performansı Önbelleği İsabetli Okuma geldiğinden en aza İsabetsiz Önbellek okuma sayısı ve süresi dolmuş önbellek isabet veri teslim hızını en iyi duruma getirebilirsiniz. |
| Hiçbiri ayrıntıları |İlk 10 URL'leri varlıkların önbellek içerik güncellik belirtilen bir süredeki denetlenmedi görüntüleyen bir grafik içerir. Bu tür varlıklar için en popüler 100 URL'ler için istatistikleri, doğrudan bu grafiğin altında görüntülenir. |
| CONFIG_NOCACHE ayrıntıları |Müşterinin CDN yapılandırması nedeniyle önbelleğe alınmamış varlıklar için en iyi 10 URL'leri görüntüleyen bir grafik içerir. Bu tür varlıklar kaynak sunucudan doğrudan sunulduğunu. Bu tür varlıklar için en popüler 100 URL'ler için istatistikleri, doğrudan bu grafiğin altında görüntülenir. |
| UNCACHEABLE ayrıntıları |İlk 10 URL'leri nedeniyle istek üst bilgisi verileri önbelleğe alınmamış varlıkları görüntüleyen bir grafik içerir. Bu tür varlıklar için en popüler 100 URL'ler için istatistikleri, doğrudan bu grafiğin altında görüntülenir. |
| TCP_HIT ayrıntıları |Önbellekten hemen sunulan varlıklar için en iyi 10 URL'leri görüntüleyen bir grafik içerir. Bu tür varlıklar için en popüler 100 URL'ler için istatistikleri, doğrudan bu grafiğin altında görüntülenir. |
| TCP_MISS ayrıntıları |İlk 10 URL'leri TCP_MISS önbellek durumuna sahip varlıklar için görüntüleyen bir grafik içerir. Bu tür varlıklar için en popüler 100 URL'ler için istatistikleri, doğrudan bu grafiğin altında görüntülenir. |
| TCP_EXPIRED_HIT ayrıntıları |Doğrudan POP sunulduğunu eski varlıklar için en iyi 10 URL'leri görüntüleyen bir grafik içerir. Bu tür varlıklar için en popüler 100 URL'ler için istatistikleri, doğrudan bu grafiğin altında görüntülenir. |
| TCP_EXPIRED_MISS ayrıntıları |Yeni bir sürümü kaynak sunucudan alınacak olan eski varlıklar için en iyi 10 URL'leri görüntüleyen bir grafik içerir. Bu tür varlıklar için en popüler 100 URL'ler için istatistikleri, doğrudan bu grafiğin altında görüntülenir. |
| TCP_CLIENT_REFRESH_MISS ayrıntıları |İlk 10 URL'leri varlıklar bir istemciden bir no-cache isteği nedeniyle kaynak sunucudan alındığını için görüntüleyen bir çubuk grafiği içerir. Bu tür istekleri için en popüler 100 URL'ler için istatistikleri, doğrudan bu grafiğin altına görüntülenir. |
| İstemci istek türleri |HTTP istemciler (örneğin, tarayıcıları) tarafından yapılan istek türlerini gösterir. Bu rapor, istekleri nasıl ele dair bir fikir sağlar halka grafik içerir. Her istek türü için bant genişliği ve trafik bilgileri, grafiğin altına görüntülenir. |
| Kullanıcı Aracısı |İçeriğinizi bizim CDN üzerinden istemek için en iyi 10 kullanıcı aracılar görüntüleyen bir çubuk grafiği içerir. Genellikle bir web tarayıcısı, media player veya cep telefonu tarayıcı kullanıcı aracısı oluşur. En çok 100 kullanıcı aracıları için istatistikleri, doğrudan bu grafiğin altına görüntülenir. |
| Başvuran |Bizim CDN üzerinden erişilen içerik için en iyi 10 başvuran görüntüleyen bir çubuk grafiği içerir. Genellikle, bir başvuran web sayfasının veya içeriğiniz için bağlantı kaynağı URL'dir. Ayrıntılı bilgi aşağıdaki grafikte ilk 100 Başvuranlar için sağlanır. |
| Sıkıştırma türleri |Olup, edge sunucularımızı sıkıştırılan tarafından istenilen varlıkların keser halka grafik içerir. Sıkıştırılmış varlıklar yüzdesi, kullanılan sıkıştırma türünü tarafından ayrılmıştır. Ayrıntılı bilgi aşağıdaki grafikte, her bir sıkıştırma türü ve durum için sağlanır. |
| Dosya türleri |Hesabınız için sunduğumuz CDN üzerinden istenen üst 10 dosya türleri görüntüleyen bir çubuk grafik içerir. Bu raporun amacı doğrultusunda, bir dosya türü varlığın dosya adı uzantısı ile tanımlanır ve Internet medya türü (örneğin, .html \[metin/html\], .htm \[metin/html\], .aspx \[metin/html\]vb..). Ayrıntılı bilgi aşağıdaki grafikte ilk 100 dosya türleri için sağlanır. |
| Benzersiz dosyaları |Belirli bir günde belirli bir süre istenen benzersiz varlıklar toplam sayısını gösteren bir grafik içerir. |
| Belirteç kimlik doğrulaması özeti |İstenilen varlıkların belirteç tabanlı kimlik doğrulamasını olup korunan hızlı bir genel bakış sağlayan bir pasta grafiğinin içerir. Korunan varlıklar göre denenen kendi kimlik doğrulama sonuçlarını grafik olarak görüntülenir. |
| Belirteç kimlik doğrulaması reddetme ayrıntıları |Belirteç tabanlı kimlik doğrulama nedeniyle reddedildi üst 10 istekleri görüntülemenize imkan tanıyan bir çubuk grafiği içerir. |
| HTTP yanıt kodları |HTTP durum kodları dökümünü sağlar (örneğin, 200 Tamam, 403 Yasak, 404 bulunamadı, vb.), teslim HTTP istemcilerinize edge sunucularımızı. Bir pasta grafiğinin varlıklarınızı nasıl sunulduğunu hızlıca değerlendirmenize olanak sağlar. Ayrıntılı istatistik verileri, grafiğin altına her yanıt kodu için sağlanır. |
| 404 hataları |Bir 404 bulunamadı yanıt kodunda sonuçlanan ilk 10 istekleri görüntülemenize imkan tanıyan bir çubuk grafiği içerir. |
| 403 hataları |Bir 403 Yasak yanıtı kodunda sonuçlanan ilk 10 istekleri görüntülemenize imkan tanıyan bir çubuk grafiği içerir. Bir müşteri kaynak sunucu veya bizim POP üzerinde bir uç sunucusu tarafından bir istek reddedildiğinde bir 403 Yasak yanıtı kodu gerçekleşir. |
| 4xx hataları |Yanıt kodu 400 aralığında sonuçlandı üst 10 istekleri görüntülemenize imkan tanıyan bir çubuk grafiği içerir. Bu raporun dışında olan 403 Yasak yanıtı kodları 404 bulunamadı ve. Genellikle, bir istemci hatası nedeniyle bir istek reddedildiğinde yanıt kodu 4xx gerçekleşir. |
| 504 hataları |504 ağ geçidi zaman aşımı yanıt kodunda sonuçlanan ilk 10 istekleri görüntülemenize imkan tanıyan bir çubuk grafiği içerir. Bir 504 ağ geçidi zaman aşımı yanıt kodu, başka bir sunucuyla iletişim kurmak bir HTTP proxy'sinin çalışırken zaman aşımı meydana geldiğinde gerçekleşir. Bir uç sunucusu müşteri kaynak sunucu ile iletişim kuramadı bizim CDN söz konusu olduğunda, bir 504 ağ geçidi zaman aşımı yanıt kodu genellikle gerçekleşir. |
| 502 hataları |502 hatalı ağ geçidi yanıt kodunda sonuçlanan ilk 10 istekleri görüntülemenize imkan tanıyan bir çubuk grafiği içerir. 502 hatalı ağ geçidi yanıt kodu, bir sunucu ile bir HTTP Ara sunucusu arasında bir HTTP protokolü hatası oluştuğunda gerçekleşir. Müşteri kaynak sunucu bir uç sunucusu için geçersiz bir yanıt döndürüldüğünde bizim CDN söz konusu olduğunda, genellikle bir 502 hatalı ağ geçidi yanıt kodu gerçekleşir. Bunu ayrıştırılamazsa ya da tamamlanmamış ise bir yanıt geçersiz. |
| 5XX hataları |Yanıt kodu 500 aralığında sonuçlanan en fazla 10 istekler görüntülemenize olanak sağlayan bir çubuk grafik içerir.  Bu raporun dışında 502 hatalı ağ geçidi ve 504 ağ geçidi zaman aşımı yanıt kodları markalarıdır. |

## <a name="see-also"></a>Ayrıca bkz.
* [Azure CDN'ye genel bakış](cdn-overview.md)
* [Microsoft Azure cdn'de gerçek zamanlı İstatistikler](cdn-real-time-stats.md)
* [Kural altyapısı kullanarak varsayılan HTTP davranışı geçersiz kılma](cdn-rules-engine.md)
* [Gelişmiş HTTP Raporları](cdn-advanced-http-reports.md)

