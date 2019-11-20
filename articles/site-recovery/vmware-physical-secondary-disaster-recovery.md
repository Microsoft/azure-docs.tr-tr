---
title: VMware VM 'lerini/fiziksel sunucuları Azure Site Recovery ikincil bir siteye olağanüstü durum kurtarma
description: VMware VM 'Leri veya Windows ve Linux fiziksel sunucularının olağanüstü durum kurtarmayı Azure Site Recovery ile ikincil bir siteye ayarlamayı öğrenin.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: 71d230c9fea25edfbf0ca4ea40f15b69779ad060
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73620529"
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Şirket içi VMware sanal makineleri veya fiziksel sunucuları ikincil bir siteye olağanüstü durum kurtarması ayarlama

[Azure Site Recovery](site-recovery-overview.md) InMage Scout, şirket içi VMware siteleri arasında gerçek zamanlı çoğaltma sağlar. InMage Scout Azure Site Recovery hizmet aboneliklerine dahildir.

## <a name="end-of-support-announcement"></a>Destek sonu duyurusu

Şirket içi VMware veya fiziksel veri merkezleri arasında çoğaltmaya yönelik Azure Site Recovery senaryosu, destek sonuna ulaşıyor.

-   2018 Ağustos 'tan, senaryo kurtarma hizmetleri kasasında yapılandırılamaz ve InMage Scout yazılımı kasadan indirilemez. Mevcut dağıtımlar desteklenecek. 
-   31 2020 Aralık 'tan senaryo desteklenmez.
- Mevcut iş ortakları, destek sona erene kadar senaryoya yeni müşteriler ekleyebilir.

2018 ve 2019 sırasında iki güncelleştirme kullanıma sunulacaktır: 

-   Güncelleştirme 7: ağ yapılandırma ve uyumluluk sorunlarını düzeltir ve TLS 1,2 desteği sağlar.
-   Güncelleştirme 8: Linux işletim sistemleri RHEL/CentOS 7.3/7.4/7.5 ve SUSE 12 için destek ekler

Güncelleştirme 8 ' den sonra daha fazla güncelleştirme yayınlanmayacaktır. Güncelleştirme 8 ' de eklenen işletim sistemleri için sınırlı Düzeltme desteği ve en iyi çabayı temel alan hata düzeltmeleri olacaktır.

Azure Site Recovery, VMware ve Hyper-V müşterilerini olağanüstü durum kurtarma sitesi olarak Azure ile sorunsuz ve en iyi bir DRaaS çözümü sunarak yenilik yapın olmaya devam eder. Microsoft, mevcut InMage/ASR Scout müşterilerinin iş sürekliliği ihtiyaçları için Azure Site Recovery VMware 'den Azure senaryosuna kullanmayı düşünmelerini önerir. VMware 'den Azure 'a senaryo Azure Site Recovery, bir VMware uygulamaları için RPO ve RTO dakika, çoklu VM uygulama çoğaltma ve kurtarma, sorunsuz ekleme, kapsamlı izleme ve önemli TCO avantajı.

### <a name="scenario-migration"></a>Senaryo geçişi
Alternatif olarak, şirket içi VMware VM 'Leri ve fiziksel makineler için olağanüstü durum kurtarma 'yı Azure 'a çoğaltarak ayarlamayı öneririz. Bunu aşağıdaki gibi yapın:

1.  Aşağıdaki hızlı karşılaştırmayı gözden geçirin. Şirket içi makineleri çoğaltabilmeniz için önce Azure 'a çoğaltmaya yönelik [gereksinimleri](./vmware-physical-azure-support-matrix.md#replicated-machines) karşıladığından emin olmanız gerekir. VMware VM 'lerini çoğaltırken, [Kapasite planlama yönergeleri](./site-recovery-plan-capacity-vmware.md)' ni incelemenizi ve [dağıtım planlayıcısı aracı](./site-recovery-deployment-planner.md) 'nı kimlik kapasitesi gereksinimlerine göre çalıştırmayı ve uyumluluğu doğrulamanızı öneririz.
2.  Dağıtım Planlayıcısı çalıştırdıktan sonra, VMware VM 'Leri Için çoğaltmayı ayarlayabilir, [Azure 'u hazırlamak](./tutorial-prepare-azure.md), Şirket [içi VMware ortamınızı hazırlamak](./vmware-azure-tutorial-prepare-on-premises.md)ve [olağanüstü durum kurtarma ayarlamak](./vmware-azure-tutorial-prepare-on-premises.md)için bu öğreticilerden birini izleyebilirsiniz.
o fiziksel makineler Için bu [öğreticiyi](./physical-azure-disaster-recovery.md)izleyin.
3.  Makineler Azure 'a çoğaltıldıktan sonra her şeyin beklendiği gibi çalıştığından emin olmak için bir [olağanüstü durum kurtarma detayına](./site-recovery-test-failover-to-azure.md) sahip olabilirsiniz.

### <a name="quick-comparison"></a>Hızlı karşılaştırma

