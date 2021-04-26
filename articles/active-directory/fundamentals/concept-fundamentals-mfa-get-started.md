---
title: Kuruluşunuz için Azure AD Multi-Factor Authentication-Azure Active Directory
description: Lisans modelinize bağlı olarak, kuruluşunuz için Azure AD Multi-Factor Authentication 'nin kullanılabilir özellikleri hakkında bilgi edinin
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: daveba
author: daveba
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85e7f02f8cbda6218396bf4a9a4654a113b7817c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94836751"
---
# <a name="overview-of-azure-ad-multi-factor-authentication-for-your-organization"></a>Kuruluşunuz için Azure AD Multi-Factor Authentication genel bakış

Kuruluşunuzun sahip olduğu lisanslara göre Azure Active Directory (AD) kullanıcılarınız için Azure AD Multi-Factor Authentication etkinleştirmenin birden çok yolu vardır. 

![Gerektiğinde sinyalleri araştırın ve MFA 'yı uygulayın](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Çalışmalarımıza göre, Multi-Factor Authentication (MFA) kullanıyorsanız, hesabınız% 99,9 ' den daha az olabilir.

Bu nedenle kuruluşunuz, istatistik olmadan önce, ücretsiz olarak MFA 'yı nasıl açabilir?

## <a name="free-option"></a>Ücretsiz seçenek

Azure AD 'nin ücretsiz avantajlarından yararlanan müşteriler, ortamınızda Multi-Factor Authentication 'ı etkinleştirmek için [güvenlik varsayılanlarını](../fundamentals/concept-fundamentals-security-defaults.md) kullanabilir.

## <a name="microsoft-365-business-e3-or-e5"></a>Microsoft 365 İş, E3 veya E5

Microsoft 365 olan müşteriler için iki seçenek vardır:

* Tüm oturum açma olayları için Azure AD Multi-Factor Authentication, tüm kullanıcılar için etkin veya devre dışı bırakılmış. Yalnızca bir kullanıcı alt kümesi için veya yalnızca belirli senaryolarda Multi-Factor Authentication 'ı etkinleştirebilme özelliği yoktur. Yönetim, Office 365 portalından yapılır. 
* Gelişmiş bir kullanıcı deneyimi için Azure AD Premium P1 veya P2 sürümüne yükseltin ve koşullu erişimi kullanın. Daha fazla bilgi için bkz. Multi-Factor Authentication ile güvenli Microsoft 365 kaynakları.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Enterprise Mobility + Security E3, Microsoft 365 F1 veya Microsoft 365 E3 gibi bu işlevselliği içeren Azure AD Premium P1 veya benzer lisanslara sahip müşteriler için: 

Belirli senaryolar veya olaylar sırasında iş gereksinimlerinize uyacak şekilde kullanıcılardan çok faktörlü kimlik doğrulaması için kullanıcı istemek üzere [Azure AD koşullu erişimi](../authentication/tutorial-enable-azure-mfa.md) kullanın.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Enterprise Mobility + Security E5 veya Microsoft 365 E5 gibi bu işlevselliği içeren Azure AD Premium P2 veya benzer lisansları olan müşteriler için: 

En güçlü güvenlik konumunu ve geliştirilmiş Kullanıcı deneyimini sağlar. Kullanıcının desenlerine uyum sağlayan Azure AD Premium P1 özelliklerine [risk tabanlı koşullu erişim](../conditional-access/howto-conditional-access-policy-risk.md) ekler ve Multi-Factor Authentication istemlerini en aza indirir.

## <a name="authentication-methods"></a>Kimlik doğrulama yöntemleri

| Yöntem | Güvenlik varsayılanları | Diğer tüm Yöntemler |
| --- | --- | --- |
| Mobil uygulama aracılığıyla bildirim | X | X |
| Mobil uygulama veya donanım belirtecinden doğrulama kodu |   | X |
| Telefona kısa mesaj |   | X |
| Telefon çağrısı |   | X |

## <a name="next-steps"></a>Sonraki adımlar

Başlamak için [Azure AD Multi-Factor Authentication ile Kullanıcı oturum açma olaylarını güvenli hale getirme](../authentication/tutorial-enable-azure-mfa.md)öğreticisine bakın.

Lisanslama hakkında daha fazla bilgi için bkz. [Azure AD Multi-Factor Authentication Için Özellikler ve lisanslar](../authentication/concept-mfa-licensing.md).
