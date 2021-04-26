---
title: Azure Active Directory B2C Multi-Factor Authentication | Microsoft Docs
description: Azure Active Directory B2C tarafından sağlanan tüketiciye yönelik uygulamalarda Multi-Factor Authentication etkinleştirme.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/01/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: eff33a8670ee8eb9ee32655956ee0e913ddaa4c1
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258155"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Azure Active Directory B2C'de çok faktörlü kimlik doğrulamasını etkinleştirme

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C), uygulamalarınızda kayıt ve oturum açma deneyimlerine ikinci bir güvenlik katmanı ekleyebilmeniz için doğrudan [Azure AD Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) ile tümleşir. Tek bir satır bile kod yazmadan çok faktörlü kimlik doğrulamasını etkinleştirirsiniz. Zaten kaydolma ve oturum açma Kullanıcı akışları oluşturduysanız, çok faktörlü kimlik doğrulamasını yine de etkinleştirebilirsiniz.

Bu özellik, uygulamaların aşağıdaki gibi senaryoları işlemesine yardımcı olur:

- Bir uygulamaya erişmek için Multi-Factor Authentication gerekmez, ancak başka bir uygulamaya erişmesi gerekir. Örneğin, müşteri, sosyal veya yerel hesapla otomatik bir sigorta uygulamasında oturum açabilir, ancak aynı dizinde kayıtlı olan ev sigortası uygulamasına erişmeden önce telefon numarasını doğrulamalıdır.
- Genel olarak bir uygulamaya erişmek için Multi-Factor Authentication gerekmez, ancak bunun içindeki hassas bölümlere erişmesi gerekir. Örneğin, müşteri, sosyal veya yerel hesap içeren bir bankacılık uygulamasında oturum açabilir ve hesap bakiyesini denetleyebilir, ancak bir hat aktarımını denemeden önce telefon numarasını doğrulamanız gerekir.

## <a name="set-multi-factor-authentication"></a>Multi-Factor Authentication 'ı ayarlama

::: zone pivot="b2c-user-flow"

1. [Azure portalda](https://portal.azure.com) oturum açma
1. Azure AD B2C kiracınızı içeren dizini seçmek için üst menüdeki **Dizin + abonelik** filtresini kullanın.
1. Sol menüden **Azure AD B2C**' yi seçin. Ya da **tüm hizmetler** ' i seçin ve **Azure AD B2C** seçin.
1. **Kullanıcı akışları ' nı** seçin.
1. MFA 'yı etkinleştirmek istediğiniz kullanıcı akışını seçin. Örneğin, *B2C_1_signinsignup*.
1. **Özellikler**’i seçin.
1. **Çok faktörlü kimlik doğrulaması** bölümünde, istenen **MFA yöntemini** seçin ve ardından **MFA zorlaması** altında **her zaman açık** veya **koşullu (önerilen)** seçeneğini belirleyin.
   > [!NOTE]
   >
   > - **Koşullu (önerilen)** seçeneğini belirlerseniz, [Kullanıcı akışlarına koşullu erişim de eklemeniz](conditional-access-user-flow.md)ve ilkenin uygulanmasını istediğiniz uygulamaları belirtmeniz gerekir.
   > - Multi-Factor Authentication (MFA), kaydolma Kullanıcı akışları için varsayılan olarak devre dışıdır. Kullanıcı akışlarında MFA 'yı telefon kayıt ile etkinleştirebilirsiniz, ancak birincil tanımlayıcı olarak bir telefon numarası kullanıldığından, tek seferlik geçiş kodu ikinci kimlik doğrulama faktörü için kullanılabilen tek seçenektir.

1. **Kaydet**’i seçin. MFA artık bu kullanıcı akışı için etkinleştirilmiştir.

Deneyimi doğrulamak için **Kullanıcı akışını Çalıştır** ' i kullanabilirsiniz. Aşağıdaki senaryoyu onaylayın:

Multi-Factor Authentication adımı gerçekleşmeden önce kiracınızda bir müşteri hesabı oluşturulur. Adım sırasında müşterinin bir telefon numarası sağlaması ve doğrulanması istenir. Doğrulama başarılı olursa telefon numarası daha sonra kullanılmak üzere hesaba iliştirilir. Müşteri iptal ediyor veya devre dışı olsa bile, çok faktörlü kimlik doğrulaması etkin olan bir sonraki oturum açma sırasında müşterinin bir telefon numarasını doğrulaması istenebilir.

::: zone-end

::: zone pivot="b2c-custom-policy"

Özel ilke başlangıç paketlerini GitHub 'dan almak Multi-Factor Authentication etkinleştirmek için, **SocialAndLocalAccountsWithMFA** Starter paketindeki XML dosyalarını Azure AD B2C kiracı adınızla güncelleştirin. **SocialAndLocalAccountsWithMFA** , sosyal, yerel ve Multi-Factor Authentication seçeneklerini sunar. Daha fazla bilgi için, bkz. [Active Directory B2C özel ilkelerle çalışmaya başlama](tutorial-create-user-flows.md?pivots=b2c-custom-policy). 

::: zone-end
