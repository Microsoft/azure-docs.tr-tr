---
title: Azure IoT Hub ileti biçimini anlayın | Microsoft Docs
description: Geliştirici Kılavuzu-IoT Hub iletilerinin biçimini ve beklenen içeriğini açıklar.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: asrastog
ms.openlocfilehash: 28537ac2389fbb1ca43ca4014515564bddeba4ce
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69872478"
---
# <a name="create-and-read-iot-hub-messages"></a>IoT Hub iletilerini oluşturma ve okuma

Protokoller genelinde kesintisiz birlikte çalışabilirliği desteklemek için, IoT Hub tüm cihaza yönelik protokoller için ortak bir ileti biçimi tanımlar. Bu ileti biçimi, [cihazdan buluta yönlendirme](iot-hub-devguide-messages-d2c.md) ve [buluttan cihaza](iot-hub-devguide-messages-c2d.md) iletiler için kullanılır. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub, akış mesajlaşma düzenlerini kullanarak cihazdan buluta mesajlaşma uygular. IoT Hub cihazdan buluta iletiler, birden çok okuyucu tarafından okunabilen hizmetten oluşan yüksek hacimlerle ilgili [Service Bus](/azure/service-bus-messaging/) *iletilerden* daha benzer [Event Hubs](/azure/event-hubs/) *olaylardır* .

IoT Hub bir ileti aşağıdakilerden oluşur:

* Aşağıda listelenen *sistem özelliklerinin* önceden belirlenmiş bir kümesi.

* *Uygulama özellikleri*kümesi. İleti gövdesinin serisini kaldırmak gerekmeden uygulamanın tanımlayabilmesine ve erişebileceği dize özelliklerinin bir sözlüğü. IoT Hub bu özellikleri hiçbir şekilde değiştirmez.

* Donuk ikili gövde.

Özellik adları ve değerleri yalnızca ASCII alfasayısal karakterler ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` içerebilir ve https protokolünü kullanarak cihazdan buluta iletiler gönderdiğinizde ya da buluttan cihaza iletiler gönderdiğinizde olabilir.

IoT Hub ile cihazdan buluta mesajlaşma aşağıdaki özelliklere sahiptir:

* Cihazdan buluta iletiler, en fazla yedi güne kadar bir IoT Hub 'ının varsayılan **iletiler/olaylar** uç noktasında dayanıklı ve korunur.

* Cihazdan buluta iletiler en çok 256 KB olabilir ve göndermeleri iyileştirmek için toplu olarak gruplandırılabilir. Toplu işlemler en fazla 256 KB olabilir.

* IoT Hub rastgele bölümlemeye izin vermez. Cihazdan buluta iletiler, başlangıçtaki **DeviceID**'lerine göre bölümlendirilir.

* [IoT Hub erişimi denetleme](iot-hub-devguide-security.md)bölümünde açıklandığı gibi, IoT Hub cihaz başına kimlik doğrulaması ve erişim denetimi sağlar.

* İletileri, uygulama özelliklerine giden bilgilerle damgalamak isteyebilirsiniz. Daha fazla bilgi için lütfen bkz. [ileti zenginleştirme](iot-hub-message-enrichments-overview.md).

Farklı protokoller kullanılarak gönderilen iletilerin kodlanması ve kodunun çözülmesi hakkında daha fazla bilgi için bkz. [Azure IoT SDK 'ları](iot-hub-devguide-sdks.md).

## <a name="system-properties-of-d2c-iot-hub-messages"></a>**D2C** IoT Hub Iletilerinin sistem özellikleri

| Özellik | Açıklama  |Kullanıcı ayarlanabilir mi?|İçin anahtar sözcük </br>Yönlendirme sorgusu|
| --- | --- | --- | --- |
| ileti kimliği |İstek-yanıt desenleri için kullanılan ileti için Kullanıcı tarafından ayarlanabilir bir tanımlayıcı. Biçim: ASCII 7 bit alfasayısal karakter + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`, büyük/küçük harfe duyarlı bir dize (en fazla 128 karakter uzunluğunda).  | Evet | Ileti |
| ıothub-enqueuedtime |[Cihazdan buluta](iot-hub-devguide-d2c-guidance.md) mesajın IoT Hub tarafından alındığı tarih ve saat. | Hayır | enqueuedTime |
| Kullanıcı kimliği |İletilerin kaynağını belirtmek için kullanılan bir KIMLIK. İletiler IoT Hub tarafından oluşturulduğunda, olarak `{iot hub name}`ayarlanır. | Evet | userId |
| ıothub-bağlantı-cihaz kimliği |Cihazdan buluta iletilerde IoT Hub tarafından ayarlanan bir KIMLIK. İletiyi gönderen cihazın **DeviceID** 'sini içerir. | Hayır | Connectiondeviceıd |
| ıothub-Connection-Module-ID |Cihazdan buluta iletilerde IoT Hub tarafından ayarlanan bir KIMLIK. İletiyi gönderen cihazın **ModuleID** 'sini içerir. | Hayır | Connectionmoduleıd |
| ıothub-bağlantı-auth-Generation-ID |Cihazdan buluta iletilerde IoT Hub tarafından ayarlanan bir KIMLIK. İletiyi gönderen cihazın **Connectiondevicegenerationıd** 'Sini ( [cihaz kimliği başına kimlik özellikleri](iot-hub-devguide-identity-registry.md#device-identity-properties)) içerir. | Hayır |Connectiondevicegenerationıd |
| ıothub-Connection-auth-yöntemi |Cihazdan buluta iletilerde IoT Hub tarafından ayarlanan bir kimlik doğrulama yöntemi. Bu özellik, iletiyi gönderen cihazın kimliğini doğrulamak için kullanılan kimlik doğrulama yöntemi hakkındaki bilgileri içerir.| Hayır | connectionAuthMethod |

