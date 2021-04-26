---
title: Azure 'da Linux VM 'lerine genel bakış
description: Azure 'da Linux sanal makinelerine genel bakış.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: fe1db967dac8a76119733f930e54a38160395932
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549847"
---
# <a name="linux-virtual-machines-in-azure"></a>Azure’da Linux sanal makineleri

Azure Virtual Machines (VM) Azure’un sunduğu [isteğe bağlı ve ölçeklenebilir işlem kaynağı türlerinden](/azure/architecture/guide/technology-choices/compute-decision-tree) biridir. Genellikle, sunulan diğer seçimlere göre bilgi işlem ortamınız üzerinde daha fazla denetime ihtiyacınız varsa, bir VM seçersiniz. Bu makalede VM oluşturmadan önce dikkat etmeniz gereken bilgilere, oluşturma yöntemine ve yönetim seçeneklerine yer verilmiştir.

Bir Azure VM, onu çalıştıran fiziksel donanımı satın almanıza ve muhafaza etmenize gerek kalmadan size sanallaştırma esnekliği sunar. Ancak yine de yapılandırma, düzeltme eki uygulama ve yazılım yükleme gibi görevleri gerçekleştirerek VM’nin bakımını yapmanız gerekir.

Azure sanal makineleri farklı amaçlarla kullanılabilir. Bazı örnekler şunlardır:

* **Geliştirme ve test etme** – Azure VM’leri bir uygulama için kod yazmak ve test yapmak için özel yapılandırmalara sahip bilgisayarları hızlıca oluşturmanızı sağlar.
* **Uygulamaları buluta taşıma** – Talep düzeyinde dalgalanmalar olan uygulamalarınızı Azure üzerindeki bir VM’de çalıştırmak mantıklıdır. İhtiyaç duyduğunuzda oluşturulan ek VM’ler için ödeme yapar, ihtiyaç kalmadığında bunları kapatırsınız.
* **Veri merkezini genişletme** – Bir Azure sanal ağı üzerindeki sanal makineleri kolayca kuruluşunuzun ağına bağlayabilirsiniz.

Uygulamanız tarafından kullanılan VM sayısı, ihtiyaçlarınıza göre ölçeklendirilebilir.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>VM oluşturmadan önce dikkat etmem gereken noktalar nelerdir?
Azure’da uygulama altyapısı oluştururken [dikkate almanız gereken tasarım ölçütleri](/azure/architecture/reference-architectures/n-tier/linux-vm) vardır. Başlamadan önce dikkat etmeniz gereken VM özellikleri şunlardır:

* Uygulama kaynaklarınızın adları
* Kaynakların depolanacağı konum
* VM’nin boyutu
* Oluşturulabilecek en fazla VM sayısı
* VM üzerinde çalışan işletim sistemi
* VM’nin başlatıldıktan sonraki yapılandırması
* VM’nin ihtiyaç duyduğu kaynaklar

