---
title: Geçici silme özelliğini etkinleştirme-Azure dosya paylaşımları
description: Veri kurtarma için Azure dosya paylaşımlarında geçici silme özelliğini etkinleştirmeyi ve yanlışlıkla silmeyi engellemeyi öğrenin.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: a0dff310ce4a40b7a66cc548f3c77213f4a10e00
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717032"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Azure dosya paylaşımlarında geçici silme özelliğini etkinleştirme

Azure depolama, dosya paylaşımları için geçici silme olanağı sunarak, verileri yanlışlıkla bir uygulama veya başka bir depolama hesabı kullanıcısı tarafından daha kolay kurtarabilirsiniz. Geçici silme hakkında daha fazla bilgi edinmek için bkz. [Azure dosya paylaşımlarının yanlışlıkla silinmesini engelleme](storage-files-prevent-file-share-deletion.md).

Aşağıdaki bölümlerde, var olan bir depolama hesabında Azure dosya paylaşımları için geçici silme 'nin nasıl etkinleştirileceği ve kullanılacağı gösterilmektedir:

# <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="getting-started"></a>Kullanmaya başlama

1. [Azure portal](https://portal.azure.com/) oturum açın.
1. Depolama hesabınıza gidin ve **veri depolama** alanındaki **dosya paylaşımları** ' nı seçin.
1. **Geçici silme** seçeneğinin yanında **etkin** ' i seçin.
1. **Tüm dosya paylaşımları Için geçici silme** **etkin** ' i seçin.
1. **Dosya paylaşma saklama süresini gün olarak** seçin ve seçtiğiniz bir sayıyı girin.
1. Veri saklama ayarlarınızı onaylamak için **Kaydet** ' i seçin.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-enable-soft-delete-new-ui.png" alt-text="Depolama hesabı geçici silme ayarları bölmesinin ekran görüntüsü. Dosya paylaşımları geçici silme bölümünü vurgulama, geçişi etkinleştirme, bekletme süresi ayarlama ve kaydetme. Bu, Depolama hesabınızdaki tüm dosya paylaşımları için geçici silme olanağı sağlar.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Geçici silme cmdlet 'leri, sürüm 2.1.3 ve [Azure CLI modülünün](/cli/azure/install-azure-cli)daha yeni sürümlerinde kullanılabilir.

## <a name="getting-started-with-cli"></a>CLı ile çalışmaya başlama

Geçici silme özelliğini etkinleştirmek için bir dosya istemcisinin hizmet özelliklerini güncelleştirmeniz gerekir. Aşağıdaki örnek, bir depolama hesabındaki tüm dosya paylaşımları için geçici silme imkanı sunar:

```azurecli
az storage account file-service-properties update --enable-delete-retention true -n yourStorageaccount -g yourResourceGroup
```

Geçici silmenin etkinleştirilip etkinleştirilmediğini doğrulayabilirsiniz ve bekletme ilkesini aşağıdaki komutla görüntüleyebilirsiniz:

```azurecli
az storage account file-service-properties show -n yourStorageaccount -g yourResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>Önkoşul

Geçici silme cmdlet 'leri, az. Storage modülünün 4.8.0 ve daha yeni sürümlerinde kullanılabilir. 

## <a name="getting-started-with-powershell"></a>PowerShell ile çalışmaya başlayın

Geçici silme özelliğini etkinleştirmek için bir dosya istemcisinin hizmet özelliklerini güncelleştirmeniz gerekir. Aşağıdaki örnek, bir depolama hesabındaki tüm dosya paylaşımları için geçici silme imkanı sunar:

```azurepowershell-interactive
$rgName = "yourResourceGroupName"
$accountName = "yourStorageAccountName"

Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $true -ShareRetentionDays 7
```

Geçici silmenin etkinleştirilip etkinleştirilmediğini doğrulayabilirsiniz ve bekletme ilkesini aşağıdaki komutla görüntüleyebilirsiniz:

```azurepowershell-interactive
Get-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName
```
---

## <a name="restore-soft-deleted-file-share"></a>Geçici olarak silinen dosya payını geri yükle

# <a name="portal"></a>[Portal](#tab/azure-portal)

Geçici olarak silinen bir dosya paylaşımının geri yüklenmesi için:

1. Depolama hesabınıza gidin ve **dosya paylaşımları**' nı seçin.
1. Dosya paylaşımı dikey penceresinde, geçici olarak silinen paylaşımları görüntülemek için **silinen paylaşımları göster** ' i etkinleştirin.

    Bu, şu anda **Silinmiş** durumdaki tüm paylaşımları görüntüler.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Durum sütunu, Ad sütununun yanındaki sütun silindi olarak ayarlandıysa, dosya paylaşımınız geçici olarak silinmiş durumda olur. Ve, belirtilen saklama süresinden sonra kalıcı olarak silinir.":::

1. Paylaşıma ve **silmeyi geri al**' ı seçtiğinizde, bu, paylaşımın geri yükleneceği

    Durumu **etkin** olarak geçiş yaptığından paylaşımın geri yüklendiğini doğrulayabilirsiniz.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Durum sütunu, Ad sütununun yanındaki sütun etkin olarak ayarlanırsa, dosya paylaşımınız geri yüklendi.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Geçici silme cmdlet 'leri, Azure CLı 'nin 2.1.3 sürümünde kullanılabilir. Geçici olarak silinen bir dosya paylaşımının geri yüklenmesi için önce `--deleted-version` paylaşımın değerini almalısınız. Bu değeri almak için, depolama hesabınız için tüm silinen paylaşımları listelemek üzere aşağıdaki komutu kullanın:

```azurecli
az storage share-rm list --storage-account yourStorageaccount --include-deleted
```

Geri yüklemek istediğiniz paylaşmayı tanımladıktan sonra geri yüklemek için aşağıdaki komutla birlikte kullanabilirsiniz:

```azurecli
az storage share-rm restore -n deletedshare --deleted-version 01D64EB9886F00C4 -g yourResourceGroup --storage-account yourStorageaccount
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Geçici silme cmdlet 'leri, az. Storage modülünün 4.8.0 ve daha yeni sürümlerinde kullanılabilir. Geçici olarak silinen bir dosya paylaşımının geri yüklenmesi için önce `-DeletedShareVersion` paylaşımın değerini almalısınız. Bu değeri almak için, depolama hesabınız için tüm silinen paylaşımları listelemek üzere aşağıdaki komutu kullanın:

```azurepowershell-interactive
Get-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -IncludeDeleted
```

Geri yüklemek istediğiniz paylaşmayı tanımladıktan sonra geri yüklemek için aşağıdaki komutla birlikte kullanabilirsiniz:

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>Geçici silmeyi devre dışı bırak

Geçici silme kullanmayı durdurmak istiyorsanız bu yönergeleri izleyin. Geçici olarak silinen bir dosya paylaşımının kalıcı olarak silinmesi için, silmeyi geri alma, geçici silme devre dışı bırakma ve sonra yeniden silme işlemini yapmanız gerekir. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Depolama hesabınıza gidin ve **veri depolama** alanındaki **dosya paylaşımları** ' nı seçin.
1. **Geçici silme** seçeneğinin yanındaki bağlantıyı seçin.
1. **Tüm dosya paylaşımları Için geçici silme** Için **devre dışı** seçeneğini belirleyin.
1. Veri saklama ayarlarınızı onaylamak için **Kaydet** ' i seçin.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-disable-soft-delete.png" alt-text="Geçici silme devre dışı bırakıldığında, Depolama hesabınızdaki tüm dosya paylaşımlarını boş olarak hemen ve kalıcı olarak silebilirsiniz.":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Geçici silme cmdlet 'leri, Azure CLı 'nin 2.1.3 sürümünde kullanılabilir. Depolama hesabınızda geçici silme devre dışı bırakmak için aşağıdaki komutu kullanabilirsiniz:

```azurecli
az storage account file-service-properties update --enable-delete-retention false -n yourStorageaccount -g yourResourceGroup
```
# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Geçici silme cmdlet 'leri, az. Storage modülünün 4.8.0 ve daha yeni sürümlerinde kullanılabilir. Depolama hesabınızda geçici silme devre dışı bırakmak için aşağıdaki komutu kullanabilirsiniz:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Sonraki adımlar

Başka bir veri koruma ve kurtarma biçimi hakkında bilgi edinmek için bkz. [Azure dosyaları için anlık görüntü paylaşma makalesine genel bakış](storage-snapshots-files.md).