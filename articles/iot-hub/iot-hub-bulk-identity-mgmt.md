---
title: Azure IoT Hub cihaz kimliklerinin içeri aktarılmasını içeri aktarma | Microsoft Docs
description: Cihaz kimliklerini içeri ve dışarı aktarmak için kimlik kayıt defterine yönelik toplu işlemler gerçekleştirmek üzere Azure IoT hizmeti SDK 'sını kullanma. İçeri aktarma işlemleri, cihaz kimliklerini toplu olarak oluşturmanızı, güncelleştirmenizi ve silmenizi sağlar.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: robinsh
ms.openlocfilehash: 3b498911999e224003710eced8e0c34989672800
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73809193"
---
# <a name="import-and-export-iot-hub-device-identities-in-bulk"></a>IoT Hub cihaz kimliklerini toplu olarak içeri ve dışarı aktarma

Her IoT Hub 'ında, hizmette cihaz başına kaynaklar oluşturmak için kullanabileceğiniz bir kimlik kayıt defteri vardır. Kimlik kayıt defteri, cihaza yönelik uç noktalara erişimi denetlemenize de olanak sağlar. Bu makalede, cihaz kimliklerinin bir kimlik kayıt defterine toplu ve dışarı aktarma işlemlerinin nasıl yapılacağı açıklanır. Üzerinde C# çalışan bir örnek görmek ve bir hub 'ı farklı bir bölgeye kopyalarken bu özelliği nasıl kullanabileceğinizi öğrenmek için, bkz. [IoT Hub kopyalama](iot-hub-how-to-clone.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

İçeri ve dışarı aktarma işlemleri, bir IoT Hub 'ına karşı toplu hizmet işlemlerini yürütmelerine olanak sağlayan *işlerin* bağlamında gerçekleşir.

**Registrymanager** sınıfı, **Iş** çerçevesini kullanan **Exportdevicesasync** ve **ımportdevicesasync** yöntemlerini içerir. Bu yöntemler, IoT Hub kimlik kayıt defterinin tamamını dışa aktarmanıza, içeri aktarmanızı ve eşitlemenize olanak tanır.

Bu konu, bir IoT Hub 'ının kimlik kayıt defterine ve aygıtlara toplu içeri aktarma ve dışarı aktarma işlemleri gerçekleştirmek için **Registrymanager** sınıfının ve **iş** sisteminin kullanımını tartışır. Ayrıca, Azure IoT Hub cihaz sağlama hizmeti 'ni kullanarak bir veya daha fazla IoT Hub 'ı için insan müdahalesi gerektirmeden tam zamanında sağlama olanağı sağlayabilirsiniz. Daha fazla bilgi edinmek için bkz. [sağlama hizmeti belgeleri](/azure/iot-dps).


## <a name="what-are-jobs"></a>İşler nedir?

Kimlik kayıt defteri işlemleri işlem sırasında **iş** sistemini kullanır:

* , Standart çalışma zamanı işlemlerine kıyasla uzun bir yürütme süresine sahiptir.

* Kullanıcıya büyük miktarda veri döndürür.

İşlemin sonucunu bekleyen veya engelleyen tek bir API çağrısı yerine, işlem zaman uyumsuz olarak bu IoT Hub 'ı için bir **iş** oluşturur. İşlem daha sonra hemen bir **Jobproperties** nesnesi döndürür.

Aşağıdaki C# kod parçacığı, bir dışarı aktarma işinin nasıl oluşturulacağını gösterir:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await 
  registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> C# Kodunuzda **registrymanager** sınıfını kullanmak için **Microsoft. Azure. Devices** NuGet paketini projenize ekleyin. **Registrymanager** sınıfı, **Microsoft. Azure. Devices** ad alanıdır.

Döndürülen **Jobproperties** meta verilerini kullanarak **iş** durumunu sorgulamak için **registrymanager** sınıfını kullanabilirsiniz. **Registrymanager** sınıfının bir örneğini oluşturmak Için **createfromconnectionstring** yöntemini kullanın.

```csharp
RegistryManager registryManager =
  RegistryManager.CreateFromConnectionString("{your IoT Hub connection string}");
```

IoT Hub 'ınız için bağlantı dizesini Azure portal bulmak için:

- IoT Hub'ınıza gidin.

- **Paylaşılan erişim ilkeleri**' ni seçin.

- İhtiyaç duyduğunuz izinleri hesaba ayırarak bir ilke seçin.

- Ekranın sağ tarafındaki panelden ConnectionString öğesini kopyalayın.

Aşağıdaki C# kod parçacığı, işin yürütülmesini tamamladığını görmek için beş saniyede bir yoklamayı gösterir:

```csharp
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="device-importexport-job-limits"></a>Cihaz içeri/dışarı aktarma işi sınırları

Tüm IoT Hub katmanları için tek seferde yalnızca 1 etkin cihaza içeri veya dışarı aktarma işine izin verilir. IoT Hub ayrıca iş hızı işlemleri için sınırlara sahiptir. Daha fazla bilgi edinmek için bkz. [başvuru IoT Hub kotaları ve azaltma](iot-hub-devguide-quotas-throttling.md).

## <a name="export-devices"></a>Cihazları dışarı aktar

Bir IoT Hub kimlik kayıt defterinin tamamını [paylaşılan erişim imzasını](../storage/common/storage-security-guide.md#authorization)kullanarak bir [Azure depolama](../storage/index.yml) blobu kapsayıcısına aktarmak Için **exportdevicesasync** yöntemini kullanın.

Bu yöntem, denetlediğiniz bir blob kapsayıcısında cihaz bilgilerinizin güvenilir yedeklerini oluşturmanızı sağlar.

**Exportdevicesasync** yöntemi iki parametre gerektirir:

* Blob kapsayıcısının URI 'sini içeren bir *dize* . Bu URI, kapsayıcıya yazma erişimi veren bir SAS belirteci içermelidir. İş, seri hale getirilmiş dışarı aktarma cihaz verilerini depolamak için bu kapsayıcıda bir Blok Blobu oluşturur. SAS belirteci şu izinleri içermelidir:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

* Dışarı aktarma verilerinizde kimlik doğrulama anahtarlarını dışlamak istediğinizi belirten bir *Boole değeri* . **Yanlışsa**, kimlik doğrulama anahtarları dışarı aktarma çıktısına dahil edilir. Aksi halde, anahtarlar **null**olarak verilir.

Aşağıdaki C# kod parçacığı, verileri dışarı aktarma bölümünde cihaz kimlik doğrulama anahtarlarını içeren bir dışarı aktarma işinin nasıl başlatılacağı ve sonra tamamlanmasını yoklamanın nasıl yapılacağını göstermektedir:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
  await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

İş, çıktısını, belirtilen blob kapsayıcısında, **Devices. txt**adlı bir Blok Blobu olarak depolar. Çıktı verileri, her satırda bir cihaz içeren JSON serileştirilmiş cihaz verilerinden oluşur.

Aşağıdaki örnek, çıkış verilerini gösterir:

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Bir cihazda ikizi veriler varsa, ikizi verileri de cihaz verileriyle birlikte verilir. Aşağıdaki örnek bu biçimi gösterir. "TwinETag" satırı, son ikizi veri olana kadar tüm veriler.

```json
{
   "id":"export-6d84f075-0",
   "eTag":"MQ==",
   "status":"enabled",
   "statusReason":"firstUpdate",
   "authentication":null,
   "twinETag":"AAAAAAAAAAI=",
   "tags":{
      "Location":"LivingRoom"
   },
   "properties":{
      "desired":{
         "Thermostat":{
            "Temperature":75.1,
            "Unit":"F"
         },
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
            "$lastUpdatedVersion":2,
            "Thermostat":{
               "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
               "$lastUpdatedVersion":2,
               "Temperature":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               },
               "Unit":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               }
            }
         },
         "$version":2
      },
      "reported":{
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:51.1309437Z"
         },
         "$version":1
      }
   }
}
```

Kodda bu verilere erişmeniz gerekiyorsa, **Exportımportdevice** sınıfını kullanarak bu verilerin serisini kolayca kaldırabilirsiniz. Aşağıdaki C# kod parçacığı, daha önce bir blok blobuna aktarılmış olan cihaz bilgilerinin nasıl okunacağını göstermektedir:

```csharp
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), null, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

