---
title: "Kavramlar: FHıR için Azure API 'sinin FHIR (Önizleme) özelliği için Azure IoT Bağlayıcısı 'nda şablonları eşleme"
description: Azure IoT Bağlayıcısı 'nda FHıR (Önizleme) için iki tür eşleme şablonu oluşturmayı öğrenin. Cihaz eşleme şablonu, cihaz verilerini Normalleştirilmemiş bir şemaya dönüştürür. FHıR eşleme şablonu, normalleştirilmiş bir iletiyi FHıR tabanlı bir gözlem kaynağına dönüştürür.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 04/05/2021
ms.author: punagpal
ms.openlocfilehash: e43b5068544927ba4d04c86b16d3a15510c32bed
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448481"
---
# <a name="azure-iot-connector-for-fhir-preview-mapping-templates"></a>FHIR için Azure IoT Bağlayıcısı’nın (önizleme) eşleme şablonları
Bu makalede, eşleme şablonlarını kullanarak hızlı sağlık birlikte çalışabilirlik kaynakları (FHıR&#174;) * için Azure IoT Bağlayıcısı 'nın nasıl yapılandırılacağı açıklanır.

FHıR için Azure IoT Bağlayıcısı, iki tür JSON tabanlı eşleme şablonu gerektirir. İlk tür olan **cihaz eşleme**, `devicedata` Azure Olay Hub 'ı uç noktasına gönderilen cihaz yüklerini eşleştirmekten sorumludur. Türleri, cihaz tanımlayıcılarını, ölçüm Tarih saatini ve ölçüm değerlerini ayıklar. İkinci tür olan **fhır eşleme**, fhır kaynağı için eşlemeyi denetler. Bu, izleme döneminin uzunluğunu, değerleri depolamak için kullanılan FHıR veri türünü ve terminoloji kodlarını yapılandırmanızı sağlar. 

Eşleme şablonları, türlerine göre bir JSON belgesinde oluşur. Bu JSON belgeleri daha sonra Azure portal aracılığıyla FHıR için Azure IoT bağlayıcınıza eklenir. Cihaz eşleme belgesi, **cihaz eşlemesini Yapılandır** sayfası ve fhır eşleme belgesi aracılığıyla **Fhır eşlemesini Yapılandır** sayfasından eklenir.

> [!NOTE]
> Eşleme şablonları temel alınan bir BLOB depolama alanında depolanır ve işlem yürütme başına blob 'dan yüklenir. Güncelleştirildikten sonra hemen etkili olmaları gerekir. 

## <a name="device-mapping"></a>Cihaz eşleme
Cihaz eşleme, daha fazla değerlendirme için cihaz içeriğini ortak bir biçimde ayıklamaya yönelik eşleme işlevleri sağlar. Alınan her ileti tüm şablonlara göre değerlendirilir. Bu yaklaşım, tek bir gelen iletinin daha sonra FHıR 'deki farklı gözlemler ile eşlendiği birden fazla giden ileti ile yansıtılma olanağı sağlar. Sonuç, şablonlar tarafından ayrıştırılmış değeri veya değerleri temsil eden normalleştirilmiş bir veri nesnesidir. Normalleştirilmiş veri modeli, bulunması ve ayıklanması gereken birkaç gerekli özelliğe sahiptir:

| Özellik | Açıklama |
| - | - |
|**Tür**|Ölçüyü sınıflandırmakta olan ad/tür. Bu değer, gerekli FHıR eşleme şablonuna bağlamak için kullanılır.  Birden fazla şablon, birden çok cihazda bulunan farklı temsilleri tek bir ortak çıkışa eşlemenizi sağlayan aynı türe çıktı verebilir.|
|**Oluşum süresi UTC**|Ölçümün gerçekleştiği zaman.|
|**DeviceID**|Cihazın tanımlayıcısı. Bu değer, hedef FHıR sunucusunda bulunan cihaz kaynağındaki bir tanımlayıcıyla eşleşmelidir.|
 |**Özellikler**|Değerin oluşturulan gözlem kaynağına kaydedilebilmesi için en az bir özelliği ayıklayın.  Özellikler, normalleştirme sırasında ayıklanan anahtar değer çiftlerinin koleksiyonudur.|

Normalleştirme sırasında ne olacağı hakkında kavramsal bir örnek aşağıda verilmiştir.

![Normalleştirme örneği](media/concepts-iot-mapping-templates/normalization-example.png)

İçerik yükünün kendisi, üç bölümden oluşan bir Azure Olay Hub iletisidir: Body, Properties ve SystemProperties. , `Body` BIR UTF-8 kodlu dizeyi temsil eden bir bayt dizisidir. Şablon değerlendirmesi sırasında, bayt dizisi otomatik olarak dize değerine dönüştürülür. `Properties` , ileti Oluşturucu tarafından kullanılmak üzere bir anahtar değer koleksiyonudur. `SystemProperties` Ayrıca, Azure Event hub çerçevesi tarafından otomatik olarak doldurulmuş girdilerle ayrılmış bir anahtar değer koleksiyonudur.

```json
{
    "Body": {
        "content": "value"
    },
    "Properties": {
        "key1": "value1",
        "key2": "value2"
    },
    "SystemProperties": {
        "x-opt-sequence-number": 1,
        "x-opt-enqueued-time": "2019-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```

### <a name="mapping-with-json-path"></a>JSON yoluyla eşleme
Günümüzde desteklenen üç cihaz içerik şablonu türü, hem gerekli şablonla hem de ayıklanan değerlerle eşleşen JSON yolunu kullanır. JSON yolu hakkında daha fazla bilgiyi [burada](https://goessner.net/articles/JsonPath/)bulabilirsiniz. Tüm üç şablon türü json yolu ifadelerini çözümlemek için [JSON .NET uygulamasını](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) kullanır.

#### <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate
JsonPathContentTemplate, JSON yolu kullanılarak bir olay hub 'ı iletisinden gelen ve ayıklanan değerleri eşleştirmeyi sağlar.

| Özellik | Açıklama |<div style="width:150px">Örnek</div>
| --- | --- | --- 
|**'Ta**|Şablonla eşleşen ölçümlerle ilişkilendirilecek tür.|`heartrate`
|**TypeMatchExpression**|Olay Hub 'ı yüküne göre değerlendirilen JSON yolu ifadesi. Eşleşen bir JToken bulunursa, şablon bir eşleşme olarak kabul edilir. Sonraki tüm ifadeler, burada eşleşen ayıklanan JToken 'a göre değerlendirilir.|`$..[?(@heartRate)]`
|**TimestampExpression**|Ölçümün oluşum süresi UTC için zaman damgası değerini Ayıklanacak JSON yol ifadesi.|`$.endDate`
|**DeviceIdExpression**|Cihaz tanımlayıcısını Ayıklanacak JSON yol ifadesi.|`$.deviceId`
|**Haentidexpression**|*Isteğe bağlı*: hasta TANıMLAYıCıYı Ayıklanacak JSON yol ifadesi.|`$.patientId`
|**Karşılaşıldı Teridexpression**|*Isteğe bağlı*: karşılaştığınız TANıMLAYıCıYı Ayıklanacak JSON yol ifadesi.|`$.encounterId`
|**Values []. ValueName**|Sonraki ifade tarafından Ayıklanan değer ile ilişkilendirilecek ad. Gerekli değeri/bileşeni FHıR eşleme şablonuna bağlamak için kullanılır. |`hr`
|**Values []. ValueExpression**|Gerekli değeri Ayıklanacak JSON yol ifadesi.|`$.heartRate`
|**Values []. Gerekli**|Yük içinde değerin mevcut olmasını gerektirir.  Bulunamazsa, bir ölçüm oluşturulmaz ve bir InvalidOperationException atılır.|`true`

##### <a name="examples"></a>Örnekler
---
**Kalp oranı**

*İleti*
```json
{
    "Body": {
        "heartRate": "78",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Şablon*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**Kan basıncı**

*İleti*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Şablon*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@systolic && @diastolic)]",
    "deviceIdExpression": "$.deviceId",
    "timestampExpression": "$.endDate",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
---

**Tek iletiden birden çok ölçümü proje**

*İleti*
```json
{
    "Body": {
        "heartRate": "78",
        "steps": "2",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*Şablon 1*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
*Şablon 2*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.steps",
                "valueName": "steps"
            }
        ]
    }
}
```
---

**İletideki diziden birden çok ölçümü proje**

*İleti*
```json
{
    "Body": [
        {
            "heartRate": "78",
            "endDate": "2019-02-01T22:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "81",
            "endDate": "2019-02-01T23:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "72",
            "endDate": "2019-02-01T24:46:01.8750000Z",
            "deviceId": "device123"
        }
    ],
    "Properties": {},
    "SystemProperties": {}
}
```
*Şablon*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```

