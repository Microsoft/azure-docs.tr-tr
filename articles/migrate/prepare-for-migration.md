---
title: Azure geçişi ile Azure 'a geçiş için şirket içi makineleri hazırlama
description: Bu makalede, Azure geçişi ile Azure 'a geçiş için şirket içi makinelerin nasıl hazırlanacağı açıklanır.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 61b4f41a0b36945413e45a357a5ca73ac75ceb98
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73480120"
---
# <a name="prepare-on-premises-machines-for-migration-to-azure"></a>Azure 'a geçiş için şirket içi makineleri hazırlama

Bu makalede, [Azure geçişi sunucu geçişi](migrate-services-overview.md#azure-migrate-server-migration-tool)ile Azure 'a geçirmeye başlamadan önce şirket içi makinelerin nasıl hazırlanacağı açıklanmaktadır.


Bu makalede şunları yapacaksınız:
> [!div class="checklist"]
> * Geçiş sınırlamalarını doğrulayın.
> * İşletim sistemi gereksinimlerini ve destek sınırlamalarını denetleyin.
> * Geçirmek istediğiniz makineler için URL/bağlantı noktası erişimini gözden geçirin.
> * Geçişe başlamadan önce yapmanız gerekebilecek değişiklikleri gözden geçirin.
> * Sürücü harflerinin geçişten sonra korunması için ayarları yapılandırın.
> * Geçiş sonrasında Azure VM 'lerine bağlanabilmeniz için makineleri hazırlayın.


## <a name="verify-migration-limitations"></a>Geçiş sınırlamalarını doğrulama

- Azure geçişi sunucu geçişini kullanarak tek bir Azure geçişi projesinde en fazla 35.000 VMware VM/Hyper-V VM 'yi değerlendirebilirsiniz. Bir proje, her biri için sınırlara kadar hem VMware VM 'lerini hem de Hyper-V VM 'lerini birleştirebilir.
- Geçiş için aynı anda en fazla 10 VM seçebilirsiniz. Daha fazla çoğaltma yapmanız gerekiyorsa, 10 ' un gruplar halinde çoğaltın.
- VMware aracısız geçiş için aynı anda en fazla 100 çoğaltma çalıştırabilirsiniz.

## <a name="verify-operating-system-requirements"></a>İşletim sistemi gereksinimlerini doğrulama

- [Windows işletim sistemlerinin](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) Azure 'da desteklendiğini doğrulayın.
- Azure 'da [Linux dağıtımlarınızın](../virtual-machines/linux/endorsed-distros.md) desteklendiğini doğrulayın.


## <a name="check-whats-supported"></a>Nelerin desteklendiğini denetleyin

- VMware VM 'Leri için Azure geçişi sunucu geçişi [aracısız veya aracı tabanlı geçişi](server-migrate-overview.md)destekler. VMware VM gereksinimlerini/ [aracısız](migrate-support-matrix-vmware.md#migration---limitations) ve [aracı tabanlı](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) geçişler için desteği doğrulayın.
- Hyper-V VM 'Leri için [geçiş gereksinimlerini ve desteğini](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) doğrulayın.
- Şirket içi fiziksel makineler veya diğer sanallaştırılmış sunucular için [geçiş gereksinimlerini ve desteğini](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) doğrulayın. Bu gereksinimler VMware VM gereksinimlerine benzerdir.





## <a name="review-urlport-access"></a>URL/bağlantı noktası erişimini gözden geçirin

Makinelerin geçiş sırasında internet erişimi olması gerekebilir.

- VMware VM 'Lerinin [aracısız](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) veya [aracı tabanlı](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements) geçişler sırasında erişmesi gereken URL 'leri gözden geçirin.
- Hyper-V konaklarının geçiş sırasında erişmesi gereken URL 'Leri gözden geçirin. Hyper-V VM 'lerine internet erişimi gerekmez.
- Fiziksel makinelerin veya diğer sanallaştırılmış sunucuların geçiş sırasında erişmesi gereken [URL 'Leri gözden geçirin](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements) .
- VMware VM 'lerinin/fiziksel sunucularının aracı tabanlı geçişinde, makinelerde çalışan Mobility hizmetinin Azure geçiş bileşenlerine erişmesi gerekir. Çoğaltma yönetimi için makinede çalışan hizmet, HTTPS 443 gelen bağlantı noktasında şirket içi Azure geçişi çoğaltma gereci ile iletişim kurar. Makineler, HTTPS 9443 gelen bağlantı noktasında Azure geçişi işlem sunucusuna çoğaltma verileri gönderir. Bu bağlantı noktası değiştirilebilir.


## <a name="verify-required-changes-before-migration"></a>Geçişten önce gerekli değişiklikleri doğrula

Bazı VM 'Ler, Azure 'da çalışabilecek şekilde değişiklik gerektirebilir. Azure geçişi, bu işletim sistemlerini çalıştıran VM 'Ler için bu değişiklikleri otomatik olarak yapar:
- Red Hat Enterprise Linux 6.5 +, 7.0 +
- CentOS 6.5 +, 7.0 +
- SUSE Linux Enterprise Server 12 SP1 +
- Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS
- Desek6, 8

Diğer işletim sistemleri için, geçiş işleminden önce makineleri el ile hazırlamanız gerekir. 

### <a name="prepare-windows-machines"></a>Windows makinelerini hazırlama

Bir Windows makinesini geçiriyorsanız geçişten önce bu değişiklikleri yapın. Değişiklikleri yapmadan önce VM 'yi geçirirseniz, VM Azure 'da önyüklenemeyebilir.

1. Azure VM için [Azure seri erişim konsolunu etkinleştirin](../virtual-machines/troubleshooting/serial-console-windows.md) . Bu sorun gidermeye yardımcı olur. VM 'yi yeniden başlatmanız gerekmez. Azure VM, disk görüntüsü kullanılarak önyüklenir. Bu, yeni VM için yeniden başlatma ile eşdeğerdir. 
2. Windows Server 2003 çalıştıran makineleri geçiriyorsanız, Hyper-V Konuk Tümleştirme Hizmetleri 'ni VM işletim sistemine yükleyebilirsiniz. [Daha fazla bilgi edinin](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#install-or-update-integration-services).

### <a name="prepare-linux-machines"></a>Linux makinelerini hazırlama

1. Hyper-V Linux Tümleştirme Hizmetleri 'ni yükler. Linux dağıtımların çoğu yeni sürümü varsayılan olarak bunu içerir.
2. Linux init görüntüsünü, gerekli Hyper-V sürücülerini içerecek şekilde yeniden derleyin. Bu, VM 'nin Azure 'da önyüklenmesini sağlar ve yalnızca bazı dağıtımlarda gerekli olur.
3. [Azure seri konsol günlüğünü etkinleştirin](../virtual-machines/troubleshooting/serial-console-linux.md). Bu sorun gidermeye yardımcı olur. VM 'yi yeniden başlatmanız gerekmez. Azure VM, disk görüntüsü kullanılarak önyüklenir. Bu, yeni VM için yeniden başlatma ile eşdeğerdir.
4. Kalıcı cihaz tanımlayıcıları kullanabilmeniz için cihaz eşleme dosyasını cihaz adı ile birim ilişkilendirmeleriyle güncelleştirin.
5. Fstab girdilerini kalıcı birim tanımlayıcıları kullanacak şekilde güncelleştirin.
6. Arabirim adlarını ayrılmış olan udev kurallarını MAC adresine göre kaldırın vb.
7. DHCP 'den bir IP adresi almak için ağ arabirimlerini güncelleştirin.
8. Azure 'da bir Linux VM çalıştırmak için gereken adımlar hakkında [daha fazla bilgi edinin](../virtual-machines/linux/create-upload-generic.md) ve popüler Linux dağıtımlarından bazılarına ilişkin yönergeler edinin.

## <a name="preserve-drive-letters-after-migration"></a>Geçişten sonra sürücü harflerini koru

Şirket içi bir makineyi Microsoft Azure geçirdiğinizde, ek veri disklerinin sürücü harfleri önceki değerleriyle değişebilir. Varsayılan olarak, Azure VM 'Leri, geçici depolama olarak kullanılmak üzere D sürücüsüne atanır. Bu sürücü ataması, diğer tüm bağlı depolama sürücüsü atamalarının bir harfle artımına neden olur.

Örneğin, şirket içi yüklemeniz uygulama yüklemeleri için D sürücüsüne atanan bir veri diski kullanıyorsa, VM 'yi Azure 'a geçirdikten sonra, bu sürücüye yönelik atama E sürücüsüne kadar artar. Bu otomatik atamayı engellemek ve Azure 'un bir sonraki boş sürücü harfini geçici birimine atamasını sağlamak için, depolama alanı ağı (SAN) ilkesini aşağıdaki gibi OnlineAll olarak ayarlayın:

1. Şirket içi makinede (konak sunucu değil) yükseltilmiş bir komut istemi açın.
2. **DiskPart**yazın.
3. **San**yazın. Konuk işletim sisteminin sürücü harfi tutulmazsa, **çevrimdışı** olarak veya **çevrimdışı paylaşılan** bir şekilde yüklenir.
4. **DISKPART** Isteminde **SANPOLICY = OnlineAll**yazın. Bu ayar, disklerin çevrimiçi hale gelmesini ve hem okunabilir hem de yazılabilir olmasını sağlar.
5. Test geçişi sırasında, sürücü harflerinin korunacağını doğrulayabilirsiniz.


## <a name="check-azure-vm-requirements"></a>Azure VM gereksinimlerini denetleme

Azure 'a çoğaltılan şirket içi makineler, işletim sistemi ve mimari, diskler, ağ ayarları ve VM adlandırması için Azure VM gereksinimleriyle uyumlu olmalıdır. Geçişten önce [VMware VM 'leri/fiziksel sunucular](migrate-support-matrix-vmware.md#azure-vm-requirements)ve [Hyper-V VM](migrate-support-matrix-hyper-v.md#migration-hyper-v-vm-requirements) 'lerinin gereksinimlerini doğrulayın.


## <a name="prepare-to-connect-after-migration"></a>Geçişten sonra bağlanmaya hazırlanma

Azure VM 'Leri Azure 'a geçiş sırasında oluşturulur. Geçişten sonra, yeni Azure VM 'lerine bağlanabiliyor olmanız gerekir. Başarıyla bağlanmak için gereken birkaç adım vardır.

### <a name="prepare-to-connect-to-windows-azure-vms"></a>Windows Azure VM 'lerine bağlanmaya hazırlanma

Şirket içi Windows makinelerde şunları yapın:

1. Windows ayarlarını yapılandırın. Bunlar, statik kalıcı yolların veya WinHTTP proxy 'sinin kaldırılmasını içerir.
2. [Bu hizmetlerin](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) çalıştığından emin olun.
3. Uzak Masaüstü 'nü (RDP) Şirket içi makineye uzak bağlantılara izin verecek şekilde etkinleştirin. RDP 'yi PowerShell ile etkinleştirmeyi [öğrenin](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings) .
4. Geçişten sonra Internet üzerinden bir Azure VM 'sine erişmek için, şirket içi makinedeki Windows Güvenlik Duvarı 'nda ortak profilde TCP ve UDP 'ye izin verin ve RDP 'yi tüm profiller için izin verilen bir uygulama olarak ayarlayın.
5. Geçişten sonra siteden siteye VPN üzerinden bir Azure VM 'ye erişmek istiyorsanız, şirket içi makinedeki Windows Güvenlik Duvarı 'nda etki alanı ve özel profiller için RDP 'ye izin verin. RDP trafiğine nasıl izin [vereceğinizi öğrenin](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules) . 
6. Geçiş yaparken şirket içi VM 'de bekleyen bir Windows güncelleştirmesi olmadığından emin olun. Varsa, güncelleştirmeler geçiş sonrasında Azure VM 'ye yüklenmeye başlayabilir ve güncelleştirmeler tamamlanana kadar VM 'de oturum açamazsınız.


### <a name="prepare-to-connect-with-linux-azure-vms"></a>Linux Azure VM 'leriyle bağlanmaya hazırlanma

Şirket içi Linux makinelerinde şunları yapın:

1. Secure Shell hizmetinin sistem önyüklemesi sırasında otomatik olarak başlayacak şekilde ayarlandığından emin olun.
2. Güvenlik duvarı kurallarının SSH bağlantısına izin verdiğinden emin olun.

### <a name="configure-azure-vms-after-migration"></a>Geçiş sonrasında Azure VM 'lerini yapılandırma

Geçişten sonra, oluşturulan Azure VM 'lerde aşağıdakileri yapın.

1. SANAL makineye internet üzerinden bağlanmak için, VM 'ye bir genel IP adresi atayın. Şirket içi makineniz için kullandığınız Azure VM için aynı genel IP adresini kullanamazsınız. [Daha fazla bilgi edinin](../virtual-network/virtual-network-public-ip-address.md).
2. VM 'deki ağ güvenlik grubu (NSG) kurallarının RDP veya SSH bağlantı noktasına gelen bağlantılara izin verin.
3. VM 'yi görüntülemek için [önyükleme tanılamalarını](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) denetleyin.

> [!NOTE]
> Azure savunma hizmeti, Azure VM 'lerine özel RDP ve SSH erişimi sağlar. Bu hizmet hakkında [daha fazla bilgi edinin](../bastion/bastion-overview.md) .



## <a name="next-steps"></a>Sonraki adımlar

[VMware VM](server-migrate-overview.md) 'lerini Azure 'a geçirmek için kullanmak istediğiniz yöntemi belirleyin veya [Hyper-V VM](tutorial-migrate-hyper-v.md) 'lerini veya [fiziksel sunucuları ya da sanallaştırılmış/bulut VM](tutorial-migrate-physical-virtual-machines.md)'lerini geçirmeyi başlatın.
