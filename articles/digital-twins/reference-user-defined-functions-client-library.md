---
title: Kullanıcı tanımlı işlevler istemci kitaplığı başvurusu-Azure dijital TWINS | Microsoft Docs
description: Azure dijital TWINS Kullanıcı tanımlı işlevler istemci kitaplığı başvurusu.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: article
ms.date: 08/12/2019
ms.custom: seodec18
ms.openlocfilehash: 0bbf247adbeab89cf8b16ed089eb13e53cf501f5
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74009231"
---
# <a name="user-defined-functions-client-library-reference"></a>Kullanıcı tanımlı işlevler istemci kitaplığı başvurusu

Bu belge, Azure Digital TWINS Kullanıcı tanımlı işlevler istemci kitaplığı için başvuru bilgileri sağlar.

## <a name="helper-methods"></a>Yardımcı yöntemler

İstemci kitaplığı, yaygın olarak kullanılan işlemler için yardımcı yöntemleri tanımlar.

### <a name="getspacemetadataid--space"></a>getSpaceMetadata (kimlik) ⇒ `space`

Alan tanımlayıcısı verildiğinde, bu işlev grafikten alanı alır.

**Tür**: genel işlev

| Parametre  | Tür                | Açıklama  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Boşluk tanımlayıcısı |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata (kimlik) ⇒ `sensor`

Bir algılayıcı tanımlayıcısı verildiğinde, bu işlev algılayıcıyı grafikten alır.

**Tür**: genel işlev

| Parametre  | Tür                | Açıklama  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Algılayıcı tanımlayıcısı |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata (kimlik) ⇒ `device`

Bir cihaz tanımlayıcısı verildiğinde, bu işlev cihazı grafikten alır.

**Tür**: genel işlev

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | Cihaz tanımlayıcısı |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue (Sensorıd, dataType) ⇒ `value`

Bu işlev, bir algılayıcı tanımlayıcısı ve veri türü verildiğinde, bu algılayıcı için geçerli değeri alır.

**Tür**: genel işlev

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *Sensorıd*  | `guid` | Algılayıcı tanımlayıcısı |
| *x*  | `string` | Algılayıcı veri türü |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue (Spaceıd, valueName) ⇒ `value`

Bir alan tanımlayıcısı ve değer adı verildiğinde, bu işlev o alan özelliği için geçerli değeri alır.

**Tür**: genel işlev

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *Spaceıd*  | `guid` | Boşluk tanımlayıcısı |
| *valueName* | `string` | Boşluk özelliği adı |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>Getsensorgeçmişini değerleri (Sensorıd, dataType) ⇒ `value[]`

Bu işlev, bir algılayıcı tanımlayıcısı ve veri türü verildiğinde, bu algılayıcı için geçmiş değerleri alır.

**Tür**: genel işlev

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *Sensorıd* | `guid` | Algılayıcı tanımlayıcısı |
| *x* | `string` | Algılayıcı veri türü |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>Getspaceıvalues değeri (Spaceıd, dataType) ⇒ `value[]`

Alan tanımlayıcısı ve değer adı verildiğinde, bu işlev alandaki bu özelliğin geçmiş değerlerini alır.

**Tür**: genel işlev

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *Spaceıd* | `guid` | Boşluk tanımlayıcısı |
| *valueName* | `string` | Boşluk özelliği adı |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces (Spaceıd) ⇒ `space[]`

Bir alan tanımlayıcısı verildiğinde, bu işlev bu üst alanın alt alanlarını alır.

**Tür**: genel işlev

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *Spaceıd* | `guid` | Boşluk tanımlayıcısı |

### <a name="getspacechildsensorsspaceid--sensor"></a>Getspacechildsensörleri (Spaceıd) ⇒ `sensor[]`

Bir alan tanımlayıcısı verildiğinde, bu işlev ilgili üst alana ait alt algılayıcılar alır.

**Tür**: genel işlev

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *Spaceıd* | `guid` | Boşluk tanımlayıcısı |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices (Spaceıd) ⇒ `device[]`

Bir alan tanımlayıcısı verildiğinde, bu işlev bu üst alanın alt cihazlarını alır.

**Tür**: genel işlev

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *Spaceıd* | `guid` | Boşluk tanımlayıcısı |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>Getdevicechildalgılayıcılar (DeviceID) ⇒ `sensor[]`

Bir cihaz tanımlayıcısı verildiğinde, bu işlev ilgili üst cihaz için alt algılayıcılar alır.

**Tür**: genel işlev

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *DeviceID* | `guid` | Cihaz tanımlayıcısı |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace (Childspaceıd) ⇒ `space`

