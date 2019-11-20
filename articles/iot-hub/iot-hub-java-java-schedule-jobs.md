---
title: İşleri Azure IoT Hub zamanlayın (Java) | Microsoft Docs
description: Bir Azure IoT Hub işini, doğrudan bir yöntemi çağırmak ve birden çok cihazda istenen bir özelliği ayarlamak için zamanlama. Sanal cihaz uygulamalarını ve bu işi çalıştırmak için bir hizmet uygulaması uygulamak üzere Java için Azure IoT hizmeti SDK 'sını uygulamak üzere Java için Azure IoT cihaz SDK 'sını kullanırsınız.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/16/2019
ms.openlocfilehash: bbb78dcd36ec986cefc1d57e01396f285a6b30dd
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70161944"
---
# <a name="schedule-and-broadcast-jobs-java"></a>İşleri zamanlama ve yayınlama (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Milyonlarca cihazı güncelleştiren işleri zamanlamak ve izlemek için Azure IoT Hub kullanın. İşleri şu şekilde kullan:

* İstenen özellikleri güncelleştirme
* Etiketleri Güncelleştir
* Doğrudan metotları çağır

Bir iş bu eylemlerden birini sarmalar ve yürütmeyi bir dizi cihaza karşı izler. Bir Device ikizi sorgusu, iş tarafından yürütülen cihaz kümesini tanımlar. Örneğin, bir arka uç uygulaması, cihazları yeniden çalıştıran 10.000 cihazda doğrudan bir yöntemi çağırmak için bir iş kullanabilir. Bir cihaz ikizi sorgusu olan cihaz kümesini belirtirsiniz ve işi gelecekteki bir zamanda çalışacak şekilde zamanlayabilirsiniz. Her bir cihazın yeniden başlatma doğrudan metodunu alıp yürütmesi sırasında iş ilerleme durumunu izler.

Bu yeteneklerin her biri hakkında daha fazla bilgi edinmek için bkz.:

* Cihaz ikizi ve özellikleri: [Cihaz ikizlerini kullanmaya başlama](iot-hub-java-java-twin-getstarted.md)

