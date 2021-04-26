---
title: SAP Ao Azure sanal makineleri SAP iş yükü için DBMS dağıtımı | Microsoft Docs
description: SAP iş yükü için SAP ASE Azure Sanal Makineler DBMS dağıtımı
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 430af1dfcbd9c7faabfca2f3f5b80b3b3106a889
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675797"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>SAP iş yükü için SAP ASE Azure Sanal Makineler DBMS dağıtımı

Bu belgede, Azure IaaS 'de SAP aşırı dağıtıldığında göz önünde bulundurmanız gereken birkaç farklı alanı ele alır. Bu belgeye yönelik bir önkoşul olarak, Azure [sanal makineler Için Azure sanal MAKINELERI DBMS dağıtımı, SAP iş yükü](dbms_guide_general.md) ve diğer kılavuzlar için [Azure belgelerindeki](./get-started.md)belge konularını okuduğunuzdan önce göz önünde bulundurmanız gerekir. Bu belgede, Linux ve Windows Işletim sistemlerinde çalışan SAP Ao ele alınmaktadır. Azure 'da desteklenen en düşük sürüm SAP Ade 16.0.02 (sürüm 16 destek paketi 2). En son SAP sürümünü ve en son düzeltme eki düzeyini dağıtmanız önerilir.  En düşük SAP ASE 16.0.03.07 olarak (sürüm 16 destek paketi 3 düzeltme eki düzeyi 7) önerilir.  SAP 'nin en son sürümü [hedeflenen asa 16,0 yayın zamanlaması ve CR liste bilgileri](https://wiki.scn.sap.com/wiki/display/SYBASE/Targeted+ASE+16.0+Release+Schedule+and+CR+list+Information)bölümünde bulunabilir.

SAP uygulamaları veya yükleme medyası konumuyla sürüm desteği hakkında ek bilgiler, bu konumlardaki SAP ürün kullanılabilirliği matrisinin yanı sıra bulunur:

- [SAP destek notunun #2134316](https://launchpad.support.sap.com/#/notes/2134316)
- [SAP destek notunun #1941500](https://launchpad.support.sap.com/#/notes/1941500)
- [SAP destek notunun #1590719](https://launchpad.support.sap.com/#/notes/1590719)
- [SAP destek notunun #1973241](https://launchpad.support.sap.com/#/notes/1973241)

Açıklama: SAP World içindeki ve dışındaki belgelerin tamamında, ürünün adı Sybase Ace veya SAP Ace olarak ya da her ikisinde de başvurulur. Tutarlı kalmak için bu belgelerde **SAP Ace** adını kullanacağız.

## <a name="operating-system-support"></a>İşletim sistemi desteği
SAP ürün kullanılabilirliği matrisi, her SAP uygulaması için desteklenen Işletim sistemi ve SAP çekirdek birleşimlerini içerir.  Linux dağıtımları SUSE 12. x, SUSE 15. x, Red hat 7. x tam olarak desteklenmektedir.  SAP ATıCı için işletim sistemi olarak Oracle Linux desteklenmez.  Kullanılabilir en son Linux sürümlerinin kullanılması önerilir. Windows müşterileri Windows Server 2016 veya Windows Server 2019 sürümlerini kullanmalıdır.  Windows 2012 gibi eski Windows sürümleri Teknik olarak desteklenmektedir ancak en son Windows sürümü her zaman önerilir.


## <a name="specifics-to-sap-ase-on-windows"></a>Windows üzerinde SAP Ao 'nun özellikleri
Microsoft Azure başlayarak, mevcut SAP Ao uygulamalarınızı Azure sanal makinelerine geçirebilirsiniz. Bir Azure sanal makinesinde SAP ASE, bu uygulamaları kolayca Microsoft Azure bir şekilde geçirerek kurumsal ayırt edilen uygulamaların dağıtım, yönetim ve bakımının toplam maliyetini düşürmenizi sağlar. Bir Azure sanal makinesinde SAP Ao ile yöneticiler ve geliştiriciler, şirket içinde bulunan aynı geliştirme ve yönetim araçlarını kullanmaya devam edebilir.

Microsoft Azure, en küçük SAP sistemlerini ve ıdscapes 'yi binlerce kullanıcıyla büyük SAP sistemlerine ve Langes 'ye kadar olan çok sayıda farklı sanal makine türü sunar. SAP, farklı SAP sertifikalı VM SKU 'Larının numaralarını SAP [destek not#1928533](https://launchpad.support.sap.com/#/notes/1928533)olarak sunulmaktadır.

Windows 'da SAP Ace 'yi yükleme belgeleri [Windows Için SAP Ace yükleme kılavuzunda](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/16.0.3.7/en-US/a660d3f1bc2b101487cbdbf10069c3ac.html) bulunabilir.

Bellek üzerindeki kilit sayfaları, SAP Ao veritabanı arabelleğinin sayfalanmasını engelleyecek bir ayardır.  Bu ayar, çok fazla bellek içeren büyük meşgul sistemler için yararlıdır. Daha fazla bilgi için BC-DB-SYB ile iletişim kurun. 


## <a name="linux-operating-system-specific-settings"></a>Linux işletim sistemine özgü ayarlar
Linux VM 'lerde, `saptune` PROFIL SAP ile çalıştırın-Ao Linux büyük sayfaları varsayılan olarak etkinleştirilmelidir ve komutla doğrulanabilir  

`cat /proc/meminfo` 

Sayfa boyutu genellikle 2048 KB 'tır. Ayrıntılar için bkz. [Linux 'ta çok büyük sayfalar](https://help.sap.com/viewer/ecbccd52e7024feaa12f4e780b43bc3b/16.0.3.7/en-US/a703d580bc2b10149695f7d838203fad.html) makalesi 


## <a name="recommendations-on-vm-and-disk-structure-for-sap-ase-deployments"></a>SAP ate dağıtımları için VM ve disk yapısına yönelik öneriler

SAP NetWeaver uygulamaları için SAP Ao, SAP [destek notunda](https://launchpad.support.sap.com/#/notes/1928533) listelenen herhangi bir sanal makine türü için desteklenir #1928533 orta büyüklükte SAP Ao veritabanı sunucuları için kullanılan tipik VM türleri Esv3 içerir.  Büyük multi-terabaytlık veritabanları, d serisi VM türlerinden faydalanabilir. SAP Ao işlem günlüğü diski yazma performansı, M serisi Yazma Hızlandırıcısı etkinleştirilerek artırılabilir. SAP ASE 'nin günlük yazma Işlemlerini gerçekleştirme yöntemi nedeniyle Yazma Hızlandırıcısı, SAP ASE ile dikkatle test edilmelidir.  [Sap destek notuna #2816580](../../how-to-enable-write-accelerator.md) inceleyin ve bir performans testi çalıştırmayı deneyin.  
Yazma Hızlandırıcısı yalnızca işlem günlüğü diski için tasarlanmıştır. Disk düzeyi önbelleği NONE olarak ayarlanmalıdır. Azure Yazma Hızlandırıcısı diğer DBMS ile benzer iyileştirmeler göstermezse şaşırmayın. SAP ATıCı 'in işlem günlüğüne yazdığı yönteme bağlı olarak, Azure Yazma Hızlandırıcısı tarafından hiçbir hızlandırma olmaması olabilir.
Veri cihazları ve günlük cihazları için ayrı diskler önerilir.  Sistem veritabanları sybgüvenlik ve `saptools` adanmış diskler gerektirmez ve SAP veritabanı verilerini ve günlük cihazlarını içeren disklere yerleştirilebilecek 

![SAP ATıCı için depolama yapılandırması](./media/dbms-guide-sap-ase/sap-ase-disk-structure.png)

### <a name="file-systems-stripe-size--io-balancing"></a>Dosya sistemleri, şeritli boyut & GÇ Dengeleme 
SAP ASE, aksi belirtilmedikçe verileri disk depolama cihazlarına sırayla yazar. Bu, dört cihazdan oluşan boş bir SAP Ao veritabanının yalnızca ilk cihaza veri yazacağı anlamına gelir.  Diğer disk cihazları yalnızca ilk cihaz dolduğunda yazılır.  Her SAP Ao cihazına yönelik okuma ve yazma GÇ miktarı büyük olasılıkla farklı olabilir. Tüm kullanılabilir Azure disklerinde disk GÇ 'yi dengelemek için Windows depolama alanları veya Linux LVM2 kullanılması gerekir. Linux 'ta, diskleri biçimlendirmek için XFS dosya sisteminin kullanılması önerilir. LVM Stripe boyutu bir performans testiyle test edilmelidir. 128 KB şeritli boyut iyi bir başlangıç noktasıdır. Windows 'da, NTFS ayırma birimi boyutu (Avustralya) test edilmelidir. 64 KB, başlangıç değeri olarak kullanılabilir. 

Otomatik veritabanı genişletmesinin, SAP Uyarlamalı sunucu Enterprise ve [sap desteği not#1815695](https://launchpad.support.sap.com/#/notes/1815695) [otomatik veritabanı alanı genişletmeyi yapılandırma](https://blogs.sap.com/2014/07/09/configuring-automatic-database-space-expansion-in-sap-adaptive-server-enterprise/) makalesinde açıklandığı şekilde yapılandırılması önerilir. 

### <a name="sample-sap-ase-on-azure-virtual-machine-disk-and-file-system-configurations"></a>Azure sanal makinesi, disk ve dosya sistemi yapılandırmalarında örnek SAP Ao 
Aşağıdaki şablonlar hem Linux hem de Windows için örnek yapılandırma gösterir. Sanal makine ve disk yapılandırmasını onaylamadan önce, bireysel VM 'nin ağ ve depolama bant genişliği kotalarının iş gereksinimini karşılamak için yeterli olduğundan emin olun. Ayrıca, farklı Azure VM türlerinin, VM 'ye eklenebilecek farklı disk sayısı üst sınırına sahip olduğunu aklınızda bulundurun. Örneğin, bir E4s_v3 VM 48 MB/sn depolama GÇ işleme sınırına sahiptir. Veritabanı yedekleme etkinliği için gereken depolama aktarım hızı 48 MB/sn 'den fazla talep isterse, daha fazla depolama bant genişliği işleme içeren daha büyük bir VM türü kaçınılmaz. Azure depolama 'yı yapılandırırken, özellikle de [Azure Premium Depolama](../../premium-storage-performance.md) ile GB 'lık kapasite başına aktarım hızı ve IOPS 'nin değişiklik olduğunu göz önünde bulundurmanız gerekir. Bu konuda daha fazla bilgi için bkz. [Azure 'da disk türleri kullanılabilir](../../disks-types.md). Belirli Azure VM türleri için kotalar, [bellek için iyileştirilmiş sanal makine boyutları](../../sizes-memory.md) ve bu makaleye bağlı makalelerde belgelenmiştir. 

> [!NOTE]
>  Bir DBMS sistemi Şirket içinden Azure 'a taşınırsa, sanal makine üzerinde izleme yapmanız ve CPU, bellek, ıOPS ve depolama aktarım hızını değerlendirmek önerilir. Yukarıda bahsedilen makalelerde belgelenen VM kota limitleriyle gözlemlenen en yüksek değerleri karşılaştırın

Aşağıda verilen örnekler tanım amaçlıdır ve bireysel gereksinimlere göre değiştirilebilir. SAP Ao 'nun tasarımı nedeniyle, veri cihazlarının sayısı diğer veritabanlarında olduğu kadar kritik değildir. Bu belgede ayrıntılı olarak açıklanan veri cihazlarının sayısı yalnızca bir kılavuzdur. 

SAP Solution Manager gibi 50 GB – 250 GB arasında bir veritabanı boyutu içeren küçük bir SAP Ao DB sunucusu için bir yapılandırmaya örnek olarak benzeyebilir

| Yapılandırma | Windows | Linux | Yorumlar |
| --- | --- | --- | --- |
| VM türü | E4s_v3 (4 vCPU/32 GB RAM) | E4s_v3 (4 vCPU/32 GB RAM) | --- |
| Hızlandırılmış Ağ | Etkinleştir | Etkinleştir | ---|
| SAP ATıCı sürümü | 16.0.03.07 veya üzeri | 16.0.03.07 veya üzeri | --- |
| veri cihazlarının sayısı | 4 | 4 | ---|
| günlük cihazlarının sayısı | 1 | 1 | --- |
| geçici cihaz sayısı | 1 | 1 | SAP BW iş yükü için daha fazla |
| İşletim sistemi | Windows Server 2019 | SUSE 12 SP4/15 SP1 veya RHEL 7,6 | --- |
| Disk toplama | Depolama Alanları | LVM2 | --- |
| Dosya sistemi | NTFS | XFS |
| Biçim blok boyutu | iş yükü testi gerekiyor | iş yükü testi gerekiyor | --- |
| # ve veri disklerinin türü | Premium Depolama: 2 x P10 (RAID0) | Premium Depolama: 2 x P10 (RAID0)| Cache = salt okunurdur |
| # ve günlük disklerinin türü | Premium Depolama: 1 x P20  | Premium Depolama: 1 x P20 | Önbellek = yok |
| ATıCı MaxMemory parametresi | Fiziksel RAM %90 | Fiziksel RAM %90 | tek örnek varsayılıyor |
| Yedekleme cihazlarının sayısı | 4 | 4| --- |
| Yedekleme disklerinin sayısı ve türü | 1 | 1 | --- |


Daha küçük bir SAP Business Suite sistemi gibi 250 GB – 750 GB arasında bir veritabanı boyutu olan orta SAP AAS DB sunucusu için bir yapılandırmaya örnek olarak benzeyebilir

| Yapılandırma | Windows | Linux | Yorumlar |
| --- | --- | --- | --- |
| VM türü | E16s_v3 (16 vCPU/128 GB RAM) | E16s_v3 (16 vCPU/128 GB RAM) | --- |
| Hızlandırılmış Ağ | Etkinleştir | Etkinleştir | ---|
| SAP ATıCı sürümü | 16.0.03.07 veya üzeri | 16.0.03.07 veya üzeri | --- |
| veri cihazlarının sayısı | 8 | 8 | ---|
| günlük cihazlarının sayısı | 1 | 1 | --- |
| geçici cihaz sayısı | 1 | 1 | SAP BW iş yükü için daha fazla |
| İşletim sistemi | Windows Server 2019 | SUSE 12 SP4/15 SP1 veya RHEL 7,6 | --- |
| Disk toplama | Depolama Alanları | LVM2 | --- |
| Dosya sistemi | NTFS | XFS |
| Biçim blok boyutu | iş yükü testi gerekiyor | iş yükü testi gerekiyor | --- |
| # ve veri disklerinin türü | Premium Depolama: 4 x P20 (RAID0) | Premium Depolama: 4 x P20 (RAID0)| Cache = salt okunurdur |
| # ve günlük disklerinin türü | Premium Depolama: 1 x P20  | Premium Depolama: 1 x P20 | Önbellek = yok |
| ATıCı MaxMemory parametresi | Fiziksel RAM %90 | Fiziksel RAM %90 | tek örnek varsayılıyor |
| Yedekleme cihazlarının sayısı | 4 | 4| --- |
| Yedekleme disklerinin sayısı ve türü | 1 | 1 | --- |

Daha büyük bir SAP Business Suite sistemi gibi 750 GB – 2000 GB arasında bir veritabanı boyutu içeren küçük bir SAP Ao DB sunucusu için bir yapılandırmaya örnek olarak benzeyebilir

| Yapılandırma | Windows | Linux | Yorumlar |
| --- | --- | --- | --- |
| VM türü | E64s_v3 (64 vCPU/432 GB RAM) | E64s_v3 (64 vCPU/432 GB RAM) | --- |
| Hızlandırılmış Ağ | Etkinleştir | Etkinleştir | ---|
| SAP ATıCı sürümü | 16.0.03.07 veya üzeri | 16.0.03.07 veya üzeri | --- |
| veri cihazlarının sayısı | 16 | 16 | ---|
| günlük cihazlarının sayısı | 1 | 1 | --- |
| geçici cihaz sayısı | 1 | 1 | SAP BW iş yükü için daha fazla |
| İşletim sistemi | Windows Server 2019 | SUSE 12 SP4/15 SP1 veya RHEL 7,6 | --- |
| Disk toplama | Depolama Alanları | LVM2 | --- |
| Dosya sistemi | NTFS | XFS |
| Biçim blok boyutu | iş yükü testi gerekiyor | iş yükü testi gerekiyor | --- |
| # ve veri disklerinin türü | Premium Depolama: 4 x P30 (RAID0) | Premium Depolama: 4 x P30 (RAID0)| Cache = salt okunurdur |
| # ve günlük disklerinin türü | Premium Depolama: 1 x P20  | Premium Depolama: 1 x P20 | Önbellek = yok |
| ATıCı MaxMemory parametresi | Fiziksel RAM %90 | Fiziksel RAM %90 | tek örnek varsayılıyor |
| Yedekleme cihazlarının sayısı | 4 | 4| --- |
| Yedekleme disklerinin sayısı ve türü | 1 | 1 | --- |


Daha büyük bir genel olarak kullanılan SAP Business Suite sistemi gibi bir veritabanı boyutu 2 TB + olan küçük SAP Ao DB sunucusu için bir yapılandırmaya örnek olarak benzeyebilir

| Yapılandırma | Windows | Linux | Yorumlar |
| --- | --- | --- | --- |
| VM türü | A serisi (1,0-4,0 TB RAM)  | A serisi (1,0-4,0 TB RAM) | --- |
| Hızlandırılmış Ağ | Etkinleştir | Etkinleştir | ---|
| SAP ATıCı sürümü | 16.0.03.07 veya üzeri | 16.0.03.07 veya üzeri | --- |
| veri cihazlarının sayısı | 32 | 32 | ---|
| günlük cihazlarının sayısı | 1 | 1 | --- |
| geçici cihaz sayısı | 1 | 1 | SAP BW iş yükü için daha fazla |
| İşletim sistemi | Windows Server 2019 | SUSE 12 SP4/15 SP1 veya RHEL 7,6 | --- |
| Disk toplama | Depolama Alanları | LVM2 | --- |
| Dosya sistemi | NTFS | XFS |
| Biçim blok boyutu | iş yükü testi gerekiyor | iş yükü testi gerekiyor | --- |
| # ve veri disklerinin türü | Premium Depolama: 4 + x P30 (RAID0) | Premium Depolama: 4 + x P30 (RAID0)| Önbellek = salt okuma, Azure Ultra disk 'i düşünün |
| # ve günlük disklerinin türü | Premium Depolama: 1 x P20  | Premium Depolama: 1 x P20 | Önbellek = yok, Azure Ultra disk 'i düşünün |
| ATıCı MaxMemory parametresi | Fiziksel RAM %90 | Fiziksel RAM %90 | tek örnek varsayılıyor |
| Yedekleme cihazlarının sayısı | 16 | 16 | --- |
| Yedekleme disklerinin sayısı ve türü | 4 | 4 | LVM2/Storage alanlarını kullanma |


### <a name="backup--restore-considerations-for-sap-ase-on-azure"></a>Azure 'da SAP Ao 'da yedekleme & geri yükleme konuları
Veri ve yedekleme cihazlarının sayısını artırmak, yedekleme ve geri yükleme performansını artırır. Daha önce gösterilen tablolarda gösterildiği gibi, SAP ASE yedekleme cihazını barındıran Azure disklerinin dizili olması önerilir. Yedekleme cihazlarının ve disklerin sayısını dengelemek ve yedek verimlilik 'in toplam VM üretilen iş kotasının %40 ' ını (%50) aşmadığından emin olmak için dikkatli olunmalıdır. SAP yedekleme sıkıştırması varsayılan olarak kullanılması önerilir. Makalelerde daha fazla ayrıntı bulabilirsiniz:

- [SAP destek notunun #1588316](https://launchpad.support.sap.com/#/notes/1588316)
- [SAP destek notunun #1801984](https://launchpad.support.sap.com/#/notes/1801984)
- [SAP destek notunun #1585981](https://launchpad.support.sap.com/#/notes/1585981) 

D:\ sürücüsünü kullanma ya da veritabanı ya da günlük dökümü hedefi olarak/Temp klasörüne yazılır Space.

### <a name="impact-of-database-compression"></a>Veritabanı sıkıştırmasının etkisi
G/ç bant genişliğinin sınırlama faktörü olabileceği yapılandırmalarda, ıOPS 'yi azaltan ölçümler, Azure gibi bir IaaS senaryosunda çalışan iş yükünü uzatmak için yardımcı olabilir. Bu nedenle, mevcut bir SAP veritabanını Azure 'a yüklemeden önce SAP ASE sıkıştırması 'nın kullanıldığından emin olmanız önerilir.

Azure 'a yüklemeden önce sıkıştırmayı uygulama önerisi çeşitli nedenlerden dolayı verilmiştir:

* Azure 'a yüklenecek veri miktarı düşüktür
* Sıkıştırma yürütmesinin süresi, bir birinin daha fazla CPU veya daha yüksek g/ç bant genişliği veya şirket içi g/ç gecikme süresiyle daha güçlü donanımlar kullanmasına olanak daha kısadır
* Daha küçük veritabanı boyutları disk ayırma için daha az maliyete yol açabilir

Veri ve LOB-Compression, şirket içinde çalıştığı gibi Azure sanal makinelerinde barındırılan bir VM 'de çalışır. Sıkıştırmanın mevcut bir SAP Ao veritabanında zaten kullanımda olup olmadığını denetleme hakkında daha fazla bilgi için [sap destek notuna 1750510](https://launchpad.support.sap.com/#/notes/1750510)bakın. SAP Ao veritabanı sıkıştırması hakkında daha fazla ayrıntı için [sap destek notuna bakın #2121797](https://launchpad.support.sap.com/#/notes/2121797)

## <a name="high-availability-of-sap-ase-on-azure"></a>Azure 'da SAP ASE 'nin yüksek kullanılabilirliği 
HADR kullanıcıları Kılavuzu, 2 düğümlü SAP ASE "her zaman açık" çözümünün kurulumunu ve yapılandırmasını ayrıntılarıyla gösterir.  Ayrıca, üçüncü bir olağanüstü durum kurtarma düğümü de desteklenir. SAP ASE, paylaşılan disk ve yerel işletim sistemi Kümelemesi (kayan IP) dahil olmak üzere çok sayıda kullanılabilir yapılandırmayı destekler. Azure 'daki tek desteklenen yapılandırma, kayan IP olmadan hata Yöneticisi kullanıyor.  Kayan IP adresi yöntemi Azure 'da çalışmayacak.  SAP Kernel, "HA duyarlı" bir uygulamadır ve birincil ve ikincil SAP Ao sunucuları hakkında bilgi sahibi olur. SAP asa ve Azure arasında hiç tümleştirme yoktur, Azure Iç yük dengeleyici kullanılmaz. Bu nedenle, standart SAP Ao belgelerinin ardından [SAP Ao HADR kullanıcıları kılavuzuyla](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.7/en-US/a6645e28bc2b1014b54b8815a64b87ba.html) başlaması gerekir 

> [!NOTE]
> Azure 'daki tek desteklenen yapılandırma, kayan IP olmadan hata Yöneticisi kullanıyor.  Kayan IP adresi yöntemi Azure 'da çalışmayacak. 

### <a name="third-node-for-disaster-recovery"></a>Olağanüstü durum kurtarma için üçüncü düğüm
Yerel yüksek kullanılabilirlik için SAP ASE Always-On kullanmanın ötesinde, yapılandırmayı başka bir Azure bölgesindeki zaman uyumsuz olarak çoğaltılan bir düğüme genişletmek isteyebilirsiniz. Böyle bir senaryoya yönelik belgeler [burada](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)bulunabilir.

## <a name="sap-ase-database-encryption--ssl"></a>SAP Ao veritabanı şifreleme & SSL 
SAP yazılım sağlama Yöneticisi (SWPM), yükleme sırasında veritabanını şifrelemek için bir seçenek sunar.  Şifrelemeyi kullanmak istiyorsanız, SAP tam veritabanı şifrelemesini kullanmanız önerilir.  Şu belgelerde belgelenen ayrıntılara bakın:

- [SAP destek notunun #2556658](https://launchpad.support.sap.com/#/notes/2556658)
- [SAP destek notunun #2224138](https://launchpad.support.sap.com/#/notes/2224138)
- [SAP destek notunun #2401066](https://launchpad.support.sap.com/#/notes/2401066)
- [SAP destek notunun #2593925](https://launchpad.support.sap.com/#/notes/2593925) 

> [!NOTE]
> SAP ASE veritabanı şifrelenirse, yedek döküm sıkıştırması çalışmaz. Ayrıca bkz. [sap destek notuna #2680905](https://launchpad.support.sap.com/#/notes/2680905) 

## <a name="sap-ase-on-azure-deployment-checklist"></a>Azure dağıtım denetim listesi üzerinde SAP Ade
 
- SAP Ao 16.0.03.07 veya üstünü dağıtın
- FaultManager ve SAPHostAgent 'ın en son sürümüne ve düzeltme eklerine güncelleştirme
- Windows 2019, SUSE 15,1 veya RedHat 7,6 veya üzeri gibi kullanıma sunulan en son sertifikalı IŞLETIM sistemlerinde dağıtın
- SAP sertifikalı VM 'Leri kullanma – Es_v3 gibi yüksek bellek Azure VM SKU 'Ları veya x-büyük sistemler için d serisi VM SKU 'Ları önerilir
- Disk ıOPS ve VM 'nin toplam VM toplam verimlilik kotasını, disk tasarımıyla eşleştirin.  Yeterli sayıda diski dağıtma
- Doğru dizili boyut ve dosya sistemi ile Windows depolama alanları veya Linux LVM2 kullanarak diskleri toplama
- Veri, günlük, geçici ve yedekleme amacıyla yeterli sayıda cihaz oluşturun
- X-büyük sistemler için UltraDisk kullanmayı düşünün 
- `saptune`LINUX işletim sisteminde SAP-Ao çalıştırma 
- Veritabanını DB şifrelemesiyle güvenli hale getirme-anahtarları Azure Key Vault el ile depolayın 
- [Azure 'Da SAP 'yi doldurun denetim listesi](./sap-deployment-checklist.md) 
- Günlük yedeklemesini ve tam yedeklemeyi yapılandırma 
- HA/DR testi, yedekleme ve geri yükleme ve stres & toplu testi gerçekleştirme 
- Otomatik veritabanı uzantısının çalıştığını Onayla 

## <a name="using-dbacockpit-to-monitor-database-instances"></a>Veritabanı örneklerini izlemek için Dbakokpit kullanma
Veritabanı platformu olarak SAP AAS kullanan SAP sistemleri için Dbakokpit, işlem Dbakokpit veya WebDynpro olarak katıştırılmış tarayıcı pencereleri olarak erişilebilir. Ancak, veritabanını izlemeye ve yönetmeye yönelik tüm işlevler yalnızca Dbakokpit 'ın WebDynpro uygulamasında kullanılabilir.

Şirket içi sistemlerde olduğu gibi, Dbakokpitinin WebDynpro uygulamasının kullandığı tüm SAP NetWeaver işlevlerini etkinleştirmek için birkaç adım gerekir. Web dynprofesyonelleri kullanımını etkinleştirmek ve gerekli olanları oluşturmak için [sap destek not#1245200](https://launchpad.support.sap.com/#/notes/1245200) izleyin. Yukarıdaki notlardaki yönergeleri izleyerek, Internet Iletişim Yöneticisi 'Ni ( `ICM` ) http ve HTTPS bağlantıları için kullanılacak bağlantı noktalarıyla birlikte da yapılandırırsınız. Http için varsayılan ayar şöyle görünür:

> ICM/server_port_0 = PROT = HTTP, bağlantı noktası = 8000, PROCTIMEOUT = 600, zaman AŞıMı = 600
> 
> ICM/server_port_1 = PROT = HTTPS, PORT = 443 $ $, PROCTIMEOUT = 600, TIMEOUT = 600
> 
> 

ve işlem Dbakokpit içinde oluşturulan bağlantılar şuna benzer:

> https: \/ / \<fullyqualifiedhostname> : 44300/SAP/BC/WebDynpro/SAP/dba_cockpit
> 
> http: \/ / \<fullyqualifiedhostname> : 8000/SAP/BC/WebDynpro/SAP/dba_cockpit
> 
> 

SAP sistemini barındıran Azure sanal makinesinin AD ve DNS 'nize nasıl bağlı olduğuna bağlı olarak, ICM 'nin Dbakokpit 'yi açtığınız makinede çözümlenebileceğiniz tam bir ana bilgisayar adı kullandığını doğrulayın. ICM 'nin profil parametrelerine bağlı olarak tam ana bilgisayar adını nasıl belirlediğini ve gerekirse ICM/host_name_full parametresini ayarlama hakkında bilgi edinmek için bkz. [sap destek notunun #773830](https://launchpad.support.sap.com/#/notes/773830) .

VM 'yi şirket içi ve Azure arasında şirketler arası bağlantı olmadan bir Cloud-Only senaryosunda dağıttıysanız, bir genel IP adresi ve bir tanımlamanız gerekir `domainlabel` . VM 'nin Genel DNS adının biçimi şöyle görünür:

> `<custom domainlabel`>. `<azure region`>. cloudapp.azure.com
> 
> 

DNS adıyla ilgili daha fazla ayrıntı [burada] [sanal-makineler-azurerd-ve-azuresd] bulunabilir.

SAP profili parametresini ICM/host_name_full olarak ayarlama, bağlantının şuna benzeyebilir:

> https: \/ /mydomainlabel.westeurope.cloudapp.net:44300/SAP/BC/WebDynpro/SAP/dba_cockpit
> 
> http: \/ /mydomainlabel.westeurope.cloudapp.net:8000/SAP/BC/WebDynpro/SAP/dba_cockpit

Bu durumda şunları yapmanız gerekir:

* ICM ile iletişim kurmak için kullanılan TCP/IP bağlantı noktaları için Azure portal ağ güvenlik grubuna gelen kuralları ekleyin
* ICM ile iletişim kurmak için kullanılan TCP/IP bağlantı noktaları için Windows Güvenlik Duvarı yapılandırmasına gelen kuralları ekleyin

Tüm düzeltmelerin otomatik olarak içeri aktarılması için, SAP sürümünüze uygun olan düzeltme toplama SAP notunun düzenli olarak uygulanması önerilir:

* [SAP destek notunun #1558958](https://launchpad.support.sap.com/#/notes/1558958)
* [SAP destek notunun #1619967](https://launchpad.support.sap.com/#/notes/1619967)
* [SAP destek notunun #1882376](https://launchpad.support.sap.com/#/notes/1882376)

SAP ATıCı için DBA kokpiti hakkında daha fazla bilgi aşağıdaki SAP notlarında bulunabilir:

* [SAP destek notunun #1605680](https://launchpad.support.sap.com/#/notes/1605680)
* [SAP destek notunun #1757924](https://launchpad.support.sap.com/#/notes/1757924)
* [SAP destek notunun #1757928](https://launchpad.support.sap.com/#/notes/1757928)
* [SAP destek notunun #1758182](https://launchpad.support.sap.com/#/notes/1758182)
* [SAP destek notunun #1758496](https://launchpad.support.sap.com/#/notes/1758496)    
* [SAP destek notunun #1814258](https://launchpad.support.sap.com/#/notes/1814258)
* [SAP destek notunun #1922555](https://launchpad.support.sap.com/#/notes/1922555)
* [SAP destek notunun #1956005](https://launchpad.support.sap.com/#/notes/1956005)


## <a name="useful-links-notes--whitepapers-for-sap-ase"></a>Faydalı bağlantılar, notlar & SAP asa için teknik incelemeler
[SAP asa 16.0.03.07 belgelerinin](https://help.sap.com/viewer/product/SAP_ASE/16.0.3.7/en-US) başlangıç sayfası, belgelerinin bulunduğu çeşitli belgelere bağlantılar sağlar:

- SAP ATıCı öğrenme yolculuğu-yönetim & Izleme
- SAP Ade öğrenme yolculuğu-yükleme & yükseltme

yararlıdır. Başka bir faydalı belge, SAP [Uyarlamalı sunucu 'Nun geçiş ve çalışma zamanı Için En Iyi uygulamalarında SAP uygulamalardır](https://assets.cdn.sap.com/sapcom/docs/2016/06/26450353-767c-0010-82c7-eda71af511fa.pdf).

Diğer yardımcı SAP destek notları şunlardır:

- [SAP destek notunun #2134316](https://launchpad.support.sap.com/#/notes/2134316) 
- [SAP destek notunun #1748888](https://launchpad.support.sap.com/#/notes/1748888) 
- [SAP destek notunun #2588660](https://launchpad.support.sap.com/#/notes/2588660) 
- [SAP destek notunun #1680803](https://launchpad.support.sap.com/#/notes/1680803) 
- [SAP destek notunun #1724091](https://launchpad.support.sap.com/#/notes/1724091) 
- [SAP destek notunun #1775764](https://launchpad.support.sap.com/#/notes/1775764) 
- [SAP destek notunun #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP destek notunun #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- [SAP destek notunun #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- [SAP destek notunun #1750510](https://launchpad.support.sap.com/#/notes/1750510) 
- [SAP destek notunun #1752266](https://launchpad.support.sap.com/#/notes/1752266) 
- [SAP destek notunun #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP destek notunun #1588316](https://launchpad.support.sap.com/#/notes/158831) 

Diğer bilgiler üzerinde yayımlanır 

- [SAP Uyarlamalı sunucu Enterprise 'ta SAP uygulamaları](https://community.sap.com/topics/applications-on-ase)
- [SAP ATıCı bilgi merkezi](http://infocenter.sybase.com/help/index.jsp) 
- [3. DR düğüm kurulumu ile SAP ASE her zaman açık](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)

Aylık bülten, [sap destek notunda yayımlanır #2381575](https://launchpad.support.sap.com/#/notes/2381575) 


## <a name="next-steps"></a>Sonraki adımlar
[Azure 'Da SAP iş yükleri makalesine bakın: planlama ve dağıtım denetim listesi](./sap-deployment-checklist.md)