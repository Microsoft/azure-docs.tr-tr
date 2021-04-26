---
title: Azure haritalar Hava Hizmetleri 'ni kullanarak gerçek zamanlı ve tahmini hava durumu verileri isteyin
description: Microsoft Azure haritaları Hava durumu hizmetlerini kullanarak gerçek zamanlı (geçerli) ve tahmin edilen (dakika, saatlik, günlük) Hava durumu verilerini isteme hakkında bilgi edinin
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 46d9847e8463d5413409ebcacc7cd62d68f13e65
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259413"
---
# <a name="request-real-time-and-forecasted-weather-data-using-azure-maps-weather-services"></a>Azure haritalar Hava Hizmetleri 'ni kullanarak gerçek zamanlı ve tahmini hava durumu verileri isteyin

Azure haritalar [Hava durumu Hizmetleri](/rest/api/maps/weather) , geliştiricilerin yüksek düzeyde dinamik geçmiş, gerçek zamanlı ve tahmini hava durumu verilerini ve görselleştirmelerini çözümlerle tümleştirmelerine olanak tanıyan bir dizi yeniden API 'dedir. Bu makalede, hem gerçek zamanlı hem de tahmin edilen hava durumu verilerini nasıl isteyeceğini göstereceğiz.

Bu makalede şunları öğreneceksiniz:

* [Geçerli koşulları Al API](/rest/api/maps/weather/getcurrentconditions)'sini kullanarak gerçek zamanlı (geçerli) Hava durumu verileri isteyin.
* [Ciddi hava durumu uyarılarını al API](/rest/api/maps/weather/getsevereweatheralerts)'sini kullanarak ciddi hava durumu uyarıları isteyin.
* [Günlük tahmin al API](/rest/api/maps/weather/getdailyforecast)'sini kullanarak günlük tahminleri isteyin.
* [Saatlik tahmin al API](/rest/api/maps/weather/gethourlyforecast)'sini kullanarak saatlik tahminleri isteyin.
* Dakika [Tahmini al API](/rest/api/maps/weather/getminuteforecast)'sini kullanarak dakikalık tahminlere göre dakika isteyin.

Bu videoda, Azure Maps Hava durumu hizmetlerine REST çağrıları yapmak için örnekler sağlanmaktadır.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps-Weather-services-for-developers/player?format=ny]

## <a name="prerequisites"></a>Önkoşullar

