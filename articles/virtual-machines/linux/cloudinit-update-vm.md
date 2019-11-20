---
title: Azure 'da bir Linux sanal makinesine paket güncelleştirmek ve onları yüklemek için Cloud-init kullanma
description: Azure CLı ile oluşturma sırasında bir Linux sanal makinesine paket güncelleştirmek ve yüklemek için Cloud-init kullanma
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
ms.date: 04/20/2018
ms.author: rclaus
ms.openlocfilehash: ddea412598e02be7d71d5a3efafa444a5dc19e8c
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036721"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>Azure 'daki bir Linux sanal makinesine paket güncelleştirmek ve onları yüklemek için Cloud-init kullanma
Bu makalede, Azure 'da sağlama zamanında bir Linux sanal makinesi (VM) veya sanal makine ölçek kümeleri (VMSS) üzerinde paket güncelleştirmek için [Cloud-init](https://cloudinit.readthedocs.io) ' in nasıl kullanılacağı gösterilmektedir. Bu Cloud-init betikleri, kaynaklar Azure tarafından sağlandıktan sonra ilk önyüklemede çalışır. Cloud-init 'in Azure 'da ve desteklenen Linux korumalar 'daki yerel olarak nasıl çalıştığı hakkında daha fazla bilgi için bkz. [Cloud-init Overview](using-cloud-init.md)

## <a name="update-a-vm-with-cloud-init"></a>Cloud-init ile VM güncelleştirme
Güvenlik nedeniyle, ilk önyüklemede en son güncelleştirmeleri uygulamak için bir VM yapılandırmak isteyebilirsiniz. Cloud-init farklı Linux kaldırmalar arasında çalıştığından, Paket Yöneticisi için `apt` veya `yum` belirtmeniz gerekmez. Bunun yerine, `package_upgrade` tanımlar ve Cloud-init işleminin kullanımda olan kullanım için uygun mekanizmayı belirlemesini sağlayabilirsiniz. Bu iş akışı, distros genelinde aynı Cloud-init betiklerini kullanmanıza olanak sağlar.

Yükseltme işlemini eylemde görmek için, geçerli kabuğunuzun *cloud_init_upgrade. txt* adlı bir dosya oluşturun ve aşağıdaki yapılandırmayı yapıştırın. Bu örnekte, dosyayı yerel makinenizde değil Cloud Shell oluşturun. İstediğiniz düzenleyiciyi kullanabilirsiniz. Dosyayı oluşturmak ve kullanılabilir düzenleyicilerin listesini görmek için `sensible-editor cloud_init_upgrade.txt` adını girin. **Nano** düzenleyiciyi kullanmak için #1 seçin. Tüm Cloud-init dosyalarının, özellikle de ilk satırda doğru şekilde kopyalandığından emin olun.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

Bu görüntüyü dağıtılmadan önce [az Group Create](/cli/azure/group) komutuyla bir kaynak grubu oluşturmanız gerekir. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. Aşağıdaki örnek *eastus* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Şimdi [az VM Create](/cli/azure/vm) Ile bir VM oluşturun ve Cloud-init dosyasını `--custom-data cloud_init_upgrade.txt` ile aşağıdaki gibi belirtin:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

Önceki komutun çıktısında gösterilen sanal makinenizin genel IP adresine SSH. Kendi **Publicıpaddress** değerini aşağıdaki şekilde girin:

```bash
ssh <publicIpAddress>
```

Paket Yönetimi aracını çalıştırın ve güncelleştirmeleri denetleyin.

```bash
sudo yum update
```

Cloud-init için denetlenir ve önyükleme sırasında güncelleştirmeler yüklüyse, uygulanacak başka bir güncelleştirme olmamalıdır.  Güncelleştirme işlemini, değiştirilen paket sayısını ve `yum history` çalıştırıp `httpd` yüklemesini görürsünüz ve aşağıdakine benzer bir çıktıyı gözden geçirin.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>Sonraki adımlar
Yapılandırma değişikliklerine yönelik ek Cloud-init örnekleri için aşağıdakilere bakın:
 
- [VM 'ye ek bir Linux kullanıcısı ekleme](cloudinit-add-user.md)
- [İlk önyüklemede var olan paketleri güncelleştirmek için bir paket yöneticisi çalıştırın](cloudinit-update-vm.md)
- [VM yerel ana bilgisayar adını değiştir](cloudinit-update-vm-hostname.md) 
- [Uygulama paketi yüklemesi, yapılandırma dosyalarını güncelleştirme ve anahtarları ekleme](tutorial-automate-vm-deployment.md)
