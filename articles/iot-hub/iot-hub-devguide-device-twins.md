---
title: Azure IoT Hub cihaz TWINS 'i anlama | Microsoft Docs
description: Geliştirici Kılavuzu-IoT Hub ve cihazlarınız arasında durum ve yapılandırma verilerini eşzamanlı hale getirmek için cihaz ikizlerini kullanın
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: f4db353e3c2f625478df6a547d1b67c5d074d18a
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640620"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>IoT Hub cihaz ikizlerini anlama ve kullanma

*Cihaz TWINS* , meta veriler, konfigürasyonlar ve koşullar dahil olmak üzere cihaz durum BILGILERINI depolayan JSON belgelerdir. Azure IoT Hub, IoT Hub bağlandığınız her cihaz için bir cihaz ikizi tutar. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Bu makalede açıklanır:

* Cihaz ikizi yapısı: *Etiketler*, *istenen* ve *bildirilen özellikler*.
* Cihaz uygulamalarının ve arka uçlarından oluşan işlemler cihaz ikikilerinde gerçekleştirilebilir.

Cihaz ikizlerini şu şekilde kullan:

* Cihaza özel meta verileri bulutta depolayın. Örneğin, bir havalandırma makinesinin dağıtım konumu.

* Cihaz uygulamanızdan kullanılabilir yetenekler ve koşullar gibi geçerli durum bilgilerini bildirin. Örneğin, bir cihaz hücresel veya WiFi üzerinden IoT Hub 'ınıza bağlanır.

* Uzun süre çalışan iş akışlarının durumunu cihaz uygulaması ile arka uç uygulaması arasında eşitler. Örneğin, çözüm arka ucu yüklenecek yeni bellenim sürümünü belirttiğinde ve cihaz uygulaması, güncelleştirme işleminin çeşitli aşamalarını raporlar.

* Cihazınızın meta verilerini, yapılandırmasını veya durumunu sorgulayın.

Bildirilen özellikleri, cihazdan buluta iletileri veya karşıya dosya yüklemeyi kullanma hakkında rehberlik için [cihazdan buluta iletişim kılavuzuna](iot-hub-devguide-d2c-guidance.md) bakın.

İstenen özellikleri, doğrudan yöntemleri veya buluttan cihaza iletileri kullanma hakkında rehberlik için [buluttan cihaza iletişim kılavuzuna](iot-hub-devguide-c2d-guidance.md) bakın.

## <a name="device-twins"></a>Cihaz ikikesi

Cihaz TWINS, cihazla ilgili bilgi depolar:

* Cihaz ve arka uçlar cihaz koşullarını ve yapılandırmayı eşitleyebilmek için kullanabilir.

* Çözüm arka ucu, uzun süre çalışan işlemleri sorgulamak ve hedeflemek için kullanılabilir.

Bir cihaz ikizi yaşam döngüsü, ilgili [cihaz kimliğiyle](iot-hub-devguide-identity-registry.md)bağlantılıdır. IoT Hub ' de bir cihaz kimliği oluşturulduğunda veya silindiğinde, cihaz WINS 'i örtülü olarak oluşturulur ve silinir.

Bir cihaz ikizi şunları içeren bir JSON belgesidir:

* **Etiketler**. JSON belgesinin çözüm arka ucunun okuyave yazabilmesi için bir bölümü. Etiketler cihaz uygulamalarına görünür değildir.

* **İstenen özellikler**. Cihaz yapılandırmasını veya koşullarını eşitlemeye yönelik bildirilen özelliklerle birlikte kullanılır. Çözüm arka ucu, istenen özellikleri ayarlayabilir ve cihaz uygulaması bunları okuyabilir. Cihaz uygulaması, istenen özelliklerde yapılan değişikliklere ilişkin bildirimler de alabilir.

* **Bildirilen özellikler**. Cihaz yapılandırması veya koşulları eşitlemesini sağlamak için istenen özelliklerle birlikte kullanılır. Cihaz uygulaması bildirilen özellikleri ayarlayabilir ve çözüm arka ucu bunları okuyabilir ve sorgulayabilir.

* **Cihaz kimliği özellikleri**. Device ikizi JSON belgesinin kökü, [kimlik kayıt defterinde](iot-hub-devguide-identity-registry.md)depolanan karşılık gelen cihaz kimliğinden salt okunurdur özellikleri içerir.

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

