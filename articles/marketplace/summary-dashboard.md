---
title: Ticari Market 'teki Iş ortağı merkezi analizi için Özet Panosu
description: Iş Ortağı Merkezi 'ndeki Özet panosundan Market etkinliklerini özetleyen toplam veri verilerine, eğilimler ve değerlerine nasıl erişebileceğinizi öğrenin.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/09/2020
author: sayantanroy83
ms.author: sroy
ms.openlocfilehash: c02ba12c790d745904d241f121e269aac3ed12f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462980"
---
# <a name="summary-dashboard-in-commercial-marketplace-analytics"></a>Ticari market analizinde özet panosu

Bu makalede, Iş Ortağı Merkezi 'ndeki Özet Panosu hakkında bilgi sağlanmaktadır. Bu Pano, tekliflerinizle ilgili Market etkinliklerini özetleyen grafikleri, eğilimleri ve toplam veri değerlerini görüntüler.

İş Ortağı Merkezi ' ndeki Özet panosuna erişmek için, **ticari Market** altında, özeti **[Çözümle](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary)**' yi seçin  >  .

>[!NOTE]
> Analiz terminolojisi hakkında ayrıntılı tanımlamalar için bkz. [ticari Market Analytics terminolojisi ve sık sorulan sorular](./analytics-faq.md).

## <a name="summary-dashboard"></a>Özet panosu

Özet Panosu, Azure Marketi 'ne genel bir bakış sunar ve ' ın iş performansı Microsoft AppSource. Panoda aşağıdakiler hakkında geniş bir genel bakış sunulmaktadır:

- Müşterilerin siparişleri
- Müşteriler
- Müşterilerin tekliflerin kullanımı
- Azure Market ve AppSource 'ta müşterilerin sayfa ziyaretleri

## <a name="elements-of-the-summary-dashboard"></a>Özet panosunun öğeleri

Aşağıdaki bölümlerde Özet panosunun nasıl kullanılacağı ve verilerin nasıl okunacağı açıklanır.

### <a name="month-range"></a>Ay aralığı

Her sayfanın sağ üst köşesinde bir ay aralığı seçimi bulabilirsiniz. Son 3, 6 veya 12 aya göre bir ay aralığı seçerek veya en fazla 12 ay süresince özel bir ay aralığı seçerek **Özet** sayfası grafiklerinin çıkışını özelleştirin. Varsayılan ay aralığı (hesaplama dönemi) altı aydır.

:::image type="content" source="./media/summary-dashboard/summary-dashboard.png" alt-text="Özet panosunda aylık Aralık seçeneklerini gösterir.":::

> [!NOTE]
> Görselleştirme pencere öğeleri ve dışarı aktarma raporlarında tüm ölçümler, Kullanıcı tarafından seçilen hesaplama dönemini kabul ediyor.

### <a name="orders-widget"></a>Siparişler pencere öğesi

Özet * * panosundaki siparişler pencere öğesi, tüm Transact-tabanlı teklifleriniz için geçerli siparişleri görüntüler. Siparişler pencere öğesi, seçilen hesaplama dönemi için tüm satın alınan siparişlerin (iptal edilen siparişler hariç) sayısını ve eğilimini görüntüler. Yüzde değer **sıraları** , seçilen hesaplama dönemi boyunca büyüme miktarını temsil eder.

