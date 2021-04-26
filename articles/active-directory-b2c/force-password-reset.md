---
title: Azure AD B2C bir zorla parola sıfırlama akışı yapılandırın
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C için zorlanan parola sıfırlama akışını ayarlamayı öğrenin.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/08/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 7fac7df0978b23e535d8761b436b14e2f41e5f91
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107209511"
---
# <a name="set-up-a-force-password-reset-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C bir parolayı zorla sıfırlama akışı ayarlama

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

> [!IMPORTANT]
> Parola sıfırlamayı zorla, Azure AD B2C genel bir önizleme özelliğidir. Önizlemeler hakkında daha fazla bilgi için bkz. [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Genel Bakış
Yönetici olarak, Kullanıcı parolasını unutursa [kullanıcının parolasını sıfırlayabilirsiniz](manage-users-portal.md#reset-a-users-password) . Ya da bu uygulamaları parolayı sıfırlamasına zorlamak istiyorsunuz. Bu makalede, bu senaryolarda parola sıfırlamayı zorunlu hale yapacağınızı öğreneceksiniz.

Yönetici, bir kullanıcının parolasını Azure portal aracılığıyla sıfırladığında [Forcechangepasswordnextsignın](user-profile-attributes.md#password-profile-property) değeri olarak ayarlanır `true` . [Oturum açma ve kaydolma yolculuğu](add-sign-up-and-sign-in-policy.md) , bu özniteliğin değerini denetler. Kullanıcı oturum açma işlemini tamamladıktan sonra, özniteliği olarak ayarlandıysa `true` , Kullanıcı parolasını sıfırlamalıdır. Ardından, özniteliğinin değeri geri olarak ayarlanır `false` .

![Parola sıfırlama akışını zorla](./media/force-password-reset/force-password-reset-flow.png)

Parola sıfırlama akışı, oturum açma için parola ile bir [e-posta adresi](identity-provider-local.md#email-sign-in) veya [kullanıcı adı](identity-provider-local.md#username-sign-in) kullanan Azure AD B2C yerel hesaplar için geçerlidir.

::: zone pivot="b2c-user-flow"

### <a name="force-a-password-reset-after-90-days"></a>90 gün sonra parola sıfırlamayı zorla

Yönetici olarak, bir kullanıcının parola süre sonunu [MS Graf](microsoft-graph-operations.md)kullanarak 90 gün olarak ayarlayabilirsiniz. 90 gün sonra, [Forcechangepasswordnextsignın](user-profile-attributes.md#password-profile-property) değeri otomatik olarak olarak ayarlanır `true` . Kullanıcının parola süre sonu ilkesini ayarlama hakkında daha fazla bilgi için bkz. [parola ilkesi özniteliği](user-profile-attributes.md#password-policy-attribute).

Parola süre dolum ilkesi ayarlandıktan sonra, bu makalede açıklandığı gibi parola sıfırlama akışını zorla ' yı da yapılandırmanız gerekir.  

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="configure-your-policy"></a>İlkenizi yapılandırma

Kaydolma veya oturum açma Kullanıcı akışında **zorlanan parola sıfırlama** ayarını etkinleştirmek için:

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Azure portal, araması yapın ve **Azure AD B2C** seçin.
1. **Kullanıcı akışları ' nı** seçin.
1. Özelleştirmek istediğiniz kaydolma ve oturum açma ya da oturum açma kullanıcı akışını ( **Önerilen** türü) seçin.
1. **Ayarlar** altındaki sol menüde **Özellikler**' i seçin.
1. **Parola karmaşıklığı** bölümünde **zorlanan parola sıfırlama**' yı seçin.
1. **Kaydet**’i seçin.

### <a name="test-the-user-flow"></a>Kullanıcı akışını test etme

1. [Azure Portal](https://portal.azure.com) Kullanıcı Yöneticisi veya parola Yöneticisi olarak oturum açın. Kullanılabilir roller hakkında daha fazla bilgi için, bkz. [Azure Active Directory yönetici rolleri atama](../active-directory/roles/permissions-reference.md#all-roles).
1. Portal araç çubuğunda **Dizin + abonelik** simgesini seçin ve ardından Azure AD B2C kiracınızı içeren dizini seçin.
1. Azure portal, araması yapın ve **Azure AD B2C** seçin.
1. **Kullanıcılar**’ı seçin. Parola sıfırlamayı test etmek için kullanacağınız kullanıcıyı arayıp seçin ve **Parolayı Sıfırla**' yı seçin.
1. Azure portal, araması yapın ve **Azure AD B2C** seçin.
1. **Kullanıcı akışları ' nı** seçin.
1. Test etmek istediğiniz kaydolma veya oturum açma kullanıcı akışını ( **Önerilen** türde) seçin.
1. **Kullanıcı akışını Çalıştır**' ı seçin.
1. **Uygulama** için, daha önce kaydettiğiniz *WebApp1* adlı Web uygulamasını seçin. **Yanıt URL 'si** gösterilmesi gerekir `https://jwt.ms` .
1. **Kullanıcı akışını Çalıştır**' ı seçin.
1. Parolayı sıfırlayabilmeniz için kullanıcı hesabıyla oturum açın.
1. Artık Kullanıcı parolasını değiştirmeniz gerekir. Parolayı değiştirin ve **devam**' ı seçin. Belirteç öğesine döner `https://jwt.ms` ve size gösterilmesi gerekir.

::: zone-end

::: zone pivot="b2c-custom-policy"

Bu özellik şu anda yalnızca Kullanıcı Akışları için kullanılabilir. Kurulum adımları için yukarıdaki **Kullanıcı akışı** ' nı seçin.

::: zone-end

## <a name="next-steps"></a>Sonraki adımlar

[Self servis parola sıfırlama](add-password-reset-policy.md)ayarlayın.
