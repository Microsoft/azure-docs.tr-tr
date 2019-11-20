---
title: Azure haritalar 'da gerçek zamanlı veriler isteme | Microsoft Docs
description: Azure haritalar Mobility hizmetini kullanarak gerçek zamanlı veriler isteyin.
author: walsehgal
ms.author: v-musehg
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4c53d1c1ffbc80e694a9a7b423b2aaf9c6d38b48
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70914392"
---
# <a name="request-real-time-data-using-the-azure-maps-mobility-service"></a>Azure haritalar Mobility hizmetini kullanarak gerçek zamanlı veriler isteme

Bu makalede, gerçek zamanlı transit verileri istemek için Azure Maps [Mobility hizmetini](https://aka.ms/AzureMapsMobilityService) nasıl kullanacağınız gösterilmektedir.

Bu makalede, şunları nasıl yapacağınızı öğreneceksiniz:


 * Verilen dura ulaşan tüm satırlar için sonraki gerçek zamanlı varışları iste
 * Belirli bir bisiklet yerleştirme istasyonu için gerçek zamanlı bilgi isteyin.


## <a name="prerequisites"></a>Önkoşullar

Azure Maps ortak Aktarma API 'Lerine yönelik herhangi bir çağrı yapmak için bir haritalar hesabı ve anahtarı gerekir. Hesap oluşturma hakkında daha fazla bilgi için hesabı [Yönet](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) bölümündeki yönergeleri izleyin ve hesabınız için birincil bir abonelik anahtarı almak üzere [birincil anahtar al](./tutorial-search-location.md#getkey) bölümündeki adımları izleyin.

Bu makale, REST çağrıları oluşturmak için [Postman uygulamasını](https://www.getpostman.com/apps) kullanır. Tercih ettiğiniz herhangi bir API geliştirme ortamını kullanabilirsiniz.


## <a name="request-real-time-arrivals-for-a-stop"></a>Durdurma için gerçek zamanlı varış süresi iste

Belirli bir genel aktarım için gerçek zamanlı varış verileri istemek üzere, Azure Maps [Mobility hizmetinin](https://aka.ms/AzureMapsMobilityService) [gerçek zamanlı varış API](https://aka.ms/AzureMapsMobilityRealTimeArrivals) 'sine bir istek yapmanız gerekir. İsteği tamamlayabilmeniz için **metroID** ve **stopid** gerekir. Bu parametreleri isteme hakkında daha fazla bilgi edinmek için bkz. nasıl yapılır kılavuzumuza [genel aktarım yolları isteme](https://aka.ms/AMapsHowToGuidePublicTransitRouting). 

"Seattle – Tacoma – Bellevue, WA" alanı için Metro kimliği olan "522" öğesini ve "522---2060603" durma KIMLIĞINI kullanarak "a. 18. St & 162. Ave, Bellevue WA" olarak bir Bus durdu. Bu durdurduğunuz sonraki tüm canlı malılar için beş gerçek zamanlı varış verisi istemek üzere aşağıdaki adımları izleyin:

1. İsteklerin depolayabileceği bir koleksiyon oluşturun. Postman uygulamasında **Yeni**' yi seçin. **Yeni oluştur** penceresinde **koleksiyon**' ı seçin. Koleksiyonu adlandırın ve **Oluştur** düğmesini seçin.

2. İsteği oluşturmak için **Yeni** ' yi seçin. **Yeni oluştur** penceresinde **istek**' ı seçin. İstek için bir **istek adı** girin, önceki adımda oluşturduğunuz koleksiyonu, isteğin kaydedileceği konum olarak seçin ve ardından **Kaydet**' i seçin.

    ![Postman 'da istek oluşturma](./media/how-to-request-transit-data/postman-new.png)

3. Oluşturucu sekmesinde HTTP Al metodunu seçin ve bir GET isteği oluşturmak için aşağıdaki URL 'YI girin.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. Başarılı bir istekten sonra aşağıdaki yanıtı alırsınız.  ' ScheduleType ' parametresinin tahmini varış zamanının gerçek zamanlı veya statik verilere dayanıp dayandırmadığını tanımladığından emin olun.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 8,
                "scheduleType": "realTime",
                "patternId": "522---4143196",
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            },
            {
                "arrivalMinutes": 25,
                "scheduleType": "realTime",
                "patternId": "522---3510227",
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
                    "stopId": "522---2060603",
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "position": {
                        "latitude": 47.631504,
                        "longitude": -122.125275
                    },
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                }
            }
        ]
    }
    ```


## <a name="real-time-data-for-bike-docking-station"></a>Bisiklet yerleştirme istasyonu için gerçek zamanlı veriler

Azure haritalar Mobility hizmeti 'nin [Transit Aktarma yerleştirme bilgileri API 'si](https://aka.ms/AzureMapsMobilityTransitDock) , belirli bir bisiklet veya bilgi yerleştirme istasyonu için kullanılabilirlik ve açık konum bilgileri gibi statik ve gerçek zamanlı bilgiler istemesine izin verir. Bisiklet için bir yerleşik istasyonla ilgili gerçek zamanlı verileri almak için bir istekte yer vereceğiz.

Get aktarma Dock Info API 'sine bir istek yapmak için bu istasyonda **Dockıd** gerekir. [Çevredeki GEÇIŞ API](https://aka.ms/AzureMapsMobilityNearbyTransit) 'sine yönelik bir arama isteği yaparak ve **ObjectType** parametresini "bikedock" olarak ayarlayarak yuva kimliğini alabilirsiniz. Bisiklet için bir yerleşik istasyonın gerçek zamanlı verilerini almak için aşağıdaki adımları izleyin.


### <a name="get-dock-id"></a>Dock ID Al

**Dockıd**'yi almak için aşağıdaki adımları izleyerek yakında bulunan transit API 'sine yönelik bir istek oluşturun:

1. Postman 'da, **yeni istek** | **Al** isteği ' ne tıklayın ve **takma birimi kimliğini alın**.

2.  Oluşturucu sekmesinde, http **Al** metodunu seçin, AŞAĞıDAKI istek URL 'sini girin ve **Gönder**' e tıklayın.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=121&query=40.7663753,-73.9627498&radius=100&objectType=bikeDock
    ```

