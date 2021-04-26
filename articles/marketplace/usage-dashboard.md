---
title: Ticari Market Analytics 'te kullanım panosu | Azure Marketi
description: Azure Marketi 'Nde yayınlanan tekliflere yönelik tüm kullanım ve ölçümlü faturalandırma ölçümlerine nasıl erişebileceğinizi öğrenin.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: 765f4f358d593cd5fcc021fbf4ec2276c422c4d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555134"
---
# <a name="usage-dashboard-in-commercial-marketplace-analytics"></a>Ticari market analizinde Kullanım panosu

Bu makalede, Iş Ortağı Merkezi 'ndeki kullanım panosu hakkında bilgi sağlanır. Bu Pano tüm sanal makineleri (VM) üç ayrı sekmede normalleştirilmiş kullanım, ham kullanım ve ölçümlü faturalandırma ölçümleri sunar: VM normalleştirilmiş kullanımı, VM ham kullanımı ve tarifeli faturalandırma kullanımı.

İş Ortağı Merkezi 'ndeki kullanım panosuna erişmek için, **ticari Market** altında kullanımı **[Çözümle](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**' yi seçin  >  .

>[!NOTE]
> Analiz terminolojisi hakkında ayrıntılı tanımlamalar için bkz. [ticari Market Analytics terminolojisi ve sık sorulan sorular](./analytics-faq.md).

## <a name="usage-dashboard"></a>Kullanım panosu

**Çözümle** menüsündeki **kullanım** panosu, tüm hizmet olarak yazılım (SaaS) teklifleriniz için geçerli siparişleri görüntüler. Aşağıdaki öğelerin grafik sunumlarını görüntüleyebilirsiniz:

- Kullanım eğilimi
- Tekliflere göre normalleştirilmiş kullanım
- Diğer boyutlara göre normalleştirilmiş kullanım: VM boyutu, satış kanalları ve teklif türleri
- Coğrafya tarafından kullanım
- Ayrıntılı kullanım tablosu
- Sipariş sayfası filtreleri

> [!NOTE]
> Iş Ortağı Merkezi 'nde kullanım olayı oluşturma ve raporlama arasındaki en uzun gecikme süresi 48 saattir.

## <a name="elements-of-the-usage-dashboard"></a>Kullanım panosunun öğeleri

Aşağıdaki bölümlerde kullanım panosunun kullanımı ve verilerin nasıl okunacağı açıklanır.

### <a name="month-range"></a>Ay aralığı

Her sayfanın sağ üst köşesinde bir ay aralığı seçimi bulabilirsiniz. Son 6 veya 12 aya göre bir ay aralığı seçerek veya en fazla 12 ay süresince özel bir ay aralığı seçerek **kullanım** sayfası grafiklerinin çıkışını özelleştirin. Varsayılan ay aralığı (hesaplama dönemi) altı aydır.

:::image type="content" source="./media/usage-dashboard/month-filter.png" alt-text="Kullanım panosundaki ay filtrelerini gösterir.":::

### <a name="usage-trend"></a>Kullanım eğilimi

Bu bölümde, seçili hesaplama dönemi boyunca müşterileriniz tarafından tüketilen tüm teklifleriniz için toplam kullanım saati ve eğilimi bulacaksınız. Ölçümler ve büyüme eğilimleri çizgi grafik tarafından temsil edilir. Grafikteki satırın üzerine gelindiğinde her ayın değerini gösterir. Pencere öğesinde kullanım ölçümlerinin altındaki yüzde değeri, seçilen hesaplama döneminde büyüme veya reddetme miktarını temsil eder.

Kullanım saatlerinin iki temsili vardır: VM normalleştirilmiş kullanımı ve VM RAW kullanımı.

- Normalleştirilmiş kullanım saatleri, sanal makine çekirdekleri ([VM çekirdeği sayısı] x [saat ham kullanım]) için hesaba normalleştirilmiş kullanım saatleri olarak tanımlanır. "SHAREDCORE" olarak belirlenen VM 'Ler [VM çekirdeklerinin sayısı] çarpanı olarak 1/6 (veya 0,1666) kullanır.
- Ham kullanım saatleri, VM 'Lerin saat bakımından çalıştırıldığı süre miktarı olarak tanımlanır.

