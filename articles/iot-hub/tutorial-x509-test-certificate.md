---
title: Eğitim-Azure IoT Hub cihazların kimliğini doğrulamak için X. 509.440 sertifikalarındaki test özelliği | Microsoft Docs
description: Öğretici-Azure IoT Hub kimlik doğrulaması yapmak için X. 509.440 sertifikalarınızı test edin
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 7d1900782fce6b84ed79014e985393f3626d171b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379443"
---
# <a name="tutorial-testing-certificate-authentication"></a>Öğretici: sertifika kimlik doğrulamasını test etme

Sertifikanızın IoT Hub cihazınızın kimliğini doğrulayabilmesi için aşağıdaki C# kod örneğini kullanabilirsiniz. Test kodunu çalıştırmadan önce aşağıdakileri yapmanız gerektiğini unutmayın:

* Kök CA veya alt CA sertifikası oluşturun.
* CA sertifikanızı IoT Hub yükleyin.
* CA sertifikasına sahip olduğunuzu kanıtlayın.
* IoT Hub bir cihaz ekleyin.
* Cihazımız ile aynı cihaz KIMLIĞINE sahip bir cihaz sertifikası oluşturun.

## <a name="code-example"></a>Kod Örneği

Aşağıdaki kod örneği, IoT Hub 'ınız için kaydedilen X. 509.440 cihazının benzetimini yapmak üzere bir C# uygulamasının nasıl oluşturulduğunu gösterir. Örnek, sanal cihazdan hub 'ınıza sıcaklık ve nem değerleri gönderir. Bu öğreticide yalnızca cihaz uygulaması oluşturacağız. Bu sanal cihaz tarafından gönderilen olaylara yanıt gönderecek IoT Hub hizmet uygulaması oluşturmak için okuyucuların bir alıştırma olarak kalır.

1. Visual Studio 'yu açın, **Yeni proje oluştur**' u seçin ve ardından **konsol uygulaması (.NET Framework)** proje şablonunu seçin. **İleri**’yi seçin.

1. **Yeni projenizi yapılandırın** bölümünde projeyi *SimulateX509Device* olarak adlandırın ve ardından **Oluştur**' u seçin.

   ![Visual Studio 'da X. 509.952 cihaz projesi oluşturma](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. Çözüm Gezgini, **SimulateX509Device** projesine sağ tıklayın ve ardından **NuGet Paketlerini Yönet**' i seçin.

1. **NuGet Paket Yöneticisi**'Nde, **Araştır** ' ı seçin ve arama yapın ve **Microsoft. Azure. Devices. Client**' ı seçin. **Yükle**'yi seçin.

   ![Visual Studio 'da cihaz SDK 'Sı NuGet paketi ekleme](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    Bu adım, Azure IoT cihaz SDK 'Sı NuGet paketi ve bağımlılıklarını indirir, yükler ve buna bir başvuru ekler.

    Aşağıdaki kodu girin ve çalıştırın:

```csharp
using System;
using Microsoft.Azure.Devices.Client;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;
using System.Text;

namespace SimulateX509Device
{
    class Program
    {
        private static int MESSAGE_COUNT = 5;

        // Temperature and humidity variables.
        private const int TEMPERATURE_THRESHOLD = 30;
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();

        // Set the device ID to the name (device identifier) of your device.
        private static String deviceId = "{your-device-id}";

        static async Task SendEvent(DeviceClient deviceClient)
        {
            string dataBuffer;
            Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

            // Iterate MESSAGE_COUNT times to set randomm termperature and humidity values.
            for (int count = 0; count < MESSAGE_COUNT; count++)
            {
                // Set random values for temperature and humidity.
                temperature = rnd.Next(20, 35);
                humidity = rnd.Next(60, 80);
                dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
                Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
                eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
                Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

                // Send to IoT Hub.
                await deviceClient.SendEventAsync(eventMessage);
            }
        }
        static void Main(string[] args)
        {
            try
            {
                // Create an X.509 certificate object.
                var cert = new X509Certificate2(@"{full path to pfx certificate.pfx}", "{your certificate password}");

                // Create an authentication object using your X.509 certificate. 
                var auth = new DeviceAuthenticationWithX509Certificate("{your-device-id}", cert);

                // Create the device client.
                var deviceClient = DeviceClient.Create("{your-IoT-Hub-name}.azure-devices.net", auth, TransportType.Mqtt);

                if (deviceClient == null)
                {
                    Console.WriteLine("Failed to create DeviceClient!");
                }
                else
                {
                    Console.WriteLine("Successfully created DeviceClient!");
                    SendEvent(deviceClient).Wait();
                }

                Console.WriteLine("Exiting...\n");
            }
            catch (Exception ex)
            {
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
         }
    }
}
```