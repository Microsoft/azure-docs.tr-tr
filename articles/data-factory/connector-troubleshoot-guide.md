---
title: Azure Data Factory bağlayıcılarıyla ilgili sorunları giderme
description: Azure Data Factory 'deki bağlayıcı sorunlarını giderme hakkında bilgi edinin.
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/13/2021
ms.author: jingwang
ms.custom: has-adal-ref
ms.openlocfilehash: 21b5522f07519e9a0c3353cb2463e0ec49063f34
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713435"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Azure Data Factory bağlayıcılarıyla ilgili sorunları giderme

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu makalede Azure Data Factory bağlayıcılarıyla ilgili sorunları gidermeye yönelik genel yollar ele açıklanır.

## <a name="azure-blob-storage"></a>Azure Blob Depolama

### <a name="error-code-azurebloboperationfailed"></a>Hata kodu: AzureBlobOperationFailed

- **İleti**: "blob Işlemi başarısız oldu. ContainerName:% containerName;, yol:% path;. "

- **Neden**: BLOB depolama işlemiyle ilgili bir sorun.

- **Öneri**: hata ayrıntılarını denetlemek için bkz. [BLOB Storage hata kodları](/rest/api/storageservices/blob-service-error-codes). Daha fazla yardım için BLOB depolama ekibine başvurun.


### <a name="invalid-property-during-copy-activity"></a>Kopyalama etkinliği sırasında geçersiz özellik

- **İleti**: `Copy activity \<Activity Name> has an invalid "source" property. The source type is not compatible with the dataset \<Dataset Name> and its linked service \<Linked Service Name>. Please verify your input against.`

- **Neden**: veri kümesinde tanımlanan tür, kopyalama etkinliğinde tanımlanan kaynak veya havuz türüyle tutarsız.

- **Çözüm**: türleri tutarlı hale getirmek için veri kümesini veya Işlem hattı JSON tanımını düzenleyin ve ardından dağıtımı yeniden çalıştırın.


## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Hata iletisi: Istek boyutu çok büyük

- **Belirtiler**: verileri varsayılan bir yazma toplu işi boyutuyla Azure Cosmos DB kopyaladığınızda, şu hatayı alırsınız: `Request size is too large.`

- **Neden**: Azure Cosmos DB tek bir isteğin boyutunu 2 MB olarak sınırlandırır. Formül, *istek boyutu = tek belge boyutu \* yazma toplu işlem boyutudur*. Belge boyutunuz büyükse, varsayılan davranış çok büyük bir istek boyutuna neden olur. Yazma toplu iş boyutunu ayarlayabilirsiniz.

