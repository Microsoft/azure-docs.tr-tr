---
title: Rol tabanlı erişim denetimini anlama-Azure dijital TWINS | Microsoft Docs
description: Azure dijital TWINS 'de rol tabanlı erişim denetimi ve izinleri yönetme hakkında bilgi edinin.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: e89a8f98d92e92fa8afe93340576b14ff1dd5051
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249193"
---
# <a name="role-based-access-control-in-azure-digital-twins"></a>Azure dijital TWINS 'de rol tabanlı erişim denetimi

Azure dijital TWINS, uzamsal grafiğinizde belirli veriler, kaynaklar ve eylemler üzerinde kesin erişim denetimi imkanı sunar. Bu, ayrıntılı rol ve [rol tabanlı erişim denetimi](https://docs.microsoft.com/azure/role-based-access-control/) (RBAC) adlı izin yönetimini kolaylaştırır. RBAC, _Roller_ ve _rol atamalarından_oluşur. Roller, izin düzeyini belirler. Rol atamaları bir rolü kullanıcı veya cihazla ilişkilendirir.

RBAC kullanarak, izin verilebilir:

- Bir kullanıcı.
- Bir cihaz.
- Hizmet sorumlusu.
- Kullanıcı tanımlı bir işlev.
- Bir etki alanına ait olan tüm kullanıcılar.
- Bir kiracı.

Erişim derecesi de ince ayar yapılabilir.

RBAC, uzamsal grafiğin Devralındığı izinlerle benzersizdir.

## <a name="what-can-i-do-with-rbac"></a>RBAC ile ne yapabilirim?

Geliştirici, RBAC 'yi kullanarak şunları alabilir:

- Kullanıcıya, tüm bina için veya yalnızca belirli bir oda veya zemin için cihazları yönetme yeteneği verin.
- Tüm grafik veya yalnızca grafiğin bir bölümü için tüm uzamsal grafik düğümlerine yönetici genel erişimi verin.
- Erişim tuşları dışında, grafiğe bir destek uzmanı okuma erişimi verin.
- Tüm grafik nesnelerine bir etki alanı okuma erişiminin her üyesine izin verin.

## <a name="rbac-best-practices"></a>RBAC en iyi uygulamaları

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-rbac-best-practices.md)]

## <a name="roles"></a>Roller

### <a name="role-definitions"></a>Rol tanımları

Rol tanımı bir rol oluşturan izinlerin ve diğer özniteliklerin koleksiyonudur. Rol tanımı, bu role sahip herhangi bir nesnenin gerçekleştirebileceği *oluşturma*, *okuma*, *güncelleştirme*ve *silme* işlemlerini içeren izin verilen işlemleri listeler. Ayrıca, izinlerin hangi nesne türlerine uygulanacağını de belirtir.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

>[!NOTE]
> Önceki rollerin tam tanımlarını almak için, sistem/rol API 'sini sorgulayın.
> [Rol atamaları oluşturmayı ve yönetmeyi](./security-create-manage-role-assignments.md#retrieve-all-roles)okuyarak daha fazla bilgi edinin.

### <a name="object-identifier-types"></a>Nesne tanımlayıcı türleri

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

>[!TIP]
> [Rol atamaları oluşturma ve yönetme](./security-create-manage-role-assignments.md#grant-permissions-to-your-service-principal)konusunu okuyarak hizmet sorumlusuna izin verme hakkında bilgi edinin.

Aşağıdaki başvuru belgesi makaleleri şunları anlatmaktadır:

- [Bir Kullanıcı Için sorgu veya nesne kimliği](https://docs.microsoft.com/powershell/module/azuread/get-azureaduser?view=azureadps-2.0).
- [Hizmet sorumlusu için nesne kimliğini edinme](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal).
- [Bir Azure AD kiracısı için nesne kimliğini alma](../active-directory/develop/quickstart-create-new-tenant.md).

## <a name="role-assignments"></a>Rol atamaları

Bir Azure dijital TWINS rol ataması, bir kullanıcı veya Azure AD kiracısı gibi bir nesneyi bir rol ve boşluk ile ilişkilendirir. Bu alana ait olan tüm nesnelere izinler verilir. Alan, altındaki tüm uzamsal grafiği içerir.

Örneğin, bir kullanıcıya bir yapı temsil eden uzamsal grafiğin kök düğümü için `DeviceInstaller` rolüne sahip bir rol ataması verilir. Kullanıcı daha sonra bu düğüm ve binanın diğer tüm alt alanları için cihazları okuyabilir ve güncelleştirebilir.

Bir alıcıya izin vermek için bir rol ataması oluşturun. İzinleri iptal etmek için rol atamasını kaldırın.

>[!IMPORTANT]
> [Rol atamaları oluşturma ve yönetme](./security-create-manage-role-assignments.md)ile rol atamaları hakkında daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar

- Azure dijital TWINS rol atamaları oluşturma ve yönetme hakkında daha fazla bilgi edinmek için [rol atamaları oluşturma ve yönetme](./security-create-manage-role-assignments.md)konusunu okuyun.

- [Azure Için RBAC](https://docs.microsoft.com/azure/role-based-access-control/)hakkında daha fazla bilgi edinin.
