---
title: Azaltmayı izleme ve azaltma-Azure Time Series Insights | Microsoft Docs
description: Azure Time Series Insights gecikme süresi ve azaltmasına neden olan performans sorunlarını izleme, tanılama ve azaltma hakkında bilgi edinin.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 7ea98baa9cb202e2584c18998c5ab96d1c1f9e5a
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012650"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Azure Time Series Insights gecikme süresini azaltmak için azaltmayı izleyin ve azaltır

Gelen veri miktarı ortamınızın yapılandırmasını aştığında, Azure Time Series Insights gecikme süresi veya azaltma yaşayabilirsiniz.

Ortamınızı çözümlemek istediğiniz veri miktarı için düzgün şekilde yapılandırarak gecikme süresini ve azaltmaktan kaçınabilirsiniz.

Şu durumlarda gecikme süresi ve azaltma deneyiminden karşılaşabilirsiniz:

- Ayrılan giriş hızınızı aşmayacak eski verileri içeren bir olay kaynağı ekleyin (Time Series Insights catch gerekecektir).
- Bir ortama daha fazla olay kaynağı ekleyin ve ek olaylardan bir ani artış elde edin (ortamınızın kapasitesini aşabilir).
- Büyük miktarlarda geçmiş olayları bir olay kaynağına göndererek bir gecikme (Time Series Insights yakalamalı olması gerekir) sonucunu vermez.
- Başvuru verilerini telemetri ile birleştirin ve daha büyük bir olay boyutuna neden olur.  Daraltma açısından, 32 KB 'lik paket boyutuna sahip bir veri paketi, her biri 1 KB boyutunda 32 olay olarak değerlendirilir. İzin verilen en fazla olay boyutu 32 KB; 32 KB 'den büyük veri paketleri kesilir.

## <a name="video"></a>Video

### <a name="learn-about-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>Time Series Insights veri giriş davranışı ve bunun nasıl planlanacağı hakkında bilgi edinin.</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>Uyarıları izleyen uyarılarla gecikme ve azaltma

Uyarılar, ortamınızın neden olduğu gecikme sorunlarını tanılamanıza ve azaltmanıza yardımcı olabilir.

