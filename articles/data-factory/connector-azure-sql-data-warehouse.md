---
title: Azure Data Factory kullanarak Azure SQL veri ambarı 'nda verileri kopyalama ve dönüştürme
description: Azure SQL veri ambarı 'na veri kopyalamayı ve Azure SQL veri ambarı 'nda Data Factory kullanarak verileri dönüştürmeyi öğrenin.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: jingwang
ms.openlocfilehash: 4d08a388e98283ff7bf05e938d7b8c48b7065074
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076761"
---
# <a name="copy-and-transform-data-in-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Azure Data Factory kullanarak Azure SQL veri ambarı 'nda verileri kopyalama ve dönüştürme 
> [!div class="op_single_selector" title1="Kullanmakta olduğunuz Data Factory hizmeti sürümünü seçin:"]
> * [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Geçerli sürüm](connector-azure-sql-data-warehouse.md)

Bu makalede, Azure SQL veri ambarı 'ndan veri kopyalamak için Azure Data Factory kopyalama etkinliğini kullanma ve Azure Data Lake Storage 2. verileri dönüştürmek için veri akışı kullanma açıklanmaktadır. Azure Data Factory hakkında bilgi edinmek için [giriş makalesi](introduction.md).

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu Azure SQL veri ambarı Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Etkinliği](copy-activity-overview.md) [Desteklenen kaynak/havuz matris](copy-activity-overview.md) tablosuyla Kopyala
- [Veri akışını eşleme](concepts-data-flow-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)
- [GetMetadata etkinliği](control-flow-get-metadata-activity.md)

Kopyalama etkinliği için, bu Azure SQL veri ambarı Bağlayıcısı şu işlevleri destekler:

- SQL kimlik doğrulaması ve Azure Active Directory (Azure AD) uygulama belirteci kimlik doğrulamasını Azure kaynakları için bir hizmet sorumlusu veya yönetilen kimliklerle kullanarak verileri kopyalayın.
- Bir kaynak olarak bir SQL sorgusu veya saklı yordamı kullanarak veri alın.
- Bir havuz olarak, PolyBase ya da bir toplu kullanarak yük veriler ekleyin. Daha iyi bir kopyalama performansı için Polybase'i öneririz.

> [!IMPORTANT]
> Azure Data Factory Integration Runtime'ı kullanarak verileri kopyalama, yapılandırma bir [Azure SQL sunucusu güvenlik duvarı](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) Azure hizmetlerinin sunucunun erişebilmesi için.
> Şirket içinde barındırılan Integration runtime'ı kullanarak verileri kopyalama, Azure SQL sunucusu güvenlik duvarı uygun IP aralığı izin verecek şekilde yapılandırın. Azure SQL veritabanı'na bağlanmak için kullanılan bir makinenin IP bu aralığa dahildir.

## <a name="get-started"></a>Başlarken

