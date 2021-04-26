---
title: 'Öğretici: Microsoft Azure haritaları Oluşturucu (Önizleme) kullanarak ınkapı haritaları oluşturma'
description: Microsoft Azure haritaları Oluşturucu (Önizleme) kullanarak ınkapıharitaları oluşturma hakkında öğretici
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 9ac53dab29feddd36a95b8b2b041caaf5c3598d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101720146"
---
# <a name="tutorial-use-creator-preview-to-create-indoor-maps"></a>Öğretici: iç kapak eşlemeleri oluşturmak için Oluşturucu (Önizleme) kullanın

> [!IMPORTANT]
> Azure haritalar Creator Hizmetleri şu anda genel önizlemededir.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Bu öğreticide, ınkapı haritaları oluşturma gösterilmektedir. Bu öğreticide API 'yi kullanarak şunları yapmayı öğreneceksiniz:

> [!div class="checklist"]
> * Inkapımap çizim paketinizi karşıya yükleyin
> * Çizim paketinizi eşleme verilerine dönüştürme
> * Harita verilerinizde veri kümesi oluşturma
> * Veri kümenizdeki verilerden bir tileset oluşturma
> * Harita özelliklerinizin hakkında bilgi edinmek için Azure Maps web özellik hizmeti (WFS) API 'sini sorgulama
> * Harita özelliklerinizi ve veri kümenizdeki verileri kullanarak bir özellik stateset oluşturma
> * Özellik stateset 'nizi güncelleştirme

## <a name="prerequisites"></a>Önkoşullar

Inkapısı haritaları oluşturmak için:

