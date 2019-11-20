---
title: Azure Site Recovery VMware VM çoğaltma hedefini hazırlama
description: Bu makalede, Azure 'a VMware VM çoğaltması için hedef Azure ortamınızı hazırlama açıklanır.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: c721234f2e1d806d51d31f3466e441bf8360f6b8
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693175"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>VMware VM 'Leri veya fiziksel sunucuları Azure 'a olağanüstü durum kurtarma için hedef ortamı hazırlama

Bu makalede, hedef Azure ortamınızı VMware sanal makinelerini veya fiziksel sunucuları Azure 'a Çoğaltmaya başlamak için nasıl hazırlayacağınız açıklanır.

## <a name="prerequisites"></a>Ön koşullar

Makalede şunları varsayılmaktadır:
- Kaynak makinelerinizi korumak için [Azure Portal](https://portal.azure.com "Azure portal") bir kurtarma hizmetleri Kasası oluşturdunuz
- Şirket içi ortamınızı, kaynak [VMware sanal makinelerini](vmware-azure-set-up-source.md) veya [fiziksel sunucuları](physical-azure-set-up-source.md) Azure 'a çoğaltmak için ayarlayın.

## <a name="prepare-target"></a>Hedefi hazırla

**1. adımı tamamladıktan sonra koruma hedefini seçin** ve **Adım 2: kaynağı hazırla**, adım **3: hedef** ' e yönlendirilirsiniz

![Hedefi hazırla](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Abonelik:** Açılan menüden, sanal makinelerinizi veya fiziksel sunucularınızı çoğaltmak istediğiniz aboneliği seçin.
2. **Dağıtım modeli:** Dağıtım modelini (klasik veya Kaynak Yöneticisi) seçin

Seçilen dağıtım modeline bağlı olarak, hedef abonelikte sanal makinenize veya fiziksel sunucunuza çoğaltma ve yük devretme için en az bir sanal ağınız olduğundan emin olmak için bir doğrulama çalıştırılır.

Doğrulamalar başarıyla tamamlandıktan sonra, sonraki adıma geçmek için Tamam ' a tıklayın.

Bir sanal ağınız yoksa, sayfanın üst kısmındaki **+ ağ** düğmesine tıklayarak bir tane oluşturabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
[Çoğaltma ayarlarını yapılandırın](vmware-azure-set-up-replication.md).
