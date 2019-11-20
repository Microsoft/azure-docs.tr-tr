---
title: Azure 'a olağanüstü durum kurtarma detayına Azure Site Recovery
description: Şirket içinden Azure 'a olağanüstü durum kurtarma detayına Azure Site Recovery ile nasıl çalıştırılacağını öğrenin.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 5bd9f5316f8b8799633de8c0c84c61424c0e4f4a
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954429"
---
# <a name="run-a-disaster-recovery-drill-to-azure"></a>Azure’da olağanüstü durum kurtarma tatbikatı çalıştırma

Bu makalede, şirket içi bir makine için [Azure Site Recovery](site-recovery-overview.md) hizmeti kullanılarak Azure 'da olağanüstü durum kurtarma detayının nasıl çalıştırılacağı açıklanmaktadır. Tatbikat, veri kaybı olmadan çoğaltma stratejinizi doğrular.


Bu, şirket içi makineler için Azure 'da olağanüstü durum kurtarmanın nasıl ayarlanacağını gösteren bir serideki dördüncü öğreticidir.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Yük devretme testi için yalıtılmış bir ağ ayarlama
> * Yük devretme sonrasında Azure VM'ye bağlanmak için hazırlık yapma
> * Tek bir makine için yük devretme testi çalıştırın.

> [!NOTE]
> Öğreticiler, bir senaryo için en basit dağıtım yolunu gösterir. Mümkün olduğunca varsayılan seçenekleri kullanır ve tüm olası ayarları ve yolları göstermez. Olağanüstü durum kurtarma detaya gitme adımları hakkında daha ayrıntılı bilgi edinmek istiyorsanız [Bu makaleyi gözden geçirin](site-recovery-test-failover-to-azure.md).

## <a name="before-you-start"></a>Başlamadan önce

Önceki öğreticilerini doldurun:

1. VMware VM 'Leri, Hyper-V VM 'Leri ve fiziksel makinelerin Azure 'a şirket içi olağanüstü durum kurtarması için [Azure 'u ayarladığınızdan](tutorial-prepare-azure.md) emin olun.
2. Şirket içi [VMware](vmware-azure-tutorial-prepare-on-premises.md) veya [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) ortamınızı olağanüstü durum kurtarma için hazırlayın. Fiziksel sunucular için olağanüstü durum kurtarma ayarlıyorsanız, [destek matrisini](vmware-physical-secondary-support-matrix.md)gözden geçirin.
3. [VMware VM](vmware-azure-tutorial.md)'leri, [Hyper-V VM 'leri](hyper-v-azure-tutorial.md)veya [fiziksel makineler](physical-azure-disaster-recovery.md)için olağanüstü durum kurtarmayı ayarlayın.
 

## <a name="verify-vm-properties"></a>VM özelliklerini doğrulama

