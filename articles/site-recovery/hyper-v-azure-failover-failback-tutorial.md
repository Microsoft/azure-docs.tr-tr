---
title: Azure Site Recovery 'de Hyper-V VM 'Leri için yük devretme ve yeniden çalışma ayarlama
description: Azure Site Recovery hizmetini kullanarak olağanüstü durum kurtarma sırasında Hyper-V VM'lerinde yük devretme ve yeniden çalışma gerçekleştirmeyi öğrenin.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: a8c197c2f0875bb31d091fb5839730ee1568b471
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082644"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>Azure'da çoğaltılan Hyper-V VM'lerinde yük devretme ve yeniden çalışma

Bu öğreticide, bir Hyper-V VM’de Azure’a nasıl yük devredileceği açıklanmaktadır. Yük devrettikten sonra şirket içi siteniz kullanılabilir olduğunda yeniden çalıştırabilirsiniz. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure gereksinimleriyle uyumlu olup olmadığını denetlemek için Hyper-V VM özelliklerini doğrulama
> * Azure'a yük devretme işlemini çalıştırma
> * Azure’dan şirket içine yeniden çalışma
> * Tekrar Azure’a çoğaltmaya başlamak için şirket içi VM’leri tersine çoğaltma

Bu öğretici, serideki beşinci öğreticidir. Önceki öğreticilerdeki adımları zaten tamamladığınız varsayılır.    

1. [Azure’u hazırlama](tutorial-prepare-azure.md)
2. [Şirket içi Hyper-V’leri hazırlama](tutorial-prepare-on-premises-hyper-v.md)
3. [Hyper-V VM'leri](tutorial-hyper-v-to-azure.md) veya [System Center VMM bulutlarında yönetilen Hyper-V VM'leri](tutorial-hyper-v-vmm-to-azure.md) için olağanüstü durum kurtarmayı ayarlama
4. [Olağanüstü durum kurtarma tatbikatı çalıştırma](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Yük devretme ve yeniden çalışma için hazırlanma

VM'de anlık görüntü olmadığından ve şirket içi VM'nin yeniden çalışma sırasında kapalı olduğundan emin olun. Bu işlem, çoğaltma sırasında veri tutarlığını sağlar. Yeniden çalışma sırasında şirket içi VM'yi açmayın. 

Yük devretme ve yeniden çalışma üç aşamalıdır:

1. **Azure'a yük devretme**: Şirket içi Hyper-V VM'lerinden Azure'a yük devretme gerçekleştirin.
2. **Şirket içinde yeniden çalışma**: Şirket içi site kullanılabilir duruma geldiğinde Azure VM'lerinden şirket içine yük devretme gerçekleştirin. Azure'daki veriler şirket içi siteyle eşitlenmeye başlar ve tamamlandığında şirket içindeki VM'ler açılır.  
3. **Şirket içi VM'lerde tersine çoğaltma**: Şirket içinde yeniden çalışma süreci tamamlandıktan sonra şirket içindeki VM'leri Azure'da çoğaltmaya başlamak için tersine çoğaltma gerçekleştirin.

## <a name="verify-vm-properties"></a>VM özelliklerini doğrulama

Yük devretme öncesinde VM özelliklerini doğrulayın ve VM’nin [Azure gereksinimlerini](hyper-v-azure-support-matrix.md#replicated-vms) karşıladığından emin olun.

**Korunan Öğeler**’de **Çoğaltılan Öğeler** > VM seçeneğine tıklayın.

1. **Çoğaltılan öğe** bölmesinde VM bilgileri ile sistem durumunun bir özeti ve kullanılabilen son kurtarma noktaları yer alır. Daha fazla ayrıntı görüntülemek için **Özellikler**’e tıklayın.

1. **İşlem ve Ağ** bölümünde Azure adını, kaynak grubunu, hedef boyutunu, [kullanılabilirlik kümesini](../virtual-machines/windows/tutorial-availability-sets.md) ve yönetilen disk ayarlarını değiştirebilirsiniz.

1. Ağ ayalarını, yük devretmeden sonra Azure VM’nin yerleştirileceği ağ/alt ağ ve VM’ye atanacak IP adresi dahil olmak üzere görüntüleyebilir ve değiştirebilirsiniz.

1. **Diskler** bölümünde VM’deki işletim sistemi ve veri diskleriyle ilgili bilgileri görüntüleyebilirsiniz.

## <a name="failover-to-azure"></a>Azure'a yük devretme

1. **Ayarlar** > **Çoğaltılan öğeler** bölümünde VM > **Yük devretme**’ye tıklayın.
2. **Yük devretme** bölümünde **En son** kurtarma noktasını seçin. 
3. **Yük devretmeyi başlatmadan önce makineyi kapatın** seçeneğini belirleyin. Site Recovery, yük devretmeyi tetiklemeden önce kaynak VM'leri kapatmaya çalışır. Kapatma işlemi başarısız olsa bile yük devretme devam eder. Yük devretme işlemini **İşler** sayfasında takip edebilirsiniz.
4. Yük devretmeyi doğruladıktan sonra **Yürüt**'e tıklayın. Bu işlem tüm kullanılabilir kurtarma noktalarını siler.

> [!WARNING]
> **Devam eden bir yük devretme işlemini iptal etmeyin**: Devam eden bir işlemi iptal ederseniz yük devretme durdurulur ancak VM yeniden çoğaltılmaz.

## <a name="failback-azure-vm-to-on-premises-and-reverse-replicate-the-on-premises-vm"></a>Azure VM'yi şirket içinde yeniden çalıştırma ve şirket içi VM'yi tersine çoğaltma

Yeniden çalışma işlemi aslında Azure'dan şirket içi siteye gerçekleştirilen yük devretme işlemidir ve tersine çoğaltma aşamasında şirket içi sitedeki VM'ler yeniden Azure'da çoğaltılmaya başlar.

1. **Ayarlar** > **Çoğaltılan öğeler** bölümünde VM > **Planlı Yük devretme**’ye tıklayın.
2. **Planlı Yük Devretme Onayı** sayfasında yük devretme yönünü (Azure'dan) onaylayıp kaynak ve hedef konumları seçin.
3. **Yük devretme öncesinde verileri eşitle (yalnızca değişiklik farklarını eşitle)** öğesini seçin. Bu seçenek VM'yi kapatmadan eşitleme yaptığından VM kesinti süresini en aza indirir.
4. Yük devretmeyi başlatın. Yük devretme işleminin ilerleme durumunu **İşler** sekmesinden takip edebilirsiniz.
5. İlk veri eşitleme işlemi tamamlandıktan ve Azure VM'lerini kapattıktan sonra **İşler** > planlı yük devretme işinin adı > **Yük Devretmeyi Tamamla**'ya tıklayın. Azure VM kapatılır, en son değişiklikler şirket içine aktarılır ve şirket içi VM başlatılır.
6. Şirket içi VM'de oturum açarak beklendiği gibi kullanılabilir durumda olup olmadığını denetleyin.
7. Şirket içi VM şimdi **Yürütme Bekleniyor** durumundadır. **Yürüt**'e tıklayın. Azure VM'lerini ve disklerini silip şirket içi VM'yi tersine çoğaltma için hazırlar.
Şirket içi VM'yi Azure'a çoğaltmaya başlamak için **Tersine Çoğaltma** seçeneğini etkinleştirin. Bu özellik Azure VM kapatıldıktan sonra gerçekleştirilen değişikliklerin çoğaltılmasını tetikler.  
