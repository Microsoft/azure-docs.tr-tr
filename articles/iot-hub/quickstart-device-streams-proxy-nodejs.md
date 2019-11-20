---
title: Azure IoT Hub cihaz akışları SSH ve RDP için Node. js hızlı başlangıç
description: Bu hızlı başlangıçta, IoT Hub cihaz akışları üzerinden SSH ve RDP senaryolarını etkinleştirmek için proxy görevi gören örnek bir Node. js uygulaması çalıştırırsınız.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 7f2b98f196a0889406e7821c60db7066a21b9178
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084279"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-nodejs-proxy-application-preview"></a>Hızlı başlangıç: node. js proxy uygulaması (Önizleme) kullanarak IoT Hub cihaz akışı üzerinden SSH ve RDP 'yi etkinleştirme

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub Şu anda cihaz akışlarını [Önizleme özelliği](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)olarak desteklemektedir.

[Cihaz akışları IoT Hub](./iot-hub-device-streams-overview.md) hizmet ve cihaz uygulamalarının güvenli ve güvenlik duvarı kolay bir şekilde iletişim kurmasına olanak tanır. 

Bu hızlı başlangıç, hizmet tarafında çalışan bir Node. js proxy uygulamasının yürütülmesini açıklar. Bu, cihaza bir cihaz akışı üzerinden Secure Shell (SSH) ve Uzak Masaüstü Protokolü (RDP) trafiğinin gönderilmesini sağlar. Kuruluma genel bakış için bkz. [yerel proxy örneği](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp). 

Genel Önizleme sırasında Node. js SDK 'Sı yalnızca hizmet tarafında cihaz akışlarını destekler. Sonuç olarak, bu hızlı başlangıçta yalnızca hizmet yerel proxy uygulamasını çalıştırmaya yönelik yönergeler ele alınmaktadır. Cihaz yerel proxy uygulamasını çalıştırmak için, bkz:  

   * [C proxy uygulaması kullanarak IoT Hub cihaz akışları üzerinden SSH ve RDP 'yi etkinleştirme](./quickstart-device-streams-proxy-c.md)
   * [C# Proxy uygulaması kullanarak IoT Hub cihaz akışları üzerinden SSH ve RDP 'yi etkinleştirme](./quickstart-device-streams-proxy-csharp.md)

Bu makalede, SSH için kurulum (bağlantı noktası 22 kullanılarak) açıklanmakta ve ardından RDP (3389 numaralı bağlantı noktasını kullanan) için kurulumu nasıl değiştireceğiniz açıklanır. Cihaz akışları uygulama ve protokol belirsiz olduğundan, aynı örneği, genellikle iletişim bağlantı noktasını değiştirerek diğer istemci-sunucu uygulama trafiği türlerine uyum sağlayacak şekilde değiştirebilirsiniz.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

* Cihaz akışlarının önizlemesi Şu anda yalnızca şu bölgelerde oluşturulan IoT Hub 'lar için desteklenmektedir:

  * Orta ABD
  * EUAP Orta ABD
  * Güneydoğu Asya
  * Kuzey Avrupa
  

* Bu hızlı başlangıçta hizmet yerel uygulamasını çalıştırmak için, geliştirme makinenizde Node. js ile v10 arasındaki. x. x veya üzeri gerekir.
  * Birden çok platform için [Node. js](https://nodejs.org) ' ye indirin.
  * Aşağıdaki komutu kullanarak, geliştirme makinenizde geçerli Node. js sürümünü doğrulayın:

   ```
   node --version
   ```

* Aşağıdaki komutu çalıştırarak Azure CLı için Azure IoT uzantısını Cloud Shell örneğine ekleyin. IOT uzantısı, Azure CLı için IoT Hub, IoT Edge ve IoT cihazı sağlama hizmeti 'ne (DPS) özgü komutlar ekler.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* Daha önce yapmadıysanız, [örnek Node. js projesini indirin](https://github.com/Azure-Samples/azure-iot-samples-node/archive/streams-preview.zip) ve ZIP arşivini ayıklayın.

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

Önceki [Hızlı Başlangıç: Bir cihazdan IoT hub’a telemetri gönderme](quickstart-send-telemetry-node.md) öğreticisini tamamladıysanız bu adımı atlayabilirsiniz.

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

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

1. Arka uç uygulamasının IoT Hub 'ınıza bağlanmasını ve iletileri almanızı sağlamak için bir *hizmet bağlantı dizesi*de gereklidir. Aşağıdaki komut, IoT Hub 'ınız için dizeyi alır:

   > [!NOTE]
   > *Youriothubname* yer tutucusunu, IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

   Daha sonra bu hızlı başlangıçta kullanılmak üzere döndürülen hizmet bağlantı dizesine göz önünde edin. Aşağıdaki örneğe benzer şekilde görünür:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="ssh-to-a-device-via-device-streams"></a>Cihaz akışları aracılığıyla bir cihaza SSH

Bu bölümde, SSH trafiğini tünele bir uçtan uca akış kurarsınız.

### <a name="run-the-device-local-proxy-application"></a>Cihaz yerel proxy uygulamasını çalıştırma

Daha önce belirtildiği gibi, IoT Hub Node. js SDK 'Sı yalnızca hizmet tarafında cihaz akışlarını destekler. Cihaz yerel uygulaması için, aşağıdaki hızlı başlangıçlardan birinde bulunan bir cihaz proxy uygulaması kullanın:

   * [C proxy uygulaması kullanarak IoT Hub cihaz akışları üzerinden SSH ve RDP 'yi etkinleştirme](./quickstart-device-streams-proxy-c.md)
   * [C# Proxy uygulaması kullanarak IoT Hub cihaz akışları üzerinden SSH ve RDP 'yi etkinleştirme](./quickstart-device-streams-proxy-csharp.md) 

Sonraki adıma geçmeden önce, cihaz yerel proxy uygulamasının çalıştığından emin olun.

### <a name="run-the-service-local-proxy-application"></a>Hizmet yerel proxy uygulamasını çalıştırma

Çalıştıran cihaz yerel proxy uygulaması ile, yerel bir Terminal penceresinde aşağıdakileri yaparak Node. js ' de yazılmış hizmet yerel ara sunucu uygulamasını çalıştırın:

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

1. Sıkıştırılmış proje klasörünüzdeki `Quickstarts/device-streams-service` dizinine gidin. Hizmet yerel proxy uygulamasını çalıştırmak için aşağıdaki kodu kullanın:

   ```
   cd azure-iot-samples-node-streams-preview/iot-hub/Quickstarts/device-streams-service

   # Install the preview service SDK, and other dependencies
   npm install azure-iothub@streams-preview
   npm install

   # Run the service-local proxy application
   node proxy.js
   ```

### <a name="ssh-to-your-device-via-device-streams"></a>Cihaz akışları aracılığıyla cihazınıza SSH

Linux 'ta, bir terminalde `ssh $USER@localhost -p 2222` kullanarak SSH 'yi çalıştırın. Windows 'ta, en sevdiğiniz SSH istemcisini (örneğin, PuTTY) kullanın.

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
