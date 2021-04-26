---
title: Azure IoT Hub modül kimliği ve modülü ikizi (Python)
description: Python için IoT SDK 'larını kullanarak modül kimliği oluşturma ve modül ikizi güncelleştirme hakkında bilgi edinin.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: menchi
ms.custom: devx-track-python
ms.openlocfilehash: 665281adc892e6b3655c0b1d0533cb3148e62940
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92139391"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>IoT Hub Module kimliği ve modülü ikizi (Python) ile çalışmaya başlama

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Modül kimlikleri ve modül TWINS](iot-hub-devguide-module-twins.md) , Azure IoT Hub cihaz kimliklerine ve cihaz TWINS 'e benzer, ancak daha ayrıntılı ayrıntı düzeyi sağlar. Azure IoT Hub cihaz kimlikleri ve cihaz TWINS 'i bir arka uç uygulamasını bir cihaz yapılandırmak ve cihazın koşullarına ilişkin görünürlük sağlamak için etkinleştirirken, modül kimlikleri ve modül TWINS, bir cihazın tek tek bileşenleri için bu özellikleri sağlar. İşletim sistemi tabanlı cihazlar veya bellenim cihazları gibi birden çok bileşeni olan uyumlu cihazlarda, her bileşen için yalıtılmış yapılandırma ve koşullara izin verir.
>

Bu öğreticinin sonunda üç Python uygulamanız vardır:

* Cihaz kimliği, modül kimliği ve cihaz ve modül istemcilerinizi bağlamak için ilişkili güvenlik anahtarları oluşturan **Createmodule**.

* **Updatemodületwindesiredproperties**, güncelleştirilmiş modül ikizi istenen özellikleri IoT Hub.

* Modüle ikizi istenen özellikler yaması modülünü alan **Receivemodületwindesiredpropertiespatch**.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub bağlantı dizesini al

Bu makalede, kimlik kayıt defterine bir cihaz ekleyen bir arka uç hizmeti oluşturursunuz ve ardından bu cihaza bir modül eklenir. Bu hizmet, **kayıt defteri yazma** iznini (Ayrıca, **kayıt defteri okuma** de içerir) gerektirir. Ayrıca, yeni oluşturulan modülün ikizi modülüne istenen özellikleri ekleyen bir hizmet oluşturursunuz. Bu hizmet, **hizmet bağlantısı** iznine sahip olmalıdır. Bu izinleri ayrı ayrı veren varsayılan paylaşılan erişim ilkeleri olsa da, bu bölümde bu izinlerin her ikisini de içeren özel bir paylaşılan erişim ilkesi oluşturacaksınız.

