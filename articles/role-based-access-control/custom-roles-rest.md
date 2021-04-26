---
title: REST API-Azure RBAC kullanarak Azure özel rolleri oluşturma veya güncelleştirme
description: REST API ve Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak Azure özel rollerini listeleme, oluşturma, güncelleştirme veya silme hakkında bilgi edinin.
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
ms.topic: how-to
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ab64765fc4e329c8edbf7a4db813113d20d476b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100557518"
---
# <a name="create-or-update-azure-custom-roles-using-the-rest-api"></a>REST API kullanarak Azure özel rolleri oluşturun veya güncelleştirin

> [!IMPORTANT]
> ' Ye bir yönetim grubu eklemek `AssignableScopes` Şu anda önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Azure yerleşik rolleri](built-in-roles.md) , kuruluşunuzun belirli ihtiyaçlarını karşılamıyorsa, kendi özel rollerinizi de oluşturabilirsiniz. Bu makalede, REST API kullanarak özel rolleri listeleme, oluşturma, güncelleştirme veya silme işlemlerinin nasıl yapılacağı açıklanır.

## <a name="list-custom-roles"></a>Özel rolleri listeleme

Bir dizindeki tüm özel rolleri listelemek için, [rol tanımları-liste](/rest/api/authorization/roledefinitions/list) REST API kullanın.

1. Aşağıdaki istekle başlayın:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. *{Filter}* öğesini rol türüyle değiştirin.

    > [!div class="mx-tableFixed"]
    > | Filtre | Description |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | CustomRole türünü temel alan filtre |

## <a name="list-custom-roles-at-a-scope"></a>Bir kapsamdaki özel rolleri listeleme

Özel rolleri bir kapsamda listelemek için, [rol tanımları-liste](/rest/api/authorization/roledefinitions/list) REST API kullanın.

1. Aşağıdaki istekle başlayın:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. URI içinde, *{scope}* değerini rolleri listelemek istediğiniz kapsamla değiştirin.

    > [!div class="mx-tableFixed"]
    > | Kapsam | Tür |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Abonelik |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Kaynak grubu |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Kaynak |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Yönetim grubu |

1. *{Filter}* öğesini rol türüyle değiştirin.

    > [!div class="mx-tableFixed"]
    > | Filtre | Description |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | CustomRole türünü temel alan filtre |

## <a name="list-a-custom-role-definition-by-name"></a>Özel bir rol tanımını ada göre Listele

Görünen adına göre özel bir rol hakkında bilgi almak için, [get REST API rol tanımlarını](/rest/api/authorization/roledefinitions/get) kullanın.

1. Aşağıdaki istekle başlayın:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. URI içinde, *{scope}* değerini rolleri listelemek istediğiniz kapsamla değiştirin.

    > [!div class="mx-tableFixed"]
    > | Kapsam | Tür |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Abonelik |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Kaynak grubu |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Kaynak |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Yönetim grubu |

