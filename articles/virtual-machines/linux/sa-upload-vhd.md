---
title: Azure CLı ile özel bir Linux diski yükleme
description: Kaynak Yöneticisi dağıtım modelini ve Azure CLı 'yi kullanarak Azure 'a bir sanal sabit disk (VHD) oluşturma ve yükleme
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/10/2017
ms.author: cynthn
ms.openlocfilehash: ef2db7f13ea5192634855b69a0d355e0f1e11ecb
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035074"
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli"></a>Azure CLı ile özel diskten bir Linux sanal makinesi yükleme ve oluşturma

Bu makalede, Azure CLı ile bir Azure depolama hesabına sanal sabit disk (VHD) yükleme ve bu özel diskten Linux VM oluşturma işlemlerinin nasıl yapılacağı gösterilir. Bu işlevsellik, gereksinimlerinize bir Linux yükleyip yapılandırmanıza ve ardından bu VHD 'yi kullanarak Azure sanal makinelerini (VM 'Ler) hızlı bir şekilde oluşturmanıza olanak tanır.

Bu konu, son VHD 'ler için depolama hesapları kullanır, ancak bu adımları [yönetilen diskleri](upload-vhd.md)kullanarak da yapabilirsiniz. 

## <a name="quick-commands"></a>Hızlı komutlar
Görevi hızlı bir şekilde gerçekleştirmeniz gerekirse, aşağıdaki bölümde bir VHD 'yi Azure 'a yüklemek için temel komutların ayrıntıları verilmiştir. Her adım için daha ayrıntılı bilgi ve bağlam, [buradan başlayarak](#requirements)belgenin geri kalanını tespit edebilir.

En son [Azure CLI](/cli/azure/install-az-cli2) 'nın yüklü olduğundan ve [az Login](/cli/azure/reference-index)kullanarak bir Azure hesabında oturum açtığınızdan emin olun.

Aşağıdaki örneklerde, örnek parametre adlarını kendi değerlerinizle değiştirin. `myResourceGroup`, `mystorageaccount`ve `mydisks`dahil olmak üzere örnek parametre adları.

Öncelikle [az group create](/cli/azure/group) komutuyla bir kaynak grubu oluşturun. Aşağıdaki örnek `myResourceGroup` konumunda `WestUs` adlı bir kaynak grubu oluşturur:

```azurecli
az group create --name myResourceGroup --location westus
```

[Az Storage Account Create](/cli/azure/storage/account)komutuyla sanal disklerinizi barındıracak bir depolama hesabı oluşturun. Aşağıdaki örnek, `mystorageaccount`adlı bir depolama hesabı oluşturur:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Depolama hesabınızın erişim anahtarlarını [az Storage Account Keys List](/cli/azure/storage/account/keys)ile listeleyin. `key1`bir kopyasını alın:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

[Az Storage Container Create](/cli/azure/storage/container)ile edindiğiniz depolama anahtarını kullanarak depolama hesabınızda bir kapsayıcı oluşturun. Aşağıdaki örnek, `key1`depolama anahtarı değerini kullanarak `mydisks` adlı bir kapsayıcı oluşturur:

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Son olarak, VHD 'nizi [az Storage blob upload](/cli/azure/storage/blob)ile oluşturduğunuz kapsayıcıya yükleyin. `/path/to/disk/mydisk.vhd`altında VHD 'niz için yerel yolu belirtin:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

[Az VM Create](/cli/azure/vm)Ile diskinizin URI 'sini (`--image`) belirtin. Aşağıdaki örnek, daha önce karşıya yüklenen sanal diski kullanarak `myVM` adlı bir sanal makıne oluşturur:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

Hedef depolama hesabı, sanal diskinizi karşıya yüklediğiniz konum ile aynı olmalıdır. Ayrıca, sanal ağ, genel IP adresi, Kullanıcı adı ve SSH anahtarları gibi **az VM Create** komutu için gereken tüm ek parametreleri de belirtmeniz veya yanıt vermenizi da gerekir. [KULLANILABILIR clı Kaynak Yöneticisi parametreleri](../azure-cli-arm-commands.md#azure-vm-commands-to-manage-your-azure-virtual-machines)hakkında daha fazla bilgi edinebilirsiniz.

## <a name="requirements"></a>Gereksinimler
Aşağıdaki adımları tamamlayabilmeniz için şunlar gerekir:

* **Linux işletim sistemi bir. vhd dosyasına yüklenir** - [Azure tarafından onaylanan bir Linux dağıtımını](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (veya [onaylı olmayan dağıtımlar için bilgileri](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) VHD biçimindeki bir sanal diske yükleme. VM ve VHD oluşturmak için birden çok araç mevcuttur:
  * Kemu veya [](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) [KVM](https://www.linux-kvm.org/page/RunningKVM)'yi yükleyip yapılandırarak, görüntü biçimi olarak VHD 'yi kullanmaya özen gösterin. Gerekirse, `qemu-img convert`kullanarak [bir görüntüyü dönüştürebilirsiniz](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) .
  * Hyper-V ' d i [Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) veya [Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx)üzerinde de kullanabilirsiniz.

> [!NOTE]
> Yeni VHDX biçimi Azure 'da desteklenmez. Bir VM oluşturduğunuzda, VHD 'yi biçim olarak belirtin. Gerekirse, [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) veya [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) POWERSHELL cmdlet 'INI kullanarak VHDX disklerini VHD 'ye dönüştürebilirsiniz. Azure, Dinamik VHD 'leri karşıya yüklemeyi desteklemez, bu nedenle karşıya yüklemeden önce bu diskleri statik VHD 'lere dönüştürmeniz gerekir. Azure 'a yükleme işlemi sırasında dinamik diskleri dönüştürmek için [Azure VHD yardımcı programları](https://github.com/Microsoft/azure-vhd-utils-for-go) gibi araçları kullanabilirsiniz.
> 
> 

* Özel diskinizden oluşturulan VM 'Lerin, diskin kendisiyle aynı depolama hesabında bulunması gerekir
  * Hem özel diskinizi hem de oluşturulan VM 'Leri barındıracak bir depolama hesabı ve kapsayıcısı oluşturun
  * Tüm VM 'lerinizi oluşturduktan sonra, diskinizi güvenle silebilirsiniz

En son [Azure CLI](/cli/azure/install-az-cli2) 'nın yüklü olduğundan ve [az Login](/cli/azure/reference-index)kullanarak bir Azure hesabında oturum açtığınızdan emin olun.

Aşağıdaki örneklerde, örnek parametre adlarını kendi değerlerinizle değiştirin. `myResourceGroup`, `mystorageaccount`ve `mydisks`dahil olmak üzere örnek parametre adları.

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>Diski karşıya yüklenecek şekilde hazırlama
Azure çeşitli Linux dağıtımlarını destekler (bkz. [onaylı dağıtımlar](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Aşağıdaki makaleler, Azure 'da desteklenen çeşitli Linux dağıtımlarını hazırlama konusunda size rehberlik sağlar:

* **[CentOS tabanlı dağıtımlar](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Linux 'u kaldırma](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Diğer-onaylı olmayan dağıtımlar](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Ayrıca, Linux görüntülerini Azure için hazırlama hakkında daha genel ipuçları için bkz. **[Linux yükleme notları](create-upload-generic.md#general-linux-installation-notes)** .

> [!NOTE]
> [Azure platformu SLA 'sı](https://azure.microsoft.com/support/legal/sla/virtual-machines/) yalnızca, [Azure tarafından onaylanan dağıtımlarda Linux](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)'ta ' desteklenen sürümler ' altında belirtilen yapılandırma ayrıntıları ile belirlenen dağıtımlardan biri kullanıldığında, Linux çalıştıran VM 'ler için geçerlidir.
> 
> 

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma
Kaynak grupları, sanal ağ ve depolama gibi sanal makinelerinizi desteklemek için tüm Azure kaynaklarını mantıksal olarak bir araya getirir. Daha fazla bilgi kaynak grubu için bkz. [kaynak gruplarına genel bakış](../../azure-resource-manager/resource-group-overview.md). Özel diskinizi karşıya yüklemeden ve VM 'Ler oluşturmadan önce, [az Group Create](/cli/azure/group)ile bir kaynak grubu oluşturmanız gerekir.

Aşağıdaki örnek `myResourceGroup` konumunda `westus` adlı bir kaynak grubu oluşturur:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

[Az Storage Account Create](/cli/azure/storage/account)komutuyla özel diskiniz ve VM 'niz için bir depolama hesabı oluşturun. Özel diskinizden oluşturduğunuz yönetilmeyen disklere sahip sanal makinelerin, bu diskle aynı depolama hesabında olması gerekir. 

Aşağıdaki örnek, daha önce oluşturulan kaynak grubunda `mystorageaccount` adlı bir depolama hesabı oluşturur:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Depolama hesabı anahtarlarını Listele
Azure, her depolama hesabı için 2 512 bitlik erişim anahtarları oluşturur. Bu erişim anahtarları, depolama hesabında kimlik doğrulaması yapılırken (örneğin, yazma işlemlerini yürütmek için) kullanılır. [Depolama alanına erişimi yönetme](../../storage/common/storage-account-manage.md#access-keys)hakkında daha fazla bilgi edinmek için buraya tıklayın. Erişim anahtarlarını [az Storage Account Keys List](/cli/azure/storage/account/keys)komutuyla görüntüleyebilirsiniz.

Oluşturduğunuz depolama hesabı için erişim anahtarlarını görüntüleyin:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Çıktı şuna benzer olacaktır:

```azurecli
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```
Sonraki adımlarda depolama hesabınızla etkileşimde bulunmak için `key1` bir göz önüne alın.

## <a name="create-a-storage-container"></a>Bir depolama kapsayıcısı oluşturma
Yerel dosya sisteminizi mantıksal olarak düzenlemek için farklı dizinler oluşturduğunuz gibi, disklerinizi düzenlemek için bir depolama hesabı içinde kapsayıcılar oluşturursunuz. Depolama hesabı, herhangi bir sayıda kapsayıcı içerebilir. [Az Storage Container Create](/cli/azure/storage/container)ile bir kapsayıcı oluşturun.

Aşağıdaki örnek, `mydisks`adlı bir kapsayıcı oluşturur:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>VHD 'YI karşıya yükle
Şimdi, [az Storage blob upload](/cli/azure/storage/blob)ile özel diskinizi karşıya yükleyin. Özel diskinizi bir Sayfa Blobu olarak karşıya yükleyip depoladığınızda.

Erişim anahtarınızı, önceki adımda oluşturduğunuz kapsayıcıyı ve ardından yerel bilgisayarınızdaki özel disk yolunu belirtin:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>Sanal makine oluşturma
Yönetilmeyen diskler içeren bir VM oluşturmak için [az VM Create](/cli/azure/vm)ile disk (`--image`) URI 'sini belirtin. Aşağıdaki örnek, daha önce karşıya yüklenen sanal diski kullanarak `myVM` adlı bir sanal makıne oluşturur:

Özel diskinize işaret etmek için [az VM Create](/cli/azure/vm) ile `--image` parametresini belirtirsiniz. `--storage-account`, özel diskinizin depolandığı depolama hesabıyla eşleştiğinden emin olun. VM 'lerinizi depolamak için özel diskle aynı kapsayıcıyı kullanmanız gerekmez. Özel diskinizi karşıya yüklemeden önce, önceki adımlarla aynı şekilde ek kapsayıcılar oluşturduğunuzdan emin olun.

Aşağıdaki örnek, özel diskinizden `myVM` adlı bir sanal makine oluşturur:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

Kullanıcı adı ve SSH anahtarları gibi **az VM Create** komutu için gerekli tüm ek parametreleri de belirtmeniz veya bu istemlere yanıt vermesi gerekir.


## <a name="resource-manager-template"></a>Resource Manager şablonu
Azure Resource Manager şablonlar, derlemek istediğiniz ortamı tanımlayan JavaScript Nesne Gösterimi (JSON) dosyalarıdır. Şablonlar, işlem veya ağ gibi farklı kaynak sağlayıcılarına bölünmüştür. Mevcut şablonları kullanabilir veya kendi kendinize yazabilirsiniz. [Kaynak Yöneticisi ve şablonları kullanma](../../azure-resource-manager/resource-group-overview.md)hakkında daha fazla bilgi edinin.

Şablonunuzun `Microsoft.Compute/virtualMachines` sağlayıcısı içinde, sanal makinenizin yapılandırma ayrıntılarını içeren bir `storageProfile` düğümünüz vardır. Düzenlenecek iki ana parametre, özel diskinize ve yeni VM 'nin sanal diskine işaret eden `image` ve `vhd` URI 'Lerdir. Aşağıda özel disk kullanımı için JSON örneği gösterilmektedir:

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

[Bu mevcut şablonu kullanarak özel görüntüden BIR VM oluşturabilir](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) veya [kendi Azure Resource Manager şablonlarınızı oluşturma](../../azure-resource-manager/resource-group-authoring-templates.md)hakkında bilgi edinebilirsiniz. 

Bir şablonunuz yapılandırıldıktan sonra, VM 'lerinizi oluşturmak için [az Group Deployment Create](/cli/azure/group/deployment) kullanın. `--template-uri` parametresiyle JSON şablonunuzun URI 'sini belirtin:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Bilgisayarınızda yerel olarak depolanan bir JSON dosyanız varsa, bunun yerine `--template-file` parametresini kullanabilirsiniz:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Sonraki adımlar
Özel sanal diskinizi hazırladıktan ve karşıya yükledikten sonra, [Kaynak Yöneticisi ve şablonları kullanma](../../azure-resource-manager/resource-group-overview.md)hakkında daha fazla bilgi edinebilirsiniz. Ayrıca, yeni sanal makinelerinize [bir veri diski eklemek](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) isteyebilirsiniz. Sanal makinelerinize erişmeniz gereken uygulamalarınız varsa, [bağlantı noktalarını ve uç noktaları açmayı](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)unutmayın.