Bir alan tanımlayıcısı verildiğinde, bu işlev üst alanını alır.

**Tür**: genel işlev

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *Childspaceıd* | `guid` | Boşluk tanımlayıcısı |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace (Childsensorıd) ⇒ `space`

Bir algılayıcı tanımlayıcısı verildiğinde, bu işlev üst alanını alır.

**Tür**: genel işlev

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *Childsensorıd* | `guid` | Algılayıcı tanımlayıcısı |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace (Childdeviceıd) ⇒ `space`

Bir cihaz tanımlayıcısı verildiğinde, bu işlev üst alanını alır.

**Tür**: genel işlev

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *Childdeviceıd* | `guid` | Cihaz tanımlayıcısı |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice (Childsensorıd) ⇒ `space`

Bir algılayıcı tanımlayıcısı verildiğinde, bu işlev üst cihazını alır.

**Tür**: genel işlev

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *Childsensorıd* | `guid` | Algılayıcı tanımlayıcısı |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty (Spaceıd, propertyName) ⇒ `extendedProperty`

Bir alan tanımlayıcısı verildiğinde, bu işlev özelliği ve değerini alandan alır.

**Tür**: genel işlev

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *Spaceıd* | `guid` | Boşluk tanımlayıcısı |
| *Başlaması* | `string` | Boşluk özelliği adı |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty (Sensorıd, propertyName) ⇒ `extendedProperty`

Bir algılayıcı tanımlayıcısı verildiğinde, bu işlev özelliği ve onun değerini sensörden alır.

**Tür**: genel işlev

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *Sensorıd* | `guid` | Algılayıcı tanımlayıcısı |
| *Başlaması* | `string` | Algılayıcı özelliği adı |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty (DeviceID, propertyName) ⇒ `extendedProperty`

Bir cihaz tanımlayıcısı verildiğinde, bu işlev özelliği ve değerini cihazdan alır.

**Tür**: genel işlev

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *DeviceID* | `guid` | Cihaz tanımlayıcısı |
| *Başlaması* | `string` | Cihaz özelliği adı |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue (Sensorıd, dataType, değer)

Bu işlev, algılayıcı nesnesinde verilen veri türüne sahip bir değer ayarlar.

**Tür**: genel işlev

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *Sensorıd* | `guid` | Algılayıcı tanımlayıcısı |
| *x*  | `string` | Algılayıcı veri türü |
| *value*  | `string` | Değer |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue (Spaceıd, dataType, value)

Bu işlev, alan nesnesinde verilen veri türüne sahip bir değer ayarlar.

**Tür**: genel işlev

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *Spaceıd* | `guid` | Boşluk tanımlayıcısı |
| *x* | `string` | Veri türü |
| *value* | `string` | Değer |

### <a name="logmessage"></a>günlük (ileti)

Bu işlev, Kullanıcı tanımlı işlev içinde aşağıdaki iletiyi günlüğe kaydeder.

**Tür**: genel işlev

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *İleti* | `string` | Günlüğe kaydedilecek ileti |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification (Topologyobjectıd, topologyObjectType, yük)

Bu işlev, dağıtılması için özel bir bildirim gönderir.

**Tür**: genel işlev

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *Topologyobjectıd*  | `guid` | Grafik nesnesi tanımlayıcısı. Örnek olarak boşluk, algılayıcı ve cihaz KIMLIĞI verilebilir.|
| *topologyObjectType*  | `string` | Örnek algılayıcı ve cihazlardır.|
| *te*  | `string` | Bildirimle gönderilecek JSON yükü. |

## <a name="return-types"></a>Dönüş türleri

İstemci başvuru Yardımcısı yöntemlerinden döndürülen yanıt modelleri aşağıda açıklanmaktadır.

