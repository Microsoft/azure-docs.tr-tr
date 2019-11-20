---
title: include dosyası
description: include dosyası
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 10/17/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: da47a471ddcf2c083ed127b79e4d3da9713d2ed4
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719064"
---
Bellek için iyileştirilmiş VM boyutları, ilişkisel veritabanı sunucuları, orta ve büyük önbellekler ve bellek içi analizler için harika olan yüksek bir bellek-CPU oranı sunar. Bu makalede, bu gruplandırmadaki her bir boyut için sanal CPU sayısı, veri diskleri ve NIC 'lerin yanı sıra depolama aktarım hızı ve ağ bant genişliği hakkında bilgi sağlanır.

* Ev3-Series, bir hiper iş yükleri için Intel® Xeon® 8171M 2,1 GHz (ufuk Gölü) veya Intel® Xeon® E5-2673 v4 2,3 GHz (geniş kapsamlı) işlemciler sunar ve bu, genel amaçlı iş yükleri için daha iyi bir değer teklifi sağlar ve Ev3 diğer bulutların çoğu için genel amaçlı VM 'Ler ile hizalama.  Bellek ve ağ sınırları, hiper iş parçacığına geçme ile hizalanmak üzere bir çekirdek temelinde ayarlandığı sürece (7 GiB/vCPU 'dan 8 GiB/vCPU 'ya kadar) bellek genişletilir.  Ev3, D/dv2 ailelerinin yüksek bellek sanal makine boyutlarına göre takip edilir.

* Eav4-Series ve Easv4 serisi, en fazla 256 MB boyutlu önbellek kullanan çok iş parçacıklı bir yapılandırmada AMD 'nin 2.35 GHz EPYıC<sup>TM</sup> 7452 işlemcisini kullanır ve en fazla bellek için iyileştirilmiş iş yüklerini çalıştırmaya yönelik seçenekleri artırır.  Eav4-Series ve Easv4 serisi, Ev3 & Esv3 serisi ile aynı bellek ve disk yapılandırmalarına sahiptir.

