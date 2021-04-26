---
title: Azure CLı kullanarak veri erişimi için bir Azure rolü atama
titleSuffix: Azure Storage
description: Azure rol tabanlı erişim denetimi (Azure RBAC) ile Azure Active Directory güvenlik sorumlusuna izin atamak için Azure CLı 'yi nasıl kullanacağınızı öğrenin. Azure depolama, Azure AD aracılığıyla kimlik doğrulaması için yerleşik ve Azure özel rollerini destekler.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 93d9a81ab75efe4ea38189a7280e041e545a2b7d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785334"
---
# <a name="use-azure-cli-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Blob ve kuyruk verilerine erişim için Azure rolü atamak üzere Azure CLı 'yi kullanma

Azure Active Directory (Azure AD), [Azure rol tabanlı erişim denetimi (Azure RBAC)](../../role-based-access-control/overview.md)aracılığıyla güvenli kaynaklara erişim hakları verir. Azure depolama, blob veya kuyruk verilerine erişmek için kullanılan ortak izin kümelerini çevreleyen bir dizi Azure yerleşik rol tanımlar.

Azure AD güvenlik sorumlusuna bir Azure rolü atandığında Azure, bu güvenlik sorumlusu için bu kaynaklara erişim izni verir. Erişim, aboneliğin düzeyi, kaynak grubu, depolama hesabı veya tek bir kapsayıcı veya kuyruk kapsamına eklenebilir. Azure AD güvenlik sorumlusu, bir Kullanıcı, Grup, uygulama hizmeti sorumlusu veya [Azure kaynakları için yönetilen bir kimlik](../../active-directory/managed-identities-azure-resources/overview.md)olabilir.

Bu makalede, Azure CLı kullanarak Azure yerleşik rollerini listeleme ve kullanıcılara atama işlemlerinin nasıl yapılacağı açıklanır. Azure CLı kullanma hakkında daha fazla bilgi için bkz. [azure Command-Line arabirimi (CLI)](/cli/azure).

## <a name="azure-roles-for-blobs-and-queues"></a>Blob 'lar ve kuyruklar için Azure rolleri

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Kaynak kapsamını belirleme

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-azure-roles"></a>Kullanılabilir Azure rollerini listeleme

Azure CLı ile kullanılabilir Azure yerleşik rollerini listelemek için [az role Definition List](/cli/azure/role/definition#az_role_definition_list) komutunu kullanın:

```azurecli-interactive
az role definition list --out table
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

Bir güvenlik sorumlusuna Azure rolü atamak için [az role atama Create](/cli/azure/role/assignment#az_role_assignment_create) komutunu kullanın. Komutun biçimi atamanın kapsamına göre farklılık gösterebilir. Aşağıdaki örneklerde, bir kullanıcıya çeşitli kapsamlardaki bir rol atama gösterilmektedir, ancak herhangi bir güvenlik sorumlusuna rol atamak için aynı komutu kullanabilirsiniz.

> [!IMPORTANT]
> Bir Azure depolama hesabı oluşturduğunuzda, Azure AD aracılığıyla verilere erişim için otomatik olarak izinler atanmamıştır. Veri erişimi için kendinize doğrudan bir Azure RBAC rolü atamanız gerekir. Aboneliğiniz, kaynak grubunuz, depolama hesabınız veya Kapsayıcınız ya da kuyruğunuzun düzeyinde atayabilirsiniz.
>
> Depolama hesabı bir Azure Resource Manager salt okuma kilidi ile kilitliyse kilit, depolama hesabı kapsamındaki Azure RBAC rollerinin veya bir veri kapsayıcısının (blob kapsayıcısı veya kuyruğu) atanmasını engeller.

### <a name="container-scope"></a>Kapsayıcı kapsamı

Bir kapsayıcıya kapsamlı bir rol atamak için, parametre için kapsayıcının kapsamını içeren bir dize belirtin `--scope` . Kapsayıcının kapsamı şu biçimdedir:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>
```

Aşağıdaki örnek, **Depolama Blobu veri katılımcısı** rolünü, kapsayıcının düzeyi kapsamındaki bir kullanıcıya atar. Parantez içinde örnek değerleri ve yer tutucu değerlerini kendi değerlerinizle değiştirdiğinizden emin olun:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

### <a name="queue-scope"></a>Sıra kapsamı

Bir sıraya kapsamlı bir rol atamak için, parametre için kuyruğun kapsamını içeren bir dize belirtin `--scope` . Bir kuyruğun kapsamı şu biçimdedir:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>
```

Aşağıdaki örnek, **depolama kuyruğu veri katılımcısı** rolünü, sıranın düzeyi kapsamındaki bir kullanıcıya atar. Parantez içinde örnek değerleri ve yer tutucu değerlerini kendi değerlerinizle değiştirdiğinizden emin olun:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>"
```

### <a name="storage-account-scope"></a>Depolama hesabı kapsamı

Depolama hesabına kapsamlı bir rol atamak için, parametresi için depolama hesabı kaynağının kapsamını belirtin `--scope` . Depolama hesabının kapsamı şu biçimdedir:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Aşağıdaki örnek, depolama hesabı düzeyinde bir kullanıcıya **Depolama Blobu veri okuyucusu** rolünün nasıl atanacağını gösterir. Örnek değerleri kendi değerlerinizle değiştirdiğinizden emin olun: \

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Kaynak grubu kapsamı

Kaynak grubuna kapsamlı bir rol atamak için, parametresinin kaynak grubu adını veya KIMLIĞINI belirtin `--resource-group` . Aşağıdaki örnek, **depolama kuyruğu veri okuyucusu** rolünü, kaynak grubunun düzeyindeki bir kullanıcıya atar. Parantez içinde örnek değerleri ve yer tutucu değerlerini kendi değerlerinizle değiştirdiğinizden emin olun:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group <resource-group>
```

### <a name="subscription-scope"></a>Abonelik kapsamı

Aboneliğe kapsamlı bir rol atamak için, parametresi için abonelik kapsamını belirtin `--scope` . Bir aboneliğin kapsamı şu biçimdedir:

```
/subscriptions/<subscription>
```

Aşağıdaki örnek, depolama hesabı düzeyinde bir kullanıcıya **Depolama Blobu veri okuyucusu** rolünün nasıl atanacağını gösterir. Örnek değerleri kendi değerlerinizle değiştirdiğinizden emin olun: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure PowerShell modülünü kullanarak Azure rol atamaları ekleme veya kaldırma](../../role-based-access-control/role-assignments-powershell.md)
- [Blob ve kuyruk verilerine erişim için bir Azure rolü atamak üzere Azure PowerShell modülünü kullanın](storage-auth-aad-rbac-powershell.md)
- [Azure portalı kullanarak blob ve kuyruk verilerine erişim için Azure rolü atama](storage-auth-aad-rbac-portal.md)
