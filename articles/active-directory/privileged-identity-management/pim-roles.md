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
ms.date: 05/11/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: c72587c5486ed61215fd20c215a1dd194f4b7bc4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92372421"
---
# <a name="roles-you-cant-manage-in-privileged-identity-management"></a>Privileged Identity Management içinde yönetileyemiyorum roller

Azure Active Directory (Azure AD) Privileged Identity Management (PıM), tüm [Azure AD rollerini](../roles/permissions-reference.md) ve tüm [Azure rollerini](../../role-based-access-control/built-in-roles.md)yönetmenize olanak sağlar. Azure rolleri, yönetim gruplarınız, abonelikleriniz, kaynak grupları ve kaynaklarınıza iliştirilmiş özel rollerinizi de içerebilir. Ancak, yönetebilmeniz gereken birkaç rol vardır. Bu makalede, Privileged Identity Management içinde yönetileyemiyorum roller açıklanmaktadır.

## <a name="classic-subscription-administrator-roles"></a>Klasik abonelik yönetici rolleri

Privileged Identity Management ' de aşağıdaki klasik abonelik yöneticisi rollerini yönetemezsiniz:

- Hesap Yöneticisi
- Hizmet Yöneticisi
- Ortak Yönetici

Klasik abonelik yöneticisi rolleri hakkında daha fazla bilgi için bkz. [Klasik abonelik yöneticisi rolleri, Azure rolleri ve Azure AD yönetici rolleri](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="what-about-microsoft-365-admin-roles"></a>Yönetici rolleri ne Microsoft 365?

Exchange Yöneticisi ve SharePoint Yöneticisi gibi Azure AD rolleri ve yöneticiler portalı deneyiminde tüm Microsoft 365 rollerini destekliyoruz, ancak Exchange RBAC veya SharePoint RBAC içindeki belirli rolleri desteklemiyoruz. Bu Microsoft 365 hizmetleri hakkında daha fazla bilgi için bkz. [Microsoft 365 Yönetici rolleri](/office365/admin/add-users/about-admin-roles).

> [!NOTE]
> SharePoint yönetici rolü için uygun kullanıcılar, Cihaz Yöneticisi rolü ve Microsoft Güvenlik ve Uyumluluk Merkezi 'ne erişmeye çalışan tüm roller, rollerini etkinleştirdikten sonra birkaç saate kadar gecikme yaşar. Bu ekiplerle ilgili sorunları gidermek için çalışıyoruz.

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management Azure AD rolleri atama](pim-how-to-add-role-to-user.md)
- [Privileged Identity Management Azure Kaynak rolleri atama](pim-resource-roles-assign-roles.md)