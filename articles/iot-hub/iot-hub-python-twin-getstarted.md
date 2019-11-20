---
title: Azure IoT Hub cihaz TWINS (Python) ile çalışmaya başlama | Microsoft Docs
description: Azure IoT Hub cihaz ikimlerini kullanarak etiketler ekleyin ve ardından bir IoT Hub sorgusu kullanın. Python için Azure IoT SDK 'larını kullanarak, Etiketler ekleyen ve IoT Hub sorguyu çalıştıran sanal cihaz uygulamasını ve bir hizmet uygulamasını uygulayabilirsiniz.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.openlocfilehash: 729371ede8df3dc21f70bc5742de52f2be874507
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030011"
---
# <a name="get-started-with-device-twins-python"></a>Cihaz ikizlerini kullanmaya başlama (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Bu öğreticinin sonunda iki Python konsol uygulamanız olacaktır:

* Etiketler ve sorgu cihaz TWINS 'i ekleyen bir Python arka uç uygulaması olan **AddTagsAndQuery.py**.

* Daha önce oluşturulan cihaz kimliğiyle IoT Hub 'ınıza bağlanan bir cihazın benzetimini yapan ve bağlantı koşulunu raporlayan bir Python uygulaması **ReportConnectivity.py**.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Bir IoT Hub oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub 'a yeni bir cihaz kaydetme

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub bağlantı dizesini al

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Hizmet uygulaması oluşturma

Bu bölümde, **{DEVICE ID}** ile ilişkili cihaz ikizi konum meta verilerini ekleyen bir Python konsol uygulaması oluşturacaksınız. Ardından, IoT Hub 'ında depolanan cihaz TWINS 'i, Redmond 'da bulunan cihazları ve ardından hücresel bağlantı bildirdikleri sorgular.

1. Çalışma dizininizde, bir komut istemi açın ve **Python Için Azure IoT Hub hizmet SDK 'sını**yükledikten sonra.

   ```cmd/sh
   pip install azure-iothub-service-client
   ```

   > [!NOTE]
   > Azure-ıothub-Service-Client için PIP paketi şu anda yalnızca Windows işletim sistemi için kullanılabilir. Linux/Mac OS için lütfen [geliştirme ortamınızı Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) gönderisini hazırlama bölümündeki linux ve Mac OS özgü bölümlere bakın.
   >

2. Bir metin düzenleyicisi kullanarak yeni bir **AddTagsAndQuery.py** dosyası oluşturun.

3. Hizmet SDK’sından gerekli modülleri içeri aktarmak için aşağıdaki kodu ekleyin:

   ```python
   import sys
   import iothub_service_client
   from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
   from iothub_service_client import IoTHubDeviceTwin, IoTHubError
   ```

