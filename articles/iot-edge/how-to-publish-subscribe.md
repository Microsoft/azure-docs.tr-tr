---
title: Azure IoT Edge yayımlayın ve abone olun | Microsoft Docs
description: İletileri yayımlamak ve abone olmak için IoT Edge MQTT Aracısı kullanın
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.reviewer: ebertra
ms.date: 11/09/2020
ms.topic: conceptual
ms.service: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 1c4760362e7c2b3965638b3213910b5b8cd6f079
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516187"
---
# <a name="publish-and-subscribe-with-azure-iot-edge-preview"></a>Azure IoT Edge yayımlama ve abone olma (Önizleme)

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

İletileri yayımlamak ve abone olmak için MQTT Broker Azure IoT Edge kullanabilirsiniz. Bu makalede, bu aracıya nasıl bağlanacağınız, Kullanıcı tanımlı konular üzerinden iletileri yayımlayıp abone olabileceğiniz ve IoT Hub mesajlaşma temelleri kullanabileceğiniz gösterilmektedir. IoT Edge MQTT Aracısı, IoT Edge hub 'ında yerleşik olarak bulunur. Daha fazla bilgi için [IoT Edge hub 'ının aracı özelliklerine](iot-edge-runtime.md)bakın.

> [!NOTE]
> IoT Edge MQTT Aracısı Şu anda genel önizlemede.

## <a name="pre-requisites"></a>Ön koşullar

- Geçerli aboneliği olan bir Azure hesabı
- CLI uzantısı yüklü olan [Azure CLI](/cli/azure/) `azure-iot` . Daha fazla bilgi için bkz. Azure [Azure CLI Için Azure IoT uzantısı yükleme adımları](/cli/azure/azure-cli-reference-for-iot).
- SKU 'nun **IoT Hub** F1, S1, S2 veya S3.
- **Sürüm 1,2 veya üzeri bir IoT Edge cihazına** sahip olmanız gerekir. MQTT Aracısı Şu anda genel önizleme aşamasında olduğundan, MQTT Aracısı 'nı etkinleştirmek için aşağıdaki ortam değişkenlerini edgeHub kapsayıcısında doğru olarak ayarlayın: IoT Edge

   | Name | Değer |
   | - | - |
   | `experimentalFeatures__enabled` | `true` |
   | `experimentalFeatures__mqttBrokerEnabled` | `true` |

