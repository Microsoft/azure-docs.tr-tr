---
title: Azure IoT Hub modül kimliği & Module ikizi (portal ve .NET)
description: Portal ve .NET kullanarak modül kimliği oluşturmayı ve modülü güncelleştirmeyi öğrenin.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 289c269100eb6ab672bb8d60562c1fa77d8d091a
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954613"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Portal ve .NET cihazını kullanarak IoT Hub modül kimliğini ve modül ikizini kullanmaya başlama

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Modül kimlikleri ve modül ikizleri](iot-hub-devguide-module-twins.md), Azure IoT Hub cihaz kimliğine ve cihaz ikizine benzer, ancak daha hassas ayrıntı düzeyi sağlar. Azure IoT Hub cihaz kimliği ve cihaz ikizi, arka uç uygulamasının bir cihaz yapılandırmasına ve cihazın koşullarına ilişkin görünürlük sağlamasına olanak sağlarken bir modül kimliği ve modül ikizi, bir cihazın tek tek bileşenleri için bu özellikleri sağlar. İşletim sistemi tabanlı cihazlar veya bellenim cihazları gibi birden çok bileşeni olan uyumlu cihazlarda, modül kimlikleri ve modül TWINS, her bileşen için yalıtılmış yapılandırma ve koşullara izin verir.
>

Bu öğreticide şunları öğreneceksiniz:

* Portalda bir modül kimliği oluşturma.

* Bir .NET cihaz SDK 'sını kullanarak ikizi modülünü cihazınızdan güncelleştirme.

> [!NOTE]
> Hem cihazlarda hem de çözüm arka ucunda çalıştırılacak uygulamalar oluşturmak için kullanabileceğiniz Azure IoT SDK 'Ları hakkında bilgi için bkz. [Azure IoT SDK 'ları](iot-hub-devguide-sdks.md).
>

## <a name="prerequisites"></a>Önkoşullar

* Visual Studio.

