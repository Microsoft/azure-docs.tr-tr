---
title: Klasik Azure CLı kullanarak bir ağ güvenlik grubu (klasik) oluşturma | Microsoft Docs
description: Klasik Azure CLı kullanarak bir ağ güvenlik grubu (klasik) oluşturma ve dağıtma hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-service-management
ms.assetid: 17d98950-5fbb-4653-bef6-d822ab37541e
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.openlocfilehash: be63cdc3df5752f73b21bb5adc5fffaa364e7f43
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056720"
---
# <a name="create-a-network-security-group-classic-using-the-azure-classic-cli"></a>Klasik Azure CLı kullanarak bir ağ güvenlik grubu (klasik) oluşturma
[!INCLUDE [virtual-networks-create-nsg-selectors-classic-include](../../includes/virtual-networks-create-nsg-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Bu makale, klasik dağıtım modelini kapsamaktadır. Ayrıca [, Kaynak Yöneticisi dağıtım modelinde NSG](tutorial-filter-network-traffic-cli.md)'ler de oluşturabilirsiniz.

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Aşağıdaki örnek Azure CLı komutları, senaryoya göre zaten oluşturulmuş basit bir ortam bekliyor. Komutları bu belgede görüntülendikleri gibi çalıştırmak istiyorsanız, önce [sanal ağ oluşturarak](virtual-networks-create-vnet-classic-cli.md)test ortamını derleyin.

## <a name="create-an-nsg-for-the-front-end-subnet"></a>Ön uç alt ağı için NSG oluşturma

1. Azure CLı 'yı hiç kullanmadıysanız bkz. [Azure CLI 'Yi yükleyip yapılandırma](/cli/azure/install-cli-version-1.0).
2. Klasik moda geç:

    ```azurecli
    azure config mode asm
    ```   

3. NSG oluştur::
   
    ```azurecli   
     azure network nsg create -l uswest -n NSG-FrontEnd
    ```
   
4. Internet 'ten 3389 (RDP) bağlantı noktasına erişime izin veren bir güvenlik kuralı oluşturun:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389
   ```

5. Internet 'ten 80 (HTTP) bağlantı noktasına erişime izin veren bir kural oluşturun:
   
    ```azurecli
    azure network nsg rule create -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80
    ```   

6. NSG 'yi ön uç alt ağıyla ilişkilendirin:
   
    ```azurecli
    azure network nsg subnet add -a NSG-FrontEnd --vnet-name TestVNet --subnet-name FrontEnd
   ```

## <a name="create-the-nsg-for-the-back-end-subnet"></a>Arka uç alt ağı için NSG oluşturma

1. NSG 'yi oluşturma:
   
    ```azurecli
    azure network nsg create -l uswest -n NSG-BackEnd
   ```

2. Ön uç alt ağından 1433 numaralı bağlantı noktasına (SQL) erişime izin veren bir kural oluşturun:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433
   ```

3. Internet 'e erişimi engelleyen bir kural oluşturun:
   
    ```azurecli
    azure network nsg rule create -a NSG-BackEnd -n web-rule -c Deny -p Tcp -r Outbound -y 200 -f * -o * -e Internet -u 80
   ```

4. NSG 'yi arka uç alt ağıyla ilişkilendirin:
   
    ```azurecli
    azure network nsg subnet add -a NSG-BackEnd --vnet-name TestVNet --subnet-name BackEnd
    ```