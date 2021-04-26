---
title: Kopyalama etkinliğinde şema ve veri türü eşleme
description: Azure Data Factory ' deki kopyalama etkinliğinin şemaları ve veri türlerini kaynak verilerden havuz verilerine nasıl eşlediğini öğrenin.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: jingwang
ms.openlocfilehash: 68d90fa56d5dce902a44a32b322e582a81e419d5
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011650"
---
# <a name="schema-and-data-type-mapping-in-copy-activity"></a>Kopyalama etkinliğinde şema ve veri türü eşleme
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, Azure Data Factory kopyalama etkinliğinin, kaynak verilerden havuz verilerine şema eşlemesi ve veri türü eşlemesi gerçekleştirme yöntemi açıklanır.

## <a name="schema-mapping"></a>Şema eşleme

### <a name="default-mapping"></a>Varsayılan eşleme

Varsayılan olarak, etkinlik, kaynak verilerini, **sütun adlarına göre** , büyük/küçük harfe duyarlı şekilde, havuza eşler. Havuz yoksa, örneğin, dosya (ler) i yazmak için kaynak alanı adları havuz adları olarak kalıcı olur. Havuz zaten varsa, kaynaktan Kopyalanmakta olan tüm sütunları içermesi gerekir. Bu tür varsayılan eşleme, kaynak veri deposu tarafından döndürülen tüm veriler havuza kopyalanabilir ve kaynaktan havuza kadar esnek şemaları ve şema drmasını destekler.