### <a name="locations"></a>Konumlar
Azure’da oluşturulan tüm kaynaklar, dünyanın farklı yerindeki çeşitli [coğrafi bölgelere](https://azure.microsoft.com/regions/) dağıtılır. VM oluştururken bu bölgeler genelde **konum** olarak adlandırılır. Bir VM için konum, sanal sabit disklerin depolandığı yeri belirtir.

Bu tabloda, kullanılabilen konumların listesini edinme yöntemlerinden bazıları gösterilmektedir.

| Yöntem | Açıklama |
| --- | --- |
| Azure portalı |VM oluştururken listeden konum seçin. |
| Azure PowerShell |[Get-AzLocation](/powershell/module/az.resources/get-azlocation) komutunu kullanın. |
| REST API |[List locations](/rest/api/resources/subscriptions) işlemini kullanın. |
| Azure CLI’si |[az account list-locations](/cli/azure/account) işlemini kullanın. |

## <a name="availability"></a>Kullanılabilirlik
Azure, sanal makineyi tüm diskler için premium depolamayla dağıtmanız koşuluyla, tek örnekli sanal makinelerde endüstri lideri %99,9 kullanılabilirlik Hizmet Düzeyi Sözleşmesi'nin duyurusunu yaptı.  Dağıtımınızın standart %99,95 VM Hizmet Düzeyi Sözleşmesinin kapsamına girebilmesi için iş yükünüzü çalıştıran iki veya daha fazla VM’yi yine bir kullanılabilirlik kümesi içinde dağıtmanız gerekir. Bir kullanılabilirlik kümesi, VM’lerinizin Azure veri merkezlerinde birden çok hata etki alanına dağıtılmasını ve aynı zamanda dağıtımlarının farklı bakım aralıklarına sahip konaklara yapılmasını sağlar. [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) şartları, Azure’un tamamının kullanılabilirlik garantisini açıklamaktadır.

## <a name="vm-size"></a>VM Boyutu
Kullandığınız VM’nin [boyutu](../sizes.md), çalıştırmak istediğiniz iş yüküne göre belirlenir. Seçtiğiniz boyut işlemci gücü, bellek ve depolama kapasitesi gibi ölçütleri belirler. Azure çok sayıda kullanım türünü desteklemek için büyük çeşitlilikteki boyutları sunar.

Azure, VM 'nin boyut ve işletim sistemine göre [saatlik bir fiyat](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) ücretlendirir. Kısmi saatler için, Azure yalnızca kullanılan dakikaları ücretlendirir. Depolama ayrı olarak fiyatlandırılır ve ücretlendirilir.

## <a name="vm-limits"></a>VM Sınırları
Aboneliğinizde, projeniz için birden fazla VM dağıtımını etkileyebilecek varsayılan [kota sınırları](../../azure-resource-manager/management/azure-subscription-service-limits.md) vardır. Geçerli sınırlar abonelik başına her bölge için 20 VM olarak belirlenmiştir. Sınırların [yükseltilmesini talep etmek için destek bileti oluşturabilirsiniz](../../azure-portal/supportability/resource-manager-core-quotas-request.md)

## <a name="managed-disks"></a>Yönetilen Diskler

Yönetilen Diskler, Azure Depolama hesabı oluşturma ve yönetme işlemini arka planda gerçekleştirir ve depolama hesabının ölçeklenebilirlik sınırları hakkında endişe etmeniz gerekmez. Azure’ın diski oluşturup yönetebilmesi için disk boyutunu ve performans katmanını (Standart veya Premium) belirtmeniz yeterlidir. Disk eklediğinizde veya VM ölçeğini artırıp azalttığınızda kullanılan depolama alanı konusunda endişelenmeniz gerekmez. Yeni VM'ler oluşturuyorsanız, VM'leri Yönetilen işletim sistemi ve veri diskleriyle oluşturmak için [Azure CLI](quick-create-cli.md) veya Azure portalını kullanın. Yönetilmeyen diskleri olan VM'leriniz varsa, [VM'leri Yönetilen Disklerle desteklenecek şekilde dönüştürebilirsiniz](convert-unmanaged-to-managed-disks.md).

Ayrıca, her Azure bölgesinde bir depolama hesabındaki özel görüntülerinizi yönetebilir ve aynı abonelikte yüzlerce VM oluşturmak için kullanabilirsiniz. Yönetilen Diskler hakkında daha fazla bilgi için bkz. [Yönetilen Disklere Genel Bakış](../managed-disks-overview.md).

## <a name="distributions"></a>Dağıtımları 
Microsoft Azure, çeşitli iş ortakları tarafından sağlanan ve bakımı yapılan bir dizi popüler Linux dağıtımının çalıştırılmasını destekler.  Azure Marketi 'nde kullanılabilir dağıtımları bulabilirsiniz. Microsoft, [Azure destekli Linux Dağıtımları](endorsed-distros.md) listesine daha da fazla çeşitleme katmak için çeşitli Linux topluluklarıyla etkin bir çalışma sürdürüyor.

Tercih ettiğiniz Linux dağıtımı şu anda galeride yoksa, [Azure'da Linux VHD'si oluşturma ve karşıya yükleme](create-upload-generic.md) yoluyla "Kendi Linux VM'inizi getirebilirsiniz".

Microsoft, sağlanan görüntülerin güncelleştirilmiş ve Azure çalışma zamanı için iyileştirilmiş olduğundan emin olmak için iş ortaklarıyla yakın bir çalışma sürdürür.  Azure iş ortağı teklifleri hakkında daha fazla bilgi için aşağıdaki bağlantılara bakın:

* Azure[ Destekli Dağıtımlarda](endorsed-distros.md) Linux
* SUSE - [Azure Market - SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=suse)
* Red hat- [Azure Marketi-Red Hat Enterprise Linux](https://azuremarketplace.microsoft.com/marketplace/apps?search=Red%20Hat%20Enterprise%20Linux)
* Kurallı- [Azure Marketi-Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer)
* Detem- [Azure Marketi-detem](https://azuremarketplace.microsoft.com/marketplace/apps?search=Debian&page=1)
* FreeBSD- [Azure Marketi-FreeBSD](https://azuremarketplace.microsoft.com/marketplace/apps?search=freebsd&page=1)
* Düz otomobil- [Azure Marketi-Yataylı kapsayıcı Linux](https://azuremarketplace.microsoft.com/marketplace/apps?search=Flatcar&page=1)
* RancherOS - [Azure Market - RancherOS](https://azuremarketplace.microsoft.com/marketplace/apps/rancher.rancheros)
* Bitnami - [Azure için Bitnami Kitaplığı](https://azure.bitnami.com/)
* Mesosphere - [Azure Market - Azure'da Mesosphere DC/OS](https://azure.microsoft.com/services/kubernetes-service/mesosphere/)
* Docker- [Azure Marketi-Docker görüntüleri](https://azuremarketplace.microsoft.com/marketplace/apps?search=docker&page=1&filters=virtual-machine-images)
* Jenkins - [Azure Market - CloudBees Jenkins Platform](https://azuremarketplace.microsoft.com/marketplace/apps/cloudbees.cloudbees-core-contact)


## <a name="cloud-init"></a>Cloud-init 

Düzgün bir DevOps kültürünü başarmak için tüm altyapı kod olmalıdır.  Tüm altyapı kodda bulunduğunda kolayca yeniden oluşturulabilir.  Azure tüm önemli otomasyon araçlarıyla, örneğin Ansible, Chef, SaltStack ve Puppet ile çalışır.  Azure'un ayrıca kendi otomasyon aracı da vardır:

* [Azure şablonları](create-ssh-secured-vm-from-template.md)
* [Mavisi `VMaccess`](../extensions/vmaccess.md)

Azure, en çok Linux destekleri tarafından desteklenen [bulutta init](https://cloud-init.io/) 'yi destekler.  Azure Marketi 'nde Cloud-init özellikli görüntülerin kullanılabilmesini sağlamak için, onaylanan Linux olmayan iş ortaklarıyla etkin bir şekilde çalışıyoruz. Bu görüntüler, Cloud-init dağıtımlarınızın ve yapılandırmalarının VM 'Ler ve sanal makine ölçek kümeleri ile sorunsuz bir şekilde çalışmasını sağlayacak.

* [Azure Linux VM'lerinde cloud-init kullanma](using-cloud-init.md)

## <a name="storage"></a>Depolama
* [Microsoft Azure Depolama'ya Giriş](../../storage/common/storage-introduction.md)
* [azure-cli kullanarak Linux VM'sine disk ekleme](add-disk.md)
* [Azure Portal’da Linux VM’sine veri diski ekleme](attach-disk-portal.md)

## <a name="networking"></a>Ağ
* [Sanal ağa genel bakış](../../virtual-network/virtual-networks-overview.md)
* [Azure'da IP adresleri](../../virtual-network/public-ip-addresses.md)
* [Azure'da Linux VM'sine bağlantı noktalarını açma](nsg-quickstart.md)
* [Azure portalda Tam Etki Alanı Adı oluşturma](../create-fqdn.md)


## <a name="data-residency"></a>Veri yerleşimi

Azure 'da, müşteri verilerinin tek bir bölgede depolanmasını sağlama özelliği şu anda yalnızca Asya Pasifik coğrafi ve Brezilya Güney (Sao Paulo Eyaleti) Brezilya coğrafi bölge bölgesinin Güneydoğu Asya bölgesinde (Singapur) kullanılabilir. Diğer tüm bölgeler için müşteri verileri coğrafi olarak depolanır. Daha fazla bilgi için bkz. [Güven Merkezi](https://azure.microsoft.com/global-infrastructure/data-residency/).


## <a name="next-steps"></a>Sonraki adımlar

İlk VM 'nizi oluşturun!

- [Portal](quick-create-portal.md)
- [Azure CLI](quick-create-cli.md)
- [PowerShell](quick-create-powershell.md)
