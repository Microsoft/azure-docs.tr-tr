---
title: Azure Site Recovery 'de Azure 'da Hyper-V VM 'lerinin yük devretmesini ayarlama
description: Hyper-V VM 'lerinin yük devretme Azure Site Recovery ile Azure 'a nasıl yük devredebileceğinizi öğrenin.
ms.service: site-recovery
ms.topic: tutorial
ms.date: 12/16/2019
ms.custom: MVC
ms.openlocfilehash: bb012f7cc5faa5e286b617785512ffa463c213bb
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579536"
---
# <a name="fail-over-hyper-v-vms-to-azure"></a>Hyper-V VM 'lerini Azure 'a devreder

Bu öğreticide, Hyper-V VM 'lerinin [Azure Site Recovery](site-recovery-overview.md)ile Azure 'a yük devretme işlemi açıklanmaktadır. Yük devrettikten sonra şirket içi siteniz kullanılabilir olduğunda yeniden çalıştırabilirsiniz. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure gereksinimleriyle uyumlu olup olmadığını kontrol etmek için Hyper-V VM özelliklerini doğrulayın.
> * Belirli VM 'Lerin yükünü Azure 'a devreder.


Bu öğretici, serideki beşinci öğreticidir. Önceki öğreticilerdeki adımları zaten tamamladığınız varsayılır.    

1. [Azure’u hazırlama](tutorial-prepare-azure.md)
2. [Şirket içi Hyper-V’leri hazırlama](./hyper-v-prepare-on-premises-tutorial.md)
3. [Hyper-V VM'leri](./hyper-v-azure-tutorial.md) veya [System Center VMM bulutlarında yönetilen Hyper-V VM'leri](./hyper-v-vmm-azure-tutorial.md) için olağanüstü durum kurtarmayı ayarlama
4. [Olağanüstü durum kurtarma tatbikatı çalıştırma](tutorial-dr-drill-azure.md)

Farklı yük devretme türleri [hakkında bilgi edinin](failover-failback-overview.md#types-of-failover) . Bir kurtarma planında birden çok VM 'nin yükünü devretmek istiyorsanız [Bu makaleyi](site-recovery-failover.md)gözden geçirin.

## <a name="prepare-for-failover"></a>Yük devretme hazırlığı 
VM'de anlık görüntü olmadığından ve şirket içi VM'nin yeniden çalışma sırasında kapalı olduğundan emin olun. Bu işlem, çoğaltma sırasında veri tutarlığını sağlar. Yeniden çalışma sırasında şirket içi VM'yi açmayın. 

Yük devretme ve yeniden çalışma üç aşamalıdır:

1. **Azure'a yük devretme**: Şirket içi Hyper-V VM'lerinden Azure'a yük devretme gerçekleştirin.
2. **Şirket içinde yeniden çalışma**: Şirket içi site kullanılabilir duruma geldiğinde Azure VM'lerinden şirket içine yük devretme gerçekleştirin. Azure'daki veriler şirket içi siteyle eşitlenmeye başlar ve tamamlandığında şirket içindeki VM'ler açılır.  
3. **Şirket içi VM'lerde tersine çoğaltma**: Şirket içinde yeniden çalışma süreci tamamlandıktan sonra şirket içindeki VM'leri Azure'da çoğaltmaya başlamak için tersine çoğaltma gerçekleştirin.

## <a name="verify-vm-properties"></a>VM özelliklerini doğrulama

Yük devretme öncesinde VM özelliklerini doğrulayın ve VM’nin [Azure gereksinimlerini](hyper-v-azure-support-matrix.md#replicated-vms) karşıladığından emin olun.

**Korunan Öğeler**’de **Çoğaltılan Öğeler** > VM seçeneğine tıklayın.

1. **Çoğaltılan öğe** bölmesinde VM bilgileri ile sistem durumunun bir özeti ve kullanılabilen son kurtarma noktaları yer alır. Daha fazla ayrıntı görüntülemek için **Özellikler**’e tıklayın.

1. **İşlem ve ağ** bölümünde Azure adını, kaynak grubunu, hedef boyutunu, [kullanılabilirlik kümesini](../virtual-machines/windows/tutorial-availability-sets.md)ve yönetilen disk ayarlarını değiştirebilirsiniz.

1. Ağ ayalarını, yük devretmeden sonra Azure VM’nin yerleştirileceği ağ/alt ağ ve VM’ye atanacak IP adresi dahil olmak üzere görüntüleyebilir ve değiştirebilirsiniz.

1. **Diskler** bölümünde VM’deki işletim sistemi ve veri diskleriyle ilgili bilgileri görüntüleyebilirsiniz.

## <a name="fail-over-to-azure"></a>Azure'a yük devretme

1. **Ayarlar**  >  **çoğaltılan öğeler**' de **Yük devretme**> VM ' ye tıklayın.
2. **Yük devretme** bölümünde **En son** kurtarma noktasını seçin. 
3. **Yük devretmeyi başlatmadan önce makineyi kapatın** seçeneğini belirleyin. Site Recovery, yük devretmeyi tetiklemeden önce kaynak VM'leri kapatmaya çalışır. Kapatma işlemi başarısız olsa bile yük devretme devam eder. **İşler** sayfasında yük devretme ilerlemesini izleyebilirsiniz.
4. Yük devretmeyi doğruladıktan sonra **Yürüt**'e tıklayın. Bu işlem tüm kullanılabilir kurtarma noktalarını siler.

> [!WARNING]
> **Devam eden bir yük devretme işlemini iptal etmeyin**: Devam eden bir işlemi iptal ederseniz yük devretme durdurulur ancak VM yeniden çoğaltılmaz.

## <a name="connect-to-failed-over-vm"></a>Yük devredilen VM 'ye bağlanma

1. Yük devretmeden sonra Uzak Masaüstü Protokolü (RDP) ve Secure Shell (SSH) kullanarak Azure VM 'lerine bağlanmak istiyorsanız, [gereksinimlerin karşılandığını doğrulayın](failover-failback-overview.md#connect-to-azure-after-failover).
2. Yük devretmeden sonra VM 'ye gidin ve bu ağa [bağlanarak](../virtual-machines/windows/connect-logon.md) doğrulayın.
3. Yük devretmeden sonra farklı bir kurtarma noktası kullanmak istiyorsanız, **değişiklik kurtarma noktasını** kullanın. Bir sonraki adımda yük devretmeyi kaydettikten sonra bu seçenek artık kullanılamaz.
4. Doğrulamadan sonra, yük devretmeden sonra sanal makinenin kurtarma noktasını sonlandırmak için **Yürüt** ' ü seçin.
5. Kaydettikten sonra, tüm kullanılabilir kurtarma noktaları silinir. Bu adım yük devretmeyi tamamlar.

>[!TIP]
> Yük devretmeden sonra herhangi bir bağlantı sorunlarıyla karşılaşırsanız, [sorun giderme kılavuzunu](site-recovery-failover-to-azure-troubleshoot.md)izleyin.


## <a name="next-steps"></a>Sonraki adımlar

Yük devretmeden sonra Azure VM 'Leri Azure 'dan şirket içine çoğaltacak şekilde yeniden koruyun. Ardından, VM 'Ler yeniden korunduktan ve şirket içi siteye çoğaltıldıktan sonra, hazırsanız Azure 'dan yeniden yük devreder.
