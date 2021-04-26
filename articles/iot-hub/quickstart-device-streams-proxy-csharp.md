---
title: Hızlı başlangıç-Azure IoT Hub cihaz akışları SSH ve RDP için C# hızlı başlangıcı
description: Bu hızlı başlangıçta, SSH ve RDP senaryolarını IoT Hub bir cihaz akışı üzerinden etkinleştiren iki örnek C# uygulaması çalıştırırsınız.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: references_regions, devx-track-azurecli
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 5bd33a2da6b2f1ae775f088950f14ac4df465fbf
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863956"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>Hızlı başlangıç: bir C# proxy uygulaması (Önizleme) kullanarak IoT Hub cihaz akışı üzerinden SSH ve RDP 'yi etkinleştirme

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Microsoft Azure IoT Hub Şu anda cihaz akışlarını [Önizleme özelliği](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)olarak desteklemektedir.

[Cihaz akışları IoT Hub](iot-hub-device-streams-overview.md) hizmet ve cihaz uygulamalarının güvenli ve güvenlik duvarı kolay bir şekilde iletişim kurmasına olanak tanır. Bu hızlı başlangıç kılavuzunda, istemci-sunucu uygulama trafiğinin (örneğin, Secure Shell [SSH] ve Uzak Masaüstü Protokolü [RDP]) IoT Hub 'ı aracılığıyla yüklenen bir cihaz akışından gönderilmesi sağlayan iki C# uygulaması vardır. Kuruluma genel bakış için bkz. [SSH veya RDP Için yerel proxy uygulama örneği](iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp).

Bu makalede ilk olarak SSH kurulumu (bağlantı noktası 22 kullanılarak) açıklanmakta ve ardından Kurulum 'un RDP için bağlantı noktasının nasıl değiştirileceği açıklanmaktadır. Cihaz akışları uygulama ve protokol belirsiz olduğundan, aynı örnek diğer uygulama trafiği türlerini kapsayacak şekilde değiştirilebilir. Bu değişiklik genellikle iletişim bağlantı noktasını amaçlanan uygulama tarafından kullanılan bir ile değiştirmeyi içerir.

## <a name="prerequisites"></a>Önkoşullar

* Cihaz akışlarının önizlemesi Şu anda yalnızca şu bölgelerde oluşturulan IoT Hub 'lar için desteklenmektedir:

  * Central US
  * EUAP Orta ABD
  * Güneydoğu Asya
  * Kuzey Avrupa

