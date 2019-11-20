---
title: Python ile Azure IoT Hub dosyaları cihazlardan yükleme | Microsoft Docs
description: Python için Azure IoT cihaz SDK 'sını kullanarak bir cihazdan buluta dosya yükleme. Karşıya yüklenen dosyalar bir Azure Storage blob kapsayıcısında depolanır.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 6dfbcc7a3e76842546326742d801c913451855f3
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001118"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>IoT Hub (Python) ile cihazınızdan buluta dosya yükleme

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Bu makalede, [Azure Blob depolamaya](../storage/index.yml)bir dosyayı karşıya yüklemek için [IoT Hub dosya yükleme yeteneklerinin](iot-hub-devguide-file-upload.md) nasıl kullanılacağı gösterilmektedir. Öğretici şunların nasıl yapıldığını göstermektedir:

* Bir dosyayı karşıya yüklemek için güvenli bir şekilde depolama kapsayıcısı sağlayın.

* IoT Hub 'ınız aracılığıyla bir dosyayı karşıya yüklemek için Python istemcisini kullanın.

[Bir cihazdan IoT Hub 'ına hızlı başlangıç Telemetriyi](quickstart-send-telemetry-python.md) , IoT Hub temel cihazdan buluta mesajlaşma işlevlerini gösterir. Ancak, bazı senaryolarda, cihazlarınızın IoT Hub kabul ettiği görece küçük cihazdan buluta iletileri içine gönderdikleri verileri kolayca eşleyemezsiniz. Dosyaları bir cihazdan kapladığınızda, IoT Hub güvenlik ve güvenilirliğini kullanmaya devam edebilirsiniz.

> [!NOTE]
> IoT Hub Python SDK Şu anda yalnızca **. txt** dosyaları gibi karakter tabanlı dosyaları karşıya yüklemeyi desteklemektedir.

Bu öğreticinin sonunda Python konsol uygulamasını çalıştırırsınız:

* Python cihaz SDK 'sını kullanarak bir dosyayı depolamaya yükleyen **FileUpload.py**.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

> [!NOTE]
> Bu kılavuz, yeni v2 SDK 'sında dosya karşıya yükleme özelliği henüz uygulanmadığı için kullanımdan kaldırılan v1 Python SDK 'sını kullanır.

## <a name="prerequisites"></a>Önkoşullar

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Bir cihaz uygulamasından bir dosya yükleme

Bu bölümde, IoT Hub 'a bir dosya yüklemek üzere cihaz uygulaması oluşturacaksınız.

1. Komut istemindeki **Azure-ıothub-Device-Client** paketini yüklemek için aşağıdaki komutu çalıştırın:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. Bir metin düzenleyicisi kullanarak, blob depolamaya yükleyeceğiniz bir test dosyası oluşturun.

    > [!NOTE]
    > IoT Hub Python SDK Şu anda yalnızca **. txt** dosyaları gibi karakter tabanlı dosyaları karşıya yüklemeyi desteklemektedir.

3. Bir metin düzenleyicisi kullanarak çalışma klasörünüzde bir **FileUpload.py** dosyası oluşturun.

4. **FileUpload.py** dosyasının başlangıcında `import` aşağıdaki deyimleri ve değişkenleri ekleyin. 

    ```python
    import time
    import sys
    import iothub_client
    import os
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError

    CONNECTION_STRING = "[Device Connection String]"
    PROTOCOL = IoTHubTransportProvider.HTTP

    PATHTOFILE = "[Full path to file]"
    FILENAME = "[File name for storage]"
    ```

5. Dosyanızda, ' yi IoT `[Device Connection String]` hub cihazınızın bağlantı dizesiyle değiştirin. Oluşturduğunuz `[Full path to file]` test dosyasının yoluyla veya cihazınızda karşıya yüklemek istediğiniz herhangi bir dosyayı değiştirin. Blob `[File name for storage]` depolamaya yüklendikten sonra dosyanıza vermek istediğiniz adla değiştirin. 

6. **Upload_blob** işlevi için bir geri çağırma oluşturun:

    ```python
    def blob_upload_conf_callback(result, user_context):
        if str(result) == 'OK':
            print ( "...file uploaded successfully." )
        else:
            print ( "...file upload callback returned: " + str(result) )
    ```

7. İstemcisini bağlamak ve dosyayı karşıya yüklemek için aşağıdaki kodu ekleyin. Ayrıca, `main` yordamını da dahil edin:

    ```python
    def iothub_file_upload_sample_run():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

            f = open(PATHTOFILE, "r")
            content = f.read()

            client.upload_blob_async(FILENAME, content, len(content), blob_upload_conf_callback, 0)

            print ( "" )
            print ( "File upload initiated..." )

            while True:
                time.sleep(30)

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
        except:
            print ( "generic error" )

    if __name__ == '__main__':
        print ( "Simulating a file upload using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_file_upload_sample_run()
    ```

8. **UploadFile.py** dosyasını kaydedin ve kapatın.

## <a name="run-the-application"></a>Uygulamayı çalıştırma

Artık uygulamayı çalıştırmaya hazırsınız.

1. Çalışma klasörünüzdeki bir komut isteminde aşağıdaki komutu çalıştırın:

    ```cmd/sh
    python FileUpload.py
    ```

2. Aşağıdaki ekran görüntüsünde, **FileUpload** uygulamasının çıktısı gösterilmektedir:

    ![Benzetimli cihaz uygulamasından çıkış](./media/iot-hub-python-python-file-upload/1.png)

3. Yapılandırdığınız depolama kapsayıcısında karşıya yüklenen dosyayı görüntülemek için portalını kullanabilirsiniz:

    ![Karşıya yüklenen dosya](./media/iot-hub-python-python-file-upload/2.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, cihazların dosya karşıya yüklemelerini basitleştirmek için IoT Hub dosya yükleme yeteneklerini nasıl kullanacağınızı öğrendiniz. Aşağıdaki makalelerle IoT Hub özelliklerini ve senaryolarını keşfetmeye devam edebilirsiniz:

* [Programlama yoluyla IoT Hub 'ı oluşturma](iot-hub-rm-template-powershell.md)

* [C SDK 'ya giriş](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK’ları](iot-hub-devguide-sdks.md)
