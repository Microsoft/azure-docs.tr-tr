---
title: Microsoft Identity platformunda (v 1.0) kimlik doğrulaması | Mavisi
description: Microsoft Identity platform 'da kimlik doğrulamaya ilişkin temel bilgileri öğrenin. uygulama modeli, API, sağlama ve Microsoft Identity platformunun desteklediği en yaygın kimlik doğrulama senaryoları.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/14/2019
ms.author: ryanwi
ms.reviewer: saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c7bf80c0471853008573bbcbb918fb33c6c43cd
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73149185"
---
# <a name="what-is-authentication"></a>Kimlik doğrulaması nedir?

*Kimlik doğrulaması*, taraflardan geçerli kimlik bilgileri isteyerek kimlik ve erişim denetimi için kullanılacak bir güvenlik sorumlusu oluşturmaktır. Daha basit bir deyişle söylediğiniz kişi olduğunuzu kanıtlama işlemidir. Kimlik doğrulaması bazen AuthN şeklinde kısaltılabilir.

*Yetkilendirme*, kimliği doğrulanmış bir güvenlik sorumlusuna bir şeyi yapma iznini vermektir. Erişme iznine sahip olduğunuz verileri ve bu verilerle gerçekleştirebileceğiniz işlemleri belirtir. Yetkilendirme bazen AuthZ şeklinde kısaltılabilir.

Microsoft Identity platform, OAuth 2,0 ve OpenID Connect gibi endüstri standardı protokoller desteğiyle ve farklı platformlar için açık kaynak kitaplıkların yanı sıra, hizmet olarak kimlik sağlayarak uygulama geliştiricileri için kimlik doğrulamasını basitleştirir. kodlamaya hızlı bir şekilde başlamanıza yardımcı olur.

Microsoft Identity platform programlama modelinde iki birincil kullanım durumu vardır:

* OAuth 2.0 yetki verme akışı: Burada kaynak sahibi istemci uygulamayı yetkilendirerek istemcinin kaynak sahibinin kaynaklarına erişmesini sağlar.
* İstemci tarafından kaynak erişimi sırasında: Kaynak sunucusu erişim belirtecindeki talep değerlerini kullanarak erişim denetimi kararları alır.

## <a name="authentication-basics-in-microsoft-identity-platform"></a>Microsoft Identity platformunda kimlik doğrulama temelleri

Kimliğin gerekli olduğu en basit senaryoyu ele alalım: Bir kullanıcının web tarayıcısından bir web uygulaması için kimlik doğrulamasından geçmesi gerekiyor. Aşağıdaki diyagramda bu senaryo gösterilmektedir:

![Web uygulamasında oturum açmaya genel bakış](./media/v1-authentication-scenarios/auth-basics-microsoft-identity-platform.svg)

Diyagramda gösterilen bileşenlerle ilgili bilmeniz gerekenler burada v erilmiştir:

* Microsoft Identity platform, kimlik sağlayıcıdır. Kimlik sağlayıcısı, kuruluşun dizinindeki kullanıcıların ve uygulamaların kimliğinin doğrulanmasından sorumludur ve bu kullanıcılarla uygulamalar kimlik doğrulamasından başarıyla geçtiğinde güvenlik belirteçleri oluşturur.
* Microsoft Identity platformuna kimlik doğrulaması yapmak isteyen bir uygulama, Azure Active Directory (Azure AD) içinde kayıtlı olmalıdır. Azure AD, dizindeki her uygulamayı kaydeder ve benzersiz bir şekilde tanımlar.
* Geliştiriciler, sizin için protokol ayrıntılarını işleyerek kimlik doğrulama kolaylığı sağlamak üzere açık kaynaklı Microsoft Identity platform kimlik doğrulama kitaplıklarını kullanabilir. Daha fazla bilgi için bkz. Microsoft Identity Platform [v 2.0 kimlik doğrulama kitaplıkları](reference-v2-libraries.md) ve [v 1.0 kimlik doğrulama kitaplıkları](active-directory-authentication-libraries.md).
* Kullanıcının kimliği doğrulandıktan sonra uygulamanın kimlik doğrulamasının başarılı olduğundan emin olmak için kullanıcının güvenlik belirtecini doğrulaması gerekir. Farklı diller ve çerçeveler için uygulamanın yapması gereken işlemleri anlatan birçok hızlı başlangıç, öğretici ve kod örneği mevcuttur.
  * Hızlıca uygulama derlemek ve belirteç alma, belirteçleri yenileme, kullanıcı oturumu açma ve kullanıcı bilgilerini görüntüleme gibi işlevler eklemek için belgelerin **Hızlı başlangıçlar** bölümüne bakın.
  * Erişim belirteçleri alma ve bunları Microsoft Graph API ve diğer API'lere yapılan çağrılarda kullanma, OpenID Connect kullanarak geleneksel bir web tarayıcısı tabanlı uygulamaya Microsoft ile oturum açma özelliğini ekleme ve daha fazla geliştirici görevi için ayrıntılı ve senaryo tabanlı yordamlar için belgelerin **Öğreticiler** bölümüne bakın.
  * Kod örneklerini indirmek için [GitHub](https://github.com/Azure-Samples?q=active-directory)'a gidin.
* Kimlik doğrulaması işlemi için istek ve yanıt akışı OAuth 2.0, OpenID Connect, WS-Federation veya SAML 2.0 gibi kullandığınız kimlik doğrulaması protokolü tarafından belirlenir. Protokoller hakkında daha fazla bilgi için, belgelerinin **> kimlik doğrulama protokolü** bölümüne bakın.

Yukarıdaki örnek senaryoda uygulamaları bu iki role göre sınıflandırabilirsiniz:

* Kaynaklara güvenli bir şekilde erişmesi gereken uygulamalar
* Kaynağın rolünü oynaması gereken uygulamalar

### <a name="how-each-flow-emits-tokens-and-codes"></a>Her akışın belirteçleri ve kodları nasıl yayar

İstemcinizin nasıl oluşturulduğuna bağlı olarak, Microsoft Identity platform tarafından desteklenen kimlik doğrulama akışlarının birini (veya birkaçını) kullanabilir.  Bu akışlar çeşitli belirteçler (id_tokens, yenileme belirteçleri, erişim belirteçleri) ve yetkilendirme kodlarını oluşturabilir ve bunların çalışmasını sağlamak için farklı belirteçler gerektirebilir. Bu grafik genel bakış sağlar:

|Akış | Gerektirmeyen | id_token | erişim belirteci | belirteci Yenile | yetkilendirme kodu | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Yetkilendirme kodu akışı](v1-protocols-oauth-code.md) | | x | x | x | x|  
|[Örtük akış](v1-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Karma OıDC akışı](v1-protocols-openid-connect-code.md#get-access-tokens)| | x  | |          |            x   |
|[Belirteç satın alma yenileme](v1-protocols-oauth-code.md#refreshing-the-access-tokens) | belirteci Yenile | x | x | x| |
|[On-behalf-of akışı](v1-oauth2-on-behalf-of-flow.md) | erişim belirteci| x| x| x| |
|[İstemci kimlik bilgileri](v1-oauth2-client-creds-grant-flow.md) | | | x (yalnızca uygulama)| | |

Örtülü mod aracılığıyla yayınlanan belirteçlerin, URL aracılığıyla tarayıcıya geri geçirilmesi nedeniyle bir uzunluk sınırlaması vardır (`response_mode` `query` veya `fragment`).  Bazı tarayıcıların, tarayıcı çubuğuna koyabileceğiniz ve çok uzun olduğunda başarısız olan URL 'nin boyutunda bir sınırı vardır.  Bu nedenle, bu belirteçlerin `groups` veya `wids` talepleri yoktur. 

Temel bilgilere genel bir bakış edindiğinize göre, kimlik uygulama modeli ve API 'sini, sağlama işlemini Microsoft Identity platformunda nasıl çalıştığını ve Microsoft Identity platformunun desteklediği yaygın senaryolar hakkında ayrıntılı bilgi bağlantılarını öğrenmek için okumaya devam edin.

## <a name="application-model"></a>Uygulama modeli

Microsoft Identity platform iki ana işlevi yerine getirmek için tasarlanan belirli bir modeli takip eden uygulamaları temsil eder:

* **Uygulamayı desteklediği kimlik doğrulaması protokollerine göre tanımlama**: Bu model tüm tanımlayıcıların, URL'lerin, gizli dizilerin ve kimlik doğrulaması sırasında gerekli olan ilgili bilgilerin listelenmesini kapsar. Burada, Microsoft Identity Platform:

    * Çalışma zamanında kimlik doğrulamasını gerçekleştirmek için gerekli tüm verileri barındırır.
    * Bir uygulamanın erişmesi gerekebilecek kaynakları ve belirli bir isteğin gerçekleştirilip gerçekleştirilmeyeceğini ve hangi koşullarda gerçekleştirileceğini belirlemek için gerekli tüm verileri barındırır.
    * Uygulama geliştiricisinin kiracısında ve diğer Azure AD kiracısında uygulama sağlamak için gerekli altyapıyı sağlar.

* **Belirteç istek süresi boyunca Kullanıcı onayını işleyin ve kiracılar genelinde uygulamaların dinamik sağlamasını kolaylaştırır** -burada, Microsoft Identity Platform:

    * Kullanıcıların ve yöneticilerin uygulamanın kendileri adına kaynaklara erişmesine dinamik olarak onay vermesini veya reddetmesini sağlar.
    * Yöneticilerin uygulamaların gerçekleştirebilecekleri işlemler, belirli uygulamalara erişebilecek kullanıcılar ve erişilen dizin kaynakları hakkında son kararı vermesini sağlar.

Microsoft Identity platformunda bir **uygulama nesnesi** , bir uygulamayı soyut bir varlık olarak açıklar. Geliştiriciler uygulamalarla çalışır. Dağıtım zamanında, Microsoft Identity platform bir dizin veya kiracı içindeki bir uygulamanın somut örneğini temsil eden bir **hizmet sorumlusu**oluşturmak için bir şema olarak belirli bir uygulama nesnesini kullanır. Uygulamanın belirli bir hedef dizinde yapabileceklerini, onu kullanabilecek kişileri, erişim sağlayabileceği kaynakları ve diğer bilgileri tanımlayan hizmet sorumlusudur. Microsoft Identity platform bir uygulama nesnesinden **onay**aracılığıyla bir hizmet sorumlusu oluşturur.

Aşağıdaki diyagramda, izin tarafından yönetilen basitleştirilmiş bir Microsoft Identity platformu sağlama akışı gösterilmektedir.  Bu durumda, kiracının uygulamanın sahibi olduğu iki kiracı (A ve B) vardır ve B kiracısı bir hizmet sorumlusu aracılığıyla uygulamayı örnekledir.  

![Onay temelli basitleştirilmiş sağlama akışı](./media/v1-authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

Bu sağlama akışında:

1. B kiracısından bir kullanıcı uygulamayla oturum açmaya çalışır, yetkilendirme uç noktası uygulama için bir belirteç ister.
1. Kimlik doğrulaması için Kullanıcı kimlik bilgileri alındı ve doğrulandı
1. Kullanıcıdan, B kiracısına erişim kazanmak için uygulamanın onayını sağlaması istenir
1. Microsoft Identity platform B kiracısında bir hizmet sorumlusu oluşturmak için bir şema olarak kiracı 'daki uygulama nesnesini kullanır
1. Kullanıcı istenen belirteci alır

Bu işlemi diğer kiracılar için (C, D vb.) istediğiniz kadar tekrarlayabilirsiniz. Kiracı A, uygulama için şemayı saklar (uygulama nesnesi). Uygulama için onay verilen diğer kiracıların kullanıcıları ve yöneticileri kiracıdaki hizmet sorumlusu nesnesini kullanarak uygulamanın gerçekleştirmesine izin verilen işlemleri belirler. Daha fazla bilgi için bkz. [Microsoft Identity platformunda uygulama ve hizmet sorumlusu nesneleri](app-objects-and-service-principals.md).

## <a name="claims-in-microsoft-identity-platform-security-tokens"></a>Microsoft Identity platform güvenlik belirteçlerinde talepler

Microsoft Identity platform tarafından verilen güvenlik belirteçleri (erişim ve KIMLIK belirteçleri) talepler veya kimliği doğrulanmış konuyla ilgili bilgi onaylamaları içerir. Uygulamalar talepleri birçok görev için kullanabilir; bazıları:

* Belirteci doğrulama
* Öznenin dizin kiracısını tanımlama
* Kullanıcı bilgilerini görüntüleme
* Öznenin yetkilendirme durumunu belirleme

Herhangi bir güvenlik belirtecindeki talepler belirteç türüne, kullanıcının kimliğini doğrulamak için kullanılan kimlik bilgisi türüne ve uygulama yapılandırmasına göre değişiklik gösterir.

Aşağıdaki tabloda Microsoft Identity platform tarafından yayılan her bir talep türünün kısa bir açıklaması verilmiştir. Daha ayrıntılı bilgi için bkz. Microsoft Identity platform tarafından verilen [erişim belirteçleri](access-tokens.md) ve [Kimlik belirteçleri](id-tokens.md) .

| İste | Açıklama |
| --- | --- |
| Uygulama Kimliği | Belirteci kullanan uygulamayı tanımlar. |
| Hedef Kitle | Belirtecin gönderileceği alıcı kaynağını tanımlar. |
| Uygulama Kimlik Doğrulaması Bağlamı Sınıf Başvurusu | İstemcinin kimliğinin nasıl doğrulandığını belirtir (genel istemci veya gizli istemci). |
| Kimlik Doğrulaması Anı | Kimlik doğrulamasının gerçekleştirildiği tarihi ve saati kaydeder. |
| Kimlik Doğrulama Yöntemi | Belirtecin kimlik doğrulama yöntemini (parola, sertifika vb.) belirtir. |
| Ad | Kullanıcının Azure AD'deki adını sağlar. |
| Gruplar | Kullanıcının üyesi olduğu Azure AD gruplarının nesne kimliklerini içerir. |
| Kimlik Sağlayıcısı | Belirtecin öznesinin kimliğini doğrulayan kimlik sağlayıcısını kaydeder. |
| Verilme Zamanı | Belirtecin verilme zamanını kaydeder ve bu değer genellikle belirtecin ne kadar güncel olduğunu anlamak için kullanılır. |
| Veren | Belirteci oluşturan STS ve Azure AD kiracısını tanımlar. |
| Soyadı | Kullanıcının Azure AD'deki soyadını sağlar. |
| Adı | Belirtecin konusunu tanımlayan ve okunabilir bir değer sunar. |
| Nesne Kimliği | Öznenin Azure AD'deki değişmez ve benzersiz tanıtıcısını içerir. |
| Roller | Kullanıcıya verilmiş olan Azure AD Uygulama Rollerinin kolay adlarını içerir. |
| Kapsam | İstemci uygulamasına verilmiş olan izinleri belirtir. |
| Özne | Belirtecin bilgi verdiği sorumluyu belirtir. |
| Kiracı Kimliği | Belirteci düzenleyen dizin kiracısının değişmez ve benzersiz tanıtıcısını içerir. |
| Belirteç Ömrü | Belirtecin geçerli olduğu zaman aralığını tanımlar. |
| Kullanıcı Asıl Adı | Öznenin kullanıcı asıl adını içerir. |
| Sürüm | Belirtecin sürüm numarasını içerir. |

## <a name="next-steps"></a>Sonraki adımlar

* [Microsoft Identity platformunda desteklenen uygulama türleri ve senaryoları](app-types.md) hakkında bilgi edinin
