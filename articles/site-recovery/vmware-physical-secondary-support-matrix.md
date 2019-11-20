---
title: Azure Site Recovery ile ikincil bir siteye VMware/fiziksel olağanüstü durum kurtarma desteği
description: VMware VM 'Leri ve fiziksel sunucuları Azure Site Recovery olan ikincil bir siteye olağanüstü durum kurtarma desteğini özetler.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: cabd3f7693c6b6b86bf0324bdafdfe1377d1ece8
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082183"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>VMware VM 'Leri ve fiziksel sunucuları ikincil bir siteye olağanüstü durum kurtarması için destek matrisi

Bu makalede, VMware VM 'Leri veya Windows/Linux fiziksel sunucularının bir ikincil VMware sitesine olağanüstü durum kurtarması için [Azure Site Recovery](site-recovery-overview.md) hizmetini kullandığınızda desteklenmekte olan özellikler özetlenmektedir.

- VMware VM 'lerini veya fiziksel sunucuları Azure 'a çoğaltmak istiyorsanız [Bu destek matrisini](vmware-physical-azure-support-matrix.md)gözden geçirin.
- Hyper-V VM 'lerini ikincil bir siteye çoğaltmak istiyorsanız [Bu destek matrisini](hyper-v-azure-support-matrix.md)gözden geçirin.

> [!NOTE]
> Şirket içi VMware VM 'lerini ve fiziksel sunucuları çoğaltma, InMage Scout tarafından sağlanır. InMage Scout Azure Site Recovery hizmet aboneliğine dahildir.

## <a name="end-of-support-announcement"></a>Destek sonu duyurusu
Şirket içi VMware veya fiziksel veri merkezleri arasında çoğaltmaya yönelik Site Recovery senaryosu, destek sonuna ulaşıyor.

- 2018 Ağustos 'tan, senaryo kurtarma hizmetleri kasasında yapılandırılamaz ve InMage Scout yazılımı kasadan indirilemez. Mevcut dağıtımlar desteklenecek.
- - 31 2020 Aralık 'tan senaryo desteklenmez.
Mevcut iş ortakları, destek sona erene kadar senaryoya yeni müşteriler ekleyebilir.
- 2018 ve 2019 sırasında iki güncelleştirme kullanıma sunulacaktır:

    - Güncelleştirme 7: ağ yapılandırma ve uyumluluk sorunlarını düzeltir ve TLS 1,2 desteği sağlar.
    - Güncelleştirme 8: Linux işletim sistemleri RHEL/CentOS 7.3/7.4/7.5 ve SUSE 12 için destek ekler
    - Güncelleştirme 8 ' den sonra daha fazla güncelleştirme yayınlanmayacaktır. Güncelleştirme 8 ' de eklenen işletim sistemleri için sınırlı Düzeltme desteği ve en iyi çabayı temel alan hata düzeltmeleri olacaktır.

## <a name="host-servers"></a>Ana bilgisayar sunucuları

**İşletim sistemi** | **Ayrıntılar**
--- | ---
vCenter server | vCenter 5,5, 6,0 ve 6,5<br/><br/> 6,0 veya 6,5 çalıştırırsanız yalnızca 5,5 özelliklerinin desteklendiğini unutmayın.


## <a name="replicated-vm-support"></a>Çoğaltılan VM desteği

Aşağıdaki tabloda, Site Recovery ile çoğaltılan makineler için işletim sistemi desteği özetlenmektedir. Desteklenen işletim sisteminde herhangi bir iş yükü çalışıyor olabilir.

**İşletim sistemi** | **Ayrıntılar**
--- | ---
Windows Server | 64-bit Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 en az SP1.
Linux | Red Hat Enterprise Linux 6,7, 6,8, 6,9, 7,1, 7,2 <br/><br/> CentOS 6,5, 6,6, 6,7, 6,8, 6,9, 7,0, 7,1, 7,2 <br/><br/> Oracle Enterprise Linux 6,4, 6,5, 6,8 Red Hat uyumlu çekirdeği veya ayırıcı kurumsal çekirdek sürümü 3 (UEK3) çalışıyor <br/><br/> SUSE Linux Enterprise Server 11 SP3, 11 SP4 


## <a name="linux-machine-storage"></a>Linux makine depolaması

