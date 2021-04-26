---
title: Ortamınızı Power BI Azure Time Series Insights bağlama | Microsoft Docs
description: Kuruluşunuz genelinde verileri paylaşmak, grafik ve görüntüleme Power BI Azure Time Series Insights nasıl bağlayacağınızı öğrenin.
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
services: time-series-insights
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 38403eed56dc718afdfce13375dd2662beb13eb6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374176"
---
# <a name="visualize-data-from-azure-time-series-insights-in-power-bi"></a>Power BI Azure Time Series Insights verileri görselleştirme

Azure Time Series Insights, bulutta zaman serisi verilerini depolamak, yönetmek, sorgulamak ve görselleştirmek için bir platformdur. [Power BI](https://powerbi.microsoft.com) , kuruluşunuz genelinde Öngörüler ve sonuçlar paylaşmanıza olanak sağlayan zengin görselleştirme özelliklerine sahip bir iş analizi aracıdır. Her iki hizmet de güçlü veri görselleştirme ve Power BI kolay paylaşım özellikleri ile Azure Time Series Insights güçlü analizine sahip olabilirsiniz.

Şunları öğrenirsiniz:

* Yerel Azure Time Series Insights bağlayıcısını kullanarak Power BI Azure Time Series Insights bağlama
* Power BI zaman serisi verileriniz ile görseller oluşturun
* Power BI için raporu yayımlayın ve kuruluşunuzun geri kalanıyla paylaşabilirsiniz


## <a name="prerequisites"></a>Önkoşullar

* Henüz yoksa ücretsiz bir [Azure aboneliğine](https://azure.microsoft.com/free/) kaydolun.
* [Power BI Desktop](https://powerbi.microsoft.com/downloads/) en son sürümünü indirin ve yükleyin
* [Azure Time Series Insights Gen2 ortamı](./how-to-provision-manage.md) oluşturun veya oluşturun

Lütfen [ortam erişim ilkelerini](./concepts-access-policies.md) gözden geçirin ve Azure Time Series Insights Gen2 ortamına doğrudan erişim veya Konuk erişiminiz olduğundan emin olun. 

> [!IMPORTANT]
> * [Power BI Desktop](https://powerbi.microsoft.com/downloads/)en son sürümünü indirip yükleyin. Bu makaledeki adımlarla birlikte takip etmek için, Power BI Desktop yüklü olan en azından Aralık 2020 (2.88.321.0) sürümüne sahip olduğunuzdan emin olun. 

## <a name="export-data-from-azure-time-series-insights-into-power-bi-desktop"></a>Azure Time Series Insights verileri Power BI masaüstüne aktarma

Başlamak için:

1. Azure Time Series Insights Gen2 Gezginini açın ve verilerinizi seçin. Power BI içine aktarılacak veriler budur.
1. Memnun olduğunuz bir görünümü oluşturduktan sonra, **diğer eylemler** açılan menüsüne gidin ve **Power BI Bağlan**' ı seçin.

    [![Azure Time Series Insights Gen2 Explorer dışarı aktarma](media/how-to-connect-power-bi/export-from-explorer.jpg)](media/how-to-connect-power-bi/export-from-explorer.jpg#lightbox)

1. Dışarı aktarma için parametrelerinizi ayarlayın:

   * **Veri biçimi**: Power BI **Toplam verileri** veya **Ham olayları** dışarı aktarmak isteyip istemediğinizi seçin. 

       > [!NOTE]
       > Ham olayları dışa aktardığınızda, bu verileri daha sonra Power BI içinde toplayabilirsiniz. Ancak, toplu verileri dışa aktardığınızda Power BI ham verilere döndüremezsiniz. Ham olay düzeyi verileri için 250.000 olay sayısı sınırı vardır.

   * **Zaman aralığı**: Power BI ' de **sabit** bir zaman aralığı veya **en son** verileri görmek isteyip istemediğinizi seçin. Sabit zaman aralığının seçilmesi, yaptığınız arama alanındaki verilerin Power BI aktarılacağı anlamına gelir. En son zaman aralığını seçmek, Power BI seçtiğiniz arama kapsamı için en son verileri alacak anlamına gelir (örneğin, 1 saatlik veri Grafikleriniz ve "en son" ayarını seçerseniz Power BI bağlayıcı her zaman en son 1 saatlik veri için sorgular yapar.)
  
   * **Mağaza türü**: seçtiğiniz sorguyu **yarı depoya** veya **soğuk depoya** karşı çalıştırmak isteyip istemediğinizi seçin. Hem soğuk hem de ısınma mağazalarını kapsayan bir Aralık seçtiyseniz, sıcak mağaza yalnızca en son verileri içermesinden, sorgunuz varsayılan olarak soğuk depoya yönlendirilir. Stoyeniden yazma parametresinin el ile değiştirilmesine izin verilir, ancak en iyi deneyim için önerilmez. 

    > [!TIP] 
    > Azure Time Series Insights Gezgini, dışarı aktarmayı seçtiğiniz verilerin bulunduğu arama aralığına ve görünümüne bağlı olarak önerilen parametreleri otomatik olarak seçer. 

1. Ayarlarınızı yapılandırdıktan sonra **sorguyu panoya kopyala**' yı seçin.

    [![Azure Time Series Insights Explorer 'ın kalıcı olarak dışarı aktarılması](media/how-to-connect-power-bi/choose-explorer-parameters.jpg)](media/how-to-connect-power-bi/choose-explorer-parameters.jpg#lightbox)

1. Power BI Desktop'ı başlatın.
   
1. **Giriş** sekmesinde Power BI Desktop ' de, sol üst köşedeki **verileri al** ' ı seçin ve daha sonra **daha fazlasını** yapın.

    [![Power BI’da verileri alma](media/how-to-connect-power-bi/get-data-power-bi.jpg)](media/how-to-connect-power-bi/get-data-power-bi.jpg#lightbox)

1. **Azure Time Series Insights** arayın, **Azure Time Series Insights (Beta)** öğesini seçin ve sonra **bağlanın**.

    [![Power BI Azure Time Series Insights bağlama](media/how-to-connect-power-bi/select-tsi-connector.jpg)](media/how-to-connect-power-bi/select-tsi-connector.jpg#lightbox)

    Alternatif olarak, **Azure** sekmesine gidin, **Azure Time Series Insights (Beta)** öğesini seçin ve sonra **bağlantısını** yapın.

1. Azure Time Series Insights Gezgini ' nden kopyaladığınız sorguyu **özel sorgu** alanına yapıştırın ve sonra **Tamam**' a basın.

    [![Özel sorguyu yapıştırın ve Tamam ' ı seçin.](media/how-to-connect-power-bi/custom-query-load.png)](media/how-to-connect-power-bi/custom-query-load.png#lightbox)  

1.  Veri tablosu şimdi yüklenir. Power BI yüklemek için **Yükle** ' ye basın. Verilerde herhangi bir dönüştürme yapmak istiyorsanız, **verileri Dönüştür**' e tıklayarak bunu şimdi yapabilirsiniz. Ayrıca, yükledikten sonra verilerinizi dönüştürebilirsiniz.

    [![Tablodaki verileri gözden geçirin ve Yükle ' yi seçin.](media/how-to-connect-power-bi/review-the-loaded-data-table.png)](media/how-to-connect-power-bi/review-the-loaded-data-table.png#lightbox)  

## <a name="create-a-report-with-visuals"></a>Görsel içeren rapor oluşturma

Verileri Power BI 'e aktardığınıza göre, görsellerin bulunduğu bir rapor oluşturmak biraz zaman alabilir.

1. Pencerenin sol tarafında, **rapor** görünümünü seçtiğinizden emin olun.

    [![Ekran görüntüsü rapor görünümü simgesini gösterir.](media/how-to-connect-power-bi/select-the-report-view.png)](media/how-to-connect-power-bi/select-the-report-view.png#lightbox)

1. **Görsel öğeler** sütununda, istediğiniz görseli seçin. Örneğin, **çizgi grafik**' i seçin. Bu, Tuvalinize boş bir çizgi grafik ekler.

1.  **Alanlar** listesinde **_Timestamp** seçin ve X ekseninde saati göstermek için **eksen** alanına sürükleyin. **Eksenin** değeri olarak **_Timestamp** ' ye geçdiğinizden emin olun (varsayılan **Tarih hiyerarşisi**).

    [![_Timestamp seçin](media/how-to-connect-power-bi/select-timestamp.png)](media/how-to-connect-power-bi/select-timestamp.png#lightbox)

1.  Yine **alanlar** listesinde, çizmek istediğiniz değişkeni seçin ve değerleri Y ekseni üzerinde göstermek için **değerler** alanına sürükleyin. Zaman serisi KIMLIK değerini seçin ve grafikte birden çok satır oluşturmak için, her zaman seri KIMLIĞI için bir tane olmak üzere **gösterge** alanına sürükleyin. Bu, Azure Time Series Insights Gezgini tarafından sağlananlara benzer bir görünüm işler! 

    [![Temel çizgi grafik oluşturma](media/how-to-connect-power-bi/power-bi-line-chart.png)](media/how-to-connect-power-bi/power-bi-line-chart.png#lightbox)

1. Tuvalinize başka bir grafik eklemek için, çizgi grafik dışında tuval üzerinde herhangi bir yeri seçin ve bu işlemi tekrarlayın.

    [![Paylaşılacak ek grafikler oluşturma](media/how-to-connect-power-bi/power-bi-additional-charts.png)](media/how-to-connect-power-bi/power-bi-additional-charts.png#lightbox)

Raporunuzu oluşturduktan sonra, Power BI Reporting Services 'e yayımlayabilir ve kuruluşunuzdaki diğer kişilerle paylaşabilirsiniz.

## <a name="advanced-editing"></a>Gelişmiş Düzenle
Power BI bir veri kümesini zaten yüklediyseniz ancak sorguyu değiştirmek istiyorsanız (Tarih/saat veya ortam KIMLIĞI parametreleri gibi), bunu Power BI Gelişmiş Düzenleyici işlevleri aracılığıyla yapabilirsiniz. **Power Query düzenleyicisini** kullanarak nasıl değişiklik yapılacağı hakkında daha fazla bilgi edinmek için [Power BI belgelerine](/power-bi/desktop-query-overview) bakın. 

## <a name="next-steps"></a>Sonraki Adımlar

* [Power BI Masaüstü](/power-bi/desktop-query-overview)hakkında daha fazla bilgi edinin.

* Azure Time Series Insights Gen2 ' deki [verileri sorgulama](concepts-query-overview.md) hakkında bilgi edinin.