---
title: İşleri Azure IoT Hub zamanlayın (.NET/.NET) | Microsoft Docs
description: Birden çok cihazda doğrudan yöntem çağırmak için bir Azure IoT Hub işi zamanlama. İşi çalıştırmak için sanal cihaz uygulamalarını ve bir hizmet uygulamasını uygulamak üzere .NET için Azure IoT cihaz SDK 'sını kullanın.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: db4824203f63fa2fe0d4256a475d18a501b17e0e
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147729"
---
# <a name="schedule-and-broadcast-jobs-net"></a>İşleri zamanlama ve yayınlama (.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Milyonlarca cihazı güncelleştiren işleri zamanlamak ve izlemek için Azure IoT Hub kullanın. İşleri şu şekilde kullan:

* İstenen özellikleri güncelleştirme

* Etiketleri Güncelleştir

* Doğrudan metotları çağır

Bir iş bu eylemlerden birini sarmalar ve yürütmeyi bir cihaz ikizi sorgusu tarafından tanımlanan bir dizi cihaza karşı izler. Örneğin, bir arka uç uygulaması, cihazları yeniden çalıştıran 10.000 cihazda doğrudan bir yöntemi çağırmak için bir iş kullanabilir. Bir cihaz ikizi sorgusu olan cihaz kümesini belirtirsiniz ve işi gelecekteki bir zamanda çalışacak şekilde zamanlayabilirsiniz. Her bir cihazın yeniden başlatma doğrudan metodunu alıp yürütmesi sırasında iş ilerleme durumunu izler.

Bu yeteneklerin her biri hakkında daha fazla bilgi edinmek için bkz.:

* Cihaz ikizi ve özellikleri: [Cihaz](iot-hub-csharp-csharp-twin-getstarted.md) ikizlerini ve [öğreticisini kullanmaya başlayın: Cihaz ikizi özelliklerini kullanma](tutorial-device-twins.md)

