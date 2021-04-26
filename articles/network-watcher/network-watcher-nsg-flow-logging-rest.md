---
title: NSG akış günlüklerini yönetme-Azure REST API
titleSuffix: Azure Network Watcher
description: Bu sayfada, Azure ağ Izleyicisi 'nde ağ güvenlik grubu akış günlüklerinin REST API ile nasıl yönetileceği açıklanmaktadır
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: b45d066d0996aaba2a25500f8134085f5e9b6ffb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535193"
---
# <a name="configuring-network-security-group-flow-logs-using-rest-api"></a>REST API kullanarak ağ güvenlik grubu akış günlüklerini yapılandırma

> [!div class="op_single_selector"]
> - [Azure portalı](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Ağ güvenlik grubu akış günlükleri, bir ağ güvenlik grubu üzerinden giriş ve çıkış IP trafiği hakkındaki bilgileri görüntülemenize olanak tanıyan bir ağ Izleyicisi özelliğidir. Bu akış günlükleri, JSON biçiminde yazılır ve bir kural temelinde giden ve gelen akışları gösterir, akışın akışı (kaynak/hedef IP 'si, kaynak/hedef bağlantı noktası, protokol) ve trafiğe izin verildiyse veya reddedildiyse, 5 demet bilgi için geçerlidir.

## <a name="before-you-begin"></a>Başlamadan önce

ARMclient, PowerShell kullanarak REST API çağırmak için kullanılır. ARMClient, [Chocolatey üzerinde](https://chocolatey.org/packages/ARMClient)Chocolatey adresinde bulunur. NSG akış günlüklerinin ayrıntılı belirtimleri [burada](https://docs.microsoft.com/rest/api/network-watcher/flowlogs) bulunabilir REST API 

Bu senaryo, ağ Izleyicisi oluşturmak için [ağ Izleyicisi oluşturma](network-watcher-create.md) bölümündeki adımları zaten izlediğinizi varsayar.

> [!Important]
> Ağ Izleyicisi için REST API, istek URI 'sindeki kaynak grubu adını çağıran, üzerinde tanılama eylemleri gerçekleştirdiğiniz kaynakları değil, Ağ İzleyicisini içeren kaynak grubudur.

## <a name="scenario"></a>Senaryo

Bu makalede ele alınan senaryo, REST API kullanarak akış günlüklerini etkinleştirme, devre dışı bırakma ve sorgulama işlemlerinin nasıl yapılacağını gösterir. Ağ güvenlik grubu akış günlükleri hakkında daha fazla bilgi edinmek için [ağ güvenlik grubu akış günlüğü 'Ne genel bakış](network-watcher-nsg-flow-logging-overview.md)' ı ziyaret edin.

Bu senaryoda şunları yapmanız gerekir:

* Akış günlüklerini etkinleştir (sürüm 2)
* Akış günlüklerini devre dışı bırak
* Sorgu akışı günlüklerinin durumu

## <a name="log-in-with-armclient"></a>ARMClient ile oturum açma

Azure kimlik bilgilerinizle armclient 'da oturum açın.

```powershell
armclient login
```

## <a name="register-insights-provider"></a>Insights sağlayıcısını kaydetme

Akış günlüğünün başarıyla çalışması için, **Microsoft. Insights** sağlayıcısı kayıtlı olmalıdır. **Microsoft. Insights** sağlayıcısının kayıtlı olduğundan emin değilseniz, aşağıdaki betiği çalıştırın.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
armclient post "https://management.azure.com//subscriptions/${subscriptionId}/providers/Microsoft.Insights/register?api-version=2016-09-01"
```

## <a name="enable-network-security-group-flow-logs"></a>Ağ güvenlik grubu akış günlüklerini etkinleştir

Akış günlükleri sürüm 2 ' i etkinleştirme komutu aşağıdaki örnekte gösterilmiştir. Sürüm 1 ' in ' version ' alanını ' 1 ' ile değiştirin:

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'true',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

Yukarıdaki örnekten döndürülen yanıt aşağıdaki gibidir:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
    "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="disable-network-security-group-flow-logs"></a>Ağ güvenlik grubu akış günlüklerini devre dışı bırak

Akış günlüklerini devre dışı bırakmak için aşağıdaki örneği kullanın. Çağrı, etkin özellik için **false** dışında, akış günlüklerini etkinleştirme ile aynıdır.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'false',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

Yukarıdaki örnekten döndürülen yanıt aşağıdaki gibidir:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": false,
    "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="query-flow-logs"></a>Sorgu akış günlükleri

Aşağıdaki REST çağrısı, akış günlüklerinin durumunu bir ağ güvenlik grubunda sorgular.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/queryFlowLogStatus?api-version=2016-12-01" $requestBody
```

Aşağıda döndürülen yanıtın bir örneği verilmiştir:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
   "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="download-a-flow-log"></a>Akış günlüğü indir

Akış günlüğünün depolama konumu, oluşturma sırasında tanımlanmıştır. Bir depolama hesabına kaydedilmiş bu akış günlüklerine erişmek için uygun bir araç, buradan indirilebilen Microsoft Azure Depolama Gezgini.  https://storageexplorer.com/

Bir depolama hesabı belirtilmişse, paket yakalama dosyaları aşağıdaki konumdaki bir depolama hesabına kaydedilir:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

## <a name="next-steps"></a>Sonraki adımlar

[NSG akış günlüklerinizi PowerBI Ile görselleştirmeyi](network-watcher-visualize-nsg-flow-logs-power-bi.md) öğrenin

[Açık kaynaklı araçlarla NSG akış günlüklerinizi görselleştirmeyi](network-watcher-visualize-nsg-flow-logs-open-source-tools.md) öğrenin
