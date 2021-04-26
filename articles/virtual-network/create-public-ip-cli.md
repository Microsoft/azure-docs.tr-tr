---
title: Genel IP oluşturma-Azure CLı
description: Azure CLı kullanarak genel IP oluşturmayı öğrenin
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: ff0dbf31f6f428b23e00f9366d55703416847b90
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767700"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-cli"></a>Hızlı başlangıç: Azure CLı kullanarak genel IP adresi oluşturma

Bu makalede, Azure CLı kullanarak genel IP adresi kaynağı oluşturma konusu gösterilmektedir. Bu kaynakların hangi kaynaklarla ilişkilendiribileceği hakkında daha fazla bilgi için, temel ve standart SKU arasındaki fark ve diğer ilgili bilgiler için bkz. [genel IP adresleri](./public-ip-addresses.md).  Bu örnekte yalnızca IPv4 adreslerine odaklanacağız; IPv6 adresleri hakkında daha fazla bilgi için bkz. [Azure VNET Için IPv6](./ipv6-overview.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.0.28 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

**Eastus2** konumunda, [az Group Create](/cli/azure/group#az_group_create) **myresourcegroup** adlı bir kaynak grubu oluşturun.

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-public-ip"></a>Genel IP oluştur

---
# <a name="standard-sku---using-zones"></a>[**Standart SKU-bölgeleri kullanma**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>Aşağıdaki komut API sürüm 2020-08-01 veya üzeri için geçerlidir.  Kullanılmakta olan API sürümü hakkında daha fazla bilgi için lütfen [kaynak sağlayıcıları ve türleri](../azure-resource-manager/management/resource-providers-and-types.md)inceleyin.

**Myresourcegroup** Içinde **Mystandardzrpublicıp** adlı standart bölge-yedekli genel IP adresi oluşturmak için [az Network public-ip Create](/cli/azure/network/public-ip#az_network_public_ip_create) komutunu kullanın.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --sku Standard
    --zone 1 2 3
```
> [!IMPORTANT]
> API 'nin 2020-08-01 'den eski sürümleri için, bölgesel olarak yedekli bir IP adresi oluşturmak üzere bölge parametresi belirtmeden yukarıdaki komutu çalıştırın. 
>

**Myresourcegroup** Içinde **Mystandardzonalpublicıp** adlı bölge 2 standart bir genel IP adresi oluşturmak için aşağıdaki komutu kullanın:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --sku Standard \
    --zone 2
```

Bölgeler için yukarıdaki seçeneklerin yalnızca [kullanılabilirlik alanları](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)bölgelerinde geçerli seçimler olduğunu unutmayın.

# <a name="standard-sku---no-zones"></a>[**Standart SKU-bölge yok**](#tab/option-create-public-ip-standard)

>[!NOTE]
>Aşağıdaki komut API sürüm 2020-08-01 veya üzeri için geçerlidir.  Kullanılmakta olan API sürümü hakkında daha fazla bilgi için lütfen [kaynak sağlayıcıları ve türleri](../azure-resource-manager/management/resource-providers-and-types.md)inceleyin.

**Myresourcegroup** Içinde **mystandardpublicpublic** adlı bir kaynak olarak standart bir genel IP adresi oluşturmak için [az Network public-ip Create](/cli/azure/network/public-ip#az_network_public_ip_create) komutunu kullanın.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --sku Standard
```
Bu seçim tüm bölgelerde geçerlidir ve [kullanılabilirlik alanları](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)olmayan bölgelerde standart genel IP adresleri için varsayılan seçimdir.

# <a name="basic-sku"></a>[**Temel SKU**](#tab/option-create-public-ip-basic)

**Myresourcegroup** Içinde **mybasicpublicıp** adlı temel bir statik genel IP adresi oluşturmak için [az Network public-ip Create](/cli/azure/network/public-ip#az_network_public_ip_create) komutunu kullanın.  Temel genel IP 'Lerde kullanılabilirlik alanları kavramı yoktur.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Basic \
    --allocation-method Static
```
IP adresinin zaman içinde değiştirilmesi kabul edilebilir ise, ayırma yöntemi ' Dynamic ' olarak değiştirilerek **dinamik** IP ataması seçilebilir.

---

## <a name="additional-information"></a>Ek bilgiler 

Yukarıda listelenen bağımsız değişkenlerle ilgili daha fazla ayrıntı için lütfen bkz. [genel IP adreslerini yönetme](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Sonraki adımlar
- Bir [genel IP adresini bir sanal makineyle](./associate-public-ip-address-vm.md#azure-portal)ilişkilendirin.
- Azure 'da [genel IP adresleri](./public-ip-addresses.md#public-ip-addresses) hakkında daha fazla bilgi edinin.
- Tüm [genel IP adresi ayarları](virtual-network-public-ip-address.md#create-a-public-ip-address)hakkında daha fazla bilgi edinin.
