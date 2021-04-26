---
title: Azure IoT Hub cihaz TWINS 'i anlama | Microsoft Docs
description: Geliştirici Kılavuzu-IoT Hub ve cihazlarınız arasında durum ve yapılandırma verilerini eşzamanlı hale getirmek için cihaz ikizlerini kullanın
author: nehsin
manager: philmea
ms.author: nehsin
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom:
- mqtt
- 'Role: Cloud Development'
ms.openlocfilehash: fff4b9157c30203f47c65a74b211e3dbf6426d92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101093039"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>IoT Hub cihaz ikizlerini anlama ve kullanma

*Cihaz TWINS* , meta veriler, konfigürasyonlar ve koşullar dahil olmak üzere cihaz durum BILGILERINI depolayan JSON belgelerdir. Azure IoT Hub, IoT Hub'a bağladığınız her cihaz için bir cihaz çifti tutar. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Bu makalede şunları açıklanmaktadır:

* Cihaz ikizi yapısı: *Etiketler*, *istenen* ve *bildirilen özellikler*.
* Cihaz uygulamalarının ve arka uçlarından oluşan işlemler cihaz ikikilerinde gerçekleştirilebilir.

Cihaz ikizlerini şu şekilde kullan:

* Cihaza özel meta verileri bulutta depolayın. Örneğin, bir havalandırma makinesinin dağıtım konumu.

* Cihaz uygulamanızdan kullanılabilir yetenekler ve koşullar gibi geçerli durum bilgilerini bildirin. Örneğin, bir cihaz hücresel veya WiFi üzerinden IoT Hub 'ınıza bağlanır.

* Uzun süre çalışan iş akışlarının durumunu cihaz uygulaması ile arka uç uygulaması arasında eşitler. Örneğin, çözüm arka ucu yüklenecek yeni bellenim sürümünü belirttiğinde ve cihaz uygulaması, güncelleştirme işleminin çeşitli aşamalarını raporlar.

* Cihazınızın meta verilerini, yapılandırmasını veya durumunu sorgulayın.

Bildirilen özellikleri, cihazdan buluta iletileri veya karşıya dosya yüklemeyi kullanma hakkında rehberlik için [cihazdan buluta iletişim kılavuzuna](iot-hub-devguide-d2c-guidance.md) bakın.

İstenen özellikleri, doğrudan yöntemleri veya buluttan cihaza iletileri kullanma hakkında rehberlik için [buluttan cihaza iletişim kılavuzuna](iot-hub-devguide-c2d-guidance.md) bakın.

Cihaz TWINS 'in bir Azure IoT Tak ve Kullan cihazı tarafından kullanılan cihaz modeliyle ilişkisini öğrenmek için bkz. [ıot Tak ve kullan dijital TWINS 'ı anlama](../iot-pnp/concepts-digital-twin.md).

## <a name="device-twins"></a>Cihaz ikikesi

Cihaz TWINS, cihazla ilgili bilgi depolar:

* Cihaz ve arka uçlar cihaz koşullarını ve yapılandırmayı eşitleyebilmek için kullanabilir.

* Çözüm arka ucu, uzun süre çalışan işlemleri sorgulamak ve hedeflemek için kullanılabilir.

Bir cihaz ikizi yaşam döngüsü, ilgili [cihaz kimliğiyle](iot-hub-devguide-identity-registry.md)bağlantılıdır. IoT Hub ' de bir cihaz kimliği oluşturulduğunda veya silindiğinde, cihaz WINS 'i örtülü olarak oluşturulur ve silinir.

Bir cihaz ikizi şunları içeren bir JSON belgesidir:

* **Etiketler**. JSON belgesinin çözüm arka ucunun okuyave yazabilmesi için bir bölümü. Etiketler cihaz uygulamalarına görünür değildir.

* **İstenen özellikler**. Cihaz yapılandırmasını veya koşullarını eşitlemeye yönelik bildirilen özelliklerle birlikte kullanılır. Çözüm arka ucu, istenen özellikleri ayarlayabilir ve cihaz uygulaması bunları okuyabilir. Cihaz uygulaması, istenen özelliklerde yapılan değişikliklere ilişkin bildirimler de alabilir.

