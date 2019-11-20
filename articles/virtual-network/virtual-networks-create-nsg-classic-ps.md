---
title: PowerShell kullanarak bir ağ güvenlik grubu (klasik) oluşturma | Microsoft Docs
description: PowerShell kullanarak bir ağ güvenlik grubu (klasik) oluşturma ve dağıtma hakkında bilgi edinin
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: 86810b0d-0240-46a2-8548-fca22daa56f3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: 5ef80ccd01c9c6979fd95d161d97d0dfaab58b24
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056660"
---
# <a name="create-a-network-security-group-classic-using-powershell"></a>PowerShell kullanarak bir ağ güvenlik grubu (klasik) oluşturma
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Bu makale, klasik dağıtım modelini kapsamaktadır. Ayrıca [, Kaynak Yöneticisi dağıtım modelinde NSG](tutorial-filter-network-traffic.md)'ler de oluşturabilirsiniz.

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Aşağıdaki örnek PowerShell komutları, yukarıdaki senaryoya göre önceden oluşturulmuş basit bir ortam bekliyor. Komutları bu belgede görüntülendikleri gibi çalıştırmak istiyorsanız, önce [sanal ağ oluşturarak](virtual-networks-create-vnet-classic-netcfg-ps.md)test ortamını derleyin.

## <a name="create-an-nsg-for-the-front-end-subnet"></a>Ön uç alt ağı için NSG oluşturma

1. Azure PowerShell hiç kullanmadıysanız, bkz. [Azure PowerShell nasıl yüklenir ve yapılandırılır](/powershell/azure/overview).

2. NSG adlı bir ağ güvenlik grubu oluşturun *-ön uç*:

    ```powershell   
    New-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" -Location uswest `
      -Label "Front end subnet NSG"
   ```

3. Internet 'ten 3389 numaralı bağlantı noktasına erişime izin veren bir güvenlik kuralı oluşturun:

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name rdp-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
      -SourceAddressPrefix Internet  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '3389'
   ```

4. Internet 'ten 80 numaralı bağlantı noktasına erişime izin veren bir güvenlik kuralı oluşturun:

    ```powershell   
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name web-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 200 `
      -SourceAddressPrefix Internet  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '80'
    ```

## <a name="create-an-nsg-for-the-back-end-subnet"></a>Arka uç alt ağı için NSG oluşturma

1. *NSG-arka uç*adlı bir ağ güvenlik grubu oluşturun:
   
    ```powershell
    New-AzureNetworkSecurityGroup -Name "NSG-BackEnd" -Location uswest `
      -Label "Back end subnet NSG"
    ```

2. Ön uç alt ağından 1433 numaralı bağlantı noktasına erişime izin veren bir güvenlik kuralı oluşturun (varsayılan bağlantı noktası SQL Server tarafından kullanılır):
   
    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-FrontEnd" `
      | Set-AzureNetworkSecurityRule -Name rdp-rule `
      -Action Allow -Protocol TCP -Type Inbound -Priority 100 `
      -SourceAddressPrefix 192.168.1.0/24  -SourcePortRange '*' `
      -DestinationAddressPrefix '*' -DestinationPortRange '1433'
    ```

3. Alt ağdan Internet 'e erişimi engelleyen bir güvenlik kuralı oluşturun:
   
    ```powershell
    Get-AzureNetworkSecurityGroup -Name "NSG-BackEnd" `
      | Set-AzureNetworkSecurityRule -Name block-internet `
      -Action Deny -Protocol '*' -Type Outbound -Priority 200 `
      -SourceAddressPrefix '*'  -SourcePortRange '*' `
      -DestinationAddressPrefix Internet -DestinationPortRange '*'
   ```