* Doğrudan Yöntemler: [IoT Hub Geliştirici Kılavuzu-doğrudan Yöntemler](iot-hub-devguide-direct-methods.md) ve [öğretici: Doğrudan Yöntemler kullanma](quickstart-control-device-java.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Bu öğretici şunların nasıl yapıldığını gösterir:

* **Lockkapısı**adlı doğrudan bir yöntemi uygulayan bir cihaz uygulaması oluşturun. Ayrıca, cihaz uygulaması, arka uç uygulamasından istenen özellik değişikliklerini de alır.

* Birden çok cihazda **Lockkapısı** doğrudan yöntemini çağırmak için bir iş oluşturan bir arka uç uygulaması oluşturun. Başka bir iş, istenen özellik güncelleştirmelerini birden çok cihaza gönderir.

Bu öğreticinin sonunda bir Java konsol cihaz uygulamanız ve bir Java Konsolu arka uç uygulamanız vardır:

IoT Hub 'ınıza bağlanan, **Lockkapısı** doğrudan yöntemini uygulayan ve istenen özellik değişikliklerini işleyen **sanal cihaz** .

**Schedule-** **lockkapısı** doğrudan yöntemini çağırmak ve cihazı ikizi istenen özellikleri birden çok cihazda güncelleştirmek için işleri kullanan işler.

> [!NOTE]
> [Azure IoT SDK 'ları](iot-hub-devguide-sdks.md) makalesi, hem cihaz hem de arka uç uygulamaları oluşturmak Için kullanabileceğiniz Azure IoT SDK 'ları hakkında bilgi sağlar.

## <a name="prerequisites"></a>Önkoşullar

* [Java SE Development Kit 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable). JDK 8 için karşıdan yüklemeye ulaşmak üzere **uzun süreli destek** altında **Java 8** ' i seçtiğinizden emin olun.

* [Maven 3](https://maven.apache.org/download.cgi)

* Etkin bir Azure hesabı. (Hesabınız yoksa yalnızca birkaç dakika içinde [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturabilirsiniz.)

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub 'a yeni bir cihaz kaydetme

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

IoT Hub 'ınıza bir cihaz eklemek için [Azure CLI Için IoT uzantısı](https://github.com/Azure/azure-iot-cli-extension) aracını da kullanabilirsiniz.

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub bağlantı dizesini al

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="create-the-service-app"></a>Hizmet uygulaması oluşturma

Bu bölümde, aşağıdakileri yapmak için işleri kullanan bir Java konsol uygulaması oluşturursunuz:

* **Kilit kapısı** doğrudan metodunu birden çok cihazda çağırın.

* İstenen özellikleri birden çok cihaza gönderin.

Uygulamayı oluşturmak için:

1. Geliştirme makinenizde **IoT-Java-Schedule-Jobs**adlı boş bir klasör oluşturun.

2. **IoT-Java-Schedule-Jobs** klasöründe, komut istemindeki aşağıdaki komutu kullanarak **Schedule-Jobs** adlı bir Maven projesi oluşturun. Bunun tek ve uzun bir komut olduğunu unutmayın:

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

3. Komut istemindeki **zamanlama işleri** klasörüne gidin.

4. Bir metin düzenleyicisi kullanarak, **Schedule-Jobs** klasöründeki **Pod. xml** dosyasını açın ve aşağıdaki bağımlılığı **Bağımlılıklar** düğümüne ekleyin. Bu bağımlılık, IoT Hub 'ınız ile iletişim kurmak için uygulamanızdaki **IoT-Service-Client** paketini kullanmanıza olanak sağlar:

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

7. Bir metin düzenleyicisi kullanarak **Schedule-jobs\src\main\java\com\mycompany\app\App.Java** dosyasını açın.

8. Aşağıdaki **içeri aktarma** deyimlerini dosyaya ekleyin:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Pair;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Query;
    import com.microsoft.azure.sdk.iot.service.devicetwin.SqlQuery;
    import com.microsoft.azure.sdk.iot.service.jobs.JobClient;
    import com.microsoft.azure.sdk.iot.service.jobs.JobResult;
    import com.microsoft.azure.sdk.iot.service.jobs.JobStatus;

    import java.util.Date;
    import java.time.Instant;
    import java.util.HashSet;
    import java.util.Set;
    import java.util.UUID;
    ```

9. Aşağıdaki sınıf düzeyi değişkenleri **App** sınıfına ekleyin. `{youriothubconnectionstring}` [IoT Hub bağlantı dizesini al](#get-the-iot-hub-connection-string)bölümünde daha önce kopyaladığınız IoT Hub bağlantı dizeniz ile değiştirin:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

10. Cihaz ikizi **Yapı** ve **kat** istenen özelliklerini güncelleştirmek üzere bir iş zamanlamak için **uygulama** sınıfına aşağıdaki yöntemi ekleyin:

    ```java
    private static JobResult scheduleJobSetDesiredProperties(JobClient jobClient, String jobId) {
      DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
      Set<Pair> desiredProperties = new HashSet<Pair>();
      desiredProperties.add(new Pair("Building", 43));
      desiredProperties.add(new Pair("Floor", 3));
      twin.setDesiredProperties(desiredProperties);
      // Optimistic concurrency control
      twin.setETag("*");

      // Schedule the update twin job to run now
      // against a single device
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleUpdateTwin(jobId, 
          "deviceId='" + deviceId + "'",
          twin,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling desired properties job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    }
    ```

11. **Lockkapısı** yöntemini çağırmak üzere bir iş zamanlamak için aşağıdaki yöntemi **uygulama** sınıfına ekleyin:

    ```java
    private static JobResult scheduleJobCallDirectMethod(JobClient jobClient, String jobId) {
      // Schedule a job now to call the lockDoor direct method
      // against a single device. Response and connection
      // timeouts are set to 5 seconds.
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleDeviceMethod(jobId,
          "deviceId='" + deviceId + "'",
          "lockDoor",
          5L, 5L, null,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling direct method job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    };
    ```

12. Bir işi izlemek için aşağıdaki yöntemi **uygulama** sınıfına ekleyin:

    ```java
    private static void monitorJob(JobClient jobClient, String jobId) {
      try {
        JobResult jobResult = jobClient.getJob(jobId);
        if(jobResult == null)
        {
          System.out.println("No JobResult for: " + jobId);
          return;
        }
        // Check the job result until it's completed
        while(jobResult.getJobStatus() != JobStatus.completed)
        {
          Thread.sleep(100);
          jobResult = jobClient.getJob(jobId);
          System.out.println("Status " + jobResult.getJobStatus() + " for job " + jobId);
        }
        System.out.println("Final status " + jobResult.getJobStatus() + " for job " + jobId);
      } catch (Exception e) {
        System.out.println("Exception monitoring job: " + jobId);
        System.out.println(e.getMessage());
        return;
      }
    }
    ```

13. Çalıştırdığınız işlerin ayrıntılarını sorgulamak için aşağıdaki yöntemi ekleyin:

    ```java
    private static void queryDeviceJobs(JobClient jobClient, String start) throws Exception {
      System.out.println("\nQuery device jobs since " + start);

      // Create a jobs query using the time the jobs started
      Query deviceJobQuery = jobClient
          .queryDeviceJob(SqlQuery.createSqlQuery("*", SqlQuery.FromType.JOBS, "devices.jobs.startTimeUtc > '" + start + "'", null).getQuery());

      // Iterate over the list of jobs and print the details
      while (jobClient.hasNextJob(deviceJobQuery)) {
        System.out.println(jobClient.getNextJob(deviceJobQuery));
      }
    }
    ```

14. **Ana** yöntem imzasını aşağıdaki `throws` yan tümceyi içerecek şekilde güncelleştirin:

    ```java
    public static void main( String[] args ) throws Exception
    ```

15. İki işi sırayla çalıştırmak ve izlemek için **ana** yöntemdeki kodu aşağıdaki kodla değiştirin:

    ```java
    // Record the start time
    String start = Instant.now().toString();

    // Create JobClient
    JobClient jobClient = JobClient.createFromConnectionString(iotHubConnectionString);
    System.out.println("JobClient created with success");

    // Schedule twin job desired properties
    // Maximum concurrent jobs is 1 for Free and S1 tiers
    String desiredPropertiesJobId = "DPCMD" + UUID.randomUUID();
    scheduleJobSetDesiredProperties(jobClient, desiredPropertiesJobId);
    monitorJob(jobClient, desiredPropertiesJobId);

    // Schedule twin job direct method
    String directMethodJobId = "DMCMD" + UUID.randomUUID();
    scheduleJobCallDirectMethod(jobClient, directMethodJobId);
    monitorJob(jobClient, directMethodJobId);

    // Run a query to show the job detail
    queryDeviceJobs(jobClient, start);

    System.out.println("Shutting down schedule-jobs app");
    ```

16. **Schedule-jobs\src\main\java\com\mycompany\app\App.Java** dosyasını Kaydet ve Kapat

17. **Schedule-Jobs** uygulamasını oluşturun ve hataları düzeltin. Komut istemindeki **Schedule-Jobs** klasörüne gidin ve şu komutu çalıştırın:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Cihaz uygulaması oluşturma

Bu bölümde, IoT Hub gönderilen istenen özellikleri işleyen ve doğrudan yöntem çağrısını uygulayan bir Java konsol uygulaması oluşturursunuz.

1. **IoT-Java-Schedule-Jobs** klasöründe, komut istemindeki aşağıdaki komutu kullanarak, **sanal cihaz** adlı bir Maven projesi oluşturun. Bunun tek ve uzun bir komut olduğunu unutmayın:

   ```cmd/sh
   mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

2. Komut istemindeki **sanal cihaz** klasörüne gidin.

3. Bir metin düzenleyicisi kullanarak, **sanal cihaz** klasöründe **polım. xml** dosyasını açın ve aşağıdaki bağımlılıkları **Bağımlılıklar** düğümüne ekleyin. Bu bağımlılık, IoT Hub 'ınız ile iletişim kurmak için uygulamanızdaki **IoT-Device-Client** paketini kullanmanıza olanak sağlar:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.17.5</version>
    </dependency>
    ```

    > [!NOTE]
    > [Maven Search](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)kullanarak **IoT-Device-Client** ' ın en son sürümünü kontrol edebilirsiniz.

4. Aşağıdaki bağımlılığı **Bağımlılıklar** düğümüne ekleyin. Bu bağımlılık, bir NOP 'yi, cihaz istemci SDK 'Sı tarafından günlüğe kaydetmeyi uygulamak için kullanılan Apache [dolayısıyla slf4j](https://www.slf4j.org/) Logging façlade için yapılandırır. Bu yapılandırma isteğe bağlıdır, ancak bunu atlarsanız, uygulamayı çalıştırdığınızda konsolda bir uyarı görebilirsiniz. Cihaz istemcisi SDK 'sında günlüğe kaydetme hakkında daha fazla bilgi için bkz [](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md#logging). *Java için Azure IoT cihaz SDK 'sı dosyası örnekleri* .

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

7. Bir metin düzenleyicisi kullanarak **Simulated-device\src\main\java\com\mycompany\app\App.Java** dosyasını açın.

8. Aşağıdaki **içeri aktarma** deyimlerini dosyaya ekleyin:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

9. Aşağıdaki sınıf düzeyi değişkenleri **App** sınıfına ekleyin. Daha `{yourdeviceconnectionstring}` önce, [IoT Hub 'da yeni bir cihaz kaydet](#register-a-new-device-in-the-iot-hub) bölümünde kopyaladığınız cihaz bağlantı dizesiyle değiştirin:

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Bu örnek uygulama, bir **DeviceClient** nesnesi örneğini oluşturduğunda **prokotol** değişkenini kullanır.

10. Cihaza cihaz ikizi bildirimleri yazdırmak için, **uygulama** sınıfına aşağıdaki iç içe geçmiş sınıfı ekleyin:

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

11. Konsola doğrudan yöntem bildirimleri yazdırmak için aşağıdaki iç içe sınıfı **uygulama** sınıfına ekleyin:

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

12. IoT Hub doğrudan Yöntem çağrılarını işlemek için, aşağıdaki iç içe sınıfı **uygulama** sınıfına ekleyin:

    ```java
    // Handler for direct method calls from IoT Hub
    protected static class DirectMethodCallback
        implements DeviceMethodCallback {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context) {
        DeviceMethodData deviceMethodData;
        switch (methodName) {
          case "lockDoor": {
            System.out.println("Executing direct method: " + methodName);
            deviceMethodData = new DeviceMethodData(METHOD_SUCCESS, "Executed direct method " + methodName);
            break;
          }
          default: {
            deviceMethodData = new DeviceMethodData(METHOD_NOT_DEFINED, "Not defined direct method " + methodName);
          }
        }
        // Notify IoT Hub of result
        return deviceMethodData;
      }
    }
    ```

13. **Ana** yöntem imzasını aşağıdaki `throws` yan tümceyi içerecek şekilde güncelleştirin:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

14. **Main** yöntemindeki kodu aşağıdaki kodla değiştirin:
    * IoT Hub ile iletişim kurmak için bir cihaz istemcisi oluşturun.
    * Cihaz ikizi özelliklerini depolamak için bir **cihaz** nesnesi oluşturun.

    ```java
    // Create a device client
    DeviceClient client = new DeviceClient(connString, protocol);

    // An object to manage device twin desired and reported properties
    Device dataCollector = new Device() {
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context)
      {
        System.out.println("Received desired property change: " + propertyKey + " " + propertyValue);
      }
    };
    ```

15. Cihaz istemci hizmetlerini başlatmak için aşağıdaki kodu **Main** yöntemine ekleyin:

    ```java
    try {
      // Open the DeviceClient
      // Start the device twin services
      // Subscribe to direct method calls
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
    } catch (Exception e) {
      System.out.println("Exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

16. Kullanıcının kapanmadan önce **ENTER** tuşuna basması için, **Main** yönteminin sonuna aşağıdaki kodu ekleyin:

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

17. **Simulated-device\src\main\java\com\mycompany\app\App.Java** dosyasını kaydedin ve kapatın.

18. **Sanal cihaz** uygulamasını oluşturun ve hataları düzeltin. Komut istemindeki **sanal cihaz** klasörüne gidin ve şu komutu çalıştırın:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Uygulamaları çalıştırma

Artık konsol uygulamalarını çalıştırmaya hazırsınız.

1. **Sanal cihaz** klasöründeki bir komut isteminde, istenen özellik değişiklikleri ve doğrudan Yöntem çağrıları için dinleme yapan cihaz uygulamasını başlatmak için aşağıdaki komutu çalıştırın:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Cihaz istemcisi başlar](./media/iot-hub-java-java-schedule-jobs/device-app-1.png)

2. Bir komut isteminde `schedule-jobs` , iki işi çalıştırmak için **Schedule-Jobs** hizmet uygulamasını çalıştırmak için aşağıdaki komutu çalıştırın. İlk olarak, istenen özellik değerlerini ayarlar, ikincisi doğrudan yöntemini çağırır:

   ```cmd\sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Java IoT Hub hizmet uygulaması iki iş oluşturuyor](./media/iot-hub-java-java-schedule-jobs/service-app-1.png)

3. Cihaz uygulaması, istenen özellik değişikliğini ve doğrudan yöntem çağrısını işler:

   ![Cihaz istemcisi değişikliklere yanıt veriyor](./media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir cihaza doğrudan yöntem zamanlamak ve Device ikizi 'in özelliklerinin güncelleştirilmesi için bir iş kullandınız.

Aşağıdakilerin nasıl yapılacağını öğrenmek için aşağıdaki kaynakları kullanın:

* [IoT Hub ile çalışmaya başlama](quickstart-send-telemetry-java.md) öğreticisini kullanarak cihazlardan telemetri gönderin.

* Cihazları etkileşimli olarak (kullanıcı denetimli bir uygulamadan bir fanı açmak gibi) [doğrudan Yöntemler öğreticisi kullanın](quickstart-control-device-java.md) . s
