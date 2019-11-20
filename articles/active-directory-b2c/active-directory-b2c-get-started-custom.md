---
title: Özel ilkeleri kullanmaya başlama-Azure Active Directory B2C
description: Azure Active Directory B2C özel ilkeleri kullanmaya nasıl başlaleyeceğinizi öğrenin.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 411710280a631d341adeb55bc4b587a613ee3c4c
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73643631"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C özel ilkeleri kullanmaya başlama

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Özel ilkeler](active-directory-b2c-overview-custom.md) , Azure Active Directory B2C (Azure AD B2C) kiracınızın davranışını tanımlayan yapılandırma dosyalarıdır. Bu makalede, bir e-posta adresi ve parola kullanarak yerel hesap kaydolma veya oturum açma 'yı destekleyen özel bir ilke oluşturacaksınız. Ayrıca, ortamınızı kimlik sağlayıcıları eklemek için hazırlarsınız.

## <a name="prerequisites"></a>Ön koşullar

- Henüz bir tane yoksa, Azure aboneliğinize bağlı [bir Azure AD B2C kiracı oluşturun](tutorial-create-tenant.md) .
- [Uygulamanızı](tutorial-register-applications.md) , Azure AD B2C ile iletişim kurabilmesi için oluşturduğunuz kiracıya kaydedin.
- Facebook uygulamasını yapılandırmak için [kayıt ayarlama ve Facebook hesabıyla oturum açma](active-directory-b2c-setup-fb-app.md) adımlarını izleyin.

## <a name="add-signing-and-encryption-keys"></a>İmzalama ve şifreleme anahtarları Ekle

