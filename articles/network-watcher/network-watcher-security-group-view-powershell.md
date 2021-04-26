---
title: Ağ güvenliğini çözümleme-güvenlik grubu görünümü-Azure PowerShell
titleSuffix: Azure Network Watcher
description: Bu makalede, PowerShell kullanarak bir sanal makine güvenliğini güvenlik grubu görünümüyle analiz etme açıklanır.
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
ms.openlocfilehash: 2da4ebfa6a9ce64b03e6e1d29956f740f6cb3c1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94960657"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-powershell"></a>PowerShell kullanarak sanal makine güvenliğine güvenlik grubu görünümü ile çözümleyin

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure CLI](network-watcher-security-group-view-cli.md)
> - [REST API](network-watcher-security-group-view-rest.md)

> [!NOTE]
> Güvenlik grubu görünümü API 'SI artık korunmaz ve yakında kullanım dışı bırakılacak. Lütfen aynı işlevselliği sağlayan [etkin güvenlik kuralları özelliğini](./network-watcher-security-group-view-overview.md) kullanın. 

Güvenlik grubu görünümü, bir sanal makineye uygulanan yapılandırılmış ve etkin ağ güvenlik kurallarını döndürür. Bu özellik, trafiğin doğru şekilde izin verilmesini veya reddedilmemesini sağlamak için bir sanal makinede yapılandırılan ağ güvenlik gruplarını ve kurallarını denetlemek ve tanılamak için yararlıdır. Bu makalede, PowerShell kullanarak yapılandırılmış ve etkin güvenlik kurallarının bir sanal makineye nasıl alınacağını göstereceğiz


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Başlamadan önce

Bu senaryoda, `Get-AzNetworkWatcherSecurityGroupView` güvenlik kuralı bilgilerini almak için cmdlet 'ini çalıştırırsınız.

Bu senaryo, ağ Izleyicisi oluşturmak için [ağ Izleyicisi oluşturma](network-watcher-create.md) bölümündeki adımları zaten izlediğinizi varsayar.

## <a name="scenario"></a>Senaryo

Bu makalede ele alınan senaryo, belirli bir sanal makine için yapılandırılmış ve etkin güvenlik kurallarını alır.

## <a name="retrieve-network-watcher"></a>Ağ İzleyicisini al

İlk adım, ağ Izleyicisi örneğini almak için kullanılır. Bu değişken cmdlet 'e geçirilir `Get-AzNetworkWatcherSecurityGroupView` .

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
```

## <a name="get-a-vm"></a>VM al

Cmdlet 'ini çalıştırmak için bir sanal makine gerekir `Get-AzNetworkWatcherSecurityGroupView` . Aşağıdaki örnek bir VM nesnesini alır.

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name testvm1
```

## <a name="retrieve-security-group-view"></a>Güvenlik grubu görünümünü al

Bir sonraki adım, güvenlik grubu görünüm sonucunu almak için kullanılır.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="viewing-the-results"></a>Sonuçları görüntüleme

Aşağıdaki örnek döndürülen sonuçların kısaltılmış bir yanıtı örneğidir. Sonuçlar, **Networkınterfacesecurityrules**, **Defaultsecurityrules** ve **securittivesecurityrules** gruplarında oluşan sanal makinede etkin ve uygulanan tüm güvenlik kurallarını gösterir.

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