Bir yük devretme testi çalıştırmadan önce VM özelliklerini doğrulayın ve [Hyper-V VM](hyper-v-azure-support-matrix.md#replicated-vms), [VMware VM](vmware-physical-azure-support-matrix.md#replicated-machines)'nin Azure gereksinimlerine uygun olduğundan emin olun.

1. **Korunan Öğeler**’de **Çoğaltılan Öğeler** > VM seçeneğine tıklayın.
2. **Çoğaltılan öğe** bölmesinde VM bilgileri ile sistem durumunun bir özeti ve kullanılabilen son kurtarma noktaları yer alır. Daha fazla ayrıntı görüntülemek için **Özellikler**’e tıklayın.
3. **İşlem ve Ağ** bölümünde Azure adını, kaynak grubunu, hedef boyutunu, kullanılabilirlik kümesini ve yönetilen disk ayarlarını değiştirebilirsiniz.
4. Ağ ayalarını, yük devretmeden sonra Azure VM’nin yerleştirileceği ağ/alt ağ ve VM’ye atanacak IP adresi dahil olmak üzere görüntüleyebilir ve değiştirebilirsiniz.
5. **Diskler** bölümünde VM’deki işletim sistemi ve veri diskleriyle ilgili bilgileri görüntüleyebilirsiniz.

## <a name="create-a-network-for-test-failover"></a>Yük devretme testi için ağ oluşturma

Yük devretme testi için her VM'nin **İşlem ve Ağ** ayarlarından üretim kurtarma sitesi ağınızdan yalıtılmış bir ağ seçmenizi öneririz. Varsayılan olarak yeni oluşturduğunuz Azure sanal ağları diğer ağlardan yalıtılmış olur. Test ağı üretim ağınıza benzer olmalıdır:

- Test ağında üretim ağınızdaki sayıda alt ağ olmalıdır. Alt ağların adı aynı olmalıdır.
- Test ağında aynı IP adresi aralığı kullanılmalıdır.
- Test ağının DNS sunucusunu **İşlem ve Ağ** ayarlarında DNS VM için belirtilen IP adresiyle güncelleştirin. Daha fazla ayrıntı için [Active Directory için yük devretme testi ile ilgili dikkat edilmesi gerekenler](site-recovery-active-directory.md#test-failover-considerations) sayfasını okuyun.

## <a name="run-a-test-failover-for-a-single-vm"></a>Tek bir VM için yük devretme testi çalıştırma

Yük devretme testi çalıştırdığınızda şunlar olur:

1. Yük devretme için gerekli tüm koşulların karşılandığından emin olmak için bir önkoşul denetimi çalıştırılır.
2. Yük devretme, bir Azure sanal makinesi oluşturulabilmesi için verileri işler. En son kurtarma noktasını seçerseniz verilerden bir kurtarma noktası oluşturulur.
3. Önceki adımda işlenen veriler kullanılarak bir Azure sanal makinesi oluşturulur.

Yük devretme testini aşağıdaki gibi çalıştırın:

1. **Ayarlar** > **Çoğaltılmış Öğeler** bölümünde, CM > **+Yük Devretme Testi**’ne tıklayın.
2. Bu öğretici için **Son işlenen** kurtarma noktasını seçin. Bu işlem VM yükünü zamandaki en son kullanılabilir noktaya devreder. Zaman damgası gösterilir. Bu seçenekte, verileri işlemek için zaman harcanmadığından düşük bir RTO (kurtarma süresi hedefi) sağlanır.
3. **Yük Devretme Testi** bölümünde, yük devretme gerçekleştikten sonra Azure VM’lerinin bağlanacağı hedef Azure ağını seçin.
4. Yük devretmeyi başlatmak için **Tamam**'a tıklayın. Sanal makine özelliklerini açmak için sanal makineye tıklayarak ilerleme durumunu izleyebilirsiniz. Ya da kasa adı > **Ayarlar**İşler > Site Recovery işleri >
    bölümünde **Yük Devretme Testi** işine tıklayabilirsiniz.
5. Yük devretme bittikten sonra, çoğaltma Azure VM, Azure portalı > **Sanal Makineler** bölümünde görünür. Sanal makinenin uygun boyutta olduğundan, doğru ağa bağlandığından ve çalıştığından emin olun.
6. Şimdi Azure’da çoğaltılan sanal makineye bağlanabiliyor olmanız gerekir.
7. Yük devretme testi sırasında oluşturulan Azure sanal makinelerini silmek için, VM’de **Yük devretme testini temizle**’ye tıklayın. Yük devretme testiyle ilişkili gözlemlerinizi **Notlar**’da kaydedin veya saklayın.

Bazı senaryolarda yük devretme için sekiz ila on dakikada tamamlanan ek işlem gerekebilir. VMware Linux makinelerinde, DHCP hizmeti etkinleştirilmemiş VMware VM’lerinde ve storvsc, vmbus, storflt, intelide, atapi önyükleme sürücülerine sahip olmayan VMware VM’lerinde uzun yük devretme testi süreleriyle karşılaşabilirsiniz.

## <a name="connect-after-failover"></a>Yük devretmeden sonra Bağlan

Yük devretmeden sonra RDP/SSH kullanarak Azure VM 'lerine bağlanmak istiyorsanız, [bağlanmaya hazırlanın](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover). Yük devretmeden sonra herhangi bir bağlantı sorunlarıyla karşılaşırsanız, [sorun giderme](site-recovery-failover-to-azure-troubleshoot.md) kılavuzunu izleyin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [VMware VM 'leri için yük devretme ve yeniden çalışma çalıştırma](vmware-azure-tutorial-failover-failback.md)
> [Hyper-V VM](hyper-v-azure-failover-failback-tutorial.md) 'lerinde yük devretme ve yeniden çalışma çalıştırma
> , [fiziksel makinelerde yük devretme ve yeniden çalışma](physical-to-azure-failover-failback.md) çalıştırma
