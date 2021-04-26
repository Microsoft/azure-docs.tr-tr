---
title: Azure AD özel rolünü güncelleştirme veya kaldırma-Privileged Identity Management (PıM)
description: Azure AD özel rol atamasını güncelleştirme veya kaldırma Privileged Identity Management (PıM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a35442dd8af1cd4acf22de453c8d10460e1e39f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371537"
---
# <a name="update-or-remove-an-assigned-azure-ad-custom-role-in-privileged-identity-management"></a>Privileged Identity Management atanan bir Azure AD özel rolünü güncelleştirme veya kaldırma

Bu makalede, Azure Active Directory (Azure AD) yönetim deneyiminde uygulama yönetimi için oluşturulan özel rollere tam zamanında ve zamana göre atamayı güncelleştirmek ya da kaldırmak için Privileged Identity Management (PıM) ' nin nasıl kullanılacağı açıklanır. 

- Azure AD 'de uygulama yönetimi için özel roller oluşturma hakkında daha fazla bilgi için, bkz. [Azure Active Directory (Önizleme) Içinde özel yönetici rolleri](../roles/custom-overview.md). 
- Privileged Identity Management henüz kullanmadıysanız [Privileged Identity Management kullanmaya başlama](pim-getting-started.md)hakkında daha fazla bilgi alın.

> [!NOTE]
> Azure AD özel rolleri, önizleme sırasında yerleşik Dizin rolleriyle tümleştirilmiştir. Yetenek genel kullanıma sunulduğunda, rol yönetimi yerleşik roller deneyiminde gerçekleşmeyecektir. Aşağıdaki başlığı görürseniz, bu rollerin [yerleşik roller deneyiminde](pim-how-to-add-role-to-user.md) yönetilmesi gerekir ve bu makale uygulanmaz:
>
> [![Azure AD > Privileged Identity Management seçin.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="update-or-remove-an-assignment"></a>Atama güncelleştirme veya kaldırma

Mevcut bir özel rol atamasını güncelleştirmek veya kaldırmak için bu adımları izleyin.

1. Azure portal [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) Için, ayrıcalıklı rol yöneticisi rolüne atanan bir kullanıcı hesabıyla oturum açın.
1. **Azure AD özel rollerini (Önizleme)** seçin.

    ![Uygun rol atamalarını görmek için Azure AD özel rolleri Önizlemesi ' ni seçin](./media/azure-ad-custom-roles-assign/view-custom.png)

1. Azure AD uygulamaları için özel rollerin **atamalar** listesini görmek için **Roller** ' i seçin.

    ![Rolleri seçin uygun rol atamaları listesini görüntüleyin](./media/azure-ad-custom-roles-update-remove/assignments-list.png)

1. Güncelleştirmek veya kaldırmak istediğiniz rolü seçin.
1. **Uygun roller** veya **etkin roller** sekmelerinde rol atamasını bulun.
1. Rol atamasını güncelleştirmek veya kaldırmak için **Güncelleştir** ' i veya **Kaldır** ' ı seçin.

    ![Uygun rol atamasında Kaldır veya Güncelleştir ' i seçin](./media/azure-ad-custom-roles-update-remove/remove-update.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure AD özel rolünü etkinleştirme](azure-ad-custom-roles-assign.md)
- [Azure AD özel rolü atama](azure-ad-custom-roles-assign.md)
- [Azure AD özel rol ataması yapılandırma](azure-ad-custom-roles-configure.md)
