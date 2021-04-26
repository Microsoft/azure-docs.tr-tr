---
title: DC-Series-Azure sanal makineleri
description: DC Serisi VM 'Ler için Özellikler.
author: susaxen
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: jushiman
ms.openlocfilehash: 677f4df0873f8b72d40dd373035111e2e0002491
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102549239"
---
# <a name="dcsv2-series"></a>DCsv2 serisi


DCsv2 serisi, genel bulutta işlendiği sırada verilerinizin ve kodunuzun gizliliğini ve bütünlüğünü korumaya yardımcı olabilir. Bu makineler, SGX teknolojisini içeren en son Intel XEON E-2288G Işlemcisi tarafından desteklenir. Intel Turbo Boost teknolojisiyle bu makineler, 5.0 GHz 'ye kadar sürebilir. DCsv2 serisi örnekler, müşterilerin, kullanıldığı sırada kod ve verilerini korumak için güvenli şifreleme tabanlı uygulamalar oluşturmasına imkan tanır.

Örnek kullanım örnekleri şunlardır: gizli multiparti veri paylaşımı, sahtekarlık algılama, para-para, blok zinciri, gizli kullanım analizi, zekası Analizi ve gizli makine öğrenimi.

[Premium Depolama](premium-storage-performance.md): desteklenen *<br> 
 [Premium depolama önbelleği](premium-storage-performance.md): desteklenen <br> 
 [dinamik geçiş](maintenance-and-updates.md): desteklenen <br> 
 [bellek güncelleştirmeleri](maintenance-and-updates.md): desteklenmeyen <br> 
 [VM oluşturma desteği](generation-2.md): 2. nesil <br> 
 [hızlandırılmış ağ](../virtual-network/create-vm-accelerated-networking-cli.md): desteklenir (* en az 4 vCPU * gerektirir) <br>
[Kısa ömürlü işletim sistemi diskleri](ephemeral-os-disks.md): desteklenir <br>

Standard_DC8_v2 hariç * <br>

| Boyut             | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum veri diskleri | Maksimum önbelleğe alınmış ve geçici depolama aktarım hızı: IOPS-MB/sn (önbellek boyutu GiB biriminde) | En fazla NIC/beklenen ağ bant genişliği (MBps) | EPC belleği (MIB) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|----------------------------------------------|---------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16                                                                                               | 2   | 28                                      |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32                                                                                               | 2  | 56                                          |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64                                                                                               | 2  | 112                                          |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128                                                                                         | 2   | 168                                         |

- DCsv2 serisi VM 'Ler [2. nesil sanal makineler](./generation-2.md#creating-a-generation-2-vm) ve yalnızca `Gen2` görüntüleri destekler.
- Şu anda [burada](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all)listelenen bölgelerde kullanılabilir.
- Önceki nesil gizli Işlem VM 'Leri: [DC Serisi](sizes-previous-gen.md#preview-dc-series)
- [Azure Portal](./linux/quick-create-portal.md) veya [Azure Marketi](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) 'ni kullanarak DCsv2 VM oluşturma



## <a name="other-sizes-and-information"></a>Diğer boyutlar ve bilgiler

- [Genel amaçlı](sizes-general.md)
- [Bellek için iyileştirilmiş](sizes-memory.md)
- [Depolama için iyileştirilmiş](sizes-storage.md)
- [GPU için iyileştirilmiş](sizes-gpu.md)
- [Yüksek performanslı işlem](sizes-hpc.md)
- [Önceki nesiller](sizes-previous-gen.md)

Fiyatlandırma Hesaplayıcı: [Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/calculator/)

Disk türleri hakkında daha fazla bilgi: [disk türleri](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Sonraki adımlar

Azure [işlem birimlerinin (ACU)](acu.md) Azure SKU 'ları genelinde işlem performansını karşılaştırmanıza nasıl yardımcı olabileceğini öğrenin.
