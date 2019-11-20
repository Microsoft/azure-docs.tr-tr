---
title: Veri odaklı Azure Stream Analytics'te hata ayıklama
description: Bu makalede, Azure Stream Analytics işinizi Azure portalında iş diyagramı ve ölçümleri kullanarak sorun giderme açıklar.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: 472d7fcbca1a221b69d681ce33d39978b53a3204
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620940"
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>Veri odaklı iş diyagramı kullanarak hata ayıklama

İş diyagramı üzerinde **izleme** Azure portalındaki dikey penceresinde, işlem hattınızı görselleştirmenize yardımcı olabilir. Girişleri, çıkışları ve sorgu adımlarını gösterir. İş diyagramını kullanarak her adımda ölçümleri inceleyebilir, sorunları giderirken sorunun kaynağını daha hızlı yalıtabilirsiniz.

## <a name="using-the-job-diagram"></a>İş diyagramı kullanma

Azure portalında bir Stream Analytics işinde altında çalışırken **destek + sorun giderme**seçin **iş diyagramı**:

![İş diyagramı ölçümlerle - konum](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

İlgili bölüm düzenleme bölmesi bir sorgu görmek için her bir sorgu adımına seçin. Bu adım için bir ölçüm grafiği, bir alt bölmede sayfasında görüntülenir.

![İş diyagramı ölçümlerle - temel iş](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Azure Event Hubs'a giriş bölümünü görmek için seçin **...** Bir bağlam menüsü görüntülenir. Giriş birleşme de görebilirsiniz.

![İş diyagramı - ölçümlerle bölümü genişletin](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

Yalnızca tek bir bölüm için ölçüm grafiğini görmek için ve bölüm düğümünü seçin. Ölçümler, sayfanın en altında gösterilir.

![İş diyagramı ölçümlerle - ölçüm daha](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

Ölçüm grafiği birleşmesi için görmek için birleşme düğümü seçin. Aşağıdaki grafik, olay bırakılan veya ayarlanmış olduğunu gösterir.

![İş diyagramı - ölçümlerle kılavuz](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

Ölçüm değeri ve saati ayrıntıları görmek için grafiğe gelin.

![Diyagram ölçümlerle proje - getirin](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Ölçümleri kullanarak sorun giderme

**QueryLastProcessedTime** ölçüm, belirli bir adıma veri alındığında gösterir. Topoloji bakarak hangi adımın veri almadığını görmek için çıkış işlemciden geriye doğru çalışabilir. Bir adım veri almıyorsa hemen önce bir sorgu adımına gidin. Önceki bir sorgu adımına bir zaman penceresi olup olmadığı ve yeterli süre için çıktı verilerini geçti, denetleyin. (Windows saate tutturulduğunu unutmayın bundan.)
 
Önceki bir sorgu adımına bir giriş işleyicisiyse, giriş ölçümlerini yanıtlanmasına yardımcı olması için aşağıdaki hedeflenen soruların kullanın. Bunlar, bir işi giriş kaynaklarından veri alma olup olmadığını belirlemenize yardımcı olabilir. Sorgu bölümlendirilmişse her bir bölümü inceleyin.
 
### <a name="how-much-data-is-being-read"></a>Ne kadar veri okunuyor?

*   **Inputeventssourcestotal** okuma veri birimlerinin sayısı. Örneğin, BLOB sayısı.
*   **Inputeventstotal** okunan olay sayısıdır. Bu ölçüm her bölüm için kullanılabilir.
*   **Inputeventsınbytestotal** okunan bayt sayısı.
*   **Inputeventslastarrivaltime** alınan her olayın sıraya zamanıyla güncelleştirilir.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>Zaman İlerliyor? Gerçek olaylar okunuyorsa noktalama işaretleri verilmeyebilir.

*   **InputEventsLastPunctuationTime**, zamanın ilerlemesini sağlamak için bir noktalama işaretinin ne zaman verildiğini gösterir. Noktalama işaretleri yazılmazsa veri akışı engellenebilir.
 
### <a name="are-there-any-errors-in-the-input"></a>Giriş hataları vardır?

*   **Inputeventseventdatanulltotal** null veriler içeren olayların sayısı.
*   **Inputeventsserializererrorstotal** doğru serisi kaldırılamadı olayların sayısı.
*   **Inputeventsdegradedtotal** seri durumdan çıkarma dışında bir sorun içeren olayların sayısı.
 
### <a name="are-events-being-dropped-or-adjusted"></a>Olayları bırakılmış veya ayarlanmış misiniz?

*   **Inputeventsearlytotal** bir uygulama zaman üst eşik olan olay sayısıdır.
*   **Inputeventslatetotal** sonra üst eşik olan bir uygulama zaman olay sayısıdır.
*   **Inputeventsdroppedbeforeapplicationstarttimetotal** işi başlatmadan önce zaman bırakılan olay sayısını olduğu.
 
### <a name="are-we-falling-behind-in-reading-data"></a>Biz verilerin okunmasını geride kalan?

*   **Giriş olayları (toplam) biriktirme listesindeki** kaç tane daha fazla ileti için Event Hubs ve Azure IOT hub'ı girişler okunması gerektiğini bildirir. Bu sayı 0'dan büyük olduğunda, iş verilerini ulaştığı andan işleyemez anlamına gelir. Bu durumda akış birimi sayısını artırın ve/veya iş paralel hale emin gerekebilir. Bunun hakkında daha fazla bilgi görebilirsiniz [sorgu paralelleştirme sayfası](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). 


## <a name="get-help"></a>Yardım alın
Ek Yardım için deneyin bizim [Azure Stream Analytics forumumuzu](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>Sonraki adımlar
* [Stream analytics'e giriş](stream-analytics-introduction.md)
* [Stream Analytics ile çalışmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Stream Analytics sorgu dili başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics Yönetimi REST API Başvurusu](https://msdn.microsoft.com/library/azure/dn835031.aspx)
