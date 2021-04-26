---
title: Azure haritalar Oluşturucu (Önizleme) ıntıl haritaları için dinamik stil uygulama
description: Oluşturucu (Önizleme) ınkapılı haritalar için dinamik stil uygulamayı nasıl uygulayacağınızı öğrenin
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: a23c492d4a81703c0dc6612928a56b5b31d52cae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101726328"
---
# <a name="implement-dynamic-styling-for-creator-preview-indoor-maps"></a>Oluşturucu (Önizleme) ınkapılı haritalar için dinamik stil uygulama

> [!IMPORTANT]
> Azure haritalar Creator Hizmetleri şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure haritalar Creator [özelliği durum hizmeti](/rest/api/maps/featurestate) , ınkapımap veri özelliklerinin dinamik özelliklerine göre stiller uygulamanıza olanak tanır.  Örneğin, hizmet toplantısı odalarını, doluluk durumunu yansıtmak için belirli bir renkle işleyebilirsiniz. Bu makalede, iç hat eşleme özelliklerinin [özellik durum hizmeti](/rest/api/maps/featurestate) ve [ınkapılı web modülü](how-to-use-indoor-module.md)ile dinamik olarak nasıl işleneceğini göstereceğiz.

## <a name="prerequisites"></a>Önkoşullar

