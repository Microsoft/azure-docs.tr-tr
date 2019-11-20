---
title: Azure Active Directory 'de yapılandırılabilir belirteç yaşam süreleri
titleSuffix: Microsoft identity platform
description: Azure AD tarafından verilen belirteçler için yaşam sürelerini ayarlamayı öğrenin.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 06f5b317-053e-44c3-aaaa-cf07d8692735
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: ryanwi
ms.custom: aaddev, annaba, identityplatformtop40
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 021d0c19ecc4bf63861bf95d99b6ba6b8e910220
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74046564"
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-preview"></a>Azure Active Directory 'de yapılandırılabilir belirteç yaşam süreleri (Önizleme)

Azure Active Directory (Azure AD) tarafından verilen belirtecin ömrünü belirtebilirsiniz. Kuruluşunuzdaki tüm uygulamalar için, çok kiracılı (çok kullanıcılı) bir uygulama veya kuruluşunuzdaki belirli bir hizmet sorumlusu için belirteç yaşam sürelerini ayarlayabilirsiniz.

> [!IMPORTANT]
> Önizleme sırasında müşterilerden bir duyduktan sonra Azure AD koşullu erişim 'de [kimlik doğrulama oturumu yönetimi özelliklerini](https://go.microsoft.com/fwlink/?linkid=2083106) uyguladık. Bu yeni özelliği, oturum açma sıklığını ayarlayarak yenileme belirteci yaşam sürelerini yapılandırmak için kullanabilirsiniz. 1 Mayıs 2020 ' den sonra, oturum yapılandırmak ve belirteçleri yenilemek için yapılandırılabilir belirteç yaşam süresi ilkesini kullanamazsınız. Kullanımdan kaldırıldıktan sonra erişim belirteci yaşam sürelerini yapılandırabilirsiniz.

Azure AD 'de bir ilke nesnesi, tek tek uygulamalarda veya bir kuruluştaki tüm uygulamalarda zorlanan bir kurallar kümesini temsil eder. Her ilke türünün, atandıkları nesnelere uygulanan bir özellikler kümesi ile benzersiz bir yapısı vardır.

Bir ilkeyi kuruluşunuz için varsayılan ilke olarak belirleyebilirsiniz. İlke, daha yüksek önceliğe sahip bir ilke tarafından geçersiz kılınmadığı sürece, kuruluştaki herhangi bir uygulamaya uygulanır. Ayrıca, belirli uygulamalara bir ilke atayabilirsiniz. Öncelik sırası, ilke türüne göre farklılık gösterir.

> [!NOTE]
> Yapılandırılabilir belirteç ömür ilkesi, SharePoint Online için desteklenmiyor.  Bu ilkeyi PowerShell aracılığıyla oluşturma olanağına sahip olsanız da, SharePoint Online Bu ilkeyi kabul etmez. Boş oturum zaman aşımlarını yapılandırma hakkında daha fazla bilgi edinmek için [SharePoint Online bloguna](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) bakın.
>* SharePoint Online erişim belirtecinin varsayılan yaşam süresi 1 saattir. 
>* SharePoint Online yenileme belirtecinin varsayılan en fazla etkin olmayan süresi 90 gündür.


## <a name="token-types"></a>Belirteç türleri

Yenileme belirteçleri, erişim belirteçleri, SAML belirteçleri, oturum belirteçleri ve KIMLIK belirteçleri için belirteç ömür ilkeleri ayarlayabilirsiniz.

### <a name="access-tokens"></a>Erişim belirteçleri

İstemciler korumalı bir kaynağa erişmek için erişim belirteçlerini kullanır. Erişim belirteci, yalnızca belirli bir Kullanıcı, istemci ve kaynak birleşimi için kullanılabilir. Erişim belirteçleri iptal edilemez ve süresi sona ermeden geçerli olur. Erişim belirteci elde eden kötü niyetli bir aktör bunu yaşam süresinin kapsamı için kullanabilir. Erişim belirtecinin kullanım süresini ayarlamak, sistem performansını artırma ve kullanıcının hesabı devre dışı bırakıldıktan sonra istemcinin erişimi koruduğunu geçen süreyi artırma arasında bir denge. Gelişmiş sistem performansı, bir istemcinin yeni bir erişim belirteci almak için ihtiyaç duymasının kaç kez daha fazla azaltılmasıyla elde edilir.  Varsayılan değer 1 saattir-1 saat sonra, istemcinin yenileme belirtecini kullanması (genellikle sessizce) yeni bir yenileme belirteci ve erişim belirteci almaları gerekir. 

### <a name="saml-tokens"></a>SAML belirteçleri

SAML belirteçleri birçok Web tabanlı SAAS uygulaması tarafından kullanılır ve Azure Active Directory SAML2 protokol uç noktası kullanılarak elde edilir. Bunlar, WS-Federation kullanan uygulamalar tarafından da kullanılır. Belirtecin varsayılan yaşam süresi 1 saattir. Bir uygulamanın perspektifinden, belirtecin geçerlilik süresi, belirteçteki `<conditions …>` öğesinin NotOnOrAfter değeri tarafından belirtilir. Belirtecin geçerlilik süresi sona erdikten sonra, istemcinin yeni bir kimlik doğrulama isteği başlatması gerekir. Bu, genellikle çoklu oturum açma (SSO) oturum belirtecinin bir sonucu olarak etkileşimli oturum açmadan memnun olur.

