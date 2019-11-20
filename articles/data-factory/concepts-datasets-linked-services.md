---
title: Azure Data Factory veri kümeleri
description: Data Factory veri kümeleri hakkında bilgi edinin. Veri kümeleri giriş/çıkış verilerini temsil eder.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 74c35d5de74fbf8ecc04cfec336bfeb4a8e669fd
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681521"
---
# <a name="datasets-in-azure-data-factory"></a>Azure Data Factory veri kümeleri
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-create-datasets.md)
> * [Geçerli sürüm](concepts-datasets-linked-services.md)

Bu makalede, veri kümelerinin ne olduğu, JSON biçiminde nasıl tanımlandığı ve Azure Data Factory işlem hatları 'nda nasıl kullanıldığı açıklanmaktadır.

Data Factory yeni bir deyişle, genel bakış için bkz. [Azure Data Factory giriş](introduction.md) .

## <a name="overview"></a>Genel Bakış
Bir veri fabrikasında bir veya daha fazla işlem hattı olabilir. İşlem **hattı** , birlikte bir görevi gerçekleştiren **etkinliklerin** mantıksal gruplandırmasıdır. Bir işlem hattındaki etkinlikler, verilerinizde gerçekleştirilecek eylemleri tanımlar. Şimdi, veri **kümesi** , **etkinliklerde** giriş ve çıkış olarak kullanmak istediğiniz verilere işaret eden veya başvuruda bulunan verilerin adlandırılmış bir görünümüdür. Veri kümeleri tablolar, dosyalar, klasörler ve belgeler gibi farklı veri depolarındaki verileri tanımlar. Örneğin Azure Blob veri kümesi, etkinliğin verileri okuması için gereken blob kapsayıcısını ve Blob depolama klasörünü belirtir.

Bir veri kümesi oluşturmadan önce, veri deponuzu veri fabrikasına bağlamak için [**bağlı bir hizmet**](concepts-linked-services.md) oluşturmanız gerekir. Bağlı hizmetler, dış kaynaklara bağlanmak için Data Factory’ye gereken bağlantı bilgilerini tanımlayan bağlantı dizelerine çok benzer. Bu şekilde düşünün; veri kümesi, bağlantılı veri depolarındaki verilerin yapısını temsil eder ve bağlı hizmet, veri kaynağıyla bağlantıyı tanımlar. Örneğin, Azure depolama bağlı hizmeti bir depolama hesabını veri fabrikasına bağlar. Azure blob veri kümesi, blob kapsayıcısını ve işlenecek giriş bloblarını içeren Azure depolama hesabı içindeki klasörü temsil eder.

