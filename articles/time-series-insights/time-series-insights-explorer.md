---
title: Explorer-Azure Time Series Insights kullanarak verileri keşfet | Microsoft Docs
description: IoT verilerinizi görüntülemek için Azure Time Series Insights Gezginini nasıl kullanacağınızı öğrenin.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 639f2ef12d190a56e04a9b48d96ea0a6537d243c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95020121"
---
# <a name="azure-time-series-insights-gen1-explorer"></a>Azure Time Series Insights Gen1 Gezgini

> [!CAUTION]
> Bu bir Gen1 makaledir.

Bu makalede Azure Time Series Insights Gen1 [Explorer Web uygulamasının](https://insights.timeseries.azure.com/)özellikleri ve seçenekleri açıklanmaktadır. Azure Time Series Insights Gezgini, hizmet tarafından sunulan güçlü veri görselleştirme yeteneklerini gösterir ve kendi ortamınız dahilinde erişilebilir.

Azure Time Series Insights, milyarlarca IoT olayını aynı anda keşfedip analiz etmeyi kolaylaştıran ve tam olarak yönetilen bir analiz, depolama ve görselleştirme hizmetidir. Verilerinize ilişkin genel bir görünüm sunar. bu sayede IoT çözümünüzü hızla doğrulamanıza ve görev açısından kritik cihazlara maliyetli kapalı kalma süresi yapmaktan kaçınabilirsiniz. Gizli eğilimleri bulabilir, anormallikleri tespit edebilir ve neredeyse gerçek zamanlı olarak kök neden analizleri gerçekleştirebilirsiniz.

> [!TIP]
> Tanıtım ortamında kılavuzlu bir tur için [Azure Time Series Insights hızlı](time-series-quickstart.md)başlangıcı makalesini okuyun.

## <a name="video"></a>Video

### <a name="learn-about-querying-data-by-using-the-azure-time-series-insights-explorer-br"></a>Azure Time Series Insights gezginini kullanarak verileri sorgulama hakkında bilgi edinin. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Yukarıdaki <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"Azure IoT çözüm Hızlandırıcısını kullanarak Azure Time Series Insights</a> kullanmaya başlama" videosunu yürütün.

## <a name="prerequisites"></a>Önkoşullar

Azure Time Series Insights Gezginini kullanabilmeniz için şunları yapmanız gerekir:

- Azure Time Series Insights ortamı oluşturun. Daha fazla bilgi için [Azure Time Series Insights ile çalışmaya başlama](./time-series-insights-get-started.md)makalesini okuyun.
- Ortamda hesabınıza [erişim sağlayın](./concepts-access-policies.md) .
- Bir [IoT Hub](./how-to-ingest-data-iot-hub.md) veya [Olay Hub](./how-to-ingest-data-event-hub.md) 'ı olay kaynağı ekleyin.

## <a name="explore-and-query-data"></a>Verileri keşfet ve sorgulama

Olay kaynağınızı Azure Time Series Insights ortamınıza bağlama sırasında, zaman serisi verilerinizi araştırıp sorgulama yapabilirsiniz.

1. Başlamak için Web tarayıcınızda [Azure Time Series Insights Gezginini](https://insights.timeseries.azure.com/) açın. Pencerenin sol tarafında bir ortam seçin. Erişiminiz olan tüm ortamlar alfabetik sırada listelenir.

1. Bir ortam seçtikten sonra, üstteki ve, en **üstte bulunan yapılandırmalardan** birini kullanın veya istediğiniz **TimeSpan 'yi seçip** sürükleyin. Sağ üst köşedeki Büyüteç Camı ' nı seçin veya seçili zaman aralığı ' na sağ tıklayıp **Ara**' yı seçin.

1. Ayrıca **Otomatik aç** düğmesini seçerek kullanılabilirliği otomatik olarak her dakikada yenileyebilirsiniz. **Otomatik aç** düğmesi yalnızca kullanılabilirlik grafiğine uygulanır, ana görselleştirmenin içeriğine uygulanmaz.

1. Azure bulut simgesi sizi Azure portal ortamınıza götürür.

   [![Azure Time Series Insights ortamı seçimi](media/time-series-insights-explorer/tsi-ga-explorer-environments.png)](media/time-series-insights-explorer/tsi-ga-explorer-environments.png#lightbox)

1. Ardından, seçili TimeSpan sırasında tüm olayların sayısını gösteren bir grafik görüntülenir. Burada bir dizi denetimleriniz vardır:

    - **Terimler Düzenleyicisi paneli**: çalışma alanı, ortamınızı sorgulayan yerdir. Ekranın sol tarafında bulunur:
      - **Ölçü**: Bu açılan liste, tüm sayısal sütunları (**Double**) gösterir.
      - **Bölme ölçütü**: Bu açılan liste kategorik sütunları (**dizeler**) gösterir.
      - Adım ilişkilendirmeyi etkinleştirebilir, minimum ve maksimum ' ı gösterebilir ve **ölçüm**' in yanındaki denetim masasından y eksenini ayarlayabilirsiniz. Ayrıca, gösterilen verilerin bir sayı, ortalama veya veri toplamı olup olmadığını da ayarlayabilirsiniz.
      - Aynı x ekseninde görüntülenecek en fazla beş terim ekleyebilirsiniz. Yeni bir terim eklemek için **Ekle** ' yi seçin veya var olan bir terimin kopyasını eklemek için **Bu terimi Kopyala** düğmesini kullanın.

        [![Terim seçimi, filtreleme ve sorgu paneli](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png)](media/time-series-insights-explorer/tsi-ga-explorer-add-or-clone.png#lightbox)

      - **Koşul**: aşağıdaki tabloda listelenen işlenenleri kümesini kullanarak olaylarınızı hızlıca filtrelemek için koşulunu kullanın. Veya öğesini seçerek bir arama yaparsanız, koşul otomatik olarak bu aramaya göre güncelleştirilir. Desteklenen işlenen türleri şunlardır:

         |İşlem  |Desteklenen türler  |Notlar  |
         |---------|---------|---------|
         |**<**, **>**, **<=**, **>=**    |  **Double**, **DateTime**, **TimeSpan**       |         |
         |**=**, **!=**, **<>**     | **Dize**, **bool**, **Double**, **DateTime**, **TimeSpan**, **null**        |         |
         |**'NDAKI**     | **Dize**, **bool**, **Double**, **DateTime**, **TimeSpan**, **null**        |  Tüm işlenenler aynı türde veya **null** sabit olmalıdır.        |
         |**SILINDI**     | **Dize**        |  Sağ tarafta yalnızca sabit dize sabit değerlerine izin verilir. Boş dize ve **null** değerlerine izin verilmez.       |

      - **Örnek sorgular**

         [![Örnek Gen1 sorguları](media/time-series-insights-explorer/tsi-ga-example-queries.png)](media/time-series-insights-explorer/tsi-ga-example-queries.png#lightbox)

1. **Aralık boyutu** kaydırıcı aracını, aynı TimeSpan üzerinde yer alarak ve aralıkları yakınlaştırmak için kullanabilirsiniz. Kaydırıcı, verilerinizin parçalı, yüksek çözünürlüklü kesişimlerini görüntülemenizi ve analiz etmenizi sağlayan, çok sayıda dilimlere doğru eğilimler gösteren büyük dilimler arasında hareket konusunda daha kesin bir denetim sağlar. Kaydırıcının varsayılan başlangıç noktası, çözümleme, sorgu hızı ve ayrıntı düzeyi dengelemek için Seçiminizdeki verilerin en uygun görünümü olarak ayarlanır.

1. **Zaman fırçası** aracı bir TimeSpan 'den diğerine gezinmeyi kolaylaştırır.

1. Geçerli sorgunuzu kaydetmek ve ortamın diğer kullanıcılarıyla paylaşmak için **Kaydet** simgesini seçin. **Aç** simgesini seçtiğinizde, erişiminiz olan ortamlardaki tüm kayıtlı sorgularınızı ve diğer kullanıcıların paylaşılan sorgularını gözden geçirebilirsiniz.

   [![Sorgular](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png)](media/time-series-insights-explorer/tsi-ga-explorer-saved-queries.png#lightbox)

## <a name="visualize-data"></a>Verileri görselleştirme

1. En fazla dört benzersiz sorgu için eş zamanlı bir görünüm için **perspektif görünümü** aracını kullanın. **Perspektif görünümü** düğmesi grafiğin sağ üst köşesinde bulunur.

   [![Perspektif bölmesine eklenecek sorguları seçin](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png)](media/time-series-insights-explorer/tsi-ga-explorer-perspective-panes.png#lightbox)

1. Verilerinizi görsel olarak araştırmak için bir grafik görüntüleyin ve **grafik** araçlarını kullanın:

    - Belirli bir TimeSpan veya tek bir veri serisini **seçin** veya **tıklayın** .
    - Bir zaman aralığı seçimi içinde, olayları yakınlaştırıp inceleyebilirsiniz.
    - Bir veri serisi içinde, seriyi başka bir sütuna göre bölebilir, seriyi yeni bir terim olarak ekleyebilir, yalnızca seçili seriyi gösterebilir, seçili seriyi hariç tutabilir, bu seriye ping yapabilir veya seçili serideki olayları keşfedebilirsiniz.
    - Grafiğin solundaki filtre alanında, görüntülenecek tüm veri serilerini gözden geçirebilir ve değer veya ada göre yeniden sıralama yapabilirsiniz. Ayrıca, tüm veri serilerini veya sabitlenmiş ya da sabitlenmemiş serileri görüntüleyebilirsiniz. Tek bir veri serisi seçebilir ve seriyi başka bir sütuna göre bölebilir, seriyi yeni bir terim olarak ekleyebilir, seçilen seriyi gösterebilir, seçili seriyi hariç tutabilir, bu seriyi bırakabilir veya seçili serideki olayları keşfedebilirsiniz.
    - Birden çok terimi aynı anda görüntülediğinizde, bir veri serisi hakkında daha fazla veriyi yığın oluşturabilir, yığmaya geçirebilir, geri yükleyebilir ve tüm şartlar genelinde aynı y eksenini kullanabilirsiniz. Grafiğin sağ üst köşesindeki düğmeleri kullanın.

    [![Grafik aracı sağ üst köşe seçeneği ayarları](media/time-series-insights-explorer/tsi-ga-example-chart-options.png)](media/time-series-insights-explorer/tsi-ga-example-chart-options.png#lightbox)

1. Belirli bir sorgudaki benzersiz veya anormal veri serisini hızlıca belirlemek için **ısı haritasını** ' i kullanın. Tek bir arama terimi, heatmap olarak görselleştirilir.

    [Time Series Insights Explorer ısı haritasını grafiğini](media/time-series-insights-explorer/tsi-ga-example-heatmap-charting.png)oluşturma] (medya/saat-serisi-Öngörüler-gezgin/tsi-ga-example-heatmap-charting.png#lightbox)

1. Olayları araştırdığınızda veya sağ tıkladıktan sonra **Olaylar** paneli kullanılabilir hale getirilir. Burada, tüm ham olaylarınızı gözden geçirebilir ve olaylarınızı JSON veya CSV dosyaları olarak dışarı aktarabilirsiniz. Azure Time Series Insights tüm ham verileri depolar.

    [![Ekinlikler](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png)](media/time-series-insights-explorer/tsi-ga-explorer-events-panel.png#lightbox)

1. Desenleri ve sütun istatistiklerini göstermek için olayları araştırdıktan sonra **İstatistikler** sekmesini seçin.

    - **Desenler**: Bu özellik, seçilen bir veri bölgesinde en istatistiksel olarak önemli desenleri proaktif olarak yüzey halinde gösterir. Hangi desenlerin en fazla ve enerji gerektirdiğini anlamak için binlerce olaya bakmanız gerekmez. Azure Time Series Insights, analiz yapmaya devam etmek için doğrudan bu istatistiksel olarak önemli desenlere geçebilirsiniz. Bu özellik ayrıca, daha sonra geçmiş verilere yönelik sonrası araştırmalar için de yararlıdır.
    - **Sütun istatistikleri**: sütun istatistikleri seçili zaman aralığı boyunca seçilen veri serisinin her bir sütunundaki verileri kesen grafikler ve tablolar sağlar.

      [![STATS sütunu grafik ve Seçenekler](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png)](media/time-series-insights-explorer/tsi-ga-explorer-stat-column.png#lightbox)

Artık Azure Time Series Insights gezgin Web uygulamasında bulunan temel özellikler, yapılandırma ayarları ve görüntüleme seçenekleri hakkında bilgi edindiniz.

## <a name="next-steps"></a>Sonraki adımlar

- Azure Time Series Insights ortamınızda [sorunları tanılamanıza ve çözmeyi](time-series-insights-diagnose-and-solve-problems.md) öğrenin.

- Kılavuzlu [Azure Time Series Insights hızlı başlangıç](time-series-quickstart.md) turunu alın.