---
title: Market ölçüm hizmeti kullanılarak yönetilen uygulamalar için ölçülen faturalandırma | Azure Marketi
description: Bu belge, esnek faturalama modelleriyle Azure uygulamalarını yayımlayan ISV 'Ler için bir kılavuzdur.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/22/2020
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: d015cec30e516541b50c2acfac38fad898965e1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96436360"
---
# <a name="managed-application-metered-billing"></a>Yönetilen uygulama ölçümlü faturalandırma 

Market ölçüm hizmeti ile standart olmayan birimlere göre ücretlendirilen Azure Uygulama teklifleri için yönetilen uygulama planları oluşturabilirsiniz. Bu teklifi yayımlamadan önce, bant genişliği, Bilet veya işlenen e-postalar gibi fatura boyutlarını tanımlarsınız. Müşteriler daha sonra bu boyutların tüketimine göre ödeme yapar.  Sisteminiz, Microsoft 'un, faturalandırılabilen etkinliklerin Market ölçüm hizmeti API 'SI aracılığıyla meydana gelir.

## <a name="prerequisites-for-metered-billing"></a>Ölçümlenen faturalandırma önkoşulları

Yönetilen bir uygulama planının tarifeli faturalandırma kullanmasını sağlamak için şunları yapmanız gerekir:

* [Azure Uygulama teklifi oluşturma](../create-new-azure-apps-offer.md)bölümünde özetlenen tüm teklif gereksinimlerini karşılayın.
* Hizmetiniz için aylık maliyet maliyetini müşterilere doldurma **fiyatlandırmasını** yapılandırın. Sabit bir ücreti ücretlendirmeniz ve bunun yerine tamamen ölçülen faturalandırmaya dayanmasını istemiyorsanız fiyat sıfır olabilir.
* Müşterinin sabit fiyat üzerinden ödeyolacağı ölçüm olayları için **Fatura boyutlarını** ayarlayın.
* Microsoft 'un faturalandırılabilir olaylarını bilgilendirmek için [Market ölçüm hizmeti API 'leriyle](./marketplace-metering-service-apis.md) tümleştirin.

## <a name="how-metered-billing-fits-in-with-pricing"></a>Ölçümlü ödeme, fiyatlandırma ile nasıl uyar

Teklifin fiyatlandırma modelleriyle birlikte tanımlanması gerektiğinde, teklif hiyerarşisini anlamak önemlidir.

* Her Azure uygulaması teklifinin çözüm şablonu veya yönetilen uygulama planları olabilir.
* Ölçülen faturalandırma yalnızca yönetilen uygulama planlarıyla uygulanır.
* Yönetilen her uygulama planının ilişkili bir fiyatlandırma modeli vardır. 
* Fiyatlandırma modelinde aylık bir yinelenen ücret bulunur ve bu $0 olarak ayarlanabilir.
* Yineleme ücretine ek olarak, plan, müşterileri sabit fiyata dahil değil kullanım için ücretlendirerek kullanılan isteğe bağlı boyutları da içerebilir. Her boyut, hizmetinizin [Market ölçüm HIZMETI API](marketplace-metering-service-apis.md)'Sini kullanarak Microsoft ile iletişim kuracağı faturalandırılabilir bir birimi temsil eder.

## <a name="sample-offer"></a>Örnek teklif

Örnek olarak contoso, contoso Analytics (CoA) adlı bir yönetilen uygulama hizmeti içeren bir yayımdır. CoA, müşterilerin raporlama ve veri depolama için büyük miktarda veriyi çözümlemesine izin verir. Contoso, ticari Market programının tekliflerini Azure müşterilerine yayınlayabilmesi için Iş Ortağı Merkezi ' nde bir yayımcı olarak kaydedilir. CoA ile ilişkili, aşağıda özetlenen iki plan vardır:

* Temel plan
    * 100 GB 'yi çözümleyin ve $0/ay için 100 rapor oluşturun
    * 100 GB 'ın ötesinde, her 1 GB için $10 ' i ödeyin
    * 100 raporlarının ötesinde her rapor için $1 ödersiniz
* Premium planı
    * 1000 GB 'yi çözümleyin ve $350/ay için 1000 raporlar oluşturun
    * 1000 GB 'ın ötesinde, her 1 TB için $100 ' i ödeyin
    * 1000 raporlarının ötesinde her rapor için $0,5 ödersiniz

CoA hizmetine abone olan bir Azure müşterisi, seçilen plana göre her ay rapor çözümleyebilir ve raporlar oluşturabilir. Contoso, kullanımı Microsoft 'a herhangi bir kullanım olayı göndermeden dahil edilen miktara göre ölçer. Müşteriler dahil edilen miktardan daha fazla tüketiklerinde, planları değiştirmek veya farklı bir şey yapmak zorunda değildir. Contoso, dahil edilen miktarın ötesinde fazla yaşı ölçecek ve [Market ölçüm HIZMETI API](./marketplace-metering-service-apis.md)'sini kullanarak ek kullanım için kullanım olaylarını Microsoft 'a yaybaşlatacak. Microsoft 'un sırasıyla, yayımcı tarafından belirtilen ek kullanım için müşteriyi ücretlendirir.

## <a name="billing-dimensions"></a>Fatura boyutları

Fatura boyutları, yazılımı kullanmak için nasıl faturalandırılabilecekleri müşteriyle iletişim kurmak için kullanılır.  Bu boyutlar, kullanım olaylarını Microsoft 'a iletmek için de kullanılır. Bunlar aşağıdaki gibi tanımlanır:

* **Boyut tanımlayıcısı**: kullanım olaylarını yayırken başvurulan sabit tanımlayıcı.
* **Boyut adı**: boyutla ilişkili görünen ad (örneğin, "gönderilen metin iletileri").
* **Ölçü birimi**: Faturalandırma biriminin açıklaması, örneğin "metin başına ileti" veya "100 başına e-posta".
* **Birim başına fiyat**: boyutun bir birimi için fiyat.
* **Aylık dönem Için dahil edilen miktar**: yinelenen aylık ücreti ödeyen müşteriler için ayda dahil edilen boyut miktarı, bir tamsayı olmalıdır.

Fatura boyutları, bir teklif için tüm planlar arasında paylaşılır. Bazı öznitelikler, tüm planlar genelinde boyut için geçerlidir ve diğer öznitelikler plana özgüdür.

Boyutun kendisini tanımlayan öznitelikler, bir teklif için tüm planlar arasında paylaşılır. Teklifi yayımlamadan önce, herhangi bir planın bağlamından bu özniteliklerde yapılan bir değişiklik, tüm planlar genelinde boyut tanımını etkiler. Teklifi yayımladıktan sonra bu öznitelikler artık düzenlenemeyecektir. Öznitelikler şunlardır:

* Tanımlayıcı
* Name
* Ölçü birimi

Bir boyutun diğer öznitelikleri her plana özeldir ve plandan plana göre farklı değerlere sahip olabilir.  Planı yayımlamadan önce, bu değerleri düzenleyebilirsiniz ve yalnızca bu plan etkilenecektir. Planı yayımladıktan sonra bu öznitelikler artık düzenlenemeyecektir. Öznitelikler şunlardır:

* Birim fiyat
* Aylık müşteriler için dahil edilen miktar 
* Yıllık müşteriler için dahil edilen miktar 

Boyutların Ayrıca "etkin" ve "sonsuz" olmak üzere iki özel kavramı vardır:

* **Etkin** , bu planın bu boyutta katıldığını gösterir.  Bu boyuta göre kullanım olayları göndermediği yeni bir plan oluşturuyorsanız, bu seçeneği işaretsiz bırakmak isteyebilirsiniz. Ayrıca, bir plan ilk yayımlandıktan sonra eklenen tüm yeni boyutlar, zaten yayımlanmış planda "etkin değil" olarak görünür.  Devre dışı bırakılan bir boyut, müşteriler tarafından görülen bir plan için boyutların herhangi bir listesinde gösterilmez.
* Sonsuzluk "∞" simgesiyle temsil edilen **sonsuz**, bu planın bu boyuta göre tarifeli kullanım olmadan bu boyutta katıldığını gösterir. Müşterilerinize bu boyut tarafından temsil edilen işlevlerin plana dahil edildiğini, ancak kullanım sınırlaması olmadığını belirtmek istiyorsanız.  Sonsuz kullanım içeren bir boyut, müşteriler tarafından görülen bir plana ait boyut listelerinde görünür.  Bu plan hiçbir şekilde ücretlendirilmeyecektir.

>[!Note] 
>Aşağıdaki senaryolar açıkça desteklenir:  <br> -Yeni bir plana yeni bir boyut ekleyebilirsiniz.  Yeni boyut, zaten yayımlanmış olan herhangi bir plan için etkinleştirilmeyecektir. <br> -Sabit bir aylık ücretle ve herhangi bir boyut olmadan bir plan yayımlayabilir, sonra yeni bir plan ekleyebilir ve bu plan için yeni bir boyut yapılandırabilirsiniz. Yeni boyut, zaten yayımlanmış planlar için etkinleştirilmeyecektir.

## <a name="constraints"></a>Kısıtlamalar

### <a name="locking-behavior"></a>Kilitleme davranışı

Market ölçüm hizmeti ile kullanılan bir boyut, müşterinin hizmet için nasıl ödeme yapılacağını anlayacaktır.  Bir teklifin tüm ayrıntıları artık bir teklif yayımlandıktan sonra düzenlenebilir değildir.  Teklifinizi yayımlamadan önce, boyutlarınızın tam olarak tanımlanmış olması önemlidir.

Bir teklif bir boyutla yayımlandıktan sonra, bu boyut için teklif düzeyi ayrıntıları artık değiştirilemez:

* Tanımlayıcı
* Name
* Ölçü birimi

Bir plan yayımlandığında, plan düzeyi ayrıntıları artık değiştirilemez:

* Birim fiyat
* Aylık dönem için dahil edilen miktar
* Plan için boyutun etkinleştirilip etkinleştirilmeyeceğini belirtir

>[!Note]
>Market ölçüm hizmeti kullanılarak ölçülen faturalandırma henüz Azure Kamu Bulutu 'nda desteklenmiyor.

### <a name="upper-limits"></a>Üst sınırlar

Tek bir teklif için yapılandırılabilecek maksimum boyut sayısı 30 benzersiz boyutlardır.

## <a name="get-support"></a>Destek alın

Aşağıdaki sorunlardan birine sahipseniz bir destek bileti açabilirsiniz.

* Market ölçüm hizmeti API 'SI ile ilgili teknik sorunlar.
* Arka taraftaki bir hata veya hata nedeniyle ilerletilen bir sorun (örn. yanlış kullanım olayı).
* Ölçümlenen faturalandırmayla ilgili diğer sorunlar.

Yayımcı desteği seçeneklerini anlamak ve Microsoft ile destek bileti açmak için [Iş Ortağı Merkezi 'ndeki ticari Market programına yönelik destek](../support.md) bölümündeki yönergeleri izleyin.

## <a name="next-steps"></a>Sonraki adımlar

- Daha fazla bilgi için bkz. [Market ölçüm hizmeti API 'leri](./marketplace-metering-service-apis.md) .
