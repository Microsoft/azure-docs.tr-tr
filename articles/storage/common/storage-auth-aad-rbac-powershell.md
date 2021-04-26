---
title: PowerShell kullanarak veri erişimi için bir Azure rolü atama
titleSuffix: Azure Storage
description: Azure rol tabanlı erişim denetimi (Azure RBAC) ile Azure Active Directory güvenlik sorumlusuna izinler atamak için Azure PowerShell modülünü nasıl kullanacağınızı öğrenin. Azure depolama, Azure AD aracılığıyla kimlik doğrulaması için yerleşik ve Azure özel rollerini destekler.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9e4fea52f56da9f2e84746daf0121df0b4355411
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100375961"
---
# <a name="use-powershell-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Blob ve kuyruk verilerine erişim için bir Azure rolü atamak üzere PowerShell 'i kullanma

Azure Active Directory (Azure AD), [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md)aracılığıyla güvenli kaynaklara erişim hakları verir. Azure depolama, kapsayıcılara veya kuyruklara erişmek için kullanılan ortak izin kümelerini çevreleyen Azure yerleşik rollerinin bir kümesini tanımlar.

Azure AD güvenlik sorumlusuna bir Azure rolü atandığında Azure, bu güvenlik sorumlusu için bu kaynaklara erişim izni verir. Erişim, aboneliğin düzeyi, kaynak grubu, depolama hesabı veya tek bir kapsayıcı veya kuyruk kapsamına eklenebilir. Azure AD güvenlik sorumlusu, bir Kullanıcı, Grup, uygulama hizmeti sorumlusu veya [Azure kaynakları için yönetilen bir kimlik](../../active-directory/managed-identities-azure-resources/overview.md)olabilir.

Bu makalede, Azure yerleşik rollerini listelemek ve kullanıcılara atamak için Azure PowerShell nasıl kullanılacağı açıklanır. Azure PowerShell kullanma hakkında daha fazla bilgi için bkz. [Azure PowerShell genel bakış](/powershell/azure/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-roles-for-blobs-and-queues"></a>Blob 'lar ve kuyruklar için Azure rolleri

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Kaynak kapsamını belirleme

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-azure-roles"></a>Kullanılabilir Azure rollerini listeleme

Azure PowerShell ile kullanılabilir Azure yerleşik rollerini listelemek için [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) komutunu kullanın:

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

## <a name="assign-an-azure-role-to-a-security-principal"></a>Güvenlik sorumlusuna bir Azure rolü atama

Bir güvenlik sorumlusuna Azure rolü atamak için [New-Azroleatama](/powershell/module/az.resources/new-azroleassignment) komutunu kullanın. Komutun biçimi atamanın kapsamına göre farklılık gösterebilir. Komutu çalıştırmak için, ilgili kapsamda sahip veya katkıda bulunan rolü atanmış olması gerekir. Aşağıdaki örneklerde, bir kullanıcıya çeşitli kapsamlardaki bir rol atama gösterilmektedir, ancak herhangi bir güvenlik sorumlusuna rol atamak için aynı komutu kullanabilirsiniz.

> [!IMPORTANT]
> Bir Azure depolama hesabı oluşturduğunuzda, Azure AD aracılığıyla verilere erişim için otomatik olarak izinler atanmamıştır. Veri erişimi için kendinize doğrudan bir Azure RBAC rolü atamanız gerekir. Aboneliğiniz, kaynak grubunuz, depolama hesabınız veya Kapsayıcınız ya da kuyruğunuzun düzeyinde atayabilirsiniz.
>
> Depolama hesabı bir Azure Resource Manager salt okuma kilidi ile kilitliyse kilit, depolama hesabı kapsamındaki Azure RBAC rollerinin veya bir veri kapsayıcısının (blob kapsayıcısı veya kuyruğu) atanmasını engeller.

### <a name="container-scope"></a>Kapsayıcı kapsamı

Bir kapsayıcıya kapsamlı bir rol atamak için, parametre için kapsayıcının kapsamını içeren bir dize belirtin `--scope` . Kapsayıcının kapsamı şu biçimdedir:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

Aşağıdaki örnek, **Depolama Blobu veri katılımcısı** rolünü, *örnek kapsayıcı* adlı bir kapsayıcıya kapsamındaki bir kullanıcıya atar. Parantez içinde örnek değerleri ve yer tutucu değerlerini kendi değerlerinizle değiştirdiğinizden emin olun: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Sıra kapsamı

Bir sıraya kapsamlı bir rol atamak için, parametre için kuyruğun kapsamını içeren bir dize belirtin `--scope` . Bir kuyruğun kapsamı şu biçimdedir:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

Aşağıdaki örnek, **depolama kuyruğu veri katılımcısı** rolünü, *örnek kuyruğu* adlı bir kuyruğa kapsamındaki bir kullanıcıya atar. Parantez içinde örnek değerleri ve yer tutucu değerlerini kendi değerlerinizle değiştirdiğinizden emin olun: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Depolama hesabı kapsamı

Depolama hesabına kapsamlı bir rol atamak için, parametresi için depolama hesabı kaynağının kapsamını belirtin `--scope` . Depolama hesabının kapsamı şu biçimdedir:

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

Kaynak grubuna kapsamlı bir rol atamak için, parametresinin kaynak grubu adını veya KIMLIĞINI belirtin `--resource-group` . Aşağıdaki örnek, **depolama kuyruğu veri okuyucusu** rolünü, kaynak grubunun düzeyindeki bir kullanıcıya atar. Parantez içinde örnek değerleri ve yer tutucu değerlerini kendi değerlerinizle değiştirdiğinizden emin olun: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Abonelik kapsamı

Aboneliğe kapsamlı bir rol atamak için, parametresi için abonelik kapsamını belirtin `--scope` . Bir aboneliğin kapsamı şu biçimdedir:

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

- [Azure PowerShell modülünü kullanarak Azure rol atamaları ekleme veya kaldırma](../../role-based-access-control/role-assignments-powershell.md)
- [Blob ve kuyruk verilerine erişim için Azure rolü atamak üzere Azure CLı 'yi kullanma](storage-auth-aad-rbac-cli.md)
- [Azure portalı kullanarak blob ve kuyruk verilerine erişim için Azure rolü atama](storage-auth-aad-rbac-portal.md)
