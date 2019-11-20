---
title: Market ölçüm hizmeti kullanılarak ölçülen faturalandırma | Azure Marketi
description: Bu belge, esnek faturalama modelleriyle SaaS tekliflerini yayımlayan ISV 'Ler için bir kılavuzdur.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 54b59cfecbbba6b71cf301aa4872892825d4b675
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827923"
---
# <a name="metered-billing-using-the-marketplace-metering-service"></a>Market ölçüm hizmeti kullanılarak ölçülen faturalandırma

Market ölçüm hizmeti ile standart olmayan birimlere göre ücretlendirilen ticari Market programında hizmet olarak yazılım (SaaS) teklifleri oluşturabilirsiniz.  Bu teklifi yayımlamadan önce, bant genişliği, Bilet veya işlenen e-postalar gibi fatura boyutlarını tanımlarsınız.  Müşteriler daha sonra bu boyutların tüketimine göre ödeme yaparak, sistem Microsoft 'un, faturalandırılabilir olayların Market ölçüm hizmeti API 'SI aracılığıyla meydana gelir.  

## <a name="prerequisites-for-metered-billing"></a>Ölçümlenen faturalandırma önkoşulları

SaaS teklifinin ölçülen faturalandırmayı kullanmasını sağlamak için şunları yapmanız gerekir:

* [SaaS teklifi oluşturma](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer)bölümünde özetlenen [Microsoft teklifi aracılığıyla bir satışı](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#sell-through-microsoft) için tüm teklif gereksinimlerini karşılayın.
* Müşterilerinizin teklifinizi sağlaması ve uygulamanıza bağlanması için SaaS ile ilgili [API](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-fulfillment-api-v2) 'lerle tümleştirin.  
* Hizmetinize yönelik müşterileri ücretlendirmesi için **sabit fiyat** fiyatlandırma modeli için yapılandırılmış olmalıdır.  Boyutlar, düz hız fiyatlandırma modelinin isteğe bağlı bir uzantısıdır. 
* Microsoft 'un faturalandırılabilir olaylarını bilgilendirmek için [Market ölçüm hizmeti API 'leriyle](./marketplace-metering-service-apis.md) tümleştirin.

>[!Note]
>Market ölçüm hizmeti yalnızca sabit fiyat faturalandırma modeli için kullanılabilir ve Kullanıcı başına faturalandırma modeli için geçerlidir.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Ölçümlü ödeme, fiyatlandırma ile nasıl uyar

Teklifin fiyatlandırma modelleriyle birlikte tanımlanması gerektiğinde, teklif hiyerarşisini anlamak önemlidir.

* Her SaaS teklifi, Microsoft aracılığıyla satış yapmak üzere yapılandırılmıştır.  Bu ayar, bir teklif yayımlandıktan sonra değiştirilemez.
* Microsoft ile satış için yapılandırılmış her SaaS teklifinin bir veya daha fazla planı olabilir. Kullanıcı SaaS teklifine abone olur, ancak Microsoft aracılığıyla bir plan bağlamı dahilinde satın alınır.
* Her planda ilişkili bir fiyatlandırma modeli vardır: **sabit oran** veya **Kullanıcı başına**. Bir teklifinizin tüm planların aynı fiyatlandırma modeliyle ilişkilendirilmesi gerekir. Örneğin, planlarından birinin bir ücret fiyatlandırma modeli olduğu ve diğeri Kullanıcı fiyatlandırma modeli başına olduğu bir teklif olamaz.
* Bir sabit fiyat faturalandırma modeli için yapılandırılmış her bir planda en az bir yinelenen ücret ($0 olabilir) dahildir:
    * Yinelenen **aylık** ücret: Kullanıcı planı satın aldığında aylık bir tekrarda önceden ödenen Sabit aylık ücret.
    * Yinelenen **yıllık** ücret: Kullanıcı planı satın aldığında yıllık bir tekrarda önceden ödenen sabit yıllık ücret.
* Yinelenen ücretlere ek olarak, plan, müşterileri sabit fiyata dahil değil kullanım için ücretlendirerek kullanılan isteğe bağlı boyutları da içerebilir.   Her boyut, hizmetinizin [Market ölçüm HIZMETI API](./marketplace-metering-service-apis.md)'Sini kullanarak Microsoft ile iletişim kuracağı faturalandırılabilir bir birimi temsil eder.

## <a name="sample-offer"></a>Örnek teklif

