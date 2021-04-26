---
title: Azure Synapse Studio not defterleri
description: Bu öğretici, Apache Spark ve sunucusuz SQL havuzu kullanarak Power BI panosu oluşturma hakkında genel bakış sağlar.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 11/16/2020
ms.openlocfilehash: 53fb256d3a0fba1cc10cafb239ff7f7bb21ac4eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98121082"
---
# <a name="tutorial-create-a-power-bi-report-using-apache-spark-and-azure-synapse-analytics"></a>Öğretici: Apache Spark ve Azure SYNAPSE Analytics kullanarak Power BI raporu oluşturma

Kuruluşlar, önemli iş hissedarlarına hizmet vermeden önce büyük hacimlerinizi genellikle işlemesi gerekir. Bu öğreticide, Apache Spark kullanarak verileri işlemek ve daha sonra verileri Power BI ve sunucusuz SQL aracılığıyla son kullanıcılara sunmak için Azure SYNAPSE Analytics 'teki tümleşik deneyimlerden nasıl yararlanacağınızı öğreneceksiniz.

## <a name="before-you-begin"></a>Başlamadan önce
- Varsayılan depolama alanı olarak yapılandırılmış bir ADLS 2. depolama hesabıyla [Azure SYNAPSE Analytics çalışma alanı](../quickstart-create-workspace.md) . 
- Power BI çalışma alanı ve Power BI Desktop verileri görselleştirilecek. Ayrıntılar için bkz. [Power BI çalışma alanı oluşturma](/power-bi/service-create-the-new-workspaces) ve [Power BI masaüstünü yüklemeye](https://powerbi.microsoft.com/downloads/)
- Azure SYNAPSE analizlerinizi ve Power BI çalışma alanlarını bağlamak için bağlı hizmet. Ayrıntılar için bkz. [Power BI çalışma alanına bağlama](../quickstart-power-bi.md)
- SYNAPSE Analytics çalışma alanınızda sunucusuz Apache Spark Havuzu. Ayrıntılar için bkz. [sunucusuz Apache Spark havuzu oluşturma](../quickstart-create-apache-spark-pool-studio.md)
  
## <a name="download-and-prepare-the-data"></a>Verileri indirin ve hazırlayın
Bu örnekte, New York 'dan TAXI Seyahat ipucu verilerinde bazı analizler gerçekleştirmek için Apache Spark kullanacaksınız. Veriler [Azure açık veri kümeleri](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)aracılığıyla kullanılabilir. Veri kümesinin bu alt kümesi, her seyahat hakkında bilgiler, başlangıç ve bitiş saati ve konumları, maliyet ve diğer ilgi çekici öznitelikler dahil olmak üzere sarı TAXI seyahatleriyle ilgili bilgiler içerir.

1. Kodu yeni bir hücreye yapıştırarak Spark dataframe oluşturmak için aşağıdaki satırları çalıştırın. Bu, verileri açık veri kümeleri API 'SI aracılığıyla alır. Bu verilerin tümünün çekilerek 1.500.000.000 satır hakkında bilgi oluşturulur. Aşağıdaki kod örneği, verilerin tek bir ayı döndüren bir filtre uygulamak için start_date ve end_date kullanır.
   
   ```python
    from azureml.opendatasets import NycTlcYellow
    from dateutil import parser
    from datetime import datetime

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
   ```
2. SQL Apache Spark kullanarak, Nyctlcöğreticisi adlı bir veritabanı oluşturacağız. Veri işlemizin sonuçlarını depolamak için bu veritabanını kullanacağız.
   ```python
   %%pyspark
    spark.sql("CREATE DATABASE IF NOT EXISTS NycTlcTutorial")
   ```
3. Daha sonra, verileri işlemek için Spark dataframe işlemlerini kullanacağız. Aşağıdaki kodda aşağıdaki dönüşümleri gerçekleştiririz:
   1. Gerekli olmayan sütunların kaldırılması.
   2. Filtreleyiciler/hatalı değerler filtrelemeye göre kaldırılıyor.
   3. ```tripTimeSecs```Daha fazla analiz için ve gibi yeni özelliklerin oluşturulması ```tipped``` .
    ```python
    from pyspark.sql.functions import unix_timestamp, date_format, col, when

    taxi_df = filtered_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                    , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                    , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                    , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                    , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                    , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                    )\
                            .filter((filtered_df.passengerCount > 0) & (filtered_df.passengerCount < 8)\
                                    & (filtered_df.tipAmount >= 0) & (filtered_df.tipAmount <= 25)\
                                    & (filtered_df.fareAmount >= 1) & (filtered_df.fareAmount <= 250)\
                                    & (filtered_df.tipAmount < filtered_df.fareAmount)\
                                    & (filtered_df.tripDistance > 0) & (filtered_df.tripDistance <= 100)\
                                    & (filtered_df.rateCodeId <= 5)
                                    & (filtered_df.paymentType.isin({"1", "2"})))
    ```
4. Son olarak, Apache Spark yöntemi kullanarak veri çerçevemizi kaydedecağız ```saveAsTable``` . Bu, daha sonra sunucusuz SQL havuzları kullanarak aynı tabloyu sorgulamanızı ve buna bağlanmanızı sağlar.
  ```python
     taxi_df.write.mode("overwrite").saveAsTable("NycTlcTutorial.nyctaxi")
  ```
   
## <a name="query-data-using-serverless-sql-pools"></a>Sunucusuz SQL havuzlarını kullanarak verileri sorgulama
Azure SYNAPSE Analytics, farklı çalışma alanı hesaplama altyapılarının, sunucusuz Apache Spark havuzları ve sunucusuz SQL havuzu arasında veritabanlarını ve tabloları paylaşmasına izin verir. Bu, SYNAPSE [paylaşılan meta veri yönetimi](../metadata/overview.md) özelliği aracılığıyla desteklenir. Sonuç olarak, Spark oluşturulan veritabanları ve kendi Parquet tarafından desteklenen tabloları, çalışma alanı sunucusuz SQL havuzunda görünür hale gelir.

Sunucusuz SQL havuzunuzu kullanarak Apache Spark tablonuzu sorgulamak için:
   1. Apache Spark tablonuzu kaydettikten sonra **veri** sekmesine geçin.
   
   2. **Çalışma alanları** altında, az önce oluşturduğunuz Apache Spark tablosunu bulun ve **Yeni SQL betiği** ' nı seçin ve ardından **Ilk 100 satır**' ı seçin. 
      
      :::image type="content" source="../spark/media/apache-spark-power-bi/query-spark-table-with-sql.png" alt-text="SQL sorgusu." border="true":::

   3. Sorgunuzu iyileştirmeye devam edebilir, hatta SQL grafik seçeneklerini kullanarak sonuçlarınızı görselleştirebilirsiniz.

## <a name="connect-to-power-bi"></a>Power BI'a bağlanma
Daha sonra, sunucusuz SQL havuzumuzu Power BI çalışma alanına bağlayacağız. Çalışma alanınızı bağladıktan sonra, hem doğrudan Azure SYNAPSE Analytics 'ten hem de Power BI masaüstünden Power BI raporlar oluşturabilirsiniz.

>[!Note]
> Başlamadan önce, [Power BI çalışma alanınıza](../quickstart-power-bi.md) bağlı bir hizmet ayarlamanız ve [Power BI masaüstünü](/power-bi/service-create-the-new-workspaces)indirmeniz gerekir.  

Sunucusuz SQL havuzumuzu Power BI çalışma alanım 'a bağlamak için:

1.  **Power BI veri kümeleri** sekmesine gidin ve **+ Yeni veri kümesi** seçeneğini belirleyin. İstemde, veri kaynağı olarak kullanmak istediğiniz SQL Analytics veritabanından. pbıd dosyasını indirin. 
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-datasets.png" alt-text="Veri kümelerini Power BI." border="true":::

2.  Bir veri kümesi oluşturmak için dosyayı Power BI Desktop açın. Dosyayı açtıktan sonra, **Microsoft hesabı** ve **içeri aktarma** seçeneğini kullanarak SQL Server veritabanına bağlanın. 
   

## <a name="create-a-power-bi-report"></a>Power BI raporu oluşturma
1. Power BI masaüstünden artık raporunuza **önemli bir etkileyen** grafik ekleyebilirsiniz.
   
   1. Ekseni **çözümlemek** Için ortalama *tipamount* sütununu sürükleyin.
   
   2. **Weekdaystring**, ortalama **Üçlü uzaklık** ve ortalama **Üçlü saat secs** sütunlarını **Açıklama** eksenine sürükleyin. 
   
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-desktop.png" alt-text="Power BI Desktop." border="true":::

2. Power BI Masaüstü Giriş sekmesinde **Yayımla** ve değişiklikleri **Kaydet** ' i seçin. Bir dosya adı girin ve bu raporu *NycTaxiTutorial çalışma alanına* kaydedin.
   
3. Ayrıca, Azure SYNAPSE Analytics çalışma alanınızın içinden de Power BI görselleştirmeler oluşturabilirsiniz. Bunu yapmak için, Azure SYNAPSE çalışma alanınızdaki **geliştirme** sekmesine gidin ve Power BI sekmesini açın. Buradan raporunuzu seçebilir ve ek görselleştirmeler oluşturmaya devam edebilirsiniz. 
   
   :::image type="content" source="../spark/media/apache-spark-power-bi/power-bi-synapse.png" alt-text="Azure SYNAPSE Analytics çalışma alanı." border="true":::

Sunucusuz SQL aracılığıyla bir veri kümesi oluşturma ve Power BI bağlanma hakkında daha fazla bilgi için bu öğreticiyi [Power BI masaüstüne bağlanma](../../synapse-analytics/sql/tutorial-connect-power-bi-desktop.md) konusunda bulabilirsiniz

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki belge ve öğreticileri ziyaret ederek Azure SYNAPSE Analytics 'teki veri görselleştirme özellikleri hakkında daha fazla bilgi edinebilirsiniz:
   - [Sunucusuz Apache Spark havuzlarıyla verileri görselleştirme](../spark/apache-spark-data-visualization-tutorial.md)
   - [Apache Spark havuzlarıyla veri görselleştirmesine genel bakış](../spark/apache-spark-data-visualization.md)