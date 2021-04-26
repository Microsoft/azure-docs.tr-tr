---
title: Android haritalar üzerinde trafik verilerini göster | Microsoft Azure haritaları
description: Bu makalede, Microsoft Azure haritaları Android SDK kullanarak bir Haritada trafik verileri görüntüleme hakkında bilgi edineceksiniz.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: aabe246c343537a42c33d3eaad0bfae3989022fe
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604524"
---
# <a name="show-traffic-data-on-the-map-android-sdk"></a>Haritadaki trafik verilerini göster (Android SDK)

Akış verileri ve olay verileri, haritada görüntülenebilen iki trafik verisi türüdür. Bu kılavuzda her iki trafik verisi türünün nasıl görüntüleneceği gösterilmektedir. Olaylar verileri, kurulumlarını, yol kapanışları ve kazalardan dolayı gibi şeyler için nokta ve hat tabanlı verilerden oluşur. Akış verileri, yolda trafik akışı hakkında ölçümleri gösterir.

## <a name="prerequisites"></a>Önkoşullar

[Hızlı başlangıç: Android uygulama belgesi oluşturma](quick-android-map.md) ' daki adımları tamamladığınızdan emin olun. Bu makaledeki kod blokları haritalar `onReady` olay işleyicisine eklenebilir.

## <a name="show-traffic-on-the-map"></a>Haritada trafiği göster

Azure haritalar 'da kullanılabilen iki tür trafik verisi vardır:

- Olay verileri-oluşturma, yol kapanışları ve kazalardan dolayı gibi şeyler için nokta ve hat tabanlı verilerden oluşur.
- Akış verileri-yollar üzerindeki trafik akışı hakkında ölçümler sağlar. Genellikle trafik akışı verileri, yolların renklendirmek için kullanılır. Renkler, akışı yavaşlatan, hız sınırına veya başka bir ölçüye göre ne kadar trafik altına göre belirlenir. Haritanın trafik seçeneğine geçirilebilecek dört değer vardır `flow` .

    |Akış değeri | Description|
    | :-- | :-- |
    | TrafficFlow. NONE | Harita üzerinde trafik verilerini görüntülemiyor |
    | TrafficFlow. GÖRELI | Yolun serbest akış hızına göre trafik verilerini gösterir |
    | TrafficFlow.RELATIVE_DELAY | Beklenen ortalama gecikmeden daha yavaş olan bölgeleri görüntüler |
    | TrafficFlow. ABSOLUTE | Yolda tüm taşıtın mutlak hızını gösterir |

Aşağıdaki kod, Haritada trafik verilerinin nasıl görüntüleneceğini gösterir.

::: zone pivot="programming-language-java-android"

```java
//Show traffic on the map using the traffic options.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
)
```

::: zone-end

Aşağıdaki ekran görüntüsünde, haritadaki gerçek zamanlı trafik bilgilerini Yukarıdaki kod işleme gösterilmektedir.

![Gerçek zamanlı trafik bilgilerini gösteren harita](media/how-to-show-traffic-android/android-show-traffic.png)

## <a name="get-traffic-incident-details"></a>Trafik olay ayrıntılarını al

Bir trafik olayıyla ilgili ayrıntılar, haritada olayı göstermek için kullanılan özelliğin özellikleri içinde mevcuttur. Trafik olayları, Azure Maps Trafik olayı vektör kutucuk hizmeti kullanılarak haritaya eklenir. [Burada belgelendiği](https://developer.tomtom.com/traffic-api/traffic-api-documentation-traffic-incidents/vector-incident-tiles)durumlarda bu vektör kutucuklarında verilerin biçimi. Aşağıdaki kod, haritaya bir tıklama olayı ekler ve tıklanan trafik olayı özelliğini alır ve bazı ayrıntıların bulunduğu bir bildirim iletisi görüntüler.

::: zone pivot="programming-language-java-android"

```java
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
);

//Add a click event to the map.
map.events.add((OnFeatureClick) (features) -> {

    if (features != null && features.size() > 0) {
        Feature incident = features.get(0);

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {

            StringBuilder sb = new StringBuilder();
            String incidentType = incident.getStringProperty("incidentType");

            if (incidentType != null) {
                sb.append(incidentType);
            }

            if (sb.length() > 0) {
                sb.append("\n");
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed".equals(incidentType)) {
                String from = incident.getStringProperty("from");

                if (from != null) {
                    sb.append(from);
                }
            } else {
                //Get the description of the traffic incident.
                String description = incident.getStringProperty("description");

                if (description != null) {
                    sb.append(description);
                }
            }

            String message = sb.toString();

            if (message.length() > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show();
            }
        }
    }
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Show traffic information on the map.
map.setTraffic(
    incidents(true),
    flow(TrafficFlow.RELATIVE)
)

//Add a click event to the map.
map.events.add(OnFeatureClick { features: List<Feature>? ->
    if (features != null && features.size > 0) {
        val incident = features[0]

        //Ensure that the clicked feature is an traffic incident feature.
        if (incident.properties() != null && incident.hasProperty("incidentType")) {
            val sb = StringBuilder()
            val incidentType = incident.getStringProperty("incidentType")

            if (incidentType != null) {
                sb.append(incidentType)
            }

            if (sb.length > 0) {
                sb.append("\n")
            }

            //If the road is closed, find out where it is closed from.
            if ("Road Closed" == incidentType) {
                val from = incident.getStringProperty("from")
                if (from != null) {
                    sb.append(from)
                }
            } else { //Get the description of the traffic incident.
                val description = incident.getStringProperty("description")
                if (description != null) {
                    sb.append(description)
                }
            }

            val message = sb.toString()
            if (message.length > 0) {
                Toast.makeText(this, message, Toast.LENGTH_LONG).show()
            }
        }
    }
})
```

::: zone-end

Aşağıdaki ekran görüntüsünde, olay ayrıntılarını görüntüleyen bir bildirim iletisi ile haritadaki gerçek zamanlı trafik bilgilerini içeren Yukarıdaki kod görüntülenir.

![Olay ayrıntılarını görüntüleyen bir bildirim iletisi ile gerçek zamanlı trafik bilgilerini gösteren harita](media/how-to-show-traffic-android/android-traffic-details.png)

## <a name="next-steps"></a>Sonraki adımlar

Haritanızda daha fazla veri eklemeyi öğrenmek için aşağıdaki kılavuzlara bakın:

> [!div class="nextstepaction"]
> [Kutucuk katmanı ekleme](how-to-add-tile-layer-android-map.md)
