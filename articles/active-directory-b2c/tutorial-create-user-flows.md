---
title: Öğretici-Kullanıcı akışları ve özel ilkeler oluşturma-Azure Active Directory B2C
description: Kaydolma, oturum açma ve Azure Active Directory B2C uygulamalarınız için Kullanıcı profili düzenlemesini etkinleştirmek üzere Azure portal ' de Kullanıcı akışları ve özel ilkeler oluşturma hakkında bilgi edinmek için bu öğreticiyi izleyin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e41c1e74dbe428ee38d4480a1587050b7f96a55f
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226235"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Öğretici: Azure Active Directory B2C Kullanıcı akışları oluşturma

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Uygulamalarınızda, kullanıcıların kaydolmalarına, oturum açmalarına veya profillerini yönetmesine olanak tanıyan Kullanıcı akışlarına sahip olabilirsiniz. Azure Active Directory B2C (Azure AD B2C) kiracınızda farklı türlerde birden çok Kullanıcı akışı oluşturabilir ve bunları gerektiği şekilde uygulamalarınızda kullanabilirsiniz. Kullanıcı akışları, uygulamalar arasında yeniden kullanılabilir.

::: zone pivot="b2c-user-flow"
Bir Kullanıcı akışı, kullanıcıların oturum açma, kaydolma, profil düzenleme veya bir parolayı sıfırlama gibi şeyler yaparken uygulamanızla nasıl etkileşime gireceğini belirlemenizi sağlar. Bu makalede şunları öğreneceksiniz:
::: zone-end

::: zone pivot="b2c-custom-policy"
[Özel ilkeler](custom-policy-overview.md) , Azure Active Directory B2C (Azure AD B2C) kiracınızın davranışını tanımlayan yapılandırma dosyalarıdır. Bu makalede şunları öğreneceksiniz:
::: zone-end

> [!div class="checklist"]
> * Kaydolma ve oturum açma Kullanıcı akışı oluşturma
> * Kendi kendine parola sıfırlamayı etkinleştirme
> * Profil düzenlemesi Kullanıcı akışı oluşturma

::: zone pivot="b2c-user-flow"
> [!IMPORTANT]
> Kullanıcı akışı sürümlerine başvuru şeklimizi değiştirdik. Daha önce V1 (üretime hazır) sürümleriyle V1.1 ve V2 (önizleme) sürümlerini sunduk. Artık Kullanıcı akışlarını **Önerilen** (yeni nesil Önizleme) ve **Standart** (genel kullanıma açık) sürümler halinde birleştiriyoruz. Tüm V 1.1 ve v2 eski Preview Kullanıcı akışları **, 1 ağustos 2021 '** e kadar kullanımdan kalkmaya yönelik bir yoldur. Ayrıntılar için bkz. [Azure AD B2C Içindeki Kullanıcı akışı sürümleri](user-flow-versions.md).
::: zone-end

## <a name="prerequisites"></a>Önkoşullar

::: zone pivot="b2c-user-flow"
- Henüz bir tane yoksa, Azure aboneliğinize bağlı [bir Azure AD B2C kiracı oluşturun](tutorial-create-tenant.md) .
- [Uygulamanızı](tutorial-register-applications.md) , Azure AD B2C ile iletişim kurabilmesi için oluşturduğunuz kiracıya kaydedin.
::: zone-end

::: zone pivot="b2c-custom-policy"
- Henüz bir tane yoksa, Azure aboneliğinize bağlı [bir Azure AD B2C kiracı oluşturun](tutorial-create-tenant.md) .
- [Uygulamanızı](tutorial-register-applications.md) , Azure AD B2C ile iletişim kurabilmesi için oluşturduğunuz kiracıya kaydedin.
- Facebook uygulamasını yapılandırmak için [kayıt ayarlama ve Facebook hesabıyla oturum açma](identity-provider-facebook.md) adımlarını izleyin. Özel ilkeleri kullanmak için Facebook uygulaması gerekli olmasa da, özel bir ilkede sosyal oturum açmayı etkinleştirmeyi göstermek için bu izlenecek yolda kullanılır.
::: zone-end

::: zone pivot="b2c-user-flow"
## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Kaydolma ve oturum açma Kullanıcı akışı oluşturma

