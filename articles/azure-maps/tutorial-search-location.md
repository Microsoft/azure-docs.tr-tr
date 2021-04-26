---
title: 'Öğretici: harita üzerinde yakındaki konumları arama | Microsoft Azure haritaları'
description: Bir harita üzerinde ilgi çekici noktaları arama hakkında öğretici. Bir haritaya arama özellikleri ve etkileşimli açılan kutular eklemek için bkz. Azure Maps web SDK 'sını kullanma.
author: anastasia-ms
ms.author: v-stharr
ms.date: 1/15/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc, devx-track-js
ms.openlocfilehash: 31dd1c06b0f17b469454593131ccdc93b45b2446
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624975"
---
# <a name="tutorial-search-nearby-points-of-interest-using-azure-maps"></a>Öğretici: Azure haritalar 'ı kullanarak yakındaki ilgi noktalarını arama

Bu öğreticide, Azure Haritalar hesabı ayarlama ve sonra Haritalar API’lerini kullanarak ilgi çekici bir noktayı arama işlemleri gösterilmektedir. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure Haritalar hesabı oluşturma
> * Haritalar hesabınızın birincil anahtarını alma
> * Harita denetimi API’sini kullanarak yeni bir web sayfası oluşturma
> * Haritalar arama hizmetini kullanarak yakınlardaki bir ilgi çekici noktayı bulma

## <a name="prerequisites"></a>Önkoşullar

<a id="createaccount"></a>
<a id="getkey"></a>

