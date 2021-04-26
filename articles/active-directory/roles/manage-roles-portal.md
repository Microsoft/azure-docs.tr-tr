---
title: Kullanıcılara Azure AD rolleri atama-Azure Active Directory
description: Azure AD rolleri atayarak Azure Active Directory kullanıcılara erişim izni verme hakkında bilgi edinin.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 03/07/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36ced586db1b4e417e623431c137c43dac8ba56f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103466690"
---
# <a name="assign-azure-ad-roles-to-users"></a>Kullanıcılara Azure AD rolleri atama

Artık Azure AD Yönetim merkezinde Yönetici rollerinin tüm üyelerini görebilir ve yönetebilirsiniz. Rol atamalarını sık sık yönetiyorsanız, büyük olasılıkla bu deneyimi tercih edersiniz. Bu makalede Azure AD Yönetim Merkezi kullanılarak Azure AD rollerinin nasıl atanacağı açıklanır.

## <a name="assign-a-role"></a>Rol atama

1. Genel yönetici veya ayrıcalıklı rol yöneticisi izinleriyle [Azure AD Yönetim merkezinde](https://aad.portal.azure.com) oturum açın.

1. **Azure Active Directory** seçin.

1. Tüm kullanılabilir rollerin listesini görmek için **Roller ve yöneticiler '** i seçin.

    ![Roller ve yöneticiler sayfasının ekran görüntüsü](./media/manage-roles-portal/roles-and-administrators.png)

1. Atamalarını görmek için bir rol seçin.

    İhtiyaç duyduğunuz rolü bulmanıza yardımcı olmak için Azure AD, rol kategorilerine göre rollerin alt kümelerini gösterebilir. Yalnızca seçili türdeki rolleri göstermek için **tür** filtresine göz atın.

1. **Atama Ekle** ' yi seçin ve ardından bu role atamak istediğiniz kullanıcıları seçin.

    Aşağıdaki resimden farklı bir şey görürseniz, PıM kullanıp kullanmadığını doğrulamak için [Privileged Identity Management (PIM)](#privileged-identity-management-pim) içindeki notu okuyun.

    ![Yönetici rolü için izin listesi](./media/manage-roles-portal/add-assignments.png)

1. Rolü atamak için **Ekle** ' yi seçin.

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PıM)

[Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md)kullanarak ek yönetim ÖZELLIKLERI için **PIM 'de Yönet** seçeneğini belirleyebilirsiniz. Ayrıcalıklı rol yöneticileri "kalıcı" (rol içinde her zaman etkin) atamalarıyla "uygun" (yalnızca yükseltilmiş olduğunda rol) olarak değişiklik yapabilir. Privileged Identity Management yoksa, denemeye kaydolmak için **PIM 'de Yönet** ' i de seçebilirsiniz. Privileged Identity Management, bir [Azure AD Premium P2 lisans planı](../privileged-identity-management/subscription-requirements.md)gerektirir.

!["PıM 'de Yönet" eylemi seçiliyken "Kullanıcı Yöneticisi-atamaları" sayfasını gösteren ekran görüntüsü](./media/manage-roles-portal/member-list-pim.png)

Genel yönetici veya ayrıcalıklı rol yöneticisiyseniz, üyeleri kolayca ekleyebilir veya kaldırabilir, listeyi filtreleyebilir veya etkin atanan rollerini görmek için bir üyeyi seçebilirsiniz.

> [!Note]
> Azure AD Premium P2 lisansınız varsa ve Privileged Identity Management zaten kullanıyorsanız, tüm rol yönetimi görevleri Azure AD 'de değil, ayrıcalık kimlik yönetimi 'nde gerçekleştirilir.
>
> ![Zaten PıM kullanan ve Premium P2 lisansına sahip olan kullanıcılar için PıM 'de yönetilen Azure AD rolleri](./media/manage-roles-portal/pim-manages-roles-for-p2.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Azure AD Yönetim rolleri forumundan](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)bizimle paylaşabilirsiniz.
* Roller hakkında daha fazla bilgi için bkz. [Azure AD yerleşik rolleri](permissions-reference.md).
* Varsayılan Kullanıcı izinleri için bkz. [varsayılan Konuk ve üye Kullanıcı izinlerinin karşılaştırması](../fundamentals/users-default-permissions.md).
