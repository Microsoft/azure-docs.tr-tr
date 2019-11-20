---
title: Inest geçmiş telemetri verileri
description: İçe geçmiş telemetri verilerini alma işlemini açıklar
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 5ae64371bd114a898ddca874e23b499bc4a2b8a3
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74128769"
---
# <a name="ingest-historical-telemetry-data"></a>Geçmiş telemetri verilerini alma

Bu makalede, geçmiş algılayıcı verilerinin Azure Farmınts 'e alınması açıklanmaktadır.

Cihazlar ve algılayıcılar gibi kaynaklar için Nesnelerin İnterneti (IoT) geçmiş verileri almak, Farmınts 'de yaygın bir senaryodur. Cihazlar ve sensörler için meta veriler oluşturup daha sonra verileri kurallı bir biçimde uygulamak için geçmiş verileri içe alın.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makaleye devam etmeden önce, Farmtts 'yi yüklediğinizden ve IoT 'den geçmiş verileri topladığınızdan emin olun.

## <a name="enable-partner-access"></a>İş ortağı erişimini etkinleştir

Azure Farmtts örneğiniz için iş ortağı tümleştirmesini etkinleştirmeniz gerekir. Bu adım, cihaz iş ortağınız olarak Azure Farmtlerinize erişimi olacak bir istemci oluşturur ve sonraki adımlarda gerekli olan aşağıdaki değerleri sağlar.

- API uç noktası – bu veri merkezi URL 'sidir, örneğin, https://<datahub>. azurewebsites.net
- Kiracı Kimliği
- İstemci Kimliği
- İstemci Gizli Anahtarı
- EventHub bağlantı dizesi

Bunları oluşturmak için aşağıdaki adımları izleyin:

>[!NOTE]
> Aşağıdaki adımları uygulamak için yönetici olmanız gerekir.

