---
title: Güvenlik grubu görünümü ile ağ güvenliğini çözümleme-Azure CLı
titleSuffix: Azure Network Watcher
description: Bu makalede, Azure CLı kullanarak bir sanal makine güvenliğini güvenlik grubu görünümüyle çözümlemek için nasıl kullanılacağı açıklanır.
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
ms.openlocfilehash: b3aa963c4da7802a9db714f25e7b544b3a132d4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94948655"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli"></a>Azure CLı kullanarak sanal makine güvenliğine güvenlik grubu görünümü ile çözümleyin

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

> [!NOTE]
> Güvenlik grubu görünümü API 'SI artık korunmaz ve yakında kullanım dışı bırakılacak. Lütfen aynı işlevselliği sağlayan [etkin güvenlik kuralları özelliğini](./network-watcher-security-group-view-overview.md) kullanın.


Güvenlik grubu görünümü, bir sanal makineye uygulanan yapılandırılmış ve etkin ağ güvenlik kurallarını döndürür. Bu özellik, trafiğin doğru şekilde izin verilmesini veya reddedilmemesini sağlamak için bir sanal makinede yapılandırılan ağ güvenlik gruplarını ve kurallarını denetlemek ve tanılamak için yararlıdır. Bu makalede, Azure CLı kullanarak bir sanal makineye yapılandırılmış ve etkin güvenlik kurallarının nasıl alınacağını göstereceğiz

Bu makaledeki adımları gerçekleştirmek için [Mac, Linux ve Windows Için Azure komut satırı arabirimini (CLI) yüklemeniz](/cli/azure/install-azure-cli)gerekir.

## <a name="before-you-begin"></a>Başlamadan önce

Bu senaryo, ağ Izleyicisi oluşturmak için [ağ Izleyicisi oluşturma](network-watcher-create.md) bölümündeki adımları zaten izlediğinizi varsayar.

## <a name="scenario"></a>Senaryo

Bu makalede ele alınan senaryo, belirli bir sanal makine için yapılandırılmış ve etkin güvenlik kurallarını alır.

## <a name="get-a-vm"></a>VM al

Cmdlet 'ini çalıştırmak için bir sanal makine gerekir `vm list` . Aşağıdaki komut bir kaynak grubundaki sanal makineleri listeler:

```azurecli
az vm list -resource-group resourceGroupName
```

Sanal makineyi öğrendikten sonra, `vm show` kaynak kimliğini almak için cmdlet 'ini kullanabilirsiniz:

```azurecli
az vm show -resource-group resourceGroupName -name virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Güvenlik grubu görünümünü al

Bir sonraki adım, güvenlik grubu görünüm sonucunu almak için kullanılır.

```azurecli
az network watcher show-security-group-view --resource-group resourceGroupName --vm vmName
```

## <a name="viewing-the-results"></a>Sonuçları görüntüleme

Aşağıdaki örnek döndürülen sonuçların kısaltılmış bir yanıtı örneğidir. Sonuçlar, **Networkınterfacesecurityrules**, **Defaultsecurityrules** ve **securittivesecurityrules** gruplarında oluşan sanal makinede etkin ve uygulanan tüm güvenlik kurallarını gösterir.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
      "resourceGroup": "{resourceGroupName}",
      "securityRuleAssociations": {
        "defaultSecurityRules": [
          {
            "access": "Allow",
            "description": "Allow inbound traffic from all VMs in VNET",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "*",
            "direction": "Inbound",
            "etag": null,
            "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups//providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/AllowVnetInBound",
            "name": "AllowVnetInBound",
            "priority": 65000,
            "protocol": "*",
            "provisioningState": "Succeeded",
            "resourceGroup": "",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "*"
          }...
        ],
        "effectiveSecurityRules": [
          {
            "access": "Deny",
            "destinationAddressPrefix": "*",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": null,
            "expandedSourceAddressPrefix": null,
            "name": "DefaultOutboundDenyAll",
            "priority": 65500,
            "protocol": "All",
            "sourceAddressPrefix": "*",
            "sourcePortRange": "0-65535"
          },
          {
            "access": "Allow",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "expandedSourceAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "name": "DefaultRule_AllowVnetOutBound",
            "priority": 65000,
            "protocol": "All",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "0-65535"
          },...
        ],
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "resourceGroup": "{resourceGroupName}",
          "securityRules": [
            {
              "access": "Allow",
              "description": null,
              "destinationAddressPrefix": "*",
              "destinationPortRange": "3389",
              "direction": "Inbound",
              "etag": "W/\"efb606c1-2d54-475a-ab20-da3f80393577\"",
              "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "name": "default-allow-rdp",
              "priority": 1000,
              "protocol": "TCP",
              "provisioningState": "Succeeded",
              "resourceGroup": "{resourceGroupName}",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*"
            }
          ]
        },
        "subnetAssociation": null
      }
    }
  ]
}
```

## <a name="next-steps"></a>Sonraki adımlar

Ağ güvenlik gruplarının doğrulanmasını otomatik hale getirmeyi öğrenmek için ağ [İzleyicisi ile ağ güvenlik grupları (NSG) denetimini](network-watcher-nsg-auditing-powershell.md) ziyaret edin.

[Güvenlik grubu görünümüne genel bakış ' ı](network-watcher-security-group-view-overview.md) ziyaret ederek ağ kaynaklarınıza uygulanan güvenlik kuralları hakkında daha fazla bilgi edinin