Örnek senaryo aşağıda verilmiştir. Blob depolamadan SQL veritabanına veri kopyalamak için, iki bağlı hizmet oluşturursunuz: Azure depolama ve Azure SQL veritabanı. Daha sonra, iki veri kümesi oluşturun: Azure blob veri kümesi (Azure Storage bağlı hizmeti 'ne başvurur) ve Azure SQL tablo veri kümesi (Azure SQL veritabanı bağlı hizmetini ifade eder). Azure depolama ve Azure SQL veritabanı bağlı hizmetleri, Data Factory çalışma zamanında, sırasıyla Azure depolama ve Azure SQL veritabanınıza bağlanmak için kullandığı bağlantı dizelerini içerir. Azure blob veri kümesi blob depoınızda giriş bloblarını içeren blob kapsayıcısını ve BLOB klasörünü belirtir. Azure SQL tablo veri kümesi, verilerin kopyalanacağı SQL veritabanınızda SQL tablosunu belirtir.

Aşağıdaki diyagramda, Data Factory içinde işlem hattı, etkinlik, veri kümesi ve bağlı hizmet arasındaki ilişkiler gösterilmektedir:

![İşlem hattı, etkinlik, veri kümesi, bağlı hizmetler arasındaki ilişki](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>JSON veri kümesi
Data Factory bir veri kümesi aşağıdaki JSON biçiminde tanımlanmıştır:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```
Aşağıdaki tabloda, yukarıdaki JSON 'daki özellikler açıklanmaktadır:

Özellik | Açıklama | Gerekli |
-------- | ----------- | -------- |
ad | Veri kümesinin adı. Bkz. [Azure Data Factory adlandırma kuralları](naming-rules.md). |  Evet |
type | Veri kümesinin türü. Data Factory tarafından desteklenen türlerden birini belirtin (örneğin: AzureBlob, Azuressqltable). <br/><br/>Ayrıntılar için bkz. [veri kümesi türleri](#dataset-type). | Evet |
yapısı | Veri kümesinin şeması. Ayrıntılar için bkz. [DataSet şeması](#dataset-structure-or-schema). | Hayır |
typeProperties | Tür özellikleri her bir tür için farklıdır (örneğin: Azure blob, Azure SQL tablosu). Desteklenen türler ve özellikleri hakkında ayrıntılı bilgi için bkz. [veri kümesi türü](#dataset-type). | Evet |

### <a name="data-flow-compatible-dataset"></a>Veri akışı ile uyumlu veri kümesi



[Veri akışı](concepts-data-flow-overview.md) Ile uyumlu veri kümesi türlerinin listesi için bkz. [desteklenen veri kümesi türleri](#dataset-type) . Veri akışı ile uyumlu veri kümeleri dönüşümler için ayrıntılı veri kümesi tanımları gerektirir. Bu nedenle, JSON tanımı biraz farklıdır. _Yapı_ özelliği yerine, veri akışı Ile uyumlu veri kümelerinin bir _şema_ özelliği vardır.

Veri akışında, veri kümeleri kaynak ve havuz dönüşümlerde kullanılır. Veri kümeleri temel veri şemalarını tanımlar. Verilerinizde şema yoksa, kaynak ve havuzunuzu için şema DRI ' nı kullanabilirsiniz. Veri kümesindeki şema fiziksel veri türünü ve şeklini temsil eder.

Veri kümesinden şemayı tanımlayarak ilgili veri türlerini, veri biçimlerini, dosya konumunu ve ilişkili bağlı hizmetten bağlantı bilgilerini alacaksınız. Veri kümelerinden meta veriler kaynak *projeksiyonu*olarak kaynak dönüşümünde görüntülenir. Kaynak dönüşümünde projeksiyon, tanımlı adlara ve türlere sahip veri akışı verilerini temsil eder.

Bir veri akışı veri kümesinin şemasını içeri aktardığınızda, **Şemayı Içeri aktar** düğmesini seçin ve kaynaktan veya yerel bir dosyadan içeri aktarmayı seçin. Çoğu durumda, şemayı doğrudan kaynaktan içeri aktarırsınız. Ancak zaten bir yerel şema dosyanız varsa (bir Parquet dosyası veya üst bilgiyle CSV), bu dosyadaki şemayı temel olarak Data Factory yönlendirebilirsiniz.


```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```

Aşağıdaki tabloda, yukarıdaki JSON 'daki özellikler açıklanmaktadır:

Özellik | Açıklama | Gerekli |
-------- | ----------- | -------- |
ad | Veri kümesinin adı. Bkz. [Azure Data Factory adlandırma kuralları](naming-rules.md). |  Evet |
type | Veri kümesinin türü. Data Factory tarafından desteklenen türlerden birini belirtin (örneğin: AzureBlob, Azuressqltable). <br/><br/>Ayrıntılar için bkz. [veri kümesi türleri](#dataset-type). | Evet |
manızı | Veri kümesinin şeması. Ayrıntılar için bkz. [veri akışı ile uyumlu veri kümeleri](#dataset-type). | Hayır |
typeProperties | Tür özellikleri her bir tür için farklıdır (örneğin: Azure blob, Azure SQL tablosu). Desteklenen türler ve özellikleri hakkında ayrıntılı bilgi için bkz. [veri kümesi türü](#dataset-type). | Evet |


## <a name="dataset-example"></a>Veri kümesi örneği
Aşağıdaki örnekte, veri kümesi SQL veritabanında MyTable adlı bir tabloyu temsil eder.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": {
                "referenceName": "MyAzureSqlLinkedService",
                "type": "LinkedServiceReference",
        },
        "typeProperties":
        {
            "tableName": "MyTable"
        },
    }
}

```
Aşağıdaki noktalara dikkat edin:

- tür Azuressqltable olarak ayarlandı.
- tableName türü özelliği (Azurestabtable türüne özgü) MyTable olarak ayarlanır.
- linkedServiceName, bir sonraki JSON parçacığında tanımlanan Azuressqldatabase türünde bağlı bir hizmete başvurur.

## <a name="dataset-type"></a>Veri kümesi türü
Kullandığınız veri deposuna bağlı olarak birçok farklı türde veri kümesi vardır. [Bağlayıcı genel bakış](connector-overview.md) makalesindeki Data Factory tarafından desteklenen veri listesini bulabilirsiniz. Bu veri deposu için bağlı bir hizmet ve veri kümesi oluşturmayı öğrenmek için bir veri deposuna tıklayın.

Önceki bölümde bulunan örnekte, veri kümesinin türü **Azuressqltable**olarak ayarlanır. Benzer şekilde, bir Azure blob veri kümesi için, veri kümesinin türü, aşağıdaki JSON içinde gösterildiği gibi **AzureBlob**olarak ayarlanır:

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
                "referenceName": "MyAzureStorageLinkedService",
                "type": "LinkedServiceReference",
        },

        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        }
    }
}
```

## <a name="dataset-structure-or-schema"></a>Veri kümesi yapısı veya şeması
**Yapı** bölümü veya **şema** (veri akışı ile uyumlu) bölümü veri kümeleri isteğe bağlıdır. Bir ad ve sütun veri türleri koleksiyonu içeren veri kümesinin şemasını tanımlar. Kaynak olan türleri ve eşleme sütunlarını hedefe dönüştürmek için kullanılan tür bilgilerini sağlamak için yapı bölümünü kullanın.

Yapıdaki her sütun aşağıdaki özellikleri içerir:

Özellik | Açıklama | Gerekli
-------- | ----------- | --------
ad | Sütunun adı. | Evet
type | Sütunun veri türü. Data Factory, izin verilen değerler olarak şu veri türlerini destekler: **Int16, Int32, Int64, Single, Double, Decimal, Byte [], Boolean, String, Guid, DateTime, DateTimeOffset ve TimeSpan** | Hayır
ayarı | . Tür bir .NET türü olduğunda kullanılacak NET tabanlı kültür: `Datetime` veya `Datetimeoffset`. Varsayılan değer: `en-us`. | Hayır
formatını | Tür bir .NET türü olduğunda kullanılacak biçim dizesi: `Datetime` veya `Datetimeoffset`. Tarih saat biçimini biçimlendirmek için [özel tarih ve saat biçim dizelerine](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) bakın. | Hayır

### <a name="example"></a>Örnek
Aşağıdaki örnekte, kaynak blobu verilerinin CSV biçiminde olduğunu ve üç sütun içerdiğini varsayalım: UserID, Name ve LastLoginDate. Bu değerler, haftanın günü için kısaltılmış Fransız adlarını kullanarak özel bir tarih saat biçimiyle Int64, dize ve tarih/saat türündedir.

Blob veri kümesi yapısını, sütunlar için tür tanımlarıyla birlikte aşağıdaki gibi tanımlayın:

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>Rehber

Aşağıdaki yönergeler, yapı bilgilerinin ne zaman ekleneceğini ve **Yapı** bölümüne nelerin ekleneceğini anlamanıza yardımcı olur. Data Factory 'nin kaynak verileri havuza nasıl eşlediğini ve [şemadan ve tür eşleştirmesinin](copy-activity-schema-and-type-mapping.md)yapı bilgilerini ne zaman belirtmesi hakkında daha fazla bilgi edinin.

- **Güçlü şema veri kaynakları için**, yalnızca kaynak sütunlarının havuz sütunlarına eşlenmesi istiyorsanız yapı bölümünü belirtin ve adları aynı değildir. Bu tür bir yapılandırılmış veri kaynağı, veri şemasını ve tür bilgilerini verilerin kendisiyle birlikte depolar. Yapılandırılmış veri kaynaklarına örnek olarak SQL Server, Oracle ve Azure SQL veritabanı verilebilir.<br/><br/>Tür bilgileri zaten yapılandırılmış veri kaynakları için kullanılabilir olduğundan, yapı bölümünü dahil ettiğinizde tür bilgilerini içermemelidir.
- **BLOB depolamada metin dosyası olmayan/zayıf şema veri kaynakları için**, veri kümesi bir kopyalama etkinliği girişi olduğunda yapıyı ekleyin ve kaynak veri kümesinin veri türleri havuz için yerel türlere dönüştürülmelidir. Ve kaynak sütunlarını havuz sütunlarına eşlemek istediğinizde yapıyı dahil edin.

## <a name="create-datasets"></a>Veri kümeleri oluşturun
Şu araçlardan veya SDK 'Lardan birini kullanarak veri kümeleri oluşturabilirsiniz: [.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), Azure Resource Manager şablonu ve Azure Portal

## <a name="current-version-vs-version-1-datasets"></a>Geçerli sürüm ile sürüm 1 veri kümeleri

Data Factory ve Data Factory sürüm 1 veri kümeleri arasında bazı farklılıklar vardır:

- External özelliği geçerli sürümde desteklenmiyor. Bir [tetikleyici](concepts-pipeline-execution-triggers.md)ile değiştirilmiştir.
- İlke ve kullanılabilirlik özellikleri geçerli sürümde desteklenmiyor. İşlem hattının başlangıç saati [tetikleyicilere](concepts-pipeline-execution-triggers.md)bağlıdır.
- Kapsamlı veri kümeleri (bir ardışık düzende tanımlanan veri kümeleri) geçerli sürümde desteklenmez.

## <a name="next-steps"></a>Sonraki adımlar
Bu araçlardan veya SDK 'Lardan birini kullanarak işlem hatları ve veri kümeleri oluşturmaya yönelik adım adım yönergeler için aşağıdaki öğreticiye bakın.

- [Hızlı başlangıç: .NET kullanarak veri fabrikası oluşturma](quickstart-create-data-factory-dot-net.md)
- [Hızlı başlangıç: PowerShell kullanarak veri fabrikası oluşturma](quickstart-create-data-factory-powershell.md)
- [Hızlı başlangıç: REST API kullanarak veri fabrikası oluşturma](quickstart-create-data-factory-rest-api.md)
- [Hızlı başlangıç: Azure portal kullanarak veri fabrikası oluşturma](quickstart-create-data-factory-portal.md)
