---
title: Azure geçişi ile makineleri geçişe hazırlama
description: Azure geçişi ile geçiş için şirket içi makineleri nasıl hazırlayacağınızı öğrenin.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 06/08/2020
ms.openlocfilehash: d8f9d4e0b002348f286f45c6b45c96531c5d6530
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558236"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Azure 'a geçiş için şirket içi makineleri hazırlama

Bu makalede, [Azure geçişi: sunucu geçiş](migrate-services-overview.md#azure-migrate-server-migration-tool) aracı 'nı kullanarak şirket Içi makinelerin Azure 'a geçirmeden önce hazırlanması açıklanmaktadır.

Bu makalede şunları yapacaksınız:
> [!div class="checklist"]
> * Geçiş sınırlamalarını gözden geçirin.
> * VMware VM 'lerini geçirmek için bir yöntem seçin
> * Geçirmek istediğiniz makineler için hiper yönetici ve işletim sistemi gereksinimlerini denetleyin.
> * Geçirmek istediğiniz makineler için URL ve bağlantı noktası erişimini gözden geçirin.
> * Geçişe başlamadan önce yapmanız gerekebilecek değişiklikleri gözden geçirin.
> * Geçirilen makineler için Azure VM gereksinimlerini denetleme
> * Geçiş sonrasında Azure VM 'lerine bağlanabilmeniz için makineleri hazırlayın.



## <a name="verify-migration-limitations"></a>Geçiş sınırlamalarını doğrulama

Tablo, Azure geçişi için bulma, değerlendirme ve geçiş sınırlarını özetler. Geçiş işleminden önce makineleri değerlendirmenizi öneririz, ancak şunları yapmanız gerekmez.

**Senaryo** | **Project** | **Bulma/değerlendirme** | **Geçiş**
--- | --- | --- | ---
**VMware Sanal Makineleri** | Tek bir Azure geçişi projesinde 35.000 adede kadar VM bulun ve değerlendirin. | VMware için tek bir [Azure geçişi](common-questions-appliance.md) gereci ile en fazla 10.000 VMware VM bulun. | **Aracısız geçiş**: her bir vCenter Server eşzamanlı olarak en fazla 500 VM çoğaltabilirsiniz. **Aracı tabanlı geçiş**: büyük sayıda VM çoğaltmak için [çoğaltma](migrate-replication-appliance.md) gerecini [ölçeklendirebilirsiniz](./agent-based-migration-architecture.md#performance-and-scaling) .<br/><br/> Portalda, çoğaltma için aynı anda en fazla 10 makine seçebilirsiniz. Daha fazla makine çoğaltmak için 10 toplu işi ekleyin.
**Hyper-V Sanal Makineleri** | Tek bir Azure geçişi projesinde 35.000 adede kadar VM bulun ve değerlendirin. | Tek bir Azure geçişi gereci ile 5.000 adede kadar Hyper-V VM 'lerini keşfetme | Hyper-V geçişi için bir gereç kullanılmaz. Bunun yerine, Hyper-V çoğaltma sağlayıcısı her Hyper-V konağında çalışır.<br/><br/> Çoğaltma kapasitesi, sanal makine karmaşıklığı gibi performans faktörlerine göre etkilenerek çoğaltma verileri için bant genişliğini karşıya yükler.<br/><br/> Portalda, çoğaltma için aynı anda en fazla 10 makine seçebilirsiniz. Daha fazla makine çoğaltmak için 10 toplu işi ekleyin.
**Fiziksel makineler** | Tek bir Azure geçişi projesinde en fazla 35.000 makine bulun ve değerlendirin. | Fiziksel sunucular için tek bir Azure geçişi gereci ile 250 adede kadar fiziksel sunucu bulun. | Çok sayıda sunucuyu çoğaltmak için [çoğaltma](migrate-replication-appliance.md) gerecini [ölçeklendirebilirsiniz](./agent-based-migration-architecture.md#performance-and-scaling) .<br/><br/> Portalda, çoğaltma için aynı anda en fazla 10 makine seçebilirsiniz. Daha fazla makine çoğaltmak için 10 toplu işi ekleyin.

## <a name="select-a-vmware-migration-method"></a>Bir VMware geçiş yöntemi seçin

VMware VM 'lerini Azure 'a geçiriyorsanız, sizin için ne kadar uygun olduğuna karar vermek üzere aracısız ve aracı tabanlı geçiş yöntemlerini [karşılaştırın](server-migrate-overview.md#compare-migration-methods) .

## <a name="verify-hypervisor-requirements"></a>Hiper yönetici gereksinimlerini doğrulama

- [VMware aracısız](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)veya [VMware Agent tabanlı](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) gereksinimleri doğrulayın.
- [Hyper-V konağı](migrate-support-matrix-hyper-v-migration.md#hyper-v-host-requirements) gereksinimlerini doğrulayın.


## <a name="verify-operating-system-requirements"></a>İşletim sistemi gereksinimlerini doğrulama

Geçiş için desteklenen işletim sistemlerini doğrulayın:

- VMware VM 'lerini veya Hyper-V VM 'lerini geçiriyorsanız, [aracısız](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless)ve [aracı tabanlı](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) geçiş için VMware VM gereksinimlerini ve [Hyper-V VM](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms)'lerinin gereksinimlerini doğrulayın.
- [Windows işletim sistemlerinin](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) Azure 'da desteklendiğini doğrulayın.
- Azure 'da desteklenen [Linux dağıtımlarını](../virtual-machines/linux/endorsed-distros.md) doğrulayın.

## <a name="review-url-and-port-access"></a>URL ve bağlantı noktası erişimini gözden geçirin

Geçiş sırasında hangi URL 'Lerin ve bağlantı noktalarına erişildiğini gözden geçirin.

**Senaryo** | **Ayrıntılar** |  **URL’ler** | **Bağlantı noktaları**
--- | --- | --- | ---
**VMware aracısız geçişi** | Geçiş için [Azure geçişi gereç](migrate-appliance-architecture.md) kullanır. VMware VM 'lerinde hiçbir şey yüklü değil. | Gereçle keşif, değerlendirme ve geçiş için gereken genel bulutu ve kamu [URL 'lerini](migrate-appliance.md#url-access) gözden geçirin. | Aracısız geçiş için bağlantı noktası gereksinimlerini [gözden geçirin](migrate-support-matrix-vmware-migration.md#port-requirements-agentless) .
**VMware Aracısı tabanlı geçiş** | Geçiş için [çoğaltma gereç](migrate-replication-appliance.md) kullanır. Mobility hizmeti Aracısı VM 'Lere yüklendi. | Çoğaltma gerecinin erişmesi gereken [genel bulutu](migrate-replication-appliance.md#url-access) ve [Azure Kamu](migrate-replication-appliance.md#azure-government-url-access) URL 'lerini gözden geçirin. | Aracı tabanlı geçiş sırasında kullanılan bağlantı noktalarını [gözden geçirin](migrate-replication-appliance.md#port-access) .
**Hyper-V geçişi** | Geçiş için Hyper-V konaklarında yüklü bir sağlayıcı kullanır. Hyper-V VM 'lerinde hiçbir şey yüklü değil. | Konaklar üzerinde çalışan çoğaltma sağlayıcısının erişmesi gereken [genel bulutu](migrate-support-matrix-hyper-v-migration.md#url-access-public-cloud) ve [Azure Kamu](migrate-support-matrix-hyper-v-migration.md#url-access-azure-government) URL 'lerini gözden geçirin. | Hyper-V konağındaki çoğaltma sağlayıcısı, VM çoğaltma verilerini göndermek için HTTPS bağlantı noktası 443 üzerinde giden bağlantıları kullanır.
**Fiziksel makineler** | Geçiş için [çoğaltma gereç](migrate-replication-appliance.md) kullanır. Mobility hizmet Aracısı fiziksel makinelere yüklenir. | Çoğaltma gerecinin erişmesi gereken [genel bulutu](migrate-replication-appliance.md#url-access) ve [Azure Kamu](migrate-replication-appliance.md#azure-government-url-access) URL 'lerini gözden geçirin. | Fiziksel geçiş sırasında kullanılan bağlantı noktalarını [gözden geçirin](migrate-replication-appliance.md#port-access) .

## <a name="verify-required-changes-before-migrating"></a>Geçirmeden önce gerekli değişiklikleri doğrulayın

Azure 'a geçirmeden önce VM 'lerde gereken bazı değişiklikler vardır.

- Bazı işletim sistemleri için Azure geçişi, çoğaltma/geçiş işlemi sırasında otomatik olarak değişiklik yapar.
- Diğer işletim sistemleri için ayarları el ile yapılandırmanız gerekir.
- Geçişe başlamadan önce ayarların el ile yapılandırılması önemlidir. Değişikliği yapmadan önce VM 'yi geçirirseniz, VM Azure 'da önyüklenemeyebilir.

Yapmanız gereken değişiklikleri belirlemek için tabloları gözden geçirin.

### <a name="windows-machines"></a>Windows makineleri

Gerekli değişiklikler tabloda özetlenmiştir.

**Eylem** | **VMware (aracısız geçiş)** | **VMware (aracı tabanlı)/fiziksel makineler** | **Hyper-V üzerinde Windows** 
--- | --- | --- | ---
**SAN ilkesini çevrimiçi olarak yapılandırma**<br/><br/> Bu, Azure VM 'deki Windows birimlerinin, şirket içi VM ile aynı sürücü harfi atamalarını kullanmasını sağlar. | Windows Server 2008 R2 veya üstünü çalıştıran makineler için otomatik olarak ayarlayın.<br/><br/> Daha önceki işletim sistemleri için el ile yapılandırın. | Çoğu durumda otomatik olarak ayarlayın. | El ile yapılandırın.
**Hyper-V Konuk tümleştirmesi 'ni yükler** | Windows Server 2003 çalıştıran makinelere [El Ile yükler](prepare-windows-server-2003-migration.md#install-on-vmware-vms) . | Windows Server 2003 çalıştıran makinelere [El Ile yükler](prepare-windows-server-2003-migration.md#install-on-vmware-vms) . | Windows Server 2003 çalıştıran makinelere [El Ile yükler](prepare-windows-server-2003-migration.md#install-on-hyper-v-vms) .
**Azure seri konsolunu etkinleştirin**.<br/><br/>Sorun gidermeye yardımcı olması için Azure VM 'lerinde [konsolunu etkinleştirin](/troubleshoot/azure/virtual-machines/serial-console-windows) . VM 'yi yeniden başlatmanız gerekmez. Azure VM, disk görüntüsü kullanılarak önyüklenir. Disk görüntüsü önyüklemesi, yeni VM için bir yeniden başlatma ile eşdeğerdir. | El ile etkinleştir | El ile etkinleştir | El ile etkinleştir
**Geçişten sonra Bağlan**<br/><br/> Geçişten sonra bağlanmak için, geçirmeden önce yapmanız gereken birkaç adım vardır. | El ile [ayarlayın](#prepare-to-connect-to-azure-windows-vms) . | El ile [ayarlayın](#prepare-to-connect-to-azure-windows-vms) . | El ile [ayarlayın](#prepare-to-connect-to-azure-windows-vms) .


#### <a name="configure-san-policy"></a>SAN ilkesini yapılandırma

Varsayılan olarak, Azure VM 'Leri, geçici depolama olarak kullanılmak üzere D sürücüsüne atanır.

- Bu sürücü ataması, diğer tüm bağlı depolama sürücüsü atamalarının bir harfle artımına neden olur.
- Örneğin, şirket içi yüklemeniz uygulama yüklemeleri için D sürücüsüne atanan bir veri diski kullanıyorsa, VM 'yi Azure 'a geçirdikten sonra, bu sürücüye yönelik atama E sürücüsüne kadar artar. 
- Bu otomatik atamayı engellemek ve Azure 'un bir sonraki boş sürücü harfini geçici birimine atamasını sağlamak için, depolama alanı ağı (SAN) ilkesini **OnlineAll** olarak ayarlayın:

Bu ayarı aşağıdaki şekilde el ile yapılandırın:

1. Şirket içi makinede (ana sunucu değil), yükseltilmiş bir komut istemi açın.
2. **DiskPart** girin.
3. **San** girin. Konuk işletim sisteminin sürücü harfi tutulmazsa, **çevrimdışı** olarak veya **çevrimdışı paylaşılan** bir şekilde yüklenir.
4. **DISKPART** Isteminde **San Ilkesi = OnlineAll** yazın. Bu ayar, disklerin çevrimiçi hale gelmesini sağlar ve her iki diski de okuyup yazabilmenizi sağlar.
5. Test geçişi sırasında, sürücü harflerinin korunacağını doğrulayabilirsiniz.


### <a name="linux-machines"></a>Linux makineleri

Azure geçişi bu işlemleri bu sürümler için otomatik olarak tamamlar

- Red Hat Enterprise Linux 7,8, 7,7, 7,6, 7,5, 7,4, 7,0, 6. x (Azure Linux VM Aracısı da geçiş sırasında otomatik olarak yüklenir)
- Sent OS 7,7, 7,6, 7,5, 7,4, 6. x (Azure Linux VM Aracısı geçiş sırasında da otomatik olarak yüklenir)
- SUSE Linux Enterprise Server 12 SP1 +
- SUSE Linux Enterprise Server 15 SP1
- Ubuntu 19,04, 19,10, 18.04 LTS, 16.04 LTS, 14.04 LTS (geçiş sırasında Azure Linux VM Aracısı da otomatik olarak yüklenir)
- Ubuntu 18.04 LTS, 16.04 LTS
- 9, 8, 7
- Oracle Linux 7,7, 7,7-CI

Diğer sürümler için, makineleri tabloda özetlenen şekilde hazırlayın.  


**Eylem** | **Ayrıntılar** | **Linux sürümü**
--- | --- | ---
**Hyper-V Linux Tümleştirme Hizmetleri 'ni yükler** | Linux init görüntüsünü, gerekli Hyper-V sürücülerini içerecek şekilde yeniden derleyin. İnit görüntüsünü yeniden oluşturmak, VM 'nin Azure 'da önyüklenebilmesini sağlar. | Linux dağıtımların çoğu yeni sürümü bu varsayılan olarak eklenmiştir.<br/><br/> Dahil edilmedikçe, yukarıda çağrılanlar hariç tüm sürümler için el ile yükleyebilirsiniz.
**Azure seri konsol günlüğünü etkinleştirme** | Konsol günlüğünü etkinleştirmek, sorun gidermenize yardımcı olur. VM 'yi yeniden başlatmanız gerekmez. Azure VM, disk görüntüsü kullanılarak önyüklenir. Disk görüntüsü önyüklemesi, yeni VM için bir yeniden başlatma ile eşdeğerdir.<br/><br/> ' İ etkinleştirmek için [Bu yönergeleri](/troubleshoot/azure/virtual-machines/serial-console-linux) izleyin.
**Cihaz eşleme dosyasını güncelleştir** | Cihaz eşleme dosyasını cihaz adı-birim ilişkilendirmeleriyle güncelleştirin, böylece kalıcı cihaz tanımlayıcılarını kullanırsınız. | Yukarıdaki çağrılanlar hariç tüm sürümler için el ile yükler. (Yalnızca aracı tabanlı VMware senaryosunda geçerlidir)
**Fstab girdilerini Güncelleştir** |  Kalıcı birim tanımlayıcılarını kullanmak için girişleri güncelleştirin.    | Yukarıdaki çağrılanlar dışındaki tüm sürümler için el ile güncelleştirin.
**Uıdev kuralını kaldır** | Arabirim adlarını ayrılmış olan tüm udev kurallarını MAC adresine göre kaldırın vb. | Yukarıdaki çağrılanlar dışındaki tüm sürümler için el ile kaldırın.
**Ağ arabirimlerini Güncelleştir** | Ağ arabirimlerini DHCP. NST tabanlı IP adresini alacak şekilde Güncelleştir | Yukarıdaki çağrılanlar dışındaki tüm sürümler için el ile güncelleştirin.
**SSH 'yi etkinleştirme** | SSH 'nin etkinleştirildiğinden ve SSHD hizmetinin yeniden başlatma sırasında otomatik olarak başlayacak şekilde ayarlandığından emin olun.<br/><br/> Gelen SSH bağlantı isteklerinin işletim sistemi güvenlik duvarı veya komut dosyası kuralları tarafından engellenmediğinden emin olun.| Yukarıdaki çağrılanlar dışındaki tüm sürümler için el ile etkinleştirin.

Aşağıdaki tabloda, yukarıda listelenen işletim sistemleri için otomatik olarak gerçekleştirilen adımlar özetlenmektedir.


| Eylem                                      | Aracı \- tabanlı VMware geçişi | Aracısız VMware geçişi | Hyper\-V   |
|---------------------------------------------|-------------------------------|----------------------------|------------|
| Hyper \- V Linux Tümleştirme Hizmetleri 'ni yükler | Yes                           | Yes                        | Gerekli değil |
| Azure seri konsol günlüğünü etkinleştirme         | Yes                           | Yes                        | Hayır         |
| Cihaz eşleme dosyasını güncelleştir                      | Yes                           | Hayır                         | Hayır         |
| Fstab girdilerini Güncelleştir                        | Yes                           | Yes                        | Hayır         |
| Uıdev kuralını kaldır                            | Yes                           | Yes                        | Hayır         |
| Ağ arabirimlerini Güncelleştir                   | Yes                           | Yes                        | Hayır         |
| SSH 'yi etkinleştirme                                  | Hayır                            | Hayır                         | Hayır         |

[Azure 'Da LINUX VM çalıştırma](../virtual-machines/linux/create-upload-generic.md)adımları hakkında daha fazla bilgi edinin ve popüler Linux dağıtımlarından bazılarına yönelik yönergeler edinin.

Linux VM Aracısı 'nı yüklemek için [gerekli paketlerin](../virtual-machines/extensions/agent-linux.md#requirements) listesini gözden geçirin. Azure geçişi, VMware geçiş 'nin aracısız yöntemi kullanılırken RHEL6 için Linux VM aracısını otomatik olarak, RHEL7, CentOS7 (6 ' da RHEL 'ye benzer şekilde desteklenmelidir), Ubuntu 14,04, Ubuntu 16,04, Ubuntu 18.04 ' yi otomatik olarak yüklüyor.

## <a name="check-azure-vm-requirements"></a>Azure VM gereksinimlerini denetleme

Azure 'a çoğaltılan şirket içi makineler, işletim sistemi ve mimari, diskler, ağ ayarları ve VM adlandırması için Azure VM gereksinimleriyle uyumlu olmalıdır.

Geçirmeden önce, [VMware](migrate-support-matrix-vmware-migration.md#azure-vm-requirements), [Hyper-V](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements)ve [fiziksel sunucu](migrate-support-matrix-physical-migration.md#azure-vm-requirements) geçişi için Azure VM gereksinimlerini gözden geçirin.



## <a name="prepare-to-connect-after-migration"></a>Geçişten sonra bağlanmaya hazırlanma

Azure VM 'Leri Azure 'a geçiş sırasında oluşturulur. Geçişten sonra, yeni Azure VM 'lerine bağlanabiliyor olmanız gerekir. Başarılı bir şekilde bağlanmak için birden çok adım gerekiyor.

### <a name="prepare-to-connect-to-azure-windows-vms"></a>Azure Windows VM 'lerine bağlanmaya hazırlanma

Şirket içi Windows makinelerde:

1. Windows ayarlarını yapılandırın. Ayarlar, statik kalıcı yolların veya WinHTTP proxy 'sinin kaldırılmasını içerir.
2. [Gerekli hizmetlerin](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) çalıştığından emin olun.
3. Uzak Masaüstü 'nü (RDP) Şirket içi makineye uzak bağlantılara izin verecek şekilde etkinleştirin. [RDP 'yi etkinleştirmek Için PowerShell](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)'i nasıl kullanacağınızı öğrenin.
4. Geçişten sonra Internet üzerinden bir Azure VM 'sine erişmek için, şirket içi makinedeki Windows Güvenlik Duvarı 'nda ortak profilde TCP ve UDP 'ye izin verin ve RDP 'yi tüm profiller için izin verilen bir uygulama olarak ayarlayın.
5. Geçişten sonra siteden siteye VPN üzerinden bir Azure VM 'ye erişmek istiyorsanız, şirket içi makinedeki Windows Güvenlik Duvarı 'nda etki alanı ve özel profiller için RDP 'ye izin verin. [RDP trafiğine nasıl izin vereceğinizi](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules)öğrenin. 
6. Geçiş yaparken şirket içi VM 'de bekleyen bir Windows güncelleştirmesi olmadığından emin olun. Varsa, güncelleştirmeler geçiş sonrasında Azure VM 'ye yüklenmeye başlayabilir ve güncelleştirmeler tamamlanana kadar VM 'de oturum açamazsınız.


### <a name="prepare-to-connect-with-linux-azure-vms"></a>Linux Azure VM 'leriyle bağlanmaya hazırlanma

Şirket içi Linux makinelerde:

1. Secure Shell hizmetinin sistem önyüklemesi sırasında otomatik olarak başlayacak şekilde ayarlandığından emin olun.
2. Güvenlik duvarı kurallarının SSH bağlantısına izin verdiğinden emin olun.

### <a name="configure-azure-vms-after-migration"></a>Geçiş sonrasında Azure VM 'lerini yapılandırma

Geçişten sonra, oluşturulan Azure VM 'lerde şu adımları uygulayın:

1. SANAL makineye internet üzerinden bağlanmak için, VM 'ye bir genel IP adresi atayın. Azure VM için, şirket içi makineniz için kullanılandan farklı bir genel IP adresi kullanmanız gerekir. [Daha fazla bilgi edinin](../virtual-network/virtual-network-public-ip-address.md).
2. VM 'deki ağ güvenlik grubu (NSG) kurallarının RDP veya SSH bağlantı noktasına gelen bağlantılara izin verin.
3. VM 'yi görüntülemek için [önyükleme tanılamalarını](/troubleshoot/azure/virtual-machines/boot-diagnostics#enable-boot-diagnostics-on-existing-virtual-machine) denetleyin.


## <a name="next-steps"></a>Sonraki adımlar

[VMware VM](server-migrate-overview.md) 'lerini Azure 'a geçirmek için kullanmak istediğiniz yöntemi belirleyin veya [Hyper-V VM](tutorial-migrate-hyper-v.md) 'lerini veya [fiziksel sunucuları ya da sanallaştırılmış veya bulut VM](tutorial-migrate-physical-virtual-machines.md)'lerini geçirmeye başlayın.

## <a name="see-whats-supported"></a>Bkz. desteklenen özellikler

VMware VM 'Leri için, sunucu geçişi [aracısız veya aracı tabanlı geçişi](server-migrate-overview.md)destekler.

- **VMware VM 'leri**: VMware VM 'leri için [geçiş gereksinimlerini ve desteğini](migrate-support-matrix-vmware-migration.md) doğrulayın.
- **Hyper-v VM 'leri**: Hyper-v VM 'lerinin [geçiş gereksinimlerini ve desteğini](migrate-support-matrix-hyper-v-migration.md) doğrulayın.
- **Fiziksel makineler**: şirket içi fiziksel makineler ve diğer sanallaştırılmış sunucular için [geçiş gereksinimlerini ve desteğini](migrate-support-matrix-physical-migration.md) doğrulayın.