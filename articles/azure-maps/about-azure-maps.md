---
title: Microsoft Azure Maps için genel bakış
description: Microsoft Azure haritalarının Hizmetleri ve özellikleri hakkında bilgi edinin ve bunları uygulamalarınızda nasıl kullanacağınızı öğrenin.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc, references_regions
ms.openlocfilehash: 55955f883425651749809da951832484f098aeaf
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256132"
---
# <a name="what-is-azure-maps"></a>Azure Haritalar nedir?

Azure Maps, Web ve mobil uygulamalara coğrafi bağlam sağlamak için yeni eşleme verilerini kullanan Jeo-uzamsal hizmetler ve SDK 'ların bir koleksiyonudur. Azure haritalar şunları sağlar:

* Vektör ve raster haritalarını birden çok stil ve uydu imagery 'de işlemek için REST API 'Leri.
* Özel ınkapılı harita verilerine göre haritalar oluşturmak ve işlemek için Oluşturucu Hizmetleri (Önizleme).
* Dünyanın dört bir yanındaki adresleri, yerleri ve ilgi çekici noktaları bulmak için Hizmetleri arayın.
* Çeşitli yönlendirme seçenekleri; noktadan noktaya, MultiPoint, MultiPoint iyileştirmesi, ısovaone, elektrik araç, ticari araç, trafik etkilenme ve matris yönlendirme gibi.
* Gerçek zamanlı trafik bilgileri gerektiren uygulamalar için trafik akışı görünümü ve olaylar görünümü.
* Mobility Hizmetleri (Önizleme) genel aktarım bilgilerini istemek için, farklı seyahat modlarını ve gerçek zamanlı varış şekillerini karıştırarak rotalar planlayın.
* Saat dilimi ve coğrafi konum (Önizleme) Hizmetleri.
* Yükseltme Hizmetleri (Önizleme) ile dijital yükseltme modeli
* Azure 'da barındırılan konum bilgileriyle bölge sınırlaması hizmeti ve eşleme veri depolaması.
* Jeo-uzamsal analiz aracılığıyla konum zeka.

Ayrıca, Azure Maps Hizmetleri Web SDK ve Android SDK aracılığıyla kullanılabilir. Bu araçlar, geliştiricilerin konum bilgilerini Azure çözümleriyle tümleştiren çözümleri hızla geliştirmesini ve ölçeklendirmesine yardımcı olur.

Ücretsiz bir [Azure Maps hesabına](https://azure.microsoft.com/services/azure-maps/) kaydolabilir ve geliştirmeye başlayabilirsiniz.

Aşağıdaki videoda Azure Haritalar ayrıntılı olarak açıklanır:

</br>

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny]

## <a name="map-controls"></a>Eşleme denetimleri

### <a name="web-sdk"></a>Web SDK’sı

Azure Haritalar Web SDK 'Sı, etkileşimli haritaları kendi içerikleriyle ve Imagery ile özelleştirmenizi sağlar. Bu etkileşimli eşlemeyi Web veya mobil uygulamalarınız için kullanabilirsiniz. Harita denetimi WebGL kullanımını sağlar, böylece büyük veri kümelerini yüksek performanslı olarak işleyebilirsiniz. JavaScript veya TypeScript kullanarak SDK ile geliştirebilirsiniz.

:::image type="content" source="./media/about-azure-maps/intro_web_map_control.png" alt-text="Azure Maps web SDK kullanılarak oluşturulan popülasyon değişikliğinin örnek Haritası":::

### <a name="android-sdk"></a>Android SDK

Mobil eşleme uygulamaları oluşturmak için Azure Haritalar Android SDK kullanın.

:::image type="content" source="./media/about-azure-maps/android_sdk.png" border="false" alt-text="Bir mobil cihazda örnek eşleme":::

## <a name="services-in-azure-maps"></a>Azure Haritalar'ın içindeki hizmetler

Azure Maps, Azure uygulamalarınıza coğrafi içerik sağlayabilen aşağıdaki hizmetlerden oluşur.

### <a name="data-service-preview"></a>Veri hizmeti (Önizleme)

