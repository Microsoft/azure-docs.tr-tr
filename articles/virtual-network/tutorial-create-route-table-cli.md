---
title: Yönlendirme ağ trafiği-Azure CLı | Microsoft Docs
description: Bu makalede, Azure CLı kullanarak bir yönlendirme tablosu ile ağ trafiğini yönlendirmeyi öğrenin.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2ff643c39820fa529c8678c7a36881dd25da354c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762506"
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Azure CLı kullanarak bir yönlendirme tablosu ile ağ trafiğini yönlendirme

Azure varsayılan olarak bir sanal ağ içindeki tüm alt ağlar arasında gerçekleşen trafiği otomatik olarak yönlendirir. Azure’ın varsayılan yönlendirmesini geçersiz kılmak için kendi yönlendirmelerinizi oluşturabilirsiniz. Örneğin, bir ağ sanal gereci üzerinden alt ağlar arasındaki trafiği yönlendirmek isteyebilirsiniz. Bu makalede şunları öğreneceksiniz:

* Yönlendirme tablosu oluşturma
* Yönlendirme oluşturma
* Birden fazla alt ağa sahip bir sanal ağ oluşturma
* Yönlendirme tablosunu bir alt ağ ile ilişkilendirme
* Trafiği yönlendiren bir NVA oluşturma
* Sanal makineleri (VM) farklı alt ağlara dağıtma
* NVA aracılığıyla trafiği bir alt ağdan başka birine yönlendirme

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Bu makale, Azure CLı 'nin sürüm 2.0.28 veya üstünü gerektirir. Azure Cloud Shell kullanılıyorsa, en son sürüm zaten yüklüdür.

## <a name="create-a-route-table"></a>Yönlendirme tablosu oluşturma

Bir rota tablosu oluşturabilmeniz için önce Bu makalede oluşturulan tüm kaynaklar için [az Group Create](/cli/azure/group) ile bir kaynak grubu oluşturun. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
```

[Az Network Route-Table Create](/cli/azure/network/route-table#az_network_route_table_create)komutuyla bir yol tablosu oluşturun. Aşağıdaki örnek *Myroutetablepublic* adlı bir yol tablosu oluşturur. 

```azurecli-interactive
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Yönlendirme oluşturma