## <a name="system-properties-of-c2d-iot-hub-messages"></a>**C2D** IoT Hub Iletilerinin sistem özellikleri

| Özellik | Açıklama  |Kullanıcı ayarlanabilir mi?|
| --- | --- | --- |
| ileti kimliği |İstek-yanıt desenleri için kullanılan ileti için Kullanıcı tarafından ayarlanabilir bir tanımlayıcı. Biçim: ASCII 7 bit alfasayısal karakter + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`, büyük/küçük harfe duyarlı bir dize (en fazla 128 karakter uzunluğunda).  |Evet|
| sıra numarası |Her buluttan cihaza ileti için IoT Hub tarafından atanan bir sayı (cihaz kuyruğu başına benzersiz). |Hayır|
| to |[Buluttan cihaza](iot-hub-devguide-c2d-guidance.md) iletilerde belirtilen hedef. |Hayır|
| mutlak-süre sonu |İleti süresinin dolma tarihi ve saati. |Hayır|   |
| bağıntı kimliği |İstek-yanıt desenlerinde, genellikle isteğin MessageID ' i içeren bir yanıt iletisindeki dize özelliği. |Evet|
| Kullanıcı kimliği |İletilerin kaynağını belirtmek için kullanılan bir KIMLIK. İletiler IoT Hub tarafından oluşturulduğunda, olarak `{iot hub name}`ayarlanır. |Evet|
| ıothub-ACK |Geri bildirim iletisi Oluşturucu. Bu özellik, cihaz tarafından ileti tüketimine yol açacak şekilde geri bildirim iletileri oluşturmak için IoT Hub istemek üzere buluttan cihaza iletimekte kullanılır. Olası değerler: **hiçbiri** (varsayılan): hiçbir geri bildirim iletisi oluşturulmaz, **pozitif**: ileti tamamlandıysa geri bildirim iletisi alın (veya enfazla teslimat sayısı ya da **tam**: hem pozitif hem de negatif olan cihaz tarafından tamamlanmadan, |Evet|

## <a name="message-size"></a>İleti boyutu

IoT Hub ileti boyutunu, yalnızca gerçek yükü göz önünde bulundurarak, protokol belirsiz bir şekilde ölçer. Bayt cinsinden boyut, aşağıdaki değerlerin toplamı olarak hesaplanır:

* Gövde boyutu (bayt cinsinden).
* İleti sistemi özelliklerinin tüm değerlerinin bayt cinsinden boyutu.
* Tüm Kullanıcı özelliği adlarının ve değerlerinin bayt cinsinden boyutu.

Özellik adları ve değerler ASCII karakterleriyle sınırlandırılmıştır, bu nedenle dizelerin uzunluğu bayt cinsinden boyuta eşittir.

## <a name="anti-spoofing-properties"></a>Sahtekarlığı önleme özellikleri

Cihazdan buluta iletilerde cihaz yanıltmasını önlemek için, aşağıdaki özelliklere sahip tüm iletileri damgalar IoT Hub:

* **ıothub-bağlantı-cihaz kimliği**
* **ıothub-bağlantı-auth-Generation-ID**
* **ıothub-Connection-auth-yöntemi**

İlk ikisi, [cihaz kimliği özelliklerine](iot-hub-devguide-identity-registry.md#device-identity-properties)göre kaynak cihazın **DeviceID** ve **GenerationID** 'sini içerir.

**Iothub-Connection-auth-Method** özelliği, aşağıdaki özelliklere sahıp bir JSON serileştirilmiş nesne içerir:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Sonraki adımlar

* IoT Hub ileti boyutu sınırları hakkında daha fazla bilgi için bkz. [IoT Hub kotalar ve azaltma](iot-hub-devguide-quotas-throttling.md).

* Çeşitli programlama dillerinde IoT Hub iletileri oluşturmayı ve okumayı öğrenmek için bkz. [hızlı başlangıç](quickstart-send-telemetry-node.md).
