---
title: Azure Kurumsal Anlaşma faturanızı gözden geçirme
description: Azure Kurumsal Anlaşmaları’nın kullanımını ve faturasını okuma ve anlama hakkında bilgi edinin.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 5d945c421dc0a4abed17b3a355339c131dd922df
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728059"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>Azure Kurumsal Anlaşma faturanızı anlama

Kurumsal Anlaşması olan Azure müşterileri, kuruluşun kredisini aştığında veya kredi kapsamında olmayan hizmetleri kullandığında bir fatura alır.

Kuruluşunuzun kredisi Azure Ön Ödemenizi (eski adıyla parasal taahhüt) içerir. Azure Ön Ödemesi, kuruluşunuzun Azure hizmetleri kullanımı için peşin ödediği tutardır. Microsoft hesap yöneticinizle veya kurumsal bayinizle iletişim kurarak Kurumsal Anlaşmanıza Azure Ön Ödemesi fonları ekleyebilirsiniz.

Bu öğretici yalnızca Azure Kurumsal Anlaşma sahibi olan Azure müşterileri için geçerlidir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Faturalanmış ücretleri gözden geçirme
> * Hizmet fazla kullanım ücretlerini gözden geçirme
> * Market faturasını inceleme

## <a name="prerequisites"></a>Ön koşullar

