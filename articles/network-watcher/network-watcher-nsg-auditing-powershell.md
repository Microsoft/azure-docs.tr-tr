---
title: NSG denetimini otomatikleştirin-güvenlik grubu görünümü
titleSuffix: Azure Network Watcher
description: Bu sayfa bir ağ güvenlik grubu denetiminin nasıl yapılandırılacağı hakkında yönergeler sağlar
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
ms.openlocfilehash: 177215775c9e83286aa98872eed0ab211a8f36ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94948757"
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Azure ağ Izleyicisi güvenlik grubu görünümü ile NSG denetimini otomatikleştirin

Müşteriler genellikle altyapısının güvenlik duruşunu doğrulamaya yönelik zorluk gösterir. Bu zorluk, Azure 'daki VM 'Ler için farklı değildir. Uygulanan ağ güvenlik grubu (NSG) kurallarını temel alan benzer bir güvenlik profiline sahip olmak önemlidir. Güvenlik grubu görünümünü kullanarak, artık NSG içindeki bir VM 'ye uygulanan kuralların listesini alabilirsiniz. Bir altın NSG güvenlik profili tanımlayabilir ve bir haftalık temposunda güvenlik grubu görünümünü başlatabilir ve çıktıyı altın profille karşılaştırabilir ve bir rapor oluşturabilirsiniz. Bu şekilde, önceden tanımlanmış güvenlik profiliyle uyumlu olmayan tüm VM 'Leri kolay bir şekilde belirleyebilirsiniz.

Ağ güvenlik grupları hakkında bilginiz varsa bkz. [ağ güvenliğine genel bakış](../virtual-network/network-security-groups-overview.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Başlamadan önce

Bu senaryoda, bilinen iyi bir taban çizgisini, bir sanal makine için döndürülen güvenlik grubu görünümü sonuçlarıyla karşılaştırırsınız.

Bu senaryo, ağ Izleyicisi oluşturmak için [ağ Izleyicisi oluşturma](network-watcher-create.md) bölümündeki adımları zaten izlediğinizi varsayar. Senaryo Ayrıca, geçerli bir sanal makineye sahip bir kaynak grubunun kullanılabilir olduğunu varsayar.

## <a name="scenario"></a>Senaryo

Bu makalede ele alınan senaryo, bir sanal makine için güvenlik grubu görünümünü alır.

Bu senaryoda şunları yapmanız gerekir:

- Bilinen iyi bir kural kümesini alma
- REST API ile sanal makine alma
- Sanal makine için güvenlik grubu görünümü al
- Yanıtı değerlendir

## <a name="retrieve-rule-set"></a>Kural kümesini al

Bu örnekteki ilk adım, var olan bir taban çizgisiyle çalışır. Aşağıdaki örnek, `Get-AzNetworkSecurityGroup` Bu örnek için taban çizgisi olarak kullanılan cmdlet 'ini kullanarak var olan bir ağ güvenlik grubundan ayıklanan bir JSON örneğidir.

```json
[
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "3389",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1000,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "default-allow-rdp",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/default-allow-rdp"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "111",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1010,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "MyRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/MyRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "112",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1020,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "My2ndRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/My2ndRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "5672",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Deny",
        "Priority":  1030,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "ThisRuleNeedsToStay",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/ThisRuleNeedsToStay"
    }
]
```

## <a name="convert-rule-set-to-powershell-objects"></a>Kural kümesini PowerShell nesnelerine Dönüştür

Bu adımda, bu örnek için ağ güvenlik grubunda olması beklenen kurallarla daha önce oluşturulmuş bir JSON dosyasını okuyoruz.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Ağ İzleyicisini al

Sonraki adım, ağ Izleyicisi örneğini almak için kullanılır. `$networkWatcher`Değişkeni cmdlet 'e geçirilir `AzNetworkWatcherSecurityGroupView` .

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
```

## <a name="get-a-vm"></a>VM al

Cmdlet 'ini çalıştırmak için bir sanal makine gerekir `Get-AzNetworkWatcherSecurityGroupView` . Aşağıdaki örnek bir VM nesnesini alır.

```powershell
$VM = Get-AzVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Güvenlik grubu görünümünü al

Bir sonraki adım, güvenlik grubu görünüm sonucunu almak için kullanılır. Bu sonuç, daha önce gösterilen "taban çizgisi" JSON ile karşılaştırılır.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>Sonuçlar çözümleniyor

Yanıt, ağ arabirimlerine göre gruplandırılır. Döndürülen farklı kural türleri, etkili ve varsayılan güvenlik kurallarıdır. Sonuç, bir alt ağda veya sanal NIC 'de nasıl uygulandığı tarafından daha da bölünür.

Aşağıdaki PowerShell betiği, güvenlik grubu görünümünün sonuçlarını bir NSG 'nin mevcut çıkışıyla karşılaştırır. Aşağıdaki örnek, sonuçların cmdlet ile nasıl karşılaştırıladığına ilişkin basit bir örnektir `Compare-Object` .

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

Aşağıdaki örnek, sonucudur. İlk kural kümesindeki kuralların iki tanesi karşılaştırmayla mevcut değildir.

```
Name                     : My2ndRuleDoNotDelete
Description              : 
Protocol                 : *
SourcePortRange          : *
DestinationPortRange     : 112
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Allow
Priority                 : 1020
Direction                : Inbound
SideIndicator            : <=

Name                     : ThisRuleNeedsToStay
Description              : 
Protocol                 : TCP
SourcePortRange          : *
DestinationPortRange     : 5672
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Deny
Priority                 : 1030
Direction                : Inbound
SideIndicator            : <=
```

## <a name="next-steps"></a>Sonraki adımlar

Ayarlar değiştirildiyse, ağ güvenlik grubunu ve söz konusu güvenlik kurallarını izlemek için [ağ güvenlik gruplarını yönetme](../virtual-network/manage-network-security-group.md) bölümüne bakın.