---
title: Ayrıcalıklı erişim grupları için uygun sahipleri ve üyeleri atama-Azure Active Directory
description: Azure AD Privileged Identity Management (PıM) içinde rol atanabilir bir grubun uygun sahiplerini veya üyelerini atamayı öğrenin.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 08/18/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48c4473cfafce1215219251c47bce1d5730645fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91534429"
---
# <a name="assign-eligibility-for-a-privileged-access-group-preview-in-privileged-identity-management"></a>Privileged Identity Management bir ayrıcalıklı erişim grubu (Önizleme) için uygunluk atayın

Azure Active Directory (Azure AD) Privileged Identity Management (PıM), Azure AD 'de ayrıcalıklı erişim grupları için atamaların uygunluğunu ve etkinleştirilmesini yönetmenize yardımcı olabilir. Grubun üyelerine veya sahibine uygunluk atayabilirsiniz.

>[!NOTE]
>Ayrıcalıklı erişim grubuna üye veya sahiplik için uygun olan her kullanıcının bir Azure AD Premium P2 Lisansı olmalıdır. Daha fazla bilgi için bkz. [Privileged Identity Management kullanılacak lisans gereksinimleri](subscription-requirements.md).

## <a name="assign-an-owner-or-member-of-a-group"></a>Bir grubun sahibini veya üyesini atama

Bir kullanıcının bir ayrıcalıklı erişim grubunun üyesi veya sahibi olmasını uygun hale getirmek için bu adımları izleyin.

1. [Azure AD 'de](https://aad.portal.azure.com) genel yönetici veya grup sahibi Izinleriyle oturum açın.
1. **Gruplar** ' ı seçin ve ardından yönetmek istediğiniz rol atanabilir grubu seçin. Listede arama yapabilir veya filtre uygulayabilirsiniz.

    ![PıM içinde yönetilecek rol atanabilir bir grup bulun](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. Grubu açın ve **ayrıcalıklı erişim (Önizleme)** seçeneğini belirleyin.

    ![Privileged Identity Management deneyimini açın](./media/groups-assign-member-owner/groups-discover-groups.png)

1. **Atama Ekle**' yi seçin.

    ![Yeni atama bölmesi](./media/groups-assign-member-owner/groups-add-assignment.png)

1. Ayrıcalıklı erişim grubu için uygun hale getirmek istediğiniz üyeleri veya sahipleri seçin.

    !["Üye veya grup seçin" bölmesinin açık olduğunu ve "Seç" düğmesinin vurgulandığı "atama Ekle" sayfasını gösteren ekran görüntüsü.](./media/groups-assign-member-owner/add-assignments.png)

1. Üyeliği veya sahiplik süresini ayarlamak için **İleri ' yi** seçin.

    ![Üye veya grup bölmesi seçin](./media/groups-assign-member-owner/assignment-duration.png)

1. **Atama türü** listesinde **uygun** veya **etkin**' i seçin. Ayrıcalıklı erişim grupları iki farklı atama türü sağlar:

    - **Uygun** atamalar, rolü kullanmak için bir eylem gerçekleştirmek üzere rolün üyesini gerektirir. Eylemler, bir Multi-Factor Authentication (MFA) denetimi gerçekleştirmeye, iş gerekçesinin sağlanmasından veya belirlenen onaylayanlardan onay isteğinde bulunabilir.

    - **Etkin** atamalar, üyenin rolü kullanmak için herhangi bir eylem gerçekleştirmesini gerektirmez. Etkin olarak atanan üyelerin her zaman role atanan ayrıcalıkları vardır.

1. Atama kalıcı olmalıdır (kalıcı olarak uygun veya kalıcı olarak atanabilir), **kalıcı** onay kutusunu seçin. Kuruluşunuzun ayarlarına bağlı olarak, onay kutusu görünmeyebilir veya düzenlenemeyebilir.

1. İşiniz bittiğinde **ata**' yı seçin.

1. Yeni rol atamasını oluşturmak için **Ekle**' yi seçin. Durum bildirimi görüntülenir.

    ![Yeni atama-bildirim](./media/groups-assign-member-owner/groups-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Var olan bir rol atamasını güncelleştirme veya kaldırma

Varolan bir rol atamasını güncelleştirmek veya kaldırmak için bu adımları izleyin.

1. [Azure AD 'de](https://aad.portal.azure.com) genel yönetici veya grup sahibi Izinleriyle oturum açın.
1. **Gruplar** ' ı seçin ve ardından yönetmek istediğiniz rol atanabilir grubu seçin. Listede arama yapabilir veya filtre uygulayabilirsiniz.

    ![PıM içinde yönetilecek rol atanabilir bir grup bulun](./media/groups-assign-member-owner/groups-list-in-azure-ad.png)

1. Grubu açın ve **ayrıcalıklı erişim (Önizleme)** seçeneğini belirleyin.

    ![Privileged Identity Management deneyimini açın](./media/groups-assign-member-owner/groups-discover-groups.png)

1. Güncelleştirmek veya kaldırmak istediğiniz rolü seçin.

1. **Uygun roller** veya **etkin roller** sekmelerinde rol atamasını bulun.

    ![Rol atamasını güncelleştirme veya kaldırma](./media/groups-assign-member-owner/groups-bring-under-management.png)

1. Rol atamasını güncelleştirmek veya kaldırmak için **Güncelleştir** ' i veya **Kaldır** ' ı seçin.

    Rol atamasını genişletme hakkında daha fazla bilgi için, bkz. [Privileged Identity Management Azure Kaynak rollerini genişletme veya yenileme](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management Azure Kaynak rollerini genişletme veya yenileme](pim-resource-roles-renew-extend.md)
- [Privileged Identity Management Azure Kaynak rolü ayarlarını yapılandırma](pim-resource-roles-configure-role-settings.md)
- [Privileged Identity Management Azure AD rolleri atama](pim-how-to-add-role-to-user.md)
