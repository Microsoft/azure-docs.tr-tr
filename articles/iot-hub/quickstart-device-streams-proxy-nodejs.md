---
title: Hızlı başlangıç-Azure IoT Hub cihaz akışları SSH ve RDP için Node.js hızlı başlangıç
description: Bu hızlı başlangıçta, IoT Hub cihaz akışları üzerinde SSH ve RDP senaryolarını etkinleştirmek için proxy görevi gören bir örnek Node.js uygulaması çalıştırırsınız.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: references_regions, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 16daee2855b8e493521c1b40468e7068dac9e759
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477953"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Hızlı başlangıç: Node.js proxy uygulaması (Önizleme) kullanarak IoT Hub cihaz akışı üzerinden SSH ve RDP 'yi etkinleştirme

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Bu hızlı başlangıçta, cihaza bir cihaz akışı üzerinden gönderilmek üzere Secure Shell (SSH) ve Uzak Masaüstü Protokolü (RDP) trafiğinin etkinleştireceğinizi görürsünüz. Azure IoT Hub cihaz akışları, hizmet ve cihaz uygulamalarının güvenli ve güvenlik duvarı kullanımı kolay bir şekilde iletişim kurmasına olanak tanır. Bu hızlı başlangıçta hizmet tarafında çalışan bir Node.js proxy uygulamasının yürütülmesi açıklanmaktadır. Genel Önizleme sırasında, Node.js SDK yalnızca hizmet tarafında cihaz akışlarını destekler. Sonuç olarak, bu hızlı başlangıçta yalnızca hizmet yerel proxy uygulamasını çalıştırmaya yönelik yönergeler ele alınmaktadır.

## <a name="prerequisites"></a>Önkoşullar

