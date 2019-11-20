---
title: Azure Stream Analytics Pencereleme işlevleri'ne giriş
description: Bu makalede, Azure Stream Analytics işlerinde kullanılan dört Pencereleme işlevleri (atlayan atlamalı, kayan, oturumu) açıklanmaktadır.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 530ff8d09d6c580a31ae26929fafcec5bb5b471b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621601"
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>Stream Analytics Pencereleme işlevleri'ne giriş

Saat akış senaryolarda, zamana bağlı windows bulunan veriler üzerinde işlem gerçekleştirme yaygın modelidir. Stream Analytics, geliştiricilerin çok az bir çabayla Yazar karmaşık akış işleme işlerini Pencereleme işlevleri için yerel desteğe sahiptir.

Aralarından seçim zamana bağlı windows dört çeşit vardır: [**Atlayan**](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics), [ **atlamalı**](https://docs.microsoft.com/stream-analytics-query/hopping-window-azure-stream-analytics), [ **kayan**](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics), ve [ **oturumu**  ](https://docs.microsoft.com/stream-analytics-query/session-window-azure-stream-analytics) windows.  Pencere işlevleri kullanmak [ **GROUP BY** ](https://docs.microsoft.com/stream-analytics-query/group-by-azure-stream-analytics) sorgu söz dizimi, Stream Analytics işlerinde yan tümcesi. Olayları kullanarak birden çok windows üzerinde toplayabilirsiniz [ **Windows()** işlevi](https://docs.microsoft.com/stream-analytics-query/windows-azure-stream-analytics).

Tüm [Pencereleme](https://docs.microsoft.com/stream-analytics-query/windowing-azure-stream-analytics) işlemleri çıktı sonuçları **son** penceresinin. Çıktı penceresi kullanılan toplama işleve göre tek bir olay olacaktır. Çıkış olayı penceresinin bitiş zaman damgası sahip olur ve tüm pencere işlevleri sabit uzunluk ile tanımlanır. 

![Stream Analytics pencere işlevleri kavramı](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Atlayan pencere
Dönen pencere işlevleri farklı zaman kesimler halinde veri akışı segmentlere ayırın ve bunları karşı bir işlev aşağıdaki örnekte verilene gerçekleştirmek için kullanılır. Atlayan pencere anahtar erişilebilirliktir, bunlar yinelemeniz, olmayan çakışma ve bir olay için birden fazla atlayan pencere ait olamaz ' dir.

![Stream Analytics atlayan pencere](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Atlamalı pencere
Atlamalı pencere işlevleri atlama İleri sabit bir süre süresi. Olayları birden fazla Hopping penceresi sonuç kümesine ait olabilir, böylece binebilir, atlayan windows alışık kolay olabilir. Bir atlayan pencere aynı Hopping pencere yapmak için pencere boyutu ile aynı olacak şekilde atlama boyutu belirtin. 

![Stream Analytics atlama penceresi](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Kayan pencere
Atlayan veya atlamalı windows aksine, kayan pencere işlevleri bir çıktı üretir **yalnızca** bir olay gerçekleştiğinde. Her penceresinde en az bir olay olur ve pencereyi sürekli bir € (epsilon) ileri doğru taşır. Windows atlamalı gibi olayları için birden fazla kayan pencere ait olabilir.

![Stream Analytics kayan pencere](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window"></a>Oturum penceresi
Oturum pencere işlevleri Grup benzer zamanlarda gelen olayları süreler filtreleme bulunduğu veri yok. Üç ana parametreye sahiptir: zaman aşımı, en uzun süre ve bölümleme anahtarı (isteğe bağlı).

![Stream Analytics oturum penceresi](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

İlk olay gerçekleştiğinde bir oturum penceresi başlar. Alınan son olayın belirtilen zaman aşımı süresi içinde başka bir olay oluşursa pencerenin yeni olay içerecek şekilde genişletir. Aksi takdirde zaman aşımı süresi içinde hiçbir olay meydana gelirse, pencerenin zaman aşımı kapatılır.

Olaylar belirtilen süre içinde gerçekleşen korumak ise en fazla süre ulaşılana kadar oturum penceresi genişletme tutmak. En uzun süresi denetimi aralıkları, belirtilen en fazla süre aynı boyutta olacak şekilde ayarlanır. Örneğin, pencerenin denetimleri varsa aşan en yüksek en fazla 10 ise süresi sorun t = 0, 10, 20, 30, vs.

Bir bölüm anahtarı sağlandığında olayları anahtarıyla birlikte gruplanır ve oturum penceresi bağımsız olarak her gruba uygulanır. Bu bölümleme, farklı oturum windows farklı kullanıcılar veya cihazlar için gereken durumlarda yararlıdır.


## <a name="next-steps"></a>Sonraki adımlar
* [Azure Stream analytics'e giriş](stream-analytics-introduction.md)
* [Azure Akış Analizi'ni kullanmaya başlama](stream-analytics-real-time-fraud-detection.md)
* [Azure Akış Analizi işlerini ölçeklendirme](stream-analytics-scale-jobs.md)
* [Azure Akış Analizi Sorgu Dili Başvurusu](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Akış Analizi Yönetimi REST API'si Başvurusu](https://msdn.microsoft.com/library/azure/dn835031.aspx)