1. [Azure haritalar hesabı oluşturma](quick-demo-map-app.md#create-an-azure-maps-account)
2. Birincil anahtar veya abonelik anahtarı olarak da bilinen [birincil bir abonelik anahtarı alın](quick-demo-map-app.md#get-the-primary-key-for-your-account). Azure haritalar 'da kimlik doğrulaması hakkında daha fazla bilgi için bkz. [Azure haritalar 'da kimlik doğrulamasını yönetme](./how-to-manage-authentication.md).

    >[!IMPORTANT]
    >[Get dakikalık tahmin API 'si](/rest/api/maps/weather/getminuteforecast) bir S1 fiyatlandırma katmanı anahtarı gerektirir. Diğer tüm API 'Ler S0 fiyatlandırma katmanı anahtarı gerektirir.

Bu öğretici [Postman](https://www.postman.com/) uygulamasını kullanır, ancak farklı bir API geliştirme ortamı seçebilirsiniz.

## <a name="request-real-time-weather-data"></a>Gerçek zamanlı hava durumu verileri iste

[Geçerli koşulları Al API 'si](/rest/api/maps/weather/getcurrentconditions) , belirli bir koordinat konumu için yağış, sıcaklık ve rüzgar gibi ayrıntılı hava durumu koşullarını döndürür. Ayrıca, belirli bir konum için son 6 veya 24 saatten gözlemlenebilir. Yanıt, gözlem tarihi ve saati, hava durumu koşullarının kısa açıklaması, hava durumu simgesi, yağış gösterge bayrakları ve sıcaklık gibi ayrıntıları içerir. Gerçekçi™ sıcaklık ve Ultraviolet (UV) dizini de döndürülür.

Bu örnekte, Seattle, WA 'da bulunan koordinatlardaki geçerli hava durumu koşullarını almak için [geçerli koşulları Al API](/rest/api/maps/weather/getcurrentconditions) 'sini kullanacaksınız.

1. Postman uygulamasını açın. Postman uygulamasının üst kısmında **Yeni**' yi seçin. **Yeni oluştur** penceresinde **koleksiyon**' ı seçin.  Koleksiyonu adlandırın ve **Oluştur** düğmesini seçin. Bu belgenin geri kalan örnekleri için bu koleksiyonu kullanacaksınız.

2. İsteği oluşturmak için **Yeni** ' yi seçin. **Yeni oluştur** penceresinde **istek**' ı seçin. İstek için bir **istek adı** girin. Önceki adımda oluşturduğunuz koleksiyonu seçin ve ardından **Kaydet**' i seçin.

3. Oluşturucu sekmesinde http **Al** metodunu seçin ve aşağıdaki URL 'yi girin. Bu istek ve bu makalede bahsedilen diğer istekler için, `{Azure-Maps-Primary-Subscription-key}` birincil abonelik anahtarınızla değiştirin.

    ```http
    https://atlas.microsoft.com/weather/currentConditions/json?api-version=1.0&query=47.60357,-122.32945&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

4. Mavi **Gönder** düğmesine tıklayın. Yanıt gövdesi geçerli hava durumu bilgilerini içerir.

    ```json
    {
    "results": [
        {
            "dateTime": "2020-10-19T20:39:00+00:00",
            "phrase": "Cloudy",
            "iconCode": 7,
            "hasPrecipitation": false,
            "isDayTime": true,
            "temperature": {
                "value": 12.4,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperatureShade": {
                "value": 13.7,
                "unit": "C",
                "unitType": 17
            },
            "relativeHumidity": 87,
            "dewPoint": {
                "value": 10.3,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 23.0,
                    "localizedDescription": "NNE"
                },
                "speed": {
                    "value": 4.5,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 9.0,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "obstructionsToVisibility": "",
            "cloudCover": 100,
            "ceiling": {
                "value": 1494.0,
                "unit": "m",
                "unitType": 5
            },
            "pressure": {
                "value": 1021.2,
                "unit": "mb",
                "unitType": 14
            },
            "pressureTendency": {
                "localizedDescription": "Steady",
                "code": "S"
            },
            "past24HourTemperatureDeparture": {
                "value": -2.1,
                "unit": "C",
                "unitType": 17
            },
            "apparentTemperature": {
                "value": 15.0,
                "unit": "C",
                "unitType": 17
            },
            "windChillTemperature": {
                "value": 12.2,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 11.3,
                "unit": "C",
                "unitType": 17
            },
            "precipitationSummary": {
                "pastHour": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past3Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past6Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past9Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past12Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past18Hours": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "past24Hours": {
                    "value": 0.4,
                    "unit": "mm",
                    "unitType": 3
                }
            },
            "temperatureSummary": {
                "past6Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past12Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 14.0,
                        "unit": "C",
                        "unitType": 17
                    }
                },
                "past24Hours": {
                    "minimum": {
                        "value": 12.2,
                        "unit": "C",
                        "unitType": 17
                    },
                    "maximum": {
                        "value": 15.6,
                        "unit": "C",
                        "unitType": 17
                    }
                }
            }
        }
    ]
    }
    ```

## <a name="request-severe-weather-alerts"></a>Ciddi hava durumu uyarıları iste

[Azure haritalar önemli hava durumu uyarıları API](/rest/api/maps/weather/getsevereweatheralerts) 'leri, hem resmi devlet türüdür kurumları hem de önde gelen küresel hava durumu uyarı sağlayıcılarından dünya çapında kullanılabilen ciddi hava durumu uyarılarını döndürür. Hizmet, uyarı türü, kategori, düzey ve istenen konuma yönelik etkin ciddi uyarılarla ilgili ayrıntılı açıklamalar (örneğin, acericanes, thunderstorms, şimşek, ısı dalgaları veya orman) gibi ayrıntılar döndürebilir. Örnek olarak, lojistik yöneticileri bir haritada önemli hava durumu koşullarını görselleştirerek iş konumları ve planlı yolların yanı sıra sürücülerle ve yerel çalışanları daha da koordine edebilir.

Bu örnekte, geçerli hava durumu koşullarını, Cheyenne, WY ' de bulunan koordinatlara almak için [önemli hava durumu uyarılarını al API 'sini](/rest/api/maps/weather/getsevereweatheralerts) kullanacaksınız.

>[!NOTE]
>Bu örnekte, bu yazma sırasında ciddi hava durumu uyarıları alınır. İstenen konumda artık ciddi hava durumu uyarılarının olmaması olasıdır. Bu örneği çalıştırırken gerçek ciddi uyarı verilerini almak için, farklı bir koordinat konumundaki verileri almanız gerekir.

1. Postman uygulamasını açın, **Yeni**' ye tıklayın ve **istek**' ı seçin. İstek için bir **istek adı** girin. Önceki bölümde oluşturduğunuz veya yeni bir tane oluşturduğunuz koleksiyonu seçin ve ardından **Kaydet**' i seçin.

2. Oluşturucu sekmesinde http **Al** metodunu seçin ve aşağıdaki URL 'yi girin. Bu istek ve bu makalede bahsedilen diğer istekler için, `{Azure-Maps-Primary-Subscription-key}` birincil abonelik anahtarınızla değiştirin.

    ```http
    https://atlas.microsoft.com/weather/severe/alerts/json?api-version=1.0&query=41.161079,-104.805450&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Mavi **Gönder** düğmesine tıklayın. Ciddi hava durumu uyarıları yoksa, yanıt gövdesi boş bir `results[]` dizi içerir. Ciddi hava durumu uyarıları varsa, yanıt gövdesi aşağıdaki JSON yanıtına benzer bir şey içerir:

    ```json
    {
    "results": [
        {
            "countryCode": "US",
            "alertId": 2194734,
            "description": {
                "localized": "Red Flag Warning",
                "english": "Red Flag Warning"
            },
            "category": "FIRE",
            "priority": 54,
            "source": "U.S. National Weather Service",
            "sourceId": 2,
            "alertAreas": [
                {
                    "name": "Platte/Goshen/Central and Eastern Laramie",
                    "summary": "Red Flag Warning in effect until 7:00 PM MDT.  Source: U.S. National Weather Service",
                    "startTime": "2020-10-05T15:00:00+00:00",
                    "endTime": "2020-10-06T01:00:00+00:00",
                    "latestStatus": {
                        "localized": "Continue",
                        "english": "Continue"
                    },
                    "alertDetails": "...RED FLAG WARNING REMAINS IN EFFECT FROM 9 AM THIS MORNING TO\n7 PM MDT THIS EVENING FOR STRONG GUSTY WINDS AND LOW HUMIDITY...\n\n* WHERE...Fire weather zones 303, 304, 305, 306, 307, 308, 309,\n  and 310 in southeast Wyoming. Fire weather zone 313 in Nebraska.\n\n* WIND...West to northwest 15 to 30 MPH with gusts around 40 MPH.\n\n* HUMIDITY...10 to 15 percent.\n\n* IMPACTS...Any fires that develop will likely spread rapidly.\n  Outdoor burning is not recommended.\n\nPRECAUTIONARY/PREPAREDNESS ACTIONS...\n\nA Red Flag Warning means that critical fire weather conditions\nare either occurring now...or will shortly. A combination of\nstrong winds...low relative humidity...and warm temperatures can\ncontribute to extreme fire behavior.\n\n&&",
                    "alertDetailsLanguageCode": "en"
                }
            ]
            },...
        ]
    }
    ```

## <a name="request-daily-weather-forecast-data"></a>Günlük hava durumu tahmin verileri iste

[Günlük tahmin API 'Si al](/rest/api/maps/weather/getdailyforecast) , sıcaklık ve rüzgar gibi ayrıntılı günlük hava durumu tahminini döndürür. İstek, belirli bir koordinat konumu için kaç gün dönemeyeceğini belirtebilir: 1, 5, 10, 15, 25 veya 45 gün. Yanıt, sıcaklık, Rüzgar, yağış, hava kalitesi ve UV dizini gibi ayrıntıları içerir.  Bu örnekte, ayarlayarak beş gün boyunca istek yaptık `duration=5` .

>[!IMPORTANT]
>S0 fiyatlandırma katmanında, sonraki 1, 5, 10 ve 15 gün için günlük tahmin isteyebilirsiniz. S1 fiyatlandırma katmanında, sonraki 25 gün ve 45 gün için günlük tahmin da isteyebilirsiniz.

Bu örnekte, Seattle, WA 'da bulunan koordinatlar için beş günlük bir hava durumu tahminini almak üzere [günlük tahmin al API](/rest/api/maps/weather/getdailyforecast) 'sini kullanacaksınız.

1. Postman uygulamasını açın, **Yeni**' ye tıklayın ve **istek**' ı seçin. İstek için bir **istek adı** girin. Önceki bölümde oluşturduğunuz veya yeni bir tane oluşturduğunuz koleksiyonu seçin ve ardından **Kaydet**' i seçin.

2. Oluşturucu sekmesinde http **Al** metodunu seçin ve aşağıdaki URL 'yi girin. Bu istek ve bu makalede bahsedilen diğer istekler için, `{Azure-Maps-Primary-Subscription-key}` birincil abonelik anahtarınızla değiştirin.

    ```http
    https://atlas.microsoft.com/weather/forecast/daily/json?api-version=1.0&query=47.60357,-122.32945&duration=5&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Mavi **Gönder** düğmesine tıklayın. Yanıt gövdesi, beş günlük hava durumu tahmin verilerini içerir. Kısaltma için aşağıdaki JSON yanıtı, ilk günün tahminini gösterir.
    ```json
    {
    "summary": {
        "startDate": "2020-10-18T17:00:00+00:00",
        "endDate": "2020-10-19T23:00:00+00:00",
        "severity": 2,
        "phrase": "Snow, mixed with rain at times continuing through Monday evening and a storm total of 3-6 cm",
        "category": "snow/rain"
    },
    "forecasts": [
        {
            "date": "2020-10-19T04:00:00+00:00",
            "temperature": {
                "minimum": {
                    "value": -1.1,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 1.3,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperature": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.5,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "realFeelTemperatureShade": {
                "minimum": {
                    "value": -6.0,
                    "unit": "C",
                    "unitType": 17
                },
                "maximum": {
                    "value": 0.7,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "hoursOfSun": 1.8,
            "degreeDaySummary": {
                "heating": {
                    "value": 18.0,
                    "unit": "C",
                    "unitType": 17
                },
                "cooling": {
                    "value": 0.0,
                    "unit": "C",
                    "unitType": 17
                }
            },
            "airAndPollen": [
                {
                    "name": "AirQuality",
                    "value": 23,
                    "category": "Good",
                    "categoryValue": 1,
                    "type": "Ozone"
                },
                {
                    "name": "Grass",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Mold",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Ragweed",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "Tree",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                },
                {
                    "name": "UVIndex",
                    "value": 0,
                    "category": "Low",
                    "categoryValue": 1
                }
            ],
            "day": {
                "iconCode": 22,
                "iconPhrase": "Snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Chilly with snow, 2-4 cm",
                "longPhrase": "Chilly with snow, accumulating an additional 2-4 cm",
                "precipitationProbability": 90,
                "thunderstormProbability": 0,
                "rainProbability": 54,
                "snowProbability": 85,
                "iceProbability": 8,
                "wind": {
                    "direction": {
                        "degrees": 36.0,
                        "localizedDescription": "NE"
                    },
                    "speed": {
                        "value": 9.3,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 70.0,
                        "localizedDescription": "ENE"
                    },
                    "speed": {
                        "value": 25.9,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 0.5,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 2.72,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 9.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 9.0,
                "hoursOfIce": 0.0,
                "cloudCover": 96
            },
            "night": {
                "iconCode": 29,
                "iconPhrase": "Rain and snow",
                "hasPrecipitation": true,
                "precipitationType": "Mixed",
                "precipitationIntensity": "Light",
                "shortPhrase": "Showers of rain and snow",
                "longPhrase": "A couple of showers of rain or snow this evening; otherwise, cloudy; storm total snowfall 1-3 cm",
                "precipitationProbability": 65,
                "thunderstormProbability": 0,
                "rainProbability": 60,
                "snowProbability": 54,
                "iceProbability": 4,
                "wind": {
                    "direction": {
                        "degrees": 16.0,
                        "localizedDescription": "NNE"
                    },
                    "speed": {
                        "value": 16.7,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "windGust": {
                    "direction": {
                        "degrees": 1.0,
                        "localizedDescription": "N"
                    },
                    "speed": {
                        "value": 35.2,
                        "unit": "km/h",
                        "unitType": 7
                    }
                },
                "totalLiquid": {
                    "value": 4.3,
                    "unit": "mm",
                    "unitType": 3
                },
                "rain": {
                    "value": 3.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "snow": {
                    "value": 0.79,
                    "unit": "cm",
                    "unitType": 4
                },
                "ice": {
                    "value": 0.0,
                    "unit": "mm",
                    "unitType": 3
                },
                "hoursOfPrecipitation": 4.0,
                "hoursOfRain": 1.0,
                "hoursOfSnow": 3.0,
                "hoursOfIce": 0.0,
                "cloudCover": 94
            },
            "sources": [
                "AccuWeather"
            ]
        },...
    ]
    }
    ```

## <a name="request-hourly-weather-forecast-data"></a>Saatlik hava durumu tahmin verileri iste

[Saatlik tahmin API 'Si al](/rest/api/maps/weather/gethourlyforecast) , verilen koordinat konumu için bir sonraki 1, 12, 24 (1 gün), 72 (3 gün), 120 (5 gün) ve 240 saat (10 gün) için saat bazında ayrıntılı hava durumu tahmini döndürür. API, sıcaklık, nem, Rüzgar, yağış ve UV dizini gibi ayrıntıları döndürür.

>[!IMPORTANT]
>S0 fiyatlandırma katmanında, sonraki 1, 12, 24 saat (1 gün) ve 72 saat (3 gün) için saatlik tahmin isteyebilirsiniz. S1 fiyatlandırma katmanında sonraki 120 (5 gün) ve 240 saat (10 gün) için saatlik tahmin isteyebilirsiniz.

Bu örnekte, Seattle, WA 'da bulunan koordinatlardan sonraki 12 saat için saatlik hava durumu tahminini almak üzere [saatlik tahmin API 'Si al](/rest/api/maps/weather/gethourlyforecast) ' ı kullanacaksınız.

1. Postman uygulamasını açın, **Yeni**' ye tıklayın ve **istek**' ı seçin. İstek için bir **istek adı** girin. Önceki bölümde oluşturduğunuz veya yeni bir tane oluşturduğunuz koleksiyonu seçin ve ardından **Kaydet**' i seçin.

2. Oluşturucu sekmesinde http **Al** metodunu seçin ve aşağıdaki URL 'yi girin. Bu istek ve bu makalede bahsedilen diğer istekler için, `{Azure-Maps-Primary-Subscription-key}` birincil abonelik anahtarınızla değiştirin.

    ```http
    https://atlas.microsoft.com/weather/forecast/hourly/json?api-version=1.0&query=47.60357,-122.32945&duration=12&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Mavi **Gönder** düğmesine tıklayın. Yanıt gövdesi, sonraki 12 saat için hava durumu tahmin verileri içerir. Kısaltma için aşağıdaki JSON yanıtı, ilk saatin tahminini gösterir.

    ```json
    {
    "forecasts": [
        {
            "date": "2020-10-19T21:00:00+00:00",
            "iconCode": 12,
            "iconPhrase": "Showers",
            "hasPrecipitation": true,
            "precipitationType": "Rain",
            "precipitationIntensity": "Light",
            "isDaylight": true,
            "temperature": {
                "value": 14.7,
                "unit": "C",
                "unitType": 17
            },
            "realFeelTemperature": {
                "value": 13.3,
                "unit": "C",
                "unitType": 17
            },
            "wetBulbTemperature": {
                "value": 12.0,
                "unit": "C",
                "unitType": 17
            },
            "dewPoint": {
                "value": 9.5,
                "unit": "C",
                "unitType": 17
            },
            "wind": {
                "direction": {
                    "degrees": 242.0,
                    "localizedDescription": "WSW"
                },
                "speed": {
                    "value": 9.3,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "windGust": {
                "speed": {
                    "value": 14.8,
                    "unit": "km/h",
                    "unitType": 7
                }
            },
            "relativeHumidity": 71,
            "visibility": {
                "value": 9.7,
                "unit": "km",
                "unitType": 6
            },
            "cloudCover": 100,
            "ceiling": {
                "value": 1128.0,
                "unit": "m",
                "unitType": 5
            },
            "uvIndex": 1,
            "uvIndexPhrase": "Low",
            "precipitationProbability": 51,
            "rainProbability": 51,
            "snowProbability": 0,
            "iceProbability": 0,
            "totalLiquid": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "rain": {
                "value": 0.3,
                "unit": "mm",
                "unitType": 3
            },
            "snow": {
                "value": 0.0,
                "unit": "cm",
                "unitType": 4
            },
            "ice": {
                "value": 0.0,
                "unit": "mm",
                "unitType": 3
            }
        }...
    ]
    }
    ```
## <a name="request-minute-by-minute-weather-forecast-data"></a>Dakika başına Hava durumu tahmin verileri iste

 [Get dakikalık tahmin API 'si](/rest/api/maps/weather/getminuteforecast) , bir sonraki 120 dakika boyunca belirli bir konum için dakika başına birkaç tahmine geri döndürür. Kullanıcılar, 1, 5 ve 15 dakika aralıklarında Hava durumu tahminleri isteyebilir. Yanıt, yağış türü (yağmur, kar veya her ikisinin karışımı dahil), başlangıç zamanı ve yağış yoğunluk değeri (dBZ) gibi ayrıntıları içerir.

Bu örnekte, Seattle, WA 'da bulunan koordinatlara göre dakika başına Hava durumu tahminini almak için [dakikalık tahmini alma API](/rest/api/maps/weather/getminuteforecast) 'sini kullanırsınız. Hava durumu tahmini önümüzdeki 120 dakika boyunca verilir. Sorgumız tahminin 15 dakikalık aralıklarla verilmesini ister, ancak parametreyi 1 veya 5 dakika olacak şekilde ayarlayabilirsiniz.

1. Postman uygulamasını açın, **Yeni**' ye tıklayın ve **istek**' ı seçin. İstek için bir **istek adı** girin. Önceki bölümde oluşturduğunuz veya yeni bir tane oluşturduğunuz koleksiyonu seçin ve ardından **Kaydet**' i seçin.

2. Oluşturucu sekmesinde http **Al** metodunu seçin ve aşağıdaki URL 'yi girin. Bu istek ve bu makalede bahsedilen diğer istekler için, `{Azure-Maps-Primary-Subscription-key}` birincil abonelik anahtarınızla değiştirin.

    ```http
    https://atlas.microsoft.com/weather/forecast/minute/json?api-version=1.0&query=47.60357,-122.32945&interval=15&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

3. Mavi **Gönder** düğmesine tıklayın. Yanıt gövdesi, 15 dakikalık aralıklarla sonraki 120 dakika için hava durumu tahmin verileri içerir.

    ```json
    {
    "summary": {
        "briefPhrase60": "No precipitation for at least 60 min",
        "shortPhrase": "No precip for 120 min",
        "briefPhrase": "No precipitation for at least 120 min",
        "longPhrase": "No precipitation for at least 120 min",
        "iconCode": 7
    },
    "intervalSummaries": [
        {
            "startMinute": 0,
            "endMinute": 119,
            "totalMinutes": 120,
            "shortPhrase": "No precip for %MINUTE_VALUE min",
            "briefPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "longPhrase": "No precipitation for at least %MINUTE_VALUE min",
            "iconCode": 7
        }
    ],
    "intervals": [
        {
            "startTime": "2020-10-19T20:51:00+00:00",
            "minute": 0,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:06:00+00:00",
            "minute": 15,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:21:00+00:00",
            "minute": 30,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:36:00+00:00",
            "minute": 45,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T21:51:00+00:00",
            "minute": 60,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:06:00+00:00",
            "minute": 75,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:21:00+00:00",
            "minute": 90,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        },
        {
            "startTime": "2020-10-19T22:36:00+00:00",
            "minute": 105,
            "dbz": 0.0,
            "shortPhrase": "No Precipitation",
            "iconCode": 7,
            "cloudCover": 100
        }
        ]
    }
    ```

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure haritalar Hava durumu Hizmetleri kavramları](./weather-services-concepts.md)

> [!div class="nextstepaction"]
> [Azure haritalar Hava durumu Hizmetleri REST API](/rest/api/maps/weather)