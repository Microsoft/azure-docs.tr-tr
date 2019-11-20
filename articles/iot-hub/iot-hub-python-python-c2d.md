---
title: Azure IoT Hub ile buluttan cihaza iletiler (Python) | Microsoft Docs
description: Python için Azure IoT SDK 'larını kullanarak Azure IoT Hub 'ından bir cihaza buluttan cihaza ileti gönderme. Bir sanal cihaz uygulamasını buluttan cihaza iletiler alacak şekilde değiştirirsiniz ve bir arka uç uygulamasını, buluttan cihaza iletileri gönderecek şekilde değiştirirsiniz.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 4cda59448856630468076ef63c51b8a216a31bd0
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001976"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>IoT Hub (Python) ile buluttan cihaza iletileri gönderme

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub, milyonlarca cihaz ile bir çözüm arka ucu arasında güvenilir ve güvenli çift yönlü iletişimin sağlanmasına yardımcı olan, tam olarak yönetilen bir hizmettir. [Bir cihazdan IoT Hub 'ına yönelik Telemetriyi, bir](quickstart-send-telemetry-python.md) IoT Hub 'ı oluşturmayı, bu kodda bir cihaz kimliği sağlamayı ve cihazdan buluta iletiler gönderen bir sanal cihaz uygulamasını nasıl kodlayacağınızı gösterir.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Bu öğretici, [bir cihazdan IoT Hub 'ına telemetri gönderme hakkında bir](quickstart-send-telemetry-python.md)yapı oluşturur. Şunu gösterir:

* Çözüm arka ucundan, IoT Hub aracılığıyla buluttan cihaza iletileri tek bir cihaza gönderin.

* Bir cihazda buluttan cihaza iletiler alın.

* Çözüm arka uçta, IoT Hub bir cihaza gönderilen iletiler için teslim bildirimi (*geri bildirim*) isteyin.

[IoT Hub geliştirici kılavuzunda](iot-hub-devguide-messaging.md)buluttan cihaza iletiler hakkında daha fazla bilgi edinebilirsiniz.

Bu öğreticinin sonunda, iki Python konsol uygulaması çalıştırırsınız:

* **SimulatedDevice.py**, bir cihazdan Telemetriyi, IoT Hub 'ınıza bağlanan ve buluttan cihaza iletileri alan bir [IoT Hub 'ına bir cihaz aracılığıyla göndererek](quickstart-send-telemetry-python.md)oluşturulan bir uygulama sürümüdür.

* **SendCloudToDeviceMessage.py**, sanal cihaz uygulamasına IoT Hub aracılığıyla buluttan cihaza ileti gönderir ve ardından teslim onayını alır.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="receive-messages-in-the-simulated-device-app"></a>Sanal cihaz uygulamasında ileti alma

Bu bölümde, IoT Hub 'ından cihazın benzetimini yapmak ve buluttan cihaza iletileri almak için bir Python konsol uygulaması oluşturacaksınız.

1. Bir metin düzenleyicisi kullanarak bir **SimulatedDevice.py** dosyası oluşturun.

2. **SimulatedDevice.py** dosyasının başlangıcında `import` aşağıdaki deyimleri ve değişkenleri ekleyin:

   ```python
    import threading
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

3. Aşağıdaki kodu **SimulatedDevice.py** dosyasına ekleyin. "{DeviceConnectionString}" yer tutucu değerini, [bir cihazdan IoT Hub 'ına bir cihazdan Telemetriyi gönder](quickstart-send-telemetry-python.md) hızlı başlangıç bölümünde oluşturduğunuz cihazın cihaz bağlantı dizesiyle değiştirin:

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Alınan iletileri konsola yazdırmak için aşağıdaki işlevi ekleyin:

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("Message received")
            print( "    Data: <<{}>>".format(message.data) )
            print( "    Properties: {}".format(message.custom_properties))
            print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
    ```

5. İstemcisini başlatmak için aşağıdaki kodu ekleyin ve buluttan cihaza iletisini almaya bekleyin:

    ```python
    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            message_listener_thread = threading.Thread(target=message_listener, args=(client,))
            message_listener_thread.daemon = True
            message_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )
    ```

6. Aşağıdaki ana işlevi ekleyin:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. **SimulatedDevice.py** dosyasını kaydedin ve kapatın.

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub bağlantı dizesini al

Bu makalede, [bir cihazdan IoT Hub 'ına telemetri gönderme](quickstart-send-telemetry-python.md)bölümünde oluşturduğunuz IoT Hub 'ı aracılığıyla buluttan cihaza iletileri göndermek için bir arka uç hizmeti oluşturursunuz. Buluttan cihaza iletiler göndermek için hizmetinize **hizmet bağlantısı** izni verilmesi gerekir. Varsayılan olarak, her IoT Hub, bu izni veren **hizmet** adlı paylaşılan bir erişim ilkesiyle oluşturulur.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Buluttan cihaza ileti gönderme

Bu bölümde, sanal cihaz uygulamasına buluttan cihaza iletiler gönderen bir Python konsol uygulaması oluşturacaksınız. [Bir cihazdan IoT Hub 'ına bir cihazdan Telemetriyi gönder](quickstart-send-telemetry-python.md) hızlı başlangıç bölümünde eklediğiniz CIHAZıN cihaz kimliği gereklidir. [IoT Hub bağlantı dizesini al](#get-the-iot-hub-connection-string)bölümünde daha önce kopyaladığınız IoT Hub bağlantı dizesine da ihtiyacınız vardır.

1. Bir metin düzenleyicisi kullanarak bir **SendCloudToDeviceMessage.py** dosyası oluşturun.

2. **SendCloudToDeviceMessage.py** dosyasının başlangıcında `import` aşağıdaki deyimleri ve değişkenleri ekleyin:

    ```python
    import random
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubMessaging, IoTHubMessage, IoTHubError

    OPEN_CONTEXT = 0
    FEEDBACK_CONTEXT = 1
    MESSAGE_COUNT = 1
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

3. Aşağıdaki kodu **SendCloudToDeviceMessage.py** dosyasına ekleyin. "{IoT Hub bağlantı dizesi}" ve "{Device ID}" yer tutucu değerlerini daha önce not ettiğiniz IoT Hub bağlantı dizesi ve cihaz KIMLIĞI ile değiştirin:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

4. Konsola geri bildirim iletilerini yazdırmak için aşağıdaki işlevi ekleyin:

    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

5. Cihazınıza bir ileti göndermek ve cihaz buluttan cihaza iletisini geldiğinde geri bildirim iletisini işlemek için aşağıdaki kodu ekleyin:

    ```python
    def iothub_messaging_sample_run():
        try:
            iothub_messaging = IoTHubMessaging(CONNECTION_STRING)

            iothub_messaging.open(open_complete_callback, OPEN_CONTEXT)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))

                # optional: assign ids
                message.message_id = "message_%d" % i
                message.correlation_id = "correlation_%d" % i
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % i
                prop_map.add("Property", prop_text)

                iothub_messaging.send_async(DEVICE_ID, message, send_complete_callback, i)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

            iothub_messaging.close()

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubMessaging sample stopped" )
    ```

6. Aşağıdaki ana işlevi ekleyin:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

7. **SendCloudToDeviceMessage.py** dosyasını kaydedin ve kapatın.

## <a name="run-the-applications"></a>Uygulamaları çalıştırma

Şimdi uygulamaları çalıştırmaya hazırsınız.

1. Bir komut istemi açın ve **Python Için Azure IoT Hub cihaz SDK 'sını**yükledikten sonra.

    ```shell
    pip install azure-iothub-device-client
    ```

2. Komut isteminde, buluttan cihaza iletileri dinlemek için aşağıdaki komutu çalıştırın:

    ```shell
    python SimulatedDevice.py
    ```

    ![Sanal cihaz uygulamasını çalıştırma](./media/iot-hub-python-python-c2d/simulated-device.png)

3. Yeni bir komut istemi açın ve **Python Için Azure IoT Hub Service SDK 'sını**yükledikten sonra.

    ```shell
    pip install azure-iothub-service-client
    ```

4. Bir komut isteminde, buluttan cihaza ileti göndermek ve ileti geri bildirimi için beklemek üzere aşağıdaki komutu çalıştırın:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Buluttan cihaza komutunu göndermek için uygulamayı çalıştırın](./media/iot-hub-python-python-c2d/send-command.png)

5. Cihaz tarafından alınan iletiyi aklınızda edin.

    ![İleti alındı](./media/iot-hub-python-python-c2d/message-received.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, buluttan cihaza iletileri gönderme ve alma hakkında daha fazla öğrendiniz.

IoT Hub kullanan uçtan uca çözümlerin tam örneklerini görmek için bkz. [Azure IoT uzaktan izleme Çözüm Hızlandırıcısı](https://azure.microsoft.com/documentation/suites/iot-suite/).

IoT Hub çözümleri geliştirme hakkında daha fazla bilgi edinmek için [IoT Hub Geliştirici Kılavuzu](iot-hub-devguide.md)' na bakın.
