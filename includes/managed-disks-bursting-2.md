---
title: include dosyası
description: include dosyası
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 03/04/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 4162fe12ff54f16cd5f982f6a576905227c9a107
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107821054"
---
## <a name="disk-level-bursting"></a>Disk düzeyinde burdıya

### <a name="on-demand-bursting-preview"></a>İsteğe bağlı (Önizleme)

İsteğe bağlı disk miktarını kullanan diskler, en fazla patlama hedefine kadar iş yüklerine göre gereken ilk sağlanmış hedefleri aşacak şekilde veri bloğu oluşturabilir. Örneğin, 1-TiB P30 diskinde sağlanan ıOPS, 5000 ıOPS olur. Bu diskte disk patlaması etkinleştirildiğinde, iş yükleriniz, 30.000 ıOPS ve 1.000 MBps olan en fazla patlama performansına kadar bu diske IOs verebilir.

İş yüklerinizin sağlanan performans hedefinin ötesinde sıklıkla çalışmasını bekleliyorsanız, disk patlaması uygun maliyetli olmayacaktır. Bu durumda, daha iyi temel performans için diskinizin performans katmanını [daha yüksek bir katmana](../articles/virtual-machines/disks-performance-tiers.md) değiştirmenizi öneririz. Faturalandırma ayrıntılarınızı gözden geçirin ve iş yüklerinizin trafik düzenine karşı değerlendirin.

İsteğe bağlı okeyi etkinleştirmeden önce aşağıdakileri anlayın:

[!INCLUDE [managed-disk-bursting-regions-limitations](managed-disk-bursting-regions-limitations.md)]

#### <a name="regional-availability"></a>Bölgesel kullanılabilirlik

[!INCLUDE [managed-disk-bursting-availability](managed-disk-bursting-availability.md)]

#### <a name="billing"></a>Faturalandırma

İsteğe bağlı bir patlama modelini kullanan diskler, saatlik bir patlama için sabit ücret ve işlem maliyetleri, sağlanan hedefin ötesinde tüm veri bloğu işlemleri için geçerlidir. İşlem maliyetleri, sağlanan hedefleri aşan okuma ve yazma işlemleri de dahil olmak üzere, önbelleğe alınmamış disk IOs temel alınarak Kullandıkça Öde modeli kullanılarak ücretlendirilir. Aşağıda, bir faturalama saati üzerinde disk trafiği desenlerinin bir örneği verilmiştir:

Disk yapılandırması: Premium SSD – 1 TiB (P30), disk patlaması etkin.

- 00:00:00 – 00:10:00 disk ıOPS, 5.000 ıOPS 'nin sağlanmış hedefi 
- 00:10:01 – 00:10:10 uygulama, disk ıOPS 'nin 10 saniye boyunca 6.000 ıOPS 'de patlaması için bir Batch işi verdi 
- 00:10:11 – 00:59:00 disk ıOPS, 5.000 ıOPS 'nin sağlanmış hedefi 
- 00:59:01 – 01:00:00 uygulaması başka bir Batch işi verdi. bu nedenle, disk ıOPS 'nin 60 saniye boyunca 7.000 

Bu Faturalandırma saati ' nde, burdıma maliyeti iki ücretden oluşur:

İlk ücret, $X (bölgenize göre belirlenir) sabit kullanım masrafına yönelik bir patlama. Bu düz ücret her zaman disk üzerinde ücretlendirilir. 

İkincisi, veri bloğu işlem maliyetidir. İki zaman yuvalarında disk patlaması gerçekleşti. 00:10:01 – 00:10:10 ' den, birikmiş patlama işlemi (6.000 – 5.000) X 10 = 10.000. 00:59:01 – 01:00:00 ' den, birikmiş patlama işlemi (7.000 – 5.000) X 60 = 120.000. Toplam patlama işlemleri 10.000 + 120.000 = 130.000. Veri bloğu işlem maliyeti, 13 10.000 işlem tarihine (bölgesel fiyatlandırmaya göre) göre $Y üzerinden ücretlendirilir.

