---
title: Yerel hesap kimlik sağlayıcısını Azure AD B2C ayarlama
titleSuffix: Azure AD B2C
description: Kimlik türlerini, Azure Active Directory B2C kiracınızda kaydolma veya oturum açma (e-posta, Kullanıcı adı, telefon numarası) kullanacak şekilde tanımlayın.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/19/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d9eb28ad19d53df542769a89b839668bbb205e30
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256115"
---
# <a name="set-up-the-local-account-identity-provider"></a>Yerel hesap kimlik sağlayıcısını ayarlama

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure AD B2C kullanıcıların kimliğini doğrulayabileceği çeşitli yollar sağlar. Kullanıcılar Kullanıcı adı ve parola, telefon doğrulaması (parola daha az kimlik doğrulama olarak da bilinir) veya sosyal kimlik sağlayıcıları kullanarak yerel bir hesapta oturum açabilirler. E-posta kaydı, varsayılan olarak yerel hesap kimliği sağlayıcısı ayarlarınızda etkindir. 

Bu makalede, kullanıcıların hesaplarının bu Azure AD B2C kiracısında yerel olarak nasıl oluşturulduğu açıklanmaktadır. Kullanıcı kimliğinin Facebook ve Google gibi bir federal kimlik sağlayıcısı tarafından yönetildiği sosyal veya kuruluş kimlikleri için bkz. [kimlik sağlayıcısı ekleme](add-identity-provider.md).

## <a name="email-sign-in"></a>E-posta oturumu açma

E-posta seçeneğiyle, kullanıcılar e-posta adresi ve parolasıyla oturum açabilir:

- **Oturum açma**, kullanıcılardan e-posta ve parola girmesi istenir.
- **Kaydolma**, kullanıcılardan kaydolma sırasında doğrulanacak (isteğe bağlı) bir e-posta adresi istenir ve oturum açma kimliği olur. Kullanıcı daha sonra kaydolma sayfasında istenen diğer bilgileri, örneğin görünen adı, verilen adı ve soyadı girer. Ardından, hesabı oluşturmak için devam ' ı seçin.
- **Parola sıfırlama**, kullanıcıların e-postalarını girmesi ve doğrulaması gerekir, kullanıcının parolayı sıfırlayabileceği

![E-posta kaydolma veya oturum açma deneyimi](./media/identity-provider-local/local-account-email-experience.png)

## <a name="username-sign-in"></a>Kullanıcı adı oturum açma

Kullanıcı seçeneği ile kullanıcılar bir Kullanıcı adı ve parolayla oturum açabilir:

- **Oturum açma**: kullanıcılardan Kullanıcı adı ve parola girmesi istenir.
- **Kaydolma**: kullanıcılardan, oturum açma kimlikleri olacak şekilde Kullanıcı adı istenir. Kullanıcılara, kayıt sırasında doğrulanacak bir e-posta adresi sorulur. E-posta adresi, parola sıfırlama akışı sırasında kullanılacaktır. Kullanıcı kaydolma sayfasında istenen diğer bilgileri, örneğin görünen adı, verilen adı ve soyadı girer. Kullanıcı daha sonra hesabı oluşturmak için devam ' ı seçer.
- **Parola sıfırlama**: kullanıcıların kullanıcı adını ve ilişkili e-posta adresini girmesi gerekir. E-posta adresinin doğrulanması ve sonra kullanıcının parolayı sıfırlayabileceği anlamına gelmelidir.

![Kullanıcı adı kaydolma veya oturum açma deneyimi](./media/identity-provider-local/local-account-username-experience.png)

## <a name="phone-sign-in-preview"></a>Telefonla oturum açma (Önizleme)

Passwordless kimlik doğrulaması, bir kullanıcının parolalarıyla oturum açması gerekmeyen bir kimlik doğrulama türüdür. Telefon kayıt ve oturum açma ile Kullanıcı, birincil oturum açma tanımlayıcısı olarak bir telefon numarası kullanarak uygulamaya kaydolabilir. Kullanıcı, kaydolma ve oturum açma sırasında aşağıdaki deneyimle sahip olacaktır:

