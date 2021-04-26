---
title: Azure Active Directory Koşullu erişim nedir?
description: Koşullu erişimin yeni kimlik temelli denetim düzlemi kalbinde nasıl olduğunu öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 01/27/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4, azuread-video-2020
ms.openlocfilehash: 7b43cf23b3f01e64c0be31ed013aa990ff80d3db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98938336"
---
# <a name="what-is-conditional-access"></a>Koşullu Erişim nedir?

Modern güvenlik çevre birimi artık bir kuruluşun ağının ötesinde Kullanıcı ve cihaz kimliğini içerir. Kuruluşlar bu kimlik sinyallerini, erişim denetimi kararlarının bir parçası olarak kullanabilir. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MwZs]

Koşullu erişim, sinyalleri bir araya getirmek, kararlar almak ve kuruluş ilkelerini zorlamak için Azure Active Directory tarafından kullanılan araçtır. Koşullu erişim, yeni kimlik temelli denetim düzlemi 'nin kalmadır.

![Kavramsal koşullu sinyal ve zorlaması alma kararı](./media/overview/conditional-access-signal-decision-enforcement.png)

Koşullu erişim ilkeleri en basit deyişle, bir Kullanıcı bir kaynağa erişmek isterse, bir eylemi tamamlaması gerekir. Örnek: bir bordro Yöneticisi, bordro uygulamasına erişmek istiyor ve bu uygulamaya erişmek için Multi-Factor Authentication gerçekleştirmek istiyor.

Yöneticiler iki birincil hedefle başlatılacaktır:

- Kullanıcıların her yerde ve her zaman üretken olmasını sağlama
- Kuruluşun varlıklarını koruma

Koşullu erişim ilkelerini kullanarak, kuruluşunuzun güvenliğini sağlamak için gerektiğinde doğru erişim denetimlerini uygulayabilir ve gerek duyulmadığında kullanıcının yolunu takip edebilirsiniz.

![Kavramsal koşullu erişim işlemi akışı](./media/overview/conditional-access-overview-how-it-works.png)

> [!IMPORTANT]
> Koşullu erişim ilkeleri, ilk faktör kimlik doğrulaması tamamlandıktan sonra zorlanır. Koşullu erişimin, bir kuruluşun hizmet reddi (DoS) saldırıları gibi senaryolar için ilk savunma hattı olması amaçlanmamıştır, ancak erişimi anlamak için bu olaylardan gelen sinyalleri kullanabilir.

## <a name="common-signals"></a>Ortak sinyaller

Bir ilke kararı verirken, Koşullu erişimin hesaba götürebileceğine ilişkin yaygın sinyaller aşağıdaki sinyalleri içerir:

- Kullanıcı veya grup üyeliği
   - İlkeler, yöneticilere erişim üzerinde ayrıntılı denetim sağlayan belirli kullanıcıları ve grupları hedefleyebilir.
- IP konumu bilgileri
   - Kuruluşlar, ilke kararları verirken kullanılabilecek güvenilen IP adresi aralıkları oluşturabilir. 
   - Yöneticiler, trafiği engellemek veya izin vermek için tüm ülke/bölge IP aralıklarını belirtebilir.
- Cihaz
   - Belirli platformları olan veya belirli bir durumla işaretlenmiş kullanıcılar, koşullu erişim ilkelerini zorlarken kullanılabilir.
- Uygulama
   - Belirli uygulamalara erişmeye çalışan kullanıcılar, farklı koşullu erişim ilkeleri tetikleyebilir. 
- Gerçek zamanlı ve hesaplanan risk algılama
   - Azure AD Kimlik Koruması ile tümleştirme sinyalleri, koşullu erişim ilkelerinin riskli oturum açma davranışını belirlemesine izin verir. İlkeler daha sonra kullanıcıların, risk düzeylerini azaltmak için parola değişiklikleri veya çok faktörlü kimlik doğrulaması gerçekleştirmesine zorlayabilir veya bir yönetici el ile eylem yapana kadar erişim engellenir.
- Microsoft Cloud App Security (MCAS)
   - Kullanıcı uygulama erişiminin ve oturumlarının gerçek zamanlı olarak izlenebilmesini ve denetlenmesini, bulut ortamınızda gerçekleştirilen ve erişim üzerinde görünürlük ve denetim işlemlerini arttırmayı ve bu işlemleri ele almasını sağlar.

## <a name="common-decisions"></a>Yaygın kararlar

- Erişimi engelleme
   - En kısıtlayıcı karar
- Erişim verme
   - En az kısıtlayıcı karar, aşağıdaki seçeneklerden birini veya birkaçını hala gerektirebilir:
      - Multi-Factor Authentication gerektir
      - Cihazın uyumlu olarak işaretlenmesini gerektir
      - Karma Azure AD 'ye katılmış cihaz gerektir
      - Onaylanan istemci uygulaması gerektir
      - Uygulama koruma ilkesi gerektir (Önizleme)

## <a name="commonly-applied-policies"></a>Yaygın olarak uygulanan ilkeler

Birçok kuruluş, [koşullu erişim ilkelerinin şu gibi yardımcı olabilecek yaygın erişim kaygılarına](concept-conditional-access-policy-common.md) sahiptir:

- Yönetici rollerine sahip kullanıcılar için Multi-Factor Authentication gerektirme
- Azure yönetim görevleri için Multi-Factor Authentication gerektirme
- Eski kimlik doğrulama protokollerini kullanmaya çalışan kullanıcılar için oturum açma işlemlerini engelleme
- Azure AD Multi-Factor Authentication kaydı için güvenilir konumlar gerektirme
- Belirli konumlardan erişimi engelleme veya verme
- Riskli oturum açma davranışlarını engelleme
- Belirli uygulamalar için kuruluş tarafından yönetilen cihazlar gerektirme

## <a name="license-requirements"></a>Lisans gereksinimleri

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

[Microsoft 365 iş Premium lisanslanan](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) müşterilerin koşullu erişim özelliklerine erişimi de vardır. 

[Oturum açma riskine](concept-conditional-access-conditions.md#sign-in-risk) [kimlik koruması](../identity-protection/overview-identity-protection.md) için erişim gerekir

## <a name="next-steps"></a>Sonraki adımlar

- [Bir koşullu erişim ilkesi parçasını parça olarak oluşturma](concept-conditional-access-policies.md)
- [Koşullu Erişim dağıtımınızı planlama](plan-conditional-access.md)
- [Kimlik koruması hakkında bilgi edinin](../identity-protection/overview-identity-protection.md)
- [Microsoft Cloud App Security hakkında bilgi edinin](/cloud-app-security/what-is-cloud-app-security)
- [Microsoft Intune hakkında bilgi edinin](/intune/index)
