---
title: Azure Site Recovery ile Azure 'a geçişten sonra olağanüstü durum kurtarmayı ayarlama
description: Bu makalede, Azure Site Recovery kullanarak Azure 'a geçişten sonra Azure bölgeleri arasında olağanüstü durum kurtarmayı ayarlamak için makinelerin nasıl hazırlanacağı açıklanır.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 874c282ff878126297dc46ca0e7a4c19910e40a1
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159121"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Azure'a geçişten sonra Azure VM'leri için olağanüstü durumdan kurtarmayı ayarlama 


Şirket içi makineleri [Site Recovery](site-recovery-overview.md) hizmetini kullanarak [Azure VM 'lerine geçirdiyseniz](tutorial-migrate-on-premises-to-azure.md) ve artık ikincil bir Azure bölgesine olağanüstü durum kurtarma için ayarlanan VM 'leri almak istiyorsanız bu makaleyi izleyin. Makalede, Azure VM aracısının geçirilmiş VM 'lerde yüklü olduğundan ve geçişten sonra artık gerekmeyen Site Recovery Mobility hizmetini nasıl kaldırabileceği açıklanmaktadır.



## <a name="verify-migration"></a>Geçişi doğrula

Olağanüstü durum kurtarmayı ayarlamadan önce geçişin beklenen şekilde tamamlandığından emin olun. Bir geçişi başarılı bir şekilde gerçekleştirmek için, yük devretmeden sonra, geçirmek istediğiniz her makine için **tüm geçiş** seçeneğini seçmeniz gerekir. 

## <a name="verify-the-azure-vm-agent"></a>Azure VM aracısını doğrulama

Her Azure sanal makinesinde [Azure VM aracısının](../virtual-machines/extensions/agent-windows.md) yüklü olması gerekir. Azure VM 'lerini çoğaltmak için Site Recovery aracıya bir uzantı yüklenir.

- Makine, Site Recovery Mobility hizmetinin sürüm 9.7.0.0 veya sonraki bir sürümünü çalıştırıyorsa, Azure VM Aracısı Windows VM 'lerine Mobility hizmeti tarafından otomatik olarak yüklenir. Mobility hizmetinin önceki sürümlerinde aracıyı el ile yükleyeceksiniz.
- Linux sanal makineleri için Azure VM aracısını el ile yüklemelisiniz. Yalnızca geçirilen makinede yüklü olan Mobility hizmeti v 9.6 'ya veya daha önceki bir sürümdeyse, Azure VM aracısını yüklemeniz gerekir.


### <a name="install-the-agent-on-windows-vms"></a>Aracıyı Windows VM 'lerine yükler

Site Recovery Mobility hizmetinin 9.7.0.0 'den önceki bir sürümünü çalıştırıyorsanız veya aracıyı el ile yüklemek için başka bir ihtiyacınız varsa şunları yapın:  

1. VM üzerinde yönetici izinlerine sahip olduğunuzdan emin olun.
2. [VM Aracısı yükleyicisini](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)indirin.
3. Yükleyici dosyasını çalıştırın.

#### <a name="validate-the-installation"></a>Yüklemeyi doğrulama
Aracının yüklenip yüklenmediğini denetlemek için:

1. Azure VM 'de, C:\WindowsAzure\Packages klasöründe, WaAppAgent. exe dosyasını görmeniz gerekir.
2. Dosyaya sağ tıklayın ve **Özellikler**' de **Ayrıntılar** sekmesini seçin.
3. **Ürün sürümü** alanının 2.6.1198.718 veya üzeri olduğunu doğrulayın.

Windows için aracı yüklemesi hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) .

### <a name="install-the-agent-on-linux-vms"></a>Aracıyı Linux VM 'lerine yükler

[Azure LINUX VM](../virtual-machines/extensions/agent-linux.md) aracısını aşağıdaki şekilde el ile yükleyebilirsiniz:

1. Makinede yönetici izinlerinizin olduğundan emin olun.
2. Kuruluşunuzun paket deposundan bir RPM veya bir DEB paketini kullanarak Linux VM aracısını yüklemenizi önemle öneririz. Tüm [onaylı dağıtım sağlayıcıları](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) , Azure Linux Aracısı paketini görüntülerle ve depolarında tümleştirin.
    - Aracıyı yalnızca bir dağıtım deposu aracılığıyla güncelleştirmenizi önemle tavsiye ederiz.
    - Linux VM aracısının doğrudan GitHub 'dan yüklenmesini ve güncelleştirilmesini önermiyoruz.
    -  Dağıtım için en son aracı kullanılamıyorsa, nasıl yükleneceğine ilişkin yönergeler için dağıtım desteğiyle iletişim kurun. 

#### <a name="validate-the-installation"></a>Yüklemeyi doğrulama 

1. Linux VM 'de Azure aracısının çalıştığından emin olmak için şu komutu çalıştırın: **PS-e** .
2. İşlem çalışmıyorsa, aşağıdaki komutları kullanarak yeniden başlatın:
    - Ubuntu: **Service walınuxagent başlatma**
    - Diğer dağıtımlar için: **hizmet waagent başlatması**


## <a name="uninstall-the-mobility-service"></a>Mobility hizmetini kaldırma

1. Aşağıdaki yöntemlerden birini kullanarak Mobility hizmetini Azure VM 'den el ile kaldırın. 
    - Windows için Denetim Masası 'nda **Program Ekle/kaldır**> **Microsoft Azure Site Recovery Mobility hizmeti/ana hedef sunucusu**' nu kaldırın. Yükseltilmiş bir komut isteminde şunu çalıştırın:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - Linux için, kök kullanıcı olarak oturum açın. Bir terminalde **/User/Local/ASR**adresine gidin ve şu komutu çalıştırın:
        ```
        ./uninstall.sh -Y
        ```
2. Çoğaltmayı yapılandırmadan önce VM 'yi yeniden başlatın.

## <a name="next-steps"></a>Sonraki adımlar

Azure VM Aracısı 'nda Site Recovery uzantısı için [sorun gidermeyi gözden geçirin](site-recovery-extension-troubleshoot.md) .
Bir Azure VM 'yi bir ikincil bölgeye [hızlıca çoğaltın](azure-to-azure-quickstart.md) .
