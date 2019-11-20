---
title: Azure Site Recovery güncelleştirmeler ve bileşen yükseltmeleri
description: Azure Site Recovery hizmet güncelleştirmelerine ve bileşen yükseltmelerine genel bakış sunar.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: rajanaki
ms.openlocfilehash: 3489f7f812798504d0c57a265a04e57344105419
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910468"
---
# <a name="service-updates-in-site-recovery"></a>Site Recovery hizmet güncelleştirmeleri

Bu makalede [Azure Site Recovery](site-recovery-overview.md) güncelleştirmelere genel bir bakış sağlanır ve Site Recovery bileşenlerinin nasıl yükseltileceğini açıklar.

Site Recovery, hizmet güncelleştirmelerini düzenli aralıklarla yayımlar. Güncelleştirmeler yeni özellikler, destek iyileştirmeleri, bileşen güncelleştirmeleri ve hata düzeltmeleri içerir. En son özellikler ve düzeltmelerinden yararlanmak için Site Recovery bileşenlerinin en son sürümlerini çalıştırmayı öneririz. 
 
 
## <a name="updates-support"></a>Güncelleştirme desteği

### <a name="support-statement-for-azure-site-recovery"></a>Azure Site Recovery için destek açıklaması

En son bileşen sürümlerine her zaman yükseltmeniz önerilir:

**Yayınlanan bir Azure Site Recovery bileşenin her yeni sürümü ' n ' ile, ' n-4 ' in altındaki tüm sürümler destek dışı olarak kabul**edilir. 

> [!IMPORTANT]
> Resmi destek, > N-4 sürümünden N sürümüne yükseltilme içindir. Örneğin, N-6 ' d e çalıştırıyorsanız, önce n-4 ' e yükseltmeniz ve ardından N ' ye yükseltmeniz gerekir.


### <a name="links-to-currently-supported-update-rollups"></a>Şu anda desteklenen güncelleştirme paketlerinin bağlantıları

 [Bu makaledeki](site-recovery-whats-new.md)en son güncelleştirme paketini (sürüm N) gözden geçirin. Site Recovery N-4 sürümleri için destek sağladığını unutmayın.



## <a name="component-expiry"></a>Bileşen süre sonu

Site Recovery süresi geçen bileşenleri (veya e-posta bildirimlerine abone olduğunuzda) e-posta ile veya portaldaki kasa panosunda size bildirir.

- Ayrıca, güncelleştirmeler kullanılabilir olduğunda, portalda senaryonuz için altyapı görünümünde, bileşenin yanında bir **güncelleştirme kullanılabilir** düğmesi görünür. Bu düğme sizi en son bileşen sürümünü indirmek için bir bağlantıya yönlendirir.
-  Hyper-V VM 'lerini çoğaltırken kasa panosu bildirimleri kullanılamaz. 

E-posta bildirimleri aşağıdaki şekilde gönderilir.

**saat** | **Sıklık**
--- | ---
bileşen süre sonu 60 gün önce | İki haftada bir
Sonraki 53 gün | Haftada bir kez
Son 7 gün | Günde bir kez
Süre dolduktan sonra | İki haftada bir


### <a name="upgrading-outside-official-support"></a>Resmi destek dışında yükseltme

Bileşen sürümünüz ve en son yayın sürümü arasındaki fark dörtten büyükse bu, destek dışında kabul edilir. Bu durumda, aşağıdaki gibi yükseltin: 

1. Şu anda yüklü olan bileşeni geçerli sürümünüze yükseltin ve dört. Örneğin, sürümünüz 9,16 ise, 9,20 ' e yükseltin.
2. Ardından, bir sonraki uyumlu sürüme yükseltin. Bizim örneğimizde 9,20 9,16 sürümüne yükselttikten sonra 9,24 ' ye yükseltin. 

Tüm ilgili bileşenler için aynı süreci izleyin.

### <a name="support-for-latest-operating-systemskernels"></a>En son işletim sistemleri/çekirdekler için destek

> [!NOTE]
> Zamanlanmış bir bakım pencereniz varsa ve buna bir yeniden başlatma varsa, önce Site Recovery bileşenleri yükseltmenizi ve ardından bakım penceresinde zamanlanan etkinliklerin geri kalanını kullanmaya devam etmenizi öneririz.

