---
title: Microsoft Commercial Market ölçüm hizmeti kullanılarak SaaS teklifleri için ölçülen faturalandırma
description: Ticari Market ölçüm hizmetini kullanarak SaaS teklifleri için esnek faturalandırma modelleri hakkında bilgi edinin.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/08/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: 9f72d54fda8f66c2fce35f0520b51406aa276bb0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92892762"
---
# <a name="metered-billing-for-saas-using-the-commercial-marketplace-metering-service"></a>Ticari Market ölçüm hizmeti kullanılarak SaaS için ölçülen faturalandırma

Ticari Market ölçüm hizmeti sayesinde standart olmayan birimlere göre ücretlendirilen bir hizmet olarak yazılım (SaaS) teklifi oluşturabilirsiniz. Ticari Market 'e SaaS teklifi yayımlamadan önce, bant genişliği, Bilet veya işlenen e-postalar gibi fatura boyutlarını tanımlarsınız.  Müşteriler daha sonra bu boyutların tüketimine göre ödeme yapar ve bu boyut, sistem Microsoft 'un ticari Market ölçüm hizmeti API 'SI ile meydana gelir.  

## <a name="prerequisites-for-metered-billing"></a>Ölçümlenen faturalandırma önkoşulları

Ölçülen faturalandırmayı kullanmaya yönelik SaaS teklifi için öncelikle şunları yapmanız gerekir:

- [Ticari Market 'Te SaaS teklifi oluşturma](../create-new-saas-offer.md)bölümünde özetlenen [Microsoft tekliflerinden bir satış](../plan-saas-offer.md#listing-options) için sunulan tüm teklif gereksinimlerini karşılayın.
- Müşterilerinizin teklifinizi sağlaması ve uygulamanıza bağlanması için SaaS ile ilgili [API](./pc-saas-fulfillment-api-v2.md) 'lerle tümleştirin.  
- Hizmetiniz için müşteriler şarj edildiğinde **sabit fiyat** fiyatlandırma modeli için yapılandırılmış olmalıdır.  Boyutlar, düz hız fiyatlandırma modelinin isteğe bağlı bir uzantısıdır. 

Daha sonra SaaS teklifi, Microsoft 'un faturalandırılabilir olaylarını bilgilendirmek için [ticari Market ölçüm hizmeti API 'leri](./marketplace-metering-service-apis.md) ile tümleştirilebilir.

>[!Note]
>Market ölçüm hizmeti yalnızca sabit fiyat faturalandırma modeli için kullanılabilir ve Kullanıcı başına faturalandırma modeli için geçerlidir.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Ölçümlü ödeme, fiyatlandırma ile nasıl uyar

Teklif hiyerarşisini anlamak, teklifin fiyatlandırma modelleriyle birlikte tanımlanması halinde önemlidir.

- Her SaaS teklifi, Microsoft aracılığıyla satış yapmak üzere yapılandırılmıştır.  Bir teklif yayımlandıktan sonra bu seçenek değiştirilemez.
- Microsoft ile satış için yapılandırılmış her SaaS teklifinin bir veya daha fazla planı olabilir.  Kullanıcı SaaS teklifine abone olur, ancak Microsoft aracılığıyla bir plan bağlamı dahilinde satın alınır.
- Her planda ilişkili bir fiyatlandırma modeli vardır: **sabit oran** veya **Kullanıcı başına**. Bir teklifinizin tüm planların aynı fiyatlandırma modeliyle ilişkilendirilmesi gerekir. Örneğin, bir sabit hızlı fiyatlandırma modeli için planlar ve diğeri de Kullanıcı başına fiyatlandırma modeli olan bir teklif olamaz.
- Bir sabit fiyat faturalandırma modeli için yapılandırılmış her bir planda en az bir yinelenen ücret ($0 olabilir) dahildir:
    - Yinelenen **aylık** ücret: Kullanıcı planı satın aldığında aylık bir tekrarda önceden ödenen Sabit aylık ücret.
    - Yinelenen **yıllık** ücret: Kullanıcı planı satın aldığında yıllık bir tekrarda önceden ödenen sabit yıllık ücret.
