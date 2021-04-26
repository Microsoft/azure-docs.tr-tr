---
title: include dosyası
description: include dosyası
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 82b4c127f983f3133326bf7fb538e40713ef9655
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100580399"
---
![D s v 3 belirtimlerini gösteren grafik.](media/vm-disk-performance/dsv3-documentation.jpg)

- *Önbelleğe* alınmamış maksimum disk aktarım hızı, sanal makinenin işleyebileceği varsayılan depolama üst sınırıdır.
- Ana bilgisayar önbelleğe almayı etkinleştirdiğinizde, en fazla *önbelleğe alınmış* depolama aktarım hızı sınırı ayrı bir sınır olur.

Ana bilgisayar önbelleğe alma, yazılabilir veya hızlı bir şekilde okunabilen sanal makineye daha yakın bir depolama alanı ile çalışmaya devam edebilir. Konak önbelleğe alma için VM tarafından kullanılabilen depolama miktarı belgelerde bulunur. Örneğin, Standard_D8s_v3, önbellek depolamanın 200 GiB ile geldiğini görebilirsiniz.

Sanal makinenizi oluştururken ve disk iliştirmeye çalıştığınızda ana bilgisayar önbelleğe almayı etkinleştirebilirsiniz. Ayrıca, mevcut bir VM 'de disklerinizde konak önbelleğini açabilir ve kapatabilirsiniz.

![Ana bilgisayar önbelleğe almayı gösteren ekran görüntüsü.](media/vm-disk-performance/host-caching.jpg)

Ana bilgisayar önbelleğini her bir disk için iş yükü gereksinimlerinize uyacak şekilde ayarlayabilirsiniz. Ana bilgisayar önbelleizin şunları olacak şekilde ayarlayabilirsiniz:

- **Salt okunurdur**: yalnızca okuma işlemleri yapan iş yükleri için
- **Okuma/yazma**: okuma ve yazma işlemlerinin bir dengesini sağlayan iş yükleri için

İş yükünüz Bu desenlerden birini izmezse konak önbelleğe alma kullanmanız önerilmez.

Veri akışını ve performansını nasıl etkilediğini görmek için farklı konak önbelleği ayarlarına birkaç örnek üzerinden çalıştıralım. Bu ilk örnekte, ana bilgisayar önbelleğe alma ayarı **salt okunurdur** olarak ayarlandığında GÇ istekleriyle ne olacağını inceleyeceğiz.

**Yapısı**

- Standard_D8s_v3
  - Önbelleğe alınan ıOPS: 16.000
  - Önbelleğe alınmamış ıOPS: 12.800
- P30 veri diski
  - IOPS: 5.000
  - Konak önbelleğe alma: **salt okunurdur**

Okuma işlemi gerçekleştirildiğinde ve istenen veriler önbellekte kullanılabilir olduğunda, önbellek istenen verileri döndürür. Diskten okunmaya gerek yoktur. Bu okuma işlemi, sanal makinenin önbelleğe alınmış sınırlarına doğru sayılır.

![Okuma ana bilgisayarı önbelleğe alma okuma isabeti gösteren diyagram.](media/vm-disk-performance/host-caching-read-hit.jpg)

Okuma işlemi gerçekleştirildiğinde ve istenen veriler *önbellekte yoksa,* okuma isteği diske gönderilir. Ardından disk, hem önbelleğe hem de VM 'ye yüzey. Bu okuma işlemi, sanal makinenin önbelleğe alınmamış sınırına ve VM 'nin önbelleğe alınmış sınırına doğru olarak sayılır.

![Bir okuma ana bilgisayarı önbelleğe alma Read isabetsizlik gösteren diyagram.](media/vm-disk-performance/host-caching-read-miss.jpg)

Bir yazma işlemi gerçekleştirildiğinde, yazma işlemi tamamlanmadan önce önbelleğin ve diskin üzerine yazılması gerekir. Bu yazma işlemi, sanal makinenin önbelleğe alınmamış sınırına ve VM 'nin önbelleğe alınmış sınırına doğru sayılır.

![Bir okuma ana bilgisayar önbelleği yazma işlemini gösteren diyagram.](media/vm-disk-performance/host-caching-write.jpg)

Daha sonra, ana bilgisayar önbelleği ayarı **okuma/yazma** olarak ayarlandığında GÇ istekleriyle neler olduğunu inceleyelim.

**Yapısı**

- Standard_D8s_v3
  - Önbelleğe alınan ıOPS: 16.000
  - Önbelleğe alınmamış ıOPS: 12.800
