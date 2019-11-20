---
title: include dosyası
description: include dosyası
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 08/08/2019
ms.author: azcspmt;jonbeck;cynthn;joelpell
ms.custom: include file
ms.openlocfilehash: c4e30c553ed7c697e1903d9745700755776700d2
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "74004209"
---
Genel amaçlı VM boyutları dengeli CPU-bellek oranını sağlar. Test ve geliştirme, küçük - orta boyutlu veritabanları, düşük - orta yoğunluklu trafiğe sahip web sunucuları için idealdir. Bu makalede, bu gruplandırmadaki boyutlarda vCPU sayısı, veri diskleri ve NIC 'lerin yanı sıra depolama aktarım hızı hakkında bilgi sağlanır.

- [DC Serisi](#dc-series) , Azure 'daki bir sanal makine ailesidir ve bu, genel bulutta işlendiği sırada verilerinizin ve kodunuzun gizliliğini ve bütünlüğünü korumanıza yardımcı olabilir. Bu makineler SGX teknolojisine sahip en son nesil 3,7 GHz Intel XEON E-2176G İşlemci ile donatılmıştır. Bu makineler Intel Turbo Boost Technology sayesinde 4,7 GHz’e kadar ulaşabilir. DC serisi örnekler, müşterilerin kod ve verilerini kullanım sırasında korumak için güvenli yalıtılmış alan tabanlı uygulamalar oluşturmasına imkan tanır.

- AV2 serisi VM 'Ler, çeşitli donanım türlerinde ve işlemcilerde dağıtılabilir. A serisi VM'ler, geliştirme ve test gibi giriş düzeyinde iş yüküne en uygun CPU performansına ve bellek yapılandırmasına sahiptir. Dağıtıldığı donanımdan bağımsız olarak, çalışan örneğe tutarlı işlemci performansı sunmak için boyut donanıma göre genişletilir. Bu boyutun dağıtıldığı fiziksel donanımı belirlemek için Sanal Makinenin içinden sanal donanımı sorgulayın.

  Örnek kullanım örnekleri arasında geliştirme ve test sunucuları, düşük trafikli web sunucuları, küçük ve orta ölçekli veritabanları, kavram kanıtı ve kod depoları bulunur.

- Özgün D serisi için bir takip olan Dv2 serisi, daha güçlü bir CPU ve en iyi CPU-bellek yapılandırmasına sahiptir ve bu da üretim iş yüklerinin çoğu için uygun hale getirir. Dv2 serisi, D serisinden daha hızlı %35 daha hızlıdır. Dv2 serisi, Intel Turbo Boost Technology 2,0 ile Intel® Xeon® 8171M 2.1 GHz (ufuk Gölü), Intel® Xeon® E5-2673 v4 2,3 GHz (çok Iyi) veya Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) işlemcileri üzerinde çalışır. Dv2 Serisi, D Serisi ile aynı bellek ve disk yapılandırmalarına sahiptir.

- Dv3 serisi Intel® Xeon® 8171M 2.1 GHz (ufuk Gölü), Intel® Xeon® E5-2673 v4 2,3 GHz (çok Iyi) veya bir hiper iş parçacıklı yapılandırmadaki Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) işlemcileri üzerinde çalışır ve en genel için daha iyi bir değer teklifi sağlar Amaç iş yükleri.  Bellek ve ağ sınırları, hiper iş parçacığına geçme ile hizalanmak üzere bir çekirdek temelinde ayarlanırken, bellek (~ 3,5 GiB/vCPU 'dan 4 GiB/vCPU 'ya) genişletildi.  Dv3 serisi artık D/dv2-Series ' in yüksek bellek sanal makine boyutlarına sahip değildir; bunlar [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory#ev3-series) ve [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#ev3-series)Için bellek için iyileştirilmiş Ev3 serisine taşınmıştır.

  Örnek D Serisi Kullanım örnekleri arasında kurumsal düzeyde uygulamalar, ilişkisel veritabanları, bellek içi önbelleğe alma ve analiz vardır.

