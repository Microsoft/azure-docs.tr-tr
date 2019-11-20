---
title: Azure Disk Depolama yönetilen disklerde uygulamanızı sınama
description: Uygulamanızı Azure 'da değerlendirme süreci hakkında bilgi edinin.
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: conceptual
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: 600b456cd77d866f1365b7dadfa9ea2473db0fa4
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698769"
---
# <a name="benchmarking-a-disk"></a>Diski sınama

Sınama, uygulamanızdaki farklı iş yüklerini taklit etme ve her iş yükü için uygulama performansını ölçme işlemidir. [Yüksek performans için tasarlama makalesinde](premium-storage-performance.md)açıklanan adımları kullanarak, uygulama performansı gereksinimlerini toplamıştır. Uygulamayı barındıran VM 'lerde benchişaretleme araçları 'nı çalıştırarak, uygulamanızın Premium depolamayla elde edilebileceği performans düzeylerini belirleyebilirsiniz. Bu makalede, Azure Premium Depolama disklerinde sağlanan standart bir DS14 VM 'yi sınama örnekleri sunuyoruz.

Windows ve Linux için sırasıyla ortak bir benchişaretleme araçları Iometer ve FIO 'u kullandık. Bu araçlar, iş yükü gibi bir üretimi taklit eden birden çok iş parçacığı oluşturup sistem performansını ölçer. Araçları kullanarak, genellikle bir uygulama için değiştiremediğiniz blok boyutu ve sıra derinliği gibi parametreleri de yapılandırabilirsiniz. Bu sayede, farklı uygulama iş yükleri türleri için Premium disklerle sağlanan yüksek ölçekli bir VM 'de maksimum performansı elde etmenizi sağlayan daha fazla esneklik sağlanır. Her bir sınama aracı hakkında daha fazla bilgi edinmek için [Iometer](http://www.iometer.org/) ve [Fio](http://freecode.com/projects/fio)'ı ziyaret edin.

Aşağıdaki örnekleri izlemek için standart bir DS14 VM oluşturun ve VM 'ye 11 Premium Depolama diski ekleyin. 11 disk, ana bilgisayar önbelleğe alma ile 10 disk yapılandırın ve bunları Nocacheyazmaları adlı bir birime ayırır. Ana bilgisayar önbelleğe almayı kalan diskte "ReadOnly" olarak yapılandırın ve bu diskle CacheReads adlı bir birim oluşturun. Bu kurulumu kullanarak, standart bir DS14 VM 'den en fazla okuma ve yazma performansını görebilirsiniz. Premium SSD 'Ler içeren bir DS14 VM oluşturma hakkında ayrıntılı adımlar için, [yüksek performans Için tasarlama](premium-storage-performance.md)bölümüne gidin.

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Sonraki adımlar

Yüksek performanslı bir makale için tasarımımızda ilerleyin. Bu durumda, prototip için mevcut uygulamanıza benzer bir denetim listesi oluşturacaksınız. Değerlendirme araçlarını kullanarak, iş yüklerinin benzetimini yapabilir ve prototip uygulamasındaki performansı ölçebilir. Bunu yaparak, hangi disk teklifini, uygulama performansı gereksinimlerinizin eşleşeceğini veya geçebileceği tespit edebilirsiniz. Daha sonra üretim uygulamanız için aynı yönergeleri uygulayabilirsiniz.

> [!div class="nextstepaction"]
> [Yüksek performanslı başlangıç için tasarlama](premium-storage-performance.md) hakkındaki makaleye bakın.