- P30 veri diski
  - IOPS: 5.000
  - Konak önbelleğe alma: **okuma/yazma**

Okuma, salt okunurdur aynı şekilde işlenir. Yazma/yazma önbelleklemesi ile farklı olan tek şey yazma işlemleri. Konak önbelleğe alma ile yazma, **okuma/yazma** olarak ayarlandığında, yalnızca yazma işleminin tamamlanmış olarak kabul edilmesi için ana bilgisayar önbelleğine yazılması gerekir. Yazma daha sonra geç arka plan işlemi olarak diske yazılır. Bu, önbellekte yazıldığında önbelleğe alınmış GÇ 'ye doğru bir yazma sayılır. Diske geç yazıldığında, önbelleğe alınmamış GÇ 'e doğru sayılır.

![Okuma/yazma ana bilgisayar önbelleği yazma işlemini gösteren diyagram.](media/vm-disk-performance/host-caching-read-write.jpg)

Standard_D8s_v3 sanal makinemizi kullanmaya devam edelim. Bu süre dışında, disklerde ana bilgisayar önbelleğe almayı etkinleştireceğiz. Ayrıca, artık VM 'nin ıOPS sınırı 16.000 ıOPS 'dir. VM 'ye bağlı üç temel P30 disk, her biri 5.000 ıOPS işleyebilir.

**Yapısı**

- Standard_D8s_v3
  - Önbelleğe alınan ıOPS: 16.000
  - Önbelleğe alınmamış ıOPS: 12.800
- P30 işletim sistemi diski
  - IOPS: 5.000
  - Konak önbelleğe alma: **okuma/yazma**
- İki P30 veri diski × 2
  - IOPS: 5.000
  - Konak önbelleğe alma: **okuma/yazma**

![Ana bilgisayar önbelleği örneği gösteren diyagram.](media/vm-disk-performance/host-caching-example-without-remote.jpg)

Uygulama, önbelleğe alma etkin bir Standard_D8s_v3 sanal makine kullanır. 15.000 ıOPS için bir istek yapar. İstekler, eklenen her diske 5.000 ıOPS olarak bölünür. Performans dönüşü gerçekleşmez.

## <a name="combined-uncached-and-cached-limits"></a>Önbelleğe alınmamış ve önbelleğe alınmış limitlerin birleştirilmesi

Bir sanal makinenin önbelleğe alınmış sınırları, önbelleğe alınmamış limitlerden ayrıdır. Bu, diğer disklerde konak önbelleğe almayı etkinleştirmekle, bir VM 'ye bağlı disklerde konak önbelleğe almayı etkinleştirebileceğiniz anlamına gelir. Bu yapılandırma, sanal makinelerinizin önbelleğe alınan sınırın toplam depolama ıO ve önbelleğe alınmamış sınırın elde etmesine olanak tanır.

Bu limitlerin birlikte nasıl çalıştığını anlamanıza yardımcı olması için bir örnek üzerinden çalışmamıza izin verin. Standard_D8s_v3 sanal makine ve Premium disklere iliştirilmiş yapılandırma ile devam edeceğiz.

**Yapısı**

- Standard_D8s_v3
  - Önbelleğe alınan ıOPS: 16.000
  - Önbelleğe alınmamış ıOPS: 12.800
- P30 işletim sistemi diski
  - IOPS: 5.000
  - Konak önbelleğe alma: **okuma/yazma**
- İki P30 veri diski × 2
  - IOPS: 5.000
  - Konak önbelleğe alma: **okuma/yazma**
- İki P30 veri diski × 2
  - IOPS: 5.000
  - Konak önbelleğe alma: **devre dışı**

![Uzak depolama ile ana bilgisayar önbelleği örneği gösteren diyagram.](media/vm-disk-performance/host-caching-example-with-remote.jpg)

Bu durumda, Standard_D8s_v3 bir sanal makinede çalışan uygulama 25.000 ıOPS için bir istek yapar. İstek, eklenen disklerin her birine 5.000 ıOPS olarak bölünür. Üç disk konak önbelleği kullanır ve iki disk konak önbelleğe alma kullanmaz.

- Konak önbelleğe alma kullanan üç disk, 16.000 olan önbelleğe alınmış limitlerin içinde olduğundan, bu istekler başarıyla tamamlanır. Depolama performansı dönüşü gerçekleşmez.
- Konak önbelleğe alma kullanmayan iki disk, 12.800 sınırının önbelleğe alınmamış sınırları dahilinde olduğundan, bu istekler de başarıyla tamamlanır. Hiçbir kafes gerçekleşmez.

