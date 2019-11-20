---
title: include dosyası
description: include dosyası
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/14/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a355307eef9f5ce1f833cfd7924f5efa234a0cd7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73524023"
---
## <a name="premium-ssd"></a>Premium SSD

Azure Premium SSD 'ler, giriş/çıkış (GÇ) yoğun iş yükleri ile sanal makineler (VM) için yüksek performanslı ve düşük gecikmeli disk desteği sunar. Premium Depolama disklerinin hız ve performansından yararlanmak için, mevcut VM disklerini Premium SSD 'Ler 'e geçirebilirsiniz. Premium SSD 'Ler, görev açısından kritik üretim uygulamaları için uygundur. Premium SSD 'Ler, yalnızca Premium Depolama ile uyumlu olan VM serileri ile kullanılabilir.

Windows için Azure 'daki tek tek VM türleri ve boyutları hakkında daha fazla bilgi edinmek için, Premium Depolama ile uyumlu olan boyutlar dahil, bkz. [WINDOWS VM boyutları](../articles/virtual-machines/windows/sizes.md). Linux için Azure 'daki tek tek VM türleri ve boyutları hakkında daha fazla bilgi edinmek için, bkz. [LINUX VM boyutları](../articles/virtual-machines/linux/sizes.md).

### <a name="disk-size"></a>Disk boyutu
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Standart depolamanın aksine bir Premium Depolama diski sağladığınızda, bu diskin kapasitesi, ıOPS ve aktarım hızı garanti edilir. Örneğin, bir P50 diski oluşturursanız, Azure bu disk için 4.095 GB depolama kapasitesi, 7.500 ıOPS ve 250 MB/sn aktarım hızı sağlar. Uygulamanız, kapasite ve performansın tümünü veya bir bölümünü kullanabilir. Premium SSD diskler, önceki tabloda% 99,9 ' de açıklanan düşük tek basamaklı milisaniyelik gecikme süreleri ve hedef ıOPS ve aktarım hızı sağlamak üzere tasarlanmıştır.

## <a name="bursting-preview"></a>Burdıya (Önizleme)

P30 ' den küçük Premium SSD boyutları artık disk patlaması (Önizleme) sunar ve disk başına ıOPS 'yi 3.500 ve bant genişliğine kadar en fazla 170 Mbps olarak kullanabilir. Burdıya otomatik ve bir kredi sistemine göre çalışır. Krediler, disk trafiği sağlanan performans hedefinin altındaysa otomatik olarak bir patlama demetini içinde biriktirilir ve trafik hedefin ötesinde en fazla patlama sınırına kadar, krediler otomatik olarak tüketilir. En fazla patlama sınırı, ' den tüketmek üzere patlama kredileriniz olsa bile, disk ıOPS & bant genişliği üst sınırını tanımlar. Disk patlaması, GÇ desenlerinin öngörülemeyen değişikliklerinde daha iyi tolerans sağlar. Çıkmanız gerekirse trafiği olan işletim sistemi diski önyüklemesi ve uygulamaları için en iyi şekilde yararlanabilirsiniz.    

Bir kullanıcı eylemi gerekmeden, varsayılan olarak, [Önizleme bölgelerinde](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability) geçerli disk boyutlarının yeni dağıtımları üzerinde disk yok desteği etkinleştirilecek. Geçerli boyutlardaki mevcut diskler için, iki seçenekten birini kullanarak gereksiz bir şekilde etkinleştirebilirsiniz: diski ayırma ve yeniden bağlama ya da bağlı VM 'yi durdurup yeniden başlatma. Disk, en yüksek veri bloğu sınırı olan 30 dakikalık bir sanal makineye eklendiğinde, tüm veri bloğu uygulanabilir disk boyutları tam bir patlama kredisi demeti ile başlar. Azure disklerinde ne kadar iş çalıştığı hakkında daha fazla bilgi edinmek için bkz. [Premium SSD burdımı](../articles/virtual-machines/linux/disk-bursting.md). 

### <a name="transactions"></a>İşlemler

Premium SSD 'Ler için, 256 kıb aktarım hızına eşit veya daha küçük olan her g/ç işlemi tek bir g/ç işlemi olarak kabul edilir. 256 Kigb 'den büyük g/ç işlemleri, 256 KiB boyutundaki birden fazla g/ç işlemi olarak kabul edilir.

## <a name="standard-ssd"></a>Standart SSD

