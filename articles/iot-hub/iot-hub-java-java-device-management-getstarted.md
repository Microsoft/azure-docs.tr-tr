---
title: Azure IoT Hub cihaz yönetimini kullanmaya başlama (Java) | Microsoft Docs
description: Uzak cihaz yeniden başlatma işlemini başlatmak için Azure IoT Hub cihaz yönetimi 'ni kullanma. Java için Azure IoT cihaz SDK 'sını, doğrudan yöntemini çağıran bir hizmet uygulaması uygulamak üzere Java için Azure IoT hizmeti SDK 'sını içeren bir sanal cihaz uygulaması uygulamak üzere kullanırsınız.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 52d7eb035ed06c368214b8b5cb7ef583cd99b51b
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70161994"
---
# <a name="get-started-with-device-management-java"></a>Cihaz yönetimini kullanmaya başlama (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Bu öğretici şunların nasıl yapıldığını gösterir:

* Bir IoT Hub oluşturmak ve IoT Hub 'ınızda bir cihaz kimliği oluşturmak için Azure portal kullanın.

* Cihazı yeniden başlatmak için doğrudan yöntem uygulayan bir sanal cihaz uygulaması oluşturun. Doğrudan Yöntemler buluttan çağrılır.

* IoT Hub 'ınız aracılığıyla sanal cihaz uygulamasında doğrudan önyükleme yöntemini çağıran bir uygulama oluşturun. Bu uygulama daha sonra yeniden başlatma işleminin ne zaman tamamlandığını görmek için cihazdan bildirilen özellikleri izler.

Bu öğreticinin sonunda iki Java konsol uygulamanız vardır:

**sanal cihaz**. Bu uygulama:

* Daha önce oluşturulan cihaz kimliğiyle IoT Hub 'ınıza bağlanır.

* Bir yeniden başlatma doğrudan yöntem çağrısı alır.

* Fiziksel yeniden başlatmanın benzetimini yapar.

* Bildirilen bir özellik ile son yeniden başlatmanın zamanını raporlar.

**Tetikle-yeniden başlatın**. Bu uygulama:

* Sanal cihaz uygulamasında doğrudan bir yöntemi çağırır.

* Sanal cihaz tarafından gönderilen doğrudan yöntem çağrısının yanıtını görüntüler.

* Güncelleştirilmiş raporlanan özellikleri görüntüler.

> [!NOTE]
> Cihazlarda ve çözüm arka ucunda çalıştırılacak uygulamalar oluşturmak için kullanabileceğiniz SDK 'lar hakkında bilgi için bkz. [Azure IoT SDK 'ları](iot-hub-devguide-sdks.md).

## <a name="prerequisites"></a>Önkoşullar

* [Java SE Development Kit 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). JDK 8 için karşıdan yüklemeye ulaşmak üzere **uzun süreli destek** altında **Java 8** ' i seçtiğinizden emin olun.

