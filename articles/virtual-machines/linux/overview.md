---
title: Azure 'da Linux VM 'lerine genel bakış
description: Linux sanal makineleriyle Azure İşlem, Depolama ve Ağ hizmetlerini açıklar.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: rickstercdn
manager: gwallace
editor: ''
ms.assetid: 7965a80f-ea24-4cc2-bc43-60b574101902
ms.service: virtual-machines-linux
ms.topic: overview
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/29/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: dc0145e23b940f6aca9021186254b966592f343d
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035352"
---
# <a name="azure-and-linux"></a>Azure ve Linux
Microsoft Azure; çözümlerinizi barındırmak için ideal olan ve giderek büyüyen bir analiz, sanal makine, veritabanı, mobil kullanım, ağ, depolama ve web dahil tümleşik genel bulut hizmetleri koleksiyonudur.  Microsoft Azure şirket içi donanım için yatırım yapmanıza gerek olmadan istediğiniz zaman yalnızca kullandığınız hizmetler için ödeme yapmanızı sağlayan ölçeklenebilir bir bilgi işlem platformu sunar.  Azure, çözümlerinizin ölçeğini artırmaya hazır olduğunuzda müşterilerinizin ihtiyaçlarını karşılamak için gereken ölçeğe yükseltilmek için hazırdır.

Amazon AWS'nin çeşitli özelliklerini tanıyorsanız, Azure - AWS [tanım eşleme belgesini](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) inceleyebilirsiniz.

## <a name="regions"></a>Bölgeler
Microsoft Azure kaynakları, dünyanın farklı yerindeki çeşitli coğrafi bölgelere dağıtılmıştır.  "Bölge", tek bir coğrafi alanda bulunan birden çok veri merkezini temsil eder. Ağustos 2018 itibarıyla Azure, dünyanın her yanında genel olarak kullanılabilen 42 bölgeye (diğer bulut sağlayıcılarından daha fazla küresel bölgeye) sahiptir ve 12 bölgenin daha duyurusu yapılmıştır. Mevcut ve yeni duyurulan bölgelerin güncel bir listesi şu sayfada bulunabilir:

* [Azure Bölgeleri](https://azure.microsoft.com/regions/)

## <a name="availability"></a>Kullanılabilirlik
Azure, sanal makineyi tüm diskler için premium depolamayla dağıtmanız koşuluyla, tek örnekli sanal makinelerde endüstri lideri %99,9 kullanılabilirlik Hizmet Düzeyi Sözleşmesi'nin duyurusunu yaptı.  Dağıtımınızın standart %99,95 VM Hizmet Düzeyi Sözleşmesinin kapsamına girebilmesi için iş yükünüzü çalıştıran iki veya daha fazla VM’yi yine bir kullanılabilirlik kümesi içinde dağıtmanız gerekir. Bir kullanılabilirlik kümesi, VM’lerinizin Azure veri merkezlerinde birden çok hata etki alanına dağıtılmasını ve aynı zamanda dağıtımlarının farklı bakım aralıklarına sahip konaklara yapılmasını sağlar. [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) şartları, Azure’un tamamının kullanılabilirlik garantisini açıklamaktadır.

## <a name="managed-disks"></a>Yönetilen Diskler

Yönetilen Diskler, Azure Depolama hesabı oluşturma ve yönetme işlemini arka planda gerçekleştirir ve depolama hesabının ölçeklenebilirlik sınırları hakkında endişe etmeniz gerekmez. Azure’ın diski oluşturup yönetebilmesi için disk boyutunu ve performans katmanını (Standart veya Premium) belirtmeniz yeterlidir. Disk eklediğinizde veya VM ölçeğini artırıp azalttığınızda kullanılan depolama alanı konusunda endişelenmeniz gerekmez. Yeni VM'ler oluşturuyorsanız, VM'leri Yönetilen işletim sistemi ve veri diskleriyle oluşturmak için [Azure CLI](quick-create-cli.md) veya Azure portalını kullanın. Yönetilmeyen diskleri olan VM'leriniz varsa, [VM'leri Yönetilen Disklerle desteklenecek şekilde dönüştürebilirsiniz](convert-unmanaged-to-managed-disks.md).

Ayrıca, her Azure bölgesinde bir depolama hesabındaki özel görüntülerinizi yönetebilir ve aynı abonelikte yüzlerce VM oluşturmak için kullanabilirsiniz. Yönetilen Diskler hakkında daha fazla bilgi için bkz. [Yönetilen Disklere Genel Bakış](../linux/managed-disks-overview.md).

## <a name="azure-virtual-machines--instances"></a>Azure Sanal Makineler ve Örnekleri
Microsoft Azure, çeşitli iş ortakları tarafından sağlanan ve bakımı yapılan bir dizi popüler Linux dağıtımının çalıştırılmasını destekler.  Azure Market'te Red Hat Enterprise, CentOS, SUSE Linux Enterprise, Debian, Ubuntu, CoreOS, RancherOS, FreeBSD gibi daha birçok dağıtım bulabilirsiniz. Microsoft, [Azure destekli Linux Dağıtımları](endorsed-distros.md) listesine daha da fazla çeşitleme katmak için çeşitli Linux topluluklarıyla etkin bir çalışma sürdürüyor.

