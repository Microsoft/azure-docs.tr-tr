---
title: Azure IoT Hub cihaz akışlarındaki C# cihaz uygulamasıyla iletişim kurma
description: Bu hızlı başlangıçta, IoT Hub aracılığıyla kurulu bir C# cihaz akışı aracılığıyla iletişim kuran iki örnek uygulama çalıştırırsınız.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: 14139d56a9a733a284d0ec49cabcb3ff7ce67fce
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084288"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>Hızlı başlangıç: IoT Hub cihaz akışları C# aracılığıyla bir cihaz uygulamasıyla iletişim kurma (Önizleme)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT Hub Şu anda cihaz akışlarını [Önizleme özelliği](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)olarak desteklemektedir.

[Cihaz akışları IoT Hub](./iot-hub-device-streams-overview.md) hizmet ve cihaz uygulamalarının güvenli ve güvenlik duvarı kolay bir şekilde iletişim kurmasına olanak tanır. Bu hızlı başlangıçta, C# verileri geri göndermek için cihaz akışlarından faydalanan iki uygulama (Echo) vardır.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

* Cihaz akışlarının önizlemesi Şu anda yalnızca şu bölgelerde oluşturulan IoT Hub 'lar için desteklenmektedir:
  * Orta ABD
  * EUAP Orta ABD
  * Kuzey Avrupa
  * Güneydoğu Asya

