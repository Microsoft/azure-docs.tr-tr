---
title: Bir raster eşlemesinde özel verileri işleme | Microsoft Azure haritaları
description: Bir raster eşlemesine Pushpin, etiket ve geometrik şekillerin nasıl ekleneceğini öğrenin. Bu amaçla Azure Maps 'ta statik görüntü hizmetini nasıl kullanacağınızı öğrenin.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 5c70835c11bafb3fd06645ba51099b33d1eb6149
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96906089"
---
# <a name="render-custom-data-on-a-raster-map"></a>Bir raster eşlemesinde özel verileri işleme

Bu makalede, bir raster haritasının üzerine Yerpaylaşımları sağlamak için görüntü oluşturma işleviyle [statik görüntü hizmetinin](/rest/api/maps/render/getmapimage)nasıl kullanılacağı açıklanmaktadır. Görüntü birleştirme, özel Pushpin, etiket ve geometri Yerpaylaşımları gibi ek verilerle bir raster kutucuğu geri alma özelliği içerir.

Özel pushpın 'leri, etiketleri ve geometri yer paylaşımlarını işlemek için Postman uygulamasını kullanabilirsiniz. Yer paylaşımlarını depolamak ve işlemek için Azure Maps [veri hizmeti API 'lerini](/rest/api/maps/data) kullanabilirsiniz.

> [!Tip]
> Azure Haritalar Web SDK 'sını kullanarak, bir Web sayfasında statik görüntü hizmetini kullanmaktan daha basit bir harita göstermek için çok daha uygun maliyetli hale gelir. Web SDK 'Sı harita kutucukları kullanır ve Kullanıcı haritayı yakınlaştırıp yakınlaşmadığı takdirde, genellikle harita yükü başına bir işlemin yalnızca bir bölümünü oluşturur. Azure Maps web SDK 'sının yatay kaydırmayı ve yakınlaştırmasını devre dışı bırakma seçenekleri olduğunu unutmayın. Ayrıca, Azure Maps web SDK 'Sı statik bir harita Web hizmetinden daha zengin bir veri görselleştirme seçenekleri kümesi sağlar.  

## <a name="prerequisites"></a>Önkoşullar

### <a name="create-an-azure-maps-account"></a>Azure Haritalar hesabı oluşturma