#### <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate

IotJsonPathContentTemplate, DeviceIdExpression ve TimestampExpression gerekli olmadığı sürece JsonPathContentTemplate öğesine benzerdir.

Bu şablonu kullanırken varsayımıyla, değerlendirilmekte olan mesajlar Azure [IoT Hub cihaz SDK 'ları](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) kullanılarak gönderilmiştir veya [Azure IoT Central](../../iot-central/core/overview-iot-central.md)'ın [veri (eski) özelliğini dışarı aktarın](../../iot-central/core/howto-export-data-legacy.md) . Bu SDK 'Ları kullanırken, cihaz kimliği (Azure IoT Hub/merkezi 'nin cihaz tanımlayıcısı, hedef FHıR sunucusundaki bir cihaz kaynağı için bir tanımlayıcı olarak kaydedilir) ve iletinin zaman damgası bilinirdi. Azure IoT Hub cihaz SDK 'larını kullanıyorsanız, ancak cihaz kimliği veya ölçüm zaman damgası için ileti gövdesinde özel özellikler kullanıyorsanız, JsonPathContentTemplate kullanmaya devam edebilirsiniz.

*Note: IotJsonPathContentTemplate kullanılırken, TypeMatchExpression, tüm iletiye JToken olarak çözümlenmelidir. Aşağıdaki örneklere bakın.* 
##### <a name="examples"></a>Örnekler
---
**Kalp oranı**

