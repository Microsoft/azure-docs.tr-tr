---
title: Apache Spark ile verileri görselleştirme
description: Apache Spark ve Azure SYNAPSE Analytics not defterlerini kullanarak zengin veri görselleştirmeleri oluşturma
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 10/20/2020
ms.author: midesa
ms.openlocfilehash: 56af49b6fa862c93822293056752182b534ac442
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942263"
---
# <a name="analyze-data-with-apache-spark"></a>Apache Spark ile verileri analiz etme

Bu öğreticide, Azure açık veri kümelerini ve Apache Spark kullanarak keşif veri analizini nasıl gerçekleştireceğinizi öğreneceksiniz. Daha sonra sonuçları Azure SYNAPSE Analytics 'teki bir Synapse Studio not defterinde görselleştirebilirsiniz.

Özellikle, [New York City (NYC) TAXI](https://azure.microsoft.com/en-us/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) veri kümesini analiz edeceğiz. Veriler Azure açık veri kümeleri aracılığıyla kullanılabilir. Veri kümesinin bu alt kümesi, sarı TAXI seyahatleriyle ilgili bilgiler içerir: her seyahat hakkında bilgi, başlangıç ve bitiş zamanı ve konumlar, maliyet ve diğer ilgi çekici öznitelikler.
  
## <a name="before-you-begin"></a>Başlamadan önce
[Apache Spark Havuzu Oluşturma öğreticisini](../articles/../quickstart-create-apache-spark-pool-studio.md)Izleyerek Apache Spark havuzu oluşturun. 

## <a name="download-and-prepare-the-data"></a>Verileri indirin ve hazırlayın
1. PySpark çekirdeğini kullanarak bir not defteri oluşturun. Yönergeler için bkz. [Not defteri oluşturma](../quickstart-apache-spark-notebook.md#create-a-notebook). 
   
   > [!Note]
   > PySpark çekirdeği nedeniyle, açıkça herhangi bir bağlam oluşturmanız gerekmez. İlk kod hücresini çalıştırdığınızda Spark bağlamı sizin için otomatik olarak oluşturulur.

2. Bu öğreticide, veri kümesini görselleştirmemize yardımcı olmak için birkaç farklı kitaplık kullanacağız. Bu analizi yapmak için aşağıdaki kitaplıkları içeri aktarın: 

   ```python
   import matplotlib.pyplot as plt
   import seaborn as sns
   import pandas as pd
   ```

3. Ham veriler bir Parquet biçiminde olduğundan, dosyayı doğrudan veri çerçevesi olarak belleğe çekmek için Spark bağlamını kullanabilirsiniz. Açık veri kümeleri API 'SI aracılığıyla verileri alarak Spark DataFrame oluşturun. Burada, veri türlerini ve şemayı çıkarması için okuma özelliklerinde Spark DataFrame *şemasını* kullanıyoruz.

   ```python
   from azureml.opendatasets import NycTlcYellow
   from datetime import datetime
   from dateutil import parser

   end_date = parser.parse('2018-06-06')
   start_date = parser.parse('2018-05-01')
   nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
   df = nyc_tlc.to_spark_dataframe()
   ```

4. Veriler okunduktan sonra, veri kümesini temizlemek için bazı ilk filtreleme yapmak istiyoruz. Gereksiz sütunları kaldırabilir ve önemli bilgileri çıkaran sütunlar ekleyebilirsiniz. Ayrıca, veri kümesi içindeki anormallikleri filtreleyeceğiz.

   ```python
   # Filter the dataset 
   from pyspark.sql.functions import *

   filtered_df = df.select('vendorID', 'passengerCount', 'tripDistance','paymentType', 'fareAmount', 'tipAmount'\
                                   , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                   , dayofweek('tpepPickupDateTime').alias('day_of_week')\
                                   , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month'))\
                               .filter((df.passengerCount > 0)\
                                   & (df.tipAmount >= 0)\
                                   & (df.fareAmount >= 1) & (df.fareAmount <= 250)\
                                   & (df.tripDistance > 0) & (df.tripDistance <= 200))

   filtered_df.createOrReplaceTempView("taxi_dataset")
   ```

## <a name="analyze-data"></a>Verileri çözümleme
Veri analisti olarak, verilerden Öngörüler ayıklamaya yardımcı olacak çok çeşitli araçlara sahip olursunuz. Öğreticinin bu bölümünde, Azure SYNAPSE Analytics Not defterleri içinde sunulan birkaç faydalı araç hakkında yol göstereceğiz. Bu çözümlemede, seçili dönemimiz için daha yüksek bir TAXI ipucu sunan faktörleri anlamak istiyoruz.

### <a name="apache-spark-sql-magic"></a>Apache Spark SQL Magic 
İlk olarak, Azure SYNAPSE not defteriyle SQL ve MAGIC komutlarıyla Apache Spark araştırmacı veri analizi yapacağız. Sorgumuzu aldıktan sonra, yerleşik özelliği kullanarak sonuçları görselleştireceğiz ```chart options``` . 

1. Not defteriniz içinde yeni bir hücre oluşturun ve aşağıdaki kodu kopyalayın. Bu sorguyu kullanarak, ortalama ipucu tutarlarının seçmiş olduğumuz dönem içinde nasıl değiştiğini anlamak istiyoruz. Bu sorgu, günlük minimum/maksimum tıp miktarı ve ortalama tarifeli havayolu miktarı dahil diğer yararlı içgörüleri tanımlamamıza da yardımcı olur.
   
   ```sql
   %%sql
   SELECT 
       day_of_month
       , MIN(tipAmount) AS minTipAmount
       , MAX(tipAmount) AS maxTipAmount
       , AVG(tipAmount) AS avgTipAmount
       , AVG(fareAmount) as fareAmount
   FROM taxi_dataset 
   GROUP BY day_of_month
   ORDER BY day_of_month ASC
   ```

2. Sorgu çalışmayı bitirdikten sonra grafik görünümüne geçerek sonuçları görselleştirebiliriz. Bu örnek, ```day_of_month``` alanı anahtar olarak ve değer olarak belirterek bir çizgi grafik oluşturur ```avgTipAmount``` . Seçimlerinizi yaptıktan sonra, grafiğinizi yenilemek için **Uygula** ' yı seçin. 
   
## <a name="visualize-data"></a>Verileri görselleştirme
Yerleşik Not defteri grafik seçeneklerine ek olarak kendi görselleştirmelerinizi oluşturmak için popüler açık kaynaklı kitaplıkları da kullanabilirsiniz. Aşağıdaki örneklerde Seaborn ve Matplotlib kullanacağız. Bunlar, veri görselleştirme için yaygın olarak kullanılan Python kitaplıklarıdır. 

> [!Note]
> Varsayılan olarak, Azure SYNAPSE Analytics 'teki tüm Apache Spark havuzları, yaygın olarak kullanılan ve varsayılan kitaplıkların bir kümesini içerir. Kitaplıkların tam listesini [Azure SYNAPSE Runtime](../spark/apache-spark-version-support.md) belgelerindeki görüntüleyebilirsiniz. Ayrıca, üçüncü taraf veya yerel olarak oluşturulan bir kodu uygulamalarınız için kullanılabilir hale getirmek için, Spark havuzlarınızdan birine [bir kitaplık yükleyebilirsiniz](../spark/apache-spark-azure-portal-add-libraries.md) .

1. Geliştirmeyi daha kolay ve daha ucuz hale getirmek için veri kümesini örnekliyoruz. Yerleşik Apache Spark örnekleme özelliğini kullanacağız. Ayrıca, hem Seaborn hem de Matplotlib bir Pandas DataFrame veya bir sayısal tuş takımı dizisi gerektirir. Bir Pandas DataFrame almak için, ```toPandas()``` veri çerçevesini dönüştürmek için komutunu kullanın.

   ```python
   # To make development easier, faster, and less expensive, downsample for now
   sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

   # The charting package needs a Pandas DataFrame or NumPy array to do the conversion
   sampled_taxi_pd_df = sampled_taxi_df.toPandas()
   ```

1. Veri kümizdeki ipuçlarının dağılımını anlamak istiyoruz. Tıp tutarının ve sayısının dağılımını gösteren bir histogram oluşturmak için Matplotlib kullanacağız. Dağıtıma bağlı olarak, ipuçlarının $10 ' den küçük veya buna eşit miktarlarda çarpılır olduğunu görebiliriz.

   ```python
   # Look at a histogram of tips by count by using Matplotlib

   ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
   ax1.set_title('Tip amount distribution')
   ax1.set_xlabel('Tip Amount ($)')
   ax1.set_ylabel('Counts')
   plt.suptitle('')
   plt.show()
   ```

   ![İpuçları histogramı.](./media/apache-spark-machine-learning-mllib-notebook/histogram.png)

1. Ardından, belirli bir yolculuğa yönelik ipuçları ve haftanın günü arasındaki ilişkiyi anlamak istiyoruz. Haftanın her günü için eğilimleri özetleyen bir kutu çizimi oluşturmak için Seaborn kullanın. 

   ```python
   # View the distribution of tips by day of week using Seaborn
   ax = sns.boxplot(x="day_of_week", y="tipAmount",data=sampled_taxi_pd_df, showfliers = False)
   ax.set_title('Tip amount distribution per day')
   ax.set_xlabel('Day of Week')
   ax.set_ylabel('Tip Amount ($)')
   plt.show()

   ```
   ![Gün başına ipuçlarının dağılımını gösteren grafik.](./media/apache-spark-data-viz/data-analyst-tutorial-per-day.png)

4. Çok iyi bir varsayım, pastıcılar sayısı ile toplam TAXI tıp miktarı arasında bir pozitif ilişki olması olabilir. Bu ilişkiyi doğrulamak için aşağıdaki kodu çalıştırarak her bir pastger sayısı için ipuçlarının dağılımını gösteren bir kutu çizimi oluşturun. 
   
   ```python
   # How many passengers tipped by various amounts 
   ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
   ax2.set_title('Tip amount by Passenger count')
   ax2.set_xlabel('Passenger count')
   ax2.set_ylabel('Tip Amount ($)')
   ax2.set_ylim(0,30)
   plt.suptitle('')
   plt.show()
   ```
   ![Bir kutu çizgi çizimi gösteren grafik.](./media/apache-spark-machine-learning-mllib-notebook/box-whisker-plot.png)

1. Son olarak, tarifeli havayolu miktarı ile tıp miktarı arasındaki ilişkiyi anlamak istiyoruz. Sonuçlara bağlı olarak, insanların ipucu olmadığı çeşitli gözlemler olduğunu görebiliriz. Bununla birlikte, genel tarifeli havayolu ve tıp tutarları arasında da pozitif bir ilişki görürsünüz.
   
   ```python
   # Look at the relationship between fare and tip amounts

   ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
   ax.set_title('Tip amount by Fare amount')
   ax.set_xlabel('Fare Amount ($)')
   ax.set_ylabel('Tip Amount ($)')
   plt.axis([-2, 80, -2, 20])
   plt.suptitle('')
   plt.show()
   ```
   ![Tıp tutarının dağılım çizimi.](./media/apache-spark-machine-learning-mllib-notebook/scatter.png)

## <a name="shut-down-the-spark-instance"></a>Spark örneğini kapatma

Uygulamayı çalıştırmayı bitirdikten sonra, kaynakları serbest bırakmak için Not defterini kapatın. Sekmeyi kapatın veya Not defterinin alt kısmındaki durum panelinden **oturumu sonlandır** ' ı seçin.

## <a name="see-also"></a>Ayrıca bkz.

- [Genel Bakış: Azure SYNAPSE Analytics 'te Apache Spark](apache-spark-overview.md)
- [Apache mini ml ile makine öğrenimi modeli oluşturma](../spark/apache-spark-machine-learning-mllib-notebook.md)

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Synapse Analytics](../index.yml)
- [Resmi belgeleri Apache Spark](https://spark.apache.org/docs/latest/)