Tercih ettiğiniz Linux dağıtımı şu anda galeride yoksa, [Azure'da Linux VHD'si oluşturma ve karşıya yükleme](create-upload-generic.md) yoluyla "Kendi Linux VM'inizi getirebilirsiniz".

Azure sanal makineleri, çok çeşitli bilgi işlem çözümlerini çevik bir şekilde dağıtmanıza olanak tanır. Neredeyse tüm işletim sistemlerinde (Windows, Linux veya giderek büyüyen iş ortağı listesindeki birinin özel olarak oluşturduğu bir işletim sistemi) hemen her iş yükünü ve dili dağıtabilirsiniz. Hala aradığınızı bulamadınız mı?  Endişelenmeyin; şirket içinden kendi görüntülerinizi de getirebilirsiniz.

## <a name="vm-sizes"></a>VM Boyutları
Kullandığınız VM’nin [boyutu](sizes.md), çalıştırmak istediğiniz iş yüküne göre belirlenir. Seçtiğiniz boyut işlemci gücü, bellek ve depolama kapasitesi gibi ölçütleri belirler. Azure çok sayıda kullanım türünü desteklemek için büyük çeşitlilikteki boyutları sunar.

Azure’un ücretlendirdiği, VM’nin boyutu ve işletim sistemi temelinde [saatlik fiyat](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). Kısmi saatler için, Azure yalnızca kullanılan dakikaları ücretlendirir. Depolama ayrı olarak fiyatlandırılır ve ücretlendirilir.

## <a name="automation"></a>Otomasyon
Düzgün bir DevOps kültürünü başarmak için tüm altyapı kod olmalıdır.  Altyapının tamamı kodda olduğunda, kolayca yeniden oluşturulabilir (Phoenix Sunucuları).  Azure tüm önemli otomasyon araçlarıyla, örneğin Ansible, Chef, SaltStack ve Puppet ile çalışır.  Azure'un ayrıca kendi otomasyon aracı da vardır:

* [Azure Şablonları](create-ssh-secured-vm-from-template.md)
* [Azure VMAccess](using-vmaccess-extension.md)

