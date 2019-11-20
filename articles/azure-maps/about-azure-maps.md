---
title: Azure Haritalar’a Genel Bakış | Microsoft Docs
description: Azure Haritalar tanıtımı
author: walsehgal
ms.author: v-musehg
ms.date: 02/04/2019
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: dbcb0e87159efc7cc33101cdd5eee55afbdf3c10
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516648"
---
# <a name="what-is-azure-maps"></a>Azure Haritalar nedir?

Azure Maps, Web ve mobil uygulamalara doğru coğrafi içerik sağlamak için yeni eşleme verilerini kullanan Jeo-uzamsal hizmetler topluluğudur. Azure haritalar şunları sağlar:

* Haritaları birden çok stilde ve uydu Imagery 'de işlemek için REST API 'Leri.
* Dünyanın dört bir yanındaki adres, yer ve ilgi noktalarını arar.
* Yönlendirme noktası noktadan noktaya, MultiPoint, MultiPoint iyileştirmesi, ısovaone, ticari araç, trafik Etkileyenleri ve matris yönlendirme; trafik akışını ve olayları görüntüleme.
* Ortak aktarım ve farklı taşıma modları (Bisiklet payı, scooter paylaşma ve otomobil paylaşma gibi) istemek ve gerçek zamanlı olarak planlama yapmak için Mobility Hizmetleri. 
* Coğrafi konum aracılığıyla Kullanıcı konumu oluşturma ve konumu saat dilimlerine dönüştürme. 
* Azure 'da barındırılan konum bilgileriyle bölge sınırlaması ve harita veri depolama hizmetleri. 
* Jeo-uzamsal analiz aracılığıyla konum zeka. 

REST API 'Lerine ek olarak, Azure Maps Hizmetleri Web SDK veya Android SDK ile kullanılabilir. Bu araçlar, geliştiricilerin konum bilgilerini Azure çözümleriyle tümleştiren çözümleri hızla geliştirmesini ve ölçeklendirmesine yardımcı olur. 

