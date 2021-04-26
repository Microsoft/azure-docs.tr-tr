---
title: Azure IoT Central verileri dışarı aktarma (eski) | Microsoft Docs
description: Azure IoT Central uygulamanızdan Azure Event Hubs, Azure Service Bus ve Azure Blob depolama 'ya veri aktarma
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/25/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: d4c099d29a843b4c354ffb218887dc7ffab51771
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98065448"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-legacy"></a>Veri dışa aktarma kullanarak IoT verilerini bulut hedeflerine dışa aktarma (eski)

> [!Note]
> Bu makalede IoT Central ' deki eski veri dışa aktarma özellikleri açıklanmaktadır.
>
> - En son veri dışa aktarma özellikleri hakkında daha fazla bilgi için bkz. [veri dışarı aktarma kullanarak IoT verilerini bulut hedeflerine dışa aktarma](./howto-export-data.md).
> - Önizleme verileri dışarı aktarma ve eski verileri dışarı aktarma özellikleri arasındaki farklar hakkında bilgi edinmek için [karşılaştırma tablosuna](./howto-export-data.md#comparison-of-legacy-data-export-and-data-export)bakın.

Bu makalede, Azure IoT Central 'da veri dışarı aktarma özelliğinin nasıl kullanılacağı açıklanır. Bu özellik verilerinizi **azure Event Hubs**, **Azure Service Bus** veya **Azure Blob Storage** örneklerine sürekli olarak dışa aktarmanıza olanak tanır. Veri dışa aktarma JSON biçimini kullanır ve Telemetriyi, cihaz bilgilerini ve cihaz şablonu bilgilerini içerebilir. İçin, bu verileri kullan:

- Sıcak yol öngörüleri ve analizi. Bu seçenek, Azure Stream Analytics özel kuralların tetiklenmesi, Azure Logic Apps özel iş akışlarını tetiklenmesi veya dönüştürülecek Azure Işlevleri aracılığıyla geçirilmesidir.
- Microsoft Power BI 'de Azure Machine Learning veya uzun süreli eğilim analizinde eğitim modelleri gibi soğuk yol analizi.

> [!Note]
> Veri dışarı aktarmayı açtığınızda, bu andan itibaren yalnızca verileri alırsınız. Şu anda veri dışa aktarma kapalı olduğunda veriler bir saat için alınamaz. Daha fazla geçmiş verileri sürdürmek için, verilerin dışarı aktarılmasını erken açın.

## <a name="prerequisites"></a>Önkoşullar

IoT Central uygulamanızda yönetici olmanız veya veri dışa aktarma izinlerinizin olması gerekir.

## <a name="set-up-export-destination"></a>Dışarı aktarma hedefini ayarla

Dışarı aktarma hedefi, veri dışa aktarma işlemini yapılandırmadan önce mevcut olmalıdır.

### <a name="create-event-hubs-namespace"></a>Event Hubs ad alanı oluşturma

Uygulamasına dışarı aktarmak için mevcut bir Event Hubs ad alanınız yoksa, aşağıdaki adımları izleyin:

1. [Azure Portal yeni bir Event Hubs ad alanı](https://ms.portal.azure.com/#create/Microsoft.EventHub)oluşturun. [Azure Event Hubs docs](../../event-hubs/event-hubs-create.md)'ta daha fazla bilgi edinebilirsiniz.

2. Bir abonelik seçin. IoT Central uygulamanızla aynı abonelikte olmayan diğer aboneliklerdeki verileri dışarı aktarabilirsiniz. Bu durumda bir bağlantı dizesi kullanarak bağlanırsınız.

3. Event Hubs ad alanında bir olay hub 'ı oluşturun. Ad alanına gidin ve bir olay hub 'ı örneği oluşturmak için en üstteki **+ Olay Hub 'ını** seçin.

### <a name="create-service-bus-namespace"></a>Service Bus ad alanı oluştur

Uygulamasına dışarı aktarmak için mevcut bir Service Bus ad alanınız yoksa, aşağıdaki adımları izleyin:

1. [Azure Portal yeni bir Service Bus ad alanı](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)oluşturun. [Azure Service Bus docs](../../service-bus-messaging/service-bus-create-namespace-portal.md)' de daha fazla bilgi edinebilirsiniz.
2. Bir abonelik seçin. IoT Central uygulamanızla aynı abonelikte olmayan diğer aboneliklerdeki verileri dışarı aktarabilirsiniz. Bu durumda bir bağlantı dizesi kullanarak bağlanırsınız.

3. Uygulamasına dışarı aktarmak için bir kuyruk veya konu oluşturmak için Service Bus ad alanına gidin ve **+ Queue** veya **+ konu başlığını** seçin.

Dışarı aktarma hedefi olarak Service Bus seçtiğinizde, kuyruklar ve konuların oturumları veya yinelenen algılama özelliği etkin olmalıdır. Bu seçeneklerden biri etkinleştirilirse, bazı iletiler kuyruğunuza veya konuya ulaşmayacaktır.

### <a name="create-storage-account"></a>Depolama hesabı oluştur

' A dışa aktarılacak mevcut bir Azure depolama hesabınız yoksa, aşağıdaki adımları izleyin:

1. [Azure Portal yeni bir depolama hesabı](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)oluşturun. Yeni [Azure Blob depolama hesapları](../../storage/blobs/storage-quickstart-blobs-portal.md) veya [Azure Data Lake Storage v2 depolama hesapları](../../storage/common/storage-account-create.md)oluşturma hakkında daha fazla bilgi edinebilirsiniz. Veri dışa aktarma, yalnızca blok bloblarını destekleyen depolama hesaplarına veri yazabilir. Aşağıdaki listede, bilinen uyumlu depolama hesabı türleri gösterilmektedir:

    |Performans katmanı|Hesap Türü|
    |-|-|
    |Standart|Genel Amaçlı v2|
    |Standart|Genel Amaçlı v1|
    |Standart|Blob depolama|
    |Premium|Blob depolamayı engelle|

2. Depolama hesabınızda bir kapsayıcı oluşturun. Depolama hesabınıza gidin. **BLOB hizmeti** altında **bloblara gözatamıyorum**' ı seçin. Yeni bir kapsayıcı oluşturmak için üst kısımdaki **+ kapsayıcı** ' yı seçin.

## <a name="set-up-data-export"></a>Veri dışarı aktarma ayarlama

Verilerin dışarı aktarılacağı bir hedef olduğuna göre, verileri dışarı aktarma işlemini ayarlamak için aşağıdaki adımları izleyin.

1. IoT Central uygulamanızda oturum açın.

2. Sol bölmede **veri dışa aktar**' ı seçin.

    > [!Tip]
    > Sol bölmede **veri dışa aktarma** 'yı görmüyorsanız, uygulamanızda veri dışarı aktarma yapılandırma izniniz yok demektir. Verilerin dışarı aktarılmasını ayarlamak için bir yöneticiye danışın.

3. **+ Yeni** düğmesini seçin. Dışarı aktarmanın hedefi olarak **Azure Blob depolama**, **azure Event Hubs**, **Azure Service Bus kuyruğu** veya **Azure Service Bus konusunun** birini seçin. Uygulama başına en fazla dışarı aktarma sayısı beştir.

4. Dışarı aktarma için bir ad girin. Aşağı açılan liste kutusunda, **ad** alanınızı seçin veya **bir bağlantı dizesi girin**.

    - Depolama hesapları, Event Hubs ad alanları ve Service Bus ad alanlarını IoT Central uygulamanızla aynı abonelikte görürsünüz. Bu aboneliğin dışında bir hedefe aktarmak istiyorsanız, **bir bağlantı dizesi girin** ' i seçin ve 6. adıma bakın.
    - Ücretsiz fiyatlandırma planı kullanılarak oluşturulan uygulamalar için, veri dışa aktarma 'yı yapılandırmanın tek yolu bir bağlantı dizesidir. Ücretsiz fiyatlandırma planındaki uygulamalar ilişkili bir Azure aboneliğine sahip değildir.

    ![Yeni Olay Hub 'ı oluştur](media/howto-export-data-legacy/export-event-hub.png)

5. Aşağı açılan liste kutusundan bir olay hub 'ı, kuyruğu, konuyu veya kapsayıcıyı seçin.

6. Seçim **Bir bağlantı dizesi girin**' i seçerseniz, Bağlantı dizenizi yapıştırmanız için yeni bir kutu belirir. İçin bağlantı dizesini almak için:

    - Event Hubs veya Service Bus, Azure portal ad alanına gidin:
        - Tüm ad alanı için bir bağlantı dizesi kullanmak için:
            1. **Ayarlar** altında, **paylaşılan erişim ilkeleri** ' ni seçin.
            2. Yeni bir anahtar oluşturun veya **gönderme** izinleri olan varolan bir anahtarı seçin.
            3. Birincil veya ikincil bağlantı dizesini kopyalayın
        - Belirli bir olay hub 'ı örneği veya Service Bus kuyruğu veya konusunun bağlantı dizesini kullanmak için, Event Hubs veya varlıklar **> kuyruklar** veya **varlıklar > konular** **>** ' a bakın. Belirli bir örnek seçin ve bağlantı dizesi almak için yukarıdaki adımları izleyin.
    - Depolama hesabı, Azure portal depolama hesabına gidin:
        - Yalnızca tüm depolama hesabının bağlantı dizeleri desteklenir. Tek bir kapsayıcıya kapsamlı bağlantı dizeleri desteklenmez.
          1. **Ayarlar** altında **erişim anahtarları** ' nı seçin.
          2. KEY1 bağlantı dizesini veya key2 bağlantı dizesini kopyalayın

    Bağlantı dizesini yapıştırın. Örneği veya büyük/küçük harfe duyarlı **kapsayıcı adını** yazın.

7. **Dışarı aktarılacak veriler**' in altında, türü **üzerine** ayarlayarak dışarı aktarılacak veri türlerini seçin.

8. Verilerin dışarı aktarılmasını açmak için **etkin** geçiş özelliğinin **Açık** olduğundan emin olun. **Kaydet**’i seçin.

9. Birkaç dakika sonra verileriniz seçtiğiniz hedefte görüntülenir.

## <a name="export-contents-and-format"></a>İçeriği ve biçimi dışarı aktar

İçe aktarılmış telemetri verileri, yalnızca telemetri değerlerinin kendileri değil, cihazlarınızın IoT Central gönderildiği iletinin tamamını içerir. İçe aktarılmış cihazlar verileri tüm cihazların özelliklerine ve meta verilerine yapılan değişiklikleri içerir ve bu cihaz şablonları tüm cihaz şablonlarında değişiklikler içerir.

Event Hubs ve Service Bus için veriler neredeyse gerçek zamanlı olarak verilir. Veriler `body` özellikte ve JSON biçimindedir. Örnekler için aşağıya bakın.

BLOB depolama için, veriler dakikada bir kez, son içe aktarma dosyasından bu yana yapılan değişiklikleri içeren her bir dosyayla birlikte verilir. Verilen veriler JSON biçiminde üç klasöre yerleştirilir. Depolama hesabınızdaki varsayılan yollar şunlardır:

- Telemetri: _{Container}/{app-id}/telemetry/{yyyy}/{mm}/{dd}/{hh}/{mm}/{filename}_
- Cihazlar: _{Container}/{app-id}/Devices/{yyyy}/{mm}/{dd}/{ss}/{mm}/{filename}_
- Cihaz şablonları: _{Container}/{App-ID}/devicetemplates/{yyyy}/{mm}/{dd}/{ss}/{mm}/{filename}_

Azure portal içe aktarılmış dosyalara gitmek için dosyaya gidin ve **blobu Düzenle** sekmesini seçin.

## <a name="telemetry"></a>Telemetri

Event Hubs ve Service Bus için IoT Central bir cihazdan iletiyi aldıktan sonra yeni bir iletiyi hızlı bir şekilde dışarı aktarır. Her bir dışarıya gönderilen ileti, cihazın, JSON biçiminde gövde özelliğinde gönderildiği tam iletiyi içerir.

BLOB depolama için, iletiler toplu ve dakikada bir kez verilir. İçeri aktarılmış dosyalar, blob depolamaya [IoT Hub ileti yönlendirme tarafından içeri](../../iot-hub/tutorial-routing.md) aktarılmış ileti dosyalarıyla aynı biçimi kullanır.

> [!NOTE]
> BLOB depolama için, cihazlarınızın `contentType: application/JSON` ve `contentEncoding:utf-8` (veya) içeren iletiler göndermesini sağlayın `utf-16` `utf-32` . Bir örnek için [IoT Hub belgelerine](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) bakın.

Telemetriyi Gönderen cihaz, cihaz KIMLIĞI tarafından temsil edilir (aşağıdaki bölümlere bakın). Cihazların adlarını almak için, cihaz verilerini dışarı aktarın ve cihaz iletisinin **DeviceID** 'Siyle eşleşen **connectiondeviceıd** 'yi kullanarak her iletiyi ilişkilendirin.

Aşağıdaki örnek, bir olay hub 'ından veya Service Bus sırasından veya konusundan alınan bir iletiyi gösterir:

```json
{
  "temp":81.129693132351775,
  "humid":59.488071477541247,
  "EventProcessedUtcTime":"2020-04-07T09:41:15.2877981Z",
  "PartitionId":0,
  "EventEnqueuedUtcTime":"2020-04-07T09:38:32.7380000Z"
}
```

Bu ileti gönderme cihazının cihaz KIMLIĞINI içermez.

Azure Stream Analytics sorgusundaki ileti verilerinden cihaz KIMLIĞINI almak için [Getmetadatapropertyvalue](/stream-analytics-query/getmetadatapropertyvalue) işlevini kullanın. Bir örnek için, [Stream Analytics, Azure işlevleri ve SendGrid kullanarak azure IoT Central özel kurallarla genişletme](./howto-create-custom-rules.md)içindeki sorguya bakın.

Azure Databricks veya Apache Spark çalışma alanındaki cihaz KIMLIĞINI almak için [SystemProperties](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)' i kullanın. Bir örnek için, [Azure Databricks kullanarak özel analizler Ile Azure IoT Central 'Yi genişleten](./howto-create-custom-analytics.md)Databricks çalışma alanına bakın.

Aşağıdaki örnekte, blob depolamaya aktarılmış bir kayıt gösterilmektedir:

```json
{
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{

  },
  "SystemProperties":{
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

## <a name="devices"></a>Cihazlar

Anlık görüntüdeki her ileti veya kayıt, bir cihazdaki bir veya daha fazla değişikliği, son dışarıya alınan iletiden bu yana cihaz ve bulut özelliklerini temsil eder. İleti şunları içerir:

- `id` IoT Central cihaz
- `displayName` Cihazın
- İçindeki cihaz şablonu KIMLIĞI `instanceOf`
- `simulated` cihaz sanal bir cihaz ise, true bayrağı
- `provisioned` cihaz sağlandıysa, doğru bayrağı
- `approved` cihaz veri göndermek üzere onaylanmışsa, doğru bayrağı
- Özellik değerleri
- `properties` Cihaz ve bulut özellikleri değerlerini ekleme

Silinen cihazlar aktarılmaz. Şu anda, silinen cihazlar için, aktarılmış iletilerde hiçbir gösterge yok.

Event Hubs ve Service Bus için IoT Central, cihaz verilerini içeren iletileri olay hub 'ınıza veya Service Bus kuyruğuna veya konuya neredeyse gerçek zamanlı olarak gönderir.

BLOB depolama için, en son yazılan son bir dakikada bir kez aktarıldığından, tüm değişiklikleri içeren yeni bir anlık görüntü

Aşağıdaki örnek ileti, bir olay hub 'ında veya Service Bus kuyruğu veya konusunun cihazlar ve özellikler hakkındaki bilgileri gösterir:

```json
{
  "body":{
    "id": "<device Id>",
    "etag": "<etag>",
    "displayName": "Sensor 1",
    "instanceOf": "<device template Id>",
    "simulated": false,
    "provisioned": true,
    "approved": true,
    "properties": {
        "sensorComponent": {
            "setTemp": "30",
            "fwVersion": "2.0.1",
            "status": { "first": "first", "second": "second" },
            "$metadata": {
                "setTemp": {
                    "desiredValue": "30",
                    "desiredVersion": 3,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 3
                },
                "fwVersion": { "ackVersion": 3 },
                "status": {
                    "desiredValue": {
                        "first": "first",
                        "second": "second"
                    },
                    "desiredVersion": 2,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 2
                }
            },
            
        }
    },
    "installDate": { "installDate": "2020-02-01" }
},
  "annotations":{
    "iotcentral-message-source":"devices",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":39740,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274959654
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":39740,
  "enqueuedTimeUtc":"2020-02-01T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Bu anlık görüntü, blob depolamada cihazları ve özellik verilerini gösteren örnek bir iletidir. İçe aktarılmış dosyalar kayıt başına tek bir satır içerir.

```json
{
  "id": "<device Id>",
  "etag": "<etag>",
  "displayName": "Sensor 1",
  "instanceOf": "<device template Id>",
  "simulated": false,
  "provisioned": true,
  "approved": true,
  "properties": {
      "sensorComponent": {
          "setTemp": "30",
          "fwVersion": "2.0.1",
          "status": { "first": "first", "second": "second" },
          "$metadata": {
              "setTemp": {
                  "desiredValue": "30",
                  "desiredVersion": 3,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 3
              },
              "fwVersion": { "ackVersion": 3 },
              "status": {
                  "desiredValue": {
                      "first": "first",
                      "second": "second"
                  },
                  "desiredVersion": 2,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 2
              }
          },
          
      }
  },
  "installDate": { "installDate": "2020-02-01" }
}
```

## <a name="device-templates"></a>Cihaz şablonları

Her ileti veya anlık görüntü kaydı, yayımlanan bir cihaz şablonunda yapılan son dışarıya alınan iletiden itibaren bir veya daha fazla değişikliği temsil eder. Her ileti veya kayıtta gönderilen bilgiler şunları içerir:

- `id``instanceOf`Yukarıdaki cihazların akışıyla eşleşen cihaz şablonu
- `displayName` cihaz şablonu
- Cihaz `capabilityModel` `interfaces` , ve telemetri, Özellikler ve komut tanımlarını dahil
- `cloudProperties` tanımlar
- Satır içi ve başlangıç değerlerini içeren `capabilityModel`

Silinen cihaz şablonları aktarılmaz. Şu anda, Silinen cihaz şablonları için, aktarılmış iletilerde hiçbir gösterge yok.

Event Hubs ve Service Bus için IoT Central, cihaz şablonu verilerini içeren iletileri olay hub 'ınıza veya Service Bus kuyruğuna veya konuya neredeyse gerçek zamanlı olarak gönderir.

BLOB depolama için, en son yazılan son bir dakikada bir kez aktarıldığından, tüm değişiklikleri içeren yeni bir anlık görüntü

Bu örnek, Olay Hub 'ında veya Service Bus kuyruğu veya konusunun cihaz şablonları verileri hakkında bir ileti gösterir:

```json
{
  "body":{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "sensorComponent",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorConfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  },
    "annotations":{
      "iotcentral-message-source":"deviceTemplates",
      "x-opt-partition-key":"<partitionKey>",
      "x-opt-sequence-number":25315,
      "x-opt-offset":"<offset>",
      "x-opt-enqueued-time":1539274985085
    },
    "partitionKey":"<partitionKey>",
    "sequenceNumber":25315,
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

Bu örnek anlık görüntü, blob depolamada cihaz ve özellik verilerini içeren bir ileti gösterir. İçe aktarılmış dosyalar kayıt başına tek bir satır içerir.

```json
{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "Sensor component",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorconfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  }
```

## <a name="data-format-change-notice"></a>Veri biçimi değişiklik bildirimi

> [!Note]
> Telemetri akış veri biçimi bu değişiklikten etkilenmez. Yalnızca cihazlar ve cihaz şablonu veri akışları etkilenir.

Önizleme uygulamanızda *cihazlar* ve *cihaz şablonları* akışları açık olan bir veri dışa aktarma Işlemi varsa, **30 Haziran 2020 tarihine** kadar dışarı aktarmayı güncelleştirin. Bu gereksinim, Azure Blob depolama, Azure Event Hubs ve Azure Service Bus dışarı aktarmalar için geçerlidir.

1 Şubat 2020 ' den başlayarak, cihazlar ve cihaz şablonları etkin olan uygulamalardaki tüm yeni dışarı aktarımlar yukarıda açıklanan veri biçimine sahip olacaktır. Bu tarihten önce oluşturulan tüm dışarı aktarımlar, 30 Haziran 2020 ' e kadar eski veri biçiminde kalır. bu zaman, bu dışarı aktarmalar otomatik olarak yeni veri biçimine geçirilir. Yeni veri biçimi [cihaz](/rest/api/iotcentral/devices/get), [cihaz özelliği](/rest/api/iotcentral/devices/getproperties), [cihaz bulutu özelliği](/rest/api/iotcentral/devices/getcloudproperties)ve IoT Central genel API 'sindeki [cihaz şablonu](/rest/api/iotcentral/devicetemplates/get) nesneleriyle eşleşir.

**Cihazlar** için eski veri biçimi ve yeni veri biçimi arasındaki önemli farklar şunlardır:
- `@id` cihaz kaldırıldığında, `deviceId` olarak yeniden adlandırılır `id` 
- `provisioned` Cihazın sağlama durumunu açıklamaya yönelik bayrak eklenir
- `approved` Cihazın onay durumunu açıklamaya yönelik bayrak eklenir
- `properties` Cihaz ve bulut özellikleri dahil, ortak API 'deki varlıklarla eşleşir

**Cihaz şablonlarında**, eski veri biçimi ve yeni veri biçimi arasındaki önemli farklar şunlardır:

- `@id` cihaz şablonu için yeniden adlandırıldı `id`
- `@type` cihaz şablonu için olarak yeniden adlandırılır `types` ve artık bir dizidir

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>Cihazlar (biçim 3 Şubat 2020 itibariyle kullanım dışı)

```json
{
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{
    "$cloudProperties":{
        "Color":"blue"
    },
    "EnvironmentalSensor":{
      "thsensormodel":{
        "reported":{
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{
        "reported":{
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{
      "totalStorage":{
        "reported":{
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{
        "reported":{
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>Cihaz şablonları (biçim 3 Şubat 2020 itibariyle kullanım dışı)

```json
{
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{
                  "@value":"50"
                }
              },
              "visualizationDetail":{
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[
      {
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>Sonraki adımlar

Artık verilerinizi Azure Event Hubs, Azure Service Bus ve Azure Blob depolama 'ya aktarmayı öğrenmiş olduğunuza göre, sonraki adıma geçin:

> [!div class="nextstepaction"]
> [Databricks ile özel analizler çalıştırma](./howto-create-custom-analytics.md)