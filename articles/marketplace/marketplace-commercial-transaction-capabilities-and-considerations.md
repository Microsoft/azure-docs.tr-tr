---
title: Microsoft ticari Market Transact özellikleri
description: Bu makalede ticari Market Transact seçeneğine yönelik fiyatlandırma, faturalandırma, faturalandırma ve ödeme konuları açıklanmaktadır.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: 0a25e1b50455cad5bdbe5b76b2a291f2a1c11940
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107015"
---
# <a name="commercial-marketplace-transact-capabilities"></a>Ticari Market Transact özellikleri

Bu makalede, Microsoft ticari Market 'e yönelik fiyatlandırma, faturalandırma, faturalandırma ve ödeme konuları açıklanmaktadır.

## <a name="transactions-by-listing-option"></a>Listeleme seçeneğine göre işlemler

Yayımcı veya Microsoft, ticari Market 'teki teklifler için yazılım lisansı işlemlerini yönetmekten sorumludur. Teklifiniz için seçtiğiniz listeleme seçeneği, işlemi kimin yönettiğini belirler. Her yayımlama seçeneğinin kullanılabilirliği ve açıklamaları için bkz. [listeye giriş seçenekleri](determine-your-listing-type.md)

### <a name="contact-me-free-trial-and-byol-options"></a>Benimle iletişim kurun, ücretsiz deneme ve KLG seçenekleri

Yayımcılar _benimle Iletişim kurun_ ve _ücretsiz deneme sürümü_, promosyon ve Kullanıcı alma amaçları için seçenekler arasından seçim yapabilir. Bazı teklif türlerinde, yayımcılar, müşterilerin doğrudan sizin oluşturduğunuz bir lisansı kullanarak teklifinizi bir abonelik satın almasını sağlamak için _kendi lisansını getir_ (KLG) seçeneğini seçebilirler. Bu seçeneklerle, Microsoft doğrudan yayımcının yazılım lisansı işlemlerine katılmaz ve ilişkili işlem ücreti yoktur, bu nedenle yayımcılar tüm gelirleri tutar.

Bu seçeneklerle, yayımcılar yazılım lisans işleminin tüm yönlerini desteklemekten sorumludur. Bu, sipariş, karşılama, ölçüm, faturalandırma, faturalama, ödeme ve koleksiyon ile sınırlı değildir ancak bunlarla sınırlı değildir. Ilgili kişi listeleme seçeneğiyle, yayımcılar, müşteriden toplanan yayımcı yazılım lisansı ücretleri %100 ' ı tutar.

### <a name="transact-publishing-option"></a>Transact yayımlama seçeneği

Microsoft aracılığıyla satışı yapmak, Microsoft ticaret olanaklarından yararlanır ve bulma ve değerlendirme ile satın alma ve uygulamaya kadar uçtan uca bir deneyim sunar. _Transactable_ teklifi, Microsoft 'un yayımcı adına yazılım lisansı için para değişimini kolaylaştıran bir tekliftir. Transact teklifleri mevcut bir Microsoft aboneliğine veya kredi kartına göre faturalandırılır ve Microsoft 'un yayımcı adına bulut marketi işlemlerini barındırmalarına olanak tanır.

Iş Ortağı Merkezi 'nde yeni bir teklif oluşturduğunuzda Transact seçeneğini belirleyin. Bu seçenek yalnızca, teklif türü için Transact kullanılabiliyorsa görüntülenir.

## <a name="transact-overview"></a>Transact genel bakış

Microsoft, Transact seçeneğini kullanırken, üçüncü taraf yazılımlarının ve bazı teklif türlerinin müşterinin Azure aboneliğine dağıtılması için satışı sağlar. Yayımcı, bir teklifin fiyatlandırma modelini seçerken altyapı ücretlerinin ve kendi yazılım lisanslama ücretlerine ilişkin faturalandırmayı göz önünde bulundurmalıdır.

Şu anda aşağıdaki teklif türleri için Transact yayımlama seçeneği desteklenmektedir:

| Teklif türü | Faturalandırma temposunda | Ölçülen faturalandırma | Fiyatlandırma modeli |
| ------------ | ------------- | ------------- | ------------- |
| Azure Uygulaması<br>(Yönetilen uygulama) | Aylık | Yes | Kullanım tabanlı |
| Azure Sanal Makinesi | Aylık | No | Kullanım tabanlı, KLG |
| Hizmet Olarak Yazılım (SaaS) | Aylık ve yıllık | Yes | Düz hız, Kullanıcı başına, kullanım tabanlı. |
|||||

