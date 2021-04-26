---
title: Kopyalama etkinliğinde oturum günlüğü
description: Azure Data Factory 'de oturum günlüğü kopyalama etkinliğinde nasıl etkinleştirileceği hakkında bilgi edinin.
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: yexu
ms.openlocfilehash: 7cb00d62556babbd8e43e2fac2faa815a63943ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100385277"
---
#  <a name="session-log-in-copy-activity"></a>Kopyalama etkinliğinde oturum günlüğü

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Kopyalanan dosya adlarınızı kopyalama etkinliğinde günlüğe kaydederek, verilerin kaynaktan yalnızca kaynak depoya başarıyla kopyalanmadığından ve ayrıca, kopyalama etkinliği oturum günlüklerinde kopyalanan dosyaları inceleyerek kaynak ve hedef depo arasında tutarlı olmasını sağlamanıza yardımcı olan kopyalama etkinliği ' nde kaydedebilirsiniz.  

Kopyalama etkinliğinde hatalı verileri atlamak için hata toleransı ayarını etkinleştirdiğinizde atlanan dosyalar ve atlanan satırlar da günlüğe kaydedilebilir.  [Kopyalama etkinliğinde hata toleransı](copy-activity-fault-tolerance.md)hakkında daha fazla bilgi edinebilirsiniz. 

## <a name="configuration"></a>Yapılandırma
Aşağıdaki örnek, kopyalama etkinliğinde oturum günlüğünü etkinleştirmek için bir JSON tanımı sağlar: 

```json
"typeProperties": {
    "source": {
        "type": "BinarySource",
        "storeSettings": {
            "type": "AzureDataLakeStoreReadSettings",
            "recursive": true
        },
        "formatSettings": {
            "type": "BinaryReadSettings"
        }
    },
    "sink": {
        "type": "BinarySink",
        "storeSettings": {
            "type": "AzureBlobFSWriteSettings"
        }
    },                    
    "skipErrorFile": {
        "fileForbidden": true,
        "dataInconsistency": true
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
enableCopyActivityLog | Doğru olarak ayarlandığında, kopyalanmış dosyaları günlüğe kaydetme, Atlanan dosyaları veya Atlanan satırları günlüğe kaydetme fırsatına sahip olursunuz.  | Doğru<br/>False (varsayılan) | No
logLevel | "Bilgi" kopyalanan tüm dosyaları, Atlanan dosyaları ve atlanan satırları günlüğe kaydeder. "Uyarı" Atlanan dosyaları günlüğe kaydeder ve yalnızca satırları atladı.  | Bilgi<br/>Uyarı (varsayılan) | No
enableReliableLogging | Doğru olduğunda, her dosya hedefe kopyalandıktan sonra, etkinlik, güvenilir modda kopyalanması durumunda günlükleri hemen boşaltır.  Kopyalama etkinliğinde güvenilir günlük modu etkin olan çok büyük miktarlarda dosya kopyalarken, her dosya kopyalama işlemi için çift yazma işlemleri gerektiğinden, kopyalama üretilen işinin etkilenmesi beklenir. Bir istek hedef depoya ve başka bir istek ise günlük depolama deposuna ait.  En iyi çaba modundaki kopyalama etkinliği, kopyalama üretilen işinin çok daha az etkilenmesi durumunda, kayıtları bir süre içinde toplu iş ile temizler. Son günlük olaylarının, kopyalama etkinliği başarısız olduğunda günlük dosyasına boşaltılmadığından birkaç olasılık olduğundan bu modda günlük kaydı ve zaman içinde garanti edilmez. Şu anda, hedefe kopyalanan bazı dosyalar günlüğe kaydedilmez.  | Doğru<br/>False (varsayılan) | No
logLocationSettings | Oturum günlüklerinin depolayabileceği konumu belirtmek için kullanılabilecek bir özellik grubu. | | No
linkedServiceName | [Azure Blob depolama alanına](connector-azure-blob-storage.md#linked-service-properties) bağlı hizmet veya oturum günlüğü dosyalarını depolamak için [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md#linked-service-properties) . | `AzureBlobStorage` `AzureBlobFS` Günlük dosyalarını depolamak için kullandığınız örneğe başvuran bağlı hizmetin veya türlerin adları. | No
path | Günlük dosyalarının yolu. | Günlük dosyalarını depolamak istediğiniz yolu belirtin. Bir yol sağlamazsanız, hizmet sizin için bir kapsayıcı oluşturur. | No


## <a name="monitoring"></a>İzleme

### <a name="output-from-copy-activity"></a>Kopyalama etkinliğinden çıkış
Kopyalama etkinliği tamamen çalıştıktan sonra, günlük dosyalarının yolunu her bir kopyalama etkinliği çalışmasının çıktısından görebilirsiniz. Günlük dosyalarını şu yoldan bulabilirsiniz: `https://[your-blob-account].blob.core.windows.net/[logFilePath]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` .  Günlük dosyaları CSV dosyaları olacaktır. 

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

