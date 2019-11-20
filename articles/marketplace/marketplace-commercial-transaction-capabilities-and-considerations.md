---
title: Market Ticari Işlem özellikleri ve konuları | Mavisi
description: Bu makalede, bir teklif türü için Transact fiyatlandırma, faturalandırma, faturalandırma ve ödeme konuları açıklanmaktadır.
services: Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
author: yijenj
manager: nuno costa
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/29/2018
ms.author: pabutler
ms.openlocfilehash: 9aa41e63c275737874d57ba016e297a64f3eb124
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823012"
---
# <a name="commercial-marketplace-transaction-capabilities-and-considerations"></a>Ticari Market işlem özellikleri ve konuları

Bu makalede ticari Market için aşağıdaki ticaret ile ilgili konular ele alınmaktadır

* Market Yayımlama seçenekleri
* Transact genel bakış
* Transact faturalandırma modelleri
* Transact gereksinimleri

## <a name="marketplace-publishing-options"></a>Market Yayımlama seçenekleri

Aşağıdaki yayımlama seçenekleri ticari Market yayımcıları için kullanılabilir.

### <a name="list--trial-publishing-options"></a>& Deneme yayımlama seçeneklerini listeleyin

Yayımcılar, promosyon ve Kullanıcı alma amaçları için liste, deneme ve KLG yayımlama seçeneklerinden yararlanabilir. Bu seçeneklerle, Microsoft doğrudan yayımcının yazılım lisansı işlemlerine katılmaz ve ilişkili işlem ücreti yoktur. Yayımcılar, sipariş, karşılama, ölçüm, faturalandırma, faturalama, ödeme ve koleksiyon dahil olmak üzere, yazılım lisans işleminin tüm yönlerini desteklemekten sorumludur. Liste ve deneme yayımlama seçenekleriyle, yayımcılar, müşteriden toplanan yayımcı yazılım lisansı ücretleri %100 ' i tutar. 

### <a name="transact-publishing-option"></a>Transact yayımlama seçeneği

Liste ve deneme yayımlama seçeneklerine ek olarak, Transact yayımlama seçeneği yayımcılar tarafından kullanılabilir. Bu, Microsoft 'un küresel olarak kullanılabilir ticari olanaklarından yararlanır ve Microsoft 'un bulut Market işlemlerini yayımcı adına barındırmasına imkan tanır.

## <a name="transact-general-overview"></a>Transact genel bakış

Transact yayımlama seçeneği kullanılırken, Microsoft üçüncü taraf yazılımlarının satışı ve bazı teklif türlerinin müşterinin Azure aboneliğine dağıtılması için bir satış sağlar. Yayımcı, bir faturalandırma modeli ve teklif türü seçerken, altyapı ücretlerinin ve yayımcının kendi yazılım lisanslama ücretlerine ilişkin faturalandırmayı göz önünde bulundurmalıdır.

Aşağıdaki teklif türleri için Transact Publishing seçeneği şu anda destekleniyor: sanal makineler, Azure uygulamaları ve SaaS uygulamaları.


