---
title: 'Öğretici: depolama hesaplarında verileri çözümlemeye başlama'
description: Bu öğreticide, depolama hesabında bulunan verileri çözümlemeyi öğreneceksiniz.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 6b88a7e6a9851018fce255fac0e39a30563b9bf4
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363844"
---
# <a name="analyze-data-in-a-storage-account"></a>Depolama hesabındaki verileri analiz etme

Bu öğreticide, depolama hesabında bulunan verileri çözümlemeyi öğreneceksiniz.

## <a name="overview"></a>Genel Bakış

Şimdiye kadar, çalışma alanındaki veritabanlarında verilerin bulunduğu senaryolar kapsandık. Artık depolama hesaplarında dosyalarla nasıl çalışacağız gösterilmektedir. Bu senaryoda, çalışma alanının ve çalışma alanını oluştururken belirttiğimiz kapsayıcının birincil depolama hesabını kullanacağız.

* Depolama hesabının adı: **contosolake**
* Depolama hesabındaki kapsayıcının adı: **Kullanıcılar**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>Depolama hesabınızda CSV ve Parquet dosyaları oluşturma

Aşağıdaki kodu yeni bir kod hücresindeki bir not defterinde çalıştırın. Depolama hesabında bir CSV dosyası ve bir Parquet dosyası oluşturur.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats_csvformat")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats_parquetformat")
```

### <a name="analyze-data-in-a-storage-account"></a>Depolama hesabındaki verileri analiz etme

Çalışma alanınızın varsayılan ADLS 2. hesabındaki verileri çözümleyebilir veya "**Yönet**" > "**bağlı hizmetler**" > "**Yeni**" ADLS 2. veya bir BLOB depolama hesabını çalışma alanınıza bağlayabilirsiniz (aşağıdaki adımlar birincil ADLS 2. hesabına başvuracaktır).

1. SYNAPSE Studio 'da **veri** merkezine gidin ve **bağlı**' yı seçin.
1. **Azure Data Lake Storage 2.**  >  **MyWorkspace (Primary-contosolake)** bölümüne gidin.
1. **Kullanıcıları (birincil)** seçin. **NYCTaxi** klasörünü görmeniz gerekir. İçinde **PassengerCountStats_csvformat** ve **PassengerCountStats_parquetformat** adlı iki klasör görmeniz gerekir.
1. **PassengerCountStats_parquetformat** klasörünü açın. İçinde, benzer bir adı olan bir Parquet dosyası görürsünüz `part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet` .
1. **. Parquet** öğesine sağ tıklayın, ardından **Yeni Not defteri**' ni ve ardından **dataframe 'e yükle**' yi seçin. Aşağıdaki gibi bir hücreyle yeni bir not defteri oluşturulur:

    ```py
    %%pyspark
    abspath = 'abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet'
    df = spark.read.load(abspath, format='parquet')
    display(df.limit(10))
    ```

1. **Spark1** adlı Spark havuzuna ekleyin. Hücreyi çalıştırın.
1. **Kullanıcılar** klasörüne geri dön seçeneğini belirleyin. **. Parquet** dosyasına tekrar sağ tıklayın ve ardından **Yeni SQL betiği**  >  **en üstteki 100 satırları** Seç ' i seçin. Şöyle bir SQL betiği oluşturur:

    ```sql
    SELECT 
        TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [result]
    ```

    Betik penceresinde, **Bağlan** alanının **YERLEŞIK** sunucusuz SQL havuzu olarak ayarlandığından emin olun.

1. Betiği çalıştırın.



## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [İşlem hatları ile etkinlikleri düzenleme](get-started-pipelines.md)
