---
title: Uygulama modeli | Mavisi
titleSuffix: Microsoft identity platform
description: Uygulamanızı Microsoft Identity platformu ile tümleştirilebilen şekilde kaydetme süreci hakkında bilgi edinin.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/28/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 86543b961698e736b2211553b0dca367b28158ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98795665"
---
# <a name="application-model"></a>Uygulama modeli

Uygulamalar, kullanıcıların kendilerine oturum açmasını veya bir kimlik sağlayıcısı için oturum açma yetkisini devredebilir. Bu makalede, bir uygulamayı Microsoft Identity platformu ile kaydetmek için gereken adımlar ele alınmaktadır.

## <a name="register-an-application"></a>Bir uygulamayı kaydetme

Bir kimlik sağlayıcısının bir kullanıcının belirli bir uygulamaya erişimi olduğunu bilmesini sağlamak için, hem Kullanıcı hem de uygulamanın kimlik sağlayıcısına kayıtlı olması gerekir. Uygulamanızı Azure Active Directory (Azure AD) ile kaydettiğinizde, uygulamanız için Microsoft Identity platformu ile tümleşmesini sağlayan bir kimlik yapılandırması sağladığınızı unutmayın. Uygulamayı kaydetmek şunları da sağlar:

* Oturum açma iletişim kutusunda uygulamanızın markasını özelleştirin. Bu marka, bir kullanıcının uygulamanız ile ilgili olduğu ilk deneyimle oturum açması açısından önemlidir.
* Kullanıcıların yalnızca kuruluşunuza ait olmaları durumunda oturum açmalarına izin vermek istediğinize karar verin. Bu mimari, tek kiracılı bir uygulama olarak bilinir. Ya da kullanıcıların, çok kiracılı bir uygulama olarak bilinen herhangi bir iş veya okul hesabını kullanarak oturum açmalarına izin verebilirsiniz. Ayrıca, bireysel Microsoft hesaplarına veya bir sosyal hesaba LinkedIn, Google vb. izin verebilirsiniz.
* Kapsam izinleri iste. Örneğin, oturum açmış kullanıcının profilini okuma izni veren "User. Read" kapsamını isteyebilirsiniz.
* Web API 'nize erişimi tanımlayan kapsamları tanımlayın. Genellikle, bir uygulama API 'nize erişmek istediğinde tanımladığınız kapsamlar için izin istemesi gerekecektir.
* Uygulamanın kimliğini karşılayan Microsoft Identity platformu ile gizli anahtar paylaşma. Gizli anahtar kullanılması, uygulamanın gizli bir istemci uygulaması olduğu durumda ilgilidir. Gizli bir istemci uygulaması, kimlik bilgilerini güvenli bir şekilde tutan bir uygulamadır. Kimlik bilgilerini depolamak için güvenilir bir arka uç sunucusu gerekir.

