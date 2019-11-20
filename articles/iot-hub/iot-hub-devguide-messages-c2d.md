---
title: Azure IoT Hub buluttan cihaza mesajlaşma iletilerini anlama | Microsoft Docs
description: Bu geliştirici kılavuzunda, IoT Hub 'ınız ile buluttan cihaza mesajlaşma 'nın nasıl kullanılacağı açıklanmaktadır. İleti yaşam döngüsü ve yapılandırma seçenekleri hakkında bilgi içerir.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: d4a51a44b48e94669e92a9d525c1b0966df53c18
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68964127"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>IoT Hub 'ından buluttan cihaza iletileri gönderme

Çözüm arka ucundan bir cihaz uygulamasına tek yönlü bildirimler göndermek için IoT Hub 'ınızdan buluta cihaz iletileri gönderin. Azure IoT Hub tarafından desteklenen diğer buluttan cihaza yönelik seçeneklere yönelik bir tartışma için bkz. [buluttan cihaza iletişim Kılavuzu](iot-hub-devguide-c2d-guidance.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Hizmet temelli bir uç nokta ( */ileti/devicebağlı*) aracılığıyla buluttan cihaza iletiler gönderirsiniz. Bir cihaz daha sonra iletileri cihaza özgü bir uç nokta, */Devices/{deviceid}/ileti/devicelimit*aracılığıyla alır.

Tek bir cihazda her buluttan cihaza bir iletiyi hedeflemek için, IoT Hub 'ınız **to** özelliğini */Devices/{deviceid}/ileti/devicebağlanacak*olarak ayarlar.

Her cihaz kuyruğu, en çok 50 buluttan cihaza iletileri barındırır. Aynı cihaza daha fazla ileti gönderilmesini denemek için bir hatayla sonuçlanır.

## <a name="the-cloud-to-device-message-life-cycle"></a>Buluttan cihaza ileti yaşam döngüsü

En az bir kez ileti teslimi sağlamak için, IoT Hub 'ınız cihaz başına kuyruklarda buluttan cihaza iletileri sürdürür. IoT Hub 'ının iletileri kuyruktan kaldırması için, cihazların açıkça *tamamlanmasını*kabul etmesi gerekir. Bu yaklaşım, bağlantı ve cihaz hatalarıyla dayanıklılık sağlar.

Yaşam döngüsü durumu grafiği aşağıdaki diyagramda görüntülenir:

![Buluttan cihaza ileti yaşam döngüsü](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

IoT Hub hizmeti bir cihaza bir ileti gönderdiğinde, hizmet ileti durumunu *sıraya alındı*olarak ayarlar. Bir cihaz bir ileti *almak* Istediğinde, IoT Hub, durumu *görünmez*olarak ayarlayarak iletiyi *kilitler* . Bu durum cihazdaki diğer iş parçacıklarının diğer iletileri almaya başlamasını sağlar. Bir cihaz iş parçacığı bir iletiyi işlemeyi tamamladığında, iletiyi *tamamlayarak* IoT Hub 'ına bildirir. IoT Hub 'ı daha sonra durumu *tamamlandı*olarak ayarlar.

Bir cihaz ayrıca şunları yapabilir:

* IoT Hub 'ının onu *ölü* duruma getirmesine neden olan iletiyi *reddedin* . Message Queuing telemetri taşıma (MQTT) protokolü üzerinden bağlanan cihazlar, buluttan cihaza iletileri reddedemez.

* Durumu *sıraya alındı*olarak ayarlanmış şekilde, IoT Hub 'ının iletiyi sıraya geri yerleştirmesine neden olan iletiyi bırakın. MQTT protokolü üzerinden bağlanan cihazlar, buluttan cihaza iletileri iptal edemiyor.

Bir iş parçacığı, IoT Hub 'ına bildirimde bulunmadan bir iletiyi işleyemeyebilir. Bu durumda, iletiler, *görünürlük* zaman aşımı (veya *kilit* zaman aşımı) sonrasında *görünmeyen* durumdan *sıraya alınan* duruma otomatik olarak geçer. Bu zaman aşımı değeri bir dakikadır ve değiştirilemez.

IoT Hub 'ındaki **Maksimum teslim sayısı** özelliği, bir Iletinin *sıraya alınmış* ve *görünmeyen* durumlar arasında kaç kez geçiş sürebileceğini belirler. Bu kadar geçiş sonrasında IoT Hub, iletinin durumunu *atılacak*duruma göre ayarlar. Benzer şekilde, IoT Hub, bir iletinin durumunu sona erme zamanından sonra *atılacak* şekilde ayarlar. Daha fazla bilgi için bkz. [yaşam süresi](#message-expiration-time-to-live).

[IoT Hub ile buluttan cihaza iletileri gönderme](iot-hub-csharp-csharp-c2d.md) , buluttan cihaza iletileri nasıl gönderirsiniz ve bunları bir cihazda nasıl alacağınızı gösterir.

İleti kaybı uygulama mantığını etkilemediği zaman bir cihaz genellikle buluttan cihaza iletisini tamamlar. Bunun bir örneği, cihaz ileti içeriğini yerel olarak kalıcı hale geldiğinde veya bir işlemi başarıyla yürütülürse olabilir. İleti ayrıca, kaybı uygulamanın işlevselliğini etkilemeyen geçici bilgiler de taşıyabilir. Bazen, uzun süre çalışan görevler için şunları yapabilirsiniz:

* Cihaz, yerel depolamada görev açıklamasını kalıcı olduktan sonra buluttan cihaza iletisini doldurun.

* Görevin ilerlemesinin çeşitli aşamalarında bir veya daha fazla cihazdan buluta ileti ile çözüm arka ucuna bildirim gönderin.

## <a name="message-expiration-time-to-live"></a>İleti süre sonu (yaşam süresi)

Her buluttan cihaza ileti için bir süre sonu vardır. Bu süre, aşağıdakilerden biri tarafından ayarlanır:

* Hizmette **Expiryıtimeutc** özelliği
* IoT Hub özelliği olarak belirtilen varsayılan *yaşam süresi* kullanılarak IoT Hub 'ı

Bkz. [buluttan cihaza yapılandırma seçenekleri](#cloud-to-device-configuration-options).

İleti süresinin dolmasının avantajlarından faydalanmanın ve bağlantısı kesilen cihazlara ileti gönderilmesini önlemek için kullanılan yaygın bir yol, kısa *süre değerini canlı* değerlere ayarlamamaktır. Bu yaklaşım, cihaz bağlantı durumunun korunmasıyla aynı sonuca ulaşır, ancak daha etkilidir. İleti bildirimleri istediğinizde, IoT Hub size hangi cihazların olduğunu bildirir:

* İleti alabiliyor.
* Çevrimiçi değil veya başarısız oldu.

## <a name="message-feedback"></a>İleti geri bildirimi

Buluttan cihaza bir ileti gönderdiğinizde, hizmet iletinin son durumu hakkında ileti başına geri bildirimin teslimini isteyebilir. Bu, aşağıdaki dört değerden birine gönderilen buluttan cihaza iletisindeki **ıothub-ACK** uygulama özelliğini ayarlayarak yapabilirsiniz:

| ACK özelliği değeri | Davranış |
| ------------ | -------- |
| yok     | IoT Hub 'ı bir geri bildirim iletisi oluşturmaz (varsayılan davranış). |
| pozitif | Buluttan cihaza ileti *tamamlandı* durumuna ulaşırsa, IoT Hub bir geri bildirim iletisi oluşturur. |
| negatif | Buluttan cihaza ileti, *kullanılmayan* duruma ulaşırsa, IoT Hub bir geri bildirim iletisi oluşturur. |
| tam     | IoT Hub 'ı her iki durumda da bir geri bildirim iletisi oluşturur. |

**ACK** değeri *doluysa*ve geri bildirim iletisi almazsanız, geri bildirim iletisinin süresi sona ermediği anlamına gelir. Hizmet özgün iletiye ne olduğunu bilmez. Uygulamada, bir hizmet, geri bildirimin süresi dolmadan önce işleyebilmesi gerekir. En uzun süre sonu süresi iki gündür ve bir hata oluşursa hizmetin yeniden çalıştırılması zaman alır.

[Uç noktalar](iot-hub-devguide-endpoints.md)bölümünde açıklandığı gibi, IoT Hub, hizmet 'e yönelik bir uç nokta, */ileti/servicebound/feedback*ile ileti olarak geri bildirimde bulunun. Geri bildirim alma semantiği, buluttan cihaza iletiler için aynıdır. Mümkün olduğunda ileti geri bildirimi, aşağıdaki biçimde tek bir ileti halinde toplu olarak oluşturulur:

| Özellik     | Açıklama |
| ------------ | ----------- |
| EnqueuedTime | Merkez tarafından geri bildirim iletisinin alındığını gösteren zaman damgası |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

Gövde, her biri aşağıdaki özelliklere sahip olan JSON seri hale getirilmiş bir kayıt dizisidir:

| Özellik           | Açıklama |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | İleti sonucunun ne zaman gerçekleştiğini belirten zaman damgası (örneğin, Merkez geri bildirim iletisini aldı veya özgün iletinin zaman aşımına uğradı) |
| Originalmessageıd  | Bu geri bildirim bilgilerinin ilişkili olduğu buluttan cihaza mesajın *MessageID* |
| Durum         | IoT Hub tarafından oluşturulan geri bildirim iletilerinde kullanılan gerekli bir dize: <br/> *Başarılı* <br/> *Aşıldığı* <br/> *Deliverycountexcebaşında* <br/> *Reddedilecek* <br/> *Temizlenir* |
| Açıklama        | *StatusCode* için dize değerleri |
| DeviceId           | Bu geri bildirim parçasının ilişkili olduğu buluttan cihaza yönelik iletinin hedef cihazının *DeviceID* 'i |
| Devicegenerationıd | Bu geri bildirim parçasının ilişkili olduğu buluttan cihaza ileti hedef cihazının *Devicegenerationıd 'si* |

Geri bildirimini özgün iletiyle ilişkilendirmek üzere buluttan cihaza ileti için, hizmet bir *MessageID*belirtmelidir.

Geri bildirim iletisinin gövdesi aşağıdaki kodda gösterilmiştir:

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

**Silinen cihazlar için bekleyen geri bildirimler**

Bir cihaz silindiğinde, bekleyen geri bildirimler de silinir. Cihaz geri bildirimi toplu olarak gönderilir. Bir cihaz, dar pencerede (genellikle 1 saniyeden daha az), cihazın ileti alındığını doğruladığı ve bir sonraki geri bildirim toplu işi hazırlandığında, geri bildirim oluşmaz.

Cihazınızı silmeden önce bekleyen geri bildirimin gelmesi için bir süre bekledikten sonra bu davranışı ele alabilirsiniz. Bir cihaz silindikten sonra ilgili ileti geri bildirimi kaybedilmelidir.

## <a name="cloud-to-device-configuration-options"></a>Buluttan cihaza yapılandırma seçenekleri

Her IoT Hub 'ı, buluttan cihaza mesajlaşma için aşağıdaki yapılandırma seçeneklerini sunar:

| Özellik                  | Açıklama | Aralık ve varsayılan |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | Buluttan cihaza iletiler için varsayılan TTL | 2 güne kadar ISO_8601 aralığı (en az 1 dakika); varsayılanını 1 saat |
| maxDeliveryCount          | Buluttan cihaza cihaz başına kuyruklar için en fazla teslimat sayısı | 1-100; varsayılanını 10 |
| geri bildirim. ttlAsIso8601     | Hizmet ile bağlantılı geri bildirim iletileri için bekletme | 2 güne kadar ISO_8601 aralığı (en az 1 dakika); varsayılanını 1 saat |
| geri bildirim. maxDeliveryCount | Geri bildirim kuyruğu için en fazla teslimat sayısı | 1-100; varsayılanını 100 |

Bu yapılandırma seçeneklerini ayarlama hakkında daha fazla bilgi için bkz. [IoT Hub 'Ları oluşturma](iot-hub-create-through-portal.md).

## <a name="next-steps"></a>Sonraki adımlar

Buluttan cihaza iletileri almak için kullanabileceğiniz SDK 'lar hakkında daha fazla bilgi için bkz. [Azure IoT SDK 'ları](iot-hub-devguide-sdks.md).

Buluttan cihaza iletileri almayı denemek için, [buluttan cihaza gönderim](iot-hub-csharp-csharp-c2d.md) Öğreticisi ' ne bakın.