Haritalar için veriler zorunludur. Uzamsal işlemler veya görüntü kompozisyonu ile kullanmak üzere Jeo uzamsal verileri karşıya yüklemek ve depolamak için veri hizmetini kullanın.  Müşteri verilerini Azure Maps hizmetine yaklaştırma gecikme süresini azaltır, üretkenliği artırabilir ve uygulamalarınızda yeni senaryolar oluşturur. Bu hizmetle ilgili ayrıntılar için bkz. [Data Service belgeleri](/rest/api/maps/data).

### <a name="geolocation-service-preview"></a>Coğrafi konum hizmeti (Önizleme)

Bir IP adresi için alınan iki harfli ülke/bölge kodunu önizlemek üzere coğrafi konum hizmetini kullanın. Bu hizmet, coğrafi konuma göre özelleştirilmiş uygulama içeriği sağlayarak kullanıcı deneyimini geliştirmenize yardımcı olabilir.

Daha fazla ayrıntı için [coğrafi konum hizmeti belgelerini](/rest/api/maps/geolocation)okuyun.

### <a name="mobility-services-preview"></a>Mobility Hizmetleri (Önizleme) 

Azure haritalar Mobility Hizmetleri, aktarım yönlendirme ve yakın ortak aktarım için arama gibi genel aktarım özelliklerine sahip uygulamalar için geliştirme süresini geliştirir. Kullanıcılar, aktarım duraklarının, satırların ve zamanlamalarla ilgili ayrıntılı bilgi alabilir. Mobility hizmeti ayrıca kullanıcıların durdurma ve çizgi geometrileri, duruyor, satırlar ve hizmet alanlarıyla ilgili uyarıları ve gerçek zamanlı genel transit ve hizmet uyarılarını almasına olanak tanır. Ayrıca, Mobility Hizmetleri, çok kalıcı seyahat planlama seçenekleriyle yönlendirme özellikleri sağlar. Çoklu kalıcı seyahat planlaması, tek seyahat halinde yürüyen, Bicycling ve genel aktarım seçeneklerini içerir. Kullanıcılar, ayrıntılı çok kalıcı adım adım yolculuk de erişebilir.

Hizmet hakkında daha fazla bilgi edinmek için bkz. [Mobility Hizmetleri belgeleri](/rest/api/maps/mobility).

### <a name="render-service"></a>İşleme hizmeti

[Render Service v2 (Önizleme)](/rest/api/maps/renderv2) , [Get Map kutucuğu v2 API](/rest/api/maps/renderv2/getmaptilepreview)'sinin yeni bir sürümünü sunmaktadır. Get Map kutucuğu v2 API 'SI artık müşterilerin Azure haritalar Oluşturucu kullanılarak oluşturulan Azure Maps yol kutucukları, hava durumu kutucukları veya harita kutucukları istemesine olanak tanır. Yeni Get Map kutucuğu v2 API 'sini kullanmanız önerilir.  

:::image type="content" source="./media/about-azure-maps/intro_map.png" border="false" alt-text="Render Service v2 'den bir harita örneği":::

Daha fazla ayrıntı için, [işleme hizmeti v2 belgelerini](/rest/api/maps/renderv2)okuyun.

GA (genel kullanılabilirlik) olan Işleme hizmeti v1 hakkında daha fazla bilgi edinmek için bkz. [hizmet v1 belgeleri işleme](/rest/api/maps/render).  

### <a name="route-service"></a>Yönlendirme hizmeti

Yol Hizmetleri, istenen her bir rota için tahmini varış zamanlarını (ETAs) hesaplamak üzere kullanılabilir. Yönlendirme API 'Leri, gerçek zamanlı trafik bilgileri ve geçmiş trafik verileri gibi faktörleri göz önünde bulundurarak haftanın istenen günü ve günün saati için tipik yol hızları gibi. API 'Ler, zaman veya mesafeden başlayarak, tek seferde birden çok hedefe veya en iyi duruma getirilmiş sıraya göre sunulan en kısa veya en hızlı yolları döndürür. Hizmet, geliştiricilerin otomobil, kamyon, Bisiklet veya yürüyen ve elektrik arabası gibi çeşitli seyahat modlarında yönleri hesaplamasını sağlar. Hizmet Ayrıca, ayrılma zamanı, ağırlık kısıtlamaları veya tehlikeli malzeme taşıma gibi girdileri de dikkate alır.