`*` Azure sanal makinesi, kullanım tabanlı faturalandırma planlarını destekler. Bu planlar, her biri çekirdek başına, çekirdek boyutu başına veya Pazar ve çekirdek boyut kullanımı başına, aboneliğin saatlik kullanım için aylık olarak faturalandırılır.

### <a name="metered-billing"></a>Ölçülen faturalandırma

_Market ölçüm hizmeti_ , sözleşmeye (yetkilendirme) dahil edilen aylık veya yıllık ücretlere ek olarak Kullandıkça Öde (tüketim tabanlı) ücretleri belirtmenize olanak tanır. Bant genişliği, Bilet veya işlenen e-postalar gibi belirttiğiniz Market ölçüm hizmeti boyutları için kullanım maliyetlerini ücretlendirmenize sağlayabilirsiniz. SaaS teklifleri için ölçülen faturalandırma hakkında daha fazla bilgi için bkz. [ticari Market ölçüm hizmeti kullanılarak SaaS Için ölçülen faturalandırma](./partner-center-portal/saas-metered-billing.md). Azure Uygulama teklifleri için ölçülen faturalandırma hakkında daha fazla bilgi için bkz. [yönetilen uygulama ölçümlü faturalandırması](./partner-center-portal/azure-app-metered-billing.md).

### <a name="billing-infrastructure-costs"></a>Faturalandırma altyapısı maliyetleri

**Sanal makineler** ve **Azure uygulamaları** için Azure altyapı kullanım ücretleri müşterinin Azure aboneliğine faturalandırılır. Altyapı kullanım ücretleri, müşterinin faturasında yazılım sağlayıcısının lisanslama ücretlerinden ayrı olarak fiyatlandırılır ve gösterilir.

**SaaS uygulamaları** için yayımcı, Azure altyapı kullanım ücretlerini ve yazılım lisanslama ücretlerini tek bir maliyet öğesi olarak hesaba malıdır. Müşteriye yönelik bir sabit ücret olarak gösterilir. Azure altyapı kullanımı yönetilir ve doğrudan yayımcıya faturalandırılır. Gerçek altyapı kullanım ücretleri müşteri tarafından görülmez. Yayımcılar genellikle Azure altyapı kullanım ücretlerini yazılım lisans fiyatlandırmasına paketlendirmeyi tercih edebilir. Yazılım Lisanslama ücretleri tarifeli değildir veya Kullanıcı tüketimine göre hesaplanır.

## <a name="pricing-models"></a>Fiyatlandırma modelleri

Kullanılan işlem seçeneğine bağlı olarak, abonelik ücretleri aşağıdaki gibidir:

- **Şimdi alın (ücretsiz)**: yazılım lisansları için ücret alınmaz. Ücretsiz teklifler, ücretli bir teklifine dönüştürülemez. Müşterilerin ücretli bir teklif siparişi olması gerekir.
- **Kendi lisansınızı getirin** (KLG): bir teklif ticari Market 'te listeleniyorsa, yazılım lisansları için geçerli olan tüm ücretler doğrudan yayımcı ile müşteri arasında yönetilir. Microsoft yalnızca müşterinin Azure abonelik hesabına uygulanabilir Azure altyapı kullanımı ücretlerini ücretlendirir.
- **Abonelik fiyatlandırması**: yazılım lisansı ücretleri aylık veya yıllık olarak sunulur, yinelenen abonelik ücreti de sabit bir fiyat veya bilgisayar başına faturalandırılır. Yinelenen abonelik ücretleri, orta dönemli müşteri iptalleri veya kullanılmayan hizmetler için eşit olarak dağıtılır. Müşteri aboneliğini abonelik döneminin ortasında yükseltir veya daha eski sürüme düşürütirse, yinelenen abonelik ücretleri eşit olarak dağıtılır.
- **Kullanım tabanlı fiyatlandırma**: Azure sanal makine teklifleri için, müşteriler teklifin kullanımının kapsamına göre ücretlendirilir. Sanal makine görüntüleri için, müşterilere VM görüntülerinden dağıtılan sanal makinelerin kullanımı için yayımcı tarafından belirlenen saatlik bir Azure Marketi ücreti ödersiniz. Saatlik ücret, sanal makine boyutları arasında Tekdüzen veya değişken olabilir. Kısmi saatler dakikaya göre ücretlendirilir. Planlar aylık olarak faturalandırılır.
- **Ölçülen fiyatlandırma**: Azure Uygulama teklifleri ve SaaS teklifleri için yayımcılar, yapılandırdıkları özel ölçüm boyutlarına göre tüketim için [Market ölçüm hizmetini](./partner-center-portal/marketplace-metering-service-apis.md) kullanabilir. Bu değişiklikler, sözleşmeye (yetkilendirme) dahil edilen aylık veya yıllık ücretlere ek niteliğindedir. Özel Ölçüm boyutları örnekleri, işlem genişliği, Bilet veya işlenen e-postalardır. Yayımcılar her plan için bir veya daha fazla ölçülen boyut tanımlayabilir ancak teklif başına en fazla 30 olabilir. Yayımcılar, her ölçüm teklifte tanımlanan her bir ölçümle, bireysel müşteri kullanımını izlemekten sorumludur. Olayların bir saat içinde Microsoft 'a bildirilmesi gerekir. Microsoft, müşterileri, uygun fatura dönemi için yayımcılar tarafından bildirilen kullanım bilgilerine göre ücretlendirir.
- **Ücretsiz deneme**: teklif türüne bağlı olarak 30 günden fazla altı aya kadar olan yazılım lisansları için ücret ödemezsiniz. Yayımcılar aynı teklif dahilinde birden çok planda ücretsiz bir deneme sağladıysanız, müşteriler başka bir plandaki ücretsiz bir denemeye geçebilir, ancak deneme süresi yeniden başlatmaz. Sanal makine teklifleri için müşteriler, deneme süresi boyunca teklifin kullanımı için Azure altyapı maliyetlerinden ücretlendirilir. Deneme süresi sona erdiğinde, müşteriler deneme süresi sona ermeden önce iptal etmedikleri takdirde standart oranlar temelinde denedikleri son plana göre otomatik olarak ücretlendirilir.

> [!NOTE]
> Bir çözüm kullanıldıktan sonra tüketimine göre faturalandırılan teklifler, para iadesi için uygun değildir.

