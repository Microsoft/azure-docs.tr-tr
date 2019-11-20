---
title: Azure ağ Izleyicisi ile ağ güvenlik grubu akış günlüklerini yönetme-PowerShell | Microsoft Docs
description: Bu sayfada, PowerShell ile Azure ağ Izleyicisi 'nde ağ güvenlik grubu akış günlüklerinin nasıl yönetileceği açıklanmaktadır
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 508fe6f7dc6c5fef99e5f62a2ce75b0c81b1577f
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676340"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Ağ güvenlik grubu akış günlüklerini PowerShell ile yapılandırma

> [!div class="op_single_selector"]
> - [Azure portalda](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Ağ güvenlik grubu akış günlükleri, bir ağ güvenlik grubu üzerinden giriş ve çıkış IP trafiği hakkındaki bilgileri görüntülemenize olanak tanıyan bir ağ Izleyicisi özelliğidir. Bu akış günlükleri, JSON biçiminde yazılır ve bir kural temelinde giden ve gelen akışları gösterir, akışın akışı (kaynak/hedef IP 'si, kaynak/hedef bağlantı noktası, protokol) ve trafiğe izin verildiyse veya reddedildiyse, 5 demet bilgi için geçerlidir.

## <a name="register-insights-provider"></a>Insights sağlayıcısını kaydetme

Akış günlüğünün başarıyla çalışması için, **Microsoft. Insights** sağlayıcısı kayıtlı olmalıdır. **Microsoft. Insights** sağlayıcısının kayıtlı olduğundan emin değilseniz, aşağıdaki betiği çalıştırın.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs-and-traffic-analytics"></a>Ağ güvenlik grubu akış günlüklerini ve Trafik Analizi etkinleştirme

Akış günlüklerini etkinleştirme komutu aşağıdaki örnekte gösterilmiştir:

```powershell
$NW = Get-AzNetworkWatcher -ResourceGroupName NetworkWatcherRg -Name NetworkWatcher_westcentralus
$nsg = Get-AzNetworkSecurityGroup -ResourceGroupName nsgRG -Name nsgName
$storageAccount = Get-AzStorageAccount -ResourceGroupName StorageRG -Name contosostorage123
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id

#Traffic Analytics Parameters
$workspaceResourceId = "/subscriptions/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb/resourcegroups/trafficanalyticsrg/providers/microsoft.operationalinsights/workspaces/taworkspace"
$workspaceGUID = "cccccccc-cccc-cccc-cccc-cccccccccccc"
$workspaceLocation = "westeurope"

#Configure Version 1 Flow Logs
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 1

#Configure Version 2 Flow Logs, and configure Traffic Analytics
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2

#Configure Version 2 FLow Logs with Traffic Analytics Configured
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -EnableTrafficAnalytics -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Query Flow Log Status
Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
```

Belirttiğiniz depolama hesabı, yalnızca Microsoft hizmetlerine veya belirli sanal ağlara ağ erişimini kısıtlayan ağ kuralları için yapılandırılmış olamaz. Depolama hesabı aynı veya farklı bir Azure aboneliğinde olabilir ve bu da akış günlüğünü etkinleştirdiğiniz NSG 'den farklıdır. Farklı abonelikler kullanıyorsanız, her ikisinin de aynı Azure Active Directory kiracısıyla ilişkilendirilmesi gerekir. Her abonelik için kullandığınız hesabın [gerekli izinlere](required-rbac-permissions.md)sahip olması gerekir.

## <a name="disable-traffic-analytics-and-network-security-group-flow-logs"></a>Trafik Analizi ve ağ güvenlik grubu akış günlüklerini devre dışı bırak

Trafik analizlerini ve akış günlüklerini devre dışı bırakmak için aşağıdaki örneği kullanın:

```powershell
#Disable Traffic Analaytics by removing -EnableTrafficAnalytics property
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true -FormatType Json -FormatVersion 2 -WorkspaceResourceId $workspaceResourceId -WorkspaceGUID $workspaceGUID -WorkspaceLocation $workspaceLocation

#Disable Flow Logging
Set-AzNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Akış günlüğü indir

Akış günlüğünün depolama konumu, oluşturma sırasında tanımlanmıştır. Bir depolama hesabına kaydedilmiş bu akış günlüklerine erişmek için uygun bir araç, buradan indirilebilen Microsoft Azure Depolama Gezgini. https://storageexplorer.com/

Bir depolama hesabı belirtilmişse, akış günlük dosyaları aşağıdaki konumdaki bir depolama hesabına kaydedilir:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
> [!IMPORTANT]
> Şu anda ağ Izleyicisi için ağ [güvenlik grubu (NSG) akış günlüklerinin](network-watcher-nsg-flow-logging-overview.md) , bekletme ilkesi ayarlarına göre blob depolamadan otomatik olarak silinmediği bir sorun vardır. Sıfır olmayan bir bekletme ilkeniz varsa, herhangi bir ücret ödemeden kaçınmak için saklama süresini aşan depolama bloblarını düzenli aralıklarla silmenizi öneririz. NSG akış günlüğü depolama blogunu silme hakkında daha fazla bilgi için bkz. [NSG akış günlüğü depolama Bloblarını silme](network-watcher-delete-nsg-flow-log-blobs.md).

Günlüğün yapısı hakkında daha fazla bilgi için bkz. [ağ güvenlik grubu akış günlüğüne genel bakış](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Sonraki Adımlar

[NSG akış günlüklerinizi PowerBI Ile görselleştirmeyi](network-watcher-visualize-nsg-flow-logs-power-bi.md) öğrenin

[Açık kaynaklı araçlarla NSG akış günlüklerinizi görselleştirmeyi](network-watcher-visualize-nsg-flow-logs-open-source-tools.md) öğrenin
