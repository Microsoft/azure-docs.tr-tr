---
title: Cihaz telemetrisini Azure IoT Central hızlı başlangıç 'a gönderme (Python)
description: Bu hızlı başlangıçta, bir cihazdan IoT Central için telemetri göndermek üzere Python için Azure IoT Hub cihaz SDK 'sını kullanacaksınız.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: python
ms.topic: quickstart
ms.date: 01/11/2021
ms.openlocfilehash: 5d872dd7c94a0b3ab23623bb246ff7ae81609779
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047176"
---
# <a name="quickstart-send-telemetry-from-a-device-to-azure-iot-central-python"></a>Hızlı başlangıç: cihazdan Azure IoT Central telemetri gönderme (Python)

Uygulama **hedefi**: [cihaz uygulaması geliştirme](about-iot-develop.md#device-application-development)

Bu hızlı başlangıçta, temel bir IoT cihaz uygulama geliştirme iş akışı öğrenirsiniz. İlk olarak, bir bulut uygulaması oluşturmak için Azure IoT Central kullanırsınız. Ardından, sanal bir cihaz oluşturmak, IoT Central bağlanmak ve cihazdan buluta telemetri göndermek için Azure IoT Python SDK 'sını kullanın. 

## <a name="prerequisites"></a>Önkoşullar
- [Python 3.7 +](https://www.python.org/downloads/). Desteklenen Python 'un diğer sürümleri için bkz. [Azure IoT cihaz özellikleri](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device#azure-iot-device-features).
    
    Python sürümünüzün güncel olduğundan emin olmak için çalıştırın `python --version` . Hem Python 2 hem de Python 3 ' ü yüklediyseniz ve Python 3 ortamı kullanıyorsanız, kullanarak tüm kitaplıkları yükleyebilirsiniz `pip3` . Çalışıyor `pip3` , kitaplıkların Python 3 çalışma zamanına yüklenmesini sağlar.
    > [!IMPORTANT]
    > Python Yükleyicisi ' nde, **yola Python ekleme** seçeneğini belirleyin. Python 3,7 veya üzeri bir sürümü zaten yüklüyse, Python yükleme klasörünü ortam değişkenine eklediğini onaylayın `PATH` .

## <a name="create-an-application"></a>Uygulama oluşturma
Bu bölümde, bir IoT Central uygulaması oluşturacaksınız. IoT Central, IoT çözümlerini geliştirme ve yönetmenin karmaşıklığını ve maliyetini azaltmaya yardımcı olan portal tabanlı bir IoT uygulama platformudur.

Azure IoT Central uygulaması oluşturmak için:
1. [Azure IoT Central](https://apps.azureiotcentral.com/) gidin ve bir Microsoft kişisel, iş veya okul hesabıyla oturum açın.
1. **Derleme** ve **özel uygulamalar** seçme bölümüne gidin.
   :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-build.png" alt-text="Başlangıç sayfası IoT Central":::
1. **Uygulama adı**' nda, benzersiz bir ad girin veya oluşturulan adı kullanın.
1. **URL**'de, etkileyici bir uygulama URL 'si öneki girin veya oluşturulan URL önekini kullanın.
1. **Uygulama şablonundan** *özel uygulama* olarak ayarlı bırakın. Hesabınızda zaten varsa, açılan liste diğer seçenekleri gösterebilir.
1. **Fiyatlandırma planı** seçeneğini belirleyin. 
    - Uygulamayı yedi gün boyunca ücretsiz olarak kullanmak için, **ücretsiz**' yi seçin. Ücretsiz bir uygulamayı, süresi dolmadan standart fiyatlandırmaya dönüştürebilirsiniz.
    - İsteğe bağlı olarak, standart bir fiyatlandırma planı seçebilirsiniz. Standart fiyatlandırma ' yı seçerseniz, daha fazla seçenek görüntülenir ve bir **Dizin**, **Azure aboneliği** ve bir **konum** ayarlamanız gerekir. Fiyatlandırma hakkında bilgi edinmek için bkz. [Azure IoT Central fiyatlandırması](https://azure.microsoft.com/pricing/details/iot-central/). 
        - **Dizin** , uygulamanızı oluşturduğunuz Azure Active Directory. Azure Active Directory Kullanıcı kimliklerini, kimlik bilgilerini ve diğer kuruluş bilgilerini içerir. Bir Azure Active Directory yoksa, bir Azure aboneliği oluşturduğunuzda bir tane oluşturulur.
        - **Azure aboneliği** , Azure Hizmetleri örnekleri oluşturmanıza olanak sağlar. IoT Central aboneliğinizdeki kaynakları sağlar. Azure aboneliğiniz yoksa [ücretsiz olarak bir tane oluşturabilirsiniz](https://azure.microsoft.com/free/). Aboneliği oluşturduktan sonra **yeni IoT Central uygulama** sayfasına dönün. Yeni aboneliğiniz **Azure aboneliği** açılır penceresinde görüntülenir.
        - **Konum** , bir uygulama oluşturduğunuz [Azure Coğrafya](https://azure.microsoft.com/global-infrastructure/geographies/) ' dır. En iyi performansı elde etmek için cihazlarınıza fiziksel olarak en yakın bir konum seçin. Bir konum seçtikten sonra, uygulamayı farklı bir konuma taşıyamazsınız.

    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-pricing.png" alt-text="Yeni uygulama iletişim kutusu IoT Central":::
1. **Oluştur**’u seçin.
    
    IoT Central uygulamayı oluşturduktan sonra uygulama panosuna yönlendirir.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-created.png" alt-text="Yeni uygulama panosu IoT Central":::

## <a name="add-a-device"></a>Cihaz ekleme
Bu bölümde, IoT Central uygulamanıza yeni bir cihaz eklersiniz. Cihaz, uygulamaya bağlanacak gerçek veya sanal bir cihazı temsil eden bir cihaz şablonu örneğidir. 

Yeni bir cihaz oluşturmak için:
1. Sol bölmede **cihazlar**' ı seçin ve sonra **+ Yeni**' yi seçin. Bu, yeni cihaz iletişim kutusunu açar.
1. **Cihaz şablonu** *atanmamış* olarak ayarlı bırakın.

    > [!NOTE]
    > Kolaylık sağlaması için bu hızlı başlangıçta atanmamış bir şablon kullanan sanal bir cihaza bağlanırsınız. Cihazları yönetmek için IoT Central kullanmaya devam ederseniz, cihaz şablonlarını kullanmayı öğreneceksiniz. Cihaz şablonlarıyla çalışmaya genel bakış için bkz. [hızlı başlangıç: IoT Central uygulamanıza sanal cihaz ekleme](../iot-central/core/quick-create-simulated-device.md).
1. Kolay bir **Cihaz adı** ve **cihaz kimliği** ayarlayın. İsteğe bağlı olarak, oluşturulan değerleri kullanın.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-create-device.png" alt-text="Yeni cihaz IoT Central iletişim kutusu":::
1. **Oluştur**’u seçin.

    Oluşturulan cihaz **tüm cihazlar** listesinde görünür.
    :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-devices-list.png" alt-text="Tüm cihaz listesini IoT Central":::
    
Yeni cihaz için bağlantı ayrıntılarını almak için:
1. **Tüm cihazlar** listesinde, ayrıntıları göstermek için bağlı cihaz adına çift tıklayın. 
1. Üst menüde **Bağlan**’ı seçin.

    **Cihaz bağlantısı** iletişim kutusu, bağlantı ayrıntılarını görüntüler: :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-device-connect.png" alt-text="IoT Central cihaz bağlantısı ayrıntıları":::
1. Aşağıdaki değerleri **cihaz bağlantısı** iletişim kutusundan güvenli bir konuma kopyalayın. Cihazınızı IoT Central bağlamak için sonraki bölümde kullanacaksınız.
    * `ID scope`
    * `Device ID`
    * `Primary key`

## <a name="send-messages-and-monitor-telemetry"></a>İleti gönderme ve Telemetriyi izleme
Bu bölümde, sanal bir cihaz oluşturmak ve IoT Central uygulamanıza telemetri göndermek için Python SDK 'sını kullanacaksınız. 

1. Windows CMD veya PowerShell veya Bash (Windows veya Linux için) kullanarak bir Terminal açın. Python SDK 'sını yüklemek, ortam değişkenlerini güncelleştirmek ve Python kod örneğini çalıştırmak için terminali kullanacaksınız.

1. [Azure IoT Python SDK cihaz örneklerini](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) yerel makinenize kopyalayın.

    ```console
    git clone https://github.com/Azure/azure-iot-sdk-python
    ```

1. *Azure-IoT-SDK-Python/Azure-IoT-Device/Samples* dizinine gidin.

    ```console
    cd azure-iot-sdk-python/azure-iot-device/samples
    ```
1. Azure IoT Python SDK 'sını yükler.

    ```console
    pip install azure-iot-device
    ```

1. Sanal cihazınızın IoT Central bağlanmasına izin vermek için aşağıdaki ortam değişkenlerinin her birini ayarlayın. `ID_SCOPE`, `DEVICE_ID` Ve için, `DEVICE_KEY` IoT Central *cihaz bağlantısı* iletişim kutusundan kaydettiğiniz değerleri kullanın.

    **Windows CMD**

    ```console
    set PROVISIONING_HOST=global.azure-devices-provisioning.net
    ```
    ```console
    set ID_SCOPE=<your ID scope>
    ```
    ```console
    set DEVICE_ID=<your device ID>
    ```
    ```console
    set DEVICE_KEY=<your device's primary key>
    ```

    > [!NOTE]
    > Windows CMD için bağlantı dizesini veya diğer değişken değerlerini çevreleyen tırnak işareti yoktur.

    **PowerShell**

    ```azurepowershell
    $env:PROVISIONING_HOST='global.azure-devices-provisioning.net'
    ```
    ```azurepowershell
    $env:ID_SCOPE='<your ID scope>'
    ```
    ```azurepowershell
    $env:DEVICE_ID='<your device ID>'
    ```
    ```azurepowershell
    $env:DEVICE_KEY='<your device's primary key>'
    ```

    **Bash (Linux veya Windows)**

    ```bash
    export PROVISIONING_HOST='global.azure-devices-provisioning.net'
    ```
    ```bash
    export ID_SCOPE='<your ID scope>'
    ```
    ```bash
    export DEVICE_ID='<your device ID>'
    ```
    ```bash
    export DEVICE_KEY='<your device's primary key>'
    ```

1. Terminalinizde, örnek dosya * simple_send_temperature. Kopyala için kodu çalıştırın. Bu kod, sanal IoT cihazına erişir ve IoT Central bir ileti gönderir.

    Terminalden Python örneğini çalıştırmak için:
    ```console
    python ./simple_send_temperature.py
    ```

    İsteğe bağlı olarak, Python IDE 'niz içindeki örnekteki Python kodunu çalıştırabilirsiniz:
    ```python
    import asyncio
    import os
    from azure.iot.device.aio import ProvisioningDeviceClient
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.iot.device import Message
    import uuid
    import json
    import random

    # ensure environment variables are set for your device and IoT Central application credentials
    provisioning_host = os.getenv("PROVISIONING_HOST")
    id_scope = os.getenv("ID_SCOPE")
    registration_id = os.getenv("DEVICE_ID")
    symmetric_key = os.getenv("DEVICE_KEY")

    # allows the user to quit the program from the terminal
    def stdin_listener():
        """
        Listener for quitting the sample
        """
        while True:
            selection = input("Press Q to quit\n")
            if selection == "Q" or selection == "q":
                print("Quitting...")
                break

    async def main():

        # provisions the device to IoT Central-- this uses the Device Provisioning Service behind the scenes
        provisioning_device_client = ProvisioningDeviceClient.create_from_symmetric_key(
            provisioning_host=provisioning_host,
            registration_id=registration_id,
            id_scope=id_scope,
            symmetric_key=symmetric_key,
        )

        registration_result = await provisioning_device_client.register()

        print("The complete registration result is")
        print(registration_result.registration_state)

        if registration_result.status == "assigned":
            print("Your device has been provisioned. It will now begin sending telemetry.")
            device_client = IoTHubDeviceClient.create_from_symmetric_key(
                symmetric_key=symmetric_key,
                hostname=registration_result.registration_state.assigned_hub,
                device_id=registration_result.registration_state.device_id,
            )

            # Connect the client.
            await device_client.connect()

        # Send the current temperature as a telemetry message
        async def send_telemetry():
            print("Sending telemetry for temperature")

            while True:
                current_temp = random.randrange(10, 50)  # Current temperature in Celsius (randomly generated)
                # Send a single temperature report message
                temperature_msg = {"temperature": current_temp}

                msg = Message(json.dumps(temperature_msg))
                msg.content_encoding = "utf-8"
                msg.content_type = "application/json"
                print("Sent message")
                await device_client.send_message(msg)
                await asyncio.sleep(8)

        send_telemetry_task = asyncio.create_task(send_telemetry())

        # Run the stdin listener in the event loop
        loop = asyncio.get_running_loop()
        user_finished = loop.run_in_executor(None, stdin_listener)
        # Wait for user to indicate they are done listening for method calls
        await user_finished

        send_telemetry_task.cancel()
        # Finally, shut down the client
        await device_client.disconnect()

    if __name__ == "__main__":
        asyncio.run(main())

        # If using Python 3.6 or below, use the following code instead of asyncio.run(main()):
        # loop = asyncio.get_event_loop()
        # loop.run_until_complete(main())
        # loop.close()
    ```

Python kodu cihazınızdan IoT Central uygulamanıza bir ileti gönderdiğinde, iletiler IoT Central cihazınızın **ham veri** sekmesinde görüntülenir. Son iletileri göstermek için sayfayı yenilemeniz gerekebilir.

   :::image type="content" source="media/quickstart-send-telemetry-python/iot-central-telemetry-output.png" alt-text="Ham veri çıkışının IoT Central ekran görüntüsü":::

Cihazınız artık güvenli bir şekilde bağlanmış ve Azure IoT 'ye telemetri gönderiyor.

## <a name="clean-up-resources"></a>Kaynakları temizleme
Bu öğreticide oluşturulan IoT Central kaynaklara artık ihtiyacınız yoksa, IoT Central portalından silebilirsiniz. İsteğe bağlı olarak, bu kılavuzdaki belgelerden sonra devam etmeyi planlıyorsanız, oluşturduğunuz uygulamayı tutabilir ve diğer örnekler için onu yeniden kullanabilirsiniz.

Azure IoT Central örnek uygulamasını ve tüm cihazlarını ve kaynaklarını kaldırmak için:
1. Uygulamanızı **yönetme** seçeneğini belirleyin  >  .
1. **Sil**’i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir cihazı buluta güvenli bir şekilde bağlamak ve cihazdan buluta telemetri göndermek için temel bir Azure IoT uygulama iş akışı öğrendiniz. Bir uygulama ve cihaz oluşturmak için Azure IoT Central kullandınız, ardından sanal bir cihaz oluşturmak ve telemetri göndermek için Azure IoT Python SDK 'sını kullandınız. Telemetriyi izlemek için IoT Central de kullandınız.

Sonraki adım olarak, uygulama örnekleri aracılığıyla Azure IoT Python SDK 'sını keşfedebilirsiniz.

- [Zaman uyumsuz örnekler](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-hub-scenarios): Bu dizin, ek IoT Hub senaryolar için zaman uyumsuz Python örnekleri içerir.
- [Zaman uyumlu örnekler](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/sync-samples): Bu dizin Python 2,7 ile kullanmak için Python örnekleri veya Python 3.6 + için zaman uyumlu uyumluluk senaryoları içerir
- [IoT Edge örnekleri](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/async-edge-scenarios): Bu dizin, Edge modülleri ve aşağı akış cihazlarıyla çalışmak için Python örnekleri içerir.
