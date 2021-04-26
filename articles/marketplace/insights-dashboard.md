---
title: Ticari market analizinde Market İçgörüleri
description: Iş Ortağı Merkezi 'nde Market Web Analytics 'in bir özetine erişin ve bu, Microsoft AppSource ve Azure Marketi 'nde müşteri etkileşimini ölçmenize olanak sağlar.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 03/23/2021
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: f6ed2029ec333120e3942570c951f5da6e846c7a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104953281"
---
# <a name="marketplace-insights-dashboard-in-commercial-marketplace-analytics"></a>Ticari market analizinde Market İçgörüleri

Bu makalede, Iş Ortağı Merkezi 'nde Market öngörüleri panosu hakkında bilgi sağlanmaktadır. Bu Pano, yayımcıların ticari Market çevrimiçi mağazalarında listelenen ilgili ürün ayrıntıları sayfalarına yönelik müşteri etkileşimini ölçmesine olanak sağlayan ticari Market Web Analytics 'in bir özetini görüntüler: Microsoft AppSource ve Azure Marketi.

**Market Insights** panosuna iş ortağı merkezi 'ne erişmek için, ticari Market ' in altında **[](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**  >  **Market öngörülerini** Çözümle ' yi seçin.

Analiz terminolojisi hakkında ayrıntılı tanımlamalar için bkz. [ticari Market Analytics terminolojisi ve sık sorulan sorular](./analytics-faq.md).

## <a name="marketplace-insights-dashboard"></a>Market İçgörüleri panosu

Market Insights panosu, Azure Marketi ve AppSource 'un iş performansı için genel bir bakış sunar. Bu pano aşağıdakiler hakkında geniş bir genel bakış sağlar:

- Sayfa ziyaretleri eğilimi
- Eylem eğilimi çağrısı
- Sayfa ziyaretleri ve tekliflere, başvuru etki alanlarına ve kampanya kimliklerine karşı eylem çağrısı
- Coğrafya tarafından Market öngörüleri
- Market öngörüleri Ayrıntıları tablosu

Market öngörüleri panosu, lider hedefi uç noktasında oluşturulan müşteri adaylarıyla bağıntılı olmayan tıklama akışı verileri sağlar.

> [!NOTE]
> Azure Market veya AppSource 'ta teklifleri ziyaret eden kullanıcılar ve Iş Ortağı Merkezi 'nde rapor veren en fazla gecikme 48 saattir.

## <a name="elements-of-the-marketplace-insights-dashboard"></a>Market öngörüleri panosunun öğeleri

Market Insights panosu, Azure Marketi ve AppSource için Web telemetri ayrıntılarını iki ayrı sekmede görüntüler. Aşağıdaki bölümlerde Market öngörüleri panosunun nasıl kullanılacağı ve verilerin nasıl okunacağı açıklanır.

### <a name="month-range"></a>Ay aralığı

Her sayfanın sağ üst köşesinde bir ay aralığı seçimi bulabilirsiniz. Son 6 veya 12 aya göre bir ay aralığı seçerek veya en fazla 12 ay süresince özel bir ay aralığı seçerek **Market Insights** sayfa grafiklerinin çıkışını özelleştirin. Varsayılan ay aralığı (hesaplama dönemi) altı aydır.

:::image type="content" source="./media/insights-dashboard/month-filters.png" alt-text="Market öngörüleri panosundaki ay filtrelerini gösterir.":::

> [!NOTE]
> Görselleştirme pencere öğeleri ve dışarı aktarma raporlarında tüm ölçümler, Kullanıcı tarafından seçilen hesaplama dönemini kabul ediyor.

### <a name="page-visits-trends"></a>Sayfa ziyaretleri eğilimleri

Market Insights **ziyaretçi** çizelgesi, seçilen hesaplama dönemi için _sayfa ziyaretlerinin_ ve _benzersiz ziyaretçilerin_ sayısını görüntüler.

**Sayfa ziyaretleri**: Bu sayı, seçilen bir hesaplama dönemi için teklif listesi sayfasındaki (Ürün Ayrıntısı sayfası) farklı kullanıcı oturumlarının sayısını temsil eder. Kırmızı ve yeşil yüzde göstergeleri, sayfa ziyaretlerinin büyüme yüzdesini temsil eder. Eğilim grafiği, sayfa ziyaretlerinin aya göre ayı sayısını temsil eder.

**Benzersiz ziyaretçiler**: Bu sayı, Azure Marketi ve appsource 'taki teklifler için seçilen hesaplama döneminde ayrı ziyaretçi sayısını temsil eder. Bir veya daha fazla ürün ayrıntısı sayfasını ziyaret eden bir ziyaretçi, benzersiz bir ziyaretçi olarak sayılır.

[![Market öngörüleri panosundaki ziyaretçi grafiğini gösterir.](./media/insights-dashboard/visitors.png)](./media/insights-dashboard/visitors.png#lightbox)

### <a name="call-to-actions-trend"></a>Eylem eğilimi çağrısı

Bu sayı teklif listesi sayfasında (Ürün Ayrıntısı sayfası) tamamlanan eylem düğmesi tıklamaları **çağrısının** sayısını temsil eder. Kullanıcılar **Şimdi al**, **ücretsiz deneme**, **benimle iletişim** veya **test sürücüsü** düğmelerini seçerken _eyleme yapılan çağrılar_ sayılır.

[![Market öngörüleri panosundaki eylem grafiği çağrısını gösterir.](./media/insights-dashboard/call-to-actions-trend.png)](./media/insights-dashboard/call-to-actions-trend.png#lightbox)

### <a name="page-visits-and-call-to-actions-against-offers-referral-domains-and-campaign-ids"></a>Sayfa ziyaretleri ve tekliflere, başvuru etki alanlarına ve kampanya kimliklerine karşı eylem çağrısı

**Başvuru etki alanları**: belirli bir başvuru etki alanı seçildiğinde, sayfa ziyaretlerinin aylık eğilimi ve sağdaki grafik üzerindeki eyleme yapılan çağrılar gösterilir.

:::image type="content" source="./media/insights-dashboard/referral-domain.png" alt-text="Market Insights panosundaki başvuru etki alanı grafiğini gösterir.":::

**Teklifler**: sayfa ziyaretlerinin aylık eğilimini ve sağdaki grafik üzerindeki eyleme yapılan çağrıları görmek için belirli bir teklifi seçin.

:::image type="content" source="./media/insights-dashboard/offer-alias.png" alt-text="Market öngörüleri panosunda teklif diğer adı grafiğini gösterir.":::

**Kampanya kimlikleri**: belirli BIR kampanya kimliğini seçerek kampanyanın başarısını anlayabilmeniz gerekir. Her kampanya için, sayfa ziyaretlerinin aylık eğilimini ve sağ taraftaki grafik üzerinde yapılacak çağrıları görmeniz gerekir.

:::image type="content" source="./media/insights-dashboard/campaign.png" alt-text="Market Insights panosundaki kampanya grafiğini gösterir.":::

### <a name="marketplace-insights-by-geography"></a>Coğrafya tarafından Market öngörüleri

Seçili hesaplama dönemi için ısı haritasını, sayfa ziyaretlerinin, benzersiz ziyaretçilerin ve eyleme yapılan çağrıların (CTA) sayısını görüntüler. Haritadaki koyu renkte olan hafif, benzersiz ziyaretçilerin en yüksek değerini temsil eder. Bir ülkede/bölgede yakınlaştırmak için tablodaki bir kaydı seçin.

:::image type="content" source="./media/insights-dashboard/geographical-spread.png" alt-text="Market Insights panosundaki coğrafi forma grafiği gösterir.":::

Şunlara dikkat edin:

- Tam konumu görüntülemek için Haritayı taşıyabilirsiniz.
- Belirli bir konuma yakınlaştırabilirsiniz.
- Isı haritasını 'in, belirli konumdaki müşteri sayısı, sipariş sayısı ve normalleştirilmiş kullanım saatlerinin ayrıntılarını görüntülemek için bir ek kılavuz vardır.
- Haritada yer alan bir ülke/bölge arayabilir ve seçebilirsiniz. Haritadaki **giriş** düğmesini seçerek özgün görünüme dönün.

### <a name="marketplace-insights-details-table"></a>Market öngörüleri Ayrıntıları tablosu

Bu tabloda, sayfa ziyaretlerinin bir liste görünümü ve seçili tekliflerinizin sayfalarına göre sıralanan eylemlere yapılan çağrılar verilmektedir.

- Veriler bir öğesine ayıklanabilir. TSV veya. Kayıt sayısı 1.000 ' den küçükse CSV dosyası.
- Kayıt sayısı 1.000 üzerinde ise, verilen veriler sonraki 30 gün boyunca bir indirmeler sayfasına zaman uyumsuz olarak yerleştirilir.
- İlgilendiğiniz verileri göstermek için adlar ve kampanya adları sunarak verileri filtreleyin.

> [!TIP]
> Verileri indirmek için herhangi bir pencere öğesinin sağ üst köşesinde bulunan İndir simgesini kullanabilirsiniz. "Thumbs up" veya "thumbs aşağı" simgesine tıklayarak Pencere öğelerinin her biri hakkında geri bildirim sağlayabilirsiniz.

| İçindeki sütun adı<br>kullanıcı arabirimi | Öznitelik adı | Tanım | Programlı olarak sütun adı<br>erişim raporları |
| ------------ | ------------- | ------------- | ------------- |
| Tarih | Ziyaret tarihi | Sayfa ziyareti ve/veya CTA 'ın tarihi, Azure Marketi ve/veya AppSource 'daki teklifin sayfasında olay oluşturma ' ya tıklayın. | Tarih |
| Teklif Adı | Teklif Adı | Ticari Market sunumunun adı. | OfferName |
| Başvuru etki alanı | Başvuru etki alanı | Sayfanın ziyaret edildiği başvuru etki alanının adı. Sayfa ziyaret için yakalanan bir başvuru etki alanı yoksa, ilgili giriş "başvuru etki alanı yok" olur. |  ReferralDomain |
| Ülke Adı | Ülke Adı | Sayfanın ziyaret edildiği ülkenin adı. | CountryName |
| Sayfa ziyaretleri | Sayfa ziyaretleri | Belirli bir tarih için teklif adıyla ilişkili sayfa ziyaretlerinin sayısı. | Pageziyaretler |
| Hemen alın | Hemen alın | Belirli bir tarih için teklifin sayfasındaki "Şimdi al" CTA öğesine tıklama sayısı. | Şimdi getit |
| Benimle iletişim kurun | Benimle iletişim kurun | Belirli bir tarih için teklifin sayfasında "benimle Iletişime geçin" ADLı tıklama sayısı. | ContactMe |
| Test Sürüşü | Test Sürüşü | Belirli bir tarih için teklifin sayfasında "Test Drive" CTA öğesine tıklama sayısı. | TestDrive |
| Ücretsiz Deneme | Ücretsiz Deneme | Belirli bir tarih için teklifin sayfasındaki "ücretsiz deneme" CTA 'ya tıklama sayısı. | FreeTrial |
| Kampanya | Kampanyanın adı | Kampanya adında Web telemetrisini (sayfa ziyaret ve CTA tıklamaları) anlayabilme özelliği. | Kampanya |
| yok | Site | Sayfanın ziyaret edilmesi veya CTA tıklamasına ait storefront adı. Olası değerler şunlardır:<br><ul><li>AZUREMARKETPLACE<li>APPSOURCE</ul> | Site |
|

## <a name="next-steps"></a>Sonraki adımlar

- Ticari Market 'te bulunan analiz raporlarına genel bakış için bkz. [Partner Center 'da ticari Market için analitik raporlara erişme](./partner-center-portal/analytics.md).
- Grafik ve indirilebilir biçimdeki Siparişleriniz hakkında daha fazla bilgi için bkz. [Orders Dashboard in The Commercial Market Analytics](./orders-dashboard.md).
- Sanal makine (VM) için kullanım ve ölçümlenen faturalandırma ölçümleri, bkz. [ticari Market Analytics 'Te kullanım panosu](./usage-dashboard.md).
- Büyüme eğilimleri dahil olmak üzere müşterileriniz hakkında ayrıntılı bilgi için bkz. [ticari Market Analytics 'Te müşteri panosu](./customer-dashboard.md).
- Son 30 güne ait indirme isteklerinizin listesi için bkz. [Commercial Market Analytics 'te panoyu indirme](./partner-center-portal/downloads-dashboard.md).
- Azure Market ve AppSource 'ta teklifler için müşteri geri bildirimlerinin birleştirilmiş bir görünümünü görmek için bkz. [Iş Ortağı Merkezi 'Nde derecelendirmeler & İncelemeleri analiz panosu](./partner-center-portal/ratings-reviews.md).
- Ticari Market Analytics hakkında sık sorulan sorular ve veri koşullarının kapsamlı bir sözlüğü için bkz. [ticari Market Analytics terminolojisi ve sık sorulan sorular](./analytics-faq.md).
