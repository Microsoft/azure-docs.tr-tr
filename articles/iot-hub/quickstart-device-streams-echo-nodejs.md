---
title: Hızlı başlangıç-Azure IoT Hub cihaz akışları ile Node.js cihaz uygulamasıyla Iletişim kurma
description: Bu hızlı başlangıçta, bir cihaz akışı aracılığıyla IoT cihazıyla iletişim kuran bir Node.js hizmet tarafı uygulaması çalıştıracaksınız.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 335014f032162866e4780bf1294ddcd108b4fd03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624397"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Hızlı başlangıç: IoT Hub cihaz akışları aracılığıyla Node.js bir cihaz uygulamasıyla Iletişim kurma (Önizleme)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Bu hızlı başlangıçta, bir hizmet tarafı uygulaması çalıştırır ve cihaz akışlarını kullanarak bir cihaz ve hizmet arasında iletişim kurabilirsiniz. Azure IoT Hub cihaz akışları, hizmet ve cihaz uygulamalarının güvenli ve güvenlik duvarı kullanımı kolay bir şekilde iletişim kurmasına olanak tanır. Genel Önizleme sırasında, Node.js SDK yalnızca hizmet tarafında cihaz akışlarını destekler. Sonuç olarak, bu hızlı başlangıç yalnızca hizmet tarafı uygulamasını çalıştırma yönergelerini ele alır.

## <a name="prerequisites"></a>Önkoşullar

