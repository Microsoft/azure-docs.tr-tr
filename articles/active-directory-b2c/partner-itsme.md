---
title: ıtsme OpenID Connect Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Client_secret Kullanıcı akış ilkesi kullanarak ıtsme OıDC ile Azure AD B2C kimlik doğrulamasını tümleştirmeyi öğrenin. ıtsme dijital bir KIMLIK uygulamasıdır. Kart okuyucular, parolalar, iki öğeli kimlik doğrulaması ve birden çok PIN kodu olmadan güvenli bir şekilde oturum açmanıza olanak tanır.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2560c294b63d0554708eda1f804bf279d16d7d74
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257968"
---
# <a name="configure-itsme-openid-connect-oidc-with-azure-active-directory-b2c"></a>Itsme OpenID Connect 'i (OıDC) Azure Active Directory B2C ile yapılandırma

Itsme dijital KIMLIĞI uygulaması, kart okuyucular, parolalar, iki öğeli kimlik doğrulama veya birden çok PIN kodu olmadan güvenli bir şekilde oturum açmanıza olanak tanır. Itsme uygulaması doğrulanmış bir kimlikle güçlü müşteri kimlik doğrulaması sağlar. Bu makalede, istemci gizli Kullanıcı akışı ilkesi kullanarak ıtsme OpenID Connect (OıDC) ile Azure AD B2C kimlik doğrulamasını tümleştirmeyi öğrenin.

## <a name="prerequisites"></a>Önkoşullar

Başlamak için şunlar gerekir:

* Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.
* Azure aboneliğinize bağlı [bir Azure AD B2C kiracısı](tutorial-create-tenant.md) .
* Istemci KIMLIĞINIZ (Iş ortağı kodu olarak da bilinir), ıtsme tarafından temin edilmiştir.
* Hizmet kodunuz, ıtsme tarafından sağlanıyor.
* Itsme hesabınız için istemci gizli anahtarı.

## <a name="scenario-description"></a>Senaryo açıklaması

![ıtsme mimarisi diyagramı](media/partner-itsme/itsme-architecture-diagram.png)

<!--
Please clarify step 1 in the description below - we don't have steps in this tutorial for "adapting in the Azure AD B2C Custom Policy- User Journeys" - should this be added somewhere?
-->

| Adım | Açıklama |
|------|------|
|1     | Web sitenizde veya uygulamanızda, Azure AD B2C Kullanıcı akışında uyarlayarak, **itsme düğmesine tıklayarak oturum açın** . Etkileşim akışı, Kullanıcı bu düğmeye tıkladığında başlar.  |
|2     | Azure AD B2C, ıtsme istemci gizli API 'sine bir yetkilendirme isteği göndererek OpenID Connect akışını başlatır. Uç noktalar hakkındaki bilgileri içeren iyi bilinen/OpenID-Configuration uç noktası kullanılabilir.  |
|3     | Itsme ortamı, kullanıcının kendi telefon numaralarını doldurmasına izin vererek kullanıcıyı kendi kendinize yönelik olarak tanımlamak için kullanıcıyı bu sayfaya yönlendirir.  |
|4     | Itsme ortamı, kullanıcının telefon numarasını alır ve doğruluğu doğrular.  |
|5     | Telefon numarası etkin bir ıtsme kullanıcısına aitse, itsme uygulaması için bir eylem oluşturulur.  |
|6     | Kullanıcı, ıtsme uygulamasını açar, isteği denetler ve eylemi onaylar.  |
|7     |  Uygulama, uygulamayı onaylanan ortama bildirir. |
|8     |  Itsme ortamı Azure AD B2C için OAuth yetkilendirme kodunu döndürür. |
|9     |  Yetkilendirme kodunu kullanarak Azure AD B2C bir belirteç isteği yapar. |
| 10 | Itsme ortamı, belirteç isteğini denetler ve hala geçerliyse, OAuth erişim belirtecini ve istenen kullanıcı bilgilerini içeren KIMLIK belirtecini döndürür. |
| 11 | Son olarak, Kullanıcı yeniden yönlendirme URL 'sine kimliği doğrulanmış bir kullanıcı olarak yönlendirilir.  |
|   |   |

## <a name="onboard-with-itsme"></a>Itsme ile ekleme

1. Itsme ile bir hesap oluşturmak için [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace)'nde ıtsme adresini ziyaret edin.

2. Adresine bir e-posta göndererek, ıtsme hesabınızı etkinleştirin onboarding@itsme.be . B2C kurulumu için gerekli olacak bir **Iş ortağı kodu** ve **hizmet kodu** alacaksınız.

3. Itsme iş ortağı hesabınızı etkinleştirdikten sonra, **istemci sırrı** için tek seferlik bir bağlantı içeren bir e-posta alacaksınız.

