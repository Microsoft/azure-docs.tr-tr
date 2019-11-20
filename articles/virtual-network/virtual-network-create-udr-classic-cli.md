---
title: Azure sanal ağında denetim yönlendirme-CLı-klasik | Microsoft Docs
description: Klasik dağıtım modelinde Azure CLı kullanarak VNET 'lerde yönlendirmeyi nasıl denetleyeceğinizi öğrenin
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: ca2b4638-8777-4d30-b972-eb790a7c804f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 1193145b315175e6394db4caf93ab2e76a942ed9
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058780"
---
# <a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Azure CLı kullanarak yönlendirmeyi denetleme ve sanal gereçler (klasik) kullanma

> [!div class="op_single_selector"]
> * [PowerShell](tutorial-create-route-table-powershell.md)
> * [Azure CLI](tutorial-create-route-table-cli.md)
> * [PowerShell (Klasik)](virtual-network-create-udr-classic-ps.md)
> * [CLI (Klasik)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Bu makale, klasik dağıtım modelini kapsamaktadır. Ayrıca [Kaynak Yöneticisi dağıtım modelinde yönlendirmeyi denetleyebilir ve sanal gereçler kullanabilirsiniz](tutorial-create-route-table-cli.md).

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Aşağıdaki örnek Azure CLı komutları, yukarıdaki senaryoya göre önceden oluşturulmuş basit bir ortam bekliyor. Komutları bu belgede görüntülendikleri gibi çalıştırmak istiyorsanız, [Azure CLI kullanarak VNET (klasik) oluşturma](virtual-networks-create-vnet-classic-cli.md)bölümünde gösterilen ortamı oluşturun.

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Ön uç alt ağı için UDR oluşturma
Yukarıdaki senaryoya göre ön uç alt ağı için gereken yol tablosu ve yolu oluşturmak için aşağıdaki adımları izleyin.

1. Klasik moda geçmek için aşağıdaki komutu çalıştırın:

    ```azurecli
    azure config mode asm
    ```

    Çıktı:

        info:    New mode is asm

2. Ön uç alt ağı için bir yol tablosu oluşturmak üzere aşağıdaki komutu çalıştırın:

    ```azurecli
    azure network route-table create -n UDR-FrontEnd -l uswest
    ```
   
    Çıktı:
   
        info:    Executing command network route-table create
        info:    Creating route table "UDR-FrontEnd"
        info:    Getting route table "UDR-FrontEnd"
        data:    Name                            : UDR-FrontEnd
        data:    Location                        : West US
        info:    network route-table create command OK
   
    Parametreler:
   
   * **-l (veya --konum)** . Yeni NSG 'nin oluşturulacağı Azure bölgesi. Senaryolarımız için *westus*.
   * **-n (veya --name)** . Yeni NSG için ad. Senaryolarımız için *NSG-ön uç*.
3. Bir arka uç alt ağına (192.168.2.0/24) giden tüm trafiği **FW1** VM 'sine (192.168.0.4) göndermek için yol tablosunda bir yol oluşturmak üzere aşağıdaki komutu çalıştırın:

    ```azurecli
    azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

    Çıktı:
   
        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK
   
    Parametreler:
   
   * **-r (veya--Route-table-name)** . Yolun ekleneceği yol tablosunun adı. Senaryolarımız için *UDR-ön uç*.
   * **-a (veya--adres-önek)** . Paketlerin hedefi olan alt ağın adres ön eki. Senaryolarımız için *192.168.2.0/24*.
   * **-t (veya--Next-Hop-Type)** . Nesne trafiğinin türüne gönderileceği yer. Olası değerler *Virtualappliance*, *virtualnetworkgateway*, *vnetlocal*, *Internet*veya *none*.
   * **-p (veya--Next-atlama-IP-adresi**). Sonraki atlama için IP adresi. Senaryolarımız için *192.168.0.4*.
4. **Ön uç** alt ağıyla oluşturulan yol tablosunu ilişkilendirmek için aşağıdaki komutu çalıştırın:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd
    ```
   
    Çıktı:
   
        info:    Executing command network vnet subnet route-table add
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        data:    Route table name                : UDR-FrontEnd
        data:      Location                      : West US
        data:      Routes:
        info:    network vnet subnet route-table add command OK    
   
    Parametreler:
   
   * **-t (veya--VNET-adı)** . Alt ağın bulunduğu VNet 'in adı. Bizim senaryomuz için bu *TestVNet* ’tir.
   * **-n (veya--alt ağ-adı**. Yol tablosunun ekleneceği alt ağın adı. Bizim senaryomuz için bu *FrontEnd* ’dir.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Arka uç alt ağı için UDR 'yi oluşturma
Senaryoya bağlı olarak arka uç alt ağı için yol tablosu ve yolu oluşturmak üzere aşağıdaki adımları izleyin:

1. Arka uç alt ağı için bir yol tablosu oluşturmak üzere aşağıdaki komutu çalıştırın:

    ```azurecli
    azure network route-table create -n UDR-BackEnd -l uswest
    ```

2. Ön uç alt ağına (192.168.1.0/24) giden tüm trafiği **FW1** VM 'sine (192.168.0.4) göndermek için yol tablosunda bir yol oluşturmak üzere aşağıdaki komutu çalıştırın:

    ```azurecli
    azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4
    ```

3. Yol tablosunu **arka uç** alt ağıyla ilişkilendirmek için aşağıdaki komutu çalıştırın:

    ```azurecli
    azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd
    ```

