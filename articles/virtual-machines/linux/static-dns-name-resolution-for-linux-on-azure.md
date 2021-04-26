---
title: Azure CLı ile VM adı çözümlemesi için dahili DNS kullanma
description: Azure CLı ile Azure 'da sanal ağ arabirim kartları oluşturma ve VM ad çözümlemesi için dahili DNS kullanma.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/16/2017
ms.author: cynthn
ms.openlocfilehash: 3d68ac7aa9927e62011c58b17139d7232ce4a10c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98200762"
---
# <a name="create-virtual-network-interface-cards-and-use-internal-dns-for-vm-name-resolution-on-azure"></a>Azure 'da sanal ağ arabirim kartları oluşturma ve VM ad çözümlemesi için dahili DNS kullanma

Bu makalede, Azure CLı ile sanal ağ arabirim kartları (vNIC 'Ler) ve DNS etiket adları kullanılarak Linux VM 'Ler için statik iç DNS adları ayarlama gösterilmektedir. Statik DNS adları, bu belge veya git sunucusu için kullanılan bir Jenkins derleme sunucusu gibi kalıcı altyapı hizmetleri için kullanılır.

Gereksinimler şunlardır:

* [bir Azure hesabı](https://azure.microsoft.com/pricing/free-trial/)
* [SSH ortak ve özel anahtar dosyaları](mac-create-ssh-keys.md)

## <a name="quick-commands"></a>Hızlı komutlar
Görevi hızlı bir şekilde gerçekleştirmeniz gerekirse, aşağıdaki bölümde gereken komutların ayrıntıları verilmiştir. Her adım için daha ayrıntılı bilgi ve bağlam, belgenin geri kalanında [buradan başlayarak](#detailed-walkthrough)bulunabilir. Bu adımları gerçekleştirmek için, en son [Azure CLI](/cli/azure/install-az-cli2) 'nın yüklü olması ve [az oturum açma](/cli/azure/reference-index)kullanarak bir Azure hesabında oturum açmış olması gerekir.

Ön gereksinimler: kaynak grubu, sanal ağ ve alt ağ, SSH gelen ağ güvenlik grubu.

### <a name="create-a-virtual-network-interface-card-with-a-static-internal-dns-name"></a>Statik iç DNS adına sahip bir sanal ağ arabirim kartı oluşturun
[Az Network Nic Create](/cli/azure/network/nic)Ile vNIC 'yi oluşturun. `--internal-dns-name`CLI bayrağı, sanal ağ arabirim kartı (vNIC) için STATIK DNS adı sağlayan DNS etiketinin ayarlanmasına yöneliktir. Aşağıdaki örnek adlı bir vNIC oluşturur `myNic` , bunu `myVnet` sanal ağa bağlar ve adlı BIR iç DNS ad kaydı oluşturur `jenkins` :

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

### <a name="deploy-a-vm-and-connect-the-vnic"></a>VM dağıtma ve sanal NIC 'yi bağlama
[az vm create](/cli/azure/vm) ile bir VM oluşturun. `--nics`Bayrak, Azure 'a dağıtım sırasında vNIC 'YI VM 'ye bağlar. Aşağıdaki örnek, `myVM` Azure yönetilen diskler ile adlı BIR VM oluşturur ve önceki adımdan adlı vNIC 'yi ekler `myNic` :

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="detailed-walkthrough"></a>Ayrıntılı kılavuz

Azure 'da tam bir sürekli tümleştirme ve sürekli dağıtım (CiCd) altyapısı, belirli sunucuların statik veya uzun süreli sunucular olmasını gerektirir. Sanal ağlar ve ağ güvenlik grupları gibi Azure varlıklarının, nadiren dağıtılan statik ve uzun süreli kaynaklar olması önerilir. Bir sanal ağ dağıtıldıktan sonra altyapıya olumsuz etkileri olmadan Yeni dağıtımlar tarafından yeniden kullanılabilir. Daha sonra bir git deposu sunucusu ekleyebilir veya bir Jenkins Otomasyon sunucusu, geliştirme veya test ortamlarınız için bu sanal ağa CiCd 'yi sunar.  

İç DNS adları yalnızca bir Azure sanal ağı içinde çözülebilir. DNS adları iç olduğundan, altyapıya ek güvenlik sağlayan, dış internet 'e çözümlenemez.

Aşağıdaki örneklerde, örnek parametre adlarını kendi değerlerinizle değiştirin. Örnek parametre adları `myResourceGroup` ,, `myNic` ve içerir `myVM` .

## <a name="create-the-resource-group"></a>Kaynak grubunu oluşturma
İlk olarak, [az Group Create](/cli/azure/group)komutuyla kaynak grubunu oluşturun. Aşağıdaki örnek `westus` konumunda `myResourceGroup` adlı bir kaynak grubu oluşturur:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-virtual-network"></a>Sanal ağı oluşturma

Sonraki adım, VM 'Leri başlatmak için bir sanal ağ oluşturmak için kullanılır. Sanal ağ Bu izlenecek yol için bir alt ağ içerir. Azure sanal ağları hakkında daha fazla bilgi için bkz. [sanal ağ oluşturma](../../virtual-network/manage-virtual-network.md#create-a-virtual-network). 

[Az Network VNET Create](/cli/azure/network/vnet)ile sanal ağ oluşturun. Aşağıdaki örnek adlı bir sanal ağ ve adlı `myVnet` alt ağ oluşturur `mySubnet` :

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

## <a name="create-the-network-security-group"></a>Ağ güvenlik grubunu oluşturma
Azure ağ güvenlik grupları, ağ katmanındaki bir güvenlik duvarıyla eşdeğerdir. Ağ güvenlik grupları hakkında daha fazla bilgi için bkz. [Azure CLI 'de NSG oluşturma](../../virtual-network/tutorial-filter-network-traffic-cli.md). 

[Az Network NSG Create](/cli/azure/network/nsg)komutuyla ağ güvenlik grubunu oluşturun. Aşağıdaki örnek adlı bir ağ güvenlik grubu oluşturur `myNetworkSecurityGroup` :

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

## <a name="add-an-inbound-rule-to-allow-ssh"></a>SSH 'ye izin vermek için bir gelen kuralı ekleme
[Az Network NSG Rule Create](/cli/azure/network/nsg/rule)komutuyla ağ güvenlik grubu için bir gelen kuralı ekleyin. Aşağıdaki örnek adlı bir kural oluşturur `myRuleAllowSSH` :

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myRuleAllowSSH \
    --protocol tcp \
    --direction inbound \
    --priority 1000 \
    --source-address-prefix '*' \
    --source-port-range '*' \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow
```

## <a name="associate-the-subnet-with-the-network-security-group"></a>Alt ağı ağ güvenlik grubuyla ilişkilendir
Alt ağı ağ güvenlik grubuyla ilişkilendirmek için [az Network VNET subnet Update](/cli/azure/network/vnet/subnet)kullanın. Aşağıdaki örnek, alt ağ adını `mySubnet` adlı ağ güvenlik grubuyla ilişkilendirir `myNetworkSecurityGroup` :

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```


## <a name="create-the-virtual-network-interface-card-and-static-dns-names"></a>Sanal ağ arabirim kartını ve statik DNS adlarını oluşturma
Azure çok esnektir, ancak VM ad çözümlemesi için DNS adlarını kullanmak istiyorsanız, DNS etiketi içeren sanal ağ arabirim kartları (vNIC 'ler) oluşturmanız gerekir. Sanal NIC 'Ler altyapı yaşam döngüsü üzerinden farklı VM 'lere bağlanarak yeniden kullanabileceğiniz için önemlidir. Bu yaklaşım, sanal makinelerin geçici olması durumunda vNIC 'yi statik kaynak olarak tutar. VNIC 'de DNS etiketleme kullanarak, VNet 'teki diğer VM 'lerden basit ad çözümlemesini etkinleştirebiliriz. Çözümlenebilen adların kullanılması, diğer sanal makinelerin Otomasyon sunucusuna DNS adı veya git sunucusu tarafından erişmesine olanak sağlar `Jenkins` `gitrepo` .  

[Az Network Nic Create](/cli/azure/network/nic)Ile vNIC 'yi oluşturun. Aşağıdaki örnek adlı bir vNIC oluşturur `myNic` , bunu `myVnet` adlı sanal ağa bağlar `myVnet` ve ADLı bir iç DNS ad kaydı oluşturur `jenkins` :

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --internal-dns-name jenkins
```

## <a name="deploy-the-vm-into-the-virtual-network-infrastructure"></a>VM 'yi sanal ağ altyapısına dağıtma
Artık bir sanal ağ ve alt ağa sahip olduğumuz bir ağ güvenlik grubu, SSH için 22 bağlantı noktası ve bir vNIC dışındaki tüm gelen trafiği engelleyerek alt ağımızı korumak için bir güvenlik duvarı olarak davranan bir ağ güvenlik grubudur. Artık bu mevcut ağ altyapısının içinde bir VM dağıtabilirsiniz.

[az vm create](/cli/azure/vm) ile bir VM oluşturun. Aşağıdaki örnek, `myVM` Azure yönetilen diskler ile adlı BIR VM oluşturur ve önceki adımdan adlı vNIC 'yi ekler `myNic` :

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Mevcut kaynakları çağırmak için CLı bayraklarını kullanarak, Azure 'un VM 'yi mevcut ağ içinde dağıtmasını sağlıyoruz. Yeniden yinelemek için, bir sanal ağ ve alt ağ dağıtıldıktan sonra, Azure bölgeniz içinde statik veya kalıcı kaynaklar olarak bırakılabilir.  

## <a name="next-steps"></a>Sonraki adımlar
* [Azure CLı komutlarını doğrudan kullanarak bir Linux VM için kendi özel ortamınızı oluşturma](create-cli-complete.md)
* [Şablonları kullanarak Azure 'da bir Linux VM oluşturma](create-ssh-secured-vm-from-template.md)
