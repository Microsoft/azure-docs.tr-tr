---
title: Büyük dosya paylaşımlarını etkinleştirme ve oluşturma-Azure dosyaları
description: Bu makalede, büyük dosya paylaşımlarını etkinleştirme ve oluşturma hakkında bilgi edineceksiniz.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/20/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a53f964020583c41e2400d97ad244bacd33813bc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818269"
---
# <a name="enable-and-create-large-file-shares"></a>Büyük dosya paylaşımlarını etkinleştirme ve oluşturma
Azure dosya paylaşımlarınız, depolama hesabınızda büyük dosya paylaşımlarını etkinleştirdikten sonra 100 TiB 'ye kadar ölçeklenebilir. Büyük dosya paylaşımlarını etkinleştirdiğinizde dosya paylaşımınızın ıOPS ve aktarım hızı sınırlarını de artırabilir. Ayrıca, mevcut ve yeni dosya paylaşımları için mevcut depolama hesaplarınızda bu ölçeklendirmeyi etkinleştirebilirsiniz. Performans farkları hakkında daha fazla bilgi için bkz. [dosya paylaşma ve dosya ölçeği hedefleri](storage-files-scale-targets.md#azure-files-scale-targets).

## <a name="prerequisites"></a>Ön koşullar
- Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
- Azure CLı 'yı kullanmayı planlıyorsanız [en son sürümü yükleyebilirsiniz](/cli/azure/install-azure-cli).
- Azure PowerShell modülünü kullanmayı düşünüyorsanız, [en son sürümü yükleyebilirsiniz](/powershell/azure/install-az-ps).

## <a name="restrictions"></a>Kısıtlamalar
Şimdilik, büyük dosya paylaşımları etkinleştirilmiş depolama hesaplarında yalnızca yerel olarak yedekli depolama (LRS) veya bölge yedekli depolama (ZRS) kullanabilirsiniz. Coğrafi bölge yedekli depolama (GZRS), coğrafi olarak yedekli depolama (GRS), Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS) veya Okuma Erişimli Coğrafi bölge-yedekli depolama (RA-GZRS) kullanamazsınız.

Hesapta büyük dosya paylaşımlarının etkinleştirilmesi geri alınamaz bir işlemdir. Bu uygulamayı etkinleştirdikten sonra, hesabınızı GZRS, GRS, RA-GRS veya RA-GZRS ' e dönüştüremeyeceksiniz.

## <a name="create-a-new-storage-account"></a>Yeni depolama hesabı oluşturma

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Azure portal, **tüm hizmetler**' i seçin. 
1. Kaynak listesinde **depolama hesapları**' nı girin. Siz yazarken, liste, girişinizi temel alarak filtreler. **Depolama hesapları**' nı seçin.
1. Görüntülenen **depolama hesapları** dikey penceresinde **+ Yeni**' yi seçin.
1. Temel bilgiler dikey penceresinde, yaptığınız seçimleri doldurun.
1. **Performansın** **Standart** olarak ayarlandığından emin olun.
1. **Artıklığı** **yerel olarak yedekli depolama** ya da bölgesel olarak **yedekli depolama** olarak ayarlayın.
1. **Gelişmiş** dikey penceresini seçin ve ardından **büyük dosya paylaşımlarının** sağ tarafındaki **etkin** seçenek düğmesini seçin.
1. Depolama hesabı ayarlarınızı gözden geçirmek ve hesabı oluşturmak için **Gözden Geçir + Oluştur**’u seçin.
1. **Oluştur**’u seçin.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Büyük dosya paylaşımları etkin olan bir depolama hesabı oluşturmak için aşağıdaki komutu kullanın. `<yourStorageAccountName>`, `<yourResourceGroup>` Ve `<yourDesiredRegion>` bilgilerinizi ile değiştirin.

```powershell
# This command creates a large file share–enabled account. It will not support changing the 
# redundancy to GRS, GZRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount `
    -ResourceGroupName <yourResourceGroup> `
    -Name <yourStorageAccountName> `
    -Location <yourDesiredRegion> `
    -SkuName Standard_LRS `
    -EnableLargeFileShare
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Büyük dosya paylaşımları etkin olan bir depolama hesabı oluşturmak için aşağıdaki komutu kullanın. `<yourStorageAccountName>`, `<yourResourceGroup>` Ve `<yourDesiredRegion>` bilgilerinizi ile değiştirin.

```azurecli-interactive
# This command creates a large file share–enabled account. It will not support changing the 
# redundancy to GRS, GZRS, RA-GRS, or RA-GZRS.
az storage account create \
    --name <yourStorageAccountName> \
    -g <yourResourceGroup> \
    -l <yourDesiredRegion> \
    --sku Standard_LRS \
    --kind StorageV2 \
    --enable-large-file-share
```

---

