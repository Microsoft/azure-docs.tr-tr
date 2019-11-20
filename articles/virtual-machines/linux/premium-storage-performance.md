---
title: "Azure Premium Depolama: Windows VM 'lerinde performans için tasarım "
description: Azure Premium Depolama kullanarak yüksek performanslı uygulamalar tasarlayın. Premium Depolama, Azure sanal makinelerinde çalışan g/ç yoğunluklu iş yükleri için yüksek performanslı ve düşük gecikmeli disk desteği sunar.
author: roygara
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 56836b81ad63c3a2170606dc64988aab8dc7f47f
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035277"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure Premium Depolama: yüksek performans için tasarım
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Bazen bir disk performans sorunu gibi görünen durum aslında ağ performans sorununa neden olur. Bu durumlarda, [ağ performansınızı](../../virtual-network/virtual-network-optimize-network-bandwidth.md)iyileştirebilmelisiniz.
>
> Diskinizin kıyaslanmaya bakıyorsanız, [bir diski sınama](disks-benchmarks.md)hakkındaki makalemize bakın.
>
> VM 'niz hızlandırılmış ağı destekliyorsa etkinleştirildiğinden emin olun. Etkinleştirilmemişse, hem [Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) hem de [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms)üzerinde zaten dağıtılmış VM 'lerde etkinleştirebilirsiniz.

Başlamadan önce Premium Storage 'a yeni başladıysanız, önce [IaaS VM 'leri için bir Azure disk türü seçin](disks-types.md) ve [depolama hesapları için Azure depolama ölçeklenebilirlik ve performans hedefleri](../../storage/common/storage-scalability-targets.md)makalesini okuyun.


[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Diskinizin kıyaslanmaya bakıyorsanız, [bir diski sınama](disks-benchmarks.md)hakkındaki makalemize bakın.

Kullanılabilir disk türleri hakkında daha fazla bilgi edinin: [bir disk türü seçin](disks-types.md)  

SQL Server kullanıcılar için SQL Server performans En Iyi yöntemleri hakkında makaleleri okuyun:

* [Azure sanal makineler 'de SQL Server için En Iyi performans uygulamaları](../windows/sql/virtual-machines-windows-sql-performance.md)
* [Azure Premium Depolama, Azure VM 'de SQL Server için en yüksek performansı sağlar](https://blogs.technet.com/b/dataplatforminsider/archive/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm.aspx)