---
title: Apache Spark kullanarak adanmış bir SQL havuzuna Azure Cosmos DB verileri için SYNAPSE bağlantısını kopyalayın
description: Verileri bir Spark veri çerçevesine yükleyin, verileri seçip özel bir SQL havuzu tablosuna yükleyin
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 08/10/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: ff04d43e72d4eca9800b330d5a4721ba951c85f9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627651"
---
# <a name="copy-data-from-azure-cosmos-db-into-a-dedicated-sql-pool-using-apache-spark"></a>Apache Spark kullanarak Azure Cosmos DB verileri adanmış bir SQL havuzuna kopyalama

Azure Cosmos DB için Azure SYNAPSE bağlantısı, kullanıcıların Azure Cosmos DB işletimsel veriler üzerinde neredeyse gerçek zamanlı analiz çalıştırmasına olanak sağlar. Ancak, veri ambarı kullanıcılarına yönelik olarak bazı verilerin toplanması ve zenginleştirilmesi gereken durumlar vardır. SYNAPSE bağlantısı verilerinin görselleştirme ve dışa aktarılması, bir not defterinde yalnızca birkaç hücre ile yapılabilir.

## <a name="prerequisites"></a>Önkoşullar
* İle [bir Synapse çalışma alanı sağlayın](../quickstart-create-workspace.md) :
    * [Sunucusuz Apache Spark havuzu](../quickstart-create-apache-spark-pool-studio.md)
    * [adanmış SQL havuzu](../quickstart-create-sql-pool-studio.md)
* [Veri içeren bir HTAP kapsayıcısına Cosmos DB hesabı sağlama](../../cosmos-db/configure-synapse-link.md)
* [Azure Cosmos DB HTAP kapsayıcısını çalışma alanına bağlama](./how-to-connect-synapse-link-cosmos-db.md)
* [Spark 'dan adanmış bir SQL havuzuna veri aktarmak için doğru kuruluma sahip](../spark/synapse-spark-sql-pool-import-export.md)

## <a name="steps"></a>Adımlar
Bu öğreticide, işlem deposuna hiçbir etkilenmemesi için analitik depoya bağlanırsınız (herhangi bir Istek birimini tüketmez). Aşağıdaki adımlara gideceğiz:
1. Cosmos DB HTAP kapsayıcısını Spark veri çerçevesine okuyun
2. Sonuçları yeni bir veri çerçevesinde toplama
3. Verileri adanmış bir SQL havuzuna alma

[![Spark to SQL adımları 1](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png)](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png#lightbox)

## <a name="data"></a>Veriler
Bu örnekte, **RetailSales** ADLı BIR htap kapsayıcısı kullanıyoruz. Bu, bağlı bir hizmetin **Connecteddata** adlı bir parçasıdır ve aşağıdaki şemaya sahiptir:
* _rid: dize (Nullable = true)
* _ts: Long (Nullable = true)
* logQuantity: Double (Nullable = true)
* productCode: String (Nullable = true)
* Quantity: Long (Nullable = true)
* Fiyat: Long (Nullable = true)
* Kimlik: dize (Nullable = true)
* Reklam: Long (Nullable = true)
* StoreID: Long (Nullable = true)
* weekStarting: Long (Nullable = true)
* _etag: dize (Nullable = true)

Rapor amaçları doğrultusunda, *productCode* ve *weekby* Sales (*Miktar*, *gelir* (fiyat x miktarı) toplanacak satışları toplayacağız. Son olarak, bu verileri **dbo. ProductSales** adlı özel bir SQL havuzu tablosuna aktaracağız.

## <a name="configure-a-spark-notebook"></a>Spark Not defteri yapılandırma
Ana dil olarak Scala as Spark (Scala) ile bir Spark Not defteri oluşturun. Oturum için Not defterinin varsayılan ayarını kullanıyoruz.

## <a name="read-the-data-in-spark"></a>Spark 'daki verileri okuma
Spark içeren Cosmos DB HTAP kapsayıcısını ilk hücredeki bir veri çerçevesine okuyun.

```java
val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "ConnectedData").
    option("spark.cosmos.container", "RetailSales").
    load()
```

## <a name="aggregate-the-results-in-a-new-dataframe"></a>Sonuçları yeni bir veri çerçevesinde toplama

İkinci hücrede, özel bir SQL havuzu veritabanına yüklemeden önce yeni veri çerçevesi için gereken dönüşümü ve toplamaları çalıştırdık.

```java
// Select relevant columns and create revenue
val df_olap_step1 = df_olap.select("productCode","weekStarting","quantity","price").withColumn("revenue",col("quantity")*col("price"))
//Aggregate revenue, quantity sold and avg. price by week and product ID
val df_olap_aggr = df_olap_step1.groupBy("productCode","weekStarting").agg(sum("quantity") as "Sum_quantity",sum("revenue") as "Sum_revenue").
    withColumn("AvgPrice",col("Sum_revenue")/col("Sum_quantity"))
```

## <a name="load-the-results-into-a-dedicated-sql-pool"></a>Sonuçları adanmış bir SQL havuzuna yükleme

Üçüncü hücrede, verileri adanmış bir SQL havuzuna yükledik. İş tamamlandıktan sonra silinecek geçici bir dış tablo, dış veri kaynağı ve dış dosya biçimi otomatik olarak oluşturulur.

```java
df_olap_aggr.write.sqlanalytics("userpool.dbo.productsales", Constants.INTERNAL)
```

## <a name="query-the-results-with-sql"></a>Sonuçları SQL ile sorgulama

Aşağıdaki SQL betiği gibi basit bir SQL sorgusu kullanarak sonucu sorgulayabilirsiniz:
```sql
SELECT  [productCode]
,[weekStarting]
,[Sum_quantity]
,[Sum_revenue]
,[AvgPrice]
 FROM [dbo].[productsales]
```

Sorgunuz bir grafik modunda aşağıdaki sonuçları sunar: [ ![ Spark to SQL Steps 2](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png)](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png#lightbox)

## <a name="next-steps"></a>Sonraki adımlar
* [Analitik depoyu Apache Spark ile sorgula Azure Cosmos DB](./how-to-query-analytical-store-spark.md)