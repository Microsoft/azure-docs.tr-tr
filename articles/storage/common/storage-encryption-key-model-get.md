---
title: Depolama hesabı için hangi şifreleme anahtarı modelinin kullanımda olduğunu belirleme
titleSuffix: Azure Storage
description: Depolama hesabı için şifreleme anahtarlarının nasıl yönetildiğini denetlemek için Azure portal, PowerShell veya Azure CLı kullanın. Anahtarlar Microsoft tarafından (varsayılan) veya müşteri tarafından yönetilebilir. Müşteri tarafından yönetilen anahtarların Azure Key Vault içinde depolanması gerekir.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/13/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 08bc36500bbd95633d1cb1d02bf10a7397401aa4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780128"
---
# <a name="determine-which-azure-storage-encryption-key-model-is-in-use-for-the-storage-account"></a>Depolama hesabı için hangi Azure depolama şifreleme anahtarı modelinin kullanımda olduğunu belirleme

Depolama hesabınızdaki veriler Azure depolama tarafından otomatik olarak şifrelenir. Azure depolama şifrelemesi, depolama hesabı düzeyinde şifreleme anahtarlarını yönetmeye yönelik iki seçenek sunar:

- **Microsoft tarafından yönetilen anahtarlar.** Varsayılan olarak, Microsoft, depolama hesabınızı şifrelemek için kullanılan anahtarları yönetir.
- **Müşteri tarafından yönetilen anahtarlar.** İsteğe bağlı olarak, depolama hesabınız için şifreleme anahtarlarını yönetmeyi seçebilirsiniz. Müşteri tarafından yönetilen anahtarların Azure Key Vault içinde depolanması gerekir.

Ayrıca, bazı BLOB depolama işlemleri için tek bir istek düzeyinde bir şifreleme anahtarı sağlayabilirsiniz. İstekte bir şifreleme anahtarı belirtildiğinde, bu anahtar depolama hesabında etkin olan şifreleme anahtarını geçersiz kılar. Daha fazla bilgi için bkz. [BLOB depolama için istekte müşteri tarafından sağlanmış anahtar belirtme](../blobs/storage-blob-customer-provided-key.md).

Şifreleme anahtarları hakkında daha fazla bilgi için bkz. [bekleyen veriler Için Azure depolama şifrelemesi](storage-service-encryption.md).

## <a name="check-the-encryption-key-model-for-the-storage-account"></a>Depolama hesabı için şifreleme anahtarı modelini denetleyin

Bir depolama hesabının şifreleme için Microsoft tarafından yönetilen anahtarları veya müşteri tarafından yönetilen anahtarları kullanıp kullanmadığını öğrenmek için aşağıdaki yaklaşımlardan birini kullanın.

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Azure portal kullanarak depolama hesabının şifreleme modelini denetlemek için şu adımları izleyin:

1. Azure portalında depolama hesabınıza gidin.
1. **Şifreleme** ayarını seçin ve ayarı göz önünde edin.

Aşağıdaki görüntüde, Microsoft tarafından yönetilen anahtarlarla şifrelenen bir depolama hesabı gösterilmektedir:

![Microsoft tarafından yönetilen anahtarlarla şifrelenen hesabı görüntüle](media/storage-encryption-key-model-get/microsoft-managed-encryption-key-setting-portal.png)

Aşağıdaki görüntüde, müşteri tarafından yönetilen anahtarlarla şifrelenen bir depolama hesabı gösterilmektedir:

![Azure portal 'de şifreleme anahtarı ayarını gösteren ekran görüntüsü](media/storage-encryption-key-model-get/customer-managed-encryption-key-setting-portal.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell kullanarak depolama hesabının şifreleme modelini denetlemek için [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) komutunu çağırın, sonra hesabın **KeySource** özelliğini denetleyin.

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$account.Encryption.KeySource
```

**KeySource** özelliğinin değeri ise `Microsoft.Storage` , hesap Microsoft tarafından yönetilen anahtarlarla şifrelenir. **KeySource** özelliğinin değeri ise `Microsoft.Keyvault` Hesap, müşteri tarafından yönetilen anahtarlarla şifrelenir.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Azure CLı kullanarak depolama hesabının şifreleme modelini denetlemek için [az Storage Account Show](/cli/azure/storage/account#az_storage_account_show) komutunu çağırın, sonra hesabın **KeySource** özelliğini denetleyin.

```azurecli-interactive
key_source=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query encryption.keySource \
    --output tsv)
```

**KeySource** özelliğinin değeri ise `Microsoft.Storage` , hesap Microsoft tarafından yönetilen anahtarlarla şifrelenir. **KeySource** özelliğinin değeri ise `Microsoft.Keyvault` Hesap, müşteri tarafından yönetilen anahtarlarla şifrelenir.

---

## <a name="next-steps"></a>Sonraki adımlar

- [Bekleyen veri için Azure Depolama şifrelemesi](storage-service-encryption.md)
- [Azure depolama şifrelemesi için müşteri tarafından yönetilen anahtarlar](customer-managed-keys-overview.md)
