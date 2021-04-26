---
title: 'Tarif: büyük veriler için bilişsel hizmetler ile tahmine dayalı bakım'
titleSuffix: Azure Cognitive Services
description: Bu hızlı başlangıçta, büyük veriler için bilişsel hizmetler ile dağıtılmış anomali algılama işlemlerinin nasıl gerçekleştirileceği gösterilmektedir
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: 5a583d74ae0bf421a7a863a65442d250489a2f8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775400"
---
# <a name="recipe-predictive-maintenance-with-the-cognitive-services-for-big-data"></a>Tarif: büyük veriler için bilişsel hizmetler ile tahmine dayalı bakım

Bu tarif, IoT cihazlarının tahmine dayalı bakımı için Apache Spark Azure SYNAPSE Analytics ve bilişsel hizmetler 'i nasıl kullanabileceğinizi gösterir. [Cosmosdb ve SYNAPSE link](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples) örneği ile birlikte takip edeceğiz. Bu tarifi basit tutmak için, CosmosDB ve SYNAPSE bağlantısı aracılığıyla akışı verileri almak yerine bir CSV dosyasından verileri doğrudan okuyacağız. SYNAPSE bağlantı örneğine bakmanızı önemle öneririz.

## <a name="hypothetical-scenario"></a>Kuramsal senaryo

Kuramsal senaryo, IoT cihazlarının [Buhar es](https://en.wikipedia.org/wiki/Steam_turbine)'yi Izlediği bir güç tesissudur. Iotsinyalleri koleksiyonunda her türbin için Revolutions/dakika (RPM) ve megavat (MW) verisi vardır. Stediğim türbinler 'nin sinyalleri çözümlenmekte ve anormal sinyaller algılandı.

Veride rastgele bir sıklıkta bir grup olabilir. Bu durumlarda, RPM değerleri devreye alınacaktır ve bu, devre koruması için MW çıkış olacaktır. Bu düşünce, verileri aynı zamanda, ancak farklı sinyallerle aynı anda görmenizde yarar vardır.

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/cognitive-services)
* [Sunucusuz Apache Spark havuzuyla](../../../synapse-analytics/quickstart-create-apache-spark-pool-portal.md) yapılandırılmış [Azure SYNAPSE çalışma alanı](../../../synapse-analytics/quickstart-create-workspace.md)

## <a name="setup"></a>Kurulum

### <a name="create-an-anomaly-detector-resource"></a>Anomali algılayıcısı kaynağı oluşturma

Azure bilişsel hizmetler, abone olduğunuz Azure kaynakları tarafından temsil edilir. [Azure Portal](../../cognitive-services-apis-create-account.md) veya [Azure CLI](../../cognitive-services-apis-create-account-cli.md)kullanarak çevirmen için bir kaynak oluşturun. Aşağıdakileri de yapabilirsiniz:

- [Azure Portal](https://portal.azure.com/)var olan bir kaynağı görüntüleyin.

Bu kaynak için uç noktayı ve anahtarı bir yere getirin, bu kılavuzda gerekecektir.

## <a name="enter-your-service-keys"></a>Hizmet anahtarlarınızı girin

Anahtarınızı ve konumunuzu ekleyerek başlayalım.

```python
service_key = None # Paste your anomaly detector key here
location = None # Paste your anomaly detector location here

assert (service_key is not None)
assert (location is not None)
```

## <a name="read-data-into-a-dataframe"></a>Verileri veri çerçevesine okuma

Sonra, ıotsinyalleri dosyasını bir veri çerçevesine okuyalim. SYNAPSE çalışma alanınızda yeni bir not defteri açın ve dosyadan bir DataFrame oluşturun.

```python
df_signals = spark.read.csv("wasbs://publicwasb@mmlspark.blob.core.windows.net/iot/IoTSignals.csv", header=True, inferSchema=True)
```

### <a name="run-anomaly-detection-using-cognitive-services-on-spark"></a>Spark üzerinde bilişsel hizmetler 'i kullanarak anomali algılama çalıştırma

Amaç, bir şeyin yanlış gittiğini ve tahmine dayalı bakım yaptığına bakmamız için IoT cihazlarından gelen sinyallerin anormal değerler çıktısı aldığı örnekleri bulmaktır. Bunu yapmak için Spark üzerinde anomali algılayıcısı kullanalım:

```python
from pyspark.sql.functions import col, struct
from mmlspark.cognitive import SimpleDetectAnomalies
from mmlspark.core.spark import FluentAPI

detector = (SimpleDetectAnomalies()
    .setSubscriptionKey(service_key)
    .setLocation(location)
    .setOutputCol("anomalies")
    .setGroupbyCol("grouping")
    .setSensitivity(95)
    .setGranularity("secondly"))

df_anomaly = (df_signals
    .where(col("unitSymbol") == 'RPM')
    .withColumn("timestamp", col("dateTime").cast("string"))
    .withColumn("value", col("measureValue").cast("double"))
    .withColumn("grouping", struct("deviceId"))
    .mlTransform(detector)).cache()

df_anomaly.createOrReplaceTempView('df_anomaly')
```

Şimdi verilere göz atalım:

```python
df_anomaly.select("timestamp","value","deviceId","anomalies.isAnomaly").show(3)
```

Bu hücre şuna benzer bir sonuç getirmelidir:

| timestamp           |   değer | deviceId   | isAnomaly   |
|:--------------------|--------:|:-----------|:------------|
| 2020-05-01 18:33:51 |    3174 | geliştirme-7      | Yanlış       |
| 2020-05-01 18:33:52 |    2976 | geliştirme-7      | Yanlış       |
| 2020-05-01 18:33:53 |    2714 | geliştirme-7      | Yanlış       |


 ## <a name="visualize-anomalies-for-one-of-the-devices"></a>Cihazlardan biri için bozukluklar görselleştirin

IoTSignals.csv birden çok IoT cihazlarından gelen sinyalleri vardır. Belirli bir cihaza odaklanarak cihazdaki anormal çıkışları görselleştirireceğiz.

```python
df_anomaly_single_device = spark.sql("""
select
  timestamp,
  measureValue,
  anomalies.expectedValue,
  anomalies.expectedValue + anomalies.upperMargin as expectedUpperValue,
  anomalies.expectedValue - anomalies.lowerMargin as expectedLowerValue,
  case when anomalies.isAnomaly=true then 1 else 0 end as isAnomaly
from
  df_anomaly
where deviceid = 'dev-1' and timestamp < '2020-04-29'
order by timestamp
limit 200""")
```

Artık belirli bir cihazın anormalilerini temsil eden bir veri çerçevesi oluşturduğumuz için, bu bozuklukları görselleştirebiliriz:

```python
import matplotlib.pyplot as plt
from pyspark.sql.functions import col

adf = df_anomaly_single_device.toPandas()
adf_subset = df_anomaly_single_device.where(col("isAnomaly") == 1).toPandas()

plt.figure(figsize=(23,8))
plt.plot(adf['timestamp'],adf['expectedUpperValue'], color='darkred', linestyle='solid', linewidth=0.25, label='UpperMargin')
plt.plot(adf['timestamp'],adf['expectedValue'], color='darkgreen', linestyle='solid', linewidth=2, label='Expected Value')
plt.plot(adf['timestamp'],adf['measureValue'], 'b', color='royalblue', linestyle='dotted', linewidth=2, label='Actual')
plt.plot(adf['timestamp'],adf['expectedLowerValue'],  color='black', linestyle='solid', linewidth=0.25, label='Lower Margin')
plt.plot(adf_subset['timestamp'],adf_subset['measureValue'], 'ro', label = 'Anomaly')
plt.legend()
plt.title('RPM Anomalies with Confidence Intervals')
plt.show()
```

Başarılı olursa, çıktınızı şöyle görünür:

![Anomali algılayıcı çizimi](../media/anomaly-output.png)

## <a name="next-steps"></a>Sonraki adımlar

Azure bilişsel hizmetler, Azure SYNAPSE Analytics ve Azure CosmosDB ile tahmine dayalı bakım yapmayı öğrenin. Daha fazla bilgi için [GitHub](https://github.com/Azure-Samples/cosmosdb-synapse-link-samples)'daki tam örneğe bakın.