Ücretsiz bir [Azure Maps hesabına](https://azure.microsoft.com/services/azure-maps/) kaydolabilir ve geliştirmeye başlayabilirsiniz.

Aşağıdaki videoda Azure Haritalar ayrıntılı olarak açıklanır:

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>Harita denetimleri

### <a name="web-sdk"></a>Web SDK

Azure Haritalar Web SDK 'Sı, etkileşimli haritaları, Web veya mobil uygulamalarınızda görüntülenmek üzere kendi içeriklerinizi ve Imagery 'yi özelleştirmenize olanak tanır. Bu denetim WebGL kullanımını sağlar, böylece büyük veri kümelerini yüksek performanslı olarak işleyebilirsiniz. JavaScript veya TypeScript kullanarak SDK ile geliştirin.

![Popülasyon değişikliğinin örnek Haritası](media/about-azure-maps/Introduction_WebMapControl.png)

### <a name="android-sdk"></a>Android SDK

Mobil eşleme uygulamaları oluşturmak için Azure Haritalar Android SDK kullanın. 

![Bir mobil cihazda örnek eşleme](media/about-azure-maps/AndroidSDK.png)

## <a name="services-in-azure-maps"></a>Azure Haritalar'ın içindeki hizmetler

Azure Maps, Azure uygulamalarınıza coğrafi içerik sağlayabilen aşağıdaki dokuz hizmetten oluşur.

### <a name="data-service"></a>Data Service

Veriler haritalar için zorunludur. Uzamsal işlemler veya görüntü kompozisyonu ile kullanmak üzere Jeo uzamsal verileri karşıya yüklemek ve depolamak için veri hizmetini kullanın.  Müşteri verilerini Azure Maps hizmetine yaklaştırma gecikme süresini azaltır, üretkenliği artırabilir ve uygulamalarınızda yeni senaryolar oluşturur. Bu hizmetle ilgili ayrıntılar için bkz. [Data Service API 'si belgeleri](https://docs.microsoft.com/rest/api/maps/data).

### <a name="mobility-service"></a>Mobility hizmeti

Azure haritalar Mobility hizmeti gerçek zamanlı Seyahat planlamasına izin verir. Olası en iyi yol seçeneklerini döndürür ve çeşitli seyahat modları sağlar. Metro (şehir) alanlarında bu modlar, yürüyen, biking ve genel aktarım içerebilir. Geliştiriciler, satır geometrisi, durak listeleri, zamanlanmış ve gerçek zamanlı varış ve hizmet uyarıları gibi transit gezi ayrıntılarını talep edebilir.

Hizmet Ayrıca, paylaşılan bisiklet, Scooters veya bir konum etrafındaki otomobiller gibi belirli nesne türlerini aramaya olanak tanır. Kullanıcılar, en yakın dock 'ta kullanılabilir paylaşılan Bisiklet sayısını isteyebilir. Mevcut otomobil eklentilerini arayabilir ve gelecekteki kullanılabilirlik ve geçerli yakıt düzeyi gibi ayrıntıları bulabilirsiniz.

Hizmet hakkında daha fazla bilgi edinmek için bkz. [Mobility API 'si belgeleri](https://docs.microsoft.com/rest/api/maps/mobility).

### <a name="render-service"></a>İşleme hizmeti

Işleme hizmeti, geliştiricilerin eşleme etrafında Web uygulamaları ve mobil uygulamalar oluşturmalarına yardımcı olur. Hizmet, 19 yakınlaştırma düzeyine sahip yüksek kaliteli hücresel grafik görüntüler veya tamamen özelleştirilebilir vektör biçiminde harita görüntüleri kullanır.

![Render Service 'ten bir harita örneği](media/about-azure-maps/Introduction_Map.png)

İşleme hizmeti şimdi geliştiricilerin uydu görüntülemeyle çalışmasına olanak tanıyan önizleme API'leri sunar. Daha fazla ayrıntı için [işleme API 'si belgelerini](https://docs.microsoft.com/rest/api/maps/render)okuyun.

### <a name="route-service"></a>Yönlendirme hizmeti

Yönlendirme hizmeti, birden çok taşıma moduna yönelik gerçek dünya altyapısı ve yol tarifleri için sağlam geometri hesaplamaları içerir. Hizmet, geliştiricilerin araba, kamyon, bisiklet veya yürüyüş gibi çeşitli seyahat modları arasında yol tariflerini hesaplamasını sağlar. Hizmet ayrıca trafik koşulları, ağırlık kısıtlamaları veya tehlikeli madde taşıma gibi girdileri de dikkate alabilir.

![Yol hizmetinden bir harita örneği](media/about-azure-maps/Introduction_Route.png)

Rota hizmeti, gelişmiş özelliklerin bir önizlemesini sağlar, örneğin: 

* Birden çok yol isteği toplu işleme.
* Bir çıkış ve hedef kümesi arasındaki seyahat süresi ve mesafenin matrisleri.
* Kullanıcıların zaman veya yakıt gereksinimlerine göre geçebileceği rotaları veya uzaklıkları bulma. 

Yönlendirme özellikleri hakkında ayrıntılı bilgi için [route API belgelerini](https://docs.microsoft.com/rest/api/maps/route)okuyun.

### <a name="search-service"></a>Arama hizmeti

Arama hizmeti, geliştiricilerin adresleri, yerleri, iş listelerini ada veya kategoriye göre ve diğer coğrafi bilgileri aramasına yardımcı olur. Arama hizmeti, kacode adreslerini ve çapraz Streets 'leri, Latitudes ve Longitudes temelinde [ters](https://en.wikipedia.org/wiki/Reverse_geocoding) kaydedebilir.

![Haritada arama örneği](media/about-azure-maps/Introduction_Search.png)

Arama hizmeti Ayrıca, gibi gelişmiş özellikler de sağlar:

* Bir rota üzerinde arama yapın.
* Daha geniş bir alan içinde arama yapın.
* Arama istekleri grubunu toplu olarak oluşturma.
* Konum noktası yerine daha büyük alanı arayın. 

Toplu arama ve alan arama API'leri şu anda önizleme aşamasındadır. Arama özellikleri hakkında daha fazla bilgi için [Arama API 'si belgelerini](https://docs.microsoft.com/rest/api/maps/search)okuyun.

### <a name="spatial-operations-service"></a>Uzamsal Işlemler hizmeti

Azure haritalar uzamsal Işlemler hizmeti, konum bilgilerini alır ve BT 'de, zaman ve alanda oluşan devam eden olayların müşterilerine bildirme konusunda yardımcı olmak için bu bilgileri anında analiz eder. Neredeyse gerçek zamanlı analizler ve olayların modellenmesi sağlar. 

Hizmet, müşterilerin, en yakın nokta, büyük daire uzaklığı ve arabellekler dahil olmak üzere ortak Jeo uzamsal matematik hesaplamaları kitaplığıyla kendi konum zekası geliştirmesini sağlar. Hizmet ve çeşitli özellikler hakkında daha fazla bilgi edinmek için, [uzamsal işlemler API 'si belgelerini](https://docs.microsoft.com/rest/api/maps/spatial)okuyun.

### <a name="time-zone-service"></a>Saat Dilimi hizmeti

Saat dilimi hizmeti, Latitude/Boylam çiftleri veya bir [ıANA kimliği](https://www.iana.org/)kullanarak geçerli, geçmiş ve gelecekteki saat dilimi bilgilerini sorgulamanızı sağlar. Saat dilimi hizmeti ayrıca şunları sağlar:

* Microsoft Windows saat dilimi kimliklerini ıANA saat dilimlerine dönüştürme.
* UTC 'ye saat dilimi kayması getiriliyor.
* Geçerli saati bir saat diliminde alma. 

Saat dilimi hizmetine bir sorgu için tipik bir JSON yanıtı aşağıdaki örneğe benzer şekilde görünür:

```JSON
{
    "Version": "2017c",
    "ReferenceUtcTimestamp": "2017-11-20T23:09:48.686173Z",
    "TimeZones": [{
        "Id": "America/Los_Angeles",
        "ReferenceTime": {
            "Tag": "PST",
            "StandardOffset": "-08:00:00",
            "DaylightSavings": "00:00:00",
            "WallTime": "2017-11-20T15:09:48.686173-08:00",
            "PosixTzValidYear": 2017,
            "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
        }
    }]
}
```

Bu hizmetle ilgili ayrıntılar için, [saat dilimi API 'si belgelerini](https://docs.microsoft.com/rest/api/maps/timezone)okuyun.

### <a name="traffic-service"></a>Trafik hizmeti

Trafik hizmeti, geliştiricilerin trafik bilgileri gerektiren Web uygulamaları ve mobil uygulamalar oluşturmak için kullanabileceği bir Web hizmetleri paketidir. Hizmet, iki tür veri sağlar:

* Trafik akışı: Ağdaki tüm anahtar yolların gerçek zamanlı gözlemlenen hızları ve seyahat süreleri.
* Trafik olayları: Trafik sıkışıklıklarının 'nin ve yol ağı çevresindeki olayların güncel görünümü.

![Trafik bilgilerine sahip bir harita örneği](media/about-azure-maps/Introduction_Traffic.png)

Daha fazla bilgi için bkz. [trafik API 'si belgeleri](https://docs.microsoft.com/rest/api/maps/traffic).

### <a name="ip-to-location-service"></a>IP-konum hizmeti

IP adresi için alınan iki harfli ülke kodunu önizlemek için IP konumu hizmetini kullanın. Bu hizmet, coğrafi konuma göre özelleştirilmiş uygulama içeriği sağlayarak kullanıcı deneyimini uyarlamanıza ve geliştirmenize yardımcı olabilir.

IP konum hizmetine yönelik REST API 'Leri hakkında daha fazla bilgi için [Azure Maps coğrafi konum API 'si belgelerini](https://docs.microsoft.com/rest/api/maps/geolocation)okuyun.

## <a name="programming-model"></a>Programlama modeli

Azure haritalar, taşınabilirlik için geliştirilmiştir ve platformlar arası uygulamalar geliştirmenize yardımcı olabilir. Dil belirsiz bir programlama modeli kullanır ve [REST API 'ler](https://docs.microsoft.com/rest/api/maps/)aracılığıyla JSON çıkışını destekler.

Azure Haritalar ayrıca hem web hem de mobil platform uygulamalarının hızlı ve kolay bir şekilde geliştirilmesi için basit bir programlama modeliyle kullanışlı bir [JavaScript harita denetimi](https://docs.microsoft.com/javascript/api/azure-maps-control) sunar.

## <a name="usage"></a>Kullanım

Azure haritalar hizmetlerine erişmek, [Azure Portal](https://portal.azure.com) ve Azure Maps hesabı oluşturmaya göre yapılır.

Azure Haritalar anahtar tabanlı bir kimlik doğrulama düzeni kullanır. Hesabınız sizin için önceden oluşturulmuş iki anahtar ile birlikte gelir. Bu anahtarlardan birini kullanıp Azure Haritalar hizmetine istekte bulunarak bu konum özelliklerini uygulamanızla tümleştirmeye başlayabilirsiniz.

## <a name="supported-regions"></a>Desteklenen bölgeler

Azure Haritalar API 'Leri Şu anda tüm ülkelerde/bölgelerde kullanılabilir:

* Çin
* Güney Kore

Geçerli IP adresinizin konumunun yukarıdaki desteklenmeyen ülkelerden birinde olmadığını doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

Azure haritalar 'ı gösteren örnek bir uygulama deneyin:

> [!div class="nextstepaction"]
> [Hızlı Başlangıç: Web uygulaması oluşturma](quick-demo-map-app.md)

Azure haritalar 'da güncel kalın: 

> [!div class="nextstepaction"]
> [Azure haritalar blogu](https://azure.microsoft.com/blog/topics/azure-maps/)