> [!NOTE]
> `enableCopyActivityLog`Özelliği olarak ayarlandığında `Enabled` , günlük dosyası adları sistem tarafından oluşturulur.

### <a name="the-schema-of-the-log-file"></a>Günlük dosyasının şeması

Bir günlük dosyasının şeması aşağıda verilmiştir.

Sütun | Açıklama 
-------- | -----------  
Timestamp | ADF nesneyi okurken, yazarken veya atlayan zaman damgası.
Level | Bu öğenin günlük düzeyi. Bu, ' Warning ' veya "Info" olabilir.
OperationName | ADF kopyalama etkinliği işlem davranışı her nesne. ' FileRead ', ' FileWrite ', ' FileSkip ' veya ' TabularRowSkip ' olabilir.
Operationıtem | Dosya adları veya Atlanan satırlar.
İleti | Dosyanın kaynak depodan okunup okunmasından veya hedef depoya yazılabilmesinin gösterilmesi hakkında daha fazla bilgi. Ayrıca, dosyanın veya satırların neden atlandığını da sağlayabilir.

Bir günlük dosyasına bir örnek aşağıda verilmiştir. 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-10-19 08:39:13.6688152,Info,FileRead,"sample1.csv","Start to read file: {""Path"":""sample1.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:39:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
2020-10-19 08:40:13.6688152,Info,FileRead,"sample2.csv","Start to read file: {""Path"":""sample2.csv"",""ItemType"":""File"",""Size"":104857620,""LastModified"":""2020-10-19T08:22:31Z"",""ETag"":""\""0x8D874081F80C01A\"""",""ContentMD5"":""dGKVP8BVIy6AoTtKnt+aYQ=="",""ObjectName"":null}"
2020-10-19 08:40:13.9003981,Info,FileWrite,"sample2.csv","Start to write file from source file: sample2.csv."
2020-10-19 08:45:17.6508407,Info,FileRead,"sample2.csv","Complete reading file successfully. "
2020-10-19 08:45:28.7390083,Info,FileWrite,"sample2.csv","Complete writing file from source file: sample2.csv. File is successfully copied."
```
Yukarıdaki günlük dosyasından, kaynak ve hedef depo arasında tutarlı olarak doğrulanamadığı için sample1.csv atlandığını görebilirsiniz. sample1.csv tutarsız hale geldiği hakkında daha fazla ayrıntı alabilirsiniz çünkü ADF kopyalama etkinliği aynı anda kopyalanırken diğer uygulamalar tarafından değiştirilmekte. Ayrıca, sample2.csv kaynaktan hedef depoya başarıyla kopyalanmış olduğunu da görebilirsiniz.

Günlük dosyalarını daha fazla analiz etmek için birden çok analiz altyapısı kullanabilirsiniz.  CSV günlük dosyasını, tablo adının SessionLogDemo olabilecek SQL veritabanına aktararak, günlük dosyasını çözümlemek üzere SQL sorgusu kullanmak için aşağıda birkaç örnek vardır.  

-   Kopyalanmış dosya listesini bana verin. 
```sql
select OperationItem from SessionLogDemo where Message like '%File is successfully copied%'
```

-   Dosya listesini belirli bir zaman aralığı içinde kopyalanmış olarak bana ver. 
```sql
select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%'
```

-   Kopyalanmış zamanı ve meta verilerini bana özel bir dosya verin. 
```sql
select * from SessionLogDemo where OperationItem='<file name>'
```

-   Meta verileri bir zaman aralığı içinde kopyalanan bir dosya listesi sağlayın. 
```sql
select * from SessionLogDemo where OperationName='FileRead' and Message like 'Start to read%' and OperationItem in (select OperationItem from SessionLogDemo where TIMESTAMP >= '<start time>' and TIMESTAMP <= '<end time>' and Message like '%File is successfully copied%')
```

-   Atlanan dosya listesini bana ver. 
```sql
select OperationItem from SessionLogDemo where OperationName='FileSkip'
```

-   Belirli bir dosyanın atlanma nedenini bana iletin. 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip'
```

-   "Blob dosyası yok" yüzünden atlanan dosyaların listesini bana ver. 
```sql
select TIMESTAMP, OperationItem, Message from SessionLogDemo where OperationName='FileSkip' and Message like '%UserErrorSourceBlobNotExist%'
```

-   En uzun kopyalama süresini gerektiren dosya adını bana verin.
```sql
select top 1 OperationItem, CopyDuration=DATEDIFF(SECOND, min(TIMESTAMP), max(TIMESTAMP)) from SessionLogDemo group by OperationItem order by CopyDuration desc
```


## <a name="next-steps"></a>Sonraki adımlar
Diğer kopyalama etkinliği makalelerine bakın:

- [Kopyalama etkinliğine genel bakış](copy-activity-overview.md)
- [Kopyalama etkinliği hata toleransı](copy-activity-fault-tolerance.md)
- [Kopyalama etkinliği veri tutarlılığı](copy-activity-data-consistency.md)