- Dav4-Series ve Dasv4-Series, 128 GB L3 önbellek kullanan çok iş parçacıklı bir yapılandırmada, en 256 fazla 128 GB L3 önbellek kullanan<sup>yeni 7452</sup> boyutlardır. bu L3 önbelleğinin her 8 çekirdeğe kadar, genel olarak çalıştırılmasına yönelik müşteri seçeneklerini artıran Amaç iş yükleri. Dav4-Series ve Dasv4 serisi, D & Dsv3 serisi ile aynı bellek ve disk yapılandırmalarına sahiptir.
  
## <a name="b-series"></a>B serisi

Premium Depolama: desteklenir

Premium depolama önbelleği: desteklenmiyor

B serisi Burstable VM 'Ler, Web sunucuları, küçük veritabanları ve geliştirme ve test ortamları gibi her zaman CPU 'nun tam performansına gerek olmayan iş yükleri için idealdir. Bu iş yükleri genellikle ani performans gereksinimlerine sahiptir. B serisi, bu müşterilere VM 'nin temel performansından daha küçük bir performans üzerinden sanal makine örneğinin krediler oluşturmasını sağlayan bir fiyat bilincine sahip bir VM boyutu satın alma olanağı sağlar. VM 'nin birikmiş kredisi olduğunda, uygulamanız daha yüksek CPU performansı gerektirdiğinde CPU 'nun en fazla %100 ' ü kullanarak VM 'nin ana hat üzerinde veri bloğu oluşturabilir.

Örnek kullanım örnekleri arasında geliştirme ve test sunucuları, düşük trafikli web sunucuları, küçük veritabanları, mikro hizmetler, kavram kanıtı için sunucular, derleme sunucuları bulunur.


| Boyut             | Sanal işlemci  | Bellek: GiB | Geçici depolama (SSD) GiB | VM 'nin temel CPU performansı | VM 'nin en yüksek CPU performansı | İlk krediler | Bankaya açık/saat | Maksimum bankaya ait krediler | Maksimum veri diskleri | Maksimum önbelleğe alınmış ve geçici depolama aktarım hızı: ıOPS/MBps | Maksimum önbelleğe alınmamış disk aktarım hızı: IOPS-MB/sn | En fazla NIC |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | %5                   | %100                   | 30                   | 3                  | 72            | 2                                      | 200/10                                  | 160/10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | %10                   | %100                   | 30                   | 6                  | 144            | 2                        | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | %20                   | %100                   | 30                   | 12                 | 288           | 2                         | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | %40                   | 200%                   | 60                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 200%                   | 60                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400%                   | 120                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 240                   | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |
| Standard_B12ms | 12           | 48             | 6400/96                         | %202                  | %1200                   | 360                   | 121                 | 2909           | 16                                     | 6480/75                                 | 4320 / 50                                 | 6  |
| Standard_B16ms | 16           | 64             | 128                         | %270                  | %1600                   | 480                   | 162                 | 3888           | 32                                     | 8640/100                                 | 4320 / 50                                 | 8  |
| Standard_B20ms | 20           | 80             | 160                         | %337                  | %2000                   | 600                   | 203                 | 4860           | 32                                     | 10800/125                                 | 4320 / 50                                 | 8  |

<sup>1</sup> B1ls yalnızca Linux üzerinde desteklenir

## <a name="dsv3-series-sup1sup"></a>Dsv3-serisi <sup>1</sup>

ACU: 160-190

Premium Depolama: desteklenir

Premium depolama önbelleği: desteklenir

Dsv3 serisi boyutlar Intel® Xeon® 8171M 2.1 GHz (ufuk Gölü), Intel® Xeon® E5-2673 v4 2,3 GHz (çok Iyi) veya Intel 2,0 Turbo 2.1® E5-2673 v3 2,4 GHz (Haswell) işlemcilerinde çalışır ve Premium depolama kullanır. Dsv3 serisi boyutları, üretim iş yüklerinin çoğu için uygun bir vCPU, bellek ve geçici depolama kombinasyonu sunar.


