---
title: Microsoft Identity platformunda OAuth 2,0 ve OpenID Connect protokolleri | Mavisi
titleSuffix: Microsoft identity platform
description: Microsoft Identity platformu tarafından desteklenen OAuth 2,0 ve OpenID Connect protokollerine yönelik bir kılavuz.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/21/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 765c363542b07deac44d47b94731e1109fcba045
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755752"
---
# <a name="oauth-20-and-openid-connect-protocols-on-the-microsoft-identity-platform"></a>Microsoft Identity platformunda OAuth 2,0 ve OpenID Connect protokolleri

Hizmet olarak kimlik için Microsoft Identity platform uç noktası, sırasıyla OpenID Connect (OıDC) ve OAuth 2,0 endüstri standardı protokolleriyle kimlik doğrulama ve yetkilendirme uygular. Hizmet standartlarla uyumlu olsa da, bu protokollerin iki uygulaması arasında hafif farklar olabilir. Buradaki bilgiler, HTTP isteklerini doğrudan gönderip işleyerek veya [Açık kaynaklı kitaplıklarımızın](reference-v2-libraries.md)birini kullanmak yerine bir üçüncü taraf açık kaynak kitaplığı kullanarak kodunuzu yazmayı tercih ediyorsanız yararlı olacaktır.

## <a name="the-basics"></a>Temel bilgiler

Neredeyse tüm OAuth 2,0 ve OpenID Connect akışlarında, Exchange 'e dahil edilen dört taraf vardır:

![OAuth 2,0 rollerinin gösterildiği diyagram](./media/active-directory-v2-flows/protocols-roles.svg)

* **Yetkilendirme sunucusu** Microsoft Identity platformudur ve kullanıcının kimliğini sağlamaktan, kaynaklara erişim verme ve iptal etme ve belirteç verme konusunda sorumludur. Kimlik sağlayıcısı olarak da bilinen yetkilendirme sunucusu, kullanıcının bilgileri, erişimleri ve bir akıştaki taraflar arasındaki güven ilişkileri ile yapılan her şeyi güvenli bir şekilde işler.
* **Kaynak sahibi** genellikle Son Kullanıcı olur. Bu, verilerin sahibi olan ve istemcilerin bu verilere veya kaynağa erişmesine izin veren bir güce sahip olan taraftır.
* **OAuth istemcisi** , uygulama kimliğiyle tanımlanan uygulamanız. OAuth istemcisi genellikle son kullanıcının etkileşime girdiği tarafdır ve yetkilendirme sunucusundan belirteçler ister. İstemciye kaynak sahibi tarafından kaynağa erişim izni verilmesi gerekir.
* Kaynak **sunucu** , kaynağın veya verilerin bulunduğu yerdir. OAuth Istemcisini güvenli şekilde doğrulamak ve yetkilendirmek için yetkilendirme sunucusuna güvenir ve bir kaynağa erişimin verilmesini sağlamak için taşıyıcı erişim belirteçlerini kullanır.

## <a name="app-registration"></a>Uygulama kaydı

