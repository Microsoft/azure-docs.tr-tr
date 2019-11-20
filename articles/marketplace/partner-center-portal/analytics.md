---
title: Iş Ortağı Merkezi 'nde ticari Market için analiz
description: Satışları izlemek, performansı değerlendirmek ve Market tekliflerinizi iyileştirmek için analitik raporlara nasıl erişebileceğinizi öğrenin.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/14/2019
ms.openlocfilehash: 53d7cb125f59491190aadb07755954ade78356fa
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075518"
---
# <a name="analytics-for-the-commercial-marketplace-in-partner-center"></a>Iş Ortağı Merkezi 'nde ticari Market için analiz

Satışları izlemek, performansı değerlendirmek ve Market 'teki tekliflerinizi iyileştirmek için Microsoft Iş Ortağı Merkezi 'ndeki analitik raporlara nasıl erişebileceğinizi öğrenin. İş ortağı olarak, Iş Ortağı Merkezi tarafından desteklenen veri görselleştirme ve Öngörüler grafiklerini kullanarak teklif listelerinizi izleyebilir ve satışlarınızı en üst düzeye çıkarmak için kullanabileceğiniz yolları bulabilirsiniz. Geliştirilmiş analiz araçları, performans sonuçları üzerinde işlem yapma ve müşterilerinizle ve satıcılarınızla daha iyi ilişkiler elde etme olanağı sağlar.

Iş ortağı merkezi analiz araçlarına erişmek için, ticari Market altında **[analiz](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)** panosunu açın.

