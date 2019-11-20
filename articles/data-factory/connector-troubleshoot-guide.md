---
title: Azure Data Factory bağlayıcılarıyla ilgili sorunları giderme
description: Azure Data Factory 'deki bağlayıcı sorunlarını giderme hakkında bilgi edinin.
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 08/26/2019
ms.author: jingwang
ms.reviewer: craigg
ms.openlocfilehash: 8cabc1031f9d0be772ba087109ae1dfc881ce163
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680077"
---
# <a name="troubleshoot-azure-data-factory-connectors"></a>Azure Data Factory bağlayıcılarıyla ilgili sorunları giderme

Bu makalede Azure Data Factory içindeki bağlayıcılar için genel sorun giderme yöntemleri incelenmektedir.

## <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

### <a name="error-message-the-remote-server-returned-an-error-403-forbidden"></a>Hata iletisi: uzak sunucu bir hata döndürdü: (403) yasak

- **Belirtiler**: kopyalama etkinliği aşağıdaki hatayla başarısız oldu: 

    ```
    Message: The remote server returned an error: (403) Forbidden.. 
    Response details: {"RemoteException":{"exception":"AccessControlException""message":"CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)....
    ```

- **Neden**: olası bir neden, kullandığınız hizmet sorumlusu veya yönetilen kimliğin belirli klasöre/dosyaya erişim izni olmaması olabilir.

