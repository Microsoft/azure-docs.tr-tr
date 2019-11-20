---
title: Azure Data Factory 'de desteklenen dosya biçimleri
description: Bu konuda dosya biçimlerini ve dosya tabanlı bağlayıcı Azure Data Factory tarafından desteklenen bir sıkıştırma kodları açıklanmaktadır.
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: jingwang
ms.openlocfilehash: 4a81cc9887610036007b92e43b8bd44f0a8b7740
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075545"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory"></a>Desteklenen dosya biçimleri ve Azure Data factory'de sıkıştırma codec bileşenleri

*Bu makale aşağıdaki bağlayıcılar için geçerlidir: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage 1.](connector-azure-data-lake-store.md), [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md), [Azure dosya depolama](connector-azure-file-storage.md), [dosya sistemi](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [,](connector-hdfs.md), [http](connector-http.md)ve [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

>[!NOTE]
>Yeni biçim tabanlı veri kümesi modeli Data Factory, Ayrıntılar için yukarıdaki ilişkili biçim makalesine bakın. Bu makalede bahsedilen dosya tabanlı veri deposu veri kümesi için aşağıdaki yapılandırmalarda, geriye doğru uyumluluk için olduğu gibi hala desteklenmektedir. İleri doğru olan yeni modeli kullanmanız önerilir. 

## <a name="text-format"></a>Metin biçimi

>[!NOTE]
>Data Factory yeni sınırlandırılmış metin biçimi veri kümesi kullanıma sunulmuştur, Ayrıntılar için bkz. [sınırlandırılmış metin biçimi](format-delimited-text.md) makalesi. Dosya tabanlı veri deposu veri kümesi için aşağıdaki yapılandırma, geriye doğru uyumluluk için olduğu gibi hala desteklenmektedir. İleri doğru olan yeni modeli kullanmanız önerilir.

Bir metin dosyasından okumak veya bir metin dosyasına yazma istiyorsanız `type` özelliğinde `format` veri kümesine bölümünü **TextFormat**. İsterseniz **bölümünde aşağıdaki**isteğe bağlı`format` özellikleri de belirtebilirsiniz. Yapılandırma adımları için [TextFormat örneği](#textformat-example) bölümünü inceleyin.

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| columnDelimiter |Bir dosyadaki sütunları ayırmak için kullanılan karakterdir. Verilerinizi olmayabilir nadir, yazdırılamaz bir karakter kullanmayı düşünebilirsiniz. Örneğin, başlangıç başlık başlangıcını (SOH) temsil eden "\u0001" belirtin. |Yalnızca bir karaktere izin verilir. **Varsayılan** değer **virgül (",")** olarak belirlenmiştir. <br/><br/>Bir Unicode karakteri kullanmak için başvurmak [Unicode karakterler](https://en.wikipedia.org/wiki/List_of_Unicode_characters) karakterin kodunu bulun. |Hayır |
| rowDelimiter |Bir dosyadaki satırları ayırmak için kullanılan karakterdir. |Yalnızca bir karaktere izin verilir. **Varsayılan** değer, okuma sırasında **["\r\n", "\r", "\n"]** değerlerinden biri, yazma sırasında ise **"\r\n"** olarak belirlenmiştir. |Hayır |
| escapeChar |Giriş dosyasının içeriğindeki bir sütun ayırıcısına kaçış karakteri eklemek için kullanılan özel karakterdir. <br/><br/>Bir tablo için hem escapeChar hem de quoteChar parametrelerini aynı anda belirtemezsiniz. |Yalnızca bir karaktere izin verilir. Varsayılan değer yoktur. <br/><br/>Örnek: Sütun sınırlayıcınız virgül (",") karakteriyse ancak metin içinde virgül karakteri kullanılıyorsa (örneğin: "Merhaba, dünya"), "$" karakterini kaçış karakteri olarak tanımlayabilir ve kaynakta "Merhaba$, dünya" dizesini kullanabilirsiniz. |Hayır |
| quoteChar |Bir dize değerini tırnak içine almak için kullanılan karakterdir. Tırnak işareti içindeki sütun ve satır sınırlayıcıları, dize değerinin bir parçası olarak kabul edilir. Bu özellik hem giriş hem de çıkış veri kümelerine uygulanabilir.<br/><br/>Bir tablo için hem escapeChar hem de quoteChar parametrelerini aynı anda belirtemezsiniz. |Yalnızca bir karaktere izin verilir. Varsayılan değer yoktur. <br/><br/>Örneğin, sütun sınırlayıcınız virgül (",") karakteriyse ancak metin içinde virgül karakteri kullanılıyorsa (örneğin: <Merhaba, dünya>), " (çift tırnak) karakterini tırnak karakteri olarak tanımlayabilir ve kaynakta "Merhaba, dünya" dizesini kullanabilirsiniz. |Hayır |
| nullValue |Bir null değeri temsil etmek için kullanılan bir veya daha fazla karakterdir. |Bir veya daha fazla karakter olabilir. **Varsayılan** değerler okuma sırasında **"\N" ve "NULL"** , yazma sırasında ise **"\N"** olarak belirlenmiştir. |Hayır |
| encodingName |Kodlama adını belirtir. |Geçerli bir kodlama adı. Bkz. [Encoding.EncodingName Özelliği](https://msdn.microsoft.com/library/system.text.encoding.aspx). Örnek: windows-1250 veya shift_jis. **Varsayılan** değer **UTF-8** olarak belirlenmiştir. |Hayır |
| firstRowAsHeader |İlk satırın üst bilgi olarak kabul edilip edilmeyeceğini belirtir. Giriş veri kümesinde Data Factory ilk satırı üst bilgi olarak okur. Çıkış veri kümesinde Data Factory ilk satırı üst bilgi olarak yazar. <br/><br/>Örnek senaryolar için bkz. [`firstRowAsHeader` ve `skipLineCount` kullanım senaryoları](#scenarios-for-using-firstrowasheader-and-skiplinecount). |True<br/><b>False (varsayılan)</b> |Hayır |
| skipLineCount |Sayısını gösteren **boş** veri giriş dosyalarından okuma sırasında atlanacak satır. Hem skipLineCount hem de firstRowAsHeader parametresi belirtilirse önce satırlar atlanır, ardından giriş dosyasındaki üst bilgi bilgileri okunur. <br/><br/>Örnek senaryolar için bkz. [`firstRowAsHeader` ve `skipLineCount` kullanım senaryoları](#scenarios-for-using-firstrowasheader-and-skiplinecount). |Tamsayı |Hayır |
| treatEmptyAsNull |Bir giriş dosyasından veri okuma sırasında null veya boş dizenin null değer olarak kabul edilip edilmeyeceğini belirtir. |**True (varsayılan)**<br/>False |Hayır |

### <a name="textformat-example"></a>TextFormat örneği

Bir veri kümesi için aşağıdaki JSON tanımında isteğe bağlı özelliklerin bazılarını belirtilir.

```json
"typeProperties":
{
    "folderPath": "mycontainer/myfolder",
    "fileName": "myblobname",
    "format":
    {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";",
        "quoteChar": "\"",
        "NullValue": "NaN",
        "firstRowAsHeader": true,
        "skipLineCount": 0,
        "treatEmptyAsNull": true
    }
},
```

`escapeChar` yerine `quoteChar` kullanmak için `quoteChar` yazan satırı şu escapeChar ile değiştirin:

```json
"escapeChar": "$",
```

### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>firstRowAsHeader ve skipLineCount kullanım senaryoları

* Dosya olmayan bir kaynaktan bir metin dosyasına kopyalama yapıyorsunuz ve şema meta verilerini (örneğin, SQL şeması) içeren bir üst bilgi satırı eklemek istiyorsunuz. Bu senaryo için çıkış veri kümesinde `firstRowAsHeader` parametresini true olarak belirleyin.
* Üst bilgi satırı içeren bir metin dosyasından dosya olmayan bir havuza kopyalama yapıyorsunuz ve üst bilgi satırını almak istemiyorsunuz. Giriş veri kümesinde `firstRowAsHeader` parametresini true olarak belirleyin.
* Bir metin dosyasından kopyalama yapıyorsunuz ve dosyanın başındaki veri içermeyen veya üst bilgi bilgilerini içeren birkaç satırı atlamak istiyorsunuz. Atlanacak satır sayısını belirtmek için `skipLineCount` değerini belirtin. Dosyanın geri kalan kısmında üst bilgi satırı varsa `firstRowAsHeader` değerini de belirtebilirsiniz. Hem `skipLineCount` hem de `firstRowAsHeader` parametresi belirtilirse önce satırlar atlanır, ardından giriş dosyasındaki başlık bilgileri okunur.

## <a name="json-format"></a>JSON biçimi

>[!NOTE]
>Data Factory yeni JSON biçimi veri kümesi sunuldu, Ayrıntılar için bkz. [JSON](format-json.md) makalesi. Dosya tabanlı veri deposu veri kümesi için aşağıdaki yapılandırma, geriye doğru uyumluluk için olduğu gibi hala desteklenmektedir. İleri doğru olan yeni modeli kullanmanız önerilir.

İçin **bir JSON dosyası olarak içeri/dışarı aktarma-olan içine buralardan Azure Cosmos DB**, içeri/dışarı aktarma JSON belgeleri bölümüne bakın [/Azure Cosmos DB'den veri taşıma](connector-azure-cosmos-db.md) makalesi.

JSON dosyalarını ayrıştırmak veya verileri JSON biçiminde yazmak istiyorsanız, `type` özelliğinde `format` bölümünü **JsonFormat**. İsterseniz **bölümünde aşağıdaki**isteğe bağlı`format` özellikleri de belirtebilirsiniz. Yapılandırma adımları için [JsonFormat örneği](#jsonformat-example) bölümünü inceleyin.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| filePattern |Her bir JSON dosyasında depolanan verilerin desenini belirtir. İzin verilen değerler: **setOfObjects** ve **arrayOfObjects**. **Varsayılan** değer **setOfObjects** olarak belirlenmiştir. Bu desenler hakkında ayrıntılı bilgi için bkz. [JSON dosyası desenleri](#json-file-patterns). |Hayır |
| jsonNodeReference | Bir dizi alanındaki aynı desene sahip verileri yinelemek ve ayıklamak istiyorsanız o dizinin JSON yolunu belirtin. Bu özellik **yalnızca JSON dosyalarından** veri kopyalanırken desteklenir. | Hayır |
| jsonPathDefinition | Her sütun için JSON yolu ifadesini belirtin ve özel bir sütun adıyla eşleyin (küçük harfle başlatın). Bu özellik yalnızca JSON dosyalarından veri kopyalarken desteklenir ve nesne veya **diziden veri ayıklayabilirsiniz** . <br/><br/> Kök nesne altındaki alanlar için root $ ile, `jsonNodeReference` özelliği tarafından seçilen dizinin içindeki alanlar için ise dizi öğesiyle başlayın. Yapılandırma adımları için [JsonFormat örneği](#jsonformat-example) bölümünü inceleyin. | Hayır |
| encodingName |Kodlama adını belirtir. Geçerli kodlama adlarının listesi için bkz. [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) Özelliği. Örneğin: windows-1250 veya shift_jis. **Varsayılan** değer **UTF-8** olarak belirlenmiştir. |Hayır |
| nestingSeparator |İç içe geçme düzeylerini ayırmak için kullanılan karakterdir. Varsayılan değer "." (nokta) olarak belirlenmiştir. |Hayır |

>[!NOTE]
>Dizideki verileri birden çok satıra çapraz uygulama (büyük/küçük harf 1), [Jsonformat örneklerinde](#jsonformat-example)> örnek 2) için, yalnızca özellik `jsonNodeReference`kullanarak tek bir diziyi genişletmeyi seçebilirsiniz.

### <a name="json-file-patterns"></a>JSON dosyası desenleri

Kopyalama etkinliği, JSON dosyalarının şu desenlerini ayrıştırabilir:

- **1. Tür: setOfObjects**

    Her dosya tek bir nesne veya satırlara ayrılmış/bitiştirilmiş birden fazla nesne içerir. Bu seçenek bir çıkış veri kümesinde belirlendiğinde, kopyalama etkinliği her satırda bir nesnenin bulunduğu (satırlara ayrılmış) tek bir JSON dosyası üretir.

    * **tek nesne JSON örneği**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **satırlara ayrılmış JSON örneği**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **bitiştirilmiş JSON örneği**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **2. Tür: arrayOfObjects**

    Her dosya bir nesne dizisi içerir.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

### <a name="jsonformat-example"></a>JsonFormat örneği

**Örnek Durum 1: JSON dosyalarından veri kopyalama**

**Örnek 1: nesne ve diziden veri ayıklama**

Bu örnekte, bir kök JSON nesnesinin tablosal sonuçtaki tek bir kayıtla eşleşmesi beklenir. Aşağıdaki içeriğe sahip bir JSON dosyanız varsa:

```json
{
    "id": "ed0e4960-d9c5-11e6-85dc-d7996816aad3",
    "context": {
        "device": {
            "type": "PC"
        },
        "custom": {
            "dimensions": [
                {
                    "TargetResourceType": "Microsoft.Compute/virtualMachines"
                },
                {
                    "ResourceManagementProcessRunId": "827f8aaa-ab72-437c-ba48-d8917a7336a3"
                },
                {
                    "OccurrenceTime": "1/13/2017 11:24:37 AM"
                }
            ]
        }
    }
}
```

ve hem nesne hem de diziden veri ayıklayarak bir Azure SQL tablosuna aşağıdaki biçimde kopyalamak istersiniz:

| Kimlik | deviceType | targetResourceType | resourceManagementProcessRunId | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | PC | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 1/13/2017 11:24:37 AM |

**JsonFormat** türüne sahip giriş veri kümesi şu şekilde tanımlanır: (yalnızca ilgili bölümlerin gösterildiği kısmi tanım). Daha ayrıntılı belirtmek gerekirse:

- `structure` bölümü, tablo verilerine dönüştürme sırasında kullanılan özelleştirilmiş sütun adlarını ve karşılık gelen veri türünü tanımlar. Bu bölüm **isteğe bağlıdır** ve yalnızca sütun eşleme için kullanmanız gerekir. Daha fazla bilgi için [hedef dataset sütunları için kaynak veri kümesi sütunlarını eşleme](copy-activity-schema-and-type-mapping.md).
- `jsonPathDefinition`, her sütun için verilerin ayıklanacağı JSON yolunu belirtir. Verileri diziden kopyalamak için kullanabilirsiniz `array[x].property` belirtilen özelliğin değerini ayıklamak için `xth` nesne veya kullanabileceğiniz `array[*].property` özelliği içeren herhangi bir nesneden değeri bulunacak.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "deviceType",
            "type": "String"
        },
        {
            "name": "targetResourceType",
            "type": "String"
        },
        {
            "name": "resourceManagementProcessRunId",
            "type": "String"
        },
        {
            "name": "occurrenceTime",
            "type": "DateTime"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonPathDefinition": {"id": "$.id", "deviceType": "$.context.device.type", "targetResourceType": "$.context.custom.dimensions[0].TargetResourceType", "resourceManagementProcessRunId": "$.context.custom.dimensions[1].ResourceManagementProcessRunId", "occurrenceTime": " $.context.custom.dimensions[2].OccurrenceTime"}
        }
    }
}
```

**Örnek 2: diziden aynı desene sahip birden fazla nesneyi çapraz uygulama**

Bu örnekte, bir kök JSON nesnesinin tablosal sonuçtaki birden fazla kayda dönüştürülmesi beklenir. Aşağıdaki içeriğe sahip bir JSON dosyanız varsa:

```json
{
    "ordernumber": "01",
    "orderdate": "20170122",
    "orderlines": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "sanmateo": "No 1" } ]
}
```

ve bunu bir Azure SQL tablosuna aşağıdaki biçimde, dizi içindeki verileri düzleştirerek ve ortak kök bilgileriyle çapraz birleşim yaparak kopyalamak istiyorsanız:

| `ordernumber` | `orderdate` | `order_pd` | `order_price` | `city` |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P2 | 13 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P3 | 231 | `[{"sanmateo":"No 1"}]` |


**JsonFormat** türüne sahip giriş veri kümesi şu şekilde tanımlanır: (yalnızca ilgili bölümlerin gösterildiği kısmi tanım). Daha ayrıntılı belirtmek gerekirse:

- `structure` bölümü, tablo verilerine dönüştürme sırasında kullanılan özelleştirilmiş sütun adlarını ve karşılık gelen veri türünü tanımlar. Bu bölüm **isteğe bağlıdır** ve yalnızca sütun eşleme için kullanmanız gerekir. Daha fazla bilgi için [hedef dataset sütunları için kaynak veri kümesi sütunlarını eşleme](copy-activity-schema-and-type-mapping.md).
- `jsonNodeReference` yineleme ve altında aynı desene sahip nesnelerdeki verilerin ayıklamak için gösterir **dizi** `orderlines`.
- `jsonPathDefinition`, her sütun için verilerin ayıklanacağı JSON yolunu belirtir. Bu örnekte, `ordernumber`, `orderdate`, ve `city` JSON yolu başlayarak ile kök nesne altındaki `$.`, ancak `order_pd` ve `order_price` dizi öğesinden türetilen yol ile tanımlanan `$.` .

```json
"properties": {
    "structure": [
        {
            "name": "ordernumber",
            "type": "String"
        },
        {
            "name": "orderdate",
            "type": "String"
        },
        {
            "name": "order_pd",
            "type": "String"
        },
        {
            "name": "order_price",
            "type": "Int64"
        },
        {
            "name": "city",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonNodeReference": "$.orderlines",
            "jsonPathDefinition": {"ordernumber": "$.ordernumber", "orderdate": "$.orderdate", "order_pd": "prod", "order_price": "price", "city": " $.city"}
        }
    }
}
```

**Aşağıdaki noktalara dikkat edin:**

* `structure` ve `jsonPathDefinition`, Data Factory veri kümesinde tanımlanmamışsa, Copy Activity şemayı ilk nesneden algılar ve nesnenin tamamını düzleştirir.
* JSON girişi bir diziye sahipse, Copy Activity dizi değerinin tamamını varsayılan olarak bir dizeye dönüştürür. Verileri `jsonNodeReference` ve/veya `jsonPathDefinition` kullanarak ayıklayabilir ya da `jsonPathDefinition` içinde belirtmeden atlayabilirsiniz.
* Aynı düzeyde birden fazla ad varsa Copy Activity sonuncusunu alır.
* Özellik adları büyük/küçük harfe duyarlıdır. Aynı ada ancak farklı büyük/küçük harf düzenine sahip iki özellik, iki ayrı özellik olarak kabul edilir.

**Durum 2: JSON dosyasına veri yazma**

Aşağıdaki tabloda, SQL veritabanı'nda varsa:

| Kimlik | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | Patrick |
| 3 | 20170121 | 4000 | Jason |

ve aşağıdaki biçimde bir JSON nesnesi yazmak amacıyla beklediğiniz her kayıt için:

```json
{
    "id": "1",
    "order": {
        "date": "20170119",
        "price": 2000,
        "customer": "David"
    }
}
```

**JsonFormat** türüne sahip çıkış veri kümesi şu şekilde tanımlanır: (yalnızca ilgili bölümlerin gösterildiği kısmi tanım). Daha açık belirtmek gerekirse `structure` bölümü, hedef dosyadaki özelleştirilmiş örnek adlarını tanımlar `nestingSeparator` (varsayılan değer ".") iç içe katmanını tanımlamak için kullanılır. Bu bölüm **isteğe bağlıdır** ve kaynak sütunu adıyla karşılaştırarak özellik adını değiştirmek veya özelliklerin bazılarını iç içe yerleştirmek için kullanmanız gerekir.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "order.date",
            "type": "String"
        },
        {
            "name": "order.price",
            "type": "Int64"
        },
        {
            "name": "order.customer",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat"
        }
    }
}
```

## <a name="parquet-format"></a>Parquet biçimi

>[!NOTE]
>Data Factory yeni Parquet biçimi veri kümesi sunuldu, ayrıntılar içeren [Parquet biçimi](format-parquet.md) makalesine bakın. Dosya tabanlı veri deposu veri kümesi için aşağıdaki yapılandırma, geriye doğru uyumluluk için olduğu gibi hala desteklenmektedir. İleri doğru olan yeni modeli kullanmanız önerilir.

Parquet dosyalarını ayrıştırmak veya verileri Parquet biçiminde yazmak istiyorsanız `format` `type` özelliğini **ParquetFormat** olarak ayarlayın. typeProperties bölümünün içindeki Format bölümünde herhangi bir özellik belirtmenize gerek yoktur. Örnek:

```json
"format":
{
    "type": "ParquetFormat"
}
```

Aşağıdaki noktalara dikkat edin:

* Karmaşık veri türleri desteklenmez (eşleme, LISTE).
* Sütun adında boşluk desteklenmiyor.
* Parquet dosyası sıkıştırmayla ilgili şu seçeneklere sahiptir: NONE, SNAPPY, GZIP ve LZO. Data Factory, LZO dışında bu sıkıştırılmış biçimlerin hiçbirinde Parquet dosyasından veri okumayı destekler-verileri okumak için Meta verilerdeki sıkıştırma codec 'ini kullanır. Ancak Data Factory bir Parquet dosyasına yazarken varsayılan Parquet biçimi SNAPPY seçeneğini kullanır. Şu anda bu davranışı geçersiz kılma seçeneği yoktur.

> [!IMPORTANT]
> Şirket içinde barındırılan Integration Runtime, örneğin şirket içi ve bulut veri depoları arasında eşit olarak kopyalanmıyorsa, Parquet dosyalarını **olduğu gibi**KOPYALAMADıYSANıZ, ır makinenize **64 bit JRE 8 (Java Runtime Environment) veya OpenJDK** yüklemeniz gerekir. Daha ayrıntılı bilgi için aşağıdaki paragrafa bakın.

Iquet dosya serileştirme/seri hale getirme ile şirket içinde barındırılan IR üzerinde çalışan kopyalama için, ADF, bir JRE için kayıt defteri *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* denetleyerek, OpenJDK için sistem değişkeni *`JAVA_HOME`* kontrol ederek Java çalışma zamanını bulur.

- **JRE 'yi kullanmak için**: 64 bit ır, 64 BIT JRE gerektirir. [Buradan](https://go.microsoft.com/fwlink/?LinkId=808605)bulabilirsiniz.
- **OpenJDK 'yi kullanmak için**: ır sürüm 3,13 ' den itibaren desteklenmektedir. JVM. dll dosyasını, OpenJDK 'nin diğer tüm gerekli Derlemeleriyle şirket içinde barındırılan IR makinesine paketleyin ve sistem ortam değişkeni JAVA_HOME uygun şekilde ayarlayın.

>[!TIP]
>Şirket içinde barındırılan Integration Runtime kullanarak Parquet biçimine/kaynağından veri kopyalarsanız ve "Java çağrılırken bir hata oluştu, ileti: **Java. lang. OutOfMemoryError: Java yığın alanı**" hata iletisini alırsanız, bir ortam değişkeni için `_JAVA_OPTIONS` Bu kopyayı güçlendiren JVM için Min/Max yığın boyutunu ayarlamak üzere şirket içinde barındırılan IR 'yi barındıran makine işlem hattını yeniden çalıştırın.

![Şirket içinde barındırılan IR 'de JVM yığın boyutunu ayarlama](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Örnek: değişken `_JAVA_OPTIONS` `-Xms256m -Xmx16g`değer olarak ayarlayın. Bayrak `Xms`, bir Java Sanal Makinesi (JVM) için ilk bellek ayırma havuzunu belirtir, ancak `Xmx` en yüksek bellek ayırma havuzunu belirtir. Bu, JVM 'nin `Xms` bellek miktarıyla başlatıldığı ve en fazla `Xmx` bellek miktarını kullanabileceği anlamına gelir. ADF, varsayılan olarak en az 64MB ve en fazla 1G kullanır.

### <a name="data-type-mapping-for-parquet-files"></a>Eşleme Parquet dosyalarını için veri türü

| Veri Fabrikası geçici veri türü | Parquet ilkel türü | Parquet özgün türü (seri durumdan) | Parquet özgün türü (seri hale getirmek) |
|:--- |:--- |:--- |:--- |
| Boole | Boole | Yok | Yok |
| SByte | Int32 | Int8 | Int8 |
| Byte | Int32 | UInt8 | Int16 |
| Int16 | Int32 | Int16 | Int16 |
| UInt16 | Int32 | UInt16 | Int32 |
| Int32 | Int32 | Int32 | Int32 |
| UInt32 | Int64 | UInt32 | Int64 |
| Int64 | Int64 | Int64 | Int64 |
| UInt64 | Int64/ikili | UInt64 | Decimal |
| Tek | Kayan | Yok | Yok |
| çift | çift | Yok | Yok |
| Decimal | İkili | Decimal | Decimal |
| Dize | İkili | Utf8 | Utf8 |
| DateTime | Int96 | Yok | Yok |
| TimeSpan | Int96 | Yok | Yok |
| DateTimeOffset | Int96 | Yok | Yok |
| ByteArray | İkili | Yok | Yok |
| Guid | İkili | Utf8 | Utf8 |
| char | İkili | Utf8 | Utf8 |
| CharArray | Desteklenmiyor | Yok | Yok |

## <a name="orc-format"></a>ORC biçimi

>[!NOTE]
>Data Factory yeni ORC biçimi veri kümesi, ayrıntılar içeren [orc biçimi](format-orc.md) makalesine bakın. Dosya tabanlı veri deposu veri kümesi için aşağıdaki yapılandırma, geriye doğru uyumluluk için olduğu gibi hala desteklenmektedir. İleri doğru olan yeni modeli kullanmanız önerilir.

ORC dosyalarını ayrıştırmak veya verileri ORC biçiminde yazmak istiyorsanız `format` `type` özelliğini **OrcFormat** olarak ayarlayın. typeProperties bölümünün içindeki Format bölümünde herhangi bir özellik belirtmenize gerek yoktur. Örnek:

```json
"format":
{
    "type": "OrcFormat"
}
```

Aşağıdaki noktalara dikkat edin:

* Karmaşık veri türleri desteklenmez (STRUCT, MAP, LIST, UNION).
* Sütun adında boşluk desteklenmiyor.
* ORC dosyası [sıkıştırmayla ilgili üç seçeneğe sahiptir](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB, SNAPPY. Data Factory, bu sıkıştırma biçimlerinin herhangi birine sahip ORC dosyalarını okuyabilir. Verileri okumak için meta verilerdeki sıkıştırma kodlayıcısı/kod çözücüsünü kullanır. Ancak Data Factory bir ORC dosyasına yazarken varsayılan ORC değeri olan ZLIB seçeneğini kullanır. Şu anda bu davranışı geçersiz kılma seçeneği yoktur.

> [!IMPORTANT]
> Şirket içinde barındırılan Integration Runtime, örneğin şirket içi ve bulut veri depoları arasında geçiş için, ORC dosyalarını **olduğu gibi**KOPYALAMADıYSANıZ, ır makinenize **64 bit JRE 8 (Java Runtime Environment) veya OpenJDK** yüklemeniz gerekir. Daha ayrıntılı bilgi için aşağıdaki paragrafa bakın.

ORC dosya serileştirme/seri hale getirme ile şirket içinde barındırılan IR üzerinde çalışan kopyalama için, ADF, bir JRE için kayıt defteri *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* denetleyerek, OpenJDK için sistem değişkeni *`JAVA_HOME`* kontrol ederek Java çalışma zamanını bulur.

- **JRE 'yi kullanmak için**: 64 bit ır, 64 BIT JRE gerektirir. [Buradan](https://go.microsoft.com/fwlink/?LinkId=808605)bulabilirsiniz.
- **OpenJDK 'yi kullanmak için**: ır sürüm 3,13 ' den itibaren desteklenmektedir. JVM. dll dosyasını, OpenJDK 'nin diğer tüm gerekli Derlemeleriyle şirket içinde barındırılan IR makinesine paketleyin ve sistem ortam değişkeni JAVA_HOME uygun şekilde ayarlayın.

### <a name="data-type-mapping-for-orc-files"></a>Eşleme ORC dosyaları için veri türü

| Veri Fabrikası geçici veri türü | ORC türleri |
|:--- |:--- |
| Boole | Boole |
| SByte | Byte |
| Byte | Kısa |
| Int16 | Kısa |
| UInt16 | Int |
| Int32 | Int |
| UInt32 | Uzun |
| Int64 | Uzun |
| UInt64 | Dize |
| Tek | Kayan |
| çift | çift |
| Decimal | Decimal |
| Dize | Dize |
| DateTime | Zaman damgası |
| DateTimeOffset | Zaman damgası |
| TimeSpan | Zaman damgası |
| ByteArray | İkili |
| Guid | Dize |
| char | CHAR(1) |

## <a name="avro-format"></a>AVRO biçimi

>[!NOTE]
>Yeni avro Format veri kümesi Data Factory tanıtılan Ayrıntılar için bkz. [Avrı biçimi](format-avro.md) makalesi. Dosya tabanlı veri deposu veri kümesi için aşağıdaki yapılandırma, geriye doğru uyumluluk için olduğu gibi hala desteklenmektedir. İleri doğru olan yeni modeli kullanmanız önerilir.

Avro dosyalarını ayrıştırmak veya verileri Avro biçiminde yazmak istiyorsanız `format` `type` özelliğini **AvroFormat** olarak ayarlayın. typeProperties bölümünün içindeki Format bölümünde herhangi bir özellik belirtmenize gerek yoktur. Örnek:

```json
"format":
{
    "type": "AvroFormat",
}
```

Avro biçimini bir Hive tablosunda kullanmak için [Apache Hive öğreticisini](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe) inceleyebilirsiniz.

Aşağıdaki noktalara dikkat edin:

* [Karmaşık veri türlerini](https://avro.apache.org/docs/current/spec.html#schema_complex) desteklenmez (kayıtlar, Enum'lar, diziler, haritalar, birleşimler ve sabit).

## <a name="binary-format"></a>İkili biçim

Ayrıntılar hakkında [ikili biçim](format-binary.md) makalesine başvurun.

## <a name="compression-support"></a>Sıkıştırma desteği

Azure Data Factory kopyalama sırasında veri sıkıştırma ve sıkıştırmasını destekler. Belirttiğinizde `compression` özelliğinde, girdi veri kümesi, kopyalama etkinliği sıkıştırılmış veri kaynağından okumak ve; genişletmek ve bir çıkış veri kümesinde özelliğini belirttiğinizde, kopyalama etkinliği Sıkıştır sonra havuz veri yazma. Bazı örnek senaryoları şunlardır:

* Okuma GZIP sıkıştırılmış verileri Azure blobundan iptal ve sonuç verilerini Azure SQL veritabanına yazma. Giriş Azure Blob veri kümesi ile tanımladığınız `compression` `type` GZIP olarak özelliği.
* Verileri şirket içi dosya sistemi düz metin dosyasından okuma, GZip biçimi kullanarak sıkıştırma ve sıkıştırılmış verileri bir Azure blobuna yazmak. Bir çıkış Azure Blob veri kümesi ile tanımladığınız `compression` `type` GZip olarak özelliği.
* FTP sunucusundan okuma .zip dosyasını içindeki dosyaları almak ve bu dosyaları Azure Data Lake Store içinde kavuşmak için açın. Bir giriş FTP veri kümesi ile tanımladığınız `compression` `type` ZipDeflate olarak özelliği.
* Azure blobundan GZIP sıkıştırılmış veri okuma, iptal, bzıp2 kullanarak sıkıştırma ve sonuç verilerini Azure blobuna yazma. Giriş Azure Blob veri kümesi ile tanımladığınız `compression` `type` GZIP ve çıktı veri kümesi ile ayarlanan `compression` `type` bzıp2 için ayarlayın.

Bir veri kümesi sıkıştırma belirtmek için kullanın **sıkıştırma** aşağıdaki örnekte olduğu gibi veri kümesi JSON özelliğinde:

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "fileName": "pagecounts.csv.gz",
            "folderPath": "compression/file/",
            "format": {
                "type": "TextFormat"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

**Sıkıştırma** bölümü iki özelliğe sahiptir:

* **Türü:** olabilir sıkıştırma codec **GZIP**, **Deflate**, **bzıp2**, veya **ZipDeflate**.
* **Düzeyi:** olabilir sıkıştırma oranı **Optimal** veya **en hızlı**.

  * **Hızlı:** elde edilen dosyanın en uygun şekilde sıkıştırılmaz bile sıkıştırma işlemi, mümkün olan en kısa sürede tamamlamanız gerekir.
  * **En iyi**: bile işlemin tamamlanması çok uzun sürüyor sıkıştırma işlemi en uygun şekilde, sıkıştırılmış olması gerekir.

    Daha fazla bilgi için [sıkıştırma düzeyi](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) konu.

> [!NOTE]
> Veri sıkıştırma ayarları desteklenmez **AvroFormat**, **OrcFormat**, veya **ParquetFormat**. Bu biçimler dosyalarında okurken, Data Factory algılar ve sıkıştırma codec meta verilerde kullanır. Bu biçimler dosyalarında yazarken, Data Factory bu biçimi için varsayılan sıkıştırma codec seçer. Örneğin, ZLIB OrcFormat ve ParquetFormat için SNAPPY.

## <a name="unsupported-file-types-and-compression-formats"></a>Desteklenmeyen dosya türleri ve sıkıştırma biçimleri

Desteklenmeyen dosyaları dönüştürmek için Azure Data Factory genişletilebilirlik özelliklerini kullanabilirsiniz.
İki seçenek Azure Batch kullanarak Azure Işlevlerini ve özel görevleri içerir.

Bir [tar dosyasının içeriğini ayıklamak](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction)Için bir Azure işlevi kullanan bir örnek görebilirsiniz. Daha fazla bilgi için bkz. [Azure işlevleri etkinliği](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity).

Özel bir DotNet etkinliği kullanarak bu işlevi de oluşturabilirsiniz. [Burada](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity) daha fazla bilgi bulabilirsiniz

## <a name="next-steps"></a>Sonraki adımlar

Azure Data Factory tarafından desteklenen dosya tabanlı veri depoları için aşağıdaki makalelere bakın:

- [Azure Blob Depolama Bağlayıcısı](connector-azure-blob-storage.md)
- [Azure Data Lake Store Bağlayıcısı](connector-azure-data-lake-store.md)
- [Amazon S3 Bağlayıcısı](connector-amazon-simple-storage-service.md)
- [Dosya sistemi Bağlayıcısı](connector-file-system.md)
- [FTP Bağlayıcısı](connector-ftp.md)
- [SFTP Bağlayıcısı](connector-sftp.md)
- [HDFS Bağlayıcısı](connector-hdfs.md)
- [HTTP Bağlayıcısı](connector-http.md)
