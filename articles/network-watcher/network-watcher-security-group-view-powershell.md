---
title: Azure ağ Izleyicisi güvenlik grubu görünümü ile ağ güvenliğini çözümleme-PowerShell | Microsoft Docs
description: Bu makalede, PowerShell kullanarak bir sanal makine güvenliğini güvenlik grubu görünümüyle analiz etme açıklanır.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 04e76b49-6a1b-4d0f-9a9b-51cf2f4df5a2
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 4c7b79460169612a046b19a4d66f222936710a8e
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163892"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-powershell"></a>PowerShell kullanarak sanal makine güvenliğine güvenlik grubu görünümü ile çözümleyin

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

Güvenlik grubu görünümü, bir sanal makineye uygulanan yapılandırılmış ve etkin ağ güvenlik kurallarını döndürür. Bu özellik, trafiğin doğru şekilde izin verilmesini veya reddedilmemesini sağlamak için bir sanal makinede yapılandırılan ağ güvenlik gruplarını ve kurallarını denetlemek ve tanılamak için yararlıdır. Bu makalede, PowerShell kullanarak yapılandırılmış ve etkin güvenlik kurallarının bir sanal makineye nasıl alınacağını göstereceğiz


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Başlamadan önce

Bu senaryoda, güvenlik kuralı bilgilerini almak `Get-AzNetworkWatcherSecurityGroupView` için cmdlet 'ini çalıştırırsınız.

Bu senaryo, ağ Izleyicisi oluşturmak için [ağ Izleyicisi oluşturma](network-watcher-create.md) bölümündeki adımları zaten izlediğinizi varsayar.

## <a name="scenario"></a>Senaryo

Bu makalede ele alınan senaryo, belirli bir sanal makine için yapılandırılmış ve etkin güvenlik kurallarını alır.

## <a name="retrieve-network-watcher"></a>Ağ İzleyicisini al

İlk adım, ağ Izleyicisi örneğini almak için kullanılır. Bu değişken `Get-AzNetworkWatcherSecurityGroupView` cmdlet 'e geçirilir.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
```

## <a name="get-a-vm"></a>VM al

`Get-AzNetworkWatcherSecurityGroupView` Cmdlet 'ini çalıştırmak için bir sanal makine gerekir. Aşağıdaki örnek bir VM nesnesini alır.

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name testvm1
```

## <a name="retrieve-security-group-view"></a>Güvenlik grubu görünümünü al

Bir sonraki adım, güvenlik grubu görünüm sonucunu almak için kullanılır.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="viewing-the-results"></a>Sonuçları görüntüleme

Aşağıdaki örnek döndürülen sonuçların kısaltılmış bir yanıtı örneğidir. Sonuçlar, **Networkınterfacesecurityrules**, **Defaultsecurityrules**ve **securittivesecurityrules**gruplarında oluşan sanal makinede etkin ve uygulanan tüm güvenlik kurallarını gösterir.

```
NetworkInterfaces : [
                      {
                        "NetworkInterfaceSecurityRules": [
                          {
                            "Name": "default-allow-rdp",
                            "Etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
                            "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/securityRules/default-allow-rdp",
                            "Protocol": "TCP",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "3389",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "Access": "Allow",
                            "Priority": 1000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "DefaultSecurityRules": [
                          {
                            "Name": "AllowVnetInBound",
                            "Id": "/subscriptions00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/defaultSecurityRules/",
                            "Description": "Allow inbound traffic from all VMs in VNET",
                            "Protocol": "*",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "*",
                            "SourceAddressPrefix": "VirtualNetwork",
                            "DestinationAddressPrefix": "VirtualNetwork",
                            "Access": "Allow",
                            "Priority": 65000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "EffectiveSecurityRules": [
                          {
                            "Name": "DefaultOutboundDenyAll",
                            "Protocol": "All",
                            "SourcePortRange": "0-65535",
                            "DestinationPortRange": "0-65535",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "ExpandedSourceAddressPrefix": [],
                            "ExpandedDestinationAddressPrefix": [],
                            "Access": "Deny",
                            "Priority": 65500,
                            "Direction": "Outbound"
                          },
                          ...
                        ]
                      }
                    ]
```

## <a name="next-steps"></a>Sonraki adımlar

Ağ güvenlik gruplarının doğrulanmasını otomatik hale getirmeyi öğrenmek için ağ [İzleyicisi ile ağ güvenlik grupları (NSG) denetimini](network-watcher-nsg-auditing-powershell.md) ziyaret edin.