*İleti*
```json
{
    "Body": {
        "heartRate": "78"        
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*Şablon*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.Body.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**Kan basıncı**

*İleti*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87"
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*Şablon*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@Body.systolic && @Body.diastolic)]",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.Body.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.Body.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```

#### <a name="iotcentraljsonpathcontenttemplate"></a>IotCentralJsonPathContentTemplate

IotCentralJsonPathContentTemplate Ayrıca DeviceIdExpression ve TimestampExpression gerektirmez ve değerlendirilmekte olan mesajlar [Azure IoT Central](../../iot-central/core/overview-iot-central.md)'In [verileri dışarı aktarma](../../iot-central/core/howto-export-data.md) özelliği aracılığıyla gönderildiğinde kullanılır. Bu özelliği kullanırken, cihaz kimliği (Azure IoT Central 'daki cihaz tanımlayıcısının, hedef FHıR sunucusundaki bir cihaz kaynağı için bir tanımlayıcı olarak kaydedilir) ve iletinin zaman damgası bilinirdi. Azure IoT Central veri dışa aktarma özelliğini kullanıyorsanız, ancak cihaz kimliği veya ölçüm zaman damgası için ileti gövdesinde özel özellikler kullanıyorsanız, JsonPathContentTemplate kullanmaya devam edebilirsiniz.

*Note: IotCentralJsonPathContentTemplate kullanılırken, TypeMatchExpression, tüm iletiye JToken olarak çözümlenmelidir. Aşağıdaki örneklere bakın.* 
##### <a name="examples"></a>Örnekler
---
**Kalp oranı**

*İleti*
```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "HeartRate": "88",
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
*Şablon*
```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@telemetry.HeartRate)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.HeartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**Kan basıncı**