> [!TIP]
> En iyi performansı elde etmek için Azure SQL veri ambarı'na veri yüklemek için PolyBase kullanın. [Azure SQL veri ambarı'na veri yüklemek için PolyBase kullanma](#use-polybase-to-load-data-into-azure-sql-data-warehouse) Ayrıntılar bölümünde bulunur. Kullanım örneği ile bir kılavuz için bkz. [1 TB 15 dakikadan daha kısa Azure Data Factory ile Azure SQL Data Warehouse'a veri yükleme](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümler, Data Factory varlıklarını belirli bir Azure SQL veri ambarı Bağlayıcısı tanımlayan özellikleri hakkında ayrıntılı bilgi sağlar.

## <a name="linked-service-properties"></a>Bağlı hizmeti özellikleri

Bir Azure SQL veri ambarı bağlı hizmeti için aşağıdaki özellikleri destekler:

| Özellik            | Açıklama                                                  | Gerekli                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | Type özelliği ayarlanmalıdır **AzureSqlDW**.             | Yes                                                          |
| connectionString    | İçin Azure SQL veri ambarı örneğine bağlanmak için gereken bilgileri belirtin **connectionString** özelliği. <br/>Bu alanı, Data Factory güvenli bir şekilde depolamak için SecureString olarak işaretleyin. Ayrıca, parola/hizmet sorumlusu anahtarını Azure Key Vault de koyabilirsiniz ve SQL kimlik doğrulaması, `password` yapılandırmasını bağlantı dizesinden dışarı çeker. Daha ayrıntılı bilgi için tablonun altındaki JSON örneğine bakın ve [kimlik bilgilerini Azure Key Vault makalesine saklayın](store-credentials-in-key-vault.md) . | Yes                                                          |
| servicePrincipalId  | Uygulamanın istemci kimliği belirtin.                         | Evet, bir hizmet sorumlusu ile Azure AD kimlik doğrulaması kullandığınızda. |
| servicePrincipalKey | Uygulama anahtarını belirtin. Data Factory'de güvenle depolamak için bir SecureString olarak bu alanı işaretleyin veya [Azure Key Vault'ta depolanan bir gizli dizi başvuru](store-credentials-in-key-vault.md). | Evet, bir hizmet sorumlusu ile Azure AD kimlik doğrulaması kullandığınızda. |
| tenant              | Kiracı bilgileri (etki alanı adı veya Kiracı kimliği), uygulamanızın bulunduğu altında belirtin. Azure portalının sağ üst köşedeki fare getirerek geri alabilirsiniz. | Evet, bir hizmet sorumlusu ile Azure AD kimlik doğrulaması kullandığınızda. |
| connectVia          | [Integration runtime](concepts-integration-runtime.md) veri deposuna bağlanmak için kullanılacak. (Veri deponuz özel bir ağda yer alıyorsa) Azure Integration Runtime veya şirket içinde barındırılan tümleştirme çalışma zamanı kullanabilirsiniz. Belirtilmezse, varsayılan Azure Integration Runtime kullanır. | Hayır                                                           |

Farklı kimlik doğrulama türleri için sırasıyla önkoşulları ve JSON örnekleri aşağıdaki bölümlere bakın:

