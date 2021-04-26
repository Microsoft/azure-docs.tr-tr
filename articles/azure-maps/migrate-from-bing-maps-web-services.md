---
title: "Öğretici: Bing Haritalar 'dan Web hizmetlerini geçirme | Microsoft Azure haritaları"
description: Web hizmetlerini Bing Haritalar 'dan Microsoft Azure Maps 'a geçirmeye yönelik öğretici.
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6024aae68183fbe02125ef4207e9fbce8abd6a2b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97679079"
---
# <a name="tutorial-migrate-web-service-from-bing-maps"></a>Öğretici: Bing Haritalar 'dan Web hizmeti geçirme

Hem Azure hem de Bing Haritalar REST Web Hizmetleri aracılığıyla uzamsal API 'lere erişim sağlar. Bu platformların API arabirimleri benzer işlevler gerçekleştirir, ancak farklı adlandırma kuralları ve yanıt nesneleri kullanır. Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> * İleri ve ters coğrafi kodlama
> * İlgi noktası arama
> * Rotaları ve yönleri hesapla
> * Harita görüntüsünü al
> * Uzaklık matrisini hesaplama
> * Saat dilimi ayrıntılarını al

Aşağıdaki tabloda, listelenen Bing Haritalar hizmeti API 'Lerinde benzer işlevler sağlayan Azure Maps hizmeti API 'Leri verilmiştir.

| Bing Haritalar hizmet API 'SI                 | Azure haritalar hizmeti API 'SI      |
|---------------------------------------|-----------------------------|
| Otomatik öneri                           | [Ara](/rest/api/maps/search)     |
| Yönergeler (kamyon dahil)          | [Yol yönleri](/rest/api/maps/route/getroutedirections)                          |
| Uzaklık matrisi                       | [Yol matrisi](/rest/api/maps/route/postroutematrixpreview)                          |
| Imagery – statik eşleme                  | [İşleme](/rest/api/maps/render/getmapimage)                                   |
| İzokron                            | [Yol aralığı](/rest/api/maps/route/getrouterange)                                    |
| Yerel Öngörüler                        | [Arama](/rest/api/maps/search)  +  [Yol aralığı](/rest/api/maps/route/getrouterange)    |
| Yerel arama                          | [Ara](/rest/api/maps/search)     |
| Konum tanıma (POI)           | [Ara](/rest/api/maps/search)     |
| Konumlar (ileri/ters coğrafi kodlama) | [Ara](/rest/api/maps/search)                                               |
| Yola yasla                          | [Rota gönderme yönleri](/rest/api/maps/route/postroutedirections)                         |
| Uzamsal veri Hizmetleri (SDS)           | [Arama](/rest/api/maps/search)  +  [Route](/rest/api/maps/route) + diğer Azure hizmetleri |
| Saat Dilimi                             | [Saat dilimi](/rest/api/maps/timezone)  |
| Trafik olayları                     | [Trafik olayı ayrıntıları](/rest/api/maps/traffic/gettrafficincidentdetail)                     |
| Nedeniyle                             | [Yükseltme (Önizleme)](/rest/api/maps/elevation)

Aşağıdaki hizmet API 'Leri Şu anda Azure haritalar 'da kullanılabilir değil:

-   İyileştirilmiş gezi rotaları-planlanmış. Azure haritalar yönlendirme API 'SI, tek bir araç için seyahat satış ve iyileştirmesini destekler.
-   Imagery meta verileri – öncelikle Bing Haritalar 'da kutucuk URL 'Leri almak için kullanılır. Azure haritalar 'ın harita kutucuklarına doğrudan erişmek için tek başına bir hizmeti vardır.

Azure haritalar, ilgi çekici olabilecek birkaç ek REST Web hizmetine sahiptir;

