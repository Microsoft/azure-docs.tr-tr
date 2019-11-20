---
title: Verilerinizi Azure Blob depolamaya aktarma | Microsoft Docs
description: Azure IoT Central uygulamanızdan Azure Blob depolama alanına veri aktarma
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: b6a437af33e3e85b8dc07de223b6e1fd7ce6cb03
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176241"
---
# <a name="export-your-data-to-azure-blob-storage"></a>Verilerinizi Azure Blob depolama alanına aktarma

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*Bu konu, Yöneticiler için geçerlidir.*

Bu makalede, Azure **BLOB depolama hesabınıza** veya **Azure Data Lake Storage 2. depolama hesabınıza**düzenli aralıklarla veri aktarmak için Azure IoT Central sürekli veri dışa aktarma özelliğinin nasıl kullanılacağı açıklanır. **Ölçümleri**, **cihazları**ve **cihaz şablonlarını** JSON veya Apache avro biçimindeki dosyalara dışarı aktarabilirsiniz. Bu veriler, Microsoft Power BI 'de Azure Machine Learning veya uzun süreli eğilim analizinde eğitim modelleri gibi soğuk yol analizi için kullanılabilir.

> [!Note]
> Sürekli veri dışarı aktarmayı açtığınızda, yalnızca o andan itibaren verileri alırsınız. Şu anda, sürekli veri dışa aktarma kapalı olduğunda veriler bir saat için alınamaz. Daha fazla geçmiş verileri sürdürmek için sürekli veri dışa aktarmayı erken açın.


## <a name="prerequisites"></a>Önkoşullar

- IoT Central uygulamanızda yönetici olmanız gerekir


## <a name="set-up-export-destination"></a>Dışarı aktarma hedefini ayarla

Uygulamasına dışarı aktarmak için mevcut bir depolama alanı yoksa, aşağıdaki adımları izleyin:

1. [Azure Portal yeni bir depolama hesabı](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)oluşturun. Yeni [Azure Blob depolama hesapları](https://aka.ms/blobdocscreatestorageaccount) veya [Azure Data Lake Storage v2 depolama hesapları](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account)oluşturma hakkında daha fazla bilgi edinebilirsiniz.

    > [!Note] 
    > Verileri bir ADLS v2 depolama hesabına aktarmayı seçerseniz, **Blobstorage**olarak **hesap türünü** seçmeniz gerekir. 

    > [!Note] 
    > Kullandıkça Öde IoT Central uygulamanız için olandan farklı aboneliklerdeki depolama hesaplarına veri aktarabilirsiniz. Bu durumda bir bağlantı dizesi kullanarak bağlanacaksınız.

2. Depolama hesabınızda bir kapsayıcı oluşturun. Depolama hesabınıza gidin. **BLOB hizmeti**altında **bloblara gözatamıyorum**' ı seçin. Yeni bir kapsayıcı oluşturmak için üst kısımdaki **+ kapsayıcı** ' yı seçin.

## <a name="set-up-continuous-data-export"></a>Sürekli veri dışarı aktarma ayarlama

Verilerin dışarı aktarılacağı bir depolama hedefine sahip olduğunuza göre, sürekli veri dışa aktarma ayarlamak için aşağıdaki adımları izleyin. 

1. IoT Central uygulamanızda oturum açın.

2. Sol bölmede **veri dışa aktar**' ı seçin.

    > [!Note]
    > Sol bölmede veri dışa aktarma görmüyorsanız, uygulamanızda yönetici değilsiniz demektir. Verilerin dışarı aktarılmasını ayarlamak için bir yöneticiye danışın.

3. Sağ üst köşedeki **+ Yeni** düğmesini seçin. Dışarı aktarma işlemi hedefi olarak **Azure Blob depolama** ' yı seçin. 

    > [!NOTE] 
    > Uygulama başına en fazla dışarı aktarma sayısı beştir. 

    ![Yeni sürekli veri dışa aktarma oluştur](media/howto-export-data/export-new2.png)

4. Aşağı açılan liste kutusunda **depolama hesabı ad**alanınızı seçin. Ayrıca, listede **bir bağlantı dizesi girerek**son seçeneği de seçebilirsiniz. 

    > [!NOTE] 
    > Depolama hesabı ad alanlarını yalnızca **IoT Central uygulamanızla aynı abonelikte**görürsünüz. Bu aboneliğin dışında bir hedefe aktarmak istiyorsanız, **bir bağlantı dizesi girin** ' i seçin ve 5. adıma bakın.

    > [!NOTE] 
    > 7 günlük deneme uygulamaları için sürekli veri vermeyi yapılandırmanın tek yolu bir bağlantı dizesidir. Bunun nedeni 7 günlük deneme uygulamalarının ilişkili bir Azure aboneliğine sahip olmaması olabilir.

    ![Bloba yeni dışarı aktarma oluştur](media/howto-export-data/export-create-blob2.png)

5. Seçim **Bir bağlantı dizesi girin**' i seçerseniz, Bağlantı dizenizi yapıştırmanız için yeni bir kutu belirir. Depolama hesabınızın bağlantı dizesini almak için Azure portal:- **Ayarlar**altında **erişim anahtarları** ' nı seçin-KEY1 bağlantı dizesini veya key2 bağlantı dizesini kopyalayın
 
6. Açılan liste kutusundan bir kapsayıcı seçin. Bir Kapsayıcınız yoksa Azure portal depolama hesabınıza gidin:
    - **BLOB hizmeti**altında **Bloblar**' ı seçin. **+ Kapsayıcı** ' ya tıklayın ve kapsayıcınıza bir ad verin. Verileriniz için bir genel erişim düzeyi seçin (herhangi bir sürekli veri dışa aktarma ile çalışır). [Azure Storage belgelerinden](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)daha fazla bilgi edinin.

7. Tercih ettiğiniz **veri biçimini** SEÇIN: JSON veya [Apache avro](https://avro.apache.org/docs/current/index.html) Format.

8. **Dışarı aktarılacak veriler**' in altında, türü **üzerine**ayarlayarak dışarı aktarılacak her bir veri türünü belirtin.

9. Sürekli veri dışa aktarmayı açmak için, **veri dışa aktarma** geçişi 'nin **Açık**olduğundan emin olun. **Kaydet**’i seçin.

   ![Verileri sürekli dışarı aktarmayı yapılandırma](media/howto-export-data/export-list-blob2.png)

10. Birkaç dakika sonra verileriniz depolama hesabınızda görüntülenir.


## <a name="path-structure"></a>Yol yapısı

Ölçümler, cihazlar ve cihaz şablonları verileri, depolama hesabınıza dakikada bir kez, son verilden bu yana yapılan değişiklikleri içeren her bir dosyayla birlikte verilir. Verilen veriler, JSON veya avro biçimindeki üç klasöre yerleştirilir. Depolama hesabınızdaki varsayılan yollar şunlardır:
- Mesajlar: {Container}/measurements/{hubname}/{yyyy}/{mm}/{dd}/{ss}/{mm}/{filename}
- Cihazlar: {Container}/Devices/{yyyy}/{mm}/{dd}/{ss}/{mm}/{filename}
- Cihaz şablonları: {Container}/Devicetemplates/{yyyy}/{mm}/{dd}/{ss}/{mm}/{filename}

Dosyaya giderek ve **blobu Düzenle** sekmesini seçerek Azure Portal dışarıya aktarılmış dosyalara gidebilirsiniz.

## <a name="data-format"></a>Veri biçimi 

### <a name="measurements"></a>Ölçümler

Yayımlanan ölçüm verilerinde, bu süre boyunca tüm cihazlardan IoT Central tarafından alınan tüm yeni iletiler bulunur. İçeri aktarılmış dosyalar, blob depolamaya [IoT Hub ileti yönlendirme tarafından içeri](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) aktarılmış ileti dosyalarıyla aynı biçimi kullanır.

> [!NOTE]
> Cihazlarınızın `contentType: application/JSON` ve `contentEncoding:utf-8` (veya `utf-16`, `utf-32`) sahip iletiler göndermesini sağlayın. Bir örnek için [IoT Hub belgelerine](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-body) bakın.

> [!NOTE]
> Ölçümleri gönderen cihazlar cihaz kimliklerine göre temsil edilir (aşağıdaki bölümlere bakın). Cihazların adlarını almak için cihaz anlık görüntülerini dışarı aktarın. Her ileti kaydını, cihaz kaydının **DeviceID** 'Siyle eşleşen **connectiondeviceıd** kullanarak ilişkilendirin.

Aşağıdaki örnek, kodu çözülmüş bir avro dosyasındaki kaydı gösterir:

```json
{ 
  "EnqueuedTimeUtc":"2019-06-11T00:00:08.2250000Z",
  "Properties":{},
  "SystemProperties":{ 
    "connectionDeviceId":"<deviceId>",
    "connectionAuthMethod":"{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"<generationId>",
    "enqueuedTime":"2019-06-11T00:00:08.2250000Z"
  },
  "Body":"{\"humidity\":80.59100954598546,\"magnetometerX\":0.29451796907056726,\"magnetometerY\":0.5550332126050068,\"magnetometerZ\":-0.04116681874733441,\"connectivity\":\"connected\",\"opened\":\"triggered\"}"
}
```

### <a name="devices"></a>Cihazlar

Sürekli veri dışa aktarma ilk açıldığında, tüm cihazları içeren tek bir anlık görüntü dışa aktarılabilir. Her cihaz şunları içerir:
- IoT Central cihaz `id`
- Cihazın `name`
- [cihaz sağlama hizmeti](/azure/iot-central/core/howto-connect-nodejs) 'nden `deviceId`
- Cihaz şablonu bilgileri
- Özellik değerleri
- Ayar değerleri

Dakikada yeni bir anlık görüntü yazılır. Anlık görüntü şunları içerir:

- Son anlık görüntüden bu yana eklenen yeni cihazlar.
- Değiştirilen özelliği olan ve son anlık görüntüden bu yana değerleri ayarlayarak cihazlar.

> [!NOTE]
> Son anlık görüntü aktarılmadığından cihazlar silindi. Şu anda anlık görüntülerde silinen cihazlar için göstergeler yok.
>
> Her cihazın ait olduğu cihaz şablonu, bir cihaz şablonu KIMLIĞI ile temsil edilir. Cihaz şablonunun adını almak için cihaz şablonu anlık görüntülerini dışarı aktarın.

İçe aktarılmış dosyalar kayıt başına tek bir satır içerir. Aşağıdaki örnek, kodu çözülen avro biçimindeki bir kaydı gösterir:

```json
{ 
  "id":"<id>",
  "name":"Refrigerator 2",
  "simulated":true,
  "deviceId":"<deviceId>",
  "deviceTemplate":{ 
    "id":"<template id>",
    "version":"1.0.0"
  },
  "properties":{ 
    "cloud":{ 
      "location":"New York",
      "maintCon":true,
      "tempThresh":20
    },
    "device":{ 
      "lastReboot":"2018-02-09T22:22:47.156Z"
    }
  },
  "settings":{ 
    "device":{ 
      "fanSpeed":0
    }
  }
}
```

### <a name="device-templates"></a>Cihaz şablonları

Sürekli veri dışa aktarma ilk açıldığında, tüm cihaz şablonlarının bulunduğu tek bir anlık görüntü dışa aktarılabilir. Her cihaz şablonu şunları içerir:
- cihaz şablonunun `id`
- cihaz şablonunun `name`
- cihaz şablonunun `version`
- Ölçüm veri türleri ve min/max değerleri.
- Özellik veri türleri ve varsayılan değerler.
- Veri türleri ve varsayılan değerler ayarlanıyor.

Dakikada yeni bir anlık görüntü yazılır. Anlık görüntü şunları içerir:

- Son anlık görüntüden bu yana yeni cihaz şablonları eklendi.
- Son anlık görüntüden bu yana değiştirilen ölçümler, özellik ve ayar tanımlarına sahip cihaz şablonları.

> [!NOTE]
> Son anlık görüntü aktarılmadığından, cihaz şablonları silinir. Şu anda anlık görüntülerde Silinen cihaz şablonları için göstergeler yok.

İçe aktarılmış dosyalar kayıt başına tek bir satır içerir. Aşağıdaki örnek, kodu çözülen avro biçimindeki bir kaydı gösterir:

```json
{ 
  "id":"<id>",
  "name":"Refrigerated Vending Machine",
  "version":"1.0.0",
  "measurements":{ 
    "telemetry":{ 
      "humidity":{ 
        "dataType":"double",
        "name":"Humidity"
      },
      "magnetometerX":{ 
        "dataType":"double",
        "name":"Magnetometer X"
      },
      "magnetometerY":{ 
        "dataType":"double",
        "name":"Magnetometer Y"
      },
      "magnetometerZ":{ 
        "dataType":"double",
        "name":"Magnetometer Z"
      }
    },
    "states":{ 
      "connectivity":{ 
        "dataType":"enum",
        "name":"Connectivity"
      }
    },
    "events":{ 
      "opened":{ 
        "name":"Door Opened",
        "category":"informational"
      }
    }
  },
  "settings":{ 
    "device":{ 
      "fanSpeed":{ 
        "dataType":"double",
        "name":"Fan Speed",
        "initialValue":0
      }
    }
  },
  "properties":{ 
    "cloud":{ 
      "location":{ 
        "dataType":"string",
        "name":"Location",
        "initialValue":"Seattle"
      },
      "maintCon":{ 
        "dataType":"boolean",
        "name":"Maintenance Contract",
        "initialValue":true
      },
      "tempThresh":{ 
        "dataType":"double",
        "name":"Temperature Alert Threshold",
        "initialValue":30
      }
    },
    "device":{ 
      "lastReboot":{ 
        "dataType":"dateTime",
        "name":"Last Reboot"
      }
    }
  }
}
```

## <a name="read-exported-avro-files"></a>İçe aktarılmış avro dosyalarını oku

Avro ikili bir biçimdir, bu nedenle dosyalar ham durumunda okunamaz. Dosyalar JSON biçiminde çözülebilir. Aşağıdaki örneklerde ölçümlerin, cihazların ve cihaz şablonlarının avro dosyalarının nasıl ayrıştırılacak gösterilmektedir. Örnekler, önceki bölümde açıklanan örneklere karşılık gelir.

### <a name="read-avro-files-by-using-python"></a>Python kullanarak avro dosyalarını okuma

#### <a name="install-pandas-and-the-pandavro-package"></a>Pandas ve pandavro paketini yükler

```python
pip install pandas
pip install pandavro
```

#### <a name="parse-a-measurements-avro-file"></a>Ölçümler avro dosyasını Ayrıştır

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    measurements = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The SystemProperties column contains a dictionary
    # with the device ID located under the connectionDeviceId key.
    transformed["device_id"] = measurements["SystemProperties"].apply(
        lambda x: x["connectionDeviceId"])

    # The Body column is a series of UTF-8 bytes that is stringified
    # and parsed as JSON. This example pulls the humidity property
    # from each column to get the humidity field.
    transformed["humidity"] = measurements["Body"].apply(
        lambda x: json.loads(bytes(x).decode('utf-8'))["humidity"])

    # Finally, print the new DataFrame with our device IDs and humidities.
    print(transformed)
```

#### <a name="parse-a-devices-avro-file"></a>Bir cihazlar avro dosyasını Ayrıştır

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    devices = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The device ID is available in the id column.
    transformed["device_id"] = devices["deviceId"]

    # The template ID and version are present in a dictionary under
    # the deviceTemplate column.
    transformed["template_id"] = devices["deviceTemplate"].apply(
        lambda x: x["id"])
    transformed["template_version"] = devices["deviceTemplate"].apply(
        lambda x: x["version"])

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = devices["settings"].apply(
        lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

#### <a name="parse-a-device-templates-avro-file"></a>Bir cihaz şablonları avro dosyasını Ayrıştır

```python
import json
import pandavro as pdx
import pandas as pd


def parse(filePath):
    # Pandavro loads the Avro file into a pandas DataFrame
    # where each record is a single row.
    templates = pdx.from_avro(filePath)

    # This example creates a new DataFrame and loads a series
    # for each column that's mapped into a column in our new DataFrame.
    transformed = pd.DataFrame()

    # The template and version are available in the id and version columns.
    transformed["template_id"] = templates["id"]
    transformed["template_version"] = templates["version"]

    # The fanSpeed setting value is located in a nested dictionary
    # under the settings column.
    transformed["fan_speed"] = templates["settings"].apply(
        lambda x: x["device"]["fanSpeed"])

    # Finally, print the new DataFrame with our device and template
    # information, along with the value of the fan speed.
    print(transformed)
```

### <a name="read-avro-files-by-using-c"></a>Kullanarak avro dosyalarını okumaC#

#### <a name="install-the-microsofthadoopavro-package"></a>Microsoft. Hadoop. avro paketini yükler

```csharp
Install-Package Microsoft.Hadoop.Avro -Version 1.5.6
```

#### <a name="parse-a-measurements-avro-file"></a>Ölçümler avro dosyasını Ayrıştır

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;
using Newtonsoft.Json;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    var systemProperties = record.GetField<IDictionary<string, object>>("SystemProperties");
                    var deviceId = systemProperties["connectionDeviceId"] as string;
                    Console.WriteLine("Device ID: {0}", deviceId);

                    using (var stream = new MemoryStream(record.GetField<byte[]>("Body")))
                    {
                        using (var streamReader = new StreamReader(stream, Encoding.UTF8))
                        {
                            var body = JsonSerializer.Create().Deserialize(streamReader, typeof(IDictionary<string, dynamic>)) as IDictionary<string, dynamic>;
                            var humidity = body["humidity"];
                            Console.WriteLine("Humidity: {0}", humidity);
                        }
                    }
                }
            }
        }
    }
}
```

#### <a name="parse-a-devices-avro-file"></a>Bir cihazlar avro dosyasını Ayrıştır

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var deviceId = record.GetField<string>("deviceId");

                    // The device template information is stored in a sub-record
                    // under the deviceTemplate field.
                    var deviceTemplateRecord = record.GetField<AvroRecord>("deviceTemplate");
                    var templateId = deviceTemplateRecord.GetField<string>("id");
                    var templateVersion = deviceTemplateRecord.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "Device ID: {0}, Template ID: {1}, Template Version: {2}, Fan Speed: {3}",
                        deviceId,
                        templateId,
                        templateVersion,
                        fanSpeed
                    );
                }
            }
        }
    }
}