* Etkin bir Azure hesabı. Hesabınız yoksa yalnızca birkaç dakika içinde [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturabilirsiniz.

## <a name="create-a-hub"></a>Hub oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-hub"></a>Hub 'a yeni bir cihaz kaydetme

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Portalda bir modül kimliği oluşturma

Tek bir cihaz kimliği içinde en fazla 20 modül kimliği oluşturabilirsiniz. Bir kimlik eklemek için aşağıdaki adımları izleyin:

1. Önceki bölümde oluşturduğunuz cihaz için, ilk modül kimliğinizi oluşturmak için **modül kimliği Ekle** ' yi seçin.

1. *Myfirstmodule*adını girin. Modül kimliğinizi kaydedin.

    ![Modül kimliği Ekle](./media/iot-hub-portal-csharp-module-twin-getstarted/add-module-identity.png)

    Yeni modül kimliğiniz ekranın alt kısmında görünür. Modül kimlik ayrıntılarını görmek için seçin.

    ![Modül kimliği ayrıntılarına bakın](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

**Bağlantı dizesini kaydet-birincil anahtar**. Bunu bir sonraki bölümde kullanarak modülünüzü cihazda ayarlarsınız.

## <a name="update-the-module-twin-using-net-device-sdk"></a>.NET cihaz SDK’sını kullanarak modül ikizini güncelleştirme

IoT Hub’ınızda modül kimliğini başarıyla oluşturdunuz. Simülasyon cihazınızdan bulutla iletişim kurmayı deneyelim. Bir modül kimliği oluşturulduktan sonra, IoT Hub’ında örtük olarak bir modül ikizi oluşturulur. Bu bölümde, modül ikizi tarafından raporlanan özelliklerini güncelleştiren simülasyon cihazınızda bir .NET konsol uygulaması oluşturacaksınız.

### <a name="create-a-visual-studio-project"></a>Visual Studio projesi oluşturma

Modülün ikizi bildirilen özelliklerini güncelleştiren bir uygulama oluşturmak için aşağıdaki adımları izleyin:

1. Visual Studio 'da **Yeni proje oluştur**' u ve ardından **konsol uygulaması (.NET Framework)** öğesini seçin ve **İleri**' yi seçin.

1. **Yeni projenizi yapılandırın**bölümünde, **Proje adı**olarak *Updatemodületwinreportedproperties* yazın. Devam etmek için **Oluştur** ' u seçin.

    ![Visual Studio projeniz olduğunuzu yapılandırma](./media/iot-hub-portal-csharp-module-twin-getstarted/configure-twins-project.png)

### <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>En son Azure IoT Hub .NET cihaz SDK 'sını yükler

Modül kimliği ve modül ikizi genel önizlemede. Yalnızca yayın öncesi cihaz SDK 'Ları IoT Hub kullanılabilir. Yüklemek için şu adımları izleyin:

1. Visual Studio 'da, **NuGet paket yöneticisi** > **Araçlar** ' ı açın > **çözüm için NuGet paketlerini yönetin**.

1. **Araştır**' ı seçin ve ardından **sürümü dahil et**' i seçin. *Microsoft. Azure. Devices. Client*araması yapın. En son sürümü seçin ve yüklemeyi yapın.

    ![Azure IoT Hub .NET Service SDK önizlemesi 'ni yükler](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

    Şimdi tüm modül özelliklerine erişiminiz vardır.

### <a name="get-your-module-connection-string"></a>Modülünüzün bağlantı dizesini alın

Konsol uygulamanız için modül bağlantı dizesine ihtiyacınız vardır. Şu adımları uygulayın:

1. [Azure portalında](https://portal.azure.com/) oturum açın.

1. IoT Hub 'ınıza gidin ve **IoT cihazları**' nı seçin. **Myfirstdevice** ' i açın ve **myfirstmodule** ' un başarıyla oluşturulduğunu görürsünüz.

1. **Modül kimlikleri**altında **myfirstmodule** öğesini seçin. **Modül kimliği ayrıntıları**' nda **bağlantı dizesini (birincil anahtar)** kopyalayın.

    ![Azure portalı modül ayrıntısı](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

### <a name="create-updatemoduletwinreportedproperties-console-app"></a>Updatemodületwinreportedproperties konsol uygulaması oluşturma

Uygulamanızı oluşturmak için aşağıdaki adımları izleyin:

1. Aşağıdaki `using` deyimlerini **Program.cs** dosyasının üst kısmına ekleyin:

  ```csharp
  using Microsoft.Azure.Devices.Client;
  using Microsoft.Azure.Devices.Shared;
  using Newtonsoft.Json;
  ```

2. **Program** sınıfına aşağıdaki alanları ekleyin. Yer tutucu değerini, modül bağlantı dizesiyle değiştirin.

  ```csharp
  private const string ModuleConnectionString = "<Your module connection string>";
  private static ModuleClient Client = null;
  ```

3. Aşağıdaki **OnDesiredPropertyChanged** yöntemini **Program** sınıfına ekleyin:

  ```csharp
  private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
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

4. Son olarak, **Main** yöntemini aşağıdaki kodla değiştirin:

  ```csharp
  static void Main(string[] args)
  {
      Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;
  
      try
      {
          Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
          Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
          Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();
  
          Console.WriteLine("Retrieving twin");
          var twinTask = Client.GetTwinAsync();
          twinTask.Wait();
          var twin = twinTask.Result;
          Console.WriteLine(JsonConvert.SerializeObject(twin));
  
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
      Console.ReadKey();
      Client.CloseAsync().Wait();
  }
  
  private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
  {
      Console.WriteLine($"Status {status} changed: {reason}");
  }
  ```
  
  **F5**kullanarak bu uygulamayı oluşturabilir ve çalıştırabilirsiniz.

Bu kod örneği, AMQP protokolüyle raporlanan özellikleri güncelleştirme ve modül ikizini alma işlemini nasıl yapacağınızı gösterir. Genel önizleme aşamasında, modül ikizi işlemleri için yalnızca AMQP’yi destekleriz.

## <a name="next-steps"></a>Sonraki adımlar

IoT Hub’ı kullanmaya başlamak ve diğer IoT senaryolarını keşfetmek için bkz:

* [.NET Backup ve .NET cihazını kullanarak IoT Hub modül kimliği ve modül ikizi ile çalışmaya başlama](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [IoT Edge kullanmaya başlama](../iot-edge/tutorial-simulate-device-linux.md)
