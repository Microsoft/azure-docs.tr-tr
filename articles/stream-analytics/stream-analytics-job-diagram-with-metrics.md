---
title: Azure Stream Analytics veri odaklı hata ayıklama
description: Bu makalede, Azure portal iş diyagramını ve ölçümleri kullanarak Azure Stream Analytics işinizin nasıl giderileceği açıklanmaktadır.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/01/2017
ms.openlocfilehash: 6d20454515088ccca87665d9b3b27c0d82c3cdf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020409"
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>İş diyagramını kullanarak veri odaklı hata ayıklama

Azure portal **izleme** dikey penceresindeki iş diyagramı, iş işlem hattınızı görselleştirmenize yardımcı olabilir. Girişleri, çıkışları ve sorgu adımlarını gösterir. İş diyagramını kullanarak her adımda ölçümleri inceleyebilir, sorunları giderirken sorunun kaynağını daha hızlı yalıtabilirsiniz.

## <a name="using-the-job-diagram"></a>İş diyagramını kullanma

Azure portal, bir Stream Analytics işinde, **destek + sorun giderme** altında **iş diyagramı**' nı seçin:

![Ölçüm-konum ile iş diyagramı](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Sorgu düzenlemesi bölmesinde ilgili bölümü görmek için her bir sorgu adımını seçin. Adım için bir ölçüm grafiği, sayfada alt bölmede görüntülenir.

![Ölçümler ile iş diyagramı-temel iş](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Azure Event Hubs girişi bölümlerini görmek için... seçeneğini belirleyin **.** Bağlam menüsü görüntülenir. Ayrıca, giriş merkli öğesini de görebilirsiniz.

![Ölçümler ile iş diyagramı-bölümü Genişlet](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

Ölçüm grafiğini yalnızca tek bir bölüm için görmek üzere bölüm düğümünü seçin. Ölçümler sayfanın alt kısmında gösterilir.

![Ölçümler ile iş diyagramı-daha fazla ölçüm](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

Birleşme için ölçüm grafiğini görmek için birleşme düğümünü seçin. Aşağıdaki grafikte hiçbir olayın bırakılmadığı veya ayarlandığı gösterilmektedir.

![Ölçümler ile iş diyagramı-kılavuz](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

Ölçüm değeri ve saatinin ayrıntılarını görmek için grafiğin üzerine gelin.

![Ölçümler ile iş diyagramı-üzerine gelme](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Ölçümleri kullanarak sorun giderme

**Querylastprocessedtime** ölçümü, belirli bir adımın veri aldığını gösterir. Topolojiyi inceleyerek, hangi adımın veri almadığını görmek için çıkış işlemcisinden geriye gidebilirsiniz. Bir adım veri almıyorsanız, hemen önceki sorgu adımına gidin. Yukarıdaki sorgu adımında bir zaman penceresi olup olmadığını ve veri çıkışı için yeterince zaman geçtiğini denetleyin. (Zaman pencerelerinin saate götürüldiğini unutmayın.)
 
Yukarıdaki sorgu adımı bir giriş işlemcisidir, aşağıdaki hedeflenen soruların yanıtlanmasına yardımcı olması için giriş ölçümlerini kullanın. Bu kişiler, bir işin giriş kaynaklarından veri alma olup olmadığını belirlemenize yardımcı olabilirler. Sorgu bölümlendirilmişse her bir bölümü inceleyin.
 
### <a name="how-much-data-is-being-read"></a>Ne kadar veri okunmakta?

*   **Inputeventssourcestotal** , okunan veri birimlerinin sayısıdır. Örneğin, Blobların sayısı.
*   **Inputeventstotal** , okunan olay sayısıdır. Bu ölçüm her bölüm için kullanılabilir.
*   **Inputeventsinbytestotal** , okunan bayt sayısıdır.
*   **Inputeventslastarrivaltime** , alınan her olayın sıraya alınma zamanına göre güncelleştirilir.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>Zaman ileri taşınıyor mı? Gerçek olaylar okunuyorsa noktalama işaretleri verilmeyebilir.

*   **InputEventsLastPunctuationTime**, zamanın ilerlemesini sağlamak için bir noktalama işaretinin ne zaman verildiğini gösterir. Noktalama işareti verilmemişse, veri akışı engellenebilir.
 
### <a name="are-there-any-errors-in-the-input"></a>Girişte herhangi bir hata var mı?

*   **Inputeventseventdadtanulltotal** , null veri içeren bir olay sayısıdır.
*   **Inputeventsserializererrorstotal** , doğru bir şekilde seri durumdan çıkarılamıyor bir olay sayısıdır.
*   **Inputeventsdüşürüldedtotal** , seri durumdan çıkarma dışında bir sorunu olan olay sayısıdır.
 
### <a name="are-events-being-dropped-or-adjusted"></a>Olaylar bırakılıyor veya düzeltildi mi?

*   **Inputeventsearlytotal** , yüksek filigrandan önce bir uygulama zaman damgasına sahip olan olay sayısıdır.
*   **Inputeventslatetotal** , yüksek filigrandan sonra uygulama zaman damgasına sahip olan olay sayısıdır.
*   **Inputeventsdroppedbeforeapplicationstarttimetotal** , iş başlangıç zamanından önce bırakılan olay sayısıdır.
 
### <a name="are-we-falling-behind-in-reading-data"></a>Verileri okurken geride düşeceğiz mı?

*   **Biriktirme listesindeki giriş olayları (Toplam)** Event Hubs ve Azure IoT Hub girişleri için kaç tane daha fazla ileti okunması gerektiğini söyler. Bu sayı 0 ' dan büyükse, işinizin verileri geldiği kadar hızlı işleyemediği anlamına gelir. Bu durumda, akış birimlerinin sayısını artırmanız ve/veya işinizin paralelleştirilmesine emin olmanız gerekebilir. [Sorgu paralelleştirme sayfasında](./stream-analytics-parallelization.md)bu konuda daha fazla bilgi görebilirsiniz. 


## <a name="get-help"></a>Yardım alın
Ek Yardım için, [Azure Stream Analytics Için Microsoft Q&soru sayfasını](/answers/topics/azure-stream-analytics.html)deneyin. 

## <a name="next-steps"></a>Sonraki adımlar
* [Stream Analytics giriş](stream-analytics-introduction.md)
* [Akış Analizi ile çalışmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Stream Analytics sorgu dili başvurusu](/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics yönetim REST API başvurusu](/rest/api/streamanalytics/)