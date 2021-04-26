---
title: Azure IoT Hub dosya yüklemeyi anlama | Microsoft Docs
description: Geliştirici Kılavuzu-bir cihazdan Azure Storage blob kapsayıcısına dosya yüklemeyi yönetmek için IoT Hub dosya karşıya yükleme özelliğini kullanın.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: d106021d90304a06ea7c08494d626511bb903df0
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553048"
---
# <a name="upload-files-with-iot-hub"></a>IoT Hub ile dosyaları karşıya yükleme

[IoT Hub uç noktalar](iot-hub-devguide-endpoints.md) makalesinde açıklandığı gibi, bir cihaz cihaza yönelik bir uç nokta (**/Devices/{deviceid}/Files**) aracılığıyla bir bildirim göndererek karşıya dosya yükleme başlatabilir. Bir cihaz karşıya yükleme işleminin tamamlandığını IoT Hub bildirdiğinde, IoT Hub **/ileti/servicebound/filenotifications** hizmetine yönelik uç nokta aracılığıyla bir dosya karşıya yükleme bildirim iletisi gönderir.

IoT Hub kendi kendine ileti almak yerine IoT Hub, ilişkili bir Azure depolama hesabına dağıtıcı görevi görür. Bir cihaz, cihazın karşıya yüklenmesini istediği dosyaya özgü IoT Hub bir depolama belirteci ister. Cihaz, dosyayı depolamaya yüklemek için SAS URI 'sini kullanır ve karşıya yükleme tamamlandığında, cihaz IoT Hub tamamlanma bildirimi gönderir. IoT Hub dosyayı karşıya yükleme işleminin tamamlandığını denetler ve ardından hizmete yönelik dosya bildirim uç noktasına bir karşıya dosya yükleme bildirim iletisi ekler.

Bir cihazdan IoT Hub bir dosyayı karşıya yüklemeden önce, bir [Azure depolama](iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub) hesabını onunla ilişkilendirerek hub 'ınızı yapılandırmanız gerekir.

