---
title: Azure Cosmos DB kaynakların silinmesini veya değiştirilmesini engelle
description: Azure Cosmos DB kaynakların silinmesini veya değiştirilmesini engellemek için Azure kaynak kilitlerini kullanın.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/06/2020
ms.author: mjbrown
ms.openlocfilehash: 1c8c766208132aec115e1fbeb15af3a057c3de3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94636716"
---
# <a name="prevent-azure-cosmos-db-resources-from-being-deleted-or-changed"></a>Azure Cosmos DB kaynakların silinmesini veya değiştirilmesini engelle
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Yönetici olarak, kuruluşunuzdaki diğer kullanıcıların yanlışlıkla önemli kaynakları silmesini veya değiştirmesini engellemek için bir Azure Cosmos hesabını, veritabanını veya kapsayıcısını kilitlemeniz gerekebilir. Kilit düzeyini CanNotDelete veya ReadOnly olarak ayarlayabilirsiniz.

- **Cannotdelete** , yetkili kullanıcıların bir kaynağı hala okuyabilecekleri ve değiştirebilecekleri anlamına gelir, ancak kaynakları silemez.
- **ReadOnly** , yetkili kullanıcıların bir kaynağı okuyabilecekleri anlamına gelir, ancak kaynakları silemez veya güncelleştiremez. Bu kilidi uygulamak, tüm yetkili kullanıcıları okuyucu rolü tarafından verilen izinlerle kısıtlamak için benzerdir.

## <a name="how-locks-are-applied"></a>Kilitlerin uygulanma şekli

Üst kapsamda bir kilit uyguladığınızda, bu kapsamdaki tüm kaynaklar aynı kilidi alır. Daha sonra eklediğiniz kaynaklar bile kilidi üst öğeden alır. Devralmada en kısıtlayıcı kilit öncelik kazanır.

Azure rol tabanlı erişim denetiminden farklı olarak, tüm kullanıcılar ve roller arasında bir kısıtlama uygulamak için yönetim kilitlerini kullanırsınız. Azure Cosmos DB için Azure RBAC hakkında bilgi edinmek için [Azure Cosmos DB Azure rol tabanlı erişim denetimi](role-based-access-control.md)bölümüne bakın.

Resource Manager kilitleri yalnızca yönetim düzleminde gerçekleşen ve https://management.azure.com adresine gönderilen işlemlere uygulanır. Kilitler kaynakların kendi işlevlerini gerçekleştirmesine bir kısıtlama getirmez. Kaynak değişiklikleri kısıtlanır ama kaynak işlemleri kısıtlanmaz. Örneğin, bir Azure Cosmos kapsayıcısındaki bir salt okunur kilit, kapsayıcıyı silmenizi veya değiştirmenizi önler. Bu, kapsayıcıda veri oluşturmanızı, güncelleştirmenizi veya silmenizi engellemez. Bu işlemler https://management.azure.com hedefine gönderilmediğinden veri işlemlerine izin verilir.

## <a name="manage-locks"></a>Kilitleri yönetme

> [!WARNING]
> Kaynak kilitleri, Azure Cosmos hesabı, disableKeyBasedMetadataWriteAccess özelliği etkinleştirilerek ilk kilitlenmediği takdirde hesap anahtarlarını kullanarak Azure Cosmos DB erişen kullanıcılar tarafından yapılan değişiklikler için çalışmaz. Bu özelliği etkinleştirmeden önce herhangi bir SDK Azure portal 'yı kullanarak kaynaklarda değişiklik yapan mevcut uygulamaları bozmadığından ve hesap anahtarları aracılığıyla bağlanan ve kaynakları değiştirme, Dizin ilkelerini güncelleştirme gibi kaynakları değiştiren var olan uygulamaları bozmadığından emin olmak için dikkatli olunması gerekir. Daha fazla bilgi edinmek ve uygulamalarınızın çalışmaya devam etmesini sağlamak üzere bir denetim listesini görmek için [Azure Cosmos DB SDK 'larından değişiklikleri önlemek](role-based-access-control.md#prevent-sdk-changes) için

### <a name="powershell"></a>PowerShell

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "my-cosmos-account"
$lockName = "$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
Update-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName -DisableKeyBasedMetadataWriteAccess true

# Create a Delete Lock on an Azure Cosmos account resource and all child resources
New-AzResourceLock `
    -ApiVersion "2020-04-01" `
    -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
    -ResourceGroupName $resourceGroupName `
    -ResourceName $accountName `
    -LockName $lockName `
    -LockLevel "CanNotDelete" # CanNotDelete or ReadOnly
```

### <a name="azure-cli"></a>Azure CLI

```bash
resourceGroupName='myResourceGroup'
accountName='my-cosmos-account'
$lockName="$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
az cosmosdb update  --name $accountName --resource-group $resourceGroupName  --disable-key-based-metadata-write-access true

# Create a Delete Lock on an Azure Cosmos account resource
az lock create --name $lockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/databaseAccount \
    --lock-type 'CanNotDelete' # CanNotDelete or ReadOnly \
    --resource $accountName
```

### <a name="template"></a>Şablon

Bir Azure Cosmos DB kaynağına bir kilit uygularken aşağıdaki biçimleri kullanın:

- ada `{resourceName}/Microsoft.Authorization/{lockName}`
- türüyle `{resourceProviderNamespace}/{resourceType}/providers/locks`

> [!IMPORTANT]
> Mevcut bir Azure Cosmos hesabını değiştirirken, bu özellikle redploying sırasında hesabınız ve alt kaynaklarınızın diğer özelliklerini eklediğinizden emin olun. Bu şablonu olduğu gibi dağıtmayın veya tüm hesap özelliklerinizi sıfırlayacaktır.

```json
"resources": [
    {
        "type": "Microsoft.DocumentDB/databaseAccounts",
        "name": "[variables('accountName')]",
        "apiVersion": "2020-04-01",
        "kind": "GlobalDocumentDB",
        "location": "[parameters('location')]",
        "properties": {
            "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
            "locations": "[variables('locations')]",
            "databaseAccountOfferType": "Standard",
            "enableAutomaticFailover": "[parameters('automaticFailover')]",
            "disableKeyBasedMetadataWriteAccess": true
        }
    },
    {
        "type": "Microsoft.DocumentDB/databaseAccounts/providers/locks",
        "apiVersion": "2020-04-01",
        "name": "[concat(variables('accountName'), '/Microsoft.Authorization/siteLock')]",
        "dependsOn": [
        "[resourceId('Microsoft.DocumentDB/databaseAccounts', variables('accountName'))]"
        ],
        "properties": {
        "level": "CanNotDelete",
        "notes": "Cosmos account should not be deleted."
        }
    }
]
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Resource Manager kilitlere genel bakış](../azure-resource-manager/management/lock-resources.md)