1. [Azure portalında](https://portal.azure.com) oturum açın. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
2. [Azure haritalar hesabı oluşturma](quick-demo-map-app.md#create-an-azure-maps-account)
3. Birincil anahtar veya abonelik anahtarı olarak da bilinen [birincil bir abonelik anahtarı alın](quick-demo-map-app.md#get-the-primary-key-for-your-account). Azure haritalar 'da kimlik doğrulaması hakkında daha fazla bilgi için bkz. [Azure haritalar 'da kimlik doğrulamasını yönetme](how-to-manage-authentication.md).

<a id="createmap"></a>

## <a name="create-a-new-map"></a>Yeni harita oluşturma

Harita Denetimi API 'SI uygun bir istemci kitaplığı. Bu API, haritaları Web uygulamanızla kolayca tümleştirmenizi sağlar. Çıplak hizmet çağrılarının karmaşıklığını gizler ve özelleştirilebilir bileşenlerle üretkenliğinizi artırır. Aşağıdaki adımlarda, Harita Denetimi API’sinin tümleşik olduğu statik bir HTML sayfasının nasıl oluşturulacağı gösterilmektedir.

1. Yerel makinenizde yeni bir dosya oluşturun ve bu dosyayı **MapSearch.html** olarak adlandırın.
2. Aşağıdaki HTML bileşenlerini dosyaya ekleyin:

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Search</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
        function GetMap(){
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

   HTML üst bilgisinin Azure Harita Denetimi kitaplığı tarafından barındırılan CSS ve JavaScript kaynak dosyalarını içerdiğine dikkat edin. Sayfanın gövdesinde bulunan ve sayfa yüklendiğinde `GetMap` işlevini çağıracak olan `onload` olayına dikkat edin. `GetMap`Bu işlev, Azure Maps API 'lerine erişmek için satır Içi JavaScript kodunu içerecektir.

3. HTML dosyasının `GetMap` işlevine aşağıdaki JavaScript kodunu ekleyin. Dizeyi `<Your Azure Maps Key>` haritalar hesabınızdan kopyaladığınız birincil anahtarla değiştirin.

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

   Bu segment, Azure Haritalar hesap anahtarınız için Harita Denetimi API’sini başlatır. `atlas` , API ve ilgili görsel bileşenleri içeren ad alanıdır. `atlas.Map` görsel ve etkileşimli bir Web haritası için denetim sağlar.

4. Değişikliklerinizi dosyaya kaydedin ve HTML sayfasını bir tarayıcıda açın. Gösterilen eşleme, hesap anahtarınızı kullanarak çağırarak yapabileceğiniz en temel eşlemedir `atlas.Map` .

   ![Haritayı görüntüleme](./media/tutorial-search-location/basic-map.png)

5. `GetMap` işlevinde haritanın başlatıldığı bölümün altına aşağıdaki JavaScript kodunu ekleyin.

    ```JavaScript
    //Wait until the map resources are ready.
    map.events.add('ready', function() {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering point data.
        var resultLayer = new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: 'pin-round-darkblue',
                anchor: 'center',
                allowOverlap: true
            },
            textOptions: {
                anchor: "top"
            }
        });

        map.layers.add(resultLayer);
    });
    ```

   Bu kod segmentinde haritaya bir `ready` olay eklenir, bu, harita kaynakları yüklendiğinde ve eşleme erişilmeye hazırsanız harekete geçilir. Harita `ready` olay işleyicisinde, sonuç verilerini depolamak için bir veri kaynağı oluşturulur. Bir simge katmanı oluşturulur ve veri kaynağına eklenir. Bu katman, veri kaynağındaki sonuç verilerinin nasıl işleneceğini belirtir. Bu durumda, sonuç, sonuç koordinatı üzerinde ortalanan ve diğer simgelerin çakışmasına izin veren koyu mavi yuvarlak pin simgesiyle işlenir. Sonuç katmanı harita katmanlarına eklenir.

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Arama özellikleri ekleme

Bu bölümde, haritalarınızın bir ilgi noktasını bulmak için haritalar [Arama API 'sinin](/rest/api/maps/search) nasıl kullanılacağı gösterilmektedir. Bu, geliştiricilerin adres, ilgi çekici nokta ve diğer coğrafi bilgileri araması için tasarlanmış bir RESTful API’dir. Arama hizmeti, belirtilen bir adrese enlem ve boylam bilgileri atar. Aşağıda açıklanan **Hizmet Modülü**, Haritalar Arama API'si ile konum bulmaya yönelik aramalarda kullanılabilir.

### <a name="service-module"></a>Hizmet Modülü

1. Eşleme `ready` olay işleyicisinde, aşağıdaki JavaScript kodunu ekleyerek arama hizmeti URL 'sini oluşturun.

    ```JavaScript
   // Use SubscriptionKeyCredential with a subscription key
   var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

   // Use subscriptionKeyCredential to create a pipeline
   var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

   // Construct the SearchURL object
   var searchURL = new atlas.service.SearchURL(pipeline); 
   ```

   , `SubscriptionKeyCredential` `SubscriptionKeyCredentialPolicy` Azure Maps 'a ABONELIK anahtarıyla http isteklerinin kimliğini doğrulamak için bir oluşturur. , `atlas.service.MapsURL.newPipeline()` `SubscriptionKeyCredential` İlkeyi alır ve bir işlem [hattı](/javascript/api/azure-maps-rest/atlas.service.pipeline) örneği oluşturur. , `searchURL` Azure Maps [arama](/rest/api/maps/search) işlemlerine yönelik bir URL 'yi temsil eder.

2. Ardından arama sorgusunu oluşturmak için aşağıdaki betik bloğunu ekleyin. Bu, Arama Hizmetinin temel arama API'si olan Belirsiz Arama Hizmetini kullanır. Belirsiz Arama Hizmeti adres, yer ve ilgi çekici nokta (POI) gibi çoğu belirsiz girişi işler. Bu kod, belirtilen enlem ve boylamın belirtilen yarıçapı içinde yakındaki Alipop Istasyonlarını arar. Yanıttan bir GeoJSON Özellik koleksiyonu daha sonra yöntemi kullanılarak ayıklanır `geojson.getFeatures()` ve veri kaynağına eklenir ve bu da otomatik olarak, sembol katmanı aracılığıyla haritada işlenen verilere neden olur. Betiğin son bölümü haritanın [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) özelliğini kullanarak sonuçların sınırlayıcı kutusuna göre harita kamera görünümünü ayarlar.

    ```JavaScript
    var query =  'gasoline-station';
    var radius = 9000;
    var lat = 47.64452336193245;
    var lon = -122.13687658309935;

    searchURL.searchPOI(atlas.service.Aborter.timeout(10000), query, {
        limit: 10,
        lat: lat,
        lon: lon,
        radius: radius
    }).then((results) => {

        // Extract GeoJSON feature collection from the response and add it to the datasource
        var data = results.geojson.getFeatures();
        datasource.add(data);

        // set camera to bounds to show the results
        map.setCamera({
            bounds: data.bbox,
            zoom: 10
        });
    });
    ```

3. **MapSearch.html** dosyasını kaydedin ve tarayıcınızı yenileyin. Alandaki yakıt istasyonların konumları için yuvarlak mavi PIN 'ler ile Seattle üzerinde ortalanmış Haritayı görmeniz gerekir.

   ![Arama sonuçlarıyla haritayı görüntüleme](./media/tutorial-search-location/pins-map.png)

4. Tarayıcınıza aşağıdaki HTTP İsteğini girerek, haritanın işlediği ham verileri görebilirsiniz. \<Your Azure Maps Key\>Birincil anahtarınızla değiştirin.

   ```http
   https://atlas.microsoft.com/search/poi/json?api-version=1.0&query=gasoline%20station&subscription-key=<subscription-key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

Bu noktada MapSearch sayfası, belirsiz arama sorgusundan döndürülen ilgi çekici noktaların konumlarını görüntüleyebilir. Şimdi bazı etkileşimli özellikler ve konumlar hakkında daha fazla bilgi ekleyelim.

## <a name="add-interactive-data"></a>Etkileşimli veri ekleme

Şu ana kadar oluşturduğumuz harita, arama sonuçları için yalnızca boylam/enlem verilerine bakar. Ancak, Haritalar arama hizmetinin döndürdüğü ham JSON, her bir gaz istasyonuyla ilgili ek bilgiler içerir. Ad ve sokak adresi dahil. Etkileşimli açılır kutularla haritada bu verilere yer verebilirsiniz.

1. `ready`Benzer arama hizmetini sorgulamak için koddan sonra Map olay işleyicisine aşağıdaki kod satırlarını ekleyin. Bu kod, bir açılan pencere örneği oluşturur ve sembol katmanına bir gelme olayından olayı ekler.

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```

    API, `*atlas.Popup` haritada gereken konuma bağlanmış bir bilgi penceresi sağlar. 

2. Açılan pencerede, `GetMap` üzerinde kullanılan sonuç bilgisini göstermek için aşağıdaki kodu işlevi içine ekleyin.

    ```JavaScript
    function showPopup(e) {
        //Get the properties and coordinates of the first shape that the event occurred on.

        var p = e.shapes[0].getProperties();
        var position = e.shapes[0].getCoordinates();

        //Create HTML from properties of the selected result.
        var html = `
          <div style="padding:5px">
            <div><b>${p.poi.name}</b></div>
            <div>${p.address.freeformAddress}</div>
            <div>${position[1]}, ${position[0]}</div>
          </div>`;

        //Update the content and position of the popup.
        popup.setPopupOptions({
            content: html,
            position: position
        });

        //Open the popup.
        popup.open(map);
    }
    ```

3. Dosyayı kaydedin ve tarayıcınızı yenileyin. Şimdi tarayıcıdaki haritada, herhangi bir arama raptiyesinin üzerine gelip beklediğinizde bilgi açılan pencereleri gösterilir.

    ![Azure Harita Denetimi ve Arama Hizmeti](./media/tutorial-search-location/popup-map.png)

Bu öğreticinin tam kodunu görüntülemek için [buraya](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html)tıklayın. Canlı örneği görüntülemek için [buraya](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest) tıklayın

## <a name="clean-up-resources"></a>Kaynakları temizleme

Temizleme gerektiren kaynak yok.

## <a name="next-steps"></a>Sonraki adımlar

Sonraki öğreticide, iki konum arasındaki bir yolun nasıl görüntüleneceği gösterilmektedir.

> [!div class="nextstepaction"]
> [Bir hedefe yönlendirme](./tutorial-route-location.md)