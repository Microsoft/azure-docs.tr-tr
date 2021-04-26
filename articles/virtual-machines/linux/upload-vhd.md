---
title: Azure CLı ile özel bir Linux VM yükleme veya kopyalama
description: Kaynak Yöneticisi dağıtım modelini ve Azure CLı 'yi kullanarak özelleştirilmiş bir sanal makineyi karşıya yükleme veya kopyalama
services: virtual-machines
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: ecad48592ecfb6723548a27997cfe1b81545b24a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759608"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Azure CLı ile özel diskten bir Linux VM oluşturma

<!-- rename to create-vm-specialized -->

Bu makalede, özelleştirilmiş bir sanal sabit diskin (VHD) nasıl karşıya yükleneceği ve mevcut bir VHD 'nin Azure 'da nasıl kopyalanacağı gösterilmektedir. Yeni oluşturulan VHD daha sonra yeni Linux sanal makineleri (VM 'Ler) oluşturmak için kullanılır. Gereksinimlerinize bir Linux yükleyip yapılandırabilir ve ardından bu VHD 'yi kullanarak yeni bir Azure sanal makinesi oluşturabilirsiniz.

Özelleştirilmiş diskinizden birden çok VM oluşturmak için önce VM 'niz veya VHD 'nizden bir görüntü oluşturun. Daha fazla bilgi için bkz. [CLI kullanarak Azure VM 'nin özel bir görüntüsünü oluşturma](tutorial-custom-images.md).

Özel bir disk oluşturmak için iki seçeneğiniz vardır:
* VHD’yi karşıya yükleme
* Mevcut bir Azure VM 'yi kopyalama


## <a name="requirements"></a>Gereksinimler
Aşağıdaki adımları tamamlayabilmeniz için şunlar gerekir:

- Azure 'da kullanılmak üzere hazırlanan bir Linux sanal makinesi. Bu makalenin [VM 'Yi hazırla](#prepare-the-vm) bölümünde, SSH Ile bir VM 'ye bağlanmanız Için gereken Azure Linux Aracısı 'nı (waagent) yükleme konusunda nasıl özel bilgiler bulacağınızı ele alınmaktadır.
- Mevcut bir [Azure tarafından onaylanan Linux dağılımınızdan](endorsed-distros.md) VHD dosyası (veya [onaylı olmayan dağıtımlar için BILGILERE](create-upload-generic.md)bakın) VHD biçimindeki bir sanal diske. VM ve VHD oluşturmak için birden çok araç mevcuttur:
  - Kemu veya [](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) [KVM](https://www.linux-kvm.org/page/RunningKVM)'yi yükleyip yapılandırarak, görüntü biçimi olarak VHD 'yi kullanmaya özen gösterin. Gerekirse, [bir görüntüyü ile dönüştürebilirsiniz](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) `qemu-img convert` .
  - Hyper-V ' d i [Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) veya [Windows Server 2012/2012 R2](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))üzerinde de kullanabilirsiniz.

> [!NOTE]
> Yeni VHDX biçimi Azure 'da desteklenmez. Bir VM oluşturduğunuzda, VHD 'yi biçim olarak belirtin. Gerekirse, [QEMU-img Convert](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) veya [Convert-VHD](/powershell/module/hyper-v/convert-vhd) POWERSHELL cmdlet 'INI kullanarak VHDX disklerini VHD 'ye dönüştürebilirsiniz. Azure, Dinamik VHD 'leri karşıya yüklemeyi desteklemez, bu nedenle karşıya yüklemeden önce bu diskleri statik VHD 'lere dönüştürmeniz gerekir. [Go Için Azure VHD yardımcı programları](https://github.com/Microsoft/azure-vhd-utils-for-go) gibi araçları, Azure 'a yükleme işlemi sırasında dinamik diskleri dönüştürmek için kullanabilirsiniz.
> 
> 


- En son [Azure CLI](/cli/azure/install-az-cli2) 'nın yüklü olduğundan ve [az oturum açma](/cli/azure/reference-index#az_login)ile bir Azure hesabında oturum açtığınızdan emin olun.

Aşağıdaki örneklerde,, ve gibi örnek parametre adlarını kendi değerlerinizle değiştirin `myResourceGroup` `mystorageaccount` `mydisks` .

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>VM’yi hazırlama

Azure çeşitli Linux dağıtımlarını destekler (bkz. [onaylı dağıtımlar](endorsed-distros.md)). Aşağıdaki makalelerde, Azure 'da desteklenen çeşitli Linux dağıtımlarını hazırlama anlatılmaktadır:

* [CentOS Tabanlı Dağıtımlar](create-upload-centos.md)
* [Debian Linux](debian-create-upload-vhd.md)
* [Oracle Linux](oracle-create-upload-vhd.md)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
* [SLES ve openSUSE](suse-create-upload-vhd.md)
* [Ubuntu](create-upload-ubuntu.md)
* [Diğerleri: Desteklenmeyen Dağıtımlar](create-upload-generic.md)

Ayrıca, Linux görüntülerini Azure için hazırlama hakkında daha genel ipuçları için bkz. [Linux yükleme notları](create-upload-generic.md#general-linux-installation-notes) .

> [!NOTE]
> [Azure platformu SLA 'sı](https://azure.microsoft.com/support/legal/sla/virtual-machines/) yalnızca, bir onaylama dağıtımlarından biri, [Azure-Endorsed dağıtımlarında Linux](endorsed-distros.md)'ta "Desteklenen sürümler" altında belirtildiği gibi yapılandırma ayrıntıları Ile kullanıldığında Linux çalıştıran VM 'ler için geçerlidir.
> 
> 

## <a name="option-1-upload-a-vhd"></a>Seçenek 1: bir VHD 'YI karşıya yükleme

Artık VHD 'YI bir yönetilen diske doğrudan yükleyebilirsiniz. Yönergeler için bkz. [Azure CLI kullanarak BIR VHD 'Yi Azure 'A yükleme](disks-upload-vhd-to-managed-disk-cli.md).

## <a name="option-2-copy-an-existing-vm"></a>Seçenek 2: var olan bir VM 'yi kopyalama

Ayrıca, Azure 'da özelleştirilmiş bir sanal makine oluşturabilir ve sonra başka bir kopya oluşturmak için işletim sistemi diskini kopyalayabilir ve yeni bir VM 'ye iliştirebilirsiniz. Bu test için uygundur, ancak mevcut bir Azure VM 'yi birden çok yeni VM için model olarak kullanmak istiyorsanız, bunun yerine bir *görüntü* oluşturun. Mevcut bir Azure VM 'den görüntü oluşturma hakkında daha fazla bilgi için bkz. [CLI kullanarak Azure VM 'nin özel görüntüsünü oluşturma](tutorial-custom-images.md).

Var olan bir VM 'yi başka bir bölgeye kopyalamak istiyorsanız, [başka bir bölgedeki bir diskin kopyasını](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)almak için AzCopy kullanmak isteyebilirsiniz. 

Aksi takdirde, VM 'nin bir anlık görüntüsünü alıp anlık görüntüden yeni bir işletim sistemi VHD 'SI oluşturmanız gerekir.

### <a name="create-a-snapshot"></a>Anlık görüntü oluşturma

Bu örnek, *Myresourcegroup* kaynak grubu Içinde *MYVM* adlı bir VM 'nin anlık görüntüsünü oluşturur ve *osdisksnapshot* adlı bir anlık görüntü oluşturur.

```azurecli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Yönetilen disk oluşturma

Anlık görüntüden yeni bir yönetilen disk oluşturun.

Anlık görüntünün KIMLIĞINI alın. Bu örnekte, anlık görüntü *Osdisksnapshot* olarak adlandırılır ve *myresourcegroup* kaynak grubunda yer alabilir.

```azurecli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Yönetilen disk oluşturun. Bu örnekte, diskin standart depolamadaki ve 128 GB olarak boyutlandırıldığından, anlık *yolumuza Mymanageddisk* adlı bir yönetilen disk oluşturacağız.

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Sanal makineyi oluşturma

VM 'nizi [az VM Create](/cli/azure/vm#az_vm_create) ve Attach (--Attach-OS-disk) ile yönetilen disk işletim sistemi diski olarak oluşturun. Aşağıdaki örnek, karşıya yüklenen VHD 'nizden oluşturduğunuz yönetilen diski kullanarak *mynewvm* adlı bir sanal makine oluşturur:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Kaynak VM 'deki kimlik bilgileriyle VM 'de SSH oluşturmanız gerekir. 

## <a name="next-steps"></a>Sonraki adımlar
Özel sanal diskinizi hazırladıktan ve karşıya yükledikten sonra, [Kaynak Yöneticisi ve şablonları kullanma](../../azure-resource-manager/management/overview.md)hakkında daha fazla bilgi edinebilirsiniz. Ayrıca, yeni sanal makinelerinize [bir veri diski eklemek](add-disk.md) isteyebilirsiniz. Sanal makinelerinize erişmeniz gereken uygulamalarınız varsa, [bağlantı noktalarını ve uç noktaları açmayı](nsg-quickstart.md)unutmayın.