Faturanızdaki ücretleri gözden geçirip doğrulamak için Kurum Yöneticisi olmanız gerekir. Daha fazla bilgi için bkz. [Azure’daki Azure Kurumsal Anlaşma yönetici rollerini anlama](../manage/understand-ea-roles.md). Kuruluşunuz için kimin Kurum Yöneticisi olduğunu bilmiyorsanız [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="review-invoiced-charges-for-most-customers"></a>Çoğu müşteri için faturalanmış ücretleri gözden geçirme

Bu bölüm, Avustralya, Japonya veya Singapur’daki Azure müşterileri için geçerli değildir.

Faturalama döneminizde aşağıdakilerden herhangi biri gerçekleştiğinde bir Azure faturası alırsınız:

- **Hizmet fazla kullanımı**: Kuruluşunuzun kullanım ücretleri, kredi bakiyenizi aştığında.
- **Ayrı olarak faturalandırılan ücretler**: Kuruluşunuzun kullandığı hizmetler, kredi kapsamında olmadığında. Kredi bakiyeniz ne olursa olsun aşağıdaki hizmetler için faturalandırılırsınız:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Kayıtlı Kullanıcı
    - Openlogic
    - Uzaktan Erişim Hakları XenApp Essentials Kayıtlı Kullanıcı
    - Ubuntu Advantage
    - Visual Studio Enterprise (Aylık)
    - Visual Studio Enterprise (Yıllık)
    - Visual Studio Professional (Aylık)
    - Visual Studio Professional (Yıllık)
- **Market ücretleri**: Azure Market satın alma işlemleri ve kullanımı, kuruluşunuzun kredi kapsamında yer almaz. Bu nedenle, kredi bakiyeniz ne olursa olsun Pazar ücretleri için faturalandırılırsınız. Enterprise Portal’da bir Kurumsal Yönetici, Pazar satın alma işlemlerini etkinleştirebilir ve devre dışı bırakabilir.

Faturanızda önce Azure kullanım ücretleri ve bunlarla ilişkili maliyetler, ardından da market ücretleri gösterilir. Kredi bakiyeniz varsa, bu bakiye Azure kullanımınıza uygulanır ve faturanızda maliyeti olmayan Azure kullanımı ve market kullanımı en son gösterilir.

Enterprise Portal’da **Raporlar** > **Kullanım Özeti** bölümünde gösterilen birleşik toplam tutarı, Azure faturanızla karşılaştırın. **Kullanım Özeti**’ndeki tutarlara vergiler dahil değildir.

[Azure EA portalında](https://ea.azure.com) oturum açın. Ardından **Raporlar**’ı seçin. Sekmenin sağ üst köşesinde **M** görünümünü **C** olarak değiştirin ve fatura dönemine göre ayarlayın.  

![Kullanım özetinde M + C seçeneğini gösteren ekran görüntüsü.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

**Toplam Kullanım** ve **Azure Market** birleşik tutarı, faturanızdaki **Toplam Genişletilmiş Tutar** ile aynı olmalıdır. Ücretlerinizle ilgili daha fazla ayrıntı almak için **Kullanım Bilgilerini İndirme** bölümüne gidin.  

![Kullanımı İndir sekmesini gösteren ekran görüntüsü](./media/review-enterprise-agreement-bill/ea-portal-download-usage.png)

## <a name="review-invoiced-charges-for-other-customers"></a>Diğer müşteriler için faturalanmış ücretleri gözden geçirme

Bu bölüm yalnızca Avustralya, Japonya veya Singapur’daki Azure müşterileri için geçerlidir.

Aşağıdaki olaylardan herhangi biri gerçekleştiğinde bir veya birden fazla Azure faturası alırsınız:

- **Hizmet fazla kullanımı**: Kuruluşunuzun kullanım ücretleri, kredi bakiyenizi aştığında.
- **Ayrı olarak faturalandırılan ücretler**: Kuruluşunuzun kullandığı hizmetler, kredi kapsamında olmadığında. Aşağıdaki hizmetler için faturalandırılırsınız:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Kayıtlı Kullanıcı
    - Openlogic
    - Uzaktan Erişim Hakları XenApp Essentials Kayıtlı Kullanıcı
    - Ubuntu Advantage
    - Visual Studio Enterprise (Aylık)
    - Visual Studio Enterprise (Yıllık)
    - Visual Studio Professional (Aylık)
    - Visual Studio Professional (Yıllık)
- **Market ücretleri**: Azure Market satın alma işlemleri ve kullanımı, kuruluşunuzun kredi kapsamında yer almaz ve ayrı olarak faturalandırılır. Enterprise Portal’da bir Kurumsal Yönetici, Pazar satın alma işlemlerini etkinleştirebilir ve devre dışı bırakabilir.

Hizmet fazla kullanımı için ödenmesi gereken ücretleriniz ve faturalama döneminde ayrı olarak faturalandırılan ücretleriniz varsa tek bir fatura alırsınız. Bu, her iki masraf türünü de içerir. Pazar ücretleri her zaman ayrı olarak faturalandırılır.

## <a name="review-service-overage-charges-for-other-customers"></a>Diğer müşteriler için hizmet fazla kullanım ücretlerini gözden geçirme

Bu bölüm yalnızca Avustralya, Japonya veya Singapur’daysanız geçerlidir.

Enterprise Portal’da **Raporlar** > **Kullanım Özeti** bölümündeki toplam kullanım tutarınızı, hizmet fazla kullanım faturanızla karşılaştırın. Hizmet fazla kullanım faturası, kuruluşunuzun kredisini aşan kullanımı ve/veya kredi kapsamında olmayan hizmetleri içerir. **Kullanım Özeti**’ndeki tutarlara vergiler dahil değildir.

[Azure EA portalında](https://ea.azure.com) oturum açın ve **Raporlar**'ı seçin. Sekmenin sağ üst köşesinde **M** görünümünü **C** olarak değiştirin ve fatura dönemine göre ayarlayın.  

![Kullanım özetinde M + C seçeneğini gösteren ekran görüntüsü.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

**Toplam Kullanım** tutarı, hizmet fazla kullanım faturanızdaki **Toplam Genişletilmiş Tutar** ile aynı olmalıdır. Ücretlerinizle ilgili daha fazla bilgi edinmek için **Kullanımı İndir** > **Gelişmiş Rapor İndirme** bölümüne gidin. Vergiler, rezervasyon ücretleri veya market ücretleri rapora dahil değildir.  

![Kullanımı indir sekmesinde Gelişmiş rapor İndirme’yi gösteren ekran görüntüsü.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-advanced.png)

Aşağıdaki tabloda, Enterprise Portal’daki **Kullanım Özeti**’nde ve faturada gösterilen terimler ve açıklamalar listelenmektedir:

|Fatura terimi|Kullanım Özeti terimi|Açıklama|
|---|---|---|
|Toplam Genişletilmiş Tutar|Toplam Kullanım|Kredi uygulanmadan önceki belirli dönem için toplam vergi öncesi kullanım ücreti.|
|Taahhüt Kullanımı|Taahhüt Kullanımı|Bu belirli dönemde uygulanan kredi.|
|Toplam Satış|Toplam Fazla Kullanım|Kredi tutarınızı aşan toplam kullanım ücreti. Bu tutara vergi dahil değildir.|
|Vergi Tutarı|Uygulanamaz|Belirli bir dönem için toplam satış tutarına uygulanan vergi.|
|Vergi Tutarı|Uygulanamaz|Kredi uygulandıktan ve vergi eklendikten sonra fatura için ödenmesi gereken tutar.|

### <a name="review-marketplace-invoice"></a>Market faturasını inceleme

Bu bölüm yalnızca Avustralya, Japonya veya Singapur’daysanız geçerlidir.

Enterprise Portal’daki **Raporlar** > **Kullanım Özeti** bölümündeki Azure Market toplamınızı market faturanızla karşılaştırın. Market faturasında yalnızca Azure Market satın alma işlemleri ve kullanımı bulunur. **Kullanım Özeti** miktarları, yayımcı tarafından belirlenen bir vergiyi içerir.

[Enterprise Portal](https://ea.azure.com)'da oturum açın ve **Raporlar**'ı seçin. Sekmenin sağ üst köşesinde **M** görünümünü **C** olarak değiştirin ve fatura dönemine göre ayarlayın.  

![Kullanım özetinde M + C seçeneğini gösteren ekran görüntüsü.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)  

**Azure Market** toplamı, market faturanızdaki **Toplam Satış** ile aynı olmalıdır. Kullanım temelli ücretlerinizle ilgili daha fazla bilgi edinmek için **Kullanımı İndir** bölümüne gidin. **Market Ücretleri** bölümünde **İndir**’i seçin. Market fiyatı, yayımcı tarafından belirlenen bir vergiyi içerir. Müşteriler, yayımcıdan işlemle ilgili verginin tahsil edilmesine ilişkin ayrı bir fatura almaz.

![Market ücretleri bölümünde indirme seçeneğini gösteren ekran görüntüsü.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-marketplace.png)

## <a name="view-price-sheet-information"></a>Fiyat listesi bilgilerini görüntüleme

Kuruluş yöneticileri Azure hizmetleri kayıtlarıyla ilişkili fiyat listesini görüntüleyebilir.

Geçerli fiyat listesini görüntülemek için:

1. Azure Enterprise Portal'da **Raporlar**'ı ve ardından **Fiyat Listesi**'ni seçin.
2. Fiyat listesini görüntüleyin veya **İndir**'i seçin.

![Fiyat listesi bilgilerini gösteren örnek](./media/review-enterprise-agreement-bill/ea-create-view-price-sheet-information.png)

Geçmiş fiyat listesini indirmek için:

1. Azure Enterprise Portal'da **Raporlar**'ı ve ardından **Kullanımı İndir**'i seçin.
2. Fiyat listesini indirin.

![Eski fiyat listesinin indirileceği yeri gösteren örnek](./media/review-enterprise-agreement-bill/create-ea-view-price-sheet-download-historical-price-list.png)

Fiyat farklılıklarının nedenlerinden bazıları şunlardır:

- Önceki kayıtla yeni kayıt arasında fiyatlandırma değişmiş olabilir. Fiyat değişiklikleri olmasının nedeni, belirli bir kayıt için fiyatlandırma anlaşmasının, sözleşmenin başlangıç tarihinden bitiş tarihine kadar geçerli olmasıdır.
- Kayıt yeni bir kayda aktarıldığında, yeni sözleşmenin fiyatlandırması geçerli olur. Fiyatlandırma, sizin fiyat listenize göre belirlenir ve bu da yeni kayıtta daha yüksek olabilir.
- Kaydın süresinin uzatılması durumunda da fiyatlandırma değişir. Fiyatlar kullandıkça öde fiyatlarına göre değişir.

## <a name="request-detailed-usage-information"></a>Ayrıntılı kullanım bilgileri isteme

Kuruluş yöneticileri, Azure Enterprise Portal’da kullanım verilerinin özetini, tüketilen Azure Ön Ödemesini ve ek kullanımla ilişkili ücretleri görüntüleyebilir. Ücretler, tüm hesaplar ve abonelikler genelinde özet düzeyinde gösterilir.

Belirli hesaplardaki ayrıntılı kullanımı görüntülemek için **Raporlar** > **Kullanımı İndir**'e gidip kullanım ayrıntıları raporunu indirebilirsiniz.

> [!NOTE]
> Kullanım ayrıntıları raporu hiçbir geçerli vergiyi içermez. Kullanımın tahakkuk etmesiyle rapora yansıtılması arasında sekiz saatlik bir gecikme olabilir.

Dolaylı kayıtlarda, maliyetle ilgili bilgileri görebilmeniz için önce iş ortağınızın ek ücret işlevini etkinleştirmesi gerekir.

## <a name="reports"></a>Raporlar

Kuruluş yöneticileri, Azure Enterprise Portal’da kullanım verilerinin özetini, tüketilen Azure Ön Ödemesini ve ek kullanımla ilişkili ücretleri görüntüleyebilir. Ücretler, tüm hesaplar ve abonelikler genelinde özet düzeyinde gösterilir.

### <a name="azure-enterprise-reports"></a>Azure Kurumsal raporları

- Kullanım özeti ve grafikler
- Hizmet kullanım raporu
- Bakiye ve ücret raporu
- Kullanım ayrıntıları raporu
- Azure Market ücretleri raporu
- Fiyat listesi
- Gelişmiş rapor indirme
- CSV raporunu biçimlendirme

### <a name="to-view-the-usage-summary-reports-and-graphs"></a>Kullanım özeti raporlarını ve grafiklerini görüntülemek için:

1. Azure Enterprise Portal'a gidin.
1. Sol taraftaki bölmeden **Raporlar**'ı seçin.
1. **Kullanım Özeti** sekmesini seçin.
1. Sol üstteki tarihi aralığı menüsünden taahhüt dönemini seçin.
1. Ek ayrıntıları görüntülemek için grafikte bir dönem veya ay seçin.
1. Bu sekmede şunları yapabilirsiniz:
   - Kullanım, hizmet fazla kullanımı, ayrı olarak faturalandırılan ücretler ve Azure Market ücretlerinin dökümünü kapsayan ve aylara göre ayrılmış grafiği görüntüleyin.
   - Grafiğin altından departmanlara, hesaplara ve aboneliklere göre filtreleyin.
   - **Hizmetlere Göre Ücret** ve **Hiyerarşiye Göre Ücret** dökümleri arasında geçiş yapın.
   - Azure hizmetlerinin ayrıntılarını, ayrı olarak faturalandırılan ücretleri ve Azure Market ücretlerini görüntüleyin.

## <a name="service-usage-report"></a>Hizmet kullanım raporu

Hizmet kullanım raporu sayfası, kuruluş yöneticilerinin hizmet kullanım verilerinin özetini görüntülemesine olanak tanır. Kullanım, tüm hesaplar ve abonelikler genelinde özet düzeyinde gösterilir. Ayrıntılı kullanımı görüntülemek için raporu hesaplara veya aboneliklere göre filtreleyebilirsiniz.

> [!NOTE]
> Gerçek kullanım tarihi ile kullanımın bu rapora yansıtıldığı tarih arasında beş güne kadar fark olabilir.

### <a name="to-view-the-report"></a>Raporu görüntülemek için:

1. Azure Enterprise Portal'da oturum açın.
1. Sol taraftaki gezinti menüsünden **Raporlar**'ı seçin.
1. **Kullanım Özeti** sekmesini seçin.
1. Tarih aralığını seçin.
1. Görüntülemek istediğiniz hesapları veya abonelikleri seçin.
1. İsteğe bağlı olarak şunları da yapabilirsiniz:
   - Farklı dökümleri görüntülemek için **Hizmetlere Göre Ücret** ve **Hiyerarşiye Göre Ücret** görünümleri arasında geçiş yapın.
   - Hizmet Adı, Ölçü Birimi, Tüketilen Birimler, Geçerli Ücret ve Genişletilmiş Maliyet gibi ayrıntıları görüntüleyin.

## <a name="download-csv-reports"></a>CSV raporlarını indirme

Aylık rapor indirme sayfası, kuruluş yöneticilerinin birden fazla raporu CSV dosyası olarak indirmesini sağlar. Şu raporlar indirilebilir:

- Bakiye ve ücret raporu
- Kullanım ayrıntıları raporu
- Azure Market ücretleri raporu
- Fiyat listesi

### <a name="to-download-reports"></a>Raporları indirmek için:

1. Azure Enterprise Portal'da **Rapor**'u seçin.
1. Üst şeritten **Kullanımı İndir**'i seçin.
1. İlgili ay raporunun yanındaki **İndir**'i seçin.

### <a name="csv-report-formatting-issues"></a>CSV raporunu biçimlendirme sorunları

Azure Enterprise Portal'ın CSV raporlarını Euro cinsinden görüntüleyen kullanıcılar, virgüller ve noktalarla ilgili biçimlendirme sorunlarıyla karşılaşabilir.

Örneğin şunları görebilirsiniz:

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| Saat | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

Şunu görmeniz gerekir:

| ServiceResource | ResourceQtyConsumed | ResourceRate | ExtendedCost |
| --- | --- | --- | --- |
| Saat | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

Bu biçimlendirme sorununun nedeni, Excel'in içeri aktarma işlevindeki varsayılan ayarlardır. Excel, tüm alanları "Genel" metin olarak içeri aktarır ve bunun şu matematik standardında ayrılmış bir sayı olduğunu kabul eder. Örneğin: "1,000.00".

Binlik ayırıcı olarak nokta (.), ondalık ayırıcı olarak da virgül (,) kullanılan bir Avrupa para birimi için değer hatalı bir şekilde görüntülenecektir. Örneğin: "1.000,00". İçeri aktarma sonuçları, bölge ve dil ayarınıza göre değişebilir.

### <a name="to-import-the-csv-file-without-formatting-issues"></a>CSV dosyasını biçimlendirme sorunu yaşamadan içeri aktarmak için:

1. Microsoft Excel'de **Dosya** > **Aç**'ı seçin.
   Metin İçeri Aktarma Sihirbazı görüntülenir.
1. **Özgün Veri Türü** bölümünde **sınırlandırılmış**'ı seçin.  Varsayılan değer **Sabit Genişlik** olarak belirlenmiştir.
1. **İleri**’yi seçin.
1. Sınırlayıcılar bölümünde **Virgül** onay kutusunu seçin. **Sekme** seçiliyse seçimini kaldırın.
1. **İleri**’yi seçin.
1. **ResourceRate** ve **ExtendedCost** sütunlarının üzerine gelin.
1. **ResourceRate** sütununu seçin. Siyah renkte vurgulanmış olarak görünür.
1. **Sütun Veri Biçimi** bölümünde **Genel** yerine **Metin** girişini seçin. Sütun başlığı **Genel** yerine **Metin** olur.
1. 8 ve 9. adımları **ExtendedCost** sütunu için tekrarlayın ve **Son**'u seçin.

> [!TIP]
> CSV dosyalarını otomatik olarak Excel'de açılacak şekilde ayarladıysanız Excel'deki **Aç** işlevini kullanmanız gerekir. Excel'de **Dosya** > **Aç**'ı seçin.

## <a name="balance-and-charge-report"></a>Bakiye ve ücret raporu

Bakiye ve ücret raporu bakiyelere, yeni satın alımlara, Azure Market hizmeti ücretlerine, ayarlamalara ve fazla kullanım ücretlerine ilişkin bilgilerin aylık bir özetini sunar.

Azure Hizmet Taahhüdü özet tablosundaki tüm satırlar, tüm aylarda sabit kalır. Tüm satın alma ve düzeltme bilgileri **Yeni Satın Alma Ayrıntıları** ve **Düzeltme Ayrıntıları** bölümlerinde görüntülenir.

### <a name="download-the-balance-and-charge-report"></a>Bakiye ve ücret raporunu indirme

1. Azure Enterprise Portal'da kuruluş yöneticisi olarak oturum açın.
1. Sol taraftaki bölmeden **Raporlar**'ı seçin.
1. **Rapor İndirme** sekmesini seçin.
1. **Bakiye ve Ücret** sütununun altından uygun ayı seçin ve raporu indirin.

## <a name="usage-detail-report"></a>Kullanım ayrıntıları raporu

Kullanım ayrıntıları raporu bir kayıt tarafından tüketilen hizmetlerin ve miktarların aylık özetini sunar. Buna ölçüm adları, ölçüm türleri ve tüketilen miktarlar hakkında bilgiler dahildir.

### <a name="download-the-usage-detail-report"></a>Kullanım ayrıntısı raporunu indirme

1. Azure Enterprise Portal'da kuruluş yöneticisi olarak oturum açın.
1. Sol taraftaki gezinti menüsünden **Raporlar**'ı seçin.
1. **Kullanımı İndir** sekmesini seçin.
1. **Kullanım Ayrıntısı** sütununun altından uygun ayı seçin ve raporu indirin.

## <a name="azure-marketplace-charges-in-azure-enterprise-portal-reports"></a>Azure Enterprise Portal raporlarındaki Azure Market ücretleri

İki tür Azure Market ücreti vardır:

- **Kullanıma dayalı:** Ürünlerin kullanımı, işlem birimi üzerinden ölçülür.  Örneğin sanal makineler saat, Bing API aramaları ise arama sayısı üzerinden ücretlendirilir.
- **Aylık ücret:** Kullanıma veya erişime göre aylık olarak faturalandırılır.

Azure Market ücretleri hakkında daha fazla bilgi için bkz. [Azure Market Hakkında SSS](https://azure.microsoft.com/marketplace/faq/).

Azure Enterprise Portal'daki farklı ücretleri görüntülemek için:

- **Kullanım özeti raporu**: Kullanıma dayalı **ve** aylık ücret türündeki Azure Market ücretlerini gösterir.
- **Market ücretleri raporu**: **Yalnızca** kullanıma dayalı Azure Market ücretlerini gösterir.  Tek seferlik ücretler gösterilmez.

> [!NOTE]
> Azure Market, MPSA kayıtlarıyla kullanılamaz.

## <a name="advanced-report-download"></a>Gelişmiş rapor indirme

Belirli tarih aralıklarına veya hesaplara göre rapor oluşturmak için gelişmiş rapor indirme işlevini kullanabilirsiniz. Daha geniş kayıt kümelerine yer verebilme amacıyla çıkış dosyasının biçimi 30 Ağustos 2016 tarihinden itibaren CSV olarak değiştirilmiştir.

1. Azure Enterprise Portal'da **Gelişmiş Rapor İndirme**'yi seçin.
1. **Uygun Tarih Aralığı**'nı seçin.
1. **Uygun Hesaplar**'ı seçin.
1. **Kullanım Verilerini İste**'yi seçin.
1. Rapor durumu **İndir** olana kadar **Yenile** düğmesini seçin.
1. Raporu indirin.

## <a name="reporting-for-non-enterprise-administrators"></a>Kuruluş yöneticileri dışındaki kullanıcılar için raporlama

Kuruluş yöneticileri, bir kaydın bölüm yöneticileri (DA) ve hesap sahipleri (AO) için ücretleri görüntüleme erişimi sunabilir. Erişim verilen hesap sahipleri kendi hesaplarına ve aboneliklerine ait CSV raporlarını indirebilir. Bu kişiler bilgileri Azure Enterprise Portal'ın raporlama bölümünde görsel olarak da görüntüleyebilir.

### <a name="to-enable-access"></a>Erişimi etkinleştirmek için:

 1. Azure Enterprise Portal'da kuruluş yöneticisi olarak oturum açın.
 1. Sol gezinti panelinde **Yönet**’i seçin.
 1. **Kayıt** sekmesini seçin.
 1. **Kayıt Ayrıntıları** bölümünde **DA Ücretleri Görüntüleme** veya **AO Ücretleri Görüntüleme** girişinin yanındaki kalem simgesini seçin.
 1. **Etkin**'i seçin.
 1. **Kaydet**’i seçin.

### <a name="to-view-reports"></a>Raporları görüntülemek için:

1. Azure Enterprise Portal'da bölüm yöneticisi veya hesap sahibi olarak oturum açın.
1. Sol taraftaki gezinti menüsünden **Raporlar**'ı seçin.
1. Hesap ve abonelik bilgilerini görsel olarak görüntülemek için **Kullanım Özeti** sekmesini seçin.
1. CSV raporlarını görüntülemek için **Kullanımı İndir**'i seçin.

Bölüm yöneticileri ve hesap sahipleri, ücretleri **Gelişmiş Rapor İndirme** işlevini kullanarak görüntüleyemez. Ücretler $0 olarak görünür.

Hesap sahibine verilen görüntüleme izinleri, hesap sahiplerini ve ilgili aboneliklerde gerekli izinlere sahip olan tüm kullanıcıları kapsar. Dolaylı müşteriyseniz ücret özelliklerinin kanal iş ortağınız tarafından etkinleştirilmesi gerekir.

## <a name="power-bi-reporting"></a>Power BI raporları

Power BI raporlarını EA doğrudan, iş ortağı ve fatura bilgilerini görüntüleme erişimine sahip olan dolaylı müşteriler kullanabilir.

### <a name="power-bi-pro"></a>Power BI Pro

Power BI Pro EA müşterileri tarafından kullanılabilir. Power BI Pro ile maliyet verilerinizi verimli bir şekilde yönetmenizi sağlayacak raporlar oluşturabilir ve paylaşabilirsiniz. Ayrıca ek işbirliği ve veri yenileme özelliklerinden de faydalanabilirsiniz. Power BI Pro, daha yüksek veri kapasitesi ve veri akışı sınırları sunar.

<!--We plan to add new cost management features for Azure Enterprise customers.

Current Power BI (free) users who use the Microsoft Azure Consumption Insights content pack can get a 60-day free trial of Power BI Pro. After the trial is over, you can continue using Power BI Pro by adding a license.

To sign up for the free Power BI Pro trial:

1. From the gear icon in Power BI, select **Manage personal storage**.
1. Select **Try Pro for free** on the right.

See [Power BI self-service sign up](https://powerbi.microsoft.com/documentation/powerbi-service-self-service-signup-for-power-bi/#power-bi-pro-60-day-trial) for more information on the Power BI Pro free trial.

### Azure EA Power BI Pro trial terms

- **General purpose**: The extended Power BI Pro for the "Microsoft Azure Enterprise" content pack trial offer (the "Offer") is available to existing qualified users during the term of the Offer, to allow them to access certain insights related to their Azure consumption through the use of a specific Power BI content pack.
- **Eligibility**: Users under an Enterprise Agreement (EA) can participate in the Offer if they have a function related to their organization's Azure billing, service, or cost management.
- **Exclusions**:
  - Users already participating in the Extended Power BI Pro trial will continue to qualify under the pre-existing offer and can't enter into the Azure EA Power BI Pro trial offer.
  - Users participating in the Offer can only use Power BI Pro with the Microsoft Azure Enterprise content pack. Any other use of Power BI Pro is prohibited.
  - Term: The Offer began on June 1, 2017 and ended on May 31, 2018.  Acceptance can occur at any time during the 12-month period, though the offer will terminate on May 31, 2018 for all users regardless of when they accepted the Offer.
  -->

### <a name="to-access-microsoft-azure-consumption-insights"></a>Microsoft Azure Consumption Insights'a erişmek için:

1. [Microsoft Azure Consumption Insights](https://app.powerbi.com/getdata/services/azureconsumption?cpcode=MicrosoftAzureConsumptionInsights&amp;getDataForceConnect=true&amp;WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26)'a gidin.
1. **Şimdi Edinin**'i seçin.
1. Bir kayıt numarası ve ay sayısı girdikten sonra **İleri**'yi seçin.
1. Bağlanmak için API erişim anahtarınızı girin. Kaydınıza ait anahtarı [Enterprise Portal](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26)'da bulabilirsiniz.
1. İçeri aktarma işlemini otomatik olarak başlatmak için **Oturum Aç**'ı seçin.
1. Bu işlem tamamlandığında gezinti bölmesinde yeni bir pano, rapor ve model görünür. İçeri aktarılan verilerinizi görüntülemek için panoyu seçin.

> [!TIP]
>
> - Kaydınız için API anahtarı oluşturmayı öğrenmek istiyorsanız lütfen [Enterprise Portal](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26)'daki API Raporları yardım dosyasını inceleyin.
> - Power BI'ı Azure tüketiminize bağlama hakkında daha fazla bilgi için bkz. [Microsoft Azure Consumption Insights](/power-bi/desktop-connect-azure-cost-management).

### <a name="to-access-the-legacy-power-bi-ea-content-pack"></a>Eski Power BI EA içerik paketine erişmek için:

1. [Power BI web sitesine](https://app.powerbi.com/getdata/services/azure-enterprise) gidin.
1. Geçerli bir iş veya okul hesabıyla oturum açın.

   Bu iş veya okul hesabı, Azure Enterprise Portal üzerinden kayda erişmek için kullandığınız hesapla aynı veya ondan farklı olabilir.
1. Hizmet panosunda **Microsoft Azure Kurumsal**'ı seçin ve **Bağlan**'ı seçin.
1. **Azure Enterprise'a Bağlan** ekranındaki alanları doldurun:
    - Azure Ortamı URL'si: [https://ea.azure.com](https://ea.azure.com/)
    - Ay Sayısı: 1 ile 36 arasında
    - Kayıt Numarası: kayıt numaranızı girin
1. **İleri**’yi seçin.
1. **Kimlik Doğrulama Anahtarı** kutusuna API anahtarını girin.

    Buraya girmeniz gereken API anahtarını Azure Enterprise Portal'ın **Kullanımı İndir** sekmesinden alabilirsiniz. **API Erişim Anahtarı**'nı seçin ve anahtarı **Hesap Anahtarı** kutusuna yapıştırın.
1. Veri kümelerinin boyutuna bağlı olarak verilerin Power BI'a yüklenmesi 5-30 dakika arasında sürebilir.

### <a name="glossary-of-processes-for-calculating-the-resource-rate"></a>Kaynak ücretini hesaplamak için kullanılan işlemlerle ilgili sözlük

- **Toplam Ham Birim Sayısı:** Ayrıntılı kullanım raporundaki tüketilen miktar değeridir.
- **Birim Başına MOCP Kaynağı:** Yukarı akış kullanım sistemi, her hizmet için kullanımları farklı birimlerde yayar. (Önceden ayarlanmış)
- **Birim Başına Tüketim:** Azure Enterprise ölçü birimidir. (Önceden ayarlanmış)
- **Fiyat:** Azure Enterprise Portal'daki birim fiyattır.
- **Toplam Maliyet:** Azure Enterprise Portal'dan alınan ayrıntılı kullanım raporu veya taahhüt kullanımına fazla kullanımın eklenmesi ile bulunan genişletilmiş maliyettir.

### <a name="charges-calculations"></a>Ücretlerin hesaplanması

- **Tüketilen MOCP kaynağına dönüştürme** = `ROUND(Total RAW Units * MOCP Resource Per Unit,4)`
- **Tüketilen birim sayısına dönüştürme** = `Consumed MOCP Resources / Consumption per Unit`
- **Toplam maliyeti hesaplama** = `Consumed Units * Price`

### <a name="logic-in-the-usage-calculation-logic"></a>Kullanım Hesaplama Mantığı

**Kaynak Ücreti** = `Total Cost /(Total RAW Units / MOCP Resource Per Unit)`

Kaynak ücreti, tahsil edilen ücretlere göre hesaplanır. Fiyat listesindeki gerçek birim fiyat ile aynı olmayabilir.

İndirilen kullanım verileri raporunda ham kullanım verileri için altı ondalık basamak bulunur. Bu veriler, fazla kullanım ücretlerinin hesaplanması için kullanılır. Ancak Azure Enterprise Portal'da gösterilen veriler, kullanımı taahhüt birimleri için dört ondalık basamağa yuvarlar, fazla kullanım birimlerinde de tüm ondalık basamakları siler. Azure Enterprise Portal'da tüm fazla kullanım yalnızca tam birimler üzerinden ücretlendirilir. Birim fiyat ile fazla kullanım veya karışık aylar içinde ücretlendirilen kullanıma ait kaynak ücreti arasında büyük bir fark görebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Faturalanmış ücretleri gözden geçirme
> * Hizmet fazla kullanım ücretlerini gözden geçirme
> * Market faturasını inceleme

Azure EA portalı hakkında daha fazla bilgi edinmek için bir sonraki makaleye geçin.

> [!div class="nextstepaction"]
> [Azure EA portalı ile çalışmaya başlama](../manage/ea-portal-get-started.md)