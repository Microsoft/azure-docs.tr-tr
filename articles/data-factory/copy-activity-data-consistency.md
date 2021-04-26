---
title: Kopyalama etkinliğinde veri tutarlılığı doğrulaması
description: Azure Data Factory ' de kopyalama etkinliğinde veri tutarlılığı doğrulamanın nasıl etkinleştirileceği hakkında bilgi edinin.
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 3/27/2020
ms.author: yexu
ms.openlocfilehash: b71657f67c1b9c623d6d48f33b986ac43533cca6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373025"
---
#  <a name="data-consistency-verification-in-copy-activity"></a>Kopyalama etkinliğinde veri tutarlılığı doğrulaması

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Verileri kaynaktan hedef depoya taşıdığınızda, Azure Data Factory kopyalama etkinliği, verilerin kaynaktan yalnızca kaynak ve hedef depo arasında tutarlı bir şekilde kopyalanmadığından emin olmak için ek veri tutarlılığı doğrulaması yapmanız için bir seçenek sunar. Veri taşıma sırasında tutarsız dosyalar bulunduğunda, kopyalama etkinliğini durdurabilir veya geri kalanı kopyalamaya devam ederek hata toleransı ayarını tutarsız dosyaları atlayacak şekilde ayarlayabilirsiniz. Kopyalama etkinliğinde oturum günlüğü ayarını etkinleştirerek Atlanan dosya adlarını alabilirsiniz. Daha fazla ayrıntı için, [kopyalama etkinliğinde oturum günlüğü '](copy-activity-log.md) ne başvurabilirsiniz.

## <a name="supported-data-stores-and-scenarios"></a>Desteklenen veri depoları ve senaryolar

-   Veri tutarlılığı doğrulama, FTP, sFTP ve HTTP dışındaki tüm bağlayıcılar tarafından desteklenir. 
-   Hazırlama kopyalama senaryosunda veri tutarlılığı doğrulaması desteklenmez.
-   İkili dosyaları kopyalarken, veri tutarlılığı doğrulaması yalnızca kopyalama etkinliğinde ' PreserveHierarchy ' davranışı ayarlandığında kullanılabilir.
-   Veri tutarlılığı doğrulaması etkinken birden çok ikili dosyayı tek kopyalama etkinliğinde kopyalarken, kopyalama etkinliğini iptal etme ya da geri kalanını kopyalamaya devam etme seçeneğiniz vardır. 
-   Veri tutarlılığı doğrulaması etkinken tek bir kopyalama etkinliğinde tablo kopyalarken, kaynaktan okunan satır sayısı, hedefe kopyalanan satır sayısından ve atlanan uyumsuz satırların sayısına farklıysa kopyalama etkinliği başarısız olur.


## <a name="configuration"></a>Yapılandırma
Aşağıdaki örnek, kopyalama etkinliğinde veri tutarlılığı doğrulamasını etkinleştirmek için bir JSON tanımı sağlar: 

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
    "validateDataConsistency": true, 
    "skipErrorFile": { 
        "dataInconsistency": true 
    }, 
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
Validatedatatutarlılığı | Bu özellik için true ayarlarsanız, ikili dosyalar kopyalanırken kopyalama etkinliği, kaynak ve hedef depo arasında veri tutarlılığı sağlamak için kaynaktan hedef depoya kopyalanan her bir ikili dosyanın dosya boyutunu, lastModifiedDate ve MD5 sağlama toplamını denetler. Tablo verilerini kopyalarken, kopyalama etkinliği, kaynaktan okunan toplam satır sayısının, hedefe kopyalanan satırların sayısı artı atlanan uyumsuz satır sayısı ile aynı olduğundan emin olmak için iş tamamlandıktan sonra toplam satır sayısını denetler. Bu seçeneği etkinleştirerek kopyalama performansının etkileneceğini unutmayın.  | Doğru<br/>False (varsayılan) | No
Veri tutarsızlığı | Tutarsız dosyaları atlamak istediğinizi öğrenmek için skipErrorFile özellik paketi içindeki anahtar-değer çiftlerinden biri. <br/> -True: tutarsız dosyaları atlayarak geri kalanı kopyalamak istiyorsunuz.<br/> -False: tutarsız dosya bulunduğunda kopyalama etkinliğini iptal etmek istiyorsunuz.<br/>Bu özelliğin yalnızca ikili dosyaları kopyalarken ve Validatedatatutarlılığı true olarak ayarlandığında geçerli olduğunu unutmayın.  | Doğru<br/>False (varsayılan) | No
Günlüğe kaydetme ayarları | Atlanan dosyaları günlüğe kaydetmek için oturum günlüğünü etkinleştirmek üzere belirtilenebilir bir özellik grubu. | | No
linkedServiceName | [Azure Blob depolama alanına](connector-azure-blob-storage.md#linked-service-properties) bağlı hizmet veya oturum günlüğü dosyalarını depolamak için [Azure Data Lake Storage 2.](connector-azure-data-lake-storage.md#linked-service-properties) . | `AzureBlobStorage` `AzureBlobFS` Günlük dosyalarını depolamak için kullandığınız örneğe başvuran bağlı hizmetin veya türlerin adları. | No
path | Günlük dosyalarının yolu. | Günlük dosyalarını depolamak istediğiniz yolu belirtin. Bir yol sağlamazsanız, hizmet sizin için bir kapsayıcı oluşturur. | No

>[!NOTE]
>- Ya da Azure Blob veya Azure Data Lake Storage 2. ikili dosyalarını kopyalarken, ADF, [Azure Blob API 'si](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions?view=azure-dotnet-legacy&preserve-view=true) ve [Azure Data Lake Storage 2. API 'si](/rest/api/storageservices/datalakestoragegen2/path/update#request-headers)aracılığıyla blok düzeyinde MD5 sağlama toplamı doğrulaması yapar. Azure Blob üzerinde ContentMD5 veya veri kaynağı olarak Azure Data Lake Storage 2., ADF dosyaları okuduktan sonra dosya düzeyi MD5 sağlama toplamı doğrulaması yapar. Dosyaları Azure Blob 'a veya veri hedefi olarak Azure Data Lake Storage 2. kopyaladıktan sonra, ADF, veri tutarlılığı doğrulaması için aşağı akış uygulamaları tarafından daha fazla tüketilen Azure Blob veya Azure Data Lake Storage 2. ContentMD5 yazar.
>- ADF, herhangi bir depolama deposu arasında ikili dosyalar kopyalanırken dosya boyutu doğrulaması yapar.

## <a name="monitoring"></a>İzleme

### <a name="output-from-copy-activity"></a>Kopyalama etkinliğinden çıkış
Kopyalama etkinliği tamamen çalıştıktan sonra, her bir kopyalama etkinliği çalışmasının çıktısından veri tutarlılığı doğrulamasının sonucunu görebilirsiniz:

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
"Dataımst doğrulama özelliği" kaynağından veri tutarlılığı doğrulama ayrıntılarını görebilirsiniz.

**Doğrulamaları Icationresult** değeri: 
-   **Doğrulandı**: kopyalanmış verilerinizin kaynak ve hedef depo arasında tutarlı olması doğrulandı. 
-   **Notdoğrulandı**: kopyalama etkinliğinde Validatedatatutarlılığını etkinleştirmediyseniz, kopyalanmış verileriniz tutarlı olarak doğrulanmadı. 
-   **Desteklenmiyor**: Bu belirli bir kopya çifti için veri tutarlılığı doğrulaması desteklenmediğinden, kopyalanmış verileriniz tutarlı olarak doğrulanmadı. 

**Inreferencetentdata** değeri: 
-   **Bulundu**: ADF kopyalama etkinliği tutarsız veriler buldu. 
-   **Atlandı**: ADF kopyalama etkinliği tutarsız veriler buldu ve atladı. 
-   **Hiçbiri**: ADF kopyalama etkinliği tutarsız veriler bulamadı. Verilerinizin kaynak ve hedef depo arasında tutarlı olarak doğrulanması veya kopyalama etkinliğinde Validatedatatutarlılığı devre dışı bırakılması nedeniyle olabilir. 

### <a name="session-log-from-copy-activity"></a>Kopyalama etkinliğinden oturum günlüğü

Tutarsız dosyayı günlüğe kaydetmek üzere yapılandırırsanız, günlük dosyasını şu yoldan bulabilirsiniz: `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/copyactivity-logs/[copy-activity-name]/[copy-activity-run-id]/[auto-generated-GUID].csv` .  Günlük dosyaları CSV dosyaları olacaktır. 

Bir günlük dosyasının şeması aşağıdaki gibidir:

Sütun | Açıklama 
-------- | -----------  
Timestamp | ADF tutarsız dosyaları atlamazsa zaman damgası.
Level | Bu öğenin günlük düzeyi. Dosya atlama gösteren öğe için ' uyarı ' düzeyinde olacaktır.
OperationName | ADF kopyalama etkinliği işlem davranışı her dosyada. Atlanacak dosyayı belirtmek için ' FileSkip ' olur.
Operationıtem | Atlanacak dosya adı.
İleti | Dosyaların neden atlandığını gösteren daha fazla bilgi.

Bir günlük dosyası örneği aşağıdaki gibidir: 
```
Timestamp, Level, OperationName, OperationItem, Message
2020-02-26 06:22:56.3190846, Warning, FileSkip, "sample1.csv", "File is skipped after read 548000000 bytes: ErrorCode=DataConsistencySourceDataChanged,'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Source file 'sample1.csv' is changed by other clients during the copy activity run.,Source=,'." 
```
Yukarıdaki günlük dosyasından, kaynak ve hedef depo arasında tutarlı olarak doğrulanamadığı için sample1.csv atlandığını görebilirsiniz. sample1.csv tutarsız hale geldiği hakkında daha fazla ayrıntı alabilirsiniz çünkü ADF kopyalama etkinliği aynı anda kopyalanırken diğer uygulamalar tarafından değiştirilmekte. 



## <a name="next-steps"></a>Sonraki adımlar
Diğer kopyalama etkinliği makalelerine bakın:

- [Kopyalama etkinliğine genel bakış](copy-activity-overview.md)
- [Kopyalama etkinliği hata toleransı](copy-activity-fault-tolerance.md)