---
title: VMware VM 'lerini/fiziksel sunucuları Azure 'dan Azure Site Recovery ile geri devretmek
description: VMware VM 'Leri ve fiziksel sunucuları Azure 'a yük devretmeden sonra şirket içi siteye geri dönme işlemini öğrenin.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 2ec2a4a91f4de0761f631bec393bb90c3feb82b9
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084053"
---
# <a name="fail-back-vmware-vms-and-physical-servers-from-azure-to-an-on-premises-site"></a>VMware VM 'lerini ve fiziksel sunucuları Azure 'dan şirket içi siteye geri dönme

Bu makalede, sanal makinelerin Azure sanal makinelerinden şirket içi VMware ortamına nasıl geri dönmesi anlatılmaktadır. Azure Site Recovery öğreticide [Yük devretmeyi](site-recovery-failover.md) kullanarak VMware sanal makinelerinizi veya Windows/Linux fiziksel sunucularınızı şirket Içi siteden Azure 'a yük devretmeden sonra geri yüklemek için bu makaledeki yönergeleri izleyin.

## <a name="prerequisites"></a>Ön koşullar
- [Farklı yeniden çalışma türleri](concepts-types-of-failback.md) ve ilgili uyarılarla ilgili ayrıntıları okuduğunuzdan emin olun.

