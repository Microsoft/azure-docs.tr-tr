---
title: 'Öğretici: IoT uzamsal analizlerini uygulama | Microsoft Azure haritaları'
description: Microsoft Azure Maps hizmeti API 'Leriyle IoT Hub tümleştirme hakkında öğretici
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 9ebc6e266c93e55bc250e8450356f8b695dd9080
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715001"
---
# <a name="tutorial-implement-iot-spatial-analytics-by-using-azure-maps"></a>Öğretici: Azure Maps kullanarak IoT uzamsal analizler uygulama

IoT senaryosunda, boşluk ve saat içinde oluşan ilgili olayları yakalamak ve izlemek yaygındır. Örneğin, filo yönetimi, varlık izleme, Mobility ve akıllı şehir uygulamaları sayılabilir. Bu öğretici, Azure Maps API 'Lerini kullanarak kullanılan otomobil kiralama hareketini izleyen bir çözümde size rehberlik eder.

Bu öğreticide şunları yapmanız gerekir:

> [!div class="checklist"]
> * Araba izleme verilerini günlüğe kaydetmek için bir Azure depolama hesabı oluşturun.
> * Veri yükleme API 'sini kullanarak Azure Maps veri hizmeti 'ne (Önizleme) bir bölge bölge yükleyin.
> * Azure IoT Hub bir hub oluşturun ve bir cihazı kaydedin.
> * Azure Maps uzamsal analizler temelinde iş mantığı uygulayan Azure Işlevlerinde bir işlev oluşturun.
> * Azure işlevindeki IoT cihaz telemetri olaylarına Azure Event Grid aracılığıyla abone olun.
> * IoT Hub ileti yönlendirme kullanarak telemetri olaylarını filtreleyin.