## <a name="import-devices"></a>Cihazları içeri aktar

**Registrymanager** sınıfındaki **ımportdevicesasync** yöntemi, bir IoT Hub kimlik kayıt defterinde toplu içeri aktarma ve eşitleme işlemleri gerçekleştirmenize olanak sağlar. **Exportdevicesasync** yöntemi gibi, **ımportdevicesasync** yöntemi de **iş** çerçevesini kullanır.

**Importdevicesasync** yöntemini kullanarak dikkatli olun, çünkü kimlik kayıt defterinizde yeni cihazlar sağlamaya ek olarak, var olan cihazları güncelleştirebilir ve silebilir.

> [!WARNING]
> İçeri aktarma işlemi geri alınamaz. Kimlik kayıt defterinizde toplu değişiklikler yapmadan önce, **Exportdevicesasync** yöntemini kullanarak mevcut verilerinizi her zaman başka bir blob kapsayıcısına yedekleyin.

**Importdevicesasync** yöntemi iki parametre alır:

* İş için *giriş* olarak kullanılacak bir [Azure Storage](../storage/index.yml) blob kapsayıcısının URI 'sini içeren bir *dize* . Bu URI, kapsayıcıya okuma erişimi veren bir SAS belirteci içermelidir. Bu kapsayıcı, kimlik kayıt defterinize aktarılacak seri hale getirilmiş cihaz verilerini içeren **Devices. txt** adlı bir blob içermelidir. İçeri aktarma verileri, Device **. txt** blobu oluşturduğunda, **Exportımportdevice** işinin kullandığı JSON biçiminde cihaz bilgilerini içermelidir. SAS belirteci şu izinleri içermelidir:

   ```csharp
   SharedAccessBlobPermissions.Read
   ```

