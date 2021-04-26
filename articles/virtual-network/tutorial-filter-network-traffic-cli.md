---
title: Ağ trafiğini filtreleme-Azure CLı | Microsoft Docs
description: Bu makalede, Azure CLı kullanarak bir ağ güvenlik grubu ile ağ trafiğini bir alt ağa nasıl filtreleyeceğinizi öğreneceksiniz.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 57657637afd9e3e527942fb262a085fa4b8b053e
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106066644"
---
# <a name="filter-network-traffic-with-a-network-security-group-using-the-azure-cli"></a>Azure CLı kullanarak ağ trafiğini ağ güvenlik grubuyla filtreleme

Bir sanal ağ alt ağına gelen ve sanal ağ alt ağından giden ağ trafiğini, bir ağ güvenlik grubu ile filtreleyebilirsiniz. Ağ güvenlik grupları, ağ trafiğini IP adresi, bağlantı noktası ve protokole göre filtreleyen güvenlik kuralları içerir. Güvenlik kuralları bir alt ağda dağıtılmış kaynaklara uygulanır. Bu makalede şunları öğreneceksiniz:

* Ağ güvenlik grubu ve güvenlik kuralları oluşturma
* Bir sanal ağ oluşturma ve ağ güvenlik grubunu alt ağ ile ilişkilendirme
* Sanal makineleri (VM) bir alt ağa dağıtma
* Trafik filtrelerini test etme

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.0.28 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

Bir ağ güvenlik grubu, güvenlik kuralları içerir. Güvenlik kuralları, bir kaynak ve hedefi belirtir. Kaynaklar ve hedefler, uygulama güvenlik grupları olabilir.

### <a name="create-application-security-groups"></a>Uygulama güvenlik grupları oluşturma

Önce Bu makalede oluşturulan tüm kaynaklar için [az Group Create](/cli/azure/group)komutuyla bir kaynak grubu oluşturun. Aşağıdaki örnekte *eastus* konumunda bir kaynak grubu oluşturulmaktadır: 

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

[Az Network ASG Create](/cli/azure/network/asg)komutuyla bir uygulama güvenlik grubu oluşturun. Uygulama güvenlik grubu, benzer bağlantı noktası filtreleme gereksinimlerine sahip sunucuları gruplandırmanızı sağlar. Aşağıdaki örnek iki uygulama güvenlik grubu oluşturur.

```azurecli-interactive
az network asg create \
  --resource-group myResourceGroup \
  --name myAsgWebServers \
  --location eastus

az network asg create \
  --resource-group myResourceGroup \
  --name myAsgMgmtServers \
  --location eastus
```

### <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

[Az Network NSG Create](/cli/azure/network/nsg)komutuyla bir ağ güvenlik grubu oluşturun. Aşağıdaki örnek *myNsg* adlı bir ağ güvenlik grubu oluşturur: 

```azurecli-interactive 
# Create a network security group
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsg
```

### <a name="create-security-rules"></a>Güvenlik kuralları oluşturma

[Az Network NSG Rule Create](/cli/azure/network/nsg/rule)ile bir güvenlik kuralı oluşturun. Aşağıdaki örnek, internetten gelen trafiğin 80 ve 443 numaralı bağlantı noktaları üzerinden *myWebServers* uygulama güvenlik grubuna gitmesine izin veren bir kural oluşturur:

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-Web-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgWebServers" \
  --destination-port-range 80 443
```

Aşağıdaki örnek, Internet 'ten gelen trafiğin 22 numaralı bağlantı noktası üzerinden *Mymgmtservers* uygulama güvenlik grubuna erişmesine izin veren bir kural oluşturur:

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsg \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 110 \
  --source-address-prefix Internet \
  --source-port-range "*" \
  --destination-asgs "myAsgMgmtServers" \
  --destination-port-range 22
```

