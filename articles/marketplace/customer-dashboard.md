---
title: Iş Ortağı Merkezi, Azure Marketi ve Microsoft AppSource Microsoft ticari Market Analytics 'teki müşteriler panosu
description: Ticari Market Analytics 'teki müşteriler panosunu kullanarak, büyüme eğilimleri dahil olmak üzere müşterilerinizle ilgili bilgilere erişmeyi öğrenin.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 3a26f7c63b639f9aeb6a8f526e77dacb37fb4d87
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106068073"
---
# <a name="customers-dashboard-in-commercial-marketplace-analytics"></a>Ticari Market Analytics 'te müşteriler panosu

Bu makalede, Iş Ortağı Merkezi 'ndeki müşteriler panosu hakkında bilgi sağlanmaktadır. Bu Pano, bir grafik ve indirilebilir biçimde sunulan büyüme eğilimleri dahil olmak üzere müşterilerinizle ilgili bilgileri görüntüler.

İş Ortağı Merkezi 'nde müşteriler panosuna erişmek için, **ticari Market** altında müşterileri **[Çözümle](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**' yi seçin  >  .

>[!NOTE]
> Analiz terminolojisi hakkında ayrıntılı tanımlamalar için bkz. [ticari Market Analytics terminolojisi ve sık sorulan sorular](./analytics-faq.md).

## <a name="customers-dashboard"></a>Müşteriler panosu

Müşteriler panosu, tekliflerinizi almış müşterilerin verilerini görüntüler. Aşağıdaki öğelerin grafik sunumlarını görüntüleyebilirsiniz:

- Etkin ve müşmış müşterilerin eğilimi
- Mevcut, yeni ve bunlarla donatılmış müşteriler dahil müşterinin büyüme eğilimi
- Siparişlere ve kullanıma göre müşteriler
- Müşteriler yüzdebirlik 
- Siparişlere ve kullanıma göre müşteri türü
- Coğrafya ile müşteriler
- Müşteriler Ayrıntılar tablosu
- Müşteriler sayfa filtreleri

> [!NOTE]
> Iş Ortağı Merkezi 'nde müşteri alımı ve raporlama arasındaki maksimum gecikme süresi 48 saattir.

## <a name="elements-of-the-customers-dashboard"></a>Müşteriler panosunun öğeleri

Aşağıdaki bölümlerde, müşteriler panosunun nasıl kullanılacağı ve verilerin nasıl okunacağı açıklanır.

### <a name="month-range"></a>Ay aralığı

Her sayfanın sağ üst köşesinde bir ay aralığı seçimi bulabilirsiniz. Son 6 veya 12 aya göre bir ay aralığı seçerek veya en fazla 12 ay süresince özel bir ay aralığı seçerek **müşteriler** sayfası grafiklerinin çıkışını özelleştirin. Varsayılan ay aralığı (hesaplama dönemi) altı aydır.

:::image type="content" source="./media/customer-dashboard/month-range-filters.png" alt-text="Müşteriler sayfasındaki ay filtrelerini gösterir.":::

> [!NOTE]
> Görselleştirme pencere öğeleri ve dışarı aktarma raporlarında tüm ölçümler, Kullanıcı tarafından seçilen hesaplama dönemini kabul ediyor.

### <a name="active-and-churned-customers-trend"></a>Etkin ve müşmış müşterilerin eğilimi

Bu bölümde, müşterilerinizin seçili hesaplama dönemi için büyüme eğilimi bulacaksınız. Ölçümler ve büyüme eğilimleri bir çizgi grafik tarafından temsil edilir ve grafikteki satırın üzerine gelindiğinde her ay için değeri görüntüler. Pencere öğesinde **etkin müşterilerin** altındaki yüzde değeri, seçilen hesaplama dönemi boyunca büyüme miktarını temsil eder. Örneğin, aşağıdaki ekran görüntüsünde seçili hesaplama dönemi için% 0,92 oranında bir büyüme gösterilmektedir.