NotOnOrAfter değeri bir `TokenLifetimePolicy``AccessTokenLifetime` parametresi kullanılarak değiştirilebilir. Varsa, ilkede yapılandırılan yaşam süresine ayarlanır ve beş dakikalık bir saat eğriltme faktörü olur.

`<SubjectConfirmationData>` öğesinde belirtilen konu onayı NotOnOrAfter, belirteç ömür yapılandırmasından etkilenmediğini unutmayın. 

### <a name="refresh-tokens"></a>Belirteçleri Yenile

İstemci korunan bir kaynağa erişmek için bir erişim belirteci aldığında, istemci Ayrıca bir yenileme belirteci alır. Yenileme belirteci, geçerli erişim belirtecinin süresi dolmuşsa yeni erişim/yenileme belirteci çiftleri almak için kullanılır. Yenileme belirteci, Kullanıcı ve istemci birleşimine bağlanır. Yenileme belirteci [herhangi bir zamanda iptal](access-tokens.md#token-revocation)edilebilir ve belirteç her kullanıldığında belirtecin geçerliliği denetlenir.  Yeni erişim belirteçleri getirmek için kullanıldığında yenileme belirteçleri iptal edilmez; bununla birlikte, yeni bir belirteç alırken eski belirteci güvenli bir şekilde silmek için en iyi uygulamadır. 

Gizli istemcilerle ortak istemciler arasında ayrım yapmak önemlidir, çünkü yenileme belirteçlerinin ne kadar süreyle kullanılabileceğini etkiler. Farklı istemci türleri hakkında daha fazla bilgi için bkz. [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Gizli istemci yenileme belirteçleriyle belirteç yaşam süreleri
Gizli istemciler, bir istemci parolasını (gizli) güvenli bir şekilde depolayabilen uygulamalardır. İsteklerin kötü niyetli bir aktörden değil, güvenli istemci uygulamasından geldiğini kanıtlayabilirler. Örneğin, bir Web uygulaması Web sunucusunda bir istemci gizli dizisi depolayabildiğinden gizli bir istemcdir. Gösterilmez. Bu akışlar daha güvenli olduğundan, bu akışlara verilen yenileme belirteçlerinin varsayılan yaşam süreleri `until-revoked`, ilke kullanılarak değiştirilemez ve gönüllü parola sıfırlamaları sırasında iptal edilmez.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Ortak istemci yenileme belirteçleri ile belirteç yaşam süreleri

Ortak istemciler, bir istemci parolasını (gizli) güvenli bir şekilde depolayaamaz. Örneğin, bir iOS/Android uygulaması, kaynak sahibinden bir parolayı gizli hale getirebilir, bu yüzden ortak istemci olarak kabul edilir. Belirli bir süreden daha eski olan ortak istemcilerden gelen yenileme belirteçlerinin, yeni bir erişim/yenileme belirteci çifti almasını engellemek için kaynaklar üzerinde ilkeler ayarlayabilirsiniz. (Bunu yapmak için, yenileme belirteci en fazla etkin olmayan süre özelliğini kullanın (`MaxInactiveTime`).) Ayrıca, yenileme belirteçlerinin daha fazla kabul edilmeden önce bir süre ayarlamak için ilkeleri kullanabilirsiniz. (Bunu yapmak için, belirteç en fazla kullanım süresini Yenile özelliğini kullanın.) Bir yenileme belirtecinin kullanım süresini, bir genel istemci uygulaması kullanılırken sessizce yeniden kimlik doğrulaması yapmak yerine, kullanıcının kimlik bilgilerini ne zaman ve ne sıklıkta yeniden girmesi gerektiğini denetlemek için ayarlayabilirsiniz.

> [!NOTE]
> Maksimum yaş özelliği, tek bir belirtecin kullanılabileceği sürenin uzunluğudur. 

### <a name="id-tokens"></a>Kimlik belirteçleri
KIMLIK belirteçleri Web sitelerine ve yerel istemcilere geçirilir. KIMLIK belirteçleri bir kullanıcıyla ilgili profil bilgilerini içerir. KIMLIK belirteci, belirli bir Kullanıcı ve istemci birleşimine bağlanır. KIMLIK belirteçleri, süresi doluncaya kadar geçerli kabul edilir. Genellikle, bir Web uygulaması, kullanıcının uygulamadaki oturum ömrünü Kullanıcı için verilen KIMLIK belirtecinin ömrü boyunca eşleştirir. Web uygulamasının uygulama oturumunun ne sıklıkta dolacağını ve kullanıcının Azure AD ile yeniden kimlik doğrulaması gerektirdiğini (sessizce veya etkileşimli) denetlemek için bir KIMLIK belirtecinin ömrünü ayarlayabilirsiniz.

### <a name="single-sign-on-session-tokens"></a>Çoklu oturum açma oturum belirteçleri
Bir Kullanıcı Azure AD ile kimlik doğrulaması yaparken, kullanıcının tarayıcısı ve Azure AD ile çoklu oturum açma oturumu (SSO) oluşturulur. Bir tanımlama bilgisi biçimindeki SSO belirteci bu oturumu temsil eder. SSO oturum belirteci belirli bir kaynak/istemci uygulamasına bağlanmamış. SSO oturum belirteçleri iptal edilebilir ve bunların geçerlilik süresi her kullanıldığında denetlenir.

Azure AD, iki tür SSO oturum belirteci kullanır: kalıcı ve kalıcı olmayan. Kalıcı oturum belirteçleri, tarayıcı tarafından kalıcı tanımlama bilgileri olarak depolanır. Kalıcı olmayan oturum belirteçleri oturum tanımlama bilgileri olarak depolanır. (Tarayıcı kapalıyken oturum tanımlama bilgileri yok edilir.) Genellikle kalıcı olmayan bir oturum belirteci depolanır. Ancak Kullanıcı kimlik doğrulaması sırasında Oturumumu **açık tut** onay kutusunu seçtiğinde kalıcı bir oturum belirteci depolanır.

Kalıcı olmayan oturum belirteçlerinin 24 saat ömrü vardır. Kalıcı belirteçlerde 180 gün ömrü vardır. Geçerlilik süresi içinde SSO oturumu belirteci kullanıldığında, belirteç türüne bağlı olarak, geçerlilik süresi bir 24 saat veya 180 gün daha genişletilir. Bir SSO oturum belirteci geçerlilik süresi içinde kullanılmıyorsa, süresi dolmuş olarak kabul edilir ve artık kabul edilmez.

Oturum belirtecinin artık kabul edilmeden önce ilk oturum belirtecinin verilme zamanından sonraki süreyi ayarlamak için bir ilke kullanabilirsiniz. (Bunu yapmak için, oturum belirteci en yüksek yaş özelliğini kullanın.) Bir Web uygulaması kullanılırken sessizce kimlik doğrulamasından geçmek yerine, bir kullanıcının kimlik bilgilerini ne zaman ve ne sıklıkta yeniden girmesi gerektiğini denetlemek için bir oturum belirtecinin ömrünü ayarlayabilirsiniz.

### <a name="token-lifetime-policy-properties"></a>Belirteç ömür ilkesi özellikleri
Belirteç ömür ilkesi, belirteç ömrü kurallarını içeren bir ilke nesnesi türüdür. Belirtilen belirteç ömrünü denetlemek için ilkenin özelliklerini kullanın. İlke ayarlanmamışsa, sistem varsayılan yaşam süresi değerini uygular.

### <a name="configurable-token-lifetime-properties"></a>Yapılandırılabilir belirteç ömrü özellikleri
| Özellik | İlke Özellik dizesi | Ekranlarını | Varsayılan | Minimum | Maksimum |
| --- | --- | --- | --- | --- | --- |
| Erişim belirteci ömrü |AccessTokenLifetime<sup>2</sup> |Erişim belirteçleri, KIMLIK belirteçleri, SAML2 belirteçleri |1 saat |10 dakika |1 gün |
| Yenileme belirteci en fazla etkin olmayan süre |Maxınactivetime |Belirteçleri Yenile |90 gün |10 dakika |90 gün |
| Tek faktörlü yenileme belirtecinin en fazla yaşı |Maxagesinglefaktör |Belirteçleri Yenile (tüm kullanıcılar için) |İptal edilene kadar |10 dakika |Until-iptal edildi<sup>1</sup> |
| Multi-Factor Refresh belirtecinin en fazla yaşı |MaxAgeMultiFactor |Belirteçleri Yenile (tüm kullanıcılar için) |İptal edilene kadar |10 dakika |Until-iptal edildi<sup>1</sup> |
| Tek faktörlü oturum belirteci maksimum yaşı |Maxagesessionsinglefaktör |Oturum belirteçleri (kalıcı ve kalıcı olmayan) |İptal edilene kadar |10 dakika |Until-iptal edildi<sup>1</sup> |
| Multi-Factor Session belirtecinin en fazla yaşı |MaxAgeSessionMultiFactor |Oturum belirteçleri (kalıcı ve kalıcı olmayan) |İptal edilene kadar |10 dakika |Until-iptal edildi<sup>1</sup> |

* <sup>1</sup>365 gün, bu öznitelikler için ayarlanoluşturulabilecek maksimum açık uzunluktadır.
* <sup>2</sup> Microsoft ekipleri web istemcisinin çalıştığından emin olmak için, Microsoft ekipleri için AccessTokenLifetime 'ın 15 dakikadan fazla tutulması önerilir.

### <a name="exceptions"></a>Özel durumlar
| Özellik | Ekranlarını | Varsayılan |
| --- | --- | --- |
| Yenileme belirteci maksimum yaşı (yetersiz iptal bilgileri<sup>1</sup>olan Federasyon kullanıcıları için verilir) |Belirteçleri Yenile (yetersiz iptal bilgileri<sup>1</sup>olan Federasyon kullanıcıları için verilir) |12 saat |
| Yenileme belirteci en fazla etkin olmayan süre (gizli istemciler için verilir) |Belirteçleri Yenile (gizli istemciler için verilen) |90 gün |
| Yenileme belirteci en fazla yaş (gizli istemciler için verilen) |Belirteçleri Yenile (gizli istemciler için verilen) |İptal edilene kadar |

* <sup>1</sup> Yetersiz iptal bilgisine sahip Federasyon kullanıcıları, "LastPasswordChangeTimestamp" özniteliğine sahip olmayan tüm kullanıcıları içerir. AAD, eski bir kimlik bilgisine (değiştirilmiş bir parola gibi) bağlı belirteçlerin iptal edileceği zamanı doğrulayamadığından ve Kullanıcı ve ilişkili belirteçlerin hala iyi durumda olduğundan emin olmak için daha sık yeniden kontrol etmeleri gereken bu kısa en fazla yaş olarak verilir  göz. Bu deneyimi geliştirmek için, kiracı yöneticilerinin "LastPasswordChangeTimestamp" özniteliğini eşitlediklerinden emin olması gerekir (Bu, PowerShell kullanılarak veya AADSync aracılığıyla Kullanıcı nesnesinde ayarlanabilir).

### <a name="policy-evaluation-and-prioritization"></a>İlke değerlendirmesi ve önceliği belirleme
Belirli bir uygulamaya, kuruluşunuza ve hizmet sorumlularına bir belirteç ömür İlkesi oluşturup bu ilke atayabilirsiniz. Belirli bir uygulama için birden çok ilke uygulanabilir. Geçerli olan belirteç ömrü ilkesi şu kurallara uyar:

* Bir ilke hizmet sorumlusuna açıkça atanmışsa, zorlanır.
* Hizmet sorumlusuna açıkça bir ilke atanmamışsa, hizmet sorumlusunun ana kuruluşa açıkça atanan bir ilke zorlanır.
* Hizmet sorumlusuna veya kuruluşa açıkça bir ilke atanmamışsa, uygulamaya atanan ilke zorlanır.
* Hizmet sorumlusuna, kuruluşa veya uygulama nesnesine bir ilke atanmamışsa, varsayılan değerler zorlanır. ( [Yapılandırılabilir belirteç ömrü özelliklerindeki](#configurable-token-lifetime-properties)tabloya bakın.)

Uygulama nesneleri ve hizmet sorumlusu nesneleri arasındaki ilişki hakkında daha fazla bilgi için, bkz. [Azure Active Directory uygulama ve hizmet sorumlusu nesneleri](app-objects-and-service-principals.md).

Belirtecin geçerliliği, belirtecin kullanıldığı sırada değerlendirilir. Erişildiği uygulama üzerinde en yüksek önceliğe sahip ilke devreye girer.

Burada kullanılan tüm zaman C# [aralığı](/dotnet/api/system.timespan) ,-D. hh: mm: ss TimeSpan nesnesine göre biçimlendirilir.  Bu nedenle 80 gün ve 30 dakika `80.00:30:00`.  0 olduğunda önde gelen D bırakılabilir, bu nedenle 90 dakika `00:90:00`.  

> [!NOTE]
> İşte örnek bir senaryo.
>
> Kullanıcı iki Web uygulamasına erişmek istiyor: Web uygulaması A ve Web uygulaması B.
> 
> Etmen
> * Her iki Web uygulaması da aynı ana kuruluştur.
> * Bir oturum belirteci en fazla sekiz saat olan belirteç ömür Ilkesi 1, ana kuruluşun varsayılan değeri olarak ayarlanır.
> * Web uygulaması, düzenli olarak kullanılan bir Web uygulamasıdır ve hiçbir ilkeye bağlanmaz.
> * Web uygulaması B, son derece duyarlı süreçler için kullanılır. Hizmet sorumlusu, en fazla 30 dakikalık bir oturum belirteci olan belirteç ömür Ilkesi 2 ' ye bağlıdır.
>
> 12:00 PM 'de Kullanıcı yeni bir tarayıcı oturumu başlatır ve Web uygulamasına erişmeye çalışır. Kullanıcı Azure AD 'ye yönlendirilir ve oturum açması istenir. Bu, tarayıcıda oturum belirtecine sahip bir tanımlama bilgisi oluşturur. Kullanıcı, kullanıcının uygulamaya erişmesine izin veren bir KIMLIK belirteciyle bir Web uygulamasına yeniden yönlendirilir.
>
> 12:15 ' de, Kullanıcı B Web uygulamasına erişmeye çalışır. Tarayıcı, oturum tanımlama bilgisini algılayan Azure AD 'ye yeniden yönlendirir. Web uygulaması B 'nin hizmet sorumlusu, belirteç ömür Ilkesi 2 ' ye bağlıdır, ancak aynı zamanda üst kuruluşun bir parçasıdır ve varsayılan belirteç yaşam süresi Ilke 1 ' dir. Belirteç yaşam süresi Ilkesi 2, hizmet sorumlularıyla bağlantılı ilkelerin kuruluş varsayılan ilkelerine kıyasla daha yüksek bir önceliğe sahip olduğu için devreye girer. Oturum belirteci ilk olarak son 30 dakika içinde verildi, bu nedenle geçerli kabul edilir. Kullanıcı, erişim izni veren bir KIMLIK belirteciyle B Web uygulamasına yeniden yönlendirilir.
>
> 1:00 ' de, Kullanıcı A Web uygulamasına erişmeyi dener. Kullanıcı Azure AD 'ye yeniden yönlendirilir. A Web uygulaması hiçbir ilkeyle bağlantılı değildir, ancak varsayılan belirteç ömür Ilkesi 1 olan bir kuruluşta olduğundan, bu ilke geçerli olur. Son sekiz saat içinde ilk olarak verilen oturum tanımlama bilgisi algılanır. Kullanıcı yeni bir KIMLIK belirteci ile sessizce Web uygulamasına geri yönlendirilir. Kullanıcının kimlik doğrulaması için gerekli değildir.
>
> Daha sonra Kullanıcı, B Web uygulamasına erişmeye çalışır. Kullanıcı Azure AD 'ye yeniden yönlendirilir. Daha önce olduğu gibi, belirteç ömür Ilkesi 2 etkili olur. Belirteç 30 dakikadan önce verildiği için kullanıcıdan oturum açma kimlik bilgilerini yeniden girmesi istenir. Yepyeni bir oturum belirteci ve KIMLIK belirteci verilir. Kullanıcı daha sonra B Web uygulamasına erişebilir.
>
>

## <a name="configurable-policy-property-details"></a>Yapılandırılabilir ilke özelliği ayrıntıları
### <a name="access-token-lifetime"></a>Erişim belirteci ömrü
**Dize:** AccessTokenLifetime

**Şunları etkiler:** Erişim belirteçleri, KIMLIK belirteçleri, SAML belirteçleri

**Özet:** Bu ilke, bu kaynak için ne kadar erişimin ve KIMLIK belirteçlerinin geçerli kabul edileceğini denetler. Erişim belirteci yaşam süresi özelliğinin azaltılması, bir erişim belirtecinin veya kötü amaçlı aktör tarafından uzun bir süre için kullanılan KIMLIK belirtecinin riskini azaltır. (Bu belirteçler iptal edilemez.) Bu konuda, belirteçlerin daha sık değiştirilmeleri gerektiğinden, performansın olumsuz bir şekilde etkilenmesi önemlidir.

### <a name="refresh-token-max-inactive-time"></a>Yenileme belirteci en fazla etkin olmayan süre
**Dize:** Maxınactivetime

**Şunları etkiler:** Belirteçleri Yenile

**Özet:** Bu ilke, bir istemcinin bu kaynağa erişmeye çalışırken yeni bir erişim/yenileme belirteci çifti almak için daha önce bir yenileme belirtecinin ne kadar eski olduğunu denetler. Yenileme belirteci kullanılırken genellikle yeni bir yenileme belirteci döndürüldüğünden, istemci belirli bir süre boyunca geçerli yenileme belirtecini kullanarak herhangi bir kaynağa erişmeyi denediğinde Bu ilke erişimi engeller.

Bu ilke, istemcileri üzerinde etkin olmayan kullanıcıları yeni bir yenileme belirteci almak için yeniden kimlik doğrulaması yapmak üzere zorlar.

Yenileme belirteci en fazla etkin olmayan süre özelliği, tek faktörlü belirteç en yüksek yaş ve Multi-Factor Refresh belirtecinin en fazla yaş özelliklerinden daha düşük bir değere ayarlanmalıdır.

### <a name="single-factor-refresh-token-max-age"></a>Tek faktörlü yenileme belirtecinin en fazla yaşı
**Dize:** Maxagesinglefaktör

**Şunları etkiler:** Belirteçleri Yenile

**Özet:** Bu ilke, bir kullanıcının en son kimlik doğrulamasından sonra yalnızca tek bir faktör kullanarak başarıyla bir erişim/yenileme belirteci kullanması için yenileme belirtecini ne kadar süreyle kullandığını denetler. Kullanıcı kimlik doğrulamasından geçtikten ve yeni bir yenileme belirteci aldıktan sonra, Kullanıcı belirli bir süre için yenileme belirteci akışını kullanabilir. (Geçerli yenileme belirteci iptal edilmediği ve etkin olmayan süreden uzun süredir kullanılmamış olmadığı sürece bu doğrudur.) Bu noktada, Kullanıcı yeni yenileme belirteci almak için yeniden kimlik doğrulaması yapmak üzere zorlanır.

Maksimum yaşı azaltmak, kullanıcıların daha sık kimlik doğrulamasından geçmesini zorlar. Tek faktörlü kimlik doğrulamasının Multi-Factor Authentication 'dan daha az güvenli olduğu kabul edildiğinden, bu özelliği Multi-Factor Refresh belirtecinin maksimum yaşı özelliğinden eşit veya ondan daha küçük bir değere ayarlamanızı öneririz.

### <a name="multi-factor-refresh-token-max-age"></a>Multi-Factor Refresh belirtecinin en fazla yaşı
**Dize:** MaxAgeMultiFactor

**Şunları etkiler:** Belirteçleri Yenile

**Özet:** Bu ilke, bir kullanıcının birden çok faktör kullanarak başarıyla kimlik doğrulamasından sonra yeni erişim/yenileme belirteci eşleştirmesi için yenileme belirtecini ne kadar süreyle kullandığını denetler. Kullanıcı kimlik doğrulamasından geçtikten ve yeni bir yenileme belirteci aldıktan sonra, Kullanıcı belirli bir süre için yenileme belirteci akışını kullanabilir. (Geçerli yenileme belirteci iptal edilmediği ve etkin olmayan süreden uzun süre kullanılmamış olduğu sürece bu doğru olur.) Bu noktada, kullanıcılar yeni yenileme belirteci almak için yeniden kimlik doğrulaması yapmak üzere zorlanır.

Maksimum yaşı azaltmak, kullanıcıların daha sık kimlik doğrulamasından geçmesini zorlar. Tek faktörlü kimlik doğrulamasının Multi-Factor Authentication 'dan daha az güvenli olduğu kabul edildiğinden, bu özelliği tek faktörlü yenileme belirteci en yüksek yaş özelliğinden eşit veya ondan daha büyük bir değere ayarlamanızı öneririz.

### <a name="single-factor-session-token-max-age"></a>Tek faktörlü oturum belirteci maksimum yaşı
**Dize:** Maxagesessionsinglefaktör

**Şunları etkiler:** Oturum belirteçleri (kalıcı ve kalıcı olmayan)

**Özet:** Bu ilke, bir kullanıcının en son kimlik doğrulamasından geçtikten sonra yalnızca tek bir faktör kullanarak yeni bir KIMLIK ve oturum belirteci almak için oturum belirtecini ne kadar süreyle kullanabileceğinizi denetler. Kullanıcı kimlik doğrulamasından ve yeni bir oturum belirtecini aldıktan sonra, Kullanıcı, belirtilen süre boyunca oturum belirteci akışını kullanabilir. (Geçerli oturum belirteci iptal edilmediği ve süresi dolmamış olduğu sürece bu doğrudur.) Belirtilen süre geçtikten sonra, Kullanıcı yeni bir oturum belirteci almak için yeniden kimlik doğrulaması yapmak üzere zorlanır.

Maksimum yaşı azaltmak, kullanıcıların daha sık kimlik doğrulamasından geçmesini zorlar. Tek faktörlü kimlik doğrulamasının Multi-Factor Authentication 'dan daha az güvenli olduğu kabul edildiğinden, bu özelliği Multi-Factor Session Token Max Age özelliğinden eşit veya ondan küçük bir değere ayarlamanızı öneririz.

### <a name="multi-factor-session-token-max-age"></a>Multi-Factor Session belirtecinin en fazla yaşı
**Dize:** MaxAgeSessionMultiFactor

**Şunları etkiler:** Oturum belirteçleri (kalıcı ve kalıcı olmayan)

**Özet:** Bu ilke, bir kullanıcının birden çok faktör kullanarak başarıyla kimlik doğrulamasından geçtikten sonra yeni bir KIMLIK ve oturum belirteci almak için oturum belirtecini ne kadar süreyle kullanabileceğinizi denetler. Kullanıcı kimlik doğrulamasından ve yeni bir oturum belirtecini aldıktan sonra, Kullanıcı, belirtilen süre boyunca oturum belirteci akışını kullanabilir. (Geçerli oturum belirteci iptal edilmediği ve süresi dolmamış olduğu sürece bu doğrudur.) Belirtilen süre geçtikten sonra, Kullanıcı yeni bir oturum belirteci almak için yeniden kimlik doğrulaması yapmak üzere zorlanır.

Maksimum yaşı azaltmak, kullanıcıların daha sık kimlik doğrulamasından geçmesini zorlar. Tek faktörlü kimlik doğrulamasının Multi-Factor Authentication 'dan daha az güvenli olduğu kabul edildiğinden, bu özelliği tek faktörlü oturum belirteci en fazla Age özelliğinden eşit veya ondan daha büyük bir değere ayarlamanızı öneririz.

## <a name="example-token-lifetime-policies"></a>Örnek belirteç ömrü ilkeleri
Uygulamalar, hizmet sorumluları ve genel kuruluşunuz için belirteç yaşam sürelerini oluşturup yönetebilmeniz için Azure AD 'de birçok senaryo mümkündür. Bu bölümde, için yeni kurallar sungetirmenize yardımcı olabilecek birkaç ortak ilke senaryosundan ilerliyoruz:

* Belirteç Ömrü
* Belirteç en fazla etkin değil süresi
* En fazla belirteç yaşı

Örneklerde, şunları nasıl yapacağınızı öğrenebilirsiniz:

* Kuruluşun varsayılan ilkesini yönetme
* Web 'de oturum açma ilkesi oluşturma
* Web API 'sini çağıran yerel uygulama için bir ilke oluşturma
* Gelişmiş bir ilkeyi yönetme

### <a name="prerequisites"></a>Önkoşullar
Aşağıdaki örneklerde uygulamalar, hizmet sorumluları ve genel kurumunuzun ilkelerini oluşturur, güncelleştirir, bağlar ve silebilirsiniz. Azure AD 'de yeni başladıysanız, bu örneklere geçmeden önce [bir Azure AD kiracısı alma](quickstart-create-new-tenant.md) hakkında bilgi almanızı öneririz.  

Başlamak için aşağıdaki adımları uygulayın:

1. En son [Azure AD PowerShell modülü genel önizleme sürümünü](https://www.powershellgallery.com/packages/AzureADPreview)indirin.
2. Azure AD yönetici hesabınızda oturum açmak için `Connect` komutunu çalıştırın. Her yeni oturumu başlattığınızda bu komutu çalıştırın.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. Kuruluşunuzda oluşturulan tüm ilkeleri görmek için aşağıdaki komutu çalıştırın. Aşağıdaki senaryolarda işlemlerden en çok sonra bu komutu çalıştırın. Komutu çalıştırmak, ** ** ilkeleriniz için .

    ```powershell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Örnek: kuruluşun varsayılan ilkesini yönetme
Bu örnekte, kullanıcılarınızın kuruluşunuzun tamamında daha az sıklıkta oturum açmasını sağlayan bir ilke oluşturacaksınız. Bunu yapmak için, kuruluşunuz genelinde uygulanan tek faktörlü yenileme belirteçleri için bir belirteç ömür ilkesi oluşturun. İlke, kuruluşunuzdaki her uygulamaya ve zaten bir ilke kümesi olmayan her bir hizmet sorumlusuna uygulanır.

1. Belirteç ömür ilkesi oluşturun.

    1. Tek Etmenli yenileme belirtecini "Until-iptal" olarak ayarlayın. Erişim iptal edilene kadar belirtecin kullanım süreleri dolmaz. Aşağıdaki ilke tanımını oluşturun:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    2. İlkeyi oluşturmak için aşağıdaki komutu çalıştırın:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    3. Yeni ilkenizi görmek ve ilkenin **ObjectID**'sini almak için aşağıdaki komutu çalıştırın:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. İlkeyi güncelleştirin.

    Bu örnekte ayarladığınız ilk ilkenin, hizmetiniz için gerekli olan katı olmadığından emin olabilirsiniz. Tek faktörlü yenileme belirtecinizi iki gün içinde dolacak şekilde ayarlamak için aşağıdaki komutu çalıştırın:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Örnek: Web 'de oturum açmak için bir ilke oluşturun

Bu örnekte, kullanıcıların Web uygulamanızda daha sık kimlik doğrulaması yapmasını gerektiren bir ilke oluşturacaksınız. Bu ilke, erişim/KIMLIK belirteçlerinin ömrünü ve çok faktörlü bir oturum belirtecinin en fazla yaşını Web uygulamanızın hizmet sorumlusuna ayarlar.

1. Belirteç ömür ilkesi oluşturun.

    Bu ilke, Web 'de oturum açmak için erişim/KIMLIK belirteci ömrünü ve en fazla tek faktörlü oturum belirteci yaşını iki saate ayarlar.

    1. İlkeyi oluşturmak için şu komutu çalıştırın:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2. Yeni ilkenize bakmak ve ilke **ObjectID**'yi almak için aşağıdaki komutu çalıştırın:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. İlkeyi hizmet sorumlusuna atayın. Ayrıca hizmet sorumlunun **ObjectID** 'sini almanız gerekir.

    1. Tüm kuruluşunuzun hizmet sorumlularını veya tek bir hizmet sorumlusunu görmek için [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) cmdlet 'ini kullanın.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    2. Hizmet sorumlusu varsa, aşağıdaki komutu çalıştırın:
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Örnek: Web API 'sini çağıran yerel uygulama için bir ilke oluşturma
Bu örnekte, kullanıcılardan daha az sıklıkta kimlik doğrulaması yapmasını gerektiren bir ilke oluşturursunuz. İlke, Kullanıcı yeniden kimlik doğrulaması yapmadan önce kullanıcının devre dışı kalabileceği süreyi de uzar. İlke, Web API 'sine uygulanır. Yerel uygulama, Web API 'sini bir kaynak olarak istediğinde, bu ilke uygulanır.

1. Belirteç ömür ilkesi oluşturun.

    1. Bir Web API 'SI için katı bir ilke oluşturmak üzere aşağıdaki komutu çalıştırın:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    2. Yeni ilkenizi görmek için şu komutu çalıştırın:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. İlkeyi Web API 'nize atayın. Ayrıca uygulamanızın **ObjectID** 'sini almanız gerekir. Uygulamanızın **ObjectID**'sini bulmak için [Get-azureadapplication](/powershell/module/azuread/get-azureadapplication) cmdlet 'ini kullanın veya [Azure Portal](https://portal.azure.com/)kullanın.

    Uygulamanızın **ObjectID** 'sini alın ve ilkeyi atayın:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="example-manage-an-advanced-policy"></a>Örnek: gelişmiş bir ilkeyi yönetme
Bu örnekte, öncelik sisteminin nasıl çalıştığını öğrenmek için birkaç ilke oluşturursunuz. Ayrıca, çeşitli nesnelere uygulanan birden çok ilkeyi yönetmeyi öğreneceksiniz.

1. Belirteç ömür ilkesi oluşturun.

    1. Tek faktörlü yenileme belirteci ömrünü 30 güne ayarlayan bir kuruluş varsayılan ilkesi oluşturmak için şu komutu çalıştırın:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    2. Yeni ilkenizi görmek için şu komutu çalıştırın:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

2. İlkeyi bir hizmet sorumlusuna atayın.

    Şimdi, tüm kuruluşa uygulanan bir ilkenize sahipsiniz. Belirli bir hizmet sorumlusu için bu 30 günlük ilkeyi korumak isteyebilirsiniz, ancak kuruluş varsayılan ilkesini "Until-iptal" olarak üst sınıra göre değiştirebilirsiniz.

    1. Tüm kuruluşunuzun hizmet sorumlularını görmek için [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) cmdlet 'ini kullanırsınız.

    2. Hizmet sorumlusu varsa, aşağıdaki komutu çalıştırın:

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

3. `IsOrganizationDefault` bayrağını false olarak ayarlayın:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

4. Yeni bir kuruluş varsayılan ilkesi oluştur:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Artık özgün ilkeyi hizmet sorumlusuna bağladınız ve yeni ilke, kuruluşunuzun varsayılan ilkesi olarak ayarlanmıştır. Hizmet sorumlularına uygulanan ilkelerin, kuruluş varsayılan ilkelerine göre öncelikli olduğunu unutmamak önemlidir.

## <a name="cmdlet-reference"></a>Cmdlet başvurusu

### <a name="manage-policies"></a>İlkeleri yönetme

İlkeleri yönetmek için aşağıdaki cmdlet 'leri kullanabilirsiniz.

#### <a name="new-azureadpolicy"></a>New-AzureADPolicy

Yeni bir ilke oluşturur.

```powershell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| Parametreler | Açıklama | Örnek |
| --- | --- | --- |
| <code>&#8209;Definition</code> |Tüm ilkenin kurallarını içeren, strıngiingjson dizisi. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |İlke adının dizesi. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Doğru ise, ilkeyi kuruluşun varsayılan ilkesi olarak ayarlar. Yanlışsa, hiçbir şey yapmaz. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |İlke türü. Belirteç ömürleri için her zaman "TokenLifetimePolicy" kullanın. | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Isteğe bağlı] |İlke için alternatif bir KIMLIK ayarlar. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy
Tüm Azure AD ilkelerini veya belirli bir ilkeyi alır.

```powershell
Get-AzureADPolicy
```

| Parametreler | Açıklama | Örnek |
| --- | --- | --- |
| <code>&#8209;Id</code> [Isteğe bağlı] |İstediğiniz ilkenin **ObjectID (ID)** . |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Bir ilkeyle bağlantılı tüm uygulamaları ve hizmet sorumlularını alır.

```powershell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Parametreler | Açıklama | Örnek |
| --- | --- | --- |
| <code>&#8209;Id</code> |İstediğiniz ilkenin **ObjectID (ID)** . |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Set-AzureADPolicy
Var olan bir ilkeyi güncelleştirir.

```powershell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Parametreler | Açıklama | Örnek |
| --- | --- | --- |
| <code>&#8209;Id</code> |İstediğiniz ilkenin **ObjectID (ID)** . |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |İlke adının dizesi. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code> [Isteğe bağlı] |Tüm ilkenin kurallarını içeren, strıngiingjson dizisi. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code> [Isteğe bağlı] |Doğru ise, ilkeyi kuruluşun varsayılan ilkesi olarak ayarlar. Yanlışsa, hiçbir şey yapmaz. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> [Isteğe bağlı] |İlke türü. Belirteç ömürleri için her zaman "TokenLifetimePolicy" kullanın. |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code> [Isteğe bağlı] |İlke için alternatif bir KIMLIK ayarlar. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Remove-AzureADPolicy
Belirtilen ilkeyi siler.

```powershell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| Parametreler | Açıklama | Örnek |
| --- | --- | --- |
| <code>&#8209;Id</code> |İstediğiniz ilkenin **ObjectID (ID)** . | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Uygulama ilkeleri
Uygulama ilkeleri için aşağıdaki cmdlet 'leri kullanabilirsiniz.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADApplicationPolicy
Belirtilen ilkeyi bir uygulamaya bağlar.

```powershell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Parametreler | Açıklama | Örnek |
| --- | --- | --- |
| <code>&#8209;Id</code> |Uygulamanın **ObjectID (ID)** . | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |İlke **ObjectID** . | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-Azureadakural Ilkesi
Bir uygulamaya atanan ilkeyi alır.

```powershell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Parametreler | Açıklama | Örnek |
| --- | --- | --- |
| <code>&#8209;Id</code> |Uygulamanın **ObjectID (ID)** . | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remove-AzureADApplicationPolicy
Bir uygulamadan bir ilkeyi kaldırır.

```powershell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Parametreler | Açıklama | Örnek |
| --- | --- | --- |
| <code>&#8209;Id</code> |Uygulamanın **ObjectID (ID)** . | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |İlke **ObjectID** . | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Hizmet sorumlusu ilkeleri
Hizmet sorumlusu ilkeleri için aşağıdaki cmdlet 'leri kullanabilirsiniz.

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-Azureadservicesprincipalpolicy
Belirtilen ilkeyi bir hizmet sorumlusu ile bağlantılandırır.

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Parametreler | Açıklama | Örnek |
| --- | --- | --- |
| <code>&#8209;Id</code> |Uygulamanın **ObjectID (ID)** . | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |İlke **ObjectID** . | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy
Belirtilen hizmet sorumlusuna bağlı herhangi bir ilkeyi alır.

```powershell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Parametreler | Açıklama | Örnek |
| --- | --- | --- |
| <code>&#8209;Id</code> |Uygulamanın **ObjectID (ID)** . | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remove-AzureADServicePrincipalPolicy
İlkeyi belirtilen hizmet sorumlusu 'ndan kaldırır.

```powershell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Parametreler | Açıklama | Örnek |
| --- | --- | --- |
| <code>&#8209;Id</code> |Uygulamanın **ObjectID (ID)** . | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |İlke **ObjectID** . | `-PolicyId <ObjectId of Policy>` |
