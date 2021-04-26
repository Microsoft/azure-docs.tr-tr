---
title: Bloblar için geçici silmeyi etkinleştirme
titleSuffix: Azure Storage
description: Blob verilerini yanlışlıkla silmeleri veya üzerine yazılmalardan korumak için Blobların geçici silinmesini etkinleştirin.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/27/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4a8d1f872ca042429276b8f0e1112bc5837d8e38
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869284"
---
# <a name="enable-soft-delete-for-blobs"></a>Bloblar için geçici silmeyi etkinleştirme

Blob geçici silme, belirli bir süre boyunca silinen verileri korumak için tek bir blobu ve sürümlerini, anlık görüntülerini ve meta verilerini yanlışlıkla silmekten veya üzerine yazacak şekilde korur. Saklama süresi boyunca Blobun silme sırasında durumuna geri yükleyebilirsiniz. Saklama süresi dolduktan sonra blob kalıcı olarak silinir. Blob geçici silme hakkında daha fazla bilgi için bkz. [Bloblar Için geçici silme](soft-delete-blob-overview.md).

Blob geçici silme, blob verileri için kapsamlı bir veri koruma stratejisinin bir parçasıdır. Microsoft 'un veri koruma önerileri hakkında daha fazla bilgi edinmek için bkz. [veri korumasına genel bakış](data-protection-overview.md).

## <a name="enable-blob-soft-delete"></a>Blob geçici silmeyi etkinleştir

Blob geçici silme, yeni bir depolama hesabı için varsayılan olarak devre dışıdır. Azure portal, PowerShell veya Azure CLı kullanarak dilediğiniz zaman bir depolama hesabı için geçici silmeyi etkinleştirebilir veya devre dışı bırakabilirsiniz.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure portal kullanarak depolama hesabınızda blob geçici silme özelliğini etkinleştirmek için şu adımları izleyin:

1. [Azure portalında](https://portal.azure.com/) depolama hesabınıza gidin.
1. **BLOB hizmeti** altında **veri koruma** seçeneğini bulun.
1. **Kurtarma** bölümünde, **Bloblar Için geçici silme özelliğini aç**' ı seçin.
1. 1 ila 365 gün arasında bir saklama süresi belirtin. Microsoft, en az yedi günlük bekletme süresi önerir.
1. Yaptığınız değişiklikleri kaydedin.

:::image type="content" source="media/soft-delete-blob-enable/blob-soft-delete-configuration-portal.png" alt-text="Azure portal geçici silmenin nasıl etkinleştirileceğini gösteren ekran görüntüsü":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell ile blob geçici silme özelliğini etkinleştirmek için, saklama süresini gün olarak belirterek [Enable-AzStorageBlobDeleteRetentionPolicy](/powershell/module/az.storage/enable-azstorageblobdeleteretentionpolicy) komutunu çağırın.

Aşağıdaki örnek, blob geçici silmeyi sağlar ve bekletme süresini yedi güne ayarlar. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurepowershell
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7
```

Blob geçici silme geçerli ayarlarını denetlemek için [Get-AzStorageBlobServiceProperty](/powershell/module/az.storage/get-azstorageblobserviceproperty) komutunu çağırın:

```azurepowershell
$properties = Get-AzStorageBlobServiceProperty -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$properties.DeleteRetentionPolicy.Enabled
$properties.DeleteRetentionPolicy.Days
```

# <a name="cli"></a>[CLI](#tab/azure-CLI)

Azure CLı ile blob geçici silme özelliğini etkinleştirmek için [az Storage Account blob-Service-Properties Update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) komutunu çağırın, bu durumda saklama süresini gün olarak belirtin.

Aşağıdaki örnek, blob geçici silmeyi sağlar ve bekletme süresini yedi güne ayarlar. Köşeli ayraçlar içindeki yer tutucu değerlerini kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli-interactive
az storage account blob-service-properties update --account-name <storage-account> \
    --resource-group <resource-group> \
    --enable-delete-retention true \
    --delete-retention-days 7
```

Blob geçici silme için geçerli ayarları denetlemek için [az Storage Account blob-Service-Properties Show](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_show) komutunu çağırın:

```azurecli-interactive
az storage account blob-service-properties show --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Sonraki adımlar

- [Bloblar için geçici silme](soft-delete-blob-overview.md)
- [Geçici olarak silinen Blobları yönetme ve geri yükleme](soft-delete-blob-manage.md)