* **Bildirilen özellikler**. Cihaz yapılandırması veya koşulları eşitlemesini sağlamak için istenen özelliklerle birlikte kullanılır. Cihaz uygulaması bildirilen özellikleri ayarlayabilir ve çözüm arka ucu bunları okuyabilir ve sorgulayabilir.

* **Cihaz kimliği özellikleri**. Device ikizi JSON belgesinin kökü, [kimlik kayıt defterinde](iot-hub-devguide-identity-registry.md)depolanan karşılık gelen cihaz kimliğinden salt okunurdur özellikleri içerir. Özellikler `connectionStateUpdatedTime` ve `generationId` dahil edilmez.

![Cihaz ikizi özelliklerinin ekran görüntüsü](./media/iot-hub-devguide-device-twins/twin.png)

Aşağıdaki örnekte bir Device ikizi JSON belgesi gösterilmektedir:

```json
{
    "deviceId": "devA",
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

Kök nesnesinde cihaz kimliği özellikleri ve hem hem de özellikleri için kapsayıcı nesneleri bulunur `tags` `reported` `desired` . `properties`Kapsayıcı, `$metadata` `$etag` `$version` [Device ikizi meta verileri](iot-hub-devguide-device-twins.md#device-twin-metadata) ve [iyimser eşzamanlılık](iot-hub-devguide-device-twins.md#optimistic-concurrency) bölümlerinde açıklanan bazı salt okunurdur (,, ve) öğeleri içerir.

### <a name="reported-property-example"></a>Bildirilen özellik örneği

Önceki örnekte, Device ikizi `batteryLevel` cihaz uygulaması tarafından bildirilen bir özelliği içerir. Bu özellik, en son bildirilen pil düzeyine dayanarak cihazlarda sorgulama ve işlem yapmayı mümkün kılar. Diğer örnekler, cihaz uygulaması raporlama cihaz yeteneklerini veya bağlantı seçeneklerini içerir.

> [!NOTE]
> Bildirilen özellikler, çözüm arka ucunun bir özelliğin bilinen son değeri ile ilgilendiği senaryoları basitleştirir. Çözüm arka ucunun, zaman serisi gibi zaman damgası bulunan olayların dizileri biçiminde cihaz telemetrisini işlemesi gerekiyorsa [cihazdan buluta iletileri](iot-hub-devguide-messages-d2c.md) kullanın.

### <a name="desired-property-example"></a>İstenen özellik örneği

Önceki örnekte, `telemetryConfig` cihaz ikizi istenen ve bildirilen özellikler, çözüm arka ucu ve cihaz uygulaması tarafından bu cihaz için telemetri yapılandırmasını eşitleyecek şekilde kullanılır. Örnek:

1. Çözüm arka ucu istenen özelliği istenen yapılandırma değeriyle ayarlar. Belge, istenen özellik kümesine sahip olan bölümüdür:

   ```json
   "desired": {
       "telemetryConfig": {
           "sendFrequency": "5m"
       },
       ...
   },
   ```

2. Cihaz uygulamasına, bağlantı kurulduktan sonra veya ilk kez yeniden bağlanıldığında değişiklik yapılır. Daha sonra cihaz uygulaması, güncelleştirilmiş yapılandırmayı (veya özelliğini kullanarak bir hata koşulunu `status` ) raporlar. Bildirilen özelliklerin bölümü aşağıda verilmiştir:

   ```json
   "reported": {
       "telemetryConfig": {
           "sendFrequency": "5m",
           "status": "success"
       }
       ...
   }
   ```

3. Çözüm arka ucu, cihaz TWINS 'i [sorgulayarak](iot-hub-devguide-query-language.md) , yapılandırma işleminin sonuçlarını birçok cihazda izleyebilir.

> [!NOTE]
> Yukarıdaki kod parçacıkları, bir cihaz yapılandırmasını ve durumunu kodlamak için en iyi duruma getirilmiş örneklerdir. IoT Hub, cihaz TWINS 'te istenen ve bildirilen özellikler ikizi cihaz için belirli bir şema uygulamaz.
> 

Üretici yazılımı güncelleştirmeleri gibi uzun süreli işlemleri eşleştirmek için TWINS kullanabilirsiniz. Cihazlarda uzun süren bir işlemi senkronize etmek ve izlemek için özellikleri kullanma hakkında daha fazla bilgi için bkz. [cihazları yapılandırmak için istenen özellikleri kullanma](tutorial-device-twins.md).

## <a name="back-end-operations"></a>Arka uç işlemleri

Çözüm arka ucu, HTTPS üzerinden sunulan aşağıdaki atomik işlemleri kullanarak cihaz ikizi üzerinde çalışır:

* **İkizi CIHAZ kimliğini alın**. Bu işlem, Etiketler ve istenen ve bildirilen sistem özellikleri dahil olmak üzere Device ikizi belgesini döndürür.

* **Cihaz Ikizi kısmen güncelleştirme**. Bu işlem, çözüm arka ucunun, bir cihaz ikizi etiketleri veya istenen özellikleri kısmen güncelleştirmesine olanak sağlar. Kısmi güncelleştirme, herhangi bir özelliği ekleyen veya güncelleştiren bir JSON belgesi olarak ifade edilir. Olarak ayarlanan özellikler `null` kaldırılır. Aşağıdaki örnek, yeni bir istenen özelliği değeri ile oluşturur `{"newProperty": "newValue"}` , var olan değerinin üzerine yazar `existingProperty` `"otherNewValue"` ve kaldırır `otherOldProperty` . İstenen varolan özellikler veya etiketlere başka bir değişiklik yapılmaz:

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

* **İkizi bildirimleri alın**. Bu işlem, ikizi değiştirildiğinde çözüm arka ucunun bildirilmesini sağlar. Bunu yapmak için, IoT çözümünüzün bir rota oluşturması ve veri kaynağını *twinChangeEvents*' e eşit olarak ayarlaması gerekir. Varsayılan olarak, bu tür yollar önceden mevcut olmadığından, hiçbir ikizi bildirimi gönderilmez. Değişiklik hızı çok yüksekse veya iç arızalar gibi diğer nedenlerden dolayı IoT Hub tüm değişiklikleri içeren yalnızca bir bildirim gönderebilir. Bu nedenle, uygulamanızın tüm ara durumların güvenilir denetim ve günlüğe kaydetme ihtiyacı varsa cihazdan buluta iletileri kullanmanız gerekir. İkizi bildirim iletisi, özellikleri ve gövdesi içerir.

  - Özellikler

    | Name | Değer |
    | --- | --- |
    $content türü | uygulama/json |
    $iothub-enqueuedtime |  Bildirimin gönderildiği zaman |
    $iothub-ileti-kaynak | twinChangeEvents |
    $content kodlaması | UTF-8 |
    deviceId | Cihazın KIMLIĞI |
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

Önceki tüm işlemler [iyimser eşzamanlılığı](iot-hub-devguide-device-twins.md#optimistic-concurrency) destekler ve [IoT Hub erişimi denetim](iot-hub-devguide-security.md)bölümünde tanımlandığı şekilde **serviceconnect** iznini gerektirir.

Bu işlemlere ek olarak çözüm arka ucu şunları yapabilir:

* SQL benzeri [IoT Hub sorgu dilini](iot-hub-devguide-query-language.md)kullanarak cihaz TWINS 'i sorgulayın.

* [İşleri](iot-hub-devguide-jobs.md)kullanan büyük cihaz kümesi kümelerinde işlemler gerçekleştirin.

## <a name="device-operations"></a>Cihaz işlemleri

Cihaz uygulaması, aşağıdaki atomik işlemleri kullanarak cihaz ikizi üzerinde çalışır:

* **Cihaz Ikizi alma**. Bu işlem, bağlı durumdaki cihaz için cihaz ikizi belgesini (istenen ve bildirilen sistem özellikleri dahil) döndürür. (Etiketler cihaz uygulamalarına görünmez.)

* **Bildirilen özellikleri kısmen güncelleştirme**. Bu işlem, bağlı durumda olan aygıtın bildirilen özelliklerinin kısmi güncelleştirilmesini mümkün. Bu işlem, çözüm arka ucunun istenen özelliklerin kısmi güncelleştirilmesi için kullandığı JSON güncelleştirme biçimini kullanır.

* **İstenen özellikleri gözlemleyin**. Şu anda bağlı olan cihaz, ne zaman meydana gelediklerinde istenen özellikler için güncelleştirmeler bildirilmesini seçebilir. Cihaz, çözüm arka ucu tarafından yürütülen aynı güncelleştirme formunu (kısmi veya tam değiştirme) alır.

Önceki tüm işlemler, [IoT Hub erişimi denetim](iot-hub-devguide-security.md)bölümünde tanımlandığı gibi **deviceconnect** iznini gerektirir.

[Azure IoT cihaz SDK 'ları](iot-hub-devguide-sdks.md) , önceki işlemleri birçok dil ve platformda kullanmayı kolaylaştırır. İstenen özellikler eşitlemesine yönelik IoT Hub temel elemanların ayrıntıları hakkında daha fazla bilgi için bkz. [cihaz yeniden bağlantı akışı](iot-hub-devguide-device-twins.md#device-reconnection-flow).

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

## <a name="device-twin-size"></a>Cihaz ikizi boyutu

IoT Hub değerinde 8 KB boyutunda bir boyut sınırı `tags` ve and değeri üzerinde her biri 32 KB boyut sınırı zorlar `properties/desired` `properties/reported` . Bu toplamlar, ve gibi salt yazılır öğelerin dışlanmalıdır `$etag` `$version` `$metadata/$lastUpdated` .

İkizi boyutu şu şekilde hesaplanır:

* JSON belgesindeki her bir özellik için, IoT Hub üst üste hesaplar ve özelliğin anahtar ve değerinin uzunluğunu ekler.

* Özellik anahtarları UTF8 kodlu dizeler olarak değerlendirilir.

* Basit özellik değerleri, UTF8 kodlu dizeler, sayısal değerler (8 bayt) veya Boole değerleri (4 bayt) olarak değerlendirilir.

* UTF8 kodlu dizelerin boyutu, UNICODE denetim karakterleri (segment C0 ve C1) hariç tüm karakterlerin sayılarak hesaplanır.

* Karmaşık özellik değerleri (iç içe geçmiş nesneler), içerdikleri özellik anahtarlarının ve özellik değerlerinin toplam boyutu temel alınarak hesaplanır.

IoT Hub `tags` ,, `properties/desired` veya sınırın üzerindeki belgelerin boyutunu artıracak tüm işlemler hata vererek reddeder `properties/reported` .

## <a name="device-twin-metadata"></a>Cihaz ikizi meta verileri

IoT Hub, cihazdaki her bir JSON nesnesi için son güncelleştirme zaman damgasını korur, ikizi istenen ve raporlanan Özellikler. Zaman damgaları UTC biçimindedir ve [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) biçiminde kodlanır `YYYY-MM-DDTHH:MM:SS.mmmZ` .

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
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
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

Cihaz ikizi istenen ve bildirilen özelliklerin ETags yoktur, ancak `$version` artımlı olması garantilenmiş bir değer vardır. Bir ETag 'e benzer şekilde, sürüm, güncelleştirmelerin tutarlılığını zorlamak için güncelleştirme partisi tarafından kullanılabilir. Örneğin, bildirilen bir özellik için bir cihaz uygulaması veya istenen bir özellik için çözüm arka ucu.

Sürümler Ayrıca, bir gözlemleme aracısının (örneğin, istenen özellikleri gözlemleme) bir alma işleminin sonucu ve bir güncelleştirme bildirimi arasındaki engelleri bağdaştırılması gerektiğinde de yararlıdır. [Cihaz yeniden bağlantı akışı bölümü](iot-hub-devguide-device-twins.md#device-reconnection-flow) daha fazla bilgi sağlar.

## <a name="device-reconnection-flow"></a>Cihaz yeniden bağlantı akışı

IoT Hub, bağlantısı kesilen cihazlar için istenen özellikleri güncelleştirme bildirimlerini korumaz. Bu, bağlanan bir cihazın, güncelleştirme bildirimleri için abone olmanın yanı sıra, istenen tam Özellikler belgesini alması gerekir. Güncelleştirme bildirimleri ve tam alma arasında KSU olasılığa karşı, aşağıdaki akış, ensred olmalıdır:

1. Cihaz uygulaması bir IoT Hub 'ına bağlanır.
2. Cihaz uygulaması, istenen özellikler güncelleştirme bildirimleri için abone olur.
3. Cihaz uygulaması, istenen özellikler için tüm belgeyi alır.

Cihaz uygulaması, `$version` tam alınan belgenin sürümünden daha az veya eşit olan tüm bildirimleri yok sayabilir. IoT Hub sürümlerinin her zaman artmasını sağladığından bu yaklaşım mümkündür.

> [!NOTE]
> Bu mantık, [Azure IoT cihaz SDK](iot-hub-devguide-sdks.md)'lerinde zaten uygulanmış. Bu açıklama yalnızca cihaz uygulaması Azure IoT cihaz SDK 'larının hiçbirini kullanamaz ve MQTT arabirimini doğrudan program,
> 

## <a name="additional-reference-material"></a>Ek başvuru malzemeleri

IoT Hub geliştirici kılavuzundaki diğer başvuru konuları şunları içerir:

* [IoT Hub uç noktaları](iot-hub-devguide-endpoints.md) makalesinde her bir IoT Hub 'ının çalışma zamanı ve yönetim işlemleri için sunduğu çeşitli uç noktalar açıklanmaktadır.

* [Kısıtlama ve Kotalar](iot-hub-devguide-quotas-throttling.md) makalesinde IoT Hub hizmetine uygulanan kotalar ve hizmeti kullandığınızda bekleneceğiniz azaltma davranışı açıklanmaktadır.

* [Azure IoT cihaz ve hizmet SDK 'ları](iot-hub-devguide-sdks.md) makalesinde, IoT Hub etkileşimde bulunan cihaz ve hizmet uygulamaları geliştirirken kullanabileceğiniz çeşitli dil SDK 'ları listelenir.

* [Cihaz TWINS, işler ve ileti yönlendirme makalesinde IoT Hub sorgu dili,](iot-hub-devguide-query-language.md) cihaz ikikinizin ve işleriniz hakkında IoT Hub bilgi almak için kullanabileceğiniz IoT Hub sorgu dilini açıklar.

* [Mqtt IoT Hub destek](iot-hub-mqtt-support.md) makalesi, MQTT protokolü için IoT Hub desteği hakkında daha fazla bilgi sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Artık cihaz ikörleriyle ilgili olarak öğrendiğinize göre, aşağıdaki IoT Hub Geliştirici Kılavuzu konularıyla ilgileniyor olabilirsiniz:

* [IoT Hub modül TWINS 'i anlayın ve kullanın](iot-hub-devguide-module-twins.md)
* [Bir cihazda doğrudan yöntem çağırma](iot-hub-devguide-direct-methods.md)
* [Birden fazla cihazda işleri zamanlama](iot-hub-devguide-jobs.md)

Bu makalede açıklanan kavramların bazılarını denemek için aşağıdaki IoT Hub öğreticileri inceleyin:

* [Cihaz ikizi kullanma](iot-hub-node-node-twin-getstarted.md)
* [Cihaz ikizi özelliklerini kullanma](tutorial-device-twins.md)
* [VS Code için Azure IoT Araçları ile cihaz yönetimi](iot-hub-device-management-iot-toolkit.md)