1. [Azure haritalar hesabı oluşturma](quick-demo-map-app.md#create-an-azure-maps-account)
2. Birincil anahtar veya abonelik anahtarı olarak da bilinen [birincil bir abonelik anahtarı alın](quick-demo-map-app.md#get-the-primary-key-for-your-account).
3. [Oluşturucu (Önizleme) kaynağı oluşturma](how-to-manage-creator.md)
4. [Örnek çizim paketini](https://github.com/Azure-Samples/am-creator-indoor-data-examples/blob/master/Sample%20-%20Contoso%20Drawing%20Package.zip)indirin.

Bu öğretici [Postman](https://www.postman.com/) uygulamasını kullanır, ancak farklı bir API geliştirme ortamı seçebilirsiniz.

>[!IMPORTANT]
> Bu belgedeki API URL 'lerinin, Oluşturucu kaynağınızın konumuna göre ayarlanması gerekebilir. Daha fazla ayrıntı için bkz. [creator hizmetlerine erişim](how-to-manage-creator.md#access-to-creator-services).

## <a name="upload-a-drawing-package"></a>Bir çizim paketini karşıya yükle

Azure Maps kaynaklarına çizim paketini yüklemek için [veri yükleme API](/rest/api/maps/data/uploadpreview) 'sini kullanın.

Karşıya veri yükleme API 'SI, burada tanımlanan kalıbı uygulayan uzun süredir çalışan bir işlemdir. İşlem tamamlandıktan sonra, öğesini `udid` dönüştürmek için karşıya yüklenen pakete erişmek üzere öğesini kullanacağız. Edinmek için aşağıdaki adımları izleyin `udid` .

1. Postman uygulamasını açın. Postman uygulamasının üst kısmında **Yeni**' yi seçin. **Yeni oluştur** penceresinde **koleksiyon**' ı seçin.  Koleksiyonu adlandırın ve **Oluştur** düğmesini seçin.

2. İsteği oluşturmak için **Yeni** ' yi seçin. **Yeni oluştur** penceresinde **istek**' ı seçin. İstek için bir **istek adı** girin. Önceki adımda oluşturduğunuz koleksiyonu seçin ve ardından **Kaydet**' i seçin.

3. Oluşturucu sekmesinde http **Post** yöntemini seçin ve çizim paketini Azure Maps hizmetine yüklemek için aşağıdaki URL 'yi girin. Bu istek ve bu makalede bahsedilen diğer istekler için, `{Azure-Maps-Primary-Subscription-key}` birincil abonelik anahtarınızla değiştirin.

    ```http
    https://atlas.microsoft.com/mapData/upload?api-version=1.0&dataFormat=zip&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. **Üst bilgiler** sekmesinde, anahtar için bir değer belirtin `Content-Type` . Çizim paketi daraltılmış bir klasördür, bu nedenle `application/octet-stream` değerini kullanın. **Gövde** sekmesinde **ikili**' ı seçin. **Dosya Seç** ' e tıklayın ve bir çizim paketi seçin.

     ![veri yönetimi](./media/tutorial-creator-indoor-maps/enter-content-type-dialog.png)

5. Mavi **Gönder** düğmesine tıklayın ve isteğin işlemesini bekleyin. İstek tamamlandıktan sonra yanıtın **üstbilgiler** sekmesine gidin. Olan **konum** anahtarının değerini kopyalayın `status URL` .

6. API çağrısının durumunu denetlemek için üzerinde bir http isteği **alın** `status URL` . Kimlik doğrulaması için birincil abonelik anahtarınızı URL 'ye eklemeniz gerekir. **Get** ISTEğI aşağıdaki URL gibi görünmelidir:

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

7. HTTP isteği **Al** işlemi başarıyla tamamlandığında, döndürür `resourceLocation` . , `resourceLocation` `udid` Karşıya yüklenen içerik için benzersiz bir içerir. İsteğe bağlı olarak, bir `resourceLocation` sonraki adımda bu kaynaktan meta verileri almak için URL 'yi kullanabilirsiniz.

    ```json
    {
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
    }
    ```

8. İçerik meta verilerini almak için,  `resourceLocation` adım 7 ' de alınan URL 'de http isteği Al ' ı oluşturun. Kimlik doğrulaması için birincil abonelik anahtarınızı URL 'ye eklemediğinizden emin olun. **Get** isteği aşağıdaki URL 'yi beğenmelidir:

    ```http
   https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

9. HTTP isteği **Al** işlemi başarıyla tamamlandığında, yanıt gövdesi `udid` `resourceLocation` adım 7 ' de belirtilen içeriği, gelecekteki içeriğe erişmek/indirmek için konumunu ve oluşturulma/güncelleme tarihi, boyutu vb. gibi içerikler hakkında bazı diğer meta verileri içerir. Genel yanıta bir örnek:

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "2020-02-03T02:32:25.0509366+00:00",
        "updated": "2020-02-11T06:12:13.0309351+00:00",
        "sizeInBytes": 766,
        "uploadStatus": "Completed"
    }
    ```

## <a name="convert-a-drawing-package"></a>Bir çizim paketini dönüştürme

 Artık çizim paketi karşıya yüklendikten sonra, `udid` paketi harita verilerine dönüştürmek için karşıya yüklenen paket için kullanacağız. Dönüştürme API 'SI, [burada](creator-long-running-operation.md)tanımlanan kalıbı uygulayan uzun süre çalışan bir işlem kullanır. İşlem tamamlandıktan sonra, ' yi kullanarak `conversionId` Dönüştürülen verilere erişin. Edinmek için aşağıdaki adımları izleyin `conversionId` .

1. **Yeni**'yi seçin. **Yeni oluştur** penceresinde **istek**' ı seçin. Bir **istek adı** girin ve bir koleksiyon seçin. **Kaydet**’e tıklayın.

2. Oluşturucu sekmesinde http **Post** yöntemini seçin ve karşıya yüklenen çizim paketinizi harita verilerine dönüştürmek için aşağıdaki URL 'yi girin. `udid`Karşıya yüklenen paket için öğesini kullanın.

    ```http
    https://atlas.microsoft.com/conversion/convert?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&udid={udid}&inputType=DWG
    ```

    >[!IMPORTANT]
    > Bu belgedeki API URL 'lerinin, Oluşturucu kaynağınızın konumuna göre ayarlanması gerekebilir. Daha fazla ayrıntı için bkz. [creator hizmetlerine erişim (Önizleme) ](how-to-manage-creator.md#access-to-creator-services).
    > Kodla ilgili bir hata alırsanız `"RequiresCreatorResource"` , Azure Maps hesabınızda [bir Azure Maps Creator kaynağı](how-to-manage-creator.md) sağladığınızdan emin olun.

3. **Gönder** düğmesine tıklayın ve isteğin işlemesini bekleyin. İstek tamamlandıktan sonra yanıtın **üstbilgiler** sekmesine gidin ve **konum** anahtarını bulun. Dönüştürme isteği için olan **konum** anahtarının değerini kopyalayın `status URL` . Bunu bir sonraki adımda kullanacaksınız.

    :::image type="content" source="./media/tutorial-creator-indoor-maps/copy-location-uri-dialog.png" border="true" alt-text="Konum anahtarının değerini kopyalayın":::

4. Oluşturucu sekmesinde yeni bir **Get** http yöntemi başlatın. Azure Maps birincil abonelik anahtarınızı öğesine ekleyin `status URL` .  `status URL` Adım 3 ' te kopyaladığınız bir get isteği yapın. `status URL`AŞAĞıDAKI URL gibi görünür:

    ```http
    https://atlas.microsoft.com/conversion/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

    Dönüştürme işlemi henüz tamamlanmadıysa, aşağıdaki JSON yanıtına benzer bir şey görebilirsiniz:

    ```json
    {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Running"
    }
    ```

5. İstek başarıyla tamamlandıktan sonra yanıt gövdesinde bir başarı durumu iletisi görürsünüz.  `conversionId` `resourceLocation` Dönüştürülen paketin URL 'sinden öğesini kopyalayın. , `conversionId` Dönüştürülmüş harita verilerine erişmek için DIĞER API tarafından kullanılır.

    ```json
   {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:39:54.9518496+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
        "properties": {}
    }
    ```

>[!NOTE]
>Postman uygulaması HTTP uzun süren Istekleri yerel olarak desteklemez. Sonuç olarak, durum URL 'sinde bir **Get** isteği yaparken uzun bir gecikme fark edebilirsiniz.  Otuz saniye bekleyip yanıt başarılı veya başarısız olana kadar **Gönder** düğmesine yeniden tıklamasına çalışın.

Örnek çizim paketinin hata veya uyarı olmadan dönüştürülmesi gerekir. Bununla birlikte, kendi çizim paketinizden hata veya uyarı alırsanız, JSON yanıtı [Çizim hatası görselleştiricisine](drawing-error-visualizer.md)bir bağlantı verecektir. Çizim hatası görselleştiricisi, hataların ve uyarıların ayrıntılarını incelemenizi sağlar. Dönüştürme hatalarının ve uyarılarının nasıl çözümleneceği hakkında öneriler almak için, bkz. [çizim dönüştürme hataları ve uyarıları](drawing-conversion-error-codes.md).

```json
{
    "operationId": "<operationId>",
    "created": "2020-04-22T19:39:54.9518496+00:00",
    "status": "Failed",
    "resourceLocation": "https://atlas.microsoft.com/conversion/{conversionId}?api-version=1.0",
    "properties": {
        "diagnosticPackageLocation": "https://atlas.microsoft.com/mapData/ce61c3c1-faa8-75b7-349f-d863f6523748?api-version=1.0"
    }
}
```

## <a name="create-a-dataset"></a>Veri kümesi oluşturma

Veri kümesi, binalar, düzeyler ve odalar gibi eşleme özelliklerinin bir koleksiyonudur. Bir veri kümesi oluşturmak için [veri kümesi oluşturma API](/rest/api/maps/dataset/createpreview)'sini kullanın. Veri kümesi oluşturma API 'SI, `conversionId` dönüştürülmüş çizim paketini alır ve `datasetId` oluşturulan veri kümesinin bir öğesini döndürür. Aşağıdaki adımlarda, bir veri kümesinin nasıl oluşturulacağı gösterilmektedir.

1. Postman uygulamasında **Yeni**' yi seçin. **Yeni oluştur** penceresinde **istek**' ı seçin. Bir **istek adı** girin ve bir koleksiyon seçin. **Kaydet**’e tıklayın

2. Yeni bir veri kümesi oluşturmak için [veri kümesi oluşturma API](/rest/api/maps/dataset/createpreview) 'Sine bir **Post** isteği yapın. İsteği göndermeden önce, `conversionId` `conversionId` 5. adımdaki dönüştürme işlemi sırasında hem abonelik anahtarınızı hem de ile elde edilen öğesini ekleyin.  İstek aşağıdaki URL gibi görünmelidir:

    ```http
    https://atlas.microsoft.com/dataset/create?api-version=1.0&conversionID={conversionId}&type=facility&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. `statusURL`Yanıt **üst bilgilerinin** **konum** anahtarına alın.

4. Elde etmek için üzerinde bir **Get** isteği yapın `statusURL` `datasetId` . Kimlik doğrulaması için Azure Maps birincil abonelik anahtarınızı ekleyin. İstek aşağıdaki URL gibi görünmelidir:

    ```http
    https://atlas.microsoft.com/dataset/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

5. HTTP isteği **Al** işlemi başarıyla tamamlandığında, yanıt üst bilgisi `datasetId` oluşturulan veri kümesi için öğesini içerir. Öğesini kopyalayın `datasetId` . `datasetId`Bir tileset oluşturmak için öğesini kullanmanız gerekir.

    ```json
    {
        "operationId": "<operationId>",
        "created": "2020-04-22T19:52:38.9352189+00:00",
        "status": "Succeeded",
        "resourceLocation": "https://azure.microsoft.com/dataset/{datasetiId}?api-version=1.0"
     }
    ```

## <a name="create-a-tileset"></a>Bir tileset oluşturma

Bir tileset, haritada işlenen vektör kutucukları kümesidir. Tilesets 'ler var olan veri kümelerinden oluşturulur. Ancak, bir tileset, kaynağı oluşturulan veri kümesinden bağımsızdır. Veri kümesi silinirse, tileset mevcut olmaya devam edecektir. Bir tileset oluşturmak için aşağıdaki adımları izleyin:

1. Postman uygulamasında **Yeni**' yi seçin. **Yeni oluştur** penceresinde **istek**' ı seçin. Bir **istek adı** girin ve bir koleksiyon seçin. **Kaydet**’e tıklayın

2. Oluşturucu sekmesinde bir **Post** isteği yapın. İstek URL 'si aşağıdaki URL gibi görünmelidir:

    ```http
    https://atlas.microsoft.com/tileset/create/vector?api-version=1.0&datasetID={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Tileset için üzerinde bir **Get** isteği yapın `statusURL` . Kimlik doğrulaması için Azure Maps birincil abonelik anahtarınızı ekleyin. İstek aşağıdaki URL gibi görünmelidir:

   ```http
    https://atlas.microsoft.com/tileset/operations/<operationId>?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. HTTP isteği **Al** işlemi başarıyla tamamlandığında, yanıt üst bilgisi `tilesetId` oluşturulan tileset için öğesini içerir. Öğesini kopyalayın `tilesetId` .

    ```json
    {
        "operationId": "<operationId>",
        "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
        "status": "Succeeded",
        "resourceLocation": "https://atlas.microsoft.com/tileset/{tilesetId}?api-version=1.0"
    }
    ```

## <a name="query-datasets-with-wfs-api"></a>WFS API ile veri kümelerini sorgulama

 Veri kümeleri,  [WFS API](/rest/api/maps/wfs)kullanılarak sorgulanabilir. WFS API ile özellik koleksiyonları, belirli bir koleksiyon veya özellik **kimliği** olan belirli bir özellik için sorgulama yapabilirsiniz. Özellik **kimliği** , özelliği veri kümesi içinde benzersiz şekilde tanımlar. Bu, örneğin, belirli bir stateset içinde hangi özellik durumunun güncelleştirileceğini belirlemek için kullanılır.

1. Postman uygulamasında **Yeni**' yi seçin. **Yeni oluştur** penceresinde **istek**' ı seçin. Bir **istek adı** girin ve bir koleksiyon seçin. **Kaydet**’e tıklayın

2. Veri kümenizdeki koleksiyonların listesini görüntülemek için bir **Get** isteği yapın. `<dataset-id>`İle değiştirin `datasetId` . Yer tutucu yerine Azure Maps birincil anahtarınızı kullanın. İstek aşağıdaki URL gibi görünmelidir:

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

3. Yanıt gövdesi GeoJSON biçiminde teslim edilir ve veri kümesindeki tüm koleksiyonları içerecektir. Kolaylık sağlaması için buradaki örnek yalnızca `unit` koleksiyonu gösterir. Tüm koleksiyonları içeren bir örneği görmek için bkz. [WFS, koleksiyonları API 'Si açıklaması](/rest/api/maps/wfs/collectiondescriptionpreview). Herhangi bir koleksiyon hakkında daha fazla bilgi edinmek için öğesi içindeki URL 'Lerden birine tıklayabilirsiniz `link` .

    ```json
    {
    "collections": [
        {
            "name": "unit",
            "description": "A physical and non-overlapping area which might be occupied and traversed by a navigating agent. Can be a hallway, a room, a courtyard, etc. It is surrounded by physical obstruction (wall), unless the is_open_area attribute is equal to true, and one must add openings where the obstruction shouldn't be there. If is_open_area attribute is equal to true, all the sides are assumed open to the surroundings and walls are to be added where needed. Walls for open areas are represented as a line_element or area_element with is_obstruction equal to true.",
            "links": [
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/definition?api-version=1.0",
                    "rel": "describedBy",
                    "title": "Metadata catalogue for unit"
                },
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?api-version=1.0",
                    "rel": "data",
                    "title": "unit"
                }
                {
                    "href": "https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit?api-version=1.0",
                    "rel": "self",
                    "title": "Metadata catalogue for unit"
                }
            ]
        },
    ```

4. Özellik koleksiyonları için bir **Get** isteği oluşturun `unit` .  `{datasetId}`İle değiştirin `datasetId` . Yer tutucu yerine Azure Maps birincil anahtarınızı kullanın. Yanıt gövdesi, koleksiyonun tüm özelliklerini içerir `unit` . İstek aşağıdaki URL gibi görünmelidir:

    ```http
    https://atlas.microsoft.com/wfs/datasets/{datasetId}/collections/unit/items?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0
    ```

5. `id`Dinamik olarak değiştirilebilen stil özelliklerine sahip bir birim özelliğinin özelliğini kopyalayın.  Birim doluluk durumu ve sıcaklık dinamik olarak güncelleştirilemediğinden, bu özelliği `id` sonraki bölümde kullanacağız. Aşağıdaki örnekte, özelliği `id` "UNIT26" dir. Bu özelliğin stil özelliklerine durum olarak başvuracağız ve bu özelliği bir stateset oluşturmak için kullanacağız.

     ```json
    {
        "type": "FeatureCollection",
        "features": [
            {
                "type": "Feature",
                "geometry": {
                    "type": "Polygon",
                    "coordinates": ["..."]
                },
                "properties": {
                    "original_id": "b7410920-8cb0-490b-ab23-b489fd35aed0",
                    "category_id": "CTG8",
                    "is_open_area": true,
                    "navigable_by": [
                        "pedestrian"
                    ],
                    "route_through_behavior": "allowed",
                    "level_id": "LVL14",
                    "occupants": [],
                    "address_id": "DIR1",
                    "name": "157"
                },
                "id": "UNIT26",
                "featureType": ""
            }, {"..."}
        ]
    }
    ```

## <a name="create-a-feature-stateset"></a>Özellik stateset oluşturma

1. Postman uygulamasında **Yeni**' yi seçin. **Yeni oluştur** penceresinde **istek**' ı seçin. Bir **istek adı** girin ve bir koleksiyon seçin. **Kaydet**’e tıklayın

2. [Create Stateset API](/rest/api/maps/featurestate/createstatesetpreview)'Sine bir **Post** isteği yapın. `datasetId`Değiştirmek istediğiniz durumu içeren veri kümesini kullanın. İstek aşağıdaki URL gibi görünmelidir:

    ```http
    https://atlas.microsoft.com/featureState/stateset?api-version=1.0&datasetId={datasetId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. **Post** Isteğinin **üst bilgilerinde** , `Content-Type` olarak ayarlanır `application/json` . **Gövdesinde**, ve durumlarındaki değişiklikleri yansıtmak için aşağıdaki ham JSON stillerini sağlayın `occupied` `temperature` . İşiniz bittiğinde **Gönder**' e tıklayın.

    ```json
    {
       "styles":[
          {
             "keyname":"occupied",
             "type":"boolean",
             "rules":[
                {
                   "true":"#FF0000",
                   "false":"#00FF00"
                }
             ]
          },
          {
             "keyname":"temperature",
             "type":"number",
             "rules":[
                {
                   "range":{
                      "exclusiveMaximum":66
                   },
                   "color":"#00204e"
                },
                {
                   "range":{
                      "minimum":66,
                      "exclusiveMaximum":70
                   },
                   "color":"#0278da"
                },
                {
                   "range":{
                      "minimum":70,
                      "exclusiveMaximum":74
                   },
                   "color":"#187d1d"
                },
                {
                   "range":{
                      "minimum":74,
                      "exclusiveMaximum":78
                   },
                   "color":"#fef200"
                },
                {
                   "range":{
                      "minimum":78,
                      "exclusiveMaximum":82
                   },
                   "color":"#fe8c01"
                },
                {
                   "range":{
                      "minimum":82
                   },
                   "color":"#e71123"
                }
             ]
          }
       ]
    }
    ```

4. `statesetId`Yanıt gövdesinden kopyalayın.

5. Durumu güncelleştirmek için bir **Post** isteği oluşturun: Statesetıd ve özelliğini `ID` Azure Maps abonelik anahtarınızla geçirin. İstek aşağıdaki URL gibi görünmelidir:

    ```http
    https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetId}&featureID={featureId}&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

6. **Post** Isteğinin **üst bilgilerinde** , `Content-Type` olarak ayarlanır `application/json` . **Post** isteğinin **gövdesinde** , JSON öğesini kopyalayıp aşağıdaki örneğe yapıştırın.

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

    >[!NOTE]
    > Güncelleştirme yalnızca, postalanan zaman damgası önceki isteğin zaman damgasından sonra olduğunda kaydedilir. Oluşturma sırasında daha önce yapılandırdığımız herhangi bir KeyName geçirebiliriz.

7. Başarılı bir güncelleştirmeden sonra bir `200 OK` http durum kodu alırsınız. Bir ınkapımap için  [dinamik stil uygulanmışsa](indoor-map-dynamic-styling.md) , güncelleştirme işlenen haritasında belirtilen zaman damgasında görüntülenir.

[Özellik alma API 'si](/rest/api/maps/featurestate/getstatespreview) , özelliğini kullanarak bir özelliğin durumunu almanıza olanak sağlar `ID` . Ayrıca, [durum SILME API](/rest/api/maps/featurestate/deletestatesetpreview)'sini kullanarak stateset ve kaynaklarını silebilirsiniz.

Bu makalede ele alınan farklı Azure Maps Creator Hizmetleri (Önizleme) hakkında daha fazla bilgi edinmek için bkz. [creator ınkapıharitaları](creator-indoor-maps.md).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Temizleme gerektiren kaynak yok.

## <a name="next-steps"></a>Sonraki adımlar

Inkapısı haritaları modülünü nasıl kullanacağınızı öğrenmek için bkz.

> [!div class="nextstepaction"]
> [Inkapıharitaları modülünü kullanma](how-to-use-indoor-module.md)