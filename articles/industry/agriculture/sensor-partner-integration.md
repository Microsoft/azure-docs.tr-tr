---
title: Algılayıcı iş ortağı tümleştirmesi
description: Algılayıcı Iş ortağı tümleştirmesini açıklar
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 1e819c94732e1cbc2de39e6400f8305b7df5aca1
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927651"
---
# <a name="sensor-partner-integration"></a>Algılayıcı iş ortağı tümleştirmesi

Bu makalede, algılayıcı iş ortağı tümleştirmesini sağlayan Azure Farmtts **Translator** bileşeni hakkında bilgi sağlanır.

İş ortakları, bu bileşeni kullanarak, API 'imizi kullanarak ve müşteri cihaz verilerini ve telemetri 'i Farmtts veri merkezine göndererek, planlayıcıları ile tümleştirilen sensörler geliştirebilir. Veriler, Farmtts Hızlandırıcısı kullanılarak görselleştirilir. Veriler Fusion için ve makine dili/yapay zeka modelleri oluşturmak için kullanılabilir.

## <a name="link-farmbeats-account"></a>Farmtts hesabını bağla

Müşteriler cihazlar/algılayıcılar satın aldıktan ve dağıttığından sonra cihaz iş ortaklarının SaaS portalındaki (hizmet olarak yazılım) cihaz verilerine ve telemetrisine erişebilirler. Cihaz iş ortaklarının, müşterilerin hesabını Azure 'daki Farmtts örneğine bağlantı kurmasını sağlamak için ihtiyacı vardır. Müşteri/sistem tümleştiricisi tarafından doldurulması için aşağıdaki kimlik bilgileri gereklidir:

   - Görünen ad (kullanıcının bu tümleştirme için bir ad tanımlayabilmeniz için isteğe bağlı bir alan)
   - API uç noktası
   - Kiracı Kimliği
   - İstemci Kimliği
   - Gizli anahtar
   - EventHub bağlantı dizesi
   - Başlangıç tarihi

   > [!NOTE]
   > Başlangıç tarihi, geçmiş veri akışını, Kullanıcı tarafından belirtilen tarihten verileri de mümkün bir şekilde sunar.

## <a name="unlink-farmbeats"></a>Farmtörler bağlantısını kaldır

Müşteriler var olan bir Farmtts tümleştirmesinin bağlantısını kaldırabilme olanağı sağlar. Farmtların bağlantısını kaldırmak, müşterinin veri merkezinde oluşturulan herhangi bir cihaz/algılayıcı meta verilerini silmemelidir. Bağlantıyı kaldırma şunları yapar:

   - Telemetri akışını durduruyor.
   - Cihaz ortağındaki tümleştirme kimlik bilgilerini siler ve siler.

## <a name="edit-farmbeats-integration"></a>Farmtts tümleştirmesini Düzenle

İstemci gizli dizisi veya bağlantı dizesi değişirse, müşteri, Farmtts tümleştirme ayarlarını düzenleyebilir. Bu durumda, müşteri yalnızca aşağıdaki alanları düzenleyebilir:

   - Görünen ad (varsa)
   - İstemci parolası ("2x8 * * * * * * * * * * *" biçiminde veya şifresiz metin yerine gösterme/gizleme özelliğinde görüntülenmelidir)
   - Bağlantı dizesi ("2x8 * * * * * * * * * * *" biçiminde veya şifresiz metin yerine gösterme/gizleme özelliğinde görüntülenmelidir)

## <a name="view-last-telemetry-sent"></a>Gönderilen son Telemetriyi görüntüle

**Gönderilen son Telemetriyi**zaman damgasını görüntüleyebilirsiniz. Bu, en son telemetrinin Farmtts 'ye başarıyla gönderildiği süredir.

## <a name="translator-development"></a>Çevirmen geliştirme

**REST API tabanlı tümleştirme**

