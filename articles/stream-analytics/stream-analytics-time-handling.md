---
title: Azure Stream Analytics zaman işlemeyi anlama
description: En iyi başlangıç zamanını seçme, geç ve erken olayların nasıl işleneceği ve zaman işleme ölçümlerinin nasıl yapılacağı gibi Azure Stream Analytics işlemenin nasıl çalıştığını öğrenin.
author: jasonwhowell
ms.author: zhongc
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: ae53663f78b684d41c36607760e30db9e48a6434
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901415"
---
# <a name="understand-time-handling-in-azure-stream-analytics"></a>Azure Stream Analytics zaman işlemeyi anlama

Bu makalede, Azure Stream Analytics hizmetindeki pratik zaman işleme sorunlarını gidermek için nasıl tasarım seçimleri yapabileceğiniz ele alınmaktadır. Zaman işleme tasarım kararları olay sıralama faktörlerine yakından ilgilidir.

## <a name="background-time-concepts"></a>Arka plan saati kavramları

Tartışmayı daha iyi çerçeveetmek için bazı arka plan kavramları tanımlayalim:

- **Olay saati**: özgün olayın gerçekleştiği zaman. Örneğin, bir hareketli araba otoyol üzerinde bir stand yaklaşırsa.

- **İşleme süresi**: olayın işleme sistemine ulaştığı ve gözlemlendiği zaman. Örneğin, bir stand algılayıcısı arabayı gördüğünde ve bilgisayar sisteminin verileri işlemesi birkaç dakika sürer.

- **Filigran**: hangi nokta olaylarının akış işlemcisine geçtiğini gösteren bir olay zaman işaretleyicisi. Filigranlar, sistemin olayları geri almaya yönelik Temizleme ilerlemesini belirtebilmesine izin verir. Akışların doğası gereği, gelen olay verileri hiçbir şekilde durdurulmayacağından, Filigranlar akışta belirli bir noktaya yönelik ilerlemeyi gösterir.

   Filigran kavramı önemlidir. Filigranlar, sistemin geri çekilmesi gerekmeyen tamamen, doğru ve yinelenebilir sonuçlara ne zaman üreteceğini belirlemesine izin Stream Analytics verir. İşlem, öngörülebilir ve yinelenebilir olan garantili bir şekilde yapılabilir. Örneğin, bazı hata işleme koşullarınız için bir yeniden sayım yapılması gerekiyorsa, Filigranlar güvenli başlangıç ve bitiş noktalarıdır.

Bu konudaki ek kaynaklar olarak, bkz. Tyler Akibau 'nin blog gönderileri [akış 101](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-101) ve [akış 102](https://www.oreilly.com/ideas/the-world-beyond-batch-streaming-102).

## <a name="choosing-the-best-starting-time"></a>En iyi başlangıç zamanını seçme

Stream Analytics, kullanıcılara olay saati çekme için iki seçenek sunar:

1. **Varış saati**  

   Olay kaynağa ulaştığında giriş kaynağına varış saati atanır. Event Hubs girişleri için **Eventenqueuedutctime** özelliğini kullanarak, IoT Hub için **ıothub. enqueuedtime** özelliği ve BLOB girişi Için **blobproperties. LastModified** özelliğini kullanarak varış zamanına erişebilirsiniz.

   Varış zamanı kullanımı, varsayılan davranıştır ve en iyi şekilde veri arşivleme senaryolarında kullanılır; burada, hiç zamana bağlı mantık gerekmez.

2. **Uygulama saati** (olay saati olarak da adlandırılır)

   Uygulama saati, olay oluşturulduğunda atanır ve olay yükünün bir parçasıdır. Olayları uygulama zamanına göre işlemek için, select sorgusunda **timestamp by** yan tümcesini kullanın. **Zaman damgası by** yan tümcesi yoksa, olaylar varış süresine göre işlenir.

   Zamana bağlı mantık dahil edildiğinde yükte zaman damgası kullanılması önemlidir. Bu şekilde, kaynak sistemdeki veya ağdaki gecikmeler için hesap yapılabilir.