Rota tablosunda [az Network Route-Table Route Create](/cli/azure/network/route-table/route#az_network_route_table_route_create)komutuyla bir yol oluşturun. 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
```

## <a name="associate-a-route-table-to-a-subnet"></a>Yönlendirme tablosunu bir alt ağ ile ilişkilendirme

Bir rota tablosunu bir alt ağ ile ilişkilendirebilmeniz için önce bir sanal ağ ve alt ağ oluşturmanız gerekir. [Az Network VNET Create](/cli/azure/network/vnet)komutuyla bir alt ağa sahip bir sanal ağ oluşturun.

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

[Az Network VNET subnet Create](/cli/azure/network/vnet/subnet)ile iki ek alt ağ oluşturun.

```azurecli-interactive
# Create a private subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24

# Create a DMZ subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name DMZ \
  --address-prefix 10.0.2.0/24
```

*Myroutetablepublic* yol tablosunu [az Network VNET subnet Update](/cli/azure/network/vnet/subnet)ile *ortak* alt ağ ile ilişkilendirin.

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

## <a name="create-an-nva"></a>NVA oluşturma

NVA; yönlendirme, güvenlik duvarı oluşturma veya WAN iyileştirmesi gibi ağ işlevlerini gerçekleştiren bir VM'dir.

[Az VM Create](/cli/azure/vm)komutuyla *DMZ* alt ağında bir NVA oluşturun. Bir VM oluşturduğunuzda, Azure varsayılan olarak VM 'ye genel bir IP adresi oluşturur ve atar. `--public-ip-address ""`Parametresi, VM 'nin internet 'ten bağlanması gerektiğinden, Azure 'un VM 'ye Genel BIR IP adresi oluşturmasını ve atamasını sağlar. SSH anahtarları, varsayılan anahtar konumunda zaten mevcut değilse komut bunları oluşturur. Belirli bir anahtar kümesini kullanmak için `--ssh-key-value` seçeneğini kullanın.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

Sanal makinenin oluşturulması birkaç dakika sürer. Azure VM 'yi oluşturmayı bitirene ve VM hakkında çıkış döndürünceye kadar sonraki adıma devam etmez. 

Bir ağ arabiriminin kendi IP adresini hedeflemeden kendisine gönderilen ağ trafiğini iletebilmesi için, ağ arabiriminde IP iletme özelliğinin etkinleştirilmiş olması gerekir. [Az Network Nic Update](/cli/azure/network/nic)ile ağ ARABIRIMI için IP iletmeyi etkinleştirin.

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

VM içindeki işletim sistemi veya VM içinde çalışan bir uygulama da ağ trafiğini iletebilmelidir. [Az VM Extension set](/cli/azure/vm/extension)ile VM 'nin işletim SISTEMI içinde IP iletmeyi etkinleştirin:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```

Komutun yürütülmesi bir dakika kadar sürebilir.

## <a name="create-virtual-machines"></a>Sanal makineler oluşturma

*Genel* alt ağdan gelen trafiğin, sonraki bir adımda NVA aracılığıyla *özel* alt ağa yönlendirildiğini doğrulayabilmek Için sanal ağda iki VM oluşturun. 

[Az VM Create](/cli/azure/vm)komutuyla *ortak* alt ağda bir VM oluşturun. Parametresi, bir `--no-wait` sonraki komuta devam edebilmeniz Için Azure 'un arka planda komutu yürütmesine olanak sağlar. Bu makaleyi kolaylaştırmak için bir parola kullanılır. Anahtarlar genellikle üretim dağıtımlarında kullanılır. Anahtarları kullanıyorsanız, SSH Aracısı iletmeyi da yapılandırmanız gerekir. Daha fazla bilgi için SSH istemcinizin belgelerine bakın. `<replace-with-your-password>`Aşağıdaki komutta yerine, seçtiğiniz parolayla değiştirin.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

*Özel* alt ağda bir VM oluşturun.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```

Sanal makinenin oluşturulması birkaç dakika sürer. VM oluşturulduktan sonra Azure CLı, aşağıdaki örneğe benzer bilgiler gösterir: 

```output
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPrivate",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

**publicIpAddress** değerini not alın. Bu adres, sonraki bir adımda sanal makineye internet 'ten erişmek için kullanılır.

## <a name="route-traffic-through-an-nva"></a>Trafiği NVA üzerinden yönlendirme

*MyVmPrivate* VM Ile bir SSH oturumu oluşturmak için aşağıdaki komutu kullanın. *\<publicIpAddress>* Sanal makinenizin genel IP adresi ile değiştirin. Yukarıdaki örnekte, IP adresi *13.90.242.231*' dir.

```bash
ssh azureuser@<publicIpAddress>
```

Parola istendiğinde, [sanal makineler oluştur](#create-virtual-machines)bölümünde seçtiğiniz parolayı girin.

İzleme yolunu *myVmPrivate* VM 'ye yüklemek için aşağıdaki komutu kullanın:

```bash
sudo apt-get install traceroute
```

*MyVmPublic* VM 'sine yönelik ağ trafiğine yönelik yönlendirmeyi test etmek için aşağıdaki komutu kullanın *myVmPrivate* VM.

```bash
traceroute myVmPublic
```

Yanıt aşağıdaki örneğe benzer:

```output
traceroute to myVmPublic (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Trafiğin *myVmPrivate* sanal makinesinden *myVmPublic* sanal makinesine doğrudan yönlendirildiğini görebilirsiniz. Azure 'un varsayılan yolları, trafiği doğrudan alt ağlar arasında yönlendirir. 

*MyVmPrivate* VM 'den *MYVMPUBLIC* VM 'ye SSH eklemek için aşağıdaki komutu kullanın:

```bash
ssh azureuser@myVmPublic
```

İzleme yolunu *myVmPublic* VM 'ye yüklemek için aşağıdaki komutu kullanın:

```bash
sudo apt-get install traceroute
```

*MyVmPrivate* VM 'sine yönelik ağ trafiğine yönelik yönlendirmeyi test etmek için aşağıdaki komutu kullanın *myVmPublic* VM.

```bash
traceroute myVmPrivate
```

Yanıt aşağıdaki örneğe benzer:

```output
traceroute to myVmPrivate (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

İlk atlamanın, NVA özel IP adresi olan 10.0.2.4 olduğunu görebilirsiniz. İkinci atlama ise *myVmPrivate* sanal makinesinin özel IP adresi olan 10.0.1.4’tür. *myRouteTablePublic* yönlendirme tablosuna eklenip *Genel* alt ağ ile ilişkilendirilen yönlendirme, Azure’un trafiği doğrudan *Özel* alt ağına yönlendirmek yerine NVA aracılığıyla yönlendirmesine neden olmuştur.

SSH oturumlarını hem *myVmPublic* hem de *myVmPrivate* VM 'lerine kapatın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [az Group Delete](/cli/azure/group) ' i kullanarak kaynak grubunu ve içerdiği tüm kaynakları kaldırın.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede bir rota tablosu oluşturdunuz ve bir alt ağ ile ilişkilendirdiyseniz. Bir genel alt ağdan özel alt ağa trafiği yönlendiren basit bir NVA oluşturdunuz. [Azure Market](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking)’ten güvenlik duvarı ve WAN iyileştirme gibi ağ işlevleri gerçekleştiren, önceden yapılandırılmış çeşitli NVA’lar dağıtın. Yönlendirme hakkında daha fazla bilgi için bkz. [Yönlendirmeye genel bakış](virtual-networks-udr-overview.md) ve [Yönlendirme tablosunu yönetme](manage-route-table.md).

Bir sanal ağ içinde çok sayıda Azure kaynağına dağıtabilmenize karşın, bazı Azure PaaS hizmetlerinin kaynakları bir sanal ağa dağıtılamaz. Yine de, bazı Azure PaaS hizmetlerinin kaynaklarına erişimi yalnızca bir sanal ağ alt ağından gelecek trafikle kısıtlayabilirsiniz. Nasıl yapılacağını öğrenmek için bkz. [PaaS kaynaklarına ağ erişimini kısıtlama](tutorial-restrict-network-access-to-resources-cli.md).