Büyük bir dosya paylaşma etkinleştirilmiş depolama hesabı oluşturduktan sonra, büyük kapasite ve ölçek limitlerinden yararlanan bir dosya paylaşma oluşturabilirsiniz. Depolama hesapları ve Azure dosya paylaşımları oluşturma hakkında daha fazla bilgi için bkz. [Azure dosya paylaşımı oluşturma](storage-how-to-create-file-share.md).

## <a name="enable-large-files-shares-on-an-existing-account"></a>Mevcut bir hesapta büyük dosya paylaşımlarını etkinleştir
Ayrıca, mevcut LRS ve ZRS depolama hesaplarınızda büyük dosya paylaşımlarını da etkinleştirebilirsiniz. GRS, GZRS, RA-GRS veya RA-GZRS hesabınız varsa, devam etmeden önce bunu LRS hesabına dönüştürmeniz gerekir.

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. [Azure Portal](https://portal.azure.com)açın ve büyük dosya paylaşımlarını etkinleştirmek istediğiniz depolama hesabına gidin.
1. Depolama hesabını açın ve **dosya paylaşımları**' nı seçin.
1. **Büyük dosya paylaşımlarında** **etkin** ' i seçin ve ardından **Kaydet**' i seçin.
1. **Genel bakış** ' ı ve **Yenile**' yi seçin.
1. **Kapasiteyi paylaşma** **' yı seçin ve ardından** **100 TİB** ' yi seçin.

    :::image type="content" source="media/storage-files-how-to-create-large-file-share/files-enable-large-file-share-existing-account.png" alt-text="Azure depolama hesabı, 100 TİB paylaşımlarının bulunduğu dosya paylaşımları dikey penceresinin ekran görüntüsü.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Mevcut hesabınızda büyük dosya paylaşımlarını etkinleştirmek için aşağıdaki komutu kullanın. `<yourStorageAccountName>`Ve `<yourResourceGroup>` bilgilerinizi ile değiştirin.

```powershell
Set-AzStorageAccount `
    -ResourceGroupName <yourResourceGroup> `
    -Name <yourStorageAccountName> `
    -EnableLargeFileShare
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Mevcut hesabınızda büyük dosya paylaşımlarını etkinleştirmek için aşağıdaki komutu kullanın. `<yourStorageAccountName>`Ve `<yourResourceGroup>` bilgilerinizi ile değiştirin.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

---

Artık depolama hesabınızda büyük dosya paylaşımlarını etkinleştirdiniz. Daha sonra, daha fazla kapasitenin ve ölçeğin avantajlarından yararlanmak için [mevcut paylaşımın kotasını güncelleştirmeniz](#expand-existing-file-shares) gerekir. 

> [!Important]  
> Mevcut dosya paylaşımları, kota değiştirilmediği takdirde büyük dosya paylaşımları için tanıtılan sınırlara kadar ölçeklendirmeyecektir.

## <a name="expand-existing-file-shares"></a>Mevcut dosya paylaşımlarını Genişlet
Depolama hesabınızda büyük dosya paylaşımlarını etkinleştirdikten sonra, artan kapasiteden ve ölçeklendirmeye yararlanmak için bu depolama hesabındaki mevcut dosya paylaşımlarını genişletmeniz gerekir. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Depolama hesabınızdan **dosya paylaşımları**' nı seçin.
1. Dosya paylaşımınıza sağ tıklayıp **Kota**' i seçin.
1. İstediğiniz yeni boyutu girip **Tamam**' ı seçin.

![Mevcut dosya paylaşımlarının kotasına sahip Azure portal Kullanıcı arabirimi](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Kotayı en büyük boyuta ayarlamak için aşağıdaki komutu kullanın. `<YourResourceGroupName>`, `<YourStorageAccountName>` Ve `<YourStorageAccountFileShareName>` bilgilerinizi ile değiştirin.

```powershell
$resourceGroupName = "<YourResourceGroupName>"
$storageAccountName = "<YourStorageAccountName>"
$shareName="<YourStorageAccountFileShareName>"

# update quota
Set-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 102400
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Kotayı en büyük boyuta ayarlamak için aşağıdaki komutu kullanın. `<yourResourceGroupName>`, `<yourStorageAccountName>` Ve `<yourFileShareName>` bilgilerinizi ile değiştirin.

```azurecli-interactive
az storage share-rm update \
    --resource-group <yourResourceGroupName> \
    --storage-account <yourStorageAccountName> \
    --name <yourFileShareName> \
    --quota 102400
```

---

## <a name="next-steps"></a>Sonraki adımlar
* [Windows üzerinde bir dosya paylaşımının bağlantısını oluşturma ve bağlama](storage-how-to-use-files-windows.md)
* [Linux 'ta bir dosya paylaşımının bağlantısını oluşturma ve bağlama](storage-how-to-use-files-linux.md)
* [MacOS 'ta bir dosya paylaşımının bağlantısını oluşturma ve bağlama](storage-how-to-use-files-mac.md)