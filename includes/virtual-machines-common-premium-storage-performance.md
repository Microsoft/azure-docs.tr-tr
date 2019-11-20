---
title: include dosyası
description: include dosyası
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 289100afe825c14ce9964f39e3f583078f51da1d
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73182301"
---
## <a name="application-performance-indicators"></a>Uygulama performansı göstergeleri

Bir uygulamanın bir Kullanıcı isteğini ne kadar hızlı bir şekilde kullandığını veya kullanmadığını, bir uygulamanın istek başına ne kadar veri işleme olduğunu, belirli bir uygulama için kaç isteğin bir uygulama işleme olduğunu değerlendiriyoruz. bir kullanıcının isteklerini gönderdikten sonra yanıt almak için bekleyeceği süre. Bu performans göstergeleriyle ilgili teknik koşullar, ıOPS, verimlilik veya bant genişliği ve gecikmedir.

Bu bölümde, genel performans göstergelerini Premium Depolama bağlamında tartışacağız. Aşağıdaki bölümde, uygulama gereksinimlerini toplarken, uygulamanız için bu performans göstergelerini nasıl ölçeceğinizi öğreneceksiniz. Daha sonra uygulama performansını En Iyi duruma getirmek için, bu performans göstergelerini etkileyen faktörleri ve bunları en iyi duruma getirmeyi sağlayacak önerileri öğreneceksiniz.

## <a name="iops"></a>IOPS

IOPS veya saniye başına giriş/çıkış Işlemi, uygulamanızın bir saniye içinde depolama disklerine gönderdiği isteklerin sayısıdır. Bir giriş/çıkış işlemi okunabilir veya yazılabilir, sıralı veya rastgele olabilir. Çevrimiçi bir perakende Web sitesi gibi çevrimiçi Işlem Işleme (OLTP) uygulamalarının pek çok eşzamanlı kullanıcı isteğini hemen işlemesi gerekir. Kullanıcı istekleri, uygulamanın hızla işlemesi gereken yoğun veritabanı işlemlerini ekleme ve güncelleştirme işlemidir. Bu nedenle, OLTP uygulamaları çok yüksek ıOPS gerektirir. Bu tür uygulamalar milyonlarca küçük ve rastgele GÇ isteğini işler. Böyle bir uygulamanız varsa, ıOPS 'yi iyileştirmek için uygulama altyapısını tasarlamanız gerekir. Sonraki bölümde, *uygulama performansını En Iyi duruma getirmek*için, yüksek IOPS 'yi almak için göz önünde bulundurmanız gereken tüm faktörlerin ayrıntısıyla tartışıyoruz.

Yüksek ölçekli sanal makinenize bir Premium Depolama diski iliştirmeniz halinde Azure, disk belirtimine göre garantili sayıda ıOPS sağlar. Örneğin, bir P50 diski 7500 ıOPS sağlar. Her bir yüksek ölçekli VM boyutunun aynı zamanda, karşılayabilmesi için belirli bir ıOPS sınırı vardır. Örneğin, standart bir GS5 VM 80.000 ıOPS sınırına sahiptir.

## <a name="throughput"></a>İşleme

Aktarım hızı veya bant genişliği, uygulamanızın belirtilen bir aralıktaki depolama disklerine gönderdiği veri miktarıdır. Uygulamanız büyük GÇ birimi boyutlarıyla giriş/çıkış işlemleri yapıyorsa, yüksek verimlilik gerektirir. Veri ambarı uygulamaları, verilerin büyük kısımlarına tek seferde erişen ve genellikle toplu işlemler yapan tarama yoğun işlemleri yapmaya eğilimlidir. Diğer bir deyişle bu uygulamalar daha yüksek aktarım hızı gerektirir. Böyle bir uygulamanız varsa, üretilen işi iyileştirmek için altyapısını tasarlamanız gerekir. Sonraki bölümde, bunu başarmak için ayarlamanız gereken faktörleri ayrıntılı olarak ele aldık.

Yüksek ölçekli bir VM 'ye Premium Depolama diski iliştirirseniz, Azure bu disk belirtimine göre aktarım hızı sağlar. Örneğin, bir P50 disk, saniyede 250 MB 'lık disk işleme sağlar. Her bir yüksek ölçekli VM boyutunun aynı zamanda, karşılayabilmesi için belirli aktarım hızı limiti de vardır. Örneğin, standart GS5 VM, saniyede en fazla 2.000 MB aktarım hızına sahiptir.

Aşağıdaki formülde gösterildiği gibi, üretilen iş ve ıOPS arasında bir ilişki vardır.

![IOPS ve aktarım hızı ilişkisi](../articles/virtual-machines/linux/media/premium-storage-performance/image1.png)

Bu nedenle, uygulamanızın gerektirdiği en iyi aktarım hızı ve ıOPS değerlerini belirlenmesi önemlidir. Bir diğerini iyileştirmenize çalıştığınızda, diğeri de etkilenir. Daha sonraki bir bölümde, *uygulama performansını en*iyi duruma GETIRMEK için IOPS ve aktarım hızını iyileştirme hakkında daha fazla ayrıntı inceleyeceğiz.

## <a name="latency"></a>Gecikme süresi

Gecikme süresi, bir uygulamanın tek bir istek alması, depolama disklerine gönderilmesi ve yanıtı istemciye gönderilmesi için gereken süredir. Bu, ıOPS ve aktarım hızına ek olarak uygulamanın performansına yönelik kritik bir ölçüdür. Premium Depolama diskinin gecikmesi, bir istek için bilgilerin alınması ve uygulamanıza geri iletişim kurması için gereken süredir. Premium Depolama, düşük gecikme süreleri sağlar. Premium diskler, çoğu GÇ işlemi için tek basamaklı milisaniyelik gecikme süreleri sağlamak üzere tasarlanmıştır. Premium Depolama disklerinde ReadOnly ana bilgisayar önbelleğe almayı etkinleştirirseniz çok daha düşük okuma gecikmesi sağlayabilirsiniz. Daha sonra *uygulama performansını En Iyi duruma getirme*bölümünde daha ayrıntılı bilgi Için disk önbelleğe alma ele alınacaktır.

Uygulamanızı daha yüksek ıOPS ve aktarım hızı almak üzere iyileştirirken uygulamanızın gecikme süresini etkiler. Uygulama performansını ayarladıktan sonra, beklenmedik yüksek gecikme süresine engel olmak için her zaman uygulamanın gecikmesini değerlendirin.

Yönetilen disklerde aşağıdaki denetim düzlemi işlemleri, diskin bir depolama konumundan diğerine taşınmasını içerebilir. Bu, tamamlanması birkaç saat sürebileceği verilerin arka plan kopyası aracılığıyla düzenlenir ve genellikle, disklerdeki veri miktarına bağlı olarak 24 saatten daha az olur. Bu süre boyunca, bazı okumalar özgün konuma yeniden yönlendiriliyorsa ve tamamlanması daha uzun sürebileceğinden, uygulamanız olağan okuma gecikmesinden daha yüksek bir deneyim yaşayabilir. Bu süre boyunca yazma gecikmesi üzerinde hiçbir etkisi yoktur.

- Depolama türünü güncelleştirin.
- Bir diski bir VM 'den diğerine ayırın ve bağlayın.
- Bir VHD 'den yönetilen disk oluşturun.
- Bir anlık görüntüden yönetilen disk oluşturun.
- Yönetilmeyen diskleri yönetilen disklere dönüştürün.

# <a name="performance-application-checklist-for-disks"></a>Diskler için performans uygulaması denetim listesi