Uygulama kaydedildikten sonra, belirteç istediğinde Microsoft Identity platformu ile paylaştığı benzersiz bir tanımlayıcı verilir. Uygulama [gizli bir istemci uygulaması](developer-glossary.md#client-application)ise, sertifikaların veya gizli anahtarların kullanıldığına bağlı olarak gizli anahtar veya ortak anahtarı da paylaşır.

Microsoft Identity platform iki ana işlevi yerine getiren bir modeli kullanarak uygulamaları temsil eder:

* Uygulamayı desteklediği kimlik doğrulama protokollerine göre belirler.
* Kimlik doğrulaması için gereken tüm tanımlayıcıları, URL 'Leri, gizli dizileri ve ilgili bilgileri sağlayın.

Microsoft Identity platformu:

* Çalışma zamanında kimlik doğrulamasını desteklemek için gereken tüm verileri tutar.
* Bir uygulamanın erişmesi gerekebilecek kaynakları ve belirli bir isteğin yerine getirilmesi gereken koşulları belirlemek için tüm verileri tutar.
* Uygulama geliştiricisinin kiracısında ve diğer Azure AD kiracılarında uygulama sağlamayı uygulamak için altyapı sağlar.
* Belirteç istek süresi boyunca Kullanıcı onayını işler ve kiracılar genelinde uygulamaların dinamik sağlamasını kolaylaştırır.

*Onay* , bir istemci uygulamanın, kaynak sahibi adına belirli izinler altında korumalı kaynaklara erişmesi için bir kaynak sahibi verme yetkilendirmesi işlemidir. Microsoft Identity platformu şunları sunar:

* Kullanıcılara ve yöneticilere, uygulamanın adına kaynaklara erişme iznini dinamik olarak verme veya reddetme izni verme.
* Yöneticiler, hangi uygulamaların çalışmasına izin verileceğini ve hangi kullanıcıların belirli uygulamaları ve dizin kaynaklarına nasıl erişildiğini son olarak karar verir.

## <a name="multi-tenant-apps"></a>Çok kiracılı uygulamalar

Microsoft Identity platformunda bir [uygulama nesnesi](developer-glossary.md#application-object) bir uygulamayı açıklar. Dağıtım zamanında, Microsoft Identity platform bir dizin veya kiracı içindeki bir uygulamanın somut örneğini temsil eden bir [hizmet sorumlusu](developer-glossary.md#service-principal-object)oluşturmak için uygulama nesnesini bir şema olarak kullanır. Hizmet sorumlusu, uygulamanın belirli bir hedef dizinde gerçekten ne yapabileceğini, bunu kullanabilecek kaynakları, hangi kaynakların erişebileceğini ve bu şekilde olduğunu tanımlar. Microsoft Identity platformu, bir uygulama nesnesinden [onay](developer-glossary.md#consent)aracılığıyla bir hizmet sorumlusu oluşturur.

Aşağıdaki diyagramda, izin tarafından yönetilen basitleştirilmiş bir Microsoft Identity platformu sağlama akışı gösterilmektedir. İki kiracı gösterir: *A* ve *B*.

* *Kiracı* , uygulamanın sahibi.
* *B kiracısı* , bir hizmet sorumlusu aracılığıyla uygulamayı örnekleniyor.

![Onayı tarafından yönetilen Basitleştirilmiş sağlama akışını gösteren diyagram.](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

Bu sağlama akışında:

1. B kiracısından bir kullanıcı uygulamayla oturum açmaya çalışır. Yetkilendirme uç noktası, uygulama için bir belirteç ister.
1. Kimlik doğrulaması için Kullanıcı kimlik bilgileri alındı ve doğrulanır.
1. Kullanıcıdan, B kiracısına erişim kazanmak için uygulamanın onayını sağlaması istenir.
1. Microsoft Identity platformu, B kiracısında bir hizmet sorumlusu oluşturmak için bir şema olarak kiracı 'daki uygulama nesnesini kullanır.
1. Kullanıcı istenen belirteci alır.

Daha fazla kiracı için bu işlemi yineleyebilirsiniz. Kiracı A, uygulama için şemayı saklar (uygulama nesnesi). Uygulamanın izin verdiği tüm diğer kiracıların kullanıcıları ve yöneticileri, uygulamanın her Kiracıdaki ilgili hizmet sorumlusu nesnesi aracılığıyla ne yapmasına izin verileceğini kontrol edin. Daha fazla bilgi için bkz. [Microsoft Identity platformunda uygulama ve hizmet sorumlusu nesneleri](app-objects-and-service-principals.md).

## <a name="next-steps"></a>Sonraki adımlar

Microsoft Identity platformunda kimlik doğrulama ve yetkilendirme hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* Kimlik doğrulama ve yetkilendirme ile ilgili temel kavramlar hakkında bilgi edinmek için bkz. [kimlik doğrulaması ve yetkilendirme](authentication-vs-authorization.md).
* Kimlik doğrulama ve yetkilendirme için erişim belirteçlerinin, yenileme belirteçlerinin ve KIMLIK belirteçlerinin nasıl kullanıldığını öğrenmek için bkz. [güvenlik belirteçleri](security-tokens.md).
* Web, masaüstü ve mobil uygulamaların oturum açma akışı hakkında bilgi edinmek için bkz. [uygulama oturum açma akışı](app-sign-in-flow.md).

Uygulama modeli hakkında daha fazla bilgi için aşağıdaki makalelere bakın:

* Microsoft Identity platformunda uygulama nesneleri ve hizmet sorumluları hakkında daha fazla bilgi için bkz. [Azure AD 'ye uygulamaların nasıl ve neden eklendiği](active-directory-how-applications-are-added.md).
* Tek kiracılı uygulamalar ve çok kiracılı uygulamalar hakkında daha fazla bilgi için bkz. [Azure Active Directory Içindeki kiracı](single-and-multi-tenant-apps.md).
* Azure AD 'nin, kuruluşların kullanıcıların oturum açmasını sağlamak için Azure Active Directory B2C sağladığı daha fazla bilgi için, bkz. [Azure Active Directory B2C belgeleri](../../active-directory-b2c/index.yml).