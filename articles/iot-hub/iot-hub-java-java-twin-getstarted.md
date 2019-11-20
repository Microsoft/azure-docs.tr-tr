---
title: Azure IoT Hub cihaz TWINS 'i kullanmaya başlama (Java) | Microsoft Docs
description: Azure IoT Hub cihaz ikimlerini kullanarak etiketler ekleyin ve ardından bir IoT Hub sorgusu kullanın. Java için Azure IoT cihaz SDK 'sını kullanarak, Etiketler ekleyen ve IoT Hub sorguyu çalıştıran bir hizmet uygulaması uygulamak üzere, Java için Azure IoT hizmeti SDK 'sını kullanın.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 4f9f4661a2c6c78438414029e803abc624a773ca
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70161970"
---
# <a name="get-started-with-device-twins-java"></a>Cihaz ikizlerini kullanmaya başlama (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Bu öğreticide, iki Java konsol uygulaması oluşturursunuz:

* Etiketler ve sorgu cihaz TWINS 'i ekleyen bir Java arka uç uygulaması olan **Add-Tags-Query**.
* IoT Hub 'ınıza bağlanan ve bildirilen bir özelliği kullanarak bağlantı koşulunu raporlayan bir Java cihaz uygulaması olan **sanal cihaz**.

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

## <a name="get-the-iot-hub-connection-string"></a>IoT Hub bağlantı dizesini al

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Hizmet uygulaması oluşturma

Bu bölümde, konum meta verilerini **Mydeviceıd**ile ilişkili IoT Hub cihaz ikizi bir etiket olarak ekleyen bir Java uygulaması oluşturacaksınız. Uygulama ilk olarak ABD 'de bulunan cihazlar için IoT Hub 'ı sorgular ve ardından hücresel ağ bağlantısı veren cihazlar için.

1. Geliştirme makinenizde **IoT-Java-ikizi-getstarted**adlı boş bir klasör oluşturun.

2. **IoT-Java-ikizi-getstarted** klasöründe, komut istemindeki aşağıdaki komutu kullanarak **Add-Tags-Query** adlı bir Maven projesi oluşturun:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. Komut istemindeki **Add-Tags-Query** klasörüne gidin.

4. Bir metin düzenleyicisi kullanarak, **Add-Tags-Query** klasöründeki **Pod. xml** dosyasını açın ve aşağıdaki bağımlılığı **Bağımlılıklar** düğümüne ekleyin. Bu bağımlılık, IoT Hub 'ınız ile iletişim kurmak için uygulamanızdaki **IoT-Service-Client** paketini kullanmanıza olanak sağlar:

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

5. **Bağımlılıklar** düğümünden sonra aşağıdaki **derleme** düğümünü ekleyin. Bu yapılandırma, Maven 'in uygulamayı derlemek için Java 1,8 kullanmasını söyler.

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

7. Bir metin düzenleyicisi kullanarak **Add-Tags-query\src\main\java\com\mycompany\app\App.Java** dosyasını açın.

8. Aşağıdaki **içeri aktarma** deyimlerini dosyaya ekleyin:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

