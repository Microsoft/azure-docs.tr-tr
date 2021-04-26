---
title: Azure Active Directory tek sayfalı uygulamalar
description: Bu uygulama türü için tek sayfalı uygulamaların (maça 'lar) ne olduğunu ve protokol akışı, kayıt ve belirteç süre sonu hakkındaki temel bilgileri açıklar.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: adf3c5b5cd40a9ea3f07ba9c92cfc4544ca60f1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "80154755"
---
# <a name="single-page-applications"></a>Tek sayfalı uygulamalar

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Tek sayfalı uygulamalar (maça 'Lar) genellikle tarayıcıda çalışan bir JavaScript sunum katmanı (ön uç) olarak yapılandırılmıştır ve bir sunucuda çalışan ve uygulamanın iş mantığını uygulayan bir Web API arka ucu olarak yapılandırılır. Örtük yetkilendirme izni hakkında daha fazla bilgi edinmek ve Uygulama senaryonuz için doğru olup olmadığına karar vermenize yardımcı olmak için, [Azure Active Directory OAuth2 örtük izin akışını anlama](v1-oauth2-implicit-grant-flow.md)bölümüne bakın.

Bu senaryoda, Kullanıcı oturum açtığında, JavaScript ön ucu [JavaScript (ADAL.JS) için Active Directory Authentication Library](https://github.com/AzureAD/azure-activedirectory-library-for-js) ve örtük yetkilendirme, Azure AD 'den bir kimlik belirteci (id_token) almaya izin verir. Belirteç önbelleğe alınır ve istemci, OWıN ara yazılımı kullanılarak güvenliği sağlanmış olan Web API 'SI arka ucuna çağrı yaparken, bu isteği taşıyıcı belirteç olarak isteğe iliştirir.

## <a name="diagram"></a>Diyagram

![Tek sayfalı uygulama diyagramı](./media/authentication-scenarios/single-page-app.png)

## <a name="protocol-flow"></a>Protokol akışı

1. Kullanıcı Web uygulamasına gider.
1. Uygulama, tarayıcıya JavaScript ön ucu (sunu katmanı) döndürür.
1. Kullanıcı oturum açma bağlantısını, örneğin bir oturum açma bağlantısına tıklayarak başlatır. Tarayıcı, KIMLIK belirteci istemek için Azure AD yetkilendirme uç noktasına bir GET gönderir. Bu istek, sorgu parametrelerinde uygulama KIMLIĞI ve yanıt URL 'sini içerir.
1. Azure AD, yanıt URL 'sini Azure portal yapılandırılan kayıtlı yanıt URL 'sine göre doğrular.
1. Kullanıcı oturum açma sayfasında oturum açar.
1. Kimlik doğrulaması başarılı olursa, Azure AD bir KIMLIK belirteci oluşturur ve bunu uygulamanın yanıt URL 'SI olarak bir URL parçası (#) olarak döndürür. Bir üretim uygulaması için, bu yanıt URL 'SI HTTPS olmalıdır. Döndürülen belirteç, Kullanıcı ve belirteci doğrulamak için uygulamanın gerektirdiği Azure AD taleplerini içerir.
1. Tarayıcıda çalışan JavaScript istemci kodu, uygulamanın Web API arka ucuna yapılan çağrıların güvenliğini sağlamak için kullanılan yanıtın belirtecini ayıklar.
1. Tarayıcı, uygulamanın Web API arka ucu 'nı yetkilendirme üstbilgisindeki KIMLIK belirteciyle çağırır. Azure AD kimlik doğrulama hizmeti, kaynak istemci KIMLIĞIYLE aynıysa, bir taşıyıcı belirteci olarak kullanılabilecek bir KIMLIK belirteci yayınlar (Bu durumda, Web API 'sinin uygulamanın arka ucu olduğu için bu doğrudur).

## <a name="code-samples"></a>Kod örnekleri

[Tek sayfalı uygulama senaryoları için kod örneklerine](sample-v1-code.md#single-page-applications)bakın. Sıklıkla yeni örnekler eklendiğinden emin olun.

## <a name="app-registration"></a>Uygulama kaydı

* Tek kiracı-yalnızca kuruluşunuz için bir uygulama oluşturuyorsanız, Azure portal kullanılarak şirketinizin dizinine kayıtlı olması gerekir.
* Çok kiracılı-kuruluşunuzun dışındaki kullanıcılar tarafından kullanılabilecek bir uygulama oluşturuyorsanız, şirketinizin dizinine kayıtlı olması gerekir, ancak aynı zamanda uygulamanın kullanacağı her bir kuruluşun dizinine kayıtlı olması gerekir. Uygulamanızı dizininde kullanılabilir hale getirmek için müşterilerinizin uygulamanıza izin vermesini sağlayan bir kaydolma işlemi ekleyebilirsiniz. Uygulamanıza kaydolduklarında, uygulamaya gereken izinleri gösteren bir iletişim kutusu görüntülenir ve ardından onay seçeneği sunulur. Gerekli izinlere bağlı olarak, diğer kuruluştaki bir yöneticinin onay vermesi gerekebilir. Kullanıcı veya yönetici, uygulama kendi dizinine kaydedilir.

Uygulama kaydedildikten sonra, OAuth 2,0 örtük izin protokolünü kullanacak şekilde yapılandırılması gerekir. Bu protokol, uygulamalar için varsayılan olarak devre dışıdır. Uygulamanız için OAuth2 örtük verme protokolünü etkinleştirmek üzere Azure portal uygulama bildirimini düzenleyin ve "oauth2AllowImplicitFlow" değerini true olarak ayarlayın. Daha fazla bilgi için bkz. [uygulama bildirimi](../develop/reference-app-manifest.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

## <a name="token-expiration"></a>Belirteç süre sonu

ADAL.js kullanmak şunları yapmanıza yardımcı olur:

* Süre dolma belirtecini yenileme
* Web API kaynağını çağırmak için erişim belirteci isteme

Başarılı bir kimlik doğrulamasından sonra Azure AD, oturum oluşturmak için kullanıcının tarayıcısına bir tanımlama bilgisi yazar. Oturumun Kullanıcı ile Azure AD arasında (Kullanıcı ile Web uygulaması arasında değil) bulunduğunu not edin. Belirtecin süresi dolarsa, ADAL.js bu oturumu kullanarak sessizce başka bir belirteç elde eder. ADAL.js, OAuth örtük izin protokolünü kullanarak isteği göndermek ve almak için gizli bir iFrame kullanır. ADAL.js, diğer Web API kaynakları için erişim belirteçlerini sessizce almak üzere bu aynı mekanizmayı da kullanabilir, çünkü bu kaynaklar çıkış noktaları arası kaynak paylaşımı 'nı (CORS) destekler, kullanıcının dizinine kaydedilir ve oturum açma sırasında Kullanıcı tarafından gerekli izin verilir.

## <a name="next-steps"></a>Sonraki adımlar

* Diğer [uygulama türleri ve senaryolar](app-types.md) hakkında daha fazla bilgi edinin
* Azure AD [kimlik doğrulaması temelleri](v1-authentication-scenarios.md) hakkında bilgi edinin
