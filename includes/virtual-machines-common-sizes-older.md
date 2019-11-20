---
title: include dosyası
description: include dosyası
services: virtual-machines-windows, virtual-machines-linux
author: laurenhughes
ms.service: multiple
ms.topic: include
ms.date: 08/15/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: a4746a945f1a89c34308a3bd968f6341e0e25ac5
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69541557"
---
Bu bölüm, daha eski nesil sanal makine boyutları hakkında bilgi sağlar. Bu boyutlar hala desteklenir, ancak ek kapasite almaz. Genel olarak kullanılabilen daha yeni veya alternatif boyutlar vardır. Gereksinimlerinize en uygun VM boyutlarını seçmek için lütfen Azure 'daki [Windows sanal makinelerinin boyutlara](../articles/virtual-machines/windows/sizes.md) veya [Azure 'da Linux sanal makineleri](../articles/virtual-machines/linux/sizes.md) için boyutlara bakın.  

Linux VM 'yi yeniden boyutlandırma hakkında daha fazla bilgi için bkz. [Azure CLI kullanarak Linux sanal makinesini yeniden boyutlandırma](../articles/virtual-machines/linux/change-vm-size.md). Windows VM 'lerini kullanıyorsanız ve PowerShell 'i kullanmayı tercih ediyorsanız, bkz. [WINDOWS VM 'Yi yeniden boyutlandırma](../articles/virtual-machines/windows/resize-vm.md).  

<br>

### <a name="basic-a"></a>Temel A  

