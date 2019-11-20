---
title: Azure 'da Linux sanal makineleri için Cloud-init desteğine genel bakış
description: Microsoft Azure 'deki Cloud-init özelliklerine genel bakış
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/11/2019
ms.author: danis
ms.openlocfilehash: d372b94ac0df4cef3c43fab10686e9bf20633bfe
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034243"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Azure 'da sanal makineler için Cloud-init desteği
Bu makalede, Azure 'da sağlama sırasında bir sanal makineyi (VM) veya sanal makine ölçek kümelerini yapılandırmak üzere [Cloud-init](https://cloudinit.readthedocs.io) için mevcut destek açıklanmaktadır. Bu Cloud-init betikleri, kaynaklar Azure tarafından sağlandıktan sonra ilk önyüklemede çalışır.  

## <a name="cloud-init-overview"></a>Cloud-init genel bakış
[Cloud-init](https://cloudinit.readthedocs.io), Linux VM’sini ilk kez önyüklendiğinde özelleştirmeyi sağlayan, sık kullanılan bir yaklaşımdır. cloud-init’i paket yükleme, dosyalara yazma ve kullanıcılar ile güvenliği yapılandırma işlemleri için kullanabilirsiniz. Cloud-init önyükleme işlemi sırasında çağrıldığı için ek adımlar veya yapılandırmanıza uygulayabileceğiniz gerekli aracı yoktur.  Düzgün bir şekilde biçimlendirme hakkında daha fazla bilgi için `#cloud-config` dosyaları görmek [cloud-init belgeler sitesinde](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` dosyaları, base64 ile kodlanmış metin dosyalarıdır.

Cloud-init, dağıtımlar arasında da çalışır. Örneğin, bir paket yüklemek için **apt-get install** veya **yum install** kullanmazsınız. Bunun yerine, yüklenecek paketlerin listesini tanımlayabilirsiniz. Cloud-init, seçtiğiniz dağıtım için yerel paket yönetim aracını otomatik olarak kullanır.

Etkin olarak desteklenen Linux distro ortaklarımızla birlikte kullanılabilir cloud-init etkinleştirilmiş görüntüleri Azure Market'te sahip olmak için çalışıyoruz. Bu görüntüler, Cloud-init dağıtımlarınızın ve yapılandırmalarının VM 'Ler ve sanal makine ölçek kümeleri ile sorunsuz bir şekilde çalışmasını sağlayacak. Aşağıdaki tabloda, Azure platformunda geçerli cloud-init etkinleştirilmiş görüntüleri kullanılabilirliği açıklanmaktadır:

| Yayımcı | Sunduğu | SKU | Sürüm | cloud-init hazır |
|:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |18.04-LTS |latest |evet | 
|Canonical |UbuntuServer |16.04-LTS |latest |evet | 
|Canonical |UbuntuServer |14.04.5-LTS |latest |evet |
|CoreOS |CoreOS |Dengeli |latest |evet |
|OpenLogic 7,7 |CentOS |7-CI |7.7.20190920 |önizleme |
|RedHat 7,6 |RHEL |7-RAW-CI |7.6.2019072418 |evet |
|RedHat 7,7 |RHEL |7-RAW-CI |7.7.2019081601 |önizleme |
    
Şu anda Azure Stack, Cloud-init kullanarak RHEL 7. x ve CentOS 7. x sağlamasını desteklemez.

* RHEL 7,6, Cloud-init paketi için desteklenen paket: *18.2-1. el7_6.2* 
* RHEL 7,7 (Önizleme) için, Cloud-init paketi, Önizleme paketi: *18.5 -3. EL7*
* CentOS 7,7 (Önizleme) için, Cloud-init paketi, Önizleme paketi: *18.5 -3. EL7. CentOS*

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Cloud-init ve Linux Aracısı (WALA) arasındaki fark nedir?
WALA, VM 'Leri sağlamak ve yapılandırmak ve Azure uzantılarını işlemek için kullanılan bir Azure platforma özgü aracıdır. Mevcut Cloud-init müşterilerinin geçerli Cloud-init betiklerini kullanmasına izin vermek için, VM 'Leri Linux Aracısı yerine Cloud-init kullanacak şekilde yapılandırma görevini geliştirdik.  Linux sistemlerini yapılandırmaya yönelik Cloud-init komut dosyalarında mevcut yatırımlarınızın varsa, bunları etkinleştirmek için **başka bir ayar yapmanız gerekmez** . 

Sağlama zamanında Azure CLı `--custom-data` anahtarını eklemezseniz, WALA VM 'yi sağlamak için gereken en düşük VM sağlama parametrelerini alır ve varsayılan olarak dağıtımı tamamlar.  Cloud-init `--custom-data` anahtarına başvurduğunuzda, özel verilerinizde (bireysel ayarlar veya tam komut dosyası) bulunan her şey WALA varsayılanlarını geçersiz kılar. 

VM 'lerin WALA yapılandırmalarının, en yüksek VM sağlama süresi içinde çalışması için zaman kısıtlanıyor.  VM 'lere uygulanan Cloud init yapılandırmalarının zaman kısıtlamaları yoktur ve zaman aşımına uğraarak dağıtımın başarısız olmasına neden olmaz. 

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Cloud-init etkin bir sanal makine dağıtma
Cloud-init etkin bir sanal makine dağıtmak, dağıtım sırasında bulut-init özellikli bir dağıtıma başvurmak kadar basittir.  Linux dağıtım bakım ve Cloud-init ' i temel Azure yayımlanmış görüntülerine etkinleştirip tümleştirmelerini tercih etmek gerekir. Dağıtmak istediğiniz görüntünün Cloud-init ' i onayladıktan sonra, görüntüyü dağıtmak için Azure CLı ' yi kullanabilirsiniz. 

Bu görüntüyü dağıtmanın ilk adımı, [az Group Create](/cli/azure/group) komutuyla bir kaynak grubu oluşturmaktır. Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır. 

Aşağıdaki örnek *eastus* konumunda *myResourceGroup* adlı bir kaynak grubu oluşturur.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
Sonraki adım, geçerli kabuğunuzun içinde *kabuğunuzda Cloud-init. txt* adlı bir dosya oluşturmak ve aşağıdaki yapılandırmayı yapıştırmaktır. Bu örnekte, dosyayı yerel makinenizde değil Cloud Shell oluşturun. İstediğiniz düzenleyiciyi kullanabilirsiniz. Dosyayı oluşturmak ve kullanılabilir düzenleyicilerin listesini görmek için `sensible-editor cloud-init.txt` adını girin. **Nano** düzenleyiciyi kullanmak için #1 seçin. Başta birinci satır olmak üzere cloud-init dosyasının tamamının doğru bir şekilde kopyalandığından emin olun:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Dosyadan çıkmak için `ctrl-X` tuşuna basın, dosyayı kaydetmek için `y` yazın ve çıkışta dosya adını doğrulamak için `enter` 'e basın.

Son adım [az VM Create](/cli/azure/vm) komutuyla bir VM oluşturmaktır. 

Aşağıdaki örnek, *centos74* adlı bir sanal makine oluşturur ve varsayılan anahtar konumunda henüz yoksa SSH anahtarları oluşturur. Belirli bir anahtar kümesini kullanmak için `--ssh-key-value` seçeneğini kullanın.  `--custom-data` parametresini kullanarak cloud-init yapılandırma dosyanızı geçirin. Dosyayı mevcut çalışma dizininizin dışına kaydettiyseniz *cloud-init.txt* yapılandırmasının tam yolunu belirtin. Aşağıdaki örnek, *centos74*adlı bir sanal makine oluşturur:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

VM oluşturulduğunda Azure CLı, dağıtımınıza özgü bilgileri gösterir. `publicIpAddress` değerini not edin. Bu adres, VM’ye erişmek için kullanılır.  VM 'nin oluşturulması, yüklenecek paketlerin ve başlatılacak uygulamayı biraz zaman alır. Azure CLI sizi isteme geri döndürdükten sonra çalışmaya devam eden arka plan görevleri vardır. VM 'de SSH oluşturabilir ve Cloud-init günlüklerini görüntülemek için sorun giderme bölümünde özetlenen adımları kullanabilirsiniz. 

## <a name="troubleshooting-cloud-init"></a>Cloud-init sorunlarını giderme
VM sağlandıktan sonra Cloud-init, VM 'yi yapılandırmak için `--custom-data` tanımlanmış tüm modüller ve betiklerle çalışır.  Yapılandırmadan herhangi bir hata veya iş sorunu gidermeniz gerekiyorsa, **/var/log/Cloud-init.log**dosyasında bulunan Cloud-init günlüğünde modül adını (örneğin`disk_setup` veya `runcmd`) aramanız gerekir.

> [!NOTE]
> Her modül hatası önemli bir Cloud-init genel yapılandırma hatasına neden olmaz. Örneğin, `runcmd` modülünü kullanarak, komut dosyası başarısız olursa, runcmd modülü yürütüldüğü için Cloud-init hala sağlama başarılı olur.

Cloud-init günlüğü hakkında daha fazla bilgi için [Cloud-init belgelerine](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) bakın 

## <a name="next-steps"></a>Sonraki adımlar
Cloud-init yapılandırma değişikliklerinin örnekleri için aşağıdaki belgelere bakın:
 
- [VM 'ye ek bir Linux kullanıcısı ekleme](cloudinit-add-user.md)
- [İlk önyüklemede var olan paketleri güncelleştirmek için bir paket yöneticisi çalıştırın](cloudinit-update-vm.md)
- [VM yerel ana bilgisayar adını değiştir](cloudinit-update-vm-hostname.md) 
- [Uygulama paketi yüklemesi, yapılandırma dosyalarını güncelleştirme ve anahtarları ekleme](tutorial-automate-vm-deployment.md)
