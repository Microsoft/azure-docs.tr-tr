---
title: Verilerin çift şifrelenmesi için altyapı şifrelemesi etkinleştirilmiş bir depolama hesabı oluşturma
titleSuffix: Azure Storage
description: Verilerin güvenliğini sağlamaya yönelik daha yüksek düzeyde güvence gerektiren müşteriler, Azure depolama altyapısı düzeyinde 256 bit AES şifrelemesini de etkinleştirebilir. Altyapı şifrelemesi etkinleştirildiğinde, bir depolama hesabındaki veriler iki farklı şifreleme algoritması ve iki farklı anahtar ile iki kez şifrelenir.
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 23b3ca919be030490cca06f31dac623d7f80be44
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790392"
---
# <a name="create-a-storage-account-with-infrastructure-encryption-enabled-for-double-encryption-of-data"></a>Verilerin çift şifrelenmesi için altyapı şifrelemesi etkinleştirilmiş bir depolama hesabı oluşturma

Azure depolama, hizmet düzeyindeki bir depolama hesabındaki tüm verileri otomatik olarak 256 bit AES şifrelemesi kullanarak şifreler, en güçlü blok şifrelemeleri kullanılabilir ve FIPS 140-2 uyumludur. Verilerin güvenliğini sağlamaya yönelik daha yüksek düzeyde güvence gerektiren müşteriler, Azure depolama altyapısı düzeyinde 256 bit AES şifrelemesini de etkinleştirebilir. Altyapı şifrelemesi etkinleştirildiğinde, bir depolama hesabındaki veriler, &mdash; hizmet düzeyinde iki kez ve altyapı düzeyinde &mdash; iki farklı şifreleme algoritmalarıyla bir kez şifrelenir. Azure depolama verilerinin çift şifrelemesi, şifreleme algoritmalarından veya anahtarlardan birinin tehlikeye girdiği bir senaryoya karşı koruma sağlar. Bu senaryoda, ek şifreleme katmanı verilerinizi korumaya devam eder.