9. Aşağıdaki sınıf düzeyi değişkenleri **App** sınıfına ekleyin. `{youriothubconnectionstring}` [IoT Hub bağlantı dizesini al](#get-the-iot-hub-connection-string)bölümünde kopyaladığınız IoT Hub bağlantı dizesiyle değiştirin.

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

10. **Ana** yöntem imzasını aşağıdaki `throws` yan tümceyi içerecek şekilde güncelleştirin:

    ```java
    public static void main( String[] args ) throws IOException
    ```

11. **Ana** yöntemdeki kodu, **devicetwin** ve **devicetwindevice** nesnelerini oluşturmak için aşağıdaki kodla değiştirin. **Devicetwin** nesnesi, IoT Hub 'ınız ile iletişimi işler. **Devicetwindevice** nesnesi, cihaz ikizi özelliklerini ve etiketlerini temsil eder:

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

12. Aşağıdaki `try/catch` bloğu **Main** yöntemine ekleyin:

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

13. Device ikizi içindeki **Region** ve **bitki** cihazı ikizi etiketlerini güncelleştirmek için, `try` bloğa aşağıdaki kodu ekleyin:

    ```java
    // Get the device twin from IoT Hub
    System.out.println("Device twin before update:");
    twinClient.getTwin(device);
    System.out.println(device);

    // Update device twin tags if they are different
    // from the existing values
    String currentTags = device.tagsToString();
    if ((!currentTags.contains("region=" + region) && !currentTags.contains("plant=" + plant))) {
      // Create the tags and attach them to the DeviceTwinDevice object
      Set<Pair> tags = new HashSet<Pair>();
      tags.add(new Pair("region", region));
      tags.add(new Pair("plant", plant));
      device.setTags(tags);

      // Update the device twin in IoT Hub
      System.out.println("Updating device twin");
      twinClient.updateTwin(device);
    }

    // Retrieve the device twin with the tag values from IoT Hub
    System.out.println("Device twin after update:");
    twinClient.getTwin(device);
    System.out.println(device);
    ```

14. IoT Hub 'ında cihaz ikizlerini sorgulamak için, önceki adımda eklediğiniz koddan sonra aşağıdaki kodu `try` bloğa ekleyin. Kod iki sorgu çalıştırır. Her sorgu en fazla 100 cihaz döndürür.

    ```java
    // Query the device twins in IoT Hub
    System.out.println("Devices in Redmond:");

    // Construct the query
    SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43'", null);

    // Run the query, returning a maximum of 100 devices
    Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }

    System.out.println("Devices in Redmond using a cellular network:");

    // Construct the query
    sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43' AND properties.reported.connectivityType = 'cellular'", null);

    // Run the query, returning a maximum of 100 devices
    twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 3);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }
    ```

15. **Add-Tags-query\src\main\java\com\mycompany\app\App.Java** dosyasını Kaydet ve Kapat

16. **Add-Tags-Query** uygulamasını oluşturun ve hataları düzeltin. Komut istemindeki **Add-Tags-Query** klasörüne gidin ve şu komutu çalıştırın:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Cihaz uygulaması oluşturma

Bu bölümde, IoT Hub gönderilen bildirilen bir özellik değerini ayarlayan bir Java konsol uygulaması oluşturursunuz.

1. **IoT-Java-ikizi-getstarted** klasöründe, komut istemindeki aşağıdaki komutu kullanarak **benzetimli-Device** adlı bir Maven projesi oluşturun:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Komut istemindeki **sanal cihaz** klasörüne gidin.

3. Bir metin düzenleyicisi kullanarak, **sanal cihaz** klasöründe **polım. xml** dosyasını açın ve aşağıdaki bağımlılıkları **Bağımlılıklar** düğümüne ekleyin. Bu bağımlılık, IoT Hub 'ınız ile iletişim kurmak için uygulamanızdaki **IoT-Device-Client** paketini kullanmanıza olanak sağlar.

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

7. Bir metin düzenleyicisi kullanarak **Simulated-device\src\main\java\com\mycompany\app\App.Java** dosyasını açın.

8. Aşağıdaki **içeri aktarma** deyimlerini dosyaya ekleyin:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

9. Aşağıdaki sınıf düzeyi değişkenleri **App** sınıfına ekleyin. `{yourdeviceconnectionstring}` [Yeni bir cihazı IoT Hub 'ına kaydetmek](#register-a-new-device-in-the-iot-hub)için kopyaladığınız cihaz bağlantı dizesiyle değiştirin.

    ```java
    private static String connString = "{yourdeviceconnectionstring}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    ```

    Bu örnek uygulama, bir **DeviceClient** nesnesi örneğini oluşturduğunda **prokotol** değişkenini kullanır.

10. İkizi güncelleştirmeleriyle ilgili bilgileri yazdırmak için **uygulama** sınıfına aşağıdaki yöntemi ekleyin:

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
          System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
      }
    ```

11. **Main** yöntemindeki kodu aşağıdaki kodla değiştirin:

    * IoT Hub ile iletişim kurmak için bir cihaz istemcisi oluşturun.

    * Cihaz ikizi özelliklerini depolamak için bir **cihaz** nesnesi oluşturun.

    ```java
    DeviceClient client = new DeviceClient(connString, protocol);

    // Create a Device object to store the device twin properties
    Device dataCollector = new Device() {
      // Print details when a property value changes
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context) {
        System.out.println(propertyKey + " changed to " + propertyValue);
      }
    };
    ```

12. Aşağıdaki kodu **Main** yöntemine ekleyerek **connectivitytype** tarafından bildirilen bir özellik oluşturun ve IoT Hub gönderin:

    ```java
    try {
      // Open the DeviceClient and start the device twin services.
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);

      // Create a reported property and send it to your IoT hub.
      dataCollector.setReportedProp(new Property("connectivityType", "cellular"));
      client.sendReportedProperties(dataCollector.getReportedProp());
    }
    catch (Exception e) {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

13. **Main** yönteminin sonuna aşağıdaki kodu ekleyin. **ENTER** tuşunun beklenmesi IoT Hub cihaz ikizi işlemlerinin durumunu raporlamak için zaman sağlar.

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

14. **main** yönteminin imzasını, aşağıda gösterilen özel durumları içerecek şekilde değiştirin:

     ```java
     public static void main(String[] args) throws URISyntaxException, IOException
     ```

15. **Simulated-device\src\main\java\com\mycompany\app\App.Java** dosyasını kaydedin ve kapatın.

16. **Sanal cihaz** uygulamasını oluşturun ve hataları düzeltin. Komut istemindeki **sanal cihaz** klasörüne gidin ve şu komutu çalıştırın:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Uygulamaları çalıştırma

Artık konsol uygulamalarını çalıştırmaya hazırsınız.

1. Add-Tags-Query klasöründeki bir komut isteminde, **Add-Tags-Query** Service uygulamasını çalıştırmak için aşağıdaki komutu çalıştırın:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Etiket değerlerini güncelleştirmek ve cihaz sorgularını çalıştırmak için Java IoT Hub hizmet uygulaması](./media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    Cihaza ve bölgeye ikizi eklenmiş **tesis** ve **bölge** etiketlerini görebilirsiniz. İlk sorgu cihazınızı döndürür, ancak ikincisi değildir.

2. **Sanal cihaz** klasöründeki bir komut Isteminde, **connectivitytype** bildirilen özelliğini cihaz ikizi eklemek için aşağıdaki komutu çalıştırın:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Cihaz istemcisi * * connectivityType * * bildirilen özelliğini ekler](./media/iot-hub-java-java-twin-getstarted/device-app-1.png)