- Yinelenen ücretlere ek olarak, bir sabit ücret planı, müşterilerin fazla kullanım için de sabit fiyata dahil edilmediğinden fazla kullanım için kullanılacak isteğe bağlı özel boyutlar içerebilir.  Her boyut, hizmetinizin [ticari Market ölçüm HIZMETI API](./marketplace-metering-service-apis.md)'Sini kullanarak Microsoft ile iletişim kuracağı faturalandırılabilir bir birimi temsil eder.

## <a name="sample-offer"></a>Örnek teklif

Contoso, contoso Bildirim Hizmetleri (CNS) adlı SaaS hizmetine sahip bir yayımdır. CNS, müşterilerinin e-posta veya metin aracılığıyla bildirim göndermesini sağlar. Contoso, ticari Market programının Azure müşterilerine SaaS tekliflerini yayımlaması için Iş Ortağı Merkezi ' nde bir yayımcı olarak kaydedilir.  Aşağıda özetlenen, CNS ile ilişkili iki plan vardır:

- Temel plan
    - $0/ay için 10000 e-posta ve 1000 metni gönderin (Sabit aylık ücret)
    - 10000 e-postaların ötesinde, her 100 e-postası için $1 olarak
    - 1000 metinlerin ötesinde, her metin için $0,02 ödeyin

    [![Temel plan fiyatlandırması](./media/saas-basic-pricing.png "Büyütülmüş görünüm için tıklayın")](./media/saas-basic-pricing.png)

- Premium planı
    - # 350/month veya 5M e-postaları için 50000 e-posta ve 10000 metinlerini, yıl başına $3500 için gönderin
    - 50000 e-postaların ötesinde, her 100 e-postası için $0,5 olarak
    - 10000 metinlerin ötesinde, her metin için $0,01 ödeyin

    [![Premium plan fiyatlandırması](./media/saas-premium-pricing.png "Büyütülmüş görünüm için tıklayın")](./media/saas-premium-pricing.png)

- Kurumsal plan
    - $400/ay için sınırsız sayıda e-posta ve 50000 metni gönderin
    - 50000 metinlerin ötesinde her txt için $0,005 ödeyin

    [![Kurumsal plan fiyatlandırması](./media/saas-enterprise-pricing.png "Büyütülmüş görünüm için tıklayın")](./media/saas-enterprise-pricing.png)

Seçilen plana bağlı olarak, CNS SaaS teklifine yönelik bir Azure müşteri satın alma aboneliği abonelik dönemi başına dahil edilen metin ve e-posta miktarını (abonelik ayrıntılarında bir ay veya yıl) gönderebilecektir.  Contoso, kullanım olaylarını Microsoft 'a göndermeden temel olarak dahil edilen miktara kadar olan kullanımı sayar. Müşteriler dahil edilen miktardan daha fazla tüketiklerinde, planları değiştirmek veya farklı bir şey yapmak zorunda değildir.  Contoso, dahil edilen miktarın ötesinde fazla yaşı ölçecek ve [ticari Market ölçüm HIZMETI API](./marketplace-metering-service-apis.md)'sini kullanarak fazla kullanım süresi kullanımını borçlandırmak üzere kullanım olaylarını Microsoft 'a yaymayı başlatacak.  Bu durumda, Microsoft, özel boyutlarda yayımcı tarafından belirtilen fazla kullanım için müşteriyi ücretlendirir. Fazla kullanım faturalaması bir sonraki fatura dönemi üzerinde yapılır (aylık, ancak bazı müşteriler için üç aylık veya erken olabilir).  Aylık düz bir ücret planı için, fazla kullanım meydana gelme gerçekleştiği her ay için fazla kullanım faturalandırması yapılır.  Yıllık bir sabit fiyat planı için, yılda baz olarak dahil edilen miktar tüketildikten sonra, özel ölçüm tarafından yayılan tüm ek kullanımlar, aboneliğin yıl döneminin sonuna kadar her faturalandırma dönemi (aylık) sırasında fazla kullanım olarak faturalandırılır.

