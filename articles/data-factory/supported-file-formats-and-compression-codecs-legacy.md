---
title: Azure Data Factory 'de desteklenen dosya biçimleri (eski)
description: Bu konu, Azure Data Factory dosya tabanlı bağlayıcılar tarafından desteklenen dosya biçimlerini ve sıkıştırma kodlarını açıklamaktadır.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: d95927a9ea7d3084387a9aedb0dcdd86f84b8e7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384835"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory-legacy"></a>Azure Data Factory 'de desteklenen dosya biçimleri ve sıkıştırma codec bileşenleri (eski)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*Bu makale aşağıdaki bağlayıcılar için geçerlidir: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage 1.](connector-azure-data-lake-store.md), [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md), [Azure dosya depolama](connector-azure-file-storage.md), [dosya sistemi](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md),, [](connector-hdfs.md), [http](connector-http.md)ve [SFTP](connector-sftp.md).*

>[!IMPORTANT]
>Data Factory yeni biçim tabanlı veri kümesi modeli sunuldu, Ayrıntılar için ilgili biçim makalesine bakın: <br>- [Avro biçimi](format-avro.md)<br>- [İkili biçim](format-binary.md)<br>- [Sınırlandırılmış metin biçimi](format-delimited-text.md)<br>- [JSON biçimi](format-json.md)<br>- [ORC biçimi](format-orc.md)<br>- [Parquet biçimi](format-parquet.md)<br>Bu makalede bahsedilen Rest konfigürasyonları, geriye doğru uyumluluk için olduğu gibi hala desteklenmektedir. İleri doğru olan yeni modeli kullanmanız önerilir. 

## <a name="text-format-legacy"></a><a name="text-format"></a> Metin biçimi (eski)

>[!NOTE]
>[Sınırlandırılmış metin biçimi](format-delimited-text.md) makalesindeki yeni modeli öğrenin. Dosya tabanlı veri deposu veri kümesi için aşağıdaki yapılandırma, geriye doğru uyumluluk için olduğu gibi hala desteklenmektedir. İleri doğru olan yeni modeli kullanmanız önerilir.

