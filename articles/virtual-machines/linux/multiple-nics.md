---
title: Azure 'da birden çok NIC ile Linux VM oluşturma
description: Azure CLı veya Kaynak Yöneticisi şablonlarını kullanarak, birden çok NIC 'ye eklenmiş bir Linux VM oluşturma hakkında bilgi edinin.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
ms.assetid: 5d2d04d0-fc62-45fa-88b1-61808a2bc691
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/07/2018
ms.author: cynthn
ms.openlocfilehash: b4ab46a59bd83bf2d1c08e3a238df3c59797f3e7
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035602"
---
# <a name="how-to-create-a-linux-virtual-machine-in-azure-with-multiple-network-interface-cards"></a>Azure 'da birden çok ağ arabirim kartı ile Linux sanal makinesi oluşturma


Bu makalede, Azure CLı ile birden çok NIC ile VM oluşturma hakkında bilgi yer aldığı açıklanır.

## <a name="create-supporting-resources"></a>Destekleyici kaynaklar oluşturma
En son [Azure CLI](/cli/azure/install-az-cli2) 'yı yükleyip [az Login](/cli/azure/reference-index)kullanarak bir Azure hesabında oturum açın.

Aşağıdaki örneklerde, örnek parametre adlarını kendi değerlerinizle değiştirin. *Myresourcegroup*, *Mystorageaccount*ve *myvm*dahil olmak üzere örnek parametre adları.

Öncelikle [az group create](/cli/azure/group) komutuyla bir kaynak grubu oluşturun. Aşağıdaki örnek *eastus* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur:

```azurecli
az group create --name myResourceGroup --location eastus
```

[Az Network VNET Create](/cli/azure/network/vnet)ile sanal ağ oluşturun. Aşağıdaki örnek *Mysubnetön uç*adlı *myvnet* ve subnet adlı bir sanal ağ oluşturur:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 10.0.0.0/16 \
    --subnet-name mySubnetFrontEnd \
    --subnet-prefix 10.0.1.0/24
```

[Az Network VNET subnet Create](/cli/azure/network/vnet/subnet)ile arka uç trafiği için bir alt ağ oluşturun. Aşağıdaki örnek, *Mysubnetarka ucu*adlı bir alt ağ oluşturur:

```azurecli
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnetBackEnd \
    --address-prefix 10.0.2.0/24
```

[Az Network NSG Create](/cli/azure/network/nsg)komutuyla bir ağ güvenlik grubu oluşturun. Aşağıdaki örnek *myNetworkSecurityGroup* adında bir ağ güvenlik grubu oluşturur:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="create-and-configure-multiple-nics"></a>Birden çok NIC oluşturma ve yapılandırma
[Az Network Nic Create](/cli/azure/network/nic)Ile iki NIC oluşturun. Aşağıdaki örnek, her bir alt ağa bağlanan bir NIC ile ağ güvenlik grubu 'na bağlı olan *myNic1* ve *MyNic2*adlı iki NIC oluşturur:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic1 \
    --vnet-name myVnet \
    --subnet mySubnetFrontEnd \
    --network-security-group myNetworkSecurityGroup
az network nic create \
    --resource-group myResourceGroup \
    --name myNic2 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

## <a name="create-a-vm-and-attach-the-nics"></a>VM oluşturma ve NIC 'Leri iliştirme
VM oluşturduğunuzda, `--nics`oluşturduğunuz NIC 'Leri belirtin. VM boyutunu seçerken de dikkatli olmanız gerekir. Bir VM 'ye ekleyebileceğiniz toplam NIC sayısı sınırı vardır. [LINUX VM boyutları](sizes.md)hakkında daha fazla bilgi edinin.

[az vm create](/cli/azure/vm) ile bir VM oluşturun. Aşağıdaki örnek *myVM* adlı bir VM oluşturur:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --size Standard_DS3_v2 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --nics myNic1 myNic2
```

