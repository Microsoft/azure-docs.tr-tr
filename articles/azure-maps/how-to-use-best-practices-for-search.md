---
title: Azure haritalar için en iyi uygulamalar Arama Hizmeti | Microsoft Azure haritaları
description: Microsoft Azure haritalardan Arama Hizmeti kullanırken en iyi uygulamaları nasıl uygulayacağınızı öğrenin.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 11c1938c3c1ccba533f52336fad81ebeaae53b24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895486"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Azure haritalar için en iyi uygulamalar Arama Hizmeti

Azure haritalar [Arama hizmeti](/rest/api/maps/search) , geliştiricilerin adresleri, yerleri, iş listelerini ada veya kategoriye göre ve diğer coğrafi bilgileri aramasına yardımcı olmak için çeşitli yetenekler sunan API 'leri içerir. Örneğin,[benzer arama API 'si](/rest/api/maps/search/getsearchfuzzy) kullanıcıların bir adres veya ilgi noktası (POI) aramasını sağlar.

Bu makalede Arama Hizmeti Azure Maps 'tan veri çağırdığınızda ses uygulamalarının nasıl uygulanacağı açıklanır. Şunları öğrenirsiniz:
> [!div class="checklist"]
> * İlgili eşleşmeleri döndürmek için sorgular oluşturun
> * Arama sonuçlarını sınırla
> * Sonuç türleri arasındaki farkları öğrenin
> * Adres Arama-Yanıt yapısını okuyun

## <a name="prerequisites"></a>Önkoşullar

