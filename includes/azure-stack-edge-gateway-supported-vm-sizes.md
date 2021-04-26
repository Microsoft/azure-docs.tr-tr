---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/27/2021
ms.author: alkohli
ms.openlocfilehash: f92dc476d3bc79fdb522424e36e44a13644c725e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078816"
---
VM boyutu, sanal makine için kullanılabilir hale getirilen işlem kaynaklarının (CPU, GPU ve bellek gibi) miktarını belirler. İş yükü için uygun bir VM boyutu kullanarak sanal makineler oluşturmalısınız. Tüm makineler aynı donanımda çalışmaya rağmen, makine boyutları disk erişimi için farklı sınırlara sahip olur. Bu, VM 'leriniz genelinde genel disk erişimini yönetmenize yardımcı olabilir. Bir iş yükü arttıkça, var olan bir sanal makineyi de yeniden boyutlandırabilirsiniz.

Aşağıdaki VM 'Ler Azure Stack Edge cihazınızda oluşturma için desteklenir.

### <a name="dv2-series"></a>Dv2 Serisi
|Boyut     |Sanal işlemci     |Bellek (GiB) | Geçici depolama (GiB)   | Maksimum veri diskleri | En fazla NIC |
|-------------------|----|----|-----|----|------|
|**Standard_D1_v2** |1   |3,5 |50   | 4    |2 |
|**Standard_D2_v2** |2   |7   |100  | 8    |4 |
|**Standard_D3_v2** |4   |14  |200  | 16  |4 |
|**Standard_D4_v2** |8   |28  |400  | 32  |8 |
|**Standard_D5_v2** |16  |56  |800  | 64  |8 |
|**Standard_D11_v2** |2   |14  |100 | 8     |2 |
|**Standard_D12_v2** |4   |28  |200  | 16   |4 |
|**Standard_D13_v2** |8   |56  |400  | 32  |8 |

### <a name="dsv2-series"></a>DSv2 serisi
|Boyut     |Sanal işlemci     |Bellek (GiB) | Geçici depolama (GiB)  | Maksimum veri diskleri| 
|--------------------|----|----|----|-----|
|**Standard_DS1_v2** |1   |3,5 |4000  |4  | 
|**Standard_DS2_v2** |2   |7   |8000  |8  | 
|**Standard_DS3_v2** |4   |14  |16000 |16 | 
|**Standard_DS4_v2** |8   |28  |32000 |32 | 
|**Standard_DS5_v2** |16  |56  |64000 |64 |  
|**Standard_DS11_v2**|2   |14  |8000  |4  | 
|**Standard_DS12_v2**|4   |28  |16000 |8  | 
|**Standard_DS13_v2**|8   |56  |32000 |16 | 


Daha fazla bilgi için bkz. [dv2 and DSv2-Series](../articles/virtual-machines/dv2-dsv2-series.md#dv2-series).

### <a name="ncast4_v3-series-preview"></a>NCasT4_v3 serisi (Önizleme)

Bu boyutlar, cihazınızdaki GPU VM 'Leri için desteklenir ve yoğun işlem yoğunluğu olan GPU hızlandırmalı uygulamalar için iyileştirilmiştir. Bu seri, NVIDIA Tesla T4 GPU 'SU olan çıkarım iş yüklerine odaklanılmıştır. 

|Boyut     |Sanal işlemci     |Bellek (GiB) | Geçici depolama (GiB)  | GPU | GPU belleği (GiB) | En fazla NIC |
|---------------------|----|----|-----|-----|-------|--------------|
|**Standard_NC4as_T4_v3** |4   |28  |180   |1 |16   |4 |
|**Standard_NC8as_T4_v3** |8   |56  |360   |1 |16  |8 |

Daha fazla bilgi için bkz. [NCasT4_v3 serisi](../articles/virtual-machines/nct4-v3-series.md).

### <a name="f-series"></a>F Serisi

Bu seriler hesaplama iş yükleri için iyileştirilmiştir ve Intel Xeon işlemcilerde çalışır. 

| Boyut | vCPU 'nun | Bellek: GiB | Geçici depolama (GiB) |  Maksimum veri diskleri | En fazla NIC |
|---|---|---|---|---|---|
| Standard_F1  | 1  | 2   |16      | 4  |  2 |
| Standard_F2 | 2  | 4 |32      | 8  |  4 |
| Standard_F4  | 4  | 8 |64   | 16 |  4 |
| Standard_F8 | 8 | 16  |132    | 32 |  8 |
| Standard_F16 | 16 | 32  |262   | 64 |  8 |
| Standard_F1s | 1 | 2  | 4  | 4 | 1 |
| Standard_F2s | 2 | 4 |8   | 8 | 4 |
| Standard_F4s | 4 | 8 |16 | 16 |  4 |
| Standard_F8s | 8 | 16 |32 | 32 |  8 |
| Standard_F16s | 16 | 32 |64 | 64 |  8 |

Daha fazla bilgi için bkz. [Fsv2-Series](../articles/virtual-machines/fsv2-series.md).