[Konuk IŞLETIM sistemini birden çok NIC Için yapılandırma](#configure-guest-os-for-multiple-nics)bölümündeki adımları tamamlayarak Konuk işletim sistemine yönlendirme tabloları ekleyin.

## <a name="add-a-nic-to-a-vm"></a>VM 'ye bir NIC ekleme
Önceki adımlarda birden çok NIC içeren bir VM oluşturulmuştur. Ayrıca, Azure CLı ile mevcut bir VM 'ye NIC 'ler de ekleyebilirsiniz. Farklı [VM boyutları](sizes.md) değişen sayıda NIC destekler, bu nedenle VM 'nizi uygun şekilde boyutlandırın. Gerekirse, [bir VM 'yi yeniden boyutlandırabilirsiniz](change-vm-size.md).

[Az Network Nic Create](/cli/azure/network/nic)komutuyla başka bir NIC oluşturun. Aşağıdaki örnek, önceki adımlarda oluşturulan arka uç alt ağına ve ağ güvenlik grubuna bağlı *myNic3* ADLı bir NIC oluşturur:

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic3 \
    --vnet-name myVnet \
    --subnet mySubnetBackEnd \
    --network-security-group myNetworkSecurityGroup
```

Mevcut bir VM 'ye bir NIC eklemek için, önce VM 'yi [az VM serbest bırakma](/cli/azure/vm)ile serbest bırakın. Aşağıdaki örnek, *myvm*adlı VM 'yi kaldırır:


```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