Hem kişisel hem de iş veya okul hesaplarını kabul etmek isteyen her uygulama, OAuth 2,0 veya OpenID Connect kullanarak bu kullanıcıları imzalayabilmesi için [Azure portal](https://aka.ms/appregistrations) **uygulama kayıtları** deneyiminden kaydedilmelidir. Uygulama kayıt işlemi, uygulamanıza birkaç değer toplayacak ve atayacaktır:

* Uygulamanızı benzersiz bir şekilde tanımlayan **uygulama kimliği**
* Yanıtları uygulamanıza geri yönlendirmek için kullanılabilen bir **yeniden yönlendirme URI 'si** (isteğe bağlı)
* Diğer senaryoya özgü birkaç değer.

Daha ayrıntılı bilgi için [uygulama kaydetmeyi](quickstart-register-app.md) öğrenin.

## <a name="endpoints"></a>Uç Noktalar

Kaydolduktan sonra, uygulama uç noktaya istek göndererek Microsoft Identity platformu ile iletişim kurar:

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

Burada `{tenant}` dört farklı değerden birini alabilir:

| Değer | Açıklama |
| --- | --- |
| `common` | Azure AD 'den hem kişisel Microsoft hesapları hem de iş/okul hesabı olan kullanıcıların uygulamada oturum açmasını sağlar. |
| `organizations` | Yalnızca Azure AD 'den iş/okul hesapları olan kullanıcıların uygulamada oturum açmasını sağlar. |
| `consumers` | Yalnızca kişisel Microsoft hesabı (MSA) olan kullanıcıların uygulamada oturum açmasına izin verir. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` veya `contoso.onmicrosoft.com` | Yalnızca belirli bir Azure AD kiracısından iş/okul hesapları olan kullanıcıların uygulamada oturum açmasını sağlar. Azure AD kiracının kolay etki alanı adı ya da kiracının GUID tanımlayıcısı kullanılabilir. |

Bu uç noktalarla nasıl etkileşim kuracağınızı öğrenmek için, [protokoller](#protocols) bölümünde belirli bir uygulama türü seçin ve daha fazla bilgi için bağlantıları izleyin.

> [!TIP]
> Azure AD 'de kayıtlı tüm uygulamalar, kişisel hesaplara kaydolmasa bile Microsoft Identity platformunu kullanabilir.  Bu şekilde, mevcut uygulamaları uygulamanızı yeniden oluşturmadan Microsoft Identity platformu ve [msal](reference-v2-libraries.md) geçirebilirsiniz.

## <a name="tokens"></a>Belirteçler

OAuth 2,0 ve OpenID Connect, genellikle [Jwts (JSON Web belirteçleri)](https://tools.ietf.org/html/rfc7519)olarak temsil edilen **taşıyıcı belirteçlerinin** yoğun bir şekilde kullanılmasını kolaylaştırır. Taşıyıcı belirteç, korunan bir kaynağa "taşıyıcı" erişimini sağlayan hafif bir güvenlik belirtecidir. Bu anlamda, "taşıyıcı" belirtecin bir kopyasını alan herkes olur. Bir tarafın, taşıyıcı belirtecini almak için Microsoft Identity platformunda kimlik doğrulaması yapması gerekir, ancak gerekli adımlar iletim ve depolama alanındaki belirtecin güvenliğini sağlamak için alınmadığından, istenmeyen bir taraf tarafından yakalanabilir ve kullanılabilir. Bazı güvenlik belirteçlerinin, yetkisiz tarafların onları kullanmasını önlemek için yerleşik bir mekanizması vardır ancak, taşıyıcı belirteçleri bu mekanizmaya sahip değildir ve Aktarım Katmanı Güvenliği (HTTPS) gibi güvenli bir kanalda aktarılmalıdır. Bir taşıyıcı belirteci açık bir şekilde iletilebilise, kötü amaçlı bir taraf, belirteci almak ve korumalı bir kaynağa yetkisiz erişim için kullanmak üzere bir ortadaki adam saldırısı kullanabilir. Aynı güvenlik ilkeleri, daha sonra kullanılmak üzere taşıyıcı belirteçleri depolarken veya önbelleğe alırken geçerlidir. Her zaman uygulamanızın taşıyıcı belirteçlerini güvenli bir şekilde ilettiğinden ve depoladığından emin olun. Taşıyıcı belirteçleriyle ilgili daha fazla bilgi için bkz. [RFC 6750 Bölüm 5](https://tools.ietf.org/html/rfc6750).

OAuth 2,0/OıDC içinde birincil olarak 3 tür belirteç vardır:

* [Erişim belirteçleri](access-tokens.md) -bir kaynak sunucusunun istemciden aldığı, istemcinin verildiği izinleri içeren belirteçler.  
* [Kimlik belirteçleri](id-tokens.md) -bir istemcinin, bir kullanıcının oturum açması ve bunlarla ilgili temel bilgileri almak için kullandığı yetkilendirme sunucusundan aldığı belirteçler.
* Belirteçleri Yenile-istemci tarafından zamanla yeni erişim ve KIMLIK belirteçleri almak için kullanılır.  Bunlar donuk dizelerdir ve yalnızca yetkilendirme sunucusu tarafından anlaşılır.

## <a name="protocols"></a>Protokoller

Bazı örnek istekleri görmek için hazırsanız, aşağıdaki protokol belgelerinden birini kullanmaya başlayın. Her biri belirli bir kimlik doğrulama senaryosuna karşılık gelir. Sizin için doğru akış olduğunu belirlemek için yardıma ihtiyacınız varsa, [Microsoft Identity platform ile oluşturabileceğiniz uygulama türlerine](v2-app-types.md)göz atın.

* [OAuth 2,0 ile mobil, yerel ve Web uygulaması oluşturma](v2-oauth2-auth-code-flow.md)
* [OpenID Connect ile oturum açan kullanıcılar](v2-protocols-oidc.md)
* [OAuth 2,0 istemci kimlik bilgileri akışıyla Daemon 'ları veya sunucu tarafı süreçler oluşturun](v2-oauth2-client-creds-grant-flow.md)
* [Bir Web API 'sinde, OAuth 2,0 adına sahip akışa sahip belirteçleri edinme](v2-oauth2-on-behalf-of-flow.md)
* [OAuth 2,0 örtük Flow ile tek sayfalı uygulamalar oluşturun](v2-oauth2-implicit-grant-flow.md)
