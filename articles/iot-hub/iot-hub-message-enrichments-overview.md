---
title: Azure IoT Hub ileti zenginleştirme 'ye Genel Bakış
description: Bu makalede, iletiler belirlenen uç noktaya gönderilmeden önce iletileri daha fazla bilgi damgalama özelliğini IoT Hub veren ileti zenginleştirmelerinin gösterildiği gösterilmektedir.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 10e999a7f0662c421d73872448506a9c9ca05975
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106079038"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages"></a>Cihazdan buluta IoT Hub iletileri için ileti zenginleştirmelerinin

*İleti zenginleştirmelerinin* iletileri belirlenen uç noktaya gönderilmeden önce ek bilgilerle iletileri *damgalamak* IoT Hub yeteneğidir. İleti zenginleştirmelerinin tek bir nedeni, aşağı akış işlemeyi basitleştirmek için kullanılabilecek verileri içermektir. Örneğin, cihaz ikizi etiketiyle cihaz telemetri iletileri zenginleştirilmesi, bu bilgilere yönelik cihaz ikizi API çağrıları yapmak için müşterilerin yükünü azaltabilir.

![İleti zenginleştirme akışı](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Bir ileti zenginleştirmesi, üç temel öğeye sahiptir:

* Zenginleştirme adı veya anahtarı

* Bir değer

* Zenginleştirme için uygulanması gereken bir veya daha fazla [uç nokta](iot-hub-devguide-endpoints.md) .

**Anahtar** bir dizedir. Anahtar yalnızca alfasayısal karakterler veya şu özel karakterleri içerebilir: kısa çizgi ( `-` ), alt çizgi ( `_` ) ve nokta ( `.` ).

**Değer** aşağıdaki örneklerden herhangi biri olabilir:

* Herhangi bir statik dize. Koşullar, Logic, işlemler ve işlevler gibi dinamik değerlere izin verilmez. Örneğin, birkaç müşteri tarafından kullanılan bir SaaS uygulaması geliştirirseniz, her müşteriye bir tanımlayıcı atayabilir ve bu tanımlayıcıyı uygulamada kullanılabilir hale getirebilirsiniz. Uygulama çalıştığında IoT Hub, cihaz telemetri iletilerini müşterinin tanımlayıcısına göre damgalayıp, bu da iletileri her müşteri için farklı şekilde işlemeyi mümkün kılar.

* İletiyi gönderen IoT Hub 'ının adı. Bu değer *$iothubname*.

* Cihaz ikizi bilgileri, örneğin yolu. Örnekler *$Twin. Tags. Field* ve *$Twin. Tags. Enlem* olacaktır.

   > [!NOTE]
   > Şu anda yalnızca $iothubname, $twin. Tags, $twin. Properties. Desired ve $twin. Properties. bildirilen ileti zenginleştirme için desteklenen değişkenler.

İleti zenginleştirme, seçilen uç noktalara gönderilen iletilere uygulama özellikleri olarak eklenir.  

## <a name="applying-enrichments"></a>Zenginler uygulanıyor

İletiler, aşağıdaki örnekler de dahil [IoT Hub ileti yönlendirme](iot-hub-devguide-messages-d2c.md)tarafından desteklenen herhangi bir veri kaynağından gelebilir:

* sıcaklık veya basınç gibi cihaz telemetrisi
* cihaz ikizi değişiklik bildirimleri--cihazdaki değişiklikler ikizi
* cihaz yaşam döngüsü olayları, Örneğin cihazın oluşturulduğu veya silindiği zaman

IoT Hub yerleşik uç noktasına veya Azure Blob depolama, Service Bus kuyruğu veya Service Bus konu gibi özel uç noktalara yönlendirilmekte olan iletilere zenginleştirme ekleyebilirsiniz.

Event Grid olarak uç noktayı seçerek Event Grid yayımlanmakta olan iletilere zenginleştirme ekleyebilirsiniz. Event Grid aboneliğinize göre cihaz telemetrisine IoT Hub varsayılan bir yol oluşturacağız. Bu tek yol, tüm Event Grid aboneliklerinizi işleyebilir. Cihaz telemetrisine olay Kılavuzu aboneliğini oluşturduktan sonra Event Grid uç noktası için enzenginler yapılandırabilirsiniz. Daha fazla bilgi için bkz. [IoT Hub ve Event Grid](iot-hub-event-grid.md).

Enzenginler, uç nokta başına uygulanır. Belirli bir uç nokta için damgalı olacak beş zenginleştirme belirtirseniz, bu uç noktaya giden tüm iletiler aynı beş enzenginle damgalı olur.

Zenginleştirme aşağıdaki yöntemler kullanılarak yapılandırılabilir:

| **Yöntem** | **Komut** |
| ----- | -----| 
| Portal | [Azure Portal](https://portal.azure.com) [İleti zenginleştirme öğreticisine](tutorial-message-enrichments.md) bakın | 
| Azure CLI’si   | [az IoT Hub iletisi-zenginleştirme](/cli/azure/iot/hub/message-enrichment) |
| Azure PowerShell | [Add-AzIotHubMessageEnrichment](/powershell/module/az.iothub/add-aziothubmessageenrichment) |

İleti sorgularının eklenmesi ileti yönlendirmeye gecikme eklemez.

İleti zenginleştirmelerinin denemek için [ileti zenginleştirme öğreticisine](tutorial-message-enrichments.md) bakın

## <a name="limitations"></a>Sınırlamalar

* Standart veya temel katmandaki bu hub 'lar için IoT Hub başına en fazla 10 zenginleştirme ekleyebilirsiniz. Ücretsiz katmanda IoT Hub 'Ları için 2 adede kadar zenginleştirme ekleyebilirsiniz.

* Bazı durumlarda, bir değer ile cihaz ikizi bir etikete veya özelliğe ayarlanmış bir zenginleştirme uyguluyorsanız, değer bir dize değeri olarak damgalanacaktır. Örneğin, bir zenginleştirme değeri $twin. Tags. Field olarak ayarlandıysa, iletiler ikizi bu alanın değeri yerine "$twin. Tags. Field" dizesiyle damgalı olur. Bu durum aşağıdaki durumlarda oluşur:

   * IoT Hub temel katmanda. Temel katman IoT Hub 'ları, cihaz ikizlerini desteklemez.

   * IoT Hub Standart katmanda, ancak iletiyi gönderen cihazda cihaz ikizi yok.

   * IoT Hub Standart katmanda, ancak zenginleştirme değeri için kullanılan Device ikizi yolu yok. Örneğin, zenginleştirme değeri $Twin. Tags. Location olarak ayarlanmışsa ve cihaz ikizi Etiketler altında bir konum özelliği yoksa, ileti "$Twin. Tags. Location" dizesiyle damgalı olur. 

* Bir cihaz ikizi güncelleştirmelerinin, ilgili zenginleştirme değerinde yansıtılması beş dakika kadar sürebilir.

* En zenginler de dahil olmak üzere toplam ileti boyutu 256 KB 'yi aşamaz. İleti boyutu 256 KB 'yi aşarsa IoT Hub iletiyi bırakacak. İletiler bırakıldığında hataları tanımlamak ve hatalarını ayıklamak için [IoT Hub ölçümlerini](monitor-iot-hub-reference.md#metrics) kullanabilirsiniz. Örneğin, [yönlendirme ölçümlerinde](monitor-iot-hub-reference.md#routing-metrics)uyumsuz (*D2C. telemetri. çıkış. geçersiz*) ölçümü ile ilgili *telemetri iletilerini* izleyebilirsiniz. Daha fazla bilgi için bkz. [izleyici IoT Hub](monitor-iot-hub.md).

* İleti zenginleştirmelerinin dijital ikizi değişiklik olayları için uygulanmıyor.

## <a name="pricing"></a>Fiyatlandırma

İleti zenginleştirme, ek ücret ödemeden kullanılabilir. Şu anda bir IoT Hub ileti gönderdiğinizde ücretlendirilirsiniz. İleti birden çok uç noktaya gitse bile, bu ileti için yalnızca bir kez ücretlendirilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

İletileri bir IoT Hub yönlendirme hakkında daha fazla bilgi için şu makalelere göz atın:

* [İleti zenginleştirme öğreticisi](tutorial-message-enrichments.md)

* [Farklı uç noktalara cihazdan buluta iletiler göndermek için IoT Hub ileti yönlendirmeyi kullanma](iot-hub-devguide-messages-d2c.md)

* [Öğretici: IoT Hub yönlendirme](tutorial-routing.md)
