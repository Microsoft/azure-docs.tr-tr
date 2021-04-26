---
title: Azure Depolama Analizi günlüğü
description: Azure depolama istekleri hakkındaki ayrıntıları günlüğe kaydetmek için Depolama Analizi kullanın. Hangi isteklerin günlüğe kaydedileceğini, günlüklerin nasıl depolandığını, depolama günlüğünü nasıl etkinleştireceğinizi ve daha fazlasını görün.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: a5d1d6af68fcbd6a5822b2652ee79c464d02241f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200772"
---
# <a name="azure-storage-analytics-logging"></a>Azure Depolama analizini günlüğe kaydetme

Depolama Analizi, bir depolama cihazına gönderilen başarılı ve başarısız isteklerle ilgili ayrıntılı bilgileri günlüğe kaydeder. Bu bilgileri kullanarak istekleri ayrı ayrı izleyebilir ve depolama hizmetiyle ilgili sorunları tanılayabilirsiniz. İstekler en iyi çaba temelinde günlüğe kaydedilir. Bu, çoğu isteğin günlük kaydıyla sonuçlanacağı anlamına gelir, ancak Depolama Analizi günlüklerinin tamamlanma ve zaman içinde garanti edilmez. 

> [!NOTE]
> Azure depolama günlüklerini Depolama Analizi günlükleri yerine Azure Izleyici 'de kullanmanızı öneririz. Azure Izleyici 'de Azure depolama günlükleri genel önizleme aşamasındadır ve tüm genel bulut bölgelerinde önizleme testi için kullanılabilir. Bu önizleme, Bloblar (Azure Data Lake Storage 2.), dosyalar, kuyruklar ve tablolar için Günlükler sunar. Daha fazla bilgi edinmek için aşağıdaki makalelerden birine bakın:
>
> - [Azure Blob depolamayı izleme](../blobs/monitor-blob-storage.md)
> - [Azure dosyalarını izleme](../files/storage-files-monitoring.md)
> - [Azure kuyruk depolamayı izleme](../queues/monitor-queue-storage.md)
> - [Azure Tablo depolamayı izleme](../tables/monitor-table-storage.md)

 Depolama Analizi günlüğe kaydetme özelliği depolama hesabınızda varsayılan olarak etkin değildir. Bunu [Azure Portal](https://portal.azure.com/) etkinleştirebilir veya PowerShell veya Azure CLI kullanarak yapabilirsiniz. Adım adım yönergeler için bkz. [Azure depolama Analizi günlüklerini etkinleştirme ve yönetme (klasik)](manage-storage-analytics-logs.md). 

Ayrıca, REST API veya istemci kitaplığı aracılığıyla Depolama Analizi günlüklerini programlı bir şekilde etkinleştirebilirsiniz. Her hizmet için Depolama Analizi etkinleştirmek üzere [BLOB hizmeti özelliklerini al](/rest/api/storageservices/Blob-Service-REST-API), [kuyruk hizmeti özelliklerini al](/rest/api/storageservices/Get-Queue-Service-Properties)ve [Tablo hizmeti özelliklerini](/rest/api/storageservices/Get-Table-Service-Properties) Al işlemlerini kullanın. .NET kullanarak Depolama Analizi günlüklerini etkinleştiren bir örnek görmek için bkz. [günlükleri etkinleştirme](manage-storage-analytics-logs.md)

 Günlük girişleri yalnızca hizmet uç noktasında yapılan istekler varsa oluşturulur. Örneğin, bir depolama hesabının kendi BLOB uç noktasında etkinliği varsa ancak tablo veya sıra uç noktalarında etkinlik varsa, yalnızca blob hizmetiyle ilgili günlükler oluşturulur.

> [!NOTE]
>  Depolama Analizi günlüğe kaydetme özelliği şu anda yalnızca Blob, Kuyruk ve Tablo hizmetleri için kullanılabilir. Depolama Analizi günlüğe kaydetme, Premium performanslı [blok Blobstorage](../blobs/storage-blob-create-account-block-blob.md) hesapları için de kullanılabilir. Ancak, Premium performansa sahip genel amaçlı v2 hesapları için kullanılamaz.

## <a name="requests-logged-in-logging"></a>Günlüğe kaydedilen istekler
### <a name="logging-authenticated-requests"></a>Kimliği doğrulanmış istekleri günlüğe kaydetme

 Aşağıdaki türden kimliği doğrulanmış istekler kaydedilir:

- Başarılı istekler
- Zaman aşımı, azaltma, ağ, yetkilendirme hatalarını ve diğer hataları içeren başarısız istekler
- Başarısız ve başarılı istekler de dahil olmak üzere Paylaşılan Erişim İmzası (SAS) veya OAuth kullanan istekler
- Analiz verisi istekleri

  Günlük oluşturma veya silme gibi Depolama Analizi kendisi tarafından yapılan istekler günlüğe kaydedilmez. Günlüğe kaydedilen verilerin tam listesi, [depolama Analizi günlüğe kaydedilmiş işlemler ve durum iletileri](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) ve [depolama Analizi günlük biçimi](/rest/api/storageservices/storage-analytics-log-format) konularında belgelenmiştir.

### <a name="logging-anonymous-requests"></a>Anonim istekleri günlüğe kaydetme

 Aşağıdaki anonim istek türleri günlüğe kaydedilir:

- Başarılı istekler
- Sunucu hataları
- İstemci ve sunucu için zaman aşımı hataları
- 304 hata koduna sahip başarısız GET istekleri (Değiştirilmemiş)

  Diğer tüm başarısız anonim istekler günlüğe kaydedilmez. Günlüğe kaydedilen verilerin tam listesi, [depolama Analizi günlüğe kaydedilmiş işlemler ve durum iletileri](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) ve [depolama Analizi günlük biçimi](/rest/api/storageservices/storage-analytics-log-format) konularında belgelenmiştir.

## <a name="how-logs-are-stored"></a>Günlüklerin nasıl depolandığı

Tüm Günlükler `$logs` , bir depolama hesabı için depolama Analizi etkinleştirildiğinde otomatik olarak oluşturulan adlı kapsayıcıda Blok Bloblarında depolanır. `$logs`Kapsayıcı, depolama hesabının blob ad alanında bulunur, örneğin: `http://<accountname>.blob.core.windows.net/$logs` . Depolama Analizi etkinleştirildikten sonra içerikleri silinebilse de bu kapsayıcı silinemez. Doğrudan kapsayıcıya gitmek için depolama tarama aracınızı kullanıyorsanız, günlüğe kaydetme verilerinizi içeren tüm Blobları görürsünüz.

> [!NOTE]
>  Kapsayıcı, `$logs` liste kapsayıcıları işlemi gibi bir kapsayıcı listeleme işlemi gerçekleştirildiğinde gösterilmez. Doğrudan erişilmesi gerekir. Örneğin, kapsayıcıdaki bloblara erişmek için Blobları Listele işlemini kullanabilirsiniz `$logs` .

İstekler günlüğe kaydedildiğinde Depolama Analizi, ara sonuçları bloklar olarak karşıya yükler. Düzenli aralıklarla, Depolama Analizi bu blokları yapar ve bunları bir blob olarak kullanılabilir hale getirir. Depolama hizmetinin günlük yazıcılarını boşaltdığı sıklık nedeniyle, günlük verilerinin **$logs** kapsayıcısındaki bloblarda görünmesi bir saate kadar sürebilir. Aynı saat içinde oluşturulan Günlükler için yinelenen kayıtlar var olabilir. **RequestId** ve **işlem** numarasını denetleyerek bir kaydın yinelenen olup olmadığını belirleyebilirsiniz.

Her saat için birden çok dosya içeren yüksek miktarda günlük veriniz varsa, blob meta verileri alanlarını inceleyerek günlüğün hangi verileri içerdiğini belirlemek için blob meta verilerini kullanabilirsiniz. Bu Ayrıca, veriler günlük dosyalarına yazılırken bir gecikme olabileceğinden da yararlıdır: blob meta verileri, Blob içeriğinin blob adından daha doğru bir şekilde göstergesidir.

Çoğu depolama gözatma araçları, Blobların meta verilerini görüntülemenizi sağlar; Ayrıca, bu bilgileri PowerShell veya programlı olarak kullanarak da okuyabilirsiniz. Aşağıdaki PowerShell kod parçacığı, bir zaman belirtmek için günlük bloblarının listesini ada göre filtreleme ve yalnızca **yazma** işlemleri içeren günlükleri tanımlamak için meta veriler ile ilgili bir örnektir.  

 ```powershell
 Get-AzStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'blob/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

Blob 'ları program aracılığıyla listeleme hakkında bilgi için bkz. blob [kaynaklarını numaralandırma](/rest/api/storageservices/Enumerating-Blob-Resources) ve [BLOB kaynakları Için özellikleri ve meta verileri ayarlama ve alma](/rest/api/storageservices/Setting-and-Retrieving-Properties-and-Metadata-for-Blob-Resources).  

### <a name="log-naming-conventions"></a>Günlük adlandırma kuralları

 Her günlük aşağıdaki biçimde yazılır:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 Aşağıdaki tabloda, günlük adındaki her bir öznitelik açıklanmaktadır:

|Öznitelik|Açıklama|
|---------------|-----------------|
|`<service-name>`|Depolama hizmetinin adı. Örneğin: `blob` , `table` , veya `queue`|
|`YYYY`|Günlük için dört basamaklı yıl. Örnek: `2011`|
|`MM`|Günlüğün iki basamaklı ayı. Örnek: `07`|
|`DD`|Günlüğün iki basamaklı günü. Örnek: `31`|
|`hh`|24 saat UTC biçiminde Günlükler için başlangıç saatini gösteren iki basamaklı saat. Örnek: `18`|
|`mm`|Günlükler için başlangıç dakikasını gösteren iki basamaklı sayı. **Note:**  Bu değer, geçerli Depolama Analizi sürümünde desteklenmez ve değeri her zaman olur `00` .|
|`<counter>`|Bir saatlik zaman diliminde depolama hizmeti için oluşturulan günlük bloblarının sayısını belirten altı basamaklı sıfır tabanlı bir sayaç. Bu sayaç tarihinde başlar `000000` . Örnek: `000001`|

 Aşağıda, Yukarıdaki örnekleri birleştiren bir örnek günlük adı verilmiştir:

 `blob/2011/07/31/1800/000001.log`

 Aşağıda, yukarıdaki günlüğe erişmek için kullanılabilecek bir örnek URI verilmiştir:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Bir depolama isteği günlüğe kaydedildiğinde, sonuçta elde edilen günlük adı, istenen işlem tamamlandığında saat ile ilişkili olur. Örneğin, 7/31/2011 tarihinde bir GetBlob isteği tamamlanırsa, günlük şu önek ile yazılır: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Günlük meta verileri

 Tüm günlük Blobları, blob 'un içerdiği günlüğe kaydetme verilerini belirlemek için kullanılabilecek meta verilerle depolanır. Aşağıdaki tabloda her meta veri özniteliği açıklanmaktadır:

|Öznitelik|Açıklama|
|---------------|-----------------|
|`LogType`|Günlük okuma, yazma veya silme işlemleriyle ilgili bilgiler içerip içermediğini açıklar. Bu değer, virgülle ayrılmış olarak bir tür ya da üç tane birleşimi içerebilir.<br /><br /> Örnek 1: `write`<br /><br /> Örnek 2: `read,write`<br /><br /> Örnek 3: `read,write,delete`|
|`StartTime`|Günlükteki girdinin en erken saati, biçiminde `YYYY-MM-DDThh:mm:ssZ` . Örnek: `2011-07-31T18:21:46Z`|
|`EndTime`|Günlükteki bir girişin en son saati, biçiminde `YYYY-MM-DDThh:mm:ssZ` . Örnek: `2011-07-31T18:22:09Z`|
|`LogVersion`|Günlük biçiminin sürümü.|

 Aşağıdaki liste Yukarıdaki örnekleri kullanarak tüm örnek meta verileri görüntüler:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`


## <a name="next-steps"></a>Sonraki adımlar

* [Azure Depolama Analizi günlüklerini etkinleştirme ve yönetme (klasik)](manage-storage-analytics-logs.md)
* [Depolama Analizi günlük biçimi](/rest/api/storageservices/storage-analytics-log-format)
* [Günlüğe kaydedilen Işlemleri ve durum Iletilerini Depolama Analizi](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Depolama Analizi ölçümleri (klasik)](storage-analytics-metrics.md)