> [!WARNING]
> [Geçiş işlemi](migrate-overview.md#what-do-we-mean-by-migration)tamamlandıktan sonra, bir sanal makineyi başka bir kaynak grubuna taşıdıktan veya Azure sanal makinesini sildikten sonra bu işlemi geri alamazsınız. Sanal makinenin korumasını devre dışı bırakırsanız, yeniden başarısız olursunuz.

> [!WARNING]
> Windows Server 2008 R2 SP1 fiziksel sunucusu, korumalı ve Azure 'a yük devretmelidir, yeniden başarısız olur.

> [!NOTE]
> VMware sanal makinelerinin yükünü devretmek için bir Hyper-V konağına geri dönemezsiniz.


- Devam etmeden önce, sanal makinelerin çoğaltılan bir durumda olması için yeniden koru adımlarını tamamlayıp şirket içi siteye geri yük devretme işlemi başlatabilirsiniz. Daha fazla bilgi için bkz. [Azure 'dan şirket içine yeniden koruma](vmware-azure-reprotect.md).

- Yeniden çalışma yapmadan önce vCenter 'ın bağlı durumda olduğundan emin olun. Aksi takdirde, disklerin bağlantısının kesilmesi ve sanal makineye geri eklenmesi başarısız olur.

- Azure 'a yük devretme sırasında şirket içi site erişilebilir olmayabilir ve yapılandırma sunucusu kullanılamıyor ya da kapatılabilir olabilir. Yeniden koruma ve yeniden çalışma sırasında, şirket içi yapılandırma sunucusunun çalışıyor olması ve bağlı bir Tamam durumunda olması gerekir. 

- Yeniden çalışma sırasında, sanal makinenin yapılandırma sunucusu veritabanında mevcut olması gerekir veya yeniden çalışma başarılı olmayacaktır. Sunucunuzun düzenli olarak zamanlanmış yedeklemelerini aldığınızdan emin olun. Bir olağanüstü durum oluşursa, yeniden çalışma işleminin çalışması için sunucuyu özgün IP adresine geri yüklemeniz gerekir.

- Yeniden korumayı/yeniden çalışmayı tetiklemeden önce ana hedef sunucunun anlık görüntü sahibi olmaması gerekir.

## <a name="overview-of-failback"></a>Yeniden çalışma için genel bakış
Azure 'a yük devretmenin ardından, aşağıdaki adımları yürüterek şirket içi sitenize geri dönebilirsiniz:

1. Azure üzerindeki sanal makineleri, şirket içi sitenizdeki VMware sanal makinelerine çoğaltmaya başlayacak şekilde [yeniden koruyun](vmware-azure-reprotect.md) . Bu işlemin bir parçası olarak şunları da yapmanız gerekir:

    * Bir şirket içi ana hedef ayarlayın. Windows sanal makineleri için bir Windows ana hedefi ve Linux sanal makineleri için bir [Linux ana hedefi](vmware-azure-install-linux-master-target.md) kullanın.
    * [İşlem sunucusu](vmware-azure-set-up-process-server-azure.md)ayarlayın.
    * Şirket içi sanal makineyi kapatmak ve Azure sanal makinesinin verilerini şirket içi disklerle eşleştirmek için [yeniden koruma](vmware-azure-reprotect.md) başlatın.

2. Azure 'daki sanal makineleriniz şirket içi sitenize çoğaltıldıktan sonra, Azure 'dan şirket içi siteye bir yük devretme işlemi başlatabilirsiniz.

3. Verileriniz yeniden başarısız olduktan sonra, şirket içi sanal makineleri Azure 'a çoğaltmaya başlayacak şekilde yeniden korumalısınız.

Hızlı bir genel bakış için, şirket içi bir siteye geri dönme hakkında aşağıdaki videoyu izleyin:
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="steps-to-fail-back"></a>Yeniden çalıştırma adımları

> [!IMPORTANT]
> Yeniden çalışmaya başlamadan önce, sanal makinelerin yeniden korunmasını tamamladığınızdan emin olun. Sanal makineler korumalı durumda olmalıdır ve sistem durumu **Tamam**olmalıdır. Sanal makineleri yeniden korumak için, [nasıl yeniden koruyacağınızı](vmware-azure-reprotect.md)okuyun.

1. Çoğaltılan öğeler sayfasında, sanal makineyi seçin. **Planlanmamış yük devretmeyi**seçmek için sağ tıklayın.
2. **Yük devretmeyi onaylayın**bölümünde yük devretme yönünü (Azure 'dan) doğrulayın. Yük devretme için kullanmak istediğiniz kurtarma noktasını (en son veya en son uygulamayla tutarlı) seçin. Uygulamayla tutarlı nokta en son zaman noktası arkasında ve veri kaybına neden olur.
3. Yük devretme sırasında Site Recovery Azure 'daki sanal makineleri kapatır. Yeniden çalışma 'nin beklenen şekilde tamamlandığını kontrol ettikten sonra, Azure 'da sanal makinelerin kapatılmasını kontrol edebilirsiniz.
4. Yük devredilen sanal makineyi Azure 'dan kaldırmak için **kayıt** gereklidir. Korunan öğeye sağ tıklayın ve ardından **Yürüt**' ü seçin. Bir iş, Azure 'daki yük devredilen sanal makineleri kaldırır.


## <a name="to-what-recovery-point-can-i-fail-back-the-virtual-machines"></a>Sanal makineleri hangi kurtarma noktasına geri gönderebilirim?

Yeniden çalışma sırasında, sanal makineyi/kurtarma planını geri yüklemek için iki seçeneğe sahip olursunuz.

- En son işlenen zaman noktasını seçerseniz, tüm sanal makineler zaman içinde en son kullanılabilir noktaya devreder. Kurtarma planı içinde bir çoğaltma grubu varsa, çoğaltma grubunun her sanal makinesi, zaman içinde en son bağımsız noktaya devreder.

  Bir sanal makineyi en az bir kurtarma noktasına ulaşana kadar geri alamazsınız. Tüm sanal makineleri en az bir kurtarma noktasına sahip olana kadar kurtarma planını geri alamazsınız.

  > [!NOTE]
  > En son kurtarma noktası, kilitlenme ile tutarlı bir kurtarma noktasıdır.

- Uygulamayla tutarlı kurtarma noktasını seçerseniz, tek bir sanal makine yeniden çalışması, kullanılabilir en son uygulamayla tutarlı kurtarma noktasına kurtarır. Çoğaltma grubu olan bir kurtarma planı durumunda, her bir çoğaltma grubu ortak kullanılabilir kurtarma noktasına kurtarır.
Uygulamayla tutarlı kurtarma noktaları zaman içinde kalabilir ve verilerde kayıp olabilir.

## <a name="what-happens-to-vmware-tools-post-failback"></a>VMware araçlarının yeniden çalışma sonrası ne olur?

Windows sanal makinesi söz konusu olduğunda, Site Recovery yük devretme sırasında VMware araçlarını devre dışı bırakır. Windows sanal makinesi yeniden çalışma sırasında, VMware araçları yeniden etkinleştirilir. 


## <a name="reprotect-from-on-premises-to-azure"></a>Şirket içinden Azure 'a yeniden koruma
Yeniden çalışma tamamlandıktan ve yürütmeyi başlattıktan sonra, Azure 'daki kurtarılan sanal makineler silinir. Artık, sanal makine Şirket içi siteye geri yüklendi, ancak korunmaz. Tekrar Azure 'a Çoğaltmaya başlamak için aşağıdakileri yapın:

1.  > **kasa** seçin > **çoğaltılan öğeleri** **Ayarla** , geri dönecek sanal makineleri seçin ve ardından **yeniden koru**seçeneğini belirleyin.
2. Verileri Azure 'a geri göndermek için kullanılması gereken işlem sunucusunun değerini girin.
3. Yeniden koru işini başlatmak için **Tamam ' ı** seçin.

> [!NOTE]
> Bir şirket içi sanal makine önyüklendiğinde, aracının yapılandırma sunucusuna (15 dakikaya kadar) kaydolması biraz zaman alır. Bu süre boyunca yeniden koruma başarısız olur ve aracının yüklenmediğini belirten bir hata iletisi döndürür. Birkaç dakika bekleyip yeniden korumayı deneyin.

## <a name="next-steps"></a>Sonraki adımlar

Yeniden koruma işi tamamlandıktan sonra, sanal makine Azure 'a geri çoğaltılır ve sanal makinelerinizi tekrar Azure 'a taşımak için bir [Yük devretme](site-recovery-failover.md) işlemi gerçekleştirebilirsiniz.


