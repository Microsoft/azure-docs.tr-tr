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
ms.openlocfilehash: 4bfac9be5041fdf4ebfe7ea56f064b8b85806703
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98859645"
---
## <a name="supported-distributions-and-drivers"></a>Desteklenen dağıtımlar ve sürücüler

### <a name="nvidia-cuda-drivers"></a>NVıDıA CUDA sürücüleri

NC, NCv2, NCv3, ND ve NDv2 serisi VM 'Ler için NVıDıA CUDA sürücüleri (NV serisi için isteğe bağlı) yalnızca aşağıdaki tabloda listelenen Linux dağıtımları üzerinde desteklenir. CUDA sürücü bilgileri yayın sırasında geçerli. En son CUDA sürücüleri ve desteklenen işletim sistemleri için [NVIDIA](https://developer.nvidia.com/cuda-zone) Web sitesini ziyaret edin. Dağıtım için en son CUDA sürücülerini yüklediğinizden veya yükseltdiğinizden emin olun. 

> [!TIP]
> Bir Linux sanal makinesinde el ile CUDA sürücüsü yüklemeye alternatif olarak Azure [veri bilimi sanal makinesi](../articles/machine-learning/data-science-virtual-machine/overview.md) görüntüsünü dağıtabilirsiniz. Ubuntu 16,04 LTS veya CentOS 7,4 için DSVM sürümleri, NVıDıA CUDA sürücülerini, CUDA derin sinir ağ kitaplığını ve diğer araçları önceden yükler.


### <a name="nvidia-grid-drivers"></a>NVıDıA KıLAVUZ sürücüleri

Microsoft, sanal iş istasyonları veya sanal uygulamalar olarak kullanılan NV ve NVv3 serisi VM 'Ler için NVıDıA GRID sürücü yükleyicilerini yeniden dağıtır. Yalnızca aşağıdaki tabloda listelenen işletim sistemlerinde Azure NV VM 'lerine yalnızca bu KıLAVUZ sürücülerini yükler. Bu sürücüler Azure 'da GRID sanal GPU yazılımı için lisanslama içerir. NVıDıA vGPU yazılım lisans sunucusu ayarlamanız gerekmez.

Azure tarafından yeniden dağıtılan KıLAVUZ sürücüleri, NC, NCv2, NCv3, ND ve NDv2 serisi VM 'Ler gibi NV serisi olmayan VM 'lerde çalışmaz.

|Dağıtım|Sürücü|
| --- | -- |
|Ubuntu 18.04 LTS<br/><br/>Ubuntu 16.04 LTS<br/><br/>7,7 Red Hat Enterprise Linux 7,9, 8,0, 8,1<br/><br/>SUSE Linux Enterprise Server 12 SP2 <br/><br/>SUSE Linux Enterprise Server 15 SP2 | NVıDıA GRID 12,0, sürücü dalı [R460](https://go.microsoft.com/fwlink/?linkid=874272)(. exe)|

Önceki tüm NVIDIA GRID sürücü bağlantılarının listesinin tamamı için [GitHub](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) 'ı ziyaret edin.

> [!WARNING] 
> Red Hat ürünlerine üçüncü taraf yazılım yüklenmesi Red Hat destek koşullarını etkileyebilir. Bkz. [Red Hat Bilgi Bankası makalesi](https://access.redhat.com/articles/1067).
>
