---
title: StorSimple 8000 serisi cihazınızı izleyin
description: StorSimple Device Manager hizmetinin kullanımı, g/ç performansını ve kapasite kullanımını izlemek için nasıl kullanılacağını açıklar.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/17/2017
ms.author: alkohli
ms.openlocfilehash: de4cae90f2cc1223271672d9fc5ba5b42e4409e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94962442"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-your-storsimple-device"></a>StorSimple cihazınızı izlemek için StorSimple Device Manager hizmetini kullanın

## <a name="overview"></a>Genel Bakış
StorSimple çözümünüzün içindeki belirli cihazları izlemek için StorSimple Device Manager hizmetini kullanabilirsiniz. G/ç performansı, kapasite kullanımı, ağ aktarım hızı ve cihaz performans ölçümlerini temel alan özel grafikler oluşturabilir ve bunları panoya sabitleyebilirsiniz. Daha fazla bilgi için [Portal panonuzu özelleştirme](../azure-portal/azure-portal-dashboards.md)bölümüne gidin.

Belirli bir cihazın izleme bilgilerini görüntülemek için Azure portal, StorSimple Device Manager hizmetini seçin. Cihaz listesinden cihazınızı seçin ve ardından **izleyici**'ye gidin. Daha sonra seçili cihaz için **kapasiteyi**, **kullanımı** ve **performans** grafiklerini görebilirsiniz.

## <a name="capacity"></a>Kapasite
**Kapasite** , sağlanan alanı ve cihazda kalan alanı izler. Kalan kapasite daha sonra yerel olarak sabitlenmiş veya katmanlı olarak görüntülenir.

Sağlanan ve kalan kapasite katmanlı ve yerel olarak sabitlenmiş birimler tarafından daha fazla bölünür. Her birim için, sağlanan kapasite ve cihazdaki kalan kapasite görüntülenir.

![GÇ kapasitesi](./media/storsimple-8000-monitor-device/device-capacity.png)



## <a name="usage"></a>Kullanım
**Kullanım** , birimler, birim kapsayıcıları veya cihaz tarafından kullanılan veri depolama alanı miktarıyla ilgili ölçümleri izler. Birincil depolama alanınızı, bulut depolama alanınızı veya cihaz depolama alanınızı kullanma kapasitesini temel alan raporlar oluşturabilirsiniz. Kapasite kullanımı belirli bir birimde, belirli bir birim kapsayıcısında veya tüm birim kapsayıcılarında ölçülebilir.
Varsayılan olarak, son 24 saatin kullanımı raporlanır. Şu kaynaktan seçim yaparak, kullanımın bildirildiği süreyi değiştirmek için grafiği düzenleyebilirsiniz:
* Son 24 saat
* Son 7 gün
* Son 30 gün
* Son 90 gün
* Geçen yıl

Kullanım grafikleri için iki anahtar metrıces, büyüme ve Aralık raporlanır. Aralık, seçilen süre (Info örneği, son 7 gün) üzerinde raporlanan en büyük değeri ve kullanım en düşük değerlerini ifade eder.

Büyüme, kullanımdaki sürenin son gününe kadar olan kullanımdaki artışı ifade eder. 

Büyüme ve Aralık aşağıdaki denklemlerle de gösterilebilir:

```
Range = {Usage(minimum), Usage(maximum)}

Growth = Usage(Last day) - Usage(first day)

Growth (%) = [{Usage(last day) - Usage(first day)} X 100]/Usage(first day)
```

Kullanılan birincil, bulut ve yerel depolama aşağıdaki gibi açıklanabilir:

### <a name="primary-storage-usage"></a>Birincil depolama alanı kullanımı
Bu grafiklerde, veriler silinmeden ve sıkıştırmadan önce StorSimple birimlerine yazılan veri miktarı gösterilmektedir. Bir birim kapsayıcısındaki veya tek bir birimde bulunan tüm birimler tarafından kullanılan birincil depolamayı görüntüleyebilirsiniz. Kullanılan birincil depolama alanı, kullanılan birincil katmanlı depolama ve kullanılan birincil yerel olarak sabitlenmiş depolama tarafından daha fazla bölünmüştür.