## <a name="billing-dimensions"></a>Fatura boyutları

Her faturalandırma boyutu, ISV 'nin kullanım olaylarını yayarak kullandığı özel bir birimi tanımlar.  Fatura boyutları Ayrıca, müşteriyle yazılım kullanımı için faturalandırılabilecekleri müşteriyle iletişim kurmak için de kullanılır.  Bunlar aşağıdaki gibi tanımlanır:

- **ID**: kullanım olayları yayırken başvurulan sabit boyut tanımlayıcısı.
- **Görünen ad**: boyutla ilişkili görünen ad (örneğin, "gönderilen metin iletileri").
- **Ölçü birimi**: Faturalandırma biriminin açıklaması, örneğin "metin başına ileti" veya "100 başına e-posta".
- **USD cinsinden birim başına fiyat**: boyutun bir birimi için fiyat.  0 olabilir. 
- **Temele dahil edilen aylık miktar**: müşterilerin yinelenen aylık ücreti ödeyerek aylık olarak dahil edilen boyut miktarı, bir tamsayı olmalıdır. 0 veya sınırsız olabilir.
- **Temele dahil edilen yıllık miktar**: müşteriler için her yıl dahil edilen boyut miktarı, yinelenen yıllık ücret ödeyerek bir tamsayı olmalıdır. 0 veya sınırsız olabilir.

Fatura boyutları, bir teklif için tüm planlar arasında paylaşılır.  Bazı öznitelikler, tüm planlar genelinde boyut için geçerlidir ve diğer öznitelikler plana özgüdür.

Boyutun kendisini tanımlayan öznitelikler, bir teklif için tüm planlar arasında paylaşılır.  Teklifi yayımlamadan önce, herhangi bir planın bağlamından bu özniteliklerde yapılan bir değişiklik, tüm planlar genelinde boyut tanımını etkiler.  Teklifi yayımladıktan sonra bu öznitelikler artık düzenlenemeyecektir.  Bu öznitelikler şunlardır:

- ID
- Görünen Ad
- Ölçü Birimi

Bir boyutun diğer öznitelikleri her plana özeldir ve plandan plana göre farklı değerlere sahip olabilir.  Planı yayımlamadan önce, bu değerleri düzenleyebilirsiniz ve yalnızca bu plan etkilenecektir.  Planı yayımladıktan sonra bu öznitelikler artık düzenlenemeyecektir.  Bu öznitelikler şunlardır:

- USD cinsinden birim başına fiyat
- Temele dahil edilen aylık miktar  
- Temel dahil edilen yıllık miktar  

Boyutların Ayrıca "etkin" ve "sonsuz" olmak üzere iki özel kavramı vardır:

- **Etkin** , bu planın bu boyutta katıldığını gösterir.  Bu boyuta göre kullanım olayları göndermeyen yeni bir plan oluşturuyorsanız, bu seçeneği işaretlenmemiş olarak bırakmak isteyebilirsiniz.  Ayrıca, bir plan ilk yayımlandıktan sonra eklenen tüm yeni boyutlar, zaten yayımlanmış planda "etkin değil" olarak görünür.  Devre dışı bırakılan bir boyut, müşteriler tarafından görülen bir plan için boyutların herhangi bir listesinde gösterilmez.
- Sonsuz "∞" simgesiyle temsil edilen **sonsuz** , bu planın bu boyuta katıldığını, ancak bu boyuta göre kullanım göstermediğini belirtir.  Müşterilerinize bu boyut tarafından temsil edilen işlevlerin plana dahil edildiğini, ancak kullanım sınırlaması olmadığını belirtmek istiyorsanız.  Sonsuz kullanım içeren bir boyut, müşteriler tarafından görülen bir plana ait boyut listelerinde görünür ve bu plan için hiçbir ücret ödemeyeceğine dair bir belirtime göstergesi olur.

