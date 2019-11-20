---
title: .NET ile cihazdan Azure IoT Hub dosya yükleme | Microsoft Docs
description: .NET için Azure IoT cihaz SDK 'sını kullanarak bir cihazdan buluta dosya yükleme. Karşıya yüklenen dosyalar bir Azure Storage blob kapsayıcısında depolanır.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 07/04/2017
ms.author: robinsh
ms.openlocfilehash: db020092c076680eddd575f8e7e85a2060603dd8
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147764"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-net"></a>IoT Hub (.NET) ile cihazınızdan buluta dosya yükleme

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Bu öğretici, IoT Hub dosya yükleme yeteneklerini nasıl kullanacağınızı göstermek için [IoT Hub öğreticisiyle buluttan cihaza Iletileri gönderme](iot-hub-csharp-csharp-c2d.md) ' deki kodu oluşturur. Şunu gösterir:

* Bir dosyayı karşıya yüklemek için Azure Blob URI 'SI ile güvenli bir şekilde cihaz sağlayın.

* Uygulama arka uçtaki dosyayı işlemeyi tetiklemek için karşıya dosya yükleme bildirimlerini IoT Hub kullanın.

[Bir cihazdan IoT Hub 'ına](quickstart-send-telemetry-dotnet.md) hızlı başlangıç yapın ve [IoT Hub öğreticisiyle buluttan cihaza iletiler gönderin](iot-hub-csharp-csharp-c2d.md) IoT Hub ' ın temel cihazdan buluta ve buluttan cihaza mesajlaşma işlevlerini gösterir. [IoT Hub öğreticisi Ile Ileti yönlendirmeyi yapılandırma](tutorial-routing.md) , Microsoft Azure Blob depolamada cihazdan buluta iletileri güvenilir bir şekilde depolamanın yolunu açıklar. Ancak, bazı senaryolarda, cihazlarınızın IoT Hub kabul ettiği görece küçük cihazdan buluta iletileri içine gönderdikleri verileri kolayca eşleyemezsiniz. Örneğin:

* Görüntü içeren büyük dosyalar

* Videolar

* Yüksek frekansta örneklenmiş veri titreşimi

* Önceden işlenmiş veriler için bir form

Bu dosyalar genellikle [Azure Data Factory](../data-factory/introduction.md) veya [Hadoop](../hdinsight/index.yml) yığını gibi araçları kullanarak bulutta toplu olarak işlenir. Bir cihazdan dosya yüklemeniz gerektiğinde IoT Hub güvenlik ve güvenilirliğini kullanmaya devam edebilirsiniz.

Bu öğreticinin sonunda iki .NET konsol uygulaması çalıştırırsınız:

* **SimulatedDevice**. Bu uygulama, IoT Hub 'ınız tarafından belirtilen SAS URI 'sini kullanarak bir dosyayı depolamaya yükler. Bu, [IoT Hub öğreticisi ile buluttan cihaza Iletileri gönder](iot-hub-csharp-csharp-c2d.md) ' de oluşturulan uygulamanın değiştirilmiş bir sürümüdür.

* **Readfileuploadnotification**. Bu uygulama, IoT Hub 'ınızdaki karşıya dosya yükleme bildirimleri alır.

> [!NOTE]
> IoT Hub, Azure IoT cihaz SDK 'Ları aracılığıyla C, Java, Python ve JavaScript gibi birçok cihaz platformunu ve dilini destekler. Cihazınızı Azure IoT Hub bağlama hakkında adım adım yönergeler için [Azure IoT Geliştirici Merkezi](https://azure.microsoft.com/develop/iot) ' ne bakın.

## <a name="prerequisites"></a>Önkoşullar

* Visual Studio

* Etkin bir Azure hesabı. Hesabınız yoksa yalnızca birkaç dakika içinde [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturabilirsiniz.

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Bir cihaz uygulamasından bir dosya yükleme

Bu bölümde, IoT Hub 'ından buluttan cihaza iletileri almak için [IoT Hub ile buluttan cihaza Iletileri gönderme](iot-hub-csharp-csharp-c2d.md) bölümünde oluşturduğunuz cihaz uygulamasını değiştirirsiniz.

1. Visual Studio Çözüm Gezgini, **SimulatedDevice** projesine sağ tıklayın ve**var olan öğe** **Ekle** > ' yi seçin. Bir görüntü dosyası bulun ve projenize ekleyin. Bu öğreticide görüntünün adlandırıldığı `image.jpg`varsayılır.

1. Görüntüye sağ tıklayın ve ardından **Özellikler**' i seçin. **Çıkış Dizinine Kopyala** ' nın **her zaman Kopyala**olarak ayarlandığından emin olun.

    ![Çıkış dizinine kopyalamak için görüntü özelliğinin nerede güncelleşbir olduğunu göster](./media/iot-hub-csharp-csharp-file-upload/image-properties.png)

1. **Program.cs** dosyasında, dosyanın en üstüne aşağıdaki deyimleri ekleyin:

    ```csharp
    using System.IO;
    ```

1. **Program** sınıfına aşağıdaki yöntemi ekleyin:

    ```csharp
    private static async void SendToBlobAsync()
    {
        string fileName = "image.jpg";
        Console.WriteLine("Uploading file: {0}", fileName);
        var watch = System.Diagnostics.Stopwatch.StartNew();

        using (var sourceData = new FileStream(@"image.jpg", FileMode.Open))
        {
            await deviceClient.UploadToBlobAsync(fileName, sourceData);
        }

        watch.Stop();
        Console.WriteLine("Time to upload file: {0}ms\n", watch.ElapsedMilliseconds);
    }
    ```

    `UploadToBlobAsync` Yöntemi, karşıya yüklenecek dosyanın dosya adı ve akış kaynağını alır ve depolamaya yüklemeyi işler. Konsol uygulaması, dosyayı karşıya yüklemek için geçen süreyi görüntüler.

1. **Main** yöntemine `Console.ReadLine()`aşağıdaki satırı ekleyin:

    ```csharp
    SendToBlobAsync();
    ```

> [!NOTE]
> Kolaylık sağlaması için, bu öğretici herhangi bir yeniden deneme ilkesi uygulamaz. Üretim kodunda, [geçici hata işleme](/azure/architecture/best-practices/transient-faults)bölümünde önerildiği gibi, üstel geri alma gibi yeniden deneme ilkeleri uygulamanız gerekir.

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub bağlantı dizesini al

Bu makalede, [bir cihazdan IoT Hub 'ına telemetri gönderme](quickstart-send-telemetry-dotnet.md)bölümünde oluşturduğunuz IoT Hub 'ından dosya yükleme bildirim iletilerini almak için bir arka uç hizmeti oluşturursunuz. Karşıya dosya yükleme bildirim iletilerini almak için hizmetinizin **hizmet bağlantısı** izni olması gerekir. Varsayılan olarak, her IoT Hub, bu izni veren **hizmet** adlı paylaşılan bir erişim ilkesiyle oluşturulur.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Karşıya dosya yükleme bildirimi alma

Bu bölümde, IoT Hub 'ten karşıya dosya yükleme bildirimi iletileri alan bir .NET konsol uygulaması yazarsınız.

1. Geçerli Visual Studio çözümünde **Dosya** > **Yeni** > **Proje**' yi seçin. **Yeni proje oluştur**' da **konsol uygulaması (.NET Framework)** öğesini seçin ve ardından **İleri**' yi seçin.

1. Projeyi *Readfileuploadnotification*olarak adlandırın. **Çözüm**altında **çözüme Ekle**' yi seçin. Projeyi oluşturmak için **Oluştur**'u seçin.

    ![Visual Studio 'da ReadFileUploadNotification projesini yapılandırma](./media/iot-hub-csharp-csharp-file-upload/read-file-upload-project-configure.png)

1. Çözüm Gezgini, **Readfileuploadnotification** projesine sağ tıklayın ve **NuGet Paketlerini Yönet**' i seçin.

1. **NuGet Paket Yöneticisi**' nde, **Araştır**' ı seçin. **Microsoft. Azure. Devices**' i arayıp seçin ve ardından **Install**' ı seçin.

    Bu adım, **Readfileuploadnotification** projesinde [Azure IoT hizmeti SDK 'sı NuGet paketi](https://www.nuget.org/packages/Microsoft.Azure.Devices/) ' ni indirir, yükler ve bir başvuru ekler.

1. Bu proje için **program.cs** dosyasında, dosyanın en üstüne aşağıdaki ifadeyi ekleyin:

    ```csharp
    using Microsoft.Azure.Devices;
    ```

1. **Program** sınıfına aşağıdaki alanları ekleyin. Yer tutucu `{iot hub connection string}` değerini, [IoT Hub bağlantı dizesini al](#get-the-iot-hub-connection-string)bölümünde daha önce kopyaladığınız IoT Hub bağlantı dizesiyle değiştirin:

    ```csharp
    static ServiceClient serviceClient;
    static string connectionString = "{iot hub connection string}";
    ```

1. **Program** sınıfına aşağıdaki yöntemi ekleyin:

    ```csharp
    private async static void ReceiveFileUploadNotificationAsync()
    {
        var notificationReceiver = serviceClient.GetFileNotificationReceiver();

        Console.WriteLine("\nReceiving file upload notification from service");
        while (true)
        {
            var fileUploadNotification = await notificationReceiver.ReceiveAsync();
            if (fileUploadNotification == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received file upload notification: {0}", 
              string.Join(", ", fileUploadNotification.BlobName));
            Console.ResetColor();

            await notificationReceiver.CompleteAsync(fileUploadNotification);
        }
    }
    ```

    Bu alma deseninin, cihaz uygulamasından gelen buluttan cihaza iletileri almak için kullandığı aynı olduğunu göz önünde bir şekilde yapın.

1. Son olarak, **Main** yöntemine aşağıdaki satırları ekleyin:

    ```csharp
    Console.WriteLine("Receive file upload notifications\n");
    serviceClient = ServiceClient.CreateFromConnectionString(connectionString);
    ReceiveFileUploadNotificationAsync();
    Console.WriteLine("Press Enter to exit\n");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Uygulamaları çalıştırma

Şimdi uygulamaları çalıştırmaya hazırsınız.

1. Çözüm Gezgini 'nde çözümünüze sağ tıklayın ve **Başlangıç projelerini ayarla**' yı seçin.

1. **Ortak özellikler** > **Başlangıç projesinde** **birden çok başlangıç**projesi ' ni seçin ve ardından **readfileuploadnotification** ve **SimulatedDevice**için **başlatma** eylemini seçin. Değişikliklerinizi kaydetmek için **Tamam**’ı seçin.

1. **F5**tuşuna basın. Her iki uygulama da başlamalıdır. Karşıya yükleme işleminin bir konsol uygulamasında tamamlandığını ve diğer konsol uygulaması tarafından alınan karşıya yükleme bildirim iletisini görmeniz gerekir. Azure Depolama hesabınızdaki karşıya yüklenen dosyanın varlığını denetlemek için [Azure Portal](https://portal.azure.com/) veya Visual Studio Sunucu Gezgini kullanabilirsiniz.

    ![Çıkış ekranını gösteren ekran görüntüsü](./media/iot-hub-csharp-csharp-file-upload/run-apps1.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, cihazların dosya karşıya yüklemelerini basitleştirmek için IoT Hub dosya yükleme yeteneklerini nasıl kullanacağınızı öğrendiniz. Aşağıdaki makalelerde IoT Hub özellikleri ve senaryoları keşfetmeye devam edebilirsiniz:

* [Programlama yoluyla IoT Hub 'ı oluşturma](iot-hub-rm-template-powershell.md)

* [C SDK 'ya giriş](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK’ları](iot-hub-devguide-sdks.md)

IoT Hub yeteneklerini daha fazla incelemek için bkz.:

* [Azure IoT Edge ile uç cihazlara AI dağıtma](../iot-edge/tutorial-simulate-device-linux.md)