- **Çözüm**: kopyalamanız gereken tüm klasörler ve alt klasörlerde ilgili izinleri verin. [Bu belgeye](connector-azure-data-lake-store.md#linked-service-properties)başvurun.

### <a name="error-message-failed-to-get-access-token-by-using-service-principal-adal-error-service_unavailable"></a>Hata iletisi: hizmet sorumlusu kullanılarak erişim belirteci alınamadı. ADAL hatası: service_unavailable

- **Belirtiler**: kopyalama etkinliği aşağıdaki hatayla başarısız oldu:

    ```
    Failed to get access token by using service principal. 
    ADAL Error: service_unavailable, The remote server returned an error: (503) Server Unavailable.
    ```

- **Neden**: Azure Active Directory sahip olduğu hizmet belirteci sunucusu (STS) kullanılamıyor, yani istekleri işlemek için çok meşgul OLMADıĞıNDA, HTTP hatası 503 döndürür. 

- **Çözüm**: birkaç dakika sonra kopyalama etkinliğini yeniden çalıştırın.

## <a name="azure-sql-data-warehouse"></a>Azure SQL Veri Ambarı

### <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Hata iletisi: bir karakter dizesinden uniqueidentifier öğesine dönüştürülürken dönüştürme başarısız oldu

- **Belirtiler**: tablo veri kaynağından (örneğin, SQL Server) verileri, hazırlanan Copy ve PolyBase kullanarak Azure SQL veri ambarı 'na kopyaladığınızda, aşağıdaki hataya ulaşırsınız:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Conversion failed when converting from a character string to uniqueidentifier...
    ```

- **Neden**: Azure SQL veri ambarı PolyBase boş dizeyi GUID 'ye dönüştüremiyor.

- **Çözüm**: kopyalama etkinliği havuzunda, PolyBase ayarları altında "**tür Varsayılanı kullan**" seçeneğini false olarak ayarlayın.

### <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Hata iletisi: beklenen veri türü: DECIMAL (x, x), sorunlu değer

- **Belirtiler**: bir tablo veri kaynağından (örneğin, SQL Server) verileri, hazırlanan kopya ve PolyBase kullanarak SQL DW 'ye kopyaladığınızda aşağıdaki hataya ulaşırsınız:

    ```
    ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
    Message=Error happened when loading data into SQL Data Warehouse.,
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException,
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt) 
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..
    ```

- **Neden**: Azure SQL veri ambarı PolyBase, decimal sütununa boş dize (null değer) ekleyemiyor.

- **Çözüm**: kopyalama etkinliği havuzunda, PolyBase ayarları altında "**tür Varsayılanı kullan**" seçeneğini false olarak ayarlayın.

### <a name="error-message-java-exception-messagehdfsbridgecreaterecordreader"></a>Hata iletisi: Java özel durum iletisi: HdfsBridge:: CreateRecordReader

- **Belirtiler**: PolyBase 'ı kullanarak Azure SQL veri ambarı 'na veri kopyalar ve şu hatayı alırsınız:

    ```
    Message=110802;An internal DMS error occurred that caused this operation to fail. 
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader. 
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....
    ```

- **Neden**: olası neden şemanın (Toplam sütun genişliği) çok büyük (1 MB 'den büyük) olması olabilir. Tüm sütunların boyutunu ekleyerek hedef SQL DW tablosunun şemasını denetleyin:

    - Int-> 4 bayt
    - Büyük tamsayı-> 8 bayt
    - Varchar (n), char (n), binary (n), varbinary (n)-> n bayt
    - Nvarchar (n), nchar (n)-> n * 2 bayt
    - Tarih-> 6 bayt
    - DateTime/(2), smalldatetime-> 16 bayt
    - DateTimeOffset-> 20 bayt
    - Decimal-> 19 bayt
    - Kayan > 8 bayt
    - Para-> 8 bayt
    - Küçük para-> 4 bayt
    - Gerçek > 4 bayt
    - Küçük tamsayı-> 2 bayt
    - Zaman-> 12 bayt
    - Tinyint-> 1 bayt

- **Çözüm**: sütun GENIŞLIĞINI 1 MB 'tan küçük olacak şekilde azalt

- Veya PolyBase 'i devre dışı bırakarak toplu ekleme yaklaşımını kullanın

### <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Hata iletisi: HTTP koşullu üst bilgi (ler) i kullanılarak belirtilen koşul karşılanmadı

- **Belirtiler**: SQL sorgusunu kullanarak Azure SQL veri ambarı 'ndan veri çekin ve şu hatayı alırsınız:

    ```
    ...StorageException: The condition specified using HTTP conditional header(s) is not met...
    ```

- **Neden**: Azure SQL veri ambarı, Azure depolama 'daki dış tabloyu sorgulama sorunu ile karşılaştı.

- **Çözüm**: aynı sorguyu SSMS 'de çalıştırın ve aynı sonucu görüp gör, kontrol edin. Yanıt Evet ise, Azure SQL veri ambarı 'na bir destek bileti açın ve daha fazla sorun gidermek için SQL DW sunucunuzu ve veritabanı adınızı sağlayın.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

### <a name="error-message-request-size-is-too-large"></a>Hata iletisi: Istek boyutu çok büyük

- **Belirtiler**: verileri varsayılan yazma toplu işi boyutuyla Azure Cosmos DB kopyalar ve *"**istek boyutu çok büyük**"* hatası ile karşılaşırsınız.

- **Neden**: Cosmos DB tek bir isteğin boyutunu 2 MB olarak sınırlar. Formül, Istek boyutu = tek belge boyutu * yazma toplu Iş boyutu ' dür. Belge boyutunuz büyükse, varsayılan davranış çok büyük istek boyutuna neden olur. Yazma toplu iş boyutunu ayarlayabilirsiniz.

- **Çözüm**: kopyalama etkinliği havuzunda ' toplu Iş boyutunu yaz ' değerini küçültün (varsayılan değer 10000 ' dir).

### <a name="error-message-unique-index-constraint-violation"></a>Hata iletisi: benzersiz dizin kısıtlaması ihlali

- **Belirtiler**: Cosmos DB veri kopyalarken şu hatayla karşılaşırsınız:

    ```
    Message=Partition range id 0 | Failed to import mini-batch. 
    Exception was Message: {"Errors":["Encountered exception while executing function. Exception &#61; Error: {\"Errors\":[\"Unique index constraint violation.\"]}... 
    ```

- **Neden**: olası iki neden vardır:

    - **Insert** as Write davranışını kullanırsanız, bu hata kaynak VERILERDE aynı kimliğe sahip satırlar/nesneler olduğu anlamına gelir.

    - Yazma davranışı olarak **upsert** kullanırsanız ve kapsayıcıya başka bir benzersiz anahtar ayarlarsanız, bu hata kaynak verilerde farklı kimliklere sahip satırlar/nesneler ve tanımlanan benzersiz anahtar için aynı değere sahip olduğunuz anlamına gelir.

- **Çözüm**: 

    - Cause1 için, **büyük sert** yazma davranışı olarak ayarlayın.
    - Neden 2 için, her belgenin tanımlanmış benzersiz anahtar için farklı bir değere sahip olduğundan emin olun.

### <a name="error-message-request-rate-is-large"></a>Hata iletisi: Istek hızı büyük

- **Belirtiler**: Cosmos DB veri kopyalarken şu hatayla karşılaşırsınız:

    ```
    Type=Microsoft.Azure.Documents.DocumentClientException,
    Message=Message: {"Errors":["Request rate is large"]}
    ```

- **Neden**: kullanılan istek birimleri Cosmos DB yapılandırılan ru 'dan daha büyük. Cosmos DB RU 'yi [buradan](../cosmos-db/request-units.md#request-unit-considerations)nasıl hesaplayacağını öğrenin.

- **Çözüm**: iki çözüm vardır:

    1. Cosmos DB, kopyalama etkinliği performansını iyileştirecek, ancak Cosmos DB daha fazla ücret ödemesine neden olacak şekilde, 1. kapsayıcıyı daha büyük bir değere **yükseltin** . 

    2. **Writebatchsize** değerini daha küçük bir değere (1000 gibi) düşürün ve **parallelcopy** değerlerini 1 gibi daha küçük bir değere ayarlayın. Bu, kopya çalıştırma performansının geçerli olmasını sağlar ancak Cosmos DB daha fazla ücret vermez.

### <a name="column-missing-in-column-mapping"></a>Sütun eşlemesinde sütun eksik

- **Belirtiler**: sütun eşleme için Cosmos DB şemayı içeri aktardığınızda bazı sütunlar eksiktir. 

- **Neden**: ADF ilk 10 Cosmos DB belgeden şemayı anlar. Bazı sütunlarda/özelliklerde bu belgelerde değer yoksa, ADF tarafından algılanmaz ve bu nedenle gösterilmez.

- **Çözüm**: sütunu boş değere sahip sonuç kümesinde göstermek üzere zorlamak için aşağıdaki gibi bir sorgu ayarlayabilirsiniz: (ilk 10 belgede "imkansız" sütunu eksik). Alternatif olarak, eşleme için sütunu el ile ekleyebilirsiniz.

    ```sql
    select c.company, c.category, c.comments, (c.impossible??'') as impossible from c
    ```

### <a name="error-message-the-guidrepresentation-for-the-reader-is-csharplegacy"></a>Hata iletisi: okuyucu için Guidtemsili CSharpLegacy

- **Belirtiler**: UUID alanı Ile Cosmos DB MongoAPI/MongoDB 'den veri kopyalarken şu hatayla karşılaşırsınız:

    ```
    Failed to read data via MongoDB client.,
    Source=Microsoft.DataTransfer.Runtime.MongoDbV2Connector,Type=System.FormatException,
    Message=The GuidRepresentation for the reader is CSharpLegacy which requires the binary sub type to be UuidLegacy not UuidStandard.,Source=MongoDB.Bson,’“,
    ```

- **Neden**: bSon-UuidStardard ve UUIDLEGACY 'de UUID 'yi göstermenin iki yolu vardır. Varsayılan olarak, UuidLegacy veri okumak için kullanılır. MongoDB 'deki UUID verileriniz UuidStandard ise hatayla karşılaşacaktır.

- **Çözüm**: MongoDB bağlantı dizesinde "**uuidRepresentation = Standard**" seçeneğini ekleyin. Daha fazla bilgi için bkz. [MongoDB bağlantı dizesi](connector-mongodb.md#linked-service-properties).

## <a name="sftp"></a>SFTP

### <a name="error-message-invalid-sftp-credential-provided-for-sshpublickey-authentication-type"></a>Hata iletisi: ' SshPublicKey ' kimlik doğrulama türü için geçersiz SFTP kimlik bilgisi belirtildi

- **Belirtiler**: `SshPublicKey` kimlik doğrulaması kullanıyorsunuz ve şu hatayı alırsınız:

    ```
    Invalid Sftp credential provided for 'SshPublicKey' authentication type
    ```

- **Neden**: 3 olası neden vardır:

    1. SFTP bağlantılı hizmetini yazmak için ADF yazma Kullanıcı arabirimini kullanıyorsanız, bu hata, kullanmak üzere seçtiğiniz özel anahtarın yanlış biçimde olduğunu gösterir. Bir PKCS # 8 SSH özel anahtarı biçimi kullanabilirsiniz, ancak ADF 'nin yalnızca geleneksel SSH anahtar biçimini desteklediğini unutmayın. Daha belirgin olarak, PKCS # 8 biçimi ve geleneksel anahtar biçimi arasındaki fark PKCS # 8 anahtar içerikleri, " *-----ŞIFRELENMIŞ özel anahtarla-----* " ile başlar, ancak geleneksel anahtar biçimi " *-----BEGIN RSA özel anahtar-----* " ile başlar.
    2. Özel anahtar içeriğini depolamak için Azure Key Vault kullanırsanız veya SFTP bağlantılı hizmetini yazmak için programmatical yöntemini kullanırsanız, bu hata özel anahtar içeriğinin yanlış olduğu anlamına gelir, büyük olasılıkla Base64 kodlamalı değildir.
    3. Geçersiz kimlik bilgisi veya özel anahtar içeriği.

- **Çözüm**: 

    - Nedeni #1 için, anahtarı geleneksel anahtar biçimine dönüştürmek için aşağıdaki komutları çalıştırın, sonra bunu ADF yazma Kullanıcı arabiriminde kullanın.

        ```
        # Decrypt the pkcs8 key and convert the format to traditional key format
        openssl pkcs8 -in pkcs8_format_key_file -out traditional_format_key_file

        chmod 600 traditional_format_key_file

        # Re-encrypte the key file using passphrase
        ssh-keygen -f traditional_format_key_file -p
        ```

    - Nedeni #2, bu tür bir dize oluşturmak Için, müşteri aşağıdaki 2 farklı şekilde kullanılabilir:
    - Üçüncü taraf Base64 dönüştürme aracını kullanma: tüm özel anahtar içeriğini [Base64 kodlama ve kod çözme](https://www.base64encode.org/)gibi araçlara yapıştırın, Base64 biçimli bir dizeye kodlayın, ardından bu dizeyi anahtar kasasına yapıştırın veya SFTP bağlantılı hizmetini programlı bir şekilde yazmak için bu değeri kullanın.
    - Kod C# kullanma:

        ```c#
        byte[] keyContentBytes = File.ReadAllBytes(privateKeyPath);
        string keyContent = Convert.ToBase64String(keyContentBytes, Base64FormattingOptions.None);
        ```

    - Nedeni #3 için, anahtar dosyasının veya parolanın, SFTP sunucusuna düzgün şekilde erişmek üzere kullanıp kullanamayacağınızı doğrulamak üzere diğer araçları kullanarak doğru olup olmadığını iki kez denetleyin.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla sorun giderme Yardım için şu kaynakları deneyin:

*  [Data Factory blogu](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory Özellik istekleri](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videoları](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN forumu](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory için Stack Overflow Forumu](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory hakkında Twitter bilgileri](https://twitter.com/hashtag/DataFactory)



