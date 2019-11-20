---
title: Data Factory kullanarak Cassandra 'dan veri taşıma
description: Azure Data Factory kullanarak şirket içi Cassandra veritabanından veri taşıma hakkında bilgi edinin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 085cc312-42ca-4f43-aa35-535b35a102d5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4edd4d663e02601a97474c5d3a54adaa6b7fd27d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682439"
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Azure Data Factory kullanarak şirket içi Cassandra veritabanından veri taşıma
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](data-factory-onprem-cassandra-connector.md)
> * [Sürüm 2 (geçerli sürüm)](../connector-cassandra.md)

> [!NOTE]
> Bu makale, Data Factory’nin 1. sürümü için geçerlidir. Data Factory hizmetinin geçerli sürümünü kullanıyorsanız, [v2 'de Cassandra Bağlayıcısı](../connector-cassandra.md)' na bakın.

Bu makalede, verileri şirket içi Cassandra veritabanından taşımak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı açıklanmaktadır. Kopyalama etkinliğiyle veri hareketine genel bir bakış sunan [veri taşıma etkinlikleri](data-factory-data-movement-activities.md) makalesinde oluşturulur.

Şirket içi Cassandra veri deposundaki verileri desteklenen herhangi bir havuz veri deposuna kopyalayabilirsiniz. Kopyalama etkinliği tarafından havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tablosuna bakın. Data Factory Şu anda yalnızca bir Cassandra veri deposundan diğer veri depolarına veri taşımayı destekler, ancak diğer veri depolarından verileri Cassandra veri deposuna taşımamaktadır.

## <a name="supported-versions"></a>Desteklenen sürümler
Cassandra Bağlayıcısı, Cassandra: 2. x ve 3. x 'in aşağıdaki sürümlerini destekler. Şirket içinde barındırılan Integration Runtime çalışan etkinlik için Cassandra 3. x, IR sürüm 3,7 ve üzeri sürümlerde desteklenir.

## <a name="prerequisites"></a>Ön koşullar
Azure Data Factory hizmetinin şirket içi Cassandra veritabanınıza bağlanabilmesi için, veritabanını barındıran aynı makineye veya veritabanına sahip kaynakların rekabeti önlemek için ayrı bir makineye bir Veri Yönetimi ağ geçidi yüklemelisiniz. Veri Yönetimi ağ geçidi, şirket içi veri kaynaklarını güvenli ve yönetilen bir şekilde bulut hizmetlerine bağlayan bir bileşendir. Veri Yönetimi ağ geçidi hakkındaki ayrıntılar için bkz. [veri yönetimi ağ geçidi](data-factory-data-management-gateway.md) makalesi. Verileri taşımak için bir veri işlem hattı ayarlama hakkında adım adım yönergeler için bkz. [Şirket içinden buluta veri taşıma](data-factory-move-data-between-onprem-and-cloud.md) makalesi.

Veritabanı bulutta barındırılıyorsa, örneğin bir Azure IaaS VM 'sinde, Cassandra veritabanına bağlanmak için ağ geçidini kullanmanız gerekir. Ağ geçidinin veritabanına bağlanabildiği sürece, veritabanını barındıran sanal makinede veya ayrı bir VM 'de ağ geçidine sahip olabilirsiniz.

Ağ geçidini yüklediğinizde, Cassandra veritabanına bağlanmak için kullanılan bir Microsoft Cassandra ODBC sürücüsünü otomatik olarak yükler. Bu nedenle, Cassandra veritabanından veri kopyalarken ağ geçidi makinesine herhangi bir sürücüyü el ile yüklemeniz gerekmez.

> [!NOTE]
> Bağlantı/ağ geçidi ile ilgili sorunları gidermeye yönelik ipuçları için bkz. [ağ geçidi sorunlarını giderme](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .

## <a name="getting-started"></a>Başlarken
Farklı araçlar/API 'Ler kullanarak şirket içi Cassandra veri deposundan veri taşıyan kopyalama etkinliği ile bir işlem hattı oluşturabilirsiniz.

- İşlem hattı oluşturmanın en kolay yolu **Kopyalama Sihirbazı**' nı kullanmaktır. Veri kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma hakkında hızlı bir yol için bkz. [öğretici: kopyalama Sihirbazı 'nı kullanarak işlem hattı oluşturma](data-factory-copy-data-wizard-tutorial.md) .
- İşlem hattı oluşturmak için aşağıdaki araçları da kullanabilirsiniz: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager şablonu**, **.NET API**ve **REST API**. Kopyalama etkinliğine sahip bir işlem hattı oluşturmak için adım adım yönergeler için bkz. [kopyalama etkinliği öğreticisi](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

Araçları veya API 'Leri kullanıp kullanmayacağınızı bir kaynak veri deposundan havuz veri deposuna veri taşınan bir işlem hattı oluşturmak için aşağıdaki adımları gerçekleştirirsiniz:

1. Giriş ve çıkış veri depolarını veri fabrikanıza bağlamak için **bağlı hizmetler** oluşturun.
2. Kopyalama işlemi için girdi ve çıktı verilerini temsil edecek **veri kümeleri** oluşturun.
3. Bir veri kümesini girdi olarak ve bir veri kümesini çıkış olarak alan kopyalama etkinliği ile bir işlem **hattı** oluşturun.

Sihirbazı kullandığınızda, bu Data Factory varlıkların JSON tanımları (bağlı hizmetler, veri kümeleri ve işlem hattı) sizin için otomatik olarak oluşturulur. Araçlar/API 'Leri (.NET API hariç) kullandığınızda, bu Data Factory varlıkları JSON biçimini kullanarak tanımlarsınız. Şirket içi Cassandra veri deposundan veri kopyalamak için kullanılan Data Factory varlıkları için JSON tanımlarının bulunduğu bir örnek için, bkz. [JSON örneği: Bu makalenin Cassandra 'Dan Azure Blob 'a veri kopyalama](#json-example-copy-data-from-cassandra-to-azure-blob) .

Aşağıdaki bölümler, Cassandra veri deposuna özgü Data Factory varlıkları tanımlamak için kullanılan JSON özellikleri hakkında ayrıntılı bilgi sağlar:

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri
Aşağıdaki tabloda, Cassandra bağlı hizmetine özgü JSON öğeleri için açıklama verilmiştir.

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| type |Type özelliği: **OnPremisesCassandra** olarak ayarlanmalıdır |Evet |
| Konağının |Cassandra sunucularının bir veya daha fazla IP adresi veya ana bilgisayar adı.<br/><br/>Aynı anda tüm sunuculara bağlanmak için IP adreslerinin veya ana bilgisayar adlarının virgülle ayrılmış bir listesini belirtin. |Evet |
| port |Cassandra sunucusunun istemci bağlantılarını dinlemek için kullandığı TCP bağlantı noktası. |Hayır, varsayılan değer: 9042 |
| authenticationType |Temel veya anonim |Evet |
| kullanıcı adı |Kullanıcı hesabı için Kullanıcı adını belirtin. |Evet, authenticationType temel olarak ayarlandıysa. |
| password |Kullanıcı hesabı için parola belirtin. |Evet, authenticationType temel olarak ayarlandıysa. |
| gatewayName |Şirket içi Cassandra veritabanına bağlanmak için kullanılan ağ geçidinin adı. |Evet |
| encryptedCredential |Ağ Geçidi tarafından şifrelenen kimlik bilgileri. |Hayır |

>[!NOTE]
>Şu anda Cassandra ile SSL kullanarak bağlantı desteklenmiyor.

## <a name="dataset-properties"></a>Veri kümesi özellikleri
Veri kümelerini tanımlamaya yönelik özellikler & bölümlerin tam listesi için bkz. [veri kümeleri oluşturma](data-factory-create-datasets.md) makalesi. Bir veri kümesinin yapısı, kullanılabilirliği ve İlkesi gibi bölümler, tüm veri kümesi türleri (Azure SQL, Azure blob, Azure tablosu vb.) için benzerdir.

**Typeproperties** bölümü her bir veri kümesi türü için farklıdır ve veri deposundaki verilerin konumu hakkında bilgi sağlar. **Cassandratable** türündeki veri kümesinin typeproperties bölümü aşağıdaki özelliklere sahiptir

| Özellik | Açıklama | Gerekli |
| --- | --- | --- |
| anahtar alanı |Cassandra veritabanında anahtar alanının veya şemanın adı. |Evet ( **Cassandrasource** **sorgusu** tanımlanmamışsa). |
| tableName |Cassandra veritabanındaki tablonun adı. |Evet ( **Cassandrasource** **sorgusu** tanımlanmamışsa). |

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri
Etkinlikleri tanımlamaya yönelik bölüm & özelliklerinin tam listesi için, işlem [hatları oluşturma](data-factory-create-pipelines.md) makalesine bakın. Ad, açıklama, giriş ve çıkış tabloları ve ilke gibi özellikler, tüm etkinlik türleri için kullanılabilir.

Ancak, etkinliğin typeProperties bölümünde kullanılabilen özellikler her etkinlik türüyle farklılık gösterir. Kopyalama etkinliği için, kaynak ve havuz türlerine göre farklılık gösterir.

Kaynak **Cassandrasource**türünde olduğunda, typeproperties bölümünde aşağıdaki özellikler mevcuttur:

| Özellik | Açıklama | İzin verilen değerler | Gerekli |
| --- | --- | --- | --- |
| sorgu |Verileri okumak için özel sorguyu kullanın. |SQL-92 sorgusu veya CQL sorgusu. Bkz. [CQL başvurusu](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>SQL sorgusu kullanırken, sorgulamak istediğiniz tabloyu temsil etmek için **anahtar alanı Name. Table adını** belirtin. |Hayır (veri kümesinde TableName ve anahtar alanı tanımlanmışsa). |
| Bu düzey |Tutarlılık düzeyi, istemci uygulamasına veri döndürmeden önce bir okuma isteğine kaç yinelemenin yanıt vereceğini belirtir. Cassandra, okuma isteğini karşılamak üzere verilerin belirtilen sayıda çoğaltmasını denetler. |ONE, IKI, ÜÇ, ÇEKIRDEK, ALL, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Ayrıntılar için bkz. [veri tutarlılığını yapılandırma](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) . |Hayır. Varsayılan değer BIR. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>JSON örneği: Cassandra 'dan Azure Blob 'a veri kopyalama
Bu örnek, [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) veya [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)kullanarak bir işlem hattı oluşturmak için kullanabileceğiniz örnek JSON tanımlarını sağlar. Şirket içi Cassandra veritabanından bir Azure Blob depolama alanına nasıl veri kopyalanacağını gösterir. Ancak, veriler burada belirtilen Azure Data Factory kopyalama etkinliği kullanılarak [burada](data-factory-data-movement-activities.md#supported-data-stores-and-formats) belirtilen herhangi bir havuza kopyalanabilir.

> [!IMPORTANT]
> Bu örnek, JSON parçacıkları sağlar. Veri Fabrikası oluşturmaya yönelik adım adım yönergeler içermez. Adım adım yönergeler için bkz. [Şirket içi konumlar ve bulut makalesi arasında verileri taşıma](data-factory-move-data-between-onprem-and-cloud.md) .

Örnek, aşağıdaki Data Factory varlıklarına sahiptir:

* [OnPremisesCassandra](#linked-service-properties)türünde bağlı bir hizmet.
* [Azurestorage](data-factory-azure-blob-connector.md#linked-service-properties)türünde bağlı bir hizmet.
* [Cassandratable](#dataset-properties)türünde bir giriş [veri kümesi](data-factory-create-datasets.md) .
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)türünde bir çıkış [veri kümesi](data-factory-create-datasets.md) .
* [Cassandrasource](#copy-activity-properties) ve [Blobsink](data-factory-azure-blob-connector.md#copy-activity-properties)kullanan kopyalama etkinliğine sahip bir işlem [hattı](data-factory-create-pipelines.md) .

**Cassandra bağlı hizmeti:**

Bu örnek, **Cassandra** bağlı hizmetini kullanır. Bu bağlı hizmet tarafından desteklenen özellikler için bkz. [Cassandra Linked Service](#linked-service-properties) bölümü.

```json
{
    "name": "CassandraLinkedService",
    "properties":
    {
        "type": "OnPremisesCassandra",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "host": "mycassandraserver",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

**Azure depolama bağlı hizmeti:**

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

**Cassandra giriş veri kümesi:**

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "mykeyspace"
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

**Azure Blob çıktı veri kümesi:**

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
            "folderPath": "adfgetstarted/fromcassandra"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Cassandra kaynağına ve BLOB havuzuna sahip bir işlem hattındaki etkinliği kopyalama:**

İşlem hattı, giriş ve çıkış veri kümelerini kullanmak üzere yapılandırılmış bir kopyalama etkinliği içerir ve her saat çalışacak şekilde zamanlanır. Ardışık düzen JSON tanımında **kaynak** türü **Cassandrasource** olarak ayarlanır ve **Havuz** türü **blobsink**olarak ayarlanır.

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
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "CassandraInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"

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

### <a name="type-mapping-for-cassandra"></a>Cassandra için tür eşleme
| Cassandra türü | .NET tabanlı tür |
| --- | --- |
| ASCII |Dize |
| BIGıNT |Int64 |
| BLOB |Byte [] |
| BOOLEAN |Boole |
| DECIMAL |Kategori |
| ÇIFT |Çift |
| FLOAT |Tek |
| INET |Dize |
| INT |Int32 |
| METINLERI |Dize |
| ILIŞKIN |DateTime |
| TIMEUUıD |Guid |
| EDIN |Guid |
| VARCHAR |Dize |
| VARıNT |Kategori |

> [!NOTE]
> Koleksiyon türleri için (eşleme, küme, liste, vb.), [sanal tablo kullanarak Cassandra koleksiyon türleriyle çalışma](#work-with-collections-using-virtual-table) bölümüne bakın.
>
> Kullanıcı tanımlı türler desteklenmez.
>
> Ikili sütun ve dize sütun uzunluklarının uzunluğu 4000 ' ten büyük olamaz.
>
>

## <a name="work-with-collections-using-virtual-table"></a>Sanal tablo kullanarak koleksiyonlarla çalışma
Azure Data Factory, Cassandra veritabanınıza bağlanmak ve veri kopyalamak için yerleşik bir ODBC sürücüsü kullanır. Eşleme, küme ve liste gibi koleksiyon türleri için, sürücü verileri ilgili sanal tablolara yeniden ayırır. Özellikle, bir tablo koleksiyon sütunları içeriyorsa, sürücü aşağıdaki sanal tabloları oluşturur:

* Koleksiyon sütunları hariç gerçek tabloyla aynı verileri içeren bir **temel tablo**. Temel tablo, temsil ettiği gerçek tabloyla aynı adı kullanır.
* İç içe geçmiş verileri genişleten her koleksiyon sütunu için bir **sanal tablo** . Koleksiyonları temsil eden sanal tablolar, gerçek tablonun adı, bir ayırıcı "*VT*" ve sütunun adı kullanılarak adlandırılır.

Sanal tablolar, gerçek tablodaki verilere başvurur ve bu da sürücünün, verilerin yoğun verilere erişmesine olanak tanır. Ayrıntılar için örnek bölümüne bakın. Cassandra koleksiyonlarının içeriğine, sanal tabloları sorgulayarak ve birleştirerek erişebilirsiniz.

Sanal tablolar dahil Cassandra veritabanındaki tablo listesini görüntülemek ve içindeki verileri önizlemek için [Kopyalama Sihirbazı](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) ' nı kullanabilirsiniz. Ayrıca kopyalama sihirbazında bir sorgu oluşturabilir ve sonucu görmek için doğrulayabilirsiniz.

### <a name="example"></a>Örnek
Örneğin, aşağıdaki "ExampleTable", "pk_int" adlı bir tamsayı birincil anahtar sütunu, value adlı bir metin sütunu, bir liste sütunu, bir harita sütunu ve bir küme sütunu ("StringSet" adlı) içeren bir Cassandra veritabanı tablosudur.

| pk_int | Değer | Liste | Eşleme | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"örnek değer 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"örnek değeri 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Bu tek tabloyu temsil eden sürücü birden çok sanal tablo oluşturur. Sanal tablolardaki yabancı anahtar sütunları, gerçek tablodaki birincil anahtar sütunlarına başvurur ve sanal tablo satırının hangi gerçek tablo satırına karşılık geldiğini belirtir.

İlk sanal tablo, "ExampleTable" adlı temel tablodur ve aşağıdaki tabloda gösterilmiştir. Temel tablo, bu tablodan atlanan ve diğer sanal tablolarda genişletilen koleksiyonlar hariç özgün veritabanı tablosuyla aynı verileri içerir.

| pk_int | Değer |
| --- | --- |
| 1 |"örnek değer 1" |
| 3 |"örnek değeri 3" |

Aşağıdaki tablolarda, List, Map ve StringSet sütunlarından verileri yeniden izleyen sanal tablolar gösterilmektedir. "_İndex" veya "_Key" ile biten adlara sahip sütunlar, verilerin özgün liste veya harita içindeki konumunu gösterir. "_Value" ile biten adlara sahip sütunlar, koleksiyondaki genişletilmiş verileri içerir.

#### <a name="table-exampletable_vt_list"></a>"ExampleTable_vt_List" tablosu:
| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

#### <a name="table-exampletable_vt_map"></a>"ExampleTable_vt_Map" tablosu:
| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

#### <a name="table-exampletable_vt_stringset"></a>"ExampleTable_vt_StringSet" tablosu:
| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |A |

## <a name="map-source-to-sink-columns"></a>Kaynağı havuz sütunlarına eşleyin
Kaynak veri kümesindeki sütunları havuz veri kümesindeki sütunlara eşleme hakkında bilgi edinmek için bkz. [Azure Data Factory veri kümesi sütunlarını eşleme](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>İlişkisel kaynaklardan yinelenebilir okuma
İlişkisel veri depolarından veri kopyalarken, istenmeyen sonuçları önlemek için yinelenebilirlik aklınızda bulundurun. Azure Data Factory, bir dilimi el ile yeniden çalıştırabilirsiniz. Bir hata oluştuğunda dilimin yeniden çalıştırılması için bir veri kümesi için yeniden deneme ilkesi de yapılandırabilirsiniz. Bir dilim her iki şekilde yeniden çalıştırıldığında, bir dilimin kaç kez çalıştırıldıklarından bağımsız olarak aynı verilerin okunmasını sağlayın. Bkz. [ilişkisel kaynaklardan tekrarlanabilir okuma](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Performans ve ayarlama
Veri taşıma (kopyalama etkinliği) performansını Azure Data Factory ve en iyileştirmek için çeşitli yollarla etkileyen temel faktörlerle ilgili bilgi edinmek için bkz. [etkinlik performansını kopyalama & ayarlama Kılavuzu](data-factory-copy-activity-performance.md) .