- **Çözüm**: kopyalama etkinliği havuzunda, *yazma toplu iş boyutu* değerini azaltın (varsayılan değer 10000 ' dir).

### <a name="error-message-unique-index-constraint-violation"></a>Hata iletisi: benzersiz dizin kısıtlaması ihlali

- **Belirtiler**: verileri Azure Cosmos DB kopyaladığınızda aşağıdaki hatayı alırsınız:

    `Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception = Error: {\"Errors\":[\"Unique index constraint violation.\"]}...`

- **Neden**: olası iki neden vardır:

    - Neden 1: ekleme davranışı olarak **Ekle** kullanırsanız, bu hata, kaynak VERILERINIZDE aynı kimliğe sahip satırlar veya nesneler olduğu anlamına gelir.
    - Neden 2: yazma davranışı olarak **upsert** kullanırsanız ve kapsayıcıya başka bir benzersiz anahtar ayarlarsanız, bu hata, kaynak verilerinizde farklı kimliklere sahip satırlar veya nesneler, tanımlı benzersiz anahtar için de aynı değere sahip olduğu anlamına gelir.

- **Çözüm**: 

    - Nedeni 1 olduğunda, yazma davranışı olarak **büyük sert** ayarlayın.
    - Neden 2 için, her belgenin tanımlı benzersiz anahtar için farklı bir değere sahip olduğundan emin olun.

### <a name="error-message-request-rate-is-large"></a>Hata iletisi: Istek hızı büyük

- **Belirtiler**: verileri Azure Cosmos DB kopyaladığınızda aşağıdaki hatayı alırsınız:

    `Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}`

- **Neden**: kullanılan istek birimleri (ru) sayısı Azure Cosmos DB ' de yapılandırılan Rus değerinden büyük. Azure Cosmos DB ru 'yi nasıl hesaplayacağını öğrenmek için [Azure Cosmos DB istek birimleri](../cosmos-db/request-units.md#request-unit-considerations)' ne bakın.

- **Çözüm**: aşağıdaki iki çözümün birini deneyin:

    - Azure Cosmos DB *kapsayıcı Rus* numarasını daha büyük bir değere yükseltin. Bu çözüm, kopyalama etkinliği performansını iyileştirir, ancak Azure Cosmos DB daha fazla ücret doğuracaktır. 
    - *Writebatchsize* , 1000 gibi daha küçük bir değere düşürün ve *parallelkopyaları* değerini 1 gibi daha küçük bir değere küçültün. Bu çözüm, kopyalama çalıştırması performansını düşürür, ancak Azure Cosmos DB daha fazla ücret vermez.

### <a name="columns-missing-in-column-mapping"></a>Sütun eşlemesinde eksik sütunlar

- **Belirtiler**: sütun eşleme için Azure Cosmos DB bir şemayı içeri aktardığınızda bazı sütunlar eksiktir. 

- **Neden**: Data Factory ilk 10 Azure Cosmos DB belgelerinden şemayı anlar. Bazı belge sütunları veya özellikleri değer içermiyorsa, şema Data Factory tarafından algılanmaz ve bu nedenle gösterilmez.

- **Çözüm**: sütun değerlerinin sonuç kümesinde boş değerlerle görüntülenmesini zorlamak için aşağıdaki kodda gösterildiği gibi sorguyu ayarlayabilirsiniz. İlk 10 belgede *imkansız* olan sütunun eksik olduğunu varsayın. Alternatif olarak, eşleme için sütunu el ile ekleyebilirsiniz.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Hata iletisi: okuyucu için Guidtemsili CSharpLegacy

- **Belirtiler**: Azure Cosmos DB Mongoapı 'Den veya MongoDB 'den evrensel benzersiz tanımlayıcı (UUID) alanı ile veri kopyaladığınızda, şu hatayı alırsınız:

    `Failed to read data via MongoDB client., 
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException, 
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,`

- **Neden**: ikili JSON (BSON) içinde UUID 'yi göstermenin iki yolu vardır: UuidStardard ve UuidLegacy. Varsayılan olarak, UuidLegacy veri okumak için kullanılır. MongoDB 'deki UUID verileriniz UuidStandard ise bir hata alırsınız.

- **Çözüm**: MongoDB bağlantı dizesinde *uuidRepresentation = standart* seçeneğini ekleyin. Daha fazla bilgi için bkz. [MongoDB bağlantı dizesi](connector-mongodb.md#linked-service-properties).
            
## <a name="azure-cosmos-db-sql-api"></a>Azure Cosmos DB (SQL API)

### <a name="error-code-cosmosdbsqlapioperationfailed"></a>Hata kodu: CosmosDbSqlApiOperationFailed

- **İleti**: `CosmosDbSqlApi operation Failed. ErrorMessage: %msg;.`

- **Neden**: CosmosDbSqlApi işlemiyle ilgili bir sorun.

- **Öneri**: hata ayrıntılarını denetlemek için [Azure Cosmos DB yardım belgesi](../cosmos-db/troubleshoot-dot-net-sdk.md)' ne bakın. Daha fazla yardım için Azure Cosmos DB ekibine başvurun.

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="error-message-the-underlying-connection-was-closed-could-not-establish-trust-relationship-for-the-ssltls-secure-channel"></a>Hata iletisi: temel alınan bağlantı kapatıldı: SSL/TLS güvenli kanalı için güven ilişkisi kurulamadı.

- **Belirtiler**: kopyalama etkinliği aşağıdaki hatayla başarısız olur: 

    `Message: ErrorCode = UserErrorFailedFileOperation, Error Message = The underlying connection was closed: Could not establish trust relationship for the SSL/TLS secure channel.`

- **Neden**: TLS anlaşması sırasında sertifika doğrulaması başarısız oldu.

- **Çözüm**: geçici bir çözüm olarak, Azure Data Lake Storage 1. Için aktarım katmanı GÜVENLIĞI (TLS) doğrulamasını atlamak üzere hazırlanan kopyayı kullanın. Bu sorunu yeniden oluşturmanız ve Ağ İzleyicisi (Netmon) izlemesini toplamanız ve sonra yerel ağ yapılandırmasını denetlemek için ağ ekibinize uygulamanız gerekir.

    ![Sorun giderme sorunları için Azure Data Lake Storage 1. bağlantıları diyagramı.](./media/connector-troubleshoot-guide/adls-troubleshoot.png)


### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Hata iletisi: uzak sunucu bir hata döndürdü: (403) yasak

- **Belirtiler**: kopyalama etkinliği aşağıdaki hatayla başarısız oldu: 

   `Message: The remote server returned an error: (403) Forbidden.   
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....`

- **Neden**: olası bir neden, kullandığınız hizmet sorumlusu veya yönetilen kimliğin belirli klasörlere veya dosyalara erişim iznine sahip olmaması olabilir.

- **Çözüm**: kopyalamanız gereken tüm klasörlere ve alt klasörlere uygun izinleri verin. Daha fazla bilgi için bkz. [Azure Data Factory kullanarak Azure Data Lake Storage 1. veri kopyalama](connector-azure-data-lake-store.md#linked-service-properties).

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Hata iletisi: hizmet sorumlusu kullanılarak erişim belirteci alınamadı. ADAL hatası: service_unavailable

- **Belirtiler**: kopyalama etkinliği aşağıdaki hatayla başarısız olur:

    `Failed to get access token by using service principal.  
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.`

- **Neden**: Azure Active Directory ait olan hizmet belirteci sunucusu (STS) kullanılabilir olmadığında bu, istekleri işlemek için çok meşgul olduğu ve HTTP hatası 503 döndürdüğü anlamına gelir. 

- **Çözüm**: birkaç dakika sonra kopyalama etkinliğini yeniden çalıştırın.


## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="error-code-adlsgen2operationfailed"></a>Hata kodu: ADLSGen2OperationFailed

- **İleti**: `ADLS Gen2 operation failed for: %adlsGen2Message;.%exceptionData;.`

- **Nedenler ve öneriler**: farklı nedenler bu hataya neden olabilir. Olası neden analizi ve ilgili öneri için aşağıdaki listeye bakın.

  | Çözümleme nedeni                                               | Öneri                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Azure Data Lake Storage 2. bir işlemin başarısız olduğunu belirten bir hata oluşturursa.| Azure Data Lake Storage 2. tarafından oluşturulan ayrıntılı hata iletisini denetleyin. Hata geçici bir hata ise işlemi yeniden deneyin. Daha fazla yardım için Azure depolama desteği 'ne başvurun ve hata iletisinde istek KIMLIĞI sağlayın. |
  | Hata iletisi "yasak" dizesini içeriyorsa, kullandığınız hizmet sorumlusu veya yönetilen kimlik Azure Data Lake Storage 2. erişmek için yeterli izne sahip olmayabilir. | Bu hatayı gidermek için [Azure Data Factory kullanarak Azure Data Lake Storage 2. verileri kopyalama ve dönüştürme](./connector-azure-data-lake-storage.md#service-principal-authentication)bölümüne bakın. |
  | Hata iletisi "ınternalservererror" dizesini içeriyorsa hata Azure Data Lake Storage 2. tarafından döndürülür. | Hatanın nedeni geçici bir hata olabilir. Başarısız durumdaysa işlemi yeniden deneyin. Sorun devam ederse, Azure depolama desteği 'ne başvurun ve hata iletisinden istek KIMLIĞINI sağlayın. |

### <a name="request-to-azure-data-lake-storage-gen2-account-caused-a-timeout-error"></a>Azure Data Lake Storage 2. hesabı isteği bir zaman aşımı hatasına neden oldu

- **İleti**: 
  * Hata kodu = `UserErrorFailedBlobFSOperation`
  * Hata Iletisi = `BlobFS operation failed for: A task was canceled.`

- **Neden**: sorun, genellikle kendinden konak INTEGRATION RUNTIME (IR) makinesinde gerçekleşen Azure Data Lake Storage 2. havuz zaman aşımı hatası nedeniyle oluşur.

- **Öneri:** 

    - Şirket içinde barındırılan IR makinenizi ve hedef Azure Data Lake Storage 2. hesabınızı, mümkünse aynı bölgeye yerleştirin. Bu, rastgele bir zaman aşımı hatasından kaçınmanıza ve daha iyi performans elde etmenize yardımcı olabilir.

    - ExpressRoute gibi özel bir ağ ayarı olup olmadığını denetleyin ve ağın yeterli bant genişliğine sahip olduğundan emin olun. Genel bant genişliği düşük olduğunda, şirket içinde barındırılan IR eş zamanlı işler ayarını düşürmenizi öneririz. Bunun yapılması, birden çok eşzamanlı iş arasında ağ kaynağı rekabetini önlemeye yardımcı olabilir.

    - Dosya boyutu orta veya küçük ise, bu tür bir zaman aşımı hatasını azaltmak için ikili olmayan kopya için daha küçük bir blok boyutu kullanın. Daha fazla bilgi için bkz. [BLOB depolama yerleştirme bloğu](/rest/api/storageservices/put-block).

       Özel blok boyutunu belirtmek için, JSON dosya düzenleyicinizdeki özelliği burada gösterildiği gibi düzenleyin:
    
        ```
        "sink": {
            "type": "DelimitedTextSink",
            "storeSettings": {
                "type": "AzureBlobFSWriteSettings",
                "blockSizeInMB": 8
            }
        }
        ```

                  
## <a name="azure-files-storage"></a>Azure dosyaları depolama

### <a name="error-code-azurefileoperationfailed"></a>Hata kodu: AzureFileOperationFailed

- **İleti**: `Azure File operation Failed. Path: %path;. ErrorMessage: %msg;.`

- **Neden**: Azure Files Storage işlemiyle ilgili bir sorun.

- **Öneri**: hata ayrıntılarını denetlemek için bkz. [Azure dosyaları yardımı](/rest/api/storageservices/file-service-error-codes). Daha fazla yardım için Azure dosyaları ekibine başvurun.


## <a name="azure-synapse-analytics-azure-sql-database-and-sql-server"></a>Azure SYNAPSE Analytics, Azure SQL veritabanı ve SQL Server

### <a name="error-code-sqlfailedtoconnect"></a>Hata kodu: SqlFailedToConnect

- **İleti**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`
- **Nedenler ve öneriler**: farklı nedenler bu hataya neden olabilir. Olası neden analizi ve ilgili öneri için aşağıdaki listeye bakın.

    | Çözümleme nedeni                                               | Öneri                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Azure SQL için, hata iletisi "SqlErrorNumber = 47073" dizesini içeriyorsa, bağlantı ayarında genel ağ erişiminin reddedildiği anlamına gelir. | Azure SQL güvenlik duvarında **ortak ağ erişimini reddet** seçeneğini *Hayır* olarak ayarlayın. Daha fazla bilgi için bkz. [Azure SQL bağlantı ayarları](../azure-sql/database/connectivity-settings.md#deny-public-network-access). |
    | Azure SQL için, hata iletisi "SqlErrorNumber = [ErrorCode]" gibi bir SQL hata kodu içeriyorsa Azure SQL sorun giderme kılavuzu 'na bakın. | Öneri için bkz. [Azure SQL veritabanı ve Azure SQL yönetilen örneği ile bağlantı sorunlarını ve diğer hataları giderme](../azure-sql/database/troubleshoot-common-errors-issues.md). |
    | 1433 bağlantı noktasının güvenlik duvarı izin verilenler listesinde olup olmadığını denetleyin. | Daha fazla bilgi için bkz. [SQL Server tarafından kullanılan bağlantı noktaları](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by-). |
    | Hata iletisi "SqlException" dizesini içeriyorsa, SQL veritabanı hata belirli bir işlemin başarısız olduğunu gösterir. | Daha fazla bilgi için, [veritabanı altyapısı HATALARıNDA](/sql/relational-databases/errors-events/database-engine-events-and-errors)SQL hata kodu ile arama yapın. Daha fazla yardım için Azure SQL desteği ile iletişime geçin. |
    | Bu geçici bir sorun (örneğin, bir Instable ağ bağlantısı) varsa, azaltma için etkinlik ilkesine yeniden dene ekleyin. | Daha fazla bilgi için bkz. [Azure Data Factory Işlem hatları ve etkinlikleri](./concepts-pipelines-activities.md#activity-policy). |
    | Hata iletisi "IP adresi olan Istemci" dizesini içeriyorsa... ' sunucusuna erişime izin verilmiyor "ve Azure SQL veritabanı 'na bağlanmaya çalışıyorsunuz, hata genellikle bir Azure SQL veritabanı güvenlik duvarı sorunundan kaynaklanır. | Azure SQL Server güvenlik duvarı yapılandırmasında, **Azure hizmetlerine ve kaynaklarına bu sunucuya erişmesine Izin ver** seçeneğini etkinleştirin. Daha fazla bilgi için bkz. [Azure SQL veritabanı ve Azure SYNAPSE IP güvenlik duvarı kuralları](../azure-sql/database/firewall-configure.md). |
    
### <a name="error-code-sqloperationfailed"></a>Hata kodu: SqlOperationFailed

- **İleti**: `A database operation failed. Please search error to get more details.`

- **Nedenler ve öneriler**: farklı nedenler bu hataya neden olabilir. Olası neden analizi ve ilgili öneri için aşağıdaki listeye bakın.

    | Çözümleme nedeni                                               | Öneri                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Hata iletisi "SqlException" dizesini içeriyorsa SQL veritabanı belirli bir işlemi başarısız olduğunu belirten bir hata oluşturur. | SQL hatası net değilse, veritabanını en son uyumluluk düzeyi olan ' 150 ' olarak değiştirmeyi deneyin. En son sürüm SQL hatalarını oluşturabilir. Daha fazla bilgi için [belgelere](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat) bakın. <br/> SQL sorunlarını giderme hakkında daha fazla bilgi için, [veritabanı altyapısı HATALARıNDA](/sql/relational-databases/errors-events/database-engine-events-and-errors)SQL hata kodu ile arama yapın. Daha fazla yardım için Azure SQL desteği ile iletişime geçin. |
    | Hata iletisi "PdwManagedToNativeInteropException" dizesini içeriyorsa, genellikle kaynak ve havuz sütun boyutları arasında uyuşmazlık olur. | Hem kaynak hem de havuz sütunlarının boyutunu denetleyin. Daha fazla yardım için Azure SQL desteği ile iletişime geçin. |
    | Hata iletisi "InvalidOperationException" dizesini içeriyorsa, genellikle geçersiz giriş verileri olur. | Bu sorunla karşılaşan satırı belirlemek için kopyalama etkinliğinde hata toleransı özelliğini etkinleştirin. Bu, daha fazla araştırma için sorunlu satırları depolamaya yeniden yönlendirebilir. Daha fazla bilgi için [Azure Data Factory kopyalama etkinliğinin hata toleransı](./copy-activity-fault-tolerance.md)bölümüne bakın. |


### <a name="error-code-sqlunauthorizedaccess"></a>Hata kodu: SqlUnauthorizedAccess

- **İleti**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Neden**: kimlik bilgileri yanlış veya oturum açma hesabı SQL veritabanına erişemiyor.

- **Öneri**: oturum açma hesabının SQL veritabanına erişmek için yeterli izinlere sahip olduğundan emin olun.


### <a name="error-code-sqlopenconnectiontimeout"></a>Hata kodu: SqlOpenConnectionTimeout

- **İleti**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Neden**: sorun bir SQL veritabanı geçici hatası olabilir.

- **Öneri**: bağlı hizmet bağlantı dizesini daha büyük bir bağlantı zaman aşımı değeri ile güncelleştirmek için işlemi yeniden deneyin.


### <a name="error-code-sqlautocreatetabletypemapfailed"></a>Hata kodu: SqlAutoCreateTableTypeMapFailed

- **İleti**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Neden**: oto oluşturma tablosu kaynak gereksinimini karşılamaz.

- **Öneri**: *eşlemelerde* sütun türünü güncelleştirin veya havuz tablosunu hedef sunucuda el ile oluşturun.


### <a name="error-code-sqldatatypenotsupported"></a>Hata kodu: SqlDataTypeNotSupported

- **İleti**: `A database operation failed. Check the SQL errors.`

- **Neden**: sorun SQL kaynağında meydana gelirse ve hata SqlDateTime overflow ile ilgiliyse, veri değeri Logic Type aralığını (1/1/1753 12:00:00:-12/31/9999 11:59:59 PM) aşıyor.

- **Öneri**: türü kaynak SQL sorgusunda dizeye atayın veya kopyalama etkinliği sütun eşlemesinde, sütun türünü *dize* olarak değiştirin.

- **Neden**: sorun SQL havuzunda gerçekleşirse ve hata SqlDateTime overflow ile ilgiliyse, veri değeri havuz tablosundaki izin verilen aralığı aşıyor.

- **Öneri**: karşılık gelen sütun türünü havuz tablosundaki *datetime2* türüne güncelleştirin.


### <a name="error-code-sqlinvaliddbstoredprocedure"></a>Hata kodu: SqlInvalidDbStoredProcedure

- **İleti**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Neden**: belirtilen saklı yordam geçersiz. Nedeni, saklı yordamın herhangi bir veri döndürmemesine neden olabilir.

- **Öneri**: SQL araçlarını kullanarak saklı yordamı doğrulayın. Saklı yordamın verileri döndüre, emin olun.


### <a name="error-code-sqlinvaliddbquerystring"></a>Hata kodu: SqlInvalidDbQueryString

- **İleti**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Neden**: belirtilen SQL sorgusu geçersiz. Bunun nedeni sorgunun herhangi bir veri döndürmemesine neden olabilir.

- **Öneri**: SQL araçlarını kullanarak SQL sorgusunu doğrulayın. Sorgunun verileri döndüre, emin olun.


### <a name="error-code-sqlinvalidcolumnname"></a>Hata kodu: SqlInvalidColumnName

- **İleti**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Neden**: yapılandırma yanlış olabileceğinden sütun bulunamıyor.

- **Öneri**: sorgudaki sütunu, veri kümesindeki *yapıyı* ve etkinlikteki *eşlemeleri* doğrulayın.


### <a name="error-code-sqlbatchwritetimeout"></a>Hata kodu: SqlBatchWriteTimeout

- **İleti**: `Timeouts in SQL write operation.`

- **Neden**: soruna bir SQL veritabanı geçici hatası neden olmuş olabilir.

- **Öneri**: işlemi yeniden deneyin. Sorun devam ederse Azure SQL desteği 'ne başvurun.


### <a name="error-code-sqlbatchwritetransactionfailed"></a>Hata kodu: SqlBatchWriteTransactionFailed

- **İleti**: `SQL transaction commits failed.`

- **Neden**: özel durum ayrıntıları sürekli bir işlem zaman aşımını gösteriyorsa, tümleştirme çalışma zamanı ve veritabanı arasındaki ağ gecikmesi 30 saniyelik varsayılan eşikten daha fazladır.

- **Öneri**: SQL bağlantılı hizmet bağlantı dizesini 120 veya üzeri bir *bağlantı zaman aşımı* değeriyle güncelleştirin ve etkinliği yeniden çalıştırın.

- **Neden**: özel durum ayrıntıları zaman zaman SQL bağlantısının kopuk olduğunu gösteriyorsa, geçici bir ağ arızası veya bir SQL veritabanı tarafı sorunu olabilir.

- **Öneri**: etkinliği yeniden deneyın ve SQL veritabanı tarafı ölçümlerini inceleyin.


### <a name="error-code-sqlbulkcopyinvalidcolumnlength"></a>Hata kodu: Sqlbulkcopyınvalidcolumnlength

- **İleti**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Neden**: toplu kopyalama programı (bcp) istemcisinden geçersiz bir sütun uzunluğu aldığından SQL toplu kopyalama başarısız oldu.

- **Öneri**: hangi satırın sorunla karşılaşdığını belirlemek için kopyalama etkinliğinde hata toleransı özelliğini etkinleştirin. Bu, daha fazla araştırma için sorunlu satırları depolamaya yönlendirebilir. Daha fazla bilgi için [Azure Data Factory kopyalama etkinliğinin hata toleransı](./copy-activity-fault-tolerance.md)bölümüne bakın.


### <a name="error-code-sqlconnectionisclosed"></a>Hata kodu: Sqlconnectionısclosed

- **İleti**: `The connection is closed by SQL Database.`

- **Neden**: yüksek bir eşzamanlı çalıştırma ve sunucu bağlantıyı sona erdirse SQL bağlantısı SQL veritabanı tarafından kapalıdır.

- **Öneri**: bağlantıyı yeniden deneyin. Sorun devam ederse Azure SQL desteği 'ne başvurun.


### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Hata iletisi: bir karakter dizesinden uniqueidentifier öğesine dönüştürülürken dönüştürme başarısız oldu

- **Belirtiler**: bir tablosal veri kaynağından (SQL Server gibi) verileri, hazırlanan kopya ve PolyBase kullanarak Azure SYNAPSE Analytics 'e kopyaladığınızda, şu hatayı alırsınız:

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **Neden**: Azure SYNAPSE Analytics PolyBase boş bir DIZEYI bir GUID 'ye dönüştüremiyor.

- **Çözüm**: kopyalama etkinliği havuzunda, PolyBase ayarları altında, **varsayılan kullanım türü** seçeneğini *false* olarak ayarlayın.


### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Hata iletisi: beklenen veri türü: DECIMAL (x, x), sorunlu değer

- **Belirtiler**: bir tablosal veri kaynağından (SQL Server gibi) verileri, hazırlanan kopya ve PolyBase kullanarak Azure SYNAPSE Analytics 'e kopyaladığınızda, şu hatayı alırsınız:

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **Neden**: Azure SYNAPSE Analytics PolyBase bir ondalık sütuna boş bir dize (null değer) ekleyemiyor.

- **Çözüm**: kopyalama etkinliği havuzunda, PolyBase ayarları altında, **varsayılan kullanım türü** seçeneğini false olarak ayarlayın.


### <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Hata iletisi: Java özel durum iletisi: HdfsBridge:: CreateRecordReader

- **Belirtiler**: PolyBase kullanarak verileri Azure SYNAPSE Analytics 'e kopyalayın ve aşağıdaki hatayı alırsınız:

    `Message=110802;An internal DMS error occurred that caused this operation to fail.  
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader.  
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....`

- **Neden**: nedeni şemanın (Toplam sütun genişliği) çok büyük (1 MB 'den büyük) olması olabilir. Tüm sütunların boyutunu ekleyerek hedef Azure SYNAPSE Analytics tablosunun şemasını denetleyin:

    - Int = 4 bayt
    - BigInt = 8 bayt
    - Varchar (n), char (n), binary (n), varbinary (n) = n bayt
    - Nvarchar (n), nchar (n) = n * 2 bayt
    - Tarih = 6 bayt
    - DateTime/(2), smalldatetime = 16 bayt
    - DateTimeOffset = 20 bayt
    - Ondalık = 19 bayt
    - Float = 8 bayt
    - Para = 8 bayt
    - Küçük para = 4 bayt
    - Gerçek = 4 bayt
    - Smallint = 2 bayt
    - Süre = 12 bayt
    - Tinyint = 1 bayt

- **Çözüm**: 
    - Sütun genişliğini 1 MB 'tan küçük olacak şekilde küçültün.
    - Veya PolyBase 'i devre dışı bırakarak bir toplu ekleme yaklaşımı kullanın.


### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Hata iletisi: HTTP koşullu üst bilgi (ler) i kullanılarak belirtilen koşul karşılanmadı

- **Belirtiler**: SQL sorgusunu kullanarak Azure SYNAPSE Analytics 'ten veri çekin ve şu hatayı alırsınız:

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **Neden**: Azure SYNAPSE Analytics, Azure depolama 'daki dış tabloyu sorgularken bir sorunla karşılaştı.

- **Çözüm**: aynı sorguyu SQL Server Management Studio (SSMS) içinde çalıştırın ve aynı sonucu elde edip etmeyeceğinizi görmek için denetleyin. Bunu yaparsanız Azure SYNAPSE Analytics 'e yönelik bir destek bileti açın ve Azure SYNAPSE Analytics sunucunuzu ve veritabanı adını sağlayın.


### <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>Performans katmanı düşük ve kopyalamanın başarısız olmasına yol açar

- **Belirtiler**: VERILERI Azure SQL veritabanına kopyalar ve şu hatayı alırsınız: `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **Neden**: Azure SQL veritabanı S1 'de isabet giriş/çıkış (g/ç) limitleri vardır.

- **Çözüm**: sorunu çözmek IÇIN Azure SQL veritabanı performans katmanını yükseltin. 


### <a name="sql-table-cant-be-found"></a>SQL tablosu bulunamıyor 

- **Belirtiler**: verileri karma kopyadan şirket içi SQL Server tablosuna kopyalar ve şu hatayı alırsınız:`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **Neden**: geçerli SQL hesabı .net SqlBulkCopy. WriteToServer tarafından verilen istekleri yürütmek için yeterli izinlere sahip değil.

- **Çözüm**: daha AYRıCALıKLı bir SQL hesabına geçiş yapın.


### <a name="error-message-string-or-binary-data-is-truncated"></a>Hata iletisi: dize veya ikili veriler kesilmiş

- **Belirtiler**: verileri şirket içi Azure SQL Server tablosuna kopyaladığınızda bir hata oluşur. 

- **Neden**: CX SQL tablo şeması tanımında beklenenden daha az uzunluğa sahip bir veya daha fazla sütun içeriyor.

- **Çözüm**: sorunu çözmek için aşağıdakileri deneyin:

    1. Hangi satırlarda soruna sahip olan sorunları gidermek için, SQL havuzu [hata toleransı](./copy-activity-fault-tolerance.md)uygulayın, özellikle "redirectıncompatiblerowsettings."

        > [!NOTE]
        > Hataya dayanıklılık ek yürütme süresi gerektirebilir, bu da daha yüksek maliyetlere neden olabilir.

    2. Hangi sütunların güncelleştirileceğini görmek için SQL tablo şeması sütun uzunluğuna karşı yeniden yönlendirilen verileri iki kez kontrol edin.

    3. Tablo şemasını uygun şekilde güncelleştirin.


## <a name="azure-table-storage"></a>Azure Tablo Depolama

### <a name="error-code-azuretableduplicatecolumnsfromsource"></a>Hata kodu: AzureTableDuplicateColumnsFromSource

- **İleti**: `Duplicate columns with same name '%name;' are detected from source. This is NOT supported by Azure Table Storage sink.`

- **Neden**: aşağıdaki nedenlerden biri için yinelenen kaynak sütunları oluşabilir:
   * Veritabanını kaynak ve uygulanan tablo birleşimleri olarak kullanıyorsunuz.
   * Üst bilgi satırında yinelenen sütun adlarıyla yapılandırılmamış CSV dosyaları var.

- **Öneri**: gerektiğinde kaynak sütunları çift işaretleyin ve onarın.


## <a name="db2"></a>DB2

### <a name="error-code-db2driverrunfailed"></a>Hata kodu: DB2DriverRunFailed

- **İleti**: `Error thrown from driver. Sql code: '%code;'`

- **Neden**: hata ILETISI "SQLSTATE = 51002 SQLCODE =-805" dizesini içeriyorsa, [Azure Data Factory kullanarak DB2 'den veri kopyalama](./connector-db2.md#linked-service-properties)içindeki "ipucu" nu izleyin.

- **Öneri**: özelliğindeki "nullıd" ayarlamaya çalışın `packageCollection`  .


## <a name="delimited-text-format"></a>Sınırlandırılmış metin biçimi

### <a name="error-code-delimitedtextcolumnnamenotallownull"></a>Hata kodu: Delimitedtextcolumnsüs Otallownull

- **İleti**: `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Neden**: etkinlikte ' firstRowAsHeader ' ayarlandığında, ilk satır sütun adı olarak kullanılır. Bu hata, ilk satırın boş bir değer (örneğin, ' ColumnA, ColumnB ') içerdiği anlamına gelir.

- **Öneri**: ilk satırı denetleyin ve boşsa değeri düzeltir.


### <a name="error-code-delimitedtextmorecolumnsthandefined"></a>Hata kodu: Delimitedtextmorecolumnsıısdefined tanımlandı

- **İleti**: `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **Nedenler ve öneriler**: farklı nedenler bu hataya neden olabilir. Olası neden analizi ve ilgili öneri için aşağıdaki listeye bakın.

  | Çözümleme nedeni                                               | Öneri                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Sorunlu satırın sütun sayısı, ilk satırın sütun sayısından daha büyük. Bunun nedeni, bir veri sorunu veya hatalı sütun sınırlayıcısı veya quote char ayarlarından kaynaklanıyor olabilir. | Hata iletisinden satır sayısını alın, satırın sütununu kontrol edin ve verileri onarın. |
  | Beklenen sütun sayısı "1" ise, bir hata iletisinde yanlış sıkıştırma veya Biçim ayarları belirtmiş olabilirsiniz. bu durum, Data Factory dosyalarınızı yanlış ayrıştırmasına neden olur. | Kaynak dosyalarınıza eşleştiğinden emin olmak için biçim ayarlarını kontrol edin. |
  | Kaynağınız bir klasörsdayken, belirtilen klasörün altındaki dosyalar farklı bir şemaya sahip olabilir. | Belirtilen klasördeki dosyaların aynı şemaya sahip olduğundan emin olun. |


## <a name="dynamics-365-common-data-service-and-dynamics-crm"></a>Dynamics 365, Common Data Service ve Dynamics CRM

### <a name="error-code-dynamicscreateserviceclienterror"></a>Hata kodu: DynamicsCreateServiceClientError

- **İleti**: `This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **Neden**: sorun, Dynamics sunucu tarafında geçici bir sorundur.

- **Öneri**: işlem hattını yeniden çalıştırın. Yeniden başarısız olursa paralelliği azaltmayı deneyin. Sorun devam ederse, Dynamics destek 'e başvurun.


### <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>Bir şemayı veya önizleme verilerini içeri aktardığınızda eksik sütunlar

- **Belirtiler**: bir şemayı veya önizleme verilerini içeri aktardığınızda bazı sütunlar eksik. Hata iletisi: `The valid structure information (column name and type) are required for Dynamics source.`

- **Neden**: Bu sorun tasarım tarafından yapılır çünkü Data Factory ilk 10 kayıtta hiçbir değer içermeyen sütunları gösteremez. Eklediğiniz sütunların doğru biçimde olduğundan emin olun. 

- **Öneri**: eşleme sekmesine sütunları el ile ekleyin.


### <a name="error-code-dynamicsmissingtargetformultitargetlookupfield"></a>Hata kodu: DynamicsMissingTargetForMultiTargetLookupField

- **İleti**: `Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **Neden**: hedef sütun kaynakta veya sütun eşlemesinde yok.

- **Öneri:**  
  1. Kaynağın hedef sütununu içerdiğinden emin olun. 
  2. Sütun eşlemesinde Target sütununu ekleyin. Havuz sütununun *{FieldName} @EntityReference* biçiminde olduğundan emin olun.


### <a name="error-code-dynamicsinvalidtargetformultitargetlookupfield"></a>Hata kodu: DynamicsInvalidTargetForMultiTargetLookupField

- **İleti**: `The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **Neden**: bir çok hedef arama alanının hedef varlığı olarak yanlış bir varlık adı belirtildi.

- **Öneri**: çoklu hedef arama alanı için geçerli bir varlık adı belirtin.


### <a name="error-code-dynamicsinvalidtypeformultitargetlookupfield"></a>Hata kodu: DynamicsInvalidTypeForMultiTargetLookupField

- **İleti**: `The provided target type is not a valid string. Field: '%fieldName;'.`

- **Neden**: hedef sütundaki değer bir dize değil.

- **Öneri**: çoklu hedef arama hedefi sütununda geçerli bir dize belirtin.


### <a name="error-code-dynamicsfailedtorequetserver"></a>Hata kodu: Dynamicsfailedtobir Etserver

- **İleti**: `The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **Neden**: Dynamics sunucusu kararlı değil veya erişilemez durumda veya ağda sorun yaşanıyor.

- **Öneri**: daha fazla ayrıntı için ağ bağlantısını denetleyin veya Dynamics Server günlüğünü kontrol edin. Daha fazla yardım için Dynamics desteği ile iletişime geçin.


### <a name="error-code--dynamicsfailedtoconnect"></a>Hata kodu: DynamicsFailedToConnect 
 
 - **İleti**: `Failed to connect to Dynamics: %message;` 
 

 - **Neden**: `Office 365 auth with OAuth failed` hata iletisinde görürseniz, sunucunuzun OAuth ile uyumlu olmayan bazı yapılandırmalara sahip olabileceği anlamına gelir. 
 
 - **Öneri:** 
    1. Yardım için ayrıntılı hata iletisiyle birlikte Dynamics destek ekibine başvurun.  
    1. Hizmet sorumlusu kimlik doğrulamasını kullanın ve bu makaleye başvurabilirsiniz: [örnek: Azure AD hizmet sorumlusu ve sertifika kimlik doğrulaması kullanarak Dynamics Online](https://docs.microsoft.com/azure/data-factory/connector-dynamics-crm-office-365#example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication). 
 

 - **Neden**: `Unable to retrieve authentication parameters from the serviceUri` hata iletisinde görürseniz, trafiği ele almak için yanlış Dynamics Service URL 'sini veya proxy/güvenlik duvarını girdiğiniz anlamına gelir. 
 
 - **Öneri:**
    1. Bağlı hizmete doğru hizmet URI 'sini yerleştirdiğinizden emin olun. 
    1. Şirket içinde barındırılan IR kullanırsanız, güvenlik duvarının/proxy 'sinin Dynamics sunucusuna yapılan istekleri kesmesini içermediğinden emin olun. 
   
 
 - **Neden**: `An unsecured or incorrectly secured fault was received from the other party` hata iletisinde görürseniz, sunucu tarafında beklenmedik yanıtların kullanıldığı anlamına gelir. 
 
 - **Öneri:** 
    1. Office 365 kimlik doğrulamasını kullanıyorsanız Kullanıcı adınızın ve parolanızın doğru olduğundan emin olun. 
    1. Doğru hizmet URI 'sini girdiğinizden emin olun. 
    1. Bölgesel CRM URL 'sini (URL 'nin ' CRM 'den sonra bir numarası varsa) kullanırsanız, doğru bölgesel tanımlayıcıyı kullandığınızdan emin olun.
    1. Yardım için Dynamics destek ekibine başvurun. 
 

 - **Neden**: `No Organizations Found` hata iletisinde görürseniz, kuruluşunuzun adı yanlış ya da hizmet URL 'SINDE yanlış bir CRM bölge tanımlayıcısı kullandınız demektir. 
 
 - **Öneri:** 
    1. Doğru hizmet URI 'sini girdiğinizden emin olun.
    1. Bölgesel CRM URL 'sini (URL 'nin ' CRM 'den sonra bir numarası varsa) kullanırsanız, doğru bölgesel tanımlayıcıyı kullandığınızdan emin olun. 
    1. Yardım için Dynamics destek ekibine başvurun. 

 
 - **Neden**: `401 Unauthorized` ve AAD ile ilgili hata iletisi görürseniz, hizmet sorumlusu ile ilgili bir sorun olduğu anlamına gelir. 

 - **Öneri**: hizmet sorumlusu sorununu onarmak için hata iletisindeki yönergeleri uygulayın.  
 
 
 - **Neden**: diğer hatalar için genellikle sorun sunucu tarafında yer alır. 

 - **Öneri**: bağlantı kurmak Için [xrmtoolbox](https://www.xrmtoolbox.com/) kullanın. Hata devam ederse yardım için Dynamics destek ekibine başvurun. 
 
 
### <a name="error-code--dynamicsoperationfailed"></a>Hata kodu: DynamicsOperationFailed 
 
- **İleti**: `Dynamics operation failed with error code: %code;, error message: %message;.` 

- **Neden**: işlem sunucu tarafında başarısız oldu. 

- **Öneri**: hata iletisinden Dynamics işleminin hata kodunu ayıklayın `Dynamics operation failed with error code: {code}` ve daha ayrıntılı bilgi için [Web hizmeti hata kodları](https://docs.microsoft.com/powerapps/developer/data-platform/org-service/web-service-error-codes) makalesine başvurun. Gerekirse Dynamics destek ekibine başvurabilirsiniz. 
 
 
### <a name="error-code--dynamicsinvalidfetchxml"></a>Hata kodu: DynamicsInvalidFetchXml 
  
- **İleti**: `The Fetch Xml query specified is invalid.` 

- **Neden**: Fetch XML dosyasında bir hata var.  

- **Öneri**: Fetch XML içindeki hatayı düzeltir. 
 
 
### <a name="error-code--dynamicsmissingkeycolumns"></a>Hata kodu: DynamicsMissingKeyColumns 
 
- **İleti**: `Input DataSet must contain keycolumn(s) in Upsert/Update scenario. Missing key column(s): %column;`
 
- **Neden**: kaynak verileri, havuz varlığının anahtar sütununu içermiyor. 

- **Öneri**: anahtar sütunlarının kaynak verilerde olduğunu onaylayın veya kaynak sütununu havuz varlığındaki anahtar sütununa eşleyin. 
 
 
### <a name="error-code--dynamicsprimarykeymustbeguid"></a>Hata kodu: DynamicsPrimaryKeyMustBeGuid 
 
- **İleti**: `The primary key attribute '%attribute;' must be of type guid.` 
 
- **Neden**: birincil anahtar sütununun türü ' Guid ' değil. 
 
- **Öneri**: kaynak verilerdeki birincil anahtar sütununun ' GUID ' türünde olduğundan emin olun. 
 

### <a name="error-code--dynamicsalternatekeynotfound"></a>Hata kodu: DynamicsAlternateKeyNotFound 
 
- **İleti**: `Cannot retrieve key information of alternate key '%key;' for entity '%entity;'.` 
 
- **Neden**: belirtilen alternatif anahtar yok, bunun nedeni yanlış anahtar adları veya yetersiz izinler olabilir. 
 
- **Öneri:** <br/> 
    1. Anahtar adındaki yazım hatalarını düzeltir.<br/> 
    1. Varlıkta yeterli izinlere sahip olduğunuzdan emin olun. 
 
 
### <a name="error-code--dynamicsinvalidschemadefinition"></a>Hata kodu: DynamicsInvalidSchemaDefinition 
 
- **İleti**: `The valid structure information (column name and type) are required for Dynamics source.` 
 
- **Neden**: sütun eşlemesindeki havuz sütunları ' Type ' özelliğini kaçırır. 
 
- **Öneri**: portalda JSON düzenleyicisini kullanarak, sütun eşlemesinde ' Type ' özelliğini bu sütunlara ekleyebilirsiniz. 


## <a name="ftp"></a>FTP

### <a name="error-code-ftpfailedtoconnecttoftpserver"></a>Hata kodu: FtpFailedToConnectToFtpServer

- **İleti**: `Failed to connect to FTP server. Please make sure the provided server information is correct, and try again.`

- **Neden**: bir FTP sunucusuna bağlanmak IÇIN güvenli FTP (SFTP) bağlı hizmetini kullanma gıbı, FTP sunucusu için yanlış bir bağlı hizmet türü kullanılabilir.

- **Öneri**: hedef sunucunun bağlantı noktasını denetleyin. FTP bağlantı noktası 21 ' i kullanır.


## <a name="http"></a>HTTP

### <a name="error-code-httpfilefailedtoread"></a>Hata kodu: HttpFileFailedToRead

- **İleti**: `Failed to read data from http server. Check the error from http server：%message;`

- **Neden**: Bu hata, http sunucusuna Azure Data Factory, ancak http isteği işlemi başarısız olduğunda oluşur.

- **Öneri**: hata iletisindeki http durum kodunu denetleyip uzak sunucu sorununu düzeltemedi.


## <a name="oracle"></a>Oracle

### <a name="error-code-argumentoutofrangeexception"></a>Hata kodu: ArgumentOutOfRangeException

- **İleti**: `Hour, Minute, and Second parameters describe an un-representable DateTime.`

- **Neden**: Data Factory DateTime değerleri 0001-01-01 00:00:00 ile 9999-12-31 23:59:59 arasında bir aralıkta desteklenir. Ancak Oracle, Data Factory hata veren BC yüzyıl veya min/sn>59 gibi daha geniş bir tarih saat değerlerini destekler.

- **Öneri:** 

    Oracle 'daki değerin Data Factory aralığında olup olmadığını görmek için, ' i çalıştırın `select dump(<column name>)` . 

    Sonucun bayt sırasını öğrenmek için bkz. [nasıl yapılır: Oracle 'Da nasıl depolanır?](https://stackoverflow.com/questions/13568193/how-are-dates-stored-in-oracle).


## <a name="orc-format"></a>ORC biçimi

### <a name="error-code-orcjavainvocationexception"></a>Hata kodu: Orcjavaınvocationexception

- **İleti**: `An error occurred when invoking Java, message: %javaException;.`
- **Nedenler ve öneriler**: farklı nedenler bu hataya neden olabilir. Olası neden analizi ve ilgili öneri için aşağıdaki listeye bakın.

    | Çözümleme nedeni                                               | Öneri                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Hata iletisi "Java. lang. OutOfMemory", "Java yığın alanı" ve "doubleCapacity" dizelerini içerdiğinde, genellikle tümleştirme çalışma zamanının eski bir sürümünde bir bellek yönetimi sorunudur. | Şirket içinde barındırılan Integration Runtime kullanıyorsanız en son sürüme yükseltmenizi öneririz. |
    | Hata iletisi "Java. lang. OutOfMemory" dizesini içerdiğinde, tümleştirme çalışma zamanı dosyaları işlemek için yeterli kaynağa sahip değildir. | Tümleştirme çalışma zamanı 'nda eşzamanlı çalıştırmaları sınırlayın. Şirket içinde barındırılan IR için, 8 GB 'a eşit veya daha büyük belleği olan güçlü bir makineye kadar ölçeklendirin. |
    |Hata iletisi "NullPointerReference" dizesini içerdiğinde, nedeni geçici bir hata olabilir. | İşlemi yeniden deneyin. Sorun devam ederse desteğe başvurun. |
    | Hata iletisi "BufferOverflowException" dizesini içerdiğinde, nedeni geçici bir hata olabilir. | İşlemi yeniden deneyin. Sorun devam ederse desteğe başvurun. |
    | Hata iletisi "Java. lang. ClassCastException:org. Apache. Hadoop. Hive. serde2. IO. Hivecharyazılabilir dizesini içerdiğinde, bu durum, Java çalışma zamanı içinde bir tür dönüştürme sorunu olabilir. Genellikle, kaynak verilerin Java çalışma zamanı 'nda iyi işlenemeyeceği anlamına gelir. | Bu bir veri sorunudur. ORC biçim verilerinde char veya varchar yerine bir dize kullanmayı deneyin. |

### <a name="error-code-orcdatetimeexceedlimit"></a>Hata kodu: OrcDateTimeExceedLimit

- **İleti**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **Neden**: DateTime değeri ' 0001-01-01 00:00:00 ' Ise, [Jülyen takvimi ile Gregoryen takvimi](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates)arasındaki farklardan kaynaklanabilir.

- **Öneri**: ticks değerini denetleyin ve ' 0001-01-01 00:00:00 ' Tarih saat değerini kullanmaktan kaçının.


## <a name="parquet-format"></a>Parquet biçimi

### <a name="error-code-parquetjavainvocationexception"></a>Hata kodu: Parquetjavaınvocationexception

- **İleti**: `An error occurred when invoking java, message: %javaException;.`

- **Nedenler ve öneriler**: farklı nedenler bu hataya neden olabilir. Olası neden analizi ve ilgili öneri için aşağıdaki listeye bakın.

    | Çözümleme nedeni                                               | Öneri                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Hata iletisi "Java. lang. OutOfMemory", "Java yığın alanı" ve "doubleCapacity" dizelerini içerdiğinde, genellikle Integration Runtime eski bir sürümünde bellek yönetimi sorunudur. | Şirket içinde barındırılan IR kullanıyorsanız ve sürüm 3.20.7159.1 'den daha eski ise en son sürüme yükseltmenizi öneririz. |
    | Hata iletisi "Java. lang. OutOfMemory" dizesini içerdiğinde, tümleştirme çalışma zamanı dosyaları işlemek için yeterli kaynağa sahip değildir. | Tümleştirme çalışma zamanı 'nda eşzamanlı çalıştırmaları sınırlayın. Şirket içinde barındırılan IR için, 8 GB 'a eşit veya daha büyük belleği olan güçlü bir makineye kadar ölçeklendirin. |
    | Hata iletisi "NullPointerReference" dizesini içerdiğinde, bu geçici bir hata olabilir. | İşlemi yeniden deneyin. Sorun devam ederse desteğe başvurun. |

### <a name="error-code-parquetinvalidfile"></a>Hata kodu: Parquetınvalidfile

- **İleti**: `File is not a valid Parquet file.`

- **Neden**: Bu bir Parquet dosya sorunudur.

- **Öneri**: girişin geçerli bir Parquet dosyası olup olmadığını denetleyin.


### <a name="error-code-parquetnotsupportedtype"></a>Hata kodu: ParquetNotSupportedType

- **İleti**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Neden**: Parquet biçimi Azure Data Factory desteklenmiyor.

- **Öneri**: [Azure Data Factory etkinliği tarafından desteklenen dosya biçimlerine ve sıkıştırma codec larına](./supported-file-formats-and-compression-codecs.md)giderek kaynak verileri iki kez kontrol edin.


### <a name="error-code-parquetmisseddecimalprecisionscale"></a>Hata kodu: ParquetMissedDecimalPrecisionScale

- **İleti**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Neden**: sayı duyarlığı ve ölçek ayrıştırıldı, ancak böyle bir bilgi sağlanmadı.

- **Öneri**: kaynak doğru duyarlık ve ölçek bilgilerini döndürmüyor. Bilgi için sorun sütununu kontrol edin.


### <a name="error-code-parquetinvaliddecimalprecisionscale"></a>Hata kodu: ParquetInvalidDecimalPrecisionScale

- **İleti**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Neden**: şema geçersiz.

- **Öneri**: sorun sütununu duyarlık ve ölçek için denetleyin.


### <a name="error-code-parquetcolumnnotfound"></a>Hata kodu: ParquetColumnNotFound

- **İleti**: `Column %column; does not exist in Parquet file.`

- **Neden**: kaynak şema, havuz şeması ile uyuşmuyor.

- **Öneri**: etkinlikteki eşlemeleri denetleyin. Kaynak sütunun doğru havuz sütunuyla eşleştiriikediğine emin olun.


### <a name="error-code-parquetinvaliddataformat"></a>Hata kodu: Parquetınvaliddataformat

- **İleti**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **Neden**: veriler eşlemeler. Source içinde belirtilen türe dönüştürülemiyor.

- **Öneri**: kaynak verileri iki kez kontrol edin veya kopyalama etkinliği sütun eşlemesinde bu sütun için doğru veri türünü belirtin. Daha fazla bilgi için, bkz. [Azure Data Factory etkinlik kopyalama tarafından desteklenen dosya biçimleri ve sıkıştırma codec bileşenleri](./supported-file-formats-and-compression-codecs.md).


### <a name="error-code-parquetdatacountnotmatchcolumncount"></a>Hata kodu: ParquetDataCountNotMatchColumnCount

- **İleti**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Neden**: kaynak sütun sayısı ile havuz sütun sayısı arasında bir uyuşmazlık var.

- **Öneri**: kaynak sütun sayısının ' Mapping ' içindeki havuz sütun sayısıyla aynı olduğundan emin olmak için iki kez denetleyin.


### <a name="error-code-parquetdatatypenotmatchcolumntype"></a>Hata kodu: ParquetDataTypeNotMatchColumnType

- **İleti**: `The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **Neden**: kaynaktaki veriler, havuzda tanımlanan türe dönüştürülemez.

- **Öneri**: Mapping. Sink içinde doğru bir tür belirtin.


### <a name="error-code-parquetbridgeinvaliddata"></a>Hata kodu: Parquetköprüınvaliddata

- **İleti**: `%message;`

- **Neden**: veri değeri sınırı aştı.

- **Öneri**: işlemi yeniden deneyin. Sorun devam ederse bizimle iletişim kurun.


### <a name="error-code-parquetunsupportedinterpretation"></a>Hata kodu: Parquetunsupportedyorumlama

- **İleti**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **Neden**: Bu senaryo desteklenmiyor.

- **Öneri**: ' Parquetınterpotfor ' ' mini SQL ' olmamalıdır.


### <a name="error-code-parquetunsupportfilelevelcompressionoption"></a>Hata kodu: ParquetUnsupportFileLevelCompressionOption

- **İleti**: `File level compression is not supported for Parquet.`

- **Neden**: Bu senaryo desteklenmiyor.

- **Öneri**: yükte ' CompressionType ' öğesini kaldırın.


### <a name="error-code-usererrorjniexception"></a>Hata kodu: UserErrorJniException

- **İleti**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **Neden**: bazı geçersiz (genel) bağımsız değişkenler ayarlanmadığından bir Java sanal makinesi (JVM) oluşturulamıyor.

- **Öneri**: kendınden konak IR 'nizin *her bir düğümünü* barındıran makinede oturum açın. Sistem değişkeninin doğru şekilde ayarlandığından emin olmak için aşağıdaki gibi denetleyin: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G` . Tüm IR düğümlerini yeniden başlatın ve ardından işlem hattını yeniden çalıştırın.


### <a name="arithmetic-overflow"></a>Aritmetik taşma

- **Belirtiler**: Parquet dosyalarını kopyaladığınızda hata iletisi oluştu: `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **Neden**: Oracle 'Dan Parquet 'e dosya kopyaladığınızda, şu anda yalnızca duyarlık <= 38 ve tamsayı bölümü <= 20 ' nin uzunluğu desteklenir. 

- **Çözüm**: geçici bir çözüm olarak, bu sorunla olan herhangi BIR sütunu VARCHAR2 'e dönüştürebilirsiniz.


### <a name="no-enum-constant"></a>Sabit Listesi sabiti yok

- **Belirtiler**: verileri Parquet biçiminde kopyaladığınızda hata iletisi oluştu: `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` , veya: `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test` .

- **Neden**: 

    Sorun, boşluk veya desteklenmeyen özel karakterlerden kaynaklanabilir (örneğin,; {} () \n\t =) sütun adında, Parquet böyle bir biçimi desteklemediğinden. 

    Örneğin, *contoso (test)* gibi bir sütun adı, koddan köşeli parantez içinde bulunan türü ayrıştıracaktır [](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");` . Böyle bir "test" türü olmadığından hata oluşur.

    Desteklenen türleri denetlemek için GitHub [Apache/Parquet-Mr sitesine](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java)gidin.

- **Çözüm**: 

    Şunları görmek için iki kez kontrol edin:
    - Havuz sütun adında boşluk var.
    - Boşluk içeren ilk satır sütun adı olarak kullanılır.
    - OriginalType türü destekleniyor. Şu özel karakterleri kullanmaktan kaçınmaya çalışın: `,;{}()\n\t=` . 


## <a name="rest"></a>REST

### <a name="error-code-restsinkcallfailed"></a>Hata kodu: RestSinkCallFailed

- **İleti**: `Rest Endpoint responded with Failure from server. Check the error from server:%message;`

- **Neden**: Bu hata, Azure Data Factory http protokolü üzerinden REST uç noktasına geldiğinde meydana gelir ve istek işlemi başarısız olur.

- **Öneri**: http durum kodunu veya hata iletisindeki iletiyi denetleyip uzak sunucu sorununu düzeltemedi.

### <a name="unexpected-network-response-from-the-rest-connector"></a>REST bağlayıcısından beklenmeyen ağ yanıtı

- **Belirtiler**: uç nokta bazen Rest bağlayıcısının beklenmedik bir yanıtını (400, 401, 403, 500) alır.

- **Neden**: Rest kaynak BAĞLAYıCıSı, http isteği oluştururken bağlı hizmet/veri kümesi/kopyalama kaynağından parametre olarak URL ve http yöntemini/üstbilgisini kullanır. Bu sorun büyük olasılıkla belirtilen bir veya daha fazla parametrede bazı hatalardan kaynaklanır.

- **Çözüm**: 
    - Parametrenin neden olup olmadığını görmek için bir komut Istemi penceresinde ' KIVI ' kullanın (**kabul et** ve **Kullanıcı Aracısı** üstbilgileri her zaman eklenmelidir):
    
      `curl -i -X <HTTP method> -H <HTTP header1> -H <HTTP header2> -H "Accept: application/json" -H "User-Agent: azure-data-factory/2.0" -d '<HTTP body>' <URL>`
      
      Komut, aynı beklenmeyen yanıtı döndürürse, beklenen yanıtı döndürünceye kadar önceki parametreleri ' kıvır ' ile onarın. 

      Komutun daha gelişmiş kullanımı için ' kıvrık--help ' de kullanabilirsiniz.

    - Yalnızca Data Factory REST Bağlayıcısı beklenmedik bir yanıt döndürürse, daha fazla sorun giderme için Microsoft desteğine başvurun.
    
    - ' Kıvrımlı ' bir SSL sertifikası doğrulama sorununu yeniden oluşturmak için uygun olmayabilir. Bazı senaryolarda, hiçbir SSL sertifikası doğrulama sorunu olmadan ' kıvrık ' komutu başarıyla yürütüldü. Ancak aynı URL bir tarayıcıda yürütüldüğünde, istemcinin sunucu ile güven kurması için bir SSL sertifikası döndürülmez.

      Önceki durumda **Postman** ve **Fiddler** gibi araçlar önerilir.


## <a name="sftp"></a>SFTP

#### <a name="error-code-sftpoperationfail"></a>Hata kodu: SftpOperationFail

- **İleti**: `Failed to '%operation;'. Check detailed error from SFTP.`

- **Neden**: SFTP işlemiyle ilgili bir sorun.

- **Öneri**: SFTP 'den hata ayrıntılarına bakın.


### <a name="error-code-sftprenameoperationfail"></a>Hata kodu: SftpRenameOperationFail

- **İleti**: `Failed to rename the temp file. Your SFTP server doesn't support renaming temp file, set "useTempFileRename" as false in copy sink to disable uploading to temp file.`

- **Neden**: SFTP sunucunuz geçici dosyayı yeniden adlandırmayı desteklemiyor.

- **Öneri**: Temp dosyasına karşıya yüklemeyi devre dışı bırakmak için kopya havuzunda "useTempFileRename" değerini false olarak ayarlayın.


### <a name="error-code-sftpinvalidsftpcredential"></a>Hata kodu: Sftpınvalidsftpcredential

- **İleti**: `Invalid SFTP credential provided for '%type;' authentication type.`

- **Neden**: özel anahtar içeriği Azure anahtar KASASı veya SDK 'dan getirilir, ancak doğru kodlanmamış.

- **Öneri:**  

    Özel anahtar içeriği anahtar kasanızdan ise, doğrudan SFTP bağlantılı hizmetine yüklerseniz özgün anahtar dosyası çalışabilir.

    Daha fazla bilgi için bkz. [Azure Data Factory kullanarak SFTP sunucusundan verileri kopyalama](./connector-sftp.md#use-ssh-public-key-authentication). Özel anahtar içeriği Base64 kodlamalı SSH özel anahtar içeridir.

    *Tüm* özgün özel anahtar dosyasını Base64 kodlaması ile kodlayın ve kodlanmış dizeyi anahtar kasanıza depolayın. Özgün özel anahtar dosyası, dosyadan **karşıya yükle** ' yi seçerseniz SFTP bağlantılı hizmetinde çalışabileceğiniz bir dosyadır.

    Dizeyi oluşturmak için kullanabileceğiniz bazı örnekler şunlardır:

    - C# kodu kullan:

        ```
        byte[] keyContentBytes = File.ReadAllBytes(Private Key Path);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - Python kodu kullan:

        ```
        import base64
        rfd = open(r'{Private Key Path}', 'rb')
        keyContent = rfd.read()
        rfd.close()
        print base64.b64encode(Key Content)
        ```

    - Üçüncü taraf bir Base64 dönüştürme aracı kullanın. [Base64 biçim aracına kodlama](https://www.base64encode.org/) önerilir.

- **Neden**: yanlış anahtar içerik biçimi seçildi.

- **Öneri:**  

    PKCS # 8 biçimlendirme SSH özel anahtarı ("-----ŞIFRELENMIŞ özel anahtara başla-----") Şu anda Data Factory ' de SFTP sunucusuna erişmek için desteklenmiyor. 

    Anahtarı geleneksel SSH anahtarı biçimine dönüştürmek için, "-----BEGIN RSA özel anahtar-----" ile başlayarak aşağıdaki komutları çalıştırın:

    ```
    openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file
    chmod 600 traditional_format_key_file
    ssh-keygen -f traditional_format_key_file -p
    ```

- **Neden**: geçersiz kimlik bilgileri veya özel anahtar içeriği.

- **Öneri**: anahtar dosyanızın veya parolanızın doğru olup olmadığını görmek Için WinSCP gibi araçlarla çift kontrol edin.

### <a name="sftp-copy-activity-failed"></a>SFTP kopyalama etkinliği başarısız oldu

- **Belirtiler**: 
  * Hata kodu: Usererrorınvalidcolumnmappingcolumnnotfound 
  * Hata iletisi: `Column 'AccMngr' specified in column mapping cannot be found in source data.`

- **Neden**: kaynak, "AccMngr" adlı bir sütun içermiyor.

- **Çözüm**: "AccMngr" sütununun mevcut olup olmadığını anlamak için, hedef veri kümesi sütununu eşleyerek veri kümesi yapılandırmanızı iki kez kontrol edin.


### <a name="error-code-sftpfailedtoconnecttosftpserver"></a>Hata kodu: SftpFailedToConnectToSftpServer

- **İleti**: `Failed to connect to SFTP server '%server;'.`

- **Neden**: hata Iletisi "yuva okuma işlemi 30.000 milisaniyelik sonra zaman aşımına uğradı" dizesini içeriyorsa, olası bir nedeni SFTP sunucusu için yanlış bir bağlı hizmet türünün kullanılmasına neden olur. Örneğin, SFTP sunucusuna bağlanmak için FTP bağlantılı hizmetini kullanıyor olabilirsiniz.

- **Öneri**: hedef sunucunun bağlantı noktasını denetleyin. Varsayılan olarak, SFTP bağlantı noktası 22 ' yi kullanır.

- **Neden**: hata Iletisi "Sunucu yanıtı SSH protokol kimliği içermez" dizesini içeriyorsa, olası bir neden, SFTP sunucusunun bağlantıyı kısıtlamasının nedeni olur. Data Factory, SFTP sunucusundan paralel olarak indirilecek birden çok bağlantı oluşturacak ve bazen SFTP sunucusu azaltmasına karşılaşacaktır. Normalde, farklı sunucular kısıtlama ile karşılaştığında farklı hatalar döndürür.

- **Öneri:**  

    SFTP veri kümesinin en fazla eş zamanlı bağlantı sayısını 1 olarak belirtin ve kopyalama etkinliğini yeniden çalıştırın. Etkinlik başarılı olursa, azaltma işleminin nedeni olduğundan emin olabilirsiniz.

    Düşük aktarım hızını yükseltmek istiyorsanız, eş zamanlı bağlantı sayısı sınırını artırmak için SFTP yöneticinizle iletişime geçin veya aşağıdakilerden birini yapabilirsiniz:

    * Şirket içinde barındırılan IR kullanıyorsanız, şirket içinde barındırılan IR makinesinin IP 'sini izin verilenler listesine ekleyin.
    * Azure IR kullanıyorsanız [Azure INTEGRATION RUNTIME IP adreslerini](./azure-integration-runtime-ip-addresses.md)ekleyin. SFTP sunucusu izin verilenler listesine bir IP aralığı eklemek istemiyorsanız, bunun yerine kendinden konak IR kullanın.

## <a name="sharepoint-online-list"></a>SharePoint Online listesi

### <a name="error-code-sharepointonlineauthfailed"></a>Hata kodu: SharePointOnlineAuthFailed

- **İleti**: `The access token generated failed, status code: %code;, error message: %message;.`

- **Neden**: hızmet sorumlusu kimliği ve anahtarı doğru ayarlanmamış olabilir.

- **Öneri**: kayıtlı uygulamanızı (HIZMET sorumlusu kimliği) ve anahtarınızı denetleyip doğru şekilde ayarlanmadığını öğrenin.


## <a name="xml-format"></a>XML biçimi

### <a name="error-code-xmlsinknotsupported"></a>Hata kodu: Xmlsinde Tsupported

- **İleti**: `Write data in XML format is not supported yet, choose a different format!`

- **Neden**: kopyalama etkinliğinizdeki havuz veri kümesi olarak bir XML veri kümesi kullanıldı.

- **Öneri**: havuz veri kümesinden farklı bir biçimde veri kümesi kullanın.


### <a name="error-code-xmlattributecolumnnameconflict"></a>Hata kodu: XmlAttributeColumnNameConflict

- **İleti**: `Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **Neden**: bir öznitelik öneki kullanıldı, bu da çakışmaya neden oldu.

- **Öneri**: "attributeprefix" özelliği için farklı bir değer ayarlayın.


### <a name="error-code-xmlvaluecolumnnameconflict"></a>Hata kodu: XmlValueColumnNameConflict

- **İleti**: `Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **Neden**: öğe değerinin sütun adı olarak alt öğe adlarından biri kullanıldı.

- **Öneri**: "valueColumn" özelliği için farklı bir değer ayarlayın.


### <a name="error-code-xmlinvalid"></a>Hata kodu: XmlInvalid

- **İleti**: `Input XML file '%file;' is invalid with parsing error '%error;'.`

- **Neden**: giriş XML dosyası düzgün biçimlendirilmemiş.

- **Öneri**: iyi biçimlendirilmiş olması için XML dosyasını düzeltin.


## <a name="general-copy-activity-error"></a>Genel kopyalama etkinliği hatası

### <a name="error-code-jrenotfound"></a>Hata kodu: JreNotFound

- **İleti**: `Java Runtime Environment cannot be found on the Self-hosted Integration Runtime machine. It is required for parsing or writing to Parquet/ORC files. Make sure Java Runtime Environment has been installed on the Self-hosted Integration Runtime machine.`

- **Neden**: Şirket IÇINDE barındırılan IR Java çalışma zamanı 'nı bulamıyor. Belirli kaynakları okumak için Java çalışma zamanı gereklidir.

- **Öneri**: Tümleştirme çalışma zamanı ortamınızı denetleyin, bkz. [şirket Içinde barındırılan Integration Runtime kullanma](./format-parquet.md#using-self-hosted-integration-runtime).


### <a name="error-code-wildcardpathsinknotsupported"></a>Hata kodu: Yavaya Cardpathsinyatsupported

- **İleti**: `Wildcard in path is not supported in sink dataset. Fix the path: '%setting;'.`

- **Neden**: havuz veri kümesi joker değerleri desteklemiyor.

- **Öneri**: havuz veri kümesini denetleyin ve bir joker değer kullanmadan yolu yeniden yazın.


### <a name="fips-issue"></a>FIPS sorunu

- **Belirtiler**: şu hata ILETISIYLE, FIPS özellikli bir şirket IÇINDE barındırılan IR makinesinde kopyalama etkinliği başarısız olur: `This implementation is not part of the Windows Platform FIPS validated cryptographic algorithms.` 

- **Neden**: Bu hata, verileri Azure blob, SFTP vb. gibi bağlayıcılarla kopyaladığınızda meydana gelebilir. Federal bilgi Işleme standartları (FIPS), kullanılmasına izin verilen belirli bir şifreleme algoritmaları kümesini tanımlar. Makinede FIPS modu etkin olduğunda, kopyalama etkinliğinin bağımlı olduğu bazı şifreleme sınıfları bazı senaryolarda engellenir.

- **Çözüm**: [Neden "FIPS modunu" önermediğimiz](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/why-we-8217-re-not-recommending-8220-fips-mode-8221-anymore/ba-p/701037)hakkında bilgi edinin ve şirket IÇINDE barındırılan IR makinenizde FIPS 'yi devre dışı bırakadığınızı değerlendirin.

    Alternatif olarak, yalnızca FIPS 'yi Azure Data Factory atlamak ve etkinliğin başarılı olmasını sağlamak istiyorsanız aşağıdakileri yapın:

    1. Şirket içinde barındırılan IR 'nin yüklendiği klasörü açın. Yol genellikle *C:\Program Files\Microsoft Integration Runtime \<IR version> \shared* şeklindedir.

    2. *diawp.exe.config* dosyasını açın ve ardından bölümünün sonunda, `<runtime>` `<enforceFIPSPolicy enabled="false"/>` burada gösterildiği gibi ekleyin:

        ![FIPS devre dışı gösteren diawp.exe.config dosyanın bir bölümünün ekran görüntüsü.](./media/connector-troubleshoot-guide/disable-fips-policy.png)

    3. Dosyayı kaydedin ve ardından Şirket içinde barındırılan IR makinesini yeniden başlatın.


## <a name="next-steps"></a>Sonraki adımlar

Daha fazla sorun giderme Yardım için şu kaynakları deneyin:

*  [Data Factory blogu](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory Özellik istekleri](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videoları](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q bir sayfa&](/answers/topics/azure-data-factory.html)
*  [Data Factory için Stack Overflow Forumu](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory hakkında Twitter bilgileri](https://twitter.com/hashtag/DataFactory)