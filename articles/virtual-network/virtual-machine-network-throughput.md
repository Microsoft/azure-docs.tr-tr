---
title: Azure sanal makine ağ aktarım hızı | Microsoft Docs
description: Azure sanal makine ağ aktarım hızı hakkında bilgi edinin.
services: virtual-network
documentationcenter: na
author: steveesp
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/26/2019
ms.author: steveesp
ms.reviewer: kumud, mareat
ms.openlocfilehash: 47f58b25b082784177910d14ab95d8d242fda71a
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72750317"
---
# <a name="virtual-machine-network-bandwidth"></a>Sanal makine ağ bant genişliği

Azure, her biri farklı performans özellikleri karışımı olan çeşitli VM boyutları ve türleri sunar. Bir özellik, saniyede megabit (Mbps) cinsinden ölçülen ağ aktarım hızı (veya bant genişliğidir). Sanal makineler paylaşılan donanımda barındırıldığından, ağ kapasitesinin aynı donanımı paylaşan sanal makineler arasında oldukça paylaşılması gerekir. Daha büyük sanal makineler daha küçük sanal makinelere göre görece daha fazla bant genişliğine ayrılır.
 
Her bir sanal makineye ayrılan ağ bant genişliği, sanal makineden çıkış (giden) trafiği üzerinden ölçülür. Sanal makineyi terk eden tüm ağ trafiği, hedefe bakılmaksızın ayrılan sınıra doğru sayılır. Örneğin, bir sanal makinede 1.000 Mbps sınırı varsa, bu sınır giden trafiğin aynı sanal ağdaki başka bir sanal makineye mi yoksa Azure dışında mı uygulanacağını uygular.
 
Giriş doğrudan tarifeli değil veya sınırlı değil. Ancak, bir sanal makinenin gelen verileri işleme yeteneğini etkileyebilecek CPU ve depolama sınırları gibi başka etmenler de vardır.

Hızlandırılmış ağ, gecikme süresi, aktarım hızı ve CPU kullanımı dahil olmak üzere ağ performansını geliştirmek için tasarlanmış bir özelliktir. Hızlandırılmış ağ bir sanal makinenin aktarım hızını geliştirebileceğinden, bu işlem yalnızca sanal makinenin ayrılmış bant genişliğine kadar yapılabilir. Hızlandırılmış ağ hakkında daha fazla bilgi edinmek için bkz. [Windows](create-vm-accelerated-networking-powershell.md) veya [Linux](create-vm-accelerated-networking-cli.md) sanal makineleri için hızlandırılmış ağ.
 
Azure sanal makinelerinin bir tane olması gerekir, ancak bunlara eklenmiş birkaç ağ arabirimi olabilir. Bir sanal makineye ayrılan bant genişliği, bir sanal makineye bağlı tüm ağ arabirimleri genelinde giden tüm trafiğin toplamıdır. Diğer bir deyişle, sanal makineye kaç ağ arabirimi iliştirildiğine bakılmaksızın, ayrılan bant genişliği sanal makine başına olur. Birçok ağ arabiriminin farklı Azure VM boyutlarını desteklediğini öğrenmek için bkz. Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ve [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM boyutları. 

## <a name="expected-network-throughput"></a>Ağ aktarım hızı bekleniyor

Beklenen giden aktarım hızı ve her VM boyutu tarafından desteklenen ağ arabirimlerinin sayısı, Azure [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ve [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) VM boyutlarında ayrıntılıdır. Genel amaçlı gibi bir tür seçin ve sonuç sayfasında, Dv2-Series gibi bir boyut serisi seçin. Her serinin, **en fazla NIC/ağ performansı (MB/sn)** adlı son sütunda ağ özelliklerine sahip bir tablosu vardır. 

Aktarım hızı sınırı, sanal makine için geçerlidir. Aktarım hızı aşağıdaki faktörlerden etkilenmez:
- **Ağ arabirimi sayısı**: bant genişliği sınırı, sanal makineden giden tüm trafiğin birikimli bir sayıdır.
- **Hızlandırılmış ağ**: Bu özellik, yayımlanan sınırı elde etmek için yararlı olabilir, ancak sınırı değiştirmez.
- **Trafik hedefi**: tüm hedefler giden sınıra doğru sayılır.
- **Protokol**: tüm protokollerde tüm giden trafik sınıra doğru sayılır.

## <a name="network-flow-limits"></a>Ağ akışı sınırları

Bant genişliğine ek olarak, belirli bir anda VM 'de bulunan ağ bağlantılarının sayısı ağ performansını etkileyebilir. Azure ağ yığını, ' Akışlar ' adlı veri yapılarında TCP/UDP bağlantısının her bir yönü için durumu korur. Tipik bir TCP/UDP bağlantısında, biri gelen ve giden yönü için bir diğeri oluşturulan 2 akışa sahip olur. 

Uç noktalar arasındaki veri aktarımı, veri aktarımını gerçekleştirenlere ek olarak birkaç akış oluşturulmasını gerektirir. Yük dengeleyici durumu araştırmaları için oluşturulan DNS çözümlemesi ve akışlar için bazı örnekler oluşturulur. Ayrıca ağ geçitleri, proxy 'ler, güvenlik duvarları gibi ağ sanal gereçlerinin (NVA 'lar), Gereç sırasında sonlandırılan ve gereç tarafından kaynaklandığı bağlantılar için oluşturulan akışları görebileceğine de göz önünde bulabilirsiniz. 

![Bir iletme gereci aracılığıyla TCP konuşması için akış sayısı](media/virtual-machine-network-throughput/flow-count-through-network-virtual-appliance.png)

## <a name="flow-limits-and-recommendations"></a>Akış sınırları ve önerileri

Azure ağ yığını, 8 ' den fazla CPU çekirdeği ve 100 ' den fazla CPU çekirdeği olan VM 'Ler için iyi performansa sahip olan 250K toplam ağ akışını destekler. Bu sınırı aşan ağ performansı, ek akışlar bırakıldıktan sonra 500 k gelen ve 250K giden bir sabit sınıra kadar ek akışlar için düzgün şekilde düşer.

||< 8 CPU çekirdeği olan VM 'Ler|8 + CPU çekirdekleri olan VM 'Ler|
|---|---|---|
|<b>İyi performans</b>|100K akış |250K akış|
|<b>Azaltılmış performans</b>|100.000 akış üzerine|250K akışlar üzerinde|
|<b>Akış sınırı</b>|500 k akış|500 k akış|

[Azure izleyici](../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines) 'de ölçümler, sanal makine veya VMSS örneklerindeki ağ akışı sayısını ve akış oluşturma hızını izlemek için kullanılabilir.

![Azure-Monitor-Flow-Metrics. png](media/virtual-machine-network-throughput/azure-monitor-flow-metrics.png)

Bağlantı kurma ve sonlandırma ücretleri ayrıca, paket işleme yordamlarına sahip bağlantı kurma ve sonlandırma paylaşımları olarak ağ performansını etkileyebilir. İş yüklerini beklenen trafik desenlerine göre kıyaslanmasını ve iş yüklerini performans ihtiyaçlarınızı karşılayacak şekilde ölçeklendirmenizi öneririz. 

## <a name="next-steps"></a>Sonraki adımlar

- [Sanal makine işletim sistemi için ağ aktarım hızını iyileştirme](virtual-network-optimize-network-bandwidth.md)
- Sanal makine için [ağ aktarım hızını test](virtual-network-bandwidth-testing.md) edin.