Bir metin dosyasından okumak veya bir metin dosyasına yazmak isterseniz, `type` `format` veri kümesinin bölümündeki özelliğini **TextFormat** olarak ayarlayın. İsterseniz `format` bölümünde aşağıdaki **isteğe bağlı** özellikleri de belirtebilirsiniz. Yapılandırma adımları için [TextFormat örneği](#textformat-example) bölümünü inceleyin.

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| columnDelimiter |Bir dosyadaki sütunları ayırmak için kullanılan karakterdir. Verilerinizde mevcut olmayan, seyrek yazdırılabilir bir karakter kullanmayı düşünebilirsiniz. Örneğin, başlık başlangıcını (SOH) temsil eden "\u0001" öğesini belirtin. |Yalnızca bir karaktere izin verilir. **Varsayılan** değer **virgül (",")** olarak belirlenmiştir. <br/><br/>Unicode karakter kullanmak için karşılık gelen kodu almak üzere [Unicode karakterlerine](https://en.wikipedia.org/wiki/List_of_Unicode_characters) bakın. |No |
| rowDelimiter |Bir dosyadaki satırları ayırmak için kullanılan karakterdir. |Yalnızca bir karaktere izin verilir. **Varsayılan** değer, okuma sırasında **["\r\n", "\r", "\n"]** değerlerinden biri, yazma sırasında ise **"\r\n"** olarak belirlenmiştir. |No |
| escapeChar |Giriş dosyasının içeriğindeki bir sütun ayırıcısına kaçış karakteri eklemek için kullanılan özel karakterdir. <br/><br/>Bir tablo için hem escapeChar hem de quoteChar parametrelerini aynı anda belirtemezsiniz. |Yalnızca bir karaktere izin verilir. Varsayılan değer yoktur. <br/><br/>Örnek: sütun sınırlayıcısı olarak virgül (', '), ancak metinde virgül karakteri (örneğin: "Hello, World") istiyorsanız, çıkış karakteri olarak ' $ ' tanımlayabilir ve kaynakta "Hello $, World" dizesini kullanabilirsiniz. |No |
| quoteChar |Bir dize değerini tırnak içine almak için kullanılan karakterdir. Tırnak işareti içindeki sütun ve satır sınırlayıcıları, dize değerinin bir parçası olarak kabul edilir. Bu özellik hem giriş hem de çıkış veri kümelerine uygulanabilir.<br/><br/>Bir tablo için hem escapeChar hem de quoteChar parametrelerini aynı anda belirtemezsiniz. |Yalnızca bir karaktere izin verilir. Varsayılan değer yoktur. <br/><br/>Örneğin, sütun sınırlayıcınız virgül (",") karakteriyse ancak metin içinde virgül karakteri kullanılıyorsa (örneğin: <Merhaba, dünya>), " (çift tırnak) karakterini tırnak karakteri olarak tanımlayabilir ve kaynakta "Merhaba, dünya" dizesini kullanabilirsiniz. |No |
| nullValue |Bir null değeri temsil etmek için kullanılan bir veya daha fazla karakterdir. |Bir veya daha fazla karakter olabilir. **Varsayılan** değerler okuma sırasında **"\N" ve "NULL"**, yazma sırasında ise **"\N"** olarak belirlenmiştir. |No |
| encodingName |Kodlama adını belirtir. |Geçerli bir kodlama adı. Bkz. [Encoding.EncodingName Özelliği](/dotnet/api/system.text.encoding). Örnek: windows-1250 veya shift_jis. **Varsayılan** değer **UTF-8** olarak belirlenmiştir. |No |
| firstRowAsHeader |İlk satırın üst bilgi olarak kabul edilip edilmeyeceğini belirtir. Giriş veri kümesinde Data Factory ilk satırı üst bilgi olarak okur. Çıkış veri kümesinde Data Factory ilk satırı üst bilgi olarak yazar. <br/><br/>Örnek senaryolar için bkz. [`firstRowAsHeader` ve `skipLineCount` kullanım senaryoları](#scenarios-for-using-firstrowasheader-and-skiplinecount). |Doğru<br/><b>False (varsayılan)</b> |No |
| skipLineCount |Giriş dosyalarından veri okurken atlanacak **boş olmayan** satır sayısını belirtir. Hem skipLineCount hem de firstRowAsHeader parametresi belirtilirse önce satırlar atlanır, ardından giriş dosyasındaki üst bilgi bilgileri okunur. <br/><br/>Örnek senaryolar için bkz. [`firstRowAsHeader` ve `skipLineCount` kullanım senaryoları](#scenarios-for-using-firstrowasheader-and-skiplinecount). |Tamsayı |No |
| treatEmptyAsNull |Bir giriş dosyasından veri okuma sırasında null veya boş dizenin null değer olarak kabul edilip edilmeyeceğini belirtir. |**True (varsayılan)**<br/>Yanlış |No |

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

`quoteChar` yerine `escapeChar` kullanmak için `quoteChar` yazan satırı şu escapeChar ile değiştirin:

```json
"escapeChar": "$",
```

### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>firstRowAsHeader ve skipLineCount kullanım senaryoları

* Dosya olmayan bir kaynaktan bir metin dosyasına kopyalama yapıyorsunuz ve şema meta verilerini (örneğin, SQL şeması) içeren bir üst bilgi satırı eklemek istiyorsunuz. Bu senaryo için çıkış veri kümesinde `firstRowAsHeader` parametresini true olarak belirleyin.
* Üst bilgi satırı içeren bir metin dosyasından dosya olmayan bir havuza kopyalama yapıyorsunuz ve üst bilgi satırını almak istemiyorsunuz. Giriş veri kümesinde `firstRowAsHeader` parametresini true olarak belirleyin.
* Bir metin dosyasından kopyalama yapıyorsunuz ve dosyanın başındaki veri içermeyen veya üst bilgi bilgilerini içeren birkaç satırı atlamak istiyorsunuz. Atlanacak satır sayısını belirtmek için `skipLineCount` değerini belirtin. Dosyanın geri kalan kısmında üst bilgi satırı varsa `firstRowAsHeader` değerini de belirtebilirsiniz. Hem `skipLineCount` hem de `firstRowAsHeader` parametresi belirtilirse önce satırlar atlanır, ardından giriş dosyasındaki üst bilgi bilgileri okunur.

## <a name="json-format-legacy"></a><a name="json-format"></a> JSON biçimi (eski)

>[!NOTE]
>[JSON biçimi](format-json.md) makalesindeki yeni modeli öğrenin. Dosya tabanlı veri deposu veri kümesi için aşağıdaki yapılandırma, geriye doğru uyumluluk için olduğu gibi hala desteklenmektedir. İleri doğru olan yeni modeli kullanmanız önerilir.

**Json dosyasını Azure Cosmos DB olarak içeri/** dışarı aktarmak için bkz. [Azure Cosmos DB verileri](connector-azure-cosmos-db.md) içeri/dışarı aktarma makalesindeki JSON belgelerini içeri/dışarı aktarma bölümünde.

JSON dosyalarını ayrıştırmak veya verileri JSON biçiminde yazmak isterseniz, `type` `format` bölümündeki özelliğini **jsonformat** olarak ayarlayın. İsterseniz `format` bölümünde aşağıdaki **isteğe bağlı** özellikleri de belirtebilirsiniz. Yapılandırma adımları için [JsonFormat örneği](#jsonformat-example) bölümünü inceleyin.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| filePattern |Her bir JSON dosyasında depolanan verilerin desenini belirtir. İzin verilen değerler: **setOfObjects** ve **arrayOfObjects**. **Varsayılan** değer **setOfObjects** olarak belirlenmiştir. Bu desenler hakkında ayrıntılı bilgi için bkz. [JSON dosyası desenleri](#json-file-patterns). |No |
| jsonNodeReference | Bir dizi alanındaki aynı desene sahip verileri yinelemek ve ayıklamak istiyorsanız o dizinin JSON yolunu belirtin. Bu özellik **yalnızca JSON dosyalarından** veri kopyalanırken desteklenir. | No |
| jsonPathDefinition | Her sütun için JSON yolu ifadesini belirtin ve özel bir sütun adıyla eşleyin (küçük harfle başlatın). Bu özellik yalnızca JSON dosyalarından veri kopyalarken desteklenir ve nesne veya **diziden veri ayıklayabilirsiniz** . <br/><br/> Kök nesne altındaki alanlar için root $ ile, `jsonNodeReference` özelliği tarafından seçilen dizinin içindeki alanlar için ise dizi öğesiyle başlayın. Yapılandırma adımları için [JsonFormat örneği](#jsonformat-example) bölümünü inceleyin. | No |
| encodingName |Kodlama adını belirtir. Geçerli kodlama adlarının listesi için bkz. [Encoding.EncodingName](/dotnet/api/system.text.encoding) Özelliği. Örneğin: windows-1250 veya shift_jis. **Varsayılan** değer: **UTF-8**. |No |
| nestingSeparator |İç içe geçme düzeylerini ayırmak için kullanılan karakterdir. Varsayılan değer "." (nokta) olarak belirlenmiştir. |No |

>[!NOTE]
>Dizideki verileri birden çok satıra çapraz uygulama (büyük/küçük harf 1), [Jsonformat örneklerinde](#jsonformat-example)> örnek 2) için, yalnızca özelliği kullanarak tek bir diziyi genişletmeyi seçebilirsiniz `jsonNodeReference` .

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

| ID | deviceType | targetResourceType | Resourcemanagementprocessrunıd | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | PC | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 1/13/2017 11:24:37 AM |

**JsonFormat** türüne sahip giriş veri kümesi şu şekilde tanımlanır: (yalnızca ilgili bölümlerin gösterildiği kısmi tanım). Daha ayrıntılı belirtmek gerekirse:

- `structure` bölümü, tablo verilerine dönüştürme sırasında kullanılan özelleştirilmiş sütun adlarını ve karşılık gelen veri türünü tanımlar. Bu bölüm **isteğe bağlıdır** ve yalnızca sütun eşleme için kullanmanız gerekir. Daha fazla bilgi için bkz. [kaynak veri kümesi sütunlarını hedef veri kümesi sütunlarına eşleme](copy-activity-schema-and-type-mapping.md).
- `jsonPathDefinition`, her sütun için verilerin ayıklanacağı JSON yolunu belirtir. Diziden veri kopyalamak için, `array[x].property` nesnesinden verilen özelliğin değerini ayıklamak için kullanabilirsiniz `xth` veya `array[*].property` Bu özelliği içeren herhangi bir nesneden değeri bulmak için kullanabilirsiniz.

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

- `structure` bölümü, tablo verilerine dönüştürme sırasında kullanılan özelleştirilmiş sütun adlarını ve karşılık gelen veri türünü tanımlar. Bu bölüm **isteğe bağlıdır** ve yalnızca sütun eşleme için kullanmanız gerekir. Daha fazla bilgi için bkz. [kaynak veri kümesi sütunlarını hedef veri kümesi sütunlarına eşleme](copy-activity-schema-and-type-mapping.md).
- `jsonNodeReference`**dizide** bulunan aynı düzene sahip nesnelerden verileri yinelemek ve ayıklamak anlamına gelir `orderlines` .
- `jsonPathDefinition`, her sütun için verilerin ayıklanacağı JSON yolunu belirtir. Bu örnekte,, `ordernumber` `orderdate` ve `city` ile başlayan JSON yolu ile kök nesne altında `$.` , `order_pd` ve, `order_price` olmadan dizi öğesinden türetilen yol ile tanımlanmıştır `$.` .

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

| ID | order_date | order_price | order_by |
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

**JsonFormat** türüne sahip çıkış veri kümesi şu şekilde tanımlanır: (yalnızca ilgili bölümlerin gösterildiği kısmi tanım). Daha belirgin olarak, `structure` bölüm, hedef dosyadaki özelleştirilmiş özellik adlarını tanımlar, `nestingSeparator` (varsayılan "."), adından iç içe geçme katmanını belirlemek için kullanılır. Bu bölüm **isteğe bağlıdır** ve kaynak sütunu adıyla karşılaştırarak özellik adını değiştirmek veya özelliklerin bazılarını iç içe yerleştirmek için kullanmanız gerekir.

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

## <a name="parquet-format-legacy"></a><a name="parquet-format"></a> Parquet biçimi (eski)

>[!NOTE]
>Yeni modeli [Parquet biçimi](format-parquet.md) makalesinden öğrenin. Dosya tabanlı veri deposu veri kümesi için aşağıdaki yapılandırma, geriye doğru uyumluluk için olduğu gibi hala desteklenmektedir. İleri doğru olan yeni modeli kullanmanız önerilir.

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
> Şirket içinde barındırılan Integration Runtime, örneğin şirket içi ve bulut veri depoları arasında eşit olarak kopyalanmıyorsa, Parquet dosyalarını **olduğu gibi** KOPYALAMADıYSANıZ, ır makinenize **64 bit JRE 8 (Java Runtime Environment) veya OpenJDK** yüklemeniz gerekir. Daha ayrıntılı bilgi için aşağıdaki paragrafa bakın.

Iquet dosya serileştirme/seri hale getirme ile şirket içinde barındırılan IR üzerinde çalışan kopya için, ADF, bir JRE için kayıt defterini denetleyerek, *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* OpenJDK için sistem değişkenini belirterek, Java çalışma zamanını konumlandırır *`JAVA_HOME`* .

- **JRE 'yi kullanmak için**: 64 bit ır, 64 BIT JRE gerektirir. [Buradan](https://go.microsoft.com/fwlink/?LinkId=808605)bulabilirsiniz.
- **OpenJDK 'yi kullanmak için**: ır sürüm 3,13 ' den itibaren desteklenmektedir. OpenJDK 'nin diğer tüm gerekli Derlemeleriyle jvm.dll, şirket içinde barındırılan IR makinesine paketleyin ve sistem ortam değişkeni JAVA_HOME uygun şekilde ayarlayın.

>[!TIP]
>Şirket içinde barındırılan Integration Runtime kullanarak Parquet biçimine/kaynağından veri kopyalarsanız ve "Java çağrılırken bir hata oluştu" hatası ile karşılaşırsanız, ileti: **Java. lang. OutOfMemoryError: Java yığın alanı**", `_JAVA_OPTIONS` Bu kopyayı güçlendiren JVM için Min/Max yığın boyutunu ayarlamak üzere ŞIRKET içinde barındırılan IR barındıran makineye bir ortam değişkeni ekleyebilirsiniz, sonra işlem hattını yeniden çalıştırın.

![Şirket içinde barındırılan IR 'de JVM yığın boyutunu ayarlama](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Örnek: değişkeni `_JAVA_OPTIONS` değeri ile ayarlayın `-Xms256m -Xmx16g` . Bayrak, `Xms` bir Java sanal makinesi (JVM) için ilk bellek ayırma havuzunu belirtir, ancak `Xmx` en fazla bellek ayırma havuzunu belirtir. Bu, JVM 'nin bellek miktarı ile başlatıldığı `Xms` ve en fazla bellek miktarını kullanabileceği anlamına gelir `Xmx` . ADF, varsayılan olarak en az 64MB ve en fazla 1G kullanır.

### <a name="data-type-mapping-for-parquet-files"></a>Parquet dosyaları için veri türü eşlemesi

| Veri Fabrikası geçici veri türü | Parquet temel türü | Parquet orijinal türü (serisini kaldırma) | Parquet orijinal türü (serileştirme) |
|:--- |:--- |:--- |:--- |
| Boole | Boole | Yok | Yok |
| SByte | Int32 | Int8 | Int8 |
| Bayt | Int32 | UInt8 | Int16 |
| Int16 | Int32 | Int16 | Int16 |
| UInt16 | Int32 | UInt16 | Int32 |
| Int32 | Int32 | Int32 | Int32 |
| UInt32 | Int64 | UInt32 | Int64 |
| Int64 | Int64 | Int64 | Int64 |
| UInt64 | Int64/binary | UInt64 | Ondalık |
| Tek | Float | Yok | Yok |
| Çift | Çift | Yok | Yok |
| Ondalık | İkili | Ondalık | Ondalık |
| Dize | İkili | Utf8 | Utf8 |
| DateTime | Int96 | Yok | Yok |
| TimeSpan | Int96 | Yok | Yok |
| DateTimeOffset | Int96 | Yok | Yok |
| ByteArray | İkili | Yok | Yok |
| Guid | İkili | Utf8 | Utf8 |
| Char | İkili | Utf8 | Utf8 |
| CharArray | Desteklenmez | Yok | Yok |

## <a name="orc-format-legacy"></a><a name="orc-format"></a> ORC biçimi (eski)

>[!NOTE]
>[Orc biçimindeki](format-orc.md) yeni modeli öğrenin. Dosya tabanlı veri deposu veri kümesi için aşağıdaki yapılandırma, geriye doğru uyumluluk için olduğu gibi hala desteklenmektedir. İleri doğru olan yeni modeli kullanmanız önerilir.

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
> Şirket içinde barındırılan Integration Runtime, örneğin şirket içi ve bulut veri depoları arasında geçiş için, ORC dosyalarını **olduğu gibi** KOPYALAMADıYSANıZ, ır makinenize **64 bit JRE 8 (Java Runtime Environment) veya OpenJDK** yüklemeniz gerekir. Daha ayrıntılı bilgi için aşağıdaki paragrafa bakın.

ORC dosya serileştirme/seri hale getirme ile şirket içinde barındırılan IR üzerinde çalışan kopyalama için, ADF, Visual x + s için kayıt defterini denetleyerek, *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* OpenJDK için sistem değişkenini belirterek, Java çalışma zamanını bulur *`JAVA_HOME`* .

- **JRE 'yi kullanmak için**: 64 bit ır, 64 BIT JRE gerektirir. [Buradan](https://go.microsoft.com/fwlink/?LinkId=808605)bulabilirsiniz.
- **OpenJDK 'yi kullanmak için**: ır sürüm 3,13 ' den itibaren desteklenmektedir. OpenJDK 'nin diğer tüm gerekli Derlemeleriyle jvm.dll, şirket içinde barındırılan IR makinesine paketleyin ve sistem ortam değişkeni JAVA_HOME uygun şekilde ayarlayın.

### <a name="data-type-mapping-for-orc-files"></a>ORC dosyaları için veri türü eşlemesi

| Veri Fabrikası geçici veri türü | ORC türleri |
|:--- |:--- |
| Boole | Boole |
| SByte | Bayt |
| Bayt | Kısadır |
| Int16 | Kısadır |
| UInt16 | int |
| Int32 | int |
| UInt32 | Kalacağını |
| Int64 | Kalacağını |
| UInt64 | Dize |
| Tek | Float |
| Çift | Çift |
| Ondalık | Ondalık |
| Dize | Dize |
| DateTime | Timestamp |
| DateTimeOffset | Timestamp |
| TimeSpan | Timestamp |
| ByteArray | İkili |
| Guid | Dize |
| Char | Char (1) |

## <a name="avro-format-legacy"></a><a name="avro-format"></a> AVRO biçimi (eski)

>[!NOTE]
>Yeni modeli [avro Format](format-avro.md) makalesinden öğrenin. Dosya tabanlı veri deposu veri kümesi için aşağıdaki yapılandırma, geriye doğru uyumluluk için olduğu gibi hala desteklenmektedir. İleri doğru olan yeni modeli kullanmanız önerilir.

Avro dosyalarını ayrıştırmak veya verileri Avro biçiminde yazmak istiyorsanız `format` `type` özelliğini **AvroFormat** olarak ayarlayın. typeProperties bölümünün içindeki Format bölümünde herhangi bir özellik belirtmenize gerek yoktur. Örnek:

```json
"format":
{
    "type": "AvroFormat",
}
```

Hive tablosunda avro biçimi kullanmak için [Apache Hive öğreticisine](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe)başvurabilirsiniz.

Aşağıdaki noktalara dikkat edin:

* [Karmaşık veri türleri](https://avro.apache.org/docs/current/spec.html#schema_complex) desteklenmez (kayıtlar, numaralandırmalar, diziler, Haritalar, birleşimler ve sabit).

## <a name="compression-support-legacy"></a><a name="compression-support"></a> Sıkıştırma desteği (eski)

Azure Data Factory, kopyalama sırasında verileri sıkıştırmayı/sıkıştırmayı destekler. `compression`Bir giriş veri kümesinde özelliği belirttiğinizde kopyalama etkinliği kaynaktaki sıkıştırılmış verileri okur ve sıkıştırmasını açın; bir çıkış veri kümesinde özelliği belirttiğinizde, kopyalama etkinliği sıkıştırır ve sonra verileri havuza yazar. Aşağıda birkaç örnek senaryo verilmiştir:

* Azure Blob 'dan GZIP sıkıştırılmış verileri okuyun, açın ve sonuç verilerini Azure SQL veritabanı 'na yazın. Giriş Azure blob veri kümesini, `compression` `type` özelliği GZIP olarak tanımlarsınız.
* Şirket içi dosya sistemindeki bir düz metin dosyasından verileri okuyun, GZip biçimini kullanarak sıkıştırın ve sıkıştırılmış verileri bir Azure Blob 'una yazın. Özelliği GZip olan bir çıktı Azure blob veri kümesi tanımlarsınız `compression` `type` .
* FTP sunucusundan. zip dosyasını okuyun, dosyaları içine almak için sıkıştırmasını açın ve bu dosyaları Azure Data Lake Store. `compression`Özelliği Zipsöndür olarak bir GIRIŞ FTP veri kümesi tanımlarsınız `type` .
* Bir Azure blobundan GZIP sıkıştırılmış verileri okuyun, açıp açın, BZIP2 kullanarak sıkıştırın ve sonuç verilerini bir Azure blobuna yazın. Bir giriş Azure blob veri kümesini `compression` `type` GZIP olarak ve çıkış VERI kümesini `compression` `type` BZIP2 olarak ayarlanmış şekilde tanımlarsınız.

Bir veri kümesinin sıkıştırmasını belirtmek için, aşağıdaki örnekte gösterildiği gibi JSON veri kümesi içindeki **Compression** özelliğini kullanın:

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

**Sıkıştırma** bölümünün iki özelliği vardır:

* **Tür:** **GZIP**, **söndür**, **BZIP2** veya **zipsöndür** olabilen sıkıştırma codec bileşeni. Bu dosyaları açmak ve dosya tabanlı havuz veri deposuna yazmak için kopyalama etkinliğini kullanırken, dosyalar klasörüne ayıklanacaktır: `<path specified in dataset>/<folder named as source zip file>/` .
* **Düzey:** **En Iyi** veya **en hızlı** olabilen sıkıştırma oranı.

  * **En hızlı:** Elde edilen dosya en iyi şekilde sıkıştırılmasa bile, sıkıştırma işleminin mümkün olduğunca hızlı bir şekilde tamamlanmalıdır.
  * **En iyi**: işlemin tamamlanmasını daha uzun sürse bile sıkıştırma işlemi en iyi şekilde sıkıştırılmalıdır.

    Daha fazla bilgi için bkz. [sıkıştırma düzeyi](/dotnet/api/system.io.compression.compressionlevel) konusu.

> [!NOTE]
> Sıkıştırma ayarları, **Avroformat**, **orcformat** veya **parquetformat** içindeki veriler için desteklenmez. Bu biçimlerdeki dosyaları okurken Data Factory meta verilerde sıkıştırma codec 'ini algılar ve kullanır. Bu biçimlerdeki dosyalara yazarken Data Factory, bu biçimin varsayılan sıkıştırma codec 'ini seçer. Örneğin, OrcFormat için ZLIB ve ParquetFormat için SNAPPY.

## <a name="unsupported-file-types-and-compression-formats"></a>Desteklenmeyen dosya türleri ve sıkıştırma biçimleri

Desteklenmeyen dosyaları dönüştürmek için Azure Data Factory genişletilebilirlik özelliklerini kullanabilirsiniz.
İki seçenek Azure Batch kullanarak Azure Işlevlerini ve özel görevleri içerir.

Bir [tar dosyasının içeriğini ayıklamak](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction)Için bir Azure işlevi kullanan bir örnek görebilirsiniz. Daha fazla bilgi için bkz. [Azure işlevleri etkinliği](./control-flow-azure-function-activity.md).

Özel bir DotNet etkinliği kullanarak bu işlevi de oluşturabilirsiniz. [Burada](./transform-data-using-dotnet-custom-activity.md) daha fazla bilgi bulabilirsiniz

## <a name="next-steps"></a>Sonraki adımlar

Desteklenen [dosya biçimlerinden ve sıkıştırmaları](supported-file-formats-and-compression-codecs.md)tarafından desteklenen en son dosya biçimlerini ve sıkıştırmaları öğrenin.