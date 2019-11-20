---
title: Azure Data Factory veri kümeleri oluşturma
description: Azure Data Factory ' de, Aralık ve anchorDateTime gibi özellikleri kullanan örneklerle veri kümeleri oluşturmayı öğrenin.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 18a5e11d2341fb020fc442d2f9ce7c1d44de9d0a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682743"
---
# <a name="datasets-in-azure-data-factory"></a>Azure Data Factory veri kümeleri
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-create-datasets.md)
> * [Sürüm 2 (geçerli sürüm)](../concepts-datasets-linked-services.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [v2 'de veri kümeleri](../concepts-datasets-linked-services.md).

Bu makalede, veri kümelerinin ne olduğu, JSON biçiminde nasıl tanımlandığı ve Azure Data Factory işlem hatları 'nda nasıl kullanıldığı açıklanmaktadır. Veri kümesi JSON tanımında her bölüm (örneğin, yapı, kullanılabilirlik ve ilke) hakkında ayrıntılı bilgi sağlar. Makalede ayrıca bir veri kümesi JSON tanımında **konum**, **anchordatetime**ve **Style** özelliklerinin kullanılmasına yönelik örnekler de verilmektedir.

> [!NOTE]
> Data Factory yeni bir deyişle, genel bakış için bkz. [Azure Data Factory giriş](data-factory-introduction.md) . Veri fabrikaları oluşturmaya yönelik uygulamalı deneyimle karşılaşdıysanız, [veri dönüştürme öğreticisini](data-factory-build-your-first-pipeline.md) ve [veri taşıma öğreticisini](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)okuyarak daha iyi bir anlayışınız elde edebilirsiniz.

## <a name="overview"></a>Genel Bakış
Bir veri fabrikasında bir veya daha fazla işlem hattı olabilir. İşlem **hattı** , birlikte bir görevi gerçekleştiren **etkinliklerin** mantıksal gruplandırmasıdır. Bir işlem hattındaki etkinlikler, verilerinizde gerçekleştirilecek eylemleri tanımlar. Örneğin, verileri şirket içi SQL Server Azure Blob depolamaya kopyalamak için bir kopyalama etkinliği kullanabilirsiniz. Daha sonra, çıktı verileri üretmek üzere blob depolamadan veri işlemek için bir Azure HDInsight kümesinde Hive betiği çalıştıran bir Hive etkinliği kullanabilirsiniz. Son olarak, çıkış verilerini Azure SQL veri ambarı 'na kopyalamak için ikinci bir kopyalama etkinliği kullanabilirsiniz. Bu işlem, en üst kısımdaki iş zekası (BI) raporlama çözümlerini oluşturulmuştur. İşlem hatları ve etkinlikler hakkında daha fazla bilgi için bkz. [Azure Data Factory Işlem hatları ve etkinlikleri](data-factory-create-pipelines.md).

Bir etkinlik sıfır veya daha fazla giriş **veri kümesi**alabilir ve bir veya daha fazla çıkış veri kümesi oluşturabilir. Giriş veri kümesi, işlem hattındaki bir etkinliğin girişini temsil eder ve bir çıkış veri kümesi etkinliğin çıkışını temsil eder. Veri kümeleri tablolar, dosyalar, klasörler ve belgeler gibi farklı veri depolarındaki verileri tanımlar. Örneğin, bir Azure blob veri kümesi, işlem hattının verileri okuması gereken blob kapsayıcısını ve BLOB depolama klasörünü belirtir.

Bir veri kümesi oluşturmadan önce, veri deponuzu veri fabrikasına bağlamak için **bağlı bir hizmet** oluşturun. Bağlı hizmetler, dış kaynaklara bağlanmak için Data Factory’ye gereken bağlantı bilgilerini tanımlayan bağlantı dizelerine çok benzer. Veri kümeleri, SQL tabloları, dosyalar, klasörler ve belgeler gibi bağlantılı veri depolarında bulunan verileri belirler. Örneğin, Azure depolama bağlı hizmeti bir depolama hesabını veri fabrikasına bağlar. Bir Azure blob veri kümesi, blob kapsayıcısını ve işlenecek giriş bloblarını içeren klasörü temsil eder.

Örnek senaryo aşağıda verilmiştir. Blob depolamadan SQL veritabanına veri kopyalamak için, iki bağlı hizmet oluşturursunuz: Azure depolama ve Azure SQL veritabanı. Daha sonra, iki veri kümesi oluşturun: Azure blob veri kümesi (Azure Storage bağlı hizmeti 'ne başvurur) ve Azure SQL tablo veri kümesi (Azure SQL veritabanı bağlı hizmetini ifade eder). Azure depolama ve Azure SQL veritabanı bağlı hizmetleri, Data Factory çalışma zamanında, sırasıyla Azure depolama ve Azure SQL veritabanınıza bağlanmak için kullandığı bağlantı dizelerini içerir. Azure blob veri kümesi blob depoınızda giriş bloblarını içeren blob kapsayıcısını ve BLOB klasörünü belirtir. Azure SQL tablo veri kümesi, verilerin kopyalanacağı SQL veritabanınızda SQL tablosunu belirtir.

Aşağıdaki diyagramda, Data Factory içinde işlem hattı, etkinlik, veri kümesi ve bağlı hizmet arasındaki ilişkiler gösterilmektedir:

![İşlem hattı, etkinlik, veri kümesi, bağlı hizmetler arasındaki ilişki](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>JSON veri kümesi
Data Factory bir veri kümesi JSON biçiminde aşağıdaki gibi tanımlanır:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": "<boolean flag to indicate external data. only for input datasets>",
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
        "policy":
        {
        }
    }
}
```

Aşağıdaki tabloda, yukarıdaki JSON 'daki özellikler açıklanmaktadır:

| Özellik | Açıklama | Gerekli | Varsayılan |
| --- | --- | --- | --- |
| ad |Veri kümesinin adı. Bkz. adlandırma kuralları için [Azure Data Factory adlandırma kuralları](data-factory-naming-rules.md) . |Evet |NA |
| type |Veri kümesinin türü. Data Factory tarafından desteklenen türlerden birini belirtin (örneğin: AzureBlob, Azuressqltable). <br/><br/>Ayrıntılar için bkz. [veri kümesi türü](#Type). |Evet |NA |
| yapısı |Veri kümesinin şeması.<br/><br/>Ayrıntılar için bkz. [DataSet yapısı](#Structure). |Hayır |NA |
| typeProperties | Tür özellikleri her bir tür için farklıdır (örneğin: Azure blob, Azure SQL tablosu). Desteklenen türler ve özellikleri hakkında ayrıntılı bilgi için bkz. [veri kümesi türü](#Type). |Evet |NA |
| external | Bir veri kümesinin bir Data Factory işlem hattı tarafından açıkça oluşturulup oluşturulmayacağını belirten Boole bayrağı. Bir etkinliğin giriş veri kümesi geçerli işlem hattı tarafından üretildiyse, bu bayrağı true olarak ayarlayın. İşlem hattının ilk etkinliğinin giriş veri kümesi için bu bayrağı true olarak ayarlayın.  |Hayır |false |
| availability | İşlem penceresini (örneğin, saatlik veya günlük) veya veri kümesi üretiminin Dilimleme modelini tanımlar. Bir etkinlik çalıştırması tarafından tüketilen ve üretilen her veri birimi veri dilimi olarak adlandırılır. Bir çıktı veri kümesinin kullanılabilirliği günlük (sıklık-gün, Aralık-1) olarak ayarlandıysa, her gün bir dilim oluşturulur. <br/><br/>Ayrıntılar için bkz. veri kümesi kullanılabilirliği. <br/><br/>Veri kümesi Dilimleme modeliyle ilgili ayrıntılar için bkz. [zamanlama ve yürütme](data-factory-scheduling-and-execution.md) makalesi. |Evet |NA |
| ilke |Veri kümesi dilimlerinin yerine getirilmesi gereken ölçütü veya koşulu tanımlar. <br/><br/>Ayrıntılar için bkz. [DataSet ilkesi](#Policy) bölümü. |Hayır |NA |

## <a name="dataset-example"></a>Veri kümesi örneği
Aşağıdaki örnekte, veri kümesi SQL veritabanında **MyTable** adlı bir tabloyu temsil eder.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties":
        {
            "tableName": "MyTable"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Aşağıdaki noktalara dikkat edin:

* **tür** Azuressqltable olarak ayarlandı.
* **TableName** türü özelliği (Azurestabtable türüne özgü) MyTable olarak ayarlanır.
* **Linkedservicename** , BIR sonraki JSON parçacığında tanımlanan Azuressqldatabase türünde bağlı bir hizmete başvurur.
* **kullanılabilirlik sıklığı** gün olarak ayarlanır ve **Aralık** 1 olarak ayarlanır. Bu, veri kümesi diliminin günlük olarak oluşturulduğu anlamına gelir.

**Azuressqllinkedservice** şu şekilde tanımlanır:

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

Önceki JSON kod parçacığında:

* **tür** Azuressqldatabase olarak ayarlandı.
* **ConnectionString** türü ÖZELLIĞI bir SQL veritabanına bağlanmak için bilgileri belirtir.

Gördüğünüz gibi, bağlantılı hizmet bir SQL veritabanına nasıl bağlanabileceğini tanımlar. Veri kümesi, bir işlem hattındaki etkinliğin giriş ve çıkış olarak hangi tablonun kullanıldığını tanımlar.

> [!IMPORTANT]
> Bir veri kümesi işlem hattı tarafından üretilmediği sürece, **dış**olarak işaretlenmelidir. Bu ayar genellikle bir işlem hattındaki ilk etkinliğin girişleri için geçerlidir.

## <a name="Type"></a>Veri kümesi türü
Veri kümesinin türü, kullandığınız veri deposuna bağlıdır. Data Factory tarafından desteklenen veri depolarının listesi için aşağıdaki tabloya bakın. Bu veri deposu için bağlı bir hizmet ve veri kümesi oluşturmayı öğrenmek için bir veri deposuna tıklayın.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> \* İle veri depoları şirket içinde veya hizmet olarak Azure altyapısı (IaaS) olabilir. Bu veri depoları [veri yönetimi ağ geçidini](data-factory-data-management-gateway.md)yüklemenizi gerektirir.

Önceki bölümde bulunan örnekte, veri kümesinin türü **Azuressqltable**olarak ayarlanır. Benzer şekilde, bir Azure blob veri kümesi için, veri kümesinin türü, aşağıdaki JSON içinde gösterildiği gibi **AzureBlob**olarak ayarlanır:

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

## <a name="Structure"></a>Veri kümesi yapısı
**Yapı** bölümü isteğe bağlıdır. Bir ad ve sütun veri türleri koleksiyonu içeren veri kümesinin şemasını tanımlar. Kaynak olan türleri ve eşleme sütunlarını hedefe dönüştürmek için kullanılan tür bilgilerini sağlamak için yapı bölümünü kullanın. Aşağıdaki örnekte, veri kümesinde üç sütun vardır: `slicetimestamp`, `projectname`ve `pageviews`. Bunlar sırasıyla String, String ve Decimal türündedir.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Yapıdaki her sütun aşağıdaki özellikleri içerir:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| ad |Sütunun adı. |Evet |
| type |Sütunun veri türü.  |Hayır |
| ayarı |. Tür bir .NET türü olduğunda kullanılacak NET tabanlı kültür: `Datetime` veya `Datetimeoffset`. Varsayılan değer: `en-us`. |Hayır |
| formatını |Tür bir .NET türü olduğunda kullanılacak biçim dizesi: `Datetime` veya `Datetimeoffset`. |Hayır |

Aşağıdaki yönergeler, yapı bilgilerinin ne zaman ekleneceğini ve **Yapı** bölümüne nelerin ekleneceğini belirlemenize yardımcı olur.

* **Yapılandırılmış veri kaynakları için**, yalnızca kaynak sütunlarının havuz sütunlarına eşlenmesi istiyorsanız yapı bölümünü belirtin ve adları aynı değildir. Bu tür bir yapılandırılmış veri kaynağı, veri şemasını ve tür bilgilerini verilerin kendisiyle birlikte depolar. Yapılandırılmış veri kaynaklarına örnek olarak SQL Server, Oracle ve Azure tablosu verilebilir.
  
    Tür bilgileri zaten yapılandırılmış veri kaynakları için kullanılabilir olduğundan, yapı bölümünü dahil ettiğinizde tür bilgilerini içermemelidir.
* **Okuma veri kaynaklarında şema için (özel olarak BLOB depolama)** , verileri herhangi bir şema veya tür bilgisi depolamadan depolamak zorunda kalmadan depolamayı tercih edebilirsiniz. Bu tür veri kaynakları için kaynak sütunları havuz sütunlarına eşlemek istediğinizde yapıyı dahil edin. Ayrıca, veri kümesi bir kopyalama etkinliği girişi olduğunda ve kaynak veri kümesinin veri türleri havuz için yerel türlere dönüştürülebilmelidir yapı da dahil edilir.
    
    Data Factory, yapıda tür bilgilerini sağlamak için aşağıdaki değerleri destekler: **Int16, Int32, Int64, Single, Double, Decimal, Byte [], Boolean, String, Guid, DateTime, DateTimeOffset ve TimeSpan**. Bu değerler ortak dil belirtimi (CLS) ile uyumludur. NET tabanlı tür değerleri.

Data Factory, verileri bir kaynak veri deposundan havuz veri deposuna taşırken tür dönüştürmeleri otomatik olarak gerçekleştirir.

## <a name="dataset-availability"></a>Veri kümesi kullanılabilirliği
Bir veri kümesindeki **kullanılabilirlik** bölümü, veri kümesi için işleme penceresini tanımlar (örneğin, saatlik, günlük veya haftalık). Etkinlik pencereleri hakkında daha fazla bilgi için bkz. [zamanlama ve yürütme](data-factory-scheduling-and-execution.md).

Aşağıdaki kullanılabilirlik bölümü, çıkış veri kümesinin saatlik olarak oluşturulduğunu veya giriş veri kümesinin her saat için kullanılabilir olduğunu belirtir:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

İşlem hattının başlangıç ve bitiş zamanları şunlardır:

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

Çıktı veri kümesi, işlem hattı başlangıç ve bitiş zamanları içinde saatlik olarak üretilir. Bu nedenle, bu işlem hattı tarafından oluşturulan beş veri kümesi dilimi vardır (her etkinlik penceresi için bir adet (12-1, 1,-2, 2, 17:00-3, 3 ila-4, 4-5).

Aşağıdaki tabloda kullanılabilirlik bölümünde kullanabileceğiniz özellikler açıklanmaktadır:

| Özellik | Açıklama | Gerekli | Varsayılan |
| --- | --- | --- | --- |
| frequency |Veri kümesi dilimi üretiminin zaman birimini belirtir.<br/><br/><b>Desteklenen sıklık</b>: dakika, saat, gün, hafta, ay |Evet |NA |
| interval |Sıklık için bir çarpan belirtir.<br/><br/>"Sıklık x Interval", dilimin ne sıklıkta üretildiğini belirler. Örneğin, veri kümesinin saatlik olarak dilimlenebilir olması gerekiyorsa <b>Sıklık</b> değerini <b>Hour</b>ve <b>Interval</b> değerini <b>1</b>olarak ayarlarsınız.<br/><br/>**Sıklık** değerini **dakika**olarak belirtirseniz, aralığı 15 ' ten az olmayacak şekilde ayarlamanız gerektiğini unutmayın. |Evet |NA |
| Biçim |Dilimin aralığın başlangıcında veya sonunda üretilmesi gerekip gerekmediğini belirtir.<ul><li>StartOfInterval</li><li>Endofınterval</li></ul>**Sıklık** değeri **Month**olarak ayarlanmışsa ve **Style** , **endofınterval**olarak ayarlanırsa, dilim ayın son gününde oluşturulur. **Stil** **StartOfInterval**olarak ayarlandıysa, dilim ayın ilk gününde oluşturulur.<br/><br/>**Sıklık** , **gün**olarak ayarlanmışsa ve **Stil** **endofınterval**olarak ayarlandıysa, dilim günün son saati içinde oluşturulur.<br/><br/>**Sıklık** değeri **Hour**olarak ayarlanmışsa ve **Style** , **endofınterval**olarak ayarlanırsa, dilim saatin sonunda üretilir. Örneğin, 1 PM-2 dönemi için bir dilim için dilim 2 PM 'de oluşturulur. |Hayır |Endofınterval |
| anchorDateTime |Zamanlayıcı tarafından veri kümesi dilim sınırlarını hesaplamak için kullanılan mutlak konumu tanımlar. <br/><br/>Bu özellikte belirtilen sıklığından daha ayrıntılı olan tarih bölümleri varsa, daha ayrıntılı parçalar yok sayılır. Örneğin, **Aralık** **saatlik** (sıklık: Hour ve Interval: 1) ve **anchordatetime** değeri **dakika ve saniye**içeriyorsa, **anchordatetime** değerinin dakika ve saniye kısımları göz ardı edilir. |Hayır |01/01/0001 |
| konumu |Tüm veri kümesi dilimlerinin başlangıcını ve bitişini kaydırılan zaman aralığı. <br/><br/>Hem **Anchordatetime** hem de **Kaydır** belirtilirse, sonucun Birleşik vardiya olduğunu unutmayın. |Hayır |NA |

### <a name="offset-example"></a>fark örneği
Varsayılan olarak, günlük (`"frequency": "Day", "interval": 1`) dilimleri 12: (gece yarısı) Eşgüdümlü Evrensel Saat (UTC) ile başlar. Başlangıç saatinin 6 ' dan UTC zamanı olmasını istiyorsanız, sapmayı aşağıdaki kod parçacığında gösterildiği gibi ayarlayın:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime örneği
Aşağıdaki örnekte, veri kümesi her 23 saatte bir oluşturulur. İlk dilim, `2017-04-19T08:00:00` (UTC) olarak ayarlanan **Anchordatetime**tarafından belirtilen saatte başlar.

```json
"availability":
{
    "frequency": "Hour",
    "interval": 23,
    "anchorDateTime":"2017-04-19T08:00:00"
}
```

### <a name="offsetstyle-example"></a>fark/stil örneği
Aşağıdaki veri kümesi ayda bir ve 8:00 (`3.08:00:00`) adresindeki her ayın 3. gününde üretilir:

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="Policy"></a>Veri kümesi ilkesi
Veri kümesi tanımındaki **ilke** bölümü ölçütü veya veri kümesi dilimlerinin yerine getirilmesi gereken koşulu tanımlar.

### <a name="validation-policies"></a>Doğrulama ilkeleri
| İlke adı | Açıklama | Uygulanan | Gerekli | Varsayılan |
| --- | --- | --- | --- | --- |
| minimumSizeMB |**Azure Blob depolama** alanındaki verilerin en düşük boyut gereksinimlerini (megabayt cinsinden) karşıladığını doğrular. |Azure Blob depolama |Hayır |NA |
| minimumRows |Bir **Azure SQL veritabanındaki** veya bir **Azure tablosundaki** verilerin en az sayıda satırı içerdiğini doğrular. |<ul><li>Azure SQL veritabanı</li><li>Azure tablosu</li></ul> |Hayır |NA |

#### <a name="examples"></a>Örnekler
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows:**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

### <a name="external-datasets"></a>Dış veri kümeleri
Dış veri kümeleri, veri fabrikasında çalışan bir işlem hattı tarafından üretilmeyen alanlardır. Veri kümesi **dış**olarak Işaretlenmişse, **externaldata** ilkesi, veri kümesi dilimi kullanılabilirliğinin davranışını etkilemek için tanımlanabilir.

Bir veri kümesi Data Factory tarafından üretilmediği sürece, **dış**olarak işaretlenmelidir. Bu ayar genellikle, etkinlik veya işlem hattı zinciri kullanılmadığı takdirde bir işlem hattının ilk etkinliğinin girişleri için geçerlidir.

| Ad | Açıklama | Gerekli | Varsayılan değer |
| --- | --- | --- | --- |
| dataDelay |Belirli bir dilim için dış verilerin kullanılabilirliğine ilişkin denetim gecikme süresi. Örneğin, bu ayarı kullanarak saatlik bir denetimi erteleyebilirsiniz.<br/><br/>Bu ayar yalnızca geçerli süre için geçerlidir. Örneğin, şu anda 1:00 PM ise ve bu değer 10 dakikadır, doğrulama 1:10 PM 'de başlar.<br/><br/>Bu ayarın geçmişte dilimleri etkilemediğini unutmayın. **Dilim bitiş zamanı** olan dilimler + **datadelay** < **artık** herhangi bir gecikme olmadan işlenir.<br/><br/>`day.hours:minutes:seconds` biçimi kullanılarak 23:59 saatten büyük süreler belirtilmelidir. Örneğin, 24 saat belirtmek için 24:00:00 kullanmayın. Bunun yerine 1.00:00:00 kullanın. 24:00:00 kullanıyorsanız, 24 gün (24.00:00:00) olarak kabul edilir. 1 gün ve 4 saat için 1:04:00:00 belirtin. |Hayır |0 |
| RetryInterval |Bir hata ve sonraki girişim arasındaki bekleme süresi. Bu ayar, geçerli süre için geçerlidir. Önceki deneme başarısız olursa, sonraki deneme, **RetryInterval** süresinden sonra olur. <br/><br/>Şu an 1:00 PM ise ilk denemeye başladık. İlk doğrulama denetiminin tamamlanma süresi 1 dakikadır ve işlem başarısız olduysa, sonraki yeniden deneme 1:00 + 1dk (süre) + 1dk (yeniden deneme aralığı) = 1:02 PM olur. <br/><br/>Geçmişte dilimlerde bir gecikme yoktur. Yeniden deneme anında gerçekleşir. |Hayır |00:01:00 (1 dakika) |
| retryTimeout |Her yeniden deneme girişimi için zaman aşımı.<br/><br/>Bu özellik 10 dakikaya ayarlanırsa, doğrulamanın 10 dakika içinde tamamlanması gerekir. Doğrulama işlemini gerçekleştirmek için 10 dakikadan uzun sürerse, yeniden deneme zaman aşımına uğrar.<br/><br/>Doğrulama denemeleri tüm denemeler zaman aşımına uğradı, dilim zaman **aşımı olarak işaretlenir**. |Hayır |00:10:00 (10 dakika) |
| maximumRetry |Dış verilerin kullanılabilirliğine yönelik denetlenecek zaman sayısı. İzin verilen en büyük değer 10 ' dur. |Hayır |3 |


## <a name="create-datasets"></a>Veri kümeleri oluşturun
Şu araçlardan veya SDK 'Lardan birini kullanarak veri kümeleri oluşturabilirsiniz:

- Kopyalama Sihirbazı
- Visual Studio
- PowerShell
- Azure Resource Manager şablonu
- REST API
- .NET API’si

Şu araçlardan veya SDK 'Lardan birini kullanarak işlem hatları ve veri kümeleri oluşturmaya yönelik adım adım yönergeler için aşağıdaki öğreticilere bakın:

- [Veri dönüştürme etkinliğine sahip işlem hattı oluşturma](data-factory-build-your-first-pipeline.md)
- [Veri taşıma etkinliği ile işlem hattı oluşturma](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Bir işlem hattı oluşturulup dağıtıldıktan sonra, Azure portal Blade veya Izleme ve yönetim uygulamasını kullanarak işlem hatlarınızı yönetebilir ve izleyebilirsiniz. Adım adım yönergeler için aşağıdaki konulara bakın:

- [Azure portal Blade kullanarak işlem hatlarını izleme ve yönetme](data-factory-monitor-manage-pipelines.md)
- [Izleme ve yönetim uygulamasını kullanarak işlem hatlarını izleme ve yönetme](data-factory-monitor-manage-app.md)

## <a name="scoped-datasets"></a>Kapsamlı veri kümeleri
**Veri kümeleri** özelliğini kullanarak bir işlem hattı kapsamındaki veri kümeleri oluşturabilirsiniz. Bu veri kümeleri, diğer işlem hatlarında etkinliklere göre değil, yalnızca bu işlem hattı içindeki etkinlikler tarafından kullanılabilir. Aşağıdaki örnek, işlem hattı içinde kullanılacak iki veri kümesi (ınputdataset-RDC ve OutputDataset-RDC) ile bir işlem hattını tanımlar.

> [!IMPORTANT]
> Kapsamlı veri kümeleri yalnızca bir kerelik işlem hatları ile desteklenir ( **PipelineMode** değeri **Onetime**olarak ayarlanır). Ayrıntılar için bkz. [Onetime ardışık düzeni](data-factory-create-pipelines.md#onetime-pipeline) .
>
>

```json
{
    "name": "CopyPipeline-rdc",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-rdc"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-rdc"
                    }
                ],
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1,
                    "style": "StartOfInterval"
                },
                "name": "CopyActivity-0"
            }
        ],
        "start": "2016-02-28T00:00:00Z",
        "end": "2016-02-28T00:00:00Z",
        "isPaused": false,
        "pipelineMode": "OneTime",
        "expirationTime": "15.00:00:00",
        "datasets": [
            {
                "name": "InputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "InputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/input",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": true,
                    "policy": {}
                }
            },
            {
                "name": "OutputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "OutputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/output",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": false,
                    "policy": {}
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar
- İşlem hatları hakkında daha fazla bilgi için bkz. işlem [hatları oluşturma](data-factory-create-pipelines.md).
- İşlem hatları zamanlandığında ve yürütüldüğü hakkında daha fazla bilgi için, bkz. [Azure Data Factory zamanlama ve yürütme](data-factory-scheduling-and-execution.md).
