---
title: Azure Site Recovery ile ikincil Hyper-V sitesine yük devretme/yeniden çalışma ayarlama
description: Hyper-V VM 'lerini ikincil şirket içi sitenize yük devretme ve Azure Site Recovery olağanüstü durum kurtarma sırasında birincil siteye geri dönme hakkında bilgi edinin.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: d31355bcb0ce42874c19988738ba06138c7a0b7c
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082595"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-your-secondary-on-premises-site"></a>İkincil şirket içi sitenize çoğaltılan Hyper-V VM 'lerinin yükünü devreder ve geri devreder

[Azure Site Recovery](site-recovery-overview.md) hizmeti, şirket içi makinelerin ve Azure sanal makinelerinin (VM 'ler) çoğaltma, yük devretme ve yeniden çalışma işlemlerini yönetir ve düzenler.

Bu makalede, bir System Center Virtual Machine Manager (VMM) bulutunda yönetilen Hyper-V VM 'sinin ikincil bir VMM sitesine yük devretme işlemi açıklanır. Yük devrettikten sonra şirket içi siteniz kullanılabilir olduğunda yeniden çalıştırabilirsiniz. Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> * Birincil VMM bulutundaki Hyper-V VM 'sinin ikincil VMM bulutuna yük devretme
> * İkincil siteden birinciye yeniden koruma ve yeniden çalıştırma
> * İsteğe bağlı olarak birincili ikinciye çoğaltmayı yeniden başlatın

## <a name="failover-and-failback"></a>Yük devretme ve yeniden çalışma

Yük devretme ve yeniden çalışma üç aşamaya sahiptir:

1. **İkincil siteye yük devretme**: birincil siteden ikincil makineye yük devreder.
2. **İkincil siteden geri yük devretmek**: VM 'leri ikinciden birinciye çoğaltın ve geri yük devretmek için planlanmış bir yük devretme çalıştırın.
3. Planlı Yük devretmenin ardından, isteğe bağlı olarak birincil siteden ikincil konuma çoğaltmayı yeniden başlatın.


## <a name="prerequisites"></a>Önkoşullar

- Her şeyin beklendiği gibi çalıştığını denetlemek için bir [olağanüstü durum kurtarma detayının](hyper-v-vmm-test-failover.md) tamamlandığından emin olun.
- Yeniden çalışmayı gerçekleştirmek için, birincil ve ikincil VMM sunucularının Site Recovery bağlı olduğundan emin olun.



## <a name="run-a-failover-from-primary-to-secondary"></a>Birincili ikinciye yük devretme çalıştırma

Hyper-V VM 'Leri için düzenli veya planlı yük devretme çalıştırabilirsiniz.

- Beklenmeyen kesintiler için düzenli yük devretme kullanın. Bu yük devretmeyi çalıştırdığınızda, Site Recovery ikincil sitede bir VM oluşturur ve onu güçlendirir. Veri kaybı, eşitlenmemiş bekleyen verilere bağlı olarak gerçekleşebilir.
- Planlanmış bir yük devretme, bakım için veya beklenen kesinti sırasında kullanılabilir. Bu seçenek sıfır veri kaybı sağlar. Planlı bir yük devretme tetiklendiğinde, kaynak VM 'Ler kapatılır. Eşitlenmemiş veriler eşitlenir ve yük devretme tetiklenir. 
- 
  Bu yordamda, düzenli bir yük devretmenin nasıl çalıştırılacağı açıklanmaktadır.


1. **Ayarlar** > **Çoğaltılan öğeler** bölümünde VM > **Yük devretme**’ye tıklayın.
1. Yük devretmeyi tetiklemeden önce Site Recovery kaynak VM 'Leri kapatmayı denemek istiyorsanız, **yük devretmeye başlamadan önce makineyi Kapat ' ı** seçin. Site Recovery, yük devretmeyi tetiklemeden önce ikincil siteye gönderilmemiş olan şirket içi verileri de eşitlemeye çalışacaktır. Yük devretme işleminin başarısız olması durumunda bile devam ettiğini unutmayın. Yük devretme işlemini **İşler** sayfasında takip edebilirsiniz.
2. Artık ikincil VMM bulutunda VM 'yi görebilmelisiniz.
3. VM 'yi doğruladıktan sonra, yük devretmeyi **yürütün** . Bu, tüm kullanılabilir kurtarma noktalarını siler.

> [!WARNING]
> **Devam eden yük devretme işlemini iptal etmeyin**: Yük devretme başlatılmadan önce VM çoğaltma durdurulur. Devam eden bir yük devretme işlemini iptal ederseniz yük devretme durdurulur, ancak VM yeniden çoğaltılamaz.  


## <a name="reverse-replicate-and-failover"></a>Çoğaltmayı tersine çevirme ve yük devretme

İkincil siteden birincil siteye çoğaltma başlatın ve birincil siteye geri dönün. VM 'Ler birincil sitede yeniden çalıştırıldıktan sonra, bunları ikincil siteye çoğaltabilirsiniz.  

 
1. SANAL makineye tıklayın > **çoğaltmayı tersine çevir**' e tıklayın.
2. İş tamamlandıktan sonra **Yük DEVRETMEDE**VM > tıklatın, yük devretme yönünü doğrulayın (ikincil VMM bulutunda) ve kaynak ve hedef konumları seçin. 
4. Yük devretmeyi başlatın. Yük devretme işleminin ilerleme durumunu **İşler** sekmesinden takip edebilirsiniz.
5. Birincil VMM bulutunda, VM 'nin kullanılabilir olup olmadığını denetleyin.
6. Birincil VM 'yi yeniden ikincil siteye Çoğaltmaya başlamak istiyorsanız, **çoğaltmayı tersine çevir**' e tıklayın.

## <a name="next-steps"></a>Sonraki adımlar
Hyper-V VM 'lerini ikincil bir siteye çoğaltma [adımını gözden geçirin](hyper-v-vmm-disaster-recovery.md) .
