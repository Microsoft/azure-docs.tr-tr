---
title: Bir Azure sanal ağında genel IPv6 adreslerini ve adres aralıklarını ayırma
titlesuffix: Azure Virtual Network
description: Bir Azure sanal ağında genel IPv6 adreslerini ve adres aralıklarını nasıl ayıracağınızı öğrenin.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2019
ms.author: kumud
ms.openlocfilehash: 76d1ba2717ac3c8ac8e86687ef1754a8790f3e4d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595080"
---
# <a name="reserve-public-ipv6-address-prefix"></a>Genel IPv6 adresi ön ekini ayır
Azure sanal ağ (VNet) için IPv6, Azure 'daki uygulamaları hem bir sanal ağ hem de Internet üzerinden IPv6 ve IPv4 bağlantısıyla barındırmanızı sağlar. Ayrı IPv6 adreslerini ayırmaya ek olarak, kullanım için Azure IPv6 adreslerinin (IP öneki olarak bilinir) bitişik aralıklarını ayırabilirsiniz. Bu makalelerde, Azure PowerShell ve CLı kullanarak IPv6 genel IP adresleri ve adres aralıklarının nasıl oluşturulacağı açıklanmaktadır.

> [!Important]
> Azure sanal ağ için IPv6 Şu anda genel önizleme aşamasındadır. Bu önizleme bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Ayrıntılar için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-single-reserved-ipv6-public-ip"></a>Tek bir ayrılmış IPv6 genel IP 'si oluşturma

### <a name="using-azure-powershell"></a>Azure PowerShell’i kullanma

[New-Azpublicıpaddress](/powershell/module/az.network/new-azpublicipaddress) ile Azure PowerShell kullanarak, tek bir ayrılmış (statik) ıPV6 genel IP adresini aşağıdaki gibi oluşturabilirsiniz:

```azurepowershell  
 $myOwnIPv6Address = New-AzPublicIpAddress `
 -name PIPv6_WestUS `
 -ResourceGroup MyRG `
 -Location "West US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6
 ```

### <a name="using-azure-cli"></a>Azure CLI’yı kullanma

 [Az Network public-IP Create](/cli/azure/network/public-ip) komutuyla şu şekilde tek bir ayrılmış (statik) ıPV6 genel IP adresi oluşturabilirsiniz:
  
```azurecli
 az network public-ip create \
 --name dsPublicIP_v6 \
 --resource-group UpgradeInPlace_CLI_RG1 \
 --location WestUS \
 --sku Standard  \
 --allocation-method static  \
 --version IPv6
```

## <a name="create-a-reserved-ipv6-prefix-range"></a>Ayrılmış IPv6 öneki (Aralık) oluşturma

Bir IPv6 öneki ayırmak için IPv6 IP adresi ailesini IPv4 öneklerini oluşturmak için kullanılan aynı komuta ekleyin. Aşağıdaki komutlar bir boyut/125 (8 IPv6 adresi) ön eki oluşturur.  

### <a name="using-azure-powershell"></a>Azure PowerShell’i kullanma

Azure CLı kullanarak, [az Network public-ip Create](/powershell/module/az.network/new-azpublicipprefix) komutuyla şu şekilde genel bir IPv6 adresi oluşturabilirsiniz:
```azurepowershell  
 $myOwnIPv6Prefix = New-AzPublicIpPrefix `
 -name IPv6PrefixWestUS `
 -ResourceGroupName MyRG `
 -Location "West US" `
 -Sku Standard `
 -IpAddressVersion IPv6 `
 -PrefixLength 125
```

### <a name="using-azure-cli"></a>Azure CLI’yı kullanma

Azure CLı kullanarak aşağıdaki gibi genel bir IPv6 adresi oluşturabilirsiniz:

```azurecli  
az network public-ip prefix create \
--name IPv6PrefixWestUS \
--resource-group MyRG \
--location WestUS \
--version IPv6 \
--length 125
```

## <a name="allocate-a-public-ip-address-from-a-reserved-ipv6-prefix"></a>Ayrılmış IPv6 önekinden ortak bir IP adresi ayırın

### <a name="using-azure-powershell"></a>Azure PowerShell’i kullanma

 Azure PowerShell kullanarak genel IP oluştururken `-PublicIpPrefix` bağımsız değişkeni ekleyerek ayrılmış bir önekten statik bir IPv6 genel IP 'si oluşturursunuz. Aşağıdaki örnek, *$MyOwnIPv 6prefix*adlı bir PowerShell değişkenine bir ön ek oluşturulup depolandığını varsayar.

```azurepowershell:  
 $MyIPv6PublicIPFromMyReservedPrefix = New-AzPublicIpAddress \
 -name PIPv6_fromPrefix `
 -ResourceGroup DsStdLb04 `
 -Location "West Central US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6 `
 -PublicIpPrefix $myOwnIPv6Prefix
```

### <a name="using-azure-cli"></a>Azure CLI’yı kullanma
 
Aşağıdaki örnek, bir önekin oluşturulduğunu ve şu adlı bir CLı değişkeninde depolandığını varsayar: *IPv6PrefixWestUS*.

```azurecli 
az network public-ip create \
--name dsPublicIP_v6 \
--resource-group UpgradeInPlace_CLI_RG1 \
--location WestUS \
--sku Standard \
--allocation-method static \
--version IPv6 \
--public-ip-prefix  IPv6PrefixWestUS
```

## <a name="next-steps"></a>Sonraki adımlar
- [IPv6 adresi ön eki](ipv6-public-ip-address-prefix.md)hakkında daha fazla bilgi edinin.
- [IPv6 adresleri](ipv6-overview.md)hakkında daha fazla bilgi edinin.
