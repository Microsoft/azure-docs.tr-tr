---
title: Bire çok ilişkisel verileri Azure Cosmos DB SQL API 'sine geçirme
description: SQL API ile bire çok arasında ilişkiler için karmaşık veri geçişini nasıl işleyeceğinizi öğrenin
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 7dbb162749e0a2f84140b8e9394934198d096eac
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589627"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>Bire çok ilişkisel verileri Azure Cosmos DB SQL API hesabına geçirme
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

İlişkisel bir veritabanından Azure Cosmos DB SQL API 'sine geçiş yapmak için, veri modelinde değişiklik yapmak üzere verilerin iyileştirilmesi için gerekli olabilir.

Yaygın olarak kullanılan bir dönüşüm, ilgili alt öğeleri tek bir JSON belgesinde katıştırarak verilerin yeniden ortaya laştırılması. Burada Azure Data Factory veya Azure Databricks kullanarak bunun için birkaç seçenek inceliyoruz. Cosmos DB için veri modellemesi hakkında genel rehberlik için lütfen [Azure Cosmos DB veri modellemesini](modeling-data.md)inceleyin.  

## <a name="example-scenario"></a>Örnek Senaryo

SQL veritabanımız, siparişlerimiz ve OrderDetails içinde aşağıdaki iki tabloya sahip olduğunu varsayalım.


:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/orders.png" alt-text="SQL veritabanındaki Orders ve OrderDetails tablolarını gösteren ekran görüntüsü." border="false" :::

Bu bire bir ilişkiyi geçiş sırasında tek bir JSON belgesiyle birleştirmek istiyoruz. Bunu yapmak için, aşağıdaki gibi "JSON IÇIN" kullanarak bir T-SQL sorgusu oluşturuyoruz:

```sql
SELECT
  o.OrderID,
  o.OrderDate,
  o.FirstName,
  o.LastName,
  o.Address,
  o.City,
  o.State,
  o.PostalCode,
  o.Country,
  o.Phone,
  o.Total,
  (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
FROM Orders o;
```

Bu sorgunun sonuçları aşağıdaki gibi görünür: 

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png" alt-text="Sipariş Ayrıntıları" lightbox="./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png":::

İdeal olarak, SQL verilerini kaynak olarak sorgulamak ve çıktıyı doğrudan doğru JSON nesneleri olarak Azure Cosmos DB havuzuna yazmak için tek bir Azure Data Factory (ADF) kopyalama etkinliği kullanmak istersiniz. Şu anda, bir kopyalama etkinliğinde gerekli JSON dönüşümünü gerçekleştirmek mümkün değildir. Yukarıdaki sorgunun sonuçlarını Azure Cosmos DB bir SQL API kapsayıcısına kopyalamaya çalışırsam, OrderDetails alanını beklenen JSON dizisi yerine belgeimizin dize özelliği olarak göreceğiz.

Aşağıdaki yollarla bu geçerli sınırlamayı geçici olarak çözebiliriz:

* **İki kopyalama etkinliği ile Azure Data Factory kullanın**: 
  1. SQL 'den bir ara BLOB depolama konumundaki bir metin dosyasına JSON biçimli veri alın ve 
  2. JSON metin dosyasındaki verileri Azure Cosmos DB bir kapsayıcıya yükleyin.

* **SQL 'den okumak ve Azure Cosmos DB yazmak için Azure Databricks kullanın** . burada iki seçenek sunacağız.


Daha ayrıntılı bilgi için şu yaklaşımları göz atalım:

## <a name="azure-data-factory"></a>Azure Data Factory

OrderDetails 'i hedef Cosmos DB belgesine JSON dizisi olarak ekleyemediğimiz halde, iki ayrı kopyalama etkinliği kullanarak soruna geçici bir çözüm bulabilirsiniz.

### <a name="copy-activity-1-sqljsontoblobtext"></a>Kopyalama etkinliği #1: SqlJsonToBlobText

Kaynak veriler için, OPENJSON ve JSON yolu yeteneklerini SQL Server kullanarak sonuç kümesini tek bir JSON nesnesi (sırayı temsil eden) ile tek bir sütun olarak almak için bir SQL sorgusu kullanırız:

