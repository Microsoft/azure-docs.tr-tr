---
title: Azure VMware çözümünden Azure 'a yönelik bir olağanüstü durum kurtarma detayına Azure Site Recovery
description: Azure VMware Çözüm özel bulutu 'ndan Azure Site Recovery ile Azure 'a olağanüstü durum kurtarma detayının nasıl çalıştırılacağını öğrenin.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 46d5e1cf773a24b032874ee54021c780e1f361af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91814617"
---
# <a name="run-a-disaster-recovery-drill-from-azure-vmware-solution-to-azure"></a>Azure VMware çözümünden Azure 'a olağanüstü durum kurtarma ayrıntısı çalıştırma

Bu makalede, Azure VMware Çözüm VM 'si için [Azure Site Recovery](site-recovery-overview.md) hizmeti kullanılarak Azure 'da olağanüstü durum kurtarma detayının nasıl çalıştırılacağı açıklanmaktadır. Tatbikat, veri kaybı olmadan çoğaltma stratejinizi doğrular.


Bu, Azure VMware Çözüm makineleri için Azure 'da olağanüstü durum kurtarmanın nasıl ayarlanacağını gösteren bir serideki dördüncü öğreticidir.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]

> * Yük devretme testi için yalıtılmış bir ağ ayarlama
> * Yük devretme sonrasında Azure VM'ye bağlanmak için hazırlık yapma
> * Tek bir makine için yük devretme testi çalıştırın.

> [!NOTE]
> Öğreticiler, bir senaryo için en basit dağıtım yolunu gösterir. Mümkün olduğunca varsayılan seçenekleri kullanır ve tüm olası ayarları ve yolları göstermez. Olağanüstü durum kurtarma detaya gitme adımları hakkında daha ayrıntılı bilgi edinmek istiyorsanız [Bu makaleyi gözden geçirin](site-recovery-test-failover-to-azure.md).

## <a name="before-you-start"></a>Başlamadan önce

Önceki öğreticilerini doldurun:

1. Azure 'da olağanüstü durum kurtarma için [Azure 'ı ayarladığınızdan](avs-tutorial-prepare-azure.md) emin olun.
2. Azure VMware Çözüm dağıtımınızı Azure 'a olağanüstü durum kurtarma için hazırlamak üzere [Bu adımları](avs-tutorial-prepare-avs.md) izleyin.
3. Azure VMware Çözüm VM 'Leri için olağanüstü durum kurtarmayı [ayarlayın](avs-tutorial-replication.md) .
 

## <a name="verify-vm-properties"></a>VM özelliklerini doğrulama