1. [Azure portalda](https://portal.azure.com) oturum açma
1. Azure AD B2C kiracınızı içeren dizini seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C**seçin.
1. Genel Bakış sayfasında **kimlik deneyimi çerçevesi**' ni seçin.

### <a name="create-the-signing-key"></a>İmzalama anahtarını oluşturma

1. **Ilke anahtarlarını** seçin ve ardından **Ekle**' yi seçin.
1. **Seçenekler**için `Generate`seçin.
1. **Ad**alanına `TokenSigningKeyContainer`girin. Ön ek `B2C_1A_` otomatik olarak eklenebilir.
1. **Anahtar türü**için **RSA**' yı seçin.
1. **Anahtar kullanımı**için **imza**' yı seçin.
1. **Oluştur**'u seçin.

### <a name="create-the-encryption-key"></a>Şifreleme anahtarı oluşturma

1. **Ilke anahtarlarını** seçin ve ardından **Ekle**' yi seçin.
1. **Seçenekler**için `Generate`seçin.
1. **Ad**alanına `TokenEncryptionKeyContainer`girin. `B2C_1A`_ ön eki otomatik olarak eklenebilir.
1. **Anahtar türü**için **RSA**' yı seçin.
1. **Anahtar kullanımı**için **şifreleme**' yi seçin.
1. **Oluştur**'u seçin.

### <a name="create-the-facebook-key"></a>Facebook anahtarını oluşturma

Facebook uygulamanızın [uygulama gizli](active-directory-b2c-setup-fb-app.md) anahtarını ilke anahtarı olarak ekleyin. Oluşturduğunuz uygulamanın uygulama gizli anahtarını, bu makalenin önkoşullarının bir parçası olarak kullanabilirsiniz.

1. **Ilke anahtarlarını** seçin ve ardından **Ekle**' yi seçin.
1. **Seçenekler**için `Manual`seçin.
1. **Ad**için `FacebookSecret`girin. Ön ek `B2C_1A_` otomatik olarak eklenebilir.
1. **Gizli**dizi bölümünde Facebook uygulamanızın *uygulama gizli* anahtarını Developers.facebook.com adresinden girin. Bu değer, uygulama KIMLIĞI değil gizli dizi.
1. **Anahtar kullanımı**için **imza**' yı seçin.
1. **Oluştur**'u seçin.

## <a name="register-identity-experience-framework-applications"></a>Kimlik deneyimi çerçevesi uygulamalarını kaydetme

Azure AD B2C, yerel hesaplarla kullanıcılara kaydolmak ve oturum açmak için kullandığı iki uygulamayı kaydetmenizi gerektirir: *IdentityExperienceFramework*, BIR Web API 'Si ve *ProxyIdentityExperienceFramework*, IdentityExperienceFramework uygulaması. Kullanıcılarınız bir e-posta adresi veya Kullanıcı adı ile kaydolabilir ve bir "yerel hesap" oluşturan kiracınızın kayıtlı uygulamalarına erişmek için bir parolayla kaydolabilirsiniz. Yerel hesaplar yalnızca Azure AD B2C kiracınızda bulunur.

Bu iki uygulamayı Azure AD B2C kiracınızda yalnızca bir kez kaydetmeniz gerekir.

### <a name="register-the-identityexperienceframework-application"></a>IdentityExperienceFramework uygulamasını kaydetme

Bir uygulamayı Azure AD B2C kiracınıza kaydetmek için, geçerli **uygulamalar** deneyimini veya yeni Birleşik **uygulama kayıtları (Önizleme)** deneyimimizi kullanabilirsiniz. [Yeni deneyim hakkında daha fazla bilgi edinin](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Uygulamalar](#tab/applications/)

1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin.
1. Arama kutusuna `Azure Active Directory` yazın.
1. Arama sonuçlarında **Azure Active Directory** ' yi seçin.
1. Sol taraftaki menüde **Yönet** ' in altında **uygulama kayıtları (eski)** seçeneğini belirleyin.
1. **Yeni uygulama kaydı**’nı seçin.
1. **Ad**için `IdentityExperienceFramework`girin.
1. **Uygulama türü**için **Web uygulaması/API**' yi seçin.
1. **Oturum açma URL 'si**için `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`girin; burada `your-tenant-name` Azure AD B2C kiracı etki alanı adıdır. Tüm URL 'Ler artık [b2clogin.com](b2clogin.md)kullanıyor olmalıdır.
1. **Oluştur**'u seçin. Oluşturulduktan sonra uygulama KIMLIĞINI kopyalayın ve daha sonra kullanmak üzere kaydedin.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Uygulama kayıtları (Önizleme)](#tab/app-reg-preview/)

1. **Uygulama kayıtları (Önizleme)** öğesini seçin ve ardından **Yeni kayıt**' ı seçin.
1. **Ad**için `IdentityExperienceFramework`girin.
1. **Desteklenen hesap türleri**altında **yalnızca bu kuruluş dizinindeki hesaplar**' ı seçin.
1. **Yeniden yönlendirme URI 'si**altında **Web**' i seçin ve ardından `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`girin; burada `your-tenant-name` Azure AD B2C kiracı etki alanı adıdır.
1. **İzinler**altında, *OpenID ve offline_access Permissions Için yönetici izni ver* onay kutusunu seçin.
1. **Kaydol**’u seçin.
1. Daha sonraki bir adımda kullanmak üzere **uygulama (istemci) kimliğini** kaydedin.

Ardından, bir kapsam ekleyerek API 'YI kullanıma sunun:

1. **Yönet**altında **bir API 'yi kullanıma**sunma ' yı seçin.
1. **Kapsam Ekle**' yi seçin, ardından **Kaydet** ' i SEÇIN ve varsayılan uygulama kimliği URI 'sini kabul edin.
1. Azure AD B2C kiracınızda özel ilke yürütmeye izin veren bir kapsam oluşturmak için aşağıdaki değerleri girin:
    * **Kapsam adı**: `user_impersonation`
    * **Yönetici onayı görünen adı**: `Access IdentityExperienceFramework`
    * **Yönetici onay açıklaması**: `Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. **Kapsam Ekle** ' yi seçin

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>ProxyIdentityExperienceFramework uygulamasını kaydetme

#### <a name="applicationstabapplications"></a>[Uygulamalar](#tab/applications/)

1. **Uygulama kayıtları (eski)** bölümünde **Yeni uygulama kaydı**' nı seçin.
1. **Ad**için `ProxyIdentityExperienceFramework`girin.
1. **Uygulama türü**için **Yerel**' i seçin.
1. **Yeniden yönlendirme URI 'si**için, `your-tenant-name` Azure AD B2C kiracınız `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`girin.
1. **Oluştur**'u seçin. Oluşturulduktan sonra uygulama KIMLIĞINI kopyalayın ve daha sonra kullanmak üzere kaydedin.
1. **Ayarlar**' ı ve ardından **gerekli izinler**' i seçin ve ardından **Ekle**' yi seçin.
1. **BIR API seçin**' i seçin, **IdentityExperienceFramework**' yi arayıp seçin ve ardından **Seç**' e tıklayın.
1. **Erişim IdentityExperienceFramework**' nın yanındaki onay kutusunu Işaretleyin, **Seç**' e ve ardından **bitti**' ye tıklayın.
1. **Izin ver**' i seçin ve ardından **Evet**' i seçerek onaylayın.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Uygulama kayıtları (Önizleme)](#tab/app-reg-preview/)

1. **Uygulama kayıtları (Önizleme)** öğesini seçin ve ardından **Yeni kayıt**' ı seçin.
1. **Ad**için `ProxyIdentityExperienceFramework`girin.
1. **Desteklenen hesap türleri**altında **yalnızca bu kuruluş dizinindeki hesaplar**' ı seçin.
1. **Yeniden yönlendirme URI 'si**altında, **genel istemci/yerel ' i (mobil & Masaüstü)** seçmek için açılan eklentiyi kullanın.
1. **Yeniden yönlendirme URI 'si**için, `your-tenant-name` Azure AD B2C kiracınız `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`girin.
1. **İzinler**altında, *OpenID ve offline_access Permissions Için yönetici izni ver* onay kutusunu seçin.
1. **Kaydol**’u seçin.
1. Daha sonraki bir adımda kullanmak üzere **uygulama (istemci) kimliğini** kaydedin.

Sonra, uygulamanın ortak istemci olarak değerlendirilip değerlendirilmeyeceğini belirtin:

1. **Yönet**altında **kimlik doğrulaması**' nı seçin.
1. **Yeni deneyimi deneyin** (gösteriliyorsa) seçeneğini belirleyin.
1. **Gelişmiş ayarlar**altında, **uygulamayı ortak istemci olarak değerlendir** ' i etkinleştirin ( **Evet**' i seçin).
1. **Kaydet**’i seçin.

Şimdi *IdentityExperienceFramework* kaydında daha önce sunulan API kapsamına izin verin:

1. **Yönet**altında **API izinleri**' ni seçin.
1. **Yapılandırılan izinler**altında **izin Ekle**' yi seçin.
1. **API 'Lerim** sekmesini seçin, sonra **IdentityExperienceFramework** uygulamasını seçin.
1. **İzin**altında, daha önce tanımladığınız **user_impersonation** kapsamını seçin.
1. **Izin Ekle**' yi seçin. Yönlendirildiğinden, bir sonraki adıma geçmeden önce birkaç dakika bekleyin.
1. **Yönetici onayı ver ' i (kiracı adınız)** seçin.
1. Şu anda oturum açmış olan yönetici hesabınızı seçin veya Azure AD B2C kiracınızda, en azından *bulut uygulama Yöneticisi* rolüne atanan bir hesapla oturum açın.
1. **Kabul Et**’i seçin.
1. **Yenile**' yi seçin ve ardından "verilen..." öğesini doğrulayın Her iki kapsam için **durum** altında görünür. İzinlerin yayılması birkaç dakika sürebilir.

* * *

## <a name="custom-policy-starter-pack"></a>Özel ilke başlangıç paketi

Özel ilkeler, teknik profiller ve Kullanıcı ilerliklerini tanımlamak için Azure AD B2C kiracınıza yüklediğiniz XML dosyaları kümesidir. Hızlı bir şekilde çalışmaya başlamanızı sağlamak için önceden oluşturulmuş birkaç ilke ile başlangıç paketleri sunuyoruz. Bu başlangıç paketlerinin her biri, açıklanan senaryolara ulaşmak için gereken en az teknik profil ve Kullanıcı bağlantısı sayısını içerir:

- **LocalAccounts** -yalnızca yerel hesapların kullanılmasına izin verir.
- **Socialaccounts** -yalnızca sosyal (veya federal) hesapların kullanılmasını mümkün.
- **SocialAndLocalAccounts** -hem yerel hem de sosyal hesapların kullanımını mümkün.
- **SocialAndLocalAccountsWithMFA** -sosyal, yerel ve Multi-Factor Authentication seçeneklerini sunar.

Her bir başlatıcı paketi şunları içerir:

- **Temel dosya** -temel için birkaç değişiklik yapılması gerekir. Örnek: *TrustFrameworkBase. xml*
- **Uzantı dosyası** -bu dosya, çoğu yapılandırma değişikliğini yapılmıştır. Örnek: *TrustFrameworkExtensions. xml*
- **Bağlı olan taraf dosyaları** -uygulamanız tarafından çağrılan göreve özel dosyalar. Örnekler: *Signuporsignın. xml*, *profileedit. xml*, *passwordreset. xml*

Bu makalede, **SocialAndLocalAccounts** Starter paketindeki XML özel ilke dosyalarını düzenlersiniz. Bir XML düzenleyiciye ihtiyacınız varsa, hafif bir platformlar arası düzenleyici [Visual Studio Code](https://code.visualstudio.com/download)deneyin.

### <a name="get-the-starter-pack"></a>Başlangıç paketini al

GitHub 'dan özel ilke başlangıç paketlerini alın, sonra SocialAndLocalAccounts Starter paketindeki XML dosyalarını Azure AD B2C kiracı adınızla güncelleştirin.

1. [. Zip dosyasını indirin](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) veya depoyu kopyalayın:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. **SocialAndLocalAccounts** dizinindeki tüm dosyalar içinde `yourtenant` dize, Azure AD B2C kiracınızın adıyla değiştirin.

    Örneğin, B2C kiracınızın adı *contosotenant*ise, tüm `yourtenant.onmicrosoft.com` örnekleri `contosotenant.onmicrosoft.com`olur.

### <a name="add-application-ids-to-the-custom-policy"></a>Özel ilkeye uygulama kimlikleri ekleme

Uygulama kimliklerini *TrustFrameworkExtensions. xml*uzantı dosyasına ekleyin.

1. `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** açın ve `<TechnicalProfile Id="login-NonInteractive">`öğe bulun.
1. `IdentityExperienceFrameworkAppId` örneklerini, daha önce oluşturduğunuz IdentityExperienceFramework uygulamasının uygulama KIMLIĞIYLE değiştirin.
1. `ProxyIdentityExperienceFrameworkAppId` örneklerini, daha önce oluşturduğunuz ProxyIdentityExperienceFramework uygulamasının uygulama KIMLIĞIYLE değiştirin.
1. Dosyayı kaydedin.

## <a name="upload-the-policies"></a>İlkeleri karşıya yükle

1. Azure portal B2C kiracınızda **kimlik deneyimi çerçevesi** menü öğesini seçin.
1. **Özel Ilkeyi karşıya yükle**' yi seçin.
1. Bu sırada, ilke dosyalarını karşıya yükleyin:
    1. *TrustFrameworkBase. xml*
    1. *TrustFrameworkExtensions. xml*
    1. *Signuporsignın. xml*
    1. *ProfileEdit. xml*
    1. *PasswordReset. xml*

Dosyaları karşıya yüklerken, Azure her birine `B2C_1A_` ön eki ekler.

> [!TIP]
> XML düzenleyiciniz doğrulamayı destekliyorsa, dosyaları başlangıç paketinin kök dizininde bulunan `TrustFrameworkPolicy_0.3.0.0.xsd` XML şemasına göre doğrulayın. XML şeması doğrulaması karşıya yüklemeden önce hataları tanımlar.

## <a name="test-the-custom-policy"></a>Özel ilkeyi test etme

1. **Özel ilkeler**altında **B2C_1A_signup_signin**öğesini seçin.
1. Özel ilkenin genel bakış sayfasında **Uygulama Seç** için, daha önce kaydettiğiniz *WebApp1* adlı Web uygulamasını seçin.
1. **Yanıt URL 'sinin** `https://jwt.ms`olduğundan emin olun.
1. **Şimdi Çalıştır**' ı seçin.
1. Bir e-posta adresi kullanarak kaydolun.
1. **Şimdi Çalıştır** ' ı seçin.
1. Doğru yapılandırmaya sahip olmadığınızı onaylamak için aynı hesapla oturum açın.

## <a name="add-facebook-as-an-identity-provider"></a>Bir kimlik sağlayıcısı olarak Facebook ekleme

1. `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** dosyasında, `client_id` değerini Facebook uygulama kimliğiyle değiştirin:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. *TrustFrameworkExtensions. xml* dosyasını kiracınıza yükleyin.
1. **Özel ilkeler**altında **B2C_1A_signup_signin**öğesini seçin.
1. **Şimdi Çalıştır** ' ı seçin ve Facebook ile oturum açmak ve özel ilkeyi test etmek için Facebook ' u seçin.

## <a name="next-steps"></a>Sonraki adımlar

Ardından, kimlik sağlayıcısı olarak Azure Active Directory (Azure AD) eklemeyi deneyin. Bu başlangıç kılavuzunda kullanılan temel dosya, Azure AD gibi diğer kimlik sağlayıcılarını eklemek için gereken bazı içerikleri zaten içeriyor.

Azure AD 'yi ve kimlik sağlayıcısını ayarlama hakkında daha fazla bilgi için bkz. [Active Directory B2C özel ilkeler kullanarak kayıt ayarlama ve Azure Active Directory hesabıyla oturum açma](active-directory-b2c-setup-aad-custom.md).
