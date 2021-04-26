---
title: Azure SQL yönetilen örneği 'nde verileri kopyalama ve dönüştürme
description: Azure Data Factory kullanarak Azure SQL yönetilen örneği 'nde verileri kopyalamayı ve dönüştürmeyi öğrenin.
ms.service: data-factory
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: eae085a73e8f43813aa3f02fa910c7931f10f36c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597428"
---
# <a name="copy-and-transform-data-in-azure-sql-managed-instance-by-using-azure-data-factory"></a>Azure Data Factory kullanarak Azure SQL yönetilen örneğindeki verileri kopyalama ve dönüştürme

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, Azure SQL yönetilen örneği 'nden ve bu örnekten veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir ve veri akışı kullanarak Azure SQL yönetilen örneğindeki verileri dönüştürebilirsiniz. Azure Data Factory hakkında bilgi edinmek için [tanıtım makalesini](introduction.md)okuyun.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu SQL yönetilen örnek Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Desteklenen kaynak/havuz matrisi](copy-activity-overview.md) ile [kopyalama etkinliği](copy-activity-overview.md)
- [Veri akışını eşleme](concepts-data-flow-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)
- [GetMetadata etkinliği](control-flow-get-metadata-activity.md)

Kopyalama etkinliği için bu Azure SQL Veritabanı Bağlayıcısı şu işlevleri destekler:

- Azure kaynakları için hizmet sorumlusu veya yönetilen kimlikler ile SQL kimlik doğrulaması ve Azure Active Directory (Azure AD) uygulama belirteci kimlik doğrulaması kullanarak veri kopyalama.
- Kaynak olarak, bir SQL sorgusu veya saklı yordam kullanarak verileri alma. Ayrıca, SQL MI kaynağından paralel kopyalama seçeneğini de belirleyebilirsiniz. Ayrıntılar için [SQL mi 'Den paralel kopyalama](#parallel-copy-from-sql-mi) bölümüne bakın.
- Havuz olarak, kaynak şemaya bağlı değilse, hedef tablo otomatik olarak oluşturuluyor; verileri bir tabloya ekleme veya kopyalama sırasında özel mantık ile saklı yordam çağırma.

>[!NOTE]
> SQL yönetilen örneği [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) artık bu bağlayıcı tarafından desteklenmiyor. Geçici bir çözüm için, şirket içinde barındırılan bir tümleştirme çalışma zamanı aracılığıyla [Genel BIR ODBC Bağlayıcısı](connector-odbc.md) ve SQL Server ODBC sürücüsü kullanabilirsiniz. [Always Encrypted bölümünü kullanarak](#using-always-encrypted) daha fazla bilgi edinin. 

## <a name="prerequisites"></a>Önkoşullar

SQL yönetilen örnek [genel uç noktasına](../azure-sql/managed-instance/public-endpoint-overview.md)erişmek için Azure Data Factory yönetilen bir Azure tümleştirme çalışma zamanı kullanabilirsiniz. Azure Data Factory veritabanınıza bağlanabilmesi için, genel uç noktasını etkinleştirdiğinizden ve ağ güvenlik grubunda ortak uç nokta trafiğine izin verdiğinizden emin olun. Daha fazla bilgi için [bu kılavuza](../azure-sql/managed-instance/public-endpoint-configure.md)bakın.

SQL yönetilen örnek özel uç noktasına erişmek için, veritabanına erişebilen, [Şirket içinde barındırılan bir tümleştirme çalışma zamanı](create-self-hosted-integration-runtime.md) ayarlayın. Şirket içinde barındırılan tümleştirme çalışma zamanını yönetilen örneğinizle aynı sanal ağda sağlıyorsanız, tümleştirme çalışma zamanı makinenizin yönetilen örneğinizden farklı bir alt ağda yer aldığından emin olun. Şirket içinde barındırılan tümleştirme çalışma zamanınızı yönetilen örneğinizden farklı bir sanal ağda sağlıyorsanız, sanal ağ bağlantısına bir sanal ağ eşlemesi veya sanal ağ kullanabilirsiniz. Daha fazla bilgi için bkz. [UYGULAMANıZı SQL yönetilen örneğine bağlama](../azure-sql/managed-instance/connect-application-instance.md).

## <a name="get-started"></a>başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, SQL yönetilen örnek bağlayıcısına özgü Azure Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılar sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

SQL yönetilen örnek bağlı hizmeti için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Type özelliği **Azuressqlmı** olarak ayarlanmalıdır. | Yes |
| Dizisi |Bu özellik SQL kimlik doğrulaması kullanılarak SQL yönetilen örneğine bağlanmak için gereken **ConnectionString** bilgilerini belirtir. Daha fazla bilgi için aşağıdaki örneklere bakın. <br/>Varsayılan bağlantı noktası 1433'tür. Ortak bir uç nokta ile SQL yönetilen örneği kullanıyorsanız, 3342 numaralı bağlantı noktasını açıkça belirtin.<br> Ayrıca, Azure Key Vault bir parola koyabilirsiniz. SQL kimlik doğrulaması ise, `password` yapılandırmayı bağlantı dizesinin dışına çekin. Daha fazla bilgi için, Azure Key Vault tablo ve [Mağaza kimlik bilgilerini](store-credentials-in-key-vault.md)izleyen JSON örneğine bakın. |Yes |
| Serviceprincipalıd | Uygulamanın istemci KIMLIĞINI belirtin. | Evet, bir hizmet sorumlusu ile Azure AD kimlik doğrulaması kullandığınızda |
| Servicesprincipalkey | Uygulamanın anahtarını belirtin. Azure Data Factory güvenli bir şekilde depolamak veya [Azure Key Vault depolanan bir gizli dizi başvurusunda bulunmak](store-credentials-in-key-vault.md)için bu alanı **SecureString** olarak işaretleyin. | Evet, bir hizmet sorumlusu ile Azure AD kimlik doğrulaması kullandığınızda |
| Kiracı | Uygulamanızın bulunduğu etki alanı adı veya kiracı KIMLIĞI gibi kiracı bilgilerini belirtin. Fareyi, Azure portal sağ üst köşesine getirerek alın. | Evet, bir hizmet sorumlusu ile Azure AD kimlik doğrulaması kullandığınızda |
| Azurecses türü | Hizmet sorumlusu kimlik doğrulaması için, Azure AD uygulamanızın kaydedildiği Azure bulut ortamının türünü belirtin. <br/> İzin verilen değerler **Azucumhuriyeti**, **AzureChina**, **AzureUsGovernment** ve **AzureGermany**. Varsayılan olarak, Data Factory 'nin bulut ortamı kullanılır. | No |
| connectVia | Bu [tümleştirme çalışma zamanı](concepts-integration-runtime.md) , veri deposuna bağlanmak için kullanılır. Yönetilen örneğinizin ortak bir uç noktası varsa ve Azure Data Factory erişmesine izin veriyorsa şirket içinde barındırılan tümleştirme çalışma zamanını veya bir Azure tümleştirme çalışma zamanı kullanabilirsiniz. Belirtilmemişse, varsayılan Azure tümleştirme çalışma zamanı kullanılır. |Yes |

Farklı kimlik doğrulama türleri için sırasıyla Önkoşullar ve JSON örnekleri hakkında aşağıdaki bölümlere bakın:

- [SQL kimlik doğrulaması](#sql-authentication)
- [Azure AD uygulama belirteci kimlik doğrulaması: hizmet sorumlusu](#service-principal-authentication)
- [Azure AD uygulama belirteci kimlik doğrulaması: Azure kaynakları için Yönetilen kimlikler](#managed-identity)

### <a name="sql-authentication"></a>SQL kimlik doğrulaması

**Örnek 1: SQL kimlik doğrulaması kullanma**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Örnek 2: Azure Key Vault bir parolayla SQL kimlik doğrulaması kullanma**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
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

1. [Yönetilen örneğiniz için Azure Active Directory Yöneticisi sağlamak](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)için adımları izleyin.

2. Azure portal [bir Azure Active Directory uygulaması oluşturun](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) . Uygulama adını ve bağlı hizmeti tanımlayan aşağıdaki değerleri unutmayın:

    - Uygulama Kimliği
    - Uygulama anahtarı
    - Kiracı Kimliği

3. Azure Data Factory yönetilen kimlik için [oturum açma bilgileri oluşturun](/sql/t-sql/statements/create-login-transact-sql) . SQL Server Management Studio (SSMS) ' de, bir **sysadmin** olan SQL Server bir hesabı kullanarak yönetilen örneğe bağlanın. **Ana** veritabanında aşağıdaki T-SQL ' i çalıştırın:

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. Azure Data Factory yönetilen kimlik için [Kapsanan Veritabanı kullanıcıları oluşturun](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) . Veri kopyalamak istediğiniz veya ' dan veritabanına bağlanın, aşağıdaki T-SQL ' i çalıştırın: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. SQL kullanıcıları ve diğerleri için normalde yaptığınız gibi Data Factory yönetilen kimliğe gerekli izinleri verin. Aşağıdaki kodu çalıştırın. Daha fazla seçenek için [Bu belgeye](/sql/t-sql/statements/alter-role-transact-sql)bakın.

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. Azure Data Factory bir SQL yönetilen örnek bağlı hizmetini yapılandırın.

**Örnek: hizmet sorumlusu kimlik doğrulamasını kullanma**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;",
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

Veri Fabrikası, belirli veri fabrikasını temsil eden [Azure kaynakları için yönetilen bir kimlikle](data-factory-service-identity.md) ilişkilendirilebilir. Bu yönetilen kimliği, SQL yönetilen örnek kimlik doğrulaması için kullanabilirsiniz. Belirlenen fabrika, bu kimliği kullanarak, veritabanınıza veya veritabanına erişebilir ve veri kopyalayabilir.

Yönetilen kimlik kimlik doğrulamasını kullanmak için aşağıdaki adımları izleyin.

1. [Yönetilen örneğiniz için Azure Active Directory Yöneticisi sağlamak](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)için adımları izleyin.

2. Azure Data Factory yönetilen kimlik için [oturum açma bilgileri oluşturun](/sql/t-sql/statements/create-login-transact-sql) . SQL Server Management Studio (SSMS) ' de, bir **sysadmin** olan SQL Server bir hesabı kullanarak yönetilen örneğe bağlanın. **Ana** veritabanında aşağıdaki T-SQL ' i çalıştırın:

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. Azure Data Factory yönetilen kimlik için [Kapsanan Veritabanı kullanıcıları oluşturun](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) . Veri kopyalamak istediğiniz veya ' dan veritabanına bağlanın, aşağıdaki T-SQL ' i çalıştırın: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. SQL kullanıcıları ve diğerleri için normalde yaptığınız gibi Data Factory yönetilen kimliğe gerekli izinleri verin. Aşağıdaki kodu çalıştırın. Daha fazla seçenek için [Bu belgeye](/sql/t-sql/statements/alter-role-transact-sql)bakın.

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. Azure Data Factory bir SQL yönetilen örnek bağlı hizmetini yapılandırın.

**Örnek: yönetilen kimlik kimlik doğrulamasını kullanır**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamak için kullanılabilecek bölümlerin ve özelliklerin tam listesi için bkz. veri kümeleri makalesi. Bu bölüm, SQL yönetilen örnek veri kümesi tarafından desteklenen özelliklerin bir listesini sağlar.

SQL yönetilen örneğinden veri kopyalamak için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Veri kümesinin Type özelliği **Azuressqlmıtable** olarak ayarlanmalıdır. | Yes |
| schema | Şemanın adı. |Kaynak için Hayır, havuz için Evet  |
| tablo | Tablo/görünüm adı. |Kaynak için Hayır, havuz için Evet  |
| tableName | Şema ile tablonun/görünümün adı. Bu özellik geriye dönük uyumluluk için desteklenir. Yeni iş yükü için `schema` ve kullanın `table` . | Kaynak için Hayır, havuz için Evet |

**Örnek**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "AzureSqlMITable",
        "linkedServiceName": {
            "referenceName": "<SQL Managed Instance linked service name>",
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

Etkinlikleri tanımlamak için kullanılabilecek bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md) makalesi. Bu bölüm, SQL yönetilen örnek kaynağı ve havuzu tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="sql-managed-instance-as-a-source"></a>Kaynak olarak SQL yönetilen örneği

>[!TIP]
>Veri bölümlemesini kullanarak SQL MI 'den verileri verimli bir şekilde yüklemek için, [SQL mi 'Den paralel kopyadan](#parallel-copy-from-sql-mi)daha fazla bilgi edinin.

SQL yönetilen örneğinden veri kopyalamak için, etkinlik kaynağını kopyalama bölümünde aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının Type özelliği **Sqlmisource** olarak ayarlanmalıdır. | Yes |
| sqlReaderQuery |Bu özellik, verileri okumak için özel SQL sorgusu kullanır. `select * from MyTable` bunun bir örneğidir. |No |
| sqlReaderStoredProcedureName |Bu özellik, kaynak tablodaki verileri okuyan saklı yordamın adıdır. Son SQL ifadesinin saklı yordamda bir SELECT ifadesinin olması gerekir. |No |
| storedProcedureParameters |Bu parametreler, saklı yordama yöneliktir.<br/>İzin verilen değerler ad veya değer çiftleridir. Parametrelerin adları ve büyük harfleri, saklı yordam parametrelerinin adlarıyla ve büyük harfleriyle aynı olmalıdır. |No |
| 'Sinden | SQL kaynağı için işlem kilitleme davranışını belirtir. İzin verilen değerler: **ReadCommitted**, **READUNCOMMITTED**, **RepeatableRead**, **Serializable**, **Snapshot**. Belirtilmemişse, veritabanının varsayılan yalıtım düzeyi kullanılır. Daha fazla ayrıntı için [Bu belgeye](/dotnet/api/system.data.isolationlevel) başvurun. | No |
| partitionOptions | SQL MI 'den veri yüklemek için kullanılan veri bölümleme seçeneklerini belirtir. <br>İzin verilen değerler: **none** (default), **Physicalpartitionsoftable** ve **DynamicRange**.<br>Bir bölüm seçeneği etkinleştirildiğinde (değil `None` ), SQL mı 'den eşzamanlı olarak veri yükleme derecesi, [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) kopyalama etkinliğindeki ayar tarafından denetlenir. | No |
| partitionSettings | Veri bölümleme için ayarların grubunu belirtin. <br>Bölüm seçeneği olmadığında uygulayın `None` . | No |
| ***Altında `partitionSettings` :*** | | |
| partitionColumnName |  `int` `smallint` `bigint` `date` `smalldatetime` `datetime` `datetime2` `datetimeoffset` Paralel kopya için Aralık bölümleme tarafından kullanılacak tamsayı veya tarih/DateTime türünde (,,,,,,, veya) kaynak sütunun adını belirtin. Belirtilmemişse, tablonun dizini veya birincil anahtarı otomatik olarak algılanır ve bölüm sütunu olarak kullanılır.<br>Bölüm seçeneği olduğunda uygulayın `DynamicRange` . Kaynak verileri almak için bir sorgu kullanırsanız,  `?AdfDynamicRangePartitionCondition ` WHERE yan tümcesinde kanca. Örnek için, [SQL veritabanı 'Ndan paralel kopyalama](#parallel-copy-from-sql-mi) bölümüne bakın. | No |
| Partitionüstsınırı | Bölüm aralığı bölme için bölüm sütununun en büyük değeri. Bu değer, tablodaki satırları filtrelemeye yönelik değil, bölümün ilerlemesine karar vermek için kullanılır. Tablodaki veya sorgu sonucundaki tüm satırlar bölümlenecek ve kopyalanabilir. Belirtilmemişse, kopyalama etkinliği değeri otomatik olarak algılar.  <br>Bölüm seçeneği olduğunda uygulayın `DynamicRange` . Örnek için, [SQL veritabanı 'Ndan paralel kopyalama](#parallel-copy-from-sql-mi) bölümüne bakın. | No |
| Partitionalme sınırı | Bölüm aralığı bölme için bölüm sütununun en küçük değeri. Bu değer, tablodaki satırları filtrelemeye yönelik değil, bölümün ilerlemesine karar vermek için kullanılır. Tablodaki veya sorgu sonucundaki tüm satırlar bölümlenecek ve kopyalanabilir. Belirtilmemişse, kopyalama etkinliği değeri otomatik olarak algılar.<br>Bölüm seçeneği olduğunda uygulayın `DynamicRange` . Örnek için, [SQL veritabanı 'Ndan paralel kopyalama](#parallel-copy-from-sql-mi) bölümüne bakın. | No |

**Aşağıdaki noktalara dikkat edin:**

- **Sqlmisource** Için **sqlreaderquery** belirtilmişse, kopyalama etkinliği verileri almak Için bu sorguyu SQL yönetilen örnek kaynağında çalıştırır. Saklı yordam parametreleri alırsa, **sqlReaderStoredProcedureName** ve **storedProcedureParameters** belirterek bir saklı yordam de belirtebilirsiniz.
- Verileri almak için kaynakta saklı yordam kullanırken, saklı yordamınız farklı bir parametre değeri geçirildiğinde farklı bir şema döndürüyor olarak tasarlanmışsa, hata ile karşılaşabilirsiniz veya kullanıcı arabiriminden şemayı içeri aktarırken ya da otomatik tablo oluşturma ile verileri SQL veritabanına kopyalarken beklenmedik sonucu görebilirsiniz.

**Örnek: bir SQL sorgusu kullanın**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Managed Instance input dataset name>",
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
                "type": "SqlMISource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Örnek: saklı yordam kullanma**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Managed Instance input dataset name>",
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
                "type": "SqlMISource",
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

**Saklı yordam tanımı**

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

### <a name="sql-managed-instance-as-a-sink"></a>Havuz olarak SQL yönetilen örneği

> [!TIP]
> [SQL yönetilen örneği 'ne veri yüklemek Için en iyi](#best-practice-for-loading-data-into-sql-managed-instance)uygulamalardan desteklenen yazma davranışları, konfigürasyonlar ve en iyi uygulamalar hakkında daha fazla bilgi edinin.

Verileri SQL yönetilen örneğine kopyalamak için, kopyalama etkinlik havuzu bölümünde aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği havuzunun Type özelliği **Sqlmisink** olarak ayarlanmalıdır. | Yes |
| Ön Copyscrıpt |Bu özellik, SQL yönetilen örneğine veri yazmadan önce, kopyalama etkinliğinin çalıştırılacağı bir SQL sorgusu belirtir. Her kopya çalıştırması için yalnızca bir kez çağrılır. Bu özelliği, önceden yüklenmiş verileri temizlemek için kullanabilirsiniz. |No |
| tableOption | Kaynak şemasına göre yoksa [Havuz tablosunun otomatik olarak oluşturulup](copy-activity-overview.md#auto-create-sink-tables) oluşturulmayacağını belirtir. Havuz, saklı yordamı belirttiğinde otomatik tablo oluşturma desteklenmez. İzin verilen değerler: `none` (varsayılan), `autoCreate` . |No |
| sqlWriterStoredProcedureName | Hedef tabloya kaynak verilerinin nasıl uygulanacağını tanımlayan saklı yordamın adı. <br/>Bu saklı yordam *toplu iş başına çağırılır*. Yalnızca bir kez çalıştırılan ve kaynak verilerle hiçbir şey olmayan işlemler için, örneğin, DELETE veya TRUNCATE, `preCopyScript` özelliğini kullanın.<br>[BIR SQL havuzundan saklı yordam çağırma](#invoke-a-stored-procedure-from-a-sql-sink)örneğine bakın. | No |
| storedProcedureTableTypeParameterName |Saklı yordamda belirtilen tablo türünün parametre adı.  |No |
| sqlWriterTableType |Saklı yordamda kullanılacak tablo türü adı. Kopyalama etkinliği, verileri bu tablo türüyle geçici bir tabloda kullanılabilir hale getirir. Saklı yordam kodu daha sonra mevcut verilerle Kopyalanmakta olan verileri birleştirebilir. |No |
| storedProcedureParameters |Saklı yordamın parametreleri.<br/>İzin verilen değerler ad ve değer çiftleridir. Parametrelerin adları ve büyük harfleri, saklı yordam parametrelerinin adlarıyla ve büyük küçük harfleriyle aynı olmalıdır. | No |
| writeBatchSize |*Toplu iş BAŞıNA* SQL tablosuna eklenecek satır sayısı.<br/>İzin verilen değerler, satır sayısı için tamsayılardır. Varsayılan olarak, Azure Data Factory satır boyutuna göre uygun toplu iş boyutunu dinamik olarak belirler.  |No |
| writeBatchTimeout |Bu özellik, toplu ekleme işleminin zaman aşımına uğramadan önce tamamlaması için bekleme süresini belirtir.<br/>İzin verilen değerler TimeSpan içindir. Örneğin, 30 dakika olan "00:30:00" bir örnektir. |No |
| maxConcurrentConnections |Etkinlik çalışması sırasında veri deposuna kurulan eşzamanlı bağlantıların üst sınırı. Yalnızca eş zamanlı bağlantıları sınırlandırmak istediğinizde bir değer belirtin.| No |

**Örnek 1: veri ekleme**

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Örnek 2: kopyalama sırasında saklı yordam çağırma**

[BIR SQL mı havuzundan saklı yordam çağırma](#invoke-a-stored-procedure-from-a-sql-sink)hakkında daha fazla bilgi edinin.

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="parallel-copy-from-sql-mi"></a>SQL MI 'den paralel kopya

Copy etkinliğinde Azure SQL yönetilen örnek Bağlayıcısı, verileri paralel olarak kopyalamak için yerleşik veri bölümlemesini sağlar. Kopyalama etkinliğinin **kaynak** sekmesinde veri bölümleme seçeneklerini bulabilirsiniz.

![Bölüm seçeneklerinin ekran görüntüsü](./media/connector-sql-server/connector-sql-partition-options.png)

Bölümlenmiş kopyayı etkinleştirdiğinizde kopyalama etkinliği, verileri bölümlere göre yüklemek için SQL MI kaynağınıza karşı paralel sorgular çalıştırır. Paralel derece [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) kopyalama etkinliğindeki ayar tarafından denetlenir. Örneğin, `parallelCopies` dört olarak ayarlarsanız, Data Factory aynı anda, belirtilen bölüm seçeneğiniz ve ayarlarınıza göre dört sorgu üretir ve çalışır ve her sorgu, SQL mı 'nizden verilerin bir kısmını alır.

SQL MI 'den büyük miktarda veri yüklediğinizde özellikle veri bölümleme ile paralel kopyayı etkinleştirmeniz önerilir. Farklı senaryolar için önerilen yapılandırma aşağıda verilmiştir. Dosya tabanlı veri deposuna veri kopyalarken, bir klasöre birden çok dosya (yalnızca klasör adı belirtin) yazılması önerilir, bu durumda performans tek bir dosyaya yazılmasından daha iyidir.

| Senaryo                                                     | Önerilen ayarlar                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Fiziksel bölümlerle büyük tablodan tam yük.        | **Bölüm seçeneği**: tablonun fiziksel bölümleri. <br><br/>Yürütme sırasında Data Factory fiziksel bölümleri otomatik olarak algılar ve verileri bölümlere göre kopyalar. <br><br/>Tablonuzun fiziksel bölümü olup olmadığını denetlemek için [Bu sorguya](#sample-query-to-check-physical-partition)başvurabilirsiniz. |
| Fiziksel bölümler olmadan, veri bölümlendirme için bir tamsayı veya tarih saat sütunuyla büyük tablodan tam yük. | **Bölüm seçenekleri**: Dinamik Aralık bölümü.<br>**Bölüm sütunu** (isteğe bağlı): verileri bölümlemek için kullanılan sütunu belirtin. Belirtilmezse, dizin veya birincil anahtar sütunu kullanılır.<br/>**Bölüm üst sınırı** ve **bölüm alt sınırı** (isteğe bağlı): Bölüm Ilerlemesiyle mı belirlemek istediğinizi belirtin. Bu, tablodaki satırları filtrelemeye yönelik değildir, tablodaki tüm satırlar bölümlenecek ve kopyalanır. Belirtilmemişse, kopyalama etkinliği değerleri otomatik olarak algılar.<br><br>Örneğin, "ID" adlı bölüm sütununuzu 1 ile 100 arasında değerler varsa ve alt sınırı 20 ve üst sınır olarak 80 olarak ayarlarsanız, paralel kopyalama Data Factory 4 ' e kadar, verileri 4 bölüm kimliği aralığında <= 20, [21, 50], [51, 80] ve >= 81 sırasıyla alır. |
| Fiziksel bölümler olmadan, veri bölümlendirme için bir tamsayı veya tarih/tarih/saat sütunuyla büyük miktarda veri yükleyin. | **Bölüm seçenekleri**: Dinamik Aralık bölümü.<br>**Sorgu**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Bölüm sütunu**: verileri bölümlemek için kullanılan sütunu belirtin.<br>**Bölüm üst sınırı** ve **bölüm alt sınırı** (isteğe bağlı): Bölüm Ilerlemesiyle mı belirlemek istediğinizi belirtin. Bu, tablodaki satırları filtrelemeye yönelik değildir, sorgu sonucundaki tüm satırlar bölümlenecek ve kopyalanır. Belirtilmemişse, kopyalama etkinliği değeri otomatik olarak algılar.<br><br>Yürütme sırasında Data Factory, `?AdfRangePartitionColumnName` her bölüm için gerçek sütun adı ve değer aralıklarıyla değiştirilir ve SQL mı 'ye gönderir. <br>Örneğin, "ID" adlı bölüm sütununuzu 1 ile 100 arasında değerler varsa ve alt sınırı 20 ve üst sınır olarak 80 olarak ayarlarsanız, paralel kopyalama Data Factory 4 ' e kadar, verileri 4 bölüm kimliği aralığında <= 20, [21, 50], [51, 80] ve >= 81 sırasıyla alır. <br><br>Farklı senaryolar için daha fazla örnek sorgu aşağıda verilmiştir:<br> 1. tüm tabloyu sorgulayın: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. sütun seçimi ve ek WHERE yan tümcesi filtreleri içeren bir tablodan sorgu: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. sorgular ile sorgu: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. alt sorguda bölüm ile sorgulama: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Bölüm seçeneğiyle verileri yüklemek için en iyi uygulamalar:

1. Veri eğriliğini önlemek için bölüm sütunu olarak (birincil anahtar veya benzersiz anahtar gibi) farklı bir sütun seçin. 
2. Tabloda yerleşik bölüm varsa, daha iyi performans sağlamak için "tablonun fiziksel bölümleri" Bölüm seçeneğini kullanın.    
3. Verileri kopyalamak için Azure Integration Runtime kullanırsanız daha fazla bilgi işlem kaynağı kullanmak için daha büyük "[veri tümleştirme birimleri (DIU)](copy-activity-performance-features.md#data-integration-units)" (>4) ayarlayabilirsiniz. İlgili senaryolara göz atın.
4. "[Kopya paralelliği derecesi](copy-activity-performance-features.md#parallel-copy)", Bölüm numaralarını denetler, bu sayının performansı karşılamamasından çok büyük bir şekilde ayarlanması, bu sayıyı (ya da şirket IÇINDE barındırılan IR düğümlerinin sayısı) * (2-4) olarak ayarlamayı öneririz.

**Örnek: fiziksel bölümlerle büyük tablodan tam yük**

```json
"source": {
    "type": "SqlMISource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Örnek: Dinamik Aralık bölümü ile sorgulama**

```json
"source": {
    "type": "SqlMISource",
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
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, pf.name AS PartitionFunctionName, c.name AS ColumnName, iif(pf.name is null, 'no', 'yes') AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id 
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id 
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id 
LEFT JOIN sys.partition_schemes ps ON i.data_space_id = ps.data_space_id 
LEFT JOIN sys.partition_functions pf ON pf.function_id = ps.function_id 
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

Tabloda fiziksel bölüm varsa, aşağıda gösterildiği gibi "HasPartition" öğesini "Yes" olarak görürsünüz.

![SQL sorgu sonucu](./media/connector-azure-sql-database/sql-query-result.png)

## <a name="best-practice-for-loading-data-into-sql-managed-instance"></a>SQL yönetilen örneğine veri yüklemek için en iyi uygulama

Verileri SQL yönetilen örneği 'ne kopyaladığınızda, farklı yazma davranışı gerekebilir:

- [Append](#append-data): Kaynak verilerinizde yalnızca yeni kayıtlar vardır.
- [Upsert](#upsert-data): Kaynak verilerinizde hem ekler hem de güncelleştirmeler vardır.
- [Üzerine yaz](#overwrite-the-entire-table): her seferinde boyut tablosunun tamamını yeniden yüklemek istiyorum.
- [Özel mantık Ile yaz](#write-data-with-custom-logic): hedef tabloya son ekleme yapmadan önce fazladan işleme ihtiyacım var. 

Azure Data Factory ve en iyi yöntemleri yapılandırmak için ilgili bölümlere bakın.

### <a name="append-data"></a>Veri Ekle

Verileri eklemek, SQL yönetilen örnek havuzu bağlayıcısının varsayılan davranışıdır. Azure Data Factory tablonuza verimli bir şekilde yazmak için toplu bir ekleme yapar. Kopyalama etkinliğinde kaynağı ve havuzu uygun şekilde yapılandırabilirsiniz.

### <a name="upsert-data"></a>Verileri upsert etme

**Seçenek 1:** Kopyalamak için büyük miktarda veriniz varsa, kopyalama etkinliğini kullanarak tüm kayıtları bir hazırlama tablosuna toplu olarak yükleyebilir, sonra bir bir çekde [birleştirme](/sql/t-sql/statements/merge-transact-sql) veya ekleme/güncelleştirme ifadesini uygulamak için bir saklı yordam etkinliği çalıştırabilirsiniz. 

Kopyalama etkinliği şu anda verileri veritabanı geçici tablosuna yüklemeyi yerel olarak desteklemiyor. Birden çok etkinliğin birleşimiyle ayarlanmasının gelişmiş bir yolu vardır ve [SQL veritabanı toplu ön Ekle senaryolarına en iyileştirme](https://github.com/scoriani/azuresqlbulkupsert)bölümüne bakın. Aşağıda, kalıcı bir tablonun hazırlama olarak kullanılması örneği gösterilmektedir.

Örnek olarak, Azure Data Factory, **saklı yordam etkinliği** ile **kopyalama etkinliği** zincirli bir işlem hattı oluşturabilirsiniz. Eski, kaynak deponuzdaki verileri, veri kümesindeki tablo adı olarak **UpsertStagingTable** gıbı Azure SQL yönetilen örnek hazırlama tablosuna kopyalar. İkinci olarak, hazırlama tablosundaki kaynak verileri hedef tabloya birleştirmek ve hazırlama tablosunu temizlemek için bir saklı yordam çağırır.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Veritabanınızda, önceki saklı yordam etkinliğinden işaret edilen aşağıdaki örnekte olduğu gibi, BIRLEŞTIRME mantığı ile bir saklı yordam tanımlayın. Hedefin üç sütunlu **Pazarlama** tablosu olduğunu varsayın: **ProfileId**, **State** ve **category**. **ProfileId** sütununu temel alarak büyük ölçüde yapın.

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING UpsertStagingTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE UpsertStagingTable
END
```

**Seçenek 2:** [Kopyalama etkinliği içinde bir saklı yordamı çağırmayı](#invoke-a-stored-procedure-from-a-sql-sink)seçebilirsiniz. Bu yaklaşım, `writeBatchSize` kopyalama etkinliğinde varsayılan yaklaşım olarak toplu ekleme kullanmak yerine kaynak tablodaki her bir toplu işi (özelliği tarafından yönetilir) çalıştırır.

### <a name="overwrite-the-entire-table"></a>Tüm tablonun üzerine yaz

Bir kopyalama etkinliği havuzunda **Precopyscript** özelliğini yapılandırabilirsiniz. Bu durumda, çalıştıran her kopyalama etkinliği için önce betiği çalıştırır Azure Data Factory. Ardından, verileri eklemek için kopyayı çalıştırır. Örneğin, en son verilerle tüm tablonun üzerine yazmak için, kaynaktan yeni verileri toplu yüklemeden önce tüm kayıtları silmek üzere bir komut dosyası belirtin.

### <a name="write-data-with-custom-logic"></a>Özel mantık ile veri yazma

Özel mantık ile veri yazma adımları, [upsert veri](#upsert-data) bölümünde açıklananlara benzerdir. Kaynak verilerin son olarak hedef tabloya eklenmesinin önüne ek işlem uygulamanız gerektiğinde, hazırlama tablosuna yükleyebilir, sonra saklı yordam etkinliğini çağırabilir veya veri uygulamak için kopyalama etkinlik havuzunda saklı bir yordam çağırabilirsiniz.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Bir SQL havuzundan saklı yordam çağırma

Verileri SQL yönetilen örneği 'ne kopyaladığınızda, kaynak tablodaki her yığın üzerinde ek parametrelerle Kullanıcı tarafından belirtilen bir saklı yordamı yapılandırabilir ve çağırabilirsiniz. Saklı yordam özelliği [tablo değerli parametrelerin](/dotnet/framework/data/adonet/sql/table-valued-parameters)avantajlarından yararlanır.

Yerleşik kopyalama mekanizmaları amaca uygun olmadığında, saklı bir yordam kullanabilirsiniz. Kaynak verilerin son olarak hedef tabloya eklenmesinin önüne daha fazla işlem uygulamak istediğinizde örnek bir örnektir. Bazı ek işleme örnekleri, sütunları birleştirmek, ek değerleri aramak ve birden fazla tabloya eklemek istebilmenizdir.

Aşağıdaki örnek, SQL Server veritabanındaki bir tabloya bir üsert yapmak için saklı yordamın nasıl kullanılacağını göstermektedir. Giriş verilerinin ve havuz **Pazarlama** tablosunun her birinin üç sütunu olduğunu varsayalım: **ProfileId**, **State** ve **category**. **ProfileId** sütununu temel alarak ve yalnızca "Producta" adlı belirli bir kategori için uygulayın.

1. Veritabanınızda, **Sqlwritertabletype** ile aynı ada sahip tablo türünü tanımlayın. Tablo türünün şeması, giriş verileriniz tarafından döndürülen şemayla aynıdır.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. Veritabanınızda, **sqlWriterStoredProcedureName** ile aynı ada sahip saklı yordamı tanımlayın. Belirtilen kaynağınızdan gelen giriş verilerini işler ve çıkış tablosu ile birleştirir. Saklı yordamdaki tablo türünün parametre adı, veri kümesinde tanımlanan **TableName** ile aynıdır.

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. Azure Data Factory, kopyalama etkinliğinde **SQL mı havuzu** bölümünü aşağıdaki gibi tanımlayın:

    ```json
    "sink": {
        "type": "SqlMISink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="mapping-data-flow-properties"></a>Veri akışı özelliklerini eşleme

Eşleme veri akışındaki verileri dönüştürürken Azure SQL yönetilen örneğinden tabloları okuyabilir ve yazabilirsiniz. Daha fazla bilgi için bkz. veri akışlarını eşleme içindeki [kaynak dönüştürme](data-flow-source.md) ve [Havuz dönüşümü](data-flow-sink.md) .

> [!NOTE]
> Eşleme veri akışı 'nda Azure SQL yönetilen örnek Bağlayıcısı Şu anda genel önizleme olarak kullanılabilir. Henüz özel uç noktaya değil, SQL yönetilen örnek genel uç noktasına bağlanabilirsiniz.

### <a name="source-transformation"></a>Kaynak dönüştürme

Aşağıdaki tabloda, Azure SQL yönetilen örnek kaynağı tarafından desteklenen özellikler listelenmiştir. Bu özellikleri **kaynak seçenekleri** sekmesinde düzenleyebilirsiniz.

| Ad | Açıklama | Gerekli | İzin verilen değerler | Veri akışı betiği özelliği |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tablo | Giriş olarak tablo ' yı seçerseniz veri akışı, veri kümesinde belirtilen tablodaki tüm verileri getirir. | No | - |- |
| Sorgu | Giriş olarak Sorgula ' yı seçerseniz, kaynaktan veri getirmek için bir SQL sorgusu belirtin. Bu, veri kümesinde belirttiğiniz tüm tabloları geçersiz kılar. Sorguları kullanmak, test veya arama için satırları azaltmanın harika bir yoludur.<br><br>**Order by** yan tümcesi desteklenmez, ancak BIR tam select from deyimi ayarlayabilirsiniz. Kullanıcı tanımlı tablo işlevleri de kullanabilirsiniz. **select * from udfGetData ()** , veri akışında kullanabileceğiniz bir tablo döndüren SQL 'de bir UDF 'dir.<br>Sorgu örneği: `Select * from MyTable where customerId > 1000 and customerId < 2000`| Hayır | Dize | sorgu |
| Toplu iş boyutu | Büyük verileri okuma işleminde öbek için bir toplu iş boyutu belirtin. | No | Tamsayı | batchSize |
| Yalıtım düzeyi | Aşağıdaki yalıtım düzeylerinden birini seçin:<br>-Kaydedilen okuma<br>-Read UNCOMMITTED (varsayılan)<br>-Yinelenebilir okuma<br>-Serileştirilebilir<br>-None (yalıtım düzeyini yoksay) | No | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERI hale GETIRILEBILIR<br/>SEÇIM</small> |'Sinden |

#### <a name="azure-sql-managed-instance-source-script-example"></a>Azure SQL yönetilen örnek kaynak betiği örneği

Azure SQL yönetilen örneğini kaynak türü olarak kullandığınızda, ilişkili veri akışı betiği şu şekilde olur:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from MYTABLE',
    format: 'query') ~> SQLMISource
```

### <a name="sink-transformation"></a>Havuz dönüştürme

Aşağıdaki tabloda, Azure SQL yönetilen örnek havuzu tarafından desteklenen özellikler listelenmiştir. Bu özellikleri, **havuz seçenekleri** sekmesinde düzenleyebilirsiniz.

| Ad | Açıklama | Gerekli | İzin verilen değerler | Veri akışı betiği özelliği |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Yöntemi Güncelleştir | Veritabanı Hedefinizdeki işlemlere izin verileceğini belirtin. Varsayılan değer yalnızca eklemeleri izin verir.<br>Satırları güncelleştirmek, kaldırmak veya silmek için, bu eylemler için satırları etiketlemek üzere bir [alter Row dönüşümü](data-flow-alter-row.md) gereklidir. | Yes | `true` veya `false` | siler <br/>eklenebilir <br/>güncellenebilir <br/>upsertable |
| Anahtar sütunlar | Güncelleştirmeler, üst üste ve siler için, hangi satırın ekleneceğini belirleyen anahtar sütunlar ayarlanmalıdır.<br>Anahtar olarak seçtiğiniz sütun adı, sonraki güncelleştirme, upsert, DELETE 'in bir parçası olarak kullanılacaktır. Bu nedenle, havuz eşlemesinde var olan bir sütun seçmeniz gerekir. | No | Dizi | keys |
| Anahtar sütunları yazmayı atla | Değeri anahtar sütununa yazmak istiyorsanız "anahtar sütunlarını yazmayı atla" seçeneğini belirleyin. | No | `true` veya `false` | Skipkeyyazmaları |
| Tablo eylemi |Yazmadan önce hedef tablodaki tüm satırların yeniden oluşturulup kaldırılacağını belirler.<br>- **Hiçbiri**: tabloya hiçbir eylem yapılmaz.<br>- **Yeniden oluştur**: tablo bırakılır ve yeniden oluşturulur. Dinamik olarak yeni bir tablo oluşturuluyoruz gereklidir.<br>- **Kes**: hedef tablodaki tüm satırlar kaldırılacak. | No | `true` veya `false` | Oluştur<br/>kesilemedi |
| Toplu iş boyutu | Her bir toplu işte kaç satır yazıldığını belirtin. Daha büyük toplu işlem boyutları sıkıştırma ve bellek iyileştirmeyi iyileştirir, ancak verileri önbelleğe alırken bellek dışında özel durumlar riskini ortadan kaldıracak. | No | Tamsayı | batchSize |
| SQL betikleri öncesi ve sonrası | (Ön işleme) ve sonra (işlem sonrası) verileri havuz veritabanınıza yazıldıktan önce yürütülecek çok satırlı SQL betikleri belirtin. | Hayır | Dize | preSQLs<br>postSQLs |

#### <a name="azure-sql-managed-instance-sink-script-example"></a>Azure SQL yönetilen örnek havuzu betiği örneği

Azure SQL yönetilen örneğini havuz türü olarak kullandığınızda, ilişkili veri akışı betiği şu şekilde olur:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:true,
    updateable:true,
    upsertable:true,
    keys:['keyColumn'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SQLMISink
```

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="getmetadata-activity-properties"></a>GetMetadata etkinlik özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [GetMetadata etkinliğini](control-flow-get-metadata-activity.md) denetleyin 

## <a name="data-type-mapping-for-sql-managed-instance"></a>SQL yönetilen örneği için veri türü eşlemesi

Kopyalama etkinliği kullanılarak SQL yönetilen örneği 'ne ve veri kopyalandığı zaman, aşağıdaki eşlemeler SQL yönetilen örnek veri türlerinden, geçici veri türlerini Azure Data Factory için kullanılır. Kopyalama etkinliğinin kaynak şemadan ve veri türünden havuza nasıl eşlendiğini öğrenmek için bkz. [şema ve veri türü eşlemeleri](copy-activity-schema-and-type-mapping.md).

| SQL yönetilen örnek veri türü | Azure Data Factory geçici veri türü |
|:--- |:--- |
| bigint |Int64 |
| ikili |Byte [] |
| bit |Boole |
| char |Dize, Char [] |
| date |DateTime |
| Tarih saat |DateTime |
| datetime2 |DateTime |
| Türünde |DateTimeOffset |
| Ondalık |Ondalık |
| FıLESTREAM özniteliği (varbinary (max)) |Byte [] |
| Float |Çift |
| image |Byte [] |
| int |Int32 |
| etmenize |Ondalık |
| nchar |Dize, Char [] |
| n |Dize, Char [] |
| sayısal |Ondalık |
| nvarchar |Dize, Char [] |
| real |Tek |
| rowversion |Byte [] |
| girişin |DateTime |
| smallint |Int16 |
| küçük para |Ondalık |
| sql_variant |Nesne |
| metin |Dize, Char [] |
| time |TimeSpan |
| timestamp |Byte [] |
| tinyint |Int16 |
| uniqueidentifier |Guid |
| ikili |Byte [] |
| varchar |Dize, Char [] |
| xml |Dize |

>[!NOTE]
> Ondalık geçici türle eşlenen veri türleri için şu anda kopyalama etkinliği, 28 ' ye kadar duyarlık destekler. 28 ' den daha büyük bir duyarlık gerektiren verileriniz varsa, bir SQL sorgusunda bir dizeye dönüştürmeyi düşünün.

## <a name="using-always-encrypted"></a>Always Encrypted kullanma

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)Ile Azure SQL yönetilen örneğinden veri kopyaladığınızda, [Genel ODBC Bağlayıcısı](connector-odbc.md) ' nı ve SQL Server ODBC sürücüsünü şirket içinde barındırılan Integration Runtime aracılığıyla kullanın. Bu Azure SQL yönetilen örnek Bağlayıcısı şimdi Always Encrypted desteklemiyor. 

Daha ayrıntılı belirtmek gerekirse:

1. Kendi kendine barındırılan Integration Runtime yoksa ayarlayın. Ayrıntılar için bkz. [Şirket içinde barındırılan Integration Runtime](create-self-hosted-integration-runtime.md) makalesi.

2. SQL Server için 64 bit ODBC sürücüsünü [buradan](/sql/connect/odbc/download-odbc-driver-for-sql-server)indirin ve Integration Runtime makinesine yükleyin. Bu sürücünün [SQL Server ODBC sürücüsüyle Always Encrypted kullanarak](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver#using-the-azure-key-vault-provider)nasıl çalıştığı hakkında daha fazla bilgi edinin.

3. ODBC türü ile bağlı hizmet oluşturma SQL veritabanınıza bağlanmak için aşağıdaki örneklere bakın:

    - **SQL kimlik doğrulamasını** kullanmak IÇIN: ODBC bağlantı dizesini aşağıda gösterildiği gibi belirtin ve **temel** kimlik doğrulaması ' nı seçerek Kullanıcı adı ve parolasını ayarlayın.

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
        ```

    - Azure sanal makinesinde şirket içinde barındırılan Integration Runtime çalıştırırsanız, Azure VM 'nin kimliğiyle **yönetilen kimlik kimlik doğrulamasını** kullanabilirsiniz: 

        1. Yönetilen kimlik için veritabanı kullanıcısı oluşturmak ve veritabanınıza uygun rolü vermek için aynı [önkoşulları](#managed-identity) izleyin.
        2. Bağlı hizmet ' de, aşağıdaki gibi ODBC bağlantı dizesini belirtin ve bağlantı dizesinin kendisi gösterdiği şekilde **anonim** kimlik doğrulaması ' nı seçin `Authentication=ActiveDirectoryMsi` .

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>; Authentication=ActiveDirectoryMsi;
        ```

4. Veri kümesi ve kopyalama etkinliğini ODBC türü ile buna uygun olarak oluşturun. [ODBC Bağlayıcısı](connector-odbc.md) makalesinden daha fazla bilgi edinin.

## <a name="next-steps"></a>Sonraki adımlar
Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları](copy-activity-overview.md#supported-data-stores-and-formats).