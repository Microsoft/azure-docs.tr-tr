---
title: Veri kümeleri
description: Data Factory veri kümeleri hakkında bilgi edinin. Veri kümeleri giriş/çıkış verilerini temsil eder.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/24/2020
ms.openlocfilehash: 167fb0014f2f0a9e7a2530fe276289f94347146e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104785793"
---
# <a name="datasets-in-azure-data-factory"></a>Azure Data Factory'de veri kümeleri
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-create-datasets.md)
> * [Güncel sürüm](concepts-datasets-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Bu makalede, veri kümelerinin ne olduğu, JSON biçiminde nasıl tanımlandığı ve Azure Data Factory işlem hatları 'nda nasıl kullanıldığı açıklanmaktadır.

Data Factory yeni bir deyişle, genel bakış için bkz. [Azure Data Factory giriş](introduction.md) .

## <a name="overview"></a>Genel Bakış
Bir veri fabrikasında bir veya daha fazla işlem hattı olabilir. İşlem **hattı** , birlikte bir görevi gerçekleştiren **etkinliklerin** mantıksal gruplandırmasıdır. İşlem hattındaki etkinlikler, verilerinizde gerçekleştirilecek eylemleri tanımlar. Şimdi, veri **kümesi** , **etkinliklerde** giriş ve çıkış olarak kullanmak istediğiniz verilere işaret eden veya başvuruda bulunan verilerin adlandırılmış bir görünümüdür. Veri kümeleri tablolar, dosyalar, klasörler ve belgeler gibi farklı veri depolarındaki verileri tanımlar. Örneğin Azure Blob veri kümesi, etkinliğin verileri okuması için gereken blob kapsayıcısını ve Blob depolama klasörünü belirtir.

Bir veri kümesi oluşturmadan önce, veri deponuzu veri fabrikasına bağlamak için [**bağlı bir hizmet**](concepts-linked-services.md) oluşturmanız gerekir. Bağlı hizmetler, dış kaynaklara bağlanmak için Data Factory’ye gereken bağlantı bilgilerini tanımlayan bağlantı dizelerine çok benzer. Bu şekilde düşünün; veri kümesi, bağlantılı veri depolarındaki verilerin yapısını temsil eder ve bağlı hizmet, veri kaynağıyla bağlantıyı tanımlar. Örneğin, Azure depolama bağlı hizmeti bir depolama hesabını veri fabrikasına bağlar. Azure blob veri kümesi, blob kapsayıcısını ve işlenecek giriş bloblarını içeren Azure depolama hesabı içindeki klasörü temsil eder.

Örnek senaryo aşağıda verilmiştir. Blob depolamadan SQL veritabanına veri kopyalamak için, iki bağlı hizmet oluşturursunuz: Azure Blob depolama ve Azure SQL veritabanı. Daha sonra, iki veri kümesi oluşturun: ayrılmış metin veri kümesi (Azure Blob depolama bağlı hizmeti 'ne başvurur, kaynak olarak metin dosyaları olduğu varsayıldığında) ve Azure SQL tablo veri kümesi (Azure SQL veritabanı bağlı hizmetini ifade eder). Azure Blob depolama ve Azure SQL veritabanı bağlantılı Hizmetleri, Data Factory çalışma zamanında, sırasıyla Azure depolama ve Azure SQL veritabanınıza bağlanmak için kullandığı bağlantı dizelerini içerir. Sınırlandırılmış metin veri kümesi, BLOB depolama alanındaki giriş bloblarını içeren blob kapsayıcısını ve BLOB klasörünü, biçimle ilgili ayarlarla birlikte belirtir. Azure SQL tablo veri kümesi, verilerin kopyalanacağı SQL veritabanınızda SQL tablosunu belirtir.

Aşağıdaki diyagramda, Data Factory içinde işlem hattı, etkinlik, veri kümesi ve bağlı hizmet arasındaki ilişkiler gösterilmektedir:

![İşlem hattı, etkinlik, veri kümesi, bağlı hizmetler arasındaki ilişki](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>JSON veri kümesi
Data Factory bir veri kümesi aşağıdaki JSON biçiminde tanımlanmıştır:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: DelimitedText, AzureSqlTable etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema":[

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
name | Veri kümesinin adı. Bkz. [Azure Data Factory adlandırma kuralları](naming-rules.md). |  Yes |
tür | Veri kümesinin türü. Data Factory tarafından desteklenen türlerden birini belirtin (örneğin: DelimitedText, Azurestabtable). <br/><br/>Ayrıntılar için bkz. [veri kümesi türleri](#dataset-type). | Yes |
schema | Veri kümesinin şeması, fiziksel veri türü ve şeklini temsil eder. | No |
typeProperties | Tür özellikleri her tür için farklıdır. Desteklenen türler ve özellikleri hakkında ayrıntılı bilgi için bkz. [veri kümesi türü](#dataset-type). | Yes |

Veri kümesinin şemasını içeri aktardığınızda, **Şemayı Içeri aktar** düğmesini seçin ve kaynaktan veya yerel bir dosyadan içeri aktarmayı seçin. Çoğu durumda, şemayı doğrudan kaynaktan içeri aktarırsınız. Ancak zaten bir yerel şema dosyanız varsa (bir Parquet dosyası veya üst bilgiyle CSV), bu dosyadaki şemayı temel olarak Data Factory yönlendirebilirsiniz.

Kopyalama etkinliğinde, veri kümeleri kaynak ve havuzda kullanılır. Veri kümesinde tanımlanan şema, başvuru olarak isteğe bağlıdır. Kaynak ve havuz arasında sütun/alan eşlemesi uygulamak istiyorsanız, [şema ve tür eşleme](copy-activity-schema-and-type-mapping.md)' ye başvurun.

Veri akışında, veri kümeleri kaynak ve havuz dönüşümlerde kullanılır. Veri kümeleri temel veri şemalarını tanımlar. Verilerinizde şema yoksa, kaynak ve havuzunuzu için şema DRI ' nı kullanabilirsiniz. Veri kümelerinden meta veriler kaynak projeksiyonu olarak kaynak dönüşümünde görüntülenir. Kaynak dönüşümünde projeksiyon, tanımlı adlara ve türlere sahip veri akışı verilerini temsil eder.

## <a name="dataset-type"></a>Veri kümesi türü

Azure Data Factory, kullandığınız veri depolarına bağlı olarak pek çok farklı türde veri kümesini destekler. [Bağlayıcı genel bakış](connector-overview.md) makalesindeki Data Factory tarafından desteklenen veri depolarının listesini bulabilirsiniz. Bağlı bir hizmetin ve veri kümesinin nasıl oluşturulacağını öğrenmek için bir veri deposuna tıklayın.

Örneğin, ayrılmış bir metin veri kümesi için, veri kümesi türü, aşağıdaki JSON örneğinde gösterildiği gibi, **Delimitedtext** olarak ayarlanır:

```json
{
    "name": "DelimitedTextInput",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorage",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "input.log",
                "folderPath": "inputdata",
                "container": "adfgetstarted"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

## <a name="create-datasets"></a>Veri kümeleri oluşturma
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
