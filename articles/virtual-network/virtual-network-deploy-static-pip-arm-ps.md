---
title: Statik genel IP adresi ile VM oluşturma-PowerShell | Microsoft Docs
description: PowerShell kullanarak statik bir genel IP adresi ile bir sanal makine (VM) oluşturun. Statik genel IP adresleri hiçbir değişiklik olmayan adreslerdir.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: allensu
ms.openlocfilehash: 047a6db82e65c97deba5270d181f72315a67e82c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791265"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-powershell"></a>PowerShell kullanarak statik bir genel IP adresi ile sanal makine oluşturma


Statik bir genel IP adresine sahip bir sanal makine oluşturabilirsiniz. Genel bir IP adresi, internet 'ten bir sanal makineyle iletişim kurmanızı sağlar. Adresin hiçbir şekilde değişmeyeceğinden emin olmak için dinamik bir adres yerine statik bir genel IP adresi atayın. [Statik genel IP adresleri](./public-ip-addresses.md#allocation-method)hakkında daha fazla bilgi edinin. Var olan bir sanal makineye atanan bir genel IP adresini dinamik veya özel IP adresleriyle çalışacak şekilde değiştirmek için bkz. [IP adreslerini ekleme, değiştirme veya kaldırma](virtual-network-network-interface-addresses.md). Genel IP adreslerinin nominal bir [ücreti](https://azure.microsoft.com/pricing/details/ip-addresses)vardır ve abonelik başına KULLANABILECEĞINIZ genel IP adresi sayısı için bir [sınır](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) vardır.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

Aşağıdaki adımları yerel bilgisayarınızdan veya Azure Cloud Shell kullanarak tamamlayabilirsiniz. Yerel bilgisayarınızı kullanmak için [Azure PowerShell yüklü](/powershell/azure/install-az-ps?toc=%2fazure%2fvirtual-network%2ftoc.json)olduğundan emin olun. Azure Cloud Shell kullanmak için, takip eden herhangi bir komut kutusunun sağ üst köşesinde bulunan **deneyin** ' i seçin. Cloud Shell sizi Azure 'da oturum açar.

1. Cloud Shell kullanılıyorsa adım 2 ' ye atlayın. Bir komut oturumu açın ve ile Azure 'da oturum açın `Connect-AzAccount` .
2. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) komutuyla bir kaynak grubu oluşturun. Aşağıdaki örnek, Doğu ABD Azure bölgesinde bir kaynak grubu oluşturur:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myResourceGroup -Location EastUS
   ```

3. [New-AzVM](/powershell/module/az.Compute/New-azVM) komutuyla bir sanal makine oluşturun. `-AllocationMethod "Static"`Seçeneği, sanal makineye statik bir genel IP adresi atar. Aşağıdaki örnek, *Mypublicıpaddress* adlı statik, temel SKU genel IP adresine sahip bir Windows Server sanal makinesi oluşturur. İstendiğinde, sanal makine için oturum açma kimlik bilgileri olarak kullanılacak bir Kullanıcı adı ve parola girin:

   ```azurepowershell-interactive
   New-AzVm `
     -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -PublicIpAddressName "myPublicIpAddress" `
     -AllocationMethod "Static"
   ```

   Genel IP adresi standart bir SKU olmalıdır, [Genel BIR IP adresi oluşturmanız](virtual-network-public-ip-address.md#create-a-public-ip-address), [bir ağ arabirimi OLUŞTURMANıZ](virtual-network-network-interface.md#create-a-network-interface), [genel IP adresini ağ arabirimine atamanız](virtual-network-network-interface-addresses.md#add-ip-addresses)ve sonra ayrı adımlarda [ağ arabirimine sahip bir sanal makine oluşturmanız](virtual-network-network-interface-vm.md#add-existing-network-interfaces-to-a-new-vm)gerekir. [Genel IP adresi SKU 'ları](./public-ip-addresses.md#sku)hakkında daha fazla bilgi edinin. Sanal makine, genel bir Azure Load Balancer arka uç havuzuna eklenecektir, sanal makinenin genel IP adresi SKU 'su, yük dengeleyicinin genel IP adresi SKU 'SU ile aynı olmalıdır. Ayrıntılar için bkz. [Azure Load Balancer](../load-balancer/skus.md).

4. Atanan genel IP adresini görüntüleyin ve [Get-Azpublicıpaddress](/powershell/module/az.network/get-azpublicipaddress)ile statik bir adres olarak oluşturulduğunu doğrulayın:

   ```azurepowershell-interactive
   Get-AzPublicIpAddress `
     -ResourceGroupName "myResourceGroup" `
     -Name "myPublicIpAddress" `
     | Select "IpAddress", "PublicIpAllocationMethod" `
     | Format-Table
   ```

   Azure, içinde sanal makineyi oluşturduğunuz bölgede kullanılan adreslerden ortak bir IP adresi atamıştır. Azure [Genel](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [Çin](https://www.microsoft.com/download/details.aspx?id=57062) ve [Almanya](https://www.microsoft.com/download/details.aspx?id=57064) bulutları için bu aralıkların (ön ekler) listesini indirebilirsiniz.

> [!WARNING]
> Sanal makinenin işletim sistemi içindeki IP adresi ayarlarını değiştirmeyin. İşletim sistemi, Azure genel IP adreslerinden oluşan farkında değildir. İşletim sistemine özel IP adresi ayarları ekleyebilse de, gerekmedikçe, [bir işletim sistemine özel bır IP adresi ekleme](virtual-network-network-interface-addresses.md#private)' yi bulana kadar yapmamasını öneririz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanarak kaynak grubunu ve içerdiği tüm kaynakları kaldırabilirsiniz:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Sonraki adımlar

- Azure 'da [genel IP adresleri](./public-ip-addresses.md#public-ip-addresses) hakkında daha fazla bilgi edinin
- Tüm [genel IP adresi ayarları](virtual-network-public-ip-address.md#create-a-public-ip-address) hakkında daha fazla bilgi edinin
- [Özel IP adresleri](./private-ip-addresses.md) hakkında daha fazla bilgi edinin ve bir Azure sanal makinesine [STATIK bir özel IP adresi](virtual-network-network-interface-addresses.md#add-ip-addresses) atama
- [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ve [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sanal makineleri oluşturma hakkında daha fazla bilgi edinin
