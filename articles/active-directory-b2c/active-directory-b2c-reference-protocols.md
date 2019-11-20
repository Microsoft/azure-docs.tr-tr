---
title: Azure Active Directory B2C'de kimlik doğrulama protokolleri | Microsoft Docs
description: Nasıl doğrudan Azure Active Directory B2C tarafından desteklenen protokoller kullanarak uygulamalar oluşturun.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 083fd6b6027c78e956c133d7801a03fd9042e88d
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835745"
---
# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C: Kimlik doğrulama protokolleri
Azure Active Directory B2C (Azure AD B2C) sağlayan kimlik uygulamalarınız için hizmet olarak iki sektör standardı protokolleri destekleyerek: Openıd Connect ve OAuth 2.0. Hizmet standartlarıyla uyumlu olduğu halde bu protokolleri, iki belirtilmesinden küçük farklılıklar olabilir.

Bu kılavuzdaki bilgilerini doğrudan göndererek ve HTTP isteklerini işlemek yerine bir açık kaynak kitaplığı kullanarak kod yazma yararlıdır. Her özel Protokolü ayrıntılara girmeden önce bu sayfayı okumanızı öneririz. Ancak zaten Azure AD B2C ile biliyorsanız, doğrudan gidebilirsiniz [Protokolü Başvuru Kılavuzu](#protocols).

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Temel bilgiler
Azure AD B2C'yi kullanan her uygulamanın B2C dizininizde kaydedilmesi gerekiyor [Azure portalında](https://portal.azure.com). Uygulama kayıt işlemi birkaç değer toplayarak uygulamanıza atar:

* Uygulamanızı benzersiz olarak tanımlayan bir **Uygulama Kimliği**.
* A **yeniden yönlendirme URI'si** veya **tanımlayıcısı paketini** yanıtları uygulamanıza geri yönlendirmek için kullanılabilir.
* Diğer birkaç senaryoya özel değerler. Daha fazla bilgi için bilgi [uygulamanızı kaydetmek nasıl](active-directory-b2c-app-registration.md).

Uygulamanızı kaydettikten sonra Azure Active Directory (Azure AD) ile uç noktaya istek göndererek iletişim kurar:

```
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token
```

Neredeyse tüm OAuth ve Openıd Connect akışı içinde dört taraflar Exchange'de karmaşıktır:

![Dört OAuth 2.0 rolleri gösteren diyagram](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

* **Yetkilendirme sunucusu** Azure AD'ye uç noktadır. Güvenli bir şekilde kullanıcı bilgilerini ve erişim ile ilgili herhangi bir şey işler. Ayrıca, bir akışta taraflar arasındaki güven ilişkilerinin işler. Bu, kullanıcının kimliğini doğrulayan, verme ve kaynaklara erişimi iptal etme ve belirteç sorumludur. Kimlik sağlayıcısı olarak da bilinen olduğu.

* **Kaynak sahibi** genellikle son kullanıcısıdır. Verilerin sahibi taraftır ve üçüncü tarafların, veri veya kaynağa erişimi izni gücüne sahiptir.

* **OAuth istemcisi** uygulamanız. Bunun uygulama kimliği tarafından tanımlanır Genellikle, son kullanıcılar ile etkileşimde bulunan taraf olur. Ayrıca belirteçleri yetkilendirme sunucusundan ister. Kaynak sahibi istemci kaynağa erişim izni vermeniz gerekir.

* **Kaynak sunucusu** kaynağı veya veri bulunduğu olduğu. Bu, güvenli bir şekilde kimlik doğrulaması ve OAuth istemci yetki vermek için yetkilendirme sunucusu güvenir. Ayrıca bir kaynağa erişim izni sağlamak için taşıyıcı erişim belirteçlerini kullanır.

## <a name="policies-and-user-flows"></a>İlkeleri ve kullanıcı akışları
Tartışmaya, Azure AD B2C ilkeleri hizmetinin en önemli özelliklerdir. Azure AD B2C ilkeleri sunarak standart OAuth 2.0 ve Openıd Connect protokollerini genişletir. Bunlar, daha fazlasını Basit kimlik doğrulaması ve yetkilendirmesi gerçekleştirmek için Azure AD B2C izin verir.

Yardımcı olması için en yaygın kimlik görevleri ayarlayın, adlı önceden tanımlanmış, yapılandırılabilir ilkeleri Azure AD B2C portal içerir **kullanıcı akışları**. Kullanıcı akışları tamamen kaydolma, oturum açma dahil olmak üzere, tüketici kimlik deneyimi açıklamak ve profil düzenleme. Kullanıcı akışları, bir yönetici kullanıcı Arabiriminde tanımlanabilir. HTTP kimlik doğrulaması isteklerini bir özel sorgu parametresi kullanarak gerçekleştirilebilir.

Bunları anlamak için zamanınız olması gerekir böylece ilkeleri ve kullanıcı akışları OAuth 2.0 ve Openıd Connect, standart özellikleri değildir. Daha fazla bilgi için [Azure AD B2C kullanıcı akışı Başvuru Kılavuzu](active-directory-b2c-reference-policies.md).

## <a name="tokens"></a>Belirteçler
OAuth 2.0 ve Openıd Connect Azure AD B2C uygulamasını taşıyıcı belirteçleri, JSON web belirteçleri (Jwt'ler) gösterilen taşıyıcı belirteçlerini dahil olmak üzere kapsamlı kullanımını sağlar. Taşıyıcı belirteç korumalı bir kaynağın "bearer" erişim veren bir basit güvenlik belirtecidir.

Taşıyıcı belirteç sunabilir herhangi bir tarafa ' dir. Taşıyıcı belirteç almak için önce azure AD ilk bir taraf kimlik doğrulaması gerekir. Ancak gerekli adımları iletilmesini ve depolanmasını belirteci güvenliğini sağlamak için alınır değil, kesildi ve olması istenmeyen bir şahıs tarafından kullanılır.

Bazı güvenlik belirteçleri yetkisiz taraflar, onları kullanmasına engel olmak yerleşik mekanizmalar vardır, ancak taşıyıcı belirteçleri Bu mekanizma yoktur. Bunlar bir Aktarım Katmanı Güvenliği (HTTPS) gibi güvenli bir kanal taşınan gerekir.

Taşıyıcı belirteç dışında güvenli bir kanal iletilirse, kötü amaçlı bir taraf belirteç almak ve korumalı kaynağa yetkisiz erişim elde etmek için kullanmak için bir adam-de-ortadaki adam saldırısı kullanabilirsiniz. Depolanan ya da daha sonra kullanmak üzere önbelleğe taşıyıcı belirteçleri, aynı güvenlik ilkeleri uygulayın. Uygulamanızı iletir ve güvenli bir şekilde taşıyıcı belirteçleri depolar her zaman emin olmalısınız.

Ek taşıyıcı belirteci güvenlik konuları için bkz. [RFC 6750 bölüm 5](https://tools.ietf.org/html/rfc6750).

Farklı Azure AD B2C'de kullanılan belirteç türleri hakkında daha fazla bilgi edinilebilir [Azure AD belirteç başvurusu](active-directory-b2c-reference-tokens.md).

## <a name="protocols"></a>Protokoller
Bazı örnek isteklerini gözden geçirme hazır olduğunuzda, aşağıdaki öğreticilerden birine ile başlayabilirsiniz. Her bir belirli kimlik doğrulama senaryosu için karşılık gelir. Hangi akış size uygun olduğunu belirleyen yardıma ihtiyacınız olursa kullanıma [uygulamaları Azure AD B2C'yi kullanarak oluşturabilirsiniz türlerini](active-directory-b2c-apps.md).

* [OAuth 2.0 kullanarak mobil ve yerel uygulamalar oluşturun](active-directory-b2c-reference-oauth-code.md)
* [Openıd Connect kullanarak Web uygulamaları oluşturun](active-directory-b2c-reference-oidc.md)
* [OAuth 2.0 örtük akışını kullanarak tek sayfalı uygulamalar oluşturun](active-directory-b2c-reference-spa.md)

