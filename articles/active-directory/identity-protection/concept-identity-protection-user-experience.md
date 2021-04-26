---
title: Azure AD Kimlik Koruması kullanıcı deneyimleri
description: Azure AD Kimlik Koruması Kullanıcı deneyimi
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
ms.openlocfilehash: 4d4897b9187caab50be4db75bbc0af03e3d35aa4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94835994"
---
# <a name="user-experiences-with-azure-ad-identity-protection"></a>Azure AD Kimlik Koruması kullanıcı deneyimleri

Azure Active Directory Kimlik Koruması, şunları yapabilirsiniz:

* Kullanıcıların Azure AD Multi-Factor Authentication (MFA) kaydolmanızı gerektir
* Riskli oturum açma işlemlerinin ve güvenliği aşılmış kullanıcıların düzeltmesini otomatikleştirin

Tüm kimlik koruması ilkelerinin, kullanıcılar için oturum açma deneyimine etkisi vardır. Kullanıcıların Azure AD MFA ve self servis parola sıfırlama gibi araçları kaydolmalarına ve kullanmasına izin vermek etkiyi olumsuz etkileyebilir. Uygun ilke seçimleriyle birlikte bu araçlar kullanıcılara ihtiyaç duydukları bir kendi kendini düzeltme seçeneği sağlar.

## <a name="multi-factor-authentication-registration"></a>Multi-Factor Authentication kaydı

Multi-Factor Authentication kaydı gerektiren kimlik koruma ilkesini etkinleştirmek ve tüm kullanıcılarınızı hedeflemek için Azure AD MFA 'yı kullanarak gelecekte kendini düzeltemedi. Bu ilkeyi yapılandırmak, kullanıcılarınıza kaydolmak üzere seçebilecekleri 14 günlük bir süre sağlar ve sona kaydolmaya zorlanır. Kullanıcılar için deneyim aşağıda özetlenmiştir. Makalenin son kullanıcı belgelerinde daha fazla bilgi bulunabilir, [iki öğeli doğrulama ve iş veya okul hesabınız Için genel bakış](../user-help/multi-factor-authentication-end-user-first-time.md).

### <a name="registration-interrupt"></a>Kayıt kesmesi

1. Azure AD ile tümleştirilen herhangi bir uygulamada oturum açarken, Kullanıcı Multi-Factor Authentication için hesabı ayarlama gereksinimi hakkında bir bildirim alır. Bu ilke ayrıca, yeni bir cihazla yeni kullanıcılar için Windows 10 ' un kullanıma hazır deneyiminden da tetiklenir.
   
    ![Daha fazla bilgi gerekiyor](./media/concept-identity-protection-user-experience/identity-protection-experience-more-info-mfa.png)

1. Azure AD Multi-Factor Authentication için kaydolmak üzere Kılavuzlu adımları doldurun ve oturum açma bilgilerinizi doldurun.

## <a name="risky-sign-in-remediation"></a>Riskli oturum açma düzeltmesi

Yönetici oturum açma riskleri için bir ilke yapılandırmışsa, etkilenen kullanıcılara, oturum açmaya ve ilke risk düzeyini tetiklemeye çalıştıklarında bildirim yapılır. 

### <a name="risky-sign-in-self-remediation"></a>Riskli oturum açma kendi kendini düzeltme

1. Kullanıcı, yeni bir konumdan, cihazdan veya uygulamadan oturum açma gibi, oturum açma bilgileri hakkında olağan dışı bir şey olduğunu bilgilendirilir.
   
    ![Bir olağan dışı istem](./media/concept-identity-protection-user-experience/120.png)

1. Kullanıcının, önceden kaydedilmiş yöntemlerinden biriyle Azure AD MFA 'yı tamamlayarak kimliklerini kanıtlamaları gerekir. 

### <a name="risky-sign-in-administrator-unblock"></a>Riskli oturum açma Yöneticisi engellemeyi kaldır

Yöneticiler, risk düzeyine bağlı olarak oturum açarken kullanıcıları engellemeyi seçebilirler. Engellenmemiş olması için son kullanıcıların BT personeline başvurmaları veya tanıdık bir konumdan veya cihazdan oturum açmayı deneyebilmeleri gerekir. Multi-Factor Authentication 'ı gerçekleştirerek kendi kendini düzeltme, bu durumda bir seçenek değildir.

![Oturum açma risk ilkesi tarafından engellendi](./media/concept-identity-protection-user-experience/200.png)

BT personeli, kullanıcıların oturum açmalarına izin vermek için [kullanıcıların engellemesini kaldırma](howto-identity-protection-remediate-unblock.md#unblocking-based-on-sign-in-risk) bölümündeki yönergeleri takip edebilir.

## <a name="risky-user-remediation"></a>Riskli Kullanıcı düzeltmesi

Bir Kullanıcı risk ilkesi yapılandırıldığında, Kullanıcı risk düzeyi güvenliğinin aşılmasına neden olan kullanıcıların, oturum açmadan önce Kullanıcı güvenliğinin aşılmasına karşı koruma akışını geçmesi gerekir. 

### <a name="risky-user-self-remediation"></a>Riskli Kullanıcı kendini düzeltme

1. Kullanıcı, şüpheli etkinlik veya sızdırılan kimlik bilgileri nedeniyle hesap güvenliğinin risk altında olduğunu bilgilendirilir.
   
    ![Düzeltme](./media/concept-identity-protection-user-experience/101.png)

1. Kullanıcının, önceden kaydedilmiş yöntemlerinden biriyle Azure AD MFA 'yı tamamlayarak kimliklerini kanıtlamaları gerekir. 
1. Son olarak, başka birinin hesabına erişimi olduğundan, Kullanıcı self servis parola sıfırlama kullanarak parolalarını değiştirmeye zorlanır.

## <a name="risky-sign-in-administrator-unblock"></a>Riskli oturum açma Yöneticisi engellemeyi kaldır

Yöneticiler, risk düzeyine bağlı olarak oturum açarken kullanıcıları engellemeyi seçebilirler. Engellenmemiş hale getirmek için, son kullanıcılar BT personeline başvurmalıdır. Multi-Factor Authentication ve self servis parola sıfırlama işlemlerini gerçekleştirerek kendi kendini düzeltme, bu durumda bir seçenek değildir.

![Kullanıcı risk ilkesi tarafından engellendi](./media/concept-identity-protection-user-experience/104.png)

BT personeli, kullanıcıların oturum açmalarına izin vermek için [kullanıcıların engellemesini kaldırma](howto-identity-protection-remediate-unblock.md#unblocking-based-on-user-risk) bölümündeki yönergeleri takip edebilir.

## <a name="see-also"></a>Ayrıca bkz.

- [Riskleri düzeltin ve kullanıcıların engellemesini kaldırın](howto-identity-protection-remediate-unblock.md)

- [Azure Active Directory Kimlik Koruması](./overview-identity-protection.md)