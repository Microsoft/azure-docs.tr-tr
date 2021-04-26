---
title: Azure AD B2C yeni Uygulama kayıtları deneyimi
description: Azure AD B2C yeni uygulama kayıt deneyimine giriş.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/25/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 430da23986fc36a0e94c049512ef716aff1fed5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98660259"
---
# <a name="the-new-app-registrations-experience-for-azure-active-directory-b2c"></a>Azure Active Directory B2C için yeni Uygulama kayıtları deneyimi

Azure Active Directory B2C (Azure AD B2C) için yeni **[uygulama kayıtları](https://aka.ms/b2cappregistrations)** deneyimi artık genel kullanıma sunulmuştur. "Eski deneyim" olarak adlandırılan Azure AD B2C **uygulamaları kaydetme** hakkında daha fazla bilginiz varsa, bu kılavuz yeni deneyimi kullanmaya başlamanızı sağlar.

## <a name="overview"></a>Genel Bakış
Daha önce, Azure AD B2C tüketiciye yönelik uygulamalarınızı eski deneyimi kullanarak uygulamalarınızın geri kalanından ayrı olarak yönetmeniz gerekiyordu. Bu, Azure 'daki farklı yerlerde farklı uygulama oluşturma deneyimleri sunmaktır.

Yeni deneyim tüm Azure AD B2C uygulama kayıtlarını ve Azure AD uygulama kayıtlarını tek bir yerde gösterir ve bunları yönetmek için tutarlı bir yol sağlar. Bir uygulamayı kaynak yönetimine yönelik Microsoft Graph izinlerle yönetmek için müşteriye yönelik bir uygulama oluşturma işleminden, tek yapmanız gereken bir yolu öğrenirsiniz.

**Azure AD B2C** veya Azure Portal **Azure Active Directory** hizmetlerinden Azure AD B2C kiracısındaki **uygulama kayıtları** giderek yeni deneyime ulaşabilirsiniz.

Azure AD B2C Uygulama kayıtları deneyimi, herhangi bir Azure AD kiracısı için genel [uygulama kayıt deneyimini](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) temel alır, ancak Azure AD B2C kiracılar için tasarlanmıştır.

## <a name="whats-not-changing"></a>Ne değiştirmiyor?
- Uygulamalarınız ve ilgili yapılandırmalar yeni deneyimde olduğu gibi bulunabilir. Uygulamaları yeniden kaydetmeniz gerekmez ve uygulamalarınızın kullanıcılarının yeniden oturum açması gerekmez.

> [!NOTE]
> Daha önce oluşturduğunuz tüm uygulamalarınızı görüntülemek için **uygulama kayıtları** dikey penceresine gidin ve **tüm uygulamalar** sekmesini seçin. Bu, eski deneyimde oluşturulan uygulamaları, yeni deneyimi ve Azure AD hizmetinde oluşturulanlar görüntülenir.

## <a name="key-new-features"></a>Yeni anahtar özellikleri

-   **Birleştirilmiş bir uygulama listesi** , Azure AD B2C ve Azure AD ile kimlik doğrulaması yapan tüm uygulamalarınızı tek bir uygun yerde gösterir. Ayrıca, **oluşturma** tarihi, **Sertifikalar & gizli** dizi durumu, arama çubuğu ve çok daha fazlası dahIl olmak üzere Azure AD uygulamaları için zaten mevcut olan özelliklerden yararlanabilirsiniz.

-   **Birleştirilmiş uygulama kaydı** , bir uygulamayı, müşteriye yönelik bir uygulama veya Microsoft Graph erişmek için bir uygulama olmasına bakılmaksızın hızlı bir şekilde kaydetmenizi sağlar.

- **Uç noktalar** bölmesi, OpenID Connect YAPıLANDıRMASı, SAML meta verileri, Microsoft Graph API ve [OAuth 2,0 Kullanıcı akış uç noktaları](tokens-overview.md#endpoints)dahil olmak üzere senaryonuz için ilgili uç noktaları hızlıca tanımlamanızı sağlar.

- API **izinleri** ve **API 'yi kullanıma** sunma daha kapsamlı kapsam, izin ve izin yönetimi sağlar. Artık bir uygulamaya MS Graph ve Azure AD Graph izinleri de atayabilirsiniz.

-   **Sahipler** ve **bildirim** artık Azure AD B2C ile kimlik doğrulayan uygulamalar için kullanılabilir. Kayıtlarınız için sahip ekleyebilir ve [bildirim düzenleyicisini kullanarak](../active-directory/develop/reference-app-manifest.md)uygulama özelliklerini doğrudan düzenleyebilirsiniz.


## <a name="new-supported-account-types"></a>Yeni desteklenen hesap türleri

Yeni deneyimde, aşağıdaki seçeneklerden bir destek hesabı türü seçersiniz:
- Yalnızca bu kuruluş dizinindeki hesaplar
- Herhangi bir kuruluş dizinindeki (herhangi bir Azure AD dizini – Multitenant) hesaplar
- Herhangi bir kimlik sağlayıcısındaki veya kuruluş dizinindeki hesaplar (Kullanıcı akışlara sahip kullanıcıların kimliğini doğrulamak için)

Farklı hesap türlerini anlamak için, oluşturma deneyiminde seçmeme **Yardım** et ' i seçin.

Eski deneyimde uygulamalar her zaman müşteriye yönelik uygulamalar olarak oluşturulmuştur. Bu uygulamalar için, hesap türü **herhangi bir kimlik sağlayıcısında veya kuruluş dizininde (Kullanıcı akışları olan kullanıcıların kimliğini doğrulamak için) hesaplar** olarak ayarlanır.
> [!NOTE]
> Bu uygulama için kullanıcıların kimliğini doğrulamak üzere Kullanıcı akışlarını Azure AD B2C çalıştırabilmek için bu seçenek gereklidir. [Kullanıcı akışlarla kullanım için bir uygulamayı kaydetmeyi öğrenin.](tutorial-register-applications.md)

Bu seçeneği, SAML hizmet sağlayıcısı olarak Azure AD B2C kullanmak için de kullanabilirsiniz. [Daha fazla bilgi edinin](identity-provider-adfs.md).

## <a name="applications-for-devops-scenarios"></a>DevOps senaryolarına yönelik uygulamalar

Diğer hesap türlerini, kimlik deneyimi çerçevesi ilkelerini karşıya yüklemek veya kullanıcıları sağlamak için Microsoft Graph kullanma gibi DevOps senaryolarınızı yönetmek üzere bir uygulama oluşturmak için kullanabilirsiniz. [Azure AD B2C kaynaklarını yönetmek için Microsoft Graph uygulamasını nasıl kaydedeceğinizi](microsoft-graph-get-started.md)öğrenin.

Bu izinlerin birçoğu Azure B2C tüketicisi kullanıcıları için uygulanmadığından, tüm Microsoft Graph izinleri göremeyebilirsiniz. [Microsoft Graph kullanarak kullanıcıları yönetme hakkında daha fazla bilgi edinin](microsoft-graph-operations.md).

## <a name="admin-consent-and-offline_accessopenid-scopes"></a>Yönetici onayı ve offline_access + OpenID kapsamları
<!-- Azure AD B2C doesn't support user consent. That is, when a user signs into an application, the user doesn't see a screen requesting consent for the application permissions. All permissions have to be granted through admin consent.  -->

Azure AD B2C bir uygulamada kullanıcıları imzalayabilmesi için **OpenID** kapsamı gereklidir. **Offline_access** kapsamı, bir kullanıcı için yenileme belirteçleri vermek üzere gereklidir. Bu kapsamlar daha önce eklendi ve varsayılan olarak yönetici onayı verildi. Şimdi, oluşturma işlemi sırasında bu kapsamlar için izinleri kolayca ekleyerek, **OpenID 'ye yönetici Izni ver ve offline_access izinleri** seçeneğinin seçili olduğundan emin olabilirsiniz. Aksi takdirde, mevcut bir uygulama için **API izinleri** ayarlarında yönetici onayı ile Microsoft Graph izinleri eklenebilir.

[İzinler ve izin](../active-directory/develop/v2-permissions-and-consent.md)hakkında daha fazla bilgi edinin.

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Platformlar/kimlik doğrulaması: yanıt URL 'Leri/yeniden yönlendirme URI 'leri
Eski deneyimde, çeşitli platform türleri Web uygulamaları/API 'ler için yanıt URL 'leri ve yerel istemciler için yeniden yönlendirme URI **'si olarak yönetilir** . "Yerel istemciler" Ayrıca "ortak istemci" olarak da bilinir ve iOS, macOS, Android ve diğer mobil ve Masaüstü uygulama türlerine yönelik uygulamalar içerir.

Yeni deneyimde, yanıt URL 'Leri ve yeniden yönlendirme URI 'lerinin her ikisi de yeniden yönlendirme URI 'Leri olarak adlandırılır ve bir uygulamanın **kimlik doğrulama** bölümünde bulunabilir. Uygulama kayıtları, bir Web uygulaması veya yerel bir uygulama ile sınırlı değildir. İlgili yeniden yönlendirme URI 'Lerini kaydederek bu platform türleri için aynı uygulama kaydını kullanabilirsiniz.

Yeniden yönlendirme URI 'Lerinin, Web veya public (mobil ve Masaüstü) bir uygulama türüyle ilişkilendirilmesi gerekir. [Yeniden yönlendirme URI 'Leri hakkında daha fazla bilgi](../active-directory/develop/quickstart-register-app.md#add-a-redirect-uri)

<!-- Whether an application should be treated as a public client is inferred at run-time from the Redirect URI platform type, if possible. The **Treat application as a public client** setting should be set to **Yes** for flows that might not use a redirect URI, such as ROPC flows. -->

**İOS/macOS** ve **Android** platformları, bir genel istemci türüdür. İOS/macOS veya Android uygulamalarını MSAL ile kullanılmak üzere karşılık gelen yeniden yönlendirme URI 'leriyle yapılandırmanın kolay bir yolunu sağlar. [Uygulama yapılandırma seçenekleri](../active-directory/develop/msal-client-applications.md)hakkında daha fazla bilgi edinin.


## <a name="application-certificates--secrets"></a>Uygulama sertifikaları & gizlilikler

Yeni deneyimde **anahtarlar** yerine, sertifikaları ve gizli dizileri yönetmek için **Sertifikalar & gizlilikler** dikey penceresini kullanırsınız. Sertifikalar & gizlilikler, uygulamaların bir Web adreslenebilir konumda (HTTPS şeması kullanarak) belirteçleri alırken kendilerini kimlik doğrulama hizmetine tanımlamasına olanak tanır. Azure AD 'de kimlik doğrulanırken istemci kimlik bilgisi senaryoları için istemci parolası yerine bir sertifika kullanmanızı öneririz. Sertifikalar Azure AD B2C karşı kimlik doğrulaması yapmak için kullanılamaz.


## <a name="features-not-applicable-in-azure-ad-b2c-tenants"></a>Azure AD B2C kiracılarda geçerli değil Özellikler
Aşağıdaki Azure AD uygulama kayıt özellikleri Azure AD B2C kiracılarda geçerli değildir veya kullanılabilir değildir:
- **Roller ve yöneticiler** -Azure AD B2C için şu anda kullanılamıyor.
- **Marka** -UI/UX özelleştirmesi, **Şirket markası** deneyiminde veya bir Kullanıcı akışının parçası olarak yapılandırılmıştır. [Azure Active Directory B2C içinde Kullanıcı arabirimini özelleştirmeyi](customize-ui-with-html.md)öğrenin.
- **Yayımcı etki alanı doğrulaması** -uygulamanız, doğrulanmış bir etki alanı olmayan *. onmicrosoft.com* tarihinde kaydedilir. Ayrıca, yayımcı etki alanı birincil olarak Kullanıcı izni vermek için kullanılır ve bu, Kullanıcı kimlik doğrulaması için Azure AD B2C uygulamalar için geçerlidir. [Yayımcı etki alanı hakkında daha fazla bilgi edinin](../active-directory/develop/howto-configure-publisher-domain.md).
- **Belirteç yapılandırması** -belirteç, bir uygulama yerine Kullanıcı akışının bir parçası olarak yapılandırılır.
- **Hızlı başlangıç** deneyimi şu anda Azure AD B2C kiracılar için kullanılamaz.
<!-- - The **Integration assistant** blade is currently not available for Azure AD B2C tenants. -->

## <a name="limitations"></a>Sınırlamalar
Yeni deneyim aşağıdaki sınırlamalara sahiptir:
- Şu anda Azure AD B2C, örtük akışlar için erişim veya KIMLIK belirteçleri yayınlayamaz. **kimlik doğrulama** dikey penceresinde **Kimlik belirteçleri** seçeneği belirlenmişse, örtük verme akışı için her iki tür belirteç vardır.
<!-- - Azure AD B2C doesn't currently support the single-page application "SPA" app type.  -->
- Desteklenen hesapların değerini değiştirmek Kullanıcı arabiriminde desteklenmez. Azure AD tek kiracılı ve çok kiracılı bir şekilde geçiş yapmadığınız takdirde uygulama bildirimini kullanmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Yeni uygulama kayıt deneyimini kullanmaya başlamak için:
* [Bir Web uygulamasını nasıl kaydedeceğinizi](tutorial-register-applications.md)öğrenin.
* [Bir Web API 'sini kaydetmeyi](add-web-api-application.md)öğrenin.
* [Yerel istemci uygulamasını nasıl kaydedeceğinizi](add-native-application.md)öğrenin.
* [Azure AD B2C kaynaklarını yönetmek için Microsoft Graph uygulamasını nasıl kaydedeceğinizi](microsoft-graph-get-started.md)öğrenin.
* [SAML hizmet sağlayıcısı olarak Azure AD B2C kullanmayı öğrenin.](identity-provider-adfs.md)
* [Uygulama türleri](application-types.md)hakkında bilgi edinin.