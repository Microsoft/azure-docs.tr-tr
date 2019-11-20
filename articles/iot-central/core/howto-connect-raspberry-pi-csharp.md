---
title: Azure IoT Central uygulamanıza bir Raspberry Pi bağlama (C#) | Microsoft Docs
description: Bir cihaz geliştiricisi olarak, kullanarak C#Raspberry Pi 'yi Azure IoT Central uygulamanıza bağlama.
author: viv-liu
ms.author: viviali
ms.date: 09/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ba903d75707be91bb8af1271b52eb260ffcde306
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72951243"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Azure IoT Central uygulamanıza bir Raspberry PI bağlama (C#)

[!INCLUDE [howto-raspberrypi-selector](../../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Bu makalede, bir cihaz geliştiricisi olarak, C# programlama dilini kullanarak bir Raspberry Pi 'yi Microsoft Azure IoT Central uygulamanıza nasıl bağlayabileceğinizi açıklar.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makaledeki adımları tamamlayabilmeniz için aşağıdaki bileşenlere ihtiyacınız vardır:

* **Örnek Devkits** uygulama şablonundan oluşturulan bir Azure IoT Central uygulaması. Daha fazla bilgi için bkz. [Uygulama oluşturma hızlı başlangıcı](quick-deploy-iot-central.md).
* Raspbian işletim sistemini çalıştıran bir Raspberry PI cihazı. Raspberry PI, internet 'e bağlanabilmelidir. Daha fazla bilgi için bkz. [Raspberry PI 'Nizi ayarlama](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

## <a name="sample-devkits-application"></a>**Örnek Devkits** uygulaması

**Örnek Devkits** uygulama şablonundan oluşturulan bir uygulama, aşağıdaki özelliklere sahip bir **Raspberry PI** cihaz şablonu içerir:

* Telemetri, cihazın toplayacağı aşağıdaki ölçüleri içerir:
  * Nem oranı
  * Sıcaklık
  * Gereksiniminin
  * Manyetik tometre (X, Y, Z)
  * İvme ölçer (X, Y, Z)
  * Jroscope (X, Y, Z)
* Ayarlar
  * Geril
  * Geçerli
  * Fan hızı
  * IR geçişi.
* Özellikler
  * Zar numarası cihaz özelliği
  * Konum bulutu özelliği

Cihaz şablonunun yapılandırmasının tam ayrıntıları için, [Raspberry PI cihaz şablonu ayrıntılarına](#raspberry-pi-device-template-details)bakın.

## <a name="add-a-real-device"></a>Gerçek cihaz ekleme

Azure IoT Central uygulamanızda, **Raspberry PI** cihaz şablonundan gerçek bir cihaz ekleyin. Cihaz bağlantısı ayrıntılarını (**kapsam kimliği**, **cihaz kimliği**ve **birincil anahtar**) bir yere unutmayın. Daha fazla bilgi için bkz. [Azure IoT Central uygulamanıza gerçek bir cihaz ekleme](tutorial-add-device.md).

### <a name="create-your-net-application"></a>.NET uygulamanızı oluşturma

Cihaz uygulamasını masaüstü makinenizde oluşturup test edersiniz.

Aşağıdaki adımları tamamlayabilmeniz için Visual Studio Code kullanabilirsiniz. Daha fazla bilgi için bkz. [Ile C#çalışma ](https://code.visualstudio.com/docs/languages/csharp).

> [!NOTE]
> İsterseniz, farklı bir kod Düzenleyicisi kullanarak aşağıdaki adımları tamamlayabilirsiniz.

1. .NET projenizi başlatmak ve gerekli NuGet paketlerini eklemek için aşağıdaki komutları çalıştırın:

   ```cmd/sh
   mkdir pisample
   cd pisample
   dotnet new console
   dotnet add package Microsoft.Azure.Devices.Client
   dotnet add package Microsoft.Azure.Devices.Provisioning.Client
   dotnet add package Microsoft.Azure.Devices.Provisioning.Transport.Mqtt
   dotnet restore
   ```

1. Visual Studio Code `pisample` klasörü açın. Ardından **pisample. csproj** proje dosyasını açın. Aşağıdaki kod parçacığında gösterilen `<RuntimeIdentifiers>` etiketini ekleyin:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <RootNamespace>pisample</RootNamespace>
        <RuntimeIdentifiers>win-arm;linux-arm</RuntimeIdentifiers>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Devices.Client" Version="1.21.0" />
        <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Client" Version="1.4.0" />
        <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Transport.Mqtt" Version="1.1.8" />
      </ItemGroup>
    </Project>
    ```

    > [!NOTE]
    > Paket sürüm numaranız gösterilenden daha yüksek olabilir.

1. **Pisample. csproj**öğesini kaydedin. Visual Studio Code restore komutunu yürütmenizi isterse, **geri yükle**' yi seçin.

1. **Program.cs** açın ve içeriğini aşağıdaki kodla değiştirin. `{your Scope ID}`, `{your Device ID}`ve `{your Device Primary Key}`, daha önce bir nota yaptığınız değerlerle güncelleştirin:

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Microsoft.Azure.Devices.Provisioning.Client;
    using Microsoft.Azure.Devices.Provisioning.Client.Transport;

    using Newtonsoft.Json;

    namespace pisample
    {
      class Program
      {
        static string ScopeID = "{your Scope ID}";
        static string DeviceID = "{your Device ID}";
        static string PrimaryKey = "{your Device Primary Key}";
        static string GlobalDeviceEndpoint = "global.azure-devices-provisioning.net";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();
        static CancellationTokenSource cts;
        static double baseTemperature = 60;
        static double basePressure = 500;
        static double baseHumidity = 50;

        static async Task Main(string[] args)
        {
          Console.WriteLine("== Raspberry Pi Azure IoT Central example ==");

          try
          {

            using (var security = new SecurityProviderSymmetricKey(DeviceID, PrimaryKey, null))
            {
              DeviceRegistrationResult result = await RegisterDeviceAsync(security);
              if (result.Status != ProvisioningRegistrationStatusType.Assigned) {
                Console.WriteLine("Failed to register device");
                return;
              }
              IAuthenticationMethod auth = new DeviceAuthenticationWithRegistrySymmetricKey(result.DeviceId, (security as SecurityProviderSymmetricKey).GetPrimaryKey());
              Client = DeviceClient.Create(result.AssignedHub, auth, TransportType.Mqtt);
            }

            await SendDevicePropertiesAsync();

            Console.Write("Register settings changed handler...");
            await Client.SetDesiredPropertyUpdateCallbackAsync(HandleSettingChanged, null);
            Console.WriteLine("Done");

            cts = new CancellationTokenSource();
            Task task = SendTelemetryAsync(cts.Token);

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
            cts.Cancel();
            await task;
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine(ex.Message);
          }
        }

        public static async Task<DeviceRegistrationResult> RegisterDeviceAsync(SecurityProviderSymmetricKey security)
        {
            Console.WriteLine("Register device...");

            using (var transport = new ProvisioningTransportHandlerMqtt(TransportFallbackType.TcpOnly))
            {
              ProvisioningDeviceClient provClient =
                        ProvisioningDeviceClient.Create(GlobalDeviceEndpoint, ScopeID, security, transport);

              Console.WriteLine($"RegistrationID = {security.GetRegistrationID()}");


              Console.Write("ProvisioningClient RegisterAsync...");
              DeviceRegistrationResult result = await provClient.RegisterAsync();

              Console.WriteLine($"{result.Status}");
              Console.WriteLine($"ProvisioningClient AssignedHub: {result.AssignedHub}; DeviceID: {result.DeviceId}");

              return result;
            }
        }

        public static async Task SendDevicePropertiesAsync()
        {
            Console.WriteLine("Send device properties...");
            Random random = new Random();
            TwinCollection telemetryConfig = new TwinCollection();
            reportedProperties["dieNumber"] = random.Next(1, 6);
            Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }

        private static async Task SendTelemetryAsync(CancellationToken token)
        {
          Random rand = new Random();

          while (true)
          {
            double currentTemperature = baseTemperature + rand.NextDouble() * 20;
            double currentPressure = basePressure + rand.NextDouble() * 100;
            double currentHumidity = baseHumidity + rand.NextDouble() * 20;

            var telemetryDataPoint = new
            {
              humidity = currentHumidity,
              pressure = currentPressure,
              temp = currentTemperature
            };
            var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
            var message = new Message(Encoding.ASCII.GetBytes(messageString));

            token.ThrowIfCancellationRequested();
            await Client.SendEventAsync(message);

            Console.WriteLine("{0} > Sending telemetry: {1}", DateTime.Now, messageString);

            await Task.Delay(1000);
          }
        }


        private static async Task HandleSettingChanged(TwinCollection desiredProperties, object userContext)
        {
          Console.WriteLine("Received settings change...");
          Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

          string setting = "fanSpeed";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          setting = "setVoltage";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          setting = "setCurrent";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          setting = "activateIR";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          Console.WriteLine("Send settings changed acknowledgement...");
          await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }

        private static void BuildAcknowledgement(TwinCollection desiredProperties, string setting)
        {
          reportedProperties[setting] = new
          {
            value = desiredProperties[setting]["value"],
            status = "completed",
            desiredVersion = desiredProperties["$version"],
            message = "Processed"
          };
        }
      }
    }
    ```

## <a name="run-your-net-application"></a>.NET uygulamanızı çalıştırma

Örnek uygulamayı derlemek ve çalıştırmak için:

1. Komut satırı ortamınızda aşağıdaki komutu çalıştırın:

   ```cmd/sh
   dotnet restore
   dotnet publish -r linux-arm
   ```

1. `pisample\bin\Debug\netcoreapp2.1\linux-arm\publish` klasörünü Raspberry PI cihazınıza kopyalayın. Dosyaları kopyalamak için **SCP** komutunu kullanabilirsiniz, örneğin:

    ```cmd/sh
    scp -r publish pi@192.168.0.40:publish
    ```

    Daha fazla bilgi için bkz. [Raspberry PI uzaktan erişim](https://www.raspberrypi.org/documentation/remote-access/).

1. Raspberry PI cihazınızda oturum açın ve bir kabukta aşağıdaki komutları çalıştırın:

    ```cmd/sh
    sudo apt-get update
    sudo apt-get install libc6 libcurl3 libgcc1 libgssapi-krb5-2 liblttng-ust0 libstdc++6 libunwind8 libuuid1 zlib1g
    ```

1. Raspberry PI 'de aşağıdaki komutları çalıştırın:

    ```cmd/sh
    cd publish
    chmod 777 pisample
    ./pisample
    ```

    ![Program başlar](./media/howto-connect-raspberry-pi-csharp/device_begin.png)

1. Azure IoT Central uygulamanızda, Raspberry PI üzerinde çalışan kodun uygulamayla nasıl etkileşime gireceğini görebilirsiniz:

   * Gerçek cihazınızın **ölçümler** sayfasında telemetri ' i görebilirsiniz.
   * **Özellikler** sayfasında, bildirilen **zar numarası** özelliğinin değerini görebilirsiniz.
   * **Ayarlar** sayfasında, Raspberry Pi üzerinde voltaj ve fan hızı gibi çeşitli ayarları değiştirebilirsiniz.

     Aşağıdaki ekran görüntüsünde, ayar değişikliğini alan Raspberry PI gösterilmektedir:

     ![Raspberry PI ayar değişikliğini alır](./media/howto-connect-raspberry-pi-csharp/device_switch.png)

## <a name="raspberry-pi-device-template-details"></a>Raspberry PI cihaz şablonu ayrıntıları

**Örnek Devkits** uygulama şablonundan oluşturulan bir uygulama, aşağıdaki özelliklere sahip bir **Raspberry PI** cihaz şablonu içerir:

### <a name="telemetry-measurements"></a>Telemetri ölçümleri

| Alan adı     | Birimler  | Minimum | Maksimum | Ondalık basamak sayısı |
| -------------- | ------ | ------- | ------- | -------------- |
| Nem oranı       | %      | 0       | 100     | 0              |
| kopyalar           | 20     | -40     | 120     | 0              |
| basınç       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| Ivometerx | mg     | -2000   | 2000    | 0              |
| Iventery | mg     | -2000   | 2000    | 0              |
| Ivometerz | mg     | -2000   | 2000    | 0              |
| Jroscopex     | MDPS   | -2000   | 2000    | 0              |
| Jroscopey     | MDPS   | -2000   | 2000    | 0              |
| Jroscopez     | MDPS   | -2000   | 2000    | 0              |

### <a name="settings"></a>Ayarlar

Sayısal ayarlar

| Görünen ad | Alan adı | Birimler | Ondalık basamak sayısı | Minimum | Maksimum | Başlatma |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Geril      | Setvoltaj | Çalışmıyorken | 0              | 0       | 240     | 0       |
| Geçerli      | setCurrent | AMPS  | 0              | 0       | 100     | 0       |
| Fan hızı    | Fanın hızı   | RPM   | 0              | 0       | 1000    | 0       |

Ayarları aç

| Görünen ad | Alan adı | Metinde | Kapalı metin | Başlatma |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | Activateır | AÇIK      | KAPALI      | Kapalı     |

### <a name="properties"></a>Özellikler

| Tür            | Görünen ad | Alan adı | Veri türü                              |
| --------------- | ------------ | ---------- | -------------------------------------- |
| Cihaz özelliği | Zar numarası   | dieNumber  | number                                 |
| Konum        | Konum     | location   | {Enlem: float, Long: float, alt?: float} |

## <a name="next-steps"></a>Sonraki adımlar

Artık bir Raspberry Pi 'yi Azure IoT Central uygulamanıza bağlamayı öğrendiğinize göre, önerilen sonraki adım, kendi IoT cihazınız için [özel bir cihaz şablonu ayarlamayı](howto-set-up-template.md) öğrenirsiniz.