[!INCLUDE [iot-hub-include-find-service-regrw-connection-string](../../includes/iot-hub-include-find-service-regrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>IoT Hub bir cihaz kimliği ve modül kimliği oluşturma

Bu bölümde, IoT Hub 'ınızdaki kimlik kayıt defterinde bir cihaz kimliği ve bir modül kimliği oluşturan bir Python hizmeti uygulaması oluşturacaksınız. Kimlik kayıt defterinde bir girişi yoksa bir cihaz veya modül IoT Hub 'ına bağlanamaz. Daha fazla bilgi için bkz. [IoT Hub 'ınızdaki kimlik kayıt defterini anlama](iot-hub-devguide-identity-registry.md). Bu konsol uygulamasını çalıştırdığınızda, hem cihaz hem de modül için benzersiz bir kimlik ve anahtar oluşturur. Cihazınız ve modülünüz, IoT Hub’ına cihazdan buluta iletileri gönderdiğinde kendisini tanımlamak için bu değerleri kullanır. Kimlikler büyük/küçük harfe duyarlıdır.

1. Komut istemindeki **Azure-IoT-Hub** paketini yüklemek için aşağıdaki komutu çalıştırın:

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Komut istemindeki **msrest** paketini yüklemek için aşağıdaki komutu çalıştırın. **Httpoperationerror** özel durumlarını yakalamak için bu pakete ihtiyacınız vardır.

    ```cmd/sh
    pip install msrest
    ```

1. Bir metin düzenleyicisi kullanarak çalışma dizininizde **CreateModule.py** adlı bir dosya oluşturun.

1. Aşağıdaki kodu Python dosyanıza ekleyin. *Youriothubconnectionstring* öğesini [, IoT Hub bağlantı dizesini al](#get-the-iot-hub-connection-string)içinde kopyaladığınız bağlantı dizesiyle değiştirin.

    ```python
    import sys
    from msrest.exceptions import HttpOperationError
    from azure.iot.hub import IoTHubRegistryManager

    CONNECTION_STRING = "YourIotHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        try:
            # CreateDevice - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            device_state = "enabled"
            new_device = iothub_registry_manager.create_device_with_sas(
                DEVICE_ID, primary_key, secondary_key, device_state
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the device already exists.
                new_device = iothub_registry_manager.get_device(DEVICE_ID)
            else:
                raise

        print("device <" + DEVICE_ID +
              "> has primary key = " + new_device.authentication.symmetric_key.primary_key)

        try:
            # CreateModule - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            managed_by = ""
            new_module = iothub_registry_manager.create_module_with_sas(
                DEVICE_ID, MODULE_ID, managed_by, primary_key, secondary_key
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the module already exists.
                new_module = iothub_registry_manager.get_module(DEVICE_ID, MODULE_ID)
            else:
                raise

        print("device/module <" + DEVICE_ID + "/" + MODULE_ID +
              "> has primary key = " + new_module.authentication.symmetric_key.primary_key)

    except Exception as ex:
        print("Unexpected error {0}".format(ex))
    except KeyboardInterrupt:
        print("IoTHubRegistryManager sample stopped")
    ```

1. Komut isteminde aşağıdaki komutu çalıştırın:

    ```cmd/sh
    python CreateModule.py
    ```

Bu uygulama, **MYFIRSTDEVICE** kimliği ile bir cihaz kimliği ve myfirstdevice **adlı cihaz** altında **myfirstmodule** kimliğiyle bir modül kimliği oluşturur. (Cihaz veya modül KIMLIĞI kimlik kayıt defterinde zaten varsa, kod yalnızca var olan cihazı veya modül bilgilerini alır.) Uygulama, her kimlik için KIMLIĞI ve birincil anahtarı görüntüler.

> [!NOTE]
> IoT Hub kimlik kayıt defteri yalnızca IoT hub'ına güvenli erişim sağlamak amacıyla cihaz ve modül kimliklerini depolar. Kimlik kayıt defteri, cihaz kimliklerini ve anahtarlarını güvenlik kimlik bilgileri olarak kullanmak için depolar. Kimlik kayıt defterinin her cihaz için depoladığı etkin/devre dışı bayrağını kullanarak, ilgili cihaza erişimi devre dışı bırakabilirsiniz. Uygulamanızın cihaza özgü diğer meta verileri depolaması gerekiyorsa uygulamaya özgü bir depo kullanması gerekir. Modül kimlikleri için etkin/devre dışı bayrağı yoktur. Daha fazla bilgi için bkz. [IoT Hub 'ınızdaki kimlik kayıt defterini anlama](iot-hub-devguide-identity-registry.md).
>

## <a name="update-the-module-twin-using-python-service-sdk"></a>Python hizmeti SDK 'sını kullanarak modül ikizi güncelleştirme

Bu bölümde, istenen özellikleri ikizi modülünü güncelleştiren bir Python hizmeti uygulaması oluşturacaksınız.

1. Komut istemindeki **Azure-IoT-Hub** paketini yüklemek için aşağıdaki komutu çalıştırın. Önceki bölümde **Azure-IoT-Hub** paketini yüklediyseniz bu adımı atlayabilirsiniz.

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Bir metin düzenleyicisi kullanarak çalışma dizininizde **UpdateModuleTwinDesiredProperties.py** adlı bir dosya oluşturun.

1. Aşağıdaki kodu Python dosyanıza ekleyin. *Youriothubconnectionstring* öğesini [, IoT Hub bağlantı dizesini al](#get-the-iot-hub-connection-string)içinde kopyaladığınız bağlantı dizesiyle değiştirin.

    ```python
    import sys
    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties

    CONNECTION_STRING = "YourIoTHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        module_twin = iothub_registry_manager.get_module_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Module twin properties before update    :" )
        print ( "{0}".format(module_twin.properties) )

        # Update twin
        twin_patch = Twin()
        twin_patch.properties = TwinProperties(desired={"telemetryInterval": 122})
        updated_module_twin = iothub_registry_manager.update_module_twin(
            DEVICE_ID, MODULE_ID, twin_patch, module_twin.etag
        )
        print ( "" )
        print ( "Module twin properties after update     :" )
        print ( "{0}".format(updated_module_twin.properties) )

    except Exception as ex:
        print ( "Unexpected error {0}".format(ex) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

## <a name="get-updates-on-the-device-side"></a>Cihaz tarafında güncelleştirmeleri al

Bu bölümde, modülün ikizi istenen özellikler güncelleştirmesini sağlamak için bir Python uygulaması oluşturacaksınız.

1. Modül Bağlantı dizenizi alın. [Azure Portal](https://portal.azure.com/)' de, IoT Hub gidin ve sol bölmedeki **IoT cihazları** ' nı seçin. Cihaz listesinden **Myfirstdevice** ' ı seçin ve açın. **Modül kimlikleri** altında **myfirstmodule**' ü seçin. Modül bağlantı dizesini kopyalayın. Aşağıdaki adımda yapmanız gerekir.

   ![Azure portalı modül ayrıntısı](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

1. Komut istemindeki **Azure-IoT-Device** paketini yüklemek için aşağıdaki komutu çalıştırın:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Bir metin düzenleyicisi kullanarak çalışma dizininizde **ReceiveModuleTwinDesiredPropertiesPatch.py** adlı bir dosya oluşturun.

1. Aşağıdaki kodu Python dosyanıza ekleyin. *Yourmoduleconnectionstring* değerini, 1. adımda kopyaladığınız modül bağlantı dizesiyle değiştirin.

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient

    CONNECTION_STRING = "YourModuleConnectionString"


    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("")
            print("Twin desired properties patch received:")
            print(patch)

    def iothub_client_sample_run():
        try:
            module_client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(module_client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            while True:
                time.sleep(1000000)

        except KeyboardInterrupt:
            print("IoTHubModuleClient sample stopped")


    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

## <a name="run-the-apps"></a>Uygulamaları çalıştırma

Bu bölümde, **Receivemodületwindesiredpropertiespatch** cihaz uygulamasını çalıştırır ve ardından modülünüzün istenen özelliklerini güncelleştirmek Için **Updatemodületwindesiredproperties** hizmet uygulamasını çalıştırırsınız.

1. Bir komut istemi açın ve cihaz uygulamasını çalıştırın:

    ```cmd/sh
    python ReceiveModuleTwinDesiredPropertiesPatch.py
    ```

   ![Cihaz uygulaması ilk çıkışı](./media/iot-hub-python-python-module-twin-getstarted/device-1.png)

1. Ayrı bir komut istemi açın ve hizmet uygulamasını çalıştırın:

    ```cmd/sh
    python UpdateModuleTwinDesiredProperties.py
    ```

    **Telemetryınterval** Desired özelliğinin, Service App çıktındaki ikizi güncelleştirilmiş modülünde göründüğünü unutmayın:

   ![Hizmet uygulaması çıkışı](./media/iot-hub-python-python-module-twin-getstarted/service.png)

    Aynı özellik, cihaz uygulama çıkışındaki istenen özellikler düzeltme ekinde görüntülenir:

   ![Cihaz uygulama çıktısı istenen özellikler düzeltme ekini gösterir](./media/iot-hub-python-python-module-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Sonraki adımlar

IoT Hub’ı kullanmaya başlamak ve diğer IoT senaryolarını keşfetmek için bkz:

* [Cihaz yönetimini kullanmaya başlama](iot-hub-node-node-device-management-get-started.md)

* [IoT Edge ile çalışmaya başlama](../iot-edge/quickstart-linux.md)