* Doğrudan Yöntemler: [IoT Hub Geliştirici Kılavuzu-doğrudan Yöntemler](iot-hub-devguide-direct-methods.md) ve [öğretici: Doğrudan Yöntemler kullanma](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Bu öğretici şunların nasıl yapıldığını gösterir:

* Arka uç uygulaması tarafından çağrılabilen **Lockkapısı**adlı doğrudan bir yöntemi uygulayan bir cihaz uygulaması oluşturun.

* Birden çok cihazda **Lockkapısı** doğrudan yöntemini çağırmak için bir iş oluşturan bir arka uç uygulaması oluşturun. Başka bir iş, istenen özellik güncelleştirmelerini birden çok cihaza gönderir.

Bu öğreticinin sonunda iki .NET (C#) konsol uygulamanız vardır:

* **SimulateDeviceMethods**. Bu uygulama, IoT Hub 'ınıza bağlanır ve **Lockkapısı** doğrudan metodunu uygular.

* **Schedulejob**. Bu uygulama, **Lockkapısı** doğrudan yöntemini çağırmak ve cihazı ikizi istenen özellikleri birden çok cihazda güncelleştirmek için işleri kullanır.

## <a name="prerequisites"></a>Önkoşullar

* Visual Studio.

* Etkin bir Azure hesabı. Hesabınız yoksa yalnızca birkaç dakika içinde [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturabilirsiniz.

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub 'a yeni bir cihaz kaydetme

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Sanal cihaz uygulaması oluşturma

Bu bölümde, çözüm arka ucu tarafından çağrılan doğrudan bir yönteme yanıt veren bir .NET konsol uygulaması oluşturacaksınız.

1. Visual Studio 'da **Yeni proje oluştur**' u seçin ve ardından **konsol uygulaması (.NET Framework)** proje şablonunu seçin. Devam etmek için **İleri**’yi seçin.

1. **Yeni projenizi yapılandırın**bölümünde projeyi *SimulateDeviceMethods*olarak adlandırın ve ardından **Oluştur**' u seçin.

    ![SimulateDeviceMethods projenizi yapılandırma](./media/iot-hub-csharp-csharp-schedule-jobs/configure-device-app.png)

1. Çözüm Gezgini, **SimulateDeviceMethods** projesine sağ tıklayın ve ardından **NuGet Paketlerini Yönet**' i seçin.

1. **NuGet Paket Yöneticisi**'Nde, **Araştır** ' ı seçin ve arama yapın ve **Microsoft. Azure. Devices. Client**' ı seçin. **Yükle**’yi seçin.

    ![NuGet Paket Yöneticisi penceresi Istemci uygulaması](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

    Bu adım, [Azure IoT cihaz SDK 'sı](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet paketi ve bağımlılıklarını indirir, yükler ve buna bir başvuru ekler.

1. Aşağıdaki `using` deyimlerini **Program.cs** dosyasının üst kısmına ekleyin:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. **Program** sınıfına aşağıdaki alanları ekleyin. Yer tutucu değerini, önceki bölümde not ettiğiniz cihaz bağlantı dizesiyle değiştirin:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

1. Cihaza doğrudan yöntemi uygulamak için aşağıdaki kodu ekleyin:

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);

        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```

1. Cihaza cihaz TWINS dinleyicisini uygulamak için aşağıdaki yöntemi ekleyin:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. Son olarak, aşağıdaki kodu **Main** yöntemine ekleyerek IoT Hub 'ınız bağlantısını açın ve Yöntem dinleyicisini başlatın:

    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
          TransportType.Mqtt);

        Client.SetMethodHandlerAsync("LockDoor", LockDoor, null);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null);

        Console.WriteLine("Waiting for direct method call and device twin update\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        Client.SetMethodHandlerAsync("LockDoor", null, null);
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. Çalışmanızı kaydedin ve çözümünüzü derleyin.

> [!NOTE]
> Bu öğretici, şeyleri basit tutmak için yeniden deneme ilkeleri uygulamaz. Üretim kodunda, [geçici hata işleme](/azure/architecture/best-practices/transient-faults)bölümünde önerildiği gibi yeniden deneme ilkelerini (bağlantı yeniden denemesi gibi) uygulamalısınız.
>

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub bağlantı dizesini al

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Doğrudan yöntem çağırmak ve cihaz ikizi güncelleştirmelerini göndermek için işleri zamanlama

Bu bölümde, **Lockkapısı** doğrudan yöntemini çağırmak ve istenen özellik güncelleştirmelerini C#birden çok cihaza göndermek için işleri kullanan bir .NET konsol uygulaması (kullanarak) oluşturursunuz.

1. Visual Studio'da **dosya** > **yeni** > **proje**. **Yeni proje oluştur**' da **konsol uygulaması (.NET Framework)** öğesini seçin ve ardından **İleri**' yi seçin.

1. **Yeni projenizi yapılandırın**bölümünde proje *schedulejob*' u adlandırın. **Çözüm**Için **çözüme Ekle**' yi seçin ve ardından **Oluştur**' u seçin.

    ![Zamanlama Işi projenizi adlandırın ve yapılandırın](./media/iot-hub-csharp-csharp-schedule-jobs/config-schedule-job-app.png)

1. Çözüm Gezgini, **Schedulejob** projesine sağ tıklayın ve ardından **NuGet Paketlerini Yönet**' i seçin.

1. **NuGet Paket Yöneticisi**' nde, **Araştır**' ı seçin, **Microsoft. Azure. Devices**' i arayın ve ardından **Install**' ı seçin.

   Bu adım, [Azure IoT hizmeti SDK 'sı](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet paketi ve bağımlılıklarını indirir, yükler ve buna bir başvuru ekler.

1. Aşağıdaki `using` deyimlerini **Program.cs** dosyasının üst kısmına ekleyin:

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Varsayılan deyimlerde `using` henüz yoksa, aşağıdaki deyimi ekleyin.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. **Program** sınıfına aşağıdaki alanları ekleyin. Yer tutucuları, [IoT Hub bağlantı dizesini](#get-the-iot-hub-connection-string) ve cihazınızın adını almak için, daha önce kopyaladığınız IoT Hub bağlantı dizesiyle değiştirin.

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

1. **Program** sınıfına aşağıdaki yöntemi ekleyin:

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && 
          (result.Status != JobStatus.Failed));
    }
    ```

1. **Program** sınıfına aşağıdaki yöntemi ekleyin:

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = 
          new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), 
          TimeSpan.FromSeconds(5));

        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

1. **Program** sınıfına başka bir yöntem ekleyin:

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        Twin twin = new Twin(deviceId);
        twin.Tags = new TwinCollection();
        twin.Tags["Building"] = "43";
        twin.Tags["Floor"] = "3";
        twin.ETag = "*";

        twin.Properties.Desired["LocationUpdate"] = DateTime.UtcNow;

        JobResponse createJobResponse = jobClient.ScheduleTwinUpdateAsync(
            jobId,
            $"DeviceId IN ['{deviceId}']", 
            twin, 
            DateTime.UtcNow, 
            (long)TimeSpan.FromMinutes(2).TotalSeconds).Result;

        Console.WriteLine("Started Twin Update Job");
    }
    ```

    > [!NOTE]
    > Sorgu sözdizimi hakkında daha fazla bilgi için [IoT Hub sorgu dili](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language)' ne bakın.
    >

1. Son olarak, **Main** yöntemine aşağıdaki satırları ekleyin:

    ```csharp
    Console.WriteLine("Press ENTER to start running jobs.");
    Console.ReadLine();

    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

1. Çalışmanızı kaydedin ve çözümünüzü derleyin.

## <a name="run-the-apps"></a>Uygulamaları çalıştırma

Şimdi uygulamaları çalıştırmaya hazırsınız.

1. Visual Studio Çözüm Gezgini çözümünüze sağ tıklayın ve ardından **Başlangıç projelerini ayarla**' yı seçin.

1. **Ortak özellikler** > **Başlangıç projesi**' ni seçin ve ardından **birden çok başlangıç**projesi ' ni seçin.

1. Listenin en `SimulateDeviceMethods` üstünde `ScheduleJob`ve sonrasında olduğundan emin olun. Her iki eylemini **Başlangıç** olarak ayarlayın ve **Tamam**' ı seçin.

1. **Başlat** ' a tıklayarak veya **hata ayıklama** menüsüne gidip **hata ayıklamayı Başlat**' a tıklayarak projeleri çalıştırın.

   Çıktıyı hem cihazdan hem de arka uç uygulamalardan görürsünüz.

    ![İşleri zamanlamak için uygulamaları çalıştırma](./media/iot-hub-csharp-csharp-schedule-jobs/schedule-jobs-console-results.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir cihaza doğrudan yöntem zamanlamak ve Device ikizi 'in özelliklerinin güncelleştirilmesi için bir iş kullandınız.

* AIR üretici yazılımı güncelleştirmesi üzerinden uzak gibi IoT Hub ve cihaz yönetim desenleriyle çalışmaya devam etmek için öğreticiyi okuyun [: Üretici yazılımı güncelleştirmesi](tutorial-firmware-update.md)nasıl yapılır?

* Azure IoT Edge ile uç cihazlara AI dağıtma hakkında bilgi edinmek için bkz. [IoT Edge kullanmaya](../iot-edge/tutorial-simulate-device-linux.md)başlama.
