---
title: Şemayı diziler ve iç içe yapılar ile analiz etme
description: Apache Spark ve SQL ile dizileri ve iç içe yapıları çözümleme
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: spark
ms.date: 06/15/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.openlocfilehash: d4004887a4806a5b8e50429031fb204f4eec5000
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627974"
---
# <a name="analyze-complex-data-types-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'te karmaşık veri türlerini çözümleme

Bu makale, [Azure Cosmos DB Için Azure SYNAPSE bağlantısı](.\synapse-link\how-to-connect-synapse-link-cosmos-db.md)'Ndaki Parquet dosyaları ve kapsayıcıları için geçerlidir. Diziler veya iç içe yapılar gibi karmaşık şemalarla veri okumak veya dönüştürmek için Spark veya SQL kullanabilirsiniz. Aşağıdaki örnek tek bir belgeyle tamamlandı, ancak Spark veya SQL ile milyarlarca belgeye kolayca ölçeklenebilen. Bu makalede yer alan kod PySpark (Python) kullanır.

## <a name="use-case"></a>Kullanım örneği

Karmaşık veri türleri giderek daha fazla yaygındır ve veri mühendislerinin bir sınamasını temsil eder. İç içe şema ve dizilerin çözümlenmesi, zaman alan ve karmaşık SQL sorguları içerebilir. Ayrıca, iç içe geçmiş sütunlar veri türünü yeniden adlandırmak veya dönüştürmek zor olabilir. Ayrıca, derin iç içe geçmiş nesnelerle çalışırken performans sorunlarıyla karşılaşabilirsiniz.

Veri mühendislerinin karmaşık veri türlerini verimli bir şekilde nasıl işleyeceğini ve herkesin kolayca erişilebilir hale getirme işlemini anlaması gerekir. Aşağıdaki örnekte, veri çerçeveleri aracılığıyla nesneleri düz bir yapıda okumak ve dönüştürmek için Azure SYNAPSE Analytics 'te Spark kullanacaksınız. Bu tür nesneleri doğrudan sorgulamak için Azure SYNAPSE Analytics 'te sunucusuz bir SQL modelini kullanırsınız ve bu sonuçları normal bir tablo olarak döndürebilirsiniz.

## <a name="what-are-arrays-and-nested-structures"></a>Diziler ve iç içe yerleştirilmiş yapılar nelerdir?

Aşağıdaki nesne [Application Insights](../azure-monitor/app/app-insights-overview.md)gelir. Bu nesnede, iç içe yapılar içeren iç içe yapılar ve diziler vardır.

```json
{
    "id": "66532691-ab20-11ea-8b1d-936b3ec64e54",
    "context": {
        "data": {
            "eventTime": "2020-06-10T13:43:34.553Z",
            "samplingRate": "100.0",
            "isSynthetic": "false"
        },
        "session": {
            "isFirst": "false",
            "id": "38619c14-7a23-4687-8268-95862c5326b1"
        },
        "custom": {
            "dimensions": [
                {
                    "customerInfo": {
                        "ProfileType": "ExpertUser",
                        "RoomName": "",
                        "CustomerName": "diamond",
                        "UserName": "XXXX@yahoo.com"
                    }
                },
                {
                    "customerInfo": {
                        "ProfileType": "Novice",
                        "RoomName": "",
                        "CustomerName": "topaz",
                        "UserName": "XXXX@outlook.com"
                    }
                }
            ]
        }
    }
}
```

### <a name="schema-example-of-arrays-and-nested-structures"></a>Diziler ve iç içe yapılar için şema örneği
Komutuyla nesnenin veri çerçevesinin ( **df** olarak adlandırılır) şemasını yazdırırken `df.printschema` aşağıdaki gösterimi görürsünüz:

* Sarı iç içe yapıları temsil eder.
* Yeşil iki öğe içeren bir diziyi temsil eder.

