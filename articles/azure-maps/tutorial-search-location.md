---
title: 'Öğretici: Azure haritalar ile arama'
description: "Öğretici: Azure haritalar 'ı kullanarak yakın ilgi çekici bir noktada arama yapın"
author: walsehgal
ms.author: v-musehg
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 86ba98bfb4003a723cfaaf2d1c51301314ca47cf
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109520"
---
# <a name="tutorial-search-nearby-points-of-interest-using-azure-maps"></a>Öğretici: Azure haritalar 'ı kullanarak yakındaki ilgi noktalarını arama

Bu öğreticide, Azure Haritalar hesabı ayarlama ve sonra Haritalar API’lerini kullanarak ilgi çekici bir noktayı arama işlemleri gösterilmektedir. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure Haritalar hesabı oluşturma
> * Haritalar hesabınızın birincil anahtarını alma
> * Harita denetimi API’sini kullanarak yeni bir web sayfası oluşturma
> * Haritalar arama hizmetini kullanarak yakınlardaki bir ilgi çekici noktayı bulma

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

[Azure portalında](https://portal.azure.com) oturum açın.

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Azure Maps hesabı oluşturma

Aşağıdaki adımları uygulayarak yeni bir Haritalar hesabı oluşturun:

1. [Azure portalının](https://portal.azure.com) sol üst köşesinde bulunan **Kaynak oluştur** öğesine tıklayın.
2. *Market’te Ara* kutusuna **Haritalar** yazın.
3. *Sonuçlar* içinden **Haritalar**’ı seçin. Haritanın altında görüntülenen **Oluştur** düğmesine tıklayın.
4. **Haritalar Hesabı Oluştur** sayfasında aşağıdaki değerleri girin:
    * Bu hesap için kullanmak istediğiniz *Abonelik*.
    * Bu hesap için *Kaynak grubu* adı. Kaynak grubu için *Yeni oluştur* veya *Mevcut olanı kullan* seçeneğini belirleyebilirsiniz.
    * Yeni hesabınıza verilen *Ad*.
    * Bu hesabın *fiyatlandırma katmanı* .
    * *Lisans*’ı ve *Gizlilik Bildirimi*’ni okuyun ve onay kutusunu işaretleyerek koşulları kabul edin.
    * **Oluştur** düğmesine tıklayın.

![Portalda Haritalar hesabı oluşturma](./media/tutorial-search-location/create-account.png)

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Hesabınızın birincil anahtarını alma

Haritalar hesabınız başarıyla oluşturulduktan sonra, Haritalar API’lerini sorgulamanıza olanak sağlayan anahtarı alın.

1. Portalda Haritalar hesabınızı açın.
2. Ayarlar bölümünde **kimlik doğrulaması**' nı seçin.
3. **Birincil Anahtar**’ı panonuza kopyalayın. Bu öğreticinin ilerleyen kısmında kullanmak üzere bunu yerel olarak kaydedin.

![Portalda Birincil Anahtar’ı alma](./media/tutorial-search-location/get-key.png)

<a id="createmap"></a>

## <a name="create-a-new-map"></a>Yeni harita oluşturma

Harita Denetimi API’si, Haritalar’ı web uygulamanızla kolayca tümleştirmenize olanak tanıyan kullanışlı bir istemci kitaplığıdır. Açık REST hizmet çağrılarının karmaşıklığını gizlemesinin yanı sıra, stili değiştirilebilen ve özelleştirilebilen bileşenlerle üretkenliğinizi arttırır. Aşağıdaki adımlarda, Harita Denetimi API’sinin tümleşik olduğu statik bir HTML sayfasının nasıl oluşturulacağı gösterilmektedir.

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

   HTML üst bilgisinin Azure Harita Denetimi kitaplığı tarafından barındırılan CSS ve JavaScript kaynak dosyalarını içerdiğine dikkat edin. Sayfanın gövdesinde bulunan ve sayfa yüklendiğinde `onload` işlevini çağıracak olan `GetMap` olayına dikkat edin. `GetMap` işlevi, Azure Maps API 'Lerine erişmek için satır içi JavaScript kodunu içerecektir.

3. HTML dosyasının `GetMap` işlevine aşağıdaki JavaScript kodunu ekleyin. Dize `<Your Azure Maps Key>`, haritalar hesabınızdan kopyaladığınız birincil anahtarla değiştirin.

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

   Bu segment, Azure Haritalar hesap anahtarınız için Harita Denetimi API’sini başlatır. `atlas`, API ve ilgili görsel bileşenleri içeren ad alanıdır. `atlas.Map`, görsel ve etkileşimli bir Web haritası için denetim sağlar.

4. Değişikliklerinizi dosyaya kaydedin ve HTML sayfasını bir tarayıcıda açın. Bu, hesap anahtarınızı kullanarak `atlas.Map` çağırarak yapabileceğiniz en temel eşlemedir.

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

   Bu kod segmentinde haritaya bir `ready` olayı eklenir, bu, harita kaynakları yüklendiğinde ve eşleme erişilmeye hazırsanız harekete geçilir. Map `ready` olay işleyicisinde, sonuç verilerini depolamak için bir veri kaynağı oluşturulur. Bir simge katmanı oluşturulur ve veri kaynağına eklenir. Bu katman, veri kaynağındaki sonuç verilerin nasıl işleneceğini belirtir. Bu örnekte sonuç koordinatlarının üzerinde ortalanmış olan ve diğer simgelerin çakışmasına izin verilen koyu mavi yuvarlak raptiye simgesi şeklindedir. Sonuç katmanı harita katmanlarına eklenir.

<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Arama özellikleri ekleme

Bu bölümde, haritalarınızın bir ilgi noktasını bulmak için haritalar [Arama API 'sinin](https://docs.microsoft.com/rest/api/maps/search) nasıl kullanılacağı gösterilmektedir. Bu, geliştiricilerin adres, ilgi çekici nokta ve diğer coğrafi bilgileri araması için tasarlanmış bir RESTful API’dir. Arama hizmeti, belirtilen bir adrese enlem ve boylam bilgileri atar. Aşağıda açıklanan **Hizmet Modülü**, Haritalar Arama API'si ile konum bulmaya yönelik aramalarda kullanılabilir.

### <a name="service-module"></a>Hizmet Modülü

1. Map `ready` olay işleyicisinde, aşağıdaki JavaScript kodunu ekleyerek arama hizmeti URL 'sini oluşturun.

    ```JavaScript
   // Use SubscriptionKeyCredential with a subscription key
   var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

   // Use subscriptionKeyCredential to create a pipeline
   var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

   // Construct the SearchURL object
   var searchURL = new atlas.service.SearchURL(pipeline); 
   ```

   `SubscriptionKeyCredential`, Azure Maps 'a yönelik HTTP isteklerinin kimliğini abonelik anahtarıyla doğrulamak için bir `SubscriptionKeyCredentialPolicy` oluşturur. `atlas.service.MapsURL.newPipeline()`, `SubscriptionKeyCredential` ilkesinde sürer ve bir işlem [hattı](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest) örneği oluşturur. `searchURL`, Azure Maps [arama](https://docs.microsoft.com/rest/api/maps/search) işlemlerine yönelik bir URL 'yi temsil eder.

2. Ardından arama sorgusunu oluşturmak için aşağıdaki betik bloğunu ekleyin. Bu, Arama Hizmetinin temel arama API'si olan Belirsiz Arama Hizmetini kullanır. Belirsiz Arama Hizmeti adres, yer ve ilgi çekici nokta (POI) gibi çoğu belirsiz girişi işler. Bu kod, belirtilen enlem ve boylamın belirtilen yarıçapı içinde yakındaki Alipop Istasyonlarını arar. Yanıttan bir GeoJSON Özellik koleksiyonu daha sonra `geojson.getFeatures()` yöntemi kullanılarak ayıklanır ve veri kaynağına eklenir. Bu, otomatik olarak, sembol katmanı aracılığıyla haritada işlenen verilere neden olur. Betiğin son bölümü haritanın [setCamera](/javascript/api/azure-maps-control/atlas.map#setcamera-cameraoptions---cameraboundsoptions---animationoptions-) özelliğini kullanarak sonuçların sınırlayıcı kutusuna göre harita kamera görünümünü ayarlar.

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

3. **MapSearch.html** dosyasını kaydedin ve tarayıcınızı yenileyin. Artık haritanın, alanda akarine istasyonların konumlarını işaretleyen yuvarlak mavi PIN 'ler ile Seattle üzerinde ortalanmasını görmeniz gerekir.

   ![Arama sonuçlarıyla haritayı görüntüleme](./media/tutorial-search-location/pins-map.png)

4. Tarayıcınıza aşağıdaki HTTP İsteğini girerek, haritanın işlediği ham verileri görebilirsiniz. \<Azure Haritalar Anahtarınız\> değerini birincil anahtarınızla değiştirin.

   ```http
   https://atlas.microsoft.com/search/poi/json?api-version=1.0&query=gasoline%20station&subscription-key=<subscription-key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

Bu noktada MapSearch sayfası, belirsiz arama sorgusundan döndürülen ilgi çekici noktaların konumlarını görüntüleyebilir. Şimdi bazı etkileşimli özellikler ve konumlar hakkında daha fazla bilgi ekleyelim.

## <a name="add-interactive-data"></a>Etkileşimli veri ekleme

Şu ana kadar oluşturduğumuz harita, arama sonuçları için yalnızca boylam/enlem verilerine bakar. Ancak Haritalar Arama hizmetinin döndürdüğü işlenmemiş JSON’a bakarsanız, bunun ad ve açık adres de dahil olmak üzere her bir benzin istasyonuyla ilgili ek bilgiler içerdiğini görürsünüz. Etkileşimli açılır kutularla haritada bu verilere yer verebilirsiniz.

1. Benzer arama hizmetini sorgulamak için koddan sonra Map `ready` olay işleyicisine aşağıdaki kod satırlarını ekleyin. Bu kod bir açılır pencere örneği oluşturur ve simge katmanına fare ile üzerine gelindiğinde gerçekleştirilecek bir olay ekler.

    ```JavaScript
   //Create a popup but leave it closed so we can update it and display it later.
    popup = new atlas.Popup();

    //Add a mouse over event to the result layer and display a popup when this event fires.
    map.events.add('mouseover', resultLayer, showPopup);
    ```

    API `*atlas.Popup`, haritada gereken konuma bağlanmış bir bilgi penceresi sağlar. 

2. Açılan pencerede kullanılan sonuç bilgilerini görüntülemek için `GetMap` işlevi içine aşağıdaki kodu ekleyin.

    ```JavaScript
    function showPopup(e) {
        //Get the properties and coordinates of the first shape that the event occured on.

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

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Azure Maps hesabı oluşturma
> * Hesabınızın birincil anahtarını alma
> * Harita Denetimi API’sini kullanarak yeni web sayfası oluşturma
> * Arama Hizmeti’ni kullanarak yakınlardaki istenen konumları bulma

> [!div class="nextstepaction"]
> [Tam kaynak kodunu görüntüle](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/search.html)

> [!div class="nextstepaction"]
> [Canlı örneği görüntüle](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20for%20points%20of%20interest)

Sonraki öğreticide, iki konum arasındaki bir yolun nasıl görüntüleneceği gösterilmektedir.

> [!div class="nextstepaction"]
> [Bir hedefe yönlendirme](./tutorial-route-location.md)
