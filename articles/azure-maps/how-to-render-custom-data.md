---
title: Azure haritalar 'da bir raster eşlemesinde özel verileri işleme | Microsoft Docs
description: Azure haritalar 'da bir raster eşlemesinde özel verileri işleme.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 6619fd842f225a6d362a4b308dde6e35b43677c9
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915751"
---
# <a name="render-custom-data-on-a-raster-map"></a>Bir raster eşlemesinde özel verileri işleme

Bu makalede, bir raster haritasının üzerine Yerpaylaşımları sağlamak için görüntü oluşturma işlevselliğiyle [statik görüntü hizmeti](https://docs.microsoft.com/rest/api/maps/render/getmapimage) 'nin nasıl kullanılacağı açıklanır. Görüntü birleştirme, özel Pushpin, etiket ve geometri Yerpaylaşımları gibi ek verilerle bir raster kutucuğu geri alma özelliği içerir.

Özel pushpın 'leri, etiketleri ve geometri yer paylaşımlarını işlemek için Postman uygulamasını kullanabilirsiniz. Yer paylaşımlarını depolamak ve işlemek için Azure Maps [veri hizmeti API 'lerini](https://docs.microsoft.com/rest/api/maps/data) kullanabilirsiniz.


## <a name="prerequisites"></a>Önkoşullar

### <a name="create-an-azure-maps-account"></a>Azure Haritalar hesabı oluşturma

Bu makaledeki yordamları tamamlayabilmeniz için öncelikle [Hesabı Yönet](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) bölümündeki yönergeleri Izleyerek bir Azure Maps hesabı oluşturmanız ve hesabınıza yönelik birincil bir abonelik anahtarı almak için [birincil anahtar al](./tutorial-search-location.md#getkey) bölümündeki adımları izlemeniz gerekir.


## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Pushpın 'leri etiketlerle ve özel bir görüntüyle işleme

> [!Note]
> Bu bölümdeki yordam, S0 veya S1 fiyatlandırma katmanında bir Azure Maps hesabı gerektirir.

Azure haritalar hesabı S0 katmanı, `pins` parametrenin yalnızca tek bir örneğini destekler. URL isteğinde belirtilen en fazla beş Pushpin (özel bir görüntüyle) oluşturmanızı sağlar.

Pushpın 'leri etiketlerle ve özel bir görüntüyle işlemek için şu adımları izleyin:

1. İsteklerin depolayabileceği bir koleksiyon oluşturun. Postman uygulamasında **Yeni**' yi seçin. **Yeni oluştur** penceresinde **koleksiyon**' ı seçin. Koleksiyonu adlandırın ve **Oluştur** düğmesini seçin. 

2. İsteği oluşturmak için **Yeni** ' yi seçin. **Yeni oluştur** penceresinde **istek**' ı seçin. Pushpin 'ler için bir **istek adı** girin, önceki adımda oluşturduğunuz koleksiyonu, isteğin kaydedileceği konum olarak seçin ve ardından **Kaydet**' i seçin.
    
    ![Postman 'da istek oluşturma](./media/how-to-render-custom-data/postman-new.png)

3. Oluşturucu sekmesinde HTTP Al metodunu seçin ve bir GET isteği oluşturmak için aşağıdaki URL 'YI girin.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttp%3A%2F%2Fazuremapscodesamples.azurewebsites.net%2FCommon%2Fimages%2Fpushpins%2Fylw-pushpin.png
    ```
    Sonuçta elde edilen görüntü:

    ![Etiketli özel iğne](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Azure haritalar veri depolamadan veri al

> [!Note]
> Bu bölümdeki yordam, fiyatlandırma katmanı S1 ' te bir Azure Maps hesabı gerektirir.

Ayrıca, [veri yükleme API](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)'sini kullanarak yolu ve PIN konum bilgilerini elde edebilirsiniz. Yolu ve PIN verilerini karşıya yüklemek için aşağıdaki adımları izleyin.

1. Postman uygulamasında, önceki bölümde oluşturduğunuz koleksiyonda yeni bir sekme açın. Oluşturucu sekmesinde HTTP POST yöntemini seçin ve POST isteği yapmak için aşağıdaki URL 'YI girin:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. Params ( **Parametreler** ) SEKMESINDE, post isteği URL 'si için kullanılan aşağıdaki anahtar/değer çiftlerini girin. `subscription-key` Değeri Azure Maps abonelik anahtarınızla değiştirin.
    
    ![Postman 'daki anahtar/değer parametreleri](./media/how-to-render-custom-data/postman-key-vals.png)

3. **Gövde** sekmesinde, ham giriş biçimini seçin ve açılan listeden giriş BIÇIMI olarak JSON ' ı seçin. Karşıya yüklenecek veri olarak bu JSON 'ı sağlayın:
    
    ```JSON
    {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -73.98235,
                  40.76799
                ],
                [
                  -73.95785,
                  40.80044
                ],
                [
                  -73.94928,
                  40.7968
                ],
                [
                  -73.97317,
                  40.76437
                ],
                [
                  -73.98235,
                  40.76799
                ]
              ]
            ]
          }
        },
        {
          "type": "Feature",
          "properties": {},
          "geometry": {
            "type": "LineString",
            "coordinates": [
              [
                -73.97624731063843,
                40.76560773817073
              ],
              [
                -73.97914409637451,
                40.766826609362575
              ],
              [
                -73.98513078689575,
                40.7585866048861
              ]
            ]
          }
        }
      ]
    }
    ```

4. **Gönder** ' i seçin ve yanıt üst bilgisini gözden geçirin. Başarılı bir istek olduğunda, konum üstbilgisi karşıya yükleme isteğinin geçerli durumunu denetlemek için durum URI 'sini içerir. Durum URI 'SI aşağıdaki biçimde olacaktır.  

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0
   ```

