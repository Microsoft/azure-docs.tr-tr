---
title: Azure IoT Hub AMQP desteğini anlayın | Microsoft Docs
description: Geliştirici Kılavuzu-AMQP protokolünü kullanarak cihaza yönelik ve hizmete yönelik uç noktalara IoT Hub bağlanan cihazlar için destek. Azure IoT cihaz SDK 'lerinde yerleşik AMQP desteği hakkında bilgiler içerir.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: robinsh
ms.openlocfilehash: 7f7e957502419b766f7da63048e8168192ea20da
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286640"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>AMQP protokolünü kullanarak IoT Hub 'ınız ile iletişim kurma

Azure IoT Hub, cihaza yönelik ve hizmete yönelik uç noktalar aracılığıyla çeşitli işlevler sunmak için [Oassıs gelişmiş ileti sıraya alma Protokolü (AMQP) sürüm 1,0](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) ' ü destekler. Bu belgede IoT Hub işlevselliği kullanmak için bir IoT Hub 'ına bağlanmak üzere AMQP istemcilerinin kullanımı açıklanmaktadır.

## <a name="service-client"></a>Hizmet istemcisi

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>IoT Hub 'ına bağlanma ve kimlik doğrulama (hizmet istemcisi)

İstemci, AMQP kullanarak bir IoT Hub 'ına bağlanmak için, [talep tabanlı güvenlik (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) veya [Basit kimlik doğrulama ve güvenlik katmanı (SASL) kimlik doğrulamasını](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)kullanabilir.

Hizmet istemcisi için aşağıdaki bilgiler gereklidir:

| Bilgi | Değer |
|-------------|--------------|
| IoT Hub ana bilgisayar adı | `<iot-hub-name>.azure-devices.net` |
| Anahtar adı | `service` |
| Erişim anahtarı | Hizmetle ilişkili birincil veya ikincil anahtar |
| Paylaşılan erişim imzası | Şu biçimdeki kısa süreli bir paylaşılan erişim imzası: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`. Bu imzayı oluşturma kodunu almak için, bkz. [IoT Hub erişimi denetleme](./iot-hub-devguide-security.md#security-token-structure).

Aşağıdaki kod parçacığı, bir gönderen bağlantısı aracılığıyla bir IoT Hub 'ına bağlanmak için [Python 'Da Uıamqp kitaplığını](https://github.com/Azure/azure-uamqp-python) kullanır.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>'  # example: '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoke-cloud-to-device-messages-service-client"></a>Buluttan cihaza iletileri çağırma (hizmet istemcisi)

Hizmet ve IoT Hub ile cihaz ile IoT Hub arasındaki buluttan cihaza ileti alışverişi hakkında bilgi edinmek için bkz. [IoT Hub 'ınızdaki buluttan cihaza Ileti gönderme](iot-hub-devguide-messages-c2d.md). Hizmet istemcisi, aşağıdaki tabloda açıklandığı gibi cihazlarından daha önce gönderilen iletiler için ileti göndermek ve geri bildirim almak üzere iki bağlantı kullanır:

| Oluşturan | Bağlantı türü | Bağlantı yolu | Açıklama |
|------------|-----------|-----------|-------------|
| Hizmet | Gönderen bağlantısı | `/messages/devicebound` | Cihazlara hedeflenen buluttan cihaza iletiler, hizmet tarafından bu bağlantıya gönderilir. Bu bağlantı üzerinden gönderilen iletilerin `To` özelliği hedef cihazın alıcı bağlantı yoluna ayarlı `/devices/<deviceID>/messages/devicebound` ' dir. |
| Hizmet | Alıcı bağlantısı | `/messages/serviceBound/feedback` | Hizmet tarafından bu bağlantıda alınan cihazlardan gelen tamamlama, reddetme ve bırakma geri bildirim iletileri. Geri bildirim iletileri hakkında daha fazla bilgi için bkz. [IoT Hub 'ından buluttan cihaza Ileti gönderme](./iot-hub-devguide-messages-c2d.md#message-feedback). |

Aşağıdaki kod parçacığı, bir buluttan cihaza ileti oluşturmayı ve [Python 'Da Uamqp kitaplığını](https://github.com/Azure/azure-uamqp-python)kullanarak bir cihaza nasıl gönderileceğini gösterir.

```python
import uuid
# Create a message and set message property 'To' to the device-bound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full'  # Alternative values are 'positive', 'negative', and 'none'
app_props = {'iothub-ack': ack}
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props,
                    application_properties=app_props)