3. Add-Tags-Query klasöründeki bir komut isteminde, **Add-Tags-Query** Service uygulamasını ikinci bir kez çalıştırmak için aşağıdaki komutu çalıştırın:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Etiket değerlerini güncelleştirmek ve cihaz sorgularını çalıştırmak için Java IoT Hub hizmet uygulaması](./media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    Cihazınız **Connectivitytype** özelliğini IoT Hub olarak gönderdiyse, ikinci sorgu cihazınızı geri döndürür.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure portalında yeni bir IoT hub'ı yapılandırdınız ve ardından IoT hub'ının kimlik kayıt defterinde bir cihaz kimliği oluşturdunuz. Cihaz meta verilerini arka uç uygulamasından Etiketler olarak eklediniz ve cihaz ikizi cihaz bağlantısı bilgilerini raporlamak için bir cihaz uygulaması yazdı. Ayrıca, SQL benzeri IoT Hub sorgu dilini kullanarak Device ikizi bilgilerini sorgulama hakkında bilgi edinirsiniz.

Aşağıdakilerin nasıl yapılacağını öğrenmek için aşağıdaki kaynakları kullanın:

* [IoT Hub ile çalışmaya başlama](quickstart-send-telemetry-java.md) öğreticisini kullanarak cihazlardan telemetri gönderin.

* Cihazları etkileşimli olarak (kullanıcı denetimli bir uygulamadan bir fanı açmak gibi) [doğrudan Yöntemler](quickstart-control-device-java.md) öğreticisini kullanarak kontrol edin.
