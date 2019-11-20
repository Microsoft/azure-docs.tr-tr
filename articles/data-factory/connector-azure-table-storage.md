---
title: Data Factory kullanarak Azure Tablo depolamadan veri kopyalama
description: Desteklenen kaynak depolardan Azure Tablo depolama 'ya veya tablo depolamadan Data Factory kullanarak verileri desteklenen havuz depolarına kopyalamayı öğrenin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/27/2019
ms.author: jingwang
ms.openlocfilehash: 9960c8cba2f1b9eb8c427163d02ecb337ef5ddfe
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681132"
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Azure Data Factory kullanarak Azure Tablo depolamadan veri kopyalama
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-azure-table-connector.md)
> * [Geçerli sürüm](connector-azure-table-storage.md)

Bu makalede, Azure Tablo depolamadan veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir. Kopyalama etkinliğine genel bir bakış sunan [kopyalama etkinliğine genel bakış](copy-activity-overview.md) makalesinde oluşturulur.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-capabilities"></a>Desteklenen yetenekler

Bu Azure Tablo depolama Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)

Desteklenen herhangi bir kaynak veri deposundan tablo depolamaya veri kopyalayabilirsiniz. Ayrıca, tablo depolamadan desteklenen herhangi bir havuz veri deposuna veri kopyalayabilirsiniz. Kopyalama etkinliği tarafından kaynak veya havuz olarak desteklenen veri depolarının listesi için [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats) tablosuna bakın.

Özellikle, bu Azure Tablo Bağlayıcısı, hesap anahtarı ve hizmet paylaşılan erişim imzası kimlik doğrulamaları kullanılarak verilerin kopyalanmasını destekler.

## <a name="get-started"></a>Başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümler tablo depolamaya özgü Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgi sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

### <a name="use-an-account-key"></a>Hesap anahtarı kullan

Hesap anahtarını kullanarak bir Azure depolama bağlı hizmeti oluşturabilirsiniz. Veri fabrikasına depolama için genel erişim sağlar. Aşağıdaki özellikler desteklenir.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Type özelliği **AzureTableStorage**olarak ayarlanmalıdır. |Evet |
| connectionString | ConnectionString özelliği için depolamaya bağlanmak için gereken bilgileri belirtin. <br/>Bu alanı, Data Factory güvenli bir şekilde depolamak için SecureString olarak işaretleyin. Ayrıca hesap anahtarını Azure Key Vault yerleştirebilir ve `accountKey` yapılandırmasını bağlantı dizesinden dışarı çekebilirsiniz. Daha ayrıntılı bilgi için aşağıdaki örneklere bakın ve [kimlik bilgilerini Azure Key Vault makalesine depolayın](store-credentials-in-key-vault.md) . |Evet |
| connectVia | Veri deposuna bağlanmak için kullanılacak [tümleştirme çalışma zamanı](concepts-integration-runtime.md) . Azure Integration Runtime veya şirket içinde barındırılan Integration Runtime (veri depolduğunuz özel bir ağda yer alıyorsa) kullanabilirsiniz. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. |Hayır |

>[!NOTE]
>"AzureStorage" türünde bağlı hizmet kullanıyorsanız, bu yeni "AzureTableStorage" bağlı hizmet türünü ileride kullanmanız önerilirken olduğu gibi hala desteklenmektedir.

**Örnek:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Örnek: Azure Key Vault hesap anahtarını depolayın**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;"
            },
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-shared-access-signature-authentication"></a>Paylaşılan erişim imzası kimlik doğrulamasını kullanma

Ayrıca, paylaşılan erişim imzası kullanarak bir depolama bağlı hizmeti oluşturabilirsiniz. Veri fabrikasını, depolama alanındaki tüm/belirli kaynaklara kısıtlı/zamana göre erişim ile sağlar.

Paylaşılan erişim imzası, Depolama hesabınızdaki kaynaklara temsilci erişimi sağlar. Bu ayarı, belirli bir süre ve belirli bir izin kümesiyle Depolama hesabınızdaki nesnelere sınırlı bir izin vermek için kullanabilirsiniz. Hesap erişim anahtarlarınızı paylaşmak zorunda değilsiniz. Paylaşılan erişim imzası, bir depolama kaynağına kimliği doğrulanmış erişim için gereken tüm bilgileri sorgu parametrelerinde kapsayan bir URI 'dir. Paylaşılan erişim imzasıyla depolama kaynaklarına erişmek için, istemcinin yalnızca paylaşılan erişim imzasını uygun oluşturucuya veya yönteme geçirmesi gerekir. Paylaşılan erişim imzaları hakkında daha fazla bilgi için bkz. paylaşılan erişim [imzaları: paylaşılan erişim imzası modelini anlama](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Data Factory artık **hizmet paylaşılan erişim imzalarını** ve **Hesap paylaşılan erişim imzalarını**desteklemektedir. Paylaşılan erişim imzaları hakkında daha fazla bilgi için bkz. [paylaşılan erişim imzaları (SAS) kullanarak Azure depolama kaynaklarına sınırlı erişim verme](../storage/common/storage-sas-overview.md). 