Hizmet düzeyi şifreleme, Azure Key Vault veya Key Vault yönetilen donanım güvenlik modeli (HSM) (Önizleme) ile Microsoft tarafından yönetilen anahtarların veya müşteri tarafından yönetilen anahtarların kullanılmasını destekler. Altyapı düzeyinde şifreleme, Microsoft tarafından yönetilen anahtarları kullanır ve her zaman ayrı bir anahtar kullanır. Azure depolama şifrelemesi ile anahtar yönetimi hakkında daha fazla bilgi için bkz. [şifreleme anahtarı yönetimi hakkında](storage-service-encryption.md#about-encryption-key-management).

Verilerinizi daha da şifrelemek için, önce altyapı şifrelemesi için yapılandırılmış bir depolama hesabı oluşturmanız gerekir. Bu makalede, altyapı şifrelemesini sağlayan bir depolama hesabının nasıl oluşturulacağı açıklanır.

## <a name="register-to-use-infrastructure-encryption"></a>Altyapı şifrelemesini kullanmak için kaydolun

Altyapı Şifrelemesi etkin olan bir depolama hesabı oluşturmak için, önce PowerShell veya Azure CLı kullanarak bu özelliği Azure ile kullanmak üzere kaydolmanız gerekir.

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Yok

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell 'e kaydolmak için [register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) komutunu çağırın.

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

Kayıt durumunuzu PowerShell 'e göre denetlemek için [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) komutunu çağırın.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

Kaydınız onaylandıktan sonra Azure depolama kaynak sağlayıcısı 'nı yeniden kaydetmeniz gerekir. Kaynak sağlayıcıyı PowerShell ile yeniden kaydetmek için [register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) komutunu çağırın.

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı ile kaydolmak için [az Feature Register](/cli/azure/feature#az_feature_register) komutunu çağırın.

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

Azure CLı ile kaydlarınızın durumunu denetlemek için [az Feature](/cli/azure/feature#az_feature_show) komutunu çağırın.

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

Kaydınız onaylandıktan sonra Azure depolama kaynak sağlayıcısı 'nı yeniden kaydetmeniz gerekir. Kaynak sağlayıcısını Azure CLı ile yeniden kaydetmek için [az Provider Register](/cli/azure/provider#az_provider_register) komutunu çağırın.

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Şablon](#tab/template)

Yok

---

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>Altyapı Şifrelemesi etkin bir hesap oluşturma

Bir depolama hesabını, hesabı oluşturduğunuz sırada altyapı şifrelemesini kullanacak şekilde yapılandırmanız gerekir. Depolama hesabının genel amaçlı v2 türünde olması gerekir.

Hesap oluşturulduktan sonra altyapı şifrelemesi etkinleştirilemez veya devre dışı bırakılamaz.

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

PowerShell kullanarak altyapı Şifrelemesi etkin bir depolama hesabı oluşturmak için aşağıdaki adımları izleyin:

1. Azure portal **depolama hesapları** sayfasına gidin.
1. Yeni bir genel amaçlı v2 depolama hesabı eklemek için **Ekle** düğmesini seçin.
1. **Gelişmiş** sekmesinde **altyapı** şifrelemesini bulun ve **etkin**' i seçin.
1. Depolama hesabı oluşturma işleminin bitmesini istiyorsanız **gözden geçir + oluştur** ' u seçin.

    :::image type="content" source="media/infrastructure-encryption-enable/create-account-infrastructure-encryption-portal.png" alt-text="Hesap oluştururken altyapı şifrelemesini etkinleştirmeyi gösteren ekran görüntüsü":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Altyapı Şifrelemesi etkin olan bir depolama hesabı oluşturmak üzere PowerShell 'i kullanmak için, [az. Storage PowerShell modülünü](https://www.powershellgallery.com/packages/Az.Storage), sürüm 2.2.0 veya üstünü yüklediğinizden emin olun. Daha fazla bilgi için bkz. [ınstall Azure PowerShell](/powershell/azure/install-az-ps).

Ardından, [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) komutunu çağırarak genel amaçlı v2 depolama hesabı oluşturun. `-RequireInfrastructureEncryption`Altyapı şifrelemesini etkinleştirme seçeneğini ekleyin.

Aşağıdaki örnek, Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS) için yapılandırılmış bir genel amaçlı v2 depolama hesabı oluşturmayı ve verilerin çift şifrelenmesi için altyapı Şifrelemesi etkin olduğunu gösterir. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Altyapı Şifrelemesi etkin olan bir depolama hesabı oluşturmak için Azure CLı 'yi kullanmak için Azure CLı sürüm 2.8.0 veya üstünü yüklediğinizden emin olun. Daha fazla bilgi için bkz. [Azure CLI 'Yı yüklerken](/cli/azure/install-azure-cli).

Ardından, [az Storage Account Create](/cli/azure/storage/account#az_storage_account_create) komutunu çağırarak ve `--require-infrastructure-encryption option` altyapı şifrelemesini etkinleştirmek için ' yi dahil ederek genel amaçlı v2 depolama hesabı oluşturun.

Aşağıdaki örnek, Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS) için yapılandırılmış bir genel amaçlı v2 depolama hesabı oluşturmayı ve verilerin çift şifrelenmesi için altyapı Şifrelemesi etkin olduğunu gösterir. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --require-infrastructure-encryption
```

# <a name="template"></a>[Şablon](#tab/template)

Aşağıdaki JSON örneği, Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS) için yapılandırılmış bir genel amaçlı v2 depolama hesabı oluşturur ve verilerin çift şifrelenmesi için altyapı şifrelemesi etkinleştirilmiştir. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

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
                "keySource": "Microsoft.Storage",
                "requireInfrastructureEncryption": true,
                "services": {
                    "blob": { "enabled": true },
                    "file": { "enabled": true }
              }
            }
        }
    }
],
```

---

## <a name="verify-that-infrastructure-encryption-is-enabled"></a>Altyapı şifrelemesinin etkinleştirildiğini doğrulama

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Altyapı şifrelemesinin Azure portal bir depolama hesabı için etkinleştirildiğini doğrulamak için şu adımları izleyin:

1. Azure portalda depolama hesabınıza gidin.
1. **Ayarlar** altında **şifreleme**' yi seçin.

    :::image type="content" source="media/infrastructure-encryption-enable/verify-infrastructure-encryption-portal.png" alt-text="Hesap için altyapı şifrelemesinin etkin olduğunu nasıl doğrulayabildiğinizi gösteren ekran görüntüsü":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell ile bir depolama hesabı için altyapı şifrelemesinin etkinleştirildiğini doğrulamak için [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) komutunu çağırın. Bu komut, bir depolama hesabı özellikleri kümesi ve değerlerini döndürür. `RequireInfrastructureEncryption`Özelliğin içindeki alanı alın `Encryption` ve olarak ayarlandığını doğrulayın `True` .

Aşağıdaki örnek, özelliğinin değerini alır `RequireInfrastructureEncryption` . Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı ile bir depolama hesabı için altyapı şifrelemesinin etkinleştirildiğini doğrulamak üzere [az Storage Account Show](/cli/azure/storage/account#az_storage_account_show) komutunu çağırın. Bu komut, bir depolama hesabı özellikleri kümesi ve değerlerini döndürür. `requireInfrastructureEncryption`Özelliğin içindeki alanı bulun `encryption` ve olarak ayarlandığını doğrulayın `true` .

Aşağıdaki örnek, özelliğinin değerini alır `requireInfrastructureEncryption` . Açılı ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli-interactive
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Şablon](#tab/template)

Yok

---

## <a name="next-steps"></a>Sonraki adımlar

- [Bekleyen veri için Azure Depolama şifrelemesi](storage-service-encryption.md)
- [Azure depolama şifrelemesi için müşteri tarafından yönetilen anahtarlar](customer-managed-keys-overview.md)