* Bir C# proxy uygulaması kullanarak bir C proxy uygulaması kullanarak veya [IoT Hub cihaz akışları ÜZERINDEN SSH ve RDP 'yi](./quickstart-device-streams-proxy-csharp.md)etkinleştirerek [IoT Hub CIHAZ akışları ÜZERINDEN SSH ve RDP 'yi etkinleştirme](./quickstart-device-streams-proxy-c.md) işleminin tamamlanması.

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Node.js 10 +](https://nodejs.org).

    Aşağıdaki komutu kullanarak, geliştirme makinenizde geçerli Node.js sürümünü doğrulayabilirsiniz:

    ```cmd/sh
    node --version
    ```

* [Örnek bir Node.js projesi](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Microsoft Azure IoT Hub Şu anda cihaz akışlarını [Önizleme özelliği](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)olarak desteklemektedir.

> [!IMPORTANT]
> Cihaz akışlarının önizlemesi Şu anda yalnızca şu bölgelerde oluşturulan IoT Hub 'Lar için desteklenir:
>
> * Central US
> * EUAP Orta ABD
> * Kuzey Avrupa
> * Güneydoğu Asya

### <a name="add-azure-iot-extension"></a>Azure IoT uzantısı ekleme

Aşağıdaki komutu çalıştırarak Azure CLı için Azure IoT uzantısını Cloud Shell örneğine ekleyin. IoT uzantısı, Azure CLı için IoT Hub, IoT Edge ve IoT cihazı sağlama hizmeti 'ne (DPS) özgü komutlar ekler.

```azurecli-interactive
az extension add --name azure-iot
```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

Önceki [Hızlı Başlangıç: Bir cihazdan IoT hub’a telemetri gönderme](quickstart-send-telemetry-node.md) öğreticisini tamamladıysanız bu adımı atlayabilirsiniz.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Cihaz kaydetme

[Hızlı başlangıç: bir cihazdan IoT Hub 'ına telemetri gönderme](quickstart-send-telemetry-node.md)işlemini tamamladıysanız, bu adımı atlayabilirsiniz.

Bir cihazın bağlanabilmesi için IoT hub’ınıza kaydedilmesi gerekir. Bu bölümde, sanal bir cihazı kaydetmek için Azure Cloud Shell kullanırsınız.

1. Cihaz kimliğini oluşturmak için Cloud Shell ' de aşağıdaki komutu çalıştırın:

   > [!NOTE]
   > * *Youriothubname* yer tutucusunu, IoT Hub 'ınız için seçtiğiniz adla değiştirin.
   > * Kaydolduğunuz aygıtın adı için *mydevice* ' ın gösterildiği gibi kullanılması önerilir. Cihazınız için farklı bir ad seçerseniz bu adı bu makale boyunca kullanın ve uygulamayı çalıştırmadan önce örnek uygulamalarda cihaz adını güncelleştirin.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Arka uç uygulamasının IoT Hub 'ınıza bağlanmasını ve iletileri almanızı sağlamak için bir *hizmet bağlantı dizesi* de gereklidir. Aşağıdaki komut, IoT Hub 'ınız için dizeyi alır:

   > [!NOTE]
   > *Youriothubname* yer tutucusunu, IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub connection-string show --policy-name service --hub-name {YourIoTHubName} --output table
    ```

   Daha sonra bu hızlı başlangıçta kullanılmak üzere döndürülen hizmet bağlantı dizesine göz önünde edin. Aşağıdaki örneğe benzer şekilde görünür:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>Cihaz akışları aracılığıyla bir cihaza SSH

Bu bölümde, SSH trafiğini tünele bir uçtan uca akış kurarsınız.

### <a name="run-the-device-local-proxy-application"></a>Cihaz yerel proxy uygulamasını çalıştırma

Daha önce belirtildiği gibi, IoT Hub Node.js SDK yalnızca hizmet tarafında cihaz akışlarını destekler. Cihaz yerel uygulaması için, aşağıdaki hızlı başlangıçlardan birinde bulunan bir cihaz proxy uygulaması kullanın:

   * [C proxy uygulaması kullanarak IoT Hub cihaz akışları üzerinden SSH ve RDP 'yi etkinleştirme](./quickstart-device-streams-proxy-c.md)
   * [C# proxy uygulaması kullanarak IoT Hub cihaz akışları üzerinden SSH ve RDP 'yi etkinleştirme](./quickstart-device-streams-proxy-csharp.md) 

Sonraki adıma geçmeden önce, cihaz yerel proxy uygulamasının çalıştığından emin olun. Kuruluma genel bakış için bkz. [yerel proxy örneği](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

### <a name="run-the-service-local-proxy-application"></a>Hizmet yerel proxy uygulamasını çalıştırma

Bu makalede, SSH için kurulum (bağlantı noktası 22 kullanılarak) açıklanmakta ve ardından RDP (3389 numaralı bağlantı noktasını kullanan) için kurulumu nasıl değiştireceğiniz açıklanır. Cihaz akışları uygulama ve protokol belirsiz olduğundan, aynı örneği, genellikle iletişim bağlantı noktasını değiştirerek diğer istemci-sunucu uygulama trafiği türlerine uyum sağlayacak şekilde değiştirebilirsiniz.

Çalıştıran cihaz yerel proxy uygulaması ile, yerel bir Terminal penceresinde aşağıdakileri yaparak Node.js yazılan hizmet yerel ara sunucu uygulamasını çalıştırın:

1. Ortam değişkenleri için, hizmet kimlik bilgilerinizi, SSH arka plan programının çalıştığı hedef cihaz KIMLIĞINI ve cihazda çalışan ara sunucunun bağlantı noktası numarasını sağlayın.

   ```
   # In Linux
   export IOTHUB_CONNECTION_STRING="{ServiceConnectionString}"
   export STREAMING_TARGET_DEVICE="MyDevice"
   export PROXY_PORT=2222

   # In Windows
   SET IOTHUB_CONNECTION_STRING={ServiceConnectionString}
   SET STREAMING_TARGET_DEVICE=MyDevice
   SET PROXY_PORT=2222
   ```

   ServiceConnectionString yer tutucusunu, hizmet bağlantı dizeniz ile eşleşecek şekilde değiştirin **ve daha** sonra farklı bir ad verırsenız cihaz Kimliğinizle eşleşmesi gerekir.

1. `Quickstarts/device-streams-service`Sıkıştırılmış proje klasörünüzdeki dizine gidin. Hizmet yerel proxy uygulamasını çalıştırmak için aşağıdaki kodu kullanın:

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>Cihaz akışları aracılığıyla cihazınıza SSH

Linux 'ta, bir terminalde kullanarak SSH 'yi çalıştırın `ssh $USER@localhost -p 2222` . Windows 'ta, en sevdiğiniz SSH istemcisini (örneğin, PuTTY) kullanın.

SSH oturumu oluşturulduktan sonra, hizmet yerel üzerinde konsol çıkışı kurulduktan sonra (hizmet yerel proxy uygulaması 2222 numaralı bağlantı noktasını dinler):

![SSH Terminal çıkışı](./media/quickstart-device-streams-proxy-nodejs/service-console-output.png)

SSH istemci uygulamasının konsol çıkışı (SSH istemcisi, hizmet yerel proxy uygulamasının dinlediği bağlantı noktası 22 ' ye bağlanarak SSH daemon ile iletişim kurar):

![SSH istemci çıktısı](./media/quickstart-device-streams-proxy-nodejs/ssh-console-output.png)

### <a name="rdp-to-your-device-via-device-streams"></a>Cihaz akışları aracılığıyla cihazınıza RDP

Şimdi, RDP istemci uygulamanızı kullanın ve daha önce seçtiğiniz rastgele bir bağlantı noktası 2222 numaralı bağlantı noktası üzerindeki hizmet proxy 'sine bağlanın.

> [!NOTE]
> Cihaz proxy 'nizin RDP için doğru yapılandırıldığından ve RDP bağlantı noktası 3389 ile yapılandırıldığından emin olun.

![RDP istemcisi, hizmet yerel ara sunucu uygulamasına bağlanır](./media/quickstart-device-streams-proxy-nodejs/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir IoT Hub 'ı ayarlarsınız, bir cihaz kaydettiniz ve bir IoT cihazında RDP ve SSH 'yi etkinleştirmek için bir hizmet proxy uygulaması dağıttınız. RDP ve SSH trafiği, IoT Hub aracılığıyla bir cihaz akışı aracılığıyla tünellendirilecektir. Bu işlem, cihaza doğrudan bağlantı gereksinimini ortadan kaldırır.

Cihaz akışları hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Cihaz akışlarına genel bakış](./iot-hub-device-streams-overview.md)
