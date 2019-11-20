---
title: PıM-Azure Active Directory 'de e-posta bildirimleri | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) ' de e-posta bildirimlerini açıklar.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/05/2019
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee5f2edbae28276f8485ae774a5b1c52e1af2fd1
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756394"
---
# <a name="email-notifications-in-pim"></a>PıM 'de e-posta bildirimleri

Privileged Identity Management (PıM), bir rolün atanma veya etkinleştirilme gibi, Azure Active Directory (Azure AD) kuruluşunuzda önemli olayların ne zaman meydana geldiğinden haberdar olmanızı sağlar. Privileged Identity Management sizi ve diğer katılımcı e-posta bildirimlerini göndererek sizi bilgilendirerek haberdar olmanızı sağlayabilirsiniz. Bu e-postalar, bir rolün etkinleştirilmesi veya yenilenmesi gibi ilgili görevlere ait bağlantıları da içerebilir. Bu makalede, bu e-postaların ne zaman gönderileceğini ve kimlerin bunları alacağını açıklanmaktadır.

## <a name="sender-email-address-and-subject-line"></a>Gönderenin e-posta adresi ve konu satırı

Hem Azure AD hem de Azure Kaynak rolleri için Privileged Identity Management gönderilen e-postaların aşağıdaki Gönderici e-posta adresi vardır:

- E-posta adresi: **Azure-noreply \@microsoft. com**
- Görünen ad: Microsoft Azure

Bu e-postalar konu satırında bir **PIM** öneki içerir. Bir örneği aşağıda verilmiştir:

- PıM: Alain Charon, yedekleme okuyucusu rolüne kalıcı olarak atandı

## <a name="notifications-for-azure-ad-roles"></a>Azure AD rolleri için bildirimler

Privileged Identity Management, Azure AD rolleri için aşağıdaki olaylar gerçekleştiğinde e-posta gönderir:

- Ayrıcalıklı bir rol etkinleştirmesi onay bekliyor olduğunda
- Ayrıcalıklı bir rol etkinleştirme isteği tamamlandığında
- Azure AD Privileged Identity Management etkinleştirildiğinde

Bu e-postaları Azure AD rolleri için alan rolünüz, olayınıza ve bildirimler ayarlarına bağlıdır:

| Kullanıcı | Rol etkinleştirme onay bekliyor | Rol etkinleştirme isteği tamamlandı | PıM etkin |
| --- | --- | --- | --- |
| Ayrıcalıklı rol yöneticisi</br>(Etkin/uygun) | Yes</br>(yalnızca açık onaylayanlar belirtilmemişse) | Evet* | Yes |
| Güvenlik Yöneticisi</br>(Etkin/uygun) | Hayır | Evet* | Yes |
| Genel Yönetici</br>(Etkin/uygun) | Hayır | Evet* | Yes |

[ **Bildirimler** ayarı](pim-how-to-change-default-settings.md#notifications) **Enable**olarak ayarlandıysa \*.

Aşağıda, bir Kullanıcı kurgusal contoso organizasyonu için bir Azure AD rolünü etkinleştirdiğinde gönderilen örnek bir e-posta gösterilmektedir.

![Azure AD rolleri için yeni Privileged Identity Management e-posta](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>Azure AD rolleri için haftalık Privileged Identity Management Özet e-postası

Azure AD rolleri için haftalık Privileged Identity Management Özet e-postası, Privileged Identity Management etkinleştirilmiş ayrıcalıklı rol yöneticilerine, güvenlik yöneticilerine ve genel yöneticilere gönderilir. Bu haftalık e-posta, hem hafta hem de ayrıcalıklı rol atamaları için Privileged Identity Management etkinliklerin anlık görüntüsünü sağlar. Yalnızca genel buluttaki kiracılar için kullanılabilir. Örnek bir e-posta aşağıda verilmiştir:

![Azure AD rolleri için haftalık Privileged Identity Management Özet e-postası](./media/pim-email-notifications/email-directory-weekly.png)

E-postada dört kutucuk bulunur:

| Kaldır | Açıklama |
| --- | --- |
| **Etkinleştirilen kullanıcılar** | Kullanıcıların kiracı içinde uygun rollerini etkinleştirme sayısı. |
| **Kullanıcılar kalıcı yaptı** | Uygun atamaya sahip kullanıcıların kalıcı hale getirilme sayısı. |
| **Privileged Identity Management rol atamaları** | Privileged Identity Management içinde kullanıcılara uygun bir rol atama sayısı. |
| **PıM dışında rol atamaları** | Kullanıcıların Privileged Identity Management dışında kalıcı bir rol atanma sayısı (Azure AD içinde). |

**En üst roller bölümünün genel bakışı** , kiracınızdaki ilk beş rolü her bir rol için toplam kalıcı ve uygun yönetici sayısına göre listeler. **Eylem al** bağlantısı, kalıcı yöneticileri toplu gruplar halinde uygun yöneticilere dönüştürebileceğiniz [PIM Sihirbazı](pim-security-wizard.md) ' nı açar.

## <a name="pim-emails-for-azure-resource-roles"></a>Azure Kaynak rolleri için PıM e-postaları

Privileged Identity Management, Azure Kaynak rolleri için aşağıdaki olaylar gerçekleştiğinde Sahibe ve Kullanıcı erişimi yöneticilerine e-posta gönderir:

- Bir rol ataması onay bekliyor olduğunda
- Bir rol atandığında
- Bir rolün kullanım süresini yakında dolacak
- Bir rol genişletmeye uygun olduğunda
- Bir rol Son Kullanıcı tarafından yenilendiğinde
- Bir rol etkinleştirme isteği tamamlandığında

Privileged Identity Management, Azure Kaynak rolleri için aşağıdaki olaylar gerçekleştiğinde son kullanıcılara e-posta gönderir:

- Kullanıcıya bir rol atandığında
- Bir kullanıcının rolü süre dolduğunda
- Bir kullanıcının rolü genişletildiğinde
- Bir kullanıcının rol etkinleştirme isteği tamamlandığında

Aşağıda, bir kullanıcıya kurgusal contoso organizasyonu için bir Azure Kaynak rolü atandığında gönderilen örnek bir e-posta gösterilmektedir.

![Azure Kaynak rolleri için yeni Privileged Identity Management e-posta](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management Azure AD rol ayarlarını yapılandırma](pim-how-to-change-default-settings.md)
- [Privileged Identity Management Azure AD rolleri için istekleri onaylama veya reddetme](azure-ad-pim-approval-workflow.md)
