---
title: Azure IoT Hub cihaz TWINS (.NET/.NET) ile çalışmaya başlama | Microsoft Docs
description: Azure IoT Hub cihaz ikimlerini kullanarak etiketler ekleyin ve ardından bir IoT Hub sorgusu kullanın. .NET için Azure IoT cihaz SDK 'sını kullanarak sanal cihaz uygulamasını ve Etiketler ekleyen ve IoT Hub sorguyu çalıştıran bir hizmet uygulaması uygulamak üzere .NET için Azure IoT hizmeti SDK 'sını kullanın.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.openlocfilehash: c07b110f0d4c31713ab432b5b5e337f3b69dfc55
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147727"
---
# <a name="get-started-with-device-twins-net"></a>Cihaz ikizlerini kullanmaya başlama (.NET)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Bu öğreticide, şu .NET konsol uygulamalarını oluşturursunuz:

* **Createdeviceıdentity**. Bu uygulama, sanal cihaz uygulamanızı bağlamak için bir cihaz kimliği ve ilişkili güvenlik anahtarı oluşturur.

* **Addtagsandquery**. Bu arka uç uygulaması Etiketler ve sorgular cihaz TWINS 'i ekler.

* **Reportconnectivity**. Bu cihaz uygulaması, daha önce oluşturulan cihaz kimliğiyle IoT Hub 'ınıza bağlanan bir cihazın benzetimini yapar ve bağlantı koşulunu raporlar.