Farmtts 'nin algılayıcı verileri tümleştirme özellikleri REST API aracılığıyla sunulur. Yetenekler, meta veri tanımı, cihaz/algılayıcı sağlama, cihaz ve algılayıcı yönetimi içerir.

**Telemetri alımı**

Telemetri verileri, işlenmek üzere Azure Event hub 'ında yayınlanan kurallı bir iletiyle eşleştirilir. Azure EventHub, bağlı cihazlardan ve uygulamalardan gerçek zamanlı veri (telemetri) alımı sağlayan bir hizmettir.

**API Geliştirme**

API 'Ler Swagger teknik belgelerini içerir. API 'Ler ve bunların karşılık gelen istekleri/yanıtları hakkında daha fazla bilgi için bkz. [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)

**Kimlik doğrulaması**

Farmtler Microsoft Azure Active Directory kimlik doğrulamasından yararlanır. Azure App Service, yerleşik kimlik doğrulama ve yetkilendirme desteği sağlar.

Daha fazla bilgi için bkz. [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization).

Farmtts veri hub 'ı, aşağıdaki kimlik bilgilerini gerektiren taşıyıcı kimlik doğrulaması kullanır:
   - İstemci Kimliği
   - İstemci parolası
   - Kiracı Kimliği

Yukarıdaki kimlik bilgilerini kullanarak, çağıran, üstbilgi bölümündeki sonraki API isteklerinde gönderilmesi gereken bir erişim belirteci isteğinde bulunabilir:

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

Aşağıda, Farmtts 'e yönelik sonraki API çağrıları için kullanılabilecek erişim belirtecine izin veren örnek bir Python kodu verilmiştir: 

```python
import azure 

from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net" [Azure website](https://<yourdatahub>.azurewebsites.net)
CLIENT_ID = "<Your Client ID>"   
CLIENT_SECRET = "<Your Client Secret>"   
TENANT_ID = "<Your Tenant ID>" 
AUTHORITY_HOST = 'https://login.microsoftonline.com' 
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID 
#Authenticating with the credentials 
context = adal.AuthenticationContext(AUTHORITY) 
token_response = context.acquire_token_with_client_credentials(ENDPOINT, CLIENT_ID, CLIENT_SECRET) 
#Should get an access token here 
access_token = token_response.get('accessToken') 
```


**HTTP istek üstbilgileri**

Aşağıda, Farmtts veri merkezine bir API çağrısı yapılırken belirtilmesi gereken en yaygın istek üstbilgileri verilmiştir:


**Üst Bilgi** | **Açıklama ve örnek**
--- | ---
İçerik türü | Farmtts veri merkezi API 'Leri biçimi Için istek biçimi (Content-Type: Application/<format>) JSON. Content-Type: Application/JSON
Yetkilendirme | API çağrı yetkilendirmesi yapmak için gereken erişim belirtecini belirtir: taşıyıcı < erişim belirteci >
ettiğinizde | Yanıt biçimi. Farmtts veri merkezi API 'Leri için, biçim JSON Accept: Application/JSON

**API istekleri**

REST (temsili durum aktarımı) API isteği oluşturmak için, HTTP (GET, POST veya PUT) yöntemini, API hizmetinin URL 'sini, URI 'yi (Tekdüzen Kaynak tanımlayıcısı) sorgulanacak bir kaynağa, bir veya daha fazla HTTP isteğini birleştirir bilgisinde. API hizmetinin URL 'SI, müşteri tarafından sunulan API uç noktasıdır. Örnek: https://\<yourdatahub-Web sitesi-adı >. azurewebsites. net

İsteğe bağlı olarak, filtrelemeye yönelik çağrıları al, boyut sınırını sınırla ve yanıtlarındaki verileri sıralamak için sorgu parametreleri ekleyebilirsiniz.

Aşağıdaki örnek istek, cihazların listesini almak için gereklidir:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
Çoğu GET, POST ve PUT çağrısı, bir JSON istek gövdesi gerektirir.

