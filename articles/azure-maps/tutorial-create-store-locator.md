---
title: 'Öğretici: Azure haritalar kullanarak bir mağaza Bulucu oluşturma'
description: 'Öğretici: Azure Maps kullanarak bir mağaza Bulucu oluşturun.'
author: walsehgal
ms.author: v-musehg
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 52deb1cf872176b69975d550dd89d870b34d9bf0
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74107077"
---
# <a name="tutorial-create-a-store-locator-by-using-azure-maps"></a>Öğretici: Azure Maps kullanarak bir mağaza Bulucu oluşturma

Bu öğretici, Azure haritalar 'ı kullanarak basit bir depolama Konumlandırıcı oluşturma sürecinde size rehberlik eder. Mağaza bulleyicileri ortaktır. Bu tür uygulamalarda kullanılan kavramların birçoğu, diğer birçok uygulama türü için geçerlidir. Müşterilere bir mağaza bulucunun teklif etmek, doğrudan tüketicilere satış yapan işletmelerin bir sunudur. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
    
> [!div class="checklist"]
> * Azure Harita Denetimi API 'sini kullanarak yeni bir Web sayfası oluşturun.
> * Bir dosyadan özel verileri yükleyin ve bir haritada görüntüleyin.
> * Azure haritalar arama hizmetini kullanarak bir adres bulun veya bir sorgu girin.
> * Tarayıcıdan kullanıcının konumunu alın ve haritada görüntüleyin.
> * Haritada özel semboller oluşturmak için birden çok katmanı birleştirin.  
> * Küme veri noktaları.  
> * Haritaya yakınlaştırma denetimleri ekleyin.

<a id="Intro"></a>