|**Pano**|**Görünen içerik**|
|:---|:---|
|[Özet](#summary-dashboard)|Tekliflerinizle ilgili Market etkinliklerini özetleyen grafikler, eğilimler ve toplam veri değerleri|
|[Siparişler](#orders-dashboard)|Grafik ve indirilebilir biçimdeki Siparişleriniz hakkında bilgi|
|[Müşterinizin](#customer-dashboard)|Grafik ve indirilebilir biçimde sunulan büyüme eğilimleri dahil olmak üzere müşterileriniz hakkında bilgiler|
|[Dosyaların](#downloads-dashboard)|Son 30 gün içindeki indirme isteklerinizin listesi|
|[Derecelendirmeler ve İncelemeler](#ratings-and-reviews)| Derecelendirdikleri tekliflere ait olan derecelendirmeler ve müşterilerinizin değerlendirmeleriyle ilgili bilgiler.

## <a name="summary-dashboard"></a>Özet Panosu

**Özet** panosu, her teklif türüne göre genel bir bakış sunar. **Öngörüler** , önemli bilgileri bir bakışta gösterir ve tekliflerinizin satış etkinliğinin geniş bir görünümünü sağlar. **Özet** panosunu kullanarak bu raporları görselleştirebilirsiniz:

- [Toplam siparişler](#totals)
- [Toplam Müşteri](#totals)
- [Müşterilerinizin coğrafi konumu](#customers-by-geography)
- [Müşteri ve sipariş bilgileriniz temelinde gelişen eğilimler](#growth-trend-chart)
- [En yüksek siparişleri olan müşteri puan tablosu](#customer-leaderboard)
- [Teklif adına göre düzenlenmiş siparişlerin sayısı](#offers-by-orders)

### <a name="date-range"></a>Tarih aralığı

Her sayfanın sağ üst köşesinde bir tarih aralığı seçimi bulabilirsiniz. **Özet** sayfası grafiklerinin çıktısı, son 3, 6 veya 12 aya dayalı bir tarih aralığı seçilerek veya en fazla 12 ay süresince özel bir tarih aralığı seçilerek özelleştirilebilir. Varsayılan tarih aralığı altı aydır.

![İş Ortağı Merkezi panoyu çözümle](./media/analyze-dashboard.png)

### <a name="totals"></a>Düzeyinin

**Toplamlar** bölümü, seçilen tarih aralığı boyunca oluşturulan tüm siparişlerin veya alınan müşterilerin sayısını görüntüler. 

- **Toplam siparişlerin** ve **Toplam müşterilerin** yanındaki yüzde değeri, önceki aya kıyasla büyüme miktarını temsil eder. 
- Yukarıyı gösteren bir yeşil üçgen, pozitif bir büyüme eğilimi gösterir. Aşağıyı gösteren kırmızı üçgen, önceki aya göre negatif büyüme eğilimi gösterir. 
- Sipariş ve müşteri büyüme eğilimleri çubuk grafiklerle temsil edilir ve grafiğin sütunlarının üzerine gelindiğinde her ay için değeri görüntüler.

![İş Ortağı Merkezi Toplam Analizi](./media/analyze-totals.png)

### <a name="customers-by-geography"></a>Coğrafya ile müşteriler

Coğrafya ısı haritasını **tarafından müşteriler** bir dünya eşlemesindeki müşteri sayısını görüntüler.

- Tam konumu görüntülemek için Haritayı taşıyabilirsiniz.
- Belirli bir konuma yakınlaştırabilirsiniz.
- Isı haritasını 'in belirli bir konumdaki müşteri sayısı (veya [sıra sayısı](#orders-by-geography)) ayrıntılarını görüntülemek için bir ek kılavuz vardır.
- Haritada konum yakınlaştırmak için kılavuzda bir ülke arayabilir ve seçebilirsiniz. Haritadaki **giriş** düğmesine basarak özgün görünüme dönün.
- **Yeni** bir müşteri, seçili tarih aralığında yer alan ilk kez tekliflerinizin birini satın almış.

![İş ortağı merkezi müşteri Coğrafya çözümle](./media/analyze-customer-geography.png)

### <a name="growth-trend-chart"></a>Büyüme eğilimi grafiği

Eğilimleri, seçilen tarih aralığına göre aya göre oluşturulan veya **müşterilerin elde ettiği**, aylık olarak gösterildiği **siparişlerin** büyümesi temelinde görebilirsiniz. Bu eğilimleri daha fazla analiz ederek grafiğin altındaki bağlantılar ' ı seçerek ilgili **sipariş** veya **Müşteri** sayfalarına gidebilirsiniz.

![İş Ortağı Merkezi büyüme eğilimlerini çözümleme](./media/analyze-growth-trends.png)

### <a name="customer-leaderboard"></a>Müşteri puan tablosu

En yüksek sayıda siparişi olan ilk 50 müşteri, en yüksek sıra sayısına ve sıra yüzdesine göre sıralanmış bir *öncü panoda*görüntülenir. 

- Profil ayrıntılarını, teklif tarafından düzenlenen siparişleri veya Azure lisans türü ve fiyatlandırma kanalı tarafından düzenlenen siparişleri görüntülemek için bir müşteri seçin. 
- Yer halka grafik **bazında teklifler** , ilk dört teklifi (order count 'a göre) ve kalan teklifleri ' rest All ' olarak gruplanmış olarak gösterir.

> [!NOTE]
> Müşterinin kişisel bilgileri yalnızca müşterinin onay sağlamış olması halinde sunulur. **Sahip** rolü izinleri düzeyi ile oturum açtıysanız, bu bilgileri görüntüleyebilirsiniz. **Katkıda** bulunan rolüne sahip kullanıcılar bu bilgileri görüntüleyemeyecektir. [Kullanıcı rolleri ve izinleri hakkında daha fazla bilgi edinin](./manage-account.md#define-user-roles-and-permissions).

![İş Ortağı Merkezi büyüme eğilimlerini çözümleme](./media/analyze-growth-trends.png)

### <a name="offers-by-orders"></a>Siparişlere göre teklifler

**Siparişlere göre teklifler** grafiği, etkin siparişlerinizi teklif adına göre düzenler. 

- Sürüklediğiniz teklifin daha fazla ayrıntılarını göstermek için sol Halka grafiğindeki dilimlerin üzerine sağ halka grafik üzerinde sürükleyebilirsiniz. Bu iki grafik, belirli bir teklifi diğer tekliflerinizin (' rest All ') performansı ile karşılaştırmanıza imkan tanır. 

![İş Ortağı Merkezi teklifleri siparişlere göre analiz etme](./media/analyze-offer-by-order.png)

## <a name="orders-dashboard"></a>Siparişler panosu

**Çözümle** menüsünün **siparişler** panosu, SaaS tekliflerinizin tamamına ait geçerli siparişleri görüntüler. Grafik gösterimlerini şu şekilde görüntüleyebilirsiniz:

- [Sıra toplamları](#order-totals)
- [Coğrafya tarafından siparişler](#orders-by-geography)
- [Etkin ve iptal edilmiş siparişlere yönelik eğilimler](#trends-for-active-and-canceled-orders)
- [Market lisans türüne göre düzenlenen siparişler](#orders-by-marketplace-license-type)
- [Yeni ve mevcut müşteriler tarafından düzenlenen siparişler](#orders-by-customer-type)
- [Sipariş Ayrıntıları tablosu](#order-details-table)

> [!NOTE]
> Analiz raporlarının Bulut İş Ortağı Portalı (CPP) ve Iş Ortağı Merkezi 'ndeki yeni ticari Market programında nasıl görüntüleneceği arasında farklılıklar vardır. Belirli bir şekilde, CPP içindeki **satıcı öngörülerinin** , kullanım tabanlı tekliflere ve kullanım tabanlı olmayan tekliflere yönelik verileri görüntüleyen bir **siparişler & kullanım** sekmesi vardır. Iş Ortağı Merkezi 'nde, **siparişler** sayfasının SaaS teklifleri için ayrı bir sekmesi vardır.

### <a name="order-totals"></a>Sıra toplamları

**Sipariş toplamı** bölümü, seçilen [Tarih aralığı](#date-range)boyunca hem **etkin** hem de **iptal edilen** siparişler dahil olmak üzere oluşturulan tüm siparişlerin sayısını görüntüler. 

- **Toplam siparişlerin** yanındaki yüzde değeri, önceki aya kıyasla büyüme miktarını temsil eder. 
- Yukarıyı gösteren bir yeşil üçgen, pozitif bir büyüme eğilimi gösterir. Aşağıyı gösteren kırmızı üçgen, önceki aya göre negatif büyüme eğilimi gösterir. 
- Büyüme eğilimleri çubuk grafiklerle temsil edilir ve grafiğin sütunlarının üzerine gelindiğinde her ay için değeri görüntüler.

### <a name="orders-by-geography"></a>Coğrafya tarafından siparişler

Coğrafya ısı haritasını **tarafından siparişler** , bir dünya haritasının siparişlerinin sayımını gösterir ve **[müşteriler ile Coğrafya ısı haritasını ile](#customers-by-geography)** aynı şekilde çalışır.

### <a name="trends-for-active-and-canceled-orders"></a>Etkin ve iptal edilmiş siparişlere yönelik eğilimler

**Etkin siparişler** , halka grafik sunarak tüm etkin siparişlerinizi kendi teklif adlarına göre düzenler.

- En üstteki dört teklif grafikte görüntülenir ve tekliflerin geri kalanı ' rest All ' olarak gruplandırılır.
- Yalnızca grafikteki teklifleri göstermek için göstergede belirli teklifleri seçebilirsiniz. 
- Grafikteki bir dilimin üzerine gelindiğinde, tüm tekliflerde toplam sipariş sayısı ile karşılaştırıldığında, bu teklifin sipariş sayısı ve yüzdesi görüntülenir.
- **Tekliflere göre siparişler eğilimi** , aylık büyüme eğilimlerini görüntüler. Month sütunu, teklif adına göre sipariş sayısını temsil eder. Çizgi grafik, z ekseni üzerinde çizilen büyüme yüzdesi eğilimini görüntüler.
- X ekseninde sağa ve sola kaydırmak ve belirli veri noktalarına odaklanmak için grafiğin en üstündeki kaydırıcıyı kullanabilirsiniz.
- Açıklamada belirli bir öğe seçerek eğilim grafiğini görüntüleyebilirsiniz.
- Ayrıca, **iptal edilen siparişlerin**eğilimlerini ve verilerini görüntülemeyi de seçebilirsiniz. Grafik, etkin siparişlerle aynı şekilde çalışır.

![İş Ortağı Merkezi etkin siparişleri çözümle](./media/analyze-active-orders.png)

### <a name="orders-by-marketplace-license-type"></a>Market lisans türüne göre siparişler
<!-- Section needs review and clarification!  -->
**Market lisans türüne göre siparişler** grafiğinde, Market teklifinin lisans türü ve faturalandırma yöntemine göre aylık bir sıra sayısı görüntülenir. Lisans türleri şunları içerir:

- **Azure üzerinden faturalandırılır**: Bu lisans türüyle [Microsoft aracılığıyla teklifinizi satmayı](./create-new-saas-offer.md#sell-through-microsoft) seçtiğinizde Microsoft, müşterileri sizin adınıza faturalar. Ödeme türleri, kredi kartı veya kurumsal faturalandırma aracılığıyla Kullandıkça Öde 'yi içerir.
- **Kendi lisansınızı getir**: Microsoft, müşterileri bu tür Market teklifinin kullanımı için faturalandırmaz. Market 'te **[Şimdi al (ücretsiz)](./create-new-saas-offer.md#get-it-now-free)** olarak listelenir.
- **Ücretsiz**: Microsoft, müşterileri bu tür Market teklifinin kullanımı için faturalandırmaz. Market 'te **[ücretsiz deneme](./create-new-saas-offer.md##free-trial-listing)** olarak listelenir.
- **Satıcı olarak Microsoft**: **[bulut çözümü sağlayıcısı (CSP) programının](./create-new-saas-offer.md)** bir parçası olarak Microsoft satıcıları tarafından satılan teklifleri temsil eder.

![İş Ortağı Merkezi, siparişleri lisans türüne göre analiz eder](./media/analyze-license-type.png)

### <a name="orders-by-customer-type"></a>Müşteri türüne göre siparişler

**Müşteri türüne göre siparişler** çubuk grafiğinde **yeni müşteriler** ve **mevcut müşteriler**arasında bölünen siparişlerin sayısı görüntülenir. 

- **Yeni bir müşteri** , bir veya daha fazla teklifinizden aynı takvim ayı (y ekseni) içinde ilk kez edinildi. **Mevcut bir müşteri** daha önce size bildirilen takvim ayına (y ekseninde) başlamadan önce bir teklif almış. 
- Ek bir pasta grafik, seçilen tarih aralığı için yeni veya mevcut müşteri tarafından oluşturulan tüm siparişleri temsil eder.
- Her iki grafikte de ilgili göstergeyi seçerek yalnızca yeni veya mevcut müşterileri görüntülemeyi tercih edebilirsiniz.

![İş Ortağı Merkezi siparişleri müşteri türüne göre analiz etme](./media/analyze-order-by-customer.png)

### <a name="order-details-table"></a>Sipariş Ayrıntıları tablosu

**Sipariş Ayrıntıları tablosu** , Alım tarihine göre sıralanan 1000 üst siparişin numaralandırılmış bir listesini görüntüler.

- Kılavuzdaki her bir sütun sıralanabilir.
- Kayıt sayısı 1000 ' den küçükse veriler TSV dosyasına ayıklanabilir.
- 1000 üzerinden kayıt numarası varsa, verilen veriler sonraki 30 güne ait bir indirmeler sayfasına zaman uyumsuz olarak yerleştirilir.
- Yalnızca ilgilendiğiniz verileri göstermek için, **Düzen Ayrıntıları tablosuna** filtreler uygulanabilir. Veriler ülke, Azure lisans türü, Market lisans türü, teklif türü, sipariş durumu, ücretsiz izler, Market abonelik KIMLIĞI, müşteri KIMLIĞI ve şirket adı ile filtrelenebilir.

![İş Ortağı Merkezi sipariş ayrıntılarını analiz etme](./media/analyze-order-details.png)

## <a name="customer-dashboard"></a>Müşteri panosu

**Çözümle** menüsünün **Müşteri** panosu, tekliflerinizi almış müşterilerin verilerini görüntüler. Grafik gösterimlerini şu şekilde görüntüleyebilirsiniz:

- [Müşteri toplamları](#customer-totals)
- [Coğrafya ile müşteriler](#customers-by-geography)
- [Müşteri eğilimleri](#customer-trends)
- [Siparişlere göre müşteriler](#customers-by-orders)
- [Müşteri Ayrıntıları tablosu](#customer-details-table)

### <a name="customer-totals"></a>Müşteri toplamları

**Müşteri toplamları** bölümü, seçilen [tarih aralığında](#date-range)yeni, mevcut ve tamamen dahil olmak üzere tüm müşterilerin sayısını görüntüler.

- Müşterilerin önceki aya kıyasla büyüme yüzdesi, yeşil veya aşağı gösterge göstergesinde sayı ve yukarı gösterge olarak belirtilmiştir.
- Büyüme eğilimleri çubuk grafiklerle temsil edilir ve grafiğin sütunlarının üzerine gelindiğinde her ay için değeri görüntüler.

#### <a name="customer-types"></a>Müşteri türleri

Üç müşteri türü vardır: yeni, var ve bir şekilde. 

- Yeni bir müşteri, seçili ay içinde ilk kez bir veya daha fazla teklifinizden elde etti.
- Mevcut bir müşteri, seçili olan aydan önce bir veya daha fazla teklifinizden elde etti.
- Oluşturulmuş bir müşteri, daha önce satın alınan tüm teklifleri iptal etti.

### <a name="customer-trends"></a>Müşteri eğilimleri

**Müşteri eğilimleri** grafiği, aylık bir büyüme eğilimi ile yeni, mevcut ve tamamen dahil olmak üzere tüm müşterilerin sayısını görüntüler.

- Çizgi grafik, genel müşteri büyüme yüzdelerini temsil eder. 
- Month sütunu, yeni, var olan ve tarafından kullanılan müşteriler tarafından yığılmış müşterilerin sayısını temsil eder.
- Tek bir müşteri sayısı, Y ekseninin negatif yönünde görüntülenir.
- Daha ayrıntılı görünümlerde görüntülenmek üzere belirli gösterge öğelerini seçebilirsiniz. Örneğin, yalnızca yeni müşterileri göstermek için göstergeden yeni müşteriler ' i seçin.
- X ekseninde sağa ve sola kaydırmak için grafiğin en üstündeki kaydırıcıyı kullanabilir ve daha ayrıntılı görüntülemek için belirli veri noktalarına odaklanırsınız.
- Grafik sütununun üzerine gelindiğinde yalnızca söz konusu ayın ayrıntıları görüntülenir.

![İş ortağı merkezi müşteri eğilimlerini çözümle](./media/analyze-customer-trends.png)

### <a name="customers-by-orders"></a>Siparişlere göre müşteriler

**Siparişlere göre müşteriler** grafiği, **en yüksek müşteri yüzdebirlik** değerlerini, siparişlerin sayısı tarafından belirlendiği şekilde x ekseni boyunca görüntüler. Y ekseni, müşterinin sıra sayısını görüntüler. Z ekseni (çizgi grafik) toplam sipariş sayısının kümülatif yüzdesini görüntüler. Çizgi grafik boyunca noktaların üzerine gelerek ayrıntıları gösterebilirsiniz.

Örnek olarak, bu müşteri siparişi verilerinden, müşterilerin %30 ' una ait siparişlerin %83 ' una eşit olduğunu ve 2.130 siparişe eşit olduğunu öğrenebilirsiniz.

![İş ortağı merkezi müşteri siparişlerini çözümle](./media/analyze-customer-orders.png)

### <a name="customer-details-table"></a>Müşteri Ayrıntıları tablosu

**Müşteri Ayrıntıları tablosu** , en önde gelen 1000 müşterinin, tekliflerinizin birini ilk almış oldukları tarihe göre sıralanan numaralandırılmış bir listesini görüntüler.

- Müşterinin kişisel bilgileri, yalnızca müşteri izin sağladıysa kullanılabilir. Bu bilgileri yalnızca **sahip** rol izinleri düzeyiyle oturum açtıysanız görüntüleyebilirsiniz. [Kullanıcı rolleri ve izinleri hakkında daha fazla bilgi edinin](./manage-account.md#define-user-roles-and-permissions).
- Kılavuzdaki her bir sütun sıralanabilir.
- Kayıt sayısı 1000 ' den küçükse veriler TSV dosyasına ayıklanabilir.
- 1000 üzerinden kayıt numarası varsa, verilen veriler sonraki 30 güne ait bir indirmeler sayfasına zaman uyumsuz olarak yerleştirilir.
- Filtreler, yalnızca ilgilendiğiniz verileri göstermek için tabloya uygulanabilir. Veriler şirket adı, müşteri KIMLIĞI, Market abonelik KIMLIĞI, Azure lisans türü, alınma tarihi, tarih kaybı, müşteri e-postası, müşteri ülkesi/eyalet/şehir/zip, müşteri dili vb. tarafından filtrelenebilir.

![İş ortağı merkezi müşteri ayrıntılarını çözümle](./media/analyze-customer-table.png)

## <a name="downloads-dashboard"></a>Karşıdan yüklemeler panosu

**Çözümle** menüsünün **indirmeler** panosu, müşteri veya sipariş verilerini 1000 ' den fazla satırı içeren indirmelere yönelik istekleri görüntüler.

1000 ' den fazla veri satırı içeren bir indirme isteğinde bulunduğunda, **indirmeler** panosuna bir bağlantı içeren bir açılır bildirim ve e-posta alacaksınız. Bu veri İndirmeleri 30 günlük bir dönemde kullanıma sunulacaktır ve sonra kaldırılır.

![İş Ortağı Merkezi İndirmeleri çözümle](./media/analyze-downloads.png)

### <a name="dictionary-of-data-terms"></a>Veri koşullarının sözlüğü

| Öznitelik adı | Raporlar | Tanım|
|---|---|---|
| Azure lisans türü | Müşteri, sipariş | Müşteriler tarafından Azure satın alma için kullanılan lisans sözleşmesinin türü. Kanal olarak da bilinen |
| Azure lisans türü: bulut çözümü sağlayıcısı | Müşteri, sipariş | Azure ve Market procures son müşterisi, bulut çözümü sağlayıcısı aracılığıyla satıcıya göre davranan bir tekliftir.|
| Azure lisans türü: Kurumsal | Müşteri, sipariş | Azure procures son müşterisi ve Market, doğrudan Microsoft ile imzalanmış bir Kurumsal Anlaşma aracılığıyla teklif.|
| Azure lisans türü: satıcı aracılığıyla kurumsal  | Müşteri, sipariş | Azure ve Market procures son müşterisi, Microsoft ile Kurumsal Anlaşma kolaylaştıran bir satıcı aracılığıyla teklif sunuyor.|  |
| Azure lisans türü: Kullandıkça öde| Müşteri, sipariş | Azure procures son müşterisi ve Market, doğrudan Microsoft ile imzalanmış bir "Kullandıkça öde" sözleşmesi aracılığıyla teklif sunuyor.||
| Bulut örneği adı| Sipariş verme| Bir VM dağıtımının gerçekleştiği Microsoft Bulut.||
| Bulut örneği adı: Azure genel| Sipariş verme| Genel genel Microsoft bulutu.|| |
| Bulut örneği adı: Azure Kamu | Sipariş verme| Aşağıdaki hükümetlerden biri için kamu 'ya özgü Microsoft bulutları: Çin, Almanya veya Amerika Birleşik Devletler.| |
| Müşteri şehri| Müşteri| Müşteri tarafından sunulan şehir adı. Şehir, müşterinin Azure aboneliğindeki şehirden farklı olabilir.||
| Müşteri Iletişim dili  | Müşteri| İletişim için müşterinin tercih ettiği dil.||
| Müşteri şirket adı | Müşteri, sipariş | Müşterinin sunduğu şirket adı. Ad, müşterinin Azure aboneliğindeki şehirden farklı olabilir.|  |
| Müşteri ülkesi | Müşteri, sipariş | Müşterinin sunduğu ülke adı. Ülke, müşterinin Azure aboneliğindeki ülkeden farklı olabilir.|  |
| Müşteri e-postası| Müşteri| Son müşteri tarafından sunulan e-posta adresi. E-posta, müşterinin Azure aboneliğindeki e-posta adresinden farklı olabilir.||
| Müşteri adı| Müşteri| Müşterinin verdiği ad. Ad, müşterinin Azure aboneliğinde belirtilen ad ile farklı olabilir.| |
| Müşteri KIMLIĞI | Müşteri, sipariş | Müşteriye atanan benzersiz tanımlayıcı. Müşterinin sıfır veya daha fazla Azure Market aboneliği olabilir.|  |
| Müşteri posta kodu  | Müşteri| Müşteri tarafından sunulan posta kodu. Kod, müşterinin Azure aboneliğinde girilen posta kodundan farklı olabilir.| |
| Müşteri durumu| Müşteri| Müşteri tarafından sunulan eyalet (adres). Durum, müşterinin Azure aboneliğinde belirtilen durumdan farklı olabilir.| |
| Alınma tarihi| Müşteri| Müşterinin sizin tarafınızdan yayınlanan teklifi satın aldığı ilk tarih.| |
| Kayıp tarihi| Müşteri| Müşterinin daha önce satın alınan tüm tekliflerden son iptal ettiği son tarih.||
| Yeni müşteri  | Sipariş verme| Bu değer, yeni bir müşteriyi, bir veya daha fazla teklifinizden ilk kez (veya değil) edinirsiniz. "Tarihi alındı" için aynı takvim ayı içinde değer "Evet" olacaktır. Müşteri tekliflerinizin herhangi birini bildirilen takvim ayına göre satın alıyorsa değer "Hayır" olacaktır. |
| Önizleme SKU 'SU| Sipariş verme| Bu değer, SKU 'YU "Önizleme" olarak etiketlediyseniz size bilgi verir. SKU buna uygun şekilde etiketlenmişse değer "Evet" olur ve yalnızca sizin tarafından yetkilendirilen Azure abonelikleri bu görüntüyü dağıtabilir ve kullanabilir. SKU "Önizleme" olarak tanımlanmamışsa, değer "Hayır" olacaktır.  |
| Promosyon ekibine katılım kabul ediyor| Müşteri| Bu değer, müşterinin yayımcılardan promosyon kişisini önceden kabul eder olduğunu bilmenizi sağlar. Şu anda, bu seçeneği müşterilere sunuyoruz, bu nedenle Pano genelinde "Hayır" belirtiyoruz. Bu özellik dağıtıldıktan sonra, güncelleştirmeye uygun olarak başlayacağız.|
| Market lisans türü| Sipariş verme| Market teklifinin faturalandırma yöntemi.||
| Market lisans türü: Azure üzerinden faturalandırılır| Sipariş verme| Microsoft bu Market 'e yönelik aracısıdır ve sizin adınıza müşteri reçeteleri sunun. (PAYG kredi kartı ya da kurumsal fatura)||
| Market lisans türü: kendi lisansınızı getirin | Sipariş verme| VM, dağıtım için müşterinin sunduğu bir lisans anahtarı gerektirir. Microsoft, tekliflerini Market aracılığıyla bu şekilde listelemek için müşterileri faturalandırmaz.||
| Market lisans türü: ücretsiz| Sipariş verme| Teklif, tüm kullanıcılar için ücretsiz olacak şekilde yapılandırılmıştır. Microsoft, müşterileri bu teklifin kullanımıyla ilgili olarak faturalandırmaz.||
| Market lisans türü: satıcı olarak Microsoft  | Sipariş verme| Microsoft bu Market teklifinin satıcısına yöneliktir.|  |
| Market abonelik KIMLIĞI | Müşteri, sipariş | Market teklifinizi satın almak için kullanılan müşterinin Azure aboneliğiyle ilişkili benzersiz tanımlayıcı. KIMLIĞI daha önce Azure aboneliği GUID 'iydi.||
| Teklif Adı  | Sipariş verme| Market sunumunun adı.|| |
| Teklif Türü  | Sipariş verme| Microsoft Market teklif türü.|||
| Teklif türü: yönetilen uygulama  | Siparişi | Aşağıdaki koşullar gerektiğinde Azure uygulaması: yönetilen uygulama teklifi türünü kullanın: bir VM veya tüm IaaS tabanlı bir çözümü kullanarak müşteriniz için abonelik tabanlı bir çözüm dağıtırsınız. Siz veya müşteriniz, çözümün bir iş ortağı tarafından yönetilmesini gerektirir. |
| Teklif türü: Azure uygulaması| Siparişi | Çözümünüz basit bir sanal makinenin ötesinde ek dağıtım ve yapılandırma Otomasyonu gerektirdiğinde Azure Uygulama çözümü şablonu teklif türünü kullanın.||
| Teklif türü: danışmanlık hizmeti| Sipariş verme| Azure Marketi 'ndeki Danışmanlık Hizmetleri, Azure 'un kullanımını desteklemek ve genişletmek için müşterilerin hizmetleriyle bağlantı sağlanmasına yardımcı olur.| |
| Teklif türü: kapsayıcı | Sipariş verme| Çözümünüz, Kubernetes tabanlı bir Azure Container Service olarak sağlanan bir Docker kapsayıcı görüntüsü olduğunda kapsayıcı teklif türünü kullanın.||
| Teklif türü: Dynamics 365 Iş Merkezi| Sipariş verme| Çözümünüz finans ve Işlemler için Dynamics 365 ile tümleştirildiğinde bu teklif türünü kullanın| |
| Teklif türü: müşteri katılımı için Dynamics 365 | Sipariş verme| Çözümünüz müşteri katılımı için Dynamics 365 ile tümleştirildiğinde bu teklif türünü kullanın.||
| Teklif türü: IoT Edge modülü | Sipariş verme| Azure IoT Edge modüller, IoT Edge tarafından yönetilen en düşük hesaplama birimleridir ve Microsoft hizmetlerini (Azure Stream Analytics), 3. taraf hizmetleri veya kendi çözümüne özgü kodunuzu içerebilir. |
| Teklif türü: Power BI uygulama | Sipariş verme| Power BI ile tümleştirilmiş bir uygulama dağıtırken Power BI uygulama teklifi türünü kullanın.|  |
| Teklif türü: SaaS uygulaması| Sipariş verme| Müşterinizin SaaS tabanlı, teknik çözümünüzü bir abonelik olarak satın almasını sağlamak için SaaS uygulaması teklif türünü kullanın.||
| Teklif türü: sanal makine | Sipariş verme| Müşterinizin ilişkili aboneliğine bir Sanal Gereç dağıtırken sanal makine teklif türünü kullanın.||
| Teklif türü: Visual Studio Market uzantısı  | Sipariş verme| Daha önce Azure DevOps uzantı geliştiricileri için sunulan teklif türü. Azure DevOps uzantısı geliştiricilerinin ileri, doğrudan müşterilere dahili olarak dahili olarak iletilmesini sağlayabilir. Uzantı teklifleri ücretli olarak veya bir deneme dahil olmak üzere yapılandırılabilir. |
| Sipariş Iptali tarihi| Sipariş verme| Market siparişinin iptal edildiği tarih.||
| Sipariş kimliği| Sipariş verme| Market hizmetiniz için müşteri siparişinin benzersiz tanımlayıcısı. Sanal makine kullanımı tabanlı teklifler bir siparişle ilişkili değildir.| |
| Sipariş satın alma tarihi| Sipariş verme| Market siparişinin oluşturulduğu tarih.|||
| Sipariş durumu| Sipariş verme| Verilerin son yenilenme sırasında Market siparişi durumu.|     |
| Sipariş durumu: etkin  | Sipariş verme| Müşterinin siparişi satın almış ve siparişi iptal edilmemiş.|         |
| Sipariş durumu: iptal edildi | Sipariş verme| Müşteri daha önce bir siparişi satın almış ve ardından sıralarını iptal etti.||
| Sağlayıcı e-postası| Müşteri| Sağlayıcının Microsoft ile son müşteri arasındaki ilişkiye dahil olan e-posta adresi. Müşteri, satıcı üzerinden bir kurumsal ise, bu satıcı olacaktır. Bir bulut çözümü sağlayıcısı (CSP) dahil edilmişse, bu CSP olacaktır.|
| Sağlayıcı adı| Müşteri| Microsoft ile son müşteri arasındaki ilişkiye katılan sağlayıcının adı. Müşteri, satıcı üzerinden bir kurumsal ise, bu satıcı olacaktır. Bir bulut çözümü sağlayıcısı (CSP) dahil edilmişse, bu CSP olacaktır.|
| SKU| Sipariş verme| Yayımlama sırasında tanımlanan SKU adı. Bir teklifin birçok SKU 'su olabilir, ancak bir SKU yalnızca tek bir teklifle ilişkilendirilebilir.||
| Deneme bitiş tarihi| Sipariş verme| Bu sipariş için deneme döneminin sona erdiği veya sonlandırmış olacağı tarih.||

## <a name="ratings-and-reviews"></a>Derecelendirmeler ve İncelemeler

Müşteriler her iki markette de teklifler sunarak, arama ve satın alma tekliflerinden elde ettikleri teklifler için derecelendirmeleri ve incelemeleri bırakabilir. Yeni derecelendirmeler veya incelemeler gönderebilirler, derecelendirmeleri veya zaten gönderdikleri İncelemeleri güncelleştirebilir veya silebilir. İncelemeler, teklifin ürün görüntüleme sayfasının Incelemeler sekmesinde Azure Market veya AppSource 'a gönderilir. Müşteriler, derecelendirmeleri veya incelemeleri için adlarını içerebilir veya anonim olarak posta oluşturabilir.  

> [!NOTE]
> Müşteriler yalnızca derecelendirmelere ve sahip oldukları gözden geçirmelere değişiklik yapabilirler.

**Derecelendirmeler & Gözden geçirme panosu** , müşteri etkinliklerinin eğilimlerini ve öngörülerini görüntüler. Grafik gösterimi şunları sağlar:

- Derecelendirmeler & değerlendirmeleri  
- Açıklamaları gözden geçir

### <a name="marketplace-storefront-tabs"></a>Market storefront sekmeleri

Yayımcılar, teklif ölçümlerini Azure Market sekmelerinde AppSource & Ayrıca görüntüleyebilir. Belirli teklif ölçümlerini görüntülemek için açılan listeden **teklif** ' i seçin:

![İş Ortağı Merkezi teklifi çözümle açılan kutusu](./media/analyze-offer-dropdown.png)

### <a name="ratings-and-reviews-summary"></a>Derecelendirmeler ve İncelemeler Özeti

Derecelendirmeler & gözden geçirmeler Özeti bölümünde, seçilen bir tarih aralığı için aşağıdaki ölçümler görüntülenir:

- **Ortalama derecelendirme:** Seçili teklif için müşteriler tarafından gönderilen tüm derecelendirmelerin ağırlıklı ortalama yıldız derecelendirmesi.
- **Derecelendirme dökümü:** Derecelendirmeyi gönderen müşterilerin sayısına göre yıldız derecelendirmesinin dökümü. Çubuk grafik gerçek ve düzeltilmiş derecelendirmeler (güncelleştirilmiş derecelendirme sayısı) ile yığılmış.
- **Toplam derecelendirme:** Gönderilen toplam derecelendirme sayısı. Bu sayı Ayrıca, gözden geçirmesiz ve olmayan derecelendirmeler içerir.
- **Gözden geçirmeler Içeren derecelendirmeler:** Gönderilen gözden geçirme sayısı.

![İş Ortağı Merkezi derecelendirmeleri ve incelemeleri İnceleme Özeti](./media/analyze-ratings-summary.png)

### <a name="review-comments"></a>Açıklamaları gözden geçir

İncelemeler, ne zaman nakledildiğini kronolojik sırada görüntülenir. Varsayılan görünüm tüm incelemeleri görüntüler. Açılır menüdeki **filtreyi** kullanarak, yıldıza göre gözden geçirmeler aracılığıyla filtre uygulayabilirsiniz. İnceleme içinde görüntülenen anahtar sözcükleri de arayabilirsiniz.  

![İş Ortağı Merkezi İnceleme açıklamalarını çözümle](./media/analyze-reviews.png)

## <a name="frequently-asked-questions"></a>Sık Sorulan Sorular

Bu bölümde, Iş Ortağı Merkezi 'nde **henüz analiz yok** iletisi bulunmayan sorular hakkında sık sorulan sorulara yanıtlar verilmektedir.

**Iş Ortağı Merkezi 'nde Analytics verilerimi görüntüleyemiyorum. Bu sayfalara erişirken aşağıdaki iletiyi görüyorum. Bunun nedeni nedir?**

![Henüz tekliflerinizi için veri yok](./media/analytics-faq-no-data.png)

Bu iletiyi neden alabilirsiniz:

- Market 'te listelenen tekliflerinizi SaaS teklifleri değildir. Şu anda Iş ortağı merkezi yalnızca SaaS teklifleri için analiz raporlar. Dynamics 365 teklifleri iş ortağı merkezi 'nde yayımlanabilir, ancak bu tekliflere ait analizler henüz iş ortağı merkezi 'nde etkin değildir.
- Market 'teki yayımlanmış tekliflerinizin Şu anda bir alım yok. Bu, tekliflerinizin Market 'te canlı olması ve ürün görüntüleme sayfalarındaki müşterilerden görünüm elde etmesinin yanı sıra müşterilerin satın alma ve dağıtma eylemi gerçekleştirmemiş olması anlamına gelir.
- Teklifinizin yayımlanması sürecinde olabilir ve henüz canlı değildir. Müşteriler tarafından yalnızca canlı tekliflerle elde edilebilir. Tekliflerinizin durumunu denetlemek için bkz. [analiz panosunda](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)genel bakış. Daha fazla bilgi için bu makaledeki [Özet Panosu](#summary-dashboard) ' na bakın.
- Tekliflerinizin yalnızca liste teklifleri olan ve Market 'teki müşteriler tarafından satın alınamamakta olan **benimle Iletişim kurmak**üzere listelenmiş olabilir. Bu teklif, müşteri adayları oluşturur ve sizinle paylaşılır, ancak satın alınamazlar, bu teklifler için Siparişler oluşturulmaz. Teklif listesi türünü denetlemek için kurulum sayfasına gidin.

**Analytics verilerine sahip olduğumu biliyorum, ancak aşağıdaki ileti görünür:**

![Belirtilen tarih aralığı için veri yok](./media/analytics-faq-data-range.png)

Bu iletiyi alıyorsanız, analiz verileriniz vardır ancak seçtiğiniz tarih aralığı için veri yok demektir. 2010 sonrasındaki verileri görüntülemek için farklı bir tarih aralığı veya özel tarih aralığı seçin. Daha fazla bilgi için bkz. [Tarih aralığı](#date-range).
