---
title: CloudSimple-PRIVATE Cloud VMware bileşenleri tarafından Azure VMware çözümü
description: VMware bileşenlerinin özel buluta nasıl yüklendiğini açıklar
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bd83cff243c94ed62014ff95f6ca7c4e878f6af7
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814577"
---
# <a name="private-cloud-vmware-components"></a>Özel bulut VMware bileşenleri

Özel bulut, yönetim etki alanındaki bir vCenter sunucusu tarafından yönetilen yalıtılmış bir VMware yığını (ESXi Konakları, vCenter, vSAN ve NSX) ortamıdır.  CloudSimple hizmeti, VMware 'yi Azure konumlarında yerel olarak Azure 'da dağıtmanıza olanak tanır.  Özel bulutlar, Azure bulutunun geri kalanı ile tümleşiktir.  Özel bir bulut aşağıdaki VMware Stack bileşenleriyle dağıtılır:

* **VMware ESXi-** Azure adanmış düğümlerinde hiper yönetici
* **VMware vCenter-** Özel bulut vSphere ortamının merkezi yönetimi için gereç
* **VMware vSAN-** Hiper yakınsanmış altyapı çözümü
* **VMware NSX veri merkezi-** Ağ sanallaştırma ve güvenlik yazılımı  

## <a name="vmware-component-versions"></a>VMware bileşen sürümleri

Özel bir bulut VMware yığını aşağıdaki yazılım sürümü ile dağıtılır.

| Bileşen | Version | Lisanslı sürüm |
|-----------|---------|------------------|
| ESXi | 6.7 U2 | Kurumsal Plus |
| vCenter | 6.7 U2 | vCenter Standard |
| vSAN | 6.7 | Enterprise |
| NSX veri merkezi | 2.4.1 | Gelişmiş |

## <a name="esxi"></a>ESXi

VMware ESXi, özel bir bulut oluşturduğunuzda sağlanan CloudSimple düğümlerine yüklenir.  ESXi, iş yükü sanal makinelerini (VM) dağıtmak için hiper yöneticiyi sağlar.  Düğümler, özel bulutunuzda hiper yakınsama altyapısı (işlem ve depolama) sağlar.  Düğümler, özel buluttaki vSphere kümesinin bir parçasıdır.  Her düğüm, ağı düşük düzenlemek için bağlı dört fiziksel ağ arabirimine sahiptir.  İki fiziksel ağ arabirimi, vCenter üzerinde bir **vSphere dağıtılmış anahtarı (VDS)** oluşturmak için kullanılır ve Iki adet **NSX tarafından yönetilen bir sanal dağıtılmış anahtar (N-VDS)** oluşturmak için kullanılır.  Ağ arabirimleri, yüksek kullanılabilirlik için etkin-etkin modda yapılandırılır.

VMware ESXi hakkında daha fazla bilgi edinin

## <a name="vcenter-server-appliance"></a>vCenter Server gereci

vCenter Server gereci (VCSA), CloudSimple tarafından VMware çözümü için kimlik doğrulama, yönetim ve düzenleme işlevleri sağlar. Özel bulutunuzu oluşturduğunuzda, katıştırılmış platform hizmetleri denetleyicisi (PSC) ile VCSA dağıtılır.  VCSA, özel bulutunuzu dağıtırken oluşturulan vSphere kümesine dağıtılır.  Her özel bulutun kendi VCSA 'sı vardır.  Özel bulutun genişletilmesi, düğümleri özel buluttaki VCSA 'ya ekler.

### <a name="vcenter-single-sign-on"></a>vCenter çoklu oturum açma

VCSA 'daki katıştırılmış platform hizmetleri denetleyicisi bir **vCenter çoklu oturum açma etki alanıyla**ilişkilendirilir.  Etki alanı adı **cloudsimple. Local**' dir.  VCenter 'a erişmeniz **CloudOwner@cloudsimple.com** için varsayılan bir Kullanıcı oluşturulur.  VCenter için şirket içi/Azure Active Directory [kimlik kaynaklarınızı](set-vcenter-identity.md)ekleyebilirsiniz.

## <a name="vsan-storage"></a>vSAN depolaması

Özel bulutlar tümüyle yapılandırılan tümü-Flash vSAN depolama, küme yereliyle oluşturulur.  VSAN veri deposu ile vSphere kümesi oluşturmak için aynı SKU 'nun en az üç düğümü gereklidir.  Devre dışı bırakma ve sıkıştırma, varsayılan olarak vSAN veri deposunda etkindir.  VSphere kümesinin her bir düğümünde iki disk grubu oluşturulur. Her disk grubu, bir önbellek diski ve üç kapasite diski içerir.

VSphere kümesinde varsayılan bir vSAN depolama ilkesi oluşturulur ve vSAN veri deposuna uygulanır.  Bu ilke, gerekli hizmet düzeyini güvence altına almak için VM depolama nesnelerinin veri deposu içinde nasıl sağlandığını ve ayrılacağını belirler.  Depolama ilkesi, **tolerans (FTT)** ve **hata toleransı yöntemi**hatalarını tanımlar.  Yeni depolama ilkeleri oluşturabilir ve bunları sanal makinelere uygulayabilirsiniz. SLA 'yı sürdürmek için, vSAN veri deposunda% 25 yedek kapasitesinin korunması gerekir.  

