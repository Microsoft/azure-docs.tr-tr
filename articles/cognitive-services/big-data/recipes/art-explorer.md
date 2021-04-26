---
title: 'Tarif: büyük veriler için bilişsel hizmetler ile akıllı resim araştırması'
titleSuffix: Azure Cognitive Services
description: Bu tarif, Azure Search ve MMLSpark kullanılarak aranabilir bir resim veritabanının nasıl oluşturulacağını gösterir.
services: cognitive-services
author: mhamilton723
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: how-to
ms.date: 07/06/2020
ms.author: marhamil
ms.custom: devx-track-python
ms.openlocfilehash: 5a65ff28a38e42e05844063a330c0325f16b2247
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94363298"
---
# <a name="recipe-intelligent-art-exploration-with-the-cognitive-services-for-big-data"></a>Tarif: büyük veriler için bilişsel hizmetler ile akıllı resim araştırması

Bu örnekte, büyük veriler için bilişsel hizmetler 'i, metropol Museum 'nun (KARŞıLANDıĞıNDAN) açık erişim koleksiyonuna akıllı ek açıklamalar ekleyecek şekilde kullanacağız. Bu, el ile ek açıklama olmadan bile Azure Search kullanarak akıllı bir arama altyapısı oluşturmamızı sağlar. 

## <a name="prerequisites"></a>Önkoşullar

* Görüntü İşleme ve Bilişsel Arama için bir abonelik anahtarınız olması gerekir. Görüntü İşleme abone olmak ve anahtarınızı almak için bilişsel [Hizmetler oluşturma](../../cognitive-services-apis-create-account.md) ' daki yönergeleri izleyin.
  > [!NOTE]
  > Fiyatlandırma bilgileri için bkz. [Azure bilişsel arama](https://azure.microsoft.com/services/search/#pricing).

## <a name="import-libraries"></a>Kitaplıkları içeri aktarma

Bu tarif için kitaplıkları içeri aktarmak üzere aşağıdaki komutu çalıştırın.

```python
import os, sys, time, json, requests
from pyspark.ml import Transformer, Estimator, Pipeline
from pyspark.ml.feature import SQLTransformer
from pyspark.sql.functions import lit, udf, col, split
```

## <a name="set-up-subscription-keys"></a>Abonelik anahtarlarını ayarlama

Hizmet anahtarlarına yönelik değişkenleri ayarlamak için aşağıdaki komutu çalıştırın. Görüntü İşleme ve Azure Bilişsel Arama için abonelik anahtarlarınızı ekleyin.

```python
VISION_API_KEY = 'INSERT_COMPUTER_VISION_SUBSCRIPTION_KEY'
AZURE_SEARCH_KEY = 'INSERT_AZURE_COGNITIVE_SEARCH_SUBSCRIPTION_KEY'
search_service = "mmlspark-azure-search"
search_index = "test"
```

## <a name="read-the-data"></a>Verileri okuyun

KARŞıLANDıĞıNDAN açık erişim koleksiyonundan veri yüklemek için aşağıdaki komutu çalıştırın.

```python
data = spark.read\
  .format("csv")\
  .option("header", True)\
  .load("wasbs://publicwasb@mmlspark.blob.core.windows.net/metartworks_sample.csv")\
  .withColumn("searchAction", lit("upload"))\
  .withColumn("Neighbors", split(col("Neighbors"), ",").cast("array<string>"))\
  .withColumn("Tags", split(col("Tags"), ",").cast("array<string>"))\
  .limit(25)
```

<a name="AnalyzeImages"></a>

## <a name="analyze-the-images"></a>Görüntüleri analiz etme

KARŞıLANDıĞıNDAN açık erişim artçalışmaları koleksiyonunda Görüntü İşleme kullanmak için aşağıdaki komutu çalıştırın. Sonuç olarak, artçalışmalardan görsel özellikler elde edersiniz.

```python
from mmlspark.cognitive import AnalyzeImage
from mmlspark.stages import SelectColumns

#define pipeline
describeImage = (AnalyzeImage()
  .setSubscriptionKey(VISION_API_KEY)
  .setLocation("eastus")
  .setImageUrlCol("PrimaryImageUrl")
  .setOutputCol("RawImageDescription")
  .setErrorCol("Errors")
  .setVisualFeatures(["Categories", "Tags", "Description", "Faces", "ImageType", "Color", "Adult"])
  .setConcurrency(5))

df2 = describeImage.transform(data)\
  .select("*", "RawImageDescription.*").drop("Errors", "RawImageDescription")
```

<a name="CreateSearchIndex"></a>

## <a name="create-the-search-index"></a>Arama dizini oluşturma

Görüntü İşleme, gelişmiş meta verilerle artçalışır bir arama altyapısı oluşturmak için Azure Search sonuçları yazmak üzere aşağıdaki komutu çalıştırın.

```python
from mmlspark.cognitive import *
df2.writeToAzureSearch(
  subscriptionKey=AZURE_SEARCH_KEY,
  actionCol="searchAction",
  serviceName=search_service,
  indexName=search_index,
  keyCol="ObjectID"
)
```

## <a name="query-the-search-index"></a>Arama dizinini sorgulama

Azure Search dizinini sorgulamak için aşağıdaki komutu çalıştırın.

```python
url = 'https://{}.search.windows.net/indexes/{}/docs/search?api-version=2019-05-06'.format(search_service, search_index)
requests.post(url, json={"search": "Glass"}, headers = {"api-key": AZURE_SEARCH_KEY}).json()
```

## <a name="next-steps"></a>Sonraki adımlar

[Anomali algılama Için büyük veriler için](anomaly-detection.md)bilişsel hizmetler 'i nasıl kullanacağınızı öğrenin.