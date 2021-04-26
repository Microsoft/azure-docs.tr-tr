---
title: PowerShell kullanarak birden çok genel IP adresi ile Azure Güvenlik Duvarı dağıtma
description: Bu makalede, Azure PowerShell kullanarak birden çok genel IP adresi ile Azure Güvenlik Duvarı dağıtmayı öğreneceksiniz.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 05/06/2020
ms.author: victorh
ms.openlocfilehash: cbad025a0d0c4d679ea9cdc7557c81b5145798fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96023678"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Azure PowerShell'i kullanarak birden çok IP adresiyle Azure Firewall dağıtma

Bu özellik aşağıdaki senaryolara izin vermez:

- **DNAT** -birden çok standart bağlantı noktası örneğini arka uç sunucularınıza çevirebilirsiniz. Örneğin iki genel IP adresini varsa 3389 (RDP) numaralı TCP bağlantı noktasını iki IP adresi için çevirebilirsiniz.
- **SNAT** -giden SNAT bağlantıları için ek bağlantı noktaları kullanılabilir ve bu, SNAT bağlantı noktası tükenmesi potansiyelini azaltır. Azure Güvenlik Duvarı şu anda bir bağlantı için kullanılacak kaynak genel IP adresini rastgele seçer. Ağınızda aşağı akış filtresi varsa güvenlik duvarınızla ilişkilendirilmiş olan tüm genel IP adreslerine izin vermeniz gerekir. Bu yapılandırmayı basitleştirmek için bir [genel IP adresi ön eki](../virtual-network/public-ip-address-prefix.md) kullanmayı düşünün.
 
Birden çok genel IP adresine sahip Azure Güvenlik Duvarı Azure portal, Azure PowerShell, Azure CLı, REST ve şablonlar aracılığıyla kullanılabilir. Azure Güvenlik Duvarı 'Nı en fazla 250 genel IP adresi ile dağıtabilirsiniz.

Aşağıdaki Azure PowerShell örneklerde, Azure Güvenlik Duvarı için genel IP adreslerini nasıl yapılandırabileceğiniz, ekleyebileceğiniz ve kaldırabilmeniz gösterilmektedir.

> [!NOTE]
> İlk IP adresini Azure Güvenlik Duvarı genel IP adresi yapılandırması sayfasından kaldıramazsınız. IP adresini değiştirmek istiyorsanız Azure PowerShell kullanabilirsiniz.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>İki veya daha fazla genel IP adresi ile bir güvenlik duvarı oluşturun

Bu örnek, iki genel IP adresi ile sanal ağ *VNET* 'e eklenmiş bir güvenlik duvarı oluşturur.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$pip2 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp2" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1, $pip2)
```

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Mevcut bir güvenlik duvarına genel IP adresi ekleme

Bu örnekte, *azFwPublicIp1* genel IP adresi güvenlik duvarına iliştirilir.

```azurepowershell
$pip = New-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.AddPublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>Mevcut bir güvenlik duvarındaki genel IP adresini kaldırma

Bu örnekte, *azFwPublicIp1* genel IP adresi güvenlik duvarından ayrılır.

```azurepowershell
$pip = Get-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg"

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.RemovePublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Sonraki adımlar

* [Hızlı başlangıç: birden çok genel IP adresi ile Azure Güvenlik Duvarı oluşturma-Kaynak Yöneticisi şablonu](quick-create-multiple-ip-template.md)
