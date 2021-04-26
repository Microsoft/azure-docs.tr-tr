---
title: Davet bağlantısı veya e-posta olmadan B2B konukları ekleme-Azure AD
description: Bir Konuk kullanıcının Azure AD 'nize, Azure Active Directory B2B işbirliğinde davet etmeden başka Konuk kullanıcılar eklemesine izin verebilirsiniz.
documentationcenter: ''
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 08/05/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: b18279e44bc5a3fd668d2ec4af6be29229af1b6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87910341"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation-link-or-email"></a>Bir davet bağlantısı veya e-posta olmadan B2B işbirliği Konuk kullanıcıları ekleme

Artık, paylaşılan bir uygulamaya [doğrudan bağlantı](redemption-experience.md#redemption-through-a-direct-link) göndererek Konuk kullanıcıları davet edebilirsiniz. Bu yöntemde, bazı özel durumlar dışında, konuk kullanıcıların artık davet e-postasını kullanması gerekmez. Konuk Kullanıcı uygulama bağlantısını tıklatır, gizlilik koşullarını gözden geçirir ve kabul eder ve ardından uygulamaya sorunsuz bir şekilde erişir. Daha fazla bilgi için bkz. [B2B işbirliği daveti satın](redemption-experience.md)alma.

Bu yeni yöntem kullanılabilir olmadan önce, davet e-postası gerekmeden Konuk kullanıcıları davet e-postası gerektirmeden davet edebilir (kuruluştan veya bir iş ortağı kuruluştan) **konuk davet** eden dizin rolüne davet edebilir ve ardından davet eden kullanıcıyı, Kullanıcı arabirimi veya PowerShell aracılığıyla dizine, gruplara veya uygulamalara Konuk kullanıcı eklemesini sağlayabilirsiniz. (PowerShell kullanıyorsanız, davet e-postasını tamamen gizleyebilirsiniz). Örnek:

1. Konak kuruluştaki bir Kullanıcı (örneğin, WoodGrove) bir kullanıcıyı iş ortağı kuruluştan (örneğin, Sam@litware.com ) Konuk olarak davet eder.
2. Konak kuruluştaki yönetici, Sam 'ın iş ortağı kuruluştan diğer kullanıcıları tanımlamasına ve eklemesine izin veren [ilkeleri ayarlar](delegate-invitations.md) (Litwlar). (Sam, **konuk davet** eden rolüne eklenmelidir.)
3. Artık, Sam, diğer kullanıcıları, davetlerin kullanılması gerekmeden WoodGrove dizinine, gruplara veya uygulamalarına daha fazla kullanıcı ekleyebilir. Sam, Litwde uygun numaralandırma ayrıcalıklarına sahipse, bu otomatik olarak gerçekleşir.
 
Bu özgün yöntem hala işe yarar. Ancak, davranışta küçük bir farklılık vardır. PowerShell kullanıyorsanız, davet edilen bir Konuk hesabının hemen **kabul edilmesi** yerine bir **pendingkabulünü** durumu olduğunu fark edeceksiniz. Durum beklemede olsa da Konuk Kullanıcı, e-posta davet bağlantısına tıklamadan uygulamaya oturum açabilir ve uygulamaya erişebilir. Bekleyen durum, kullanıcının, davet eden kuruluşun gizlilik koşullarını kabul ettiği [onay deneyiminden](redemption-experience.md#consent-experience-for-the-guest)gitmediği anlamına gelir. Konuk Kullanıcı ilk kez oturum açtıklarında bu onay ekranını görür. 

Bir kullanıcıyı dizine davet ederseniz, Konuk kullanıcının, gizlilik koşullarını görüntülemek ve kabul etmek için doğrudan kaynak kiracıya özgü Azure portal URL 'sine ( https://portal.azure.com/ *resourcetenant*. onmicrosoft.com gibi) erişmesi gerekir.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD B2B işbirliği nedir?](what-is-b2b.md)
- [B2B işbirliği daveti satın alma](redemption-experience.md)
- [Azure Active Directory B2B işbirliği için temsilci davetleri](delegate-invitations.md)
- [Bilgi çalışanları B2B işbirliği kullanıcılarını nasıl ekler?](add-users-information-worker.md)