4. Aşağıdaki kodu ekleyin. @No__t-0 ' [ı, IoT Hub bağlantı dizesini al](#get-the-iot-hub-connection-string)bölümünde kopyaladığınız IoT Hub bağlantı dizesiyle değiştirin. @No__t-0 ' yı [, IoT Hub 'ına yeni bir cihaz kaydetme](#register-a-new-device-in-the-iot-hub)bölümünde KAYDETTIĞINIZ cihaz kimliğiyle değiştirin.
  
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

5. Aşağıdaki kodu **AddTagsAndQuery.py** dosyasına ekleyin:

    ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            iothub_registry_statistics = iothub_registry_manager.get_statistics()
            print ( "Total device count                       : {0}".format(iothub_registry_statistics.totalDeviceCount) )
            print ( "Enabled device count                     : {0}".format(iothub_registry_statistics.enabledDeviceCount) )
            print ( "Disabled device count                    : {0}".format(iothub_registry_statistics.disabledDeviceCount) )
            print ( "" )

            number_of_devices = iothub_registry_statistics.totalDeviceCount
            dev_list = iothub_registry_manager.get_device_list(number_of_devices)

            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            for device in range(0, number_of_devices):
                if dev_list[device].deviceId == DEVICE_ID:
                    twin_info = iothub_twin_method.update_twin(dev_list[device].deviceId, UPDATE_JSON)

            print ( "Devices in Redmond: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    print ( dev_list[device].deviceId )

            print ( "" )

            print ( "Devices in Redmond using cellular network: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    if twin_info.find(UPDATE_JSON_CLIENT_SEARCH) > -1:
                        print ( dev_list[device].deviceId )

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "IoTHub sample stopped" )
    ```

    **Kayıt defteri** nesnesi, hizmetten cihaz ikikiyle etkileşimde bulunmak için gereken tüm yöntemleri kullanıma sunar. Kod, ilk olarak **kayıt defteri** nesnesini başlatır ve ardından, **DeviceID**için cihaz ikizi güncelleştirir ve son olarak iki sorgu çalıştırır. İlki, **Redmond43** tesisinde bulunan cihazların yalnızca cihaz ikelerini seçer ve ikincisi ise yalnızca hücresel ağ üzerinden de bağlı olan cihazları seçecek şekilde sorguyu iyileştirir.

6. **İothub_service_sample_run** işlevini uygulamak için **AddTagsAndQuery.py** sonuna aşağıdaki kodu ekleyin:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

7. Uygulamayı ile çalıştırın:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    **Redmond43** ' de bulunan tüm cihazları isteyen sorgunun sonuçlarında bir cihaz görmeniz gerekir ve sonuçları hücresel ağ kullanan cihazlara kısıtlayan sorgu için yok.

    ![Redmond 'daki tüm cihazları gösteren ilk sorgu](./media/iot-hub-python-twin-getstarted/service-1.png)

Sonraki bölümde, bağlantı bilgilerini raporlayan ve önceki bölümde sorgunun sonucunu değiştiren bir cihaz uygulaması oluşturacaksınız.

## <a name="create-the-device-app"></a>Cihaz uygulaması oluşturma

Bu bölümde, hub 'ınıza **{CIHAZ kimliğiniz}** olarak bağlanan bir Python konsol uygulaması oluşturursunuz ve ardından cihaz ikizi 'nin bildirilen özelliklerini bir hücresel ağ kullanılarak bağlı olduğu bilgileri içerecek şekilde güncelleştirir.

1. Çalışma dizininizde bir komut isteminden **Python Için Azure IoT Hub cihaz SDK 'sını**yükledikten sonra:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Bir metin düzenleyicisi kullanarak yeni bir **ReportConnectivity.py** dosyası oluşturun.

3. Cihaz SDK 'sından gerekli modülleri içeri aktarmak için aşağıdaki kodu ekleyin:

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient
    ```

4. Aşağıdaki kodu ekleyin. @No__t-0 yer tutucu değerini, [IoT Hub 'ına yeni bir cihaz kaydet](#register-a-new-device-in-the-iot-hub)' de kopyaladığınız cihaz bağlantı dizesiyle değiştirin.

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    ```

5. Cihaz TWINS işlevlerini uygulamak için aşağıdaki kodu **ReportConnectivity.py** dosyasına ekleyin:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("Twin patch received:")
            print(patch)

    def iothub_client_init():
        client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            # Send reported 
            print ( "Sending data as reported property..." )
            reported_patch = {"connectivity": "cellular"}
            client.patch_twin_reported_properties(reported_patch)
            print ( "Reported properties updated" )

            while True:
                time.sleep(1000000)
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```

    **İstemci** nesnesi, cihazdan cihaz ikikiyle etkileşimde bulunmak için gereken tüm yöntemleri kullanıma sunar. Önceki kod, **istemci** nesnesini başlattıktan sonra cihazınızla ilgili cihaz ikizi alır ve bildirilen özelliğini bağlantı bilgileriyle güncelleştirir.

6. **İothub_client_sample_run** işlevini uygulamak için **ReportConnectivity.py** sonuna aşağıdaki kodu ekleyin:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Cihaz uygulamasını çalıştırın:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Cihaz ikikinin güncelleştirildiğinden onay görmeniz gerekir.

    ![güncelleştirme TWINS](./media/iot-hub-python-twin-getstarted/device-1.png)

8. Cihaz bağlantı bilgilerini raporladığı için, her iki sorgu da görünmelidir. Geri dönüp sorguları yeniden çalıştırın:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Bu kez, **{DEVICE ID}** hesabınız her iki sorgu sonucunda da görünmelidir.

    ![İkinci sorgu](./media/iot-hub-python-twin-getstarted/service-2.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure portalında yeni bir IoT hub'ı yapılandırdınız ve ardından IoT hub'ının kimlik kayıt defterinde bir cihaz kimliği oluşturdunuz. Cihaz meta verilerini bir arka uç uygulamasından Etiketler olarak eklediniz ve cihaz ikizi cihaz bağlantı bilgilerini raporlamak için bir sanal cihaz uygulaması yazmış olursunuz. Ayrıca, bu bilgileri kayıt defterini kullanarak sorgulama hakkında bilgi edinirsiniz.

Aşağıdakilerin nasıl yapılacağını öğrenmek için aşağıdaki kaynakları kullanın:

* [IoT Hub ile çalışmaya başlama](quickstart-send-telemetry-python.md) öğreticisini kullanarak cihazlardan telemetri gönderin.

* [Cihazları yapılandırmak için istenen özellikleri kullan](tutorial-device-twins.md) öğreticisiyle cihaz ikizi 'nin istenen özelliklerini kullanarak cihazları yapılandırın.

* Cihazları etkileşimli olarak (kullanıcı denetimli bir uygulamadan bir fanı açmak gibi) [doğrudan Yöntemler](quickstart-control-device-python.md) öğreticisini kullanarak kontrol edin.
