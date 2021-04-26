---
title: Oturum açma akışı ayarlama
titleSuffix: Azure Active Directory B2C
description: Azure Active Directory B2C bir oturum açma akışı ayarlamayı öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/04/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2310bd39c39036b6d6ac919517fa5539d7b70779
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104581873"
---
# <a name="set-up-a-sign-in-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C oturum açma akışı ayarlama

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-in-flow-overview"></a>Oturum açma akışına genel bakış

Oturum açma ilkesi, kullanıcıların şunları sağlar: 

* Kullanıcılar Azure AD B2C yerel hesapla oturum açabilirler
* Bir sosyal hesapla kaydolma veya oturum açma
* Parola sıfırlama
* Kullanıcılar Azure AD B2C yerel bir hesap için kaydolabilir. Bir hesap oluşturmak için yönetici [Azure Portal](manage-users-portal.md#create-a-consumer-user)veya [MS Graph API](microsoft-graph-operations.md)kullanabilir.

![Profil düzenlemesi akışı](./media/add-sign-in-policy/sign-in-user-flow.png)

## <a name="prerequisites"></a>Önkoşullar

Henüz yapmadıysanız, [bir Web uygulamasını Azure Active Directory B2C kaydedin](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-in-user-flow"></a>Oturum açma Kullanıcı akışı oluşturma

Oturum açma ilkesi eklemek için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Azure portal, araması yapın ve **Azure AD B2C** seçin.
1. **İlkeler** altında **Kullanıcı akışları**' nı seçin ve ardından **Yeni Kullanıcı akışı**' nı seçin.
1. **Kullanıcı akışı oluştur** sayfasında **oturum aç** Kullanıcı akışı ' nı seçin.
1. **Sürüm seçin** altında **Önerilen**' i seçin ve ardından **Oluştur**' u seçin. (Kullanıcı akışı sürümleri hakkında[daha fazla bilgi edinin](user-flow-versions.md) .)
1. Kullanıcı akışı için bir **ad** girin. Örneğin, *signupsignin1*.
1. **Kimlik sağlayıcıları** Için **e-posta oturum açma** seçeneğini belirleyin.
1. **Uygulama talepleri** için, uygulamanıza göndermek istediğiniz talepleri ve öznitelikleri seçin. Örneğin, **daha fazla göster**' i seçin ve ardından **görünen ad**, **ad**,  **Soyadı** ve **kullanıcının nesne kimliği** için öznitelikler ve talepler ' i seçin. **Tamam**'a tıklayın.
1. Kullanıcı akışını eklemek için **Oluştur** ' a tıklayın. *B2C_1* bir ön eki otomatik olarak ada eklenir.

### <a name="test-the-user-flow"></a>Kullanıcı akışını test etme

1. Genel Bakış sayfasını açmak için oluşturduğunuz kullanıcı akışını seçin ve ardından **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama** için, daha önce kaydettiğiniz *WebApp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Kullanıcı akışını Çalıştır**' a tıklayın.
1. Kaydolma bağlantısı olmadan oluşturduğunuz hesapla (MS Graph API kullanarak) oturum açabiliyor olmanız gerekir. Döndürülen belirteç seçtiğiniz talepleri içerir.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="remove-the-sign-up-link"></a>Kaydolma bağlantısını kaldırma

**Selfaskıted-Localaccountsignın-e-posta** teknik profili, kaydolma veya oturum açma akışı sırasında çağrılan, [kendiliğinden onaylanan](self-asserted-technical-profile.md)bir derlemedir. Kaydolma bağlantısını kaldırmak için `setting.showSignupLink` meta verileri olarak ayarlayın `false` . Uzantı dosyasındaki SelfAsserted-Localaccountsignın-email teknik profillerini geçersiz kılın. 

1. İlkenizin uzantıları dosyasını açın. Örneğin, _`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**_ .
1. Öğesini bulun `ClaimsProviders` . Öğe yoksa, ekleyin.
1. Aşağıdaki talep sağlayıcısını `ClaimsProviders` öğesine ekleyin:

    ```xml
    <!--
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Local Account</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
            <Metadata>
              <Item Key="setting.showSignupLink">false</Item>
            </Metadata>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!--
    </ClaimsProviders> -->
    ```

1. `<BuildingBlocks>`Öğesinin içinde, sürüm 1.2.0 veya daha yeni veri URI 'sine başvurmak için aşağıdaki [ContentDefinition](contentdefinitions.md) öğesini ekleyin:

    ```XML
    <!-- 
    <BuildingBlocks> 
      <ContentDefinitions>-->
        <ContentDefinition Id="api.localaccountsignup">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
        </ContentDefinition>
      <!--
      </ContentDefinitions>
    </BuildingBlocks> -->
    ```

## <a name="update-and-test-your-policy"></a>İlkenizi güncelleştirme ve test etme

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Üst menüdeki **Dizin + abonelik** filtresini SEÇIP Azure AD kiracınızı içeren dizini seçerek Azure AD kiracınızı içeren dizini kullandığınızdan emin olun.
1. Azure portal sol üst köşesindeki **tüm hizmetler** ' i seçin ve ardından **uygulama kayıtları**' i arayıp seçin.
1. **Kimlik deneyimi çerçevesini** seçin.
1. **Özel Ilkeyi karşıya yükle**' yi seçin ve ardından değiştirdiğiniz ilke dosyasını karşıya yükleyin *TrustFrameworkExtensions.xml*.
1. Karşıya yüklediğiniz oturum açma ilkesini seçin ve **Şimdi Çalıştır** düğmesine tıklayın.
1. Kaydolma bağlantısı olmadan oluşturduğunuz hesapla (MS Graph API kullanarak) oturum açabiliyor olmanız gerekir.

::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

* [Sosyal kimlik sağlayıcısı ile oturum açma](add-identity-provider.md)ekleyin.
* [Parola sıfırlama akışı](add-password-reset-policy.md)ayarlayın.
