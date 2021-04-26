---
title: Azure RBAC kapsamını anlama
description: Azure rol tabanlı erişim denetimi (Azure RBAC) kapsamı ve bir kaynağın kapsamını belirleme hakkında bilgi edinin.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 10/08/2020
ms.author: rolyon
ms.openlocfilehash: deee42c46c9b08bb265c972695b9319413d4fcb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555912"
---
# <a name="understand-scope-for-azure-rbac"></a>Azure RBAC kapsamını anlama

*Kapsam* , erişimin uygulandığı kaynak kümesidir. Bir rol atadığınızda, bir güvenlik sorumlusuna yalnızca gerçekten ihtiyacı olan erişimi vermek için kapsamı anlamanız önemlidir. Kapsamı sınırlayarak, güvenlik sorumlusu tehlikeye girerse, hangi kaynakların risk altında olduğunu sınırlayabilirsiniz.

## <a name="scope-levels"></a>Kapsam düzeyleri

Azure 'da, dört düzeyde bir kapsam belirtebilirsiniz: [Yönetim grubu](../governance/management-groups/overview.md), abonelik, [kaynak grubu](../azure-resource-manager/management/overview.md#resource-groups)ve kaynak. Kapsamlar üst-alt ilişkisiyle yapılandırılmıştır. Her hiyerarşi düzeyi kapsamı daha belirgin hale getirir. Bu kapsam düzeylerinden herhangi birine roller atayabilirsiniz. Seçtiğiniz düzey rolün ne kadar büyük ölçüde uygulanacağını belirler. Alt düzeyler, daha yüksek düzeylerdeki rol izinlerini miras alır. 

![Rol ataması kapsamı](./media/scope-overview/rbac-scope-no-label.png)

Yönetim grupları, aboneliklerin üzerindeki kapsam düzeyidir, ancak yönetim grupları daha karmaşık hiyerarşileri destekler. Aşağıdaki diyagramda, tanımlayabileceğiniz bir yönetim grupları ve abonelikler hiyerarşisi örneği gösterilmektedir. Yönetim grupları hakkında daha fazla bilgi için bkz. [Azure Yönetim grupları nelerdir?](../governance/management-groups/overview.md).

![Yönetim grubu ve abonelik hiyerarşisi](./media/scope-overview/rbac-scope-management-groups.png)

## <a name="scope-format"></a>Kapsam biçimi

Komut satırını kullanarak roller atarsanız, kapsamı belirtmeniz gerekir. Komut satırı araçları için kapsam, rol atamasının tam kapsamını tanımlayan potansiyel olarak uzun bir dizedir. Azure portal, bu kapsam genellikle *kaynak kimliği* olarak listelenir.

Kapsam, eğik çizgi (/) karakteriyle ayrılmış bir dizi tanımlayıcıdan oluşur. Bu dizeyi, yer tutucular () olmayan metnin sabit tanımlayıcılardan oluşan aşağıdaki hiyerarşiyi ifade etmek için düşünebilirsiniz `{}` :

```
/subscriptions
    /{subscriptionId}
        /resourcegroups
            /{resourceGroupName}
                /providers
                    /{providerName}
                        /{resourceType}
                            /{resourceSubType1}
                                /{resourceSubType2}
                                    /{resourceName}
```

- `{subscriptionId}` , kullanılacak aboneliğin KIMLIĞIDIR (bir GUID).
- `{resourcesGroupName}` , kapsayan kaynak grubunun adıdır.
- `{providerName}` kaynağı işleyen [kaynak sağlayıcısının](../azure-resource-manager/management/azure-services-resource-providers.md) adı, ardından `{resourceType}` `{resourceSubType*}` Bu kaynak sağlayıcıda daha fazla düzeyi belirler.
- `{resourceName}` , belirli bir kaynağı tanımlayan dizenin son bölümüdür.

Yönetim grupları, aboneliklerin üzerinde bir düzeydir ve en geniş (en az belirli) kapsamı vardır. Bu düzeydeki rol atamaları, yönetim grubu içindeki abonelikler için geçerlidir. Bir yönetim grubunun kapsamı aşağıdaki biçimdedir:

```
/providers
    /Microsoft.Management
        /managementGroups
            /{managmentGroupName}
```

## <a name="scope-examples"></a>Kapsam örnekleri

> [!div class="mx-tableFixed"]
> | Kapsam | Örnek |
> | --- | --- |
> | Yönetim grubu | `/providers/Microsoft.Management/managementGroups/marketing-group` |
> | Abonelik | `/subscriptions/00000000-0000-0000-0000-000000000000` |
> | Kaynak grubu | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales` |
> | Kaynak | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVirtualNetwork12345` |

## <a name="how-to-determine-the-scope-for-a-resource"></a>Bir kaynağın kapsamını belirleme

Bir yönetim grubu, abonelik veya kaynak grubu için kapsamı belirlenmesi oldukça basittir. Yalnızca adı ve abonelik KIMLIĞINI bilmeniz gerekir. Ancak, bir kaynağın kapsamını belirlemek biraz daha fazla iş alır. Bir kaynağın kapsamını belirleyebilmeniz için kullanabileceğiniz birkaç yol aşağıda verilmiştir.

- Azure portal, kaynağı açın ve Özellikler ' e bakın. Kaynak, kapsamı belirleyebileceğiniz **kaynak kimliğini** listelemelidir. Örneğin, bir depolama hesabı için kaynak kimlikleri aşağıda verilmiştir.

    ![Azure portal bir depolama hesabı için kaynak kimlikleri](./media/scope-overview/scope-resource-id.png)

- Diğer bir yöntem, kaynak kapsamında geçici olarak bir rol atamak için Azure portal kullanmak ve ardından rol atamasını listelemek için [Azure PowerShell](role-assignments-list-powershell.md) veya [Azure CLI](role-assignments-list-cli.md) 'yi kullanmaktır. Çıktıda kapsam bir özellik olarak listelenecektir.

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/pro
                         viders/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01
    DisplayName        : User
    SignInName         : user@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <principalId>
    ObjectType         : User
    CanDelegate        : False
    Description        :
    ConditionVersion   :
    Condition          :
    ```

    ```azurecli
    {
        "canDelegate": null,
        "condition": null,
        "conditionVersion": null,
        "description": null,
        "id": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{principalId}",
        "principalName": "user@contoso.com",
        "principalType": "User",
        "resourceGroup": "test-rg",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01",
        "type": "Microsoft.Authorization/roleAssignments"
      }
    ```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure rolü atama adımları](role-assignments-steps.md)
- [Azure hizmetleri için kaynak sağlayıcıları](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Azure yönetim grupları nedir?](../governance/management-groups/overview.md)
