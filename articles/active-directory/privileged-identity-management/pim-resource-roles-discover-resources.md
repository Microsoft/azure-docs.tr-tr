---
title: PıM-Azure AD 'de yönetilecek Azure kaynaklarını bulma | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) içinde yönetilecek Azure kaynaklarını bulmayı öğrenin.
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
ms.date: 09/29/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42d38990289169412f06b0c7e4bcbdf67f688da7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99539018"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>Privileged Identity Management içinde yönetilecek Azure kaynaklarını bulma

Azure Active Directory (Azure AD) Privileged Identity Management (PıM) kullanarak Azure kaynaklarınızın korunmasını geliştirebilirsiniz. Bu, şu durumlarda yararlı olur:

- Azure AD rollerini korumak için zaten Privileged Identity Management kullanan kuruluşlar
- Üretim kaynaklarını güvenli hale getirmeye çalışan yönetim grubu ve abonelik sahipleri

Azure kaynakları için Privileged Identity Management ilk kez ayarladığınızda, Privileged Identity Management korunacak kaynakları bulmanız ve seçmeniz gerekir. Privileged Identity Management ile yönetebileceğiniz kaynak sayısı için bir sınır yoktur. Ancak, en kritik üretim kaynaklarınızın başlamasını öneririz.

## <a name="discover-resources"></a>Kaynak bulma

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. **Azure AD Privileged Identity Management** açın.

1. **Azure kaynakları**' nı seçin.

    Azure kaynakları için Privileged Identity Management ilk kez kullanıyorsanız, bir **bulma kaynakları** sayfası görürsünüz.

    ![İlk deneyime göre listelenen kaynakları içeren kaynakları bul bölmesi](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Kuruluşunuzdaki başka bir yönetici zaten Privileged Identity Management ' de Azure kaynaklarını yönetiyor ise, şu anda yönetilmekte olan kaynakların bir listesini görürsünüz.

    ![Şu anda yönetilmekte olan kaynakları listelemeyi bul kaynak bölmesi](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Bulma deneyimini başlatmak için **kaynakları bul** ' u seçin.

    ![Keşif bölmesi, abonelikler ve yönetim grupları gibi yönetilebilecek kaynakları listeler](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. **Bulma** sayfasında, **kaynak durumu Filtresi** ' ni kullanın ve yazma izninizin olduğu yönetim gruplarını veya abonelikleri filtrelemek için **kaynak türü** ' nü seçin. **Başlangıçta başlamak** en kolay yoldur.

   Privileged Identity Management yönetmek için yönetim grubu veya abonelik kaynakları arayabilir ve seçebilirsiniz. Privileged Identity Management bir yönetim grubunu veya aboneliği yönetirken, alt kaynaklarını da yönetebilirsiniz.

   > [!Note]
   > PıM ile yönetilen bir yönetim grubuna yeni bir alt Azure kaynağı eklediğinizde, PıM 'de arayarak alt kaynağı yönetim altına getirebilirsiniz.

1. Yönetmek istediğiniz yönetilmeyen kaynakları seçin.

1. Seçilen kaynakları yönetmeye başlamak için **kaynağı Yönet** ' i seçin.

    > [!NOTE]
    > Bir yönetim grubu veya abonelik yönetilmiyorsa, yönetilmeyen olamaz. Bu, başka bir kaynak yöneticisinin Privileged Identity Management ayarlarını kaldırmasını engeller.

    ![Kaynak seçiliyken bulma bölmesi ve kaynağı Yönet seçeneği vurgulandı](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Seçili kaynağı yönetim için eklemeyi onaylamak üzere bir ileti görürseniz, **Evet**' i seçin. PıM daha sonra kaynak (ler) in altındaki tüm yeni ve mevcut alt nesneleri yönetecek şekilde yapılandırılır.

    ![Seçili kaynakları yönetim için ekleme onayı bildiren ileti](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management Azure Kaynak rolü ayarlarını yapılandırma](pim-resource-roles-configure-role-settings.md)
- [Privileged Identity Management Azure Kaynak rolleri atama](pim-resource-roles-assign-roles.md)