![[Azure Market 'te kurumsal anlaşmalar deneyimidir]](./media/marketplace-publishers-guide/Transact-enterprise-deals.png)

### <a name="billing-infrastructure-costs"></a>Faturalandırma altyapısı maliyetleri

**Sanal makineler ve Azure uygulamaları için**

Sanal makineler ve Azure uygulamaları için Azure altyapı kullanım ücretleri müşterinin Azure aboneliğine faturalandırılır.  Altyapı kullanım ücretleri fiyatlandırılır ve müşterinin faturasında yazılım sağlayıcısının lisanslama ücretlerinden ayrı olarak sunulur.

**SaaS uygulamaları için**

SaaS uygulamaları için yayımcı, Azure altyapı kullanım ücretlerini ve yazılım lisanslama ücretlerini tek bir maliyet öğesi olarak hesaba malıdır.  Müşteriye yönelik bir sabit ücret olarak gösterilir. Azure altyapı kullanımı, doğrudan iş ortağı tarafından yönetilir ve faturalandırılır.  Gerçek altyapı kullanım ücretleri müşteri tarafından görülmez.  Yayımcılar genellikle Azure altyapı kullanım ücretlerini yazılım lisans fiyatlandırmasına paketlendirmeyi tercih edebilir.  Yazılım Lisanslama ücretleri tarifeli veya tüketim temelli değildir.

## <a name="transact-billing-models"></a>Transact faturalandırma modelleri

Kullanılan işlem seçeneğine bağlı olarak, yayımcının yazılım lisansı ücretleri aşağıdaki gibi gösterilebilir:  

* Ücretsiz: yazılım lisansları için ücret alınmaz. 

* Kendi lisansınızı getirin (KLG): yazılım lisansları için geçerli ücretler, doğrudan yayımcı ile müşteri arasında yönetilir. Microsoft yalnızca Azure altyapı kullanım ücretleri üzerinden geçer. (Yalnızca sanal makineler ve Azure uygulamaları.)

* Kullandıkça öde: yazılım lisansı ücretleri, kullanılan Azure altyapısına bağlı olarak saat başına, çekirdek başına (vCPU) fiyatlandırma ücreti olarak sunulur. Bu yalnızca sanal makineler ve Azure uygulamaları için geçerlidir.

* • Abonelik fiyatlandırması: yazılım lisansı ücretleri aylık veya yıllık olarak, sabit bir fiyat veya bilgisayar başına faturalandırılan yinelenen ücret olarak sunulur. Bu yalnızca SaaS uygulamaları ve Azure uygulamaları tarafından yönetilen uygulamalar için geçerlidir.

* Ücretsiz yazılım deneme sürümü: 30 günlük veya 90 gün için yazılım lisansları için ücret alınmaz.

### <a name="free-and-bring-your-own-license-byol-pricing"></a>Ücretsiz ve kendi lisansını getir (KLG) fiyatlandırması

Ücretsiz veya kendi lisans işlem teklifini yayımlarken, Microsoft, yazılım lisans ücretlerinizin satış işlemini kolaylaştırmaya yönelik bir rol oynamaz. Yayımcı, liste ve deneme yayımlama seçenekleri gibi yazılım lisansı ücretlerine %100 oranında devam eder. 

### <a name="pay-as-you-go-and-subscription-site-based-pricing"></a>Kullandıkça öde ve abonelik (site tabanlı) fiyatlandırması

Kullandıkça Öde veya abonelik işlemi teklifi yayımlandığında, Microsoft yazılım lisansı satın alma, iade ve geri ödeme işlemlerini işleyecek teknoloji ve hizmetler sağlar. Bu senaryoda, yayımcı Microsoft 'un bu amaçlar için bir aracı görevi görmesini yetkilendirir. Yayımcı, Microsoft 'un yazılım lisanslama işlemini kolaylaştırmasına olanak sağlarken satıcı, sağlayıcı, dağıtıcı ve lisans verme özelliklerini korur.

Microsoft, müşterilerin, Microsoft 'un ticari marketi ve yayımcısının Son Kullanıcı lisanslama sözleşmesinin hüküm ve koşullarına subjecting, yayımcı yazılımını sipariş etmesini, lisanslamasını ve kullanmasına olanak sağlar. Yayımcılar, Son Kullanıcı lisanslama anlaşmasını sağlamalıdır veya teklifi oluştururken [Standart sözleşmeyi](https://docs.microsoft.com/azure/marketplace/standard-contract) seçmelidir.


### <a name="free-software-trials"></a>Ücretsiz yazılım denemeleri

Transact yayımlama senaryolarında, Yayımcı, yazılım lisansını 30 gün veya 90 gün boyunca ücretsiz kullanılabilir hale getirir. Bu ayırma özelliği, iş ortağı çözümü kullanılarak yönlendirilen Azure altyapı kullanımının maliyetini içermez.

### <a name="private-offers"></a>Özel teklifler

Bir teklifi ayırmak için teklif türlerini ve faturalandırma modellerini kullanmanın yanı sıra, yayımcılar özel bir teklifi kullanarak, anlaşmalı, anlaşmaya özgü fiyatlandırmayla veya özel yapılandırmalara göre tamamlanmış olabilir. Özel teklifler, tüm 3 Transact yayımlama seçenekleri tarafından desteklenir.

Bu seçenek, genel kullanıma sunulan teklife göre daha yüksek veya daha düşük fiyatlandırmayı sağlar. Özel teklifler, indirim için veya bir teklif için Premium eklemek üzere kullanılabilir. Özel teklifler, Azure aboneliklerini teklif düzeyinde listeleyerek beyaz bir veya daha fazla müşterinin kullanımına sunulabilir.


### <a name="examples"></a>Örnekler

**Kullandıkça Öde** 

* Kullandıkça Öde seçeneğini etkinleştirirseniz, aşağıdaki maliyet yapısına sahip olursunuz.

|Lisans maliyetiniz  | $1,00/saat  |
|---------|---------|
|Azure kullanım maliyeti (D1/1-çekirdek)    |   $0,14/saat     |
|*Müşteri Microsoft tarafından faturalandırılır*    |  *$1,14/saat*       |

* Bu senaryoda, Microsoft, yayımlanmış VM Görüntünüzün kullanımı için saat başına $1,14.

|Microsoft faturaları  | $1,14/saat  |
|---------|---------|
|Microsoft, lisans maliyetinizi %80 oranında ödetir|   $0,80/saat     |
|Microsoft, lisans maliyetlerinizin %20 ' sini tutar  |  $0,20/saat       |
|Microsoft, Azure kullanım maliyetinin %100 ' ünü tutar | $0,14/saat |

**Kendi lisansınızı getirin (KLG)**

* KLG seçeneğini etkinleştirirseniz, aşağıdaki maliyet yapısına sahip olursunuz.

|Lisans maliyetiniz  | Lisans ücreti size anlaştı ve sizin tarafınızdan faturalandırılır  |
|---------|---------|
|Azure kullanım maliyeti (D1/1-çekirdek)    |   $0,14/saat     |
|*Müşteri Microsoft tarafından faturalandırılır*    |  *$0,14/saat*       |

* Bu senaryoda, Microsoft, yayımlanmış VM Görüntünüzün kullanımı için saat başına $0,14.

|Microsoft faturaları  | $0,14/saat  |
|---------|---------|
|Microsoft, Azure kullanım maliyetini korur    |   $0,14/saat     |
|Microsoft, lisans maliyetinizi %0 oranında tutar   |  $0,00/saat       |

**SaaS uygulama aboneliği**

Bu seçenek, Microsoft üzerinden satış için yapılandırılmış olmalıdır ve aylık veya yıllık olarak, sabit bir hızda veya Kullanıcı başına fiyatlandırılabilecek.
• SaaS teklifi için Microsoft 'tan satışı etkinleştir seçeneğini etkinleştirirseniz, aşağıdaki maliyet yapısına sahip olursunuz.

|Lisans maliyetiniz       | $100,00/ay  |
|--------------|---------|
|Azure kullanım maliyeti (D1/1-çekirdek)    | Müşteriye değil doğrudan yayımcıya faturalandırılır |
|*Müşteri Microsoft tarafından faturalandırılır*    |  *ayda $100,00 (Note: Yayımcı, lisans ücretindeki herhangi bir tahakkuk eden veya doğrudan altyapı maliyeti için hesaba başvurmalıdır)*  |

* Bu senaryoda, Microsoft, yazılım lisansınızın $100,00 ' i faturalandırır ve Yayımcı için $80,00 ' i ödeder.
* Azaltılmış Market hizmet ücreti için uygun olan iş ortakları, 2019 2020 Haziran 'a kadar Mayıs 'a kadar olan SaaS teklifleriyle daha düşük bir işlem ücreti görür. Bu senaryoda, Microsoft, yazılım lisansınızın $100,00 ' i faturalandırır ve Yayımcı için $90,00 ' i ödeder.

|Microsoft faturaları  | $100,00/ay  |
|---------|---------|
|Microsoft, lisans maliyetinizi %80 oranında ödetir <br> \* Microsoft, tüm nitelikli SaaS uygulamaları için lisans maliyetinizi %90 oranında ödemelidir   |   $80,00/ay <br> Ayda $90,00 \*    |
|Microsoft, lisans maliyetlerinizin %20 ' sini tutar <br> \* Microsoft, tüm nitelikli SaaS uygulamaları için lisans maliyetinizi %10 oranında tutar.  |  $20,00/ay <br> \* $10,00     |

* **Daha az Market hizmeti ücreti:** Ticari marketi 'nde yayımladığınız belirli SaaS ürünleri için Microsoft, Market hizmet ücretini %20 ' den (Microsoft Publisher anlaşmasında açıklandığı gibi) %10 oranında azaltacak.  Ürününüzün uygun olabilmesi için, ürünlerinizin en az birinin, Microsoft tarafından IP ortak satışı için ayrılan veya IP ortak satışı için tasarlanmış olması gerekir. Bu ay için daha düşük olan bu Market hizmeti ücretini almak için, uygunluk önceki takvim ayının sonundan önce en az beş (5) iş gününe ulaşılmalıdır. Daha az Market hizmeti ücreti, sanal makineler, yönetilen uygulamalar veya ticari Market 'ten sunulan diğer ürünlerden hiçbiri için uygulanmaz.  Bu azaltılan Market hizmet ücreti, Microsoft tarafından 1 Mayıs 2019 ile 30 Haziran 2020 arasında toplanan lisans ücretleri sayesinde nitelikli tekliflerle kullanılabilir.  Bu süreden sonra Market hizmeti ücreti normal miktarına geri döner.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Müşteri Faturalama, ödeme, faturalandırma ve koleksiyonlar

**Faturalama ve ödeme**

Yayımcı, abonelik veya PAYGO yazılım lisansı ücretleri sunmak için müşterinin tercih ettiği faturalandırma yöntemini kullanabilir.

**Kurumsal Anlaşma** 

Müşterinin tercih edilen faturalandırma yöntemi Microsoft Kurumsal Anlaşma ise, yazılım lisans ücretleriniz, Azure 'a özgü kullanım maliyetlerinden ayrı olarak bu faturalandırma yöntemi kullanılarak, listelenen bir maliyet olarak faturalandırılacaktır.

**Kredi kartları ve aylık fatura** 

Müşteriler, kredi kartı ve aylık fatura kullanarak da ödeme yapabilir. Bu durumda, yazılım lisans ücretleriniz, bir Azure 'a özgü kullanım maliyetlerinden ayrı olarak, Kurumsal Anlaşma senaryosu gibi, yeni bir maliyet olarak faturalandırılacaktır.

Örneğin, müşteri kredi kartı kullanarak satın alıyorsa:

|Açıklama    |    Tarih  |
|----------|----------|
|Sipariş dönemi   | 15 Ağu, 2018-30 Ağu, 2018 |
|Dönem sonu (ay)   | 30 Ağu, 2018 |
|Faturalama tarihi | 1 Eylül 2018 |
|Müşteri ödeme tarihi | 1 Eylül 2018 |
|Emanet dönemi (yalnızca kredi kartları, 30 gün) | 1 Eylül 2018-Eyl 30, 2018 |
|Toplama dönemi başlangıcı | 1 Eylül 2018 |
|Toplama dönemi sonu (maksimum, 30 gün) | 30 Eylül 2018 |
|Ödeme hesaplama tarihi (aylık 15 ' te) | 1 Eki, 2018 |
|Ödeme tarihi | 15 Ekim 2018 |

Müşteri bir Kurumsal Anlaşma kullanarak satın alıyorsa:

| Açıklama |    Tarih  |
|----------|----------|
|Sipariş dönemi | 15 Ağu, 2018-30 Ağu, 2018 |
|Dönem sonu (çeyrek) | 30 Eylül 2018 |
|Faturalama tarihi | 15 Ekim 2018 |
|Emanet dönemi (yalnızca kredi kartları, 30 gün) | yok |
|Toplama dönemi başlangıcı | 15 Ekim 2018 |
|Toplama dönemi sonu (maksimum, 90 gün) | 15 Ocak 2019 |
|Müşteri ödeme tarihi | 30 Aralık 2018 |
|Ödeme hesaplama tarihi (aylık 15 ' te) | 15 Ocak 2019 |
|Ödeme tarihi | 15 Şubat 2019 |

**Ücretsiz krediler ve parasal taahhüt** 

Bazı müşteriler Azure 'u Kurumsal Anlaşma bir parasal taahhütte ön ödeme yapmayı tercih ediyor veya Azure ile kullanım için ücretsiz krediler sağladı. Bu krediler Azure kullanımı için ödeme yapmak üzere kullanılabilse de, yayımcı yazılımı lisans ücretleri için ödeme yapmak üzere kullanılamaz.

**Faturalandırma ve koleksiyonlar** 

Yayımcı yazılım lisansı faturalandırması, faturalama için müşterinin seçtiği yöntemi kullanılarak sunulur ve faturalama zaman çizelgesine uyar. Yerinde Kurumsal Anlaşma olmayan müşteriler Market yazılım lisansları için aylık olarak faturalandırılır. Kurumsal Anlaşma olan müşteriler aylık olarak sunulan bir fatura aracılığıyla aylık olarak faturalandırılır.

Abonelik veya kullandıkça öde fiyatlandırma modelleri seçildiğinde, Microsoft, yayımcının aracı olarak davranır ve faturalandırma, ödeme ve koleksiyonun tüm yönleriyle sorumludur.

### <a name="publisher-payout-and-reporting"></a>Yayımcı ödeme ve raporlama

* Aracı olarak Microsoft tarafından toplanan tüm yazılım lisanslama ücretleri, aksi belirtilmediği ve yayımcı ödeme sırasında kesilmedikleri sürece %20 işlem ücretine tabidir.

* Müşteriler genellikle Kurumsal Anlaşma veya kredi kartı etkin bir Kullandıkça Öde sözleşmesini kullanarak satın alabilir. Anlaşma türü faturalandırma, faturalandırma, toplama ve ödeme zamanlamasını belirler.

>[!NOTE] 
>Transact yayımlama seçeneğine yönelik tüm raporlama ve içgörüler, Iş Ortağı Merkezi 'nin Bulut İş Ortağı Portalı veya Analiz bölümünün Öngörüler bölümü aracılığıyla bulunabilir.

#### <a name="billing-questions-and-support"></a>Faturalandırma soruları ve destek

Daha fazla bilgi ve yasal ilkeler için bkz. [Yayımcı Sözleşmesi](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) (bulut iş ortağı portalı kullanılabilir).

Faturalandırma soruları hakkında yardım almak için lütfen [ticari Market yayımcı desteği](https://aka.ms/marketplacepublishersupport)'ne başvurun.

## <a name="transact-requirements"></a>Transact gereksinimleri

Farklı teklif türleri için Transact gereksinimleri bu bölümde ele alınmıştır.

### <a name="requirements-for-all-offer-types"></a>Tüm teklif türleri için gereksinimler

- Teklifin fiyatlandırma modelinden bağımsız olarak, Transact yayımlama seçeneği için Microsoft hesabı ve finansal bilgiler gereklidir.
- Zorunlu mali bilgiler, ödeme hesabı ve vergi profilini içerir.

Bu hesapları ayarlama hakkında daha fazla bilgi için bkz. [Partner Center hesabınızı yönetme](https://docs.microsoft.com/azure/marketplace/partner-center-portal/manage-account#financial-details).


### <a name="requirements-for-specific-offer-types"></a>Belirli teklif türleri için gereksinimler

Transact yayımlama seçeneği yalnızca aşağıdaki Market teklif türleriyle kullanılabilir: 

**Sanal Makine** 

Ücretsiz, kendi lisansını getir veya kullandıkça öde fiyatlandırma modelleri arasından seçim yapın ve teklif düzeyinde tanımlanan SKU 'Lar olarak sunun. Microsoft, müşterinin Azure faturasında, yayımcı yazılımı lisans ücretlerini temel alınan Azure altyapı ücretlerinden ayrı olarak sunar. Azure altyapı ücretleri, yayımcı yazılımı kullanılarak dağıtılır.

**Azure uygulamaları: çözüm şablonu veya yönetilen uygulama** 

Bir veya daha fazla sanal makine sağlanmalıdır ve sanal makine fiyatlandırmasının toplamına göz çekmelidir. Tek bir planda yönetilen uygulamalar için, sanal makine fiyatlandırması yerine fiyatlandırma modeli olarak bir sabit hızlı aylık abonelik seçilebilir. Bazı durumlarda, Azure altyapı kullanım ücretleri müşteriye yazılım lisans ücretlerinden ayrı olarak geçirilir, ancak aynı faturalandırma beyanının üzerinden yapılır. Ancak, ISV altyapı ücretleri için yönetilen bir uygulama teklifi yapılandırırsanız Azure kaynakları yayımcıya faturalandırılır ve müşteri altyapı, yazılım lisansı ve yönetim hizmetleri maliyetini içeren bir sabit ücret alır.

## <a name="next-steps"></a>Sonraki adımlar

* Teklifinizin seçimini ve yapılandırmasını tamamlamak için teklif türüne göre yayımlama seçeneklerinde uygunluk gereksinimlerini gözden geçirin.
* Çözümünüzün bir teklif türü ve yapılandırmaya nasıl eşlendiğini gösteren örnekler için storefront göre yayımlama düzenlerini gözden geçirin.
