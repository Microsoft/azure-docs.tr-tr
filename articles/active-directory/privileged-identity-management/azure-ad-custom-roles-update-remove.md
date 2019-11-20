---
title: Privileged Identity Management bir Azure AD özel rol atamasını güncelleştirme veya kaldırma (PıM) | Microsoft Docs
description: Azure AD özel rol atamasını güncelleştirme veya kaldırma Privileged Identity Management (PıM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccbc9fbd763b9393a64d6cfc29f6b5a9d021da88
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756347"
---
# <a name="update-or-remove-an-assigned-azure-ad-custom-role-in-privileged-identity-management"></a>Privileged Identity Management atanan bir Azure AD özel rolünü güncelleştirme veya kaldırma

Bu makalede, Azure Active Directory (Azure AD) yönetim deneyiminde uygulama yönetimi için oluşturulan özel rollere tam zamanında ve zamana göre atamayı güncelleştirmek ya da kaldırmak için Privileged Identity Management (PıM) ' nin nasıl kullanılacağı açıklanır. 

- Azure AD 'de uygulama yönetimi için özel roller oluşturma hakkında daha fazla bilgi için, bkz. [Azure Active Directory (Önizleme) Içinde özel yönetici rolleri](../users-groups-roles/roles-custom-overview.md). 
- Privileged Identity Management henüz kullanmadıysanız [Privileged Identity Management kullanmaya başlama](pim-getting-started.md)hakkında daha fazla bilgi alın.

> [!NOTE]
> Azure AD özel rolleri, önizleme sırasında yerleşik Dizin rolleriyle tümleştirilmiştir. Yetenek genel kullanıma sunulduğunda, rol yönetimi rol yönetimi yerleşik roller deneyiminde gerçekleşmeyecektir.

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
