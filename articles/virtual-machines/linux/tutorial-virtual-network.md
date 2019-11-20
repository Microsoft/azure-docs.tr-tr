---
title: Öğretici-Linux VM 'Ler için Azure sanal ağları oluşturma ve yönetme
description: Bu öğreticide, Azure CLI kullanarak Linux sanal makineleri için Azure sanal ağları oluşturup yönetmeyi öğrenirsiniz
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/10/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: d6624b9d5d77a8552584049463b63738bbf17627
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034341"
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-linux-virtual-machines-with-the-azure-cli"></a>Öğretici: Azure CLI ile Linux sanal makineleri için Azure sanal ağlarını yönetme

Azure sanal makineleri, iç ve dış ağ iletişimi için Azure ağını kullanır. Bu öğretici, iki sanal makineyi dağıtma ve bu VM’ler için Azure ağını yapılandırma konusunda rehberlik sunar. Bu öğreticideki örneklerde VM’lerde veritabanı arka ucuna sahip bir web uygulaması barındırıldığı varsayılır, ancak öğreticide uygulama dağıtılmaz. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Sanal ağ ve alt ağ oluşturma
> * Genel IP adresi oluşturma
> * Ön uç VM’si oluşturma
> * Ağ trafiğinin güvenliğini sağlama
> * Arka uç VM’si oluşturma

Bu öğretici, en son sürüme sürekli olarak güncellenen [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)içindeki CLI 'yi kullanır. Cloud Shell açmak için herhangi bir kod bloğunun en üstünden **deneyin** ' i seçin.

CLI'yi yerel olarak yükleyip kullanmayı tercih ederseniz bu öğretici için Azure CLI 2.0.30 veya sonraki bir sürümünü çalıştırmanız gerekir. Sürümü bulmak için `az --version` komutunu çalıştırın. Yükleme veya yükseltme yapmanız gerekiyorsa bkz. [Azure CLI'yı yükleme]( /cli/azure/install-azure-cli).

## <a name="vm-networking-overview"></a>VM ağına genel bakış

Azure sanal ağları, sanal makineler ile İnternet ve Azure SQL veritabanı gibi diğer Azure hizmetleri arasında güvenli ağ bağlantıları kurulmasını sağlar. Sanal ağlar, alt ağ adı verilen mantıksal segmentlere ayrılır. Alt ağlar, ağ akışını denetlemek için ve güvenlik sınırı olarak kullanılır. Bir VM dağıtılırken, genellikle bir alt ağa eklenmiş sanal ağ arabirimine sahiptir.

Öğreticiyi tamamlarken, aşağıdaki sanal ağ kaynakları oluşturulur:

