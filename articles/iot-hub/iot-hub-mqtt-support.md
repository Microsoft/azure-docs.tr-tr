---
title: Azure IoT Hub MQTT desteğini anlayın | Microsoft Docs
description: Geliştirici Kılavuzu-MQTT protokolünü kullanarak IoT Hub cihaza yönelik bir uç noktaya bağlanan cihazlar için destek. Azure IoT cihaz SDK 'lerinde yerleşik MQTT desteği hakkında bilgiler içerir.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.openlocfilehash: 183b85ad8a61c76942981ebb764512b8a090b0a8
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890454"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>MQTT protokolünü kullanarak IoT Hub 'ınız ile iletişim kurma

IoT Hub, cihazların kullanarak IoT Hub cihaz uç noktalarıyla iletişim kurmasını sağlar:

* 8883 numaralı bağlantı noktasında [MQTT v 3.1.1](https://mqtt.org/)
* 443 numaralı bağlantı noktasında WebSocket üzerinden MQTT v 3.1.1.

IoT Hub tam özellikli bir MQTT Aracısı değildir ve MQTT v 3.1.1 Standard 'da belirtilen tüm davranışları desteklemez. Bu makalede, cihazların IoT Hub ile iletişim kurmak için desteklenen MQTT davranışlarını nasıl kullanabileceği açıklanır.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub ile tüm cihaz iletişimi, TLS/SSL kullanılarak güvenli hale getirilmelidir. Bu nedenle IoT Hub, 1883 numaralı bağlantı noktası üzerinden güvenli olmayan bağlantıları desteklemez.

## <a name="connecting-to-iot-hub"></a>IoT Hub bağlanılıyor

Bir cihaz, aşağıdaki seçeneklerden herhangi birini kullanarak bir IoT Hub 'ına bağlanmak için MQTT protokolünü kullanabilir.

* [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks)'larının kitaplıkları.
* MQTT protokolü doğrudan.

## <a name="using-the-device-sdks"></a>Cihaz SDK 'larını kullanma

MQTT protokolünü destekleyen [cihaz SDK 'ları](https://github.com/Azure/azure-iot-sdks) , Java, Node. js, C, C#ve Python için kullanılabilir. Cihaz SDK 'Ları, IoT Hub ile bağlantı kurmak için standart IoT Hub bağlantı dizesini kullanır. MQTT protokolünü kullanmak için, istemci protokol parametresi **MQTT**olarak ayarlanmalıdır. Varsayılan olarak, cihaz SDK 'Ları **Cleansession** bayrağı **0** olarak ayarlanmış bir IoT Hub bağlanır ve IoT Hub Ile ileti alışverişi için **QoS 1** kullanır.

Bir cihaz IoT Hub 'ına bağlandığında cihaz SDK 'Ları, cihazın IoT Hub ile ileti alışverişi için izin veren yöntemler sağlar.

Aşağıdaki tablo desteklenen her dil için kod örneklerine bağlantılar içerir ve MQTT protokolünü kullanarak IoT Hub bağlantı kurmak için kullanılacak parametreyi belirtir.

| Dil | Protokol parametresi |
| --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) |Azure-IoT-cihaz-MQTT |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |IotHubClientProtocol. MQTT |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) |MQTT_Protocol |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) |TransportType. MQTT |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) |Her zaman varsayılan olarak MQTT 'yi destekler |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Bir cihaz uygulamasını AMQP 'den MQTT 'ye geçirme