```sql
SELECT [value] FROM OPENJSON(
  (SELECT
    id = o.OrderID,
    o.OrderDate,
    o.FirstName,
    o.LastName,
    o.Address,
    o.City,
    o.State,
    o.PostalCode,
    o.Country,
    o.Phone,
    o.Total,
    (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
   FROM Orders o FOR JSON PATH)
)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf1.png" alt-text="ADF kopyası":::


SqlJsonToBlobText kopyalama etkinliğinin havuzu için, "sınırlandırılmış metin" i seçer ve dinamik olarak oluşturulan benzersiz bir dosya adına (örneğin, ' @concat (işlem hattı ()) Azure Blob depolama 'daki belirli bir klasöre işaret ediyor. RunId, '. json ').
Metin dosyamız gerçekten "sınırlı" olmadığından ve çift tırnak işareti (") korumak istediğimiz için," sütun sınırlayıcısı "seçeneğini bir sekmeye (" \t ") veya veri ve" tırnak karakteri "olarak" tırnak işareti karakteri "olarak belirledik.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf2.png" alt-text="Sütun sınırlayıcısı ve teklif karakteri ayarlarını vurgulayan ekran görüntüsü.":::

### <a name="copy-activity-2-blobjsontocosmos"></a>Kopyalama etkinliği #2: BlobJsonToCosmos

Daha sonra, ilk etkinlik tarafından oluşturulan metin dosyası için Azure Blob depolama alanına görünen ikinci kopyalama etkinliğini ekleyerek ADF işlem hattımızı değiştirdik. Metin dosyasında her JSON satırı için bir belge olarak Cosmos DB havuza eklemek üzere bunu "JSON" kaynağı olarak işler.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf3.png" alt-text="JSON kaynak dosyasını ve dosya yolu alanlarını vurgulayan ekran görüntüsü.":::

İsteğe bağlı olarak, her çalıştırmadan önce/Orders/klasöründe kalan tüm dosyaları silmesi için işlem hattına bir "Sil" etkinliği de ekleyeceğiz. ADF işlem hatmız şu şekilde görünür:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf4.png" alt-text="Silme etkinliğini vurgulayan ekran görüntüsü.":::

Yukarıdaki işlem hattını tetikledikten sonra, her satır için bir JSON nesnesi içeren ara Azure Blob depolama konumunda oluşturulmuş bir dosya görüyoruz:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf5.png" alt-text="JSON nesnelerini içeren oluşturulan dosyayı gösteren ekran görüntüsü.":::

Ayrıca, Cosmos DB koleksiyonumuza eklenmiş, doğru gömülü OrderDetails içeren siparişler belgelerini de görüyorsunuz:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/adf6.png" alt-text="Cosmos DB belgenin bir parçası olarak sipariş ayrıntılarını gösteren ekran görüntüsü":::


## <a name="azure-databricks"></a>Azure Databricks

Azure Blob depolamada ara metin/JSON dosyalarını oluşturmadan verileri SQL veritabanı kaynağınızdan Azure Cosmos DB hedefe kopyalamak için [Azure Databricks](https://azure.microsoft.com/services/databricks/) Spark 'ı da kullanabiliriz. 

> [!NOTE]
> Netlik ve kolaylık sağlaması için aşağıdaki kod parçacıkları açık olarak satır içi boş veritabanı parolaları içerir, ancak her zaman gizli dizileri Azure Databricks kullanmanız gerekir.
>

İlk olarak, gerekli [SQL bağlayıcısını](/connectors/sql/) ve [Azure Cosmos DB bağlayıcı](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html) kitaplıklarını oluşturup Azure Databricks kümemize iliştirdik. Kitaplıkların yüklü olduğundan emin olmak için kümeyi yeniden başlatın.

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks1.png" alt-text="Gerekli SQL bağlayıcısının ve Azure Cosmos DB bağlayıcı kitaplıklarının Azure Databricks kümemize nerede oluşturulacağını ve iliştirilebileceği gösteren ekran görüntüsü.":::

Ardından, Scala ve Python için iki örnek sunuyoruz. 

### <a name="scala"></a>Scala
Burada, SQL sorgusunun sonuçları bir veri çerçevesine "FOR JSON" çıktısı ile alınır:

```scala
// Connect to Azure SQL /connectors/sql/
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
val configSql = Config(Map(
  "url"          -> "xxxx.database.windows.net",
  "databaseName" -> "xxxx",
  "queryCustom"  -> "SELECT o.OrderID, o.OrderDate, o.FirstName, o.LastName, o.Address, o.City, o.State, o.PostalCode, o.Country, o.Phone, o.Total, (SELECT OrderDetailId, ProductId, UnitPrice, Quantity FROM OrderDetails od WHERE od.OrderId = o.OrderId FOR JSON AUTO) as OrderDetails FROM Orders o",
  "user"         -> "xxxx", 
  "password"     -> "xxxx" // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
))
// Create DataFrame from Azure SQL query
val orders = sqlContext.read.sqlDB(configSql)
display(orders)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks2.png" alt-text="SQL sorgu çıkışını bir veri çerçevesinde gösteren ekran görüntüsü.":::

