---
title: 'Öğretici: Apache Spark için Microsoft Machine Learning kullanarak makine öğrenimi uygulamaları oluşturma (Önizleme)'
description: Azure SYNAPSE Analytics 'te makine öğrenimi uygulamaları oluşturmak için Microsoft Machine Learning Apache Spark nasıl kullanacağınızı öğrenin.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: ''
ms.date: 03/08/2021
author: ruixinxu
ms.author: ruxu
ms.openlocfilehash: 5258d8f16e288e7df7e1286eb1902cc6ba6d10f7
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227730"
---
# <a name="tutorial-build-machine-learning-applications-using-microsoft-machine-learning-for-apache-spark-preview"></a>Öğretici: Apache Spark için Microsoft Machine Learning kullanarak makine öğrenimi uygulamaları oluşturma (Önizleme)

Bu makalede, makine öğrenimi uygulamaları oluşturmak için Apache Spark ([Mmlspark](https://github.com/Azure/mmlspark)) için Microsoft Machine Learning kullanmayı öğreneceksiniz. MMLSpark, Azure bilişsel [Hizmetler](../../cognitive-services/big-data/cognitive-services-for-big-data.md), [OpenCV](https://opencv.org/), [lightgbm](https://github.com/Microsoft/LightGBM) ve daha fazlası gibi birçok derin öğrenme ve veri bilimi aracı ekleyerek Apache Spark dağıtılmış makine öğrenimi çözümünü genişletir.  MMLSpark, çeşitli Spark veri kaynaklarından güçlü ve yüksek düzeyde ölçeklenebilir tahmine dayalı ve analitik modeller oluşturmanızı sağlar.
SYNAPSE Spark aşağıdakiler dahil yerleşik MMLSpark kitaplıklarını sağlar:

- [Vowpal Wabbit](https://github.com/Azure/mmlspark/blob/master/docs/vw.md) – makine öğrenimi 'nde yaklaşım analizi gibi metin analizlerini etkinleştirmek üzere Machine Learning için Kitaplık Hizmetleri.
- [Spark üzerinde](https://github.com/Azure/mmlspark/blob/master/docs/cogsvc.md) bilişsel hizmetler – anomali algılama gibi bilişsel veri modelleme Hizmetleri için çözüm tasarımı elde etmek amacıyla, Azure bilişsel hizmetler 'In, Mini erişimli bir ml işlem hattı içinde özelliklerini birleştirmek için
- [Lightgbm](https://github.com/Azure/mmlspark/blob/master/docs/lightgbm.md) – lightgbm, ağaç tabanlı öğrenme algoritmaları kullanan bir gradyan artırma çerçevesidir. Dağıtılması ve daha yüksek verimlilik sağlamak üzere tasarlanmıştır.
- Koşullu KNN-koşullu sorgularla ölçeklenebilir KNN modelleri.
- [Spark üzerinde http](https://github.com/Azure/mmlspark/blob/master/docs/http.md) – Spark ve http protokol tabanlı erişilebilirliği tümleştirerek dağıtılmış mikro hizmetleri düzenlemeye izin vermez.

Bu öğreticide, MMLSpark içindeki Azure bilişsel hizmetler 'i kullanma örnekleri ele alınmaktadır 

- Metin Analizi-bir tümce kümesinin yaklaşımını (veya ruh) alın.
- Görüntü İşleme-bir görüntü kümesiyle ilişkili etiketleri (tek sözcüklük açıklamaları) alın.
- Bing Resim Arama-Web 'de doğal dil sorgusuyla ilgili görüntüleri arayın.
- Anomali algılayıcısı-bir zaman serisi verileri içindeki aykırlıkları algılayın.

Azure aboneliğiniz yoksa [başlamadan önce ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Önkoşullar 

- Varsayılan depolama alanı olarak yapılandırılmış bir Azure Data Lake Storage 2. depolama hesabıyla [Azure SYNAPSE Analytics çalışma alanı](../get-started-create-workspace.md) . Birlikte çalıştığınız Data Lake Storage 2. dosya sisteminin *Depolama Blobu veri katılımcısı* olması gerekir.
- Azure SYNAPSE Analytics çalışma alanınızdaki Spark Havuzu. Ayrıntılar için bkz. [Azure 'Da Spark havuzu oluşturma SYNAPSE](../quickstart-create-sql-pool-studio.md).
- Öğreticide açıklanan ön yapılandırma adımları [Azure SYNAPSE 'de bilişsel hizmetler 'ı yapılandırın](./tutorial-configure-cognitive-services-synapse.md).


## <a name="get-started"></a>başlarken
Başlamak için, mmlspark ve yapılandırılaate hizmet anahtarlarını içeri aktarın. 

```python
import mmlspark
mmlspark.__spark_package_version__ # current version: 1.0.0-rc3-6-a862d6b1-SNAPSHOT

from mmlspark.cognitive import *
from notebookutils import mssparkutils

# A general Cognitive Services key for Text Analytics and Computer Vision (or use separate keys that belong to each service)
service_key =  "ADD_YOUR_SUBSCRIPION_KEY" 
# A Bing Search v7 subscription key
bing_search_key = "ADD_YOUR_SUBSCRIPION_KEY" 
# An Anomaly Dectector subscription key
anomaly_key =  "ADD_YOUR_SUBSCRIPION_KEY" 
# Your linked key vault for Synapse workspace
key_vault = "YOUR_KEY_VAULT_NAME"


cognitive_service_key = mssparkutils.credentials.getSecret(key_vault, service_key)
bingsearch_service_key = mssparkutils.credentials.getSecret(key_vault, bing_search_key)
anomalydetector_key = mssparkutils.credentials.getSecret(key_vault, anomaly_key)

```


## <a name="text-analytics-sample"></a>Metin analizi örneği

[Metin analizi](../../cognitive-services/text-analytics/index.yml) hizmeti, metinden akıllı öngörüleri ayıklamak için çeşitli algoritmalar sağlar. Örneğin, verilen giriş metninin yaklaşımını bulabiliriz. Hizmet, 0,0 ile 1,0 arasında bir puan döndürür. bu noktada, düşük puanlar negatif yaklaşım ve yüksek puan pozitif yaklaşım olduğunu gösterir. Bu örnek üç basit cümle kullanır ve her biri için yaklaşımı döndürür.

```python
from pyspark.sql.functions import col

# Create a dataframe that's tied to it's column names
df_sentences = spark.createDataFrame([
  ("I am so happy today, its sunny!", "en-US"), 
  ("this is a dog", "en-US"), 
  ("I am frustrated by this rush hour traffic!", "en-US") 
], ["text", "language"])

# Run the Text Analytics service with options
sentiment = (TextSentiment()
    .setTextCol("text")
    .setLocation("eastasia")
    .setSubscriptionKey(cognitive_service_key)
    .setOutputCol("sentiment")
    .setErrorCol("error")
    .setLanguageCol("language"))

# Show the results of your text query in a table format

display(sentiment.transform(df_sentences).select("text", col("sentiment")[0].getItem("sentiment").alias("sentiment")))
```

### <a name="expected-results"></a>Beklenen sonuçlar

|metin | yaklaşım|
|--|--|
| Bu aceleniz saat trafiği tarafından hayal dışındayım! | negatif |
| Bu bir köpek | nötr |
| Bugün bugün kutlu olsun! | pozitif |

## <a name="computer-vision-sample"></a>Bilgisayar vizyonu örneği
[Görüntü işleme](../../cognitive-services/computer-vision/index.yml) , yüzeyleri, nesneleri ve doğal dil açıklamaları gibi yapıyı belirlemek için görüntüleri analiz eder. Bu örnekte, izleme görüntüsünü etiketliyoruz. Etiketler, görüntüde tanınabilir nesneler, kişiler, manzara ve eylemler gibi öğelerin bir sözcüklerdir.


![image](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg)

```python
# Create a dataframe with the image URL
df_images = spark.createDataFrame([
        ("https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg", )
    ], ["image", ])

# Run the Computer Vision service. Analyze Image extracts information from/about the images.
analysis = (AnalyzeImage()
    .setLocation("eastasia")
    .setSubscriptionKey(cognitive_service_key)
    .setVisualFeatures(["Categories","Color","Description","Faces","Objects","Tags"])
    .setOutputCol("analysis_results")
    .setImageUrlCol("image")
    .setErrorCol("error"))

# Show the results of what you wanted to pull out of the images.
display(analysis.transform(df_images).select("image", "analysis_results.description.tags"))
```
### <a name="expected-results"></a>Beklenen sonuçlar

|image | etiketler|
|--|--|
| `https://raw.githubusercontent.com/Azure-Samples/cognitive-services-sample-data-files/master/ComputerVision/Images/objects.jpg` | [skalama, kişi, Man, dış mek, perding, spor, skateboard, Başak, pano, gömlek, hava, Park, Erkek, yüz, atlama, rampa, el, yapma, bekletme] |

## <a name="bing-image-search-sample"></a>Bing resim arama örneği
[Bing resim arama](../../cognitive-services/bing-image-search/overview.md) , kullanıcının doğal dil sorgusuyla ilgili görüntüleri almak için Web 'de arama yapar. Bu örnekte, tırnak içine alınmış resimleri gösteren bir metin sorgusu kullanıyoruz. Sorgumuzla ilgili fotoğraflar içeren resim URL 'Lerinin bir listesini döndürür.


```python
from pyspark.ml import PipelineModel

# Number of images Bing will return per query
imgsPerBatch = 2
# A list of offsets, used to page into the search results
offsets = [(i*imgsPerBatch,) for i in range(10)]
# Since web content is our data, we create a dataframe with options on that data: offsets
bingParameters = spark.createDataFrame(offsets, ["offset"])

# Run the Bing Image Search service with our text query
bingSearch = (BingImageSearch()
    .setSubscriptionKey(bingsearch_service_key)
    .setOffsetCol("offset")
    .setQuery("Martin Luther King Jr. quotes")
    .setCount(imgsPerBatch)
    .setOutputCol("images"))

# Transformer that extracts and flattens the richly structured output of Bing Image Search into a simple URL column
getUrls = BingImageSearch.getUrlTransformer("images", "url")
pipeline_bingsearch = PipelineModel(stages=[bingSearch, getUrls])

# Show the results of your search: image URLs
res_bingsearch = pipeline_bingsearch.transform(bingParameters)
display(res_bingsearch.dropDuplicates())
```

### <a name="expected-results"></a>Beklenen sonuçlar

|image | 
|--|
|`http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes-16.jpg` |
|`http://www.scrolldroll.com/wp-content/uploads/2017/06/6-25.png` |
| `http://abettertodaymedia.com/wp-content/uploads/2017/01/86783bd7a92960aedd058c91a1d10253.jpg`|
| `https://weneedfun.com/wp-content/uploads/2016/05/martin-luther-king-jr-quotes-11.jpg` |
| `http://www.sofreshandsogreen.com/wp-content/uploads/2012/01/martin-luther-king-jr-quote-sofreshandsogreendotcom.jpg` |
| `https://cdn.quotesgram.com/img/72/57/1104209728-martin_luther_king_jr_quotes_16.jpg` |
| `http://comicbookandbeyond.com/wp-content/uploads/2019/05/Martin-Luther-King-Jr.-Quotes.jpg` |
| `https://exposingthepain.files.wordpress.com/2015/01/martin-luther-king-jr-quotes-08.png` |
| `https://topmemes.me/wp-content/uploads/2020/01/Top-10-Martin-Luther-King-jr.-Quotes2-1024x538.jpg` |
| `http://img.picturequotes.com/2/581/580286/dr-martin-luther-king-jr-quote-1-picture-quote-1.jpg` |
| `http://parryz.com/wp-content/uploads/2017/06/Amazing-Martin-Luther-King-Jr-Quotes.jpg` |
| `http://everydaypowerblog.com/wp-content/uploads/2014/01/Martin-Luther-King-Jr.-Quotes1.jpg` |
| `https://lessonslearnedinlife.net/wp-content/uploads/2020/05/Martin-Luther-King-Jr.-Quotes-2020.jpg` |
| `https://quotesblog.net/wp-content/uploads/2015/10/Martin-Luther-King-Jr-Quotes-Wallpaper.jpg` |

## <a name="anomaly-detector-sample"></a>Anomali algılayıcı örneği

[Anomali algılayıcısı](../../cognitive-services/anomaly-detector/index.yml) , zaman serisi verilerinizde ırdüzenleyeaykırıluluğunu saptamak için idealdir. Bu örnekte, tüm zaman serisinde bozukluklar bulmak için hizmeti kullanırız.

```python
from pyspark.sql.functions import lit

# Create a dataframe with the point data that Anomaly Detector requires
df_timeseriesdata = spark.createDataFrame([
    ("1972-01-01T00:00:00Z", 826.0),
    ("1972-02-01T00:00:00Z", 799.0),
    ("1972-03-01T00:00:00Z", 890.0),
    ("1972-04-01T00:00:00Z", 900.0),
    ("1972-05-01T00:00:00Z", 766.0),
    ("1972-06-01T00:00:00Z", 805.0),
    ("1972-07-01T00:00:00Z", 821.0),
    ("1972-08-01T00:00:00Z", 20000.0), # anomaly
    ("1972-09-01T00:00:00Z", 883.0),
    ("1972-10-01T00:00:00Z", 898.0),
    ("1972-11-01T00:00:00Z", 957.0),
    ("1972-12-01T00:00:00Z", 924.0),
    ("1973-01-01T00:00:00Z", 881.0),
    ("1973-02-01T00:00:00Z", 837.0),
    ("1973-03-01T00:00:00Z", 9000.0) # anomaly
], ["timestamp", "value"]).withColumn("group", lit("series1"))

# Run the Anomaly Detector service to look for irregular data
anamoly_detector = (SimpleDetectAnomalies()
  .setSubscriptionKey(anomalydetector_key)
  .setLocation("eastasia")
  .setTimestampCol("timestamp")
  .setValueCol("value")
  .setOutputCol("anomalies")
  .setGroupbyCol("group")
  .setGranularity("monthly"))

# Show the full results of the analysis with the anomalies marked as "True"
display(anamoly_detector.transform(df_timeseriesdata).select("timestamp", "value", "anomalies.isAnomaly"))

```

### <a name="expected-results"></a>Beklenen sonuçlar

|timestamp | değer | isAnomaly |
|--|--|--|
| 1972-01-01T00:00:00Z|826,0|yanlış|
|1972-02-01T00:00:00Z|799,0|yanlış|
|1972-03-01T00:00:00Z|890,0|yanlış|
|1972-04-01T00:00:00Z|900,0|yanlış|
|1972-05-01T00:00:00Z|766,0|yanlış|
|1972-06-01T00:00:00Z|805,0|yanlış|
|1972-07-01T00:00:00Z|821,0|yanlış|
|1972-08-01T00:00:00Z|20000,0|true|
|1972-09-01T00:00:00Z|883,0|yanlış|
|1972-10-01T00:00:00Z|898,0|yanlış|
|1972-11-01T00:00:00Z|957,0|yanlış|
|1972-12-01T00:00:00Z|924,0|yanlış|
|1973-01-01T00:00:00Z|881,0|yanlış|
|1973-02-01T00:00:00Z|837,0|yanlış|
|1973-03-01T00:00:00Z|9000,0|true|

## <a name="next-steps"></a>Sonraki adımlar

* [SYNAPSE örnek not defterlerine göz atın](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks) 
* [MMLSpark GitHub deposu](https://github.com/Azure/mmlspark)
