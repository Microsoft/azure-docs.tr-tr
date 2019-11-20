---
title: NSG akış günlüklerini okuma | Microsoft Docs
description: Bu makalede NSG akış günlüklerinin nasıl ayrıştırılacak gösterilmektedir
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: kumud
ms.openlocfilehash: edc4cc32cd358bd37fdab46e323c59ec207b2d5a
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293472"
---
# <a name="read-nsg-flow-logs"></a>NSG akış günlüklerini okuma

PowerShell ile NSG akış günlüğü girdilerini okumayı öğrenin.

NSG akış günlükleri, [Blok Bloblarında](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)bir depolama hesabında depolanır. Blok Blobları daha küçük bloklarından oluşur. Her günlük, her saat oluşturulan ayrı bir blok bloğudur. Her saat yeni günlükler oluşturulur. Günlükler en son verilerle birkaç dakikada bir yeni girişlerle güncelleştirilir. Bu makalede, akış günlüklerinin bölümlerini nasıl okuyacağınızı öğreneceksiniz.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>Senaryo

Aşağıdaki senaryoda, bir depolama hesabında depolanan örnek bir akış günlüğüne sahipsiniz. NSG akış günlüklerinde en son olayları seçmeli olarak okumayı öğrenirsiniz. Bu makalede PowerShell kullanıyorsunuz, ancak makalede açıklanan kavramlar programlama diliyle sınırlı değildir ve Azure Storage API 'Leri tarafından desteklenen tüm diller için geçerlidir.

## <a name="setup"></a>Kurulum

Başlamadan önce, hesabınızdaki bir veya daha fazla ağ güvenlik grubunda ağ güvenlik grubu akış günlüğü etkin olmalıdır. Ağ güvenlik akışı günlüklerinin etkinleştirilmesi hakkındaki yönergeler için aşağıdaki makaleye bakın: [ağ güvenlik grupları için akış günlüğüne giriş](network-watcher-nsg-flow-logging-overview.md).

## <a name="retrieve-the-block-list"></a>Engelleme listesini alma

Aşağıdaki PowerShell, NSG akış günlüğü blobunu sorgulamak için gereken değişkenleri ayarlar ve [Cloudblockblob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblockblob) Blok Blobu içindeki blokları listeler. Komut dosyasını ortamınız için geçerli değerler içerecek şekilde güncelleştirin.

```powershell
function Get-NSGFlowLogCloudBlockBlob {
    [CmdletBinding()]
    param (
        [string] [Parameter(Mandatory=$true)] $subscriptionId,
        [string] [Parameter(Mandatory=$true)] $NSGResourceGroupName,
        [string] [Parameter(Mandatory=$true)] $NSGName,
        [string] [Parameter(Mandatory=$true)] $storageAccountName,
        [string] [Parameter(Mandatory=$true)] $storageAccountResourceGroup,
        [string] [Parameter(Mandatory=$true)] $macAddress,
        [datetime] [Parameter(Mandatory=$true)] $logTime
    )

    process {
        # Retrieve the primary storage account key to access the NSG logs
        $StorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $storageAccountResourceGroup -Name $storageAccountName).Value[0]

        # Setup a new storage context to be used to query the logs
        $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

        # Container name used by NSG flow logs
        $ContainerName = "insights-logs-networksecuritygroupflowevent"

        # Name of the blob that contains the NSG flow log
        $BlobName = "resourceId=/SUBSCRIPTIONS/${subscriptionId}/RESOURCEGROUPS/${NSGResourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/${NSGName}/y=$($logTime.Year)/m=$(($logTime).ToString("MM"))/d=$(($logTime).ToString("dd"))/h=$(($logTime).ToString("HH"))/m=00/macAddress=$($macAddress)/PT1H.json"

        # Gets the storage blog
        $Blob = Get-AzStorageBlob -Context $ctx -Container $ContainerName -Blob $BlobName

        # Gets the block blog of type 'Microsoft.Azure.Storage.Blob.CloudBlob' from the storage blob
        $CloudBlockBlob = [Microsoft.Azure.Storage.Blob.CloudBlockBlob] $Blob.ICloudBlob

        #Return the Cloud Block Blob
        $CloudBlockBlob
    }
}

function Get-NSGFlowLogBlockList  {
    [CmdletBinding()]
    param (
        [Microsoft.Azure.Storage.Blob.CloudBlockBlob] [Parameter(Mandatory=$true)] $CloudBlockBlob
    )
    process {
        # Stores the block list in a variable from the block blob.
        $blockList = $CloudBlockBlob.DownloadBlockList()

        # Return the Block List
        $blockList
    }
}


$CloudBlockBlob = Get-NSGFlowLogCloudBlockBlob -subscriptionId "yourSubscriptionId" -NSGResourceGroupName "FLOWLOGSVALIDATIONWESTCENTRALUS" -NSGName "V2VALIDATIONVM-NSG" -storageAccountName "yourStorageAccountName" -storageAccountResourceGroup "ml-rg" -macAddress "000D3AF87856" -logTime "11/11/2018 03:00" 

$blockList = Get-NSGFlowLogBlockList -CloudBlockBlob $CloudBlockBlob
```