Kaydolma ve oturum açma Kullanıcı akışı, hem kayıt hem de oturum açma deneyimlerini tek bir yapılandırmayla işler. Uygulamanızın kullanıcıları, bağlama göre doğru yolun altına alınır.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.

    ![B2C kiracısı, dizin ve abonelik bölmesi, Azure portal](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. Azure portal, araması yapın ve **Azure AD B2C** seçin.
1. **İlkeler** altında **Kullanıcı akışları**' nı seçin ve ardından **Yeni Kullanıcı akışı**' nı seçin.

    ![Yeni Kullanıcı akışı düğmesi vurgulanmış şekilde portalda Kullanıcı akışları sayfası](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. **Kullanıcı akışı oluştur** sayfasında, **oturum aç ve kullanıcı akışını oturum aç** ' ı seçin.

    ![Kaydolma ve oturum açma akışı vurgulanmış bir Kullanıcı akış sayfası seçin](./media/tutorial-create-user-flows/select-user-flow-type.png)

1. **Sürüm seçin** altında **Önerilen**' i seçin ve ardından **Oluştur**' u seçin. (Kullanıcı akışı sürümleri hakkında[daha fazla bilgi edinin](user-flow-versions.md) .)

    ![Azure portal ' de vurgulanan özelliklerle Kullanıcı akışı oluşturma sayfası](./media/tutorial-create-user-flows/select-version.png)

1. Kullanıcı akışı için bir **ad** girin. Örneğin, *signupsignin1*.
1. **Kimlik sağlayıcıları** Için **e-posta kaydı**' nı seçin.
1. **Kullanıcı öznitelikleri ve talepler** için, kayıt sırasında kullanıcıdan toplamak ve göndermek istediğiniz talepleri ve öznitelikleri seçin. Örneğin, **daha fazla göster**' i seçin ve ardından **ülke/bölge**, **görünen ad** ve **posta kodu** için öznitelikler ve talepler ' i seçin. **Tamam**'a tıklayın.

    ![Üç talep seçili olan öznitelikler ve talepler seçim sayfası](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Kullanıcı akışını eklemek için **Oluştur** ' a tıklayın. *B2C_1* bir ön eki otomatik olarak ada eklenir.

### <a name="test-the-user-flow"></a>Kullanıcı akışını test etme

1. Genel Bakış sayfasını açmak için oluşturduğunuz kullanıcı akışını seçin ve ardından **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama** için, daha önce kaydettiğiniz *WebApp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Kullanıcı akışını Çalıştır**' a tıklayın ve ardından **Şimdi kaydolun**' ı seçin.

    ![Kullanıcı akışını Çalıştır düğmesi vurgulanmış şekilde portalda Kullanıcı akış sayfasını Çalıştır](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Geçerli bir e-posta adresi girin, **doğrulama kodu gönder**' e tıklayın, aldığınız doğrulama kodunu girin ve **kodu doğrula**' yı seçin.
1. Yeni bir parola girin ve parolayı onaylayın.
1. Ülkenizi ve bölgenizi seçin, görüntülenmesini istediğiniz adı girin, bir posta kodu girin ve ardından **Oluştur**' a tıklayın. Belirteç öğesine döner `https://jwt.ms` ve size gösterilmesi gerekir.
1. Artık kullanıcı akışını yeniden çalıştırabilirsiniz ve oluşturduğunuz hesapla oturum açabilmelisiniz. Döndürülen belirteç, ülke/bölge, ad ve posta kodu ' nu seçtiğiniz talepleri içerir.

> [!NOTE]
> "Kullanıcı akışını Çalıştır" deneyimi şu anda yetkilendirme kodu akışını kullanan SPA yanıt URL 'SI türüyle uyumlu değil. "Kullanıcı akışını Çalıştır" deneyimini bu tür uygulamalarla kullanmak için, "Web" türünde bir yanıt URL 'SI kaydedin ve [burada](tutorial-register-spa.md)açıklandığı gibi örtük akışı etkinleştirin.

## <a name="enable-self-service-password-reset"></a>Kendi kendine parola sıfırlamayı etkinleştirme

Kaydolma veya oturum açma Kullanıcı akışı için [self servis parola sıfırlamayı](add-password-reset-policy.md) etkinleştirmek için:

1. Oluşturduğunuz kaydolma veya oturum açma kullanıcı akışını seçin.
1. Sol menüdeki **Ayarlar** ' ın altında **Özellikler**' i seçin.
1. **Parola karmaşıklığı** bölümünde **self servis parola sıfırlama**' yı seçin.
1. **Kaydet**’i seçin.

### <a name="test-the-user-flow"></a>Kullanıcı akışını test etme

1. Genel Bakış sayfasını açmak için oluşturduğunuz kullanıcı akışını seçin ve ardından **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama** için, daha önce kaydettiğiniz *WebApp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Kullanıcı akışını Çalıştır**' ı seçin.
1. Kaydolma veya oturum açma sayfasından **parolanızı unuttum?** seçeneğini belirleyin.
1. Daha önce oluşturduğunuz hesabın e-posta adresini doğrulayın ve sonra **devam**' ı seçin.
1. Artık Kullanıcı parolasını değiştirme fırsatına sahipsiniz. Parolayı değiştirin ve **devam**' ı seçin. Belirteç öğesine döner `https://jwt.ms` ve size gösterilmesi gerekir.

## <a name="create-a-profile-editing-user-flow"></a>Profil düzenlemesi Kullanıcı akışı oluşturma

Kullanıcıların uygulamanızdaki profilini düzenlemesini etkinleştirmek istiyorsanız, bir profil düzenleme Kullanıcı akışı kullanın.

1. Azure AD B2C kiracının Genel Bakış sayfasının menüsünde **Kullanıcı akışları**' nı seçin ve ardından **Yeni Kullanıcı akışı**' nı seçin.
1. **Kullanıcı akışı oluştur** sayfasında **profil düzenlemesi** Kullanıcı akışı ' nı seçin. 
1. **Sürüm seçin** altında **Önerilen**' i seçin ve ardından **Oluştur**' u seçin.
1. Kullanıcı akışı için bir **ad** girin. Örneğin, *profileediting1*.
1. **Kimlik sağlayıcıları** Için **yerel hesap oturumu açma**' yı seçin.
2. **Kullanıcı öznitelikleri** için, müşterinin profilinde düzenleyebilmesini istediğiniz öznitelikleri seçin. Örneğin, **daha fazla göster**' i seçin ve **görünen ad** ve **iş unvanı** için her iki özniteliği ve talebi seçin. **Tamam**'a tıklayın.
3. Kullanıcı akışını eklemek için **Oluştur** ' a tıklayın. *B2C_1* bir ön eki otomatik olarak ada eklenir.

### <a name="test-the-user-flow"></a>Kullanıcı akışını test etme

1. Genel Bakış sayfasını açmak için oluşturduğunuz kullanıcı akışını seçin ve ardından **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama** için, daha önce kaydettiğiniz *WebApp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Kullanıcı akışını Çalıştır**' a tıklayın ve daha önce oluşturduğunuz hesapla oturum açın.
1. Artık Kullanıcı için görünen adı ve iş başlığını değiştirme fırsatına sahipsiniz. **Devam**’a tıklayın. Belirteç öğesine döner `https://jwt.ms` ve size gösterilmesi gerekir.
::: zone-end

::: zone pivot="b2c-custom-policy"
> [!TIP]
> Bu makalede, kiracınızı el ile ayarlama açıklanmaktadır. İşlemin tamamını bu makaleden otomatikleştirebilirsiniz. Otomatik hale getirmek, kaydolma ve oturum açma, parola sıfırlama ve profil düzenleme ile bağlantı sağlayan Azure AD B2C [SocialAndLocalAccountsWithMFA başlangıç paketini](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)dağıtır. Aşağıdaki izlenecek yolu otomatik hale getirmek için [IEF kurulum uygulamasını](https://aka.ms/iefsetup) ziyaret edin ve yönergeleri izleyin.


## <a name="add-signing-and-encryption-keys"></a>İmzalama ve şifreleme anahtarları Ekle

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Azure portal, araması yapın ve **Azure AD B2C** seçin.
1. Genel Bakış sayfasında, **ilkeler** altında **kimlik deneyimi çerçevesi**' ni seçin.

### <a name="create-the-signing-key"></a>İmzalama anahtarını oluşturma

1. **Ilke anahtarlarını** seçin ve ardından **Ekle**' yi seçin.
1. **Seçenekler** için öğesini seçin `Generate` .
1. **Ad** alanına girin `TokenSigningKeyContainer` . Ön ek `B2C_1A_` otomatik olarak eklenebilir.
1. **Anahtar türü** için **RSA**' yı seçin.
1. **Anahtar kullanımı** için **imza**' yı seçin.
1. **Oluştur**’u seçin.

### <a name="create-the-encryption-key"></a>Şifreleme anahtarı oluşturma

1. **Ilke anahtarlarını** seçin ve ardından **Ekle**' yi seçin.
1. **Seçenekler** için öğesini seçin `Generate` .
1. **Ad** alanına girin `TokenEncryptionKeyContainer` . _ Ön eki `B2C_1A` otomatik olarak eklenebilir.
1. **Anahtar türü** için **RSA**' yı seçin.
1. **Anahtar kullanımı** için **şifreleme**' yi seçin.
1. **Oluştur**’u seçin.

### <a name="create-the-facebook-key"></a>Facebook anahtarını oluşturma

Facebook uygulamanızın [uygulama gizli](identity-provider-facebook.md) anahtarını ilke anahtarı olarak ekleyin. Oluşturduğunuz uygulamanın uygulama gizli anahtarını, bu makalenin önkoşullarının bir parçası olarak kullanabilirsiniz.

1. **Ilke anahtarlarını** seçin ve ardından **Ekle**' yi seçin.
1. **Seçenekler** için öğesini seçin `Manual` .
1. **Ad** için girin `FacebookSecret` . Ön ek `B2C_1A_` otomatik olarak eklenebilir.
1. **Gizli** dizi bölümünde Facebook uygulamanızın *uygulama gizli* anahtarını Developers.facebook.com adresinden girin. Bu değer, uygulama KIMLIĞI değil gizli dizi.
1. **Anahtar kullanımı** için **imza**' yı seçin.
1. **Oluştur**’u seçin.

## <a name="register-identity-experience-framework-applications"></a>Kimlik deneyimi çerçevesi uygulamalarını kaydetme

Azure AD B2C, yerel hesaplarla kullanıcılara kaydolmak ve oturum açmak için kullandığı iki uygulamayı kaydetmenizi gerektirir: *IdentityExperienceFramework*, BIR Web API 'Si ve *ProxyIdentityExperienceFramework*, IdentityExperienceFramework uygulaması için temsilci izni olan yerel bir uygulama. Kullanıcılarınız bir e-posta adresi veya Kullanıcı adı ile kaydolabilir ve bir "yerel hesap" oluşturan kiracınızın kayıtlı uygulamalarına erişmek için bir parolayla kaydolabilirsiniz. Yerel hesaplar yalnızca Azure AD B2C kiracınızda bulunur.

Bu iki uygulamayı Azure AD B2C kiracınızda yalnızca bir kez kaydetmeniz gerekir.

### <a name="register-the-identityexperienceframework-application"></a>IdentityExperienceFramework uygulamasını kaydetme

Bir uygulamayı Azure AD B2C kiracınıza kaydetmek için **uygulama kayıtları** deneyimini kullanabilirsiniz.

1. **Uygulama kayıtları** öğesini seçin ve ardından **Yeni kayıt**' ı seçin.
1. **Ad** için girin `IdentityExperienceFramework` .
1. **Desteklenen hesap türleri** altında **yalnızca bu kuruluş dizinindeki hesaplar**' ı seçin.
1. **Yeniden yönlendirme URI 'si** altında **Web**' i seçin ve ardından `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com` , `your-tenant-name` Azure AD B2C kiracı etki alanı adınız yazın.
1. **İzinler** altında, *openıd ve offline_access izinleri Için yönetici izni ver* onay kutusunu seçin.
1. **Kaydet**’i seçin.
1. Daha sonraki bir adımda kullanmak üzere **uygulama (istemci) kimliğini** kaydedin.

Ardından, bir kapsam ekleyerek API 'YI kullanıma sunun:

1. Sol taraftaki menüde, **Yönet** altında, **bir API 'yi kullanıma** sunma ' yı seçin.
1. **Kapsam Ekle**' yi seçin, ardından **Kaydet** ' i SEÇIN ve varsayılan uygulama kimliği URI 'sini kabul edin.
1. Azure AD B2C kiracınızda özel ilke yürütmeye izin veren bir kapsam oluşturmak için aşağıdaki değerleri girin:
    * **Kapsam adı**: `user_impersonation`
    * **Yönetici onayı görünen adı**: `Access IdentityExperienceFramework`
    * **Yönetici onay açıklaması**: `Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. **Kapsam Ekle** ' yi seçin

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>ProxyIdentityExperienceFramework uygulamasını kaydetme

1. **Uygulama kayıtları** öğesini seçin ve ardından **Yeni kayıt**' ı seçin.
1. **Ad** için girin `ProxyIdentityExperienceFramework` .
1. **Desteklenen hesap türleri** altında **yalnızca bu kuruluş dizinindeki hesaplar**' ı seçin.
1. **Yeniden yönlendirme URI 'si** altında, **genel istemci/yerel ' i (mobil & Masaüstü)** seçmek için açılan eklentiyi kullanın.
1. **Yeniden yönlendirme URI 'si** için girin `myapp://auth` .
1. **İzinler** altında, *openıd ve offline_access izinleri Için yönetici izni ver* onay kutusunu seçin.
1. **Kaydet**’i seçin.
1. Daha sonraki bir adımda kullanmak üzere **uygulama (istemci) kimliğini** kaydedin.

Sonra, uygulamanın ortak istemci olarak değerlendirilip değerlendirilmeyeceğini belirtin:

1. Sol taraftaki menüde, **Yönet** altında **kimlik doğrulaması**' nı seçin.
1. **Gelişmiş ayarlar** altında, **ortak Istemci akışlarına izin ver** bölümünde, **aşağıdaki mobil ve Masaüstü akışlarını** **Evet** olarak ayarlayın. Uygulama bildiriminde **"Allowpublicclient": true** değerinin ayarlandığından emin olun. 
1. **Kaydet**’i seçin.

Şimdi *IdentityExperienceFramework* kaydında daha önce sunulan API kapsamına izin verin:

1. Sol taraftaki menüde, **Yönet** altında, **API izinleri**' ni seçin.
1. **Yapılandırılan izinler** altında **izin Ekle**' yi seçin.
1. **API 'Lerim** sekmesini seçin, sonra **IdentityExperienceFramework** uygulamasını seçin.
1. **İzin** altında, daha önce tanımladığınız **user_impersonation** kapsamını seçin.
1. **Izin Ekle**' yi seçin. Yönlendirildiğinden, bir sonraki adıma geçmeden önce birkaç dakika bekleyin.
1. **Yönetici onayı ver ' i (kiracı adınız)** seçin.
1. Şu anda oturum açmış olan yönetici hesabınızı seçin veya Azure AD B2C kiracınızda, en azından *bulut uygulama Yöneticisi* rolüne atanan bir hesapla oturum açın.
1. **Kabul Et**’i seçin.
1. **Yenile**' yi seçin ve ardından "verilen..." öğesini doğrulayın offline_access, OpenID ve user_impersonation kapsamların **durumu** altında görüntülenir. İzinlerin yayılması birkaç dakika sürebilir.

* * *

## <a name="custom-policy-starter-pack"></a>Özel ilke başlangıç paketi

Özel ilkeler, teknik profiller ve Kullanıcı ilerliklerini tanımlamak için Azure AD B2C kiracınıza yüklediğiniz XML dosyaları kümesidir. Hızlı bir şekilde çalışmaya başlamanızı sağlamak için önceden oluşturulmuş birkaç ilke ile başlangıç paketleri sunuyoruz. Bu başlangıç paketlerinin her biri, açıklanan senaryolara ulaşmak için gereken en az teknik profil ve Kullanıcı bağlantısı sayısını içerir:

- **LocalAccounts** -yalnızca yerel hesapların kullanılmasına izin verir.
- **Socialaccounts** -yalnızca sosyal (veya federal) hesapların kullanılmasını mümkün.
- **SocialAndLocalAccounts** -hem yerel hem de sosyal hesapların kullanımını mümkün.
- **SocialAndLocalAccountsWithMFA** -sosyal, yerel ve Multi-Factor Authentication seçeneklerini sunar.

Her bir başlatıcı paketi şunları içerir:

- **Temel dosya** -temel için birkaç değişiklik yapılması gerekir. Örnek: *TrustFrameworkBase.xml*
- **Uzantı dosyası** -bu dosya, çoğu yapılandırma değişikliğini yapılmıştır. Örnek: *TrustFrameworkExtensions.xml*
- **Bağlı olan taraf dosyaları** -uygulamanız tarafından çağrılan göreve özel dosyalar. Örnekler: *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*

Bu makalede, **SocialAndLocalAccounts** Starter paketindeki XML özel ilke dosyalarını düzenlersiniz. Bir XML düzenleyiciye ihtiyacınız varsa, hafif bir platformlar arası düzenleyici [Visual Studio Code](https://code.visualstudio.com/download)deneyin.

### <a name="get-the-starter-pack"></a>Başlangıç paketini al

GitHub 'dan özel ilke başlangıç paketlerini alın, sonra SocialAndLocalAccounts Starter paketindeki XML dosyalarını Azure AD B2C kiracı adınızla güncelleştirin.

1. [. Zip dosyasını indirin](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) veya depoyu kopyalayın:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. **SocialAndLocalAccounts** dizinindeki tüm dosyalar içinde, dizeyi `yourtenant` Azure AD B2C kiracınızın adıyla değiştirin.

    Örneğin, B2C kiracınızın adı *contosotenant* ise, tüm örnekleri `yourtenant.onmicrosoft.com` olur `contosotenant.onmicrosoft.com` .

### <a name="add-application-ids-to-the-custom-policy"></a>Özel ilkeye uygulama kimlikleri ekleme

Uygulama kimliklerini uzantı dosyasına ekleyin *TrustFrameworkExtensions.xml*.

1. Açın `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** ve öğesini bulun `<TechnicalProfile Id="login-NonInteractive">` .
1. Her iki örneğini, `IdentityExperienceFrameworkAppId` daha önce oluşturduğunuz IdentityExperienceFramework uygulamasının uygulama kimliğiyle değiştirin.
1. Her iki örneğini, `ProxyIdentityExperienceFrameworkAppId` daha önce oluşturduğunuz ProxyIdentityExperienceFramework uygulamasının uygulama kimliğiyle değiştirin.
1. Dosyayı kaydedin.

## <a name="upload-the-policies"></a>İlkeleri karşıya yükle

1. Azure portal B2C kiracınızda **kimlik deneyimi çerçevesi** menü öğesini seçin.
1. **Özel Ilkeyi karşıya yükle**' yi seçin.
1. Bu sırada, ilke dosyalarını karşıya yükleyin:
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *SignUpOrSignin.xml*
    1. *ProfileEdit.xml*
    1. *PasswordReset.xml*

Dosyaları karşıya yüklerken, Azure her birine ön ek ekler `B2C_1A_` .

> [!TIP]
> XML düzenleyiciniz doğrulamayı destekliyorsa, dosyaları `TrustFrameworkPolicy_0.3.0.0.xsd` Başlangıç paketinin kök dizininde bulunan XML şemasına göre doğrulayın. XML şeması doğrulaması karşıya yüklemeden önce hataları tanımlar.

## <a name="test-the-custom-policy"></a>Özel ilkeyi test etme

1. **Özel ilkeler** altında **B2C_1A_signup_signin**' yi seçin.
1. Özel ilkenin genel bakış sayfasında **Uygulama Seç** için, daha önce kaydettiğiniz *WebApp1* adlı Web uygulamasını seçin.
1. **Yanıt URL 'sinin** olduğundan emin olun `https://jwt.ms` .
1. **Şimdi Çalıştır**' ı seçin.
1. Bir e-posta adresi kullanarak kaydolun.
1. **Şimdi Çalıştır** ' ı seçin.
1. Doğru yapılandırmaya sahip olmadığınızı onaylamak için aynı hesapla oturum açın.

## <a name="add-facebook-as-an-identity-provider"></a>Bir kimlik sağlayıcısı olarak Facebook ekleme

[Önkoşullar](#prerequisites)bölümünde belirtildiği gibi, Facebook özel ilkeleri kullanmak için gerekli *değildir* , ancak burada, Federasyon sosyal oturum açmayı özel bir ilkede nasıl etkinleştirebileceğinizi göstermek için kullanılır.

1. `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** Dosyasında, değerini `client_id` Facebook uygulama kimliğiyle değiştirin:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. *TrustFrameworkExtensions.xml* dosyasını kiracınıza yükleyin.
1. **Özel ilkeler** altında **B2C_1A_signup_signin**' yi seçin.
1. **Şimdi Çalıştır** ' ı seçin ve Facebook ile oturum açmak ve özel ilkeyi test etmek için Facebook ' u seçin.

::: zone-end
## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, şu şekilde nasıl yapılacağını öğrendiniz:

> [!div class="checklist"]
> * Kaydolma ve oturum açma Kullanıcı akışı oluşturma
> * Profil düzenlemesi Kullanıcı akışı oluşturma
> * Parola sıfırlama Kullanıcı akışı oluşturma

Daha sonra, bir uygulamada oturum açmak ve kullanıcılara kaydolmak için Azure AD B2C kullanmayı öğrenin. Aşağıda bağlı olan ASP.NET Web uygulamasını izleyin veya **kullanıcıların kimliğini doğrulama** altındaki içindekiler tablosunda başka bir uygulamaya gidin.

> [!div class="nextstepaction"]
> [Öğretici: Azure AD B2C >kullanarak bir Web uygulamasında kimlik doğrulamasını etkinleştirme ](tutorial-web-app-dotnet.md)