* Bu hızlı başlangıçta çalıştırdığınız iki örnek uygulama içinde C#yazılmıştır. Geliştirme makinenizde .NET Core SDK 2.1.0 veya üzeri bir sürüm gerekir.
  * [.Net 'ten birden çok platform için .NET Core SDK](https://www.microsoft.com/net/download/all)indirin.
  * Aşağıdaki komutu kullanarak geliştirme makinenizde C# geçerli sürümünü doğrulayın:

   ```
   dotnet --version
   ```

* Aşağıdaki komutu çalıştırarak Azure CLı için Azure IoT uzantısını Cloud Shell örneğine ekleyin. IOT uzantısı, Azure CLı için IoT Hub, IoT Edge ve IoT cihazı sağlama hizmeti 'ne (DPS) özgü komutlar ekler.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* [Örnek C# PROJEYI indirin](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) ve ZIP arşivini ayıklayın. Hem cihaz tarafında hem de hizmet tarafında ihtiyacınız vardır.

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>Cihaz kaydetme

Bir cihazın bağlanabilmesi için IoT hub’ınıza kaydedilmesi gerekir. Bu bölümde, sanal bir cihazı kaydetmek için Azure Cloud Shell kullanırsınız.

1. Cihaz kimliğini oluşturmak için Cloud Shell ' de aşağıdaki komutu çalıştırın:

   > [!NOTE]
   > * *Youriothubname* yer tutucusunu, IoT Hub 'ınız için seçtiğiniz adla değiştirin.
   > * Kaydolduğunuz aygıtın adı için *mydevice* ' ın gösterildiği gibi kullanılması önerilir. Cihazınız için farklı bir ad seçerseniz bu adı bu makale boyunca kullanın ve uygulamayı çalıştırmadan önce örnek uygulamalarda cihaz adını güncelleştirin.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. Yeni kaydettiğiniz cihazın *Cihaz bağlantı dizesini* almak için Cloud Shell ' de aşağıdaki komutu çalıştırın:

   > [!NOTE]
   > *Youriothubname* yer tutucusunu, IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    Bu hızlı başlangıçta kullanılmak üzere döndürülen cihaz bağlantı dizesini aklınızda edin. Aşağıdaki örneğe benzer şekilde görünür:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

3. Ayrıca, hizmet tarafı uygulamasının IoT Hub 'ınıza bağlanmasını ve bir cihaz akışını kurmasını sağlamak için IoT Hub 'ınızdaki *hizmet bağlantı dizesine* ihtiyacınız vardır. Aşağıdaki komut, IoT Hub 'ınız için bu değeri alır:

   > [!NOTE]
   > *Youriothubname* yer tutucusunu, IoT Hub 'ınız için seçtiğiniz adla değiştirin.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name {YourIoTHubName} --output table
    ```

    Daha sonra bu hızlı başlangıçta kullanılmak üzere döndürülen hizmet bağlantı dizesine göz önünde edin. Aşağıdaki örneğe benzer şekilde görünür:

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>Cihaz akışları aracılığıyla cihaz ve hizmet arasında iletişim kurma

Bu bölümde hem cihaz tarafı uygulamasını hem de hizmet tarafı uygulamasını çalıştırırsınız ve iki arasında iletişim kurabilirsiniz.

### <a name="run-the-service-side-application"></a>Hizmet tarafı uygulamasını çalıştırma

Yerel bir Terminal penceresinde, sıkıştırılmış proje klasörünüzdeki `iot-hub/Quickstarts/device-streams-echo/service` dizinine gidin. Aşağıdaki bilgileri yararlı tutun:

| Parametre adı | Parametre değeri |
|----------------|-----------------|
| `ServiceConnectionString` | IoT Hub 'ınızın hizmet bağlantı dizesi. |
| `MyDevice` | Daha önce oluşturduğunuz cihazın tanımlayıcısı. |

Kodu derleyin ve aşağıdaki komutlarla çalıştırın:

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{ServiceConnectionString}" "MyDevice"

# In Windows
dotnet run {ServiceConnectionString} MyDevice
```
Uygulama, cihaz uygulamasının kullanılabilir hale gelmesini bekler.

> [!NOTE]
> Cihaz tarafı uygulama zaman yanıt vermezse zaman aşımı oluşur.

### <a name="run-the-device-side-application"></a>Cihaz tarafı uygulamayı çalıştırma

Başka bir yerel Terminal penceresinde, sıkıştırdönüştürülmemiş proje klasörünüzdeki `iot-hub/Quickstarts/device-streams-echo/device` dizinine gidin. Aşağıdaki bilgileri yararlı tutun:

| Parametre adı | Parametre değeri |
|----------------|-----------------|
| `DeviceConnectionString` | IoT Hub cihaz bağlantı dizesi. |

Kodu derleyin ve aşağıdaki komutlarla çalıştırın:

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux or macOS
dotnet run "{DeviceConnectionString}"

# In Windows
dotnet run {DeviceConnectionString}
```

Son adımın sonunda, hizmet tarafı uygulaması cihazınıza bir akış başlatır. Akış kurulduktan sonra uygulama, hizmete akış üzerinden bir dize arabelleği gönderir. Bu örnekte, hizmet tarafı uygulaması, iki uygulama arasında başarılı bir çift yönlü iletişimi gösteren cihaza aynı verileri geri doğru bir şekilde yankılar.

Cihaz tarafında konsol çıkışı:

![Cihaz tarafında konsol çıkışı](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

Hizmet tarafında konsol çıkışı:

![Hizmet tarafında konsol çıkışı](./media/quickstart-device-streams-echo-csharp/service-console-output.png)

Akış üzerinden gönderilmekte olan trafik doğrudan gönderilmek yerine IoT Hub 'ı aracılığıyla tünededir. Belirtilen avantajlar [cihaz akışlarının avantajları](./iot-hub-device-streams-overview.md#benefits)bölümünde ayrıntılıdır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir IoT Hub 'ı ayarlarsınız, cihaz kaydettiniz, cihaz ve hizmet taraflarındaki uygulamalar arasında C# bir cihaz akışı kurdu ve verileri uygulamalar arasında ileri ve geri göndermek için akışı kullandınız.

Cihaz akışları hakkında daha fazla bilgi edinmek için bkz.:

> [!div class="nextstepaction"]
> [Cihaz akışlarına genel bakış](./iot-hub-device-streams-overview.md)