* Bu hızlı başlangıçta çalıştırdığınız iki örnek uygulama C# dilinde yazılmıştır. Geliştirme makinenizde .NET Core SDK 2.1.0 veya üzeri bir sürüm gerekir.

    [.NET Core SDK .net 'ten birden çok platform için](https://dotnet.microsoft.com/download)indirebilirsiniz.

    Aşağıdaki komutu kullanarak geliştirme makinenizde geçerli C# sürümünü doğrulayın:

    ```
    dotnet --version
    ```

* [Azure IoT C# örneklerini indirin](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)ve ZIP arşivini ayıklayın.

* Kullanıcının kimliğini doğrulamak için kullanılan cihazdaki geçerli bir kullanıcı hesabı ve kimlik bilgisi (Windows veya Linux).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="how-it-works"></a>Nasıl çalışır?

Aşağıdaki şekilde, bu örnekteki cihaz yerel ve hizmet yerel proxy uygulamalarının SSH istemcisi ile SSH Daemon işlemlerinde uçtan uca bağlantıyı nasıl etkinleştireceğinizi gösterilmektedir. Burada, arka plan programının cihaz yerel ara sunucu uygulamasıyla aynı cihazda çalıştığını varsayalım.

![Yerel proxy uygulama kurulumu](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.png)

1. Hizmet yerel proxy uygulaması IoT Hub 'ına bağlanır ve hedef cihaza bir cihaz akışı başlatır.

1. Cihaz yerel proxy uygulaması, akış başlatma anlaşmasını tamamlar ve IoT Hub 'ının akış uç noktası aracılığıyla hizmet tarafına uçtan uca bir akış tüneli oluşturur.

1. Cihaz yerel proxy uygulaması, cihazda 22 numaralı bağlantı noktasında dinleme yapan SSH Daemon 'a bağlanır. Bu ayar, "cihaz-yerel proxy uygulaması çalıştırma" bölümünde açıklandığı gibi yapılandırılabilir.

1. Hizmet yerel proxy uygulaması, bir kullanıcıdan belirli bir bağlantı noktasını dinleyerek, bu durumda 2222 numaralı bağlantı noktası olan yeni SSH bağlantıları bekler. Bu ayar, "hizmet-yerel proxy uygulamasını çalıştırma" bölümünde açıklandığı gibi yapılandırılabilir. Kullanıcı SSH istemcisi aracılığıyla bağlanıyorsa, tünel SSH uygulaması trafiğinin SSH istemcisi ile sunucu uygulaması arasında aktarılmasını sağlar.

> [!NOTE]
> Bir cihaz akışı üzerinden gönderilen SSH trafiği, doğrudan hizmet ve cihaz arasında gönderilmek yerine IoT Hub 'ının akış uç noktası aracılığıyla tünel oluşturulur. Daha fazla bilgi için bkz. [IoT Hub cihaz akışlarını kullanmanın avantajları](iot-hub-device-streams-overview.md#benefits).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Cihaz kaydetme

Bir cihazın bağlanabilmesi için IoT hub’ınıza kaydedilmesi gerekir. Bu hızlı başlangıçta, sanal cihazı kaydetmek için Azure Cloud Shell kullanırsınız.

1. Cihaz kimliğini oluşturmak için Cloud Shell ' de aşağıdaki komutu çalıştırın:

   > [!NOTE]
   > * *Youriothubname* yer tutucusunu, IoT Hub 'ınız için seçtiğiniz adla değiştirin.
   > * Kaydolduğunuz aygıtın adı için *mydevice* ' ın gösterildiği gibi kullanılması önerilir. Cihazınız için farklı bir ad seçerseniz bu adı bu makale boyunca kullanın ve uygulamayı çalıştırmadan önce örnek uygulamalarda cihaz adını güncelleştirin.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Yeni kaydettiğiniz cihazın *Cihaz bağlantı dizesini* almak için Cloud Shell ' de aşağıdaki komutları çalıştırın:

   > [!NOTE]
   > *Youriothubname* yer tutucusunu, IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub device-identity connection-string show --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Bu hızlı başlangıçta kullanılmak üzere döndürülen cihaz bağlantı dizesini aklınızda edin. Aşağıdaki örneğe benzer şekilde görünür:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

1. IoT Hub 'ınıza bağlanmak ve bir cihaz akışı oluşturmak için, hizmet tarafı uygulamasını etkinleştirmek üzere IoT Hub 'ınızdaki *hizmet bağlantı dizesine* de ihtiyacınız vardır. Aşağıdaki komut, IoT Hub 'ınız için bu değeri alır:

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

Yerel bir Terminal penceresinde, `device-streams-proxy/device` Sıkıştırılmış proje klasörünüzdeki dizine gidin. Aşağıdaki bilgileri yararlı tutun:

| Bağımsız değişken adı | Bağımsız değişken değeri |
|----------------|-----------------|
| `DeviceConnectionString` | Daha önce oluşturduğunuz cihazın cihaz bağlantı dizesi. |
| `targetServiceHostName` | SSH sunucusunun dinlediği IP adresi. Bu adres, `localhost` cihaz yerel proxy uygulamasının çalıştığı aynı IP ise olur. |
| `targetServicePort` | Uygulama protokolü tarafından kullanılan bağlantı noktası (SSH için varsayılan olarak, bu bağlantı noktası 22 ' dir).  |

Kodu derleyin ve aşağıdaki komutlarla çalıştırın:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 22

# In Windows
dotnet run {DeviceConnectionString} localhost 22
```

### <a name="run-the-service-local-proxy-application"></a>Hizmet yerel proxy uygulamasını çalıştırma

Başka bir yerel Terminal penceresinde, `iot-hub/quickstarts/device-streams-proxy/service` zip 'lenmiş proje klasörünüzde öğesine gidin. Aşağıdaki bilgileri yararlı tutun:

| Parametre adı | Parametre değeri |
|----------------|-----------------|
| `ServiceConnectionString` | IoT Hub hizmet bağlantı dizesi. |
| `MyDevice` | Daha önce oluşturduğunuz cihazın tanımlayıcısı. |
| `localPortNumber` | SSH istemcinizin bağlanacağı yerel bir bağlantı noktası. Bu örnekte 2222 numaralı bağlantı noktasını kullanıyoruz, ancak diğer rastgele sayılar kullanabilirsiniz. |

Kodu derleyin ve aşağıdaki komutlarla çalıştırın:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${ServiceConnectionString} MyDevice 2222

# In Windows
dotnet run {ServiceConnectionString} MyDevice 2222
```

### <a name="run-the-ssh-client"></a>SSH istemcisini çalıştırma

Artık SSH istemci uygulamanızı kullanın ve bağlantı noktası 2222 ' de hizmet yerel ara sunucu uygulamasına bağlanın (doğrudan SSH Daemon yerine).

```
ssh {username}@localhost -p 2222
```

Bu noktada, SSH oturum açma penceresi, kimlik bilgilerinizi girmenizi ister.

Hizmet tarafındaki konsol çıkışı (hizmet yerel proxy uygulaması 2222 numaralı bağlantı noktasını dinler):

![Hizmet-yerel proxy uygulama çıkışı](./media/quickstart-device-streams-proxy-csharp/service-console-output.png)

*IP_address: 22*' de ssh daemon 'a bağlanan cihaz yerel proxy uygulamasında konsol çıktısı:

![Cihaz-yerel proxy uygulama çıkışı](./media/quickstart-device-streams-proxy-csharp/device-console-output.png)

SSH istemci uygulamasının konsol çıktısı. SSH istemcisi, hizmet yerel proxy uygulamasının dinlediği bağlantı noktası 22 ' ye bağlanarak SSH arka plan programı ile iletişim kurar:

![SSH istemci uygulaması çıkışı](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="rdp-to-a-device-via-device-streams"></a>Cihaz akışları aracılığıyla bir cihaza RDP

RDP kurulumu, SSH kurulumuna (yukarıda açıklanmıştır) benzer. Bunun yerine RDP hedef IP 'sini ve bağlantı noktası 3389 ' i kullanın ve RDP istemcisini kullanın (SSH istemcisi yerine).

### <a name="run-the-device-local-proxy-application-rdp"></a>Cihaz yerel proxy uygulaması (RDP) çalıştırma

Yerel bir Terminal penceresinde, `device-streams-proxy/device` Sıkıştırılmış proje klasörünüzdeki dizine gidin. Aşağıdaki bilgileri yararlı tutun:

| Bağımsız değişken adı | Bağımsız değişken değeri |
|----------------|-----------------|
| `DeviceConnectionString` | Daha önce oluşturduğunuz cihazın cihaz bağlantı dizesi. |
| `targetServiceHostName` | RDP sunucusunun çalıştığı ana bilgisayar adı veya IP adresi. Bu adres, `localhost` cihaz yerel proxy uygulamasının çalıştığı aynı IP ise olur. |
| `targetServicePort` | Uygulama protokolü tarafından kullanılan bağlantı noktası (RDP için varsayılan olarak bu bağlantı noktası 3389 ' dir).  |

Kodu derleyin ve aşağıdaki komutlarla çalıştırın:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/device

# Run the application
# In Linux or macOS
dotnet run ${DeviceConnectionString} localhost 3389

# In Windows
dotnet run {DeviceConnectionString} localhost 3389
```

### <a name="run-the-service-local-proxy-application-rdp"></a>Hizmet yerel proxy uygulamasını (RDP) çalıştırma

Başka bir yerel Terminal penceresinde, `device-streams-proxy/service` zip 'lenmiş proje klasörünüzde öğesine gidin. Aşağıdaki bilgileri yararlı tutun:

| Parametre adı | Parametre değeri |
|----------------|-----------------|
| `ServiceConnectionString` | IoT Hub hizmet bağlantı dizesi. |
| `MyDevice` | Daha önce oluşturduğunuz cihazın tanımlayıcısı. |
| `localPortNumber` | SSH istemcinizin bağlanacağı yerel bir bağlantı noktası. Bu örnekte 2222 numaralı bağlantı noktasını kullanıyoruz, ancak bunu diğer rastgele sayılarla değiştirebilirsiniz. |

Kodu derleyin ve aşağıdaki komutlarla çalıştırın:

```
cd ./iot-hub/Quickstarts/device-streams-proxy/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run ${ServiceConnectionString} MyDevice 2222

# In Windows
dotnet run {ServiceConnectionString} MyDevice 2222
```

### <a name="run-rdp-client"></a>RDP istemcisini Çalıştır

Şimdi RDP istemci uygulamanızı kullanın ve bağlantı noktası 2222 ' de hizmet yerel ara sunucu uygulamasına bağlanın (Bu, daha önce seçtiğiniz rastgele bir bağlantı noktasıdır).

![RDP, hizmet yerel ara sunucu uygulamasına bağlanır](./media/quickstart-device-streams-proxy-csharp/rdp-screen-capture.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, IoT Hub 'ı aracılığıyla bir cihaz akışı oluşturmak için bir IoT Hub 'ı ve bir cihaz kaydettiniz, cihaz yerel ve hizmet yerel proxy uygulamaları dağıttınız ve SSH ya da RDP trafiğini tünele sağlamak için proxy uygulamalarını kullandınız. Aynı paradigma, sunucunun cihazda çalıştığı diğer istemci-sunucu protokollerine (örneğin, SSH daemon) uyum sağlayabilir.

Cihaz akışları hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Cihaz akışlarına genel bakış](./iot-hub-device-streams-overview.md)