```

#### <a name="parse-a-device-templates-avro-file"></a>Bir cihaz şablonları avro dosyasını Ayrıştır

```csharp
using Microsoft.Hadoop.Avro;
using Microsoft.Hadoop.Avro.Container;

public static async Task Run(string filePath)
{
    using (var fileStream = File.OpenRead(filePath))
    {
        using (var reader = AvroContainer.CreateGenericReader(fileStream))
        {
            // For one Avro container, where a container can contain multiple blocks,
            // loop through each block in the container.
            while (reader.MoveNext())
            {
                // Loop through the Avro records in the block and extract the fields.
                foreach (AvroRecord record in reader.Current.Objects)
                {
                    // Get the field value directly. You can also yield return
                    // records and make the function IEnumerable<AvroRecord>.
                    var id = record.GetField<string>("id");
                    var version = record.GetField<string>("version");

                    // The settings and properties are nested two levels deep.
                    // The first level indicates settings or properties.
                    // The second level indicates the type of setting or property.
                    var settingsRecord = record.GetField<AvroRecord>("settings");
                    var deviceSettingsRecord = settingsRecord.GetField<IDictionary<string, dynamic>>("device");
                    var fanSpeed = deviceSettingsRecord["fanSpeed"];
                    
                    Console.WriteLine(
                        "ID: {1}, Version: {2}, Fan Speed: {3}",
                        id,
                        version,
                        fanSpeed
                    );
                }
            }
        }
    }
}
```

### <a name="read-avro-files-by-using-javascript"></a>JavaScript kullanarak avro dosyalarını okuma

#### <a name="install-the-avsc-package"></a>Avsc paketini yükler

```javascript
npm install avsc
```

#### <a name="parse-a-measurements-avro-file"></a>Ölçümler avro dosyasını Ayrıştır

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device ID and humidity from each record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the system properties.
        const deviceId = record.SystemProperties.connectionDeviceId;

        // Convert the body from a buffer to a string and parse it.
        const body = JSON.parse(record.Body.toString());

        // Get the humidty property from the body.
        const humidity = body.humidity;

        // Log the retrieved device ID and humidity.
        console.log(`Device ID: ${deviceId}`);
        console.log(`Humidity: ${humidity}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-devices-avro-file"></a>Bir cihazlar avro dosyasını Ayrıştır

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the device ID from the deviceId property.
        const deviceId = record.deviceId;

        // Fetch the template ID and version from the deviceTemplate property.
        const deviceTemplateId = record.deviceTemplate.id;
        const deviceTemplateVersion = record.deviceTemplate.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device ID and humidity.
        console.log(`deviceID: ${deviceId}, Template ID: ${deviceTemplateId}, Template Version: ${deviceTemplateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

#### <a name="parse-a-device-templates-avro-file"></a>Bir cihaz şablonları avro dosyasını Ayrıştır

```javascript
const avro = require('avsc');

// Read the Avro file. Parse the device and template identification
// information and the fanSpeed setting for each device record.
async function parse(filePath) {
    const records = await load(filePath);
    for (const record of records) {
        // Fetch the template ID and version from the id and verison properties.
        const templateId = record.id;
        const templateVersion = record.version;

        // Get the fanSpeed from the nested device settings property.
        const fanSpeed = record.settings.device.fanSpeed;

        // Log the retrieved device id and humidity.
        console.log(`Template ID: ${templateId}, Template Version: ${templateVersion}, Fan Speed: ${fanSpeed}`);
    }
}

function load(filePath) {
    return new Promise((resolve, reject) => {
        // The file decoder emits each record as a data event on a stream.
        // Collect the records into an array and return them at the end.
        const records = [];
        avro.createFileDecoder(filePath)
            .on('data', record => { records.push(record); })
            .on('end', () => resolve(records))
            .on('error', reject);
    });
}
```

## <a name="next-steps"></a>Sonraki adımlar

Artık verilerinizi dışarı aktarmayı öğrenmiş olduğunuza göre, sonraki adıma geçin:

> [!div class="nextstepaction"]
> [Power BI verilerinizi görselleştirme](howto-connect-powerbi.md)