> [!NOTE]
> [Azure IoT SDK 'ları](iot-hub-devguide-sdks.md) makalesi, hem cihaz hem de arka uç uygulamaları oluşturmak Için kullanabileceğiniz Azure IoT SDK 'ları hakkında bilgi sağlar.
>

## <a name="prerequisites"></a>Önkoşullar

* Visual Studio.

* Etkin bir Azure hesabı. Hesabınız yoksa yalnızca birkaç dakika içinde [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturabilirsiniz.

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub 'a yeni bir cihaz kaydetme

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub bağlantı dizesini al

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Hizmet uygulaması oluşturma

Bu bölümde, **Mydeviceıd**ile ilişkili cihaz ikizi konum meta verilerini C#ekleyen bir .NET konsol uygulaması oluşturursunuz. Ardından, IoT Hub 'ında depolanan cihaz TWINS 'i, ABD 'de bulunan cihazları ve ardından hücresel bağlantı bildirdikleri sorgular.

1. Visual Studio 'da **Yeni proje oluştur**' u seçin. **Yeni proje oluştur**' da **konsol uygulaması (.NET Framework)** öğesini seçin ve ardından **İleri**' yi seçin.

1. **Yeni projenizi yapılandırın**bölümünde **Addtagsandquery**adlı projeyi adlandırın.

    ![AddTagsAndQuery projenizi yapılandırma](./media/iot-hub-csharp-csharp-twin-getstarted/config-addtagsandquery-app.png)

1. Çözüm Gezgini, **Addtagsandquery** projesine sağ tıklayın ve ardından **NuGet Paketlerini Yönet**' i seçin.

1. **Araştır** ' ı seçin ve **Microsoft. Azure. Devices**' i arayın ve seçin. **Yükle**’yi seçin.

    ![NuGet Paket Yöneticisi penceresi](./media/iot-hub-csharp-csharp-twin-getstarted/nuget-package-addtagsandquery-app.png)

   Bu adım, [Azure IoT hizmeti SDK 'sı](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet paketi ve bağımlılıklarını indirir, yükler ve buna bir başvuru ekler.

1. Aşağıdaki `using` deyimlerini **Program.cs** dosyasının üst kısmına ekleyin:

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

1. **Program** sınıfına aşağıdaki alanları ekleyin. `{iot hub connection string}` [IoT Hub bağlantı dizesini al](#get-the-iot-hub-connection-string)bölümünde kopyaladığınız IoT Hub bağlantı dizesiyle değiştirin.

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. **Program** sınıfına aşağıdaki yöntemi ekleyin:

    ```csharp  
    public static async Task AddTagsAndQuery()
    {
        var twin = await registryManager.GetTwinAsync("myDeviceId");
        var patch =
            @"{
                tags: {
                    location: {
                        region: 'US',
                        plant: 'Redmond43'
                    }
                }
            }";
        await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);

        var query = registryManager.CreateQuery(
          "SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
        var twinsInRedmond43 = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43: {0}", 
          string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));

        query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
        var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43 using cellular network: {0}", 
          string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
    }
    ```

    **Registrymanager** sınıfı, hizmetten cihaz ikikiyle etkileşimde bulunmak için gereken tüm yöntemleri kullanıma sunar. Önceki kod, ilk olarak **Registrymanager** nesnesini başlatır ve ardından **mydeviceıd**için Device ikizi alır ve son olarak etiketlerini istenen konum bilgileriyle güncelleştirir.

    Güncelleştirme sonrasında, iki sorgu yürütülür: İlki yalnızca **Redmond43** tesisinde bulunan cihazların cihaz ikelerini seçer ve ikincisi ise yalnızca hücresel ağ üzerinden de bağlı olan cihazları seçecek şekilde sorguyu iyileştirir.

    Önceki kod, **sorgu** nesnesini oluşturduğunda, en fazla döndürülen belge sayısını belirtir. **Sorgu** nesnesi, tüm sonuçları almak Için **Getnextastwınasync** yöntemlerini birden çok kez çağırmak için kullanabileceğiniz bir **HasMoreResults** Boolean özelliği içerir. **Getnextasjson** adlı bir yöntem, Device-wins olmayan sonuçlar (örneğin, toplama sorgularının sonuçları) için kullanılabilir.

1. Son olarak, **Main** yöntemine aşağıdaki satırları ekleyin:

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. **Addtagsandquery** projesine sağ tıklayıp **Hata Ayıkla**' yı ve ardından **Yeni örnek Başlat**' ı seçerek bu uygulamayı çalıştırın. **Redmond43** ' de bulunan tüm cihazları isteyen sorgunun sonuçlarında bir cihaz görmeniz gerekir ve sonuçları hücresel ağ kullanan cihazlara kısıtlayan sorgu için yok.

    ![Penceredeki sorgu sonuçları](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

Sonraki bölümde, bağlantı bilgilerini raporlayan ve önceki bölümde sorgunun sonucunu değiştiren bir cihaz uygulaması oluşturacaksınız.

## <a name="create-the-device-app"></a>Cihaz uygulaması oluşturma

Bu bölümde, hub 'ınıza **Mydeviceıd**olarak bağlanan bir .NET konsol uygulaması oluşturun ve ardından, bildirilen özelliklerini bir hücresel ağ kullanılarak bağlı olan bilgileri içerecek şekilde günceller.

1. Visual Studio'da **dosya** > **yeni** > **proje**. **Yeni proje oluştur**' da **konsol uygulaması (.NET Framework)** öğesini seçin ve ardından **İleri**' yi seçin.

1. **Yeni projenizi yapılandırın**bölümünde, proje **reportconnectivity**olarak adlandırın. **Çözüm**Için **çözüme Ekle**' yi seçin ve ardından **Oluştur**' u seçin.

1. Çözüm Gezgini, **Reportconnectivity** projesine sağ tıklayın ve ardından **NuGet Paketlerini Yönet**' i seçin.

1. **Araştır** ' ı seçin ve arama yapın ve **Microsoft. Azure. Devices. Client**' ı seçin. **Yükle**’yi seçin.

   Bu adım, [Azure IoT cihaz SDK 'sı](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet paketi ve bağımlılıklarını indirir, yükler ve buna bir başvuru ekler.

1. Aşağıdaki `using` deyimlerini **Program.cs** dosyasının üst kısmına ekleyin:

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. **Program** sınıfına aşağıdaki alanları ekleyin. `{device connection string}` [IoT Hub 'ına yeni bir cihaz kaydetme](#register-a-new-device-in-the-iot-hub)bölümünde not ettiğiniz cihaz bağlantı dizesiyle değiştirin.

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

1. **Program** sınıfına aşağıdaki yöntemi ekleyin:

    ```csharp
    public static async void InitClient()
    {
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
              TransportType.Mqtt);
            Console.WriteLine("Retrieving twin");
            await Client.GetTwinAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

    **İstemci** nesnesi, cihazdan cihaz ikikiyle etkileşimde bulunmak için gereken tüm yöntemleri kullanıma sunar. Yukarıda gösterilen kod, **istemci** nesnesini başlatır ve ardından **mydeviceıd**için cihaz ikizi alır.

1. **Program** sınıfına aşağıdaki yöntemi ekleyin:

    ```csharp  
    public static async void ReportConnectivity()
    {
        try
        {
            Console.WriteLine("Sending connectivity data as reported property");

            TwinCollection reportedProperties, connectivity;
            reportedProperties = new TwinCollection();
            connectivity = new TwinCollection();
            connectivity["type"] = "cellular";
            reportedProperties["connectivity"] = connectivity;
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

   Yukarıdaki kod, **Mydeviceıd** öğesinin bildirilen özelliğini bağlantı bilgileriyle güncelleştirir.

1. Son olarak, **Main** yöntemine aşağıdaki satırları ekleyin:

    ```csharp
    try
    {
        InitClient();
        ReportConnectivity();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. Çözüm Gezgini, çözümünüze sağ tıklayın ve **Başlangıç projelerini ayarla**' yı seçin.

1. **Ortak özellikler** > **Başlangıç projesinde** **birden çok başlangıç**projesi seçin. **Reportconnectivity**için **eylem**olarak **Başlat** ' ı seçin. Değişikliklerinizi kaydetmek için **Tamam**’ı seçin.  

1. **Reportconnectivity** projesine sağ tıklayıp **Hata Ayıkla**' yı seçip **Yeni örnek Başlat**' ı seçerek bu uygulamayı çalıştırın. Uygulamanın ikizi bilgilerini alarak ve sonra ***bildirilen bir özellik***olarak bağlantı göndererek uygulamayı görmeniz gerekir.

    ![Bağlantı raporlamak için cihaz uygulamasını çalıştırma](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)

   Cihaz bağlantı bilgilerini raporladıktan sonra, her iki sorgu de görünmelidir.

1. **Addtagsandquery** projesine sağ tıklayın ve sorguları yeniden çalıştırmak için **Hata Ayıkla** > **Yeni örnek Başlat** ' ı seçin. Bu kez, **Mydeviceıd** her iki sorgu sonucunda görünmelidir.

    ![Cihaz bağlantısı başarıyla bildirildi](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure portalında yeni bir IoT hub'ı yapılandırdınız ve ardından IoT hub'ının kimlik kayıt defterinde bir cihaz kimliği oluşturdunuz. Cihaz meta verilerini bir arka uç uygulamasından Etiketler olarak eklediniz ve cihaz ikizi cihaz bağlantı bilgilerini raporlamak için bir sanal cihaz uygulaması yazmış olursunuz. Ayrıca, bu bilgilerin SQL benzeri IoT Hub sorgu dilini kullanarak nasıl sorgulanalınacağını da öğrendiniz.

Aşağıdaki kaynaklardan daha fazla bilgi edinebilirsiniz:

* Cihazlardan telemetri gönderme hakkında bilgi edinmek için bkz. [bir cihazdan IoT Hub 'a telemetri gönderme](quickstart-send-telemetry-dotnet.md) öğreticisi.

* Device ikizi 'ın istenen özelliklerini kullanarak cihazların nasıl yapılandırılacağını öğrenmek için, [cihazları yapılandırmak için istenen özellikleri kullanma](tutorial-device-twins.md) öğreticisini inceleyin.

* Kullanıcı denetimli bir uygulamadan bir fanı açmak gibi cihazları etkileşimli olarak denetleme hakkında bilgi edinmek için [doğrudan Yöntemler kullanma](quickstart-control-device-dotnet.md) öğreticisine bakın.