3. Başarılı bir istekten sonra aşağıdaki yanıtı alırsınız. Artık yanıttaki kimliğe sahip olduğumuz, daha sonra aktarım noktası bilgi API 'sine yönelik istekte bir sorgu parametresi olarak kullanılabilecek olan **kimliği** görürsünüz.

    ```JSON
    {
        "results": [
            {
                "id": "121---4640799",
                "type": "bikeDock",
                "objectDetails": {
                    "availableVehicles": 0,
                    "vacantLocations": 31,
                    "lastUpdated": "2019-09-07T00:55:19Z",
                    "operatorInfo": {
                        "id": "121---80",
                        "name": "Citi Bike"
                    }
                },
                "position": {
                    "latitude": 40.767128,
                    "longitude": -73.962243
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 40.768039,
                        "longitude": -73.963413
                    },
                    "btmRightPoint": {
                        "latitude": 40.766216,
                        "longitude": -73.961072
                    }
                }
            }
        ]
    }
    ```


### <a name="get-real-time-bike-dock-status"></a>Gerçek zamanlı Bisiklet dock durumunu al

Seçilen yerleştirme için gerçek zamanlı verileri almak üzere aktarma yerleştirme noktası bilgi API 'sine bir istek yapmak için aşağıdaki adımları izleyin.

1. Postman 'da **yeni istek** | **Al** isteği ' ne tıklayın ve **gerçek zamanlı yerleştirme verilerini alın**.

2.  Oluşturucu sekmesinde, http **Al** metodunu seçin, AŞAĞıDAKI istek URL 'sini girin ve **Gönder**' e tıklayın.
 
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/dock/json?subscription-key={subscription-key}&api-version=1.0&query=121---4640799
    ```

3. Başarılı bir istekten sonra, aşağıdaki yapıya bir yanıt alırsınız:

    ```JSON
    {
        "availableVehicles": 0,
        "vacantLocations": 31,
        "position": {
            "latitude": 40.767128,
            "longitude": -73.962246
        },
        "lastUpdated": "2019-09-07T00:55:19Z",
        "operatorInfo": {
            "id": "121---80",
            "name": "Citi Bike"
        }
    }
    ```


## <a name="next-steps"></a>Sonraki adımlar

Mobility hizmetini kullanarak geçiş verileri isteme hakkında bilgi edinin:

> [!div class="nextstepaction"]
> [Geçiş verileri isteme](how-to-request-transit-data.md)

Azure Maps Mobility hizmeti API 'SI belgelerini inceleyin:

> [!div class="nextstepaction"]
> [Mobility hizmeti API 'SI belgeleri](https://aka.ms/AzureMapsMobilityService)
