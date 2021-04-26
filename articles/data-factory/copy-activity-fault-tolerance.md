---
title: Azure Data Factory’de kopyalama etkinliğinin hataya dayanıklılığı
description: Uyumsuz verileri atlayarak Azure Data Factory etkinliği kopyalamak için hataya dayanıklılık ekleme hakkında bilgi edinin.
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: yexu
ms.openlocfilehash: 0fe1470661c006399ea176af1112d271524b2a1f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100390972"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Azure Data Factory’de kopyalama etkinliğinin hataya dayanıklılığı
> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [Güncel sürüm](copy-activity-fault-tolerance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Verileri kaynaktan hedef depoya kopyaladığınızda, Azure Data Factory kopyalama etkinliği, veri hareketinin ortasında hata kesintilerini engellemek için belirli bir hata toleransı düzeyi sağlar. Örneğin, kaynak veritabanında milyonlarca satırı hedef depoya kopyalarsınız, ancak hedef veritabanında birincil bir anahtar oluşturulmuştur, ancak kaynak veritabanı tanımlı birincil anahtara sahip değildir. Çoğaltılan satırları kaynaktan hedefe kopyalamak istediğinizde, hedef veritabanında PK ihlali hatasına vurun. Şu anda kopyalama etkinliği, bu tür hataları idare etmek için iki yol sunar: 
- Herhangi bir hatayla karşılaşıldığında kopyalama etkinliğini iptal edebilirsiniz. 
- Uyumsuz verileri atlamak için hata toleransını etkinleştirerek geri kalanı kopyalamaya devam edebilirsiniz. Örneğin, bu durumda çoğaltılan satırı atlayın. Ayrıca, kopyalama etkinliği içinde oturum günlüğü etkinleştirerek atlanan verileri günlüğe kaydedebilirsiniz. Daha fazla ayrıntı için, [kopyalama etkinliğinde oturum günlüğü '](copy-activity-log.md) ne başvurabilirsiniz.

## <a name="copying-binary-files"></a>İkili dosyalar kopyalanıyor 

ADF, ikili dosyaları kopyalarken aşağıdaki hata toleransı senaryolarını destekler. Kopyalama etkinliğini durdurmayı veya geri kalanı aşağıdaki senaryolarda kopyalamaya devam etmeyi seçebilirsiniz:

1. ADF tarafından kopyalanacak dosyalar diğer uygulamalar tarafından aynı anda siliniyor.
2. Belirli bazı klasörler veya dosyalar ADF 'nin erişmesine izin vermez, çünkü bu dosya veya klasörlerin ACL 'Leri ADF 'de yapılandırılan bağlantı bilgilerine göre daha yüksek izin düzeyi gerektirir.
3. ADF 'de veri tutarlılığı doğrulama ayarını etkinleştirirseniz, bir veya daha fazla dosyanın kaynak ve hedef depo arasında tutarlı olması doğrulanmaz.

### <a name="configuration"></a>Yapılandırma 
Depolama depoları arasında ikili dosyaları kopyaladığınızda, hata toleransını şu şekilde etkinleştirebilirsiniz: 

```json
"typeProperties": { 
    "source": { 
        "type": "BinarySource", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreReadSettings", 
            "recursive": true 
            } 
    }, 
    "sink": { 
        "type": "BinarySink", 
        "storeSettings": { 
            "type": "AzureDataLakeStoreWriteSettings" 
        } 
    }, 
    "skipErrorFile": { 
        "fileMissing": true, 
        "fileForbidden": true, 
        "dataInconsistency": true,
        "invalidFileName": true        
    }, 
    "validateDataConsistency": true, 
    "logSettings": {
        "enableCopyActivityLog": true,
        "copyActivityLogSettings": {            
            "logLevel": "Warning",
            "enableReliableLogging": false
        },
        "logLocationSettings": {
            "linkedServiceName": {
               "referenceName": "ADLSGen2",
               "type": "LinkedServiceReference"
            },
            "path": "sessionlog/"
        }
    }
} 
```
Özellik | Açıklama | İzin verilen değerler | Gerekli
-------- | ----------- | -------------- | -------- 
skipErrorFile | Veri taşıma sırasında atlamak istediğiniz başarısızlık türlerini belirtmek için bir özellik grubu. | | No
fileMissing | SkipErrorFile özellik paketi içindeki anahtar-değer çiftlerinden biri, ADF 'nin bir arada kopyalanırken diğer uygulamalar tarafından silinmekte olan dosyaları atlamak istediğinizi öğrenmek için. <br/> -True: diğer uygulamalar tarafından silinmekte olan dosyaları atlayarak geri kalanını kopyalamak istersiniz. <br/> -False: veri hareketinin ortasında Kaynak depodan silinen dosyalar silindiğinde kopyalama etkinliğini iptal etmek istiyorsunuz. <br/>Bu özelliğin varsayılan olarak true olarak ayarlandığını unutmayın. | True (varsayılan) <br/>Yanlış | No
Dosya yasak | Belirli dosyaları atlamak istiyorsanız, bu dosya veya klasörlerin ACL 'Leri ADF 'de yapılandırılan bağlantıdan daha yüksek izin düzeyi gerektirdiğinde, bu dosyaların atlanmasını istediğinizi öğrenmek için skipErrorFile özellik paketi içindeki anahtar-değer çiftlerinden biridir. <br/> -True: dosyaları atlayarak geri kalanı kopyalamak istiyorsunuz. <br/> -False: klasörler veya dosyalar üzerinde izin sorununu elde etmek için kopyalama etkinliğini iptal etmek istiyorsunuz. | Doğru <br/>False (varsayılan) | No
Veri tutarsızlığı | Kaynak ve hedef depo arasında tutarsız verileri atlamak istediğinizi öğrenmek için skipErrorFile özellik paketi içindeki anahtar-değer çiftlerinden biri. <br/> -True: tutarsız verileri atlayarak geri kalanı kopyalamak istiyorsunuz. <br/> -False: tutarsız veriler bulunduğunda kopyalama etkinliğini iptal etmek istiyorsunuz. <br/>Bu özelliğin yalnızca Validatedatatutarlılığı doğru olarak ayarladığınızda geçerli olduğunu unutmayın. | Doğru <br/>False (varsayılan) | No
ınvalidfilename | Dosya adları hedef depo için geçersiz olduğunda, belirli dosyaları atlamak istediğinizi öğrenmek için, skipErrorFile özellik paketi içindeki anahtar-değer çiftlerinden biri. <br/> -True: geçersiz dosya adlarına sahip dosyaları atlayarak geri kalanı kopyalamak istiyorsunuz. <br/> -False: herhangi bir dosya geçersiz dosya adlarına sahip olduktan sonra kopyalama etkinliğini iptal etmek istiyorsunuz. <br/>Bu özellik, herhangi bir depolama ADLS 2. deposundan ikili dosyaları kopyalarken veya ikili dosyaları AWS S3 öğesinden yalnızca herhangi bir depolama deposuna kopyalarken işe yarar. | Doğru <br/>False (varsayılan) | No
Günlüğe kaydetme ayarları  | Atlanan nesne adlarını günlüğe kaydetmek istediğinizde belirtilenebilir bir özellik grubu. | &nbsp; | No
linkedServiceName | [Azure Blob depolama alanına](connector-azure-blob-storage.md#linked-service-properties) bağlı hizmet veya oturum günlüğü dosyalarını depolamak için [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md#linked-service-properties) . | `AzureBlobStorage` `AzureBlobFS` Günlük dosyasını depolamak için kullandığınız örneğe karşılık gelen veya tür bağlı bir hizmetin adları. | No
path | Günlük dosyalarının yolu. | Günlük dosyalarını depolamak için kullandığınız yolu belirtin. Bir yol sağlamazsanız, hizmet sizin için bir kapsayıcı oluşturur. | No

> [!NOTE]
> Bu şekilde, ikili dosyalar kopyalanırken kopyalama etkinliğinde hata toleransını etkinleştirme önkoşulları bulunur.
> Kaynak deposundan silindiklerinde belirli dosyaları atlamak için:
> - Kaynak veri kümesi ve havuz veri kümesi ikili biçimde olmalıdır ve sıkıştırma türü belirtilemez. 
> - Desteklenen veri deposu türleri Azure Blob depolama, Azure Data Lake Storage 1., Azure Data Lake Storage 2., Azure dosya depolama, dosya sistemi, FTP, SFTP, Amazon S3, Google Cloud Storage ve bir.
> - Kaynak veri kümesinde, bir klasör, joker karakter veya dosya listesi olabilen birden çok dosya belirttiğinizde, kopyalama etkinliği belirli hata dosyalarını atlayabilir. Kaynak veri kümesinde hedefe kopyalanacak tek bir dosya belirtilmişse, herhangi bir hata oluştuysa kopyalama etkinliği başarısız olur.
>
> Kaynak depolamadan erişimleri yasaklanmış olduğunda belirli dosyaları atlamak için:
> - Kaynak veri kümesi ve havuz veri kümesi ikili biçimde olmalıdır ve sıkıştırma türü belirtilemez. 
> - Desteklenen veri deposu türleri Azure Blob depolama, Azure Data Lake Storage 1., Azure Data Lake Storage 2., Azure dosya depolama, SFTP, Amazon S3 ve bir.
> - Kaynak veri kümesinde, bir klasör, joker karakter veya dosya listesi olabilen birden çok dosya belirttiğinizde, kopyalama etkinliği belirli hata dosyalarını atlayabilir. Kaynak veri kümesinde hedefe kopyalanacak tek bir dosya belirtilmişse, herhangi bir hata oluştuysa kopyalama etkinliği başarısız olur.
>
> Kaynak ve hedef depo arasında tutarsız oldukları doğrulandıktan sonra belirli dosyaları atlamak için:
> - Veri [tutarlılığı belgesi buradan](./copy-activity-data-consistency.md)daha ayrıntılı bilgi edinebilirsiniz.

### <a name="monitoring"></a>İzleme 

#### <a name="output-from-copy-activity"></a>Kopyalama etkinliğinden çıkış
Okuma, yazma ve her bir kopyalama etkinliği çalışmasının çıktısı aracılığıyla Atlanan dosya sayısını alabilirsiniz. 

```json
"output": {
            "dataRead": 695,
            "dataWritten": 186,
            "filesRead": 3,  
            "filesWritten": 1, 
            "filesSkipped": 2, 
            "throughput": 297,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "dataConsistencyVerification": 
           { 
                "VerificationResult": "Verified", 
                "InconsistentData": "Skipped" 
           } 
        }

```

#### <a name="session-log-from-copy-activity"></a>Kopyalama etkinliğinden oturum günlüğü

' İ Atlanan dosya adlarını günlüğe kaydetmek üzere yapılandırırsanız, günlük dosyasını şu yoldan bulabilirsiniz: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` . 

Günlük dosyaları CSV dosyaları olmalıdır. Günlük dosyasının şeması aşağıdaki gibidir:

Sütun | Açıklama 
-------- | -----------  
Timestamp | ADF dosyayı atlayan zaman damgası.
Level | Bu öğenin günlük düzeyi. Dosya atlama gösteren öğe için ' uyarı ' düzeyinde olacaktır.
OperationName | ADF kopyalama etkinliği işlem davranışı her dosyada. Atlanacak dosyayı belirtmek için ' FileSkip ' olur.
Operationıtem | Atlanacak dosya adları.
İleti | Dosyanın neden atlandığını göstermek için daha fazla bilgi.

Bir günlük dosyası örneği aşağıdaki gibidir: 
```
Timestamp,Level,OperationName,OperationItem,Message 
2020-03-24 05:35:41.0209942,Warning,FileSkip,"bigfile.csv","File is skipped after read 322961408 bytes: ErrorCode=UserErrorSourceBlobNotExist,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=The required Blob is missing. ContainerName: https://transferserviceonebox.blob.core.windows.net/skipfaultyfile, path: bigfile.csv.,Source=Microsoft.DataTransfer.ClientLibrary,'." 
2020-03-24 05:38:41.2595989,Warning,FileSkip,"3_nopermission.txt","File is skipped after read 0 bytes: ErrorCode=AdlsGen2OperationFailed,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=ADLS Gen2 operation failed for: Operation returned an invalid status code 'Forbidden'. Account: 'adlsgen2perfsource'. FileSystem: 'skipfaultyfilesforbidden'. Path: '3_nopermission.txt'. ErrorCode: 'AuthorizationPermissionMismatch'. Message: 'This request is not authorized to perform this operation using this permission.'. RequestId: '35089f5d-101f-008c-489e-01cce4000000'..,Source=Microsoft.DataTransfer.ClientLibrary,''Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Operation returned an invalid status code 'Forbidden',Source=,''Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message='Type=Microsoft.Azure.Storage.Data.Models.ErrorSchemaException,Message=Operation returned an invalid status code 'Forbidden',Source=Microsoft.DataTransfer.ClientLibrary,',Source=Microsoft.DataTransfer.ClientLibrary,'." 
```
Yukarıdaki günlükte, ADF kopyalanırken başka bir uygulama bu dosyayı sildiği için bigfile.csv atlandığını görebilirsiniz. Ve 3_nopermission.txt, izin sorunu nedeniyle ADF 'ye erişmesine izin verilmediği için atlandı.


## <a name="copying-tabular-data"></a>Tablo verilerini kopyalama 

### <a name="supported-scenarios"></a>Desteklenen senaryolar
Kopyalama etkinliği, uyumsuz tablolu verileri saptamak, atlamak ve günlüğe kaydetmek için üç senaryoyu destekler:

- **Kaynak veri türü ile havuz yerel türü arasında uyumsuzluk**. 

    Örneğin: BLOB depolama alanındaki bir CSV dosyasından, üç INT tür sütunu içeren bir şema tanımına sahip bir SQL veritabanına veri kopyalama. 123.456.789 gibi sayısal veriler içeren CSV dosyası satırları havuz deposuna başarıyla kopyalanır. Ancak, 123.456, ABC gibi sayısal olmayan değerler içeren satırlar uyumsuz olarak algılanır ve atlanır.

- **Kaynak ve havuz arasındaki sütun sayısında uyuşmazlık var**.

    Örneğin: BLOB depolama alanındaki bir CSV dosyasından, altı sütun içeren bir şema tanımına sahip bir SQL veritabanına veri kopyalama. Altı sütun içeren CSV dosyası satırları havuz deposuna başarıyla kopyalanır. Altıdan fazla sütun içeren CSV dosyası satırları uyumsuz olarak algılanır ve atlanır.

- **SQL Server/Azure SQL veritabanı/Azure Cosmos DB yazılırken birincil anahtar ihlali**.

    Örneğin: bir SQL Server 'dan SQL veritabanı 'na veri kopyalama. Birincil anahtar, havuz SQL veritabanında tanımlanmıştır, ancak kaynak SQL Server 'da böyle bir birincil anahtar tanımlanmamıştır. Kaynakta bulunan yinelenen satırlar havuza kopyalanamıyor. Kopyalama etkinliği yalnızca kaynak verilerin ilk satırını havuza kopyalar. Yinelenen birincil anahtar değerini içeren sonraki kaynak satırlar uyumsuz olarak algılanır ve atlanır.

>[!NOTE]
>- PolyBase kullanarak Azure SYNAPSE Analytics 'e veri yüklemek için, kopyalama etkinliğinde "[Polybasesettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" aracılığıyla reddetme Ilkelerini belirterek PolyBase 'in yerel hata toleransı ayarlarını yapılandırın. Aynı zamanda, PolyBase uyumsuz satırları blob veya ADLS 'e aşağıda gösterildiği gibi normal şekilde yeniden yönlendirmeyi etkinleştirebilirsiniz.
>- Copy etkinliği [Amazon Redshift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift)komutunu çağırmak üzere yapılandırıldığında bu özellik uygulanmaz.
>- Kopyalama etkinliği [BIR SQL havuzundan saklı yordam](./connector-azure-sql-database.md#invoke-a-stored-procedure-from-a-sql-sink)çağırmak üzere yapılandırıldığında bu özellik uygulanmaz.

### <a name="configuration"></a>Yapılandırma
Aşağıdaki örnek, kopyalama etkinliğinde uyumsuz satırları atlamayı yapılandırmak için bir JSON tanımı sağlar:

```json
"typeProperties": { 
    "source": { 
        "type": "AzureSqlSource" 
    }, 
    "sink": { 
        "type": "AzureSqlSink" 
    }, 
    "enableSkipIncompatibleRow": true, 
    "logSettings": {
        "enableCopyActivityLog": true,
        "copyActivityLogSettings": {            
            "logLevel": "Warning",
            "enableReliableLogging": false
        },
        "logLocationSettings": {
            "linkedServiceName": {
               "referenceName": "ADLSGen2",
               "type": "LinkedServiceReference"
            },
            "path": "sessionlog/"
        }
    } 
}, 
```

Özellik | Açıklama | İzin verilen değerler | Gerekli
-------- | ----------- | -------------- | -------- 
Enableskipıncompatiblerow | Kopya sırasında uyumsuz satırların atlanıp atlanmayacağını belirtir. | Doğru<br/>False (varsayılan) | No
Günlüğe kaydetme ayarları | Uyumsuz satırları günlüğe kaydetmek istediğinizde belirtilenebilir bir özellik grubu. | &nbsp; | No
linkedServiceName | Atlanan satırları içeren günlüğü depolamak için [Azure Blob depolama](connector-azure-blob-storage.md#linked-service-properties) veya [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md#linked-service-properties) bağlı hizmeti. | `AzureBlobStorage` `AzureBlobFS` Günlük dosyasını depolamak için kullandığınız örneğe karşılık gelen veya tür bağlı bir hizmetin adları. | No
path | Atlanan satırları içeren günlük dosyalarının yolu. | Uyumsuz verileri günlüğe kaydetmek için kullanmak istediğiniz yolu belirtin. Bir yol sağlamazsanız, hizmet sizin için bir kapsayıcı oluşturur. | No

### <a name="monitor-skipped-rows"></a>Atlanan satırları izle
Kopyalama etkinliği çalıştırıldıktan sonra kopyalama etkinliğinin çıkışında atlanan satır sayısını görebilirsiniz:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "logFilePath": "myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```

' I uyumsuz satırları günlüğe kaydetmek üzere yapılandırırsanız, günlük dosyasını şu yoldan bulabilirsiniz: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` . 

Günlük dosyaları CSV dosyaları olacaktır. Günlük dosyasının şeması aşağıdaki gibidir:

Sütun | Açıklama 
-------- | -----------  
Timestamp | ADF uyumsuz satırları atlamazsa zaman damgası
Level | Bu öğenin günlük düzeyi. Bu öğe atlanan satırları gösteriyorsa, bu ' uyarı ' düzeyinde olur
OperationName | ADF kopyalama etkinliği işlem davranışı her satırda. Bu, uyumsuz bir satırın atlandığını belirtmek için ' TabularRowSkip ' olur
Operationıtem | Kaynak veri deposundan atlanan satırlar.
İleti | Bu belirli satırın uyumsuzluğunun neden olduğunu gösteren daha fazla bilgi.


Günlük dosyası içeriğine bir örnek aşağıdaki gibidir:

```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:32.2586581, Warning, TabularRowSkip, """data1"", ""data2"", ""data3""," "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'." 
2020-02-26 06:22:33.2586351, Warning, TabularRowSkip, """data4"", ""data5"", ""data6"",", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)." 
```

Yukarıdaki örnek günlük dosyasından bir "Veri1, Veri2, DATA3" satırını, kaynaktan hedef depoya tür dönüştürme sorunu nedeniyle atladığını görebilirsiniz. Kaynaktan hedef depoya olan PK ihlali sorunu nedeniyle başka bir "DATA4, DATA5, data6" satırı atlandı. 


## <a name="copying-tabular-data-legacy"></a>Tablo verilerini kopyalama (eski):

Aşağıda yalnızca tablo verilerini kopyalamak için hata toleransını etkinleştirmenin eski yolu verilmiştir. Yeni işlem hattı veya etkinlik oluşturuyorsanız bunun yerine [buradan](#copying-tabular-data) başlamanız önerilir.

### <a name="configuration"></a>Yapılandırma
Aşağıdaki örnek, kopyalama etkinliğinde uyumsuz satırları atlamayı yapılandırmak için bir JSON tanımı sağlar:

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

Özellik | Açıklama | İzin verilen değerler | Gerekli
-------- | ----------- | -------------- | -------- 
Enableskipıncompatiblerow | Kopya sırasında uyumsuz satırların atlanıp atlanmayacağını belirtir. | Doğru<br/>False (varsayılan) | No
Redirectıncompatiblerowsettings | Uyumsuz satırları günlüğe kaydetmek istediğinizde belirtilenebilir bir özellik grubu. | &nbsp; | No
linkedServiceName | Atlanan satırları içeren günlüğü depolamak için [Azure depolama](connector-azure-blob-storage.md#linked-service-properties) 'nın bağlı hizmeti veya [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) . | `AzureStorage` `AzureDataLakeStore` Günlük dosyasını depolamak için kullanmak istediğiniz örneğe karşılık gelen veya tür bağlı bir hizmetin adı. | No
path | Atlanan satırları içeren günlük dosyasının yolu. | Uyumsuz verileri günlüğe kaydetmek için kullanmak istediğiniz yolu belirtin. Bir yol sağlamazsanız, hizmet sizin için bir kapsayıcı oluşturur. | No

### <a name="monitor-skipped-rows"></a>Atlanan satırları izle
Kopyalama etkinliği çalıştırıldıktan sonra kopyalama etkinliğinin çıkışında atlanan satır sayısını görebilirsiniz:

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
' I uyumsuz satırları günlüğe kaydetmek üzere yapılandırırsanız, günlük dosyasını şu yolda bulabilirsiniz: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` . 

Günlük dosyaları yalnızca CSV dosyaları olabilir. Atlanan özgün veriler, gerekirse sütun sınırlayıcısı olarak virgülle kaydedilir. Günlük dosyasındaki özgün kaynak verilerine ek olarak "ErrorCode" ve "ErrorMessage" olmak üzere iki sütun daha ekler. Bu, uyumsuzluğun kök nedenini görebileceğiniz yerdir. ErrorCode ve ErrorMessage, çift tırnak ile alıntılanacaktır. 

Günlük dosyası içeriğine bir örnek aşağıdaki gibidir:

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>Sonraki adımlar
Diğer kopyalama etkinliği makalelerine bakın:

- [Kopyalama etkinliğine genel bakış](copy-activity-overview.md)
- [Kopyalama etkinliği performansı](copy-activity-performance.md)