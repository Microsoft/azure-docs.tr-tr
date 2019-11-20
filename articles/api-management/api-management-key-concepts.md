---
title: Azure API Management - Genel bakış ve temel kavramlar | Microsoft Docs
description: API'ler, ürünler, roller, gruplar ve diğer API Management temel kavramları hakkında bilgi edinin.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 11/15/2017
ms.author: apimpm
ms.custom: mvc
ms.openlocfilehash: 8d7fa8b8119ddf1769b36bcb55831047d6242470
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073428"
---
# <a name="about-api-management"></a>API Management hakkında

API Management (APıM), mevcut arka uç hizmetleri için tutarlı ve modern API ağ geçitleri oluşturmanın bir yoludur.

API Management kuruluşların, veri ve hizmetlerinin potansiyelini açmak için API 'Leri dış, iş ortağı ve iç geliştiricilere yayımlamalarına yardımcı olur. Her yerdeki işletmeler, bir dijital platform olarak işlemlerini genişletmek, yeni kanallar oluşturmak, yeni müşteriler bulmak ve mevcut müşterilerle daha derin etkileşimi yürütmeyi amaçlar. API Management; geliştirici katılımı, iş öngörüleri, analizler, güvenlik ve koruma aracılığıyla başarılı bir API programı yürütmeye ilişkin temel uzmanlıklar sağlar. Azure API Management’i bir arka uç almak ve bunu temel alan tam özellikli bir API programını başlatmak için kullanabilirsiniz.

Bu makale, APIM içeren yaygın senaryolara genel bakış sağlar.  Ayrıca, APIM sisteminin ana bileşenlerine de kısa bir genel bakış sağlar. Böylece makale, her bir bileşene daha ayrıntılı bir genel bakış sunmuş olur.

## <a name="overview"></a>Genel Bakış

Yöneticiler API Management’i kullanmak için API'ler oluşturur. Her API bir veya daha fazla işlemden oluşur ve her API bir veya daha fazla ürüne eklenebilir. Bir API kullanmak için, geliştiriciler bu API’yi içeren ürüne abone olur ve böylece geçerli olabilecek kullanım ilkelerine tabi olarak API işlemini çağırabilir. Yaygın senaryolar şunlardır:

* **Mobil altyapıyı koruma**: API anahtarlarına erişim geçişi sağlayarak, azaltma ile DOS saldırılarını önleyerek ya da JWT belirtecini doğrulama gibi gelişmiş güvenlik ilkelerini kullanarak mobil altyapıyı koruyun.
* **ISV iş ortağı ekosistemlerini etkinleştirme**: Geliştirici Portalı üzerinden hızlı iş ortağı ekleyerek ve iş ortağı kullanımı için hazır olmayan dahili uygulamalardan bir API cephesi oluşturarak ISV iş ortağı eko sistemlerini etkinleştirin.
* **Dahili API programı çalıştırma** API’lerin kullanılabilirliği ve son değişikliklerine ilişkin iletişim için kuruluşa merkezi bir konum sağlayarak ve kurumsal hesaplar temelinde erişim geçişi sağlayarak, tümü API ağ geçidi ve arka uç arasında güvenli bir kanalı temel alan dahili API programı çalıştırın.

Sistem aşağıdaki bileşenlerden oluşur:

* **API ağ geçidi** şunları yapan uç noktadır:
  
  * API çağrılarını kabul eder ve bunları arka uçlarınıza yönlendirir.
  * API anahtarları, JWT belirteçleri, sertifikaları ve diğer kimlik bilgilerini doğrular.
  * Kullanım kotalarını ve oran limitlerini uygular.
  * Kod değişiklikleri olmadan API'nizi anında dönüştürür.
  * Ayarlandığında arka uç yanıtlarını önbelleğe kaydeder.
  * Analiz amaçlı çağrı meta verilerini günlüğe kaydeder.
* **Azure portal**, API programınızı ayarladığınız yönetim arabirimidir. Bunu şunlar için kullanın:
  
  * API şeması tanımlama ya da içeri aktarma.
  * API'leri ürünler halinde paketleme.
  * API’lerde kota veya dönüşüm gibi ilkeler ayarlama.
  * Analizlerden öngörüler edinme
  * Kullanıcıları yönetme.
* **Geliştirici portalı**, geliştiricilerin şunları yapabileceği ana web varlığı görevi görür:
  
  * API belgelerini okuma.
  * Etkileşimli konsol üzerinden bir API’yi deneme.
  * Bir hesap oluşturma ve API anahtarlarını almak için abone olma.
  * Kendi kullanımlarına ilişkin analize erişme.

