---
title: Disk ölçümleri
description: Disk toplama ölçümleri örnekleri
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 60486c41ad843cf193ee0648dfcfef66f7668e47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101674981"
---
# <a name="disk-performance-metrics"></a>Disk performans ölçümleri
Azure, sanal makinelerinizin (VM) ve disklerinin nasıl gerçekleştirdiği hakkında öngörü sağlayan Azure portal ölçümler sunar. Ölçümler Ayrıca bir API çağrısıyla alınabilir. Bu makale 3 alt bölümlere ayrılmıştır:

- **DISK GÇ, aktarım hızı ve sıra derinliği ölçümleri** -bu ölçümler, disk ve sanal makine açısından depolama performansını görmenizi sağlar.
- **Disk** patlaması ölçümleri-bu ölçümler, Premium [disklerimiz üzerinde Observability özelliği sunar](disk-bursting.md) .
- **Storage IO kullanım ölçümleri** -bu ölçümler, diskler ile depolama performansındaki darboğazları tanılamaya yardımcı olur. 

Tüm ölçümler her dakikada, her 5 dakikada bir yayınlanan kredi yüzdesi ölçümü hariç dağıtılır.

## <a name="disk-io-throughput-and-queue-depth-metrics"></a>Disk GÇ, işleme ve sıra derinliği ölçümleri
VM ve disk GÇ, verimlilik ve sıra derinliği performansı hakkında bilgi almak için aşağıdaki ölçümler mevcuttur:

- **Işletim sistemi diski sıra derinliği**: işletim sistemi diskine okunmayı bekleyen veya bir işletim sistemi diskine yazılan mevcut bekleyen GÇ isteklerinin sayısı.
- **Işletim sistemi diski okuma bayt/sn**: işletim sistemi diskinden saniye cinsinden okunan bayt sayısı.
- **Işletim sistemi diski okuma işlemi/sn**: işletim sistemi diskinden saniye cinsinden okunan giriş işlemlerinin sayısı.
- **Işletim sistemi diski yazma bayt/sn**: işletim sistemi diskinden saniye olarak yazılan bayt sayısı.
- **Işletim sistemi diski yazma işlemi/sn**: işletim sistemi diskinden saniye olarak yazılan çıkış işlemlerinin sayısı.
- **Veri diski sıra derinliği**: okuma veya veri diskine yazma için bekleyen mevcut bekleyen GÇ isteklerinin sayısı.
- **Veri diski okuma bayt/sn**: veri disklerinin saniye cinsinden okunan bayt sayısı.
- **Veri diski okuma işlemi/sn**: saniye başına veri diskinden okunan giriş işlemlerinin sayısı.
- **Veri diski yazma bayt/sn**: veri disklerden saniye olarak yazılan bayt sayısı.
- **Veri diski yazma işlemi/sn**: bir saniyede veri diskinden yazılan çıkış işlemlerinin sayısı.
- **Disk okuma bayt/sn**: bir VM 'ye bağlı tüm disklerden saniye cinsinden okunan toplam bayt sayısı.
- **Disk okuma işlemi/sn**: bir VM 'ye bağlı tüm disklerden saniye cinsinden okunan giriş işlemlerinin sayısı.
- **Disk yazma bayt/sn**: bir VM 'ye bağlı tüm disklerden saniye cinsinden yazılan bayt sayısı.
- **Disk yazma işlemi/sn**: bir VM 'ye bağlı tüm disklerden saniye içinde yazılan çıkış işlemlerinin sayısı.

## <a name="bursting-metrics"></a>Yatırım ölçümleri
Aşağıdaki ölçümler, Premium [disklerimiz üzerinde Observability özelliği ile](disk-bursting.md) ilgili olarak size yardımcı olur:

- **Veri diski en fazla patlama bant genişliği**: veri diskleri (ler) tarafından veri bloğu alabileceği aktarım hızı sınırı.
- **Işletim sistemi diski en fazla patlama bant genişliği**: işletim sistemi diskinin veri bloğu alabileceği aktarım hızı sınırı.
- **Veri diski en fazla patlama IOPS**: veri diskleri (ler) tarafından veri bloğu için ızın verilen IOPS sınırı.
- **Işletim sistemi diski en fazla patlama IOPS**: işletim sistemi diskinin veri bloğu IÇIN kullandığı IOPS sınırı.
- **Veri diski hedef bant genişliği**: veri diskinin, patlama olmadan elde edilebileceği aktarım hızı sınırı.
- **Işletim sistemi diski hedef bant genişliği**: işletim sistemi diskinin, patlama olmadan elde edilebileceği aktarım hızı sınırı.
- **Veri diski hedef IOPS**: veri disklerin, ani olarak elde edilebilecek IOPS sınırı.
- **Işletim sistemi diski hedef IOPS**: veri diskleri için bir patlama olmadan elde edilebilecek IOPS sınırı.
- **Veri diski kullanılan patlama bps kredileri yüzdesi**: veri diskleri için kullanılan üretilen iş bloğu sayısının birikmiş yüzdesi. 5 dakikalık bir aralığa göre yayılır.
- **Işletim sistemi diski kullanılan patlama bps kredileri yüzdesi**: işletim sistemi diski için kullanılan üretilen iş bloğu toplamının birikmiş yüzdesi. 5 dakikalık bir aralığa göre yayılır.
- **Veri diski kullanılan patlama GÇ kredileri yüzdesi**: veri diskleri IÇIN kullanılan IOPS veri bloğu birikmiş yüzdesi. 5 dakikalık bir aralığa göre yayılır.
- **Işletim sistemi diski kullanılan patlama GÇ kredileri yüzdesi**: işletim sistemi diski IÇIN kullanılan IOPS veri bloğu birikmiş yüzdesi. 5 dakikalık bir aralığa göre yayılır.

