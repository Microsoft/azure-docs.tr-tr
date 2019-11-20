---
title: Azure Data Lake Storage 2. depolama hesabı oluşturun | Microsoft Docs
description: Azure portal, Azure PowerShell veya Azure CLı kullanarak Data Lake Storage 2. erişimi olan yeni bir depolama hesabı oluşturmayı hızlı bir şekilde öğrenin.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 675d1889fc74474a1d732cb5d4e9f46c638ce200
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467930"
---
# <a name="create-an-azure-data-lake-storage-gen2-storage-account"></a>Azure Data Lake Storage 2. depolama hesabı oluşturma

Azure Data Lake Storage 2., Hadoop Dağıtılmış Dosya Sistemi (bir) ile çalışacak şekilde uyarlanmış yerel dizin tabanlı bir kapsayıcı sağlayan [hiyerarşik bir ad alanını destekler](data-lake-storage-introduction.md) . HDFS'den Data Lake Storage Gen2 verilerine erişim [ABFS sürücüsü](data-lake-storage-abfs-driver.md) aracılığıyla sağlanabilir.

Bu makalede, Azure CLı aracılığıyla Azure portal, Azure PowerShell veya kullanarak nasıl hesap oluşturacağınız gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun. 

|           | Önkoşul |
|-----------|--------------|
|Portal     | None         |
|PowerShell | Bu makale, PowerShell modülünü az. Storage sürüm **0,7** veya üstünü gerektirir. Geçerli sürümünüzü bulmak için `Get-Module -ListAvailable Az.Storage` komutunu çalıştırın. Bu komutu çalıştırdıktan sonra hiçbir sonuç görünmez veya **0,7** 'den daha düşük bir sürüm görüntülenirse, PowerShell modülünüzü yükseltmeniz gerekir. Bu kılavuzun [PowerShell modülünü yükseltme](#upgrade-your-powershell-module) bölümüne bakın.
|CLI        | Azure 'da oturum açabilir ve Azure CLı komutlarını iki şekilde çalıştırabilirsiniz: <ul><li>CLI komutlarını Azure portalında Azure Cloud Shell içinden çalıştırabilirsiniz </li><li>CLI yükleyip CLI komutlarını yerel olarak çalıştırabilirsiniz</li></ul>|

Komut satırında çalışırken Azure Cloud Shell'i çalıştırabilir veya CLI'yı yerel ortama yükleyebilirsiniz.

### <a name="use-azure-cloud-shell"></a>Azure Cloud Shell kullanma

Azure Cloud Shell doğrudan Azure portalının içinde çalıştırabileceğiniz ücretsiz bir Bash kabuğudur. Azure CLI, kabuğa önceden yüklenmiştir ve kabuk, hesabınızla birlikte kullanılacak şekilde yapılandırılmıştır. Azure portalının sağ üst tarafında yer alan menüdeki **Cloud Shell** düğmesine tıklayın:

[![Cloud Shell](./media/data-lake-storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Düğme, bu makaledeki adımları çalıştırmak için kullanabileceğiniz etkileşimli bir kabuk başlatır:

[![Portaldaki Cloud Shell penceresini gösteren ekran görüntüsü](./media/data-lake-storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>CLI’yi yerel olarak yükleme

Ayrıca, Azure CLI’yi yerel olarak yükleyip kullanabilirsiniz. Bu makalede, Azure CLı sürüm 2.0.38 veya üstünü çalıştırıyor olmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekirse bkz. [Azure CLI’yı yükleme](/cli/azure/install-azure-cli).

## <a name="create-a-storage-account-with-azure-data-lake-storage-gen2-enabled"></a>Azure Data Lake Storage 2. Nesil etkin bir depolama hesabı oluşturma

Azure depolama hesabı; Bloblar, dosyalar, kuyruklar, tablolar ve diskler içeren tüm Azure depolama veri nesnelerinizi içerir. Depolama hesabı, Azure depolama verileriniz için HTTP veya HTTPS üzerinden dünyanın herhangi bir yerinden erişilebilen benzersiz bir ad alanı sağlar. Azure Depolama hesabınızdaki veriler dayanıklı ve yüksek oranda kullanılabilir, güvenli ve yüksek düzeyde ölçeklenebilir.

> [!NOTE]
> Data Lake Storage Gen2 özelliklerinden faydalanmak için yeni depolama hesaplarını **StorageV2 (genel amaçlı V2)** türünde oluşturmanız gerekir.  

Depolama hesapları hakkında daha fazla bilgi için bkz. [Azure Depolama hesabına genel bakış](../common/storage-account-overview.md).

## <a name="create-an-account-using-the-azure-portal"></a>Azure portalı kullanarak bir hesap oluşturma

[Azure portalında](https://portal.azure.com) oturum açın.

### <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

Her depolama hesabı bir Azure kaynak grubuna ait olmalıdır. Kaynak grubu, Azure hizmetlerinizi gruplandırmaya yönelik mantıksal bir kapsayıcıdır. Bir depolama hesabı oluşturduğunuzda, yeni bir kaynak grubu oluşturma veya var olan bir kaynak grubu kullanma seçeneğiniz vardır. Bu makalede, yeni bir kaynak grubu oluşturma gösterilmektedir.

Azure portalında genel amaçlı v2 bir depolama hesabı oluşturmak için aşağıdaki adımları izleyin:

> [!NOTE]
> Hiyerarşik ad alanı şu anda tüm genel bölgelerde kullanılabilir.

1. Depolama hesabını oluşturmak istediğiniz aboneliği seçin.
2. Azure portal, **kaynak oluştur** düğmesini seçin ve ardından **depolama hesabı**' nı seçin.
3. **Kaynak grubu** alanı altında **Yeni oluştur**’u seçin. Yeni kaynak grubunuz için bir ad girin.
   
   Kaynak grubu, Azure hizmetlerinizi gruplandırmaya yönelik mantıksal bir kapsayıcıdır. Bir depolama hesabı oluşturduğunuzda, yeni bir kaynak grubu oluşturma veya var olan bir kaynak grubu kullanma seçeneğiniz vardır.

4. Ardından, depolama hesabınız için bir ad girin. Seçtiğiniz ad Azure genelinde benzersiz olmalıdır. Ad ayrıca 3 - 24 karakter uzunluğunda olmalıdır ve yalnızca rakam ve küçük harf içerebilir.
5. Konum seçin.
6. **StorageV2 (genel amaçlı v2)** **hesabının, hesap türü** açılan listesinde seçili şekilde göründüğünden emin olun.
7. İsteğe bağlı olarak şu alanların her birinde bulunan değerleri değiştirin: **performans**, **çoğaltma**, **erişim katmanı**. Bu seçenekler hakkında daha fazla bilgi edinmek için bkz. [Azure depolama 'Ya giriş](https://docs.microsoft.com/azure/storage/common/storage-introduction#azure-storage-services).
8. **Gelişmiş** sekmesini seçin.
10. **Data Lake Storage 2.** bölümünde **hiyerarşik ad alanını** **etkin**olarak ayarlayın.
11. Depolama hesabı oluşturmak için **gözden geçir + oluştur** ' a tıklayın.

Depolama hesabınız portaldan oluşturulmuş olur.

### <a name="clean-up-resources"></a>Kaynakları temizleme

Azure portalını kullanarak kaynak grubunu kaldırmak için:

1. Azure portalında sol taraftaki menüyü genişleterek hizmet menüsünü açın ve **Kaynak Grupları**'nı seçerek kaynak gruplarınızın listesini görüntüleyin.
2. Silinecek kaynak grubunu bulun ve listenin sağ tarafındaki **Daha fazla** düğmesine ( **...** ) sağ tıklayın.
3. **Kaynak grubunu sil**'i seçip onaylayın.

## <a name="create-an-account-using-powershell"></a>PowerShell kullanarak hesap oluşturma

İlk olarak [PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget) modülünün en son sürümünü yüklemeniz gerekir.

Ardından, PowerShell modülünüzü yükseltin, Azure aboneliğinizde oturum açın, bir kaynak grubu oluşturun ve ardından bir depolama hesabı oluşturun.

### <a name="upgrade-your-powershell-module"></a>PowerShell modülünüzü yükseltme

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

PowerShell 'i kullanarak Data Lake Storage 2. etkileşimde bulunmak için az. Storage sürüm **0,7** veya üzeri modülünü yüklemeniz gerekir.

Yükseltilmiş izinlerle bir PowerShell oturumu açarak başlayın.

Az. Storage modülünü yükler

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Azure aboneliğinizde oturum açın

`Login-AzAccount` komutunu kullanın ve kimlik doğrulaması yapmak için ekrandaki yönergeleri izleyin.

```powershell
Login-AzAccount
```

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

PowerShell ile yeni bir kaynak grubu oluşturmak için [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) komutunu kullanın: 

> [!NOTE]
> Hiyerarşik ad alanı şu anda tüm genel bölgelerde kullanılabilir.

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-quickstart-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-general-purpose-v2-storage-account"></a>Genel amaçlı v2 depolama hesabı oluşturma

Yerel olarak yedekli depolama (LRS) ile PowerShell 'den genel amaçlı v2 depolama hesabı oluşturmak için [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) komutunu kullanın:

```powershell
$location = "westus2"

New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "storagequickstart" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind StorageV2 `
  -EnableHierarchicalNamespace $True
```

### <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak grubunu ve yeni depolama hesabı dahil olmak üzere ilişkili kaynaklarını kaldırmak için [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanın: 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-an-account-using-azure-cli"></a>Azure CLI'yı kullanarak hesap oluşturma

Azure Cloud Shell başlamak için [Azure Portal](https://portal.azure.com)oturum açın.

CLı 'nın yerel yüklemesinde oturum açmak isterseniz, oturum açma komutunu çalıştırın:

```cli
az login
```

### <a name="add-the-cli-extension-for-azure-data-lake-gen-2"></a>Azure Data Lake Gen 2 için CLı uzantısını ekleyin

CLı kullanarak Data Lake Storage 2. etkileşimde bulunmak için, kabuğa bir uzantı eklemeniz gerekir.

Bunu yapmak için, Cloud Shell ya da yerel bir kabuğu kullanarak aşağıdaki komutu girin: `az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure CLI ile yeni bir kaynak grubu oluşturmak için [az group create](/cli/azure/group) komutunu kullanın.

```azurecli-interactive
az group create `
    --name storage-quickstart-resource-group `
    --location westus2
```

> [!NOTE]
> > Hiyerarşik ad alanı şu anda tüm genel bölgelerde kullanılabilir.

### <a name="create-a-general-purpose-v2-storage-account"></a>Genel amaçlı v2 depolama hesabı oluşturma

Azure CLI’dan yerel olarak yedekli depolama ile genel amaçlı v2 depolama hesabı oluşturmak için [az storage account create](/cli/azure/storage/account) komutunu kullanın.

```azurecli-interactive
az storage account create `
    --name storagequickstart `
    --resource-group storage-quickstart-resource-group `
    --location westus2 `
    --sku Standard_LRS `
    --kind StorageV2 `
    --hierarchical-namespace true
```

### <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak grubunu ve yeni depolama hesabı dahil olmak üzere ilişkili kaynakları kaldırmak için [az group delete](/cli/azure/group) komutunu kullanın.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede Data Lake Storage 2. özellikleri olan bir depolama hesabı oluşturdunuz. Depolama hesabınıza blob 'ları yükleme ve indirme hakkında bilgi edinmek için aşağıdaki konuya bakın.

* [AzCopy Ile v10 arasındaki](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