### <a name="space"></a>Uzay

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "Space",
  "friendlyName": "Conference Room",
  "typeId": 0,
  "parentSpaceId": "00000000-0000-0000-0000-000000000001",
  "subtypeId": 0
}
```

### <a name="space-methods"></a>Boşluk yöntemleri

#### <a name="parent--space"></a>Parent () ⇒ `space`

Bu işlev, geçerli alanın üst alanını döndürür.

#### <a name="childsensors--sensor"></a>Childsensörler () ⇒ `sensor[]`

Bu işlev, geçerli alanın alt sensörleri döndürür.

#### <a name="childdevices--device"></a>ChildDevices () ⇒ `device[]`

Bu işlev, geçerli alanın alt cihazlarını döndürür.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty (propertyName) ⇒ `extendedProperty`

Bu işlev, geçerli alanın genişletilmiş özelliğini ve değerini döndürür.

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *Başlaması* | `string` | Genişletilmiş özelliğin adı |

#### <a name="valuevaluename--value"></a>Değer (valueName) ⇒ `value`

Bu işlev, geçerli alanın değerini döndürür.

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Değerin adı |

#### <a name="historyvaluename--value"></a>Geçmiş (valueName) ⇒ `value[]`

Bu işlev, geçerli alanın geçmiş değerlerini döndürür.

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Değerin adı |

#### <a name="notifypayload"></a>Bildir (yük)

Bu işlev, belirtilen yük ile bir bildirim gönderir.

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *te* | `string` | Bildirime dahil edilecek JSON yükü |

### <a name="device"></a>Cihaz

```JSON
{
  "id": "00000000-0000-0000-0000-000000000002",
  "name": "Device",
  "friendlyName": "Temperature Sensing Device",
  "description": "This device contains a sensor that captures temperature readings.",
  "type": "None",
  "subtype": "None",
  "typeId": 0,
  "subtypeId": 0,
  "hardwareId": "ABC123",
  "gatewayId": "ABC",
  "spaceId": "00000000-0000-0000-0000-000000000000"
}
```

### <a name="device-methods"></a>Cihaz yöntemleri

#### <a name="parent--space"></a>Parent () ⇒ `space`

Bu işlev, geçerli cihazın üst alanını döndürür.

#### <a name="childsensors--sensor"></a>Childsensörler () ⇒ `sensor[]`

Bu işlev, geçerli cihazın alt sensörleri döndürür.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty (propertyName) ⇒ `extendedProperty`

Bu işlev, geçerli cihaz için Genişletilmiş özelliği ve değerini döndürür.

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *Başlaması* | `string` | Genişletilmiş özelliğin adı |

#### <a name="notifypayload"></a>Bildir (yük)

Bu işlev, belirtilen yük ile bir bildirim gönderir.

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *te* | `string` | Bildirime dahil edilecek JSON yükü |

### <a name="sensor"></a>Algılayıcısı

```JSON
{
  "id": "00000000-0000-0000-0000-000000000003",
  "port": "30",
  "pollRate": 3600,
  "dataType": "Temperature",
  "dataSubtype": "None",
  "type": "Classic",
  "portType": "None",
  "dataUnitType": "FahrenheitTemperature",
  "spaceId": "00000000-0000-0000-0000-000000000000",
  "deviceId": "00000000-0000-0000-0000-000000000001",
  "portTypeId": 0,
  "dataUnitTypeId": 0,
  "dataTypeId": 0,
  "dataSubtypeId": 0,
  "typeId": 0  
}
```

### <a name="sensor-methods"></a>Algılayıcı yöntemleri

#### <a name="space--space"></a>Boşluk () ⇒ `space`

Bu işlev, geçerli algılayıcının üst alanını döndürür.

#### <a name="device--device"></a>Cihaz () ⇒ `device`

Bu işlev, geçerli algılayıcının ana cihazını döndürür.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty (propertyName) ⇒ `extendedProperty`

Bu işlev, geçerli algılayıcı için Genişletilmiş özelliği ve değerini döndürür.

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *Başlaması* | `string` | Genişletilmiş özelliğin adı |

#### <a name="value--value"></a>Değer () ⇒ `value`

Bu işlev, geçerli algılayıcının değerini döndürür.

#### <a name="history--value"></a>History () ⇒ `value[]`

Bu işlev, geçerli algılayıcının geçmiş değerlerini döndürür.

#### <a name="notifypayload"></a>Bildir (yük)

Bu işlev, belirtilen yük ile bir bildirim gönderir.

| Parametre  | Tür                | Açıklama  |
| ------ | ------------------- | ------------ |
| *te* | `string` | Bildirime dahil edilecek JSON yükü |

### <a name="value"></a>Değer

```JSON
{
  "dataType": "Temperature",
  "value": "70",
  "createdTime": ""
}
```

### <a name="extended-property"></a>Genişletilmiş özellik

```JSON
{
  "name": "OccupancyStatus",
  "value": "Occupied"
}
```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure dijital TWINS Kullanıcı tanımlı işlevleri](./concepts-user-defined-functions.md)hakkında bilgi edinin.

- [Kullanıcı tanımlı işlevler oluşturmayı](./how-to-user-defined-functions.md)öğrenin.

- [Kullanıcı tanımlı işlevlerde hata ayıklamayı](./how-to-diagnose-user-defined-functions.md)öğrenin.
