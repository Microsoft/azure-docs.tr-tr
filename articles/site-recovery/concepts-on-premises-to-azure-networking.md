---
title: Azure Site Recovery ile Azure VM 'lerini şirket içi yük devretmeye bağlama
description: Şirket içinden Azure 'a yük devretmeden sonra Azure Site Recovery kullanarak Azure VM 'lerine nasıl bağlanabileceğinizi açıklar
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: f222cdd315b79503b1bdea032f495c71df4682b5
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084258"
---
# <a name="connect-to-azure-vms-after-failover-from-on-premises"></a>Şirket içinden yük devretmeden sonra Azure VM 'lerine bağlanma 


Bu makalede, yük devretmeden sonra Azure VM 'lerine başarıyla bağlanabilmek için bağlantının nasıl ayarlanacağı açıklanır.

Şirket içi sanal makinelerin (VM 'Ler) ve fiziksel sunucuların Azure 'a olağanüstü durum kurtarması ayarladığınızda [Azure Site Recovery](site-recovery-overview.md) makineleri Azure 'a çoğaltmaya başlar. Daha sonra, kesintiler gerçekleştiğinde şirket içi sitenizde Azure 'a yük devretmek için yük devretme yapabilirsiniz. Yük devretme gerçekleştiğinde Site Recovery, çoğaltılan şirket içi verileri kullanarak Azure VM 'Ler oluşturur. Olağanüstü durum kurtarma planlaması kapsamında, yük devretmeden sonra bu Azure VM 'lerinde çalışan uygulamalara nasıl bağlanacağınızı belirlemeniz gerekir.

Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Yük devretmeden önce şirket içi makineleri hazırlayın.
> * Yük devretmeden sonra Azure VM 'Leri hazırlayın. 
> * Yük devretmeden sonra Azure VM 'lerinde IP adreslerini koruyun.
> * Yük devretmeden sonra Azure VM 'lerine yeni IP adresleri atayın.

## <a name="prepare-on-premises-machines"></a>Şirket içi makineleri hazırlama

Azure VM 'lerine bağlantı sağlamak için, yük devretmeden önce şirket içi makinelerinizi hazırlayın.

### <a name="prepare-windows-machines"></a>Windows makinelerini hazırlama

Şirket içi Windows makinelerde şunları yapın:

1. Windows ayarlarını yapılandırın. Bunlar, statik kalıcı yolların veya WinHTTP proxy 'nin kaldırılmasını ve disk SAN ilkesinin **OnlineAll**olarak ayarlanmasını içerir. Bu yönergeleri [izleyin](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure) .

2. [Bu hizmetlerin](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) çalıştığından emin olun.

3. Uzak Masaüstü 'nü (RDP) Şirket içi makineye uzak bağlantılara izin verecek şekilde etkinleştirin. RDP 'yi PowerShell ile etkinleştirmeyi [öğrenin](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) .

4. Yük devretmeden sonra Internet üzerinden bir Azure VM 'sine erişmek için, şirket içi makinedeki Windows Güvenlik Duvarı 'nda ortak profilde TCP ve UDP 'ye izin verin ve RDP 'yi tüm profiller için izin verilen bir uygulama olarak ayarlayın.

5. Yük devretmeden sonra siteden siteye VPN üzerinden bir Azure VM 'ye erişmek istiyorsanız, şirket içi makinedeki Windows Güvenlik Duvarı 'nda etki alanı ve özel profiller için RDP 'ye izin verin. RDP trafiğine nasıl izin [vereceğinizi öğrenin](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) .
6. Yük devretme tetiklemeniz sırasında şirket içi VM 'de bekleyen bir Windows güncelleştirmesi olmadığından emin olun. Varsa, güncelleştirmeler yük devretmeden sonra Azure VM 'ye yüklenmeye başlayabilir ve güncelleştirmeler tamamlanana kadar VM 'de oturum açamazsınız.

### <a name="prepare-linux-machines"></a>Linux makinelerini hazırlama

Şirket içi Linux makinelerinde şunları yapın:

1. Secure Shell hizmetinin sistem önyüklemesi sırasında otomatik olarak başlayacak şekilde ayarlandığından emin olun.
2. Güvenlik duvarı kurallarının SSH bağlantısına izin verdiğinden emin olun.


## <a name="configure-azure-vms-after-failover"></a>Yük devretmeden sonra Azure VM 'lerini yapılandırma

Yük devretmeden sonra, oluşturulan Azure VM 'lerinde aşağıdakileri yapın.

1. SANAL makineye internet üzerinden bağlanmak için, VM 'ye bir genel IP adresi atayın. Şirket içi makineniz için kullandığınız Azure VM için aynı genel IP adresini kullanamazsınız. [Daha fazla bilgi](../virtual-network/virtual-network-public-ip-address.md)
2. VM 'deki ağ güvenlik grubu (NSG) kurallarının RDP veya SSH bağlantı noktasına gelen bağlantılara izin verin.
3. VM 'yi görüntülemek için [önyükleme tanılamalarını](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) denetleyin.


> [!NOTE]
> Azure savunma hizmeti, Azure VM 'lerine özel RDP ve SSH erişimi sağlar. Bu hizmet hakkında [daha fazla bilgi edinin](../bastion/bastion-overview.md) .

## <a name="set-a-public-ip-address"></a>Genel IP adresi ayarlama

Bir Azure VM 'ye el ile genel bir IP adresi atamaya alternatif olarak, Site Recovery [kurtarma planında](site-recovery-create-recovery-plans.md)bir betik veya Azure Otomasyonu runbook 'u kullanarak yük devretme sırasında adresi atayabilir veya Azure TRAFFIC Manager kullanarak DNS düzeyinde yönlendirme ayarlayabilirsiniz. Ortak adres ayarlama hakkında [daha fazla bilgi edinin](concepts-public-ip-address-with-site-recovery.md) .


## <a name="assign-an-internal-address"></a>İç adres ata

Yük devretmeden sonra bir Azure VM 'sinin iç IP adresini ayarlamak için birkaç seçeneğiniz vardır:

- **Aynı IP adresini sakla**: Azure VM 'de şirket içi MAKINEYE ayrılan IP adresini de kullanabilirsiniz.
- **Farklı IP adresi kullan**: Azure VM için farklı bir IP adresi kullanabilirsiniz.


## <a name="retain-ip-addresses"></a>IP adreslerini sakla

Site Recovery, Azure 'a yük devrettikten sonra aynı IP adreslerini korumanızı sağlar. Aynı IP adresini saklamak, yük devretmeden sonra olası ağ sorunlarını önler, ancak bazı karmaşıklıklar getirir.

- Hedef Azure VM, şirket içi siteniz ile aynı IP adresini veya alt ağı kullanıyorsa, adres çakışması nedeniyle siteden siteye VPN bağlantısı veya ExpressRoute kullanarak bunlarla bağlantı oluşturamazsınız. Alt ağlar benzersiz olmalıdır.
- Yük devretmeden sonra Şirket içinden Azure 'a bağlantı gerekir, böylece uygulamalar Azure VM 'lerde kullanılabilir. Azure, uzatılmış VLAN 'Ları desteklemez, bu nedenle IP adreslerini sürdürmek istiyorsanız, alt ağın tamamında, şirket içi makineye ek olarak, IP alanını Azure 'a almanız gerekir.
- Alt ağ yük devretmesi, belirli bir alt ağın aynı anda şirket içinde ve Azure 'da kullanılabilir olmamasını sağlar.

IP adreslerini koruma aşağıdaki adımları gerektirir:

- Çoğaltılan öğenin Işlem & Ağ özelliklerinde, hedef Azure sanal makinesi için ağ ve IP adresini şirket içi ayarını yansıtacak şekilde ayarlayın.
- Alt ağlar olağanüstü durum kurtarma sürecinin bir parçası olarak yönetilmelidir. Şirket içi ağla eşleşmesi için bir Azure sanal ağı gerekir ve yük devretme ağ yollarının, alt ağın Azure 'a taşındığını ve yeni IP adresi konumlarını yansıtacak şekilde değiştirilmesi gerekir.  

### <a name="failover-example"></a>Yük devretme örneği

Bir örneğe göz atalım.

- Kurgusal şirket Woodgrove Bank, mobil uygulamalarını Azure 'da barındırdıkları kurumsal uygulamaları barındırır.
- Şirket içinden siteden siteye VPN üzerinden Azure 'a bağlanır. 
- Woodgrove, şirket içi makineleri Azure 'a çoğaltmak için Site Recovery kullanıyor.
- Şirket içi uygulamaları sabit kodlanmış IP adresleri kullanır, bu nedenle Azure 'da aynı IP adreslerini sürdürmek ister.
- Şirket içi uygulamaları çalıştıran makineler üç alt ağda çalışmaktadır:
    - 192.168.1.0/24.
    - 192.168.2.0/24
    - 192.168.3.0/24
- Azure 'da çalışan uygulamaları, Azure VNet **Azure ağında** iki alt ağda bulunur:
- 172.16.1.0/24
- 172.16.2.0/24.

Adresleri bekletmek için şu şekilde yapılır.

1. Çoğaltma etkinleştirildiklerinde, makinelerin **Azure ağına**çoğaltılması gerektiğini belirtir.
2. Azure 'da **Kurtarma ağı** oluşturur. Bu sanal ağ, şirket içi ağındaki 192.168.1.0/24 alt ağını yansıtır.
3. Woodgrove iki ağ arasında [VNET-VNet bağlantısı](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) kurar. 

    > [!NOTE]
    > Uygulama gereksinimlerine bağlı olarak, bir sanal ağdan sanal ağa bağlantı yük devretme işleminden önce, bir Site Recovery [Recovery planında](site-recovery-create-recovery-plans.md)el ile gerçekleştirilen adım/komut dosyalı adım/Azure Otomasyonu runbook 'u veya yük devretme işlemi tamamlandıktan sonra ayarlanabilir.

4. Yük devretmeden önce, Site Recovery makine özelliklerinde, sonraki yordamda açıklandığı gibi, hedef IP adresini şirket içi makinenin adresine ayarlar.
5. Yük devretmeden sonra Azure VM 'Leri aynı IP adresiyle oluşturulur. Woodgrove, VNet eşlemesi (geçiş bağlantısı etkin) kullanılarak **Azure ağından** **Kurtarma ağı** VNET 'e bağlanır.
6. Şirket içi, Woodgrove, 192.168.1.0/24 ' ün Azure 'a taşındığını yansıtmak için yolları değiştirme dahil ağ değişiklikleri yapması gerekir.  

**Yük devretmeden önce altyapı**

![Alt ağ yük devretmeden önce](./media/site-recovery-network-design/network-design7.png)


**Yük devretmeden sonra altyapı**

![Alt ağ yük devretmeden sonra](./media/site-recovery-network-design/network-design9.png)


### <a name="set-target-network-settings"></a>Hedef ağ ayarlarını ayarla

Yük devretmeden önce, hedef Azure VM için ağ ayarlarını ve IP adresini belirtin.

1.  Kurtarma Hizmetleri Kasası- **çoğaltılan öğeleri**>, şirket içi makineyi seçin.
2. Makinenin **işlem ve ağ** sayfasında, hedef Azure VM 'nin ağ ve bağdaştırıcı ayarlarını yapılandırmak için **Düzenle**' ye tıklayın.
3. **Ağ özellikleri**' nde, yük devretmeden sonra oluşturulduğu zaman Azure VM 'nin konum olarak bulunduğu hedef ağı seçin.
4. **Ağ arabirimleri**' nde, hedef ağdaki ağ bağdaştırıcılarını yapılandırın. Varsayılan olarak Site Recovery, şirket içi makinede algılanan tüm NIC 'Leri gösterir.
    - Hedef ağ **arabirim türü** ' nde, hedef ağda belirli NIC 'ye ihtiyacınız yoksa her bir NIC 'yi **birincil**, **İkincil**veya **oluşturma** ' ya ayarlayabilirsiniz. Yük devretme için bir ağ bağdaştırıcısının birincil olarak ayarlanması gerekir. Hedef ağın değiştirilmesi, Azure VM için tüm NIC 'Leri etkilediğini unutmayın.
    - Azure VM 'nin dağıtılacağı alt ağı belirtmek için NIC adına tıklayın.
    - Hedef Azure VM 'sine atamak istediğiniz özel IP adresi ile **dinamik** olarak üzerine yazın. Bir IP adresi belirtilmezse Site Recovery yük devretmede alt ağdaki bir sonraki kullanılabilir IP adresini NIC 'ye atayacaktır.
    - Şirket içi Azure 'a yük devretme için NIC 'Leri yönetme hakkında [daha fazla bilgi edinin](site-recovery-manage-network-interfaces-on-premises-to-azure.md) .


## <a name="get-new-ip-addresses"></a>Yeni IP adresleri Al

Bu senaryoda, yük devretmeden sonra Azure VM yeni bir IP adresi alır. Yük devredilen makinelerin Azure VM 'nin IP adresini işaret eden kayıtlarını güncelleştirmek için bir DNS güncelleştirmesi.



## <a name="next-steps"></a>Sonraki adımlar
Şirket içi Active Directory ve DNS 'yi Azure 'a çoğaltma [hakkında bilgi edinin](site-recovery-active-directory.md) .