- [SQL kimlik doğrulaması](#sql-authentication)
- Azure AD uygulama belirteci kimlik doğrulamasını: [hizmet sorumlusu](#service-principal-authentication)
- Azure AD uygulama belirteci kimlik doğrulamasını: [kimliklerini Azure kaynakları için yönetilen](#managed-identity)

>[!TIP]
>Hata olarak "UserErrorFailedToConnectToSqlServer" hata koduyla isabet ve gibi ileti "veritabanı için oturum sınırı xxx ve üst sınırına ulaşıldı.", ekleme `Pooling=false` bağlantı dizesi ve yeniden deneyin.

### <a name="sql-authentication"></a>SQL kimlik doğrulaması

#### <a name="linked-service-example-that-uses-sql-authentication"></a>SQL kimlik doğrulamasını kullanan bağlı hizmet örneği

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Azure Key Vault parola:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Hizmet sorumlusu kimlik doğrulaması

Hizmet sorumlusu tabanlı Azure AD uygulama belirteci kimlik doğrulamasını kullanmak için aşağıdaki adımları izleyin:

1. **[Bir Azure Active Directory uygulaması oluşturma](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)**  Azure portalından. Uygulama adı ve bağlı hizmetini tanımlamak aşağıdaki değerleri not edin:

    - Uygulama Kimliği
    - Uygulama anahtarı
    - Kiracı Kimliği

2. **[Bir Azure Active Directory Yöneticisi sağlama](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  zaten yapmadıysanız Azure Portal'da Azure SQL sunucunuzun. Azure AD Yöneticisi, Azure AD kullanıcısı veya Azure AD grubu olabilir. Gruba yönetilen kimliğe sahip bir yönetici rolü verirseniz, 3 ve 4. adımları atlayın. Yönetici veritabanında tam erişiminiz olacaktır.

3. **[Bağımsız veritabanı kullanıcılarını oluşturun](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  hizmet sorumlusu için. Veri ambarından ya da en az bir Azure AD kimlik ile SSMS gibi araçları kullanarak verileri kopyalamak istediğiniz herhangi bir kullanıcı ALTER izni. Aşağıdaki T-SQL çalıştırın:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Hizmet sorumlusuna gerekli izinleri vermek** SQL kullanıcıları veya diğerleri için normalde yaptığınız gibi. Aşağıdaki kodu çalıştırın veya daha [fazla seçeneğe bakın](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Verileri yüklemek için PolyBase kullanmak istiyorsanız [gerekli veritabanı iznini](#required-database-permission)öğrenin.

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Bir Azure SQL veri ambarı bağlı hizmetini yapılandırma** Azure Data factory'de.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Hizmet sorumlusu kimlik doğrulamasını kullanan bağlı hizmet örneği

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Azure kaynaklarında kimlik doğrulaması için yönetilen kimlik

Veri Fabrikası ile ilişkilendirilmiş bir [yönetilen Azure kaynakları için kimliği](data-factory-service-identity.md) , belirli üretecini temsil eder. Bu yönetilen kimliği, Azure SQL veri ambarı kimlik doğrulaması için kullanabilirsiniz. Belirtilen Üreteç erişebilir ve bu kimlik kullanarak veri kopyalama ya da verilerinize ambarı.

Yönetilen kimlik kimlik doğrulamasını kullanmak için şu adımları izleyin:

1. **[Bir Azure Active Directory Yöneticisi sağlama](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)**  zaten yapmadıysanız Azure Portal'da Azure SQL sunucunuzun. Azure AD Yöneticisi, Azure AD kullanıcısı veya Azure AD grubu olabilir. Gruba yönetilen kimliğe sahip bir yönetici rolü verirseniz, 3 ve 4. adımları atlayın. Yönetici veritabanında tam erişiminiz olacaktır.

2. Data Factory yönetilen kimlik için **[Kapsanan Veritabanı kullanıcıları oluşturun](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** . Veri ambarından ya da en az bir Azure AD kimlik ile SSMS gibi araçları kullanarak verileri kopyalamak istediğiniz herhangi bir kullanıcı ALTER izni. Aşağıdaki T-SQL çalıştırın. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. SQL kullanıcıları ve diğerleri için normalde yaptığınız gibi **Data Factory yönetilen kimliğe gerekli Izinleri verin** . Aşağıdaki kodu çalıştırın veya daha [fazla seçeneğe bakın](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Verileri yüklemek için PolyBase kullanmak istiyorsanız [gerekli veritabanı iznini](#required-database-permission)öğrenin.

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Bir Azure SQL veri ambarı bağlı hizmetini yapılandırma** Azure Data factory'de.

**Örnek:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Bölümleri ve veri kümeleri tanımlamak için mevcut özelliklerin tam listesi için bkz: [veri kümeleri](concepts-datasets-linked-services.md) makalesi. 

Azure SQL veri ambarı veri kümesi için aşağıdaki özellikler desteklenir:

| Özellik  | Açıklama                                                  | Gerekli                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | **type** kümesinin özelliği ayarlanmalıdır **AzureSqlDWTable**. | Yes                         |
| schema | Şemanın adı. |Kaynak, havuz için Evet Hayır  |
| table | Tablo/görünüm adı. |Kaynak, havuz için Evet Hayır  |
| tableName | Şema ile tablonun/görünümün adı. Bu özellik geriye dönük uyumluluk için desteklenir. Yeni iş yükü için `schema` ve `table`kullanın. | Kaynak, havuz için Evet Hayır |

#### <a name="dataset-properties-example"></a>Veri kümesi özellikleri örneği

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopyalama etkinliğinin özellikleri

Bölümleri ve etkinlikleri tanımlamak için mevcut özelliklerin tam listesi için bkz: [işlem hatları](concepts-pipelines-activities.md) makalesi. Bu bölümde, Azure SQL veri ambarı kaynak ve havuz desteklenen özelliklerin bir listesini sağlar.

### <a name="azure-sql-data-warehouse-as-the-source"></a>Kaynağı olarak Azure SQL veri ambarı

Verileri Azure SQL Data Warehouse'dan veri kopyalamak için ayarlanmış **türü** kopyalama etkinliği kaynak özelliğinde **SqlDWSource**. Kopyalama etkinliği aşağıdaki özellikler desteklenir **kaynak** bölümü:

| Özellik                     | Açıklama                                                  | Gerekli |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | **type** kopyalama etkinliği kaynak özelliği ayarlanmalıdır **SqlDWSource**. | Yes      |
| sqlReaderQuery               | Verileri okumak için özel bir SQL sorgusu kullanın. Örnek: `select * from MyTable`. | Hayır       |
| sqlReaderStoredProcedureName | Kaynak tablo verilerini okuyan saklı yordamın adı. Son SQL deyim bir SELECT deyimi saklı yordam içinde olmalıdır. | Hayır       |
| storedProcedureParameters    | Saklı yordamın parametreleri.<br/>İzin verilen değerler, ad veya değer çiftleridir. Adları ve parametreleri büyük küçük harfleri, adları ve saklı yordam parametreleri büyük küçük harfleri eşleşmelidir. | Hayır       |

### <a name="points-to-note"></a>Dikkat edilecek noktaları

- Varsa **sqlReaderQuery** için belirtilen **SqlSource**, kopyalama etkinliği, verileri almak için Azure SQL veri ambarı kaynağına karşı bu sorgu çalıştırır. Veya bir saklı yordam belirtebilirsiniz. Belirtin **sqlReaderStoredProcedureName** ve **storedProcedureParameters** saklı yordamın kullandığı parametreler varsa.
- Ya da belirtmezseniz **sqlReaderQuery** veya **sqlReaderStoredProcedureName**, tanımlanan sütunları **yapısı** JSON veri kümesi bölümünü alışkın olduğunuz bir sorgu oluşturun. `select column1, column2 from mytable` Azure SQL veri ambarı karşı çalışır. Veri kümesi tanımı yoksa **yapısı**, tablodan tüm sütun seçilmedi.

#### <a name="sql-query-example"></a>SQL sorgusu örneği

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>Saklı yordam örneği

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlDWSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="stored-procedure-definition"></a>Saklı yordam tanımında

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-data-warehouse-as-sink"></a> Havuz olarak Azure SQL veri ambarı

Azure SQL veri ambarı'na veri kopyalamak için kopyalama etkinliği Havuz türü ayarlayın. **SqlDWSink**. Kopyalama etkinliği aşağıdaki özellikler desteklenir **havuz** bölümü:

| Özellik          | Açıklama                                                  | Gerekli                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | **type** kopyalama etkinliği havuz özelliği ayarlanmalıdır **SqlDWSink**. | Yes                                           |
| allowPolyBase     | PolyBase, uygun olduğunda yerine BULKINSERT mekanizması kullanılıp kullanılmayacağını belirtir. <br/><br/> PolyBase kullanarak SQL Data Warehouse'a veri yükleme öneririz. Bkz: [Azure SQL veri ambarı'na veri yüklemek için PolyBase kullanma](#use-polybase-to-load-data-into-azure-sql-data-warehouse) kısıtlamaları ve ayrıntıları bölümü.<br/><br/>İzin verilen değerler **True** ve **False** (varsayılan). | Hayır                                            |
| polyBaseSettings  | Bir grup olabilir özellik belirtilen **Bulunan'allowpolybase** özelliği **true**. | Hayır                                            |
| rejectValue       | Sayı veya sorgu başarısız olmadan önce reddedilemiyor satırları yüzdesini belirtir.<br/><br/>Bağımsız değişkenler bölümünde PolyBase'nın reddetme seçenekleri hakkında daha fazla bilgi [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>İzin verilen değerler: 0 (varsayılan), 1, 2, vs. | Hayır                                            |
| rejectType        | Belirtir olup olmadığını **rejectValue** seçenektir değişmez değer veya bir yüzdesi.<br/><br/>İzin verilen değerler **değer** (varsayılan) ve **yüzdesi**. | Hayır                                            |
| rejectSampleValue | Reddedilen satırların yüzdesi PolyBase yeniden hesaplar önce almak için satır sayısını belirler.<br/><br/>İzin verilen değerler: 1, 2, vs. | Evet, varsa **rejectType** olduğu **yüzdesi**. |
| useTypeDefault    | PolyBase metin dosyasından veri aldığında sınırlandırılmış metin dosyaları eksik değerleri nasıl ele alınacağını belirtir.<br/><br/>Bağımsız değişkenler bölümünden bu özellik hakkında daha fazla bilgi [oluşturma EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>İzin verilen değerler **True** ve **False** (varsayılan).<br><br> | Hayır                                            |
| writeBatchSize    | **Toplu iş BAŞıNA**SQL tablosuna eklenecek satır sayısı. Yalnızca PolyBase ne zaman kullanılmaz geçerlidir.<br/><br/>İzin verilen değer **tamsayı** (satır sayısı). Varsayılan olarak, Data Factory satır boyutuna göre uygun toplu iş boyutunu dinamik olarak belirler. | Hayır                                            |
| writeBatchTimeout | Toplu ekleme işleminin, zaman aşımına uğramadan önce tamamlaması için bekleme süresi. Yalnızca PolyBase kullanılmazsa geçerlidir.<br/><br/>İzin verilen değer **timespan**. Örnek: "00: 30:00" (30 dakika). | Hayır                                            |
| preCopyScript     | Her bir çalıştırmada Azure SQL Data Warehouse'a veri yazılmadan önce çalıştırmak kopyalama etkinliği için bir SQL sorgusunu belirtin. Önceden yüklenmiş ve verileri temizlemek için bu özelliği kullanın. | Hayır                                            |
| tableOption | Kaynak şemasına göre yoksa havuz tablosunun otomatik olarak oluşturulup oluşturulmayacağını belirtir. Hazırlanan kopya kopyalama etkinliğinde yapılandırıldığında otomatik tablo oluşturma desteklenmez. İzin verilen değerler: `none` (varsayılan), `autoCreate`. |Hayır |
| disableMetricsCollection | Data Factory, kopyalama performansı iyileştirmesi ve önerileri için SQL veri ambarı DWUs gibi ölçümleri toplar. Bu davranışla ilgileniyorlarsa, kapatmak için `true` belirtin. | Hayır (varsayılan `false`) |

#### <a name="sql-data-warehouse-sink-example"></a>SQL veri ambarı havuzu örnek

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

SQL veri ambarı'nda bir sonraki bölüm verimli bir şekilde yüklemek için PolyBase kullanma hakkında daha fazla bilgi edinin.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Azure SQL veri ambarı'na veri yüklemek için PolyBase kullanma

Kullanarak [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) büyük miktarda veri, yüksek aktarım hızı ile Azure SQL Data Warehouse'a yüklemek için etkili bir yoludur. Yerine varsayılan BULKINSERT mekanizmasını PolyBase kullanarak büyük bir kazanç aktarım hızının görürsünüz. Kullanım örneği ile bir kılavuz için bkz. [1 TB'ı Azure SQL Data Warehouse'a veri yükleme](v1/data-factory-load-sql-data-warehouse.md).

* Kaynak verileriniz Azure Blob 'dayken **, Azure Data Lake Storage 1. veya Azure Data Lake Storage 2.** ve **Biçim PolyBase UYUMLUYSA**, Azure SQL veri ambarı 'nın verileri kaynaktan çekmesini sağlamak üzere doğrudan PolyBase 'i çağırmak için kopyalama etkinliğini kullanabilirsiniz. Ayrıntılar için bkz  **[kopyalama PolyBase kullanarak doğrudan](#direct-copy-by-using-polybase)** .
* Kaynak veri deposu ve biçim başlangıçta desteklenmez, PolyBase tarafından kullanın **[kopyalama PolyBase kullanarak hazırlanmış](#staged-copy-by-using-polybase)** bunun yerine özellik. Hazırlanmış kopya özelliğini de daha iyi aktarım hızı sağlar. Otomatik olarak verileri PolyBase ile uyumlu biçimine dönüştürür. Ve verileri Azure Blob Depolama alanında depolar. Ardından verileri SQL veri ambarı'na yükler.

>[!TIP]
>[PolyBase 'i kullanmaya yönelik en iyi uygulamalar](#best-practices-for-using-polybase)hakkında daha fazla bilgi edinin.

### <a name="direct-copy-by-using-polybase"></a>PolyBase kullanarak doğrudan Kopyala

SQL veri ambarı PolyBase doğrudan Azure blob, Azure Data Lake Storage 1. ve Azure Data Lake Storage 2. destekler. Kaynak verileriniz bu bölümde açıklanan ölçütleri karşılıyorsa, doğrudan kaynak veri deposundan Azure SQL veri ambarı 'na kopyalamak için PolyBase kullanın. Aksi takdirde kullanın [kopyalama PolyBase kullanarak hazırlanmış](#staged-copy-by-using-polybase).

> [!TIP]
> Verileri SQL veri ambarı 'na verimli bir şekilde kopyalamak için Azure Data Factory 'tan daha fazla bilgi edinin, [SQL veri ambarı ile Data Lake Store kullanırken verilerin öngörülerini daha kolay ve](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/)kolay bir şekilde açığa çıkarabilir.

Gereksinimleri karşılanmadığı takdirde, Azure Data Factory ayarları denetler ve veri taşıma BULKINSERT mekanizması için otomatik olarak geri döner.

1. **Kaynak bağlı hizmeti** aşağıdaki türler ve kimlik doğrulama yöntemleriyle aynıdır:

    | Desteklenen kaynak veri deposu türü                             | Desteklenen kaynak kimlik doğrulama türü                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure blobu](connector-azure-blob-storage.md)                | Hesap anahtarı kimlik doğrulaması, yönetilen kimlik kimlik doğrulaması |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | Hizmet sorumlusu kimlik doğrulaması                            |
    | [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md) | Hesap anahtarı kimlik doğrulaması, yönetilen kimlik kimlik doğrulaması |

    >[!IMPORTANT]
    >Azure depolama alanı sanal ağ hizmeti uç noktası ile yapılandırıldıysa, yönetilen kimlik kimlik doğrulaması kullanmanız gerekir- [VNET hizmet uç noktalarını Azure depolama ile kullanmanın etkileri](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)için başvurun. [Azure Blob tarafından yönetilen kimlik doğrulama](connector-azure-blob-storage.md#managed-identity) ve [Azure Data Lake Storage 2. tarafından yönetilen kimlik doğrulama](connector-azure-data-lake-storage.md#managed-identity) bölümündeki Data Factory gereken konfigürasyonları öğrenin.

2. **Kaynak veri biçimi** , aşağıdaki yapılandırmalara **sahip Parquet**, **orc**veya **sınırlandırılmış bir metindir**:

   1. Klasör yolu joker karakter filtresi içermiyor.
   2. Dosya adı boş veya tek bir dosyaya işaret ediyor. Kopyalama etkinliğinde joker karakter dosya adı belirtirseniz, yalnızca `*` veya `*.*`olabilir.
   3. **varsayılan**, **\n**, **\r\n**veya **\r**`rowDelimiter`.
   4. `nullValue` varsayılan olarak bırakılır veya **boş dize** ("") olarak ayarlanır ve `treatEmptyAsNull` varsayılan olarak bırakılır veya true olarak ayarlanır.
   5. `encodingName` varsayılan olarak bırakılır veya **UTF-8**olarak ayarlanır.
   6. `quoteChar`, `escapeChar`ve `skipLineCount` belirtilmedi. PolyBase desteği, ADF 'de `firstRowAsHeader` olarak yapılandırılabilen üst bilgi satırını atlar.
   7. `compression` olabilir **sıkıştırma**, **GZip**, veya **Deflate**.

3. Kaynağınız bir klasörse `recursive` Copy etkinliğinin true olarak ayarlanması gerekir.

>[!NOTE]
>Kaynağınız bir klasörssa, PolyBase dosyaları klasörden ve tüm alt klasörlerinden alır ve dosya adının bir alt çizgiyle (_) veya nokta (.) ile başladığı dosyalardan veri almaz ( [burada, konum bağımsız değişkeni](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2)olarak belgelenmiştir).

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSetting",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>PolyBase kullanarak hazırlanmış Kopyala

Veri kaynağınızı önceki bölümde ölçütleri karşılamayan, veri kopyalama işlemini bir geçici hazırlama Azure Blob Depolama örneği etkinleştirin. Azure Premium depolama olamaz. Bu durumda, Azure Data Factory otomatik olarak PolyBase veri biçim gereksinimlerini karşılamak için veriler üzerinde dönüştürmeler çalıştırır. Ardından verileri SQL Data Warehouse'a veri yüklemek için PolyBase kullanır. Son olarak, blob depolama alanından, geçici verileri temizler. Bkz: [kopyalama aşamalı](copy-activity-performance.md#staged-copy) hazırlama bir Azure Blob Depolama örneği aracılığıyla veri kopyalama hakkında ayrıntılar için.

Bu özelliği kullanmak için, ara BLOB depolama alanı ile Azure depolama hesabına başvuran bir [Azure Blob depolama bağlı hizmeti](connector-azure-blob-storage.md#linked-service-properties) oluşturun. Ardından, aşağıdaki kodda gösterildiği gibi kopyalama etkinliğinin `enableStaging` ve `stagingSettings` özelliklerini belirtin.

>[!IMPORTANT]
>Hazırlama Azure depolama alanı sanal ağ hizmeti uç noktası ile yapılandırıldıysa, yönetilen kimlik kimlik doğrulaması kullanmanız gerekir- [VNET hizmet uç noktalarını Azure depolama ile kullanmanın etkileri](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)için başvurun. [Azure Blob tarafından yönetilen kimlik kimlik doğrulamasından](connector-azure-blob-storage.md#managed-identity)Data Factory, gerekli konfigürasyonları öğrenin.

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

## <a name="best-practices-for-using-polybase"></a>PolyBase kullanmak için en iyi uygulamalar

Aşağıdaki bölümlerde belirtildiği ek olarak en iyi [en iyi uygulamalar için Azure SQL veri ambarı](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Gerekli database izni

PolyBase kullanmak için SQL Data Warehouse'a veri yükleyen kullanıcının olmalıdır ["CONTROL" izni](https://msdn.microsoft.com/library/ms191291.aspx) hedef veritabanında. Elde etmenin bir yolu olan bir üye olarak kullanıcı eklemek için **db_owner** rol. Bunu yapmak öğrenin [SQL veri ambarı genel bakış](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limits"></a>Satır boyutu ve veri sınırları yazın

PolyBase yükleri satır 1 MB'tan küçük sınırlıdır. VARCHR (MAX), NVARCHAR (MAX) veya VARBINARY (MAX) ' e yüklemek için kullanılamaz. Daha fazla bilgi için [SQL veri ambarı hizmet kapasite sınırları](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Veri kaynağınızı satırları 1 MB'den büyük olduğunda, dikey olarak kaynak tablolarda birkaç küçük parçalara bölmek isteyebilirsiniz. Her satırın en büyük boyut sınırı aşmadığından emin olun. Daha küçük tablolar PolyBase kullanılarak yüklenen ve birlikte Azure SQL veri ambarı'nda birleştirildi.

Alternatif olarak, bu tür geniş sütunlara sahip veriler için, "PolyBase 'e izin ver" ayarını kapatarak ADF kullanarak verileri yüklemek için PolyBase 'i kullanabilirsiniz.

### <a name="sql-data-warehouse-resource-class"></a>SQL veri ambarı kaynak sınıfı

Olası en iyi verimi elde etmek için PolyBase aracılığıyla SQL veri ambarı'na veri yükleyen kullanıcının daha büyük bir kaynak sınıfına atayın.

### <a name="polybase-troubleshooting"></a>PolyBase sorunlarını giderme

**Ondalık sütuna yükleniyor**

Kaynak verileriniz Metin biçimindeyse veya PolyBase ile uyumlu olmayan diğer depolarda (hazırlanan kopya ve PolyBase kullanılarak) ve SQL veri ambarı ondalık sütununa yüklenecek boş bir değer içeriyorsa, şu hatayla karşılaşabilirsiniz:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

Çözüm, "**tür Varsayılanı kullan**" seçeneğinin (false olarak) kopyalama etkinliği havuzu-> PolyBase ayarları ' nda seçimini kaldırır. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)", PolyBase metin dosyasından verileri aldığında sınırlandırılmış metin dosyalarında eksik değerlerin nasıl Işleneceğini belirten PolyBase yerel bir yapılandırmadır. 

**Azure SQL veri ambarı 'nda `tableName`**

Aşağıdaki tabloda belirtmek örnekler verilmektedir **tableName** JSON veri kümesi özelliği. Bu, çeşitli birleşimlerini şema ve tablo adları gösterir.

| DB şema | Tablo adı | **tableName** JSON özelliği               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable ya da dbo. MyTable veya [dbo].[MyTable] |
| dbo1      | MyTable    | dbo1. MyTable veya [dbo1].[MyTable]          |
| dbo       | My.Table   | [My.Table] veya [dbo].[My.Table]            |
| dbo1      | My.Table   | [dbo1]. [My.Table]                         |

Aşağıdaki hatayı görürseniz, sorunu için belirtilen değer olabilir **tableName** özelliği. Doğru şekilde değerlerini belirtmek için önceki tabloya bakın **tableName** JSON özelliği.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**Varsayılan değerlere sahip sütunlar**

Şu anda, Data Factory, PolyBase özelliği yalnızca aynı sayıda sütun hedef tabloda olduğu gibi kabul eder. Bunlardan biri, varsayılan bir değerle tanımlandığı dört sütunlarını içeren bir tablo buna bir örnektir. Giriş verilerini hala dört sütun olması gerekir. Üç sütunlu giriş veri kümesi aşağıdaki iletiye benzer bir hata verir:

```
All columns of the table must be specified in the INSERT BULK statement.
```

NULL değerini varsayılan değer özel bir biçimidir. Sütun null ise, söz konusu sütun için BLOB giriş veri boş olabilir. Ancak, giriş veri kümesinden eksik olamaz. PolyBase, Azure SQL veri ambarı'nda eksik değerler için NULL ekler.

## <a name="mapping-data-flow-properties"></a>Veri akışı özelliklerini eşleme

Eşleme veri akışındaki [kaynak dönüşümden](data-flow-source.md) ve [Havuz dönüşümünde](data-flow-sink.md) ayrıntıları öğrenin.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Azure SQL veri ambarı için veri türü eşlemesi

Veya Azure SQL veri ambarı veri kopyalayın, aşağıdaki eşlemeler Azure SQL veri ambarı veri türlerinden Azure veri fabrikası geçici veri türleri için kullanılır. Bkz: [şema ve veri türü eşlemeleri](copy-activity-schema-and-type-mapping.md) kopyalama etkinliği havuz için kaynak şema ve veri türü eşlemelerini nasıl öğrenin.

>[!TIP]
>SQL DW tarafından desteklenen veri türleri ve desteklenmeyen çözümler için [Azure SQL veri ambarı makalesindeki tablo veri türleri](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md) bölümüne bakın.

| Azure SQL veri ambarı veri türü    | Veri Fabrikası geçici veri türü |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| binary                                | Byte[]                         |
| bit                                   | Boole                        |
| char                                  | String, Char[]                 |
| date                                  | DateTime                       |
| Tarih saat                              | DateTime                       |
| datetime2                             | DateTime                       |
| Datetimeoffset                        | DateTimeOffset                 |
| Decimal                               | Decimal                        |
| FILESTREAM attribute (varbinary(max)) | Byte[]                         |
| Kayan                                 | çift                         |
| image                                 | Byte[]                         |
| int                                   | Int32                          |
| money                                 | Decimal                        |
| nchar                                 | String, Char[]                 |
| numeric                               | Decimal                        |
| nvarchar                              | String, Char[]                 |
| real                                  | Tek                         |
| rowversion                            | Byte[]                         |
| smalldatetime                         | DateTime                       |
| smallint                              | Int16                          |
| smallmoney                            | Decimal                        |
| time                                  | TimeSpan                       |
| tinyint                               | Byte                           |
| uniqueidentifier                      | Guid                           |
| varbinary                             | Byte[]                         |
| varchar                               | String, Char[]                 |

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="getmetadata-activity-properties"></a>GetMetadata etkinlik özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [GetMetadata etkinliğini](control-flow-get-metadata-activity.md) denetleyin 

## <a name="next-steps"></a>Sonraki adımlar
Azure veri fabrikasında kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları ve biçimler](copy-activity-overview.md##supported-data-stores-and-formats).
