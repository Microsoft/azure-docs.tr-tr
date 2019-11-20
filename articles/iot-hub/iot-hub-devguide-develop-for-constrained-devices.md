---
title: Azure IoT Hub IoT Hub C SDK kullanarak kısıtlı cihazlar için geliştirme
description: Geliştirici Kılavuzu-kısıtlı cihazlar için Azure IoT SDK 'larını kullanarak geliştirme hakkında rehberlik.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: robinsh
ms.openlocfilehash: a1918a99efcdcc5764140093ad422f7887ca3c88
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954691"
---
# <a name="develop-for-constrained-devices-using-azure-iot-c-sdk"></a>Azure IoT C SDK kullanarak kısıtlı cihazlar için geliştirme

Azure IoT Hub C SDK 'Sı ANSI C 'de yazılmıştır (C99) ve bu da küçük disk ve bellek parmak izine sahip çeşitli platformlar çalışmaya uygun hale getirir. Önerilen RAM en az 64 KB 'tır, ancak tam bellek parmak izi kullanılan protokole, açılan bağlantı sayısına ve hedeflenen platforma bağlıdır.
> [!NOTE]
> * Azure IoT C SDK, geliştirmeye yardımcı olmak için kaynak tüketim bilgilerini düzenli olarak yayımlar.  Lütfen [GitHub deponuzu](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md) ziyaret edin ve en son kıyaslama bölümünü gözden geçirin.
>

C SDK, apt-get, NuGet ve MBED 'den paket biçiminde kullanılabilir. Kısıtlanmış cihazları hedeflemek için, hedef platformunuz için SDK 'Yı yerel olarak oluşturmak isteyebilirsiniz. Bu belgelerde [CMake](https://cmake.org/)kullanarak C SDK 'sının parmak izini daraltmak için bazı özelliklerin nasıl kaldırılacağı gösterilmektedir. Ayrıca, bu belgede kısıtlanmış cihazlarla çalışmaya yönelik en iyi yöntem programlama modelleri ele alınmaktadır.

## <a name="building-the-c-sdk-for-constrained-devices"></a>Kısıtlanmış cihazlar için C SDK 'Sı oluşturma

Kısıtlanmış cihazlar için C SDK 'Yı derleyin.

### <a name="prerequisites"></a>Önkoşullar

Geliştirme ortamınızı C SDK 'Sı oluşturmaya hazırlamak için bu [c SDK 'sı kurulum kılavuzunu](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) izleyin. CMake ile oluşturmaya yönelik adıma geçmeden önce, kullanılmayan özellikleri kaldırmak için CMake bayraklarını çağırabilirsiniz.

### <a name="remove-additional-protocol-libraries"></a>Ek protokol kitaplıklarını kaldır

C SDK Şu anda beş protokolü destekler: MQTT, WebSocket üzerinden MQTT, AMQPs, AMQP, WebSocket üzerinden ve HTTPS. Çoğu senaryo, bir istemcide çalışan bir veya iki protokolde olmasını gerektirir, bu nedenle, SDK 'dan kullanmadığınız protokol kitaplığını kaldırabilirsiniz. Senaryolarınız için uygun iletişim protokolünü seçme hakkında ek bilgiler, [bir IoT Hub iletişim protokolü seçin](iot-hub-devguide-protocols.md)bölümünde bulunabilir. Örneğin MQTT, kısıtlanmış cihazlara genellikle daha uygun olan hafif bir protokoldür.

AMQP ve HTTP kitaplıklarını aşağıdaki CMake komutunu kullanarak kaldırabilirsiniz:

```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

### <a name="remove-sdk-logging-capability"></a>SDK günlüğü özelliğini kaldır

C SDK, hata ayıklamaya yardımcı olmak için kapsamlı günlük kaydı sağlar. Aşağıdaki CMake komutunu kullanarak üretim cihazları için günlüğe kaydetme özelliğini kaldırabilirsiniz:

```
cmake -Dno_logging=OFF <Path_to_cmake>
```

### <a name="remove-upload-to-blob-capability"></a>Blob özelliğine karşıya yüklemeyi kaldır

SDK 'daki yerleşik özelliği kullanarak, büyük dosyaları Azure depolama 'ya yükleyebilirsiniz. Azure IoT Hub, ilişkili bir Azure depolama hesabına dağıtıcı görevi görür. Bu özelliği medya dosyaları, büyük telemetri toplu işleri ve Günlükler göndermek için kullanabilirsiniz. [IoT Hub ile karşıya dosya yükleme konusunda](iot-hub-devguide-file-upload.md)daha fazla bilgi edinebilirsiniz. Uygulamanız bu işlevi gerektirmiyorsa, aşağıdaki CMake komutunu kullanarak bu özelliği kaldırabilirsiniz:

```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```

### <a name="running-strip-on-linux-environment"></a>Linux ortamında şerit çalıştırma

İkili dosyalarınız Linux sisteminde çalışıyorsa, derleme işleminden sonra son uygulamanın boyutunu azaltmak için [Strip komutundan](https://en.wikipedia.org/wiki/Strip_(Unix)) yararlanabilirsiniz.

```
strip -s <Path_to_executable>
```

## <a name="programming-models-for-constrained-devices"></a>Kısıtlanmış cihazlar için programlama modelleri

Ardından, kısıtlanmış cihazlar için programlama modellerine bakın.

### <a name="avoid-using-the-serializer"></a>Serileştiriciyi kullanmaktan kaçının

C SDK 'sının, yöntemleri ve cihaz ikizi özelliklerini tanımlamak için bildirime dayalı eşleme tabloları kullanmanıza olanak tanıyan, isteğe bağlı bir [C SDK serileştiricisi](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer)vardır. Serileştirici, geliştirmeyi basitleştirmek için tasarlanmıştır, ancak kısıtlı cihazlar için ideal olmayan ek yük ekler. Bu durumda, ilkel istemci API 'Leri kullanmayı ve [Parson](https://github.com/kgabis/parson)gibi basit bir AYRıŞTıRıCıSı kullanarak JSON 'u ayrıştırmayı düşünün.

### <a name="use-the-lower-layer-_ll_"></a>Alt katmanı (_ll_) kullanma

C SDK iki programlama modelini destekler. Bir küme, daha düşük katmanı temsil eden bir _ll_ /düzeltmesini Içeren API 'lere sahiptir. Bu API kümesi daha açık ağırlıklardır ve çalışan iş parçacıklarını kullanmaz; bu da kullanıcının zamanlamayı el ile denetleyebileceği anlamına gelir. Örneğin, cihaz istemcisi için _ll_ API 'leri bu [üstbilgi dosyasında](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h)bulunabilir. 

_Ll_ dizinine sahip olmayan başka bir API kümesine, bir çalışan iş parçacığının otomatik olarak ayarlandığı kolay bir katman denir. Örneğin, cihaz istemcisi için uygun katman API 'Leri bu [IoT cihazı istemci üst bilgi dosyasında](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h)bulunabilir. Her bir ek iş parçacığının sistem kaynaklarının önemli bir yüzdesi alabildiği kısıtlı cihazlarda, _ll_ API 'lerini kullanmayı düşünün.

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT C SDK mimarisi hakkında daha fazla bilgi edinmek için:
-   [Azure IoT C SDK kaynak kodu](https://github.com/Azure/azure-iot-sdk-c/)
-   [C için Azure IoT cihaz SDK 'Sı tanıtımı](iot-hub-device-sdk-c-intro.md)
