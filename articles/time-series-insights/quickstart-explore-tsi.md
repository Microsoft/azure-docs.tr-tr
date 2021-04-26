---
title: 'Hızlı başlangıç: Gen2 demo ortamını araştırma-Azure Time Series Insights Gen2 | Microsoft Docs'
description: Azure Time Series Insights Gen2 demo ortamının temel özelliklerini gezin.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 03/01/2021
ms.openlocfilehash: 25340af243217313ce6d3f7205c102d4bcd350b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101698119"
---
# <a name="quickstart-explore-the-azure-time-series-insights-gen2-demo-environment"></a>Hızlı Başlangıç: Azure Time Series Insights 2. Nesil tanıtım ortamını keşfedin

Bu hızlı başlangıç, bir Azure Time Series Insights Gen2 ortamıyla başlamanızı ister. Ücretsiz gösteride, Azure Time Series Insights Gen2 'e eklenmiş olan önemli özellikler turuna katılın.

Azure Time Series Insights Gen2 demo ortamı, iki rüzgar türbin grubu çalıştıran bir senaryo şirketi olan contoso içerir. Her grupta 10 türbines vardır. Her türbin, verileri her dakika Azure IoT Hub rapor eden 20 sensöre sahiptir. Algılayıcılar, hava durumu koşulları, dikey pencere sıklığı ve yaw konumu hakkında bilgi toplar. Üretici performansı, Gearbox davranışı ve güvenlik izleyicileri ile ilgili bilgiler de kaydedilir.

Bu hızlı başlangıçta, contoso verilerinde eyleme dönüştürülebilir Öngörüler bulmak için Azure Time Series Insights Gen2 kullanmayı öğreneceksiniz. Ayrıca kritik sorunları daha iyi tahmin etmek ve bakım gerçekleştirmek için kısa bir kök neden analizi de gerçekleştirirsiniz.

> [!IMPORTANT]
> Ücretsiz bir [Azure hesabı](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)oluşturun   .

## <a name="explore-the-azure-time-series-insights-gen2-explorer-in-a-demo-environment"></a>Tanıtım ortamında Azure Time Series Insights Gen2 Gezginini keşfet

Azure Time Series Insights Gen2 Explorer geçmiş verileri ve kök neden analizini gösterir. Başlamak için:

1.  [Contoso rüzgar grubu tanıtım](https://insights.timeseries.azure.com/preview/samples) ortamına gidin.  

1. İstenirse, Azure hesabı kimlik bilgilerinizi kullanarak Azure Time Series Insights Gen2 Explorer 'da oturum açın.

## <a name="work-with-historical-data"></a>Geçmiş verilerle çalışma

1. **Contoso WINI grup hiyerarşisi**' ni seçin.

   [![Contoso Wınbir grup hiyerarşisi](media/quickstart-explore/quick-start-contoso-1.png)](media/quickstart-explore/quick-start-contoso-1.png#lightbox)

1. **Contoso bitki 1**' de rüzgar türbin **W7** bölümüne bakın.

   [![Contoso tesis 1 ' de W7](media/quickstart-explore/quick-start-contoso-2.png)](media/quickstart-explore/quick-start-contoso-2.png#lightbox)

   1. Görünüm aralığını **1/1/17 20:00:00.00 olarak 3/10/17 20:00:00.00 (UTC)** olarak değiştirin.

      [![Aralık görünümü](media/quickstart-explore/range-setting-1.png)](media/quickstart-explore/range-setting-1.png#lightbox)

      [![Aralık görünümü ayarı](media/quickstart-explore/range-setting-2.png)](media/quickstart-explore/range-setting-2.png#lightbox)

   1. Ölçeği Yakınlaştır **(+)** ve out **(-)** ile ve slayt çubuğunu taşıyarak Aralık görünümünü ayarlayın.

      [![Aralık görünümünü ayarla](media/quickstart-explore/view-range-setting.png)](media/quickstart-explore/view-range-setting.png#lightbox)

   1. Bir algılayıcı seçmek için **contoso bitki 1**  >  **W7**  >  **Generator sistem**  >  **generatorspeed**' i seçin. Ardından, gösterilen değerleri gözden geçirin.

      [![Üretici hızı](media/quickstart-explore/quick-start-generator-speed-1.png)](media/quickstart-explore/quick-start-generator-speed-1.png#lightbox)

1. Contoso, Rüzgar Turbin **W7** içinde bir yangın buldu. Opın ons, yangın ne olduğuna ilişkin farklılık gösterir. Azure Time Series Insights Gen2 ' de, yangın sırasında etkinleştirilen yangın uyarı algılayıcısı görüntülenir.

   1. Görünüm aralığını **3/9/17 20:00:00.00 olarak 3/10/17 20:00:00.00 (UTC)** olarak değiştirin.
   1. **Güvenlik sistemi**  >  **firealert** öğesini seçin.

      [![Contoso, Rüzgar Turbin W7 'de bir yangın buldu](media/v2-update-quickstart/quick-start-fire-alert.png)](media/v2-update-quickstart/quick-start-fire-alert.png#lightbox)

1. Ne olduğunu anlamak için yangın süresi etrafında diğer olayları gözden geçirin. Yağ baskısı ve etkin uyarılar, yalnızca yangın 'tan önce.

   1. **Sıklık sistem**  >  **Hidrolik licoilbasıncı**' nı seçin.
   1. **Aralıklı sistem**  >  **activewarning** öğesini seçin.

      [![Aynı anda diğer olayları gözden geçirme](media/v2-update-quickstart/quick-start-active-warning.png)](media/v2-update-quickstart/quick-start-active-warning.png#lightbox)

1. Yağ basıncı ve etkin uyarı sensörleri, yangın 'tan önce. Harekete geçiren önde gelen diğer işaretleri gözden geçirmek için görüntülenen zaman serisini genişletin. Her iki algılayıcı zaman içinde sürekli olarak dalgalanılır. Dalgalanmalar kalıcı ve worrisme deseninin olduğunu gösterir.

    * Görünüm aralığını **2/24/17 20:00:00.00 olarak 3/10/17 20:00:00.00 (UTC)** olarak değiştirin.

      [![Yağ basıncı ve etkin uyarı algılayıcıları Ayrıca spıked](media/v2-update-quickstart/quick-start-view-range.png)](media/v2-update-quickstart/quick-start-view-range.png#lightbox)

1. İki yıllık geçmiş verileri incelemek, aynı algılayıcı dalgalanmalarına sahip başka bir yangın olayını ortaya çıkarır.

    * Görünüm aralığını **1/1/16, 12/31/17** (tüm veriler) olarak değiştirin.

      [![Geçmiş desenleri ara](media/v2-update-quickstart/quick-start-expand-view-range.png)](media/v2-update-quickstart/quick-start-expand-view-range.png#lightbox)

Azure Time Series Insights Gen2 ve algılayıcı telemetrisi kullanarak, geçmiş verilerde uzun süreli bir eğilim gizliyoruz. Bu yeni Öngörüler sayesinde şunları yapabilirsiniz:

* Gerçekten ne olduğunu açıklayın.
* Sorunu giderin.
* Daha iyi uyarı bildirimi sistemlerini yerine koyun.

## <a name="root-cause-analysis"></a>Kök neden analizi

1. Bazı senaryolar, verilerdeki ipuçlarına yönelik gelişmiş analizler gerektirir. **6/25** tarihinde Windmill **W6** seçin.

    1. Görünüm aralığını **6/1/17 20:00:00.00 olarak 7/1/17 20:00:00.00 (UTC)** olarak değiştirin.
    1. **Contoso bitki 1**  >  **W6**  >  **güvenlik sistemi**  >  **VoltageActuatorSwitchWarning**' nı seçin.

       [![Görünüm aralığını değiştirin ve W6 seçin](media/v2-update-quickstart/quick-start-voltage-switch-warning.png)](media/v2-update-quickstart/quick-start-voltage-switch-warning.png#lightbox)

1. Uyarı, oluşturucunun gerilimiyle ilgili bir sorun olduğunu gösterir. Oluşturucunun genel güç çıkışı, geçerli aralıktaki normal parametreler içindedir. Zaman sıklığımızı artırarak başka bir model ortaya çıktı. Bir bırakma kapalı olur.

    1. **VoltageActuatorSwitchWarning** algılayıcısı 'nı kaldırın.
    1. **Generator sistem**  >  **activepower** öğesini seçin.
    1. Aralığı **3B** olarak değiştirin.

       [![Aralığı 3B olarak değiştirme](media/v2-update-quickstart/quick-start-interval-change.png)](media/v2-update-quickstart/quick-start-interval-change.png#lightbox)

1. Zaman aralığını genişleterek, sorunun durdurulup durdurulmadığını veya devam edip etmediğini belirleyebiliriz.

    * Zaman aralığını 60 güne genişletin.

      [![Süreyi 60 güne uzat](media/v2-update-quickstart/quick-start-expand-interval-range.png)](media/v2-update-quickstart/quick-start-expand-interval-range.png#lightbox)

1. Diğer algılayıcı veri noktaları, daha fazla bağlam sağlamak için eklenebilir. Daha fazla algılayıcı, sorunun anlaşılmasından daha fazla bilgi sahibi. Gerçek değerleri göstermek için bir işaret bırakmaya izin verin.

    1. **Oluşturucu sistemi**' ni seçin ve ardından üç algılayıcı seçin **: GridVoltagePhase1**, **GridVoltagePhase2** ve **GridVoltagePhase3**.
    1. Görünür alandaki son veri noktasında bir işaretleyici bırakın.

       [![İşaretleyici bırak](media/v2-update-quickstart/quick-start-drop-marker.png)](media/v2-update-quickstart/quick-start-drop-marker.png#lightbox)

    Voltaj sensörlerinden ikisi, çalışan comparably ve normal parametreler içinde. **GridVoltagePhase3** sensöri olduğu anlaşılıyor.

1. Yüksek düzeyde bağlamsal veriler eklendiğinde, 3. aşama devre, sorun için daha da büyük görünür. Şimdi, uyarının nedenine yönelik iyi bir lider sunuyoruz. Sorunu, bakım ekibimize başvuracak şekilde hazırlıyoruz.  

    * Görüntüyü aynı grafik ölçeğinde tüm **Oluşturucu sistem** algılayıcılarının kaplaması için değiştirin.

      [![Görüntüyü her şeyi içerecek şekilde değiştirin](media/v2-update-quickstart/quick-start-generator-system.png)](media/v2-update-quickstart/quick-start-generator-system.png#lightbox)

## <a name="next-steps"></a>Sonraki adımlar

Kendi Azure Time Series Insights Gen2 ortamınızı oluşturmaya hazırsınız. Başlamak için:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2 ortamınızı planlayın](./how-to-plan-your-environment.md)

Tanıtım ve özelliklerini kullanmayı öğrenin:

> [!div class="nextstepaction"]
> [Azure Time Series Insights Gen2 Explorer](./concepts-ux-panels.md)
