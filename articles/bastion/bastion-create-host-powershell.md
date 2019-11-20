---
title: Azure PowerShell kullanarak bir savunma Konağı oluşturma | Microsoft Docs
description: Bu makalede, Azure savunma Konağı oluşturmayı öğrenin
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: 6cae6d258da2ddf0c3bfaade65ae74f1201b67b7
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121062"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Azure PowerShell kullanarak bir Azure savunma ana bilgisayarı oluşturma

Bu makalede bir Azure savunma ana bilgisayarı oluşturma işlemi gösterilmektedir. Sanal ağınızda Azure savunma hizmetini sağladığınızda, sorunsuz RDP/SSH deneyimi aynı sanal ağdaki tüm VM 'leriniz için kullanılabilir. Bu dağıtım, abonelik/hesap veya sanal makine değil sanal ağ başına yapılır.

## <a name="before-you-begin"></a>Başlamadan önce

Azure aboneliğiniz olduğunu doğrulayın. Henüz Azure aboneliğiniz yoksa [MSDN abonelik avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial) için kaydolabilirsiniz.

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="createhost"></a>Savunma Konağı oluşturma

Bu bölüm Azure PowerShell kullanarak yeni bir Azure savunma kaynağı oluşturmanıza yardımcı olur.

1. Bir sanal ağ ve bir Azure savunma alt ağı oluşturun. **AzureBastionSubnet**ad değerini kullanarak Azure savunma alt ağını oluşturmanız gerekir. Bu değer, Azure 'un savunma kaynaklarını hangi alt ağa dağıtacağınızı bilmesini sağlar. Bu, bir ağ geçidi alt ağından farklıdır. En az bir/27 veya daha büyük alt ağın (/27,/26, vb.) bir alt ağını kullanmanız gerekir. Rota tabloları veya temsilcileri olmadan **AzureBastionSubnet** oluşturun. **AzureBastionSubnet**üzerinde ağ güvenlik grupları kullandığınızda [NSG 'ler ile çalışma](bastion-nsg.md)bölümüne bakın.

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName " myBastionRG " -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Azure savunma için genel bir IP adresi oluşturun. Genel IP, RDP/SSH 'ye erişilecek savunma kaynağına genel IP adresidir (443 numaralı bağlantı noktası üzerinden). Genel IP adresi, oluşturmakta olduğunuz savunma kaynağıyla aynı bölgede olmalıdır.

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. Sanal ağınızın AzureBastionSubnet yeni bir Azure savunma kaynağı oluşturun. Savunma kaynağının oluşturulması ve dağıtılması yaklaşık 5 dakika sürer.

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName " myBastionRG " -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>Sonraki adımlar

Savunma [hakkında SSS](bastion-faq.md)makalesini okuyun.
