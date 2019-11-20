---
title: 'Hızlı başlangıç: Önizleme tanıtım ortamını araştırma-Azure Time Series Insights | Microsoft Docs'
description: Örnek hızlı başlangıç aracılığıyla Azure Time Series Insights önizleme tanıtım ortamları hakkında bilgi edinin.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 10/24/2019
ms.openlocfilehash: 61d13831ff0d8810cfdce35f86a0402cb1679ad1
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014765"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Hızlı başlangıç: Azure Time Series Insights Preview demo ortamını keşfet

Bu hızlı başlangıç, Azure Time Series Insights Preview ortamını kullanmaya başlamanızı ister. Ücretsiz gösteride, Time Series Insights önizlemeye eklenen önemli özellikler turuna katılın.

Time Series Insights Preview demo ortamı, iki rüzgar türbin grubu çalıştıran bir senaryo şirketi olan contoso içerir. Her grupta 10 türbines vardır. Her türbin, verileri her dakika Azure IoT Hub rapor eden 20 sensöre sahiptir. Algılayıcılar, hava durumu koşulları, dikey pencere sıklığı ve yaw konumu hakkında bilgi toplar. Üretici performansı, Gearbox davranışı ve güvenlik izleyicileri ile ilgili bilgiler de kaydedilir.

Bu hızlı başlangıçta, contoso verilerinde eyleme dönüştürülebilir Öngörüler bulmak için Time Series Insights kullanmayı öğreneceksiniz. Ayrıca kritik sorunları daha iyi tahmin etmek ve bakım gerçekleştirmek için kısa bir kök neden analizi de gerçekleştirirsiniz.

> [!IMPORTANT]
> Yoksa [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) oluşturun oluşturun.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Tanıtım ortamında Time Series Insights Gezginini keşfet

Time Series Insights önizleme Gezgini geçmiş verileri ve kök neden analizini gösterir. Başlamak için:

1.  [Contoso rüzgar grubu tanıtım](https://insights.timeseries.azure.com/preview/samples) ortamına gidin.  

1. İstenirse, Azure hesabı kimlik bilgilerinizi kullanarak Time Series Insights Gezgini ' nde oturum açın.

## <a name="work-with-historical-data"></a>Geçmiş verilerle çalışma

1. **Contoso bitki 1**' de rüzgar türbin **W7**bölümüne bakın.  

   1. Görünüm aralığını **1/1/17 20:00:00.00 olarak 3/10/17 20:00:00.00 (UTC)** olarak değiştirin.
   1. Bir algılayıcı seçmek için **contoso bitki 1** > **W7** > **Generator sistem** > **generatorspeed**' i seçin. Ardından, gösterilen değerleri gözden geçirin.

      [Contoso tesis 1 ' de ![W7](media/v2-update-quickstart/quickstart-one.png)](media/v2-update-quickstart/quickstart-one.png#lightbox)

1. Contoso, Rüzgar Turbin **W7**içinde bir yangın buldu. Opın ons, yangın ne olduğuna ilişkin farklılık gösterir. Time Series Insights, yangın uyarı algılayıcısının yangın sırasında etkinleştirildiğini görebiliriz.

   1. Görünüm aralığını **3/9/17 20:00:00.00 olarak 3/10/17 20:00:00.00 (UTC)** olarak değiştirin.
   1. **Güvenlik sistemi** > **firealert**' i seçin.

      [![contoso, Rüzgar Turbin W7 'de bir yangın buldu](media/v2-update-quickstart/quickstart-two.png)](media/v2-update-quickstart/quickstart-two.png#lightbox)

1. Ne olduğunu anlamak için yangın süresi etrafında diğer olayları gözden geçirin. Yağ baskısı ve etkin uyarılar, yalnızca yangın 'tan önce.

   1. **Aralıklı sistem** > **Hidrolik Licoilbasınç**' ı seçin.
   1. **Aralıklı sistem** > **activewarning**öğesini seçin.

      [aynı anda diğer olayları gözden ![](media/v2-update-quickstart/quickstart-three.png)](media/v2-update-quickstart/quickstart-three.png#lightbox)

1. Yağ basıncı ve etkin uyarı sensörleri, yangın 'tan önce. Daha önce harekete geçmiş olan diğer işaretleri görmek için görüntülenen zaman serisini genişletin. Her iki algılayıcı zaman içinde sürekli olarak dalgalanılır. Dalgalanmalar kalıcı ve worrisme deseninin olduğunu gösterir.

    * Görünüm aralığını **2/24/17 20:00:00.00 olarak 3/10/17 20:00:00.00 (UTC)** olarak değiştirin.

      [Yağ basıncı ve etkin uyarı algılayıcıları ![de spıked](media/v2-update-quickstart/quickstart-four.png)](media/v2-update-quickstart/quickstart-four.png#lightbox)

1. İki yıllık geçmiş verileri incelemek, aynı algılayıcı dalgalanmalarına sahip başka bir yangın olayını ortaya çıkarır.

    * Görünüm aralığını **1/1/16, 12/31/17** (tüm veriler) olarak değiştirin.

      [geçmiş desenleri aramak ![](media/v2-update-quickstart/quickstart-five.png)](media/v2-update-quickstart/quickstart-five.png#lightbox)

Time Series Insights ve algılayıcı telemetrisi kullanarak, geçmiş verilerde uzun süreli bir eğilim gizliyoruz. Bu yeni Öngörüler sayesinde şunları yapabilirsiniz:

* Gerçekten ne olduğunu açıklayın.
* Sorunu giderin.
* Daha iyi uyarı bildirimi sistemlerini yerine koyun.

## <a name="root-cause-analysis"></a>Kök neden analizi

1. Bazı senaryolar, verilerdeki ipuçlarına yönelik gelişmiş analizler gerektirir. **6/25**tarihinde Windmill **W6** seçin.

    1. Görünüm aralığını **6/1/17 20:00:00.00 olarak 7/1/17 20:00:00.00 (UTC)** olarak değiştirin.
    1. **Contoso bitki 1** > **W6** > **güvenlik sistemi** > **VoltageActuatorSwitchWarning**' nı seçin.

       [![görünüm aralığını değiştirin ve W6 seçin](media/v2-update-quickstart/quickstart-six.png)](media/v2-update-quickstart/quickstart-six.png#lightbox)

1. Uyarı, oluşturucunun gerilimiyle ilgili bir sorun olduğunu gösterir. Oluşturucunun genel güç çıkışı, geçerli aralıktaki normal parametreler içindedir. Zaman sıklığımızı artırarak, başka bir model ortaya çıktı görürsünüz. Bir bırakma kapalı olur.

    1. **VoltageActuatorSwitchWarning** algılayıcısı 'nı kaldırın.
    1. **Generator sistem** > **activepower**öğesini seçin.
    1. Aralığı **3B**olarak değiştirin.

       [![aralığı 3B olarak değiştirme](media/v2-update-quickstart/quickstart-seven.png)](media/v2-update-quickstart/quickstart-seven.png#lightbox)

1. Zaman aralığını genişleterek, sorunun durdurulup durdurulmadığını veya devam edip etmediğini belirleyebiliriz.

    * Zaman aralığını 60 güne genişletin.

      [![zaman aralığını 60 güne uzat](media/v2-update-quickstart/quickstart-eight.png)](media/v2-update-quickstart/quickstart-eight.png#lightbox)

1. Diğer algılayıcı veri noktaları, daha fazla bağlam sağlamak için eklenebilir. Daha fazla algılayıcı, sorunun anlaşılmasından daha fazla bilgi sahibi. Şimdi gerçek değerleri görmek için bir işaret bırakın. 

    1. **Oluşturucu sistemi**' ni seçin ve ardından üç algılayıcı seçin **: GridVoltagePhase1**, **GridVoltagePhase2**ve **GridVoltagePhase3**.
    1. Görünür alandaki son veri noktasında bir işaretleyici bırakın.

       [işaretleyici ![bırakma](media/v2-update-quickstart/quickstart-nine.png)](media/v2-update-quickstart/quickstart-nine.png#lightbox)

    Voltaj sensörlerinden ikisi, çalışan comparably ve normal parametreler içinde. **GridVoltagePhase3** sensöri olduğu anlaşılıyor.

1. Yüksek düzeyde bağlamsal veriler eklendiğinde, 3. aşama devre, sorun için daha da büyük görünür. Şimdi, uyarının nedenine yönelik iyi bir lider sunuyoruz. Sorunu, bakım ekibimize başvuracak şekilde hazırlıyoruz.  

    * Görüntüyü aynı grafik ölçeğinde tüm **Oluşturucu sistem** algılayıcılarının kaplaması için değiştirin.

      [![her şeyi içerecek şekilde değiştirin](media/v2-update-quickstart/quickstart-ten.png)](media/v2-update-quickstart/quickstart-ten.png#lightbox)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Hızlı başlangıcı tamamladığınıza göre, oluşturduğunuz kaynakları temizleyin:

1. [Azure Portal](https://portal.azure.com)sol menüden **tüm kaynaklar**' ı seçin ve Azure Time Series Insights kaynak grubunuzu bulun.
1. Her kaynağı ayrı ayrı **Sil** veya Kaldır ' a tıklayarak kaynak grubunun tamamını (ve içinde yer alan tüm kaynakları) silin.

## <a name="next-steps"></a>Sonraki adımlar

Kendi Time Series Insights önizleme ortamınızı oluşturmaya hazırsınız. Başlamak için:

> [!div class="nextstepaction"]
> [Time Series Insights Preview ortamınızı planlayın](time-series-insights-update-plan.md)

Tanıtım ve özelliklerini kullanmayı öğrenin:

> [!div class="nextstepaction"]
> [Time Series Insights önizleme Gezgini](time-series-insights-update-explorer.md)