[Cihaz SDK](https://github.com/Azure/azure-iot-sdks)'larını kullanıyorsanız AMQP Ile MQTT arasında geçiş yapmak için, daha önce belirtildiği gibi istemci başlatılmasında protokol parametresinin değiştirilmesini gerektirir.

Bunu yaparken, aşağıdaki öğeleri denetlediğinizden emin olun:

* AMQP, çok sayıda koşul için hatalar döndürüyor, ancak MQTT bağlantıyı sonlandırır. Sonuç olarak, özel durum işleme mantığınızın bazı değişiklikler gerektirebilirler.

* MQTT, [buluttan cihaza iletileri](iot-hub-devguide-messaging.md)alırken *reddetme* işlemlerini desteklemez. Arka uç uygulamanızın cihaz uygulamasından bir yanıt alması gerekiyorsa, [doğrudan Yöntemler](iot-hub-devguide-direct-methods.md)kullanmayı göz önünde bulundurun.

* AMQP, Python SDK 'sında desteklenmez

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>MQTT protokolünü doğrudan kullanma (cihaz olarak)

Bir cihaz, cihaz SDK 'larını kullanalamazsanız, bağlantı noktası 8883 üzerindeki MQTT protokolünü kullanarak ortak cihaz uç noktalarına bağlanabilir. **Bağlantı** paketinde, cihaz aşağıdaki değerleri kullanmalıdır:

* **ClientID** alanı Için, **DeviceID**'yi kullanın.

* **Kullanıcı adı** alanı için, `{iothubhostname}` IoT Hub 'ının tam CNAME 'i olan `{iothubhostname}/{device_id}/?api-version=2018-06-30`kullanın.

    Örneğin, IoT Hub 'ınızın adı **contoso.Azure-Devices.net** ise ve cihazınızın adı **MyDevice01**Ise, tam **Kullanıcı adı** alanı şunları içermelidir:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* **Parola** alanı IÇIN bir SAS belirteci kullanın. SAS belirtecinin biçimi hem HTTPS hem de AMQP protokolleriyle aynıdır:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > X. 509.952 sertifikası kimlik doğrulamasını kullanıyorsanız SAS belirteç parolaları gerekmez. Daha fazla bilgi için bkz. [Azure IoT Hub 'Da X. 509.440 güvenliğini ayarlama](iot-hub-security-x509-get-started.md) ve [aşağıdaki](#tlsssl-configuration)kod yönergelerini izleyin.

  SAS belirteçleri oluşturma hakkında daha fazla bilgi için [IoT Hub güvenlik belirteçlerini kullanma](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)konusunun cihaz bölümüne bakın.

  Test edilirken, kendi kodunuza kopyalayabileceğiniz ve yapıştırabileceğiniz bir SAS belirtecini hızlıca oluşturmak için [Visual Studio Code için platformlar arası Azure IoT araçlarını](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) veya [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) aracını da kullanabilirsiniz:

### <a name="for-azure-iot-tools"></a>Azure IoT araçları için

1. Visual Studio Code sol alt köşesindeki **Azure ıOT hub cihazları** sekmesini genişletin.
  
2. Cihazınıza sağ tıklayın ve **cihaz IÇIN SAS belirteci oluştur**' u seçin.
  
3. **Süre sonu saatini** ayarlayın ve ' Enter ' tuşuna basın.
  
4. SAS belirteci oluşturulup panoya kopyalanır.

### <a name="for-device-explorer"></a>Device Explorer için

1. **Device Explorer** **Yönetim** sekmesine gidin.

2. **SAS belirteci** (sağ üst) seçeneğine tıklayın.

3. **Sastokenform**'Da, **DeviceID** açılan penceresinde cihazınızı seçin. **TTL**'nizi ayarlayın.

4. Belirtecinizi oluşturmak için **Oluştur** ' a tıklayın.

   Oluşturulan SAS belirteci aşağıdaki yapıya sahiptir:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   Bu belirtecin MQTT kullanarak bağlanmak için **parola** alanı olarak kullanılacak bölümü:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

MQTT paketleri bağlama ve bağlantısını kesme için, IoT Hub **Işlemler izleme** kanalında bir olay yayınlar. Bu olay, bağlantı sorunlarını gidermenize yardımcı olabilecek ek bilgiler içerir.

Cihaz uygulaması, **Connect** paketinde bir **yapılacak** ileti belirtebilir. Cihaz **uygulamasının, bir** telemetri iletisi **olarak iletileceği iletileri tanımlayacak** `devices/{device_id}/messages/events/` veya `devices/{device_id}/messages/events/{property_bag}` kullanması gerekir. Bu durumda, ağ bağlantısı kapatılırsa, ancak cihazdan bir **bağlantı kesme** paketi daha önce alınmıyorsa IoT Hub, **Connect** paketinde sağlanan **yapılacak** iletiyi telemetri kanalına gönderir. Telemetri kanalı, varsayılan **Olaylar** uç noktası ya da IoT Hub yönlendirme tarafından tanımlanan özel bir uç nokta olabilir. İleti, bir değeri olan **ıothub-MessageType** özelliğine sahip **olur** .

### <a name="an-example-of-c-code-using-mqtt-without-azure-iot-c-sdk"></a>Azure IoT C SDK 'Sı olmadan MQTT kullanan C kodu örneği
Bu [depoda](https://github.com/Azure-Samples/IoTMQTTSample), telemetri iletilerinin nasıl gönderileceğini gösteren birkaç C/C++ demo projesi bulacaksınız, Azure IoT C SDK 'sını kullanmadan bir IoT Hub ile olay alma. 

Bu örnekler, IoT Hub 'ında uygulanan MQTT aracısına ileti göndermek için tutulma mosquıto kitaplığını kullanır.

Bu depo şunları içerir:

**Windows için:**

• TelemetryMQTTWin32: bir Windows makinesinde oluşturulup çalıştırılan bir Azure IoT Hub 'ına telemetri iletisi göndermek için kod içerir.

• SubscribeMQTTWin32: bir Windows makinesindeki belirli bir IoT Hub 'ının olaylarına abone olmak için kod içerir.

• DeviceTwinMQTTWin32: bir Windows makinesindeki Azure IoT Hub 'ındaki bir cihazın cihaz ikizi olaylarını sorgulamak ve abone olmak için kod içerir.

• PnPMQTTWin32: bir Windows makinesinde oluşturulup çalıştırılan bir Azure IoT Hub 'ına IoT eklentisi & oynatma önizleme cihaz özellikleri içeren bir telemetri iletisi göndermek için kod içerir. IoT eklentisi & [buradan](https://docs.microsoft.com/azure/iot-pnp/overview-iot-plug-and-play) oynat

**Linux için:**

• MQTTLinux: Linux üzerinde çalıştırılacak kod ve derleme betiği içerir (WSL, Ubuntu ve Raspbian şu ana kadar sınanmıştır).

• LinuxConsoleVS2019: aynı kodu, ancak WSL 'yi hedefleyen bir VS2019 projesinde (Windows Linux alt sistemi) içerir. Bu proje, Visual Studio 'da Linux adımında çalışan kodun hatalarını ayıklamanıza olanak tanır.

**Mosquito_pub için:**

• Bu klasör, Mosquitto.org tarafından sağlanan mosquitto_pub yardımcı program aracıyla kullanılan iki örnek komut içerir.

Mosquitto_sendmessage: bir Azure IoT Hub 'ına cihaz olarak davranan basit bir kısa mesaj göndermek için.

Mosquitto_subscribe: bir Azure IoT Hub 'ında gerçekleşen olayları görmek için.


## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>MQTT protokolünü doğrudan kullanma (modül olarak)

Modül kimliğini kullanarak MQTT üzerinden IoT Hub bağlantı, cihaza benzerdir ( [yukarıda](#using-the-mqtt-protocol-directly-as-a-device)açıklanmıştır) ancak aşağıdakileri kullanmanız gerekir:

* İstemci kimliğini `{device_id}/{module_id}`olarak ayarlayın.

* Kullanıcı adı ve parolayla kimlik doğrulaması yapıyorsa, Kullanıcı adını `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` olarak ayarlayın ve parola olarak modül kimliğiyle ilişkili SAS belirtecini kullanın.

* Telemetriyi yayımlamak için konu olarak `devices/{device_id}/modules/{module_id}/messages/events/` kullanın.

* `devices/{device_id}/modules/{module_id}/messages/events/` olarak, bu konu başlığı altında kullanın.

* İkizi GET ve PATCH konuları modüller ve cihazlar için aynıdır.

* İkizi durumu konusu modüller ve cihazlar için aynıdır.

## <a name="tlsssl-configuration"></a>TLS/SSL yapılandırması

MQTT protokolünü doğrudan kullanmak için, istemciniz TLS/SSL *üzerinden bağlanmalıdır.* Bu adımı atlama denemeleri bağlantı hatalarıyla başarısız olur.

Bir TLS bağlantısı kurmak için DigiCert Baltidaha daha fazla kök sertifikasını indirmeniz ve başvurmanız gerekebilir. Bu sertifika, Azure 'un bağlantıyı güvenli hale getirmek için kullandığı bir sertifikadır. Bu sertifikayı [Azure-IoT-SDK-c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) deposunda bulabilirsiniz. Bu sertifikalar hakkında daha fazla bilgi, [DigiCert 'ın Web sitesinde](https://www.digicert.com/digicert-root-certificates.htm)bulunabilir.

Bunu, tutulma temeli tarafından [PAHO MQTT kitaplığı](https://pypi.python.org/pypi/paho-mqtt) 'nın Python sürümü kullanarak nasıl uygulayacağınızı gösteren bir örnek aşağıdaki gibi görünebilir.

İlk olarak, komut satırı ortamınızdan PAHO kitaplığı 'nı yükleme:

```cmd/sh
pip install paho-mqtt
```

Ardından, istemcisini bir Python betiğine uygulayın. Yer tutucuları aşağıdaki gibi değiştirin:

* `<local path to digicert.cer>`, DigiCert Baltidaha fazla kök sertifikasını içeren yerel bir dosyanın yoludur. Bu dosyayı, sertifika bilgilerini C için Azure IoT SDK 'sindeki [CERT. c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) ' den kopyalayarak oluşturabilirsiniz. satırları `-----BEGIN CERTIFICATE-----` ve `-----END CERTIFICATE-----`ekleyin, her satırın başındaki ve sonundaki `"` işaretlerini kaldırın ve her satırın sonundaki `\r\n` karakterleri kaldırın.

* `<device id from device registry>`, IoT Hub 'ınıza eklediğiniz bir cihazın KIMLIĞIDIR.

* `<generated SAS token>`, bu makalede daha önce açıklandığı gibi oluşturulan cihaz için bir SAS belirtecidir.

* IoT Hub 'ınızın adını `<iot hub name>`.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer file>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"


def on_connect(client, userdata, flags, rc):
    print("Device connected with result code: " + str(rc))


def on_disconnect(client, userdata, rc):
    print("Device disconnected with result code: " + str(rc))


def on_publish(client, userdata, mid):
    print("Device sent message")


client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

Önkoşullar için yükleme yönergeleri aşağıda verilmiştir.

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

Bir cihaz sertifikası kullanarak kimlik doğrulaması yapmak için yukarıdaki kod parçacığını aşağıdaki değişikliklerle güncelleştirin (bkz. sertifika tabanlı kimlik doğrulamasına hazırlanma hakkında [bir X. 509.952 CA sertifikası alma](./iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) ):

```python
# Create the client as before
# ...

# Set the username but not the password on your client
client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=None)

# Set the certificate and key paths on your client
cert_file = "<local path to your certificate file>"
key_file = "<local path to your device key file>"
client.tls_set(ca_certs=path_to_root_cert, certfile=cert_file, keyfile=key_file,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)

# Connect as before
client.connect(iot_hub_name+".azure-devices.net", port=8883)
```

## <a name="sending-device-to-cloud-messages"></a>Cihazdan buluta iletiler gönderme

Başarılı bir bağlantı yaptıktan sonra, bir cihaz `devices/{device_id}/messages/events/` veya `devices/{device_id}/messages/events/{property_bag}` bir **Konu adı**olarak IoT Hub ileti gönderebilir. `{property_bag}` öğesi, cihazın URL kodlamalı bir biçimde ek özelliklerle ileti göndermesini sağlar. Örneğin:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Bu `{property_bag}` öğesi, HTTPS protokolünde sorgu dizeleriyle aynı kodlamayı kullanır.

IoT Hub uygulamaya özgü davranışların bir listesi aşağıda verilmiştir:

* IoT Hub, QoS 2 iletilerini desteklemez. Bir cihaz uygulaması **QoS 2**ile bir ileti yayımladığında, IoT Hub ağ bağlantısını kapatır.

* IoT Hub iletileri kalıcı olarak sürdürmez. Bir cihaz, **bekletme** bayrağı 1 olarak ayarlanmış bir ileti gönderirse, IoT Hub **x-Opt-Opt** uygulama özelliğini iletiye ekler. Bu durumda, bekletme iletisini kalıcı hale getirmeniz yerine IoT Hub arka uç uygulamasına geçirir.

* IoT Hub, cihaz başına yalnızca bir etkin MQTT bağlantısını destekler. Aynı cihaz KIMLIĞI adına göre yeni MQTT bağlantısı, IoT Hub var olan bağlantıyı bırakmaya neden olur.

Daha fazla bilgi için bkz. [mesajlaşma Geliştirici Kılavuzu](iot-hub-devguide-messaging.md).

## <a name="receiving-cloud-to-device-messages"></a>Buluttan cihaza iletileri alma

IoT Hub ileti almak için, bir cihazın bir **Konu filtresi**olarak `devices/{device_id}/messages/devicebound/#` kullanarak abone olması gerekir. Konu filtresindeki çok düzeyli joker karakter `#` yalnızca cihazın konu adında ek özellikler almasına izin vermek için kullanılır. IoT Hub, alt konuların filtrelenmesi için `#` veya `?` joker karakterlerinden kullanılmasına izin vermez. IoT Hub, genel amaçlı bir yayın-Sub mesajlaşma Aracısı olmadığından, yalnızca belgelenen konu adlarını ve konu filtrelerini destekler.

Cihaz, `devices/{device_id}/messages/devicebound/#` konu filtresiyle temsil edilen cihaza özgü uç noktaya başarıyla abone olana kadar IoT Hub ileti almaz. Abonelik kurulduktan sonra cihaz, abonelik zamanından sonra kendisine gönderilen buluttan cihaza iletiler alır. Cihaz **Cleansession** bayrağı **0**olarak ayarlandıysa, abonelik farklı oturumlarda kalıcı hale getirilir. Bu durumda, cihazın bir dahaki sefer **Cleansession 0** ile bağlanması, bağlantısı kesilirken kendisine gönderilen bekleyen iletileri alır. Cihaz, **Cleansession** bayrağını **1** olarak ayarlandıysa, cihaz uç noktasına abone olana kadar IoT Hub ileti almaz.

IoT Hub **Konu adı** `devices/{device_id}/messages/devicebound/`veya ileti özellikleri olduğunda `devices/{device_id}/messages/devicebound/{property_bag}` ileti sunar. `{property_bag}`, ileti özelliklerinin URL kodlamalı anahtar/değer çiftlerini içerir. Özellik paketine yalnızca uygulama özellikleri ve Kullanıcı tarafından ayarlanabilir sistem özellikleri (örneğin, **MessageID** veya **bağıntıkimliği**) dahil edilir. Sistem özelliği adlarının ön eki **$** , uygulama özellikleri ön ek olmadan özgün özellik adını kullanır.

Bir cihaz uygulaması **QoS 2**ile bir konuya abone olduğunda IoT Hub, **suback** paketinde en fazla QoS düzey 1 ' i verir. Bundan sonra, IoT Hub QoS 1 kullanarak cihaza ileti teslim eder.

## <a name="retrieving-a-device-twins-properties"></a>Bir cihaz ikizi özelliklerini alma

İlk olarak, bir cihaz işlemin yanıtlarını almak için `$iothub/twin/res/#`abone olur. Daha sonra, `$iothub/twin/GET/?$rid={request id}`konuya boş bir ileti gönderir ve **Istek kimliği**için doldurulmuş bir değer vardır. Hizmet daha sonra, istekle aynı **Istek kimliğini** kullanarak `$iothub/twin/res/{status}/?$rid={request id}`konu başlığı altında cihaz ikizi verilerini içeren bir yanıt iletisi gönderir.

İstek KIMLIĞI, ileti özelliği değeri için geçerli bir değer olabilir, [IoT Hub mesajlaşma geliştiricisi kılavuzu 'na](iot-hub-devguide-messaging.md)göre ve durum bir tamsayı olarak onaylanır.

Yanıt gövdesi, aşağıdaki yanıt örneğinde gösterildiği gibi, Device ikizi 'ın Özellikler bölümünü içerir:

```json
{
    "desired": {
        "telemetrySendFrequency": "5m",
        "$version": 12
    },
    "reported": {
        "telemetrySendFrequency": "5m",
        "batteryLevel": 55,
        "$version": 123
    }
}
```

Olası durum kodları şunlardır:

|Durum | Açıklama |
| ----- | ----------- |
| 204 | Başarılı (hiçbir içerik döndürülmez) |
| 429 | [IoT Hub azaltma](iot-hub-devguide-quotas-throttling.md) başına çok fazla istek (daraltılmış) |
| 5 * * | Sunucu hataları |

Daha fazla bilgi için bkz. [cihaz TWINS Geliştirici Kılavuzu](iot-hub-devguide-device-twins.md).

## <a name="update-device-twins-reported-properties"></a>Cihaz ikizi bildirilen özelliklerini güncelleştir

Bildirilen özellikleri güncelleştirmek için cihaz, belirlenen bir MQTT konusunun yayını üzerinden IoT Hub bir istek yayınlar. İstek işlendikten sonra, IoT Hub güncelleştirme işleminin başarı veya başarısızlık durumunu başka bir konuya yayınlar. Bu konu, ikizi Güncelleştirme isteğinin sonucu hakkında bilgilendirmek üzere cihaza abone olabilir. MQTT 'de bu tür bir istek/yanıt etkileşimini uygulamak için, güncelleştirme isteğinde başlangıçta cihaz tarafından belirtilen istek kimliği (`$rid`) kavramından faydalanır. Bu istek kimliği, cihazın yanıtı belirli bir önceki isteğiyle ilişkilendirme izni vermek için IoT Hub yanıtı da dahil edilir.

Aşağıdaki sıra, bir cihazın IoT Hub cihaz ikizi bildirilen özelliklerini nasıl güncelleştirçalıştığını açıklar:

1. Bir cihazın, işlemin IoT Hub yanıtlarını almak için öncelikle `$iothub/twin/res/#` konusuna abone olması gerekir.

2. Bir cihaz, `$iothub/twin/PATCH/properties/reported/?$rid={request id}` konusuna cihaz ikizi güncelleştirmesini içeren bir ileti gönderir. Bu ileti bir **Istek kimliği** değeri içerir.

3. Hizmet daha sonra `$iothub/twin/res/{status}/?$rid={request id}`konu başlığı altında bildirilen özellikler koleksiyonu için yeni ETag değerini içeren bir yanıt iletisi gönderir. Bu yanıt iletisi istekle aynı **Istek kimliğini** kullanır.

İstek iletisi gövdesi, bildirilen özelliklerin yeni değerlerini içeren bir JSON belgesi içerir. JSON belgesindeki her üye, Device ikizi 'in belgesine karşılık gelen üyeyi güncelleştirir veya ekler. `null`olarak ayarlanan bir üye, kendisini içeren nesneden siler. Örneğin:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

Olası durum kodları şunlardır:

|Durum | Açıklama |
| ----- | ----------- |
| 200 | Başarılı |
| 400 | Hatalı Istek. Hatalı biçimlendirilmiş JSON |
| 429 | [IoT Hub azaltma](iot-hub-devguide-quotas-throttling.md) başına çok fazla istek (daraltılmış) |
| 5 * * | Sunucu hataları |

Aşağıdaki Python kod parçacığı, MQTT üzerinden ikizi raporlanan Özellikler güncelleştirme işlemini gösterir (PAHO MQTT Client kullanılarak):

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

Yukarıdaki ikizi bildirilen özellikler güncelleştirme işleminin başarılı olması durumunda, IoT Hub yayın iletisi şu konuya sahip olacaktır: `$iothub/twin/res/204/?$rid=1&$version=6`, burada `204`, başarıyı gösteren durum kodudur, `$rid=1` koddaki cihaz tarafından belirtilen istek KIMLIĞINE karşılık gelir ve `$version`, güncelleştirmeden sonra cihaz TWINS 'in bildirilen Özellikler bölümüne karşılık gelir.

Daha fazla bilgi için bkz. [cihaz TWINS Geliştirici Kılavuzu](iot-hub-devguide-device-twins.md).

## <a name="receiving-desired-properties-update-notifications"></a>İstenen özellikleri güncelleştirme bildirimleri alınıyor

Bir cihaz bağlandığında IoT Hub, çözüm arka ucu tarafından gerçekleştirilen güncelleştirmenin içeriğini içeren `$iothub/twin/PATCH/properties/desired/?$version={new version}`konuya bildirim gönderir. Örneğin:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

Özellik güncelleştirmelerinde olduğu gibi `null` değerler JSON nesnesi üyesinin silinmekte olduğu anlamına gelir. Ayrıca `$version`, ikizi 'in istenen özellikler bölümünün yeni sürümünü gösterir.

> [!IMPORTANT]
> IoT Hub yalnızca cihazlar bağlıyken değişiklik bildirimleri oluşturur. İstenen özellikleri IoT Hub ile cihaz uygulaması arasında eşitlenmiş tutmak için [cihaz yeniden bağlantı akışını](iot-hub-devguide-device-twins.md#device-reconnection-flow) uyguladığınızdan emin olun.

Daha fazla bilgi için bkz. [cihaz TWINS Geliştirici Kılavuzu](iot-hub-devguide-device-twins.md).

## <a name="respond-to-a-direct-method"></a>Doğrudan bir yönteme yanıt verme

İlk olarak, bir cihazın `$iothub/methods/POST/#`abone olması gerekir. IoT Hub, geçerli bir JSON ya da boş bir gövdele yöntem isteklerini konuya `$iothub/methods/POST/{method name}/?$rid={request id}`gönderir.

Yanıt vermek için, cihaz `$iothub/methods/res/{status}/?$rid={request id}`konu başlığı altında geçerli bir JSON veya boş gövde içeren bir ileti gönderir. Bu iletide, **istek kimliği** istek iletisindeki bir kimlikle eşleşmelidir ve **durum** bir tamsayı olmalıdır.

Daha fazla bilgi için bkz. [doğrudan yöntem Geliştirici Kılavuzu](iot-hub-devguide-direct-methods.md).

## <a name="additional-considerations"></a>Diğer konular

Son dikkat olarak, bulut tarafında MQTT protokol davranışını özelleştirmeniz gerekirse, [Azure IoT protokolü ağ geçidini](iot-hub-protocol-gateway.md)gözden geçirmeniz gerekir. Bu yazılım, IoT Hub doğrudan arabirimlerini sağlayan yüksek performanslı bir özel protokol ağ geçidi dağıtmanıza olanak sağlar. Azure IoT protokolü ağ geçidi, cihaz protokolünü brownfield MQTT dağıtımlarını veya diğer özel protokolleri kapsayacak şekilde özelleştirmenize olanak sağlar. Ancak bu yaklaşım, çalıştırdığınız ve özel bir protokol ağ geçidi işletebilmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

MQTT protokolü hakkında daha fazla bilgi edinmek için [MQTT belgelerine](https://mqtt.org/documentation)bakın.

IoT Hub Dağıtımınızı planlama hakkında daha fazla bilgi edinmek için bkz.:

* [IoT için Azure Sertifikalı cihaz kataloğu](https://catalog.azureiotsolutions.com/)
* [Ek protokolleri destekleme](iot-hub-protocol-gateway.md)
* [Event Hubs ile karşılaştırın](iot-hub-compare-event-hubs.md)
* [Ölçeklendirme, HA ve DR](iot-hub-scaling.md)

IoT Hub yeteneklerini daha fazla incelemek için bkz.:

* [IoT Hub Geliştirici Kılavuzu](iot-hub-devguide.md)
* [Azure IoT Edge ile uç cihazlara AI dağıtma](../iot-edge/tutorial-simulate-device-linux.md)