## <a name="prerequisites"></a>Önkoşullar

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. [Azure haritalar hesabı oluşturun](quick-demo-map-app.md#create-an-azure-maps-account).

3. Birincil anahtar veya abonelik anahtarı olarak da bilinen [birincil bir abonelik anahtarı alın](quick-demo-map-app.md#get-the-primary-key-for-your-account). Daha fazla bilgi için bkz. [Azure haritalar 'da kimlik doğrulamasını yönetme](how-to-manage-authentication.md).

4. [Bir kaynak grubu oluşturun](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups). Bu öğreticide kaynak grubumuzu *ContosoRental* adı vereceğiz, ancak istediğiniz adı seçebilirsiniz.

5. [Rentalcarsimülasyonu C# projesini](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation)indirin.

Bu öğretici [Postman](https://www.postman.com/) uygulamasını kullanır, ancak farklı bir API geliştirme ortamı seçebilirsiniz.

## <a name="use-case-rental-car-tracking"></a>Kullanım örneği: Kiralama arabası izleme

Bir otomobil kiralama şirketinin, Kiralama arabasının konum bilgilerini, mesafeyi ve çalışma durumunu günlüğe kaydetmek istediğini varsayalım. Şirket, her bir otomobil doğru yetkili coğrafi bölgeyi terk ettiğinde da bu bilgileri depolamak istiyor.

Kiralama otomobilleri, IoT Hub düzenli olarak telemetri verileri gönderen IoT cihazlarıyla donatılmıştır. Telemetri geçerli konumu içerir ve otomobil altyapısının çalışıp çalışmadığını gösterir. Cihaz konumu şeması, [Jeo uzamsal veriler Için ıot Tak ve kullan şemasına](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md)uyar. Kiralık otomobil cihaz telemetri şeması aşağıdaki JSON kodu gibi görünür:

```JSON
{
    "data": {
        "properties": {
            "Engine": "ON"
        },
        "systemProperties": {
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "ContosoRentalDevice",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "636959817064335548",
            "iothub-enqueuedtime": "2019-06-18T00:17:20.608Z",
            "iothub-message-source": "Telemetry"
        },
        "body": {
            "location": {
                "type": "Point",
                "coordinates": [ -77.025988698005662, 38.9015330523316 ]
            }
        }
    }
}
```

Bu öğreticide yalnızca bir araç izlersiniz. Azure hizmetlerini ayarladıktan sonra, araç simülatörünü çalıştırmak için [Rentalcarsimülasyonu C# projesini](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) indirmeniz gerekir. Olaydan işlev yürütmeye kadar tüm işlem aşağıdaki adımlarda özetlenmiştir:

1. Araç içi cihaz IoT Hub telemetri verilerini gönderir.

2. Araba altyapısı çalışıyorsa, hub Event Grid için telemetri verilerini yayımlar.

3. Bir Azure işlevi, cihaz telemetri olaylarına ait olay aboneliği nedeniyle tetiklenir.

4. İşlev, araç cihazının Konum koordinatlarını, olay zamanını ve cihaz KIMLIĞINI günlüğe kaydeder. Daha sonra, arabasının bölge dışı olup olmadığını anlamak için [uzamsal bölge Get API](/rest/api/maps/spatial/getgeofence) 'sini kullanır. Bölge sınırları dışında bir seyahat varsa, işlev olaydan alınan konum verilerini bir blob kapsayıcısına depolar. İşlev Ayrıca, koordinat konumunu bir cadde adresine çevirmek için [ters arama adresini](/rest/api/maps/search/getsearchaddressreverse) sorgular ve cihaz konumu verilerinin geri kalanı ile depolar.

Aşağıdaki diyagramda, sistem için üst düzey bir genel bakış gösterilmektedir.

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="Sisteme genel bakış Diyagramı.":::

Aşağıdaki şekilde, bölge sınırı alanını mavi olarak vurgular. Kiralık Araba rotası yeşil bir çizgi ile belirtilir.

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="Bölge sınırı yolunu gösteren şekil.":::

## <a name="create-an-azure-storage-account"></a>Azure depolama hesabı oluşturma

Araba ihlali izleme verilerini depolamak için, kaynak grubunuzda [genel amaçlı v2 depolama hesabı](../storage/common/storage-account-overview.md) oluşturun. Bir kaynak grubu oluşturmadıysanız, [kaynak grubu oluşturma](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)bölümündeki yönergeleri izleyin. Bu öğreticide kaynak grubunuzu *ContosoRental* olarak adlandırın.

Depolama hesabı oluşturmak için [depolama hesabı oluşturma](../storage/common/storage-account-create.md?tabs=azure-portal)bölümündeki yönergeleri izleyin. Bu öğreticide, depolama hesabını *contosorentalstorage* olarak adlandırın, ancak genel olarak istediğiniz herhangi bir şeyi adlandırın.

Depolama hesabınızı başarıyla oluşturduğunuzda, günlük verilerini depolamak için bir kapsayıcı oluşturmanız gerekir.

1. Yeni oluşturulan depolama hesabınıza gidin. **Temel** bileşenler bölümünde **kapsayıcılar** bağlantısını seçin.

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="BLOB depolama için kapsayıcıların ekran görüntüsü.":::

2. Sol üst köşede **+ kapsayıcı**' yı seçin. Tarayıcının sağ tarafında bir panel görüntülenir. Kapsayıcınızı *contoso-Kiralık günlüklerinizi* olarak adlandırın ve **Oluştur**' u seçin.

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="Blob kapsayıcısı oluşturma ekranının ekran görüntüsü.":::

3. Depolama hesabınızdaki **erişim tuşları** bölmesine gidin ve **KEY1** bölümüne **depolama hesabı adını** ve **anahtar** değerini kopyalayın. Bu değerlerin her ikisi de "Azure Işlevi oluşturma ve Event Grid aboneliği ekleme" bölümünde gereklidir.

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="Depolama hesabı adını ve anahtarını kopyalama ekran görüntüsü.":::

## <a name="upload-a-geofence"></a>Bölge listesini karşıya yükle

Ardından, Azure Maps 'a [bölge bölge 'sini yüklemek](./geofence-geojson.md) Için [Postman uygulamasını](https://www.getpostman.com) kullanın. Bölge alanı, Kiralama aracımız için yetkili coğrafi bölgeyi tanımlar. Bir arabasının bölge alanının dışına taşınıp taşınmadığını öğrenmek için Azure işlevinizde bölge bölge kullanımını kullanacaksınız.

Azure haritalar verilerini karşıya yükleme API 'sini kullanarak bölge bölge 'yi karşıya yüklemek için şu adımları izleyin: 

1. Postman uygulamasını açın ve **Yeni**' yi seçin. **Yeni oluştur** penceresinde **koleksiyon**' ı seçin. Koleksiyonu adlandırın ve **Oluştur**' u seçin.

2. İsteği oluşturmak için **Yeni** ' yi seçin. **Yeni oluştur** penceresinde **istek**' ı seçin ve istek için bir istek adı girin. Önceki adımda oluşturduğunuz koleksiyonu seçin ve ardından **Kaydet**' i seçin.

3. Oluşturucu sekmesinde http **Post** yöntemini seçin ve VERI yükleme API 'sine bölge bölge adresini yüklemek için aşağıdaki URL 'yi girin. `{subscription-key}`Birincil abonelik anahtarınızla değiştirdiğinizden emin olun.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    URL yolunda, `geojson` parametresindeki değer, `dataFormat` karşıya yüklenen verilerin biçimini temsil eder.

4.   >  Giriş biçimi için gövde **RAW** ' yı seçin ve açılan listeden **JSON** ' ı seçin. [JSON veri dosyasını açın](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)ve JSON 'ı Body bölümüne kopyalayın. **Gönder**’i seçin.

5. **Gönder** ' i seçin ve isteği işleme için bekleyin. İstek tamamlandıktan sonra yanıtın **üstbilgiler** sekmesine gidin. Olan **konum** anahtarının değerini kopyalayın `status URL` .

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. API çağrısının durumunu denetlemek için üzerinde bir http isteği **alın** `status URL` . Kimlik doğrulaması için birincil abonelik anahtarınızı URL 'ye eklemeniz gerekir. **Get** isteği aşağıdaki URL 'yi beğenmelidir:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}
   ```
   
7. HTTP isteği **Al** işlemi başarıyla tamamlandığında, bir döndürür `resourceLocation` . , `resourceLocation` `udid` Karşıya yüklenen içerik için benzersiz bir içerir. Bu `udid` öğreticide daha sonra kullanmak üzere bunu kopyalayın.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-iot-hub"></a>IoT hub oluşturma

IoT Hub IoT uygulaması ile yönettiği cihazlar arasında güvenli ve güvenilir çift yönlü iletişim imkanı sunar. Bu öğreticide, Kiralama arabasının konumunu öğrenmek için araç cihazınızdan bilgi almak istiyorsunuz. Bu bölümde, *ContosoRental* kaynak grubu Içinde bir IoT Hub 'ı oluşturursunuz. Bu hub, cihaz telemetri olaylarınızın yayınlanmasından sorumludur.

> [!NOTE]
> Event Grid cihaz telemetri olaylarını yayımlama özelliği şu anda önizleme aşamasındadır. Bu özellik, şunlar dışında tüm bölgelerde kullanılabilir: Doğu ABD, Batı ABD, Batı Avrupa, Azure Kamu, Azure Çin 21Vianet ve Azure Almanya.

*ContosoRental* kaynak grubunda bir IoT Hub 'ı oluşturmak için [IoT Hub oluşturma](../iot-hub/quickstart-send-telemetry-dotnet.md#create-an-iot-hub)' daki adımları izleyin.

## <a name="register-a-device-in-your-iot-hub"></a>IoT Hub 'ınıza cihaz kaydetme

IoT Hub kimlik kayıt defterine kaydedilmedikleri takdirde cihazlar IoT Hub 'ına bağlanamaz. Burada, *InVehicleDevice* adında tek bir cihaz oluşturacaksınız. Cihaz oluşturmak ve IoT Hub 'ınızda kaydetmek için [IoT Hub 'ına yeni bir cihaz kaydetme](../iot-hub/iot-hub-create-through-portal.md#register-a-new-device-in-the-iot-hub)bölümündeki adımları izleyin. Cihazınızın birincil bağlantı dizesini kopyalamadığınızdan emin olun. Buna daha sonra ihtiyacınız olacak.

## <a name="create-a-function-and-add-an-event-grid-subscription"></a>Bir işlev oluşturma ve bir Event Grid aboneliği ekleme

Azure Işlevleri, işlem altyapısını açıkça sağlamak veya yönetmek zorunda kalmadan, küçük kod parçalarını ("işlevler") çalıştırmanıza olanak tanıyan sunucusuz bir işlem hizmetidir. Daha fazla bilgi için bkz. [Azure işlevleri](../azure-functions/functions-overview.md).

Bir işlev belirli bir olay tarafından tetiklenir. Burada, bir Event Grid tetikleyicisi tarafından tetiklenen bir işlev oluşturacaksınız. IoT Hub cihaz telemetri olayları için bir olay aboneliği oluşturarak tetikleyici ve işlev arasında ilişki oluşturun. Bir cihaz telemetri olayı gerçekleştiğinde, işleviniz bir uç nokta olarak çağrılır ve daha önce IoT Hub kaydettiğiniz cihaz için ilgili verileri alır.

[İşlevinizin Içereceği C# betik kodu](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx)aşağıda verilmiştir.

Şimdi Azure işlevinizi ayarlayın.

1. Azure portal panosunda **kaynak oluştur**' u seçin. Arama metin kutusuna **işlev uygulaması** yazın. **İşlev uygulaması**  >  **Oluştur**' u seçin.

1. **İşlev uygulaması** oluşturma sayfasında, işlev uygulamanızı adlandırın. **Kaynak grubu** altında, açılan listeden **ContosoRental** öğesini seçin. **Çalışma zamanı yığını** olarak **.NET Core** ' u seçin. Sayfanın en altında, Ileri ' yi seçin **: barındırma >**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="İşlev uygulaması oluşturma ekranının ekran görüntüsü.":::

1. **Depolama hesabı** Için, [Azure depolama hesabı oluşturma](#create-an-azure-storage-account)bölümünde oluşturduğunuz depolama hesabını seçin. **Gözden geçir ve oluştur**’u seçin.

1. İşlev uygulaması ayrıntıları ' nı gözden geçirin ve **Oluştur**' u seçin.

1. Uygulama oluşturulduktan sonra ona bir işlev eklersiniz. İşlev uygulamasına gidin. **İşlevler** bölmesini seçin. Sayfanın üst kısmında **+ Ekle**' yi seçin. İşlev şablonu paneli görüntülenir. Paneli aşağı kaydırın ve **Azure Event Grid tetikleyicisi**' ni seçin.

     >[!IMPORTANT]
    > **Azure Olay Hub 'ı tetikleyicisi** ve **Azure Event Grid tetikleyici** şablonlarının benzer adları vardır. **Azure Event Grid tetikleyici** şablonunu seçtiğinizden emin olun.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="İşlev oluşturma ekranının ekran görüntüsü.":::

1. İşleve bir ad verin. Bu öğreticide, *GetGeoFunction* adını kullanacaksınız, ancak genel olarak dilediğiniz adı kullanabilirsiniz. **Işlev oluştur**' u seçin.

1. Sol menüde, **kod + test** Bölmesi ' ni seçin. [C# betiğini](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) kopyalayıp kod penceresine yapıştırın.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="Kodu işlev penceresine Yapıştır ' ın kopyası/ekran görüntüsü.":::

1. C# kodunda, aşağıdaki parametreleri değiştirin:
    * **SUBSCRIPTION_KEY** Azure Maps hesabı birincil abonelik anahtarınızla değiştirin.
    * **UDID** `udid` değerini, [bölge yükleme](#upload-a-geofence)sırasında karşıya yüklediğiniz bölge alanı ile değiştirin.
    * `CreateBlobAsync`Betikteki işlev veri depolama hesabında olay başına bir blob oluşturur. **ACCESS_KEY**, **account_name** ve **STORAGE_CONTAINER_NAME** değerini depolama hesabınızın erişim anahtarı, hesap adı ve veri depolama kapsayıcınızla değiştirin. Bu değerler, [Azure depolama hesabı oluşturma](#create-an-azure-storage-account)bölümünde depolama hesabınızı oluşturduğunuzda oluşturulmuştur.

1. Sol taraftaki menüden **tümleştirme** bölmesini seçin. Diyagramda **Event Grid tetikleyiciyi** seçin. Tetikleyici için bir ad yazın, *Eventgridevent* ve **Event Grid aboneliği oluştur**' u seçin.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="Olay aboneliği Ekle ekran görüntüsü.":::

1. Abonelik ayrıntılarını doldurun. Olay aboneliğini adlandırın. **Olay şeması** Için **Event Grid şeması**' nı seçin. **Konu türleri** için **Azure IoT Hub hesapları**' nı seçin. **Kaynak grubu** için, Bu öğreticinin başlangıcında oluşturduğunuz kaynak grubunu seçin. **Kaynak** için, "Azure IoT Hub 'ı oluşturma" bölümünde oluşturduğunuz IoT Hub 'ını seçin. **Olay türlerine filtre** Için **cihaz telemetrisi**' ni seçin.

   Bu seçenekleri belirledikten sonra **IoT Hub**, **konu türü** değişikliğini görürsünüz. **Sistem konu adı** için, kaynağınız ile aynı adı kullanabilirsiniz. Son olarak, **uç nokta ayrıntıları** bölümünde **uç nokta seç**' i seçin. Tüm ayarları kabul edin ve **Seçimi Onayla**' yı seçin.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="Olay aboneliği oluşturma ekranının ekran görüntüsü.":::

1. Ayarlarınızı gözden geçirin. Uç noktanın, bu bölümün başlangıcında oluşturduğunuz işlevi belirttiğinden emin olun. **Oluştur**’u seçin.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="Olay aboneliği oluşturma onayı ekran görüntüsü.":::

1. Şimdi **düzenleme tetikleyicisi** paneline geri dönersiniz. **Kaydet**’i seçin.

## <a name="filter-events-by-using-iot-hub-message-routing"></a>IoT Hub ileti yönlendirme kullanarak olayları filtreleme

Azure işlevine Event Grid aboneliği eklediğinizde, belirtilen IoT Hub 'ında otomatik olarak bir mesajlaşma yolu oluşturulur. İleti yönlendirme, farklı veri türlerini çeşitli uç noktalara yönlendirmenize olanak tanır. Örneğin, cihaz telemetri iletilerini, cihaz yaşam döngüsü olaylarını ve cihaz ikizi değişiklik olaylarını yönlendirebilirsiniz. Daha fazla bilgi için bkz. [IoT Hub ileti yönlendirmeyi kullanma](../iot-hub/iot-hub-devguide-messages-d2c.md).

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="IoT Hub 'da ileti yönlendirmenin ekran görüntüsü.":::

Örnek senaryonuz, yalnızca Kiralama arabasının hareket ettirilmesi durumunda ileti almak istersiniz. `Engine`Özelliğin **"on"** değerine eşit olduğu olayları filtrelemek için bir yönlendirme sorgusu oluşturun. Yönlendirme sorgusu oluşturmak için **Routetoeventgrid** yolunu seçin ve **yönlendirme SORGUSUNU** **' "üzerinde" Engine = '** ile değiştirin. Sonra **Kaydet**'i seçin. Artık IoT Hub 'ı yalnızca altyapının açık olduğu cihaz telemetrisini yayımlar.

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="Filtre yönlendirme iletilerinin ekran görüntüsü.":::

>[!TIP]
>IoT cihazdan buluta iletileri sorgulamak için çeşitli yollar vardır. İleti yönlendirme sözdizimi hakkında daha fazla bilgi için bkz. [IoT Hub Message Routing](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

## <a name="send-telemetry-data-to-iot-hub"></a>Telemetri verilerini IoT Hub gönder

Azure işleviniz çalışırken, artık IoT Hub 'ına telemetri verileri gönderebilirsiniz. Bu işlem, Event Grid yönlendirir. Bir kiralama arabasının bir araç içi cihazının konum verilerinin benzetimini yapmak için bir C# uygulaması kullanın. Uygulamayı çalıştırmak için geliştirme bilgisayarınızda .NET Core SDK 2.1.0 veya üzeri bir sürüm gerekir. IoT Hub 'a sanal telemetri verileri göndermek için aşağıdaki adımları izleyin:

1. Daha önce yapmadıysanız, [Rentalcarsimülasyonu](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C# projesini indirin.

2. `simulatedCar.cs`Dosyayı seçtiğiniz bir metin düzenleyicisinde açın ve değerini, cihazı kaydettiğinizde kaydettiğiniz değerle değiştirin `connectionString` . Değişiklikleri dosyaya kaydedin.

3. Makinenizde .NET Core yüklü olduğundan emin olun. Yerel Terminal pencerenizde, C# projesinin kök klasörüne gidin ve sanal cihaz uygulaması için gerekli paketleri yüklemek üzere aşağıdaki komutu çalıştırın:

    ```cmd/sh
    dotnet restore
    ```

4. Aynı terminalde, Kiralık otomobil benzetim uygulamasını derlemek ve çalıştırmak için aşağıdaki komutu çalıştırın:

    ```cmd/sh
    dotnet run
    ```


  Yerel terminalinizde aşağıdaki gibi görünmelidir.

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="Terminal çıktısının ekran görüntüsü.":::

Blob Storage kapsayıcısını şimdi açarsanız, aracın bölge alanının dışında olduğu konumlar için dört blob görebilirsiniz.

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="Kapsayıcı içindeki blob 'ları görüntüleme ekran görüntüsü.":::

Aşağıdaki haritada bölge bölge dışında dört araç konum noktası gösterilmektedir. Her konum düzenli zaman aralıklarıyla günlüğe kaydedilir.

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="İhlal eşlemesinin ekran görüntüsü.":::

## <a name="explore-azure-maps-and-iot"></a>Azure haritalar ve IoT 'yi keşfet

Bu öğreticide kullanılan Azure Maps API 'Lerini araştırmak için, bkz.:

* [Arama adresini tersine al](/rest/api/maps/search/getsearchaddressreverse)
* [Bölge bölge al](/rest/api/maps/spatial/getgeofence)

Azure haritalar REST API 'lerinin tüm listesi için bkz.:

* [Azure haritalar REST API 'Leri](/rest/api/maps/spatial/getgeofence)

* [IoT Tak Çalıştır](../iot-pnp/index.yml)

IoT için Azure Sertifikalı cihazların listesini almak için şu adresi ziyaret edin:

* [Azure Sertifikalı cihazlar](https://devicecatalog.azure.com/)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Temizleme gerektiren kaynak yok.

## <a name="next-steps"></a>Sonraki adımlar

Cihazdan buluta telemetri gönderme hakkında daha fazla bilgi edinmek için, bkz.:


> [!div class="nextstepaction"]
> [Cihazdan telemetri gönderme](../iot-hub/quickstart-send-telemetry-dotnet.md)