## <a name="how-time-progresses-in-azure-stream-analytics"></a>Azure Stream Analytics zaman içinde ilerleme

Uygulama süresi kullanılırken, zaman ilerleme durumu gelen olaylara göre belirlenir. Akış işleme sisteminin hiçbir olay olmadığını veya olayların geciktiremediğini bilmeleri zordur. Bu nedenle Azure Stream Analytics her giriş bölümü için aşağıdaki yollarla buluşsal filigranlar üretir:

1. Herhangi bir gelen olay olduğunda, bu, en büyük olay zamanının, bu nedenle, sipariş dışı tolerans penceresi boyutunun sonuna kadar olduğunu belirledik.

2. Gelen hiçbir olay olmadığında, bu sınır geçerli tahmini varış saati (bir giriş olayının son tarihinde olayları işleyen geçen süre ve giriş olayının varış süresi) eksi varış toleransı penceresini sona erdiğinde.

   Varış saati yalnızca tahmin edilebilir, çünkü Event Hubs gibi gerçek varış saati, olayları işleyen Azure Stream Analytics VM değil, giriş olay aracısı üzerinde oluşturulmuştur.

Tasarım, Filigranlar oluşturmanın yanı sıra iki ek amaca hizmet eder:

1. Sistem sonuçları, gelen olaylar ile veya olmadan zamanında bir şekilde oluşturur.

   Çıktı sonuçlarını görmek için ne kadar süre sonra kontrol edersiniz. Azure portal, Stream Analytics işinizin **olay sıralaması** sayfasında, **sıra dışı olayları** ayarını yapılandırabilirsiniz. Bu ayarı yapılandırırken, dakikliklerini 'in, olay akışındaki sıra dışı olayların toleransı ile olan ticaretlerini göz önünde bulundurun.

   Geç varış toleransı penceresi, gelen olayların yokluğunda bile filigran oluşturmaya devam etmek için önemlidir. Her zaman, bir olay giriş akışı seyrek olduğunda olduğu gibi, gelen olayların geldiği bir nokta olabilir. Bu sorun, giriş olay aracıda birden çok bölüm kullanılarak exacerbated.

   Bir geç varış toleransı penceresi olmadan akış veri işleme sistemleri, girişler seyrek olduğunda ve birden çok bölüm kullanıldığında gecikmeli çıkışlardan zarar verebilir.

2. Sistem davranışının tekrarlanabilir olması gerekmez. Yinelenebilirlik, akış veri işleme sisteminin önemli bir özelliğidir.

   Filigran, varış zamanı ve uygulama saatinden türetilir. Her ikisi de olay aracıda kalıcıdır ve bu nedenle yinelenebilir. Varış zamanının, olayların yokluğunda tahmini olması durumunda Azure Stream Analytics, yeniden yürütme sırasında yinelenebilirlik için tahmini varış süresini, hata kurtarma amacına karşı günlüğe günlüklere.

Olay saati olarak **varış süresini** kullanmayı seçtiğinizde, sipariş dışı toleransı ve geç varış toleransını yapılandırmaya gerek olmadığını unutmayın. **Varış zamanının** giriş olay aracısıdır tek parçalı olarak artırılmasından dolayı, Azure Stream Analytics yalnızca yapılandırmaların ayırt edilmesini sağlar.

## <a name="late-arriving-events"></a>Geç gelen olaylar

Her gelen olay için, geç varış toleransı penceresinin tanımına göre, Azure Stream Analytics **olay saatini** **varış süresi**ile karşılaştırır; Olay saati tolerans penceresinin dışındaysa, sistemi olayı bırakacak şekilde yapılandırabilir ya da olayın süresini tolerans dahilinde olacak şekilde ayarlayabilirsiniz.

Filigranların oluşturulduktan sonra, hizmetin olay zaman filigranından düşük olan olayları alabileceğinden emin olmanız gerekir. Hizmeti bu olayları **bırakacak** şekilde yapılandırabilir ya da olayın saatini filigran değerine göre **ayarlayabilirsiniz** .

