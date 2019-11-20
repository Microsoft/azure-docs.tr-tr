---
title: include dosyası
description: include dosyası
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2303d36e93cecfca03894a8b0e55458c03b13d78
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73413019"
---
**Giden veri aktarımları**: [giden veri aktarımları](https://azure.microsoft.com/pricing/details/bandwidth/) (Azure veri merkezlerinden çıkan veriler) bant genişliği kullanımı için faturalandırılır.

**İşlemler**: Standart yönetilen bir diskte gerçekleştirdiğiniz işlem sayısı için faturalandırılırsınız. Standart SSD 'Ler için, 256 kıb aktarım hızına eşit veya daha küçük olan her g/ç işlemi tek bir g/ç işlemi olarak kabul edilir. 256 Kigb 'den büyük g/ç işlemleri, 256 KiB boyutundaki birden fazla g/ç işlemi olarak kabul edilir. Standart HDD 'Ler için, her GÇ işlemi, g/ç boyutundan bağımsız olarak tek bir işlem olarak değerlendirilir.

İşlem maliyetleri dahil olmak üzere yönetilen disklere yönelik fiyatlandırma hakkında ayrıntılı bilgi için bkz. [yönetilen diskler fiyatlandırması](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Ultra disk VM ayırma ücreti

Azure VM 'Leri, Ultra disklerle uyumlu olup olmadığını gösterebilir. Bir ultra disk uyumlu VM, performansı iyileştirmek ve gecikme süresini azaltmak için işlem VM örneği ve blok depolama ölçek birimi arasında ayrılmış bant genişliği kapasitesini ayırır. Bu özelliği sanal makineye eklemek, yalnızca bir ultra disk iliştirmeden VM 'de Ultra disk özelliğini etkinleştirdiyseniz uygulanan bir rezervasyon ücretine neden olur. Ultra disk uyumlu VM 'ye bir ultra disk eklendiğinde, bu ücret uygulanmaz. Bu ücret, VM 'de sağlanan vCPU başına. 

> [!Note]
> [Kısıtlanmış çekırdek VM boyutları](../articles/virtual-machines/linux/constrained-vcpu.md)için, rezervasyon ücreti, kısıtlı çekirdekler değil, gerçek vCPU sayısına dayanır. Standard_E32-8s_v3 için, ayırma ücreti 32 çekirdekleri temel alır. 

Ultra disk fiyatlandırma ayrıntıları için [Azure diskleri fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/managed-disks/) bakın.

### <a name="azure-disk-reservation"></a>Azure disk ayırma

Disk ayırma, indirimli bir yıllık disk depolama alanı satın alma seçeneğidir ve toplam maliyetinizi azaltır. Bir disk ayırması satın alırken, bir yıllık dönem için Doğu ABD 2 bölgede bulunan 10 P30 (1TiB) Premium SSDs gibi bir hedef bölgede belirli bir disk SKU 'SU seçersiniz. Ayırma deneyimi, ayrılmış sanal makine (VM) örneklerine benzerdir. Tasarruf etmenizi en üst düzeye çıkarmak için VM ve disk ayırmalarını paketleyebilirsiniz. Azure disk rezervasyonu şimdilik, P30 (1TiB) ile P80 (32 TiB) arasında Premium SSD SKU 'Ları için tüm üretim bölgelerinde bir yıl taahhüt planı sunar. Ayrılmış diskler fiyatlandırması hakkında daha fazla bilgi için bkz. [Azure diskleri fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/managed-disks/).