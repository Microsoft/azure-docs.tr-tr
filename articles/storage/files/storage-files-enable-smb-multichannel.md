---
title: Çok kanallı SMB 'yi etkinleştirme
description: Azure Premium dosya paylaşımlarında çok kanallı SMB 'yi nasıl etkinleştirebileceğinizi öğrenin.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: da4e1a58aef28e5c47100a0311ff81a5af04a918
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718990"
---
# <a name="enable-smb-multichannel-on-a-filestorage-account-preview"></a>Dosya depolama hesabında çok kanallı SMB 'yi etkinleştirme (Önizleme) 

Azure FileStorage hesapları, Premium dosya paylaşımlarınıza birden çok ağ bağlantısı kurarak SMB 3. x istemcisinden performansı artıran SMB çok kanallı (Önizleme) desteği sağlar. Bu makalede, var olan bir depolama hesabında çok kanallı SMB 'yi etkinleştirmek için adım adım yönergeler sağlanmaktadır. Azure dosyaları SMB çok kanallı hakkında ayrıntılı bilgi için bkz. çok kanallı SMB performansı.

## <a name="limitations"></a>Sınırlamalar

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Bölgesel kullanılabilirlik

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="prerequisites"></a>Önkoşullar

- [Bir dosya depolama hesabı oluşturun](./storage-how-to-create-file-share.md).
- Azure PowerShell modülünü kullanmayı düşünüyorsanız, [modülün 3.0.1-Preview sürümünü yükleyebilirsiniz](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview).

## <a name="getting-started"></a>Kullanmaya başlama

Bir PowerShell penceresi açın ve Azure aboneliğinizde oturum açın. Buradan, aşağıdaki komutlarla çok kanallı SMB önizlemesine kaydolabilirsiniz.

```azurepowershell
Connect-AzAccount
# Setting your active subscription to the one you want to register for the preview. 
# Replace the <subscription-id> placeholder with your subscription id. 
$context = Get-AzSubscription -SubscriptionId <your-subscription-id> 
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage 
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage 
```

> [!NOTE]
> Kayıt, bir saate kadar sürebilir.

### <a name="verify-that-feature-registration-is-complete"></a>Özellik kaydının tamamlandığını doğrulama

Depolama hesabınızda özelliğin etkinleştirilmesi bir saate kadar sürelediğinden, aboneliğiniz için kayıtlı olduğunu doğrulamak üzere aşağıdaki komutu kullanmak isteyebilirsiniz:

```azurepowershell
Get-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage
```


## <a name="enable-smb-multichannel"></a>Çok Kanallı SMB’yi etkinleştirme 
Bir dosya depolama hesabı oluşturduktan sonra, depolama hesabınız için çok kanallı SMB ayarlarını güncelleştirmek üzere yönergeleri izleyebilirsiniz.

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Azure portal oturum açın ve çok kanallı SMB 'yi yapılandırmak istediğiniz dosya depolama depolama hesabına gidin.
1. Dosya **hizmeti** altında dosya **paylaşımları** ' nı seçin ve **dosya paylaşım ayarları**' nı seçin.
1. **SMB çok kanallı** (veya devre dışı bırakmak için **kapalı** ) **seçeneğini açıp** **Kaydet**' i seçin.

:::image type="content" source="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png" alt-text="Depolama hesabının ekran görüntüsü, çok kanallı SMB açık olarak açıldı."  lightbox="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png":::

SMB çok kanallı seçeneği **dosya paylaşma ayarları** altında görülemiyorsa veya yapılandırma güncelleştirilirken ayar hatası güncelleştirilemediğiniz takdirde, aboneliğinizin kaydedildiğinden ve hesabınızın desteklenen hesap türü ve çoğaltma ile [desteklenen bölgelerden](#regional-availability) birinde bulunduğundan emin olun.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell modülünü kullanarak çok kanallı SMB 'yi etkinleştirmek için, modülün [3.0.1-Preview sürümünü yüklemelisiniz](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview) .

`$resourceGroupName` `$storageAccountName` Bu PowerShell komutlarını çalıştırmadan önce değişkenleri ve kaynak grubunuzu ve depolama hesabınızı ayarlayın.

```azurepowershell
# Enable SMB Multichannel on the premium storage account that's in one of the supported regions
Update-AzStorageFileServiceProperty -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -EnableSmbMultichannel $true 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLı, çok kanallı SMB yapılandırmayı henüz desteklememektedir. Depolama hesabında çok kanallı SMB 'yi yapılandırmak için Portal yönergelerine bakın.

---

> [!NOTE]
> SMB çok kanallı yapılandırma ayarlarındaki değişiklikler, depolama hesabı altındaki tüm dosya paylaşımları için geçerlidir. Değişikliklerin etkili olması için, bu paylaşımın istemci üzerinde yeniden bağlama yapmanız gerekir.


## <a name="next-steps"></a>Sonraki adımlar 

- Çok kanallı SMB 'nin avantajlarından yararlanmak için [Dosya paylaşımınızı uzaktan bağlayın](storage-how-to-use-files-windows.md) .
- [Çok KANALLı SMB ile ilgili sorunları giderin](storage-troubleshooting-files-performance.md#smb-multichannel-option-not-visible-under-file-share-settings).
- İyileştirmeler hakkında daha fazla bilgi edinmek için bkz. çok [kanallı SMB performansı](storage-files-smb-multichannel-performance.md)
 - Windows SMB çok kanallı özelliği hakkında daha fazla bilgi edinmek için bkz. [Manage SMB Mulitchannel](/azure-stack/hci/manage/manage-smb-multichannel).