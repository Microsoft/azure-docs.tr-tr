---
title: Azure haritalar yükseltme hizmetini kullanarak yükseltme verileri isteme (Önizleme)
description: Azure haritalar yükseltme hizmeti 'ni (Önizleme) kullanarak yükseltme verileri isteme hakkında bilgi edinin.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: d14eda84144105bf2e04f1238284bc58a91c4c03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98684064"
---
# <a name="request-elevation-data-using-the-azure-maps-elevation-service-preview"></a>Azure haritalar yükseltme hizmetini kullanarak yükseltme verileri isteme (Önizleme)

> [!IMPORTANT]
> Azure haritalar yükseltme hizmeti şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure haritalar [yükseltme hizmeti](/rest/api/maps/elevation) , dünyanın her yerindeki yükseltme verilerini sorgulamak Için API 'ler sağlar. Tanımlanmış bir sınırlama kutusu içinde veya belirli koordinatlarda, bir veya daha fazla şekilde örneklenmiş yükseltme verileri isteyebilirsiniz. Ayrıca, yükseltme verilerini kutucuk biçiminde almak için [Işleme v2-harita kutucuğu API](/rest/api/maps/renderv2) 'sini kullanabilirsiniz. Kutucuklar GeoTIFF raster biçiminde teslim edilir. Bu makalede, yükseltme verilerini istemek için Azure haritalar yükseltme hizmeti ve harita alma kutucuğu API 'sinin nasıl kullanılacağı gösterilir. Yükseltme verileri hem GeoJSON hem de GeoTiff biçimlerinde istenebilir.

## <a name="prerequisites"></a>Önkoşullar