1. [Azure Haritalar hesabı oluşturma](quick-demo-map-app.md#create-an-azure-maps-account)
2. Birincil anahtar veya abonelik anahtarı olarak da bilinen [birincil bir abonelik anahtarı alın](quick-demo-map-app.md#get-the-primary-key-for-your-account).
3. [Oluşturucu (Önizleme) kaynağı oluşturma](how-to-manage-creator.md)
4. [Örnek çizim paketini](https://github.com/Azure-Samples/am-creator-indoor-data-examples)indirin.
5. Ve almak için bir [ınkapısı haritası oluşturun](tutorial-creator-indoor-maps.md) `tilesetId` `statesetId` .
6. [Inkapımap modülünü kullanma](how-to-use-indoor-module.md)bölümündeki adımları izleyerek bir Web uygulaması oluşturun.

Bu öğretici [Postman](https://www.postman.com/) uygulamasını kullanır, ancak farklı bir API geliştirme ortamı seçebilirsiniz.

## <a name="implement-dynamic-styling"></a>Dinamik stil uygulama

Önkoşulları tamamladıktan sonra, ve abonelik anahtarınızla yapılandırılmış basit bir Web uygulamasına sahip olmanız gerekir `tilesetId` `statesetId` .

### <a name="select-features"></a>Özellik seçme

Dinamik stil uygulamak için, bir toplantı veya konferans odası gibi bir özellik, özelliği tarafından başvurulmalıdır `id` . Özelliği, `id` Bu özelliğin dinamik özelliğini veya *durumunu* güncelleştirmek için kullanacaksınız. Bir veri kümesinde tanımlanan özellikleri görüntülemek için aşağıdaki yöntemlerden birini kullanabilirsiniz:

* WFS API 'SI (Web özelliği hizmeti). Veri kümeleri WFS API kullanılarak sorgulanabilir. WFS, Open Geospatial Consortium API özelliklerini izler. WFS API 'SI, bir veri kümesi içindeki özellikleri sorgulamak için yararlıdır. Örneğin, belirli bir tesis ve kat düzeyi için tüm orta ölçekli toplantı odalarını bulmak üzere WFS 'yi kullanabilirsiniz.

* Kullanıcının Web uygulamanızı kullanarak haritada Özellikler seçmesini sağlayan özelleştirilmiş kodu uygulayın. Bu makalede, bu seçeneği kullanacağız.  

Aşağıdaki betik, fare tıklaması olayını uygular. Kod, `id` tıklatıklanan noktaya göre özelliği alır. Uygulamanızdaki kodu, ınkapıyöneticisi kod blobunun altına ekleyebilirsiniz. Uygulamanızı çalıştırın ve tıklanan noktanın özelliğini edinmek için konsolu kontrol edin `id` .

```javascript
/* Upon a mouse click, log the feature properties to the browser's console. */
map.events.add("click", function(e){

    var features = map.layers.getRenderedShapes(e.position, "indoor");

    features.forEach(function (feature) {
        if (feature.layer.id == 'indoor_unit_office') {
            console.log(feature);
        }
    });
});
```

[Bir ınkapımap oluşturma](tutorial-creator-indoor-maps.md) öğreticisi, durum güncelleştirmelerini kabul etmek için stateset özelliğini yapılandırdı `occupancy` .

Sonraki bölümde, Office 'in doluluk *durumunu* olarak ayarlayacağız `UNIT26` `true` . Office `UNIT27` , olarak ayarlanacak `false` .

### <a name="set-occupancy-status"></a>Doluluk durumunu ayarla

 Şimdi iki ofisin durumunu güncelleştireceğiz `UNIT26` ve `UNIT27` :

1. Postman uygulamasında **Yeni**' yi seçin. **Yeni oluştur** penceresinde **istek**' ı seçin. Bir **istek adı** girin ve bir koleksiyon seçin. **Kaydet**’e tıklayın

2. Durumu güncelleştirmek için [özellik güncelleştirme DURUMLARı API](/rest/api/maps/featurestate/updatestatespreview) 'sini kullanın. Stateset KIMLIĞINI ve `UNIT26` iki birimden birini geçirin. Azure Maps abonelik anahtarınızı ekleyin. Durumu güncelleştirmek için bir **Post** isteğinin URL 'si aşağıda verilmiştir:

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID=UNIT26&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. **Post** Isteğinin **üst bilgilerinde** , `Content-Type` olarak ayarlanır `application/json` . **Post** isteğinin **gövdesinde** , özellik GÜNCELLEŞTIRMELERIYLE birlikte aşağıdaki ham JSON ' ı yazın. Güncelleştirme yalnızca, deftere nakledilen zaman damgası, aynı özellik için önceki özellik durumu güncelleştirme isteklerinde kullanılan zaman damgasından sonra kaydedilir `ID` . Değerini güncelleştirmek için "dolu" öğesini geçirin `keyName` .

    ```json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": true,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

4. Aşağıdaki JSON ile adım 2 ve 3 `UNIT27` ' ü yineleyin.

    ``` json
    {
        "states": [
            {
                "keyName": "occupied",
                "value": false,
                "eventTimestamp": "2019-11-14T17:10:20"
            }
        ]
    }
    ```

### <a name="visualize-dynamic-styles-on-a-map"></a>Haritada dinamik stilleri görselleştirme

Daha önce bir tarayıcıda açtığınız web uygulaması, artık eşleme özelliklerinin güncelleştirilmiş durumunu yansıtmalıdır. `UNIT27`(142) yeşil ve `UNIT26` (143) kırmızı görünmelidir.

![Yeşil ve meşgul odadaki boş oda kırmızı renkte](./media/indoor-map-dynamic-styling/room-state.png)

[Canlı tanıtımı gör](https://azuremapscodesamples.azurewebsites.net/?sample=Creator%20indoor%20maps)

## <a name="next-steps"></a>Sonraki adımlar

Okumaya göre daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Inkapıeşleme için Oluşturucu (Önizleme)](creator-indoor-maps.md)

Bu makalede bahsedilen API 'Lerin başvurularına bakın:

> [!div class="nextstepaction"]
> [Karşıya veri yükleme](creator-indoor-maps.md#upload-a-drawing-package)

> [!div class="nextstepaction"]
> [Veri dönüştürme](creator-indoor-maps.md#convert-a-drawing-package)

> [!div class="nextstepaction"]
> [Veri kümesi](creator-indoor-maps.md#datasets)

> [!div class="nextstepaction"]
> [Tileset](creator-indoor-maps.md#tilesets)

> [!div class="nextstepaction"]
> [Özellik durumu kümesi](creator-indoor-maps.md#feature-statesets)

> [!div class="nextstepaction"]
> [WFS hizmeti](creator-indoor-maps.md#web-feature-service-api)