* [IoT Hub cihaz akışları aracılığıyla C 'deki cihaz uygulamalarıyla Iletişim kurmayı](./quickstart-device-streams-echo-c.md) tamamlama veya [IoT Hub cihaz akışları aracılığıyla C# ' deki cihaz uygulamalarıyla iletişim](./quickstart-device-streams-echo-csharp.md)kurma.

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Node.js 10 +](https://nodejs.org).

    Aşağıdaki komutu kullanarak geliştirme makinenizde geçerli Node.js sürümünü doğrulayabilirsiniz:

    ```cmd/sh
    node --version
    ```

* [Örnek bir Node.js projesi](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

Microsoft Azure IoT Hub Şu anda cihaz akışlarını [Önizleme özelliği](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)olarak desteklemektedir.

> [!IMPORTANT]
> Cihaz akışlarının önizlemesi Şu anda yalnızca şu bölgelerde oluşturulan IoT Hub 'Lar için desteklenir:
>
> * Central US
> * EUAP Orta ABD
> * Kuzey Avrupa
> * Güneydoğu Asya

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

Önceki [Hızlı Başlangıç: Bir cihazdan IoT hub’a telemetri gönderme](quickstart-send-telemetry-node.md) öğreticisini tamamladıysanız bu adımı atlayabilirsiniz.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Cihaz kaydetme

Önceki [Hızlı Başlangıç: Bir cihazdan IoT hub’a telemetri gönderme](quickstart-send-telemetry-node.md) öğreticisini tamamladıysanız bu adımı atlayabilirsiniz.

Bir cihazın bağlanabilmesi için IoT hub’ınıza kaydedilmesi gerekir. Bu hızlı başlangıçta Azure Cloud Shell kullanarak bir simülasyon cihazı kaydedeceksiniz.

1. Cihaz kimliğini oluşturmak için Azure Cloud Shell aşağıdaki komutu çalıştırın.

   **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

   **Mydevice**: Bu, kaydetmekte olduğunuz cihazın adıdır. Aşağıda gösterildiği gibi **Mydevice** kullanılması önerilir. Cihazınız için farklı bir ad seçerseniz, bu adı da bu makalede kullanmanız gerekir ve bunları çalıştırmadan önce örnek uygulamalarda cihaz adını güncelleştirin.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

2. Arka uç uygulamasının IoT hub’ınıza bağlanmasına ve iletileri almasına olanak sağlamak için bir *hizmet bağlantı dizesi* de gerekir. Aşağıdaki komut, IoT hub'ınız için hizmeti bağlantı dizesini alır:

    **Youriothubname**: aşağıdaki yer tutucuyu IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub connection-string show --policy-name service --name {YourIoTHubName} --output table
    ```

    Daha sonra bu hızlı başlangıçta kullanılmak üzere döndürülen hizmet bağlantı dizesine göz önünde edin. Aşağıdaki örneğe benzer şekilde görünür:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Cihaz akışları aracılığıyla cihaz ve hizmet arasında iletişim kurma

Bu bölümde hem cihaz tarafı uygulamasını hem de hizmet tarafı uygulamasını çalıştırırsınız ve iki arasında iletişim kurabilirsiniz.

### <a name="run-the-device-side-application"></a>Cihaz tarafı uygulamayı çalıştırma

Daha önce belirtildiği gibi, IoT Hub Node.js SDK yalnızca hizmet tarafında cihaz akışlarını destekler. Cihaz tarafı bir uygulama için, bu hızlı başlangıçlarda bulunan cihaz programlarından birini kullanın:

* [IoT Hub cihaz akışları aracılığıyla C 'de cihaz uygulamalarıyla iletişim kurma](./quickstart-device-streams-echo-c.md)

* [IoT Hub cihaz akışları aracılığıyla C# ' deki cihaz uygulamalarıyla iletişim kurma](./quickstart-device-streams-echo-csharp.md)

Sonraki adıma geçmeden önce cihaz tarafı uygulamasının çalıştığından emin olun.

### <a name="run-the-service-side-application"></a>Hizmet tarafı uygulamasını çalıştırma

Bu hızlı başlangıçta hizmet tarafı Node.js uygulaması aşağıdaki işlevlere sahiptir:

* IoT cihazına bir cihaz akışı oluşturur.
* Komut satırından girişi okur ve cihaz uygulamasına gönderir ve bunu yeniden yankılendirilecektir.

Kod, bir cihaz akışının başlatma işlemini ve veri göndermek ve almak için nasıl kullanılacağını gösterir.

Cihaz tarafı uygulamasının çalıştığını varsayarsak, Node.js hizmet tarafı uygulamasını çalıştırmak için bir yerel Terminal penceresinde aşağıdaki adımları izleyin:

* Hizmet kimlik bilgilerinizi ve cihaz KIMLIĞINIZI ortam değişkenleri olarak sağlayın.
 
   ```cmd/sh
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   ```
  
   ServiceConnectionString yer tutucusunu, hizmet bağlantı dizeniz ile eşleşecek şekilde değiştirin **ve daha** sonra farklı bir ad verırsenız cihaz Kimliğinizle eşleşmesi gerekir.

* `Quickstarts/device-streams-service`Sıkıştırılmış proje klasörünüzde öğesine gidin ve düğümü kullanarak örneği çalıştırın.

   ```cmd/sh
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

Son adımın sonunda, hizmet tarafı programı cihazınıza bir akış başlatır ve oluşturulduktan sonra, hizmete akış üzerinden bir dize arabelleği gönderir. Bu örnekte, hizmet tarafı programı `stdin` terminaldeki öğesini okur ve cihaza gönderir ve daha sonra geri yankılendirilecektir. Bu, iki uygulama arasındaki başarılı çift yönlü iletişimi gösterir.

![Hizmet tarafı konsol çıkışı](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

Daha sonra ENTER tuşuna basarak programı sonlandırabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir IoT Hub 'ı ayarlarsınız, cihaz kaydettiniz, cihaz ve hizmet tarafındaki uygulamalar arasında bir cihaz akışı kurdu ve bu akışı, verileri uygulamalar arasında ileri ve geri göndermek için kullandınız.

Cihaz akışları hakkında daha fazla bilgi edinmek için aşağıdaki bağlantıları kullanın:

> [!div class="nextstepaction"]
> [Cihaz akışlarına genel bakış](./iot-hub-device-streams-overview.md)
