---
title: Azure Site Recovery eklenen bir Azure VM diski için çoğaltmayı etkinleştirme
description: Bu makalede, Azure Site Recovery ile olağanüstü durum kurtarma için etkinleştirilen bir Azure VM 'sine eklenen bir disk için çoğaltmanın nasıl etkinleştirileceği açıklanır.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: asgang
ms.openlocfilehash: 6cbe71d67417ce817d317b65f27d0e6ceabec983
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084998"
---
# <a name="enable-replication-for-a-disk-added-to-an-azure-vm"></a>Azure VM 'ye eklenen bir disk için çoğaltmayı etkinleştirme


Bu makalede, [Azure Site Recovery](site-recovery-overview.md)kullanarak başka bir Azure bölgesine olağanüstü durum kurtarma için zaten etkinleştirilmiş BIR Azure VM 'sine eklenen veri diskleri için çoğaltmanın nasıl etkinleştirileceği açıklanır.

VM 'ye eklediğiniz bir disk için çoğaltmanın etkinleştirilmesi, yönetilen disklere sahip Azure VM 'Leri için desteklenir.

Başka bir Azure bölgesine çoğaltılan bir Azure VM 'ye yeni bir disk eklediğinizde, aşağıdakiler gerçekleşir:

-   VM için çoğaltma durumu bir uyarı gösterir ve portalda bir notun bir veya daha fazla diskin koruma için kullanılabilir olduğunu bildirir.
-   Eklenen diskler için korumayı etkinleştirirseniz, diskin ilk çoğaltmadan sonra uyarı kaybolur.
-   Disk için çoğaltmayı etkinleştiremeyebilir ' i seçerseniz, uyarıyı kapatmak için seçeneğini belirleyebilirsiniz.

![Yeni disk eklendi](./media/azure-to-azure-enable-replication-added-disk/newdisk.png)



## <a name="before-you-start"></a>Başlamadan önce

Bu makalede, diski eklediğiniz VM için zaten olağanüstü durum kurtarmayı ayarlamış olduğunuz varsayılmaktadır. Yapmadıysanız, Azure 'dan [Azure 'a olağanüstü durum kurtarma öğreticisini](azure-to-azure-tutorial-enable-replication.md)takip edin. 

## <a name="enable-replication-for-an-added-disk"></a>Eklenen disk için çoğaltmayı etkinleştirme 

Eklenen bir disk için çoğaltmayı etkinleştirmek üzere aşağıdakileri yapın:

1. Kasaya **çoğaltılan öğeleri**>, DISKI eklediğiniz VM 'ye tıklayın.
2. **Diskler**' e tıklayın ve ardından çoğaltmayı etkinleştirmek istediğiniz veri diskini seçin (bu disklerde **korumalı olmayan** bir durum vardır).
3.  **Disk ayrıntıları**' nda **çoğaltmayı etkinleştir**' e tıklayın.

    ![Eklenen disk için çoğaltmayı etkinleştir](./media/azure-to-azure-enable-replication-added-disk/enabled-added.png)

Çoğaltmayı etkinleştirme işi çalıştıktan ve ilk çoğaltma tamamlandıktan sonra, disk sorunu için çoğaltma sistem durumu uyarısı kaldırılır.



## <a name="next-steps"></a>Sonraki adımlar

Yük devretme testi çalıştırma hakkında [daha fazla bilgi edinin](site-recovery-test-failover-to-azure.md) .