*İleti*
```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "BloodPressure": {
            "Diastolic": "87",
            "Systolic": "123"
        }
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
*Şablon*
```json
{
    "templateType": "IotCentralJsonPathContent",
    "template": {
        "typeName": "bloodPressure",
        "typeMatchExpression": "$..[?(@telemetry.BloodPressure.Diastolic && @telemetry.BloodPressure.Systolic)]",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Diastolic",
                "valueName": "bp_diastolic"
            },
            {
                "required": "true",
                "valueExpression": "$.telemetry.BloodPressure.Systolic",
                "valueName": "bp_systolic"
            }
        ]
    }
}
```

## <a name="fhir-mapping"></a>FHıR eşleme
Cihaz içeriği normalleştirilmiş bir modele ayıklandıktan sonra, veriler toplanır ve cihaz tanımlayıcısına, ölçüm türüne ve zaman dilimine göre gruplandırılır. Bu gruplandırmanın çıktısı bir FHıR kaynağına dönüştürmek için gönderilir (Şu anda[gözlemler](https://www.hl7.org/fhir/observation.html) ). Burada FHıR eşleme şablonu, verilerin bir FHAR Gözlemyle nasıl eşleştirildiğini denetler. Bir zaman aralığı için veya bir saat boyunca bir gözlem oluşturulmalıdır mi? Gözlemye hangi kodlar eklenmelidir? Değer, [Sampleddata](https://www.hl7.org/fhir/datatypes.html#SampledData) veya [Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity)olarak temsil edilmelidir? Bu veri türleri, FHıR eşleme yapılandırma denetimlerinin tüm seçenekleridir.

### <a name="codevaluefhirtemplate"></a>CodeValueFhirTemplate
CodeValueFhirTemplate Şu anda FHıR eşlemesinde desteklenen tek şablondur.  Kodları, geçerlilik süresini ve gözlemin değerini tanımlamanızı sağlar. Birden çok değer türü desteklenir: [Sampleddata](https://www.hl7.org/fhir/datatypes.html#SampledData), [codeablekavram](https://www.hl7.org/fhir/datatypes.html#CodeableConcept)ve [Quantity](https://www.hl7.org/fhir/datatypes.html#Quantity). Bu yapılandırılabilir değerlerle birlikte, gözlem kaynağı için tanımlayıcı ve uygun cihaza ve hasta kaynaklara bağlantı otomatik olarak işlenir.

| Özellik | Açıklama 
| --- | ---
|**'Ta**| Bu şablonun bağlanması gereken ölçüm türü. Bu türü veren en az bir cihaz eşleme şablonu olmalıdır.
|**Periodınterval**|Gözlemin oluşturulduğu zaman dilimi temsil etmelidir. Desteklenen değerler 0 (bir örnek), 60 (bir saat), 1440 (bir gün).
|**Kategori**|Oluşturulan gözlem türünü sınıflandırmak için herhangi bir sayıda [Codeableconcepts](http://hl7.org/fhir/datatypes-definitions.html#codeableconcept) .
|**Kodları**|Oluşturulan gözlemye uygulanan bir veya daha fazla [ortak](http://hl7.org/fhir/datatypes-definitions.html#coding) .
|**[] Kodları. Kodudur**|[Kodlama](http://hl7.org/fhir/datatypes-definitions.html#coding)kodu.
|**[] Kodları. Sistemin**|[Kodlama](http://hl7.org/fhir/datatypes-definitions.html#coding)sistemi.
|**[] Kodları. Görüntülenme**|[Kodlama](http://hl7.org/fhir/datatypes-definitions.html#coding)için görüntü.
|**Değer**|Ayıklanacak ve gözlemde temsil edilecek değer. Daha fazla bilgi için bkz. [değer türü şablonları](#valuetypes).
|**Bileşenler**|*Isteğe bağlı:* Gözlemde oluşturulacak bir veya daha fazla bileşen.
|**Bileşenler []. Kodları**|Bileşene uygulanacak bir veya daha fazla [ortak](http://hl7.org/fhir/datatypes-definitions.html#coding) .
|**Bileşenler []. Deeri**|Bileşendeki Ayıklanacak ve temsil edilecek değer. Daha fazla bilgi için bkz. [değer türü şablonları](#valuetypes).

### <a name="value-type-templates"></a>Değer türü şablonları <a name="valuetypes"></a>
Şu anda desteklenen değer türü şablonları aşağıda verilmiştir. Gelecekte, daha fazla şablon eklenebilir.
#### <a name="sampleddata"></a>SampledData
[Sampleddata](http://hl7.org/fhir/datatypes.html#SampledData) fhır veri türünü temsil eder. İzleme ölçümleri, zaman içinde bir noktada başlayan bir değer akışına yazılır ve tanımlanan süre kullanılarak ileri 'yi artırmaktadır. Değer yoksa, `E` veri akışına yazılır. Süre, iki değerden daha fazla değerin veri akışında aynı konumda yer alıyorsa, en son değer kullanılır. Aynı mantık, SampledData kullanan bir izleme güncellendiği zaman uygulanır.

| Özellik | Açıklama 
| --- | ---
|**DefaultPeriod**|Kullanılacak varsayılan süre (milisaniye). 
|**Birim**|SampledData 'ın kaynağına ayarlanacak birim. 

#### <a name="quantity"></a>Miktar
Fhır veri türü [miktarını](http://hl7.org/fhir/datatypes.html#Quantity) temsil eder. Gruplandırmada birden fazla değer varsa, yalnızca ilk değer kullanılır. Aynı gözlemye eşlenen yeni değer geldiğinde eski değerin üzerine yazılır.

| Özellik | Açıklama 
| --- | --- 
|**Birim**| Birim gösterimi.
|**Kod**| Birimin kodlanmış biçimi.
|**Sistem**| Kodlanmış birim formunu tanımlayan sistem.

### <a name="codeableconcept"></a>Codeablekavram
[Codeablekavram](http://hl7.org/fhir/datatypes.html#CodeableConcept) fhır veri türünü temsil eder. Gerçek değer kullanılmaz.

| Özellik | Açıklama 
| --- | --- 
|**Metin**|Düz metin gösterimi. 
|**Kodları**|Oluşturulan gözlemye uygulanan bir veya daha fazla [ortak](http://hl7.org/fhir/datatypes-definitions.html#coding) .
|**[] Kodları. Kodudur**|[Kodlama](http://hl7.org/fhir/datatypes-definitions.html#coding)kodu.
|**[] Kodları. Sistemin**|[Kodlama](http://hl7.org/fhir/datatypes-definitions.html#coding)sistemi.
|**[] Kodları. Görüntülenme**|[Kodlama](http://hl7.org/fhir/datatypes-definitions.html#coding)için görüntü.

### <a name="examples"></a>Örnekler
**Kalp oranı-SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "8867-4",
                "system": "http://loinc.org",
                "display": "Heart rate"
            }
        ],
        "periodInterval": 60,
        "typeName": "heartrate",
        "value": {
            "defaultPeriod": 5000,
            "unit": "count/min",
            "valueName": "hr",
            "valueType": "SampledData"
        }
    }
}
```
---
**Adımlar-örnekledi verileri**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "55423-8",
                "system": "http://loinc.org",
                "display": "Number of steps"
            }
        ],        
        "periodInterval": 60,
        "typeName": "stepsCount",
        "value": {
            "defaultPeriod": 5000,
            "unit": "",
            "valueName": "steps",
            "valueType": "SampledData"
        }
    }
}
```
---
**Kan basıncı-SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 60,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "SampledData"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "SampledData"
                }
            }
        ]
    }
}
```
---
**Kan basıncı-miktar**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 0,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "Quantity"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "Quantity"
                }
            }
        ]
    }
}
```
---
**Cihaz kaldırıldı-Codeablekavram**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "deviceEvent",
                "system": "https://www.mydevice.com/v1",
                "display": "Device Event"
            }
        ],
        "periodInterval": 0,
        "typeName": "deviceRemoved",
        "value": {
            "text": "Device Removed",
            "codes": [
                {
                    "code": "deviceRemoved",
                    "system": "https://www.mydevice.com/v1",
                    "display": "Device Removed"
                }
            ],
            "valueName": "deviceRemoved",
            "valueType": "CodeableConcept"
        }
    }
}
```
---

## <a name="next-steps"></a>Sonraki adımlar

FHıR (Önizleme) için Azure IoT Bağlayıcısı hakkında sık sorulan sorulara göz atın.

>[!div class="nextstepaction"]
>[FHıR SSS için Azure IoT Bağlayıcısı](fhir-faq.md)

* Azure portal, FHıR için Azure IoT Bağlayıcısı, IoT Bağlayıcısı (Önizleme) olarak adlandırılır. FHıR, HL7 'in tescilli ticari markasıdır ve HL7 izniyle kullanılır.