---
title: Azure IoT Hub cihaz yönetimini kullanmaya başlama (.NET/.NET) | Microsoft Docs
description: Uzak cihaz yeniden başlatma işlemini başlatmak için Azure IoT Hub cihaz yönetimi 'ni kullanma. Doğrudan yöntemini çağıran bir hizmet uygulaması uygulamak üzere .NET için Azure IoT hizmeti SDK 'sını içeren bir sanal cihaz uygulaması uygulamak üzere .NET için Azure IoT cihaz SDK 'sını kullanın.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 0ab714efc3e9eb0de9d6753854031110e09fe06b
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147845"
---
# <a name="get-started-with-device-management-net"></a>Cihaz yönetimini kullanmaya başlama (.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Bu öğretici şunların nasıl yapıldığını gösterir:

* IoT Hub 'ınızda bir IoT Hub 'ı oluşturmak ve bir cihaz kimliği oluşturmak için Azure portal kullanın.

* Bu cihazı yeniden yükleyen doğrudan bir yöntem içeren bir sanal cihaz uygulaması oluşturun. Doğrudan Yöntemler buluttan çağrılır.

* IoT Hub 'ınız aracılığıyla sanal cihaz uygulamasındaki önyükleme doğrudan yöntemini çağıran bir .NET konsol uygulaması oluşturun.

Bu öğreticinin sonunda iki .NET konsol uygulamanız olacak:

* **SimulateManagedDevice**. Bu uygulama daha önce oluşturulan cihaz kimliğiyle IoT Hub 'ınıza bağlanır, bir yeniden başlatma doğrudan yöntemi alır, fiziksel yeniden başlatmanın benzetimini yapar ve son yeniden başlatmanın zamanını raporlar.

* **Triggerreboot**. Bu uygulama, sanal cihaz uygulamasında doğrudan bir yöntem çağırır, yanıtı görüntüler ve güncelleştirilmiş bildirilen özellikleri görüntüler.

## <a name="prerequisites"></a>Önkoşullar

* Visual Studio.

