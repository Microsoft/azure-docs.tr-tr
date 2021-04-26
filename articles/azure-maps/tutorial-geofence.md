---
title: 'Öğretici: Microsoft Azure haritasında bölge alanı oluşturma ve cihazları izleme'
description: Bölge ayarlama hakkında öğretici. Azure haritalar uzamsal hizmetini kullanarak bölge ile ilişkili cihazları nasıl izleyeceğinizi öğrenin
author: anastasia-ms
ms.author: v-stharr
ms.date: 8/20/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 759adea3cf34b79c76b6facec3bd4626ca54107e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98625041"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Öğretici: Azure Haritalar’ı kullanarak bölge sınırı ayarlama

Bu öğreticide, Azure haritalar bölge hizmetleri oluşturma ve kullanma hakkında temel bilgiler verilmektedir. Bunu aşağıdaki senaryo bağlamında gerçekleştirirsiniz:

*Bir oluşturma Site Yöneticisi, bir yapı alanının perimetrelerini girip terk ettiğinde ekipmanları izmelidir. Her bir ekipman her çıktığında veya bu perimeters 'a girdiğinde, Operations Manager 'a bir e-posta bildirimi gönderilir.*

Azure haritalar, üretim alanını girip çıkarken ekipmanların izlenmesini desteklemeye yönelik çeşitli hizmetler sunar. Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * İzlemek istediğiniz yapı alanı alanları ' nı tanımlayan [bölge sınırlama coğrafi JSON verilerini](geofence-geojson.md) karşıya yükleyin. Azure haritalar hesabınıza Çokgen koordinatları olarak bölge 'leri yüklemek için [veri yükleme API](/rest/api/maps/data/uploadpreview) 'sini kullanırsınız.
> * Tetiklendiğinde bölge bölgesine girdiğinde ve bu alandan çıkılırken yapım site Operations Manager 'a e-posta bildirimleri gönderen iki [mantıksal uygulama](../event-grid/handler-webhooks.md#logic-apps) ayarlayın.
> * Azure haritalar bölge 'larınızın giriş ve çıkış olaylarına abone olmak için [Azure Event Grid](../event-grid/overview.md) kullanın. İki mantıksal uygulamalarınızda tanımlanan HTTP uç noktalarını çağıran iki Web kancası olay aboneliği ayarlarsınız. Logic Apps bundan sonra, mantığın ötesinde veya girilme gibi donanımların uygun e-posta bildirimlerini gönderir.
> * Bir ekipman parçası çıktığında ve bölge alanları girdiğinde bildirim almak için [Arama bölge kümesi API 'sini](/rest/api/maps/spatial/getgeofence) kullanın.

## <a name="prerequisites"></a>Önkoşullar

1. [Azure haritalar hesabı oluşturun](quick-demo-map-app.md#create-an-azure-maps-account).
2. Birincil anahtar veya abonelik anahtarı olarak da bilinen [birincil bir abonelik anahtarı alın](quick-demo-map-app.md#get-the-primary-key-for-your-account).

Bu öğretici [Postman](https://www.postman.com/) uygulamasını kullanır, ancak farklı bir API geliştirme ortamı seçebilirsiniz.

## <a name="upload-geofencing-geojson-data"></a>Bölge sınırlaması coğrafi JSON verilerini karşıya yükle

Bu öğreticide, içeren bölge sınırlaması coğrafi JSON verilerini karşıya yüklersiniz `FeatureCollection` . , `FeatureCollection` Oluşturma sitesi içinde Çokgen alanları tanımlayan iki bölge içerir. İlk bölge için zaman aşımı süresi veya kısıtlama yoktur. İkinci bir, yalnızca iş saatleri (Pasifik saati diliminde 9:00 ÖÖ-5:00 PM) ile sorgulanabilir ve 1 Ocak 2022 ' den sonra geçerli olmayacaktır. GeoJSON biçimi hakkında daha fazla bilgi için bkz. [bölge sınırlama coğrafi JSON verileri](geofence-geojson.md).

>[!TIP]
>Bölge sınırlaması verilerinizi dilediğiniz zaman güncelleştirebilirsiniz. Daha fazla bilgi için bkz. [veri yükleme API 'si](/rest/api/maps/data/uploadpreview).

1. Postman uygulamasını açın. En üstteki, **Yeni**' yi seçin. **Yeni oluştur** penceresinde **koleksiyon**' ı seçin. Koleksiyonu adlandırın ve **Oluştur**' u seçin.

2. İsteği oluşturmak için **Yeni** ' yi seçin. **Yeni oluştur** penceresinde **istek**' ı seçin. İstek için bir **istek adı** girin. Önceki adımda oluşturduğunuz koleksiyonu seçin ve ardından **Kaydet**' i seçin.

3. Oluşturucu sekmesinde http **Post** yöntemini seçin ve bölge sınırlaması verilerini Azure Maps 'a yüklemek için aşağıdaki URL 'yi girin. Bu istek ve bu makalede bahsedilen diğer istekler için, `{Azure-Maps-Primary-Subscription-key}` birincil abonelik anahtarınızla değiştirin.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    `geojson`URL yolundaki parametre, karşıya yüklenen verilerin veri biçimini temsil eder.

4. **Gövde** sekmesini seçin. Girdi biçimi olarak **RAW** ve sonra **JSON** ' ı seçin. Aşağıdaki GeoJSON verilerini kopyalayıp **gövde** metin alanına yapıştırın:

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
            "expiredTime": "2022-01-01T00:00:00",
            "validityPeriod": [
                {
                  "startTime": "2020-07-15T16:00:00",
                  "endTime": "2020-07-15T24:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

5. **Gönder**' i seçin ve isteğin işlemesini bekleyin. İstek tamamlandığında, yanıtın **üstbilgiler** sekmesine gidin. Olan **konum** anahtarının değerini kopyalayın `status URL` .

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. API çağrısının durumunu denetlemek için üzerinde bir http isteği **alın** `status URL` . Kimlik doğrulaması için birincil abonelik anahtarınızı URL 'ye eklemeniz gerekir. **Get** isteği aşağıdaki URL 'yi beğenmelidir:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. HTTP isteği **Al** işlemi başarıyla tamamlandığında, bir döndürür `resourceLocation` . , `resourceLocation` `udid` Karşıya yüklenen içerik için benzersiz bir içerir. Bu `udid` öğreticinin son bölümünde bölge Al API 'sini sorgulamak için bunu kaydedin. İsteğe bağlı olarak, bir `resourceLocation` sonraki adımda bu kaynaktan meta verileri almak için URL 'yi kullanabilirsiniz.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

8. İçerik meta verilerini almak için,  `resourceLocation` adım 7 ' de alınan URL 'de http isteği Al ' ı oluşturun. Kimlik doğrulaması için birincil abonelik anahtarınızı URL 'ye eklemediğinizden emin olun. **Get** isteği aşağıdaki URL 'yi beğenmelidir:

    ```http
   https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

9. HTTP isteği **Al** işlemi başarıyla tamamlandığında, yanıt gövdesi `udid` `resourceLocation` 7. adımda belirtilen ' yi içerir. Ayrıca, gelecekte içeriğe erişmek ve içerik hakkında diğer meta verileri yüklemek ve indirmek için konumu da içerecektir. Genel yanıta bir örnek:

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "7/15/2020 6:11:43 PM +00:00",
        "updated": "7/15/2020 6:11:45 PM +00:00",
        "sizeInBytes": 1962,
        "uploadStatus": "Completed"
    }
    ```

## <a name="create-workflows-in-azure-logic-apps"></a>Azure Logic Apps iş akışları oluşturma

Ardından, bir e-posta bildirimi tetikleyen iki [mantıksal uygulama](../event-grid/handler-webhooks.md#logic-apps) uç noktası oluşturursunuz. İlk olanı oluşturmak için aşağıdaki adımları uygulayın:

1. [Azure portalında](https://portal.azure.com) oturum açın.

2. Azure portal **kaynak oluştur**' u seçin.

3. Market 'te **Ara** kutusuna **Logic App** yazın.

4. Sonuçlardan **Logic App**  >  **Oluştur**' u seçin.

5. **Mantıksal uygulama** sayfasında, aşağıdaki değerleri girin:
    * Bu mantıksal uygulama için kullanmak istediğiniz **abonelik** .
    * Bu mantıksal uygulamanın **kaynak grubu** adı. **Yeni oluştur** veya **var olan** kaynak grubunu kullan seçeneğini belirleyebilirsiniz.
    * Mantıksal uygulamanızın **mantıksal uygulama adı** . Bu durumda, `Equipment-Enter` ad olarak kullanın.

    Bu öğreticinin amaçları doğrultusunda, diğer tüm değerleri varsayılan ayarlarında saklayın.

    :::image type="content" source="./media/tutorial-geofence/logic-app-create.png" alt-text="Mantıksal uygulama oluşturma ekranının ekran görüntüsü.":::

6. **Gözden geçir + Oluştur**’u seçin. Ayarlarınızı gözden geçirin ve dağıtımı göndermek için **Oluştur** ' u seçin. Dağıtım başarıyla tamamlandığında **Kaynağa Git**' i seçin. **Mantıksal uygulama Tasarımcısı**' na götürülürsünüz.

7. Tetikleyici türü seçin. **Sık kullanılan bir tetikleyici Ile başlayın** bölümüne gidin. **BIR http isteği alındığında** öğesini seçin.

     :::image type="content" source="./media/tutorial-geofence/logic-app-trigger.png" alt-text="Mantıksal uygulama oluşturma HTTP tetikleyicisi ekran görüntüsü.":::

8. Mantıksal uygulama Tasarımcısı 'nın sağ üst köşesinde **Kaydet**' i seçin. **Http post URL 'si** otomatik olarak oluşturulur. URL 'YI kaydedin. Bir sonraki bölümde bir olay uç noktası oluşturmak için bu gereklidir.

    :::image type="content" source="./media/tutorial-geofence/logic-app-httprequest.png" alt-text="Mantıksal uygulama HTTP Isteği URL 'SI ve JSON ekran görüntüsü.":::

9. **+ Yeni adım**' ı seçin. Şimdi bir eylem seçersiniz. `outlook.com email`Arama kutusuna yazın. **Eylemler** listesinde aşağı kaydırın ve **e-posta gönder (v2)** seçeneğini belirleyin.
  
    :::image type="content" source="./media/tutorial-geofence/logic-app-designer.png" alt-text="Mantıksal uygulama Tasarımcısı oluşturma ekranının ekran görüntüsü.":::

10. Outlook Hesabınızda oturum açın. Mantıksal uygulamanın hesaba erişmesine izin vermek için **Evet** ' i seçtiğinizden emin olun. E-posta göndermek için alanları girin.

    :::image type="content" source="./media/tutorial-geofence/logic-app-email.png" alt-text="Mantıksal uygulama oluşturma için e-posta gönder adımının ekran görüntüsü.":::

    >[!TIP]
    > `geometryId` `deviceId` E-posta bildirimlerinde, veya gibi coğrafi JSON yanıt verilerini alabilirsiniz. Event Grid tarafından gönderilen verileri okumak için Logic Apps yapılandırabilirsiniz. Logic Apps yapılandırma hakkında daha fazla bilgi için bkz. [öğretici: Azure IoT Hub olaylar hakkında Event Grid ve Logic Apps kullanarak e-posta bildirimleri gönderme](../event-grid/publish-iot-hub-events-to-logic-apps.md).

11. Mantıksal uygulama Tasarımcısı 'nın sol üst köşesinde **Kaydet**' i seçin.

Ekipman yapım sitesinden çıktığında yöneticiye bildirmek için ikinci bir mantıksal uygulama oluşturmak üzere 3-11 arasındaki adımları yineleyin. Mantıksal uygulamayı adlandırın `Equipment-Exit` .

## <a name="create-azure-maps-events-subscriptions"></a>Azure haritalar olay abonelikleri oluşturma

Azure haritalar [üç olay türünü](../event-grid/event-schema-azure-maps.md)destekler. Burada iki farklı olay aboneliği oluşturmanız gerekir: bir adet, bölge sınırı olayları için bir tane ve bir diğeri de bölge, çıkış olayları için.

Aşağıdaki adımlarda, bölge girme olayları için bir olay aboneliğinin nasıl oluşturulacağı gösterilmektedir. Adımları benzer bir şekilde tekrarlayarak bölge, çıkış olaylarına abone olabilirsiniz.

1. Azure haritalar hesabınıza gidin. Panoda, **abonelikler**' i seçin. Abonelik adınızı seçin ve Ayarlar menüsünden **Olaylar** ' ı seçin.

    :::image type="content" source="./media/tutorial-geofence/events-tab.png" alt-text="Azure Maps hesap olaylarına git ekran görüntüsü.":::

2. Bir olay aboneliği oluşturmak için olaylar sayfasından **+ olay aboneliği** ' ni seçin.

    :::image type="content" source="./media/tutorial-geofence/create-event-subscription.png" alt-text="Azure Maps olayları aboneliği oluşturma için ekran görüntüsü.":::

3. **Olay aboneliği oluştur** sayfasında, aşağıdaki değerleri girin:
    * Olay aboneliğinin **adı** .
    * **Olay şeması** *Event Grid şema* olmalıdır.
    * Bu olay aboneliği için **Sistem konu adı** , bu durumda `Contoso-Construction` .
    * **Olay türlerine filtre** için `Geofence Entered` olay türü olarak öğesini seçin.
    * **Uç nokta türü** için öğesini seçin `Web Hook` .
    * **Uç nokta** için, önceki bölümde oluşturduğunuz uç nokta girin mantıksal uygulama IÇIN http post URL 'sini kopyalayın. Kaydetmeyi unuttuysanız, mantıksal uygulama tasarımcısına geri dönüp HTTP tetikleyici adımından kopyalamanız yeterlidir.

    :::image type="content" source="./media/tutorial-geofence/events-subscription.png" alt-text="Azure Maps olayları abonelik ayrıntılarının ekran görüntüsü.":::

4. **Oluştur**’u seçin.

Önceki bölümde oluşturduğunuz mantıksal uygulama çıkış uç noktası için 1-4 adımlarını yineleyin. Adım 3 ' te `Geofence Exited` olay türü olarak seçtiğinizden emin olun.

## <a name="use-spatial-geofence-get-api"></a>Uzamsal bölge Get API 'sini kullanma

Bir ekipman parçası bölge dilimleri girdiğinde veya oradan çıktığında Operations Manager 'a e-posta bildirimleri göndermek için [uzamsal bölge Get API 'sini](/rest/api/maps/spatial/getgeofence) kullanın.

Her bir ekipman parçası bir `deviceId` . Bu öğreticide, benzersiz bir KIMLIĞIYLE tek bir ekipman parçasını izliyorsunuz `device_1` .

Aşağıdaki diyagramda, zaman içindeki donanımların, bölge sayısı dışında bir yerde olan *Başlangıç* konumundan başlayan beş konumu gösterilmektedir. Bu öğreticinin amaçları doğrultusunda, bu konumda cihazı sorgulamayacağından *Başlangıç* konumu tanımsızdır.

[Uzamsal bölge edınme API](/rest/api/maps/spatial/getgeofence) 'sini ilk bölge girişi veya çıkışı gösteren bir ekipman konumuyla sorgulayıp, Event Grid Operations Manager 'a bir e-posta bildirimi göndermek için uygun mantıksal uygulama uç noktasını çağırır.

Aşağıdaki bölümlerin her biri, donanımların beş farklı konum koordinatlarını kullanarak API istekleri yapar.

![Azure haritalar 'da bölge haritası haritasının diyagramı](./media/tutorial-geofence/geofence.png)

### <a name="equipment-location-1-47638237-122132483"></a>Ekipman konumu 1 (47.638237,-122,132483)

1. Postman uygulamasının üst kısmında **Yeni**' yi seçin. **Yeni oluştur** penceresinde **istek**' ı seçin. İstek için bir **istek adı** girin. Bu *konumu 1* yapın. Bölge sınırlaması olan [coğrafi JSON verilerini karşıya yükle bölümünde](#upload-geofencing-geojson-data)oluşturduğunuz koleksiyonu seçin ve ardından **Kaydet**' i seçin.

2. Oluşturucu sekmesinde http **Al** metodunu seçin ve aşağıdaki URL 'yi girin. `{Azure-Maps-Primary-Subscription-key}`Birincil abonelik anahtarınızla ve `{udid}` Bölge sınırlaması olan `udid` [coğrafi JSON verilerini karşıya yükleme bölümüne](#upload-geofencing-geojson-data)kaydettiğiniz ile değiştirdiğinizden emin olun.

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. **Gönder**’i seçin. Aşağıdaki GeoJSON yanıt penceresinde görünür.

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638291,
          "nearestLon": -122.132483
        },
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.638053,
          "nearestLon": -122.13295
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ```

Önceki GeoJSON yanıtında, ana site bölge listesinden olumsuz mesafe, ekipmanın bölge bölgesi içinde olduğu anlamına gelir. Alt siteden gelen pozitif mesafe, ekipmanın alt site bölge sınırı dışında olduğu anlamına gelir. Bu cihaz, ana site bölge sınırı içinde ilk kez bulunduğu `isEventPublished` için parametresi olarak ayarlanır `true` . Operations Manager, ekipmanın bölge sınırlaması girdiği bir e-posta bildirimi alır.

### <a name="location-2-4763800-122132531"></a>Konum 2 (47.63800,-122,132531)

1. Postman uygulamasının üst kısmında **Yeni**' yi seçin. **Yeni oluştur** penceresinde **istek**' ı seçin. İstek için bir **istek adı** girin. Bu *konumu 2* yapın. Bölge sınırlaması olan [coğrafi JSON verilerini karşıya yükle bölümünde](#upload-geofencing-geojson-data)oluşturduğunuz koleksiyonu seçin ve ardından **Kaydet**' i seçin.

2. Oluşturucu sekmesinde http **Al** metodunu seçin ve aşağıdaki URL 'yi girin. `{Azure-Maps-Primary-Subscription-key}`Birincil abonelik anahtarınızla ve `{udid}` Bölge sınırlaması olan `udid` [coğrafi JSON verilerini karşıya yükleme bölümüne](#upload-geofencing-geojson-data)kaydettiğiniz ile değiştirdiğinizden emin olun.

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. **Gönder**’i seçin. Aşağıdaki GeoJSON yanıt penceresinde görünür:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637997,
          "nearestLon": -122.132399
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.63789,
          "nearestLon": -122.132809
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

Önceki GeoJSON yanıtında, ekipman ana site bölge bölgesine kaldı ve alt site bölge bölge bölge 'sini girmemiştir. Sonuç olarak, parametresi olarak `isEventPublished` ayarlanır `false` ve Operations Manager herhangi bir e-posta bildirimi almaz.

### <a name="location-3-4763810783315048-12213336020708084"></a>Konum 3 (47.63810783315048,-122.13336020708084)

1. Postman uygulamasının üst kısmında **Yeni**' yi seçin. **Yeni oluştur** penceresinde **istek**' ı seçin. İstek için bir **istek adı** girin. BT *konumunu 3* yapın. Bölge sınırlaması olan [coğrafi JSON verilerini karşıya yükle bölümünde](#upload-geofencing-geojson-data)oluşturduğunuz koleksiyonu seçin ve ardından **Kaydet**' i seçin.

2. Oluşturucu sekmesinde http **Al** metodunu seçin ve aşağıdaki URL 'yi girin. `{Azure-Maps-Primary-Subscription-key}`Birincil abonelik anahtarınızla ve `{udid}` Bölge sınırlaması olan `udid` [coğrafi JSON verilerini karşıya yükleme bölümüne](#upload-geofencing-geojson-data)kaydettiğiniz ile değiştirdiğinizden emin olun.

    ```HTTP
      https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
      ```

3. **Gönder**’i seçin. Aşağıdaki GeoJSON yanıt penceresinde görünür:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638294,
          "nearestLon": -122.133359
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": -999.0,
          "nearestLat": 47.638161,
          "nearestLon": -122.133549
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

Önceki GeoJSON yanıtında, ekipman ana site bölge bölgesine kaldı, ancak alt site bölge bölge bölgesine girdi. Sonuç olarak, parametresi olarak `isEventPublished` ayarlanır `true` . Operations Manager, ekipmanın bölge olarak girildiğini belirten bir e-posta bildirimi alır.

>[!NOTE]
>Ekipman iş saatlerinden sonra alt siteye taşınmışsa, hiçbir olay yayımlanmaz ve Operations Manager hiçbir bildirim almaz.  

### <a name="location-4-47637988-1221338344"></a>Konum 4 (47.637988,-122,1338344)

1. Postman uygulamasının üst kısmında **Yeni**' yi seçin. **Yeni oluştur** penceresinde **istek**' ı seçin. İstek için bir **istek adı** girin. Bu *konumu 4* yapın. Bölge sınırlaması olan [coğrafi JSON verilerini karşıya yükle bölümünde](#upload-geofencing-geojson-data)oluşturduğunuz koleksiyonu seçin ve ardından **Kaydet**' i seçin.

2. Oluşturucu sekmesinde http **Al** metodunu seçin ve aşağıdaki URL 'yi girin. `{Azure-Maps-Primary-Subscription-key}`Birincil abonelik anahtarınızla ve `{udid}` Bölge sınırlaması olan `udid` [coğrafi JSON verilerini karşıya yükleme bölümüne](#upload-geofencing-geojson-data)kaydettiğiniz ile değiştirdiğinizden emin olun.

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&userTime=2023-01-16&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. **Gönder**’i seçin. Aşağıdaki GeoJSON yanıt penceresinde görünür:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637985,
          "nearestLon": -122.133907
        }
      ],
      "expiredGeofenceGeometryId": [
        "2"
      ],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

Önceki GeoJSON yanıtında, ekipman ana site bölge bölgesine çıkımıştır, ancak alt site bölge bölge değerinden çıkıldı. Bununla birlikte, `userTime` değerin `expiredTime` bölge verilerinde tanımlandığına göre olduğuna dikkat edin. Sonuç olarak, parametresi olarak `isEventPublished` ayarlanır `false` ve Operations Manager bir e-posta bildirimi almaz.

### <a name="location-5-4763799--122134505"></a>Konum 5 (47,63799,-122,134505)

1. Postman uygulamasının üst kısmında **Yeni**' yi seçin. **Yeni oluştur** penceresinde **istek**' ı seçin. İstek için bir **istek adı** girin. BT *konumunu 5* yapın. Bölge sınırlaması olan [coğrafi JSON verilerini karşıya yükle bölümünde](#upload-geofencing-geojson-data)oluşturduğunuz koleksiyonu seçin ve ardından **Kaydet**' i seçin.

2. Oluşturucu sekmesinde http **Al** metodunu seçin ve aşağıdaki URL 'yi girin. `{Azure-Maps-Primary-Subscription-key}`Birincil abonelik anahtarınızla ve `{udid}` Bölge sınırlaması olan `udid` [coğrafi JSON verilerini karşıya yükleme bölümüne](#upload-geofencing-geojson-data)kaydettiğiniz ile değiştirdiğinizden emin olun.

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63799&lon=-122.134505&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. **Gönder**’i seçin. Aşağıdaki GeoJSON yanıt penceresinde görünür:

    ```json
    {
      "geometries": [
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "1",
        "distance": -999.0,
        "nearestLat": 47.637985,
        "nearestLon": -122.133907
      },
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "2",
        "distance": 999.0,
        "nearestLat": 47.637945,
        "nearestLon": -122.133683
      }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

Önceki GeoJSON yanıtında, ekipman ana site bölge bölgesine çıkış yaptı. Sonuç olarak, parametresi olarak `isEventPublished` ayarlanır `true` ve Operations Manager, ekipmanın bir bölge kapsamında çıkış yapıldığını belirten bir e-posta bildirimi alır.


Ayrıca, [Event Grid ve Logic Apps kullanarak e-posta bildirimleri gönderebilir](../event-grid/publish-iot-hub-events-to-logic-apps.md) ve Azure haritalar 'ı kullanarak [Event Grid desteklenen olay işleyicilerini](../event-grid/event-handlers.md) denetleyebilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Temizleme gerektiren kaynak yok.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Logic Apps içerik türlerini işleme](../logic-apps/logic-apps-content-type.md)