---
title: include dosyası
description: include dosyası
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/12/2019
ms.author: cynthn;kareni
ms.custom: include file
ms.openlocfilehash: 6668d9753d0b93ab907d37cdeff8315f488cff7a
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73935867"
---
**Son belge güncelleştirmesi**: 12 kasım 2019 10:00 PST.

Kurgusal yürütme yan kanal saldırıları olarak bilinen [Yeni BIR CPU güvenlik açığı sınıfının](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002) açıklanması, müşterilerden daha fazla açıklık isteyen sorulara neden oldu.  

Microsoft, tüm bulut hizmetlerimiz üzerinde azaltmalar dağıttı. Azure çalıştıran ve müşteri iş yüklerini birbirinden yalıtan altyapı korunur. Yani aynı altyapıyı kullanan potansiyel bir saldırgan, bu güvenlik açıklarını kullanarak uygulamanıza saldırır.

Azure, müşteri etkisini en aza indirmek ve yeniden başlatmalar gereksinimini ortadan kaldırmak için mümkün olduğunda [bakım işlemlerini koruyan bellek](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates#maintenance-that-doesnt-require-a-reboot) kullanıyor. Azure, konakta sistem genelinde güncelleştirmeler yaparken ve müşterilerimizi koruduğunuz bu yöntemlerle yararlanmaya devam edecektir.

Güvenliğin Azure 'un her yönüyle tümleştirildiği hakkında daha fazla bilgi için [Azure Güvenlik belgeleri](https://docs.microsoft.com/azure/security/) sitesinde bulabilirsiniz. 

> [!NOTE] 
> Bu belge ilk yayımlandığında, bu güvenlik açığı sınıfının birden çok varyantı duyurulmuştur. Microsoft, müşterilerimizi korumak ve rehberlik sağlamak için büyük ölçüde yatırım yapmaya devam etmektedir. Bu sayfa, daha fazla düzeltme yapmaya devam ettiğimiz için güncelleştirilir. 
> 
> 12 Kasım 2019 ' de, [ıntel](https://software.intel.com/security-software-guidance/insights/deep-dive-intel-transactional-synchronization-extensions-intel-tsx-asynchronous-abort) ® Işlemsel eşitleme uzantıları (ıNTEL® TSX) Işlem zaman uyumsuz iptali (taa) güvenlik açığı, [CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135)' y a kadar bir teknik danışmanlık yayımladı. Bu güvenlik açığı Intel® Core® işlemcileri ve Intel® Xeon® işlemcileri etkiler.  Microsoft Azure, işletim sistemi güncelleştirmeleri yayımlamıştır ve yeni mikro kod dağıtmakta ve bu yeni güvenlik açıklarına karşı müşterilerimizi korumak için, dünyanın her yerindeki yeni mikro kod dağıtmaktadır.   Azure, yeni mikro kodu platformda çalışan resmi sürümünden önce test etmek ve doğrulamak üzere Intel ile yakından çalışır. 
>
> **VM 'ler içinde güvenilmeyen kod çalıştıran müşterilerin** , tüm kurgusal yürütme tarafı güvenlik açıklarına yönelik ek yönergeler için aşağıdaki adımları okuyarak bu güvenlik açıklarına karşı korunmak üzere işlem yapması gerekir (Microsoft danışma adımları [180002](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180002), [180018](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/adv180018)ve [190013](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV190013)).
>
> Diğer müşteriler bu güvenlik açıklarını derinlemesine bir savunma açısından değerlendirmelidir ve seçilen yapılandırmalarının güvenlik ve performans etkilerini göz önünde bulundurun.
> 



## <a name="keeping-your-operating-systems-up-to-date"></a>İşletim sistemlerinizi güncel tutma

Diğer Azure müşterilerinin Azure üzerinde çalışan uygulamalarınızı yalıtmak için bir işletim sistemi güncelleştirmesi gerekli olmasa da, yazılımınızın güncel tutulması her zaman en iyi uygulamadır. Windows için en son güvenlik paketleri, bazı kurgusal yürütme tarafı kanalları güvenlik açıkları için azaltıcı etkenleri içerir. Benzer şekilde, Linux dağıtımları bu güvenlik açıklarını gidermek için birden çok güncelleştirme yayımlamıştır. İşletim sisteminizi güncelleştirmek için önerdiğimiz eylemler şunlardır:

| Teklifi | Önerilen Eylem  |
|----------|---------------------|
| Azure Cloud Services  | [Otomatik güncelleştirmeyi](https://docs.microsoft.com/azure/cloud-services/cloud-services-how-to-configure-portal) etkinleştirin veya en yeni Konuk işletim sistemini çalıştırdığınız emin olun. |
| Azure Linux Sanal Makineleri | İşletim sistemi sağlayıcınızdan güncelleştirmeleri yükler. Daha fazla bilgi için bu belgenin devamındaki [Linux](#linux) bölümüne bakın. |
| Azure Windows Sanal Makineleri  | En son güvenlik paketini yükler.
| Diğer Azure PaaS hizmetleri | Bu hizmetleri kullanan müşteriler için gerekli bir işlem yoktur. Azure, işletim sistemi sürümlerinizi otomatik olarak güncel tutar. |

## <a name="additional-guidance-if-you-are-running-untrusted-code"></a>Güvenilmeyen kod çalıştırıyorsanız ek rehberlik 

Güvenilmeyen kullanıcıların rastgele kod yürütmesine izin veren müşteriler, bazı ek güvenlik özelliklerini Azure sanal makineleri içinde veya Cloud Services uygulamak isteyebilir. Bu özellikler, çeşitli öngörülebilir yürütme güvenlik açıklarının açıkladığı, işlem içi açıklama vektörlerine karşı koruma sunar.

Ek güvenlik özelliklerinin önerildiği örnek senaryolar:

- SANAL makinenizin içinde çalışmak için güvenmediğiniz koda izin verebilirsiniz.  
    - *Örneğin, müşterilerinizin uygulamanızın içinde yürütebileceğiniz bir ikili veya betiği karşıya yüklemesine izin vereceksiniz*. 
- Güvensiz olmayan kullanıcıların, düşük ayrıcalıklı hesaplar kullanarak sanal makinenizde oturum açmasına izin verebilirsiniz.   
    - *Örneğin, düşük ayrıcalıklı bir kullanıcının Uzak Masaüstü veya SSH kullanarak sanal makinelerinizdeki birinde oturum açmasına izin*verebilirsiniz.  
- Güvenilmeyen kullanıcıların, iç içe sanallaştırma aracılığıyla uygulanan sanal makinelere erişmesine izin verebilirsiniz.  
    - *Örneğin, Hyper-V konağını denet, ancak VM 'leri güvenilmeyen kullanıcılara ayırırsınız*. 

Güvenilmeyen kod içeren bir senaryo gerçekleştirmeyen müşterilerin bu ek güvenlik özelliklerini etkinleştirmesi gerekmez. 

## <a name="enabling-additional-security"></a>Ek güvenliği etkinleştirme 

Güvenilmeyen kod çalıştırıyorsanız, VM 'niz veya bulut hizmetiniz içinde ek güvenlik özellikleri etkinleştirebilirsiniz. Paralel olarak, sanal makinenizin veya bulut hizmetinizin içindeki güvenlik özelliklerini etkinleştirmek için işletim sisteminizin güncel olduğundan emin olun

### <a name="windows"></a>Windows 

Bu ek güvenlik özelliklerinin etkinleştirilmesi için hedef işletim sisteminizin güncel olması gerekir. Birçok sayıda kurgusal yürütme tarafı kanal azaltmaları varsayılan olarak etkinken, burada açıklanan ek özellikler el ile etkinleştirilmelidir ve performans etkisine neden olabilir. 


**1. Adım: sanal makinede hiper iş parçacığı devre dışı bırakma** -hiper iş PARÇACıKLı bir sanal makinede güvenilmeyen kod çalıştıran müşterilerin hiper iş parçacığı devre dışı bırakılması veya hiper iş PARÇACıKLı bir VM boyutuna taşınması gerekir. Hiper iş parçacıklı VM boyutlarının bir listesi için [Bu belgeye](https://docs.microsoft.com/azure/virtual-machines/windows/acu) başvurun (vCPU ile çekirdekli oran 2:1 ' dir). VM 'nizin hiper iş parçacığı oluşturma özelliğinin etkin olup olmadığını denetlemek için lütfen VM 'nin içinden Windows komut satırını kullanarak aşağıdaki komut dosyasına bakın.

Etkileşimli arabirimi girmek için `wmic` yazın. Ardından, VM 'deki fiziksel ve mantıksal işlemci miktarını görüntülemek için aşağıdaki adımları yazın.

```console
CPU Get NumberOfCores,NumberOfLogicalProcessors /Format:List
```

Mantıksal işlemcilerin sayısı fiziksel işlemcilere (çekirdek) fazlaysa, hiper iş parçacığı etkin olur.  Hiper iş parçacıklı bir VM çalıştırıyorsanız, lütfen hiper iş parçacığı oluşturmayı devre dışı bırakmak için [Azure desteğine başvurun](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) .  Hiper iş parçacığı devre dışı bırakıldıktan sonra, **destek tam VM yeniden başlatması gerektirir**. VM çekirdek saymanızın neden azaldığına anlamak için lütfen [çekirdek sayısı](#core-count) ' na bakın.


**2. adım**: 1. adıma paralel olarak, korumaların [speculationcontrol](https://aka.ms/SpeculationControlPS) PowerShell modülü kullanılarak etkinleştirildiğini doğrulamak için [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) içindeki yönergeleri izleyin.

> [!NOTE]
> Bu modülü daha önce indirdiyseniz, en yeni sürümü yüklemeniz gerekir.
>


PowerShell betiğinin çıktısı, etkin korumaların bu güvenlik açıklarına karşı doğrulanması için aşağıdaki değerlere sahip olmalıdır:

```
Windows OS support for branch target injection mitigation is enabled: True
Windows OS support for kernel VA shadow is enabled: True
Windows OS support for speculative store bypass disable is enabled system-wide: False
Windows OS support for L1 terminal fault mitigation is enabled: True
Windows OS support for MDS mitigation is enabled: True
Windows OS support for TAA mitigation is enabled: True
```

Çıktı `MDS mitigation is enabled: False`gösteriyorsa, lütfen kullanılabilir risk azaltma seçenekleri için [Azure desteğine başvurun](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) .



**3. adım**: çekirdek sanal adres GÖLGELEME (Kvas) ve dal hedefi ekleme (BTı) işletim sistemi desteğini etkinleştirmek için, `Session Manager` kayıt defteri anahtarlarını kullanarak korumaları etkinleştirmek üzere [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) içindeki yönergeleri izleyin. Yeniden başlatma gerekiyor.


**4. adım**: [iç içe sanallaştırma](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization) kullanan dağıtımlar (yalnızca D3 ve E3): Bu yönergeler Hyper-V konağı olarak kullandığınız VM 'nin içinde geçerlidir.

1.  `MinVmVersionForCpuBasedMitigations` kayıt defteri anahtarlarını kullanarak korumaları etkinleştirmek için [KB4072698](https://support.microsoft.com/help/4072698/windows-server-guidance-to-protect-against-the-speculative-execution) içindeki yönergeleri izleyin.
2.  [Buradaki](https://docs.microsoft.com/windows-server/virtualization/hyper-v/manage/manage-hyper-v-scheduler-types)yönergeleri izleyerek hiper yönetici zamanlayıcı türünü `Core` olarak ayarlayın.


### <a name="linux"></a>Linux

<a name="linux"></a>İçinde ek güvenlik özellikleri kümesinin etkinleştirilmesi, hedef işletim sisteminin tamamen güncel olmasını gerektirir. Bazı azaltmaları varsayılan olarak etkinleştirilir. Aşağıdaki bölümde, varsayılan olarak kapalı olan ve/veya donanım desteği 'ne (mikro kod) bağlı olan özellikler açıklanmaktadır. Bu özelliklerin etkinleştirilmesi performans etkisine neden olabilir. Daha fazla yönerge için işletim sistemi sağlayıcınızın belgelerine başvurun


**1. Adım: sanal makinede hiper iş parçacığı devre dışı bırakma** -hiper iş PARÇACıKLı bir sanal makinede güvenilmeyen kod çalıştıran müşterilerin hiper iş parçacığı devre dışı bırakılması veya hiper iş PARÇACıKLı bir VM 'ye taşınması gerekir.  Hiper iş parçacıklı VM boyutlarının bir listesi için [Bu belgeye](https://docs.microsoft.com/azure/virtual-machines/linux/acu) başvurun (vCPU ile çekirdekli oran 2:1 ' dir). Hiper iş parçacıklı bir VM çalıştırıp çalıştıracağınızı denetlemek için Linux VM 'de `lscpu` komutunu çalıştırın. 

`Thread(s) per core = 2`, hiper iş parçacığı oluşturma etkinleştirilmiştir. 

`Thread(s) per core = 1`, hiper iş parçacığı devre dışı bırakıldı. 

 
Hiper iş parçacığı etkin bir VM için örnek çıkış: 

```console
CPU Architecture:      x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                8
On-line CPU(s) list:   0-7
Thread(s) per core:    2
Core(s) per socket:    4
Socket(s):             1
NUMA node(s):          1

```

Hiper iş parçacıklı bir VM çalıştırıyorsanız, lütfen hiper iş parçacığı oluşturmayı devre dışı bırakmak için [Azure desteğine başvurun](https://aka.ms/MicrocodeEnablementRequest-SupportTechnical) .  Hiper iş parçacığı devre dışı bırakıldıktan sonra, **destek tam VM yeniden başlatması gerektirir**. VM çekirdek saymanızın neden azaldığına anlamak için lütfen [çekirdek sayısı](#core-count) ' na bakın.



**2. adım**: aşağıdaki kurgusal yürütme tarafı kanallı güvenlik açıklarından herhangi birine karşı hafifletmek için, işletim sistemi sağlayıcınızın belgelerine başvurun:   
 
- [RedHat ve CentOS](https://access.redhat.com/security/vulnerabilities) 
- [SUSE](https://www.suse.com/support/kb/?doctype%5B%5D=DT_SUSESDB_PSDB_1_1&startIndex=1&maxIndex=0) 
- [Ubuntu](https://wiki.ubuntu.com/SecurityTeam/KnowledgeBase/) 


### <a name="core-count"></a>Çekirdek sayısı

Hiper iş parçacıklı bir VM oluşturulduğunda Azure, çekirdek başına 2 iş parçacığı ayırır. Bunlar vCPU olarak adlandırılır. Hiper iş parçacığı devre dışı bırakıldığında, Azure bir iş parçacığını kaldırır ve tek iş parçacıklı çekirdekleri (fiziksel çekirdekler) yüzeyler. VCPU 'nun CPU 'ya oranı 2:1 ' dir, bu nedenle hiper iş parçacığı devre dışı bırakıldıktan sonra, VM 'deki CPU sayısı yarıya düşdü olarak görünür. Örneğin, bir D8_v3 VM, 8 vCPU üzerinde çalışan hiper iş parçacıklı bir sanal sunucudur (çekirdek x 4 çekirdek başına 2 iş parçacığı).  Hiper iş parçacığı devre dışı bırakıldığında, CPU 'Lar çekirdek başına 1 iş parçacığı ile 4 fiziksel çekirdeğe düşürülecektir. 

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede birçok modern işlemciyi etkileyen aşağıdaki kurgusal yürütme yan kanal saldırılarına yönelik rehberlik sunulmaktadır:

[Spectre Meltazaltma](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180002):
- CVE-2017-5715-dal hedefi ekleme (BTI)  
- CVE-2017-5754-çekirdek sayfa tablosu yalıtımı (KPTI)
- CVE-2018-3639 – Speculatıcı depoyu atlama (KPTI) 
- [CVE-2019-1125](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1125) – Windows çekirdek bilgileri – Spectre Variant 1 değişkeni
 
[L1 Terminal hatası (L1TF)](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV180018):
- CVE-2018-3615-Intel Software Guard uzantıları (Intel SGX)
- CVE-2018-3620-Işletim sistemleri (OS) ve sistem yönetimi modu (SMM)
- CVE-2018-3646 – Virtual Machine Manager etkiler (VMM)

[Mikro mimari veri örnekleme](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190013): 
- CVE-2019-11091-mikro mimari veri örnekleme önbelleklenebilir bellek (MDSUM)
- CVE-2018-12126-mikro mimari depo arabelleği veri örnekleme (MSBDS)
- CVE-2018-12127-mikro mimari yük bağlantı noktası veri örnekleme (MLPDS)
- CVE-2018-12130-mikro mimari Fill buffer veri örnekleme (MFBDS)

İşlem eşitleme uzantıları (Intel® TSX) Işlem zaman uyumsuz Iptali:  
- [CVE-2019-11135](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-11135) – TSX Işlem zaman uyumsuz iptali (taa)








