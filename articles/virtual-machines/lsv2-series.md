---
title: Lsv2-Series-Azure sanal makineleri
description: Lsv2 serisi VM 'Ler için Özellikler.
author: sasha-melamed
ms.service: virtual-machines
ms.subservice: vm-sizes-storage
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 787f9ada921e1580a34431568e87c41496b37f6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563209"
---
# <a name="lsv2-series"></a>Lsv2 serisi

Lsv2-Series, yüksek performans, düşük gecikme süresi ve [AMD EPIC<sup>TM</sup> 7551 işlemcisi](https://www.amd.com/en/products/epyc-7000-series) üzerinde çalışan yerel NVMe depolama alanını, 2.55 GHz 'nin tüm çekirdek artışı ve en fazla 3,0 GHz sürümü ile birlikte sunar. Lsv2 serisi VM 'Ler, eşzamanlı bir çoklu iş parçacığı yapılandırmasında 8 ile 80 vCPU boyutunda olacak şekilde gelir.  Her vCPU için 8 GiB bellek ve 8 vCPU başına, L80s v2 'de bulunan 19.2 TB 'a kadar (10 x 1.92 TB) bir 1.92 TB NVMe SSD M. 2 cihaz vardır.

> [!NOTE]
> Lsv2 serisi VM 'Ler, dayanıklı veri disklerini kullanmak yerine doğrudan VM 'ye eklenen düğümdeki yerel diski kullanacak şekilde iyileştirilmiştir. Bu, iş yükleriniz için daha fazla IOPS/aktarım hızı sağlar. Lsv2 ve LS Serisi, dayanıklı veri disklerine yönelik IOPS ulaşılabilir arttırmak için yerel bir önbellek oluşturulmasını desteklemez.
>
> Yerel diskin yüksek aktarım hızı ve IOPS, Lsv2 serisi VM 'Leri Apache Cassandra ve MongoDB gibi NoSQL depoları için ideal hale getirir. Bu, tek bir VM 'nin arızası durumunda Kalıcılık sağlamak için verileri birden çok VM arasında çoğaltır.
>
> Daha fazla bilgi için bkz. [Windows](../virtual-machines/windows/storage-performance.md) veya [Linux](../virtual-machines/linux/storage-performance.md)için Lsv2 serisi sanal makinelerde performansı iyileştirme.  

[Acu](acu.md): 150-175<br>
[Premium Depolama](premium-storage-performance.md): desteklenir<br>
[Premium depolama önbelleği](premium-storage-performance.md): desteklenmiyor<br>
[Dinamik geçiş](maintenance-and-updates.md): desteklenmiyor<br>
[Güncelleştirmeleri koruyan bellek](maintenance-and-updates.md): desteklenmiyor<br>
[VM oluşturma desteği](generation-2.md): 1. ve 2. nesil<br>
Burdımı: destekleniyor<br>
[Hızlandırılmış ağ](../virtual-network/create-vm-accelerated-networking-cli.md): desteklenir<br>
[Kısa ömürlü işletim sistemi diskleri](ephemeral-os-disks.md): desteklenmez <br>
<br>

| Boyut | Sanal işlemci | Bellek (GiB) | Geçici disk<sup>1</sup> (gib) | NVMe diskleri<sup>2</sup> | NVMe disk işleme<sup>3</sup> (Okuma IOPS/Mbps) | Önbelleğe alınmamış veri diski verimlilik (IOPS/MBps)<sup>4</sup> | Önbelleğe alınmamış veri diski performansı üst sınırı (IOPS/MBps)<sup>5</sup>| En fazla veri diski | En fazla NIC | Beklenen ağ bant genişliği (MB/sn) |
|---|---|---|---|---|---|---|---|---|---|---|
| Standard_L8s_v2   |  8 |  64 |  80 |  1x 1.92 TB  | 400000/2000  | 8000/160   | 8000/1280 | 16 | 2 | 3200   |
| Standard_L16s_v2  | 16 | 128 | 160 |  2x 1.92 TB  | 800000/4000  | 16000/320  | 16000/1280 | 32 | 4 | 6400   |
| Standard_L32s_v2  | 32 | 256 | 320 |  4X 1.92 TB  | 1,5 milyon/8000    | 32000/640  | 32000/1280 | 32 | 8 | 12800  |
| Standard_L48s_v2  | 48 | 384 | 480 |  6x 1.92 TB  | 2.2 d/14000   | 48000/960  | 48000/2000 | 32 | 8 | 16000 + |
| Standard_L64s_v2  | 64 | 512 | 640 |  8X 1.92 TB  | 2.9 a/16000   | 64000/1280 | 64000/2000 | 32 | 8 | 16000 + |
| Standard_L80s_v2<sup>6</sup> | 80 | 640 | 800 | 10 x 1.92 TB | 3.8 d/20000 | 80000/1400 | 80000/2000 | 32 | 8 | 16000 + |

<sup>1</sup> Lsv2 serisi VM 'ler, işletim sistemi disk belleği/takas dosyası kullanımı için standart bir SCSI tabanlı geçici kaynak diskine sahiptir (D: Windows 'Ta, Linux üzerinde/dev/sdb). Bu disk, her 8 vCPU için 80 GiB depolama, 4.000 ıOPS ve 80 MBps aktarım hızı sağlar (örneğin, Standard_L80s_v2 40.000 ıOPS ve 800 MBPS ' te 800 GiB sağlar). Bu, NVMe sürücülerinin uygulama kullanımına tamamen ayrılmasını sağlar. Bu disk geçici bir işlemdir ve tüm veriler durdurma/serbest bırakma durumunda kaybedilir.

<sup>2</sup> yerel NVMe diski kısa ömürlü, VM 'nizi durdurur/serbest bırakırsanız bu disklerde veri kaybolacaktır.

<sup>3</sup> Hyper-V NVMe Direct teknolojisi, Konuk VM alanı ile güvenli bir şekilde eşlenmiş yerel NVMe sürücülerine kısıtlı erişim sağlar.  En yüksek performansı elde etmek için Azure Marketi 'nden en son WS2019 Build veya Ubuntu 18,04 ya da 16,04 kullanılması gerekir.  Yazma performansı, GÇ boyutu, sürücü yükü ve kapasite kullanımına göre farklılık gösterir.

<sup>4</sup> Lsv2 serisi VM 'Ler, Lsv2 iş yüklerini avantajına sahip olmadığından veri diski için konak önbelleği sunmaz.

<sup>5</sup> Lsv2 serisi VM 'ler, disk performansını aynı anda en fazla 30 dakika boyunca [aşırı](./disk-bursting.md) alabilir. 

64 ' den fazla vCPU içeren <sup>6</sup> VM, desteklenen bu konuk işletim sistemlerinden birini gerektirir:

- Windows Server 2016 veya üzeri
- Ubuntu 16,04 LTS veya üzeri, Azure ayarlanmış çekirdekle (4,15 çekirdek veya üzeri)
- SLES 12 SP2 veya üzeri
- RHEL veya CentOS Sürüm 6,7 ile 6,10, Microsoft tarafından sunulan LIS paketi 4.3.1 (veya üzeri) yüklü
- RHEL veya CentOS Sürüm 7,3, Microsoft tarafından sunulan LIS paketi 4.2.1 (veya üzeri) yüklü
- RHEL veya CentOS Sürüm 7,6 veya üzeri
- UEK4 veya üzeri ile Oracle Linux
- Arka bağlantı noktaları çekirdeği, 10. ve üzeri ile 9.
- 4,14 çekirdekine sahip CoreOS veya üzeri

## <a name="size-table-definitions"></a>Boyut tablosu tanımları

- Depolama kapasitesi GiB veya 1024^3 bayt cinsinden gösterilmiştir. GB (1000^3 bayt) ile ölçülen diskleri GiB (1024^3 bayt) ile ölçülen disklerle karşılaştırırken GiB cinsinden verilen kapasite rakamlarının daha küçük görünebileceğini unutmayın. Örneğin: 1023 GiB = 1098,4 GB
- Disk aktarım hızı, saniye başına giriş/çıkış işlemi sayısı (IOPS) ve MB/sn (MB/sn = 10^6 bayt/sn) üzerinden ölçülür.
- Sanal makinelerinize en iyi performansı elde etmek istiyorsanız, vCPU başına veri disklerinin sayısını 2 disk ile sınırlamanız gerekir.
- **Beklenen ağ bant** genişliği tüm ormanlarda tüm NIC 'lerde [sanal makine türü başına ayrılan en yüksek bant genişliğine sahip](../virtual-network/virtual-machine-network-throughput.md) olur. Üst sınırlar garanti edilmez, ancak hedeflenen uygulama için doğru VM türünün seçilmesine ilişkin kılavuzluk sağlamak için tasarlanmıştır. Gerçek ağ performansı, ağ tıkanıklığı, uygulama yükleri ve ağ ayarları gibi çeşitli faktörlere bağlıdır. Ağ aktarım hızını iyileştirme hakkında bilgi için bkz. [Windows ve Linux için ağ aktarım hızını iyileştirme](../virtual-network/virtual-network-optimize-network-bandwidth.md). Linux veya Windows üzerinde beklenen ağ performansını gerçekleştirmek için belirli bir sürümü seçmeniz veya VM’nizi en iyi duruma getirmeniz gerekebilir. Daha fazla bilgi için bkz. [Sanal makine aktarım hızını güvenilir bir şekilde test etme](../virtual-network/virtual-network-bandwidth-testing.md).


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