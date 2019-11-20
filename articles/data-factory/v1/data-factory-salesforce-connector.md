---
title: Data Factory kullanarak Salesforce 'tan veri taşıma
description: Azure Data Factory kullanarak Salesforce 'tan veri taşıma hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: dbe3bfd6-fa6a-491a-9638-3a9a10d396d1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 71201efeb56ffda2dfbf82ca19b3bacb773c7c3d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73666160"
---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Azure Data Factory kullanarak Salesforce 'tan veri taşıma
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-salesforce-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-salesforce.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, bkz. [v2 'de Salesforce Bağlayıcısı](../connector-salesforce.md).

Bu makalede, Azure Data Factory 'de kopyalama etkinliğini kullanarak Salesforce 'tan verileri, [desteklenen kaynaklar ve havuzlar](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tablosunda yer alan havuz sütunu altında listelenen herhangi bir veri deposuna kopyalamak için nasıl kullanabileceğiniz özetlenmektedir. Bu makale, kopyalama etkinliği ve desteklenen veri deposu birleşimleri ile veri hareketine genel bir bakış sunan [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesine dayanır.

Azure Data Factory Şu anda yalnızca Salesforce 'tan [desteklenen havuz veri depolarına](data-factory-data-movement-activities.md#supported-data-stores-and-formats)veri taşımayı destekler, ancak diğer veri depolarından verileri Salesforce 'a taşımayı desteklemez.

## <a name="supported-versions"></a>Desteklenen sürümler
Bu bağlayıcı, aşağıdaki Salesforce sürümlerini destekler: Developer Edition, Professional Edition, Enterprise Edition veya sınırsız sürüm. Ve Salesforce üretimden, korumalı alana ve özel etki alanından kopyalamayı destekler.

## <a name="prerequisites"></a>Ön koşullar
* API izninin etkinleştirilmesi gerekir. [Nasıl yaparım? izin kümesine göre Salesforce 'TA API erişimini etkinleştirmek](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/) için bkz.
* Salesforce 'tan şirket içi veri depolarına veri kopyalamak için, şirket içi ortamınızda en az Veri Yönetimi Gateway 2,0 yüklü olmalıdır.

## <a name="salesforce-request-limits"></a>Salesforce istek sınırları
Salesforce, hem toplam API istekleri hem de eşzamanlı API istekleri için sınırlara sahiptir. Aşağıdaki noktalara dikkat edin:

- Eşzamanlı isteklerin sayısı sınırı aşarsa, kısıtlama gerçekleşir ve rastgele sorunlar görürsünüz.
- Toplam istek sayısı sınırı aşarsa, Salesforce hesabı 24 saat için engellenir.

Ayrıca, her iki senaryoda da "REQUEST_LIMIT_EXCEEDED" hatasını alabilirsiniz. Ayrıntılar için [Salesforce geliştirici sınırları](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) MAKALESINDEKI "API isteği sınırları" bölümüne bakın.

## <a name="getting-started"></a>Başlarken
Verileri Salesforce 'tan farklı araçlar/API 'Ler kullanarak taşıyan kopyalama etkinliğiyle bir işlem hattı oluşturabilirsiniz.

İşlem hattı oluşturmanın en kolay yolu **Kopyalama Sihirbazı**' nı kullanmaktır. Veri kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma hakkında hızlı bir yol için bkz. [öğretici: kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md) .

İşlem hattı oluşturmak için aşağıdaki araçları da kullanabilirsiniz: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager şablonu**, **.NET API**ve **REST API**. Kopyalama etkinliğine sahip bir işlem hattı oluşturmak için adım adım yönergeler için bkz. [kopyalama etkinliği öğreticisi](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Araçları veya API 'Leri kullanıp kullanmayacağınızı bir kaynak veri deposundan havuz veri deposuna veri taşınan bir işlem hattı oluşturmak için aşağıdaki adımları gerçekleştirirsiniz:

1. Giriş ve çıkış veri depolarını veri fabrikanıza bağlamak için **bağlı hizmetler** oluşturun.
2. Kopyalama işlemi için girdi ve çıktı verilerini temsil edecek **veri kümeleri** oluşturun.
3. Bir veri kümesini girdi olarak ve bir veri kümesini çıkış olarak alan kopyalama etkinliği ile bir işlem **hattı** oluşturun.

Sihirbazı kullandığınızda, bu Data Factory varlıkların JSON tanımları (bağlı hizmetler, veri kümeleri ve işlem hattı) sizin için otomatik olarak oluşturulur. Araçlar/API 'Leri (.NET API hariç) kullandığınızda, bu Data Factory varlıkları JSON biçimini kullanarak tanımlarsınız. Salesforce 'tan veri kopyalamak için kullanılan Data Factory varlıkları için JSON tanımlarına sahip bir örnek için, bu makalenin [JSON örneği: Salesforce 'Tan Azure Blob 'a veri kopyalama](#json-example-copy-data-from-salesforce-to-azure-blob) bölümüne bakın.

Aşağıdaki bölümler Salesforce 'a özgü Data Factory varlıkları tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılı bilgi sağlar:

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri
Aşağıdaki tabloda, Salesforce bağlantılı hizmetine özgü JSON öğelerine yönelik açıklamalar verilmiştir.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| type |Type özelliği: **Salesforce**olarak ayarlanmalıdır. |Evet |
| environmentUrl | Salesforce örneğinin URL 'sini belirtin. <br><br> -Varsayılan: "https:\//login.salesforce.com". <br> -Korumalı verileri veri kopyalamak için "https://test.salesforce.com" belirtin. <br> -Özel etki alanından veri kopyalamak için, örneğin "https://[Domain]. My. Salesforce. com" belirtin. |Hayır |
| kullanıcı adı |Kullanıcı hesabı için bir Kullanıcı adı belirtin. |Evet |
| password |Kullanıcı hesabı için bir parola belirtin. |Evet |
| securityToken |Kullanıcı hesabı için bir güvenlik belirteci belirtin. Güvenlik belirtecini sıfırlama/alma hakkında yönergeler için bkz. [güvenlik belirteci alma](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) . Genel olarak güvenlik belirteçleri hakkında daha fazla bilgi edinmek için bkz. [güvenlik ve API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Evet |

## <a name="dataset-properties"></a>Veri kümesi özellikleri
Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri oluşturma](data-factory-create-datasets.md) makalesi. Bir veri kümesinin yapısı, kullanılabilirliği ve İlkesi gibi bölümler, tüm veri kümesi türleri (Azure SQL, Azure blob, Azure tablosu vb.) için benzerdir.

**Typeproperties** bölümü her bir veri kümesi türü için farklıdır ve veri deposundaki verilerin konumu hakkında bilgi sağlar. **Relationaltable** türünde bir veri kümesinin typeproperties bölümü aşağıdaki özelliklere sahiptir:

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| tableName |Salesforce 'daki tablonun adı. |Hayır ( **Relationalsource** **sorgusu** belirtilmişse) |

> [!IMPORTANT]
> Tüm özel nesneler için API adının "__C" kısmı gereklidir.

![Data Factory-Salesforce bağlantısı-API adı](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri
Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için, işlem [hatları oluşturma](data-factory-create-pipelines.md) makalesine bakın. Ad, açıklama, giriş ve çıkış tabloları ve çeşitli ilkeler gibi özellikler, tüm etkinlik türleri için kullanılabilir.

Etkinliğin typeProperties bölümünde bulunan özellikler, diğer yandan her etkinlik türüyle farklılık gösterir. Kopyalama etkinliği için, kaynak ve havuz türlerine göre farklılık gösterir.

Kopyalama etkinliğinde, kaynak **Relationalsource** türünde olduğunda (Salesforce içeren), typeproperties bölümünde aşağıdaki özellikler mevcuttur:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sorgu |Verileri okumak için özel sorguyu kullanın. |Bir SQL-92 sorgusu veya [Salesforce nesne sorgulama dili (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) sorgusu. Örneğin: `select * from MyTable__c`. |Hayır ( **veri kümesinin** **tabloadı** belirtilmişse) |

> [!IMPORTANT]
> Tüm özel nesneler için API adının "__C" kısmı gereklidir.

![Data Factory-Salesforce bağlantısı-API adı](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>Sorgu ipuçları
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Tarih saat sütununda WHERE yan tümcesi kullanılarak veri alma
SOQL veya SQL sorgusu belirttiğinizde, tarih saat biçimi farklılığı ile ilgilenyin. Örneğin:

* **Soql örneği**: `$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)`
* **SQL örneği**:
    * **Sorgu belirtmek için kopyalama Sihirbazı 'Nı kullanma:** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`
    * **Sorgu belirtmek IÇIN JSON düzenlemesini kullanma (doğru kaçış karakteri):** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Salesforce raporundan veri alma
`{call "<report name>"}`, örneğin, sorgu belirterek Salesforce raporlarından veri alabilirsiniz. `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Silinen kayıtları Salesforce geri dönüşüm kutusu 'ndan alma
Salesforce geri dönüşüm kutusu 'ndaki geçici silinen kayıtları sorgulamak için sorgunuzda **"IsDeleted = 1"** belirtebilirsiniz. Örneğin,

* Yalnızca silinen kayıtları sorgulamak için "select * from MyTable__c **WHERE IsDeleted = 1**" belirtin
* Var olan ve silinen dahil tüm kayıtları sorgulamak için, "select * from MyTable__c **WHERE IsDeleted = 0 veya IsDeleted = 1**" belirtin

## <a name="json-example-copy-data-from-salesforce-to-azure-blob"></a>JSON örneği: Salesforce 'tan Azure Blob 'a veri kopyalama
Aşağıdaki örnek, [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) veya [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)kullanarak bir işlem hattı oluşturmak için kullanabileceğiniz örnek JSON tanımlarını sağlar. Verilerin Salesforce 'tan Azure Blob depolama alanına nasıl kopyalanacağını gösterir. Ancak, veriler burada belirtilen Azure Data Factory kopyalama etkinliği kullanılarak [burada](data-factory-data-movement-activities.md#supported-data-stores-and-formats) belirtilen herhangi bir havuza kopyalanabilir.

Senaryoyu uygulamak için oluşturmanız gereken Data Factory yapıtlar aşağıda verilmiştir. Listeyi izleyen bölümler bu adımlarla ilgili ayrıntıları sağlar.

* [Salesforce](#linked-service-properties) türünde bağlı bir hizmet
* [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties) türünde bağlı bir hizmet
* [Relationaltable](#dataset-properties) türünde bir giriş [veri kümesi](data-factory-create-datasets.md)
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) türünde bir çıkış [veri kümesi](data-factory-create-datasets.md)
* [Relationalsource](#copy-activity-properties) ve [Blobsink](data-factory-azure-blob-connector.md#copy-activity-properties) kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md)

**Salesforce bağlı hizmeti**

Bu örnek **Salesforce** bağlantılı hizmetini kullanır. Bu bağlı hizmet tarafından desteklenen özellikler için [Salesforce bağlı hizmeti](#linked-service-properties) bölümüne bakın. Güvenlik belirtecini sıfırlama/alma hakkında yönergeler için bkz. [güvenlik belirteci alma](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) .

```json
{
    "name": "SalesforceLinkedService",
    "properties":
    {
        "type": "Salesforce",
        "typeProperties":
        {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```
**Azure Storage bağlı hizmeti**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```
**Salesforce giriş veri kümesi**

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**External** to **true** olarak ayarlamak, Data Factory hizmetine veri kümesinin veri fabrikasının dışında olduğunu ve veri fabrikasındaki bir etkinlik tarafından üretilmediğini bildirir.

> [!IMPORTANT]
> Tüm özel nesneler için API adının "__C" kısmı gereklidir.

![Data Factory-Salesforce bağlantısı-API adı](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Azure Blob çıktı veri kümesi**

Veriler her saat yeni bir bloba yazılır (sıklık: saat, Aralık: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/alltypes_c"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Kopyalama etkinliği içeren işlem hattı**

İşlem hattı, giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılan kopyalama etkinliğini içerir ve her saat çalışacak şekilde zamanlanır. Ardışık düzen JSON tanımında **kaynak** türü **relationalsource**olarak ayarlanır ve **Havuz** türü **blobsink**olarak ayarlanır.

RelationalSource tarafından desteklenen özelliklerin listesi için bkz. [relationalsource türü özellikleri](#copy-activity-properties) .

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[
        {
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "SalesforceInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }
        ]
    }
}
```
> [!IMPORTANT]
> Tüm özel nesneler için API adının "__C" kısmı gereklidir.

![Data Factory-Salesforce bağlantısı-API adı](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)


### <a name="type-mapping-for-salesforce"></a>Salesforce için tür eşleme

| Salesforce türü | . NET tabanlı tür |
| --- | --- |
| Otomatik sayı |Dize |
| kutusunun |Boole |
| Para birimi |Kategori |
| Tarih |DateTime |
| Tarih/saat |DateTime |
| Email |Dize |
| Kimlik |Dize |
| Arama Ilişkisi |Dize |
| Çoklu seçim listesi |Dize |
| Sayı |Kategori |
| Yüzde |Kategori |
| Telefon |Dize |
| Seçim listesi |Dize |
| Metin |Dize |
| Metin alanı |Dize |
| Metin alanı (uzun) |Dize |
| Metin alanı (zengin) |Dize |
| Metin (şifrelenmiş) |Dize |
| URL'si |Dize |

> [!NOTE]
> Kaynak veri kümesindeki sütunları havuz veri kümesinden sütunlara eşlemek için, bkz. [Azure Data Factory veri kümesi sütunlarını eşleme](data-factory-map-columns.md).

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Performans ve ayar
Azure Data Factory veri taşıma (kopyalama etkinliği) performansını etkileyen anahtar faktörleri ve bunu iyileştirmek için çeşitli yollar hakkında bilgi edinmek için [etkinlik performansını ve ayarlama kılavuzunu kopyalama](data-factory-copy-activity-performance.md) bölümüne bakın.