Contoso, contoso Bildirim Hizmetleri (CNS) adlı SaaS hizmetine sahip bir yayımdır. CNS, müşterilerin e-posta veya metin aracılığıyla bildirim göndermesini sağlar. Contoso, ticari Market programının tekliflerini Azure müşterilerine yayınlayabilmesi için Iş Ortağı Merkezi ' nde bir yayımcı olarak kaydedilir.  Aşağıda özetlenen, CNS ile ilişkili iki plan vardır:

* Temel plan
    * $0/ay için 10000 e-posta ve 1000 metni gönderin
    * 10000 e-postaların ötesinde, her 100 e-postası için $1 olarak
    * 1000 metinlerin ötesinde, her metin için $0,02 ödeyin
* Premium planı
    * $350/ay için 50000 e-posta ve 10000 metni gönderin
    * 50000 e-postaların ötesinde, her 100 e-postası için $0,5 olarak
    * 10000 metinlerin ötesinde, her metin için $0,01 ödeyin

CNS Service 'e abone olan bir Azure müşterisi, seçilen plana bağlı olarak aylık metin ve e-posta miktarının dahil edilmesini gönderebilecektir.  Contoso, kullanımı Microsoft 'a herhangi bir kullanım olayı göndermeden dahil edilen miktara göre ölçer.  Müşteriler dahil edilen miktardan daha fazla tüketiklerinde, planları değiştirmek veya farklı bir şey yapmak zorunda değildir.  Contoso, dahil edilen miktarın ötesinde fazla yaşı ölçecek ve [Market ölçüm HIZMETI API](./marketplace-metering-service-apis.md)'sini kullanarak ek kullanım için kullanım olaylarını Microsoft 'a yaybaşlatacak.  Microsoft 'un sırasıyla, yayımcı tarafından belirtilen ek kullanım için müşteriyi ücretlendirir.

## <a name="billing-dimensions"></a>Fatura boyutları

Fatura boyutları, yazılımı kullanmak için nasıl faturalandırılabilecekleri müşteriyle iletişim kurmak ve ayrıca kullanım olaylarını Microsoft 'a iletmek için kullanılır. Bunlar aşağıdaki gibi tanımlanır:

* **Boyut tanımlayıcısı**: kullanım olaylarını yayırken başvurulan sabit tanımlayıcı.
* **Boyut adı**: boyutla ilişkili görünen ad (örneğin, "gönderilen metin iletileri").
* **Ölçü birimi**: Faturalandırma biriminin açıklaması; Örneğin, "metin başına ileti" veya "100 başına e-posta".
* **Birim başına fiyat**: boyutun bir birimi için fiyat.  
* **Aylık dönem Için dahil edilen miktar**: yinelenen aylık ücreti ödeyen müşteriler için ayda dahil edilen boyut miktarı, bir tamsayı olmalıdır.
* **Yıllık dönem Için dahil edilen miktar**: yinelenen yıllık ücret ödeyerek müşteriler için ayda dahil edilen boyut miktarı, bir tamsayı olmalıdır.

Fatura boyutları, bir teklif için tüm planlar arasında paylaşılır.  Bazı öznitelikler, tüm planlar genelinde boyut için geçerlidir ve diğer öznitelikler plana özgüdür.

Boyutun kendisini tanımlayan öznitelikler, bir teklif için tüm planlar arasında paylaşılır.  Teklifi yayımlamadan önce, herhangi bir planın bağlamından bu özniteliklerde yapılan bir değişiklik, tüm planlar genelinde boyut tanımını etkiler.  Teklifi yayımladıktan sonra bu öznitelikler artık düzenlenemeyecektir.  Bu öznitelikler şunlardır:

* Tanımlayıcı
* Ad
* Ölçü birimi

Bir boyutun diğer öznitelikleri her plana özeldir ve plandan plana göre farklı değerlere sahip olabilir.  Planı yayımlamadan önce bu değerleri düzenleyebilir ve yalnızca bu plan etkilenecektir.  Planı yayımladıktan sonra bu öznitelikler artık düzenlenemeyecektir.  Bu öznitelikler şunlardır:

* Birim başına fiyat
* Aylık müşteriler için dahil edilen miktar 
* Yıllık müşteriler için dahil edilen miktar 

Boyutların Ayrıca "etkin" ve "sonsuz" olmak üzere iki özel kavramı vardır:

* **Etkin** , bu planın bu boyutta katıldığını gösterir.  Bu boyuta göre kullanım olayları göndermediği yeni bir plan oluşturuyorsanız, bu onay işaretini kaldırmak isteyebilirsiniz.  Ayrıca, bir plan ilk yayımlandıktan sonra eklenen tüm yeni boyutlar, zaten yayımlanmış planda "etkin değil" olarak görünür.  Devre dışı bırakılan bir boyut, müşteriler tarafından görülen bir plan için boyutların herhangi bir listesinde gösterilmez.
* Sonsuzluk "∞" simgesiyle temsil edilen **sonsuz**, bu planın bu boyuta katıldığını, ancak kullanımı bu boyuta göre ölçmediğini belirtir.  Müşterilerinize bu boyut tarafından temsil edilen işlevlerin plana dahil edildiğini, ancak kullanım sınırlaması olmadığını belirtmek istiyorsanız.  Sonsuz kullanım içeren bir boyut, müşteriler tarafından görülen bir plana ait boyut listelerinde görünür ve bu plan için hiçbir ücret ödemeyeceğine dair bir belirtime göstergesi olur.

>[!Note] 
>Aşağıdaki senaryolar açıkça desteklenir: <br> -Yeni bir plana yeni bir boyut ekleyebilirsiniz.  Yeni boyut, zaten yayımlanmış olan herhangi bir plan için etkinleştirilmeyecektir. <br> -Bir boyut olmadan bir **sabit ücret** planı yayımlayabilir, sonra yeni bir plan ekleyebilir ve bu plan için yeni bir boyut yapılandırabilirsiniz. Yeni boyut, zaten yayımlanmış planlar için etkinleştirilmeyecektir.

## <a name="constraints"></a>Kısıtlamalar

### <a name="trial-behavior"></a>Deneme davranışı

Market ölçüm hizmeti kullanılarak ölçülen faturalandırma, ücretsiz deneme teklifiyle uyumlu değildir.  Bir planı hem ölçülen faturalandırma hem de ücretsiz deneme kullanacak şekilde yapılandırmak mümkün değildir.

### <a name="locking-behavior"></a>Kilitleme davranışı

Market ölçüm hizmeti ile kullanılan bir boyut bir müşterinin hizmet için nasıl ödeme yapılacağı hakkında bilgi sahibi olduğu için, bir boyutun tüm ayrıntıları, bunu yayımladığınızda artık düzenlenebilir olmaz.  Yayımlamadan önce, boyutlarınızın bir plan için tam olarak tanımlanmış olması önemlidir.
  
Bir teklif bir boyutla yayımlandıktan sonra, bu boyut için teklif düzeyi ayrıntıları artık değiştirilemez:

* Tanımlayıcı
* Ad
* Ölçü birimi

Bir plan yayımlandığında, plan düzeyi ayrıntıları artık değiştirilemez:

* Birim başına fiyat
* Aylık dönem için dahil edilen miktar
* Yıllık dönem için dahil edilen miktar
* Plan için boyutun etkinleştirilip etkinleştirilmeyeceğini belirtir

### <a name="upper-limits"></a>Üst sınırlar

Tek bir teklif için yapılandırılabilecek maksimum boyut sayısı 18 benzersiz boyutlardır.

## <a name="get-support"></a>Destek alın

Aşağıdakilerden birine sahipseniz bir destek bileti açabilirsiniz.

* Market ölçüm hizmeti API 'SI ile ilgili teknik sorunlar.
* Arka taraftaki bir hata veya hata nedeniyle ilerletilen bir sorun (örn. yanlış kullanım olayı).
* Ölçümlenen faturalandırmayla ilgili diğer sorunlar. 

Destek biletinizi göndermek için aşağıdaki adımları izleyin:

1. [Destek sayfasına](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff)gidin. İlk birkaç açılan menü, sizin için otomatik olarak doldurulur. Market desteği için ürün ailesini **bulut ve çevrimiçi hizmetler**olarak, **Market yayımcısı**olarak da belirleyebilirsiniz.  Önceden doldurulmuş açılan menü seçimlerini değiştirmeyin.
2. "Ürün sürümünü seçin" altında, **canlı teklif yönetimi**' ni seçin.
3. "Sorunu en iyi tanımlayan bir kategori seçin" altında **SaaS uygulamaları**' nı seçin.
4. "Sorunu en iyi açıklayan bir sorun seçin" bölümünde **ölçülen faturalandırma**' i seçin.
5. **İleri** düğmesini seçerek sorun **ayrıntıları** sayfasına yönlendirilirsiniz. burada sorun hakkında daha fazla ayrıntı girebilirsiniz.

Daha fazla yayımcı desteği seçeneği için [Iş Ortağı Merkezi 'nde ticari Market programına yönelik desteğe](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support) bakın.

## <a name="next-steps"></a>Sonraki adımlar

- Daha fazla bilgi için bkz. [Market ölçüm hizmeti API 'leri](./marketplace-metering-service-apis.md) .