Kök nesnesinde cihaz kimliği özellikleri ve `tags` `desired` hem hem de `reported` özellikleri için kapsayıcı nesneleri bulunur. `$etag``$metadata` `$version` [](iot-hub-devguide-device-twins.md#device-twin-metadata) [](iot-hub-devguide-device-twins.md#optimistic-concurrency) Kapsayıcı, Device ikizi meta verileri ve iyimser eşzamanlılık bölümlerinde açıklanan bazı salt okunurdur (,, ve) öğeleri içerir. `properties`

### <a name="reported-property-example"></a>Bildirilen özellik örneği

Önceki örnekte, Device ikizi cihaz uygulaması tarafından bildirilen bir `batteryLevel` özelliği içerir. Bu özellik, en son bildirilen pil düzeyine dayanarak cihazlarda sorgulama ve işlem yapmayı mümkün kılar. Diğer örnekler, cihaz uygulaması raporlama cihaz yeteneklerini veya bağlantı seçeneklerini içerir.

> [!NOTE]
> Bildirilen özellikler, çözüm arka ucunun bir özelliğin bilinen son değeri ile ilgilendiği senaryoları basitleştirir. Çözüm arka ucunun, zaman serisi gibi zaman damgası bulunan olayların dizileri biçiminde cihaz telemetrisini işlemesi gerekiyorsa [cihazdan buluta iletileri](iot-hub-devguide-messages-d2c.md) kullanın.

### <a name="desired-property-example"></a>İstenen özellik örneği

Önceki örnekte, `telemetryConfig` cihaz ikizi istenen ve bildirilen özellikler, çözüm arka ucu ve cihaz uygulaması tarafından bu cihaz için telemetri yapılandırmasını eşitleyecek şekilde kullanılır. Örneğin:

1. Çözüm arka ucu istenen özelliği istenen yapılandırma değeriyle ayarlar. Belge, istenen özellik kümesine sahip olan bölümüdür:

   ```json
   "desired": {
       "telemetryConfig": {
           "sendFrequency": "5m"
       },
       ...
   },
   ```

2. Cihaz uygulamasına, bağlantı kurulduktan sonra veya ilk kez yeniden bağlanıldığında değişiklik yapılır. Daha sonra cihaz uygulaması, güncelleştirilmiş yapılandırmayı (veya `status` özelliğini kullanarak bir hata koşulunu) raporlar. Bildirilen özelliklerin bölümü aşağıda verilmiştir:

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

* **Cihaz Ikizi kısmen güncelleştirme**. Bu işlem, çözüm arka ucunun, bir cihaz ikizi etiketleri veya istenen özellikleri kısmen güncelleştirmesine olanak sağlar. Kısmi güncelleştirme, herhangi bir özelliği ekleyen veya güncelleştiren bir JSON belgesi olarak ifade edilir. Olarak `null` ayarlanan özellikler kaldırılır. Aşağıdaki örnek, yeni bir `{"newProperty": "newValue"}`istenen özelliği değeri ile oluşturur, var olan `existingProperty` değerinin `"otherNewValue"`üzerine yazar ve kaldırır `otherOldProperty`. İstenen varolan özellikler veya etiketlere başka bir değişiklik yapılmaz:

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

* **İstenen özellikleri değiştirin**. Bu işlem, çözüm arka ucunun, tüm mevcut özellikleri tümüyle üzerine yazmasına ve için `properties/desired`yeni bir JSON belgesi yerine geçecek şekilde olanak sağlar.

* **Etiketleri değiştirin**. Bu işlem, çözüm arka ucunun tüm mevcut etiketlerin üzerine yazılmasına ve yeni bir JSON belgesini `tags`yerine kullanmasına olanak sağlar.

* **İkizi bildirimleri alın**. Bu işlem, ikizi değiştirildiğinde çözüm arka ucunun bildirilmesini sağlar. Bunu yapmak için, IoT çözümünüzün bir rota oluşturması ve veri kaynağını *twinChangeEvents*' e eşit olarak ayarlaması gerekir. Varsayılan olarak, bu tür yollar önceden mevcut olmadığından, hiçbir ikizi bildirimi gönderilmez. Değişiklik hızı çok yüksekse veya iç arızalar gibi diğer nedenlerden dolayı IoT Hub tüm değişiklikleri içeren yalnızca bir bildirim gönderebilir. Bu nedenle, uygulamanızın tüm ara durumların güvenilir denetim ve günlüğe kaydetme ihtiyacı varsa cihazdan buluta iletileri kullanmanız gerekir. İkizi bildirim iletisi, özellikleri ve gövdesi içerir.

  - Özellikler

    | Ad | Değer |
    | --- | --- |
    $content türü | uygulama/json |
    $iothub-enqueuedtime |  Bildirimin gönderildiği zaman |
    $iothub-ileti-kaynak | twinChangeEvents |
    $content kodlaması | UTF-8 |
    deviceId | Cihazın KIMLIĞI |
    hubName | IoT Hub adı |
    operationTimestamp | [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) işlem zaman damgası |
    ıothub-Message-Schema | deviceLifecycleNotification |
    opType | "yeniden kazan" veya "updateTwin" |

    İleti sistemi özelliklerine `$` sembol ön eki eklenir.

  - Body
        
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

* JSON nesnelerindeki tüm anahtarlar büyük/küçük harfe duyarlı 64 bayt UTF-8 UNICODE dizeleridir. İzin verilen karakterler UNICODE denetim karakterlerini (C0 ve C1 kesimleri),, `.`ve `$`SP 'yi hariç tutar.

* JSON nesnelerindeki tüm değerler şu JSON türlerine sahip olabilir: Boolean, Number, String, Object. Dizilere izin verilmiyor. Tamsayılar için maksimum değer 4503599627370495, tamsayıların en küçük değeri ise-4503599627370496 ' dir.

* Etiketler, istenen ve raporlanan özelliklerin tüm JSON nesneleri en fazla 5 derinliğine sahip olabilir. Örneğin, aşağıdaki nesne geçerlidir:

   ```json
   {
       ...
       "tags": {
           "one": {
               "two": {
                   "three": {
                       "four": {
                           "five": {
                               "property": "value"
                           }
                       }
                   }
               }
           }
       },
       ...
   }
   ```

* Tüm dize değerleri en fazla 512 bayt uzunluğunda olabilir.

## <a name="device-twin-size"></a>Cihaz ikizi boyutu

IoT Hub, ve öğelerinin her biri için, salt okuma öğeleri hariç olmak üzere, `tags` `properties/desired`ve `properties/reported`' nin her bir toplam değeri için 8 KB 'lık boyut sınırlaması uygular.

Boyut, UNICODE denetim karakterleri (segment C0 ve C1) ve dize sabitleri dışında kalan boşluklar hariç olmak üzere tüm karakterlerin sayılarak hesaplanır.

IoT Hub, sınırın üzerinde bu belgelerin boyutunu arttırabilecek tüm işlemleri bir hata ile reddeder.

## <a name="device-twin-metadata"></a>Cihaz ikizi meta verileri

IoT Hub, cihazdaki her bir JSON nesnesi için son güncelleştirme zaman damgasını korur, ikizi istenen ve raporlanan Özellikler. Zaman damgaları UTC biçimindedir ve [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) biçiminde `YYYY-MM-DDTHH:MM:SS.mmmZ`kodlanır.

Örneğin:

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

Cihaz ikizi istenen ve bildirilen özelliklerin ETags yoktur, ancak artımlı olması garantilenmiş `$version` bir değer vardır. Bir ETag 'e benzer şekilde, sürüm, güncelleştirmelerin tutarlılığını zorlamak için güncelleştirme partisi tarafından kullanılabilir. Örneğin, bildirilen bir özellik için bir cihaz uygulaması veya istenen bir özellik için çözüm arka ucu.

Sürümler Ayrıca, bir gözlemleme aracısının (örneğin, istenen özellikleri gözlemleme) bir alma işleminin sonucu ve bir güncelleştirme bildirimi arasındaki engelleri bağdaştırılması gerektiğinde de yararlıdır. [Cihaz yeniden bağlantı akışı bölümü](iot-hub-devguide-device-twins.md#device-reconnection-flow) daha fazla bilgi sağlar.

## <a name="device-reconnection-flow"></a>Cihaz yeniden bağlantı akışı

IoT Hub, bağlantısı kesilen cihazlar için istenen özellikleri güncelleştirme bildirimlerini korumaz. Bu, bağlanan bir cihazın, güncelleştirme bildirimleri için abone olmanın yanı sıra, istenen tam Özellikler belgesini alması gerekir. Güncelleştirme bildirimleri ve tam alma arasında KSU olasılığa karşı, aşağıdaki akış, ensred olmalıdır:

1. Cihaz uygulaması bir IoT Hub 'ına bağlanır.
2. Cihaz uygulaması, istenen özellikler güncelleştirme bildirimleri için abone olur.
3. Cihaz uygulaması, istenen özellikler için tüm belgeyi alır.

Cihaz uygulaması, tam alınan belgenin sürümünden daha `$version` az veya eşit olan tüm bildirimleri yok sayabilir. IoT Hub sürümlerinin her zaman artmasını sağladığından bu yaklaşım mümkündür.

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
* [VS Code için Azure IoT araçları ile cihaz yönetimi](iot-hub-device-management-iot-toolkit.md)
