---
title: RBAC ve Azure CLı kullanarak Azure kaynaklarına erişimi yönetme | Microsoft Docs
description: Rol tabanlı erişim denetimi (RBAC) ve Azure CLı kullanarak kullanıcılar, gruplar ve uygulamalar için Azure kaynaklarına erişimi yönetmeyi öğrenin. Buna erişimi listeleme, erişim verme ve erişimi kaldırma işlemleri dahildir.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 3420374e90790bd1ffe4c845c19de1bfed317302
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173740"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-cli"></a>RBAC ve Azure CLı kullanarak Azure kaynaklarına erişimi yönetme

[Rol tabanlı erişim denetimi (RBAC)](overview.md) , Azure kaynaklarına erişimi yönetme yöntemidir. Bu makalede, RBAC ve Azure CLı kullanarak kullanıcılar, gruplar ve uygulamalar için erişimi nasıl yöneteceğiniz açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

Erişimi yönetmek için aşağıdakilerden birine ihtiyacınız vardır:

* [Bash Azure Cloud Shell](/azure/cloud-shell/overview)
* [Azure CLI](/cli/azure)

## <a name="list-roles"></a>Rolleri listeleme

Tüm kullanılabilir rol tanımlarını listelemek için [az role Definition List](/cli/azure/role/definition#az-role-definition-list)kullanın:

```azurecli
az role definition list
```

Aşağıdaki örnekte, tüm kullanılabilir rol tanımlarının adı ve açıklaması listelenmektedir:

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.roleName, "description":.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

Aşağıdaki örnek, yerleşik rol tanımlarının tümünü listeler:

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "roleType": "BuiltInRole"
}

...
```

## <a name="list-a-role-definition"></a>Rol tanımı listeleme

Rol tanımını listelemek için [az role Definition List](/cli/azure/role/definition#az-role-definition-list)kullanın:

```azurecli
az role definition list --name <role_name>
```

Aşağıdaki örnek, *katkıda bulunan* rol tanımını listeler:

```azurecli
az role definition list --name "Contributor"
```

```Output
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="list-actions-of-a-role"></a>Rolün listeleme eylemleri

Aşağıdaki örnek, *katkıda bulunan* rolünün yalnızca *eylemlerini* ve *notActions* listeler:

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.permissions[0].actions, "notActions":.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

Aşağıdaki örnek yalnızca *sanal makine katılımcısı* rolünün eylemlerini listeler:

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="list-access"></a>Erişimi listeleme

RBAC 'de, erişimi listelemek için rol atamalarını listeleyin.

### <a name="list-role-assignments-for-a-user"></a>Bir kullanıcının rol atamalarını listeleme

