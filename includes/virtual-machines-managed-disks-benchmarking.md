---
title: include dosyası
description: include dosyası
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/29/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 25404837d5bc66ff415be8d8670eb6650475c30f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99094671"
---
## <a name="warm-up-the-cache"></a>Önbelleği ısınma

ReadOnly konak önbelleğe alma ile disk, disk sınırından daha yüksek ıOPS verebilir. Ana bilgisayar önbelleğinden bu en büyük okuma performansını almak için öncelikle bu diskin önbelleğini ısınma yapmanız gerekir. Bu, sınama aracının, benchereads birimini önbelleğe almasını sağlar ve aslında diski doğrudan değil, önbelleğe alır. Önbellek İsabetleri, tek önbellek etkin diskten daha fazla ıOPS ile sonuçlanır.

> [!IMPORTANT]
> VM 'nin her yeniden başlatıldığı zaman kıyaslamaları çalıştırmadan önce önbelleği ısınma yapmanız gerekir.

## <a name="diskspd"></a>DISKSPD

[DISKSP ARACıNı](https://github.com/Microsoft/diskspd) sanal makineye indirin. DISKSPD, kendi yapay iş yüklerinizi oluşturmak için özelleştirebileceğiniz bir araçtır. Sınama testleri çalıştırmak için yukarıda açıklanan kurulumu kullanacağız. Farklı iş yüklerini test etmek için belirtimleri değiştirebilirsiniz.

Bu örnekte, aşağıdaki temel Parametreler kümesini kullanırız:

- -c200G: testte kullanılan örnek dosyayı oluşturur (veya yeniden oluşturur). Bayt, KiB, MIB, GiB veya bloklar halinde ayarlanabilir. Bu durumda, bellek önbelleğe alma işlemini en aza indirmek için 200-GiB hedef dosyası büyük bir dosyası kullanılır.
- -W100: yazma istekleri olan işlemlerin yüzdesini belirtir (-W0, %100 ile eşdeğerdir).
- -b4K: blok boyutunu bayt, KiB, MIB veya GiB olarak gösterir. Bu durumda, bir rastgele g/ç testinin benzetimini yapmak için 4K blok boyutu kullanılır.
- -F4: toplam dört iş parçacığı ayarlar.
- -r: rastgele g/ç testini belirtir (-s parametresini geçersiz kılar).
- -o128: her iş parçacığı için hedef başına bekleyen g/ç isteklerinin sayısını gösterir. Bu, sıra derinliği olarak da bilinir. Bu durumda, CPU 'YU vurgulamak için 128 kullanılır.
- -W7200: ölçümler başlamadan önce ısınma zamanının süresini belirtir.
- -D30: ısınma dahil değil testin süresini belirtir.
- -Sh: yazılım ve donanım yazma önbelleğini devre dışı bırak (-suw ile eşdeğerdir).

Parametrelerin tüm listesi için bkz. [GitHub deposu](https://github.com/Microsoft/diskspd/wiki/Command-line-and-parameters).

### <a name="maximum-write-iops"></a>Maksimum yazma ıOPS 'si
Büyük sıra derinliğini 128, küçük bir blok boyutu 8 KB ve yazma işlemlerini yönlendiren dört çalışan iş parçacığı kullanıyoruz. Yazma çalışanları "none" olarak ayarlanmış üç diske sahip olan "Nocachewrite" birimine trafik yönlendirmiştir.

30 saniyelik ısınma ve 30 saniyelik ölçüm için aşağıdaki komutu çalıştırın:

`diskspd -c200G -w100 -b8K -F4 -r -o128 -W30 -d30 -Sh testfile.dat`

Sonuçlar, Standard_D8ds_v4 VM 'nin 12.800 olan en büyük yazma ıOPS sınırını dağıtmakta olduğunu gösterir.

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-write-io-per-second.png" alt-text="3208642560 toplam bayt için toplam/sn ve saniye başına toplam 13052,65 g/ç değeri 101,97 ile 391680 en fazla.":::

### <a name="maximum-read-iops"></a>Maksimum okuma ıOPS 'si

Yüksek sıra derinliğini 128, küçük bir blok boyutu dört KB ve okuma işlemlerini yapmak için dört çalışan iş parçacığı kullanıyoruz. Okuma çalışanları, önbelleği "ReadOnly" olarak ayarlanmış bir diski olan "CacheReads" biriminde trafiği yönlendirmiştir.

İki saatlik ısınma ve 30 saniyelik ölçüm için aşağıdaki komutu çalıştırın:

`diskspd -c200G -b4K -F4 -r -o128 -W7200 -d30 -Sh testfile.dat`

Sonuçlar, Standard_D8ds_v4 VM 'nin 77.000 olan en yüksek okuma ıOPS sınırını dağıtmakta olduğunu gösterir.

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-read-io-per-second.png" alt-text="Toplam 9652785152 bayt için, Toplam 306,72 saat MIB/sn ve saniye başına toplam 78521,23 ı/OS 2356637 toplam/Işletim sistemi vardı.":::

### <a name="maximum-throughput"></a>Aktarım hızı üst sınırı

Okuma ve yazma aktarım hızını en fazla almak için, 64 KB 'lik daha büyük bir blok boyutuna geçiş yapabilirsiniz.
## <a name="fio"></a>FIO

FIO, Linux VM 'lerinde kıyaslama depolaması için popüler bir araçtır. Farklı GÇ boyutları, sıralı veya rastgele okuma ve yazma esnekliği vardır. Belirtilen g/ç işlemlerini gerçekleştirmek için çalışan iş parçacıklarını veya işlemlerini işler. Her çalışan iş parçacığının iş dosyalarını kullanarak gerçekleştirmesi gereken g/ç işlemlerinin türünü belirtebilirsiniz. Aşağıdaki örneklerde gösterildiği her senaryo için bir iş dosyası oluşturduk. Bu iş dosyalarındaki belirtimleri, Premium depolamada çalışan farklı iş yükleriyle Kıyaslanılacak şekilde değiştirebilirsiniz. Örneklerde, **Ubuntu** çalıştıran bir Standard_D8ds_v4 kullanılmaktadır. Kıyaslama bölümünün başlangıcında açıklanan kurulum 'u kullanın ve kıyaslama testlerini çalıştırmadan önce önbelleği sıcak yapın.

Başlamadan önce, [FIO 'u indirin](https://github.com/axboe/fio) ve sanal makinenize yükleyin.

Ubuntu için aşağıdaki komutu çalıştırın,

```
apt-get install fio
```

Disklerde okuma işlemlerini yapmak için yazma işlemlerini ve dört çalışan iş parçacığını yönlendiren dört çalışan iş parçacığı kullanıyoruz. Yazma çalışanları, önbellek "none" olarak ayarlanmış üç diske sahip olan "NoCache" biriminde trafiği yönlendirmiştir. Okuma çalışanları, "ReadOnly" olarak ayarlanmış bir disk olan "readcache" biriminde trafiği yönlendirmiştir.

### <a name="maximum-write-iops"></a>Maksimum yazma ıOPS 'si

En yüksek yazma ıOPS 'sini almak için aşağıdaki belirtimlerle iş dosyasını oluşturun. "fiowrite.ini" olarak adlandırın.

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[writer1]
rw=randwrite
directory=/mnt/nocache
```

Önceki bölümlerde ele alınan tasarım yönergeleriyle birlikte aşağıdaki önemli noktalara dikkat edin. Bu belirtimler, maksimum ıOPS 'yi sürücü için gereklidir,  

* 256 yüksek sıra derinliği.  
* 4 KB 'lık küçük bir blok boyutu.  
* Rastgele yazma işlemleri gerçekleştiren birden çok iş parçacığı.

FIO testini 30 saniye boyunca çalıştırmak için aşağıdaki komutu çalıştırın.  

```
sudo fio --runtime 30 fiowrite.ini
```

Test çalışırken, VM ve Premium disklerin teslim aldığı yazma ıOPS sayısını görebilirsiniz. Aşağıdaki örnekte gösterildiği gibi, Standard_D8ds_v4 VM, 12.800 ıOPS 'nin en büyük yazma ıOPS sınırını teslim ediyor.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-uncached-writes-1.jpg" alt-text="Yazma ıOPS VM 'si ve Premium SSD 'ler teslim edilirken, yazmaların 13.1 k ıOPS olduğunu gösterir.":::

### <a name="maximum-read-iops"></a>Maksimum okuma ıOPS 'si

Maksimum okuma ıOPS 'sini almak için aşağıdaki belirtimlerle iş dosyasını oluşturun. "fioread.ini" olarak adlandırın.

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache
```

Önceki bölümlerde ele alınan tasarım yönergeleriyle birlikte aşağıdaki önemli noktalara dikkat edin. Bu belirtimler, maksimum ıOPS 'yi sürücü için gereklidir,

* 256 yüksek sıra derinliği.  
* 4 KB 'lık küçük bir blok boyutu.  
* Rastgele yazma işlemleri gerçekleştiren birden çok iş parçacığı.

FIO testini 30 saniye boyunca çalıştırmak için aşağıdaki komutu çalıştırın.

```
sudo fio --runtime 30 fioread.ini
```

Test çalışırken, VM ve Premium disklerin teslim aldığı okuma ıOPS sayısını görebilirsiniz. Aşağıdaki örnekte gösterildiği gibi, Standard_D8ds_v4 VM 77.000 'den fazla okuma ıOPS 'si teslim ediyor. Bu, diskin ve önbellek performansının bir birleşimidir.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-cached-reads-1.jpg" alt-text="Yazma ıOPS sanal makinesinin ve Premium SSDs 'nin gösterdiği ekran görüntüsü, okumaların 78.6 k olduğunu gösterir.":::

### <a name="maximum-read-and-write-iops"></a>Maksimum okuma ve yazma ıOPS 'si

En yüksek Birleşik okuma ve yazma ıOPS 'yi almak için iş dosyasını aşağıdaki belirtimlerle oluşturun. "fioreadwrite.ini" olarak adlandırın.

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=3200
```

Önceki bölümlerde ele alınan tasarım yönergeleriyle birlikte aşağıdaki önemli noktalara dikkat edin. Bu belirtimler, maksimum ıOPS 'yi sürücü için gereklidir,

* 128 yüksek sıra derinliği.  
* 4 KB 'lık küçük bir blok boyutu.  
* Rastgele okuma ve yazma işlemleri gerçekleştiren birden çok iş parçacığı.

FIO testini 30 saniye boyunca çalıştırmak için aşağıdaki komutu çalıştırın.

```
sudo fio --runtime 30 fioreadwrite.ini
```

Test çalışırken, sanal makine ve Premium disklerin teslim edilen birleştirilmiş okuma ve yazma ıOPS sayısını görebilirsiniz. Aşağıdaki örnekte gösterildiği gibi, Standard_D8ds_v4 VM 90.000 ' den fazla Birleşik okuma ve yazma ıOPS 'yi teslim ediyor. Bu, diskin ve önbellek performansının bir birleşimidir.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-both-1.jpg" alt-text="Birleşik okuma ve yazma ıOPS, okumaların 78.3 k olduğunu ve yazmaları 12,6 k ıOPS olduğunu gösterir.":::

### <a name="maximum-combined-throughput"></a>En yüksek Birleşik verimlilik

En yüksek Birleşik okuma ve yazma verimini almak için, okuma ve yazma işlemleri yapan birden fazla iş parçacığı ile daha büyük bir blok boyutu ve büyük sıra derinliği kullanın. 64 KB 'lik bir blok boyutu ve 128 sıra derinliğini kullanabilirsiniz.