---
title: Azure ağ Izleyicisi 'nde ağ güvenlik grubu akış günlükleri için depolama bloblarını silme | Microsoft Docs
description: Bu makalede, Azure ağ Izleyicisi 'nde bekletme ilkesi süresi dışında olan ağ güvenlik grubu akış günlüğü depolama bloblarının nasıl silineceği açıklanır.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2019
ms.author: damendo
ms.openlocfilehash: 6898bed0645146af9c0131307459e31bad661329
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036306"
---
# <a name="delete-network-security-group-flow-log-storage-blobs-in-network-watcher"></a>Ağ Izleyicisi 'nde ağ güvenlik grubu akış günlüğü depolama bloblarını silme

Şu anda ağ Izleyicisi için ağ [güvenlik grubu (NSG) akış günlüklerinin](network-watcher-nsg-flow-logging-overview.md) , bekletme ilkesi ayarlarına göre blob depolamadan otomatik olarak silinmediği bir sorun vardır. Artık bu makalede açıklandığı gibi, akış günlüklerini depolama hesabınızdan el ile silmek için bir PowerShell betiği çalıştırmanız gerekir.

## <a name="run-powershell-script-to-delete-nsg-flow-logs"></a>NSG akış günlüklerini silmek için PowerShell betiğini çalıştırma
 
Aşağıdaki betiği kopyalayın ve geçerli çalışma dizininiz gibi bir konuma kaydedin. 

```powershell
# This powershell script deletes all NSG flow log blobs that should not be retained anymore as per configured retention policy.
# While configuring NSG flow logs on Azure portal, the user configures the retention period of NSG flow log blobs in
# their storage account (in days).
# This script reads all blobs and deletes blobs that are not to be retained (outside retention window)
# if the retention days are zero; all blobs are retained forever and hence no blobs are deleted.
#
#

param (
        [string] [Parameter(Mandatory=$true)]  $SubscriptionId,
        [string] [Parameter(Mandatory=$true)]  $Location,
        [switch] [Parameter(Mandatory=$false)] $Confirm
    )

Login-AzAccount

$SubId = Get-AzSubscription| Where-Object {$_.Id.contains($SubscriptionId.ToLower())}

if ($SubId.Count -eq 0)
{
    Write-Error 'The SubscriptionId does not exist' -ErrorAction Stop
}

Set-AzContext -SubscriptionId $SubscriptionId

$NsgList = Get-AzNetworkSecurityGroup | Where-Object {$_.Location -eq $Location}
$NW = Get-AzNetworkWatcher | Where-Object {$_.Location -eq $Location}

$FlowLogsList = @()
foreach ($Nsg in $NsgList)
{
    # Query Flow Log Status which are enabled
    $NsgFlowLog = Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $Nsg.Id | Where-Object {$_.Enabled -eq "True"}
    if ($NsgFlowLog.Count -gt 0)
    {
        $FlowLogsList +=  $NsgFlowLog
        Write-Output ('Enabled NSG found: ' +  $NsgFlowLog.TargetResourceId)
    }
}

foreach ($Psflowlog in $FlowLogsList)
{
    $RetentionDays = $Psflowlog.RetentionPolicy.Days
    if ($RetentionDays -le 0)
    {
        continue
    }

    $Strings = $Psflowlog.StorageId -split '/'
    $RGName = $Strings[4]
    $StorageAccountName = $Strings[-1]

    $Key = (Get-AzStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName).Value[1]
    $StorageAccount = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $Key

    $ContainerName = 'insights-logs-networksecuritygroupflowevent'  
    $BLobsList = Get-AzStorageBlob -Container $ContainerName -Context $StorageAccount.Context

    $TargetBLobsList = $BLobsList | Where-Object {$_.Name.contains($Psflowlog.TargetResourceId.ToUpper())}

    $RetentionDate = Get-Date
    $RetentionDate = $RetentionDate.AddDays(-1*$RetentionDays)
    $RetentionDateInUTC = $RetentionDate.ToUniversalTime()

    foreach ($Blob in $TargetBLobsList)
    {
        $BlobLastModifietedDTinUTC = [datetime]$Blob.LastModified.UtcDateTime

        if ($BlobLastModifietedDTinUTC -ge  $RetentionDateInUTC)
        {
            Write-Output ($Blob.Name + '===>' + $BlobLastModifietedDTinUTC  + ' ===> RETAINED')
            continue
        }

        if ($Confirm)
        {
            Write-Output (Blob to be deleted: $Blob.Name)
            $Confirmation = Read-Host "Are you sure you want to remove this blob (Y/N)?"
        }

        if ((-not $Confirm) -or ($Confirmation -eq 'Y'))
        {
            Write-Output ($Blob.Name + '===>' + $BlobLastModifietedDTinUTC  + ' ===> DELETED')
            Remove-AzStorageBlob -Container $ContainerName -Context $StorageAccount.Context -Blob $Blob.Name
        }
        else
        {
            Write-Output ($Blob.Name + '===>' + $BlobLastModifietedDTinUTC  + ' ===> RETAINED')
        }
    }
}

Write-Output ('Retention policy for all NSGs evaluated and completed successfully')
```

1. Komut dosyasına gereken şekilde aşağıdaki parametreleri girin:
   - **Abonelik kimliği** [Zorunlu]: NSG akış günlüğü bloblarını silmek istediğiniz abonelik KIMLIĞI.
   - **Konum** [Zorunlu]: NSG akış günlüğü bloblarını silmek istediğiniz NSGs bölgesinin _konum dizesi_ . Bu bilgileri Azure portal veya [GitHub](https://github.com/Azure/azure-extensions-cli/blob/beb3d3fe984cfa9c7798cb11a274c5337968cbc5/regions.go#L23)üzerinde görüntüleyebilirsiniz.
   - **Onayla** [İsteğe bağlı]: Her depolama blobunun silinmesini el ile onaylamak istiyorsanız, Onayla bayrağını geçirin.

1. Aşağıdaki örnekte gösterildiği gibi kaydedilen betiği çalıştırın, burada betik dosyası **Delete-NsgFlowLogsBlobs. ps1**olarak kaydedilir:
   ```
   .\Delete-NsgFlowLogsBlobs.ps1 -SubscriptionId <subscriptionId> -Location  <location> -Confirm
   ```
    
## <a name="next-steps"></a>Sonraki adımlar
- Müşteriler, [Azure Scheduler](https://azure.microsoft.com/services/scheduler/) veya [Azure Otomasyonu](https://azure.microsoft.com/services/automation/) kullanarak betiği çalıştırmayı otomatik hale getirebilir
- NSG günlüğü hakkında daha fazla bilgi edinmek için bkz. [ağ güvenlik grupları (NSG 'ler) Için Azure izleyici günlükleri](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

