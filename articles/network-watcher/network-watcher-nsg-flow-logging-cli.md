---
title: NSG akış günlüklerini yönetme-Azure CLı
titleSuffix: Azure Network Watcher
description: Bu sayfada Azure CLı ile Azure ağ Izleyicisi 'nde ağ güvenlik grubu akış günlüklerinin nasıl yönetileceği açıklanmaktadır
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
ms.openlocfilehash: a25d14660e5006aca2913053b17852c752c786d0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535262"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Azure CLı ile ağ güvenlik grubu akış günlüklerini yapılandırma

> [!div class="op_single_selector"]
> - [Azure portalı](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

Ağ güvenlik grubu akış günlükleri, bir ağ güvenlik grubu üzerinden giriş ve çıkış IP trafiği hakkındaki bilgileri görüntülemenize olanak tanıyan bir ağ Izleyicisi özelliğidir. Bu akış günlükleri, JSON biçiminde yazılır ve bir kural temelinde giden ve gelen akışları gösterir, akışın akışı (kaynak/hedef IP 'si, kaynak/hedef bağlantı noktası, protokol) ve trafiğe izin verildiyse veya reddedildiyse, 5 demet bilgi için geçerlidir.

Bu makaledeki adımları gerçekleştirmek için [Mac, Linux ve Windows Için Azure komut satırı arabirimini (CLI) yüklemeniz](/cli/azure/install-azure-cli)gerekir. Tüm akış günlükleri komutlarının ayrıntılı belirtimine [buradan](https://docs.microsoft.com/cli/azure/network/watcher/flow-log?view=azure-cli-latest) ulaşabilirsiniz

## <a name="register-insights-provider"></a>Insights sağlayıcısını kaydetme

Akış günlüğünün başarıyla çalışması için, **Microsoft. Insights** sağlayıcısı kayıtlı olmalıdır. **Microsoft. Insights** sağlayıcısının kayıtlı olduğundan emin değilseniz, aşağıdaki betiği çalıştırın.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Ağ güvenlik grubu akış günlüklerini etkinleştir

Akış günlüklerini etkinleştirme komutu aşağıdaki örnekte gösterilmiştir:

```azurecli
az network watcher flow-log create --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName --location location
# Configure 
az network watcher flow-log create --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName --location location --format JSON --log-version 2
```

Belirttiğiniz depolama hesabı, yalnızca Microsoft hizmetlerine veya belirli sanal ağlara ağ erişimini kısıtlayan ağ kuralları için yapılandırılmış olamaz. Depolama hesabı aynı veya farklı bir Azure aboneliğinde olabilir ve bu da akış günlüğünü etkinleştirdiğiniz NSG 'den farklıdır. Farklı abonelikler kullanıyorsanız, her ikisinin de aynı Azure Active Directory kiracısıyla ilişkilendirilmesi gerekir. Her abonelik için kullandığınız hesabın [gerekli izinlere](required-rbac-permissions.md)sahip olması gerekir. 

Depolama hesabı, ağ güvenlik grubundan farklı bir kaynak grubunda veya abonelikte yer alıyorsa, depolama hesabının adı yerine tam KIMLIĞI belirtin. Örneğin, depolama hesabı, bir önceki komutta *storageAccountName* belirtmek yerine *RG-Storage* adlı bir kaynak grubunsa, */Subscriptions/{SubscriptionID}/resourceGroups/RG-Storage/Providers/Microsoft.Storage/storageAccounts/storageAccountName* belirtmeniz gerekir.

## <a name="disable-network-security-group-flow-logs"></a>Ağ güvenlik grubu akış günlüklerini devre dışı bırak

Akış günlüklerini devre dışı bırakmak için aşağıdaki örneği kullanın:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Akış günlüğü indir

Akış günlüğünün depolama konumu, oluşturma sırasında tanımlanmıştır. Bir depolama hesabına kaydedilmiş bu akış günlüklerine erişmek için uygun bir araç, buradan indirilebilen Microsoft Azure Depolama Gezgini.  https://storageexplorer.com/

Bir depolama hesabı belirtilmişse, akış günlük dosyaları aşağıdaki konumdaki bir depolama hesabına kaydedilir:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```


## <a name="next-steps"></a>Sonraki Adımlar

[NSG akış günlüklerinizi PowerBI Ile görselleştirmeyi](network-watcher-visualize-nsg-flow-logs-power-bi.md) öğrenin

[Açık kaynaklı araçlarla NSG akış günlüklerinizi görselleştirmeyi](network-watcher-visualize-nsg-flow-logs-open-source-tools.md) öğrenin
