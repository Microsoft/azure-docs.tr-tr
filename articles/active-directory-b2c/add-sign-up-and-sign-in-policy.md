---
title: Kaydolma ve oturum açma akışı ayarlama
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C oturum açma ve oturum açma akışını ayarlamayı öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 047b295d68e872b4dc334e53b5b9da0ec07d5826
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257084"
---
# <a name="set-up-a-sign-up-and-sign-in-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C oturum açma ve oturum açma akışı ayarlama

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-up-and-sign-in-flow"></a>Kaydolma ve oturum açma akışı

Kaydolma ve oturum açma ilkesi, kullanıcıların şunları sağlar: 

* Yerel hesapla kaydolma
* Yerel hesapla oturum açma
* Bir sosyal hesapla kaydolma veya oturum açma
* Parola sıfırlama

![Profil düzenlemesi akışı](./media/add-sign-up-and-sign-in-policy/add-sign-up-and-sign-in-flow.png)

## <a name="prerequisites"></a>Önkoşullar

Henüz yapmadıysanız, [bir Web uygulamasını Azure Active Directory B2C kaydedin](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Kaydolma ve oturum açma Kullanıcı akışı oluşturma

Kaydolma ve oturum açma Kullanıcı akışı, hem kayıt hem de oturum açma deneyimlerini tek bir yapılandırmayla işler. Uygulamanızın kullanıcıları, bağlama göre doğru yolun altına alınır.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Azure portal, araması yapın ve **Azure AD B2C** seçin.
1. **İlkeler** altında **Kullanıcı akışları**' nı seçin ve ardından **Yeni Kullanıcı akışı**' nı seçin.

    ![Yeni Kullanıcı akışı düğmesi vurgulanmış şekilde portalda Kullanıcı akışları sayfası](./media/add-sign-up-and-sign-in-policy/signup-signin-user-flow.png)

1. **Kullanıcı akışı oluştur** sayfasında, **oturum aç ve kullanıcı akışını oturum aç** ' ı seçin.

    ![Kaydolma ve oturum açma akışı vurgulanmış bir Kullanıcı akış sayfası seçin](./media/add-sign-up-and-sign-in-policy/select-user-flow-type.png)

1. **Sürüm seçin** altında **Önerilen**' i seçin ve ardından **Oluştur**' u seçin. (Kullanıcı akışı sürümleri hakkında[daha fazla bilgi edinin](user-flow-versions.md) .)

    ![Azure portal ' de vurgulanan özelliklerle Kullanıcı akışı oluşturma sayfası](./media/add-sign-up-and-sign-in-policy/select-version.png)

1. Kullanıcı akışı için bir **ad** girin. Örneğin, *signupsignin1*.
1. **Kimlik sağlayıcıları** Için **e-posta kaydı**' nı seçin.
1. **Kullanıcı öznitelikleri ve talepler** için, kayıt sırasında kullanıcıdan toplamak ve göndermek istediğiniz talepleri ve öznitelikleri seçin. Örneğin, **daha fazla göster**' i seçin ve ardından **ülke/bölge**, **görünen ad** ve **posta kodu** için öznitelikler ve talepler ' i seçin. **Tamam**'a tıklayın.

    ![Üç talep seçili olan öznitelikler ve talepler seçim sayfası](./media/add-sign-up-and-sign-in-policy/signup-signin-attributes.png)

1. Kullanıcı akışını eklemek için **Oluştur** ' a tıklayın. *B2C_1* bir ön eki otomatik olarak ada eklenir.
2. ["Parolanızı unuttum?" akışını işlemek](add-password-reset-policy.md?pivots=b2c-user-flow.md#self-service-password-reset-recommended) için gereken adımları izleyin. kayıt veya oturum açma ilkesi içinde.

### <a name="test-the-user-flow"></a>Kullanıcı akışını test etme

1. Genel Bakış sayfasını açmak için oluşturduğunuz kullanıcı akışını seçin ve ardından **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama** için, daha önce kaydettiğiniz *WebApp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Kullanıcı akışını Çalıştır**' a tıklayın ve ardından **Şimdi kaydolun**' ı seçin.

    ![Kullanıcı akışını Çalıştır düğmesi vurgulanmış şekilde portalda Kullanıcı akış sayfasını Çalıştır](./media/add-sign-up-and-sign-in-policy/signup-signin-run-now.png)

1. Geçerli bir e-posta adresi girin, **doğrulama kodu gönder**' e tıklayın, aldığınız doğrulama kodunu girin ve **kodu doğrula**' yı seçin.
1. Yeni bir parola girin ve parolayı onaylayın.
1. Ülkenizi ve bölgenizi seçin, görüntülenmesini istediğiniz adı girin, bir posta kodu girin ve ardından **Oluştur**' a tıklayın. Belirteç öğesine döner `https://jwt.ms` ve size gösterilmesi gerekir.
1. Artık kullanıcı akışını yeniden çalıştırabilirsiniz ve oluşturduğunuz hesapla oturum açabilmelisiniz. Döndürülen belirteç, ülke/bölge, ad ve posta kodu ' nu seçtiğiniz talepleri içerir.

> [!NOTE]
> "Kullanıcı akışını Çalıştır" deneyimi şu anda yetkilendirme kodu akışını kullanan SPA yanıt URL 'SI türüyle uyumlu değil. "Kullanıcı akışını Çalıştır" deneyimini bu tür uygulamalarla kullanmak için, "Web" türünde bir yanıt URL 'SI kaydedin ve [burada](tutorial-register-spa.md)açıklandığı gibi örtük akışı etkinleştirin.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-sign-up-and-sign-in-policy"></a>Kaydolma ve oturum açma ilkesi oluşturma

Özel ilkeler, Kullanıcı, neys tanımlamak için Azure AD B2C kiracınıza yüklediğiniz XML dosyaları kümesidir. Kaydolma ve oturum açma, parola sıfırlama ve profil düzenlemesi ilkesi dahil olmak üzere önceden oluşturulmuş birkaç ilke ile başlangıç paketleri sunuyoruz. Daha fazla bilgi için, bkz. [Azure AD B2C özel ilkelerle çalışmaya başlama](tutorial-create-user-flows.md?pivots=b2c-custom-policy).

::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

* [Sosyal kimlik sağlayıcısı ile oturum açma](add-identity-provider.md)ekleyin.
* [Parola sıfırlama akışı](add-password-reset-policy.md)ayarlayın.
