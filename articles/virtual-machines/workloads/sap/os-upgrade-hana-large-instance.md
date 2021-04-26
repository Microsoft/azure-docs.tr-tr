---
title: Azure 'daki SAP HANA için işletim sistemi yükseltmesi (büyük örnekler) | Microsoft Docs
description: Azure 'da SAP HANA için Işletim sistemi yükseltmesi gerçekleştirme (büyük örnekler)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 97c07f010fad6c12424b1684d4ff5e12c7cac3ce
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553252"
---
# <a name="operating-system-upgrade"></a>İşletim sistemi yükseltme
Bu belgede, HANA büyük örneklerinde işletim sistemi yükseltmeleriyle ilgili ayrıntılar açıklanmaktadır.

>[!NOTE]
>İşletim sistemi yükseltmesi müşterinin sorumluluğundadır, Microsoft operasyon desteği yükseltme sırasında izlenecek önemli alanlara kılavuzluk edebilir. Bir yükseltmeyi planlayabilmeniz için, işletim sistemi satıcınıza da başvurmanız gerekir.

HLI birim sağlama sırasında, Microsoft operasyon ekibi işletim sistemini de yüklüyor.
Zaman içinde, işletim sistemini (örnek: düzeltme eki uygulama, ayarlama, yükseltme vb.), HLI birim üzerinde korumanız gerekir.

İşletim sisteminde büyük değişiklikler yapmadan önce (örneğin, SP1 'i SP2'YE yükseltmek için), bir destek bileti açarak Microsoft Operasyon ekibine başvurmanız gerekir.

Biletini ekleyin:

* HLI abonelik KIMLIĞINIZ.
* Sunucu adınız.
* Uygulanmasını planladığınız düzeltme eki düzeyi.
* Bu değişikliği planlamanızın tarihi. 

Bu bileti, istenen yükseltmeden önce en az bir hafta önce açmanız önerilir. Bu, takımın istenen bellenim sürümü hakkında bilgi sahibi olduğunu bilmesini sağlar.

Farklı Linux sürümlerindeki farklı SAP HANA sürümlerinin destek matrisi için bkz. [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581).

## <a name="known-issues"></a>Bilinen sorunlar

Yükseltme sırasında yaygın olarak karşılaşılan birkaç sorun aşağıda verilmiştir:
- SKU türü II sınıf SKU 'sunda, yazılım altyapısı yazılımı (SFS) işletim sistemi yükseltmesinden sonra kaldırılır. İşletim sistemi yükseltmesinden sonra uyumlu SFS 'yi yeniden yüklemeniz gerekir.
- Ethernet kartı sürücüleri (ENIC ve FNıC) eski sürüme geri döndürüldü. Yükseltmeden sonra sürücülerin uyumlu sürümünü yeniden yüklemeniz gerekir.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>SAP HANA büyük örnek (tür I) önerilen yapılandırma

Düzeltme eki uygulama, sistem yükseltmeleri ve müşteriler tarafından yapılan değişiklikler nedeniyle, işletim sistemi yapılandırması zaman içinde Önerilen ayarlardan fazla olabilir. Ayrıca, Microsoft, mevcut sistemler için gereken güncelleştirmeleri tanımlar ve en iyi performans ve dayanıklılık için en iyi şekilde yapılandırılmasını sağlar. Aşağıdaki yönergeler ağ performansını, sistem kararlılığını ve en iyi HANA performansını ele alan önerilerin ana hatlarıyla yapılır.

### <a name="compatible-enicfnic-driver-versions"></a>Uyumlu eNIC/Fnıc sürücü sürümleri
  Doğru ağ performansına ve sistem kararlılığını sağlamak için, aşağıdaki uyumluluk tablosunda belirtilen eNIC ve Fnıc sürücülerinin işletim sistemine özgü sürümünün yüklü olduğundan emin olmanız önerilir. Sunucular, uyumlu sürümlere sahip müşterilere dağıtılır. Bazı durumlarda, işletim sistemi/çekirdek düzeltme eki uygulama sırasında sürücüler varsayılan sürücü sürümlerine geri alınabilir. İşletim sistemi/çekirdek düzeltme eki uygulama sonrasında uygun sürücü sürümünün çalıştığından emin olun.
       
      
  |  İşletim sistemi satıcısı    |  İşletim sistemi paketi sürümü     |  Üretici Yazılımı Sürümü  |  eNIC sürücüsü |  Fnıc sürücüsü | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   SuSE        |  SLES 12 SP2            |   3.1.3 h           |  2.3.0.40    |   1.6.0.34   |
  |   SuSE        |  SLES 12 SP3            |   3.1.3 h           |  2.3.0.44    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP2            |   3.2.3 ı           |  2.3.0.45    |   1.6.0.37   |
  |   SuSE        |  SLES 12 SP3            |   3.2.3 ı           |  2.3.0.43    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP4            |   3.2.3 ı           |  4.0.0.6     |   2.0.0.60   |
  |   SuSE        |  SLES 12 SP5            |   4.1.1 b           |  4.0.0.6     |   2.0.0.59   |
  |   SuSE        |  SLES 15 SP1            |   4.1.1 b           |  4.0.0.8     |   2.0.0.60   |
  |   SuSE        |  SLES 15 SP1            |   4.1.1 b           |  4.0.0.8     |   2.0.0.60   |
  |   Red Hat     |  RHEL 7,2               |   3.1.3 h           |  2.3.0.39    |   1.6.0.34   |
  |   Red Hat     |  RHEL 7,6               |   3.2.3 ı           |  3.1.137.5   |   2.0.0.50   |
  |   Red Hat     |  RHEL 7,6               |   4.1.1 b           |  4.0.0.8     |   2.0.0.60   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>Sürücü yükseltme ve eski RPM paketlerini Temizleme komutları