Azure Premium depolamada çalışan yüksek performanslı uygulamalar tasarlamanın ilk adımı, uygulamanızın performans gereksinimlerini öğrenmiş olur. Performans gereksinimlerini topladıktan sonra en iyi performansı elde etmek için uygulamanızı en iyi hale getirebilirsiniz.

Önceki bölümde, genel performans göstergeleri, ıOPS, verimlilik ve gecikme süresi açıklandık. İstenen kullanıcı deneyimini sunmak için, uygulamanız için hangi performans göstergelerinin önemli olduğunu belirlemeniz gerekir. Örneğin, yüksek ıOPS çok büyük bir saniyede milyonlarca işlemi işleyen OLTP uygulamalarının önemli bir kısmını. Öte yandan, büyük miktarlarda verileri saniye içinde işleyen veri ambarı uygulamaları için yüksek aktarım hızı önemlidir. Canlı video akışı Web siteleri gibi gerçek zamanlı uygulamalarda son derece düşük gecikme süresi önemlidir.

Daha sonra, uygulamanızın ömrü boyunca en fazla performans gereksinimlerini ölçün. Başlangıç olarak aşağıdaki örnek denetim listesini kullanın. Normal, yoğun ve kapalı iş yükü dönemlerinde en yüksek performans gereksinimlerini kaydeder. Tüm iş yükü düzeyleri için gereksinimleri tanımlayarak Uygulamanızın genel performans gereksinimini belirleyebileceksiniz. Örneğin, bir e-ticaret Web sitesinin normal iş yükü, bir yılda en fazla gün içinde hizmet verdiği işlemler olacaktır. Web sitesinin en yoğun iş yükü, tatil sezonu veya özel satış olayları sırasında hizmet verdiği işlemler olacaktır. Yoğun iş yükü genellikle sınırlı bir süre için yaşanır, ancak uygulamanızın normal işlemini iki veya daha fazla kez ölçeklendirmesini gerektirebilir. 50 yüzdebirlik, 90 yüzdebirlik ve 99 yüzdebirlik gereksinimlerini bulun. Bu, performans gereksinimleriyle ilgili herhangi bir şeyi filtrelemeye yardımcı olur ve doğru değerler için en iyi duruma getirme çabalarınıza odaklanabilir.

## <a name="application-performance-requirements-checklist"></a>Uygulama performansı gereksinimleri denetim listesi

| **Performans gereksinimleri** | **50 yüzdebirlik** | **90 yüzdebirlik** | **99 yüzdebirlik** |
| --- | --- | --- | --- |
| En çok, Saniye başına işlem | | | |
| Okuma işlemleri yüzdesi | | | |
| % Yazma işlemleri | | | |
| % Rastgele işlemler | | | |
| % Sıralı işlemler | | | |
| GÇ istek boyutu | | | |
| Ortalama üretilen Iş | | | |
| En çok, İşleme | | | |
| Min. Gecikme süresi | | | |
| Ortalama gecikme süresi | | | |
| En çok, CPU | | | |
| Ortalama CPU | | | |
| En çok, Hafıza | | | |
| Ortalama bellek | | | |
| Sıra derinliği | | | |

> [!NOTE]
> Bu sayıları, uygulamanızın gelecekteki büyüme artışına göre ölçeklendirmelisiniz. Daha sonra performansı iyileştirmek için altyapıyı değiştirmek daha zor olabileceğinden, büyümenin önceden planlanacağı iyi bir fikirdir.

Mevcut bir uygulamanız varsa ve Premium depolamaya geçmek istiyorsanız, önce mevcut uygulama için yukarıdaki denetim listesini derleyin. Daha sonra, Premium depolamada uygulamanızın bir örneğini oluşturun ve uygulamayı bu belgenin sonraki bölümlerinde *uygulama performansını En Iyi duruma getirme* bölümünde açıklanan yönergelere göre tasarlayın. Sonraki makalede, performans ölçümlerini toplamak için kullanabileceğiniz araçlar açıklanmaktadır.

### <a name="counters-to-measure-application-performance-requirements"></a>Uygulama performansı gereksinimlerini ölçmeye yönelik sayaçlar

Uygulamanızın performans gereksinimlerini ölçmenin en iyi yolu, sunucusunun işletim sistemi tarafından sunulan performans izleme araçlarını kullanmaktır. Windows için PerfMon ve Linux için Iostat kullanabilirsiniz. Bu araçlar yukarıdaki bölümde açıklanan her ölçüye karşılık gelen sayaçları yakalar. Uygulamanız normal, yoğun ve saat dışı iş yüklerini çalıştırıyorsa, bu sayaçların değerlerini yakalamalısınız.

PerfMon sayaçları işlemci, bellek ve sunucunuzdaki her mantıksal disk ve fiziksel disk için kullanılabilir. Premium Depolama disklerini bir VM ile kullandığınızda, her bir Premium Depolama diski için fiziksel disk sayaçları ve Premium Depolama disklerinde oluşturulan her birim için mantıksal disk sayaçları bulunur. Uygulama iş yükünüzü barındıran disklerin değerlerini yakalamalısınız. Mantıksal ve fiziksel diskler arasında bire bir eşleme varsa, fiziksel disk sayaçlarına başvurabilirsiniz; Aksi takdirde mantıksal disk sayaçlarına bakın. Linux 'ta, Iostat komutu bir CPU ve disk kullanımı raporu oluşturur. Disk kullanımı raporu, fiziksel cihaz veya bölüm başına istatistikler sağlar. Verileri ve günlükleri ayrı disklerde içeren bir veritabanı sunucusuna sahipseniz, bu verileri her iki disk için de toplayın. Aşağıdaki tabloda diskler, işlemciler ve Bellek sayaçlarını açıklanmaktadır:

| Sayaç | Açıklama | Sin | Iostat |
| --- | --- | --- | --- |
| **IOPS veya saniye başına Işlem** |Depolama diskine saniye başına verilen g/ç isteği sayısı. |Disk Okuma/sn <br> Disk yazma/sn |TPS <br> r/s <br> w/s |
| **Disk okuma ve yazma Işlemleri** |disk üzerinde gerçekleştirilen okuma ve yazma işlemlerinin yüzdesi. |% Disk okuma zamanı <br> % Disk yazma zamanı |r/s <br> w/s |
| **Aktarım hızı** |Saniye başına diskten okunan veya diske yazılan veri miktarı. |Disk okuma bayt/sn <br> Disk yazma bayt/sn |kB_read/s <br> kB_wrtn/s |
| **Gecikme süresi** |Bir disk GÇ isteğini tamamlayacak toplam süre. |Ortalama Disk sn/okuma <br> Ortalama Disk sn/yazma |Await <br> svctd |
| **GÇ boyutu** |G/ç isteklerinin boyutu depolama disklerinde sorun verir. |Ortalama disk bayt/okuma <br> Ortalama disk bayt/yazma |avgrq-SZ |
| **Sıra derinliği** |Depolama diskine okunmayı bekleyen bekleyen g/ç isteklerinin sayısı. |Geçerli disk sırası uzunluğu |avgqu-SZ |
| **Biçimlendir. Bellek** |Uygulamayı sorunsuz bir şekilde çalıştırmak için gereken bellek miktarı |Kullanılan kaydedilmiş bayt yüzdesi |Vmstat kullanma |
| **Biçimlendir. 'SUNA** |Uygulamayı sorunsuz şekilde çalıştırmak için CPU miktarı gereklidir |% İşlemci zamanı |% Util |

