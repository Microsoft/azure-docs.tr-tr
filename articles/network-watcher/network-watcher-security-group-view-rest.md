---
title: Ağ güvenliğini çözümleme-güvenlik grubu görünümü-Azure REST API
titleSuffix: Azure Network Watcher
description: Bu makalede, Azure REST API 'nin güvenlik grubu görünümü ile bir sanal makineler güvenliğini nasıl analiz edileceği açıklanır.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 2efd3e9c9ca97ea3d94b03bd5e440cd24d5da5da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94960630"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-rest-api"></a>REST API kullanarak, sanal makine güvenliğine güvenlik grubu görünümü ile çözümleyin

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

> [!NOTE]
> Güvenlik grubu görünümü API 'SI artık korunmaz ve yakında kullanım dışı bırakılacak. Lütfen aynı işlevselliği sağlayan [etkin güvenlik kuralları özelliğini](./network-watcher-security-group-view-overview.md) kullanın. 

Güvenlik grubu görünümü, bir sanal makineye uygulanan yapılandırılmış ve etkin ağ güvenlik kurallarını döndürür. Bu özellik, trafiğin doğru şekilde izin verilmesini veya reddedilmemesini sağlamak için bir sanal makinede yapılandırılan ağ güvenlik gruplarını ve kurallarını denetlemek ve tanılamak için yararlıdır. Bu makalede, REST API kullanarak, etkin ve uygulanan güvenlik kurallarını bir sanal makineye nasıl alacağınızı göstereceğiz


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Başlamadan önce

Bu senaryoda, bir sanal makine için güvenlik grubu görünümünü almak üzere ağ Izleyicisi REST API 'sini çağırabilirsiniz. ARMclient, PowerShell kullanarak REST API çağırmak için kullanılır. ARMClient, [Chocolatey üzerinde](https://chocolatey.org/packages/ARMClient) Chocolatey konumunda bulunur

Bu senaryo, ağ Izleyicisi oluşturmak için [ağ Izleyicisi oluşturma](network-watcher-create.md) bölümündeki adımları zaten izlediğinizi varsayar. Senaryo Ayrıca, geçerli bir sanal makineye sahip bir kaynak grubunun kullanılabilir olduğunu varsayar.

## <a name="scenario"></a>Senaryo

Bu makalede ele alınan senaryo, belirli bir sanal makine için geçerli ve uygulanan güvenlik kurallarını alır.

## <a name="log-in-with-armclient"></a>ARMClient ile oturum açma

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Bir sanal makineyi al

Bir sanal machineto döndürmek için aşağıdaki betiği çalıştırın. Aşağıdaki kod, değişkenlere ihtiyaç duyuyor:

- **SubscriptionID** -abonelik kimliği **Get-azsubscription** cmdlet 'i ile de alınabilir.
- **Resourcegroupname** -sanal makineler içeren bir kaynak grubunun adı.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

 `Microsoft.Compute/virtualMachines` Aşağıdaki örnekte görüldüğü gibi, gereken bilgiler, yanıt olarak tür ' ün altında yer alan kimliğidir:

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft
.Network/networkInterfaces/{nicName}"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Com
pute/virtualMachines/{vmName}/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute
/virtualMachines/{vmName}",
      "name": "{vmName}"
    }
  ]
}
```

## <a name="get-security-group-view-for-virtual-machine"></a>Sanal makine için güvenlik grubu görünümü al

Aşağıdaki örnek, hedeflenen bir sanal makinenin güvenlik grubu görünümünü ister. Bu örneğin sonuçları, yapılandırma DRIP 'yi aramak için alma tarafından tanımlanan kurallarla ve güvenlikle karşılaştırmak için kullanılabilir.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName

$requestBody = @"
{
    'targetResourceId': '${targetUri}'

}
"@
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/securityGroupView?api-version=2016-12-01" $requestBody -verbose
```

## <a name="view-the-response"></a>Yanıtı görüntüleme

Aşağıdaki örnek, önceki komuttan döndürülen yanıt örneğidir. Sonuçlar, **Networkınterfacesecurityrules**, **Defaultsecurityrules** ve **securittivesecurityrules** gruplarında oluşan sanal makinede etkin ve uygulanan tüm güvenlik kurallarını gösterir.

```json

{
  "networkInterfaces": [
    {
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "securityRules": [
            {
              "name": "default-allow-rdp",
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
              "properties": {
                "provisioningState": "Succeeded",
                "protocol": "TCP",
                "sourcePortRange": "*",
                "destinationPortRange": "3389",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 1000,
                "direction": "Inbound"
              }
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "name": "AllowVnetInBound",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/",
            "properties": {
              "provisioningState": "Succeeded",
              "description": "Allow inbound traffic from all VMs in VNET",
              "protocol": "*",
              "sourcePortRange": "*",
              "destinationPortRange": "*",
              "sourceAddressPrefix": "VirtualNetwork",
              "destinationAddressPrefix": "VirtualNetwork",
              "access": "Allow",
              "priority": 65000,
              "direction": "Inbound"
            }
          },
          ...
        ],
        "effectiveSecurityRules": [
          {
            "name": "DefaultOutboundDenyAll",
            "protocol": "All",
            "sourcePortRange": "0-65535",
            "destinationPortRange": "0-65535",
            "sourceAddressPrefix": "*",
            "destinationAddressPrefix": "*",
            "access": "Deny",
            "priority": 65500,
            "direction": "Outbound"
          },
          ...
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Sonraki adımlar

Ağ güvenlik gruplarının doğrulanmasını otomatik hale getirmeyi öğrenmek için ağ [İzleyicisi ile ağ güvenlik grupları (NSG) denetimini](network-watcher-security-group-view-powershell.md) ziyaret edin.