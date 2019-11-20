---
title: Azure Site Recovery hizmeti ile olağanüstü durum kurtarma için ikincil bir Azure bölgesine çoğaltılan Azure VM 'lerinin yükünü devretmek ve yeniden koruyun.
description: Azure Site Recovery hizmeti ile olağanüstü durum kurtarma için ikincil bir Azure bölgesine çoğaltılan Azure VM 'lerinin yükünü devretme ve yeniden koruma hakkında bilgi edinin.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9bc0d25e19ad3412e62eb3386b0faf3ae5d2a444
ms.sourcegitcommit: f7998db5e6ba35cbf2a133174027dc8ccf8ce957
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68782585"
---
# <a name="fail-over-and-reprotect-azure-vms-between-regions"></a>Bölgeler arasında Azure VM 'Leri yük devretme ve yeniden koruma

Bu öğreticide, Azure sanal makinesi 'nin (VM) [Azure Site Recovery](site-recovery-overview.md) hizmeti ile Ikincil bir Azure bölgesine yük devretme işlemi açıklanmaktadır. Yük devretdikten sonra, sanal makineyi yeniden koruyabilirsiniz. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure VM’ye yük devretme
> * İkincil Azure VM 'sini birincil bölgeye çoğaltan şekilde yeniden koruyun.

> [!NOTE]
> Bu öğretici, varsayılan ayarlara ve en düşük özelleştirmeye sahip en basit yolu içerir. Daha karmaşık senaryolar için, Azure VM 'Leri için ' nasıl yapılır ' altındaki makaleleri kullanın.


## <a name="prerequisites"></a>Önkoşullar

- Başlamadan önce, yük devretme hakkında [sık sorulan soruları](site-recovery-faq.md#failover) gözden geçirin.
- Her şeyin beklenildiği gibi çalışıp çalışmadığını denetlemek için bir [olağanüstü durum kurtarma tatbikatını](azure-to-azure-tutorial-dr-drill.md) tamamladığınızdan emin olun.
- Yük devretme testini çalıştırmadan önce VM özelliklerini doğrulayın. VM, [Azure gereksinimlerine](azure-to-azure-support-matrix.md#replicated-machine-operating-systems) uymalıdır.

## <a name="run-a-failover-to-the-secondary-region"></a>İkincil bölgeye yük devretme çalıştırma

1. **Çoğaltılmış öğeler** bölümünde, yük devretmek istediğiniz VM’yi seçin > **Yük devretme**

   ![Yük devret](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. **Yük devretme** kısmında, yük devredeceğiniz bir **Kurtarma Noktası** seçin. Şu seçeneklerden birini kullanabilirsiniz:

   * **En son** (varsayılan): Site Recovery hizmetindeki tüm verileri işler ve en düşük kurtarma noktası hedefini (RPO) sağlar.
   * **En son işlenen**: Sanal makineyi Site Recovery hizmeti tarafından işlenen en son kurtarma noktasına geri döndürür.
   * **Özel**: Belirli bir kurtarma noktasına yük devreder. Bu seçenek, bir yük devretme testi gerçekleştirmek için faydalıdır.

3. Yük devretmeyi tetiklemeden önce Site Recovery kaynak VM 'Leri kapatmayı denemek istiyorsanız, **yük devretmeye başlamadan önce makineyi Kapat ' ı** seçin. Kapalı, veri kaybı olmamasını sağlamaya yardımcı olur. Kapatma işlemi başarısız olsa bile yük devretme devam eder. Site Recovery yük devretmeden sonra kaynağı temizlemez.

4. Yük devretme ilerleme durumunu **İşler** sayfasından takip edin.

5. Yük devretmeden sonra, sanal makineyi doğrulamak için makinede oturum açın. Sanal makine için başka bir kurtarma noktasına gitmek istiyorsanız, **Kurtarma noktasını değiştir** seçeneğini kullanabilirsiniz.

6. Yük devredilmiş sanal makineden memnun kaldığınızda, yük devretmeyi **Yürütebilirsiniz**.
   Yürütme işlemi, hizmette kullanılabilir olan tüm kurtarma noktalarını siler. Artık kurtarma noktasını değiştiremeyeceksiniz.

> [!NOTE]
> VM için çoğaltmayı etkinleştirdikten sonra disk ekleyeceğiniz bir VM 'nin yükünü devretmek, çoğaltma noktaları, kurtarma için kullanılabilen diskleri gösterir. Örneğin, bir VM 'nin tek bir diski varsa ve yeni bir tane eklerseniz, diski eklemeden önce oluşturulan çoğaltma noktaları, çoğaltma noktasının "1/2 diskten" oluştuğunu gösterir.

![Eklenen bir disk ile yük devretme](./media/azure-to-azure-tutorial-failover-failback/failover-added.png)

## <a name="reprotect-the-secondary-vm"></a>İkincil VM’yi yeniden koruma

VM’nin yük devretmesinden sonra, birincil bölgeye geri çoğaltması için VM’yi yeniden korumalısınız.

1. VM’nin **Yük devretme yürütüldü** durumunda olduğundan emin olun ve birincil bölgenin kullanılabilir olduğunu ve içinde yeni kaynaklar oluşturup bunlara erişebildiğinizi denetleyin.
2. **Kasa** > **Çoğaltılmış öğeler** bölümünde, yük devredilmiş VM’ye sağ tıklayın ve sonra **Yeniden Koru** seçeneğini belirleyin.

   ![Yeniden korumaya sağ tıklayın](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Birincil bölgeye kadar olan koruma yönünün zaten seçili olduğunu doğrulayın.
3. **Kaynak grubu, Ağ, Depolama ve Kullanılabilirlik kümeleri** bilgilerini gözden geçirin. Yeni olarak işaretlenen tüm kaynaklar yeniden koruma işleminin bir parçası olarak oluşturulur.
4. Yeniden koruma işini tetiklemek için **Tamam**’a tıklayın. Bu iş, hedef siteye en son verileri sağlar. Ardından, deltaları birincil bölgeye çoğaltır. VM artık korunan bir durumdadır.

## <a name="next-steps"></a>Sonraki adımlar
- Yeniden koruma altına aldıktan sonra, kullanılabilir olduğunda birincil bölgeye nasıl yeniden yük [devredebileceğinizi öğrenin](azure-to-azure-tutorial-failback.md) .
- Yeniden koruma akışı hakkında [daha fazla bilgi edinin](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) .