>[!Note] 
>Aşağıdaki senaryolar açıkça desteklenir: <br> -Yeni bir plana yeni bir boyut ekleyebilirsiniz.  Yeni boyut, zaten yayımlanmış olan herhangi bir plan için etkinleştirilmeyecektir. <br> -Bir boyut olmadan bir **sabit ücret** planı yayımlayabilir, sonra yeni bir plan ekleyebilir ve bu plan için yeni bir boyut yapılandırabilirsiniz. Yeni boyut, zaten yayımlanmış planlar için etkinleştirilmeyecektir.

### <a name="setting-dimension-price-per-unit-per-supported-market"></a>Desteklenen Pazar başına birim başına boyut fiyatını ayarlama

Düz oran fiyatlandırmasına benzer şekilde, faturalandırma boyut fiyatları desteklenen ülke veya bölge başına ayarlanabilir. Iş Ortağı Merkezi 'nde fiyatlandırma verilerini içeri ve dışarı aktarma özelliğini aşağıdaki gibi kullanmanız gerekir.

1. İstenen boyutları tanımlayın ve hangi pazarların desteklendiğini işaretleyin. 
1. Bu verileri bir dosyaya aktarın.
1. Ülke/bölge başına doğru fiyatları ekleyin ve dosyayı Iş Ortağı Merkezi 'nde içeri aktarın.

Ölçerin Kullanıcı arabirimi, boyutun fiyatlarına yalnızca dosyada görünebileceğini yansıtmak üzere değişecektir.

[![ticari Market ölçüm hizmeti boyutları](media/metering-service-dimensions.png "Büyütülmüş görünüm için tıklayın")](media/metering-service-dimensions.png)

### <a name="private-plan"></a>Özel plan

Düz oran planları gibi, boyutları olan bir plan, yalnızca planın tanımlı kitlelerine göre erişilebilen özel plan olarak ayarlanabilir.

## <a name="constraints"></a>Kısıtlamalar

### <a name="trial-behavior"></a>Deneme davranışı

Ticari Market ölçüm hizmeti kullanılarak ölçülen faturalandırma, ücretsiz deneme teklifiyle uyumlu değildir.  Bir planı hem ölçülen faturalandırma hem de ücretsiz deneme kullanacak şekilde yapılandırmak mümkün değildir.

### <a name="locking-behavior"></a>Kilitleme davranışı

Ticari Market ölçüm hizmeti ile kullanılan bir boyut bir müşterinin hizmet için nasıl ödendiğini anlayacağından, bir boyutun tüm ayrıntıları, yayımlamadan sonra artık düzenlenemeyecektir.  Yayımlamadan önce, boyutlarınızın bir plan için tam olarak tanımlanmış olması önemlidir.

Bir teklif bir boyutla yayımlandıktan sonra, bu boyut için teklif düzeyi ayrıntıları artık değiştirilemez:

- ID
- Görünen Ad
- Ölçü Birimi

Bir plan yayımlandığında, plan düzeyi ayrıntıları artık değiştirilemez:

- USD cinsinden birim başına fiyat
- Temele dahil edilen aylık miktar
- Temele dahil edilen yıllık miktar
- Plan için boyutun etkin olup olmadığı

### <a name="upper-limits"></a>Üst sınırlar

Tek bir teklif için yapılandırılabilecek maksimum boyut sayısı 30 benzersiz boyutlardır.

## <a name="get-support"></a>Destek alın

Aşağıdaki sorunlardan birine sahipseniz bir destek bileti açabilirsiniz.

- Market ölçüm hizmeti API 'SI ile ilgili teknik sorunlar.
- Arka taraftaki bir hata veya hata nedeniyle ilerletilen bir sorun (örn. yanlış kullanım olayı).
- Ölçümlenen faturalandırmayla ilgili diğer sorunlar.

Yayımcı desteği seçeneklerini anlamak ve Microsoft ile bir destek bileti açmak için [Iş Ortağı Merkezi 'nde ticari Market programına yönelik destek](../support.md)bölümündeki yönergeleri izleyin.

## <a name="next-steps"></a>Sonraki adımlar

- [Market ölçüm hizmeti API’leri](./marketplace-metering-service-apis.md)