**Özellik** | **Azure 'a çoğaltma** |**VMware veri merkezleri arasında çoğaltma**
--|--|--
**Gerekli bileşenler** |Çoğaltılan makinelerde Mobility hizmeti. Şirket içi yapılandırma sunucusu, işlem sunucusu, ana hedef sunucusu. Yeniden çalışma için Azure 'da geçici işlem sunucusu.|Mobility hizmeti, Işlem sunucusu, yapılandırma sunucusu ve ana hedef
**Yapılandırma ve düzenleme** |Azure portal kurtarma hizmetleri Kasası | VContinuum kullanma 
**Çoğaltılamaz** |Disk (Windows ve Linux) |Birim-Windows<br> Disk-Linux
**Paylaşılan disk kümesi** |Desteklenmiyor|Destekleniyor
**Veri dalgalanma sınırları (Ortalama)** |disk başına 10 MB/s veri<br> VM başına 250 MB/s veri<br> [Daha fazla bilgi](./site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) | disk başına 10 MB/s veri >  <br> VM başına 25 MB/s veri >
**İzleme** |Azure portal 'den|CX (yapılandırma sunucusu)
**Destek matrisi** | [Ayrıntılar için buraya tıklayın](./vmware-physical-azure-support-matrix.md)|[ASR Scout uyumlu matrisi indir](https://aka.ms/asr-scout-cm)


## <a name="prerequisites"></a>Ön koşullar
Bu öğreticiyi tamamlamak için:

- Tüm bileşenler için destek gereksinimlerini [gözden geçirin](vmware-physical-secondary-support-matrix.md) .
- Çoğaltmak istediğiniz makinelerin [çoğaltılan makine desteğiyle](vmware-physical-secondary-support-matrix.md#replicated-vm-support)uyumlu olduğundan emin olun.


## <a name="download-and-install-component-updates"></a>Bileşen güncelleştirmelerini indirme ve yükleme

 En son [güncelleştirmeleri](#updates)gözden geçirin ve yükler. Güncelleştirmeler aşağıdaki sırayla sunuculara yüklenmelidir:

1. RX sunucusu (varsa)
2. Yapılandırma sunucuları
3. İşlem sunucuları
4. Ana hedef sunucuları
5. vContinuum sunucuları
6. Kaynak sunucu (hem Windows hem de Linux sunucuları)

Güncelleştirmeleri şu şekilde yükler:

> [!NOTE]
>Tüm Scout bileşenlerinin dosya güncelleştirme sürümü Update. zip dosyasında aynı olmayabilir. Eski sürüm, bu güncelleştirme için önceki güncelleştirmeden bu yana bileşende değişiklik olmadığını gösterir.

[Update](https://aka.ms/asr-scout-update7) . zip dosyasını ve [MySQL ve php yükseltme](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade) yapılandırma dosyalarını indirin. Update. zip dosyası, aşağıdaki bileşenlerin tüm temel ikililerini ve toplu yükseltme ikili dosyalarını içerir: 
- InMage_ScoutCloud_RX_ 8.0.1.0 _RHEL6-64_GA_02Mar2015. tar. gz
- RX_ 8.0.7.0 _GA_Update_7_2965621_28Dec18. tar. gz
- InMage_CX_ 8.0.1.0 _Windows_GA_26Feb2015_release. exe
- InMage_CX_TP_ 8.0.1.0 _Windows_GA_26Feb2015_release. exe
- CX_Windows_ 8.0.7.0 _GA_Update_7_2965621_28Dec18. exe
- InMage_PI_ 8.0.1.0 _Windows_GA_26Feb2015_release. exe
- InMage_Scout_vContinuum_MT_ 8.0.7.0 _Windows_GA_27Dec2018_release. exe
- InMage_UA_ 8.0.7.0 _Windows_GA_27Dec2018_release. exe
- InMage_UA_ 8.0.7.0 _OL5-32_GA_03Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _OL5-64_GA_03Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _OL6-32_GA_03Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _OL6-64_GA_03Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _RHEL5-32_GA_03Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _RHEL5-64_GA_03Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _RHEL6-32_GA_03Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _RHEL6-64_GA_03Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _RHEL7-64_GA_03Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _SLES10-32_GA_03Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _SLES10-64_GA_03Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _SLES10-SP1-32_GA_03Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _SLES10-SP1-64_GA_03Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _SLES10-SP2-32_GA_03Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _SLES10-SP2-64_GA_03Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _SLES10-SP3-32_GA_03Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _SLES10-SP3-64_GA_03Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _SLES10-SP4-32_GA_03Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _SLES10-SP4-64_GA_03Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _SLES11-32_GA_03Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _SLES11-64_GA_04Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _SLES11-SP1-32_GA_03Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _SLES11-SP1-64_GA_04Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _SLES11-SP2-32_GA_03Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _SLES11-SP2-64_GA_03Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _SLES11-SP3-32_GA_03Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _SLES11-SP3-64_GA_03Dec2018_release. tar. gz
- InMage_UA_ 8.0.7.0 _SLES11-SP4-64_GA_03Dec2018_release. tar. gz
  1. . Zip dosyalarını ayıklayın.
  2. **RX sunucusu**: **Rx_ 8.0.7.0 _GA_Update_7_2965621_28Dec18. tar. gz** dosyasını RX sunucusuna kopyalayın ve ayıklayın. Ayıklanan klasörde, **/install**' i çalıştırın.
  3. **Yapılandırma sunucusu ve işlem sunucusu**: **Cx_windows_ 8.0.7.0 _GA_Update_7_2965621_28Dec18. exe** ' yi yapılandırma sunucusu ve işlem sunucusuna kopyalayın. Çalıştırmak için çift tıklayın.<br>
  4. **Windows ana hedef sunucusu**: Birleşik aracıyı güncelleştirmek için **inmage_ua_ 8.0.7.0 _windows_ga_27dec2018_release. exe dosyasını** sunucuya kopyalayın. Çalıştırmak için çift tıklayın. Aynı dosya aynı zamanda yeni yükleme için de kullanılabilir. Aynı Birleşik aracı güncelleştirmesi aynı zamanda kaynak sunucu için de geçerlidir.
  Güncelleştirme, **inmage_scout_vcontinuum_mt_ 8.0.7.0 _windows_ga_27dec2018_release. exe** Ile hazırlanan ana hedefte uygulanması gerekmez, çünkü bu yeni GA yükleyicisinden en son değişikliklerle birlikte geçerlidir.
  5. **Vcontinuum sunucusu**: **inmage_scout_vcontinuum_mt_ 8.0.7.0 _windows_ga_27dec2018_release. exe dosyasını** sunucuya kopyalayın.  VContinuum Sihirbazı 'nı kapattığınızdan emin olun. Dosyayı çalıştırmak için çift tıklayın.
  6. **Linux ana hedef sunucusu**: Birleşik aracıyı güncelleştirmek için **inmage_ua_ 8.0.7.0 _rhel6-64_ga_03dec2018_release. tar. gz** dosyasını Linux ana hedef sunucusuna kopyalayın ve ayıklayın. Ayıklanan klasörde, **/install**' i çalıştırın.
  7. **Windows kaynak sunucusu**: Birleşik aracıyı güncelleştirmek için **inmage_ua_ 8.0.7.0 _windows_ga_27dec2018_release. exe dosyasını** kaynak sunucuya kopyalayın. Dosyayı çalıştırmak için çift tıklayın. 
  8. **Linux kaynak sunucusu**: Birleşik aracıyı güncelleştirmek için, Birleşik aracı dosyasının Ilgili sürümünü Linux sunucusuna kopyalayın ve ayıklayın. Ayıklanan klasörde, **/install**' i çalıştırın.  Örnek: RHEL 6,7 64-bit sunucu Için, **inmage_ua_ 8.0.7.0 _rhel6-64_ga_03dec2018_release. tar. gz** dosyasını sunucuya kopyalayın ve ayıklayın. Ayıklanan klasörde, **/install**' i çalıştırın.
  9. Yapılandırma sunucusunu, Işlem sunucusunu ve RX sunucusunu yukarıda bahsedilen yükleyicilerle yükselttikten sonra, PHP ve MySQL kitaplıklarının [hızlı yükleme kılavuzunun](https://aka.ms/asr-scout-quick-install-guide)7,4 bölümünde bahsedilen adımlarla el ile yükseltilmesi gerekir.

## <a name="enable-replication"></a>Çoğaltmayı etkinleştirme

1. Kaynak ve hedef VMware siteleri arasında çoğaltmayı ayarlayın.
2. Yükleme, koruma ve kurtarma hakkında daha fazla bilgi edinmek için aşağıdaki belgelere bakın:

   * [Sürüm notları](https://aka.ms/asr-scout-release-notes)
   * [Uyumluluk matrisi](https://aka.ms/asr-scout-cm)
   * [Kullanıcı Kılavuzu](https://aka.ms/asr-scout-user-guide)
   * [RX Kullanıcı Kılavuzu](https://aka.ms/asr-scout-rx-user-guide)
   * [Hızlı Yükleme Kılavuzu](https://aka.ms/asr-scout-quick-install-guide)
   * [MYSQL ve PHP kitaplıklarını yükseltme](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade)

## <a name="updates"></a>Güncelleştirmeler

### <a name="site-recovery-scout-801-update-7"></a>Site Recovery Scout 8.0.1 güncelleştirme 7 
Güncelleştirme tarihi: 31 Aralık 2018, [Scout güncelleştirme 7 ' yi](https://aka.ms/asr-scout-update7)indirin.
Scout Update 7, yeni yükleme için kullanılabilen ve önceki güncelleştirmelerde (güncelleştirme 1 ' den güncelleştirme 6 ' ya kadar) var olan aracıları/MT 'yi yükseltmek için kullanılabilecek tam bir yükleyicidir. Güncelleştirme 1 ' den güncelleştirme 6 ' ya ve aşağıda açıklanan yeni düzeltmeleri ve geliştirmeleri içeren tüm düzeltmeleri içerir.
 
#### <a name="new-features"></a>Yeni özellikler
* PCI uyumluluğu
* TLS v 1.2 desteği

#### <a name="bug-and-security-fixes"></a>Hata ve güvenlik düzeltmeleri
* Düzeltildi: kurtarma/DR-detaylandırma sonrasında Windows kümesi/bağımsız makineler yanlış IP yapılandırmasına sahip.
* Düzeltildi: bazen V2V kümesi için disk ekleme işlemi başarısız oluyor.
* Düzeltildi: Ana hedef Windows Server 2016 ise kurtarma aşamasında vContinuum Sihirbazı takıldığında
* Düzeltildi: MySQL 'in 5.7.23 sürümüne yükseltilmesi için MySQL güvenlik sorunları azaltıldığında

#### <a name="manual-upgrade-for-php-and-mysql-on-csps-and-rx"></a>CS, PS ve RX üzerinde PHP ve MySQL için el ile yükseltme
PHP betik platformu, yapılandırma sunucusu, Işlem sunucusu ve RX sunucusu üzerinde 7.2.10 sürümüne yükseltilmelidir.
MySQL veritabanı yönetim sistemi, yapılandırma sunucusu, Işlem sunucusu ve RX sunucusu üzerinde 5.7.23 sürümüne yükseltilmelidir.
PHP ve MySQL sürümlerini yükseltmek için lütfen [hızlı yükleme kılavuzunda](https://aka.ms/asr-scout-quick-install-guide) verilen el ile adımları izleyin.

### <a name="site-recovery-scout-801-update-6"></a>Site Recovery Scout 8.0.1 güncelleştirme 6 
Güncelleştirilme tarihi: 12 Ekim 2017

[Scout güncelleştirme 6 ' yı](https://aka.ms/asr-scout-update6)indirin.

Scout Update 6, toplu bir güncelleştirmedir. Güncelleştirme 1 ' den güncelleştirme 5 ' e kadar olan tüm düzeltmeleri ve aşağıda açıklanan yeni düzeltmeleri ve geliştirmeleri içerir. 

#### <a name="new-platform-support"></a>Yeni platform desteği
* Kaynak Windows Server 2016 için destek eklendi
* Aşağıdaki Linux işletim sistemleri için destek eklenmiştir:
    - Red Hat Enterprise Linux (RHEL) 6,9
    - CentOS 6,9
    - Oracle Linux 5,11
    - Oracle Linux 6,8
* VMware Center 6,5 için destek eklendi

Güncelleştirmeleri şu şekilde yükler:

> [!NOTE]
>Tüm Scout bileşenlerinin dosya güncelleştirme sürümü Update. zip dosyasında aynı olmayabilir. Eski sürüm, bu güncelleştirme için önceki güncelleştirmeden bu yana bileşende değişiklik olmadığını gösterir.

[Update](https://aka.ms/asr-scout-update6) . zip dosyasını indirin. Dosya aşağıdaki bileşenleri içerir: 
- RX_ 8.0.4.0 _GA_Update_4_8725872_16Sep16. tar. gz
- CX_Windows_ 8.0.6.0 _GA_Update_6_13746667_18Sep17. exe
- UA_Windows_ 8.0.5.0 _GA_Update_5_11525802_20Apr17. exe
- UA_RHEL6-64_ 8.0.4.0 _GA_Update_4_9035261_26Sep16. tar. gz
- vCon_Windows_ 8.0.6.0 _GA_Update_6_11525767_21Sep17. exe
- RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_\<Linux OS > _ 8.0.4.0 _GA_Update_4_9035261_26Sep16. tar. gz için UA Update4 bitleri
  1. . Zip dosyalarını ayıklayın.
  2. **RX sunucusu**: **Rx_ 8.0.4.0 _GA_Update_4_8725872_16Sep16. tar. gz** dosyasını RX sunucusuna kopyalayın ve ayıklayın. Ayıklanan klasörde, **/install**' i çalıştırın.
  3. **Yapılandırma sunucusu ve işlem sunucusu**: **Cx_windows_ 8.0.6.0 _GA_Update_6_13746667_18Sep17. exe** ' yi yapılandırma sunucusu ve işlem sunucusuna kopyalayın. Çalıştırmak için çift tıklayın.<br>
  4. **Windows ana hedef sunucusu**: Birleşik aracıyı güncelleştirmek için **ua_windows_ 8.0.5.0 _ga_update_5_11525802_20apr17. exe dosyasını** sunucuya kopyalayın. Çalıştırmak için çift tıklayın. Aynı Birleşik aracı güncelleştirmesi aynı zamanda kaynak sunucu için de geçerlidir. Kaynak güncelleştirme 4 ' e güncellenmemişse, Birleşik aracıyı güncelleştirmeniz gerekir.
  Güncelleştirme, **inmage_scout_vcontinuum_mt_ 8.0.1.0 _Windows_GA_10Oct2017_release. exe** Ile hazırlanan ana hedefe uygulanmamalıdır çünkü bu, en son değişikliklerle yenı bir GA yükleyicidir.
  5. **Vcontinuum Server**: **Vcon_windows_ 8.0.6.0 _GA_Update_6_11525767_21Sep17. exe** ' yi sunucuya kopyalayın.  VContinuum Sihirbazı 'nı kapattığınızdan emin olun. Dosyayı çalıştırmak için çift tıklayın.
  Güncelleştirme, **inmage_scout_vcontinuum_mt_ 8.0.1.0 _Windows_GA_10Oct2017_release. exe** Ile hazırlanan ana hedefe uygulanmamalıdır çünkü bu, en son değişikliklerle yenı bir GA yükleyicidir.
  6. **Linux ana hedef sunucusu**: Birleşik aracıyı güncelleştirmek için **ua_rhel6-64_ 8.0.4.0 _ga_update_4_9035261_26sep16. tar. gz** dosyasını ana hedef sunucuya kopyalayın ve ayıklayın. Ayıklanan klasörde, **/install**' i çalıştırın.
  7. **Windows kaynak sunucusu**: Birleşik aracıyı güncelleştirmek için **ua_windows_ 8.0.5.0 _ga_update_5_11525802_20apr17. exe dosyasını** kaynak sunucuya kopyalayın. Dosyayı çalıştırmak için çift tıklayın. 
  Güncelleştirme 5 aracısını güncelleştirme 4 ' e zaten güncelleştirilmiş veya kaynak Aracısı en son temel yükleyici **inmage_ua_ 8.0.1.0 _Windows_GA_28Sep2017_release. exe**ile yüklenmişse kaynak sunucuya yüklemeniz gerekmez.
  8. **Linux kaynak sunucusu**: Birleşik aracıyı güncelleştirmek için, Birleşik aracı dosyasının Ilgili sürümünü Linux sunucusuna kopyalayın ve ayıklayın. Ayıklanan klasörde, **/install**' i çalıştırın.  Örnek: RHEL 6,7 64-bit sunucu Için, **ua_rhel6-64_ 8.0.4.0 _ga_update_4_9035261_26sep16. tar. gz** dosyasını sunucuya kopyalayın ve ayıklayın. Ayıklanan klasörde, **/install**' i çalıştırın.


> [!NOTE]
> * Windows için temel Birleşik Aracı (UA) yükleyicisi Windows Server 2016 ' i destekleyecek şekilde yenilendi. Yeni yükleyici **inmage_ua_ 8.0.1.0 _Windows_GA_28Sep2017_release. exe** taban Scout GA paketiyle (**inmage_scout_standard_ 8.0.1 Ga-Oct17. zip**) paketlenir. Desteklenen tüm Windows sürümleri için aynı yükleyici kullanılacaktır. 
> * Windows Server 2016 ' i desteklemek için temel Windows vContinuum & Ana hedef yükleyicisi yenilendi. Yeni yükleyici **inmage_scout_vcontinuum_mt_ 8.0.1.0 _Windows_GA_10Oct2017_release. exe** taban Scout GA paketiyle (**inmage_scout_standard_ 8.0.1 Ga-Oct17. zip**) paketlenir. Aynı yükleyici, Windows 2016 ana hedefini ve Windows 2012R2 ana hedefini dağıtmak için kullanılacaktır.
> * Fiziksel sunucuda Windows Server 2016, ASR Scout tarafından desteklenmez. Yalnızca Windows Server 2016 VMware VM 'yi destekler. 
>

#### <a name="bug-fixes-and-enhancements"></a>Hata düzeltmeleri ve geliştirmeleri
- Yapılandırma sonunda, çoğaltılacak disklerin listesi ile Linux VM için yeniden çalışma koruması başarısız oluyor.

### <a name="site-recovery-scout-801-update-5"></a>Site Recovery Scout 8.0.1 güncelleştirme 5
Scout güncelleştirme 5, toplu bir güncelleştirmedir. Güncelleştirme 1 ' den güncelleştirme 4 ' e yapılan tüm düzeltmeleri ve aşağıda açıklanan yeni düzeltmeleri içerir.
- Site Recovery Scout Update 4 ' ten güncelleştirme 5 ' e yönelik düzeltmeler özellikle ana hedef ve vContinuum bileşenleri içindir.
- Kaynak sunucular, ana hedef, yapılandırma, işlem ve RX sunucuları zaten güncelleştirme 4 ' ü çalıştırıyorsa, bunu yalnızca ana hedef sunucuda uygulayın. 

#### <a name="new-platform-support"></a>Yeni platform desteği
* SUSE Linux Enterprise Server 11 Service Pack 4 (SP4)
* SLES 11 SP4 64 bit **inmage_ua_ 8.0.1.0 _sles11-SP4-64_ga_13apr2017_release. tar. gz** , taban Scout GA paketiyle (**inmage_scout_standard_ 8.0.1 ga. zip**) paketlenir. Kasa oluşturma bölümünde açıklandığı gibi, portaldan GA paketini indirin.


#### <a name="bug-fixes-and-enhancements"></a>Hata düzeltmeleri ve geliştirmeleri

* Artırılmış Windows kümesi desteği güvenilirliğini Düzeltme:
    * Fixed-bazı P2V MSCS küme diskleri kurtarma sonrasında ham haline gelir.
    * Sabit P2V MSCS küme kurtarması, bir disk sırası uyumsuzluğu nedeniyle başarısız oluyor.
    * Düzeltildi-disk eklemeye yönelik MSCS kümesi işlemi disk boyutu uyuşmazlığı hatasıyla başarısız oluyor.
    * Düzeltildi-RDM LUN 'Ları ile kaynak MSCS kümesi için hazır olma denetimi boyut doğrulamasında başarısız olur.
    * SCSI uyumsuzluğu sorunu nedeniyle sabit tek düğümlü küme koruması başarısız oluyor. 
    * Hedef küme diskleri varsa P2V Windows küme sunucusu 'nun sabit yeniden korunması başarısız olur. 
    
* Düzeltildi: yeniden çalışma koruması sırasında, seçili ana hedef sunucu korunan kaynak makineyle aynı ESXi sunucusunda değilse (ileriye doğru koruma sırasında), yeniden çalışma kurtarması sırasında vContinuum yanlış ana hedef sunucusunu seçer ve kurtarma işlem başarısız oldu.

> [!NOTE]
> * P2V küme düzeltmeleri yalnızca Site Recovery Scout güncelleştirme 5 ile yeni korunan fiziksel MSCS kümeleri için geçerlidir. Daha eski güncelleştirmelerle korunan P2V MSCS kümelerine küme düzeltmeleri yüklemek için [Site Recovery Scout sürüm notlarının](https://aka.ms/asr-scout-release-notes)12. bölümünde bahsedilen yükseltme adımlarını izleyin.
> * yeniden koruma sırasında, her bir küme düğümünün başlangıçta korunduğu sırada aynı disk kümesi etkindir, ardından fiziksel bir MSCS kümesinin yeniden korunması yalnızca var olan hedef diskleri yeniden kullanabilir. Aksi takdirde, yeniden koruma sırasında yeniden kullanmak üzere hedef tarafı diskleri doğru veri deposu yoluna taşımak için [Site Recovery Scout sürüm notlarındaki](https://aka.ms/asr-scout-release-notes)12. bölümde el ile gerçekleştirilen adımları kullanın. Yükseltme adımlarını izleyerek MSCS kümesini P2V modunda yeniden koruyorduğunuzda, hedef ESXi sunucusunda yeni bir disk oluşturur. Eski diskleri veri deposundan el ile silmeniz gerekir.
> * Bir kaynak SLES11 veya SLES11 (herhangi bir hizmet paketi ile) sunucusu düzgün şekilde yeniden başlatıldığında, yeniden eşitleme için **kök** disk çoğaltma çiftlerini el ile işaretleyin. CX arabiriminde bildirim yok. Kök diski yeniden eşitleme için işaretlememezseniz, veri bütünlüğü sorunları fark edebilirsiniz.


### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 güncelleştirme 4
Scout Update 4, toplu bir güncelleştirmedir. Güncelleştirme 1 ' den güncelleştirme 3 ' e yapılan tüm düzeltmeleri ve aşağıda açıklanan yeni düzeltmeleri içerir.

#### <a name="new-platform-support"></a>Yeni platform desteği

* VCenter/vSphere 6,0, 6,1 ve 6,2 için destek eklendi
* Bu Linux işletim sistemleri için destek eklenmiştir:
  * Red Hat Enterprise Linux (RHEL) 7,0, 7,1 ve 7,2
  * CentOS 7,0, 7,1 ve 7,2
  * Red Hat Enterprise Linux (RHEL) 6,8
  * CentOS 6,8

> [!NOTE]
> RHEL/CentOS 7 64 bit **inmage_ua_ 8.0.1.0 _RHEL7-64_GA_06Oct2016_release. tar. gz** , taban Scout GA Package **inmage_scout_standard_ 8.0.1 ga. zip**ile paketlenmiştir. Kasa oluşturma bölümünde açıklandığı şekilde portaldan Scout GA paketini indirin.

#### <a name="bug-fixes-and-enhancements"></a>Hata düzeltmeleri ve geliştirmeleri

* Aşağıdaki Linux işletim sistemleri ve klonları için, istenmeyen yeniden eşitleme sorunlarını engellemek için geliştirilmiş kapalı bir işlem:
    * Red Hat Enterprise Linux (RHEL) 6. x
    * Oracle Linux (OL) 6. x
* Linux için, Birleşik aracı yükleme dizinindeki tüm klasör erişim izinleri artık yalnızca yerel kullanıcıyla kısıtlıdır.
* Windows 'ta, SQL Server ve paylaşma noktası kümeleri gibi çok sayıda yüklenmiş dağıtılmış uygulamalarda ortak dağıtılmış tutarlılık yer işaretleri verirken oluşan zaman aşımı sorununa yönelik bir çözüm.
* Yapılandırma sunucusu temel yükleyicisinde günlüğe kaydetme ile ilgili bir çözüm.
* VMware Vclı 6,0 'e yönelik bir indirme bağlantısı Windows ana hedef temel yükleyicisine eklendi.
* Yük devretme ve olağanüstü durum kurtarma ile ilgili ağ yapılandırması değişiklikleri için ek denetimler ve Günlükler eklenmiştir.
* Saklama bilgilerinin yapılandırma sunucusuna raporlanmamasına neden olan bir sorun için bir çözüm.  
* Fiziksel kümeler için, birim yeniden boyutlandırmasının, kaynak birimi daraltılması sırasında vContinuum sihirbazında başarısız olmasına neden olan bir sorun için bir çözüm.
* Küme koruma sorununa yönelik bir sorun oluştu; bu, küme diski bir PRDM diski olduğunda "disk imzası bulunamadı" hatasıyla başarısız oldu.
* Bir cxps aktarım sunucusu kilitlenmesi için, Aralık dışı bir özel durum nedeniyle oluşan bir çözüm.
* Sunucu adı ve IP adresi sütunları artık vContinuum sihirbazının **anında yükleme** sayfasında yeniden boyutlandırılabilir.
* RX API geliştirmeleri:
  * Kullanılabilir en son kullanılan beş ortak tutarlılık noktası artık kullanılabilir (yalnızca garantili Etiketler).
  * Tüm korumalı cihazlar için kapasite ve boş alan ayrıntıları görüntülenir.
  * Kaynak sunucudaki Scout sürücü durumu kullanılabilir.

> [!NOTE]
> * **İnmage_scout_standard_ 8.0.1 _GA. zip** temel paketi:
>     * Güncelleştirilmiş bir Configuration Server temel yükleyicisi (**inmage_cx_ 8.0.1.0 _windows_ga_26feb2015_release. exe**)
>     * Bir Windows ana hedef temel yükleyicisi (**inmage_scout_vcontinuum_mt_ 8.0.1.0 _windows_ga_26feb2015_release. exe**).
>     * Tüm yeni yüklemeler için yeni yapılandırma sunucusunu ve Windows ana hedef GA bitlerini kullanın.
> * Güncelleştirme 4, doğrudan 8.0.1 GA üzerinde uygulanabilir.
> * Yapılandırma sunucusu ve RX güncelleştirmeleri uygulandıktan sonra geri alınamaz.


### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 güncelleştirme 3

Tüm Site Recovery güncelleştirmeleri birikimlidir. Güncelleştirme 3, güncelleştirme 1 ve güncelleştirme 2 ' deki tüm düzeltmeleri içerir. Güncelleştirme 3, 8.0.1 GA üzerinde doğrudan uygulanabilir. Yapılandırma sunucusu ve RX güncelleştirmeleri uygulandıktan sonra geri alınamaz.

#### <a name="bug-fixes-and-enhancements"></a>Hata düzeltmeleri ve geliştirmeleri
Güncelleştirme 3 aşağıdaki sorunları düzeltir:

* Yapılandırma sunucusu ve RX, proxy 'nin arkasında olduklarında kasada kayıtlı değildir.
* Kurtarma noktası hedefi 'nin (RPO) ulaştığı saat sayısı, sistem durumu raporunda güncelleştirilmedi.
* ESX donanım ayrıntıları veya ağ ayrıntıları, herhangi bir UTF-8 karakteri içerdiğinde yapılandırma sunucusu RX ile eşitlenmiyor.
* Windows Server 2008 R2 etki alanı denetleyicileri kurtarma sonrasında başlatılmaz.
* Çevrimdışı eşitleme beklendiği gibi çalışmıyor.
* VM yük devretmesinin ardından, çoğaltma çifti silme işlemi, yapılandırma sunucusu konsolunda uzun süredir ilermez. Kullanıcılar yeniden çalışma veya işleme işlemlerini tamamlayamıyor.
* Tutarlılık işinin genel anlık görüntü işlemleri, SQL Server istemcileri gibi uygulama bağlantısının kesilmesini azaltmaya yardımcı olmak için iyileştirildi.
* Tutarlılık Aracı (açık p. exe) performansı geliştirildi. Windows üzerinde anlık görüntü oluşturmak için gereken bellek kullanımı azaltılmıştır.
* Parola 16 karakterden daha büyükse gönderim yükleme hizmeti çöküyor.
* kimlik bilgileri değiştirildiğinde vContinuum yeni vCenter kimlik bilgilerini denetlemez ve istemez.
* Linux 'ta, ana hedef önbellek Yöneticisi (cachemgr), dosyaları işlem sunucusundan indirilemiyor. Bu, çoğaltma çiftinin azaltılmasına neden olur.
* Fiziksel yük devretme kümesi (MSCS) disk sırası tüm düğümlerde aynı olmadığında, bazı küme birimleri için çoğaltma ayarlı değildir. Bu düzeltmeden faydalanmak için kümenin yeniden korunması gerekir.  
* RX, Scout 7,1 ' den Scout 8.0.1 sürümüne yükseltildikten sonra, SMTP işlevselliği beklendiği gibi çalışmıyor.
* Geri alma işlemi için günlükte daha fazla istatistik eklenmiştir, bu da tamamlanma süresini takip ediyor.
* Kaynak sunucuda Linux işletim sistemleri için destek eklenmiştir:
  * Red Hat Enterprise Linux (RHEL) 6 güncelleştirme 7
  * CentOS 6 güncelleştirme 7
* Yapılandırma sunucusu ve RX konsolları artık çift için bit eşlem moduna geçen bildirimleri gösterir.
* Aşağıdaki güvenlik düzeltmeleri RX 'ye eklenmiştir:
    * Parametre değişikliği aracılığıyla yetkilendirme atlama: geçerli olmayan kullanıcılara kısıtlı erişim.
    * Çapraz site isteği forgery: sayfa belirteci kavramı uygulandı ve her sayfa için rastgele bir üretir. Bu, aynı kullanıcı için yalnızca bir çoklu oturum açma örneği olduğu anlamına gelir ve sayfa yenileme çalışmaz. Bunun yerine, panoya yeniden yönlendirir.
    * Kötü amaçlı dosya yükleme: dosyalar belirli uzantılarla kısıtlıdır: z, AIFF, ASF, avi, BMP, CSV, Doc, docx, FLA, FLV, gif, GZ, gzip, JPEG, jpg, log, Mid, MOV, MP3, MP4, MPC, MPEG, MPG, ODS, ODT, , sdc, SITD, SWF, SXC, SXW, tar, tgz, tif, TIFF, txt, VSD, WAV, WMA, WMV, xls, xlsx, XML ve zip.
    * Kalıcı siteler arası betik oluşturma: giriş doğrulamaları eklendi.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 güncelleştirme 2 (güncelleştirme 03Dec15)

Güncelleştirme 2 ' deki düzeltmeler şunlardır:

* **Yapılandırma sunucusu**: yapılandırma sunucusu Azure Site Recovery kasaya kaydedildiğinde, 31 günlük ücretsiz ölçüm özelliğinin beklendiği gibi çalışmasını engelleyen sorunlar.
* **Birleşik aracı**: 8,0 sürümünden 8.0.1 sürümüne yükseltme sırasında güncelleştirme 1 ' de güncelleştirmenin ana hedef sunucuda yüklü olmayan bir sorunu giderme.

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 Update 1
Güncelleştirme 1 aşağıdaki hata düzeltmelerini ve yeni özellikleri içerir:

* sunucu örneği başına 31 günlük ücretsiz koruma. Bu, işlevselliği test etmenizi veya kavram kanıtı ayarlamanıza olanak sağlar.
* Sunucu üzerindeki tüm işlemler, yük devretme ve yeniden çalışma dahil, ilk 31 gün boyunca ücretsizdir. Süre, bir sunucu ilk olarak Site Recovery Scout ile korunduğunda başlar. Her korunan sunucu, bir müşterinin sahip olduğu bir siteye Site Recovery koruma için standart örnek fiyatı üzerinden ücretlendirilir.
* Herhangi bir zamanda, şu anda ücretlendirilen korumalı sunucu sayısı kasadaki **panoda** kullanılabilir.
* VSphere komut satırı arabirimi (Vclı) 5,5 güncelleştirme 2 için destek eklendi.
* Kaynak sunucuda bu Linux işletim sistemleri için destek eklendi:
    * RHEL 6 güncelleştirme 6
    * RHEL 5 güncelleştirme 11
    * CentOS 6 güncelleştirme 6
    * CentOS 5 güncelleştirme 11
* Aşağıdaki sorunları gidermek için hata düzeltmeleri:
  * Yapılandırma sunucusu veya RX sunucusu için kasa kaydı başarısız oluyor.
  * Kümelenmiş VM 'Ler sürdürüldiklerinde yeniden korunduğunda, küme birimleri beklendiği gibi görünmez.
  * Ana hedef sunucu, şirket içi üretim sanal makinelerinden farklı bir ESXi sunucusunda barındırıldığında yeniden çalışma başarısız olur.
  * 8\.0.1 sürümüne yükselttiğinizde yapılandırma dosyası izinleri değiştirilir. Bu değişiklik koruma ve işlemleri etkiler.
  * Yeniden eşitleme eşiği beklenen şekilde zorlanmaz, ancak tutarsız çoğaltma davranışına neden olur.
  * RPO ayarları yapılandırma sunucusu konsolunda doğru görünmüyor. Sıkıştırılmamış veri değeri hatalı biçimde sıkıştırılmış değeri gösterir.
  * Kaldırma işlemi vContinuum sihirbazında beklendiği gibi silinmez ve çoğaltma, yapılandırma sunucusu konsolundan silinmez.
  * VContinuum sihirbazında, MSCS VM 'lerinin korunması sırasında disk görünümündeki **Ayrıntılar** ' a tıkladığınızda disk otomatik olarak seçili değildir.
  * Fizikselden uca (P2V) senaryosunda, gerekli HP Hizmetleri (CIMnotify ve CqMgHost gibi) VM kurtarmasında el ile taşınmaz. Bu sorun, ek önyükleme zamanına neden olur.
  * Ana hedef sunucuda 26 ' dan fazla disk olduğunda Linux VM koruması başarısız olur.