* Mv2-Series, buluttaki herhangi bir VM 'nin en yüksek vCPU sayısını (416 vCPU 'ya kadar) ve en büyük bellek miktarını (8,19 TiB 'ye kadar) sunar. Son derece büyük veritabanları veya yüksek vCPU sayısı ve büyük miktarlarda belleğin yararlı olacağı diğer uygulamalar için idealdir.

* M serisi, yüksek bir vCPU sayısı (128 vCPU kadar) ve büyük miktarda bellek (3,8 TiB 'ye kadar) sunar. Son derece büyük veritabanları veya yüksek sanal CPU sayılarından ve büyük miktarda bellekten faydalanabilir diğer uygulamalar için de idealdir.

* Dv2 serisi, G serisi ve DSv2/GS karşılıkları, daha hızlı vCPU 'Ları, daha iyi geçici depolama performansını talep eden veya daha yüksek bellek taleplerine sahip olan uygulamalar için idealdir. Bu seçenekler birçok kurumsal sınıf uygulama için güçlü bir bileşim sunar.

* Orijinal D Serisinin üzerine geliştirilen Dv2 Serisi, daha güçlü bir CPU'ya sahiptir. Dv2 serisi, D serisinden daha hızlı %35 daha hızlıdır. Intel® Xeon® 8171M 2,1 GHz (ufuk Gölü) veya Intel® Xeon® E5-2673 v4 2,3 GHz (geniş) veya Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) işlemcileri üzerinde çalışır ve Intel Turbo Boost Technology 2,0. Dv2 Serisi, D Serisi ile aynı bellek ve disk yapılandırmalarına sahiptir.

* Azure Compute, belirli bir donanım türüyle sınırlanmış ve tek bir müşteriye ayrılmış olan sanal makine boyutları sunar.  Bu sanal makine boyutları, uyumluluk ve düzenleme gereksinimleri gibi öğeler içeren iş yükleri nedeniyle diğer müşterilerden yüksek ölçüde yalıtıma ihtiyaç duyan iş yükleri için idealdir.  Müşteriler Ayrıca, [iç içe geçmiş sanal makineler Için Azure desteği](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)'ni kullanarak bu yalıtılmış sanal makinelerin kaynaklarını daha fazla alt bölümlere ayırmak da tercih edebilir.  Yalıtılmış VM seçenekleriniz için lütfen aşağıdaki sanal makine ailelerinin tablolarına bakın.

## <a name="esv3-series"></a>Esv3 serisi

ACU: 160-190 <sup>1</sup>

Premium Depolama: desteklenir

Premium depolama önbelleği: desteklenir

ESv3 serisi örnekler, Intel® Xeon® 8171M 2,1 GHz (ufuk Gölü) veya Intel® Xeon® E5-2673 v4 2,3 GHz (geniş) işlemcileri özelliğini kullanır ve Intel Turbo Boost Technology 2,0 ile 3,5 GHz elde edebilir ve Premium depolama kullanabilir. Ev3 serisi örnekleri, yoğun bellek kullanımlı kurumsal uygulamalar için idealdir.


| Boyut             | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | En fazla veri diski | Maksimum önbelleğe alınmış ve geçici depolama aktarım hızı: IOPS-MB/sn (önbellek boyutu GiB biriminde) | Maksimum önbelleğe alınmamış disk aktarım hızı: IOPS-MB/sn | En fazla NIC/beklenen ağ bant genişliği (Mbps) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_E2s_v3 | 2      | 16          | 32             | 4              | 4000/32 (50)                                                       | 3200/48                                | 2 / 1000                                   |
| Standard_E4s_v3&nbsp;<sup>2</sup> | 4      | 32          | 64             | 8              | 8000/64 (100)                                                      | 6400/96                                | 2 / 2000                                   |
| Standard_E8s_v3&nbsp;<sup>2</sup> | 8      | 64          | 128            | 16             | 16000/128 (200)                                                    | 12800/192                              | 4 / 4000                                       |
| Standard_E16s_v3&nbsp;<sup>2</sup> | 16     | 128         | 256            | 32             | 32000/256 (400)                                                    | 25600/384                              | 8 / 8000                                       |
| Standard_E20s_v3                   | 20     | 160         | 320            | 32             | 40000/320 (400)                                                    | 32000/480                              | 8 / 10000                                       |
| Standard_E32s_v3&nbsp;<sup>2</sup> | 32     | 256         | 512            | 32             | 64000/512 (800)                                                    | 51200/768                              | 8 / 16000                             |
| Standard_E48s_v3&nbsp;<sup>2</sup> | 48     | 384         | 768            | 32             | 96000/768 (1200)                                                   | 76800/1152                             | 8 / 24000                             |
| Standard_E64s_v3&nbsp;<sup>2</sup> | 64     | 432         | 864            | 32             | 128000/1024 (1600)                                                   | 80000/1200                             | 8 / 30000                             |
| Standard_E64is_v3&nbsp;<sup>3</sup> | 64     | 432         | 864            | 32             | 128000/1024 (1600)                                                   | 80000/1200                             | 8 / 30000                             |


<sup>1</sup> ESV3 serisi VM 'nin özelliği, hiper Iş parçacığı teknolojisinin Intel®.

<sup>2</sup> kısıtlı çekirdek boyutu var.

<sup>3</sup> örnek, tek bir müşteriye adanmış donanımlar için yalıtılmıştır.

## <a name="easv4-series"></a>Easv4 serisi

ACU: 230-260

Premium Depolama: desteklenir

Premium depolama önbelleği: desteklenir

Easv4 serisi Boyutlar, 3.35 GHz 'nin en yüksek frekansını ve Premium SSD 'yi kullanmayı sağlayan 2.35 GHz AMD EPIC<sup>TM</sup> 7452 işlemcisini temel alır. Easv4 serisi Boyutlar, bellek açısından yoğun kurumsal uygulamalar için idealdir.

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | En fazla veri diski | Maksimum önbelleğe alınmış ve geçici depolama aktarım hızı: IOPS-MB/sn (önbellek boyutu GiB biriminde) | Maksimum önbelleğe alınmamış disk aktarım hızı: IOPS-MB/sn | En fazla NIC/beklenen ağ bant genişliği (MBps) |
|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000/32 (50)|3200/48|2 / 1000 |
| Standard_E4as_v4|4|32|64|8|8000/64 (100)|6400/96|2 / 2000 |
| Standard_E8as_v4|8|64|128|16|16000/128 (200)|12800/192|4 / 4000 |
| Standard_E16as_v4|16|128|256|32|32000/255 (400)|25600/384|8 / 8000 |
| Standard_E20as_v4|20|160|320|32|40000/320 (500)|32000/480|8 / 10000 |
| Standard_E32as_v4|32|256|512|32|64000/510 (800)|51200/768|8 / 16000 |
| Standard_E48as_v4 <sup>**</sup> |48|384|768|32|  | | 
| Standard_E64as_v4 <sup>**</sup> |64|512|1024|32| | | 
| Standard_E96as_v4 <sup>**</sup> |6400/96|672|1344|32| | |  

<sup>**</sup>  Bu boyutlar önizlemededir. Bu büyük boyutları denemek istiyorsanız [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview)' de kaydolun.

## <a name="ev3-series"></a>Ev3 serisi 

ACU: 160-190 <sup>1</sup>

Premium Depolama: desteklenmiyor

Premium depolama önbelleği: desteklenmiyor

Ev3-Series örnekleri, Intel® Xeon® 8171M 2,1 GHz (ufuk Gölü) veya Intel® Xeon® E5-2673 v4 2,3 GHz (geniş) işlemcileri özelliğine sahiptir ve Intel Turbo Boost Technology 2,0 ile 3,5 GHz elde edebilir. Ev3 serisi örnekleri, yoğun bellek kullanımlı kurumsal uygulamalar için idealdir.

Veri disk depolaması, sanal makinelerden ayrı olarak faturalandırılır. Premium depolama disklerini kullanmak için ESv3 boyutlarını kullanın. ESv3 boyutları için fiyatlandırma ve faturalandırma oranları Ev3 serisi ile aynıdır. 


| Boyut            | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | En fazla veri diski | Maksimum geçici depolama aktarım hızı: IOPS / Okuma MB/sn / Yazma MB/sn | Maksimum NIC/Ağ bant genişliği |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_E2_v3  | 2         | 16          | 50             | 4              | 3000/46/23                                               | 2 / 1000                 |
| Standard_E4_v3  | 4         | 32          | 100            | 8              | 6000/93/46                                               | 2 / 2000                 |
| Standard_E8_v3  | 8         | 64          | 200            | 16             | 12000/187/93                                             | 4 / 4000                     |
| Standard_E16_v3 | 16        | 128         | 400            | 32             | 24000/375/187                                            | 8 / 8000                     |
| Standard_E20_v3 | 20        | 160         | 500            | 32             | 30000/469/234                                            | 8 / 10000                     |
| Standard_E32_v3 | 32        | 256         | 800            | 32             | 48000/750/375                                            | 8 / 16000                 |
| Standard_E48_v3 | 48        | 384         | 1200            | 32             | 96000/1000/500                                            | 8 / 24000                 |
| Standard_E64_v3 | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000           |
| Standard_E64i_v3&nbsp;<sup>2,&nbsp;3</sup> | 64        | 432         | 1600           | 32             | 96000/1000/500                                           | 8 / 30000           |

<sup>1</sup> EV3 serisi VM 'nin özelliği, hiper Iş parçacığı teknolojisinin Intel®.

<sup>2</sup> kısıtlı çekirdek boyutu var.

<sup>3</sup> örnek, tek bir müşteriye adanmış donanımlar için yalıtılmıştır.

## <a name="eav4-series"></a>Eav4 serisi

ACU: 230-260

Premium Depolama: desteklenmiyor

Premium depolama önbelleği: desteklenmiyor

Eav4 serisi Boyutlar, 3.35 GHz 'nin en yüksek frekansını ve Premium SSD 'yi kullanmayı sağlayan 2.35 GHz AMD EPIC<sup>TM</sup> 7452 işlemcisini temel alır. Eav4 serisi Boyutlar, bellek açısından yoğun kurumsal uygulamalar için idealdir. Veri disk depolaması, sanal makinelerden ayrı olarak faturalandırılır. Premium SSD 'yi kullanmak için Easv4-Series boyutlarını kullanın. Easv4 boyutları için fiyatlandırma ve faturalandırma ölçümleri Eav3 serisi ile aynıdır.

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | En fazla veri diski | Maksimum geçici depolama aktarım hızı: IOPS / Okuma MB/sn / Yazma MB/sn | En fazla NIC/beklenen ağ bant genişliği (MBps) |
| -----|-----|-----|-----|-----|-----|-----|
| Standart\_E2a\_v4|2|16|50|4|3000/46/23|2 / 1000 |
| Standart\_E4a\_v4|4|32|100|8|6000/93/46|2 / 2000 |
| Standart\_E8a\_v4|8|64|200|16|12000/187/93|4 / 4000 |
| Standart\_E16a\_v4|16|128|400|32|24000/375/187|8 / 8000 |
| Standart\_E20a\_v4|20|160|500|32|30000/468/234|8 / 10000 |
| Standart\_E32a\_v4|32|256|800|32|48000/750/375|8 / 16000 |
| Standart\_E48a\_v4 <sup>**</sup> |48|384|1200|32| | |
| Standart\_E64a\_v4 <sup>**</sup> |64|512|1600|32| | |
| Standart\_E96a\_v4 <sup>**</sup> |6400/96|672|2400|32| | |

<sup>**</sup>  Bu boyutlar önizlemededir.  Bu büyük boyutları denemek istiyorsanız [https://aka.ms/AzureAMDLargeVMPreview](https://aka.ms/AzureAMDLargeVMPreview)' de kaydolun.

## <a name="mv2-series"></a>Mv2 serisi

ACU: 188-280<sup>1</sup>

Premium Depolama: desteklenir

Premium depolama önbelleği: desteklenir

Yazma Hızlandırıcısı: [destekleniyor](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

Mv2-Series, bir hiper iş parçacıklı Intel® Xeon® Platinum 81805 2.5 GHz (ufuk Gölü) işlemcisi üzerinde çalışan yüksek performans, 2,5 GHz ve en fazla Turbo frekansı 3,8 GHz ile çalışır. Tüm Mv2 serisi sanal makine boyutları, hem standart hem de Premium kalıcı diskler kullanabilir. Mv2 serisi örnekler, büyük bellek içi veritabanlarını ve iş yüklerini desteklemek için benzersiz işlem performansı sağlayan bellek için iyileştirilmiş VM boyutlarıdır ve ilişkisel veritabanı sunucuları, büyük önbellekler ve bellek içi için ideal olan yüksek bellekle CPU oranıyla desteklenir analiz.

|Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | En fazla veri diski | Maksimum önbelleğe alınmış ve geçici depolama aktarım hızı: IOPS-MB/sn (önbellek boyutu GiB biriminde) | Maksimum önbelleğe alınmamış disk aktarım hızı: IOPS-MB/sn | En fazla NIC/beklenen ağ bant genişliği (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M208ms_v2<sup>2</sup> | 208 | 5700 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 / 16000 |
| Standard_M208s_v2<sup>2</sup> | 208 | 2850 | 4096 | 64 | 80000/800 (7040) | 40000/1000 | 8 / 16000 |
| Standard_M416ms_v2<sup>2, 3</sup> | 416 | 11400 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8 / 32000 |
| Standard_M416s_v2<sup>2, 3</sup> | 416 | 5700 | 8192 | 64 | 250000/1600 (14080) | 80000/2000 | 8 / 32000 |

<sup>1</sup> MV2 serisi VM 'Nin özellik Intel® Hiper Iş parçacığı teknolojisi

<sup>2</sup> Mv2 serisi VM 'ler yalnızca 2. kuşak. Linux kullanıyorsanız, bir görüntüyü bulma ve seçme hakkında yönergeler için bkz. [Azure 'da 2. nesil sanal makineler Için destek](../articles/virtual-machines/linux/generation-2.md) .

<sup>3</sup> M416ms_v2 ve M416s_v2 boyutları için yalnızca şu görüntü için başlangıç desteğinin bulunduğunu unutmayın: "GEN2: SUSE Linux Enterprise Server (SLES) 12 SP4 for SAP Applications."

## <a name="m-series"></a>M serisi 

ACU: 160-180 <sup>1</sup>

Premium Depolama: desteklenir

Premium depolama önbelleği: desteklenir

M serisi boyutlar Intel (R) Xeon (R) CPU E7-8890 v3 @ 2.50 GHz 'yi temel alır   

Yazma Hızlandırıcısı: [destekleniyor](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| Boyut            | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | En fazla veri diski | Maksimum önbelleğe alınmış ve geçici depolama aktarım hızı: IOPS-MB/sn (önbellek boyutu GiB biriminde) | Maksimum önbelleğe alınmamış disk aktarım hızı: IOPS-MB/sn | En fazla NIC/beklenen ağ bant genişliği (Mbps) |
|-----------------|------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------|
| Standard_M8ms&nbsp;<sup>3</sup>    | 8  | 218,75 | 256  | 8  | 10000/100 (793)  | 5000/125 | 4 / 2000 |
| Standard_M16ms&nbsp;<sup>3</sup>   | 16 | 437,5  | 512  | 16 | 20000/200 (1587) | 10000/250 | 8 / 4000 |
| Standard_M32ts | 32 | 192    | 1024 | 32 | 40000/400 (3174) | 20000/500 | 8 / 8000 |
| Standard_M32ls | 32 | 256    | 1024 | 32 | 40000/400 (3174) | 20000/500 | 8 / 8000 |
| Standard_M32ms&nbsp;<sup>3</sup>   | 32 | 875    | 1024 | 32 | 40000/400 (3174) | 20000/500 | 8 / 8000 |
| Standard_M64s  | 64 | 1024   | 2048 | 64 | 80000/800 (6348)| 40000/1000 | 8 / 16000          |
| Standard_M64ls  | 64 | 512    | 2048 | 64 | 80000/800 (6348) | 40000/1000 | 8 / 16000 |
| Standard_M64ms&nbsp;<sup>3</sup>  | 64   | 1792 | 2048 | 64 | 80000/800 (6348)| 40000/1000 | 8 / 16000          |
| Standard_M128s&nbsp;<sup>2</sup> | 128  | 2048        | 4096  | 64 | 160000/1600 (12696) | 80000/2000                            | 8 / 30000          |
| Standard_M128ms&nbsp;<sup>2,&nbsp;3,&nbsp;4</sup> | 128  | 3892  | 4096 | 64 | 160000/1600 (12696) | 80000/2000                            | 8 / 30000          |
| Standard_M64   | 64  | 1024 | 7168  | 64 | 80000/800 (1228) | 40000/1000 | 8 / 16000 |
| Standard_M64m  | 64  | 1792 | 7168  | 64 | 80000/800 (1228) | 40000/1000 | 8 / 16000 |
| Standard_M128&nbsp;<sup>2  | 128 | 2048 | 14336 | 64 | 250000/1600 (2456) | 80000/2000 | 8 / 32000 |
| Standard_M128m&nbsp;<sup>2 | 128 | 3892 | 14336 | 64 | 250000/1600 (2456) | 80000/2000 | 8 / 32000 |



<sup>1</sup> mılyon serisi VM 'nin özelliği, hiper Iş parçacığı teknolojisinin Intel®

<sup>2</sup> 64 'Den fazla vCPU, desteklenen bu konuk işletim sistemlerinden birini gerektirir: Windows Server 2016, Ubuntu 16,04 LTS, SLES 12 SP2 ve Red Hat Enterprise Linux, centos 7,3 ya da LIS 4.2.1 ile Oracle Linux 7,3.

<sup>3</sup> kısıtlı çekirdek boyutu var.

<sup>4</sup> örnek, tek bir müşteriye adanmış donanımlar için yalıtılmıştır.
<br>


## <a name="dsv2-series-11-15"></a>DSv2 serisi 11-15

ACU: 210-250 <sup>1</sup>

Premium Depolama: desteklenir

Premium depolama önbelleği: desteklenir

DSv2 serisi boyutlar Intel® Xeon® 8171M 2,1 GHz (ufuk Gölü) veya Intel® Xeon® E5-2673 v4 2,3 GHz (geniş) veya Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) işlemcileri üzerinde çalışır.

| Boyut | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | En fazla veri diski | Maksimum önbelleğe alınmış ve geçici depolama aktarım hızı: IOPS-MB/sn (önbellek boyutu GiB biriminde) | Maksimum önbelleğe alınmamış disk aktarım hızı: IOPS-MB/sn | En fazla NIC/beklenen ağ bant genişliği (Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS11_v2&nbsp;<sup>3</sup> |2 |14 |28 |8 |8000/64 (72) |6400/96 |2 / 1500 |
| Standard_DS12_v2&nbsp;<sup>3</sup> |4 |28 |56 |16 |16000/128 (144) |12800/192 |4 / 3000 |
| Standard_DS13_v2&nbsp;<sup>3</sup> |8 |56 |112 |32 |32000/256 (288) |25600/384 |8 / 6000 |
| Standard_DS14_v2&nbsp;<sup>3</sup>|16 |112 |224 |64 |64000/512 (576) |51200/768 |8 / 12000 |
| Standard_DS15_v2&nbsp;<sup>2</sup> |20 |140 |280 |64 |80000/640 (720) |64000/960 |8/25000&nbsp;<sup>4</sup>

<sup>1</sup> DSV2 serisi VM ile olası maksimum disk aktarım hızı (IOPS veya MB/sn), eklenen disklerin sayısı, boyutu ve şeritle sınırlı olabilir.  Ayrıntılar için bkz. [yüksek performans Için tasarlama](../articles/virtual-machines/windows/premium-storage-performance.md).  
<sup>2</sup> örnek, tek bir müşteriye adanmış donanımlar için yalıtılmıştır.  
<sup>3</sup> kısıtlı çekirdek boyutu var.  
Hızlandırılmış ağ ile <sup>4</sup> 25000 Mbps. 

<br>

## <a name="dv2-series-11-15"></a>Dv2 serisi 11-15

ACU: 210 - 250

Premium Depolama: desteklenmiyor

Premium depolama önbelleği: desteklenmiyor

DSv2 serisi boyutlar Intel® Xeon® 8171M 2,1 GHz (ufuk Gölü) veya Intel® Xeon® E5-2673 v4 2,3 GHz (geniş) veya Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) işlemcileri üzerinde çalışır.

| Boyut              | Sanal işlemci | Bellek: GiB | Geçici depolama (SSD) GiB | Maksimum geçici depolama aktarım hızı: IOPS / Okuma MB/sn / Yazma MB/sn | Maksimum veri diski/aktarım hızı: IOPS | En fazla NIC/beklenen ağ bant genişliği (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D11_v2   | 2         | 14          | 100            | 6000/93/46                                           | 8/8x500                         | 2 / 1500                     |
| Standard_D12_v2   | 4         | 28          | 200            | 12000/187/93                                         | 16/16x500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000/375/187                                        | 32/32x500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000/750/375                                        | 64/64x500                       | 8 / 12000          |
| Standard_D15_v2&nbsp;<sup>1</sup> | 20        | 140         | 1000          | 60000/937/468                                        | 64/64x500                       | 8/25000&nbsp;<sup>2</sup> |

<sup>1</sup> örnek, tek bir müşteriye adanmış donanımlar için yalıtılmıştır.  
Hızlandırılmış ağ ile <sup>2</sup> 25000 Mbps. 
