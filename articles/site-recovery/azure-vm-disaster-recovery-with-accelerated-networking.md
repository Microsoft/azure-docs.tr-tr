---
title: Azure VM olağanüstü durum kurtarma için Azure Site Recovery ile hızlandırılmış ağı etkinleştirme
description: Azure sanal makine olağanüstü durum kurtarma için Azure Site Recovery ile hızlandırılmış ağı etkinleştirmeyi açıklar
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 27691d8fab3e7c8ccd60351dc0be83898ff984ed
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622424"
---
# <a name="accelerated-networking-with-azure-virtual-machine-disaster-recovery"></a>Azure sanal makine olağanüstü durum kurtarma ile hızlandırılmış ağ

Hızlandırılmış ağ, bir VM 'ye tek köklü g/ç Sanallaştırması (SR-ıOV) sağlar ve ağ performansını büyük ölçüde geliştirir. Bu yüksek performanslı yol, desteklenen VM türlerinde en zorlu ağ iş yükleri ile kullanım için, gecikme süresi, değişim ve CPU kullanımını azaltan ana bilgisayarı veri yolundan atlar. Aşağıdaki resimde, hızlandırılmış ağ ile ve olmadan iki VM arasındaki iletişim gösterilmektedir:

![Karşılaştırma](./media/azure-vm-disaster-recovery-with-accelerated-networking/accelerated-networking-benefit.png)

Azure Site Recovery, farklı bir Azure bölgesine yük devredilen Azure sanal makineleri için hızlandırılmış ağ avantajlarından faydalanmanızı sağlar. Bu makalede, Azure Site Recovery ile çoğaltılan Azure sanal makineleri için hızlandırılmış ağı nasıl etkinleştirebileceğinizi anlatmaktadır.

## <a name="prerequisites"></a>Ön koşullar

Başlamadan önce, şunu anladığınızdan emin olun:
-   Azure sanal makine [çoğaltma mimarisi](azure-to-azure-architecture.md)
-   Azure sanal makineleri için [çoğaltmayı ayarlama](azure-to-azure-tutorial-enable-replication.md)
-   [Yük devrediliyor](azure-to-azure-tutorial-failover-failback.md) Azure sanal makineleri

## <a name="accelerated-networking-with-windows-vms"></a>Windows VM 'Leri ile hızlandırılmış ağ

Azure Site Recovery, çoğaltılan sanal makineler için yalnızca kaynak sanal makinede hızlandırılmış ağ etkinse hızlandırılmış ağ etkinleştirmeyi destekler. Kaynak sanal makinenizde hızlandırılmış ağ etkin değilse, Windows sanal makineleri için hızlandırılmış ağı nasıl etkinleştirebileceğinizi [buradan](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)öğrenebilirsiniz.

### <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri
Aşağıdaki dağıtımlar Azure galerisindeki kutudan çıkar:
* **Windows Server 2016 Datacenter**
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>Desteklenen VM örnekleri
Hızlandırılmış ağ, 2 veya daha fazla vCPU ile en genel amaçlı ve işlem için iyileştirilmiş örnek boyutlarında desteklenir.  Desteklenen bu seriler şunlardır: D/DSv2 ve F/FS

Hiper iş parçacığı destekleyen örneklerde, hızlandırılmış ağ, 4 veya daha fazla vCPU içeren VM örneklerinde desteklenir. Desteklenen Seriler: D/DSv3, E/ESv3, Fsv2 ve MS/MMS

VM örnekleri hakkında daha fazla bilgi için bkz. [WINDOWS VM boyutları](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="accelerated-networking-with-linux-vms"></a>Linux VM 'lerle hızlandırılmış ağ

Azure Site Recovery, çoğaltılan sanal makineler için yalnızca kaynak sanal makinede hızlandırılmış ağ etkinse hızlandırılmış ağ etkinleştirmeyi destekler. Kaynak sanal makinenizde hızlandırılmış ağ etkin değilse, Linux [sanal makineleri Için](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms)hızlandırılmış ağı nasıl etkinleştirebileceğinizi öğrenebilirsiniz.

### <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri
Aşağıdaki dağıtımlar Azure galerisindeki kutudan çıkar:
* **Ubuntu 16,04**
* **SLES 12 SP3**
* **RHEL 7,4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Geribağlantı noktaları çekirdeki "uzat"**
* **Oracle Linux 7,4**

### <a name="supported-vm-instances"></a>Desteklenen VM örnekleri
Hızlandırılmış ağ, 2 veya daha fazla vCPU ile en genel amaçlı ve işlem için iyileştirilmiş örnek boyutlarında desteklenir.  Desteklenen bu seriler şunlardır: D/DSv2 ve F/FS

Hiper iş parçacığı destekleyen örneklerde, hızlandırılmış ağ, 4 veya daha fazla vCPU içeren VM örneklerinde desteklenir. Desteklenen Seriler: D/DSv3, E/ESv3, Fsv2 ve MS/MMS.

VM örnekleri hakkında daha fazla bilgi için bkz. [LINUX VM boyutları](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enabling-accelerated-networking-for-replicated-vms"></a>Çoğaltılan VM 'Ler için hızlandırılmış ağı etkinleştirme

Azure sanal makineleri için [çoğaltmayı etkinleştirdiğinizde](azure-to-azure-tutorial-enable-replication.md) Site Recovery, sanal makine ağ arabirimlerinin hızlandırılmış ağ özelliğinin etkin olup olmadığını otomatik olarak algılar. Hızlandırılmış ağ zaten etkinse, Site Recovery çoğaltılan sanal makinenin ağ arabirimlerinde hızlandırılmış ağı otomatik olarak yapılandırır.

Hızlandırılmış ağ durumu, çoğaltılan sanal makinenin **işlem ve ağ** ayarlarının **ağ arabirimleri** bölümünde doğrulanabilir.

![Hızlandırılmış ağ ayarı](./media/azure-vm-disaster-recovery-with-accelerated-networking/compute-network-accelerated-networking.png)

Çoğaltmayı etkinleştirdikten sonra kaynak sanal makinede hızlandırılmış ağı etkinleştirdiyseniz, çoğaltılan sanal makinenin ağ arabirimleri için aşağıdaki işlem ile hızlandırılmış ağı etkinleştirebilirsiniz:
1. Çoğaltılan sanal makine için **işlem ve ağ** ayarlarını aç
2. Ağ **arabirimleri** bölümünde ağ arabiriminin adına tıklayın
3. **Hedef** sütun altında hızlandırılmış ağ açılır listesinden **etkin** ' i seçin

![Hızlandırılmış ağı etkinleştir](./media/azure-vm-disaster-recovery-with-accelerated-networking/network-interface-accelerated-networking-enabled.png)

Yukarıdaki işlem, daha önce Site Recovery tarafından otomatik olarak etkinleştirilen mevcut çoğaltılan sanal makineler için de izlenmelidir.

## <a name="next-steps"></a>Sonraki adımlar
- [Hızlandırılmış ağ avantajları](../virtual-network/create-vm-accelerated-networking-powershell.md#benefits)hakkında daha fazla bilgi edinin.
- [Windows sanal makineleri](../virtual-network/create-vm-accelerated-networking-powershell.md#limitations-and-constraints) ve [Linux sanal makineleri](../virtual-network/create-vm-accelerated-networking-cli.md#limitations-and-constraints)için hızlandırılmış ağ sınırlamaları ve kısıtlamaları hakkında daha fazla bilgi edinin.
- Uygulama yük devretmesini otomatikleştirmek için [kurtarma planları](site-recovery-create-recovery-plans.md) hakkında daha fazla bilgi edinin.
