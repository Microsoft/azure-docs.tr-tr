---
title: Azure Site Recovery 'de VMware/fiziksel olağanüstü durum kurtarma için destek matrisi
description: Azure Site Recovery kullanarak, VMware VM 'Leri ve fiziksel sunucu ile Azure arasında olağanüstü durum kurtarma desteğini özetler.
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: c7f2d6ecd01959e239a1ab048018452b2ae5fc20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103495224"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>VMware VM 'Leri ve fiziksel sunucuları Azure 'a olağanüstü durum kurtarma için destek matrisi

Bu makalede, [Azure Site Recovery](site-recovery-overview.md)kullanarak VMware VM 'leri ve fiziksel sunucuları Azure 'a olağanüstü durum kurtarmaya yönelik desteklenen bileşenler ve ayarlar özetlenmektedir.

- VMware VM/fiziksel sunucu olağanüstü durum kurtarma mimarisi hakkında [daha fazla bilgi edinin](vmware-azure-architecture.md) .
- Olağanüstü durum kurtarmayı denemek için [öğreticilerimizi](tutorial-prepare-azure.md) izleyin.

> [!NOTE]
> Site Recovery, müşteri verilerini, kaynak makineler için olağanüstü durum kurtarma için ayarlanan hedef bölgeden taşımaz veya depolamaz. Müşteriler, bir kurtarma hizmetleri kasasını tercih ettikleri farklı bir bölgeden seçebilirler. Kurtarma Hizmetleri Kasası meta veriler içeriyor ancak gerçek müşteri verisi yok.

## <a name="deployment-scenarios"></a>Dağıtım senaryoları

**Senaryo** | **Ayrıntılar**
--- | ---
VMware VM 'lerinin olağanüstü durum kurtarması | Şirket içi VMware VM 'lerinin Azure 'a çoğaltılması. Bu senaryoyu Azure portal veya [PowerShell](vmware-azure-disaster-recovery-powershell.md)kullanarak dağıtabilirsiniz.
Fiziksel sunucuların olağanüstü durum kurtarması | Şirket içi Windows/Linux fiziksel sunucularını Azure 'a çoğaltma. Bu senaryoyu Azure portal dağıtabilirsiniz.

## <a name="on-premises-virtualization-servers"></a>Şirket içi sanallaştırma sunucuları

**Sunucu** | **Gereksinimler** | **Ayrıntılar**
--- | --- | ---
vCenter Server | Sürüm 7,0, bu sürümde sonraki güncelleştirmeler &, 6,7, 6,5, 6,0 veya 5,5 | Olağanüstü durum kurtarma dağıtımınızda bir vCenter sunucusu kullanmanızı öneririz.
vSphere Konakları | Sürüm 7,0, bu sürümde sonraki güncelleştirmeler &, 6,7, 6,5, 6,0 veya 5,5 | VSphere konaklarının ve vCenter sunucularının, işlem sunucusuyla aynı ağda bulunmasını öneririz. Varsayılan olarak, işlem sunucusu yapılandırma sunucusunda çalışır. [Daha fazla bilgi edinin](vmware-physical-azure-config-process-server-overview.md).

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
Grup ilkeleri| -Komut istemine erişimi engelleyin. <br/> -Kayıt defteri düzenlemesi araçlarına erişimi engelleyin. <br/> -Dosya ekleri için güven mantığı. <br/> -Betik yürütmeyi açın. <br/> - [Daha fazla bilgi](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))|
IIS | Şunları yaptığınızdan emin olun:<br/><br/> -Önceden var olan varsayılan Web siteniz yok <br/> - [Anonim kimlik doğrulamasını](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) etkinleştir <br/> - [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) ayarını etkinleştir  <br/> -443 numaralı bağlantı noktasında önceden bir Web sitesine/uygulamaya dinleme yapmayın<br/>
NIC türü | VMXNET3 (bir VMware sanal makinesi olarak dağıtıldığında)
IP adresi türü | Statik
Bağlantı noktaları | denetim kanalı düzenleme için kullanılan 443<br/>veri aktarımı için 9443

> [!NOTE]
İşletim sisteminin Ingilizce yerel ayar ile yüklenmesi gerekebilir. Yerel ayar sonrası yükleme işleminin dönüştürülmesi olası sorunlara yol açabilir.

## <a name="replicated-machines"></a>Çoğaltılan makineler

Site Recovery, desteklenen bir makinede çalışan herhangi bir iş yükünün çoğaltılmasını destekler.

**Bileşen** | **Ayrıntılar**
--- | ---
Makine ayarları | Azure 'a çoğaltılan makinelerin [Azure gereksinimlerini](#azure-vm-requirements)karşılaması gerekir.
Makine iş yükü | Site Recovery, desteklenen bir makinede çalışan herhangi bir iş yükünün çoğaltılmasını destekler. [Daha fazla bilgi edinin](./site-recovery-workload.md).
Makine adı | Makinenin görünen adının [Azure ayrılmış kaynak adlarına](../azure-resource-manager/templates/error-reserved-resource-name.md) sahip olmadığından emin olun<br/><br/> Mantıksal birim adları büyük/küçük harfe duyarlı değildir. Cihazdaki iki birimin aynı ada sahip olmadığından emin olun. Ex: "voLUME1", "voLUME1" adlı birimler Azure Site Recovery aracılığıyla korunamaz.

### <a name="for-windows"></a>Windows için

