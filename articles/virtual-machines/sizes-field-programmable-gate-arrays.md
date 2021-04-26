---
title: Alan için Azure sanal makine boyutları-programlanabilir kapı dizileri (FPGA)
description: Azure 'daki sanal makineler için kullanılabilen farklı FPGA iyileştirilmiş boyutları listeler. Bu serideki boyutlarda vCPU sayısı, veri diskleri ve NIC 'lerin yanı sıra depolama aktarım hızı ve ağ bant genişliği hakkındaki bilgileri listeler.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-fpga
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: vikancha
ms.openlocfilehash: d9eb0d5bc93cbe9c2a7cbae56c336115bb227b04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557684"
---
# <a name="fpga-optimized-virtual-machine-sizes"></a>FPGA iyileştirilmiş sanal makine boyutları

FPGA iyileştirilmiş VM boyutları, tek veya birden çok FPGAs ile sunulan özelleştirilmiş sanal makinelerdir. Bu boyutlar, işlem yoğunluğu yoğun iş yükleri için tasarlanmıştır. Bu makalede, FPGAs, vCPU 'Lar, veri diskleri ve NIC 'lerin sayısı ve türü hakkında bilgi sağlanır. Bu gruplandırmadaki her boyut için depolama verimlilik ve ağ bant genişliği de mevcuttur.

- [NP serisi](np-series.md) boyutları makine öğrenme çıkarımı, video dönüştürme kodu ve veritabanı arama & analizi gibi iş yükleri için iyileştirilmiştir. NP serisi Xilinx U250 hızlandırıcıları tarafından desteklenir.


## <a name="deployment-considerations"></a>Dağıtma konuları

- N serisi VM 'lerin kullanılabilirliği için bkz. [bölgelere göre kullanılabilir ürünler](https://azure.microsoft.com/regions/services/).

- N serisi VM 'Ler yalnızca Kaynak Yöneticisi dağıtım modelinde dağıtılabilir.

- Birkaç tane N serisi VM dağıtmak istiyorsanız, Kullandıkça Öde aboneliğine veya diğer satın alma seçeneklerine göz önünde bulundurun. [Ücretsiz Azure hesabı](https://azure.microsoft.com/free/) kullanıyorsanız, yalnızca sınırlı sayıda Azure işlem çekirdeği kullanabilirsiniz.

- Azure aboneliğinizdeki çekirdek kotasını (bölge başına) artırmanız ve NP çekirdekleri için ayrı kotayı artırmanız gerekebilir. Kota artışı istemek için, ücretsiz [bir çevrimiçi müşteri destek isteği açın](../azure-portal/supportability/how-to-create-azure-support-request.md) . Varsayılan sınırlar, abonelik kategorime bağlı olarak değişebilir.

## <a name="other-sizes"></a>Diğer boyutlar

- [Genel amaçlı](sizes-general.md)
- [İşlem için iyileştirilmiş](sizes-compute.md)
- [GPU hızlandırılmış işlem](sizes-gpu.md)
- [Yüksek performanslı işlem](sizes-hpc.md)
- [Bellek için iyileştirilmiş](sizes-memory.md)
- [Depolama için iyileştirilmiş](sizes-storage.md)
- [Önceki nesiller](sizes-previous-gen.md)

## <a name="next-steps"></a>Sonraki adımlar

Azure [işlem birimlerinin (ACU)](acu.md) Azure SKU 'ları genelinde işlem performansını karşılaştırmanıza nasıl yardımcı olabileceğini öğrenin.
