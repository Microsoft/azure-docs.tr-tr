---
title: SSS-CloudSimple 'a göre Azure VMware çözümü
description: CloudSimple tarafından Azure VMware çözümü hakkında sık sorulan sorular
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 95d8c2974ea372dd59d15a9f2cc31ed171acd932
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828928"
---
# <a name="frequently-asked-questions-about-vmware-solution-by-cloudsimple"></a>CloudSimple ile VMware çözümü hakkında sık sorulan sorular

## <a name="cloudsimple-service"></a>CloudSimple hizmeti

**CloudSimple Azure VMware çözümü nedir?**

CloudSimple ile Azure VMware çözümü, VMware iş yüklerini Azure 'da dakikalar içinde özel ve adanmış bulutlara genişletir. CloudSimple, altyapıyı sağlama, altyapıyı yönetme ve şirket içi ile Azure arasında iş yüklerini düzenleme işlemlerini gerçekleştirir. Uygulamalarınız şirket içinde ve Azure 'da tam olarak aynı çalıştığından, uygulamalarınızın yeniden mimarının karmaşıklığı olmadan bulutun esneklik ve hizmetlerinden yararlanabilirsiniz. CloudSimple, isteğe bağlı sağlama, Kullandıkça öde ve kapasite iyileştirmesi sağlayan bir bulut tüketim modeliyle toplam sahip olma maliyetinizi azaltır.  Özellikler, avantajlar ve senaryolar için [CloudSimple tarafından Azure 'Da VMware çözümü nedir?](cloudsimple-vmware-solutions-overview.md) konusuna bakın.

**CloudSimple özel bulutu nedir?**

CloudSimple özel bulutu, Azure konumlarında Microsoft Azure altyapısına (donanım ve veri merkezi alanı) dağıtılan yüksek performanslı bir işlem, depolama ve ağ ortamından oluşan özel, ayrılmış bir bulutdır.  Özel bulut, yerel bir VMware ' hizmet olarak platform ' sağlar. VMware koşullarında, her özel bulut vCenter Server tam olarak bir örneğini içerir. VCenter Server, bir veya daha fazla vSphere kümesinde bulunan birden çok ESXi düğümünü, karşılık gelen sanal SAN (vSAN) depolaması ile birlikte yönetir. CloudSimple hizmeti, Azure aboneliğinizde birden çok özel bulut içerebilir.  Daha ayrıntılı bilgi için bkz. [özel buluta genel bakış](cloudsimple-private-cloud.md).

**CloudSimple hizmeti nerede kullanılabilir?**

CloudSimple, çok yakında başka bölgelere sahip Doğu ABD, Batı ABD ve Batı Avrupa bölgelerinde kullanılabilir.

**CloudSimple için aboneliğimi etkinleştirmek Nasıl yaparım? mı?**

Microsoft hesabı temsilcinizle, CloudSimple hizmeti aboneliğinizi etkinleştirmek için [azurevmwaresales@microsoft.com](mailto:azurevmwaresales@microsoft.com) ' de başvurabilirsiniz. CloudSimple hizmetinin etkin olmasını istediğiniz e-postada abonelik KIMLIĞINIZI belirtin.  

**CloudSimple portalına erişmek Nasıl yaparım? mı?**

CloudSimple portalına Azure portal erişirsiniz.  Ayrıntılar için bkz. [Azure Portal CloudSimple Portal tarafından VMware çözümüne erişme](access-cloudsimple-portal.md).

**Özel bulutun kapasitesini artırmak Nasıl yaparım? mı?**

Kapasiteyi artırmak için Azure portal ek düğümler satın alın ve ardından, özel bulutunuzu CloudSimple portalından genişletmek için düğümleri kullanın.  Mevcut bir vSphere kümesine ek düğümler ekleyebilir veya bunları yeni bir vSphere kümesine ekleyebilirsiniz.  Ayrıntılar için bkz. [CloudSimple özel bulutunu genişletme](expand-private-cloud.md).

**Bakım sırasında özel bulutum ne olur?**

CloudSimple, zamanlanan bir bakım aralığından önce birkaç gün önce bildirim sağlar.  Bakım, özel bulutunuzun kullanılabilirliğini güvence altına almak için kesintiye uğramamış bir şekilde yapılır.  Bakım aşağıdaki türlerde olabilir:

* **Cloudsimple altyapısı**.  CloudSimple altyapısı yüksek oranda kullanılabilir olacak şekilde tasarlanmıştır.  Bu tür bakım aralığı sırasında, herhangi bir hizmet kesintisini önlemek için yedekli bileşenler aynı anda güncelleştirilir. Özel bulut vCenter, tüm sanal makineler, özel bulutunuzun internet bağlantısı ve şirket içi veya Azure bağlantıları için erişim sağlayabilirsiniz.
* **Cloudsimple portalı**. Bu tür bakım aralığı sırasında CloudSimple portalındaki bazı özellikler devre dışı bırakılabilir veya erişilemez durumda olabilir.  Bakım aralığından önceki bildirim, bakım gerçekleşirken Özellik sınırlamalarıyla ilgili ayrıntıları içerir.

## <a name="connectivity"></a>Bağlantı

**CloudSimple bölgesi ağına bağlantı seçeneklerim nelerdir?**

CloudSimple, CloudSimple bölge ağınıza bağlanmak için aşağıdaki bağlantı seçeneklerini sağlar. Aynı anda birden çok seçenek kullanılabilir.

* Şirket **içi veri merkezinizden CloudSimple bölge ağına ExpressRoute bağlantısı**. Bu, şirket içi ExpressRoute bağlantı hattınızı CloudSimple ExpressRoute bağlantı hattına bağlamak için Global Reach kullanan yüksek hızlı, düşük gecikme süreli, güvenli bir özel bağlantıdır. Bağlantıyı ayarlama hakkında yönergeler için bkz. [ExpressRoute kullanarak şirket Içinden CloudSimple 'A bağlanma](on-premises-connection.md).
* **Azure sanal ağınızdan CloudSimple bölge ağınıza ExpressRoute bağlantısı**. Bu, Sanal ağınızı Azure 'da CloudSimple ExpressRoute bağlantı hattına bağlamak için sanal ağ geçitleri kullanan yüksek hızlı, düşük gecikme süreli ve güvenli bir özel bağlantıdır. Bağlantıyı ayarlamayla ilgili yönergeler için bkz. [ExpressRoute kullanarak CloudSimple özel bulut ortamınızı Azure sanal ağına bağlama](azure-expressroute-connection.md).
* Şirket **içi veri merkezinizden CloudSimple bölgesi ağınıza siteden sıteye VPN bağlantısı**. Bu, şirket içi VPN cihazınızdan CloudSimple özel bulut bölgenize yönelik güvenli bir sanal özel ağ.  Ayrıntılar için bkz. [CloudSimple AĞıNDA VPN ağ geçitlerini ayarlama](vpn-gateway.md).

**Nasıl yaparım? özel buluta mı bağlanacak?**

Özel bulutunuzun ayrıntılarını CloudSimple portalında görüntüleyebilirsiniz. Özel buluta karşılık gelen vCenter 'a bağlanmak için önce siteden siteye VPN, Noktadan siteye VPN veya ExpressRoute kullanarak bir ağ bağlantısının yapıldığını doğrulayın. Ardından, Azure portal CloudSimple portalını başlatın ve giriş sayfasında veya özel bulut ayrıntıları sayfasında **vSphere Istemcisini Başlat** ' a tıklayın.

**ExpressRoute devrelerinin avantajı nedir?**

Azure ExpressRoute bağlantı hattı yüksek hızlı, düşük gecikmeli, güvenli bir bağlantıdır.  CloudSimple, her müşteri için bölge başına adanmış bir ExpressRoute devresi sağlar.  Bu devreyi kullanarak şirket içi veya Azure aboneliğinizden güvenli bir bağlantı kurabilirsiniz.

**CloudSimple 'a bağlanmak için ağ maliyetleri nelerdir?  Herhangi bir çıkış ücreti CloudSimple ve Azure arasında ya da bölgeler arasında geçerlidir mi?**

Ağ çıkışı için CloudSimple ücreti yoktur.  Azure Standart ücretleri, sanal ağınızdan veya şirket içi ExpressRoute bağlantı hattınızdan gelen çıkış trafiği için geçerlidir.

## <a name="networking"></a>Networking (Ağ İletişimi)

**Özel bulutum için hangi ağ özellikleri kullanılabilir?**

VLAN 'Lar (ve alt ağları) ve güvenlik duvarı tabloları sağlayabilir ve özel bulutunuzda çalışan bir sanal makineyle eşlenen genel IP adresleri atayabilirsiniz. Ağ özellikleriyle ilgili ayrıntılar için bkz. [VLAN ve alt ağlara genel bakış](cloudsimple-vlans-subnets.md), [güvenlik duvarı tablolarına genel](cloudsimple-firewall-tables.md)bakış ve [genel IP adresine genel bakış](cloudsimple-public-ip-address.md).

**Nasıl yaparım? özel Bulutumdaki Uygulamalarım için farklı alt ağlar mı ayarlanacak?**

Özel bulutunuzda, CloudSimple portalından VLAN 'Lar oluşturun.  Bir VLAN oluşturduktan sonra, VLAN kullanarak özel bulut vCenter 'unuzda dağıtılmış bir bağlantı noktası grubu oluşturabilir ve dağıtılmış bağlantı noktası grubuna bağlı sanal makineler oluşturabilirsiniz.  VLAN/subnet için güvenlik duvarı tablolarını etkinleştirebilir ve ağ trafiğinin güvenliğini sağlamak için güvenlik duvarı kuralları tanımlayabilirsiniz.

**Özel bulutlarım için hangi güvenlik duvarı ayarları kullanılabilir?**

Kuzey-Güney ve Doğu-Batı trafiği için kurallar yapılandırabilirsiniz.  Kurallar bir güvenlik duvarı tablosunda tanımlanır.  Güvenlik Duvarı tablosu, özel bulutunuzda VLAN 'Lara bağlanabilir.  Ayrıntılar için bkz. [özel bulutlar için güvenlik duvarı tablolarını ve kurallarını ayarlama](firewall.md).

**Özel bulut ortammdaki VM 'lere genel IP adresleri atayabilir miyim?**

CloudSimple portalında, yeni bir genel IP adresi ayırabilir ve bunu bir sanal makinenin veya gerecin özel IP adresiyle ilişkilendirebilirsiniz.  Ayrıca, portalda belirli bağlantı noktalarından ve IP adreslerinden gelen trafiğe izin vermek için yeni güvenlik duvarı kuralları oluşturabilir veya mevcut güvenlik duvarı kurallarını uygulayabilirsiniz. Ayrıntılar için bkz. [özel bulut ortamı için genel IP adresleri ayırma](public-ips.md).

## <a name="security"></a>Güvenlik

**CloudSimple 'ta güvenlik seçeneklerim nelerdir?**

CloudSimple, özel bulut ortamınızı güvenli hale getirmek için aşağıdaki güvenlik özelliklerini sağlar:

* **Bekleyen şifreleme verileri**. Özel bulutunuzda vSAN depolamada bulunan bekleyen verileri şifreleyebilirsiniz. vSAN, Azure vNet 'iniz veya şirket içi ortamınızda dağıtılabilecek dış anahtar yönetim sunucularını destekler.  Ayrıntılar için bkz. [CloudSimple özel bulutunuz Için vSAN şifrelemesini yapılandırma](vsan-encryption.md).
* **Ağ güvenliği**. Özel bulutunuz ile internet, özel bulut ve şirket içi ortamınız veya özel bulutunuzun alt ağları arasında uygulanan güvenlik duvarı kurallarıyla ağ trafiği akışını denetleyin.
* **Güvenli, özel bağlantı**. Şirket içi ağınız ile Azure aboneliğiniz arasında güvenli, özel bir bağlantı oluşturulur.

## <a name="compute"></a>İşlem

**Hangi tür konaklar kullanılabilir?**

CloudSimple şu ana bilgisayar türlerini sunar:

* **CS28 düğümü:** CPU: 2x 2,2 GHz, toplam 28 çekirdek, 48 HT.  RAM: 256 GB.  Depolama: 1600 GB NVMe önbelleği, 5760 GB veri (tümü-Flash). Ağ: 4x25Gbe NIC
* **CS36 düğümü:** CPU 2x 2,3 GHz, toplam 36 çekirdek, 72 HT.  RAM: 512 GB.  Depolama: 3200 GB NVMe Cache 11520 GB veri (tümü-Flash).  Ağ: 4x25Gbe NIC
* **CS36m düğümü:** CPU 2x 2,3 GHz, toplam 36 çekirdek, 72 HT.  RAM: 576 GB.  Depolama: 3200 GB NVMe Cache 13360 GB veri (tümü-Flash).  Ağ: 4x25Gbe NIC

**Donanım hatalarının tümü nasıl işlenir?**

Tüm CloudSimple altyapısı, CloudSimple platformu ve hizmet işlemleri takımlarımız tarafından sürekli olarak izlenir.  Bir donanım hatası algılanırsa, özel bulutunuzda yeni bir düğüm eklenir ve başarısız olan düğüm kaldırılır.

## <a name="storage"></a>Depolama

**Özel bulutta ne tür bir depolama alanı desteklenir?**

CloudSimple, her özel bulutla birlikte tüm-Flash VMware vSAN depolama alanı sunar.  Her vSphere kendi vSAN veri deposu ile oluşturulur.  Ayrıntılar için bkz. [özel bulut VMware bileşenleri-vSAN depolaması](vmware-components.md#vsan-storage).

**Verilerin şifrelenmesi destekleniyor mu?**
Evet.  Özel bulutunuzda vSAN depolama alanını, vSAN 'da depolanan verileri şifrelemek için şirket içinde veya Azure 'da dağıtılan bir anahtar yönetim sunucusu (KMS) kullanmak üzere ayarlayabilirsiniz.

**Hatalı diskler nasıl işlenir?**

CloudSimple, özel bulutun tüm donanım bileşenlerini sürekli olarak izler.  Bir disk hatası algılanırsa veya disk başarısız olarak tanımlanmışsa (buluşsal yöntemler temelinde), özel buluta otomatik olarak yeni bir düğüm eklenir.  Başarısız veya hatalı diski olan düğüm özel buluttan kaldırılır.

## <a name="vmware"></a>VMware

**Şirket içinden uygulamalar ve veriler için büyük ölçekli karşıya yükleme veya geçiş Nasıl yaparım? istiyor musunuz?**

CloudSimple, yerel bir VMware vSphere çözümü sağlar.  Toplu veri geçişi için tüm VMware araçları, özel bulutunuz ile kullanılabilir.  Seçeneklere şunlar dahildir:

* Verilerin toplu geçirilmesi için VMware HCX.
* Şirket içinden CloudSimple 'a Depolama vMotion kullanılarak verilerin soğuk geçirilmesi.

**Herhangi bir VMware aracını yükleyebilir miyim?**

CloudSimple, yerel bir VMware vSphere çözümü sağlar.  Şirket içi vSphere ortamınızı yönetmek için kullanılan tüm VMware araçları CloudSimple üzerinde kullanılabilir.  CloudSimple, VMware araçlarını yüklemek için kendi lisansını getir (KLG) modelini destekler.

**Güncelleştirmeler ve yükseltmeler nasıl yönetilir?**

CloudSimple, özel bulutunuzun tüm altyapı bileşenlerini sorunsuz bir şekilde yönetir ve güncelleştirir.  VMware veya altyapı satıcıları tarafından yayınlanan tüm güncelleştirmeler ve güvenlik yamaları, CloudSimple tarafından nitelendirildikleri anda güncelleştirme için zamanlanır.

CloudSimple, özel bulutta yüklü uygulamaların yükseltmelerini veya güncelleştirmelerini gerçekleştirmez.

## <a name="azure-integration"></a>Azure tümleştirmesi

**Hangi Azure hizmetleri destekleniyor?**

CloudSimple, Azure 'da Aboneliğinize yönelik bir Azure ExpressRoute bağlantısı sağlar.  Aboneliğinizde çalışan tüm hizmetler özel bulutunuzun bağlanmasına izin verebilir.  Örneğin:

* CloudSimple vCenter için bir kimlik kaynağı olarak **Azure Active Directory** .
* Özel bulutunuzun yedeklerini, görüntülerini ve diğer verilerini depolamak için **Azure depolama** .
* Ortak ve özel bulutlara yayılan bir uygulama mimarisine sahip **karma uygulamalar** .  Örneğin, Azure 'da özel bulutunuzda uygulama ve veritabanı sunucularına erişen Web sunucuları oluşturabilirsiniz.
* VMware desteği günlüğü, performans ölçümleri ve güvenlik yönetimi üzerinde çalışan iş yükleri için **Azure izleyici** ve **Azure Güvenlik Merkezi** .

**Nasıl yaparım? VMware kiracılarımı Azure 'a eşleyin mi?**

CloudSimple, VMware VM 'lerinizi Azure portal özel bulutta yönetmek için benzersiz bir özellik sağlar.  İstenen kaynak kısıtlamalarıyla yapılandırılmış bir vCenter kaynak havuzu, genel yöneticiniz tarafından aboneliğiniz ile eşleştirilebilir.  

**Azure ile hangi lisans avantajlarını alabilirim?**

CloudSimple sayesinde, Azure hibrit kullanım avantajı avantajlarından yararlanarak lisanslarda% 90 tasarruf edebilirsiniz. Bu avantaj, yatırımınızı Microsoft lisanslarına karşı korur ve diğer bulut çözümlerine göre TCO 'nizi düşürür. Ayrıca Windows Server 2008 ve 2008 Microsoft SQL Server için genişletilmiş güvenlik güncelleştirmeleri de alırsınız.  Kendi lisansını getir (KLG) modeli, Veeae ve Zerto gibi ortak uygulamalar için maliyetleri düşük tutmaya yardımcı olur.  
