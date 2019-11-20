---
title: Azure maliyet yönetimi verilerini anlama | Microsoft Docs
description: Bu makale, Azure maliyet yönetimi 'ne dahil edilen verileri ve ne sıklıkta işlendiğini, toplandığını, gösterildiğini ve kapatıldığını daha iyi anlamanıza yardımcı olur.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: bf3c2224361cd2890a0b8b4686506eac6858d9cb
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122993"
---
# <a name="understand-cost-management-data"></a>Maliyet Yönetimi verilerini anlama

Bu makale, Azure maliyet yönetimi 'ne dahil edilen Azure maliyet ve kullanım verilerini daha iyi anlamanıza yardımcı olur. Verilerin ne sıklıkta işlendiğini, toplanacağını, gösterildiğini ve kapatıldığını açıklar. Azure kullanım için aylık olarak faturalandırılırsınız. Fatura döngüleri aylık dönemler olsa da, döngü başlangıç ve bitiş tarihleri abonelik türüne göre farklılık gösterir. Maliyet yönetiminin kullanım verilerini alma sıklığı, farklı faktörlere göre farklılık gösterir. Bu tür etkenler, verilerin işlenmesi için geçen süreyi ve Azure hizmetlerinin kullanım sıklığını faturalandırma sistemine nasıl yayduğunu içerir.

Maliyet yönetimi, Kurumsal Anlaşma (EA) hesapları için rezervasyonlar ve üçüncü taraf teklifleri dahil olmak üzere tüm kullanım ve satın alma işlemlerini içerir. Kullandıkça Öde tarifelerine sahip Microsoft müşteri anlaşması hesapları ve bireysel abonelikler yalnızca Azure ve Market hizmetlerinden kullanım içerir. Destek ve diğer maliyetler dahil değildir. Bir fatura oluşturuluncaya kadar maliyetler tahmin edilir ve krediler üzerinde etken olmaz.

## <a name="supported-microsoft-azure-offers"></a>Desteklenen Microsoft Azure teklifleri

Aşağıdaki bilgiler, Azure maliyet yönetimi 'nde Şu anda desteklenen [Microsoft Azure tekliflerini](https://azure.microsoft.com/support/legal/offer-details/) gösterir. Azure teklifi, sahip olduğunuz Azure aboneliğinin türüdür. Veriler, tarihte **bulunan verilerden** Itibaren maliyet yönetimi 'nde kullanılabilir. Bir abonelik değişirse, teklif değişikliği tarihinden önceki maliyetler kullanılabilir olmayacaktır.