-   [Azure haritalar Oluşturucu (Önizleme) ](./creator-indoor-maps.md) – binaların ve boşlukların özel bir özel dijital ikizi oluşturun.
-   [Uzamsal işlemler](/rest/api/maps/spatial) – bir hizmete bölge sınırlaması gibi karmaşık uzamsal hesaplamalar ve işlemler yük boşaltma.
-   Azure haritalarından tarama ve vektör kutucukları olarak erişim ve [görüntü döşeme kutucukları](/rest/api/maps/render/getmaptile) .
-   [Batch yönlendirme](/rest/api/maps/route/postroutedirectionsbatchpreview) – bir süre içinde tek bir toplu işte 1.000 adede kadar rota isteğine izin verir. Yollar daha hızlı işlenmek üzere sunucuda paralel olarak hesaplanır.
-   [Trafik](/rest/api/maps/traffic) Flow: gerçek zamanlı trafik akışı verilerine hem raster hem de vektör kutucukları olarak erişin.
-   [Coğrafi konum API 'si (Önizleme)](/rest/api/maps/geolocation/getiptolocationpreview) – bir IP adresinin konumunu alın.
-   [Hava durumu Hizmetleri](/rest/api/maps/weather) – gerçek zamanlı erişim elde edin ve hava durumu verilerini tahmin edin.

Aşağıdaki en iyi yöntemler kılavuzlarını da gözden geçirdiğinizden emin olun:

-   [Arama için en iyi yöntemler](./how-to-use-best-practices-for-search.md)
-   [Yönlendirme için en iyi uygulamalar](./how-to-use-best-practices-for-routing.md)

## <a name="prerequisites"></a>Önkoşullar

1. [Azure portalında](https://portal.azure.com) oturum açın. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
2. [Azure haritalar hesabı oluşturma](quick-demo-map-app.md#create-an-azure-maps-account)
3. Birincil anahtar veya abonelik anahtarı olarak da bilinen [birincil bir abonelik anahtarı alın](quick-demo-map-app.md#get-the-primary-key-for-your-account). Azure haritalar 'da kimlik doğrulaması hakkında daha fazla bilgi için bkz. [Azure haritalar 'da kimlik doğrulamasını yönetme](how-to-manage-authentication.md).

## <a name="geocoding-addresses"></a>Coğrafi kodlama adresleri

Coğrafi kodlama, bir adresi (gibi `"1 Microsoft way, Redmond, WA"` ) bir koordine (Boylam:-122,1298, Enlem: 47,64005 gibi) dönüştürme işlemidir. Koordinatlar daha sonra bir haritada bir raptiye konumlandırmak veya Haritayı ortalamak için kullanılır.

Azure haritalar, coğrafi kodlama adresleri için çeşitli yöntemler sağlar;

-   [Serbest biçimli adres coğrafi kodlama](/rest/api/maps/search/getsearchaddress): tek bir adres dizesi (gibi) belirtip `"1 Microsoft way, Redmond, WA"` isteği hemen işleyin. Bu hizmet, tek tek adreslere hızla coğrafi olarak kod eklemeniz gerekiyorsa önerilir.
-   [Yapılandırılmış adres coğrafi kodlama](/rest/api/maps/search/getsearchaddressstructured): sokak adı, şehir, ülke ve posta kodu gibi tek bir adresin parçalarını belirtin ve isteği hemen işleyin. Bu hizmet, tek tek adreslerin hızlı bir şekilde coğrafi olarak kodılabilmesi ve verilerin zaten ayrı adres bölümlerine ayrıştırılabilmeniz durumunda önerilir.
-   [Toplu iş adresi coğrafi kodlama](/rest/api/maps/search/postsearchaddressbatchpreview): en fazla 10.000 adresi içeren ve bunların bir süre içinde işlenmesini içeren bir istek oluşturun. Tüm adresler, sunucuda paralel olarak coğrafi olarak kodlanmıştır ve tamamlandığında tam sonuç kümesi indirilecektir. Bu hizmet, büyük veri kümelerine coğrafi kodlama için önerilir.
-   [Benzer arama](/rest/api/maps/search/getsearchfuzzy): Bu API, adres coğrafi kodlamasını ilgi çekici arama noktasıyla birleştirir. Bu API, bir adres, yer, yer işareti, ilgi alanı veya ilgi alanı kategorisi olabilecek bir serbest biçimli dize alır ve isteği hemen işleyebilir. Bu API, kullanıcıların aynı metin kutusundan adres veya ilgi çekici noktaları araybilecekleri uygulamalar için önerilir.
-   [Benzer toplu işlem araması](/rest/api/maps/search/postsearchfuzzybatchpreview): en fazla 10.000 adres, yer, yer işareti veya ilgi alanı içeren ve bunların bir süre içinde işlenmesini içeren bir istek oluşturun. Tüm veriler sunucuda paralel olarak işlenir ve tamamlandığında tam sonuç kümesi indirilecektir.

Aşağıdaki tablolar, yapılandırılmış ve serbest biçimli adres coğrafi kodlama için Azure haritalar 'daki karşılaştırılabilir API parametreleriyle Bing Haritalar API parametreleri ile çapraz başvuru yapılır.

**Adrese göre konum (yapılandırılmış adres)**

| Bing Haritalar API parametresi              | Karşılaştırılabilir Azure Maps API parametresi                 |
|--------------------------------------|-----------------------------------------------------|
| `addressLine`                      | `streetNumber``streetName`veya`crossStreet` |
| `adminDistrict`                    | `countrySubdivision`                              |
| `countryRegion`                    | `country` ve `countryCode`                     |
| `locality`                         | `municipality` veya `municipalitySubdivision`     |
| `postalCode`                       | `postalCode`                                      |
| `maxResults` (`maxRes`)          | `limit`                                           |
| `includeNeighborhood` (`inclnb`) | Yok – varsa Azure Maps tarafından her zaman döndürülür.   |
| `include` (`incl`)               | N/A – ülke ISO2 kodu her zaman Azure haritaları tarafından döndürülür. |
| `key`                              | `subscription-key` – Ayrıca bkz. [Azure Maps Ile kimlik doğrulama](./azure-maps-authentication.md) belgeleri. |
| `culture` (`c`)                  | `language` – [Desteklenen diller](./supported-languages.md) belgesine bakın. |
| `userRegion` (`ur`)              | `view` – [Desteklenen görünümler](./supported-languages.md#azure-maps-supported-views) belgeleri bölümüne bakın. |

Azure haritalar da destekler;

* `countrySecondarySubdivision` – İlçe, bölgeleri
* `countryTertiarySubdivision` -Adlandırılmış alan; Boroughs, Canton, communes
* `ofs` -Parametre ile birlikte sonuçlar aracılığıyla sayfa `maxResults` .

**Sorguya göre konum (serbest biçimli adres dizesi)**

| Bing Haritalar API parametresi              | Karşılaştırılabilir Azure Maps API parametresi      |
|--------------------------------------|------------------------------------------|
| `query`                            | `query`                                |
| `maxResults` (`maxRes`)          | `limit`                                |
| `includeNeighborhood` (`inclnb`) | Yok – varsa Azure Maps tarafından her zaman döndürülür.  |
| `include` (`incl`)               | N/A – ülke ISO2 kodu her zaman Azure haritaları tarafından döndürülür.  |
| `key`                              | `subscription-key` – Ayrıca bkz. [Azure Maps Ile kimlik doğrulama](./azure-maps-authentication.md) belgeleri. |
| `culture` (`c`)                  | `language` – [Desteklenen diller](./supported-languages.md) belgesine bakın.  |
| `userRegion` (`ur`)              | `view` – [Desteklenen görünümler](./supported-languages.md#azure-maps-supported-views) belgeleri bölümüne bakın. |

Azure haritalar da destekler;

* `typeahead` -Türler sorgu kısmi giriş olarak yorumlanıp, arama tahmine dayalı moda (otomatik öneri/otomatik tamamlama) girer.
* `countrySet` – Aramanın sınırlandıralınacağı ISO2 ülkelerin kodlarının virgülle ayrılmış bir listesi.
* `lat`/`lon`, `topLeft` / `btmRight` , `radius` – Sonuçları daha yerel olarak alakalı hale getirmek için Kullanıcı konumunu ve alanını belirtin.
* `ofs` -Parametre ile birlikte sonuçlar aracılığıyla sayfa `maxResults` .

Arama hizmetinin nasıl kullanılacağına ilişkin bir örnek [burada](./how-to-search-for-address.md)belgelenmiştir. [Arama belgeleri için en iyi uygulamaları](./how-to-use-best-practices-for-search.md) gözden geçirdiğinizden emin olun.

## <a name="reverse-geocode-a-coordinate-find-a-location-by-point"></a>Kodlamayı bir koordinatı ters çevir (noktaya göre konum bulma)

Ters coğrafi kodlama, coğrafi koordinatları (Boylam:-122,1298, Latitude: 47,64005 gibi) yaklaşık bir adrese (gibi) dönüştürme işlemidir `"1 Microsoft way, Redmond, WA"` .

Azure haritalar, birkaç ters coğrafi kodlama yöntemi sağlar;

-   [Adres ters coğrafi bölge](/rest/api/maps/search/getsearchaddressreverse): yaklaşık adresini almak ve isteği hemen işlemek için tek bir coğrafi koordinat belirtin.
-   [Çapraz cadde ters Geocoder](/rest/api/maps/search/getsearchaddressreversecrossstreet): yakın çapraz cadde bilgilerini almak için tek bir coğrafi koordinat belirtin (örneğin, & 1. ana) ve isteği hemen işleyin.
-   [Batch adresi ters coğrafi makinesi](/rest/api/maps/search/postsearchaddressreversebatchpreview): en fazla 10.000 koordinat içeren bir istek oluşturun ve bunların bir süre içinde işlenmesini sağlayabilirsiniz. Tüm veriler sunucuda paralel olarak işlenir ve tamamlandığında tam sonuç kümesi indirilecektir.

Aşağıdaki tabloda, Bing Haritalar API parametreleri Azure haritalar 'daki karşılaştırılabilir API parametreleriyle birlikte çapraz başvuru yapılır.

| Bing Haritalar API parametresi              | Karşılaştırılabilir Azure Maps API parametresi       |
|--------------------------------------|-------------------------------------------|
| `point`                              | `query`                                   |
| `includeEntityTypes`                 | `entityType` – Aşağıdaki varlık türü karşılaştırma tablosuna bakın.    |
| `includeNeighborhood` (`inclnb`)     | Yok – varsa Azure Maps tarafından her zaman döndürülür.         |
| `include` (`incl`)                   | N/A – ülke ISO2 kodu her zaman Azure haritaları tarafından döndürülür.    |
| `key`                                | `subscription-key` – Ayrıca bkz. [Azure Maps Ile kimlik doğrulama](./azure-maps-authentication.md) belgeleri. |
| `culture` (`c`)                      | `language` – [Desteklenen diller](./supported-languages.md) belgesine bakın.   |
| `userRegion` (`ur`)                  | `view` – [Desteklenen görünümler](./supported-languages.md#azure-maps-supported-views) belgeleri bölümüne bakın. |

[Arama belgeleri için en iyi uygulamaları](./how-to-use-best-practices-for-search.md) gözden geçirdiğinizden emin olun.

Azure haritalar ters coğrafi kodlama API 'SI, uygulamanızı geçirirken tümleştirme açısından yararlı olabilecek Bing Haritalar 'da bulunmayan bazı ek özelliklere sahiptir:

* Hız sınırı verilerini alın.
* Yol kullanım bilgilerini alma; Yerel yol, arterial, sınırlı erişim, rampa, vb.
* Koordinat, koordinat tarafında yer alır.

**Varlık türü karşılaştırma tablosu**

Aşağıdaki tabloda, Bing Haritalar varlık türü değerleri, Azure haritalar 'daki eşdeğer özellik adlarına başvuruyordur.

| Bing Haritalar varlık türü | Karşılaştırılabilir Azure Maps varlık türü               | Description                                |
|-----------------------|-------------------------------------------------|--------------------------------------------|
| `Address`             |                                                 | *Adres*                                  |
| `Neighborhood`        | `Neighbourhood`                                 | *Larım*                             |
| `PopulatedPlace`      | `Municipality` veya `MunicipalitySubdivision`     | *Şehir*, *kasaya veya alt* veya *süper şehir*     |
| `Postcode1`           | `PostalCodeArea`                                | *Posta kodu* veya *posta kodu*                |
| `AdminDivision1`      | `CountrySubdivision`                            | *Eyalet* veya *İl*                      |
| `AdminDivision2`      | `CountrySecondarySubdivison`                    | *İlçe veya bölge* *'lar*                    |
| `CountryRegion`       | `Country`                                       | *Ülke adı*                             |
|                       | `CountryTertiarySubdivision`                    | *Boroughs*, *Canton*, *communes*          |

## <a name="get-location-suggestions-autosuggest"></a>Konum önerilerini alma (otomatik öneri)

Azure haritalar arama API 'sinin bazıları, otomatik öneri senaryolarında kullanılabilecek destek tahmini modundan biridir. Azure Maps [benzer arama](/rest/api/maps/search/getsearchfuzzy) API 'Si, Bing Haritalar otomatik öneri API 'si ile aynıdır. Aşağıdaki API, tahmine dayalı modunu da destekler, sorguya ekler `&typeahead=true` ;

-   [Serbest biçimli adres coğrafi kodlama](/rest/api/maps/search/getsearchaddress): tek bir adres dizesi (gibi) belirtip `"1 Microsoft way, Redmond, WA"` isteği hemen işleyin. Bu hizmet, tek tek adreslere hızla coğrafi olarak kod eklemeniz gerekiyorsa önerilir.
-   [Benzer arama](/rest/api/maps/search/getsearchfuzzy): Bu API, adres coğrafi kodlamasını ilgi çekici arama noktasıyla birleştirir. Bu API, bir adres, yer, yer işareti, ilgi alanı veya ilgi alanı kategorisi olabilecek bir serbest biçimli dize alır ve isteği hemen işleyebilir. Bu API, kullanıcıların aynı metin kutusundan adres veya ilgi çekici noktaları araybilecekleri uygulamalar için önerilir.
-   [POI arama](/rest/api/maps/search/getsearchpoi): ada göre ilgi alanları için arama yapın. Örneğin; `"starbucks"`.
-   [POI kategorisi arama](/rest/api/maps/search/getsearchpoicategory): kategoriye göre ilgi alanları için arama yapın. Örneğin; "Restoran".

## <a name="calculate-routes-and-directions"></a>Rotaları ve yönleri hesapla

Azure haritalar, yolları ve yönergeleri hesaplamak için kullanılabilir. Azure haritalar, Bing Haritalar yönlendirme hizmeti ile aynı işlevselliklerin çoğuna sahiptir; Örneğin;

* varış ve ayrılma süreleri
* gerçek zamanlı ve tahmine dayalı trafik rotaları
* farklı ulaşım modları; itici, yürüyen, kamyon
* güzergah noktası sırası iyileştirmesi (seyahat satış Bay)

> [!NOTE]
> Azure haritalar tüm waypoints 'in koordinatlarını gerektirir. Önce adreslerin coğrafi kodlanmış olması gerekir.

Azure haritalar yönlendirme hizmeti, yolları hesaplamak için aşağıdaki API 'Leri sağlar;

-   [Rotayı hesapla](/rest/api/maps/route/getroutedirections): bir rotayı hesaplayın ve isteğin hemen işlenmesini sağlayabilirsiniz. Bu API hem GET hem POST isteklerini destekler. Çok sayıda waypoints belirtildiğinde veya URL isteğinin çok uzun olmadığından ve sorunlara yol açmamasına yetecek kadar fazla yol seçeneği kullanılırken POST istekleri önerilir.
-   [Batch rotası](/rest/api/maps/route/postroutedirectionsbatchpreview): en fazla 1.000 yol isteği içeren bir istek oluşturun ve bunların bir süre içinde işlenmesini isteyin. Tüm veriler sunucuda paralel olarak işlenir ve tamamlandığında tam sonuç kümesi indirilecektir.
-   [Mobility Hizmetleri (Önizleme) ](/rest/api/maps/mobility): genel aktarım kullanarak yolları ve yönergeleri hesaplayın.

Aşağıdaki tabloda, Bing Haritalar API parametreleri Azure haritalar 'daki karşılaştırılabilir API parametreleriyle birlikte çapraz başvuru yapılır.

| Bing Haritalar API parametresi                                    | Karşılaştırılabilir Azure Maps API parametresi               |
|------------------------------------------------------------|---------------------------------------------------|
| `avoid`                                                    | `avoid`                                           |
| `dateTime` (`dt`)                                          | `departAt` veya `arriveAt`                          |
| `distanceBeforeFirstTurn` (`dbft`)                         | Yok                                               |
| `distanceUnit` (`du`)                                      | Yok – Azure Maps yalnızca ölçüm sistemini kullanır.     |
| `heading` (`hd`)                                           | `vehicleHeading`                                  |
| `maxSolutions` (`maxSolns`)                                | `maxAlternatives`, `alternativeType` , `minDeviationDistance` ve `minDeviationTime`  |
| `optimize` (`optwz`)                                       | `routeType` ve `traffic`                         |
| `optimizeWaypoints` (`optWp`)                              | `computeBestOrder`                                |
| `routeAttributes` (`ra`)                                   | `instructionsType`                                |
| `routePathOutput` (`rpo`)                                  | `routeRepresentation`                             |
| `timeType` (`tt`)                                          | `departAt` veya `arriveAt`                          |
| `tolerances` (`tl`)                                        | Yok                                               |
| `travelMode`                                               | `travelMode`                                      |
| `waypoint.n` ( `wp.n` ) veya `viaWaypoint.n` (`vwp.n`)         | `query` – biçimde koordinatları `lat0,lon0:lat1,lon1….`   |
| `key`                                                      | `subscription-key` – Ayrıca bkz. [Azure Maps Ile kimlik doğrulama](./azure-maps-authentication.md) belgeleri. |
| `culture` (`c`)                                            | `language` – [Desteklenen diller](./supported-languages.md) belgesine bakın. |
| `userRegion` (`ur`)                                        | `view` – [Desteklenen görünümler](./supported-languages.md#azure-maps-supported-views) belgeleri bölümüne bakın. |

Azure haritalar yönlendirme API 'SI aynı API içindeki kamyon yönlendirmeyi de destekler. Aşağıdaki tabloda, ek Bing Haritalar kamyonu yönlendirme parametrelerine Azure Maps 'teki karşılaştırılabilir API parametreleri ile çapraz başvuru yapılır.

| Bing Haritalar API parametresi                  | Karşılaştırılabilir Azure Maps API parametresi        |
|------------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                 | Yok – ölçüm içindeki boyutlar yalnızca desteklenir. |
| `weightUnit` (`wu`)                      | Yok – kilogram içindeki ağırlıklar yalnızca desteklenir. |
| `vehicleHeight` (`height`)               | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                 | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                   | `vehicleLength`                            |
| `vehicleWeight` (`weight`)               | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                 | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                 | **yok**                                    |
| `vehicleSemi` (`semi`)                   | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)             | **yok**                                    |
| `vehicleMinTurnRadius` (`vmtr`)          | **yok**                                    |
| `vehicleAvoidCrossWind` (`vacw`)         | **yok**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)     | **yok**                                    |
| `vehicleHazardousMaterials` (`vhm`)      | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)        | `vehicleLoadType`                          |

> [!TIP]
> Varsayılan olarak, Azure Maps Route API 'SI yalnızca bir Özet (uzaklık ve saatler) ve yol yolunun koordinatlarını döndürür. `instructionsType`Açma yönergelerini almak için parametresini kullanın. `routeRepresentation`Parametresi Özet ve yol yolunu filtrelemek için kullanılabilir.

[Yönlendirme belgelerinin en iyi yöntemlerini](./how-to-use-best-practices-for-routing.md) de gözden geçirdiğinizden emin olun.

Azure haritalar yönlendirme API 'SI, Bing Haritalar 'da, uygulamanızı geçirirken tümleştirilebilen yararlı olabilecek birçok ek özelliğe sahiptir:

* Rota türü desteği: en kısa, en hızlı, trilini ve en çok yakıt etkin.
* Ek seyahat modları için destek: bisiklet, Bus, otocycle, Taxi, kamyon ve Van.
* 150 waypoints desteği.
* Tek bir istekte birden çok seyahat süresi hesaplama; geçmiş trafik, canlı trafik, trafik yok.
* Ek yol türlerinden kaçının: Carpool yol, geri alınamaz yollar, zaten kullanılan yollar.
* Altyapı belirtimi tabanlı yönlendirme. Kalan yakıt/ücret ve altyapı belirtimlerini temel alarak, combustion veya elektrik taşıtlar için rotaları hesaplayın.
* Maksimum araç hızını belirtin.

## <a name="snap-coordinates-to-road"></a>Koordinatları yola yasla

Azure haritalar 'daki yollara yönelik koordinatları yapışmanın birkaç yolu vardır.

* Koordinatları yol ağı üzerinde bir mantıksal yola yaslama için yol yönleri API 'sini kullanın.
* Bağımsız koordinatları vektör kutucuklarında en yakın yola eklemek için Azure Maps web SDK 'sını kullanın.
* Tek tek koordinatları eklemek için Azure Maps vektör kutucuklarını doğrudan kullanın.

**Koordinatları yaslama için rota yönü API 'sini kullanma**

Azure haritalar [yol yönleri](/rest/api/maps/route/postroutedirections) API 'sini kullanarak koordinatları yollara yönlendirebilir. Bu hizmet, bir koordinat kümesi arasındaki mantıksal bir yolu yeniden oluşturmak için kullanılabilir ve Bing Haritalar ile yol API 'sine ek olarak karşılaştırılabilir.

Koordinatları yollara eklemek için yol yönleri API 'SI kullanmanın iki farklı yolu vardır.

* 150 koordinatları veya daha az varsa, rota yönlerini al API 'sinde waypoints olarak geçirilebilir. Bu yaklaşımın kullanılması iki farklı tür veri türü alınabilir; yol yönergeleri, tek tek bir şekilde, yol yolunda, koordinat arasındaki tam yolu dolduran, enterpolasyonlu bir koordinat kümesine sahip olacaktır.
* 150 ' den fazla koordinat varsa, yönlendirme yönlendirmeleri API 'SI kullanılabilir. Başlangıç ve bitiş koordinatlarının, sorgu parametresine geçirilmesi gerekir, ancak tüm koordinatlar `supportingPoints` POST isteğinin gövdesinde parametreye geçirilebilir ve bir coğrafi JSON geometrisi koleksiyonu olarak biçimlendirilir. Bu yaklaşım kullanılarak sunulan tek açık veriler, koordinatlar arasındaki tam yolu dolduran, enterpolasyonlu bir koordinat kümesi olan yol yolu olacaktır. Azure Maps web SDK 'sindeki hizmetler modülünü kullanarak bu yaklaşımın [bir örneği aşağıda](https://azuremapscodesamples.azurewebsites.net/?sample=Snap%20points%20to%20logical%20route%20path) verilmiştir.

Aşağıdaki tabloda, Bing Haritalar API parametreleri Azure haritalar 'daki karşılaştırılabilir API parametreleriyle birlikte çapraz başvuru yapılır.

| Bing Haritalar API parametresi    | Karşılaştırılabilir Azure Maps API parametresi                                 |
|----------------------------|---------------------------------------------------------------------|
| `points`                   | `supportingPoints` – Bu noktaları POST isteğinin gövdesine geçirin  |
| `interpolate`              | Yok                                                                 |
| `includeSpeedLimit`        | Yok                                                                 |
| `includeTruckSpeedLimit`   | Yok                                                                 |
| `speedUnit`                | Yok                                                                 |
| `travelMode`               | `travelMode`                                                        |
| `key`                      | `subscription-key` – Ayrıca bkz. [Azure Maps Ile kimlik doğrulama](./azure-maps-authentication.md) belgeleri. |
| `culture` (`c`)            | `language` – [Desteklenen diller](./supported-languages.md) belgesine bakın.   |
| `userRegion` (`ur`)        | `view` – [Desteklenen görünümler](./supported-languages.md#azure-maps-supported-views) belgeleri bölümüne bakın.   |

Azure haritalar yönlendirme API 'SI ayrıca mantıksal yolların hesaplandığından emin olmak için aynı API içinde kamyonu yönlendirme parametresini destekler. Aşağıdaki tabloda, ek Bing Haritalar kamyonu yönlendirme parametrelerine Azure Maps 'teki karşılaştırılabilir API parametreleri ile çapraz başvuru yapılır.

| Bing Haritalar API parametresi                 | Karşılaştırılabilir Azure Maps API parametresi        |
|-----------------------------------------|--------------------------------------------|
| `dimensionUnit` (`dims`)                | Yok – ölçüm içindeki boyutlar yalnızca desteklenir. |
| `weightUnit` (`wu`)                     | Yok – kilogram içindeki ağırlıklar yalnızca desteklenir. |
| `vehicleHeight` (`height`)              | `vehicleHeight`                            |
| `vehicleWidth` (`width`)                | `vehicleWidth`                             |
| `vehicleLength` (`vl`)                  | `vehicleLength`                            |
| `vehicleWeight` (`weight`)              | `vehicleWeight`                            |
| `vehicleAxles` (`axles`)                | `vehicleAxelWeight`                        |
| `vehicleTrailers` (`vt`)                | **yok**                                    |
| `vehicleSemi` (`semi`)                  | `vehicleCommercial`                        |
| `vehicleMaxGradient` (`vmg`)            | **yok**                                    |
| `vehicleMinTurnRadius` (`vmtr`)         | **yok**                                    |
| `vehicleAvoidCrossWind` (`vacw`)        | **yok**                                    |
| `vehicleAvoidGroundingRisk` (`vagr`)    | **yok**                                    |
| `vehicleHazardousMaterials` (`vhm`)     | `vehicleLoadType`                          |
| `vehicleHazardousPermits` (`vhp`)       | `vehicleLoadType`                          |

Bu yaklaşım yol yönleri API 'sini kullandığından, bu hizmetteki tam seçenek kümesi, koordinatı yollara eklemek için kullanılan mantığı özelleştirmek için kullanılabilir. Örneğin, bir ayrılma saati belirtildiğinde, geçmiş trafik verilerinin dikkate alınması neden olur.

Azure haritalar yol yönleri API 'SI Şu anda hızlı sınır verileri döndürmüyor, ancak Azure Maps ters coğrafi kodlama API 'SI kullanılarak alınabilir.

**Koordinatları yaslama için Web SDK 'sını kullanma**

Azure Haritalar Web SDK 'Sı, haritaları işlemek için vektör kutucukları kullanır. Bu vektör kutucukları ham yol geometrisi bilgilerini içerir ve tek tek koordinatların kolay bir şekilde yapışması için en yakın yolu hesaplamak üzere kullanılabilir. Bu, koordinatların yolların üzerinde görsel olarak görünmesini istediğinizde ve verileri görselleştirmek için zaten Azure Maps web SDK 'sını kullandığınızdan yararlıdır.

Bu yaklaşım ancak yalnızca harita görünümü içinde yüklenen yol segmentlerine yastur. Ülke düzeyinde yakınlaştırıldığında, yol verisi olmayabilir, bu nedenle yaslama yapılamaz, ancak bu yakınlaştırma düzeyinde tek bir piksel birkaç şehir bloğunu temsil edebilir, böylece yapışma gerekli değildir. Bunu çözmek için, eşleme her taşıma tamamlandığında yapışma mantığı uygulanabilir. Bunu gösteren [bir kod örneği aşağıda verilmiştir](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic) .

**Azure Maps vektör kutucukları, koordinatları doğrudan yaslama için kullanma**

Azure haritalar vektör kutucukları, bireysel koordinatların temel bir şekilde yaslamasını yapmak için bir yol üzerinde en yakın noktayı hesaplamak üzere kullanılabilecek ham yol geometrisi verilerini içerir. Tüm yol kesimleri, yakınlaştırma düzeyi 15 olan kesimlerde görünür, bu nedenle kutucukları buradan almak isteyeceksiniz. Daha sonra, kutucukların gerekli olduğunu ve kutucukları geometriye dönüştürmenizi sağlamak için [quadtree kutucuğunu](./zoom-levels-and-tile-grid.md) kullanabilirsiniz. [Turf js](http://turfjs.org/) veya [Nettopologyısuite](https://github.com/NetTopologySuite/NetTopologySuite) gibi bir uzamsal matematik kitaplığı, en yakın çizgi segmentlerini hesaplamak için kullanılabilir.

## <a name="retrieve-a-map-image-static-map"></a>Harita görüntüsünü alma (Statik eşleme)

Azure Maps, çakışan verilerle statik harita görüntülerini işlemek için bir API sağlar. Azure haritalar [harita görüntü işleme](/rest/api/maps/render/getmapimagerytile) API 'Si, Bing Haritalar 'daki statik harita API 'siyle karşılaştırılabilir.

> [!NOTE]
> Azure haritalar Merkezi, tüm Pushpin 'leri ve yol konumlarını biçimde koordine etmek için, `longitude,latitude` Bing Haritalar `latitude,longitude` biçimini kullanır.</p>
<p>Önce adreslerin coğrafi kodlanmış olması gerekir.

Aşağıdaki tabloda, Bing Haritalar API parametreleri Azure haritalar 'daki karşılaştırılabilir API parametreleriyle birlikte çapraz başvuru yapılır.

| Bing Haritalar API parametresi  | Karşılaştırılabilir Azure Maps API parametresi            |
|--------------------------|------------------------------------------------|
| `centerPoint`            | `center`                                       |
| `format`                 | `format` – URL yolunun bir parçası olarak belirtilir. Şu anda yalnızca PNG destekleniyor.  |
| `heading`                | Yok – Streetside desteklenmez.                |
| `imagerySet`             | `layer` ve `style` – [desteklenen harita stilleri](./supported-map-styles.md) belgelerine bakın.   |
| `mapArea` (`ma`)         | `bbox`                                         |
| `mapLayer` (`ml`)        | Yok                                            |
| `mapSize` (`ms`)         | `width` ve `height` – boyutu 8192x8192 olabilir. |
| `declutterPins` (`dcl`)  | Yok                                            |
| `dpi`                    | Yok                                            |
| `drawCurve`              | `path`                                         |
| `mapMetadata`            | Yok                                            |
| `pitch`                  | Yok – Streetside desteklenmez.                |
| `pushpin` (`pp`)         | `pins`                                         |
| `zoomLevel`              | `zoom`                                         |
| `query`                  | N/A – orta veya sınırlayıcı kutu kullanılmalıdır.     |
| `highlightEntity` (`he`) | Yok                                            |
| `style`                  | Yok                                            |
| rota parametreleri         | Yok                                            |
| `key`                    | `subscription-key` – Ayrıca bkz. [Azure Maps Ile kimlik doğrulama](./azure-maps-authentication.md) belgeleri. |
| `culture` (`c`)          | `language` – [Desteklenen diller](./supported-languages.md) belgesine bakın.   |
| `userRegion` (`ur`)      | `view` – [Desteklenen görünümler](./supported-languages.md#azure-maps-supported-views) belgeleri bölümüne bakın. |

> [!NOTE]
> Azure haritalar, Bing Haritalar 'da kullanılan harita kutucukları boyutunun iki katı olan kutucukları olan bir kutucuk sistemi kullanır. Bu nedenle, Azure haritalar 'daki yakınlaştırma düzeyi değeri, Bing Haritalar ile karşılaştırıldığında Azure Maps 'ta bir yakınlaştırma düzeyi daha yakından görüntülenir. 1 ile geçiş yaptığınız isteklerindeki yakınlaştırma düzeyini bu şekilde telafi altına düşürün.

Daha fazla bilgi için [harita görüntü Işleme API 'Sindeki nasıl yapılır kılavuzuna](./how-to-render-custom-data.md) bakın.

Azure haritalar işleme hizmeti, statik bir harita görüntüsü oluşturamayacak ek olarak tarama (PNG) ve vektör biçimindeki harita kutucuklarına doğrudan erişme özelliği de sağlar;

-   [Harita kutucuğu](/rest/api/maps/render/getmaptile) – temel haritalar (yollar, sınırlar, arka plan) için tarama (png) ve vektör kutucukları alın.
-   [Harita Imagery kutucuğu](/rest/api/maps/render/getmapimagerytile) – havadan ve uydu canlandırın kutucukları alın.

### <a name="pushpin-url-parameter-format-comparison"></a>İğne URL parametre biçimi karşılaştırması

**Önce: Bing Haritalar**

Bing Haritalar 'da, URL 'deki parametresi kullanılarak bir statik harita görüntüsüne Pushpin eklenebilir `pushpin` . `pushpin`Parametresi `latitude,longitude` , aşağıda gösterildiği gibi, bir simge stili ve metin etiketi (en fazla üç karakter) biçiminde bir konum alır:

> `&pushpin=latitude,longitude;iconStyle;label`

Başka `pushpin` bir değer KÜMESIYLE URL 'ye ek parametreler eklenerek ek Pushpin eklenebilir. Raptiye simge stilleri, Bing Haritalar API 'sinde bulunan önceden tanımlanmış stillerden biriyle sınırlıdır.

Örneğin, Bing Haritalar 'da, "AB" etiketine sahip kırmızı bir raptiye, şu URL parametresiyle birlikte koordinatlara (Boylam:-110, Latitude: 45) eklenebilir:

> `&pushpin=45,-110;7;AB`

![Bing Haritalar statik eşleme PIN 'i](media/migrate-bing-maps-web-service/bing-maps-static-map-pin.jpg)

**Sonrasında: Azure Maps**

Azure haritalar 'da, URL 'de parametre belirtilerek Pushpin 'ler de statik bir harita görüntüsüne eklenebilir `pins` . Azure haritalar 'daki Pushpin 'ler, simge stili ve bu simge stilini kullanan konumların listesi belirtilerek tanımlanır. Bu bilgiler daha sonra parametreye geçirilir, `pins` farklı stillerle Pushpin 'leri desteklemek için birden çok kez belirtilebilir.

> `&pins=iconType|pinStyles||pinLocation1|pinLocation2|...`

Ek stiller `pins` , URL 'ye farklı bir stille ve konum kümesiyle ek parametreler eklenerek kullanılabilir.

Azure haritalar, PIN konumlarına geldiğinde, koordinatları biçiminde olmasını gerektirir, `longitude latitude` Bing Haritalar ise `latitude,longitude` biçim kullanır. Ayrıca, Azure Maps 'ta boylam ve enlem **bir virgülle ayırarak bir boşluk olduğunu** unutmayın.

`iconType`Değer, oluşturulacak PIN türünü belirtir ve aşağıdaki değerlere sahip olabilir:

* `default` – Varsayılan pin simgesi.
* `none` – Simge gösterilmez, yalnızca Etiketler işlenir.
* `custom` – Özel bir simgenin kullanılacağını belirtir. Simgenin sonuna işaret eden bir URL, `pins` PIN konum bilgileri sonrasında parametrenin sonuna eklenebilir.
* `{udid}` – Azure Maps veri depolama platformunda depolanan bir simgenin benzersiz veri KIMLIĞI (UDıD).

Azure haritalar 'daki PIN stilleri `optionNameValue` , bu şekilde kanal () karakterleriyle ayrılmış birden çok stil içeren biçimiyle eklenir `|` `iconType|optionName1Value1|optionName2Value2` . Seçenek adları ve değerleri ayrılmadığını not edin. Aşağıdaki stil seçenek adları, Azure Maps 'ta pushpın 'leri stil eklemek için kullanılabilir:

* `al` – Pushpin opaklığını (Alpha) belirtir. 0 ile 1 arasında bir sayı olabilir.
* `an` – PIN bağlayıcısını belirtir. Biçimde belirtilen X ve y piksel değerleri `x y` .
* `co` – PIN rengi. 24 bit onaltılık renk olmalıdır: `000000` to `FFFFFF` .
* `la` – Etiket bağlayıcısını belirtir. Biçimde belirtilen X ve y piksel değerleri `x y` .
* `lc` – Etiketin rengi. 24-, onaltılık renk olmalıdır: `000000` `FFFFFF` .
* `ls` – Etiketin piksel cinsinden boyutu. 0 ' dan büyük bir sayı olabilir.
* `ro` – Simgeyi döndürmek için derece cinsinden bir değer. -360 ile 360 arasında bir sayı olabilir.
* `sc` – Pin simgesi için bir ölçek değeri. 0 ' dan büyük bir sayı olabilir.

Her PIN konumu için etiket değerleri, konum listesindeki tüm Pushpin 'ler için geçerli olan tek bir etiket değerine sahip olmak yerine belirtilir. Etiket değeri birden fazla karakterden oluşan bir dize olabilir ve bir stil veya konum değeri olarak yanlış alınacağından emin olmak için tek tırnak işaretleriyle sarmalanabilir.

Örneğin, Azure Maps 'ta `FF0000` "Space iğne" etiketiyle kırmızı () varsayılan simge ekleme (15 50) koordinatlarındaki simgenin (Boylam:-122,349300, Latitude: 47,620180) AŞAĞıDAKI URL parametresiyle yapılabilir:

> `&pins=default|coFF0000|la15 50||'Space Needle'-122.349300 47.620180`

![Azure Maps statik harita PIN 'i](media/migrate-bing-maps-web-service/azure-maps-static-map-pin.jpg)

Aşağıdaki örnek, ' 1 ', ' 2 ' ve ' 3 ' Etiket değerleriyle üç PIN ekler:

> `&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12`

![Azure haritalar statik eşleme birden çok pin](media/migrate-bing-maps-web-service/azure-maps-static-map-multiple-pins.jpg)

### <a name="draw-curve-url-parameter-format-comparison"></a>Çiz eğrisi URL parametre biçimi karşılaştırması

**Önce: Bing Haritalar**

Bing Haritalar, çizgiler ve çokgenler, URL 'deki parametresi kullanılarak bir statik harita görüntüsüne eklenebilir `drawCurve` . `drawCurve`Parametresi, aşağıda gösterildiği gibi bir şekil türü, bir stil türü ve haritada işlenecek konumların bir listesini alır:

> `&drawCurve=shapeType,styleType,location1,location2...`

Ek stiller `drawCurve` , URL 'ye farklı bir stille ve konum kümesiyle ek parametreler eklenerek kullanılabilir.

Bing haritalarındaki konumlar biçimiyle belirtilir `latitude1,longitude1_latitude2,longitude2_…` . Konumlar da kodlanabilir.

Bing Haritalar 'daki şekil türleri arasında çizgiler, çokgenler, daire ve eğri bulunur. Stil türleri çizgi rengini, çizgi kalınlığını, ana hat rengini, Fill rengini, ana hat kalınlığını ve dairesel yarıçapı içerir.

Örneğin, Bing Haritalar 'da %50 opaklık ve dört piksellik bir çizgi, şu URL parametresi ile Koordinatlar (Boylam:-110, Enlem: 45 ve Boylam:-100, Latitude: 50) arasında haritaya eklenebilir:

`&drawCurve=l,FF000088,4;45,-110_50,-100`

![Bing Haritalar statik eşleme çizgisi](media/migrate-bing-maps-web-service/bing-maps-static-map-line.jpg)

**Sonrasında: Azure Maps**

Azure haritalar 'da, URL 'de *yol* parametresi belirtilerek bir statik harita görüntüsüne çizgiler ve çokgenler da eklenebilir. Bing Haritalar gibi, bu parametrede bir stil ve konumların listesi belirlenebilir ve farklı stillerle birden çok daire, çizgi ve çokgen işlemek için *yol* parametresi birden çok kez belirtilebilir.

> `&path=pathStyles||pathLocation1|pathLocation2|...`

Azure haritalar, yol konumlarına geldiğinde, koordinatları biçiminde olmasını gerektirir, `longitude latitude` Bing Haritalar ise `latitude,longitude` biçim kullanır. Ayrıca, Azure Maps 'ta boylam ve enlem **bir virgülle ayırarak bir boşluk olduğunu** unutmayın. Azure Maps, şu anda kodlanmış yolları desteklemez. Daha büyük veri kümeleri, [burada](./how-to-render-custom-data.md#get-data-from-azure-maps-data-storage)belgelendiği gibi Azure Maps VERI depolama API 'Sine bir coğrafi JSON olarak yüklenebilir.

Azure haritalar 'daki yol stilleri `optionNameValue` , bu şekilde kanal () karakterleriyle ayrılmış birden çok stil içeren biçimiyle eklenir `|` `optionName1Value1|optionName2Value2` . Seçenek adları ve değerleri ayrılmadığını not edin. Aşağıdaki stil seçenek adları, Azure haritalar 'daki yollara stil eklemek için kullanılabilir:

* `fa` – Poligonları işlerken kullanılan Fill Color geçirgenliği (Alpha). 0 ile 1 arasında bir sayı olabilir.
* `fc` – Bir çokgenin alanını işlemek için kullanılan Fill Color.
* `la` – Çizgiler ve çokgenler ana hattı işlenirken kullanılan çizgi rengi geçirgenliği (Alpha). 0 ile 1 arasında bir sayı olabilir.
* `lc` – Çizgileri oluşturmak için kullanılan çizgi rengi ve çokgenler ana hattı.
* `lw` – Çizginin piksel cinsinden genişliği.
* `ra` – Ölçümlerde bir daire yarıçapı belirtir.

Örneğin, Azure Maps 'ta, %50 opaklık ve dört piksellik bir çizgi, şu URL parametresiyle birlikte koordinatları (Boylam:-110, Enlem: 45 ve Boylam:-100, Latitude: 50) arasında haritaya eklenebilir:

> `&path=lc0000FF|la.5|lw4||-110 45|-100 50`

![Azure haritalar statik eşleme çizgisi](media/migrate-bing-maps-web-service/azure-maps-static-map-line.jpg)

## <a name="calculate-a-distance-matrix"></a>Uzaklık matrisini hesaplama

Azure Maps, bir konum kümesi arasında uzaklık matrisi olarak seyahat sürelerini ve uzaklıkları hesaplamak için bir API sağlar. Azure haritalar Mesafe Matrisi API 'SI, Bing Haritalar 'daki uzaklık Matrisi API 'siyle karşılaştırılabilir;

-   [Yol matrisi](/rest/api/maps/route/postroutematrixpreview): zaman uyumsuz bir çıkış ve hedef kümesi için seyahat sürelerini ve uzaklıkları hesaplar. İstek başına en fazla 700 hücre desteklenir (hedef sayısı ile çarpılan çıkış sayısı). Bu kısıtlama göz önünde bulundurularak, olası matris boyutlarına örnek olarak şunlar verilebilir: `700x1` , `50x10` ,, `10x10` `28x25` , `10x70` .

> [!NOTE]
> Uzaklık Matrisi API 'sine yönelik bir istek yalnızca istek gövdesinde kaynak ve hedef bilgileri olan bir POST isteği kullanılarak yapılabilir.</p>
<p>Ayrıca, Azure Maps tüm kaynakları ve hedefleri koordine etmek gerektirir. Önce adreslerin coğrafi kodlanmış olması gerekir.

Aşağıdaki tabloda, Bing Haritalar API parametreleri Azure haritalar 'daki karşılaştırılabilir API parametreleriyle birlikte çapraz başvuru yapılır.

| Bing Haritalar API parametresi | Karşılaştırılabilir Azure Maps API parametresi                         |
|-------------------------|-------------------------------------------------------------|
| `origins`               | `origins` – istek SONRASı gövdesinin coğrafi JSON olarak belirleyin.    |
| `destinations`          | `destination` – istek SONRASı gövdesinin coğrafi JSON olarak belirleyin. |
| `endTime`               | `arriveAt`                                                  |
| `startTime`             | `departAt`                                                  |
| `travelMode`            | `travelMode`                                                |
| `resolution`            | Yok                                                         |
| `distanceUnit`          | Yok – ölçüm içindeki tüm uzaklıklar.                              |
| `timeUnit`              | N/A – her zaman Saniyeler içinde.                                 |
| `key`                   | `subscription-key` – Ayrıca bkz. [Azure Maps Ile kimlik doğrulama](./azure-maps-authentication.md) belgeleri. |
| `culture` (`c`)         | `language` – [Desteklenen diller](./supported-languages.md) belgesine bakın.  |
| `userRegion` (`ur`)     | `view` – [Desteklenen görünümler](./supported-languages.md#azure-maps-supported-views) belgeleri bölümüne bakın.     |

> [!TIP]
> Azure haritalar yönlendirme API 'sinde (kamyon yönlendirme, altyapı belirtimleri, kaçınma...) sunulan tüm gelişmiş yönlendirme seçenekleri, Azure haritalar Mesafe Matrisi API 'sinde desteklenir.

## <a name="calculate-an-isochrone"></a>Bir ısovaone hesaplama

Azure Maps, belirli bir süre veya yakıt/ücret miktarı dahilindeki bir kaynak noktasından herhangi bir yöne doğru bir şekilde bir alan kaplayan bir ıseone 'yı hesaplamak için bir API sağlar. Azure haritalar yol aralığı API 'SI, Bing Haritalar 'daki ısovaone API 'siyle karşılaştırılabilir;

-   [Rota](/rest/api/maps/route/getrouterange) Aralık * *: belirtilen bir süre, uzaklık veya yakıt/ücret miktarı dahilinde bir kaynak noktasından herhangi bir yöne doğru bir şekilde bir alan kaplayan bir çokgen hesaplama.

> [!NOTE]
> Azure haritalar, sorgu kaynağının bir koordinat olmasını gerektirir. Önce adreslerin coğrafi kodlanmış olması gerekir.</p>
<p>Ayrıca, Bing haritalar zaman veya mesafeye göre ikizlerini hesaplayabilir ve Azure Maps, zaman, uzaklık veya kullanılabilir yakıt/ücret miktarına göre ikizlerini hesaplayabilir.

Aşağıdaki tabloda, Bing Haritalar API parametreleri Azure haritalar 'daki karşılaştırılabilir API parametreleriyle birlikte çapraz başvuru yapılır.

| Bing Haritalar API parametresi      | Karşılaştırılabilir Azure Maps API parametresi            |
|------------------------------|------------------------------------------------|
| `waypoint` (`wp`)            | `query`                                        |
| `dateTime` (`dt`)            | `departAt`                                     |
| `maxTime`                    | `timeBudgetInSec`                              |
| `timeUnit` (`tu`)            | N/A – her zaman Saniyeler içinde.                    |
| `travelMode` (`mode`)        | `travelMode`                                   |
| `maxDistance` (`maxDis`)     | `distanceBudgetInMeters`                       |
| `distanceUnit` (`du`)        | Yok – ölçüm içindeki tüm uzaklıklar.                 |
| `optimize` (`optmz`)         | `routeType`                                    |
| `key`                        | `subscription-key` – Ayrıca bkz. [Azure Maps Ile kimlik doğrulama](./azure-maps-authentication.md) belgeleri. |
| `culture` (`c`)              | `language` – [Desteklenen diller](./supported-languages.md) belgesine bakın.  |
| `userRegion` (`ur`)          | `view` – [Desteklenen görünümler](./supported-languages.md#azure-maps-supported-views) belgeleri bölümüne bakın. |

> [!TIP]
> Azure haritalar yönlendirme API 'sinde (kamyon yönlendirme, altyapı belirtimleri, kaçınma...) sunulan tüm gelişmiş yönlendirme seçenekleri Azure Maps ısovaone API 'sinde desteklenir.

## <a name="search-for-points-of-interest"></a>İlgi noktası arama

Aşağıdaki API 'Ler kullanılarak Bing Haritalar 'da ilgi çekici veri arama yapılabilir:

-   **Yerel arama:** Yakın (radyal arama), ada göre veya varlık türüne (kategori) göre ilgi çekici noktaları arar. Azure haritalar [POI araması](/rest/api/maps/search/getsearchpoi) ve [POI kategorisi arama](/rest/api/maps/search/getsearchpoicategory) API 'leri bu API 'nin en çok gibidir.
-   **Konum tanıma**: bir konumun belirli bir uzaklıkta yer alan ilgi alanlarını arar. Azure haritalar [yakın arama](/rest/api/maps/search/getsearchnearby) API 'si, bu API 'ye benzer.
-   **Yerel Öngörüler:** Belirli bir koordinat süresi boyunca belirtilen en uzun süre içinde olan ilgi alanlarını arar. Bu, önce bir ısovaone hesaplanarak ve ardından [geometri API içinde aramaya](/rest/api/maps/search/postsearchinsidegeometry) geçirilerek Azure Maps ile ulaşılabilir.

Azure Maps, ilgi noktaları için çeşitli arama API 'Leri sağlar:

-   [POI arama](/rest/api/maps/search/getsearchpoi): ada göre ilgi alanları için arama yapın. Örneğin; `"starbucks"`.
-   [POI kategorisi arama](/rest/api/maps/search/getsearchpoicategory): kategoriye göre ilgi alanları için arama yapın. Örneğin; "Restoran".
-   [Yakın arama](/rest/api/maps/search/getsearchnearby): bir konumun belirli bir uzaklıkta yer alan ilgi alanlarını arar.
-   [Benzer arama](/rest/api/maps/search/getsearchfuzzy): Bu API, adres coğrafi kodlamasını ilgi çekici arama noktasıyla birleştirir. Bu API, bir adres, yer, yer işareti, ilgi alanı veya ilgi alanı kategorisi olabilecek bir serbest biçimli dize alır ve isteği hemen işleyebilir. Bu API, kullanıcıların aynı metin kutusundan adres veya ilgi çekici noktaları araybilecekleri uygulamalar için önerilir.
-   [Geometri dahilinde ara](/rest/api/maps/search/postsearchinsidegeometry): belirtilen geometri (çokgen) içinde olan ilgi alanlarını arayın.
-   [Yol boyunca ara](/rest/api/maps/search/postsearchalongroute): belirtilen yol yolu boyunca ilgi alanları için arama yapın.
-   [Benzer toplu işlem araması](/rest/api/maps/search/postsearchfuzzybatchpreview): en fazla 10.000 adres, yer, yer işareti veya ilgi alanı içeren ve bunların bir süre içinde işlenmesini içeren bir istek oluşturun. Tüm veriler sunucuda paralel olarak işlenir ve tamamlandığında tam sonuç kümesi indirilecektir.

[Arama belgeleri için en iyi uygulamaları](./how-to-use-best-practices-for-search.md) gözden geçirdiğinizden emin olun.

## <a name="get-traffic-incidents"></a>Trafik olaylarını al

Azure Maps trafik verilerini almak için çeşitli API 'Ler sağlar. İki tür trafik verisi mevcuttur;

* **Akış verileri** : yolların bölümlerine trafik akışı hakkında ölçümler sağlar. Bu, genellikle kod yollar için kullanılır. Bu veriler her 2 dakikada bir güncelleştirilir.
* **Olay verileri** : oluşturma, yol kapanışları, kazalardan dolayı ve trafiği etkileyebilecek diğer olaylar hakkında veri sağlar. Bu veriler her dakikada güncellenir.

Bing Haritalar, etkileşimli harita denetimlerinde trafik akışı ve olay verileri sağlar ve ayrıca olay verilerini bir hizmet olarak kullanılabilir hale getirir.

Trafik verileri de Azure Maps etkileşimli harita denetimleriyle tümleşiktir. Azure haritalar Ayrıca aşağıdaki trafik Hizmetleri API 'Lerini de sağlar;

-   [Trafik akışı kesimleri](/rest/api/maps/traffic/gettrafficflowsegment): verilen koordinatlara en yakın yol parçasının hızları ve seyahat zamanları hakkında bilgi sağlar.
-   [Trafik akışı kutucukları](/rest/api/maps/traffic/gettrafficflowtile): trafik akışı verileri içeren raster ve vektör kutucukları sağlar. Bunlar, Azure Maps denetimleriyle veya leaflet gibi üçüncü taraf eşleme denetimlerinde kullanılabilir. Vektör kutucukları, gelişmiş veri analizi için de kullanılabilir.
-   [Trafik olayı ayrıntıları](/rest/api/maps/traffic/gettrafficincidentdetail): bir sınırlayıcı kutu, yakınlaştırma düzeyi ve trafik modeli içinde olan trafik olay ayrıntılarını sağlar.
-   [Trafik olayı kutucukları](/rest/api/maps/traffic/gettrafficincidenttile): trafik olay verileri içeren raster ve vektör kutucukları sağlar.
-   [Trafik olayı Görünüm penceresi](/rest/api/maps/traffic/gettrafficincidentviewport): istekte tanımlanan görünüm penceresinin, trafık modeli kimliği gibi yasal ve teknik bilgileri alır.

Aşağıdaki tabloda, Bing Haritalar trafiği API parametreleri, Azure haritalar 'daki karşılaştırılabilir trafik olayı ayrıntıları API parametreleri ile çapraz başvuru yapılır.

| Bing Haritalar API parametresi  | Karşılaştırılabilir Azure Maps API parametresi   |
|--------------------------|---------------------------------------|
| `mapArea`                | `boundingBox` ve `boundingZoom`      |
| `includeLocationCodes`   | Yok                                   |
| `severity` (`s`)         | Yok – döndürülen tüm veriler               |
| `type` (`t`)             | Yok – döndürülen tüm veriler               |
| `key`                    | `subscription-key` – Ayrıca bkz. [Azure Maps Ile kimlik doğrulama](./azure-maps-authentication.md) belgeleri. |
| `culture` (`c`)          | `language` – [Desteklenen diller](./supported-languages.md) belgesine bakın. |
| `userRegion` (`ur`)      | `view` – [Desteklenen görünümler](./supported-languages.md#azure-maps-supported-views) belgeleri bölümüne bakın. |

## <a name="get-a-time-zone"></a>Saat dilimi al

Azure Maps, bir koordinat içindeki zaman dilimini almak için bir API sağlar. Azure haritalar Saat dilimi API 'SI, Bing Haritalar 'daki saat dilimi API 'siyle karşılaştırılabilir;

-   [Koordine eden saat dilimi](/rest/api/maps/timezone/gettimezonebycoordinates): bir koordinat belirtin ve bulunduğu saat dilimiyle ilgili ayrıntıları alın.

Aşağıdaki tabloda, Bing Haritalar API parametreleri Azure haritalar 'daki karşılaştırılabilir API parametreleriyle birlikte çapraz başvuru yapılır.

| Bing Haritalar API parametresi | Karşılaştırılabilir Azure Maps API parametresi          |
|-------------------------|----------------------------------------------|
| `point`                 | `query`                                      |
| `query`                 | N/A-konumlar önce coğrafi kodlaması yapılır olmalıdır.      |
| `dateTime`              | `timeStamp`                                  |
| `includeDstRules`       | Yok – Azure Maps tarafından her zaman yanıt olarak dahil edilen. |
| `key`                   | `subscription-key` – Ayrıca bkz. [Azure Maps Ile kimlik doğrulama](./azure-maps-authentication.md) belgeleri. |
| `culture` (`c`)         | `language` – [Desteklenen diller](./supported-languages.md) belgesine bakın.  |
| `userRegion` (`ur`)     | `view` – [Desteklenen görünümler](./supported-languages.md#azure-maps-supported-views) belgeleri bölümüne bakın.  |

Buna ek olarak, Azure Maps Platformu, saat dilimi adları ve kimlikleri ile Dönüştürmelere yardımcı olmak için çeşitli ek saat dilimi API 'Leri de sağlar;

-   [Kimliğe göre saat dilimi](/rest/api/maps/timezone/gettimezonebyid): belirtilen IANA saat dilimi kimliği için geçerli, geçmiş ve gelecekteki saat dilimi bilgilerini döndürür.
-   [Bölge enum IANA](/rest/api/maps/timezone/gettimezoneenumiana): bir IANA saat dilimi kimliklerinin tam listesini döndürür. IANA hizmetine yapılan güncelleştirmeler sisteme bir gün içinde yansıtılır. 
-   [Zaman dilimi numaralandırması pencereleri](/rest/api/maps/timezone/gettimezoneenumwindows): Windows saat dilimi kimliklerinin tam listesini döndürür.
-   [Saat dılımı IANA sürümü](/rest/api/maps/timezone/gettimezoneianaversion): Azure haritalar tarafından kullanılan geçerli IANA sürüm numarasını döndürür. 
-   [IANA Için saat dilimi pencereleri](/rest/api/maps/timezone/gettimezonewindowstoiana): geçerli bir Windows saat dilimi kimliği verilen ılgılı bır IANA kimliğini döndürür. Tek bir Windows KIMLIĞI için birden çok ıANA kimliği döndürülebilir.

## <a name="spatial-data-services-sds"></a>Uzamsal veri Hizmetleri (SDS)

Bing Haritalar 'daki uzamsal veri Hizmetleri üç temel işlev sunar:

* Batch coğrafi kodlama – tek bir istekle birlikte büyük bir adres coğrafi kod grubu Işleyin.
* Yönetim sınırı verilerini alma – bir koordinat kullanın ve belirtilen varlık türü için bir kesişme sınırı alın.
* Konak ve sorgu uzamsal iş verileri – basit bir 2B veri tablosu yükleyin ve birkaç basit uzamsal sorgu kullanarak bu verilere erişin.

### <a name="batch-geocode-data"></a>Toplu iş coğrafi kod verileri

Toplu coğrafi kodlama, çok sayıda adres veya konum alma, bunları bir hizmete tek bir istekte geçirme ve bunların tümünün paralel ve tek bir yanıtta döndürdüğü sonuçlara sahip olma işlemidir.

Bing Haritalar, tek bir toplu iş coğrafi kod isteğinde 200.000 ' e kadar adres geçirilmesini sağlar. Bu istek bir sıraya gider ve genellikle bir süre boyunca işlenir ve veri kümesinin boyutuna ve hizmetin yüküne bağlı olarak birkaç dakika ile birkaç saat arasında işlem olur. İstekteki her adres bir işlem oluşturdu.

Azure haritalar toplu bir coğrafi kodlama hizmetine sahiptir, ancak tek bir istekte 10.000 ' e kadar adrese geçirilir ve veri kümesinin boyutuna ve hizmetin yüküne bağlı olarak birkaç dakika sonra Saniyeler içinde işlenir. İstekteki her adres bir işlem oluşturdu. Azure haritalar 'da toplu iş coğrafi kodlama hizmeti yalnızca S1 katmanını kullanabilir.

Azure Maps ile büyük bir sayı adresi olan geokodlamaya yönelik başka bir seçenek de standart arama API 'Lerine paralel istekler yapmına yöneliktir. Bu hizmetler istek başına yalnızca tek bir adres kabul eder, ancak aynı zamanda boş kullanım sınırları sağlayan S0 katmanıyla birlikte kullanılabilir. S0 katmanı, saniyede en fazla 50 istek ve Azure Maps Platformu için tek bir hesaptan izin verir. Bu nedenle 180.000, bu sınırı aşmaya devam etmek için bunları sınırlayın S1 katmanı, bir hesaptan gerçekleştirilen sorgu sayısı üzerinde belgelenmiş bir sınıra sahip değildir, bu nedenle bu fiyatlandırma katmanı kullanılırken çok daha fazla veri işlenebilir. ancak Batch coğrafi kodlama hizmetini kullanmak, aktarılan toplam veri miktarını azaltmaya yardımcı olur ve ağ trafiğini büyük ölçüde azaltır.

-   [Serbest biçimli adres coğrafi kodlama](/rest/api/maps/search/getsearchaddress): tek bir adres dizesi (gibi) belirtip `"1 Microsoft way, Redmond, WA"` isteği hemen işleyin. Bu hizmet, tek tek adreslere hızla coğrafi olarak kod eklemeniz gerekiyorsa önerilir.
-   [Yapılandırılmış adres coğrafi kodlama](/rest/api/maps/search/getsearchaddressstructured): sokak adı, şehir, ülke ve posta kodu gibi tek bir adresin parçalarını belirtin ve isteği hemen işleyin. Bu hizmet, tek tek adreslerin hızlı bir şekilde coğrafi olarak kodılabilmesi ve verilerin zaten ayrı adres bölümlerine ayrıştırılabilmeniz durumunda önerilir.
-   [Toplu iş adresi coğrafi kodlama](/rest/api/maps/search/postsearchaddressbatchpreview): en fazla 10.000 adresi içeren ve bunların bir süre içinde işlenmesini içeren bir istek oluşturun. Tüm adresler, sunucuda paralel olarak coğrafi olarak kodlanmıştır ve tamamlandığında tam sonuç kümesi indirilecektir. Bu hizmet, büyük veri kümelerine coğrafi kodlama için önerilir.
-   [Benzer arama](/rest/api/maps/search/getsearchfuzzy): Bu API, adres coğrafi kodlamasını ilgi çekici arama noktasıyla birleştirir. Bu API, bir adres, yer, yer işareti, ilgi alanı veya ilgi alanı kategorisi olabilecek bir serbest biçimli dize alır ve isteği hemen işleyebilir. Bu API, kullanıcıların aynı metin kutusundan adres veya ilgi çekici noktaları araybilecekleri uygulamalar için önerilir.
-   [Benzer toplu işlem araması](/rest/api/maps/search/postsearchfuzzybatchpreview): en fazla 10.000 adres, yer, yer işareti veya ilgi alanı içeren ve bunların bir süre içinde işlenmesini içeren bir istek oluşturun. Tüm veriler sunucuda paralel olarak işlenir ve tamamlandığında tam sonuç kümesi indirilecektir.

### <a name="get-administrative-boundary-data"></a>Yönetim sınırı verilerini al

Bing Haritalar 'da, ülke, eyalet, ilçe, şehir ve posta kodlarına ait yönetim sınırları coğrafi veri API 'SI aracılığıyla kullanılabilir hale getirilir. Bu API, geocode için bir koordinat ya da sorgu alır. Bir sorgu geçirilirse, coğrafi olarak kodlanmıştır ve ilk sonuçtan koordinatları kullanılır. Bu API koordinatları alır ve koordinat kesiştiği belirtilen varlık türünün sınırını alır. Bu API 'nin geçirilmiş sorgu için sınır döndürmediğini unutmayın. İçin bir sorgu `"Seattle, WA"` geçirilir, ancak varlık türü değeri ülke bölgesi olarak ayarlanırsa, USA için sınır döndürülür.

Azure haritalar ayrıca yönetim sınırlarına (ülkeler, durumlar, ilçeler, şehirler ve posta kodları) erişim sağlar. Bir sınır almak için, istediğiniz sınır için arama API 'Lerinden birini (ör.) sorgumalısınız `Seattle, WA` . Arama sonucunun ilişkili bir sınırı varsa, sonuç yanıtında bir geometri KIMLIĞI sağlanacaktır. Daha sonra arama Çokgen API 'SI, bir veya daha fazla geometri kimliği için tam sınırları almak üzere kullanılabilir. Azure haritalar, belirtilen bir koordinat içinde belirtilen bir varlık türü için sınır döndürdüğünden, Bing Haritalar 'dan biraz farklıdır. Ayrıca, Azure Maps tarafından döndürülen sınır verileri GeoJSON biçimindedir.

Yeniden üst sınır:

1.  Aşağıdaki arama API 'Lerinden birine almak istediğiniz sınır için bir sorgu geçirin.
    -   [Serbest biçimli adres coğrafi kodlama](/rest/api/maps/search/getsearchaddress)
    -   [Yapılandırılmış adres coğrafi kodlama](/rest/api/maps/search/getsearchaddressstructured)
    -   [Toplu iş adresi coğrafi kodlama](/rest/api/maps/search/postsearchaddressbatchpreview)
    -   [Belirsiz arama](/rest/api/maps/search/getsearchfuzzy)
    -   [Benzer toplu işlem araması](/rest/api/maps/search/postsearchfuzzybatchpreview)
2.  İstenen sonuçlardan bir geometri KIMLIĞI varsa, bunu [arama Çokgen API](/rest/api/maps/search/getsearchpolygon)'sine geçirin.

### <a name="host-and-query-spatial-business-data"></a>Konak ve sorgu uzamsal iş verileri

Bing Haritalar 'daki uzamsal veri Hizmetleri, iş verilerini barındırmak ve bunu bir uzamsal REST hizmeti olarak ortaya çıkarmak için basit bir uzamsal veri depolama çözümü sağlar. Bu hizmet dört ana sorgu sağlar; özelliğe göre bul, yakındaki bul, sınırlayıcı halinde bulma ve 1 mil bir rota ile bulma. Bu hizmeti kullanan birçok şirket, genellikle iş verileri zaten bir veritabanında depolanmaktadır ve mağaza bulleyicileri gibi güç uygulamalarına bu hizmete daha küçük bir alt kümesini karşıya yüklüyor. Anahtar tabanlı kimlik doğrulaması temel güvenlik sağladığından bu hizmetin yalnızca genel kullanıma yönelik verilerle kullanılması önerilir.

Birçok iş konumu verisi bir veritabanında başlatılır. Bu nedenle, Azure SQL veya Azure PostgreSQL gibi mevcut Azure depolama çözümlerini (PostGIS eklentisi ile) kullanmanız önerilir. Bu depolama çözümlerinin her ikisi de uzamsal verileri destekler ve zengin bir uzamsal sorgulama özellikleri kümesi sağlar. Verileriniz uygun bir depolama çözümünden sonra, özel bir Web hizmeti oluşturarak ya da ASP.NET veya Entity Framework gibi bir çerçeve kullanarak uygulamanızla tümleştirilebilir. Bu yaklaşımı kullanmak daha fazla sorgulama özelliği ve çok daha yüksek güvenlik seçenekleri sağlar.

Azure Cosmos DB, senaryonuza bağlı olarak sınırlı sayıda uzamsal özelliği de sağlar.

Azure 'da uzamsal verileri barındırmak ve sorgulamak için bazı faydalı kaynaklar aşağıda verilmiştir.

-   [Azure SQL uzamsal veri türlerine genel bakış](/sql/relational-databases/spatial/spatial-data-types-overview)
-   [Azure SQL uzamsal – en yakın komşu sorgulayın](/sql/relational-databases/spatial/query-spatial-data-for-nearest-neighbor)
-   [Azure Cosmos DB Jeo uzamsal özelliklerine genel bakış](../cosmos-db/sql-query-geospatial-intro.md)

## <a name="client-libraries"></a>İstemci kitaplıkları

Azure Maps, aşağıdaki programlama dilleri için istemci kitaplıkları sağlar;

-   JavaScript, TypeScript, Node.js – [belge](./how-to-use-services-module.md) \| [NPM paketi](https://www.npmjs.com/package/azure-maps-rest)

Diğer programlama dilleri için açık kaynaklı istemci kitaplıkları;

* .NET Standard 2,0 – [GitHub projesi](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet paketi](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Temizlenecek kaynak yok.

## <a name="next-steps"></a>Sonraki adımlar

Azure haritalar REST hizmetleri hakkında daha fazla bilgi edinin.

> [!div class="nextstepaction"]
> [Arama hizmetini kullanmaya yönelik en iyi uygulamalar](how-to-use-best-practices-for-search.md)
