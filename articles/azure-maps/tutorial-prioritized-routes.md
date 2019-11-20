---
title: 'Öğretici: Azure Maps ile birden çok yol'
description: 'Öğretici: Azure Maps kullanarak farklı seyahat modları için rotalar bulma'
author: walsehgal
ms.author: v-musehg
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 07391633436931a8f7cc46c430387f5877d10674
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74106714"
---
# <a name="tutorial-find-routes-for-different-modes-of-travel-using-azure-maps"></a>Öğretici: Azure Maps kullanarak farklı seyahat modları için rotalar bulma

Bu öğreticide, istediğiniz konuma yönelik ve ulaşım yönteminize göre önceliklendirilmiş yol tarifleri almak için Azure Haritalar hesabınız ile yol hizmetini nasıl kullanacağınız gösterilmektedir. Haritanızda, biri arabalar, diğeri ise yükseklik, ağırlık ya da tehlikeli yük nedeniyle yol kısıtlaması olabilecek kamyonlar için olmak üzere iki farklı yol görüntüleyin. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Harita denetimi API’sini kullanarak yeni bir web sayfası oluşturma
> * Haritanızda trafik akışını görselleştirme
> * Seyahat modunu bildiren yol sorguları oluşturma
> * Haritanızda birden fazla yol görüntüleme

## <a name="prerequisites"></a>Önkoşullar

