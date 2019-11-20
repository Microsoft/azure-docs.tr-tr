---
title: include dosyası
description: include dosyası
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/24/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c2ce39c40ad535b4a66349fcbb4529333218555a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512671"
---
Premium SSD 'Ler < = 512 GiB (P20 veya below) herhangi bir disk boyutunda patlama desteği. Bu disk boyutları, en iyi çaba temelinde patlaması destekler ve burdıya yönetimi için bir kredi sistemi kullanır. Krediler disk boyutu için sağlanan performans hedefinin altında olduğunda, bir patlama demetini birikir ve trafik hedeften fazla olduğunda krediler tüketir. Disk trafiği, sağlanan hedefteki ıOPS ve bant genişliğine göre izlenir.

Disk patlaması, bunu destekleyen disk boyutlarının yeni dağıtımları üzerinde varsayılan olarak etkindir. Disk kullanımını destekliyorsa, mevcut disk boyutları aşağıdaki yöntemlerden birini kullanarak ani bir şekilde etkinleştirebilir:

- Diski kullanımdan çıkarın ve yeniden bağlayın.
- VM 'yi durdurup başlatın.

## <a name="burst-states"></a>Veri bloğu durumları

Disk sanal makineye eklendiğinde, tüm veri bloğu uygulanabilir disk boyutları tam bir patlama kredisi demeti ile başlar. En uzun patlama süresi, patlama kredisi demeti boyutuna göre belirlenir. Kullanılmayan kredilerin yalnızca kredi demeti boyutuna kadar birikmesini sağlayabilirsiniz. Herhangi bir zamanda, disk patlama kredi demeti aşağıdaki üç durumdan birinde olabilir: 

- Disk trafiği sağlanan performans hedefinden daha az kullanıldığında tahakkuk etme. Disk trafiği ıOPS veya bant genişliği hedefi veya her ikisi de olursa kredisi birikitebilmeniz gerekir. Tam disk bant genişliği kullanırken bunun tersi de GÇ kredileri birikmesini sağlayabilirsiniz.  

- Disk trafiği sağlanan performans hedefinden daha fazla kullanıldığında reddediliyor. Veri bloğu trafiği ıOPS 'den veya bant genişliğinden bağımsız olarak kredileri kullanacaktır. 

- Disk trafiği tam olarak sağlanan performans hedefinde olduğunda kalan sabit. 

Veri bloğu belirtimleriyle birlikte burte desteği sağlayan disk boyutları aşağıdaki tabloda özetlenmiştir.

## <a name="regional-availability"></a>Bölgesel kullanılabilirlik

Şu anda, disk patlaması yalnızca Orta Batı ABD bölgede kullanılabilir.

## <a name="disk-sizes"></a>Disk boyutları

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="example-scenarios"></a>Örnek senaryolar

Bunun nasıl çalıştığını daha iyi bir fikir vermek için birkaç örnek senaryo aşağıda verilmiştir:

- Disk bulmasının avantajlarından faydalanabilecek bir yaygın senaryo, işletim sistemi disklerinde daha hızlı VM önyüklemesi ve uygulama başlatma aşamadır. Örnek olarak 8 GiB OS görüntüsüne sahip bir Linux VM alın. İşletim sistemi diski olarak bir P2 diski kullandığımızda, sağlanan hedef 120 ıOPS ve 25 MB/sn 'dir. VM başlatıldığında önyükleme dosyalarını yükleyen işletim sistemi diskine yönelik bir okuma ani artış olur. Burdıya 'nın tanıtılmasıyla birlikte, 3500 ıOPS ve 170 MBps maksimum patlama hızına giderek, yükleme süresini en az 6x ile hızlandırmaya devam edebilirsiniz. VM önyüklemesi sonrasında, uygulama tarafından çoğu veri işlemi bağlı veri disklerine karşı işlem yaptığından, işletim sistemi diskindeki trafik düzeyi genellikle düşüktür. Trafik sağlanan hedefin altındaysa kredileri biriktirilecektir.

- Uzak bir sanal masaüstü ortamı barındırıyorsanız, etkin bir Kullanıcı AutoCAD gibi bir uygulamayı başlattığında, işletim sistemi diskine giden trafiği önemli ölçüde artırır. Bu durumda, patlama trafiği birikmiş krediler tüketir ve sağlanan hedefin ötesine gidip uygulamayı çok daha hızlı bir şekilde başlatmayı sağlar.

- P1 diskinde, sağlanan 120 ıOPS ve 25 MBps bir hedef vardır. Diskteki gerçek trafik 100 ıOPS ise ve son 1 ikinci aralıkta 20 MBps ise, kullanılmayan 20 IOs ve 5 MB, diskin patlama demetine alacaklandırılır. Patlama demetini içindeki krediler, daha sonra trafik sağlanan hedefi aştığında, en fazla patlama sınırına kadar kullanılabilir. En fazla patlama sınırı, tüketmek üzere veri bloğu kredisi olsa bile disk trafiğinin tavan sayısını tanımlar. Bu durumda, kredi demetine 10.000 IOs sahip olsanız bile, bir P1 diski saniyede en fazla 3.500 GÇ olan maksimum veri bloğu sayısından daha fazla veremez.  