| Boyut             | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum veri diskleri | Maksimum önbelleğe alınmış ve geçici depolama aktarım hızı: IOPS-MB/sn (önbellek boyutu GiB biriminde) | Maksimum önbelleğe alınmamış disk aktarım hızı: IOPS-MB/sn | En fazla NIC/beklenen ağ bant genişliği (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_D2s_v3  | 2      | 8           | 16             | 4              | 4000/32 (50)                                                       | 3200/48                                | 2 / 1000                                   |
| Standard_D4s_v3  | 4      | 16          | 32             | 8              | 8000/64 (100)                                                      | 6400/96                                | 2 / 2000                                   |
| Standard_D8s_v3  | 8      | 32          | 64             | 16             | 16000/128 (200)                                                    | 12800/192                              | 4 / 4000                                      |
| Standard_D16s_v3 | 16     | 64          | 128            | 32             | 32000/256 (400)                                                    | 25600/384                              | 8 / 8000                                      |
| Standard_D32s_v3 | 32     | 128          | 256            | 32             | 64000/512 (800)                                                    | 51200/768                              | 8 / 16000                                               |
| Standard_D48s_v3 | 48     | 192          | 384            | 32             | 96000/768 (1200)                                                    | 76800/1152                               | 8 / 24000                                               |
| Standard_D64s_v3 | 64     | 256          | 512            | 32             | 128000/1024 (1600)                                                    | 80000/1200                              | 8 / 30000                                               |

<sup>1</sup> DSV3 serisi VM 'Nin özellik Intel® Hiper Iş parçacığı teknolojisi

## <a name="dasv4-series"></a>Dasv4 serisi

ACU: 230-260

Premium Depolama: desteklenir

Premium depolama önbelleği: desteklenir

Dasv4 serisi Boyutlar, 3.35 GHz 'nin en yüksek frekansını ve Premium SSD 'yi kullanmayı sağlayan 2.35 GHz AMD EPIC<sup>TM</sup> 7452 işlemcisini temel alır. Dasv4 serisi boyutları, üretim iş yüklerinin çoğu için vCPU, bellek ve geçici depolama alanı birleşimini sunar.

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum veri diskleri | Maksimum önbelleğe alınmış ve geçici depolama aktarım hızı: IOPS-MB/sn (önbellek boyutu GiB biriminde) | Maksimum önbelleğe alınmamış disk aktarım hızı: IOPS-MB/sn | En fazla NIC/beklenen ağ bant genişliği (MBps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2as_v4|2|8|16|4|4000/32 (50)|3200/48|2 / 1000 |
| Standard_D4as_v4|4|16|32|8|8000/64 (100)|6400/96|2 / 2000 |
| Standard_D8as_v4|8|32|64|16|16000/128 (200)|12800/192|4 / 4000 |
| Standard_D16as_v4|16|64|128|32|32000/255 (400)|25600/384|8 / 8000 |
| Standard_D32as_v4|32|128|256|32|64000/510 (800)|51200/768|8 / 16000 |
| Standard_D48as_v4 <sup>**</sup>|48|192|384|32| | | 
| Standard_D64as_v4 <sup>**</sup>|64|256|512|32| | | 
| Standard_D96as_v4 <sup>**</sup>|6400/96|384|768|32| | | 

<sup>**</sup> Bu boyutlar önizlemededir.  Bu büyük boyutları denemek istiyorsanız [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview)' de kaydolun.

## <a name="dv3-series-sup1sup"></a>Dv3-serisi <sup>1</sup>

ACU: 160-190

Premium Depolama: desteklenmiyor

Premium depolama önbelleği: desteklenmiyor

Dv3 serisi Boyutlar, Intel Turbo Boost Technology 2,0 ile Intel® Xeon® 8171M 2.1 GHz (ufuk Gölü), Intel® Xeon® E5-2673 v4 2,3 GHz (çok Iyi) veya Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) işlemcileri üzerinde çalışır. Dv3 serisi boyutları, üretim iş yüklerinin çoğu için uygun bir vCPU, bellek ve geçici depolama kombinasyonu sunar.

Veri disk depolaması, sanal makinelerden ayrı olarak faturalandırılır. Premium depolama disklerini kullanmak için Dsv3 boyutlarını kullanın. Dsv3 boyutları için fiyatlandırma ve faturalandırma oranları Dv3 serisi ile aynıdır. 


| Boyut            | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum veri diskleri | Maksimum geçici depolama aktarım hızı: IOPS / Okuma MB/sn / Yazma MB/sn | Maksimum NIC/Ağ bant genişliği |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_D2_v3  | 2         | 8           | 50             | 4              | 3000/46/23                                               | 2 / 1000                    |
| Standard_D4_v3  | 4         | 16          | 100            | 8              | 6000/93/46                                               | 2 / 2000                    |
| Standard_D8_v3  | 8         | 32          | 200            | 16             | 12000/187/93                                             | 4 / 4000                    |
| Standard_D16_v3 | 16        | 64          | 400            | 32             | 24000/375/187                                            | 8 / 8000                    |
| Standard_D32_v3 | 32        | 128         | 800            | 32             | 48000/750/375                                            | 8 / 16000                   |
| Standard_D48_v3 | 48        | 192          | 1200            | 32             | 96000/1000/500                                            | 8 / 24000                             |
| Standard_D64_v3 | 64        | 256         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000                   |

<sup>1</sup> dv3 serisi VM 'Nin özellik Intel® Hiper Iş parçacığı teknolojisi

## <a name="dav4-series"></a>Dav4 serisi

ACU: 230-260

Premium Depolama: desteklenmiyor

Premium depolama önbelleği: desteklenmiyor

Dav4 serisi Boyutlar, 3.35 GHz 'nin en yüksek frekansını elde etmeye yönelik 2.35 GHz AMD EPıC<sup>TM</sup> 7452 işlemcisini temel alır. Dav4 serisi boyutları, üretim iş yüklerinin çoğu için vCPU, bellek ve geçici depolama alanı birleşimini sunar. Veri disk depolaması, sanal makinelerden ayrı olarak faturalandırılır. Premium SSD 'yi kullanmak için Dasv4 boyutlarını kullanın. Dasv4 boyutları için fiyatlandırma ve faturalandırma ölçümleri Dav4 serisi ile aynıdır.

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum veri diskleri | Maksimum geçici depolama aktarım hızı: IOPS / Okuma MB/sn / Yazma MB/sn | En fazla NIC/beklenen ağ bant genişliği (MBps) |
|-----|-----|-----|-----|-----|-----|-----|
| Standard_D2a_v4 |  2  | 8  | 50  | 4  | 3000/46/23   | 2 / 1000 |
| Standard_D4a_v4 |  4  | 16 | 100 | 8  | 6000/93/46   | 2 / 2000 |
| Standard_D8a_v4 |  8  | 32 | 200 | 16 | 12000/187/93 | 4 / 4000 |
| Standard_D16a_v4|  16 | 64 | 400 |32  | 24000/375/187 |8 / 8000 |
| Standard_D32a_v4|  32 | 128| 800 | 32 | 48000/750/375 |8 / 16000 |
| Standard_D48a_v4 <sup>**</sup> | 48 | 192| 1200 | 32 | | |
| Standard_D64a_v4 <sup>**</sup> | 64 | 256 | 1600 | 32 | | |
| Standard_D96a_v4 <sup>**</sup> | 6400/96 | 384 | 2400 | 32 | | |

<sup>**</sup> Bu boyutlar önizlemededir.  Bu büyük boyutları denemek istiyorsanız [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview)' de kaydolun.

## <a name="dsv2-series"></a>DSv2 serisi

ACU: 210-250

Premium Depolama: desteklenir

Premium depolama önbelleği: desteklenir

DSv2 serisi Boyutlar, Intel Turbo Boost Technology 2,0 ile Intel® Xeon® 8171M 2.1 GHz (2,3®® ufuk Gölü) veya Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) işlemcileri üzerinde çalışır ve Premium depolama kullanır.

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum veri diskleri | Maksimum önbelleğe alınmış ve geçici depolama aktarım hızı: IOPS-MB/sn (önbellek boyutu GiB biriminde) | Maksimum önbelleğe alınmamış disk aktarım hızı: IOPS-MB/sn | En fazla NIC/beklenen ağ bant genişliği (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3,5 |7 |4 |4000/32 (43) |3200/48 |2 / 750 |
| Standard_DS2_v2 |2 |7 |14 |8 |8000/64 (86) |6400/96 |2 / 1500 |
| Standard_DS3_v2 |4 |14 |28 |16 |16000/128 (172) |12800/192 |4 / 3000 |
| Standard_DS4_v2 |8 |28 |56 |32 |32000/256 (344) |25600/384 |8 / 6000 |
| Standard_DS5_v2 |16 |56 |112 |64 |64000/512 (688) |51200/768 |8 / 12000 |

## <a name="dv2-series"></a>Dv2 Serisi

ACU: 210-250

Premium Depolama: desteklenmiyor

Premium depolama önbelleği: desteklenmiyor

DSv2 serisi Boyutlar, Intel Turbo Boost Technology 2,0 ile Intel® Xeon® 8171M 2.1 GHz (ufuk Gölü)® veya Intel® 2,4 Xeon® E5-2673 v4 2,3 GHz (Haswell) işlemcileri üzerinde çalışır.

| Boyut           | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum geçici depolama aktarım hızı: IOPS / Okuma MB/sn / Yazma MB/sn | Maksimum veri diskleri | Aktarım hızı: ıOPS | En fazla NIC/beklenen ağ bant genişliği (Mbps) |
|----------------|------|-------------|------------------------|------------------------------------------------------------|----------------|------------------|----------------------------------------------|
| Standard_D1_v2 | 1    | 3,5         | 50                     | 3000/46/23                                             | 4              | 4x500            | 2 / 750                                      |
| Standard_D2_v2 | 2    | 7           | 100                    | 6000/93/46                                             | 8              | 8x500            | 2 / 1500                                     |
| Standard_D3_v2 | 4    | 14          | 200                    | 12000/187/93                                           | 16             | 16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000/375/187                                          | 32             | 32x500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000/750/375                                          | 64             | 64x500           | 8 / 12000                                    |

## <a name="av2-series"></a>Av2 Serisi

ACU: 100

Premium Depolama: desteklenmiyor

Premium depolama önbelleği: desteklenmiyor

AV2 serisi Boyutlar, Intel Turbo Boost Technology 2,0 ile Intel® Xeon® 8171M 2.1 GHz (2,3®® ufuk Gölü) veya Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) işlemcileri üzerinde çalışır ve Premium depolama kullanır.