### <a name="default-vsan-storage-policy"></a>Varsayılan vSAN depolama ilkesi

Aşağıdaki tabloda varsayılan vSAN depolama ilkesi parametreleri gösterilmektedir.

| VSphere kümesindeki düğüm sayısı | FTT | Hata toleransı yöntemi |
|------------------------------------|-----|--------------------------|
| 3 ve 4 düğümleri | 1\. | RAID 1 (yansıtma)-2 kopya oluşturur |
| 5-16 düğümleri | 2 | RAID 1 (yansıtma)-3 kopya oluşturur |

## <a name="nsx-data-center"></a>NSX veri merkezi

NSX veri merkezi, özel bulutunuzda ağ sanallaştırma, mikro segmentleme ve ağ güvenliği özellikleri sağlar.  NSX veri merkezi tarafından desteklenen tüm hizmetleri NSX aracılığıyla özel bulutunuzda yapılandırabilirsiniz.  Özel bir bulut oluşturduğunuzda, aşağıdaki NSX bileşenleri yüklenir ve yapılandırılır.

* NSXT Yöneticisi
* Taşıma bölgeleri
* Konak ve kenar yukarı bağlantı profili
* Edge Transport, Ext1 ve Ext2 için mantıksal anahtar
* ESXi aktarım düğümü için IP Havuzu
* Sınır taşıma düğümü için IP Havuzu
* Kenar düğümleri
* Denetleyici ve uç VM 'Ler için DRS, benzeşim önleme kuralı
* Katman 0 yönlendirici
* Tier0 yönlendiricisinde BGP 'yi etkinleştir

## <a name="vsphere-cluster"></a>vSphere kümesi

ESXi Konakları, özel bulutun yüksek oranda kullanılabilirliğini sağlamak için bir küme olarak yapılandırılır.  Özel bir bulut oluşturduğunuzda, vSphere Yönetim bileşenleri ilk kümede dağıtılır.  Yönetim bileşenleri için bir kaynak havuzu oluşturulur ve tüm yönetim VM 'Leri bu kaynak havuzunda dağıtılır. İlk küme, özel bulutu daraltmak için silinemez.  vSphere kümesi, **VSPHERE ha**kullanan VM 'ler için yüksek kullanılabilirlik sağlar.  Tolerans sorunları, kümedeki kullanılabilir düğümlerin sayısını temel alır.  Kabul edilecek başarısızlık sayısı ```Number of nodes = 2N+1``` ```N``` olan formülünü kullanabilirsiniz.

### <a name="vsphere-cluster-limits"></a>vSphere kümesi sınırları

| Resource | Sınır |
|----------|-------|
| Özel bir bulut oluşturmak için en az düğüm sayısı (ilk vSphere kümesi) | 3 |
| Özel buluttaki bir vSphere kümesindeki en fazla düğüm sayısı | 16 |
| Özel buluttaki en fazla düğüm sayısı | 64 |
| Özel buluttaki maksimum vSphere kümesi sayısı | 21 |
| Yeni bir vSphere kümesindeki düğüm sayısı alt sınırı | 3 |

## <a name="vmware-infrastructure-maintenance"></a>VMware altyapı Bakımı

Bazen VMware altyapısının yapılandırmasında değişiklik yapmak gerekir. Şu anda bu aralıklar her 1-2 ayda bir gerçekleşebilir, ancak zamanın zaman içinde reddetmesi beklenir. Bu tür bir bakım genellikle CloudSimple hizmetlerinin normal tüketimi kesintiye uğramadan yapılabilir. Bir VMware bakım aralığı sırasında, aşağıdaki hizmetler herhangi bir etki olmadan çalışmaya devam eder:

* VMware yönetim düzlemi ve uygulamaları
* vCenter erişimi
* Tüm ağ ve depolama
* Tüm Azure trafiği

## <a name="updates-and-upgrades"></a>Güncelleştirmeler ve yükseltmeler

CloudSimple, özel bulutta VMware yazılımının (ESXi, vCenter, PSC ve NSX) yaşam döngüsü yönetiminden sorumludur.

Yazılım güncelleştirmeleri şunları içerir:

* **Düzeltme ekleri**. VMware tarafından yayınlanan güvenlik düzeltme ekleri veya hata düzeltmeleri.
* **Güncelleştirmeler**. VMware Stack bileşeninin küçük sürüm değişikliği.
* **Yükseltmeleri**. VMware Stack bileşeninin ana sürüm değişikliği.

CloudSimple, VMware 'den kullanılabilir hale geldiğinde kritik bir güvenlik düzeltme ekini test eder. SLA 'Sı başına, CloudSimple, güvenlik düzeltme ekini bir hafta içinde özel bulut ortamlarına kaydeder.

CloudSimple, VMware yazılım bileşenlerine üç aylık bakım güncelleştirmeleri sağlar. VMware yazılımının yeni bir ana sürümü kullanılabilir olduğunda, CloudSimple, yükseltme için uygun bir bakım penceresini koordine etmek üzere müşterilerle birlikte çalışmaktadır.  

## <a name="next-steps"></a>Sonraki adımlar

* [CloudSimple bakım ve güncelleştirmeleri](cloudsimple-maintenance-updates.md)