![İki alt ağ içeren sanal ağ](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet* - VM’lerin birbirleriyle ve İnternet’le iletişim kurmak için kullandığı sanal ağ.
- *myFrontendSubnet* - Ön uç kaynakları tarafından kullanılan *myVNet*’teki alt ağ.
- *myPublicIPAddress* - İnternet’ten *myFrontendVM*’ye erişmek için kullanılan genel IP adresi.
- *myFrontentNic* - *myBackendVM* ile iletişim kurmak için *myFrontendVM* tarafından kullanılan ağ arabirimi.
- *myFrontendVM* - İnternet ile *myBackendVM* arasında iletişim kurmak için kullanılan VM.
- *myBackendNSG* - *myFrontendVM* ile *myBackendVM* arasındaki iletişimi denetleyen ağ güvenlik grubu.
- *myBackendSubnet* - *myBackendNSG* ile ilişkilendirilmiş ve arka uç kaynakları tarafından kullanılan alt ağ.
- *myBackendNic* - *myFrontendVM* ile iletişim kurmak için *myBackendVM* tarafından kullanılan ağ arabirimi.
- *myBackendVM* - *myFrontendVM* ile iletişim kurmak için 22 ve 3306 numaralı bağlantı noktalarını kullanan VM.

## <a name="create-a-virtual-network-and-subnet"></a>Sanal ağ ve alt ağ oluşturma

Bu öğreticide iki alt ağa sahip tek bir sanal ağ oluşturulur. Web uygulamasını barındırmak için bir ön uç alt ağı ve veritabanı sunucusunu barındırmak için bir arka uç alt ağı.

Sanal ağ oluşturabilmek için önce [az group create](/cli/azure/group) ile bir kaynak grubu oluşturun. Aşağıdaki örnekte eastus konumunda *myRGNetwork* adlı bir kaynak grubu oluşturulmaktadır.

```azurecli-interactive 
az group create --name myRGNetwork --location eastus
```

### <a name="create-virtual-network"></a>Sanal ağ oluşturma

Sanal ağ oluşturmak için [az network vnet create](/cli/azure/network/vnet) komutunu kullanın. Bu örnekte ağ, *mvVNet* olarak adlandırılmaktadır ve *10.0.0.0/16* adres öneki belirtilmiştir. Ayrıca *myFrontendSubnet* adıyla ve *10.0.1.0/24* önekiyle bir alt ağ oluşturulmaktadır. Bu öğreticinin ilerleyen bölümlerinde bu alt ağa bir ön uç bağlanmaktadır. 

```azurecli-interactive 
az network vnet create \
  --resource-group myRGNetwork \
  --name myVNet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myFrontendSubnet \
  --subnet-prefix 10.0.1.0/24
```

### <a name="create-subnet"></a>Alt ağ oluşturma

[az network vnet subnet create](/cli/azure/network/vnet/subnet) komutu kullanılarak sanal ağa yeni bir alt ağ eklenir. Bu örnekte alt ağ, *myBackendSubnet* olarak adlandırılmaktadır ve *10.0.2.0/24* adres öneki belirtilmiştir. Bu alt ağ tüm arka uç hizmetleriyle kullanılır.

```azurecli-interactive 
az network vnet subnet create \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --address-prefix 10.0.2.0/24
```

Bu noktada ağ oluşturulur ve biri ön uç hizmetlerine, diğeri ise arka uç hizmetlerine yönelik olan iki alt ağ segmentine ayrılır. Sonraki bölümde sanal makineler oluşturulacak ve bu alt ağlara bağlanacak.

## <a name="create-a-public-ip-address"></a>Genel IP adresi oluşturma

Genel IP adresi, Azure kaynaklarına İnternet’ten erişilmesine izin verir. Genel IP adresi ayırma yöntemi dinamik veya statik olarak yapılandırılabilir. Genel IP adresi varsayılan olarak dinamik biçimde ayrılır. Bir VM serbest bırakıldığında dinamik IP adresleri de serbest bırakılır. Bu davranış, VM’nin serbest bırakılmasını içeren tüm işlemlerde IP adresinin değişmesine neden olur.

Ayırma yöntemi statik olarak ayarlanabilir; bu yöntem, VM serbest bırakılsa bile IP adresinin VM’ye atanmış olarak kalmasını sağlar. Statik olarak ayrılan bir IP adresi kullanılırken IP adresi belirtilemez. Bunun yerine IP adresi, kullanılabilen adresler havuzundan ayrılır.

```azurecli-interactive
az network public-ip create --resource-group myRGNetwork --name myPublicIPAddress
```

[az vm create](/cli/azure/vm) komutu ile bir VM oluşturulduğunda varsayılan genel IP adresi ayırma yöntemi dinamiktir. [az vm create](/cli/azure/vm) komutu kullanılarak bir sanal makine oluştururken statik genel IP adresi atamak için `--public-ip-address-allocation static` bağımsız değişkenini ekleyin. Bu işlem bu öğreticide gösterilmemektedir, ancak sonraki bölümde dinamik olarak ayrılan bir IP adresi statik olarak ayrılmış bir adrese dönüştürülecek. 

### <a name="change-allocation-method"></a>Ayırma yöntemini değiştirme

IP adresi ayırma yöntemi [az network public-ip update](/cli/azure/network/public-ip) komutu kullanılarak değiştirilebilir. Bu örnekte, ön uç VM’nin IP adresi ayırma yöntemi statik olarak değiştirilmektedir.

Öncelikle VM’yi serbest bırakın.

```azurecli-interactive 
az vm deallocate --resource-group myRGNetwork --name myFrontendVM
```

Ayırma yöntemini güncelleştirmek için [az network public-ip update](/cli/azure/network/public-ip) komutunu kullanın. Bu durumda `--allocation-method`, *static* olarak ayarlanır.

```azurecli-interactive 
az network public-ip update --resource-group myRGNetwork --name myPublicIPAddress --allocation-method static
```

VM’yi başlatın.

```azurecli-interactive 
az vm start --resource-group myRGNetwork --name myFrontendVM --no-wait
```

### <a name="no-public-ip-address"></a>Genel IP adresi yok

Çoğunlukla bir VM’nin İnternet’ten erişilebilir olmasına gerek yoktur. Genel IP adresi olmayan bir VM oluşturmak için `--public-ip-address ""` bağımsız değişkenini boş çift tırnaklar ile kullanın. Bu yapılandırma, öğreticinin sonraki bölümlerinde gösterilmektedir.

## <a name="create-a-front-end-vm"></a>Ön uç VM’si oluşturma

[az vm create](/cli/azure/vm) komutunu kullanarak *myPublicIPAddress* adresini kullanan *myFrontendVM* adlı bir VM oluşturun.

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myFrontendVM \
  --vnet-name myVNet \
  --subnet myFrontendSubnet \
  --nsg myFrontendNSG \
  --public-ip-address myPublicIPAddress \
  --image UbuntuLTS \
  --generate-ssh-keys
```

## <a name="secure-network-traffic"></a>Ağ trafiğinin güvenliğini sağlama

Ağ güvenlik grubu (NSG), Azure Sanal Ağlara (VNet) bağlı kaynaklara ağ trafiğine izin veren veya reddeden güvenlik kurallarının listesini içerir. NSG’ler alt ağlarla veya tek tek ağ arabirimleriyle ilişkilendirilebilir. Bir NSG ağ arabirimiyle ilişkilendirildiğinde, yalnızca ilişkili VM için geçerli olur. Bir NSG bir alt ağ ile ilişkilendirildiğinde kurallar alt ağa bağlı tüm kaynaklar için geçerli olur. 

### <a name="network-security-group-rules"></a>Ağ güvenlik grubu kuralları

NSG kuralları trafiğe izin verilen veya trafiğin engellendiği ağ bağlantı noktalarını tanımlar. Trafiğin belirli sistemler veya alt ağlar arasında denetlenmesi için kurallar, kaynak ve hedef IP adresi aralıkları içerebilir. Ayrıca NSG kuralları öncelik (1 ile 4.096 arasında) içerir. Kurallar öncelik sırasına göre değerlendirilir. 100 önceliğine sahip bir kural, 200 önceliğine sahip kuraldan önce değerlendirilir.

Tüm NSG'ler bir varsayılan kurallar kümesini içerir. Varsayılan kurallar silinemez ancak en düşük önceliğe atanmış oldukları için sizin oluşturduğunuz kurallar tarafından geçersiz kılınabilirler.

NSG’ler için varsayılan kurallar şunlardır:

- **Sanal ağ** - Kaynağı bir sanal ağ olan ve bir sanal ağda biten trafiğe hem gelen hem de giden yönlerde izin verilir.
- **İnternet** - Giden trafiğe izin verilir, ancak gelen trafik engellenir.
- **Yük dengeleyici** - VM’lerinizin ve rol örneklerinizin sistem durumunu araştıran Azure yük dengeleyicisine izin verir. Yük dengeli bir küme kullanmıyorsanız bu kuralı geçersiz kılabilirsiniz.

### <a name="create-network-security-groups"></a>Ağ güvenlik grupları oluşturma

Ağ güvenlik grubu, [az vm create](/cli/azure/vm) komutu kullanılarak VM ile aynı anda oluşturulabilir. Bu işlem yapıldığında NSG, VM ağ arabirimi ile ilişkilendirilir ve herhangi bir kaynaktan *22* numaralı bağlantı noktası üzerinden gelen trafiğe izin veren NSG kuralı otomatik olarak oluşturulur. Bu öğreticinin önceki bölümlerinde ön uç NSG’si ön uç VM’si ile otomatik oluşturulmuştu. Ayrıca 22 numaralı bağlantı noktası için bir NSG kuralı otomatik olarak oluşturulmuştu. 

Bazı durumlarda, örneğin varsayılan SSH kurallarının oluşturulmaması gerektiğinde veya NSG’nin bir alt ağa eklenmesi gerektiğinde NSG’yi önceden oluşturmak yararlı olabilir. 

Bir ağ güvenlik grubu oluşturmak için [az network nsg create](/cli/azure/network/nsg) komutunu kullanın.

```azurecli-interactive 
az network nsg create --resource-group myRGNetwork --name myBackendNSG
```

NSG, bir ağ arabirimiyle ilişkilendirilmek yerine bir alt ağla ilişkilendirilir. Bu yapılandırmada alt ağa eklenmiş tüm VM’ler NSG kurallarını devralır.

*myBackendSubnet* adlı mevcut alt ağı yeni NSG ile güncelleştirin.

```azurecli-interactive 
az network vnet subnet update \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --network-security-group myBackendNSG
```

### <a name="secure-incoming-traffic"></a>Gelen trafiğin güvenliğini sağlama

Ön uç VM’si oluşturulduğunda 22 numaralı bağlantı noktasından gelen trafiğe izin veren bir NSG kuralı oluşturulur. Bu kural, VM ile SSH bağlantısı kurulmasına izin verir. Bu örnekte aynı zamanda *80* numaralı bağlantı noktasındaki trafiğe de izin verilmelidir. Bu yapılandırma VM’den web uygulamasına erişilmesine izin verir.

[80](/cli/azure/network/nsg/rule) numaralı bağlantı noktası için bir kural oluşturmak üzere *az network nsg rule create* komutunu kullanın.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myFrontendNSG \
  --name http \
  --access allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 80
```

Ön uç VM’sine yalnızca *22* ve *80* numaralı bağlantı noktalarından erişilebilir. Diğer gelen trafiğin tümü, ağ güvenlik grubunda engellenir. NSG kuralı yapılandırmalarını görselleştirmek yararlı olabilir. NSG kuralı yapılandırmasını [az network rule list](/cli/azure/network/nsg/rule) komutu ile döndürün. 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myFrontendNSG --output table
```

### <a name="secure-vm-to-vm-traffic"></a>VM’den VM’ye trafiğin güvenliğini sağlama

Ağ güvenlik grubu kuralları VM’ler arasında da uygulanabilir. Bu örnekte ön uç VM’sinin arka uç VM’siyle *22* ve *3306* numaralı bağlantı noktalarından iletişim kurması gerekiyor. Bu yapılandırma, ön uç VM’sinden SSH bağlantısı kurulmasına ve ön uçtaki bir uygulamanın arka uç MySQL veritabanı ile iletişim kurmasına izin verir. Diğer trafiğin tümünün ön uç ve arka uç sanal makineleri arasında engellenmesi gerekir.

22 numaralı bağlantı noktası için bir kural oluşturmak üzere [az network nsg rule create](/cli/azure/network/nsg/rule) komutunu kullanın. `--source-address-prefix` bağımsız değişkenin *10.0.1.0/24* değerini belirttiğini görebilirsiniz. Bu yapılandırma NSG’de yalnızca ön uç alt ağından gelen trafiğe izin verilmesini sağlar.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name SSH \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "22"
```

Şimdi 3306 numaralı bağlantı noktasına MySQL trafiği için bir kural ekleyin.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name MySQL \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "3306"
```

NSG’ler aynı VNet içinde yer alan VM’ler arasındaki tüm trafiğe izin veren bir varsayılan kurala sahiptir, bu nedenle arka uç NSG’lerinin tüm trafiği engellenmesi için bir kural oluşturulabilir. Burada `--priority` için, hem NSG hem de MySQL kurallarından küçük olan *300* değerinin belirtildiğini görebilirsiniz. Bu yapılandırma, NSG’de SSH ve MySQL trafiğine izin verilmesini sağlar.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name denyAll \
  --access Deny \
  --protocol Tcp \
  --direction Inbound \
  --priority 300 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "*"
```

## <a name="create-back-end-vm"></a>Arka uç VM’si oluşturma

*myBackendSubnet*’e eklenmiş bir sanal makine oluşturun. `--nsg` bağımsız değişkenin boş çift tırnak içerdiğini görebilirsiniz. VM ile bir NSG oluşturmak gerekemez. VM, önceden oluşturulmuş arka uç NSG’si ile korunan arka uç alt ağına eklenir. Bu NSG, VM için geçerlidir. Ayrıca burada `--public-ip-address` bağımsız değişkeninin boş çift tırnak değeri içerdiğini görebilirsiniz. Bu yapılandırma genel IP adresi olmayan bir VM oluşturur. 

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myBackendVM \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --public-ip-address "" \
  --nsg "" \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Arka uç VM’sine, ön uç alt ağından yalnızca *22* ve *3306* numaralı bağlantı noktaları üzerinden erişilebilir. Diğer gelen trafiğin tümü, ağ güvenlik grubunda engellenir. NSG kuralı yapılandırmalarını görselleştirmek yararlı olabilir. NSG kuralı yapılandırmasını [az network rule list](/cli/azure/network/nsg/rule) komutu ile döndürün. 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myBackendNSG --output table
```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide sanal makinelerle ilgili Azure ağlarını oluşturup ve güvenliğini sağladınız. Şunları öğrendiniz:

> [!div class="checklist"]
> * Sanal ağ ve alt ağ oluşturma
> * Genel IP adresi oluşturma
> * Ön uç VM’si oluşturma
> * Ağ trafiğinin güvenliğini sağlama
> * Arka uç VM’si oluşturma

Azure Backup kullanarak sanal makinelerdeki verilerin güvenliğini sağlamayı öğrenmek için sonraki öğreticiye geçin. 

> [!div class="nextstepaction"]
> [Azure’da Linux sanal makinelerini yedekleme](./tutorial-backup-vms.md)