Bu şekilde, bu faturalandırma saati ile birlikte disk üzerindeki toplam maliyet $X + $Y eşittir. Aynı hesaplama, MB/sn 'nin sağlanan hedefinin üzerine patlama için geçerlidir. MB/ç miktarı, 256 KB GÇ boyutuyla işlem için fazla. Disk trafiğiniz hem sağlanan ıOPS hem de MBps hedefini aşarsa, veri bloğu işlemlerini hesaplamak için aşağıdaki örneğe başvurabilirsiniz. 

Disk yapılandırması: Premium SSD – 1 TB (P30), disk patlaması etkin.

- 00:00:01 – 00:00:05 uygulama bir Batch işi verdi ve beş saniye boyunca disk ıOPS 'nin 10.000 ıOPS ve 300 MBps hızında veri bloğu olmasına neden oldu.
- 00:00:06 – 00:00:10 uygulaması, bir kurtarma işi verdi ve beş saniye boyunca disk ıOPS 'nin 6.000 ıOPS ve 600 MBps hızında veri bloğu olmasına neden oldu.

Veri bloğu işlemi, ıOPS 'den veya MB/sn 'ye kadar olan en fazla işlem sayısı olarak hesaba katılmaz. 00:00:01 – 00:00:05 ' den, birikmiş patlama işlemi Max ((10.000 – 5.000), (300-200) * 1024/256)) * 5 = 25.000 işlemleri. 00:00:06 – 00:00:10 ' den, birikmiş patlama işlemi Max ((6.000 – 5.000), (600-200) * 1024/256)) * 5 = 8.000 işlemleri. Bunun üzerine, isteğe bağlı tabanlı disk patlaması sağlamak için toplam maliyeti almak üzere veri bloğu etkinleştirme düz ücretini dahil edersiniz. 