[![Kullanım panosundaki normalleştirilmiş kullanımı ve ham kullanım verilerini gösterir.](./media/usage-dashboard/normalized-usage.png)](./media/usage-dashboard/normalized-usage.png#lightbox)

### <a name="normalized-usage-by-offers"></a>Tekliflere göre normalleştirilmiş kullanım

Bu bölümde, Azure Marketi 'ndeki kullanım tabanlı teklifleriniz için toplam kullanım saatleri ve eğilimi sağlanmaktadır. Tekliflere göre normalleştirilmiş kullanım grafiği aşağıda açıklanmıştır.

- Yığılmış sütun grafiği **sunan tarafından normalleştirilmiş kullanım** , seçilen hesaplama dönemine göre ilk beş teklif için normalleştirilmiş kullanım saatlerinin dökümünü görüntüler. İlk beş teklif bir grafikte görüntülenir, diğerleri **rest All** kategorisinde gruplandırılır.
- Yığılmış sütun grafiği, seçilen tarih aralığı için aylık bir büyüme eğilimi gösterir. Month sütunları, ilgili ay için en yüksek kullanım saatlerine sahip tekliflerden kullanım saatlerini temsil eder. Çizgi grafik, ikincil Y ekseni üzerinde çizilen büyüme yüzdesi eğilimini gösterir.
- Yalnızca grafikteki teklifleri göstermek için göstergede belirli teklifleri seçebilirsiniz.

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers.png" alt-text="Kullanım panosundaki verileri gösteren normalleştirilmiş kullanımı gösterir.":::

Teklifin ve seçilen SKU 'Ların aylık kullanım eğilimini görüntülemek için teklif ve en fazla üç SKU 'yu seçebilirsiniz.

:::image type="content" source="./media/usage-dashboard/normalized-usage-offers-sku.png" alt-text="Kullanım panosundaki normalleştirilmiş kullanım tekliflerini ve SKU verilerini gösterir.":::

### <a name="orders-by-offers-and-skus"></a>Tekliflere ve SKU 'Lara göre siparişler

**Tekliflere ve SKU şemasına göre siparişler** , tüm tekliflerin ölçülerini ve eğilimlerini gösterir. Şunlara dikkat edin:

- En üstteki teklifler grafikte görüntülenir ve tekliflerin geri kalanı **rest** olarak gruplandırılır.
- Yalnızca grafikteki teklifleri göstermek için göstergede belirli teklifleri seçebilirsiniz.
- Grafikteki bir dilimin üzerine gelindiğinde, tüm tekliflerde toplam sipariş sayısı ile karşılaştırıldığında, bu teklifin sipariş sayısı ve yüzdesi görüntülenir.
- **Siparişlere göre siparişler eğilimi** , aylık büyüme eğilimlerini görüntüler. Month sütunu, teklif adına göre sipariş sayısını temsil eder. Çizgi grafik, z ekseni üzerinde çizilen büyüme yüzdesi eğilimini görüntüler.

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus.png" alt-text="Kullanım panosundaki teklifleri ve SKU 'Ları grafik olarak gösterir.":::

Teklif, SKU 'Lar ve koltuk için aylık aylık eğilimi görüntülemek üzere teklif ve en fazla üç SKU 'yu seçebilirsiniz.

:::image type="content" source="./media/usage-dashboard/orders-by-offers-and-skus-2.png" alt-text="Kullanım panosundaki teklifleri ve SKU 'Ları grafik olarak gösterir. Teklif eğilimi, SKU eğilimi ve koltuk eğilimi gösterilmektedir.":::

#### <a name="normalized-usage-by-other-dimensions-vm-size-sales-channels-and-offer-type"></a>Diğer boyutlara göre normalleştirilmiş kullanım: VM boyutu, satış kanalları ve teklif türü

Boyutlar için üç sekme vardır: VM boyutu, satış kanalları ve teklif türü. Kullanım ölçümlerini ve bu boyutlarda her bir ay boyunca aylık eğilimi görebilirsiniz.

:::image type="content" source="./media/usage-dashboard/normalized-usage-other-dimensions.png" alt-text="Kullanım panosu üzerindeki normalleştirilmiş kullanım diğer boyutlar grafiğini gösterir.":::

### <a name="usage-by-geography"></a>Coğrafya tarafından kullanım

Seçilen hesaplama dönemi için ısı haritasını, coğrafi boyutun toplam kullanımını görüntüler. Haritadaki koyu renkli bir renk, müşteri sayısının yüksek değerini gösterir. Bir ülkede/bölgede yakınlaştırmak için tablodaki bir kaydı seçin.

:::image type="content" source="./media/usage-dashboard/normalized-usage-country.png" alt-text="Kullanım panosundaki normalleştirilmiş kullanım ülkesi grafiğini gösterir.":::

Şunlara dikkat edin:

- Tam konumu görüntülemek için Haritayı taşıyabilirsiniz.
- Belirli bir konuma yakınlaştırabilirsiniz.
- Isı haritasını 'in, belirli konumdaki müşteri sayısı, sipariş sayısı ve normalleştirilmiş kullanım saatlerinin ayrıntılarını görüntülemek için bir ek kılavuz vardır.
- Haritada yer alan bir ülke/bölge arayabilir ve seçebilirsiniz. Haritadaki **giriş** düğmesini seçerek özgün görünüme dönün.

### <a name="usage-details-table"></a>Kullanım Ayrıntıları tablosu

**Kullanım ayrıntıları** tablosu, kullanıma göre sıralanan ilk 1.000 kullanım kaydının numaralandırılmış bir listesini görüntüler. Şunlara dikkat edin:

- Kılavuzdaki her bir sütun sıralanabilir.
- Veriler bir öğesine ayıklanabilir. TSV veya. Kayıt sayısı 1.000 ' den küçükse CSV dosyası.
- Kayıt sayısı 1.000 ' den fazla ise, dışarı aktarma verileri, sonraki 30 gün boyunca kullanılabilecek bir indirmeler sayfasına zaman uyumsuz olarak yerleştirilir.
- Yalnızca ilgilendiğiniz verileri göstermek için **ayrıntılı kullanım verilerine** filtre uygulayın. Verileri ülkeye/bölgeye, Satış kanalına, Market lisans türüne, kullanım türüne, teklif adına, teklif türüne, ücretsiz deneme, Market abonelik KIMLIĞI, müşteri KIMLIĞI ve şirket adına göre filtreleyin.

_**Tablo 1: veri koşullarının sözlüğü**_

| İçindeki sütun adı<br>kullanıcı arabirimi | Öznitelik adı | Tanım | Programlı olarak sütun adı<br>erişim raporları |
| ------------ | ------------- | ------------- | ------------- |
| Market abonelik KIMLIĞI | Market abonelik KIMLIĞI | Ticari Market teklifinizi satın almak için kullanılan müşterinin Azure aboneliğiyle ilişkili benzersiz tanımlayıcı. KIMLIĞI daha önce Azure aboneliği GUID 'iydi. | Pazar Placesubscriptionıd |
| MonthStartDate | Ay başlangıç tarihi | Ayın başlangıç tarihi, satın alma ayını temsil eder. | MonthStartDate |
| Teklif Türü | Teklif Türü | Ticari Market teklifi türü. | OfferType |
| Azure lisans türü | Azure lisans türü | Müşteriler tarafından Azure satın alma için kullanılan lisans sözleşmesinin türü. Kanal olarak da bilinir. Olası değerler şunlardır:<ui><li>Bulut çözümü sağlayıcısı</li><li>Kurumsal</li><li>Satıcı aracılığıyla kurumsal</li><li>Kullandıkça öde</li></ul> | AzureLicenseType |
| Market lisans türü | Market lisans türü | Ticari Market teklifinin faturalandırma yöntemi. Olası değerler şunlardır:<ul><li>Azure üzerinden faturalandırılır</li><li>Kendi lisansınızı getirin</li><li>Ücretsiz</li><li>Satıcı olarak Microsoft</li></ul> | MarketplaceLicenseType |
| SKU | SKU | Teklifle ilişkili plan. | SKU |
| Müşteri ülkesi | Müşteri ülkesi/bölgesi | Müşterinin sunduğu ülke/bölge adı. Ülke/bölge, müşterinin Azure aboneliğindeki ülke/bölgeden farklı olabilir. | CustomerCountry |
| Önizleme SKU 'SU | Önizleme SKU 'SU | Değer, SKU 'YU "Önizleme" olarak etiketlediyseniz görüntülenir. SKU buna uygun şekilde etiketlenmişse değer "Evet" olur ve yalnızca sizin tarafından yetkilendirilen Azure abonelikleri bu görüntüyü dağıtabilir ve kullanabilir. SKU "Önizleme" olarak tanımlanmamışsa, değer "Hayır" olacaktır. | Isönizleme SKU 'su |
| SKU faturalandırma türü | SKU faturalandırma türü | Teklifteki her SKU ile ilişkili faturalandırma türü. Olası değerler şunlardır:<ul><li>Ücretsiz</li><li>Ücretli</li></ul> | SKUBillingType |
| IsInternal | Kullanım Dışı | Kullanım Dışı | Kullanım Dışı |
| VM Boyutu | Sanal makine boyutu | VM tabanlı teklif türleri için bu varlık, teklifin SKU 'SU ile ilişkili VM 'nin boyutunu belirtir. | VMSize |
| Bulut örneği adı | Bulut örneği adı | Bir VM dağıtımının gerçekleştiği Microsoft Bulut. | Cloudınstancename |
| ServicePlanName | Kullanım Dışı | Kullanım dışı (SKU ile aynı tanım) | ServicePlanName |
| Teklif Adı | Teklif Adı | Ticari Market sunumunun adı. | OfferName |
| DeploymentMethod | Kullanım Dışı | Kullanım dışı (teklif türü olarak aynı tanım) | DeploymentMethod |
| Müşteri şirket adı | Müşteri şirket adı | Müşterinin sunduğu şirket adı. Ad, müşterinin Azure aboneliğindeki adından farklı olabilir. | CustomerCompanyName |
| Kullanım Tarihi | Kullanım Tarihi | Kullanım tabanlı varlıklar için kullanım olayı oluşturma tarihi. | UsageDate |
| Imultisolution | Çok çözüm | Teklifin çok çözüm teklif türü olup olmadığını belirtir. | Imultisolution |
| Yeni müşteri | Kullanım Dışı | Kullanım Dışı | IsNewCustomer |
| Çekirdek boyutu | Çekirdek boyutu | VM tabanlı teklifle ilişkili çekirdek sayısı. | CoreSize |
| Kullanım Türü | Kullanım Türü | Teklifle ilişkili kullanım olayının aşağıdakilerden biri olup olmadığını belirtir:<ul><li>Normalleştirilmiş kullanım</li><li>Ham kullanım</li><li>Tarifeli kullanım</li></ul> | UsageType |
| Deneme bitiş tarihi | Deneme bitiş tarihi | Bu sipariş için deneme döneminin sona erdiği veya sonlandırmış olacağı tarih. | TrialEndDate |
| Müşteri para birimi (CC) | Müşteri para birimi | Ticari Market hareketi için müşterinin kullandığı para birimi. | CustomerCurrencyCC |
| Fiyat (CC) | Fiyat | Müşteri para birimi cinsinden gösterilen SKU 'nun birim fiyatı. | PriceCC |
| Ödeme para birimi (PC) | Ödeme para birimi | Yayımcı, yayımcı tarafından yapılandırılan para birimindeki varlıkla ilişkili kullanım olayları için ödeme yapılır. | PayoutCurrencyPC |
| Tahmini fiyat (PC) | Tahmini fiyat | SKU 'nun yayımcı tarafından yapılandırılan para birimi fiyatı. | EstimatedPricePC |
| Kullanım başvurusu | Kullanım başvurusu | Kullanım raporunu (ticari Market Analytics 'te), ödeyen işlem raporuyla bağlamak için kullanılan birleştirilmiş bir GUID. Kullanım başvurusu, ödeme işlem raporundaki OrderID ve Lineıtemıd ile bağlantılı. | UsageReference |
| Kullanım birimi | Kullanım birimi | SKU ile ilişkili tüketim birimi. | UsageUnit |
| Müşteri Kimliği | Müşteri Kimliği | Müşteriye atanan benzersiz tanımlayıcı. Müşterinin sıfır veya daha fazla Azure Market aboneliği olabilir. | CustomerId |
| Faturalandırma hesabı KIMLIĞI | Faturalandırma hesabı KIMLIĞI | Faturalandırma 'nin oluşturulduğu hesabın tanımlayıcısı. Ödeme Işlem raporunuzu müşteri, sipariş ve kullanım raporlarıyla bağlamak için **faturalandırma HESABı kimliğini** **MüşteriNo** olarak eşleyin. | Billingaccountıd |
| Kullanım miktarı | Kullanım miktarı | Müşteri tarafından dağıtılan varlık tarafından tüketilen toplam kullanım birimi.<br>Bu, kullanım türü öğesine dayalıdır. Örneğin, kullanım türü normalleştirilmiş kullanım ise, kullanım miktarı normalleştirilmiş kullanım için olur. | UsageQuantity |
| NormalizedUsage | Normalleştirilmiş kullanım | Müşteri tarafından dağıtılan varlık tarafından tüketilen toplam normalleştirilmiş kullanım birimi.<br>Normalleştirilmiş kullanım saatleri, sanal makine çekirdekleri ([VM çekirdeği sayısı] x [saat ham kullanım]) için hesaba normalleştirilmiş kullanım saatleri olarak tanımlanır. "SHAREDCORE" olarak belirlenen VM 'Ler [VM çekirdeklerinin sayısı] çarpanı olarak 1/6 (veya 0,1666) kullanır. | NormalizedUsage |
| MeteredUsage | Tarifeli kullanım | Müşteri tarafından dağıtılan teklifle yapılandırılmış ölçüm tarafından tüketilen toplam kullanım birimleri. | MeteredUsage |
| RawUsage | Ham kullanım | Müşteri tarafından dağıtılan varlık tarafından tüketilen toplam ham kullanım birimleri.<br>Ham kullanım saatleri, sanal makinelerin kullanım birimi açısından çalıştırıldığı zaman miktarı olarak tanımlanır. | RawUsage |
| Tahmini genişletilmiş ücret (CC) | Müşteri para birimi cinsinden tahmini genişletilmiş ücret | Kullanımla ilişkili ücretleri belirtir. Sütun, Fiyat (CC) ve kullanım miktarının ürünüdür. | EstimatedExtendedChargeCC |
| Tahmini genişletilmiş ücret (PC) | Ödeme para birimi cinsinden tahmini genişletilmiş ücret | Kullanımla ilişkili ücretleri belirtir. Sütun, tahmini fiyat (PC) ve kullanım miktarının ürünüdür. | EstimatedExtended ChargePC |
|||||

### <a name="usage-page-filters"></a>Kullanım sayfası filtreleri

**Kullanım** sayfası filtreleri, siparişler sayfa düzeyinde uygulanır. Bir veya daha fazla filtre seçerek, seçtiğiniz ölçütlere göre grafiği işleyebilir ve kullanım siparişlerinin verilerinin kılavuz/dışarı aktarma bölümünde görmek istediğiniz veriler görüntülenir. Filtreler, kullanım sayfasının sağ üst köşesinde seçtiğiniz ay aralığı için ayıklanan verilere uygulanır.

VM ham kullanımı için pencere öğeleri ve dışarı aktarma raporu, aşağıdaki farklılıklarla sanal makine normalleştirilmiş kullanımına benzer:

- Normalleştirilmiş kullanım saatleri, sanal makine çekirdekleri ([VM çekirdeği sayısı] x [saat ham kullanım]) için hesaba normalleştirilmiş kullanım saatleri olarak tanımlanır. "SHAREDCORE" olarak belirlenen VM 'Ler [VM çekirdeklerinin sayısı] çarpanı olarak 1/6 (veya 0,1666) kullanır.
- Ham kullanım saatleri, sanal makinelerin kullanım birimi açısından çalıştırıldığı zaman miktarı olarak tanımlanır.

### <a name="metered-billing-usage"></a>Ölçülen faturalandırma kullanımı

**Ölçülen kullanım** sekmesi, kullanımın ölçüm boyutuyla ölçülebileceği teklif türleri için kullanım bilgilerini gösterir. SaaS teklif türü fazla kullanımı Şu anda sunulmuştur. Sekme, SaaS ölçülen faturalama kullanımı için fazla kullanım eğilimlerinin grafiksel sunumlarını sunar:

- **Ölçüm boyutuna göre fazla kullanım eğilimi**: bir teklifin seçili ölçüm boyutu için aylık fazla kullanım eğilimi gösterir. X ekseni ayı temsil eder ve Y ekseni fazla kullanım miktarını temsil eder. Özel ölçerin ölçü birimi Y ekseni üzerinde de görüntülenir.

    :::image type="content" source="./media/usage-dashboard/metered-usage.png" alt-text="Kullanım panosundaki VM normalleştirilmiş kullanım grafiğini gösterir.":::

- **SKU 'ya göre fazla kullanım eğilimi**: seçili ölçüm boyutunun SKU/planlara göre kullanım miktarının eğilimini temsil eder. Seçili teklif için en yüksek kullanım miktarına sahip ilk beş plan görüntülenir.

- **Müşterilere göre fazla kullanım eğilimi**: müşteri lideri panosu, en yüksek kullanım saatlerine sahip müşterilerin yığın listesini temsil eder ve özel ölçerin en yüksek kullanımıyla derecelendirilen bir _öncü panoda_ görüntülenir. Seçili ölçüm boyutunun fazla kullanım eğilimini görüntülemek için öncü panoda bir müşteri seçin.

    :::image type="content" source="./media/usage-dashboard/metered-usage-by-customers.png" alt-text="Kullanım panosundaki müşterilerin ölçülen kullanım grafiğini gösterir.":::

Özel ölçümler kullanan birden çok teklifinizi varsa, ölçülen faturalandırma kullanımı raporu, özel ölçüm boyutlarına göre tüm tekliflerinizin kullanım bilgilerini gösterir.

> [!TIP]
> Verileri indirmek için herhangi bir pencere öğesinin sağ üst köşesinde bulunan İndir simgesini kullanabilirsiniz. "Thumbs up" veya "thumbs aşağı" simgesine tıklayarak Pencere öğelerinin her biri hakkında geri bildirim sağlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- Ticari Market 'te bulunan analiz raporlarına genel bakış için bkz. [Partner Center 'da ticari Market için analitik raporlara erişme](./partner-center-portal/analytics.md).
- Teklifinizin Market etkinliklerini özetleyen grafikler, eğilimler ve veri değerleri için bkz. [ticari Market Analytics 'Te Özet Panosu](./summary-dashboard.md).
- Grafik ve indirilebilir biçimdeki Siparişleriniz hakkında daha fazla bilgi için bkz. [ticari Market Analytics 'Te siparişler panosu](./orders-dashboard.md)
- Sanal makine (VM) için kullanım ve ölçümlenen faturalandırma ölçümleri, bkz. [ticari Market Analytics 'Te kullanım panosu](usage-dashboard.md).
- Son 30 güne ait indirme isteklerinizin listesi için bkz. [Commercial Market Analytics 'te panoyu indirme](./partner-center-portal/downloads-dashboard.md).
- Azure Marketi ve Microsoft AppSource tekliflere yönelik müşteri geri bildirimlerinin birleştirilmiş bir görünümünü görmek için bkz. [Iş Ortağı Merkezi 'Nde derecelendirme & gözden geçirme Analizi panosu](./partner-center-portal/ratings-reviews.md).
- Ticari Market Analytics hakkında sık sorulan sorular ve veri koşullarının kapsamlı bir sözlüğü için bkz. [ticari Market Analytics terminolojisi ve sık sorulan sorular](./analytics-faq.md).
