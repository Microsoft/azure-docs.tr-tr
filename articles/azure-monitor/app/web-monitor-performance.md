---
title: Application Insights ile uygulamanızın sistem durumunu ve kullanımını izleyin
description: Application Insights kullanmaya başlayın. Şirket içi veya Microsoft Azure uygulamalarınızın kullanımını, kullanılabilirliğini ve performansını analiz edin.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/10/2018
ms.reviewer: sdash
ms.openlocfilehash: ebf6fa6d3dac6c63dfaa349a77a08bc81d402ef8
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899295"
---
# <a name="monitor-performance-in-web-applications"></a>Web uygulamalarının performansını izleme


Uygulamanızın iyi bir şekilde çalıştığından emin olun ve tüm hatalarda hızlıca bilgi edinin. [Application Insights][start] , herhangi bir performans sorunu ve özel durumu hakkında bilgi verir ve temel nedenleri bulmanıza ve tanılamanıza yardımcı olur.

Application Insights hem Java hem de ASP.NET Web uygulamaları ve Hizmetleri, WCF hizmetlerini izleyebilir. Şirket içinde, sanal makinelerde veya Microsoft Azure Web siteleri olarak barınabilir. 

İstemci tarafında, Application Insights Web sayfalarından telemetri alabilir ve iOS, Android ve Windows Mağazası uygulamaları dahil olmak üzere çok çeşitli cihazlar olabilir.

## <a name="setup"></a>Performans izlemeyi ayarlama
Projenize henüz Application Insights eklemediyseniz (yani, ApplicationInsights. config yoksa), başlamak için şu yollarla birini seçin:

* [ASP.NET Web Apps](../../azure-monitor/app/asp-net.md)
  * [Özel durum izleme ekleme](../../azure-monitor/app/asp-net-exceptions.md)
  * [Bağımlılık izleme ekleme](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Java EE Web uygulamaları](../../azure-monitor/app/java-get-started.md)
  * [Bağımlılık izleme ekleme](../../azure-monitor/app/java-agent.md)

