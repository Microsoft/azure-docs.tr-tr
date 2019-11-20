---
title: Azure Data Factory dosya ve sıkıştırma biçimleri
description: Azure Data Factory tarafından desteklenen dosya biçimleri hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 82d69c739e56a344036e8b91cacdd3e955a4c1d6
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73665870"
---
# <a name="file-and-compression-formats-supported-by-azure-data-factory"></a>Azure Data Factory tarafından desteklenen dosya ve sıkıştırma biçimleri
*Bu konu şu bağlayıcılar için geçerlidir: [Amazon S3](data-factory-amazon-simple-storage-service-connector.md), [Azure Blob](data-factory-azure-blob-connector.md), [Azure Data Lake Store](data-factory-azure-datalake-connector.md), [dosya sistemi](data-factory-onprem-file-system-connector.md), [FTP](data-factory-ftp-connector.md),, [](data-factory-hdfs-connector.md) [http](data-factory-http-connector.md)ve [SFTP](data-factory-sftp-connector.md).*

> [!NOTE]
> Bu makale, Azure Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, [Data Factory içindeki desteklenen dosya biçimleri ve sıkıştırma codec bileşenleri](../supported-file-formats-and-compression-codecs.md)' ne bakın.

Azure Data Factory aşağıdaki dosya biçimi türlerini destekler:

* [Metin biçimi](#text-format)
* [JSON biçimi](#json-format)
* [Avro biçimi](#avro-format)
* [ORC biçimi](#orc-format)
* [Parquet biçimi](#parquet-format)

## <a name="text-format"></a>Metin biçimi
Bir metin dosyasından okumak veya bir metin dosyasına yazmak isterseniz, veri kümesinin `format` bölümündeki `type` özelliğini **TextFormat**olarak ayarlayın. İsterseniz **bölümünde aşağıdaki**isteğe bağlı`format` özellikleri de belirtebilirsiniz. Yapılandırma adımları için [TextFormat örneği](#textformat-example) bölümünü inceleyin.

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| columnDelimiter |Bir dosyadaki sütunları ayırmak için kullanılan karakterdir. Verilerinizde olası olabilecek seyrek yazdırılabilir bir karakter kullanmayı düşünebilirsiniz. Örneğin, başlık başlangıcını (SOH) temsil eden "\u0001" öğesini belirtin. |Yalnızca bir karaktere izin verilir. **Varsayılan** değer **virgül (",")** olarak belirlenmiştir. <br/><br/>Unicode karakter kullanmak için karşılık gelen kodu almak üzere [Unicode karakterlerine](https://en.wikipedia.org/wiki/List_of_Unicode_characters) bakın. |Hayır |
| rowDelimiter |Bir dosyadaki satırları ayırmak için kullanılan karakterdir. |Yalnızca bir karaktere izin verilir. **Varsayılan** değer, okuma sırasında **["\r\n", "\r", "\n"]** değerlerinden biri, yazma sırasında ise **"\r\n"** olarak belirlenmiştir. |Hayır |
| escapeChar |Giriş dosyasının içeriğindeki bir sütun ayırıcısına kaçış karakteri eklemek için kullanılan özel karakterdir. <br/><br/>Bir tablo için hem escapeChar hem de quoteChar parametrelerini aynı anda belirtemezsiniz. |Yalnızca bir karaktere izin verilir. Varsayılan değer yoktur. <br/><br/>Örnek: Sütun sınırlayıcınız virgül (",") karakteriyse ancak metin içinde virgül karakteri kullanılıyorsa (örneğin: "Merhaba, dünya"), "$" karakterini kaçış karakteri olarak tanımlayabilir ve kaynakta "Merhaba$, dünya" dizesini kullanabilirsiniz. |Hayır |
| quoteChar |Bir dize değerini tırnak içine almak için kullanılan karakterdir. Tırnak işareti içindeki sütun ve satır sınırlayıcıları, dize değerinin bir parçası olarak kabul edilir. Bu özellik hem giriş hem de çıkış veri kümelerine uygulanabilir.<br/><br/>Bir tablo için hem escapeChar hem de quoteChar parametrelerini aynı anda belirtemezsiniz. |Yalnızca bir karaktere izin verilir. Varsayılan değer yoktur. <br/><br/>Örneğin, sütun sınırlayıcınız virgül (",") karakteriyse ancak metin içinde virgül karakteri kullanılıyorsa (örneğin: <Merhaba, dünya>), " (çift tırnak) karakterini tırnak karakteri olarak tanımlayabilir ve kaynakta "Merhaba, dünya" dizesini kullanabilirsiniz. |Hayır |
| nullValue |Bir null değeri temsil etmek için kullanılan bir veya daha fazla karakterdir. |Bir veya daha fazla karakter olabilir. **Varsayılan** değerler okuma sırasında **"\N" ve "NULL"** , yazma sırasında ise **"\N"** olarak belirlenmiştir. |Hayır |
| encodingName |Kodlama adını belirtir. |Geçerli bir kodlama adı. Bkz. [Encoding.EncodingName Özelliği](https://msdn.microsoft.com/library/system.text.encoding.aspx). Örnek: windows-1250 veya shift_jis. **Varsayılan** değer **UTF-8** olarak belirlenmiştir. |Hayır |
| firstRowAsHeader |İlk satırın üst bilgi olarak kabul edilip edilmeyeceğini belirtir. Giriş veri kümesinde Data Factory ilk satırı üst bilgi olarak okur. Çıkış veri kümesinde Data Factory ilk satırı üst bilgi olarak yazar. <br/><br/>Örnek senaryolar için bkz. [`firstRowAsHeader` ve `skipLineCount` kullanım senaryoları](#scenarios-for-using-firstrowasheader-and-skiplinecount). |True<br/><b>False (varsayılan)</b> |Hayır |
| skipLineCount |Giriş dosyalarından okuma sırasında atlanacak satır sayısını belirtir. Hem skipLineCount hem de firstRowAsHeader parametresi belirtilirse önce satırlar atlanır, ardından giriş dosyasındaki üst bilgi bilgileri okunur. <br/><br/>Örnek senaryolar için bkz. [`firstRowAsHeader` ve `skipLineCount` kullanım senaryoları](#scenarios-for-using-firstrowasheader-and-skiplinecount). |Tamsayı |Hayır |
| treatEmptyAsNull |Bir giriş dosyasından veri okuma sırasında null veya boş dizenin null değer olarak kabul edilip edilmeyeceğini belirtir. |**True (varsayılan)**<br/>False |Hayır |

### <a name="textformat-example"></a>TextFormat örneği
Bir veri kümesi için aşağıdaki JSON tanımında, isteğe bağlı özelliklerden bazıları belirtilmiştir.

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
* Bir metin dosyasından kopyalama yapıyorsunuz ve dosyanın başındaki veri içermeyen veya üst bilgi bilgilerini içeren birkaç satırı atlamak istiyorsunuz. Atlanacak satır sayısını belirtmek için `skipLineCount` değerini belirtin. Dosyanın geri kalan kısmında başlık satırı varsa `firstRowAsHeader` değerini de belirtebilirsiniz. Hem `skipLineCount` hem de `firstRowAsHeader` parametresi belirtilirse önce satırlar atlanır, ardından giriş dosyasındaki üst bilgi bilgileri okunur.

## <a name="json-format"></a>JSON biçimi
**Json dosyasını Azure Cosmos DB olarak içeri/** dışarı aktarmak için, [Azure Cosmos DB verileri taşıma](data-factory-azure-documentdb-connector.md) /içeri aktarma konusunun [JSON belgelerini içeri/dışarı aktarma](data-factory-azure-documentdb-connector.md#importexport-json-documents) bölümüne bakın.

JSON dosyalarını ayrıştırmak veya verileri JSON biçiminde yazmak isterseniz, `format` bölümündeki `type` özelliğini **Jsonformat**olarak ayarlayın. İsterseniz **bölümünde aşağıdaki**isteğe bağlı`format` özellikleri de belirtebilirsiniz. Yapılandırma adımları için [JsonFormat örneği](#jsonformat-example) bölümünü inceleyin.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| filePattern |Her bir JSON dosyasında depolanan verilerin desenini belirtir. İzin verilen değerler: **setOfObjects** ve **arrayOfObjects**. **Varsayılan** değer **setOfObjects** olarak belirlenmiştir. Bu desenler hakkında ayrıntılı bilgi için bkz. [JSON dosyası desenleri](#json-file-patterns). |Hayır |
| jsonNodeReference | Bir dizi alanındaki aynı desene sahip verileri yinelemek ve ayıklamak istiyorsanız o dizinin JSON yolunu belirtin. Bu özellik yalnızca JSON dosyalarından veri kopyalarken desteklenir. | Hayır |
| jsonPathDefinition | Her sütun için JSON yolu ifadesini belirtin ve özel bir sütun adıyla eşleyin (küçük harfle başlatın). Bu özellik yalnızca JSON dosyalarından veri kopyalarken desteklenir. Verileri nesne veya diziden ayıklayabilirsiniz. <br/><br/> Kök nesne altındaki alanlar için root $ ile, `jsonNodeReference` özelliği tarafından seçilen dizinin içindeki alanlar için ise dizi öğesiyle başlayın. Yapılandırma adımları için [JsonFormat örneği](#jsonformat-example) bölümünü inceleyin. | Hayır |
| encodingName |Kodlama adını belirtir. Geçerli kodlama adlarının listesi için bkz. [Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx) Özelliği. Örneğin: windows-1250 veya shift_jis. **Varsayılan** değer **UTF-8** olarak belirlenmiştir. |Hayır |
| nestingSeparator |İç içe geçme düzeylerini ayırmak için kullanılan karakterdir. Varsayılan değer "." (nokta) olarak belirlenmiştir. |Hayır |

### <a name="json-file-patterns"></a>JSON dosyası desenleri

Kopyalama etkinliği aşağıdaki JSON dosyası desenlerini ayrıştırılabilir:

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

JSON dosyalarından veri kopyalarken aşağıdaki iki örneği inceleyin. Genel olarak şunlara işaret eder:

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

| id | deviceType | targetResourceType | Resourcemanagementprocessrunıd | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | PC | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 1/13/2017 11:24:37 AM |

**JsonFormat** türüne sahip giriş veri kümesi şu şekilde tanımlanır: (yalnızca ilgili bölümlerin gösterildiği kısmi tanım). Daha ayrıntılı belirtmek gerekirse:

- `structure` bölümü, tablo verilerine dönüştürme sırasında kullanılan özelleştirilmiş sütun adlarını ve karşılık gelen veri türünü tanımlar. Bu bölüm **isteğe bağlıdır** ve yalnızca sütun eşleme için kullanmanız gerekir. Daha fazla bilgi için bkz. [kaynak veri kümesi sütunlarını hedef veri kümesi sütunları bölümüne eşleme](data-factory-map-columns.md) .
- `jsonPathDefinition`, her sütun için verilerin ayıklanacağı JSON yolunu belirtir. Diziden veri kopyalamak için **Array [x]. Property** ' yi kullanarak verilen özelliğin değerini XTH nesnesinden çıkarabilir veya bu tür özelliği içeren herhangi bir nesneden değeri bulmak için **Array [*]. Property** ' yi kullanabilirsiniz.

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

| ordernumber | orderdate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | [{"sanmateo":"No 1"}] |
| 01 | 20170122 | P2 | 13 | [{"sanmateo":"No 1"}] |
| 01 | 20170122 | P3 | 231 | [{"sanmateo":"No 1"}] |

**JsonFormat** türüne sahip giriş veri kümesi şu şekilde tanımlanır: (yalnızca ilgili bölümlerin gösterildiği kısmi tanım). Daha ayrıntılı belirtmek gerekirse:

- `structure` bölümü, tablo verilerine dönüştürme sırasında kullanılan özelleştirilmiş sütun adlarını ve karşılık gelen veri türünü tanımlar. Bu bölüm **isteğe bağlıdır** ve yalnızca sütun eşleme için kullanmanız gerekir. Daha fazla bilgi için bkz. [kaynak veri kümesi sütunlarını hedef veri kümesi sütunları bölümüne eşleme](data-factory-map-columns.md) .
- `jsonNodeReference`, **dizi** sipariş satırlarının altında aynı desene sahip nesnelerdeki verilerin yineleneceğini ve ayıklanacağını belirtir.
- `jsonPathDefinition`, her sütun için verilerin ayıklanacağı JSON yolunu belirtir. Bu örnekte "ordernumber", "orderdate" ve "city", kök nesnenin altında ve "$." ile başlayan JSON yolundayken, "order_pd" ve "order_price", "$." olmadan dizi öğesinden türetilen yol kullanılarak tanımlanmıştır.

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

SQL veritabanında aşağıdaki tablo varsa:

| id | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | Patrick |
| 3 | 20170121 | 4000 | Jason |

Her kayıt için aşağıdaki biçimde bir JSON nesnesine yazmayı beklemeniz gerekir:
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

**JsonFormat** türüne sahip çıkış veri kümesi şu şekilde tanımlanır: (yalnızca ilgili bölümlerin gösterildiği kısmi tanım). Daha belirgin bir şekilde, `structure` bölüm, hedef dosyadaki özelleştirilmiş özellik adlarını tanımlar, `nestingSeparator` (varsayılan "."), iç içe geçme katmanını adından belirlemek için kullanılır. Bu bölüm **isteğe bağlıdır** ve kaynak sütunu adıyla karşılaştırarak özellik adını değiştirmek veya özelliklerin bazılarını iç içe yerleştirmek için kullanmanız gerekir.

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

## <a name="avro-format"></a>AVRO biçimi
Avro dosyalarını ayrıştırmak veya verileri Avro biçiminde yazmak istiyorsanız `format` `type` özelliğini **AvroFormat** olarak ayarlayın. typeProperties bölümünün içindeki Format bölümünde herhangi bir özellik belirtmenize gerek yoktur. Örnek:

```json
"format":
{
    "type": "AvroFormat",
}
```

Avro biçimini bir Hive tablosunda kullanmak için [Apache Hive öğreticisini](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe) inceleyebilirsiniz.

Aşağıdaki noktalara dikkat edin:  

* [Karmaşık veri türleri](https://avro.apache.org/docs/current/spec.html#schema_complex) desteklenmez (kayıtlar, numaralandırmalar, diziler, Haritalar, birleşimler ve sabit).

## <a name="orc-format"></a>ORC biçimi
ORC dosyalarını ayrıştırmak veya verileri ORC biçiminde yazmak istiyorsanız `format` `type` özelliğini **OrcFormat** olarak ayarlayın. typeProperties bölümünün içindeki Format bölümünde herhangi bir özellik belirtmenize gerek yoktur. Örnek:

```json
"format":
{
    "type": "OrcFormat"
}
```

> [!IMPORTANT]
> Şirket içi ve bulut veri depoları arasında ORC dosyalarını **olduğu gibi** kopyalamıyorsanız, ağ geçidi cihazınıza JRE 8 (Java Çalışma Zamanı Ortamı) yüklemeniz gerekir. 64 bit ağ geçidi için 64 bit JRE, 32 bit ağ geçidi için de 32 bit JRE gerekir. İki sürüme de [buradan](https://go.microsoft.com/fwlink/?LinkId=808605) ulaşabilirsiniz. Cihazınıza uygun olanı seçin.
>
>

Aşağıdaki noktalara dikkat edin:

* Karmaşık veri türleri desteklenmez (STRUCT, MAP, LIST, UNION)
* ORC dosyası [sıkıştırmayla ilgili üç seçeneğe sahiptir](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/): NONE, ZLIB, SNAPPY. Data Factory, bu sıkıştırma biçimlerinin herhangi birine sahip ORC dosyalarını okuyabilir. Verileri okumak için meta verilerdeki sıkıştırma kodlayıcısı/kod çözücüsünü kullanır. Ancak Data Factory bir ORC dosyasına yazarken varsayılan ORC değeri olan ZLIB seçeneğini kullanır. Şu anda bu davranışı geçersiz kılma seçeneği yoktur.

## <a name="parquet-format"></a>Parquet biçimi
Parquet dosyalarını ayrıştırmak veya verileri Parquet biçiminde yazmak istiyorsanız `format` `type` özelliğini **ParquetFormat** olarak ayarlayın. typeProperties bölümünün içindeki Format bölümünde herhangi bir özellik belirtmenize gerek yoktur. Örnek:

```json
"format":
{
    "type": "ParquetFormat"
}
```
> [!IMPORTANT]
> Şirket içi ve bulut veri depoları arasında Parquet dosyalarını **olduğu gibi** kopyalamıyorsanız, ağ geçidi cihazınıza JRE 8 (Java Çalışma Zamanı Ortamı) yüklemeniz gerekir. 64 bit ağ geçidi için 64 bit JRE, 32 bit ağ geçidi için de 32 bit JRE gerekir. İki sürüme de [buradan](https://go.microsoft.com/fwlink/?LinkId=808605) ulaşabilirsiniz. Cihazınıza uygun olanı seçin.
>
>

Aşağıdaki noktalara dikkat edin:

* Karmaşık veri türleri desteklenmez (MAP, LIST)
* Parquet dosyası sıkıştırmayla ilgili şu seçeneklere sahiptir: NONE, SNAPPY, GZIP ve LZO. Data Factory, bu sıkıştırma biçimlerinin herhangi birine sahip ORC dosyalarını okuyabilir. Verileri okumak için meta verilerdeki sıkıştırma kodlayıcısı/kod çözücüsünü kullanır. Ancak Data Factory bir Parquet dosyasına yazarken varsayılan Parquet biçimi SNAPPY seçeneğini kullanır. Şu anda bu davranışı geçersiz kılma seçeneği yoktur.

## <a name="compression-support"></a>Sıkıştırma desteği
Büyük veri kümelerini işlemek g/ç ve ağ performans sorunlarına neden olabilir. Bu nedenle, mağazalardaki sıkıştırılmış veriler yalnızca ağ genelindeki veri aktarımını hızlandırabilir ve disk alanından tasarruf edebilir, ayrıca büyük verileri işlerken önemli performans iyileştirmeleri de getirebilir. Şu anda sıkıştırma, Azure Blob veya şirket içi dosya sistemi gibi dosya tabanlı veri depoları için desteklenir.  

Bir veri kümesinin sıkıştırmasını belirtmek için, aşağıdaki örnekte gösterildiği gibi JSON veri kümesi içindeki **Compression** özelliğini kullanın:   

```json
{  
    "name": "AzureBlobDataSet",  
    "properties": {  
        "availability": {  
            "frequency": "Day",  
              "interval": 1  
        },  
        "type": "AzureBlob",  
        "linkedServiceName": "StorageLinkedService",  
        "typeProperties": {  
            "fileName": "pagecounts.csv.gz",  
            "folderPath": "compression/file/",  
            "compression": {  
                "type": "GZip",  
                "level": "Optimal"  
            }  
        }  
    }  
}  
```

Örnek veri kümesinin bir kopyalama etkinliğinin çıktısı olarak kullanıldığını varsayalım, kopyalama etkinliği en iyi oranı kullanarak GZIP codec ile çıktı verilerini sıkıştırır ve ardından sıkıştırılmış verileri Azure Blob depolamada pagecounts. csv. gz adlı bir dosyaya yazar.

> [!NOTE]
> Sıkıştırma ayarları, **Avroformat**, **orcformat**veya **parquetformat**içindeki veriler için desteklenmez. Bu biçimlerdeki dosyaları okurken Data Factory meta verilerde sıkıştırma codec 'ini algılar ve kullanır. Bu biçimlerdeki dosyalara yazarken Data Factory, bu biçimin varsayılan sıkıştırma codec 'ini seçer. Örneğin, OrcFormat için ZLIB ve ParquetFormat için SNAPPY.   

**Sıkıştırma** bölümünün iki özelliği vardır:  

* **Tür:** **GZIP**, **söndür**, **BZIP2**veya **zipsöndür**olabilen sıkıştırma codec bileşeni.  
* **Düzey:** **En Iyi** veya **en hızlı**olabilen sıkıştırma oranı.

  * **En hızlı:** Elde edilen dosya en iyi şekilde sıkıştırılmasa bile, sıkıştırma işleminin mümkün olduğunca hızlı bir şekilde tamamlanmalıdır.
  * **En iyi**: işlemin tamamlanmasını daha uzun sürse bile sıkıştırma işlemi en iyi şekilde sıkıştırılmalıdır.

    Daha fazla bilgi için bkz. [sıkıştırma düzeyi](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) konusu.

JSON giriş veri kümesinde `compression` özelliği belirttiğinizde, işlem hattı sıkıştırılmış verileri kaynaktan okuyabilir; bir çıkış veri kümesi JSON öğesinde özelliği belirttiğinizde kopyalama etkinliği, hedefe sıkıştırılmış veri yazabilir. Aşağıda birkaç örnek senaryo verilmiştir:

* Bir Azure blobundan GZIP sıkıştırılmış verileri okuyun, sıkıştırmasını açın ve sonuç verilerini bir Azure SQL veritabanına yazın. Giriş Azure blob veri kümesini, `compression` `type` JSON özelliği ile GZIP olarak tanımlarsınız.
* Şirket içi dosya sistemindeki bir düz metin dosyasından verileri okuyun, GZip biçimini kullanarak sıkıştırın ve sıkıştırılmış verileri bir Azure Blob 'una yazın. `compression` `type` JSON özelliği olan bir çıktı Azure blob veri kümesini GZip olarak tanımlarsınız.
* FTP sunucusundan. zip dosyasını okuyun, dosyaları içine almak için sıkıştırmasını açın ve bu dosyaları Azure Data Lake Store içine alın. `compression` `type` JSON özelliği olan bir giriş FTP veri kümesini Zipsöndür olarak tanımlarsınız.
* Bir Azure blobundan GZIP sıkıştırılmış verileri okuyun, açıp açın, BZIP2 kullanarak sıkıştırın ve sonuç verilerini bir Azure blobuna yazın. Giriş Azure blob veri kümesini, `compression` `type` GZIP olarak ayarlanmış ve bu durumda `compression` `type` olan çıktı veri kümesi ile tanımlarsınız.   


## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory tarafından desteklenen dosya tabanlı veri depoları için aşağıdaki makalelere bakın:

- [Azure Blob Depolama](data-factory-azure-blob-connector.md)
- [Azure Data Lake Store](data-factory-azure-datalake-connector.md)
- [FTP](data-factory-ftp-connector.md)
- [HDFS](data-factory-hdfs-connector.md)
- [Dosya Sistemi](data-factory-onprem-file-system-connector.md)
- [Amazon S3](data-factory-amazon-simple-storage-service-connector.md)