- **Oturum açma**: kullanıcının kimlik numarası olarak telefon numarası olan mevcut bir hesabı varsa, Kullanıcı telefon numarasını girer ve *oturum aç*' ı seçer. *Devam*' i seçerek ülke ve telefon numarasını onaylarlar ve telefonlarına bir kerelik doğrulama kodu gönderilir. Kullanıcı doğrulama kodunu girer ve oturum açmak için *devam* ' i seçer.
- **Kaydolma**: kullanıcının uygulamanız için zaten bir hesabı yoksa, *Şimdi kaydol* bağlantısına tıklayarak bir tane oluşturabilirsiniz. 
    1. Kullanıcının *ülkesini* seçtiği, telefon numarasını girdiği ve *kodu gönder* seçtiği bir kaydolma sayfası görüntülenir. 
    1. Kullanıcının telefon numarasına bir kerelik doğrulama kodu gönderilir. Kullanıcı *doğrulama kodunu* kaydolma sayfasına girer ve *kodu doğrula*' yı seçer. (Kullanıcı kodu alamadıysanız, *Yeni kod gönder*' i seçebilir). 
    1. Kullanıcı kaydolma sayfasında istenen diğer bilgileri, örneğin görünen adı, verilen adı ve soyadı girer. Daha sonra Devam seçeneğini belirleyin.
    1. Ardından, kullanıcıdan bir **Kurtarma e-postası** sağlaması istenir. Kullanıcı e-posta adresini girip, *doğrulama kodu gönder*' i seçer. Bir kod, kullanıcının e-posta gelen kutusuna gönderilir ve bu kimlik doğrulama kodu kutusuna girebilirler. Ardından Kullanıcı doğrulama kodunu seçer.
    1. Kod doğrulandıktan sonra Kullanıcı, hesabını oluşturmak için *Oluştur* ' u seçer. 

![Telefon kaydolma veya oturum açma deneyimi](./media/identity-provider-local/local-account-phone-experience.png)

### <a name="pricing"></a>Fiyatlandırma