## <a name="storage-io-utilization-metrics"></a>Depolama GÇ kullanım ölçümleri
Aşağıdaki ölçümler, sanal makinenizde ve disk birleşiminizdeki performans sorunlarını tanılamaya yardımcı olur. Bu ölçümler yalnızca Premium etkin VM kullanılırken kullanılabilir. Bu ölçümler, Ultra hariç tüm disk türleri için kullanılabilir. 

Disk GÇ dönüşü tanılamaya yardımcı olan ölçümler:

- **Veri DISKI IOPS tüketilen yüzde**: VERI diski IOPS 'si tarafından hesaplanan yüzde, sağlanan VERI diski IOPS üzerinden tamamlandı. Bu miktar %100 ise, çalışan uygulamanız veri diskinizin ıOPS sınırından itibaren GÇ 'dir.
- **Kullanılan veri diski bant genişliği yüzdesi**: sağlanan veri diski verimlilik üzerinden tamamlanan veri diski işleme tarafından hesaplanan yüzde. Bu miktar %100 ise, çalışan uygulamanız, veri diskinizin bant genişliği sınırından GÇ 'dir.
- **Işletim sistemi DISK IOPS tüketilen yüzde**: işletim sistemi diski IOPS 'si tarafından hesaplanan yüzde, sağlanan işletim SISTEMI disk IOPS üzerinden tamamlandı. Bu miktar %100 ise, çalışan uygulamanız, işletim sistemi diskinizin ıOPS sınırından itibaren GÇ 'dir.
- **Işletim sistemi diski kullanılan bant genişliği yüzdesi**: işletim sistemi disk aktarım hızı tarafından hesaplanan yüzde, sağlanan işletim sistemi disk aktarım hızı üzerinden tamamlandı. Bu miktar %100 ise, çalışan uygulamanız, işletim sistemi diskinizin bant genişliği sınırından GÇ olarak çalışır.

VM GÇ dönüşü tanılamaya yardımcı olan ölçümler:

- **VM önbelleğe ALıNAN IOPS yüzde yüzdesi**: Toplam IOPS tarafından hesaplanan yüzde, önbelleğe alınmış maksimum sanal makine IOPS sınırı üzerinden tamamlandı. Bu miktar %100 ise, çalışan uygulamanız VM 'nin önbelleğe alınmış ıOPS sınırından itibaren GÇ 'dir.
- **VM önbelleğe alınan bant genişliği yüzdesi**: toplam disk işleme tarafından hesaplanan yüzde, önbelleğe alınmış maksimum sanal makine üretilen iş üzerinden tamamlanır. Bu miktar %100 ise, çalışan uygulamanız VM 'nin önbelleğe alınmış bant genişliği sınırından itibaren GÇ 'dir.
- **VM önbelleğe ALıNMAMıŞ IOPS yüzde yüzdesi**: bir sanal MAKINEDEKI toplam IOPS tarafından hesaplanan yüzde, önbelleğe alınmamış maksimum sanal makine IOPS sınırı üzerinden tamamlandı. Bu miktar %100 ise, çalışan uygulamanız VM 'nin önbelleğe alınmamış ıOPS sınırından itibaren GÇ 'dir.
- **VM önbelleğe alınmamış bant genişliği yüzdesi**: bir sanal makinedeki toplam disk işleme tarafından hesaplanan yüzde, sağlanan en yüksek sanal makine aktarım hızı üzerinden tamamlanır. Bu miktar %100 ise, çalışan uygulamanız VM 'nin önbelleğe alınmamış bant genişliği sınırından itibaren GÇ 'dir.

## <a name="storage-io-metrics-example"></a>Storage ıO ölçümleri örneği

Bu yeni depolama GÇ kullanım ölçümlerini kullanarak hata ayıklamamıza yardımcı olması için bu yeni depolama ıO kullanım ölçümlerinin nasıl kullanılacağına ilişkin bir örnek üzerinden çalıştırılalım. Sistem Kurulumu Önceki örnekle aynıdır, ancak bu süre, ekli işletim sistemi *diski önbelleğe alınmaz* .

**Yapısı**

- Standard_D8s_v3
  - Önbelleğe alınan ıOPS: 16.000
  - Önbelleğe alınmamış ıOPS: 12.800
- P30 işletim sistemi diski
  - IOPS: 5.000
  - Konak önbelleğe alma: **devre dışı**
