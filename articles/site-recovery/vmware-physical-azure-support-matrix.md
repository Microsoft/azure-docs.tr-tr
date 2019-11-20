---
title: Azure Site Recovery 'de VMware/fiziksel olağanüstü durum kurtarma için destek matrisi
description: Azure Site Recovery kullanarak, VMware VM 'Leri ve fiziksel sunucu ile Azure arasında olağanüstü durum kurtarma desteğini özetler.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: raynew
ms.openlocfilehash: 5c20c280fdc9097692f98c83dcb3d310e9914ecf
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133369"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>VMware VM 'Leri ve fiziksel sunucuları Azure 'a olağanüstü durum kurtarma için destek matrisi

Bu makalede, [Azure Site Recovery](site-recovery-overview.md)kullanarak VMware VM 'leri ve fiziksel sunucuları Azure 'a olağanüstü durum kurtarmaya yönelik desteklenen bileşenler ve ayarlar özetlenmektedir.

- VMware VM/fiziksel sunucu olağanüstü durum kurtarma mimarisi hakkında [daha fazla bilgi edinin](vmware-azure-architecture.md) .
- Olağanüstü durum kurtarmayı denemek için [öğreticilerimizi](tutorial-prepare-azure.md) izleyin.

## <a name="deployment-scenarios"></a>Dağıtım senaryoları

**Senaryo** | **Ayrıntılar**
--- | ---
VMware VM 'lerinin olağanüstü durum kurtarması | Şirket içi VMware VM 'lerinin Azure 'a çoğaltılması. Bu senaryoyu Azure portal veya [PowerShell](vmware-azure-disaster-recovery-powershell.md)kullanarak dağıtabilirsiniz.
Fiziksel sunucuların olağanüstü durum kurtarması | Şirket içi Windows/Linux fiziksel sunucularını Azure 'a çoğaltma. Bu senaryoyu Azure portal dağıtabilirsiniz.

## <a name="on-premises-virtualization-servers"></a>Şirket içi sanallaştırma sunucuları

