---
title: Kuruluşunuz için Multi-Factor Authentication etkinleştirme-Azure Active Directory
description: Lisansınızı temel alarak kuruluşunuz için Azure MFA 'yı etkinleştirin
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98bf62dff3e6c642f5de775f2d5bf8682ecea169
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164932"
---
# <a name="enable-multi-factor-authentication-for-your-organization"></a>Kuruluşunuz için Multi-Factor Authentication etkinleştirme

Kuruluşunuzun sahip olduğu lisanslara göre Azure Active Directory (AD) kullanıcılarınız için Azure Multi-Factor Authentication (MFA) etkinleştirmenin birden çok yolu vardır. 

![Gerektiğinde sinyalleri araştırın ve MFA 'yı uygulayın](./media/concept-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Önerilerimize bağlı olarak, MFA kullanıyorsanız, hesabınız% 99,9 ' den daha az olabilir.

Böylece kuruluşunuz, bir istatistik olmadan önce Multi-Factor Authentication 'ı ücretsiz olarak nasıl açıp açsın?

## <a name="free-option"></a>Ücretsiz seçenek

Azure AD 'nin ücretsiz avantajlarından yararlanan müşteriler, ortamınızda Multi-Factor Authentication 'ı etkinleştirmek için [güvenlik varsayılanlarını](../conditional-access/concept-conditional-access-security-defaults.md) kullanabilir.

## <a name="office-365"></a>Office 365

Office 365 ile müşteriler için iki seçenek vardır:

- [Güvenlik Varsayılanları](../conditional-access/concept-conditional-access-security-defaults.md) , Azure Multi-Factor Authentication ile tüm kullanıcılarınızı korumak IÇIN Azure AD aracılığıyla etkinleştirilebilir.
- Kuruluşunuz Multi-Factor Authentication sağlamaya yönelik daha fazla ayrıntı gerektiriyorsa, Office lisanslarınız [Kullanıcı BAŞıNA MFA](../authentication/howto-mfa-userstates.md) özelliklerini içerir. Kullanıcı başına MFA etkinleştirilir ve yöneticiler tarafından tek tek her kullanıcı için zorlanır.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Enterprise Mobility + Security E3, Microsoft 365 F1 veya Microsoft 365 E3 gibi bu işlevselliği içeren Azure AD Premium P1 veya benzer lisanslara sahip müşteriler için: 

En iyi kullanıcı deneyimi için [koşullu erişim ilkelerinin](../conditional-access/concept-conditional-access-policy-common.md) kullanılması önerilir.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Enterprise Mobility + Security E5 veya Microsoft 365 E5 gibi bu işlevselliği içeren Azure AD Premium P2 veya benzer lisansları olan müşteriler için: 

En iyi kullanıcı deneyimi ve zorlama esnekliği açısından, [koşullu erişim ilkelerinin](../conditional-access/concept-conditional-access-policy-common.md) yanı sıra [kimlik koruması](../identity-protection/overview-v2.md) risk ilkeleriyle birlikte kullanılması önerilir.

## <a name="authentication-methods"></a>Kimlik doğrulama yöntemleri

|   | Güvenlik varsayılanları | Diğer tüm Yöntemler |
| --- | --- | --- |
| Mobil uygulama aracılığıyla bildirim | X | X |
| Mobil uygulama veya donanım belirtecinden doğrulama kodu |   | X |
| Telefona kısa mesaj |   | X |
| Telefon çağrısı |   | X |
| Uygulama parolaları |   | X * * |

\* * Uygulama parolaları yalnızca yöneticiler tarafından etkinleştirildiyse eski kimlik doğrulama senaryolarıyla Kullanıcı başına MFA 'da kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

[Azure AD fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/active-directory/)