Ayarlamanın bir parçası olarak, olayın **System. Timestamp** değeri yeni değere ayarlanır, ancak **olay zamanı** alanının kendisi değiştirilmez. Bu ayarlama, bir olayın **System. Timestamp** değerinin olay zamanı alanındaki değerden farklı olabileceği ve beklenmeyen sonuçların oluşturulmasına neden olabileceği tek durumdur.

## <a name="handling-time-variation-with-substreams"></a>Alt akışlarla zaman çeşitlemesini işleme

Burada açıklanan buluşsal filigran oluşturma mekanizması, zamanın çoğu zaman çeşitli olay gönderenler arasında eşitlenmiş olduğu durumlarda iyi bir şekilde çalışmaktadır. Ancak, özellikle birçok IoT senaryosunda, sistem, olay göndericlarındaki saat üzerinde çok az denetime sahiptir. Olay gönderenlerin her türlü cihaz, farklı donanım ve yazılım sürümlerinde olabilir.

Bir giriş bölümündeki tüm olaylara küresel bir filigran kullanmak yerine, Stream Analytics yardımcı olması için alt akışlar adlı başka bir mekanizmaya sahiptir. [**Zaman DAMGASı by**](/stream-analytics-query/timestamp-by-azure-stream-analytics) yan tümcesini ve **üzerine**anahtar sözcüğünü kullanan bir iş sorgusu yazarak, işinizdeki alt akışlardan yararlanabilirsiniz. Alt akışı belirlemek için, `deviceid` gibi, **Over** anahtar sözcüğünden sonra bir anahtar sütun adı sağlayın. böylece sistem bu sütuna zaman ilkeleri uygular. Her alt akış kendi bağımsız filigranını alır. Bu mekanizma, büyük saat ve Olay Gönderenler arasındaki ağ gecikmeleriyle ilgilenirken zamanında çıkış oluşturulmasına izin vermek için yararlıdır.

Alt akışlar Azure Stream Analytics tarafından sağlanan benzersiz bir çözümdür ve diğer akış veri işleme sistemleri tarafından sunulmaz. Stream Analytics, alt akışlar kullanıldığında, gelen olaylara geç varış toleransı penceresini uygular. Varsayılan ayar (5 saniye), daha fazla zaman damgalarına sahip cihazlar için büyük olasılıkla çok küçüktür. 5 dakikada bir başlangıç yapmanızı ve bunların cihaz saati eğriltme düzenine göre ayarlamalar yapmanızı öneririz.

## <a name="early-arriving-events"></a>Erken gelen olaylar

Erken varış süresi adlı bir kavramı fark etmiş olabilirsiniz. Bu, geç varış toleransı penceresinin tersi gibi görünür. Bu pencere 5 dakikada düzeltilir ve geç varış noktasından farklı bir amaca hizmet eder.

Azure Stream Analytics, her zaman tüm sonuçlar ürettiğinden, giriş süresini değil işin ilk çıkış saati olarak yalnızca **iş başlangıç saatini** belirtebilirsiniz. Yalnızca pencerenin ortasından değil, tam pencerenin işlenmesi için iş başlangıç zamanı gereklidir.

Stream Analytics sonra sorgu belirtiminden başlangıç saatini türetiliyor. Ancak, giriş olay Aracısı yalnızca varış süresine göre dizinlendiği için, sistemin başlangıç olayı saatini varış zamanı olarak çevirmesi gerekir. Sistem, giriş olay aracısıdır bu noktadan olayları işlemeye başlayabilir. Erken ulaşan pencere sınırı ile, çeviri basittir. Başlangıç olayı süresi, 5 dakikalık erken gelme penceresi. Bu hesaplama Ayrıca sistemin, olay zamanı 5 dakika, varış süresinden daha büyük olan tüm olayları düşürdüğü anlamına gelir.

Bu kavram, işlemenin nereden çıkış için başladıklarından bağımsız olarak işlemin tekrarlanabilir olduğundan emin olmak için kullanılır. Böyle bir mekanizma olmadan, diğer birçok akış sistemi tarafından talep ettikleri için yinelenebilirlik garantisi yoktur.

