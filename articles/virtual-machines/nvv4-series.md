---
title: NVv4 serisi
description: NVv4 serisi VM 'Ler için Özellikler.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: 2d0f82565ec4010d03003ed902e9aba819e93bc3
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303194"
---
# <a name="nvv4-series"></a>NVv4 serisi 

NVv4 serisi sanal makineler, [AMD Radeon INSTINCT MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU 'LARı ve AMD epi dili 7V12 (Roma) CPU 'ları tarafından desteklenir. NVv4-Series ile Azure, kısmi GPU 'lara sahip sanal makineler ile tanışın. 16 GiB çerçeve arabelleğiyle tam bir GPU 'ya 2 GiB çerçeve arabelleğinden 1 GB 'den başlayan GPU hızlandırılmış grafik uygulamaları ve sanal masaüstleri için doğru boyutlu sanal makineyi seçin. NVv4 sanal makineler Şu anda yalnızca Windows Konuk işletim sistemini destekliyor.

<br>

[Acu](acu.md): 230-260<br>
[Premium Depolama](premium-storage-performance.md): desteklenir<br>
[Premium depolama önbelleği](premium-storage-performance.md): desteklenir<br>
[Ultra diskler](disks-types.md#ultra-disk): desteklenir (kullanılabilirlik, kullanım ve performans hakkında[daha fazla bilgi edinin](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312) ) <br>
[Dinamik geçiş](maintenance-and-updates.md): desteklenmiyor<br>
[Güncelleştirmeleri koruyan bellek](maintenance-and-updates.md): desteklenmiyor<br>
[VM oluşturma desteği](generation-2.md): 1. ve 2. nesil<br>
[Hızlandırılmış ağ](../virtual-network/create-vm-accelerated-networking-cli.md): desteklenir<br>
[Kısa ömürlü işletim sistemi diskleri](ephemeral-os-disks.md): desteklenir <br>
<br>

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | GPU | GPU belleği: GiB | Maksimum veri diskleri | En fazla NIC/beklenen ağ bant genişliği (MBps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 / 1000 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 / 2000 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 / 4000 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 / 8000 |

<sup>1</sup> NVv4 serisi VM 'ler özelliği AMD eşzamanlı çoklu Iş parçacığı teknolojisi

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Desteklenen işletim sistemleri ve sürücüler

Windows çalıştıran Azure NVv4 serisi VM 'lerin GPU Özellikleri avantajlarından yararlanmak için, AMD GPU sürücüleri yüklenmelidir.

AMD GPU sürücülerini el ile yüklemek için desteklenen işletim sistemleri, sürücüler, yükleme ve doğrulama adımları için bkz. [Windows Için N SERISI AMD GPU sürücü kurulumu](./windows/n-series-amd-driver-setup.md) .

## <a name="other-sizes"></a>Diğer boyutlar

- [Genel amaçlı](sizes-general.md)
- [Bellek için iyileştirilmiş](sizes-memory.md)
- [Depolama için iyileştirilmiş](sizes-storage.md)
- [GPU için iyileştirilmiş](sizes-gpu.md)
- [Yüksek performanslı işlem](sizes-hpc.md)
- [Önceki nesiller](sizes-previous-gen.md)

## <a name="next-steps"></a>Sonraki adımlar

Azure [işlem birimlerinin (ACU)](acu.md) Azure SKU 'ları genelinde işlem performansını karşılaştırmanıza nasıl yardımcı olabileceğini öğrenin.
