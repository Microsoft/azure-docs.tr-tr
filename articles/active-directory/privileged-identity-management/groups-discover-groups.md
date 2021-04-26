---
title: Privileged Identity Management-Azure AD 'de yönetilecek bir grubu belirler | Microsoft Docs
description: Rol atanabilir grupları Privileged Identity Management (PıM) içinde ayrıcalıklı erişim grupları olarak yönetmeye nasıl ekleneceğini öğrenin.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 08/03/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c9e2580d2a88fbbab755f0c3df2f923bdc45548
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101688357"
---
# <a name="bring-privileged-access-groups-preview-into-privileged-identity-management"></a>Ayrıcalıklı erişim gruplarını (Önizleme) Privileged Identity Management içine taşıyın

Azure Active Directory (Azure AD) ' de, rol atamalarını nasıl yöneteceğinizi basitleştirmek için bulut gruplarına Azure AD yerleşik rolleri atayabilirsiniz. Azure AD rollerini korumak ve erişimin güvenliğini sağlamak için artık bu grupların üyeleri veya sahipleri için tam zamanında erişimi yönetmek üzere Privileged Identity Management (PıM) kullanabilirsiniz. Privileged Identity Management bir Azure AD rolüne atanabilir grubu, bir ayrıcalıklı erişim grubu olarak yönetmek için, bunu PıM 'de Yönetim altına almanız gerekir.

## <a name="identify-groups-to-manage"></a>Yönetilecek grupları tanımla

[Azure Active Directory içinde rol atanabilir Grup oluşturma](../roles/groups-create-eligible.md)bölümünde açıklandığı gibi, Azure AD 'de rol atanabilir bir grup oluşturabilirsiniz. Privileged Identity Management ile yönetimi altına getirmek için grubun sahibi siz olursunuz.

1. [Azure AD 'de](https://aad.portal.azure.com) ayrıcalıklı rol yöneticisi rol Izinleriyle oturum açın.
1. **Gruplar** ' ı seçin ve ardından PIM içinde yönetmek istediğiniz rol atanabilir grubu seçin. Listede arama yapabilir ve filtre uygulayabilirsiniz.

    ![PıM içinde yönetilecek rol atanabilir bir grup bulun](./media/groups-discover-groups/groups-list-in-azure-ad.png)

1. Grubu açın ve **ayrıcalıklı erişim (Önizleme)** seçeneğini belirleyin.

    ![Privileged Identity Management deneyimini açın](./media/groups-discover-groups/groups-discover-groups.png)

1. PıM 'de atamaları yönetmeye başlayın.

    ![Privileged Identity Management atamaları yönetme](./media/groups-discover-groups/groups-bring-under-management.png)

> [!NOTE]
> Ayrıcalıklı bir erişim grubu yönetilmiyorsa, yönetim dışında alınamaz. Bu, başka bir kaynak yöneticisinin Privileged Identity Management ayarlarını kaldırmasını engeller.
>

> [!IMPORTANT]
> Ayrıcalıklı bir erişim grubu Azure Active Directory silinirse, grubun ayrıcalıklı erişim grupları (Önizleme) dikey penceresinden kaldırılması 24 saate kadar sürebilir. 
>


## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management 'da ayrıcalıklı erişim grubu atamalarını yapılandırma](pim-resource-roles-configure-role-settings.md)
- [Privileged Identity Management ayrıcalıklı erişim grupları atama](pim-resource-roles-assign-roles.md)