NIC ['yi az VM NIC Add](/cli/azure/vm/nic)ile ekleyin. Aşağıdaki örnek *Myvm*'e *myNic3* ekler:

```azurecli
az vm nic add \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

VM 'yi [az VM start](/cli/azure/vm)ile başlatın:

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```

[Konuk IŞLETIM sistemini birden çok NIC Için yapılandırma](#configure-guest-os-for-multiple-nics)bölümündeki adımları tamamlayarak Konuk işletim sistemine yönlendirme tabloları ekleyin.

## <a name="remove-a-nic-from-a-vm"></a>Bir VM 'den NIC kaldırma
Bir NIC 'yi var olan bir VM 'den kaldırmak için ilk olarak VM 'yi [az VM serbest bırakma](/cli/azure/vm)ile serbest bırakın. Aşağıdaki örnek, *myvm*adlı VM 'yi kaldırır:

```azurecli
az vm deallocate --resource-group myResourceGroup --name myVM
```

[Az VM NIC Remove](/cli/azure/vm/nic)ile NIC 'yi kaldırın. Aşağıdaki örnek, *Myvm*'den *myNic3* kaldırır:

```azurecli
az vm nic remove \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --nics myNic3
```

VM 'yi [az VM start](/cli/azure/vm)ile başlatın:

```azurecli
az vm start --resource-group myResourceGroup --name myVM
```


## <a name="create-multiple-nics-using-resource-manager-templates"></a>Kaynak Yöneticisi şablonları kullanarak birden çok NIC oluşturma
Azure Resource Manager şablonlar, ortamınızı tanımlamak için bildirim temelli JSON dosyalarını kullanır. [Azure Resource Manager bir genel bakışı](../../azure-resource-manager/resource-group-overview.md)okuyabilirsiniz. Kaynak Yöneticisi şablonlar, dağıtım sırasında birden çok NIC oluşturma gibi bir kaynağın birden çok örneğini oluşturmak için bir yol sağlar. Oluşturulacak örnek sayısını belirtmek için *Kopyala* ' yı kullanın:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

[ *Kopyayı*kullanarak birden çok örnek oluşturma](../../resource-group-create-multiple.md)hakkında daha fazla bilgi edinin. 

Ayrıca, bir `copyIndex()` kullanarak bir kaynak adına bir sayı ekleyebilirsiniz, bu da `myNic1`, `myNic2`, vb. oluşturmanızı sağlar. Aşağıda dizin değeri ekleme örneği gösterilmektedir:

```json
"name": "[concat('myNic', copyIndex())]", 
```

[Kaynak Yöneticisi şablonları kullanarak birden çok NIC oluşturmaya](../../virtual-network/template-samples.md)ilişkin bir örnek bulabilirsiniz.

[Konuk IŞLETIM sistemini birden çok NIC Için yapılandırma](#configure-guest-os-for-multiple-nics)bölümündeki adımları tamamlayarak Konuk işletim sistemine yönlendirme tabloları ekleyin.

## <a name="configure-guest-os-for-multiple-nics"></a>Konuk işletim sistemini birden çok NIC için yapılandırma

Önceki adımlarda bir sanal ağ ve alt ağ, bağlı NIC 'ler oluşturulup bir VM oluşturulur. SSH trafiğine izin veren bir genel IP adresi ve ağ güvenlik grubu kuralları oluşturulmadı. Konuk işletim sistemini birden çok NIC için yapılandırmak için, uzak bağlantılara izin vermeniz ve komutları VM 'de yerel olarak çalıştırmanız gerekir.

SSH trafiğine izin vermek için [az Network NSG Rule Create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) komutuyla aşağıdaki gibi bir ağ güvenlik grubu kuralı oluşturun:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name allow_ssh \
    --priority 101 \
    --destination-port-ranges 22
```

Az Network [Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) komutunu kullanarak BIR genel IP adresi oluşturun ve [az Network NIC IP-CONFIG güncelleştirmesiyle](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update)ilk NIC 'ye atayın:

```azurecli
az network public-ip create --resource-group myResourceGroup --name myPublicIP

az network nic ip-config update \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --name ipconfig1 \
    --public-ip myPublicIP
```

VM 'nin genel IP adresini görüntülemek için [az VM Show](/cli/azure/vm#az-vm-show) aşağıdaki gibi kullanın:

```azurecli
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Şimdi VM 'nizin genel IP adresi için SSH. Önceki adımda belirtilen varsayılan Kullanıcı adı *azureuser*idi. Kendi Kullanıcı adınızı ve genel IP adresinizi girin:

```bash
ssh azureuser@137.117.58.232
```

İkincil bir ağ arabirimine veya bir ikincil ağ arabirimine göndermek için, her bir ikincil ağ arabirimi için işletim sistemine kalıcı yolları el ile eklemeniz gerekir. Bu makalede, *eth1* ikincil arabirimdir. İşletim sistemine kalıcı yollar eklemeye yönelik yönergeler, farklıdır. Kendi talimatlarınız için belgelere bakın.

Yönlendirme işletim sistemine eklenirken, ağ arabiriminin içinde bulunduğu alt ağ için ağ geçidi adresi *1* ' dir. Örneğin, ağ arabirimine *10.0.2.4*adresi atanmışsa, yol için belirttiğiniz ağ geçidi *10.0.2.1*olur. Arabirimin tüm trafiğinin belirtilen ağ geçidiyle gitmesini istiyorsanız yolun hedefi için belirli bir ağ tanımlayabilir veya *0.0.0.0*hedefini belirtebilirsiniz. Her alt ağ için ağ geçidi sanal ağ tarafından yönetilir.

Bir ikincil arabirim için yolu ekledikten sonra, yolun `route -n`ile yol tablonuzda olduğunu doğrulayın. Aşağıdaki örnek çıktı, bu makaledeki sanal makineye iki ağ arabirimi eklenmiş olan rota tablosu içindir:

```bash
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.1.1        0.0.0.0         UG    0      0        0 eth0
0.0.0.0         10.0.2.1        0.0.0.0         UG    0      0        0 eth1
10.0.1.0        0.0.0.0         255.255.255.0   U     0      0        0 eth0
10.0.2.0        0.0.0.0         255.255.255.0   U     0      0        0 eth1
168.63.129.16   10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
169.254.169.254 10.0.1.1        255.255.255.255 UGH   0      0        0 eth0
```

Yeniden başlatma sonrasında rota tablonuzu yeniden denetleyerek, eklediğiniz yolun yeniden başlatmalar arasında devam ettiğinden emin olun. Bağlantıyı test etmek için, örneğin *eth1* bir ikincil ağ arabiriminin adı olduğu gibi, aşağıdaki komutu girebilirsiniz:

```bash
ping bing.com -c 4 -I eth1
```

## <a name="next-steps"></a>Sonraki adımlar
Birden çok NIC ile VM oluşturmaya çalışırken [LINUX VM boyutlarını](sizes.md) gözden geçirin. Her VM boyutunun desteklediği en fazla NIC sayısına dikkat edin.

VM 'lerinizi daha güvenli hale getirmek için tam zamanında VM erişimi kullanın. Bu özellik, gerektiğinde SSH trafiği için ağ güvenlik grubu kurallarını ve tanımlı bir süre için açar. Daha fazla bilgi için bkz. [Tam zamanında özelliğini kullanarak sanal makine erişimini yönetme](../../security-center/security-center-just-in-time.md).
