---
title: Azure bağımsız bulutlarındaki verileri yönetmek için PowerShell 'i kullanma
titleSuffix: Azure Storage
description: Azure PowerShell kullanarak Çin bulutu, kamu bulutu ve Almanya bulutunda depolamayı yönetme.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e924a5f6c765b5b964fe3b1492393b063d9d23b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92783581"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>PowerShell kullanarak Azure bağımsız bulutlarında depolamayı yönetme

Çoğu kişi küresel Azure dağıtımı için Azure genel bulutu kullanır. Ayrıca, bağımsız Microsoft Azure bağımsız dağıtımları da vardır. Bu bağımsız dağıtımlar "ortamlar" olarak adlandırılır. Aşağıdaki listede şu anda kullanılabilir olan bağımsız bulutlar ayrıntıları verilmiştir.

* [Azure Kamu Bulutu](https://azure.microsoft.com/features/gov/)
* [Çin 'de 21Vianet tarafından işletilen Azure Çin 21Vianet bulutu](http://www.windowsazure.cn/)
* [Azure Almanya bulutu](../../germany/germany-welcome.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="using-an-independent-cloud"></a>Bağımsız bir bulut kullanma

Azure depolama 'yı bağımsız bulutlardan birinde kullanmak için Azure genel yerine bu buluta bağlanırsınız. Azure genel yerine bağımsız bulutlardan birini kullanmak için:

* Bağlanılacak *ortamı* belirtirsiniz.
* Kullanılabilir bölgeleri belirlersiniz ve kullanabilirsiniz.
* Azure genel 'den farklı olan doğru uç nokta sonekini kullanırsınız.

Örneklerde Azure PowerShell modülü az 0,7 veya üzeri bir sürüm gerekir. Bir PowerShell penceresinde, `Get-Module -ListAvailable Az` sürümü bulmak için öğesini çalıştırın. Hiçbir şey listelenmemişse veya yükseltmeniz gerekiyorsa, bkz. [ınstall Azure PowerShell Module](/powershell/azure/install-Az-ps).

## <a name="log-in-to-azure"></a>Azure'da oturum açma

Kullanılabilir Azure ortamlarını görmek için [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment) cmdlet 'ini çalıştırın:

```powershell
Get-AzEnvironment
```

Bağlanmak istediğiniz buluta erişimi olan hesabınızda oturum açın ve ortamı ayarlayın. Bu örnekte, Azure Kamu Bulutu 'nı kullanan bir hesapta nasıl oturum yapılacağı gösterilmektedir.   

```powershell
Connect-AzAccount –Environment AzureUSGovernment
```

Çin bulutuna erişmek için **AzureChinaCloud** ortamını kullanın. Almanya bulutuna erişmek için **AzureGermanCloud** kullanın.

Bu noktada, bir depolama hesabı veya başka bir kaynak oluşturmak için konum listesine ihtiyacınız varsa, [Get-AzLocation](/powershell/module/az.resources/get-azlocation)kullanarak seçilen bulut için kullanılabilir konumları sorgulayabilirsiniz.

```powershell
Get-AzLocation | select Location, DisplayName
```

Aşağıdaki tabloda, Almanya bulutu için döndürülen konumlar gösterilmektedir.

|Konum | Görünen Ad |
|----|----|
| `germanycentral` | Orta Almanya|
| `germanynortheast` | Kuzeydoğu Almanya |


## <a name="endpoint-suffix"></a>Uç nokta soneki

Bu ortamların her biri için uç nokta soneki, Azure genel uç noktasından farklıdır. Örneğin, Azure genel için blob uç nokta soneki **BLOB.Core.Windows.net**. Kamu Bulutu için blob Endpoint suffix **BLOB.Core.usgovcloudapi.net**.

### <a name="get-endpoint-using-get-azenvironment"></a>Get-AzEnvironment kullanarak uç nokta al

[Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment)kullanarak uç nokta sonekini alın. Uç noktası, ortamının *Storageendpointsuffix* özelliğidir.

Aşağıdaki kod parçacıkları, uç nokta sonekinin nasıl alınacağını gösterir. Bu komutların tümü, "core.cloudapp.net" veya "core.cloudapi.de" gibi bir şey döndürür. Bu hizmete erişmek için son eki depolama hizmetine ekleyin. Örneğin, "queue.core.cloudapi.de", Almanya bulutundaki kuyruk hizmetine erişir.

Bu kod parçacığı, her biri için tüm ortamları ve uç nokta sonekini alır.

```powershell
Get-AzEnvironment | select Name, StorageEndpointSuffix 
```

Bu komut aşağıdaki sonuçları döndürür.

| Name| StorageEndpointSuffix|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgovcloudapi.net |

Belirtilen ortamın tüm özelliklerini almak için **Get-AzEnvironment** ' ı çağırın ve bulut adını belirtin. Bu kod parçacığı, özelliklerin bir listesini döndürür; listede **Storageendpointsuffix** öğesini arayın. Aşağıdaki örnek, Almanya bulutuna yöneliktir.

```powershell
Get-AzEnvironment -Name AzureGermanCloud
```

Sonuçlar aşağıdaki değerlere benzer:

|Özellik Adı|Değer|
|----|----|
| Ad | `AzureGermanCloud` |
| EnableAdfsAuthentication | `False` |
| Activedirectoryserviceendpointresourceı | `http://management.core.cloudapi.de/` |
| Gallerurl 'Si | `https://gallery.cloudapi.de/` |
| ManagementPortalUrl 'Si | `https://portal.microsoftazure.de/` |
| ServiceManagementUrl | `https://manage.core.cloudapi.de/` |
| PublishSettingsFileUrl| `https://manage.microsoftazure.de/publishsettings/index` |
| ResourceManagerUrl | `http://management.microsoftazure.de/` |
| SqlDatabaseDnsSuffix | `.database.cloudapi.de` |
| **StorageEndpointSuffix** | `core.cloudapi.de` |
| ... | ... |

Yalnızca depolama uç noktası soneki özelliğini almak için, belirli bulutu alın ve yalnızca bir özelliği isteyin.

```powershell
$environment = Get-AzEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix
```

Bu komut aşağıdaki bilgileri döndürür:

`Storage Endpoint Suffix = core.cloudapi.de`

### <a name="get-endpoint-from-a-storage-account"></a>Depolama hesabından uç nokta al

Ayrıca, uç noktaları almak için bir depolama hesabının özelliklerini inceleyebilirsiniz:

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

Kamu bulutundaki bir depolama hesabı için bu komut aşağıdaki çıktıyı döndürür:

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>Ortamı ayarladıktan sonra

Artık, depolama hesaplarınızı yönetmek ve blob, kuyruk, dosya ve tablo verilerine erişmek için PowerShell 'i kullanabilirsiniz. Daha fazla bilgi için bkz. [az. Storage](/powershell/module/az.storage).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu alıştırma için yeni bir kaynak grubu ve depolama hesabı oluşturduysanız, kaynak grubunu silerek her iki varlığı da kaldırabilirsiniz. Kaynak grubunu sildiğinizde o grupta bulunan tüm kaynaklar da silinir.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Sonraki adımlar

* [PowerShell oturumları arasında kullanıcı oturumlarını sürdürme](/powershell/azure/context-persistence)
* [Azure Kamu depolama](../../azure-government/compare-azure-government-global-azure.md)
* [Microsoft Azure Kamu Geliştirici Kılavuzu](../../azure-government/documentation-government-developer-guide.md)
* [Azure Çin 21Vianet uygulamaları için geliştirici notları](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Azure Almanya belgeleri](../../germany/germany-welcome.md)