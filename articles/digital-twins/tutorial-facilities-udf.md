---
title: 'Öğretici: Azure dijital TWINS ile bir alanı Izleme'
description: Bu öğreticideki adımları kullanarak, uzamsal kaynaklarınızı sağlamayı ve Azure Digital TWINS ile çalışma koşullarını izlemeyi öğrenin.
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 11/13/2019
ms.openlocfilehash: 6c9403f8355d2f842226c9c7257803edd3215829
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74107489"
---
# <a name="tutorial-provision-your-building-and-monitor-working-conditions-with-azure-digital-twins-preview"></a>Öğretici: Azure dijital TWINS önizlemesi ile derleme ve izleme çalışma koşullarınızı sağlama

Bu öğreticide, Azure Digital TWINS önizlemesi 'ni kullanarak istediğiniz sıcaklık koşulları ve rahatlık düzeyi için boşlukların nasıl izleneceği gösterilmektedir. [Örnek yapıınızı](tutorial-facilities-setup.md)yapılandırdıktan sonra, bu öğreticideki adımları kullanarak, oluşturma ve algılayıcı verilerinize özel işlevler çalıştırma sağlayabilirsiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * İzlenecek koşulları tanımlayın.
> * Kullanıcı tanımlı bir işlev (UDF) oluşturun.
> * Algılayıcı verilerinin benzetimini yapın.
> * Kullanıcı tanımlı bir işlevin sonuçlarını alın.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticide, [Azure dijital TWINS kurulumunuzu tamamladığınız](tutorial-facilities-setup.md)varsayılmaktadır. Devam etmeden önce aşağıdakilere sahip olduğunuzdan emin olun:

- Bir [Azure hesabı](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Çalışan bir Digital Twins örneği. 
- Çalışma makinenize indirilmiş ve ayıklanmış [Digital Twins C# örnekleri](https://github.com/Azure-Samples/digital-twins-samples-csharp). 
- Örnek derlemek ve çalıştırmak için geliştirme makinenizde [sürüm 2.1.403 veya sonraki bir sürümü .NET Core SDK](https://www.microsoft.com/net/download) . Doğru sürümün yüklendiğini doğrulamak için `dotnet --version` çalıştırın. 
- Örnek kodu incelemek için [Visual Studio Code](https://code.visualstudio.com/). 

> [!TIP]
> Yeni bir örnek sağlıyorsanız benzersiz bir dijital TWINS örnek adı kullanın.

## <a name="define-conditions-to-monitor"></a>İzleme koşullarını tanımlama

*Eşleme*olarak adlandırılan cihaz veya algılayıcı verilerinde izlenecek belirli koşullar kümesini tanımlayabilirsiniz. Ardından, *Kullanıcı tanımlı işlevler*adlı işlevleri tanımlayabilirsiniz. Kullanıcı tanımlı işlevler, boşluklarla ve cihazlarınızdan gelen veriler üzerinde özel mantık yürütür ve bu durum, Matchers tarafından belirtilen koşullardır. Daha fazla bilgi için, [veri işleme ve Kullanıcı tanımlı işlevleri](concepts-user-defined-functions.md)okuyun. 

**İs-QuickStart** örnek projesinden, Visual Studio Code içinde **Src\actions\provisionsample.exe** adlı dosyayı açın. **matchers** türü ile başlayan bölümü bulun. Bu tür altındaki her giriş, belirtilen **ada**sahip bir Eşleştiricisi oluşturur. Eşleştiricisi, **datatypevalue**türünde bir algılayıcı izler. Birkaç algılayıcı içeren bir **cihazlar** düğümüne sahip olan, *odak odası a1*adlı alanla nasıl ilişkili olduğuna dikkat edin. Bu sensörlerden birini izleyecek bir eşleştirici sağlamak için, **Datatypevalue değerinin** algılayıcı **veri türüyle**eşleştiğinden emin olun. 

Aşağıdaki eşleştiriciyi mevcut eşleştiriciler altına ekleyin. Anahtarların hizalandığından ve boşlukların sekmeyle değiştirilmediğinden emin olun. Bu satırlar ayrıca, *Provisionsample. YAML* dosyasında, açıklamalı çizgiler olarak da mevcuttur. Her satırın önündeki `#` karakterini kaldırarak bu açıklamaları özelleştirebilirsiniz.

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

Bu Eşleştiricisi [, ilk öğreticide](tutorial-facilities-setup.md)eklediğiniz `SAMPLE_SENSOR_TEMPERATURE` algılayıcıyı izler. 

## <a name="create-a-user-defined-function"></a>Kullanıcı tanımlı işlev oluşturma

Algılayıcı verilerinizin işlenmesini özelleştirmek için Kullanıcı tanımlı işlevleri kullanabilirsiniz. Bunlar, eşleştiriciler tarafından açıklanan belirli koşullar gerçekleştiğinde, Azure dijital TWINS Örneğinizde çalışabilecek özel JavaScript kodlarıdır. İzlemek istediğiniz her algılayıcı için eşleştiriciler ve Kullanıcı tanımlı işlevler oluşturabilirsiniz. Daha fazla bilgi için, [veri işleme ve Kullanıcı tanımlı işlevleri](concepts-user-defined-functions.md)okuyun. 

Örnek *provisionsample. YAML* dosyasında, **UserDefinedFunctions**türünde başlayan bir bölüm bulun. Bu bölüm, belirli bir **ada**sahip kullanıcı tanımlı bir işlev sağlar. Bu UDF, **Matchernames**altındaki eşleştiriciler listesi üzerinde çalışır. UDF için kendi JavaScript dosyanızı **script** bölümünde sağlayabilirsiniz.

Ayrıca **roleassignments** adlı bölüme de dikkat edin. Kullanıcı tanımlı işleve alan yöneticisi rolünü atar. Bu rol, sağlanan boşlukların herhangi birinden gelen olaylara erişmesine izin verir. 

1. `matcherNames`provisionSample.yaml*adlı dosyanın* düğümüne aşağıdaki satırı ekleyerek veya var olan satırın açıklamasını kaldırarak UDF'yi sıcaklık eşleştiricisini içerecek şekilde yapılandırın:

    ```yaml
            - Matcher Temperature
    ```

1. Düzenleyicinizde **Src\actions\userdefinedfunctions\kullanılabilirliği bility.exe** dosyasını açın. Bu, *Provisionsample. YAML* **komut dosyası** öğesinde başvurulan dosyadır. Bu dosyadaki Kullanıcı tanımlı işlev, odada hiçbir hareket algılanmadığında ve karbon dioksit seviyelerinin 1.000 ppm 'dan daha düşük olduğu durumlar için arama yapar. 

   Sıcaklık ve diğer koşulları izlemek için JavaScript dosyasını değiştirin. Odada hiçbir hareket algılanmadığında koşullara bakmak için aşağıdaki kod satırlarını ekleyin; karbon dioksit seviyelerinin 1.000 ppm ve sıcaklığın 78 derece Fahrenbir süre altında olması gerekir.

   > [!NOTE]
   > Bu bölüm, Kullanıcı tanımlı bir işlevi yazmanın tek bir yolu hakkında bilgi edinmek için *Src\actions\userdefinedfunctions\kullanılabilirliği bility.exe* dosyasını değiştirir. Ancak, [src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) dosyasını doğrudan kurulumda kullanmayı seçebilirsiniz. Bu dosya, öğretici için gerekli olan tüm değişikliklere sahiptir. Bunun yerine bu dosyayı kullanırsanız, [Src\actions\provisionsample.exe](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml)yolunda **betik** anahtarı için doğru dosya adını kullandığınızdan emin olun.

    a. Dosyanın en üstünde, `// Add your sensor type here` açıklamasının altına sıcaklık için şu satırları ekleyin:

    ```JavaScript
        var temperatureType = "Temperature";
        var temperatureThreshold = 78;
    ```

    b. `var motionSensor`tanımlayan deyimden sonra, açıklamanın `// Add your sensor variable here`altına aşağıdaki satırları ekleyin:

     ```JavaScript
        var temperatureSensor = otherSensors.find(function(element) {
            return element.DataType === temperatureType;
        });
    ```

    c. `var carbonDioxideValue`tanımlayan deyimden sonra, açıklamanın `// Add your sensor latest value here`altına aşağıdaki satırı ekleyin:

    ```JavaScript
        var temperatureValue = getFloatValue(temperatureSensor.Value().Value);
    ```

    d. Şu kod satırlarını `// Modify this line to monitor your sensor value` açıklamasının altından kaldırın:

     ```JavaScript
        if(carbonDioxideValue === null || motionValue === null) {
            sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide or motion are null, returning");
            return;
        }
    ```

    Bunları şu satırlarla değiştirin:

    ```JavaScript
        if(carbonDioxideValue === null || motionValue === null || temperatureValue === null){
            sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide, motion, or temperature are null, returning");
            return;
        }
    ```

    e. Şu kod satırlarını `// Modify these lines as per your sensor` açıklamasının altından kaldırın:

    ```JavaScript
        var availableFresh = "Room is available and air is fresh";
        var noAvailableOrFresh = "Room is not available or air quality is poor";
    ```

    Bunları şu satırlarla değiştirin:

    ```JavaScript
        var alert = "Room with fresh air and comfortable temperature is available.";
        var noAlert = "Either room is occupied, or working conditions are not right.";
    ```

    f. Şu *if-else* kod bloğunu `// Modify this code block for your sensor` açıklamasının altından kaldırın:

    ```JavaScript
        // If carbonDioxide less than threshold and no presence in the room => log, notify and set parent space computed value
        if(carbonDioxideValue < carbonDioxideThreshold && !presence) {
            log(`${availableFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, availableFresh);
        }
        else {
            log(`${noAvailableOrFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAvailableOrFresh);

            // Set up custom notification for poor air quality
            parentSpace.Notify(JSON.stringify(noAvailableOrFresh));
        }
    ```

    Sonrasında şu *if-else* bloğuyla değiştirin:

    ```JavaScript
        // If sensor values are within range and room is available
        if(carbonDioxideValue < carbonDioxideThreshold && temperatureValue < temperatureThreshold && !presence) {
            log(`${alert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

            // log, notify and set parent space computed value
            setSpaceValue(parentSpace.Id, spaceAvailFresh, alert);

            // Set up notification for this alert
            parentSpace.Notify(JSON.stringify(alert));
        }
        else {
            log(`${noAlert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

            // log, notify and set parent space computed value
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAlert);
        }
    ```

    Değiştirilen UDF, bir odanın kullanılabilir durumda olması, karbondioksit ve sıcaklık sınırlarının kabul edilen sınırlar içinde olması durumunu izler. Bu koşul yerine getirildiğinde `parentSpace.Notify(JSON.stringify(alert));` deyimiyle bir bildirim oluşturur. Sağlanan koşuldan bağımsız olarak izlenen alanın değerini ayarlayacak ve aşağıdaki iletiyi görüntüleyecektir.

    g. Dosyayı kaydedin.

1. Bir komut penceresi açın ve **Occupancy-quickstart\src**klasörüne gidin. Uzamsal zeka grafınızı ve Kullanıcı tanımlı işlevinizi sağlamak için aşağıdaki komutu çalıştırın:

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   > [!IMPORTANT]
   > Dijital TWINS yönetim API 'nize yetkisiz erişimi engellemek için, **doluluk-hızlı başlangıç** uygulaması Azure hesabı kimlik bilgilerinizle oturum açmanızı gerektirir. Kimlik bilgilerinizi kısa bir süre kaydeder, bu nedenle her çalıştırdığınızda oturum açmanız gerekmez. Bu program ilk kez çalıştığında ve bu tarihten sonra kaydettiğiniz kimlik bilgilerinizin süresi dolduğunda, uygulama sizi oturum açma sayfasına yönlendirir ve bu sayfaya girilecek oturuma özgü bir kod verir. Azure hesabınızda oturum açmak için yönergeleri izleyin.

1. Hesabınız doğrulandıktan sonra uygulama, *provisionsample. YAML*içinde yapılandırılmış şekilde örnek bir uzamsal grafik oluşturmaya başlar. Sağlama bitene kadar bekleyin. İşlem birkaç dakika sürer. Bundan sonra, komut penceresindeki iletileri gözlemleyin ve uzamsal grafiklerinizin nasıl oluşturulduğuna dikkat edin. Uygulamanın kök düğümde veya `Venue`bir IoT Hub 'ı nasıl oluşturduğunu fark edin.

1. Komut penceresindeki çıktıdan, `Devices` bölümünün altındaki `ConnectionString`değerini panonuza kopyalayın. Sonraki bölümde cihaz bağlantısının benzetimini yapmak için bu değere ihtiyacınız olacaktır.

    [![sağlama örneği](./media/tutorial-facilities-udf/run-provision-sample.png)](./media/tutorial-facilities-udf/run-provision-sample.png#lightbox)

> [!TIP]
> Hatanın ortasında "bir iş parçacığı çıkışı veya uygulama isteği nedeniyle g/ç işlemi iptal edildi" hatasıyla benzer bir hata iletisi alırsanız, komutu yeniden çalıştırmayı deneyin. Bu durum, HTTP istemcisinin bir ağ sorunundan zaman aşımına uğramasından kaynaklanabilir.

## <a name="simulate-sensor-data"></a>Sensör verilerinin simülasyonunu yapma

Bu bölümde, örnekte *cihaz bağlantısı* adlı projeyi kullanacaksınız. Hareket, sıcaklık ve karbon dioksit saptamak için algılayıcı verilerinin benzetimini yapabilirsiniz. Bu proje, sensörler için rastgele değerler oluşturur ve bunları cihaz bağlantı dizesini kullanarak IoT hub'a gönderir.

1. Ayrı bir komut penceresinde Azure dijital TWINS örneğine ve ardından **cihaz bağlantısı** klasörüne gidin.

1. Projenizin bağımlılıklarının doğru olduğundan emin olmak için şu komutu çalıştırın:

    ```cmd/sh
    dotnet restore
    ```

1. Düzenleyicinizde [appSettings. JSON](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) dosyasını açın ve aşağıdaki değerleri düzenleyin:

   a. **DeviceConnectionString**: Bir önceki bölümde çıktı penceresinde görünen `ConnectionString` değerini atayın. Bu dizeyi tırnak içinde tamamen kopyalayın, böylece simülatör IoT Hub ile düzgün bir şekilde bağlanabilir.

   b. **Algılayıcılar** dizisinin Içindeki **HardwareID** : Azure dijital TWINS Örneğinizde sağlanan sensörlerden olayları taklit ettiğiniz için, bu dosyadaki donanım kimliği ve algılayıcıların adları, *provisionsample. YAML* dosyasının `sensors` düğümüyle eşleşmelidir.

      Sıcaklık algılayıcısı için yeni bir giriş ekleyin. *AppSettings. JSON* içindeki **Algılayıcılar** düğümü aşağıdaki gibi görünmelidir:

      ```JSON
      "Sensors": [{
        "DataType": "Motion",
        "HardwareId": "SAMPLE_SENSOR_MOTION"
      },{
        "DataType": "CarbonDioxide",
        "HardwareId": "SAMPLE_SENSOR_CARBONDIOXIDE"
      },{
        "DataType": "Temperature",
        "HardwareId": "SAMPLE_SENSOR_TEMPERATURE"
      }]
      ```

1. Sıcaklık, hareket ve karbondioksit için cihaz olayı simülasyonunu başlatmak üzere şu komutu çalıştırın:

    ```cmd/sh
    dotnet run
    ```

   > [!NOTE]
   > Simülasyon örneği doğrudan dijital TWINS örneğiniz ile iletişim kurmadığından, kimlik doğrulaması yapmanız gerekmez.

## <a name="get-results-of-the-user-defined-function"></a>Kullanıcı tanımlı işlevin sonuçlarını al

Örneğiniz cihaz ve sensör verilerini her aldığında kullanıcı tanımlı işlev çalışır. Bu bölüm, Kullanıcı tanımlı işlevin sonuçlarını almak için Azure dijital TWINS örneğinizi sorgular. Neredeyse gerçek zamanlı olarak, bir odanın yeni olduğunu ve sıcaklığın doğru olduğunu göreceksiniz. 

1. Örneği veya yeni bir komut penceresini sağlamak için kullandığınız komut penceresini açın ve örneğin **Occupancy-quickstart\src** klasörüne yeniden gidin.

1. Aşağıdaki komutu çalıştırın ve istendiğinde oturum açın:

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

Çıkış penceresi, Kullanıcı tanımlı işlevin cihaz benzetimine ait olayları nasıl yürüttüğünde gösterir. 

   [UDF için ![çıkışı](./media/tutorial-facilities-udf/udf-running.png)](./media/tutorial-facilities-udf/udf-running.png#lightbox)

İzlenen koşul karşılanıyorsa, Kullanıcı tanımlı işlevi, [daha önce](#create-a-user-defined-function)gördüğünüz gibi ilgili iletiyle birlikte alanın değerini ayarlar. `GetAvailableAndFreshSpaces` işlevi, konsolundaki iletiyi yazdırır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu noktada Azure dijital İkizlerini keşfetmeye durdurmak istiyorsanız, bu öğreticide oluşturulan kaynakları silmek çekinmeyin:

1. Sol menüden [Azure portalında](https://portal.azure.com)seçin **tüm kaynakları**dijital İkizlerini kaynak grubunuzu seçin ve seçin **Sil**.

    > [!TIP]
    > Dijital İkizlerini örneğinizin silme sorun olduysa, bir hizmet güncelleştirmesi düzeltme alındı. Örneğiniz silme yeniden deneyin.

2. Gerekirse, iş makinenizdeki örnek uygulamaları silin.

## <a name="next-steps"></a>Sonraki adımlar

Artık alanları sağladığınıza ve özel bildirimleri tetiklemek için bir çerçeve oluşturduğunuza göre, aşağıdaki öğreticilerden birine gidebilirsiniz:

> [!div class="nextstepaction"]
> [Öğretici: Logic Apps'i kullanarak Azure Digital Twins alanlarınızdan bildirim alma](tutorial-facilities-events.md)

> [!div class="nextstepaction"]
> [Öğretici: Time Series Insights'ı kullanarak Azure Digital Twins alanlarınızdan gelen olayları görselleştirme ve analiz etme](tutorial-facilities-analyze.md)
