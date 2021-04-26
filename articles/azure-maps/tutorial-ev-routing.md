---
title: 'Öğretici: Microsoft Azure eşlemelerle Azure Notebooks (Python) kullanarak elektrik taşıtlar'
description: Microsoft Azure haritaları yönlendirme API 'Leri ve Azure Notebooks kullanarak elektrik taşıtlarının nasıl yönlendirileceği hakkında öğretici
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 7341d1f07e8814edcad7b84f6b3b46c7bece3159
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98680340"
---
# <a name="tutorial-route-electric-vehicles-by-using-azure-notebooks-python"></a>Öğretici: Azure Notebooks (Python) kullanarak elektrik ve yönlendirme

Azure Maps, Azure ile yerel olarak tümleştirilmiş Jeo-uzamsal hizmet API 'Lerinin bir portföyüdür. Bu API 'Ler, geliştiricilerin, kuruluşların ve ISV 'Lerin konuma duyarlı uygulamalar, IoT, Mobility, lojistik ve varlık izleme çözümleri geliştirmesini sağlar. 

Azure haritalar REST API 'Leri, Jeo-uzamsal veri analizi ve makine öğrenimi senaryolarını etkinleştirmek için Python ve R gibi dillerden çağrılabilir. Azure Maps, kullanıcıların çeşitli veri noktaları arasında rota hesaplamasını sağlayan sağlam bir [yönlendirme API](/rest/api/maps/route) 'si kümesi sunar. Hesaplamalar, araç türü veya ulaşılabilir alan gibi çeşitli koşullara dayanır. 

Bu öğreticide, elektrik araç pili düşük olan bir sürücünün yardımına kılavuzluk edersiniz. Sürücünün, aracın konumundan olası en yakın ücretlendirme istasyonunu bulması gerekir.

Bu öğreticide şunları yapacaksınız:

> [!div class="checklist"]
> * Bulutta [Azure Notebooks](https://notebooks.azure.com) bir Jupyter Notebook dosyası oluşturun ve çalıştırın.
> * Python 'da Azure haritalar REST API 'Lerini çağırın.
> * Elektrik araç tüketiminin tüketim modeline göre erişilebilir bir Aralık arayın.
> * Erişilebilir Aralık veya ısovaone içindeki elektrik araç doldurma istasyonlarını arayın.
> * Bir haritada erişilebilir Aralık sınırı ve ücretlendirme istasyonlarını işleme.
> * Sürücü zamanına göre en yakın elektrik araç doldurma istasyonuna bir yol bulun ve görselleştirin.


## <a name="prerequisites"></a>Önkoşullar 

Bu öğreticiyi tamamlayabilmeniz için öncelikle bir Azure Maps hesabı oluşturmanız ve birincil anahtarınızı (abonelik anahtarı) almanız gerekir. 

Azure haritalar hesabı aboneliği oluşturmak için [Hesap oluşturma](quick-demo-map-app.md#create-an-azure-maps-account)bölümündeki yönergeleri izleyin. S1 fiyat katmanıyla bir Azure Maps hesap aboneliğine ihtiyacınız vardır. 

Hesabınız için birincil abonelik anahtarını almak üzere [birincil anahtar al](quick-demo-map-app.md#get-the-primary-key-for-your-account)' daki yönergeleri izleyin.

Azure haritalar 'da kimlik doğrulaması hakkında daha fazla bilgi için bkz. [Azure haritalar 'da kimlik doğrulamasını yönetme](./how-to-manage-authentication.md).

## <a name="create-an-azure-notebooks-project"></a>Azure Notebooks projesi oluşturma

Bu öğreticiyle birlikte izlemek için bir Azure Notebooks projesi oluşturmanız ve Jupyter Notebook dosyasını indirip çalıştırmanız gerekir. Jupyter Notebook dosyası, bu öğreticide senaryoyu uygulayan Python kodunu içerir. Bir Azure Notebooks projesi oluşturmak ve Jupyter Notebook belgeyi bu klasöre yüklemek için aşağıdaki adımları uygulayın:

1. [Azure Notebooks](https://notebooks.azure.com) gidin ve oturum açın. Daha fazla bilgi için bkz. [hızlı başlangıç: oturum açın ve bir kullanıcı kimliği ayarlayın](https://notebooks.azure.com).
1. Ortak profil sayfanızın en üstünde **Projelerim**' nı seçin.

    ![Projelerim düğmesi](./media/tutorial-ev-routing/myproject.png)

1. **Projelerim** sayfasında **Yeni proje**' yi seçin.
 
   ![Yeni proje düğmesi](./media/tutorial-ev-routing/create-project.png)

1. **Yeni proje oluştur** bölmesinde bir proje adı ve proje kimliği girin.
 
    ![Yeni proje oluştur bölmesi](./media/tutorial-ev-routing/create-project-window.png)

1. **Oluştur**’u seçin.

1. Projeniz oluşturulduktan sonra bu [Jupyter Notebook belge dosyasını](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) [Azure Maps Jupyter Notebook deposundan](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook)indirin.

1. Projeler listesinde, **projem** sayfasında, projenizi seçin ve sonra Jupyter Notebook belge dosyasını karşıya yüklemek Için **karşıya yükle** ' yi seçin. 

    ![karşıya yükleme Jupyter Notebook](./media/tutorial-ev-routing/upload-notebook.png)

1. Dosyayı bilgisayarınızdan karşıya yükleyin ve ardından **bitti**' yi seçin.

1. Karşıya yükleme başarıyla tamamlandıktan sonra, dosyanız proje sayfanızda görüntülenir. Dosyayı Jupyter Notebook olarak açmak için dosyaya çift tıklayın.

Jupyter Notebook dosyasında uygulanan işlevselliği anlamaya çalışın. Kodu, Jupyter Notebook dosyasında, tek seferde bir hücre olarak çalıştırın. Jupyter Notebook uygulamasının en üstündeki **Çalıştır** düğmesini seçerek kodu her hücrede çalıştırabilirsiniz.

  ![Çalıştır düğmesi](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Proje düzeyi paketleri 'ni yükler

Kodu Jupyter Notebook çalıştırmak için, aşağıdaki adımları uygulayarak paketleri proje düzeyine yüklersiniz:

1. [Azure haritalar Jupyter Notebook deposundan](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook) [*requirements.txt*](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) dosyasını indirin ve ardından projenize yükleyin.
1. Proje panosunda **proje ayarları**' nı seçin. 
1. **Proje ayarları** bölmesinde **ortam** sekmesini seçin ve ardından **Ekle**' yi seçin.
1. **Ortam kurulum adımları** altında aşağıdakileri yapın:   
    a. İlk açılan listede **Requirements.txt**' yi seçin.  
    b. İkinci aşağı açılan listede *requirements.txt* dosyanızı seçin.  
    c. Üçüncü açılan listede sürüm olarak **Python sürüm 3,6** ' ı seçin.
1. **Kaydet**’i seçin.

    ![Paketleri yükler](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-the-required-modules-and-frameworks"></a>Gerekli modülleri ve çerçeveleri yükleme

Gerekli tüm modülleri ve çerçeveleri yüklemek için aşağıdaki betiği çalıştırın.

```Python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-the-reachable-range-boundary"></a>Erişilebilir Aralık sınırı iste

Bir paket teslim şirketi, Fleet ortamında bazı elektrik taşıtlar içerir. Gün boyunca, elektrik taşıtlarının ambara geri dönmek zorunda kalmadan yeniden ücretlendirilmelidir. Kalan tüm ücretler bir saatten daha az düşdüğünde, erişilebilir bir aralıktaki bir dizi ücretlendirme istasyonu arayın. Esas olarak, pil düşük bir ücret karşılığında bir ücretlendirme İstasyonu arar. Ve, bu ücretlendirme istasyonlarıyla ilgili sınır bilgilerini alırsınız. 

Şirket ekonomisi ve hız dengelemesi gerektiren rotaları kullanmayı tercih ettiğinden, istenen routeType *ekdir*. Aşağıdaki betik, Azure Maps yönlendirme hizmeti 'nin [Rota aralığı al API](/rest/api/maps/route/getrouterange) 'sini çağırır. Bu, aracın tüketim modeli için parametreler kullanır. Daha sonra betiği, arabasının en fazla erişilebilir aralığını temsil eden geojson biçimindeki bir çokgen nesnesi oluşturma yanıtını ayrıştırır.

Elektrik Aracı 'nın erişilebilir aralığının sınırlarını öğrenmek için betiği aşağıdaki hücrede çalıştırın:

```python
subscriptionKey = "Your Azure Maps key"
currentLocation = [34.028115,-118.5184279]
session = aiohttp.ClientSession()

# Parameters for the vehicle consumption model 
travelMode = "car"
vehicleEngineType = "electric"
currentChargeInkWh=45
maxChargeInkWh=80
timeBudgetInSec=550
routeType="eco"
constantSpeedConsumptionInkWhPerHundredkm="50,8.2:130,21.3"


# Get boundaries for the electric vehicle's reachable range.
routeRangeResponse = await (await session.get("https://atlas.microsoft.com/route/range/json?subscription-key={}&api-version=1.0&query={}&travelMode={}&vehicleEngineType={}&currentChargeInkWh={}&maxChargeInkWh={}&timeBudgetInSec={}&routeType={}&constantSpeedConsumptionInkWhPerHundredkm={}"
                                              .format(subscriptionKey,str(currentLocation[0])+","+str(currentLocation[1]),travelMode, vehicleEngineType, currentChargeInkWh, maxChargeInkWh, timeBudgetInSec, routeType, constantSpeedConsumptionInkWhPerHundredkm))).json()

polyBounds = routeRangeResponse["reachableRange"]["boundary"]

for i in range(len(polyBounds)):
    coordList = list(polyBounds[i].values())
    coordList[0], coordList[1] = coordList[1], coordList[0]
    polyBounds[i] = coordList

polyBounds.pop()
polyBounds.append(polyBounds[0])

boundsData = {
               "geometry": {
                 "type": "Polygon",
                 "coordinates": 
                   [
                      polyBounds
                   ]
                }
             }
```

## <a name="search-for-electric-vehicle-charging-stations-within-the-reachable-range"></a>Erişilebilir Aralık içinde elektrik araç doldurma istasyonlarını arayın

Elektrik Aracı için erişilebilir Aralık (ısovaone) belirledikten sonra, bu aralıktaki doldurma istasyonlarında arama yapabilirsiniz. 

Aşağıdaki betik, Azure Maps [arama sonrası bir geometri API 'sine](/rest/api/maps/search/postsearchinsidegeometry)çağrı yapılır. Otomobilin en fazla erişilebilir aralığının sınırları dahilinde elektrik arabasının doldurma istasyonlarını arar. Sonra betik, yanıtı erişilebilir konumlar dizisine ayrıştırır.

Erişilebilir Aralık dahilinde elektrik araç doldurma istasyonlarını aramak için aşağıdaki betiği çalıştırın:

```python
# Search for electric vehicle stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-the-reachable-range-and-charging-points-to-azure-maps-data-service-preview"></a>Erişilebilir Aralık ve doldurma noktalarını Azure Maps veri hizmeti 'ne yükleme (Önizleme)

Bir haritada, elektrik taşısının maksimum erişilebilir aralığının miktarını ve sınırını görselleştirmek isteyeceksiniz. Bunu yapmak için, sınır verilerini karşıya yükleyin ve istasyon verilerini Azure Maps veri hizmeti 'ne (Önizleme) coğrafi JSON nesneleri olarak ücretlendirme yapın. [Karşıya veri yükleme API](/rest/api/maps/data/uploadpreview)'sini kullanın. 

Azure Maps Data Service 'e sınır ve ücretlendirme noktası verilerini yüklemek için aşağıdaki iki hücreyi çalıştırın:

```python
rangeData = {
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          polyBounds
        ]
      }
    }
  ]
}

# Upload the range data to Azure Maps Data service (Preview).
uploadRangeResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = rangeData)

rangeUdidRequest = uploadRangeResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getRangeUdid = await (await session.get(rangeUdidRequest)).json()
    if 'udid' in getRangeUdid:
        break
    else:
        time.sleep(0.2)
rangeUdid = getRangeUdid["udid"]
```

```python
poiData = {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "properties": {},
        "geometry": {
            "type": "MultiPoint",
            "coordinates": reachableLocations
        }
    }
  ]
}

# Upload the electric vehicle charging station data to Azure Maps Data service (Preview).
uploadPOIsResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = poiData)

poiUdidRequest = uploadPOIsResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getPoiUdid = await (await session.get(poiUdidRequest)).json()
    if 'udid' in getPoiUdid:
        break
    else:
        time.sleep(0.2)
poiUdid = getPoiUdid["udid"]
```

## <a name="render-the-charging-stations-and-reachable-range-on-a-map"></a>Bir haritada doldurma istasyonlarını ve erişilebilir aralığı işleme

Verileri veri hizmetine yükledikten sonra, Azure Maps [harita görüntüsü al hizmeti](/rest/api/maps/render/getmapimage)' ni çağırın. Bu hizmet, şu betiği çalıştırarak, statik eşleme görüntüsünde doldurma noktalarını ve en fazla erişilebilir sınırı işlemek için kullanılır:

```python
# Get boundaries for the bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
    lonBuffer = (maxLon-minLon)*0.1
    minLon -= lonBuffer
    maxLon += lonBuffer

    latBuffer = (maxLat-minLat)*0.1
    minLat -= latBuffer
    maxLat += latBuffer
    
    return [minLon, maxLon, minLat, maxLat]

minLon, maxLon, minLat, maxLat = getBounds(polyBounds)

path = "lcff3333|lw3|la0.80|fa0.35||udid-{}".format(rangeUdid)
pins = "custom|an15 53||udid-{}||https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/images/icons/ev_pin.png".format(poiUdid)

encodedPins = urllib.parse.quote(pins, safe='')

# Render the range and electric vehicle charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![Konum aralığını gösteren bir harita](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station"></a>En iyi doldurma istasyonunu bulun

İlk olarak, erişilebilir aralıktaki tüm olası ücretlendirme istasyonlarını öğrenmek istersiniz. Daha sonra, en az bir süre içinde bunlardan hangilerinin ulaşılabileceğini bilmeniz gerekir. 

Aşağıdaki betik, Azure Maps [matris YÖNLENDIRME API](/rest/api/maps/route/postroutematrix)'sini çağırır. Belirtilen araç konumunu, seyahat süresini ve her bir doldurma istasyonunun uzaklığını döndürür. Sonraki hücredeki betik, zaman açısından en yakın erişilebilir doldurma istasyonunu bulma yanıtını ayrıştırır.

En az sürede erişilebilecek en yakın erişilebilir doldurma istasyonunu bulmak için betiği aşağıdaki hücrede çalıştırın:

```python
locationData = {
            "origins": {
              "type": "MultiPoint",
              "coordinates": [[currentLocation[1],currentLocation[0]]]
            },
            "destinations": {
              "type": "MultiPoint",
              "coordinates": reachableLocations
            }
         }

# Get the travel time and distance to each specified charging station.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-the-route-to-the-closest-charging-station"></a>En yakın doldurma istasyonuyla olan rotayı hesapla

En yakın ücretlendirme istasyonunu buldığınıza göre, elektrik ale's geçerli konumundan ücretlendirme istasyonuna ayrıntılı rotayı istemek için [yol yönlerini al API](/rest/api/maps/route/getroutedirections) 'sini çağırabilirsiniz.

Doldurma istasyonunun yolunu almak ve yolu temsil eden bir geojson nesnesi oluşturmak için yanıtı ayrıştırmak üzere betiği aşağıdaki hücrede çalıştırın:

```python
# Get the route from the electric vehicle's current location to the closest charging station. 
routeResponse = await (await session.get("https://atlas.microsoft.com/route/directions/json?subscription-key={}&api-version=1.0&query={}:{}".format(subscriptionKey, str(currentLocation[0])+","+str(currentLocation[1]), closestChargeLoc))).json()

route = []
for loc in range(len(routeResponse["routes"][0]["legs"][0]["points"])):
                location = list(routeResponse["routes"][0]["legs"][0]["points"][loc].values())
                location[0], location[1] = location[1], location[0]
                route.append(location)

routeData = {
         "type": "LineString",
         "coordinates": route
     }
```

## <a name="visualize-the-route"></a>Rotayı görselleştirin

Yolun görselleştirilmesine yardımcı olmak için önce Azure Maps veri hizmeti 'ne (Önizleme) bir coğrafi JSON nesnesi olarak yol verilerini karşıya yüklersiniz. Bunu yapmak için Azure Maps [verilerini karşıya yükleme API](/rest/api/maps/data/uploadpreview)'sini kullanın. Ardından, işleme hizmetini çağırın, [harita görüntüsü API 'Sini alın](/rest/api/maps/render/getmapimage), rotayı haritada oluşturup görselleştirin.

Haritada işlenen yolun bir görüntüsünü almak için aşağıdaki betiği çalıştırın:

```python
# Upload the route data to Azure Maps Data service (Preview).
routeUploadRequest = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = routeData)

udidRequestURI = routeUploadRequest.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    udidRequest = await (await session.get(udidRequestURI)).json()
    if 'udid' in udidRequest:
        break
    else:
        time.sleep(0.2)

udid = udidRequest["udid"]

destination = route[-1]

destination[1], destination[0] = destination[0], destination[1]

path = "lc0f6dd9|lw6||udid-{}".format(udid)
pins = "default|codb1818||{} {}|{} {}".format(str(currentLocation[1]),str(currentLocation[0]),destination[1],destination[0])


# Get boundaries for the bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

# Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render the route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![Yolu gösteren bir harita](./media/tutorial-ev-routing/route.png)

Bu öğreticide, Azure haritalar REST API 'Lerinin doğrudan nasıl çağrılacağını ve Python kullanarak Azure Maps verilerini görselleştirmeyi öğrendiniz.

Bu öğreticide kullanılan Azure Maps API 'Lerini araştırmak için, bkz.:

* [Rota aralığını al](/rest/api/maps/route/getrouterange)
* [Geometri Içinde arama sonrası](/rest/api/maps/search/postsearchinsidegeometry)
* [Karşıya veri yükleme](/rest/api/maps/data/uploadpreview)
* [Render-harita görüntüsünü al](/rest/api/maps/render/getmapimage)
* [Rota sonrası matrisi](/rest/api/maps/route/postroutematrix)
* [Rota yönlerini al](/rest/api/maps/route/getroutedirections)
* [Azure haritalar REST API 'Leri](./consumption-model.md)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Temizleme gerektiren kaynak yok.

## <a name="next-steps"></a>Sonraki adımlar

Azure Notebooks hakkında daha fazla bilgi edinmek için bkz.

> [!div class="nextstepaction"]
> [Azure Notebooks](https://notebooks.azure.com)