[![Şema kaynağını gösteren sarı ve yeşil vurgu içeren kod](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

`_rid`, `_ts` ve, `_etag` belge Azure Cosmos DB işlem deposuna alındığı için sisteme eklenmiştir.

Önceki veri çerçevesi yalnızca 5 sütun ve yalnızca 1 satır için sayılır. Dönüşümden sonra, seçkin veri çerçevesinin tablolu biçimde 13 sütun ve 2 satırı olacaktır.

## <a name="flatten-nested-structures-and-explode-arrays"></a>İç içe yapıları düzleştirme ve dizileri açın

Azure SYNAPSE Analytics 'te Spark sayesinde, iç içe yapıları sütunlara ve dizi öğelerine birden çok satıra dönüştürmek kolaydır. Uygulama için aşağıdaki adımları kullanın.

[![Spark dönüştürmeleri için adımları gösteren akış çizelgesi](./media/how-to-complex-schema/spark-transform-steps.png)](./media/how-to-complex-schema/spark-transform-steps.png#lightbox)

### <a name="define-a-function-to-flatten-the-nested-schema"></a>İç içe şemayı düzleştirmek için bir işlev tanımlama

Bu işlevi, değişiklik olmadan kullanabilirsiniz. [Pyspark not defterinde](quickstart-apache-spark-notebook.md) aşağıdaki işlevle bir hücre oluşturun:

```python
from pyspark.sql.functions import col

def flatten_df(nested_df):
    stack = [((), nested_df)]
    columns = []

    while len(stack) > 0:
        parents, df = stack.pop()

        flat_cols = [
            col(".".join(parents + (c[0],))).alias("_".join(parents + (c[0],)))
            for c in df.dtypes
            if c[1][:6] != "struct"
        ]

        nested_cols = [
            c[0]
            for c in df.dtypes
            if c[1][:6] == "struct"
        ]

        columns.extend(flat_cols)

        for nested_col in nested_cols:
            projected_df = df.select(nested_col + ".*")
            stack.append((parents + (nested_col,), projected_df))

    return nested_df.select(columns)
```

### <a name="use-the-function-to-flatten-the-nested-schema"></a>İç içe şemayı düzleştirmek için işlevini kullanın

Bu adımda, veri çerçevesinin (**df**) iç içe şemasını yeni bir veri çerçevesine () düzleştirmeniz gerekir `df_flat` :

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

Görüntüleme işlevi 10 sütun ve 1 satır döndürmelidir. Dizi ve iç içe yerleştirilmiş öğeleri hala orada.

### <a name="transform-the-array"></a>Diziyi Dönüştür

Burada, `context_custom_dimensions` veri çerçevesindeki diziyi `df_flat` Yeni bir veri çerçevesine dönüştürürler `df_flat_explode` . Aşağıdaki kodda, hangi sütunu seçistediğinizi de tanımlamanız gerekir:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

Görüntüleme işlevi 10 sütun ve 2 satır döndürmelidir. Sonraki adım, 1. adımda tanımlanan işlevle iç içe şemaları düzleştirmeniz olur.

### <a name="use-the-function-to-flatten-the-nested-schema"></a>İç içe şemayı düzleştirmek için işlevini kullanın

Son olarak, veri çerçevesinin iç içe şemasını `df_flat_explode` , yeni bir veri çerçevesine düzleştirmek için işlevini kullanın `df_flat_explode_flat` :
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

Görüntüleme işlevi 13 sütun ve 2 satır göstermelidir.

`printSchema`Veri çerçevesinin işlevi `df_flat_explode_flat` aşağıdaki sonucu verir:

[![Son şemayı gösteren kod](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly"></a>Dizileri ve iç içe yapıları doğrudan okuyun

Sunucusuz SQL modeliyle, bu tür nesneler üzerinde görünümleri ve tabloları sorgulayabilir ve oluşturabilirsiniz.

Birincisi, verilerin nasıl depolandığına bağlı olarak, kullanıcılar aşağıdaki taksonomiyi kullanmalıdır. Büyük harfle gösterilen her şey kullanım örneğine özeldir:

| Toplu | Biçimlendir |
| ------ | ------ |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |' Parquet ' (ADLSg2)|
| N 'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; Account = ACCOUNTNAME; Database = DatabaseName; koleksiyon = COLLECTIONNAME; Region = REGIONTOQUERY ', gizli = ' YOURSECRET ' |' CosmosDB ' (Azure SYNAPSE bağlantısı)|


Her bir alanı aşağıdaki gibi değiştirin:
* ' Toplu sürüm ', bağlandığınız veri kaynağının bağlantı dizesidir.
* ' YUKARıDAKI tür ', kaynağa bağlanmak için kullandığınız biçimdir.

```sql
select *
FROM
openrowset(
    BULK 'YOUR BULK ABOVE',
    FORMAT='YOUR TYPE ABOVE'
)
with (id varchar(50),
        contextdataeventTime varchar(50) '$.context.data.eventTime',
        contextdatasamplingRate varchar(50) '$.context.data.samplingRate',
        contextdataisSynthetic varchar(50) '$.context.data.isSynthetic',
        contextsessionisFirst varchar(50) '$.context.session.isFirst',
        contextsessionid varchar(50) '$.context.session.id',
        contextcustomdimensions varchar(max) '$.context.custom.dimensions'
) as q 
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType',
            RoomName varchar(50) '$.customerInfo.RoomName',
            CustomerName varchar(50) '$.customerInfo.CustomerName',
            UserName varchar(50) '$.customerInfo.UserName'
    )
```

İki farklı türde işlem vardır:

- İlk işlem türü, iç içe öğe öğesine başvuran adlı sütunu tanımlayan aşağıdaki kod satırında gösterilir `contextdataeventTime` `Context.Data.eventTime` . 
  ```sql
  contextdataeventTime varchar(50) '$.context.data.eventTime'
  ```

  Bu satır `contextdataeventTime` , iç içe öğe öğesine başvuran adlı sütunu tanımlar `Context>Data>eventTime` .

- İkinci işlem türü, `cross apply` dizi altındaki her öğe için yeni satırlar oluşturmak için kullanır. Ardından, iç içe geçmiş her nesneyi tanımlar. 
  ```sql
  cross apply openjson (contextcustomdimensions) 
  with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
  ```

  Dizide 4 iç içe yapı içeren 5 öğe varsa, sunucusuz SQL modelinin 5 satırı ve 4 sütunu döndürür. Sunucusuz SQL modeli yerinde sorgu yapabilir, diziyi 2 satır ile eşler ve tüm iç içe yerleştirilmiş yapıları sütunlar halinde görüntüleyebilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Spark ile Azure Cosmos DB için SYNAPSE bağlantısını sorgulama hakkında bilgi edinin](./synapse-link/how-to-query-analytical-store-spark.md)
* [Parquet iç içe türlerini sorgulama](./sql/query-parquet-nested-types.md)