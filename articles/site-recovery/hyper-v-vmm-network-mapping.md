---
title: Site Recovery ile Hyper-V (VMM ile) ağ eşlemesi hakkında
description: Hyper-V VM 'lerinin (VMM bulutlarında yönetilen) olağanüstü durum kurtarma için ağ eşlemesinin, Azure Site Recovery ile Azure 'a nasıl ayarlanacağını açıklar.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 6b68b4c943ec96620427978c2309f27e1fb1f217
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082570"
---
# <a name="prepare-network-mapping-for-hyper-v-vm-disaster-recovery-to-azure"></a>Azure 'da Hyper-V VM olağanüstü durum kurtarma için ağ eşlemeyi hazırlama


Bu makale, System Center Virtual Machine Manager (VMM) bulutlarındaki Hyper-V VM 'lerini Azure 'a veya [Azure Site Recovery](site-recovery-overview.md) hizmetini kullanarak ikincil bir siteye çoğalttığınızda Ağ eşlemesini anlamanıza ve hazırlamanızı sağlar.


## <a name="prepare-network-mapping-for-replication-to-azure"></a>Azure 'a çoğaltma için ağ eşlemeyi hazırlama

Azure 'a çoğaltma yaparken, bir kaynak VMM sunucusundaki VM ağları arasında ağ eşlemesi eşlenir ve Azure sanal ağlarını hedefleyin. Eşleme sürecinde şu işlemler gerçekleştirilir:
-  **Ağ bağlantısı**— çoğaltılan Azure VM 'lerinin eşlenen ağa bağlı olmasını sağlar. Aynı ağda yük devredilen tüm makineler, farklı kurtarma planlarında yük devretseler bile birbirlerine bağlanabilir.
- **Ağ geçidi**— hedef Azure ağında bir ağ geçidi ayarlandıysa, VM 'ler diğer şirket içi sanal makinelere bağlanabilir.

Ağ eşleme aşağıdaki gibi çalışmaktadır:

- Bir kaynak VMM VM ağını bir Azure sanal ağıyla eşleyebilirsiniz.
- Yük devretmeden sonra kaynak ağdaki Azure VM 'Leri eşlenen hedef sanal ağa bağlanır.
- Kaynak VM ağına eklenen yeni VM 'Ler, Çoğaltma gerçekleştiğinde eşlenen Azure ağına bağlanır.
- Hedef ağın birden çok alt ağı varsa ve bu alt ağlardan biri kaynak sanal makinenin bulunduğu alt ağ ile aynı adı taşıyorsa, çoğaltılan sanal makine yük devretme işleminin ardından hedef alt ağa bağlanır.
- Eşleşen ada sahip bir hedef alt ağ yoksa sanal makine ağdaki ilk alt ağa bağlanır.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>İkincil bir siteye çoğaltma için ağ eşlemeyi hazırlama

İkincil bir siteye çoğaltma yaparken, bir kaynak VMM sunucusundaki VM ağları ve hedef VMM sunucusundaki VM ağları arasında ağ eşlemesi eşlenir. Eşleme sürecinde şu işlemler gerçekleştirilir:

- **Ağ bağlantısı**— yük devretmeden sonra VM 'leri uygun ağlara bağlar. Çoğaltma VM 'si, kaynak ağla eşlenmiş hedef ağa bağlanır.
- **En ıyı VM yerleşimi**— çoğaltma sanal makinelerini Hyper-V ana bilgisayar sunucularına en iyi şekilde koyar. Çoğaltma VM 'Leri, eşlenmiş VM ağlarına erişebilen konaklara yerleştirilir.
- **Ağ eşlemesi yok**— Ağ eşlemesini yapılandırmazsanız, çoğaltma VM 'leri yük devretme sonrasında HERHANGI bir VM ağına bağlanmaz.

Ağ eşleme aşağıdaki gibi çalışmaktadır:

- Ağ eşlemesi iki VMM sunucusundaki VM ağları arasında veya iki site aynı sunucu tarafından yönetiliyorsa tek bir VMM sunucusunda yapılandırılabilir.
- Eşleme doğru şekilde yapılandırıldığında ve çoğaltma etkin olduğunda, birincil konumdaki bir VM bir ağa bağlanır ve hedef konumdaki çoğaltma, eşlenmiş ağına bağlanır.
- Site Recovery ağ eşleme sırasında bir hedef VM ağını seçtiğinizde, kaynak VM ağını kullanan VMM kaynak bulutları, koruma için kullanılan hedef bulutlarda kullanılabilir hedef VM ağları ile birlikte görüntülenir.
- Hedef ağın birden çok alt ağı varsa ve bu alt ağlardan biri kaynak sanal makinenin bulunduğu alt ağ ile aynı ada sahipse, çoğaltma VM 'si yük devretmeden sonra bu hedef alt ağa bağlanır. Eşleşen ada sahip bir hedef alt ağ yoksa, sanal makine ağdaki ilk alt ağa bağlanır.

