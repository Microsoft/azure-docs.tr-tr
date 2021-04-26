---
title: Azure IoT Hub yerleşik uç noktasını anlayın | Microsoft Docs
description: Geliştirici Kılavuzu-cihazdan buluta iletileri okumak için yerleşik, Olay Hub 'ı ile uyumlu uç noktanın nasıl kullanılacağını açıklar.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/01/2020
ms.custom:
- amqp
- 'Role: Cloud Development'
ms.openlocfilehash: 4bb33721625f4fc752745ce2b43051c90b3aaa74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92147685"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Cihazdan buluta iletilerini yerleşik uç noktadan okuma

Varsayılan olarak, iletiler, [Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/)uyumlu yerleşik hizmete yönelik uç noktaya (**iletiler/olaylar**) yönlendirilir. Bu uç nokta şu anda yalnızca bağlantı noktası 5671 üzerinde [AMQP](https://www.amqp.org/) protokolü kullanılarak kullanıma sunuldu. IoT Hub 'ı, yerleşik Olay Hub 'ı ile uyumlu mesajlaşma uç nokta **iletilerini/olaylarını** denetlemenize olanak tanımak için aşağıdaki özellikleri kullanıma sunar.

| Özellik            | Açıklama |
| ------------------- | ----------- |
| **Bölüm sayısı** | Cihazdan buluta olay alımı için [bölüm](../event-hubs/event-hubs-features.md#partitions) sayısını tanımlamak üzere bu özelliği oluşturma sırasında ayarlayın. |
| **Saklama süresi**  | Bu özellik, iletilerin gün cinsinden ne kadar süreyle tutulacağını belirtir IoT Hub. Varsayılan değer bir gündür, ancak yedi güne artırılabilir. |

IoT Hub, yerleşik Event Hubs en fazla 7 gün boyunca veri bekletmesine izin verir. IoT Hub oluşturma sırasında saklama süresini ayarlayabilirsiniz. IoT Hub veri saklama süresi, IoT Hub katmanınıza ve birim türüne bağlıdır. Boyut açısından, yerleşik Event Hubs en büyük ileti boyutu mesajlarının en az 24 saate kadar olan iletilerini tutabilir. Örneğin, 1 S1 IoT Hub birimi için, her biri 4k boyutunda en az 400K mesajı koruyacak yeterli depolama alanı sağlar. Cihazlarınız daha küçük iletiler gönderiyorsa, ne kadar depolama alanının tüketildiğine bağlı olarak bunlar daha uzun süre korunabilir (en fazla 7 gün) olabilir. Belirtilen saklama süresi için verilerin en düşük düzeyde tutulması garanti ediyoruz. İletilerin süresi sona erer ve bekletme saati geçtikten sonra erişilemeyecektir. 

IoT Hub Ayrıca, yerleşik cihazdan buluta alma uç noktasında tüketici gruplarını yönetmenizi sağlar. Her bir IoT Hub için en fazla 20 tüketici grubunuz olabilir.

[İleti yönlendirme](iot-hub-devguide-messages-d2c.md) kullanıyorsanız ve [geri dönüş yolu](iot-hub-devguide-messages-d2c.md#fallback-route) etkinse, herhangi bir yoldaki sorguyla eşleşmeyen tüm iletiler yerleşik uç noktaya gider. Bu geri dönüş yolunu devre dışı bırakırsanız, hiçbir sorguyla eşleşmeyen iletiler bırakılır.

[IoT Hub kaynak sağlayıcısı REST API 'lerini](/rest/api/iothub/iothubresource)kullanarak ya da [Azure Portal](https://portal.azure.com)ile program aracılığıyla saklama süresini değiştirebilirsiniz.

IoT Hub, arka uç hizmetleriniz için, hub 'ınız tarafından alınan cihazdan buluta iletileri okumak üzere **ileti/olay** yerleşik uç noktasını kullanıma sunar. Bu uç nokta, Event Hubs hizmetinin iletileri okumak için desteklediği mekanizmalardan birini kullanmanıza olanak sağlayan Event hub ile uyumludur.

## <a name="read-from-the-built-in-endpoint"></a>Yerleşik uç noktadan oku

Bazı ürün tümleştirmeleri ve Event Hubs SDK 'Ları, IoT Hub farkındadır ve yerleşik uç noktaya bağlanmak için IoT Hub hizmeti Bağlantı dizenizi kullanmanıza olanak sağlar.

IoT Hub farkında olmayan Event Hubs SDK 'Ları veya ürün tümleştirmelerini kullandığınızda, Event hub ile uyumlu bir uç nokta ve Event hub ile uyumlu bir ada sahip olmanız gerekir. Bu değerleri portaldan aşağıdaki gibi alabilirsiniz:

1. [Azure Portal](https://portal.azure.com) oturum açın ve IoT Hub 'ınıza gidin.

2. **Yerleşik uç noktalar**' a tıklayın.

3. **Olaylar** bölümü şu değerleri Içerir: **bölümler**, **Olay Hub 'ı Ile uyumlu ad**, **Olay Hub 'ı ile uyumlu uç nokta**, **saklama süresi** ve **tüketici grupları**.

    ![Cihazdan buluta ayarları](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

Portalda, Olay Hub 'ı ile uyumlu uç nokta alanı, şunun gibi görünen bir tam Event Hubs bağlantı dizesi içerir: **Endpoint = SB://abcd1234namespace.ServiceBus.Windows.net/; SharedAccessKeyName = ıothubowner; SharedAccessKey = keykeykeykeykeykey =; EntityPath = ıothub-eHub-abcd-1234-123456**. Kullanmakta olduğunuz SDK başka değerler gerektiriyorsa, bunlar şöyle olur:

| Name | Değer |
| ---- | ----- |
| Uç Nokta | sb://abcd1234namespace.servicebus.windows.net/ |
| Konak adı | abcd1234namespace.servicebus.windows.net |
| Ad Alanı | abcd1234namespace |

Daha sonra, yukarıdaki ekran görüntüsünde gösterildiği gibi açılan listeden herhangi bir paylaşılan erişim ilkesini seçebilirsiniz. Yalnızca belirtilen olay hub 'ına bağlanmak için **Serviceconnect** izinlerine sahip olan ilkeleri gösterir.

IoT Hub ortaya çıkaran yerleşik Olay Hub 'ı ile uyumlu uç noktaya bağlanmak için kullanabileceğiniz SDK 'lar şunlardır:

| Dil | SDK | Örnek |
| -------- | --- | ------ |
| .NET | https://www.nuget.org/packages/Azure.Messaging.EventHubs | [Hızlı Başlangıç](quickstart-send-telemetry-dotnet.md) |
| Java | https://mvnrepository.com/artifact/com.azure/azure-messaging-eventhubs | [Hızlı Başlangıç](quickstart-send-telemetry-java.md) |
| Node.js | https://www.npmjs.com/package/@azure/event-hubs | [Hızlı Başlangıç](quickstart-send-telemetry-node.md) |
| Python | https://pypi.org/project/azure-eventhub/ | [Hızlı Başlangıç](quickstart-send-telemetry-python.md) |

IoT Hub ortaya çıkaran yerleşik Olay Hub 'ı ile uyumlu uç nokta ile kullanabileceğiniz ürün tümleştirmeleri şunlardır:

* [Azure işlevleri](../azure-functions/index.yml). Bkz. [Azure işlevleri ile IoT Hub verileri işleme](https://azure.microsoft.com/resources/samples/functions-js-iot-hub-processing/).
* [Azure Stream Analytics](../stream-analytics/index.yml). Bkz. [Stream Analytics giriş olarak akış verileri](../stream-analytics/stream-analytics-define-inputs.md#stream-data-from-iot-hub).
* [Time Series Insights](../time-series-insights/index.yml). Bkz. [Time Series Insights ortamınıza IoT Hub olay kaynağı ekleme](../time-series-insights/how-to-ingest-data-iot-hub.md).
* [Apache Storm Spout](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). GitHub üzerinde [Spout kaynağını](https://github.com/apache/storm/tree/master/external/storm-eventhubs) görüntüleyebilirsiniz.
* [Apache Spark tümleştirme](../hdinsight/spark/apache-spark-ipython-notebook-machine-learning.md).
* [Azure Databricks](/azure/azure-databricks/).

## <a name="next-steps"></a>Sonraki adımlar

* IoT Hub uç noktaları hakkında daha fazla bilgi için bkz. [IoT Hub uç noktaları](iot-hub-devguide-endpoints.md).

* [Hızlı](quickstart-send-telemetry-node.md) başlangıçlarda, sanal cihazlardan cihazdan buluta iletileri gönderme ve yerleşik uç noktadan iletileri okuma işlemleri gösterilir. 

Daha fazla ayrıntı için, rotalar öğreticisini [kullanarak cihazdan buluta iletileri IoT Hub işleme](tutorial-routing.md) bakın.

* Cihazdan buluta iletilerinizi özel uç noktalara yönlendirmek istiyorsanız bkz. [cihazdan buluta iletiler için ileti yollarını ve özel uç noktaları kullanma](iot-hub-devguide-messages-read-custom.md).