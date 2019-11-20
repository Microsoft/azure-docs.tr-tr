---
title: Java ile Azure IoT Hub dosyaları cihazlardan yükleme | Microsoft Docs
description: Java için Azure IoT cihaz SDK 'sını kullanarak bir cihazdan buluta dosya yükleme. Karşıya yüklenen dosyalar bir Azure Storage blob kapsayıcısında depolanır.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 06/28/2017
ms.openlocfilehash: 81b80edcd2e880488e203960f8e2a6aa71b69679
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70161825"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-java"></a>IoT Hub (Java) ile cihazınızdan buluta dosya yükleme

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Bu öğretici, [Azure Blob depolamaya](../storage/index.yml)bir dosyayı karşıya yüklemek için [IoT Hub dosya karşıya yükleme yeteneklerini](iot-hub-devguide-file-upload.md) nasıl kullanacağınızı göstermek için [IoT Hub öğreticisiyle buluttan cihaza ileti gönderme](iot-hub-java-java-c2d.md) ' deki kodu oluşturur. Öğretici şunların nasıl yapıldığını göstermektedir:

* Bir dosyayı karşıya yüklemek için Azure Blob URI 'SI ile güvenli bir şekilde cihaz sağlayın.

* Uygulama arka uçtaki dosyayı işlemeyi tetiklemek için karşıya dosya yükleme bildirimlerini IoT Hub kullanın.