* Etkin bir Azure hesabı. Hesabınız yoksa yalnızca birkaç dakika içinde [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturabilirsiniz.

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub 'a yeni bir cihaz kaydetme

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub bağlantı dizesini al

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Doğrudan bir yöntem kullanarak cihazda Uzaktan yeniden başlatma tetikleyin

Bu bölümde, bir doğrudan yöntem kullanan bir cihazda Uzaktan yeniden başlatma C#Başlatan bir .NET konsol uygulaması oluşturacaksınız. Uygulama, bu cihazın son yeniden başlatma zamanını saptamak için Device ikizi sorgularını kullanır.

1. Visual Studio 'da **Yeni proje oluştur**' u seçin.

1. **Yeni proje oluştur**' da **konsol uygulaması (.NET Framework)** proje şablonunu bulup seçin ve ardından **İleri**' yi seçin.

1. **Yeni projenizi yapılandırın**bölümünde, projeyi *triggerreboot*olarak adlandırın ve .NET Framework Version 4.5.1 veya üstünü seçin. **Oluştur**’u seçin.

    ![Yeni Visual C# Windows Klasik Masaüstü projesi](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-configure.png)

1. **Çözüm Gezgini**, **triggerreboot** projesine sağ tıklayın ve ardından **NuGet Paketlerini Yönet**' i seçin.

1. **Araştır**' ı seçin, **Microsoft. Azure. Devices**' i arayıp seçin. **Microsoft. Azure. Devices** paketini yüklemek için **yüklemeyi** seçin.

    ![NuGet Paket Yöneticisi penceresi](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-nuget-devices.png)

   Bu adım, [Azure IoT hizmeti SDK 'sı](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet paketi ve bağımlılıklarını indirir, yükler ve buna bir başvuru ekler.

1. Aşağıdaki `using` deyimlerini **Program.cs** dosyasının üst kısmına ekleyin:

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```

1. **Program** sınıfına aşağıdaki alanları ekleyin. Yer tutucu `{iot hub connection string}` değerini, [IoT Hub bağlantı dizesini al](#get-the-iot-hub-connection-string)içinde daha önce kopyaladığınız IoT Hub bağlantı dizesiyle değiştirin.

   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

1. **Program** sınıfına aşağıdaki yöntemi ekleyin.  Bu kod, yeniden başlatılan cihaz için cihaz ikizi alır ve bildirilen özellikleri verir.

   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```

1. **Program** sınıfına aşağıdaki yöntemi ekleyin.  Bu kod, doğrudan bir yöntemi kullanarak cihazda yeniden başlatmayı başlatır.

   ```csharp
   public static async Task StartReboot()
   {
       client = ServiceClient.CreateFromConnectionString(connString);
       CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
       method.ResponseTimeout = TimeSpan.FromSeconds(30);

       CloudToDeviceMethodResult result = await 
         client.InvokeDeviceMethodAsync(targetDevice, method);

       Console.WriteLine("Invoked firmware update on device.");
   }
   ```

1. Son olarak, **Main** yöntemine aşağıdaki satırları ekleyin:

   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

1. Yapı yapı**çözümünü**seçin. > 

> [!NOTE]
> Bu öğreticide, cihazın bildirilen özellikleri için yalnızca tek bir sorgu gerçekleştirilir. Üretim kodunda, bildirilen özelliklerde yapılan değişiklikleri algılamayı yoklamayı öneririz.

## <a name="create-a-simulated-device-app"></a>Sanal cihaz uygulaması oluşturma

Bu bölümde şunları yapacaksınız:

* Bulut tarafından çağrılan doğrudan bir yönteme yanıt veren bir .NET konsol uygulaması oluşturun.

* Sanal cihaz yeniden başlatması tetikleyin.

* Cihaz ikizi sorguları cihazları ve en son yeniden başlatıldığı zaman bunları belirlemek için, bildirilen özellikleri kullanın.

Sanal cihaz uygulamasını oluşturmak için aşağıdaki adımları izleyin:

1. Visual Studio 'da, zaten oluşturduğunuz triggerreboot çözümünde **Dosya** > **Yeni** > **Proje**' yi seçin. **Yeni proje oluştur**' da **konsol uygulaması (.NET Framework)** proje şablonunu bulup seçin ve ardından **İleri**' yi seçin.

1. **Yeni projenizi yapılandırın**bölümünde, projeyi *SimulateManagedDevice*olarak adlandırın ve **çözüm**için **çözüme Ekle**' yi seçin. **Oluştur**’u seçin.

    ![Projeyi adlandırın ve çözüme ekleyin](./media/iot-hub-csharp-csharp-device-management-get-started/configure-device-app.png)

1. Çözüm Gezgini, yeni **SimulateManagedDevice** projesine sağ tıklayın ve ardından **NuGet Paketlerini Yönet**' i seçin.

1. **Araştır**' ı seçin, ardından **Microsoft. Azure. Devices. Client**öğesini arayıp seçin. **Yükle**’yi seçin.

    ![NuGet Paket Yöneticisi penceresi Istemci uygulaması](./media/iot-hub-csharp-csharp-device-management-get-started/create-device-nuget-devices-client.png)

   Bu adım, [Azure IoT cihaz SDK 'sı](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet paketi ve bağımlılıklarını indirir, yükler ve buna bir başvuru ekler.

1. Aşağıdaki `using` deyimlerini **Program.cs** dosyasının üst kısmına ekleyin:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

1. **Program** sınıfına aşağıdaki alanları ekleyin. Yer tutucu `{device connection string}` değerini, [IoT Hub 'ına yeni bir cihaz kaydetmek için](#register-a-new-device-in-the-iot-hub)önceden not ettiğiniz cihaz bağlantı dizesiyle değiştirin.

    ```csharp
    static string DeviceConnectionString = "{device connection string}";
    static DeviceClient Client = null;
    ```

1. Cihaza doğrudan yöntemini uygulamak için aşağıdakileri ekleyin:

   ```csharp
   static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
   {
       // In a production device, you would trigger a reboot 
       //   scheduled to start after this method returns.
       // For this sample, we simulate the reboot by writing to the console
       //   and updating the reported properties.
       try
       {
           Console.WriteLine("Rebooting!");

           // Update device twin with reboot time. 
           TwinCollection reportedProperties, reboot, lastReboot;
           lastReboot = new TwinCollection();
           reboot = new TwinCollection();
           reportedProperties = new TwinCollection();
           lastReboot["lastReboot"] = DateTime.Now;
           reboot["reboot"] = lastReboot;
           reportedProperties["iothubDM"] = reboot;
           Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
       }
       catch (Exception ex)
       {
           Console.WriteLine();
           Console.WriteLine("Error in sample: {0}", ex.Message);
       }

       string result = "'Reboot started.'";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

1. Son olarak, aşağıdaki kodu **Main** yöntemine ekleyerek IoT Hub 'ınız bağlantısını açın ve Yöntem dinleyicisini başlatın:

   ```csharp
   try
   {
       Console.WriteLine("Connecting to hub");
       Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
         TransportType.Mqtt);

       // setup callback for "reboot" method
       Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
       Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
       Console.ReadLine();

       Console.WriteLine("Exiting...");

       // as a good practice, remove the "reboot" handler
       Client.SetMethodHandlerAsync("reboot", null, null).Wait();
       Client.CloseAsync().Wait();
   }
   catch (Exception ex)
   {
       Console.WriteLine();
       Console.WriteLine("Error in sample: {0}", ex.Message);
   }
   ```

1. Çözüm Gezgini, çözümünüze sağ tıklayın ve ardından **Başlangıç projelerini ayarla**' yı seçin.

1. **Ortak özellikler** > **Başlangıç projesi**için **tek başlangıç projesi**' ni seçin ve ardından **SimulateManagedDevice** projesini seçin. Değişikliklerinizi kaydetmek için **Tamam**’ı seçin.

1. Yapı yapı**çözümünü**seçin. > 

> [!NOTE]
> Sade ve basit bir anlatım gözetildiği için bu öğretici herhangi bir yeniden deneme ilkesi uygulamaz. Üretim kodunda, [geçici hata işleme](/azure/architecture/best-practices/transient-faults)bölümünde önerildiği gibi yeniden deneme ilkelerini (üstel geri alma gibi) uygulamanız gerekir.

## <a name="run-the-apps"></a>Uygulamaları çalıştırma

Şimdi uygulamaları çalıştırmaya hazırsınız.

1. .NET cihaz uygulaması **SimulateManagedDevice**çalıştırmak için, Çözüm Gezgini ' de, **SimulateManagedDevice** projesine sağ tıklayın, **Hata Ayıkla**' yı seçin ve ardından **Yeni örnek Başlat**' ı seçin. Uygulama, IoT Hub 'ınızdaki Yöntem çağrılarını dinlemeye başlamalıdır.

1. Cihaz bağlandıktan ve Yöntem etkinleştirmeleri için beklerken, **Triggerreboot** projesine sağ tıklayın, **Hata Ayıkla**' yı seçin ve ardından **Yeni örnek Başlat**' ı seçin.

   "Yeniden başlatılıyor!" öğesini görmeniz gerekir **SimulatedManagedDevice** konsolunda yazılır ve cihazın, **triggerreboot** konsoluna yazılan son yeniden başlatma zamanını içeren bildirilen özellikleri.

    ![Hizmet ve cihaz uygulaması çalıştırma](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