Belirli bir kullanıcı için rol atamalarını listelemek için [az role atama listesi](/cli/azure/role/assignment#az-role-assignment-list)kullanın:

```azurecli
az role assignment list --assignee <assignee>
```

Varsayılan olarak, yalnızca abonelik kapsamındaki doğrudan atamalar görüntülenir. Kaynak veya gruba göre kapsamı belirlenmiş atamaları görüntülemek için, `--all` devralınan atamaları `--include-inherited`görüntülemek için ve kullanın.

Aşağıdaki örnekte, doğrudan *\@patlong contoso.com* kullanıcısına atanan rol atamaları listelenmektedir:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
```

### <a name="list-role-assignments-at-a-resource-group-scope"></a>Kaynak grubu kapsamındaki rol atamalarını listeleme

Bir kaynak grubu kapsamında var olan rol atamalarını listelemek için [az role atama listesi](/cli/azure/role/assignment#az-role-assignment-list)kullanın:

```azurecli
az role assignment list --resource-group <resource_group>
```

Aşağıdaki örnekte, *ilaç-Sales* kaynak grubu için rol atamaları listelenmektedir:

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

### <a name="list-role-assignments-at-a-subscription-scope"></a>Abonelik kapsamındaki rol atamalarını listeleme

Tüm rol atamalarını bir abonelik kapsamında listelemek için [az role atama listesi](/cli/azure/role/assignment#az-role-assignment-list)' ni kullanın. Abonelik KIMLIĞINI almak için Azure portal **abonelikler** dikey penceresinde bulabilir veya [az Account List](/cli/azure/account#az-account-list)' i kullanabilirsiniz.

```azurecli
az role assignment list --subscription <subscription_name_or_id>
```

```Example
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

### <a name="list-role-assignments-at-a-management-group-scope"></a>Bir yönetim grubu kapsamındaki rol atamalarını listeleyin

Bir yönetim grubu kapsamındaki tüm rol atamalarını listelemek için [az role atama listesi](/cli/azure/role/assignment#az-role-assignment-list)' ni kullanın. Yönetim grubu KIMLIĞINI almak için Azure portal **Yönetim grupları** dikey penceresinde bulabilir veya [az Account Management-Group List](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list)kullanabilirsiniz.

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="grant-access"></a>Erişim izni ver

RBAC'de erişim vermek için bir rol ataması oluşturmanız gerekir.

### <a name="create-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>Kaynak grubu kapsamındaki bir kullanıcı için rol ataması oluşturma

Bir kaynak grubu kapsamındaki bir kullanıcıya erişim izni vermek için [az role atama oluştur](/cli/azure/role/assignment#az-role-assignment-create)' u kullanın.

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

Aşağıdaki örnek, *sanal makine katılımcısı* rolünü *ilaç-Sales* kaynak grubu kapsamındaki *\@patlong contoso.com* kullanıcısına atar:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>Benzersiz rol KIMLIĞINI kullanarak bir rol ataması oluşturma

Rol adının değişebilir birkaç zaman vardır, örneğin:

- Kendi özel rolünüzü kullanıyorsunuz ve adı değiştirmeye karar verdiniz.
- Adında **(Önizleme)** olan bir önizleme rolü kullanıyorsunuz. Rol serbest bırakıldığında, rol yeniden adlandırılır.

> [!IMPORTANT]
> Bir önizleme sürümü, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir.
> Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Rol yeniden adlandırılsa bile, rol KIMLIĞI değişmez. Rol atamalarınızı oluşturmak için betikler veya Otomasyon kullanıyorsanız, rol adı yerine benzersiz rol KIMLIĞINI kullanmak en iyi uygulamadır. Bu nedenle, bir rol yeniden adlandırılırsa, betiklerinizin çalışması daha olasıdır.

Rol adı yerine benzersiz rol KIMLIĞI kullanarak bir rol ataması oluşturmak için [az role atama Create](/cli/azure/role/assignment#az-role-assignment-create)' i kullanın.

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

Aşağıdaki örnek, [sanal makine katılımcısı](built-in-roles.md#virtual-machine-contributor) rolünü *ilaç-Sales* kaynak grubu kapsamındaki *\@patlong contoso.com* kullanıcısına atar. Benzersiz rol KIMLIĞINI almak için [az role Definition List](/cli/azure/role/definition#az-role-definition-list) ' i kullanabilir veya [Azure kaynakları için yerleşik roller](built-in-roles.md)' e bakabilirsiniz.

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-group"></a>Bir grup için rol ataması oluşturma

Bir gruba erişim vermek için [az role atama Create](/cli/azure/role/assignment#az-role-assignment-create)' i kullanın. Grubun KIMLIĞINI almak için [az ad Grup listesi](/cli/azure/ad/group#az-ad-group-list) ' ni veya [az Ad Group Show](/cli/azure/ad/group#az-ad-group-show)' ı kullanabilirsiniz.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Aşağıdaki örnek, *okuyucu* rolünü bir ABONELIK kapsamındaki kimliği 22222222-2222-2222-2222-222222222222 olan *Ann Mack ekip* grubuna atar.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

Aşağıdaki örnek, *sanal makine katılımcısı* rolünü, kimlik 22222222-2222-2222-2222-222222222222 olan *Ann Mack ekip* grubuna, *ilaç-Sales-Project-Network*adlı bir sanal ağ için kaynak kapsamında atar.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application-at-a-resource-group-scope"></a>Kaynak grubu kapsamındaki bir uygulama için rol ataması oluşturma

Bir uygulamaya erişim izni vermek için [az role atama Create](/cli/azure/role/assignment#az-role-assignment-create)' i kullanın. Uygulamanın nesne KIMLIĞINI almak için [az ad uygulama listesi](/cli/azure/ad/app#az-ad-app-list) ' ni veya [az ad App Show](/cli/azure/ad/app#az-ad-app-show)' ı kullanabilirsiniz.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

Aşağıdaki örnek, *sanal makine katılımcısı* rolünü, *ilaç-Sales* kaynak grubu kapsamındaki 44444444-4444-4444-4444-444444444444 nesne kimliği ile bir uygulamaya atar.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-user-at-a-subscription-scope"></a>Abonelik kapsamındaki bir kullanıcı için rol ataması oluşturma

Abonelik kapsamındaki bir kullanıcıya erişim izni vermek için [az role atama oluştur](/cli/azure/role/assignment#az-role-assignment-create)' u kullanın. Abonelik KIMLIĞINI almak için Azure portal **abonelikler** dikey penceresinde bulabilir veya [az Account List](/cli/azure/account#az-account-list)' i kullanabilirsiniz.

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

Aşağıdaki örnek, *okuyucu* rolünü bir abonelik kapsamındaki *\@annm example.com* kullanıcısına atar.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="create-a-role-assignment-for-a-user-at-a-management-group-scope"></a>Yönetim grubu kapsamındaki bir kullanıcı için rol ataması oluşturma

Bir yönetim grubu kapsamındaki bir kullanıcıya erişim izni vermek için [az role atama oluştur](/cli/azure/role/assignment#az-role-assignment-create)' u kullanın. Yönetim grubu KIMLIĞINI almak için Azure portal **Yönetim grupları** dikey penceresinde bulabilir veya [az Account Management-Group List](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list)kullanabilirsiniz.

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Aşağıdaki örnek, *faturalandırma okuyucusu* rolünü bir yönetim grubu kapsamındaki *\@Alain example.com* kullanıcısına atar.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="create-a-role-assignment-for-a-new-service-principal"></a>Yeni hizmet sorumlusu için rol ataması oluşturma

Yeni bir hizmet sorumlusu oluşturur ve bu hizmet sorumlusuna hemen bir rol atamayı denerseniz, bu rol ataması bazı durumlarda başarısız olabilir. Örneğin, yeni bir yönetilen kimlik oluşturmak ve ardından bu hizmet sorumlusuna bir rol atamayı denemek için bir komut dosyası kullanırsanız, rol ataması başarısız olabilir. Bu hatanın nedeni büyük olasılıkla çoğaltma gecikmesi. Hizmet sorumlusu tek bir bölgede oluşturulur; Ancak, rol ataması henüz hizmet sorumlusunu çoğaltılmamış farklı bir bölgede gerçekleşebilir. Bu senaryoya yönelik olarak, rol atamasını oluştururken asıl türü belirtmeniz gerekir.

Rol ataması oluşturmak için [az role atama oluştur](/cli/azure/role/assignment#az-role-assignment-create)kullanın, için `--assignee-object-id`bir değer belirtin ve sonra olarak `ServicePrincipal`ayarlayın `--assignee-principal-type` .

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

Aşağıdaki örnek, *sanal makine katılımcısı* rolünü, *sahte ma-Sales* kaynak grubu kapsamındaki *MSI-test* tarafından yönetilen kimliğe atar:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-access"></a>Erişimi kaldır

RBAC 'de, erişimi kaldırmak için [az role atama Sil](/cli/azure/role/assignment#az-role-assignment-delete)' i kullanarak bir rol atamasını kaldırırsınız:

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

Aşağıdaki örnek, *ilaç-Sales* kaynak grubundaki *\@patlong contoso.com* kullanıcısının *sanal makine katılımcısı* rolü atamasını kaldırır:

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

Aşağıdaki örnek, bir abonelik kapsamındaki KIMLIĞI 22222222-2222-2222-2222-222222222222 olan *Ann Mack ekip* grubundan *okuyucu* rolünü kaldırır. Grubun KIMLIĞINI almak için [az ad Grup listesi](/cli/azure/ad/group#az-ad-group-list) ' ni veya [az Ad Group Show](/cli/azure/ad/group#az-ad-group-show)' ı kullanabilirsiniz.

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

Aşağıdaki örnek, yönetim grubu kapsamındaki *Alain\@example.com* kullanıcısının *faturalandırma okuyucusu* rolünü kaldırır. Yönetim grubunun KIMLIĞINI almak için [az Account Management-Group List](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list)kullanabilirsiniz.

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>Sonraki adımlar

- [Öğretici: Azure CLı kullanarak Azure kaynakları için özel bir rol oluşturma](tutorial-custom-role-cli.md)
- [Azure CLı 'yı kullanarak Azure kaynaklarını ve kaynak gruplarını yönetme](../azure-resource-manager/cli-azure-resource-manager.md)
