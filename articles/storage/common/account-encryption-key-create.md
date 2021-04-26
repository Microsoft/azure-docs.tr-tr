---
title: Tablolar ve kuyruklar için müşteri tarafından yönetilen anahtarları destekleyen bir hesap oluşturun
titleSuffix: Azure Storage
description: Tablolar ve kuyruklar için müşteri tarafından yönetilen anahtarları yapılandırmayı destekleyen bir depolama hesabı oluşturmayı öğrenin. Azure depolama şifrelemesi için hesap şifreleme anahtarını temel alan bir depolama hesabı oluşturmak için Azure CLı veya Azure Resource Manager şablonunu kullanın. Daha sonra hesap için müşteri tarafından yönetilen anahtarları yapılandırabilirsiniz.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/31/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 4c86811ee72d2713fced6320a17d1ccde1866d99
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769958"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Tablolar ve kuyruklar için müşteri tarafından yönetilen anahtarları destekleyen bir hesap oluşturun

Azure depolama, bekleyen bir depolama hesabındaki tüm verileri şifreler. Varsayılan olarak, kuyruk depolama ve tablo depolama, hizmet kapsamındaki ve Microsoft tarafından yönetilen bir anahtar kullanır. Ayrıca, kuyruk veya tablo verilerini şifrelemek için müşteri tarafından yönetilen anahtarlar kullanmayı da tercih edebilirsiniz. Müşteri tarafından yönetilen anahtarları kuyruklar ve tablolarla birlikte kullanmak için, öncelikle hesap kapsamında olan ve hizmet yerine, bir şifreleme anahtarı kullanan bir depolama hesabı oluşturmanız gerekir. Kuyruk ve tablo verileri için hesap şifreleme anahtarını kullanan bir hesap oluşturduktan sonra, bu depolama hesabı için müşteri tarafından yönetilen anahtarları yapılandırabilirsiniz.

Bu makalede, hesap kapsamındaki bir anahtara dayanan bir depolama hesabının nasıl oluşturulacağı açıklanır. Hesap ilk oluşturulduğunda, Microsoft hesap anahtarını hesaptaki verileri şifrelemek için kullanır ve Microsoft bu anahtarı yönetir. Daha sonra, kendi anahtarlarınızı sağlamak, anahtar sürümünü güncelleştirmek, anahtarları döndürmek ve erişim denetimlerini iptal etmek dahil olmak üzere bu avantajlardan yararlanmak için hesap için müşteri tarafından yönetilen anahtarları yapılandırabilirsiniz.

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Hesap şifreleme anahtarını kullanan bir hesap oluşturun

Depolama hesabını oluşturduğunuz sırada kuyruklar ve tablolar için hesap şifreleme anahtarını kullanmak üzere yeni bir depolama hesabı yapılandırmanız gerekir. Şifreleme anahtarının kapsamı, hesap oluşturulduktan sonra değiştirilemez.

Depolama hesabının genel amaçlı v2 türünde olması gerekir. Depolama hesabı oluşturabilir ve bunu, Azure CLı veya bir Azure Resource Manager şablonu kullanarak hesap şifreleme anahtarına göre yapılandırabilirsiniz.

> [!NOTE]
> Depolama hesabı oluşturulduğunda, yalnızca kuyruk ve tablo depolaması, hesap şifreleme anahtarıyla verileri şifrelemek için isteğe bağlı olarak yapılandırılabilir. BLOB depolama ve Azure dosyaları her zaman, verileri şifrelemek için hesap şifreleme anahtarını kullanır.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell kullanarak hesap şifreleme anahtarına dayanan bir depolama hesabı oluşturmak için, Azure PowerShell modülünü, sürüm 3.4.0 veya üstünü yüklediğinizden emin olun. Daha fazla bilgi için bkz. [Azure PowerShell modülünü Install](/powershell/azure/install-az-ps).

Ardından, [Yeni-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) komutunu çağırarak uygun parametrelerle bir genel amaçlı v2 depolama hesabı oluşturun:

- `-EncryptionKeyTypeForQueue`' İ ve değerini `Account` kuyruk depolamadaki verileri şifrelemek için hesap şifreleme anahtarını kullanacak şekilde ayarlayın.
- Seçeneğini ekleyin `-EncryptionKeyTypeForTable` ve değerini `Account` tablo depolamadaki verileri şifrelemek için hesap şifreleme anahtarını kullanacak şekilde ayarlayın.

