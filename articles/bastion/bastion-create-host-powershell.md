---
title: Azure PowerShell kullanarak bir savunma ana bilgisayarı oluşturun | Microsoft Docs
description: Bu makalede, Azure savunma Konağı oluşturmayı öğrenin
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/14/2020
ms.author: cherylmc
ms.openlocfilehash: 8abd4c417181b46fbf4d5c139c157044b329ea2a
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580152"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Azure PowerShell’i kullanarak bir Azure Bastion konağı oluşturma

Bu makalede, PowerShell kullanarak bir Azure savunma ana bilgisayarı oluşturma işlemi gösterilmektedir. Sanal ağınızda Azure savunma hizmetini sağladığınızda, sorunsuz RDP/SSH deneyimi aynı sanal ağdaki tüm VM 'Ler tarafından kullanılabilir. Azure savunma dağıtımı, abonelik/hesap veya sanal makine başına değil, sanal ağ başına değildir.

İsteğe bağlı olarak, [Azure Portal](./tutorial-create-host-portal.md)kullanarak bir Azure savunma ana bilgisayarı oluşturabilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Azure aboneliğiniz olduğunu doğrulayın. Henüz Azure aboneliğiniz yoksa [MSDN abonelik avantajlarınızı](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) etkinleştirebilir veya [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial) için kaydolabilirsiniz.

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

 >[!NOTE]
 >Azure Özel DNS bölgeleriyle Azure savunma kullanımı Şu anda desteklenmiyor. Başlamadan önce lütfen savunma kaynağınızı dağıtmayı planladığınız sanal ağın özel bir DNS bölgesine bağlı olmadığından emin olun.
 >

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Savunma Konağı oluşturma

Bu bölüm Azure PowerShell kullanarak yeni bir Azure savunma kaynağı oluşturmanıza yardımcı olur.

1. Bir sanal ağ ve bir Azure savunma alt ağı oluşturun. **AzureBastionSubnet** ad değerini kullanarak Azure savunma alt ağını oluşturmanız gerekir. Bu değer, Azure 'un savunma kaynaklarını hangi alt ağa dağıtacağınızı bilmesini sağlar. Bu, bir ağ geçidi alt ağından farklıdır. En az/27 veya daha büyük alt ağın (/27,/26, vb.) bir alt ağını kullanmanız gerekir. Rota tabloları veya temsilcileri olmadan **AzureBastionSubnet** oluşturun. **AzureBastionSubnet** üzerinde ağ güvenlik grupları kullanıyorsanız [Nsgs ile çalışma](bastion-nsg.md) makalesine başvurun.

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myBastionRG" -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Azure Bastion için genel bir IP adresi oluşturun. Genel IP, RDP/SSH 'ye erişilecek savunma kaynağına genel IP adresidir (443 numaralı bağlantı noktası üzerinden). Genel IP adresi, oluşturmakta olduğunuz savunma kaynağıyla aynı bölgede olmalıdır.

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. Sanal ağınızın AzureBastionSubnet yeni bir Azure savunma kaynağı oluşturun. Savunma kaynağının oluşturulması ve dağıtılması yaklaşık 5 dakika sürer.

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName "myBastionRG" -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>Sonraki adımlar

* Ek bilgi için savunma [hakkında SSS](bastion-faq.md) makalesini okuyun.
* Azure savunma alt ağıyla ağ güvenlik gruplarını kullanmak için bkz. [NSG Ile çalışma](bastion-nsg.md).