| Boyut            | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum geçici depolama aktarım hızı: IOPS / Okuma MB/sn / Yazma MB/sn | Maksimum veri diski/aktarım hızı: IOPS | En fazla NIC/beklenen ağ bant genişliği (Mbps) | 
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1000/20/10                                           | 2/2x500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000/40/20                                           | 4/4x500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000/80/40                                           | 8/8x500               | 4 / 1000                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8000/160/80                                          | 16/16x500             | 8 / 2000                     |
| Standard_A2m_v2 | 2         | 16          | 20             | 2000/40/20                                           | 4/4x500               | 2 / 500                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4000/80/40                                           | 8/8x500               | 4 / 1000                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8000/160/80                                          | 16/16x500             | 8 / 2000                     |

## <a name="dc-series"></a>DC serisi

Premium Depolama: desteklenir

Premium depolama önbelleği: desteklenir



| Boyut          | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum veri diskleri | Maksimum önbelleğe alınmış ve geçici depolama aktarım hızı: IOPS-MB/sn (önbellek boyutu GiB biriminde) | Maksimum önbelleğe alınmamış disk aktarım hızı: IOPS-MB/sn | En fazla NIC/beklenen ağ bant genişliği (Mbps) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                          | 3200/48                                  | 2 / 1500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                          | 6400/96                                  | 2 / 3000                                     |
