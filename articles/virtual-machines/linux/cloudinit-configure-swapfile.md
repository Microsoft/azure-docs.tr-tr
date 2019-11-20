---
title: Linux VM 'de takas bölümü yapılandırmak için Cloud-init kullanma
description: Azure CLı ile oluşturma sırasında bir Linux sanal makinesinde takas bölümü yapılandırmak için Cloud-init kullanma
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: fad73a7dbed9351d684ef2464cf2fa6fa3489290
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036766"
---
# <a name="use-cloud-init-to-configure-a-swap-partition-on-a-linux-vm"></a>Linux VM 'de takas bölümü yapılandırmak için Cloud-init kullanma
Bu makalede, [Cloud-init](https://cloudinit.readthedocs.io) kullanarak çeşitli Linux dağıtımlarına takas bölümünü nasıl yapılandırabileceğiniz gösterilmektedir. Değiştirme bölümü geleneksel olarak Linux Aracısı (WALA) tarafından hangi dağıtımların gerekli olduğunu temel alan bir şekilde yapılandırılmıştır.  Bu belge, Cloud-init ' i kullanarak sağlama süresi boyunca değiştirme bölümünü isteğe bağlı olarak oluşturma sürecini özetler.  Cloud-init 'in Azure 'da ve desteklenen Linux korumalar 'daki yerel olarak nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Cloud-init Overview](using-cloud-init.md)

## <a name="create-swap-partition-for-ubuntu-based-images"></a>Ubuntu tabanlı görüntüler için takas bölümü oluşturma
Varsayılan olarak, Azure 'da Ubuntu Galerisi görüntüleri takas bölümleri oluşturmaz. Cloud-init kullanarak VM sağlama süresi sırasında bölüm yapılandırmasını değiştirme özelliğini etkinleştirmek için lütfen Ubuntu wiki üzerinde [Azureswappartitions belgesine](https://wiki.ubuntu.com/AzureSwapPartitions) bakın.

## <a name="create-swap-partition-for-red-hat-and-centos-based-images"></a>Red Hat ve CentOS tabanlı görüntüler için takas bölümü oluşturma

Geçerli kabuğunuzun *cloud_init_swappart. txt* adlı bir dosya oluşturun ve aşağıdaki yapılandırmayı yapıştırın. Bu örnekte, dosyayı yerel makinenizde değil Cloud Shell oluşturun. İstediğiniz düzenleyiciyi kullanabilirsiniz. Dosyayı oluşturmak ve kullanılabilir düzenleyicilerin listesini görmek için `sensible-editor cloud_init_swappart.txt` adını girin. **Nano** düzenleyiciyi kullanmak için #1 seçin. Tüm Cloud-init dosyalarının, özellikle de ilk satırda doğru şekilde kopyalandığından emin olun.  

```yaml
#cloud-config
disk_setup:
  ephemeral0:
    table_type: gpt
    layout: [66, [33,82]]
    overwrite: true
fs_setup:
  - device: ephemeral0.1
    filesystem: ext4
  - device: ephemeral0.2
    filesystem: swap
mounts:
  - ["ephemeral0.1", "/mnt"]
  - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
```

Bu görüntüyü dağıtılmadan önce [az Group Create](/cli/azure/group) komutuyla bir kaynak grubu oluşturmanız gerekir. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Aşağıdaki örnek *eastus* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Şimdi [az VM Create](/cli/azure/vm) Ile bir VM oluşturun ve Cloud-init dosyasını `--custom-data cloud_init_swappart.txt` ile aşağıdaki gibi belirtin:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swappart.txt \
  --generate-ssh-keys 
```

## <a name="verify-swap-partition-was-created"></a>Değiştirme bölümünün oluşturulduğunu doğrula
Önceki komutun çıktısında gösterilen sanal makinenizin genel IP adresine SSH. Kendi **Publicıpaddress** değerini aşağıdaki şekilde girin:

```bash
ssh <publicIpAddress>
```

VM 'ye bir Sshtikten sonra, takas bölümünün oluşturulup oluşturulmadığımsını denetleyin

```bash
swapon -s
```

Bu komutun çıktısı şuna benzemelidir:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Bir değiştirme bölümü yapılandırılmış mevcut bir Azure görüntünüz varsa ve yeni görüntüler için takas bölümü yapılandırmasını değiştirmek istiyorsanız, var olan takas bölümünü kaldırmanız gerekir. Daha fazla ayrıntı için lütfen bkz. ' Cloud-init tarafından sağlanacak resimleri özelleştirme ' belgesi.

## <a name="next-steps"></a>Sonraki adımlar
Yapılandırma değişikliklerine yönelik ek Cloud-init örnekleri için aşağıdakilere bakın:
 
- [VM 'ye ek bir Linux kullanıcısı ekleme](cloudinit-add-user.md)
- [İlk önyüklemede var olan paketleri güncelleştirmek için bir paket yöneticisi çalıştırın](cloudinit-update-vm.md)
- [VM yerel ana bilgisayar adını değiştir](cloudinit-update-vm-hostname.md) 
- [Uygulama paketi yüklemesi, yapılandırma dosyalarını güncelleştirme ve anahtarları ekleme](tutorial-automate-vm-deployment.md)