Bir teklifle ilişkili kullanım ücretlerini değiştirmek isteyen yayımcılar, öncelikle teklifi (veya teklif içindeki belirli bir planı) ticari marketten kaldırmalıdır. Kaldırma, [Microsoft Publisher sözleşmesinin](https://go.microsoft.com/fwlink/?LinkID=699560)gereksinimlerine uygun olarak yapılmalıdır. Ardından yayımcı yeni kullanım ücretlerini içeren yeni bir teklif (veya bir teklif içinde plan) yayımlayabilir. Teklif veya planı kaldırma hakkında bilgi için bkz. [bir teklif veya plan satma](./partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan).

### <a name="free-contact-me-and-bring-your-own-license-byol-pricing"></a>Ücretsiz, benimle Iletişim kurun ve kendi lisansını getir (KLG) fiyatlandırması

Şimdi al (ücretsiz), benimle Iletişim kurun veya KLG seçeneğiyle bir teklif yayımlarken, Microsoft, yazılım lisans ücretlerine yönelik satış işlemini kolaylaştırmaya yönelik bir rol oynamaz. Yayımcı, yazılım lisansı ücretlerine %100 oranında devam eder.

### <a name="usage-based-and-subscription-pricing"></a>Kullanım tabanlı ve abonelik fiyatlandırması

Bir teklifi kullanım tabanlı veya abonelik işlemi olarak yayımlarken, Microsoft yazılım lisansı satın alma, iade ve geri ödeme işlemlerini işleyecek teknoloji ve hizmetler sağlar. Bu senaryoda, yayımcı Microsoft 'un bu amaçlar için bir aracı görevi görmesini yetkilendirir. Yayımcı, Microsoft 'un yazılım lisanslama işlemini kolaylaştırmasına olanak tanır. Yayımcı, hesabınızı satıcı, sağlayıcı, dağıtıcı ve lisans verme olarak koruyun.

Microsoft, müşterilerin, Microsoft 'un ticari marketi ve Son Kullanıcı lisanslama sözleşmenizin hüküm ve koşullarına tabi olmak üzere yazılımınızı sipariş, lisans ve kullanma imkanı sağlar. Kendi Son Kullanıcı lisanslama sözleşmenizi sağlamanız veya teklifi oluştururken [Standart sözleşmeyi](./standard-contract.md) seçmeniz gerekir.

### <a name="free-software-trials"></a>Ücretsiz yazılım denemeleri

Transact yayımlama senaryolarında, aboneliğe bağlı olarak, yazılım lisansını 30 ila 120 gün boyunca ücretsiz kullanılabilir hale getirebilirsiniz. Müşteriler, geçerli Azure altyapı kullanımı için değiştirilecek.

### <a name="examples-of-pricing-and-store-fees"></a>Fiyatlandırma ve mağaza ücretleri örnekleri

**Kullanım tabanlı**

Kullanım tabanlı fiyatlandırma aşağıdaki maliyet yapısına sahiptir:

| **Lisans maliyetiniz** | **$1,00/saat** |
|---------|---------|
| Azure kullanım maliyeti (D1/1-çekirdek) | $0,14/saat |
| _Müşteri Microsoft tarafından faturalandırılır_ | _$1,14/saat_ |
||

Bu senaryoda, Microsoft, yayımlanmış VM Görüntünüzün kullanımı için saat başına $1,14.

| **Microsoft faturaları** | **$1,14/saat**  |
|---------|---------|
| Microsoft, lisans maliyetinizi %80 oranında ödetir | $0,80/saat |
| Microsoft, lisans maliyetlerinizin %20 ' sini tutar  |  $0,20/saat |
| Microsoft, Azure kullanım maliyetinin %100 ' ünü tutar | $0,14/saat |
||

**Kendi lisansınızı getirin (KLG)**

KLG aşağıdaki maliyet yapısına sahiptir:

| **Lisans maliyetiniz** | **Lisans ücreti size anlaştı ve sizin tarafınızdan faturalandırılır** |
|---------|---------|
|Azure kullanım maliyeti (D1/1-çekirdek)    |   $0,14/saat     |
| _Müşteri Microsoft tarafından faturalandırılır_ | _$0,14/saat_ |
||

Bu senaryoda, Microsoft, yayımlanmış VM Görüntünüzün kullanımı için saat başına $0,14.

| **Microsoft faturaları** | **$0,14/saat** |
|---------|---------|
| Microsoft, Azure kullanım maliyetini korur | $0,14/saat |
| Microsoft, lisans maliyetinizi %0 oranında tutar | $0,00/saat |
||

**SaaS uygulama aboneliği**

SaaS abonelikleri, aylık veya yıllık esasına göre sabit bir hızda veya Kullanıcı başına fiyatlandırılır. SaaS teklifi için  **Microsoft 'Tan satışı** etkinleştir seçeneğini etkinleştirirseniz, aşağıdaki maliyet yapısına sahipsiniz:

| **Lisans maliyetiniz** | **$100,00/ay** |
|--------------|---------|
| Azure kullanım maliyeti (D1/1-çekirdek) | Müşteriye değil doğrudan yayımcıya faturalandırılır |
| _Müşteri Microsoft tarafından faturalandırılır_ | _aylık $100,00 (yayımcı, lisans ücretindeki herhangi bir tahakkuk eden veya geçiş altyapı maliyeti için hesap olmalıdır)_ |
||

Bu senaryoda, Microsoft, yazılım lisansınızın $100,00 ' i faturalandırır ve teklifin azaltılmış bir mağaza hizmeti ücreti için uygun olup olmadığına bağlı olarak $80,00 veya $90,00.

| **Microsoft faturaları** | **$100,00/ay** |
|---------|---------|
| Microsoft, lisans maliyetinizi %80 oranında ödetir <br> \* Microsoft, tüm nitelikli SaaS uygulamaları için lisans maliyetinizi %90 oranında ödetir | $80,00/ay <br> \* $90,00/ay |
| Microsoft, lisans maliyetlerinizin %20 ' sini tutar <br> \* Microsoft, tüm nitelikli SaaS uygulamaları için lisans maliyetinizi %10 oranında tutar. | $20,00/ay <br> \* $10,00 |

### <a name="commercial-marketplace-service-fees"></a>Ticari Market hizmet ücretleri

Müşteriler, ticari Market 'ten alınan Transact teklifinizi satın alırken %20 Standart mağaza hizmeti ücreti ücretlendirir. Bu masrafın ayrıntıları için, [Microsoft Publisher sözleşmesinin](https://go.microsoft.com/fwlink/?LinkID=699560)bölüm 5c bölümüne bakın.

Ticari Market 'e yayımladığınız belirli Transact teklifleri için, %10 oranında sınırlı bir mağaza hizmeti ücreti için uygun olabilir. Uygun bir teklif için, Microsoft tarafından _Azure IP ortak satışı incentivized_ olarak atanmış olmalıdır. Uygunluk, daha düşük Market servis ücretini almak için her bir takvim ayının sonundan önce en az beş iş gününe ulaşılmalıdır. Uygunluk karşılandıktan sonra, sınırlı hizmet ücreti, _Azure IP ortak satışı incentivized_ durumu kaybolana kadar aşağıdaki ayın ilk gününde geçerli olan tüm işlemler için geçerlidir. IP ortak satışı uygunluğu hakkında daha fazla bilgi için bkz. [ortak satış durumu gereksinimleri](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status).

Daha düşük Market hizmet ücreti, Azure IP ortak satışı incentivized SaaS, VM 'Ler, yönetilen uygulamalar ve ticari Market aracılığıyla sunulan diğer tüm nitelikli transactable IaaS çözümleri için geçerlidir. Tek bir Microsoft ekipleri uygulamasıyla ilişkili ücretli SaaS teklifleri veya en az iki Microsoft 365 eklentisi (Excel, PowerPoint, Word, Outlook ve SharePoint) ve Microsoft AppSource yayımlandığında bu indirimde uygun olabilir.

### <a name="customer-invoicing-payment-billing-and-collections"></a>Müşteri Faturalama, ödeme, faturalandırma ve koleksiyonlar

**Faturalama ve ödeme**: abonelik veya [PAYGO](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) yazılım lisansı ücretleri sunmak için müşterinin tercih edilen faturalandırma yöntemini kullanabilirsiniz.

**Kurumsal Anlaşma**: müşterinin tercih edilen faturalandırma yöntemi Microsoft kurumsal anlaşma ise, yazılım lisans ücretleriniz, Azure 'a özgü kullanım maliyetlerinden ayrı olarak bu faturalandırma yöntemi kullanılarak, bir maliyet dökümü olarak faturalandırılır.

**Kredi kartları ve aylık fatura**: müşteriler, kredi kartı ve aylık fatura kullanarak ödeme yapabilir. Bu durumda, yazılım lisans ücretleriniz, bir Azure 'a özgü kullanım maliyetlerinden ayrı olarak, Kurumsal Anlaşma senaryosu gibi, yeni bir maliyet olarak faturalandırılacaktır.

**Ücretsiz krediler ve parasal taahhüt**: bazı müşteriler azure 'u kurumsal anlaşma bir parasal taahhütte ön ödeme yapmayı tercih ediyor veya Azure kullanımı için kullanabileceğiniz ücretsiz krediler sağladı. Bu krediler Azure kullanımı için ödeme yapmak üzere kullanılabilse de, yayımcı yazılımı lisans ücretleri için ödeme yapmak üzere kullanılamaz.

**Faturalandırma ve koleksiyonlar**: Yayımcı yazılım lisansı faturalandırması, müşterinin seçtiği faturalandırma yöntemiyle faturalandırılır ve faturalama zaman çizelgesine uyar. Yerinde Kurumsal Anlaşma olmayan müşteriler Market yazılım lisansları için aylık olarak faturalandırılır. Kurumsal Anlaşma olan müşteriler aylık olarak sunulan bir fatura aracılığıyla aylık olarak faturalandırılır.

Abonelik ya da Kullandıkça Öde (kullanım tabanlı) fiyatlandırma modelleri seçildiğinde, Microsoft, yayımcının aracı olarak davranır ve faturalandırma, ödeme ve koleksiyonun tüm yönleriyle sorumludur.

### <a name="publisher-payout-and-reporting"></a>Yayımcı ödeme ve raporlama

Aracı olarak Microsoft tarafından toplanan tüm yazılım lisanslama ücretleri, aksi belirtilmediği ve yayımcı ödeme sırasında kesilmedikleri sürece %20 işlem ücretine tabidir.

Müşteriler genellikle Kurumsal Anlaşma veya kredi kartı etkin bir Kullandıkça Öde sözleşmesini kullanarak satın alabilir. Anlaşma türü faturalandırma, faturalandırma, toplama ve ödeme zamanlamasını belirler.

>[!NOTE]
>Transact yayımlama seçeneğine yönelik tüm raporlama ve Öngörüler, Iş Ortağı Merkezi 'nin analiz bölümü aracılığıyla kullanılabilir.

#### <a name="billing-questions-and-support"></a>Faturalandırma soruları ve destek

Daha fazla bilgi ve yasal ilkeler için bkz. [Microsoft Publisher sözleşmesi](https://go.microsoft.com/fwlink/?LinkID=699560). Faturalandırma soruları hakkında yardım almak için [ticari Market yayımcı desteği](https://aka.ms/marketplacepublishersupport)'ne başvurun.

## <a name="transact-requirements"></a>Transact gereksinimleri

Bu bölümde, farklı teklif türleri için Transact gereksinimleri ele alınmaktadır.

### <a name="requirements-for-all-offer-types"></a>Tüm teklif türleri için gereksinimler

- Teklifin fiyatlandırma modelinden bağımsız olarak, Transact yayımlama seçeneği için Microsoft hesabı ve finansal bilgiler gereklidir.
- Zorunlu mali bilgiler, ödeme hesabı ve vergi profilini içerir.

Bu hesapları ayarlama hakkında daha fazla bilgi için bkz. [Iş Ortağı Merkezi 'nde ticari Market hesabınızı yönetme](manage-account.md).

### <a name="requirements-for-specific-offer-types"></a>Belirli teklif türleri için gereksinimler

Microsoft 'un Transact özelliği, yalnızca aşağıdaki ticari Market teklif türleri için kullanılabilir. Bu liste, ticari Market 'te bu teklif türlerini transactable yapma gereksinimlerini sağlar.

- **Azure uygulaması (çözüm şablonu ve yönetilen uygulama planları**: bazı durumlarda Azure altyapı kullanımı ücretleri müşteriye yazılım lisans ücretlerinden ayrı olarak geçirilir, ancak aynı faturalandırma ekstresinde. Ancak, ISV altyapı ücretleri için bir yönetilen uygulama planı yapılandırırsanız Azure kaynakları yayımcıya faturalandırılır ve müşteri altyapı, yazılım lisansı ve yönetim hizmetleri maliyetini içeren bir sabit ücret alır.

- **Azure sanal makinesi**: ÜCRETSIZ, KLG veya kullanım tabanlı fiyatlandırma modelleri arasından seçim yapın. Microsoft, müşterinin Azure faturasında, yayımcı yazılımı lisans ücretlerini temel alınan Azure altyapı ücretlerinden ayrı olarak sunar. Azure altyapı ücretleri, yayımcının yazılımı kullanılarak dağıtılır.

- **SaaS uygulaması**: çok kiracılı bir çözüm olmalıdır, kimlik doğrulaması için [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) kullanın ve [SaaS karşılama API 'leriyle](partner-center-portal/pc-saas-fulfillment-api-v2.md)tümleştirin. Azure altyapı kullanımı, doğrudan sizin kullanımınıza (yayımcı) göre yönetilir ve faturalandırılır. bu nedenle, Azure altyapı kullanım ücretleri ve yazılım lisanslama ücretlerini tek bir maliyet öğesi olarak hesaba almalısınız. Ayrıntılı yönergeler için bkz. [ticari Market Için SaaS teklifi planlaması](plan-saas-offer.md#plans).

## <a name="private-plans"></a>Özel planlar

Teklif için özel bir plan oluşturabilir, anlaşmalı, anlaşmaya özgü fiyatlandırmayla veya özel yapılandırmalara göre tamamlayabilirsiniz.

Özel planlar, genel kullanıma sunulmadan belirli müşterilere daha yüksek veya daha düşük fiyatlar sağlamanıza olanak tanır. Özel planlar, bir teklifle bir Premium için indirim veya bir Premium eklemek için kullanılabilir. Özel planlar, Azure aboneliklerini plan düzeyinde listeleyerek bir veya daha fazla müşterinin kullanımına sunulabilir.

## <a name="next-steps"></a>Sonraki adımlar

- Çözümünüzün bir teklif türü ve yapılandırmaya nasıl eşlendiğini gösteren örnekler için çevrimiçi mağazaya göre yayımlama düzenlerini gözden geçirin.
- [Teklif türüne göre kılavuz yayımlanıyor](publisher-guide-by-offer-type.md).
