---
title: Azure Site Recovery ile şirket içi olağanüstü durum kurtarma için ağ bağdaştırıcılarını yönetme
description: Azure 'da şirket içi olağanüstü durum kurtarma için ağ arabirimlerinin Azure Site Recovery ile nasıl yönetileceğini açıklar
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 2a4752b501e40f9e8a4f3bc82cb2533c11f9e526
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954599"
---
# <a name="manage-vm-network-interfaces-for-on-premises-disaster-recovery-to-azure"></a>Azure 'da şirket içi olağanüstü durum kurtarma için VM ağ arabirimlerini yönetme

Azure 'daki bir sanal makineye (VM) en az bir ağ arabirimi eklenmiş olmalıdır. VM boyutunun desteklediğinden, kendisine bağlı çok sayıda ağ arabirimi olabilir.

Varsayılan olarak, bir Azure sanal makinesine bağlı ilk ağ arabirimi, birincil ağ arabirimi olarak tanımlanır. Sanal makinedeki diğer tüm ağ arabirimleri ikincil ağ arabirimlerdir. Ayrıca, varsayılan olarak, sanal makinedeki tüm giden trafik, birincil ağ arabiriminin birincil IP yapılandırmasına atanan IP adresine gönderilir.

Şirket içi bir ortamda, sanal makineler veya sunucular, ortamda farklı ağlar için birden çok ağ arabirimine sahip olabilir. Farklı ağlar genellikle yükseltmeler, bakım ve internet erişimi gibi belirli işlemleri gerçekleştirmek için kullanılır. Şirket içi bir ortamdan Azure 'a geçiş yaparken veya yük devrettikten sonra, aynı sanal makinede bulunan ağ arabirimlerinin tümünün aynı sanal ağa bağlı olması gerektiğini aklınızda bulundurun.

Azure Site Recovery, varsayılan olarak, Azure sanal makinesinde şirket içi sunucuya bağlı olarak çok sayıda ağ arabirimi oluşturur. Çoğaltma veya yük devretme sırasında, çoğaltılan sanal makinenin ayarları altındaki Ağ arabirimi ayarlarını düzenleyerek yedek ağ arabirimleri oluşturmaktan kaçınabilirsiniz.

## <a name="select-the-target-network"></a>Hedef ağı seçin

VMware ve fiziksel makineler için ve Hyper-V (System Center Virtual Machine Manager olmadan) sanal makineler için, hedef sanal ağı ayrı sanal makineler için belirtebilirsiniz. Virtual Machine Manager ile yönetilen Hyper-V sanal makineleri için, bir kaynak Virtual Machine Manager sunucusundaki VM ağlarını eşlemek ve Azure ağlarını hedeflemek için [ağ eşleme](site-recovery-network-mapping.md) kullanın.

1. Kurtarma Hizmetleri kasasındaki **çoğaltılan öğeler** altında, çoğaltılan öğenin ayarlarına erişmek için çoğaltılan öğeleri seçin.

2. Çoğaltılan öğenin ağ ayarlarına erişmek için **işlem ve ağ** sekmesini seçin.

3. **Ağ özellikleri**altında, kullanılabilir ağ arabirimleri listesinden bir sanal ağ seçin.

    ![Ağ ayarları](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

Hedef ağın değiştirilmesi söz konusu sanal makineye ait tüm ağ arabirimlerini etkiler.

Virtual Machine Manager bulutları için Ağ eşlemesini değiştirme tüm sanal makineleri ve bunların ağ arabirimlerini etkiler.

## <a name="select-the-target-interface-type"></a>Hedef arabirim türünü seçin

**İşlem ve ağ** bölmesinin **ağ arabirimleri** bölümünde, ağ arabirimi ayarlarını görüntüleyebilir ve düzenleyebilirsiniz. Hedef ağ arabirim türünü de belirtebilirsiniz.

- Yük devretme için bir **birincil** ağ arabirimi gerekir.
- Varsa, diğer tüm seçili ağ arabirimleri **İkincil** ağ arabirimlerdir.
- Yük devretmede bir ağ arabirimini oluşturmadan dışlamak için **kullanmayın** ' ı seçin.

Varsayılan olarak, çoğaltmayı etkinleştirirken Site Recovery, şirket içi sunucuda algılanan tüm ağ arabirimlerini seçer. Bunlardan birini **birincil** olarak, diğerlerinin tümünü **İkincil**olarak işaretler. Şirket içi sunucuya eklenen sonraki tüm arabirimler varsayılan olarak **kullanılmaz** . Daha fazla ağ arabirimi eklerken, gereken tüm ağ arabirimlerine uyum sağlamak için doğru Azure sanal makine hedef boyutunun seçildiğinden emin olun.

## <a name="modify-network-interface-settings"></a>Ağ arabirimi ayarlarını değiştir

Çoğaltılan bir öğenin ağ arabirimlerinin alt ağını ve IP adresini değiştirebilirsiniz. Bir IP adresi belirtilmemişse, Site Recovery yük devretme sırasında alt ağdan ağ arabirimine bir sonraki kullanılabilir IP adresini atayacaktır.

1. Ağ arabirimi ayarlarını açmak için kullanılabilir ağ arabirimini seçin.

2. Kullanılabilir alt ağlar listesinden istenen alt ağı seçin.

3. İstenen IP adresini girin (gerektiğinde).

    ![Ağ arabirimi ayarları](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Düzenlemeden **sonra işlem ve ağ** bölmesine geri dönmek için **Tamam** ' ı seçin.

5. Diğer ağ arabirimleri için 1-4 adımlarını yineleyin.

6. Tüm değişiklikleri kaydetmek için **Kaydet** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar
  Azure sanal makineleri için ağ arabirimleri hakkında [daha fazla bilgi edinin](../virtual-network/virtual-network-network-interface-vm.md) .
