---
title: Azure AD Kimlik Koruması İlkeleri
description: Kimlik koruması ile etkinleştirilen üç ilkeyi tanımlama
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28a9080ce878e262573adf0b3c79394079c09ca2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835977"
---
# <a name="identity-protection-policies"></a>Kimlik Koruması ilkeleri

Azure Active Directory Kimlik Koruması, yöneticilerin etkinleştirmek için seçebileceğiniz üç varsayılan ilke içerir. Bu ilkeler sınırlı özelleştirmeyi içerir ancak çoğu kuruluş için geçerlidir. Tüm ilkeler, [acil erişim veya kesme camı yönetici hesaplarınız](../roles/security-emergency-access.md)gibi kullanıcıların dışlanmasını sağlar.

![Kimlik Koruması ilkeleri](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-ad-mfa-registration-policy"></a>Azure AD MFA kayıt ilkesi

Kimlik koruması, kuruluşların oturum açma sırasında kayıt gerektiren bir koşullu erişim ilkesi kullanarak Azure AD Multi-Factor Authentication (MFA) kullanıma sunmasına yardımcı olabilir. Bu ilkeyi etkinleştirmek, kuruluşunuzdaki yeni kullanıcıların ilk gününde MFA için kaydolmalarına emin olmanın harika bir yoludur. Multi-Factor Authentication, kimlik koruması kapsamındaki risk olayları için kendi kendine düzeltme yöntemlerinden biridir. Kendi kendini düzeltme, kullanıcılarınızın yardım masası çağrı birimini azaltmak için kendi başına işlem yapmasına olanak sağlar.

Azure AD Multi-Factor Authentication hakkında daha fazla bilgi makalesinde, nasıl çalıştığı hakkında daha fazla bilgi bulabilirsiniz [: Azure ad Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md).

## <a name="sign-in-risk-policy"></a>Oturum açma riski ilkesi

Kimlik koruması, hem gerçek zamanlı hem de çevrimdışı olan her oturum açma işleminden gelen sinyalleri analiz eder ve oturum açma işleminin Kullanıcı tarafından gerçekleştirilmeme olasılığını temel alarak bir risk puanı hesaplar. Yöneticiler, kurumsal gereksinimleri zorlamak için bu risk puanı sinyaline dayalı bir karar verebilir. Yöneticiler erişimi engellemeyi, erişime izin vermeyi veya erişime izin vermeyi seçebilir ancak çok faktörlü kimlik doğrulaması gerektirir.

Risk algılanırsa, kullanıcılar, yöneticilerin gereksiz gürültüsünü engellemek için riskli oturum açma olayını kendi kendine düzeltebileceği ve kapatan çok faktörlü kimlik doğrulaması gerçekleştirebilir.

> [!NOTE] 
> Kullanıcılar, oturum açma risk ilkesini tetiklemeden önce Azure AD Multi-Factor Authentication için önceden kaydolmalıdır.

### <a name="custom-conditional-access-policy"></a>Özel koşullu erişim ilkesi

Yöneticiler, oturum açma riskini atama koşulu da dahil olmak üzere özel bir koşullu erişim ilkesi oluşturmayı da tercih edebilir. Koşullu erişim ilkesinde koşul olarak risk hakkında daha fazla bilgi, [koşullu erişim: koşullar](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk) makalesinde bulunabilir.

![Özel koşullu erişim oturum açma risk ilkesi](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>Kullanıcı riski ilkesi

Kimlik koruması, kullanıcının davranışı için ne düşündüğü için ne olduğunu hesaplayabilir ve bu durumu risk temelinde kararlara dayandırmak için kullanır. Kullanıcı riski, bir kimliğin tehlikeye düşmesi olasılığının bir hesaplanmasıdır. Yöneticiler, kurumsal gereksinimleri zorlamak için bu risk puanı sinyaline dayalı bir karar verebilir. Yöneticiler erişimi engellemeyi, erişime izin vermeyi veya erişime izin vermeyi seçebilir ancak [Azure AD self servis parola sıfırlama](../authentication/howto-sspr-deployment.md)kullanarak parola değişikliği gerektirir.

Risk algılanırsa, kullanıcılar self servis parola sıfırlama işlemini kendi kendine düzeltir ve yöneticiler için gereksiz paraziti engellemek için Kullanıcı riski olayını kapatabilir.

> [!NOTE] 
> Kullanıcılar, Kullanıcı risk ilkesini tetiklemeden önce self servis parola sıfırlama için önceden kaydolmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD self servis parola sıfırlamayı etkinleştirme](../authentication/howto-sspr-deployment.md)

- [Azure AD Multi-Factor Authentication’ı etkinleştirme](../authentication/howto-mfa-getstarted.md)

- [Azure AD Multi-Factor Authentication kayıt ilkesini etkinleştirme](howto-identity-protection-configure-mfa-policy.md)

- [Oturum açma ve Kullanıcı risk ilkelerini etkinleştir](howto-identity-protection-configure-risk-policies.md)