Cihazınız daha sonra karşıya yüklemeyi [başlatabilir](iot-hub-devguide-file-upload.md#initialize-a-file-upload) ve ardından yükleme tamamlandığında [IoT Hub 'ına bildirimde](iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload) bulunabilir. İsteğe bağlı olarak, bir cihaz karşıya yükleme işleminin tamamlandığını IoT Hub bildirimde bulunduğunda, hizmet bir [bildirim iletisi](iot-hub-devguide-file-upload.md#file-upload-notifications)oluşturabilir.

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

### <a name="when-to-use"></a>Kullanılması gereken durumlar

Dosya yükleme ' yi kullanarak, aralıklı olarak bağlanmış cihazlar tarafından karşıya yüklenen veya bant genişliği tasarrufu için sıkıştırılan büyük telemetri toplu işleri gönderin.

Bildirilen özellikler, cihazdan buluta iletiler veya karşıya dosya yükleme arasında şüpheliyse [cihazdan buluta iletişim kılavuzuna](iot-hub-devguide-d2c-guidance.md) bakın.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Azure Depolama hesabını IoT Hub ilişkilendir

Karşıya dosya yükleme işlevini kullanmak için, önce bir Azure Depolama hesabını IoT Hub bağlamanız gerekir. Bu görevi Azure portal veya program aracılığıyla [IoT Hub kaynak sağlayıcısı REST API 'leri](/rest/api/iothub/iothubresource)aracılığıyla tamamlayabilirsiniz. IoT Hub bir Azure Depolama hesabını ilişkilendirdikten sonra, hizmet bir dosya yükleme isteği başlattığında bir cihaza SAS URI 'SI döndürür.

IoT Hub nasıl yapılır [kılavuzlarıyla cihazınızdan buluta dosya yükleme](iot-hub-csharp-csharp-file-upload.md) , dosya yükleme işlemine tam bir anlatım sağlar. Bu nasıl yapılır kılavuzlarında, bir depolama hesabını IoT Hub ile ilişkilendirmek için Azure portal nasıl kullanılacağı gösterilmektedir.

> [!NOTE]
> [Azure IoT SDK 'ları](iot-hub-devguide-sdks.md) , paylaşılan ERIŞIM imzası URI 'sini alma, dosyayı karşıya yükleme ve tamamlanan karşıya yükleme IoT Hub bildirme için otomatik olarak işler. Bir güvenlik duvarı BLOB depolama uç noktasına erişimi engelliyorsa, ancak IoT Hub uç noktasına erişime izin verirse, dosya yükleme işlemi başarısız olur ve IoT C# cihaz SDK 'Sı için aşağıdaki hatayı gösterir:
>
> `---> System.Net.Http.HttpRequestException: A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond`
>
> Karşıya dosya yükleme özelliğinin çalışması için, hem IoT Hub uç noktası hem de BLOB depolama uç noktası erişimine cihaz için kullanılabilir olmalıdır.
> 


## <a name="initialize-a-file-upload"></a>Karşıya dosya yüklemeyi Başlat
IoT Hub, özellikle cihazların bir dosyayı karşıya yüklemek için bir SAS URI 'SI istemesine yönelik bir uç nokta içerir. Dosya karşıya yükleme işlemini başlatmak için, cihaz `{iot hub}.azure-devices.net/devices/{deviceId}/files` AŞAĞıDAKI JSON gövdesiyle öğesine BIR post isteği gönderir:

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT Hub, cihazın dosyayı karşıya yüklemek için kullandığı aşağıdaki verileri döndürür:

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Kullanım dışı: bir dosya yüklemeyi GET ile başlatma

> [!NOTE]
> Bu bölümde, IoT Hub bir SAS URI 'SI alma için kullanım dışı bırakılan işlevsellik açıklanmaktadır. Daha önce açıklanan POST metodunu kullanın.

IoT Hub dosya yüklemeyi desteklemek için iki REST uç noktası vardır, biri depolama için SAS URI 'sini almak ve diğeri ise tamamlanan bir karşıya yüklemenin IoT Hub 'ına bildirimde bulunur. Cihaz, konumundaki IoT Hub 'ına bir GET göndererek dosya yükleme işlemini başlatır `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}` . IoT Hub 'ı şunu döndürür:

* Karşıya yüklenecek dosyaya özel bir SAS URI 'SI.

* Karşıya yükleme tamamlandıktan sonra kullanılacak bağıntı KIMLIĞI.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Tamamlanmış bir dosyayı karşıya yükleme IoT Hub bildirme

Cihaz, Azure Storage SDK 'larını kullanarak dosyayı depolamaya yükler. Karşıya yükleme tamamlandığında, cihaz `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` AŞAĞıDAKI JSON gövdesiyle öğesine BIR post isteği gönderir:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

Değeri, `isSuccess` dosyanın başarıyla karşıya yüklenip yüklenmediğini belirten bir Boole değeridir. İçin durum kodu, `statusCode` dosyanın depolamaya yüklemesinin durumudur ve `statusDescription` öğesine karşılık gelir `statusCode` .

## <a name="reference-topics"></a>Başvuru konuları:

Aşağıdaki başvuru konuları, bir cihazdan dosya yükleme hakkında daha fazla bilgi sağlar.

## <a name="file-upload-notifications"></a>Karşıya dosya yükleme bildirimleri

İsteğe bağlı olarak, bir cihaz karşıya yükleme işleminin tamamlandığını IoT Hub bildiren bir bildirim iletisi üretir IoT Hub. Bu ileti, dosyanın adını ve depolama konumunu içerir.

[Uç noktalar](iot-hub-devguide-endpoints.md)bölümünde açıklandığı gibi, IoT Hub, hizmet 'e yönelik bir uç nokta (**/ileti/servicebound/fileuploadnotifications**) üzerinden ileti olarak dosya yükleme bildirimleri sağlar. Karşıya dosya yükleme bildirimlerinin alma semantiği, buluttan cihaza iletilerle aynıdır ve aynı [ileti yaşam döngüsüne](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-life-cycle)sahiptir. Karşıya dosya yükleme bildirim uç noktasından alınan her ileti, aşağıdaki özelliklere sahip bir JSON kaydıdır:

| Özellik | Açıklama |
| --- | --- |
| EnqueuedTimeUtc |Bildirimin ne zaman oluşturulduğunu gösteren zaman damgası. |
| DeviceId |Dosyayı karşıya yükleyen cihazın **DeviceID** dosyası. |
| BlobUri |Karşıya yüklenen dosyanın URI 'SI. |
| BlobName |Karşıya yüklenen dosyanın adı. |
| LastUpdatedTime |Dosyanın en son ne zaman güncelleştirildiğini gösteren zaman damgası. |
| Blobsizeınbytes |Karşıya yüklenen dosyanın boyutu. |

**Örnek**. Bu örnekte bir dosya karşıya yükleme bildirim iletisinin gövdesi gösterilmektedir.

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>Karşıya dosya yükleme bildirimi yapılandırma seçenekleri

Her IoT Hub 'ı, karşıya dosya yükleme bildirimleri için aşağıdaki yapılandırma seçeneklerine sahiptir:

| Özellik | Açıklama | Aralık ve varsayılan |
| --- | --- | --- |
| **enableFileUploadNotifications** |Dosya yükleme bildirimlerinin dosya bildirimleri uç noktasına yazılıp yazılmadığını denetler. |Bool. Varsayılan: true. |
| **fileNotifications. ttlAsIso8601** |Dosya yükleme bildirimleri için varsayılan TTL. |Aralığı 48H 'ye (en az 1 dakika) kadar ISO_8601. Varsayılan: 1 saat. |
| **fileNotifications. lockDuration** |Dosya karşıya yükleme bildirimleri kuyruğu için kilit süresi. |5-300 saniye (en az 5 saniye). Varsayılan: 60 saniye. |
| **fileNotifications. maxDeliveryCount** |Dosya karşıya yükleme bildirim kuyruğu için en fazla teslimat sayısı. |1 ile 100 arasında. Varsayılan: 100. |

Bu özellikleri IoT Hub 'ınızda Azure portal, Azure CLı veya PowerShell kullanarak ayarlayabilirsiniz. Nasıl yapılacağını öğrenmek için [karşıya dosya yükleme yapılandırma](iot-hub-configure-file-upload.md)altındaki konulara bakın.

## <a name="additional-reference-material"></a>Ek başvuru malzemeleri

IoT Hub geliştirici kılavuzundaki diğer başvuru konuları şunları içerir:

* [IoT Hub uç noktaları](iot-hub-devguide-endpoints.md) , çalışma zamanı ve yönetim işlemleri Için çeşitli IoT Hub uç noktalarını açıklar.

* [Daraltma ve Kotalar](iot-hub-devguide-quotas-throttling.md) , IoT Hub hizmetine uygulanan kotaları ve azaltma davranışlarını açıklar.

* [Azure IoT cihaz ve hizmet SDK 'ları](iot-hub-devguide-sdks.md) , IoT Hub etkileşimde bulunan cihaz ve hizmet uygulamaları geliştirirken kullanabileceğiniz çeşitli dil SDK 'larını listeler.

* [IoT Hub sorgu dili](iot-hub-devguide-query-language.md) , cihaz WINS ve işleriniz hakkında IoT Hub bilgi almak için kullanabileceğiniz sorgu dilini açıklar.

* [MQTT desteği IoT Hub](iot-hub-mqtt-support.md) MQTT protokolü için IoT Hub desteği hakkında daha fazla bilgi sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Artık IoT Hub kullanarak cihazlardan dosya yüklemeyi öğrendiniz, aşağıdaki IoT Hub Geliştirici Kılavuzu konularıyla ilgileniyor olabilirsiniz:

* [IoT Hub cihaz kimliklerini yönetme](iot-hub-devguide-identity-registry.md)

* [IoT Hub’a erişimi denetleme](iot-hub-devguide-security.md)

* [Durum ve yapılandırmaların eşitlenmesi için cihaz ikizlerini kullanma](iot-hub-devguide-device-twins.md)

* [Bir cihazda doğrudan yöntem çağırma](iot-hub-devguide-direct-methods.md)

* [Birden fazla cihazda işleri zamanlama](iot-hub-devguide-jobs.md)

Bu makalede açıklanan kavramların bazılarını denemek için aşağıdaki IoT Hub öğreticisine bakın:

* [IoT Hub ile cihazdan buluta dosya yükleme](iot-hub-csharp-csharp-file-upload.md)