Bu makaledeki yordamları tamamlayabilmeniz için öncelikle bir Azure Maps hesabı oluşturmanız ve haritalar hesap anahtarınızı almanız gerekir. Hesap [oluşturma](quick-demo-map-app.md#create-an-azure-maps-account) ' daki yönergeleri Izleyerek Azure Maps hesap aboneliği oluşturun ve hesabınızın birincil anahtarını almak için [birincil anahtar al](quick-demo-map-app.md#get-the-primary-key-for-your-account) bölümündeki adımları izleyin. Azure haritalar 'da kimlik doğrulaması hakkında daha fazla bilgi için bkz. [Azure haritalar 'da kimlik doğrulamasını yönetme](./how-to-manage-authentication.md).


## <a name="render-pushpins-with-labels-and-a-custom-image"></a>Pushpın 'leri etiketlerle ve özel bir görüntüyle işleme

> [!Note]
> Bu bölümdeki yordam, S0 veya S1 fiyatlandırma katmanında bir Azure Maps hesabı gerektirir.

Azure haritalar hesabı S0 katmanı, parametrenin yalnızca tek bir örneğini destekler `pins` . URL isteğinde belirtilen en fazla beş Pushpin (özel bir görüntüyle) oluşturmanızı sağlar.

Pushpın 'leri etiketlerle ve özel bir görüntüyle işlemek için şu adımları izleyin:

1. İsteklerin depolayabileceği bir koleksiyon oluşturun. Postman uygulamasında **Yeni**' yi seçin. **Yeni oluştur** penceresinde **koleksiyon**' ı seçin. Koleksiyonu adlandırın ve **Oluştur** düğmesini seçin. 

2. İsteği oluşturmak için **Yeni** ' yi seçin. **Yeni oluştur** penceresinde **istek**' ı seçin. Pushpin 'ler için bir **istek adı** girin. Önceki adımda oluşturduğunuz koleksiyonu, isteğin kaydedileceği konum olarak seçin. Sonra **Kaydet**' i seçin.
    
    ![Postman 'da istek oluşturma](./media/how-to-render-custom-data/postman-new.png)

3. Oluşturucu sekmesinde HTTP Al metodunu seçin ve bir GET isteği oluşturmak için aşağıdaki URL 'YI girin.

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.98,%2040.77&pins=custom%7Cla15+50%7Cls12%7Clc003b61%7C%7C%27CentralPark%27-73.9657974+40.781971%7C%7Chttps%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FAzureMapsCodeSamples%2Fmaster%2FAzureMapsCodeSamples%2FCommon%2Fimages%2Ficons%2Fylw-pushpin.png
    ```
    Sonuçta elde edilen görüntü:

    ![Etiketli özel iğne](./media/how-to-render-custom-data/render-pins.png)


## <a name="get-data-from-azure-maps-data-storage"></a>Azure haritalar veri depolamadan veri al

> [!Note]
> Bu bölümdeki yordam, fiyatlandırma katmanı S1 ' te bir Azure Maps hesabı gerektirir.

Ayrıca, [veri yükleme API](/rest/api/maps/data/uploadpreview)'sini kullanarak yolu ve PIN konum bilgilerini elde edebilirsiniz. Yolu ve PIN verilerini karşıya yüklemek için aşağıdaki adımları izleyin.

1. Postman uygulamasında, önceki bölümde oluşturduğunuz koleksiyonda yeni bir sekme açın. Oluşturucu sekmesinde HTTP POST yöntemini seçin ve POST isteği yapmak için aşağıdaki URL 'YI girin:

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

2. Params ( **Parametreler** ) SEKMESINDE, post isteği URL 'si için kullanılan aşağıdaki anahtar/değer çiftlerini girin. `subscription-key`Değeri Azure Maps abonelik anahtarınızla değiştirin.
    
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

5. Durum URI 'nizi kopyalayın ve abonelik anahtarı parametresini Azure haritalar hesabı abonelik anahtarınızın değeriyle birlikte ekleyin. Verileri karşıya yüklemek için kullandığınız hesap abonelik anahtarını kullanın. Durum URI biçimi aşağıdaki gibi görünmelidir:

   ```HTTP
   https://atlas.microsoft.com/mapData/{uploadStatusId}/status?api-version=1.0&subscription-key={Subscription-key}
   ```

6. UDID 'yi almak için Postman uygulamasında yeni bir sekme açın. Oluşturucu sekmesinde HTTP yöntemini Al ' ı seçin. Durum URI 'sinde bir GET isteği yapın. Karşıya veri yükleme işlemi başarılı olduysa yanıt gövdesinde bir UDID alırsınız. Uıdıd 'i kopyalayın.

   ```JSON
   {
      "udid" : "{udId}"
   }
   ```

7. `udId`Haritadaki özellikleri işlemek Için veri yükleme API 'sinden alınan değeri kullanın. Bunu yapmak için, önceki bölümde oluşturduğunuz koleksiyonda yeni bir sekme açın. Oluşturucu sekmesinde HTTP Al metodunu seçin, {Subscription-Key} ve {UDID} değerlerini değerlerinizle değiştirin ve bir GET isteği yapmak için bu URL 'YI girin:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?subscription-key={subscription-key}&api-version=1.0&layer=basic&style=main&zoom=12&center=-73.96682739257812%2C40.78119135317995&pins=default|la-35+50|ls12|lc003C62|co9B2F15||'Times Square'-73.98516297340393 40.758781646381024|'Central Park'-73.96682739257812 40.78119135317995&path=lc0000FF|fc0000FF|lw3|la0.80|fa0.30||udid-{udId}
    ```

    İşte yanıt görüntüsü:

    ![Azure haritalar veri depolamadan veri al](./media/how-to-render-custom-data/uploaded-path.png)

## <a name="render-a-polygon-with-color-and-opacity"></a>Renk ve opaklık ile Çokgen işleme

> [!Note]
> Bu bölümdeki yordam, fiyatlandırma katmanı S1 ' te bir Azure Maps hesabı gerektirir.


[Yol parametresiyle](/rest/api/maps/render/getmapimage#uri-parameters)stil değiştiricilerini kullanarak bir çokgenin görünümünü değiştirebilirsiniz.

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


PIN 'lerin görünümünü stil değiştiricileri ekleyerek değiştirebilirsiniz. Örneğin, raptik ve etiketlerini daha büyük veya küçük hale getirmek için `sc` "stil Ölçeği" değiştiricisini kullanın. Bu değiştirici sıfırdan büyük bir değer alır. 1 değeri standart ölçeğe sahiptir. 1 ' den büyük değerler, PIN 'leri daha büyük yapar ve 1 ' den küçük değerler bunları daha küçük hale getirir. Stil değiştiriciler hakkında daha fazla bilgi için bkz. [statik görüntü hizmeti yol parametreleri](/rest/api/maps/render/getmapimage#uri-parameters).


Özel etiketlerle bir daire ve Pushpin işlemek için aşağıdaki adımları izleyin:

1. Postman uygulamasında daha önce oluşturduğunuz koleksiyonda yeni bir sekme açın. Oluşturucu sekmesinde HTTP Al metodunu seçin ve bir GET isteği yapmak için bu URL 'YI girin:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co002D62||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    İşte yanıt görüntüsü:

    ![Özel raptiyeler ile bir daire işleme](./media/how-to-render-custom-data/circle-custom-pins.png)

2. Son adımdaki Pushpin rengini değiştirmek için "Co" stil değiştiricisini değiştirin. ' A bakın `pins=default|la15+50|al0.66|lc003C62|co002D62|` , geçerli renk CSS 'de #002D62 olarak belirtilir. #41d42a için değiştirmek istediğinizi varsayalım. "Ortak" belirticisinden sonra yeni renk değerini şöyle yazın: `pins=default|la15+50|al0.66|lc003C62|co41D42A|` . Yeni bir GET isteği oluşturun:

    ```HTTP
    https://atlas.microsoft.com/map/static/png?api-version=1.0&style=main&layer=basic&zoom=14&height=700&Width=700&center=-122.13230609893799,47.64599069048016&path=lcFF0000|lw2|la0.60|ra1000||-122.13230609893799 47.64599069048016&pins=default|la15+50|al0.66|lc003C62|co41D42A||'Microsoft Corporate Headquarters'-122.14131832122801  47.64690503939462|'Microsoft Visitor Center'-122.136828 47.642224|'Microsoft Conference Center'-122.12552547454833 47.642940335653996|'Microsoft The Commons'-122.13687658309935  47.64452336193245&subscription-key={subscription-key}
    ```

    PIN renklerinin renkleri değiştirildikten sonra yanıt görüntüsü aşağıda verilmiştir:

    ![Güncelleştirilmiş raptiyeler ile bir daire işleme](./media/how-to-render-custom-data/circle-updated-pins.png)

Benzer şekilde, diğer stil değiştiricilerini değiştirebilir, ekleyebilir ve kaldırabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar


* [Azure haritalar harita görüntüsünü Al API](/rest/api/maps/render/getmapimage) belgelerini inceleyin.
* Azure haritalar veri hizmeti (Önizleme) hakkında daha fazla bilgi için bkz. [hizmet belgeleri](/rest/api/maps/data).