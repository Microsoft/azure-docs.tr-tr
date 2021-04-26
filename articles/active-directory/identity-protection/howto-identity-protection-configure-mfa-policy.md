---
title: MFA kayıt ilkesini Yapılandırma-Azure Active Directory Kimlik Koruması
description: Azure AD Kimlik Koruması Multi-Factor Authentication kayıt ilkesini nasıl yapılandıracağınızı öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 072db1d47abd95844075aeedfeddc4f8cf6bf936
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835875"
---
# <a name="how-to-configure-the-azure-ad-multi-factor-authentication-registration-policy"></a>Nasıl yapılır: Azure AD Multi-Factor Authentication kayıt ilkesini yapılandırma

Azure AD Kimlik Koruması, bir koşullu erişim ilkesini, oturum açmak istediğiniz modern kimlik doğrulama uygulamasına bakılmaksızın MFA kaydı gerektirecek şekilde yapılandırarak Azure AD Multi-Factor Authentication (MFA) kaydını yönetmenize yardımcı olur.

## <a name="what-is-the-azure-ad-multi-factor-authentication-registration-policy"></a>Azure AD Multi-Factor Authentication kayıt ilkesi nedir?

Azure AD Multi-Factor Authentication, yalnızca bir Kullanıcı adı ve paroladan fazlasını kullandığınızı doğrulamak için bir yol sağlar. Kullanıcı oturum açma işlemlerinin ikinci bir güvenlik katmanını sağlar. Kullanıcıların MFA istemlerine yanıt verebilmeleri için önce Azure AD Multi-Factor Authentication kaydolmaları gerekir.

Kullanıcı oturum açma işlemleri için Azure AD Multi-Factor Authentication gerektirmenizi öneririz, çünkü:

- Bir dizi doğrulama seçeneği aracılığıyla güçlü kimlik doğrulaması sunar.
- Kuruluşunuzun kimlik korumasında risk algılamalarını kendi kendine düzeltmesini sağlamak için temel bir rol oynar.

Azure AD Multi-Factor Authentication hakkında daha fazla bilgi için bkz. [Azure ad Multi-Factor Authentication nedir?](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>İlke yapılandırması

1. [Azure Portal](https://portal.azure.com)gidin.
1. **Azure Active Directory**  >  **güvenlik**  >  **kimlik koruması**  >  **MFA kayıt ilkesi**' ne gidin.
   1. **Atamalar** altında
      1. **Kullanıcılar** - **tüm kullanıcılar** ' ı seçin veya dağıtımı sınırlandırdıysanız **bireyler ve gruplar ' ı seçin** .
         1. İsteğe bağlı olarak, kullanıcıların ilkeden hariç tutulmasını seçebilirsiniz.
   1. **Denetimler** altında
      1. CheckBox 'ın **Azure AD MFA kaydının** seçili olduğundan emin olun ve **Seç**' i seçin.
   1. **Ilkeyi zorla**  -  **Üzerinde**
   1. **Kaydet**

## <a name="user-experience"></a>Kullanıcı deneyimi

Azure Active Directory Kimlik Koruması, kullanıcılarınıza etkileşimli olarak oturum açtıklarında kaydolmaları istenir ve kayıt işleminin tamamlanışında 14 gün daha olur. Bu 14 günlük süre boyunca kayıt atlayabilir, ancak sürenin sonunda oturum açma işlemini tamamlayabilmeleri için önce kaydolmaları gerekecektir.

İlgili Kullanıcı deneyimine genel bir bakış için bkz.:

- [Azure AD kimlik koruması Ile oturum açma deneyimleri](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>Sonraki adımlar

- [Oturum açma ve Kullanıcı risk ilkelerini etkinleştir](howto-identity-protection-configure-risk-policies.md)

- [Azure AD self servis parola sıfırlamayı etkinleştirme](../authentication/howto-sspr-deployment.md)

- [Azure AD Multi-Factor Authentication’ı etkinleştirme](../authentication/howto-mfa-getstarted.md)
