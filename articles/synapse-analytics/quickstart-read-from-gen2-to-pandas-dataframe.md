---
title: "Hızlı başlangıç: ADLS 2. Pandas dataframe 'ten veri okuma"
description: Azure SYNAPSE Analytics 'te SYNAPSE Studio 'da Python kullanarak bir Azure Data Lake Storage 2. hesabındaki verileri bir Pandas dataframe 'e okuyun.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: quickstart
ms.reviewer: jrasnick, garye, negust
ms.date: 03/23/2021
author: garyericson
ms.author: garye
ms.openlocfilehash: b7358c522cf12e7856496ad71fda393394e7ceab
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969891"
---
# <a name="quickstart-read-data-from-adls-gen2-to-pandas-dataframe-in-azure-synapse-analytics"></a>Hızlı başlangıç: Azure SYNAPSE Analytics 'te Pandas dataframe 'e ADLS 2. verileri okuma

Bu hızlı başlangıçta, Azure Data Lake Storage (ADLS) Gen2 Azure SYNAPSE Analytics 'teki bir Pandas dataframe 'e veri okumak için nasıl kolayca Python kullanacağınızı öğreneceksiniz.

Bir Synapse Studio Not defterinden şunları yapabilirsiniz:

- Azure SYNAPSE Analytics çalışma alanınıza bağlı Data Lake Storage 2. bir kapsayıcıya bağlanma
- kullanarak PySpark not defteriyle verileri okuma `spark.read.load`
- kullanarak verileri bir Pandas dataframe 'e dönüştürme `.toPandas()`

## <a name="prerequisites"></a>Önkoşullar

- Azure aboneliği- [ücretsiz olarak bir tane oluşturun](https://azure.microsoft.com/free/).
- Varsayılan depolama olarak yapılandırılmış Data Lake Storage 2. Analytics çalışma alanı-ile birlikte çalıştığınız Data Lake Storage 2. FileSystem **Depolama Blobu veri katılımcısı** olması gerekir. Çalışma alanı oluşturma hakkında ayrıntılı bilgi için bkz. [SYNAPSE çalışma alanı oluşturma](get-started-create-workspace.md).
- Çalışma alanınızdaki Apache Spark havuzu-bkz. [sunucusuz Apache Spark havuzu oluşturma](get-started-analyze-spark.md#create-a-serverless-apache-spark-pool).

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com/) oturum açın.

## <a name="upload-sample-data-to-adls-gen2"></a>Örnek verileri ADLS 2. karşıya yükleme

1. Azure portal, SYNAPSE Studio tarafından kullanılan aynı Data Lake Storage 2. bir kapsayıcı oluşturun. Azure SYNAPSE Analytics çalışma alanınızda varsayılan bağlantılı depolama hesabını kullanmak istiyorsanız bu adımı atlayabilirsiniz.

1. SYNAPSE Studio 'da, **veriler**' e tıklayın, **bağlantılı** sekmesini seçin ve **Azure Data Lake Storage 2.** altında kapsayıcıyı seçin.

1. Örnek dosya [RetailSales.csv](https://github.com/Azure-Samples/Synapse/blob/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData/RetailSales.csv) indirin ve kapsayıcıya yükleyin.

1. Karşıya yüklenen dosyayı seçin, **Özellikler**' e tıklayın ve **Abfss yolu** değerini kopyalayın.

## <a name="read-data-from-adls-gen2-into-a-pandas-dataframe"></a>ADLS 2. verileri bir Pandas dataframe 'e okuma

1. Sol bölmede, **Geliştir**' i tıklatın.

1. **+** Yeni bir not defteri oluşturmak için tıklayın ve "Not defteri" ni seçin.

1. **Ekle**' de Apache Spark havuzunuzu seçin. Bir tane yoksa **Apache Spark havuzu oluştur**' a tıklayın.

1. Not defteri kod hücresinde, daha önce kopyaladığınız ABFSS yolunu ekleyerek aşağıdaki python kodunu yapıştırın:

   ```python
   %%pyspark
   data_path = spark.read.load('<ABFSS Path to RetailSales.csv>', format='csv', header=True)
   data_path.show(10)
   
   print('Converting to Pandas.')
   
   pdf = data_path.toPandas()
   print(pdf)
   ```

1. Hücreyi çalıştırın.

Birkaç dakika sonra, görüntülenecek metin aşağıdakine benzer görünmelidir.

```text
Command executed in 25s 324ms by gary on 03-23-2021 17:40:23.481 -07:00

Job execution Succeeded Spark 2 executors 8 cores

+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
|storeId|productCode|quantity|logQuantity|advertising|price|weekStarting|                  id|
+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
|      2| surface.go|     105|9.264828557|          1|  159|   6/15/2017|d6bd47a7-2ad6-4f0...|
|      2| surface.go|      80|8.987196821|          0|  269|   7/27/2017|64cc74c2-c7da-4e1...|
|      2| surface.go|      68|8.831711918|          1|  209|    8/3/2017|9a2d164b-5e44-44d...|
|      2| surface.go|      28|7.965545573|          0|  209|   8/10/2017|b8cd9987-1d5a-4f4...|
|      2| surface.go|      16|7.377758908|          0|  209|   8/24/2017|ac0ec099-e102-4bf...|
|      2| surface.go|     253| 10.1402973|          1|  189|   8/31/2017|3d22c002-b04c-409...|
|      2| surface.go|     107|9.282847063|          0|  189|    9/7/2017|b6e19699-d684-449...|
|      2| surface.go|      66|8.803273983|          0|  189|   9/14/2017|e89a5838-fb8f-413...|
|      2| surface.go|      65|8.793612072|          0|  179|   9/21/2017|c3278682-16c0-483...|
|      2| surface.go|      17|7.454719949|          0|  269|  10/12/2017|f40190c1-b2ed-46f...|
+-------+-----------+--------+-----------+-----------+-----+------------+--------------------+
only showing top 10 rows

Converting to Pandas.
      storeId  ...                                    id
0           2  ...  d6bd47a7-2ad6-4f0a-b8de-ed1386cae5ea
1           2  ...  64cc74c2-c7da-4e12-af64-c95bdf429934
2           2  ...  9a2d164b-5e44-44d7-9837-cf9ae6566c99
3           2  ...  b8cd9987-1d5a-4f4f-9346-719d73b1f7f0
4           2  ...  ac0ec099-e102-4bfc-9775-983b151dcd03
...       ...  ...                                   ...
28942     137  ...  6af00133-7015-415d-831b-ddf05bb5828c
28943     137  ...  1e0d3a21-ab43-49c4-89e2-49d202821807
28944     137  ...  5cc7e50a-6aa4-419b-a933-905a667aa2df
28945     137  ...  650ca506-7a4f-46f8-b2e1-e52ceffadf16
28946     137  ...  9bb216f6-04ec-4b61-9e68-34772b814c44

[28947 rows x 8 columns]
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Synapse Analytics nedir?](overview-what-is.md)
- [Azure SYNAPSE Analytics 'i kullanmaya başlama](get-started.md)
- [Sunucusuz Apache Spark havuzu oluşturma](get-started-analyze-spark.md#create-a-serverless-apache-spark-pool)