## <a name="example"></a>Örnek

Bu mekanizmayı gösteren bir örnek aşağıda verilmiştir. Yeni York ve Chicago 'da iki konum içeren bir kuruluş alalım.

**Konum** | **VMM sunucusu** | **VM ağları** | **Eşlendi**
---|---|---|---
New York | VMM-NewYork| VMNetwork1-NewYork | VMNetwork1-Chicago ile eşlendi
 |  | VMNetwork2-NewYork | Eşlenmedi
Chicago | VMM-Chicago| VMNetwork1-Chicago | VMNetwork1 ile eşlendi-NewYork
 | | VMNetwork2-Chicago | Eşlenmedi

Bu örnekte:

- VMNetwork1-NewYork 'a bağlı herhangi bir VM için bir çoğaltma VM 'si oluşturulduğunda, VMNetwork1-Chicago öğesine bağlanır.
- VMNetwork2-NewYork veya VMNetwork2-Chicago için bir çoğaltma sanal makinesi oluşturulduğunda, herhangi bir ağa bağlanmaz.

VMM bulutlarının örnek kuruluşumuzda ve bulutlarla ilişkili mantıksal ağların nasıl ayarlandığı aşağıda verilmiştir.

### <a name="cloud-protection-settings"></a>Bulut koruması ayarları

**Korumalı bulut** | **Bulutu koruma** | **Mantıksal ağ (New York)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Mantıksal ve VM ağ ayarları

**Konum** | **Mantıksal ağ** | **İlişkili VM ağı**
---|---|---
New York | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

### <a name="target-network-settings"></a>Hedef ağ ayarları

Bu ayarlara bağlı olarak, hedef VM ağını seçtiğinizde, aşağıdaki tabloda kullanılabilen seçimler gösterilmektedir.

**Seç** | **Korumalı bulut** | **Bulutu koruma** | **Hedef ağ kullanılabilir**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Kullanılabilir
 | GoldCloud1 | GoldCloud2 | Kullanılabilir
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Kullanılamaz
 | GoldCloud1 | GoldCloud2 | Kullanılabilir


Hedef ağın birden çok alt ağı varsa ve bu alt ağlardan biri kaynak sanal makinenin bulunduğu alt ağ ile aynı ada sahipse, çoğaltma sanal makinesi yük devretmeden sonra bu hedef alt ağa bağlanır. Eşleşen ada sahip bir hedef alt ağ yoksa sanal makine ağdaki ilk alt ağa bağlanır.


### <a name="failback-behavior"></a>Yeniden çalışma davranışı

Yeniden çalışma durumunda neler olduğunu görmek için (tersine çoğaltma), VMNetwork1-NewYork 'un aşağıdaki ayarlarla VMNetwork1-Chicago ile eşlendiğini varsayın.


**'Nın** | **VM ağına bağlanıldı**
---|---
VM1 | VMNetwork1-ağ
VM2 (VM1 çoğaltması) | VMNetwork1-Chicago

Bu ayarlarla, birkaç olası senaryoda neler olduğunu gözden geçirelim.

**Senaryo** | **Sonucu**
---|---
Yük devretmeden sonra VM-2 Ağ özelliklerinde değişiklik yapılmaz. | VM-1 kaynak ağa bağlı kalır.
VM-2 ' nin ağ özellikleri, yük devretmeden sonra değiştirilir ve bağlantısı kesilir. | VM-1 bağlantısı kesildi.
VM-2 ' nin ağ özellikleri, yük devretmeden sonra değiştirilir ve VMNetwork2-Chicago öğesine bağlanır. | VMNetwork2-Chicago eşlenmemişse, VM-1 ' in bağlantısı kesilir.
VMNetwork1-Chicago 'nin ağ eşlemesi değiştirildi. | VM-1, artık VMNetwork1-Chicago ile eşlenmiş ağa bağlanır.



## <a name="next-steps"></a>Sonraki adımlar

- [Hakkında bilgi edinin](hyper-v-vmm-networking.md) İkincil bir VMM sitesine yük devretmeden sonra IP adresleme.
- [Hakkında bilgi edinin](concepts-on-premises-to-azure-networking.md) Azure 'a yük devretmeden sonra IP adresleme.