Azure, bunu destekleyen Linux Dağıtımlarının çoğunda [cloud-init](https://cloud-init.io/) desteği sunar.  Şu anda Canonical'ın Ubuntu VM'leri varsayılan olarak cloud-init etkinleştirilmiş olarak dağıtılmaktadır.  Red Hat tarafından kullanıma sunulan RHEL, CentOS ve Fedora cloud-init desteğine sahiptir ancak Red Hat'te tutulan Azure görüntülerinde şu anda cloud-init yüklü değildir.  Rad Hat işletim sistemi ailesinde cloud-init kullanmak için, cloud-init'in yüklü olduğu bir özel görüntü oluşturmanız gerekir.

* [Azure Linux VM'lerinde cloud-init kullanma](using-cloud-init.md)

## <a name="quotas"></a>Kotalar
Her Azure Aboneliğinizde, projeniz için çok fazla sayıda VM dağıtımını etkileyebilecek varsayılan kota sınırları vardır. Geçerli sınırlar abonelik başına her bölge için 20 VM olarak belirlenmiştir.  Kota sınırları, sınır artışı isteyen bir destek bileti doldurarak hızla ve kolayca yükseltilebilir.  Kota sınırları hakkındaki diğer ayrıntılar için:

* [Azure Aboneliği Hizmet Sınırları](../../azure-subscription-service-limits.md)

## <a name="partners"></a>İş Ortakları
Microsoft, sağlanan görüntülerin güncelleştirilmiş ve Azure çalışma zamanı için iyileştirilmiş olduğundan emin olmak için iş ortaklarıyla yakın bir çalışma sürdürür.  Azure iş ortakları hakkında daha fazla bilgi için şu bağlantılara göz atın:

* Azure[ Destekli Dağıtımlarda](endorsed-distros.md) Linux
* SUSE - [Azure Market - SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/marketplace/apps/SUSE.SLES?tab=Overview)
* Red Hat - [Azure Market - Red Hat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)
* Canonical - [Azure Market - Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)
* Debian - [Azure Market - Debian 8 "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)
* FreeBSD- [Azure Marketi-freebsd 10,4](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
* CoreOS - [Azure Market - CoreOS (Stable)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)
* RancherOS - [Azure Market - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)
* Bitnami - [Azure için Bitnami Kitaplığı](https://azure.bitnami.com/)
* Mesosphere - [Azure Market - Azure'da Mesosphere DC/OS](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)
* Docker - [Azure Market - Docker Swarm ile Azure Container Service](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)
* Jenkins - [Azure Market - CloudBees Jenkins Platform](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cloudbees.cloudbees-core-contact)

## <a name="getting-started-with-linux-on-azure"></a>Azure'da Linux'ı kullanmaya başlama
Azure'ı kullanmaya başlamak için bir Azure hesabına, yüklü Azure CLI'ye ve bir çift SSH genel ve özel anahtarına ihtiyacınız vardır.

### <a name="sign-up-for-an-account"></a>Hesap edinmek için kaydolun
Azure Cloud kullanmanın ilk adımı Azure hesabı için kaydolmaktır.  Başlamak için [Azure Hesap Kaydı](https://azure.microsoft.com/pricing/free-trial/) sayfasına gidin.

### <a name="install-the-cli"></a>CLI'yi yükleme
Yeni Azure hesabınızla, web tabanlı bir yönetim paneli olan Azure Portal'ı kullanmaya hemen başlayabilirsiniz.  Azure Cloud'u komut satırı üzerinden yönetmek için `azure-cli`'yi yüklersiniz.  Mac veya Linux iş istasyonunuza [Azure CLI](/cli/azure/install-azure-cli) yükleyin.

### <a name="create-an-ssh-key-pair"></a>SSH anahtar çifti oluşturma
Artık Azure hesabınız, Azure web portalınız ve Azure CLI'niz vardır.  Sonraki adım, parola kullanmadan Linux'ta SSH için kullanılan SSH anahtar çiftini oluşturmaktır.  Parolasız oturum açmak ve daha iyi bir güvenlik elde etmek için [Linux ve Mac'te SSH anahtarları oluşturun](mac-create-ssh-keys.md).

### <a name="create-a-vm-using-the-cli"></a>CLI kullanarak VM oluşturma
CLI kullanarak Linux VM'si oluşturmak, çalıştığınız terminalden çıkmadan VM dağıtımı yapmanın hızlı bir yoludur.  Web portalında belirtebileceğiniz her şey, bir komut satırı bayrağı veya anahtarı aracılığıyla sağlanır.  

* [Portal CLI kullanarak Linux VM oluşturma](quick-create-cli.md)

### <a name="create-a-vm-in-the-portal"></a>Portalda VM oluşturma
Azure web portalında Linux VM'si oluşturmak, dağıtıma ulaşmak için çeşitli seçenekleri işaretlemenin ve tıklamanın kolay bir yoludur.  Komut satırı bayraklarını veya anahtarlarını kullanmak yerine, çeşitli seçenekler ve ayarların bulunduğu güzel bir web düzeni görebilirsiniz.  Komut satırı arabirimi üzerinden sağlanan her şey portalda da sağlanır.

* [Portal kullanarak Linux VM oluşturma](quick-create-portal.md)

### <a name="log-in-using-ssh-without-a-password"></a>Parola olmadan SSH kullanarak oturum açma
Artık VM Azure'da çalışıyor ve oturum açmaya hazırsınız.  SSH üzerinden parolalar kullanarak oturum açmak güvenli değildir ve zaman alır.  Oturum açmanın hem en güvenli hem de en hızlı yolu SSH anahtarlarını kullanmaktır.  Linux VM'nizi portal veya CLI aracılığıyla oluştururken, iki kimlik doğrulama seçeneğiniz vardır.  SSH için parolayı seçerseniz, Azure VM'yi parolalarla oturum açmaya izin verecek şekilde yapılandırır.  SSH ortak anahtarı kullanmayı seçerseniz, Azure VM'yi yalnızca SSH anahtarlarıyla oturum açmaya izin verecek şekilde yapılandırır ve parolayla oturum açmayı devre dışı bırakır. Yalnızca SSH anahtarıyla oturum açmaya izin vererek Linux VM'nizin güvenliğini sağlamak için, portalda veya CLI'de VM'yi oluşturma sırasında SSH ortak anahtar seçeneğini kullanın.

## <a name="related-azure-components"></a>İlgili Azure bileşenleri
## <a name="storage"></a>Depolama
* [Microsoft Azure Depolama'ya Giriş](../../storage/common/storage-introduction.md)
* [azure-cli kullanarak Linux VM'sine disk ekleme](add-disk.md)
* [Azure Portal’da Linux VM’sine veri diski ekleme](attach-disk-portal.md)

## <a name="networking"></a>Ağ
* [Sanal Ağ’a Genel Bakış](../../virtual-network/virtual-networks-overview.md)
* [Azure’da IP adresleri](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Azure'da Linux VM'sine bağlantı noktalarını açma](nsg-quickstart.md)
* [Azure portalda Tam Etki Alanı Adı oluşturma](portal-create-fqdn.md)

## <a name="containers"></a>Kapsayıcılar
* [Azure’da Sanal Makineler ve Kapsayıcılar](containers.md)
* [Azure Container Service’e giriş](../../container-service/container-service-intro.md)
* [Azure Container Service kümesi dağıtma](../../container-service/dcos-swarm/container-service-deployment.md)

## <a name="next-steps"></a>Sonraki adımlar
Artık Azure üzerinde Linux'la ilgili genel bakış bilgilerine sahipsiniz.  Sonraki adım, işe girişip birkaç VM oluşturmaktır!

* [AzureCLI üzerinden sık kullanılan görevlere yönelik giderek büyüyen örnek betikler listesini inceleyin](cli-samples.md)
