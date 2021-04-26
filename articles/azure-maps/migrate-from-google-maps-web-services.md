---
title: Öğretici-Google Maps 'tan Web hizmetlerini geçirme | Microsoft Azure haritaları
description: Google Maps 'tan Microsoft Azure Maps 'a Web Hizmetleri geçirme öğreticisi
author: rbrundritt
ms.author: richbrun
ms.date: 08/19/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: d2b80dfa091d6a2b892c8baf8e9c35084c5bee4e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076397"
---
# <a name="tutorial-migrate-web-service-from-google-maps"></a>Öğretici: Google Maps 'tan Web hizmeti geçirme

Hem Azure hem de Google Maps, REST Web Hizmetleri aracılığıyla uzamsal API 'lere erişim sağlar. Bu platformların API arabirimleri benzer işlevleri gerçekleştirir. Ancak, bunların her biri farklı adlandırma kuralları ve yanıt nesneleri kullanır.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * İleri ve ters coğrafi kodlama
> * İlgi noktası arama
> * Rotaları ve yönleri hesapla
> * Harita görüntüsünü al
> * Uzaklık matrisini hesaplama
> * Saat dilimi ayrıntılarını al

Ayrıca şunları öğreneceksiniz:

> [!div class="checklist"]
> * Google Maps Web hizmetinden geçiş yaparken hangi Azure Maps REST hizmeti
> * Azure haritalar hizmetlerinden en iyi şekilde yararlanmanıza yönelik ipuçları
> * Diğer ilgili Azure haritalar hizmetleri hakkında Öngörüler

Tablo, listelenen Google Maps hizmeti API 'Lerinde benzer işlevlere sahip Azure Maps hizmeti API 'Lerini gösterir.

