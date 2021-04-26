---
title: 'Özel roller: SQL yönetilen örnek geçişlere çevrimiçi SQL Server'
titleSuffix: Azure Database Migration Service
description: Azure SQL yönetilen örnek çevrimiçi geçişleri SQL Server için özel rolleri kullanmayı öğrenin.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 1228234b6a2904c453ec92f3c09a7b3f55604953
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100363772"
---
# <a name="custom-roles-for-sql-server-to-azure-sql-managed-instance-online-migrations"></a>Azure SQL yönetilen örnek çevrimiçi geçişleri SQL Server için özel roller

Azure veritabanı geçiş hizmeti, Azure hizmetleriyle etkileşim kurmak için bir uygulama KIMLIĞI kullanır. UYGULAMA KIMLIĞI, abonelik düzeyinde katılımcı rolü (birçok kurumsal güvenlik departmanının izin vermeyeceği) veya Azure veritabanı geçiş hizmeti 'nin gerektirdiği belirli izinleri veren özel roller oluşturmayı gerektirir. Azure Active Directory 'de özel rol 2.000 sınırlaması olduğundan, özellikle uygulama KIMLIĞI tarafından gereken tüm izinleri bir veya iki özel rolde birleştirmek ve ardından uygulama KIMLIĞINI belirli nesnelerde veya kaynak gruplarında (abonelik düzeyinde) özel rol olarak vermek isteyebilirsiniz. Özel rol sayısı sorun yaratmıyorsa, aşağıda açıklandığı gibi, toplamda üç özel rol oluşturmak için özel rolleri kaynak türüne göre bölebilirsiniz.

Rol tanımı JSON dizesinin Astifblescopes bölümü, portalda **rol ataması ekleme** Kullanıcı arabiriminde izinlerin nerede göründüğünü denetlemenize olanak tanır. Kullanıcı arabirimine ek roller ile karışıklık vermekten kaçınmak için kaynak grubunda veya hatta kaynak düzeyinde rolü tanımlamak isteyeceksiniz. Bunun gerçek rol atamasını gerçekleştirmediğini unutmayın.

## <a name="minimum-number-of-roles"></a>En az rol sayısı

Şu anda uygulama KIMLIĞI için, biri kaynak düzeyinde ve diğeri abonelik düzeyinde olmak üzere en az iki özel rol oluşturmanızı öneririz.

> [!NOTE]
> Yeni SQL yönetilen örnek kodu Azure 'a dağıtıldığı için son özel rol gereksinimi sonunda kaldırılabilir.

**Uygulama kimliği Için özel rol**. Bu rol, Azure veritabanı geçiş hizmeti 'ni barındıran *kaynak* veya *kaynak grubu* düzeyinde Azure veritabanı geçiş hizmeti GEÇIŞI IÇIN gereklidir (uygulama kimliği hakkında daha fazla bilgi Için, bkz. [Azure AD uygulaması ve kaynaklara erişebilen hizmet sorumlusu oluşturmak için portalı kullanma](../active-directory/develop/howto-create-service-principal-portal.md)makalesine bakın).

```json
{
  "Name": "DMS Role - App ID",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read",
        "Microsoft.DataMigration/locations/*",
        "Microsoft.DataMigration/services/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/ResourceGroups/<StorageAccount_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<ManagedInstance_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<DMS_rg_name>",
  ]
}
```

**Uygulama kimliği aboneliği Için özel rol**. Bu rol, SQL yönetilen örneğini barındıran *abonelik* düzeyinde Azure veritabanı geçiş hizmeti geçişi için gereklidir.

```json
{
  "Name": "DMS Role - App ID - Sub",
  "IsCustom": true,
  "Description": "DMS App ID access at subscription level to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>"
  ]
}
```

Yukarıdaki JSON üç metin dosyasında depolanmalıdır ve **New-AzureRmRoleDefinition (azurerd)** veya **New-AZROLEDEFINITION (az)** öğesini kullanarak rolleri oluşturmak Için Azurere, az PowerShell CMDLET 'lerini veya Azure CLI 'yi de kullanabilirsiniz.

Daha fazla bilgi için bkz. [Azure özel roller](../role-based-access-control/custom-roles.md).

Bu özel rolleri oluşturduktan sonra, kullanıcılara ve uygulama KIMLIĞINE, uygun kaynaklara veya kaynak gruplarına rol atamaları eklemeniz gerekir:

* Geçişler için kullanılacak uygulama KIMLIĞINE ve ayrıca depolama hesabında, Azure veritabanı geçiş hizmeti örneğine ve SQL yönetilen örnek kaynak düzeylerinde "DMS rol-uygulama KIMLIĞI" rolü verilmelidir. Azure veritabanı geçiş hizmeti 'ni barındıran kaynak veya kaynak grubu düzeyinde verilir.
* "DMS rol-uygulama KIMLIĞI-Sub" rolü, SQL yönetilen örneğini barındıran abonelik düzeyinde (kaynak veya kaynak grubunda verme başarısız olur) uygulama KIMLIĞINE verilmelidir. Bu gereksinim, bir kod güncelleştirmesi dağıtılana kadar geçicidir.

## <a name="expanded-number-of-roles"></a>Genişletilmiş rol sayısı

Azure Active Directory özel rol sayısı sorun yaratmıyorsa, toplam üç rol oluşturmanız önerilir. Yine de "DMS rol-uygulama KIMLIĞI – Sub" rolüne ihtiyacınız vardır, ancak yukarıdaki "DMS rol-uygulama KIMLIĞI" rolü kaynak türüne göre iki farklı role bölünür.

**SQL yönetilen örneği için uygulama KIMLIĞI için özel rol**

```json
{
  "Name": "DMS Role - App ID - SQL MI",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<ManagedInstance_rg_name>"
  ]
}
```

**Depolama için uygulama KIMLIĞI için özel rol**

```json
{
  "Name": "DMS Role - App ID - Storage",
  "IsCustom": true,
  "Description": "DMS App ID storage access to complete MI migrations",
  "Actions": [
"Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<StorageAccount_rg_name>"
  ]
}
```

## <a name="role-assignment"></a>Rol ataması

Kullanıcılara/uygulama KIMLIĞINE bir rol atamak için, Azure portal açın ve aşağıdaki adımları gerçekleştirin:

1. Kaynak grubuna veya kaynağa (abonelikte verilmesi gereken rol dışında) gidin, **Access Control**' a gidin ve ardından yeni oluşturduğunuz özel rolleri bulmak için kaydırın.

2. Uygun rolü seçin, uygulama KIMLIĞI ' ni seçin ve değişiklikleri kaydedin.

  Uygulama KIMLIĞINIZ artık **rol atamaları** sekmesinde listelenmiş olarak görüntülenir.

## <a name="next-steps"></a>Sonraki adımlar

* Microsoft [veritabanı geçiş kılavuzu](https://datamigration.microsoft.com/)' nda senaryonuz için geçiş kılavuzunu gözden geçirin.
