---
title: RBAC-Azure depolama ile blob ve kuyruk verilerinde Azure AD erişim haklarını yönetmek için Azure PowerShell kullanma
description: Rol tabanlı erişim denetimi (RBAC) ile kapsayıcılara ve kuyruklara erişim atamak için Azure PowerShell kullanın. Azure depolama, Azure AD aracılığıyla kimlik doğrulaması için yerleşik ve özel RBAC rollerini destekler.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 967e1754ec4be504669e176a5643186d08efb9d4
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673186"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-using-powershell"></a>PowerShell kullanarak RBAC ile Azure Blob ve kuyruk verilerine erişim verme

Azure Active Directory (Azure AD), [rol tabanlı erişim denetimi (RBAC)](../../role-based-access-control/overview.md)aracılığıyla güvenli kaynaklara erişim haklarını yetkilendirir. Azure depolama, kapsayıcılara veya kuyruklara erişmek için kullanılan ortak izin kümelerini çevreleyen yerleşik RBAC rollerinin bir kümesini tanımlar. 

Azure AD güvenlik sorumlusuna bir RBAC rolü atandığında Azure, bu güvenlik sorumlusu için bu kaynaklara erişim izni verir. Erişim, aboneliğin düzeyi, kaynak grubu, depolama hesabı veya tek bir kapsayıcı veya kuyruk kapsamına eklenebilir. Azure AD güvenlik sorumlusu, bir Kullanıcı, Grup, uygulama hizmeti sorumlusu veya [Azure kaynakları için yönetilen bir kimlik](../../active-directory/managed-identities-azure-resources/overview.md)olabilir.

Bu makalede, yerleşik RBAC rollerini listelemek ve kullanıcılara atamak için Azure PowerShell nasıl kullanılacağı açıklanır. Azure PowerShell kullanma hakkında daha fazla bilgi için bkz. [Azure PowerShell genel bakış](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>Blob 'lar ve kuyruklar için RBAC rolleri

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Kaynak kapsamını belirleme

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Kullanılabilir RBAC rollerini listeleme

Mevcut yerleşik RBAC rollerini Azure PowerShell listelemek için [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) komutunu kullanın:

```powershell
Get-AzRoleDefinition | FT Name, Description
```

Azure için diğer yerleşik rollerle birlikte listelenen yerleşik Azure depolama veri rollerini görürsünüz:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-security-principal"></a>Güvenlik sorumlusuna RBAC rolü atama

Bir güvenlik sorumlusuna RBAC rolü atamak için, [New-Azroleatama](/powershell/module/az.resources/new-azroleassignment) komutunu kullanın. Komutun biçimi atamanın kapsamına göre farklılık gösterebilir. Aşağıdaki örneklerde, bir kullanıcıya çeşitli kapsamlardaki bir rol atama gösterilmektedir, ancak herhangi bir güvenlik sorumlusuna rol atamak için aynı komutu kullanabilirsiniz.

### <a name="container-scope"></a>Kapsayıcı kapsamı

Bir kapsayıcıya kapsamlı bir rol atamak için, `--scope` parametresi için kapsayıcının kapsamını içeren bir dize belirtin. Kapsayıcının kapsamı şu biçimdedir:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

Aşağıdaki örnek, **Depolama Blobu veri katılımcısı** rolünü, *örnek kapsayıcı*adlı bir kapsayıcıya kapsamındaki bir kullanıcıya atar. Parantez içinde örnek değerleri ve yer tutucu değerlerini kendi değerlerinizle değiştirdiğinizden emin olun: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Sıra kapsamı

Bir sıraya kapsamlı bir rol atamak için, `--scope` parametresi için kuyruğun kapsamını içeren bir dize belirtin. Bir kuyruğun kapsamı şu biçimdedir:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

Aşağıdaki örnek, **depolama kuyruğu veri katılımcısı** rolünü, *örnek kuyruğu*adlı bir kuyruğa kapsamındaki bir kullanıcıya atar. Parantez içinde örnek değerleri ve yer tutucu değerlerini kendi değerlerinizle değiştirdiğinizden emin olun: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Depolama hesabı kapsamı

Depolama hesabına kapsamlı bir rol atamak için, `--scope` parametresi için depolama hesabı kaynağının kapsamını belirtin. Depolama hesabının kapsamı şu biçimdedir:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Aşağıdaki örnek, depolama hesabı düzeyindeki bir kullanıcıya **Depolama Blobu veri okuyucusu** rolünün nasıl kapsam oluşturulacağını gösterir. Örnek değerleri kendi değerlerinizle değiştirdiğinizden emin olun: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Kaynak grubu kapsamı

Kaynak grubuna kapsamlı bir rol atamak için, `--resource-group` parametresinin kaynak grubu adını veya KIMLIĞINI belirtin. Aşağıdaki örnek, **depolama kuyruğu veri okuyucusu** rolünü, kaynak grubunun düzeyindeki bir kullanıcıya atar. Parantez içinde örnek değerleri ve yer tutucu değerlerini kendi değerlerinizle değiştirdiğinizden emin olun: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Abonelik kapsamı

Aboneliğe kapsamlı bir rol atamak için, `--scope` parametresi için abonelik kapsamını belirtin. Bir aboneliğin kapsamı şu biçimdedir:

```
/subscriptions/<subscription>
```

Aşağıdaki örnek, depolama hesabı düzeyinde bir kullanıcıya **Depolama Blobu veri okuyucusu** rolünün nasıl atanacağını gösterir. Örnek değerleri kendi değerlerinizle değiştirdiğinizden emin olun: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Sonraki adımlar

- [RBAC ve Azure PowerShell kullanarak Azure kaynaklarına erişimi yönetme](../../role-based-access-control/role-assignments-powershell.md)
- [Azure CLI kullanarak RBAC ile Azure blob ve kuyruk verilerine erişim izni verme](storage-auth-aad-rbac-cli.md)
- [Azure portalında RBAC ile Azure blob ve kuyruk verilerine erişim izni verme](storage-auth-aad-rbac-portal.md)
