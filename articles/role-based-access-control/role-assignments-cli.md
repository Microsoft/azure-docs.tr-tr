---
title: Azure CLı kullanarak Azure rolleri atama-Azure RBAC
description: Azure CLı ve Azure rol tabanlı erişim denetimi (Azure RBAC) kullanarak kullanıcılar, gruplar, hizmet sorumluları veya yönetilen kimlikler için Azure kaynaklarına erişim izni verme hakkında bilgi edinin.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/28/2020
ms.author: rolyon
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: ee356f32b6799c6182ec1c9e061a35271a4bbc23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100556969"
---
# <a name="assign-azure-roles-using-azure-cli"></a>Azure CLı kullanarak Azure rolleri atama

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Bu makalede, Azure CLı kullanarak rollerin nasıl atanacağı açıklanır.

## <a name="prerequisites"></a>Önkoşullar

Rol atamak için şunları yapmanız gerekir:

- `Microsoft.Authorization/roleAssignments/write`[Kullanıcı erişimi Yöneticisi](built-in-roles.md#user-access-administrator) veya [sahibi](built-in-roles.md#owner) gibi izinler
- Azure Cloud Shell veya [Azure CLI](/cli/azure) ['da Bash](../cloud-shell/overview.md)

## <a name="steps-to-assign-an-azure-role"></a>Azure rolü atama adımları

Rol atamak için üç öğeden oluşur: güvenlik sorumlusu, rol tanımı ve kapsam.

### <a name="step-1-determine-who-needs-access"></a>1. Adım: kimlerin erişime ihtiyacı olduğunu belirleme

Bir Kullanıcı, Grup, hizmet sorumlusu veya yönetilen kimliğe bir rol atayabilirsiniz. Rol atamak için, nesnenin benzersiz KIMLIĞINI belirtmeniz gerekebilir. KIMLIK şu biçimdedir: `11111111-1111-1111-1111-111111111111` . KIMLIĞI Azure portal veya Azure CLı 'yi kullanarak edinebilirsiniz.

**Kullanıcı**

Bir Azure AD kullanıcısı için, *patlong \@ contoso.com* veya Kullanıcı nesne kimliği gibi Kullanıcı asıl adını alın. Nesne KIMLIĞINI almak için [az ad User Show](/cli/azure/ad/user#az_ad_user_show)' i kullanabilirsiniz.

```azurecli
az ad user show --id "{principalName}" --query "objectId" --output tsv
```

**Grup**

Bir Azure AD grubu için Grup nesne KIMLIĞI gereklidir. Nesne KIMLIĞINI almak için [az Ad Group Show](/cli/azure/ad/group#az_ad_group_show) veya [az Ad Group List](/cli/azure/ad/group#az_ad_group_list)kullanabilirsiniz.

```azurecli
az ad group show --group "{groupName}" --query "objectId" --output tsv
```

**Hizmet sorumlusu**

Bir Azure AD hizmet sorumlusu (bir uygulama tarafından kullanılan kimlik) için hizmet sorumlusu nesne KIMLIĞI gereklidir. Nesne KIMLIĞINI almak için [az ad SP List](/cli/azure/ad/sp#az_ad_sp_list)kullanabilirsiniz. Hizmet sorumlusu için uygulama KIMLIĞINI **değil** , nesne kimliğini kullanın.

```azurecli
az ad sp list --all --query "[].{displayName:displayName, objectId:objectId}" --output tsv
az ad sp list --display-name "{displayName}"
```

**Yönetilen kimlik**

Sistem tarafından atanan veya Kullanıcı tarafından atanan bir yönetilen kimlik için, nesne KIMLIĞI gereklidir. Nesne KIMLIĞINI almak için [az ad SP List](/cli/azure/ad/sp#az_ad_sp_list)kullanabilirsiniz.

```azurecli
az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
```

Yalnızca Kullanıcı tarafından atanan yönetilen kimlikleri listelemek için [az Identity List](/cli/azure/identity#az_identity_list)' i kullanabilirsiniz.

```azurecli
az identity list
```
    
### <a name="step-2-select-the-appropriate-role"></a>2. Adım: uygun rolü seçin

İzinler, rollerle birlikte gruplandırılır. Çeşitli [Azure yerleşik rollerinin](built-in-roles.md) listesinden seçim yapabilir veya kendi özel rollerinizi kullanabilirsiniz. Gerekli en az ayrıcalığa sahip erişim vermek en iyi uygulamadır, bu nedenle daha geniş bir rol atamaktan kaçının.

Rolleri listelemek ve benzersiz rol KIMLIĞINI almak için [az role Definition List](/cli/azure/role/definition#az_role_definition_list)' i kullanabilirsiniz.

```azurecli
az role definition list --query "[].{name:name, roleType:roleType, roleName:roleName}" --output tsv
```

Belirli bir rolün ayrıntılarının listesi aşağıda verilmiştir.

```azurecli
az role definition list --name "{roleName}"
```

Daha fazla bilgi için bkz. [Azure rol tanımlarını listeleme](role-definitions-list.md#azure-cli).
 
### <a name="step-3-identify-the-needed-scope"></a>3. Adım: gerekli kapsamı tanımla

Azure dört kapsam düzeyi sağlar: kaynak, [kaynak grubu](../azure-resource-manager/management/overview.md#resource-groups), abonelik ve [Yönetim grubu](../governance/management-groups/overview.md). Gerekli en az ayrıcalıkla erişim sağlamak en iyi uygulamadır. bu nedenle, daha geniş bir kapsamda rol atamaktan kaçının. Kapsam hakkında daha fazla bilgi için bkz. [kapsamı anlama](scope-overview.md).

**Kaynak kapsamı**

Kaynak kapsamı için kaynağın kaynak KIMLIĞI gereklidir. Kaynak KIMLIĞINI Azure portal kaynağın özelliklerine bakarak bulabilirsiniz. Kaynak KIMLIĞI aşağıdaki biçimdedir.

```
/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}
```

**Kaynak grubu kapsamı**

Kaynak grubu kapsamı için kaynak grubunun adına ihtiyacınız vardır. Adı Azure portal **kaynak grupları** sayfasında bulabilir veya [az Group List](/cli/azure/group#az_group_list)' i kullanabilirsiniz.

```azurecli
az group list --query "[].{name:name}" --output tsv
```

**Abonelik kapsamı** 

Abonelik kapsamı için abonelik KIMLIĞI gereklidir. KIMLIĞI Azure portal **abonelikler** sayfasında bulabilir veya [az Account List](/cli/azure/account#az_account_list)' i kullanabilirsiniz.

```azurecli
az account list --query "[].{name:name, id:id}" --output tsv
```

**Yönetim grubu kapsamı** 

Yönetim grubu kapsamı için yönetim grubu adına ihtiyacınız vardır. Adı Azure portal **Yönetim grupları** sayfasında bulabilir veya [az Account Management-Group List](/cli/azure/account/management-group#az_account_management_group_list)kullanabilirsiniz.

```azurecli
az account management-group list --query "[].{name:name, id:id}" --output tsv
```
    
### <a name="step-4-assign-role"></a>4. Adım: rol atama

Rol atamak için [az role atama Create](/cli/azure/role/assignment#az_role_assignment_create) komutunu kullanın. Kapsama bağlı olarak, komut genellikle aşağıdaki biçimlerden birine sahiptir.

**Kaynak kapsamı**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}"
```

**Kaynak grubu kapsamı**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}"
```

**Abonelik kapsamı** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--subscription "{subscriptionNameOrId}"
```

**Yönetim grubu kapsamı** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/providers/Microsoft.Management/managementGroups/{managementGroupName}"
``` 

Aşağıda, [sanal makine katılımcısı](built-in-roles.md#virtual-machine-contributor) rolünü bir kaynak grubu kapsamındaki bir kullanıcıya atadığınızda çıktının bir örneği gösterilmektedir.

```azurecli
{
  "canDelegate": null,
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
  "name": "{roleAssignmentId}",
  "principalId": "{principalId}",
  "principalType": "User",
  "resourceGroup": "{resourceGroupName}",
  "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "type": "Microsoft.Authorization/roleAssignments"
}
```
    
## <a name="assign-role-examples"></a>Rol örnekleri atama

#### <a name="assign-a-role-for-all-blob-containers-in-a-storage-account-resource-scope"></a>Depolama hesabı kaynak kapsamındaki tüm blob kapsayıcıları için bir rol atama

*Storage12345* adlı bir depolama hesabının kaynak kapsamındaki *55555555-5555-5555-5555-555555555555* nesne kimliğine sahip bir hizmet sorumlusuna [Depolama Blobu veri katılımcısı](built-in-roles.md#storage-blob-data-contributor) rolünü atar.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"
```

#### <a name="assign-a-role-for-a-specific-blob-container-resource-scope"></a>Belirli bir blob kapsayıcısı kaynak kapsamı için rol atama

*BLOB-Container-01* adlı bir blob kapsayıcısının kaynak kapsamındaki *55555555-5555-5555-5555-555555555555* nesne kimliğine sahip bir hizmet sorumlusuna [Depolama Blobu veri katılımcısı](built-in-roles.md#storage-blob-data-contributor) rolünü atar.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"
```

#### <a name="assign-a-role-for-a-group-in-a-specific-virtual-network-resource-scope"></a>Belirli bir sanal ağ kaynak kapsamındaki bir grup için rol atama

[Sanal makine katılımcısı](built-in-roles.md#virtual-machine-contributor) rolünü, kimlik 22222222-2222-2222-2222-222222222222 olan *Ann Mack ekip* grubuna, *sahte ma-Sales-Project-Network* adlı bir sanal ağ için kaynak kapsamında atar.

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Virtual Machine Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network"
```

#### <a name="assign-a-role-for-a-user-at-a-resource-group-scope"></a>Kaynak grubu kapsamındaki bir kullanıcı için rol atama

[Sanal makine katılımcısı](built-in-roles.md#virtual-machine-contributor) rolünü *ilaç-Sales* kaynak grubu kapsamındaki *patlong \@ contoso.com* kullanıcısına atar.

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="assign-a-role-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>Bir kaynak grubu kapsamındaki benzersiz rol KIMLIĞINI kullanarak bir kullanıcı için rol atama

Rol adının değişebilir birkaç zaman vardır, örneğin:

- Kendi özel rolünüzü kullanıyorsunuz ve adı değiştirmeye karar verdiniz.
- Adında **(Önizleme)** olan bir önizleme rolü kullanıyorsunuz. Rol serbest bırakıldığında, rol yeniden adlandırılır.

Rol yeniden adlandırılsa bile, rol KIMLIĞI değişmez. Rol atamalarınızı oluşturmak için betikler veya Otomasyon kullanıyorsanız, rol adı yerine benzersiz rol KIMLIĞINI kullanmak en iyi uygulamadır. Bu nedenle, bir rol yeniden adlandırılırsa, betiklerinizin çalışması daha olasıdır.

Aşağıdaki örnek, [sanal makine katılımcısı](built-in-roles.md#virtual-machine-contributor) rolünü *ilaç-Sales* kaynak grubu kapsamındaki *patlong \@ contoso.com* kullanıcısına atar.

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "9980e02c-c2be-4d73-94e8-173b1dc7cf3c" \
--resource-group "pharma-sales"
```

#### <a name="assign-a-role-for-all-blob-containers-at-a-resource-group-scope"></a>Kaynak grubu kapsamındaki tüm blob kapsayıcıları için bir rol atama

[Depolama Blobu veri katılımcısı](built-in-roles.md#storage-blob-data-contributor) rolünü, *örnek depolama-RG* kaynak grubu kapsamındaki *55555555-5555-5555-5555-555555555555* nesne kimliği ile bir hizmet sorumlusuna atar.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--resource-group "Example-Storage-rg"
```

Alternatif olarak, parametresiyle tam kaynak grubunu belirtebilirsiniz `--scope` :

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg"
```

#### <a name="assign-a-role-for-an-application-at-a-resource-group-scope"></a>Kaynak grubu kapsamındaki bir uygulama için rol atama

[Sanal makine katılımcısı](built-in-roles.md#virtual-machine-contributor) rolünü, *ilaç-Sales* kaynak grubu KAPSAMıNDAKI hizmet sorumlusu nesne kimliği 44444444-4444-4444-4444-444444444444 olan bir uygulamaya atar.

```azurecli
az role assignment create --assignee "44444444-4444-4444-4444-444444444444" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="assign-a-role-for-a-new-service-principal-at-a-resource-group-scope"></a>Kaynak grubu kapsamındaki yeni bir hizmet sorumlusu için rol atama

Yeni bir hizmet sorumlusu oluşturur ve bu hizmet sorumlusuna hemen bir rol atamayı denerseniz, bu rol ataması bazı durumlarda başarısız olabilir. Örneğin, yeni bir yönetilen kimlik oluşturmak ve ardından bu hizmet sorumlusuna bir rol atamayı denemek için bir komut dosyası kullanırsanız, rol ataması başarısız olabilir. Bu hatanın nedeni büyük olasılıkla çoğaltma gecikmesi. Hizmet sorumlusu tek bir bölgede oluşturulur; Ancak, rol ataması henüz hizmet sorumlusunu çoğaltılmamış farklı bir bölgede gerçekleşebilir. Bu senaryoya yönelik olarak, rol atamasını oluştururken asıl türü belirtmeniz gerekir.

Rol atamak için [az role atama oluştur](/cli/azure/role/assignment#az_role_assignment_create)kullanın, için bir değer belirtin `--assignee-object-id` ve sonra `--assignee-principal-type` olarak ayarlayın `ServicePrincipal` .

```azurecli
az role assignment create --assignee-object-id "{assigneeObjectId}" \
--assignee-principal-type "{assigneePrincipalType}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}" \
--scope "/subscriptions/{subscriptionId}"
```

Aşağıdaki örnek, [sanal makine katılımcısı](built-in-roles.md#virtual-machine-contributor) rolünü, *sahte ma-Sales* kaynak grubu kapsamındaki *MSI-test* tarafından yönetilen kimliğe atar:

```azurecli
az role assignment create --assignee-object-id "33333333-3333-3333-3333-333333333333" \
--assignee-principal-type "ServicePrincipal" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="assign-a-role-for-a-user-at-a-subscription-scope"></a>Abonelik kapsamındaki bir kullanıcı için rol atama

Bir abonelik kapsamındaki *annm \@ example.com* kullanıcısına [okuyucu](built-in-roles.md#reader) rolünü atar.

```azurecli
az role assignment create --assignee "annm@example.com" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

#### <a name="assign-a-role-for-a-group-at-a-subscription-scope"></a>Abonelik kapsamındaki bir grup için rol atama

Bir abonelik kapsamındaki KIMLIĞI 22222222-2222-2222-2222-222222222222 olan *Ann Mack ekip* grubuna [okuyucu](built-in-roles.md#reader) rolünü atar.

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

#### <a name="assign-a-role-for-all-blob-containers-at-a-subscription-scope"></a>Abonelik kapsamındaki tüm blob kapsayıcıları için bir rol atama

Bir abonelik kapsamındaki *Alain \@ example.com* kullanıcısına [Depolama Blobu veri okuyucusu](built-in-roles.md#storage-blob-data-reader) rolünü atar.

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Storage Blob Data Reader" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

#### <a name="assign-a-role-for-a-user-at-a-management-group-scope"></a>Yönetim grubu kapsamındaki bir kullanıcı için rol atama

Bir yönetim grubu kapsamındaki *Alain \@ example.com* kullanıcısına [faturalandırma okuyucusu](built-in-roles.md#billing-reader) rolünü atar.

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure CLı kullanarak Azure rol atamalarını listeleme](role-assignments-list-cli.md)
- [Azure CLı 'yı kullanarak Azure kaynaklarını ve kaynak gruplarını yönetme](../azure-resource-manager/management/manage-resources-cli.md)