Devam etmeden önce, S1 Fiyatlandırma Katmanı içeren bir Azure Maps hesabı aboneliği oluşturmak için [Hesabı Yönet](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) bölümündeki yönergeleri izleyin ve hesabınıza ilişkin birincil abonelik anahtarını almak için [birincil anahtar al](./tutorial-search-location.md#getkey) bölümündeki adımları izleyin.

## <a name="create-a-new-map"></a>Yeni harita oluşturma

Aşağıdaki adımlarda, Harita Denetimi API’sinin tümleşik olduğu statik bir HTML sayfasının nasıl oluşturulacağı gösterilmektedir.

1. Yerel makinenizde yeni bir dosya oluşturun ve bu dosyayı **MapTruckRoute.html** olarak adlandırın.
2. Aşağıdaki HTML bileşenlerini dosyaya ekleyin:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

    HTML üst bilgisinin Azure Harita Denetimi kitaplığı tarafından barındırılan CSS ve JavaScript kaynak dosyalarını içerdiğine dikkat edin. Sayfanın gövdesinde bulunan ve sayfa yüklendiğinde `onload` işlevini çağıracak olan `GetMap` olayına dikkat edin. Bu işlev, Azure Haritalar API’lerine erişime yönelik satır içi JavaScript kodunu içerir.

3. `GetMap` işlevine aşağıdaki JavaScript kodunu ekleyin. Dize `<Your Azure Maps Key>`, haritalar hesabınızdan kopyaladığınız birincil anahtarla değiştirin.

    ```JavaScript
    //Instantiate a map object
    var map = new atlas.Map("myMap", {
        //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
        authOptions: {
            authType: 'subscriptionKey',
            subscriptionKey: '<Your Azure Maps Key>'
        }
    });
    ```

    `atlas.Map` sınıfı, bir görsel ve etkileşimli Web eşlemesi için denetim sağlar ve Azure Harita Denetimi API 'sinin bir bileşenidir.

4. Dosyayı kaydedin ve tarayıcınızda açın. Bu noktada, daha fazla geliştirebileceğiniz temel bir haritanız olur.

   ![Temel haritayı görüntüleme](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Trafik akışını görselleştirme

1. Haritaya trafik akışı görüntüsünü ekleyin. Haritalar `ready` olayı, haritalar kaynakları yüklenene ve güvenle etkileşim kurmaya başlamaya kadar bekler.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    Map `ready` olay işleyicisinde, haritadaki trafik akışı ayarı, boş akışa göre yolun hızı olan `relative`olarak ayarlanır. Bunu trafik hızı olan `absolute` veya serbest akışla farklılık gösteren bağıl hız `relative-delay` olarak da ayarlayabilirsiniz.

2. **MapTruckRoute.html** dosyasını kaydedin ve tarayıcınızın sayfasını yenileyin. Eşlemle etkileşim kurabilir ve Los Angeles 'ta yakınlaştırırsanız, geçerli trafik verileriyle birlikte bir cadde görmeniz gerekir.

   ![Trafik haritasını görüntüleme](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Rotanın nasıl işleneceğini tanımlama

Bu öğreticide iki rota hesaplanacak ve haritada işlenecektir. Rotalardan biri arabalar, diğeri de tırlar için kullanılabilir durumdaki yolları kullanacaktır. İşleme tamamlandığında rotanın başlangıcı ve bitişi için bir simgeye ek olarak her rota yolu için farklı renkte çizgiler görüntülenir.

1. Eşlemeyi başlattıktan sonra Maps `ready` olay işleyicisine aşağıdaki JavaScript kodunu ekleyin.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('ready', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: ['get', 'strokeColor'],
            strokeWidth: ['get', 'strokeWidth'],
            lineJoin: 'round',
            lineCap: 'round'
        }), 'labels');

        //Add a layer for rendering point data.
        map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: ['get', 'icon'],
                allowOverlap: true
            },
            textOptions: {
                textField: ['get', 'title'],
                offset: [0, 1.2]
            },
            filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']] //Only render Point or MultiPoints in this layer.
        }));
    });
    ```
    
    Haritalar `ready` olay işleyicisinde, yol çizgilerinin yanı sıra başlangıç ve bitiş noktalarını depolamak için bir veri kaynağı oluşturulur. Bir çizgi katmanı oluşturulup veri kaynağına eklenir ve rota çizgisinin nasıl işleneceği tanımlanır. Rota çizgisi özelliğinden çizgi kalınlığını ve rengini almak için ifadeler kullanılır. Katman haritaya eklenirken bu katmanın harita etiketlerinin altında işlenmesi gerektiğini belirten `'labels'` değerine sahip ikinci bir parametre geçirilir. Bu parametre, rota çizgisinin yol etiketlerini kapatmamasını sağlar. Bir simge katmanı oluşturulur ve veri kaynağına eklenir. Bu katman, başlangıç ve bitiş noktalarının nasıl işleneceğini belirtir. Bu durumda her bir nokta nesnesinin özelliklerinden simge görüntüsünü ve metin etiketi bilgilerini almak için ifadeler eklenmiştir. 
    
2. Bu öğreticide başlangıç noktasını Seattle’daki Fabrikam adlı hayali şirket, varış noktasını ise bir Microsoft ofisi olarak ayarlayın. Haritalar `ready` olay işleyicisinde aşağıdaki kodu ekleyin.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-round-blue'
    });
    ```

    Bu kod, yolun başlangıç ve bitiş noktalarını temsil eden iki [GeoJSON nesnesi](https://en.wikipedia.org/wiki/GeoJSON) oluşturur. Her noktaya `title` ve `icon` özelliği eklenir.

3. Ardından haritaya başlangıç ve bitiş noktalarını sabitlemek için aşağıdaki JavaScript kodunu ekleyin:

    ```JavaScript
    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });
    ```

    Başlangıç ve bitiş noktaları veri kaynağına eklenir. Başlangıç ve bitiş noktaları için sınırlayıcı kutu, `atlas.data.BoundingBox.fromData` işlevi kullanılarak hesaplanır. Bu sınırlama kutusu, `map.setCamera` işlevini kullanarak tüm yol üzerinde harita kameralarının görünümünü ayarlamak için kullanılır. Simgelerin piksel boyutlarını telafi etmek için iç boşluk eklenir.

4. Dosyayı kaydedin ve iğneleri haritanızda görmek için tarayıcınızı yenileyin. Şimdi harita, Seattle üzerinde ortalanır ve başlangıç noktasının yuvarlak mavi raptiyeyle ve bitiş noktasının mavi raptiyeyle işaretlendiğini görebilirsiniz.

   ![Başlangıç ve bitiş noktaları ile haritayı görüntüleme](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Ulaşım yöntemine göre önceliklendirilmiş yolları işleme

Bu bölümde, taşıma modınızdan yola çıkarak belirli bir başlangıç noktasından son noktaya birden çok yol bulmak için Maps Route Service API 'sinin nasıl kullanılacağı gösterilmektedir. Yönlendirme hizmeti, mevcut trafik koşullarını göz önünde bulundurarak iki konum arasındaki *en hızlı*, *en kısa*, *ekonomik* veya *heyecan verici* yolları planlamak için API’ler sağlar. Ayrıca, Azure’ın geçmişe ait kapsamlı trafik veritabanını kullanarak herhangi bir gün ve saat için yol süresini tahmin eder. Böylece kullanıcıların ileri bir tarih için yol tarifi alabilmesini sağlar. Daha fazla bilgi için bkz. [getRouteDirections](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Aşağıdaki kod bloklarının harita tamamen yüklendikten sonra yüklendiğinden emin olmak için tümü **map load eventListener** içine eklenmelidir.

1. GetMap işlevinde, JavaScript koduna aşağıdakini ekleyin.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential`, Azure Maps 'a yönelik HTTP isteklerinin kimliğini abonelik anahtarıyla doğrulamak için bir `SubscriptionKeyCredentialPolicy` oluşturur. `atlas.service.MapsURL.newPipeline()`, `SubscriptionKeyCredential` ilkesinde sürer ve bir işlem [hattı](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) örneği oluşturur. `routeURL`, Azure Maps [yönlendirme](https://docs.microsoft.com/rest/api/maps/route) işlemlerine yönelik bir URL 'yi temsil eder.

2. Kimlik bilgilerini ve URL 'yi ayarladıktan sonra, USHazmatClass2 sınıflandırılan kargo 'yu taşıyan bir kamyon için başlangıçtan bitiş noktasına bir yol oluşturmak üzere aşağıdaki JavaScript kodunu ekleyin ve sonuçları görüntüleyin.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request for a truck vehicle type
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates,{
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```

    Yukarıdaki kod parçacığı, Azure Maps yönlendirme hizmetini [Getrouteyönleri](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) yöntemiyle sorgular. Daha sonra yol satırı, `geojson.getFeatures()` yöntemi kullanılarak ayıklanan yanıttan coğrafi JSON özelliği koleksiyonundan ayıklanır. Yol satırı daha sonra veri kaynağına eklenir. Ayrıca, veri kaynağındaki diğer satırlardan önce oluşturulmasını sağlamak için 0 dizini ekler. Bunun nedeni, tır rotası hesaplama işlemlerinin araba rotası hesaplama işlemlerinden genellikle daha yavaş gerçekleştirilmesi ve tır rotasının araba rotasından sonra veri kaynağına eklenmesi durumunda üstünde işlenecek olmasıdır. Kamyonun yol satırına iki özellik eklenir, iyi bir mavi gölge ve dokuz piksellik bir vuruş genişliği.

3. Bir otomobil için yol oluşturmak ve sonuçları göstermek için aşağıdaki JavaScript kodunu ekleyin.

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.  
        datasource.add(routeLine);
    });
    ```

    Yukarıdaki kod parçacığı, Azure Maps yönlendirme hizmetini [Getrouteyönleri](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest) yöntemiyle sorgular. Daha sonra yol satırı, `geojson.getFeatures()` yöntemi kullanılarak ayıklanan yanıttan coğrafi JSON özelliği koleksiyonundan ayıklanır. Yol satırı daha sonra veri kaynağına eklenir. Oto yol satırına iki özellik eklenir, Mor Gölge olan bir kontur rengi ve beş piksellik bir kontur genişliği.  

4. **MapTruckRoute.html** dosyasını kaydedin ve tarayıcınızı yenileyerek sonucu gözlemleyin. Haritalar API’leriyle başarılı bir şekilde bağlantı kurulması için aşağıdakine benzer bir harita görürsünüz.

    ![Azure Yönlendirme Hizmeti ile önceliklendirilen yollar](./media/tutorial-prioritized-routes/prioritized-routes.png)

    Kamyon yolu mavi ve daha kalınken araba yolu mor ve daha ince gösterilir. Araba yolu konut alanlarının altındaki tünellerden geçen I-90 üzerinden Washington Gölü’nü geçer ve bu nedenle tehlikeli atık yükünü kısıtlar. USHazmatClass2 yük tipini belirten kamyon yolu ise farklı bir otoyol kullanmak üzere doğru şekilde yönlendirilir.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Harita denetimi API’sini kullanarak yeni bir web sayfası oluşturma
> * Haritanızda trafik akışını görselleştirme
> * Seyahat modunu bildiren yol sorguları oluşturma
> * Haritanızda birden fazla yol görüntüleme

> [!div class="nextstepaction"]
> [Tam kaynak kodunu görüntüle](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

> [!div class="nextstepaction"]
> [Canlı örneği görüntüle](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

Sonraki öğreticide, Azure Maps kullanarak basit bir depolama Konumlandırıcı oluşturma işlemi gösterilmektedir.

> [!div class="nextstepaction"]
> [Azure haritalar kullanarak bir mağaza Bulucu oluşturma](./tutorial-create-store-locator.md)

> [!div class="nextstepaction"]
> [Veri tabanlı stil ifadeleri kullanın](data-driven-style-expressions-web-sdk.md)