Bu makalede, SSH (bağlantı noktası 22), *Myasgmgmtservers* sanal makinesi için internet 'e açıktır. Üretim ortamları için 22 numaralı bağlantı noktasını Internet 'e sunmak yerine, bir [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) veya [özel](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ağ bağlantısı kullanarak yönetmek istediğiniz Azure kaynaklarına bağlanmanız önerilir.

## <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

[az network vnet create](/cli/azure/network/vnet) komutu ile bir sanal ağ oluşturun. Aşağıdaki örnek *myVirtualNetwork* adlı bir sanal ağ oluşturur:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefixes 10.0.0.0/16
```

[Az Network VNET subnet Create](/cli/azure/network/vnet/subnet)komutuyla bir sanal ağa alt ağ ekleyin. Aşağıdaki örnek, sanal ağa *mySubnet* adlı bir alt ağ ekler ve *myNsg* ağ güvenlik grubunu onunla ilişkilendirir:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name mySubnet \
  --address-prefix 10.0.0.0/24 \
  --network-security-group myNsg
```

## <a name="create-virtual-machines"></a>Sanal makineler oluşturma

Daha sonraki bir adımda trafik filtrelemesini doğrulayabilmek için sanal ağda iki VM oluşturun. 

[az vm create](/cli/azure/vm) ile bir VM oluşturun. Aşağıdaki örnek, web sunucusu olarak görev yapacak bir VM oluşturur. `--asgs myAsgWebServers`Seçeneği, Azure 'un VM Için *Myasgwebservers* uygulama güvenlik grubunun bir üyesi için oluşturduğu ağ arabirimini yapmasına neden olur.

`--nsg ""`Bu seçenek, Azure 'un VM oluşturduğunda Azure tarafından oluşturulan ağ arabirimi için varsayılan bir ağ güvenlik grubu oluşturmasını engellemek üzere belirtilmiştir. Bu makaleyi kolaylaştırmak için bir parola kullanılır. Anahtarlar genellikle üretim dağıtımlarında kullanılır. Anahtarları kullanıyorsanız, diğer adımlar için SSH Aracısı iletmeyi da yapılandırmanız gerekir. Daha fazla bilgi için SSH istemcinizin belgelerine bakın. `<replace-with-your-password>`Aşağıdaki komutta yerine, seçtiğiniz parolayla değiştirin.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgWebServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

Sanal makinenin oluşturulması birkaç dakika sürer. VM oluşturulduktan sonra, aşağıdaki örneğe benzer bir çıktı döndürülür: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmWeb",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

**publicIpAddress** değerini not alın. Bu adres, sonraki bir adımda sanal makineye internet 'ten erişmek için kullanılır.  Yönetim sunucusu olarak görev yapacak bir VM oluşturun:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet mySubnet \
  --nsg "" \
  --asgs myAsgMgmtServers \
  --admin-username azureuser \
  --admin-password $adminPassword
```

Sanal makinenin oluşturulması birkaç dakika sürer. VM oluşturulduktan sonra döndürülen çıktıda **Publicıpaddress** ' i aklınızda bulunur. Bu adres, bir sonraki adımda sanal makineye erişmek için kullanılır. Azure VM oluşturma işlemini tamamlayana kadar sonraki adıma geçmeyin.

## <a name="test-traffic-filters"></a>Trafik filtrelerini test etme

*Myvmmgmt* VM Ile bir SSH oturumu oluşturmak için aşağıdaki komutu kullanın. *\<publicIpAddress>* Sanal makinenizin genel IP adresi ile değiştirin. Yukarıdaki örnekte, IP adresi *13.90.242.231*' dir.

```bash 
ssh azureuser@<publicIpAddress>
```

Bir parola istendiğinde, [VM oluşturma](#create-virtual-machines)bölümünde girdiğiniz parolayı girin.

Bağlantı noktası 22 ' den Internet 'ten gelen ve *Myvmmgmt* sanal makinesine bağlı ağ arabiriminin bulunduğu *Myasgmgmtservers* uygulama güvenlik grubuna izin verildiğinden bağlantı başarılı olur.

*Myvmmgmt* VM 'Sinden *MYVMWEB* VM 'sine SSH için aşağıdaki komutu kullanın:

```bash 
ssh azureuser@myVmWeb
```

Her bir ağ güvenlik grubu içindeki varsayılan güvenlik kuralı bir sanal ağ içindeki tüm IP adresleri arasında tüm bağlantı noktaları üzerinden trafiğe izin verdiği için bağlantı başarılı olur. *Myasgwebservers* güvenlik kuralı Internet 'ten gelen bağlantı noktası 22 ' ye izin vermediğinden, *myvmweb* VM 'sine internet 'ten SSH yapamazsınız.

*Myvmweb* VM 'sine NGINX web sunucusunu yüklemek için aşağıdaki komutları kullanın:

```bash 
# Update package source
sudo apt-get -y update

# Install NGINX
sudo apt-get -y install nginx
```

Varsayılan bir güvenlik kuralı Internet 'e giden tüm trafiğe izin verdiğinden, *Myvmweb* VM 'sinin NGINX 'i alabilmesi için internet 'e giden trafiğe izin verilir. *Myvmweb* SSH oturumundan çıkın, bu, sizi `username@myVmMgmt:~$` *myvmmgmt* sanal makinesi isteminde bırakır. *Myvmweb* VM 'sinden NGINX karşılama ekranını almak için aşağıdaki komutu girin:

```bash
curl myVmWeb
```

*Myvmmgmt* sanal makinesi oturumu kapatma. Azure dışından *Myvmweb* Web sunucusuna erişebildiğinizden emin olmak için `curl <publicIpAddress>` kendi bilgisayarınızdan girin. Bağlantı noktası 80, Internet 'ten *Myvmweb* VM 'sine bağlı ağ arabiriminin bulunduğu *Myasgwebservers* uygulama güvenlik grubuna izin verildiğinden bağlantı başarılı olur.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [az Group Delete](/cli/azure/group) ' i kullanarak kaynak grubunu ve içerdiği tüm kaynakları kaldırın.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede bir ağ güvenlik grubu oluşturdunuz ve bunu bir sanal ağ alt ağıyla ilişkilendirdiyseniz. Ağ güvenlik grupları hakkında daha fazla bilgi edinmek bkz. [Ağ güvenlik grubuna genel bakış](./network-security-groups-overview.md) ve [Ağ güvenlik grubunu yönetme](manage-network-security-group.md).

Azure, varsayılan olarak trafiği alt ağlar arasında yönlendirir. Bunun yerine, alt ağlar arasındaki trafiği, örneğin, güvenlik duvarı olarak görev yapan bir VM aracılığıyla yönlendirmeyi seçebilirsiniz. Nasıl yapılacağını öğrenmek için bkz. [yol tablosu oluşturma](tutorial-create-route-table-cli.md).