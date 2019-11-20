---
title: Öğretici-uygulamalarınıza kimlik sağlayıcıları ekleme-Azure Active Directory B2C
description: Azure Active Directory B2C Azure portal kullanarak uygulamalarınıza kimlik sağlayıcıları eklemeyi öğrenin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bc7828b7926ea6e7f2d6bc3891ee231972ae3208
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063245"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Öğretici: Azure Active Directory B2C ' deki uygulamalarınıza kimlik sağlayıcıları ekleyin

Uygulamalarınızda, kullanıcıların farklı kimlik sağlayıcılarıyla oturum açmasını sağlamak isteyebilirsiniz. Kimlik *sağlayıcısı* , uygulamalara kimlik doğrulama hizmetleri sağlarken kimlik bilgilerini oluşturur, korur ve yönetir. Azure portal kullanarak [Kullanıcı akışlarınıza](active-directory-b2c-reference-policies.md) Azure Active Directory B2C (Azure AD B2C) tarafından desteklenen kimlik sağlayıcıları ekleyebilirsiniz.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Kimlik sağlayıcısı uygulamalarını oluşturma
> * Kiracınıza kimlik sağlayıcılarını ekleyin
> * Kimlik sağlayıcılarını Kullanıcı akışınıza ekleme

Genellikle uygulamalarınızda yalnızca bir kimlik sağlayıcısı kullanırsınız, ancak daha fazla ekleme seçeneğiniz vardır. Bu öğreticide, uygulamanıza bir Azure AD kimlik sağlayıcısı ve Facebook kimlik sağlayıcısı ekleme işlemlerinin nasıl yapılacağı gösterilmektedir. Bu kimlik sağlayıcılarının her ikisini de uygulamanıza eklemek isteğe bağlıdır. Ayrıca, [Amazon](active-directory-b2c-setup-amzn-app.md), [GitHub](active-directory-b2c-setup-github-app.md), [Google](active-directory-b2c-setup-goog-app.md), [LinkedIn](active-directory-b2c-setup-li-app.md), [Microsoft](active-directory-b2c-setup-msa-app.md)veya [Twitter](active-directory-b2c-setup-twitter-app.md)gibi diğer kimlik sağlayıcılarını da ekleyebilirsiniz.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Kullanıcıların uygulamanızda kaydolup oturum açmasını sağlamak için [bir Kullanıcı akışı oluşturun](tutorial-create-user-flows.md) .

## <a name="create-applications"></a>Uygulama oluşturma

Kimlik sağlayıcısı uygulamaları, Azure AD B2C kiracınızla iletişimi etkinleştirmek için tanımlayıcıyı ve anahtarı sağlar. Öğreticinin bu bölümünde, kiracınıza kimlik sağlayıcıları eklemek için tanımlayıcıları ve anahtarları alacağınız bir Azure AD uygulaması ve Facebook uygulaması oluşturacaksınız. Kimlik sağlayıcılarından yalnızca birini ekliyorsanız yalnızca bu sağlayıcı için uygulamayı oluşturmanız gerekir.

### <a name="create-an-azure-active-directory-application"></a>Azure Active Directory uygulaması oluşturma

Azure AD 'den kullanıcıların oturum açma özelliğini etkinleştirmek için Azure AD kiracısı içinde bir uygulamayı kaydetmeniz gerekir. Azure AD kiracısı Azure AD B2C kiracınızla aynı değildir.

