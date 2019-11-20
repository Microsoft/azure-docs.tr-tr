---
title: 'Öğretici: Azure Maps kullanarak IoT uzamsal Analizi uygulama'
description: "Öğretici: Azure haritalar hizmet API 'Leri ile IoT Hub tümleştirin."
author: walsehgal
ms.author: v-musehg
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: b876b27d0eb24a9eabcffe0d131ea0ef5bb79bad
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74107049"
---
# <a name="tutorial-implement-iot-spatial-analytics-using-azure-maps"></a>Öğretici: Azure Maps kullanarak IoT uzamsal Analizi uygulama

Boşluk ve saat içinde oluşan ilgili olayları izlemek ve yakalamak, yaygın bir IoT senaryosudur. Örneğin, filo yönetimi, varlık izleme, Mobility ve akıllı şehir uygulamaları. Bu öğretici, Event Grid tarafından sunulan olay aboneliği modeli kullanılarak IoT Hub tarafından yakalanan ilgili olaylara karşı Azure Maps API 'Lerini kullanmaya yönelik bir çözüm modeli boyunca size rehberlik eder.

Bu öğreticide şunları yapmanız gerekir:

> [!div class="checklist"]
> * Bir IoT Hub oluşturma.
> * Veri yükleme API 'sini kullanarak Azure Maps, Data Service içindeki bölge bölge alanını karşıya yükleyin.
> * Azure Maps uzamsal analizler temelinde iş mantığı uygulayan Azure Işlevlerinde bir işlev oluşturun.
> * Azure işlevindeki IoT cihaz telemetri olaylarına Event Grid aracılığıyla abone olun.
> * IoT Hub ileti yönlendirme kullanarak telemetri olaylarını filtreleyin.
> * İlgili olay verilerini depolamak için bir depolama hesabı oluşturun.
> * Araç içi IoT cihazının benzetimini yapın.
    

## <a name="use-case"></a>Kullanım örneği

Bir otomobil kiralama şirketinin, yeniden düzenlenen otomobiller için olayları izleyip günlüğe depolayacağı bir senaryoya yönelik çözümü kiralarız. Araba kiralama şirketleri genellikle belirli bir coğrafi bölge için otomobilleri kiralayıp bir süre sonra da bunların yer aldığı yeri takip etmeniz gerekir. Belirtilen coğrafi bölgeyi tutan bir otomobil içeren her örnek, ilkelerin, ücretlerin ve diğer iş yönlerinin düzgün işlenebilmesi için günlüğe kaydedilecek.