## <a name="side-effects-of-event-ordering-time-tolerances"></a>Olay sıralaması zaman toleransları 'nın yan etkileri

Stream Analytics işlerin birkaç **olay sırası** seçeneği vardır. Azure portal iki adet yapılandırılabilir: **sıra dışı olaylar** ayarı (sıra dışı tolerans) ve **geç ayarına ulaşan olaylar** (geç varış toleransı). **Erken varış** toleransı sabittir ve ayarlanamaz. Bu zaman ilkeleri, Stream Analytics tarafından güçlü garanti sağlamak için kullanılır. Ancak, bu ayarlar bazen beklenmeyen bazı etkilere sahiptir:

1. Yanlışlıkla çok erken olan olayları gönderme.

   Erken olayların normal şekilde outputolmaması gerekir. Gönderenin saati de çok hızlı çalışıyorsa, çıkışa erken olayların gönderilmesi mümkündür. Tüm erken gelen olaylar bırakılır, bu nedenle çıkışta bunlardan hiçbirini görmezsiniz.

2. Azure Stream Analytics tarafından işlenecek Event Hubs eski olayları gönderme.

   Eski olaylar ilk başta zararsız görünebilir, çünkü geç varış toleransı uygulaması nedeniyle, eski olaylar bırakılmış olabilir. Olaylar çok eski ise, **sistem. Timestamp** değeri olay alımı sırasında değiştirilir. Bu davranış nedeniyle şu anda Azure Stream Analytics geçmiş olay işleme senaryoları yerine neredeyse gerçek zamanlı olay işleme senaryoları için daha uygundur. Bazı durumlarda bu davranışa geçici bir çözüm bulmak için, **geç zamana ulaşan olayları** en büyük olası değere (20 gün) ayarlayabilirsiniz.

3. Çıktılar gecikiyor gibi görünüyor.

   İlk filigran, hesaplanan sürede oluşturulur: sistemin şimdiye kadar gözlemlediği **en uzun olay süresi** , sıra dışı tolerans penceresi boyutu. Varsayılan olarak, sıra dışı tolerans sıfır (00 dakika ve 00 saniye) olarak yapılandırılmıştır. Daha yüksek, sıfır olmayan bir zaman değeri olarak ayarlarsanız, akış işinin ilk çıktısı, hesaplanan ilk eşik zamanı nedeniyle bu süre (veya daha fazla) ile gecikilir.

4. Girişler seyrek.

   Belirli bir bölümde giriş olmadığında, **eşik zamanı geç** varış toleransı penceresi olarak hesaplanır. Sonuç olarak, giriş olayları seyrek ve seyrek olursa çıktı bu süre kadar geciktirilebilir. Geç değere **ulaşan varsayılan olaylar** 5 saniyedir. Örneğin, giriş olaylarını tek seferde gönderirken biraz gecikme görmeniz beklenir. Geç pencereye büyük bir değere **ulaşan olayları** ayarladığınızda gecikmeler daha kötüleştir.

5. **System. Timestamp** değeri, **Olay saati** alanındaki zamandan farklı.

   Daha önce açıklandığı gibi, sistem olay saatini sıra dışı tolerans veya geç varış toleransı pencereleri ile ayarlar. Olayın **System. Timestamp** değeri ayarlandı, ancak **Olay saati** alanı değil.

## <a name="metrics-to-observe"></a>Gözlemleyecek ölçümler

[Stream Analytics iş ölçümlerine](stream-analytics-monitoring.md)göre olay sıralaması zaman toleransı etkileri sayısını gözlemleyebilirsiniz. Aşağıdaki ölçümler geçerlidir:

|Ölçüm  | Açıklama  |
|---------|---------|
| **Sıra dışı olaylar** | Ayarlanmış olan veya ayarlanan bir zaman damgasına verilen, sıranın dışında alınan olay sayısını gösterir. Bu ölçüm, Azure portal işin **olay sıralaması** sayfasında **sıra dışı olaylar** ayarının yapılandırmasından doğrudan etkilenir. |
| **Geç giriş olayları** | Kaynaktan geç ulaşan olay sayısını gösterir. Bu ölçüm, bırakılmış olan veya zaman damgası ayarlanmış olan olayları içerir. Bu ölçüm, Azure portal işin **olay sıralaması** sayfasında geç ayarına **ulaşan olayların** yapılandırmasından doğrudan etkilenir. |
| **Erken giriş olayları** | Kaynağa bırakılmış olan kaynaktan erken ulaşan olay sayısını veya 5 dakikadan erken bir süre sonra zaman damgalarının ayarlandığını gösterir. |
| **Filigran gecikmesi** | Akış veri işleme işinin gecikmesini gösterir. Aşağıdaki bölümde daha fazla bilgi görüntüleyin.|

## <a name="watermark-delay-details"></a>Eşik gecikmesi ayrıntıları

**Filigran gecikmesi** ölçümü, işleme düğümünün duvar saati saati, şimdiye kadar gördüğü en büyük filigran olarak hesaplanır. Daha fazla bilgi için bkz. [filigran gecikmesi blog gönderisi](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/).

Bu ölçüm değerinin normal işlem altında 0 ' dan büyük olmasının birkaç nedeni olabilir:

1. Akış işlem hattının devralınan işleme gecikmesi. Normalde bu gecikme kabul edilir.

2. Sıra dışı tolerans penceresi, sınır toleransı penceresinin boyutuna göre azaltılacağı için gecikme getirmiştir.

3. Gecikme süresi, sınır toleransı penceresi tarafından azaltılacağı için gecikmeli olarak ortaya çıkarılmıştır.

4. Ölçüm üreten işleme düğümünün saat eğriliği.

Akış ardışık düzeninin yavaşlamasına neden olabilecek birçok farklı kaynak kısıtlaması vardır. Şu nedenlerle filigran gecikmesi ölçümü artabilirsiniz:

1. Giriş olaylarının hacmini işlemek için Stream Analytics 'de yeterli işlem kaynağı yok. Kaynakları ölçeklendirmek için bkz. [akış birimlerini anlama ve ayarlama](stream-analytics-streaming-unit-consumption.md).

2. Giriş olay aracılarında yeterli işleme yok, bu nedenle bunlar kısıtlanıyor. Olası çözümler için bkz. [Azure Event Hubs işleme birimlerini otomatik olarak ölçeklendirme](../event-hubs/event-hubs-auto-inflate.md).

3. Çıkış havuzları yeterince kapasiteye göre sağlanmamıştır, bu nedenle bunlar kısıtlanırlar. Olası çözümler, kullanılan çıkış hizmeti özelliğine göre farklılık gösterir.

## <a name="output-event-frequency"></a>Çıkış olayı sıklığı

Azure Stream Analytics, çıkış olayları oluşturmak için tek tetikleyici olarak filigran ilerlemesini kullanır. Filigran giriş verilerinden türetildiği için, hata kurtarma sırasında ve ayrıca Kullanıcı tarafından başlatılan yeniden işlemede yinelenebilir.

[Pencereli toplamalar](stream-analytics-window-functions.md)kullanılırken, hizmet yalnızca Windows sonunda çıktılar üretir. Bazı durumlarda, kullanıcılar Windows 'dan oluşturulan kısmi toplamaları görmek isteyebilir. Kısmi toplamalar Azure Stream Analytics Şu anda desteklenmiyor.

Diğer akış çözümlerinde, çıkış olayları, dış koşullara bağlı olarak çeşitli tetikleyici noktalarında gerçekleştirilmiş olabilir. Bazı çözümlerde belirli bir zaman penceresi için çıkış olaylarının birden çok kez üretilecektir. Giriş değerleri iyileştirilmesine göre, toplama sonuçları daha doğru olur. Olaylar ilk başta ve zaman içinde düzeltilebilir. Örneğin, belirli bir cihaz ağdan çevrimdışı olduğunda, bir sistem tarafından tahmini bir değer kullanılabilir. Daha sonra, aynı cihaz ağa çevrimiçi olarak gelir. Ardından, gerçek olay verileri giriş akışına eklenebilir. Çıkış, bu zaman penceresinin daha doğru çıkış üretmesinin sonucu.

