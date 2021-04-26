---
title: Blob sürüm oluşturmayı etkinleştirme ve yönetme
titleSuffix: Azure Storage
description: Azure portal blob sürümü oluşturmayı nasıl etkinleştireceğinizi veya bir Azure Resource Manager şablonu kullanarak öğrenin.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/09/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: f6a1d315342ea98ccaf1382630eccca876ada3f1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870202"
---
# <a name="enable-and-manage-blob-versioning"></a>Blob sürüm oluşturmayı etkinleştirme ve yönetme

Bir blob 'un önceki sürümlerini değiştirildiğinde veya silindiğinde blob Storage sürümü oluşturmayı etkinleştirebilirsiniz. Blob sürümü oluşturma etkinleştirildiğinde, yanlışlıkla değiştirildiyse veya silinirse verilerinizi kurtarmak için bir Blobun önceki bir sürümünü geri yükleyebilirsiniz.

Bu makalede, Azure portal veya bir Azure Resource Manager şablonu kullanılarak depolama hesabı için blob sürüm oluşturma 'nın nasıl etkinleştirileceği veya devre dışı bırakılacağı gösterilmektedir. Blob sürümü oluşturma hakkında daha fazla bilgi için bkz. [BLOB sürümü oluşturma](versioning-overview.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="enable-blob-versioning"></a>Blob sürümü oluşturmayı etkinleştirme

# <a name="azure-portal"></a>[Azure portalı](#tab/portal)

Azure portal bir depolama hesabı için blob sürüm oluşturmayı etkinleştirmek için:

1. Portalda depolama hesabınıza gidin.
1. **BLOB hizmeti** altında **veri koruma**' yı seçin.
1. **Sürüm oluşturma** bölümünde, **etkin**' i seçin.

:::image type="content" source="media/versioning-enable/portal-enable-versioning.png" alt-text="Azure portal blob sürümü oluşturmayı nasıl etkinleştireceğinizi gösteren ekran görüntüsü":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell ile bir depolama hesabı için blob sürüm oluşturmayı etkinleştirmek için önce [az. Storage](https://www.powershellgallery.com/packages/Az.Storage) Module Version 2.3.0 veya üstünü yüklemeniz gerekir. Ardından, aşağıdaki örnekte gösterildiği gibi, sürüm oluşturmayı etkinleştirmek için [Update-AzStorageBlobServiceProperty](/powershell/module/az.storage/update-azstorageblobserviceproperty) komutunu çağırın. Açılı ayraçlar içindeki değerleri kendi değerlerinizle değiştirmeyi unutmayın:

```powershell
# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -IsVersioningEnabled $true
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLı ile bir depolama hesabı için blob sürüm oluşturmayı etkinleştirmek üzere önce Azure CLı sürüm 2.2.0 veya üstünü yüklemeniz gerekir. Ardından, aşağıdaki örnekte gösterildiği gibi, sürüm oluşturmayı etkinleştirmek için [az Storage Account blob-Service-Properties Update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) komutunu çağırın. Açılı ayraçlar içindeki değerleri kendi değerlerinizle değiştirmeyi unutmayın:

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-versioning true
```

# <a name="template"></a>[Şablon](#tab/template)

Bir şablonla blob sürümü oluşturmayı etkinleştirmek için, **ısversioningenabled** özelliği **true** olan bir şablon oluşturun. Aşağıdaki adımlar Azure portal bir şablonun nasıl oluşturulacağını açıklamaktadır.

1. Azure portal **kaynak oluştur**' u seçin.
1. **Market 'Te ara**' te, **şablon dağıtımı** yazın ve ardından **ENTER** tuşuna basın.
1. **Şablon dağıtımı** öğesini seçin, **Oluştur**' u seçin ve ardından **düzenleyicide kendi şablonunuzu oluştur**' u seçin.
1. Şablon Düzenleyicisi 'nde aşağıdaki JSON öğesine yapıştırın. `<accountName>`Yer tutucusunu depolama hesabınızın adıyla değiştirin.
1. Şablonu kaydedin.
1. Hesabın kaynak grubunu belirtin ve ardından şablonu dağıtmak ve BLOB sürümü oluşturmayı etkinleştirmek için **satın al** düğmesini seçin.

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts/blobServices",
                "apiVersion": "2019-06-01",
                "name": "<accountName>/default",
                "properties": {
                    "IsVersioningEnabled": true
                }
            }
        ]
    }
    ```

Azure portal şablonlar ile kaynak dağıtma hakkında daha fazla bilgi için bkz. [Azure Portal ile kaynak dağıtma](../../azure-resource-manager/templates/deploy-portal.md).

---

## <a name="modify-a-blob-to-trigger-a-new-version"></a>Yeni bir sürüm tetiklemek için blobu değiştirme

Aşağıdaki kod örneği, .NET için Azure depolama istemci kitaplığı, sürüm [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) veya daha yeni bir sürümün nasıl tetikleneceğini göstermektedir. Bu örneği çalıştırmadan önce, depolama hesabınız için sürüm oluşturmayı etkinleştirdiğinizden emin olun.

Örnek bir Blok Blobu oluşturur ve ardından Blobun meta verilerini günceller. Blob 'un meta verilerini güncelleştirme, yeni bir sürümün oluşturulmasını tetikler. Örnek, ilk sürümü ve geçerli sürümü alır ve yalnızca geçerli sürümün meta verileri içerdiğini gösterir.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_UpdateVersionedBlobMetadata":::

## <a name="list-blob-versions"></a>Blob sürümlerini Listele

Blob sürümlerini veya anlık görüntüleri .NET V12 istemci kitaplığı ile listelemek için **Sürüm** alanıyla [blobstates](/dotnet/api/azure.storage.blobs.models.blobstates) parametresini belirtin.

Aşağıdaki kod örneği, .NET için Azure Storage istemci kitaplığı, sürüm [12.5.1](https://www.nuget.org/packages/Azure.Storage.Blobs/12.5.1) veya daha yeni bir sürümü ile Blobların nasıl ekleneceğini gösterir. Bu örneği çalıştırmadan önce, depolama hesabınız için sürüm oluşturmayı etkinleştirdiğinizden emin olun.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ListBlobVersions":::

## <a name="next-steps"></a>Sonraki adımlar

- [Blob sürümü oluşturma](versioning-overview.md)
- [Azure Depolama blobları için geçici silme](./soft-delete-blob-overview.md)