Sonra, Cosmos DB veritabanı ve koleksiyonumuza bağlandık:

```scala
// Connect to Cosmos DB https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
val configMap = Map(
  "Endpoint" -> "https://xxxx.documents.azure.com:443/",
  // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
  "Masterkey" -> "xxxx",
  "Database" -> "StoreDatabase",
  "Collection" -> "Orders")
val configCosmos = Config(configMap)
```

Son olarak, şemanızı tanımlar ve veri çerçevesini CosmosDB koleksiyonuna kaydetmeden önce uygulamak için from_json kullanın.

```scala
// Convert DataFrame to proper nested schema
import org.apache.spark.sql.types._
val orderDetailsSchema = ArrayType(StructType(Array(
    StructField("OrderDetailId",IntegerType,true),
    StructField("ProductId",IntegerType,true),
    StructField("UnitPrice",DoubleType,true),
    StructField("Quantity",IntegerType,true)
  )))
val ordersWithSchema = orders.select(
  col("OrderId").cast("string").as("id"),
  col("OrderDate").cast("string"),
  col("FirstName").cast("string"),
  col("LastName").cast("string"),
  col("Address").cast("string"),
  col("City").cast("string"),
  col("State").cast("string"),
  col("PostalCode").cast("string"),
  col("Country").cast("string"),
  col("Phone").cast("string"),
  col("Total").cast("double"),
  from_json(col("OrderDetails"), orderDetailsSchema).as("OrderDetails")
)
display(ordersWithSchema)
// Save nested data to Cosmos DB
CosmosDBSpark.save(ordersWithSchema, configCosmos)
```

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks3.png" alt-text="Cosmos DB koleksiyonuna kaydetmek için uygun diziyi vurgulayan ekran görüntüsü.":::


### <a name="python"></a>Python

Alternatif bir yaklaşım olarak, Spark (kaynak veritabanı "JSON IÇIN" veya benzer bir işlem) için JSON dönüştürmeleri yürütmeniz gerekebilir ya da çok büyük bir veri kümesi için paralel işlemleri kullanmak isteyebilirsiniz. Burada PySpark örneği sunuyoruz. İlk hücrede kaynak ve hedef veritabanı bağlantılarını yapılandırarak başlayın:

```python
import uuid
import pyspark.sql.functions as F
from pyspark.sql.functions import col
from pyspark.sql.types import StringType,DateType,LongType,IntegerType,TimestampType
 
#JDBC connect details for SQL Server database
jdbcHostname = "jdbcHostname"
jdbcDatabase = "OrdersDB"
jdbcUsername = "jdbcUsername"
jdbcPassword = "jdbcPassword"
jdbcPort = "1433"
 
connectionProperties = {
  "user" : jdbcUsername,
  "password" : jdbcPassword,
  "driver" : "com.microsoft.sqlserver.jdbc.SQLServerDriver"
}
jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, jdbcUsername, jdbcPassword)
 
#Connect details for Target Azure Cosmos DB SQL API account
writeConfig = {
    "Endpoint": "Endpoint",
    "Masterkey": "Masterkey",
    "Database": "OrdersDB",
    "Collection": "Orders",
    "Upsert": "true"
}
```

Daha sonra, kaynak veritabanını (Bu durumda SQL Server) hem sipariş hem de sipariş ayrıntısı kayıtları için sorgularız ve sonuçları Spark veri çerçevelerine yerleştireceğiz. Ayrıca, tüm sıra kimliklerini içeren bir liste ve paralel işlemler için bir Iş parçacığı havuzu oluşturacağız:

```python
import json
import ast
import pyspark.sql.functions as F
import uuid
import numpy as np
import pandas as pd
from functools import reduce
from pyspark.sql import SQLContext
from pyspark.sql.types import *
from pyspark.sql import *
from pyspark.sql.functions import exp
from pyspark.sql.functions import col
from pyspark.sql.functions import lit
from pyspark.sql.functions import array
from pyspark.sql.types import *
from multiprocessing.pool import ThreadPool
 
#get all orders
orders = sqlContext.read.jdbc(url=jdbcUrl, table="orders", properties=connectionProperties)
 
#get all order details
orderdetails = sqlContext.read.jdbc(url=jdbcUrl, table="orderdetails", properties=connectionProperties)
 
#get all OrderId values to pass to map function 
orderids = orders.select('OrderId').collect()
 
#create thread pool big enough to process merge of details to orders in parallel
pool = ThreadPool(10)
```

Ardından, hedef SQL API koleksiyonuna sipariş yazmak için bir işlev oluşturun. Bu işlev, belirtilen sipariş KIMLIĞI için tüm sipariş ayrıntılarını filtreleyecek, bunları bir JSON dizisine dönüştürecek ve diziyi bu sıra için hedef SQL API koleksiyonuna yazdığımız bir JSON belgesine ekleyecek:

```python
def writeOrder(orderid):
  #filter the order on current value passed from map function
  order = orders.filter(orders['OrderId'] == orderid[0])
  
  #set id to be a uuid
  order = order.withColumn("id", lit(str(uuid.uuid1())))
  
  #add details field to order dataframe
  order = order.withColumn("details", lit(''))
  
  #filter order details dataframe to get details we want to merge into the order document
  orderdetailsgroup = orderdetails.filter(orderdetails['OrderId'] == orderid[0])
  
  #convert dataframe to pandas
  orderpandas = order.toPandas()
  
  #convert the order dataframe to json and remove enclosing brackets
  orderjson = orderpandas.to_json(orient='records', force_ascii=False)
  orderjson = orderjson[1:-1] 
  
  #convert orderjson to a dictionaory so we can set the details element with order details later
  orderjsondata = json.loads(orderjson)
  
  
  #convert orderdetailsgroup dataframe to json, but only if details were returned from the earlier filter
  if (orderdetailsgroup.count() !=0):
    #convert orderdetailsgroup to pandas dataframe to work better with json
    orderdetailsgroup = orderdetailsgroup.toPandas()
    
    #convert orderdetailsgroup to json string
    jsonstring = orderdetailsgroup.to_json(orient='records', force_ascii=False)
    
    #convert jsonstring to dictionary to ensure correct encoding and no corrupt records
    jsonstring = json.loads(jsonstring)
    
    #set details json element in orderjsondata to jsonstring which contains orderdetailsgroup - this merges order details into the order 
    orderjsondata['details'] = jsonstring
 
  #convert dictionary to json
  orderjsondata = json.dumps(orderjsondata)
 
  #read the json into spark dataframe
  df = spark.read.json(sc.parallelize([orderjsondata]))
  
  #write the dataframe (this will be a single order record with merged many-to-one order details) to cosmos db using spark the connector
  #https://docs.microsoft.com/azure/cosmos-db/spark-connector
  df.write.format("com.microsoft.azure.cosmosdb.spark").mode("append").options(**writeConfig).save()
```

Son olarak, daha önce oluşturduğumuz sıra kimliklerinin listesini geçirerek paralel olarak yürütmek için iş parçacığı havuzunda bir Map işlevi kullanarak yukarıdaki ' ı çağıracağız:

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
Her iki yaklaşımda da sonda, Cosmos DB koleksiyonundaki her bir sipariş belgesinde doğru şekilde kaydedilmiş ekli OrderDetails almalıdır:

:::image type="content" source="./media/migrate-relational-to-cosmos-sql-api/databricks4.png" alt-text="Databricks":::

## <a name="next-steps"></a>Sonraki adımlar
* [Azure Cosmos DB veri modelleme](./modeling-data.md) hakkında bilgi edinin
* [Azure Cosmos DB verileri nasıl modelleyip bölümleyeceğinizi](./how-to-model-partition-example.md) öğrenin
