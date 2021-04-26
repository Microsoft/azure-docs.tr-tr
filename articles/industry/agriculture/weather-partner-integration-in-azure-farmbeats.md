---
title: Hava durumu iş ortağı tümleştirmesi
description: Hava durumu veri sağlayıcısının, Farmtts ile nasıl tümleştirileceği hakkında bilgi edinin.
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: f0fbd93e2a5f4e92089e10e75dc17e304ff80bf6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93147088"
---
# <a name="weather-partner-integration-with-farmbeats"></a>Farmtempolar ile hava durumu ortağı tümleştirmesi

Bu makalede Azure Farmker Connector Docker bileşeni hakkında bilgi sağlanır. Hava durumu veri sağlayıcısı olarak, bağlayıcı Docker 'ı kullanarak Farmker 'Lar ile tümleştirme yapabilirsiniz. Hava durumu verilerini Farmtlerine göndermek için API 'Lerini kullanın. Bu veriler, verileri Fusion 'ta ve makine öğrenimi modelleri ya da yapay zeka modelleri oluşturmak için kullanılabilir.

 > [!NOTE]
 > Bu makalede, Ulusal Okyanus ve atmosfer yönetimi (NOAA) ile Azure açık veri kümeleri ve hava durumu verileri kullanılarak oluşturulan bir [başvuru uygulamasını](https://github.com/azurefarmbeats/noaa_docker) kullanıyoruz. Ayrıca, ilgili [Docker görüntüsünü](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa)de kullanırız.

[Uygun bir Docker görüntüsü veya programı](#docker-specifications) sağlamalısınız ve Docker görüntüsünü, müşterilerin erişebileceği bir kapsayıcı kayıt defterinde barındırmalısınız. Müşterilerinize aşağıdaki bilgileri sağlayın:

- Docker görüntü URL 'SI
- Docker resim etiketi
- Docker görüntüsüne erişmek için anahtarlar veya kimlik bilgileri
- Sisteminizdeki verilere erişmek için müşteriye özgü API anahtarları veya kimlik bilgileri
- VM SKU 'SU ayrıntıları (Docker görüntünüz belirli VM gereksinimlerine sahipse bu ayrıntıları sağlayın. Aksi takdirde, müşteriler Azure 'da desteklenen VM SKU 'Ları arasından seçim yapabilir.)

Müşteriler bu Docker bilgilerini, bir hava durumu ortağını kendi Farmker örneğine kaydetmek için kullanır. Müşterilerin, Farmınts 'deki Hava durumu verilerini almak için Docker 'ı nasıl kullanabileceği hakkında daha fazla bilgi için bkz. [Hava durumu ortaklarından veri alma](./get-weather-data-from-weather-partner.md).

## <a name="connector-docker-development"></a>Bağlayıcı Docker geliştirme

**REST API tabanlı tümleştirme**

Farmtts API 'Leri Swagger teknik belgelerini içerir. API 'Ler ve bunlara karşılık gelen istekler ya da yanıtları hakkında daha fazla bilgi için bkz. [Farmtıtlar Swagger](https://aka.ms/farmbeatsswagger). 

Daha önce Farmtts yüklediyseniz, `https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger`

*-API* 'nin Farmtts Web sitenizin adına eklendiğine dikkat edin. API uç noktası `https://yourfarmbeatswebsitename-api.azurewebsites.net`

### <a name="datahub-lib"></a>Datahub kitaplığı

Farmtler, kullanabileceğiniz bir lib sağlar. LIB, [başvuru uygulamasının bir parçası](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib)olarak şu anda kullanılabilir. Daha sonra, birden çok dil için SDK olarak kullanılabilir olacaktır.

### <a name="authentication"></a>Kimlik Doğrulaması

**Farmtempts API 'Leri ile kimlik doğrulama**

Farmtts, taşıyıcı kimlik doğrulamasını kullanır. İsteğin üstbilgi bölümünde bir erişim belirteci sağlayarak API 'Lerine erişebilirsiniz. Aşağıda bir örnek verilmiştir:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

Erişim belirtecini, müşterinin Farmtts örneğinde çalışan bir Azure Işlevleri uygulamasından isteyebilirsiniz. Azure Işlevleri URL 'SI, Docker programına bir bağımsız değişken olarak sağlanır. URL üzerinde bir istek yaparak erişim belirtecini alabilirsiniz `GET` . URL 'den gelen yanıt erişim belirtecini içerir. 

Erişim belirtecini almak için veri hub 'ı kitaplığı 'ndaki yardımcı işlevleri kullanın. Daha fazla bilgi için, [NOAA Docker görüntüsünün GitHub sayfasına](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py)bakın.

Erişim belirteci yalnızca birkaç saat için geçerlidir. Süresi sona erdiğinde, yeniden istemeniz gerekir.

**İş ortağı tarafı API 'Leri ile kimlik doğrulama**

Docker işi çalışırken iş ortağı tarafı API 'Lerinde kimlik doğrulaması yapmak için, müşterilerin iş ortağı kaydı sırasında kimlik bilgilerini sağlaması gerekir. Aşağıda bir örnek verilmiştir:

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
API hizmeti bu dict 'yi seri hale getirir ve bir [anahtar kasasında](../../key-vault/general/basic-concepts.md)depolar.

[Azure Data Factory](../../data-factory/introduction.md) , hava durumu işlerini düzenlemek için kullanılır. Docker kodunu çalıştırmak için kaynakları dönerek. Data Factory Ayrıca, Docker işinin çalıştığı sanal makineye güvenli bir şekilde veri göndermek için bir mekanizma sağlar. API kimlik bilgileri, anahtar kasasında güvenli bir şekilde depolanır. 

Kimlik bilgileri, anahtar kasasından güvenli dizeler olarak okunurdur. Docker kapsayıcısının çalışma dizininde genişletilmiş özellikler olarak sağlanırlar. Dosya yolu */mnt/working_dir/activity.js'* dir. 

Docker kodu, müşterinin iş ortağı tarafı API 'Lerine erişmek için çalışma süresi boyunca *activity.jsüzerindeki* kimlik bilgilerini okuyabilir. JSON dosyasında, kimlik bilgileri şu kod örneğine benzer şekilde görünür:

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
`partnerCredentials`Kimlik bilgileri, müşterinin iş ortağı kaydı sırasında sağlandığı şekilde kullanılabilir.

Farmtts kitaplığı yardımcı işlevler sağlar. Etkinlik özelliklerinden kimlik bilgilerini okumak için bu işlevleri kullanın. Daha fazla bilgi için, [NOAA Docker görüntüsünün GitHub sayfasına](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py)bakın.

Dosya yalnızca Docker kodu çalışırken kullanılır. Kod bittikten sonra dosya silinir.

Data Factory ardışık düzen ve etkinliklerin nasıl çalıştığı hakkında daha fazla bilgi için bkz. [şema ve veri türü eşleme](../../data-factory/copy-activity-schema-and-type-mapping.md).

**HTTP istek üstbilgileri**

Aşağıdaki tabloda, Farmtts 'ye bir API çağrısı yaptığınızda belirtmeniz gereken en yaygın istek üstbilgileri gösterilmektedir.

Üst bilgi | Açıklama ve örnek
--- | ---
İçerik Türü | İstek biçimi. Örnek: `Content-Type: application/<format>` <br/>Farmrets veri hub 'ı API 'Leri için, biçim JSON olur. Örnek: ` Content-Type: application/json`
Yetkilendirme | API çağrısı yapmak için gereken erişim belirteci. Örnek: `Authorization: Bearer <Access-Token>`
Kabul Et | Yanıt biçimi. Farmrets veri hub 'ı API 'Leri için, biçim JSON olur. Örnek: `Accept: application/json`

## <a name="data-format"></a>Veri biçimi

JSON, rastgele veri yapılarının basit bir metin gösterimini sağlayan, dilden bağımsız ortak bir veri biçimidir. Daha fazla bilgi için bkz. [JSON.org](https://json.org).

## <a name="docker-specifications"></a>Docker belirtimleri

Docker programı iki bileşene ihtiyaç duyuyor: Bootstrap ve Job. Program birden fazla işe sahip olabilir.

### <a name="bootstrap"></a>Bootstrap

Müşteri, Farmtts 'de Docker kaydını başlattığında önyükleme bileşeni çalıştırılmalıdır. Aşağıdaki bağımsız değişkenler ( `arg1` ve `arg2` ) programa geçirilir:

- **Farmtts API uç noktası**: API Istekleri için farmtts API uç noktası. Bu uç nokta, Farmtts dağıtımına yönelik API çağrıları yapar.
- **Azure işlevleri URL 'si**: kendi uç noktanız. Bu URL, Farmtts API 'Leri için erişim belirtecinizi sağlar. `GET`Erişim belirtecini getirmek için bu URL 'ye çağrı yapabilirsiniz.

Önyükleme, kullanıcıların Hava durumu verilerini almak için işlerinizi çalıştırması gereken meta verileri oluşturur. Daha fazla bilgi için [başvuru uygulamasına](https://github.com/azurefarmbeats/noaa_docker)bakın. 

Dosyadaki *bootstrap_manifest.js* özelleştirirseniz, başvuru önyükleme programı sizin için gerekli meta verileri oluşturur. Önyükleme programı aşağıdaki meta verileri oluşturur: 

 > [!NOTE]
 > [Başvuru uygulamasının](https://github.com/azurefarmbeats/noaa_docker) açıkladığı dosyadaki *bootstrap_manifest.js* güncelleştirirseniz, aşağıdaki meta verileri oluşturmanız gerekmez. Önyükleme programı, gerekli meta verileri oluşturmak için bildirim dosyanızı kullanacaktır.

- /**Dalgalı veri modeli**: dalgalı veri modeli meta verileri hava durumu verilerini temsil eder. Kaynağın sağladığı veri kümelerine karşılık gelir. Örneğin, bir Dailyforebir Simplemodel günde bir kez ortalama sıcaklık, nem ve yağış bilgilerini sağlayabilir. Buna karşılık, bir DailyForecastAdvancedModel saatlik ayrıntı düzeyi konusunda çok daha fazla bilgi sağlayabilir. İstediğiniz sayıda hava durumu veri modeli oluşturabilirsiniz.
- /**JobType**: farmtts, genişletilebilir bir iş yönetim sistemine sahiptir. Hava durumu veri sağlayıcısı olarak, çeşitli veri kümeleri ve API 'leriniz (örneğin, Getgünlüktahminleri) vardır. Bu veri kümelerini ve API 'Leri, JobType kullanarak Farmtempts ' de etkinleştirebilirsiniz. Bir iş türü oluşturulduktan sonra, müşteri, konumları veya onların ilgi alanları için hava durumu verilerini almak üzere bu türdeki işleri tetikleyebilir. Daha fazla bilgi için, bkz. [Farmtts Swagger](https://aka.ms/farmbeatsswagger)Içindeki JobType ve Job API 'leri.

### <a name="jobs"></a>İşler

Işler bileşeni, bir Farmher Kullanıcı, önyükleme işleminin bir parçası olarak oluşturduğunuz/JobType işini her çalıştırdığında çağrılır. İş için Docker Run komutu, oluşturduğunuz/JobType 'ın bir parçası olarak tanımlanmıştır.

İş, kaynaktan veri getirir ve bunu Farmtörler 'e gönderir. Önyükleme işlemi sırasında, verileri almak için gereken parametrelerin/Jobtype'un bir parçası olarak tanımlanması gerekir.

İşin bir parçası olarak programın, önyükleme işlemi sırasında oluşturulan/dalgalı therdatamodel temelinde bir/dalgalı iş datalocation oluşturması gerekir. /Dalgalı iş datalocation, kullanıcının iş için bir parametre olarak sağladıysa bir konuma (Enlem ve boylam koordinatları) karşılık gelir.

### <a name="object-details"></a>Nesne ayrıntıları

Dalgalı veri modeli | Açıklama |
--- | ---
Ad  | Hava durumu veri modelinin adı. |
Description  | Modelin anlamlı bir açıklaması. |
Özellikler  | Veri sağlayıcısı tarafından tanımlanan ek özellikler. |
Hava ölçüleri > adı  | Hava durumu ölçüsünün adı. Örneğin, humidity_max. |
Dalgalı ölçüler > veri türü  | Double veya Enum. Enum ise, measureEnumDefinition gereklidir. |
>, measureEnumDefinition  | Yalnızca veri türü enum ise gereklidir. Örneğin, `{ "NoRain": 0, "Snow": 1, "Drizzle": 2, "Rain": 3 }` |
Hava ölçüleri > türü  | Hava durumu Telemetri verilerinin türü. Örneğin, Relativenem. Sistem tanımlı türler AmbientTemperature, NoUnit, CO2, Depth, Elektriicaliletkenctivity, Leafwetitesi, uzunluk, LiquidLevel, Nitrate, O2, PH, Fospnefret, Pointıntime, Potassium, basınç, RainGauge, Relativenem, Salinity, Soılmoisture, Soalınma, SolarRadiation, eyalet, TimeDuration, UVRadiation, Uvındex, Volume, Wıngıdirection Daha fazla tür eklemek için bu makaledeki [ExtendedType ekleme](#add-extendedtype) bölümüne bakın.
Dalgalı ölçüler > birimi | Hava durumu telemetri verileri birimi. Sistem tarafından tanımlanan birimler NoUnit, santigrat, Fahrenhayt, Kelvin, Rankine, Pascal, CIS, PSI, milimetre ölçüm, Santimeter, ölçüm, Inç, fit, mil, kilometre, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, derece, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentage, Partspermilyon, mikro Mole, mikro Molesperlitre, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Santibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, litre, MilliLiter, saniyeler, UnixTimestamp, MicroMolePerMeterSquaredPerSecond ve InchesPerHour. Daha fazla birim eklemek için bu makaledeki [ExtendedType ekleme](#add-extendedtype) bölümüne bakın.
> AggregationType  | Toplama türü. Olası değerler None, Average, Maximum, minimum, Standardsapma, Sum ve Total değerleridir.
Hava ölçüleri > derinliği  | Algılayıcının santimetre cinsinden derinliği. Örneğin, zemin altındaki nemi 10 cm ölçümü.
Hava ölçüleri > açıklaması  | Ölçümün anlamlı bir açıklaması. 

JobType | Açıklama |
--- | ---
Ad  | İşin adı. Örneğin, Get_Daily_Forecast. Müşteri Hava durumu verilerini almak için bu işi çalıştıracaktır.|
pipelineDetails > parametreler > adı  | Parametrenin adı. |
pipelineDetails > parametreler > tür | Parametre türü. Olası değerler şunlardır dize, INT, float, bool ve dizi. |
pipelineDetails > parametreler > IsRequired | Parametrenin Boolean değeri. Parametre gerekliyse değer true 'dur. Aksi halde değer false 'tur. Varsayılan değer true 'dur. |
pipelineDetails > parametreler > defaultValue | Parametrenin varsayılan değeri. |
pipelineDetails > parametreler > açıklama | Parametrenin açıklaması. |
Özellikler  | Üreticiden ek özellikler.
> programRunCommand özellikleri | Docker Run komutu. Bu komut, müşteri Hava durumu işini çalıştırdığında çalışır. |

Dalgalı bir Datalocation | Description |
--- | ---
Dalgalı veri ModelId  | Önyükleme işlemi sırasında oluşturulan karşılık gelen dalgalı veri modelinin KIMLIĞI.|
location  | Enlem, boylam ve yükseltme. |
Name | Nesnenin adı. |
Description | Hava durumu veri konumunun açıklaması. |
farmId | Seçim Grubun KIMLIĞI. Müşteri, iş parametresinin bir parçası olarak bu KIMLIĞI sağlar. |
Özellikler  | Üreticiden ek özellikler.

Nesneler ve özellikleri hakkında daha fazla bilgi için bkz. [Farmtts Swagger](https://aka.ms/FarmBeatsSwagger).

> [!NOTE]
> API 'Ler oluşturulan her örnek için benzersiz kimlikler döndürür. Cihaz yönetimi ve meta veri eşitleme çeviricisinin bu KIMLIĞI korumasını sağlaması gerekir.

**Meta veri eşitleme**

Bağlayıcı Docker bileşeni, meta verilerde güncelleştirmeleri gönderebilmelidir. Örneğin, hava durumu sağlayıcısı bir veri kümesine yeni parametreler eklediğinde veya yeni bir 30 günlük tahmin gibi yeni işlevsellik eklendiğinde güncelleştirmeleri göndermelidir.

> [!NOTE]
> Hava durumu veri modelindeki meta veriler için silme desteklenmiyor.
>
> Meta verileri güncelleştirmek için `/Get/{ID}` Hava durumu veri modeli ' ne çağrı yapmanız gerekir. Değiştirilen özellikleri güncelleştirin ve ardından `/Put/{ID}` kullanıcının ayarladığı özellikleri sürdürmek için bir yapın.

## <a name="weather-data-telemetry-specifications"></a>Hava durumu verileri (telemetri) belirtimleri

Hava durumu verileri, işlenmek üzere bir Azure Olay Hub 'ına gönderilen kurallı bir iletiyle eşleştirilir. Azure Event Hubs, bağlı cihazlardan ve uygulamalardan gerçek zamanlı veri (telemetri) alımı sağlayan bir hizmettir. 

Hava durumu verilerini Farmtempts 'e göndermek için, bir olay hub 'ına Farmtts 'de ileti gönderen bir istemci oluşturun. Daha fazla bilgi için bkz. [bir olay hub 'ına telemetri gönderme](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

Aşağıdaki örnek Python kodu, belirtilen bir olay hub 'ına bir istemci olarak telemetri gönderir.

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub connection string provided by customer>"
EVENTHUBNAME = "<EventHub name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

Kurallı ileti biçimi şöyledir:

```json
{
   "weatherstations": [
   {
   "id": "ID of the WeatherDataLocation.",
   "weatherdata": [
   {
     "timestamp": "Timestamp of the data. For historical purposes, this is the time for which the observations are sent. For forecast, this is the time for which data is forecasted. Format is ISO 8601. Default time zone is UTC.",
     "predictiontimestamp": "Timestamp on which the forecast data is predicted. I.e., the time of prediction. Required only for forecast data. Format is ISO 8601. Default time zone is UTC. ",
     "weathermeasurename1": <value>,
     "weathermeasurename2": <value>
     }
     ]
    }
   ]
}
```

Telemetri iletisine bir örnek aşağıda verilmiştir:

```json
{
   "weatherstations": [
   {
     "id": "5e4b34e7-bf9e-4f39-xxxx-f3c06d0366ea",
     "weatherdata": [
     {
      "timestamp": "2019-07-10T00:00:00Z",
      "predictiontimestamp": "2019-07-05T00:00:00Z",
      "PrecipitationTotalLiquidEquivalent": 0,
      "AvgPressure": 0,
      "AvgRelativeHumidity": 72
     }
    ] 
  }
 ]
}

```

## <a name="troubleshooting-and-error-management"></a>Sorun giderme ve hata yönetimi

### <a name="error-logging"></a>Hata günlüğü

İş ortağı işi mevcut iş çerçevesinde çalışır. Bu nedenle, hatalar önceden varolan diğer Farmciler işleri (GetFarmData ve Sensoryerleştirmesi gibi) için hatalarla aynı şekilde günlüğe kaydedilir. Data Factory ardışık düzen içinde çalışan Data Factory etkinliği hem hem de `STDERR` `STDOUT` . Her iki dosya da `datahublogs-xxx` Farmtempts kaynak grubundaki depolama hesabında kullanılabilir.

Datahub kitaplığı, genel veri hub 'ı günlüklerinin bir parçası olarak günlüğe kaydetmeyi etkinleştirmek için yardımcı işlevler sağlar. Daha fazla bilgi için, [NOAA Docker görüntüsünün GitHub sayfasına](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py)bakın.

### <a name="troubleshooting-and-support"></a>Sorun giderme ve destek

Müşteri, Farmtts örneğinde Hava durumu verisi alamadıysanız, sorunu gidermeye yönelik destek ve bir mekanizma sağlayın.

## <a name="add-extendedtype"></a>ExtendedType Ekle

Farmtts, yeni algılayıcı ölçü türleri ve birimleri eklemeyi destekler. [Başvuru uygulamasındaki](https://github.com/azurefarmbeats/noaa_docker)dosyadaki *bootstrap_manifest.js* güncelleştirerek yeni birimler veya türler ekleyebilirsiniz.

Yeni bir dalgalı ölçü türü eklemek için bu adımları izleyin, örneğin, PrecipitationDepth.

1. `GET`Sorguyu kullanarak/ExtendedType üzerinde bir istek yapın `filter - key = WeatherMeasureType` .
2. Döndürülen nesnenin KIMLIĞINI aklınızda edin.
3. Döndürülen nesnedeki listeye yeni türü ekleyin. `PUT`/ExtendedType{ID} üzerinde aşağıdaki yeni liste ile bir istek yapın. Giriş yükü, daha önce aldığınız Yanıtla aynı olmalıdır. Yeni birim, değerler listesinin sonuna eklenmelidir.

/ExtendedType API 'SI hakkında daha fazla bilgi için bkz. [Farmtts Swagger](https://aka.ms/FarmBeatsSwagger).

## <a name="next-steps"></a>Sonraki adımlar

Şimdi, Farmtts ile tümleşen bir bağlayıcı Docker bileşenine sahipsiniz. Daha sonra, Farmker 'daki Docker görüntünüzü kullanarak [Hava durumu verilerini nasıl alacağınız](get-weather-data-from-weather-partner.md) hakkında bilgi edinin. 
