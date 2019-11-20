---
title: Bulutu Azure PowerShell kullanarak Azure bağımsız depolama yönetme | Microsoft Docs
description: Depolama Çin Bulutu, kamu Bulutu ve Azure PowerShell kullanarak Almanya Bulutu yönetme
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/24/2017
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 69707eec0ea1f2260ee50a48ce1dcb82dc9ddd8f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65145877"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>PowerShell kullanarak Azure bağımsız bulutlarda depolama yönetme

Çoğu kişi, Azure genel bulut, genel Azure dağıtım için kullanın. De vardır özerkliği, nedeniyle için Microsoft Azure'nın bağımsız bazı dağıtımları ve benzeri. Bu bağımsız dağıtımlar "ortam" adlandırılır Aşağıdaki listede, bağımsız bulutlarda şu anda kullanılabilir ayrıntıları.

* [Azure kamu Bulutu](https://azure.microsoft.com/features/gov/)
* [Çin'de 21Vianet tarafından işletilen Azure Çin Bulutu](http://www.windowsazure.cn/)
* [Azure Almanya Bulutu](../../germany/germany-welcome.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="using-an-independent-cloud"></a>Bağımsız bir bulut kullanma 

Azure depolama bağımsız bulutlarda birini kullanmak için Azure genel yerine, bir buluta bağlanın. Bağımsız Bulutlar yerine Azure genel birini kullanmak için:

* Belirttiğiniz *ortam* bağlantı kurmak için.
* Belirlemek ve kullanılabildiği bölgeler kullanın.
* Azure genel farklı doğru uç noktaya soneki kullanırsınız.

Örneği, Azure PowerShell modülü Az 0.7 veya üzerini gerektirir. Bir PowerShell penceresinde çalıştırın `Get-Module -ListAvailable Az` sürümü bulmak için. Hiçbir şey listelenen veya yükseltme için ihtiyacınız [Azure PowerShell modülü yükleme](/powershell/azure/install-Az-ps). 

## <a name="log-in-to-azure"></a>Azure'da oturum açma

Çalıştırma [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment) kullanılabilir Azure ortamları görmek için cmdlet:
   
```powershell
Get-AzEnvironment
```

Hesabınıza bağlamak ve ortamı kurun istediğiniz buluta erişimi olan oturum açın. Bu örnek, Azure kamu Bulutu kullanan bir hesapta oturum gösterilmektedir.   

```powershell
Connect-AzAccount –Environment AzureUSGovernment
```

Çin bulut erişmek için ortamı kullanmak **AzureChinaCloud**. Alman bulut erişmek için **AzureGermanCloud**.

Bu noktada, bir depolama hesabına veya başka bir kaynak oluşturmak için konumlarının listesini gerekirse kullanılarak seçilen bulut için kullanılabilir konumlar sorgulayabilirsiniz [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

```powershell
Get-AzLocation | select Location, DisplayName
```

Aşağıdaki tabloda Almanya bulutu için döndürülen konumlarını gösterir.

|Location | displayName |
|----|----|
| germanycentral | Almanya Orta|
| germanynortheast | Almanya Kuzeydoğu | 


## <a name="endpoint-suffix"></a>Uç nokta son eki

Bu ortamların her birinde için uç nokta soneki Azure genel uç noktasından farklıdır. Örneğin, ortak Azure blob uç noktası soneki olan **blob.core.windows.net**. Kamu bulutu için blob uç noktası sonekidir **blob.core.usgovcloudapi.net**. 

### <a name="get-endpoint-using-get-azenvironment"></a>Get-AzEnvironment kullanarak uç noktasını alın 

Uç nokta son eki kullanarak almak [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment). Uç nokta *StorageEndpointSuffix* ortam özelliği. Aşağıdaki kod parçacıkları, bunun nasıl yapılacağını gösterir. Bu komutların tümü bir şey iade "core.cloudapp.net" veya "core.cloudapi.de", vb. gibi. Bu, bu hizmete erişmek için depolama hizmeti ekleyin. Örneğin, "queue.core.cloudapi.de" Almanca bulutta kuyruk hizmeti erişim sağlar.

Bu kod parçacığı tüm ortamları ve her biri için uç nokta sonekini alır.

```powershell
Get-AzEnvironment | select Name, StorageEndpointSuffix 
```

Bu komut, aşağıdaki sonuçları döndürür.

| Name| StorageEndpointSuffix|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgovcloudapi.net |

Belirtilen ortam için özelliklerin tümünü almak için arama **Get-AzEnvironment** ve bulut adı belirtin. Bu kod parçacığı özelliklerin bir listesini döndürür; Aranacak **StorageEndpointSuffix** listesinde. Aşağıdaki örnek, Almanya bulutu için aynıdır.

```powershell
Get-AzEnvironment -Name AzureGermanCloud 
```

Sonuçlar aşağıdakine benzer:

|Özellik Adı|Değer|
|----|----|
| Name | AzureGermanCloud |
| EnableAdfsAuthentication | False |
| ActiveDirectoryServiceEndpointResourceI | http://management.core.cloudapi.de/ |
| GalleryURL | https://gallery.cloudapi.de/ |
| ManagementPortalUrl | https://portal.microsoftazure.de/ | 
| ServiceManagementUrl | https://manage.core.cloudapi.de/ |
| PublishSettingsFileUrl| https://manage.microsoftazure.de/publishsettings/index |
| ResourceManagerUrl | http://management.microsoftazure.de/ |
| SqlDatabaseDnsSuffix | .database.cloudapi.de |
| **StorageEndpointSuffix** | core.cloudapi.de |
| ... | ... | 

Yalnızca depolama uç noktası son eki özelliği almak için belirli bulut almak ve bu bir özellik için isteyin.

```powershell
$environment = Get-AzEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix 
```

Bu, aşağıdaki bilgileri döndürür.

```
Storage Endpoint Suffix = core.cloudapi.de
```

### <a name="get-endpoint-from-a-storage-account"></a>Bir depolama hesabından uç noktası alma

Ayrıca, uç noktaları almak için bir depolama hesabı özelliklerini inceleyebilirsiniz. Bu PowerShell komut dosyanızda bir depolama hesabı kullanıyorsanız yararlı olabilir; yalnızca gereksinim duyduğunuz uç nokta da alabilirsiniz. 

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

Kamu bulutunda bir depolama hesabı için bu aşağıdaki döndürür: 

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>Ortam ayarladıktan sonra

Burada, depolama hesaplarınızı yönetin ve veri düzlemine makalesinde açıklandığı şekilde erişmek için kullanılan aynı PowerShell kullanabileceğiniz ileride gelen [Azure PowerShell kullanarak Azure depolama ile](storage-powershell-guide-full.md).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Yeni bir kaynak grubu ve bu alıştırma için depolama hesabı oluşturduysanız, kaynak grubunu kaldırarak tüm varlıkları kaldırabilirsiniz. Bu, ayrıca grubun içerdiği tüm kaynakları da siler. Bu durumda, oluşturduğunuz depolama hesabına ve kaynak grubunu kaldırır.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

* [PowerShell oturumları arasında kalıcı kullanıcı oturumları](/powershell/azure/context-persistence)
* [Azure kamu depolama](../../azure-government/documentation-government-services-storage.md)
* [Microsoft Azure kamu Geliştirici Kılavuzu](../../azure-government/documentation-government-developer-guide.md)
* [Azure Çin uygulamaları için Geliştirici Notları](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Azure Almanya belgeleri](../../germany/germany-welcome.md)
