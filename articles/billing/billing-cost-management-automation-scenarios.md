---
title: Azure faturalama ve maliyet yönetimi için otomasyon senaryoları | Microsoft Docs
description: Yaygın faturalama ve maliyet yönetimi senaryolarının farklı API'lere nasıl eşlendiğini öğrenin.
services: billing
documentationcenter: ''
author: bandersmsft
manager: dougeby
editor: ''
tags: billing
ms.assetid: 204b15b2-6667-4b6c-8ea4-f32c06f287fd
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 54c4426fb8bdb1476b33defb5351b3fb0e6a0663
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718844"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Faturalama ve maliyet yönetimi için otomasyon senaryoları

Bu makalede Azure faturalama ve maliyet yönetimi için yaygın senaryolar listelenir. Bu senaryolar kullanabileceğiniz API'lerle eşlenir. Her senaryo-API eşlemesinde API'lerin özetini ve sağladıkları işlevleri bulabilirsiniz.

## <a name="common-scenarios"></a>Genel senaryolar

Maliyetle ilgili ve kullanımla ilgili soru ları yanıtlamak için faturalama ve maliyet yönetimi API'lerini çeşitli senaryolarda kullanabilirsiniz. Yaygın senaryolar genel hatlarıyla şöyledir:

- **Fatura mutabakatı**: Microsoft beni doğru miktarda mı ücretlendirdi?  Faturam ne kadardır ve bunu kendim hesaplayabilir miyim?

- **Çapraz ücretler**: Artık ne kadar ücretlendirildiğimi biliyorum, kuruluşumda kimin ödemesi gerekiyor?

- **Maliyet iyileştirmesi**: Ne kadar ücretlendirildiğimi biliyorum. Azure'a yaptığım harcamadan nasıl daha iyi yararlanabilirim?

- **Maliyet izleme**: Zaman içinde Azure'a ne kadar harcama yaptığımı ve Azure'ı ne kadar kullandığımı görmek istiyorum. Eğilimler nelerdir? Daha iyisini nasıl yapabilirim?

- **Ay boyunca Azure harcaması**: Geçerli ay içinde bugüne kadar ne kadar harcama yaptım? Azure harcamamda ve/veya kullanımında herhangi bir değişiklik yapmak gerekiyor mu? Ay boyunca ne zaman Azure tüketimim en yüksek düzeye çıkıyor?

- **Uyarılar**: Kaynak tabanlı tüketimi veya para tabanlı uyarıları nasıl ayarlayabilirim?

## <a name="scenario-to-api-mapping"></a>Senaryo-API eşlemesi

|         API        | Fatura mutabakatı    | Çapraz ücretler    | Maliyet iyileştirmesi    | Maliyet izleme    | Ay ortası harcaması    | Uyarılar    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Bütçeler                     |                           |                  |           X          |                  |                    |     X     |
| Market Ücretleri                |             X             |         X        |           X          |         X        |          X         |     X     |
| Fiyat Listesi                 |             X             |         X        |           X          |         X        |          X         |           |
| Rezervasyon Önerileri |                           |                  |           X          |                  |                    |           |
| Rezervasyon Ayrıntıları         |                           |                  |           X          |         X        |                    |           |
| Rezervasyon Özetleri       |                           |                  |           X          |         X        |                    |           |
| Kullanım Ayrıntıları               |             X             |         X        |           X          |         X        |          X         |     X     |
| Fatura Dönemleri             |             X             |         X        |           X          |         X        |                    |           |
| Faturalar                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Derecelendirilmemiş Kullanım               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> Senaryo-API eşlemesi Kurumsal Tüketim API'leri içermez. Mümkün olduğunca yeni geliştirme senaryoları için genel Tüketim API'lerini kullanın.

## <a name="api-summaries"></a>API özetleri

### <a name="consumption"></a>Tüketim
Web Direct ve Enterprise müşterileri tersi belirtilmediği sürece aşağıdaki API'lerin tümünü kullanabilir:

-   [Bütçe API'si](https://docs.microsoft.com/rest/api/consumption/budgets) (*Yalnızca Enterprise müşterileri*): Kaynaklar, kaynak grupları veya faturalama ölçümleri için maliyet veya kullanım bütçeleri oluşturun. Bütçeleri oluştururken, tanımlı bütçe eşiklerini aştığınızda size bildirilmesi için uyarıları yapılandırabilirsiniz. Ayrıca bütçe tutarlarına ulaştığınızda bazı eylemlerin gerçekleştirilmesini de yapılandırabilirsiniz.

-   [Market Ücretleri API'si](https://docs.microsoft.com/rest/api/consumption/marketplaces): Tüm Azure Market kaynakları (Azure iş ortağı teklifleri) ile ilgili ücret ve kullanım verileri alın. Bu verileri kullanarak tüm Market kaynaklarının maliyetlerini toplayabilir veya belirli kaynakların maliyet/kullanım durumunu araştırabilirsiniz.

-   [Fiyat Listesi API'si](https://docs.microsoft.com/rest/api/consumption/pricesheet) (*Yalnızca Enterprise müşterileri*): Tüm ölçümler için özel fiyatlandırmayı alın. Kuruluşlar bu verileri kullanım ayrıntılarıyla ve market kullanım bilgileriyle birlikte kullanarak, kullanım ve market verileri aracılığıyla maliyetleri hesaplayabilir. 

-   [Rezervasyon Önerileri API'si](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations): Ayrılmış VM Örnekleri satın alma önerileri alın. Öneriler beklenen maliyet tasarruflarını ve satın alma tutarlarını analiz etmenize yardımcı olur. Daha fazla bilgi için bkz. [Azure rezervasyon otomasyonu API’leri](billing-reservation-apis.md).

-   [Rezervasyon Ayrıntıları API'si](https://docs.microsoft.com/rest/api/consumption/reservationsdetails): Daha önce satın alınmış VM rezervasyonları hakkında, ne kadar tüketimin ayrıldığı ve ne kadarının kullanıldığı gibi bilgileri görün. Verileri VM ayrıntı düzeyinde görebilirsiniz. Daha fazla bilgi için bkz. [Azure rezervasyon otomasyonu API’leri](billing-reservation-apis.md).

-   [Rezervasyon Özetleri API'si](https://docs.microsoft.com/rest/api/consumption/reservationssummaries): Kuruluşunuzun satın aldığı VM rezervasyonları hakkında, ne kadar tüketimin ayrıldığı ve toplamda ne kadarının kullanıldığı gibi toplama bilgilerini görün. Daha fazla bilgi için bkz. [Azure rezervasyon otomasyonu API’leri](billing-reservation-apis.md).

-   [Kullanım Ayrıntıları API'si](https://docs.microsoft.com/rest/api/consumption/usagedetails): Microsoft'un sağladığı tüm Azure kaynaklarıyla ilgili ücret ve kullanım bilgilerini alın. Bilgiler şu anda ölçüm başına günde bir kez gösterilen kullanım ayrıntısı kayıtları biçimindedir. Bilgileri kullanarak tüm kaynakların maliyetlerini toplayabilir veya belirli kaynakların maliyet/kullanım durumunu araştırabilirsiniz.

-   [RateCard API'si](/previous-versions/azure/reference/mt219005(v=azure.100)): Web Direct müşterisiyseniz ölçüm ücretlerini alın. Ardından döndürülen bilgileri kaynak kullanım bilgilerinizle birlikte kullanarak beklenen faturayı el ile hesaplayabilirsiniz. 

-   [Derecelendirilmemiş Kullanım API'si](/previous-versions/azure/reference/mt219003(v=azure.100)): Azure herhangi bir ölçüm/ücretlendirme yapmadan önce ham kullanım bilgilerini alın.

### <a name="billing"></a>Faturalandırma
-   [Fatura Dönemleri API'si](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Analiz edilecek faturalama dönemini ve bu döneme ait fatura kimliklerini belirleyin. Fatura kimliklerini Faturalar API'siyle kullanabilirsiniz.

-   [Faturalar API'si](https://docs.microsoft.com/rest/api/billing/2018-11-01-preview/invoices): Faturalama dönemine ait PDF biçiminde bir fatura için indirme URL'sini alın.

### <a name="enterprise-consumption"></a>Kuruluş tüketimi
Aşağıdaki API'ler yalnızca Kuruluş içindir:

-   [Bakiye Özeti API'si](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): Bakiyeler, yeni satın almalar, Azure Market hizmeti ücretleri, düzeltmeler ve fazla kullanım ücretleri hakkındaki bilgilerin aylık özetini alın. Bu bilgileri geçerli faturalama dönemi veya geçmişe ait bir dönem için alabilirsiniz. Kuruluşlar el ile hesaplanan özet ücretleriyle karşılaştırmak için bu verileri kullanabilir. Bu API kaynağa özgü bilgiler veya maliyetlerin toplam görünümünü sağlamaz.

-   [Kullanım Ayrıntıları API'si](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): Geçerli ay, belirli bir faturalama dönemi veya özel bir tarih aralığı için Azure kullanımı (Microsoft tekliflerinin kullanımı) hakkındaki bilgileri alabilirsiniz. Kuruluşlar bu verileri kullanarak fiyat ve tüketim temelinde aylık faturaları el ile hesaplayabilir. Kuruluşlar, organizasyonlar genelindeki maliyetlere atıfta bulunmak için bölüm/kuruluş bilgilerini de kullanabilir. Veriler kaynağa özgü kullanım/maliyet görünümü sağlar.

-   [Market Mağazası Ücreti API'si](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): Geçerli ay, belirli bir faturalama dönemi veya özel bir tarih aralığı için Azure kullanımı (iş ortağı tekliflerinin kullanımı) hakkındaki bilgileri alabilirsiniz. Kuruluşlar bu verileri kullanarak fiyat ve tüketim temelinde aylık faturaları el ile hesaplayabilir. Kuruluşlar, organizasyonlar genelindeki maliyetlere atıfta bulunmak için bölüm/kuruluş bilgilerini de kullanabilir. API kaynağa özgü kullanım/maliyet görünümü sağlar.

-   [Fiyat Listesi API'si](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): Belirtilen kayıt ve faturalama dönemi için her ölçümün geçerli fiyatını alın. Bu fiyat bilgilerini kullanım ayrıntıları ve market kullanım bilgileriyle birlikte kullanarak beklenen faturayı el ile hesaplayabilirsiniz.

-   [Fatura Dönemleri API'si](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): Faturalama dönemlerinin listesini alın. API size faturalama dönemine ait dört Kurumsal API veri kümesi için API yoluna işaret eden bir de özellik verir: BalanceSummary, UsageDetails, Marketplace Charges ve PriceSheet.

-   [Ayrılmış Örnek Önerileri API'si](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): 7 günlük, 30 günlük veya 60 günlük sanal makine kullanımına bakın ve Tek veya Paylaşılan Satın Alma önerileri alın. Bu API'yi kullanarak beklenen maliyet tasarruflarını ve önerilen satın alma tutarlarını analiz edebilirsiniz. Daha fazla bilgi için bkz. [Azure rezervasyon otomasyonu API’leri](billing-reservation-apis.md).

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Kurumsal Raporlama API'leriyle Tüketim API'leri arasında ne fark vardır? Bunların her birini hangi durumlarda kullanmalıyım?
Bu API'lerin benzer bir işlev kümesi vardır ve faturalama ve maliyet yönetimi alanında aynı kapsamlı soruları yanıtlayabilirler. Ama hedef kitleleri farklıdır: 

- Kurumsal Raporlama API'leri, Microsoft'la kendilerine anlaşmalı parasal taahhütler ve özel fiyatlandırma getiren bir Kurumsal Anlaşma imzalamış müşterilere yöneliktir. API'ler için [Enterprise Portal](https://ea.azure.com)'dan alabileceğiniz bir anahtar gerekir. Bu API'lerin açıklaması için bkz. [Enterprise müşterileri için Raporlama API'lerine genel bakış](billing-enterprise-api.md).

- Tüketim API'leri birkaç özel durum dışında tüm müşteriler tarafından kullanılabilir. Daha fazla bilgi için bkz. [Azure tüketim API'sine genel bakış](billing-consumption-api-overview.md) ve [Azure Tüketim API'si başvurusu](https://docs.microsoft.com/rest/api/consumption/). En son geliştirme senaryolarına çözüm olarak sağlanan API'lerin kullanılmasını öneririz. 

### <a name="whats-the-difference-between-the-usage-details-api-and-the-usage-api"></a>Kullanım Ayrıntıları API'siyle Kullanım API'si arasında ne fark vardır?
Bu API'ler temelde farklı veriler sağlar:

- [Kullanım Ayrıntıları API'si](https://docs.microsoft.com/rest/api/consumption/usagedetails) ölçüm örneği başına Azure kullanım ve maliyet bilgilerini sağlar. Sağlanan veriler Azure'da maliyet ölçüm sisteminden zaten geçirilmiştir ve bunlara diğer olası değişikliklerle birlikte maliyet de uygulanmıştır:

   - Ön ödemeli parasal taahhütler için hesapta yapılan değişiklikler
   - Azure tarafından bulunan kullanım tutarsızlıkları için hesapta yapılan değişiklikler

- [Kullanım API'si](/previous-versions/azure/reference/mt219003(v=azure.100)) Azure'da maliyet ölçüm sisteminden geçirilmeden önceki ham Azure kullanım bilgilerini sağlar. Bu verilerin, Azure ücret ölçüm sisteminden sonra görülen kullanım ve ücret miktarıyla hiçbir bağıntısı olmayabilir.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Fatura API'siyle Kullanım Ayrıntıları API'si arasında ne fark vardır?
Bu API'ler aynı verilerin farklı görünümünü sağlar:

- [Fatura API'si](https://docs.microsoft.com/rest/api/billing/2018-11-01-preview/invoices) yalnızca Web Direct müşterilerine yöneliktir. Her ölçüm türü için toplanan ücretlere dayanarak faturanızın aylık toplamını sağlar. 

- [Kullanım Ayrıntıları API'si](https://docs.microsoft.com/rest/api/consumption/usagedetails) her gün için kullanım/maliyet kayıtlarının ayrıntılı görünümünü sağlar. Hem Enterprise hem de Web Direct müşterileri bunu kullanabilir.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Fiyat Listesi API'siyle RateCard API'si arasında ne fark vardır?
Bu API'ler benzer veri kümeleri sağlar ama hedef kitleleri farklıdır:

- [Fiyat Listesi API'si](https://docs.microsoft.com/rest/api/consumption/pricesheet) Enterprise müşterisi için üzerinde anlaşmaya varılmış özel fiyatlandırmayı sağlar.

- [RateCard API'si](/previous-versions/azure/reference/mt219005(v=azure.100)) Web Direct müşterilerine uygulanan herkese açık fiyatlandırmayı sağlar.

## <a name="next-steps"></a>Sonraki adımlar

- Azure API'lerini kullanarak program aracılığıyla Azure kullanımınızla ilgili içgörü elde etme hakkında bilgi için bkz. [Azure Tüketim API'sine genel bakış](billing-consumption-api-overview.md) ve [Azure Faturalama API'sine genel bakış](billing-usage-rate-card-overview.md).

- Azure portalında faturanızı ayrıntılı günlük kullanım dosyası ve maliyet yönetimi raporlarıyla karşılaştırmak için bkz. [Microsoft Azure faturanızı anlama](billing-understand-your-bill.md).

- Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).