[Bir cihazdan IoT Hub 'ına](quickstart-send-telemetry-java.md) hızlı başlangıç yapın ve [IoT Hub öğreticisiyle buluttan cihaza iletiler gönderin](iot-hub-java-java-c2d.md) IoT Hub ' ın temel cihazdan buluta ve buluttan cihaza mesajlaşma işlevlerini gösterir. [IoT Hub öğreticisi ile ileti yönlendirmeyi yapılandırma](tutorial-routing.md) , Azure Blob depolamada cihazdan buluta iletileri güvenilir bir şekilde depolamanın yolunu açıklar. Ancak, bazı senaryolarda, cihazlarınızın IoT Hub kabul ettiği görece küçük cihazdan buluta iletileri içine gönderdikleri verileri kolayca eşleyemezsiniz. Örneğin:

* Görüntü içeren büyük dosyalar
* Videolar
* Yüksek frekansta örneklenmiş veri titreşimi
* Önceden işlenmiş veriler için bir form.

Bu dosyalar genellikle [Azure Data Factory](../data-factory/introduction.md) veya [Hadoop](../hdinsight/index.yml) yığını gibi araçları kullanarak bulutta toplu olarak işlenir. Dosyaları bir cihazdan kapladığınızda, IoT Hub güvenlik ve güvenilirliğini kullanmaya devam edebilirsiniz.

Bu öğreticinin sonunda iki Java konsol uygulaması çalıştırırsınız:

* **sanal cihaz**, [IoT Hub ile buluttan cihaza iletileri gönderme] öğreticisinde oluşturulan uygulamanın değiştirilmiş bir sürümüdür. Bu uygulama, IoT Hub 'ınız tarafından belirtilen SAS URI 'sini kullanarak bir dosyayı depolamaya yükler.

* , IoT Hub 'ınızdaki karşıya dosya yükleme bildirimleri alan **okuma-dosya-karşıya yükleme bildirimi**.

> [!NOTE]
> IoT Hub, Azure IoT cihaz SDK 'Ları aracılığıyla birçok cihaz platformunu ve dilini (C, .NET ve JavaScript dahil) destekler. Cihazınızı Azure IoT Hub bağlama hakkında adım adım yönergeler için [Azure IoT Geliştirici Merkezi](https://azure.microsoft.com/develop/iot) ' ne bakın.

## <a name="prerequisites"></a>Önkoşullar

* [Java SE Development Kit 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). JDK 8 için karşıdan yüklemeye ulaşmak üzere **uzun süreli destek** altında **Java 8** ' i seçtiğinizden emin olun.

* [Maven 3](https://maven.apache.org/download.cgi)

* Etkin bir Azure hesabı. (Hesabınız yoksa yalnızca birkaç dakika içinde [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturabilirsiniz.)

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Bir cihaz uygulamasından bir dosya yükleme

Bu bölümde, IoT Hub 'a bir dosyayı karşıya yüklemek için [IoT Hub ile buluttan cihaza Iletileri gönderme](iot-hub-java-java-c2d.md) bölümünde oluşturduğunuz cihaz uygulamasını değiştirirsiniz.

1. Bir resim dosyasını `simulated-device` klasöre kopyalayın ve `myimage.png`yeniden adlandırın.

2. Bir metin düzenleyicisi kullanarak `simulated-device\src\main\java\com\mycompany\app\App.java` dosyayı açın.

3. **Uygulama** sınıfına değişken bildirimini ekleyin:

    ```java
    private static String fileName = "myimage.png";
    ```

4. Karşıya dosya yükleme durumu geri çağırma iletilerini işlemek için aşağıdaki iç içe sınıfı **uygulama** sınıfına ekleyin:

    ```java
    // Define a callback method to print status codes from IoT Hub.
    protected static class FileUploadStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to file upload for " + fileName
            + " operation with status " + status.name());
      }
    }
    ```

5. Görüntüleri IoT Hub yüklemek için, aşağıdaki yöntemi **uygulama** sınıfına ekleyerek görüntüleri IoT Hub içine yükleyin:

    ```java
    // Use IoT Hub to upload a file asynchronously to Azure blob storage.
    private static void uploadFile(String fullFileName) throws FileNotFoundException, IOException
    {
      File file = new File(fullFileName);
      InputStream inputStream = new FileInputStream(file);
      long streamLength = file.length();

      client.uploadToBlobAsync(fileName, inputStream, streamLength, new FileUploadStatusCallBack(), null);
    }
    ```

6. Aşağıdaki kod parçacığında gösterildiği gibi, **UploadFile** yöntemini çağırmak için **Main** metodunu değiştirin:

    ```java
    client.open();

    try
    {
      // Get the filename and start the upload.
      String fullFileName = System.getProperty("user.dir") + File.separator + fileName;
      uploadFile(fullFileName);
      System.out.println("File upload started with success");
    }
    catch (Exception e)
    {
      System.out.println("Exception uploading file: " + e.getCause() + " \nERROR: " + e.getMessage());
    }

    MessageSender sender = new MessageSender();
    ```

7. **Sanal cihaz** uygulamasını derlemek ve hata olup olmadığını denetlemek için aşağıdaki komutu kullanın:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub bağlantı dizesini al

Bu makalede, [bir cihazdan IoT Hub 'ına telemetri gönderme](quickstart-send-telemetry-java.md)bölümünde oluşturduğunuz IoT Hub 'ından dosya yükleme bildirim iletilerini almak için bir arka uç hizmeti oluşturursunuz. Karşıya dosya yükleme bildirim iletilerini almak için hizmetinizin **hizmet bağlantısı** izni olması gerekir. Varsayılan olarak, her IoT Hub, bu izni veren **hizmet** adlı paylaşılan bir erişim ilkesiyle oluşturulur.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="receive-a-file-upload-notification"></a>Karşıya dosya yükleme bildirimi alma

Bu bölümde, IoT Hub karşıya dosya yükleme bildirim iletilerini alan bir Java konsol uygulaması oluşturursunuz.

1. Komut istemindeki aşağıdaki komutu kullanarak **okuma-dosya-karşıya yükleme bildirimi** adlı bir Maven projesi oluşturun. Bu komutun tek bir uzun komut olduğunu aklınızda:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-file-upload-notification -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Komut istemindeki yeni `read-file-upload-notification` klasöre gidin.

3. Bir metin düzenleyicisi kullanarak `pom.xml` dosyayı `read-file-upload-notification` klasöründe açın ve **Bağımlılıklar** düğümüne aşağıdaki bağımlılığı ekleyin. Bağımlılığı eklemek, IoT Hub hizmetinize iletişim kurmak için uygulamanızdaki **ıothub-Java-Service-Client** paketini kullanmanıza olanak sağlar:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > [Maven aramasını](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)kullanarak **IoT-Service-Client** ' ın en son sürümünü kontrol edebilirsiniz.

4. `pom.xml` Dosyayı kaydedin ve kapatın.

5. Bir metin düzenleyicisi kullanarak `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` dosyayı açın.

6. Aşağıdaki **içeri aktarma** deyimlerini dosyaya ekleyin:

    ```java
    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;
    ```

7. Aşağıdaki sınıf düzeyi değişkenleri **App** sınıfına ekleyin. Yer tutucu `{Your IoT Hub connection string}` değerini, [IoT Hub bağlantı dizesini al](#get-the-iot-hub-connection-string)bölümünde daha önce kopyaladığınız IoT Hub bağlantı dizesiyle değiştirin:

    ```java
    private static final String connectionString = "{Your IoT Hub connection string}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
    private static FileUploadNotificationReceiver fileUploadNotificationReceiver = null;
    ```

8. Konsola dosya yükleme hakkında bilgi yazdırmak için aşağıdaki iç içe yerleştirilmiş sınıfı **uygulama** sınıfına ekleyin:

    ```java
    // Create a thread to receive file upload notifications.
    private static class ShowFileUploadNotifications implements Runnable {
      public void run() {
        try {
          while (true) {
            System.out.println("Receive file upload notifications...");
            FileUploadNotification fileUploadNotification = fileUploadNotificationReceiver.receive();
            if (fileUploadNotification != null) {
              System.out.println("File Upload notification received");
              System.out.println("Device Id : " + fileUploadNotification.getDeviceId());
              System.out.println("Blob Uri: " + fileUploadNotification.getBlobUri());
              System.out.println("Blob Name: " + fileUploadNotification.getBlobName());
              System.out.println("Last Updated : " + fileUploadNotification.getLastUpdatedTimeDate());
              System.out.println("Blob Size (Bytes): " + fileUploadNotification.getBlobSizeInBytes());
              System.out.println("Enqueued Time: " + fileUploadNotification.getEnqueuedTimeUtcDate());
            }
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

9. Karşıya dosya yükleme bildirimlerini dinleyen iş parçacığını başlatmak için aşağıdaki kodu **Main** yöntemine ekleyin:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(connectionString, protocol);

      if (serviceClient != null) {
        serviceClient.open();

        // Get a file upload notification receiver from the ServiceClient.
        fileUploadNotificationReceiver = serviceClient.getFileUploadNotificationReceiver();
        fileUploadNotificationReceiver.open();

        // Start the thread to receive file upload notifications.
        ShowFileUploadNotifications showFileUploadNotifications = new ShowFileUploadNotifications();
        ExecutorService executor = Executors.newFixedThreadPool(1);
        executor.execute(showFileUploadNotifications);

        System.out.println("Press ENTER to exit.");
        System.in.read();
        executor.shutdownNow();
        System.out.println("Shutting down sample...");
        fileUploadNotificationReceiver.close();
        serviceClient.close();
      }
    }
    ```

10. `read-file-upload-notification\src\main\java\com\mycompany\app\App.java` Dosyayı kaydedin ve kapatın.

11. **Okuma-dosya-karşıya yükleme-bildirim** uygulamasını derlemek ve hataları denetlemek için aşağıdaki komutu kullanın:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Uygulamaları çalıştırma

Şimdi uygulamaları çalıştırmaya hazırsınız.

`read-file-upload-notification` Klasöründeki bir komut isteminde aşağıdaki komutu çalıştırın:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

`simulated-device` Klasöründeki bir komut isteminde aşağıdaki komutu çalıştırın:

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

Aşağıdaki ekran görüntüsünde, **sanal cihaz** uygulamasındaki çıkış gösterilmektedir:

![Benzetimli cihaz uygulamasından çıkış](media/iot-hub-java-java-upload/simulated-device.png)

Aşağıdaki ekran görüntüsünde, **okuma-dosya-karşıya yükleme-bildirim** uygulamasının çıktısı gösterilmektedir:

![Okuma-dosya-karşıya yükleme-bildirim uygulamasının çıkışı](media/iot-hub-java-java-upload/read-file-upload-notification.png)

Yapılandırdığınız depolama kapsayıcısında karşıya yüklenen dosyayı görüntülemek için portalını kullanabilirsiniz:

![Karşıya yüklenen dosya](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, cihazların dosya karşıya yüklemelerini basitleştirmek için IoT Hub dosya yükleme yeteneklerini nasıl kullanacağınızı öğrendiniz. Aşağıdaki makalelerle IoT Hub özelliklerini ve senaryolarını keşfetmeye devam edebilirsiniz:

* [Programlama yoluyla IoT Hub 'ı oluşturma](iot-hub-rm-template-powershell.md)

* [C SDK 'ya giriş](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK’ları](iot-hub-devguide-sdks.md)

IoT Hub yeteneklerini daha fazla incelemek için bkz.:

* [IoT Edge ile cihaz benzetimi yapma](../iot-edge/tutorial-simulate-device-linux.md)