4. Yapılandırmayı gerçekleştirmek için [itsme](https://business.itsme.be/en) içindeki yönergeleri izleyin.

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C ile tümleştirme

### <a name="set-up-a-new-identity-provider-in-azure-ad-b2c"></a>Azure AD B2C yeni bir kimlik sağlayıcısı ayarlama

> [!NOTE]
> Henüz bir tane yoksa, Azure aboneliğinize bağlı [bir Azure AD B2C kiracı oluşturun](tutorial-create-tenant.md) .

1. Azure AD B2C kiracı içeren dizini kullandığınızdan emin olun. Üstteki menüden **Dizin + abonelik** filtresini seçin ve Azure AD B2C kiracınızı içeren dizini seçin.

2. **Azure hizmetleri** altında **Azure AD B2C** ' yi seçin (veya **diğer hizmetler** ' i seçin ve *Azure AD B2C* aramak için **tüm hizmetler** arama kutusunu kullanın).

3. **Kimlik sağlayıcıları**' nı seçin ve ardından **Yeni OpenID Connect sağlayıcısı**' nı seçin.

4. Formu aşağıdaki bilgilerle girin:

   |Özellik | Değer |
   |------------ |------- |
   | Ad | itsme |
   | Meta veri URL 'SI | `https://oidc.<environment>.itsme.services/clientsecret-oidc/csapi/v0.1/.well-known/openid-configuration` <br>nerede `<environment>` `e2e` (test ortamı) ya da `prd` (üretim)  |
   | ClientID     | **ISTEMCI kimliğiniz**( **iş ortağı kodu** olarak da bilinir)  |
   | İstemci Gizli Anahtarı | **Client_secret** |
   | Kapsam  | OpenID hizmeti: YOURSERVICECODE profili e-postası [Phone] [Adres]  |
   |Yanıt türü | kod |
   |Yanıt modu | sorgu |
   |Etki alanı Ipucu | *Bu boş bırakabilirsiniz* |
   |UserID | alt |
   |Görünen Ad | name |
   |Ad | given_name |
   |Soyadı | family_name |
   |E-posta | e-posta|

5. **Kaydet**’i seçin.

### <a name="configure-a-user-flow"></a>Kullanıcı akışı yapılandırma

1. Azure AD B2C kiracınızda **ilkeler** altında **Kullanıcı akışları**' nı seçin.

2. **Yeni Kullanıcı akışı**' nı seçin.

3. **Kaydolun ve oturum açın**' ı seçin, bir sürüm seçin ve ardından **Oluştur**' u seçin.

4. Bir **ad** girin.

5. **Kimlik sağlayıcıları** bölümünde, **ıtsme**' yi seçin.

6. **Oluştur**’u seçin.

7. Kullanıcı akış adını seçerek yeni oluşturulan kullanıcı akışını açın.

8. **Özellikler** ' i seçin ve aşağıdaki değerleri ayarlayın:

   * **Erişim & kimliği belirteci yaşam sürelerini (dakika)** **5**' e değiştirin.
   * **Yenileme belirteci kayan pencere ömrünü** **süre sonu yok** olarak değiştirin.

### <a name="register-an-application"></a>Bir uygulamayı kaydetme

1. B2C kiracınızda, **Yönet** altında   >  **Yeni kayıt** uygulama kayıtları ' yi seçin.

2. Uygulama için bir **ad** girin ve **yeniden yönlendirme URI**'nizi girin. Sınama amacıyla, girin `https://jwt.ms` .

3. Multi-Factor Authentication 'ın **devre dışı bırakıldığından** emin olun.

4. **Kaydet**’i seçin.

   a. Sınama amacıyla **kimlik doğrulaması**' nı seçin ve **örtük izin**' ın altında, **erişim belirteçleri** ve **Kimlik belirteçleri** onay kutularını seçin.  

   b. **Kaydet**’i seçin.

## <a name="test-the-user-flow"></a>Kullanıcı akışını test etme

1. B2C kiracınızda **ilkeler** altında **Kullanıcı akışları**' nı seçin.

2. Daha önce oluşturduğunuz Kullanıcı akışınızı seçin.

3. **Kullanıcı akışını Çalıştır**' ı seçin.

   a. **Uygulama**: *kayıtlı uygulamayı seçin*

   b. **Yanıt URL 'si**: *yeniden yönlendirme URL 'sini seçin*

4. **Kendinizi tanımlayabilme** sayfası görüntülenir.  

5. Cep telefonu numaranızı girin ve **Gönder**' i seçin.

6. Itsme uygulamasındaki eylemi onaylayın.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makaleleri gözden geçirin:

* [Azure AD B2C'deki özel ilkeler](custom-policy-overview.md)

* [Azure AD B2C özel ilkeleri kullanmaya başlama](tutorial-create-user-flows.md?pivots=b2c-custom-policy)