**İşletim sistemi** | **Ayrıntılar**
--- | ---
Windows Server 2019 | [Güncelleştirme paketi 34](https://support.microsoft.com/help/4490016) (Mobility hizmetinin sürüm 9,22) ve sonraki sürümlerde desteklenir.
Windows Server 2016 64-bit | Sunucu çekirdeği için desteklenir, masaüstü deneyimi olan sunucu.
Windows Server 2012 R2/Windows Server 2012 | Destekleniyor.
Windows Server 2008 R2 SP1 ve sonraki sürümler. | Destekleniyor.<br/><br/> Mobility hizmeti aracısının [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) sürümünden, SP1 veya sonraki sürümleriyle Windows 2008 R2 çalıştıran makinelerde [hizmet yığını güncelleştirmesi (SSU)](https://support.microsoft.com/help/4490628) ve [SHA-2 güncelleştirmesi](https://support.microsoft.com/help/4474419) gerekir. SHA-1 Eylül 2019 ' den desteklenmez ve SHA-2 kod imzalama etkinleştirilmemişse, aracı uzantısı beklendiği gibi yüklenmez/yükseltilmez. [SHA-2 yükseltme ve gereksinimleri](https://support.microsoft.com/topic/sha-2-code-signing-support-update-for-windows-server-2008-r2-windows-7-and-windows-server-2008-september-23-2019-84a8aad5-d8d9-2d5c-6d78-34f9aa5f8339)hakkında daha fazla bilgi edinin.
Windows Server 2008 SP2 veya üzeri (64 bit/32 bit) |  Yalnızca geçiş için desteklenir. [Daha fazla bilgi edinin](migrate-tutorial-windows-server-2008.md).<br/><br/> Mobility hizmeti aracısının [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) . sürümünden WINDOWS 2008 SP2 makinelerinde [hizmet yığını güncelleştirmesi (SSU)](https://support.microsoft.com/help/4493730) ve [SHA-2 güncelleştirmesi](https://support.microsoft.com/help/4474419) yüklü olmalıdır. SHA-1 Eylül 2019 ' den desteklenmez ve SHA-2 kod imzalama etkinleştirilmemişse, aracı uzantısı beklendiği gibi yüklenmez/yükseltilmez. [SHA-2 yükseltme ve gereksinimleri](https://support.microsoft.com/topic/sha-2-code-signing-support-update-for-windows-server-2008-r2-windows-7-and-windows-server-2008-september-23-2019-84a8aad5-d8d9-2d5c-6d78-34f9aa5f8339)hakkında daha fazla bilgi edinin.
10, Windows 8.1, Windows 8 | Yalnızca 64 bit sistem desteklenir. 32 bit sistem desteklenmez.
Windows 7 SP1 64-bit | [Güncelleştirme paketi 36](https://support.microsoft.com/help/4503156) (Mobility hizmetinin sürüm 9,22) ve sonraki sürümlerde desteklenir. </br></br> Mobility hizmeti aracısının [9,30](https://support.microsoft.com/en-us/help/4531426/update-rollup-42-for-azure-site-recovery) ' den Itibaren, WINDOWS 7 SP1 makinelerinde yüklü [hizmet yığını güncelleştirmesi (SSU)](https://support.microsoft.com/help/4490628) ve [SHA-2 güncelleştirmesi](https://support.microsoft.com/help/4474419) gerekir.  SHA-1 Eylül 2019 ' den desteklenmez ve SHA-2 kod imzalama etkinleştirilmemişse, aracı uzantısı beklendiği gibi yüklenmez/yükseltilmez. [SHA-2 yükseltme ve gereksinimleri](https://support.microsoft.com/topic/sha-2-code-signing-support-update-for-windows-server-2008-r2-windows-7-and-windows-server-2008-september-23-2019-84a8aad5-d8d9-2d5c-6d78-34f9aa5f8339)hakkında daha fazla bilgi edinin.

### <a name="for-linux"></a>Linux için

**İşletim sistemi** | **Ayrıntılar**
--- | ---
Linux | Yalnızca 64 bit sistem desteklenir. 32 bit sistem desteklenmez.<br/><br/>Her Linux sunucusunda [Linux Integration Services (LIS) bileşenleri](https://www.microsoft.com/download/details.aspx?id=55106) yüklü olmalıdır. Yük devretme/yük devretme testi sonrasında Azure 'da sunucuyu önyüklemek gerekir. Yerleşik LIS bileşenleri eksikse, makinelerin Azure 'da önyüklenebilmesi için çoğaltmayı etkinleştirmeden önce [bileşenleri](https://www.microsoft.com/download/details.aspx?id=55106) yüklediğinizden emin olun. <br/><br/> Azure 'da Linux sunucularını çalıştırmak için yük devretmeyi Site Recovery. Ancak, Linux satıcıları, desteği yalnızca yaşam sonuna ulaşmamış olan dağıtım sürümleriyle sınırlayabilir.<br/><br/> Linux dağıtımları üzerinde yalnızca dağıtım alt sürüm sürümü/güncelleştirmesi 'nin bir parçası olan hisse senedi çekirdekleri desteklenir.<br/><br/> Korunan makinelerin birincil Linux dağıtım sürümleri arasında yükseltilmesi desteklenmez. Yükseltmek için çoğaltmayı devre dışı bırakın, işletim sistemini yükseltin ve sonra çoğaltmayı yeniden etkinleştirin.<br/><br/> Azure 'da Linux ve açık kaynaklı teknoloji desteği hakkında [daha fazla bilgi edinin](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) .
Linux Red Hat Enterprise | 5,2 5,11</b><br/> 6,1 6,10</b> </br> 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7,8](https://support.microsoft.com/help/4564347/), [7,9 Beta sürümü](https://support.microsoft.com/help/4578241/), [7,9](https://support.microsoft.com/help/4590304/) </br> [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1, [8,2](https://support.microsoft.com/help/4570609), [8,3](https://support.microsoft.com/help/4597409/) <br/> Red Hat Enterprise Linux 5.2-5.11 & 6.1-6.Server çalıştıran sunucularda daha eski çekirdekler, [Linux Integration Services (LIS) bileşenleri](https://www.microsoft.com/download/details.aspx?id=55106) önceden yüklenmiş değildir. Yerleşik LIS bileşenleri eksikse, makinelerin Azure 'da önyüklenebilmesi için çoğaltmayı etkinleştirmeden önce [bileşenleri](https://www.microsoft.com/download/details.aspx?id=55106) yüklediğinizden emin olun.
Linux: CentOS | 5,2 5,11</b><br/> 6,1 6,10</b><br/> </br> 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery), [7,8](https://support.microsoft.com/help/4564347/), [7,9](https://support.microsoft.com/help/4578241/) </br> [8,0](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), 8,1, [8,2](https://support.microsoft.com/help/4570609), [8,3](https://support.microsoft.com/help/4597409/) <br/><br/> CentOS 5.2-5.11 & 6.1-6.Server çalıştıran sunucularda daha eski çekirdekler,  [Linux Integration Services (LIS) bileşenleri](https://www.microsoft.com/download/details.aspx?id=55106) önceden yüklenmiş değildir. Yerleşik LIS bileşenleri eksikse, makinelerin Azure 'da önyüklenebilmesi için çoğaltmayı etkinleştirmeden önce [bileşenleri](https://www.microsoft.com/download/details.aspx?id=55106) yüklediğinizden emin olun.
Ubuntu | Ubuntu 14,04 * LTS sunucusu [(desteklenen çekirdek sürümlerini gözden geçirin)](#ubuntu-kernel-versions)<br/>Ubuntu 16,04 * LTS sunucusu [(desteklenen çekirdek sürümlerini gözden geçirin)](#ubuntu-kernel-versions) </br> Ubuntu 18,04 * LTS sunucusu [(desteklenen çekirdek sürümlerini gözden geçirin)](#ubuntu-kernel-versions) </br> Ubuntu 20,04 * LTS sunucusu [(desteklenen çekirdek sürümlerini gözden geçirin)](#ubuntu-kernel-versions) </br> (*tüm 14,04 için destek içerir.* x *, 16,04.* x *, 18,04.* x *, 20,04.* x * sürüm)
Debian | 7. ve 8. (tüm 7 için destek içerir). *x*, 8. *x* sürümleri); Debir 9 (9,13 9,1 için destek içerir. Denu 9,0 desteklenmez.), DEA 10 [(desteklenen çekirdek sürümlerini gözden geçirin)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4, [SP5](https://support.microsoft.com/help/4570609) [(desteklenen çekirdek sürümlerini gözden geçirin)](#suse-linux-enterprise-server-12-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 15, 15 SP1 [(desteklenen çekirdek sürümlerini gözden geçirin)](#suse-linux-enterprise-server-15-supported-kernel-versions) <br/> SUSE Linux Enterprise Server 11 SP3. [Yapılandırma sunucusunda en son Mobility Aracısı yükleyicisini Indirdiğinizden emin olun](vmware-physical-mobility-service-overview.md#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server). </br> SUSE Linux Enterprise Server 11 SP4 </br> **Not**: SUSE LINUX ENTERPRISE Server 11 SP3 'den SP4 'e çoğaltılan makinelerin yükseltilmesi desteklenmez. Yükseltmek için çoğaltmayı devre dışı bırakıp yükseltmeden sonra yeniden etkinleştirin. <br/>|
Oracle Linux | 6,4, 6,5, 6,6, 6,7, 6,8, 6,9, 6,10, 7,0, 7,1, 7,2, 7,3, 7,4, 7,5, 7,6, [7,7](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery), [7,8](https://support.microsoft.com/help/4573888/), [7,9](https://support.microsoft.com/help/4597409/), [8,0](https://support.microsoft.com/help/4573888/), [8,1](https://support.microsoft.com/help/4573888/), [8,3](https://support.microsoft.com/help/4597409/)  <br/> Red Hat uyumlu çekirdek veya ayırıcı kurumsal çekirdek sürümü 3, 4 & 5 (UEK3, UEK4, UEK5) çalıştırılıyor<br/><br/>8.1<br/>Tüm uı3.10.0 çekirdekler ve RedHat çekirdekleri üzerinde çalışma <=-1062. * [9,36](https://support.microsoft.com/help/4578241/) [9,35](https://support.microsoft.com/help/4573888/) desteklenir

> [!Note]
>- Windows sürümlerinin her biri için Azure Site Recovery yalnızca [uzun süreli bakım kanalı (LTSC)](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) derlemelerini destekler.  [Yarı yıllık kanal](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) yayınları Şu anda şu anda desteklenmiyor.
>- Lütfen Linux sürümleri için Azure Site Recovery özelleştirilmiş işletim sistemi görüntülerini desteklemediğinden emin olun. Yalnızca dağıtım alt sürüm sürümünün/güncelleştirmesinin parçası olan hisse senedi çekirdekleri desteklenir.

### <a name="ubuntu-kernel-versions"></a>Ubuntu çekirdek sürümleri

**Desteklenen yayın** | **Mobility hizmeti sürümü** | **Çekirdek sürümü** |
--- | --- | --- |
14,04 LTS | [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a), [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 3.13.0-24-genel-3.13.0-170-Generic,<br/>3.16.0-25-Genel-3.16.0-77-Generic,<br/>3.19.0-18-Generic ila 3.19.0-80-Generic,<br/>4.2.0-18-Generic ila 4.2.0-42-Generic,<br/>4.4.0-21-Generic-4.4.0-148-Generic,<br/>4.15.0-1023-Azure to 4.15.0-1045-Azure |
|||
16,04 LTS | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.4.0-21-Generic-4.4.0-201-Generic,<br/>4.8.0-34-Generic ile 4.8.0-58-Generic,<br/>4.10.0-14-generic-4.10.0-42-Generic,<br/>4.11.0-13-Genel-4.11.0-14-generic,<br/>4.13.0-16-Generic to 4.13.0-45-Generic,<br/>4.15.0-13-Genel-4.15.0-133-Generic<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1106-Azure|
16,04 LTS | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.4.0-21-Generic-4.4.0-197-Generic,<br/>4.8.0-34-Generic ile 4.8.0-58-Generic,<br/>4.10.0-14-generic-4.10.0-42-Generic,<br/>4.11.0-13-Genel-4.11.0-14-generic,<br/>4.13.0-16-Generic to 4.13.0-45-Generic,<br/>4.15.0-13-Generic ila 4.15.0-128-Generic<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1102-Azure |
16,04 LTS | [9,39](https://support.microsoft.com/help/4597409/) | 4.4.0-21-Generic-4.4.0-194-Generic,<br/>4.8.0-34-Generic ile 4.8.0-58-Generic,<br/>4.10.0-14-generic-4.10.0-42-Generic,<br/>4.11.0-13-Genel-4.11.0-14-generic,<br/>4.13.0-16-Generic to 4.13.0-45-Generic,<br/>4.15.0-13-Genel-4.15.0-123-Generic<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1098-Azure|
16,04 LTS | [9.38](https://support.microsoft.com/help/4590304/) | 4.4.0-21-Generic-4.4.0-190-Generic,<br/>4.8.0-34-Generic ile 4.8.0-58-Generic,<br/>4.10.0-14-generic-4.10.0-42-Generic,<br/>4.11.0-13-Genel-4.11.0-14-generic,<br/>4.13.0-16-Generic to 4.13.0-45-Generic,<br/>4.15.0-13-Genel-4.15.0-118-Generic<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1096-Azure|
16,04 LTS | [9,37](https://support.microsoft.com/help/4582666/) | 4.4.0-21-Generic-4.4.0--Generic,<br/>4.8.0-34-Generic ile 4.8.0-58-Generic,<br/>4.10.0-14-generic-4.10.0-42-Generic,<br/>4.11.0-13-Genel-4.11.0-14-generic,<br/>4.13.0-16-Generic to 4.13.0-45-Generic,<br/>4.15.0-13-Genel-4.15.0-115-Generic<br/>4.11.0-1009-Azure-4.11.0-1016-Azure,<br/>4.13.0-1005-Azure-4.13.0-1018-Azure <br/>4.15.0-1012-Azure-4.15.0-1093-Azure |
|||
18,04 LTS | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.15.0-20-Genel-4.15.0-135-Generic </br> 4.18.0-13-Genel-4.18.0-25-genel </br> 5.0.0-15-genel-5.0.0-65-Generic </br> 5.3.0-19-Generic-5.3.0-70-Generic </br> 5.4.0-37-genel-5.4.0-59-Generic</br> 5.4.0-60-Generic ila 5.4.0-65-Generic </br> 4.15.0-1009-Azure-4.15.0-1106-Azure </br> 4.18.0-1006-Azure to 4.18.0-1025-Azure </br> 5.0.0-1012-Azure-5.0.0-1036-Azure </br> 5.3.0-1007-Azure-5.3.0-1035-Azure </br> 5.4.0-1020-Azure-5.4.0-1039-Azure|
18,04 LTS | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.15.0-20-Genel-4.15.0-129-Generic </br> 4.18.0-13-Genel-4.18.0-25-genel </br> 5.0.0-15-genel-5.0.0-63-Generic </br> 5.3.0-19-genel-5.3.0-69-Generic </br> 5.4.0-37-genel-5.4.0-59-Generic</br> 4.15.0-1009-Azure-4.15.0-1103-Azure </br> 4.18.0-1006-Azure to 4.18.0-1025-Azure </br> 5.0.0-1012-Azure-5.0.0-1036-Azure </br> 5.3.0-1007-Azure-5.3.0-1035-Azure </br> 5.4.0-1020-Azure-5.4.0-1035-Azure|
18,04 LTS | [9,39](https://support.microsoft.com/help/4597409/) | 4.15.0-20-Generic ila 4.15.0-123-Generic </br> 4.18.0-13-Genel-4.18.0-25-genel </br> 5.0.0-15-genel-5.0.0-63-Generic </br> 5.3.0-19-genel-5.3.0-69-Generic </br> 5.4.0-37-Generic-5.4.0-53-Generic</br> 4.15.0-1009-Azure-4.15.0-1099-Azure </br> 4.18.0-1006-Azure to 4.18.0-1025-Azure </br> 5.0.0-1012-Azure-5.0.0-1036-Azure </br> 5.3.0-1007-Azure-5.3.0-1035-Azure </br> 5.4.0-1020-Azure-5.4.0-1031-Azure|
18,04 LTS | [9.38](https://support.microsoft.com/help/4590304/) | 4.15.0-20-Genel-4.15.0-118-Generic </br> 4.18.0-13-Genel-4.18.0-25-genel </br> 5.0.0-15-genel-5.0.0-61-Generic </br> 5.3.0-19-Generic-5.3.0-67-Generic </br> 5.4.0-37-Generic to 5.4.0-48-Generic</br> 4.15.0-1009-Azure-4.15.0-1096-Azure </br> 4.18.0-1006-Azure to 4.18.0-1025-Azure </br> 5.0.0-1012-Azure-5.0.0-1036-Azure </br> 5.3.0-1007-Azure-5.3.0-1035-Azure </br> 5.4.0-1020-Azure-5.4.0-1026-Azure|
18,04 LTS | [9,37](https://support.microsoft.com/help/4582666/) | 4.15.0-20-Generic ila 4.15.0-115-Generic </br> 4.18.0-13-Genel-4.18.0-25-genel </br> 5.0.0-15-genel-5.0.0-60-Generic </br> 5.3.0-19-Generic-5.3.0-66-Generic </br> 5.4.0-37-Generic to 5.4.0-45-Generic</br> 4.15.0-1009-Azure-4.15.0-1093-Azure </br> 4.18.0-1006-Azure to 4.18.0-1025-Azure </br> 5.0.0-1012-Azure-5.0.0-1036-Azure </br> 5.3.0-1007-Azure-5.3.0-1035-Azure </br> 5.4.0-1020-Azure ile 5.4.0-1023-Azure|
|||
20,04 LTS |[9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)| 5.4.0-26-Generic-5.4.0-65 </br> -Generic 5.4.0-1010-Azure-5.4.0-1039-Azure </br> 5.8.0-29-Generic to 5.8.0-43-Generic </br>
20,04 LTS |[9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)| 5.4.0-26-Generic-5.4.0-59 </br> -Generic 5.4.0-1010-Azure-5.4.0-1035-Azure </br> 5.8.0-29-genel-5.8.0-34-Generic|
20,04 LTS |[9,39](https://support.microsoft.com/help/4597409/) | 5.4.0-26-Generic-5.4.0-53 </br> -Generic 5.4.0-1010-Azure-5.4.0-1031-Azure
20,04 LTS |[9.38](https://support.microsoft.com/help/4590304/) | 5.4.0-26-Generic to 5.4.0-48 </br> -Generic 5.4.0-1010-Azure-5.4.0-1026-Azure
20,04 LTS |[9,37](https://support.microsoft.com/help/4582666/) | 5.4.0-26-Generic-5.4.0-45 </br> -Generic 5.4.0-1010-Azure ile 5.4.0-1023-Azure

### <a name="debian-kernel-versions"></a>Çekirdek sürümlerini kaldırma


**Desteklenen yayın** | **Mobility hizmeti sürümü** | **Çekirdek sürümü** |
--- | --- | --- |
Deyi 7 | [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a), [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 3.2.0-4-AMD64-3.2.0-6-AMD64, 3.16.0 -0. BPO. 4-AMD64 |
|||
Debian 8 | [9,37](https://support.microsoft.com/help/4582666/), [9,38](https://support.microsoft.com/help/4590304/), [9,39](https://support.microsoft.com/help/4597409/), [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a), [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 3.16.0-4-AMD64 ila 3.16.0-11-AMD64, 4.9.0 -0. BPO. 4-AMD64 ila 4.9.0 -0. BPO. 11-AMD64 |
|||
De, 9,1 | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.9.0-1-AMD64 to 4.9.0-14-AMD64 </br> 4.19.0 -0. BPO. 1-AMD64 to 4.19.0 -0. BPO. 14-AMD64 </br> 4.19.0 -0. BPO. 1-Cloud-AMD64 to 4.19.0 -0. BPO. 14-Cloud-AMD64
De, 9,1 | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.9.0-1-AMD64 to 4.9.0-14-AMD64 </br> 4.19.0 -0. BPO. 1-AMD64 to 4.19.0 -0. BPO. 13-AMD64 </br> 4.19.0 -0. BPO. 1-Cloud-AMD64 to 4.19.0 -0. BPO. 13-Cloud-AMD64 </br>
De, 9,1 | [9,39](https://support.microsoft.com/help/4597409/) | 4.9.0-1-AMD64 to 4.9.0-14-AMD64 </br> 4.19.0 -0. BPO. 1-AMD64 to 4.19.0 -0. BPO. 12-AMD64 </br> 4.19.0 -0. BPO. 1-Cloud-AMD64 to 4.19.0 -0. BPO. 12-Cloud-AMD64 </br> 
De, 9,1 | [9.38](https://support.microsoft.com/help/4590304/) | 4.9.0-1-AMD64-4.9.0-13-AMD64 </br> 4.19.0 -0. BPO. 1-AMD64 ila 4.19.0 -0. BPO. 11-AMD64 </br> 4.19.0 -0. BPO. 1-Cloud-AMD64 ila 4.19.0 -0. BPO. 11-Cloud-AMD64 </br> 
De, 9,1 | [9,37](https://support.microsoft.com/help/4582666/) | 4.9.0-3-AMD64-4.9.0-13-AMD64, 4.19.0 -0. BPO. 6-AMD64 ila 4.19.0 -0. BPO. 10-AMD64, 4.19.0 -0. BPO. 6-Cloud-AMD64 ila 4.19.0 -0. BPO. 10-Cloud-AMD64
|||
Debian 10 | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | 4.19.0-5-AMD64-4.19.0-14-AMD64 </br> 4.19.0-6-Cloud-AMD64, 4.19.0-14-Cloud-AMD64 </br> 5.8.0 -0. BPO. 2-AMD64 </br> 5.8.0 -0. BPO. 2-Cloud-AMD64
Debian 10 | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | 4.19.0-5-AMD64-4.19.0-13-AMD64 </br> 4.19.0-6-Cloud-AMD64, 4.19.0-13-Cloud-AMD64 </br> 5.8.0 -0. BPO. 2-AMD64 </br> 5.8.0 -0. BPO. 2-Cloud-AMD64

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>SUSE Linux Enterprise Server 12 desteklenen çekirdek sürümü

**Sürüm** | **Mobility hizmeti sürümü** | **Çekirdek sürümü** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533) | Tüm [hisse senedi SUSE 12 SP1, SP2, SP3, SP4, SP5 çekirdekler](https://www.suse.com/support/kb/doc/?id=000019587) desteklenir.</br></br> 4.4.138-4.7-Azure to 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure to 4.12.14-6.43-Azure </br> 4.12.14-16,7-Azure to 4.12.14-16.44-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a) | Tüm [hisse senedi SUSE 12 SP1, SP2, SP3, SP4, SP5 çekirdekler](https://www.suse.com/support/kb/doc/?id=000019587) desteklenir.</br></br> 4.4.138-4.7-Azure to 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure to 4.12.14-6.43-Azure </br> 4.12.14-16,7-Azure to 4.12.14-16.38-Azure|
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,39](https://support.microsoft.com/help/4597409/) | Tüm [hisse senedi SUSE 12 SP1, SP2, SP3, SP4, SP5 çekirdekler](https://www.suse.com/support/kb/doc/?id=000019587) desteklenir.</br></br> 4.4.138-4.7-Azure to 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure to 4.12.14-6.43-Azure </br> 4.12.14-16,7-Azure to 4.12.14-16.34-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9.38](https://support.microsoft.com/help/4590304/) | Tüm [hisse senedi SUSE 12 SP1, SP2, SP3, SP4, SP5 çekirdekler](https://www.suse.com/support/kb/doc/?id=000019587) desteklenir.</br></br> 4.4.138-4.7-Azure to 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure to 4.12.14-6.43-Azure </br> 4.12.14-16,7-Azure to 4.12.14-16.28-Azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4, SP5) | [9,37](https://support.microsoft.com/help/4582666/),  | Tüm [hisse senedi SUSE 12 SP1, SP2, SP3, SP4, SP5 çekirdekler](https://www.suse.com/support/kb/doc/?id=000019587) desteklenir.</br></br> 4.4.138-4.7-Azure to 4.4.180-4.31-Azure,</br>4.12.14-6.3-Azure to 4.12.14-6.43-Azure </br> 4.12.14-16,7-Azure to 4.12.14-16.22-Azure  |

### <a name="suse-linux-enterprise-server-15-supported-kernel-versions"></a>SUSE Linux Enterprise Server 15 desteklenen çekirdek sürümü

**Sürüm** | **Mobility hizmeti sürümü** | **Çekirdek sürümü** |
--- | --- | --- |
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,41](https://support.microsoft.com/en-us/topic/update-rollup-54-for-azure-site-recovery-50873c7c-272c-4a7a-b9bb-8cd59c230533)  | Varsayılan olarak, tüm [hisse senedi SUSE 15, SP1, SP2 çekirdekler](https://www.suse.com/support/kb/doc/?id=000019587) desteklenir.</br></br> 4.12.14-5,5-Azure to 4.12.14-5.47-Azure </br></br> 4.12.14-8.5-Azure to 4.12.14-8.55-Azure </br> 5.3.18-16-Azure </br> 5.3.18-18.5-Azure-5.3.18-18.35-Azure
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,40](https://support.microsoft.com/en-us/topic/update-rollup-53-for-azure-site-recovery-060268ef-5835-bb49-7cbc-e8c1e6c6e12a)  | Varsayılan olarak, tüm [hisse senedi SUSE 15, SP1, SP2 çekirdekler](https://www.suse.com/support/kb/doc/?id=000019587) desteklenir.</br></br> 4.12.14-5,5-Azure to 4.12.14-5.47-Azure </br></br> 4.12.14-8.5-Azure to 4.12.14-8.55-Azure </br> 5.3.18-16-Azure </br> 5.3.18-18.5-Azure-5.3.18-18.29-Azure
SUSE Linux Enterprise Server 15, SP1, SP2 | [9,39](https://support.microsoft.com/help/4597409/)  | Varsayılan olarak, tüm [hisse senedi SUSE 15, SP1, SP2 çekirdekler](https://www.suse.com/support/kb/doc/?id=000019587) desteklenir.</br></br> 4.12.14-5,5-Azure to 4.12.14-5.47-Azure </br></br> 4.12.14-8.5-Azure to 4.12.14-8.47-Azure </br> 5.3.18-16-Azure </br> 5.3.18-18.5-Azure-5.3.18-18.21-Azure
SUSE Linux Enterprise Server 15, SP1, SP2 | [9.38](https://support.microsoft.com/help/4590304/)  | Varsayılan olarak, tüm [hisse senedi SUSE 15, SP1, SP2 çekirdekler](https://www.suse.com/support/kb/doc/?id=000019587) desteklenir.</br></br> 4.12.14-5,5-Azure to 4.12.14-5.47-Azure </br></br> 4.12.14-8.5-Azure to 4.12.14-8.44-Azure </br> 5.3.18-16-Azure </br> 5.3.18-18.5-Azure-5.3.18-18.18-Azure
SUSE Linux Enterprise Server 15 ve 15 SP1 | [9,37](https://support.microsoft.com/help/4582666/)  | Varsayılan olarak, tüm [hisse senedi SUSE 15, SP1, SP2 çekirdekler](https://www.suse.com/support/kb/doc/?id=000019587) desteklenir.</br></br> 4.12.14-5,5-Azure to 4.12.14-5.47-Azure </br></br> 4.12.14-8.5-Azure to 4.12.14-8.38-Azure

## <a name="linux-file-systemsguest-storage"></a>Linux dosya sistemleri/Konuk depolama

**Bileşen** | **Desteklenir**
--- | ---
Dosya sistemleri | ext3, ext4, XFS, BTRFS (bu tabloya göre geçerli olan koşullar)
Mantıksal birim yönetimi (LVM) sağlama| Kalın sağlama-Evet <br></br> Ölçülü kaynak sağlama-Hayır
Birim Yöneticisi | -LVM destekleniyor.<br/> -LVM üzerinde-/Boot [güncelleştirme paketi 31](https://support.microsoft.com/help/4478871/) ' den (Mobility hizmeti sürüm 9,20) ve sonraki sürümlerde desteklenir. Önceki Mobility hizmeti sürümlerinde desteklenmez.<br/> -Birden çok işletim sistemi diski desteklenmez.
Parasanallaştırılmış depolama cihazları | Parasanallaştırılmış sürücüler tarafından dışarı aktarılan cihazlar desteklenmez.
Çok kuyruğundaki blok GÇ cihazları | Desteklenmez.
HP CCıS depolama denetleyicisi ile fiziksel sunucular | Desteklenmez.
Cihaz/bağlama noktası adlandırma kuralı | Cihaz adı veya bağlama noktası adı benzersiz olmalıdır.<br/> İki cihazda/bağlama noktasında büyük/küçük harf duyarlı adlara sahip olmadığından emin olun. Örneğin, *Device1* ve *DEVICE1* ile aynı VM 'nin cihazlarını adlandırma desteklenmez.
Dizinler | Mobility hizmetinin sürüm 9,20 ' den önceki bir sürümünü çalıştırıyorsanız ( [güncelleştirme paketi 31](https://support.microsoft.com/help/4478871/)' de yayımlanmıştır), bu kısıtlamalar uygulanır:<br/><br/> -Bu dizinler (ayrı bölümler/dosya sistemleri olarak ayarlandıysa) kaynak sunucudaki aynı işletim sistemi diskinde olmalıdır:/(kök),/Boot,/usr,/usr/local,/var,/etc.</br> -/Boot dizini bir disk bölümünde olmalı ve bir LVM birimi olmamalıdır.<br/><br/> Sürüm 9,20 ' den itibaren bu kısıtlamalar uygulanmaz. 
Önyükleme dizini | -Önyükleme diskleri, GPT bölüm biçiminde olmamalıdır. Bu bir Azure mimarisi kısıtlamasıdır. GPT diskleri, veri diskleri olarak desteklenir.<br/><br/> Bir VM 'de birden çok önyükleme diski desteklenmez<br/><br/> -bir LVM biriminde birden fazla disk arasında-/Boot desteklenmez.<br/> -Önyükleme diski olmayan bir makine çoğaltılamıyor.
Boş alan gereksinimleri| /root bölümünde 2 GB <br/><br/> yükleme klasöründe 250 MB
XFSv5 | Meta veri sağlama toplamı gibi XFS dosya sistemlerindeki XFSv5 özellikleri desteklenir (Mobility hizmeti sürüm 9,10 ve üzeri).<br/> Bölüm için XFS süper bloğunu denetlemek üzere xfs_info yardımcı programını kullanın. `ftype`1 olarak ayarlanırsa, XFSv5 özellikleri kullanımda olur.
BTRFS | BTRFS, [güncelleştirme paketi 34](https://support.microsoft.com/help/4490016) (Mobility hizmetinin sürüm 9,22) ve sonraki sürümlerde desteklenir. Şu durumlarda BTRFS desteklenmez:<br/><br/> -BTRFS dosya sistemi alt birimi, koruma etkinleştirildikten sonra değiştirilir.</br> -BTRFS dosya sistemi birden çok diske yayılır.</br> -BTRFS dosya sistemi RAID 'i destekler.

## <a name="vmdisk-management"></a>VM/disk yönetimi

**Eylem** | **Ayrıntılar**
--- | ---
Çoğaltılan VM 'de diski yeniden boyutlandır | Yük devretmeden önce kaynak VM 'de, doğrudan VM özelliklerinde desteklenir. Çoğaltmayı devre dışı bırakıp yeniden etkinleştirmeye gerek yoktur.<br/><br/> Yük devretmeden sonra kaynak VM 'yi değiştirirseniz, değişiklikler yakalanmazlar.<br/><br/> Yük devretmeden sonra Azure VM 'de disk boyutunu değiştirirseniz, geri döndüğünüzde Site Recovery güncelleştirmeler ile yeni bir VM oluşturur.
Çoğaltılan VM 'ye disk ekleme | Desteklenmez.<br/> VM için çoğaltmayı devre dışı bırakın, diski ekleyin ve ardından çoğaltmayı yeniden etkinleştirin.

> [!NOTE]
> Disk kimliğinde yapılan herhangi bir değişiklik desteklenmez. Örneğin, disk bölümleme GPT 'den MBR 'ye değiştirildiyse veya tam tersi durumda, bu işlem disk kimliğini değiştirir. Böyle bir senaryoda çoğaltma kesilir ve yeni bir kurulum gerekecektir. Linux makinelerinde, disk kimliği üzerinde bir etkisi olduğundan cihaz adı değişikliği desteklenmez.

## <a name="network"></a>Ağ

**Bileşen** | **Desteklenir**
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
Site Recovery hizmetine özel bağlantı erişimi | Evet. [Daha fazla bilgi edinin](hybrid-how-to-enable-replication-private-endpoints.md).


## <a name="azure-vm-network-after-failover"></a>Azure VM ağı (yük devretmeden sonra)

**Bileşen** | **Desteklenir**
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
Hızlandırılmış ağ iletişimi | No

## <a name="storage"></a>Depolama
**Bileşen** | **Desteklenir**
--- | ---
Dinamik disk | İşletim sistemi diski, temel bir disk olmalıdır. <br/><br/>Veri diskleri dinamik diskler olabilir
Docker disk yapılandırması | No
Konak NFS | VMware için Evet<br/><br/> Fiziksel sunucular için Hayır
Ana bilgisayar SAN (Iscsı/FC) | Yes
Ana bilgisayar vSAN | VMware için Evet<br/><br/> Fiziksel sunucular için yok
Ana bilgisayar çok yollu (MPIO) | Evet, Microsoft DSM ile test edildi, EMC PowerPath 5,7 SP4, Clariiyon için EMC PowerPath DSM
Konak sanal birimleri (Vvir) | VMware için Evet<br/><br/> Fiziksel sunucular için yok
Konuk/sunucu VMDK | Yes
Konuk/sunucu paylaşılan küme diski | No
Konuk/sunucu tarafından şifrelenen disk | No
Konuk/sunucu NFS | No
Konuk/sunucu Iscsı | Geçiş için-Evet<br/>Olağanüstü durum kurtarma için-Hayır, Iscsı sanal makineye bağlı bir disk olarak yeniden çalışır
Konuk/sunucu SMB 3,0 | No
Konuk/sunucu RDM | Yes<br/><br/> Fiziksel sunucular için yok
Konuk/sunucu diski > 1 TB | Evet, disk 1024 MB 'tan büyük olmalıdır<br/><br/>Yönetilen disklere çoğaltma yaparken 8.192 GB 'a kadar (9,26 sürüm ve sonraki sürümler)<br></br> Depolama hesaplarına çoğaltma yaparken 4.095 GB 'a kadar
4K mantıksal ve 4k fiziksel sektör boyutuna sahip konuk/sunucu diski | No
4K mantıksal ve 512 bayt fiziksel kesim boyutuna sahip konuk/sunucu diski | No
Şeritli disk >4 TB olan konuk/sunucu birimi | Yes
Mantıksal birim yönetimi (LVM)| Kalın sağlama-Evet <br></br> Ölçülü kaynak sağlama-Hayır
Konuk/sunucu-depolama alanları | No
Konuk/sunucu-NVMe arabirimi | No
Konuk/sunucu Hot Add/Remove disk | No
Konuk/sunucu-diski hariç tut | Yes
Konuk/sunucu çok yollu (MPIO) | No
Konuk/sunucu GPT bölümleri | Beş bölüm [güncelleştirme paketi 37](https://support.microsoft.com/help/4508614/) (Mobility hizmetinin sürüm 9,25) ve sonraki sürümlerde desteklenir. Daha önce dört destekleniyordu.
ReFS | Dayanıklı dosya sistemi, Mobility hizmeti sürüm 9,23 veya üzeri sürümlerde desteklenir
Konuk/sunucu EFı/UEFı önyüklemesi | -Site Recovery Mobility Agent sürüm 9,30 ve sonraki sürümleri ile tüm [Azure MARKETI UEFI işletim sistemleri](../virtual-machines/generation-2.md#generation-2-vm-images-in-azure-marketplace) için desteklenir. <br/> -Secure UEFı önyükleme türü desteklenmez. [Daha fazla bilgi edinin.](../virtual-machines/generation-2.md#on-premises-vs-azure-generation-2-vms)
RAID diski| No

## <a name="replication-channels"></a>Çoğaltma kanalları

|**Çoğaltma türü**   |**Desteklenir**  |
|---------|---------|
|Boşaltılan veri aktarımları (ODX)    |       No  |
|Çevrimdışı dengeli dağıtım        |   No      |
| Azure Data Box | No

## <a name="azure-storage"></a>Azure depolama

**Bileşen** | **Desteklenir**
--- | ---
Yerel olarak yedekli depolama | Yes
Coğrafi olarak yedekli depolama | Yes
Okuma erişimli coğrafi olarak yedekli depolama | Yes
Seyrek Erişimli Depolama | No
Sık erişimli depolama| No
Blok blobları | No
Bekleyen şifreleme (SSE)| Yes
Bekleyen şifreleme (CMK)| Evet (PowerShell aracılığıyla az 3.3.0 Module onödüller)
Rest 'te Çift şifreleme | Evet (PowerShell aracılığıyla az 3.3.0 Module onödüller). [Windows](../virtual-machines/disk-encryption.md) ve [Linux](../virtual-machines/disk-encryption.md)için desteklenen bölgeler hakkında daha fazla bilgi edinin.
Premium depolama | Yes
Güvenli aktarım seçeneği | Yes
İçeri/dışarı aktarma hizmeti | No
Sanal ağlar için Azure Storage güvenlik duvarları | Evet.<br/> Hedef depolama/önbellek depolama hesabında yapılandırıldı (çoğaltma verilerini depolamak için kullanılır).
Genel amaçlı v2 depolama hesapları (sık erişimli ve seyrek erişimli Katmanlar) | Evet (v1 ile karşılaştırıldığında v2 için Işlem maliyetleri önemli ölçüde yüksektir)

## <a name="azure-compute"></a>Azure İşlem

**Özellik** | **Desteklenir**
--- | ---
Kullanılabilirlik kümeleri | Yes
Kullanılabilirlik alanları | No
HUB | Yes
Yönetilen diskler | Yes

## <a name="azure-vm-requirements"></a>Azure VM gereksinimleri

Azure 'a çoğaltılan şirket içi VM 'Ler, bu tabloda özetlenen Azure VM gereksinimlerini karşılamalıdır. Site Recovery, çoğaltma için bir önkoşul denetimi çalıştırdığında, bazı gereksinimler karşılanmazsa denetim başarısız olur.

**Bileşen** | **Gereksinimler** | **Ayrıntılar**
--- | --- | ---
Konuk işletim sistemi | Çoğaltılan makineler için [desteklenen işletim sistemlerini](#replicated-machines) doğrulayın. | Desteklenmiyorsa denetim başarısız olur.
Konuk işletim sistemi mimarisi | 64 bit. | Desteklenmiyorsa denetim başarısız olur.
İşletim sistemi disk boyutu | 1. nesil makineler için 2.048 GB 'a kadar. <br> 2. nesil makinelerde 4.095 GB 'a kadar. | Desteklenmiyorsa denetim başarısız olur.
İşletim sistemi disk sayısı | 1 </br> farklı disklerde önyükleme ve sistem bölümü desteklenmiyor | Desteklenmiyorsa denetim başarısız olur.
Veri diski sayısı | 64 veya daha az. | Desteklenmiyorsa denetim başarısız olur.
Veri diski boyutu | Yönetilen diske çoğaltma sırasında 32.767 GB 'a kadar (9,41 sürüm ve sonraki sürümler)<br> Depolama hesabına çoğaltılırken 4.095 GB 'a kadar </br> Minimum disk boyutu gereksinimi-en az 1024 MB| Desteklenmiyorsa denetim başarısız olur.
Ağ bağdaştırıcıları | Birden çok bağdaştırıcı desteklenir. |
Paylaşılan VHD | Desteklenmez. | Desteklenmiyorsa denetim başarısız olur.
FC diski | Desteklenmez. | Desteklenmiyorsa denetim başarısız olur.
BitLocker | Desteklenmez. | Bir makine için çoğaltmayı etkinleştirmeden önce BitLocker devre dışı bırakılmalıdır. |
VM adı | 1 ile 63 karakter arasında.<br/><br/> Harfler, sayılar ve kısa çizgilerden oluşabilir.<br/><br/> Makine adı bir harf veya sayıyla başlamalı ve bitmelidir. |  Site Recovery makine özelliklerindeki değeri güncelleştirin.

## <a name="resource-group-limits"></a>Kaynak grubu sınırları

Tek bir kaynak grubu altında korunabilecek sanal makinelerin sayısını anlamak için [abonelik limitleri ve kotaları](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits)hakkındaki makaleye bakın.

## <a name="churn-limits"></a>Karmaşıklık limitleri

Aşağıdaki tablo, Azure Site Recovery sınırlarını sağlar.
- Bu sınırlar, testlerimize dayalıdır, ancak olası tüm uygulama g/ç birleşimlerini kapsamamaktadır.
- Gerçek sonuçlar, uygulamanızın G/Ç karışımına göre değişebilir.
- En iyi sonuçlar için [dağıtım planlayıcısı aracını](site-recovery-deployment-planner.md)çalıştırmanızı ve uygulamanız için doğru performans resmini elde etmek üzere yük devretme testi kullanarak kapsamlı uygulama testleri gerçekleştirmenizi önemle öneririz.

**Çoğaltma hedefi** | **Ortalama kaynak disk G/Ç boyutu** |**Ortalama kaynak disk veri değişim sıklığı** | **Gün başına toplam kaynak disk veri değişim sıklığı**
---|---|---|---
Standart depolama | 8 KB    | 2 MB/sn | Disk başına 168 GB
Premium P10 veya P15 disk | 8 KB    | 2 MB/sn | Disk başına 168 GB
Premium P10 veya P15 disk | 16 KB | 4 MB/sn |    Disk başına 336 GB
Premium P10 veya P15 disk | 32 KB veya daha büyük | 8 MB/sn | Disk başına 672 GB
Premium P20 veya P30 veya P40 veya P50 disk | 8 KB    | 5 MB/sn | Disk başına 421 GB
Premium P20 veya P30 veya P40 veya P50 disk | 16 KB veya daha büyük |20 MB/sn | disk başına 1684 GB


**Kaynak veri değişim sıklığı** | **Maksimum sınır**
---|---
VM üzerindeki tüm disklerde en yüksek veri değişim sıklığı | 54 MB/sn
İşlem Sunucusu tarafından desteklenen günlük en fazla veri değişim sıklığı | 2 TB

- Bunlar yüzde 30 G/Ç çakışmasını varsayan ortalama sayılardır.
- Site Recovery; çakışma oranı, büyük yazma boyutları ve gerçek iş yükü G/Ç davranışına göre daha yüksek aktarım hızını işleyebilir.
- Bu numaralar yaklaşık beş dakikalık tipik bir biriktirme listesini varsayar. Diğer bir deyişle, veriler karşıya yüklendikten sonra işlenir ve beş dakika içinde kurtarma noktası oluşturulur.

## <a name="storage-account-limits"></a>Depolama hesabı sınırları

Diskler üzerinde ortalama dalgalanma arttıkça, bir depolama hesabının destekleyebileceği disk sayısı azalır. Aşağıdaki tablo, sağlanması gereken depolama hesabı sayısıyla ilgili kararlar almak için bir kılavuz olarak kullanılabilir.
 
**Depolama hesabı türü**    |    **Dalgalanma = disk başına 4 MBps**    |    **Dalgalanma = disk başına 8 MBps**
---    |    ---    |    ---
V1 depolama hesabı    |    600 disk    |    300 disk
V2 depolama hesabı    |    1500 disk    |    750 disk

Yukarıdaki limitlerin yalnızca karma DR senaryoları için geçerli olduğunu lütfen unutmayın.

## <a name="vault-tasks"></a>Kasa görevleri

**Eylem** | **Desteklenir**
--- | ---
Kasayı kaynak grupları arasında taşıma | No
Kasayı abonelik içinde ve abonelikler arasında taşıma | No
Depolama, ağ ve Azure VM 'lerini kaynak grupları arasında taşıma | No
Depolama, ağ, Azure VM 'Leri ve abonelikler arasında taşıma. | No


## <a name="obtain-latest-components"></a>En son bileşenleri edinin

**Ad** | **Açıklama** | **Ayrıntılar**
--- | --- | ---
Yapılandırma Sunucusu | Şirket içi yüklendi.<br/> Şirket içi VMware sunucuları veya fiziksel makineler ile Azure arasındaki iletişimleri koordine eder. | - Yapılandırma sunucusu [hakkında bilgi edinin](vmware-physical-azure-config-process-server-overview.md) .<br/> - En son sürüme yükseltme [hakkında bilgi edinin](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) .<br/> - Yapılandırma sunucusunu ayarlama [hakkında bilgi edinin](vmware-azure-deploy-configuration-server.md) .
İşlem sunucusu | Varsayılan olarak yapılandırma sunucusuna yüklenir.<br/> Çoğaltma verilerini alır, önbelleğe alma, sıkıştırma ve şifreleme ile iyileştirir ve Azure 'a gönderir.<br/> Dağıtımınız büyüdükçe, daha büyük hacimde çoğaltma trafiğini işlemek için ek işlem sunucuları ekleyebilirsiniz. | - İşlem sunucusu [hakkında bilgi edinin](vmware-physical-azure-config-process-server-overview.md) .<br/> - En son sürüme yükseltme [hakkında bilgi edinin](vmware-azure-manage-process-server.md#upgrade-a-process-server) .<br/> - Genişleme işlem sunucularını ayarlama [hakkında bilgi edinin](vmware-physical-large-deployment.md#set-up-a-process-server) .
Ulaşım Hizmeti | Çoğaltmak istediğiniz VMware VM veya fiziksel sunucularda yüklü.<br/> Şirket içi VMware sunucuları/fiziksel sunucular ile Azure arasında çoğaltmayı koordine eder.| - Mobility hizmeti [hakkında bilgi edinin](vmware-physical-mobility-service-overview.md) .<br/> - En son sürüme yükseltme [hakkında bilgi edinin](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) .<br/>



## <a name="next-steps"></a>Sonraki adımlar
VMware VM 'lerinin olağanüstü durum kurtarması için Azure 'u [nasıl hazırlayacağınızı öğrenin](tutorial-prepare-azure.md) .

[9.32 UR]: https://support.microsoft.com/en-in/help/4538187/update-rollup-44-for-azure-site-recovery
[9.31 UR]: https://support.microsoft.com/en-in/help/4537047/update-rollup-43-for-azure-site-recovery
[9.30 UR]: https://support.microsoft.com/en-in/help/4531426/update-rollup-42-for-azure-site-recovery
[9.29 UR]: https://support.microsoft.com/en-in/help/4528026/update-rollup-41-for-azure-site-recovery
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
