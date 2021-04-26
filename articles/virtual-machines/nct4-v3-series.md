---
title: NCas T4 v3-serisi
description: NCas T4 v3 serisi VM 'Leri için belirtim.
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
author: vikancha-MSFT
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: vikancha
ms.openlocfilehash: 2e10177de1a40bf7ece7acde43a91686bd4c98e5
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306458"
---
# <a name="ncast4_v3-series"></a>NCasT4_v3 serisi 

NCasT4_v3 serisi sanal makineler, [NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) GPU 'LARı ve AMD Epde 7V12 (Roma) CPU 'lar tarafından desteklenir. VM 'Ler, en fazla 64, çok iş parçacıklı AMD EPIC 7V12 (Roma) işlemci çekirdeği ve 440 GiB 'a kadar, her biri 16 GB bellek içeren 4 KB 'a kadar NVıDıA T4 GPU özelliğine sahiptir. Bu sanal makineler, Kullanıcı tarafından oluşturulan isteklerin gerçek zamanlı olarak kullanılması veya NVıDıA 'ın KıLAVUZ sürücüsü ve sanal GPU teknolojisini kullanan etkileşimli grafikler ve görselleştirme iş yükleri gibi AI hizmetlerini dağıtmak için idealdir. CUDA, TensorRT, Caffe, ONNX ve diğer çerçeveleri temel alan standart GPU hesaplama iş yükleri ya da GPU hızlandırmalı grafik uygulamaları tabanlı OpenGL ve DirectX, NCasT4_v3 serisinde kullanıcılara yakın bir şekilde dağıtılır.

<br>

[Acu](acu.md): 230-260<br>
[Premium Depolama](premium-storage-performance.md): desteklenir<br>
[Premium depolama önbelleği](premium-storage-performance.md): desteklenir<br>
[Ultra diskler](disks-types.md#ultra-disk): desteklenir (kullanılabilirlik, kullanım ve performans hakkında[daha fazla bilgi edinin](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312) ) <br>
[Dinamik geçiş](maintenance-and-updates.md): desteklenmiyor<br>
[Güncelleştirmeleri koruyan bellek](maintenance-and-updates.md): desteklenmiyor<br>
[VM oluşturma desteği](generation-2.md): 1. ve 2. nesil<br>
[Hızlandırılmış ağ](../virtual-network/create-vm-accelerated-networking-cli.md): desteklenir<br>
[Kısa ömürlü işletim sistemi diskleri](ephemeral-os-disks.md): desteklenmez <br>
NVIDIA NVLink Interconnect: desteklenmiyor<br>
<br>

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | GPU | GPU belleği: GiB | Maksimum veri diskleri | En fazla NIC/beklenen ağ bant genişliği (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2 / 8000 |
| Standard_NC8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4 / 8000  |
| Standard_NC16as_T4_v3 |16 |110 |360 | 1 | 16 | 32 | 8 / 8000  |
| Standard_NC64as_T4_v3 |64 |440 |2880 | 4 | 64 | 32 | 8 / 32000  |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Desteklenen işletim sistemleri ve sürücüler

Windows veya Linux çalıştıran Azure NCasT4_v3 serisi VM 'lerin GPU Özellikleri avantajlarından yararlanmak için NVIDIA GPU sürücüleri yüklenmelidir.

NVIDIA GPU sürücülerini el ile yüklemek için desteklenen işletim sistemleri, sürücüler, yükleme ve doğrulama adımları için bkz. [Windows Için N SERISI GPU sürücü kurulumu](./windows/n-series-driver-setup.md) .

Azure NVIDIA GPU sürücü uzantısı, NCasT4_v3 serisi VM 'lerde CUDA sürücülerini dağıtacaktır. Grafik ve görselleştirme iş yükleri için Azure tarafından desteklenen KıLAVUZ sürücülerini el ile yükleyebilirsiniz.

## <a name="other-sizes"></a>Diğer boyutlar

- [Genel amaçlı](sizes-general.md)
- [Bellek için iyileştirilmiş](sizes-memory.md)
- [Depolama için iyileştirilmiş](sizes-storage.md)
- [GPU için iyileştirilmiş](sizes-gpu.md)
- [Yüksek performanslı işlem](sizes-hpc.md)
- [Önceki nesiller](sizes-previous-gen.md)

## <a name="next-steps"></a>Sonraki adımlar

Azure [işlem birimlerinin (ACU)](acu.md) Azure SKU 'ları genelinde işlem performansını karşılaştırmanıza nasıl yardımcı olabileceğini öğrenin.