[Canlı mağaza Bulucu örneğine](https://azuremapscodesamples.azurewebsites.net/?sample=Simple%20Store%20Locator) veya [kaynak koduna](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator)atlayın. 

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticideki adımları tamamlayabilmeniz için öncelikle [Azure haritalar hesabınızı oluşturmanız](./tutorial-search-location.md#createaccount) ve hesabınızın birincil abonelik anahtarını almak için [birincil anahtar al](./tutorial-search-location.md#getkey) bölümündeki adımları izlemeniz gerekir.

## <a name="design"></a>Tasarım

Koda geçmeden önce tasarım ile başlamak iyi bir fikirdir. Mağaza konumlarınız, olmasını istediğiniz kadar basit veya karmaşık olabilir. Bu öğreticide, basit bir mağaza Bulucu oluşturacağız. Tercih ediyorsanız bazı işlevleri genişletmenize yardımcı olmak için bazı ipuçları ekledik. Contoso kahve adlı kurgusal bir şirket için mağaza Bulucu oluşturacağız. Aşağıdaki şekilde, bu öğreticide oluşturduğumuz mağaza bulucunun genel düzeninin bir tel kafes 'i gösterilmektedir:

<br/>
<center>

Contoso Coffee kahve dükkanı için mağaza bulucunun ![tel kafes](./media/tutorial-create-store-locator/SimpleStoreLocatorWireframe.png)</center>

Bu mağaza bulucunun kullanışlılığını en üst düzeye çıkarmak için, bir kullanıcının ekran genişliği 700 pikselden daha küçük olduğunda ayarlayan bir yanıt veren düzen ekledik. Hızlı yanıt veren bir düzen, mağaza bulucunun bir mobil cihazda olduğu gibi küçük bir ekranda kullanılmasını kolaylaştırır. Küçük ekran düzeninin tel kafesi aşağıda verilmiştir:  

<br/>
<center>

bir mobil cihazda contoso kahve Mağazası bulucunun ![tel kafes](./media/tutorial-create-store-locator/SimpleStoreLocatorMobileWireframe.png)</center>

Wireframes oldukça basittir bir uygulama gösterir. Uygulamanın bir arama kutusu, yakındaki mağazaların listesi, bazı işaretçileri olan bir eşlem (semboller) ve Kullanıcı bir işaretleyici seçtiğinde ek bilgi görüntüleyen bir açılır pencere. Daha ayrıntılı bilgi için, bu öğreticide bu mağaza bulucusunu geliştirdiğimiz özellikler şunlardır:

* İçeri aktarılan sekmeyle ayrılmış veri dosyasındaki tüm konumlar haritaya yüklenir.
* Kullanıcı Haritayı açabilir ve yakınlaştırabilir, bir arama gerçekleştirebilir ve konumumu GPS düğmesini seçebilir.
* Sayfa düzeni, cihaz ekranının genişliğine göre ayarlanır.  
* Bir üst bilgi mağaza logosunu gösterir.  
* Kullanıcı, adres, posta kodu veya şehir gibi bir konum aramak için bir arama kutusu ve arama düğmesi kullanabilir. 
* Arama kutusuna eklenen `keypress` bir olay, Kullanıcı ENTER tuşuna bastığında aramayı tetikler. Bu işlevsellik genellikle daha fazla bakmış olsa da daha iyi bir kullanıcı deneyimi oluşturur.
* Harita taşırken, eşlemenin merkezinden her konum için uzaklık hesaplanır. Sonuçlar listesi haritanın en üstündeki konumları görüntüleyecek şekilde güncelleştirilir.  
* Sonuçlar listesinde bir sonuç seçtiğinizde, eşleme seçilen konumun üzerine ortalanır ve konum hakkındaki bilgiler açılır pencerede görüntülenir.  
* Haritada belirli bir konumun seçilmesi bir açılır pencereyi de tetikler.
* Kullanıcı uzaklaşır konumlar kümeler halinde gruplandırılır. Kümeler, dairenin içinde sayı olan bir daireyle temsil edilir. Kullanıcı yakınlaştırma düzeyini değiştirdiğinde kümeler formu ve ayrı ayrı.
* Bir kümeyi seçmek, iki düzeyi haritada ve kümenin konumunu üzerine ortalar.

<a id="create a data-set"></a>

## <a name="create-the-store-location-dataset"></a>Depo konumu veri kümesini oluşturma

Bir mağaza Bulucu uygulaması geliştirmeden önce, haritada göstermek istediğimiz mağazaların bir veri kümesi oluşturuyoruz. Bu öğreticide, contoso kahve adlı kurgusal bir kafeterde bir veri kümesi kullanacağız. Bu basit mağaza bulucunun veri kümesi bir Excel çalışma kitabında yönetilir. Veri kümesi, dokuz ülkede/bölgede 10.213 contoso kahve kahve dükkanı yayma konumları içerir: Birleşik Devletler, Kanada, Birleşik Krallık, Fransa, Almanya, Italya, Hollanda, Danimarka ve Ispanya. Verilerin nasıl göründüğünü aşağıda görebilirsiniz:

<br/>
<center>

bir Excel çalışma kitabındaki mağaza Konumlandırıcı verilerinin ekran görüntüsünü ![](./media/tutorial-create-store-locator/StoreLocatorDataSpreadsheet.png)</center>

[Excel çalışma kitabını indirebilirsiniz](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data). 

Verilerin ekran görüntüsüne bakarak aşağıdaki gözlemleri yapabiliriz:
    
* Konum bilgileri **Adressatırı**, **şehir**, **municipsellik** (ilçe), **Adminbölüm** (Eyalet/bölge), **Postcode** (posta kodu) ve **ülke** sütunları kullanılarak depolanır.  
* **Enlem** ve **boylam** sütunları, her contoso Coffee kahve dükkanı mağaza konumunun koordinatlarını içerir. Koordinat bilgilerine sahip değilseniz, Konum koordinatlarını öğrenmek için Azure haritalar 'daki arama hizmetleri ' ni kullanabilirsiniz.
* Bazı ek sütunlar, kafeterlerle ilgili meta veriler içerir: bir telefon numarası, Wi-Fi etkin noktası için Boole sütunları ve wheelsandalye erişilebilirliği ve açılış ve kapanış zamanlarını 24 saat biçiminde depola. Konum verilerinize daha uygun olan meta verileri içeren kendi sütunlarınızı oluşturabilirsiniz.

> [!Note]
> Azure Maps, verileri küresel Mercator projeksiyonu "EPSG: 3857" olarak işler, ancak WGS84 Datum kullanan "EPSG: 4325" içindeki verileri okur. 

Veri kümesini uygulamada açığa çıkarmak için birçok yol vardır. Bir yaklaşım, verileri bir veritabanına yüklemek ve verileri sorgulayan ve sonuçları kullanıcının tarayıcısına gönderen bir Web hizmetini kullanıma sunmasıdır. Bu seçenek, büyük veri kümeleri veya sık güncellenen veri kümeleri için idealdir. Ancak, bu seçenek önemli ölçüde daha fazla geliştirme çalışması gerektirir ve maliyeti daha yüksektir. 

Başka bir yaklaşım, bu veri kümesini tarayıcının kolayca ayrıştırabileceği düz bir metin dosyasına dönüştürmesidir. Dosyanın kendisi, uygulamanın geri kalanı ile barındırılabilir. Bu seçenek, şeyleri basit tutar, ancak kullanıcı tüm verileri indirdiğinden daha küçük veri kümeleri için iyi bir seçenektir. Veri dosyasının boyutu 1 MB 'tan küçük olduğundan bu veri kümesi için düz metin dosyası kullanıyoruz.  

Çalışma kitabını düz metin dosyasına dönüştürmek için çalışma kitabını sekmeyle ayrılmış bir dosya olarak kaydedin. Her sütun bir sekme karakteriyle sınırlandırılır, bu da sütunları kodlarımızda ayrıştırmayı kolaylaştırır. Virgülle ayrılmış değer (CSV) biçimi kullanabilirsiniz, ancak bu seçenek daha fazla ayrıştırma mantığı gerektirir. İçinde virgül olan herhangi bir alan, tırnak işaretleriyle kaydırılır. Bu verileri Excel 'de sekmeyle ayrılmış bir dosya olarak dışarı aktarmak için **farklı kaydet**' i seçin. **Farklı kaydet türü** aşağı açılan listesinde **metin (sekmeyle sınırlandırılmış) (*. txt)** seçeneğini belirleyin. Dosyayı *ContosoCoffee. txt*olarak adlandırın. 

<br/>
<center>

Farklı Kaydet tür iletişim kutusunun ekran görüntüsünü ![](./media/tutorial-create-store-locator/SaveStoreDataAsTab.png)</center>

Metin dosyasını Not defteri 'nde açarsanız, aşağıdaki şekle benzer şekilde görünür:

<br/>
<center>

sekmeyle ayrılmış bir veri kümesi gösteren bir not defteri dosyasının ekran görüntüsünü ![](./media/tutorial-create-store-locator/StoreDataTabFile.png)</center>


## <a name="set-up-the-project"></a>Projeyi ayarlama

Projeyi oluşturmak için, [Visual Studio 'yu](https://visualstudio.microsoft.com) veya seçtiğiniz kod düzenleyicisini kullanabilirsiniz. Proje klasörünüzde üç dosya oluşturun: *index. html*, *index. css*ve *index. js*. Bu dosyalar, uygulamanın yerleşimini, stilini ve mantığını tanımlar. *Data* adlı bir klasör oluşturun ve klasöre *ContosoCoffee. txt* ekleyin. *Görüntüler*adlı başka bir klasör oluşturun. Haritada simgeler, düğmeler ve işaretçiler için bu uygulamada on görüntü kullanıyoruz. [Bu görüntüleri indirebilirsiniz](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/data). Proje klasörünüz artık aşağıdaki şekilde görünmelidir:

<br/>
<center>

Basit depo Konumlandırıcı proje klasörünün ekran görüntüsünü ![](./media/tutorial-create-store-locator/StoreLocatorVSProject.png)</center>

## <a name="create-the-user-interface"></a>Kullanıcı arabirimini oluşturma

Kullanıcı arabirimini oluşturmak için *index. html*dosyasına kod ekleyin:

1. Aşağıdaki `meta` etiketlerini *index. html*`head` ekleyin. Etiketler, (UTF-8) karakter kümesini tanımlar, Internet Explorer ve Microsoft Edge 'in en son tarayıcı sürümlerini kullanmasını söyler ve yanıt veren düzenler için iyi bir görünüm penceresi belirtir.

    ```HTML
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="IE=Edge">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

1. Azure Maps Web Control JavaScript ve CSS dosyalarına başvurular ekleyin:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

1. Azure haritalar Hizmetleri modülüne bir başvuru ekleyin. Modül, Azure Maps REST hizmetlerini sarmalayan ve JavaScript 'te kullanımını kolaylaştıran bir JavaScript kitaplığı. Modül, arama işlevselliğini güçetmek için kullanışlıdır.

    ```HTML
    <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>
    ```

1. *İndex. js* ve *index. css*öğesine başvurular ekleyin:

    ```HTML
    <link rel="stylesheet" href="index.css" type="text/css">
    <script src="index.js"></script>
    ```

1. Belge gövdesinde bir `header` etiketi ekleyin. `header` etiketinin içinde, logo ve şirket adını ekleyin.

    ```HTML
    <header>
        <img src="images/Logo.png" />
        <span>Contoso Coffee</span>
    </header>
    ```

1. Bir `main` etiketi ekleyin ve metin kutusu ve arama düğmesine sahip bir arama paneli oluşturun. Ayrıca, harita, liste paneli ve Konumum, GPS için `div` başvurular ekleyin.

    ```HTML
    <main>
        <div class="searchPanel">
            <div>
                <input id="searchTbx" type="search" placeholder="Find a store" />
                <button id="searchBtn" title="Search"></button>
            </div>
        </div>
        <div id="listPanel"></div>
        <div id="myMap"></div>
        <button id="myLocationBtn" title="My Location"></button>
    </main>
    ```

İşiniz bittiğinde *index. html* [Bu örnek index. html dosyası](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator/index.html)gibi görünmelidir.

Sonraki adım CSS stillerini tanımlamaktır. CSS stilleri, uygulama bileşenlerinin nasıl düzenlendiğini ve uygulamanın görünümünü tanımlar. *İndex. css* ' ye açın ve aşağıdaki kodu ekleyin. `@media` stili, ekran genişliği 700 pikselden küçük olduğunda kullanılacak alternatif stil seçeneklerini tanımlar.  

   ```CSS
    html, body {
        padding: 0;
        margin: 0;
        font-family: Gotham, Helvetica, sans-serif;
        overflow-x: hidden;
    }

    header {
        width: calc(100vw - 10px);
        height: 30px;
        padding: 15px 0 20px 20px;
        font-size: 25px;
        font-style: italic;
        font-family: "Comic Sans MS", cursive, sans-serif;
        line-height: 30px;
        font-weight: bold;
        color: white;
        background-color: #007faa;
    }

    header span {
        vertical-align: middle;
    }

    header img {
        height: 30px;
        vertical-align: middle;
    }

    .searchPanel {
        position: relative;
        width: 350px;
    }

    .searchPanel div {
        padding: 20px;
    }

    .searchPanel input {
        width: calc(100% - 50px);
        font-size: 16px;
        border: 0;
        border-bottom: 1px solid #ccc;
    }

    #listPanel {
        position: absolute;
        top: 135px;
        left: 0px;
        width: 350px;
        height: calc(100vh - 135px);
        overflow-y: auto;
    }

    #myMap { 
        position: absolute;
        top: 65px;
        left: 350px;
        width: calc(100vw - 350px);
        height: calc(100vh - 65px);
    }

    .statusMessage {
        margin: 10px;
    }

    #myLocationBtn, #searchBtn {
        margin: 0;
        padding: 0;
        border: none;
        border-collapse: collapse;
        width: 32px;
        height: 32px; 
        text-align: center;
        cursor: pointer;
        line-height: 32px;
        background-repeat: no-repeat;
        background-size: 20px;
        background-position: center center;
        z-index: 200;
    }

    #myLocationBtn {
        position: absolute;
        top: 150px;
        right: 10px;
        box-shadow: 0px 0px 4px rgba(0,0,0,0.16);
        background-color: white;
        background-image: url("images/GpsIcon.png");
    }

    #myLocationBtn:hover {
        background-image: url("images/GpsIcon-hover.png");
    }

    #searchBtn {
        background-color: transparent;
        background-image: url("images/SearchIcon.png");
    }

    #searchBtn:hover {
        background-image: url("images/SearchIcon-hover.png");
    }

    .listItem {
        height: 50px;
        padding: 20px;
        font-size: 14px;
    }

    .listItem:hover {
        cursor: pointer;
        background-color: #f1f1f1;
    }

    .listItem-title {
        color: #007faa;
        font-weight: bold;
    }

    .storePopup {
        min-width: 150px;
    }

    .storePopup .popupTitle {
        border-top-left-radius: 4px;
        border-top-right-radius: 4px;
        padding: 8px;
        height: 30px;
        background-color: #007faa;
        color: white;
        font-weight: bold;
    }

    .storePopup .popupSubTitle {
        font-size: 10px;
        line-height: 12px;
    }

    .storePopup .popupContent {
        font-size: 11px;
        line-height: 18px;
        padding: 8px;
    }

    .storePopup img {
        vertical-align:middle;
        height: 12px;
        margin-right: 5px;
    }

    /* Adjust the layout of the page when the screen width is less than 700 pixels. */
    @media screen and (max-width: 700px) {
        .searchPanel {
            width: 100vw;
        }

        #listPanel {
            top: 385px;
            width: 100%;
            height: calc(100vh - 385px);
        }

        #myMap {
            width: 100vw;
            height: 250px;
            top: 135px;
            left: 0px;
        }

        #myLocationBtn {
            top: 220px;
        }
    }

    .mapCenterIcon {
        display: block;
        width: 10px;
        height: 10px;
        border-radius: 50%;
        background: orange;
        border: 2px solid white;
        cursor: pointer;
        box-shadow: 0 0 0 rgba(0, 204, 255, 0.4);
        animation: pulse 3s infinite;
    }

    @keyframes pulse {
        0% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0.4);
        }

        70% {
            box-shadow: 0 0 0 50px rgba(0, 204, 255, 0);
        }

        100% {
            box-shadow: 0 0 0 0 rgba(0, 204, 255, 0);
        }
    }
   ```

Uygulamayı şimdi çalıştırırsanız, üst bilgi, arama kutusu ve arama düğmesini görürsünüz, ancak henüz yüklenmediği için harita görünür değildir. Bir arama yapmayı denerseniz, hiçbir şey olmaz. Mağaza bulucunun tüm işlevlerine erişmek için sonraki bölümde açıklanan JavaScript mantığını ayarladık.

## <a name="wire-the-application-with-javascript"></a>JavaScript ile uygulamayı tel bağlantı

Bu noktada, her şey Kullanıcı arabiriminde ayarlanır. Şimdi, verileri yüklemek ve ayrıştırmak için JavaScript 'i eklememiz ve sonra verileri haritada işlemesi gerekiyor. Başlamak için *index. js* ' yi açın ve aşağıdaki adımlarda açıklandığı gibi buna kod ekleyin.

1. Ayarları daha kolay güncelleştirilmesini sağlamak için genel seçenekler ekleyin. Ayrıca harita, bir açılan pencere, veri kaynağı, simge katmanı, bir arama alanının merkezini görüntüleyen HTML işaretleyicisi ve Azure haritalar arama hizmeti istemcisinin bir örneğini tanımlayın.

    ```JavaScript
    //The maximum zoom level to cluster data point data on the map.
    var maxClusterZoomLevel = 11;

    //The URL to the store location data.
    var storeLocationDataUrl = 'data/ContosoCoffee.txt';

    //The URL to the icon image.
    var iconImageUrl = 'images/CoffeeIcon.png';
    var map, popup, datasource, iconLayer, centerMarker, searchURL;
    ```

1. *İndex. js*' ye kod ekleyin. Aşağıdaki kod eşlemeyi başlatır, sayfa yüklemeyi bitirene kadar bekleyen bir [olay dinleyicisi](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#events) ekler, haritanın yüklenmesini izlemek için olayları kablolendirir ve arama düğmesini ve konumumu kapatır düğmesini güçlendirir.

   Kullanıcı arama düğmesini seçtiğinde veya arama kutusuna bir konum girdikten sonra Kullanıcı ENTER tuşuna bastığında, kullanıcının sorgusuna yönelik belirsiz bir arama başlatılır. Arama sonuçlarını bu ülkelere/bölgelerle sınırlamak için `countrySet` seçeneğine bir Country ISO 2 değerleri dizisi geçirin. Ülkeleri/bölgeleri arama yapılacak şekilde sınırlamak, döndürülen sonuçların doğruluğunu artırmaya yardımcı olur. 
  
   Arama tamamlandığında, ilk sonucu alın ve harita kamerayı bu alana ayarlayın. Kullanıcı konumumu seçtiğinde, kullanıcının konumunu almak ve Haritayı konumlarına göre ortalamak için tarayıcıda yerleşik HTML5 coğrafi konum API 'sini kullanın.  

   > [!Tip]
   > Açılır pencereleri kullanırken, tek bir `Popup` örneği oluşturmak ve içeriğini ve konumunu güncelleştirerek örneği yeniden kullanmak en iyisidir. Kodunuza eklediğiniz her `Popup`örneği için, sayfaya birden fazla DOM öğesi eklenir. Sayfada daha fazla DOM öğesi varsa, tarayıcıda izlemek için gereken daha fazla şey vardır. Çok fazla öğe varsa tarayıcı yavaş kalabilir.

    ```JavaScript
    function initialize() {
        //Initialize a map instance.
        map = new atlas.Map('myMap', {
            center: [-90, 40],
            zoom: 2,

            //Add your Azure Maps primary subscription key to the map SDK.
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });

        //Create a pop-up window, but leave it closed so we can update it and display it later.
        popup = new atlas.Popup();

        //Use SubscriptionKeyCredential with a subscription key
        const subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

        //Use subscriptionKeyCredential to create a pipeline
        const pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
            retryOptions: { maxTries: 4 } // Retry options
        });

        //Create an instance of the SearchURL client.
        searchURL = new atlas.service.SearchURL(pipeline);

        //If the user selects the search button, geocode the value the user passed in.
        document.getElementById('searchBtn').onclick = performSearch;

        //If the user presses Enter in the search box, perform a search.
        document.getElementById('searchTbx').onkeyup = function(e) {
            if (e.keyCode === 13) {
                performSearch();
            }
        };

        //If the user selects the My Location button, use the Geolocation API to get the user's location. Center and zoom the map on that location.
        document.getElementById('myLocationBtn').onclick = setMapToUserLocation;

        //Wait until the map resources are ready.
        map.events.add('ready', function() {

            //Add your post-map load functionality.

        });
    }

    //Create an array of country ISO 2 values to limit searches to. 
    var countrySet = ['US', 'CA', 'GB', 'FR','DE','IT','ES','NL','DK'];

    function performSearch() {
        var query = document.getElementById('searchTbx').value;

        //Perform a fuzzy search on the users query.
        searchURL.searchFuzzy(atlas.service.Aborter.timeout(3000), query, {
            //Pass in the array of country ISO2 for which we want to limit the search to.
            countrySet: countrySet
        }).then(results => {
            //Parse the response into GeoJSON so that the map can understand.
            var data = results.geojson.getFeatures();

            if (data.features.length > 0) {
                //Set the camera to the bounds of the results.
                map.setCamera({
                    bounds: data.features[0].bbox,
                    padding: 40
                });
            } else {
                document.getElementById('listPanel').innerHTML = '<div class="statusMessage">Unable to find the location you searched for.</div>';
            }
        });
    }

    function setMapToUserLocation() {
        //Request the user's location.
        navigator.geolocation.getCurrentPosition(function(position) {
            //Convert the Geolocation API position to a longitude and latitude position value that the map can interpret and center the map over it.
            map.setCamera({
                center: [position.coords.longitude, position.coords.latitude],
                zoom: maxClusterZoomLevel + 1
            });
        }, function(error) {
            //If an error occurs when the API tries to access the user's position information, display an error message.
            switch (error.code) {
                case error.PERMISSION_DENIED:
                    alert('User denied the request for geolocation.');
                    break;
                case error.POSITION_UNAVAILABLE:
                    alert('Position information is unavailable.');
                    break;
                case error.TIMEOUT:
                    alert('The request to get user position timed out.');
                    break;
                case error.UNKNOWN_ERROR:
                    alert('An unknown error occurred.');
                    break;
            }
        });
    }

    //Initialize the application when the page is loaded.
    window.onload = initialize;
    ```

1. Haritanın `ready` olay dinleyicisinde, bir arama alanının merkezini göstermek için bir yakınlaştırma denetimi ve HTML işaretleyicisi ekleyin.

    ```JavaScript
    //Add a zoom control to the map.
    map.controls.add(new atlas.control.ZoomControl(), {
        position: 'top-right'
    });

    //Add an HTML marker to the map to indicate the center to use for searching.
    centerMarker = new atlas.HtmlMarker({
        htmlContent: '<div class="mapCenterIcon"></div>',
        position: map.getCamera().center
    });

    map.markers.add(centerMarker);
    ```

1. Haritanın `ready` olay dinleyicisinde bir veri kaynağı ekleyin. Ardından, veri kümesini yükleme ve ayrıştırma çağrısı yapın. Veri kaynağında kümelendirmeyi etkinleştirin. Veri kaynağı gruplarında, çakışan noktaları bir kümede birlikte bulunan kümelendirmelidir. Kümeler, Kullanıcı yakınlaşarak tek tek noktalara ayrılır. Bu, daha akıcı bir kullanıcı deneyimi sağlar ve performansı geliştirir.

    ```JavaScript
    //Create a data source, add it to the map, and then enable clustering.
    datasource = new atlas.source.DataSource(null, {
        cluster: true,
        clusterMaxZoom: maxClusterZoomLevel - 1
    });

    map.sources.add(datasource);

    //Load all the store data now that the data source is defined.  
    loadStoreData();
    ```

1. Veri kümesini haritanın `ready` olay dinleyicisine yükledikten sonra, verileri işlemek için bir katman kümesi tanımlayın. Bir kabarcık katmanı, kümelenmiş veri noktalarını işlemek için kullanılır. Balon katmanının üzerindeki her kümedeki noktaların sayısını işlemek için bir sembol katmanı kullanılır. İkinci bir sembol katmanı, haritadaki ayrı konumlar için özel bir simge oluşturur.

   Kullanıcı haritada bir küme veya simgenin üzerine geldiğinde fare imlecini değiştirmek için kabarcık ve simge katmanlarına `mouseover` ve `mouseout` olaylar ekleyin. Küme kabarcık katmanına `click` bir olay ekleyin. Bu `click` olay, Haritayı iki düzeyde büyütür ve Kullanıcı herhangi bir kümeyi seçtiğinde Haritayı bir küme üzerine ortalar. Simge katmanına `click` bir olay ekleyin. Bu `click` olay, Kullanıcı tek bir konum simgesi seçtiğinde bir kafeterin ayrıntılarını gösteren bir açılır pencere görüntüler. Haritanın taşınması tamamlandığında izlemek üzere haritaya bir olay ekleyin. Bu olay tetiklendiğinde, liste panelindeki öğeleri güncelleştirin.  

    ```JavaScript
    //Create a bubble layer to render clustered data points.
    var clusterBubbleLayer = new atlas.layer.BubbleLayer(datasource, null, {
        radius: 12,
        color: '#007faa',
        strokeColor: 'white',
        strokeWidth: 2,
        filter: ['has', 'point_count'] //Only render data points that have a point_count property; clusters have this property.
    });

    //Create a symbol layer to render the count of locations in a cluster.
    var clusterLabelLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none' //Hide the icon image.
        },

        textOptions: {
            textField: ['get', 'point_count_abbreviated'],
            size: 12,
            font: ['StandardFont-Bold'],
            offset: [0, 0.4],
            color: 'white'
        }
    });

    map.layers.add([clusterBubbleLayer, clusterLabelLayer]);

    //Load a custom image icon into the map resources.
    map.imageSprite.add('myCustomIcon', iconImageUrl).then(function() {

    //Create a layer to render a coffee cup symbol above each bubble for an individual location.
    iconLayer = new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            //Pass in the ID of the custom icon that was loaded into the map resources.
            image: 'myCustomIcon',

            //Optionally, scale the size of the icon.
            font: ['SegoeUi-Bold'],

            //Anchor the center of the icon image to the coordinate.
            anchor: 'center',

            //Allow the icons to overlap.
            allowOverlap: true
        },

        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
    });

    map.layers.add(iconLayer);

    //When the mouse is over the cluster and icon layers, change the cursor to a pointer.
    map.events.add('mouseover', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'pointer';
    });

    //When the mouse leaves the item on the cluster and icon layers, change the cursor back to the default (grab).
    map.events.add('mouseout', [clusterBubbleLayer, iconLayer], function() {
        map.getCanvasContainer().style.cursor = 'grab';
    });

    //Add a click event to the cluster layer. When the user selects a cluster, zoom into it by two levels.  
    map.events.add('click', clusterBubbleLayer, function(e) {
        map.setCamera({
            center: e.position,
            zoom: map.getCamera().zoom + 2
        });
    });

    //Add a click event to the icon layer and show the shape that was selected.
    map.events.add('click', iconLayer, function(e) {
        showPopup(e.shapes[0]);
    });

    //Add an event to monitor when the map is finished rendering the map after it has moved.
    map.events.add('render', function() {
        //Update the data in the list.
        updateListItems();
    });
    ```

1. Kahve dükkanı veri kümesi yüklendiğinde, önce indirilmelidir. Sonra, metin dosyası çizgilere bölünmelidir. İlk satır üst bilgi bilgilerini içerir. Kodun izlenmesini kolaylaştırmak için üst bilgiyi bir nesnesine ayrıştırır, bu da daha sonra her bir özelliğin hücre dizinini aramak için kullanabiliriz. İlk satırdan sonra, kalan satırlarda ilerleyin ve bir nokta özelliği oluşturun. Point özelliğini veri kaynağına ekleyin. Son olarak, liste panelini güncelleştirin.

    ```JavaScript
    function loadStoreData() {

    //Download the store location data.
    fetch(storeLocationDataUrl)
        .then(response => response.text())
        .then(function(text) {

            //Parse the tab-delimited file data into GeoJSON features.
            var features = [];

            //Split the lines of the file.
            var lines = text.split('\n');

            //Grab the header row.
            var row = lines[0].split('\t');

            //Parse the header row and index each column to make the code for parsing each row easier to follow.
            var header = {};
            var numColumns = row.length;
            for (var i = 0; i < row.length; i++) {
                header[row[i]] = i;
            }

            //Skip the header row and then parse each row into a GeoJSON feature.
            for (var i = 1; i < lines.length; i++) {
                row = lines[i].split('\t');

                //Ensure that the row has the correct number of columns.
                if (row.length >= numColumns) {

                    features.push(new atlas.data.Feature(new atlas.data.Point([parseFloat(row[header['Longitude']]), parseFloat(row[header['Latitude']])]), {
                        AddressLine: row[header['AddressLine']],
                        City: row[header['City']],
                        Municipality: row[header['Municipality']],
                        AdminDivision: row[header['AdminDivision']],
                        Country: row[header['Country']],
                        PostCode: row[header['PostCode']],
                        Phone: row[header['Phone']],
                        StoreType: row[header['StoreType']],
                        IsWiFiHotSpot: (row[header['IsWiFiHotSpot']].toLowerCase() === 'true') ? true : false,
                        IsWheelchairAccessible: (row[header['IsWheelchairAccessible']].toLowerCase() === 'true') ? true : false,
                        Opens: parseInt(row[header['Opens']]),
                        Closes: parseInt(row[header['Closes']])
                    }));
                }
            }

            //Add the features to the data source.
            datasource.add(new atlas.data.FeatureCollection(features));

            //Initially, update the list items.
            updateListItems();
        });
    }
    ```

1. Liste paneli güncelleştirilirken haritanın merkezinden uzaklığı geçerli harita görünümündeki tüm nokta özelliklerine göre hesaplanır. Özellikler daha sonra uzaklığına göre sıralanır. Her konumu liste panelinde göstermek için HTML oluşturulur.

    ```JavaScript
    var listItemTemplate = '<div class="listItem" onclick="itemSelected(\'{id}\')"><div class="listItem-title">{title}</div>{city}<br />Open until {closes}<br />{distance} miles away</div>';

    function updateListItems() {
        //Hide the center marker.
        centerMarker.setOptions({
            visible: false
        });

        //Get the current camera and view information for the map.
        var camera = map.getCamera();
        var listPanel = document.getElementById('listPanel');

        //Check to see whether the user is zoomed out a substantial distance. If they are, tell the user to zoom in and to perform a search or select the My Location button.
        if (camera.zoom < maxClusterZoomLevel) {
            //Close the pop-up window; clusters might be displayed on the map.  
            popup.close(); 
            listPanel.innerHTML = '<div class="statusMessage">Search for a location, zoom the map, or select the My Location button to see individual locations.</div>';
        } else {
            //Update the location of the centerMarker property.
            centerMarker.setOptions({
                position: camera.center,
                visible: true
            });

            //List the ten closest locations in the side panel.
            var html = [], properties;

            /*
            Generating HTML for each item that looks like this:
            <div class="listItem" onclick="itemSelected('id')">
                <div class="listItem-title">1 Microsoft Way</div>
                Redmond, WA 98052<br />
                Open until 9:00 PM<br />
                0.7 miles away
            </div>
            */

            //Get all the shapes that have been rendered in the bubble layer. 
            var data = map.layers.getRenderedShapes(map.getCamera().bounds, [iconLayer]);

            //Create an index of the distances of each shape.
            var distances = {};

            data.forEach(function (shape) {
                if (shape instanceof atlas.Shape) {

                    //Calculate the distance from the center of the map to each shape and store in the index. Round to 2 decimals.
                    distances[shape.getId()] = Math.round(atlas.math.getDistanceTo(camera.center, shape.getCoordinates(), 'miles') * 100) / 100;
                }
            });

            //Sort the data by distance.
            data.sort(function (x, y) {
                return distances[x.getId()] - distances[y.getId()];
            });

            data.forEach(function(shape) {
                properties = shape.getProperties();
                html.push('<div class="listItem" onclick="itemSelected(\'', shape.getId(), '\')"><div class="listItem-title">',
                properties['AddressLine'],
                '</div>',
                //Get a formatted addressLine2 value that consists of City, Municipality, AdminDivision, and PostCode.
                getAddressLine2(properties),
                '<br />',

                //Convert the closing time to a format that is easier to read.
                getOpenTillTime(properties),
                '<br />',

                //Get the distance of the shape.
                distances[shape.getId()],
                ' miles away</div>');
            });

            listPanel.innerHTML = html.join('');

            //Scroll to the top of the list panel in case the user has scrolled down.
            listPanel.scrollTop = 0;
        }
    }

    //This converts a time that's in a 24-hour format to an AM/PM time or noon/midnight string.
    function getOpenTillTime(properties) {
        var time = properties['Closes'];
        var t = time / 100;
        var sTime;

        if (time === 1200) {
            sTime = 'noon';
        } else if (time === 0 || time === 2400) {
            sTime = 'midnight';
        } else {
            sTime = Math.round(t) + ':';

            //Get the minutes.
            t = (t - Math.round(t)) * 100;

            if (t === 0) {
                sTime += '00';
            } else if (t < 10) {
                sTime += '0' + t;
            } else {
                sTime += Math.round(t);
            }

            if (time < 1200) {
                sTime += ' AM';
            } else {
                sTime += ' PM';
            }
        }

        return 'Open until ' + sTime;
    }

    //Create an addressLine2 string that contains City, Municipality, AdminDivision, and PostCode.
    function getAddressLine2(properties) {
        var html = [properties['City']];

        if (properties['Municipality']) {
            html.push(', ', properties['Municipality']);
        }

        if (properties['AdminDivision']) {
            html.push(', ', properties['AdminDivision']);
        }

        if (properties['PostCode']) {
            html.push(' ', properties['PostCode']);
        }

        return html.join('');
    }
    ```

1. Kullanıcı liste panelinde bir öğe seçtiğinde, öğenin ilişkili olduğu şekil veri kaynağından alınır. Şeklin içinde depolanan Özellik bilgilerini temel alan bir açılır pencere oluşturulur. Harita şeklin üzerine ortalanır. Eşleme 700 piksel genişliğinde değilse, harita görünümü, açılır pencere görünür olacak şekilde denkleştirilir.

    ```JavaScript
    //When a user selects a result in the side panel, look up the shape by its ID value and display the pop-up window.
    function itemSelected(id) {
        //Get the shape from the data source by using its ID.  
        var shape = datasource.getShapeById(id);
        showPopup(shape);

        //Center the map over the shape on the map.
        var center = shape.getCoordinates();
        var offset;

        //If the map is less than 700 pixels wide, then the layout is set for small screens.
        if (map.getCanvas().width < 700) {
            //When the map is small, offset the center of the map relative to the shape so that there is room for the popup to appear.
            offset = [0, -80];
        }

        map.setCamera({
            center: center,
            centerOffset: offset
        });
    }

    function showPopup(shape) {
        var properties = shape.getProperties();

        /* Generating HTML for the pop-up window that looks like this:

            <div class="storePopup">
                <div class="popupTitle">
                    3159 Tongass Avenue
                    <div class="popupSubTitle">Ketchikan, AK 99901</div>
                </div>
                <div class="popupContent">
                    Open until 22:00 PM<br/>
                    <img title="Phone Icon" src="images/PhoneIcon.png">
                    <a href="tel:1-800-XXX-XXXX">1-800-XXX-XXXX</a>
                    <br>Amenities:
                    <img title="Wi-Fi Hotspot" src="images/WiFiIcon.png">
                    <img title="Wheelchair Accessible" src="images/WheelChair-small.png">
                </div>
            </div>
        */

         //Calculate the distance from the center of the map to the shape in miles, round to 2 decimals.
        var distance = Math.round(atlas.math.getDistanceTo(map.getCamera().center, shape.getCoordinates(), 'miles') * 100)/100;

        var html = ['<div class="storePopup">'];
        html.push('<div class="popupTitle">',
            properties['AddressLine'],
            '<div class="popupSubTitle">',
            getAddressLine2(properties),
            '</div></div><div class="popupContent">',

            //Convert the closing time to a format that's easier to read.
            getOpenTillTime(properties),

            //Add the distance information.  
            '<br/>', distance,
            ' miles away',
            '<br /><img src="images/PhoneIcon.png" title="Phone Icon"/><a href="tel:',
            properties['Phone'],
            '">',  
            properties['Phone'],
            '</a>'
        );

        if (properties['IsWiFiHotSpot'] || properties['IsWheelchairAccessible']) {
            html.push('<br/>Amenities: ');

            if (properties['IsWiFiHotSpot']) {
                html.push('<img src="images/WiFiIcon.png" title="Wi-Fi Hotspot"/>');
            }

            if (properties['IsWheelchairAccessible']) {
                html.push('<img src="images/WheelChair-small.png" title="Wheelchair Accessible"/>');
            }
        }

        html.push('</div></div>');

        //Update the content and position of the pop-up window for the specified shape information.
        popup.setOptions({

            //Create a table from the properties in the feature.
            content:  html.join(''),
            position: shape.getCoordinates()
        });

        //Open the pop-up window.
        popup.open(map);
    }
    ```

Artık tam işlevli bir depolama Konumlayıcı vardır. Bir Web tarayıcısında mağaza Bulucu için *index. html* dosyasını açın. Kümeler haritada görüntülendiğinde, arama kutusunu kullanarak, bir küme seçerek veya tek tek konumları görmek için haritada yakınlaştırarak bir konum araması yapabilirsiniz ' ı seçin.

Kullanıcı konumumu ilk kez seçtiğinde tarayıcı, kullanıcının konumuna erişmek için izin isteyen bir güvenlik uyarısı görüntüler. Kullanıcı konumunu paylaşmayı kabul ederse, harita kullanıcının konumuna yakınlaşarak yakın kafeterler gösterilir. 

<br/>
<center>

tarayıcının kullanıcının konumuna erişim isteğinin ekran görüntüsünü ![](./media/tutorial-create-store-locator/GeolocationApiWarning.png)</center>

Kahve dükterleri bulunan bir alanda yeterince yakından yakınlaştırdığınızda, kümeler tek tek konumlara ayrılır. Haritadaki simgelerden birini seçin veya yan bölmede bir öğe seçerek söz konusu konumun bilgilerini gösteren bir açılır pencere görüntüleyin.

<br/>
<center>

tamamlanmış mağaza bulucunun ekran görüntüsünü ![](./media/tutorial-create-store-locator/FinishedSimpleStoreLocator.png)</center>

Tarayıcı penceresini 700 piksel genişliğinde veya uygulamayı bir mobil cihazda açmak için yeniden boyutlandırırsanız, düzen daha küçük ekranlarda daha uygun olacak şekilde değişir. 

<br/>
<center>

Mağaza Konumlandırıcı 'nın küçük ekran sürümünün ekran görüntüsünü ![](./media/tutorial-create-store-locator/FinishedSimpleStoreLocatorSmallScreen.png)</center>

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Azure haritalar 'ı kullanarak temel bir mağaza Konumlandırıcısı oluşturmayı öğreneceksiniz. Bu öğreticide oluşturduğunuz mağaza Bulucu, ihtiyacınız olan tüm işlevlere sahip olabilir. Mağaza bulucusinize özellikler ekleyebilir veya daha özel bir kullanıcı deneyimi için daha gelişmiş özellikler kullanabilirsiniz: 

> [!div class="checklist"]
> * Arama kutusuna [yazarken önerileri](https://azuremapscodesamples.azurewebsites.net/?sample=Search%20Autosuggest%20and%20JQuery%20UI) etkinleştirin.  
> * [Birden çok dil için destek](https://azuremapscodesamples.azurewebsites.net/?sample=Map%20Localization)ekleyin. 
> * Kullanıcının [bir rota üzerinde konumları filtrelemesine](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Data%20Along%20Route)izin verin. 
> * [Filtre ayarlama](https://azuremapscodesamples.azurewebsites.net/?sample=Filter%20Symbols%20by%20Property)özelliğini ekleyin. 
> * Sorgu dizesi kullanarak bir ilk arama değeri belirtmek için destek ekleyin. Bu seçeneği mağaza bulucusınıza eklediğinizde, kullanıcılar aramaları yer işaretine ekleyebilir ve aramaları paylaşabilir. Ayrıca, bu sayfaya aramaları başka bir sayfadan iletmeniz için kolay bir yöntem sağlar.  
> * Mağaza bulucuyu bir [Azure App Service Web uygulaması](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html)olarak dağıtın. 
> * Verilerinizi bir veritabanında depolayın ve yakındaki konumları arayın. Daha fazla bilgi edinmek için bkz. [SQL Server uzamsal veri türlerine genel bakış](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-types-overview?view=sql-server-2017) ve [en yakın komşu Için uzamsal verileri sorgulama](https://docs.microsoft.com/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor?view=sql-server-2017).

> [!div class="nextstepaction"]
> [Tam kaynak kodunu görüntüle](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Tutorials/Simple%20Store%20Locator)

> [!div class="nextstepaction"]
> [Canlı örneği görüntüle](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Simple%20Store%20Locator)

Azure Haritalar'ın kapsamı ve özellikleri hakkında daha fazla bilgi edinmek için:

> [!div class="nextstepaction"]
> [Yakınlaştırma düzeyleri ve kutucuk kılavuzu](zoom-levels-and-tile-grid.md)

Daha fazla kod örneği ve etkileşimli bir kodlama deneyimi için:

> [!div class="nextstepaction"]
> [Harita denetimini kullanma](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Veri tabanlı stil ifadeleri kullanın](data-driven-style-expressions-web-sdk.md)
