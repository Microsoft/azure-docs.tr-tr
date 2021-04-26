---
title: Statik genel IP adresi ile VM oluşturma-Azure CLı | Microsoft Docs
description: Azure komut satırı arabirimi 'ni (CLı) kullanarak statik genel IP adresi ile VM oluşturma hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: 55bc21b0-2a45-4943-a5e7-8d785d0d015c
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: kumud
ms.openlocfilehash: f0f61cc4ef02033a2c21ce5acde68caea483e743
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790140"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-cli"></a>Azure CLı kullanarak statik genel IP adresi ile sanal makine oluşturma

Statik bir genel IP adresine sahip bir sanal makine oluşturabilirsiniz. Genel bir IP adresi, internet 'ten bir sanal makineyle iletişim kurmanızı sağlar. Adresin hiçbir şekilde değişmeyeceğinden emin olmak için dinamik bir adres yerine statik bir genel IP adresi atayın. [Statik genel IP adresleri](./public-ip-addresses.md#allocation-method)hakkında daha fazla bilgi edinin. Var olan bir sanal makineye atanan bir genel IP adresini dinamik veya özel IP adresleriyle çalışacak şekilde değiştirmek için bkz. [IP adreslerini ekleme, değiştirme veya kaldırma](virtual-network-network-interface-addresses.md). Genel IP adreslerinin nominal bir [ücreti](https://azure.microsoft.com/pricing/details/ip-addresses)vardır ve abonelik başına KULLANABILECEĞINIZ genel IP adresi sayısı için bir [sınır](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) vardır.

## <a name="create-a-virtual-machine"></a>Sanal makine oluşturma

Aşağıdaki adımları yerel bilgisayarınızdan veya Azure Cloud Shell kullanarak tamamlayabilirsiniz. Yerel bilgisayarınızı kullanmak için [Azure CLI 'nin yüklü](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)olduğundan emin olun. Azure Cloud Shell kullanmak için, takip eden herhangi bir komut kutusunun sağ üst köşesinde bulunan **deneyin** ' i seçin. Cloud Shell sizi Azure 'da oturum açar.

1. Cloud Shell kullanılıyorsa adım 2 ' ye atlayın. Bir komut oturumu açın ve ile Azure 'da oturum açın `az login` .
2. [az group create](/cli/azure/group#az_group_create) komutuyla bir kaynak grubu oluşturun. Aşağıdaki örnek, Doğu ABD Azure bölgesinde bir kaynak grubu oluşturur:

   ```azurecli-interactive
   az group create --name myResourceGroup --location eastus
   ```

3. [az vm create](/cli/azure/vm#az_vm_create) komutuyla bir sanal makine oluşturun. `--public-ip-address-allocation=static`Seçeneği, sanal makineye statik bir genel IP adresi atar. Aşağıdaki örnek, *Mypublicıpaddress* adlı statik, temel SKU genel IP adresi ile birlikte bir Ubuntu sanal makinesi oluşturur:

   ```azurecli-interactive
   az vm create \
     --resource-group myResourceGroup \
     --name myVM \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys \
     --public-ip-address myPublicIpAddress \
     --public-ip-address-allocation static
   ```

   Genel IP adresi standart bir SKU olmalıdır ve `--public-ip-sku Standard` önceki komuta ekleyin. [Genel IP adresi SKU 'ları](./public-ip-addresses.md#sku)hakkında daha fazla bilgi edinin. Sanal makine, genel bir Azure Load Balancer arka uç havuzuna eklenecektir, sanal makinenin genel IP adresi SKU 'su, yük dengeleyicinin genel IP adresi SKU 'SU ile aynı olmalıdır. Ayrıntılar için bkz. [Azure Load Balancer](../load-balancer/skus.md).

4. Atanan genel IP adresini görüntüleyin ve statik, temel bir SKU adresi olarak oluşturulduğunu, [az Network public-IP Show](/cli/azure/network/public-ip#az_network_public_ip_show)komutuyla doğrulayın:

   ```azurecli-interactive
   az network public-ip show \
     --resource-group myResourceGroup \
     --name myPublicIpAddress \
     --query [ipAddress,publicIpAllocationMethod,sku] \
     --output table
   ```

   Azure, içinde sanal makineyi oluşturduğunuz bölgede kullanılan adreslerden ortak bir IP adresi atamıştır. Azure [Genel](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [Çin](https://www.microsoft.com/download/details.aspx?id=57062) ve [Almanya](https://www.microsoft.com/download/details.aspx?id=57064) bulutları için bu aralıkların (ön ekler) listesini indirebilirsiniz.

> [!WARNING]
> Sanal makinenin işletim sistemi içindeki IP adresi ayarlarını değiştirmeyin. İşletim sistemi, Azure genel IP adreslerinden oluşan farkında değildir. İşletim sistemine özel IP adresi ayarları ekleyebilse de, gerekmedikçe, [bir işletim sistemine özel bır IP adresi ekleme](virtual-network-network-interface-addresses.md#private)' yi bulana kadar yapmamasını öneririz.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [az group delete](/cli/azure/group#az_group_delete) komutunu kullanarak kaynak grubunu ve içerdiği tüm kaynakları kaldırabilirsiniz:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Sonraki adımlar

- Azure 'da [genel IP adresleri](./public-ip-addresses.md#public-ip-addresses) hakkında daha fazla bilgi edinin
- Tüm [genel IP adresi ayarları](virtual-network-public-ip-address.md#create-a-public-ip-address) hakkında daha fazla bilgi edinin
- [Özel IP adresleri](./private-ip-addresses.md) hakkında daha fazla bilgi edinin ve bir Azure sanal makinesine [STATIK bir özel IP adresi](virtual-network-network-interface-addresses.md#add-ip-addresses) atama
- [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ve [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) sanal makineleri oluşturma hakkında daha fazla bilgi edinin