Aşağıdaki örnek, Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS) için yapılandırılmış ve hem sıra hem de tablo depolama için verileri şifrelemek üzere hesap şifreleme anahtarını kullanan genel amaçlı v2 depolama hesabının nasıl oluşturulacağını gösterir. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Hesap şifreleme anahtarına dayanan bir depolama hesabı oluşturmak için Azure CLı 'yi kullanmak için Azure CLı sürüm 2.0.80 veya üstünü yüklediğinizden emin olun. Daha fazla bilgi için bkz. [Azure CLI 'Yı yüklerken](/cli/azure/install-azure-cli).

Ardından, [az Storage Account Create](/cli/azure/storage/account#az_storage_account_create) komutunu çağırarak, uygun parametrelerle genel amaçlı v2 depolama hesabı oluşturun:

- `--encryption-key-type-for-queue`' İ ve değerini `Account` kuyruk depolamadaki verileri şifrelemek için hesap şifreleme anahtarını kullanacak şekilde ayarlayın.
- Seçeneğini ekleyin `--encryption-key-type-for-table` ve değerini `Account` tablo depolamadaki verileri şifrelemek için hesap şifreleme anahtarını kullanacak şekilde ayarlayın.

Aşağıdaki örnek, Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS) için yapılandırılmış ve hem sıra hem de tablo depolama için verileri şifrelemek üzere hesap şifreleme anahtarını kullanan genel amaçlı v2 depolama hesabının nasıl oluşturulacağını gösterir. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[Şablon](#tab/template)

Aşağıdaki JSON örneği, Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS) için yapılandırılmış ve hem sıra hem de tablo depolama için verileri şifrelemek üzere hesap şifreleme anahtarını kullanan genel amaçlı bir v2 depolama hesabı oluşturur. Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

Hesap şifreleme anahtarına dayanan bir hesap oluşturduktan sonra, Azure Key Vault veya Key Vault yönetilen donanım güvenlik modeli (HSM) (Önizleme) içinde depolanan müşteri tarafından yönetilen anahtarları yapılandırabilirsiniz. Müşteri tarafından yönetilen anahtarları bir anahtar kasasında nasıl depolayacağınızı öğrenmek için, bkz. [Azure Key Vault depolanan müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma](customer-managed-keys-configure-key-vault.md). Yönetilen bir HSM 'de müşteri tarafından yönetilen anahtarların nasıl depolanacağını öğrenmek için bkz. [Azure Key Vault YÖNETILEN HSM 'de depolanan müşteri tarafından yönetilen anahtarlarla şifrelemeyi yapılandırma (Önizleme)](customer-managed-keys-configure-key-vault-hsm.md).

## <a name="verify-the-account-encryption-key"></a>Hesap şifreleme anahtarını doğrulama

Bir depolama hesabındaki bir hizmetin hesap şifreleme anahtarını kullandığını doğrulamak için Azure CLı [az Storage Account](/cli/azure/storage/account#az_storage_account_show) komutunu çağırın. Bu komut, bir depolama hesabı özellikleri kümesi ve değerlerini döndürür. `keyType`Şifreleme özelliği içindeki her bir hizmetin alanını bulun ve olarak ayarlandığını doğrulayın `Account` .

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Bir depolama hesabındaki bir hizmetin hesap şifreleme anahtarını kullandığını doğrulamak için [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) komutunu çağırın. Bu komut, bir depolama hesabı özellikleri kümesi ve değerlerini döndürür. `KeyType`Özelliğin içindeki her bir hizmet için alanı bulun `Encryption` ve olarak ayarlandığını doğrulayın `Account` .

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Bir depolama hesabındaki bir hizmetin hesap şifreleme anahtarını kullandığını doğrulamak için [az Storage Account Show](/cli/azure/storage/account#az_storage_account_show) komutunu çağırın. Bu komut, bir depolama hesabı özellikleri kümesi ve değerlerini döndürür. `keyType`Şifreleme özelliği içindeki her bir hizmetin alanını bulun ve olarak ayarlandığını doğrulayın `Account` .

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Şablon](#tab/template)

Yok

---

## <a name="pricing-and-billing"></a>Fiyatlandırma ve Faturalama

Hesap kapsamındaki bir şifreleme anahtarını kullanmak için oluşturulan bir depolama hesabı, tablo depolama kapasitesi ve işlemleri için varsayılan hizmet kapsamındaki anahtarı kullanan bir hesaptan farklı bir hızda faturalandırılır. Ayrıntılar için bkz. [Azure Tablo Depolama fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/tables/).

## <a name="next-steps"></a>Sonraki adımlar

- [Bekleyen veri için Azure Depolama şifrelemesi](storage-service-encryption.md)
- [Azure depolama şifrelemesi için müşteri tarafından yönetilen anahtarlar](customer-managed-keys-overview.md)
- [Azure Key Vault nedir](../../key-vault/general/overview.md)?