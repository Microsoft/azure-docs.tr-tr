---
title: Azure SYNAPSE Analytics 'te verileri kopyalama ve dönüştürme
description: Azure SYNAPSE Analytics 'e ve veri kopyalama ve Data Factory kullanarak Azure SYNAPSE Analytics 'teki verileri dönüştürme hakkında bilgi edinin.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 9c843ededd1fa863cc5eb4dc0db3a6da3478466d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597530"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-by-using-azure-data-factory"></a>Azure Data Factory kullanarak Azure SYNAPSE Analytics 'te veri kopyalama ve dönüştürme

> [!div class="op_single_selector" title1="Kullanmakta olduğunuz Data Factory hizmeti sürümünü seçin:"]
>
> - [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> - [Güncel sürüm](connector-azure-sql-data-warehouse.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, Azure SYNAPSE Analytics 'ten verileri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir ve Azure Data Lake Storage 2. verileri dönüştürmek için veri akışı kullanılır. Azure Data Factory hakkında bilgi edinmek için [tanıtım makalesini](introduction.md)okuyun.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu Azure SYNAPSE Analytics Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Etkinliği](copy-activity-overview.md) [Desteklenen kaynak/havuz matris](copy-activity-overview.md) tablosuyla Kopyala
- [Veri akışını eşleme](concepts-data-flow-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)
- [GetMetadata etkinliği](control-flow-get-metadata-activity.md)

Kopyalama etkinliği için bu Azure SYNAPSE Analytics Bağlayıcısı şu işlevleri destekler:

- Azure kaynakları için hizmet sorumlusu veya yönetilen kimlikler ile SQL kimlik doğrulaması ve Azure Active Directory (Azure AD) uygulama belirteci kimlik doğrulamasını kullanarak verileri kopyalayın.
- Kaynak olarak, bir SQL sorgusu veya saklı yordam kullanarak verileri alın. Ayrıca, Azure SYNAPSE Analytics kaynağından paralel kopyalama seçeneğini de belirleyebilirsiniz. Ayrıntılar için [Azure SYNAPSE Analytics 'Ten paralel kopyalama](#parallel-copy-from-azure-synapse-analytics) bölümüne bakın.
- Havuz olarak, [PolyBase](#use-polybase-to-load-data-into-azure-synapse-analytics) veya [Copy ifadesini](#use-copy-statement) veya toplu ekleme 'yi kullanarak veri yükleyin. Daha iyi kopyalama performansı için PolyBase veya COPY deyimimizi öneririz. Bağlayıcı, kaynak şemasına bağlı değilse otomatik olarak hedef tablo oluşturmayı da destekler.

> [!IMPORTANT]
> Azure Data Factory Integration Runtime kullanarak verileri kopyalarsanız, Azure hizmetlerinin [MANTıKSAL SQL Server](../azure-sql/database/logical-servers.md)'a erişebilmesi için [sunucu düzeyinde bir güvenlik duvarı kuralı](../azure-sql/database/firewall-configure.md) yapılandırın.
> Şirket içinde barındırılan tümleştirme çalışma zamanı kullanarak verileri kopyalarsanız, güvenlik duvarını uygun IP aralığına izin verecek şekilde yapılandırın. Bu Aralık, Azure SYNAPSE Analytics 'e bağlanmak için kullanılan makinenin IP 'sini içerir.

## <a name="get-started"></a>başlarken

> [!TIP]
> En iyi performansı elde etmek için PolyBase veya COPY deyimlerini kullanarak Azure SYNAPSE Analytics 'e veri yükleyin. [Azure SYNAPSE Analytics 'e veri yüklemek](#use-polybase-to-load-data-into-azure-synapse-analytics) ve [Copy deyimlerini kullanarak Azure SYNAPSE Analytics bölümlerine veri yüklemek](#use-copy-statement) için PolyBase kullanma ayrıntıları vardır. Kullanım örneği ile ilgili bir anlatım için, [Azure Data Factory ile 15 dakika altında Azure SYNAPSE Analytics 'e 1 TB yükleme](load-azure-sql-data-warehouse.md)bölümüne bakın.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, bir Azure SYNAPSE Analytics bağlayıcısına özgü Data Factory varlıkları tanımlayan özellikler hakkında ayrıntılı bilgi sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Aşağıdaki özellikler bir Azure SYNAPSE Analytics bağlı hizmeti için desteklenir:

| Özellik            | Açıklama                                                  | Gerekli                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| tür                | Type özelliği **Azuresqldw** olarak ayarlanmalıdır.             | Yes                                                          |
| Dizisi    | **ConnectionString** özelliği Için Azure SYNAPSE Analytics örneğine bağlanmak için gereken bilgileri belirtin. <br/>Bu alanı, Data Factory güvenli bir şekilde depolamak için SecureString olarak işaretleyin. Ayrıca, parola/hizmet sorumlusu anahtarını Azure Key Vault de koyabilirsiniz ve SQL kimlik doğrulaması ise `password` yapılandırmayı bağlantı dizesinin dışına çekebilirsiniz. Daha ayrıntılı bilgi için tablonun altındaki JSON örneğine bakın ve [kimlik bilgilerini Azure Key Vault makalesine saklayın](store-credentials-in-key-vault.md) . | Yes                                                          |
| Serviceprincipalıd  | Uygulamanın istemci KIMLIĞINI belirtin.                         | Evet, Azure AD kimlik doğrulamasını bir hizmet sorumlusu ile kullandığınızda. |
| Servicesprincipalkey | Uygulamanın anahtarını belirtin. Data Factory güvenli bir şekilde depolamak için bu alanı SecureString olarak işaretleyin veya [Azure Key Vault depolanan bir gizli dizi başvurusu](store-credentials-in-key-vault.md)yapın. | Evet, Azure AD kimlik doğrulamasını bir hizmet sorumlusu ile kullandığınızda. |
| Kiracı              | Uygulamanızın altında bulunduğu kiracı bilgilerini (etki alanı adı veya kiracı KIMLIĞI) belirtin. Fareyi, Azure portal sağ üst köşesine getirerek alabilirsiniz. | Evet, Azure AD kimlik doğrulamasını bir hizmet sorumlusu ile kullandığınızda. |
| Azurecses türü | Hizmet sorumlusu kimlik doğrulaması için, Azure AD uygulamanızın kaydedildiği Azure bulut ortamının türünü belirtin. <br/> İzin verilen değerler `AzurePublic` ,,, `AzureChina` `AzureUsGovernment` ve `AzureGermany` . Varsayılan olarak, Data Factory 'nin bulut ortamı kullanılır. | No |
| connectVia          | Veri deposuna bağlanmak için kullanılacak [tümleştirme çalışma zamanı](concepts-integration-runtime.md) . Azure Integration Runtime veya şirket içinde barındırılan tümleştirme çalışma zamanını (veri depolubir özel ağda bulunuyorsa) kullanabilirsiniz. Belirtilmemişse, varsayılan Azure Integration Runtime kullanır. | No                                                           |

Farklı kimlik doğrulama türleri için sırasıyla Önkoşullar ve JSON örnekleri hakkında aşağıdaki bölümlere bakın:

- [SQL kimlik doğrulaması](#sql-authentication)
- Azure AD uygulama belirteci kimlik doğrulaması: [hizmet sorumlusu](#service-principal-authentication)
- Azure AD uygulama belirteci kimlik doğrulaması: [Azure kaynakları Için Yönetilen kimlikler](#managed-identity)

>[!TIP]
>Kullanıcı arabiriminden Azure SYNAPSE **sunucusuz** SQL havuzu için bağlı hizmet oluştururken, abonelikten göz atmak yerine "el ile gir" i seçin.

>[!TIP]
>"UserErrorFailedToConnectToSqlServer" adlı hata kodu ile hata ve "veritabanı için oturum sınırı XXX ve erişildi." gibi bir hatayla karşılaşırsanız, `Pooling=false` Bağlantı dizenizi ekleyin ve yeniden deneyin.

### <a name="sql-authentication"></a>SQL kimlik doğrulaması

#### <a name="linked-service-example-that-uses-sql-authentication"></a>SQL kimlik doğrulaması kullanan bağlı hizmet örneği

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
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

Hizmet sorumlusu tabanlı Azure AD uygulama belirteci kimlik doğrulamasını kullanmak için şu adımları izleyin:

1. Azure portal **[bir Azure Active Directory uygulaması oluşturun](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)** . Uygulama adını ve bağlı hizmeti tanımlayan aşağıdaki değerleri unutmayın:

   - Uygulama Kimliği
   - Uygulama anahtarı
   - Kiracı Kimliği

2. Daha önce yapmadıysanız Azure portal sunucunuz için **[bir Azure Active Directory Yöneticisi sağlayın](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** . Azure AD yöneticisi bir Azure AD kullanıcısı veya Azure AD grubu olabilir. Gruba yönetilen kimliğe sahip bir yönetici rolü verirseniz, 3 ve 4. adımları atlayın. Yöneticinin veritabanına tam erişimi olur.

3. Hizmet sorumlusu için **[Kapsanan Veritabanı kullanıcıları oluşturun](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** . SSMS gibi araçları kullanarak verileri kopyalamak istediğiniz veri ambarına ya da en az BIR kullanıcı izni olan bir Azure AD kimliğiyle bağlanın. Aşağıdaki T-SQL ' i çalıştırın:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. SQL kullanıcıları veya diğerleri için normalde yaptığınız sürece **Hizmet sorumlusuna gerekli Izinleri verin** . Aşağıdaki kodu çalıştırın veya daha [fazla seçeneğe bakın](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). Verileri yüklemek için PolyBase kullanmak istiyorsanız [gerekli veritabanı iznini](#required-database-permission)öğrenin.

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. Azure Data Factory 'de **Azure SYNAPSE Analytics bağlı hizmetini yapılandırın** .

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Hizmet sorumlusu kimlik doğrulaması kullanan bağlı hizmet örneği

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30",
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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Azure kaynakları kimlik doğrulaması için Yönetilen kimlikler

Bir veri fabrikası, belirli bir fabrikası temsil eden [Azure kaynakları için yönetilen bir kimlikle](data-factory-service-identity.md) ilişkilendirilebilir. Bu yönetilen kimliği, Azure SYNAPSE Analytics kimlik doğrulaması için kullanabilirsiniz. Belirlenen fabrika, bu kimliği kullanarak veri ambarınıza veya verilere erişebilir ve bu verileri kopyalayabilir.

Yönetilen kimlik kimlik doğrulamasını kullanmak için şu adımları izleyin:

1. Daha önce yapmadıysanız Azure portal sunucunuza yönelik **[bir Azure Active Directory Yöneticisi sağlayın](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** . Azure AD yöneticisi bir Azure AD kullanıcısı veya Azure AD grubu olabilir. Gruba yönetilen kimliğe sahip bir yönetici rolü verirseniz, 3 ve 4. adımları atlayın. Yöneticinin veritabanına tam erişimi olur.

2. Data Factory yönetilen kimlik için **[Kapsanan Veritabanı kullanıcıları oluşturun](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** . SSMS gibi araçları kullanarak verileri kopyalamak istediğiniz veri ambarına ya da en az BIR kullanıcı izni olan bir Azure AD kimliğiyle bağlanın. Aşağıdaki T-SQL ' i çalıştırın.
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. SQL kullanıcıları ve diğerleri için normalde yaptığınız gibi **Data Factory yönetilen kimliğe gerekli Izinleri verin** . Aşağıdaki kodu çalıştırın veya daha [fazla seçeneğe bakın](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). Verileri yüklemek için PolyBase kullanmak istiyorsanız [gerekli veritabanı iznini](#required-database-permission)öğrenin.

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

4. Azure Data Factory 'de **Azure SYNAPSE Analytics bağlı hizmetini yapılandırın** .

**Örnek:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](concepts-datasets-linked-services.md) makalesi.

Aşağıdaki özellikler Azure SYNAPSE Analytics veri kümesi için desteklenir:

| Özellik  | Açıklama                                                  | Gerekli                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| tür      | Veri kümesinin **Type** özelliği **Azuresqldwtable** olarak ayarlanmalıdır. | Yes                         |
| schema | Şemanın adı. |Kaynak için Hayır, havuz için Evet  |
| tablo | Tablo/görünüm adı. |Kaynak için Hayır, havuz için Evet  |
| tableName | Şema ile tablonun/görünümün adı. Bu özellik geriye dönük uyumluluk için desteklenir. Yeni iş yükü için `schema` ve kullanın `table` . | Kaynak için Hayır, havuz için Evet |

### <a name="dataset-properties-example"></a>DataSet özellikleri örneği

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure Synapse Analytics linked service name>",
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

## <a name="copy-activity-properties"></a>Kopyalama etkinliği özellikleri

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, Azure SYNAPSE Analytics kaynağı ve havuzu tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="azure-synapse-analytics-as-the-source"></a>Kaynak olarak Azure SYNAPSE Analytics

>[!TIP]
>Veri bölümlemesini kullanarak Azure SYNAPSE Analytics 'ten verileri verimli bir şekilde yüklemek için [Azure SYNAPSE Analytics 'Ten paralel kopyadan](#parallel-copy-from-azure-synapse-analytics)daha fazla bilgi edinin.

Azure SYNAPSE Analytics 'ten veri kopyalamak için kopyalama etkinliği kaynağındaki **Type** özelliğini **sqldwsource** olarak ayarlayın. Aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik                     | Açıklama                                                  | Gerekli |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| tür                         | Kopyalama etkinliği kaynağının **Type** özelliği **sqldwsource** olarak ayarlanmalıdır. | Yes      |
| sqlReaderQuery               | Verileri okumak için özel SQL sorgusunu kullanın. Örnek: `select * from MyTable`. | No       |
| sqlReaderStoredProcedureName | Kaynak tablodaki verileri okuyan saklı yordamın adı. Son SQL ifadesinin saklı yordamda bir SELECT ifadesinin olması gerekir. | No       |
| storedProcedureParameters    | Saklı yordamın parametreleri.<br/>İzin verilen değerler ad veya değer çiftleridir. Parametrelerin adları ve büyük harfleri, saklı yordam parametrelerinin adlarıyla ve büyük küçük harfleriyle aynı olmalıdır. | No       |
| 'Sinden | SQL kaynağı için işlem kilitleme davranışını belirtir. İzin verilen değerler: **ReadCommitted**, **READUNCOMMITTED**, **RepeatableRead**, **Serializable**, **Snapshot**. Belirtilmemişse, veritabanının varsayılan yalıtım düzeyi kullanılır. Daha fazla bilgi için bkz. [System. Data. IsolationLevel](/dotnet/api/system.data.isolationlevel). | No |
| partitionOptions | Azure SYNAPSE Analytics 'ten veri yüklemek için kullanılan veri bölümleme seçeneklerini belirtir. <br>İzin verilen değerler: **none** (default), **Physicalpartitionsoftable** ve **DynamicRange**.<br>Bir bölüm seçeneği etkinleştirildiğinde (yani `None` ), Azure SYNAPSE Analytics 'ten eş zamanlı olarak veri yükleme derecesi, [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) kopyalama etkinliğindeki ayar tarafından denetlenir. | No |
| partitionSettings | Veri bölümleme için ayarların grubunu belirtin. <br>Bölüm seçeneği olmadığında uygulayın `None` . | No |
| ***Altında `partitionSettings` :*** | | |
| partitionColumnName |  `int` `smallint` `bigint` `date` `smalldatetime` `datetime` `datetime2` `datetimeoffset` Paralel kopya için Aralık bölümleme tarafından kullanılacak tamsayı veya tarih/DateTime türünde (,,,,,,, veya) kaynak sütunun adını belirtin. Belirtilmemişse, tablonun dizini veya birincil anahtarı otomatik olarak algılanır ve bölüm sütunu olarak kullanılır.<br>Bölüm seçeneği olduğunda uygulayın `DynamicRange` . Kaynak verileri almak için bir sorgu kullanırsanız,  `?AdfDynamicRangePartitionCondition ` WHERE yan tümcesinde kanca. Örnek için, [SQL veritabanı 'Ndan paralel kopyalama](#parallel-copy-from-azure-synapse-analytics) bölümüne bakın. | No |
| Partitionüstsınırı | Bölüm aralığı bölme için bölüm sütununun en büyük değeri. Bu değer, tablodaki satırları filtrelemeye yönelik değil, bölümün ilerlemesine karar vermek için kullanılır. Tablodaki veya sorgu sonucundaki tüm satırlar bölümlenecek ve kopyalanabilir. Belirtilmemişse, kopyalama etkinliği değeri otomatik olarak algılar.  <br>Bölüm seçeneği olduğunda uygulayın `DynamicRange` . Örnek için, [SQL veritabanı 'Ndan paralel kopyalama](#parallel-copy-from-azure-synapse-analytics) bölümüne bakın. | No |
| Partitionalme sınırı | Bölüm aralığı bölme için bölüm sütununun en küçük değeri. Bu değer, tablodaki satırları filtrelemeye yönelik değil, bölümün ilerlemesine karar vermek için kullanılır. Tablodaki veya sorgu sonucundaki tüm satırlar bölümlenecek ve kopyalanabilir. Belirtilmemişse, kopyalama etkinliği değeri otomatik olarak algılar.<br>Bölüm seçeneği olduğunda uygulayın `DynamicRange` . Örnek için, [SQL veritabanı 'Ndan paralel kopyalama](#parallel-copy-from-azure-synapse-analytics) bölümüne bakın. | No |

**Aşağıdaki noktaya göz önünde edin:**

- Verileri almak için kaynakta saklı yordam kullanırken, saklı yordamınız farklı bir parametre değeri geçirildiğinde farklı bir şema döndürüyor olarak tasarlanmışsa, hata ile karşılaşabilirsiniz veya kullanıcı arabiriminden şemayı içeri aktarırken ya da otomatik tablo oluşturma ile verileri SQL veritabanına kopyalarken beklenmedik sonucu görebilirsiniz.

#### <a name="example-using-sql-query"></a>Örnek: SQL sorgusu kullanma

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Synapse Analytics input dataset name>",
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

#### <a name="example-using-stored-procedure"></a>Örnek: saklı yordamı kullanma

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Synapse Analytics input dataset name>",
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

#### <a name="sample-stored-procedure"></a>Örnek saklı yordam:

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

### <a name="azure-synapse-analytics-as-sink"></a><a name="azure-sql-data-warehouse-as-sink"></a> Havuz olarak Azure SYNAPSE Analytics

Azure Data Factory, verileri Azure SYNAPSE Analytics 'e yüklemek için üç yolu destekler.

![Azure SYNAPSE Analytics havuz kopyalama seçenekleri](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [PolyBase kullanma](#use-polybase-to-load-data-into-azure-synapse-analytics)
- [COPY ifadesini kullan](#use-copy-statement)
- Toplu ekleme kullan

Veri yükleme en hızlı ve en ölçeklenebilir yolu [PolyBase](/sql/relational-databases/polybase/polybase-guide) veya [Copy deyimdir](/sql/t-sql/statements/copy-into-transact-sql).

Verileri Azure SYNAPSE Analytics 'e kopyalamak için kopyalama etkinliğindeki havuz türünü **Sqldwsink** olarak ayarlayın. Aşağıdaki özellikler, kopyalama etkinliği **havuzu** bölümünde desteklenir:

| Özellik          | Açıklama                                                  | Gerekli                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| tür              | Kopyalama etkinliği havuzunun **Type** özelliği **sqldwsink** olarak ayarlanmalıdır. | Yes                                           |
| allowPolyBase     | Azure SYNAPSE Analytics 'e veri yüklemek için PolyBase kullanılıp kullanılmayacağını belirtir. `allowCopyCommand` ve `allowPolyBase` her ikisi de true olamaz. <br/><br/>Kısıtlamalar ve Ayrıntılar için bkz. [Azure SYNAPSE Analytics 'e veri yüklemek Için PolyBase kullanma](#use-polybase-to-load-data-into-azure-synapse-analytics) .<br/><br/>İzin verilen değerler **true** ve **false** (varsayılan) şeklindedir. | Hayır.<br/>PolyBase kullanırken uygulayın.     |
| polyBaseSettings  | `allowPolybase`Özelliği **true** olarak ayarlandığında belirtilenebilir bir özellik grubu. | Hayır.<br/>PolyBase kullanırken uygulayın. |
| allowCopyCommand | Azure SYNAPSE Analytics 'e veri yüklemek için [kopyalama ifadesinin](/sql/t-sql/statements/copy-into-transact-sql) kullanılıp kullanılmayacağını gösterir. `allowCopyCommand` ve `allowPolyBase` her ikisi de true olamaz. <br/><br/>Kısıtlamalar ve Ayrıntılar için bkz. [Copy deyimlerini kullanarak Azure SYNAPSE Analytics 'e veri yükleme](#use-copy-statement) bölümüne bakın.<br/><br/>İzin verilen değerler **true** ve **false** (varsayılan) şeklindedir. | Hayır.<br>KOPYALAMA kullanırken uygulayın. |
| copyCommandSettings | Özelliği TRUE olarak ayarlandığında belirtilenebilir bir özellik grubu `allowCopyCommand` . | Hayır.<br/>KOPYALAMA kullanırken uygulayın. |
| writeBatchSize    | **Toplu iş BAŞıNA** SQL tablosuna eklenecek satır sayısı.<br/><br/>İzin verilen değer **Integer** (satır sayısı). Varsayılan olarak, Data Factory satır boyutuna göre uygun toplu iş boyutunu dinamik olarak belirler. | Hayır.<br/>Toplu ekleme kullanırken uygulayın.     |
| writeBatchTimeout | Toplu ekleme işleminin, zaman aşımına uğramadan önce tamamlaması için bekleme süresi.<br/><br/>İzin verilen değer **TimeSpan** değeridir. Örnek: "00:30:00" (30 dakika). | Hayır.<br/>Toplu ekleme kullanırken uygulayın.        |
| Ön Copyscrıpt     | Her çalıştırmada Azure SYNAPSE Analytics 'e veri yazmadan önce çalıştırılacak bir SQL sorgusu belirtin. Önceden yüklenmiş verileri temizlemek için bu özelliği kullanın. | No                                            |
| tableOption | Kaynak şemasına göre yoksa [Havuz tablosunun otomatik olarak oluşturulup](copy-activity-overview.md#auto-create-sink-tables) oluşturulmayacağını belirtir. İzin verilen değerler: `none` (varsayılan), `autoCreate` . |No |
| disableMetricsCollection | Data Factory, performansı en iyi duruma getirme ve öneriler için Azure SYNAPSE Analytics DWUs gibi ölçümleri toplar ve bu da ek ana DB erişimi sağlar. Bu davranışla ilgileniyorlarsa, `true` devre dışı bırakmak için belirtin. | Hayır (varsayılan değer `false` ) |
| maxConcurrentConnections |Etkinlik çalışması sırasında veri deposuna kurulan eşzamanlı bağlantıların üst sınırı. Yalnızca eş zamanlı bağlantıları sınırlandırmak istediğinizde bir değer belirtin.| No |

#### <a name="azure-synapse-analytics-sink-example"></a>Azure SYNAPSE Analytics havuz örneği

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

## <a name="parallel-copy-from-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'ten paralel kopya

Copy etkinliğinde Azure SYNAPSE Analytics Connector, verileri paralel olarak kopyalamak için yerleşik veri bölümlendirme sağlar. Kopyalama etkinliğinin **kaynak** sekmesinde veri bölümleme seçeneklerini bulabilirsiniz.

![Bölüm seçeneklerinin ekran görüntüsü](./media/connector-sql-server/connector-sql-partition-options.png)

Bölümlenmiş kopyayı etkinleştirdiğinizde kopyalama etkinliği, verileri bölümlere göre yüklemek için Azure SYNAPSE Analytics kaynağınıza karşı paralel sorgular çalıştırır. Paralel derece [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) kopyalama etkinliğindeki ayar tarafından denetlenir. Örneğin, `parallelCopies` dört olarak ayarlarsanız, Data Factory aynı anda, belirtilen bölüm seçeneğiniz ve ayarlarınıza göre dört sorgu üretir ve çalışır ve her sorgu Azure SYNAPSE analizinizden verilerin bir kısmını alır.

Azure SYNAPSE Analizinizden büyük miktarda veri yüklerken özellikle veri bölümleme ile paralel kopyayı etkinleştirmeniz önerilir. Farklı senaryolar için önerilen yapılandırma aşağıda verilmiştir. Dosya tabanlı veri deposuna veri kopyalarken, bir klasöre birden çok dosya (yalnızca klasör adı belirtin) yazılması önerilir, bu durumda performans tek bir dosyaya yazılmasından daha iyidir.

| Senaryo                                                     | Önerilen ayarlar                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Fiziksel bölümlerle büyük tablodan tam yük.        | **Bölüm seçeneği**: tablonun fiziksel bölümleri. <br><br/>Yürütme sırasında Data Factory fiziksel bölümleri otomatik olarak algılar ve verileri bölümlere göre kopyalar. <br><br/>Tablonuzun fiziksel bölümü olup olmadığını denetlemek için [Bu sorguya](#sample-query-to-check-physical-partition)başvurabilirsiniz. |
| Fiziksel bölümler olmadan, veri bölümlendirme için bir tamsayı veya tarih saat sütunuyla büyük tablodan tam yük. | **Bölüm seçenekleri**: Dinamik Aralık bölümü.<br>**Bölüm sütunu** (isteğe bağlı): verileri bölümlemek için kullanılan sütunu belirtin. Belirtilmezse, dizin veya birincil anahtar sütunu kullanılır.<br/>**Bölüm üst sınırı** ve **bölüm alt sınırı** (isteğe bağlı): Bölüm Ilerlemesiyle mı belirlemek istediğinizi belirtin. Bu, tablodaki satırları filtrelemeye yönelik değildir, tablodaki tüm satırlar bölümlenecek ve kopyalanır. Belirtilmemişse, kopyalama etkinliği değerleri otomatik olarak algılar.<br><br>Örneğin, "ID" adlı bölüm sütununuzu 1 ile 100 arasında değerler varsa ve alt sınırı 20 ve üst sınır olarak 80 olarak ayarlarsanız, paralel kopyalama Data Factory 4 ' e kadar, verileri 4 bölüm kimliği aralığında <= 20, [21, 50], [51, 80] ve >= 81 sırasıyla alır. |
| Fiziksel bölümler olmadan, veri bölümlendirme için bir tamsayı veya tarih/tarih/saat sütunuyla büyük miktarda veri yükleyin. | **Bölüm seçenekleri**: Dinamik Aralık bölümü.<br>**Sorgu**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Bölüm sütunu**: verileri bölümlemek için kullanılan sütunu belirtin.<br>**Bölüm üst sınırı** ve **bölüm alt sınırı** (isteğe bağlı): Bölüm Ilerlemesiyle mı belirlemek istediğinizi belirtin. Bu, tablodaki satırları filtrelemeye yönelik değildir, sorgu sonucundaki tüm satırlar bölümlenecek ve kopyalanır. Belirtilmemişse, kopyalama etkinliği değeri otomatik olarak algılar.<br><br>Yürütme sırasında, Data Factory `?AdfRangePartitionColumnName` her bölüm için gerçek sütun adı ve değer aralıklarıyla değiştirilir ve Azure SYNAPSE Analytics 'e gönderir. <br>Örneğin, "ID" adlı bölüm sütununuzu 1 ile 100 arasında değerler varsa ve alt sınırı 20 ve üst sınır olarak 80 olarak ayarlarsanız, paralel kopyalama Data Factory 4 ' e kadar, verileri 4 bölüm kimliği aralığında <= 20, [21, 50], [51, 80] ve >= 81 sırasıyla alır. <br><br>Farklı senaryolar için daha fazla örnek sorgu aşağıda verilmiştir:<br> 1. tüm tabloyu sorgulayın: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. sütun seçimi ve ek WHERE yan tümcesi filtreleri içeren bir tablodan sorgu: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. sorgular ile sorgu: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. alt sorguda bölüm ile sorgulama: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Bölüm seçeneğiyle verileri yüklemek için en iyi uygulamalar:

1. Veri eğriliğini önlemek için bölüm sütunu olarak (birincil anahtar veya benzersiz anahtar gibi) farklı bir sütun seçin. 
2. Tabloda yerleşik bölüm varsa, daha iyi performans sağlamak için "tablonun fiziksel bölümleri" Bölüm seçeneğini kullanın.
3. Verileri kopyalamak için Azure Integration Runtime kullanırsanız daha fazla bilgi işlem kaynağı kullanmak için daha büyük "[veri tümleştirme birimleri (DIU)](copy-activity-performance-features.md#data-integration-units)" (>4) ayarlayabilirsiniz. İlgili senaryolara göz atın.
4. "[Kopya paralelliği derecesi](copy-activity-performance-features.md#parallel-copy)", Bölüm numaralarını denetler, bu sayının performansı karşılamamasından çok büyük bir şekilde ayarlanması, bu sayıyı (ya da şirket IÇINDE barındırılan IR düğümlerinin sayısı) * (2-4) olarak ayarlamayı öneririz.
5. Azure SYNAPSE Analytics bir anda en fazla 32 sorgu yürütebilir, "kopya paralellik derecesi" ayarı çok büyük olduğunda SYNAPSE azaltma sorununa neden olabilir.

**Örnek: fiziksel bölümlerle büyük tablodan tam yük**

```json
"source": {
    "type": "SqlDWSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Örnek: Dinamik Aralık bölümü ile sorgulama**

```json
"source": {
    "type": "SqlDWSource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

### <a name="sample-query-to-check-physical-partition"></a>Fiziksel bölümü denetlemek için örnek sorgu

```sql
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, c.name AS ColumnName, CASE WHEN c.name IS NULL THEN 'no' ELSE 'yes' END AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id
LEFT JOIN sys.types AS y ON c.system_type_id = y.system_type_id
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

Tabloda fiziksel bölüm varsa, "HasPartition" öğesini "Yes" olarak görürsünüz.

## <a name="use-polybase-to-load-data-into-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'e veri yüklemek için PolyBase kullanma

[PolyBase](/sql/relational-databases/polybase/polybase-guide) 'in kullanılması, yüksek aktarım hızı Ile Azure SYNAPSE Analytics 'e büyük miktarda veri yüklemek için etkili bir yoldur. Varsayılan BULKıNSERT mekanizması yerine PolyBase kullanarak üretilen iş için büyük bir kazanç görürsünüz. Kullanım örneği ile ilgili bir anlatım için bkz. [1 TB 'Yi Azure SYNAPSE Analytics 'e yükleme](v1/data-factory-load-sql-data-warehouse.md).

- Kaynak verileriniz Azure Blob 'dayken **, Azure Data Lake Storage 1. veya Azure Data Lake Storage 2.** ve **Biçim PolyBase uyumluysa**, Azure SYNAPSE Analytics 'in verileri kaynaktan çekmesini sağlamak üzere doğrudan PolyBase 'i çağırmak için kopyalama etkinliğini kullanabilirsiniz. Ayrıntılar için bkz. **[PolyBase kullanarak doğrudan kopyalama](#direct-copy-by-using-polybase)**.
- Kaynak veri depoluü ve biçimlendirmeniz ilk olarak PolyBase tarafından desteklenmiyorsa, bunun yerine **[PolyBase özelliğini kullanarak hazırlanan kopyayı](#staged-copy-by-using-polybase)** kullanın. Hazırlanan kopya özelliği de size daha iyi aktarım hızı sağlar. Verileri otomatik olarak PolyBase uyumlu biçime dönüştürür, verileri Azure Blob depolama alanında depolar ve Azure SYNAPSE Analytics 'e veri yüklemek için PolyBase 'i çağırır.

> [!TIP]
> [PolyBase 'i kullanmaya yönelik en iyi uygulamalar](#best-practices-for-using-polybase)hakkında daha fazla bilgi edinin. Azure Integration Runtime ile PolyBase kullanılırken doğrudan veya hazırlanan depolama-SYNAPSE için etkili [veri tümleştirme birimleri (DIU)](copy-activity-performance-features.md#data-integration-units) her zaman 2 ' dir. Depolama alanından yükleme, SYNAPSE altyapısı tarafından desteklenmektedir, DIU 'nın ayarlanması performansı etkilemez.

Kopyalama etkinliğinde aşağıdaki PolyBase ayarları desteklenir `polyBaseSettings` :

| Özellik          | Açıklama                                                  | Gerekli                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | Sorgu başarısız olmadan önce reddedilecek satırların sayısını veya yüzdesini belirtir.<br/><br/>[Dış tablo oluşturma (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql)öğesinin bağımsız değişkenler bölümünde PolyBase 'in reddetme seçenekleri hakkında daha fazla bilgi edinin. <br/><br/>İzin verilen değerler 0 (varsayılan), 1, 2, vb. olabilir. | No                                            |
| rejectType        | **RejectValue** seçeneğinin sabit değer veya yüzde değeri olup olmadığını belirtir.<br/><br/>İzin verilen değerler **değer** (varsayılan) ve **yüzde** değeridir. | No                                            |
| rejectSampleValue | PolyBase reddedilen satırların yüzdesini yeniden hesaplamadan önce alınacak satır sayısını belirler.<br/><br/>İzin verilen değerler 1, 2, vb. olabilir. | Evet, **rejectType** **ise.** |
| useTypeDefault    | PolyBase metin dosyasından verileri aldığında, sınırlandırılmış metin dosyalarında eksik değerlerin nasıl işleneceğini belirtir.<br/><br/>[Dış dosya biçimi oluşturma (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql)Içindeki bağımsız değişkenler bölümünden bu özellik hakkında daha fazla bilgi edinin.<br/><br/>İzin verilen değerler **true** ve **false** (varsayılan) şeklindedir.<br><br> | No                                            |

### <a name="direct-copy-by-using-polybase"></a>PolyBase kullanarak doğrudan kopyalama

Azure SYNAPSE Analytics PolyBase doğrudan Azure blob, Azure Data Lake Storage 1. ve Azure Data Lake Storage 2. destekler. Kaynak verileriniz bu bölümde açıklanan ölçütleri karşılıyorsa, doğrudan kaynak veri deposundan Azure SYNAPSE Analytics 'e kopyalamak için PolyBase kullanın. Aksi takdirde, [PolyBase kullanarak hazırlanmış kopyayı](#staged-copy-by-using-polybase)kullanın.

> [!TIP]
> Verileri Azure SYNAPSE Analytics 'e verimli bir şekilde kopyalamak için Azure Data Factory 'tan daha fazla bilgi edinin [Data Lake Store Azure SYNAPSE Analytics ile birlikte kullanırken verilerin öngörülerini açığa çıkarmak daha da kolay ve kullanışlı hale gelir](/archive/blogs/azuredatalake/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse).

Gereksinimler karşılanmazsa, Azure Data Factory ayarları denetler ve veri taşıma için otomatik olarak BULKıNSERT mekanizmasına geri döner.

1. **Kaynak bağlı hizmeti** aşağıdaki türler ve kimlik doğrulama yöntemleriyle aynıdır:

    | Desteklenen kaynak veri deposu türü                             | Desteklenen kaynak kimlik doğrulama türü                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | Hesap anahtarı kimlik doğrulaması, yönetilen kimlik kimlik doğrulaması |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | Hizmet sorumlusu kimlik doğrulaması                            |
    | [Azure Data Lake Storage 2. Nesil](connector-azure-data-lake-storage.md) | Hesap anahtarı kimlik doğrulaması, yönetilen kimlik kimlik doğrulaması |

    >[!IMPORTANT]
    >- Depolama bağlı hizmetiniz için yönetilen kimlik kimlik doğrulaması kullandığınızda, [Azure Blob](connector-azure-blob-storage.md#managed-identity) için gerekli konfigürasyonları ve [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md#managed-identity) sırasıyla öğrenin.
    >- Azure depolama alanı VNet hizmet uç noktası ile yapılandırıldıysa, depolama hesabında "Güvenilen Microsoft hizmeti 'ne izin ver" özelliği etkinleştirilmiş olarak yönetilen kimlik kimlik doğrulamasını kullanmanız gerekir. [Azure depolama Ile VNET hizmet uç noktaları kullanmanın etkileri](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage).

2. **Kaynak veri biçimi** , aşağıdaki yapılandırmalara **sahip Parquet**, **orc** veya **sınırlandırılmış bir metindir**:

   1. Klasör yolu joker karakter filtresi içermiyor.
   2. Dosya adı boş veya tek bir dosyaya işaret ediyor. Kopyalama etkinliğinde joker karakter dosya adı belirtirseniz, yalnızca `*` veya olabilir `*.*` .
   3. `rowDelimiter`**varsayılan**, **\n**, **\r\n** veya **\r**'dir.
   4. `nullValue` Varsayılan olarak bırakılır veya **boş dize** ("") olarak ayarlanır ve `treatEmptyAsNull` Varsayılan olarak bırakılır ya da true olarak ayarlanır.
   5. `encodingName` Varsayılan olarak bırakılır veya **UTF-8** olarak ayarlanır.
   6. `quoteChar`, `escapeChar` ve `skipLineCount` belirtilmedi. PolyBase desteği, ADF 'de olduğu gibi yapılandırılabilen üst bilgi satırını atlar `firstRowAsHeader` .
   7. `compression`**sıkıştırma**, **``GZip``** veya **söndür** olamaz.

3. Kaynağınız bir klasörse, `recursive` Copy etkinliğinin true olarak ayarlanması gerekir.

4. `wildcardFolderPath` ,,, `wildcardFilename` `modifiedDateTimeStart` ,, `modifiedDateTimeEnd` `prefix` `enablePartitionDiscovery` ve `additionalColumns` belirtilmedi.

>[!NOTE]
>Kaynağınız bir klasörssa, PolyBase dosyaları klasörden ve tüm alt klasörlerinden alır ve dosya adının bir alt çizgiyle (_) veya nokta (.) ile başladığı dosyalardan veri almaz ( [burada, konum bağımsız değişkeni](/sql/t-sql/statements/create-external-table-transact-sql#arguments-2)olarak belgelenmiştir).

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
                    "type": "AzureBlobStorageReadSettings",
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

### <a name="staged-copy-by-using-polybase"></a>PolyBase kullanarak hazırlanmış kopya

Kaynak verileriniz PolyBase ile yerel olarak uyumlu değilse, geçici hazırlama Azure Blob 'U veya Azure Data Lake Storage 2. (Azure Premium Depolama olamaz) aracılığıyla veri kopyalamayı etkinleştirin. Bu durumda Azure Data Factory, verileri, PolyBase 'in veri biçimi gereksinimlerini karşılayacak şekilde otomatik olarak dönüştürür. Daha sonra, Azure SYNAPSE Analytics 'e veri yüklemek için PolyBase 'i çağırır. Son olarak, depolama alanından geçici verilerinizi temizler. Hazırlama yoluyla veri kopyalama hakkındaki ayrıntılar için bkz. [aşamalı kopya](copy-activity-performance-features.md#staged-copy) .

Bu özelliği kullanmak için, Azure [BLOB depolama bağlı hizmeti](connector-azure-blob-storage.md#linked-service-properties) oluşturun veya hesap anahtarı ya da ara depolama alanı olarak Azure depolama hesabına başvuran **yönetilen kimlik kimlik doğrulamasıyla** [Azure Data Lake Storage 2. bağlı hizmeti](connector-azure-data-lake-storage.md#linked-service-properties) oluşturun.

>[!IMPORTANT]
>- Hazırlama bağlantılı hizmetiniz için yönetilen kimlik kimlik doğrulaması kullandığınızda, [Azure Blob](connector-azure-blob-storage.md#managed-identity) için gerekli konfigürasyonları ve [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md#managed-identity) sırasıyla öğrenin.
>- Hazırlama Azure depolama alanı sanal ağ hizmeti uç noktası ile yapılandırıldıysa, depolama hesabında "Güvenilen Microsoft hizmeti 'ne izin ver" özelliği etkinleştirilmiş olarak yönetilen kimlik kimlik doğrulamasını kullanmanız gerekir. [Azure depolama Ile VNET hizmet uç noktaları kullanmanın etkileri](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage). 

>[!IMPORTANT]
>Hazırlama Azure depolama birimi yönetilen özel uç noktayla yapılandırıldıysa ve depolama Güvenlik Duvarı etkinse, PolyBase yükü sırasında hazırlanan dosyalara erişebildiğinden emin olmak için, yönetilen kimlik kimlik doğrulamasını kullanmanız ve SYNAPSE SQL Server Depolama Blobu veri okuyucusu izinleri vermeniz gerekir.

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
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

### <a name="best-practices-for-using-polybase"></a>PolyBase 'i kullanmak için en iyi uygulamalar

Aşağıdaki bölümlerde, [Azure SYNAPSE Analytics Için en iyi yöntemler](../synapse-analytics/sql/best-practices-dedicated-sql-pool.md)bölümünde bahsedilen uygulamalara ek olarak en iyi uygulamalar sağlanmaktadır.

#### <a name="required-database-permission"></a>Gerekli veritabanı izni

PolyBase 'i kullanmak için, verileri Azure SYNAPSE Analytics 'e yükleyen kullanıcının hedef veritabanında ["Control" iznine](/sql/relational-databases/security/permissions-database-engine) sahip olması gerekir. Bunu yapmanın bir yolu, kullanıcıyı **db_owner** rolünün bir üyesi olarak eklemektir. [Azure SYNAPSE Analytics genel](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)görünümünde bunu nasıl yapacağınızı öğrenin.

#### <a name="row-size-and-data-type-limits"></a>Satır boyutu ve veri türü sınırları

PolyBase yükleri 1 MB 'tan küçük satırlarla sınırlıdır. VARCHR (MAX), NVARCHAR (MAX) veya VARBINARY (MAX) ' e yüklemek için kullanılamaz. Daha fazla bilgi için bkz. [Azure SYNAPSE Analytics hizmet kapasitesi sınırları](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Kaynak verilerinizde 1 MB 'den büyük satırlar varsa, kaynak tabloları birkaç küçük olanlara dikey olarak bölmek isteyebilirsiniz. Her satırın en büyük boyutunun sınırı aşmadığından emin olun. Daha sonra, PolyBase kullanarak ve Azure SYNAPSE Analytics 'te birlikte birleştirilmiş daha küçük tablolar yüklenebilir.

Alternatif olarak, bu tür geniş sütunlara sahip veriler için, "PolyBase 'e izin ver" ayarını kapatarak ADF kullanarak verileri yüklemek için PolyBase 'i kullanabilirsiniz.

#### <a name="azure-synapse-analytics-resource-class"></a>Azure SYNAPSE Analytics kaynak sınıfı

Mümkün olan en iyi verimi elde etmek için, PolyBase aracılığıyla Azure SYNAPSE Analytics 'e veri yükleyen kullanıcıya daha büyük bir kaynak sınıfı atayın.

#### <a name="polybase-troubleshooting"></a>PolyBase sorunlarını giderme

#### <a name="loading-to-decimal-column"></a>Ondalık sütuna yükleniyor

Kaynak verileriniz Metin biçimindeyse veya PolyBase ile uyumlu olmayan diğer depolarda (hazırlanan kopya ve PolyBase kullanılarak) ve Azure SYNAPSE Analytics decimal sütununa yüklenecek boş bir değer içeriyorsa, şu hatayı alabilirsiniz:

```output
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

Çözüm, "**tür Varsayılanı kullan**" seçeneğinin (false olarak) kopyalama etkinliği havuzu-> PolyBase ayarları ' nda seçimini kaldırır. "[USE_TYPE_DEFAULT](/sql/t-sql/statements/create-external-file-format-transact-sql#arguments)", PolyBase metin dosyasından verileri aldığında sınırlandırılmış metin dosyalarında eksik değerlerin nasıl Işleneceğini belirten PolyBase yerel bir yapılandırmadır.

#### <a name="check-the-tablename-property-in-azure-synapse-analytics"></a>Azure SYNAPSE Analytics 'teki tableName özelliğini denetleyin

Aşağıdaki tabloda, JSON veri kümesindeki **TableName** özelliğinin nasıl belirtilme örnekleri verilmiştir. Şema ve tablo adlarının birkaç birleşimini gösterir.

| DB şeması | Tablo adı | **TableName** JSON özelliği               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable veya dbo. MyTable veya [dbo]. MyTable |
| dbo1      | MyTable    | dbo1. MyTable veya [dbo1]. MyTable          |
| dbo       | My. Table   | [My. Table] veya [dbo]. [My. Table]            |
| dbo1      | My. Table   | [dbo1]. [My. Table]                         |

Aşağıdaki hatayı görürseniz, sorun **TableName** özelliği için belirttiğiniz değer olabilir. **TableName** JSON özelliğinin değerlerini belirtmenin doğru yolu için yukarıdaki tabloya bakın.

```output
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

#### <a name="columns-with-default-values"></a>Varsayılan değerlere sahip sütunlar

Şu anda, Data Factory PolyBase özelliği hedef tablodaki gibi yalnızca aynı sayıda sütunu kabul eder. Örnek olarak, biri varsayılan değerle tanımlanmış dört sütunlu bir tablodur. Giriş verilerinde hala dört sütun olması gerekir. Üç sütunlu bir giriş veri kümesi aşağıdaki iletiye benzer bir hata verir:

```output
All columns of the table must be specified in the INSERT BULK statement.
```

NULL değeri, varsayılan değer olan özel bir formdur. Sütun null atanabilir ise, bu sütun için blobdaki giriş verileri boş olabilir. Ancak giriş veri kümesinde eksik olamaz. PolyBase, Azure SYNAPSE Analytics 'te eksik değerler için NULL ekliyor.

#### <a name="external-file-access-failed"></a>Dış dosya erişimi başarısız oldu

Aşağıdaki hatayı alıyorsanız, yönetilen kimlik kimlik doğrulamasını kullandığınızdan ve Azure SYNAPSE çalışma alanının yönetilen kimliğine Depolama Blobu veri okuyucusu izinleri verildiğinden emin olun.

```output
Job failed due to reason: at Sink '[SinkName]': shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: External file access failed due to internal error: 'Error occurred while accessing HDFS: Java exception raised on call to HdfsBridge_IsDirExist. Java exception message:\r\nHdfsBridge::isDirExist 
```

Daha fazla bilgi için bkz. [çalışma alanı oluşturulduktan sonra yönetilen kimliğe Izin verme](../synapse-analytics/security/how-to-grant-workspace-managed-identity-permissions.md#grant-permissions-to-managed-identity-after-workspace-creation).

## <a name="use-copy-statement-to-load-data-into-azure-synapse-analytics"></a><a name="use-copy-statement"></a> Azure SYNAPSE Analytics 'e veri yüklemek için kopyalama ifadesini kullanma

Azure SYNAPSE Analytics [Copy deyimleri](/sql/t-sql/statements/copy-into-transact-sql) doğrudan **azure blob ve Azure Data Lake Storage 2.** verilerini yüklemeyi destekler. Kaynak verileriniz bu bölümde açıklanan kriterleri karşılıyorsa, verileri Azure SYNAPSE Analytics 'e yüklemek için ADF 'de COPY ifadesini kullanmayı seçebilirsiniz. Azure Data Factory, ayarları denetler ve ölçütler karşılanmazsa kopyalama etkinliğinin çalıştırılmasına başarısız olur.

>[!NOTE]
>Şu anda Data Factory yalnızca aşağıda belirtilen COPY deyimiyle uyumlu kaynaklardan kopyalama desteklenir.

>[!TIP]
>Azure Integration Runtime ile kopyalama açıklaması kullanılırken, etkin [veri tümleştirme birimleri (DIU)](copy-activity-performance-features.md#data-integration-units) her zaman 2 ' dir. Depolama alanından yükleme, SYNAPSE altyapısı tarafından desteklenmektedir, DIU 'nın ayarlanması performansı etkilemez.

COPY ifadesinin kullanılması aşağıdaki yapılandırmayı destekler:

1. **Kaynak bağlı hizmeti ve biçimi** aşağıdaki türler ve kimlik doğrulama yöntemleriyle aynıdır:

    | Desteklenen kaynak veri deposu türü                             | Desteklenen biçim           | Desteklenen kaynak kimlik doğrulama türü                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [Azure Blob](connector-azure-blob-storage.md)                | [Sınırlandırılmış metin](format-delimited-text.md)             | Hesap anahtarı kimlik doğrulaması, paylaşılan erişim imzası kimlik doğrulaması, hizmet sorumlusu kimlik doğrulaması, yönetilen kimlik doğrulama |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | Hesap anahtarı kimlik doğrulaması, paylaşılan erişim imzası kimlik doğrulaması |
    | &nbsp;                                                       | [ORC](format-orc.md)                        | Hesap anahtarı kimlik doğrulaması, paylaşılan erişim imzası kimlik doğrulaması |
    | [Azure Data Lake Storage 2. Nesil](connector-azure-data-lake-storage.md) | [Sınırlandırılmış metin](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[ORC](format-orc.md) | Hesap anahtarı kimlik doğrulaması, hizmet sorumlusu kimlik doğrulaması, yönetilen kimlik kimlik doğrulaması |

    >[!IMPORTANT]
    >- Depolama bağlı hizmetiniz için yönetilen kimlik kimlik doğrulaması kullandığınızda, [Azure Blob](connector-azure-blob-storage.md#managed-identity) için gerekli konfigürasyonları ve [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md#managed-identity) sırasıyla öğrenin.
    >- Azure depolama alanı VNet hizmet uç noktası ile yapılandırıldıysa, depolama hesabında "Güvenilen Microsoft hizmeti 'ne izin ver" özelliği etkinleştirilmiş olarak yönetilen kimlik kimlik doğrulamasını kullanmanız gerekir. [Azure depolama Ile VNET hizmet uç noktaları kullanmanın etkileri](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage).

2. Biçim ayarları şunlardır:

   1. **Parquet**: `compression` **sıkıştırma**, **Snappy** veya olabilir **``GZip``** .
   2. **Orc** için: `compression` **Compression**, **```zlib```** veya **Snappy** olabilir.
   3. **Sınırlandırılmış metin** için:
      1. `rowDelimiter` açık olarak **tek karakter** veya "**\r\n**" olarak ayarlanmıştır, varsayılan değer desteklenmez.
      2. `nullValue` Varsayılan olarak bırakılır veya **boş dize** ("") olarak ayarlanır.
      3. `encodingName` Varsayılan olarak bırakılır veya **UTF-8 veya UTF-16** olarak ayarlanır.
      4. `escapeChar` ile aynı olmalı `quoteChar` ve boş olmamalıdır.
      5. `skipLineCount` Varsayılan olarak bırakılır veya 0 olarak ayarlanır.
      6. `compression` sıkıştırma veya **yok** olabilir **``GZip``** .

3. Kaynağınız bir klasörse, `recursive` kopyalama etkinliği ' nde true olarak ayarlanmalıdır ve `wildcardFilename` olması gerekir `*` . 

4. `wildcardFolderPath` , `wildcardFilename` (dışındaki `*` ),, `modifiedDateTimeStart` , `modifiedDateTimeEnd` `prefix` `enablePartitionDiscovery` ve `additionalColumns` belirtilmedi.

Kopyalama etkinliğinde aşağıdaki COPY deyimin ayarları desteklenir `allowCopyCommand` :

| Özellik          | Açıklama                                                  | Gerekli                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| defaultValues | Azure SYNAPSE Analytics 'te her bir hedef sütun için varsayılan değerleri belirtir.  Özelliğindeki varsayılan değerler, veri ambarında ayarlanan varsayılan kısıtlamanın üzerine yazılır ve kimlik sütunu varsayılan değere sahip olamaz. | No |
| additionalOptions | [Copy deyimindeki](/sql/t-sql/statements/copy-into-transact-sql)"with" yan tümcesinde doğrudan bir Azure SYNAPSE Analytics Copy ifadesine geçirilecek ek seçenekler. COPY deyimlerine göre hizalamak için değeri gereken şekilde tırnak içine edin. | No |

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaCOPY",
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
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowCopyCommand": true,
                "copyCommandSettings": {
                    "defaultValues": [
                        {
                            "columnName": "col_string",
                            "defaultValue": "DefaultStringValue"
                        }
                    ],
                    "additionalOptions": {
                        "MAXERRORS": "10000",
                        "DATEFORMAT": "'ymd'"
                    }
                }
            },
            "enableSkipIncompatibleRow": true
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>Veri akışı özelliklerini eşleme

Eşleme veri akışındaki verileri dönüştürürken Azure SYNAPSE Analytics 'teki tabloları okuyabilir ve yazabilirsiniz. Daha fazla bilgi için bkz. veri akışlarını eşleme içindeki [kaynak dönüştürme](data-flow-source.md) ve [Havuz dönüşümü](data-flow-sink.md) .

### <a name="source-transformation"></a>Kaynak dönüştürme

Azure SYNAPSE Analytics 'e özgü ayarlar, kaynak dönüşümünün **kaynak seçenekleri** sekmesinde bulunabilir.

**Giriş** Kaynağınızı bir tabloya (eşdeğerini) işaret edip etmeyeceğinizi seçin ```Select * from <table-name>``` ya da özel BIR SQL sorgusu girin.

**Hazırlamayı etkinleştir** Azure SYNAPSE Analytics kaynaklarıyla üretim iş yükleri için bu seçeneği kullanmanız önemle önerilir. Bir işlem hattından Azure SYNAPSE Analytics kaynaklarıyla bir [veri akışı etkinliği](control-flow-execute-data-flow-activity.md) YÜRÜTTÜĞÜNÜZDE, ADF sizden bir hazırlama konumu depolama hesabı ister ve bunu hazırlanan veri yüklemesi için kullanır. Azure SYNAPSE Analytics 'ten veri yüklemeye en hızlı bir mekanizmadır.

- Depolama bağlı hizmetiniz için yönetilen kimlik kimlik doğrulaması kullandığınızda, [Azure Blob](connector-azure-blob-storage.md#managed-identity) için gerekli konfigürasyonları ve [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md#managed-identity) sırasıyla öğrenin.
- Azure depolama alanı VNet hizmet uç noktası ile yapılandırıldıysa, depolama hesabında "Güvenilen Microsoft hizmeti 'ne izin ver" özelliği etkinleştirilmiş olarak yönetilen kimlik kimlik doğrulamasını kullanmanız gerekir. [Azure depolama Ile VNET hizmet uç noktaları kullanmanın etkileri](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage).
- Kaynak olarak Azure SYNAPSE **sunucusuz** SQL havuzu kullandığınızda, hazırlama etkinleştirme desteklenmez.

**Sorgu**: giriş alanında sorgu ' yı seçerseniz, kaynağınız IÇIN bir SQL sorgusu girin. Bu ayar, veri kümesinde seçtiğiniz tüm tabloları geçersiz kılar. **Order by** yan tümceleri burada desteklenmez, ancak BIR tam select from ifadesini ayarlayabilirsiniz. Kullanıcı tanımlı tablo işlevleri de kullanabilirsiniz. **select * from udfGetData ()** , bir tablo döndüren SQL 'de bir UDF 'dir. Bu sorgu, veri akışınızda kullanabileceğiniz bir kaynak tablosu oluşturur. Sorguların kullanılması, test veya aramalar için satırları azaltmanın harika bir yoludur.

SQL örneği: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Toplu iş boyutu**: büyük verileri okuma işleminde öbek için bir toplu iş boyutu girin. Veri akışlarında, ADF bu ayarı Spark sütunlu önbelleğe alma ayarlamak için kullanır. Bu bir seçenek alanıdır ve boş bırakılırsa Spark varsayılanlarını kullanır.

**Yalıtım düzeyi**: eşleme VERI akışındaki SQL kaynakları için varsayılan değer read UNCOMMITTED ' dır. Yalıtım düzeyini buradaki değerlerden birine değiştirebilirsiniz:

- Okuma Işlendi
- Kaydedilmeyen oku
- Yinelenebilir okuma
- Seri hale getirilebilir
- Hiçbiri (yalıtım düzeyini yoksay)

![Yalıtım düzeyi](media/data-flow/isolationlevel.png)

### <a name="sink-transformation"></a>Havuz dönüştürme

Azure SYNAPSE Analytics 'e özgü ayarlar, havuz dönüşümünün **Ayarlar** sekmesinde bulunur.

**Güncelleştirme yöntemi:** Veritabanı Hedefinizdeki hangi işlemlere izin verileceğini belirler. Varsayılan değer yalnızca eklemeleri izin verir. Satırları güncelleştirmek, kaldırmak veya silmek için, bu eylemler için satırları etiketlemek üzere alter-Row dönüşümü gereklidir. Güncelleştirmeler, yukarı ve silme için bir anahtar sütunu veya sütunları ayarlanacak satırı belirleyecek şekilde ayarlanmalıdır.

**Tablo eylemi:** Yazmadan önce hedef tablodaki tüm satırların yeniden oluşturulup kaldırılacağını belirler.

- Hiçbiri: tabloya hiçbir eylem yapılmaz.
- Yeniden oluştur: tablo bırakılır ve yeniden oluşturulur. Dinamik olarak yeni bir tablo oluşturuluyoruz gereklidir.
- Kes: hedef tablodaki tüm satırlar kaldırılacak.

**Hazırlamayı etkinleştir:** Bu, Copy komutu kullanılarak Azure SYNAPSE Analytics SQL havuzlarının yüklenmesine izin verebilir ve çoğu Synpase havuzları için önerilir. Hazırlama depolaması [veri akışını Yürüt etkinliğinde](control-flow-execute-data-flow-activity.md)yapılandırılır. 

- Depolama bağlı hizmetiniz için yönetilen kimlik kimlik doğrulaması kullandığınızda, [Azure Blob](connector-azure-blob-storage.md#managed-identity) için gerekli konfigürasyonları ve [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md#managed-identity) sırasıyla öğrenin.
- Azure depolama alanı VNet hizmet uç noktası ile yapılandırıldıysa, depolama hesabında "Güvenilen Microsoft hizmeti 'ne izin ver" özelliği etkinleştirilmiş olarak yönetilen kimlik kimlik doğrulamasını kullanmanız gerekir. [Azure depolama Ile VNET hizmet uç noktaları kullanmanın etkileri](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage).

**Toplu iş boyutu**: her bir sepete kaç satır yazıldığını denetler. Daha büyük toplu işlem boyutları sıkıştırma ve bellek iyileştirmeyi iyileştirir, ancak verileri önbelleğe alırken bellek dışında özel durumlar riskini ortadan kaldıracak.

**SQL betiklerini ön ve sonrası**: (ön işleme) ve sonra (işlem sonrası) verileri havuz veritabanınıza yazıldıktan sonra yürütülecek çok satırlı SQL betikleri girin

![SQL işleme betikleri ön ve sonrası](media/data-flow/prepost1.png "SQL işleme betikleri")

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="getmetadata-activity-properties"></a>GetMetadata etkinlik özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [GetMetadata etkinliğini](control-flow-get-metadata-activity.md) denetleyin

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Azure SYNAPSE Analytics için veri türü eşlemesi

Veya Azure SYNAPSE Analytics 'ten veri kopyaladığınızda, Azure SYNAPSE Analytics veri türleri arasında, geçici veri türlerini Azure Data Factory için aşağıdaki eşlemeler kullanılır. Kopyalama etkinliğinin kaynak şemayı ve veri türünü havuza nasıl eşlediğini öğrenmek için bkz. [şema ve veri türü eşlemeleri](copy-activity-schema-and-type-mapping.md) .

>[!TIP]
>Azure SYNAPSE Analytics tarafından desteklenen veri türleri ve desteklenmeyen çözümler için [Azure SYNAPSE Analytics makalesindeki tablo veri türleri](../synapse-analytics/sql/develop-tables-data-types.md) bölümüne bakın.

| Azure SYNAPSE Analytics veri türü    | Data Factory geçici veri türü |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| ikili                                | Byte []                         |
| bit                                   | Boole                        |
| char                                  | Dize, Char []                 |
| date                                  | DateTime                       |
| Tarih saat                              | DateTime                       |
| datetime2                             | DateTime                       |
| Türünde                        | DateTimeOffset                 |
| Ondalık                               | Ondalık                        |
| FıLESTREAM özniteliği (varbinary (max)) | Byte []                         |
| Float                                 | Çift                         |
| image                                 | Byte []                         |
| int                                   | Int32                          |
| etmenize                                 | Ondalık                        |
| nchar                                 | Dize, Char []                 |
| sayısal                               | Ondalık                        |
| nvarchar                              | Dize, Char []                 |
| real                                  | Tek                         |
| rowversion                            | Byte []                         |
| girişin                         | DateTime                       |
| smallint                              | Int16                          |
| küçük para                            | Ondalık                        |
| time                                  | TimeSpan                       |
| tinyint                               | Bayt                           |
| uniqueidentifier                      | Guid                           |
| ikili                             | Byte []                         |
| varchar                               | Dize, Char []                 |

## <a name="next-steps"></a>Sonraki adımlar

Azure Data Factory ' de kopyalama etkinliğine göre kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları ve biçimleri](copy-activity-overview.md#supported-data-stores-and-formats).
