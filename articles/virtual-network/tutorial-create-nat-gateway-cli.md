---
title: 'Öğretici: NAT Gateway oluşturma-Azure CLı'
titlesuffix: Azure Virtual Network NAT
description: Kullanmaya başlamak için Azure CLı kullanarak bir NAT ağ geçidi oluşturdunuz.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/10/2021
ms.custom: template-tutorial, devx-track-azurecli
ms.openlocfilehash: 60436b8d4a0f338f4ece59ad4cd11c14c9e4c352
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762650"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-cli"></a>Öğretici: Azure CLı kullanarak bir NAT ağ geçidi oluşturma

Bu öğreticide, Azure sanal ağ NAT hizmetini nasıl kullanacağınız gösterilmektedir. Azure 'da bir sanal makine için giden bağlantı sağlamak üzere bir NAT ağ geçidi oluşturacaksınız. 

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Sanal ağ oluşturun.
> * Sanal makine oluşturur.
> * Bir NAT ağ geçidi oluşturun ve sanal ağla ilişkilendirin.
> * Sanal makineye bağlanın ve NAT IP adresini doğrulayın.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu hızlı başlangıç, Azure CLı 'nin sürüm 2.0.28 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

[az group create](/cli/azure/group#az_group_create) ile bir kaynak grubu oluşturun. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

Aşağıdaki örnek **eastus2** konumunda **Myresourcegroupnat** adlı bir kaynak grubu oluşturur:

```azurecli-interactive
  az group create \
    --name myResourceGroupNAT \
    --location eastus2
```

## <a name="create-the-nat-gateway"></a>NAT ağ geçidini oluşturma

Bu bölümde, NAT ağ geçidini ve destekleyici kaynakları oluşturacağız.

### <a name="create-public-ip-address"></a>Ortak IP adresi oluşturma

Internet 'e erişmek için NAT ağ geçidi için bir veya daha fazla genel IP adresi gerekir. **Myresourcegroupnat** Içinde **Mypublicıp** adlı bir genel IP adresi kaynağı oluşturmak için [az Network public-ip Create](/cli/azure/network/public-ip#az_network_public_ip_create) komutunu kullanın. 

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myPublicIP \
    --sku standard \
    --allocation static
```

### <a name="create-nat-gateway-resource"></a>NAT ağ geçidi kaynağı oluşturma

[Az Network NAT Gateway Create](/cli/azure/network/nat#az_network_nat_gateway_create)komutuyla küresel BIR Azure NAT ağ geçidi oluşturun. Bu komutun sonucu **Mtypiklılicıp** genel IP adresini kullanan **mynatgateway** adlı bir ağ geçidi kaynağı oluşturur. Boşta kalma zaman aşımı 10 dakikaya ayarlanır.  

```azurecli-interactive
  az network nat gateway create \
    --resource-group myResourceGroupNAT \
    --name myNATgateway \
    --public-ip-addresses myPublicIP \
    --idle-timeout 10       
  ```

### <a name="create-virtual-network"></a>Sanal ağ oluşturma

Myresourcegroup kaynak grubunda **mysubnet** [az Network VNET Create](/cli/azure/network/vnet#az_network_vnet_create) adlı bir alt ağ ile **myvnet** adlı bir  sanal ağ oluşturun. Sanal ağın IP adresi alanı **10.1.0.0/16**' dır. Sanal ağ içindeki alt ağ **10.1.0.0/24**' dir.

```azurecli-interactive
  az network vnet create \
    --resource-group myResourceGroupNAT \
    --location eastus2 \
    --name myVnet \
    --address-prefix 10.1.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 10.1.0.0/24
```

### <a name="create-bastion-host"></a>Savunma Konağı oluştur

Sanal makineye erişmek için **Mybastionhost** adlı bir Azure savunma ana bilgisayarı oluşturun. 

Bir Azure savunma alt ağı oluşturmak için [az Network VNET subnet Create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create) kullanın.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroupNAT \
    --name AzureBastionSubnet \
    --vnet-name myVNet \
    --address-prefixes 10.1.1.0/24
```

[Az Network public-IP Create](/cli/azure/network/public-ip#az_network_public_ip_create)komutuyla savunma konağı için genel bir IP adresi oluşturun. 

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroupNAT \
    --name myBastionIP \
    --sku Standard
```

Savunma konağını oluşturmak için [az Network tasyon oluştur](/cli/azure/network/bastion#az_network_bastion_create) seçeneğini kullanın. 

```azurecli-interactive
az network bastion create \
    --resource-group myResourceGroupNAT \
    --name myBastionHost \
    --public-ip-address myBastionIP \
    --vnet-name myVNet \
    --location eastus2
```

### <a name="configure-nat-service-for-source-subnet"></a>Kaynak alt ağ için NAT hizmetini yapılandırma

**Myvnet** sanal ağındaki **mysubnet** kaynak alt ağını, [az Network VNET alt ağ güncelleştirmesiyle](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update) **MYNATGATEWAY** adlı belirli bir NAT Gateway kaynağını kullanacak şekilde yapılandıracağız. Bu komut, belirtilen alt ağda NAT hizmetini etkinleştirir.

```azurecli-interactive
  az network vnet subnet update \
    --resource-group myResourceGroupNAT \
    --vnet-name myVnet \
    --name mySubnet \
    --nat-gateway myNATgateway
```

Internet hedeflerine giden tüm trafik artık NAT ağ geçidini kullanıyor.  UDR 'yi yapılandırmak gerekli değildir.


## <a name="virtual-machine"></a>Sanal makine

Bu bölümde, giden bağlantının genel IP adresini doğrulamak üzere NAT ağ geçidini test etmek için bir sanal makine oluşturacaksınız.

[Az VM Create](/cli/azure/vm#az_vm_create)ile sanal makineyi oluşturun.

```azurecli-interactive
az vm create \
    --name myVM \
    --resource-group myResourceGroupNAT  \
    --admin-username azureuser \
    --image win2019datacenter \
    --public-ip-address "" \
    --subnet mySubnet \
    --vnet-name myVNet
```

Bir sonraki bölüme geçmeden önce sanal makine oluşturma işleminin tamamlanmasını bekleyin.

## <a name="test-nat-gateway"></a>Test NAT ağ geçidi

Bu bölümde, NAT ağ geçidini test edeceğiz. Önce NAT ağ geçidinin genel IP 'sini keşfedeceğiz. Ardından, test sanal makinesine bağlanır ve NAT ağ geçidi üzerinden giden bağlantıyı doğrulayacağız.
    
1. [Azure portalda](https://portal.azure.com) oturum açma

1. **Genel bakış** ekranında NAT ağ GEÇIDININ genel IP adresini bulun. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından **mypublicıp**' yi seçin.

2. Genel IP adresini bir yere getirin:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/find-public-ip.png" alt-text="NAT ağ geçidinin genel IP adresini bulma" border="true":::

3. Sol taraftaki menüden **tüm hizmetler** ' i seçin, **tüm kaynaklar**' ı seçin ve ardından kaynaklar listesinden **myresourcegroupnat** kaynak grubunda bulunan **myvm** ' yi seçin.

4. **Genel bakış** sayfasında **Bağlan** **' ı ve** sonra da ' yi seçin.

5. Mavi **kullanımı** savunma düğmesini seçin.

6. VM oluşturma sırasında girilen kullanıcı adını ve parolayı girin.

7. **Mytestvm**'de **Internet Explorer** 'ı açın.

8. **https://whatsmyip.com** Adres çubuğuna yazın.

9. Görünen IP adresinin, önceki adımda not ettiğiniz NAT ağ geçidi adresiyle eşleştiğini doğrulayın:

    :::image type="content" source="./media/tutorial-create-nat-gateway-portal/my-ip.png" alt-text="Dış giden IP 'yi gösteren Internet Explorer" border="true":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam edemeyecekiz, sanal ağ, sanal makine ve NAT ağ geçidini aşağıdaki adımlarla silin:

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

## <a name="next-steps"></a>Sonraki adımlar

Azure sanal ağ NAT hakkında daha fazla bilgi için bkz.:
> [!div class="nextstepaction"]
> [Sanal ağ NAT genel bakış](nat-overview.md)
