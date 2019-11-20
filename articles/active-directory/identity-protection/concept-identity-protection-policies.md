---
title: Azure AD Kimlik Koruması İlkeleri
description: Kimlik koruması ile etkinleştirilen üç ilkeyi tanımlama
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a44e32efa3e38cf9c4df7dc00e3189c129db418
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887422"
---
# <a name="identity-protection-policies"></a>Kimlik koruma ilkeleri

Azure Active Directory Kimlik Koruması, yöneticilerin etkinleştirmek için seçebileceğiniz üç varsayılan ilke içerir. Bu ilkeler sınırlı özelleştirmeyi içerir ancak çoğu kuruluş için geçerlidir. Tüm ilkeler, [acil erişim veya kesme camı yönetici hesaplarınız](../users-groups-roles/directory-emergency-access.md)gibi kullanıcıların dışlanmasını sağlar.

![Kimlik koruma ilkeleri](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-mfa-registration-policy"></a>Azure MFA kayıt ilkesi

Kimlik koruması, kuruluşların oturum açma sırasında kayıt gerektiren bir koşullu erişim ilkesi kullanarak Azure Multi-Factor Authentication (MFA) kullanıma sunmasına yardımcı olabilir. Bu ilkeyi etkinleştirmek, kuruluşunuzdaki yeni kullanıcıların ilk gününde MFA için kaydolmalarına emin olmanın harika bir yoludur. Multi-Factor Authentication, kimlik koruması kapsamındaki risk olayları için kendi kendine düzeltme yöntemlerinden biridir. Kendi kendini düzeltme, kullanıcılarınızın yardım masası çağrı birimini azaltmak için kendi başına işlem yapmasına olanak sağlar.

Azure Multi-Factor Authentication hakkında daha fazla bilgi makalesinde, nasıl çalıştığı hakkında daha fazla bilgi bulabilirsiniz [: Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md).

## <a name="sign-in-risk-policy"></a>Oturum açma risk ilkesi

Kimlik koruması, hem gerçek zamanlı hem de çevrimdışı olan her oturum açma işleminden gelen sinyalleri analiz eder ve oturum açma işleminin Kullanıcı tarafından gerçekleştirilmeme olasılığını temel alarak bir risk puanı hesaplar. Yöneticiler, kurumsal gereksinimleri zorlamak için bu risk puanı sinyaline dayalı bir karar verebilir. Yöneticiler erişimi engellemeyi, erişime izin vermeyi veya erişime izin vermeyi seçebilir ancak çok faktörlü kimlik doğrulaması gerektirir.

Risk algılanırsa, kullanıcılar, yöneticilerin gereksiz gürültüsünü engellemek için riskli oturum açma olayını kendi kendine düzeltebileceği ve kapatan çok faktörlü kimlik doğrulaması gerçekleştirebilir.

> [!NOTE] 
> Kullanıcılar, oturum açma risk ilkesini tetiklemeden önce Azure Multi-Factor Authentication için önceden kaydolmalıdır.

### <a name="custom-conditional-access-policy"></a>Özel koşullu erişim ilkesi

Yöneticiler, oturum açma riskini atama koşulu da dahil olmak üzere özel bir koşullu erişim ilkesi oluşturmayı da tercih edebilir. Koşullu erişim hakkında daha fazla bilgi makalesinde, [koşullu erişim nedir?](../conditional-access/overview.md)

![Özel koşullu erişim oturum açma risk ilkesi](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>Kullanıcı risk ilkesi

Kimlik koruması, kullanıcının davranışı için ne düşündüğü için ne olduğunu hesaplayabilir ve bu durumu risk temelinde kararlara dayandırmak için kullanır. Kullanıcı riski, bir kimliğin tehlikeye düşmesi olasılığının bir hesaplanmasıdır. Yöneticiler, kurumsal gereksinimleri zorlamak için bu risk puanı sinyaline dayalı bir karar verebilir. Yöneticiler erişimi engellemeyi, erişime izin vermeyi veya erişime izin vermeyi seçebilir ancak [Azure AD self servis parola sıfırlama](../authentication/howto-sspr-deployment.md)kullanarak parola değişikliği gerektirir.

Risk algılanırsa, kullanıcılar self servis parola sıfırlama işlemini kendi kendine düzeltir ve yöneticiler için gereksiz paraziti engellemek için Kullanıcı riski olayını kapatabilir.

> [!NOTE] 
> Kullanıcılar, Kullanıcı risk ilkesini tetiklemeden önce self servis parola sıfırlama için önceden kaydolmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD self servis parola sıfırlamayı etkinleştirme](../authentication/howto-sspr-deployment.md)

- [Azure Multi-Factor Authentication etkinleştirme](../authentication/howto-mfa-getstarted.md)

- [Azure Multi-Factor Authentication kayıt ilkesini etkinleştirme](howto-identity-protection-configure-mfa-policy.md)

- [Oturum açma ve Kullanıcı risk ilkelerini etkinleştir](howto-identity-protection-configure-risk-policies.md)