[Iostat](https://linux.die.net/man/1/iostat) ve [Perfmon](https://msdn.microsoft.com/library/aa645516.aspx)hakkında daha fazla bilgi edinin.



## <a name="optimize-application-performance"></a>Uygulama performansını iyileştirme

Premium depolamada çalışan bir uygulamanın performansını etkileyen ana faktörler, GÇ isteklerinin, VM boyutunun, disk boyutunun, disk sayısının, disk önbelleği, çoklu iş parçacığı ve sıra derinliği unsurlardır. Bu faktörlerden bazılarını sistem tarafından sunulan KBS ile kontrol edebilirsiniz. Çoğu uygulama, GÇ boyutunu ve sıra derinliğini doğrudan değiştirme seçeneği sunmayabilir. Örneğin, SQL Server kullanıyorsanız, GÇ boyutunu ve sıra derinliğini seçemezsiniz. SQL Server en iyi GÇ boyutunu ve sıra derinliği değerlerini en iyi performansı elde etmek için seçer. Performans ihtiyaçlarını karşılamak üzere uygun kaynakları sağlayabilmeniz için, uygulama performansındaki her iki tür faktörün etkilerini anlamak önemlidir.

Bu bölümde, uygulama performansınızı iyileştirmek için ne kadar ihtiyacınız olduğunu belirlemek için oluşturduğunuz uygulama gereksinimleri denetim listesine bakın. Buna bağlı olarak, bu bölümden hangi faktörleri ayarlamanız gerektiğini belirleyebileceksiniz. Uygulama performansındaki her bir faktörün etkilerini bir şekilde uygulamak için, uygulama kurulumunda benchişaretleme araçları 'nı çalıştırın. Windows ve Linux VM 'lerinde ortak bir değerlendirme aracı çalıştırma adımları için, sonda bağlı olan Benchişaretleme makalesine başvurun.

### <a name="optimize-iops-throughput-and-latency-at-a-glance"></a>IOPS 'yi, aktarım hızını ve gecikme süresini bir bakışta iyileştirin

Aşağıdaki tabloda, performans faktörleri ve ıOPS 'yi, aktarım hızını ve gecikmeyi iyileştirmek için gereken adımlar özetlenmektedir. Bu Özetteki bölümlerde her bir faktör daha ayrıntılı olarak açıklanır.

VM boyutları hakkında daha fazla bilgi ve her bir sanal makine türü için ıOPS, aktarım hızı ve gecikme süresi hakkında daha fazla bilgi için bkz. [LINUX VM boyutları](../articles/virtual-machines/linux/sizes.md) veya [Windows VM boyutları](../articles/virtual-machines/windows/sizes.md).

| &nbsp; | **'Ye** | **Aktarım hızı** | **Gecikme süresi** |
| --- | --- | --- | --- |
| **Örnek senaryo** |Saniye başına çok yüksek işlem gerektiren kurumsal OLTP uygulaması. |Büyük miktarlarda veriyi işleyen kurumsal veri ambarı uygulaması. |Çevrimiçi oyun gibi Kullanıcı isteklerine anında yanıt gerektiren neredeyse gerçek zamanlı uygulamalar. |
| Performans faktörleri | &nbsp; | &nbsp; | &nbsp; |
| **GÇ boyutu** |Daha küçük GÇ boyutu, ıOPS 'yi verir. |Daha yüksek performans elde etmek için daha büyük GÇ boyutu. | &nbsp;|
| **VM boyutu** |Uygulama gereksiniminden daha büyük ıOPS sağlayan bir VM boyutu kullanın. |Uygulama gereksiniminden daha büyük aktarım hızı sınırı ile bir VM boyutu kullanın. |Uygulama gereksiniminden daha büyük ölçek sınırları sunan bir VM boyutu kullanın. |
| **Disk boyutu** |Uygulama gereksiniminden daha büyük ıOPS sağlayan bir disk boyutu kullanın. |Uygulama gereksiniminden daha büyük aktarım hızı sınırı ile disk boyutu kullanın. |Uygulama gereksiniminden daha büyük ölçek sınırları sunan bir disk boyutu kullanın. |
| **VM ve disk ölçek sınırları** |Seçilen VM boyutunun ıOPS sınırı, kendisine bağlı Premium Depolama disklerinin kullanıldığı toplam ıOPS 'den büyük olmalıdır. |Seçilen VM boyutunun aktarım hızı sınırı, kendisine bağlı olan Premium Depolama disklerinin toplam aktarım hızına eşit olmalıdır. |Seçilen VM boyutunun ölçek sınırları, ekli Premium Depolama disklerinin toplam ölçek limitinden büyük olmalıdır. |
| **Disk önbelleğe alma** |Daha yüksek okuma ıOPS sağlamak için Premium Depolama disklerinde, okuma ağır işlemleri olan salt okunur önbelleği etkinleştirin. | &nbsp; |Çok düşük okuma gecikmeleri sağlamak için, Premium Depolama disklerinde, Premium Depolama disklerinde salt okunur önbelleği etkinleştirin. |
| **Disk şeridi** |Daha yüksek bir ıOPS ve aktarım hızı sınırı almak için birden çok disk kullanın ve bunları birlikte toplayın. VM başına Birleşik Sınır, ekli Premium disklerin birleştirilmiş limitlerinden daha yüksek olmalıdır. | &nbsp; | &nbsp; |
| **Şerit boyutu** |OLTP uygulamalarında görülen rastgele küçük GÇ deseninin daha küçük şerit boyutu. Örneğin, SQL Server OLTP uygulaması için 64 KB 'lık Stripe boyutunu kullanın. |Veri ambarı uygulamalarında görülen sıralı büyük GÇ deseninin daha büyük şerit boyutu. Örneğin, SQL Server veri ambarı uygulaması için 256 KB 'lık dizili boyut kullanın. | &nbsp; |
| **İş** |Daha yüksek ıOPS ve aktarım hızına yol açacak Premium depolamaya daha yüksek sayıda istek göndermek için çoklu iş parçacığı kullanımı kullanın. Örneğin, SQL Server üzerinde SQL Server daha fazla CPU ayırmak için yüksek bir MAXDOP değeri ayarlayın. | &nbsp; | &nbsp; |
| **Sıra derinliği** |Daha büyük sıra derinliği, ıOPS 'yi verir. |Daha büyük sıra derinliği daha yüksek aktarım hızı verir. |Daha küçük sıra derinliği daha düşük gecikme süreleri verir. |

## <a name="nature-of-io-requests"></a>GÇ isteklerinin doğası

GÇ isteği, uygulamanızın gerçekleştirmesi için bir giriş/çıkış işlemi birimidir. GÇ isteklerinin yapısını, rastgele veya sıralı, okuma veya yazma, küçük veya büyük olarak tanımlamak, uygulamanızın performans gereksinimlerini belirlemenize yardımcı olur. Uygulama altyapınızı tasarlarken doğru kararları vermek için GÇ isteklerinin doğasını anlamak önemlidir. Mümkün olan en iyi performansı elde etmek için IOs 'un eşit olarak dağıtılması gerekir.

GÇ boyutu, daha önemli faktörlerden biridir. GÇ boyutu, uygulamanız tarafından oluşturulan giriş/çıkış işlemi isteğinin boyutudur. GÇ boyutunun, özellikle de uygulamanın elde edebildii ıOPS ve bant genişliği üzerinde performans üzerinde önemli bir etkisi vardır. Aşağıdaki formül ıOPS, GÇ boyutu ve bant genişliği/aktarım hızı arasındaki ilişkiyi gösterir.  
    ![](media/premium-storage-performance/image1.png)

Bazı uygulamalar, bazı uygulamalar olmasa da bunun GÇ boyutunu değiştirmelerini sağlar. Örneğin, SQL Server en iyi GÇ boyutunu belirler ve bunu değiştirmek için kullanıcılara herhangi bir KBS sağlamaz. Diğer taraftan, Oracle, veritabanının g/ç istek boyutunu yapılandırabileceğiniz [DB\_BLOCK\_boyutu](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) adlı bir parametre sağlar.

GÇ boyutunu değiştirmenize izin verilmeyen bir uygulama kullanıyorsanız, uygulamanızla en uygun performans KPI 'sini iyileştirmek için bu makaledeki yönergeleri kullanın. Örneğin,

* OLTP uygulaması milyonlarca küçük ve rastgele GÇ isteği oluşturur. Bu GÇ isteği türlerini işlemek için, uygulama altyapınızı daha yüksek ıOPS 'yi almak üzere tasarlamanız gerekir.  
* Veri ambarı uygulaması büyük ve sıralı GÇ istekleri üretir. Bu GÇ isteği türlerini işlemek için, uygulama altyapınızı daha yüksek bant genişliği veya aktarım hızı sağlamak üzere tasarlamanız gerekir.

GÇ boyutunu değiştirmenize olanak sağlayan bir uygulama kullanıyorsanız, diğer performans yönergelerine ek olarak GÇ boyutu için bu Thumb kuralını kullanın.

* Daha yüksek ıOPS 'yi almak için daha küçük GÇ boyutu. Örneğin, OLTP uygulaması için 8 KB.  
* Daha yüksek bant genişliği/aktarım hızı sağlamak için büyük GÇ boyutu. Örneğin, bir veri ambarı uygulaması için 1024 KB.

Uygulamanızın ıOPS ve aktarım hızını ve bant genişliğini nasıl hesaplayabileceğiniz hakkında bir örnek aşağıda verilmiştir. Bir P30 diski kullanarak bir uygulamayı düşünün. P30 diskinin alabileceği maksimum ıOPS ve aktarım hızı/bant genişliği, sırasıyla 5000 ıOPS ve 200 MB 'tır. Artık, uygulamanız P30 diskinden maksimum ıOPS gerektiriyorsa ve 8 KB gibi daha küçük bir GÇ boyutu kullanırsanız, elde edilen bant genişliği saniyede 40 MB 'lık bir değer elde edersiniz. Ancak, uygulamanız P30 diskinden maksimum üretilen Iş/bant genişliği gerektiriyorsa ve 1024 KB gibi daha büyük bir GÇ boyutu kullanırsanız, sonuçta elde edilen ıOPS daha az, 200 ıOPS olur. Bu nedenle, GÇ boyutunu uygulamanızın ıOPS ve aktarım hızı/bant genişliği gereksinimini karşılayacak şekilde ayarlayın. Aşağıdaki tabloda, bir P30 diski için farklı GÇ boyutları ve bunlara karşılık gelen ıOPS ve aktarım hızı özetlenmektedir.

| Uygulama gereksinimi | G/ç boyutu | IOPS | Aktarım hızı/bant genişliği |
| --- | --- | --- | --- |
| Maksimum ıOPS |8 KB |5\.000 |saniyede 40 MB |
| En fazla aktarım hızı |1024 KB |200 |saniyede 200 MB |
| Maksimum üretilen Iş + yüksek ıOPS |64 KB |3\.200 |saniyede 200 MB |
| Maksimum ıOPS + yüksek aktarım hızı |32 KB |5\.000 |saniyede 160 MB |

IOPS 'yi ve bant genişliğini tek bir Premium Depolama diskinin en büyük değerinden daha yüksek bir değere almak için birlikte şeritli birden fazla Premium disk kullanın. Örneğin, 10.000 ıOPS 'nin Birleşik bir ıOPS 'sini veya saniyede 400 MB Birleşik aktarım hızını almak için iki P30 diski Stripe. Sonraki bölümde açıklandığı gibi, Birleşik disk ıOPS ve aktarım hızını destekleyen bir VM boyutu kullanmanız gerekir.

> [!NOTE]
> IOPS 'yi veya üretilen Iş üretimini artırdıkça, diğerini artırarak diskin veya VM 'nin aktarım hızı veya ıOPS limitlerinin isabet aldığınızdan emin olun.

Uygulama performansı üzerinde GÇ boyutunun etkilerini sağlamak için, VM 'niz ve disklerinizde benchişaretleme araçları çalıştırabilirsiniz. Birden çok test çalıştırması oluşturun ve etkiyi görmek için her çalıştırma için farklı GÇ boyutu kullanın. Daha fazla ayrıntı için, sonda bağlı olan Benchişaretleme makalesine başvurun.

## <a name="high-scale-vm-sizes"></a>Yüksek ölçekli VM boyutları

Bir uygulamayı tasarlamaya başladığınızda, ilk yapmanız gereken tek şey, uygulamanızı barındırmak için bir VM seçin. Premium Depolama, daha yüksek bilgi işlem gücü ve yüksek bir yerel disk g/ç performansı gerektiren uygulamalar çalıştırabilen yüksek ölçekli VM boyutlarına sahiptir. Bu VM 'Ler, daha hızlı işlemciler, daha yüksek bellek-çekirdek oranı ve yerel disk için katı hal sürücüsü (SSD) sağlar. Premium depolamayı destekleyen yüksek ölçekli VM 'Lere örnek olarak DS, DSv2 ve GS serisi VM 'Ler verilebilir.

Yüksek ölçekli VM 'Ler, farklı boyutlarda CPU çekirdekleri, bellek, işletim sistemi ve geçici disk boyutuyla farklı boyutlarda kullanılabilir. Her VM boyutunun Ayrıca sanal makineye iliştirebilmeniz için maksimum veri diski sayısı vardır. Bu nedenle, seçilen VM boyutu, uygulamanız için ne kadar işlem, bellek ve depolama kapasitesi olduğunu etkiler. Ayrıca Işlem ve depolama maliyetini de etkiler. Örneğin, bir DS serisi, DSv2 serisi ve GS serisi içindeki en büyük VM boyutunun belirtimleri aşağıda verilmiştir:

| VM boyutu | CPU çekirdekleri | Hafıza | VM diski boyutları | En çok, Veri diskleri | Önbellek boyutu | IOPS | Bant genişliği önbellek GÇ sınırları |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS14 |16 |112 GB |OS = 1023 GB <br> Yerel SSD = 224 GB |32 |576 GB |50.000 ıOPS <br> saniyede 512 MB |4\.000 ıOPS ve 33 MB/saniye |
| Standard_GS5 |32 |448 GB |OS = 1023 GB <br> Yerel SSD = 896 GB |64 |4224 GB |80.000 ıOPS <br> saniyede 2.000 MB |5\.000 ıOPS ve 50 MB/saniye |

Tüm kullanılabilir Azure VM boyutlarının tüm listesini görüntülemek için [WINDOWS VM boyutları](../articles/virtual-machines/windows/sizes.md) veya [Linux VM boyutları](../articles/virtual-machines/linux/sizes.md)' na bakın. İstenen uygulama performansı gereksinimlerinize uyan ve ölçeklendirebilen bir VM boyutu seçin. Buna ek olarak, VM boyutlarını seçerken önemli noktalara göz atın.

*Ölçek sınırları*  
VM başına en fazla ıOPS sınırı ve disk başına her ikisi birbirinden farklıdır. Uygulamanın ıOPS 'yi VM sınırları içinde ve ona bağlı olan Premium disklere yönlendirdiğinden emin olun. Aksi takdirde, uygulama performansı azaltma deneyimidir.

Örnek olarak, bir uygulama gereksiniminin en fazla 4.000 ıOPS olduğunu varsayalım. Bunu başarmak için, bir DS1 sanal makinesine P30 diski sağlayacaksınız. P30 diski en fazla 5.000 ıOPS sağlayabilir. Ancak, DS1 VM 3.200 ıOPS ile sınırlıdır. Sonuç olarak, uygulama performansı 3.200 ıOPS 'de VM sınırı ile sınırlandırılır ve performans düzeyi düşecek. Bu durumu engellemek için, uygulama gereksinimlerini karşılayacak bir VM ve disk boyutu seçin.

*Işlem maliyeti*  
Çoğu durumda, Premium depolama kullanan genel işlem maliyetiniz standart depolamayı kullanmaktan daha düşüktür.

Örneğin, 16.000 ıOPS gerektiren bir uygulamayı düşünün. Bu performansı elde etmek için standart bir\_D14 Azure IaaS VM gerekir. Bu, 32 standart depolama 1 TB disklerini kullanarak en fazla 16.000 ıOPS sağlayabilir. Her 1 TB standart depolama diski en fazla 500 ıOPS elde edebilir. Bu VM 'nin aylık tahmini maliyeti $1.570 olacaktır. 32 standart depolama disklerinin aylık maliyeti $1.638 olacaktır. Tahmini toplam aylık maliyet $3.208 olacaktır.

Ancak, Premium depolamada aynı uygulamayı barındırdıysanız, daha küçük bir VM boyutu ve daha az Premium Depolama diski gerekir ve bu sayede genel maliyeti azaltabilirsiniz. Standart bir\_DS13 VM, dört P30 diski kullanarak 16.000 ıOPS gereksinimini karşılayabilir. DS13 VM en fazla 25.600 ıOPS 'ye sahiptir ve her P30 diskte en fazla 5.000 ıOPS vardır. Genel olarak, bu yapılandırma 5.000 x 4 = 20.000 ıOPS elde edebilir. Bu VM 'nin aylık tahmini maliyeti $1.003 olacaktır. Dört P30 Premium Depolama diskinin aylık maliyeti $544,34 olacaktır. Tahmini toplam aylık maliyet $1.544 olacaktır.

Aşağıdaki tablo, standart ve Premium Depolama için bu senaryonun maliyet dökümünü özetler.

| &nbsp; | **Standart** | **Premium** |
| --- | --- | --- |
| **Aylık VM maliyeti** |$1.570,58 (Standart\_D14) |$1.003,66 (Standart\_DS13) |
| **Aylık disk maliyeti** |$1.638,40 (32 x 1-TB disk) |$544,34 (4 x P30 disk) |
| **Aylık genel maliyet** |$3.208,98 |$1.544,34 |

*Linux distros*  

Azure Premium Depolama ile Windows ve Linux çalıştıran VM 'Ler için aynı performans düzeyine sahip olursunuz. Linux kaldırmalarının pek çok türünü destekliyoruz ve listenin tamamını [burada](../articles/virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)görebilirsiniz. Farklı iş yüklerinin farklı türleri için daha uygun olduğunu unutmamak önemlidir. İş yükünüzün üzerinde çalıştığı bir değere göre farklı düzeylerde performans göreceksiniz. Linux Distro 'lara 'yi uygulamanızla test edin ve en iyi şekilde çalışacak olanı seçin.

Linux 'u Premium Depolama ile çalıştırırken, yüksek performans sağlamak için gerekli sürücülerle ilgili en son güncelleştirmeleri denetleyin.

## <a name="premium-storage-disk-sizes"></a>Premium Depolama diski boyutları

Azure Premium Depolama, gereksinimlerinize en uygun olanı seçebilmeniz için çeşitli boyutlarda bir boyut sunar. Her disk boyutunun ıOPS, bant genişliği ve depolama için farklı ölçek sınırı vardır. Uygulama gereksinimlerine ve yüksek ölçekli VM boyutuna bağlı olarak doğru Premium Depolama diski boyutunu seçin. Aşağıdaki tabloda, disklerin boyutları ve özellikleri gösterilmektedir. P4, P6, P15, P60, P70 ve P80 boyutları şu anda yalnızca yönetilen diskler için desteklenmektedir.

[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Kaç disk seçtiğiniz disk boyutuna bağlıdır. Uygulamanızın gereksinimini karşılamak için tek bir P50 disk veya birden çok P10 diski kullanabilirsiniz. Seçim yaparken aşağıda listelenen hesaba göz atın.

*Ölçek sınırları (ıOPS ve aktarım hızı)*  
Her Premium disk boyutunun ıOPS ve aktarım hızı sınırları farklı ve VM Ölçek limitinden bağımsızdır. Disklerden toplam ıOPS ve üretilen Iş üretiminin, seçilen VM boyutunun ölçek sınırları içinde olduğundan emin olun.

Örneğin, bir uygulama gereksinimi en fazla 250 MB/sn aktarım hızı ise ve tek bir P30 diski ile bir DS4 VM kullanıyorsanız. DS4 VM, en fazla 256 MB/sn aktarım hızı verebilir. Ancak, tek bir P30 diskinde 200 MB/sn aktarım hızı sınırı vardır. Sonuç olarak, uygulama, disk sınırı nedeniyle 200 MB/sn ile kısıtlanır. Bu sınırı aşmak için, VM 'de birden fazla veri diski sağlayın veya disklerinizi P40 veya P50 olarak yeniden boyutlandırın.

> [!NOTE]
> Önbellek tarafından sunulan okumalar disk ıOPS ve aktarım hızına dahil değildir, bu nedenle disk sınırlarına tabi değildir. Önbelleğin VM başına ayrı ıOPS ve aktarım hızı sınırı vardır.
>
> Örneğin, başlangıçta okuma ve yazma işlemleri sırasıyla 60MB/sn ve 40MB/sn 'DIR. Zaman içinde önbellek, önbellekten daha fazla ve daha fazla okuma yapar. Daha sonra, diskten daha yüksek yazma aktarım hızı edinebilirsiniz.

*Disk sayısı*  
Uygulama gereksinimlerini değerlendirerek gereken disk sayısını belirleme. Her VM boyutunun Ayrıca VM 'ye iliştirebilmeniz için disk sayısıyla ilgili bir sınırı vardır. Genellikle, bu çekirdek sayısı iki katına kaydedilir. Seçtiğiniz VM boyutunun, gereken disk sayısını destekleyediğinden emin olun.

Premium Depolama disklerinin standart depolama disklerine kıyasla daha yüksek performans özelliklerine sahip olduğunu unutmayın. Bu nedenle, uygulamanızı standart depolama kullanarak Azure IaaS VM 'den Premium depolamaya geçiriyorsanız, uygulamanız için aynı veya daha yüksek performans elde etmek için büyük olasılıkla daha az Premium disk gerekecektir.

## <a name="disk-caching"></a>Disk önbelleğe alma

Azure Premium Depolamalarından yararlanan yüksek ölçekli VM 'Ler BlobCache adlı çok katmanlı bir önbelleğe alma teknolojisine sahiptir. BlobCache, önbelleğe alma için sanal makine RAM ve yerel SSD 'nin bir birleşimini kullanır. Bu önbellek, Premium Depolama kalıcı disklerinde ve VM yerel disklerinde kullanılabilir. Varsayılan olarak, bu önbellek ayarı, Premium depolamada barındırılan veri diskleri için işletim sistemi diskleri ve salt okunur için okuma/yazma olarak ayarlanır. Premium Depolama disklerinde etkinleştirilmiş disk önbelleği sayesinde, yüksek ölçekli VM 'Ler temel disk performansını aşan son derece yüksek performans düzeyine sahip olabilir.

> [!WARNING]
> Disk önbelleği 4 TiB ve daha büyük diskler için desteklenmez. Sanal makinenize birden çok disk iliştirilmişse, 4 TiB 'den küçük olan her bir disk önbelleğe almayı destekleyecektir.
>
> Bir Azure diskinin önbellek ayarını değiştirmek hedef diski ayırır ve yeniden iliştirir. İşletim sistemi diski ise, VM yeniden başlatılır. Disk önbelleği ayarını değiştirmeden önce bu kesintiye uğramadan etkilenebilecek tüm uygulamaları/hizmetleri durdurun.

BlobCache 'in nasıl çalıştığı hakkında daha fazla bilgi edinmek için [Azure Premium Depolama](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) blog gönderisine bakın.

Doğru disk kümesinde önbelleğin etkinleştirilmesi önemlidir. Bir Premium diskte disk önbelleğe almayı etkinleştirmeniz gerekip gerekmediğini belirten bir işlem, diskin işleme alınacak iş yükü düzenine bağlıdır. Aşağıdaki tabloda, işletim sistemi ve veri diskleri için varsayılan önbellek ayarları gösterilmektedir.

| **Disk türü** | **Varsayılan önbellek ayarı** |
| --- | --- |
| İşletim sistemi diski |ReadWrite |
| Veri diski |ReadOnly |

Veri diskleri için önerilen disk önbelleği ayarları aşağıda verilmiştir.

| **Disk önbelleğe alma ayarı** | **Bu ayarın ne zaman kullanılacağı önerisi** |
| --- | --- |
| Hiçbiri |Ana bilgisayar ön belleğini salt yazılır ve yazma ağır diskler için hiçbiri olarak yapılandırın. |
| ReadOnly |Salt okunur ve okuma/yazma diskleri için konak önbelleğini ReadOnly olarak yapılandırın. |
| ReadWrite |Konak ön belleğini yalnızca, uygulamanız gerektiğinde kalıcı disklere önbelleğe alınmış verileri yazmayı doğru şekilde işlediğinde, salt yazılır olarak yapılandırın. |

*Özelliğinin*  
Premium depolama veri disklerinde ReadOnly önbelleğe alma yapılandırarak, düşük okuma gecikmesi elde edebilir ve uygulamanız için çok fazla okuma ıOPS ve aktarım hızı elde edebilirsiniz. Bunun nedeni iki nedenden dolayı

1. VM belleği ve yerel SSD üzerinde olan önbellekten gerçekleştirilen okumalar, Azure Blob depolamada bulunan veri diskinden okumalarından çok daha hızlıdır.  
1. Premium Depolama, önbellekten sunulan okuma sayısını, disk ıOPS ve aktarım hızına doğru saymaz. Bu nedenle, uygulamanız toplam ıOPS ve aktarım hızı elde edebilir.

*ReadWrite*  
Varsayılan olarak, işletim sistemi disklerinin ReadWrite önbelleği etkindir. Kısa süre önce veri disklerinde de ReadWrite önbelleği desteği ekledik. Okuma önbelleği kullanıyorsanız, verileri önbellekten kalıcı disklere yazmak için uygun bir yola sahip olmanız gerekir. Örneğin, SQL Server önbelleğe alınan verileri kendi kendine kalıcı depolama disklerine yazmayı işler. Gerekli verileri kalıcı olarak işlemeyen bir uygulamayla ReadWrite önbelleği kullanmak, VM kilitlenirse veri kaybına yol açabilir.

*Seçim*  
Şu anda **hiçbiri** yalnızca veri disklerinde desteklenir. İşletim sistemi disklerinde desteklenmez. Bir işletim sistemi diskinde **hiçbiri** ayarlarsanız, bunu dahili olarak geçersiz kılar ve **salt okunur**olarak ayarlar.

Örnek olarak, aşağıdaki işlemleri yaparak Premium depolamada çalışan SQL Server için bu yönergeleri uygulayabilirsiniz.

1. Veri dosyalarını barındıran Premium Depolama disklerinde "ReadOnly" önbelleğini yapılandırın.  
   a.  Veri sayfalarının önbellekten doğrudan veri disklerinden kıyasla daha hızlı bir şekilde alınmasından sonra önbellekten hızlı okuma SQL Server sorgu süresini azaltır.  
   b.  Önbellekten okuma hizmeti sunma, Premium veri disklerinden daha fazla verimlilik olduğu anlamına gelir. SQL Server, daha fazla veri sayfası ve yedekleme/geri yükleme, toplu iş yükleri ve dizin yeniden oluştur gibi diğer işlemleri almak için bu ek aktarım hızını kullanabilir.  
1. Günlük dosyalarını barındıran Premium Depolama disklerinde "none" önbelleğini yapılandırın.  
   a.  Günlük dosyalarında öncelikle yazma ağır işlemler vardır. Bu nedenle, salt okunur önbellekten faydalanır.

## <a name="optimize-performance-on-linux-vms"></a>Linux VM 'lerde performansı iyileştirme

Önbelleği **ReadOnly** veya **none**olarak ayarlanmış tüm Premium SSD 'ler veya ultra diskler için, dosya sistemini bağladığınızda "engelleri" devre dışı bırakmanız gerekir. Premium Depolama disklerine yazma işlemleri bu önbellek ayarları için dayanıklı olduğundan, bu senaryoda engellere ihtiyacınız yoktur. Yazma isteği başarıyla tamamlandığında, veriler kalıcı depoya yazıldı. "Engelleri" devre dışı bırakmak için aşağıdaki yöntemlerden birini kullanın. Dosya sisteminiz için bir tane seçin:
  
* **Reıfs**için, engelleri devre dışı bırakmak için `barrier=none` bağlama seçeneğini kullanın. (Engelleri etkinleştirmek için `barrier=flush` kullanın.)
* **Ext3/ext4**için, engelleri devre dışı bırakmak için `barrier=0` bağlama seçeneğini kullanın. (Engelleri etkinleştirmek için `barrier=1` kullanın.)
* **XFS**için, engelleri devre dışı bırakmak için `nobarrier` bağlama seçeneğini kullanın. (Engelleri etkinleştirmek için `barrier` kullanın.)
* Önbellek **okuma**olarak ayarlanan Premium Depolama disklerinde, yazma dayanıklılığı için engelleri etkinleştirin.
* VM 'yi yeniden başlattıktan sonra birim etiketlerinin devam etmesi için,/etc/fstab ' ı disklere evrensel benzersiz tanımlayıcı (UUID) başvuruları ile güncelleştirmeniz gerekir. Daha fazla bilgi için bkz. [LINUX VM 'ye yönetilen disk ekleme](../articles/virtual-machines/linux/add-disk.md).

Aşağıdaki Linux dağıtımları Premium SSD 'Ler için onaylanmıştır. Premium SSD 'Ler ile daha iyi performans ve kararlılık için, sanal makinelerinizi bu sürümlerden birine veya daha yeni bir sürüme yükseltmenizi öneririz. 

Sürümlerden bazıları Azure için en son Linux Integration Services (LIS), v 4.0 gerektirir. Bir dağıtımı indirmek ve yüklemek için aşağıdaki tabloda listelenen bağlantıyı izleyin. Doğrulama tamamlandığımızda listeye görüntüler ekleyeceğiz. Doğrulamalarımız performansı her bir görüntü için farklılık gösterir. Performans, iş yükü özelliklerine ve görüntü ayarlarınıza bağlıdır. Farklı iş yükü türleri için farklı görüntüler ayarlanır.

| Dağıtım | Sürüm | Desteklenen çekirdek | Ayrıntılar |
| --- | --- | --- | --- |
| Ubuntu | 12,04 veya üzeri| 3.2.0-75.110 + | Ubuntu-12_04_5-LTS-amd64-Server-20150119-en-US-30 |
| Ubuntu | 14,04 veya üzeri| 3.13.0-44.73 +  | Ubuntu-14_04_1-LTS-amd64-Server-20150123-en-US-30 |
| Debian | 7. x, 8. x veya daha yeni| 3.16.7-ckt4-1 + | &nbsp; |
| SUSE | SLES 12 veya üzeri| 3.12.36-38.1 + | SUSE-SLES-12-Priority-v20150213 <br> SUSE-SLES-12-v20150213 |
| SUSE | SLES 11 SP4 veya daha yeni| 3.0.101-0.63.1 + | &nbsp; |
| CoreOS | 584.0.0 + veya üzeri| 3.18.4 + | CoreOS 584.0.0 |
| CentOS | 6,5, 6,6, 6,7, 7,0 veya daha yeni| &nbsp; | [LIS4 gerekli](https://www.microsoft.com/download/details.aspx?id=51612) <br> *Sonraki bölümde nota bakın* |
| CentOS | 7.1 + veya üzeri| 3.10.0-229.1.2. EL7 + | [LIS4 önerilir](https://www.microsoft.com/download/details.aspx?id=51612) <br> *Sonraki bölümde nota bakın* |
| Red Hat Enterprise Linux (RHEL) | 6.8 +, 7.2 + ya da daha yeni | &nbsp; | &nbsp; |
| Oracle | 6.0 +, 7.2 + veya daha yeni | &nbsp; | UEK4 veya RHCK |
| Oracle | 7.0-7.1 veya üzeri | &nbsp; | UEK4 veya RHCK w/[LIS 4.1 +](https://www.microsoft.com/download/details.aspx?id=51612) |
| Oracle | 6.4-6.7 veya üzeri | &nbsp; | UEK4 veya RHCK w/[LIS 4.1 +](https://www.microsoft.com/download/details.aspx?id=51612) |

### <a name="lis-drivers-for-openlogic-centos"></a>OpenLogic CentOS için LIS sürücüleri

OpenLogic CentOS sanal makinelerini çalıştırıyorsanız, en son sürücüleri yüklemek için aşağıdaki komutu çalıştırın:

```
sudo rpm -e hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
```

Yeni sürücüleri etkinleştirmek için VM 'yi yeniden başlatın.

## <a name="disk-striping"></a>Disk şeridi

Yüksek ölçekli bir VM birkaç Premium Depolama kalıcı diskle birlikte eklendiğinde, diskler IOPS, bant genişliği ve depolama kapasitesini toplamak için birlikte birleştirilebilir.

Windows 'ta, diskleri birleştirmek için depolama alanlarını kullanabilirsiniz. Havuzdaki her disk için bir sütun yapılandırmanız gerekir. Aksi takdirde, disklerin disk genelindeki düzensiz dağıtımı nedeniyle, Şeritli birimin genel performansı beklenenden daha düşük olabilir.

Önemli: Sunucu Yöneticisi Kullanıcı arabirimini kullanarak, şeritli bir birim için toplam sütun sayısını 8 ' e kadar ayarlayabilirsiniz. Sekiz ' dan fazla disk iliştirirken, birimi oluşturmak için PowerShell kullanın. PowerShell 'i kullanarak, sütun sayısını disk sayısına eşit olarak ayarlayabilirsiniz. Örneğin, tek bir Stripe kümesinde 16 disk varsa; *New-VirtualDisk* PowerShell cmdlet 'Inin *numberofcolumns* parametresinde 16 sütun belirtin.

Linux 'ta, disk birleştirmek için MDADDM yardımcı programını kullanın. Linux 'ta diskleri şeritleme hakkında ayrıntılı adımlar için [bkz. Linux 'Ta yazılım RAID yapılandırma](../articles/virtual-machines/linux/configure-raid.md).

*Şerit boyutu*  
Disk şeridi oluşturma bölümünde önemli bir yapılandırma, şerit boyutudur. Şerit boyutu veya blok boyutu, uygulamanın dizili bir birimde adresebilen en küçük veri öbektir. Yapılandırdığınız Stripe boyutu, uygulamanın türüne ve istek düzenine bağlıdır. Yanlış Stripe boyutunu seçerseniz, GÇ hizalanmasına neden olabilir ve bu da uygulamanızın performansının düşmesine yol açar.

Örneğin, uygulamanız tarafından oluşturulan bir GÇ isteği disk Stripe boyutundan büyükse, depolama sistemi bu dosyayı birden fazla diskte dizili birim sınırlarına yazar. Bu verilere erişmek için zaman olduğunda, isteği tamamlaması için birden fazla Stripe biriminde arama yapmak gerekir. Bu davranışın birikimli etkisi önemli performans düşüşüne neden olabilir. Öte yandan, GÇ istek boyutu Stripe boyutundan küçükse ve doğası halinde rastgele ise, GÇ istekleri aynı disk üzerinde bir performans sorununa neden olabilir ve sonuç olarak GÇ performansını düşürür.

Uygulamanızın çalıştırıldığı iş yükünün türüne bağlı olarak uygun bir şerit boyutu seçin. Rastgele küçük GÇ istekleri için daha küçük bir şerit boyutu kullanın. Öte yandan, büyük sıralı GÇ istekleri için daha büyük bir şerit boyutu kullanır. Premium depolama alanında çalıştırdığınız uygulamanın Stripe boyut önerilerini bulun. SQL Server için, OLTP iş yükleri için 64 KB 'lık şeritli boyut ve veri ambarı iş yükleri için 256 KB yapılandırın. Daha fazla bilgi için bkz. [Azure VM 'lerinde SQL Server için En Iyi performans uygulamaları](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md#disks-guidance) .

> [!NOTE]
> Bir DS serisi VM 'de en fazla 32 Premium Depolama diski ve bir GS serisi VM 'de 64 Premium Depolama diski ekleyebilirsiniz.

## <a name="multi-threading"></a>Çoklu iş parçacığı

Azure, Premium Depolama platformunu çok büyük bir paralel olacak şekilde tasarlamış. Bu nedenle, çok iş parçacıklı bir uygulama tek iş parçacıklı bir uygulamadan çok daha yüksek performansa sahip olur. Çok iş parçacıklı bir uygulama, birden çok iş parçacığı üzerinde görevlerini böler ve VM ve disk kaynaklarından maksimum sınıra kadar kendi yürütme verimliliğini artırır.

Örneğin, uygulamanız iki iş parçacığı kullanan tek çekirdekli bir VM üzerinde çalışıyorsa, verimlilik elde etmek için CPU iki iş parçacığı arasında geçiş yapabilir. Bir iş parçacığı bir disk GÇ işleminin tamamlanmasını beklerken, CPU diğer iş parçacığına geçebilir. Bu şekilde, iki iş parçacığı tek bir iş parçacığından fazlasını gerçekleştirebilir. VM 'nin birden fazla çekirdeği varsa, her bir çekirdek görevleri paralel olarak yürütebileceğinden çalışma süresini daha fazla azaltır.

Raf dışı bir uygulamanın tek iş parçacığı veya çoklu iş parçacığı uygulama biçimini değiştiremeyebilirsiniz. Örneğin, SQL Server Çoklu CPU ve çok çekirdekli işleme yeteneğine sahiptir. Ancak, bir sorguyu işlemek için bir veya daha fazla iş parçacığından hangi koşullarda yararlanılacağı SQL Server karar verir. Birden çok iş parçacığı kullanarak sorguları çalıştırabilir ve dizinleri oluşturabilir. Büyük tablolara katılmayı ve verileri kullanıcıya döndürmeden önce sıralamayı içeren bir sorgu için SQL Server, büyük olasılıkla birden çok iş parçacığı kullanacaktır. Ancak, bir kullanıcı SQL Server tek bir iş parçacığı veya birden çok iş parçacığı kullanarak bir sorgu yürüttüğünü denetleyebilir.

Bu çoklu iş parçacığı oluşturmayı veya bir uygulamanın paralel işlemesini etkilemek için, değişiklik yaptığınız yapılandırma ayarları vardır. Örneğin, SQL Server olması durumunda paralellik yapılandırmanın en yüksek derecesi olur. MAXDOP adlı bu ayar, paralel işleme sırasında kullanabileceği en fazla SQL Server işlemci sayısını yapılandırmanıza olanak tanır. MAXDOP 'yi tekil sorgular veya dizin işlemleri için yapılandırabilirsiniz. Bu, performans açısından kritik bir uygulama için sisteminizin kaynaklarını dengelemek istediğinizde faydalıdır.

Örneğin, SQL Server kullanarak uygulamanızın aynı anda büyük bir sorgu ve dizin işlemi yürüttüğünü varsayalım. Dizin işleminin büyük sorguyla karşılaştırıldığında daha fazla performansa sahip olduğunu varsayalım. Böyle bir durumda, Dizin işleminin MAXDOP değerini sorgu için MAXDOP değerinden daha yüksek olacak şekilde ayarlayabilirsiniz. Bu şekilde SQL Server, dizin işlemi için, büyük sorguya ayırabildiğinden işlemci sayısıyla karşılaştırıldığında daha fazla sayıda işlemciye sahiptir. Her işlem için kullanılacak SQL Server iş parçacığı sayısını kontrol etmeyi unutmayın. Çoklu iş parçacığı için ayrılan en fazla işlemci sayısını kontrol edebilirsiniz.

SQL Server [paralellik dereceleri](https://technet.microsoft.com/library/ms188611.aspx) hakkında daha fazla bilgi edinin. Uygulamanızda çoklu iş parçacığı ve bunların yapılandırmasını etkileyen bu ayarları, performansı iyileştirmek için öğrenin.

## <a name="queue-depth"></a>Sıra derinliği

Sıra derinliği veya sıra uzunluğu veya sıra boyutu, sistemdeki bekleyen GÇ isteklerinin sayısıdır. Sıra derinliği değeri, uygulamanızın ne kadar GÇ işlemi olacağını, depolama disklerinin işleneceğini belirler. Bu makalede görselleştirme., ıOPS, aktarım hızı ve gecikme süresi açıklandığımız üç uygulama performansı göstergelerini etkiler.

Sıra derinliği ve çoklu iş parçacığı yakından ilgilidir. Sıra derinliği değeri, uygulama tarafından ne kadar çok iş parçacığına ulaşıldığını gösterir. Sıra derinliği büyükse, uygulama başka bir deyişle, daha fazla çoklu iş parçacığı gibi daha fazla işlemi eşzamanlı olarak yürütebilir. Sıra derinliği küçükse, uygulamanın çok iş parçacıklı olmasına rağmen, eşzamanlı yürütme için yeterli sayıda istek sıralanmaz.

Genellikle raf uygulamalarının devre dışı bırakıldığı sıra derinliğini değiştirmenize izin vermez, çünkü yanlış ayarlanmış bir şekilde ayarlandıysa daha fazla zarar verir. Uygulamalar en iyi performansı elde etmek için sıra derinliğinin doğru değerini ayarlayacaktır. Ancak, uygulamanızla ilgili performans sorunlarını giderebilmeniz için bu kavramın anlaşılması önemlidir. Ayrıca, sisteminizde benchişaretleme araçlarını çalıştırarak sıra derinliğinin etkilerini gözlemleyebilirsiniz.

Bazı uygulamalar sıra derinliğini etkilemek için ayarlar sağlar. Örneğin, önceki bölümde açıklanan SQL Server için MAXDOP (en fazla paralellik derecesi) ayarı. MAXDOP, SQL Server sıra derinliği değerini doğrudan değiştirmese de, sıra derinliğini ve çoklu iş parçacığı oluşturmayı etkileyen bir yoldur.

*Yüksek sıra derinliği*  
Yüksek bir sıra derinliği diskte daha fazla işlem daha yüksektir. Disk, sırasındaki Sıradaki isteği zaman önünde bilir. Sonuç olarak, disk, işlemleri zaman içinde zamanlayabilir ve bunları en uygun sırayla işleyebilir. Uygulama diske daha fazla istek gönderdiğinden, disk daha paralel IOs işleyebilir. Sonuç olarak, uygulama daha yüksek ıOPS elde edebilir. Uygulama daha fazla istek yaptığından, uygulamanın toplam verimi da artar.

Genellikle, bir uygulama, bağlı disk başına 8-16 + bekleyen IOs ile maksimum Işleme elde edebilir. Sıra derinliği bir ise, uygulama sisteme yeterli sayıda IOs itimez ve belirli bir dönemde daha az miktarda işlem görür. Diğer bir deyişle, daha az üretilen Iş.

Örneğin, SQL Server, bir sorgu için MAXDOP değerinin "4" olarak ayarlanması, sorguyu yürütmek için en fazla dört çekirdekli SQL Server bildirir. SQL Server en iyi sıra derinliği değerini ve sorgu yürütme için çekirdek sayısını belirlemektir.

*En iyi sıra derinliği*  
Çok yüksek sıra derinliği değerinin de dezavantajları vardır. Sıra derinliği değeri çok yüksekse, uygulama çok yüksek ıOPS 'yi yeniden dener. Uygulamanın yeterli sağlanan ıOPS 'ye sahip kalıcı diskleri yoksa, bu uygulama gecikmeleri olumsuz etkileyebilir. Aşağıdaki formül ıOPS, gecikme süresi ve sıra derinliği arasındaki ilişkiyi gösterir.  
    ![](media/premium-storage-performance/image6.png)

Sıra derinliğini herhangi bir yüksek değere, ancak en uygun değere göre yapılandırmamalısınız. Bu, gecikme sürelerini etkilemeden uygulama için yeterli ıOPS sunabilmelidir. Örneğin, uygulama gecikmesi 1 milisaniyeye ihtiyaç duyuyorsa, 5.000 ıOPS elde etmek için gereken sıra derinliği, QD = 5000 x 0,001 = 5 olur.

*Şeritli birim için sıra derinliği*  
Şeritli bir birim için, her diskin ayrı ayrı sıra derinliğine sahip olduğu için yüksek miktarda sıra derinliği saklayın. Örneğin, 2. sıra derinliğini veren bir uygulamayı düşünün ve Stripe içinde dört disk vardır. İki GÇ isteği iki diske gider ve kalan iki disk boşalacak. Bu nedenle, tüm disklerin meşgul olması için sıra derinliğini yapılandırın. Aşağıdaki formülde, şeritli birimlerin sıra derinliğini belirleme gösterilmektedir.  
    ![](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>Azaltma

Azure Premium Depolama, seçtiğiniz VM boyutlarına ve disk boyutlarına göre belirtilen ıOPS ve aktarım hızı sayısını sağlar. Uygulamanız her zaman VM 'nin veya diskin işleyebileceği Bu limitlerin üzerinde ıOPS veya aktarım hızını sorgulamaya çalıştığında, Premium depolama alanı kısıtlayıp azalmasını sağlar. Bu bildirimler uygulamanızdaki performans düşüklüğü biçiminde. Bu, daha yüksek gecikme süresi, düşük aktarım hızı veya düşük ıOPS anlamına gelebilir. Premium Depolama kısıtlama uygulamamılamaz, uygulamanız kaynakları elde edebilen bir süreyi aşarak tamamen başarısız olabilir. Bu nedenle, azaltma nedeniyle performans sorunlarından kaçınmak için her zaman uygulamanız için yeterli kaynak sağlayın. Yukarıdaki VM boyutları ve disk boyutları bölümünde açıklandığımız şeyleri dikkate alın. Sınama, uygulamanızı barındırmak için hangi kaynakların gerekli olacağını belirlemenin en iyi yoludur.

## <a name="next-steps"></a>Sonraki adımlar

