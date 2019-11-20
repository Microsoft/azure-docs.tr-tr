---
title: CloudSimple tarafından Azure VMware çözümü-VMware ufuk 'i kullanarak sanal masaüstü altyapısını barındırmak için özel bulut sitesini kullanma
description: VMware ufuk 'i kullanarak sanal masaüstü altyapısını barındırmak için CloudSimple özel bulut sitenizi nasıl kullanabileceğinizi açıklar.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8e5aeb63c54bd9ad71d5eb179fb93972468af4c0
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972764"
---
# <a name="use-cloudsimple-private-cloud-site-to-host-a-virtual-desktop-infrastructure-using-vmware-horizon"></a>VMware ufuk 'i kullanarak sanal masaüstü altyapısını barındırmak için CloudSimple özel bulut sitesini kullanma

Sanal Masaüstü altyapısını (VDı) VMware ufuk 7. x kullanarak barındırmak için CloudSimple özel bulut sitenizi kullanabilirsiniz. Aşağıdaki şekilde VDı için mantıksal çözüm mimarisi gösterilmektedir.

![Ufku dağıtımı](media/horizon-deployment.png)

Bu çözümle, ufuk görünümü Yöneticisi ve uygulama hacmi üzerinde tam denetime sahip olursunuz. Tanıdık UI, API ve CLı arabirimleri, mevcut betiklerinizin ve araçlarınızın kullanımını sağlar.

CloudSimple çözümü şunları yapmanızı gerektirir:

* Özel bulutunuzda VMware ufuk 7. x ' i yükler, yapılandırın ve yönetin.
* Kendi ufku lisanslarınızı sağlayın.

## <a name="deploy-the-solution"></a>Çözümü dağıtma

Aşağıdaki bölümlerde özel bulutunuzda ufuk kullanarak bir VDı çözümünün nasıl dağıtılacağı açıklanır.

