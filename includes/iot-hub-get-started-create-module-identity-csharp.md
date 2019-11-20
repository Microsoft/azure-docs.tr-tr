---
title: include dosyası
description: include dosyası
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: a5c1ddd085ae65b9920d73f50f993f4646785a69
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883814"
---
## <a name="create-a-module-identity"></a>Modül kimliği oluşturma

Bu bölümde, hub 'ınızdaki kimlik kayıt defterinde bir cihaz kimliği ve bir modül kimliği oluşturan bir .NET konsol uygulaması oluşturacaksınız. Kimlik kayıt defterinde bir girişi yoksa bir cihaz veya modül hub 'a bağlanamaz. Daha fazla bilgi için, [IoT Hub geliştirici kılavuzunun kimlik kayıt defteri bölümüne](../articles/iot-hub/iot-hub-devguide-identity-registry.md)bakın.

Bu konsol uygulamasını çalıştırdığınızda, hem cihaz hem de modül için benzersiz bir kimlik ve anahtar oluşturur. Cihazınız ve modülünüzün, IoT Hub için cihazdan buluta iletileri gönderdiğinde kendilerini tanımlamak üzere bu değerleri kullanın. Kimlikler büyük/küçük harfe duyarlıdır.

1. Visual Studio 'yu açın ve **Yeni proje oluştur**' u seçin.

1. **Yeni proje oluştur**' da **konsol uygulaması ' nı (.NET Framework)** seçin.

1. **Yeni projenizi Yapılandır**' ı açmak için **İleri ' yi** seçin. Projeye *CreateIdentities* ve çözüme *IoTHubGetStarted* adını verin. .NET Framework sürümünün 4.6.1 veya sonraki bir sürüm olduğundan emin olun.

    ![Visual Studio çözümünüz için ad ve çerçeve girin](./media/iot-hub-get-started-create-module-identity-csharp/configure-createidentities-project.png)

1. Visual Studio 'da **Araçlar** > **NuGet Paket Yöneticisi** > **çözüm için NuGet Paketlerini Yönet**' i açın. **Gözat** sekmesini seçin.

1. **Microsoft. Azure. Devices**' i arayın. Seçin ve ardından **Install**' ı seçin.

    ![Azure IoT Hub .NET Service SDK 'sının güncel sürümünü yükler](./media/iot-hub-get-started-create-module-identity-csharp/install-service-sdk.png)

1. Aşağıdaki `using` deyimlerini **Program.cs** dosyasının üst kısmına ekleyin:

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Common.Exceptions;
   ```

1. **Program** sınıfına aşağıdaki alanları ekleyin. Yer tutucu değerini, önceki bölümde hub için oluşturduğunuz IoT Hub bağlantı dizesiyle değiştirin.

   ```csharp
   const string connectionString = "<replace_with_iothub_connection_string>";
   const string deviceID = "myFirstDevice";
   const string moduleID = "myFirstModule";
   ```

1. Aşağıdaki kodu **Main** sınıfına ekleyin.

   ```csharp
   static void Main(string[] args)
   {
       AddDeviceAsync().Wait();
       AddModuleAsync().Wait();
   }
   ```

1. **Program** sınıfına aşağıdaki yöntemleri ekleyin:

    ```csharp
    private static async Task AddDeviceAsync()
    {
       RegistryManager registryManager = 
         RegistryManager.CreateFromConnectionString(connectionString);
       Device device;

       try
       {
           device = await registryManager.AddDeviceAsync(new Device(deviceID));
       }
       catch (DeviceAlreadyExistsException)
        {
            device = await registryManager.GetDeviceAsync(deviceID);
        }

        Console.WriteLine("Generated device key: {0}", 
          device.Authentication.SymmetricKey.PrimaryKey);
    }

    private static async Task AddModuleAsync()
    {
        RegistryManager registryManager = 
          RegistryManager.CreateFromConnectionString(connectionString);
        Module module;

        try
        {
            module = 
              await registryManager.AddModuleAsync(new Module(deviceID, moduleID));
        }
        catch (ModuleAlreadyExistsException)
        {
            module = await registryManager.GetModuleAsync(deviceID, moduleID);
        }

        Console.WriteLine("Generated module key: {0}", module.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    Yöntemi `AddDeviceAsync` , **myfirstdevice**kimliğiyle bir cihaz kimliği oluşturur. Bu cihaz KIMLIĞI kimlik kayıt defterinde zaten varsa, kod yalnızca mevcut cihaz bilgilerini alır. Bu durumda uygulama, bu kimliğin birincil anahtarını görüntüler. Bu anahtarı, sanal cihaz uygulamasında hub 'ınıza bağlanmak için kullanırsınız.

    Bu `AddModuleAsync` Yöntem, myfirstdevice adlı cihaz altında **myfirstmodule** kimliğiylebir modül kimliği oluşturur. Bu modül KIMLIĞI kimlik kayıt defterinde zaten varsa, kod yalnızca var olan modül bilgilerini alır. Bu durumda uygulama, bu kimliğin birincil anahtarını görüntüler. Bu anahtarı, hub 'ınıza bağlanmak için sanal modül uygulamasında kullanırsınız.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Bu uygulamayı çalıştırın ve cihaz anahtarını ve modül anahtarını bir yere göz önünde yapın.

> [!NOTE]
> IoT Hub Identity kayıt defteri, hub 'a güvenli erişim sağlamak için yalnızca cihaz ve modül kimliklerini depolar. Kimlik kayıt defteri, cihaz kimliklerini ve anahtarlarını güvenlik kimlik bilgileri olarak kullanmak için depolar. Kimlik kayıt defterinin her cihaz için depoladığı etkin/devre dışı bayrağını kullanarak, ilgili cihaza erişimi devre dışı bırakabilirsiniz. Uygulamanızın cihaza özgü diğer meta verileri depolaması gerekiyorsa uygulamaya özgü bir depo kullanması gerekir. Modül kimlikleri için etkin/devre dışı bayrağı yoktur. Daha fazla bilgi için bkz. [IoT Hub Geliştirici Kılavuzu](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
