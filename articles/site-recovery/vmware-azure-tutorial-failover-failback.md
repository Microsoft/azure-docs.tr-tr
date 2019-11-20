---
title: Olağanüstü durum kurtarma sırasında VMware VM’lerde ve fiziksel sunucularda Azure Site Recovery’ye yük devretme ve yeniden çalışma | Microsoft Docs
description: VMware VM 'lerini ve fiziksel sunucuları Azure 'a devretmek ve Azure 'a olağanüstü durum kurtarma sırasında Site Recovery kullanarak şirket içi sitede nasıl yük devredebileceğinizi öğrenin.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: tutorial
ms.date: 08/22/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 852193e137eab10d1e46c5ba6ae6636d530095be
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972202"
---
# <a name="fail-over-and-fail-back-vmware-vms"></a>Yük devretme ve VMware VM 'lerini geri dönme

Bu makalede, [Azure Site Recovery](site-recovery-overview.md)için bir şirket içi VMware sanal MAKINESININ (VM) yük devretme işlemi açıklanır.

Bu, şirket içi makineler için Azure 'da olağanüstü durum kurtarmanın nasıl ayarlanacağını gösteren bir serideki beşinci öğreticidir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * VMware VM özelliklerinin Azure gereksinimleriyle uyumlu olduğunu doğrulayın.
> * Azure 'da yük devretme çalıştırın.

> [!NOTE]
> Öğreticiler, bir senaryo için en basit dağıtım yolunu gösterir. Mümkün olduğunca varsayılan seçenekleri kullanır ve tüm olası ayarları ve yolları göstermez. Ayrıntılı yük devretme hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [VM 'ler ve fiziksel sunucular](site-recovery-failover.md).

## <a name="before-you-start"></a>Başlamadan önce

Önceki öğreticilerini doldurun:

1. VMware VM 'Leri, Hyper-V VM 'Leri ve fiziksel makinelerin Azure 'a şirket içi olağanüstü durum kurtarması için [Azure 'u ayarladığınızdan](tutorial-prepare-azure.md) emin olun.
2. Şirket içi [VMware](vmware-azure-tutorial-prepare-on-premises.md) veya [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) ortamınızı olağanüstü durum kurtarma için hazırlayın. Fiziksel sunucular için olağanüstü durum kurtarma ayarlıyorsanız, [destek matrisini](vmware-physical-secondary-support-matrix.md)gözden geçirin.
3. [VMware VM](vmware-azure-tutorial.md)'leri, [Hyper-V VM 'leri](hyper-v-azure-tutorial.md)veya [fiziksel makineler](physical-azure-disaster-recovery.md)için olağanüstü durum kurtarmayı ayarlayın.
4. Her şeyin beklendiği gibi çalıştığından emin olmak için bir [olağanüstü durum kurtarma ayrıntısı](tutorial-dr-drill-azure.md) çalıştırın.

## <a name="failover-and-failback"></a>Yük devretme ve yeniden çalışma

Yük devretme ve yeniden çalışma dört aşamalıdır:

1. **Azure 'a yük devretme:** Şirket içi birincil siteniz kaldığında, makineleri Azure 'a devreder. Yük devretmeden sonra Azure VM 'Leri çoğaltılan verilerden oluşturulur.
2. **Azure VM 'lerini yeniden koruma:** Azure 'da, Azure VM 'Leri, şirket içi VMware VM 'lerine tekrar çoğaltmaya başlayacak şekilde yeniden koruyun. Veri tutarlılığı sağlamaya yardımcı olmak için, yeniden koruma sırasında şirket içi VM kapalıdır.
3. **Şirket içinde yük devreder:** Şirket içi siteniz çalışır duruma geldiğinde Azure 'dan yeniden yük devretmek için bir yük devretme çalıştırın.
4. **Şirket içi VM 'Leri yeniden koruma:** Veriler yeniden başarısız olduktan sonra, geri aldığınız şirket içi VM 'Leri yeniden koruyun, böylece Azure 'a çoğaltmaya başlarlar.

## <a name="verify-vm-properties"></a>VM özelliklerini doğrulama

Yük devretmeyi çalıştırmadan önce VM 'Lerin [Azure gereksinimlerini](vmware-physical-azure-support-matrix.md#replicated-machines)karşıladığından emin olmak için VM özelliklerini denetleyin.

Özellikleri aşağıdaki gibi doğrulayın:

1. **Korunan öğeler**' de **çoğaltılan öğeler**' i seçin ve ardından doğrulamak istediğiniz VM 'yi seçin.

2. **Çoğaltılan öğe** bölmesinde VM bilgileri ile sistem durumunun bir özeti ve kullanılabilen son kurtarma noktaları yer alır. Daha fazla ayrıntı görüntülemek için **Özellikler** ' i seçin.

3. **İşlem ve ağ**bölümünde, bu özellikleri gerektiği şekilde değiştirebilirsiniz:
    * Azure adı
    * Resource group
    * Hedef boyutu
    * [Kullanılabilirlik kümesi](../virtual-machines/windows/tutorial-availability-sets.md)
    * Yönetilen disk ayarları

