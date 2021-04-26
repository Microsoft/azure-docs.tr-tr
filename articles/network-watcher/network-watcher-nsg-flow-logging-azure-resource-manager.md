---
title: Ağ Izleyicisi-Azure Resource Manager şablonu kullanarak NSG akış günlükleri oluşturma
description: NSG akış günlüklerini kolayca ayarlamak için bir Azure Resource Manager şablonu ve PowerShell kullanın.
services: network-watcher
documentationcenter: na
author: damendo
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.custom: fasttrack-edit
ms.openlocfilehash: 2cae673dd319e55670357ae468d21ff63e4f9c72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104669449"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>Azure Resource Manager şablonundan NSG akış günlüklerini yapılandırma

> [!div class="op_single_selector"]
> - [Azure portalı](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) , Azure 'un [altyapınızı kod olarak](/azure/devops/learn/what-is-infrastructure-as-code)yönetmenin yerel ve güçlü bir yoludur.

Bu makalede, Azure Resource Manager şablonu ve Azure PowerShell kullanarak el ile [NSG akış günlüklerinin](./network-watcher-nsg-flow-logging-overview.md) nasıl etkinleştirileceği gösterilmektedir. NSG akış günlüğü nesnesinin özelliklerine genel bir bakış sunarak ve ardından birkaç örnek şablon tarafından başlayacağız. Ardından, şablonu yerel bir PowerShell örneği kullanarak dağıtın.


## <a name="nsg-flow-logs-object"></a>NSG akış günlükleri nesnesi

NSG akış günlüğü nesnesi tüm parametreleri aşağıda gösterilmiştir.
Özelliklere yönelik kapsamlı bir genel bakış için [NSG akış günlükleri şablon başvurusunu](/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters)okuyabilirsiniz.

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
         "enabled": "boolean",
         "workspaceResourceId": "string",
          "trafficAnalyticsInterval": "integer"
        },
        "retentionPolicy": {
           "days": "integer",
           "enabled": "boolean"
         },
        "format": {
           "type": "string",
           "version": "integer"
         }
      }
    }
  }
```
Bir Microsoft. Network/networkWatchers/flowLogs kaynağı oluşturmak için, yukarıdaki JSON 'ı şablonunuzun kaynaklar bölümüne ekleyin.


## <a name="creating-your-template"></a>Şablonunuz oluşturma

Azure Resource Manager şablonlarını ilk kez kullanıyorsanız aşağıdaki bağlantıları kullanarak bunlarla ilgili daha fazla bilgi edinebilirsiniz.

* [Kaynakları Resource Manager şablonları ve Azure PowerShell ile dağıtma](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template-or-bicep-file)
* [Öğretici: ilk Azure Resource Manager şablonunuzu oluşturma ve dağıtma](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-powershell)


Aşağıda NSG akış günlüklerini ayarlamaya yönelik tüm şablonlara yönelik iki örnek verilmiştir.

**Örnek 1**: Yukarıdaki en küçük parametrelere sahip en basit sürümü geçildi. Aşağıdaki şablon bir hedef NSG 'de NSG akış günlüklerini etkinleştirirler ve bunları belirli bir depolama hesabında depolar.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {},
      "retentionPolicy": {},
      "format": {}
    }

  }
  ]
}
```

> [!NOTE]
> * Kaynağın adı "üst Resource_Child kaynağı" biçimindedir. Burada, üst kaynak bölgesel ağ Izleyicisi örneğidir (Biçim: NetworkWatcher_RegionName. Örnek: NetworkWatcher_centraluseuap)
> * Targetresourceıd, hedef NSG 'nin kaynak KIMLIĞIDIR
> * Storageıd, hedef depolama hesabının kaynak KIMLIĞIDIR

**Örnek 2**: Aşağıdaki şablonlar, NSG akış günlüklerini (sürüm 2) 5 güne yönelik bir bekletme ile etkinleştirir. 10 dakikalık bir işlem aralığı ile Trafik Analizi etkinleştiriliyor.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
        "enabled": true,
        "flowAnalyticsConfiguration": {
          "networkWatcherFlowAnalyticsConfiguration": {
            "enabled": true,
            "workspaceResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/defaultresourcegroup-wcus/providers/Microsoft.OperationalInsights/workspaces/1c4f42e5-3a02-4146-ac9b-3051d8501db0",
            "trafficAnalyticsInterval": 10
          }
        },
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
  ]
}
```

## <a name="deploying-your-azure-resource-manager-template"></a>Azure Resource Manager şablonunuzu dağıtma

Bu öğreticide, akış günlüğünü etkinleştirmek için mevcut bir kaynak grubunuz ve bir NSG olduğunu varsaymaktadır.
Yukarıdaki örnek şablonlardan herhangi birini olarak yerel olarak kaydedebilirsiniz `azuredeploy.json` . Özellik değerlerini aboneliğinizdeki geçerli kaynaklara işaret eden bir şekilde güncelleştirin.

Şablonu dağıtmak için PowerShell 'de aşağıdaki komutu çalıştırın.
```azurepowershell
$context = Get-AzSubscription -SubscriptionId <SubscriptionId>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Yukarıdaki komutlar, NSG 'yi içeren kaynak grubuna değil NetworkWatcherRG kaynak grubuna bir kaynak dağıtmakta


## <a name="verifying-your-deployment"></a>Dağıtımınız doğrulanıyor

Dağıtımınızın başarılı olup olmadığını denetlemek için birkaç yol vardır. PowerShell konsolunuzun "başarılı" olarak "ProvisioningState" gösterilmesi gerekir. Ayrıca, değişikliklerinizi onaylamak için [NSG akış günlükleri portalı sayfasını](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) ziyaret edebilirsiniz. Dağıtım ile ilgili sorunlar varsa, [Azure Resource Manager ile yaygın Azure dağıtım hatalarıyla Ilgili sorunları gidermeye](../azure-resource-manager/templates/common-deployment-errors.md)göz atın.

## <a name="deleting-your-resource"></a>Kaynağınız siliniyor
Azure, "tamamlanmış" dağıtım modundan kaynak silmeye izin vermez. Bir akış günlükleri kaynağını silmek için, silmek istediğiniz kaynağı dahil etmeden bir dağıtımı tamamlanmış modda belirtin. [Tüm dağıtım modu](../azure-resource-manager/templates/deployment-modes.md#complete-mode) hakkında daha fazla bilgi edinin

## <a name="next-steps"></a>Sonraki adımlar

Kullanarak NSG akış verilerinizi görselleştirmeyi öğrenin:
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Açık kaynak araçları](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure Trafik Analizi](./traffic-analytics.md)