| **Kategori**  | **Teklif adı** | **Kota KIMLIĞI** | **Teklif numarası** | **Verilerin kullanılabildiği yer** |
| --- | --- | --- | --- | --- |
| **Azure Devlet Kurumları** | Azure Kamu kuruluşu                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | <sup>2014 mayıs</sup> |
| **Kurumsal Anlaşma (EA)** | Kurumsal Geliştirme ve Test                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | <sup>2014 mayıs</sup> |
| **Kurumsal Anlaşma (EA)** | [Microsoft Azure Kurumsal](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | <sup>2014 mayıs</sup> |
| **Microsoft Müşteri Sözleşmesi** | [Microsoft Azure planı](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | Yok | Mart 2019<sup>3</sup> |
| **Microsoft Müşteri Sözleşmesi** | [Geliştirme ve test için plan Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | Yok | Mart 2019<sup>3</sup> |
| **İş ortakları tarafından desteklenen Microsoft Müşteri Sözleşmesi** | Microsoft Azure Planı | CSP_2015-05-01, CSP_MG_2017-12-01 ve CSPDEVTEST_2018-05-01<br><br>Kota KIMLIĞI, Microsoft Müşteri Sözleşmesi ve eski CSP abonelikleri için yeniden kullanılır. Şu anda yalnızca Microsoft Müşteri Sözleşmesi abonelikleri desteklenir. | Yok | Ekim 2019 |
| **Microsoft Developer Network (MSDN)** | [MSDN Platformları](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2 Ekim 2018<sup>2</sup> |
| **Kullandıkça Öde** | [Kullandıkça Öde](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2 Ekim 2018<sup>2</sup> |
| **Kullandıkça Öde** | [Kullandıkça Öde Geliştirme ve Test](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2 Ekim 2018<sup>2</sup> |
| **Kullandıkça Öde** | [Microsoft İş Ortağı Ağı](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 2 Ekim 2018<sup>2</sup> |
| **Kullandıkça Öde** | [Ücretsiz deneme](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2 Ekim 2018<sup>2</sup> |
| **Kullandıkça Öde** | [Open ile Azure](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2 Ekim 2018<sup>2</sup> |
| **Kullandıkça Öde** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P-MS-AZR-0125P, MS-AZR-0128P-MS-AZR-0130P | 2 Ekim 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2 Ekim 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2 Ekim 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2 Ekim 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2 Ekim 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2 Ekim 2018<sup>2</sup> |

_<sup>**1**</sup> Mayıs 2014 tarihinden önceki veriler Için [Azure Enterprise Portal](https://ea.azure.com)' ı ziyaret edin._

_<sup>**2 ekim**</sup> 2018 tarihinden önceki veriler için [Azure Hesap Merkezi](https://account.azure.com/subscriptions)ziyaret edin._

_<sup>**3**</sup> Microsoft Müşteri anlaşmaları Mart 2019 ' de başlatılmış ve bu noktadan önce geçmiş verisi yok._

_Kredi tabanlı ve kullandıkça öde abonelikleri için <sup>**4**</sup> geçmiş veriler faturanızdan eşleşmeyebilir. Bkz. [geçmiş verileri, aşağıdaki faturayla eşleşmeyebilir](#historical-data-might-not-match-invoice) ._

Şu teklifler henüz desteklenmiyor:

| Kategori  | **Teklif adı** | **Kota KIMLIĞI** | **Teklif numarası** |
| --- | --- | --- | --- |
| **Azure Almanya** | [Azure Almanya Kullandıkça öde](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Devlet Kurumları** | Azure Kamu Kullandıkça Öde | PayAsYouGo_2014-09-01 | MS-AZR-USGOV-0003P |
| **Bulut çözümü sağlayıcısı (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Bulut çözümü sağlayıcısı (CSP)** | Azure Kamu CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Bulut çözümü sağlayıcısı (CSP)** | Microsoft Bulut Almanya için CSP’de Azure Almanya   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Kullandıkça Öde**                 | Öğrenciler için Azure Başlangıç | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Kullandıkça Öde** | [Öğrenciler Için Azure](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Kullandıkça Öde**                 | [Microsoft Azure Sponsorluğu](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Destek Planları** | Standart destek                    | Default_2014-09-01 | MS-AZR-0041P |
| **Destek Planları** | Profesyonel Doğrudan desteği         | Default_2014-09-01 | MS-AZR-0042P |
| **Destek Planları** | Geliştirici desteği                   | Default_2014-09-01 | MS-AZR-0043P |
| **Destek Planları** | Almanya destek planı                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Destek Planları** | Azure Kamu Standart Destek   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Destek Planları** | Azure Kamu Pro-Direct desteği | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Destek Planları** | Azure Kamu Geliştirici Desteği  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>Teklif türü belirleme
Bir abonelik için veri görmüyorsanız ve aboneliğinizin desteklenen tekliflerin altında olup olmadığını öğrenmek istiyorsanız, aboneliğinizin desteklendiğini doğrulayabilirsiniz. Bir Azure aboneliğinin desteklendiğini doğrulamak için [Azure Portal](https://portal.azure.com)oturum açın. Ardından sol menü bölmesinde **tüm hizmetler** ' i seçin. Hizmetler listesinde **abonelikler**' i seçin. Abonelik listesi menüsünde, doğrulamak istediğiniz aboneliğe tıklayın. Aboneliğiniz Genel Bakış sekmesinde gösterilir ve **teklif** ve **teklif kimliğini**görebilirsiniz. Aşağıdaki resimde bir örnek gösterilir.

![Teklif ve teklif KIMLIĞINI gösteren aboneliğe Genel Bakış sekmesi örneği](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Maliyet yönetimi 'ne dahil edilen maliyetler

Aşağıdaki tablolarda, maliyet yönetimi dahil edilen veya olmayan veriler gösterilmektedir. Bir fatura üretilene kadar tüm maliyetler tahmin edilir. Gösterilen maliyetler ücretsiz ve ön ödemeli krediler içermez.

**Maliyet ve kullanım verileri**

| **Verilen** | **Dahil değildir** |
| --- | --- |
| Azure hizmeti kullanımı<sup>5</sup>        | Destek ücretleri-daha fazla bilgi Için bkz. [Fatura koşulları açıklanmıştı](../billing/billing-understand-your-invoice.md). |
| Market teklifi kullanımı<sup>6</sup> | Vergiler-daha fazla bilgi Için bkz. [Fatura koşulları açıklanmıştı](../billing/billing-understand-your-invoice.md). |
| Market satınalmaları<sup>6</sup>      | Jenerik-daha fazla bilgi Için bkz. [Fatura koşulları açıklanmıştı](../billing/billing-understand-your-invoice.md). |
| Rezervasyon satınalmaları<sup>7</sup>      |  |
| Rezervasyon satınalmaları<sup>7 ' yi</sup> İtfası      |  |

_<sup>**5**</sup> Azure hizmeti kullanımı, rezervasyon ve anlaşmalı fiyatlara göre belirlenir._

_<sup>**6**</sup> Market satın alma, Kullandıkça öde, MSDN ve Visual Studio 'da Şu anda ücretsiz olarak sunulmamaktadır._

_<sup>**7**</sup> rezervasyon satın alma işlemleri yalnızca şu anda kurumsal anlaşma (EA) hesapları için kullanılabilir._

**Veriyi**

| **Verilen** | **Dahil değildir** |
| --- | --- |
| Kaynak etiketleri<sup>8</sup> | Kaynak grubu etiketleri |

_<sup>**8**</sup> kaynak etiketi, kullanım her bir hizmetten yayıldığından ve daha etkin bir şekilde geçmiş kullanım için kullanılabilir olmadığı için geçerlidir._

## <a name="rated-usage-data-refresh-schedule"></a>Derecelendirilen kullanım verilerini yenileme zamanlaması

Maliyet ve kullanım verileri, Azure portal ve [destekleyici API](index.yml)'Lerde maliyet yönetimi + Faturalandırma bölümünde mevcuttur. Maliyetleri gözden geçirdikten sonra aşağıdaki noktaları göz önünde bulundurun:

- Geçerli fatura dönemi için tahmini ücretler günde altı kez güncelleştirilir.
- Geçerli fatura dönemi için tahmini ücretler, daha fazla kullanım yaptığınız sürece değişebilir.
- Her güncelleştirme birikimlidir ve önceki güncelleştirmedeki tüm satır öğelerini ve bilgileri içerir.
- Azure, fatura dönemi bittikten sonra geçerli faturalandırma dönemini 72 saate kadar (üç takvim günü) sonlandırır veya _kapatır_ .

Aşağıdaki örneklerde, faturalandırma dönemlerinin nasıl bitemediği gösterilmektedir.

Kurumsal Anlaşma (EA) abonelikleri – fatura ayı 31 Mart tarihinde biterse tahmini ücretler 72 saat daha sonra güncelleştirilir. Bu örnekte, gece yarısı (UTC) 4 Nisan.

Kullandıkça Öde abonelikleri – faturalama ayı 15 Mayıs 'ta biterse tahmini ücretler 72 saat daha sonra güncelleştirilir. Bu örnekte, gece yarısı (UTC) 19 Mayıs 'ta olabilir.

### <a name="rerated-data"></a>Yeniden yönlendirme verileri

Veri almak için [maliyet yönetimi API 'lerini](index.yml), Power BI veya Azure Portal kullanıp, geçerli fatura döneminin ücretlendirildiği ücretleri, sonra da fatura kapatılana kadar değişiklik yapın.

## <a name="usage-data-update-frequency-varies"></a>Kullanım verileri güncelleştirme sıklığı farklılık gösterir

Maliyet yönetiminde, tahakkuk eden kullanım verilerinin kullanılabilirliği, aşağıdakiler dahil olmak üzere birkaç etkene bağlıdır:

- Azure hizmetlerinin (depolama, Işlem, CDN ve SQL) kullanım sıklığı.
- Kullanım verilerini derecelendirme altyapısı ve maliyet yönetimi ardışık düzenleri aracılığıyla işlemek için geçen süre.

Bazı hizmetler, kullanımı diğerlerinden daha sık yayın. Bu nedenle, verileri daha az sıklıkta sunan diğer hizmetlerden daha önce bazı hizmetler için maliyet yönetimi 'nde verileri görebilirsiniz. Genellikle, hizmetler için kullanım, maliyet yönetiminde 8-24 saat sürer. Güncelleştirmeler birikimli olduğundan, açık bir aya ait verilerin yenilendiğine dikkat edin.

## <a name="historical-data-might-not-match-invoice"></a>Geçmiş verileri faturayla eşleşmeyebilir

Kredi tabanlı ve kullandıkça öde teklifleri için geçmiş verileri faturanızdan eşleşmeyebilir. Azure Kullandıkça öde, MSDN ve Visual Studio tekliflerinizi, faturada Azure kredileri ve gelişmiş ödemeler uygulamış olabilir. Ancak, maliyet yönetimi 'nde gösterilen geçmiş verileri yalnızca tahmini tüketim ücretlerine göre belirlenir. Maliyet yönetimi geçmiş verileri ödemeler ve krediler içermez. Sonuç olarak, aşağıdaki teklifler için gösterilen geçmiş verileri faturanızdan tam olarak eşleşmeyebilir.

- Öğrenciler için Azure (MS-AZR-0170P)
- Open ile Azure (MS-AZR-0111P)
- Azure Pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Ücretsiz deneme (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-azr-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Ayrıca bkz.

- Maliyet yönetimi için zaten ilk hızlı tamamlamadıysanız, hem okuma [maliyetleri başlamanızı](quick-acm-cost-analysis.md).