1. *{Filter}* değerini rolün görünen adıyla değiştirin.

    > [!div class="mx-tableFixed"]
    > | Filtre | Description |
    > | --- | --- |
    > | `$filter=roleName+eq+'{roleDisplayName}'` | Rolün tam ekran adının URL kodlamalı formunu kullanın. Örneğin, `$filter=roleName+eq+'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>KIMLIĞE göre özel bir rol tanımı listeleyin

Özel bir rol hakkında benzersiz tanımlayıcı ile ilgili bilgi almak için, Get REST API [rol tanımlarını](/rest/api/authorization/roledefinitions/get) kullanın.

1. Rolün GUID tanımlayıcısını almak için [rol tanımları-liste](/rest/api/authorization/roledefinitions/list) REST API kullanın.

1. Aşağıdaki istekle başlayın:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. URI içinde, *{scope}* değerini rolleri listelemek istediğiniz kapsamla değiştirin.

    > [!div class="mx-tableFixed"]
    > | Kapsam | Tür |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Abonelik |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Kaynak grubu |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Kaynak |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Yönetim grubu |

1. *{Roledefinitionıd}* değerini rol tanımının GUID tanımlayıcısı ile değiştirin.

## <a name="create-a-custom-role"></a>Özel rol oluşturma

Özel bir rol oluşturmak için, rol tanımlarını kullanın; REST API [oluşturun veya güncelleştirin](/rest/api/authorization/roledefinitions/createorupdate) . Bu API 'yi çağırmak için, tüm için izni olan bir rol atanmış bir kullanıcıyla oturum açmalısınız `Microsoft.Authorization/roleDefinitions/write` `assignableScopes` . Yerleşik rollerde yalnızca [sahip](built-in-roles.md#owner) ve [Kullanıcı erişimi Yöneticisi](built-in-roles.md#user-access-administrator) bu izni içerir.

1. Özel rolünüzün izinlerini oluşturmak için kullanılabilir [kaynak sağlayıcısı işlemlerinin](resource-provider-operations.md) listesini gözden geçirin.

1. Özel rol tanımlayıcısı için kullanılacak benzersiz bir tanımlayıcı oluşturmak için bir GUID aracı kullanın. Tanımlayıcının biçimi vardır: `00000000-0000-0000-0000-000000000000`

1. Aşağıdaki istek ve gövdeyi başlatın:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. URI içinde, *{scope}* öğesini özel rolün birinciyle değiştirin `assignableScopes` .

    > [!div class="mx-tableFixed"]
    > | Kapsam | Tür |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Abonelik |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Kaynak grubu |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Yönetim grubu |

1. *{Roledefinitionıd}* öğesini özel rolün GUID tanımlayıcısı ile değiştirin.

1. İstek gövdesi içinde *{Roledefinitionıd}* değerini GUID tanımlayıcısı ile değiştirin.

1. `assignableScopes`Bir abonelik veya kaynak grubu ise, *{SubscriptionID}* veya *{resourceGroup}* örneklerini tanımlayıcılarınız ile değiştirin.

1. `assignableScopes`Bir yönetim grub, *{GroupID}* örneğini yönetim grubu tanımlayıcı ile değiştirin. ' Ye bir yönetim grubu eklemek `assignableScopes` Şu anda önizlemededir.

1. `actions`Özelliğinde, rolün gerçekleştirilmesine izin verdiği işlemleri ekleyin.

1. `notActions`Özelliğinde, izin verilen öğesinden hariç tutulan işlemleri ekleyin `actions` .

1. `roleName`Ve `description` Özellikleri ' nde, benzersiz bir rol adı ve açıklama belirtin. Özellikler hakkında daha fazla bilgi için bkz. [Azure özel roller](custom-roles.md).

    Aşağıda bir istek gövdesi örneği gösterilmektedir:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Özel rolü güncelleştirme

Özel bir rolü güncelleştirmek için, rol tanımlarını kullanın; REST API [oluşturun veya güncelleştirin](/rest/api/authorization/roledefinitions/createorupdate) . Bu API 'yi çağırmak için, tüm için izni olan bir rol atanmış bir kullanıcıyla oturum açmalısınız `Microsoft.Authorization/roleDefinitions/write` `assignableScopes` . Yerleşik rollerde yalnızca [sahip](built-in-roles.md#owner) ve [Kullanıcı erişimi Yöneticisi](built-in-roles.md#user-access-administrator) bu izni içerir.

1. Özel rol hakkında bilgi almak için [rol tanımları-liste](/rest/api/authorization/roledefinitions/list) veya [rol tanımları-al](/rest/api/authorization/roledefinitions/get) REST API kullanın. Daha fazla bilgi için, önceki [özel roller listesi](#list-custom-roles) bölümüne bakın.

1. Aşağıdaki istekle başlayın:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. URI içinde, *{scope}* öğesini özel rolün birinciyle değiştirin `assignableScopes` .

    > [!div class="mx-tableFixed"]
    > | Kapsam | Tür |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Abonelik |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Kaynak grubu |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Yönetim grubu |

1. *{Roledefinitionıd}* öğesini özel rolün GUID tanımlayıcısı ile değiştirin.

1. Özel rolle ilgili bilgilere bağlı olarak, aşağıdaki biçimde bir istek gövdesi oluşturun:

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. İstek gövdesini, özel rolde yapmak istediğiniz değişikliklerle güncelleştirin.

    Aşağıda yeni bir tanılama ayarları eylemi eklenen bir istek gövdesi örneği gösterilmektedir:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Insights/diagnosticSettings/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Özel rolü silme

Özel bir rolü silmek için, [rol tanımlarını kullanın-sil](/rest/api/authorization/roledefinitions/delete) REST API. Bu API 'yi çağırmak için, tüm için izni olan bir rol atanmış bir kullanıcıyla oturum açmalısınız `Microsoft.Authorization/roleDefinitions/delete` `assignableScopes` . Yerleşik rollerde yalnızca [sahip](built-in-roles.md#owner) ve [Kullanıcı erişimi Yöneticisi](built-in-roles.md#user-access-administrator) bu izni içerir.

1. [Rol tanımlarını-liste](/rest/api/authorization/roledefinitions/list) veya [rol tanımlarını kullanın-](/rest/api/authorization/roledefinitions/get) özel rolün guıd tanımlayıcısını almak için REST API alın. Daha fazla bilgi için, önceki [özel roller listesi](#list-custom-roles) bölümüne bakın.

1. Aşağıdaki istekle başlayın:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. URI içinde, *{scope}* öğesini özel rolü silmek istediğiniz kapsamla değiştirin.

    > [!div class="mx-tableFixed"]
    > | Kapsam | Tür |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Abonelik |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Kaynak grubu |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Yönetim grubu |

1. *{Roledefinitionıd}* öğesini özel rolün GUID tanımlayıcısı ile değiştirin.

## <a name="next-steps"></a>Sonraki adımlar

- [Özel Azure rolleri](custom-roles.md)
- [REST API kullanarak Azure rolleri atama](role-assignments-rest.md)
- [Azure REST API Başvurusu](/rest/api/azure/)
