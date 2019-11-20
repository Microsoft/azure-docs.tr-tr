---
title: Azure IoT Hub cihaz akışları ile Node. js ' de cihaz uygulamasıyla iletişim kurma
description: Bu hızlı başlangıçta, bir cihaz akışı aracılığıyla IoT cihazıyla iletişim kuran bir Node. js hizmet tarafı uygulaması çalıştıracaksınız.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 538e04d7ae4f6528c26762a8efac06d02b4f86bc
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083740"
---
# <a name="quickstart-communicate-to-a-device-application-in-nodejs-via-iot-hub-device-streams-preview"></a>Hızlı başlangıç: IoT Hub cihaz akışları aracılığıyla Node. js ' de bir cihaz uygulamasıyla Iletişim kurma (Önizleme)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Microsoft Azure IoT Hub Şu anda cihaz akışlarını [Önizleme özelliği](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)olarak desteklemektedir.

[Cihaz akışları IoT Hub](./iot-hub-device-streams-overview.md) hizmet ve cihaz uygulamalarının güvenli ve güvenlik duvarı kolay bir şekilde iletişim kurmasına olanak tanır. Genel Önizleme sırasında Node. js SDK yalnızca hizmet tarafında cihaz akışlarını destekler. Sonuç olarak, bu hızlı başlangıç yalnızca hizmet tarafı uygulamasını çalıştırma yönergelerini ele alır. Aşağıdaki hızlı başlangıçlardan biri ile birlikte bir cihaz tarafı uygulaması çalıştırmalısınız:

* [IoT Hub cihaz akışları aracılığıyla C 'de cihaz uygulamalarıyla iletişim kurma](./quickstart-device-streams-echo-c.md)

* [IoT Hub cihaz akışlarındaki C# cihaz uygulamalarıyla iletişim kurun](./quickstart-device-streams-echo-csharp.md).

Bu hızlı başlangıçta hizmet tarafı Node. js uygulaması aşağıdaki işlevlere sahiptir:

* IoT cihazına bir cihaz akışı oluşturur.

* Komut satırından girişi okur ve cihaz uygulamasına gönderir ve bunu yeniden yankılendirilecektir.

Kod, bir cihaz akışının başlatma işlemini ve veri göndermek ve almak için nasıl kullanılacağını gösterir.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Cihaz akışlarının önizlemesi Şu anda yalnızca şu bölgelerde oluşturulan IoT Hub 'Lar için desteklenir:

  * Orta ABD
  * EUAP Orta ABD
  * Kuzey Avrupa
  * Güneydoğu Asya

Bu hızlı başlangıçta hizmet tarafı uygulamasını çalıştırmak için, geliştirme makinenizde Node. js ile v10 arasındaki. x. x veya üzeri gerekir.

[NodeJS.org](https://nodejs.org)adresinden birden çok platform için Node. js ' ye indirebilirsiniz.

Aşağıdaki komutu kullanarak geliştirme makinenizde geçerli Node.js sürümünü doğrulayabilirsiniz:

```cmd/sh
node --version
```

Azure CLı için Microsoft Azure IoT uzantısını Cloud Shell örneğinize eklemek için aşağıdaki komutu çalıştırın. IOT uzantısı, Azure CLı 'ye IoT Hub, IoT Edge ve IoT cihaz sağlama hizmeti (DPS) komutları ekler.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Örnek Node.js projesini önceden indirmediyseniz https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip adresinden indirip ZIP arşivini ayıklayın.

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

Önceki [Hızlı Başlangıç: Bir cihazdan IoT hub’a telemetri gönderme](quickstart-send-telemetry-node.md) öğreticisini tamamladıysanız bu adımı atlayabilirsiniz.

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

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
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Daha sonra bu hızlı başlangıçta kullanılmak üzere döndürülen hizmet bağlantı dizesine göz önünde edin. Aşağıdaki örneğe benzer şekilde görünür:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>Cihaz akışları aracılığıyla cihaz ve hizmet arasında iletişim kurma

Bu bölümde hem cihaz tarafı uygulamasını hem de hizmet tarafı uygulamasını çalıştırırsınız ve iki arasında iletişim kurabilirsiniz.

### <a name="run-the-device-side-application"></a>Cihaz tarafı uygulamayı çalıştırma

Daha önce belirtildiği gibi, IoT Hub Node. js SDK yalnızca hizmet tarafında cihaz akışlarını destekler. Cihaz tarafı bir uygulama için, bu hızlı başlangıçlarda bulunan cihaz programlarından birini kullanın:

   * [IoT Hub cihaz akışları aracılığıyla C 'de cihaz uygulamalarıyla iletişim kurma](./quickstart-device-streams-echo-c.md)

   * [IoT Hub cihaz akışları C# aracılığıyla cihaz uygulamalarıyla iletişim kurma](./quickstart-device-streams-echo-csharp.md)

Sonraki adıma geçmeden önce cihaz tarafı uygulamasının çalıştığından emin olun.

### <a name="run-the-service-side-application"></a>Hizmet tarafı uygulamasını çalıştırma

Cihaz tarafı uygulamasının çalıştığını varsayarsak, Node. js ' de hizmet tarafı uygulamasını çalıştırmak için bir yerel Terminal penceresinde aşağıdaki adımları izleyin:

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

* Sıkıştırılmış proje klasörünüzdeki `Quickstarts/device-streams-service` gidin ve düğümü kullanarak örneği çalıştırın.

   ```cmd/sh
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service
    
   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   node echo.js
   ```

Son adımın sonunda, hizmet tarafı programı cihazınıza bir akış başlatır ve oluşturulduktan sonra, hizmete akış üzerinden bir dize arabelleği gönderir. Bu örnekte, hizmet tarafı programı terminaldeki `stdin` okur ve cihazı cihaza gönderir ve ardından onu geri yankıdan alır. Bu, iki uygulama arasındaki başarılı çift yönlü iletişimi gösterir.

![Hizmet tarafı konsol çıkışı](./media/quickstart-device-streams-echo-nodejs/service-console-output.png)

Daha sonra ENTER tuşuna basarak programı sonlandırabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir IoT Hub 'ı ayarlarsınız, cihaz kaydettiniz, cihaz ve hizmet tarafındaki uygulamalar arasında bir cihaz akışı kurdu ve bu akışı, verileri uygulamalar arasında ileri ve geri göndermek için kullandınız.

Cihaz akışları hakkında daha fazla bilgi edinmek için aşağıdaki bağlantıları kullanın:

> [!div class="nextstepaction"]
> [Cihaz akışlarına genel bakış](./iot-hub-device-streams-overview.md) 