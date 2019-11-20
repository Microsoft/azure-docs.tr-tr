---
title: Azure sanal ağında denetim yönlendirme-PowerShell-klasik | Microsoft Docs
description: PowerShell kullanarak VNET 'lerde yönlendirmeyi nasıl denetleyeceğinizi öğrenin | Klasik
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: d8d07c16-cbe5-4536-acd6-870269346fe3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: f2f2c17740bd94629209c2bffb82689ecc931fc8
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058761"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>PowerShell kullanarak yönlendirmeyi denetleme ve sanal gereçler (klasik) kullanma

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [Azure CLI](tutorial-create-route-table-cli.md)
> * [PowerShell (Klasik)](virtual-network-create-udr-classic-ps.md)
> * [CLI (Klasik)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

> [!IMPORTANT]
> Azure kaynaklarıyla çalışmadan önce Azure 'da Şu anda iki dağıtım modeli olduğunu anlamanız önemlidir: Azure Resource Manager ve klasik. Azure kaynaklarıyla çalışmadan önce [dağıtım modellerini ve araçlarlarını](../azure-resource-manager/resource-manager-deployment-model.md) iyice anladığınızdan emin olun. Bu makalenin üst kısmında bir seçenek belirleyerek farklı araçların belgelerini görüntüleyebilirsiniz. Bu makale, klasik dağıtım modelini kapsamaktadır.
> 

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Aşağıdaki örnek Azure PowerShell komutları yukarıdaki senaryoya göre önceden oluşturulmuş basit bir ortam bekler. Komutları bu belgede görüntülendikleri gibi çalıştırmak istiyorsanız, [PowerShell kullanarak VNET (klasik) oluşturma](virtual-networks-create-vnet-classic-netcfg-ps.md)bölümünde gösterilen ortamı oluşturun.

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Ön uç alt ağı için UDR oluşturma
Yukarıdaki senaryoya göre ön uç alt ağı için gereken yol tablosu ve yolu oluşturmak için aşağıdaki adımları izleyin.

1. Ön uç alt ağı için bir yol tablosu oluşturmak üzere aşağıdaki komutu çalıştırın:

    ```powershell
    New-AzureRouteTable -Name UDR-FrontEnd -Location uswest `
    -Label "Route table for front end subnet"
    ```

2. Bir arka uç alt ağına (192.168.2.0/24) giden tüm trafiği **FW1** VM 'sine (192.168.0.4) göndermek için yol tablosunda bir yol oluşturmak üzere aşağıdaki komutu çalıştırın:

    ```powershell
    Get-AzureRouteTable UDR-FrontEnd `
    |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Yol tablosunu **ön uç** alt ağıyla ilişkilendirmek için aşağıdaki komutu çalıştırın:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName FrontEnd `
    -RouteTableName UDR-FrontEnd
    ```

## <a name="create-the-udr-for-the-back-end-subnet"></a>Arka uç alt ağı için UDR 'yi oluşturma
Senaryoya bağlı olarak arka uç alt ağı için yol tablosu ve yolu oluşturmak üzere aşağıdaki adımları izleyin:

1. Arka uç alt ağı için bir yol tablosu oluşturmak üzere aşağıdaki komutu çalıştırın:

    ```powershell
    New-AzureRouteTable -Name UDR-BackEnd `
    -Location uswest `
    -Label "Route table for back end subnet"
    ```

2. Ön uç alt ağına (192.168.1.0/24) giden tüm trafiği **FW1** VM 'sine (192.168.0.4) göndermek için yol tablosunda bir yol oluşturmak üzere aşağıdaki komutu çalıştırın:

    ```powershell
    Get-AzureRouteTable UDR-BackEnd
    | Set-AzureRoute `
    -RouteName RouteToFrontEnd `
    -AddressPrefix 192.168.1.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Yol tablosunu **arka uç** alt ağıyla ilişkilendirmek için aşağıdaki komutu çalıştırın:

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName BackEnd `
    -RouteTableName UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>FW1 VM 'de IP iletmeyi etkinleştirme

FW1 VM 'de IP iletmeyi etkinleştirmek için aşağıdaki adımları izleyin:

1. IP iletimi durumunu denetlemek için şu komutu çalıştırın:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Get-AzureIPForwarding
    ```

2. *FW1* VM için IP iletmeyi etkinleştirmek üzere aşağıdaki komutu çalıştırın:

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Set-AzureIPForwarding -Enable
    ```