4. Aşağıdakiler dahil olmak üzere ağ ayarlarını görüntüleyebilir ve değiştirebilirsiniz:

    * Yük devretmeden sonra Azure VM 'nin bulunduğu ağ ve alt ağ.
    * Kendisine atanacak IP adresi.

5. **Diskler** bölümünde VM’deki işletim sistemi ve veri diskleriyle ilgili bilgileri görüntüleyebilirsiniz.

## <a name="run-a-failover-to-azure"></a>Azure'a yük devretme işlemini çalıştırma

1. **Ayarlar** > **çoğaltılan öğeler**' de, yük devretmek istediğiniz VM 'yi seçin ve ardından **Yük devretme**' yı seçin.
2. **Yük devretme** kısmında, yük devredeceğiniz bir **Kurtarma Noktası** seçin. Şu seçeneklerden birini kullanabilirsiniz:
   * **En son**: Bu seçenek ilk olarak Site Recovery gönderilen tüm verileri işler. Yük devretmeden sonra oluşturulan Azure VM, yük devretme tetiklendiğinde Site Recovery çoğaltılan tüm verilere sahip olduğundan en düşük kurtarma noktası hedefini (RPO) sağlar.
   * **En son işlenen**: Bu seçenek, VM 'yi Site Recovery tarafından işlenen en son kurtarma noktasına devreder. İşlenmemiş verileri işlemek için zaman harcanmadığından, bu seçenek düşük bir RTO (kurtarma süresi hedefi) sağlar.
   * **En son uygulamayla tutarlı**: Bu seçenek, VM 'yi Site Recovery tarafından işlenen en son uygulamayla tutarlı kurtarma noktasına devreder.
   * **Özel**: Bu seçenek, bir kurtarma noktası belirtmenizi sağlar.

3. Yük devretmeyi tetiklemeden önce kaynak VM 'Leri kapatmayı denemek için **yük devretmeye başlamadan önce makineyi kapat** ' ı seçin. Yük devretme, kapatılma başarısız olsa bile devam eder. Yük devretme işleminin ilerleme durumunu **İşler** sayfasında takip edebilirsiniz.

Bazı senaryolarda, yük devretme işleminin tamamlanabilmesi için 8 ila 10 dakika geçen ek işleme gerekir. İçin yük devretme sürelerinin daha uzun süre test edebilirsiniz:

* 9,8 'den eski bir Mobility hizmeti sürümü çalıştıran VMware VM 'Leri.
* Fiziksel sunucular.
* VMware Linux VM 'Leri.
* Fiziksel sunucu olarak korunan Hyper-V VM 'Leri.
* DHCP hizmeti etkinleştirilmemiş VMware VM 'Leri.
* Şu önyükleme sürücülerine sahip olmayan VMware VM 'Leri: storvsc, VMBus, storflt, intelide, Atapi.

> [!WARNING]
> Devam eden bir yük devretme işlemini iptal etmeyin. Yük devretme başlatılmadan önce VM çoğaltması durdurulur. Devam eden bir yük devretme işlemini iptal ederseniz yük devretme durdurulur, ancak VM yeniden çoğaltılmaz.

## <a name="connect-to-failed-over-vm"></a>Yük devredilen VM 'ye bağlanma

1. Yük devretmeden sonra Uzak Masaüstü Protokolü (RDP) ve Secure Shell (SSH) kullanarak Azure VM 'lerine bağlanmak istiyorsanız, [gereksinimlerin karşılandığını doğrulayın](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
2. Yük devretmeden sonra VM 'ye gidin ve bu ağa [bağlanarak](../virtual-machines/windows/connect-logon.md) doğrulayın.
3. Yük devretmeden sonra farklı bir kurtarma noktası kullanmak istiyorsanız, **değişiklik kurtarma noktasını** kullanın. Bir sonraki adımda yük devretmeyi kaydettikten sonra bu seçenek artık kullanılamaz.
4. Doğrulamadan sonra, yük devretmeden sonra sanal makinenin kurtarma noktasını sonlandırmak için **Yürüt** ' ü seçin.
5. Kaydettikten sonra, tüm kullanılabilir kurtarma noktaları silinir. Bu adım yük devretmeyi tamamlar.

>[!TIP]
> Yük devretmeden sonra herhangi bir bağlantı sorunlarıyla karşılaşırsanız, [sorun giderme kılavuzunu](site-recovery-failover-to-azure-troubleshoot.md)izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Yük devretmeden sonra Azure VM 'lerini şirket içine yeniden koruyun. Ardından, VM 'Ler yeniden korunduktan ve şirket içi siteye çoğaltıldıktan sonra, hazırsanız Azure 'dan yeniden yük devreder.

> [!div class="nextstepaction"]
> [Azure VM 'leri](vmware-azure-reprotect.md)
> yeniden koruma[Azure 'dan geri dönme](vmware-azure-failback.md)
