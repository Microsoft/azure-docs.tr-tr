---
title: Explorer-Azure Time Series Insights kullanarak verileri keşfet | Microsoft Docs
description: IoT verilerinizi görüntülemek için Azure Time Series Insights Gezginini nasıl kullanacağınızı öğrenin.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/03/2019
ms.custom: seodec18
ms.openlocfilehash: df895f99b64d30506bc9457fb5bdc54a2182b5fe
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012643"
---
# <a name="azure-time-series-insights-explorer"></a>Azure Time Series Insights Gezgini

Bu makalede, Azure Time Series Insights [Explorer Web uygulaması](https://insights.timeseries.azure.com/)için genel kullanıma yönelik özellikler ve seçenekler açıklanmaktadır. Time Series Insights Gezgini, hizmet tarafından sunulan güçlü veri görselleştirme yeteneklerini gösterir ve kendi ortamınız dahilinde erişilebilir.

Azure Time Series Insights, milyarlarca IoT olayını aynı anda keşfedip analiz etmeyi kolaylaştıran ve tam olarak yönetilen bir analiz, depolama ve görselleştirme hizmetidir. Verilerinize ilişkin genel bir görünüm sunar. bu sayede IoT çözümünüzü hızla doğrulamanıza ve görev açısından kritik cihazlara maliyetli kapalı kalma süresi yapmaktan kaçınabilirsiniz. Gizli eğilimleri bulabilir, anormallikleri tespit edebilir ve neredeyse gerçek zamanlı olarak kök neden analizleri gerçekleştirebilirsiniz. Time Series Insights Gezgini Şu anda genel önizlemededir.

> [!TIP]
> Tanıtım ortamında kılavuzlu bir tur için [Azure Time Series Insights hızlı](time-series-quickstart.md)başlangıcı makalesini okuyun.

## <a name="video"></a>Video

### <a name="learn-about-querying-data-by-using-the-time-series-insights-explorer-br"></a>Time Series Insights gezginini kullanarak verileri sorgulama hakkında bilgi edinin. </br>

> [!VIDEO https://www.youtube.com/embed/SHFPZvrR71s]

>[!NOTE]
>Yukarıdaki <a href="https://www.youtube.com/watch?v=6ehNf6AJkFo">"Azure IoT Çözüm Hızlandırıcısı kullanarak Time Series Insights</a> kullanmaya başlama" videosunu inceleyin.

## <a name="prerequisites"></a>Önkoşullar

Time Series Insights Gezginini kullanabilmeniz için şunları yapmanız gerekir:

- Zaman serisi görüşleri ortamı oluşturun. Daha fazla bilgi için bkz. [Time Series Insights kullanmaya başlama](./time-series-insights-get-started.md).
- Ortamda hesabınıza [erişim sağlayın](time-series-insights-data-access.md) .
- Bir [IoT Hub](time-series-insights-how-to-add-an-event-source-iothub.md) veya [Olay Hub](time-series-insights-how-to-add-an-event-source-eventhub.md) 'ı olay kaynağı ekleyin.

## <a name="explore-and-query-data"></a>Verileri keşfet ve sorgulama

Olay kaynağınızı Time Series Insights ortamınıza bağlama sırasında, zaman serisi verilerinizi araştırıp sorgulama yapabilirsiniz.

1. Başlamak için Web tarayıcınızda [Time Series Insights Gezginini](https://insights.timeseries.azure.com/) açın. Pencerenin sol tarafında bir ortam seçin. Erişiminiz olan tüm ortamlar alfabetik sırada listelenir.

1. Bir ortam seçtikten sonra, üstteki ve en üstteki **yapılandırmalardan birini kullanın** **veya istediğiniz TimeSpan** üzerine tıklayıp sürükleyin. Sağ üst köşedeki Büyüteç Camı ' nı seçin veya seçili zaman aralığı ' na sağ tıklayıp **Ara**' yı seçin.

1. Ayrıca **Otomatik aç** düğmesini seçerek kullanılabilirliği otomatik olarak her dakikada yenileyebilirsiniz. **Otomatik aç** düğmesi yalnızca kullanılabilirlik grafiğine uygulanır, ana görselleştirmenin içeriğine uygulanmaz.

1. Azure bulut simgesi sizi Azure portal ortamınıza götürür.

   [![Time Series Insights ortamı](media/time-series-insights-explorer/explorer1.png)](media/time-series-insights-explorer/explorer1.png#lightbox)

1. Ardından, seçili TimeSpan sırasında tüm olayların sayısını gösteren bir grafik görürsünüz. Burada bir dizi denetimleriniz vardır:

    - **Terimler Düzenleyicisi paneli**: çalışma alanı, ortamınızı sorgulayan yerdir. Ekranın sol tarafında bulunur:
      - **Ölçü**: Bu açılan liste, tüm sayısal sütunları (**Double**) gösterir.
      - **Bölme ölçütü**: Bu açılan liste kategorik sütunları (**dizeler**) gösterir.
      - Adım ilişkilendirmeyi etkinleştirebilir, minimum ve maksimum ' ı gösterebilir ve **ölçüm**' in yanındaki denetim masasından y eksenini ayarlayabilirsiniz. Ayrıca, gösterilen verilerin bir sayı, ortalama veya veri toplamı olup olmadığını da ayarlayabilirsiniz.
      - Aynı x ekseninde görüntülenecek en fazla beş terim ekleyebilirsiniz. Ek bir terim eklemek için **aşağı Kopyala** düğmesini kullanın veya yeni bir terim eklemek için **Ekle** ' yi seçin.

        [![terimleri düzenleyici paneli](media/time-series-insights-explorer/explorer2.png)](media/time-series-insights-explorer/explorer2.png#lightbox)

      - **Koşul**: aşağıdaki tabloda listelenen işlenenleri kümesini kullanarak olaylarınızı hızlıca filtrelemek için koşulunu kullanın. Veya öğesini seçerek bir arama yaparsanız, koşul otomatik olarak bu aramaya göre güncelleştirilir. Desteklenen işlenen türleri şunlardır:

         |İşlem  |Desteklenen türler  |Notlar  |
         |---------|---------|---------|
         |`<`, `>`, `<=`, `>=`     |  Çift, DateTime, zaman aralığı       |         |
         |`=`, `!=`, `<>`     | Dize, Bool, Double, DateTime, zaman aralığı, NULL        |         |
         |IN     | Dize, Bool, Double, DateTime, zaman aralığı, NULL        |  Tüm işlenenler aynı türde veya NULL sabiti olması.        |
         |SILINDI     | Dize        |  Sağ tarafta yalnızca sabit dize sabit değerlerine izin verilir. Boş dize ve NULL değerlerine izin verilmez.       |

      - **Sorgu örnekleri**

         [![örnek sorgular](media/time-series-insights-explorer/explorer9.png)](media/time-series-insights-explorer/explorer9.png#lightbox)

1. **Aralık boyutu** kaydırıcı aracını, aynı TimeSpan üzerinde yer alarak ve aralıkları yakınlaştırmak için kullanabilirsiniz. Kaydırıcı, verilerinizin parçalı, yüksek çözünürlüklü olarak ölçeğini görmenizi sağlayan, çok sayıda dilimde dilimlere doğru eğilimler gösteren, büyük zaman dilimleri arasında hareket konusunda daha kesin bir denetim sağlar. Kaydırıcının varsayılan başlangıç noktası, çözümleme, sorgu hızı ve ayrıntı düzeyi dengelemek için Seçiminizdeki verilerin en uygun görünümü olarak ayarlanır.

1. **Zaman fırçası** aracı bir TimeSpan 'den diğerine gezinmeyi kolaylaştırır.

1. Geçerli sorgunuzu kaydetmek ve ortamın diğer kullanıcılarıyla paylaşmak için **Kaydet** komutunu kullanın. **Aç**'ı kullandığınızda, tüm kaydedilmiş sorgularınızı ve erişiminiz olan ortamlarda diğer kullanıcıların paylaşılan sorgularını görebilirsiniz.

   [![sorguları](media/time-series-insights-explorer/explorer3.png)](media/time-series-insights-explorer/explorer3.png#lightbox)

## <a name="visualize-data"></a>Verileri görselleştirme

1. En fazla dört benzersiz sorgu için eş zamanlı bir görünüm için **perspektif görünümü** aracını kullanın. **Perspektif görünümü** düğmesi grafiğin sağ üst köşesinde bulunur.

   [![perspektif görünümü](media/time-series-insights-explorer/explorer4.png)](media/time-series-insights-explorer/explorer4.png#lightbox)

1. Verilerinizi görsel olarak araştırmak için bir grafik görüntüleyin ve **grafik** araçlarını kullanın:

    - Belirli bir TimeSpan veya tek bir veri serisini **seçin** veya **tıklayın** .
    - Bir zaman aralığı seçimi içinde, olayları yakınlaştırıp inceleyebilirsiniz.
    - Bir veri serisi içinde, seriyi başka bir sütuna göre bölebilir, seriyi yeni bir terim olarak ekleyebilir, yalnızca seçili seriyi gösterebilir, seçili seriyi hariç tutabilir, bu seriye ping yapabilir veya seçili serideki olayları keşfedebilirsiniz.
    - Grafiğin solundaki filtre alanında, görüntülenecek tüm veri serilerini ve değer veya ada göre yeniden sıralama görüntüleyebilirsiniz. Ayrıca, tüm veri serilerini veya sabitlenmiş ya da sabitlenmemiş serileri görüntüleyebilirsiniz. Tek bir veri serisi seçebilir ve seriyi başka bir sütuna göre bölebilir, seriyi yeni bir terim olarak ekleyebilir, seçilen seriyi gösterebilir, seçili seriyi hariç tutabilir, bu seriyi bırakabilir veya seçili serideki olayları keşfedebilirsiniz.
    - Birden çok terimi aynı anda görüntülediğinizde, yığın oluşturabilir, yığın oluşturabilir, bir veri serisi hakkında ek verilere bakabilirsiniz ve tüm şartlar genelinde aynı y eksenini kullanabilirsiniz. Grafiğin sağ üst köşesindeki düğmeleri kullanın.

    [![grafik aracı](media/time-series-insights-explorer/explorer5.png)](media/time-series-insights-explorer/explorer5.png#lightbox)

1. Belirli bir sorgudaki benzersiz veya anormal veri serisini hızlıca belirlemek için **ısı haritasını** ' i kullanın. Tek bir arama terimi, heatmap olarak görselleştirilir.

    [![heatmap](media/time-series-insights-explorer/explorer6.png)](media/time-series-insights-explorer/explorer6.png#lightbox)

1. Olayları araştırdığınızda veya sağ tıkladıktan sonra **Olaylar** paneli kullanılabilir hale getirilir. Burada, tüm ham olaylarınızı görebilir ve olaylarınızı JSON veya CSV dosyaları olarak dışarı aktarabilirsiniz. Time Series Insights tüm ham verileri depolar.

    [![olaylar](media/time-series-insights-explorer/explorer7.png)](media/time-series-insights-explorer/explorer7.png#lightbox)

1. Desenleri ve sütun istatistiklerini göstermek için olayları araştırdıktan sonra **İstatistikler** sekmesini seçin.

    - **Desenler**: Bu özellik, seçilen bir veri bölgesinde en istatistiksel olarak önemli desenleri proaktif olarak yüzey halinde gösterir. Hangi desenlerin en fazla ve enerji gerektirdiğini anlamak için binlerce olaya bakmanız gerekmez. Time Series Insights, analiz yapmaya devam etmek için doğrudan bu istatistiksel olarak önemli desenlere geçebilirsiniz. Bu özellik ayrıca, daha sonra geçmiş verilere yönelik sonrası araştırmalar için de yararlıdır.
    - **Sütun istatistikleri**: sütun istatistikleri seçili zaman aralığı boyunca seçilen veri serisinin her bir sütunundaki verileri kesen grafikler ve tablolar sağlar.

      [![ISTATISTIKLERI](media/time-series-insights-explorer/explorer8.png)](media/time-series-insights-explorer/explorer8.png#lightbox)

Artık Time Series Insights Explorer Web uygulamasında bulunan çeşitli özellikleri ve seçenekleri gördünüz.

## <a name="next-steps"></a>Sonraki adımlar

- Time Series Insights ortamınızda [sorunları tanılamanıza ve çözmeyi](time-series-insights-diagnose-and-solve-problems.md) öğrenin.

- Kılavuzlu [Azure Time Series Insights hızlı başlangıç](time-series-quickstart.md) turunu alın.