* İşten *çıktı* olarak kullanılacak bir [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) blob kapsayıcısının URI 'sini içeren bir *dize* . İş, tamamlanan içeri aktarma **işinden**hata bilgilerini depolamak için bu kapsayıcıda bir Blok Blobu oluşturur. SAS belirteci şu izinleri içermelidir:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> İki parametre aynı blob kapsayıcısını işaret edebilir. Ayrı parametreler, çıkış kapsayıcısı ek izinler gerektirdiğinden verileriniz üzerinde daha fazla denetim sağlamak yeterlidir.

Aşağıdaki C# kod parçacığı, bir içeri aktarma işinin nasıl başlatılacağı gösterilmektedir:

```csharp
JobProperties importJob = 
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Bu yöntem ayrıca cihaz ikizi verilerini içeri aktarmak için de kullanılabilir. Veri girişi biçimi **Exportdevicesasync** bölümünde gösterilen biçimle aynıdır. Bu şekilde, dışarı aktarılan verileri yeniden içeri aktarabilirsiniz. **$Metadata** isteğe bağlıdır.

## <a name="import-behavior"></a>İçeri aktarma davranışı

Kimlik kayıt defterinizde aşağıdaki toplu işlemleri gerçekleştirmek için **ımportdevicesasync** yöntemini kullanabilirsiniz:

* Yeni cihazların toplu kaydı
* Mevcut cihazların toplu silme işlemleri
* Toplu durum değişiklikleri (cihazları etkinleştir veya devre dışı bırak)
* Yeni cihaz kimlik doğrulama anahtarlarının toplu ataması
* Cihaz kimlik doğrulama anahtarlarını toplu otomatik yeniden oluşturma
* İkizi verilerinin toplu güncelleştirmesi

Önceki işlemlerin herhangi bir bileşimini tek bir **ımportdevicesasync** çağrısı içinde gerçekleştirebilirsiniz. Örneğin, yeni cihazları kaydedebilir ve var olan cihazları aynı anda silebilir veya güncelleştirebilirsiniz. **Exportdevicesasync** yöntemiyle birlikte kullanıldığında, tüm cihazlarınızı bir IoT Hub 'ından diğerine tamamen geçirebilirsiniz.

İçeri aktarma dosyası ikizi meta verileri içeriyorsa, bu meta veriler var olan ikizi meta verilerinin üzerine yazar. İçeri aktarma dosyası ikizi meta verileri içermiyorsa, yalnızca `lastUpdateTime` meta veriler geçerli saat kullanılarak güncelleştirilir.

Cihaz başına içeri aktarma işlemini denetlemek için her bir cihaz için serileştirme verilerini içeri aktarma ' da isteğe bağlı **ImportMode** özelliğini kullanın. **ImportMode** özelliği aşağıdaki seçeneklere sahiptir:

| ImportMode | Açıklama |
| --- | --- |
| **createOrUpdate** |Belirtilen **kimliğe**sahip bir cihaz yoksa, yeni kaydedilir. <br/>Cihaz zaten varsa, varolan bilgilerin, **ETag** değeriyle ilgili olarak girilen giriş verileriyle üzerine yazılır. <br> Kullanıcı isteğe bağlı olarak cihaz verileriyle birlikte ikizi verisi belirtebilir. İkizi 'ın ETag 'i, belirtilmişse cihazın ETag öğesinden bağımsız olarak işlenir. Varolan ikizi ETag ile bir uyumsuzluk varsa, günlük dosyasına bir hata yazılır. |
| **oluşturmaya** |Belirtilen **kimliğe**sahip bir cihaz yoksa, yeni kaydedilir. <br/>Cihaz zaten varsa, günlük dosyasına bir hata yazılır. <br> Kullanıcı isteğe bağlı olarak cihaz verileriyle birlikte ikizi verisi belirtebilir. İkizi 'ın ETag 'i, belirtilmişse cihazın ETag öğesinden bağımsız olarak işlenir. Varolan ikizi ETag ile bir uyumsuzluk varsa, günlük dosyasına bir hata yazılır. |
| **Update** |Belirtilen **kimliğe**sahip bir cihaz zaten varsa, varolan bilgilerin **ETag** değerine bakılmaksızın, girilen giriş verileriyle üzerine yazılır. <br/>Cihaz yoksa, günlük dosyasına bir hata yazılır. |
| **updateIfMatchETag** |Belirtilen **kimliğe**sahip bir cihaz zaten varsa, yalnızca bir **ETag** eşleşmesi varsa, mevcut bilgilerin girilen giriş verileriyle üzerine yazılır. <br/>Cihaz yoksa, günlük dosyasına bir hata yazılır. <br/>**ETag** uyumsuzluğu varsa, günlük dosyasına bir hata yazılır. |
| **createOrUpdateIfMatchETag** |Belirtilen **kimliğe**sahip bir cihaz yoksa, yeni kaydedilir. <br/>Cihaz zaten mevcutsa, belirtilen giriş verileriyle yalnızca bir **ETag** eşleşmesi varsa, varolan bilgilerin üzerine yazılır. <br/>**ETag** uyumsuzluğu varsa, günlük dosyasına bir hata yazılır. <br> Kullanıcı isteğe bağlı olarak cihaz verileriyle birlikte ikizi verisi belirtebilir. İkizi 'ın ETag 'i, belirtilmişse cihazın ETag öğesinden bağımsız olarak işlenir. Varolan ikizi ETag ile bir uyumsuzluk varsa, günlük dosyasına bir hata yazılır. |
| **sil** |Belirtilen **kimliğe**sahip bir cihaz zaten varsa, **ETag** değeri dikkate almadan silinir. <br/>Cihaz yoksa, günlük dosyasına bir hata yazılır. |
| **deleteIfMatchETag** |Belirtilen **kimliğe**sahip bir cihaz zaten varsa, yalnızca bir **ETag** eşleşmesi varsa silinir. Cihaz yoksa, günlük dosyasına bir hata yazılır. <br/>ETag uyumsuzluğu varsa, günlük dosyasına bir hata yazılır. |

> [!NOTE]
> Serileştirme verileri bir cihaz için açıkça bir **ImportMode** bayrağı tanımlamıyorsa, içeri aktarma işlemi sırasında varsayılan olarak **CreateOrUpdate** olur.

## <a name="import-devices-example--bulk-device-provisioning"></a>Cihazları içeri aktarma örneği – toplu cihaz sağlama

Aşağıdaki kod C# örneği, şu şekilde birden çok cihaz kimliği oluşturmayı göstermektedir:

* Kimlik doğrulama anahtarlarını dahil edin.
* Bu cihaz bilgilerini bir blok blobuna yazın.
* Cihazları kimlik kayıt defterine aktarın.

```csharp
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
  // Create a new ExportImportDevice
  // CryptoKeyGenerator is in the Microsoft.Azure.Devices.Common namespace
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to the list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write the list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the blob to add new devices
// Log information related to the job is written to the same container
// This normally takes 1 minute per 100 devices
JobProperties importJob =
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>Cihazları içeri aktarma örneği – toplu silme

