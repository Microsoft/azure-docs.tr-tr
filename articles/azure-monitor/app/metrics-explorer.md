---
title: Azure Application Insights ölçümleri keşfetme | Microsoft Docs
description: Ölçüm Gezgini 'nde grafikleri yorumlama ve Ölçüm Gezgini dikey pencerelerini özelleştirme.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 01/22/2019
ms.openlocfilehash: b0831ff500ba4cbe71dae6251fd960f6c96c0fe5
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820796"
---
# <a name="exploring-metrics-in-application-insights"></a>Application Insights ölçümleri keşfetme
[Application Insights][start] ölçümler, uygulamanızdan telemetri olarak gönderilen olayların ve sayımların ölçümleridir. Bunlar, performans sorunlarını tespit etmenize ve uygulamanızın nasıl kullanıldığı konusunda eğilimleri izlemenize yardımcı olur. Birçok standart ölçüm yelpazesi vardır ve ayrıca kendi özel ölçümlerinizi ve olaylarınızı da oluşturabilirsiniz.

> [!NOTE]
> Bu makalede, şu anda kullanım dışı olan ve sonunda kullanımdan kaldırılacak olan klasik Ölçüm Gezgini deneyimi açıklanmaktadır. [Bu makalede](../platform/metrics-charts.md)açıklanan yeni deneyimi kullanıma sunmanızı öneririz.

Ölçümler ve olay sayıları, toplamlar, ortalamalar veya sayımlar gibi toplanmış değerlerin grafiklerde görüntülenir.

Aşağıda örnek bir grafik kümesi verilmiştir:

![](./media/metrics-explorer/01-overview.png)

Ölçüm grafiklerini Application Insights portalında her yerde bulabilirsiniz. Çoğu durumda, özelleştirilebilir ve dikey pencereye daha fazla grafik ekleyebilirsiniz. Genel Bakış dikey penceresinde, daha ayrıntılı grafikler ("sunucular" gibi başlıklar içeren) için öğesine tıklayın veya özel grafikler oluşturabileceğiniz yeni bir dikey pencere açmak için **Ölçüm Gezgini** ' a tıklayın.

## <a name="time-range"></a>Zaman aralığı
Herhangi bir dikey penceredeki grafiklerin veya ızgaraların kapsadığı zaman aralığını değiştirebilirsiniz.

![Azure portal uygulamanızın genel bakış dikey penceresini açın](./media/metrics-explorer/03-range.png)

Henüz görüntülenmeyen bazı verileri bekliyorsanız Yenile ' ye tıklayın. Grafikler kendi başlarına zaman aralıklarıyla yenilenir, ancak aralıklar daha büyük zaman aralıkları için daha uzun olur. Verilerin analiz işlem hattından grafik üzerine gelmesi biraz zaman alabilir.

Grafiğin bir kısmına yakınlaştırmak için, üzerine sürükleyin:

![Grafiğin bir bölümü boyunca sürükleyin.](./media/metrics-explorer/12-drag.png)

Geri yüklemek için yakınlaştırmayı geri al düğmesine tıklayın.

## <a name="granularity-and-point-values"></a>Ayrıntı düzeyi ve nokta değerleri
Bu noktada ölçümlerin değerlerini göstermek için farenizi grafiğin üzerine getirin.

![Fareyi grafiğin üzerine getirin](./media/metrics-explorer/02-focus.png)

Belirli bir noktadaki ölçüm değeri, yukarıdaki örnekleme aralığı içinde toplanır.

Örnekleme aralığı veya "ayrıntı düzeyi" dikey pencerenin en üstünde gösterilir.

![Bir dikey pencerenin üst bilgisi.](./media/metrics-explorer/11-grain.png)

Zaman aralığı dikey penceresinde ayrıntı düzeyi ayarlayabilirsiniz:

![Bir dikey pencerenin üst bilgisi.](./media/metrics-explorer/grain.png)

Kullanılabilir olan granuya seçtiğiniz zaman aralığına göre değişir. Açık granuo, zaman aralığı için "otomatik" ayrıntı düzeyi alternatiflerdir.


## <a name="editing-charts-and-grids"></a>Grafikleri ve ızgaraları düzenleniyor
Dikey pencereye yeni bir grafik eklemek için:

![Ölçüm Gezgini, grafik Ekle ' yi seçin.](./media/metrics-explorer/04-add.png)

Mevcut veya yeni bir grafik üzerinde **Düzenle** ' yi seçerek gösterdiği şeyi düzenleyin:

![Bir veya daha fazla ölçüm seçin](./media/metrics-explorer/08-select.png)

Bir grafikte birden fazla ölçüm gösterebilirsiniz, ancak birlikte görüntülenebilen birleşimler hakkında kısıtlamalar vardır. Tek bir ölçüm seçtiğinizde bazı diğerleri devre dışı bırakılır.

[Özel ölçümleri][track] uygulamanıza koddıysanız (trackmetric ve TrackEvent çağrıları), burada listelenecektir.

## <a name="segment-your-data"></a>Verilerinizi segmentlere ayırın
Bir ölçümü özelliğe göre bölebilirsiniz; Örneğin, farklı işletim sistemlerine sahip istemcilerde sayfa görünümlerini karşılaştırmak için.

Bir grafik veya kılavuz seçin, gruplandırma üzerinde geçiş yapın ve gruplamak için bir özellik seçin:

![Gruplandırma açık ' i seçin, sonra gruptaki bir özelliği Seç ' i ayarla](./media/metrics-explorer/15-segment.png)

> [!NOTE]
> Gruplandırma kullandığınızda, alan ve çubuk grafik türleri Yığılmış bir görüntü sağlar. Bu, toplama yönteminin toplam olduğu yerde uygundur. Ancak toplama türünün ortalama olduğu, çizgi veya ızgara görüntüleme türlerini seçin.
>
>

[Özel ölçümleri][track] uygulamanıza kodlarsa ve özellik değerlerini içeriyorsa, listeden özelliğini seçebilirsiniz.

Grafik, bölümlenmiş veriler için çok küçük mi? Yüksekliğini ayarlayın:

![Kaydırıcıyı ayarla](./media/metrics-explorer/18-height.png)

## <a name="aggregation-types"></a>Toplama türleri
Yan taraftaki gösterge, genellikle grafiğin dönemi boyunca toplanmış değeri gösterir. Grafiğin üzerine geldiğinizde, bu noktada değeri gösterir.

Grafikteki her bir veri noktası, önceki örnekleme aralığında veya "ayrıntı düzeyi" olarak alınan veri değerlerinin toplamıdır. Ayrıntı düzeyi dikey pencerenin üst kısmında gösterilir ve grafiğin genel zaman ölçeğiyle birlikte değişir.

Ölçümler, farklı yollarla toplanabilir:

* **Count** , örnekleme aralığında alınan olayların sayısıdır. İstekler gibi olaylar için kullanılır. Grafiğin yükseklikteki değişimler, olayların oluşma hızında değişimleri gösterir. Ancak örnekleme aralığını değiştirdiğinizde sayısal değerin değişdiğine unutmayın.
* **Sum** , örnekleme aralığı üzerinden alınan tüm veri noktalarının değerlerini veya grafiğin dönemini ekler.
* **Ortalama** , toplamı Aralık üzerinden alınan veri noktası sayısına böler.
* **Benzersiz** sayımlar Kullanıcı ve hesap sayısı için kullanılır. Örnekleme aralığı boyunca veya grafiğin dönemi boyunca, bu sırada görülen farklı kullanıcıların sayısını gösterir.
* **%** -her bir toplama 'nın yüzde olan sürümleri yalnızca kesimli grafiklerle kullanılır. Toplam, %100 ' e kadar her zaman ekler ve grafik toplam farklı bileşenlerin göreli katkısını gösterir.

    ![Yüzde toplamı](./media/metrics-explorer/percentage-aggregation.png)

### <a name="change-the-aggregation-type"></a>Toplama türünü değiştirme

![Grafiği düzenleyin ve ardından toplama 'yı seçin](./media/metrics-explorer/05-aggregation.png)

Her ölçüm için varsayılan yöntem, yeni bir grafik oluşturduğunuzda veya tüm ölçümler seçimi kaldırıldığında gösterilir:

![Varsayılanları görmek için tüm ölçümlerin seçimini kaldırın](./media/metrics-explorer/06-total.png)

## <a name="pin-y-axis"></a>Y eksenini sabitle 
Varsayılan olarak bir grafik, değerlerin hisse Sayın görsel gösterimini vermek için, veri aralığındaki maksimum değere kadar sıfırdan başlayan Y ekseni değerlerini gösterir. Ancak bazı durumlarda ücretten daha fazla değişiklik yapmak, değerlerin küçük değişikliklerini görsel olarak incelemek ilginç olabilir. Bu gibi özelleştirmeler için y ekseni Aralık düzenlemesi özelliğini kullanarak, istenen yerde Y ekseninin en küçük veya en büyük değerini sabitleyebilirsiniz.
Y ekseni Aralık ayarlarını açmak için "Gelişmiş ayarlar" onay kutusunu tıklatın

![Gelişmiş ayarlar ' a tıklayın, özel Aralık ' ı seçin ve min maks. değer belirtin](./media/metrics-explorer/y-axis-range.png)

## <a name="filter-your-data"></a>Verilerinizi filtreleyin
Yalnızca seçili bir özellik değerleri kümesine ilişkin ölçümleri görmek için:

![Filtre ' ye tıklayın, bir özellik genişletin ve bazı değerleri denetleyin](./media/metrics-explorer/19-filter.png)

Belirli bir özellik için herhangi bir değer seçmezseniz, tümünü seçmekle aynı olur: Bu özellikte filtre yoktur.

Her özellik değerinin yanı sıra olay sayılarına dikkat edin. Bir özelliğin değerlerini seçtiğinizde, diğer özellik değerlerinin yanında bulunan sayımlar ayarlanır.

Filtreler dikey penceredeki tüm grafiklere uygulanır. Farklı grafiklere farklı filtreler uygulanmasını istiyorsanız, farklı ölçüm dikey pencereleri oluşturun ve kaydedin. İsterseniz, farklı dikey pencerelere ait grafikleri birbirlerine sabitleyebilir, böylece birbirleriyle birlikte görüntüleyebilirsiniz.

### <a name="remove-bot-and-web-test-traffic"></a>Bot ve Web test trafiğini kaldır
**Gerçek veya yapay trafiği** filtrele ve **Gerçek**denetim ' i kullanın.

**Yapay trafik kaynağına**göre de filtre uygulayabilirsiniz.

### <a name="to-add-properties-to-the-filter-list"></a>Filtre listesine özellik eklemek için
Kendi seçtiğiniz bir kategoride Telemetriyi filtrelemek istiyor musunuz? Örneğin, kullanıcılarınızı farklı kategorilere ayırabilirsiniz, ancak verilerinizi bu kategorilere göre segmentlere ayırmak istiyorsunuz.

[Kendi özelliğini oluşturun](../../azure-monitor/app/api-custom-events-metrics.md#properties). Farklı SDK modülleri tarafından gönderilen standart telemetri dahil olmak üzere tüm telemetride görünmesini sağlamak için bir [telemetri başlatıcısında](../../azure-monitor/app/api-custom-events-metrics.md#defaults) ayarlayın.

## <a name="edit-the-chart-type"></a>Grafik türünü düzenleme
Kılavuzlar ve grafikler arasında geçiş yapabildiğinize dikkat edin:

![Bir ızgara veya grafik seçin, sonra bir grafik türü seçin](./media/metrics-explorer/16-chart-grid.png)

## <a name="save-your-metrics-blade"></a>Ölçümler dikey penceresini kaydedin
Bazı grafikler oluşturduğunuzda bunları sık kullanılan olarak kaydedin. Bir kuruluş hesabı kullanıyorsanız, onu diğer ekip üyeleriyle paylaşmak isteyip istemediğinizi seçebilirsiniz.

![Sık kullanılan Seç](./media/metrics-explorer/21-favorite-save.png)

Dikey pencereyi tekrar görmek için **genel bakış dikey penceresine gidin** ve Sık Kullanılanlar ' ı açın:

![Genel Bakış dikey penceresinde Sık Kullanılanlar ' ı seçin.](./media/metrics-explorer/22-favorite-get.png)

Kaydettiğinizde göreli zaman aralığı ' nı seçerseniz, dikey pencere en son ölçülerle güncelleştirilir. Mutlak zaman aralığı ' nı seçtiyseniz, her seferinde aynı veriler gösterilir.

## <a name="reset-the-blade"></a>Dikey pencereyi sıfırlama
Bir dikey pencereyi düzenlerseniz, ancak özgün kaydedilmiş kümesine geri dönmek isterseniz, Sıfırla ' ya tıklamanız yeterlidir.

![Ölçüm Gezgini ' nin en üstündeki düğmelerde](./media/metrics-explorer/17-reset.png)

## <a name="live-metrics-stream"></a>Canlı ölçüm akışı

Telemetrinizin çok daha hızlı bir görünümü için [canlı akış](live-stream.md)açın. Toplama işlemi nedeniyle çoğu ölçüm görünmesi birkaç dakika sürer. Bunun aksine, canlı ölçümler düşük gecikme süresi için iyileştirilmiştir. 

## <a name="set-alerts"></a>Uyarılar ayarlama
Herhangi bir ölçümün olağandışı değerlerinin e-postasından haberdar olmak için bir uyarı ekleyin. E-postayı hesap yöneticilerine veya belirli e-posta adreslerine göndermek üzere birini seçebilirsiniz.

![Ölçüm Gezgini, uyarı kuralları ' nı seçin, uyarı Ekle](./media/metrics-explorer/appinsights-413setMetricAlert.png)

[Uyarılar hakkında daha fazla bilgi edinin][alerts].


## <a name="continuous-export"></a>Sürekli Dışarı Aktarma
Verileri dışarıdan işleyebilmeniz için sürekli olarak dışa aktarılmasını istiyorsanız [sürekli dışarı aktarma](../../azure-monitor/app/export-telemetry.md)kullanmayı düşünün.

### <a name="power-bi"></a>Power BI
Verilerinizin daha zengin görünümlerini istiyorsanız [Power BI dışa aktarabilirsiniz](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx).

## <a name="analytics"></a>Analiz
[Analiz](../../azure-monitor/app/analytics.md) , güçlü bir sorgu dili kullanarak telemetrinizi çözümlemenin daha çok yönlü bir yoludur. Ölçümleri ölçülerden birleştirmek veya hesaplamak isterseniz veya uygulamanızın en son performansını derinlemesine bir şekilde araştırmayı gerçekleştirmek istiyorsanız bu seçeneği kullanın. 

Ölçüm grafiğinden analiz simgesine tıklayarak doğrudan eşdeğer analiz sorgusuna ulaşabilirsiniz.

## <a name="troubleshooting"></a>Sorun giderme
*Grafiğimde hiç veri görmüyorum.*

* Filtreler dikey penceredeki tüm grafiklere uygulanır. Tek bir grafiğe odaklanırken, diğer tüm verileri dışlayan bir filtre ayarlamadıysanız emin olun.

    Farklı grafiklerde farklı filtreler ayarlamak istiyorsanız bunları farklı dikey pencerelerde oluşturun, bunları ayrı sık kullanılanlar olarak kaydedin. İsterseniz, bunları birbirlerine göre görebilmeniz için panoya sabitleyebilir.
* Bir grafiği, ölçüm üzerinde tanımlı olmayan bir özelliğe göre gruplandırdıysanız grafik üzerinde hiçbir şey olmayacaktır. ' Group by ' öğesini temizlemeyi deneyin veya farklı bir gruplama özelliği seçin.
* Durum İzleyicisi 'ni ve [Azure Cloud Services](../../azure-monitor/app/app-insights-overview.md)'yi yüklüyorsanız, Java Web Hizmetleri, Windows Masaüstü uygulamaları, [IIS Web uygulamaları ve Hizmetleri](../../azure-monitor/app/monitor-performance-live-website-now.md)IÇIN performans VERILERI (CPU, GÇ oranı vb.) kullanılabilir. Azure Web siteleri için kullanılamaz.

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Sonraki adımlar
* [Application Insights ile kullanımı izleme](../../azure-monitor/app/usage-overview.md)
* [Tanılama aramasını kullanma](../../azure-monitor/app/diagnostic-search.md)

<!--Link references-->

[alerts]: ../../azure-monitor/app/alerts.md
[start]: ../../azure-monitor/app/app-insights-overview.md
[track]: ../../azure-monitor/app/api-custom-events-metrics.md