#### <a name="command-to-check-existing-installed-drivers"></a>Mevcut yüklü sürücüleri denetlemek için komut
```
rpm -qa | grep enic/fnic 
```
#### <a name="delete-existing-enicfnic-rpm"></a>Mevcut eNIC/Fnıc RPM 'yi Sil
```
rpm -e <old-rpm-package>
```
#### <a name="install-the-recommended-enicfnic-driver-packages"></a>Önerilen eNIC/Fnıc sürücü paketlerini yükler
```
rpm -ivh <enic/fnic.rpm> 
```

#### <a name="commands-to-confirm-the-installation"></a>Yüklemeyi onaylama komutları
```
modinfo enic
modinfo fnic
```

#### <a name="steps-for-enicfnic-drivers-installation-during-os-upgrade"></a>İşletim sistemi yükseltmesi sırasında eNIC/Fnıc sürücüleri yükleme adımları

* İşletim sistemi sürümünü yükselt
* Eski RPM paketlerini kaldır
* Yüklü işletim sistemi sürümüne göre uyumlu eNIC/Fnıc sürücüleri yükleme
* Sistemi yeniden Başlat
* Yeniden başlatmadan sonra, eNIC/Fnıc sürümünü denetleyin


### <a name="suse-hlis-grub-update-failure"></a>SuSE HLIs GRUB güncelleştirme hatası
Azure HANA büyük örneklerinde (tür ı) SAP, yükseltmeden sonra önyüklenebilir olmayan bir durumda olabilir. Aşağıdaki yordam bu sorunu düzeltir.
#### <a name="execution-steps"></a>Yürütme adımları


*   `multipath -ll`Komutunu yürütün.
*   Boyutu yaklaşık 50G olan LUN KIMLIĞINI alın veya şu komutu kullanın: `fdisk -l | grep mapper`
*   `/etc/default/grub_installdevice`Dosyayı satırla Güncelleştir `/dev/mapper/<LUN ID>` . Örnek:/dev/mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>LUN KIMLIĞI sunucudan sunucuya farklılık gösterir.


### <a name="disable-edac"></a>EDAC 'yi devre dışı bırak 
   Hata algılama ve düzeltme (EDAC) modülü bellek hatalarını algılamada ve düzeltmenize yardımcı olur. Ancak, Azure üzerinde SAP HANA Büyük Örnekleri (tür ı) için temel alınan donanım zaten aynı işlevi gerçekleştiriyor. Aynı özelliğin donanım ve işletim sistemi (OS) düzeylerinde etkinleştirilmiş olması çakışmaya neden olabilir ve sunucunun zaman zaman, planlanmamış kapanmasına yol açabilir. Bu nedenle, modülün IŞLETIM sisteminden devre dışı bırakılması önerilir.

#### <a name="execution-steps"></a>Yürütme adımları

* EDAC modülünün etkinleştirilip etkinleştirilmediğini denetleyin. Aşağıdaki komutta bir çıkış döndürülürse, bu, modülün etkin olduğu anlamına gelir. 
```
lsmod | grep -i edac 
```
* Aşağıdaki satırları dosyaya ekleyerek modülleri devre dışı bırakın `/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
Değişikliklerin yerine gelmesi için yeniden başlatma gerekiyor. `lsmod`Komutunu yürütün ve modülün çıkışta mevcut olmadığını doğrulayın.

### <a name="kernel-parameters"></a>Çekirdek parametreleri
   ,, Ve için doğru ayarların `transparent_hugepage` uygulandığından emin olun `numa_balancing` `processor.max_cstate` `ignore_ce` `intel_idle.max_cstate` .

* intel_idle intel_idle.max_cstate = 1
* processor.max_cstate = 1
* transparent_hugepage = hiçbir bir
* numa_balancing = devre dışı bırak
* MCE = ignore_ce

#### <a name="execution-steps"></a>Yürütme adımları

* Bu parametreleri `GRB_CMDLINE_LINUX` dosyadaki satıra ekleyin `/etc/default/grub`
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* Yeni bir grub dosyası oluşturun.
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* Sistemi yeniden Başlat.


## <a name="next-steps"></a>Sonraki adımlar
- İşletim sistemi yedekleme türü ı SKU sınıfı için [yedekleme ve geri yükleme](hana-overview-high-availability-disaster-recovery.md) bölümüne bakın.
- Tür II SKU sınıfı için [Düzeltme 3 damgalarının tür II SKU 'ları Için Işletim sistemi yedeklemesi](os-backup-type-ii-skus.md) ' ne bakın.