Bir kerelik parolalar, SMS metin iletileri kullanılarak kullanıcılarınıza gönderilir. Mobil ağ operatörünüze bağlı olarak, gönderilen her ileti için ücret ödemeniz gerekebilir. Fiyatlandırma bilgileri için [Azure Active Directory B2C fiyatlandırmasının](https://azure.microsoft.com/pricing/details/active-directory-b2c/) **ayrı ücretler** bölümüne bakın.

> [!NOTE]
> Telefon kaydı ile bir Kullanıcı akışı yapılandırdığınızda, Multi-Factor Authentication (MFA) varsayılan olarak devre dışıdır. Kullanıcı akışlarında MFA 'yı telefon kayıt ile etkinleştirebilirsiniz, ancak birincil tanımlayıcı olarak bir telefon numarası kullanıldığından, tek seferlik geçiş kodu ikinci kimlik doğrulama faktörü için kullanılabilen tek seçenektir.

### <a name="phone-recovery"></a>Telefon kurtarma

Telefon kaydı ve Kullanıcı akışlarınız için oturum açma etkinleştirdiğinizde, kurtarma e-posta özelliğini etkinleştirmek de iyi bir fikirdir. Bu özellikle, bir Kullanıcı, telefonlarını olmadığında hesabını kurtarmak için kullanılabilecek bir e-posta adresi sağlayabilir. Bu e-posta adresi yalnızca hesap kurtarma için kullanılır. Oturum açmak için kullanılamaz.

- Kurtarma e-postası istemi **Açık** olduğunda, ilk kez kaydolan bir kullanıcının bir yedekleme e-postasını doğrulaması istenir. Daha önce bir kurtarma e-postası sağlanmayan bir kullanıcının, bir sonraki oturum açma işlemi sırasında bir yedekleme e-postasını doğrulaması istenmez.

- Kurtarma e-postası **kapalıyken**, oturum açan veya oturum açan bir Kullanıcı, kurtarma e-postası istemi gösterilmez.
 
Aşağıdaki ekran görüntülerinde telefon kurtarma akışı gösterilmektedir:

![Telefon kurtarma Kullanıcı akışı](./media/identity-provider-local/local-account-change-phone-flow.png)


## <a name="phone-or-email-sign-in-preview"></a>Telefon veya e-posta oturum açma (Önizleme)

[Telefonla oturum](#phone-sign-in-preview)açmayı ve [e-posta oturumunu](#email-sign-in)birleştirmeyi seçebilirsiniz. Kaydolma veya oturum açma sayfasında, Kullanıcı bir telefon numarası veya e-posta adresi yazabilir. Kullanıcı girişine bağlı olarak, Azure AD B2C kullanıcıyı ilgili akışa götürür. 

![Telefon veya e-posta kaydolma veya oturum açma deneyimi](./media/identity-provider-local/local-account-phone-and-email-experience.png)

::: zone pivot="b2c-user-flow"

## <a name="configure-local-account-identity-provider-settings"></a>Yerel hesap kimlik sağlayıcısı ayarlarını yapılandırma

Sağlayıcıları (e-posta, Kullanıcı adı veya telefon numarası) etkinleştirerek veya devre dışı bırakarak bir Kullanıcı akışında kullanılmak üzere kullanılabilir yerel kimlik sağlayıcılarını yapılandırabilirsiniz.  Kiracı düzeyinde etkinleştirilmiş birden fazla yerel kimlik sağlayıcısı olabilir.

Bir Kullanıcı akışı, yalnızca herhangi bir anda yerel hesap kimlik sağlayıcılarından birini kullanacak şekilde yapılandırılabilir. Kiracı düzeyinde birden fazla etkinleştirildiyse, her Kullanıcı akışı farklı bir yerel hesap kimliği sağlayıcısı kümesine sahip olabilir.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini seçerek ve Azure AD kiracınızı içeren dizini seçerek Azure AD B2C kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **Azure AD B2C**' i arayıp seçin.
1. **Yönet** altında **kimlik sağlayıcıları**' nı seçin.
1. Kimlik sağlayıcısı listesinde **yerel hesap**' ı seçin.
1. **Yerel IDP 'Yi Yapılandır** sayfasında, kullanıcılarınızın Azure AD B2C kiracınızda kendi yerel hesaplarını oluşturmak için kullanabileceği, izin verilen kimlik türlerinden en az birini seçmiş olmanız gerekir.
1. **Kaydet**’i seçin.

## <a name="configure-your-user-flow"></a>Kullanıcı akışınızı yapılandırma

1. Azure portal sol menüsünde **Azure AD B2C**' i seçin.
1. **İlkeler** altında **Kullanıcı akışları ' nı (ilkeler)** seçin.
1. Kaydolma ve oturum açma deneyimini yapılandırmak istediğiniz kullanıcı akışını seçin.
1. **Kimlik sağlayıcılarını** seçin
1. **Yerel hesaplar** altında aşağıdakilerden birini seçin: **e-posta kaydı**, **Kullanıcı kimliği kaydolma**, **telefon kaydı**, **telefon/e-posta kaydı** veya **yok**.

### <a name="enable-the-recovery-email-prompt"></a>Kurtarma e-posta istemi 'ni etkinleştir

**Telefon kaydolma**, **telefon/e-posta kaydolma** seçeneğini belirlerseniz, kurtarma e-posta İstemi ' ni etkinleştirin.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Azure portal, araması yapın ve **Azure AD B2C** seçin.
1. Azure AD B2C, **ilkeler** altında **Kullanıcı akışları**' nı seçin.
1. Listeden Kullanıcı akışını seçin.
1. **Ayarlar** bölümünde **Özellikler**’i seçin.
1. **Telefon numarası kaydolma ve oturum açma (Önizleme) için kurtarma e-postası Isteğini etkinleştir ' in** yanındaki şunları seçin:
   - Hem oturum açma hem de oturum açma sırasında kurtarma e-posta uyarısını göstermek için **üzerinde** .
   - **Kapatın** ve kurtarma e-posta istemi 'ni gizleyin.
1. **Kaydet**’i seçin.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="get-the-starter-pack"></a>Başlangıç paketini al

Özel ilkeler, Kullanıcı, neys tanımlamak için Azure AD B2C kiracınıza yüklediğiniz XML dosyaları kümesidir. Önceden oluşturulmuş birkaç ilke ile başlangıç paketleri sağlıyoruz. İlgili başlangıç paketini indirin: 

- [E-posta oturumu açma](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts)
- [Kullanıcı adı oturum açma](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-signup-or-signin)
- [Telefonla oturum açma](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless). [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhone.xml) bağlı olan taraf ilkesini seçin. 
- [Telefon veya e-posta oturum açma](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless). [SignUpOrSignInWithPhone.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/scenarios/phone-number-passwordless/SignUpOrSignInWithPhoneOrEmail.xml) bağlı olan taraf ilkesini seçin.

Başlangıç paketini indirdikten sonra.

1. Her dosyada, dizeyi `yourtenant` Azure AD B2C kiracınızın adıyla değiştirin. Örneğin, B2C kiracınızın adı *contosob2c* ise, tüm örnekleri `yourtenant.onmicrosoft.com` olur `contosob2c.onmicrosoft.com` .

1. [Azure Active Directory B2C özel ilkeleri kullanmaya başlama](tutorial-create-user-flows.md?pivots=b2c-custom-policy)konusunun [özel Ilkeye uygulama kimlikleri ekleme](tutorial-create-user-flows.md?pivots=b2c-custom-policy#add-application-ids-to-the-custom-policy) bölümündeki adımları uygulayın. Örneğin, `/phone-number-passwordless/` **`Phone_Email_Base.xml`** *IdentityExperienceFramework* ve *ProxyIdentityExperienceFramework* önkoşullarını tamamlarken kaydettiğiniz Iki uygulamanın **uygulama (istemci) kimlikleriyle** güncelleştirin.
1. İlke dosyalarını karşıya yükle

::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

- [Dış kimlik sağlayıcıları Ekle](add-identity-provider.md)
- [Kullanıcı akışı oluşturma](tutorial-create-user-flows.md)