Azure Standart SSD 'ler, düşük ıOPS düzeylerinde tutarlı performans gerektiren iş yükleri için optimize edilmiş uygun maliyetli bir depolama seçeneğidir. Standart SSD, özellikle de şirket içinde HDD çözümlerinizde çalışan iş yüklerinin farkıyla ilgili sorunlarla karşılaşırsanız, buluta taşımak isteyen kişiler için iyi bir giriş düzeyi deneyimi sunmaktadır. Standart HDD 'ler ile karşılaştırıldığında, standart SSD 'Ler daha iyi kullanılabilirlik, tutarlılık, güvenilirlik ve gecikme süresi sağlar. Standart SSD 'Ler, Web sunucuları, düşük ıOPS uygulama sunucuları, hafif kullanılan kurumsal uygulamalar ve geliştirme/test iş yükleri için uygundur. Standart HDD 'ler gibi tüm Azure sanal makinelerinde standart SSD 'ler de mevcuttur.

### <a name="disk-size"></a>Disk boyutu
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

Standart SSD 'ler, tek basamaklı milisaniyelik gecikme süreleri ve ıOPS ve aktarım hızı, önceki tabloda %99 ' de tanımlanan sınırlara kadar bir süre sağlamak üzere tasarlanmıştır. Gerçek ıOPS ve aktarım hızı bazen trafik desenlerine bağlı olarak farklılık gösterebilir. Standart SSD 'Ler, daha düşük gecikme süresine sahip HDD disklerinden daha tutarlı performans sağlayacaktır.

### <a name="transactions"></a>İşlemler

Standart SSD 'Ler için, 256 kıb aktarım hızına eşit veya daha küçük olan her g/ç işlemi tek bir g/ç işlemi olarak kabul edilir. 256 Kigb 'den büyük g/ç işlemleri, 256 KiB boyutundaki birden fazla g/ç işlemi olarak kabul edilir. Bu işlemlerin bir faturalandırma etkisi vardır.

## <a name="standard-hdd"></a>Standart HDD

Azure Standart HDD 'Ler, gecikme süresine duyarlı iş yükleri çalıştıran VM 'Ler için güvenilir, düşük maliyetli disk desteği sunar. Standart depolama ile, veriler sabit disk sürücülerinde (HDD 'Ler) depolanır. Standart HDD disklerin gecikmesi, ıOPS ve verimlilik, SSD tabanlı disklere kıyasla daha geniş farklılık gösterebilir. Standart HDD diskler, 10ms altında yazma gecikmeleri ve g/ç işlemleri için 20 MS 'nin altında okuma gecikmeleri sunacak şekilde tasarlanmıştır, ancak gerçek performans, GÇ boyutu ve iş yükü düzenine göre farklılık gösterebilir. VM 'lerle çalışırken geliştirme/test senaryoları ve daha az kritik iş yükleri için standart HDD disklerini kullanabilirsiniz. Standart HDD 'ler tüm Azure bölgelerinde kullanılabilir ve tüm Azure VM 'Leri ile kullanılabilir.

### <a name="disk-size"></a>Disk boyutu
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>İşlemler

Standart HDD 'Ler için, her GÇ işlemi, g/ç boyutundan bağımsız olarak tek bir işlem olarak değerlendirilir. Bu işlemlerin bir faturalandırma etkisi vardır.

## <a name="billing"></a>Faturalandırma

Yönetilen diskler kullanılırken, aşağıdaki faturalandırma konuları geçerlidir:

- Disk türü
- yönetilen disk boyutu
- Anlık Görüntüler
- Giden veri aktarımları
- İşlem sayısı

**Yönetilen disk boyutu**: yönetilen diskler sağlanan boyutta faturalandırılır. Azure sağlanan boyutu (yuvarlanır) en yakın sunulan disk boyutuna eşler. Sunulan disk boyutlarının ayrıntıları için bkz. önceki tablolar. Her disk desteklenen bir sağlanan disk boyutu teklifiyle eşlenir ve buna göre faturalandırılır. Örneğin, bir 200 GiB Standart SSD sağladıysanız, E15 (256 GiB) disk boyutu teklifiyle eşlenir. Sağlanan tüm diskler için faturalandırma, Premium Depolama teklifi için aylık fiyat kullanılarak günlere eşit olarak dağıtılır. Örneğin, bir E10 diski sağladıysanız ve 20 saat sonra sildiyseniz, E10 teklifi için 20 saate göre faturalandırılırsınız. Bu, diske yazılan gerçek veri miktarına bakılmaksızın olur.

**Anlık görüntüler**: anlık görüntüler kullanılan boyuta göre faturalandırılır. Örneğin, sağlanan 64 GiB kapasitesine sahip bir yönetilen diskin anlık görüntüsünü ve 10 GiB 'nin gerçek kullanılan veri boyutunu oluşturursanız, anlık görüntü yalnızca 10 GiB 'nin kullanılan veri boyutu için faturalandırılır.
