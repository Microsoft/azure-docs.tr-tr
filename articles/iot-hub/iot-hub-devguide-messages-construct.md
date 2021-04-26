---
title: Azure IoT Hub ileti biçimini anlayın | Microsoft Docs
description: Geliştirici Kılavuzu-IoT Hub iletilerinin biçimini ve beklenen içeriğini açıklar.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: asrastog
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 21f22f9aa31210b1690d0be562643d94901ce58a
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106079055"
---
# <a name="create-and-read-iot-hub-messages"></a>IoT Hub iletilerini oluşturma ve okuma

Protokoller genelinde kesintisiz birlikte çalışabilirliği desteklemek için, IoT Hub tüm cihaza yönelik protokoller için ortak bir ileti biçimi tanımlar. Bu ileti biçimi, [cihazdan buluta yönlendirme](iot-hub-devguide-messages-d2c.md) ve [buluttan cihaza](iot-hub-devguide-messages-c2d.md) iletiler için kullanılır. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub, akış mesajlaşma düzenlerini kullanarak cihazdan buluta mesajlaşma uygular. IoT Hub cihazdan buluta iletiler, birden çok okuyucu tarafından okunabilen hizmetten oluşan yüksek hacimlerle ilgili [Service Bus](../service-bus-messaging/index.yml) *iletilerden* daha benzer [Event Hubs](../event-hubs/index.yml) *olaylardır* .

IoT Hub bir ileti aşağıdakilerden oluşur:

* Aşağıda listelenen *sistem özelliklerinin* önceden belirlenmiş bir kümesi.

* *Uygulama özellikleri* kümesi. İleti gövdesinin serisini kaldırmak gerekmeden uygulamanın tanımlayabilmesine ve erişebileceği dize özelliklerinin bir sözlüğü. IoT Hub bu özellikleri hiçbir şekilde değiştirmez.

* Donuk ikili gövde.

Özellik adları ve değerleri yalnızca ASCII alfasayısal karakterler içerebilir ve ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` https protokolünü kullanarak cihazdan buluta iletiler gönderdiğinizde ya da buluttan cihaza iletiler gönderdiğinizde olabilir.

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
| ileti kimliği |İstek-yanıt desenleri için kullanılan ileti için Kullanıcı tarafından ayarlanabilir bir tanımlayıcı. Biçim: büyük/küçük harfe duyarlı bir dize (en fazla 128 karakter uzunluğunda) ASCII 7 bit alfasayısal karakter + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}` .  | Yes | Ileti |
| ıothub-enqueuedtime |[Cihazdan buluta](iot-hub-devguide-d2c-guidance.md) mesajın IoT Hub tarafından alındığı tarih ve saat. | No | enqueuedTime |
| user-id |İletilerin kaynağını belirtmek için kullanılan bir KIMLIK. İletiler IoT Hub tarafından oluşturulduğunda, olarak ayarlanır `{iot hub name}` . | Yes | userId |
| ıothub-bağlantı-cihaz kimliği |Cihazdan buluta iletilerde IoT Hub tarafından ayarlanan bir KIMLIK. İletiyi gönderen cihazın **DeviceID** 'sini içerir. | No | Connectiondeviceıd |
| ıothub-Connection-Module-ID |Cihazdan buluta iletilerde IoT Hub tarafından ayarlanan bir KIMLIK. İletiyi gönderen cihazın **ModuleID** 'sini içerir. | No | Connectionmoduleıd |
| ıothub-bağlantı-auth-Generation-ID |Cihazdan buluta iletilerde IoT Hub tarafından ayarlanan bir KIMLIK. İletiyi gönderen cihazın **Connectiondevicegenerationıd** 'Sini ( [cihaz kimliği başına kimlik özellikleri](iot-hub-devguide-identity-registry.md#device-identity-properties)) içerir. | No |Connectiondevicegenerationıd |
| ıothub-Connection-auth-yöntemi |Cihazdan buluta iletilerde IoT Hub tarafından ayarlanan bir kimlik doğrulama yöntemi. Bu özellik, iletiyi gönderen cihazın kimliğini doğrulamak için kullanılan kimlik doğrulama yöntemi hakkındaki bilgileri içerir.| No | connectionAuthMethod |
| DT-DataSchema | Bu değer, cihazdan buluta iletilerde IoT Hub tarafından ayarlanır. Cihaz bağlantısında ayarlanan cihaz modeli KIMLIĞINI içerir. | No | Yok |
| DT-konu | Cihazdan buluta iletileri gönderen bileşenin adı. | Yes | Yok |