Aşağıdaki grafiklerde bir StorSimple cihazı için bir bulut anlık görüntüsü alınmadan önce ve sonra kullanılan birincil depolama gösterilmektedir. Bu yalnızca birim verileri olduğundan, bir bulut anlık görüntüsünün birincil depolamayı değiştirmemelidir. Görebileceğiniz gibi, grafik, bulut anlık görüntüsü alma sonucu olarak kullanılan birincil katmanlı veya yerel olarak sabitlenmiş depolamada fark göstermez. Bulut anlık görüntüsü bu cihazda 11:50 PM 'de başladı.

![Bulut anlık görüntüsünden sonra birincil kapasite kullanımı](./media/storsimple-8000-monitor-device/device-primary-storage-after-cloudsnapshot.png)

StorSimple cihazınıza bağlı olan konakta artık GÇ çalıştırırsanız, birincil katmanlı depolamada veya verileri hangi birimlerin (katmanlı veya yerel olarak sabitlenmiş) bağlı olarak kullanılan birincil yerel olarak sabitlenmiş depolamanın artışını görürsünüz. StorSimple cihazının birincil depolama kullanım grafikleri aşağıda verilmiştir. Bu cihazda, StorSimple ana bilgisayarı, cihazdaki katmanlı bir birimde 2:30: etrafında yazma işlemleri sunmaya başladı. En yüksek değeri, konakta çalışan GÇ 'ye karşılık gelen yazma bayt/sn cinsinden görebilirsiniz.

