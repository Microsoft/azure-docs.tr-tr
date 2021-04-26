---
title: Azure IoT Central cihaz şablonları nelerdir | Microsoft Docs
description: Azure IoT Central cihaz şablonları, uygulamanıza bağlı cihazların davranışını belirtmenizi sağlar. Cihaz şablonu, cihazın uygulaması gereken telemetri, Özellikler ve komutları belirtir. Bir cihaz şablonu Ayrıca, bir işlecin kullandığı formlar ve panolar gibi IoT Central cihaz için Kullanıcı arabirimini tanımlar.
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 2396768d87b93c4df16b6de78d03faf1d8d1cc2b
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492010"
---
# <a name="what-are-device-templates"></a>Cihaz şablonları nedir?

_Bu makale, cihaz geliştiricileri ve çözüm oluşturucuları için geçerlidir._

Azure IoT Central 'deki bir cihaz şablonu, uygulamanıza bağlanan bir cihaz türünün özelliklerini ve davranışlarını tanımlayan bir şema ' dir. Örneğin, cihaz şablonu, bir cihazın gönderdiği Telemetriyi tanımlar, böylece IoT Central doğru birimleri ve veri türlerini kullanan görselleştirmeler oluşturabilir.

Çözüm Oluşturucu bir IoT Central uygulamasına cihaz şablonları ekler. Bir cihaz geliştiricisi, cihaz şablonunda tanımlanan davranışları uygulayan Cihaz kodunu yazar.

Bir cihaz şablonu aşağıdaki bölümleri içerir:

- _Bir cihaz modeli_. Cihaz şablonunun bu bölümü, cihazın uygulamanızla nasıl etkileşime gireceğini tanımlar. Bir cihaz geliştiricisi modelde tanımlanan davranışları uygular.
    - _Varsayılan bileşen_. Her cihaz modelinde varsayılan bir bileşen vardır. Varsayılan bileşen arabirimi, cihaz modeline özgü özellikleri açıklar.
    - _Bileşenler_. Cihaz modeli, cihaz yeteneklerini betimleyen varsayılan bileşene ek olarak bileşenleri içerebilir. Her bileşenin, bileşenin yeteneklerini açıklayan bir arabirimi vardır. Bileşen arabirimleri, diğer cihaz modellerinde yeniden kullanılabilir. Örneğin, birkaç telefon cihaz modeli aynı kamera arabirimini kullanabilir.
    - _Devralınan arabirimler_. Bir cihaz modeli, varsayılan bileşenin yeteneklerini genişleten bir veya daha fazla arabirim içerir.
- _Bulut özellikleri_. Cihaz şablonunun bu bölümü, çözüm geliştiricisinin depolanacak cihaz meta verilerini belirtmesini sağlar. Bulut özellikleri hiçbir zaman cihazlarla eşitlenmez ve uygulamada mevcut değildir. Bulut özellikleri bir cihaz geliştiricisinin cihaz modelini uygulamak için yazdığı kodu etkilemez.
- _Özelleştirmeler_. Cihaz şablonunun bu bölümü, çözüm geliştiricisinin cihaz modelindeki bazı tanımları geçersiz kılmasına izin verir. Çözüm geliştiricisi, uygulamanın bir özelliğin görünen adını değiştirme veya telemetri değerini göstermek için kullanılan renk gibi bir değeri nasıl işleyeceğini iyileştirmek isterse, özelleştirmeler yararlı olur. Özelleştirmeler bir cihaz geliştiricisinin cihaz modelini uygulamak için yazdığı kodu etkilemez.
- _Görünümler_. Cihaz şablonunun bu bölümü, çözüm geliştiricisinin Cihazdaki verileri görüntülemek ve bir cihazı yönetmek ve denetlemek için form görselleştirmeleri tanımlamasına olanak sağlar. Görünümler cihaz modelini, bulut özelliklerini ve özelleştirmeleri kullanır. Görünümler bir cihaz geliştiricisinin cihaz modelini uygulamak için yazdığı kodu etkilemez.

## <a name="device-models"></a>Cihaz modelleri

