---
title: "Azure’da Linux VM oluşturmanın farklı yolları | Microsoft Azure"
description: "Azure üzerinde bir Linux sanal makine oluşturmanın farklı yollarını her bir yöntemin araç ve öğreticilerinin bağlantılarıyla birlikte listeler."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f38f8a44-6c88-4490-a84a-46388212d24c
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: d7ff1317cdf6ccfe6b4e5035878fc4e096fcc0f9
ms.lasthandoff: 04/03/2017


---
# <a name="different-ways-to-create-a-linux-vm"></a>Linux VM oluşturmak için farklı yöntemler
Azure’da size uygun araçları ve iş akışlarını kullanarak bir Linux sanal makine (VM) oluşturma esnekliğine sahipsiniz. Bu makalede bu farklılıklar ve Azure CLI 2.0 dahil olmak üzere Linux VM'lerinizi oluşturmaya yönelik örnekler özetlenmektedir. [Azure CLI 1.0](creation-choices-nodejs.md) dahil oluşturma seçeneklerini de görüntüleyebilirsiniz.

[Azure CLI 2.0](/cli/azure/install-az-cli2) bir npm paketi, distro ile sağlanan paketler veya Docker kapsayıcısı üzerinden çeşitli platformlarda kullanılabilir. Ortamınız için en uygun derlemeyi yükleyin ve [az login](/cli/azure/#login) komutunu kullanarak Azure’da oturum açın

Aşağıdaki örnekler Azure CLI 2.0 sürümünü kullanır. Gösterilen komutlara ilişkin daha fazla ayrıntı için her bir makaleyi okuyun. Ayrıca [Azure CLI 1.0](creation-choices-nodejs.md) kullanarak Linux oluşturma seçeneklerine ilişkin örnekleri bulabilirsiniz.

* [Azure CLI 2.0 kullanarak bir Linux VM oluşturma](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Bu örnekte `myResourceGroup` adlı bir kaynak grubu oluşturmak için [az group create](/cli/azure/group#create) komutu kullanılmaktadır: 
-    
    ```azurecli
    az group create --name myResourceGroup --location westus
    ```
    
  * Bu örnekte Azure Yönetilen Diskler ve `id_rsa.pub` adlı ortak anahtarla en son Debian görüntüsü kullanılarak `myVM` adlı bir VM oluşturmak için [az vm create](/cli/azure/vm#create) komutu kullanılmaktadır:

    ```azurecli
    az vm create \
    --image credativ:Debian:8:latest \
     --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
az vm disk attach –g myResourceGroup –-vm-name myVM –-disk myDataDisk  –-new --size-gb 5    --public-ip-address-dns-name myPublicDNS \
    --resource-group myResourceGroup \
    --location westus \
    --name myVM
    ```

    * Yönetilmeyen diskler kullanmak isterseniz yukarıdaki komuta `--use-unmanaged-disks` bayrağını ekleyin. Sizin için bir depolama hesabı oluşturulur. Daha fazla bilgi için bkz. [Azure Yönetilen Disklere Genel Bakış](../../storage/storage-managed-disks-overview.md).

* [Bir Azure şablonu kullanarak güvenli bir Linux VM oluşturma](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Aşağıdaki örnekte GitHub’a depolanmış bir şablon kullanılarak VM oluşturmak için [az group deployment create](/cli/azure/group/deployment#create) komutu kullanılmaktadır:
    
    ```azurecli
    az group deployment create --resource-group myResourceGroup \ 
      --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json \
      --parameters @myparameters.json
    ```
    
* [Azure CLI kullanarak eksiksiz bir Linux ortamı oluşturma](create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Bir yük dengeleyici ve kullanılabilirlik kümesi içinde birden fazla sanal makine oluşturmayı içerir.

* [Linux VM’ye disk ekleme](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
  
  * Aşağıdaki örnekte `myVM` adlı var olan VM'ye 50 GB boyutunda bir yönetilen disk eklemek için [az vm disk attach-new](/cli/azure/vm/disk#attach-new) komutu kullanılmaktadır:
  
    ```azurecli
    az vm disk attach –g myResourceGroup –-vm-name myVM –-disk myDataDisk  \
    –-new --size-gb 50
    ```

## <a name="azure-portal"></a>Azure portal
[Azure portalı](https://portal.azure.com) sisteminize yüklenecek bir şey olmadığı için sanal makineyi hızlıca oluşturmanıza imkan tanır. VM oluşturmak için Azure portalını kullanma:

* [Azure portalını kullanarak Linux VM oluşturma](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 
* [Azure portalını kullanarak bir diski kullanıma açma](../windows/attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="operating-system-and-image-choices"></a>İşletim sistemi ve görüntü seçimi
Bir sanal makine oluşturulurken çalıştırmak istediğiniz işletim sistemini temel alan bir görüntü seçebilirsiniz. Azure ve ortakları, bazıları önyüklü uygulamalar ve araçlarla gelen çok sayıda görüntü sağlar. Veya kendi görüntülerinizden birini karşıya yükleyin ([aşağıdaki bölüme](#use-your-own-image) bakın).

### <a name="azure-images"></a>Azure görüntüleri
Yayımcı, distro sürümü ve derlemelere göre kullanılabilir seçenekleri görmek için [az vm image](/cli/azure/vm/image) komutlarını kullanın.

Kullanılabilir yayımcıların listesi:

```azurecli
az vm image list-publishers --location WestUS
```

Belirli bir yayımcının kullanılabilir ürünlerinin (teklifler) listesi:

```azurecli
az vm image list-offers --publisher Canonical --location WestUS
```

Belirli bir teklif için kullanılabilir SKU’ların (distro sürümleri) listesi:

```azurecli
az vm image list-skus --publisher Canonical --offer UbuntuServer --location WestUS
```

Belirli bir sürüm için kullanılabilir tüm görüntülerin listesi:

```azurecli
az vm image list --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS --location WestUS
```

Gezinme ve geçerli görüntüleri kullanma ile ilgili daha fazla örnek için bkz. [Azure CLI ile Azure Virtual Machine görüntülerine erişin ve seçin](../windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

**az vm create** komutu daha yaygın distro’lara ve en son sürümlerine hızlıca erişmek için kullanabileceğiniz bazı diğer adlara sahiptir. Diğer adların kullanılması yayımcı, teklif, SKU ve sürümün bir sanal makine oluşturulan her durumda belirtilmesinden çoğunlukla daha hızlıdır:

| Diğer ad | Yayımcı | Sunduğu | SKU | Sürüm |
|:--- |:--- |:--- |:--- |:--- |
| CentOS |OpenLogic |Centos |7.2 |en son |
| CoreOS |CoreOS |CoreOS |Dengeli |en son |
| Debian |credativ |Debian |8 |en son |
| openSUSE |SUSE |openSUSE |13.2 |en son |
| RHEL |RedHat |RHEL |7.2 |en son |
| SLES |SLES |SLES |12 SP1 |en son |
| UbuntuLTS |Canonical |UbuntuServer |14.04.4-LTS |en son |

### <a name="use-your-own-image"></a>Kendi görüntünüzü kullanma
Belirli özelleştirmelere ihtiyaç duyuyorsanız, mevcut bir Azure VM’i temel alan bir görüntü kullanabilirsiniz, bunun için söz konusu VM’i *yakalayabilirsiniz*. Şirket içinde oluşturduğunuz bir görüntüyü de yükleyebilirsiniz. Desteklenen distro’lar ve kendi görüntünüzü kullanma ile ilgili daha fazla bilgi için aşağıdaki makalelere bakın:

* [Azure destekli dağıtımlar](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Desteklenmeyen dağıtımlarla ilgili bilgiler](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Linux sanal makinesini Resource Manager şablonu olarak yakalama](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
  
  * Yönetilmeyen diskleri kullanarak mevcut bir sanal makineyi yakalamaya yönelik örnek **az vm** hızlı başlangıç komutları:
    
    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    az vm generalize --resource-group myResourceGroup --name myVM
    az vm capture --resource-group myResourceGroup --name myVM --vhd-name-prefix myCapturedVM
    ```

## <a name="next-steps"></a>Sonraki adımlar
* [CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ile [portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) üzerinden veya [Azure Resource Manager şablonu](../windows/cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) kullanarak bir Linux VM oluşturun.
* Bir Linux VM oluşturduktan sonra [veri diski ekleyin](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* [Parola veya SSH anahtarlarını sıfırlama ve kullanıcıları yönetme](using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ile ilgili hızlı adımlar

