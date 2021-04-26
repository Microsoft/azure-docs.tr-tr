---
title: Azure rollerini REST API kullanarak atama-Azure RBAC
description: REST API ve Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak kullanıcılar, gruplar, hizmet sorumluları veya yönetilen kimlikler için Azure kaynaklarına nasıl erişim sağlayacağınızı öğrenin.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 04/06/2021
ms.author: rolyon
ms.openlocfilehash: 3baf44a4240b23b41ce2e80dc22dbda4c7d0672a
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363725"
---
# <a name="assign-azure-roles-using-the-rest-api"></a>REST API kullanarak Azure rolleri atama

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Bu makalede, REST API kullanarak rollerin nasıl atanacağı açıklanır.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="assign-an-azure-role"></a>Azure rolü atama

Rol atamak için, [rol atamalarını kullanın-REST API oluşturun](/rest/api/authorization/roleassignments/create) ve güvenlik sorumlusunu, rol tanımını ve kapsamı belirtin. Bu API 'yi çağırmak için işleme erişiminizin olması gerekir `Microsoft.Authorization/roleAssignments/write` . Yerleşik rollerde, yalnızca [sahip](built-in-roles.md#owner) ve [Kullanıcı erişim yöneticisine](built-in-roles.md#user-access-administrator) bu işleme erişim izni verilir.

1. Atamak istediğiniz rol tanımının tanımlayıcısını almak için [rol tanımları-liste](/rest/api/authorization/roledefinitions/list) REST API kullanın veya [yerleşik roller](built-in-roles.md) ' e bakın.

1. Rol atama tanımlayıcısı için kullanılacak benzersiz bir tanımlayıcı oluşturmak için bir GUID aracı kullanın. Tanımlayıcının biçimi vardır: `00000000-0000-0000-0000-000000000000`

1. Aşağıdaki istek ve gövdeyi başlatın:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```

1. URI içinde, *{scope}* yerine rol atamasının kapsamını koyun.

    > [!div class="mx-tableFixed"]
    > | Kapsam | Tür |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Yönetim grubu |
    > | `subscriptions/{subscriptionId1}` | Abonelik |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Kaynak grubu |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Kaynak |

    Önceki örnekte, Microsoft. Web bir App Service örneğine başvuran bir kaynak sağlayıcıdır. Benzer şekilde, diğer herhangi bir kaynak sağlayıcısını kullanabilir ve kapsamı belirtebilirsiniz. Daha fazla bilgi için bkz. [Azure kaynak sağlayıcıları ve türleri](../azure-resource-manager/management/resource-providers-and-types.md) ve desteklenen [Azure Kaynak sağlayıcısı işlemleri](resource-provider-operations.md).  

1. *{Roleatamamentıd}* değerini rol atamasının GUID tanımlayıcısı ile değiştirin.

1. İstek gövdesi içinde, *{scope}* değerini rol atamasının kapsamıyla değiştirin.

    > [!div class="mx-tableFixed"]
    > | Kapsam | Tür |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Yönetim grubu |
    > | `subscriptions/{subscriptionId1}` | Abonelik |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Kaynak grubu |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/microsoft.web/sites/mysite1` | Kaynak |

1. *{Roledefinitionıd}* değerini rol tanımı tanımlayıcısı ile değiştirin.

1. *{PrincipalId}* öğesini, role atanacak olan Kullanıcı, Grup veya hizmet sorumlusunun nesne tanımlayıcısıyla değiştirin.

Aşağıdaki istek ve gövde, abonelik kapsamındaki bir kullanıcıya [yedekleme okuyucusu](built-in-roles.md#backup-reader) rolünü atar:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId1}/providers/microsoft.authorization/roleassignments/{roleAssignmentId1}?api-version=2015-07-01
```

```json
{
  "properties": {
    "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
    "principalId": "{objectId1}"
  }
}
```

Aşağıda çıktının bir örneği gösterilmektedir:

```json
{
    "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
        "principalId": "{objectId1}",
        "scope": "/subscriptions/{subscriptionId1}",
        "createdOn": "2020-05-06T23:55:23.7679147Z",
        "updatedOn": "2020-05-06T23:55:23.7679147Z",
        "createdBy": null,
        "updatedBy": "{updatedByObjectId1}"
    },
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId1}",
    "type": "Microsoft.Authorization/roleAssignments",
    "name": "{roleAssignmentId1}"
}
```

### <a name="new-service-principal"></a>Yeni hizmet sorumlusu

Yeni bir hizmet sorumlusu oluşturur ve bu hizmet sorumlusuna hemen bir rol atamayı denerseniz, bu rol ataması bazı durumlarda başarısız olabilir. Örneğin, yeni bir yönetilen kimlik oluşturup bu hizmet sorumlusuna bir rol atamayı denerseniz, rol ataması başarısız olabilir. Bu hatanın nedeni büyük olasılıkla çoğaltma gecikmesi. Hizmet sorumlusu tek bir bölgede oluşturulur; Ancak, rol ataması henüz hizmet sorumlusunu çoğaltılmamış farklı bir bölgede gerçekleşebilir.

Bu senaryoya yönelik olarak, [-Create REST API rol atamalarını](/rest/api/authorization/roleassignments/create) kullanın ve `principalType` özelliğini olarak ayarlayın `ServicePrincipal` . Ayrıca, veya sonraki bir sürümünü de ayarlamanız gerekir `apiVersion` `2018-09-01-preview` .

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}?api-version=2018-09-01-preview
```

```json
{
  "properties": {
    "roleDefinitionId": "/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
    "principalId": "{principalId}",
    "principalType": "ServicePrincipal"
  }
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [REST API kullanarak Azure rol atamalarını listeleyin](role-assignments-list-rest.md)
- [Kaynakları Resource Manager şablonları ve Resource Manager REST API’si ile dağıtma](../azure-resource-manager/templates/deploy-rest.md)
- [Azure REST API Başvurusu](/rest/api/azure/)
- [REST API kullanarak Azure özel rolleri oluşturun veya güncelleştirin](custom-roles-rest.md)