Yük devretme testi çalıştırmadan önce VM özelliklerini doğrulayın ve  [VMware VM](vmware-physical-azure-support-matrix.md#replicated-machines) 'nin Azure gereksinimleriyle uyumlu olduğundan emin olun.

1. **Korunan Öğeler**’de **Çoğaltılan Öğeler** > VM seçeneğine tıklayın.
2. **Çoğaltılan öğe** bölmesinde VM bilgileri ile sistem durumunun bir özeti ve kullanılabilen son kurtarma noktaları yer alır. Daha fazla ayrıntı görüntülemek için **Özellikler**’e tıklayın.
3. **İşlem ve ağ** bölümünde Azure adını, kaynak grubunu, hedef boyutunu, kullanılabilirlik kümesini ve yönetilen disk ayarlarını değiştirebilirsiniz.
4. Ağ ayalarını, yük devretmeden sonra Azure VM’nin yerleştirileceği ağ/alt ağ ve VM’ye atanacak IP adresi dahil olmak üzere görüntüleyebilir ve değiştirebilirsiniz.
5. **Diskler** bölümünde VM’deki işletim sistemi ve veri diskleriyle ilgili bilgileri görüntüleyebilirsiniz.

## <a name="create-a-network-for-test-failover"></a>Yük devretme testi için ağ oluşturma

Yük devretme testi için her VM'nin **İşlem ve Ağ** ayarlarından üretim kurtarma sitesi ağınızdan yalıtılmış bir ağ seçmenizi öneririz. Varsayılan olarak yeni oluşturduğunuz Azure sanal ağları diğer ağlardan yalıtılmış olur. Test ağı üretim ağınıza benzer olmalıdır:

- Test ağında üretim ağınızdaki sayıda alt ağ olmalıdır. Alt ağların adı aynı olmalıdır.
- Sınama ağı aynı IP adresi sınıfını ve alt ağ aralığını kullanmalıdır.
- Test ağının DNS sunucusunu **İşlem ve Ağ** ayarlarında DNS VM için belirtilen IP adresiyle güncelleştirin. Daha fazla ayrıntı için [Active Directory için yük devretme testi ile ilgili dikkat edilmesi gerekenler](site-recovery-active-directory.md#test-failover-considerations) sayfasını okuyun.

## <a name="run-a-test-failover-for-a-single-vm"></a>Tek bir VM için yük devretme testi çalıştırma

Yük devretme testi çalıştırdığınızda şunlar olur:

1. Yük devretme için gerekli tüm koşulların karşılandığından emin olmak için bir önkoşul denetimi çalıştırılır.
2. Yük devretme, bir Azure sanal makinesi oluşturulabilmesi için verileri işler. En son kurtarma noktasını seçerseniz verilerden bir kurtarma noktası oluşturulur.
3. Önceki adımda işlenen veriler kullanılarak bir Azure sanal makinesi oluşturulur.

Yük devretme testini aşağıdaki gibi çalıştırın:

1. **Ayarlar**  >  **çoğaltılan öğeler**' de VM > **+ Yük devretme testi**' ne tıklayın.
2. Bu öğretici için **Son işlenen** kurtarma noktasını seçin. Bu işlem VM yükünü zamandaki en son kullanılabilir noktaya devreder. Zaman damgası gösterilir. Bu seçenekte, verileri işlemek için zaman harcanmadığından düşük bir RTO (kurtarma süresi hedefi) sağlanır.
3. **Yük Devretme Testi** bölümünde, yük devretme gerçekleştikten sonra Azure VM’lerinin bağlanacağı hedef Azure ağını seçin.
4. Yük devretmeyi başlatmak için **Tamam**'a tıklayın. Sanal makine özelliklerini açmak için sanal makineye tıklayarak ilerleme durumunu izleyebilirsiniz. Ya da kasa adı > **Ayarlar** > **İşler** >
   **Site Recovery işleri** bölümünde **Yük Devretme Testi** işine tıklayabilirsiniz.
5. Yük devretme bittikten sonra, çoğaltma Azure VM, Azure portalı > **Sanal Makineler** bölümünde görünür. Sanal makinenin uygun boyutta olduğundan, doğru ağa bağlandığından ve çalıştığından emin olun.
6. Şimdi Azure’da çoğaltılan sanal makineye bağlanabiliyor olmanız gerekir.
7. Yük devretme testi sırasında oluşturulan Azure sanal makinelerini silmek için, VM’de **Yük devretme testini temizle**’ye tıklayın. **Notlar**’da, yük devretme testiyle ilişkili gözlemlerinizi kaydedin ve saklayın.

Bazı senaryolarda yük devretme için sekiz ila on dakikada tamamlanan ek işlem gerekebilir. VMware Linux makinelerinde, DHCP hizmeti etkinleştirilmemiş VMware VM’lerinde ve storvsc, vmbus, storflt, intelide, atapi önyükleme sürücülerine sahip olmayan VMware VM’lerinde uzun yük devretme testi süreleriyle karşılaşabilirsiniz.

## <a name="connect-after-failover"></a>Yük devretmeden sonra Bağlan

Yük devretmeden sonra RDP/SSH kullanarak Azure VM 'lerine bağlanmak istiyorsanız, [bağlanmaya hazırlanın](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover). Yük devretmeden sonra herhangi bir bağlantı sorunlarıyla karşılaşırsanız, [sorun giderme](site-recovery-failover-to-azure-troubleshoot.md) kılavuzunu izleyin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Yük devretme çalıştırma](avs-tutorial-failover.md)