1. İşletim sistemi/çekirdek sürümlerini yükseltmeden önce, hedef sürümün desteklenip desteklenmediğini doğrulayın Site Recovery. 

    - [Azure VM](azure-to-azure-support-matrix.md#replicated-machine-operating-systems) desteği.
    - [VMware/fiziksel sunucu](vmware-physical-azure-support-matrix.md#replicated-machines) desteği
    - [Hyper-V](hyper-v-azure-support-matrix.md#replicated-vms) desteği.
2. Ne yükseltmek istediğinizi öğrenmek için [kullanılabilir güncelleştirmeleri](site-recovery-whats-new.md) gözden geçirin.
3. En son Site Recovery sürümüne yükseltin.
4. İşletim sistemini/çekirdeğini gerekli sürümlere yükseltin.
5. Yeniden başlatma.


Bu işlem, makine işletim sisteminin/çekirdeğin en son sürüme yükseltilmesini ve yeni sürümü desteklemek için gereken en son Site Recovery değişikliklerinin makineye yüklenmesini sağlar.

## <a name="azure-vm-disaster-recovery-to-azure"></a>Azure'a Azure VM olağanüstü durum kurtarma

Bu senaryoda, [otomatik güncelleştirmeleri etkinleştirmenizi](azure-to-azure-autoupdate.md)kesinlikle öneririz. Site Recovery güncelleştirmeleri şu şekilde yönetmesine izin verebilirsiniz:

- Çoğaltma işlemini etkinleştirin.
- Kasa içindeki uzantı güncelleştirme ayarlarını ayarlayarak.

Güncelleştirmeleri el ile yönetmek istiyorsanız aşağıdakileri yapın:

1. Kasaya **çoğaltılan öğeleri**>, ekranın en üstündeki bu bildirime tıklayın: 
    
    **Yeni Site Recovery çoğaltma Aracısı güncelleştirmesi var. Yüklemek için tıklayın->**

4. Güncelleştirmeyi uygulamak istediğiniz VM 'Leri seçin ve ardından **Tamam**' a tıklayın.


## <a name="vmware-vmphysical-server-disaster-recovery-to-azure"></a>Azure 'da VMware VM/fiziksel sunucu olağanüstü durum kurtarma

1. Güncel sürümünüzü ve [destek bildirisini](#support-statement-for-azure-site-recovery)temel alarak, [Bu yönergeleri](vmware-azure-deploy-configuration-server.md#upgrade-the-configuration-server)kullanarak güncelleştirmeyi önce şirket içi yapılandırma sunucusuna yüklemeniz gerekir. 
2. Genişleme işlem sunucularınız varsa, [Bu yönergeleri](vmware-azure-manage-process-server.md#upgrade-a-process-server)kullanarak bunları daha sonra güncelleştirin.
3. Korunan her makinede Mobility Aracısı 'nı güncelleştirmek için [Bu](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) makaleye bakın.

### <a name="reboot-after-mobility-service-upgrade"></a>Mobility hizmeti yükseltmesinden sonra yeniden Başlat

Tüm son değişikliklerin kaynak makinede yüklü olduğundan emin olmak için Mobility hizmetinin her yükseltildikten sonra yeniden başlatma önerilir.

Son yeniden başlatma sırasında aracı sürümü arasındaki fark ve geçerli sürüm dörtten büyük değilse yeniden başlatma zorunlu değildir.

Tablodaki örnekte bunun nasıl çalıştığı gösterilmektedir.

|**Aracı sürümü (son yeniden başlatma)** | **Sürümüne yükselt** | **Zorunlu önyükleme yapılsın mı?**|
|---------|---------|---------|
|9,16 |  9,18 | Zorunlu değil|
|9,16 | 9,19 | Zorunlu değil|
| 9,16 | 9,20 | Zorunlu değil
 | 9,16 | 9,21 | Zorunlu.<br/><br/> 9,20 sürümüne yükseltin ve ardından 9,21 ' e yükseltmeden önce yeniden başlatın.

## <a name="hyper-v-vm-disaster-recovery-to-azure"></a>Azure'a Hyper-V VM olağanüstü durum kurtarma

### <a name="between-a-hyper-v-site-and-azure"></a>Hyper-V sitesi ile Azure arasında

1. Microsoft Azure Site Recovery sağlayıcısına yönelik güncelleştirmeyi indirin.
2. Sağlayıcıyı Site Recovery kayıtlı her Hyper-V sunucusuna yükler. Bir küme çalıştırıyorsanız, tüm küme düğümlerinde yükseltme yapın.


## <a name="between-an-on-premises-vmm-site-and-azure"></a>Bir şirket içi VMM sitesi ve Azure arasında
1. Microsoft Azure Site Recovery sağlayıcısına yönelik güncelleştirmeyi indirin.
2. Sağlayıcıyı VMM sunucusuna yükler. VMM bir kümede dağıtılırsa, sağlayıcıyı tüm küme düğümlerine yükler.
3. Tüm Hyper-V konaklarına veya küme düğümlerine en son Microsoft Azure Kurtarma Hizmetleri aracısını yükler.


## <a name="between-two-on-premises-vmm-sites"></a>İki şirket içi VMM sitesi arasında
1. Microsoft Azure Site Recovery sağlayıcısı için en son güncelleştirmeyi indirin.
2. İkincil kurtarma sitesini yöneten VMM sunucusuna en son sağlayıcıyı yükler. VMM bir kümede dağıtılırsa, sağlayıcıyı tüm küme düğümlerine yükler.
3. Kurtarma sitesi güncelleştirildikten sonra, sağlayıcıyı birincil siteyi yöneten VMM sunucusuna yüklersiniz.

## <a name="next-steps"></a>Sonraki adımlar

Yeni güncelleştirmeleri ve yayınları izlemek için [Azure Updates](https://azure.microsoft.com/updates/?product=site-recovery) sayfamızı izleyin.