- İki P30 veri diski × 2
  - IOPS: 5.000
  - Konak önbelleğe alma: **okuma/yazma**
- İki P30 veri diski × 2
  - IOPS: 5.000
  - Konak önbelleğe alma: **devre dışı**

Bu sanal makine ve GÇ etkinliği oluşturan disk birleşimi üzerinde bir sınama testi çalıştıralım. Azure 'da depolama GÇ 'yi kıyaslama hakkında bilgi edinmek için bkz. [Azure disk depolama uygulamanızı kıyaslama](disks-benchmarks.md). Sınama aracı ' ndan, VM ve disk birleşiminin 22.800 ıOPS ile elde olduğunu görebilirsiniz:

![R = 22.8 k vurgulanmasını gösteren f ı o çıkışının ekran görüntüsü.](media/disks-metrics/utilization-metrics-example/fio-output.jpg)



Standard_D8s_v3 toplam 28.600 ıOPS elde edebilir. Ölçümleri kullanarak, neler olduğunu araştıralım ve depolama GÇ darboğazımuzu nasıl tanımlayacağım. Sol bölmede **ölçümler**' i seçin:

![Sol bölmede vurgulanan ölçümleri gösteren ekran görüntüsü.](media/disks-metrics/utilization-metrics-example/metrics-menu.jpg)

İlk olarak, **VM 'Nin önbelleğe alınmış IOPS yüzde** ölçüsünü göz atalım:

![V M önbelleğe alınan g/ç 'yi gösteren ekran görüntüsü yüzdesi.](media/disks-metrics/utilization-metrics-example/vm-cached.jpg)

Bu ölçüm, sanal makine üzerindeki önbelleğe alınmış ıOPS 'ye ayrılan 16.000 ıOPS 'nin %61 ' u olduğunu söyler. Bu yüzde, depolama GÇ sorununa %100 ' de olmadığı için önbelleğe alınan diskler olmadığı anlamına gelir. Şimdi **VM 'Nin önbelleğe ALıNMAMıŞ IOPS yüzdesi** ölçümünü inceleyelim:

![V M önbelleğe alınmamış g/ç 'Leri tüketilen ekran görüntüsü.](media/disks-metrics/utilization-metrics-example/vm-uncached.jpg)

Bu ölçüm %100 ' dir. Bu, VM 'deki önbelleğe alınmamış ıOPS 'ye ayrılan tüm 12.800 ıOPS 'nin kullanıldığını söyler. Bu sorunu düzelttiğimiz yollardan biri, VM 'imizin boyutunu ek GÇ 'yi işleyebileceğiniz daha büyük bir boyuta değiştirmek olabilir. Ancak bunu yapmadan önce, kaç ıOPS gördüğünü öğrenmek için eklenen diske göz atalım. İşletim **sistemi DISK IOPS tüketilen yüzdeyi** inceleyerek Işletim sistemi diskini kontrol edin:

![O 'un tükettiği disk g/ç yüzdesini gösteren ekran görüntüsü.](media/disks-metrics/utilization-metrics-example/os-disk.jpg)

Bu ölçüm, bu P30 işletim sistemi diski için sağlanan 5.000 ıOPS 'nin yaklaşık %90 ' üne sahip olduğunu bize söyler. Bu yüzde, işletim sistemi diskinde performans sorunu olmadığı anlamına gelir. Şimdi, VM 'ye bağlı veri disklerini, **tüketilen veri DISKI IOPS yüzdesine** göz atalım.

![Veri diski g/ç yüzdesini gösteren ekran görüntüsü.](media/disks-metrics/utilization-metrics-example/data-disks-no-splitting.jpg)

Bu ölçüm, eklenen tüm diskler genelinde yüzde 42 ' ün altında olan ortalama ıOPS 'nin yüzdesini söyler. Bu yüzde, diskler tarafından kullanılan ıOPS 'ye göre hesaplanır ve konak önbelleğinden sunulmuyor. Bu ölçülere *bölme* ve LUN değerine göre bölme uygulayarak bu ölçüme daha ayrıntılı bir bakalım:

![Veri diski g/ç 'Leri bölme ile tüketilen ekran görüntüsü.](media/disks-metrics/utilization-metrics-example/data-disks-splitting.jpg)

Bu ölçüm, 1. LUN 'a eklenen veri disklerini ve 2 ' nin sağlanan ıOPS 'nin %85 ' de kullandığını söyler. Bu, GÇ 'nin VM ve diskler mimarisinden nasıl görüneceğine ilişkin bir diyagram aşağıda verilmiştir:

![Depolama g/ç ölçümleri örneği diyagramı.](media/disks-metrics/utilization-metrics-example/metrics-diagram.jpg)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Izleyici ölçümlerine genel bakış](../azure-monitor/essentials/data-platform-metrics.md)
- [Ölçüm toplama açıklanıyor](../azure-monitor/essentials/metrics-aggregation-explained.md)
- [Azure İzleyici'yi kullanarak ölçüm uyarıları oluşturma, görüntüleme ve yönetme](../azure-monitor/alerts/alerts-metric.md)