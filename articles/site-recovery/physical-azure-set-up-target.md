---
title: Azure Site Recovery ' deki fiziksel sunucular için hedef ortamı ayarlama
description: Bu makalede, Azure Site Recovery kullanarak fiziksel sunucuların olağanüstü durum kurtarması için hedef Azure ortamının nasıl ayarlanacağı açıklanır.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 685f04b27cd315b04ef5c45e155e825eebe2747b
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953893"
---
# <a name="prepare-target-vmware-to-azure"></a>Hedefi hazırla (VMware 'den Azure 'a)

Bu makalede, Azure ortamınızı Windows veya Linux çalıştıran fiziksel sunucuları (x64) Azure 'a Çoğaltmaya başlamak üzere nasıl hazırlayacağınız açıklanır.

## <a name="prerequisites"></a>Önkoşullar

Makalede şunları varsayılmaktadır:
- Fiziksel sunucularınızı korumak için bir kurtarma hizmetleri Kasası oluşturdunuz. [Azure Portal](https://portal.azure.com "Azure portalında")bir kurtarma hizmetleri Kasası oluşturabilirsiniz.
- [Şirket içi ortamınızı](physical-azure-disaster-recovery.md) fiziksel sunucuları Azure 'a çoğaltmak için ayarlayın.

## <a name="prepare-target"></a>Hedefi hazırla

**1. adımı tamamladıktan sonra koruma hedefini seçin** ve **Adım 2: kaynağı hazırla**, adım **3: hedef** ' e yönlendirilirsiniz

![Hedefi hazırla](./media/physical-azure-set-up-target/prepare-target-physical-to-azure.png)

1. **Abonelik:** Aşağı açılan menüden, fiziksel sunucularınızı çoğaltmak istediğiniz aboneliği seçin.
2. **Dağıtım modeli:** Dağıtım modelini (klasik veya Kaynak Yöneticisi) seçin

Seçilen dağıtım modeline bağlı olarak, fiziksel sunucularınızın çoğaltılmasını ve yük devretmesini sağlamak için hedef abonelikte en az bir uyumlu depolama hesabınız ve sanal ağınızın olduğundan emin olmak üzere bir doğrulama çalıştırılır.

Doğrulamalar başarıyla tamamlandıktan sonra, sonraki adıma geçmek için Tamam ' a tıklayın.

Uyumlu bir Kaynak Yöneticisi depolama hesabınız veya sanal ağınız yoksa, sayfanın üst kısmındaki **+ depolama hesabı** veya **+ ağ** düğmelerine tıklayarak bir tane oluşturabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
[Çoğaltma ayarlarını yapılandırın](vmware-azure-set-up-replication.md).