* [Maven 3](https://maven.apache.org/download.cgi)

* Etkin bir Azure hesabı. (Hesabınız yoksa yalnızca birkaç dakika içinde [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturabilirsiniz.)

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub 'a yeni bir cihaz kaydetme

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub bağlantı dizesini al

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Doğrudan bir yöntem kullanarak cihazda Uzaktan yeniden başlatma tetikleyin

Bu bölümde, şu şekilde bir Java konsol uygulaması oluşturursunuz:

1. Sanal cihaz uygulamasında doğrudan önyükleme yöntemini çağırır.

2. Yanıtı görüntüler.

3. Yeniden başlatmanın ne zaman tamamlandığını öğrenmek için cihazdan gönderilen bildirilen özellikleri yoklar.

Bu konsol uygulaması, doğrudan yöntemi çağırmak ve bildirilen özellikleri okumak için IoT Hub bağlanır.

1. **DM-Get-Started**adlı boş bir klasör oluşturun.

2. **DM-Get-Started** klasöründe, komut isteminde aşağıdaki komutu kullanarak **tetikleyici-yeniden başlatma** adlı bir Maven projesi oluşturun:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. Komut istemindeki **tetikleyici-yeniden Başlat** klasörüne gidin.

4. Bir metin düzenleyicisi kullanarak, **tetikleyici-yeniden başlatma** klasöründeki **pom. xml** dosyasını açın ve aşağıdaki bağımlılığı **Bağımlılıklar** düğümüne ekleyin. Bu bağımlılık, IoT Hub 'ınız ile iletişim kurmak için uygulamanızdaki IoT-Service-Client paketini kullanmanıza olanak sağlar:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.17.1</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > [Maven aramasını](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)kullanarak **IoT-Service-Client** ' ın en son sürümünü kontrol edebilirsiniz.

5. **Bağımlılıklar** düğümünden sonra aşağıdaki **derleme** düğümünü ekleyin. Bu yapılandırma, Maven 'in uygulamayı derlemek için Java 1,8 kullanmasını söyler:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. **Pod. xml** dosyasını kaydedin ve kapatın.

7. Bir metin düzenleyicisi kullanarak **Trigger-reboot\src\main\java\com\mycompany\app\App.Java** kaynak dosyasını açın.

8. Aşağıdaki **içeri aktarma** deyimlerini dosyaya ekleyin:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

9. Aşağıdaki sınıf düzeyi değişkenleri **App** sınıfına ekleyin. Daha `{youriothubconnectionstring}` önce [, IoT Hub bağlantı dizesini al](#get-the-iot-hub-connection-string)bölümünde kopyaladığınız IoT Hub bağlantı dizesiyle değiştirin:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

10. Cihazdan bildirilen özellikleri her 10 saniyede bir ikizi okuyan bir iş parçacığı uygulamak için aşağıdaki iç içe sınıfını **uygulama** sınıfına ekleyin:

    ```java
    private static class ShowReportedProperties implements Runnable {
      public void run() {
        try {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          while (true) {
            System.out.println("Get reported properties from device twin");
            deviceTwins.getTwin(twinDevice);
            System.out.println(twinDevice.reportedPropertiesToString());
            Thread.sleep(10000);
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

11. **Main** yönteminin imzasını değiştirerek aşağıdaki özel durumu oluşturun:

    ```java
    public static void main(String[] args) throws IOException
    ```

12. Sanal cihazdaki doğrudan önyükleme yöntemini çağırmak için **Main** yöntemindeki kodu aşağıdaki kodla değiştirin:

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      System.out.println("Invoke reboot direct method");
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, null);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }
      System.out.println("Invoked reboot on device");
      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }
    ```

13. Benzetimi yapılan cihazdan bildirilen özellikleri yoklamak üzere iş parçacığını başlatmak için aşağıdaki kodu **Main** yöntemine ekleyin:

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

14. Uygulamayı durdurmanızı sağlamak için aşağıdaki kodu **Main** yöntemine ekleyin:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

15. **Trigger-reboot\src\main\java\com\mycompany\app\App.Java** dosyasını kaydedin ve kapatın.

16. **Tetikleyici-yeniden başlatma** arka uç uygulamasını oluşturun ve tüm hataları düzeltin. Komut isteminiz, **Tetikle-reboot** klasörüne gidin ve şu komutu çalıştırın:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Sanal cihaz uygulaması oluşturma

Bu bölümde, bir cihaza benzetim yapan bir Java konsol uygulaması oluşturursunuz. Uygulama, IoT Hub 'ınızdan yeniden başlatma doğrudan yöntem çağrısını dinler ve bu çağrıya anında yanıt verir. Daha sonra uygulama, yeniden başlatma işleminin bir süre sonra **tetiklenmesi** için, bildirilen bir özelliği kullanmadan önce yeniden başlatma işlemi için uyku moduna geçer.

1. **DM-Get-Started** klasöründe, komut isteminde aşağıdaki komutu kullanarak **benzetimli-Device** adlı bir Maven projesi oluşturun:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Komut istemindeki **sanal cihaz** klasörüne gidin.

3. Bir metin düzenleyicisi kullanarak, **sanal cihaz** klasöründe **polım. xml** dosyasını açın ve aşağıdaki bağımlılığı **Bağımlılıklar** düğümüne ekleyin. Bu bağımlılık, IoT Hub 'ınız ile iletişim kurmak için uygulamanızdaki IoT-Service-Client paketini kullanmanıza olanak sağlar:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > [Maven Search](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)kullanarak **IoT-Device-Client** ' ın en son sürümünü kontrol edebilirsiniz.

4. Aşağıdaki bağımlılığı **Bağımlılıklar** düğümüne ekleyin. Bu bağımlılık, bir NOP 'yi, cihaz istemci SDK 'Sı tarafından günlüğe kaydetmeyi uygulamak için kullanılan Apache [dolayısıyla slf4j](https://www.slf4j.org/) Logging façlade için yapılandırır. Bu yapılandırma isteğe bağlıdır, ancak bunu atlarsanız, uygulamayı çalıştırdığınızda konsolunda bir uyarı görebilirsiniz. Cihaz istemcisi SDK 'sında günlüğe kaydetme hakkında daha fazla bilgi için bkz [](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging) . *Java için Azure IoT cihaz SDK 'sı dosyası örnekleri* .

    ```xml
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-nop</artifactId>
      <version>1.7.28</version>
    </dependency>
    ```

5. **Bağımlılıklar** düğümünden sonra aşağıdaki **derleme** düğümünü ekleyin. Bu yapılandırma, Maven 'in uygulamayı derlemek için Java 1,8 kullanmasını söyler:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. **Pod. xml** dosyasını kaydedin ve kapatın.

7. Bir metin düzenleyicisi kullanarak **Simulated-device\src\main\java\com\mycompany\app\App.Java** kaynak dosyasını açın.

8. Aşağıdaki **içeri aktarma** deyimlerini dosyaya ekleyin:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.time.LocalDateTime;
    import java.util.Scanner;
    import java.util.Set;
    import java.util.HashSet;
    ```

9. Aşağıdaki sınıf düzeyi değişkenleri **App** sınıfına ekleyin. `{yourdeviceconnectionstring}` [IoT Hub 'ında yeni bir cihaz kaydet](#register-a-new-device-in-the-iot-hub) bölümünde not ettiğiniz cihaz bağlantı dizesiyle değiştirin:

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

10. Doğrudan yöntem durum olayları için bir geri çağırma işleyicisi uygulamak için aşağıdaki iç içe sınıfını **uygulama** sınıfına ekleyin:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

11. Device ikizi durum olayları için bir geri çağırma işleyicisi uygulamak için aşağıdaki iç içe sınıfını **uygulama** sınıfına ekleyin:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

12. Özellik olayları için bir geri çağırma işleyicisi uygulamak için aşağıdaki iç içe sınıfını **uygulama** sınıfına ekleyin:

    ```java
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

13. Cihaz yeniden başlatmanın benzetimini yapmak için bir iş parçacığı uygulamak üzere, aşağıdaki iç içe sınıfını **App** sınıfına ekleyin. İş parçacığı beş saniye boyunca uyku moduna geçer ve ardından **Lastreboot** tarafından bildirilen özelliği ayarlar:

    ```java
    protected static class RebootDeviceThread implements Runnable {
      public void run() {
        try {
          System.out.println("Rebooting...");
          Thread.sleep(5000);
          Property property = new Property("lastReboot", LocalDateTime.now());
          Set<Property> properties = new HashSet<Property>();
          properties.add(property);
          client.sendReportedProperties(properties);
          System.out.println("Rebooted");
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

14. Cihaza doğrudan yöntemini uygulamak için aşağıdaki iç içe yerleştirilmiş sınıfı **uygulama** sınıfına ekleyin. Sanal uygulama **yeniden başlatma** doğrudan yöntemine bir çağrı aldığında, çağırana bir bildirim döndürür ve sonra yeniden başlatmayı işlemek için bir iş parçacığı başlatır:

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "reboot" :
          {
            int status = METHOD_SUCCESS;
            System.out.println("Received reboot request");
            deviceMethodData = new DeviceMethodData(status, "Started reboot");
            RebootDeviceThread rebootThread = new RebootDeviceThread();
            Thread t = new Thread(rebootThread);
            t.start();
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

15. Aşağıdaki özel durumları oluşturmak için **Main** yönteminin imzasını değiştirin:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

16. Bir **Deviceclient**örneği oluşturmak için **Main** yöntemindeki kodu aşağıdaki kodla değiştirin:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

17. Doğrudan Yöntem çağrılarını dinlemeye başlamak için aşağıdaki kodu **Main** yöntemine ekleyin:

    ```java
    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Subscribed to direct methods and polling for reported properties. Waiting...");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

18. Cihaz simülatörünü kapatmak için aşağıdaki kodu **Main** yöntemine ekleyin:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

19. Simulated-device\src\main\java\com\mycompany\app\App.java dosyasını kaydedin ve kapatın.

20. **Sanal cihaz** uygulamasını oluşturun ve hataları düzeltin. Komut istemindeki **sanal cihaz** klasörüne gidin ve şu komutu çalıştırın:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Uygulamaları çalıştırma

Şimdi uygulamaları çalıştırmaya hazırsınız.

1. **Sanal cihaz** klasöründeki bir komut Isteminde, IoT Hub 'ınızdan yeniden başlatma yöntemi çağrılarını dinlemeye başlamak için aşağıdaki komutu çalıştırın:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Yeniden başlatma doğrudan Yöntem çağrılarını dinlemek için Java IoT Hub sanal cihaz uygulaması](./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png)

2. **Tetikleyici-yeniden başlatma** klasöründeki bir komut Isteminde, IoT Hub 'ınızdan sanal cihazınızdan yeniden başlatma yöntemini çağırmak için aşağıdaki komutu çalıştırın:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Yeniden başlatma doğrudan yöntemini çağırmak için Java IoT Hub hizmet uygulaması](./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png)

3. Sanal cihaz doğrudan önyükleme yöntemi çağrısına yanıt veriyor:

    ![Java IoT Hub sanal cihaz uygulaması doğrudan yöntem çağrısına yanıt verir](./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
