---
title: include dosyası
description: include dosyası
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/11/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: e5148ff9e92a2e550a3117356a4e77cbac8fc6f4
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673555"
---
*Önbelleği hazırlanıyor*  
Salt okunur konak önbelleği disk disk sınırdan daha yüksek IOPS sağlayabilir. Öncelikle bu en yüksek okuma performansı ana bilgisayar önbelleğe almak için bu disk önbelleği sıcak gerekir. Bu aracın Kıyaslama CacheReads birimde artıracak okuma IOs gerçekten ulaştığını önbellek ve diskin değil doğrudan sağlar. Önbellek isabet sayısı sonucu tek önbellekten ek IOPS disk etkin.

> [!IMPORTANT]
> VM her yeniden değerlendirmesi, çalıştırmadan önce önbelleği sıcak gerekir.

## <a name="tools"></a>Araçlar

### <a name="iometer"></a>Iometer

[Iometer Aracı'nı indirme](https://sourceforge.net/projects/iometer/files/iometer-stable/2006-07-27/iometer-2006.07.27.win32.i386-setup.exe/download) VM üzerinde.

#### <a name="test-file"></a>Test dosyası

Iometer Kıyaslama test çalıştırması biriminde depolanan bir test dosyası kullanır. Okuma sürücü ve disk ölçmek için bu test dosyası üzerinde IOPS ve aktarım hızı yazar. Bir sağlamadınız Iometer bu test dosyası oluşturur. İobw.tst CacheReads ve NoCacheWrites birimlere adlı bir 200 GB test dosyası oluşturun.

#### <a name="access-specifications"></a>Erişim belirtimleri

Belirtimleri isteği % okuma/yazma g/ç boyutu, % rastgele/sıralı yapılandırılmış Iometer içinde "Access belirtimleri" sekmesini kullanarak. Aşağıda açıklanan senaryoların her biri için bir erişim belirtimini oluşturun. Erişim belirtimleri oluşturun ve "Kaydet" uygun bir ad gibi – RandomWrites\_8K, RandomReads\_8 K. Test senaryosu çalıştırırken karşılık gelen belirtimi seçin.

Maksimum IOPS, yazma senaryosu için erişim belirtimleri örneği aşağıda gösterilmiştir,  
    ![Maksimum yazma IOPS için erişim belirtimleri örneği](../articles/virtual-machines/linux/media/premium-storage-performance/image8.png)

#### <a name="maximum-iops-test-specifications"></a>Maksimum IOPS sınama belirtimleri

Maksimum IOPS göstermek için daha küçük istek boyutu kullanın. 8 K istek boyutu ve rastgele yazma ve okuma özellikleri oluşturun.

| Erişim belirtimi | İstek boyutu | Rastgele % | Okuma % |
| --- | --- | --- | --- |
| RandomWrites\_8 K |8K |100 |0 |
| RandomReads\_8 K |8K |100 |100 |

#### <a name="maximum-throughput-test-specifications"></a>En fazla aktarım hızı testi belirtimleri

En yüksek aktarım göstermek için istek boyutu daha büyük kullanın. 64 K istek boyutu ve rastgele yazma ve okuma özellikleri oluşturun.

| Erişim belirtimi | İstek boyutu | Rastgele % | Okuma % |
| --- | --- | --- | --- |
| RandomWrites\_64 K |64 K |100 |0 |
| RandomReads\_64 K |64 K |100 |100 |

#### <a name="run-the-iometer-test"></a>Iometer testi çalıştırın

Önbelleği sıcak için aşağıdaki adımları gerçekleştirin

1. Aşağıda gösterilen değerlerle iki erişim belirtimleri oluşturun

   | Ad | İstek boyutu | Rastgele % | Okuma % |
   | --- | --- | --- | --- |
   | RandomWrites\_1 MB |1 MB |100 |0 |
   | RandomReads\_1 MB |1 MB |100 |100 |
1. Aşağıdaki parametrelerle önbellek diski başlatma için Iometer testi çalıştırın. Hedef birim ve 128 sıra derinliği için üç çalışan iş parçacıkları kullanın. Test "çalışma zamanı" süresi 2 olarak ayarlayın "Test" Kurulum"sekmesinde saat.

   | Senaryo | Hedef birim | Ad | Duration |
   | --- | --- | --- | --- |
   | Önbellek diski başlatın |CacheReads |RandomWrites\_1 MB |2 saat |
1. Önbellek diski aşağıdaki parametrelerle hazırlanıyor için Iometer testi çalıştırın. Hedef birim ve 128 sıra derinliği için üç çalışan iş parçacıkları kullanın. Test "çalışma zamanı" süresi 2 olarak ayarlayın "Test" Kurulum"sekmesinde saat.

   | Senaryo | Hedef birim | Ad | Duration |
   | --- | --- | --- | --- |
   | Önbellek diski ısınıyor |CacheReads |RandomReads\_1 MB |2 saat |

Önbellek diski warmed sonra aşağıda listelenen test senaryoları ile devam edin. Iometer testi çalıştırmak için en az üç çalışan iş parçacıkları için kullanın. **her** hedef birim. Her iş parçacığı için hedef birimi seçin, sıra derinliğini ayarlayın ve karşılık gelen test senaryosu çalıştırmak için aşağıdaki tabloda gösterildiği gibi kaydedilmiş test belirtimleri birini seçin. Tabloda ayrıca beklenen sonuçları IOPS ve aktarım hızı için bu testleri çalıştırırken gösterir. Tüm senaryolar için 8 KB'lık bir küçük g/ç boyutu ve 128 yüksek sıra derinliğini kullanılır.

| Test senaryosu | Hedef birim | Ad | Sonuç |
| --- | --- | --- | --- |
| En çok, Okuma IOPS |CacheReads |RandomWrites\_8 K |50.000 IOPS |
| En çok, Yazma IOPS |NoCacheWrites |RandomReads\_8 K |64.000 IOPS |
| En çok, Toplam IOPS |CacheReads |RandomWrites\_8 K |100.000 IOPS |
| NoCacheWrites |RandomReads\_8 K | &nbsp; | &nbsp; |
| En çok, Okuma MB/sn |CacheReads |RandomWrites\_64 K |524 MB/sn |
| En çok, Yazma MB/sn |NoCacheWrites |RandomReads\_64 K |524 MB/sn |
| Birleşik MB/sn |CacheReads |RandomWrites\_64 K |1000 MB/sn |
| NoCacheWrites |RandomReads\_64 K | &nbsp; | &nbsp; |

Iometer ekran görüntüleri için toplam IOPS ve aktarım hızı senaryoları test sonuçları aşağıda verilmiştir.

#### <a name="combined-reads-and-writes-maximum-iops"></a>Birleştirilmiş okuyan ve yazan maksimum IOPS

![Birleşik okuma ve yazma işlemleri maksimum IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image9.png)

#### <a name="combined-reads-and-writes-maximum-throughput"></a>Birleştirilmiş okuyan ve yazan en fazla aktarım hızı

![Birleşik okuma ve yazma işlemleri, en fazla aktarım hızı](../articles/virtual-machines/linux/media/premium-storage-performance/image10.png)

### <a name="fio"></a>FIO

FIO Linux vm'lerinde Kıyaslama depolama için popüler bir araçtır. Bu farklı GÇ boyutları, sıralı seçmek için esneklik veya rastgele okuma ve yazma. Çalışan iş parçacıkları veya belirtilen g/ç işlemleri gerçekleştirmek için işlemleri olarak çoğaltılır. Her iş parçacığı proje dosyalarını kullanarak gerçekleştirmelidir g/ç işlemleri türünü belirtebilirsiniz. Aşağıdaki örneklerde gösterildiği senaryo başına bir proje dosyası oluşturduk. Premium depolama alanında çalışan farklı iş yüklerini karşılaştırılmasıdır bu proje dosyalarındaki belirtimleri değiştirebilirsiniz. Örneklerde standart DS 14 sanal makine çalıştırma kullanıyoruz **Ubuntu**. Aynı Kurulumu Benchmarking bölümün başında açıklanan ve orta Gecikmeli Kıyaslama testleri çalıştırmadan önce önbelleği kullanın.

Başlamadan önce [FIO indirme](https://github.com/axboe/fio) ve sanal makinenize yükleyin.

Ubuntu için aşağıdaki komutu çalıştırın

```
apt-get install fio
```

Yazma işlemleri kullanımını dört çalışan iş parçacıkları ve dört çalışan iş parçacıkları disklerde sürüş okuma işlemleri için kullanıyoruz. Yazma çalışanları "Yok" olarak ayarlanmış önbellek ile 10 disklere sahip "nocache" birimdeki trafiği yönlendirdiği. Okuma çalışanları sahip bir diski önbellek kümesi "Salt okunur" "readcache" birim trafiği yönlendirdiği.

#### <a name="maximum-write-iops"></a>Maksimum yazma IOPS

Maksimum yazma IOPS almak için aşağıdaki özelliklerle iş dosyasını oluşturun. "Fiowrite.ini" olarak adlandırın.

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[writer1]
rw=randwrite
directory=/mnt/nocache
[writer2]
rw=randwrite
directory=/mnt/nocache
[writer3]
rw=randwrite
directory=/mnt/nocache
[writer4]
rw=randwrite
directory=/mnt/nocache
```

Önceki bölümlerde ele alınan tasarım ilkelerine uygun olarak izleme önemli şey unutmayın. Bu belirtimler maksimum IOPS desteklemek üzere gerekli olan,  

* 256 yüksek sıra derinliği.  
* 8 KB'lık bir küçük blok boyutu.  
* Birden çok iş parçacığı rastgele yazma işlemlerini gerçekleştirme.

30 saniye için FIO test hız kazandırın için aşağıdaki komutu çalıştırın,  

```
sudo fio --runtime 30 fiowrite.ini
```

Test çalışırken, sayısını, VM IOPS yazma ve Premium diskleri teslim görebilirsiniz. Aşağıdaki örnekte gösterildiği gibi maksimum, yazma IOPS sınırı 50.000 IOPS DS14 VM sağlıyor.  
    ![Yazma IOPS VM ve Premium diskleri teslim sayısı](../articles/virtual-machines/linux/media/premium-storage-performance/image11.png)

#### <a name="maximum-read-iops"></a>Maksimum IOPS okuyun

Proje dosyası, maksimum okuma IOPS almak için aşağıdaki özelliklerle oluşturun. "Fioread.ini" olarak adlandırın.

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=8k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache
```

Önceki bölümlerde ele alınan tasarım ilkelerine uygun olarak izleme önemli şey unutmayın. Bu belirtimler maksimum IOPS desteklemek üzere gerekli olan,

* 256 yüksek sıra derinliği.  
* 8 KB'lık bir küçük blok boyutu.  
* Birden çok iş parçacığı rastgele yazma işlemlerini gerçekleştirme.

30 saniye için FIO test hız kazandırın için aşağıdaki komutu çalıştırın,

```
sudo fio --runtime 30 fioread.ini
```

Test çalışırken, VM okuma IOPS sayısı ve Premium diskleri teslim görebilirsiniz. Aşağıdaki örnekte gösterildiği gibi birden fazla 64.000 okuma IOPS DS14 VM sağlıyor. Disk ve önbellek performansı budur.  
    ![](../articles/virtual-machines/linux/media/premium-storage-performance/image12.png)

#### <a name="maximum-read-and-write-iops"></a>En fazla okuma ve yazma IOPS

Şunlarla birlikte proje dosyasını oluşturmak en fazla almak için özellikleri birleştirilmiş okuma ve yazma IOPS. "Fioreadwrite.ini" olarak adlandırın.

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k

[reader1]
rw=randread
directory=/mnt/readcache
[reader2]
rw=randread
directory=/mnt/readcache
[reader3]
rw=randread
directory=/mnt/readcache
[reader4]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer2]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer3]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
[writer4]
rw=randwrite
directory=/mnt/nocache
rate_iops=12500
```

Önceki bölümlerde ele alınan tasarım ilkelerine uygun olarak izleme önemli şey unutmayın. Bu belirtimler maksimum IOPS desteklemek üzere gerekli olan,

* Yüksek sıra derinliğini 128.  
* 4 KB'lık bir küçük blok boyutu.  
* Birden çok iş parçacığı rastgele gerçekleştirme okur ve yazar.

30 saniye için FIO test hız kazandırın için aşağıdaki komutu çalıştırın,

```
sudo fio --runtime 30 fioreadwrite.ini
```

Test çalışırken, birleşik okuma sayısı görebilir ve IOPS yazmak VM ve Premium diskler sunma. Aşağıdaki örnekte gösterildiği gibi DS14 VM 100. 000'den fazla birleştirilmiş okuma ve yazma IOPS sağlıyor. Disk ve önbellek performansı budur.  
    ![Birleşik okuma ve yazma IOPS](../articles/virtual-machines/linux/media/premium-storage-performance/image13.png)

#### <a name="maximum-combined-throughput"></a>Maksimum toplam aktarım hızı

Okuma ve yazma aktarım hızı üst sınırı almak için birleştirilmiş, daha büyük blok boyutu ve büyük sıra derinliğini okuma ve yazma işlemleri gerçekleştiren birden çok iş parçacığı ile kullanın. Bir blok boyutu 64 KB'ın ve 128 sıra derinliğini kullanabilirsiniz.
