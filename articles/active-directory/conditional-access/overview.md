---
title: Azure Active Directory Koşullu erişim nedir?
description: Koşullu erişimin yeni kimlik temelli denetim düzlemi kalbinde nasıl olduğunu öğrenin.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/17/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0463ffad87d00421c2fcb5c8357406d5f692144
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71075313"
---
# <a name="what-is-conditional-access"></a>Koşullu Erişim nedir?

Modern güvenlik çevre birimi artık bir kuruluşun ağının ötesinde Kullanıcı ve cihaz kimliğini içerir. Kuruluşlar bu kimlik sinyallerini, erişim denetimi kararlarının bir parçası olarak kullanabilir. 

Koşullu erişim, sinyalleri bir araya getirmek, kararlar almak ve kuruluş ilkelerini zorlamak için Azure Active Directory tarafından kullanılan araçtır. Koşullu erişim, yeni kimlik temelli denetim düzlemi 'nin kalmadır.

![Kavramsal koşullu sinyal ve zorlaması alma kararı](./media/overview/conditional-access-signal-decision-enforcement.png)

Koşullu erişim ilkeleri en basit deyişle, bir Kullanıcı bir kaynağa erişmek isterse, bir eylemi tamamlaması gerekir. Örnek: Bir bordro Yöneticisi, bordro uygulamasına erişmek istiyor ve bu uygulamaya erişmek için Multi-Factor Authentication gerçekleştirmek istiyor.

Yöneticiler iki birincil hedefle başlatılacaktır:

- Kullanıcıların her yerde ve her zaman üretken olmasını sağlama
- Kuruluşun varlıklarını koruma

Koşullu erişim ilkelerini kullanarak, kuruluşunuzun güvenliğini sağlamak için gerektiğinde doğru erişim denetimlerini uygulayabilir ve gerek duyulmadığında kullanıcının yolunu takip edebilirsiniz.

![Kavramsal koşullu erişim işlemi akışı](./media/overview/conditional-access-overview-how-it-works.png)

Koşullu erişim ilkeleri, ilk faktör kimlik doğrulaması tamamlandıktan sonra zorlanır. Koşullu erişim, kuruluşun hizmet reddi (DoS) saldırıları gibi senaryolar için ilk savunma hattı olarak tasarlanmamıştır, ancak erişimi anlamak için bu olaylardan gelen sinyalleri kullanabilir.

## <a name="common-signals"></a>Ortak sinyaller

Bir ilke kararı verirken, Koşullu erişimin hesaba götürebileceğine ilişkin yaygın sinyaller aşağıdaki sinyalleri içerir:

- Kullanıcı veya grup üyeliği
   - İlkeler, yöneticilere erişim üzerinde ayrıntılı denetim sağlayan belirli kullanıcıları ve grupları hedefleyebilir.
- IP konumu bilgileri
   - Kuruluşlar, ilke kararları verirken kullanılabilecek güvenilen IP adresi aralıkları oluşturabilir. 
   - Yöneticiler, trafiği engellemek veya buna izin vermek için tüm ülkelerin IP aralıklarını belirtebilir.
- Cihaz
   - Belirli platformları olan veya belirli bir durumla işaretlenmiş kullanıcılar, koşullu erişim ilkelerini zorlarken kullanılabilir.
- Uygulama
   - Belirli uygulamalara erişmeye çalışan kullanıcılar, farklı koşullu erişim ilkeleri tetikleyebilir. 
- Gerçek zamanlı ve hesaplanan risk algılama
   - Azure AD Kimlik Koruması ile tümleştirme sinyalleri, koşullu erişim ilkelerinin riskli oturum açma davranışını belirlemesine izin verir. İlkeler daha sonra kullanıcıların, risk düzeylerini azaltmak için parola değişiklikleri veya çok faktörlü kimlik doğrulaması gerçekleştirmesine zorlayabilir veya bir yönetici el ile eylem yapana kadar erişim engellenir.
- Microsoft Cloud App Security (MCAS)
   - Kullanıcı uygulama erişiminin ve oturumlarının gerçek zamanlı olarak izlenebilmesini ve denetlenmesini, bulut ortamınızda gerçekleştirilen ve erişim üzerinde görünürlük ve denetim işlemlerini arttırmayı ve bu işlemleri ele almasını sağlar.

## <a name="common-decisions"></a>Yaygın kararlar

- Erişimi engelle
   - En kısıtlayıcı karar
- Erişim izni ver
   - En az kısıtlayıcı karar, aşağıdaki seçeneklerden birini veya birkaçını hala gerektirebilir:
      - Multi-Factor Authentication gerektir
      - Cihazın uyumlu olarak işaretlenmesini gerektir
      - Karma Azure AD 'ye katılmış cihaz gerektir
      - Onaylı istemci uygulaması gerektir
      - Uygulama koruma ilkesi gerektir (Önizleme)

## <a name="commonly-applied-policies"></a>Yaygın olarak uygulanan ilkeler

Birçok kuruluş, koşullu erişim ilkelerinin şu gibi yardımcı olabilecek yaygın erişim kaygılarına sahiptir:

- Yönetici rollerine sahip kullanıcılar için Multi-Factor Authentication gerektirme
- Azure yönetim görevleri için Multi-Factor Authentication gerektirme
- Eski kimlik doğrulama protokollerini kullanmaya çalışan kullanıcılar için oturum açma işlemlerini engelleme
- Azure Multi-Factor Authentication kaydı için güvenilir konumlar gerektirme
- Belirli konumlardan erişimi engelleme veya verme
- Riskli oturum açma davranışlarını engelleme
- Belirli uygulamalar için kuruluş tarafından yönetilen cihazlar gerektirme

## <a name="license-requirements"></a>Lisans gereksinimleri

[!INCLUDE [Active Directory P1 license](../../../includes/active-directory-p1-license.md)]

[Microsoft 365 iş lisanslarına](https://docs.microsoft.com/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) sahip müşterilerin koşullu erişim özelliklerine de erişimi vardır. 

## <a name="next-steps"></a>Sonraki adımlar

[Bir koşullu erişim ilkesi parçasını parça olarak oluşturma](concept-conditional-access-policies.md)

Ortamınızda Koşullu erişimin nasıl uygulanacağını öğrenmek için, bkz. [Azure Active Directory Koşullu erişim dağıtımınızı planlayın](plan-conditional-access.md).

[Kimlik koruması hakkında bilgi edinin](../identity-protection/overview-v2.md)

[Microsoft Cloud App Security hakkında bilgi edinin](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)

[Microsoft Intune hakkında bilgi edinin](https://docs.microsoft.com/intune/index)