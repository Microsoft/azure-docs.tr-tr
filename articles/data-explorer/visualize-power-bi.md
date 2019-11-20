---
title: "Öğretici: Azure Veri Gezgini verilerini Power BI'da görselleştirme"
description: Bu öğreticide Power BI ile Azure Veri Gezgini'ne bağlanmayı ve verilerinizi görselleştirmeyi öğreneceksiniz.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: ec1579792a6e247bf49946bb8609a626154fbd46
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037352"
---
# <a name="tutorial-visualize-data-from-azure-data-explorer-in-power-bi"></a>Öğretici: Azure Veri Gezgini verilerini Power BI'da görselleştirme

Azure Veri Gezgini, günlük ve telemetri verileri için hızlı ve yüksek oranda ölçeklenebilir veri keşfetme hizmetidir. Power BI, verilerinizi görselleştirmenizi ve sonuçları kuruluşunuzda paylaşmanızı sağlayan bir iş analizi çözümüdür. Bu öğreticide ilk olarak Azure Veri Gezgini'nde görsel oluşturmayı öğreneceksiniz. Ardından Power BI ile Azure Veri Gezgini'ne bağlanıp örnek verileri temel alan bir rapor oluşturacak ve bu raporu Power BI hizmetinde yayımlayacaksınız.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir Azure hesabı](https://azure.microsoft.com/free/) oluşturun. Power BI Pro’ya kaydolmadıysanız başlamadan önce [ücretsiz deneme için kaydolun](https://app.powerbi.com/signupredirect?pbi_source=web).

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure Veri Gezgini'nde görsel oluşturma
> * Power BI Desktop'tan Azure Veri Gezgini'ne bağlanma
> * Power BI Desktop'ta verilerle çalışma
> * Görsel içeren rapor oluşturma
> * Raporu yayımlama ve paylaşma

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için Azure ve Power BI aboneliklerine ek olarak aşağıdakilere ihtiyacınız vardır:

* [Test kümesi ve veritabanı](create-cluster-database-portal.md)

* [StormEvents örnek verileri](ingest-sample-data.md). [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) ( **indirmeyi ücretsiz**Seç)

## <a name="render-visuals-in-azure-data-explorer"></a>Azure Veri Gezgini'nde görsel oluşturma

Power BI'a geçmeden önce Azure Veri Gezgini'nde görsel oluşturma adımlarını inceleyelim. Bu özellik verileri hızlıca analiz etmek için idealdir.

1. [https://dataexplorer.azure.com](https://dataexplorer.azure.com) adresinde oturum açın.

1. Soldaki bölmede StormEvents örnek verilerini içeren test veritabanını seçin.

1. Aşağıdaki sorguyu sağ taraftaki pencereye yapıştırın ve **Çalıştır**'ı seçin.

    ```Kusto
    StormEvents
    | summarize event_count=count() by State
    | where event_count > 1800
    | project State, event_count
    | sort by event_count
    | render columnchart
    ```

    Bu sorgu eyalete göre hava olaylarını sayar. Ardından 1800'den fazla hava olayı bulunan tüm eyaletleri içeren bir sütun grafik oluşturur.

    ![Olaylar sütun grafiği](media/visualize-power-bi/events-column-chart.png)

1. Aşağıdaki sorguyu sağ taraftaki pencereye yapıştırın ve **Çalıştır**'ı seçin.

    ```Kusto
    StormEvents
    | where State == "WASHINGTON" and StartTime >= datetime(2007-07-01) and StartTime <= datetime(2007-07-31)
    | summarize StormCount = count() by EventType
    | render piechart
    ```

    Bu sorgu Washington eyaletinde Temmuz ayında yaşanan hava olaylarını türe göre sayar. Ardından her olay türünün yüzdesini gösteren bir pasta grafiği oluşturur.

    ![Olaylar pasta grafiği](media/visualize-power-bi/events-pie-chart.png)

Artık Power BI'a geçebiliriz ancak Azure Veri Gezgini'nde yapabileceklerinizin bunlarla sınırlı olmadığını unutmayın.

## <a name="connect-to-azure-data-explorer"></a>Azure Veri Gezgini'ne bağlanma

Artık Power BI Desktop'tan Azure Veri Gezgini'ne bağlanabilirsiniz.

1. **Giriş** sekmesinde Power BI Desktop **veri al** ' ı **seçin.**

    ![Verileri alma](media/visualize-power-bi/get-data-more.png)

1. *Azure Veri Gezgini*araması yapın, **Azure Veri Gezgini (Beta)** seçeneğini belirleyin ve sonra **bağlantısını**yapın.

    ![Arama ve veri alma](media/visualize-power-bi/search-get-data.png)

1. **Bağlayıcıyı önizle** ekranında **Devam**'ı seçin.

1. Sonraki ekranda, test kümenizin ve veritabanınızın adını girin. Küme `https://<ClusterName>.<Region>.kusto.windows.net` biçiminde olmalıdır. Tablo adı olarak *StormEvents* yazın. Diğer seçenekleri varsayılan değerleriyle bırakın ve **Tamam**'ı seçin.

    ![Küme, veritabanı, tablo seçenekleri](media/visualize-power-bi/cluster-database-table.png)

1. Veri önizleme ekranında **Düzenle**'yi seçin.

    Tablo, Power Query Düzenleyicisi'nde açılır ve burada verileri içeri aktarmadan önce satırları ve sütunları düzenleyebilirsiniz.

## <a name="work-with-data-in-power-bi-desktop"></a>Power BI Desktop'ta verilerle çalışma

Azure Veri Gezgini'ne bağlandığınıza göre verileri Power Query Düzenleyicisi'nde düzenleyebilirsiniz. **BeginLat** sütunundaki null değerli satırları ve **StormSummary** JSON sütununun tamamını bırakın. Bunlar basit işlemlerdir ancak verileri içeri aktarırken karmaşık işlemler de gerçekleştirebilirsiniz.

1. **BeginLat** sütununun okunu seçin, **null** onay kutusunun işaretini kaldırın ve **Tamam**'ı seçin.

    ![Sütunu filtreleme](media/visualize-power-bi/filter-column.png)

1. **StormSummary** sütununun başlığına sağ tıklayıp **Kaldır**'ı seçin.

    ![Sütunu kaldırma](media/visualize-power-bi/remove-column.png)

1. **SORGU AYARLARI** bölmesinde adı *Query1* yerine *StormEvents* olarak değiştirin.

    ![Sorgu adını değiştirme](media/visualize-power-bi/query-name.png)

1. Şeridin **Giriş** sekmesinde **Kapat ve uygula**'yı seçin.

    ![Kapat ve uygula](media/visualize-power-bi/close-apply.png)

    Power Query değişikliklerinizi uygular ve örnek verileri bir *veri modeline* aktarır. Aşağıdaki adımlar bu modeli nasıl zenginleştirebileceğinizi göstermektedir. Bunlar da gerçekleştirebileceğiniz işlemlerle ilgili fikir vermek için örnek olarak verilmiştir.

1. Ana pencerenin sol tarafından veri görünümünü seçin.

    ![Veri görünümü](media/visualize-power-bi/data-view.png)

1. Şeridin **Modelleme** sekmesinde **Yeni sütun**'u seçin.

    ![Yeni sütun](media/visualize-power-bi/new-column.png)

1. Formül çubuğuna aşağıdaki Veri Çözümleme İfadeleri (DAX) formülünü girin ve Enter tuşuna basın.

    ```DAX
    DurationHours = DATEDIFF(StormEvents[StartTime], StormEvents[EndTime], hour)
    ```

    ![Formül çubuğu](media/visualize-power-bi/formula-bar.png)

    Bu formül, her bir hava olayının kaç saat sürdüğünü hesaplayan *DurationHours* sütununu oluşturur. Bu sütunu bir sonraki bölümde oluşturacağınız görselde kullanacaksınız.

1. Sütunu görmek için tablonun ekranı sağ tarafına doğru kaydırın.

## <a name="create-a-report-with-visuals"></a>Görsel içeren rapor oluşturma

Verileri içeri aktarıp veri modelini geliştirdiğinize göre görseller içeren bir rapor oluşturmaya başlayabilirsiniz. Olay süresini temel alan bir sütun grafik ve ekin hasarını gösteren bir harita ekleyeceksiniz.

1. Pencerenin sol tarafından rapor görünümünü seçin.

    ![Rapor görünümü](media/visualize-power-bi/report-view.png)

1. **GÖRSELLEŞTİRMELER** bölmesinde yığılmış sütun grafiği seçin.

    ![Sütun grafiği ekleme](media/visualize-power-bi/add-column-chart.png)

    Tuvale boş bir grafik eklenir.

    ![Boş grafik](media/visualize-power-bi/blank-chart.png)

1. **ALANLAR** listesinden **DurationHours** ve **State** alanlarını seçin.

    ![Alanları seçme](media/visualize-power-bi/select-fields.png)

    Artık bir yıl boyunca eyalete göre hava olaylarının toplam saatini gösteren bir grafiğe sahipsiniz.

    ![Süre sütun grafiği](media/visualize-power-bi/duration-column-chart.png)

1. Tuvalde sütun grafiğin dışındaki bir yere tıklayın.

1. **GÖRSELLEŞTİRMELER** bölmesinde haritayı seçin.

    ![Harita ekleme](media/visualize-power-bi/add-map.png)

1. **ALANLAR** listesinden **CropDamage** ve **State** alanlarını seçin. Haritayı ABD eyaletlerini net bir şekilde görecek şekilde boyutlandırın.

    ![Ekin zararı haritası](media/visualize-power-bi/crop-damage-map.png)

    Kabarcıkların boyutu ekin hasarının ABD doları cinsinden değerini temsil eder. Ayrıntıları görmek için farenizle kabarcıkların üzerine gelin.

1. Görselleri taşıyıp yeniden boyutlandırarak raporunuzun aşağıdaki görüntüye benzemesini sağlayın.

    ![Tamamlanmış rapor](media/visualize-power-bi/finished-report.png)

1. Raporu *storm-events.pbix* adıyla kaydedin.

## <a name="publish-and-share-the-report"></a>Raporu yayımlama ve paylaşma

Bu noktaya kadar Power BI'da gerçekleştirdiğiniz tüm işlemleri Power BI Desktop uygulamasını kullanarak yerel ortamda tamamladınız. Şimdi raporu başkalarıyla paylaşmak için Power BI hizmetine yükleyeceksiniz.

1. Power BI Desktop'ta şeridin **Giriş** sekmesinde **Yayımla**'yı seçin.

    ![Yayımla düğmesi](media/visualize-power-bi/publish-button.png)

1. Önceden Power BI oturumu açmadıysanız, oturum açma işlemlerini tamamlayın.

1. **Çalışma alanım**'a ve ardından **Seç**'e tıklayın.

    ![Çalışma alanını seçme](media/visualize-power-bi/select-workspace.png)

1. Yayımlama işlemi tamamlandığında **storm-events.pbix adlı dosyayı Power BI'da aç**'ı seçin.

    ![Yayımlama başarılı](media/visualize-power-bi/publishing-succeeded.png)

    Rapor, hizmette açılır ve Power BI Desktop'ta tanımladığınız görseller ve belirlediğiniz düzen görüntülenir.

1. Raporun sağ üst köşesinden **Paylaş**'ı seçin.

    ![Paylaş düğmesi](media/visualize-power-bi/share-button.png)

1. **Raporu paylaş** ekranında kuruluşunuzdaki iş arkadaşlarınızdan birini seçin, not ekleyin ve **Paylaş**'ı seçin.

    ![Raporu paylaşma](media/visualize-power-bi/share-report.png)

    İş arkadaşınız uygun izinlere sahipse paylaştığınız rapora erişebilir.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Oluşturduğunuz raporu saklamak istemiyorsanız *storm-events.pbix* dosyasını silmeniz yeterlidir. Yayımladığınız raporu kaldırmak istiyorsanız aşağıdaki adımları izleyin.

1. **Çalışma alanım** sayfasında **RAPORLAR** bölümüne inin ve **storm-events** adlı raporu bulun.

1. **storm-events** girişinin yanındaki üç noktayı ( **. . .** ) ve ardından **KALDIR**'ı seçin.

    ![Raporu kaldırma](media/visualize-power-bi/remove-report.png)

1. Kaldırma işlemini onaylayın.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Sorgu yazma](write-queries.md)