## <a name="illustrated-example-of-watermarks"></a>Gösterilen filigranlar örneği

Aşağıdaki resimlerde, filigranların farklı koşullarda nasıl ilerlemesinin yapılacağı gösterilmektedir.

Bu tabloda aşağıdaki örnek veriler gösterilmektedir. Olay saatinin ve varış zamanının, bazen eşleşen ve bazen de değişebileceğini fark edebilirsiniz.

| Olay saati | Varış saati | DeviceID |
| --- | --- | --- |
| 12:07 | 12:07 | cihaz1
| 12:08 | 12:08 | cihaz2
| 12:17 | 12:11 | cihaz1
| 12:08 | 12:13 | device3
| 12:19 | 12:16 | cihaz1
| 12:12 | 12:17 | device3
| 12:17 | 12:18 | cihaz2
| 12:20 | 12:19 | cihaz2
| 12:16 | 12:21 | device3
| 12:23 | 12:22 | cihaz2
| 12:22 | 12:24 | cihaz2
| 12:21 | 12:27 | device3

Bu çizimde, aşağıdaki toleranslar kullanılır:

- Erken varış pencereleri 5 dakikadır
- Geç gelme penceresi 5 dakikadır
- Yeniden sıralama penceresi 2 dakikadır

1. Bu olaylar üzerinden ilerleyen filigranın çizimi:

   ![Azure Stream Analytics filigranı çizimi](media/stream-analytics-time-handling/WaterMark-graph-1.png)

   Önceki grafikte gösterilen önemli süreçler:

   1. İlk olay (Device1) ve ikinci olay (Device2) hizalı saatlere sahiptir ve Ayarlamasız işlenir. Her olay için filigran ilerler.

   2. Üçüncü olay (Device1) işlendiğinde varış saati (12:11) olay saatinden (12:17) önce gelir. Olay 6 dakika erken geldi, bu nedenle olay, 5 dakikalık erken varış toleransı nedeniyle bırakıldı.

      Bu, erken bir olayda bu durumda ilerleme yapmaz.

   3. Dördüncü olay (Device3) ve beşinci olay (Device1) hizalı saatlere sahiptir ve Ayarlamasız işlenir. Her olay için filigran ilerler.

   4. Altıncı olay (Device3) işlendiğinde, varış saati (12:17) ve olay saati (12:12), filigran düzeyinin altında. Olay saati su işareti düzeyine ayarlanır (12:17).

   5. On ikinci olay (Device3) işlendiğinde varış süresi (12:27) olay zamanından önce 6 dakikadır (12:21). Geç varış ilkesi uygulanır. Olay saati ayarlanır (12:22), 12:21 Bu, daha fazla ayarlama uygulanmaz.

2. Erken alma ilkesi olmadan ilerleyen filigranın ikinci çizimi:

   ![Erken ilke filigranı gösterimi Azure Stream Analytics](media/stream-analytics-time-handling/watermark-graph-2.png)

   Bu örnekte, hiçbir erken varış ilkesi uygulanmaz. Filigranı önemli ölçüde oluşturan aykırı olay olayları. Üçüncü olay (deviceId1 at 12:11) Bu senaryoda bırakılmadığından ve filigranın 12:15 olarak oluşturulduğuna dikkat edin. Dördüncü olay saati, bir sonuç olarak 7 dakika (12:08-12:15) olarak ayarlanır.

3. Son çizimde alt akışlar kullanılır (DeviceID ÜZERINDE). Birden çok filigran, her akış için bir tane izlenir. Sonuç olarak ayarlanan süreleriyle daha az olay vardır.

   ![Azure Stream Analytics alt akışlar filigranı çizimi](media/stream-analytics-time-handling/watermark-graph-3.png)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Stream Analytics olay sırası konuları](stream-analytics-out-of-order-and-late-events.md)
- [Stream Analytics iş ölçümleri](stream-analytics-monitoring.md)
