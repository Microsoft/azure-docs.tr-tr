---
title: Azure Kimlik Koruması ile risk tabanlı MFA ve SSPR
description: Bu öğreticide riskli davranışları azaltmak için Multi-Factor Authentication ve self servis parola sıfırlama Azure Kimlik Koruması tümleştirmelerini etkinleştireceksiniz.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 01/31/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9289bfe9e90186896a753e5853d81d2f06669917
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125190"
---
# <a name="tutorial-use-risk-detections-to-trigger-multi-factor-authentication-and-password-changes"></a>Öğretici: Multi-Factor Authentication ve parola değişikliklerini tetiklemek için risk algılamalarını kullanma

Bu öğreticide Azure AD Premium P2 ile sunulan ve bir izleme ve raporlama aracından fazlası olan Azure Active Directory (Azure AD) Kimlik Koruması özelliklerini etkinleştireceksiniz. Kuruluşunuzun kimliklerini korumak için riskli davranışlar karşısında otomatik olarak harekete geçen risk tabanlı ilkeler yapılandırabilirsiniz. Bu ilkeler erişimi otomatik olarak engelleyebilir veya parola değişikliği isteme ve Multi-Factor Authentication kullanılmasını zorlama gibi düzeltme işlemleri başlatabilir.

Azure AD Kimlik Koruması İlkeleri, ek bir koruma katmanı olarak mevcut koşullu erişim ilkelerine ek olarak kullanılabilir. Kullanıcılarınız bu ilkelerin kullanılmasını gerektiren riskli davranışları tetiklemeyebilir ancak yönetici olarak onların koruma altında olduğunu bilirsiniz.

Risk algılamayı tetikleyeetkileyebilecek bazı öğeler şunlardır:

* Sızan kimlik bilgilerine sahip kullanıcılar
* Anonim IP adreslerinden oturum açma işlemleri
* Alışılmadık konumlara imkansız seyahat
* Bulaşma olan cihazlardan oturum açma işlemleri
* Şüpheli etkinlik gösteren IP adreslerinden gerçekleştirilen oturum açma işlemleri
* Alışılmadık konumlardan oturum açma işlemleri

Azure AD Kimlik Koruması hakkında daha fazla bilgi için bkz. [Azure AD Kimlik Koruması nedir?](../active-directory-identityprotection.md)

> [!div class="checklist"]
> * Azure MFA kaydını etkinleştirme
> * Risk tabanlı parola değişikliğini etkinleştirme
> * Risk tabanlı Multi-Factor Authentication'ı etkinleştirme

## <a name="prerequisites"></a>Önkoşullar

* En az Azure AD Premium P2 deneme sürümü lisansı bulunan çalışan bir Azure AD kiracısına erişim.
* Azure AD kiracınızda Genel Yönetici ayrıcalıklarına sahip olan bir hesap.
* Self servis parola sıfırlama (SSPR) ve Multi-Factor Authentication (MFA) öğreticilerini tamamlamış olmak.

## <a name="enable-risk-based-policies-for-sspr-and-mfa"></a>SSPR ve MFA için risk tabanlı ilkeleri etkinleştirme

Risk tabanlı ilkeleri etkinleştirmek oldukça kolay bir işlemdir. Aşağıdaki adımlar basit bir yapılandırma gerçekleştirmenize yardımcı olacaktır.

### <a name="enable-users-to-register-for-multi-factor-authentication"></a>Kullanıcıların Multi-Factor Authentication'a kaydolmasını sağlama

Azure AD Kimlik Koruması, kullanıcılarınızın Multi-Factor Authentication kaydolabilme ve geçerli kayıt durumunu kolayca belirlemesine yardımcı olabilecek bir varsayılan ilke içerir. Bu ilkeyi etkinleştirdiğinizde kullanıcılarınızın Multi-Factor Authentication ile oturum açması gerekmez ancak ön kayıt yapmaları istenir.

1. [Azure Portal](https://portal.azure.com) oturum açın.
1. **Tüm hizmetler**'e tıklayıp **Azure AD Kimlik Koruması**'na gidin.
1. **MFA kaydı**'na tıklayın.
1. İlkeyi Zorla'yı **Açık** duruma getirin.
   1. Bu ilkeyi ayarladığınızda tüm kullanıcılarınızın Multi-Factor Authentication kullanmaya hazırlanmak için gerekli yöntemleri kaydetmeleri gerekir.
1. **Kaydet**’e tıklayın.

   ![Kullanıcıların oturum açma sırasında MFA 'ya kaydolmesini gerektir](./media/tutorial-risk-based-sspr-mfa/risk-based-require-mfa-registration.png)

### <a name="enable-risk-based-password-changes"></a>Risk tabanlı parola değişikliğini etkinleştirme

Microsoft, kullanıcı adı ve parola çiftlerini bulma amacıyla araştırmacılar, kolluk kuvvetleri, Microsoft'taki çeşitli güvenlik ekipleri ve diğer güvenilir kaynaklarla birlikte çalışmalar yapmaktadır. Bu çiftlerden biri ortamınızdaki bir hesapla eşleştiğinde aşağıdaki ilkeyi kullanarak risk tabanlı parola değişikliğinin tetiklenmesini sağlayabilirsiniz.

1. Kullanıcı riski ilkesi'ne tıklayın.
1. **Koşullar** bölümünde **Kullanıcı riski**'ni ve ardından **Orta ve üzeri**'ni seçin.
1. "Seç"e ve ardından "Bitti"ye tıklayın
1. **Erişim** bölümünde **Erişime izin ver**'i ve ardından **Parola değişikliği iste**'yi seçin.
1. "Seç"e tıklayın
1. İlkeyi Zorla'yı **Açık** duruma getirin.
1. **Kaydet**'e tıklayın.

### <a name="enable-risk-based-multi-factor-authentication"></a>Risk tabanlı Multi-Factor Authentication'ı etkinleştirme

Çoğu kullanıcı, takip edilebilen normal bir davranışa sahiptir ve davranışları normalin dışına çıktığında oturum açmalarına izin vermek riskli olabilir. Bu kullanıcıyı engellemek veya gerçekten söyledikleri kişi olduklarını kanıtlamaları için Multi-Factor Authentication gerçekleştirmelerini istemeyi tercih edebilirsiniz. Riskli oturum açma algılandığında MFA gerektiren ilkeyi kullanmak için aşağıdaki ilkeyi etkinleştirin.

1. Oturum açma riski ilkesi'ne tıklayın
1. **Koşullar** bölümünde **Kullanıcı riski**'ni ve ardından **Orta ve üzeri**'ni seçin.
1. "Seç"e ve ardından "Bitti"ye tıklayın
1. **Erişim** bölümünde **Erişime izin ver**'i ve ardından **Çok faktörlü kimlik doğrulamasını gerekli kıl**'ı seçin.
1. "Seç"e tıklayın
1. İlkeyi Zorla'yı **Açık** duruma getirin.
1. **Kaydet**'e tıklayın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Testi tamamladıysanız ve risk tabanlı ilkelerin etkin durumda kalmasını istemiyorsanız devre dışı bırakmak istediğiniz ilkelere gidip **İlkeyi Zorla** ayarını **Kapalı** olarak değiştirin.
