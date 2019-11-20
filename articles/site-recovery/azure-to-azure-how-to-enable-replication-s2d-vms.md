---
title: Azure Site Recovery kullanarak Depolama Alanları Doğrudan çalıştıran Azure VM 'lerini çoğaltma
description: Bu makalede Azure Site Recovery kullanarak Depolama Alanları Doğrudan çalıştıran Azure VM 'lerinin nasıl çoğaltılacağı açıklanır.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/29/2019
ms.author: asgang
ms.openlocfilehash: 25ac7fa577aa33eda036c0f8544cc5ab03b12cd7
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954457"
---
# <a name="replicate-azure-vms-running-storage-spaces-direct-to-another-region"></a>Depolama Alanları Doğrudan çalıştıran Azure VM 'lerini başka bir bölgeye çoğaltma

Bu makalede, depolama alanları doğrudan çalıştıran Azure VM 'lerinin olağanüstü durum kurtarmasının nasıl etkinleştirileceği açıklanır.

>[!NOTE]
>Yalnızca çökme ile tutarlı kurtarma noktaları, depolama alanları doğrudan kümeleri için desteklenir.
>

## <a name="introduction"></a>Giriş 
[Depolama alanları doğrudan (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct) , Azure üzerinde [Konuk kümeler](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure) oluşturmak için bir yol sağlayan, yazılım tanımlı bir depodır.  Microsoft Azure Konuk küme, IaaS VM 'lerinden oluşan bir yük devretme kümesidir. Barındırılan VM iş yüklerinin Konuk kümeler genelinde yük devredebilmeleri için, tek bir Azure VM 'nin sağlayabileceğinden uygulamalar için daha yüksek kullanılabilirlik SLA 'Sı sağlar. VM 'nin SQL veya genişleme dosya sunucusu gibi kritik bir uygulamayı barındırmakta olduğu senaryolarda faydalıdır.

## <a name="disaster-recovery-of-azure-virtual-machines-using-storage-spaces-direct"></a>Depolama alanları doğrudan kullanarak Azure sanal makinelerini olağanüstü durumdan kurtarma
Tipik bir senaryoda, Azure 'da genişleme dosya sunucusu gibi daha yüksek dayanıklılık sağlamak için sanal makineler konuk kümeniz olabilir. Bu, uygulamanızın daha yüksek kullanılabilirlik sağlamasına karşın, herhangi bir bölge düzeyindeki başarısızlık için Site Recovery kullanarak bu uygulamaları korumak istersiniz. Site Recovery, verileri bir bölgeden başka bir Azure bölgesine çoğaltır ve yük devretme durumunda kümeyi olağanüstü durum kurtarma bölgesinde getirir.

Aşağıdaki diyagramda, depolama alanları doğrudan kullanan iki Azure VM yük devretme kümesinin resim gösterimi gösterilmektedir.

![storagespacesdirect](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacedirect.png)

 
- Bir Windows Yük devretme kümesindeki iki Azure sanal makinesi ve her sanal makinenin iki veya daha fazla veri diski vardır.
- S2D veri diskindeki verileri eşitler ve eşitlenmiş depolamayı bir depolama havuzu olarak gösterir.
- Depolama havuzu, yük devretme kümesine bir küme paylaşılan birimi (CSV) olarak sunulur.
- Yük devretme kümesi, veri sürücüleri için CSV 'yi kullanır.

**Olağanüstü durum kurtarma konuları**

1. Küme için [bulut tanığı](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp) ayarlarken, tanığını olağanüstü durum kurtarma bölgesinde tutun.
2. Kaynak bölgeden farklı olan DR bölgesindeki alt ağa sanal makinelerin yükünü devretmek için, küme IP adresinin yük devretmeden sonra değiştirilmesi gerekir.  Kümenin IP 'sini değiştirmek için ASR [kurtarma planı betiği](https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation) kullanmanız gerekir.</br>
Özel Betik uzantısı kullanarak VM içinde komut yürütmek için [örnek betik](https://github.com/krnese/azure-quickstart-templates/blob/master/asr-automation-recovery/scripts/ASR-Wordpress-ChangeMysqlConfig.ps1) 

### <a name="enabling-site-recovery-for-s2d-cluster"></a>S2D kümesi için Site Recovery Etkinleştiriliyor:

1. Kurtarma Hizmetleri kasasının içinde "+ Çoğalt" a tıklayın.
1. Kümedeki tüm düğümleri seçin ve bunları [Çoklu VM tutarlılık grubunun](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-common-questions#multi-vm-consistency) bir parçası yapın
1. Uygulama tutarlılığı olmadan çoğaltma ilkesi seçin * (yalnızca kilitlenme tutarlılığı desteği kullanılabilir)
1. Çoğaltmayı etkinleştirme

   ![storagespacesdirect koruması](./media/azure-to-azure-how-to-enable-replication-s2d-vms/multivmgroup.png)

2. Çoğaltılan öğelere gidin ve sanal makine durumunu da görebilirsiniz. 
3. Her iki sanal makine de korunur ve çoklu VM tutarlılık grubunun bir parçası olarak gösterilir.

   ![storagespacesdirect koruması](./media/azure-to-azure-how-to-enable-replication-s2d-vms/storagespacesdirectgroup.PNG)

## <a name="creating-a-recovery-plan"></a>Kurtarma planı oluşturma
Kurtarma planı, yük devretme sırasında çok katmanlı bir uygulamadaki çeşitli katmanların sıralamasını destekler. Sıralama, uygulama tutarlılığını sürdürmenize yardımcı olur. Çok katmanlı bir Web uygulaması için bir kurtarma planı oluşturduğunuzda [Site Recovery kullanarak kurtarma planı oluşturma](site-recovery-create-recovery-plans.md)bölümünde açıklanan adımları uygulayın.

### <a name="adding-virtual-machines-to-failover-groups"></a>Yük devretme gruplarına sanal makineler ekleme

1.  Sanal makineleri ekleyerek bir kurtarma planı oluşturun.
2.  VM 'Leri gruplandırmak için ' Özelleştir 'e tıklayın. Varsayılan olarak, tüm VM 'Ler ' Grup 1 ' in bir parçasıdır.


### <a name="add-scripts-to-the-recovery-plan"></a>Kurtarma planına betikler ekleme
Uygulamalarınızın düzgün çalışması için, yük devretmeden sonra veya yük devretme testi sırasında Azure sanal makinelerinde bazı işlemler yapmanız gerekebilir. Bazı yük devretme sonrası işlemleri otomatik hale getirebilirsiniz. Örneğin, bu yük dengeleyici ekleniyor ve küme IP 'yi değiştiriyor.


### <a name="failover-of-the-virtual-machines"></a>Sanal makinelerin yük devretmesi 
Her iki sanal makine düğümünün de [ASR kurtarma planı](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) kullanılarak yük devri yapması gerekir 

![storagespacesdirect koruması](./media/azure-to-azure-how-to-enable-replication-s2d-vms/recoveryplan.PNG)

## <a name="run-a-test-failover"></a>Yük devretme testi çalıştırma
1.  Azure portal, kurtarma hizmetleri kasanızı seçin.
2.  Oluşturduğunuz kurtarma planını seçin.
3.  **Yük Devretme Testi**'ni seçin.
4.  Yük devretme testi işlemini başlatmak için kurtarma noktasını ve Azure sanal ağını seçin.
5.  İkincil ortam çalışır duruma geldiğinde doğrulama gerçekleştirin.
6.  Doğrulamalar tamamlandığında, yük devretme ortamını temizlemek için **Test yük devretmesini**Temizle ' yi seçin.

Daha fazla bilgi için bkz. [Azure 'a yük devretmeyi test etme Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Yük devretme çalıştırma

1.  Azure portal, kurtarma hizmetleri kasanızı seçin.
2.  SAP uygulamaları için oluşturduğunuz kurtarma planını seçin.
3.  **Yük devretme**'yi seçin.
4.  Yük devretme işlemini başlatmak için kurtarma noktasını seçin.

Daha fazla bilgi için bkz. [Site Recovery 'de yük devretme](site-recovery-failover.md).
## <a name="next-steps"></a>Sonraki adımlar

Yeniden çalışma çalıştırma hakkında [daha fazla bilgi edinin](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) .