Aşağıdaki örnek istek bir cihaz oluşturmaktır (Bu örnek, istek gövdesi ile bir JSON girişi içerir).

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>Veri biçimi

JSON (JavaScript Nesne Gösterimi), rastgele veri yapılarının basit bir metin gösterimini sağlayan ortak, dilden bağımsız bir veri biçimidir. Daha fazla bilgi için bkz. [JSON.org](http://json.org).

## <a name="metadata-specifications"></a>Meta veri belirtimleri

Farmtts veri hub 'ı cihaz iş ortaklarının cihaz/algılayıcı meta verilerini oluşturmasına ve yönetmesine olanak tanıyan aşağıdaki API 'Lere sahiptir.  

- /**devicemodel** -cihaz modeli, Cihazın üreticisi, ağ geçidi veya düğüm gibi cihazın meta verilerine karşılık gelir.  
- /**cihaz** -cihaz, grupta bulunan bir fiziksel cihaza karşılık gelir.
- **Sensormodel** -algılayıcı modeli /, sensörin üreticisi, sensör türü, analog veya dijital, örneğin çevresel sıcaklık, basınç vb. gibi algılayıcının meta verilerine karşılık gelir.
- /**algılayıcı** algılayıcı, değerleri kaydeden bir fiziksel sensöre karşılık gelir. Bir algılayıcı genellikle cihaz KIMLIĞI olan bir cihaza bağlanır.

  Cihaz modeli| DeviceModel, Cihazın üreticisi, ağ geçidi ya da düğüm gibi cihazın meta verilerine karşılık gelir.
  --- | ---
  Tür (düğüm, ağ geçidi)  | 1 Yıldız |
  Üretici  | 2 Yıldız |
  ProductCode  | Cihaz ürün kodu veya model adı/numarası. Örneğin, EnviroMonitor # 6800 |
  Bağlantı Noktaları  | Bağlantı noktası adı ve türü (dijital/analog)  |
  Ad  | Kaynağı tanımlamak için ad. Örneğin, model adı/ürün adı |
  Açıklama  | Modelin anlamlı bir açıklamasını sağlayın |
  Özellikler  | Üreticiden ek özellikler |
  **Aygıtların** | **Cihaz, grupta bulunan bir fiziksel cihaza karşılık gelir. Her cihazın benzersiz bir cihaz KIMLIĞI vardır** |
Devicemodelıd  |İlişkili cihaz modelinin KIMLIĞI. |
Donanım kimliği   |Cihazın MAC adresi vb. gibi benzersiz KIMLIĞI.  |
Reportingınterval |Saniye cinsinden raporlama aralığı |
Konum    |Cihaz Latitude (-90 ila + 90)/Boylam (-180 ila 180)/yükseltme (metre) |
Parentdeviceıd | Bu cihazın bağlandığı ana cihazın KIMLIĞI. Örneğin,. Ağ geçidine bağlı bir düğüm; düğüm, ağ geçidi olarak Parentdeviceıd 'ye sahip olacaktır |
  Ad  | Kaynağı tanımlamak için ad.  Cihaz iş ortaklarının cihaz adı ile tutarlı bir adı cihaz iş ortağı tarafında gönderebilmesi gerekir. Cihaz adı cihaz iş ortağı tarafında Kullanıcı tanımlı ise, aynı kullanıcı tanımlı ad, Farmınts 'e yayılmalıdır  |
  Açıklama  | Anlamlı bir açıklama sağlayın  |
  Özellikler  |Üreticiden ek özellikler  |
  **Algılayıcı modeli** | SensorModel, üretici, bir algılayıcı türü olan analog veya dijital, ortam sıcaklık, basınç vb. gibi algılayıcı meta verilerine karşılık gelir. |
  Tür (analog, dijital)  |Analog veya dijital sensörden bahsetme|
  üreticisini  | Üretici adı |
  ProductCode  | Ürün kodu veya model adı/numarası. Örneğin, RS-CO2-N01  |
  Sensorölçüleri > adı  | Algılayıcı ölçüsünün adı. Yalnızca küçük harf desteklenir. Farklı derinlikleri ölçüsünde derinliği belirtin. Örneğin, bu adın soil_moisture_15cm telemetri verileriyle tutarlı olması gerekir. |
  Sensorölçüleri > veri türü  | Telemetri veri türü. Şu anda çift destekleniyor  |
  Sensorölçüleri > türü  | Algılayıcı Telemetri verilerinin ölçüm türü. Sistem tanımlı türler şunlardır: AmbientTemperature, CO2, Depth, elektrik Icaldistributionctivity, Leafwetity, length, LiquidLevel, Nitrate, O2, PH, Fospnefret, Pointınıltıma, Potassium, basınç, RainGauge, Relativenem, Salınity, SoilMoisture, Soilsıcaklık, SolarRadiation, durum, TimeDuration, UVRadiation, Uıvindex, hacim, WIN Direction, WIN Run, Wıngıı, Evapotranspiration, PAR. Daha fazla eklemek için/ExtendedType API 'sine başvurun
  Sensorölçüleri > birimi | Algılayıcı telemetri verileri birimi. Sistem tanımlı birimler şunlardır: NoUnit, santigrat, Fahrenhayt, Kelvin, Rankine, Pascal, CIO, Ukrayna, mil ölçüm, Santimeter, ölçüm, Inç, fit, mil, kilometre, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, derece, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentage, Partspermilyon, MicroMol, mikro Molesperlitre, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Santibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, litre, MilliLiter, saniyeler, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour daha fazla ekleme Için bkz./ ExtendedType API 'SI
  Sensorölçüleri > aggregationType  | Hiçbiri, ortalama, maksimum, en az, Standardsapmı
  Sensorölçüleri > derinliği  | Algılayıcının santimetre cinsinden derinliği (örneğin, arka planda nemi 10 cm ölçüsü)
  Sensorölçüleri > açıklaması  | Ölçünün anlamlı bir açıklamasını sağlayın
  ad  | Kaynağı tanımlamak için ad. Örneğin, model adı/ürün adı
  açıklama  | Modelin anlamlı bir açıklamasını sağlayın
  properties  | Üreticiden ek özellikler
  **Algılayıcısı**  |
  Donanım kimliği  | Üretici tarafından ayarlanan algılayıcı için benzersiz KIMLIK
  Sensormodelıd  | İlişkili algılayıcı modelinin KIMLIĞI.
  location  | Algılayıcı Enlem (-90 ila + 90)/Boylam (-180 ila 180)/yükseltme (metre)
  Bağlantı noktası > adı  |Algılayıcıdan cihazda bağlı olduğu bağlantı noktasının adı ve türü. Bu, cihaz modelinde tanımlananla aynı ada sahip olmalıdır
  deviceId  | Algılayıcıdan bağlı olduğu cihazın KIMLIĞI
  ad  | Kaynağı tanımlamak için ad. Örneğin, algılayıcı adı/ürün adı ve model numarası/ürün kodu.
  açıklama  | Anlamlı bir açıklama sağlayın
  properties  | Üreticiden ek özellikler

 Nesnelerin ve özelliklerinin her biri hakkında bilgi için bkz. [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

 > [!NOTE]
 > API 'Ler oluşturulan her örnek için benzersiz kimlikler döndürür. Bu KIMLIğIN cihaz yönetimi ve meta veri eşitleme çevirmeni tarafından korunması gerekir.


**Meta veri eşitleme**

Çevirmen, güncelleştirmeleri meta veriler üzerinde göndermelidir. Örneğin, güncelleştirme senaryoları: cihaz/algılayıcı adının değiştirilmesi, cihaz/algılayıcı konumunun değiştirilmesi.

Çevirmen, Kullanıcı tarafından, Farmtörler bağlantısı sonrası tarafından yüklenmiş yeni cihaz ve/veya sensörler ekleyebilmelidir. Benzer şekilde, bir cihaz/algılayıcı Kullanıcı tarafından güncellendiyse, buna karşılık gelen cihaz/algılayıcı için aynı şekilde de aynı şekilde güncelleştirilmeleri gerekir. Güncelleştirme cihazı/algılayıcısı için tipik senaryolar: cihaz konumu değişikliği, bir düğümdeki sensörların eklenmesi vb. olabilir.


> [!NOTE]
> Cihaz/algılayıcı meta verileri için silme işlemi desteklenmiyor.
>
> Meta verileri güncelleştirmek için, cihaz/algılayıcı üzerinde/Get/{id} çağrısı yapmanız zorunludur, değiştirilen özellikleri güncelleştirebilir, sonra da Kullanıcı tarafından ayarlanan tüm özellikler kaybolmaması için bir/put/{ID} yapın

### <a name="adding-new-typesunit"></a>Yeni türler/birim ekleniyor

Farmtts, yeni algılayıcı ölçü türleri ve birimleri eklemeyi destekler. /ExtendedType API 'SI, [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)hakkında daha fazla bilgi için.

## <a name="telemetry-specifications"></a>Telemetri belirtimleri

Telemetri verileri, işlenmek üzere Azure Event hub 'ında yayınlanan kurallı bir iletiyle eşleştirilir. Azure EventHub, bağlı cihazlardan ve uygulamalardan gerçek zamanlı veri (telemetri) alımı sağlayan bir hizmettir.

## <a name="send-telemetry-data-to-farmbeats"></a>Telemetri verilerini Farmolar 'a gönderme

Telemetriye telemetri verileri göndermek için, Farmtts 'deki bir olay hub 'ına ileti gönderen bir istemci oluşturmanız gerekir. Telemetri verileri hakkında daha fazla bilgi için bkz. [Olay Hub 'ına telemetri gönderme](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

Aşağıda, belirtilen bir olay hub 'ına bir istemci olarak telemetri gönderen örnek bir Python kodu verilmiştir:

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

Kurallı ileti biçimi aşağıdaki gibidir:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```
Telemetri JSON 'ındaki tüm anahtar adları, örneğin, DeviceID, sensordata vb. gibi küçük harf olmalıdır.

Örneğin, telemetri iletisi:


```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version" : "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}

```

## <a name="troubleshooterror-management"></a>Sorun giderme/hata yönetimi

**Seçenek/destek sorunlarını giderme**

Müşterinin belirtilen Farmtts örneğinde cihaz verileri ve/veya telemetri alamamasından dolayı, cihaz ortağının aynı sorunu gidermek için destek ve bir mekanizma sağlaması gerekir.

**Telemetri verilerini bekletme**

Telemetri verileri, hata ayıklama veya veri kaybı durumunda Telemetriyi yeniden göndermede yararlı olabilecek şekilde, önceden tanımlanmış bir zaman aralığı için de tutulmalıdır.

**Hata yönetimi/hata bildirimi**

Cihaz ortağı sisteminde cihaz/algılayıcı meta verileri/veri tümleştirmesi veya telemetri veri akışını etkileyen bir hata durumunda, aynı müşteriye bildirilmesi gerekir. Hata (ler) i çözümlemek için bir mekanizma da tasarlanmalı ve uygulanmalıdır.

**Bağlantı denetim listesi**

Cihaz üreticileri/iş ortakları, müşteri tarafından sunulan kimlik bilgilerinin doğru olduğundan emin olmak için aşağıdaki sağlamlık testine/denetim listelerine sahip olabilir.

   - Girilen kimlik bilgileriyle bir erişim belirtecinin alınıp alınmadığını denetleyin
   - Alınan erişim belirteci ile bir API çağrısının başarılı olup olmadığını denetleyin
   - EventHub istemci bağlantısının kurulu olup olmadığını denetle

## <a name="next-steps"></a>Sonraki adımlar

REST API hakkında daha fazla bilgi için bkz. [REST API](references-for-farmbeats.md#rest-api).
