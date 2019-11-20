---
title: 'Öğretici: Azure haritalar kullanarak bölge oluşturma'
description: "Öğretici: Azure haritalar 'ı kullanarak bölge düzeni ayarlayın."
author: walsehgal
ms.author: v-musehg
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 62005546c653796773083eaf625820ab532a8a2c
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74107106"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Öğretici: Azure haritalar 'ı kullanarak bölge bölge ayarlama

Bu öğretici, Azure haritalar 'ı kullanarak bölge sınırlaması ayarlama konusunda size kılavuzluk eder. Bu öğreticide adresdığımız senaryo, site yöneticilerinin, belirlenen oluşturma alanlarının ötesine geçen potansiyel tehlikeli donanımları izlemesine yardımcı olur. Bir inşaat sitesi pahalı ekipman ve düzenlemeler içerir. Genellikle, ekipmanın yapım sitesinin içinde kalmasını ve izin olmadan ayrılmamaya gerek yoktur.

Bölge listesini depolamak için Azure Maps veri yükleme API 'sini kullanacağız ve bölge konumunu, bölge alanına göre denetlemek için Azure Maps geofence API 'sini kullanacağız. Bölge sonuçlarının akışını yapmak ve bölge sonuçlarına dayalı bir bildirim ayarlamak için Azure Event Grid kullanacağız.
Event Grid hakkında daha fazla bilgi için bkz. [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview).
Bu öğreticide şunları öğreneceksiniz:

> [!div class="checklist"]
> * Veri yükleme API 'sini kullanarak Azure Maps, Data Service içindeki bölge bölge alanını karşıya yükleyin.
> *   Bölge olaylarını işlemek için bir Event Grid ayarlayın.
> *   Bölge olayları işleyicisini ayarlayın.
> *   Logic Apps kullanarak bölge sınırı olaylarına yanıt olarak uyarıları ayarlayın.
> *   Bir oluşturma varlığının yapım sitesi içinde olup olmadığını izlemek için Azure haritalar bölge hizmeti API 'Lerini kullanın.


## <a name="prerequisites"></a>Önkoşullar

### <a name="create-an-azure-maps-account"></a>Azure Haritalar hesabı oluşturma 

Bu öğreticideki adımları tamamlayabilmeniz için, S1 fiyatlandırma katmanıyla bir Azure Maps hesap aboneliği oluşturmak üzere [Hesabı Yönet](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) ' deki yönergeleri izleyin ve hesabınıza ilişkin birincil abonelik anahtarını almak için [birincil anahtar al](./tutorial-search-location.md#getkey) bölümündeki adımları izleyin.

## <a name="upload-geofences"></a>Bölge dilimleri karşıya yükle

Veri yükleme API 'sini kullanarak inşaat sitesinin bölge bölge 'sini karşıya yüklemek için Postman uygulamasını kullanacağız. Bu öğreticinin bir bölümü olarak, yapı donanımının ihlal etmemelidir bir sabit parametre olan genel bir oluşturma site alanı olduğunu varsayalım. Bu çit ihlalleri ciddi bir OFFENSE ve Operations Manager bildirilir. En iyi duruma getirilmiş ek bir küme, genel yapım alanı içindeki farklı oluşturma alanlarının zamanlamaya göre izlenmesi için kullanılabilir. Ana geofalanın, zaman aşımı süresi geçen ve bu süreden sonra sona ereceği bir subsite1 olduğunu varsayabiliriz. Gereksinimlerinize göre daha fazla iç içe bölge oluşturabilirsiniz. Örneğin, subsite1, zamanlamanın 1 ila 4 ' e kadar iş gerçekleştiği ve alt site 2 ' nin hafta 5 ' e kadar iş gerçekleştiği yerdir. Bu tür balıkların hepsi projenin başlangıcında tek bir veri kümesi olarak yüklenebilir ve kuralları saat ve alana göre izlemek için kullanılabilir. Bölge verileri biçimi hakkında daha fazla bilgi için bkz. [bölge bölge coğrafi JSON verileri](https://docs.microsoft.com/azure/azure-maps/geofence-geojson). Verileri Azure Maps hizmetine yükleme hakkında daha fazla bilgi için bkz. [veri yükleme API 'si belgeleri](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) .

Postman uygulamasını açın ve Azure Maps, veri yükleme API 'sini kullanarak inşaat sitesini karşıya yüklemek için aşağıdaki adımları izleyin.

1. Postman uygulamasını açın ve yeni ' ye tıklayın | Yeni oluştur ve Istek seç. Bölge bölge verilerini karşıya yükleme için bir Istek adı girin, kaydedilecek bir koleksiyon veya klasör seçin ve Kaydet ' e tıklayın.

    ![Postman kullanarak bölge dilimleri yükleme](./media/tutorial-geofence/postman-new.png)

