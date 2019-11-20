---
title: Azure haritalar arama hizmetini kullanarak etkin bir şekilde arama yapma | Microsoft Docs
description: Azure haritalar arama hizmetini kullanarak arama için en iyi yöntemleri kullanmayı öğrenin
author: walsehgal
ms.author: v-musehg
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: de9e484e43c87375c2fdf9b34dd2efce3bb8aa8c
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429169"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Azure haritalar 'ı kullanmak için en iyi uygulamalar Arama Hizmeti

Azure haritalar [Arama hizmeti](https://docs.microsoft.com/rest/api/maps/search) , çeşitli yeteneklere sahip API 'leri içerir, örneğin, belirli bir konum etrafında ilgi noktası (POI) verileri aramak için adres araması. Bu makalede, Azure haritalar arama hizmetleri aracılığıyla verileri çağırmak için en iyi yöntemleri paylaşacağız. Şunları öğrenirsiniz:

* İlgili eşleşmeleri döndürmek için sorgular oluşturun
* Arama sonuçlarını sınırla
* Çeşitli sonuç türleri arasındaki farkı öğrenin
* Adres arama yanıtı yapısını okuyun


## <a name="prerequisites"></a>Önkoşullar

Haritalar hizmeti API 'Lerine herhangi bir çağrı yapmak için bir haritalar hesabı ve anahtarı gereklidir. Hesap oluşturma hakkında daha fazla bilgi için hesabı [Yönet](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) bölümündeki yönergeleri izleyin ve hesabınız için birincil bir abonelik anahtarı almak üzere [birincil anahtar al](./tutorial-search-location.md#getkey) bölümündeki adımları izleyin.

> [!Tip]
> Arama hizmetini sorgulamak için [Postman uygulamasını](https://www.getpostman.com/apps) kullanarak Rest çağrıları oluşturabilir veya tercih ettiğiniz HERHANGI bir API geliştirme ortamını kullanabilirsiniz.


## <a name="best-practices-for-geocoding"></a>Coğrafi kodlama için en iyi uygulamalar

Azure haritalar Arama Hizmeti kullanarak tam veya kısmi bir adres aradığınızda, arama teriminizi alır ve adresin boylam ve enlem koordinatlarını döndürür. Bu işleme geokodlamaya denir. Bir ülkede coğrafi kod yeteneği, coğrafi veri kapsamına ve coğrafi kodlama hizmetinin coğrafi kodlama duyarlığına bağımlıdır.

Azure haritalar coğrafi kodlama özelliklerini ülkeye/bölgeye göre daha fazla bilgi edinmek için bkz. [coğrafi kodlama kapsamı](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) .

### <a name="limit-search-results"></a>Arama sonuçlarını sınırla

   Bu bölümde, arama sonuçlarını sınırlamak için Azure haritalar arama API 'Lerini nasıl kullanacağınızı öğreneceksiniz. 

   > [!Note]
   > Tüm arama API 'Leri aşağıda listelenen parametreleri tam olarak desteklemez

   **Coğrafi sapma arama sonuçları**

   Sonuçlarınızı kullanıcılarınızın ilgili alanına coğrafi olarak sıralamak için, olası en yüksek ayrıntılı konum girişini her zaman eklemeniz gerekir. Arama sonuçlarını kısıtlamak için aşağıdaki giriş türlerini eklemeyi göz önünde bulundurun:

   1. @No__t-0 parametresini, örneğin "US, FR" olarak ayarlayın. Varsayılan arama davranışı, büyük olasılıkla gereksiz sonuçlar döndüren dünyanın tamamında arama yapmak için kullanılır. Sorgunuz `countrySet` parametresini içermiyorsa arama yanlış sonuçlar döndürebilir. Örneğin, **Bellevue** adlı bir şehri aramak, Fransa ve ABD 'de **Bellevue** adlı ŞEHIRLERIN bulunduğu için ABD ve Fransa 'dan sonuçlar döndürür.

   2. Aramayı haritadaki belirli bir alanla sınırlamak için sınırlayıcı kutuyu ayarlamak üzere `btmRight` ve `topleft` parametrelerini kullanabilirsiniz.

   3. Sonuçlara ilişkin ilgi alanını etkilemek için `lat`ve `lon` koordinat parametrelerini tanımlayabilir ve arama alanının yarıçapını `radius` parametresini kullanarak ayarlayabilirsiniz.


   **Benzer arama parametreleri**

   1. @No__t-0 ve `maxFuzzyLevel`, sorgu parametreleri tam olarak istenen bilgilere karşılık gelmese bile ilgili eşleşmeleri geri döndürmesine yardımcı olur. Çoğu arama, performansı elde etmek ve olağandışı sonuçları azaltmak için varsayılan olarak `minFuzzyLevel=1` ve `maxFuzzyLevel=2` ' i sorgular. "Restrant" arama terimine bir örnek alın, `maxFuzzyLevel` olarak ayarlandığında, "Restoran" ile eşleştirilir. Varsayılan benzer düzeyler, istek gereksinimlerine göre geçersiz kılınabilir. 

   2. @No__t-0 parametresini kullanarak döndürülecek sonuç türlerinin tam kümesini de belirtebilirsiniz. Bu amaçla, virgülle ayrılmış dizinlerin listesini gönderebilirsiniz, öğe sırası önemi yoktur. Desteklenen dizinler aşağıda verilmiştir:

       * `Addr` @ no__t-1**adres aralıkları**: bazı Streets için, cadde başı ve sonundan alınmış adres noktaları vardır; Bu noktaların adres aralıkları olarak temsil edilir.
       * `Geo` @ no__t-1**Geographies**: bir kapıdaki yönetim bölümünü temsil eden, yani ülke, eyalet, şehir olan bölgeler.
       * `PAD` @ no__t-1**noktası adresi**: bir dizin içinde cadde adı ve sayı olan belirli bir adresin bulunabileceği bir haritada işaret eder; örneğin, Soquel Dr 2501. Adresler için kullanılabilen en yüksek doğruluk düzeyindedir.  
       * `POI` @ no__t-1**ilgi çekici noktaları**: bir haritada dikkat çekici olan ve ilginç olabilecek bir haritada işaret ediyor.  [Arama adresini al](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) , POI döndürmez.  
       * `Str` @ no__t-1**Streets**: haritadaki cadde temsili.
       * `XStr` @ no__t-1**çapraz Streets/kesişmeler**: ardımları gösterimi; İki cadde kesiştiğinde yer koyar.


       **Kullanım örnekleri**:

       * ıdxset = POı (yalnızca Ilgilendiğiniz arama noktaları) 

       * ıdxset = PAD, addr (yalnızca arama adresleri, PAD = Point adresi, addr = adres aralığı)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>Coğrafi kod ve Coğrafya varlık türü filtresini ters çevir

[Arama adresi ters API 'si](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)ile ters coğrafi kod araması gerçekleştirirken hizmetin, yönetim alanlarının çokgenler döndürme özelliği vardır. İstekte `entityType` parametresini sağlayarak, belirtilen Coğrafya varlık türleri için aramayı daraltabilirsiniz. Elde edilen yanıt, Coğrafya KIMLIĞI ve eşleşen varlık türü ile birlikte içerecektir. Birden fazla varlık sağlarsanız, uç nokta **kullanılabilir en küçük varlığı**döndürür. Döndürülen geometri KIMLIĞI, bu coğrafi bölge 'nin geometrisini [Al (çokgen) hizmeti](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)aracılığıyla almak için kullanılabilir.

**Örnek istek:**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**Yanıtıyla**

```JSON
{
    "summary": {
        "queryTime": 8,
        "numResults": 1
    },
    "addresses": [
        {
            "address": {
                "routeNumbers": [],
                "countryCode": "US",
                "countrySubdivision": "WA",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "municipality": "Redmond",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Redmond, WA",
                "boundingBox": {
                    "northEast": "47.717105,-122.034537",
                    "southWest": "47.627016,-122.164998",
                    "entity": "position"
                },
                "countrySubdivisionName": "Washington"
            },
            "position": "47.639454,-122.130455",
            "dataSources": {
                "geometry": {
                    "id": "00005557-4100-3c00-0000-0000596ae571"
                }
            },
            "entityType": "Municipality"
        }
    ]
}
```

### <a name="search-results-language"></a>Arama sonuçları dili

@No__t-0 parametresi, hangi dilde arama sonuçlarının döndürülmeyeceğini ayarlamanıza olanak sağlar. İstek içinde dil ayarlanmamışsa, arama hizmeti otomatik olarak ülke/bölgedeki en yaygın dile varsayılan olarak ayarlanır. Ayrıca, belirtilen dildeki veriler kullanılabilir olmadığında, varsayılan dil kullanılır. Desteklenen dillerin bir listesi için bkz. Azure Maps hizmetleriyle ülkeye/bölgeye göre [desteklenen diller.](https://docs.microsoft.com/azure/azure-maps/supported-languages)


### <a name="predictive-mode-auto-suggest"></a>Tahmine dayalı mod (otomatik öneri)

Kısmi sorgularla ilgili daha fazla eşleşme bulmak için `typeahead` parametresi ' true ' olarak ayarlanmalıdır. Sorgu kısmi giriş olarak yorumlanır ve arama tahmine dayalı moda girer. Aksi takdirde hizmet ilgili tüm bilgilerin geçtiğini varsayacaktır.

Aşağıdaki örnek sorguda, arama adresi hizmeti 'nin "mikro so" için `typeahead` parametresi **true**olarak ayarlanmış şekilde sorgulandığını görebilirsiniz. Yanıtı gözlemlerseniz, arama hizmetinin sorguyu kısmi sorgu olarak yorumlayıp yanıt, otomatik önerilmiş sorgu sonuçları içerdiğini görebilirsiniz.

**Örnek sorgu:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

**Yanıtıyla**

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 25,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/10294417",
            "score": 2.594,
            "dist": 327.546040632591,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980526399,980528300",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63989,
                "lon": -122.12509
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309
                },
                "btmRightPoint": {
                    "lat": 47.64223,
                    "lon": -122.13061
                }
            }
        },
        ...,
        ...,
        ...,
        ...,
        {
            "type": "Street",
            "id": "US/STR/p0/9063400",
            "score": 2.075,
            "dist": 3655467.6406921702,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "postalCode": "28217",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28217",
                "countrySubdivisionName": "North Carolina"
            },
            "position": {
                "lat": 35.14279,
                "lon": -80.91814
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814
                },
                "btmRightPoint": {
                    "lat": 35.14279,
                    "lon": -80.91824
                }
            }
        }
    ]
}
```


### <a name="uri-encoding-to-handle-special-characters"></a>Özel karakterleri işlemek için URI kodlaması 

Çapraz cadde adreslerini bulmak için, diğer bir deyişle, "1. Avenue & UNION Street, Seattle", özel ' & ' karakterinin istek gönderilmeden önce kodlanması gerekir. Tüm karakterlerin bir '% ' karakteri ve UTF-8 karakterlerine karşılık gelen iki karakterlik bir onaltılık değer kullanılarak kodlandığı bir URI 'de karakter verilerini kodlamayı öneririz.

**Kullanım örnekleri**:

Arama adresini al:

```
query=1st Avenue & E 111th St, New York
```

 şöyle kodlanır:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```