> [!TIP]
> Depolama hesabınız için bir hizmet paylaşılan erişim imzası oluşturmak için aşağıdaki PowerShell komutlarını çalıştırabilirsiniz. Yer tutucuları değiştirin ve gerekli izinleri verin.
> `$context = New-AzStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Paylaşılan erişim imzası kimlik doğrulamasını kullanmak için aşağıdaki özellikler desteklenir.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Type özelliği **AzureTableStorage**olarak ayarlanmalıdır. |Evet |
| sasUri | Tabloya paylaşılan erişim imzası URI 'sinin SAS URI 'sini belirtin. <br/>Bu alanı, Data Factory güvenli bir şekilde depolamak için SecureString olarak işaretleyin. Ayrıca, otomatik dönüşten yararlanmak ve belirteç bölümünü kaldırmak için Azure Key Vault SAS belirtecini de koyabilirsiniz. Daha ayrıntılı bilgi için aşağıdaki örneklere bakın ve [kimlik bilgilerini Azure Key Vault makalesine depolayın](store-credentials-in-key-vault.md) . | Evet |
| connectVia | Veri deposuna bağlanmak için kullanılacak [tümleştirme çalışma zamanı](concepts-integration-runtime.md) . Azure Integration Runtime veya şirket içinde barındırılan Integration Runtime (veri depolduğunuz özel bir ağda yer alıyorsa) kullanabilirsiniz. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. |Hayır |

>[!NOTE]
>"AzureStorage" türünde bağlı hizmet kullanıyorsanız, bu yeni "AzureTableStorage" bağlı hizmet türünü ileride kullanmanız önerilirken olduğu gibi hala desteklenmektedir.

**Örnek:**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource e.g. https://<account>.table.core.windows.net/<table>?sv=<storage version>&amp;st=<start time>&amp;se=<expire time>&amp;sr=<resource>&amp;sp=<permissions>&amp;sip=<ip range>&amp;spr=<protocol>&amp;sig=<signature>>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Örnek: Azure Key Vault hesap anahtarını depolayın**

```json
{
    "name": "AzureTableStorageLinkedService",
    "properties": {
        "type": "AzureTableStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource without token e.g. https://<account>.table.core.windows.net/<table>>"
            },
            "sasToken": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Paylaşılan erişim imzası URI 'SI oluşturduğunuzda, aşağıdaki noktaları göz önünde bulundurun:

- Veri fabrikanızdaki bağlantılı hizmetin (okuma, yazma, okuma/yazma) nasıl kullanıldığını temel alarak nesneler üzerinde uygun okuma/yazma izinleri ayarlayın.
- **Süre sonu süresini** uygun şekilde ayarlayın. Depolama nesneleri erişiminin işlem hattının etkin döneminde dolamamasını sağlayın.
- URI, ihtiyaya göre doğru tablo düzeyinde oluşturulmalıdır.

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi. Bu bölüm, Azure Tablo veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

Verileri Azure tablosuna kopyalamak için, veri kümesinin Type özelliğini **AzureTable**olarak ayarlayın. Aşağıdaki özellikler desteklenir.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Veri kümesinin Type özelliği **AzureTable**olarak ayarlanmalıdır. |Evet |
| tableName |Bağlı hizmetin başvurduğu tablo depolama veritabanı örneğindeki tablonun adı. |Evet |

**Örnek:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Table storage linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Data Factory şema

Azure tablosu gibi şema içermeyen veri depoları için, Data Factory şemayı aşağıdaki yollarla bir şekilde algılar:

* Kopyalama etkinliğinde sütun eşlemeyi belirtirseniz, verileri almak için kaynak tarafı sütun listesini kullanın Data Factory. Bu durumda, bir satır bir sütun için değer içermiyorsa, için null değer sağlanır.
* Kopyalama etkinliğinde sütun eşlemeyi belirtmezseniz, verileri verilerdeki ilk satırı kullanarak şemayı Data Factory. Bu durumda, ilk satır tam şemayı içermiyorsa (ör. bazı sütunlarda null değer varsa), kopyalama işleminin sonucunda bazı sütunlar kaçırılacaktır.

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, Azure tablo kaynağı ve havuzu tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="azure-table-as-a-source-type"></a>Kaynak türü olarak Azure tablosu

Azure tablosundan veri kopyalamak için kopyalama etkinliğindeki kaynak türünü **AzureTableSource**olarak ayarlayın. Aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği kaynağının Type özelliği **AzureTableSource**olarak ayarlanmalıdır. |Evet |
| azureTableSourceQuery |Verileri okumak için özel tablo depolama sorgusunu kullanın. Aşağıdaki bölümdeki örneklere bakın. |Hayır |
| azureTableSourceIgnoreTableNotFound |Tablonun özel durumunun mevcut olup olmayacağını gösterir.<br/>İzin verilen değerler **true** ve **false** (varsayılan) şeklindedir. |Hayır |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery örnekleri

Azure Tablo sütunu tarih saat türünde ise:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Azure Tablo sütunu dize türünde ise:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

İşlem hattı parametresini kullanırsanız, DateTime değerini önceki örneklere göre doğru biçime atayın.

### <a name="azure-table-as-a-sink-type"></a>Havuz türü olarak Azure tablosu

Verileri Azure tablosuna kopyalamak için kopyalama etkinliğindeki havuz türünü **AzureTableSink**olarak ayarlayın. Aşağıdaki özellikler, etkinlik **havuzunu** Kopyala bölümünde desteklenir.

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| type | Kopyalama etkinliği havuzunun Type özelliği **AzureTableSink**olarak ayarlanmalıdır. |Evet |
| azureTableDefaultPartitionKeyValue |Havuz tarafından kullanılabilen varsayılan bölüm anahtarı değeri. |Hayır |
| azureTablePartitionKeyName |Değerleri bölüm anahtarları olarak kullanılan sütunun adını belirtin. Belirtilmemişse, bölüm anahtarı olarak "AzureTableDefaultPartitionKeyValue" kullanılır. |Hayır |
| azureTableRowKeyName |Sütun değerleri satır anahtarı olarak kullanılan sütunun adını belirtin. Belirtilmezse, her satır için bir GUID kullanın. |Hayır |
| azureTableInsertType |Azure tablosuna veri ekleme modu. Bu özellik, çıkış tablosunda eşleşen bölüm ve satır anahtarlarının değerlerinin değiştirilmesini veya birleştirildiğini denetler. <br/><br/>İzin verilen değerler **merge** (varsayılan) ve **Replace**. <br/><br> Bu ayar, tablo düzeyinde değil, satır düzeyinde geçerlidir. Hiçbir seçenek, girişte bulunmayan çıkış tablosundaki satırları siler. Birleştirme ve değiştirme ayarlarının nasıl çalıştığı hakkında bilgi edinmek için bkz. [varlık ekleme veya birleştirme](https://msdn.microsoft.com/library/azure/hh452241.aspx) ve [varlık ekleme veya değiştirme](https://msdn.microsoft.com/library/azure/hh452242.aspx). |Hayır |
| writeBatchSize |WriteBatchSize veya writeBatchTimeout değeri isabet edildiğinde Azure tablosuna veri ekler.<br/>İzin verilen değerler Integer (satır sayısı). |Hayır (varsayılan değer 10.000) |
| writeBatchTimeout |WriteBatchSize veya writeBatchTimeout değeri isabet edildiğinde Azure tablosuna veri ekler.<br/>İzin verilen değerler TimeSpan. Örnek olarak "00:20:00" (20 dakika) bulunur. |Hayır (varsayılan değer 90 saniyedir, depolama istemcisinin varsayılan zaman aşımı) |

**Örnek:**

```json
"activities":[
    {
        "name": "CopyToAzureTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Table output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureTableSink",
                "azureTablePartitionKeyName": "<column name>",
                "azureTableRowKeyName": "<column name>"
            }
        }
    }
]
```

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName

Hedef sütunu azureTablePartitionKeyName olarak kullanabilmeniz için önce **"Translator"** özelliğini kullanarak bir kaynak sütunu hedef sütuna eşleyin.

Aşağıdaki örnekte, DivisionID kaynak sütunu DivisionID hedef sütunuyla eşlenir:

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

"DivisionID", bölüm anahtarı olarak belirtilir.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Azure tablosu için veri türü eşlemesi

Ve Azure tablosundan verileri kopyaladığınızda, Azure Tablo veri türleri arasında, geçici veri türlerini Data Factory için aşağıdaki eşlemeler kullanılır. Kopyalama etkinliğinin kaynak şemayı ve veri türünü havuza nasıl eşlediğini öğrenmek için bkz. [şema ve veri türü eşlemeleri](copy-activity-schema-and-type-mapping.md).

Verileri Azure tablosuna taşıdığınızda, Azure tablosu [tarafından tanımlanan aşağıdaki eşlemeler](https://msdn.microsoft.com/library/azure/dd179338.aspx) Azure Tablo OData türlerinden .net türüne ve tam tersi şekilde kullanılır.

| Azure Tablo veri türü | Data Factory geçici veri türü | Ayrıntılar |
|:--- |:--- |:--- |
| EDM.Binary |Byte [] |64 KB 'a kadar olan bir bayt dizisi. |
| Edm.Boolean |bool |Boolean değeri. |
| EDM. DateTime |DateTime |Eşgüdümlü Evrensel Saat (UTC) olarak ifade edilen 64 bitlik bir değer. Desteklenen tarih saat aralığı gece yarısı başlar, 1 Ocak 1601 M.S. (C.E.), UTC. Aralık 31 Aralık 9999 ' de sona erer. |
| Edm.Double |double |64 bitlik kayan nokta değeri. |
| EDM.Guid |Guid |128 bitlik bir genel benzersiz tanımlayıcı. |
| Edm.Int32 |Int32 |32 bitlik bir tamsayı. |
| Edm.Int64 |Int64 |64 bitlik bir tamsayı. |
| Edm.String |Dize |UTF-16 kodlu bir değer. Dize değerleri 64 KB 'a kadar olabilir. |

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="next-steps"></a>Sonraki adımlar
Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).