- IoT Edge cihazda yüklü olan **istemciler** . Bu makalede, popüler mosquıto istemcileri [MOSQUITTO_PUB](https://mosquitto.org/man/mosquitto_pub-1.html) ve [MOSQUITTO_SUB](https://mosquitto.org/man/mosquitto_sub-1.html)kullanılmaktadır. Bunun yerine diğer MQTT istemcileri kullanılabilir. Bir Ubuntu cihazına mosquıto istemcilerini yüklemek için şu komutu çalıştırın:

    ```cmd
    sudo apt-get update && sudo apt-get install mosquitto-clients
    ```

    MQTT bağlantı noktalarını (8883 ve 1883) engellemeye ve IoT Edge hub ile çakışmasına neden olabileceğinden, mosquıto sunucusunu yüklemeyin.

## <a name="connect-to-iot-edge-hub"></a>IoT Edge hub 'ına Bağlan

IoT Edge hub 'a bağlanmak, [Genel BIR MQTT istemcisiyle IoT Hub bağlanma makalesindeki](../iot-hub/iot-hub-mqtt-support.md) veya [IoT Edge hub makalesinin kavramsal açıklamasında](iot-edge-runtime.md)açıklanan adımları izler. Bu adımlar şunlardır:

1. İsteğe bağlı olarak, MQTT istemcisi, Aktarım Katmanı Güvenliği (TLS) kullanarak IoT Edge hub ile *güvenli bir bağlantı* kurar
2. MQTT istemcisi IoT Edge hub 'a kendi *kimliğini doğrular*
3. IoT Edge hub, MQTT istemcisini yetkilendirme ilkesi başına *yetkilendirir*

### <a name="secure-connection-tls"></a>Güvenli bağlantı (TLS)

Aktarım Katmanı Güvenliği (TLS), istemciyle IoT Edge merkezi arasında şifrelenmiş bir iletişim kurmak için kullanılır.

TLS 'yi devre dışı bırakmak için, bağlantı noktası 1883 (MQTT) kullanın ve edgeHub kapsayıcısını 1883 numaralı bağlantı noktasına bağlayın.

TLS 'yi etkinleştirmek için, istemci bağlantı noktası 8883 ' de (MQTTS) MQTT aracısına bağlanırsa bir TLS kanalı başlatılır. Aracı, istemcinin doğrulanması gereken sertifika zincirini gönderir. Sertifika zincirini doğrulamak için MQTT aracısının kök sertifikasının, istemcide güvenilir bir sertifika olarak yüklenmesi gerekir. Kök sertifikaya güvenilmiyorsa, istemci kitaplığı bir sertifika doğrulama hatası ile MQTT Aracısı tarafından reddedilir. Bu aracı kök sertifikasını istemciye yüklemek için izlenecek adımlar, [saydam ağ geçidindeki](how-to-create-transparent-gateway.md) ile aynıdır ve [bir aşağı akış cihazı hazırlama](how-to-connect-downstream-device.md#prepare-a-downstream-device) belgelerinde açıklanmıştır.

### <a name="authentication"></a>Kimlik Doğrulaması

Bir MQTT istemcisinin kendi kimliğini doğrulamak için, öncelikle bir bağlantı paketini MQTT aracısına göndermelidir, bu dosyanın adında bir bağlantı başlatması gerekir. Bu paket üç kimlik doğrulama bilgisi sağlar: a `client identifier` , a `username` ve a `password` :

- `client identifier`Alan, IoT Hub içindeki cihazın veya modül adının adıdır. Aşağıdaki sözdizimini kullanır:

  - Bir cihaz için: `<device_name>`

  - Bir modül için: `<device_name>/<module_name>`

   MQTT aracısına bağlanmak için bir cihazın veya modülün IoT Hub kayıtlı olması gerekir.

   Aracı, aynı kimlik bilgilerini kullanarak birden çok istemciden gelen bağlantılara izin vermez. İkinci istemci aynı kimlik bilgilerini kullanarak bağlanırsa, aracı zaten bağlı olan istemcinin bağlantısını keser.

- `username`Alan, cihaz veya modül adından türetilir ve cihazın aşağıdaki sözdizimini kullanarak ait olduğu ıothub adıdır:

  - Bir cihaz için: `<iot_hub_name>.azure-devices.net/<device_name>/?api-version=2018-06-30`

  - Bir modül için: `<iot_hub_name>.azure-devices.net/<device_name>/<module_name>/?api-version=2018-06-30`

- `password`Bağlantı paketinin alanı kimlik doğrulama moduna bağlıdır:

  - [Simetrik anahtar kimlik doğrulaması](how-to-authenticate-downstream-device.md#symmetric-key-authentication)kullanılırken, `password` alan bir SAS belirtecidir.
  - [X. 509.440 otomatik imzalı kimlik doğrulaması](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)kullanılırken `password` alan yok. Bu kimlik doğrulama modunda bir TLS kanalı gereklidir. Bir TLS bağlantısı kurmak için istemcinin 8883 numaralı bağlantı noktasına bağlanması gerekir. TLS anlaşması sırasında MQTT Aracısı bir istemci sertifikası ister. Bu sertifika, istemcinin kimliğini doğrulamak için kullanılır ve bu nedenle, `password` daha sonra bağlantı paketi gönderildiğinde alanı gerekli değildir. Hem bir istemci sertifikası hem de parola alanı gönderilmesi bir hataya yol açacağından bağlantı kapatılır. MQTT kitaplıkları ve TLS istemci kitaplıkları genellikle bir bağlantı başlatırken istemci sertifikası göndermenin bir yoludur. [İstemci kimlik doğrulaması Için x509 sertifikası](how-to-authenticate-downstream-device.md#x509-self-signed-authentication)' nı kullanarak bölümde adım adım bir örnek görebilirsiniz.

IoT Edge tarafından dağıtılan modüller, [simetrik anahtar kimlik doğrulaması](how-to-authenticate-downstream-device.md#symmetric-key-authentication) kullanır ve çevrimdışı olduğunda bıle bir SAS belirtecini programlı bir şekilde almak için yerel [IoT Edge iş yükü API](https://github.com/Azure/iotedge/blob/40f10950dc65dd955e20f51f35d69dd4882e1618/edgelet/workload/README.md) 'sini çağırabilir.

### <a name="authorization"></a>Yetkilendirme

MQTT istemcisinin kimliği IoT Edge hub 'a doğrulandıktan sonra, bağlanmak için yetkilendirilmiş olması gerekir. Bağlandıktan sonra, belirli konularda yayımlama veya abone olma yetkisi olması gerekir. Bu yetkilendirmeler, yetkilendirme ilkesi temel alınarak IoT Edge hub tarafından verilir. Yetkilendirme ilkesi, ikizi aracılığıyla IoT Edge hub 'ına gönderilen JSON yapısı olarak ifade edilen deyimler kümesidir. Yetkilendirme ilkesini yapılandırmak için bir IoT Edge hub ikizi düzenleyin.

> [!NOTE]
> Genel önizleme için yalnızca Azure CLı, MQTT Broker yetkilendirme ilkelerini içeren dağıtımları destekler. Azure portal Şu anda IoT Edge hub ikizi ve yetkilendirme ilkesini düzenlemenizi desteklemiyor.

Her yetkilendirme ilkesi beyanı,, ve ' nin birleşiminden oluşur `identities` `allow` `deny` `operations` `resources` :

- `identities` ilkenin konusunu açıkla. Bu, `client identifier` Connect paketindeki istemcileri tarafından gönderilen istemcilerle eşleşmelidir.
- `allow` ya da `deny` efektler işlemlere izin verip vermeyeceğinizi tanımlar.
- `operations` yetkilendirmede kullanılacak eylemleri tanımlayın. `mqtt:connect`, `mqtt:publish` ve `mqtt:subscribe` bugün üç adet desteklenen eylemlerdir.
- `resources` ilkenin nesnesini tanımlayın. [MQTT joker karakterlerle](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107)tanımlanmış bir konu veya konu başlığı olabilir.

Aşağıda, "rogue_client" istemcisinin bağlanmasına izin veren bir yetkilendirme ilkesine örnek verilmiştir, tüm Azure IoT istemcilerinin bağlanmasına izin verilir ve "sensor_1" konusunun konuya yayımlamasına izin verir `events/alerts` .

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "routes":{
            "Route1":"FROM /messages/* INTO $upstream"
         },
         "storeAndForwardConfiguration":{
            "timeToLiveSecs":7200
         },
         "mqttBroker":{
            "authorizations":[
               {
                  "identities":[
                     "rogue_client"
                  ],
                  "deny":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities":[
                     "sensor_1"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "events/alerts"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

Yetkilendirme ilkenizi yazarken göz önünde bulundurmanız gereken birkaç nokta vardır:

- `$edgeHub`İkizi şema sürümü 1,2 gerektirir
- Varsayılan olarak, tüm işlemler reddedilir.
- Yetkilendirme deyimleri, JSON tanımında göründükleri sırayla değerlendirilir. `identities`' A bakarak ve sonra istekle eşleşen ilk izin verme veya reddetme deyimlerini seçerek başlar. İzin verme ve reddetme deyimleri arasındaki çakışmalar durumunda reddetme deyimi kazanır.
- Yetkilendirme ilkesinde çeşitli değişkenler (örneğin, değişimler) kullanılabilir:

  - `{{iot:identity}}` o anda bağlı olan istemcinin kimliğini temsil eder. Örneğin, gibi bir cihaz kimliği `myDevice` veya gibi bir modül kimliği `myEdgeDevice/SampleModule` .
  - `{{iot:device_id}}` o anda bağlı olan aygıtın kimliğini temsil eder. Örneğin, gibi bir cihaz kimliği `myDevice` veya bir modülün çalıştığı cihaz kimliği `myEdgeDevice` .
  - `{{iot:module_id}}` o anda bağlı olan modülün kimliğini temsil eder. Bu değişken bağlı cihazlar veya gibi bir modül kimliği için boştur `SampleModule` .
  - `{{iot:this_device_id}}` yetkilendirme ilkesini çalıştıran IoT Edge cihazının kimliğini temsil eder. Örneğin, `myIoTEdgeDevice`.

IoT Hub konuları için yetkilendirmeler, Kullanıcı tanımlı konulardan biraz farklı şekilde işlenir. Anımsanması gereken önemli noktaları aşağıda bulabilirsiniz:

- Azure IoT cihazlarının veya modüllerinin IoT Edge hub MQTT aracısına bağlanmak için açık bir yetkilendirme kuralı gerekir. Varsayılan bir bağlantı yetkilendirme ilkesi aşağıda verilmiştir.
- Azure IoT cihazları veya modülleri, herhangi bir açık yetkilendirme kuralı olmadan kendi IoT Hub konularına varsayılan olarak erişebilir. Ancak, bu durumda üst/alt ilişkilerden oluşan yetkilendirmeler ve bu ilişkilerin ayarlanması gerekir. IoT Edge modüller otomatik olarak IoT Edge cihazlarının alt öğeleri olarak ayarlanır ancak cihazların IoT Edge ağ geçitlerinde açıkça bir alt öğe olarak ayarlanması gerekir.

Bu, tüm Azure IoT cihazlarının veya modüllerinin aracısına **bağlanmasını** sağlamak için kullanılabilecek varsayılan bir yetkilendirme ilkesidir:

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

IoT Edge MQTT aracısına nasıl bağlanacağınızı anladığınıza göre, önce Kullanıcı tanımlı konularda, daha sonra IoT Hub konularında ve son olarak başka bir MQTT aracısına ileti yayımlamak ve abone olmak için nasıl kullanılabileceğinizi görelim.

## <a name="publish-and-subscribe-on-user-defined-topics"></a>Kullanıcı tanımlı konularda yayımlayın ve abone olun

Bu makalede, bir konuya abone olan **sub_client** adlı bir istemciyi ve bir konuya yayımlayan **pub_client** adlı başka bir istemciyi kullanacaksınız. [Simetrik anahtar kimlik doğrulamasını](how-to-authenticate-downstream-device.md#symmetric-key-authentication) kullanacağız, ancak aynı şekilde [x. 509.440 otomatik imzalı kimlik doğrulama](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) veya [x. 509.440 CA-imzalı kimlik](./how-to-authenticate-downstream-device.md#x509-ca-signed-authentication)doğrulamasıyla yapılabilir.

### <a name="create-publisher-and-subscriber-clients"></a>Yayımcı ve abone istemcileri oluşturma

IoT Hub iki IoT cihazı oluşturun ve parolalarını alın. Terminalinizden Azure CLı kullanarak şunları yapın:

1. IoT Hub iki IoT cihazı oluşturun ve bunları IoT Edge cihazınıza ayırın:

   ```azurecli-interactive
   az iot hub device-identity create --device-id  sub_client --hub-name <iot_hub_name> --pd <edge_device_id>
   az iot hub device-identity create --device-id  pub_client --hub-name <iot_hub_name> --pd <edge_device_id>
   ```

2. SAS belirteci oluşturarak parolalarını alın:

   - Bir cihaz için:

     ```azurecli-interactive
     az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> --key-type primary --du 3600
     ```

     Burada 3600, SAS belirtecinin saniye cinsinden süresi (örneğin, 3600 = 1 saat).

   - Bir modül için:

     ```azurecli-interactive
     az iot hub generate-sas-token -n <iot_hub_name> -d <device_name> -m <module_name> --key-type primary --du 3600
     ```

     Burada 3600, SAS belirtecinin saniye cinsinden süresi (örneğin, 3600 = 1 saat).

3. Çıktıdan "SAS" anahtarına karşılık gelen değer olan SAS belirtecini kopyalayın. Yukarıdaki Azure CLı komutundan bir örnek çıktı aşağıda verilmiştir:

   ```output
   {
      "sas": "SharedAccessSignature sr=example.azure-devices.net%2Fdevices%2Fdevice_1%2Fmodules%2Fmodule_a&sig=H5iMq8ZPJBkH3aBWCs0khoTPdFytHXk8VAxrthqIQS0%3D&se=1596249190"
   }
   ```

### <a name="authorize-publisher-and-subscriber-clients"></a>Yayımcı ve abone istemcilerini yetkilendirme

Yayımcıyı ve aboneyi yetkilendirmek için, aşağıdaki yetkilendirme ilkesini içeren bir IoT Edge dağıtımında IoT Edge hub ikizi düzenleyin.

>[!NOTE]
>Şu anda, MQTT yetkilendirme özelliklerini içeren dağıtımlar yalnızca Azure CLı kullanan IoT Edge cihazlara uygulanabilir.

```json
{
   "$edgeHub":{
      "properties.desired":{
         "schemaVersion":"1.2",
         "mqttBroker":{
            "authorizations":[
               {
                  "identities": [
                     "{{iot:identity}}"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:connect"
                        ]
                     }
                  ]
               },
               {
                  "identities": [
                     "<iot_hub_name>.azure-devices.net/sub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:subscribe"
                        ],
                        "resources":[
                           "test_topic"
                        ]
                     }
                  ],
               },
               {
                  "identities": [
                     "<iot_hub_name>.azure-devices.net/pub_client"
                  ],
                  "allow":[
                     {
                        "operations":[
                           "mqtt:publish"
                        ],
                        "resources":[
                           "test_topic"
                        ]
                     }
                  ]
               }
            ]
         }
      }
   }
}
```

### <a name="symmetric-keys-authentication-without-tls"></a>TLS olmadan simetrik anahtarlar kimlik doğrulaması

#### <a name="subscribe"></a>Abonelik

**Sub_client** MQTT ISTEMCINIZI MQTT aracısına bağlayın ve `test_topic` IoT Edge cihazınızda aşağıdaki komutu çalıştırarak hizmetine abone olun:

```bash
mosquitto_sub \
    -t "test_topic" \
    -i "sub_client" \
    -u "<iot_hub_name>.azure-devices.net/sub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883
```

`<edge_device_address>`  =  `localhost` Bu örnekte, istemcinin IoT Edge ile aynı cihazda çalıştığından bu yana.

Bu ilk örnekte, TLS olmadan bağlantı noktası 1883 (MQTT) kullanıldığını unutmayın. TLS etkinleştirilmiş bağlantı noktası 8883 (MQTTS) ile başka bir örnek, sonraki bölümde gösterilmiştir.

MQTT istemcisi **sub_client** artık başlatılmış ve üzerinde gelen iletileri bekliyor `test_topic` .

#### <a name="publish"></a>Yayımlama

**Pub_client** MQTT ISTEMCINIZI MQTT aracısına bağlayın ve `test_topic` başka bir terminalden IoT Edge cihazınızda aşağıdaki komutu çalıştırarak yukarıdaki gibi bir ileti yayımlar:

```bash
mosquitto_pub \
    -t "test_topic" \
    -i "pub_client" \
    -u "<iot_hub_name>.azure-devices.net/pub_client/?api-version=2018-06-30" \
    -P "<sas_token>" \
    -h "<edge_device_address>" \
    -V mqttv311 \
    -p 1883 \
    -m "hello"
```

`<edge_device_address>`  =  `localhost` Bu örnekte, istemcinin IoT Edge ile aynı cihazda çalıştığından bu yana.

Komutu yürütürken MQTT istemcisinin **sub_client** "Merhaba" iletisini alır.

### <a name="symmetric-keys-authentication-with-tls"></a>TLS ile simetrik anahtarlar kimlik doğrulaması

TLS 'i etkinleştirmek için, bağlantı noktasının MQTT Aracısı tarafından gönderilen sertifika zincirini doğrulayabilmesi için 1883 (MQTT) ile 8883 (MQTTS) arasında değiştirilmesi gerekir. Bu işlem, [güvenli bağlantı (TLS)](#secure-connection-tls)bölümünde belirtilen adımları izleyerek yapılabilir.

İstemciler, yukarıdaki örnekteki MQTT Aracısı ile aynı cihazda çalıştığından, 1883 (MQTT) numaralı bağlantı noktasını 8883 (MQTTS) olarak değiştirerek yalnızca TLS 'i etkinleştirmek için aynı adımlar geçerlidir.

## <a name="publish-and-subscribe-on-iot-hub-topics"></a>IoT Hub yayımlama ve abone olma konuları

[Azure IoT cihaz SDK 'ları](https://github.com/Azure/azure-iot-sdks) , istemcilerin IoT Hub işlemler gerçekleştirmesine izin veriyor, ancak kullanıcı tanımlı konularda yayımlama/abone olma izni yok. IoT Hub işlemler, IoT Hub temel elemanlar protokollerinin kullanıldığı sürece Yayımla ve abone olma semantiği kullanılarak gerçekleştirilen MQTT istemcileri kullanılarak gerçekleştirilebilir. Bu protokollerin nasıl çalıştığını anlamak için, aşağıdaki kavrama ilerliyoruz.

### <a name="send-telemetry-data-to-iot-hub"></a>Telemetri verilerini IoT Hub gönder

Telemetri verilerinin IoT Hub gönderilmesi Kullanıcı tanımlı bir konu üzerinde yayımlamaya benzer, ancak belirli bir IoT Hub konusunu kullanmaktır:

- Bir cihaz için telemetri şu konuya gönderilir: `devices/<device_name>/messages/events/`
- Bir modül için telemetri şu konuya gönderilir: `devices/<device_name>/<module_name>/messages/events/`

Ayrıca, `FROM /messages/* INTO $upstream` IoT Edge MQTT aracılarından IoT Hub 'ına telemetri göndermek için gibi bir yol oluşturun. Yönlendirme hakkında daha fazla bilgi için bkz. [yolları bildirme](module-composition.md#declare-routes).

### <a name="get-twin"></a>İkizi al

Cihaz/modül ikizi alma tipik bir MQTT deseninin değildir. İstemcinin, IoT Hub sunacak bir ikizi için bir istek vermesi gerekir.

TWINS almak için, istemcinin IoT Hub belirli bir konuya abone olması gerekir `$iothub/twin/res/#` . Bu konu adı IoT Hub devralınır ve tüm istemcilerin aynı konuya abone olmaları gerekir. Cihazların veya modüllerin birbirini ikizi aldığı anlamına gelmez. IoT Hub ve IoT Edge hub, tüm cihazlar aynı konu adını dinlerken bile hangi ikizi teslim edileceğini bilir.

Abonelik yapıldıktan sonra istemci, `$iothub/twin/GET/?rid=<request_id>/#` rastgele bir tanımlayıcı olan IoT Hub belirli bir konuya bir ileti yayımlayarak ikizi için istekte bulunur  `<request_id>` . IoT Hub daha sonra yanıtını, istemcinin abone olduğu konu üzerine istenen verilerle gönderir `$iothub/twin/res/200/?rid=<request_id>` . Bu, bir istemcinin isteklerini yanıtlarla eşleştirmesine yönelik bir istekdir.

### <a name="receive-twin-patches"></a>İkizi düzeltme eklerini al

İkizi düzeltme eklerini almak için, bir istemcinin özel ıothub konusuna abone olması gerekir `$iothub/twin/PATCH/properties/desired/#` . Abonelik yapıldıktan sonra istemci, bu konuda IoT Hub tarafından gönderilen ikizi düzeltme eklerini alır.

### <a name="receive-direct-methods"></a>Doğrudan Yöntemler al

Doğrudan bir yöntem alınması, istemcinin çağrıyı aldığını doğrulamak için gereken ek ile tam TWINS almaya benzer. İlk olarak istemci IoT Hub 'ı özel konusuna abone olur `$iothub/methods/POST/#` . Bu konuda doğrudan bir yöntem alındıktan sonra, istemcinin `rid` doğrudan yöntemin alındığı alt konudan istek tanımlayıcısını ayıklayıp, son olarak IoT Hub özel konusunda bir onay iletisi yayımlaması gerekir `$iothub/methods/res/200/<request_id>` .

### <a name="send-direct-methods"></a>Doğrudan Yöntemler gönder

Doğrudan bir yöntemi göndermek bir HTTP çağrısıdır ve bu nedenle MQTT aracısına gitmez. IoT Hub 'a doğrudan yöntem göndermek için bkz. [doğrudan yöntemleri anlama ve çağırma](../iot-hub/iot-hub-devguide-direct-methods.md). Doğrudan bir yöntemi başka bir modüle yerel olarak göndermek için bkz. [Azure ıOT C# SDK doğrudan yöntem çağırma örneği](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/ModuleClient.cs#L597).

## <a name="publish-and-subscribe-between-mqtt-brokers"></a>MQTT aracıları arasında yayımlama ve abone olma

İki MQTT aracılarını bağlamak için IoT Edge hub 'ı bir MQTT Köprüsü içerir. Bir MQTT Köprüsü, başka bir MQTT aracısına çalışan bir MQTT aracısına bağlanmak için yaygın olarak kullanılır. Yalnızca yerel trafiğin bir alt kümesi, genellikle başka bir aracıya gönderilir.

> [!NOTE]
> IoT Edge hub Köprüsü şu anda yalnızca iç içe IoT Edge cihazları arasında kullanılabilir. IoT Hub tam özellikli bir MQTT Aracısı olmadığından IoT Hub 'a veri göndermek için kullanılamaz. Daha fazla IoT Hub MQTT aracı özellikleri desteğini öğrenmek için bkz. [MQTT protokolünü kullanarak IoT Hub 'ınız Ile Iletişim kurma](../iot-hub/iot-hub-mqtt-support.md). IoT Edge cihazları iç içe geçirme hakkında daha fazla bilgi için bkz. [bir aşağı akış IoT Edge cihazını Azure IoT Edge ağ geçidine bağlama](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices).

İç içe bir yapılandırmada, IoT Edge hub MQTT Köprüsü, üst MQTT aracısının bir istemcisi olarak davranır. bu nedenle, alt EdgeHub 'ın, köprünün yapılandırıldığı belirli kullanıcı tanımlı konuları yayımlamasına ve abone olmalarına izin vermek için üst EdgeHub 'da yetkilendirme kurallarının ayarlanması gerekir.

MQTT Köprüsü IoT Edge, IoT Edge hub 'ına ikizi üzerinden gönderilen bir JSON yapısı aracılığıyla yapılandırılır. MQTT Köprüsü yapılandırmak için bir IoT Edge hub ikizi düzenleyin.

> [!NOTE]
> Genel önizleme için, MQTT köprü yapılandırması içeren dağıtımları yalnızca Azure CLı destekler. Azure portal Şu anda IoT Edge hub ikizi ve MQTT Köprüsü yapılandırmasını düzenlemenizi desteklemiyor.

MQTT Köprüsü, bir IoT Edge hub MQTT aracısına birden çok dış aracıya bağlanacak şekilde yapılandırılabilir. Her dış aracı için aşağıdaki ayarlar gereklidir:

- `endpoint` , Bağlanılacak uzak MQTT aracısının adresidir. Şu anda yalnızca üst IoT Edge cihazları destekleniyor ve değişken tarafından tanımlandı `$upstream` .
- `settings` bir uç nokta için hangi konuların köprü oluşturmak gerektiğini tanımlar. Uç nokta başına birden fazla ayar olabilir ve yapılandırmak için aşağıdaki değerler kullanılır:
  - `direction`: `in` Uzak aracı konularına abone olmak veya `out` Uzak aracı konularına yayımlamak için
  - `topic`: eşleştirilecek temel konu deseninin. [MQTT joker karakterleri](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718107) , bu düzenin tanımlanması için kullanılabilir. Yerel aracıda ve uzak aracıda bu konu düzenine farklı ön ekler uygulanabilir.
  - `outPrefix`: `topic` Uzak aracıda modele uygulanan önek.
  - `inPrefix`: `topic` Yerel aracıda modele uygulanan önek.

Aşağıda, bir üst IoT Edge cihazının konu başlıkları üzerinde alınan tüm iletileri `alerts/#` aynı konularda bir alt IoT Edge cihazına yeniden yayımlayan ve bir alt IoT Edge cihazının konu başlıkları üzerinde gönderilen tüm iletileri `/local/telemetry/#` konularda bir üst IoT Edge cihaza yeniden yayımlayan IoT Edge MQTT Köprüsü yapılandırmasına bir örnek verilmiştir `/remote/messages/#` .

```json
{
    "schemaVersion": "1.2",
    "mqttBroker": {
        "bridges": [{
            "endpoint": "$upstream",
            "settings": [{
                    "direction": "in",
                    "topic": "alerts/#"
                },
                {
                    "direction": "out",
                    "topic": "#",
                    "inPrefix": "/local/telemetry/",
                    "outPrefix": "/remote/messages/"
                }
            ]
        }]
    }
}
```
IoT Edge hub MQTT köprüsünün diğer notları:
- MQTT protokolü kullanıldığında ve bir iç içe yapılandırmada IoT Edge, örneğin, belirtilen bir şekilde kullanıldığında MQTT protokolü otomatik olarak yukarı akış protokolü olarak kullanılacaktır `parent_hostname` . Yukarı akış protokolleri hakkında daha fazla bilgi edinmek için bkz. [bulut iletişimi](iot-edge-runtime.md#cloud-communication). İç içe geçmiş yapılandırmalarda daha fazla bilgi edinmek için bkz. [bir aşağı akış IoT Edge cihazını bir Azure IoT Edge ağ geçidine bağlama](how-to-connect-downstream-iot-edge-device.md#configure-iot-edge-on-devices).

## <a name="next-steps"></a>Sonraki adımlar

[IoT Edge hub 'ını anlayın](iot-edge-runtime.md#iot-edge-hub)