Bir cihaz modeli, bir cihazın IoT Central uygulamanızla nasıl etkileşime gireceğini tanımlar. Cihaz geliştiricisi, cihazın cihazı izleyip yönetebilmesi için cihazın cihaz IoT Central modelinde tanımlanan davranışları uyguladığından emin olmalıdır. Bir cihaz modeli bir veya daha fazla _arabirimden_ oluşur ve her arabirim _telemetri_ türlerinin, _cihaz özelliklerinin_ ve _komutlarının_ bir koleksiyonunu tanımlayabilir. Bir çözüm geliştiricisi cihaz modelini bir cihaz şablonuna tanımlayan bir JSON dosyasını içeri aktarabilir veya bir cihaz modeli oluşturmak veya düzenlemek için IoT Central 'de Web Kullanıcı arabirimini kullanabilir. Web Kullanıcı arabirimi kullanılarak oluşturulan bir cihaz modelinde yapılan değişiklikler, [cihaz şablonunun sürümlendirimesini](./howto-version-device-template.md)gerektirir.

Bir çözüm geliştiricisi, cihaz modelini içeren bir JSON dosyasını da dışa aktarabilir. Bir cihaz geliştiricisi, cihazın IoT Central uygulamayla nasıl iletişim kuracağını anlamak için bu JSON belgesini kullanabilir.

