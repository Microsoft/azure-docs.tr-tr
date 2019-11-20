---
title: Premium Azure dosya paylaşma oluşturma
description: Bu makalede, Premium Azure dosya paylaşımının nasıl oluşturulacağını öğreneceksiniz.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 795f18365c4b4846d18eddf3212059040bf9e319
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260225"
---
# <a name="how-to-create-an-premium-azure-file-share"></a>Premium Azure dosya paylaşma oluşturma
Premium dosya paylaşımları, katı hal disk (SSD) depolama medyasında sunulur ve barındırma veritabanları ve yüksek performanslı bilgi işlem (HPC) dahil olmak üzere GÇ yoğunluklu iş yükleri için kullanışlıdır. Premium dosya paylaşımları, FileStorage hesabı olarak adlandırılan özel bir amaç depolama hesabı türünde barındırılır. Premium dosya paylaşımları, yüksek performans ve kurumsal ölçekli uygulamalar için tasarlanmıştır; tutarlı düşük gecikme süresi, yüksek ıOPS ve yüksek verimlilik paylaşımları sağlar.

Bu makalede [Azure Portal](https://portal.azure.com/), Azure PowerShell ve Azure CLI kullanarak bu yeni hesap türünü nasıl oluşturacağınız gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Premium Azure dosya paylaşımları dahil olmak üzere Azure kaynaklarına erişmek için bir Azure aboneliğine sahip olmanız gerekir. Aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Azure portal kullanarak Premium dosya paylaşma oluşturma

### <a name="sign-in-to-azure"></a>Azure'da oturum açma

[Azure Portal](https://portal.azure.com/) oturum açın.

### <a name="create-a-filestorage-storage-account"></a>FileStorage depolama hesabı oluşturma

Artık depolama hesabınızı oluşturmaya hazır olursunuz.

Her depolama hesabı bir Azure kaynak grubuna ait olmalıdır. Kaynak grubu, Azure hizmetlerinizi gruplandırmaya yönelik mantıksal bir kapsayıcıdır. Bir depolama hesabı oluşturduğunuzda, yeni bir kaynak grubu oluşturma veya var olan bir kaynak grubu kullanma seçeneğiniz vardır. Bu makalede, yeni bir kaynak grubu oluşturma gösterilmektedir.

1. Azure portal sol taraftaki menüden **depolama hesapları** ' nı seçin.

    ![Ana sayfa Azure portal depolama hesabı seçin](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. Açılan **Depolama Hesapları** penceresinde **Ekle**'yi seçin.
1. Depolama hesabının oluşturulacağı aboneliği seçin.
1. **Kaynak grubu** alanı altında **Yeni oluştur**’u seçin. Aşağıdaki görüntüde gösterildiği gibi yeni kaynak grubunuz için bir ad girin.

1. Ardından, depolama hesabınız için bir ad girin. Seçtiğiniz ad Azure genelinde benzersiz olmalıdır. Ad ayrıca 3 - 24 karakter uzunluğunda olmalıdır ve yalnızca rakam ve küçük harf içerebilir.
1. Depolama hesabınız için bir konum seçin veya varsayılan konumu kullanın.
1. **Performans** için **Premium**seçin.
1. **Hesap türü** ' nü seçin ve **FileStorage**' ı seçin.
1. **Çoğaltmayı** **yerel olarak yedekli depolama (LRS)** varsayılan değerine ayarlı bırakın.

    ![Premium dosya paylaşımında depolama hesabı oluşturma](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

1. Depolama hesabı ayarlarınızı gözden geçirmek ve hesabı oluşturmak için **Gözden Geçir + Oluştur**’u seçin.
1. **Oluştur**’u seçin.

Depolama hesabı kaynağınız oluşturulduktan sonra şuraya gidin.

### <a name="create-a-premium-file-share"></a>Premium dosya paylaşımı oluşturma

1. Depolama hesabının sol menüsünde **Dosya hizmeti** bölümüne kaydırın ve ardından **dosyalar**' ı seçin.
1. Premium dosya paylaşma oluşturmak için **dosya paylaşma** ' yı seçin.
1. Dosya paylaşımınız için bir ad ve istediğiniz kotayı girip **Oluştur**' u seçin.

> [!NOTE]
> Sağlanan paylaşım boyutları paylaşım kotası ile belirtilir, dosya paylaşımları sağlanan boyutta faturalandırılır, daha fazla ayrıntı için [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/storage/files/) bakın.

   ![Premium dosya paylaşımı oluşturma](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>Kaynakları temizleme

Bu makalede oluşturulan kaynakları temizlemek isterseniz, kaynak grubunu silmeniz yeterlidir. Kaynak grubunun silinmesi, ilişkili depolama hesabının yanı sıra kaynak grubuyla ilişkili diğer tüm kaynakları da siler.

## <a name="create-a-premium-file-share-using-powershell"></a>PowerShell kullanarak Premium dosya paylaşma oluşturma

### <a name="create-an-account-using-powershell"></a>PowerShell kullanarak hesap oluşturma

İlk olarak, en son sürümünü yükleyin [PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget) modülü.

Ardından, PowerShell modülünüzü yükseltin, Azure aboneliğinizde oturum açın, bir kaynak grubu oluşturun ve ardından bir depolama hesabı oluşturun.

### <a name="upgrade-your-powershell-module"></a>PowerShell modülünüzü yükseltin

PowerShell ile bir Premium dosya paylaşımıyla etkileşim kurmak için az. Storage Module sürüm 1.4.0 veya en son az. Storage modülünü yüklemeniz gerekir.

Yükseltilmiş izinlere sahip bir PowerShell oturumu açarak işleme başlayın.

Az. Storage modülünü Install:

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Azure aboneliğinizde oturum açın

Kullanım `Connect-AzAccount` izleyin ve komut ekrandaki kimlik doğrulaması yapın.

```powershell
Connect-AzAccount
```

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

PowerShell ile yeni bir kaynak grubu oluşturmak için [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) komutunu kullanın:

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-how-to-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-filestorage-storage-account"></a>FileStorage depolama hesabı oluşturma

PowerShell 'den bir dosya depolama depolama hesabı oluşturmak için [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) komutunu kullanın:

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>Premium dosya paylaşımı oluşturma

Artık bir dosya depolama hesabınız olduğuna göre, Premium bir dosya paylaşma oluşturabilirsiniz. [Yeni-Azstoragespaylaşma](/powershell/module/az.storage/New-AzStorageShare) cmdlet 'ini kullanarak bir tane oluşturun.

> [!NOTE]
> Sağlanan paylaşım boyutları paylaşım kotası ile belirtilir, dosya paylaşımları sağlanan boyutta faturalandırılır, daha fazla ayrıntı için [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/storage/files/) bakın.

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

### <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak grubunu ve yeni depolama hesabı dahil olmak üzere ilişkili kaynaklarını kaldırmak için [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanın: 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-a-premium-file-share-using-azure-cli"></a>Azure CLı kullanarak Premium dosya paylaşma oluşturma

Azure Cloud Shell başlamak için [Azure Portal](https://portal.azure.com)oturum açın.

CLı 'nın yerel yüklemesinde oturum açmak istiyorsanız, öncelikle en son sürüme sahip olduğunuzdan emin olun, sonra oturum aç komutunu çalıştırın:

```cli
az login
```

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure CLI ile yeni bir kaynak grubu oluşturmak için [az group create](/cli/azure/group) komutunu kullanın.

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-storage-account"></a>FileStorage depolama hesabı oluşturma

Azure CLı 'dan bir FileStorage depolama hesabı oluşturmak için [az Storage Account Create](/cli/azure/storage/account) komutunu kullanın.

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>Depolama hesabı anahtarını alma

Depolama hesabı anahtarları bir depolama hesabındaki kaynaklara erişimi denetler, bu makalede bir Premium dosya paylaşımının oluşturulması için anahtarı kullanırız. Bu anahtarlar, depolama hesabını oluşturduğunuzda otomatik olarak oluşturulur. [az storage account keys list](/cli/azure/storage/account/keys) komutunu kullanarak depolama hesabınızın depolama hesabı anahtarlarını alabilirsiniz:

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>Premium dosya paylaşımı oluşturma

Artık bir dosya depolama hesabınız olduğuna göre, Premium bir dosya paylaşma oluşturabilirsiniz. Bir tane oluşturmak için [az Storage Share Create](/cli/azure/storage/share) komutunu kullanın.

> [!NOTE]
> Sağlanan paylaşım boyutları paylaşım kotası ile belirtilir, dosya paylaşımları sağlanan boyutta faturalandırılır, daha fazla ayrıntı için [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/storage/files/) bakın.

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

### <a name="clean-up-resources"></a>Kaynakları temizleme

Kaynak grubunu ve yeni depolama hesabı dahil olmak üzere ilişkili kaynakları kaldırmak için [az group delete](/cli/azure/group) komutunu kullanın.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede bir Premium dosya paylaşma oluşturdunuz. Bu hesabın sunduğu performans hakkında daha fazla bilgi edinmek için Planlama Kılavuzu 'nun performans katmanı bölümüne ilerleyin.

> [!div class="nextstepaction"]
> [Dosya paylaşımının performans katmanları](storage-files-planning.md#file-share-performance-tiers)