Daha fazla bilgi için bkz [. bulut tabanlı API Management: API](https://j.mp/ms-apim-whitepaper) 'lerin gücünden yararlanma PDF teknik incelemesi. API Management hakkında CITO Research tarafından hazırlanan tanıtım amaçlı bu teknik inceleme şunları içerir: 
 
 * Yaygın API gereksinimleri ve sorunları
 * API'leri ayırma ve cepheleri sunma
 * Geliştiricilerin hızla hazır olmalarını sağlama
 * Erişimi güvenli hale getirme
 * Analizler ve ölçümler
 * API Management platformuyla denetim ve öngörü elde etme
 * Bulut ile şirket içi çözüm kullanımının karşılaştırması
 * Azure API Yönetimi
 
## <a name="apis"> </a>API’ler ve işlemler
API'ler bir API Management hizmet örneğinin temelini oluşturur. Her API geliştiricilere sunulan bir işlemler kümesini temsil eder. Her API, API’yi uygulayan arka uç hizmetine başvuru içerir ve bunun işlemleri arka uç hizmeti tarafından uygulanan işlemlere eşlenir. API Management işlemleri; URL eşleme, sorgu ve yol parametreleri, istek ve yanıt içeriği ve işlem yanıtını önbelleğe alma üzerinde sahip olunan denetim sayesinde yüksek oranda yapılandırılabilir niteliktedir. Hızı sınırı, kotalar ve IP kısıtlama ilkeleri de API veya tek işlem düzeyinde uygulanabilir.

Daha fazla bilgi için bkz. [API oluşturma][How to create APIs] ve [API’ye işlem ekleme][How to add operations to an API].

## <a name="products"> </a> Ürünler
Ürünler API'lerin geliştiricilerin kullanımına nasıl sunulduğudur. API Management ürünleri bir ya da daha fazla API’ye sahiptir. Başlık, açıklama ve kullanım koşulları ile yapılandırılırlar. Ürünler **Açık** veya **Korumalı** olabilir. Korumalı ürünleri kullanabilmek için bunlara abone olmak gerekir, açık ürünler abonelik olmadan kullanılabilir. Bir ürün, geliştiriciler tarafından kullanılmaya hazır olduğunda yayımlanabilir. Yayımlandıktan sonra, geliştiriciler tarafından görüntülenebilir (ve abone olunan korumalı ürünler söz konusu olduğunda). Abonelik onayı ürün düzeyinde yapılandırılır ve yönetici onayı gerektirebilir ya da otomatik olarak onaylanır.

Gruplar, ürünlerin geliştiricilere görünürlüğünü yönetmek için kullanılır. Ürünler gruplara görünürlük sağlar ve geliştiriciler ait oldukları gruplar tarafından görünür olan ürünleri görüntüleyip bunlara abone olabilir. 

## <a name="groups"> </a> Gruplar
Gruplar, ürünlerin geliştiricilere görünürlüğünü yönetmek için kullanılır. API Management şu sabit sistem gruplarına sahiptir:

* **Yöneticiler**: Azure aboneliği yöneticileri bu grubun üyesidir. Yöneticiler, geliştiriciler tarafından kullanılan API’leri, işlemleri ve ürünleri oluşturarak API Management hizmet örneklerini yönetir.
* **Geliştiriciler**: Kimliği doğrulanmış geliştirici portalı kullanıcıları bu gruba girer. Geliştiriciler, API'lerinizi kullanarak uygulama oluşturan müşterilerdir. Geliştiriciler, geliştirici portalına erişim iznine sahiptir ve bir API’nin işlemlerini çağıran uygulamalar oluşturur.
* **Konuklar**: Kimliği doğrulanmamış geliştirici portalı kullanıcıları (bir API Management örneğinin geliştirici portalını ziyaret eden olası müşteriler gibi) bu gruba girer. Bunlara API’leri görüntüleyebilme ancak çağıramama gibi bazı salt okunur erişimler verilebilir.

Bu sistem gruplarına ek olarak, yöneticiler özel gruplar oluşturabilir veya [ilişkili Azure Active Directory kiracılarındaki dış grupları kullanabilir](api-management-howto-aad.md). Özel ve dış gruplar, geliştiricilere görünürlük ve API ürünlerine erişim sağlayan sistem gruplarıyla birlikte kullanılabilir. Örneğin, belirli bir iş ortağı kuruluşla ilişkili geliştiriciler için özel bir grup oluşturabilir ve bunlara yalnızca ilgili API'leri içeren bir üründen API'lere erişim izni verebilirsiniz. Bir kullanıcı birden fazla grubun üyesi olabilir.

Daha fazla bilgi için bkz. [Grupları oluşturma ve kullanma][How to create and use groups].

## <a name="developers"> </a> Geliştiriciler
Geliştiriciler API Management hizmet örneğindeki kullanıcı hesaplarını temsil eder. Geliştiriciler yöneticiler tarafından oluşturulabilir ve davet edilebilir ya da [Geliştirici portalı][Developer portal] üzerinden kaydolabilir. Her geliştirici bir veya daha fazla grubun üyesidir ve bu gruplara görünürlük sağlayan ürünlere abone olabilir.

Bir ürüne abone olan geliştiricilere ürün için birincil ve ikincil anahtar verilir. Bu anahtar ürün API’lerine çağrılar yapılırken kullanılır.

Daha fazla bilgi için bkz. [Geliştirici oluşturma ve davet etme][How to create or invite developers] ve [Grupları geliştiricilerle ilişkilendirme][How to associate groups with developers].

## <a name="policies"> </a> İlkeler
İlkeler, Azure portalın API’nin davranışını yapılandırma yoluyla değiştirmesini sağlayan güçlü API Management özellikleridir. İlkeler, bir API isteği veya yanıtı üzerinde sırayla yürütülen deyimlerin bir koleksiyonudur. Sık kullanılan deyimler, XML’den JSON’a biçim dönüştürmeyi ve bir geliştiriciden gelen çağrıların sayısını sınırlamak üzere çağrı hızını sınırlamayı ve çeşitli ilkeleri içerir.

İlke ifadeleri herhangi bir API Management ilkesinde, ilke aksini belirtmedikçe, öznitelik değerleri ya da metin değerleri olarak kullanılabilir. [Akışı denetle](/azure/api-management/api-management-advanced-policies#choose) ve [Değişken ayarla](/azure/api-management/api-management-advanced-policies#set-variable) gibi bazı ilkeler ilke ifadelerini temel alır. Daha fazla bilgi için bkz. [Gelişmiş ilkeler](/azure/api-management/api-management-advanced-policies#AdvancedPolicies) ve [İlke ifadeleri](/azure/api-management/api-management-policy-expressions).


API Management ilkelerinin tam listesi için bkz. [İlke başvurusu][Policy reference]. İlkeleri yapılandırma ve kullanma hakkında daha fazla bilgi için bkz. [API Management ilkeleri][API Management policies]. Hız sınırı ve kota ilkeleri içeren bir ürün oluşturmaya ilişkin öğretici için bkz. [Gelişmiş ürün ayarları oluşturma ve yapılandırma][How create and configure advanced product settings].


## <a name="developer-portal"> </a> Geliştirici portalı
Geliştirici portalı, geliştiricilerin API’leriniz hakkında bilgi alabileceği, işlemleri görüntüleyebileceği ve çağırabileceği ve ürünlere abone olabileceği yerdir. Müşteri adayları, geliştirici portalını ziyaret edebilir, API’lerle işlemleri görüntüleyebilir ve portala kaydolabilir. Geliştirici portalınızın URL’si, API Management hizmet örneğinizin Azure portalındaki panoda yer alır.

Özel içerik ekleyerek, stilleri özelleştirerek ve marka bilgilerinizi ekleyerek, geliştirici portalınızın genel görünümünü özelleştirebilirsiniz.

## <a name="api-management-and-the-api-economy"></a>API Management ve API ekonomisi

API Management hakkında daha fazla bilgi için Microsoft Ignite 2017 konferansına ait şu sunuyu izleyin.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK2186/player]
> 
> 

## <a name="next-steps"></a>Sonraki adımlar

Şu hızlı başlangıcı tamamlayarak Azure API Management'i kullanmaya başlayın:

> [!div class="nextstepaction"]
> [Azure API Management örneği oluşturma](get-started-create-service-instance.md)

[APIs and operations]: #apis
[Products]: #products
[Groups]: #groups
[Developers]: #developers
[Policies]: #policies
[Developer portal]: #developer-portal

[How to create APIs]: api-management-howto-create-apis.md
[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[How create and configure advanced product settings]: transform-api.md
[How to create or invite developers]: api-management-howto-create-or-invite-developers.md
[Policy reference]: api-management-policy-reference.md
[API Management policies]: api-management-howto-policies.md
[Create an API Management service instance]: get-started-create-service-instance.md




