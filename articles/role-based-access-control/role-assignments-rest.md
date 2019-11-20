---
title: RBAC ve REST API-Azure kullanarak Azure kaynaklarına erişimi yönetme | Microsoft Docs
description: Rol tabanlı erişim denetimi (RBAC) ve REST API kullanarak kullanıcılar, gruplar ve uygulamalar için Azure kaynaklarına erişimi yönetmeyi öğrenin. Buna erişimi listeleme, erişim verme ve erişimi kaldırma işlemleri dahildir.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 86ee030e8c97cf3033b9d2d76b8125c64ecf8065
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996462"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-rest-api"></a>RBAC ve REST API kullanarak Azure kaynaklarına erişimi yönetme

[Rol tabanlı erişim denetimi (RBAC)](overview.md) , Azure kaynaklarına erişimi yönetme yöntemidir. Bu makalede, RBAC ve REST API kullanarak kullanıcılar, gruplar ve uygulamalar için erişimi nasıl yöneteceğiniz açıklanmaktadır.

## <a name="list-access"></a>Erişimi listeleme

RBAC 'de, erişimi listelemek için rol atamalarını listeleyin. Rol atamalarını listelemek için, [rol atamaları-liste](/rest/api/authorization/roleassignments/list) REST API 'lerinden birini kullanın. Sonuçlarınızı iyileştirmek için bir kapsam ve isteğe bağlı bir filtre belirtirsiniz.

1. Aşağıdaki istekle başlayın:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. URI içinde, *{scope}* değerini rol atamalarını listelemek istediğiniz kapsamla değiştirin.

    | `Scope` | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Yönetim grubu |
    | `subscriptions/{subscriptionId1}` | Abonelik |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

    Önceki örnekte, Microsoft. Web bir App Service örneğine başvuran bir kaynak sağlayıcıdır. Benzer şekilde, diğer herhangi bir kaynak sağlayıcısını kullanabilir ve kapsamı belirtebilirsiniz. Daha fazla bilgi için bkz. [Azure kaynak sağlayıcıları ve türleri](../azure-resource-manager/resource-manager-supported-services.md) ve desteklenen [Azure Resource Manager kaynak sağlayıcısı işlemleri](resource-provider-operations.md).  
     
1. *{Filter}* değerini, rol atama listesini filtrelemek için uygulamak istediğiniz koşulla değiştirin.

    | Filtre | Açıklama |
    | --- | --- |
    | `$filter=atScope()` | Alt kapsamlardaki rol atamalarını dahil etmez, yalnızca belirtilen kapsam için rol atamalarını listeler. |
    | `$filter=principalId%20eq%20'{objectId}'` | Belirtilen kullanıcı, Grup veya hizmet sorumlusu için rol atamalarını listeler. |
    | `$filter=assignedTo('{objectId}')` | Belirtilen kullanıcı veya hizmet sorumlusu için rol atamalarını listeler. Kullanıcı, rol ataması olan bir grubun üyesiyse, bu rol ataması da listelenir. Bu filtre, Kullanıcı bir grubun üyesiyse ve bu grup rol ataması olan başka bir grubun üyesiyse, bu rol atamasının de listelenmediği anlamına gelen gruplar için geçişlidir. Bu filtre yalnızca bir kullanıcı veya hizmet sorumlusu için bir nesne KIMLIĞI kabul eder. Bir grup için nesne KIMLIĞI geçirilemez. |

## <a name="grant-access"></a>Erişim izni ver

RBAC'de erişim vermek için bir rol ataması oluşturmanız gerekir. Rol ataması oluşturmak için, [rol atamalarını kullanın-REST API oluşturun](/rest/api/authorization/roleassignments/create) ve güvenlik sorumlusunu, rol tanımını ve kapsamı belirtin. Bu API 'yi çağırmak için `Microsoft.Authorization/roleAssignments/write` işleme erişiminizin olması gerekir. Yerleşik rollerde, yalnızca [sahip](built-in-roles.md#owner) ve [Kullanıcı erişim yöneticisine](built-in-roles.md#user-access-administrator) bu işleme erişim izni verilir.

1. Atamak istediğiniz rol tanımının tanımlayıcısını almak için [rol tanımları-liste](/rest/api/authorization/roledefinitions/list) REST API kullanın veya [yerleşik roller](built-in-roles.md) ' e bakın.

1. Rol atama tanımlayıcısı için kullanılacak benzersiz bir tanımlayıcı oluşturmak için bir GUID aracı kullanın. Tanımlayıcının biçimi vardır:`00000000-0000-0000-0000-000000000000`

1. Aşağıdaki istek ve gövdeyi başlatın:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
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

    | `Scope` | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Yönetim grubu |
    | `subscriptions/{subscriptionId1}` | Abonelik |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. *{Roleatamaadı}* değerini rol atamasının GUID tanımlayıcısı ile değiştirin.

1. İstek gövdesi içinde, *{scope}* değerini rol atamasının kapsamıyla değiştirin.

    | `Scope` | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Yönetim grubu |
    | `subscriptions/{subscriptionId1}` | Abonelik |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. *{Roledefinitionıd}* değerini rol tanımı tanımlayıcısı ile değiştirin.

1. *{PrincipalId}* öğesini, role atanacak olan Kullanıcı, Grup veya hizmet sorumlusunun nesne tanımlayıcısıyla değiştirin.

## <a name="remove-access"></a>Erişimi kaldır

RBAC'de erişimi kaldırmak için rol atamasını kaldırmanız gerekir. Rol atamasını kaldırmak için, [atamaları-sil REST API rol atamalarını](/rest/api/authorization/roleassignments/delete) kullanın. Bu API 'yi çağırmak için `Microsoft.Authorization/roleAssignments/delete` işleme erişiminizin olması gerekir. Yerleşik rollerde, yalnızca [sahip](built-in-roles.md#owner) ve [Kullanıcı erişim yöneticisine](built-in-roles.md#user-access-administrator) bu işleme erişim izni verilir.

1. Rol atama tanımlayıcısını (GUID) alın. Bu tanımlayıcı, rol atamasını ilk oluşturduğunuzda döndürülür veya rol atamalarını listeleyerek alabilir.

1. Aşağıdaki istekle başlayın:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. URI içinde, *{scope}* değerini rol atamasını kaldırma kapsamı ile değiştirin.

    | `Scope` | Type |
    | --- | --- |
    | `providers/Microsoft.Management/managementGroups/{groupId1}` | Yönetim grubu |
    | `subscriptions/{subscriptionId1}` | Abonelik |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Resource group |
    | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/ providers/microsoft.web/sites/mysite1` | Resource |

1. *{Roleatamaadı}* değerini rol atamasının GUID tanımlayıcısı ile değiştirin.

## <a name="next-steps"></a>Sonraki adımlar

- [Kaynakları Resource Manager şablonları ve Resource Manager REST API’si ile dağıtma](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Azure REST API Başvurusu](/rest/api/azure/)
- [REST API kullanarak Azure kaynakları için özel roller oluşturma](custom-roles-rest.md)