# Send the message by using the send client that you created and connected to the IoT hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if it's not needed
send_client.close()
```

Hizmet istemcisi geri bildirim almak için bir alıcı bağlantısı oluşturur. Aşağıdaki kod parçacığı, [Python 'Da Uamqp kitaplığı](https://github.com/Azure/azure-uamqp-python)kullanarak bir bağlantının nasıl oluşturulacağını gösterir:

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Re-create the URI by using the preceding feedback path and authenticate it
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
    print('received a message')
    # Check content_type in message property to identify feedback messages coming from device
    if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
        msg_body_raw = msg.get_data()
        msg_body_str = ''.join(msg_body_raw)
        msg_body = json.loads(msg_body_str)
        print(json.dumps(msg_body, indent=2))
        print('******************')
        for feedback in msg_body:
            print('feedback received')
            print('\tstatusCode: ' + str(feedback['statusCode']))
            print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
            print('\tdeviceId: ' + str(feedback['deviceId']))
            print
    else:
        print('unknown message:', msg.properties.content_type)
```

Yukarıdaki kodda gösterildiği gibi, buluttan cihaza geri bildirim iletisinde bir tür *Application/vnd. Microsoft. ıothub. feedback. JSON*bulunur. Özgün iletinin teslim durumunu anlamak için iletinin JSON gövdesindeki özellikleri kullanabilirsiniz:

* Geri bildirim gövdesinde anahtar `statusCode` şu değerlerden birine sahip: *Success*, *süresi doldu*, *deliverycountexce,* *reddedildi*veya *temizlendi*.

* Geri bildirim gövdesindeki anahtar `deviceId` hedef cihazın KIMLIĞINE sahiptir.

* Geri bildirim gövdesinde anahtar `originalMessageId`, hizmet tarafından gönderilen orijinal buluttan cihaza ileti KIMLIĞINI içerir. Bu teslim durumunu, buluttan cihaza iletilerle geri bildirimde bulunmak için kullanabilirsiniz.

### <a name="receive-telemetry-messages-service-client"></a>Telemetri iletileri alma (hizmet istemcisi)

Varsayılan olarak, IoT Hub 'ınız oluşturulan cihaz telemetri iletilerini yerleşik bir olay hub 'ına depolar. Hizmet istemciniz, depolanan olayları almak için AMQP protokolünü kullanabilir.

Bu amaçla, hizmet istemcisinin öncelikle IoT Hub uç noktasına bağlanması ve yerleşik Olay Hub 'larına bir yeniden yönlendirme adresi alması gerekir. Hizmet istemcisi daha sonra yerleşik Olay Hub 'ına bağlanmak için belirtilen adresi kullanır.

Her adımda, istemcinin aşağıdaki bilgi parçalarını sunması gerekir:

* Geçerli hizmet kimlik bilgileri (hizmet paylaşılan erişim imza belirteci).

