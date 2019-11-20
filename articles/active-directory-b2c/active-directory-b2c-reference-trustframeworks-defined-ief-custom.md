---
title: Azure Active Directory B2C 'de başvuru güven çerçeveleri | Microsoft Docs
description: Azure Active Directory B2C özel ilkelerle ve kimlik deneyimi çerçevesiyle ilgili bir konu.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e96ddcb904bbda6c3123ffc9d3da50ff80823689
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500024"
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Azure AD B2C Identity Experience Framework ile güven çerçeveleri tanımlama

Azure Active Directory B2C (Azure AD B2C) kimlik deneyimi çerçevesini kullanan özel ilkeler, kuruluşunuza merkezi bir hizmet sağlar. Bu hizmet, büyük bir ilgi alanındaki kimlik Federasyonu karmaşıklığını azaltır. Karmaşıklık, tek bir güven ilişkisine ve tek bir meta veri değişimine düşürülür.

Azure AD B2C özel ilkeler, aşağıdaki soruları cevaplamanıza olanak tanımak için kimlik deneyimi çerçevesini kullanır:

- Hangi yasal, güvenlik, gizlilik ve veri koruma ilkelerine uymak gerekir?
- Kişiler kim ve acalacaklandırılan katılımcı haline geliyor?
- Acalacaklandırılan kimlik bilgileri sağlayıcıları kim ("talep sağlayıcıları" olarak da bilinir) ve neleri sunuyoruz?
- Acalacaklandırılan bağlı olan taraflar kim (ve isteğe bağlı olarak ne gerekir)?
- Katılımcılar için "tel" birlikte çalışabilirlik gereksinimleri nelerdir?
- Dijital kimlik bilgilerini değiş tokuş için uygulanması gereken işletimsel "çalışma zamanı" kuralları nelerdir?

Tüm bu soruları yanıtlamak için, kimlik deneyimi çerçevesini kullanan özel ilkeler Azure AD B2C güven çerçevesi (TF) yapısını kullanın. Bu yapıyı ve neler sağladığını ele alalım.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>Güven çerçevesini ve Federasyon yönetim temelini anlayın

Güven çerçevesi, bir ilgilendiğiniz topluluktaki katılımcıların uyması gereken kimlik, güvenlik, gizlilik ve veri koruma ilkelerinin yazılı bir belirtimidir.

Federal Kimlik, Internet ölçeğinde Son Kullanıcı kimlik güvencesi sağlamak için bir temel sağlar. Üçüncü taraflara kimlik yönetimi temsilcisi seçerek, son kullanıcının tek bir dijital kimliği birden çok bağlı olan tarafla yeniden kullanılabilir.  

Kimlik güvencesi, kimlik sağlayıcılarının (IDPs) ve öznitelik sağlayıcılarının (AtPs) belirli güvenlik, gizlilik ve işlem ilkelerine ve uygulamalarına bağlı olmasını gerektirir.  Doğrudan İncelemeleri gerçekleştireistemlerse, bağlı olan taraflar (RPs), ile çalışmayı seçtikleri IDPs ve AtPs 'Ler ile güven ilişkileri geliştirmeniz gerekir.  

Dijital kimlik bilgilerinin tüketicileri ve sağlayıcılarının sayısı arttıkça, bu güven ilişkilerinin ikili yönetimine veya hatta ağ bağlantısı için gereken teknik meta verilerin ikili değişimine devam etmek zordur.  Federasyon hub 'ları, bu sorunları çözmek için yalnızca sınırlı başarıları elde edin.

### <a name="what-a-trust-framework-specification-defines"></a>Güven çerçevesi belirtimi ne tanımlar
TFs, her bir ilgilendiğiniz topluluğun belirli bir TF belirtimine göre yönetilebileceği açık kimlik değişimi (Ox) güven çerçevesi modelinin linchkıgiğlerdir. Bu tür bir TF belirtimi şunları tanımlar:

- **Şu tanım ile ilgilenilen topluluk için güvenlik ve gizlilik ölçümleri:**
    - Katılımcılar tarafından sunulan/gerekli güvence düzeyleri (LOA); Örneğin, dijital kimlik bilgisinin orijinalliği için sıralı bir güven derecelendirmeleri kümesi.
    - Katılımcılar tarafından sunulan/gerekli olan koruma düzeyleri (LOP); Örneğin, ilgilenilen topluluktaki katılımcılar tarafından işlenen dijital kimlik bilgilerinin korunması için sıralı bir güven derecelendirmeleri kümesi.

- **Katılımcılar tarafından sunulan/gerekli dijital kimlik bilgilerinin açıklaması**.

- **Dijital kimlik bilgilerinin üretimi ve tüketimi için teknik ilkeler ve LOA ve LOP 'nin ölçülmesi. Bu yazılı ilkeler genellikle aşağıdaki ilke kategorilerini içerir:**
    - Kimlik doğrulama ilkeleri, örneğin: *Kişinin kimlik bilgileri ne kadar kuvvetlidir?*
    - Güvenlik ilkeleri, örneğin: *Bilgi tutarlılığı ve gizliliği ne kadar kuvvetlidir?*
    - Gizlilik ilkeleri, örneğin: *Kullanıcının kişisel olarak tanımlanabilen bilgileri (PII) hangi denetimde*?
    - Daha fazla kullanılabilirlik ilkesi, örneğin: *Bir sağlayıcı işlemleri sona erer, PII işlevinin sürekliliği ve korunması nasıl yapılır?*

- **Üretim ve dijital kimlik bilgilerinin tüketimi için teknik profiller. Bu profiller şunları içerir:**
    - Belirtilen LOA 'da dijital kimlik bilgisinin kullanılabildiği kapsam arabirimleri.
    - Hat üzeri birlikte çalışabilirlik için teknik gereksinimler.

- **Topluluktaki katılımcıların gerçekleştirebileceği çeşitli rollerin açıklamaları ve bu rolleri yerine getirmek için gereken nitelikler.**

Bu nedenle, bir TF belirtimi, kimlik bilgilerinin ilgilendiğiniz Topluluk katılımcıları arasında nasıl değiştirildiğini yönetir: bağlı olan taraflar, kimlik ve öznitelik sağlayıcıları ve öznitelik Doğrulayıcıları.

Bir TF belirtimi, topluluk içerisindeki dijital kimlik bilgilerinin onayını ve tüketimini düzenleyen bir ilgilendiğiniz topluluk yönetimi için başvuru olarak görev yapan bir veya birden çok belgelerdir. Bu, ilgilenilen bir topluluk üyeleri arasındaki çevrimiçi işlemler için kullanılan dijital kimliklerdeki güven oluşturmak için tasarlanan, belgelenmiş bir ilke ve yordamlar kümesidir.  

Diğer bir deyişle, bir TF belirtimi, bir topluluk için uygun bir federal kimlik ekosistemi oluşturmaya yönelik kuralları tanımlar.

Şu anda böyle bir yaklaşımın avantajı hakkında yaygın olarak anlaşma vardır. Altyapı özelliklerinin güveneceği, doğrulanabilen güvenlik, güvence ve gizlilik özellikleriyle dijital kimlik ekosistemlerinin geliştirilmesini kolaylaştırmaya yardımcı olur ve bu da birçok ilgilendiğiniz topluluk genelinde yeniden kullanılabilir.

Bu nedenle, kimlik deneyimi çerçevesini kullanan Azure AD B2C özel ilkeler, birlikte çalışabilirliği kolaylaştırmak için bir TF 'in veri gösteriminin temeli olarak belirtimi kullanır.  