1. [VMware ürün sürümlerinin uyumlu olduğunu doğrulama](#verify-that-vmware-product-versions-are-compatible)
2. [Masaüstü ortamınızın boyutunu tahmin etme](#estimate-the-size-of-your-desktop-environment)
3. [Ortamınız için özel bir bulut oluşturma](#create-a-private-cloud-for-your-environment)
4. [Özel bulutunuzda VMware ufuk 'i 'ni yükler](#install-vmware-horizon-in-your-private-cloud)

### <a name="verify-that-vmware-product-versions-are-compatible"></a>VMware ürün sürümlerinin uyumlu olduğunu doğrulama

* Geçerli ve planlı sürümlerinin, uygulama birimlerinin, Birleşik erişim ağ geçidinin ve Kullanıcı ortam yöneticisinin birbirleriyle ve özel buluttaki vCenter ve PSC ile uyumlu olduğunu doğrulayın. Uyumluluk bilgileri için bkz. [ufuk 7,5 Için VMware uyumluluk matrisi](https://www.vmware.com/resources/compatibility/sim/interop_matrix.php#interop&260=2877&0=).
* Özel bulutunuzda vCenter ve PSC 'nin güncel sürümlerini bulmak için [Cloudsimple portalındaki](access-cloudsimple-portal.md) **kaynaklar** ' a gidin, özel bulutunuzu seçin ve **vSphere yönetim ağı** sekmesine tıklayın.

![vCenter ve PSC sürümleri](media/private-cloud-vsphere-versions.png)

### <a name="estimate-the-size-of-your-desktop-environment"></a>Masaüstü ortamınızın boyutunu tahmin etme

* Tanımlı yapılandırmanızın VMware işlemsel sınırları içinde olduğunu doğrulayın.
* Şirket içi ortamınızı korumak için DR sitenizde gereken kaynakları tahmin edin.

### <a name="create-a-private-cloud-for-your-environment"></a>Ortamınız için özel bir bulut oluşturma

1. [Özel bulut ortamı yapılandırma](quickstart-create-private-cloud.md)konusundaki yönergeleri Izleyerek cloudsimple portalından özel bir bulut oluşturun.  CloudSimple, yeni oluşturulan her özel bulutta ' cloudowner ' adlı bir varsayılan vCenter kullanıcısı oluşturur. Varsayılan özel bulut kullanıcısı ve izin modeli hakkında daha fazla bilgi için bkz. [özel bulut izinleri modelini öğrenme](learn-private-cloud-permissions.md).
2. Ufıt yönetim düzlemi için özel bulutunuzda bir VLAN oluşturun ve bir alt ağ CıDR atayın. Yönergeler için bkz. [VLAN/alt ağlar oluşturma ve yönetme](create-vlan-subnet.md). Bu, tüm çözüm bileşenlerinin (Birleşik erişim ağ geçidi, bağlantı sunucusu, App Volume Server ve Kullanıcı ortam yöneticisi sunucuları) yükleneceği ağ ' dır.
3. Özel bulut vCenter 'unuzda bir dış kimlik sağlayıcısı kullanmak istediğinize karar verin. Yanıt Evet ise, şu seçeneklerden birini seçin:
    * Şirket içi Active Directory dış kimlik sağlayıcısı olarak kullanın. Yönergeler için bkz. [vCenter Identity Sources](set-vcenter-identity.md).
    * Dış kimlik sağlayıcınız olarak kullanmak için, ufuk yönetimi düzlemi VLAN ' da özel bulutta bir Active Directory sunucusu ayarlayın. Yönergeler için bkz. [vCenter Identity Sources](set-vcenter-identity.md).
    * Özel bulutta ufuk yönetimi düzlemi VLAN 'da bir DHCP ve DNS sunucusu ayarlayın. Yönergeler için bkz. [CloudSimple özel bulutunuzda DNS ve DHCP uygulamalarını ve iş yüklerini ayarlama](dns-dhcp-setup.md).
4. Özel bulutta yüklü DNS sunucusunda DNS iletmeyi yapılandırın. Yönergeler için bkz. [koşullu Iletici oluşturma](on-premises-dns-setup.md#create-a-conditional-forwarder).

### <a name="install-vmware-horizon-in-your-private-cloud"></a>Özel bulutunuzda VMware ufuk 'i 'ni yükler

Aşağıdaki dağıtım diyagramı, özel bir bulutta dağıtılan bir ufuk çözümünü göstermektedir. Birleşik erişim ağ geçidi, AD/DC, görünüm ve App Volume sunucusu, Kullanıcı tarafından oluşturulan VLAN 234 ' de yüklenir. Birleşik erişim ağ geçidi, Internet 'ten erişilebilen atanmış bir genel IP adresine sahiptir. Ufuk masaüstü havuzu VM 'Leri, ek yalıtım ve güvenlik sağlamak için VLAN 235 ' de dağıtılır.

![Özel bulutta ufku dağıtımı](media/horizon-private-cloud.png)

Aşağıdaki bölümlerde, şekilde gösterildiği gibi bir dağıtımı ayarlama yönergeleri ana hatlarıyla gösterilmiştir. Başlamadan önce, aşağıdakilere sahip olduğunuzu doğrulayın:

* Masaüstü havuzlarınızı çalıştırmak için yeterli kapasiteye sahip CloudSimple Portalı kullanılarak oluşturulan özel bir bulut.
* Masaüstleri için ağ trafiğini desteklemek üzere şirket içi ortamınız ve özel bulut ortamı arasında yeterli bant genişliği.
* Şirket içi veri merkeziniz ve özel bulut arasında bir siteden siteye VPN tüneli ayarlayın.
* Şirket içi ortamınızdaki Son Kullanıcı alt ağlarından, CloudSimple özel bulut alt ağlarına IP erişilebilirliği vardır.
* Özel bulutunuz için AD/DHCP/DNS yüklendi.

#### <a name="cloudsimple-portal-create-a-dedicated-vlansubnet-for-desktop-pools"></a>CloudSimple Portalı: Masaüstü havuzları için adanmış VLAN/alt ağ oluşturma

Ufıt masaüstü havuzları için bir VLAN oluşturun ve bir alt ağ CıDR atayın. Yönergeler için bkz. [VLAN/alt ağlar oluşturma ve yönetme](create-vlan-subnet.md). Bu, tüm Masaüstü sanal makinelerinin çalışacağı ağ olur.

Ufuk dağıtımınızın güvenliğini sağlamak için standart güvenlik en iyi yöntemlerini izleyin:

* Masaüstü sanal makinelerinize yalnızca masaüstü RDP trafiğine/SSH trafiğine izin verin.
* Yalnızca Ufkyönetim düzlemi VLAN ve masaüstü havuzu VLAN arasındaki Yönetim trafiğine izin verin.
* Şirket içi ağdan yalnızca Yönetim trafiğine izin verin.

CloudSimple portalından [güvenlik duvarı kurallarını](firewall.md) yapılandırarak bu en iyi yöntemleri uygulayabilirsiniz.

#### <a name="cloudsimple-portal-configure-firewall-rules-to-secure-horizon-management-plane"></a>CloudSimple Portalı: Güvenlik Duvarı Yönetim düzleminin güvenliğini sağlamak için güvenlik duvarı kurallarını yapılandırma

CloudSimple portalında aşağıdaki kuralları ayarlayın. Yönergeler için bkz. [güvenlik duvarı tablolarını ve kurallarını ayarlama](firewall.md).

1. Yalnızca VMware belge [ufku bağlantı noktası listesinde](https://docs.vmware.com/en/VMware-Horizon-7/7.1/com.vmware.horizon-client-agent.security.doc/GUID-52807839-6BB0-4727-A9C7-EA73DE61ADAB.html) listelenen ağ bağlantı noktalarına izin verilmesi Için, cloudsimple N-S güvenlik duvarında güvenlik duvarı kurallarını şirket içi alt ağlar ve ufuk yönetimi VLAN 'ları arasında iletişime izin verecek şekilde yapılandırın.

2. Özel bulutta ufuk yönetimi VLAN ve masaüstü havuzu VLAN arasında E-W güvenlik duvarı kuralları oluşturun.

#### <a name="cloudsimple-portal-create-a-public-ip-address-for-unified-access-gateway"></a>CloudSimple Portalı: Birleşik erişim ağ geçidi için genel bir IP adresi oluşturma

Internet 'ten masaüstü istemci bağlantılarını etkinleştirmek üzere Birleşik erişim ağ geçidi gereci için genel bir IP adresi oluşturun. Yönergeler için bkz. [genel IP adresleri ayırma](public-ips.md).

Kurulum tamamlandığında genel IP adresi atanır ve genel IP 'Ler sayfasında listelenir.

#### <a name="cloudsimple-portal-escalate-privileges"></a>CloudSimple Portalı: Ayrıcalıkları yükseltme

Varsayılan ' cloudowner ' kullanıcısının ufku yüklemesi için özel bulut vCenter 'da yeterli ayrıcalıkları yoktur, bu nedenle kullanıcının vCenter ayrıcalıkları ilerletilmiş olmalıdır. Daha fazla bilgi için bkz. [ayrıcalıkları Yükselt](escalate-private-cloud-privileges.md).

#### <a name="vcenter-ui-create-a-user-in-private-cloud-for-horizon-installation"></a>vCenter Kullanıcı arabirimi: Ufku yüklemesi için özel bulutta Kullanıcı oluşturma

1. ' Cloudowner ' Kullanıcı kimlik bilgilerini kullanarak vCenter 'da oturum açın.
2. VCenter 'da ' ufksoln-admin ' yeni bir kullanıcı oluşturun ve Kullanıcı ' yı vCenter 'daki Yöneticiler grubuna ekleyin.
3. ' Cloudowner ' kullanıcısı olarak vCenter oturumunu kapatın ve ' ufuk-soln-Yönetici ' kullanıcısı olarak oturum açın.

#### <a name="vcenter-ui-install-vmware-horizon"></a>vCenter Kullanıcı arabirimi: VMware ufuk 'i 'ni yükler

Önceki mantıksal mimari bölümünde belirtildiği gibi, ufuk çözümü aşağıdaki bileşenlere sahiptir:

* VMware ufuk görünümü
* VMware Birleşik erişim ağ geçidi
* VMware App Volume Manager
* VMware Kullanıcı ortamı Yöneticisi

Bileşenleri aşağıdaki gibi yükler:

1. VMware [birleştirilmiş erişim ağ geçidini dağıtma ve yapılandırma](https://docs.vmware.com/en/Unified-Access-Gateway/3.3.1/com.vmware.uag-331-deploy-config.doc/GUID-F5CE0D5E-BE85-4FA5-BBCF-0F86C9AB8A70.html)VMware belgesinde belirtilen yönergeleri Izleyerek Birleşik erişim ağ geçidini yükleyip yapılandırın.

2. [Yükleme kılavuzunu görüntüle](https://docs.vmware.com/en/VMware-Horizon-7/7.4/horizon-installation/GUID-37D39B4F-5870-4188-8B11-B6C41AE9133C.html)bölümündeki yönergeleri Izleyerek, ufku görünümünü özel buluta yükleme.

3. [VMware App Volumes 'ı yükleyip yapılandırma](https://docs.vmware.com/en/VMware-App-Volumes/2.10/com.vmware.appvolumes.user.doc/GUID-5E8BAF8C-F5A6-412C-9424-266BA7109BA4.html)bölümündeki yönergeleri Izleyerek App Volume Manager 'ı yükler.

4. [VMware Kullanıcı ortamı yöneticisini yükleme ve yapılandırma hakkındaki](https://docs.vmware.com/en/VMware-User-Environment-Manager/9.4/com.vmware.user.environment.manager-install-config/GUID-DBBC82E4-483F-4B28-9D49-4D28E08715BC.html)yönergeleri Izleyerek Kullanıcı ortam yöneticisi 'ni yükleme ve yapılandırma.

#### <a name="file-a-support-request-to-upload-vmware-horizon-pre-packaged-app-volumes"></a>VMware ufuk ön paketlenmiş uygulama birimlerini karşıya yüklemek için bir destek isteği dosyası oluşturma

App Volume Manager, yükleme işleminin bir parçası olarak, uygulama yığınları ve yazılabilir birimler sağlamak için önceden paketlenmiş birimleri kullanır. Bu birimler, uygulama yığınları ve yazılabilir birimler için şablon olarak görev yapar.

Birimleri özel bulut veri deposuna yüklemek için ESXi kök parolası gerekir. Yardım almak için bir [destek isteği](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)gönderebilirsiniz. CloudSimple destek personelinin şablonları özel bulut ortamınıza indirebilmesi için AppVolumes yükleyici paketini iliştirin.

#### <a name="cloudsimple-portal-de-escalate-privileges"></a>CloudSimple Portalı: Ön yükseltme ayrıcalıkları

Artık ' cloudowner ' kullanıcısı için [ayrıcalıkların kullanımını devre dışı](escalate-private-cloud-privileges.md#de-escalate-privileges) bırakabilirsiniz.

## <a name="ongoing-management-of-your-horizon-solution"></a>Ufuk çözümünüzün devam eden yönetimi

Özel bulut ortamınızda ufku ve App Volume Manager yazılımı üzerinde tam denetime sahip olursunuz ve gerekli yazılım yaşam döngüsü yönetimini gerçekleştiriyorduk. Yeni yazılım sürümlerinin, ufuk veya uygulama birimini güncelleştirmeden veya yükseltmeden önce özel bulut vCenter ve PSC ile uyumlu olduğundan emin olun.
