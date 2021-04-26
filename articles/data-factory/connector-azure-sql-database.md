---
title: Azure SQL veritabanı 'nda verileri kopyalama ve dönüştürme
description: Azure SQL veritabanına veri kopyalamayı ve Azure SQL veritabanı 'nda Azure Data Factory kullanarak verileri dönüştürmeyi öğrenin.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/17/2021
ms.openlocfilehash: 75615b4bb8773d0c0b8f72278e5598462c779ceb
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365238"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Azure Data Factory kullanarak Azure SQL veritabanındaki verileri kopyalama ve dönüştürme

> [!div class="op_single_selector" title1="Kullanmakta olduğunuz Azure Data Factory sürümünü seçin:"]
>
> - [Sürüm 1](v1/data-factory-azure-sql-connector.md)
> - [Güncel sürüm](connector-azure-sql-database.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede, Azure SQL veritabanı 'ndan veri kopyalamak için Azure Data Factory kopyalama etkinliğinin nasıl kullanılacağı özetlenmektedir ve veri akışı kullanarak Azure SQL veritabanı 'nda verileri dönüştürebilirsiniz. Azure Data Factory hakkında bilgi edinmek için [tanıtım makalesini](introduction.md)okuyun.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Bu Azure SQL Veritabanı Bağlayıcısı aşağıdaki etkinlikler için desteklenir:

- [Etkinliği](copy-activity-overview.md) [Desteklenen kaynak/havuz matris](copy-activity-overview.md) tablosuyla Kopyala
- [Veri akışını eşleme](concepts-data-flow-overview.md)
- [Arama etkinliği](control-flow-lookup-activity.md)
- [GetMetadata etkinliği](control-flow-get-metadata-activity.md)

Kopyalama etkinliği için bu Azure SQL Veritabanı Bağlayıcısı şu işlevleri destekler:

- Azure kaynakları için hizmet sorumlusu veya yönetilen kimlikler ile SQL kimlik doğrulaması ve Azure Active Directory (Azure AD) uygulama belirteci kimlik doğrulaması kullanarak veri kopyalama.
- Kaynak olarak, bir SQL sorgusu veya saklı yordam kullanarak verileri alma. Ayrıca, Azure SQL veritabanı kaynağından paralel kopyalama seçeneğini de belirleyebilirsiniz. Ayrıntılar için [SQL veritabanı 'Ndan paralel kopyalama](#parallel-copy-from-sql-database) bölümüne bakın.
- Havuz olarak, kaynak şemaya bağlı değilse, hedef tablo otomatik olarak oluşturuluyor; bir tabloya veri ekleme veya kopyalama sırasında özel mantık ile saklı yordam çağırma.

Azure SQL veritabanı [sunucusuz katmanı](../azure-sql/database/serverless-tier-overview.md)kullanıyorsanız, sunucu duraklatıldığında, otomatik özgeçmişin hazırlanmasını beklemek yerine etkinlik çalışmasının başarısız olup olmadığını aklınızda olun. Sunucunun gerçek yürütme üzerinde canlı olduğundan emin olmak için etkinlik yeniden deneme veya zincir ek etkinliklerini ekleyebilirsiniz.

>[!NOTE]
> Azure SQL veritabanı [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) artık bu bağlayıcı tarafından desteklenmiyor. Geçici bir çözüm için, şirket içinde barındırılan bir tümleştirme çalışma zamanı aracılığıyla [Genel BIR ODBC Bağlayıcısı](connector-odbc.md) ve SQL Server ODBC sürücüsü kullanabilirsiniz. [Always Encrypted bölümünü kullanarak](#using-always-encrypted) daha fazla bilgi edinin. 

> [!IMPORTANT]
> Azure Integration Runtime kullanarak verileri kopyalarsanız, Azure hizmetlerinin sunucuya erişebilmesi için [sunucu düzeyinde bir güvenlik duvarı kuralı](../azure-sql/database/firewall-configure.md) yapılandırın.
> Şirket içinde barındırılan tümleştirme çalışma zamanı kullanarak verileri kopyalarsanız, güvenlik duvarını uygun IP aralığına izin verecek şekilde yapılandırın. Bu Aralık, Azure SQL veritabanına bağlanmak için kullanılan makinenin IP 'sini içerir.

## <a name="get-started"></a>başlarken

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Aşağıdaki bölümlerde, bir Azure SQL veritabanı bağlayıcısına özgü Azure Data Factory varlıkları tanımlamak için kullanılan özellikler hakkında ayrıntılı bilgiler sağlanmaktadır.

## <a name="linked-service-properties"></a>Bağlı hizmet özellikleri

Bu özellikler, Azure SQL veritabanı bağlı hizmeti için desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | **Type** özelliği **Azuressqldatabase** olarak ayarlanmalıdır. | Yes |
| Dizisi | **ConnectionString** özelliği IÇIN Azure SQL veritabanı örneğine bağlanmak için gereken bilgileri belirtin. <br/>Ayrıca, Azure Key Vault bir parola veya hizmet sorumlusu anahtarı da koyabilirsiniz. SQL kimlik doğrulaması ise, `password` yapılandırmayı bağlantı dizesinin dışına çekin. Daha fazla bilgi için, Azure Key Vault tablo ve [Mağaza kimlik bilgilerini](store-credentials-in-key-vault.md)izleyen JSON örneğine bakın. | Yes |
| Serviceprincipalıd | Uygulamanın istemci KIMLIĞINI belirtin. | Evet, bir hizmet sorumlusu ile Azure AD kimlik doğrulaması kullandığınızda |
| Servicesprincipalkey | Uygulamanın anahtarını belirtin. Azure Data Factory güvenli bir şekilde depolamak veya [Azure Key Vault depolanan bir gizli dizi başvurusunda bulunmak](store-credentials-in-key-vault.md)için bu alanı **SecureString** olarak işaretleyin. | Evet, bir hizmet sorumlusu ile Azure AD kimlik doğrulaması kullandığınızda |
| Kiracı | Uygulamanızın bulunduğu etki alanı adı veya kiracı KIMLIĞI gibi kiracı bilgilerini belirtin. Fareyi, Azure portal sağ üst köşesine getirerek alın. | Evet, bir hizmet sorumlusu ile Azure AD kimlik doğrulaması kullandığınızda |
| Azurecses türü | Hizmet sorumlusu kimlik doğrulaması için, Azure AD uygulamanızın kaydedildiği Azure bulut ortamının türünü belirtin. <br/> İzin verilen değerler **Azucumhuriyeti**, **AzureChina**, **AzureUsGovernment** ve **AzureGermany**. Varsayılan olarak, Data Factory 'nin bulut ortamı kullanılır. | Hayır |
| connectVia | Bu [tümleştirme çalışma zamanı](concepts-integration-runtime.md) , veri deposuna bağlanmak için kullanılır. Veri depolubir özel ağda yer alıyorsa Azure tümleştirme çalışma zamanını veya şirket içinde barındırılan tümleştirme çalışma zamanını kullanabilirsiniz. Belirtilmemişse, varsayılan Azure tümleştirme çalışma zamanı kullanılır. | Hayır |

Farklı kimlik doğrulama türleri için sırasıyla Önkoşullar ve JSON örnekleri hakkında aşağıdaki bölümlere bakın:

- [SQL kimlik doğrulaması](#sql-authentication)
- [Azure AD uygulama belirteci kimlik doğrulaması: hizmet sorumlusu](#service-principal-authentication)
- [Azure AD uygulama belirteci kimlik doğrulaması: Azure kaynakları için Yönetilen kimlikler](#managed-identity)

>[!TIP]
>"UserErrorFailedToConnectToSqlServer" hata koduyla bir hatayla karşılaşırsanız ve "veritabanı için oturum sınırı XXX ve ulaşıldığında", `Pooling=false` Bağlantı dizenizi ekleyin ve yeniden deneyin. `Pooling=false` Ayrıca, **Shir (Şirket Içinde barındırılan Integration Runtime)** türünde bağlı hizmet kurulumu için de önerilir. Havuz ve diğer bağlantı parametreleri, bağlantılı hizmet oluşturma formunun **ek bağlantı özellikleri** bölümünde yeni parametre adları ve değerler olarak eklenebilir.

### <a name="sql-authentication"></a>SQL kimlik doğrulaması

**Örnek: SQL kimlik doğrulaması kullanma**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Örnek: Azure Key Vault parola**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
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

1. Azure portal [bir Azure Active Directory uygulaması oluşturun](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) . Uygulama adını ve bağlı hizmeti tanımlayan aşağıdaki değerleri unutmayın:

    - Uygulama Kimliği
    - Uygulama anahtarı
    - Kiracı Kimliği

2. Daha önce yapmadıysanız Azure portal sunucunuza yönelik [bir Azure Active Directory Yöneticisi sağlayın](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database) . Azure AD yöneticisi bir Azure AD kullanıcısı veya Azure AD grubu olmalıdır, ancak hizmet sorumlusu olamaz. Bu adım, bir sonraki adımda hizmet sorumlusu için kapsanan bir veritabanı kullanıcısı oluşturmak üzere bir Azure AD kimliği kullanabilmeniz için yapılır.

3. Hizmet sorumlusu için [Kapsanan Veritabanı kullanıcıları oluşturun](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) . En az BIR kullanıcı iznini değiştiren bir Azure AD kimliğiyle, SQL Server Management Studio gibi araçları kullanarak verileri kopyalamak istediğiniz veritabanına bağlanın. Aşağıdaki T-SQL ' i çalıştırın:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. SQL kullanıcıları veya diğerleri için normalde yaptığınız sürece hizmet sorumlusuna gerekli izinleri verin. Aşağıdaki kodu çalıştırın. Daha fazla seçenek için [Bu belgeye](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)bakın.

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. Azure Data Factory Azure SQL veritabanı bağlı hizmetini yapılandırın.

#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Hizmet sorumlusu kimlik doğrulaması kullanan bağlı hizmet örneği

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30",
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

Veri Fabrikası, belirli veri fabrikasını temsil eden [Azure kaynakları için yönetilen bir kimlikle](data-factory-service-identity.md) ilişkilendirilebilir. Bu yönetilen kimliği, Azure SQL veritabanı kimlik doğrulaması için kullanabilirsiniz. Belirlenen fabrika, bu kimliği kullanarak, veritabanınıza veya veritabanına erişebilir ve veri kopyalayabilir.

Yönetilen kimlik kimlik doğrulamasını kullanmak için aşağıdaki adımları izleyin.

1. Daha önce yapmadıysanız Azure portal sunucunuza yönelik [bir Azure Active Directory Yöneticisi sağlayın](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database) . Azure AD yöneticisi bir Azure AD kullanıcısı veya bir Azure AD grubu olabilir. Gruba yönetilen kimliğe sahip bir yönetici rolü verirseniz, 3 ve 4. adımları atlayın. Yöneticinin veritabanına tam erişimi vardır.

2. Azure Data Factory yönetilen kimlik için [Kapsanan Veritabanı kullanıcıları oluşturun](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) . En az BIR kullanıcı iznini değiştiren bir Azure AD kimliğiyle, SQL Server Management Studio gibi araçları kullanarak verileri kopyalamak istediğiniz veritabanına bağlanın. Aşağıdaki T-SQL ' i çalıştırın:
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. SQL kullanıcıları ve diğerleri için normalde yaptığınız gibi Data Factory yönetilen kimliğe gerekli izinleri verin. Aşağıdaki kodu çalıştırın. Daha fazla seçenek için [Bu belgeye](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)bakın.

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your Data Factory name];
    ```

4. Azure Data Factory Azure SQL veritabanı bağlı hizmetini yapılandırın.

**Örnek**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Veri kümesi özellikleri

Veri kümelerini tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. [veri kümeleri](./concepts-datasets-linked-services.md).

Azure SQL veritabanı veri kümesi için aşağıdaki özellikler desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | DataSet 'in **Type** özelliği **Azuressqltable** olarak ayarlanmalıdır. | Yes |
| schema | Şemanın adı. |Kaynak için Hayır, havuz için Evet  |
| tablo | Tablo/görünüm adı. |Kaynak için Hayır, havuz için Evet  |
| tableName | Şema ile tablonun/görünümün adı. Bu özellik geriye dönük uyumluluk için desteklenir. Yeni iş yükü için `schema` ve kullanın `table` . | Kaynak için Hayır, havuz için Evet |

### <a name="dataset-properties-example"></a>DataSet özellikleri örneği

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
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

Etkinlikleri tanımlamaya yönelik bölümlerin ve özelliklerin tam listesi için bkz. işlem [hatları](concepts-pipelines-activities.md). Bu bölüm, Azure SQL veritabanı kaynağı ve havuzu tarafından desteklenen özelliklerin bir listesini sağlar.

### <a name="azure-sql-database-as-the-source"></a>Kaynak olarak Azure SQL veritabanı

>[!TIP]
>Veri bölümlemesini kullanarak Azure SQL veritabanından verileri verimli bir şekilde yüklemek için [SQL veritabanından paralel kopyadan](#parallel-copy-from-sql-database)daha fazla bilgi edinin.

Azure SQL veritabanından veri kopyalamak için aşağıdaki özellikler, etkinlik **kaynağını** kopyalama bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği kaynağının **Type** özelliği **Azuressqlsource** olarak ayarlanmalıdır. "SqlSource" türü, geriye dönük uyumluluk için hala desteklenmektedir. | Yes |
| sqlReaderQuery | Bu özellik, verileri okumak için özel SQL sorgusu kullanır. `select * from MyTable` bunun bir örneğidir. | Hayır |
| sqlReaderStoredProcedureName | Kaynak tablodaki verileri okuyan saklı yordamın adı. Son SQL ifadesinin saklı yordamda bir SELECT ifadesinin olması gerekir. | Hayır |
| storedProcedureParameters | Saklı yordamın parametreleri.<br/>İzin verilen değerler ad veya değer çiftleridir. Parametrelerin adları ve büyük harfleri, saklı yordam parametrelerinin adlarıyla ve büyük küçük harfleriyle eşleşmelidir. | Hayır |
| 'Sinden | SQL kaynağı için işlem kilitleme davranışını belirtir. İzin verilen değerler: **ReadCommitted**, **READUNCOMMITTED**, **RepeatableRead**, **Serializable**, **Snapshot**. Belirtilmemişse, veritabanının varsayılan yalıtım düzeyi kullanılır. Daha fazla ayrıntı için [Bu belgeye](/dotnet/api/system.data.isolationlevel) başvurun. | Hayır |
| partitionOptions | Azure SQL veritabanından veri yüklemek için kullanılan veri bölümleme seçeneklerini belirtir. <br>İzin verilen değerler: **none** (default), **Physicalpartitionsoftable** ve **DynamicRange**.<br>Bir bölüm seçeneği etkin olduğunda (yani, `None` ), bir Azure SQL veritabanından eşzamanlı olarak veri yükleme için paralellik derecesi [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) kopyalama etkinliğindeki ayarla birlikte denetlenir. | Hayır |
| partitionSettings | Veri bölümleme için ayarların grubunu belirtin. <br>Bölüm seçeneği olmadığında uygulayın `None` . | Hayır |
| ***Altında `partitionSettings` :*** | | |
| partitionColumnName |  `int` `smallint` `bigint` `date` `smalldatetime` `datetime` `datetime2` `datetimeoffset` Paralel kopya için Aralık bölümleme tarafından kullanılacak tamsayı veya tarih/DateTime türünde (,,,,,,, veya) kaynak sütunun adını belirtin. Belirtilmemişse, tablodaki dizin veya birincil anahtar, bölüm sütunu olarak yeniden algılanır ve kullanılır.<br>Bölüm seçeneği olduğunda uygulayın `DynamicRange` . Kaynak verileri almak için bir sorgu kullanırsanız,  `?AdfDynamicRangePartitionCondition ` WHERE yan tümcesinde kanca. Örnek için, [SQL veritabanı 'Ndan paralel kopyalama](#parallel-copy-from-sql-database) bölümüne bakın. | Hayır |
| Partitionüstsınırı | Bölüm aralığı bölme için bölüm sütununun en büyük değeri. Bu değer, tablodaki satırları filtrelemeye yönelik değil, bölümün ilerlemesine karar vermek için kullanılır. Tablodaki veya sorgu sonucundaki tüm satırlar bölümlenecek ve kopyalanabilir. Belirtilmemişse, kopyalama etkinliği değeri otomatik olarak algılar.  <br>Bölüm seçeneği olduğunda uygulayın `DynamicRange` . Örnek için, [SQL veritabanı 'Ndan paralel kopyalama](#parallel-copy-from-sql-database) bölümüne bakın. | Hayır |
| Partitionalme sınırı | Bölüm aralığı bölme için bölüm sütununun en küçük değeri. Bu değer, tablodaki satırları filtrelemeye yönelik değil, bölümün ilerlemesine karar vermek için kullanılır. Tablodaki veya sorgu sonucundaki tüm satırlar bölümlenecek ve kopyalanabilir. Belirtilmemişse, kopyalama etkinliği değeri otomatik olarak algılar.<br>Bölüm seçeneği olduğunda uygulayın `DynamicRange` . Örnek için, [SQL veritabanı 'Ndan paralel kopyalama](#parallel-copy-from-sql-database) bölümüne bakın. | Hayır |

**Aşağıdaki noktalara dikkat edin:**

- **Azuresopsource** Için **sqlreaderquery** belirtilmişse, kopyalama etkinliği verileri almak IÇIN bu sorguyu Azure SQL veritabanı kaynağına karşı çalıştırır. Saklı yordam parametreleri alırsa, **sqlReaderStoredProcedureName** ve **storedProcedureParameters** belirterek bir saklı yordam de belirtebilirsiniz.
- Verileri almak için kaynakta saklı yordam kullanırken, saklı yordamınız farklı bir parametre değeri geçirildiğinde farklı bir şema döndürüyor olarak tasarlanmışsa, hata ile karşılaşabilirsiniz veya kullanıcı arabiriminden şemayı içeri aktarırken ya da otomatik tablo oluşturma ile verileri SQL veritabanına kopyalarken beklenmedik sonucu görebilirsiniz.

#### <a name="sql-query-example"></a>SQL sorgu örneği

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "AzureSqlSource",
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
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
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
                "type": "AzureSqlSource",
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

### <a name="stored-procedure-definition"></a>Saklı yordam tanımı

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

### <a name="azure-sql-database-as-the-sink"></a>Havuz olarak Azure SQL veritabanı

> [!TIP]
> [Azure SQL veritabanı 'na veri yüklemek Için en iyi](#best-practice-for-loading-data-into-azure-sql-database)uygulamalardan desteklenen yazma davranışları, konfigürasyonlar ve en iyi uygulamalar hakkında daha fazla bilgi edinin.

Azure SQL veritabanı 'na veri kopyalamak için aşağıdaki özellikler, etkinlik **havuzunu** Kopyala bölümünde desteklenir:

| Özellik | Açıklama | Gerekli |
|:--- |:--- |:--- |
| tür | Kopyalama etkinliği havuzunun **Type** özelliği **Azuressqlsink** olarak ayarlanmalıdır. "SqlSink" türü, geriye dönük uyumluluk için hala destekleniyor. | Yes |
| Ön Copyscrıpt | Azure SQL veritabanı 'na veri yazmadan önce çalıştırılacak kopyalama etkinliği için bir SQL sorgusu belirtin. Her kopya çalıştırması için yalnızca bir kez çağrılır. Önceden yüklenmiş verileri temizlemek için bu özelliği kullanın. | Hayır |
| tableOption | Kaynak şemasına göre yoksa [Havuz tablosunun otomatik olarak oluşturulup](copy-activity-overview.md#auto-create-sink-tables) oluşturulmayacağını belirtir. <br>Havuz, saklı yordamı belirttiğinde otomatik tablo oluşturma desteklenmez. <br>İzin verilen değerler: `none` (varsayılan), `autoCreate` . | Hayır |
| sqlWriterStoredProcedureName | Hedef tabloya kaynak verilerinin nasıl uygulanacağını tanımlayan saklı yordamın adı. <br/>Bu saklı yordam *toplu iş başına çağırılır*. Yalnızca bir kez çalıştırılan ve kaynak verilerle hiçbir şey olmayan işlemler için, örneğin, DELETE veya TRUNCATE, `preCopyScript` özelliğini kullanın.<br>[BIR SQL havuzundan saklı yordam çağırma](#invoke-a-stored-procedure-from-a-sql-sink)örneğine bakın. | Hayır |
| storedProcedureTableTypeParameterName |Saklı yordamda belirtilen tablo türünün parametre adı.  |Hayır |
| sqlWriterTableType |Saklı yordamda kullanılacak tablo türü adı. Kopyalama etkinliği, verileri bu tablo türüyle geçici bir tabloda kullanılabilir hale getirir. Saklı yordam kodu daha sonra mevcut verilerle Kopyalanmakta olan verileri birleştirebilir. |Hayır |
| storedProcedureParameters |Saklı yordamın parametreleri.<br/>İzin verilen değerler ad ve değer çiftleridir. Parametrelerin adları ve büyük harfleri, saklı yordam parametrelerinin adlarıyla ve büyük küçük harfleriyle aynı olmalıdır. | Hayır |
| writeBatchSize | *Toplu iş BAŞıNA* SQL tablosuna eklenecek satır sayısı.<br/> İzin verilen değer **Integer** (satır sayısı). Varsayılan olarak, Azure Data Factory satır boyutuna göre uygun toplu iş boyutunu dinamik olarak belirler. | Hayır |
| writeBatchTimeout | Toplu iş ekleme işleminin, zaman aşımına uğramadan önce tamamlaması için bekleme süresi.<br/> İzin verilen değer **TimeSpan** değeridir. Örnek olarak "00:30:00" (30 dakika) bulunur. | Hayır |
| disableMetricsCollection | Data Factory, performansı iyileştirmek için Azure SQL veritabanı DTU 'ları ve ek ana DB erişimi sunan öneriler toplar. Bu davranışla ilgileniyorlarsa, `true` devre dışı bırakmak için belirtin. | Hayır (varsayılan değer `false` ) |
| maxConcurrentConnections |Etkinlik çalışması sırasında veri deposuna kurulan eşzamanlı bağlantıların üst sınırı. Yalnızca eş zamanlı bağlantıları sınırlandırmak istediğinizde bir değer belirtin.| Hayır |

**Örnek 1: veri ekleme**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Örnek 2: kopyalama sırasında saklı yordam çağırma**

[BIR SQL havuzundan saklı yordam çağırma](#invoke-a-stored-procedure-from-a-sql-sink)hakkında daha fazla bilgi edinin.

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
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

## <a name="parallel-copy-from-sql-database"></a>SQL veritabanından paralel kopya

Copy etkinliğinde Azure SQL Veritabanı Bağlayıcısı, verileri paralel olarak kopyalamak için yerleşik veri bölümlemesini sağlar. Kopyalama etkinliğinin **kaynak** sekmesinde veri bölümleme seçeneklerini bulabilirsiniz.

![Bölüm seçeneklerinin ekran görüntüsü](./media/connector-sql-server/connector-sql-partition-options.png)

Bölümlenmiş kopyayı etkinleştirdiğinizde kopyalama etkinliği, verileri bölümlere göre yüklemek için Azure SQL veritabanı kaynağınıza karşı paralel sorgular çalıştırır. Paralel derece [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) kopyalama etkinliğindeki ayar tarafından denetlenir. Örneğin, `parallelCopies` dört olarak ayarlarsanız, Data Factory aynı anda, belirtilen bölüm seçeneğiniz ve ayarlarınıza göre dört sorgu üretir ve çalışır ve her sorgu Azure SQL veritabanından verilerin bir kısmını alır.

Azure SQL veritabanından büyük miktarda veri yüklerken özellikle veri bölümleme ile paralel kopyayı etkinleştirmeniz önerilir. Farklı senaryolar için önerilen yapılandırma aşağıda verilmiştir. Dosya tabanlı veri deposuna veri kopyalarken, bir klasöre birden çok dosya (yalnızca klasör adı belirtin) yazılması önerilir, bu durumda performans tek bir dosyaya yazılmasından daha iyidir.

| Senaryo                                                     | Önerilen ayarlar                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Fiziksel bölümlerle büyük tablodan tam yük.        | **Bölüm seçeneği**: tablonun fiziksel bölümleri. <br><br/>Yürütme sırasında Data Factory fiziksel bölümleri otomatik olarak algılar ve verileri bölümlere göre kopyalar. <br><br/>Tablonuzun fiziksel bölümü olup olmadığını denetlemek için [Bu sorguya](#sample-query-to-check-physical-partition)başvurabilirsiniz. |
| Fiziksel bölümler olmadan, veri bölümlendirme için bir tamsayı veya tarih saat sütunuyla büyük tablodan tam yük. | **Bölüm seçenekleri**: Dinamik Aralık bölümü.<br>**Bölüm sütunu** (isteğe bağlı): verileri bölümlemek için kullanılan sütunu belirtin. Belirtilmezse, dizin veya birincil anahtar sütunu kullanılır.<br/>**Bölüm üst sınırı** ve **bölüm alt sınırı** (isteğe bağlı): Bölüm Ilerlemesiyle mı belirlemek istediğinizi belirtin. Bu, tablodaki satırları filtrelemeye yönelik değildir, tablodaki tüm satırlar bölümlenecek ve kopyalanır. Belirtilmemişse, kopyalama etkinliği değerleri otomatik olarak algılar.<br><br>Örneğin, "ID" adlı bölüm sütununuzu 1 ile 100 arasında değerler varsa ve alt sınırı 20 ve üst sınır olarak 80 olarak ayarlarsanız, paralel kopyalama Data Factory 4 ' e kadar, verileri 4 bölüm kimliği aralığında <= 20, [21, 50], [51, 80] ve >= 81 sırasıyla alır. |
| Fiziksel bölümler olmadan, veri bölümlendirme için bir tamsayı veya tarih/tarih/saat sütunuyla büyük miktarda veri yükleyin. | **Bölüm seçenekleri**: Dinamik Aralık bölümü.<br>**Sorgu**: `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>` .<br>**Bölüm sütunu**: verileri bölümlemek için kullanılan sütunu belirtin.<br>**Bölüm üst sınırı** ve **bölüm alt sınırı** (isteğe bağlı): Bölüm Ilerlemesiyle mı belirlemek istediğinizi belirtin. Bu, tablodaki satırları filtrelemeye yönelik değildir, sorgu sonucundaki tüm satırlar bölümlenecek ve kopyalanır. Belirtilmemişse, kopyalama etkinliği değeri otomatik olarak algılar.<br><br>Yürütme sırasında, Data Factory `?AdfRangePartitionColumnName` her bölüm için gerçek sütun adı ve değer aralıklarıyla değiştirilir ve Azure SQL veritabanı 'na gönderir. <br>Örneğin, "ID" adlı bölüm sütununuzu 1 ile 100 arasında değerler varsa ve alt sınırı 20 ve üst sınır olarak 80 olarak ayarlarsanız, paralel kopyalama Data Factory 4 ' e kadar, verileri 4 bölüm kimliği aralığında <= 20, [21, 50], [51, 80] ve >= 81 sırasıyla alır. <br><br>Farklı senaryolar için daha fazla örnek sorgu aşağıda verilmiştir:<br> 1. tüm tabloyu sorgulayın: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. sütun seçimi ve ek WHERE yan tümcesi filtreleri içeren bir tablodan sorgu: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. sorgular ile sorgu: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. alt sorguda bölüm ile sorgulama: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Bölüm seçeneğiyle verileri yüklemek için en iyi uygulamalar:

1. Veri eğriliğini önlemek için bölüm sütunu olarak (birincil anahtar veya benzersiz anahtar gibi) farklı bir sütun seçin. 
2. Tabloda yerleşik bölüm varsa, daha iyi performans sağlamak için "tablonun fiziksel bölümleri" Bölüm seçeneğini kullanın.    
3. Verileri kopyalamak için Azure Integration Runtime kullanırsanız daha fazla bilgi işlem kaynağı kullanmak için daha büyük "[veri tümleştirme birimleri (DIU)](copy-activity-performance-features.md#data-integration-units)" (>4) ayarlayabilirsiniz. İlgili senaryolara göz atın.
4. "[Kopya paralelliği derecesi](copy-activity-performance-features.md#parallel-copy)", Bölüm numaralarını denetler, bu sayının performansı karşılamamasından çok büyük bir şekilde ayarlanması, bu sayıyı (ya da şirket IÇINDE barındırılan IR düğümlerinin sayısı) * (2-4) olarak ayarlamayı öneririz.

**Örnek: fiziksel bölümlerle büyük tablodan tam yük**

```json
"source": {
    "type": "AzureSqlSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Örnek: Dinamik Aralık bölümü ile sorgulama**

```json
"source": {
    "type": "AzureSqlSource",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Azure SQL veritabanı 'na veri yüklemek için en iyi uygulama

Verileri Azure SQL veritabanına kopyaladığınızda, farklı yazma davranışı gerekebilir:

- [Append](#append-data): Kaynak verilerinizde yalnızca yeni kayıtlar vardır.
- [Upsert](#upsert-data): Kaynak verilerinizde hem ekler hem de güncelleştirmeler vardır.
- [Üzerine yaz](#overwrite-the-entire-table): her seferinde bir boyut tablosunun tamamını yeniden yüklemek istiyorum.
- [Özel mantık Ile yaz](#write-data-with-custom-logic): hedef tabloya son ekleme yapmadan önce fazladan işleme ihtiyacım var.

Azure Data Factory ve en iyi uygulamalarında yapılandırma hakkında ilgili bölümlere bakın.

### <a name="append-data"></a>Veri Ekle

Verilerin eklenmesi, bu Azure SQL veritabanı havuz bağlayıcısının varsayılan davranışıdır. Azure Data Factory tablonuza verimli bir şekilde yazmak için toplu bir ekleme yapar. Kopyalama etkinliğinde kaynağı ve havuzu uygun şekilde yapılandırabilirsiniz.

### <a name="upsert-data"></a>Verileri upsert etme

**Seçenek 1:** Kopyalamak için büyük miktarda veriniz varsa, kopyalama etkinliğini kullanarak tüm kayıtları bir hazırlama tablosuna toplu olarak yükleyebilir, sonra bir bir çekde [birleştirme](/sql/t-sql/statements/merge-transact-sql) veya ekleme/güncelleştirme ifadesini uygulamak için bir saklı yordam etkinliği çalıştırabilirsiniz. 

Kopyalama etkinliği şu anda verileri veritabanı geçici tablosuna yüklemeyi yerel olarak desteklemiyor. Birden çok etkinliğin birleşimiyle ayarlanmasının gelişmiş bir yolu vardır ve [Azure SQL veritabanı toplu gelişmiş senaryolarını iyileştirme](https://github.com/scoriani/azuresqlbulkupsert)bölümüne bakın. Aşağıda, kalıcı bir tablonun hazırlama olarak kullanılması örneği gösterilmektedir.

Örnek olarak, Azure Data Factory, **saklı yordam etkinliği** ile **kopyalama etkinliği** zincirli bir işlem hattı oluşturabilirsiniz. Eski, verileri veri kümesindeki tablo adı olarak kaynak deponuzdan Azure SQL veritabanı hazırlama tablosuna (örneğin, **UpsertStagingTable**) kopyalar. İkinci olarak, hazırlama tablosundaki kaynak verileri hedef tabloya birleştirmek ve hazırlama tablosunu temizlemek için bir saklı yordam çağırır.

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

**Seçenek 3:** Yerleşik INSERT/upsert/Update yöntemleri sunan [eşleme veri akışını](#sink-transformation) kullanabilirsiniz.

### <a name="overwrite-the-entire-table"></a>Tüm tablonun üzerine yaz

Kopyalama etkinliği havuzunda **Precopyscript** özelliğini yapılandırabilirsiniz. Bu durumda, çalıştıran her kopyalama etkinliği için önce betiği çalıştırır Azure Data Factory. Ardından, verileri eklemek için kopyayı çalıştırır. Örneğin, en son verilerle tüm tablonun üzerine yazmak için, kaynaktan yeni verileri toplu yüklemeden önce tüm kayıtları silmek üzere bir komut dosyası belirtin.

### <a name="write-data-with-custom-logic"></a>Özel mantık ile veri yazma

Özel mantık ile veri yazma adımları, [upsert veri](#upsert-data) bölümünde açıklananlara benzerdir. Hedef tabloya son kaynak verileri eklemeden önce fazladan işlem uygulamanız gerektiğinde, bir hazırlama tablosuna yükleyebilir, sonra saklı yordam etkinliğini çağırabilir veya kopyalama etkinliği havuzunda verileri uygulamak için bir saklı yordam çağırabilir veya eşleme veri akışını kullanabilirsiniz.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Bir SQL havuzundan saklı yordam çağırma

Verileri Azure SQL veritabanına kopyaladığınızda, kaynak tablodaki her yığın üzerinde ek parametrelerle Kullanıcı tarafından belirtilen bir saklı yordamı yapılandırabilir ve çağırabilirsiniz. Saklı yordam özelliği [tablo değerli parametrelerin](/dotnet/framework/data/adonet/sql/table-valued-parameters)avantajlarından yararlanır.

Yerleşik kopyalama mekanizmaları amaca uygun olmadığında, saklı bir yordam kullanabilirsiniz. Kaynak verilerin son olarak hedef tabloya eklenmesinin önüne daha fazla işlem uygulamak istediğinizde örnek bir örnektir. Bazı ek işleme örnekleri, sütunları birleştirmek, ek değerleri aramak ve birden fazla tabloya eklemek istebilmenizdir.

Aşağıdaki örnek, Azure SQL veritabanı 'nda bir tabloyu kullanarak bir saklı yordamın nasıl kullanıldığını gösterir. Giriş verilerinin ve havuz **Pazarlama** tablosunun her birinin üç sütunu olduğunu varsayalım: **ProfileId**, **State** ve **category**. **ProfileId** sütununu temel alarak ve yalnızca "Producta" adlı belirli bir kategori için uygulayın.

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

3. Azure Data Factory, kopyalama etkinliğinde **SQL havuzu** bölümünü aşağıdaki gibi tanımlayın:

    ```json
    "sink": {
        "type": "AzureSqlSink",
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

Eşleme veri akışındaki verileri dönüştürürken Azure SQL veritabanı 'ndan tabloları okuyabilir ve yazabilirsiniz. Daha fazla bilgi için bkz. veri akışlarını eşleme içindeki [kaynak dönüştürme](data-flow-source.md) ve [Havuz dönüşümü](data-flow-sink.md) .

### <a name="source-transformation"></a>Kaynak dönüştürme

Azure SQL veritabanı 'na özgü ayarlar, kaynak dönüşümünün **kaynak seçenekleri** sekmesinde bulunur.

**Giriş:** Kaynağınızı bir tabloya (eşdeğerini) işaret edip etmeyeceğinizi seçin ```Select * from <table-name>``` ya da özel BIR SQL sorgusu girin.

**Sorgu**: giriş alanında sorgu ' yı seçerseniz, kaynağınız IÇIN bir SQL sorgusu girin. Bu ayar, veri kümesinde seçtiğiniz tüm tabloları geçersiz kılar. **Order by** yan tümceleri burada desteklenmez, ancak BIR tam select from ifadesini ayarlayabilirsiniz. Kullanıcı tanımlı tablo işlevleri de kullanabilirsiniz. **select * from udfGetData ()** , bir tablo döndüren SQL 'de bir UDF 'dir. Bu sorgu, veri akışınızda kullanabileceğiniz bir kaynak tablosu oluşturur. Sorguların kullanılması, test veya aramalar için satırları azaltmanın harika bir yoludur.

**Saklı yordam**: kaynak veritabanından yürütülen bir saklı yordamdan yansıtma ve kaynak verileri oluşturmak istiyorsanız bu seçeneği belirleyin. Şema, yordam adı ve parametreleri yazabilir veya bir ADF 'yi tıklayarak, ADF 'yi şemaları ve yordam adlarını bulmayı isteyebilirsiniz. Ardından, formunu kullanarak tüm yordam parametrelerini içeri aktarmak için Içeri Aktar ' a tıklayabilirsiniz ``@paraName`` .

![Saklı yordam](media/data-flow/stored-procedure-2.png "Saklı Yordam")

- SQL örneği: ```Select * from MyTable where customerId > 1000 and customerId < 2000```
- Parametreli SQL örneği: ``"select * from {$tablename} where orderyear > {$year}"``

**Toplu iş boyutu**: büyük verileri okuma işleminde öbek için bir toplu iş boyutu girin.

**Yalıtım düzeyi**: eşleme VERI akışındaki SQL kaynakları için varsayılan değer read UNCOMMITTED ' dır. Yalıtım düzeyini buradaki değerlerden birine değiştirebilirsiniz:

- Okuma Işlendi
- Kaydedilmeyen oku
- Yinelenebilir okuma
- Seri hale getirilebilir
- Hiçbiri (yalıtım düzeyini yoksay)

![Yalıtım düzeyi](media/data-flow/isolationlevel.png "Yalıtım düzeyi")

### <a name="sink-transformation"></a>Havuz dönüştürme

Azure SQL veritabanı 'na özgü ayarlar, havuz dönüşümünün **Ayarlar** sekmesinde bulunur.

**Güncelleştirme yöntemi:** Veritabanı Hedefinizdeki hangi işlemlere izin verileceğini belirler. Varsayılan değer yalnızca eklemeleri izin verir. Satırları güncelleştirmek, kaldırmak veya silmek için, bu eylemler için satırları etiketlemek üzere alter-Row dönüşümü gereklidir. Güncelleştirmeler, yukarı ve silme için bir anahtar sütunu veya sütunları ayarlanacak satırı belirleyecek şekilde ayarlanmalıdır.

![Anahtar sütunlar](media/data-flow/keycolumn.png "Anahtar sütunlar")

Burada anahtar olarak seçtiğiniz sütun adı, ADF tarafından sonraki güncelleştirme, upsert, DELETE 'in bir parçası olarak kullanılacaktır. Bu nedenle, havuz eşlemesinde var olan bir sütun seçmeniz gerekir. Değeri bu anahtar sütununa yazmak istiyorsanız "anahtar sütunlarını yazmayı atla" seçeneğine tıklayın.

Hedef Azure SQL veritabanı tablonuzu güncelleştirmek için burada kullanılan anahtar sütununu parametreleştirebilirsiniz. Bileşik anahtar için birden çok sütuniniz varsa, "özel Ifade" düğmesine tıklayın ve bir bileşik anahtar için sütun adlarıyla bir dize dizisi içerebilen ADF veri akışı ifade dilini kullanarak dinamik içerik ekleyebileceksiniz.

**Tablo eylemi:** Yazmadan önce hedef tablodaki tüm satırların yeniden oluşturulup kaldırılacağını belirler.

- Hiçbiri: tabloya hiçbir eylem yapılmaz.
- Yeniden oluştur: tablo bırakılır ve yeniden oluşturulur. Dinamik olarak yeni bir tablo oluşturuluyoruz gereklidir.
- Kes: hedef tablodaki tüm satırlar kaldırılacak.

**Toplu iş boyutu**: her bir sepete kaç satır yazıldığını denetler. Daha büyük toplu işlem boyutları sıkıştırma ve bellek iyileştirmeyi iyileştirir, ancak verileri önbelleğe alırken bellek dışında özel durumlar riskini ortadan kaldıracak.

**Tempdb kullan:** Varsayılan olarak Data Factory, verileri yükleme işleminin bir parçası olarak depolamak için genel geçici bir tablo kullanacaktır. Alternatif olarak, "TempDB kullan" seçeneğinin işaretini kaldırın ve bunun yerine, geçici saklama tablosunu bu havuz için kullanılmakta olan veritabanında bulunan bir kullanıcı veritabanında depolamasını Data Factory isteyebilirsiniz.

![Geçici VERITABANıNı kullan](media/data-flow/tempdb.png "Geçici VERITABANıNı kullan")

**SQL betiklerini ön ve sonrası**: (ön işleme) ve sonra (işlem sonrası) verileri havuz veritabanınıza yazıldıktan sonra yürütülecek çok satırlı SQL betikleri girin

![SQL işleme betikleri ön ve sonrası](media/data-flow/prepost1.png "SQL işleme betikleri")

### <a name="error-row-handling"></a>Hata satırı işleme

Azure SQL DB 'ye yazarken, hedef tarafından ayarlanan kısıtlamalar nedeniyle belirli veri satırları başarısız olabilir. Bazı sık karşılaşılan hatalar şunlardır:

*    Tablodaki dize veya ikili veriler kesilebilir
*    NULL değeri sütuna eklenemiyor
*    INSERT deyimleri CHECK kısıtlaması ile çakışıyor

Varsayılan olarak, bir veri akışı çalıştırması aldığı ilk hatada başarısız olur. Ayrı satırlarda hata olsa bile, veri akışının tamamlanmasını sağlayan **hata durumunda devam** etmeyi tercih edebilirsiniz. Azure Data Factory, bu hata satırlarını işleyebilmeniz için farklı seçenekler sağlar.

**Işlem işleme:** Verilerinizin tek bir işlemde mi yoksa toplu olarak mı yazıldığını seçin. Tek işlem daha kötü performans sağlar, ancak işlem tamamlanana kadar başka hiçbir veri yazılmadı.  

**Reddedilen çıkış verileri:** Etkinleştirilirse, hata satırlarını Azure Blob depolama alanındaki bir CSV dosyasına veya seçtiğiniz Azure Data Lake Storage 2. bir hesaba aktarabilirsiniz. Bu, hata satırlarını üç ek sütunla Yazar: ekleme veya GÜNCELLEŞTIRME, veri akışı hata kodu ve satırdaki hata iletisi gibi SQL işlemi.

**Hatada başarı bildir:** Etkinleştirilirse, hata satırları bulunursa bile veri akışı başarılı olarak işaretlenir. 

![Hata satırı işleme](media/data-flow/sql-error-row-handling.png "Hata satırı işleme")


## <a name="data-type-mapping-for-azure-sql-database"></a>Azure SQL veritabanı için veri türü eşlemesi

Veriler veya Azure SQL veritabanı 'na kopyalandığında, Azure SQL veritabanı veri türleri arasında, geçici veri türlerini Azure Data Factory için aşağıdaki eşlemeler kullanılır. Kopyalama etkinliğinin kaynak şemayı ve veri türünü havuza nasıl eşlediğini öğrenmek için bkz. [şema ve veri türü eşlemeleri](copy-activity-schema-and-type-mapping.md).

| Azure SQL veritabanı veri türü | Azure Data Factory geçici veri türü |
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
| tinyint |Bayt |
| uniqueidentifier |Guid |
| ikili |Byte [] |
| varchar |Dize, Char [] |
| xml |Dize |

>[!NOTE]
> Ondalık geçici türle eşlenen veri türleri için şu anda kopyalama etkinliği, 28 ' ye kadar duyarlık destekler. Daha yüksek bir duyarlığa sahip verileriniz varsa, SQL sorgusunda bir dizeye dönüştürmeyi düşünün.

## <a name="lookup-activity-properties"></a>Arama etkinliği özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [arama etkinliğini](control-flow-lookup-activity.md)denetleyin.

## <a name="getmetadata-activity-properties"></a>GetMetadata etkinlik özellikleri

Özelliklerle ilgili ayrıntıları öğrenmek için [GetMetadata etkinliğini](control-flow-get-metadata-activity.md) denetleyin

## <a name="using-always-encrypted"></a>Always Encrypted kullanma

[Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)Ile Azure SQL veritabanı 'ndan veri kopyaladığınızda, [Genel ODBC Bağlayıcısı](connector-odbc.md) ' nı ve SQL Server ODBC sürücüsünü şirket içinde barındırılan Integration Runtime aracılığıyla kullanın. Bu Azure SQL Veritabanı Bağlayıcısı şimdi Always Encrypted desteklemiyor. 

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

Azure Data Factory içindeki kopyalama etkinliği tarafından kaynak ve havuz olarak desteklenen veri depolarının listesi için bkz. [desteklenen veri depoları ve biçimleri](copy-activity-overview.md#supported-data-stores-and-formats).