[![Müşteriler sayfasındaki müşteriler pencere öğesini gösterir.](./media/customer-dashboard/customers-widget.png)](./media/customer-dashboard/customers-widget.png#lightbox)

Üç _Müşteri türü_ vardır: yeni, var ve bir şekilde.

- Yeni bir müşteri, seçili ay içinde ilk kez bir veya daha fazla teklifinizden elde etti.
- Mevcut bir müşteri, seçili olan aydan önce bir veya daha fazla teklifinizden elde etti.
- Oluşturulmuş bir müşteri, daha önce satın alınan tüm teklifleri iptal etti. Uyumsuz müşteriler eğilim pencere öğesinde negatif eksende temsil edilir.

### <a name="customer-growth-trend-including-existing-new-and-churned-customers"></a>Mevcut, yeni ve bunlarla donatılmış müşteriler dahil müşterinin büyüme eğilimi

Bu bölümde, yeni, mevcut ve tamamen dahil olmak üzere tüm müşterilerin bir aylık ay büyüme eğilimi ile eğilim ve sayısını bulacaksınız.

- Çizgi grafik, genel müşteri büyüme yüzdelerini temsil eder.
- Month sütunu, yeni, var olan ve tarafından kullanılan müşteriler tarafından yığılmış müşterilerin sayısını temsil eder.
- Tek başına müşteri sayısı X ekseninin negatif yönünde görüntülenir.
- Daha ayrıntılı görünümleri göstermek için belirli gösterge öğeleri seçebilirsiniz. Örneğin, yalnızca yeni müşterileri göstermek için göstergedeki yeni müşteriler ' i seçin.
- Grafikteki bir sütunun üzerine gelindiğinde yalnızca söz konusu aya ait ayrıntılar gösterilir.

[![Müşteriler sayfasındaki müşteriler eğilimi pencere öğesini gösterir.](./media/customer-dashboard/customers-trend.png)](./media/customer-dashboard/customers-trend.png#lightbox)

### <a name="customers-by-ordersusage"></a>Siparişlere/kullanıma göre müşteriler

**Siparişler/kullanım grafiğinde müşteriler** üç sekmeye sahiptir: siparişler, normalleştirilmiş kullanım ve ham kullanım. Sipariş ayrıntılarını göstermek için **siparişler** sekmesini seçin.

:::image type="content" source="./media/customer-dashboard/customers-by-orders-usage.png" alt-text="Müşteriler sayfasında siparişler ve kullanım pencere öğesi ' nin Siparişler sekmesini gösterir.":::

Şunlara dikkat edin:

- Öncü tahta, sipariş sayısına göre derecelendirilen müşterilerin ayrıntılarını sunar. Müşteri seçildikten sonra, müşteri ayrıntıları bitişik "Ayrıntılar", "SKU 'Lara göre siparişler" ve "koltuk tarafından STB 'ler" bölümlerine sunulur.
- Yayımcılar bir sahip rolüyle oturum açmışsa, bu alanda müşteri profili ayrıntıları görüntülenir. Yayımcılar bir katkıda bulunan rolüyle oturum açmışsa, bu bölümdeki Ayrıntılar kullanılabilir olmayacaktır.
- **SKU 'lara göre siparişler** halka grafiği, planlar için satın alınan siparişlerin dökümünü görüntüler. En yüksek sıra sayısı olan ilk beş plan görüntülenir, ancak siparişlerin geri kalanı **rest tümü** altında gruplandırılır.
- Koltuk için bir veya daha fazla halka grafiği olan **SKU 'lar** , planlar için sıralanmış bir koltuk dökümünü görüntüler En yüksek bilgisayar sayısı en fazla olan beş plan görüntülenir, ancak siparişlerin geri kalanı **kalan tüm** altında gruplandırılır.

Kullanım ayrıntılarını görüntülemek için **normalleştirilmiş kullanım** veya **Ham kullanım** sekmesini de seçebilirsiniz.

- Öncü Pano, kullanım saatleri sayısına göre derecelendirilen müşterilerin ayrıntılarını sunar. Müşteri seçildikten sonra müşterinin ayrıntıları bitişik "Ayrıntılar", "tekliflere göre normalleştirilmiş kullanım" ve "sanal makine (VM) boyutu tarafından normalleştirilmiş kullanım" bölümünde gösterilir.
- Yayımcılar bir sahip rolüyle oturum açtıklarında, bu alanda müşteri profili ayrıntıları görüntülenir. Yayımcılar bir katkıda bulunan rolüyle oturum açtıysa, bu bölümdeki Ayrıntılar kullanılabilir olmayacaktır.
- Halka grafik **teklifleri tarafından normalleştirilmiş kullanım** , teklifler tarafından tüketilen kullanım dökümünü görüntüler. En yüksek kullanım sayısı olan ilk beş plan görüntülenir, ancak tekliflerin geri kalanı **rest tümü** altında gruplandırılır.
- **VM boyut (ler) halka grafiğinin normalleştirilmiş kullanımı** , farklı VM boyutları tarafından tüketilen kullanım dökümünü görüntüler. En yüksek normalleştirilmiş kullanımı olan en iyi beş VM boyutu görüntülenir, ancak kullanımın geri kalanı **rest All** altında gruplandırılır.

### <a name="top-customers-percentile"></a>Popüler müşteriler yüzdebirlik

**En popüler müşteriler yüzdebirlik** grafiğinde üç sekme bulunur, "Siparişler", "normalleştirilmiş kullanım" ve "ham kullanım". _En üstteki müşteri yüzdebirlik_ değeri, sipariş sayısı tarafından belirlendiği şekilde x ekseni boyunca görüntülenir. Y ekseni, müşterinin sıra sayısını görüntüler. İkincil y ekseni (çizgi grafik), toplam sipariş sayısının birikmiş yüzdesini görüntüler. Çizgi grafik boyunca noktaların üzerine gelerek ayrıntıları gösterebilirsiniz.

[![Müşteriler sayfasındaki üst müşteri yüzdebirlik pencere öğesinin Siparişler sekmesini gösterir.](./media/customer-dashboard/top-customer-percentile.png)](./media/customer-dashboard/top-customer-percentile.png#lightbox)

### <a name="customer-type-by-orders-and-usage"></a>Siparişlere ve kullanıma göre müşteri türü

**Müşteri türüne göre siparişler/kullanım** grafiğinde, yeni müşteriler ve mevcut müşteriler arasında bölünen sipariş sayısı, normalleştirilmiş kullanım ve ham kullanım saatleri gösterilir; grafikte sırasıyla siparişlerin, normalleştirilmiş ve ham kullanımın seçimine bağlı olarak.

Bu grafik şunları gösterir:

- Yeni bir müşteri, bir veya daha fazla teklif veya bildirilen kullanımınızdan aynı takvim ayı (y ekseni) içinde ilk kez edinildi.
- Mevcut bir müşteri daha önce sizin veya bildirilen takvim ayına (y ekseninde) önce bir teklif almış veya rapor verdi.

[![Müşteriler sayfasındaki müşteri türüne göre siparişler pencere öğesi ' nin Siparişler sekmesini gösterir.](./media/customer-dashboard/orders-by-customer-type.png)](./media/customer-dashboard/orders-by-customer-type.png#lightbox)

### <a name="customers-by-geography"></a>Coğrafya ile müşteriler

Seçilen hesaplama dönemi için ısı haritasını, toplam müşteri sayısını ve yeni eklenen müşterilerin Coğrafya boyutuna karşı yüzdesini görüntüler. Haritadaki koyu renkli bir renk, müşteri sayısının yüksek değerini gösterir. Bir ülkede veya bölgede yakınlaştırmak için tablodaki bir kaydı seçin.

[![Müşteriler sayfasında Coğrafya tarafından siparişler pencere öğesinin Siparişler sekmesini gösterir.](./media/customer-dashboard/customers-by-geography.png)](./media/customer-dashboard/customers-by-geography.png#lightbox)

Şunlara dikkat edin:

- Tam konumu görüntülemek için Haritayı taşıyabilirsiniz.
- Belirli bir konuma yakınlaştırabilirsiniz.
- Isı haritasını 'in, belirli konumdaki müşteri sayısı, sipariş sayısı, normalleştirilmiş kullanım saatlerinin ayrıntılarını görüntülemek için bir ek kılavuz vardır.
- Haritada yer alan bir ülke/bölge arayabilir ve seçebilirsiniz. Haritadaki **giriş** düğmesini seçerek özgün görünüme dönün.

### <a name="customer-details-table"></a>Müşteri Ayrıntıları tablosu

**Müşteri ayrıntıları** tablosu, en önde gelen 1.000 müşterinin, tekliflerinizin birini ilk almış oldukları tarihe göre sıralanan numaralandırılmış bir listesini görüntüler. Ayrıntılar şeridinde genişletme simgesini seçerek bir bölümü genişletebilirsiniz.

Şunlara dikkat edin:

- Müşterinin kişisel bilgileri, yalnızca müşteri izin sağladıysa kullanılabilir. Bu bilgileri yalnızca sahip rol izinleri düzeyiyle oturum açtıysanız görüntüleyebilirsiniz.
- Kılavuzdaki her bir sütun sıralanabilir.
- Veriler bir öğesine ayıklanabilir. CSV veya. TSV dosyası 1.000 ' den küçükse, kayıt sayısı.
- Kayıt numarası 1.000 ' den büyükse, verilen veriler sonraki 30 güne ait bir indirmeler sayfasına zaman uyumsuz olarak yerleştirilir.
- Yalnızca ilgilendiğiniz verileri göstermek için tabloya filtre uygulayın. Verileri şirket adı, müşteri KIMLIĞI, Market abonelik KIMLIĞI, Azure lisans türü, alınma tarihi, tarih kaybı, müşteri e-postası, müşteri ülkesi/bölgesi/eyalet/şehir/posta, müşteri dili vb. olarak filtreleyin.
- Bir teklif, korunan bir müşteri tarafından satın alındığında, **müşterinin ayrıntılı verilerinde** bilgiler maskelenecek (* * * * * * * * * * * *).
- Şirket adı, müşteri adı ve müşteri e-postası gibi müşteri boyut ayrıntıları, Azure Marketi veya Microsoft AppSource işlem düzeyinde değil bir kuruluş KIMLIĞI düzeyidir.

_**Tablo 1: veri koşullarının sözlüğü**_

| İçindeki sütun adı<br>kullanıcı arabirimi | Öznitelik adı | Tanım | Programlı olarak sütun adı<br>erişim raporları |
| ------------ | ------------- | ------------- | ------------- |
| Market abonelik KIMLIĞI | Market abonelik KIMLIĞI | Ticari Market teklifinizi satın almak için kullanılan müşterinin Azure aboneliğiyle ilişkili benzersiz tanımlayıcı. Altyapı teklifleri için bu müşterinin Azure abonelik GUID 'sidir. SaaS tekliflerini bir Azure aboneliği gerektirmediğinden, SaaS teklifleri için bu sıfır olarak gösterilir. | Pazar Placesubscriptionıd |
| Tarih alındı | Alınma tarihi | Müşterinin yayımladığınız teklifi satın aldığı ilk tarih. | Tarih alındı |
| Tarih kayıp | Kayıp tarihi | Müşterinin önceden satın alınan tüm tekliflerden en son iptal edilme tarihi. | Tarih kayıp |
| Sağlayıcı Adı | Sağlayıcı Adı | Microsoft ile müşteri arasındaki ilişkide yer alan sağlayıcının adı. Müşteri, satıcı üzerinden bir kurumsal ise, bu satıcı olacaktır. Bir bulut çözümü sağlayıcısı (CSP) dahil edilmişse, bu CSP olacaktır. | Adı |
| Sağlayıcı e-postası | Sağlayıcı e-postası | Sağlayıcının, Microsoft ile müşteri arasındaki ilişkiye dahil olan e-posta adresi. Müşteri, satıcı üzerinden bir kurumsal ise, bu satıcı olacaktır. Bir bulut çözümü sağlayıcısı (CSP) dahil edilmişse, bu CSP olacaktır. | ProviderEmail |
| FirstName | Müşteri adı | Müşterinin ilk adı. Ad, müşterinin Azure aboneliğinde belirtilen ad ile farklı olabilir. | FirstName |
| LastName | Müşterinin soyadını soyadı | Müşterinin sunduğu soyadı. Ad, müşterinin Azure aboneliğinde belirtilen ad ile farklı olabilir. | LastName |
| E-posta | Müşteri e-postası | Son müşteri tarafından sunulan e-posta adresi. E-posta, müşterinin Azure aboneliğindeki e-posta adresinden farklı olabilir. | E-posta |
| Müşteri şirket adı | Müşteri şirket adı | Müşterinin sunduğu şirket adı. Ad, müşterinin Azure aboneliğindeki şehirden farklı olabilir. | CustomerCompany adı |
| CustomerCity | Müşteri şehri | Müşteri tarafından sunulan şehir adı. Şehir, müşterinin Azure aboneliğindeki şehirden farklı olabilir. | CustomerCity |
| Müşteri posta kodu | Müşteri posta kodu | Müşteri tarafından sunulan posta kodu. Kod, müşterinin Azure aboneliğinde girilen posta kodundan farklı olabilir. | Customerposta kodu |
| CustomerCommunicationCulture | Müşteri Iletişim dili | İletişim için müşterinin tercih ettiği dil. | CustomerCommunicationCulture |
| CustomerCountryRegion | Müşteri ülkesi/bölgesi | Müşterinin sunduğu ülke/bölge adı. Ülke/bölge, müşterinin Azure aboneliğindeki ülke/bölgeden farklı olabilir. | CustomerCountryRegion |
| AzureLicenseType | Azure lisans türü | Müşteriler tarafından Azure satın alma için kullanılan lisans sözleşmesinin türü. _Kanal_ olarak da bilinir. Olası değerler şunlardır:<ul><li>Bulut çözümü sağlayıcısı</li><li>Kurumsal</li><li>Satıcı aracılığıyla kurumsal</li><li>Kullandıkça öde</li></ul> | AzureLicenseType |
| Promotionalmüşterileri | Promosyon ekibine katılım kabul ediyor | Bu değer, müşterinin yayımcılardan promosyon kişisini önceden kabul eder olduğunu bilmenizi sağlar. Şu anda, bu seçeneği müşterilere sunuyoruz, bu nedenle Pano genelinde "Hayır" belirtiyoruz. Bu özellik dağıtıldıktan sonra, güncelleştirmeye uygun olarak başlayacağız. | Promotionalmüşterileri |
| CustomerState | Müşteri durumu | Müşterinin verdiği durum. Durum, müşterinin Azure aboneliğinde belirtilen durumdan farklı olabilir. | CustomerState |
| Ticari sertifika kimliği müşterisi | Ticaret kök müşterisi | Bir faturalandırma hesabı KIMLIĞI, birden fazla müşteri kimliğiyle ilişkilendirilebilir.<br>Bir faturalandırma hesabı KIMLIĞI ve müşteri KIMLIĞI birleşimi, birden çok ticari Market abonelikleriyle ilişkilendirilebilir.<br>Commerce root müşterisi, abonelik müşterisinin adını belirtir. | Ticari sertifika kimliği müşterisi |
| Müşteri Kimliği | Müşteri Kimliği | Müşteriye atanan benzersiz tanımlayıcı. Müşterinin sıfır veya daha fazla Azure Market aboneliği olabilir. | CustomerId |
| Faturalandırma hesabı KIMLIĞI | Faturalandırma hesabı KIMLIĞI | Faturalandırma 'nin oluşturulduğu hesabın tanımlayıcısı. Ödeme Işlem raporunuzu müşteri, sipariş ve kullanım raporlarıyla bağlamak için **faturalandırma HESABı kimliğini** **MüşteriNo** olarak eşleyin. | Billingaccountıd |
| Müşteri Türü | Müşteri Türü | Bu alanın değeri, müşterinin türünü belirtir. Olası değerler şunlardır:<ul><li>ye</li> <li>Kuruluşunuzun</li></ul> | CustomerType |
|||||

### <a name="customers-page-filters"></a>Müşteriler sayfa filtreleri

Müşteriler sayfası filtreleri, müşteriler sayfa düzeyinde uygulanır. Grafik oluşturmak için seçtiğiniz ölçütlere ve "ayrıntılı siparişler verileri" kılavuzunda/dışarı aktarmada görmek istediğiniz verileri görüntülemek için birden çok filtre seçebilirsiniz. Filtreler, müşteriler sayfasının sağ üst köşesinde seçtiğiniz ay aralığı için ayıklanan verilere uygulanır.

> [!TIP]
> Verileri indirmek için herhangi bir pencere öğesinin sağ üst köşesinde bulunan İndir simgesini kullanabilirsiniz. "Thumbs up" veya "thumbs aşağı" simgesine tıklayarak Pencere öğelerinin her biri hakkında geri bildirim sağlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- Ticari Market 'te bulunan analiz raporlarına genel bakış için bkz. [Partner Center 'da ticari Market için analitik raporlara erişme](./partner-center-portal/analytics.md).
- Teklifinizin Market etkinliklerini özetleyen grafikler, eğilimler ve veri değerleri için bkz. [ticari Market Analytics 'Te Özet Panosu](./summary-dashboard.md).
- Grafik ve indirilebilir biçimdeki Siparişleriniz hakkında daha fazla bilgi için bkz. [Orders Dashboard in The Commercial Market Analytics](./orders-dashboard.md).
- Sanal makine (VM) için kullanım ve ölçümlenen faturalandırma ölçümleri, bkz. [ticari Market Analytics 'Te kullanım panosu](./usage-dashboard.md).
- Son 30 güne ait indirme isteklerinizin listesi için bkz. [Commercial Market Analytics 'te panoyu indirme](./partner-center-portal/downloads-dashboard.md).
- Azure Marketi ve Microsoft AppSource tekliflere yönelik müşteri geri bildirimlerinin birleştirilmiş bir görünümünü görmek için bkz. [Iş Ortağı Merkezi 'Nde derecelendirme & gözden geçirme Analizi panosu](./partner-center-portal/ratings-reviews.md).
- Ticari Market Analytics hakkında sık sorulan sorular ve veri koşullarının kapsamlı bir sözlüğü için bkz. [ticari Market Analytics terminolojisi ve sık sorulan sorular](./analytics-faq.md).
