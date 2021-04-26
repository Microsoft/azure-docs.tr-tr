---
title: Azure IoT Hub modül TWINS 'i anlayın | Microsoft Docs
description: Geliştirici Kılavuzu-IoT Hub ve cihazlarınız arasında durum ve yapılandırma verilerini eşzamanlı hale getirmek için modül TWINS kullanın
author: nehsin
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: nehsin
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 6e728eaf8335a102e38a3b4b07ab5e504d452294
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91996459"
---
# <a name="understand-and-use-module-twins-in-iot-hub"></a>IoT Hub modül TWINS 'i anlayın ve kullanın

Bu makalede, önce [IoT Hub ' de anlaşılması ve cihaz TWINS kullandığınızı varsaymış](iot-hub-devguide-device-twins.md) olursunuz. IoT Hub, her bir cihaz kimliği altında, en fazla 50 modül kimliği oluşturabilirsiniz. Her modül kimliği dolaylı olarak ikizi bir modül üretir. Cihaz ikikine benzer şekilde, modül TWINS 'i meta veriler, konfigürasyonlar ve koşullar dahil olmak üzere modül durum bilgilerini depolayan JSON belgelerdir. Azure IoT Hub, IoT Hub bağlandığınız her modül için bir modül ikizi tutar. 

Cihaz tarafında IoT Hub cihaz SDK 'Ları, her birinin IoT Hub için bağımsız bir bağlantı açtığı modüller oluşturmanızı sağlar. Bu işlevsellik, cihazınızdaki farklı bileşenler için ayrı ad alanları kullanmanıza olanak sağlar. Örneğin, üç farklı algılayıcı içeren bir havalandırma makineniz vardır. Her algılayıcı, şirketinizdeki farklı departmanlar tarafından denetlenir. Her algılayıcı için bir modül oluşturabilirsiniz. Bu şekilde, her departman yalnızca kontrol ettikleri sensöre iş veya doğrudan yöntemleri gönderebilir, çakışmaların ve Kullanıcı hatalarından kaçınırlar.

 Modül kimliği ve modül ikizi, cihaz kimliği ve cihaz ikizi ile aynı özellikleri sağlar, ancak daha ayrıntılı bir ayrıntı düzeyine sahiptir. Bu daha ayrıntılı ayrıntı düzeyi, bu bileşenlerin her biri için yapılandırma ve koşulları yalıtmak amacıyla, işletim sistemi tabanlı cihazlar veya birden çok bileşeni yöneten bellenim cihazları gibi özellikli cihazların kullanılmasını sağlar. Modül kimliği ve modül TWINS, Modüler yazılım bileşenlerine sahip IoT cihazlarıyla çalışırken dikkat edilmesi gereken bir yönetim ayrımı sağlar. Modül ikizi Level ' da tüm Device ikizi işlevselliğini, Module ikizi genel kullanıma sunarak desteklemeye hedefliyoruz. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Bu makalede şunları açıklanmaktadır:

* Modülün yapısı ikizi: *Etiketler*, *istenen* ve *bildirilen özellikler*.
* Modüllerdeki ve arka uçlardaki işlemler modül TWINS üzerinde gerçekleştirilebilir.

Bildirilen özellikleri, cihazdan buluta iletileri veya karşıya dosya yüklemeyi kullanma hakkında rehberlik için [cihazdan buluta iletişim kılavuzuna](iot-hub-devguide-d2c-guidance.md) bakın.

İstenen özellikleri, doğrudan yöntemleri veya buluttan cihaza iletileri kullanma hakkında rehberlik için [buluttan cihaza iletişim kılavuzuna](iot-hub-devguide-c2d-guidance.md) bakın.

## <a name="module-twins"></a>Modül TWINS

Modül TWINS, modülle ilgili bilgileri şu şekilde saklayın:

* Cihaz ve IoT Hub modüller modül koşullarını ve yapılandırmayı eşitleyebilmek için kullanabilir.

* Çözüm arka ucu, uzun süre çalışan işlemleri sorgulamak ve hedeflemek için kullanılabilir.

Modül ikizi yaşam döngüsü karşılık gelen [Modül kimliğiyle](iot-hub-devguide-identity-registry.md)bağlantılıdır. IoT Hub bir modül kimliği oluşturulduğunda veya silindiğinde, modüller örtülü olarak oluşturulur ve silinir.

Modül ikizi, şunları içeren bir JSON belgesidir:

* **Etiketler**. JSON belgesinin çözüm arka ucunun okuyave yazabilmesi için bir bölümü. Etiketler cihazdaki modüller için görünür değildir. Etiketler, sorgulama amacını ayarlar.

* **İstenen özellikler**. Modül yapılandırmalarını veya koşullarını eşitlemeye yönelik bildirilen özelliklerle birlikte kullanılır. Çözüm arka ucu, istenen özellikleri ayarlayabilir ve modül uygulaması bunları okuyabilir. Modül uygulaması, istenen özelliklerde değişiklik bildirimleri de alabilir.

* **Bildirilen özellikler**. Modül yapılandırmalarını veya koşullarını eşleştirmek için istenen özelliklerle birlikte kullanılır. Modül uygulaması bildirilen özellikleri ayarlayabilir ve çözüm arka ucu bunları okuyabilir ve sorgulayabilir.

* **Modül kimliği özellikleri**. Module ikizi JSON belgesinin kökü, [kimlik kayıt defterinde](iot-hub-devguide-identity-registry.md)depolanan karşılık gelen modül kimliğinden salt okunurdur özellikleri içerir.

![Cihaz ikizi mimari temsili](./media/iot-hub-devguide-device-twins/module-twin.jpg)

Aşağıdaki örnek bir Module ikizi JSON belgesi göstermektedir:

```json
{
    "deviceId": "devA",
    "moduleId": "moduleA",
    "etag": "AAAAAAAAAAc=", 
    "status": "enabled",
    "statusReason": "provisioned",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "connected",
    "lastActivityTime": "2015-02-30T16:24:48.789Z",
    "cloudToDeviceMessageCount": 0, 
    "authenticationType": "sas",
    "x509Thumbprint": {     
        "primaryThumbprint": null, 
        "secondaryThumbprint": null 
    }, 
    "version": 2, 
    "tags": {
        "$etag": "123",
        "deploymentLocation": {
            "building": "43",
            "floor": "1"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata" : {...},
            "$version": 1
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

Kök nesnede modül kimliği özellikleri ve `tags` hem de hem de özellikleri için kapsayıcı nesneleri bulunur `reported` `desired` . `properties`Kapsayıcı, `$metadata` `$etag` `$version` [ikizi meta verileri](iot-hub-devguide-module-twins.md#module-twin-metadata) ve [iyimser eşzamanlılık](iot-hub-devguide-device-twins.md#optimistic-concurrency) bölümlerinde açıklanan bazı salt okunurdur (,, ve) öğeleri içerir.

### <a name="reported-property-example"></a>Bildirilen özellik örneği

Önceki örnekte modül ikizi Module `batteryLevel` uygulaması tarafından bildirilen bir özelliği içerir. Bu özellik, en son bildirilen pil düzeyine dayanarak modüller üzerinde sorgu ve işlem yapmayı mümkün kılar. Diğer örneklerde modül uygulaması raporlama modülü özellikleri veya bağlantı seçenekleri bulunur.

> [!NOTE]
> Bildirilen özellikler, çözüm arka ucunun bir özelliğin bilinen son değeri ile ilgilendiği senaryoları basitleştirir. Çözüm arka ucunun, zaman serisi gibi zaman damgası bulunan olayların dizileri biçiminde modül telemetrisini işlemesi gerekiyorsa [cihazdan buluta iletileri](iot-hub-devguide-messages-d2c.md) kullanın.

### <a name="desired-property-example"></a>İstenen özellik örneği

Önceki örnekte, `telemetryConfig` Modül ikizi istenen ve bildirilen özellikler, Bu modülün telemetri yapılandırmasını eşitleyecek şekilde çözüm arka ucu ve modül uygulaması tarafından kullanılır. Örnek:

1. Çözüm arka ucu istenen özelliği istenen yapılandırma değeriyle ayarlar. Belge, istenen özellik kümesine sahip olan bölümüdür:

    ```json
    ...
    "desired": {
        "telemetryConfig": {
            "sendFrequency": "5m"
        },
        ...
    },
    ...
    ```

2. Modül uygulamasına, bağlantı kurulduktan sonra veya ilk kez yeniden bağlandıklarında bildirim gönderilir. Modül uygulaması daha sonra güncelleştirilmiş yapılandırmayı (veya özelliğini kullanarak bir hata koşulunu) raporlar `status` . Bildirilen özelliklerin bölümü aşağıda verilmiştir:

    ```json
    "reported": {
        "telemetryConfig": {
            "sendFrequency": "5m",
            "status": "success"
        }
        ...
    }
    ```

3. Çözüm arka ucu, modül TWINS 'i [sorgulayarak](iot-hub-devguide-query-language.md) birçok modül genelinde yapılandırma işleminin sonuçlarını izleyebilir.

> [!NOTE]
> Önceki kod parçacıkları, bir modül yapılandırmasını ve durumunu kodlamak için en iyi duruma getirilmiş örnektir. IoT Hub, modül TWINS 'de istenen ve bildirilen özellikler ikizi modülü için belirli bir şema uygulamaz.
> 
> 

## <a name="back-end-operations"></a>Arka uç işlemleri
Çözüm arka ucu, HTTPS üzerinden sunulan aşağıdaki atomik işlemleri kullanarak ikizi modülü üzerinde çalışır:

* **Modül ikizi by ID olarak alın**. Bu işlem, Etiketler ve istenen ve bildirilen sistem özellikleri dahil olmak üzere Module ikizi belgesini döndürür.

* **Modül Ikizi kısmen güncelleştirme**. Bu işlem, çözüm arka ucunun, ikizi modülündeki etiketlerin veya istenen özelliklerin kısmen güncelleştirilmesini sağlar. Kısmi güncelleştirme, herhangi bir özelliği ekleyen veya güncelleştiren bir JSON belgesi olarak ifade edilir. Olarak ayarlanan özellikler `null` kaldırılır. Aşağıdaki örnek, yeni bir istenen özelliği değeri ile oluşturur `{"newProperty": "newValue"}` , var olan değerinin üzerine yazar `existingProperty` `"otherNewValue"` ve kaldırır `otherOldProperty` . İstenen varolan özellikler veya etiketlere başka bir değişiklik yapılmaz:

    ```json
    {
        "properties": {
            "desired": {
                "newProperty": {
                    "nestedProperty": "newValue"
                },
                "existingProperty": "otherNewValue",
                "otherOldProperty": null
            }
        }
    }
    ```

* **İstenen özellikleri değiştirin**. Bu işlem, çözüm arka ucunun, tüm mevcut özellikleri tümüyle üzerine yazmasına ve için yeni bir JSON belgesi yerine geçecek şekilde olanak sağlar `properties/desired` .

* **Etiketleri değiştirin**. Bu işlem, çözüm arka ucunun tüm mevcut etiketlerin üzerine yazılmasına ve yeni bir JSON belgesini yerine kullanmasına olanak sağlar `tags` .

* **İkizi bildirimleri alın**. Bu işlem, ikizi değiştirildiğinde çözüm arka ucunun bildirilmesini sağlar. Bunu yapmak için, IoT çözümünüzün bir rota oluşturması ve veri kaynağını *twinChangeEvents*' e eşit olarak ayarlaması gerekir. Varsayılan olarak, hiçbir ikizi bildirimi gönderilmez, diğer bir deyişle, böyle bir yol önceden mevcut değildir. Değişiklik hızı çok yüksekse veya iç arızalar gibi diğer nedenlerden dolayı IoT Hub tüm değişiklikleri içeren yalnızca bir bildirim gönderebilir. Bu nedenle, uygulamanızın tüm ara durumların güvenilir denetim ve günlüğe kaydetme ihtiyacı varsa cihazdan buluta iletileri kullanmanız gerekir. İkizi bildirim iletisi, özellikleri ve gövdesi içerir.

  - Özellikler

    | Name | Değer |
    | --- | --- |
    $content türü | uygulama/json |
    $iothub-enqueuedtime |  Bildirimin gönderildiği zaman |
    $iothub-ileti-kaynak | twinChangeEvents |
    $content kodlaması | UTF-8 |
    deviceId | Cihazın KIMLIĞI |
    Modül kimliği | Modülün KIMLIĞI |
    hubName | IoT Hub adı |
    operationTimestamp | [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) işlem zaman damgası |
    ıothub-Message-Schema | twinChangeNotification |
    opType | "yeniden kazan" veya "updateTwin" |

    İleti sistemi özelliklerine sembol ön eki eklenir `$` .

  - Gövde
        
    Bu bölüm bir JSON biçimindeki tüm ikizi değişikliklerini içerir. Bir düzeltme ekiyle aynı biçimi kullanır ve tüm ikizi bölümlerini içerebileceği fark vardır: Etiketler, Özellikler. bildirilen, Özellikler. istenen ve "$metadata" öğelerini içerir. Örneğin,

    ```json
    {
      "properties": {
          "desired": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          },
          "reported": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          }
      }
    }
    ```

Önceki tüm işlemler [iyimser eşzamanlılığı](iot-hub-devguide-device-twins.md#optimistic-concurrency) destekler ve [IoT Hub erişimi denetimi](iot-hub-devguide-security.md) makalesinde tanımlandığı şekilde **serviceconnect** iznini gerektirir.

Bu işlemlere ek olarak, çözüm arka ucu SQL benzeri [IoT Hub sorgu dilini](iot-hub-devguide-query-language.md)kullanarak modül TWINS 'i sorgulayabilir.

## <a name="module-operations"></a>Modül işlemleri

Modül uygulaması, aşağıdaki atomik işlemleri kullanarak ikizi modülü üzerinde çalışır:

* **İkizi modülünü alın**. Bu işlem, şu anda bağlı olan modül için Module ikizi Document (Etiketler ve istenen ve bildirilen sistem özellikleri dahil) döndürür.

* **Bildirilen özellikleri kısmen güncelleştirme**. Bu işlem, bağlı olan modülün bildirilen özelliklerinin kısmi güncelleştirilmesini mümkün. Bu işlem, çözüm arka ucunun istenen özelliklerin kısmi güncelleştirilmesi için kullandığı JSON güncelleştirme biçimini kullanır.

* **İstenen özellikleri gözlemleyin**. Şu anda bağlı olan modül, ne zaman meydana gelediklerinde istenen özellikler için güncelleştirmeler bildirilmesini seçebilir. Modül, çözüm arka ucu tarafından yürütülen aynı güncelleştirme formunu (kısmi veya tam değiştirme) alır.

Önceki tüm işlemler, [IoT Hub erişimi denetimi](iot-hub-devguide-security.md) makalesinde tanımlandığı şekilde **moduleconnect** iznini gerektirir.

[Azure IoT cihaz SDK 'ları](iot-hub-devguide-sdks.md) , önceki işlemleri birçok dil ve platformda kullanmayı kolaylaştırır.

## <a name="tags-and-properties-format"></a>Etiketler ve Özellikler biçimi

Etiketler, istenen özellikler ve bildirilen özellikler, JSON nesneleridir ve aşağıdaki kısıtlamalara sahiptir:

* **Anahtarlar**: JSON nesnelerindeki tüm anahtarlar UTF-8 kodlamalı, büyük/küçük harfe duyarlıdır ve uzunluğu 1 KB 'tır. İzin verilen karakterler UNICODE denetim karakterlerini (C0 ve C1 kesimleri),, `.` ve SP 'yi hariç tutar `$` .

* **Değerler**: JSON nesnelerindeki tüm değerler şu JSON türlerine sahip olabilir: Boolean, Number, String, Object. Diziler de desteklenir.

    * Tamsayılar en az-4503599627370496 ve en yüksek değer olan 4503599627370495 değerine sahip olabilir.

    * Dize değerleri UTF-8 kodlardır ve en fazla 4 KB olabilir.

* **Derinlik**: etiket, istenen özellikler ve bildirilen ÖZELLIKLER içindeki JSON nesnelerinin maksimum derinliği 10 ' dur. Örneğin, aşağıdaki nesne geçerli olur:

   ```json
   {
       ...
       "tags": {
           "one": {
               "two": {
                   "three": {
                       "four": {
                           "five": {
                               "six": {
                                   "seven": {
                                       "eight": {
                                           "nine": {
                                               "ten": {
                                                   "property": "value"
                                               }
                                           }
                                       }
                                   }
                               }
                           }
                       }
                   }
               }
           }
       },
       ...
   }
   ```

## <a name="module-twin-size"></a>Modül ikizi boyutu

IoT Hub değerinde 8 KB boyutunda bir boyut sınırı `tags` ve and değeri üzerinde her biri 32 KB boyut sınırı zorlar `properties/desired` `properties/reported` . Bu toplamlar, ve gibi salt yazılır öğelerin dışlanmalıdır `$etag` `$version` `$metadata/$lastUpdated` .

İkizi boyutu şu şekilde hesaplanır:

* JSON belgesindeki her bir özellik için, IoT Hub üst üste hesaplar ve özelliğin anahtar ve değerinin uzunluğunu ekler.

* Özellik anahtarları UTF8 kodlu dizeler olarak değerlendirilir.

* Basit özellik değerleri, UTF8 kodlu dizeler, sayısal değerler (8 bayt) veya Boole değerleri (4 bayt) olarak değerlendirilir.

* UTF8 kodlu dizelerin boyutu, UNICODE denetim karakterleri (segment C0 ve C1) hariç tüm karakterlerin sayılarak hesaplanır.

* Karmaşık özellik değerleri (iç içe geçmiş nesneler), içerdikleri özellik anahtarlarının ve özellik değerlerinin toplam boyutu temel alınarak hesaplanır.

IoT Hub, sınırın üzerinde bu belgelerin boyutunu arttırabilecek tüm işlemleri bir hata ile reddeder.

## <a name="module-twin-metadata"></a>Module ikizi meta verileri

IoT Hub, modüldeki her JSON nesnesi için son güncelleştirmenin zaman damgasını ikizi istenen ve bildirilen özellikleri tutar. Zaman damgaları UTC biçimindedir ve [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) biçiminde kodlanır `YYYY-MM-DDTHH:MM:SS.mmmZ` .
Örnek:

```json
{
    ...
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$lastUpdated": "2016-03-30T16:24:48.789Z"
                },
                "$lastUpdated": "2016-03-30T16:24:48.789Z"
            },
            "$version": 23
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": "55%",
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": "5m",
                    "status": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "$lastUpdated": "2016-03-31T16:35:48.789Z"
                },
                "batteryLevel": {
                    "$lastUpdated": "2016-04-01T16:35:48.789Z"
                },
                "$lastUpdated": "2016-04-01T16:24:48.789Z"
            },
            "$version": 123
        }
    }
    ...
}
```

Bu bilgiler, nesne anahtarlarını kaldırma güncelleştirmelerini korumak için her düzeyde (yalnızca JSON yapısının değil) tutulur.

## <a name="optimistic-concurrency"></a>İyimser eşzamanlılık

Etiketler, istenen ve raporlanan Özellikler iyimser eşzamanlılık desteği.
Etiketler, etiketin JSON gösterimini temsil eden, [RFC7232](https://tools.ietf.org/html/rfc7232)başına bir ETag öğesine sahiptir. Tutarlılık sağlamak için çözüm arka ucundan koşullu güncelleştirme işlemlerinde ETags kullanabilirsiniz.

İkizi modülü istenen ve bildirilen özelliklerin ETags yoktur, ancak `$version` artımlı olması garantilenmiş bir değer vardır. Bir ETag 'e benzer şekilde, sürüm, güncelleştirmelerin tutarlılığını zorlamak için güncelleştirme partisi tarafından kullanılabilir. Örneğin, bildirilen bir özellik için modül uygulaması veya istenen özellik için çözüm arka ucu.

Sürümler Ayrıca bir gözlemleme aracısının (modül uygulaması istenen özellikleri gözlemleme gibi), bir alma işleminin sonucu ve bir güncelleştirme bildirimi arasındaki engelleri bağdaştırmak gerektiğinde de yararlıdır. Cihaz yeniden [bağlantı akışı](iot-hub-devguide-device-twins.md#device-reconnection-flow) bölümü daha fazla bilgi sağlar. 

## <a name="next-steps"></a>Sonraki adımlar

Bu makalede açıklanan kavramların bazılarını denemek için aşağıdaki IoT Hub öğreticileri inceleyin:

* [.NET arka ucu ve .NET cihazını kullanarak IoT Hub modül kimliği ve modül ikizi ile çalışmaya başlama](iot-hub-csharp-csharp-module-twin-getstarted.md)
