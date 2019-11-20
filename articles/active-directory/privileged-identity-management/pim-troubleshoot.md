---
title: Privileged Identity Management ile ilgili sorun giderme-Azure Active Directory | Microsoft Docs
description: Azure AD Privileged Identity Management (PıM) içindeki rollerle sistem hatalarını nasıl giderebileceğinizi öğrenin.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/18/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: f54382d652c3fc59b8ff462d41ec8c0fbdbe8498
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596701"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>Privileged Identity Management sorun giderme

Azure Active Directory (Azure AD) içinde Privileged Identity Management (PıM) ile ilgili bir sorun var mı? Aşağıdaki bilgiler, yeniden çalışan işlemleri almanıza yardımcı olabilir.

## <a name="access-to-azure-resources-denied"></a>Azure kaynaklarına erişim reddedildi

### <a name="problem"></a>Sorun

Bir Azure kaynağı için etkin bir sahip veya Kullanıcı erişimi Yöneticisi olarak, kaynağınızı Privileged Identity Management içinde görebilir, ancak uygun bir atama yapma ya da kaynağın rol atamalarının bir listesini görüntüleme gibi işlemleri gerçekleştiremeyeceksiniz Genel Bakış sayfası. Bu eylemlerin herhangi biri bir yetkilendirme hatasına neden olur.

### <a name="cause"></a>Nedeni

Bu sorun, PıM hizmeti sorumlusu için Kullanıcı erişimi yönetici rolü yanlışlıkla aboneliğinden kaldırıldığında meydana gelebilir. Privileged Identity Management hizmetinin Azure kaynaklarına erişebilmesi için, MS-PıM hizmet sorumlusuna her zaman Azure aboneliği üzerinden [Kullanıcı erişimi yönetici rolü](../../role-based-access-control/built-in-roles.md#user-access-administrator) atanmalıdır.

### <a name="resolution"></a>Çözünürlük

Kullanıcı erişimi Yöneticisi rolünü, abonelik düzeyinde ayrıcalıklı kimlik yönetimi hizmet asıl adına (MS – PıM) atayın. Bu atama, ayrıcalıklı kimlik yönetimi hizmetinin Azure kaynaklarına erişmesine izin verilmelidir. Rol, gereksinimlerinize bağlı olarak bir yönetim grubu düzeyinde veya abonelik düzeyinde atanabilir. Daha fazla bilgi hizmet sorumlusu için bkz. [bir role uygulama atama](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role).

## <a name="next-steps"></a>Sonraki adımlar

- [Privileged Identity Management kullanılacak lisans gereksinimleri](subscription-requirements.md)
- [Azure AD'de karma ve bulut dağıtımları için ayrıcalıklı erişim güvenliğini sağlama](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Privileged Identity Management dağıt](pim-deployment-plan.md)