## <a name="system-properties-of-c2d-iot-hub-messages"></a>**C2D** IoT Hub Iletilerinin sistem özellikleri

| Özellik | Açıklama  |Kullanıcı ayarlanabilir mi?|
| --- | --- | --- |
| ileti kimliği |İstek-yanıt desenleri için kullanılan ileti için Kullanıcı tarafından ayarlanabilir bir tanımlayıcı. Biçim: büyük/küçük harfe duyarlı bir dize (en fazla 128 karakter uzunluğunda) ASCII 7 bit alfasayısal karakter + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}` .  |Yes|
| sıra numarası |Her buluttan cihaza ileti için IoT Hub tarafından atanan bir sayı (cihaz kuyruğu başına benzersiz). |No|
| kullanıcısı |[Buluttan cihaza](iot-hub-devguide-c2d-guidance.md) iletilerde belirtilen hedef. |No|
| mutlak-süre sonu |İleti süresinin dolma tarihi ve saati. |No| 
| correlation-id |İstek-yanıt desenlerinde, genellikle isteğin MessageID ' i içeren bir yanıt iletisindeki dize özelliği. |Yes|
| user-id |İletilerin kaynağını belirtmek için kullanılan bir KIMLIK. İletiler IoT Hub tarafından oluşturulduğunda, olarak ayarlanır `{iot hub name}` . |Yes|
| ıothub-ACK |Geri bildirim iletisi Oluşturucu. Bu özellik, cihaz tarafından ileti tüketimine yol açacak şekilde geri bildirim iletileri oluşturmak için IoT Hub istemek üzere buluttan cihaza iletimekte kullanılır. Olası değerler: **hiçbiri** (varsayılan): hiçbir geri bildirim iletisi oluşturulmaz, **pozitif**: ileti tamamlandığında bir geri bildirim iletisi alın (veya en fazla teslim sayısına ulaşıldığında), ya da **tam** **olarak pozitif** ve negatif olur. |Yes|

### <a name="system-property-names"></a>Sistem özelliği adları

Sistem özelliği adları, iletilerin yönlendirildiği uç noktaya göre farklılık gösterir. Lütfen bu adlarla ilgili ayrıntılar için aşağıdaki tabloya bakın.

|Sistem özelliği adı|Event Hubs|Azure Depolama|Service Bus|Event Grid|
|--------------------|----------|-------------|-----------|----------|
|İleti KIMLIĞI|ileti kimliği|Ileti|Ileti|ileti kimliği|
|Kullanıcı kimliği|user-id|userId|UserId|user-id|
|Bağlantı cihazı kimliği|ıothub-bağlantı-cihaz kimliği| Connectiondeviceıd|ıothub-bağlantı-cihaz kimliği|ıothub-bağlantı-cihaz kimliği|
|Bağlantı modülü kimliği|ıothub-Connection-Module-ID|Connectionmoduleıd|ıothub-Connection-Module-ID|ıothub-Connection-Module-ID|
|Bağlantı kimlik doğrulaması oluşturma kimliği|ıothub-bağlantı-auth-Generation-ID|Connectiondevicegenerationıd| ıothub-bağlantı-auth-Generation-ID|ıothub-bağlantı-auth-Generation-ID|
|Bağlantı kimlik doğrulama yöntemi|ıothub-Connection-auth-yöntemi|connectionAuthMethod|ıothub-Connection-auth-yöntemi|ıothub-Connection-auth-yöntemi|
|contentType|içerik türü|contentType|ContentType|ıothub-Content-Type|
|Contentenkodlamaya|İçerik kodlama|Contentenkodlamaya|Contentenkodlamaya|ıothub-Content-Encoding|
|ıothub-enqueuedtime|ıothub-enqueuedtime|enqueuedTime| Yok |ıothub-enqueuedtime|
|CorrelationId|correlation-id|correlationId|CorrelationId|correlation-id|
|DT-DataSchema|DT-DataSchema|DT-DataSchema|DT-DataSchema|DT-DataSchema|
|DT-konu|DT-konu|DT-konu|DT-konu|DT-konu|

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