**Daha yeni boyut önerisi**: [AV2 serisi](../articles/virtual-machines/windows/sizes-general.md#av2-series)

Premium Depolama:  Desteklenmiyor

Premium depolama önbelleği:  Desteklenmiyor

Temel katman boyutları genelde geliştirme iş yükleri ve yük dengeleme, otomatik ölçeklendirme veya bellek kullanımı yoğun sanal makineler gerektirmeyen diğer uygulamalar içindir.

|Boyut – Boyut\Ad | Sanal işlemci |Bellek|NIC'ler (Maks.)|En fazla geçici disk boyutu |En çok, veri diskleri (1023 GB)|En çok, IOPS (disk başına 300)|
|---|---|---|---|---|---|---|
|A0\Basic_A0|1\.|768 MB|2| 20 GB|1\.|1x300|
|A1\Basic_A1|1\.|1,75 GB|2| 40 GB |2|2x300|
|A2\Basic_A2|2|3,5 GB|2| 60 GB|4|4x300|
|A3\Basic_A3|4|7 GB|2| 120 GB |8|8x300|
|A4\Basic_A4|8|14 GB|2| 240 GB |16|16x300|

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0 - A4, CLI ve PowerShell kullanarak

Klasik dağıtım modelinde bazı VM boyutu adları CLI ve PowerShell'dekilerden biraz farklıdır:

* Standard_A0 = Çok Küçük
* Standard_A1 = Küçük
* Standard_A2 = Orta
* Standard_A3 = Büyük
* Standard_A4 = Çok Büyük

### <a name="a-series"></a>A Serisi  

**Daha yeni boyut önerisi**: [AV2 serisi](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU 50-100

Premium Depolama:  Desteklenmiyor

Premium depolama önbelleği:  Desteklenmiyor

| Size | Sanal işlemci | Bellek: GiB | Geçici depolama (HDD): GiB | Maksimum veri diskleri | Maksimum veri diski performansı: IOPS | En fazla NIC/beklenen ağ bant genişliği (Mbps)  |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0&nbsp;<sup>1</sup> |1\. |0,768 |20 |1\. |1x500 |2 / 100 |
| Standard_A1 |1\. |1,75 |70 |2 |2x500 |2 / 500  |
| Standard_A2 |2 |3,5 |135 |4 |4x500 |2 / 500 |
| Standard_A3 |4 |7 |285 |8 |8x500 |2 / 1000 |
| Standard_A4 |8 |14 |605 |16 |16x500 |4 / 2000 |
| Standard_A5 |2 |14 |135 |4 |4x500 |2 / 500 |
| Standard_A6 |4 |28 |285 |8 |8x500 |2 / 1000 |
| Standard_A7 |8 |56 |605 |16 |16x500 |4 / 2000 |

<sup>1</sup> a0 boyutu, fiziksel donanımda abone olunmuş. Yalnızca bu boyutta diğer müşteri dağıtımları, çalışan iş yükünüzün performansını etkileyebilir. Göreli performans aşağıda beklenen temel düzey olarak belirtilmiştir ve %15 oranında değişiklik gösterebilir.

<br>

### <a name="a-series---compute-intensive-instances"></a>A Serisi - Yoğun işlem gücü kullanımlı örnekler  

**Daha yeni boyut önerisi**: [AV2 serisi](../articles/virtual-machines/windows/sizes-general.md#av2-series)

ACU 225

Premium Depolama:  Desteklenmiyor

Premium depolama önbelleği:  Desteklenmiyor

A8-A11 ve H Serisi boyutlar *yoğun işlem gücü kullanımlı örnekler* olarak da bilinir. Bu boyutları çalıştıran donanım; yüksek performanslı bilgi işlem (HPC) kümesi uygulamaları, modellemeler ve simülasyonlar gibi yoğun işlem ve ağ kullanımlı uygulamalar için tasarlanmış ve iyileştirilmiştir. A8-A11 Serisinde, Intel Xeon E5-2670 @ 2,6 GHZ, H Serisinde ise Intel Xeon E5-2667 v3 @ 3,2 GHz işlemciler kullanılmaktadır.  

| Size | Sanal işlemci | Bellek: GiB | Geçici depolama (HDD): GiB | Maksimum veri diskleri | Maksimum veri diski performansı: IOPS | En fazla NIC|
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8&nbsp;<sup>1</sup> |8 |56 |382 |32 |32x500 |2 |
| Standard_A9&nbsp;<sup>1</sup> |16 |112 |382 |64 |64x500 |4 |
| Standard_A10 |8 |56 |382 |32 |32x500 |2  |
| Standard_A11 |16 |112 |382 |64 |64x500 |4 |

<sup>1</sup> MPı uygulamaları için, adanmış RDMA arka uç ağı, son derece düşük gecikme süresi ve yüksek bant genişliği sunan FDR InfiniBand ağı tarafından etkinleştirilir.  

<br>

### <a name="d-series"></a>D Serisi  

**Daha yeni boyut önerisi**: [Dv3 serisi](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU 160-250 <sup>1</sup>

Premium Depolama:  Desteklenmiyor

Premium depolama önbelleği:  Desteklenmiyor

| Size         | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | En yüksek geçici depolama aktarım hızı: IOPS/okuma MBps/yazma MBps | Maksimum veri diski/aktarım hızı: IOPS | En fazla NIC/beklenen ağ bant genişliği (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D1  | 1\.         | 3,5         | 50             | 3000/46/23                                           | 4/4x500                         | 2 / 500                 |
| Standard_D2  | 2         | 7           | 100            | 6000/93/46                                           | 8/8x500                         | 2 / 1000                     |
| Standard_D3  | 4         | 14          | 200            | 12000/187/93                                         | 16/16x500                         | 4 / 2000                     |
| Standard_D4  | 8         | 28          | 400            | 24000/375/187                                        | 32/32x500                       | 8 / 4000                     |

<sup>1</sup> VM AILESI aşağıdaki CPU 'dan birinde çalıştırılabilir: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) veya 2,3 GHz Intel XEON® E5-2673 v4 (çok Iyi)  

<br>

### <a name="d-series---memory-optimized"></a>D serisi-bellek için iyileştirilmiş  

**Daha yeni boyut önerisi**: [Dv3 serisi](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1)

ACU 160-250 <sup>1</sup>

Premium Depolama:  Desteklenmiyor

Premium depolama önbelleği:  Desteklenmiyor

| Size         | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | En yüksek geçici depolama aktarım hızı: IOPS/okuma MBps/yazma MBps | Maksimum veri diski/aktarım hızı: IOPS | En fazla NIC/beklenen ağ bant genişliği (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11 | 2         | 14          | 100            | 6000/93/46                                           | 8/8x500                         | 2 / 1000                     |
| Standard_D12 | 4         | 28          | 200            | 12000/187/93                                         | 16/16x500                         | 4 / 2000                     |
| Standard_D13 | 8         | 56          | 400            | 24000/375/187                                        | 32/32x500                       | 8 / 4000                     |
| Standard_D14 | 16        | 112         | 800            | 48000/750/375                                        | 64/64x500                       | 8 / 8000                |

<sup>1</sup> VM AILESI aşağıdaki CPU 'dan birinde çalıştırılabilir: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) veya 2,3 GHz Intel XEON® E5-2673 v4 (çok Iyi)  

<br>

### <a name="ds-series"></a>DS serisi  

**Daha yeni boyut önerisi**: [DSv3 serisi](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU 160-250 <sup>1</sup>

Premium Depolama:  Desteklenen

Premium depolama önbelleği:  Desteklenen

| Size | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum veri diskleri | Maksimum önbelleğe alınmış ve geçici depolama aktarım hızı: IOPS/MBps (GiB 'de önbellek boyutu) | Maksimum önbelleğe alınmamış disk aktarım hızı: IOPS/MBps | En fazla NIC/beklenen ağ bant genişliği (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1 |1\. |3,5 |7 |4 |4000/32 (43) |3200/32 |2 / 500 |
| Standard_DS2 |2 |7 |14 |8 |8000/64 (86) |6400/64 |2 / 1000 |
| Standard_DS3 |4 |14 |28 |16 |16.000/128 (172) |12.800/128 |4 / 2000 |
| Standard_DS4 |8 |28 |56 |32 |32.000/256 (344) |25.600/256 |8 / 4000 |

<sup>1</sup> VM AILESI aşağıdaki CPU 'dan birinde çalıştırılabilir: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) veya 2,3 GHz Intel XEON® E5-2673 v4 (çok Iyi)  

<br>

### <a name="ds-series---memory-optimized"></a>DS serisi bellek için iyileştirilmiş  

**Daha yeni boyut önerisi**: [DSv3 serisi](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general#dsv3-series-1)

ACU 160-250 <sup>1, 2</sup>

Premium Depolama:  Desteklenen

Premium depolama önbelleği:  Desteklenen

| Size | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum veri diskleri | Maksimum önbelleğe alınmış ve geçici depolama aktarım hızı: IOPS/MBps (GiB 'de önbellek boyutu) | Maksimum önbelleğe alınmamış disk aktarım hızı: IOPS/MBps | En fazla NIC/beklenen ağ bant genişliği (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11 |2 |14 |28 |8 |8000/64 (72) |6400/64 |2 / 1000 |
| Standard_DS12 |4 |28 |56 |16 |16.000/128 (144) |12.800/128 |4 / 2000 |
| Standard_DS13 |8 |56 |112 |32 |32.000/256 (288) |25.600/256 |8 / 4000 |
| Standard_DS14 |16 |112 |224 |64 |64.000/512 (576) |51.200/512 |8 / 8000 |

<sup>1</sup> DS serisi VM ile olası maksimum disk aktarım hızı (IOPS veya MB/sn), eklenen disklerin sayısı, boyutu ve şeritle sınırlı olabilir.  Ayrıntılar için bkz. [yüksek performans Için tasarlama](../articles/virtual-machines/windows/premium-storage-performance.md).   
<sup>2</sup> VM AILESI aşağıdaki CPU 'dan birinde çalıştırılabilir: 2,2 GHz Intel Xeon® E5-2660 v2, 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) veya 2,3 GHz Intel XEON® E5-2673 v4 (çok Iyi)  

<br>

### <a name="ls-series"></a>Ls serisi

Ls serisi, [Intel® Xeon İşlemci E5 v3 ailesi](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html) ile 32’ye kadar vCPU kullanım olanağı sunar. Ls serisi, G/GS serisi ile aynı CPU performansı sunar ve her vCPU başına 8 GiB bellek içerir.

Ls serisi, dayanıklı veri diskleri tarafından ulaşılabilir ıOPS 'yi artırmak için yerel bir önbellek oluşturulmasını desteklemez. Yerel diskin yüksek işleme ve ıOPS değeri, tek bir VM 'nin arızası durumunda Kalıcılık sağlamak için verileri birden çok VM arasında çoğaltan Apache Cassandra ve MongoDB gibi NoSQL depoları için ideal olan LS Serisi VM 'Leri sağlar.

ACU 180-240

Premium Depolama:  Desteklenen

Premium depolama önbelleği:  Desteklenmiyor
 
| Size          | Sanal işlemci | Bellek (GiB) | Geçici depolama (GiB) | Maksimum veri diskleri | En fazla geçici depolama aktarım hızı (ıOPS/MBps) | Önbelleğe alınmamış maksimum disk aktarım hızı (ıOPS/MBps) | En fazla NIC/beklenen ağ bant genişliği (Mbps) | 
|----------------|-----------|-------------|--------------------------|----------------|-------------------------------------------------------------|-------------------------------------------|------------------------------| 
| Standart_L4s   | 4  | 32  | 678   | 16 | 20000/200 | 5000/125  | 2 / 4000  | 
| Standart_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4 / 8000  | 
| Standart_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8 / 16000 | 
| Standard_L32s&nbsp;<sup>1</sup> | 32   | 256  | 5630 | 64   | 160000/1600   | 40000/1000     | 8 / 20000 | 

LS Serisi VM 'lerle mümkün olan en fazla disk aktarım hızı, ekli disklerin sayısı, boyutu ve şeritleriyle sınırlı olabilir. Ayrıntılar için bkz. [yüksek performans Için tasarlama](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>1</sup> örnek, tek bir müşteriye adanmış donanımlar için yalıtılmıştır.

### <a name="gs-series"></a>GS serisi 

ACU 180-240 <sup>1</sup>

Premium Depolama:  Desteklenen

Premium depolama önbelleği:  Desteklenen

| Size | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum veri diskleri | Maksimum önbelleğe alınmış ve geçici depolama aktarım hızı: IOPS/MBps (GiB 'de önbellek boyutu) | Maksimum önbelleğe alınmamış disk aktarım hızı: IOPS/MBps | En fazla NIC/beklenen ağ bant genişliği (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 |2 |28 |56 |8 |10.000/100 (264) |5000/125 |2 / 2000 |
| Standard_GS2 |4 |56 |112 |16 |20.000/200 (528) |10.000/250 |2 / 4000 |
| Standard_GS3 |8 |112 |224 |32 |40.000/400 (1056) |20.000/500 |4 / 8000 |
| Standard_GS4&nbsp;<sup>3</sup> |16 |224 |448 |64 |80.000/800 (2112) |40.000/1000 |8 / 16000 |
| Standard_GS5&nbsp;<sup>2,&nbsp;3</sup> |32 |448 |896 |64 |160.000/1600 (4224) |80.000/2000 |8 / 20000 |

<sup>1</sup> GS serisi VM ile mümkün olan maksimum disk aktarım hızı (IOPS veya MB/sn), eklenen disklerin sayısı, boyutu ve şeritle sınırlı olabilir. Ayrıntılar için bkz. [yüksek performans Için tasarlama](../articles/virtual-machines/windows/premium-storage-performance.md).

<sup>2</sup> örnek, tek bir müşteriye adanmış donanımlar için yalıtılmıştır.

<sup>3</sup> kısıtlı çekirdek boyutu var.

<br>

### <a name="g-series"></a>G Serisi

ACU 180-240

Premium Depolama:  Desteklenmiyor

Premium depolama önbelleği:  Desteklenmiyor

| Size         | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | En yüksek geçici depolama aktarım hızı: IOPS/okuma MBps/yazma MBps | Maksimum veri diski/aktarım hızı: IOPS | En fazla NIC/beklenen ağ bant genişliği (Mbps) |
|--------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_G1  | 2         | 28          | 384            | 6000/93/46                                           | 8/8x500                       | 2 / 2000                     |
| Standard_G2  | 4         | 56          | 768            | 12000/187/93                                         | 16/16x500                       | 2 / 4000                     |
| Standard_G3  | 8         | 112         | 1536          | 24000/375/187                                        | 32/32x500                     | 4 / 8000                |
| Standard_G4  | 16        | 224         | 3072          | 48000/750/375                                        | 64/64x500                     | 8 / 16000          |
| Standard_G5&nbsp;<sup>1</sup> | 32        | 448         | 6144          | 96000/1500/750                                       | 64/64x500                     | 8 / 20000           |

<sup>1</sup> örnek, tek bir müşteriye adanmış donanımlar için yalıtılmıştır.
<br>
