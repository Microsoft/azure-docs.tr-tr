---
title: Privileged Identity Management Azure Active Directory Azure Kaynak rolleri atama | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) içinde Azure Kaynak rolleri atamayı öğrenin.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34051a31c6ccf69356f330d7c5ecb009f760857a
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895957"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Privileged Identity Management Azure Kaynak rolleri atama

Azure Active Directory (Azure AD) Privileged Identity Management (PıM), yerleşik Azure Kaynak rollerinin yanı sıra (ancak bunlarla sınırlı olmamak üzere) özel roller yönetebilir:

- Sahip
- Kullanıcı Erişimi Yöneticisi
- Katılımcı
- Güvenlik Yöneticisi
- Güvenlik Yöneticisi

> [!NOTE]
> Sahibine veya Kullanıcı erişimi Yöneticisi abonelik rollerine atanan bir grubun kullanıcıları veya üyeleri ve Azure AD 'de abonelik yönetimine olanak tanıyan Azure AD Genel yöneticileri, varsayılan olarak Kaynak Yöneticisi izinlerine sahiptir. Bu yöneticiler, rol atayabilir, rol ayarlarını yapılandırabilir ve Azure kaynakları için Privileged Identity Management kullanarak erişimi gözden geçirebilir. Kullanıcı, Kaynak Yöneticisi izinleri olmayan kaynaklar için Privileged Identity Management yönetemez. [Azure kaynakları için yerleşik rollerin](../../role-based-access-control/built-in-roles.md)listesini görüntüleyin.

## <a name="assign-a-role"></a>Rol atama

Bir kullanıcıyı Azure Kaynak rolüne uygun hale getirmek için aşağıdaki adımları izleyin.

1. [Ayrıcalıklı rol yöneticisi](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) rolünün üyesi olan bir kullanıcıyla [Azure Portal](https://portal.azure.com/) oturum açın.

    Privileged Identity Management yönetmek için başka bir yöneticiye erişim verme hakkında daha fazla bilgi için bkz. [Privileged Identity Management yönetmek için diğer yöneticilere erişim verme](pim-how-to-give-access-to-pim.md).

1. **Azure AD Privileged Identity Management**açın.

1. **Azure kaynakları**' nı seçin.

1. Yönetilen kaynakların listesini filtrelemek için **kaynak filtresini** kullanın.

    ![Yönetilecek Azure kaynakları listesi](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Yönetmek istediğiniz kaynağı bir abonelik veya yönetim grubu gibi seçin.

1. Yönet altında, Azure kaynakları rollerinin listesini görmek için **Roller** ' i seçin.

    ![Azure kaynakları rolleri](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Yeni atama bölmesini açmak için **üye Ekle** ' yi seçin.

1. Rol Seç bölmesini açmak için **Rol Seç** ' i seçin.

    ![Yeni atama bölmesi](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Atamak istediğiniz bir rol seçin ve ardından **Seç**' e tıklayın.

    Üye Seç veya grup bölmesi açılır.

1. Role atamak istediğiniz üye veya grubu seçin ve ardından **Seç**' e tıklayın.

    ![Üye veya grup bölmesi seçin](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    Üyelik ayarları bölmesi açılır.

1. **Atama türü** listesinde **uygun** veya **etkin**' i seçin.

    ![Üyelik ayarları bölmesi](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Azure kaynakları için Privileged Identity Management iki farklı atama türü sağlar:

    - **Uygun** atamalar, rolü kullanmak için bir eylem gerçekleştirmek üzere rolün üyesini gerektirir. Eylemler, bir Multi-Factor Authentication (MFA) denetimi gerçekleştirmeye, iş gerekçesinin sağlanmasından veya belirlenen onaylayanlardan onay isteğinde bulunabilir.

    - **Etkin** atamalar, üyenin rolü kullanmak için herhangi bir eylem gerçekleştirmesini gerektirmez. Etkin olarak atanan üyelerin her zaman role atanan ayrıcalıkları vardır.

1. Atama kalıcı olmalıdır (kalıcı olarak uygun veya kalıcı olarak atanabilir), **kalıcı** onay kutusunu seçin.

    Rol ayarlarına bağlı olarak, onay kutusu görünmeyebilir veya hiç değiştirilebilir olmayabilir.

1. Belirli bir atama süresi belirtmek için onay kutusunu temizleyin ve başlangıç ve/veya bitiş tarih ve saat kutularını değiştirin.

    ![Üyelik ayarları-Tarih ve saat](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. İşiniz bittiğinde **bitti**' yi seçin.

    ![Yeni atama-Ekle](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Yeni rol atamasını oluşturmak için **Ekle**' yi seçin. Durum bildirimi görüntülenir.

    ![Yeni atama-bildirim](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Var olan bir rol atamasını güncelleştirme veya kaldırma

Varolan bir rol atamasını güncelleştirmek veya kaldırmak için bu adımları izleyin.

1. **Azure AD Privileged Identity Management**açın.

1. **Azure kaynakları**' nı seçin.

1. Yönetmek istediğiniz kaynağı bir abonelik veya yönetim grubu gibi seçin.

1. Yönet altında, Azure kaynakları rollerinin listesini görmek için **Roller** ' i seçin.

    ![Azure Kaynak rolleri-rol seçin](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Güncelleştirmek veya kaldırmak istediğiniz rolü seçin.

1. **Uygun roller** veya **etkin roller** sekmelerinde rol atamasını bulun.

    ![Rol atamasını güncelleştirme veya kaldırma](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Rol atamasını güncelleştirmek veya kaldırmak için **Güncelleştir** ' i veya **Kaldır** ' ı seçin.

    Rol atamasını genişletme hakkında daha fazla bilgi için, bkz. [Privileged Identity Management Azure Kaynak rollerini genişletme veya yenileme](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management Azure Kaynak rollerini genişletme veya yenileme](pim-resource-roles-renew-extend.md)
- [Privileged Identity Management Azure Kaynak rolü ayarlarını yapılandırma](pim-resource-roles-configure-role-settings.md)
- [Privileged Identity Management Azure AD rolleri atama](pim-how-to-add-role-to-user.md)