Cihaz modelini tanımlayan JSON dosyası [Digital Ikizi tanım dili (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)kullanır. IoT Central, JSON dosyasının ayrı dosyalar yerine satır içi tanımlanmış arabirimlere sahip cihaz modelini içermesini bekler. Daha fazla bilgi için bkz. [ıot Tak ve kullan modelleme Kılavuzu](../../iot-pnp/concepts-modeling-guide.md).

Tipik bir IoT cihazı şu şekilde yapılır:

- Özel parçalar, cihazınızın benzersiz hale getirme işlemleri.
- Tüm cihazlarda ortak olan standart parçalar.

Bu bölümler bir cihaz modelinde _arabirimler_ olarak adlandırılır. Arabirimler, cihazınızın uyguladığı her bir bölümün ayrıntılarını tanımlar. Arabirimler cihaz modelleri arasında yeniden kullanılabilir. DTDL 'de bir bileşen, ayrı bir DTDL dosyasında ya da dosyanın ayrı bir bölümünde tanımlanabilir olan başka bir arabirime başvurur.

Aşağıdaki örnek, bir [sıcaklık denetleyicisi cihazının](https://github.com/Azure/iot-plugandplay-models/blob/main/dtmi/com/example/temperaturecontroller-2.json)cihaz modelinin ana hattını gösterir. Varsayılan bileşen,, ve için tanımlar içerir `workingSet` `serialNumber` `reboot` . Cihaz modeli Ayrıca iki bileşeni `thermostat` ve bir bileşeni içerir `deviceInformation` . Üç bileşenin içeriği, breçekimi 'nin sake 'ı için kaldırılmıştır:

```json
[
  {
    "@context": [
      "dtmi:iotcentral:context;2",
      "dtmi:dtdl:context;2"
    ],
    "@id": "dtmi:com:example:TemperatureController;2",
    "@type": "Interface",
    "contents": [
      {
        "@type": [
          "Telemetry",
          "DataSize"
        ],
        "description": {
          "en": "Current working set of the device memory in KiB."
        },
        "displayName": {
          "en": "Working Set"
        },
        "name": "workingSet",
        "schema": "double",
        "unit": "kibibit"
      },
      {
        "@type": "Property",
        "displayName": {
          "en": "Serial Number"
        },
        "name": "serialNumber",
        "schema": "string",
        "writable": false
      },
      {
        "@type": "Command",
        "commandType": "synchronous",
        "description": {
          "en": "Reboots the device after waiting the number of seconds specified."
        },
        "displayName": {
          "en": "Reboot"
        },
        "name": "reboot",
        "request": {
          "@type": "CommandPayload",
          "description": {
            "en": "Number of seconds to wait before rebooting the device."
          },
          "displayName": {
            "en": "Delay"
          },
          "name": "delay",
          "schema": "integer"
        }
      },
      {
        "@type": "Component",
        "displayName": {
          "en": "thermostat1"
        },
        "name": "thermostat1",
        "schema": "dtmi:com:example:Thermostat;2"
      },
      {
        "@type": "Component",
        "displayName": {
          "en": "thermostat2"
        },
        "name": "thermostat2",
        "schema": "dtmi:com:example:Thermostat;2"
      },
      {
        "@type": "Component",
        "displayName": {
          "en": "DeviceInfo"
        },
        "name": "deviceInformation",
        "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1"
      }
    ],
    "displayName": {
      "en": "Temperature Controller"
    }
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;2",
    "@type": "Interface",
    "displayName": "Thermostat",
    "description": "Reports current temperature and provides desired temperature control.",
    "contents": [
      ...
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:azure:DeviceManagement:DeviceInformation;1",
    "@type": "Interface",
    "displayName": "Device Information",
    "contents": [
      ...
    ]
  }
]
```

Bir arabirimin bazı gerekli alanları vardır:

- `@id`: basit bir Tekdüzen Kaynak adı biçimindeki benzersiz bir KIMLIK.
- `@type`: Bu nesnenin bir arabirim olduğunu bildirir.
- `@context`: arabirim için kullanılan DTDL sürümünü belirtir.
- `contents`: cihazınızı oluşturan özellikleri, telemetri ve komutları listeler. Özellikleri birden çok arabirimde tanımlanabilir.

Özellik modeline, görünen ad ve açıklama gibi daha ayrıntılı bilgi eklemek için kullanabileceğiniz bazı isteğe bağlı alanlar vardır.

Implements bölümündeki arabirimler listesindeki her giriş için şunu vardır:

- `name`: arabirimin programlama adı.
- `schema`: yetenek modelinin uyguladığı arabirim.

## <a name="interfaces"></a>Arabirimler

DTDL, cihazınızın yeteneklerini açıklamanıza olanak sağlar. İlgili yetenekler arabirimler halinde gruplandırılır. Arabirimler, cihazınızın bir parçası olan özellikleri, telemetri ve komutları anlatmaktadır:

- `Properties`. Özellikler, cihazınızın durumunu temsil eden veri alanlarıdır. Bir Coolant göndericisinin yerinde durumu gibi, cihazın dayanıklı durumunu göstermek için özellikleri kullanın. Özellikler Ayrıca cihazın bellenim sürümü gibi temel cihaz özelliklerini de temsil edebilir. Özellikleri salt okunurdur veya yazılabilir olarak bildirebilirsiniz. Yalnızca cihazlar salt okunurdur bir özelliğin değerini güncelleştirebilir. Bir işleç bir cihaza göndermek için yazılabilir bir özelliğin değerini ayarlayabilir.
- `Telemetry`. Telemetri alanları sensörlerden ölçümleri temsil eder. Cihazınız her bir algılayıcı ölçümü aldığında, algılayıcı verilerini içeren bir telemetri olayı göndermelidir.
- `Commands`. Komutlar, cihazınızın kullanıcılarının cihazda yürütebilmesi için yöntemleri temsil eder. Örneğin, bir fanı değiştirme veya kapatma için bir Reset komutu veya komutu.

Aşağıdaki örnekte, termostat arabirim tanımı gösterilmektedir:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;2",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "targetTemperature",
      "schema": "double",
      "displayName": "Target Temperature",
      "description": "Allows to remotely specify the desired target temperature.",
      "unit": "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit": "degreeCelsius",
      "displayName": "Max temperature since last reboot.",
      "description": "Returns the max temperature since last device reboot."
    },
    {
      "@type": "Command",
      "name": "getMaxMinReport",
      "displayName": "Get Max-Min report.",
      "description": "This command returns the max, min and average temperature from the specified time to the current time.",
      "request": {
        "name": "since",
        "displayName": "Since",
        "description": "Period to return the max-min report.",
        "schema": "dateTime"
      },
      "response": {
        "name": "tempReport",
        "displayName": "Temperature Report",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "maxTemp",
              "displayName": "Max temperature",
              "schema": "double"
            },
            {
              "name": "minTemp",
              "displayName": "Min temperature",
              "schema": "double"
            },
            {
              "name": "avgTemp",
              "displayName": "Average Temperature",
              "schema": "double"
            },
            {
              "name": "startTime",
              "displayName": "Start Time",
              "schema": "dateTime"
            },
            {
              "name": "endTime",
              "displayName": "End Time",
              "schema": "dateTime"
            }
          ]
        }
      }
    }
  ]
}
```

Bu örnekte, iki Özellik (bir salt okunurdur ve bir yazılabilir), telemetri türü ve bir komut gösterilmektedir. En az bir alan açıklamasında şunu vardır:

- `@type` özelliğin türünü belirtmek için: `Telemetry` , `Property` , veya `Command` .  Bazı durumlarda tür, değerin nasıl işleneceği hakkında bazı varsayımlar yapmak IoT Central sağlamak için bir anlamsal tür içerir.
- `name` Telemetri değeri için.
- `schema` Telemetri veya özelliğin veri türünü belirtmek için. Bu değer, Double, Integer, Boolean veya String gibi bir temel tür olabilir. Karmaşık nesne türleri ve eşlemeler de desteklenir.

Görünen ad ve açıklama gibi isteğe bağlı alanlar, arabirime ve yeteneklere daha fazla ayrıntı eklemenizi sağlar.

## <a name="properties"></a>Özellikler

Varsayılan olarak, özellikler salt okunurdur. Salt okuma özellikleri, cihazın IoT Central uygulamanızdaki Özellik değeri güncelleştirmelerini bildirdiği anlamına gelir. IoT Central uygulamanız salt okunurdur bir özelliğin değerini ayarlayamıyorum.

Ayrıca, bir özelliği bir arabirimde yazılabilir olarak işaretleyebilirsiniz. Bir cihaz, IoT Central uygulamanızdan yazılabilir bir özelliğe güncelleştirme alabilir ve uygulamanıza yönelik rapor özellik değeri güncelleştirmelerini de alabilir.

Özellik değerlerini ayarlamak için cihazların bağlı olması gerekmez. Güncelleştirilmiş değerler cihaz bir sonraki uygulamaya bağlandıktan sonra aktarılır. Bu davranış hem salt okunurdur hem de yazılabilir özellikler için geçerlidir.

Cihazınızdan telemetri göndermek için özellikleri kullanmayın. Örneğin, gibi bir salt okunur özelliği, `temperatureSetting=80` cihaz sıcaklığının 80 olarak ayarlandığı ve cihazın bu sıcaklığa veya bu sıcaklığın devam etmesi için çalıştığı anlamına gelir.

Yazılabilir özellikler için, cihaz uygulaması, özellik değerini alınıp uygulamadığını göstermek için istenen bir durum durum kodu, sürüm ve açıklama döndürür.

## <a name="telemetry"></a>Telemetri

IoT Central, panolar ve grafiklerde Telemetriyi görüntülemenizi ve eşiklere ulaşıldığında eylemleri tetiklemek için kuralları kullanmanızı sağlar. IoT Central telemetri değerlerinin nasıl görüntüleneceğini anlamak için, veri türleri, birimler ve görünen adlar gibi cihaz modelindeki bilgileri kullanır.

IoT Central verilerini dışa aktarma özelliğini kullanarak depolama veya Event Hubs gibi diğer hedeflere telemetri akışını sağlayabilirsiniz.

## <a name="commands"></a>Komutlar

Komutun varsayılan olarak 30 saniye içinde yürütülmesi gerekir ve komut geldiğinde cihazın bağlanması gerekir. Cihaz zaman yanıt verirse veya cihaz bağlı değilse, komut başarısız olur.

Komutların istek parametreleri olabilir ve bir yanıt döndürebilir.

### <a name="offline-commands"></a>Çevrimdışı komutlar

Cihaz şablonundaki bir komutla ilgili olarak, bir cihaz çevrimdışıyken **sırayı** etkinleştirerek kuyruk komutları ' nı seçebilirsiniz.

Çevrimdışı komutlar, çözümünüzde cihaza yönelik tek yönlü bildirimlerdir. Çevrimdışı komutların istek parametreleri olabilir ancak yanıt döndürmez.

> [!NOTE]
> Bu seçenek yalnızca IoT Central Web Kullanıcı arabiriminde kullanılabilir. Bu ayar, cihaz şablonundan bir modeli veya arabirimi dışa aktardığınızda dahil değildir.

## <a name="cloud-properties"></a>Bulut özellikleri

Bulut özellikleri cihaz şablonunun bir parçasıdır, ancak cihaz modelinin bir parçası değildir. Bulut özellikleri, çözüm geliştiricisinin IoT Central uygulamasında depolanacak cihaz meta verilerini belirtmesini sağlar. Bulut özellikleri bir cihaz geliştiricisinin cihaz modelini uygulamak için yazdığı kodu etkilemez.

Bir çözüm geliştiricisi, uygulamaya bağlı cihazları yönetmek için bir operatör etkinleştirmek üzere cihaz özellikleriyle birlikte panolara ve görünümlere bulut özellikleri ekleyebilir. Bir çözüm geliştiricisi, bir eşik değerini bir operatör tarafından düzenlenebilir hale getirmek için bir kural tanımının parçası olarak bulut özelliklerini de kullanabilir.

## <a name="customizations"></a>Özelleştirmeler

Özelleştirmeler cihaz şablonunun bir parçasıdır, ancak cihaz modelinin bir parçası değildir. Özelleştirmeler, çözüm geliştiricisinin cihaz modelindeki bazı tanımları geliştirmesine veya geçersiz kılmasına izin verir. Örneğin, bir çözüm geliştiricisi bir telemetri türü veya özelliği için görünen adı değiştirebilir. Bir çözüm geliştiricisi, bir dize cihaz özelliği için minimum veya maksimum uzunluk gibi doğrulama eklemek için özelleştirmeleri de kullanabilir.

Özelleştirmeler bir cihaz geliştiricisinin cihaz modelini uygulamak için yazdığı kodu etkileyebilir. Örneğin, bir özelleştirme minimum ve maksimum dize uzunluklarını veya minimum ve maksimum sayı değerlerini telemetri için ayarlayabilir.

## <a name="views"></a>Görünümler

Bir çözüm geliştiricisi, operatörlerin bağlı cihazları izlemesine ve yönetmesine olanak sağlayan görünümler oluşturur. Görünümler cihaz şablonunun bir parçasıdır, bu nedenle bir görünüm belirli bir cihaz türüyle ilişkilendirilir. Bir görünüm şunları içerebilir:

- Telemetriyi çizilecek grafikler.
- Salt okuma cihaz özelliklerini görüntüleyen kutucuklar.
- , İşlecin yazılabilir cihaz özelliklerini düzenlemesini sağlamak için kutucuklar.
- İşlecin bulut özelliklerini düzenlemesini sağlamak için kutucuklar.
- Bir yük bekleyen komutlar da dahil olmak üzere operatör çağrı komutlarına izin veren kutucuklar.
- Etiketleri, resimleri veya markı metnini görüntüleyen kutucuklar.

Bir görünüme ekleyebileceğiniz telemetri, Özellikler ve komutlar cihaz şablonundaki cihaz modeli, bulut özellikleri ve özelleştirmeler tarafından belirlenir.

## <a name="next-steps"></a>Sonraki adımlar

Cihaz geliştiricisi olarak cihaz şablonları hakkında bilgi edindiğinize göre, bir sonraki adım önerilir IoT Central bir cihaz alışverişi hakkında daha fazla bilgi edinmek için [telemetri, özellik ve komut yüklerini](./concepts-telemetry-properties-commands.md) okumalıdır.

Çözüm geliştiricisi olarak bir sonraki adım, bir cihaz şablonu oluşturma hakkında daha fazla bilgi edinmek için [Azure IoT Central uygulamanızda yeni bir IoT cihaz türü tanımlama](./howto-set-up-template.md) ' ı okumalıdır.