**Sunucu** | **Gereksinimler** | **Ayrıntılar**
--- | --- | ---
vCenter Server | Sürüm 6,7, 6,5, 6,0 veya 5,5 | Olağanüstü durum kurtarma dağıtımınızda bir vCenter sunucusu kullanmanızı öneririz.
vSphere Konakları | Sürüm 6,7, 6,5, 6,0 veya 5,5 | VSphere konaklarının ve vCenter sunucularının, işlem sunucusuyla aynı ağda bulunmasını öneririz. Varsayılan olarak, işlem sunucusu yapılandırma sunucusunda çalışır. [Daha fazla bilgi edinin](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Site Recovery yapılandırma sunucusu

Yapılandırma sunucusu, yapılandırma sunucusu, işlem sunucusu ve ana hedef sunucu dahil olmak üzere Site Recovery bileşenleri çalıştıran bir şirket içi makinedir.

- VMware VM 'Leri için bir VMware VM oluşturmak üzere bir OVF şablonu indirerek yapılandırma sunucusunu ayarlarsınız.
- Fiziksel sunucular için yapılandırma sunucusu makinesini el ile ayarlarsınız.

**Bileşen** | **Gereksinimler**
--- |---
CPU çekirdekleri | 8
RAM | 16 GB
Disk sayısı | 3 disk<br/><br/> Diskler işletim sistemi diski, işlem sunucusu önbellek diski ve yeniden çalışma için bekletme sürücüsü içerir.
Boş disk alanı | işlem sunucusu önbelleği için 600 GB alan.
Boş disk alanı | bekletme sürücüsü için 600 GB alan.
İşletim sistemi  | Windows Server 2012 R2 veya masaüstü deneyimi ile Windows Server 2016 <br/><br> Bu gerecin yerleşik ana hedefini yeniden çalışma için kullanmayı planlıyorsanız, işletim sistemi sürümünün çoğaltılan öğelerden aynı veya daha yüksek olduğundan emin olun.|
İşletim sistemi yerel ayarı | İngilizce (en-us)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | Yapılandırma sunucusu sürüm [9,14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) veya üzeri için gerekli değildir. 
Windows Server rolleri | Active Directory Domain Services etkinleştirmeyin; Internet Information Services (IIS) veya Hyper-V. 
Grup ilkeleri| -Komut istemine erişimi engelleyin. <br/> -Kayıt defteri düzenlemesi araçlarına erişimi engelleyin. <br/> -Dosya ekleri için güven mantığı. <br/> -Betik yürütmeyi açın. <br/> - [daha fazla bilgi](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Şunları yaptığınızdan emin olun:<br/><br/> -Önceden varolan bir varsayılan Web sitesi yok <br/> - [Anonim kimlik doğrulamasını](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) etkinleştir <br/> - [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) ayarını etkinleştir  <br/> -443 numaralı bağlantı noktasında önceden bir Web sitesine/uygulamaya dinleme yapmayın<br/>
NIC türü | VMXNET3 (bir VMware sanal makinesi olarak dağıtıldığında)
IP adresi türü | Statik
Bağlantı Noktaları | denetim kanalı düzenleme için kullanılan 443<br/>veri aktarımı için 9443

## <a name="replicated-machines"></a>Çoğaltılan makineler

Site Recovery, desteklenen bir makinede çalışan herhangi bir iş yükünün çoğaltılmasını destekler.

> [!Note]
> Aşağıdaki tabloda, BIOS önyüklemesi olan makineler için destek listelenmiştir. UEFı tabanlı makinelerde destek için lütfen [depolama](#storage) bölümüne bakın.

**Bileşen** | **Ayrıntılar**
--- | ---
Makine ayarları | Azure 'a çoğaltılan makinelerin [Azure gereksinimlerini](#azure-vm-requirements)karşılaması gerekir.
Makine iş yükü | Site Recovery, desteklenen bir makinede çalışan herhangi bir iş yükünün çoğaltılmasını destekler. [Daha fazla bilgi edinin](https://aka.ms/asr_workload).
Windows Server 2019 | [Güncelleştirme paketi 34](https://support.microsoft.com/help/4490016) (Mobility hizmetinin sürüm 9,22) ve sonraki sürümlerde desteklenir.
Windows Server 2016 64-bit | Sunucu çekirdeği için desteklenir, masaüstü deneyimi olan sunucu.
Windows Server 2012 R2/Windows Server 2012 | Destekleniyor.
Windows Server 2008 R2 SP1 ve sonraki sürümler. | Destekleniyor.<br/><br/> Mobility hizmeti aracısının 9.30. x. x sürümünden (2019 Kasım 'dan itibaren sürüm bekleniyor), SP1 veya sonraki sürümleriyle Windows 2008 R2 çalıştıran makinelere [bakım yığını güncelleştirmesi (SSU)](https://support.microsoft.com/help/4490628) ve [SHA-2 güncelleştirmesi](https://support.microsoft.com/help/4474419) gerekir. SHA-1 Eylül 2019 ' den desteklenmez ve SHA-2 kod imzalama etkinleştirilmemişse, aracı uzantısı beklendiği gibi yüklenmez/yükseltilmez. [SHA-2 yükseltmesi ve gereksinimleri](https://aka.ms/SHA-2KB) hakkında daha fazla bilgi edinin.
Windows Server 2008 SP2 veya üzeri (64 bit/32 bit) |  Yalnızca geçiş için desteklenir. [Daha fazla bilgi edinin](migrate-tutorial-windows-server-2008.md).<br/><br/> Mobility hizmeti Aracısı 'nın 9.30. x. x sürümünden (2019 Kasım 'dan itibaren sürüm bekleniyor), Windows 2008 SP2 makinelerinde yüklü [bakım yığını güncelleştirmesi (SSU)](https://support.microsoft.com/help/4493730) ve [SHA-2 güncelleştirmesi](h https://support.microsoft.com/help/4474419) gerekir. ISHA-1 Eylül 2019 ' den desteklenmez ve SHA-2 kod imzalama etkinleştirilmemişse, aracı uzantısı beklendiği gibi yüklenmez/yükseltilmez. [SHA-2 yükseltmesi ve gereksinimleri](https://aka.ms/SHA-2KB) hakkında daha fazla bilgi edinin.
Windows 10, Windows 8.1, Windows 8 | Destekleniyor.
Windows 7 SP1 64-bit | [Güncelleştirme paketi 36](https://support.microsoft.com/help/4503156) (Mobility hizmetinin sürüm 9,22) ve sonraki sürümlerde desteklenir. </br></br> Mobility hizmeti Aracısı 'nın 9.30. x. x sürümünden (2019 Kasım 'dan itibaren sürüm bekleniyor), Windows 7 SP1 makinelerinde yüklü [bakım yığını güncelleştirmesi (SSU)](https://support.microsoft.com/help/4490628) ve [SHA-2 güncelleştirmesi](https://support.microsoft.com/help/4474419) gerekir.  SHA-1 Eylül 2019 ' den desteklenmez ve SHA-2 kod imzalama etkinleştirilmemişse, aracı uzantısı beklendiği gibi yüklenmez/yükseltilmez. [SHA-2 yükseltmesi ve gereksinimleri](https://aka.ms/SHA-2KB) hakkında daha fazla bilgi edinin.
Linux | Yalnızca 64 bit sistem desteklenir. 32 bit sistem desteklenmez.<br/><br/>Her Linux sunucusunda [Linux Integration Services (LIS) bileşenleri](https://www.microsoft.com/download/details.aspx?id=55106) yüklü olmalıdır. Yük devretme/yük devretme testi sonrasında Azure 'da sunucuyu önyüklemek gerekir. LIS bileşenleri eksikse, makinelerin Azure 'da önyüklemesi için çoğaltmayı etkinleştirmeden önce [bileşenleri](https://www.microsoft.com/download/details.aspx?id=55106) yüklediğinizden emin olun. <br/><br/> Azure 'da Linux sunucularını çalıştırmak için yük devretmeyi Site Recovery. Ancak, Linux satıcıları, desteği yalnızca yaşam sonuna ulaşmamış olan dağıtım sürümleriyle sınırlayabilir.<br/><br/> Linux dağıtımları üzerinde yalnızca dağıtım alt sürüm sürümü/güncelleştirmesi 'nin bir parçası olan hisse senedi çekirdekleri desteklenir.<br/><br/> Korunan makinelerin birincil Linux dağıtım sürümleri arasında yükseltilmesi desteklenmez. Yükseltmek için çoğaltmayı devre dışı bırakın, işletim sistemini yükseltin ve sonra çoğaltmayı yeniden etkinleştirin.<br/><br/> Azure 'da Linux ve açık kaynaklı teknoloji desteği hakkında [daha fazla bilgi edinin](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) .
Linux Red Hat Enterprise | 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) <br/> Red Hat Enterprise Linux 5.2-5.11 & 6.1-6.Server çalıştıran sunucularda [Linux Integration Services (LIS) bileşenleri](https://www.microsoft.com/download/details.aspx?id=55106) önceden yüklenmiş değildir. Makinelerin Azure 'da önyüklemesi için çoğaltmayı etkinleştirmeden önce [bileşenleri](https://www.microsoft.com/download/details.aspx?id=55106) yüklediğinizden emin olun.
Linux: CentOS | 5,2</b> 5,11<br/> 6,1</b> 6,10<br/> 7,0 7,6<br/> <br/> CentOS 5.2-5.11 & 6.1-6.10 çalıştıran sunucularda [Linux Integration Services (LIS) bileşenleri](https://www.microsoft.com/download/details.aspx?id=55106) önceden yüklenmiş değildir. Makinelerin Azure 'da önyüklemesi için çoğaltmayı etkinleştirmeden önce [bileşenleri](https://www.microsoft.com/download/details.aspx?id=55106) yüklediğinizden emin olun.
Ubuntu | Ubuntu 14,04 LTS sunucusu [(desteklenen çekirdek sürümlerini gözden geçirin)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16,04 LTS sunucusu [(desteklenen çekirdek sürümlerini gözden geçirin)](#ubuntu-kernel-versions) </br> Ubuntu 18,04 LTS sunucusu [(desteklenen çekirdek sürümlerini gözden geçirin)](#ubuntu-kernel-versions)
Debian | 7\. ve 8. [(desteklenen çekirdek sürümlerini gözden geçirin)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [(desteklenen çekirdek sürümlerini gözden geçirin)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> SUSE Linux Enterprise Server 11 SP3 'den SP4 'e çoğaltılan makinelerin yükseltilmesi desteklenmez. Yükseltmek için çoğaltmayı devre dışı bırakıp yükseltmeden sonra yeniden etkinleştirin.
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery)<br/><br/> Red Hat uyumlu çekirdek veya ayırıcı kurumsal çekirdek sürümü 3, 4 & 5 (UEK3, UEK4, UEK5) çalıştırılıyor 


### <a name="ubuntu-kernel-versions"></a>Ubuntu çekirdek sürümleri

**Desteklenen yayın** | **Mobility hizmeti sürümü** | **Çekirdek sürümü** |
--- | --- | --- |
14,04 LTS | [9,28][9.28 UR]| 3.13.0-24-genel-3.13.0-170-Generic,<br/>3.16.0-25-Genel-3.16.0-77-Generic,<br/>3.19.0-18-Generic ila 3.19.0-80-Generic,<br/>4.2.0-18-Generic ila 4.2.0-42-Generic,<br/>4.4.0-21-Generic-4.4.0-148-Generic,<br/>4.15.0-1023-Azure to 4.15.0-1045-Azure |
14,04 LTS | [9,27][9.27 UR]| 3.13.0-24-genel-3.13.0-170-Generic,<br/>3.16.0-25-Genel-3.16.0-77-Generic,<br/>3.19.0-18-Generic ila 3.19.0-80-Generic,<br/>4.2.0-18-Generic ila 4.2.0-42-Generic,<br/>4.4.0-21-Generic-4.4.0-148-Generic,<br/>4.15.0-1023-Azure to 4.15.0-1045-Azure |
14,04 LTS | [9,26][9.26 UR]| 3.13.0-24-genel-3.13.0-170-Generic,<br/>3.16.0-25-Genel-3.16.0-77-Generic,<br/>3.19.0-18-Generic ila 3.19.0-80-Generic,<br/>4.2.0-18-Generic ila 4.2.0-42-Generic,<br/>4.4.0-21-Generic-4.4.0-148-Generic,<br/>4.15.0-1023-Azure to 4.15.0-1045-Azure |
14,04 LTS | [9,25][9.25 UR]  | 3.13.0-24-genel-3.13.0-169-Generic,<br/>3.16.0-25-Genel-3.16.0-77-Generic,<br/>3.19.0-18-Generic ila 3.19.0-80-Generic,<br/>4.2.0-18-Generic ila 4.2.0-42-Generic,<br/>4.4.0-21-Generic-4.4.0-146-Generic,<br/>4.15.0-1023-Azure to 4.15.0-1042-Azure |
|||
16,04 LTS | [9,30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.4.0-21-Generic ile 4.4.0-166-Generic,<br/>4.8.0-34-Generic ile 4.8.0-58-Generic,<br/>4.10.0-14-generic-4.10.0-42-Generic,<br/>4.11.0-13-Genel-4.11.0-14-generic,<br/>4.13.0-16-Generic to 4.13.0-45-Generic,<br/>4.15.0-13-Genel-4.15.0-66-Generic<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1061-Azure|
16,04 LTS | [9,28][9.28 UR] | 4.4.0-21-Generic-4.4.0-159-Generic,<br/>4.8.0-34-Generic ile 4.8.0-58-Generic,<br/>4.10.0-14-generic-4.10.0-42-Generic,<br/>4.11.0-13-Genel-4.11.0-14-generic,<br/>4.13.0-16-Generic to 4.13.0-45-Generic,<br/>4.15.0-13-Genel-4.15.0-58-Generic<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1055-Azure|
16,04 LTS | [9,27][9.27 UR] | 4.4.0-21-Generic-4.4.0-154-Generic,<br/>4.8.0-34-Generic ile 4.8.0-58-Generic,<br/>4.10.0-14-generic-4.10.0-42-Generic,<br/>4.11.0-13-Genel-4.11.0-14-generic,<br/>4.13.0-16-Generic to 4.13.0-45-Generic,<br/>4.15.0-13-Genel-4.15.0-54-Generic<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1050-Azure|
16,04 LTS | [9,26][9.26 UR] | 4.4.0-21-Generic-4.4.0-148-Generic,<br/>4.8.0-34-Generic ile 4.8.0-58-Generic,<br/>4.10.0-14-generic-4.10.0-42-Generic,<br/>4.11.0-13-Genel-4.11.0-14-generic,<br/>4.13.0-16-Generic to 4.13.0-45-Generic,<br/>4.15.0-13-Generic ila 4.15.0-50-Generic<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1045-Azure|
16,04 LTS | [9,25][9.25 UR] | 4.4.0-21-Generic-4.4.0-146-Generic,<br/>4.8.0-34-Generic ile 4.8.0-58-Generic,<br/>4.10.0-14-generic-4.10.0-42-Generic,<br/>4.11.0-13-Genel-4.11.0-14-generic,<br/>4.13.0-16-Generic to 4.13.0-45-Generic,<br/>4.15.0-13-Genel-4.15.0-48-Generic<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1042-Azure|
16,04 LTS | [9,24][9.24 UR] | 4.4.0-21-Generic-4.4.0-143.360-Generic,<br/>4.8.0-34-Generic ile 4.8.0-58-Generic,<br/>4.10.0-14-generic-4.10.0-42-Generic,<br/>4.11.0-13-Genel-4.11.0-14-generic,<br/>4.13.0-16-Generic to 4.13.0-45-Generic,<br/>4.15.0-13-Genel-4.15.0-46-genel<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1040-Azure|
|||
18,04 LTS | [9,29](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) | 4.15.0-20-Genel-4.15.0-62-Generic </br> 4.18.0-13-Genel-4.18.0-25-genel </br> 5.0.0-15-genel-5.0.0-27-Generic </br> 4.15.0-1009-Azure-4.15.0-1037-Azure </br> 4.18.0-1006-Azure to 4.18.0-1025-Azure </br> 5.0.0-1012-Azure-5.0.0-1018-Azure
18,04 LTS | [9,30](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) | 4.15.0-20-Genel-4.15.0-66-Generic </br> 4.18.0-13-Genel-4.18.0-25-genel </br> 5.0.0-15-genel-5.0.0-32-Generic </br> 4.15.0-1009-Azure-4.15.0-1037-Azure </br> 4.18.0-1006-Azure to 4.18.0-1025-Azure </br> 5.0.0-1012-Azure-5.0.0-1023-Azure


### <a name="debian-kernel-versions"></a>Çekirdek sürümlerini kaldırma


**Desteklenen yayın** | **Mobility hizmeti sürümü** | **Çekirdek sürümü** |
--- | --- | --- |
Deyi 7 | [9,25][9.25 UR],[9,26][9.26 UR], [9,27][9.27 UR], [9,28][9.28 UR]| 3.2.0-4-AMD64-3.2.0-6-AMD64, 3.16.0 -0. BPO. 4-AMD64 |
|||
Debian 8 | [9,28][9.28 UR] | 3.16.0-4-AMD64-3.16.0-10-AMD64, 4.9.0 -0. BPO. 4-AMD64 ila 4.9.0 -0. BPO. 9-AMD64 |
Debian 8 | [9,27][9.27 UR] | 3.16.0-4-AMD64-3.16.0-9-AMD64, 4.9.0 -0. BPO. 4-AMD64-4.9.0 -0. BPO. 9-AMD64 |
Debian 8 | [9,25][9.25 UR], [9,26][9.26 UR] | 3.16.0-4-AMD64-3.16.0-8-AMD64, 4.9.0 -0. BPO. 4-AMD64-4.9.0 -0. BPO. 8-AMD64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 desteklenen çekirdek sürümü

**Yayın** | **Mobility hizmeti sürümü** | **Çekirdek sürümü** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9,28][9.28 UR] | SP1 3.12.49-11-varsayılan olarak 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45-varsayılan olarak 3.12.74-60.64.118-default</br></br> SP2 4.4.21-69-varsayılan olarak 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73-varsayılan olarak 4.4.121-92.117-default</br></br>SP3 4.4.73-5-varsayılan olarak 4.4.180-94.100-default</br></br>SP3 4.4.138-4.7-Azure to 4.4.180-4.31-Azure</br></br>SP4 4.12.14-94.41-varsayılan olarak 4.12.14-95.29-default</br>SP4 4.12.14-6.3-Azure to 4.12.14-6.23-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9,27][9.27 UR] | SP1 3.12.49-11-varsayılan olarak 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45-varsayılan olarak 3.12.74-60.64.115-default</br></br> SP2 4.4.21-69-varsayılan olarak 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73-varsayılan olarak 4.4.121-92.114-default</br></br>SP3 4.4.73-5-varsayılan olarak 4.4.180-94.97-default</br></br>SP3 4.4.138-4.7-Azure to 4.4.180-4.31-Azure</br></br>SP4 4.12.14-94.41-varsayılan olarak 4.12.14-95.19-default</br>SP4 4.12.14-6.3-Azure to 4.12.14-6.15-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9,26][9.26 UR] | SP1 3.12.49-11-varsayılan olarak 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45-varsayılan olarak 3.12.74-60.64.110-default</br></br> SP2 4.4.21-69-varsayılan olarak 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73-varsayılan olarak 4.4.121-92.109-default</br></br>SP3 4.4.73-5-varsayılan olarak 4.4.178-94.91-default</br></br>SP3 4.4.138-4.7-Azure to 4.4.178-toplam 4,28 olacaktır-Azure</br></br>SP4 4.12.14-94.41-varsayılan olarak 4.12.14-95.16-default</br>SP4 4.12.14-6.3-Azure to 4.12.14-6.9-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9,25][9.25 UR] | SP1 3.12.49-11-varsayılan olarak 3.12.74-60.64.40-default</br></br> SP1 (LTSS) 3.12.74-60.64.45-varsayılan olarak 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-varsayılan olarak 4.4.120-92.70-default</br></br>SP2 (LTSS) 4.4.121-92.73-varsayılan olarak 4.4.121-92.104-default</br></br>SP3 4.4.73-5-varsayılan olarak 4.4.176-94.88-default</br></br>SP3 4.4.138-4.7-Azure to 4.4.176-4.25-Azure</br></br>SP4 4.12.14-94.41-varsayılan olarak 4.12.14-95.13-default</br>SP4 4.12.14-6.3-Azure to 4.12.14-6.9-Azure |

## <a name="linux-file-systemsguest-storage"></a>Linux dosya sistemleri/Konuk depolama

**Bileşen** | **Destekleniyor**
--- | ---
Dosya sistemleri | ext3, ext4, XFS
Birim Yöneticisi | -LVM destekleniyor.<br/> -LVM üzerinde-/Boot [güncelleştirme paketi 31](https://support.microsoft.com/help/4478871/) ' den (Mobility hizmeti sürüm 9,20) ve sonraki sürümlerde desteklenir. Önceki Mobility hizmeti sürümlerinde desteklenmez.<br/> -Birden çok işletim sistemi diski desteklenmez.
Parasanallaştırılmış depolama cihazları | Parasanallaştırılmış sürücüler tarafından dışarı aktarılan cihazlar desteklenmez.
Çok kuyruğundaki blok GÇ cihazları | Desteklenmiyor.
HP CCıS depolama denetleyicisi ile fiziksel sunucular | Desteklenmiyor.
Cihaz/bağlama noktası adlandırma kuralı | Cihaz adı veya bağlama noktası adı benzersiz olmalıdır.<br/> İki cihazda/bağlama noktasında büyük/küçük harf duyarlı adlara sahip olmadığından emin olun. Örneğin, *Device1* ve *DEVICE1* ile aynı VM için cihaz adlandırma desteklenmez.
Dizinler | Mobility hizmetinin sürüm 9,20 ' den önceki bir sürümünü çalıştırıyorsanız ( [güncelleştirme paketi 31](https://support.microsoft.com/help/4478871/)' de yayımlanmıştır), bu kısıtlamalar uygulanır:<br/><br/> -Bu dizinler (ayrı bölümler/dosya sistemleri olarak ayarlandıysa) kaynak sunucudaki aynı işletim sistemi diskinde olmalıdır:/(kök),/Boot,/usr,/usr/local,/var,/etc.</br> -/Boot dizini bir disk bölümünde olmalı ve bir LVM birimi olmamalıdır.<br/><br/> Sürüm 9,20 ' den itibaren bu kısıtlamalar uygulanmaz. 
Önyükleme dizini | -Önyükleme diskleri, GPT bölüm biçiminde olmamalıdır. Bu bir Azure mimarisi kısıtlamasıdır. GPT diskleri, veri diskleri olarak desteklenir.<br/><br/> Bir VM 'de birden çok önyükleme diski desteklenmez<br/><br/> -bir LVM biriminde birden fazla disk arasında-/Boot desteklenmez.<br/> -Önyükleme diski olmayan bir makine çoğaltılamıyor.
Boş alan gereksinimleri| /root bölümünde 2 GB <br/><br/> yükleme klasöründe 250 MB
XFSv5 | Meta veri sağlama toplamı gibi XFS dosya sistemlerindeki XFSv5 özellikleri desteklenir (Mobility hizmeti sürüm 9,10 ve üzeri).<br/> Bölüm için XFS süper bloğunu denetlemek üzere xfs_info yardımcı programını kullanın. `ftype` 1 olarak ayarlanırsa, XFSv5 özellikleri kullanımda olur.
BTRFS | BTRFS, [güncelleştirme paketi 34](https://support.microsoft.com/help/4490016) (Mobility hizmetinin sürüm 9,22) ve sonraki sürümlerde desteklenir. Şu durumlarda BTRFS desteklenmez:<br/><br/> -BTRFS dosya sistemi alt birimi, koruma etkinleştirildikten sonra değiştirilir.</br> -BTRFS dosya sistemi birden çok diske yayılır.</br> -BTRFS dosya sistemi RAID 'i destekler.

## <a name="vmdisk-management"></a>VM/disk yönetimi

**Eylem** | **Ayrıntılar**
--- | ---
Çoğaltılan VM 'de diski yeniden boyutlandır | Destekleniyor.
Çoğaltılan VM 'ye disk ekleme | Desteklenmiyor.<br/> VM için çoğaltmayı devre dışı bırakın, diski ekleyin ve ardından çoğaltmayı yeniden etkinleştirin.

## <a name="network"></a>Ağ

**Bileşen** | **Destekleniyor**
--- | ---
Konak ağı NIC Grubu oluşturma | VMware VM 'Leri için desteklenir. <br/><br/>Fiziksel makine çoğaltması için desteklenmez.
Konak ağ VLAN 'ı | Evet.
Konak ağı IPv4 | Evet.
Konak ağı IPv6 | Hayır.
Konuk/sunucu ağı NIC Grubu oluşturma | Hayır.
Konuk/sunucu ağı IPv4 | Evet.
Konuk/sunucu ağı IPv6 | Hayır.
Konuk/sunucu ağı statik IP 'si (Windows) | Evet.
Konuk/sunucu ağı statik IP (Linux) | Evet. <br/><br/>VM 'Ler, yeniden çalışma sırasında DHCP kullanacak şekilde yapılandırılmıştır.
Konuk/sunucu ağı birden çok NIC | Evet.


## <a name="azure-vm-network-after-failover"></a>Azure VM ağı (yük devretmeden sonra)

**Bileşen** | **Destekleniyor**
--- | ---
Azure ExpressRoute | Yes
ILB | Yes
ELB | Yes
Azure Traffic Manager | Yes
Çoklu NIC | Yes
Ayrılmış IP adresi | Yes
IPv4 | Yes
Kaynak IP adresini sakla | Yes
Azure sanal ağ hizmet uç noktaları<br/> | Yes
Hızlandırılmış ağ iletişimi | Hayır

## <a name="storage"></a>Depolama
**Bileşen** | **Destekleniyor**
--- | ---
Dinamik disk | İşletim sistemi diski, temel bir disk olmalıdır. <br/><br/>Veri diskleri dinamik diskler olabilir
Docker disk yapılandırması | Hayır
Konak NFS | VMware için Evet<br/><br/> Fiziksel sunucular için Hayır
Ana bilgisayar SAN (Iscsı/FC) | Yes
Ana bilgisayar vSAN | VMware için Evet<br/><br/> Fiziksel sunucular için yok
Ana bilgisayar çok yollu (MPIO) | Evet, Microsoft DSM ile test edildi, EMC PowerPath 5,7 SP4, Clariiyon için EMC PowerPath DSM
Konak sanal birimleri (Vvir) | VMware için Evet<br/><br/> Fiziksel sunucular için yok
Konuk/sunucu VMDK | Yes
Konuk/sunucu paylaşılan küme diski | Hayır
Konuk/sunucu tarafından şifrelenen disk | Hayır
Konuk/sunucu NFS | Hayır
Konuk/sunucu Iscsı | Geçiş için-Evet<br/>Olağanüstü durum kurtarma için-Hayır, Iscsı sanal makineye bağlı bir disk olarak yeniden çalışır
Konuk/sunucu SMB 3,0 | Hayır
Konuk/sunucu RDM | Yes<br/><br/> Fiziksel sunucular için yok
Konuk/sunucu diski > 1 TB | Evet, disk 1024 MB 'tan büyük olmalıdır<br/><br/>Yönetilen disklere çoğaltma yaparken 8.192 GB 'a kadar (9,26 sürüm ve sonraki sürümler)<br></br> Depolama hesaplarına çoğaltma yaparken 4.095 GB 'a kadar
4K mantıksal ve 4k fiziksel sektör boyutuna sahip konuk/sunucu diski | Hayır
4K mantıksal ve 512 bayt fiziksel kesim boyutuna sahip konuk/sunucu diski | Hayır
Şeritli disk > 4 TB olan konuk/sunucu birimi <br/><br/>Mantıksal birim yönetimi (LVM)| Yes
Konuk/sunucu-depolama alanları | Hayır
Konuk/sunucu Hot Add/Remove disk | Hayır
Konuk/sunucu-diski hariç tut | Yes
Konuk/sunucu çok yollu (MPIO) | Hayır
Konuk/sunucu GPT bölümleri | Beş bölüm [güncelleştirme paketi 37](https://support.microsoft.com/help/4508614/) (Mobility hizmetinin sürüm 9,25) ve sonraki sürümlerde desteklenir. Daha önce dört bölüm destekleniyordu.
ReFS | Dayanıklı dosya sistemi, Mobility hizmeti sürüm 9,23 veya üzeri sürümlerde desteklenir
Konuk/sunucu EFı/UEFı önyüklemesi | -Windows Server 2012 veya üzeri için desteklenir, Mobility Aracısı sürüm 9,30 ve sonraki sürümleri içeren SLES 12 SP4 ve RHEL 8,0<br/> -Secure UEFı önyükleme türü desteklenmez. 

## <a name="replication-channels"></a>Çoğaltma kanalları

|**Çoğaltma türü**   |**Destekleniyor**  |
|---------|---------|
|Boşaltılan veri aktarımları (ODX)    |       Hayır  |
|Çevrimdışı Dengeli Dağıtım        |   Hayır      |
| Azure Data Box | Hayır

## <a name="azure-storage"></a>Azure Storage

**Bileşen** | **Destekleniyor**
--- | ---
Yerel olarak yedekli depolama | Yes
Coğrafi olarak yedekli depolama | Yes
Okuma Erişimli Coğrafi olarak yedekli depolama | Yes
Seyrek Erişimli Depolama | Hayır
Sık erişimli depolama| Hayır
Blok blobları | Hayır
Bekleyen şifreleme (SSE)| Yes
Bekleyen şifreleme (CMK)| Hayır
Premium depolama | Yes
İçeri/dışarı aktarma hizmeti | Hayır
Sanal ağlar için Azure Storage güvenlik duvarları | Evet.<br/> Hedef depolama/önbellek depolama hesabında yapılandırıldı (çoğaltma verilerini depolamak için kullanılır).
Genel amaçlı v2 depolama hesapları (sık erişimli ve seyrek erişimli Katmanlar) | Evet (v1 ile karşılaştırıldığında v2 için Işlem maliyetleri önemli ölçüde yüksektir)

## <a name="azure-compute"></a>Azure işlem

**Özelliği** | **Destekleniyor**
--- | ---
Kullanılabilirlik kümeleri | Yes
Kullanılabilirlik alanları | Hayır
HUB | Yes
Yönetilen diskler | Yes

## <a name="azure-vm-requirements"></a>Azure VM gereksinimleri

Azure 'a çoğaltılan şirket içi VM 'Ler, bu tabloda özetlenen Azure VM gereksinimlerini karşılamalıdır. Site Recovery, çoğaltma için bir önkoşul denetimi çalıştırdığında, bazı gereksinimler karşılanmazsa denetim başarısız olur.

**Bileşen** | **Gereksinimler** | **Ayrıntılar**
--- | --- | ---
Konuk işletim sistemi | Çoğaltılan makineler için [desteklenen işletim sistemlerini](#replicated-machines) doğrulayın. | Desteklenmiyorsa denetim başarısız olur.
Konuk işletim sistemi mimarisi | 64 bit. | Desteklenmiyorsa denetim başarısız olur.
İşletim sistemi disk boyutu | 2\.048 GB 'a kadar. | Desteklenmiyorsa denetim başarısız olur.
İşletim sistemi disk sayısı | 1 | Desteklenmiyorsa denetim başarısız olur.
Veri diski sayısı | 64 veya daha az. | Desteklenmiyorsa denetim başarısız olur.
Veri diski boyutu | Yönetilen diske çoğaltma sırasında 8.192 GB 'a kadar (9,26 sürüm ve sonraki sürümler)<br></br>Depolama hesabına çoğaltılırken 4.095 GB 'a kadar| Desteklenmiyorsa denetim başarısız olur.
Ağ bağdaştırıcıları | Birden çok bağdaştırıcı desteklenir. |
Paylaşılan VHD | Desteklenmiyor. | Desteklenmiyorsa denetim başarısız olur.
FC diski | Desteklenmiyor. | Desteklenmiyorsa denetim başarısız olur.
BitLocker | Desteklenmiyor. | Bir makine için çoğaltmayı etkinleştirmeden önce BitLocker devre dışı bırakılmalıdır. |
VM adı | 1 ile 63 karakter arasında.<br/><br/> Harfler, sayılar ve kısa çizgilerden oluşabilir.<br/><br/> Makine adı bir harf veya sayıyla başlamalı ve bitmelidir. |  Site Recovery makine özelliklerindeki değeri güncelleştirin.

## <a name="resource-group-limits"></a>Kaynak grubu sınırları

Tek bir kaynak grubu altında korunabilecek sanal makinelerin sayısını anlamak için [abonelik limitleri ve kotaları](https://docs.microsoft.com/azure/azure-subscription-service-limits#resource-group-limits) hakkındaki makaleye bakın.

## <a name="churn-limits"></a>Karmaşıklık limitleri

Şu tablo, Azure Site Recovery sınırlarını sağlar. 
- Bu sınırlar, testlerimize dayalıdır, ancak olası tüm uygulama g/ç birleşimlerini kapsamamaktadır.
- Gerçek sonuçlar, uygulamanızın G/Ç karışımına göre değişebilir.
- En iyi sonuçlar için [dağıtım planlayıcısı aracını](site-recovery-deployment-planner.md)çalıştırmanızı ve uygulamanız için doğru performans resmini elde etmek üzere yük devretme testi kullanarak kapsamlı uygulama testleri gerçekleştirmenizi önemle öneririz.

**Çoğaltma hedefi** | **Ortalama kaynak disk G/Ç boyutu** |**Ortalama kaynak disk veri değişim sıklığı** | **Günlük toplam kaynak disk veri değişim sıklığı**
---|---|---|---
Standart depolama | 8 KB | 2 MB/sn | Disk başına 168 GB
Premium P10 veya P15 disk | 8 KB  | 2 MB/sn | Disk başına 168 GB
Premium P10 veya P15 disk | 16 KB | 4 MB/sn |  Disk başına 336 GB
Premium P10 veya P15 disk | 32 KB veya daha büyük | 8 MB/sn | Disk başına 672 GB
Premium P20 veya P30 veya P40 veya P50 disk | 8 KB    | 5 MB/sn | Disk başına 421 GB
Premium P20 veya P30 veya P40 veya P50 disk | 16 KB veya daha büyük |20 MB/sn | disk başına 1684 GB


**Kaynak veri değişim sıklığı** | **Üst Sınır**
---|---
VM üzerindeki tüm disklerde en yüksek veri değişim sıklığı | 54 MB/sn
İşlem Sunucusu tarafından desteklenen günlük en fazla veri değişim sıklığı | 2 TB

- Bunlar yüzde 30 G/Ç çakışmasını varsayan ortalama sayılardır.
- Site Recovery; çakışma oranı, büyük yazma boyutları ve gerçek iş yükü G/Ç davranışına göre daha yüksek aktarım hızını işleyebilir.
- Bu numaralar yaklaşık beş dakikalık tipik bir biriktirme listesini varsayar. Diğer bir deyişle, veriler karşıya yüklendikten sonra işlenir ve beş dakika içinde kurtarma noktası oluşturulur.

## <a name="vault-tasks"></a>Kasa görevleri

**Eylem** | **Destekleniyor**
--- | ---
Kasayı kaynak grupları arasında taşıma | Hayır
Kasayı abonelik içinde ve abonelikler arasında taşıma | Hayır
Depolama, ağ ve Azure VM 'lerini kaynak grupları arasında taşıma | Hayır
Depolama, ağ, Azure VM 'Leri ve abonelikler arasında taşıma. | Hayır


## <a name="obtain-latest-components"></a>En son bileşenleri edinin

**Adı** | **Açıklama** | **Ayrıntılar**
--- | --- | ---
Yapılandırma Sunucusu | Şirket içi yüklendi.<br/> Şirket içi VMware sunucuları veya fiziksel makineler ile Azure arasındaki iletişimleri koordine eder. | - yapılandırma sunucusu [hakkında bilgi edinin](vmware-physical-azure-config-process-server-overview.md) .<br/> - en son sürüme yükseltme [hakkında bilgi edinin](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) .<br/> - yapılandırma sunucusunu ayarlama [hakkında bilgi edinin](vmware-azure-deploy-configuration-server.md) . 
İşlem sunucusu | Varsayılan olarak yapılandırma sunucusuna yüklenir.<br/> Çoğaltma verilerini alır, önbelleğe alma, sıkıştırma ve şifreleme ile iyileştirir ve Azure 'a gönderir.<br/> Dağıtımınız büyüdükçe, daha büyük hacimde çoğaltma trafiğini işlemek için ek işlem sunucuları ekleyebilirsiniz. | işlem sunucusu [hakkında bilgi](vmware-physical-azure-config-process-server-overview.md) - .<br/> - en son sürüme yükseltme [hakkında bilgi edinin](vmware-azure-manage-process-server.md#upgrade-a-process-server) .<br/> - genişleme işlem sunucularını ayarlama [hakkında bilgi edinin](vmware-physical-large-deployment.md#set-up-a-process-server) .
Mobility hizmeti | Çoğaltmak istediğiniz VMware VM veya fiziksel sunucularda yüklü.<br/> Şirket içi VMware sunucuları/fiziksel sunucular ile Azure arasında çoğaltmayı koordine eder.| - Mobility hizmeti [hakkında bilgi edinin](vmware-physical-mobility-service-overview.md) .<br/> - en son sürüme yükseltme [hakkında bilgi edinin](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) .<br/> 



## <a name="next-steps"></a>Sonraki adımlar
VMware VM 'lerinin olağanüstü durum kurtarması için Azure 'u [nasıl hazırlayacağınızı öğrenin](tutorial-prepare-azure.md) .

[9.28 UR]: https://support.microsoft.com/en-in/help/4521530/update-rollup-40-for-azure-site-recovery
[9.27 UR]: https://support.microsoft.com/en-in/help/4517283/update-rollup-39-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
