---
title: Güvenilir Mesajlaşma w/cihaz SDK 'Ları & IoT Hub bağlantısını yönetme
description: Azure IoT Hub cihaz SDK 'larını kullanırken cihaz bağlantınızın ve iletilerinizin nasıl iyileştireceğinizi öğrenin
services: iot-hub
author: robinsh
ms.author: robinsh
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
ms.openlocfilehash: 8774129b3a1d3c9a1095e7a7c478dd94086b5867
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954488"
---
# <a name="manage-connectivity-and-reliable-messaging-by-using-azure-iot-hub-device-sdks"></a>Azure IoT Hub cihaz SDK 'larını kullanarak bağlantı ve güvenilir mesajlaşma yönetimi

Bu makalede daha dayanıklı olan cihaz uygulamaları tasarlamanıza yardımcı olacak üst düzey rehberlik sunulmaktadır. Azure IoT cihaz SDK 'larının bağlantı ve güvenilir mesajlaşma özelliklerinden nasıl yararlanabilmeniz gösterilmektedir. Bu kılavuzun amacı, aşağıdaki senaryoları yönetmenize yardımcı olmaktır:

* Bırakılan bir ağ bağlantısı düzeltiliyor

* Farklı ağ bağlantıları arasında geçiş yapma

* Hizmet geçici bağlantı hataları nedeniyle yeniden bağlanıyor

Uygulama ayrıntıları dile göre farklılık gösterebilir. Daha fazla bilgi için bkz. API belgeleri veya belirli SDK:

* [C/iOS SDK 'Sı](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Düğüm SDK 'Sı](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python SDK](https://github.com/Azure/azure-iot-sdk-python) (henüz güvenilirlik uygulanmadı)

## <a name="designing-for-resiliency"></a>Dayanıklı olacak şekilde tasarlama

IoT cihazları genellikle sürekli olmayan veya kararsız ağ bağlantılarına bağlıdır (örneğin, GSM veya uydu). Cihazlar, aralıklı hizmet kullanılabilirliği ve altyapı düzeyi veya geçici hatalar nedeniyle bulut tabanlı hizmetlerle etkileşim kurarken hatalar oluşabilir. Cihazda çalışan bir uygulamanın bağlantı, yeniden bağlanma mekanizmalarını ve ileti göndermek ve almak için yeniden deneme mantığı yönetmesi gerekir. Ayrıca, yeniden deneme stratejisi gereksinimleri cihazın IoT senaryosuna, içeriğine ve özelliklerine göre büyük ölçüde değişir.

Azure IoT Hub cihaz SDK 'Ları, buluttan cihaza ve cihazdan buluta bağlanmayı ve iletişim kurmasını basitleştirecek şekilde hedeflenir. Bu SDK 'lar, Azure IoT Hub bağlanmak için güçlü bir yol ve ileti göndermek ve almak için kapsamlı bir seçenek kümesi sağlar. Geliştiriciler ayrıca, belirli bir senaryo için daha iyi bir yeniden deneme stratejisi özelleştirmek üzere mevcut uygulamayı değiştirebilir.

Bağlantı ve güvenilir mesajlaşma 'Yı destekleyen ilgili SDK özellikleri aşağıdaki bölümlerde ele alınmıştır.

## <a name="connection-and-retry"></a>Bağlantı ve yeniden deneme

Bu bölüm, bağlantıları yönetirken yeniden bağlantı ve yeniden deneme desenlerine genel bir bakış sunar. Cihaz uygulamanızda farklı bir yeniden deneme ilkesi kullanma ve cihaz SDK 'larından ilgili API 'Leri listelemede uygulama kılavuzu ayrıntılarını vermektedir.

### <a name="error-patterns"></a>Hata desenleri

Bağlantı arızaları birçok düzeyde olabilir:

* Ağ hataları: bağlantısı kesilen yuva ve ad çözümlemesi hataları

* HTTP, AMQP ve MQTT taşıması için protokol düzeyi hataları: ayrılmış bağlantılar veya süre sonu oturumları

* Yerel hatalardan kaynaklanan uygulama düzeyi hataları: geçersiz kimlik bilgileri veya hizmet davranışı (örneğin, kotayı aşma veya daraltma)

Cihaz SDK 'Ları, her üç düzeydeki hataları algılar. İşletim sistemi ile ilgili hatalar ve donanım hataları, cihaz SDK 'Ları tarafından algılanmaz ve işlenmez. SDK tasarımı, Azure Mimari Merkezi [geçici hata Işleme kılavuzlarını](/azure/architecture/best-practices/transient-faults#general-guidelines) temel alır.

### <a name="retry-patterns"></a>Yeniden deneme desenleri

Aşağıdaki adımlar bağlantı hataları algılandığında yeniden deneme işlemini anlatmaktadır:

1. SDK hatayı ve ağ, protokol veya uygulamadaki ilişkili hatayı algılar.

2. SDK hata türünü belirlemek için hata filtresini kullanır ve yeniden deneme gerekli olup olmadığına karar verir.

3. SDK **kurtarılamaz bir hata**tanımlarsa bağlantı, gönderme ve alma gibi işlemler durdurulur. SDK, kullanıcıya bildirir. Kurtarılamaz hatalara örnek olarak bir kimlik doğrulama hatası ve hatalı bir uç nokta hatası verilebilir.

4. SDK **kurtarılabilir bir hata**tanımlarsa, tanımlanan zaman aşımı sona erdiğinde belirtilen yeniden deneme ilkesine göre yeniden dener.  SDK 'nın varsayılan olarak değişim yeniden deneme ilkesi **Ile üstel geri kapatmayı** kullandığını unutmayın.
5. Tanımlı zaman aşımı süresi dolduğunda, SDK bağlanmayı veya gönderilmesini denemeyi sonlandırır. Kullanıcıya bildirir.

6. SDK, kullanıcının bağlantı durumu değişikliklerini almak için bir geri çağırma eklemesine izin verir.

SDK 'lar üç yeniden deneme ilkesi sağlar:

* **Değişim Ile üstel geri dönüş**: Bu varsayılan yeniden deneme İlkesi başlangıçta agresif ve en yüksek gecikmeye kadar zaman içinde yavaşlıyor. Tasarım, [Azure mimari merkezi yeniden deneme](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific)yönergelerine dayalıdır. 

* **Özel yeniden deneme**: bazı SDK dilleri için, senaryonuza daha uygun olan özel bir yeniden deneme ilkesi tasarlayabilmeniz ve sonra bunu retrypolicy 'e ekleyebilmeniz gerekir. Özel yeniden deneme C SDK 'da kullanılamaz.

* **Yeniden deneme yok**: yeniden deneme mantığını devre dışı bırakan "yeniden deneme yok" olarak yeniden deneme ilkesini ayarlayabilirsiniz. SDK bir kez bağlanmaya çalışır ve bağlantının kurulduğu varsayılarak bir kez ileti gönderir. Bu ilke genellikle bant genişliği veya maliyet sorunları olan senaryolarda kullanılır. Bu seçeneği belirlerseniz, göndermeyecek iletiler kaybolur ve kurtarılamaz.

### <a name="retry-policy-apis"></a>İlke API 'Lerini yeniden dene

   | SDK | SetRetryPolicy yöntemi | İlke uygulamaları | Uygulama kılavuzu |
   |-----|----------------------|--|--|
   |  C/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Varsayılan**: [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Özel:** kullanılabilir [retrypolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies) kullanın<BR>**Yeniden deneme yok:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [C/iOS uygulama](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.deviceclientconfig.setretrypolicy?view=azure-java-stable)        | **Varsayılan**: [üs albackoffwithdeğişim sınıfı](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Özel:** [retrypolicy arabirimini](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java) Uygula<BR>**Yeniden deneme yok:** [NoRetry sınıfı](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Java uygulama](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |
   | .NET| [DeviceClient. SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet) | **Varsayılan**: [üs albackoff sınıfı](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**Özel:** [ıretrypolicy arabirimini](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet) Uygula<BR>**Yeniden deneme yok:** [NoRetry sınıfı](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [C#paylaşır](https://github.com/Azure/azure-iot-sdk-csharp) | |
   | Node| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest) | **Varsayılan**: [üs albackoffwithdeğişim sınıfı](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**Özel:** [retrypolicy arabirimini](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest) Uygula<BR>**Yeniden deneme yok:** [NoRetry sınıfı](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Düğüm uygulama](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |
   | Python| Çok yakında | Çok yakında | Çok yakında

Aşağıdaki kod örnekleri bu akışı göstermektedir:

#### <a name="net-implementation-guidance"></a>.NET Uygulama Kılavuzu

Aşağıdaki kod örneği, varsayılan yeniden deneme ilkesinin nasıl tanımlanacağını ve ayarlanacağını göstermektedir:

   ```csharp
   // define/set default retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

Yüksek CPU kullanımını önlemek için, kod hemen başarısız olursa yeniden denemeler azaltılır. Örneğin, hedefe ağ veya yol olmadığında. Bir sonraki yeniden denemeden yürütülecek en kısa süre 1 saniyedir.

Hizmet bir azaltma hatasıyla yanıt verirse, yeniden deneme ilkesi farklıdır ve ortak API aracılığıyla değiştirilemez:

   ```csharp
   // throttled retry policy
   IRetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), 
     TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5)); SetRetryPolicy(retryPolicy);
   ```

Yeniden deneme mekanizması, şu anda 4 dakika olarak ayarlanan `DefaultOperationTimeoutInMilliseconds`sonra duraklar.

#### <a name="other-languages-implementation-guidance"></a>Diğer diller uygulama kılavuzu

Diğer dillerdeki kod örnekleri için aşağıdaki uygulama belgelerini gözden geçirin. Depo, yeniden deneme ilkesi API 'lerinin kullanımını gösteren örnekler içerir.

* [C/iOS SDK 'Sı](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Düğüm SDK 'Sı](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

* [Python SDK'sı](https://github.com/Azure/azure-iot-sdk-python)

## <a name="next-steps"></a>Sonraki adımlar

* [Cihaz ve hizmet SDK’ları kullanma](./iot-hub-devguide-sdks.md)

* [C için IoT cihaz SDK'sını kullanma](./iot-hub-device-sdk-c-intro.md)

* [Kısıtlanmış cihazlar için geliştirme](./iot-hub-devguide-develop-for-constrained-devices.md)

* [Mobil cihazlar için geliştirme](./iot-hub-how-to-develop-for-mobile-devices.md)

* [Cihaz bağlantısı kesilme sorunlarını giderme](iot-hub-troubleshoot-connectivity.md)
