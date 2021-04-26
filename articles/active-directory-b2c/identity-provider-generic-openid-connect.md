---
title: OpenID Connect ile kaydolma ve oturum açma ayarlama
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C içinde herhangi bir OpenID Connect kimlik sağlayıcısı (IDP) ile kaydolma ve oturum açma ayarlayın.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/05/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 21f31a21921953148ae0438cee04d846706161cc
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382938"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Azure Active Directory B2C kullanarak OpenID Connect ile kaydolma ve oturum açma ayarlama

[OpenID Connect](openid-connect.md) , OAuth 2,0 üzerinde oluşturulmuş, güvenli Kullanıcı oturumu açma için kullanılabilen bir kimlik doğrulama protokolüdür. Bu protokolü kullanan kimlik sağlayıcılarının çoğu Azure AD B2C desteklenir. Bu makalede, Kullanıcı akışlarınıza özel OpenID Connect kimlik sağlayıcılarını nasıl ekleyebileceğiniz açıklanmaktadır.

## <a name="add-the-identity-provider"></a>Kimlik sağlayıcısını ekleme

1. [Azure portalda](https://portal.azure.com/) Azure AD B2C kiracınızın genel yöneticisi olarak oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresi ' ne tıklayarak ve kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portalın sol üst köşesinde **Tüm hizmetler**’i seçin ve **Azure AD B2C**’yi arayıp seçin.
1. **Kimlik sağlayıcıları**' nı seçin ve ardından **Yeni OpenID Connect sağlayıcısı**' nı seçin.
1. Bir **ad** girin. Örneğin, *contoso* girin.

## <a name="configure-the-identity-provider"></a>Kimlik sağlayıcısını yapılandırma

Her OpenID Connect kimlik sağlayıcısı, oturum açma işlemini gerçekleştirmek için gereken bilgilerin çoğunu içeren bir meta veri belgesi tanımlar. Bu, kullanılacak URL 'Ler ve hizmetin ortak imzalama anahtarlarının konumu gibi bilgileri içerir. OpenID Connect meta veri belgesi her zaman içinde biten bir uç noktada bulunur `.well-known/openid-configuration` . Eklemek istediğiniz OpenID Connect kimlik sağlayıcısı için, meta veri URL 'sini girin.

## <a name="client-id-and-secret"></a>İstemci KIMLIĞI ve gizli anahtar

Kullanıcıların oturum açmalarına izin vermek için, kimlik sağlayıcısı geliştiricilerin hizmetine bir uygulama kaydetmesini gerektirir. Bu uygulamanın, **ISTEMCI kimliği** ve **istemci PAROLASı** olarak adlandırılan kimliği vardır. Bu değerleri kimlik sağlayıcısından kopyalayın ve bunlara karşılık gelen alanlara girin.

> [!NOTE]
> İstemci parolası isteğe bağlıdır. Ancak, belirteç kodunu değiştirmek için gizli dizi kullanan [yetkilendirme kodu akışını](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)kullanmak istiyorsanız bir istemci gizli anahtarı girmeniz gerekir.

## <a name="scope"></a>Kapsam

Kapsam, kimlik sağlayıcınızdan toplamak istediğiniz bilgileri ve izinleri (örneğin,) tanımlar `openid profile` . Kimlik sağlayıcısından kimlik belirtecini almak için `openid` kapsam belirtilmelidir. KIMLIK belirteci olmadan, kullanıcılar özel kimlik sağlayıcısını kullanarak Azure AD B2C oturum açamaz. Diğer kapsamlar boşlukla ayırarak eklenebilir. Diğer kapsamların kullanılabilir olduğunu görmek için özel kimlik sağlayıcısının belgelerine bakın.

## <a name="response-type"></a>Yanıt türü

Yanıt türü, özel kimlik sağlayıcısının ilk çağrısında ne tür bilgilerin geri gönderileceğini açıklar `authorization_endpoint` . Aşağıdaki yanıt türleri kullanılabilir:

* `code`: [Yetkilendirme kodu akışına](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)göre Azure AD B2C bir kod geri döndürülür. Azure AD B2C, belirtecin kodunu değiştirmek için ' a çağrı devam eder `token_endpoint` .
* `id_token`: Özel kimlik sağlayıcısından Azure AD B2C bir KIMLIK belirteci geri döndürülür.

## <a name="response-mode"></a>Yanıt modu

Yanıt modu, verileri özel kimlik sağlayıcısından Azure AD B2C 'e geri göndermek için kullanılması gereken yöntemi tanımlar. Aşağıdaki yanıt modları kullanılabilir:

* `form_post`: Bu yanıt modu en iyi güvenlik için önerilir. Yanıt HTTP yöntemiyle iletilir ve `POST` biçimi kullanılarak gövdede kodlanacak kodla veya belirtece sahip olur `application/x-www-form-urlencoded` .
* `query`: Kod veya belirteç bir sorgu parametresi olarak döndürülür.

## <a name="domain-hint"></a>Etki alanı İpucu

Etki alanı ipucu, kullanıcının kullanılabilir kimlik sağlayıcılarının listesi arasında seçim yapmasını sağlamak yerine, belirtilen kimlik sağlayıcısının oturum açma sayfasına doğrudan atlamak için kullanılabilir. Bu tür davranışa izin vermek için, etki alanı ipucu için bir değer girin. Özel kimlik sağlayıcısına geçmek için, `domain_hint=<domain hint value>` oturum açma için Azure AD B2C çağırırken parametresini isteğinizin sonuna ekleyin.

## <a name="claims-mapping"></a>Talep eşleme

Özel kimlik sağlayıcısı bir KIMLIK belirtecini Azure AD B2C 'e geri gönderdikten sonra, Azure AD B2C alınan belirteçteki talepleri Azure AD B2C tanıdığı ve kullandığı taleplerle eşleyebilmelidir. Aşağıdaki eşlemelerin her biri için, kimlik sağlayıcısının belirteçlerine geri döndürülen talepleri anlamak üzere özel kimlik sağlayıcısının belgelerine bakın:

* **Kullanıcı kimliği**: oturum açmış kullanıcı için *benzersiz tanımlayıcıyı* sağlayan talebi girin.
* **Görünen ad**: Kullanıcı için *görünen adı* veya *tam adı* sağlayan talebi girin.
* **Verilen ad**: kullanıcının *ilk adını* sağlayan talebi girin.
* **Soyadı**: kullanıcının *soyadını* sağlayan talebi girin.
* **E-posta**: kullanıcının *e-posta adresini* sağlayan talebi girin.

## <a name="add-the-identity-provider-to-a-user-flow"></a>Kimlik sağlayıcısını bir Kullanıcı akışına ekleme 

1. Azure AD B2C kiracınızda **Kullanıcı akışları**' nı seçin.
1. Kimlik sağlayıcısını eklemek istediğiniz kullanıcı akışına tıklayın. 
1. **Sosyal kimlik sağlayıcıları** altında, eklediğiniz kimlik sağlayıcısını seçin. Örneğin, *contoso*.
1. **Kaydet**’i seçin.
1. İlkenizi test etmek için **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama** için, daha önce kaydettiğiniz *testapp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Kullanıcı akışını Çalıştır** düğmesini seçin.
1. Kaydolma veya oturum açma sayfasından, oturum açmak istediğiniz kimlik sağlayıcısını seçin. Örneğin, *contoso*.

Oturum açma işlemi başarılı olursa, tarayıcınız öğesine yönlendirilir `https://jwt.ms` ve bu, Azure AD B2C tarafından döndürülen belirtecin içeriğini görüntüler.