![Katmanlı birimlerde çalışan GÇ performansı](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

Kullanılan birincil katmanlı depolama bölümüne bakarsanız, bu durumda yerel olarak sabitlenmiş kullanım, cihazdaki yerel olarak sabitlenmiş birimlere sunulan bir yazma işlemi olmadığından değişmeden kalır.

![Katmanlı birimlerde GÇ çalışırken birincil kapasite kullanımı](./media/storsimple-8000-monitor-device/device-primary-storage-io-from-initiator.png)

Güncelleştirme 3 veya üzeri bir sürümü çalıştırıyorsanız, birincil depolama kapasitesi kullanımını tek bir birim, tüm birimler, katmanlı birimler ve aşağıda gösterildiği gibi yerel olarak sabitlenmiş tüm birimlerde azaltabilirsiniz. Tüm yerel olarak sabitlenmiş birimlerin bölünmesi, yerel katmanın ne kadarının kullanıldığını hızlı bir şekilde değerlendirmenize olanak tanır.

![Tüm katmanlı birimlerde birincil kapasite kullanımı](./media/storsimple-8000-monitor-device/monitor-usage3.png)

![Yerel olarak sabitlenmiş tüm birimlerde birincil kapasite kullanımı](./media/storsimple-8000-monitor-device/monitor-usage4.png)

Listedeki birimlerin her birine daha fazla tıklayabilir ve ilgili kullanımı görebilirsiniz.

![Yerel olarak sabitlenmiş tüm birimler için birincil kapasite kullanımı 2](./media/storsimple-8000-monitor-device/device-primary-storage-usage-by-volume.png)

### <a name="cloud-storage-usage"></a>Bulut depolama alanı kullanımı
Bu grafiklerde kullanılan bulut depolama alanı miktarı gösterilmektedir. Bu veriler yinelenenleri kaldırılmış ve sıkıştırılır. Bu miktar, herhangi bir birincil birimde yansıtılmayan ve eski veya gerekli saklama amaçlarıyla tutulan veriler içerebilen bulut anlık görüntülerini içerir. Birincil ve bulut depolama tüketimi rakamlarını, veri azaltma oranının bir fikrini alacak şekilde karşılaştırabilir, ancak sayı tam olarak olmaz.

Aşağıdaki grafiklerde, bir bulut anlık görüntüsü çekilirken bir StorSimple cihazının bulut depolama kullanımı gösterilmektedir.

* Bulut anlık görüntüsü bu cihazda 11:50 ' den başlayarak başlatılır ve bulut anlık görüntüsünden önce, kullanılan bulut depolama olmadığını görebilirsiniz. 
* Bulut anlık görüntüsü tamamlandığında, bulut depolaması kullanımı 0,89 GB 'yi kullanıma çekirsiniz. 
* Bulut anlık görüntüsü devam ederken, cihazdan buluta kadar GÇ 'ye karşılık gelen bir tepe değeri de vardır.

    ![Bulut anlık görüntüsünden önce bulut depolama alanı kullanımı](./media/storsimple-8000-monitor-device/device-cloud-storage-before-cloudsnapshot.png)

    ![Bulut anlık görüntüsünden sonra bulut depolama kullanımı](./media/storsimple-8000-monitor-device/device-cloud-storage-after-cloudsnapshot.png)

    ![Bulut anlık görüntüsü sırasında cihazdan buluta GÇ](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)


### <a name="local-storage-usage"></a>Yerel depolama alanı kullanımı
Bu grafikler, SSD doğrusal katmanını içerdiğinden, birincil depolama kullanımından daha fazla olacak şekilde cihazın toplam kullanımını gösterir. Bu katman, cihazın diğer katmanlarında de bulunan veri miktarını içerir. SSD doğrusal katmanındaki kapasite, yeni veriler geldiği zaman, eski verilerin HDD katmanına taşınması (Bu sırada yinelenenleri kaldırılmış ve sıkıştırıldığı) ve daha sonra buluta taşınabilmesini sağlamak için de kullanılır.

Zaman içinde, kullanılan birincil depolama alanı ve kullanılan yerel depolama büyük olasılıkla veriler buluta katmanlanana kadar artmaya başlar. Bu noktada, kullanılan yerel depolama büyük olasılıkla Plateau 'ya başlar, ancak kullanılan birincil depolama alanı daha fazla veri yazıldığı için artacaktır.

Aşağıdaki grafiklerde, bir bulut anlık görüntüsü çekilirken StorSimple cihazı için kullanılan birincil depolama gösterilmektedir. Bulut anlık görüntüsü 11:50 ' de ve yerel depolama alanı bu zamanda azaltılmış olarak başladı. Kullanılan yerel depolama alanı 1,445 GB 'den 1,09 GB 'a kadar oldu. Bu, doğrusal SSD katmanındaki sıkıştırılmamış verilerin büyük olasılıkla yinelendiğini, sıkıştırıldığını ve HDD katmanına taşındığını gösterir. Cihazda zaten SSD ve HDD katmanlarında büyük miktarda veri varsa, bu azalmayı görmeyebilirsiniz. Bu örnekte, cihazda az miktarda veri bulunur.

![Bulut anlık görüntüsünden sonra yerel depolama kullanımı](./media/storsimple-8000-monitor-device/device-local-storage-after-cloudsnapshot.png)

## <a name="performance"></a>Performans
**Performans** , ana bilgisayar sunucusundaki iSCSI başlatıcısı arabirimleri ile cihaz ya da cihaz ve bulut arasındaki okuma ve yazma işlemleri sayısıyla ilgili ölçümleri izler. Bu performans, belirli bir birim, belirli bir birim kapsayıcısı veya tüm birim kapsayıcıları için ölçülebilir. Performans Ayrıca, cihazınızdaki çeşitli ağ arabirimlerinin CPU kullanımını ve ağ aktarım hızını da içerir.

### <a name="io-performance-for-initiator-to-device"></a>Başlatıcıdan cihaza GÇ performansı
Aşağıdaki grafik, bir üretim cihazının tüm birimleri için, cihazınıza başlatıcıdan g/ç 'yi gösterir. Çizili ölçümler, saniye başına okuma ve yazma bayttır. Ayrıca, okuma, yazma, bekleyen GÇ veya okuma ve yazma gecikme sürelerini grafik olarak da kullanabilirsiniz.

![Başlatıcıdan cihaza GÇ performansı](./media/storsimple-8000-monitor-device/device-io-from-initiator.png)

### <a name="io-performance-for-device-to-cloud"></a>Cihazdan buluta g/ç performansı
Aynı cihaz için, tüm birim kapsayıcıları için cihazdan buluta veri için g/ç işlemleri çizilir. Bu cihazda, veriler yalnızca doğrusal katmanda bulunur ve buluta hiçbir şey taşmıştır. Cihazdan buluta gerçekleştirilen okuma/yazma işlemleri yok. Bu nedenle, grafikteki en üst öğeler 5 dakikalık bir aralıktır ve bu, cihaz ile hizmet arasında sinyalin denetlenme sıklığa karşılık gelir.

Aynı cihaz için, 11:50 ile başlayan birim verileri için bir bulut anlık görüntüsü alınmıştır. Bu, aygıttan buluta akan veri akışını sonuçlanmış. Yazma işlemleri bu süre içinde buluta sağlandı. GÇ grafiğinde, anlık görüntünün alındığı zamana karşılık gelen yazma baytları/sn cinsinden bir tepe gösterilmektedir.

![Bulut anlık görüntüsü sırasında cihazdan buluta GÇ](./media/storsimple-8000-monitor-device/device-io-to-cloud-during-cloudsnapshot.png)

### <a name="network-throughput-for-device-network-interfaces"></a>Cihaz ağ arabirimleri için ağ aktarım hızı
**Ağ aktarım hızı** , Konak sunucusundaki ve cihazdaki ve cihaz ile bulut arasındaki iSCSI başlatıcısı ağ arabirimlerinden aktarılan veri miktarı ile ilgili ölçümleri izler. Bu ölçümü, cihazınızdaki her Iscsı ağ arabirimi için izleyebilirsiniz.

Aşağıdaki grafiklerde, cihazınızdaki bulut özellikli (varsayılan) ve Iscsı özellikli veri 0, 1 1 GbE ağı için ağ aktarım hızı gösterilmektedir. Bu cihazda 14 Haziran 'da 9 Haziran 'da, veriler buluta katmanlanmış (Bu durumda, verileri buluta taşımak için gereken bir bulut anlık görüntüsü yoktur) ve bu da, GÇ 'nin buluta sunulduğunu gösterir. Ağ aktarım hızı grafiğinde aynı süre için karşılık gelen bir tepe noktası vardır ve ağ trafiğinin çoğu buluta giden bir işlem olur.

![Veriler için ağ aktarım hızı 0](./media/storsimple-8000-monitor-device/device-network-throughput-data0.png)

Veri 1 arabirim üretilen iş grafiğine baktığımızda, yalnızca Iscsı özellikli başka bir 1 GbE ağ arabirimine bakıyoruz, bu süre içinde neredeyse hiçbir ağ trafiği yoktu.

![Veri 1 için ağ aktarım hızı](./media/storsimple-8000-monitor-device/device-network-throughput-data1.png)


## <a name="cpu-utilization-for-device"></a>Cihaz için CPU kullanımı
**CPU kullanımı** , CIHAZıNıZDA kullanılan CPU ile ilgili ölçümleri izler. Aşağıdaki grafikte, üretimde bir cihaz için CPU kullanım istatistikleri gösterilmektedir.

![Cihaz için CPU kullanımı](./media/storsimple-8000-monitor-device/device-cpu-utilization.png)



## <a name="next-steps"></a>Sonraki adımlar
* [StorSimple Device Manager hizmeti cihaz panosunu kullanmayı](./storsimple-8000-device-dashboard.md)öğrenin.
* StorSimple [cihazınızı yönetmek Için storsimple Device Manager hizmetini nasıl kullanacağınızı](./storsimple-8000-manager-service-administration.md)öğrenin.