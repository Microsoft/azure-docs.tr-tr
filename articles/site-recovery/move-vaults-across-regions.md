---
title: Azure Site Recovery kasasını başka bir bölgeye taşıma
description: Kurtarma Hizmetleri kasasının (Azure Site Recovery) başka bir Azure bölgesine nasıl taşınacağını açıklar
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 32dff9a165125ab1949560ce36438ae266cd3036
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74090307"
---
# <a name="move-a-recovery-services-vault-and-azure-site-recovery-configuration-to-another-azure-region"></a>Kurtarma Hizmetleri kasasını ve Azure Site Recovery yapılandırmasını başka bir Azure bölgesine taşıma

Mevcut Azure kaynaklarınızı bir bölgeden diğerine taşımak isteyebileceğiniz çeşitli senaryolar vardır. Bu örnekler yönetilebilirlik, idare nedenleri veya şirket birleşmeleri ve alımlar nedeniyle yapılır. Azure VM 'lerinizi taşırken taşımak isteyebileceğiniz ilgili kaynaklardan biri olağanüstü durum kurtarma yapılandırması olur. 

Mevcut bir olağanüstü durum kurtarma yapılandırmasını bir bölgeden diğerine taşımanın ilk sınıf yolu yoktur. Bunun nedeni, hedef bölgenizi kaynak VM bölgenize göre yapılandırdığınıza bağlıdır. Kaynak bölgeyi değiştirmeye karar verirken, hedef bölgenin daha önce var olan yapılandırmalarının yeniden kullanılması ve sıfırlanması gerekir. Bu makale, olağanüstü durum kurtarma kurulumunu yeniden yapılandırmak ve farklı bir bölgeye taşımak için adım adım işlemi tanımlar.

Bu belgede şunları yapmanız gerekir:

> [!div class="checklist"]
> * Taşıma için önkoşulları doğrulayın.
> * Azure Site Recovery tarafından kullanılan kaynakları belirler.
> * Çoğaltmayı devre dışı bırakın.
> * Kaynakları silin.
> * VM 'Lerin yeni kaynak bölgesine göre Site Recovery ayarlayın.

> [!IMPORTANT]
> Şu anda, kurtarma hizmetleri kasasını ve olağanüstü durum kurtarma yapılandırmasını farklı bir bölgeye taşımak için birinci sınıf bir yol yoktur. Bu makale, çoğaltmayı devre dışı bırakma ve yeni bölgede ayarlama sürecinde size rehberlik eder.

## <a name="prerequisites"></a>Önkoşullar

- Azure VM 'lerini farklı bir bölgeye taşımayı denemeden önce olağanüstü durum kurtarma yapılandırmasını kaldırıp sildiğinizden emin olun. 

  > [!NOTE]
  > Azure VM için yeni hedef bölgeniz olağanüstü durum kurtarma hedefi bölgesiyle aynıysa, mevcut çoğaltma yapılandırmanızı kullanabilir ve taşıyabilirsiniz. [Azure IaaS VM 'lerini başka bir Azure bölgesine taşıma](azure-to-azure-tutorial-migrate.md)bölümündeki adımları izleyin.

- Bilinçli bir karar verirken ve paydaşların bilgilendirilmesi olduğunuzdan emin olun. VM 'nin taşınması tamamlanana kadar VM 'niz olağanüstü durumlara karşı korunmaz.

## <a name="identify-the-resources-that-were-used-by-azure-site-recovery"></a>Azure Site Recovery tarafından kullanılan kaynakları belirler
Bir sonraki adıma geçmeden önce bu adımı yapmanızı öneririz. VM 'Ler çoğaltılırken ilgili kaynakları belirlemek daha kolay.

Çoğaltılan her bir Azure sanal makinesi için, **korunan** öğeler > **çoğaltılan öğeler** > **Özellikler** ' e gidin ve aşağıdaki kaynakları tanımla:

- Hedef kaynak grubu
- Önbellek depolama hesabı
- Hedef depolama hesabı (yönetilmeyen disk tabanlı bir Azure VM 'si olması durumunda) 
- Hedef ağ


## <a name="disable-the-existing-disaster-recovery-configuration"></a>Mevcut olağanüstü durum kurtarma yapılandırmasını devre dışı bırak

1. Kurtarma Hizmetleri kasasına gidin.
2. **Yinelenen öğeler** > **korunan öğeler** ' de makineye sağ tıklayın ve **çoğaltmayı devre dışı bırak**' ı seçin.
3. Taşımak istediğiniz tüm VM 'Ler için bu adımı tekrarlayın.

> [!NOTE]
> Mobility hizmeti korunan sunuculardan kaldırılmaz. El ile kaldırmanız gerekir. Sunucuyu yeniden korumayı planlıyorsanız Mobility hizmetini kaldırmayı atlayabilirsiniz.

## <a name="delete-the-resources"></a>Kaynakları Sil

1. Kurtarma Hizmetleri kasasına gidin.
2. **Sil**’i seçin.
3. [Daha önce tanımlamış](#identify-the-resources-that-were-used-by-azure-site-recovery)olduğunuz diğer tüm kaynakları silin.
 
## <a name="move-azure-vms-to-the-new-target-region"></a>Azure VM 'lerini yeni hedef bölgeye taşıma

Azure VM 'lerini hedef bölgeye taşıma gereksiniminize bağlı olarak bu makalelerdeki adımları izleyin:

- [Azure VM’lerini başka bir bölgeye taşıma](azure-to-azure-tutorial-migrate.md)
- [Azure VM'lerini Kullanılabilirlik Alanlarına taşıma](move-azure-VMs-AVset-Azone.md)

## <a name="set-up-site-recovery-based-on-the-new-source-region-for-the-vms"></a>VM 'Ler için yeni kaynak bölgeye göre Site Recovery ayarlama

[Azure VM 'leri için olağanüstü durum kurtarmayı ayarlama](azure-to-azure-tutorial-enable-replication.md)bölümündeki adımları izleyerek yeni bölgeye taşınan Azure VM 'leri için olağanüstü durum kurtarmayı yapılandırın.
