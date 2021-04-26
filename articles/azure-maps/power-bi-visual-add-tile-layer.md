---
title: Görsel Power BI Azure Maps 'a kutucuk katmanı ekleme | Microsoft Azure haritaları
description: Bu makalede, Power BI için Microsoft Azure haritaları görselindeki döşeme katmanını nasıl kullanacağınızı öğreneceksiniz.
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6ab99f32932f39d5ad140b7a16d16ceae30fff54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92896217"
---
# <a name="add-a-tile-layer"></a>Kutucuk katmanı ekleme

Başvuru katmanı özelliği gibi döşeme katmanı özelliği, daha fazla bağlam sağlamak üzere haritada yer almasına izin verir. Döşeme katmanları, Azure Maps temel harita kutucuklarının üzerine görüntü eklemenize olanak tanır. Bu, Drones veya milyonlarca veri satırı gibi büyük veya karmaşık veri kümelerini kaplayan harika bir yoldur.

> [!div class="mx-imgBorder"]
> ![Azure haritalar 'dan geçerli kızılötesi hava durumu verilerini gösteren bir kutucuk katmanının üzerinde kabarcık katmanı görüntüleyen bir harita](media/power-bi-visual/radar-tile-layer-with-bubbles.png)

Döşeme katmanı bir sunucudan kutucukları yükler. Bu görüntüler önceden işlenmiş ya da dinamik olarak işlenmiş olabilir. Önceden işlenmiş görüntüler, kutucuk katmanının anladığı bir adlandırma kuralını kullanarak bir sunucu üzerindeki diğer herhangi bir görüntü gibi saklanır. Dinamik olarak işlenen görüntüler görüntüleri gerçek zamanlı olarak yakın bir şekilde yüklemek için bir hizmet kullanır. Döşeme katmanları, haritada büyük veri kümelerini görselleştirmenin harika bir yoludur. Bir görüntüden yalnızca kutucuk katmanı oluşturulmayabilir, vektör verileri de kutucuk katmanı olarak da oluşturulabilir.

Bir kutucuk hizmetinin kullanılabildiği sınırlama kutusu ve yakınlaştırma aralığı, kutucukların istendiği yerleri sınırlamak için ayarlar olarak geçirilebilir. Bu, hem görsel hem de kutucuk hizmeti için performans geliştirmedir. Aşağıda, **döşeme katmanı** bölümünde bulunan **Biçim** bölmesinde bulunan tüm ayarlara genel bir bakış verilmiştir.

| Ayar        | Açıklama   |
|----------------|---------------|
| Url            | Bir kutucuk hizmetine işaret eden biçimli URL.  |
| Kutucuk boyutu      | Kutucukların genişlik ve yükseklik boyutlarını belirten bir tamsayı değeri.   |
| Kuzey bağı    | Her birinin kullanılabilir olduğu sınırlayıcı kutunun kuzey enlem. |
| Güney ile bağlantılı    | Kutucukların kullanılabildiği sınırlayıcı kutunun güney enlem. |
| Doğu sınırlı     | Kutucukların kullanılabildiği sınırlayıcı kutunun Doğu Boylam.  |
| Batı bağlantılı     | Kutucukların kullanılabildiği sınırlayıcı kutunun Batı boylam.   |
| Şeffaflık   | Döşeme katmanının saydamlığı.   |
| TMS         | Döşeme katmanı, kutucuk katmanının Y koordinatı eksenini tersine çevirir. |
| En az yakınlaştırma       | En düşük yakınlaştırma düzeyi kutucukları var. |
| En fazla yakınlaştırma       | En yüksek yakınlaştırma düzeyi kutucukları var.  |
| Katman konumu | Katmanın diğer harita katmanlarına göre konumunu belirtir. |

## <a name="tile-url-formatting"></a>Kutucuk URL 'SI biçimlendirmesi

Azure haritalar görseli tarafından desteklenen üç farklı kutucuk hizmeti adlandırma kuralı vardır:

-   **X, y, zoom gösterimi** -x sütundur, Y, kutucuk kılavuzundaki döşemenin satır konumudur ve yakınlaştırma düzeyi yakınlaştırma düzeyine göre bir değer gösterimi.
-   **Quadkey gösterimi** -x, y ve zoom bilgilerini tek bir dize değeri olarak birleştirir. Bu dize değeri, tek bir kutucuk için benzersiz bir tanımlayıcı haline gelir.
-   **Sınırlayıcı kutusu** -sınırlayıcı kutu koordinatları biçiminde bir görüntü belirtin: `{west},{south},{east},{north}` . Bu biçim genellikle [Web eşleme Hizmetleri (WMS)](https://www.opengeospatial.org/standards/wms)tarafından kullanılır.

Kutucuk URL 'si, aşağıdaki parametreleri kullanan bir kutucuk URL şablonuna yönelik https URL 'sine sahiptir:

-   `{x}` -X kutucuğunun konumu. Ayrıca `{y}` , ve gerektirir `{z}` .
-   `{y}` -Kutucuğun Y konumu. Ayrıca `{x}` , ve gerektirir `{z}` .
-   `{z}` -Kutucuğun yakınlaştırma düzeyi. Ayrıca `{x}` , ve gerektirir `{y}` .
-   `{quadkey}` - `quadkey` Bing Haritalar kutucuk sistem adlandırma kuralına göre kutucuk tanımlayıcısı.
-   `{bbox-epsg-3857}` - `{west},{south},{east},{north}` EPSG 3857 uzamsal başvuru sisteminde biçim içeren bir sınırlayıcı kutu dizesi.

Örnek olarak, aşağıdakiler Azure haritalar 'daki [Hava durumu radar kutucuk hizmeti](/rest/api/maps/renderv2/getmaptilepreview) için biçimlendirilen bir kutucuk URL 'sidir. `[subscription-key]`Azure Maps abonelik anahtarınız için bir yer tutucu olduğunu unutmayın.

> `https://atlas.microsoft.com/map/tile?zoom={z}&x={x}&y={y}&tilesetId=microsoft.weather.radar.main&api-version=2.0&subscription-key=[subscription-key]`

Azure haritalar döşeme sistemi hakkında daha fazla bilgi için bkz. [yakınlaştırma düzeyleri ve kutucuk Kılavuzu](zoom-levels-and-tile-grid.md).

## <a name="next-steps"></a>Sonraki adımlar

Haritaya daha fazla bağlam ekleyin:

> [!div class="nextstepaction"]
> [Gerçek zamanlı trafiği gösterme](power-bi-visual-show-real-time-traffic.md)