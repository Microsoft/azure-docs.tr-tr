---
title: Azure 'da bir Linux sanal makinesi için konak adı ayarlamak için Cloud-init kullanın
description: Azure CLı ile oluşturma sırasında bir Linux sanal makinesini özelleştirmek için Cloud-init kullanma
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
ms.openlocfilehash: adac9dc41aaba7ce6bfd9f01917d647174b41282
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036708"
---
# <a name="use-cloud-init-to-set-hostname-for-a-linux-vm-in-azure"></a>Azure 'da bir Linux sanal makinesi için konak adı ayarlamak için Cloud-init kullanın
Bu makalede, Azure 'da sağlama zamanında bir sanal makinede (VM) veya sanal makine ölçek kümelerinde (VMSS) belirli bir ana bilgisayar adını yapılandırmak için [Cloud-init](https://cloudinit.readthedocs.io) ' in nasıl kullanılacağı gösterilmektedir. Bu Cloud-init betikleri, kaynaklar Azure tarafından sağlandıktan sonra ilk önyüklemede çalışır. Cloud-init 'in Azure 'da ve desteklenen Linux korumalar 'daki yerel olarak nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Cloud-init Overview](using-cloud-init.md)

## <a name="set-the-hostname-with-cloud-init"></a>Konak adını Cloud-init ile ayarlama
Varsayılan olarak, ana bilgisayar adı, Azure 'da yeni bir sanal makine oluştururken VM adıyla aynıdır.  Azure 'da [az VM Create](/cli/azure/vm)Ile bir VM oluşturduğunuzda, bu varsayılan ana bilgisayar adını değiştirmek için bir Cloud-init betiği çalıştırmak için, `--custom-data` anahtarıyla Cloud-init dosyasını belirtin.  

Yükseltme işlemini eylemde görmek için, geçerli kabuğunuzun *cloud_init_hostname. txt* adlı bir dosya oluşturun ve aşağıdaki yapılandırmayı yapıştırın. Bu örnekte, dosyayı yerel makinenizde değil Cloud Shell oluşturun. İstediğiniz düzenleyiciyi kullanabilirsiniz. Dosyayı oluşturmak ve kullanılabilir düzenleyicilerin listesini görmek için `sensible-editor cloud_init_hostname.txt` adını girin. **Nano** düzenleyiciyi kullanmak için #1 seçin. Tüm Cloud-init dosyalarının, özellikle de ilk satırda doğru şekilde kopyalandığından emin olun.  

```yaml
#cloud-config
hostname: myhostname
```

Bu görüntüyü dağıtılmadan önce [az Group Create](/cli/azure/group) komutuyla bir kaynak grubu oluşturmanız gerekir. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Aşağıdaki örnek *eastus* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Şimdi [az VM Create](/cli/azure/vm) Ile bir VM oluşturun ve Cloud-init dosyasını `--custom-data cloud_init_hostname.txt` ile aşağıdaki gibi belirtin:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_hostname.txt \
  --generate-ssh-keys 
```

Azure CLı, oluşturulduktan sonra VM hakkındaki bilgileri gösterir. Sanal makinenize SSH eklemek için `publicIpAddress` kullanın. Kendi adresinizi aşağıdaki gibi girin:

```bash
ssh <publicIpAddress>
```

VM adını görmek için `hostname` komutunu aşağıdaki gibi kullanın:

```bash
hostname
```

Aşağıdaki örnek çıktıda gösterildiği gibi, VM, ana bilgisayar adını Cloud-init dosyasında belirlenen değer olarak raporlemelidir:

```bash
myhostname
```

## <a name="next-steps"></a>Sonraki adımlar
Yapılandırma değişikliklerine yönelik ek Cloud-init örnekleri için aşağıdakilere bakın:
 
- [VM 'ye ek bir Linux kullanıcısı ekleme](cloudinit-add-user.md)
- [İlk önyüklemede var olan paketleri güncelleştirmek için bir paket yöneticisi çalıştırın](cloudinit-update-vm.md)
- [VM yerel ana bilgisayar adını değiştir](cloudinit-update-vm-hostname.md) 
- [Uygulama paketi yüklemesi, yapılandırma dosyalarını güncelleştirme ve anahtarları ekleme](tutorial-automate-vm-deployment.md)
