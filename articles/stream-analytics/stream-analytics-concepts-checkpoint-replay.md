---
title: Azure Stream Analytics denetim noktası ve yeniden yürütme kavramları
description: Bu makalede Azure Stream Analytics içindeki denetim noktası ve yeniden yürütme işi kurtarma kavramları açıklanmaktadır.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 26d8d8248c9dcc57edaaa4a90f87071ee61a70ce
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935040"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Azure Stream Analytics işlerinde denetim noktası ve yeniden yürütme kavramları
Bu makalede, Azure Stream Analytics ' deki iç denetim noktası ve yeniden yürütme kavramları ve bunların iş kurtarmasında etkileri açıklanmaktadır. Stream Analytics iş her çalıştığında durum bilgileri dahili olarak tutulur. Bu durum bilgileri düzenli aralıklarla bir denetim noktasına kaydedilir. Bazı senaryolarda, iş hatası veya yükseltme gerçekleşirse, iş kurtarma için denetim noktası bilgileri kullanılır. Başka bir durumda, denetim noktası kurtarma için kullanılamaz ve yeniden yürütme gereklidir.

## <a name="stateful-query-logicin-temporal-elements"></a>Zamana bağlı öğelerde durum bilgisi olan sorgu mantığı
Azure Stream Analytics işin benzersiz özelliğinden biri, pencereli toplamalar, zamana bağlı birleşimler ve zamana bağlı analitik işlevler gibi durum bilgisi olmayan bir işlem gerçekleştirmesinin bir özelliğidir. Bu işleçlerin her biri, iş çalıştırıldığında durum bilgilerini tutar. Bu sorgu öğeleri için en büyük pencere boyutu yedi gündür. 

Zamana bağlı pencere kavramı çeşitli Stream Analytics sorgu öğelerinde görünür:
1. Pencereli toplamalar (Tdönerek gruplama, atlamalı ve kayan pencereler)

2. Zamana bağlı birleşimler (DATEDIFF ile BIRLEŞTIRME)

3. Zamana bağlı analitik işlevler (ıSFIRST, LAST ve LIMIT SÜRESI ile GECIKME)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>İşletim sistemi yükseltmesi dahil olmak üzere düğüm hatasından iş kurtarma
Bir Stream Analytics işi her çalıştığında, dahili olarak birden çok çalışan düğümünde çalışacak şekilde ölçeklendirilir. Her çalışan düğümün durumu her birkaç dakikada bir denetim noktası olarak belirlenir ve bu da bir hata oluşursa sistemin kurtarılmasına yardımcı olur.

Her zaman, belirli bir çalışan düğümü başarısız olabilir veya çalışan düğümü için bir Işletim sistemi yükseltmesi meydana gelebilir. Otomatik olarak kurtarmak için, Stream Analytics yeni bir sağlıklı düğüm elde edin ve önceki çalışan düğümün durumu kullanılabilir en son denetim noktasından geri yüklenir. Çalışmayı sürdürdüğünüzde, durumu kontrol noktasının alındığı zamandan geri yüklemek için az miktarda yeniden yürütme gerekir. Genellikle geri yükleme boşluğu yalnızca birkaç dakikadır. İş için yeterli sayıda akış birimi seçildiğinde, yeniden yürütme işlemi hızlı tamamlanmalıdır. 

Tam paralel bir sorguda, bir çalışan düğümü hatasından sonra yakalamak için gereken süre şu şekilde orantılıdır:

[giriş olayı oranı] x [boşluk uzunluğu]/[işleme bölümlerinin sayısı]

Düğüm arızası ve işletim sistemi yükseltmesi nedeniyle önemli işlem gecikmesi gözlemlerseniz, sorguyu tamamen paralel hale getirmeyi düşünün ve işi daha fazla akış birimi ayırmaya göre ölçeklendirin. Daha fazla bilgi için bkz. [işlem hızını artırmak için Azure Stream Analytics bir Işi ölçeklendirme](stream-analytics-scale-jobs.md).

Geçerli Stream Analytics, bu tür bir kurtarma işlemi gerçekleşirken rapor göstermez.

## <a name="job-recovery-from-a-service-upgrade"></a>Hizmet yükseltmesinden iş kurtarma 
Microsoft, Azure hizmetindeki Stream Analytics işleri çalıştıran ikilileri zaman zaman yükseltir. Bu saatlerde, kullanıcıların çalışan işleri daha yeni bir sürüme yükseltilir ve iş otomatik olarak yeniden başlatılır. 

