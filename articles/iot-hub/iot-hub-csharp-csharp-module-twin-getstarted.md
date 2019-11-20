---
title: Kullanmaya başlama w/Azure IoT Hub modül kimliği & Module ikizi (.NET)
description: .NET için IoT SDK’larını kullanarak modül kimliği oluşturmayı ve modülü güncelleştirmeyi öğrenin.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: menchi
ms.openlocfilehash: e728d0ef8f52927687d56bd1d4c64f03c53ef401
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73947678"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-net"></a>IoT Hub Module kimliği ve modülü ikizi (.NET) ile çalışmaya başlama

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Modül kimlikleri ve modül ikizleri](iot-hub-devguide-module-twins.md), Azure IoT Hub cihaz kimliğine ve cihaz ikizine benzer, ancak daha hassas ayrıntı düzeyi sağlar. Azure IoT Hub cihaz kimliği ve cihaz ikizi, arka uç uygulamasının bir cihaz yapılandırmasına ve cihazın koşullarına ilişkin görünürlük sağlamasına olanak sağlarken bir modül kimliği ve modül ikizi, bir cihazın tek tek bileşenleri için bu özellikleri sağlar. İşletim sistemi tabanlı cihazlar veya bellenim cihazları gibi birden çok bileşeni olan uyumlu cihazlarda, modül kimlikleri ve modül TWINS, her bileşen için yalıtılmış yapılandırma ve koşullara izin verir.

Bu öğreticinin sonunda iki .NET konsol uygulamanız olacak:

* **Createdentities**. Bu uygulama, cihazınızı ve modül istemcilerinizi bağlamak için bir cihaz kimliği, bir modül kimliği ve ilişkili güvenlik anahtarı oluşturur.

* **Updatemodületwinreportedproperties**. Bu uygulama, güncelleştirilmiş modülün ikizi bildirilen özelliklerini IoT Hub 'ınıza gönderir.

> [!NOTE]
> Cihazlarda çalıştırılacak uygulamaları ve çözüm arka ucu oluşturmak için kullanabileceğiniz Azure IoT SDK 'Ları hakkında daha fazla bilgi için bkz. [Azure IoT SDK 'ları](iot-hub-devguide-sdks.md).

## <a name="prerequisites"></a>Önkoşullar

* Visual Studio.

* Etkin bir Azure hesabı. Hesabınız yoksa yalnızca birkaç dakika içinde [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturabilirsiniz.

## <a name="create-a-hub"></a>Hub oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub bağlantı dizesini al

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>.NET cihaz SDK’sını kullanarak modül ikizini güncelleştirme

Bu bölümde, modül ikizi tarafından raporlanan özelliklerini güncelleştiren simülasyon cihazınızda bir .NET konsol uygulaması oluşturursunuz.

Başlamadan önce modül Bağlantı dizenizi alın. [Azure portalında](https://portal.azure.com/) oturum açın. Hub 'ınıza gidip **IoT cihazları**' nı seçin. **Myfirstdevice**bul. Dosyayı açmak için **Myfirstdevice** ' ı seçin ve ardından dosyayı açmak Için **myfirstmodule** ' ü seçin. **Modül kimliği ayrıntıları**' nda, aşağıdaki yordamda gerektiğinde **bağlantı dizesini (birincil anahtar)** kopyalayın.

   ![Azure portalı modül ayrıntısı](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-identity-detail.png)

1. Visual Studio 'da **dosya** > **Yeni** > **Proje**' yi seçerek çözümünüze yeni bir proje ekleyin. Yeni proje oluştur ' da **konsol uygulaması (.NET Framework)** öğesini seçin ve **İleri**' yi seçin.

1. Projeye *UpdateModuleTwinReportedProperties* adını verin. **Çözüm**Için **çözüme Ekle**' yi seçin. .NET Framework sürümünün 4.6.1 veya sonraki bir sürüm olduğundan emin olun.

    ![Visual Studio projesi oluşturma](./media/iot-hub-csharp-csharp-module-twin-getstarted/configure-update-twins-csharp1.png)

1. Projenizi oluşturmak için **Oluştur** ' u seçin.

1. Visual Studio 'da, **NuGet paket yöneticisi** > **Araçlar** ' ı açın > **çözüm için NuGet paketlerini yönetin**. **Gözat** sekmesini seçin.

1. Arama yapın ve **Microsoft. Azure. Devices. Client**' ı seçin ve ardından **Install**' u seçin.

    ![Azure IoT Hub .NET Service SDK 'sının güncel sürümünü yükler](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-client-sdk.png)

1. Aşağıdaki `using` deyimlerini **Program.cs** dosyasının üst kısmına ekleyin:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

1. **Program** sınıfına aşağıdaki alanları ekleyin. Yer tutucu değerini, modül bağlantı dizesiyle değiştirin.

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

1. Aşağıdaki **OnDesiredPropertyChanged** yöntemini **Program** sınıfına ekleyin:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
        {
            Console.WriteLine("desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
            Console.WriteLine("Sending current time as reported property");
            TwinCollection reportedProperties = new TwinCollection
            {
                ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
            };

            await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
        }
    ```

1. **Main** yöntemine aşağıdaki satırları ekleyin:

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = 
          Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = 
              ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin.Properties)); 

            Console.WriteLine("Sending app start time as reported property");
            TwinCollection reportedProperties = new TwinCollection();
            reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

            Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (AggregateException ex)
        {
            Console.WriteLine("Error in sample: {0}", ex);
        }

        Console.WriteLine("Waiting for Events.  Press enter to exit...");
        Console.ReadLine();
        Client.CloseAsync().Wait();
    }
    ```

    Bu kod örneği, AMQP protokolüyle raporlanan özellikleri güncelleştirme ve modül ikizini alma işlemini nasıl yapacağınızı gösterir. Genel önizleme aşamasında, modül ikizi işlemleri için yalnızca AMQP’yi destekleriz.

1. İsteğe bağlı olarak, bu deyimleri modülünden IoT Hub bir olay göndermek için **Main** yöntemine ekleyebilirsiniz. Bu satırları `try catch` bloğunun altına yerleştirin.

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>Uygulamaları çalıştırma

Artık uygulamaları çalıştırabilirsiniz.

1. Visual Studio 'da **Çözüm Gezgini**' de çözümünüze sağ tıklayın ve ardından **Başlangıç projelerini ayarla**' yı seçin.

1. **Ortak özellikler**altında **Başlangıç projesi** ' ni seçin.

1. **Birden çok başlangıç projesi**seçin ve ardından uygulamalar için eylem olarak **Başlat** ' ı ve değişikliklerinizi kabul etmek için **Tamam** ' ı seçin.

1. Uygulamaları başlatmak için **F5** tuşuna basın.

## <a name="next-steps"></a>Sonraki adımlar

IoT Hub’ı kullanmaya başlamak ve diğer IoT senaryolarını keşfetmek için bkz:

* [Cihaz yönetimini kullanmaya başlama](iot-hub-node-node-device-management-get-started.md)

* [IoT Edge kullanmaya başlama](../iot-edge/tutorial-simulate-device-linux.md)