1. Azure portal, **Uyarılar**' ı seçin.

   [![uyarılar](media/environment-mitigate-latency/add-alerts.png)](media/environment-mitigate-latency/add-alerts.png#lightbox)

1. **Kural oluştur** paneli görüntülenir. **Koşul**altında **Ekle** ' yi seçin.

   [uyarı eklemek ![](media/environment-mitigate-latency/alert-pane.png)](media/environment-mitigate-latency/alert-pane.png#lightbox)

1. Sonra, sinyal mantığı için tam koşulları yapılandırın.

   [sinyal mantığını yapılandırma ![](media/environment-mitigate-latency/configure-alert-rule.png)](media/environment-mitigate-latency/configure-alert-rule.png#lightbox)

   Buradan, aşağıdaki koşullardan bazılarını kullanarak uyarılar yapılandırabilirsiniz:

   |Ölçüm  |Açıklama  |
   |---------|---------|
   |**Alınan bayt sayısı**     | Olay kaynaklarından okunan ham bayt sayısı. Ham sayı genellikle özellik adını ve değerini içerir.  |  
   |**Giriş geçersiz Iletiler aldı**     | Tüm Azure Event Hubs veya Azure IoT Hub olay kaynaklarından okunan geçersiz iletilerin sayısı.      |
   |**Giriş alınan Iletiler**   | Tüm Event Hubs veya IoT Hub 'Ları olay kaynaklarından okunan ileti sayısı.        |
   |**Giriş depolanan baytlar**     | Sorgu için depolanan ve kullanılabilir olayların toplam boyutu. Boyut yalnızca özellik değeri üzerinde hesaplanır.        |
   |Giriş **saklı olayları**     |   Depolanan ve sorgu için kullanılabilir düzleştirilmiş olay sayısı.      |
   |**Alınan Ileti zaman gecikmesi alındı**    |  İleti olay kaynağında sıraya alındığı zaman ve giriş sırasında işlendiği zaman arasındaki saniye cinsinden fark.      |
   |Giriş **alınan Ileti sayısı gecikme**    |  Olay kaynak bölümünde en son sıraya alınan iletinin sıra numarası ve giriş olarak işlenen iletinin sıra numarası arasındaki fark.      |

   **Done** (Bitti) öğesini seçin.

1. İstenen sinyal mantığını yapılandırdıktan sonra, seçilen uyarı kuralını görsel olarak gözden geçirin.

   [![giriş](media/environment-mitigate-latency/ingress.png)](media/environment-mitigate-latency/ingress.png#lightbox)

## <a name="throttling-and-ingress-management"></a>Daraltma ve giriş yönetimi

* Kısıtladıysanız, *alınan Ileti zaman gecikmesi*için bir değer görürsünüz ve bu, zaman serisi öngörülerinin arkasındaki sürenin gerçek zamandan (Appx dizin oluşturma süresi dışında) ne kadar süreyle olduğunu size bildiren bir ileti alırsınız. 30-60 saniye).  

  *Alınan Ileti sayısı gecikmesi* de bir değer içermelidir, bu da arkasında kaç ileti olduğunu belirlemenizi sağlar.  En kolay şekilde, ortamınızın kapasitesini, farkı aşmanızı sağlayacak bir boyuta artırmanız gerekir.  

  Örneğin, S1 ortamınızın 5.000.000 ileti gecikmesini gösterir olduğunu görürseniz, bir gün boyunca daha fazla bilgi almak için ortamınızın boyutunu altı birim olarak artırabilirsiniz.  Daha hızlı bir şekilde daha fazla yakalamak için daha fazla artırabilirsiniz. Yakalama dönemi, özellikle bir ortamı ilk kez sağlarken, özellikle de olayları zaten olan bir olay kaynağına bağladığınızda veya çok sayıda geçmiş verileri toplu olarak karşıya yüklerken yaygın bir oluşumdır.

* Başka bir yöntem de bir giriş **saklı olayları** ayarlaması > = bir eşik değeri 2 saatlik bir dönem boyunca toplam ortam kapasitenizin altına göre biraz daha düşük.  Bu uyarı, gecikme süresinin yüksek olma olasılığını gösteren kapasiteyi sürekli olarak anlamanıza yardımcı olabilir. 

  Örneğin, üç adet S1 birimi sağlanmışsa (veya bir dakika başına 2100 olay), 2 saat boyunca > = 1900 olay için bir giriş **saklı olayları** uyarısı ayarlayabilirsiniz. Bu eşiği sürekli aşdıysanız ve bu nedenle uyarınızı tetikleyerek, büyük olasılıkla sağlanmış olursunuz.  

* Kısıtladığınızı kuşkulanıyorsanız, giriş **alınan iletilerinizi** olay kaynağınızın yumurtılan iletileriyle karşılaştırabilirsiniz.  Olay Hub 'ınız giriş **alınızdan**daha büyükse, Time Series Insights muhtemelen kısıtlanıyor demektir.

## <a name="improving-performance"></a>Performansı artırma

Azaltmayı veya gecikmeyi azaltmak için, bunu düzeltmenin en iyi yolu ortamınızın kapasitesini artırmaktır.

Ortamınızı çözümlemek istediğiniz veri miktarı için düzgün şekilde yapılandırarak gecikme süresini ve azaltmaktan kaçınabilirsiniz. Ortamınıza kapasite ekleme hakkında daha fazla bilgi için bkz. [ortamınızı ölçeklendirme](time-series-insights-how-to-scale-your-environment.md).

## <a name="next-steps"></a>Sonraki adımlar

- [Time Series Insights ortamınızda sorunları tanılama ve çözme](time-series-insights-diagnose-and-solve-problems.md)hakkında bilgi edinin.

- [Time Series Insights ortamınızı ölçeklendirmeyi](time-series-insights-how-to-scale-your-environment.md)öğrenin.