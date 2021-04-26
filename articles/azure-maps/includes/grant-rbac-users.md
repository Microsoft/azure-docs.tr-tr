---
title: Kullanıcılar için rol tabanlı erişim verme
titleSuffix: Azure Maps
description: Kullanıcılara Azure Maps yetkilendirmesi sağlamak için rol tabanlı erişim denetimi kullanma
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 234c3358b98b7af677793fba58c1602a8bd976f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101102827"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Kullanıcılara Azure Maps 'e rol tabanlı erişim verme

Bir ya da daha fazla Azure Maps rol tanımına bir Azure AD grubu veya güvenlik sorumlusu atayarak *Azure rol tabanlı erişim denetimi (Azure RBAC)* verirsiniz. Azure haritalar için kullanılabilen Azure rol tanımlarını görüntülemek için **erişim denetimi 'ne (IAM)** gidin. **Roller**' i seçin ve ardından *Azure Maps* ile başlayan roller için arama yapın.

* Büyük miktarda kullanıcının Azure Maps 'e erişimini verimli bir şekilde yönetmek için bkz. [Azure AD grupları](../../active-directory/fundamentals/active-directory-manage-groups.md).
* Kullanıcıların uygulamada kimlik doğrulamasına izin verilmesi için kullanıcıların Azure AD 'de oluşturulması gerekir. Bkz. [Azure ad kullanarak Kullanıcı ekleme veya silme](../../active-directory/fundamentals/add-users-azure-active-directory.md).

Kullanıcıları bir dizini etkin bir şekilde yönetmek için [Azure AD](../../active-directory/fundamentals/index.yml) 'de daha fazla bilgi edinin.

1. **Azure haritalar hesabınıza** gidin. **Erişim denetimi (IAM)**  >  **rol atamasını** seçin.

    ![Azure RBAC kullanarak erişim verme](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. **Rol atamaları** sekmesinde, **rol** altında **Azure Maps veri okuyucusu** veya **Azure haritalar veri katılımcısı** gibi yerleşik bir Azure Maps rol tanımı seçin. **Erişim ata**' nın altında **Azure AD Kullanıcı, Grup veya hizmet sorumlusu**' nı seçin. Asıl ada göre ' yi seçin. Sonra **Kaydet**'i seçin.

   * [Azure rolleri atama](../../role-based-access-control/role-assignments-portal.md)hakkında ayrıntılara bakın.

> [!WARNING]
> Azure Maps yerleşik rol tanımları birçok Azure haritalar REST API 'Lerine çok büyük bir yetkilendirme erişimi sağlar. API 'Leri kullanıcılara en düşük düzeyde kısıtlamak için bkz. [özel rol tanımı oluşturma ve kullanıcıları](../../role-based-access-control/custom-roles.md) özel rol tanımına atama. Bu, kullanıcıların uygulama için gereken en düşük ayrıcalığa sahip olmasını sağlar.