Şu anda, kurtarma denetim noktası biçimi yükseltmeler arasında korunmaz. Sonuç olarak, akış sorgusunun durumu tamamen yeniden yürütme tekniği kullanılarak geri yüklenmelidir. Stream Analytics işlerin daha önce aynı girişi yeniden çalıştırmasına izin vermek için, kaynak veriler için bekletme ilkesinin en azından Sorgunuzdaki pencere boyutlarına ayarlanması önemlidir. Bunun başarısız olması, hizmet yükseltmesi sırasında yanlış veya kısmi sonuçlara neden olabilir. bu nedenle, kaynak veriler tam pencere boyutunu içerecek kadar geri saklanmayabilir.

Genel olarak, gereken yeniden yürütme miktarı, ortalama olay oranı ile çarpılan pencere boyutuyla orantılıdır. Örnek olarak, saniye başına 1000 etkinlikten oluşan bir iş için, bir saatten büyük bir pencere boyutu büyük bir yeniden yürütme boyutuna sahip olacak şekilde değerlendirilir. Bir saatlik verilerin tam ve doğru sonuçlar üretebilmesi için, bu durum, bazı genişletilmiş bir süre için geciken çıkış (çıkış yok) oluşmasına neden olabilecek şekilde yeniden işlenmesi gerekebilir. `JOIN` veya `LAG`gibi, Windows veya diğer zamana bağlı işleçlere sahip sorgular sıfır yeniden yürütmeye sahip olur.

## <a name="estimate-replay-catch-up-time"></a>Tahmin yeniden yürütme yakalama süresi
Bir hizmet yükseltmesi nedeniyle gecikme süresini tahmin etmek için bu tekniği izleyebilirsiniz:

1. Sorgunuzun beklenen olay hızında en büyük pencere boyutunu kapsamak için, giriş olayı hub 'ını yeterli verilerle yükleyin. Etkinlik zaman damgası, canlı bir giriş akışımız gibi, bu süre boyunca o zaman içindeki duvar saati zamanına yakın olmalıdır. Örneğin, sorgunuzda 3 günlük bir pencere varsa olayları üç gün boyunca olay hub 'ına gönderin ve olayları gönderilmeye devam edin. 

2. İşi **Şimdi** başlangıç saati olarak kullanmaya başlayın. 

3. Başlangıç saati ile ilk çıktının oluşturulduğu zaman arasındaki süreyi ölçün. Bu süre, bir hizmet yükseltmesi sırasında işin ne kadar ertelendirileceğini kabaca doğurur.

4. Gecikme çok uzunsa, işinizi bölümleyip SUs sayısını artırarak yük daha fazla düğüme yayılmaya çalışın. Alternatif olarak, Sorgunuzdaki pencere boyutlarını azaltmayı göz önünde bulundurun ve aşağı akış havuzunda (örneğin, Azure SQL veritabanı kullanılarak) Stream Analytics işi tarafından oluşturulan çıktıda daha fazla toplama veya diğer durum bilgisi içeren işlem gerçekleştirin.

Görev açısından kritik işlerin yükseltilmesi sırasında genel hizmet kararlılığı sorunu için, eşlenmiş Azure bölgelerinde yinelenen işleri çalıştırmayı göz önünde bulundurun. Daha fazla bilgi için bkz. [hizmet güncelleştirmeleri sırasında iş güvenilirliğini garanti Stream Analytics](stream-analytics-job-reliability.md).

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>Kullanıcı tarafından başlatılan durdurma ve başlatmadan iş kurtarma
Bir akış işinde sorgu söz dizimini düzenlemek veya girişleri ve çıkışları ayarlamak için, değişiklikleri yapmak ve iş tasarımını yükseltmek için işin durdurulması gerekir. Bu tür senaryolarda, bir Kullanıcı akış işini durdurduğu ve yeniden başlattığı zaman, Kurtarma senaryosu hizmet yükseltmesine benzerdir. 

Denetim noktası verileri Kullanıcı tarafından başlatılan bir iş yeniden başlatması için kullanılamaz. Bu tür bir yeniden başlatma sırasında çıktının gecikmesini tahmin etmek için, önceki bölümde açıklanan yordamın aynısını kullanın ve gecikme çok uzunsa, benzer risk azaltma uygulayın.

## <a name="next-steps"></a>Sonraki adımlar
Güvenilirlik ve ölçeklenebilirlik hakkında daha fazla bilgi için şu makalelere bakın:
- [Öğretici: Azure Stream Analytics işleri için uyarıları ayarlama](stream-analytics-set-up-alerts.md)
- [Azure Stream Analytics işini, aktarım hızını artırmak için ölçeklendirin](stream-analytics-scale-jobs.md)
- [Hizmet güncelleştirmeleri sırasında iş güvenilirliğini Stream Analytics garanti edin](stream-analytics-job-reliability.md)