2. Oluşturucu sekmesinde HTTP SONRASı yöntemi ' ni seçin ve POST isteği oluşturmak için aşağıdaki URL 'YI girin.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```
    
    URL yolundaki GEOJSON parametresi, karşıya yüklenen verilerin veri biçimini temsil eder.

3. **Parametreler**' e tıklayın ve post isteği URL 'si için kullanılacak aşağıdaki anahtar/değer çiftlerini girin. Abonelik anahtarı değerini Azure Maps birincil abonelik anahtarınızla değiştirin.
   
    ![Anahtar-değer params Postman](./media/tutorial-geofence/postman-key-vals.png)

4. **Gövde** ' ye tıklayın, ham giriş biçimi ' ni seçin ve açılan listeden giriş BIÇIMI olarak JSON ' ı seçin. Karşıya yüklenecek veri olarak aşağıdaki JSON 'ı sağlayın:

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
              "expiredTime": "2019-01-15T00:00:00",
              "validityPeriod": [
                {
                  "startTime": "2019-01-08T01:00:00",
                  "endTime": "2019-01-08T17:00:00",
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

5. Gönder ' e tıklayın ve yanıt üst bilgisini gözden geçirin. Başarılı bir istek olduğunda, **konum** üstbilgisi karşıya yükleme isteğinin geçerli durumunu denetlemek IÇIN durum URI 'sini içerir. Durum URI 'SI aşağıdaki biçimde olacaktır. 

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

## <a name="set-up-an-event-handler"></a>Olay işleyicisi ayarlama

Operations Manager giriş ve çıkış olaylarıyla ilgili bilgilendirmek için bildirimleri alan bir olay işleyicisi oluşturacağız.

Olayları işlemek, girmek ve çıkmak için iki [Logic Apps](https://docs.microsoft.com/azure/event-grid/event-handlers#logic-apps) hizmeti oluşturacağız. Ayrıca, bu olaylar tarafından tetiklenen Logic Apps içindeki olay tetikleyicileri de oluşturacağız. Bu durumda, ekipman her seferinde veya çıkış sitesinden çıktığında, bu e-postalara Operations Manager uyarı gönderilmesi önerilir. Aşağıdaki şekilde, bölge sınırı Enter olayı için bir mantıksal uygulama oluşturma gösterilmektedir. Benzer şekilde, çıkış olayı için başka bir tane de oluşturabilirsiniz.
Daha fazla bilgi için [desteklenen tüm olay işleyicilerini](https://docs.microsoft.com/azure/event-grid/event-handlers) görebilirsiniz.

1. Azure portal bir mantıksal uygulama oluşturma

   ![Logic Apps oluştur](./media/tutorial-geofence/logic-app.png)

2. Bir HTTP isteği tetikleyicisi seçin ve Outlook Bağlayıcısı 'nda "e-posta gönder" eylemini seçin
  
   ![Logic Apps şeması](./media/tutorial-geofence/logic-app-schema.png)

3. HTTP URL uç noktasını oluşturmak için mantıksal uygulamayı kaydedin ve HTTP URL 'sini kopyalayın.

   ![Logic Apps uç noktası](./media/tutorial-geofence/logic-app-endpoint.png)


## <a name="create-an-azure-maps-events-subscription"></a>Azure haritalar olayları aboneliği oluşturma

Azure haritalar üç olay türünü destekler. Azure haritalar desteklenen olay türlerine [buradan](https://docs.microsoft.com/azure/event-grid/event-schema-azure-maps)göz atabilirsiniz. Biri, diğeri de çıkış olayları için olmak üzere iki farklı abonelik oluşturacağız.

Bölge giriş olayları için bir olay aboneliği oluşturmak üzere aşağıdaki adımları izleyin. Bölge sınırı çıkış olaylarına benzer bir şekilde abone olabilirsiniz.

1. [Bu portal bağlantısı](https://ms.portal.azure.com/#@microsoft.onmicrosoft.com/dashboard/) aracılığıyla Azure haritalar hesabınıza gidin ve olaylar sekmesini seçin.

   ![Azure haritalar olayları](./media/tutorial-geofence/events-tab.png)

2. Bir olay aboneliği oluşturmak için olaylar sayfasından olay aboneliği ' ni seçin.

   ![Azure haritalar olayları aboneliği](./media/tutorial-geofence/create-event-subscription.png)

3. Olayların aboneliğini adlandırın ve ENTER olay türüne abone olun. Şimdi "uç nokta türü" olarak Web kancası ' yi seçin ve mantıksal uygulamanızın HTTP URL uç noktasını "uç noktaya" kopyalayın

   ![Olay aboneliği](./media/tutorial-geofence/events-subscription.png)


## <a name="use-geofence-api"></a>Geofence API kullanma

Bir **cihazın** (donanımların durumun parçası olduğunu) bir bölge 'nin içinde mi yoksa dışında mı olduğunu denetlemek Için GEOFENCE API 'sini kullanabilirsiniz. Bölge sınırı Get API 'sini daha iyi anlamak için. Belirli bir ekipmanın zaman içinde taşındığı farklı konumlara karşı sorgu yaptık. Aşağıdaki şekilde, belirli bir yapım donanımının, kronolojik sırada gözlemlendiği şekilde benzersiz bir **cihaz kimliğiyle** birlikte beş konumu gösterilmektedir. Bu beş konumların her biri, dilimçine karşı bölge girme ve çıkış durumu değişikliğini değerlendirmek için kullanılır. Bir durum değişikliği gerçekleşirse, bölge sınırı hizmeti, mantıksal uygulamaya Event Grid tarafından gönderilen bir olayı tetikler. Sonuç olarak, işlem yöneticisi bir e-posta yoluyla ilgili ENTER veya çıkış bildirimini alır.

> [!Note]
> Yukarıdaki senaryo ve davranış, aşağıdaki şekilde beş farklı konumu yansıtacak şekilde aynı **cihaz kimliğini** temel alır.

![Bölge Haritası](./media/tutorial-geofence/geofence.png)

Postman uygulamasında, yukarıda oluşturduğunuz koleksiyonda yeni bir sekme açın. Oluşturucu sekmesinde HTTP yöntemini Al ' ı seçin:

Aşağıda, donanımların kronolojik sırada gözlemlendiği gibi farklı ilgili konum koordinatları bulunan beş HTTP GET Geofsel API isteği bulunur. Her isteğin ardından yanıt gövdesi gelir.
 
1. Konum 1:
    
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
   ![Geofence sorgu 1](./media/tutorial-geofence/geofence-query1.png)

   Yukarıdaki yanıta baktığınızda, ana bölge değerinden olumsuz mesafe, ekipmanın bölge bölge içinde olduğu ve alt siteden gelen pozitif bölge, alt site bölge ilişkisinin dışında olduğu anlamına gelir. 

2. Konum 2: 
   
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
    
   ![Geofence sorgu 2](./media/tutorial-geofence/geofence-query2.png)

   Yukarıdaki JSON yanıtına dikkatlice bakarsanız, ekipman alt sitenin dışındadır, ancak ana çit içindedir. Bir olayı tetiklemez ve e-posta gönderilmez.

3. Konum 3: 
  
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Geofence sorgu 3](./media/tutorial-geofence/geofence-query3.png)

   Bir durum değişikliği gerçekleşti ve artık ekipman hem ana hem de alt site bölge içindedir. Bu, bir olay yayımlar ve Operations Manager bir bildirim e-postası gönderilir.

4. Konum 4: 

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.637988&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```
  
   ![Geofence sorgu 4](./media/tutorial-geofence/geofence-query4.png)

   Karşılık gelen yanıtı dikkatle gözlemleyerek, ekipman alt site bölge değerinden çıksa bile burada hiçbir olay yayımlanmadığını unutmayın. Kullanıcının GET isteğinde belirtilen zamanına bakarsanız, alt site bölge kullanım süresinin bu saate göre dolduğunu ve ekipmanın hala ana bölge yolunda olduğunu görebilirsiniz. Yanıt gövdesinde `expiredGeofenceGeometryId` alt sitenin bölge bölge numarasını da görebilirsiniz.