:::image type="content" source="./media/about-azure-maps/intro_route.png" border="false" alt-text="Yol hizmetinden bir harita örneği":::

Rota hizmeti, Gelişmiş küme özellikleri sunar, örneğin:

* Birden çok yol isteği toplu işleme.
* Bir çıkış ve hedef kümesi arasındaki seyahat süresi ve mesafenin matrisleri.
* Kullanıcıların zaman veya yakıt gereksinimlerine göre geçebileceği rotaları veya uzaklıkları bulma.

Yönlendirme özellikleri hakkında ayrıntılı bilgi için [route hizmeti belgelerini](/rest/api/maps/route)okuyun.

### <a name="search-service"></a>Arama hizmeti

Arama hizmeti, geliştiricilerin adresleri, yerleri, iş listelerini ada veya kategoriye göre ve diğer coğrafi bilgileri aramasına yardımcı olur. Ayrıca, hizmetler, [coğrafi kod](https://en.wikipedia.org/wiki/Reverse_geocoding) adreslerini ve çapraz Streets 'leri, Latitudes ve Longitudes temelinde ters kaydedebilir.

:::image type="content" source="./media/about-azure-maps/intro_search.png" border="false" alt-text="Haritada arama örneği":::

Arama hizmeti Ayrıca, gibi gelişmiş özellikler de sağlar:

* Bir rota üzerinde arama yapın.
* Daha geniş bir alan içinde arama yapın.
* Arama istekleri grubunu toplu olarak oluşturma.
* Elektrik araç doldurma istasyonlarını ve Ilgi noktası (POı) verilerini marka adına göre arayın.

Arama özellikleri hakkında daha fazla bilgi için [Arama hizmeti belgelerini](/rest/api/maps/search)okuyun.

### <a name="spatial-service"></a>Uzamsal hizmet

Uzamsal hizmet, müşterilerin zaman ve alanda oluşan devam eden olayları bilgilendirmesine yardımcı olmak için konum bilgilerini hızlıca analiz eder. Neredeyse gerçek zamanlı analizler ve olayların modellenmesi sağlar.

Hizmet, müşterilerin konum zekasını ortak Jeo uzamsal matematik hesaplamaları kitaplığıyla geliştirmesini sağlar. Yaygın hesaplamalar, en yakın nokta, büyük daire uzaklığı ve arabellekleri içerir. Hizmet ve çeşitli özellikler hakkında daha fazla bilgi edinmek için, [uzamsal hizmet belgelerini](/rest/api/maps/spatial)okuyun.

### <a name="timezone-service"></a>Saat dilimi hizmeti

Saat dilimi hizmeti geçerli, geçmiş ve gelecekteki saat dilimi bilgilerini sorgulamanızı sağlar. Giriş olarak Enlem ve boylam çiftlerini ya da [ıANA kimliğini](https://www.iana.org/) kullanabilirsiniz. Saat dilimi hizmeti ayrıca şunları sağlar:

* Microsoft Windows saat dilimi kimliklerini ıANA saat dilimlerine dönüştürme.
* UTC 'ye saat dilimi kayması getiriliyor.
* Seçili saat diliminde geçerli saati alma.

Saat dilimi hizmetine bir sorgu için tipik bir JSON yanıtı aşağıdaki örneğe benzer şekilde görünür:

```JSON
{
  "Version": "2020a",
  "ReferenceUtcTimestamp": "2020-07-31T19:15:14.4570053Z",
  "TimeZones": [
    {
      "Id": "America/Los_Angeles",
      "Names": {
        "ISO6391LanguageCode": "en",
        "Generic": "Pacific Time",
        "Standard": "Pacific Standard Time",
        "Daylight": "Pacific Daylight Time"
      },
      "ReferenceTime": {
        "Tag": "PDT",
        "StandardOffset": "-08:00:00",
        "DaylightSavings": "01:00:00",
        "WallTime": "2020-07-31T12:15:14.4570053-07:00",
        "PosixTzValidYear": 2020,
        "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
      }
    }
  ]
}
```

Bu hizmetle ilgili ayrıntılar için [saat dilimi hizmeti belgelerini](/rest/api/maps/timezone)okuyun.

### <a name="traffic-service"></a>Trafik hizmeti

Trafik hizmeti, geliştiricilerin trafik bilgileri gerektiren Web uygulamaları veya mobil uygulamalar için kullanabileceği bir Web hizmetleri paketidir. Hizmet, iki tür veri sağlar:

* Trafik akışı: ağdaki tüm anahtar yolların gerçek zamanlı gözlemlenen hızları ve seyahat süreleri.
* Trafik olayları: trafik sıkışıklıklarının 'nin ve yol ağı çevresindeki olayların güncel görünümü.

![Trafik bilgilerine sahip bir harita örneği](media/about-azure-maps/intro_traffic.png)

Daha fazla bilgi için bkz. [trafik hizmeti belgeleri](/rest/api/maps/traffic).

### <a name="weather-services"></a>Hava durumu hizmetleri

Hava durumu Hizmetleri, geliştiricilerin belirli bir konum için hava durumu bilgilerini almak üzere kullanabileceği API 'Ler sunar. Bilgiler, gözlem tarihi ve saati, hava durumu koşullarının kısa açıklaması, hava durumu simgesi, yağış gösterge bayrakları, sıcaklık ve rüzgar hızı bilgilerinin ayrıntılarını içerir. ™ Sıcaklık ve UV dizini gibi ek ayrıntılar da döndürülür.

Geliştiriciler, belirli bir rota hakkında hava durumu bilgilerini almak için [Hava durumu ve rota API](/rest/api/maps/weather/getweatheralongroute) 'sini kullanabilir. Ayrıca hizmet, bir veya daha ağır yağmur gibi hava durumu riskleri tarafından etkilenen waypoints için hava durumu bildirimleri oluşturmayı destekler.

[Get Map kutucuğu v2 API 'si](/rest/api/maps/renderv2/getmaptilepreview) , geçmiş, güncel ve gelecekteki radar ve uydu kutucukları isteyebilmeniz için izin verir.

![Gerçek zamanlı hava durumu radar kutucukları ile harita örneği](media/about-azure-maps/intro_weather.png)

### <a name="maps-creator-service-preview"></a>Haritalar Oluşturucu hizmeti (Önizleme) 

Haritalar Oluşturucu hizmeti, geliştiricilerin, ınkapımap verilerine dayalı harita özellikleriyle uygulamalar oluşturmak için kullanabilecekleri bir Web hizmetleri paketidir.

Haritalar Oluşturucu üç çekirdek hizmeti sağlar:

* [Veri kümesi hizmeti](/rest/api/maps/dataset). Dönüştürülmüş bir çizim paketi verilerinden bir veri kümesi oluşturmak için veri kümesi hizmetini kullanın. Paket gereksinimlerini çizme hakkında daha fazla bilgi için bkz. çizim paketi gereksinimleri.

* [Dönüştürme hizmeti](/rest/api/maps/dataset). Bir DWG tasarım dosyasını, ınkapımaps için çizim paketi verilerine dönüştürmek için dönüştürme hizmetini kullanın.

* [Tileset hizmeti](/rest/api/maps/tileset). Bir veri kümesinin vektör tabanlı temsilini oluşturmak için tileset hizmetini kullanın. Uygulamalar, veri kümesinin görsel kutucuk tabanlı görünümünü sunmak için bir tileset kullanabilir.

* [Özellik durumu hizmeti](/rest/api/maps/featurestate). Dinamik harita stillendirilmesini desteklemek için özellik durumu hizmetini kullanın. Dinamik harita stili, uygulamaların IoT sistemleri tarafından sunulan alanlara gerçek zamanlı olayları yansıtmaları sağlar.

* [WFS hizmeti](/rest/api/maps/featurestate). Inkapımap verilerinizi sorgulamak için WFS hizmetini kullanın. WFS hizmeti, tek bir veri kümesini sorgulamak için [Open GEOSPATIAL CONSORTIUM API](http://docs.opengeospatial.org/is/17-069r3/17-069r3.html) standartlarını izler.

### <a name="elevation-service-preview"></a>Yükseltme hizmeti (Önizleme)

Azure haritalar yükseltme hizmeti, geliştiricilerin dünya yüzeyine ait her yerden yükseltme verilerini almak için kullanabileceği bir Web hizmetidir.

Yükseltme hizmeti, yükseltme verilerini iki biçimde almanıza izin verir:

* **GeoTiff raster biçimi**. Yükseltme verilerini kutucuk biçiminde almak için [oluşturma v2-harita kutucuğunu al API](/rest/api/maps/renderv2) 'sini kullanın.

* **Geojson biçimi**. Tanımlı bir sınırlayıcı kutusunda veya belirli koordinatlarda, bir yol boyunca örneklenmiş yükseltme verileri istemek için [yükseltme API 'lerini](/rest/api/maps/elevation) kullanın. 

:::image type="content" source="./media/about-azure-maps/elevation.png" alt-text="Yükseltme verileri olan eşleme örneği":::


## <a name="programming-model"></a>Programlama modeli

Azure haritalar, taşınabilirlik için geliştirilmiştir ve platformlar arası uygulamalar geliştirmenize yardımcı olabilir. Dil belirsiz bir programlama modeli kullanır ve [REST API 'ler](/rest/api/maps/)aracılığıyla JSON çıkışını destekler.

Ayrıca, Azure Maps basit bir programlama modeliyle kullanışlı bir [JavaScript harita denetimi](/javascript/api/azure-maps-control) sunar. Geliştirme, hem Web uygulamaları hem de mobil uygulamalar için hızlı ve kolaydır.





## <a name="power-bi-visual"></a>Power BI görseli

Power BI için Azure Maps Visual, bir haritanın en üstündeki uzamsal veriler için zengin bir veri görselleştirmesi kümesi sağlar. İş verilerinin %80 ' de bir konum bağlamına sahip olduğu tahmin edilir. Azure haritalar görseli, bu konum bağlamının iş verilerinizi nasıl ilişkili olduğu hakkında Öngörüler kazanmak için kod içermeyen bir çözüm sunar.

:::image type="content" source="./media/about-azure-maps/intro-power-bi.png" border="false" alt-text="Azure haritalar ile masaüstü Power BI iş verilerini görüntüleme":::

Daha fazla bilgi için bkz. Azure Maps ile çalışmaya başlama [Power BI görsel](power-bi-visual-getting-started.md) belgeleri.

## <a name="usage"></a>Kullanım

Azure haritalar hizmetlerine erişmek için [Azure Portal](https://portal.azure.com) gidin ve Azure Maps hesabı oluşturun.

Azure Haritalar anahtar tabanlı bir kimlik doğrulama düzeni kullanır. Hesabınızı oluşturduğunuzda iki anahtar oluşturulur. Azure haritalar hizmetlerinde kimlik doğrulaması yapmak için, her iki anahtarı da kullanabilirsiniz.

Note-Azure haritaları, işlevselliği eşleştirmek amacıyla üçüncü taraf TomTom ile müşteri tarafından sunulan adres/konum sorgularını ("sorgular") paylaşır. Sorgular, TomTom ile paylaşıldığında hiçbir müşteriye veya son kullanıcıya bağlanmaz ve bireyler tanımlamak için kullanılamaz. Moovit ve Accuhava durumu ile tümleştirme dahil olmak üzere taşınabilirlik ve hava durumu Hizmetleri şu anda [Önizleme](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)aşamasındadır.

Microsoft şu anda, çevrimiçi hizmetler taşeronları listesine TomTom, Moovit ve Accuhava durumu ekleme sürecidir.

## <a name="supported-regions"></a>Desteklenen bölgeler

Azure haritalar Hizmetleri şu anda aşağıdaki ülkelerde/bölgelerde yer almalardır:

* Çin
* Güney Kore

Geçerli IP adresinizin konumunun desteklenen bir ülkede/bölgede olduğunu doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

Azure haritalar 'ı gösteren örnek bir uygulama deneyin:

[Hızlı başlangıç: Web uygulaması oluşturma](quick-demo-map-app.md)

Azure haritalar 'da güncel kalın:

[Azure haritalar blogu](https://azure.microsoft.com/blog/topics/azure-maps/)