Kimlik deneyimi çerçevesi 'nden yararlanan Azure AD B2C özel ilkeler, insan ve makine tarafından okunabilen verilerin bir karışımı olarak bir TF belirtimini temsil eder. Bu modelin bazı bölümleri (genellikle idare altına daha fazla yönelimli bölümler), yayımlanan güvenlik ve Gizlilik ilkesi belgelerinin (varsa) ilgili yordamlarla birlikte başvuru olarak temsil edilir. Diğer bölümlerde, işletimsel Otomasyonu kolaylaştıran yapılandırma meta verileri ve çalışma zamanı kuralları ayrıntılı olarak açıklanır.

## <a name="understand-trust-framework-policies"></a>Güven çerçevesi ilkelerini anlama

Uygulama açısından, TF belirtimi, kimlik davranışları ve deneyimleri üzerinde tüm denetime izin veren bir ilke kümesinden oluşur.  Kimlik deneyimi çerçevesini kullanan Azure AD B2C özel ilkeler, kendi TF ' i tanımlayıp yapılandırabileceğiniz, bu tür bildirimli ilkeler aracılığıyla yazıp oluşturmanızı sağlar:

- TF ile ilgili olan topluluğun federal kimlik ekosistemini tanımlayan belge başvurusu veya başvuruları. Bunlar, TF belgelerinin bağlantılardır. (Önceden tanımlanmış) işlemsel "çalışma zamanı" kuralları ya da Kullanıcı, talepler için ve/veya kullanımını otomatik hale getirmeyi ve/veya denetlemeyi denetler. Bu Kullanıcı, bir LOA (ve LOP) ile ilişkilendirilir. Bu nedenle, bir ilke farklı LOAs (ve LOPs) ile Kullanıcı ile ilgili olabilir.

- Kimlik ve öznitelik sağlayıcıları veya talep sağlayıcılar, ilgilendikleri topluluk içinde ve bunlarla ilgili (bant dışı) LOA/LOP actatsyon ile birlikte destekledikleri teknik profiller.

- Öznitelik Doğrulayıcıları veya talep sağlayıcılarıyla tümleştirme.

- Topluluktaki bağlı olan taraflar (çıkarım).

- Katılımcılar arasında ağ iletişimleri oluşturmaya yönelik meta veriler. Bu meta veriler, teknik profillerle birlikte, bağlı olan taraf ve diğer topluluk katılımcıları arasında "tel çalışma" ile birlikte çalışabilirlik için bir işlem sırasında kullanılır.

- Varsa protokol dönüştürmesi (örneğin, SAML 2,0, OAuth2, WS-Federation ve OpenID Connect).

- Kimlik doğrulama gereksinimleri.

- Varsa çok faktörlü düzenlemesi.

- Kullanılabilen tüm talepler için paylaşılan bir şema ve ilgili bir topluluk katılımcılarının katılımcıları ile eşlemeler.

- Tüm talep dönüştürmelerinin yanı sıra bu bağlamdaki olası veriler en aza, taleplerin değişimine ve kullanımına dayanabilir.

- Bağlama ve şifreleme.

- Talep depolama alanı.

### <a name="understand-claims"></a>Talepleri anlama

> [!NOTE]
> "Talepler" olarak alışverişi gerekebilecek tüm olası kimlik bilgileri türlerine topluca başvurduk: son kullanıcının kimlik doğrulama kimlik bilgileri, kimlik erişimi, iletişim cihazı, fiziksel konum, kişisel tanımlama öznitelikleri, vb.  
>
> "Öznitelikler" yerine "talepler" terimini kullanıyoruz--çevrimiçi işlemlerde bu veri yapıtları, bağlı olan taraf tarafından doğrudan doğrulanmayacak olgular değildir. Bunlar, bağlı olan tarafın son kullanıcıya istenen işleme izin vermek için yeterli güvenle geliştirilmesi gereken olgular hakkında veya taleplerdir.  
>
> Ayrıca, kimlik deneyimi çerçevesini kullanan Azure AD B2C özel ilkeler, temel alınan protokolün ne olursa olsun, her türlü dijital kimlik bilgisinin Exchange 'i tutarlı bir şekilde basitleştirecek şekilde tasarlandığından, "talepler" terimini kullanırız. Kullanıcı kimlik doğrulaması veya öznitelik alımı için tanımlanır.  Benzer şekilde, belirli işlevleri arasında ayrım yapmak istemediğimiz zaman, kimlik sağlayıcılarına, öznitelik sağlayıcılarına ve öznitelik Doğrulayıcıları için "talep sağlayıcıları" terimini kullanırız.   