1. [Azure Portal](https://portal.azure.com) oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini SEÇIP Azure AD kiracınızı içeren dizini seçerek Azure AD kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **uygulama kayıtları**' i arayıp seçin.
1. **Yeni kayıt**seçeneğini belirleyin.
1. Uygulamanız için bir ad girin. Örneğin: `Azure AD B2C App`.
1. Bu **kuruluş dizinindeki hesapların seçimini yalnızca** bu uygulama için kabul edin.
1. **Yeniden yönlendirme URI 'si**için, **Web** değerini kabul edin ve Azure AD B2C kiracınızın adıyla değiştirerek `your-B2C-tenant-name` aşağıdaki URL 'yi tüm küçük harflerle girin.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Örneğin: `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Tüm URL 'Ler artık [b2clogin.com](b2clogin.md)kullanıyor olmalıdır.

1. **Kaydet**' i seçin ve ardından sonraki adımda kullandığınız **uygulama (istemci) kimliğini** kaydedin.
1. Uygulama menüsünde **Yönet** ' ın altında **Sertifikalar & gizlilikler**' ı seçin ve ardından **yeni istemci parolası**' nı seçin.
1. İstemci parolası için bir **Açıklama** girin. Örneğin: `Azure AD B2C App Secret`.
1. Süre sonu dönemini seçin. Bu uygulama için, **1 yıl içinde**seçimini kabul edin.
1. **Ekle**' yi seçin ve ardından sonraki bir adımda kullandığınız yeni istemci parolasının değerini kaydedin.

### <a name="create-a-facebook-application"></a>Facebook uygulaması oluşturma

Bir Facebook hesabını Azure AD B2C bir kimlik sağlayıcısı olarak kullanmak için Facebook 'ta bir uygulama oluşturmanız gerekir. Henüz bir Facebook hesabınız yoksa, bu [https://www.facebook.com/](https://www.facebook.com/)hesabı edinebilirsiniz.

1. Facebook hesabı kimlik bilgilerinizle [geliştiriciler Için Facebook](https://developers.facebook.com/) 'ta oturum açın.
1. Daha önce yapmadıysanız, Facebook geliştiricisi olarak kaydolmanız gerekir. Bunu yapmak için sayfanın sağ üst **köşesinden başlayın ' ı seçin,** Facebook ilkelerini kabul edin ve kayıt adımlarını uygulayın.
1. **Uygulamalarım** ' ı ve ardından **uygulama oluştur**' u seçin.
1. Bir **görünen ad** ve geçerli bir **Iletişim e-postası**girin.
1. **Uygulama kimliği oluştur**' a tıklayın. Bu, Facebook platformu ilkelerini kabul etmenizi ve çevrimiçi bir güvenlik denetimi tamamlamanızı gerektirebilir.
1. **Ayarlar** > **temel**öğesini seçin.
1. Örneğin`Business and Pages`, bir **Kategori**seçin. Bu değer Facebook için gereklidir, ancak Azure AD B2C tarafından kullanılmaz.
1. Sayfanın alt kısmındaki **Platform Ekle**' yi ve ardından **Web sitesi**' ni seçin.
1. **Site URL 'si**' nde `https://your-tenant-name.b2clogin.com/` , `your-tenant-name` kiracınızın adıyla değiştirin yazın.
1. **Gizlilik ILKESI URL**'si IÇIN bir URL girin, örneğin `http://www.contoso.com/`. Gizlilik ilkesi URL 'SI, uygulamanız için gizlilik bilgilerini sağlamak üzere tuttuğunuz bir sayfasıdır.
1. **Değişiklikleri Kaydet**' i seçin.
1. Sayfanın üst kısmında, **uygulama kimliği**değerini kaydedin.
1. **Uygulama gizli**dizisi ' nin yanında **göster** ' i seçin ve değerini kaydedin. Facebook 'ı kiracınızda bir kimlik sağlayıcısı olarak yapılandırmak için hem uygulama KIMLIĞI hem de uygulama gizli anahtarını kullanın. **Uygulama gizli anahtarı** , güvenli bir şekilde depolamanız gereken önemli bir güvenlik kimlik bilgileridir.
1. **Ürünlerin**yanındaki artı işaretini ve ardından **Facebook oturumu**' nın altında **Ayarla**' yı seçin.
1. Sol taraftaki menüde **Facebook oturumu** ' nın altında **Ayarlar**' ı seçin.
1. **Geçerli OAuth yeniden yönlendirme URI 'lerinde**, `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`girin. Kiracınızın adıyla değiştirin `your-tenant-name` . Sayfanın alt kısmındaki **Değişiklikleri Kaydet** ' i seçin.
1. Facebook uygulamanızı Azure AD B2C için kullanılabilir hale **getirmek için sayfanın** sağ üst kısmındaki **durum** seçicisine tıklayın ve uygulamayı ortak hale getirmek Için açın ve ardından **Onayla**' ya tıklayın. Bu noktada durum, **geliştirme aşamasında** **canlı**olarak değiştirilmelidir.

## <a name="add-the-identity-providers"></a>Kimlik sağlayıcılarını ekleme

Eklemek istediğiniz kimlik sağlayıcısı için uygulamayı oluşturduktan sonra, kimlik sağlayıcısını kiracınıza eklersiniz.

### <a name="add-the-azure-active-directory-identity-provider"></a>Azure Active Directory Identity sağlayıcısını ekleme

1. Azure AD B2C kiracı içeren dizini kullandığınızdan emin olun. Üstteki menüden **Dizin + abonelik** filtresini seçin ve Azure AD B2C kiracınızı içeren dizini seçin.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
1. **Kimlik sağlayıcıları**' nı seçin ve ardından **Yeni OpenID Connect sağlayıcısı**' nı seçin.
1. Bir **ad**girin. Örneğin, *contoso Azure AD*yazın.
1. **Meta veri URL 'si**için, Azure AD kiracınızın etki alanı adıyla `your-AD-tenant-domain` birlikte aşağıdaki URL 'yi girin:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Örneğin: `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

1. **ISTEMCI kimliği**için, daha önce KAYDETTIĞINIZ uygulama kimliğini girin.
1. **İstemci parolası**için, daha önce kaydettiğiniz istemci gizli anahtarını girin.
1. **Kapsam**, **yanıt türü**ve **Yanıt modu**için varsayılan değerleri bırakın.
1. Seçim **Domain_hint**için bir değer girin. Örneğin, *Contosoad*. [Etki alanı ipuçları](../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) , bir uygulamadan gelen kimlik doğrulama isteğine dahil olan yönergelerden yapılır. Bu kişiler, kullanıcıyı Federal IDP oturum açma sayfasında hızlandırmak için kullanılabilirler. Ya da çok kiracılı bir uygulama tarafından, kullanıcıyı kiracının kendi markalı Azure AD oturum açma sayfasına doğrudan hızlandırmak için kullanılabilirler.
1. **Kimlik sağlayıcısı talep eşlemesi**altında, aşağıdaki talep eşleme değerlerini girin:

    * **Kullanıcı kimliği**: *OID*
    * **Görünen ad**: *ad*
    * **Verilen ad**: *given_name*
    * **Soyadı**: *family_name*
    * **E-posta**: *unique_name*

1. **Kaydet**’i seçin.

### <a name="add-the-facebook-identity-provider"></a>Facebook kimlik sağlayıcısını ekleme

1. **Kimlik sağlayıcıları**' nı seçin ve **Facebook**' u seçin.
1. Bir **ad**girin. Örneğin, *Facebook*.
1. **ISTEMCI kimliği**için, daha önce oluşturduğunuz Facebook UYGULAMASıNıN uygulama kimliğini girin.
1. **İstemci parolası**için, kaydettiğiniz uygulama gizli anahtarını girin.
1. **Kaydet**’i seçin.

## <a name="update-the-user-flow"></a>Kullanıcı akışını güncelleştirme

Önkoşulların bir parçası olarak tamamladığınız öğreticide, kaydolma ve oturum açma için *B2C_1_signupsignin1*adlı bir Kullanıcı akışı oluşturdunuz. Bu bölümde, kimlik sağlayıcılarını *B2C_1_signupsignin1* Kullanıcı akışına eklersiniz.

1. **Kullanıcı akışları ' nı (ilkeler)** seçin ve ardından *B2C_1_signupsignin1* Kullanıcı akışını seçin.
2. **Kimlik sağlayıcıları**' nı seçin, eklediğiniz **Facebook** ve **contoso Azure AD** kimlik sağlayıcılarını seçin.
3. **Kaydet**’i seçin.

## <a name="test-the-user-flow"></a>Kullanıcı akışını test etme

1. Oluşturduğunuz Kullanıcı akışının genel bakış sayfasında, **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama**için, daha önce kaydettiğiniz *WebApp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi `https://jwt.ms`gerekir.
1. **Kullanıcı akışını Çalıştır**' ı seçin ve daha önce eklemiş olduğunuz bir kimlik sağlayıcısıyla oturum açın.
1. Eklediğiniz diğer kimlik sağlayıcıları için 1 ile 3 arasındaki adımları yineleyin.

Oturum açma işlemi başarılı olursa, şu şekilde kodu çözülen belirteci görüntüleyen `https://jwt.ms` öğesine yönlendirilirsiniz:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "<key-ID>"
}.{
  "exp": 1562346892,
  "nbf": 1562343292,
  "ver": "1.0",
  "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
  "sub": "20000000-0000-0000-0000-000000000000",
  "aud": "30000000-0000-0000-0000-000000000000",
  "nonce": "defaultNonce",
  "iat": 1562343292,
  "auth_time": 1562343292,
  "name": "User Name",
  "idp": "facebook.com",
  "postalCode": "12345",
  "tfp": "B2C_1_signupsignin1"
}.[Signature]
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, şu şekilde nasıl yapılacağını öğrendiniz:

> [!div class="checklist"]
> * Kimlik sağlayıcısı uygulamalarını oluşturma
> * Kiracınıza kimlik sağlayıcılarını ekleyin
> * Kimlik sağlayıcılarını Kullanıcı akışınıza ekleme

Ardından, kullanıcılara gösterilen sayfaların Kullanıcı arabirimini, uygulamalarınızda bulunan kimlik deneyiminin bir parçası olarak özelleştirmeyi öğrenin:

> [!div class="nextstepaction"]
> [Azure Active Directory B2C ' de uygulamalarınızın Kullanıcı arabirimini özelleştirme](tutorial-customize-ui.md)
