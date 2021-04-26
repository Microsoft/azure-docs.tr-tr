---
title: Open Geospatial Consortium (OGC) eşleme katmanı ekleme | Microsoft Azure haritaları
description: Haritadaki bir OGC Haritası katmanını nasıl kaplayacağınızı ve Logcmaplayer sınıfında farklı seçeneklerin nasıl kullanılacağını öğrenin.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7d94a681fa987a4b23dbcda744c2f8516da4437a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92891674"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>Open Geospatial Consortium eşleme katmanını ekleme (OGC)

`atlas.layer.OgcMapLayer`Sınıfı, haritadaki Web harita Hizmetleri (WMS) Imagery ve Web harita kutucuk Hizmetleri (WMTS) Imagery ile yer alabilir. WMS, coğrafi olarak başvurulan harita görüntülerini Internet üzerinden sunmak için OGC tarafından geliştirilen standart bir protokoldür. Görüntü coğrafi başvuru, bir görüntüyü coğrafi bir konuma ilişkilendirirken yapılan işlemlerdir. WMTS Ayrıca OGC tarafından geliştirilen standart bir protokoldür. Önceden işlenmiş ve coğrafi olarak başvurulan harita kutucukları sunmak için tasarlanmıştır.

Aşağıdaki bölümlerde, sınıfı tarafından desteklenen Web harita hizmeti özellikleri ana hatlarıyla verilmiştir `OgcMapLayer` .

**Web harita hizmeti (WMS)**

- Desteklenen sürümler: `1.0.0` , `1.1.0` , `1.1.1` ve `1.3.0`
- Hizmet, `EPSG:3857` projeksiyon sistemini desteklemelidir veya yeniden projeksiyonları idare etmelidir.
- Getfeatureınfo, hizmetin `EPSG:4326` yeniden tahminleri desteklemesini veya işlemesini gerektirir. 
- Desteklenen işlemler:

    | İşlem | Description |
    | :-- | :-- |
    | GetCapabilities | Desteklenen yeteneklere sahip hizmet hakkındaki meta verileri alır |
    | GetMap | Belirtilen bölge için bir harita görüntüsü alır |
    | Getfeatureınfo | `feature_info`Özelliği ile ilgili temel verileri içeren alır. |

**Web Haritası kutucuk hizmeti (WMTS)**

- Desteklenen sürümler: `1.0.0`
- Kutucuklar kare olmalıdır, örneğin `TileWidth == TileHeight` .
- Desteklenen CCR 'ler: `EPSG:3857` veya `GoogleMapsCompatible` 
- TileMatrix tanımlayıcısı, eşlemedeki yakınlaştırma düzeyine karşılık gelen bir tamsayı değeri olmalıdır. Azure haritasında yakınlaştırma düzeyi ve arasında bir değerdir `"0"` `"22"` . Bu nedenle `"0"` desteklenir, ancak `"00"` desteklenmez.
- Desteklenen işlemler:

    | İşlem | Description |
    | :-- | :-- |
    | GetCapabilities | Desteklenen işlemleri ve özellikleri alır |
    | GetTile | Belirli bir kutucuk için canlandırın alır |

## <a name="overlay-an-ogc-map-layer"></a>OGC eşleme katmanını kaplama

Hizmetin `url` temel URL 'si veya hizmetin yeteneklerini alma sorgusuna sahip tam URL olabilir. Belirtilen ayrıntılara bağlı olarak, WFS istemcisi hizmetin ilk olarak nasıl erişebileceğini belirleyebilmek için birkaç standart URL biçimi deneyebilir.

Aşağıdaki kod, haritada bir OGC Haritası katmanının nasıl kaplamasıyla ilgili gösterilmektedir.

<br/>

<iframe height='700' scrolling='no' title='OGC Map katman örneği' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen 'da Azure Maps () ile kalem <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>ogc Haritası katman örneğine</a> bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="ogc-map-layer-options"></a>OGC eşleme katmanı seçenekleri

Aşağıdaki örnek farklı OGC Haritası katman seçeneklerini gösterir. Kod kalemini düzenlemek için sağ üst köşedeki kod kalemi düğmesine tıklayabilirsiniz.

<br/>

<iframe height='700' scrolling='no' title='OGC eşleme katmanı seçenekleri' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen 'da Azure Maps () tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>ogc Haritası katman seçeneklerine</a> bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="ogc-web-map-service-explorer"></a>OGC Web harita hizmet Gezgini

Aşağıdaki araç, Web harita Hizmetleri (WMS) ve Web harita kutucuk Hizmetleri 'nden (WMTS) katman olarak görüntü kaplayan görünümleri. Hizmet içindeki hangi katmanların haritada işleneceğini seçebilirsiniz. Ayrıca, bu katmanların ilişkili göstergelerini de görüntüleyebilirsiniz.

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='OGC Web harita hizmet Gezgini' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Codepen üzerinde Azure Maps () tarafından bulunan kalem <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>ogc Web Haritası hizmet Gezgini</a> ' ne bakın <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

Ayrıca, bir proxy hizmeti kullanmak için eşleme ayarlarını belirtebilirsiniz. Proxy hizmeti, CORS 'nin etkinleştirildiği etki alanlarında barındırılan kaynakları yüklemenize olanak sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan sınıflar ve yöntemler hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [OgcMapLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcMapLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

Haritalarınıza ekleyebileceğiniz kod örneklerini içeren aşağıdaki makalelere bakın:

> [!div class="nextstepaction"]
> [WFS hizmetine bağlanma](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Çekirdek işlemlerden yararlanma](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Desteklenen veri biçimi ayrıntıları](spatial-io-supported-data-format-details.md)