[![Özet panosunda siparişler pencere öğesini gösterir.](./media/summary-dashboard/orders-widget.png)](./media/summary-dashboard/orders-widget.png#lightbox)


Ayrıca pencere öğesinin sol alt köşesindeki **siparişler panosu** bağlantısını seçerek siparişler raporuna gidebilirsiniz.

### <a name="customers-widget"></a>Müşteriler pencere öğesi

**Özet** panosunun **müşteriler** pencere öğesi, seçili hesaplama dönemi için tekliflerinizi almış olan müşterilerin toplam sayısını görüntüler. Müşteriler pencere öğesi, seçilen hesaplama dönemi için toplam etkin (yeni ve mevcut) müşteriler dahil olmak üzere (yeni ve var olan müşteriler hariç) bir sayı ve eğilim görüntüler. **Müşteriler** altındaki yüzde değeri, seçilen hesaplama dönemi boyunca büyüme miktarını temsil eder.

[![Özet panosundaki müşteriler pencere öğesini gösterir.](./media/summary-dashboard/customers-widget.png)](./media/summary-dashboard/customers-widget.png#lightbox)

Ayrıca, pencere öğesinin sol alt köşesindeki **müşteriler panosu** bağlantısını seçerek ayrıntılı müşteriler raporuna gidebilirsiniz.

### <a name="usage-widget"></a>Kullanım pencere öğesi

**Özet** panosunun **kullanım** pencere öğesi, tüm Azure sanal makine (VM) tekliflerinin toplam normalleştirilmiş ve ham kullanım saatlerini temsil eder. Kullanım pencere öğesi, seçilen hesaplama dönemi için toplam kullanım saatlerinin sayısını ve eğilimini görüntüler.

Kullanım Özeti tablosu, satın aldıkları tüm teklifler için müşterinin kullanım saatlerini görüntüler.

- Normalleştirilmiş kullanım saatleri, sanal makine çekirdekleri ([VM çekirdeği sayısı] x [saat ham kullanım]) için hesaba normalleştirilmiş kullanım saatleri olarak tanımlanır. "SHAREDCORE" olarak belirlenen VM 'Ler [VM çekirdeklerinin sayısı] çarpanı olarak 1/6 (veya 0,1666) kullanır.
- Ham kullanım saatleri, VM 'Lerin saat bakımından çalıştırıldığı süre miktarı olarak tanımlanır.

Toplam kullanım saatlerinin altındaki yüzde değeri, seçilen hesaplama döneminde kullanım saatlerinde büyüme miktarını temsil eder.

[![Özet panosundaki kullanım pencere öğesini gösterir.](./media/summary-dashboard/usage-widget.png)](./media/summary-dashboard/usage-widget.png#lightbox)

Ayrıca, pencere öğesinin sol alt köşesindeki **kullanım panosu** bağlantısını seçerek kullanım raporuna gidebilirsiniz.

### <a name="marketplace-insights"></a>Market öngörüleri

Market öngörüleri, tekliflerinizi Azure Marketi 'nde ve AppSource 'ta ziyaret eden kullanıcı sayısını gösterir. **Sayfa ziyaret sayısı sayfasında** , yayımcıların ticari Market çevrimiçi mağazalarında listelenen ilgili ürün ayrıntı sayfaları için müşteri etkileşimini ölçmesine olanak tanıyan ticari Market web analizlerinin bir özeti gösterilmektedir: Microsoft AppSource ve Azure Marketi. Bu pencere öğesi, seçilen hesaplama dönemi boyunca toplam sayfa ziyaretlerinin sayısını ve eğilimini görüntüler.

[![Özet panosundaki sayfa sayısını ziyaret et pencere öğesini gösterir.](./media/summary-dashboard/page-visit-count.png)](./media/summary-dashboard/page-visit-count.png#lightbox)

Ayrıca, pencere öğesinin sol alt köşesindeki **Market öngörüleri panosu** bağlantısını seçerek Market Insights raporuna gidebilirsiniz.

### <a name="geographical-spread"></a>Coğrafi yayma

Seçilen hesaplama dönemi için ısı haritasını, Coğrafya boyutuyla ilgili toplam müşteri, sipariş ve normalleştirilmiş kullanım saati sayısını görüntüler.

:::image type="content" source="./media/summary-dashboard/geo-spread.png" alt-text="Özet panosunda ülke formayı pencere öğesini gösterir.":::

Şunlara dikkat edin:

- Tam konumu görüntülemek için Haritayı taşıyabilirsiniz.
- Belirli bir konuma yakınlaştırabilirsiniz.
- Isı haritasını 'in, belirli bir konum için müşteri sayısı, sipariş sayısı ve normalleştirilmiş kullanım saatlerinin ayrıntılarını görüntülemek için bir ek kılavuz vardır.
- Haritada yer alan bir ülke/bölge arayabilir ve seçebilirsiniz. Haritadaki **giriş** düğmesini seçerek özgün görünüme dönün.

> [!TIP]
> Verileri indirmek için herhangi bir pencere öğesinin sağ üst köşesinde bulunan İndir simgesini kullanabilirsiniz. "Thumbs up" veya "thumbs aşağı" simgesini seçerek Pencere öğelerinin her biri hakkında geri bildirim sağlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- Ticari Market 'te bulunan analiz raporlarına genel bakış için bkz. [Partner Center 'da ticari Market için analitik raporlara erişme](./partner-center-portal/analytics.md).
- Grafik ve indirilebilir biçimdeki Siparişleriniz hakkında daha fazla bilgi için bkz. [Orders Dashboard in The Commercial Market Analytics](orders-dashboard.md).
- Sanal makine (VM) için kullanım ve ölçümlenen faturalandırma ölçümleri, bkz. [ticari Market Analytics 'Te kullanım panosu](usage-dashboard.md).
- Büyüme eğilimleri dahil olmak üzere müşterileriniz hakkında ayrıntılı bilgi için bkz. [ticari Market Analytics 'Te müşteri panosu](customer-dashboard.md).
- Son 30 güne ait indirme isteklerinizin listesi için bkz. [Commercial Market Analytics 'Te panoyu indirme](./partner-center-portal/downloads-dashboard.md).
- Azure Market ve AppSource 'ta teklifler için müşteri geri bildirimlerinin birleştirilmiş bir görünümünü görmek için bkz. [Iş Ortağı Merkezi 'Nde derecelendirmeler & İncelemeleri analiz panosu](./partner-center-portal/ratings-reviews.md).
- Ticari Market Analytics hakkında sık sorulan sorular ve veri koşullarının kapsamlı bir sözlüğü için bkz. [ticari Market Analytics terminolojisi ve sık sorulan sorular](./analytics-faq.md).