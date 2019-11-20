---
title: include dosyası
description: include dosyası
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 224180bdebdf94c6d2360df8ea6615add4675921
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72902323"
---
## <a name="supported-distributions-and-drivers"></a>Desteklenen dağıtımlar ve sürücüler

### <a name="nvidia-cuda-drivers"></a>NVıDıA CUDA sürücüleri

NC, NCv2, NCv3, ND ve NDv2 serisi VM 'Ler için NVıDıA CUDA sürücüleri (NV serisi için isteğe bağlı) yalnızca aşağıdaki tabloda listelenen Linux dağıtımları üzerinde desteklenir. CUDA sürücü bilgileri yayın sırasında geçerli. En son CUDA sürücüleri için [NVIDIA](https://developer.nvidia.com/cuda-zone) Web sitesini ziyaret edin. Dağıtım için en son CUDA sürücülerini yüklediğinizden veya yükseltdiğinizden emin olun. 

> [!TIP]
> Bir Linux sanal makinesinde el ile CUDA sürücüsü yüklemeye alternatif olarak Azure [veri bilimi sanal makinesi](../articles/machine-learning/data-science-virtual-machine/overview.md) görüntüsünü dağıtabilirsiniz. Ubuntu 16,04 LTS veya CentOS 7,4 için DSVM sürümleri, NVıDıA CUDA sürücülerini, CUDA derin sinir ağ kitaplığını ve diğer araçları önceden yükler.

| Dağıtım | Sürücü |
| --- | -- | 
| Ubuntu 16,04 LTS, 18,04 LTS<br/><br/> Red Hat Enterprise Linux 7,3, 7,4, 7,5, 7,6<br/><br/> CentOS tabanlı 7,3, 7,4, 7,5, 7,6, CentOS tabanlı 7,4 HPC | NVıDıA CUDA 10,1, sürücü dalı R418 |

### <a name="nvidia-grid-drivers"></a>NVıDıA KıLAVUZ sürücüleri

Microsoft, sanal iş istasyonları veya sanal uygulamalar olarak kullanılan NV ve NVv3 serisi VM 'Ler için NVıDıA GRID sürücü yükleyicilerini yeniden dağıtır. Yalnızca aşağıdaki tabloda listelenen işletim sistemlerinde Azure NV VM 'lerine yalnızca bu KıLAVUZ sürücülerini yükler. Bu sürücüler Azure 'da GRID sanal GPU yazılımı için lisanslama içerir. NVıDıA vGPU yazılım lisans sunucusu ayarlamanız gerekmez.

| Dağıtım | Sürücü |
| --- | -- |
|Ubuntu 18,04 LTS<br/><br/>Ubuntu 16.04 LTS<br/><br/>7,0 Red Hat Enterprise Linux 7,6<br/><br/>CentOS tabanlı 7,0 7,6<br/><br/>SUSE Linux Enterprise Server 12 SP2 | NVıDıA GRID 9,1, sürücü dalı R430|

> [!WARNING] 
> Red Hat ürünlerine üçüncü taraf yazılım yüklenmesi Red Hat destek koşullarını etkileyebilir. Bkz. [Red Hat Bilgi Bankası makalesi](https://access.redhat.com/articles/1067).
>