1. [Azure haritalar hesabı oluşturma](quick-demo-map-app.md#create-an-azure-maps-account)
2. Birincil anahtar veya abonelik anahtarı olarak da bilinen [birincil bir abonelik anahtarı alın](quick-demo-map-app.md#get-the-primary-key-for-your-account).

Bu makale, REST çağrıları oluşturmak için [Postman uygulamasını](https://www.postman.com/downloads/) kullanır, ancak HERHANGI bir API geliştirme ortamı seçebilirsiniz.

## <a name="best-practices-to-geocode-addresses"></a>Kodlamayı adreslerine yönelik en iyi uygulamalar

Azure haritalar Arama Hizmeti kullanarak tam veya kısmi bir adres aradığınızda, API arama sorgunuzun anahtar sözcüklerini okur. Ardından, adresin boylam ve enlem koordinatlarını döndürür. Bu işleme *geokodlamaya* denir.

Bir ülkede/bölgede kodlamayı özelliği, yol verilerinin kullanılabilirliğine ve coğrafi kodlama hizmetinin duyarlığına bağlıdır. Azure 'un coğrafi kodlama özelliklerini ülkeye veya bölgeye göre Maps hakkında daha fazla bilgi için bkz. [coğrafi kodlama kapsamı](./geocoding-coverage.md).

### <a name="limit-search-results"></a>Arama sonuçlarını sınırla

 Azure haritalar arama API 'SI, arama sonuçlarını uygun şekilde sınırlamanıza yardımcı olabilir. Kullanıcılara ilgili verileri görüntüleyebilmeniz için sonuçları sınırlayabilirsiniz.

> [!NOTE]
> Arama API 'Leri, bu makalede ele aldığı kadar çok parametreyi destekler.

#### <a name="geobiased-search-results"></a>Geobıased arama sonuçları

Kullanıcı için ilgili alana sonuç olarak, her zaman mümkün olduğunca fazla konum ayrıntısı ekleyin. Bazı giriş türlerini belirterek arama sonuçlarını kısıtlamak isteyebilirsiniz:

* Parametreyi ayarlayın `countrySet` . `US,FR`Örneğin, olarak ayarlayabilirsiniz. Varsayılan olarak, API tüm dünyayı arar, bu nedenle gereksiz sonuçlar döndürebilir. Sorgunuzun `countrySet` parametresi yoksa arama yanlış sonuçlar döndürebilir. Örneğin, *Bellevue* adlı bir şehirde arama, her iki ülke/bölge de *Bellevue* ADLı bir şehir içerdiğinden ABD ve Fransa 'dan sonuçları döndürür.

* `btmRight`Ve `topleft` parametrelerini, sınırlayıcı kutuyu ayarlamak için kullanabilirsiniz. Bu parametreler, aramayı haritadaki belirli bir alanla kısıtlar.

* Sonuçlara ilişkin ilgi alanını etkilemek için, `lat` ve `lon` koordinat parametrelerini tanımlayın. `radius`Arama alanının yarıçapını ayarlamak için parametresini kullanın.


#### <a name="fuzzy-search-parameters"></a>Benzer arama parametreleri

Bir arama sorgusuna yönelik Kullanıcı girdlerinizi bilmiyorsanız, Azure haritalar [arama, benzer API 'yi](/rest/api/maps/search/getsearchfuzzy) kullanmanızı öneririz. Örneğin, kullanıcıdan gelen giriş bir adres veya Ilgi noktası (POı) türünde olabilir, *örn.* API, POı aramasını ve coğrafi kodlamayı kurallı *tek satırlık bir aramada* birleştirir: 

* `minFuzzyLevel`Ve `maxFuzzyLevel` parametreleri, sorgu parametreleri kullanıcının istediği bilgilerle tam olarak eşleşmediği zaman bile ilgili eşleşmeleri döndürmeye yardımcı olur. Performansı en üst düzeye çıkarmak ve olağandışı sonuçları azaltmak için arama sorgularını, ve varsayılan değerleri olarak ayarlayın `minFuzzyLevel=1` `maxFuzzyLevel=2` . 

    Örneğin, `maxFuzzyLevel` parametresi 2 olarak ayarlandığında, arama terimi *restrant* ile eşleştirilir.  Gerektiğinde varsayılan benzer düzeyleri geçersiz kılabilirsiniz. 

* `idxSet`Sonuç türlerinin tam kümesini önceliklendirmek için parametresini kullanın. Sonuçların tam bir kümesini önceliklendirmek için, virgülle ayrılmış dizinlerin bir listesini gönderebilirsiniz. Listenizde, öğe sırası önemi yoktur. Azure haritalar aşağıdaki dizinleri destekler:

* `Addr` - **Adres aralıkları**: cadde başı ve sonundan alınan adres noktaları. Bu noktaların adres aralıkları olarak temsil edilir.
* `Geo` - **Coğrafi** bölmeler: kara yönetim bölümleri. Coğrafya, örneğin bir ülke/bölge, eyalet veya şehir olabilir.
* `PAD` - **Nokta adresleri**: sokak adı ve sayı içeren adresler. Nokta adreslerini bir dizinde bulabilirsiniz. Bir örnek, *Soquel Dr 2501*' dir. Bir nokta adresi adresler için kullanılabilen en yüksek doğruluk düzeyini sağlar.  
* `POI` - **İlgi çekici noktaları**: bir haritanın, dikkat edilmesi veya ilginç olması düşünülen bir haritada işaret eder. [Arama adresi API 'Si](/rest/api/maps/search/getsearchaddress) POI döndürmez.  
* `Str` - **Streets**: eşlemedeki Streets.
* `XStr` - **Çapraz Streets veya kesişimler**: iki Streets 'in kesişimleri veya yerleri.


#### <a name="usage-examples"></a>Kullanım örnekleri

* `idxSet=POI` -Yalnızca POI arama yapın. 

* `idxSet=PAD,Addr` -Yalnızca adres arayın. `PAD` nokta adresini gösterir ve `Addr` adres aralığını gösterir.

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>Coğrafya varlık türü için ters coğrafi kod ve filtre

[Arama adresi ters API](/rest/api/maps/search/getsearchaddressreverse)'sinde ters coğrafi kod araması yaptığınızda hizmet, yönetim alanlarının çokgenler döndürebilir. Örneğin, bir şehirde alan çokgeni getirmek isteyebilirsiniz. Belirli Coğrafya varlık türlerine aramayı daraltmak için, `entityType` isteklerinizin parametresini ekleyin. 

Elde edilen yanıt, Coğrafya KIMLIĞI ve eşleşen varlık türü içerir. Birden fazla varlık sağlarsanız, uç nokta *kullanılabilir en küçük varlığı* döndürür. [Arama Çokgen hizmeti](/rest/api/maps/search/getsearchpolygon)aracılığıyla Coğrafya geometrisini almak için döndürülen geometri kimliğini kullanabilirsiniz.

#### <a name="sample-request"></a>Örnek istek

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

#### <a name="response"></a>Yanıt

```JSON
{
    "summary": {
        "queryTime": 14,
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

### <a name="set-the-results-language"></a>Sonuçlar dilini ayarlama

`language`Döndürülen arama sonuçlarının dilini ayarlamak için parametresini kullanın. İstek dili ayarlanmamışsa varsayılan Arama Hizmeti ülke veya bölgedeki en yaygın dili kullanır. Belirtilen dilde hiçbir veri yoksa, varsayılan dil kullanılır. 

Daha fazla bilgi için bkz. [Azure Maps desteklenen diller](./supported-languages.md).


### <a name="use-predictive-mode-automatic-suggestions"></a>Tahmine dayalı modu kullanın (otomatik öneriler)

Kısmi sorgularla ilgili daha fazla eşleşme bulmak için `typeahead` parametresini olarak ayarlayın `true` . Bu sorgu kısmi giriş olarak yorumlanır ve arama tahmine dayalı moda girer. `typeahead`Parametresini öğesine ayarlamazsanız `true` , hizmet ilgili tüm bilgilerin geçtiğini varsayar.

Aşağıdaki örnek sorguda, arama adresi hizmeti *mikro* için sorgulanır. Burada, `typeahead` parametresi olarak ayarlanır `true` . Yanıt, arama hizmetinin sorguyu kısmi sorgu olarak yorumdığını gösterir. Yanıt, otomatik olarak önerilen bir sorgunun sonuçlarını içerir.

#### <a name="sample-query"></a>Örnek sorgu

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

#### <a name="response"></a>Yanıt

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 18,
        "numResults": 7,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/9438784",
            "score": 2.594099998474121,
            "dist": 314.0590106663596,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
            },
            "position": {
                "lat": 47.63988,
                "lon": -122.12438
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64223,
                    "lon": -122.1256,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1756074",
            "score": 2.592679977416992,
            "dist": 876.0272035824189,
            "address": {
                "streetName": "Microsoft Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64032,
                "lon": -122.1344
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64253,
                    "lon": -122.13535,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63816,
                    "lon": -122.13305,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1470668",
            "score": 2.5290400981903076,
            "dist": 2735.4883918101486,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Bellevue, WA 98007"
            },
            "position": {
                "lat": 47.65784,
                "lon": -122.14335
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.65785,
                    "lon": -122.14335,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65784,
                    "lon": -122.14325,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/12812615",
            "score": 2.527509927749634,
            "dist": 2870.9579016916873,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.66034,
                "lon": -122.1404
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.66039,
                    "lon": -122.14325,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65778,
                    "lon": -122.13749,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/197588",
            "score": 2.4630401134490967,
            "dist": 878.1404663812472,
            "address": {
                "streetName": "157th Avenue Northeast",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "extendedPostalCode": "980525344, 980525398, 980525399",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "157th Avenue Northeast, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64351,
                "lon": -122.13056
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64473,
                    "lon": -122.13058,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.6425,
                    "lon": -122.13016,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/3033991",
            "score": 2.0754499435424805,
            "dist": 3655467.8844475765,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
            },
            "position": {
                "lat": 35.14267,
                "lon": -80.91824
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14287,
                    "lon": -80.91839,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8395877",
            "score": 2.0754499435424805,
            "dist": 3655437.0037482483,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "countrySubdivisionName": "North Carolina",
                "postalCode": "28273",
                "extendedPostalCode": "282738105, 282738106, 282738108, 2827382, 282738200",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28273"
            },
            "position": {
                "lat": 35.14134,
                "lon": -80.9198
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14274,
                    "lon": -80.92159,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14002,
                    "lon": -80.91824,
                    "valid": true
                }
            }
        }
    ]
}
```


### <a name="encode-a-uri-to-handle-special-characters"></a>Özel karakterleri işlemek için bir URI kodla 

Çapraz cadde adreslerini bulmak için, adresin özel karakterlerini işlemek üzere URI 'yi kodlamanız gerekir. Bu adresi göz önünde bulundurun: *1. aven& UNION Street, Seattle*. Burada, isteği göndermeden önce ampersan karakterini ( `&` ) kodlayın. 

Karakter verilerini bir URI içinde kodlamanızı öneririz. Bir URI 'de, bir yüzde işareti ( `%` ) ve karakter ' UTF-8 koduna karşılık gelen iki karakterlik bir onaltılık değer kullanarak tüm karakterleri kodlayabilirsiniz.

#### <a name="usage-examples"></a>Kullanım örnekleri

Bu adresle başla:

```
query=1st Avenue & E 111th St, New York
```

Adresi kodla:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

Aşağıdaki yöntemleri kullanabilirsiniz.

JavaScript veya TypeScript:
```javascript
encodeURIComponent(query)
```

C# veya Visual Basic:
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

PHP
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


## <a name="best-practices-for-poi-searching"></a>POı arama için en iyi uygulamalar

Bir POı aramasında, ada göre POı sonuçları isteyebilirsiniz. Örneğin, bir işi adına göre arama yapabilirsiniz. 

`countrySet`Uygulamanızın kapsama ihtiyacı olan ülkeleri/bölgeleri belirtmek için parametresini kullanmanız önemle önerilir. Varsayılan davranış, dünyanın tamamında arama yapmak için kullanılır. Bu geniş arama gereksiz sonuçlar döndürebilir ve arama uzun zaman alabilir.

### <a name="brand-search"></a>Marka araması

Sonuçların ve yanıttaki bilgilerin uygunluğunu artırmak için, bir POı arama yanıtı, marka bilgilerini içerir. Yanıtı ayrıştırmak için bu bilgileri kullanabilirsiniz.

Bir istekte, virgülle ayrılmış bir marka adları listesi gönderebilirsiniz. Parametresi ayarlayarak sonuçları belirli markalara kısıtlamak için listeyi kullanın `brandSet` . Listenizde, öğe sırası önemi yoktur. Birden çok marka listesi sağladığınızda, döndürülen sonuçlar listelerden en az birine ait olmalıdır.

Marka aramasını araştırmak için bir [POI kategorisi arama](/rest/api/maps/search/getsearchpoicategory) isteği oluşturalım. Aşağıdaki örnekte, Redmond, Washington 'daki Microsoft Kampüsü yakınlarında gaz istasyonlara bakacağız. Yanıt, döndürülen her bir POı için marka bilgilerini gösterir.

#### <a name="sample-query"></a>Örnek sorgu

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

#### <a name="response"></a>Yanıt

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 276,
        "numResults": 3,
        "offset": 0,
        "totalResults": 762680,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/8831765",
            "score": 5.6631999015808105,
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
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.chevron.com",
                "categories": [
                    "petrol station"
                ],
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
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63222,
                        "lon": -122.13312,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831752",
            "score": 5.662710189819336,
            "dist": 1330.1278248163273,
            "info": "search:ta:840539001100326-US",
            "poi": {
                "name": "76",
                "phone": "+(1)-(425)-7472126",
                "brands": [
                    {
                        "name": "76"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.76.com",
                "categories": [
                    "petrol station"
                ],
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
                "streetNumber": "2421",
                "streetName": "148Th Ave Ne",
                "municipalitySubdivision": "Redmond, Bridle Trails, Bellevue",
                "municipality": "Redmond, Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "2421 148Th Ave Ne, Bellevue, WA 98007",
                "localName": "Bellevue"
            },
            "position": {
                "lat": 47.63187,
                "lon": -122.14365
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63277,
                    "lon": -122.14498,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63097,
                    "lon": -122.14232,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "minor",
                    "position": {
                        "lat": 47.63187,
                        "lon": -122.14374,
                        "valid": true
                    }
                },
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63186,
                        "lon": -122.14313,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831764",
            "score": 5.662449836730957,
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
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.texaco.com/",
                "categories": [
                    "petrol station"
                ],
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
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62827,
                        "lon": -122.13628,
                        "valid": true
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>Havaalanı araması

Arama POI API 'sini kullanarak, resmi kodlarını kullanarak havaalanları arayabilirsiniz. Örneğin, Uluslararası Havaalanı Seattle-Tacoma bulmak için *Sea* 'yi kullanabilirsiniz: 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>Yakındaki arama

Belirli bir konumun etrafında POı sonuçları almak için, [YAKıNDAKI API ara](/rest/api/maps/search/getsearchnearby)' yı kullanmayı deneyebilirsiniz. Uç nokta yalnızca POı sonuçları döndürüyor. Arama sorgu parametresinde almaz. 

Sonuçları sınırlandırmak için, yarıçapı ayarlamanızı öneririz.

## <a name="understanding-the-responses"></a>Yanıtları anlama

Azure Maps Arama Hizmeti bir adres arama isteği yaparak Seattle 'da bir adres bulalim. Aşağıdaki istek URL 'sinde, `countrySet` PARAMETRESINI `US` ABD 'deki adresi arayacak şekilde ayarlayacağız.

### <a name="sample-query"></a>Örnek sorgu

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>Desteklenen sonuç türleri

* **Nokta adresi**: bir haritada cadde adı ve sayı olan belirli bir adrese sahip olan bir haritaya işaret eder. Nokta adresi adresler için en yüksek doğruluk düzeyini sağlar. 

* **Adres aralığı**: cadde başı ve sonundan alınan adres noktalarının aralığıdır.  

* **Coğrafya**: bir örneğin, ülke/bölge, eyalet veya şehir gibi yönetim bölümlerini temsil eden bir harita üzerindeki bölgeler. 

* **POI**: bir haritaya dikkat çekici ve ilginç olabilecek bir haritada işaret ediyor.

* **Cadde**: haritadaki Streets. Adresler, adresi içeren sokak 'nin Enlem ve boylam koordinatları olarak çözümlenir. Ev numarası işlenmeyebilir. 

* **Çapraz cadde**: kesişmeler. Çapraz Streets, iki cadde kesişikinin kesişimlerini temsil eder.

### <a name="response"></a>Yanıt

Yanıt yapısına göz atalım. Aşağıdaki yanıtta, sonuç nesnelerinin türleri farklıdır. Dikkatlice bakarsanız, üç tür sonuç nesnesi görürsünüz:

* Nokta adresi
* Sokak
* Çapraz cadde

Adres aramasının POI döndürmediğine dikkat edin.  

`Score`Her bir yanıt nesnesinin parametresi, eşleşen puanın aynı yanıttaki diğer nesnelerin puanlarıyla ilişkisini gösterir. Yanıt nesnesi parametreleri hakkında daha fazla bilgi için bkz. [arama adresini al](/rest/api/maps/search/getsearchaddress).

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 146,
        "numResults": 6,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/28725082",
            "score": 9.893799781799316,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/6700384",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61724,
                "lon": -122.35207
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61825,
                    "lon": -122.35336,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61626,
                    "lon": -122.35078,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/9701953",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61965,
                "lon": -122.349
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62066,
                    "lon": -122.35041,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.34761,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/11721297",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Downtown Seattle, Denny Regrade, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98121",
                "extendedPostalCode": "981211237",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98121"
            },
            "position": {
                "lat": 47.61825,
                "lon": -122.35078
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61857,
                    "lon": -122.35078,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61825,
                    "lon": -122.35041,
                    "valid": true
                }
            }
        },
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/232144",
            "score": 6.754479885101318,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
            },
            "position": {
                "lat": 47.62545,
                "lon": -122.33974
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62635,
                    "lon": -122.34107,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62455,
                    "lon": -122.33841,
                    "valid": true
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>Geometri

Bir *geometri* yanıt türü, `dataSources` ve altındaki NESNESINDE döndürülen geometri kimliğini içerebilir `geometry` `id` . Örneğin, bir GeoJSON biçiminde geometri verileri istemek için [arama Çokgen hizmetini](/rest/api/maps/search/getsearchpolygon) kullanabilirsiniz. Bu biçimi kullanarak bir varlık kümesi için bir şehir veya Havaalanı ana hattı edinebilirsiniz. Daha sonra bu sınır verilerini kullanarak [bir bölge alanı ayarlayabilir](./tutorial-geofence.md) veya [geometrisi içinde POI araması](/rest/api/maps/search/postsearchinsidegeometry)yapabilirsiniz.


[Arama adresi](/rest/api/maps/search/getsearchaddress) API 'Si veya [arama belirsiz](/rest/api/maps/search/getsearchfuzzy) API 'si için yanıtlar, `dataSources` ve altındaki nesnesinde döndürülen geometri kimliğini içerebilir `geometry` `id` :


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için lütfen bkz.

> [!div class="nextstepaction"]
> [Azure haritalar Arama Hizmeti isteklerini oluşturma](./how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [API belgelerini Arama Hizmeti](/rest/api/maps/search)