| Google Maps hizmeti API 'SI | Azure haritalar hizmeti API 'SI                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Yönergeler              | [Yolu](/rest/api/maps/route)                                     |                         
| Uzaklık matrisi         | [Yol matrisi](/rest/api/maps/route/postroutematrixpreview)       |                         
| Coğrafi Kodlama               | [Ara](/rest/api/maps/search)                                   |                         
| Konum arama           | [Ara](/rest/api/maps/search)                                   |                         
| Otomatik tamamlamayı yerleştir      | [Ara](/rest/api/maps/search)                                   |                         
| Yola yasla            | Bkz. [rotaları ve yönleri hesaplama](#calculate-routes-and-directions) bölümü.  |        
| Hız sınırları            | Bkz. [bir koordinat Için ters coğrafi kod](#reverse-geocode-a-coordinate) bölümü.         |         
| Statik eşleme              | [İşleme](/rest/api/maps/render/getmapimage)                       |                         
| Saat Dilimi               | [Saat dilimi](/rest/api/maps/timezone)                              |                         
| Nedeniyle               | [Yükseltme (Önizleme)](/rest/api/maps/elevation)                   | 

Aşağıdaki hizmet API 'Leri Şu anda Azure haritalar 'da kullanılabilir değildir:

- Coğrafi konum-Azure haritaları coğrafi konum olarak adlandırılan bir hizmete sahiptir, ancak konum bilgilerine IP adresi sağlar, ancak şu anda Cell Tower veya WiFi çapraz yerleşimini desteklemez.
- Ayrıntılar ve fotoğraflar-telefon numaralarını ve Web sitesi URL 'sini Azure haritalar arama API 'sinde bulabilirsiniz.
- Harita URL 'Leri
- En yakın yollar-bu, [burada](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Basic%20snap%20to%20road%20logic)gösterildiği gıbı Web SDK kullanılarak ulaşılabilir, ancak şu anda hizmet olarak kullanılamaz.
- Statik cadde görünümü

Azure haritalar, ilgi çekici olabilecek birkaç ek REST Web hizmetine sahiptir:

- [Uzamsal işlemler](/rest/api/maps/spatial): bir hizmete bölge sınırlaması gibi karmaşık uzamsal hesaplamalar ve işlemler için yük boşaltma.
- [Trafik](/rest/api/maps/traffic): gerçek zamanlı trafik akışına ve olay verilerine erişin.

## <a name="prerequisites"></a>Önkoşullar

1. [Azure portalında](https://portal.azure.com) oturum açın. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/) oluşturun.
2. [Azure haritalar hesabı oluşturma](quick-demo-map-app.md#create-an-azure-maps-account)
3. Birincil anahtar veya abonelik anahtarı olarak da bilinen [birincil bir abonelik anahtarı alın](quick-demo-map-app.md#get-the-primary-key-for-your-account). Azure haritalar 'da kimlik doğrulaması hakkında daha fazla bilgi için bkz. [Azure haritalar 'da kimlik doğrulamasını yönetme](how-to-manage-authentication.md).

## <a name="geocoding-addresses"></a>Coğrafi kodlama adresleri

Coğrafi kodlama, bir adresi bir koordinat içine dönüştürme işlemidir. Örneğin, "1 Microsoft Way, Redmond, WA", Boylam:-122,1298, Enlem: 47,64005 olarak dönüştürülür. Ardından, koordinatları farklı tür amaçlar için kullanılabilir (örneğin, bir haritada bir işaret ortalama konumlandırmayı).

Azure Maps, coğrafi kodlama adresleri için çeşitli yöntemler sağlar:

- [**Serbest biçimli adres coğrafi kodlama**](/rest/api/maps/search/getsearchaddress): tek bir adres dizesi belirtip isteği hemen işleyin. "1 Microsoft Way, Redmond, WA" tek bir adres dizesinin bir örneğidir. Bu API, tek tek adreslere hızla coğrafi olarak kod eklemeniz gerekiyorsa önerilir.
- [**Yapılandırılmış adres coğrafi kodlama**](/rest/api/maps/search/getsearchaddressstructured): sokak adı, şehir, ülke/bölge ve posta kodu gibi tek bir adresin parçalarını belirtin ve isteği hemen işleyin. Bu API, tek tek adreslerin hızlı bir şekilde coğrafi olarak kodılabilmesi ve verilerin zaten ayrı adres bölümlerine ayrıştırılabilmeniz halinde önerilir.
- [**Toplu iş adresi coğrafi kodlama**](/rest/api/maps/search/postsearchaddressbatchpreview): en fazla 10.000 adresi içeren ve bunların bir süre içinde işlenmesini içeren bir istek oluşturun. Tüm adresler, sunucuda paralel olarak coğrafi olarak kodlanmıştır ve tamamlandığında tam sonuç kümesi indirilecektir. Bu, büyük veri kümelerine coğrafi kodlama için önerilir.
- [**Benzer arama**](/rest/api/maps/search/getsearchfuzzy): Bu API, adres coğrafi kodlamasını ilgi çekici arama noktasıyla birleştirir. Bu API, serbest biçimli bir dize alır. Bu dize bir adres, yer, yer işareti, ilgi noktası veya ilgi alanı kategorisi olabilir. Bu API, isteği gerçek zamanlı olarak işler. Bu API, kullanıcıların aynı metin kutusundaki adresleri veya ilgi noktalarını araytığına yönelik uygulamalar için önerilir.
- [**Benzer toplu işlem araması**](/rest/api/maps/search/postsearchfuzzybatchpreview): en fazla 10.000 adres, yer, yer işareti veya ilgi alanı içeren ve bunların bir süre içinde işlenmesini içeren bir istek oluşturun. Tüm veriler sunucuda paralel olarak işlenir ve tamamlandığında tam sonuç kümesi indirilecektir.

Aşağıdaki tabloda, Google Maps API parametreleri Azure haritalar 'daki karşılaştırılabilir API parametreleriyle birlikte çapraz başvuru yapılır.

| Google Maps API parametresi | Karşılaştırılabilir Azure Maps API parametresi  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` ve `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality` -City/Town<br/>`municipalitySubdivision` – Komşuları, alt/süper şehir<br/>`countrySubdivision` -Eyalet veya il<br/>`countrySecondarySubdivision` -İlçe<br/>`countryTertiarySubdivision` -District<br/>`countryCode` -iki harfli ülke/bölge kodu |
| `key`                       | `subscription-key` – Ayrıca bkz. [Azure Maps Ile kimlik doğrulama](azure-maps-authentication.md) belgeleri. |
| `language`                  | `language` – [Desteklenen diller](supported-languages.md) belgesine bakın.  |
| `region`                    | `countrySet`                       |

Arama hizmetinin nasıl kullanılacağına ilişkin bir örnek [burada](how-to-search-for-address.md)belgelenmiştir. [Arama için en iyi yöntemleri](how-to-use-best-practices-for-search.md)gözden geçirdiğinizden emin olun.

> [!TIP]
> Serbest biçimli adres coğrafi kodlama ve benzer arama API 'Leri, istek URL 'sine eklenerek otomatik tamamlama modunda kullanılabilir `&typeahead=true` . Bu, sunucuya giriş metninin büyük olasılıkla kısmi olduğunu ve aramanın tahmine dayalı moda gitmesini söyler.

## <a name="reverse-geocode-a-coordinate"></a>Bir koordinatı tersine çevrilmiş coğrafi kod

Ters coğrafi kodlama, coğrafi koordinatları yaklaşık bir adrese dönüştürme işlemidir. "Boylam:-122,1298, Latitude: 47,64005" ile koordinatları "1 Microsoft Way, Redmond, WA" olarak dönüştürür.

Azure haritalar, birkaç ters coğrafi kodlama yöntemi sağlar:

- [**Adres ters coğrafi bölge**](/rest/api/maps/search/getsearchaddressreverse): Bu koordinat ile ilgili yaklaşık adresi almak için tek bir coğrafi koordinat belirtin. İsteği gerçek zamanlı olarak işler.
- [**Çapraz cadde ters Geocoder**](/rest/api/maps/search/getsearchaddressreversecrossstreet): yakın çapraz cadde bilgilerini almak ve isteği hemen işlemek için tek bir coğrafi koordinat belirtin. Örneğin, aşağıdaki çapraz cadde 1 ' i ve ana St 'ı alabilirsiniz.
- [**Batch adresi ters coğrafi makinesi**](/rest/api/maps/search/postsearchaddressreversebatchpreview): en fazla 10.000 koordinat içeren bir istek oluşturun ve bunların bir süre içinde işlenmesini sağlayabilirsiniz. Tüm veriler, sunucuda paralel olarak işlenir. İstek tamamlandığında, tüm sonuç kümesini indirebilirsiniz.

Bu tablo, Google Maps API parametrelerine Azure haritalar 'daki karşılaştırılabilir API parametreleriyle çapraz başvurular.

| Google Maps API parametresi   | Karşılaştırılabilir Azure Maps API parametresi   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` – Ayrıca bkz. [Azure Maps Ile kimlik doğrulama](azure-maps-authentication.md) belgeleri. |
| `language`                  | `language` – [Desteklenen diller](supported-languages.md) belgesine bakın.  |
| `latlng`                    | `query`  |
| `location_type`             | *yok*     |
| `result_type`               | `entityType`    |

[Arama için en iyi uygulamaları](how-to-use-best-practices-for-search.md)gözden geçirin.

Azure haritalar ters coğrafi kodlama API 'SI, Google Maps ' de kullanılamayan bazı ek özelliklere sahiptir. Uygulamanızı geçirirken, bu özellikler uygulamanızla tümleştirilecek şekilde yararlı olabilir:

* Hız sınırı verilerini alma
* Yol kullanım bilgilerini alma: yerel yol, arterial, sınırlı erişim, rampa, vb.
* Bir koordinat bulunduğu cadde tarafını alma

## <a name="search-for-points-of-interest"></a>İlgi noktası arama

Yer arama API 'sini kullanarak Google Haritalar 'da ilgi çekici verilerin bulunduğu noktaya erişilebilir. Bu API, ilgilendiğiniz noktaları aramak için üç farklı yol sağlar:

* **Metinden yer bulun:** Adını, adresini veya telefon numarasını temel alarak bir ilgi alanı arar.
* **Yakın arama**: bir konumun belirli bir uzaklıkta yer alan ilgi alanlarını arar.
* **Metin arama:** Bir dizi ilgi ve konum bilgisi içeren serbest biçimli bir metin kullanarak yerleri arar. Örneğin, "New York 'ta pizza" veya "ana St yakınında" Restoranlar ".

Azure Maps, ilgi noktaları için çeşitli arama API 'Leri sağlar:

- [**POI arama**](/rest/api/maps/search/getsearchpoi): ada göre ilgi alanları için arama yapın. Örneğin, "Starbuları".
- [**POI kategorisi arama**](/rest/api/maps/search/getsearchpoicategory): kategoriye göre ilgi alanları için arama yapın. Örneğin, "Restoran".
- [**Yakın arama**](/rest/api/maps/search/getsearchnearby): bir konumun belirli bir uzaklıkta yer alan ilgi alanlarını arar.
- [**Benzer arama**](/rest/api/maps/search/getsearchfuzzy): Bu API, adres coğrafi kodlamasını ilgi çekici arama noktasıyla birleştirir. Bu API, bir adres, yer, yer işareti, ilgi noktası veya ilgi alanı kategorisi olabilecek serbest biçimli bir dize alır. İstek neredeyse gerçek zamanlı olarak işlenir. Bu API, kullanıcıların aynı metin kutusundaki adresleri veya ilgi noktalarını araytığına yönelik uygulamalar için önerilir.
- [**Geometri dahilinde ara**](/rest/api/maps/search/postsearchinsidegeometry): belirtilen geometri dahilinde ilgi alanları için arama yapın. Örneğin, bir çokgen içinde ilgi çekici bir noktada arama yapın.
- [**Yol boyunca ara**](/rest/api/maps/search/postsearchalongroute): belirtilen yol yolu boyunca ilgi alanları için arama yapın.
- [**Benzer toplu işlem araması**](/rest/api/maps/search/postsearchfuzzybatchpreview): en fazla 10.000 adres, yer, yer işareti veya ilgi alanı içeren bir istek oluşturun. İstek bir süre içinde işlendi. Tüm veriler, sunucuda paralel olarak işlenir. İstek işlemi tamamlandığında, tüm sonuç kümesini indirebilirsiniz.

Şu anda Azure Maps 'ta, Google Maps 'ta metin arama API 'sine yönelik karşılaştırılabilir bir API yok.

> [!TIP]
> POı araması, POı kategorisi araması ve benzer arama API 'Leri, istek URL 'sine eklenerek otomatik tamamlama modunda kullanılabilir `&typeahead=true` . Bu, sunucuya giriş metninin büyük olasılıkla kısmi olduğunu bildirir. API, tahmine dayalı modda aramayı yürütmek için kullanılır.

Arama belgeleri [için en iyi uygulamaları](how-to-use-best-practices-for-search.md) gözden geçirin.

### <a name="find-place-from-text"></a>Metinden yer bulun

Ad veya adrese göre ilgi alanlarını aramak için Azure Maps [POI aramasını](/rest/api/maps/search/getsearchpoi) ve [benzer aramayı](/rest/api/maps/search/getsearchfuzzy) kullanın.

Tabloda, Google Maps API parametrelerine benzer Azure Maps API parametreleri ile çapraz başvurular yapılır.

| Google Maps API parametresi | Karşılaştırılabilir Azure Maps API parametresi |
|---------------------------|-------------------------------------|
| `fields`                  | *yok*                               |
| `input`                   | `query`                             |
| `inputtype`               | *yok*                               |
| `key`                     | `subscription-key` – Ayrıca bkz. [Azure Maps Ile kimlik doğrulama](azure-maps-authentication.md) belgeleri. |
| `language`                | `language` – [Desteklenen diller](supported-languages.md) belgesine bakın.  |
| `locationbias`            | `lat``lon`ve`radius`<br/>`topLeft` ve `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Yakındaki arama

Azure haritalar 'da, yakın ilgi noktalarını almak için [yakındaki arama](/rest/api/maps/search/getsearchnearby) API 'sini kullanın.

Tablo, Google Maps API parametrelerini karşılaştırılabilir Azure Maps API parametreleriyle gösterir.

| Google Maps API parametresi | Karşılaştırılabilir Azure Maps API parametresi  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key` – Ayrıca bkz. [Azure Maps Ile kimlik doğrulama](azure-maps-authentication.md) belgeleri. |
| `keyword`                   | `categorySet` ve `brandSet`        |
| `language`                  | `language` – [Desteklenen diller](supported-languages.md) belgesine bakın.  |
| `location`                  | `lat` ve `lon`                     |
| `maxprice`                  | *yok*                               |
| `minprice`                  | *yok*                               |
| `name`                      | `categorySet` ve `brandSet`        |
| `opennow`                   | *yok*                               |
| `pagetoken`                 | `ofs` ve `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *yok*                               |
| `type`                      | `categorySet –` Bkz. [desteklenen arama kategorileri](supported-search-categories.md) belgeleri.   |

## <a name="calculate-routes-and-directions"></a>Rotaları ve yönleri hesapla

Azure haritalar 'ı kullanarak rotaları ve yönergeleri hesaplayın. Azure haritalar, Google Maps yönlendirme hizmetiyle aynı işlevselliklerin çoğuna sahiptir, örneğin:

* Varış ve ayrılma süreleri.
* Gerçek zamanlı ve tahmine dayalı trafik rotaları.
* Farklı ulaşım modları. Örneğin, gidiş, yürüyen, Bicycling.

> [!NOTE]
> Azure haritalar tüm waypoints 'in koordinatlarını gerektirir. Önce adreslerin coğrafi kodlanmış olması gerekir.

Azure haritalar yönlendirme hizmeti, yolları hesaplamak için aşağıdaki API 'Leri sağlar:

- [**Rotayı hesapla**](/rest/api/maps/route/getroutedirections): bir rotayı hesaplayın ve isteğin hemen işlenmesini sağlayabilirsiniz. Bu API hem GET hem POST isteklerini destekler. Çok sayıda waypoints belirtildiğinde veya URL isteğinin çok uzun olmadığından ve sorunlara yol açmamasına yetecek kadar fazla yol seçeneği kullanılırken POST istekleri önerilir. Azure haritalar 'daki POST rotası yönü, binlerce [destekleyici noktası](/rest/api/maps/route/postroutedirections#supportingpoints) olan bir seçeneğe sahiptir ve bunları aralarında bir mantıksal yol yolu (yola yasla) yeniden oluşturmak için kullanır. 
- [**Batch rotası**](/rest/api/maps/route/postroutedirectionsbatchpreview): en fazla 1.000 yol isteği içeren bir istek oluşturun ve bunların bir süre içinde işlenmesini isteyin. Tüm veriler sunucuda paralel olarak işlenir ve tamamlandığında tam sonuç kümesi indirilecektir.
- [* * Mobility Hizmetleri (Önizleme) * *](/rest/api/maps/mobility): genel aktarım kullanarak rotaları ve yönergeleri hesaplayın.

Tablo çapraz başvuruları, Google Maps API parametreleri ile Azure haritalar 'daki karşılaştırılabilir API parametrelerini içeren çapraz başvurular.

| Google Maps API parametresi    | Karşılaştırılabilir Azure Maps API parametresi  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query` – biçimde koordinatları `"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key` – Ayrıca bkz. [Azure Maps Ile kimlik doğrulama](azure-maps-authentication.md) belgeleri. |
| `language`                     | `language` – [Desteklenen diller](supported-languages.md) belgesine bakın.   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *Yok* : Bu özellik, coğrafi kodlama ile ilgilidir. Azure Haritalar Coğrafi Kodlama API 'sini kullanırken *Countryset* parametresini kullanın.  |
| `traffic_model`               | *Yok* – *trafik parametresi yalnızca trafik parametresiyle* birlikte kullanılması gerekiyorsa belirtilebilir. |
| `transit_mode`                | Bkz. [Mobility Hizmetleri (Önizleme) belgeleri](/rest/api/maps/mobility) |
| `transit_routing_preference` | Bkz. [Mobility Hizmetleri (Önizleme) belgeleri](/rest/api/maps/mobility) |
| `units`                        | *Yok* – Azure Maps yalnızca ölçüm sistemini kullanır.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Varsayılan olarak, Azure Maps Route API 'SI yalnızca bir Özet döndürür. Yol yolu için uzaklık ve saat ve koordinatları döndürür. `instructionsType`Açma yönergelerini almak için parametresini kullanın. Ve, `routeRepresentation` Özet ve yol yolunu filtrelemek için parametresini kullanın.

Azure haritalar yönlendirme API 'SI, Google Maps ' de kullanılamayan ek özelliklere sahiptir. Uygulamanızı geçirirken, bu özellikleri kullanmayı göz önünde bulundurun ve bunları yararlı bulabilirsiniz.

* Rota türü desteği: en kısa, en hızlı, trilini ve en çok yakıt etkin.
* Ek seyahat modları için destek: Bus, motocycle, Taxi, kamyon ve Van.
* 150 waypoints desteği.
* Tek bir istekte birden çok seyahat süresi hesaplama; geçmiş trafik, canlı trafik, trafik yok.
* Ek yol türlerinden kaçının: Carpool yol, geri alınamaz yollar, zaten kullanılan yollar.
* Kaçınmak için özel alan belirtin.
* Yolun Ascend olabileceği yükseltmeyi sınırlayın.
* Altyapı belirtimlerine göre rota. Altyapı belirtimlerini ve kalan yakıt veya ücreti temel alan combustion veya elektrik taşıtlar için yolları hesaplayın.
* Ticari araç yönlendirme parametrelerini destekler. Örneğin, araç boyutları, ağırlık, Axler sayısı ve kargo türü.
* Maksimum araç hızını belirtin.

Buna ek olarak, Azure Maps 'taki yönlendirme hizmeti [yönlendirilebilir aralıkları hesaplamayı](/rest/api/maps/route/getrouterange)destekler. Yönlendirilebilir aralıkları hesaplama de ikizde olarak bilinir. Kaynak noktasından herhangi bir yöne doğru bir şekilde gezilecek bir alanı kapsayan bir çokgen oluşturmayı gerektirir. Tüm belirli bir süre veya yakıt veya ücret miktarı altında.

Yönlendirme belgeleri [için en iyi uygulamaları](how-to-use-best-practices-for-routing.md) gözden geçirin.

## <a name="retrieve-a-map-image"></a>Harita görüntüsünü al

Azure Maps, çakışan verilerle statik harita görüntülerini işlemek için bir API sağlar. Azure haritalar 'daki [harita görüntü işleme](/rest/api/maps/render/getmapimagerytile) API 'Si, Google Maps 'taki statik harita API 'siyle karşılaştırılabilir.

> [!NOTE]
> Azure haritalar, "boylam, enlem" biçiminde koordine edilecek merkezi, tüm işaretçiyi ve yol konumlarını gerektirir. Ancak, Google Maps "enlem, Boylam" biçimini kullanır. Önce adreslerin coğrafi kodlanmış olması gerekir.

Tablo çapraz başvuruları, Google Maps API parametreleri ile Azure haritalar 'daki karşılaştırılabilir API parametrelerini içeren çapraz başvurular.

| Google Maps API parametresi | Karşılaştırılabilir Azure Maps API parametresi  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format` – URL yolunun bir parçası olarak belirtilir. Şu anda yalnızca PNG destekleniyor. |
| `key`                       | `subscription-key` – Ayrıca bkz. [Azure Maps Ile kimlik doğrulama](azure-maps-authentication.md) belgeleri. |
| `language`                  | `language` – [Desteklenen diller](supported-languages.md) belgesine bakın.  |
| `maptype`                   | `layer` ve `style` – [desteklenen harita stilleri](supported-map-styles.md) belgelerine bakın. |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *Yok* – bu, coğrafi kodlama ile ilgili bir özelliktir. `countrySet`Azure Maps coğrafi kodlama API 'sini kullanırken parametresini kullanın.  |
| `scale`                     | *yok*                              |
| `size`                      | `width` ve `height` – boyutu 8192x8192 olabilir. |
| `style`                     | *yok*                              |
| `visible`                   | *yok*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> Azure haritalar kutucuk sisteminde, kutucuklar Google Maps ' de kullanılan harita kutucuklarının iki katına alınır. Bu nedenle, Azure haritalar 'daki yakınlaştırma düzeyi değeri, Google Maps ile karşılaştırıldığında Azure Maps 'ta bir yakınlaştırma düzeyi daha yakından görünecektir. Bu farkı dengelemek için, geçirdiğiniz isteklerindeki yakınlaştırma düzeyini azaltın.

Daha fazla bilgi için [harita görüntü Işleme API 'Sindeki nasıl yapılır kılavuzuna](how-to-render-custom-data.md)bakın.

Azure haritalar işleme hizmeti, statik bir harita görüntüsü oluşturmaya ek olarak tarama (PNG) ve vektör biçimindeki harita kutucuklarına doğrudan erişme özelliği sağlar:

- [**Harita kutucuğu**](/rest/api/maps/render/getmaptile): temel haritalar (yollar, sınırlar, arka plan) için raster (png) ve vektör kutucukları alma.
- [**Harita Imagery kutucuğu**](/rest/api/maps/render/getmapimagerytile): havadan ve uydu görüntüsü al kutucukları alın.

> [!TIP]
> Birçok Google harita, etkileşimli eşleme deneyimlerinden birkaç yıl önce statik harita görüntülerine geçti. Bu, maliyet tasarrufu yöntemi olarak gerçekleştirildi. Azure haritalar 'da, Web SDK 'sında etkileşimli harita denetimini kullanmak genellikle daha uygun maliyetli bir hale gelir. Etkileşimli harita denetim ücretleri, kutucuk yüklerinin sayısını temel alarak ücretlidir. Azure haritalar 'daki harita kutucukları büyük. Genellikle, aynı harita görünümünü statik eşleme ile yeniden oluşturmak için yalnızca birkaç kutucuk sürer. Harita kutucukları tarayıcı tarafından otomatik olarak önbelleğe alınır. Bu nedenle, etkileşimli harita denetimi genellikle statik harita görünümü çoğaltma yaparken bir işlemin bir bölümünü oluşturur. Kaydırma ve yakınlaştırma, daha fazla kutucuk yükler; Ancak, bu davranışı devre dışı bırakmak için harita denetiminde seçenekler vardır. Etkileşimli harita denetimi, statik eşleme hizmetlerinden çok daha fazla görselleştirme seçeneği de sağlar.

### <a name="marker-url-parameter-format-comparison"></a>İşaret URL 'SI parametre biçimi karşılaştırması

**Önce: Google Maps**

`markers`URL 'deki parametresini kullanarak işaretçiler ekleyin. `markers`Parametresi bir stili ve aşağıda gösterildiği gibi bu stille birlikte, haritada oluşturulacak konumların bir listesini alır:

```text
&markers=markerStyles|markerLocation1|markerLocation2|...
```

Ek stiller eklemek için, URL 'nin `markers` parametrelerini farklı bir stille ve konum kümesiyle kullanın.

"Enlem, Boylam" biçimiyle işaret konumları belirtin.

`optionName:value` \| Bu "optionName1: değer1 \| optionName2: değer2" gibi kanal () karakterleriyle ayrılmış birden çok stil içeren, biçimiyle işaret stilleri ekleyin. Seçenek adları ve değerleri, iki nokta üst üste (:)) ayrılır. Google Maps 'ta stil işaretçileri için aşağıdaki stil seçeneğini kullanın:

* `color` – Varsayılan işaret simgesinin rengi. 24 bit onaltılı renk ( `0xrrggbb` ) veya aşağıdaki değerlerden biri olabilir; `black` , `brown` , `green` , `purple` , `yellow` , `blue` , `gray` , `orange` `red` `white` ,,.
* `label` – Simgenin üstünde görüntülenecek tek bir büyük harf alfasayısal karakter.
* `size` -İşaretin boyutu. `tiny`, Veya olabilir `mid` `small` .

Google Maps 'teki özel simgeler için aşağıdaki stil seçenekleri adlarını kullanın:

* `anchor` – Simge resminin koordinatya nasıl hizalanacağını belirtir. Bir piksel (x, y) değeri veya aşağıdaki değerlerden biri olabilir; `top`, `bottom` , `left` , `right` , `center` , `topleft` , `topright` , `bottomleft` veya `bottomright` .
* `icon` – Simge resmine işaret eden bir URL.

Örneğin, haritaya Boylam:-110, Enlem: 45 olan kırmızı, orta ölçekli bir işaret ekleyelim:

```text
&markers=color:red|size:mid|45,-110
```

![Google Haritalar işaretçisi](media/migrate-google-maps-web-services/google-maps-marker.png)

**Sonrasında: Azure Maps**

URL 'de parametreyi belirterek statik harita resmine işaretçiler ekleyin `pins` . Google Maps gibi, parametresindeki konumların bir stilini ve bir listesini belirtin. `pins`Parametresi, farklı stillerle işaretçileri desteklemek için birden çok kez belirtilebilir.

```text
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Ek stilleri kullanmak için, `pins` URL 'ye farklı bir stille ve konum kümesiyle ek parametreler ekleyin.

Azure haritalar 'da, PIN konumunun "Boylam Enlem" biçiminde olması gerekir. Google Maps "enlem, Boylam" biçimini kullanır. Bir boşluk değil, bir virgül değil, boylam ve enlem 'yi Azure Maps biçiminde ayırır.

`iconType`Oluşturulacak PIN türünü belirtir. Aşağıdaki değerlere sahip olabilir:

* `default` – Varsayılan pin simgesi.
* `none` – Simge gösterilmez, yalnızca Etiketler işlenir.
* `custom` – Özel bir simgenin kullanılacağını belirtir. Simgenin sonuna işaret eden bir URL, `pins` PIN konum bilgileri sonrasında parametrenin sonuna eklenebilir.
* `{udid}` – Azure Maps veri depolama platformunda depolanan bir simgenin benzersiz veri KIMLIĞI (UDıD).

Biçim ile PIN stilleri ekleyin `optionNameValue` . Birden çok stili kanal ( \| ) karakterleriyle ayırın. Örneğin: `iconType|optionName1Value1|optionName2Value2`. Seçenek adları ve değerleri ayrılmamış. Stil işaretçileri için aşağıdaki stil seçenek adlarını kullanın:

* `al` – İşaretin opaklığını (Alpha) belirtir. 0 ile 1 arasında bir sayı seçin.
* `an` – PIN bağlayıcısını belirtir. X ve y piksel değerlerini "x y" biçiminde belirtin.
* `co` – PIN rengi. 24 bit onaltılık renk belirtin: `000000` için `FFFFFF` .
* `la` – Etiket bağlayıcısını belirtir. X ve y piksel değerlerini "x y" biçiminde belirtin.
* `lc` – Etiketin rengi. 24 bit onaltılık renk belirtin: `000000` için `FFFFFF` .
* `ls` – Etiketin piksel cinsinden boyutu. 0 ' dan büyük bir sayı seçin.
* `ro` – Simgeyi döndürmek için derece cinsinden bir değer. -360 ile 360 arasında bir sayı seçin.
* `sc` – Pin simgesi için bir ölçek değeri. 0 ' dan büyük bir sayı seçin.

Her PIN konumu için etiket değerlerini belirtin. Bu yaklaşım, konum listesindeki tüm işaretçilere tek bir etiket değeri uygulamaktan daha etkilidir. Etiket değeri birden çok karakterden oluşan bir dize olabilir. Bir stil veya konum değeri olarak yanlış alınana emin olmak için dizeyi tek tırnak işaretleriyle sarın.

`FF0000`"Space iğne" etiketiyle aşağıda konumlandırılmış kırmızı () varsayılan simge ekleyelim (15 50). Simge Boylam:-122,349300, Enlem: 47,620180:

```text
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

![Azure haritalar işaretleyicisi](media/migrate-google-maps-web-services/azure-maps-marker.png)

' 1 ', ' 2 ' ve ' 3 ' Etiket değerleriyle üç PIN ekleyin:

```text
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

![Azure birden çok işaretçileri eşler](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)

### <a name="path-url-parameter-format-comparison"></a>Yol URL parametre biçimi karşılaştırması

**Önce: Google Maps**

URL 'deki parametresini kullanarak statik harita resmine çizgiler ve çokgen ekleyin `path` . `path`Parametresi, aşağıda gösterildiği gibi bir stil ve haritada işlenecek konumların bir listesini alır:

```text
&path=pathStyles|pathLocation1|pathLocation2|...
```

`path`URL 'ye farklı bir stille ve konum kümesiyle ek parametreler ekleyerek ek stilleri kullanın.

Yol konumları `latitude1,longitude1|latitude2,longitude2|…` biçimiyle belirtilir. Yollar, noktaların adreslerini kodlanabilir veya içerebilir.

Biçim ile yol stilleri ekleyin `optionName:value` ve birden çok stili kanal ( \| ) karakterleriyle ayırın. Ve seçenek adları ve değerlerini iki noktayla ayırın (:). Şöyle: `optionName1:value1|optionName2:value2` . Aşağıdaki stil seçenek adları, Google Maps içindeki yollara stil eklemek için kullanılabilir:

* `color` – Yolun veya Çokgen anahattının rengi. 24 bit onaltılı renk ( `0xrrggbb` ), 32 bit onaltılı renk ( `0xrrggbbbaa` ) veya aşağıdaki değerlerden biri olabilir: siyah, kahverengi, yeşil, mor, sarı, mavi, gri, turuncu, kırmızı, beyaz.
* `fillColor` – Yol alanını (çokgen) dolduracak olan renk. 24 bit onaltılı renk ( `0xrrggbb` ), 32 bit onaltılı renk ( `0xrrggbbbaa` ) veya aşağıdaki değerlerden biri olabilir: siyah, kahverengi, yeşil, mor, sarı, mavi, gri, turuncu, kırmızı, beyaz.
* `geodesic` – Yolun, dünya eğriliği takip eden bir çizgi olması gerekip gerekmediğini gösterir.
* `weight` – Yol çizgisinin piksel cinsinden kalınlığı.

URL parametresindeki koordinatlar arasındaki haritaya kırmızı çizgi geçirgenliği ve piksel kalınlığı ekleyin. Aşağıdaki örnekte, satırda %50 opaklık ve dört piksel kalınlığı bulunur. Koordinatlar Boylam:-110, Enlem: 45 ve Boylam:-100, Enlem: 50.

```text
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

![Google Haritalar çoklu çizgi](media/migrate-google-maps-web-services/google-maps-polyline.png)

**Sonrasında: Azure Maps**

URL 'de parametreyi belirterek statik harita görüntüsüne çizgiler ve çokgenler ekleyin `path` . Google Maps gibi, bu parametrede bir stil ve konumların bir listesini belirtin. `path`Birden çok daire, çizgi ve poligonu farklı stillerle işlemek için parametreyi birden çok kez belirtin.

```text
&path=pathStyles||pathLocation1|pathLocation2|...
```

Azure haritalar, yol konumlarına geldiğinde, koordinatların "Boylam Enlem" biçiminde olmasını gerektirir. Google Maps "enlem, Boylam" biçimini kullanır. Bir boşluk değil, bir virgül değil, boylam ve enlem 'yi Azure Maps biçiminde ayırır. Azure Maps, noktalara yönelik kodlanmış yollar veya adresler desteklemez. Daha büyük veri kümelerini, [burada](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage)belgelenen Azure Maps VERI depolama API 'Sine bir geojson dosyası olarak yükleyin.

Biçim ile yol stilleri ekleyin `optionNameValue` . Birden çok stili kanal ( \| ) karakterleriyle ayırın (bunun gibi `optionName1Value1|optionName2Value2` ). Seçenek adları ve değerleri ayrılmamış. Azure haritalar 'daki stil yolları için aşağıdaki stil seçenek adlarını kullanın:

* `fa` -Poligonları işlerken kullanılan Fill Color geçirgenliği (Alpha). 0 ile 1 arasında bir sayı seçin.
* `fc` -Bir çokgenin alanını işlemek için kullanılan Fill Color.
* `la` – Çizgiler ve çokgenler ana hattı işlenirken kullanılan çizgi rengi geçirgenliği (Alpha). 0 ile 1 arasında bir sayı seçin.
* `lc` – Çizgileri oluşturmak için kullanılan çizgi rengi ve çokgenler ana hattı.
* `lw` – Çizginin piksel cinsinden genişliği.
* `ra` – Ölçümlerde bir daire yarıçapı belirtir.

URL parametresinde, koordinatlar arasına kırmızı çizgi geçirgenliği ve piksel kalınlığı ekleyin. Aşağıdaki örnekte, satır %50 opaklık ve dört piksellik bir kalınlığa sahiptir. Koordinatlar şu değerlere sahiptir: Boylam:-110, Enlem: 45 ve Boylam:-100, Enlem: 50.

```text
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

![Azure haritalar çoklu çizgi](media/migrate-google-maps-web-services/azure-maps-polyline.png)

## <a name="calculate-a-distance-matrix"></a>Uzaklık matrisini hesaplama

Azure haritalar mesafe matris API 'sini sağlar. Seyahat sürelerini ve bir konum kümesi arasındaki mesafeyi, uzaklık matrisi ile hesaplamak için bu API 'yi kullanın. Google Maps 'taki uzaklık Matrisi API 'siyle karşılaştırılabilir.

- [**Yol matrisi**](/rest/api/maps/route/postroutematrixpreview): zaman uyumsuz bir çıkış ve hedef kümesi için seyahat sürelerini ve uzaklıkları hesaplar. İstek başına en fazla 700 hücreyi destekler. Bu, hedef sayısı ile çarpılan çıkış sayısıdır. Bu kısıtlama göz önünde bulundurularak, olası matris boyutlarına örnek olarak şunlar verilebilir: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> Uzaklık Matrisi API 'sine yönelik bir istek yalnızca istek gövdesinde kaynak ve hedef bilgileri olan bir POST isteği kullanılarak yapılabilir. Ayrıca, Azure Maps tüm kaynakları ve hedefleri koordine etmek gerektirir. Önce adreslerin coğrafi kodlanmış olması gerekir.

Bu tablo, Google Maps API parametrelerine benzer Azure Maps API parametreleri ile çapraz başvuru yapıyor.

| Google Maps API parametresi      | Karşılaştırılabilir Azure Maps API parametresi  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination` – istek SONRASı gövdesinin coğrafi JSON olarak belirleyin. |
| `key`                          | `subscription-key` – Ayrıca bkz. [Azure Maps Ile kimlik doğrulama](azure-maps-authentication.md) belgeleri. |
| `language`                     | `language` – [Desteklenen diller](supported-languages.md) belgesine bakın.  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins` – istek SONRASı gövdesinin coğrafi JSON olarak belirleyin.  |
| `region`                       | *Yok* : Bu özellik, coğrafi kodlama ile ilgilidir. `countrySet`Azure Maps coğrafi kodlama API 'sini kullanırken parametresini kullanın. |
| `traffic_model`                | *Yok* – yalnızca gelen trafik verilerinin parametresiyle kullanılması gerekiyorsa belirtilebilir `traffic` . |
| `transit_mode`                 | *N/A* -aktarım tabanlı uzaklık matrisleri Şu anda desteklenmiyor.  |
| `transit_routing_preference`   | *N/A* -aktarım tabanlı uzaklık matrisleri Şu anda desteklenmiyor.  |
| `units`                        | *Yok* – Azure Maps yalnızca ölçüm sistemini kullanır. |

> [!TIP]
> Azure haritalar yönlendirme API 'sinde bulunan tüm gelişmiş yönlendirme seçenekleri Azure haritalar Mesafe Matrisi API 'sinde desteklenir. Gelişmiş yönlendirme seçenekleri şunlardır: kamyonu yönlendirme, altyapı belirtimleri vb.

Yönlendirme belgeleri [için en iyi uygulamaları](how-to-use-best-practices-for-routing.md) gözden geçirin.

## <a name="get-a-time-zone"></a>Saat dilimi al

Azure Maps, bir koordinat saat dilimini almak için bir API sağlar. Azure haritalar Saat dilimi API 'SI, Google Maps 'taki saat dilimi API 'siyle karşılaştırılabilir:

- [**Koordine eden saat dilimi**](/rest/api/maps/timezone/gettimezonebycoordinates): koordinat belirtin ve koordinat saat dilimi ayrıntılarını alın.

Bu tablo, Google Maps API parametrelerine Azure haritalar 'daki karşılaştırılabilir API parametreleriyle çapraz başvurular.

| Google Maps API parametresi | Karşılaştırılabilir Azure Maps API parametresi   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` – Ayrıca bkz. [Azure Maps Ile kimlik doğrulama](azure-maps-authentication.md) belgeleri.       |
| `language`                  | `language` – [Desteklenen diller](supported-languages.md) belgesine bakın.    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Azure Maps, bu API 'ye ek olarak çeşitli saat dilimi API 'Leri sağlar. Bu API 'Ler saati, saat diliminin adlarına veya kimliklerine göre dönüştürür:

- [**Kimliğe göre saat dilimi**](/rest/api/maps/timezone/gettimezonebyid): belirtilen IANA saat dilimi kimliği için geçerli, geçmiş ve gelecekteki saat dilimi bilgilerini döndürür.
- [**Bölge enum IANA**](/rest/api/maps/timezone/gettimezoneenumiana): bir IANA saat dilimi kimliklerinin tam listesini döndürür. IANA hizmetine yapılan güncelleştirmeler sisteme bir gün içinde yansıtılır.
- [**Zaman dilimi numaralandırması pencereleri**](/rest/api/maps/timezone/gettimezoneenumwindows): Windows saat dilimi kimliklerinin tam listesini döndürür.
- [**Saat dılımı IANA sürümü**](/rest/api/maps/timezone/gettimezoneianaversion): Azure haritalar tarafından kullanılan geçerli IANA sürüm numarasını döndürür.
- [**IANA Için saat dilimi pencereleri**](/rest/api/maps/timezone/gettimezonewindowstoiana): geçerli bir Windows saat dilimi kimliği verilen ılgılı bır IANA kimliğini döndürür. Tek bir Windows KIMLIĞI için birden çok ıANA kimliği döndürülebilir.

## <a name="client-libraries"></a>İstemci kitaplıkları

Azure Maps, istemci kitaplıklarını aşağıdaki programlama dilleri için sağlar:

* JavaScript, TypeScript, Node.js – [belge](how-to-use-services-module.md) \| [NPM paketi](https://www.npmjs.com/package/azure-maps-rest)

Bu açık kaynaklı istemci kitaplıkları diğer programlama dillerine yöneliktir:

* .NET Standard 2,0 – [GitHub projesi](https://github.com/perfahlen/AzureMapsRestServices) \| [NuGet paketi](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Temizlenecek kaynak yok.

## <a name="next-steps"></a>Sonraki adımlar

Azure haritalar REST hizmetleri hakkında daha fazla bilgi edinin:

> [!div class="nextstepaction"]
> [Arama için en iyi yöntemler](how-to-use-best-practices-for-search.md)