@No__t-0 değişkeni blob içindeki blokların bir listesini döndürür. Her Blok Blobu en az iki blok içerir.  İlk blok uzunluğu @no__t 0 bayttır, bu blok JSON günlüğünün sol köşeli ayracını içerir. Diğer blok, kapanış ayraçları ve uzunluğu @no__t 0 bayttır.  Aşağıdaki örnek günlükte, her biri tek bir giriş olmak üzere, burada yedi giriş olduğunu görebilirsiniz. Günlükteki tüm yeni girişler son bloğundan önce son sağa eklenir.

```
Name                                         Length Committed
----                                         ------ ---------
ZDk5MTk5N2FkNGE0MmY5MTk5ZWViYjA0YmZhODRhYzY=     12      True
NzQxNDA5MTRhNDUzMGI2M2Y1MDMyOWZlN2QwNDZiYzQ=   2685      True
ODdjM2UyMWY3NzFhZTU3MmVlMmU5MDNlOWEwNWE3YWY=   2586      True
ZDU2MjA3OGQ2ZDU3MjczMWQ4MTRmYWNhYjAzOGJkMTg=   2688      True
ZmM3ZWJjMGQ0ZDA1ODJlOWMyODhlOWE3MDI1MGJhMTc=   2775      True
ZGVkYTc4MzQzNjEyMzlmZWE5MmRiNjc1OWE5OTc0OTQ=   2676      True
ZmY2MjUzYTIwYWIyOGU1OTA2ZDY1OWYzNmY2NmU4ZTY=   2777      True
Mzk1YzQwM2U0ZWY1ZDRhOWFlMTNhYjQ3OGVhYmUzNjk=   2675      True
ZjAyZTliYWE3OTI1YWZmYjFmMWI0MjJhNzMxZTI4MDM=      2      True
```

## <a name="read-the-block-blob"></a>Blok Blobu okuyun

Sonra, verileri almak için `$blocklist` değişkenini okumanız gerekir. Bu örnekte, blok listesinden yineliyoruz, her bloktaki baytları okur ve bunları bir dizi içinde hikaye. Verileri almak için [Downloadrangetobytearray](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadrangetobytearray) metodunu kullanın.

```powershell
function Get-NSGFlowLogReadBlock  {
    [CmdletBinding()]
    param (
        [System.Array] [Parameter(Mandatory=$true)] $blockList,
        [Microsoft.Azure.Storage.Blob.CloudBlockBlob] [Parameter(Mandatory=$true)] $CloudBlockBlob

    )
    # Set the size of the byte array to the largest block
    $maxvalue = ($blocklist | measure Length -Maximum).Maximum

    # Create an array to store values in
    $valuearray = @()

    # Define the starting index to track the current block being read
    $index = 0

    # Loop through each block in the block list
    for($i=0; $i -lt $blocklist.count; $i++)
    {
        # Create a byte array object to story the bytes from the block
        $downloadArray = New-Object -TypeName byte[] -ArgumentList $maxvalue

        # Download the data into the ByteArray, starting with the current index, for the number of bytes in the current block. Index is increased by 3 when reading to remove preceding comma.
        $CloudBlockBlob.DownloadRangeToByteArray($downloadArray,0,$index, $($blockList[$i].Length-1)) | Out-Null

        # Increment the index by adding the current block length to the previous index
        $index = $index + $blockList[$i].Length

        # Retrieve the string from the byte array

        $value = [System.Text.Encoding]::ASCII.GetString($downloadArray)

        # Add the log entry to the value array
        $valuearray += $value
    }
    #Return the Array
    $valuearray
}
$valuearray = Get-NSGFlowLogReadBlock -blockList $blockList -CloudBlockBlob $CloudBlockBlob
```

Artık `$valuearray` dizisi her bloğun dize değerini içerir. Girişi doğrulamak için `$valuearray[$valuearray.Length-2]` ' ı çalıştırarak dizideki son değeri alın. Son değeri, kapanış ayracı olduğu için istemezsiniz.

Bu değerin sonuçları aşağıdaki örnekte gösterilmiştir:

```json
        {
             "time": "2017-06-16T20:59:43.7340000Z",
             "systemId": "5f4d02d3-a7d0-4ed4-9ce8-c0ae9377951c",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/CONTOSORG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/CONTOSONSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_AllowInternetOutBound","flows":[{"mac":"000D3A18077E","flowTuples":["1497646722,10.0.0.4,168.62.32.14,44904,443,T,O,A","1497646722,10.0.0.4,52.240.48.24,45218,443,T,O,A","1497646725,10.
0.0.4,168.62.32.14,44910,443,T,O,A","1497646725,10.0.0.4,52.240.48.24,45224,443,T,O,A","1497646728,10.0.0.4,168.62.32.14,44916,443,T,O,A","1497646728,10.0.0.4,52.240.48.24,45230,443,T,O,A","1497646732,10.0.0.4,168.62.32.14,44922,443,T,O,A","14976
46732,10.0.0.4,52.240.48.24,45236,443,T,O,A","1497646735,10.0.0.4,168.62.32.14,44928,443,T,O,A","1497646735,10.0.0.4,52.240.48.24,45242,443,T,O,A","1497646738,10.0.0.4,168.62.32.14,44934,443,T,O,A","1497646738,10.0.0.4,52.240.48.24,45248,443,T,O,
A","1497646742,10.0.0.4,168.62.32.14,44942,443,T,O,A","1497646742,10.0.0.4,52.240.48.24,45256,443,T,O,A","1497646745,10.0.0.4,168.62.32.14,44948,443,T,O,A","1497646745,10.0.0.4,52.240.48.24,45262,443,T,O,A","1497646749,10.0.0.4,168.62.32.14,44954
,443,T,O,A","1497646749,10.0.0.4,52.240.48.24,45268,443,T,O,A","1497646753,10.0.0.4,168.62.32.14,44960,443,T,O,A","1497646753,10.0.0.4,52.240.48.24,45274,443,T,O,A","1497646756,10.0.0.4,168.62.32.14,44966,443,T,O,A","1497646756,10.0.0.4,52.240.48
.24,45280,443,T,O,A","1497646759,10.0.0.4,168.62.32.14,44972,443,T,O,A","1497646759,10.0.0.4,52.240.48.24,45286,443,T,O,A","1497646763,10.0.0.4,168.62.32.14,44978,443,T,O,A","1497646763,10.0.0.4,52.240.48.24,45292,443,T,O,A","1497646766,10.0.0.4,
168.62.32.14,44984,443,T,O,A","1497646766,10.0.0.4,52.240.48.24,45298,443,T,O,A","1497646769,10.0.0.4,168.62.32.14,44990,443,T,O,A","1497646769,10.0.0.4,52.240.48.24,45304,443,T,O,A","1497646773,10.0.0.4,168.62.32.14,44996,443,T,O,A","1497646773,
10.0.0.4,52.240.48.24,45310,443,T,O,A","1497646776,10.0.0.4,168.62.32.14,45002,443,T,O,A","1497646776,10.0.0.4,52.240.48.24,45316,443,T,O,A","1497646779,10.0.0.4,168.62.32.14,45008,443,T,O,A","1497646779,10.0.0.4,52.240.48.24,45322,443,T,O,A"]}]}
,{"rule":"DefaultRule_DenyAllInBound","flows":[]},{"rule":"UserRule_ssh-rule","flows":[]},{"rule":"UserRule_web-rule","flows":[{"mac":"000D3A18077E","flowTuples":["1497646738,13.82.225.93,10.0.0.4,1180,80,T,I,A","1497646750,13.82.225.93,10.0.0.4,
1184,80,T,I,A","1497646768,13.82.225.93,10.0.0.4,1181,80,T,I,A","1497646780,13.82.225.93,10.0.0.4,1336,80,T,I,A"]}]}]}
        }
```

Bu senaryo, tüm günlüğü ayrıştırmanıza gerek kalmadan NSG akış günlüklerinde girişlerin nasıl okunacağını gösteren bir örnektir. Günlükteki yeni girişleri, blok KIMLIĞINI kullanarak yazıldığı gibi veya Blok Blobu içinde depolanan blokların uzunluğunu izleyerek okuyabilirsiniz. Bu, yalnızca yeni girdileri okumanızı sağlar.

## <a name="next-steps"></a>Sonraki adımlar

NSG akış günlüklerini görüntüleme yolları hakkında daha fazla bilgi edinmek için [elastik Stack kullanın](network-watcher-visualize-nsg-flow-logs-open-source-tools.md), [Grafana kullanın](network-watcher-nsg-grafana.md)ve [gri günlük kullanın](network-watcher-analyze-nsg-flow-logs-graylog.md) . Blob 'ları doğrudan tüketmeye yönelik açık kaynaklı bir Azure Işlevi yaklaşımı ve çeşitli Log Analytics tüketicilerine yayma, burada bulunabilir: [Azure Ağ İzleyicisi NSG akış günlükleri Bağlayıcısı](https://github.com/Microsoft/AzureNetworkWatcherNSGFlowLogsConnector).

Depolama Blobları hakkında daha fazla bilgi edinmek için ziyaret edin: [Azure Işlevleri BLOB depolama bağlamaları](../azure-functions/functions-bindings-storage-blob.md)