1. [S1 fiyatlandırma katmanında bir Azure Maps hesabı oluşturun](quick-demo-map-app.md#create-an-azure-maps-account)
2. Birincil anahtar veya abonelik anahtarı olarak da bilinen [birincil bir abonelik anahtarı alın](quick-demo-map-app.md#get-the-primary-key-for-your-account).

Azure haritalar 'da kimlik doğrulaması hakkında daha fazla bilgi için [Azure haritalar 'Da kimlik doğrulamasını yönetin](how-to-manage-authentication.md).

Bu makale [Postman](https://www.postman.com/) uygulamasını kullanır, ancak farklı bir API geliştirme ortamı seçebilirsiniz.

## <a name="request-elevation-data-in-raster-tiled-format"></a>Tarama döşenmiş biçimdeki yükseltme verileri iste

Raster kutucuk biçiminde yükseltme verileri istemek için, [oluşturma v2-Get Map kutucuğu API](/rest/api/maps/renderv2)'sini kullanın. Kutucuk bulunamıyorsa, API, kutucuğu bir GeoTIFF olarak döndürür. Aksi takdirde, API 0 döndürür. Tüm raster DEM kutucukları, GeoId (Sea düzeyi) Dünya modunu kullanıyor. Bu örnekte, mt için yükseltme verileri isteyeceğiz. Her bir yaprak.

>[!TIP]
>Dünya eşlemesindeki belirli bir alanda kutucuk almak için uygun yakınlaştırma düzeyinde doğru kutucuğu bulmanız gerekir. Ayrıca, WorldDEM genel yer kaplamasından, ancak Oceans kapsamadığı unutulmamalıdır.  Daha fazla bilgi için bkz. [yakınlaştırma düzeyleri ve kutucuk Kılavuzu](zoom-levels-and-tile-grid.md).

1. Postman uygulamasını açın. Postman uygulamasının üst kısmında **Yeni**' yi seçin. **Yeni oluştur** penceresinde **koleksiyon**' ı seçin.  Koleksiyonu adlandırın ve **Oluştur** düğmesini seçin.

2. İsteği oluşturmak için **Yeni** ' yi seçin. **Yeni oluştur** penceresinde **istek**' ı seçin. İstek için bir **istek adı** girin. Önceki adımda oluşturduğunuz koleksiyonu seçin ve ardından **Kaydet**' i seçin.

3. Oluşturucu sekmesinde http **Al** yöntemini seçin ve raster kutucuğunu istemek için aşağıdaki URL 'yi girin. Bu istek ve bu makalede bahsedilen diğer istekler için, `{Azure-Maps-Primary-Subscription-key}` birincil abonelik anahtarınızla değiştirin.

    ```http
    https://atlas.microsoft.com/map/tile?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=2.0&tilesetId=microsoft.dem&zoom=13&x=6074&y=3432
    ```

4. **Gönder** düğmesine tıklayın. Yükseltme verilerini içeren raster kutucuğunu GeoTIFF biçiminde almalısınız. Raster kutucuğu ham verilerinin içindeki her bir pikselin türü `float` . Her pikselin değeri, ölçü cinsinden yükseltme yüksekliğini temsil eder.

## <a name="request-elevation-data-in-geojson-format"></a>GeoJSON biçiminde istek yükseltme verileri

GeoJSON biçiminde yükseltme verileri istemek için yükseltme hizmeti (Önizleme) API 'Lerini kullanın. Bu bölümde, üç API 'den her biri gösterilir:

* [Noktalara yönelik verileri al](/rest/api/maps/elevation/getdataforpoints)
* [Noktaları için veri Gönder](/rest/api/maps/elevation/postdataforpoints)
* [Çoklu çizgi için veri al](/rest/api/maps/elevation/getdataforpolyline)
* [Çoklu çizgi için veri Gönder](/rest/api/maps/elevation/postdataforpolyline)
* [Sınırlayıcı kutu için veri al](/rest/api/maps/elevation/getdataforboundingbox)

>[!IMPORTANT]
> Hiçbir veri döndürülmediğinde, tüm API 'Ler döndürülür `0` .

### <a name="request-elevation-data-for-points"></a>Noktalara yönelik yükseltme verileri iste

Bu örnekte, MT 'de yükseltme verileri istemek için [noktalara yönelik verileri al API 'sini](/rest/api/maps/elevation/getdataforpoints) kullanacağız. Marest ve Chamlang mountaıns. Daha sonra, aynı iki noktayı kullanarak yükseltme verileri istemek için [puntolar API 'si API 'sini](/rest/api/maps/elevation/postdataforpoints) kullanacağız. URL 'deki Latitudes ve Longitudes 'in, WGS84 (Dünya coğrafi sistem) ondalık derecesindeki olması beklenir.

 >[!IMPORTANT]
 >2048 URL karakter uzunluğu sınırı nedeniyle, bir URL GET isteğinde işlem hattı ile ayrılmış bir dize olarak 100 ' den fazla koordinat geçirmek mümkün değildir. İşlem hattı ile ayrılmış bir dize olarak 100 ' den fazla koordinat geçirmek istiyorsanız, noktalara Ilişkin POST verilerini kullanın.

1. İsteği oluşturmak için **Yeni** ' yi seçin. **Yeni oluştur** penceresinde **istek**' ı seçin. İstek için bir **istek adı** girin. Önceki adımda oluşturduğunuz koleksiyonu seçin ve ardından **Kaydet**' i seçin.

2. Oluşturucu sekmesinde http **Al** metodunu seçin ve aşağıdaki URL 'yi girin. Bu istek ve bu makalede bahsedilen diğer istekler için, `{Azure-Maps-Primary-Subscription-key}` birincil abonelik anahtarınızla değiştirin.

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&points=-73.998672,40.714728|150.644,-34.397
    ```

3. **Gönder** düğmesine tıklayın.  Aşağıdaki JSON yanıtını alırsınız:

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 40.714728,
                "longitude": -73.998672
            },
            "elevationInMeter": 12.142355447638208
        },
        {
            "coordinate": {
                "latitude": -34.397,
                "longitude": 150.644
            },
            "elevationInMeter": 384.47041445517846
        }
    ]
    }
    ```

4. Şimdi aynı iki noktaya yönelik yükseltme verileri almak için [Points API 'sine yönelik verileri](/rest/api/maps/elevation/postdataforpoints) arayacağız. Oluşturucu sekmesinde http **Post** yöntemini seçin ve aşağıdaki URL 'yi girin. Bu istek ve bu makalede bahsedilen diğer istekler için, `{Azure-Maps-Primary-Subscription-key}` birincil abonelik anahtarınızla değiştirin.

    ```http
    https://atlas.microsoft.com/elevation/point/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. **Post** Isteğinin **üst bilgilerinde** , `Content-Type` olarak ayarlanır `application/json` . **Gövdede**, aşağıdaki koordinat noktası bilgilerini girin. İşiniz bittiğinde **Gönder**' e tıklayın.

     ```json
    [
        {
            "lon": -73.998672,
            "lat": 40.714728
        },
        {
            "lon": 150.644,
            "lat": -34.397
        }
    ]
    ```

### <a name="request-elevation-data-samples-along-a-polyline"></a>Çoklu çizgi üzerinde yükseltme veri örnekleri iste

Bu örnekte, her bir [Çoklu çizgi Için veri al](/rest/api/maps/elevation/getdataforpolyline) ' ı kullanarak, her Ikisi de MT 'deki koordinatlar arasında düz bir çizgi boyunca, eşit oranda yükseltme verisi örnekleri talep ederiz. Marest ve Chamlang mountaıns. Her iki koordinat de Long/Lat biçiminde tanımlanmalıdır. Parametresi için bir değer belirtmezseniz `samples` , örnek sayısı varsayılan olarak 10 ' dur. En fazla örnek sayısı 2.000 ' dir.

Daha sonra, bir yol boyunca aynı şekilde yükseltme verilerinin üç eşit olarak yer aldığı örnek istemek için çoklu çizgi için veri al ' ı kullanacağız. Üç Long/Lat koordinat çiftlerine geçirerek örneklerin kesin konumunu tanımlayacağız.

Son olarak, aynı üç eşit aralıklı örnek üzerinde yükseltme verileri istemek için [Çoklu çizgi API 'Si Için post verilerini](/rest/api/maps/elevation/postdataforpolyline) kullanacağız.

URL 'deki Latitudes ve Longitudes 'in, WGS84 (Dünya coğrafi sistem) ondalık derecesindeki olması beklenir.

 >[!IMPORTANT]
 >2048 URL karakter uzunluğu sınırı nedeniyle, bir URL GET isteğinde işlem hattı ile ayrılmış bir dize olarak 100 ' den fazla koordinat geçirmek mümkün değildir. İşlem hattı ile ayrılmış bir dize olarak 100 ' den fazla koordinat geçirmek istiyorsanız, noktalara Ilişkin POST verilerini kullanın.

1. **Yeni**'yi seçin. **Yeni oluştur** penceresinde **istek**' ı seçin. Bir **istek adı** girin ve bir koleksiyon seçin. **Kaydet**’e tıklayın.

2. Oluşturucu sekmesinde http **Al** metodunu seçin ve aşağıdaki URL 'yi girin. Bu istek ve bu makalede bahsedilen diğer istekler için, `{Azure-Maps-Primary-Subscription-key}` birincil abonelik anahtarınızla değiştirin.

   ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&lines=-73.998672,40.714728|150.644,-34.397&samples=5
    ```

3. **Gönder** düğmesine tıklayın.  Aşağıdaki JSON yanıtını alırsınız:

    ```JSON
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 40.714728,
                    "longitude": -73.998672
                },
                "elevationInMeter": 12.14236
            },
            {
                "coordinate": {
                    "latitude": 21.936796000000001,
                    "longitude": -17.838003999999998
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": 3.1588640000000012,
                    "longitude": 38.322664000000003
                },
                "elevationInMeter": 598.66943
            },
            {
                "coordinate": {
                    "latitude": -15.619067999999999,
                    "longitude": 94.483332000000019
                },
                "elevationInMeter": 0.0
            },
            {
                "coordinate": {
                    "latitude": -34.397,
                    "longitude": 150.644
                },
                "elevationInMeter": 384.47041
            }
        ]
    }
    ```

4. Şimdi, Mount, Chamlang ve marnu dağııns 'teki koordinatlar arasında bir yol üzerinde üç yükseltme verisi örneği isteyeceğiz. **Params** bölümünde, sorgu anahtarının değeri için aşağıdaki koordinat dizisini kopyalayın `lines` .

    ```html
        86.9797222, 27.775|86.9252778, 27.9880556 | 88.0444444, 27.6822222
    ```

5. `samples`Sorgu anahtarı değerini olarak değiştirin `3` .  Aşağıdaki görüntüde yeni değerler gösterilmektedir.

     :::image type="content" source="./media/how-to-request-elevation-data/get-elevation-samples.png" alt-text="Üç yükseltme verisi örneği alın.":::

6. Mavi **Gönder** düğmesine tıklayın. Aşağıdaki JSON yanıtını alırsınız:

    ```json
    {
        "data": [
            {
                "coordinate": {
                    "latitude": 27.775,
                    "longitude": 86.9797222
                },
                "elevationInMeter": 7116.0348851572589
            },
            {
                "coordinate": {
                    "latitude": 27.737403546316028,
                    "longitude": 87.411180791156454
                },
                "elevationInMeter": 1798.6945512521534
            },
            {
                "coordinate": {
                    "latitude": 27.682222199999998,
                    "longitude": 88.0444444
                },
                "elevationInMeter": 7016.9372013588072
            }
        ]
    }
    ```

7. Artık aynı üç noktaya yönelik yükseltme verilerini almak için [Çoklu çizgi API 'Si Için post verilerini](/rest/api/maps/elevation/postdataforpolyline) çağıracağız. Oluşturucu sekmesinde http **Post** yöntemini seçin ve aşağıdaki URL 'yi girin. Bu istek ve bu makalede bahsedilen diğer istekler için, `{Azure-Maps-Primary-Subscription-key}` birincil abonelik anahtarınızla değiştirin.

    ```http
    https://atlas.microsoft.com/elevation/line/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&samples=5
    ```

8. **Post** Isteğinin **üst bilgilerinde** , `Content-Type` olarak ayarlanır `application/json` . **Gövdede**, aşağıdaki koordinat noktası bilgilerini girin. İşiniz bittiğinde **Gönder**' e tıklayın.

     ```json
    [
        {
            "lon": 86.9797222,
            "lat": 27.775
        },
        {
            "lon": 86.9252778,
            "lat": 27.9880556
        },
        {
            "lon": 88.0444444,
            "lat": 27.6822222
        }
    ]
    ```

### <a name="request-elevation-data-by-bounding-box"></a>Sınırlama kutusuna göre yükseltme verileri iste

Şimdi, veri al ' a yakın bir yükseltme verisi istemek için [sınırlayıcı Için veri al kutusunu](/rest/api/maps/elevation/getdataforboundingbox) kullanacağız. Rainier, WA. Yükseltme verileri, bir sınırlayıcı kutu içindeki eşit aralıklı konumlarda döndürülür. (2) enlem/uzun koordinatları (Güney Latitude, Batı boylam | kuzey enlem, Doğu Boylam) tarafından tanımlanan sınırlayıcı alan, satırlara ve sütunlara bölünür. Satırların iki (2) için sınırlayıcı kutu hesabının kenarları ve iki (2) sütun. Satır ve sütun kesişimlerde oluşturulan kılavuz köşeleri için yükseltme döndürülür. Tek bir istekte 2000 ' e kadar yükseltme döndürülebilir.

Bu örnekte, satırları = 3 ve sütun = 6 olarak belirteceğiz. yanıtta 18 yükseltme değeri döndürülür. Aşağıdaki diyagramda, yükseltme değerleri güneybatı köşesinden başlayarak sıralanır ve Batı ile Doğu ve Güney-Kuzey arasında devam eder.  Yükseltme noktaları, döndürültikleri sırada numaralandırılır.

:::image type="content" source="./media/how-to-request-elevation-data/bounding-box.png" border="false" alt-text="NE ve o köşeler için sınırlayıcı kutu koordinatları.":::

1. **Yeni**'yi seçin. **Yeni oluştur** penceresinde **istek**' ı seçin. Bir **istek adı** girin ve bir koleksiyon seçin. **Kaydet**’e tıklayın.

2. Oluşturucu sekmesinde http **Al** metodunu seçin ve aşağıdaki URL 'yi girin. Bu istek ve bu makalede bahsedilen diğer istekler için, `{Azure-Maps-Primary-Subscription-key}` birincil abonelik anahtarınızla değiştirin.

    ```http
    https://atlas.microsoft.com/elevation/lattice/json?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&bounds=-121.66853362143818, 46.84646479863713,-121.65853362143818, 46.85646479863713&rows=2&columns=3
    ```

3. Mavi **Gönder** düğmesine tıklayın. 18 ' in her köşesi için bir tane olmak üzere 18 yükseltme veri örneği, yanıtta döndürülür.

    ```json
    {
    "data": [
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2298.6581875651746
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2306.3980756609963
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2279.3385479564113
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2233.1549264690366
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2196.4485923541492
        },
        {
            "coordinate": {
                "latitude": 46.846464798637129,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2133.1756767157253
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2345.3227848228803
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2292.2449195443587
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2270.5867788258074
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2296.8311427390604
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2266.0729430891065
        },
        {
            "coordinate": {
                "latitude": 46.849798131970459,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2242.216346631234
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2378.460838833359
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2327.6761137260387
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2208.3782743402949
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2106.9526472760981
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2054.3270174034078
        },
        {
            "coordinate": {
                "latitude": 46.8531314653038,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 2030.6438331110671
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66853362143819
            },
            "elevationInMeter": 2318.753153399402
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66653362143819
            },
            "elevationInMeter": 2253.88875188271
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66453362143818
            },
            "elevationInMeter": 2136.6145845357587
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66253362143819
            },
            "elevationInMeter": 2073.6734467948486
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.66053362143818
            },
            "elevationInMeter": 2042.994055784251
        },
        {
            "coordinate": {
                "latitude": 46.856464798637127,
                "longitude": -121.65853362143818
            },
            "elevationInMeter": 1988.3631481900356
        }
    ]
    }
    ```

## <a name="samples-use-elevation-service-preview-apis-in-azure-maps-control"></a>Örnekler: Azure Maps denetimindeki yükseltme hizmeti (Önizleme) API 'Lerini kullanma

### <a name="get-elevation-data-by-coordinate-position"></a>Koordinat konumuna göre yükseltme verileri al

Aşağıdaki örnek Web sayfası, bir koordinat noktasında yükseltme verilerini göstermek için harita denetimini nasıl kullanacağınızı gösterir. Kullanıcı işaretçiyi sürüklediğinde harita, bir açılan pencerede yükseltme verilerini görüntüler.

<br/>

<iframe height="500" style="width:100%;" scrolling="no" title="Konumda yükseltme al" src="https://codepen.io/azuremaps/embed/c840b510e113ba7cb32809591d5f96a2?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Codepen 'da Azure Maps () tarafından <a href='https://codepen.io/azuremaps/pen/c840b510e113ba7cb32809591d5f96a2'>konum üzerinde yükseltme al</a> bölümüne bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

### <a name="get-elevation-data-by-bounding-box"></a>Sınırlama kutusuna göre yükseltme verileri al

Aşağıdaki örnek Web sayfası, bir sınırlayıcı kutu içinde bulunan yükseltme verilerini göstermek için harita denetimini nasıl kullanacağınızı gösterir. Kullanıcı, `square` sol üst köşedeki simgeye tıklayarak ve sonra kareyi haritanın herhangi bir yerinden çizerek sınırlayıcı kutuyu tanımlar. Eşleme denetimi daha sonra, sağ üst köşede bulunan anahtarda belirtilen renklerle uyumlu olarak yükseltme verilerini işler.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Sınırlayıcı kutu ile yükseltme" src="https://codepen.io/azuremaps/embed/619c888c70089c3350a3e95d499f3e48?height=500&theme-id=default&default-tab=js,result" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Codepen üzerinde Azure Maps () ile bir <a href='https://codepen.io/azuremaps/pen/619c888c70089c3350a3e95d499f3e48'>sınırlayıcı kutu</a> Ile, kalemin yükseltme bölümüne bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

### <a name="get-elevation-data-by-polyline-path"></a>Çoklu çizgi yoluna göre yükseltme verileri al

Aşağıdaki örnek Web sayfası, bir yol üzerinde yükseltme verilerini göstermek için harita denetimini nasıl kullanacağınızı gösterir. Kullanıcı, `Polyline` sol üst köşedeki simgeye tıklayarak ve çoklu çizgiyi haritaya çizerek yolu tanımlar. Harita denetimi daha sonra sağ üst köşede yer alan anahtarda belirtilen renklerde yükseltme verilerini işler.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Yükseltme yolu gradyanı" src="https://codepen.io/azuremaps/embed/7bee08e5cb13d05cb0a11636b60f14ca?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder="no" loading="lazy" allowtransparency="true" allowfullscreen="true">
Codepen üzerinde Azure Maps () ile kalem <a href='https://codepen.io/azuremaps/pen/7bee08e5cb13d05cb0a11636b60f14ca'>yükseltme yolu degradesini</a> inceleyin <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>


## <a name="next-steps"></a>Sonraki adımlar

Azure haritalar yükseltme (Önizleme) API 'Lerini daha ayrıntılı incelemek için bkz.:

> [!div class="nextstepaction"]
> [Yükseltme (Önizleme)-enlem uzun koordinatları için veri al](/rest/api/maps/elevation/getdataforpoints)

> [!div class="nextstepaction"]
> [Yükseltme (Önizleme)-sınırlayıcı kutu için veri al](/rest/api/maps/elevation/getdataforboundingbox)

> [!div class="nextstepaction"]
> [Yükseltme (Önizleme)-çoklu çizgi için veri al](/rest/api/maps/elevation/getdataforpolyline)

> [!div class="nextstepaction"]
> [Render v2: harita kutucuğunu al](/rest/api/maps/renderv2)

Azure haritalar REST API 'lerinin tüm listesi için bkz.:

> [!div class="nextstepaction"]
> [Azure haritalar REST API 'Leri](/rest/api/maps/)