5. Durum URI 'nizi kopyalayın ve abonelik anahtarı parametresini, verileri karşıya yüklemek için kullandığınız Azure Maps hesabı abonelik anahtarınız olan değere ekleyin. Durum URI biçimi aşağıdaki gibi görünmelidir:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. ' I almak için, Postman uygulamasında yeni bir sekme açın ve Oluşturucu sekmesinde HTTP yöntemi Al ' ı seçin ve durum URI 'sinde bir GET isteği yapın. Karşıya veri yükleme işlemi başarılı olduysa yanıt gövdesinde bir UDID alırsınız. Uıdıd 'i kopyalayın.

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. Haritadaki özellikleri işlemek için veri yükleme API 'sinden alınan değerikullanın.`udId` Bunu yapmak için, önceki bölümde oluşturduğunuz koleksiyonda yeni bir sekme açın. Oluşturucu sekmesinde HTTP Al metodunu seçin ve bir GET isteği yapmak için bu URL 'YI girin:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    İşte yanıt görüntüsü:

    ![Azure haritalar veri depolamadan veri al](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Renk ve opaklık ile Çokgen işleme

> [!Note]
> Bu bölümdeki yordam, fiyatlandırma katmanı S1 ' te bir Azure Maps hesabı gerektirir.


[Yol parametresiyle](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters)stil değiştiricilerini kullanarak bir çokgenin görünümünü değiştirebilirsiniz.

1. Postman uygulamasında daha önce oluşturduğunuz koleksiyonda yeni bir sekme açın. Oluşturucu sekmesinde HTTP Al yöntemini seçin ve renk ve opaklık ile bir çokgen işlemek için bir GET isteği yapılandırmak üzere aşağıdaki URL 'YI girin:
    
    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&sku=S1&zoom=14&height=500&Width=500&center=-74.040701, 40.698666&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.50||-74.03995513916016 40.70090237454063|-74.04082417488098 40.70028420372218|-74.04113531112671 40.70049568385827|-74.04298067092896 40.69899904076542|-74.04271245002747 40.69879568992435|-74.04367804527283 40.6980961582905|-74.04364585876465 40.698055487620714|-74.04368877410889 40.698022951066996|-74.04168248176573 40.696444909137|-74.03901100158691 40.69837271818651|-74.03824925422668 40.69837271818651|-74.03809905052185 40.69903971085914|-74.03771281242369 40.699340668780984|-74.03940796852112 40.70058515602143|-74.03948307037354 40.70052821920425|-74.03995513916016 40.70090237454063
    &subscription-key={subscription-key}
    ```

    İşte yanıt görüntüsü:

    ![Donuk bir çokgen oluşturma](./media/how-to-render-custom-data/opaque-polygon.png)


## <a name="render-a-circle-and-pushpins-with-custom-labels"></a>Özel etiketlerle bir daire ve Pushpin işleme

> [!Note]
> Bu bölümdeki yordam, fiyatlandırma katmanı S1 ' te bir Azure Maps hesabı gerektirir.


`sc` Ölçek stili değiştiricisini kullanarak Pushpin ve etiketlerini daha büyük veya daha küçük hale getirebilirsiniz. Bu değiştirici sıfırdan büyük bir değer alır. 1 değeri standart ölçeğe sahiptir. 1 ' den büyük değerler, PIN 'leri daha büyük yapar ve 1 ' den küçük değerler bunları daha küçük hale getirir. Stil değiştiriciler hakkında daha fazla bilgi için bkz. [statik görüntü hizmeti yol parametreleri](https://docs.microsoft.com/rest/api/maps/render/getmapimage#uri-parameters).


Özel etiketlerle bir daire ve Pushpin işlemek için aşağıdaki adımları izleyin:

1. Postman uygulamasında daha önce oluşturduğunuz koleksiyonda yeni bir sekme açın. Oluşturucu sekmesinde HTTP Al metodunu seçin ve bir GET isteği yapmak için bu URL 'YI girin:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    İşte yanıt görüntüsü:

    ![Özel raptiyeler ile bir daire işleme](./media/how-to-render-custom-data/circle-custom-pins.png)

## <a name="next-steps"></a>Sonraki adımlar


* [Azure haritalar harita görüntüsünü Al API](https://docs.microsoft.com/rest/api/maps/render/getmapimage) belgelerini inceleyin.
* Azure haritalar veri hizmeti hakkında daha fazla bilgi edinmek için bkz. [hizmet belgeleri](https://docs.microsoft.com/rest/api/maps/data).