Yalnızca aşağıdaki depolama alanına sahip Linux makineleri çoğaltılabiliyor:

- Dosya sistemi (EXT3, ETX4, ReiserFS, XFS).
- Çok yollu yazılım-cihaz Eşleyici.
- Birim Yöneticisi (LVM2).
- HP CCıS denetleyici depolaması olan fiziksel sunucular desteklenmez.
- Reıfs dosya sistemi yalnızca SUSE Linux Enterprise Server 11 SP3'TE desteklenir.

## <a name="network-configuration---hostguest-vm"></a>Ağ yapılandırması-Konak/Konuk VM

**Yapılandırma** | **Destekleniyor**  
--- | --- 
Konak-NIC Grubu oluşturma | Yes 
Konak-VLAN | Yes 
Ana bilgisayar-IPv4 | Yes 
Ana bilgisayar-IPv6 | Hayır 
Konuk VM-NIC ekibi oluşturma | Hayır
Konuk VM-IPv4 | Yes
Konuk VM-IPv6 | Hayır
Konuk VM-Windows/Linux-statik IP adresi | Yes
Konuk VM-çoklu NIC | Yes


## <a name="storage"></a>Depolama

### <a name="host-storage"></a>Konak depolaması

**Depolama (ana bilgisayar)** | **Destekleniyor** 
--- | --- 
NFS | Yes 
SMB 3.0 | Yok 
SAN (ISCSı) | Yes 
Çoklu yol (MPIO) | Yes 

### <a name="guest-or-physical-server-storage"></a>Konuk veya fiziksel sunucu depolaması

**Yapılandırma** | **Destekleniyor** 
--- | --- 
VMDK | Yes 
VHD/VHDX | Yok 
Gen 2 VM | Yok 
Paylaşılan küme diski | Yes 
Şifrelenmiş disk | Hayır 
UEFI| Yes 
NFS | Hayır 
SMB 3.0 | Hayır 
RDM | Yes 
Disk > 1 TB | Yes 
Dizili disk > 1 TB olan birim<br/><br/> LVM | Yes 
Depolama alanları | Hayır 
Dinamik disk Ekle/Kaldır | Yes 
Diski hariç tutma | Yes 
Çoklu yol (MPIO) | Yok 

## <a name="vaults"></a>Kasalar

**Eylem** | **Destekleniyor** 
--- | --- 
Kasalarını kaynak grupları arasında taşıma (veya abonelikler arasında) | Hayır 
Depolama, ağ ve Azure VM 'lerini kaynak grupları arasında taşıma (abonelikler içinde veya abonelikler arasında) | Hayır 

## <a name="mobility-service-and-updates"></a>Mobility hizmeti ve güncelleştirmeleri

Mobility hizmeti, şirket içi VMware sunucuları veya fiziksel sunucular ile ikincil site arasındaki çoğaltmayı koordine eder. Çoğaltmayı ayarlarken, Mobility hizmetinin en son sürümüne ve diğer bileşenlere sahip olduğunuzdan emin olun.

| **Güncelleştirme** | **Ayrıntılar** |
| --- | --- |
|Scout güncelleştirmeleri | Scout güncelleştirmeleri birikimlidir. <br/><br/> En son Scout güncelleştirmelerini [öğrenin ve indirin](vmware-physical-secondary-disaster-recovery.md#updates) |
|Bileşen güncelleştirmeleri | Scout güncelleştirmeleri, korumak istediğiniz RX sunucusu, yapılandırma sunucusu, işlem ve ana hedef sunucuları, vContinuum sunucuları ve kaynak sunucuları dahil olmak üzere tüm bileşenlere yönelik güncelleştirmeleri içerir.<br/><br/> [Daha fazla bilgi edinin](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).|


## <a name="next-steps"></a>Sonraki adımlar

[InMage Scout Kullanıcı kılavuzunu](https://aka.ms/asr-scout-user-guide) indirin

- [VMM bulutlarındaki Hyper-V VM 'lerini ikincil bir siteye çoğaltma](tutorial-vmm-to-vmm.md)
- [VMware VM’lerini ve fiziksel sunucuları ikincil bir siteye çoğaltma](tutorial-vmware-to-vmware.md)
