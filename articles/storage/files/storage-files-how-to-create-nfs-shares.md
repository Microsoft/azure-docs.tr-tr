---
title: NFS paylaşma oluşturma-Azure dosyaları (Önizleme)
description: Ağ dosya sistemi protokolü kullanılarak iliştirilenebilir bir Azure dosya paylaşımının nasıl oluşturulacağını öğrenin.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: b549c625f0a6ff0480eafc38f84d292e66350950
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717140"
---
# <a name="how-to-create-an-nfs-share"></a>NFS paylaşma oluşturma
Azure dosya paylaşımları, bulutta yaşayan tamamen yönetilen dosya paylaşımlardır. Bu makalede NFS protokolünü kullanan bir dosya paylaşımının oluşturulması ele alınmaktadır. Her iki protokol hakkında daha fazla bilgi için bkz. [Azure dosya paylaşma protokolleri](storage-files-compare-protocols.md).

## <a name="limitations"></a>Sınırlamalar
[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Bölgesel kullanılabilirlik
[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Önkoşullar
- NFS paylaşımlarına yalnızca güvenilen ağlardan erişilebilir. NFS paylaşımınızla kurulan bağlantılar aşağıdaki kaynaklardan birinden kaynaklanmalıdır:
    - [Özel uç nokta oluşturun](storage-files-networking-endpoints.md#create-a-private-endpoint) (önerilir) ya da [genel uç noktanıza erişimi kısıtlayın](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Azure dosyaları ile kullanmak üzere Linux üzerinde Noktadan siteye (P2S) VPN yapılandırın](storage-files-configure-p2s-vpn-linux.md).
    - [Azure dosyaları ile kullanmak Için siteden sıteye VPN yapılandırın](storage-files-configure-s2s-vpn.md).
    - [ExpressRoute](../../expressroute/expressroute-introduction.md)'ı yapılandırın.

- Azure CLı 'yı kullanmayı planlıyorsanız [en son sürümü yükleyebilirsiniz](/cli/azure/install-azure-cli).

## <a name="register-the-nfs-41-protocol"></a>NFS 4,1 protokolünü kaydetme
Azure PowerShell modülünü veya Azure CLı kullanıyorsanız, aşağıdaki komutları kullanarak özelliğinizi kaydedin:

# <a name="portal"></a>[Portal](#tab/azure-portal)
Azure dosyaları için NFS 4,1 özelliğini kaydetmek üzere Azure PowerShell ya da Azure CLı kullanın.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
# Connect your PowerShell session to your Azure account, if you have not already done so.
Connect-AzAccount

# Set the actively selected subscription, if you have not already done so.
$subscriptionId = "<yourSubscriptionIDHere>"
$context = Get-AzSubscription -SubscriptionId $subscriptionId
Set-AzContext $context

# Register the NFS 4.1 feature with Azure Files to enable the preview.
Register-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares 
    
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
# Connect your Azure CLI to your Azure account, if you have not already done so.
az login

# Provide the subscription ID for the subscription where you would like to 
# register the feature
subscriptionId="<yourSubscriptionIDHere>"

az feature register \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId

az provider register \
    --namespace Microsoft.Storage
```

---

Kayıt onayı bir saate kadar sürebilir. Kaydın tamamlandığını doğrulamak için aşağıdaki komutları kullanın:

# <a name="portal"></a>[Portal](#tab/azure-portal)
Azure dosyaları için NFS 4,1 özelliğinin kaydını denetlemek üzere Azure PowerShell veya Azure CLı kullanın. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
Get-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az feature show \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId
```

---

## <a name="create-a-filestorage-storage-account"></a>FileStorage depolama hesabı oluşturma
Şu anda, NFS 4,1 paylaşımları yalnızca Premium dosya paylaşımları olarak kullanılabilir. Bir Premium dosya paylaşımının NFS 4,1 protokol desteğiyle dağıtılması için önce bir FileStorage depolama hesabı oluşturmanız gerekir. Depolama hesabı, Azure 'da birden çok Azure dosya paylaşımını dağıtmak için kullanılabilen, paylaşılan bir depolama havuzunu temsil eden üst düzey bir nesnedir.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Bir dosya depolama depolama hesabı oluşturmak için Azure portal gidin.

1. Azure portal sol taraftaki menüden **depolama hesapları** ' nı seçin.

    ![Ana sayfa Azure portal depolama hesabı seçin.](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. Açılan **Depolama Hesapları** penceresinde **Ekle**'yi seçin.
1. Depolama hesabının oluşturulacağı aboneliği seçin.
1. Depolama hesabının oluşturulacağı kaynak grubunu seçin
1. Ardından, depolama hesabınız için bir ad girin. Seçtiğiniz ad Azure genelinde benzersiz olmalıdır. Ad ayrıca 3 - 24 karakter uzunluğunda olmalıdır ve yalnızca rakam ve küçük harf içerebilir.
1. Depolama hesabınız için bir konum seçin veya varsayılan konumu kullanın.
1. **Performans** için **Premium** seçin.

    **Dosya paylaşımları** için **Premium** ' u, **Hesap türü** açılan listesinde kullanılabilir bir seçenek olacak şekilde seçmelisiniz.

1. **Premium hesap türü** Için **dosya paylaşımları**' nı seçin.

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-premium.png" alt-text="Premium performans ekranının ekran görüntüsü.":::

1. **Çoğaltmayı** **yerel olarak yedekli depolama (LRS)** varsayılan değerine ayarlı bırakın.
1. Depolama hesabı ayarlarınızı gözden geçirmek ve hesabı oluşturmak için **Gözden Geçir + Oluştur**’u seçin.
1. **Oluştur**’u seçin.

Depolama hesabı kaynağınız oluşturulduktan sonra şuraya gidin.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Bir dosya depolama depolama hesabı oluşturmak için, bir PowerShell istemi açın ve aşağıdaki komutları yürütün ve `<resource-group>` `<storage-account>` ortamınız için uygun değerleri ve ile değiştirin.

```powershell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$location = "westus2"

$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $location `
    -Kind FileStorage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Bir dosya depolama depolama hesabı oluşturmak için terminalinizi açın ve aşağıdaki komutları yürütün ve `<resource-group>` `<storage-account>` ortamınız için uygun değerleri ve ile değiştirin.

```azurecli-interactive
resourceGroup="<resource-group>"
storageAccount="<storage-account>"
location="westus2"

az storage account create \
    --resource-group $resourceGroup \
    --name $storageAccount \
    --location $location \
    --sku Premium_LRS \
    --kind FileStorage
```
---

## <a name="create-an-nfs-share"></a>NFS paylaşımı oluşturma

# <a name="portal"></a>[Portal](#tab/azure-portal)

Bir dosya depolama hesabı oluşturup ağı yapılandırdığınıza göre, bir NFS dosya paylaşma oluşturabilirsiniz. İşlem SMB paylaşımının oluşturulmasına benzer, ancak paylaşımın oluşturulması sırasında **SMB** yerine **NFS** 'yi seçersiniz.

1. Depolama hesabınıza gidin ve **dosya paylaşımları**' nı seçin.
1. Yeni bir dosya paylaşma oluşturmak için **+ dosya paylaşma** ' yı seçin.
1. Dosya paylaşımınızı adlandırın, sağlanan kapasiteyi seçin.
1. **Protokol** için **NFS (Önizleme)** seçeneğini belirleyin.
1. **Kök Squash** için bir seçim yapın.

    - Kök sıkıştırma (varsayılan)-uzak Süper Kullanıcı (root) erişimi, UID (65534) ve GID (65534) ile eşleştirilir.
    - Kök sıkıştırarak-uzak Süper Kullanıcı (root), kök olarak erişim almaz.
    - Tüm sıkıştırarak-tüm Kullanıcı erişimi UID (65534) ve GID (65534) ile eşleştirilir.
    
1. **Oluştur**’u seçin.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/files-nfs-create-share.png" alt-text="Dosya paylaşma oluşturma dikey penceresinin ekran görüntüsü.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. .NET Framework 'ün yüklü olduğundan emin olun. Bkz. [indirme .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
 
1. `5.1`Aşağıdaki komutu kullanarak yüklü PowerShell sürümünün veya daha yüksek olduğunu doğrulayın.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   PowerShell sürümünüzü yükseltmek için bkz. [var olan Windows PowerShell 'ı yükseltme](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)
    
1. PowershellGet modülünün en son sürümünü yükler.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. ' I kapatın ve ardından PowerShell konsolunu yeniden açın.

1. **Az. Storage** Preview Module sürüm **2.5.2 Parallel sections-Preview**' i yükler.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   PowerShell modüllerinin nasıl yükleneceği hakkında daha fazla bilgi için bkz [. Azure PowerShell modülünü Install](/powershell/azure/install-az-ps)
   
1. Azure PowerShell modülüyle Premium bir dosya paylaşımı oluşturmak için [New-Azrmstoragesshare](/powershell/module/az.storage/new-azrmstorageshare) cmdlet 'ini kullanın.

    > [!NOTE]
    > Premium dosya paylaşımları, sağlanan bir model kullanılarak faturalandırılır. Paylaşımın sağlanan boyutu aşağıdaki şekilde belirtilmiştir `QuotaGiB` . Daha fazla bilgi için bkz. [sağlanan modeli](understanding-billing.md#provisioned-model) ve [Azure dosyaları fiyatlandırma sayfasını](https://azure.microsoft.com/pricing/details/storage/files/)anlama.

    ```powershell
    New-AzRmStorageShare `
        -StorageAccount $storageAccount `
        -Name myshare `
        -EnabledProtocol NFS `
        -RootSquash RootSquash `
        -QuotaGiB 1024
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLı ile Premium dosya paylaşma oluşturmak için [az Storage Share Create](/cli/azure/storage/share-rm) komutunu kullanın.

> [!NOTE]
> Premium dosya paylaşımları, sağlanan bir model kullanılarak faturalandırılır. Paylaşımın sağlanan boyutu aşağıdaki şekilde belirtilmiştir `quota` . Daha fazla bilgi için bkz. [sağlanan modeli](understanding-billing.md#provisioned-model) ve [Azure dosyaları fiyatlandırma sayfasını](https://azure.microsoft.com/pricing/details/storage/files/)anlama.

```azurecli-interactive
az storage share-rm create \
    --resource-group $resourceGroup \
    --storage-account $storageAccount \
    --name "myshare" \
    --enabled-protocol NFS \
    --root-squash RootSquash \
    --quota 1024
```
---

## <a name="next-steps"></a>Sonraki adımlar
Artık bir NFS paylaşma oluşturduğunuza göre, bunu kullanmak için Linux istemcinizle bağlamanız gerekir. Ayrıntılar için bkz. [NFS paylaşma bağlama](storage-files-how-to-mount-nfs-shares.md).

Herhangi bir sorunla karşılaşırsanız bkz. [Azure NFS dosya paylaşımları sorunlarını giderme](storage-troubleshooting-files-nfs.md).