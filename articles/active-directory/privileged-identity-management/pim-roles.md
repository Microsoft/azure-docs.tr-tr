---
title: Privileged Identity Management içinde yönetilemez roller-Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) içinde yönetebileceğiniz rolleri açıklar.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fac7074cf85a585c93ece60be9eea8ffb9a6345
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72895198"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Privileged Identity Management içinde yönetileyemiyorum roller

Azure Active Directory (Azure AD) Privileged Identity Management (PıM), tüm [Azure AD rollerini](../users-groups-roles/directory-assign-admin-roles.md) ve tüm [Azure Kaynak rollerini](../../role-based-access-control/built-in-roles.md)yönetmenize olanak sağlar. Bu roller ayrıca yönetim gruplarınız, abonelikleriniz, kaynak grupları ve kaynaklarınıza iliştirilmiş özel rollerinizi de içerir. Ancak, yönetebilmeniz gereken birkaç rol vardır. Bu makalede, Privileged Identity Management içinde yönetiyoruz roller açıklanmaktadır.

## <a name="classic-subscription-administrator-roles"></a>Klasik abonelik yönetici rolleri

Privileged Identity Management ' de aşağıdaki klasik abonelik yöneticisi rollerini yönetemezsiniz:

- Hesap Yöneticisi
- Hizmet Yöneticisi
- Ortak Yönetici

Klasik abonelik yöneticisi rolleri hakkında daha fazla bilgi için bkz. [Klasik abonelik yöneticisi rolleri, Azure RBAC rolleri ve Azure AD yönetici rolleri](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-office-365-admin-roles"></a>Office 365 Yönetici rolleri hakkında ne olacak?

Exchange Yöneticisi ve SharePoint Yöneticisi dışında, Exchange Online veya SharePoint Online içindeki roller, Azure AD 'de gösterilmediğinden Privileged Identity Management yönetilemiyor. Bu Office 365 hizmetleri hakkında daha fazla bilgi için bkz. [office 365 Yönetici rolleri](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> SharePoint Yöneticisi SharePoint Online Yönetim Merkezi aracılığıyla SharePoint Online 'a yönetici erişimine sahiptir ve SharePoint Online 'da neredeyse tüm görevleri gerçekleştirebilir. Uygun kullanıcılar, Privileged Identity Management ' de etkinleştirdikten sonra SharePoint içindeki bu rolü kullanarak gecikmelere neden olabilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management Azure AD rolleri atama](pim-how-to-add-role-to-user.md)
- [Privileged Identity Management Azure Kaynak rolleri atama](pim-resource-roles-assign-roles.md)