* Tüketici grubu bölümünün iletileri almayı amaçladığı, iyi biçimlendirilmiş bir yoldur. Belirli bir tüketici grubu ve bölüm KIMLIĞI için yol şu biçimdedir: `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` (varsayılan Tüketici grubu `$Default` ' dir).

* Bölümde bir başlangıç noktası belirlemek için isteğe bağlı bir filtreleme koşulu. Bu koşul sıra numarası, konum veya sıraya alınmış zaman damgası biçiminde olabilir.

Aşağıdaki kod parçacığı, önceki adımları göstermek için [Python 'Da Uıamqp kitaplığını](https://github.com/Azure/azure-uamqp-python) kullanır:

```python
import json
import uamqp
import urllib
import time

# Use the generate_sas_token implementation that's available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(
    consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified by using endpoint_filter
# Valid predicates include:
# - amqp.annotation.x-opt-sequence-number
# - amqp.annotation.x-opt-offset
# - amqp.annotation.x-opt-enqueued-time
# Set endpoint_filter variable to None if no filter is needed
endpoint_filter = b'amqp.annotation.x-opt-sequence-number > 2995'

# Helper function to set the filtering predicate on the source URI


def set_endpoint_filter(uri, endpoint_filter=''):
    source_uri = uamqp.address.Source(uri)
    source_uri.set_filter(endpoint_filter)
    return source_uri


receive_client = uamqp.ReceiveClient(
    set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
    batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
    # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
    receive_client.close()

    sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(
        redirect.address, policy_name, access_key)
    receive_client = uamqp.ReceiveClient(set_endpoint_filter(
        redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))
    print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
    print('\t: ' + str(msg.annotations['x-opt-offset']))
    print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

Belirli bir cihaz KIMLIĞI için IoT Hub, iletilerinin hangi bölüme depolanacağını belirleyen cihaz KIMLIĞI karmasını kullanır. Yukarıdaki kod parçacığı, olayların tek bir bölümden nasıl alındığını gösterir. Ancak tipik bir uygulamanın, genellikle tüm olay hub bölümlerinde depolanan olayları alması gerektiğini unutmayın.

## <a name="device-client"></a>Cihaz istemcisi

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>Bir IoT Hub 'ına bağlanma ve kimlik doğrulama (cihaz istemcisi)

Bir cihaz, AMQP kullanarak bir IoT Hub 'ına bağlanmak için [talep tabanlı güvenlik (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) veya [Basit kimlik doğrulama ve güvenlik katmanı (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer) kimlik doğrulaması kullanabilir.

Cihaz istemcisi için aşağıdaki bilgiler gereklidir:

| Bilgi | Değer |
|-------------|--------------|
| IoT Hub ana bilgisayar adı | `<iot-hub-name>.azure-devices.net` |
| Erişim anahtarı | Cihazla ilişkili birincil veya ikincil anahtar |
| Paylaşılan erişim imzası | Şu biçimdeki kısa süreli bir paylaşılan erişim imzası: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`. Bu imzayı oluşturma kodunu almak için, bkz. [IoT Hub erişimi denetleme](./iot-hub-devguide-security.md#security-token-structure).

Aşağıdaki kod parçacığı, bir gönderen bağlantısı aracılığıyla bir IoT Hub 'ına bağlanmak için [Python 'Da Uıamqp kitaplığını](https://github.com/Azure/azure-uamqp-python) kullanır.

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(
    device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(
    hostname=hostname, device_id=device_id), access_key, None)

# e.g., '/devices/{device_id}/messages/devicebound'
operation = '<operation-link-name>'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

Aşağıdaki bağlantı yolları cihaz işlemleri olarak desteklenir:

| Oluşturan | Bağlantı türü | Bağlantı yolu | Açıklama |
|------------|-----------|-----------|-------------|
| Cihazlar | Alıcı bağlantısı | `/devices/<deviceID>/messages/devicebound` | Cihazlara hedeflenen buluttan cihaza iletiler, her hedef cihaz tarafından bu bağlantı üzerinden alınır. |
| Cihazlar | Gönderen bağlantısı | `/devices/<deviceID>/messages/events` | Bir cihazdan gönderilen cihazdan buluta iletiler, bu bağlantı üzerinden gönderilir. |
| Cihazlar | Gönderen bağlantısı | `/messages/serviceBound/feedback` | Cihazlar tarafından bu bağlantı üzerinden hizmete gönderilen buluttan cihaza ileti geri bildirimi. |

### <a name="receive-cloud-to-device-commands-device-client"></a>Buluttan cihaza komutları alma (cihaz istemcisi)

Cihazlara gönderilen buluttan cihaza olan komutlar `/devices/<deviceID>/messages/devicebound` bağlantısına ulaşır. Cihazlar bu iletileri toplu işler halinde alabilir ve iletideki ileti veri yükünü, ileti özelliklerini, ek açıklamaları veya uygulama özelliklerini kullanabilir.

Aşağıdaki kod parçacığı, bir cihaz tarafından buluttan cihaza ileti almak için [Python 'Da Uıamqp kitaplığını](https://github.com/Azure/azure-uamqp-python)kullanır.

```python
# ...
# Create a receive client for the cloud-to-device receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(
    device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
while True:
    batch = receive_client.receive_message_batch(max_batch_size=5)
    for msg in batch:
        print('*** received a message ***')
        print(''.join(msg.get_data()))

        # Property 'to' is set to: '/devices/device1/messages/devicebound',
        print('\tto:                     ' + str(msg.properties.to))

        # Property 'message_id' is set to value provided by the service
        print('\tmessage_id:             ' + str(msg.properties.message_id))

        # Other properties are present if they were provided by the service
        print('\tcreation_time:          ' + str(msg.properties.creation_time))
        print('\tcorrelation_id:         ' +
              str(msg.properties.correlation_id))
        print('\tcontent_type:           ' + str(msg.properties.content_type))
        print('\treply_to_group_id:      ' +
              str(msg.properties.reply_to_group_id))
        print('\tsubject:                ' + str(msg.properties.subject))
        print('\tuser_id:                ' + str(msg.properties.user_id))
        print('\tgroup_sequence:         ' +
              str(msg.properties.group_sequence))
        print('\tcontent_encoding:       ' +
              str(msg.properties.content_encoding))
        print('\treply_to:               ' + str(msg.properties.reply_to))
        print('\tabsolute_expiry_time:   ' +
              str(msg.properties.absolute_expiry_time))
        print('\tgroup_id:               ' + str(msg.properties.group_id))

        # Message sequence number in the built-in event hub
        print('\tx-opt-sequence-number:  ' +
              str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>Telemetri iletileri gönderme (cihaz istemcisi)

Ayrıca, AMQP kullanarak bir cihazdan telemetri iletileri gönderebilirsiniz. Cihaz isteğe bağlı olarak, uygulama özellikleri veya ileti KIMLIĞI gibi çeşitli ileti özellikleri için bir sözlük sağlayabilir.

Aşağıdaki kod parçacığı, cihazdan buluta iletileri bir cihazdan göndermek için [Python 'Da Uıamqp kitaplığını](https://github.com/Azure/azure-uamqp-python) kullanır.

```python
# ...
# Create a send client for the device-to-cloud send link on the device
operation = '/devices/{device_id}/messages/events'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

send_client = uamqp.SendClient(uri, debug=True)

# Set any of the applicable message properties
msg_props = uamqp.message.MessageProperties()
msg_props.message_id = str(uuid.uuid4())
msg_props.creation_time = None
msg_props.correlation_id = None
msg_props.content_type = None
msg_props.reply_to_group_id = None
msg_props.subject = None
msg_props.user_id = None
msg_props.group_sequence = None
msg_props.to = None
msg_props.content_encoding = None
msg_props.reply_to = None
msg_props.absolute_expiry_time = None
msg_props.group_id = None

# Application properties in the message (if any)
application_properties = { "app_property_key": "app_property_value" }

# Create message
msg_data = b"Your message payload goes here"
message = uamqp.Message(msg_data, properties=msg_props, application_properties=application_properties)

send_client.queue_message(message)
results = send_client.send_all_messages()

for result in results:
    if result == uamqp.constants.MessageState.SendFailed:
        print result
```

## <a name="additional-notes"></a>Ek notlar

* Bir ağ hatası veya kimlik doğrulama belirtecinin süresi dolduğunda (kodda oluşturulan) AMQP bağlantıları kesilebilir. Hizmet istemcisi bu koşulları işlemeli ve gerekirse bağlantıyı ve bağlantıları yeniden kuracak. Bir kimlik doğrulama belirtecinin süresi dolarsa, istemci, belirtecinin süresi dolduktan önce önceden yenileyerek bir bağlantı bırakmaya engel olabilir.

* İstemciniz bazen bağlantı yeniden yönlendirmelerini doğru şekilde işleyebilmelidir. Bu tür bir işlemi anlamak için AMQP istemci belgelerinize bakın.

## <a name="next-steps"></a>Sonraki adımlar

AMQP protokolü hakkında daha fazla bilgi edinmek için [AMQP v 1.0 belirtimine](https://www.amqp.org/sites/amqp.org/files/amqp.pdf)bakın.

IoT Hub mesajlaşma hakkında daha fazla bilgi edinmek için bkz.:

* [Buluttan cihaza iletiler](./iot-hub-devguide-messages-c2d.md)
* [Ek protokoller için destek](iot-hub-protocol-gateway.md)
* [Message Queuing telemetri taşıma (MQTT) protokolü için destek](./iot-hub-mqtt-support.md)