1. Bu [betiği](https://aka.ms/farmbeatspartnerscript) indirip yerel sürücünüzde ayıklayın. ZIP dosyasının içinde iki dosya bulacaksınız.
2. [Azure Portal](https://portal.azure.com/) oturum açın ve Cloud Shell açın (Bu seçenek portalın sağ üst çubuğunda bulunur)  

    ![Proje grubu ları](./media/for-tutorials/navigation-bar-1.png)

3. Ortamın **PowerShell**olarak ayarlandığından emin olun.

    ![Proje grubu ları](./media/for-tutorials/power-shell-new-1.png)

4. Cloud Shell indirdiğiniz iki dosyayı karşıya yükleyin (yukarıdaki 1. adım).  

    ![Proje grubu ları](./media/for-tutorials/power-shell-two-1.png)

5. Dosyaların karşıya yüklendiği dizine git

   >[!NOTE]
   > Dosya varsayılan olarak,/Home/username/giriş dizinine yüklenir.
6. Komutunu kullanarak betiği çalıştırın:  

    ```azurepowershell-interactive
    ./generateCredentials.ps1
    ```

7. Yordamı gerçekleştirmek için ekrandaki yönergeleri izleyin.

## <a name="create-devicesensor-metadata"></a>Cihaz/algılayıcı meta verileri oluştur

 Artık gerekli kimlik bilgilerine sahip olduğunuza göre, Farmtts API 'Lerini kullanarak meta verileri oluşturarak cihaz ve algılayıcıları tanımlayabilirsiniz.

 Farmtts veri hub 'ı, cihaz/algılayıcı meta verilerinin oluşturulmasını ve yönetilmesini sağlayan aşağıdaki API 'Lere sahiptir.   

- /**devicemodel** -cihaz modeli, Cihazın üreticisi, ağ geçidi veya düğüm gibi cihazın meta verilerine karşılık gelir.  
- /**cihaz** -cihaz, grupta bulunan bir fiziksel cihaza karşılık gelir.  
- **Sensormodel** -algılayıcı modeli /, örneğin üreticisi, sensör türü, analog veya dijital, ortam sıcaklığı gibi algılayıcı ölçüsü ya da basınç gibi sensörin meta verilerine karşılık gelir.
- /**algılayıcı** algılayıcı, değerleri kaydeden bir fiziksel sensöre karşılık gelir. Bir algılayıcı genellikle cihaz KIMLIĞI olan bir cihaza bağlanır.  


|        Cihaz modeli   |  Öneriler   |
| ------- | -------             |
|     Tür (düğüm, ağ geçidi)        |          1 Yıldız      |
|          Üretici            |         2 Yıldız     |
|  ProductCode                    |  Cihaz ürün kodu veya model adı/numarası. Örneğin, EnviroMonitor # 6800.  |
|            Bağlantı Noktaları          |     Bağlantı noktası adı ve türü (dijital/analog)
|     Ad                 |  Kaynağı tanımlamak için ad. Örneğin, model adı/ürün adı.
      Açıklama     | Modelin anlamlı bir açıklamasını sağlayın
|    Özellikler          |    Üreticiden ek özellikler   |
|    **cihaz**             |                      |
|   Devicemodelıd     |     İlişkili cihaz modelinin KIMLIĞI  |
|  Donanım kimliği          | Cihazın MAC adresi vb. gibi benzersiz KIMLIĞI.
|  Reportingınterval        |   Saniye cinsinden raporlama aralığı
|  Konum            |  Cihaz Latitude (-90 ila + 90)/Boylam (-180 ila 180)/yükseltme (metre)   
|Parentdeviceıd       |    Bu cihazın bağlı olduğu üst cihazın KIMLIĞI. Örneğin, bir ağ geçidine bağlı bir düğüm. Düğüm, ağ geçidi olarak Parentdeviceıd 'ye sahip olacaktır.  |
|    Ad            | Kaynağı tanımlamak için bir ad. Cihaz iş ortakları, iş ortağı tarafında cihaz adı ile tutarlı bir ad göndermelidir. İş ortağı cihaz adı Kullanıcı tanımlı ise, aynı kullanıcı tanımlı ad, Farmtts 'ye yayılmalıdır.|
|     Açıklama       |      Anlamlı bir açıklama sağlayın  |
|     Özellikler    |  Üreticiden ek özellikler
|     **Algılayıcı modeli**        |          |
|       Tür (analog, dijital)          |      Analog veya dijital olup olmadığı algılayıcı türü       |
|          Üretici            |       sensör üreticisi     |
|     ProductCode| Ürün kodu veya model adı/numarası. Örneğin, RS-CO2-N01. |
|       Sensorölçüleri > adı       | Algılayıcı ölçüsünün adı. Yalnızca küçük harf desteklenir. Farklı derinlikleri ölçüsünde derinliği belirtin. Örneğin, soil_moisture_15cm. Bu ad telemetri verileriyle tutarlı olmalıdır  |
|          Sensorölçüleri > veri türü       |Telemetri veri türü. Şu anda çift destekleniyor|
|    Sensorölçüleri > türü    |Algılayıcı Telemetri verilerinin ölçüm türü. Sistem tanımlı türler şunlardır: AmbientTemperature, CO2, Depth, elektrik Icaldistributionctivity, Leafwetity, length, LiquidLevel, Nitrate, O2, PH, Fospnefret, Pointınıltıma, Potassium, basınç, RainGauge, Relativenem, Salınity, SoilMoisture, Soilsıcaklık, SolarRadiation, durum, TimeDuration, UVRadiation, Uıvindex, hacim, WIN Direction, WIN Run, Wıngıı, Evapotranspiration, PAR. Daha fazla bilgi için/ExtendedType API 'sine bakın.|
|        Sensorölçüleri > birimi              | Algılayıcı telemetri verileri birimi. Sistem tanımlı birimler şunlardır: NoUnit, santigrat, Fahrenhayt, Kelvin, Rankine, Pascal, CIO, Ukrayna, mil ölçüm, Santimeter, ölçüm, Inç, fit, mil, kilometre, MilesPerHour, MilesPerSecond, KMPerHour, KMPerSecond, MetersPerHour, MetersPerSecond, derece, WattsPerSquareMeter, KiloWattsPerSquareMeter, MilliWattsPerSquareCentiMeter, MilliJoulesPerSquareCentiMeter, VolumetricWaterContent, Percentage, Partspermilyon, MicroMol, mikro Molesperlitre, SiemensPerSquareMeterPerMole, MilliSiemensPerCentiMeter, Santibar, DeciSiemensPerMeter, KiloPascal, VolumetricIonContent, litre, MilliLiter, saniyeler, UnixTimestamp, MicroMolPerMeterSquaredPerSecond, InchesPerHour daha fazla ekleme Için bkz./ ExtendedType API 'SI.|
|    Sensorölçüleri > aggregationType    |  Değerler None, Average, maksimum, minimum veya Standardsapması olabilir  |
|          Ad            | Kaynağı tanımlamak için ad. Örneğin, model adı/ürün adı.  |
|    Açıklama        | Modelin anlamlı bir açıklamasını sağlayın  |
|   Özellikler       |  Üreticiden ek özellikler  |
|    **Algılayıcısı**      |          |
| Donanım kimliği          |   Üretici tarafından ayarlanan algılayıcı için benzersiz KIMLIK |
|  Sensormodelıd     |    İlişkili algılayıcı modelinin KIMLIĞI   |
| location          |  Algılayıcı Enlem (-90 ila + 90)/Boylam (-180 ila 180)/yükseltme (metre)|
|   Bağlantı noktası > adı        |  Algılayıcıdan cihazda bağlı olduğu bağlantı noktasının adı ve türü. Bunun, cihaz modelinde tanımlananla aynı ada sahip olması gerekir. |
|    DeviceID  |    Algılayıcıdan bağlı olduğu cihazın KIMLIĞI     |
| Ad            |   Kaynağı tanımlamak için ad. Örneğin, algılayıcı adı/ürün adı ve model numarası/ürün kodu.|
|    Açıklama      | Anlamlı bir açıklama sağlayın |
|    Özellikler        |Üreticiden ek özellikler |

Nesneler hakkında daha fazla bilgi için bkz. [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

**Meta veri oluşturmak için API isteği**

Bir API isteği oluşturmak için HTTP (POST) yöntemini, API hizmetinin URL 'sini, sorgulanacak bir kaynağa yönelik URI 'yi, bir istek oluşturmak veya silmek için veri göndermek ve bir ya da daha fazla HTTP istek üst bilgisi eklemek için verileri birleştirebilirsiniz. API hizmetinin URL 'si, API uç noktasıdır (örneğin, veri merkezi URL 'SI (https://<yourdatahub>. azurewebsites.net)  

**Kimlik doğrulama**:

Farmtts veri hub 'ı, yukarıdaki bölümde oluşturduğumuz aşağıdaki kimlik bilgilerini gerektiren taşıyıcı kimlik doğrulamasını kullanır.

- İstemci Kimliği
- İstemci Gizli Anahtarı
- Kiracı Kimliği  

Yukarıdaki kimlik bilgilerini kullanarak, çağıran, üstbilgi bölümündeki sonraki API isteklerinde gönderilmesi gereken bir erişim belirteci isteğinde bulunabilir:

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

**Http Istek üstbilgileri**:

Aşağıda, Farmtts veri merkezine bir API çağrısı yapılırken belirtilmesi gereken en yaygın istek üstbilgileri verilmiştir:

- Content-Type: Application/JSON
- Yetkilendirme: taşıyıcı < erişim-belirteç >
- Kabul et: uygulama/JSON

**Meta veri oluşturmak Için giriş yükü**:

**DeviceModel**


```json
{
  "type": "Node",
  "manufacturer": "string",
  "productCode": "string",
  "ports": [
    {
      "name": "string",
      "type": "Analog"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

Cihaz

```json
{
  "deviceModelId": "string",
  "hardwareId": "string",
  "farmId": "string",
  "reportingInterval": 0,
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "parentDeviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

SensorModel

```json
{
  "type": "Analog",
  "manufacturer": "string",
  "productCode": "string",
  "sensorMeasures": [
    {
      "name": "string",
      "dataType": "Double",
      "type": "string",
      "unit": "string",
      "aggregationType": "None",
      "depth": 0,
      "description": "string"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
Algılayıcısı

```json
{
  "hardwareId": "string",
  "sensorModelId": "string",
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "depth": 0,
  "port": {
    "name": "string",
    "type": "Analog"
  },
  "deviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```
Aşağıdaki örnek istek bir cihaz oluşturmaktır (Bu, istek gövdesinde yük olarak bir JSON girişi olur).  

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

> [!NOTE]
> API 'Ler oluşturulan her örnek için benzersiz kimlikler döndürür. İlgili telemetri iletilerini göndermek için kimlikleri tutmanız gerekir.

**Telemetri gönder**

Artık cihaz ve sensörler ' de bir cihaz oluşturdığınıza göre, ilişkili telemetri iletilerini gönderebilirsiniz.  

**Telemetri istemcisi oluşturma**

İşlem için Telemetriyi Azure Event hub 'a göndermeniz gerekir. Azure EventHub, bağlı cihazlardan ve uygulamalardan gerçek zamanlı veri (telemetri) alımı sağlayan bir hizmettir. Telemetriye telemetri verileri göndermek için, Farmtts 'deki bir olay hub 'ına ileti gönderen bir istemci oluşturmanız gerekir. Telemetri gönderme hakkında daha fazla bilgi için bkz. [Azure Olay Hub 'ları](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send).

**İstemci olarak telemetri iletisi gönder**

Bir EventHub istemcisi olarak kurulan bir bağlantınız olduğunda, EventHub 'e bir JSON olarak ileti gönderebilirsiniz.  
Geçmiş algılayıcı veri biçimini Azure Farmtarafından anladığı kurallı bir biçime dönüştürün. Kurallı ileti biçimi aşağıdaki gibidir:  

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

Karşılık gelen cihazları ve algılayıcıları ekledikten sonra, önceki bölümde açıklandığı gibi, telemetri iletisindeki DeviceID 'yi ve sensorıd 'yi edinin

Örnek telemetri iletisi:


 ```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version": "1",
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


## <a name="next-steps"></a>Sonraki adımlar

REST API tabanlı tümleştirme ayrıntıları hakkında daha fazla bilgi için bkz. [REST API](references-for-farmbeats.md#rest-api).
