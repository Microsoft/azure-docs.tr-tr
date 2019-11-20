---
title: Azure Cosmos DB, Azure Analysis Services ve Power BI kullanarak gerçek zamanlı bir pano oluşturun
description: Azure Cosmos DB ve Azure Analysis Services kullanarak Power BI canlı hava durumu panosu oluşturmayı öğrenin.
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: bharathb
ms.reviewer: sngun
ms.openlocfilehash: d225a14edddcad58c08094dbc758d67df8f834e6
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376599"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>Azure Cosmos DB ve Power BI kullanarak gerçek zamanlı pano oluşturma

Bu makalede Azure Cosmos DB ve Azure Analysis Services kullanarak Power BI canlı bir hava durumu panosu oluşturmak için gereken adımlar açıklanmaktadır. Power BI panosunda, bir bölgedeki sıcaklık ve kinfhakkında gerçek zamanlı bilgileri göstermek için grafikler görüntülenir.

## <a name="reporting-scenarios"></a>Raporlama senaryoları

Azure Cosmos DB depolanan veriler üzerinde raporlama panoları kurmanın birden çok yolu vardır. Eskime gereksinimlerine ve verilerin boyutuna bağlı olarak, aşağıdaki tabloda her senaryo için Raporlama kurulumu açıklanmaktadır:


|Senaryo |Kurulum |
|---------|---------|
|1. Geçici raporlar oluşturma (yenileme yok)    |  [İçeri aktarma modundaki Azure Cosmos DB bağlayıcısını Power BI](powerbi-visualize.md)       |
|2. Düzenli yenileme ile geçici raporlar oluşturma   |  [İçeri aktarma modundaki Azure Cosmos DB bağlayıcısını Power BI (zamanlanmış düzenli yenileme)](powerbi-visualize.md)       |
|3. Büyük veri kümelerinde raporlama (< 10 GB)     |  Artımlı Yenilemesiz Azure Cosmos DB Bağlayıcısı Power BI       |
|4. Büyük veri kümelerinde gerçek zamanlı raporlama    |  Doğrudan sorgu + Azure Analysis Services (Azure Cosmos DB Bağlayıcısı) ile Azure Analysis Services Bağlayıcısı Power BI       |
|5. Toplamalarda canlı veriler hakkında raporlama     |  [Doğrudan sorgu + Azure Databricks + Cosmos DB Spark Bağlayıcısı ile Spark bağlayıcısını Power BI.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6. Büyük veri kümelerinde toplamalarda canlı veriler hakkında raporlama   |  Doğrudan sorgu + Azure Analysis Services + Azure Databricks + Cosmos DB Spark Bağlayıcısı ile Azure Analysis Services bağlayıcısını Power BI.       |

1 ve 2. senaryolar Azure Cosmos DB Power BI bağlayıcısı kullanılarak kolayca ayarlanabilir. Bu makalede, 3 ve 4 senaryolarına yönelik ayarlar açıklanmaktadır.

### <a name="power-bi-with-incremental-refresh"></a>Artımlı Yenilemesiz Power BI

Power BI artımlı yenilemenin yapılandırılabileceği bir mod vardır. Bu mod Azure Analysis Services bölümlerinin oluşturulması ve yönetilmesi gereksinimini ortadan kaldırır. Artımlı yenileme, büyük veri kümelerinde yalnızca en son güncelleştirmeleri filtrelemek için ayarlanabilir. Ancak, bu mod yalnızca 10 GB 'lık veri kümesi sınırlaması olan Power BI Premium hizmeti ile birlikte kullanılır.

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis Connector + Azure Analysis Services

Azure Analysis Services, bulutta kurumsal düzeyde veri modelleri barındıran bir hizmet olarak tam olarak yönetilen bir platform sağlar. Büyük ölçekli veri kümeleri, Azure Analysis Services Azure Cosmos DB yüklenebilir. Veri kümesinin tamamını her zaman sorgulamadan kaçınmak için, veri kümeleri, farklı sıklıklardan bağımsız olarak yenilenebilen Azure Analysis Services bölümlerine ayrılabilir.

## <a name="power-bi-incremental-refresh"></a>Artımlı yenilemeyi Power BI

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Azure Cosmos DB Hava durumu verileri alma

Azure Cosmos DB [Hava durumu verilerini](https://catalog.data.gov/dataset/local-weather-archive) yüklemek için bir giriş işlem hattı ayarlayın. En son hava durumu verilerini, HTTP kaynağı ve Cosmos DB havuzunu kullanarak Azure Cosmos DB düzenli aralıklarla yüklemek için bir [Azure Data Factory (ADF)](../data-factory/connector-azure-cosmos-db.md) işi ayarlayabilirsiniz.


### <a name="connect-power-bi-to-azure-cosmos-db"></a>Power BI Azure Cosmos DB bağlama

1. **Azure Cosmos hesabını Power BI bağlama** -Power BI Desktop açın ve Azure Cosmos DB bağlayıcısını kullanarak doğru veritabanını ve kapsayıcıyı seçin.

   ![Azure Cosmos DB Power BI bağlayıcısı](./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png)

1. **Artımlı yenilemeyi yapılandırma** -veri kümesi için artımlı yenilemeyi yapılandırmak üzere [Power BI ile artımlı yenileme](/power-bi/service-premium-incremental-refresh) makalesindeki adımları izleyin. **RangeStart** ve **rangeend** parametrelerini aşağıdaki ekran görüntüsünde gösterildiği gibi ekleyin:

   ![Aralık parametrelerini Yapılandır](./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png)

   Veri kümesinin metin biçiminde bir tarih sütunu olduğundan, **RangeStart** ve **rangeend** parametrelerinin aşağıdaki filtreyi kullanacak şekilde dönüştürülmesi gerekir. **Gelişmiş Düzenleyici** bölmesinde sorgunuzu değiştirin, satırları RangeStart ve rangeend parametrelerine göre filtrelemek için aşağıdaki metni ekleyin:

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   Kaynak veri kümesinde hangi sütun ve veri türünün mevcut olduğuna bağlı olarak, RangeStart ve RangeEnd alanlarını uygun şekilde değiştirebilirsiniz

   
   |Özellik  |Veri türü  |Filtre  |
   |---------|---------|---------|
   |_ts     |   Numeric      |  [_ts] > süresi. TotalSeconds (RangeStart-#datetime (1970, 1, 1, 0, 0, 0)) ve [_ts] < Duration. TotalSeconds (RangeEnd-#datetime (1970, 1, 1, 0, 0, 0)))       |
   |Tarih (örneğin:-2019-08-19)     |   Dize      | [Document. Date] > DateTime. ToText (RangeStart, "yyyy-aa-gg") ve [Document. Date] < DateTime. ToText (RangeEnd, "yyyy-aa-gg")        |
   |Tarih (örneğin:-2019-08-11 12:00:00)   |  Dize       |  [Document. Date] > DateTime. ToText (RangeStart, "yyyy-mm-dd HH: mm: ss") ve [Document. Date] < DateTime. ToText (RangeEnd, "yyyy-AA-GG SS: DD: ss")       |


1. **Yenileme Ilkesini tanımlayın** -tablonun **bağlam** menüsündeki **artımlı yenileme** sekmesine giderek yenileme ilkesini tanımlayın. Yenileme ilkesini **her gün** yenilemek ve son ay verilerini depolamak için ayarlayın.

   ![Yenileme ilkesini tanımla](./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png)

   *D sorgusunun katlanacak şekilde onaylanamadığını*belirten uyarıyı yoksayın. Azure Cosmos DB Bağlayıcısı sorguları filtreler.

1. **Verileri yükleme ve raporları oluşturma** -daha önce yüklediğiniz verileri kullanarak sıcaklık ve Korni raporlamak üzere grafikleri oluşturun.

   ![Veri yükle ve rapor oluştur](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

1. **Raporu Power BI Premium 'A yayımlayın** -artımlı yenileme yalnızca Premium bir özellik olduğundan, Yayımla iletişim kutusu yalnızca Premium kapasitede bir çalışma alanının seçilmesine izin verir. İlk yenilemenin geçmiş verilerin içeri aktarılması daha uzun sürebilir. Sonraki veri yenilemeleri, artımlı yenileme kullandığından çok daha hızlıdır.


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis Connector + Azure Analysis Services 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Azure Cosmos DB Hava durumu verileri alma 

Azure Cosmos DB [Hava durumu verilerini](https://catalog.data.gov/dataset/local-weather-archive) yüklemek için bir giriş işlem hattı ayarlayın. En son hava durumu verilerini, HTTP kaynağı ve Cosmos DB havuzunu kullanarak Azure Cosmos DB düzenli aralıklarla yüklemek için bir Azure Data Factory (ADF) işi ayarlayabilirsiniz.

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>Azure Analysis Services Azure Cosmos hesabına bağlama

1. **Yeni bir Azure Analysis Services kümesi** - oluşturun Azure Cosmos hesabı ve databricks kümesiyle aynı bölgede[Azure Analysis Services örneği oluşturun](../analysis-services/analysis-services-create-server.md) .

1. **Visual Studio** -  'da yeni bir Analysis Services tablosal projesi oluşturma[SQL Server veri araçları (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017) ve Visual Studio 'da Analysis Services tablolu bir proje oluşturun.

   ![Azure Analysis Services projesi oluştur](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png)

   **Tümleşik çalışma alanı** örneğini seçin ve uyumluluk düzeyini **SQL Server 2017/Azure Analysis Services olarak ayarlayın (1400)**

   ![Azure Analysis Services tablolu model Tasarımcısı](./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png)

1. **Azure Cosmos DB veri kaynağını ekleyin** - **modeller**> **veri kaynakları** > **Yeni veri kaynağı** ' na gidin ve aşağıdaki ekran görüntüsünde gösterildiği gibi Azure Cosmos DB veri kaynağını ekleyin:

   ![Cosmos DB veri kaynağı Ekle](./media/create-real-time-weather-dashboard-powerbi/add-data-source.png)

   **Hesap URI 'si**, **veritabanı adı**ve **kapsayıcı adı**sağlayarak Azure Cosmos DB bağlanın. Artık Azure Cosmos kapsayıcısından alınan verileri Power BI olarak görebilirsiniz.

   ![Azure Cosmos DB verilerini Önizle](./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png)

1. **Analysis Services modelini oluşturun** -sorgu düzenleyicisini açın, yüklenen veri kümesini iyileştirmek için gerekli işlemleri gerçekleştirin:

   * Yalnızca hava durumu ile ilgili sütunları Ayıkla (sıcaklık ve kinftümü)

   * Tablodaki ay bilgilerini ayıklayın. Bu veriler, sonraki bölümde açıklandığı gibi bölüm oluşturmak için faydalıdır.

   * Sıcaklık sütunlarını sayıya Dönüştür

   Elde edilen a ifadesi aşağıdaki gibidir:

   ```
    let
        Source=#"DocumentDB/https://[ACCOUNTNAME].documents.azure.com:443/",
        #"Expanded Document" = Table.ExpandRecordColumn(Source, "Document", {"id", "_rid", "_self", "_etag", "fogground", "snowfall", "dust", "snowdepth", "mist", "drizzle", "hail", "fastest2minwindspeed", "thunder", "glaze", "snow", "ice", "fog", "temperaturemin", "fastest5secwindspeed", "freezingfog", "temperaturemax", "blowingsnow", "freezingrain", "rain", "highwind", "date", "precipitation", "fogheavy", "smokehaze", "avgwindspeed", "fastest2minwinddir", "fastest5secwinddir", "_attachments", "_ts"}, {"Document.id", "Document._rid", "Document._self", "Document._etag", "Document.fogground", "Document.snowfall", "Document.dust", "Document.snowdepth", "Document.mist", "Document.drizzle", "Document.hail", "Document.fastest2minwindspeed", "Document.thunder", "Document.glaze", "Document.snow", "Document.ice", "Document.fog", "Document.temperaturemin", "Document.fastest5secwindspeed", "Document.freezingfog", "Document.temperaturemax", "Document.blowingsnow", "Document.freezingrain", "Document.rain", "Document.highwind", "Document.date", "Document.precipitation", "Document.fogheavy", "Document.smokehaze", "Document.avgwindspeed", "Document.fastest2minwinddir", "Document.fastest5secwinddir", "Document._attachments", "Document._ts"}),
        #"Select Columns" = Table.SelectColumns(#"Expanded Document",{"Document.temperaturemin", "Document.temperaturemax", "Document.rain", "Document.date"}),
        #"Duplicated Column" = Table.DuplicateColumn(#"Select Columns", "Document.date", "Document.month"),
        #"Extracted First Characters" = Table.TransformColumns(#"Duplicated Column", {{"Document.month", each Text.Start(_, 7), type text}}),
        #"Sorted Rows" = Table.Sort(#"Extracted First Characters",{{"Document.date", Order.Ascending}}),
        #"Changed Type" = Table.TransformColumnTypes(#"Sorted Rows",{{"Document.temperaturemin", type number}, {"Document.temperaturemax", type number}}),
        #"Filtered Rows" = Table.SelectRows(#"Changed Type", each [Document.month] = "2019-07")
    in
        #"Filtered Rows"
   ```

   Ayrıca, bu değerlerin Power BI çizildiğinizden emin olmak için sıcaklık sütunlarının veri türünü ondalık olarak değiştirin.

1. **Azure Analysis Partitions oluşturma** -veri kümesini bağımsız olarak ve farklı sıklıklardan yenilenebilen mantıksal bölümlere bölmek için Azure Analysis Services bölüm oluşturun. Bu örnekte, veri kümesini en son ayın verilerine ve diğer her şeye bölmek için iki bölüm oluşturursunuz.

   ![Analysis Services bölümleri oluşturma](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png)

   Azure Analysis Services içinde aşağıdaki iki bölümü oluşturun:

   * **En son ay** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **Eskiye** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **Modeli Azure Analiz sunucusu dağıtma** -Azure Analysis Services projeye sağ tıklayıp **Dağıt**' ı seçin. Sunucu adını **dağıtım sunucusu Özellikler** bölmesine ekleyin.

   ![Azure Analysis Services modeli dağıt](./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png)

1. **Bölüm yenilemelerini ve birleştirmeleri yapılandırma** -Azure Analysis Services bölümlerin bağımsız işlemesine izin verir. **En son ay** bölümünün en son verilerle sürekli güncelleştirilmesini istiyoruz çünkü yenileme aralığını 5 dakikaya ayarlayın. Geçmiş bölümünde verilerin yenilenmesi gerekli değildir. Ayrıca, en son ay bölümünü geçmiş bölüme birleştirmek ve yeni bir en son ay bölümü oluşturmak için bazı kodlar yazmanız gerekir.


## <a name="connect-power-bi-to-analysis-services"></a>Power BI Analysis Services bağlama

1. **Azure Analysis Services veritabanı bağlayıcısını kullanarak Azure Analiz sunucusu bağlanma** - **canlı modu** seçin ve aşağıdaki ekran görüntüsünde gösterildiği gibi Azure Analysis Services örneğine bağlanın:

   ![Azure Analysis Services veri al](./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png)

1. **Verileri yükleme ve rapor oluşturma** -daha önce yüklediğiniz verileri kullanarak sıcaklık ve mainfall hakkında rapor vermek için grafikler oluşturun. Canlı bağlantı oluşturduğunuz için sorgular, önceki adımda dağıttığınız Azure Analysis Services modelinde veriler üzerinde yürütülmelidir. Sıcaklık grafikleri, yeni veriler Azure Cosmos DB ' ye yüklendikten sonra beş dakika içinde güncelleştirilir.

   ![Verileri yükleme ve rapor oluşturma](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

## <a name="next-steps"></a>Sonraki adımlar

* Power BI hakkında daha fazla bilgi için bkz. [Power BI kullanmaya başlama](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

* [Verilerinizi Azure Cosmos DB ve görselleştirmek için Qlik Sense bağlama](visualize-qlik-sense.md)