Fiyatlandırma hakkında ayrıntılı bilgi edinmek için [yönetilen diskler fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/managed-disks/) başvurabilirsiniz ve iş yükünüz için değerlendirme yapmak üzere [Azure Fiyatlandırma Hesaplayıcı](https://azure.microsoft.com/pricing/calculator/?service=storage) ' yı kullanabilirsiniz. 


İsteğe bağlı tekrar açma özelliğini etkinleştirmek için bkz. [isteğe](../articles/virtual-machines/disks-enable-bursting.md)bağlı patlaması etkinleştirme.

### <a name="credit-based-bursting"></a>Kredi tabanlı patlama

Azure genel, kamu ve Çin bulutlarındaki tüm bölgelerde P20 ve daha küçük bir disk boyutu için kredi tabanlı burdıal sunulmaktadır. Varsayılan olarak, disk patlaması desteklenen disk boyutlarının tüm yeni ve var olan dağıtımlarında etkindir. VM düzeyinde burdıya yalnızca kredi tabanlı patlama kullanır.

## <a name="virtual-machine-level-bursting"></a>Sanal makine düzeyinde patlama

VM düzeyi patlaması yalnızca iade için kredi tabanlı modeli kullanıyorsa, bunu destekleyen tüm VM 'Ler için varsayılan olarak etkindir.

VM düzeyinde burdımı, desteklenen boyutlarda Azure genel bulutundaki tüm bölgelerde etkindir: 
- [Lsv2 serisi](../articles/virtual-machines/lsv2-series.md)
- [Dv3 ve Dsv3 serisi](../articles/virtual-machines/dv3-dsv3-series.md)
- [Ev3 ve Esv3 serisi](../articles/virtual-machines/ev3-esv3-series.md)

## <a name="bursting-flow"></a>Patlama akışı

Hakeyi kredi sistemi, hem VM düzeyinde hem de disk düzeyinde aynı şekilde geçerlidir. Kaynağınız, bir VM ya da disk, kendi veri bloğu demetini içinde tamamen Stokdaki kredilerle başlar. Bu krediler, en fazla patlama hızında 30 dakikaya kadar veri bloğu yapmanıza olanak sağlar. Kaynağın ıOPS veya MB/sn, kaynağın performans hedefinin altında kullanıldığı her seferinde krediler birikir. Kaynağınız, yatırım kredisi tahakkuk etmiştir ve iş yükünüz ek performansa ihtiyaç duyuyorsa, kaynağınız bu kredileri kullanarak performans sınırlarının üzerine gidebilir ve iş yükü taleplerini karşılamak için performansını artırabilir.

![Yer demet diyagramı.](media/managed-disks-bursting/bucket-diagram.jpg)

Kullanılabilir kredilerinizi size nasıl harcadığını öğrenin. 30 dakikalık patlama kredilerinizi günde bir arka arkaya veya spora üzerinden kullanabilirsiniz. Kaynaklar dağıtıldığında, kredilerin tam tahsisatına sahip olurlar. Bu gün sonra, yeniden stoklama için bir günden daha az zaman alır. Kredilerin, kaynakların patlaması için, patlama demetini 'nin tam olması gerekmez. Veri bloğu birikmesi, kullanılmayan ıOPS 'yi ve performans hedeflerinin altında MB/s 'yi temel aldığı için her kaynağa bağlı olarak farklılık gösterir. Daha yüksek temel performans kaynakları, ani kredilerin en düşük temel performanslı kaynakları daha hızlı bir şekilde tahakkuk edebilir. Örneğin, bir P1 disk kimliği saniyede 120 ıOPS tahakkuk eder, ancak bir Idling P20 diski saniyede 2.300 ıOPS olarak ücretlendirilir.

## <a name="bursting-states"></a>Gereksiz durumlar
Kaynağınız, yazılabilir özelliği etkin olan üç durum vardır:
- **Tahakkuk** : kaynağın GÇ trafiği performans hedefinden daha az kullanılıyor. IOPS için patlama kredisi ve MB/sn birbirinden farklı bir şekilde gerçekleştirilir. Kaynağınız ıOPS kredileri tahakkuk edebilir ve 50/s kredisi ya da tam tersi olabilir.
- **Burdımı** : kaynağın trafiği performans hedefinden daha fazla kullanılıyor. Veri bloğu trafiği ıOPS 'den veya bant genişliğinden bağımsız olarak kredileri kullanacaktır.
- **Sabit** : kaynağın trafiği tam olarak performans hedefidir.

## <a name="bursting-examples"></a>Patlama örnekleri

Aşağıdaki örneklerde, kaç farklı VM ve disk birleşimleriyle birlikte nasıl çalıştığı gösterilmektedir. Örneklerin daha kolay olmasını sağlamak için MB/sn 'ye odaklanacağız, ancak aynı mantık ıOPS 'ye bağımsız olarak uygulanır.

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>Burstable diskler içeren, bursuz sanal makine
**VM ve disk birleşimi:** 
- Standard_D8as_v4 
    - Önbelleğe alınmamış MB/s: 192
- P4 işletim sistemi diski
    - Sağlanan MB/s: 25
    - En fazla patlama MB/s: 170 
- 2 P10 veri diski 
    - Sağlanan MB/s: 100
    - En fazla patlama MB/s: 170

 VM önyüklendiğinde, işletim sistemi diskinden veri alır. İşletim sistemi diski, önyükleme yapan bir sanal makinenin parçası olduğundan, işletim sistemi diski, ani kredilerin sonuna kadar olacaktır. Bu krediler, işletim sistemi diskinin başlangıç olarak 170 MB/s saniye üzerinden başlatılmasını sağlar.

![VM, IŞLETIM sistemi diskine 192 MB/sn aktarım hızı için bir istek gönderir; işletim sistemi diski 170 MB/s verileriyle yanıt verir.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-startup.jpg)

Önyükleme tamamlandıktan sonra, bir uygulama VM üzerinde çalışır ve kritik olmayan bir iş yüküne sahiptir. Bu iş yükü, tüm diskler arasında eşit olarak yayılan 15 MB/S gerektirir.

![Uygulama VM 'ye 15 MB/sn aktarım hızı isteği gönderir, VM isteği alır ve disklerin her birini 5 MB/sn için bir istek gönderir, sanal makine 5 MB/sn döndürür; VM, uygulamaya 15 MB/s döndürür.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-idling.jpg)

Sonra uygulamanın 192 MB/s gerektiren bir toplu işi işlemesi gerekir. 2 MB/s, işletim sistemi diski tarafından kullanılır ve REST, veri diskleri arasında eşit olarak bölünür.

![Uygulama, VM 'ye 192 MB/sn aktarım hızı isteği gönderir, VM isteği alır ve isteğin toplu işini veri disklerine (95 MB/s) ve 2 MB/sn 'yi işletim sistemi diskine gönderir, veri diskleri talebi karşılayacak şekilde, bu da uygulamayı uygulamaya döndüren tüm diskler, istenen aktarım hızını sanal makineye döndürür.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>Bursuz diskler içeren Burstable sanal makine
**VM ve disk birleşimi:** 
- Standard_L8s_v2 
    - Önbelleğe alınmamış MB/s: 160
    - En fazla patlama MB/s: 1.280
- P50 işletim sistemi diski
    - Sağlanan MB/s: 250 
- 2 P10 veri diski 
    - Sağlanan MB/s: 250

 İlk önyüklemeden sonra, sanal makinede bir uygulama çalıştırılır ve kritik olmayan bir iş yüküne sahiptir. Bu iş yükü, tüm diskler arasında eşit olarak yayılan 30 MB/s gerektirir.
![Uygulama, VM 'ye 30 MB/sn aktarım hızı isteği gönderir, VM isteği alır ve her bir disk için 10 MB/sn isteği gönderir, sanal makine 10 MB/sn döndürür.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

Sonra uygulamanın 600 MB/s gerektiren bir toplu işi işlemesi gerekir. Standard_L8s_v2, bu talebi karşılamak ve ardından disklere yönelik istekler, P50 disklere eşit bir şekilde yayılmak üzere artışlarıyla.

![Uygulama, VM 'ye 600 MB/sn aktarım isteği gönderir, VM, isteği almak ve her bir disk için bir istek olan 200 MB/s için bir istek göndermesi için, her disk, 600 MB/s 'yi uygulamaya döndürecek şekilde 200 MB/s, VM artışlarıyla döndürür.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>Burstable disklere sahip Burstable sanal makine
**VM ve disk birleşimi:** 
- Standard_L8s_v2 
    - Önbelleğe alınmamış MB/s: 160
    - En fazla patlama MB/s: 1.280
- P4 işletim sistemi diski
    - Sağlanan MB/s: 25
    - En fazla patlama MB/s: 170 
- 2 P4 veri diski 
    - Sağlanan MB/s: 25
    - En fazla patlama MB/s: 170 

VM başlatıldığında, işletim sistemi diskinden 1.280 MB/sn 'ye ait veri bloğu sınırını istemek için veri bloğu, işletim sistemi diski ise 170 MB/s veri bloğu performansı ile yanıt verir.

![Başlangıçta VM, işletim sistemi diskine 1.280 MB/sn isteği göndermek için, işletim sistemi diski artışlarıyla 'yi, 1.280 MB/s döndürür.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

Başlangıçtan sonra, kritik olmayan iş yüküne sahip bir uygulama başlatabilirsiniz. Bu uygulama, tüm diskler arasında eşit olarak yayılan 15 MB/s gerektirir.

![Uygulama, VM 'ye 15 MB/sn aktarım hızı isteği gönderir, VM isteği alır ve disklerin her birini 5 MB/sn için bir istek gönderir, sanal makine 5 MB/sn yanıt döndürür; VM, uygulamaya 15 MB/s döndürür.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

Sonra uygulamanın 360 MB/s gerektiren bir toplu işi işlemesi gerekir. Standard_L8s_v2, bu talebi karşılamak ve sonra isteklerini karşılar. İşletim sistemi diski için yalnızca 20 MB/sn gerekiyor. Kalan 340 MB/s, burdıya P4 veri diskleri tarafından işlenir.

![Uygulama, VM 'ye 360 MB/sn aktarım isteği gönderir, VM, isteği alıp her bir veri diskine işletim sistemi diskinden bir 170 MB/s ve 20 MB/sn isteği gönderir, her disk istenen MB/s, sanal makine burslarını, uygulamaya 360.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)