Aşağıdaki kod örneği, önceki kod örneğini kullanarak eklediğiniz cihazların nasıl silineceğini gösterir:

```csharp
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="get-the-container-sas-uri"></a>Kapsayıcı SAS URI 'sini al

Aşağıdaki kod örneğinde bir blob kapsayıcısı için okuma, yazma ve silme izinleriyle bir [SAS URI 'si](../storage/common/storage-dotnet-shared-access-signature-part-1.md) oluşturma gösterilmektedir:

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}
```

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede, bir IoT Hub 'ında kimlik kayıt defterine yönelik toplu işlemler gerçekleştirmeyi öğrendiniz. Cihazların bir hub 'dan diğerine taşınması dahil olmak üzere bu işlemlerin çoğu, [bir IoT Hub nasıl klonlamalarının IoT Hub 'ına kayıtlı cihazları yönetme bölümünde](iot-hub-how-to-clone.md#managing-the-devices-registered-to-the-iot-hub)kullanılır. 

Kopyalama makalesinde onunla ilişkili çalışan bir örnek vardır ve bu sayfada IoT C# örneklerinde bulunur: [için C#Azure IoT örnekleri ](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/), projeyle ımportexportdevicessample. Örneği indirebilir ve deneyebilirsiniz; [IoT Hub nasıl klonlamadaki](iot-hub-how-to-clone.md) yönergeler vardır.

Azure IoT Hub 'yi yönetme hakkında daha fazla bilgi edinmek için aşağıdaki makalelere göz atın:

* [IoT Hub ölçümleri](iot-hub-metrics.md)
* [IoT Hub günlükleri](iot-hub-monitor-resource-health.md)

IoT Hub yeteneklerini daha fazla incelemek için bkz.:

* [IoT Hub Geliştirici Kılavuzu](iot-hub-devguide.md)
* [Azure IoT Edge ile uç cihazlara AI dağıtma](../iot-edge/tutorial-simulate-device-linux.md)

Tam zamanında sağlama işlemini etkinleştirmek üzere IoT Hub cihaz sağlama hizmetini kullanarak araştırmak için, bkz.: 

* [Azure IoT Hub Cihazı Sağlama Hizmeti](/azure/iot-dps)