5. Konum 5:
      
   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63799&lon=-122.134505&userTime=2019-01-16&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

   ![Geofence sorgu 5](./media/tutorial-geofence/geofence-query5.png)

   Ekipmanın ana yapım site bölge bölgesine ayrıldığı hakkında bilgi alabilirsiniz. Bir olay yayımlar, ciddi bir ihlal değildir ve Operations Manager kritik bir uyarı e-postası gönderilir.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, verileri karşıya yükleme API 'sini kullanarak Azure Maps, veri hizmeti ' ne karşıya yükleyerek bölge sayısını nasıl ayarlayacaınız. Ayrıca bölge olaylarına abone olmak ve bunları işlemek için Azure Maps olayları kılavuzunu nasıl kullanacağınızı öğrendiniz. 

* Daha karmaşık bir mantık oluşturmak için JSON ayrıştırmak üzere Logic Apps kullanmayı öğrenmek için [Azure Logic Apps içerik türlerini işleme](https://docs.microsoft.com/azure/logic-apps/logic-apps-content-type)bölümüne bakın.
* Event Grid içindeki olay işleyicileri hakkında daha fazla bilgi edinmek için [Event Grid desteklenen olay işleyicileri](https://docs.microsoft.com/azure/event-grid/event-handlers)bölümüne bakın.
