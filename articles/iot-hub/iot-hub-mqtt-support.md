---
title: Azure IoT Hub MQTT desteğini anlayın | Microsoft Docs
description: MQTT protokolünü kullanarak IoT Hub cihaza yönelik bir uç noktaya bağlanan cihazlar için destek. Azure IoT cihaz SDK 'lerinde yerleşik MQTT desteği hakkında bilgiler içerir.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.custom:
- amqp
- mqtt
- 'Role: IoT Device'
- 'Role: Cloud Development'
- contperf-fy21q1
- fasttrack-edit
- iot
ms.openlocfilehash: 077b4ea6c7d4506e29673cbb4d6d89a92657b9c1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873694"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>MQTT protokolünü kullanarak IoT Hub 'ınız ile iletişim kurma

IoT Hub, cihazların kullanarak IoT Hub cihaz uç noktalarıyla iletişim kurmasını sağlar:

* 8883 numaralı bağlantı noktasında [MQTT v 3.1.1](https://mqtt.org/)
* 443 numaralı bağlantı noktasında WebSocket üzerinden MQTT v 3.1.1.

IoT Hub, tam özellikli bir MQTT aracısı değildir ve MQTT v3.1.1 standart sürümünde belirtilen tüm davranışları desteklemez. Bu makalede, cihazların IoT Hub ile iletişim kurmak için desteklenen MQTT davranışlarını nasıl kullanabileceği açıklanır.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub ile tüm cihaz iletişimi, TLS/SSL kullanılarak güvenli hale getirilmelidir. Bu nedenle IoT Hub, 1883 numaralı bağlantı noktası üzerinden güvenli olmayan bağlantıları desteklemez.

## <a name="connecting-to-iot-hub"></a>IoT Hub bağlanılıyor

Bir cihaz, aşağıdaki seçeneklerden herhangi birini kullanarak bir IoT Hub 'ına bağlanmak için MQTT protokolünü kullanabilir.

* [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks)'larının kitaplıkları.
* MQTT protokolü doğrudan.

MQTT bağlantı noktası (8883) birçok kurumsal ve eğitim ağı ortamında engellenir. Güvenlik duvarınızdaki 8883 numaralı bağlantı noktasını açamazsınız, Web Yuvaları üzerinden MQTT kullanmanızı öneririz. Web Yuvaları üzerinden MQTT, ağ ortamlarında neredeyse her zaman açık olan 443 numaralı bağlantı noktası üzerinden iletişim kurar. Azure IoT SDK 'larını kullanırken, Web Sockets protokolleri üzerinden MQTT ve MQTT 'yi belirtmeyi öğrenmek için bkz. [cihaz SDK 'Larını kullanma](#using-the-device-sdks).

## <a name="using-the-device-sdks"></a>Cihaz SDK 'larını kullanma

MQTT protokolünü destekleyen [cihaz SDK 'ları](https://github.com/Azure/azure-iot-sdks) , Java, Node.js, C, C# ve Python için kullanılabilir. Cihaz SDK 'Ları, IoT Hub ile bağlantı kurmak için standart IoT Hub bağlantı dizesini kullanır. MQTT protokolünü kullanmak için, istemci protokol parametresi **MQTT** olarak ayarlanmalıdır. İstemci protokol parametresinde, Web Yuvaları üzerinden MQTT ' i de belirtebilirsiniz. Varsayılan olarak, cihaz SDK 'Ları **Cleansession** bayrağı **0** olarak ayarlanmış bir IoT Hub bağlanır ve IoT Hub Ile ileti alışverişi için **QoS 1** kullanır. Daha hızlı ileti alışverişi için **QoS 0** ' ı yapılandırmak mümkün olsa da, teslimin garanti veya onaylanmadığını unutmayın. Bu nedenle, **QoS 0** genellikle "yangın ve unut" olarak adlandırılır.

Bir cihaz IoT Hub 'ına bağlandığında cihaz SDK 'Ları, cihazın IoT Hub ile ileti alışverişi için izin veren yöntemler sağlar.

Aşağıdaki tablo desteklenen her dil için kod örneklerine bağlantılar içerir ve MQTT veya Web soketi üzerinden MQTT kullanarak IoT Hub bağlantı kurmak için kullanılacak parametreyi belirtir.

| Dil | MQTT protokol parametresi | Web Sockets protokol parametresi üzerinden MQTT
| --- | --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) | Azure-IoT-Device-MQTT. MQTT | Azure-IoT-Device-MQTT. MqttWs |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |[IotHubClientProtocol](/java/api/com.microsoft.azure.sdk.iot.device.iothubclientprotocol). MQTT | IotHubClientProtocol.MQTT_WS |
| [,](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) | [MQTT_Protocol](/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-h/mqtt-protocol) | [MQTT_WebSocket_Protocol](/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-websockets-h/mqtt-websocket-protocol) |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) | [TransportType](/dotnet/api/microsoft.azure.devices.client.transporttype). MQTT | TransportType. MQTT, MQTT başarısız olursa Web Yuvaları üzerinden MQTT 'e geri döner. Yalnızca Web Yuvaları üzerinden MQTT belirtmek için TransportType.Mqtt_WebSocket_Only kullanın |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) | Varsayılan olarak MQTT 'yi destekler | `websockets=True`İstemciyi oluşturmak için çağrıya ekleyin |

Aşağıdaki parça, Azure IoT Node.js SDK kullanılırken Web soketi üzerinden MQTT 'in nasıl kullanılacağını göstermektedir:

```javascript
var Client = require('azure-iot-device').Client;
var Protocol = require('azure-iot-device-mqtt').MqttWs;
var client = Client.fromConnectionString(deviceConnectionString, Protocol);
```

Aşağıdaki parça, Azure IoT Python SDK kullanılırken Web soketi üzerinden MQTT 'in nasıl kullanılacağını göstermektedir:

```python
from azure.iot.device.aio import IoTHubDeviceClient
device_client = IoTHubDeviceClient.create_from_connection_string(deviceConnectionString, websockets=True)
```

### <a name="default-keep-alive-timeout"></a>Varsayılan etkin tutma zaman aşımı

İstemci/IoT Hub bağlantısının etkin kalmasını sağlamak için, hem hizmet hem de istemci birbirlerine *sürekli olarak canlı* bir ping gönderin. IoT SDK kullanan istemci, aşağıdaki tabloda tanımlanan aralıkta etkin tutma gönderir:

|Dil  |Varsayılan etkin tut aralığı  |Yapılandırılabilir  |
|---------|---------|---------|
|Node.js     |   180 saniye      |     No    |
|Java     |    230 saniye     |     No    |
|C     | 240 saniye |  [Evet](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md#mqtt-transport)   |
|C#     | 300 saniye |  [Evet](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/Transport/Mqtt/MqttTransportSettings.cs#L89)   |
|Python   | 60 saniye |  No   |

[MQTT belirtiminin](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718081)ardından IoT Hub etkin tut ping aralığı, istemci canlı tutma değerinin 1,5 katına kaydedilir. Ancak, tüm Azure hizmetleri Azure yük dengeleyici TCP boşta kalma zaman aşımı süresi olan 29,45 (1767 saniye) ile birlikte IoT Hub en fazla sunucu tarafı zaman aşımını 29,45 dakikaya (saniye) sınırlar. 

Örneğin, Java SDK 'sını kullanan bir cihaz, etkin tut ping komutunu gönderir ve ardından ağ bağlantısını kaybeder. 230 saniye sonra, cihaz çevrimdışı olduğu için etkin tut ping komutunu yok. Ancak IoT Hub bağlantıyı hemen kapatmaz; `(230 * 1.5) - 230 = 115` cihazın bağlantısını kesmeden önce, [404104 Deviceconnectioncloseduzaktan](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)hatası ile daha önce bir saniye bekler. 

Ayarlayabileceğiniz maksimum istemci etkin tutma değeri `1767 / 1.5 = 1177` saniyedir. Tüm trafik etkin tut ' a sıfırlanır. Örneğin, başarılı bir SAS belirteci yenilemesi canlı tutmayı sıfırlar.

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Bir cihaz uygulamasını AMQP 'den MQTT 'ye geçirme

[Cihaz SDK](https://github.com/Azure/azure-iot-sdks)'larını kullanıyorsanız AMQP 'den MQTT 'ye geçiş yapmak, daha önce belirtildiği gibi, istemci başlatmasında protokol parametresinin değiştirilmesini gerektirir.

Bunu yaparken, aşağıdaki öğeleri denetlediğinizden emin olun:

* AMQP, çok sayıda koşul için hatalar döndürüyor, ancak MQTT bağlantıyı sonlandırır. Sonuç olarak, özel durum işleme mantığınızın bazı değişiklikler gerektirebilirler.

* MQTT, [buluttan cihaza iletileri](iot-hub-devguide-messaging.md)alırken *reddetme* işlemlerini desteklemez. Arka uç uygulamanızın cihaz uygulamasından bir yanıt alması gerekiyorsa, [doğrudan Yöntemler](iot-hub-devguide-direct-methods.md)kullanmayı göz önünde bulundurun.

* AMQP, Python SDK 'sında desteklenmez.

## <a name="example-in-c-using-mqtt-without-an-azure-iot-sdk"></a>Azure IoT SDK olmadan MQTT kullanarak C 'de örnek

[IOT MQTT örnek deposunda](https://github.com/Azure-Samples/IoTMQTTSample), telemetri iletilerinin nasıl gönderileceğini ve Azure ıOT C SDK 'sını kullanmadan bir IoT Hub ile olay almayı gösteren birkaç C/C++ demo projesi bulacaksınız. 

Bu örnekler, IoT Hub 'ında uygulanan MQTT aracısına ileti göndermek için tutulma mosquıto kitaplığını kullanır.

[Azure ıot Tak ve kullan](../iot-pnp/overview-iot-plug-and-play.md) kurallarını kullanmak üzere örnekleri nasıl uyarlayacağınızı öğrenmek için bkz. [öğretici-MQTT kullanarak IoT Tak ve kullan cihaz istemcisi geliştirme](../iot-pnp/tutorial-use-mqtt.md).

Bu depo şunları içerir:

**Windows için:**

* TelemetryMQTTWin32: bir Windows makinesinde oluşturulup çalıştırılan bir Azure IoT Hub 'ına telemetri iletisi göndermek için kod içerir.

* SubscribeMQTTWin32: bir Windows makinesindeki belirli bir IoT Hub 'ının olaylarına abone olmak için kod içerir.

* DeviceTwinMQTTWin32: bir Windows makinesindeki Azure IoT Hub 'ındaki bir cihazın cihaz ikizi olaylarını sorgulamak ve abone olmak için kod içerir.

* PnPMQTTWin32: bir Windows makinesinde oluşturulup çalıştırılan bir Azure IoT Hub 'ına IoT Tak ve Kullan cihaz özelliklerine sahip bir telemetri iletisi göndermek için kod içerir. IoT üzerinde daha fazla bilgi edinebilirsiniz [Tak ve kullan](../iot-pnp/overview-iot-plug-and-play.md)

**Linux için:**

* MQTTLinux: Linux üzerinde çalıştırılacak kod ve derleme betiği içerir (WSL, Ubuntu ve Raspbian şu ana kadar sınanmıştır).

* LinuxConsoleVS2019: aynı kodu içerir, ancak WSL 'yi hedefleyen bir VS2019 projesi (Windows Linux alt sistemi). Bu proje, Visual Studio 'da Linux adımında çalışan kodun hatalarını ayıklamanıza olanak tanır.

**Mosquitto_pub için:**

Bu klasör, Mosquitto.org tarafından sağlanan mosquitto_pub yardımcı program aracıyla kullanılan iki örnek komut içerir.

* Mosquitto_sendmessage: bir Azure IoT Hub 'ına cihaz olarak davranan basit bir kısa mesaj göndermek için.

* Mosquitto_subscribe: bir Azure IoT Hub 'ında gerçekleşen olayları görmek için.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>MQTT protokolünü doğrudan kullanma (cihaz olarak)

Bir cihaz, cihaz SDK 'larını kullanalamazsanız, bağlantı noktası 8883 üzerindeki MQTT protokolünü kullanarak ortak cihaz uç noktalarına bağlanabilir. **Bağlantı** paketinde, cihaz aşağıdaki değerleri kullanmalıdır:

* **ClientID** alanı Için, **DeviceID**'yi kullanın.

* **Kullanıcı adı** alanı için, `{iothubhostname}/{device_id}/?api-version=2018-06-30` , `{iothubhostname}` IoT Hub 'ının tam CNAME 'i olan öğesini kullanın.

    Örneğin, IoT Hub 'ınızın adı **contoso.Azure-Devices.net** ise ve cihazınızın adı **MyDevice01** Ise, tam **Kullanıcı adı** alanı şunları içermelidir:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

    Alana api-Version eklenmesi önemle önerilir. Aksi takdirde, beklenmeyen davranışlara neden olabilir. 
    
* **Parola** alanı IÇIN bir SAS belirteci kullanın. SAS belirtecinin biçimi hem HTTPS hem de AMQP protokolleriyle aynıdır:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > X. 509.952 sertifikası kimlik doğrulamasını kullanıyorsanız SAS belirteç parolaları gerekmez. Daha fazla bilgi için bkz. [Azure IoT Hub 'Da X. 509.440 güvenliğini ayarlama](iot-hub-security-x509-get-started.md) ve [TLS/SSL yapılandırma bölümünde](#tlsssl-configuration)kod yönergelerini izleme.

  SAS belirteçleri oluşturma hakkında daha fazla bilgi için [IoT Hub güvenlik belirteçlerini kullanma](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)konusunun cihaz bölümüne bakın.

  Test edilirken, kendi kodunuza kopyalayabilir ve yapıştırabileceğiniz bir SAS belirtecini hızlıca oluşturmak için, [Visual Studio Code için platformlar arası Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 'Nı veya CLI uzantısı komutunu [az IoT Hub Generate-SAS-Token](/cli/azure/iot/hub#az_iot_hub_generate_sas_token) komutunu da kullanabilirsiniz.

### <a name="for-azure-iot-tools"></a>Azure IoT araçları için

1. Visual Studio Code sol alt köşesindeki **Azure ıOT hub cihazları** sekmesini genişletin.
  
2. Cihazınıza sağ tıklayın ve **cihaz IÇIN SAS belirteci oluştur**' u seçin.
  
3. **Süre sonu saatini** ayarlayın ve ' Enter ' tuşuna basın.
  
4. SAS belirteci oluşturulup panoya kopyalanır.

   Oluşturulan SAS belirteci aşağıdaki yapıya sahiptir:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   Bu belirtecin MQTT kullanarak bağlanmak için **parola** alanı olarak kullanılacak bölümü:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

MQTT paketleri bağlama ve bağlantısını kesme için, IoT Hub **Işlemler izleme** kanalında bir olay yayınlar. Bu olay, bağlantı sorunlarını gidermenize yardımcı olabilecek ek bilgiler içerir.

Cihaz uygulaması, **Connect** paketinde bir **yapılacak** ileti belirtebilir. Cihaz uygulaması, veya ' i kullanarak, `devices/{device_id}/messages/events/` `devices/{device_id}/messages/events/{property_bag}` bir telemetri iletisi olarak Iletilecek iletiler **olacak** şekilde **görüntülenecek** konu adı olarak kullanılmalıdır. Bu durumda, ağ bağlantısı kapatılırsa, ancak cihazdan bir **bağlantı kesme** paketi daha önce alınmıyorsa IoT Hub, **Connect** paketinde sağlanan **yapılacak** iletiyi telemetri kanalına gönderir. Telemetri kanalı, varsayılan **Olaylar** uç noktası ya da IoT Hub yönlendirme tarafından tanımlanan özel bir uç nokta olabilir. İleti, bir değeri olan **ıothub-MessageType** özelliğine sahip **olur** .

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>MQTT protokolünü doğrudan kullanma (modül olarak)

Modül kimliğini kullanarak MQTT üzerinden IoT Hub bağlantı, cihaza benzerdir ( [MQTT protokolünü doğrudan bir cihaz olarak kullanma bölümünde](#using-the-mqtt-protocol-directly-as-a-device)açıklanmıştır), ancak aşağıdakileri kullanmanız gerekir:

* İstemci KIMLIĞINI olarak ayarlayın `{device_id}/{module_id}` .

* Kullanıcı adı ve parolayla kimlik doğrulaması yapıyorsa, Kullanıcı adını olarak ayarlayın `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` ve parola olarak modül kimliğiyle ILIŞKILI SAS belirtecini kullanın.

* `devices/{device_id}/modules/{module_id}/messages/events/`Telemetri yayımlamak için konu olarak kullanın.

* `devices/{device_id}/modules/{module_id}/messages/events/`Bu konu başlığı altında kullanın.

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

* `<local path to digicert.cer>` , DigiCert Baltidaha fazla kök sertifikasını içeren yerel bir dosyanın yoludur. Bu dosyayı, sertifika bilgilerini C için Azure IoT SDK 'sindeki [CERT. c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) ' den kopyalayarak oluşturabilirsiniz. satırları `-----BEGIN CERTIFICATE-----` ve `-----END CERTIFICATE-----` `"` her satırın başındaki ve sonundaki işaretleri kaldırın ve `\r\n` her satırın sonundaki karakterleri kaldırın.

* `<device id from device registry>` , IoT Hub 'ınıza eklediğiniz bir cihazın KIMLIĞIDIR.

* `<generated SAS token>` , bu makalede daha önce açıklandığı gibi oluşturulan cihaz için bir SAS belirtecidir.

* `<iot hub name>` IoT Hub 'ınızın adı.

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

Başarılı bir bağlantı yaptıktan sonra, bir cihaz IoT Hub `devices/{device_id}/messages/events/` `devices/{device_id}/messages/events/{property_bag}` bir **Konu adı** olarak veya kullanarak ileti gönderebilir. `{property_bag}`Öğesi, cihazın URL kodlamalı bir biçimde ek özelliklerle ileti göndermesini sağlar. Örnek:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Bu `{property_bag}` öğe, https protokolünde sorgu dizeleriyle aynı kodlamayı kullanır.

IoT Hub uygulamaya özgü davranışların bir listesi aşağıda verilmiştir:

* IoT Hub, QoS 2 iletilerini desteklemez. Bir cihaz uygulaması **QoS 2** ile bir ileti yayımladığında, IoT Hub ağ bağlantısını kapatır.

* IoT Hub iletileri kalıcı olarak sürdürmez. Bir cihaz, **bekletme** bayrağı 1 olarak ayarlanmış bir ileti gönderirse IoT Hub **MQTT** uygulama özelliğini iletiye ekler. Bu durumda, bekletme iletisini kalıcı hale getirmeniz yerine IoT Hub arka uç uygulamasına geçirir.

* IoT Hub, cihaz başına yalnızca bir etkin MQTT bağlantısını destekler. Aynı cihaz KIMLIĞI adına yapılan tüm yeni MQTT bağlantıları, IoT Hub var olan bağlantıyı başlatmasına neden olur ve **400027 Connectionforcefullyıclosedonnewconnection** IoT Hub günlüklerde oturum açar


Daha fazla bilgi için bkz. [mesajlaşma Geliştirici Kılavuzu](iot-hub-devguide-messaging.md).

## <a name="receiving-cloud-to-device-messages"></a>Buluttan cihaza iletileri alma

IoT Hub ileti almak için, bir cihazın `devices/{device_id}/messages/devicebound/#` bir **Konu filtresi** olarak kullanarak abone olması gerekir. Konu filtresindeki çok düzeyli joker karakter `#` Yalnızca cihazın konu adında ek özellikler almasına izin vermek için kullanılır. IoT Hub, alt `#` `?` konuların filtrelenmesi için veya joker karakterlerinden kullanılmasına izin vermez. IoT Hub, genel amaçlı bir yayın-Sub mesajlaşma Aracısı olmadığından, yalnızca belgelenen konu adlarını ve konu filtrelerini destekler.

Cihaz, konu filtresiyle temsil edilen cihaza özgü uç noktaya başarıyla abone olana kadar IoT Hub ileti almaz `devices/{device_id}/messages/devicebound/#` . Abonelik kurulduktan sonra cihaz, abonelik zamanından sonra kendisine gönderilen buluttan cihaza iletiler alır. Cihaz **Cleansession** bayrağı **0** olarak ayarlandıysa, abonelik farklı oturumlarda kalıcı hale getirilir. Bu durumda, cihazın bir dahaki sefer **Cleansession 0** ile bağlanması, bağlantısı kesilirken kendisine gönderilen bekleyen iletileri alır. Cihaz, **Cleansession** bayrağını **1** olarak ayarlandıysa, cihaz uç noktasına abone olana kadar IoT Hub ileti almaz.

IoT Hub, **Konu adı** ile `devices/{device_id}/messages/devicebound/` veya `devices/{device_id}/messages/devicebound/{property_bag}` ileti özellikleri olduğunda iletileri teslim eder. `{property_bag}` ileti özelliklerinin URL kodlamalı anahtar/değer çiftlerini içerir. Özellik paketine yalnızca uygulama özellikleri ve Kullanıcı tarafından ayarlanabilir sistem özellikleri (örneğin, **MessageID** veya **bağıntıkimliği**) dahil edilir. Sistem özelliği adlarının öneki vardır **$** , uygulama özellikleri ön ek olmadan özgün özellik adını kullanır. Özellik paketi biçimi hakkında daha fazla bilgi için bkz. [cihazdan buluta Iletiler gönderme](#sending-device-to-cloud-messages).

Buluttan cihaza iletilerde, özellik çantasındaki değerler aşağıdaki tabloda olarak temsil edilir:

| Özellik değeri | İmle | Description |
|----|----|----|
| `null` | `key` | Özellik paketinde yalnızca anahtar görünür |
| boş dize | `key=` | Bu anahtar, değer olmadan eşittir işareti izler |
| null olmayan, boş olmayan değer | `key=value` | Ardından bir eşittir işareti ve değeri |

Aşağıdaki örnek, üç uygulama özelliği içeren bir özellik paketini gösterir: **Prop1** değeri ile `null` **Prop2**, boş bir dize (""); ve "bir String" değeri ile **Prop3** .

```mqtt
/?prop1&prop2=&prop3=a%20string
```

Bir cihaz uygulaması **QoS 2** ile bir konuya abone olduğunda IoT Hub, **suback** paketinde en fazla QoS düzey 1 ' i verir. Bundan sonra, IoT Hub QoS 1 kullanarak cihaza ileti teslim eder.

## <a name="retrieving-a-device-twins-properties"></a>Bir cihaz ikizi özelliklerini alma

İlk olarak, bir cihaz, `$iothub/twin/res/#` işlemin yanıtlarını almak için uygulamasına abone olur. Daha sonra, `$iothub/twin/GET/?$rid={request id}` **istek kimliği** için doldurulmuş bir değer ile konuya boş bir ileti gönderir. Bu hizmet daha sonra, `$iothub/twin/res/{status}/?$rid={request id}` istekle aynı **istek kimliğini** kullanarak, konusunda cihaz ikizi verilerini içeren bir yanıt iletisi gönderir.

İstek KIMLIĞI, [IoT Hub mesajlaşma geliştiricisi kılavuzu](iot-hub-devguide-messaging.md)' na göre bir ileti özelliği değeri için geçerli bir değer olabilir ve durum bir tamsayı olarak onaylanır.

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
| 200 | Başarılı |
| 429 | [IoT Hub azaltma](iot-hub-devguide-quotas-throttling.md) başına çok fazla istek (daraltılmış) |
| 5 * * | Sunucu hataları |

Daha fazla bilgi için bkz. [cihaz TWINS Geliştirici Kılavuzu](iot-hub-devguide-device-twins.md).

## <a name="update-device-twins-reported-properties"></a>Cihaz ikizi bildirilen özelliklerini güncelleştir

Bildirilen özellikleri güncelleştirmek için cihaz, belirlenen bir MQTT konusunun yayını üzerinden IoT Hub bir istek yayınlar. İstek işlendikten sonra, IoT Hub güncelleştirme işleminin başarı veya başarısızlık durumunu başka bir konuya yayınlar. Bu konu, ikizi Güncelleştirme isteğinin sonucu hakkında bilgilendirmek üzere cihaza abone olabilir. MQTT 'de bu tür bir istek/yanıt etkileşimini uygulamak için, `$rid` ilk olarak cihaz güncelleştirme isteğinde belirtilen Istek kimliği () kavramının faydalanır. Bu istek KIMLIĞI, cihazın yanıtı belirli bir önceki isteğiyle ilişkilendirme izni vermek için IoT Hub yanıtı da dahil edilir.

Aşağıdaki sıra, bir cihazın IoT Hub cihaz ikizi bildirilen özelliklerini nasıl güncelleştirçalıştığını açıklar:

1. Bir cihazın `$iothub/twin/res/#` IoT Hub, işlemin yanıtlarını almak için öncelikle bu konuya abone olması gerekir.

2. Bir cihaz, konuya cihaz ikizi güncelleştirmesini içeren bir ileti gönderir `$iothub/twin/PATCH/properties/reported/?$rid={request id}` . Bu ileti bir **Istek kimliği** değeri içerir.

3. Bu hizmet daha sonra, konusunun bildirilen özellikler koleksiyonu için yeni ETag değerini içeren bir yanıt iletisi gönderir `$iothub/twin/res/{status}/?$rid={request id}` . Bu yanıt iletisi istekle aynı **Istek kimliğini** kullanır.

İstek iletisi gövdesi, bildirilen özelliklerin yeni değerlerini içeren bir JSON belgesi içerir. JSON belgesindeki her üye, Device ikizi 'in belgesine karşılık gelen üyeyi güncelleştirir veya ekler. Üye `null` , içerilen nesneden üyeyi siler. Örnek:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

Olası durum kodları şunlardır:

|Durum | Açıklama |
| ----- | ----------- |
| 204 | Başarılı (hiçbir içerik döndürülmez) |
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

Yukarıdaki ikizi ile bildirilen özellikler güncelleştirme işleminin başarılı olması durumunda, IoT Hub yayın iletisi şu konuya sahip olacaktır: `$iothub/twin/res/204/?$rid=1&$version=6` , burada `204` başarıyı gösteren durum kodudur, `$rid=1` koddaki cihaz tarafından belirtilen istek kimliğine karşılık gelir ve `$version` güncelleştirmeden sonra cihaz TWINS 'in bildirilen özellikler bölümünün sürümüne karşılık gelir.

Daha fazla bilgi için bkz. [cihaz TWINS Geliştirici Kılavuzu](iot-hub-devguide-device-twins.md).

## <a name="receiving-desired-properties-update-notifications"></a>İstenen özellikleri güncelleştirme bildirimleri alınıyor

Bir cihaz bağlandığında IoT Hub, `$iothub/twin/PATCH/properties/desired/?$version={new version}` çözüm arka ucu tarafından gerçekleştirilen güncelleştirmenin içeriğini içeren konuya bildirim gönderir. Örnek:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

Özellik güncelleştirmelerinde olduğu gibi, `null` değerler JSON nesnesi üyesinin silinmekte olduğu anlamına gelir. Ayrıca, `$version` ikizi 'in istenen özellikler bölümünün yeni sürümünü gösterir.

> [!IMPORTANT]
> IoT Hub yalnızca cihazlar bağlıyken değişiklik bildirimleri oluşturur. İstenen özellikleri IoT Hub ile cihaz uygulaması arasında eşitlenmiş tutmak için [cihaz yeniden bağlantı akışını](iot-hub-devguide-device-twins.md#device-reconnection-flow) uyguladığınızdan emin olun.

Daha fazla bilgi için bkz. [cihaz TWINS Geliştirici Kılavuzu](iot-hub-devguide-device-twins.md).

## <a name="respond-to-a-direct-method"></a>Doğrudan bir yönteme yanıt verme

İlk olarak, bir cihazın abone olması gerekir `$iothub/methods/POST/#` . IoT Hub `$iothub/methods/POST/{method name}/?$rid={request id}` , geçerli BIR JSON ya da boş bir gövdele Yöntem istekleri konuya gönderir.

Yanıt vermek için cihaz, konuya geçerli bir JSON veya boş gövde içeren bir ileti gönderir `$iothub/methods/res/{status}/?$rid={request id}` . Bu iletide, **istek kimliği** istek iletisindeki bir kimlikle eşleşmelidir ve **durum** bir tamsayı olmalıdır.

Daha fazla bilgi için [doğrudan yöntem Geliştirici Kılavuzu](iot-hub-devguide-direct-methods.md)' na bakın.

## <a name="additional-considerations"></a>Diğer konular

Son dikkat olarak, bulut tarafında MQTT protokol davranışını özelleştirmeniz gerekirse, [Azure IoT protokolü ağ geçidini](iot-hub-protocol-gateway.md)gözden geçirmeniz gerekir. Bu yazılım, IoT Hub doğrudan arabirimlerini sağlayan yüksek performanslı bir özel protokol ağ geçidi dağıtmanıza olanak sağlar. Azure IoT protokolü ağ geçidi, cihaz protokolünü brownfield MQTT dağıtımlarını veya diğer özel protokolleri kapsayacak şekilde özelleştirmenize olanak sağlar. Ancak bu yaklaşım, çalıştırdığınız ve özel bir protokol ağ geçidi işletebilmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

MQTT protokolü hakkında daha fazla bilgi edinmek için [MQTT belgelerine](https://mqtt.org/)bakın.

IoT Hub Dağıtımınızı planlama hakkında daha fazla bilgi edinmek için bkz.:

* [IoT için Microsoft Azure Sertifikalı cihaz kataloğu](https://devicecatalog.azure.com/)
* [Ek protokolleri destekleme](iot-hub-protocol-gateway.md)
* [Event Hubs ile karşılaştırın](iot-hub-compare-event-hubs.md)
* [Ölçeklendirme, HA ve DR](iot-hub-scaling.md)

IoT Hub yeteneklerini daha fazla incelemek için bkz.:

* [IoT Hub Geliştirici Kılavuzu](iot-hub-devguide.md)
* [Azure IOT Edge ile sınır cihazlarına Al dağıtma](../iot-edge/quickstart-linux.md)