Farklı diller için kullanılacak farklı yöntemler şunlardır: 

JavaScript/TypeScript:
```Javascript
encodeURIComponent(query)
```

C#VB
```csharp
Uri.EscapeDataString(query)
```

Java:
```Java
URLEncoder.encode(query, "UTF-8") 
```

Python:
```Python
import urllib.parse 
urllib.parse.quote(query)
```

C++:
```C++
#include <curl/curl.h>
curl_easy_escape(query)
```

PHP:
```PHP
urlencode(query)
```

Söyleniş
```Ruby
CGI::escape(query) 
```

SWIFT
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Sayfasına
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>POı araması için en iyi uygulamalar

Ilgi noktaları (POı) arama, bir ada göre POı sonuçları talep etmenize olanak tanır. Örneğin, iş adına göre arama yapın. Varsayılan davranış tüm dünyayı aramak, büyük olasılıkla gereksiz sonuçları geri döndürmek ve/veya daha uzun arama süreleriyle sonuçlanabileceğinden, uygulamanızın kapsama ihtiyacı olan ülkeleri belirtmek için `countrySet` parametresini kullanmanızı önemle tavsiye ederiz.

### <a name="brand-search"></a>Marka araması

Sonuçların ve yanıttaki bilgilerin uygunluğunu artırmak için, Ilgi noktası (POı) arama yanıtı, yanıtı ayrıştırmak için kullanılabilecek marka bilgilerini içerir.

