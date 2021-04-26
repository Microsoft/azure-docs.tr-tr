---
title: Profil düzenlemesi akışı ayarlama
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C profil düzenlemesi akışını ayarlamayı öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/16/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d84756a2ae4f8897c42e1846e3a91dbb9f7ad7e1
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257050"
---
# <a name="set-up-a-profile-editing-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C profil düzenlemesi oluşturma akışı ayarlama

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="profile-editing-flow"></a>Profil düzenlemesi akışı

Profil düzenlemesi ilkesi, kullanıcıların, görünen ad, soyadı, verilen ad, şehir ve diğerleri gibi profil özniteliklerini yönetmesine olanak tanır. Profil düzenlemesi akışı aşağıdaki adımları içerir: 

1. Yerel veya sosyal hesapla kaydolma veya oturum açma. Oturum hala etkinse, Azure AD B2C kullanıcıyı yetkilendirir ve sonraki adıma atlar.
1. Azure AD B2C, dizinden kullanıcı profilini okur ve kullanıcının öznitelikleri düzenlemesine izin verir.

![Profil düzenlemesi akışı](./media/add-profile-editing-policy/profile-editing-flow.png)


## <a name="prerequisites"></a>Önkoşullar

Henüz yapmadıysanız, [bir Web uygulamasını Azure Active Directory B2C kaydedin](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-profile-editing-user-flow"></a>Profil düzenlemesi Kullanıcı akışı oluşturma

Kullanıcıların uygulamanızdaki profilini düzenlemesini etkinleştirmek istiyorsanız, bir profil düzenleme Kullanıcı akışı kullanın.

1. Azure AD B2C kiracının Genel Bakış sayfasının menüsünde **Kullanıcı akışları**' nı seçin ve ardından **Yeni Kullanıcı akışı**' nı seçin.
1. **Kullanıcı akışı oluştur** sayfasında **profil düzenlemesi** Kullanıcı akışı ' nı seçin. 
1. **Sürüm seçin** altında **Önerilen**' i seçin ve ardından **Oluştur**' u seçin.
1. Kullanıcı akışı için bir **ad** girin. Örneğin, *profileediting1*.
1. **Kimlik sağlayıcıları** Için **e-posta oturum açma** seçeneğini belirleyin.
1. **Kullanıcı öznitelikleri** için, müşterinin profilinde düzenleyebilmesini istediğiniz öznitelikleri seçin. Örneğin, **daha fazla göster**' i seçin ve **görünen ad** ve **iş unvanı** için her iki özniteliği ve talebi seçin. **Tamam**'a tıklayın.
1. Kullanıcı akışını eklemek için **Oluştur** ' a tıklayın. *B2C_1* bir ön eki otomatik olarak ada eklenir.

### <a name="test-the-user-flow"></a>Kullanıcı akışını test etme

1. Genel Bakış sayfasını açmak için oluşturduğunuz kullanıcı akışını seçin ve ardından **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama** için, daha önce kaydettiğiniz *WebApp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Kullanıcı akışını Çalıştır**' a tıklayın ve daha önce oluşturduğunuz hesapla oturum açın.
1. Artık Kullanıcı için görünen adı ve iş başlığını değiştirme fırsatına sahipsiniz. **Devam**’a tıklayın. Belirteç öğesine döner `https://jwt.ms` ve size gösterilmesi gerekir.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-profile-editing-policy"></a>Profil düzenleme ilkesi oluşturma

Özel ilkeler, Kullanıcı, neys tanımlamak için Azure AD B2C kiracınıza yüklediğiniz XML dosyaları kümesidir. Kaydolma ve oturum açma, parola sıfırlama ve profil düzenlemesi ilkesi dahil olmak üzere önceden oluşturulmuş birkaç ilke ile başlangıç paketleri sunuyoruz. Daha fazla bilgi için, bkz. [Azure AD B2C özel ilkelerle çalışmaya başlama](tutorial-create-user-flows.md?pivots=b2c-custom-policy).

::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

* [Sosyal kimlik sağlayıcısı ile oturum açma](add-identity-provider.md)ekleyin.