Kaynağınız başlık satırı olmayan bir metin dosyası ise, kaynak sütun adlarını içermediğinden [Açık eşleme](#explicit-mapping) gerekir.

### <a name="explicit-mapping"></a>Açık eşleme

Ayrıca, ihtiyacınız olan sütun/alan eşlemesini kaynak olarak havuza göre özelleştirmek için açık eşleme belirtebilirsiniz. Açık eşleme ile, yalnızca kısmen kaynak verileri havuza kopyalayabilir veya kaynak verileri farklı adlarla havuza eşleyebilirsiniz veya tablo/hiyerarşik verileri yeniden şekillendirebilirsiniz. Kopyalama etkinliği:

1. Kaynaktaki verileri okur ve kaynak şemayı tespit edin.
2. Tanımlı eşlemenizi uygular.
3. Verileri havuza yazar.

Aşağıdakiler hakkında daha fazla bilgi edinin:

- [Tablosal kaynaktan tablolu havuz](#tabular-source-to-tabular-sink)
- [Hiyerarşik kaynaktan tablo havuzuna](#hierarchical-source-to-tabular-sink)
- [Hiyerarşik havuza tablo/hiyerarşik kaynak](#tabularhierarchical-source-to-hierarchical-sink)

Eşlemeyi Data Factory yazma Kullanıcı arabirimi-> kopyalama etkinliği > eşleme sekmesinde yapılandırabilir veya kopyalama etkinliği-> özelliği aracılığıyla eşlemeyi programlı bir şekilde belirtebilirsiniz `translator` . Aşağıdaki özellikler `translator`  ->  `mappings` dizi > nesnelerinde desteklenir-> `source` ve `sink` verileri eşlemek için belirli bir sütunu/alanı işaret eder.

| Özellik | Açıklama                                                  | Gerekli |
| -------- | ------------------------------------------------------------ | -------- |
| name     | Kaynak veya havuz sütununun/alanının adı. Tablosal kaynak ve havuz için geçerlidir. | Yes      |
| numarasını  | Sütun dizini. 1 ' den başlayın. <br>Üst bilgi satırı olmayan sınırlandırılmış metin kullanılırken geçerlidir ve gereklidir. | No       |
| path     | Ayıklanacak veya eşlenecek her alan için JSON yol ifadesi. Hiyerarşik kaynak ve havuz için, örneğin Cosmos DB, MongoDB veya REST bağlayıcıları için geçerlidir.<br>Kök nesnesi altındaki alanlar için JSON yolu root ile başlar `$` ; özelliği tarafından seçilen dizi içindeki alanlar IÇIN `collectionReference` JSON yolu, olmadan dizi öğesinden başlar `$` . | No       |
| tür     | Kaynak veya havuz sütununun geçici veri türü Data Factory. Genel olarak, bu özelliği belirtmeniz veya değiştirmeniz gerekmez. [Veri türü eşlemesi](#data-type-mapping)hakkında daha fazla bilgi edinin. | No       |
| kültür  | Kaynak veya havuz sütununun kültürü. Tür veya olduğunda geçerlidir `Datetime` `Datetimeoffset` . Varsayılan değer: `en-us`.<br>Genel olarak, bu özelliği belirtmeniz veya değiştirmeniz gerekmez. [Veri türü eşlemesi](#data-type-mapping)hakkında daha fazla bilgi edinin. | No       |
| biçim   | Tür veya olduğunda kullanılacak biçim dizesi `Datetime` `Datetimeoffset` . Tarih saat biçimini biçimlendirmek için [özel tarih ve saat biçim dizelerine](/dotnet/standard/base-types/custom-date-and-time-format-strings) bakın. Genel olarak, bu özelliği belirtmeniz veya değiştirmeniz gerekmez. [Veri türü eşlemesi](#data-type-mapping)hakkında daha fazla bilgi edinin. | No       |

Aşağıdaki özellikler aşağıdakilere ek olarak desteklenir `translator` `mappings` :

| Özellik            | Açıklama                                                  | Gerekli |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Hiyerarşik kaynaktaki verileri kopyalarken uygulayın, örneğin, Cosmos DB, MongoDB veya REST bağlayıcıları.<br>Aynı düzene sahip **bir dizi alanı içindeki** nesnelerden verileri yinelemek ve ayıklamak istiyorsanız, her nesne için bu dizinin JSON yolunu belirtin. | No       |

#### <a name="tabular-source-to-tabular-sink"></a>Tablosal kaynaktan tablolu havuz

Örneğin, Salesforce 'tan Azure SQL veritabanı 'na veri kopyalama ve üç sütunu açıkça eşleme:

1. Kopyalama etkinliği-> eşleme sekmesinde, hem kaynak hem de havuz şemalarını içeri aktarmak için **şemaları Içeri aktar** düğmesine tıklayın.

2. Gerekli alanları eşleyin ve geri kalanı hariç tutun/silin.

![Tablosal tablolu eşleme](media/copy-activity-schema-and-type-mapping/map-tabular-to-tabular.png)

Aynı eşleme, kopyalama etkinliği yükü (bkz.) içinde aşağıdaki şekilde yapılandırılabilir `translator` :

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "SalesforceSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "name": "Id" },
                    "sink": { "name": "CustomerID" }
                },
                {
                    "source": { "name": "Name" },
                    "sink": { "name": "LastName" }
                },
                {
                    "source": { "name": "LastModifiedDate" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

Üst bilgi satırı olmayan sınırlı metin dosyasından verileri kopyalamak için, sütunlar adları yerine Ordinal ile temsil edilir. 

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "ordinal": "1" },
                    "sink": { "name": "CustomerID" }
                }, 
                {
                    "source": { "ordinal": "2" },
                    "sink": { "name": "LastName" }
                }, 
                {
                    "source": { "ordinal": "3" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

#### <a name="hierarchical-source-to-tabular-sink"></a>Hiyerarşik kaynaktan tablo havuzuna

Hiyerarşik kaynaktaki verileri tablolu havuza kopyalarken, kopyalama etkinliği aşağıdaki özellikleri destekler:

- Nesnelerden ve dizilerden veri ayıklayın.
- Bir diziden aynı düzene sahip birden fazla nesne yerleştirme, bu durumda bir JSON nesnesini tablo sonucunda birden çok kayda dönüştürmek için.

Daha gelişmiş hiyerarşik tablo dönüştürmesi için [veri akışını](concepts-data-flow-overview.md)kullanabilirsiniz. 

Örneğin, aşağıdaki içeriğe sahip kaynak MongoDB belgeniz varsa:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
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
    "city": [ { "name": "Seattle" } ]
}
```

Ayrıca, üst bilgi satırı ile aşağıdaki biçimdeki bir metin dosyasına kopyalamak istiyorsanız, dizideki verileri *(order_pd ve order_price)* ve ortak kök bilgiyle *(sayı, tarih ve şehir)* çapraz birleşerek ekleyin:

| Sipariş numarası | orderDate | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | Seattle |
| 01          | 20170122  | P2       | 13          | Seattle |
| 01          | 20170122  | P3       | 231         | Seattle |

Data Factory yazma Kullanıcı arabiriminde bu eşlemeyi tanımlayabilirsiniz:

1. Kopyalama etkinliği-> eşleme sekmesinde, hem kaynak hem de havuz şemalarını içeri aktarmak için **şemaları Içeri aktar** düğmesine tıklayın. Data Factory şemayı içeri aktarırken en az sayıda nesneyi örneklediğinizde, herhangi bir alan görünmüyorsa, var olan bir alan adının üzerine gelin ve düğüm, nesne veya dizi eklemeyi tercih edebilirsiniz.

2. Yinelemek istediğiniz diziyi seçin ve verileri ayıklayın. **Koleksiyon başvurusu** olarak otomatik olarak doldurulur. Note bu işlem için yalnızca tek bir dizi desteklenir.

3. Gerekli alanları havuza eşleyin. Data Factory hiyerarşik kenar için karşılık gelen JSON yollarını otomatik olarak belirler.

> [!NOTE]
> Toplama başvurusu olarak işaretlenen dizinin boş olduğu ve onay kutusunun seçildiği kayıtlar için tüm kayıt atlanır.

![Kullanıcı arabirimini kullanarak hiyerarşik olarak tablolu eşleme](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-ui.png)

Ayrıca, alanların JSON yollarını doğrudan görüp düzenleyebilmeniz durumunda **Gelişmiş düzenleyiciye** geçiş yapabilirsiniz. Bu görünüme yeni eşleme eklemeyi tercih ederseniz JSON yolunu belirtin.

![Gelişmiş düzenleyiciyi kullanarak sıradüzenli ile tablolu eşleme](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-advanced-editor.png)

Aynı eşleme, kopyalama etkinliği yükü (bkz.) içinde aşağıdaki şekilde yapılandırılabilir `translator` :


```json
{
    "name": "CopyActivityHierarchicalToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "MongoDbV2Source" },
        "sink": { "type": "DelimitedTextSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "path": "$['number']" },
                    "sink": { "name": "orderNumber" }
                },
                {
                    "source": { "path": "$['date']" },
                    "sink": { "name": "orderDate" }
                },
                {
                    "source": { "path": "['prod']" },
                    "sink": { "name": "order_pd" }
                },
                {
                    "source": { "path": "['price']" },
                    "sink": { "name": "order_price" }
                },
                {
                    "source": { "path": "$['city'][0]['name']" },
                    "sink": { "name": "city" }
                }
            ],
            "collectionReference": "$['orders']"
        }
    },
    ...
}
```

#### <a name="tabularhierarchical-source-to-hierarchical-sink"></a>Hiyerarşik havuza tablo/hiyerarşik kaynak

Kullanıcı deneyimi akışı, [hiyerarşik kaynağa tablo havuzuna](#hierarchical-source-to-tabular-sink)benzer. 

Tablo kaynağından hiyerarşik havuza veri kopyalarken, nesne içindeki diziye yazma desteklenmez.

Hiyerarşik kaynaktaki verileri hiyerarşik havuza kopyalarken, nesne/dizi ' ı seçerek ve iç alanlara dokunmadan havuza Eşle ' ye ek olarak katmanın tüm hiyerarşisini de koruyabilirsiniz.

Daha gelişmiş veri yeniden şekillendirme dönüştürmesi için [veri akışını](concepts-data-flow-overview.md)kullanabilirsiniz. 

### <a name="parameterize-mapping"></a>Eşleme Parametreleştir

Çok sayıda nesneyi dinamik olarak kopyalamak için bir şablonlaştırılmış işlem hattı oluşturmak istiyorsanız, [Varsayılan eşlemeyi](#default-mapping) kullanıp kullanmayacağınızı veya ilgili nesneler için [Açık eşleme](#explicit-mapping) tanımlamanız gerektiğini belirleyin.

Açık eşleme gerekliyse şunları yapabilirsiniz:

1. İşlem hattı düzeyinde nesne türü olan bir parametre tanımlayın, örneğin, `mapping` .

2. Eşlemeyi Parametreleştirme: kopyalama etkinliği-> eşleme sekmesinde dinamik içerik eklemek ve yukarıdaki parametreyi seçmek için seçin. Etkinlik yükü aşağıdaki gibi olacaktır:

    ```json
    {
        "name": "CopyActivityHierarchicalToTabular",
        "type": "Copy",
        "typeProperties": {
            "source": {...},
            "sink": {...},
            "translator": {
                "value": "@pipeline().parameters.mapping",
                "type": "Expression"
            },
            ...
        }
    }
    ```

3. Eşleme parametresine geçirilecek değeri oluşturun. Bu, tanım nesnesinin tamamı olmalıdır `translator` ve [Açık eşleme](#explicit-mapping) bölümündeki örneklere bakın. Örneğin, tablo kaynağından tablolu havuz kopyalama için değer olmalıdır `{"type":"TabularTranslator","mappings":[{"source":{"name":"Id"},"sink":{"name":"CustomerID"}},{"source":{"name":"Name"},"sink":{"name":"LastName"}},{"source":{"name":"LastModifiedDate"},"sink":{"name":"ModifiedDate"}}]}` .

## <a name="data-type-mapping"></a>Veri türü eşlemesi

Kopyalama etkinliği, aşağıdaki akış ile havuz türleri eşleme için kaynak türlerini gerçekleştirir: 

1. Kaynak yerel veri türlerinden Azure Data Factory geçici veri türlerine Dönüştür.
2. Her iki [varsayılan eşleme](#default-mapping) ve [Açık eşleme](#explicit-mapping)için geçerli olan ilgili havuz türleriyle eşleşecek şekilde, geçici veri türünü gerektiği şekilde otomatik olarak dönüştürün.
3. Azure Data Factory geçici veri türlerinden yerel veri türlerini havuza dönüştürme.

Kopyalama etkinliği şu anda şu veri türlerini destekler: Boolean, Byte, Byte array, DateTime, DatetimeOffset, Decimal, Double, GUID, Int16, Int32, Int64, SByte, Single, String, TimeSpan, UInt16, UInt32 ve UInt64.

Kaynak-havuz arasında aradaki türler arasında aşağıdaki veri türü dönüştürmeleri desteklenir.

| Source\Sink | Boole | Bayt dizisi | Ondalık | Tarih/saat <small>(1)</small> | Kayan nokta <small>(2)</small> | GUID | Tamsayı <small>(3)</small> | Dize | TimeSpan |
| ----------- | ------- | ---------- | ------- | ---------------------------- | ------------------------------ | ---- | -------------------------- | ------ | -------- |
| Boole     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Bayt dizisi  |         | ✓          |         |                              |                                |      |                            | ✓      |          |
| Tarih/Saat   |         |            |         | ✓                            |                                |      |                            | ✓      |          |
| Ondalık     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Kayan nokta | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| GUID        |         |            |         |                              |                                | ✓    |                            | ✓      |          |
| Tamsayı     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Dize      | ✓       | ✓          | ✓       | ✓                            | ✓                              | ✓    | ✓                          | ✓      | ✓        |
| TimeSpan    |         |            |         |                              |                                |      |                            | ✓      | ✓        |

(1) Tarih/saat DateTime ve DateTimeOffset içerir.

(2) kayan nokta tek ve çift içerir.

(3) tamsayı SByte, Byte, Int16, UInt16, Int32, UInt32, Int64 ve UInt64 içerir.

> [!NOTE]
> - Veri türü dönüştürme Şu anda tablolu veriler arasında kopyalanırken desteklenir. Hiyerarşik kaynaklar/havuzlar desteklenmez; bu, kaynak ve havuz ara türleri arasında sistem tarafından tanımlanan veri türü dönüştürmesi olmadığı anlamına gelir.
> - Bu özellik, en son veri kümesi modeliyle birlikte kullanılır. Bu seçeneği kullanıcı arabiriminden görmüyorsanız, yeni bir veri kümesi oluşturmayı deneyin.

Aşağıdaki özellikler veri türü dönüştürmesi için kopyalama etkinliğinde desteklenir ( `translator` programmatical Authoring için bölümünde):

| Özellik                         | Açıklama                                                  | Gerekli |
| -------------------------------- | ------------------------------------------------------------ | -------- |
| Tür dönüştürme                   | Yeni veri türü dönüştürme deneyimini etkinleştirin. <br>Geriye dönük uyumluluk nedeniyle varsayılan değer false 'tur.<br><br>Data Factory yazma Kullanıcı arabirimi aracılığıyla, geç Haziran 2020 ' den sonra oluşturulan yeni kopyalama etkinlikleri için, bu veri türü dönüştürmesi en iyi deneyim için varsayılan olarak etkindir ve ilgili senaryolar için kopyalama etkinliği ' ne > eşleme sekmesinde aşağıdaki tür dönüştürme ayarlarını görebilirsiniz. <br>Programlı olarak işlem hattı oluşturmak için özelliği etkinleştirmek üzere açıkça `typeConversion` true olarak ayarlamanız gerekir.<br>Bu özellik yayınlanmadan önce oluşturulan mevcut kopyalama etkinlikleri için, geriye dönük uyumluluk için Data Factory yazma Kullanıcı arabirimine tür dönüştürme seçeneklerini görmezsiniz. | No       |
| typeConversionSettings           | Tür dönüştürme ayarları grubu. `typeConversion`Olarak ayarlandığında Uygula `true` . Aşağıdaki özellikler bu grubun altında bulunur. | No       |
| *Altındaki `typeConversionSettings`* |                                                              |          |
| Allowdatakesilme              | Kaynak verileri kopyalama sırasında farklı tür ile havuza dönüştürürken veri kesilmesine izin ver, örneğin Decimal 'den integer 'e, DatetimeOffset 'den TarihSaat 'e kadar. <br>True varsayılan değerdir. | No       |
| treatBooleanAsNumber             | Boolean değerlerini sayı olarak değerlendirin, örneğin, true, 1.<br>Varsayılan değer false 'dur. | No       |
| dateTimeFormat                   | Örneğin, saat dilimi boşluğu ve dizeleri olmadan tarihler arasında dönüştürme yaparken dize biçimlendirme `yyyy-MM-dd HH:mm:ss.fff` .  Ayrıntılı bilgi için [özel tarih ve saat biçim dizelerine](/dotnet/standard/base-types/custom-date-and-time-format-strings) bakın. | No       |
| dateTimeOffsetFormat             | Saat dilimi boşluğu ve dizeleri ile tarihler arasında dönüştürme sırasında dize biçimlendirme (örneğin,) `yyyy-MM-dd HH:mm:ss.fff zzz` .  Ayrıntılı bilgi için [özel tarih ve saat biçim dizelerine](/dotnet/standard/base-types/custom-date-and-time-format-strings) bakın. | No       |
| timeSpanFormat                   | Zaman dönemleri ve dizeler arasında dönüştürme sırasında dize biçimlendirme (örneğin,) `dd\.hh\:mm` . Ayrıntılı bilgi için [Özel TimeSpan Biçim dizelerine](/dotnet/standard/base-types/custom-timespan-format-strings) bakın. | No       |
| kültür                          | Türler dönüştürülürken kullanılacak kültür bilgileri, örneğin `en-us` veya `fr-fr` . | No       |

**Örnek:**

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "ParquetSource"
        },
        "sink": {
            "type": "SqlSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "typeConversion": true,
            "typeConversionSettings": {
                "allowDataTruncation": true,
                "treatBooleanAsNumber": true,
                "dateTimeFormat": "yyyy-MM-dd HH:mm:ss.fff",
                "dateTimeOffsetFormat": "yyyy-MM-dd HH:mm:ss.fff zzz",
                "timeSpanFormat": "dd\.hh\:mm",
                "culture": "en-gb"
            }
        }
    },
    ...
}
```

## <a name="legacy-models"></a>Eski modeller

> [!NOTE]
> Kaynak sütunları/alanları havuza eşlemek için aşağıdaki modeller, geriye dönük uyumluluk için olduğu gibi hala desteklenmektedir. [Şema eşlemesinde](#schema-mapping)belirtilen yeni modeli kullanmanızı öneririz. Data Factory yazma Kullanıcı arabirimi yeni modeli oluşturmaya geçti.

### <a name="alternative-column-mapping-legacy-model"></a>Alternatif sütun eşleme (eski model)

`translator`  ->  `columnMappings` Tablosal şekilli veriler arasında eşlenecek kopyalama etkinliği-> belirtebilirsiniz. Bu durumda, hem giriş hem de çıkış veri kümeleri için "yapı" bölümü gereklidir. Sütun eşleme **, "Structure" kaynak veri kümesindeki sütunların tüm veya alt kümelerinin havuz veri kümesi "Structure" içindeki tüm sütunlara eşlenmesinin** kullanılmasını destekler. Aşağıda bir özel durumla sonuçlanan hata koşulları verilmiştir:

- Kaynak veri deposu sorgu sonucunun, giriş veri kümesi "yapısı" bölümünde belirtilen bir sütun adı yok.
- Havuz veri deposu (önceden tanımlanmış şema varsa), çıkış veri kümesi "yapısı" bölümünde belirtilen bir sütun adına sahip değildir.
- Havuz veri kümesinin "yapısında" eşlemede belirtilenden daha az sütun veya daha fazla sütun.
- Yinelenen eşleme.

Aşağıdaki örnekte, giriş veri kümesi bir yapıya sahiptir ve şirket içi Oracle veritabanındaki bir tabloya işaret eder.

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

Bu örnekte, çıkış veri kümesi bir yapıya sahiptir ve Salesfoce içindeki bir tabloya işaret eder.

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

Aşağıdaki JSON bir işlem hattındaki kopyalama etkinliğini tanımlar. **Çevirmen**  ->  **ColumnMappings** özelliği kullanılarak, havuzdaki sütunlara eşlenen kaynak sütunları.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

`"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"`Sütun eşlemesini belirtmek için öğesinin sözdizimini kullanıyorsanız, hala olduğu gibi desteklenir.

### <a name="alternative-schema-mapping-legacy-model"></a>Alternatif şema eşleme (eski model)

`translator`  ->  `schemaMapping` Hiyerarşik şekillendirilmiş veriler ve tablo şeklinde şekillendirilmiş veriler arasında eşleme yapmak için kopyalama etkinliği-> belirtebilirsiniz (örneğin, MongoDB/Rest 'den metin dosyasına kopyalama ve Oracle 'dan MongoDB için Azure Cosmos DB 'in API 'sine kopyalama). Kopyalama etkinliği bölümünde aşağıdaki özellikler desteklenir `translator` :

| Özellik            | Açıklama                                                  | Gerekli |
| :------------------ | :----------------------------------------------------------- | :------- |
| tür                | Kopyalama etkinliği çeviricisinin Type özelliği: **Tabulartranslator** olarak ayarlanmalıdır | Yes      |
| schemaMapping       | **Kaynak taraftan havuz tarafına** olan eşleme ilişkisini temsil eden anahtar-değer çiftleri koleksiyonu.<br/>- **Anahtar:** kaynağı temsil eder. **Tablo kaynağı** için, sütun adını veri kümesi yapısında tanımlanan şekilde belirtin; **hiyerarşik kaynak** için, Ayıklanacak ve eşlenecek her alan için JSON yolu ifadesini belirtin.<br>- **Değer:** havuzu temsil eder. **Tablo havuzu** için, sütun adını veri kümesi yapısında tanımlanan şekilde belirtin; **hiyerarşik havuz** için, Ayıklanacak ve eşlenecek her alan için JSON yolu ifadesini belirtin. <br>Hiyerarşik veriler söz konusu olduğunda, kök nesne altındaki alanlar için JSON yolu kök $; ile başlar özelliği tarafından seçilen dizi içindeki alanlar için `collectionReference` JSON yolu dizi öğesinden başlar. | Yes      |
| collectionReference | Aynı düzene sahip **bir dizi alanı içindeki** nesnelerden verileri yinelemek ve ayıklamak istiyorsanız, her nesne için bu dizinin JSON yolunu belirtin. Bu özellik yalnızca hiyerarşik veriler kaynak olduğunda desteklenir. | No       |

**Örnek: MongoDB 'den Oracle 'a kopyalama:**

Örneğin, aşağıdaki içeriğe sahip MongoDB belgeniz varsa:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
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
    "city": [ { "name": "Seattle" } ]
}
```

Ayrıca, dizi içindeki verileri *(order_pd ve order_price)* ve ortak kök bilgiyle *(numara, tarih ve şehir)* çapraz birleştirmeyi düzleştirerek, bir Azure SQL tablosuna aşağıdaki biçimde kopyalamak istersiniz:

| Sipariş numarası | orderDate | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | Seattle |
| 01          | 20170122  | P2       | 13          | Seattle |
| 01          | 20170122  | P3       | 231         | Seattle |

Şema eşleme kuralını aşağıdaki kopyalama etkinliği JSON örneği olarak yapılandırın:

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "$.number": "orderNumber",
                "$.date": "orderDate",
                "prod": "order_pd",
                "price": "order_price",
                "$.city[0].name": "city"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar
Diğer kopyalama etkinliği makalelerine bakın:

- [Kopyalama etkinliğine genel bakış](copy-activity-overview.md)