## <a name="view"></a>Performans ölçümlerini keşfetme
[Azure Portal](https://portal.azure.com), uygulamanız için ayarladığınız Application Insights kaynağına gidin. Genel Bakış dikey penceresinde temel performans verileri gösterilmektedir:

Daha fazla ayrıntı görmek için herhangi bir grafiğe tıklayın ve daha uzun bir süre için sonuçları görün. Örneğin, Istekler kutucuğuna tıklayın ve sonra bir zaman aralığı seçin:

![Daha fazla veri için tıklayın ve bir zaman aralığı seçin](./media/web-monitor-performance/appinsights-48metrics.png)

Hangi ölçümlerin görüntülediğini seçmek için bir grafiğe tıklayın veya yeni bir grafik ekleyin ve onun ölçümlerini seçin:

![Ölçümleri seçmek için bir grafiğe tıklayın](./media/web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> Kullanılabilir tam seçimi görmek için **Tüm ölçümlerin Işaretini kaldırın** . Ölçümler gruplar halinde yer almalıdır; bir grubun herhangi bir üyesi seçildiğinde, yalnızca o grubun diğer üyeleri görünür.

## <a name="metrics"></a>Hepsi ne anlama geliyor? Performans kutucukları ve raporları
Alabileceğiniz çeşitli performans ölçümleri vardır. Uygulama dikey penceresinde varsayılan olarak görünenler ile başlayalım.

### <a name="requests"></a>İstekler
Belirtilen bir dönemde alınan HTTP isteklerinin sayısı. Yük değiştiğinden uygulamanızın nasıl davranacağını görmek için bunu diğer raporlardaki sonuçlarla karşılaştırın.

HTTP istekleri, sayfalar, veriler ve görüntüler için tüm GET veya POST isteklerini içerir.

Belirli URL 'Lerin sayısını almak için kutucuğa tıklayın.

### <a name="average-response-time"></a>Ortalama yanıt süresi
Uygulamanızı ve döndürülen yanıtı girerek bir Web isteği arasındaki süreyi ölçer.

Noktalara bir hareketli ortalama gösterilmektedir. Çok sayıda istek varsa, grafikte belirgin bir tepe veya DIP olmadan ortalama olan bir miktar olabilir.

Olağan dışı tepe noktaları arayın. Genel olarak, yanıt süresinin artmasıyla bir artış olması beklenir. Artorantısız ise, uygulamanız CPU veya kullandığı bir hizmetin kapasitesi gibi bir kaynak sınırına ulaşmalıdır.

Belirli URL 'Lerin zamanlarını almak için kutucuğa tıklayın.

![](./media/web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>En yavaş istekler
![](./media/web-monitor-performance/appinsights-44slowest.png)

Hangi isteklerin performans ayarlamaya ihtiyacı olabileceğini gösterir.

### <a name="failed-requests"></a>Başarısız istekler
![](./media/web-monitor-performance/appinsights-46failed.png)

Yakalanmayan özel durumları oluşturan istek sayısı.

Belirli hataların ayrıntılarını görmek için kutucuğa tıklayın ve ayrıntılarını görmek için tek bir istek seçin. 

Bireysel İnceleme için yalnızca temsili bir başarısızlık örneği tutulur.

### <a name="other-metrics"></a>Diğer ölçümler
Hangi diğer ölçümleri görebileceğinize bakmak için bir grafiğe tıklayın ve ardından tüm ölçümler seçimini kaldırın ve tüm kullanılabilir kümesini görüntüleyin. Her ölçümün tanımını görmek için (i) seçeneğine tıklayın.

![Tüm kümeyi görmek için tüm ölçümlerin seçimini kaldırın](./media/web-monitor-performance/appinsights-62allchoices.png)

Herhangi bir ölçümü seçmek, aynı grafikte görünmeyen diğerlerini devre dışı bırakır.

## <a name="set-alerts"></a>Uyarılar ayarlama
Herhangi bir ölçümün olağandışı değerlerinin e-postasından haberdar olmak için bir uyarı ekleyin. E-postayı hesap yöneticilerine veya belirli e-posta adreslerine göndermek üzere birini seçebilirsiniz.

![](./media/web-monitor-performance/appinsights-413setMetricAlert.png)

Kaynağı diğer özelliklerden önce ayarlayın. Performans veya kullanım ölçümlerinde uyarı ayarlamak istiyorsanız WebTest kaynaklarını seçmeyin.

Eşik değerini girmeniz istenecek birimleri göz önünde bulunmamaya dikkat edin.

*Uyarı Ekle düğmesini görmüyorum.* -Bu, salt okuma erişiminizin bulunduğu bir grup hesabıdır misiniz? Hesap yöneticisiyle görüşün.

## <a name="diagnosis"></a>Sorunları tanılama
Performans sorunlarını bulmak ve tanılamak için birkaç ipucu aşağıda verilmiştir:

* Web siteniz daha sonra yanlış veya yavaş yanıt verirse, [Web testlerini][availability] uyarılmak üzere ayarlayın. 
* Hataların veya yavaş yanıtın yükleme ile ilgili olup olmadığını görmek için Istek sayısını diğer ölçümlerle karşılaştırın.
* Kodunuzun sorunlarını belirlemenize yardımcı olması için kodunuzda [Trace deyimlerini ekleyin ve arama][diagnostic] yapın.
* [Canlı ölçüm akışı][livestream]Web uygulamanızı işlem sırasında izleyin.
* [Snapshot Debugger][snapshot]ile .net uygulamanızın durumunu yakalayın.

## <a name="find-and-fix-performance-bottlenecks-with-performance-investigation-experience"></a>Performans araştırma deneyimiyle performans sorunlarını bulun ve onarın

Web uygulamanızda yavaş gerçekleştirilen işlemleri gözden geçirmek için performans araştırma deneyimini kullanabilirsiniz. Hızlı bir şekilde belirli bir yavaş işlemi seçebilir ve temel işlem için [Profil Oluşturucu](../../azure-monitor/app/profiler.md) kullanarak yavaş işlemleri koda düşürür. Seçilen işlem için gösterilen yeni süre dağıtımını kullanarak hızlı bir şekilde, yalnızca bir deneyimin müşterileriniz için ne kadar kötü olduğunu değerlendirirsiniz. Her bir yavaş işlem için Kullanıcı etkileşimlerinizin kaç tane etkilendiğinin nasıl etkileneceği hakkında bilgi alabilirsiniz. Aşağıdaki örnekte, müşteriler/Ayrıntılar alma deneyiminden daha yakından göz atalım. Süre dağıtımında, üç ani artışlar olduğunu görebiliriz. En soldaki ani artış 400 MS 'den ve çok fazla yanıt veren deneyimi temsil etmektedir. Orta artış 1,2 ' in etrafında ve bir mediocre deneyimini temsil etmektedir. Son olarak 3,6 s ' de, müşterilerimizin karşılanmamasına neden olan 99. yüzdebirlik deneyimini temsil eden bir küçük ani artış vardır. Bu deneyim, aynı işlem için harika deneyimden on kat daha yavaştır. 

![Müşterileri/ayrıntıları al üç süreli ani artışlar](./media/web-monitor-performance/PerformanceTriageViewZoomedDistribution.png)

Bu işleme yönelik kullanıcı deneyimlerinden daha iyi bir fikir almak için daha büyük bir zaman aralığı seçebilirsiniz. Daha sonra işlemin yavaş olduğu belirli bir zaman penceresinde de dar bir süre sonra daraltabiliriz. Aşağıdaki örnekte, varsayılan 24 saatlik zaman aralığından 7 gün zaman aralığına geçtik ve 12:47 9:47 sonra 12. ve 13.6. ve 13. Hem süre dağılımı hem de örnek ve profil oluşturucu izlemelerinin sayısı sağ tarafta güncelleştirilmiştir.

![Zaman penceresi ile 7 gün aralığında müşterileri/ayrıntıları üç süre ani artışları alın](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrend.png)

Yavaş deneyimler üzerinde daraltmak için, daha sonra 95 ' i ve 99. yüzdebirlik aralığında kalan süreleri yakınlaştırıyoruz. Bunlar, yavaş olan kullanıcı etkileşimlerinin %4 ' i temsil eder.

![Zaman penceresi ile 7 gün aralığında müşterileri/ayrıntıları üç süre ani artışları alın](./media/web-monitor-performance/PerformanceTriageView7DaysZoomedTrendZoomed95th99th.png)

Artık, örnek düğmesine tıklayarak ya da profil oluşturucu izlemeleri düğmesine tıklayarak temsil eden profil oluşturucu izlemelerinde temsilci örneklerine bakabiliriz. Bu örnekte, zaman penceresinde müşterileri/ayrıntıları al için toplanan dört izleme vardır.

Bazen sorun kodunuzda olmaz, bunun yerine kodunuzun çağrı yaptığı bir bağımlılığa sahip olur. Bu tür yavaş bağımlılıkları araştırmak için performans önceliklendirme görünümündeki Bağımlılıklar sekmesine geçiş yapabilirsiniz. Varsayılan olarak, performans görünümü büyük ortalamalar olur, ancak gerçekten de bakmak istediğiniz değer, en fazla 95. yüzdebirlik (veya çok büyük ölçekli bir hizmeti izliyorsanız). Aşağıdaki örnekte, PUT fabrikamaccount ' ı çağırdığımız yavaş Azure BLOB bağımlılığına odaklandık. 40 ms çevresindeki iyi deneyimler sayesinde, aynı bağımlılığa yönelik yavaş çağrılar üç kez daha yavaştır ve 120 MS etrafında kümelenemez. Bu, ilgili işlemin önemli ölçüde yavaşlamasına neden olacak şekilde eklemek için bu çağrıların çoğunu almaz. Aynı şekilde, tıpkı Işlemler sekmesiyle yaptığınız gibi, temsilci örnekleri ve profil oluşturucu izlemelerinin detayına gidebilirsiniz.

![Zaman penceresi ile 7 gün aralığında müşterileri/ayrıntıları üç süre ani artışları alın](./media/web-monitor-performance/SlowDependencies95thTrend.png)

Performans araştırma deneyimi, odaklanmaya karar verdiğiniz örnek küme üzerinde ilgili öngörüleri gösterir. Tüm mevcut öngörülere baktığınızın en iyi yolu 30 günlük bir zaman aralığına geçiş yapmak ve sonra geçen aya ait tüm işlemlerde öngörüleri görmek için genel ' i seçbilmenizdir.

![Zaman penceresi ile 7 gün aralığında müşterileri/ayrıntıları üç süre ani artışları alın](./media/web-monitor-performance/Performance30DayOveralllnsights.png)


## <a name="next"></a>Sonraki adımlar
[Web testlerinde][availability] , dünyanın dört bir yanındaki düzenli aralıklarla uygulamanıza gönderilen Web istekleri vardır.

[Tanılama Izlemelerini yakalama ve arama][diagnostic] -izleme çağrıları ekleyin ve sonuçları sabitleme ile ilgili sorunları giderin.

[Kullanım izleme][usage] -kullanıcıların uygulamanızı nasıl kullandığını öğrenin.

[Sorun giderme][qna] ve soru-cevap &



<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[usage]: usage-overview.md
[livestream]: ../../azure-monitor/app/live-stream.md
[snapshot]: ../../azure-monitor/app/snapshot-debugger.md



