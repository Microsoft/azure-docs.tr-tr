---
title: IoT cihaz güvenlik iletileri için Defender gönderme
description: IoT için Defender 'ı kullanarak güvenlik iletilerinizi gönderme hakkında bilgi edinin.
ms.topic: how-to
ms.date: 2/8/2021
ms.custom: devx-track-js
ms.openlocfilehash: dcda7f4c1d078fa32a5ab8c7b7ce690ab2134afe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781645"
---
# <a name="send-security-messages-sdk"></a>Güvenlik iletileri SDK 'Sı gönder

Bu nasıl yapılır kılavuzunda, IoT Aracısı için bir Defender kullanmadan cihaz güvenlik iletilerinizi toplayıp göndermenizi seçtiğinizde IoT hizmeti özelliklerine yönelik Defender açıklanır ve bunun nasıl yapılacağı açıklanır.

Bu kılavuzda şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Azure IoT C SDK 'sını kullanarak güvenlik iletileri gönderme
> * Azure IoT C# SDK 'SıNı kullanarak güvenlik iletileri gönderme
> * Azure IoT Python SDK 'sını kullanarak güvenlik iletileri gönderme
> * Azure IoT Node.js SDK kullanarak güvenlik iletileri gönderme
> * Azure IoT Java SDK 'sını kullanarak güvenlik iletileri gönderme

## <a name="defender-for-iot-capabilities"></a>IoT için Defender özellikleri

IoT için Defender, gönderilen veriler [IoT şeması Için Defender](https://aka.ms/iot-security-schemas) 'a uygun olduğu ve ileti güvenlik iletisi olarak ayarlandığı sürece her türlü güvenlik iletisi verilerini işleyebilir ve analiz edebilir.

## <a name="security-message"></a>Güvenlik iletisi

IoT için Defender, aşağıdaki ölçütleri kullanarak bir güvenlik iletisi tanımlar:

- İleti Azure IoT SDK ile gönderildiyse
- İleti [güvenlik iletisi şemasına](https://aka.ms/iot-security-schemas) uygunsa
- İleti gönderilmeden önce bir güvenlik iletisi olarak ayarlandıysa

Her güvenlik iletisi, gönderenin meta verilerini, `AgentId` `AgentVersion` `MessageSchemaVersion` ve güvenlik olaylarının bir listesini içerir.
Şema, olay türleri dahil olmak üzere güvenlik iletisinin geçerli ve gerekli özelliklerini tanımlar.

> [!NOTE]
> Şemayla uyumlu olmayan gönderilen iletiler yok sayılır. Yoksayılan iletiler Şu anda depolanmadığından, verileri göndermeyi başlatmadan önce şemayı doğrulamaya dikkat edin.

> [!NOTE]
> Azure IoT SDK kullanılarak bir güvenlik iletisi olarak ayarlanmamış iletiler, IoT işlem hattı için Defender 'a yönlendirilmeyecektir.

## <a name="valid-message-example"></a>Geçerli ileti örneği

Aşağıdaki örnekte geçerli bir güvenlik iletisi nesnesi gösterilmektedir. Örnek, ileti meta verilerini ve bir `ProcessCreate` güvenlik olayını içerir.

Bir güvenlik iletisi olarak ayarladıktan ve gönderildikten sonra bu ileti IoT için Defender tarafından işlenir.

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/myApp",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "aUser",
                    "CommandLine": "myApp -a -b"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>Güvenlik iletilerini gönder

[Azure IoT C cihaz SDK 'sı](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview), [Azure IoT C# cihaz SDK 'Sı](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview), [Azure IoT Node.js SDK](https://github.com/Azure/azure-iot-sdk-node), [Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python)veya [Azure IoT Java SDK 'sını](https://github.com/Azure/azure-iot-sdk-java)kullanarak IoT Aracısı için Defender *'ı kullanmadan güvenlik iletileri gönderin* .

IoT için Defender tarafından işlenmek üzere cihazlarınızdan cihaz verilerini göndermek için aşağıdaki API 'lerden birini kullanarak, iletileri IoT işleme işlem hattı için Defender 'a doğru yönlendirme için işaretleyin.

Doğru üstbilgiyle işaretlenmiş olsa bile gönderilen tüm veriler, [IoT Için Defender ileti şeması](https://aka.ms/iot-security-schemas)ile de uyumlu olmalıdır.

### <a name="send-security-message-api"></a>Güvenlik iletisi API 'SI gönder

**Güvenlik Iletileri gönderme** API 'si Şu anda C ve C#, Python, Node.js ve Java 'da kullanılabilir.

#### <a name="c-api"></a>C APı 'SI

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {

    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;

    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);

    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }

    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }

    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }

cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }

    return success;
}

static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }

    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```

#### <a name="c-api"></a>C# API’si

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```

#### <a name="nodejs-api"></a>Node.js API’si

```typescript
var Protocol = require('azure-iot-device-mqtt').Mqtt

function SendSecurityMessage(messageContent)
{
  var client = Client.fromConnectionString(connectionString, Protocol);

  var connectCallback = function (err) {
    if (err) {
      console.error('Could not connect: ' + err.message);
    } else {
      var message = new Message(messageContent);
      message.setAsSecurityMessage();
      client.sendEvent(message);
  
      client.on('error', function (err) {
        console.error(err.message);
      });
  
      client.on('disconnect', function () {
        clearInterval(sendInterval);
        client.removeAllListeners();
        client.open(connectCallback);
      });
    }
  };

  client.open(connectCallback);
}
```

#### <a name="python-api"></a>Python API

Python API 'sini kullanmak için, [Azure-IoT-Device](https://pypi.org/project/azure-iot-device/)paketini yüklemeniz gerekir.

Python API 'sini kullanırken, güvenlik iletisini modül aracılığıyla veya benzersiz cihaz ya da modül bağlantı dizesini kullanarak cihaz aracılığıyla gönderebilirsiniz. Aşağıdaki Python betik örneğini kullanırken, bir cihazla, **Iothubdeviceclient**' i kullanın ve bir modülle **Iothubmoduleclient**' ı kullanın.

```python
from azure.iot.device.aio import IoTHubDeviceClient, IoTHubModuleClient
from azure.iot.device import Message

async def send_security_message_async(message_content):
    conn_str = os.getenv("<connection_string>")
    device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)
    await device_client.connect()
    security_message = Message(message_content)
    security_message.set_as_security_message()
    await device_client.send_message(security_message)
    await device_client.disconnect()
```

#### <a name="java-api"></a>Java API’si

```java
public void SendSecurityMessage(string message)
{
    ModuleClient client = new ModuleClient("<connection_string>", IotHubClientProtocol.MQTT);
    Message msg = new Message(message);
    msg.setAsSecurityMessage();
    EventCallback callback = new EventCallback();
    string context = "<user_context>";
    client.sendEventAsync(msg, callback, context);
}
```

## <a name="next-steps"></a>Sonraki adımlar

- IoT hizmetine [genel bakış](overview.md) Için Defender 'ı okuyun
- IoT [mimarisi](architecture.md) için Defender hakkında daha fazla bilgi edinin
- [Hizmeti](quickstart-onboard-iot-hub.md) etkinleştirme
- [SSS](resources-frequently-asked-questions.md) 'yi okuyun
- [Ham güvenlik verilerine](how-to-security-data-access.md) erişme hakkında bilgi edinin
- [Önerileri](concept-recommendations.md) anlama
- [Uyarıları](concept-security-alerts.md) anlama