Microsoft kampüs (Redmond, WA) yakınında gaz istasyonlara yönelik bir [POI kategorisi arama](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) isteği oluşturalım. Yanıtı gözlemlerseniz, dönen her bir POM için marka bilgilerini görebilirsiniz.

**Örnek sorgu:**

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

**Yanıtıyla**

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 206,
        "numResults": 3,
        "offset": 0,
        "totalResults": 742169,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/245813",
            "score": 5.663,
            "dist": 1037.0280221303253,
            "info": "search:ta:840531000004190-US",
            "poi": {
                "name": "Chevron",
                "phone": "+(1)-(425)-6532200",
                "brands": [
                    {
                        "name": "Chevron"
                    }
                ],
                "url": "www.chevron.com",
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2444",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Northeast Bellevue, Bellevue",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "2444 Bel Red Rd, Bellevue, WA 98007",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63223,
                        "lon": -122.13311
                    }
                }
            ]
        },
        ...,
        {
            "type": "POI",
            "id": "US/POI/p0/7727106",
            "score": 5.662,
            "dist": 1458.645407416307,
            "info": "search:ta:840539000488527-US",
            "poi": {
                "name": "BROWN BEAR CAR WASH",
                "phone": "+(1)-(425)-6442868",
                "brands": [
                    {
                        "name": "Texaco"
                    }
                ],
                "url": "www.texaco.com/",
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "15248",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980525511",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "15248 Bel Red Rd, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62826,
                        "lon": -122.13626
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>Havaalanı araması

POı araması, resmi Havaalanı kodlarını kullanarak havaalanları aramayı destekler. Örneğin, **Sea** (Seattle-Tacoma Uluslararası Havaalanı). 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Yakındaki arama

Belirli bir konumun etrafında yalnızca POı sonuçlarını almak için, [yakın arama API 'si](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) doğru seçim olabilir. Bu uç nokta yalnızca POI sonuçlarını döndürür ve bir arama sorgu parametresi almaz. Sonuçları sınırlandırmak için yarıçap ayarlanması önerilir.

## <a name="understanding-the-responses"></a>Yanıtları anlama

Seattle 'daki bir adres için Azure haritalar [Arama hizmeti](https://docs.microsoft.com/rest/api/maps/search) 'Ne bir adres arama isteği oluşturalım. Aşağıdaki istek URL 'sini dikkatle görüyorsanız, `countrySet` parametresini, " **abd** Birleşik Devletler adresini arayacak şekilde ayarlayacağız.

**Örnek sorgu:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

Aşağıda yanıt yapısına göz atalım. Yanıttaki sonuç nesnelerinin sonuç türleri farklıdır. Dikkatlice gözlemlerseniz, "nokta adresi", "cadde" ve "çapraz cadde" olan üç farklı sonuç nesnesi türü olduğunu görebilirsiniz. Adres araması 'nın POI döndürmediğine dikkat edin. Her yanıt nesnesi için `Score` parametresi, aynı yanıttaki diğer nesnelerin puanlarını gösteren göreli eşleşen puanı gösterir. Yanıt nesnesi parametreleri hakkında daha fazla bilgi edinmek için bkz. [arama adresini al](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) .

**Desteklenen sonuç türleri:**

* **Nokta adresi:** Cadde adı ve numarası olan belirli bir adrese sahip bir haritada işaret eder. Adresler için kullanılabilir en yüksek doğruluk düzeyi. 

* **Adres Aralığı:**  Bazı Streets için, sokak başı ve sonundan alınmış adres noktaları vardır; Bu noktaların adres aralıkları olarak temsil edilir. 

* **Coğrafya:** Bir kapıdaki yönetim bölümünü temsil eden, yani ülke, eyalet, şehir. 

* **Poi-(Ilgi noktaları):** Bir haritanın dikkatini çekecek ve ilgi çekici olabilecek bir haritada işaret ediyor.

* **Cadde:** Haritadaki cadde temsili. Adresler, adresi içeren sokak 'nin Enlem/Boylam koordinatı olarak çözümlenir. Evin numarası işlenemiyor olabilir. 

* **Çapraz cadde:** Kesişimlerini. Junler; İki cadde kesiştiğinde yer koyar.

**Yanıtıyla**

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 129,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/43076024",
            "score": 9.894,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, South Lake Union, Lower Queen Anne",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "400 Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/2440854",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Westlake, South Lake Union",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "extendedPostalCode": "981094347,981094700,981094701,981094702",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62553,
                "lon": -122.33936
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62545,
                    "lon": -122.33861
                },
                "btmRightPoint": {
                    "lat": 47.62574,
                    "lon": -122.33974
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8450985",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109,98121",
                "extendedPostalCode": "981094991,981211117,981211237,981213206",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.61691,
                "lon": -122.35251
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61502,
                    "lon": -122.35041
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.35484
                }
            }
        },
        ...,
        ...,
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/3816818",
            "score": 6.759,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street & Valley Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62574,
                "lon": -122.33861
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62664,
                    "lon": -122.33994
                },
                "btmRightPoint": {
                    "lat": 47.62484,
                    "lon": -122.33728
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Geometrisi

Yanıt türü **geometri**olduğunda, "Geometry" ve "ID" altındaki **DataSources** nesnesinde döndürülen geometri kimliğini içerebilir. Örneğin, [Çokgen hizmeti](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) , geometri verilerini coğrafi bir varlık kümesi için şehir veya Havaalanı ana hattı gibi geojson biçiminde talep etmenize olanak tanır. Bu sınır verilerini [, geometri Içinde](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) [bölge](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) sınırlaması veya arama POI için kullanabilirsiniz.


[Arama adresi](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) veya [arama benzer](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) API yanıtları, "geometri" ve "kimlik" altındaki veri kaynakları nesnesinde döndürülen **geometri kimliğini** içerebilir.


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure haritalar arama hizmeti istekleri oluşturmayı](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)öğrenin.
* Azure haritalar [Arama hizmeti API 'si belgelerini](https://docs.microsoft.com/rest/api/maps/search)inceleyin. 