Bu nedenle, bir bağlı olan taraf, kimlik ve öznitelik sağlayıcıları ve öznitelik doğrulayıcıları arasında kimlik bilgilerinin nasıl değiştirildiğini yönetir. Bağlı olan tarafın kimlik doğrulaması için hangi kimlik ve öznitelik sağlayıcılarının gerekli olduğunu denetler. Bunlar, etki alanına özgü dil (DSL), diğer bir deyişle, devralma, *if* deyimleri, çok biçimlilik olan belirli bir uygulama etki alanı için özelleştirilmiş bir bilgisayar dili olarak düşünülmelidir.

Bu ilkeler, kimlik deneyimi çerçevesini kullanan Azure AD B2C özel ilkelerde, TF yapısının makine tarafından okunabilen bölümünü oluşturur. İşlem düzenleme ve Otomasyonu kolaylaştırmak için, talep sağlayıcıları ' meta verileri ve teknik profilleri, talep şeması tanımları, talep dönüştürme işlevleri ve Kullanıcı başvuruları dahil olmak üzere tüm işletimsel ayrıntıları içerirler.  

İçeriklerinin, ilkelerde bildirildiği etkin katılımcılar ile ilgili zaman içinde değişeceğinden emin olacağından, bunlar, *yaşayan belgeler* olarak kabul edilir. Ayrıca, katılımcı olma koşullarının ve koşulların değiştirebileceği da potansiyel olarak vardır.  

Federasyon kurulumu ve bakımı, farklı talep sağlayıcıları/doğrulayıcılar (tarafından temsil edilen topluluk) ilke kümesiyle sürekli güven ve bağlantı yeniden yapılandırmalarından koruma için büyük ölçüde basitleştirilmiştir.

Birlikte çalışabilirlik başka bir önemli zorluk dır. Bağlı olan taraflar gerekli tüm protokollerin desteklenmesi olası olduğundan, ek talep sağlayıcıları/Doğrulayıcıları tümleştirmelidir. Azure AD B2C özel ilkeler, sektör standardı protokollerini destekleyerek ve bağlı olan taraflar ve öznitelik sağlayıcılarının aynı protokolü desteklemediği durumlarda istekleri tersine çevir için belirli kullanıcı ilerliklerini uygulayarak bu sorunu çözebilir.  

Kullanıcı yolculukları, bağlı olan taraf ve diğer katılımcılar arasında "kabloda" birlikte çalışabilirliği açmak için kullanılan protokol profillerini ve meta verileri içerir. Ayrıca, TF belirtiminin bir parçası olarak yayımlanan ilkelerle uyumluluğu zorlamaya yönelik kimlik bilgileri Exchange istek/yanıt iletilerine uygulanan işletimsel çalışma zamanı kuralları vardır. Kullanıcı yolculukları, müşteri deneyiminin özelleştirilmesi için önemli bir uygulamadır. Ayrıca, sistemin protokol düzeyinde nasıl çalıştığı üzerinde de ışık vardır.

Bu temelde, bağlı olan taraf uygulamaları ve portalları kendi bağlamına bağlı olarak, belirli bir ilkenin adını geçirerek kimlik deneyimi çerçevesini kullanan Azure AD B2C özel ilkeler çağırabilir ve tam olarak davranış ve bilgi alışverişi yapabilir herhangi bir muslar, Fuss veya risk olmadan istedikleri gibi.