Kullanım örneğimizde, Kiralama otomobilleri düzenli olarak Azure IoT Hub telemetri verileri gönderen IoT cihazlarıyla donatılmıştır. Telemetri geçerli konumu içerir ve otomobil altyapısının çalışıp çalışmadığını belirtir. Cihaz konumu şeması, [Jeo uzamsal veriler Için ıot Tak ve kullan şemasına](https://github.com/Azure/IoTPlugandPlay/blob/master/Schemas/geospatial.md)uyar. Kiralık otomobil cihaz telemetri şeması şöyle görünür:

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

Araç içi cihaz telemetrisi, hedefi başarmak için kullanılabilir. Hedefimiz, bölge sınırlaması kuralları yürütmektir ve arabayı gösteren bir olay, taşınan konum alındığında her seferinde uygun şekilde takip edilir. Bunu yapmak için, Event Grid üzerinden IoT Hub cihaz telemetri olaylarına abone edeceğiz, böylece istenen müşteri iş mantığı yalnızca uygun olduğunda yürütülebilir. Event Grid abone olmanın birkaç yolu vardır. Bu öğreticide Azure Işlevleri 'ni kullanacağız. Azure Işlevleri, Event Grid yayımlanan olaylara tepki verir ve Azure Maps uzamsal analizler temelinde Otomobil Kiralama iş mantığını uygular. Azure işlevi, gerecin bölge alanının altına doğru olup olmadığını denetlemesinin yanı sıra geçerli konumla ilişkili adres gibi ek bilgiler de toplamasını içerir. İşlev Ayrıca, Araba Kiralama analisti ve Kiralama müşterisi için olay koşulları 'nın doğru açıklamasının sağlanmasına yardımcı olan bir veri blobu depolamada anlamlı olay verilerini depolamak için mantığı uygular.

Aşağıdaki diyagramda sisteme yüksek düzeyde bir genel bakış sunulmaktadır.

 
  <center>

  ![sisteme genel bakış](./media/tutorial-iot-hub-maps/system-diagram.png)</center>

Aşağıdaki şekilde, mavi ve kiralık araç rotasında yeşil bir çizgi olarak vurgulanan bölge bölge alanı gösterilmektedir.

  ![Geofence rotası](./media/tutorial-iot-hub-maps/geofence-route.png)


## <a name="prerequisites"></a>Önkoşullar 

### <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Bu öğreticideki adımları tamamlayabilmeniz için öncelikle Azure portal bir kaynak grubu oluşturmanız gerekir. Bir kaynak grubu oluşturmak için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com)’da oturum açın.

2. **kaynak grupları**' nı seçin.
    
   ![Kaynak grupları](./media/tutorial-iot-hub-maps/resource-group.png)

3. Kaynak grupları altında **Ekle**' yi seçin.
    
   ![Kaynak Grubu Ekle](./media/tutorial-iot-hub-maps/add-resource-group.png) 

4. Aşağıdaki özellik değerlerini girin:
    * **Abonelik:** Azure aboneliğiniz ' ı seçin.
    * **Kaynak grubu:** Kaynak grubu adı olarak "ContosoRental" yazın.
    * **Bölge:** Kaynak grubu için bir bölge seçin.  

    ![Kaynak grubu ayrıntıları](./media/tutorial-iot-hub-maps/resource-details.png)

    **Gözden geçir + oluştur** ' a tıklayın ve sonraki sayfada **Oluştur** ' u seçin.

### <a name="create-an-azure-maps-account"></a>Azure Haritalar hesabı oluşturma 

Azure haritalar uzamsal analizlerini temel alan iş mantığını uygulamak için, oluşturduğumuz kaynak grubunda bir Azure Maps hesabı oluşturuyoruz. S1 Fiyatlandırma Katmanı içeren bir Azure Maps hesabı aboneliği oluşturmak için [Hesabı Yönet](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) bölümündeki yönergeleri izleyin ve hesabınız için birincil abonelik anahtarını almak üzere [birincil anahtar al](./tutorial-search-location.md#getkey) bölümündeki adımları izleyin.


### <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

Olay verilerini günlüğe kaydetmek için, "ContosoRental" kaynak grubunda, verileri Bloblar olarak depolamak için bir genel amaçlı **v2storage** hesabı oluşturacağız. Depolama hesabı oluşturmak için [depolama hesabı oluşturma](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal)' daki yönergeleri izleyin. Daha sonra Blobları depolamak için bir kapsayıcı oluşturması gerekir. Bunu yapmak için aşağıdaki adımları izleyin:

1. Depolama hesabınızda bloblara gidin.

    ![larını](./media/tutorial-iot-hub-maps/blobs.png)

2. Sol üstteki kapsayıcı düğmesine tıklayın ve kapsayıcınızı "contoso-Kiralık-Logs" olarak adlandırın ve "Tamam" a tıklayın.

    ![blob kapsayıcısı](./media/tutorial-iot-hub-maps/blob-container.png)

3. Depolama hesabınızdaki **erişim tuşları** dikey penceresine gidip hesap adını ve erişim anahtarını kopyalayın, daha sonra kullanacağız.

    ![erişim tuşları](./media/tutorial-iot-hub-maps/access-keys.png)


Artık bir depolama hesabı ve olay verilerini günlüğe kaydetmek için bir kapsayımız olduğuna göre, bir IoT Hub oluşturacağız.

### <a name="create-an-iot-hub"></a>IoT Hub'ı oluşturma

IoT Hub, bir IoT uygulaması ve BT tarafından yönetilen cihazlar arasında çift yönlü iletişim için merkezi bir ileti hub 'ı görevi gören, bulutta yönetilen bir hizmettir. Cihaz telemetri iletilerini bir Event Grid yönlendirmek için, "ContosoRental" kaynak grubu içinde bir IoT Hub oluşturacak ve iletileri arabasının altyapı durumuna göre filtreleyecek ve cihaz telemetrisini gönderecek bir ileti yolu tümleştirmesi ayarlayacağız araba her hareket ettirdiğinde Event Grid iletiler. 

> [!Note] 
> IoT Hub cihaz telemetri olaylarını Event Grid yayımlama işlevselliği genel önizlemede. Genel Önizleme özellikleri **Doğu ABD, Batı ABD, Batı Avrupa, Azure Kamu, Azure Çin 21Vianet** ve **Azure Almanya**dışındaki tüm bölgelerde kullanılabilir. 

[IoT Hub oluşturma bölümündeki](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet#create-an-iot-hub)adımları Izleyerek IoT Hub 'ı oluşturun.


### <a name="register-a-device"></a>Cihaz kaydetme 

IoT Hub bağlanmak için bir cihazın kayıtlı olması gerekir. Bir cihazı IoT Hub 'a kaydetmek için aşağıdaki adımları izleyin:

1. IoT Hub, "IoT cihazları" dikey penceresine tıklayın ve "yeni" seçeneğine tıklayın.

    ![Cihaz Ekle](./media/tutorial-iot-hub-maps/add-device.png)

2. Cihaz Oluştur sayfasında IoT cihazınızı adlandırın ve "Kaydet" e tıklayın.
    
    ![kayıt-cihaz](./media/tutorial-iot-hub-maps/register-device.png)


## <a name="upload-geofence"></a>Bölge bölge yükleme

Azure haritalar verilerini karşıya yükleme API 'sini kullanarak [bölge bölge 'Sini](https://docs.microsoft.com/azure/azure-maps/geofence-geojson) Azure Maps hizmetine yüklemek Için [Postman uygulamasını](https://www.getpostman.com) kullanacağız. Araba bu bölge dışında olduğunda herhangi bir olay günlüğe kaydedilir.

Postman uygulamasını açın ve Azure Maps, veri yükleme API 'sini kullanarak bölge bölge 'yi karşıya yüklemek için aşağıdaki adımları izleyin.  

1. Postman uygulamasında yeni ' ye tıklayın | Yeni oluştur ve Istek seç. Bölge bölge verilerini karşıya yükleme için bir Istek adı girin, kaydedilecek bir koleksiyon veya klasör seçin ve Kaydet ' e tıklayın.

    ![Postman kullanarak bölge dilimleri yükleme](./media/tutorial-iot-hub-maps/postman-new.png)

2. Oluşturucu sekmesinde HTTP SONRASı yöntemi ' ni seçin ve POST isteği oluşturmak için aşağıdaki URL 'YI girin.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    URL yolundaki `dataFormat` parametresine karşılık gelen "geojson" değeri, karşıya yüklenen verilerin biçimini temsil eder.

3. **Parametreler**' e tıklayın ve post isteği URL 'si için kullanılacak aşağıdaki anahtar/değer çiftlerini girin. Abonelik anahtarı değerini Azure Maps birincil abonelik anahtarınızla değiştirin.
   
    ![Anahtar-değer params Postman](./media/tutorial-iot-hub-maps/postman-key-vals.png)

4. **Gövde** ' ye tıklayın, **Ham** giriş biçimi ' ni seçin ve açılan listeden giriş biçimi olarak **JSON (uygulama/metin)** seçeneğini belirleyin. [Buraya](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)JSON veri dosyasını açın ve geri yüklenecek veriler olarak JSON 'ı Postman 'daki gövde bölümüne kopyalayın ve **Gönder**' e tıklayın.
    
    ![veri Gönder](./media/tutorial-iot-hub-maps/post-json-data.png)
    
5. Yanıt üst bilgilerini gözden geçirin. Başarılı bir istek olduğunda, **konum** üstbilgisi karşıya yükleme isteğinin geçerli durumunu denetlemek IÇIN durum URI 'sini içerir. Durum URI 'SI aşağıdaki biçimde olacaktır. 

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

6. Durum URI 'nizi kopyalayın ve bu değere bir `subscription-key` parametresi ekleyerek Azure Maps hesabı abonelik anahtarınız olacak şekilde ekleyin. Durum URI biçimi aşağıdaki gibi olmalıdır:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. `udId` ' ı almak için, Postman uygulamasında yeni bir sekme açın ve Oluşturucu sekmesinde HTTP yöntemi Al ' ı seçin ve durum URI 'sinde bir GET isteği yapın. Karşıya veri yükleme işlemi başarılı olduysa yanıt gövdesinde bir UDID alırsınız. Uıdıd ' i daha sonra kullanmak üzere kopyalayın.

   ```JSON
   {
    "udid" : "{udId}"
   }
   ```


Daha sonra, "ContosoRental" kaynak grubu içinde bir Azure Işlevi oluşturacak ve cihaz telemetri iletilerini filtrelemek için IoT Hub bir ileti yolu ayarlayacağız.


## <a name="create-an-azure-function-and-add-an-event-grid-subscription"></a>Azure Işlevi oluşturma ve bir Event Grid aboneliği ekleme

Azure Işlevleri, işlem altyapısını açıkça sağlamaya veya yönetmeye gerek kalmadan isteğe bağlı olarak kod çalıştırmamızı sağlayan sunucusuz bir işlem hizmetidir. Azure Işlevleri hakkında daha fazla bilgi edinmek için [Azure işlevleri](https://docs.microsoft.com/azure/azure-functions/functions-overview) belgelerine göz atın. İşlevinde uygulamadığımız mantık, bölge durumunu değerlendirmek için araç içi cihaz telemetrisinden gelen konum verilerini kullanmaktır. Belirli bir araç bölge alanının dışında kaldığında, işlev, belirli bir konum koordinatını insan tarafından anlaşılabilir bir açık adrese çeviren [arama adresini al ters API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) aracılığıyla konumun adresi gibi daha fazla bilgi toplar. Tüm ilgili olay bilgileri daha sonra Blob deposunda depolanır. Aşağıdaki 5. adım, böyle bir mantığı uygulayan çalıştırılabilir kodu gösterir. Depolama hesabındaki blob kapsayıcısına veri günlüklerini gönderen ve buna Event Grid bir abonelik ekleyen bir Azure Işlevi oluşturmak için aşağıdaki adımları izleyin.

1. Azure portal panosunda kaynak oluştur ' u seçin. Kullanılabilir kaynak türleri listesinden **işlem** ' ı seçin ve ardından **işlev uygulaması**' nı seçin.

    ![kaynak oluştur](./media/tutorial-iot-hub-maps/create-resource.png)

2. İşlev uygulaması oluşturma sayfasında, işlev uygulamanızı adlandırın, **kaynak grubu** altında **Varolanı kullan**' ı seçin ve açılan listeden "ContosoRental" öğesini seçin. Çalışma zamanı yığını olarak ".NET Core" seçeneğini belirleyin, **depolama** altında **Varolanı kullan** ' ı seçin ve açılan listeden "contosorentaldata" öğesini seçin ve **Oluştur**' a tıklayın.
    
    ![oluşturma-uygulama](./media/tutorial-iot-hub-maps/rental-app.png)

3. Uygulama oluşturulduktan sonra buna bir işlev eklememiz gerekir. İşlev uygulamasına gidin ve bir işlev eklemek için **yeni işlev** ' e tıklayın, geliştirme ortamı olarak **Portal içi** ' i seçin ve **devam**' ı seçin.

    ![oluşturma-işlev](./media/tutorial-iot-hub-maps/function.png)

4. **Daha fazla şablon** seçin ve son ' a tıklayın **ve şablonları görüntüleyin**. 

5. **Azure Event Grid tetikleyicisi**olan şablonu seçin. İstenirse uzantıları yüklensin, işlevi ve isabet **Oluştur**' u adlandırın.

    ![işlev şablonu](./media/tutorial-iot-hub-maps/eventgrid-funct.png)

6. [C# kodunu](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) işlevinizin içine kopyalayın ve **Kaydet**' e tıklayın.
 
7. C# komut dosyasında aşağıdaki parametreleri değiştirin:
    * **SUBSCRIPTION_KEY** Azure Maps hesabı birincil abonelik anahtarınızla değiştirin.
    * **UDID** 'yi karşıya yüklediğiniz bölge sayısının UDID değeriyle değiştirin, 
    * Betikteki **Createblobasync** işlevi, veri depolama hesabında olay başına bir blob oluşturur. **ACCESS_KEY**, **account_name** ve **STORAGE_CONTAINER_NAME** , depolama hesabınızın erişim anahtarı ve hesap adı ve veri depolama kapsayıcınızla değiştirin.

10. **Event Grid abonelik Ekle**' ye tıklayın.
    
    ![Add-Event-Grid](./media/tutorial-iot-hub-maps/add-egs.png)

11. Abonelik ayrıntılarını doldur, **olay ABONELIĞI ayrıntıları** ' nın altında aboneliğiniz ve olay şeması için "Event Grid şeması" seçeneğini belirleyin. Konu **ayrıntıları** ' nın altında, konu türü olarak "Azure IoT Hub hesapları" seçeneğini belirleyin, kaynak grubunu oluşturmak için kullandığınız aboneliği seçin, "kaynak grubu" olarak "ContosoRental" öğesini seçin ve "kaynak" olarak oluşturduğunuz IoT Hub seçin. **Cihaz telemetrisini** olay türü olarak seçin. Bu seçenekleri belirledikten sonra, "IoT Hub" için "konu türü" değişikliğini otomatik olarak görürsünüz.

    ![olay-kılavuz-abonelik](./media/tutorial-iot-hub-maps/af-egs.png)
 

## <a name="filter-events-using-iot-hub-message-routing"></a>IoT Hub ileti yönlendirme kullanarak olayları filtreleme

Azure Işlevine bir Event Grid aboneliği ekledikten sonra, artık IoT Hub **Ileti yönlendirme** dikey penceresinde Event Grid bir varsayılan ileti yolunu görebileceksiniz. İleti yönlendirme, cihaz telemetri iletileri, cihaz yaşam döngüsü olayları ve cihaz ikizi değişiklik olayları gibi farklı veri türlerini çeşitli uç noktalara yönlendirmenizi sağlar. IoT Hub ileti yönlendirme hakkında daha fazla bilgi için bkz. [IoT Hub ileti yönlendirmeyi kullanma](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c).

![Merkez-örn. rota](./media/tutorial-iot-hub-maps/hub-route.png)

Örnek senaryolarımızda, Kiralama aracın hareket ettiği tüm iletileri filtrelemek istiyoruz. Event Grid cihaz telemetri olaylarını yayımlamak için, `Engine` özelliğinin **"açık"** olduğu olayları filtrelemek için yönlendirme sorgusunu kullanacağız. IoT cihazdan buluta iletileri sorgulamak için çeşitli yollar vardır. ileti yönlendirme sözdizimi hakkında daha fazla bilgi edinmek için bkz. [IoT Hub Message Routing](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax). Yönlendirme sorgusu oluşturmak için, **Routetoeventgrid** yoluna tıklayın ve **yönlendirme SORGUSUNU** **' "üzerinde" Engine = '** ile değiştirin ve **Kaydet**' e tıklayın. Artık IoT Hub 'ı yalnızca altyapının açık olduğu cihaz telemetrisini yayımlayacak.

![Merkez-örn. filtre](./media/tutorial-iot-hub-maps/hub-filter.png)


## <a name="send-telemetry-data-to-iot-hub"></a>Telemetri verilerini IoT Hub gönder

Azure Işlevimiz çalışır duruma getirildikten sonra, IoT Hub telemetri verilerini Event Grid göndereceğiz. Bir kiralama arabasının bir araç içi cihazının konum verilerinin benzetimini yapmak için bir c# uygulaması kullanacağız. Uygulamayı çalıştırmak için geliştirme makinenizde .NET Core SDK 2.1.0 veya üzeri bir sürümü gerekir. IoT Hub benzetimi yapılan telemetri verilerini göndermek için aşağıdaki adımları izleyin.

1. [Rentalcarsimülasyonu](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) c# projesini indirin. 

2. SimulatedCar.cs dosyasını istediğiniz bir metin düzenleyicisinde açın ve `connectionString` değerini, cihazı kaydettiğiniz sırada kaydettiğiniz değerle değiştirin ve değişiklikleri dosyaya kaydedin.
 
3. Yerel Terminal pencerenizde, C# projenin kök klasörüne gidin ve sanal cihaz uygulaması için gerekli paketleri yüklemek üzere aşağıdaki komutu çalıştırın:
    
    ```cmd/sh
    dotnet restore
    ```

4. Aynı terminalde, Kiralık otomobil benzetim uygulamasını derlemek ve çalıştırmak için aşağıdaki komutu çalıştırın:

    ```cmd/sh
    dotnet run
    ```

  Yerel terminalinizde aşağıdaki gibi görünmelidir.

  ![Terminal çıkışı](./media/tutorial-iot-hub-maps/terminal.png)

Blob Storage kapsayıcısını şimdi açarsanız, aracın bölge alanının dışında olduğu konumlar için dört blob görmeniz gerekir.

![Blobu girin](./media/tutorial-iot-hub-maps/blob.png)

Aşağıdaki haritada, araç, düzenli zaman aralıklarıyla günlüğe kaydedilen bölge sınırlarını aşacak olan dört noktayı gösterir.

![ihlalin eşlemesi](./media/tutorial-iot-hub-maps/violation-map.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide kullanılan Azure Haritalar API 'Lerini araştırmak için, bkz.:

* [Arama adresini tersine al](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)
* [Bölge bölge al](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

Azure haritalar REST API 'lerinin tüm listesi için bkz.:

* [Azure haritalar REST API 'Leri](https://docs.microsoft.com/rest/api/maps/spatial/getgeofence)

IoT Tak ve Kullan hakkında daha fazla bilgi edinmek için bkz.:

* [IoT Tak ve Kullan](https://docs.microsoft.com/azure/iot-pnp)

IoT için Azure Sertifikalı cihazların listesini almak için şu adresi ziyaret edin:

* [Azure Sertifikalı cihazlar](https://catalog.azureiotsolutions.com/)

Cihazın bulut telemetrisine gönderilmesi ve tam tersi hakkında daha fazla bilgi edinmek için bkz.:

* [Cihazdan telemetri gönderme](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-dotnet)
