---
title: Azure VM 'lerini Azure Site Recovery hizmeti ile bir birincil bölgeye geri devretmek.
description: Azure VM 'lerinin Azure Site Recovery hizmeti ile birincil bölgeye nasıl geri yükleneceğini açıklar.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c27b7bf29e5f124fdcfb886b658fd8e9d4cc48fe
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091356"
---
# <a name="fail-back-an-azure-vm-between-azure-regions"></a>Azure bölgeleri arasında Azure VM geri dönme

[Azure Site Recovery](site-recovery-overview.md) hizmeti, şirket içi makinelerin ve Azure sanal makinelerinin (VM) çoğaltma, yük devretme ve yeniden çalışma işlemlerini yönetip düzenleyerek olağanüstü durum kurtarma stratejinize katkıda bulunur.

Bu öğreticide, tek bir Azure VM 'nin nasıl geri yükleneceği açıklanmaktadır. Yük devretdikten sonra, kullanılabilir olduğunda birincil bölgeye yeniden yük devredebilmeniz gerekir. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> 
> * İkincil bölgedeki VM 'yi yeniden devreder.
> * Birincil VM 'yi ikincil bölgeye yeniden koruyun.
> 
> [!NOTE]
> 
> Bu öğretici, bir hedef bölgeye birkaç VM 'nin yükünü devretmek ve en düşük özelleştirmeler ile kaynak bölgeye geri dönmek için size yardımcı olur. Daha ayrıntılı yönergeler için [Azure VM 'lerinde nasıl yapılır kılavuzlarını](https://docs.microsoft.com/azure/virtual-machines/windows/)gözden geçirin.

## <a name="before-you-start"></a>Başlamadan önce

* VM durumunun **Yük devretme taahhütine**sahip olduğundan emin olun.
* Birincil bölgenin kullanılabilir olup olmadığını ve içinde yeni kaynaklar oluşturup bu kaynaklara erişebiliyor olduğunuzdan emin olun.
* Yeniden korumanın etkinleştirildiğinden emin olun.

## <a name="fail-back-to-the-primary-region"></a>Birincil bölgeye geri dönme

VM 'Ler yeniden korunduktan sonra, gerekirse birincil bölgeye yeniden yük devreedebilirsiniz.

1. Kasada **çoğaltılan öğeler**' i seçin ve ardından YENIDEN korunan VM 'yi seçin.

    ![Birincili yeniden çalışma](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

2. **Çoğaltılan öğeler**' de VM ' yi seçin ve ardından **Yük devretme**' yı seçin.
3. **Yük devretme**bölümünde yük devretmek için bir kurtarma noktası seçin:
    - **En son (varsayılan)** : Site Recovery hizmetindeki tüm verileri işler ve en düşük kurtarma noktası HEDEFINI (RPO) sağlar.
    - **En son işlenen**: VM 'yi, Site Recovery tarafından işlenen en son kurtarma noktasına geri döndürür.
    - **Özel**: belirli bir kurtarma noktasına yük devreder. Bu seçenek, bir yük devretme testi gerçekleştirmek için faydalıdır.
4. Yük devretmeyi tetiklemeden önce DR bölgesindeki VM 'lerin kapatılmasını denemesini Site Recovery istiyorsanız, **yük devretmeye başlamadan önce makineyi Kapat ' ı** seçin. Yük devretme işlemi, kapatılma başarısız olsa bile devam eder. 
5. Yük devretme ilerleme durumunu **İşler** sayfasından takip edin.
6. Yük devretme tamamlandıktan sonra, üzerinde oturum açarak VM 'yi doğrulayın. Kurtarma noktasını gerektiği gibi değiştirebilirsiniz.
7. Yük devretmeyi doğruladıktan sonra **Yük devretmeyi Yürüt**' ü seçin. Yürütülüyor, tüm kullanılabilir kurtarma noktalarını siler. Kurtarma noktasını Değiştir seçeneği artık kullanılamıyor.
8. VM, yük devredildi ve geri başarısız olarak göstermelidir.

    ![Birincil ve ikincil bölgelerde VM](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

> [!NOTE]
> Site Recovery uzantısı sürümü 9.28. x. x onler [güncelleştirme paketi 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) Site Recovery çalıştıran makinelerde, yeniden çalışma tamamlandıktan ve VM 'ler yeniden korunduktan sonra ikincil olağanüstü durum kurtarma bölgesindeki makineleri temizler. İkincil bölgedeki VM 'Leri ve NIC 'leri el ile silmeniz gerekmez. Çoğaltmayı geri aldıktan sonra tamamen devre dışı bırakırsanız, Site Recovery VM 'Ler ve NIC 'Lerin yanı sıra olağanüstü durum kurtarma bölgesindeki diskleri temizler.

## <a name="next-steps"></a>Sonraki adımlar

Yeniden koruma akışı hakkında [daha fazla bilgi edinin](azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection) .
