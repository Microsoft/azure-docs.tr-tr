---
title: Şablon işlevleri-dize
description: Dizelerle çalışmak için bir Azure Resource Manager şablonunda (ARM şablonunda) kullanılacak işlevleri açıklar.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: cff1424562b45bc722f87fa3ec896c1c641ee758
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105108851"
---
# <a name="string-functions-for-arm-templates"></a>ARM şablonları için dize işlevleri

Kaynak Yöneticisi, Azure Resource Manager şablonunuzda dizeler (ARM şablonu) ile çalışmak için aşağıdaki işlevleri sağlar:

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [Concat](#concat)
* [vardır](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [empty](#empty)
* [endsWith](#endswith)
* [adı](#first)
* [formatını](#format)
* [guid](#guid)
* [IndexOf](#indexof)
* [nesnesinde](#json)
* [soyadına](#last)
* [LastIndexOf](#lastindexof)
* [length](#length)
* [newGuid](#newguid)
* [Asma sol](#padleft)
* [değiştirin](#replace)
* [Şimdilik](#skip)
* [ayırmayı](#split)
* [startsWith](#startswith)
* [string](#string)
* [dizeden](#substring)
* [almanız](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [kırpma](#trim)
* [uniqueString](#uniquestring)
* [kullanılmamışsa](#uri)
* [URIComponent](#uricomponent)
* [Urıonenttostring](#uricomponenttostring)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="base64"></a>base64

`base64(inputString)`

Giriş dizesinin Base64 gösterimini döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| InputString |Yes |string |Base64 temsili olarak döndürülecek değer. |

### <a name="return-value"></a>Döndürülen değer

Base64 gösterimini içeren bir dize.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) , Base64 işlevinin nasıl kullanılacağını göstermektedir.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringData": {
      "type": "string",
      "defaultValue": "one, two, three"
    },
    "jsonFormattedData": {
      "type": "string",
      "defaultValue": "{'one': 'a', 'two': 'b'}"
    }
  },
  "variables": {
    "base64String": "[base64(parameters('stringData'))]",
    "base64Object": "[base64(parameters('jsonFormattedData'))]"
  },
  "resources": [
  ],
  "outputs": {
    "base64Output": {
      "type": "string",
      "value": "[variables('base64String')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[base64ToString(variables('base64String'))]"
    },
    "toJsonOutput": {
      "type": "object",
      "value": "[base64ToJson(variables('base64Object'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| base64Output | Dize | b25lLCB0d28sIHRocmVl |
| toStringOutput | Dize | Bir iki üç |
| toJsonOutput | Nesne | {"One": "a", "iki": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Base64 gösterimini JSON nesnesine dönüştürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| Base64value değeri |Yes |string |JSON nesnesine dönüştürülecek Base64 temsili. |

### <a name="return-value"></a>Döndürülen değer

JSON nesnesi.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) bir Base64 değerini dönüştürmek için base64ToJson işlevini kullanır:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringData": {
      "type": "string",
      "defaultValue": "one, two, three"
    },
    "jsonFormattedData": {
      "type": "string",
      "defaultValue": "{'one': 'a', 'two': 'b'}"
    }
  },
  "variables": {
    "base64String": "[base64(parameters('stringData'))]",
    "base64Object": "[base64(parameters('jsonFormattedData'))]"
  },
  "resources": [
  ],
  "outputs": {
    "base64Output": {
      "type": "string",
      "value": "[variables('base64String')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[base64ToString(variables('base64String'))]"
    },
    "toJsonOutput": {
      "type": "object",
      "value": "[base64ToJson(variables('base64Object'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)

```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| base64Output | Dize | b25lLCB0d28sIHRocmVl |
| toStringOutput | Dize | Bir iki üç |
| toJsonOutput | Nesne | {"One": "a", "iki": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Base64 gösterimini dizeye dönüştürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| Base64value değeri |Yes |string |Dizeye dönüştürülecek Base64 temsili. |

### <a name="return-value"></a>Döndürülen değer

Dönüştürülmüş Base64 değeri dizesi.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) bir Base64 değerini dönüştürmek için base64ToString işlevini kullanır:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringData": {
      "type": "string",
      "defaultValue": "one, two, three"
    },
    "jsonFormattedData": {
      "type": "string",
      "defaultValue": "{'one': 'a', 'two': 'b'}"
    }
  },
  "variables": {
    "base64String": "[base64(parameters('stringData'))]",
    "base64Object": "[base64(parameters('jsonFormattedData'))]"
  },
  "resources": [
  ],
  "outputs": {
    "base64Output": {
      "type": "string",
      "value": "[variables('base64String')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[base64ToString(variables('base64String'))]"
    },
    "toJsonOutput": {
      "type": "object",
      "value": "[base64ToJson(variables('base64Object'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringData string = 'one, two, three'
param jsonFormattedData string = '{\'one\': \'a\', \'two\': \'b\'}'

var base64String = base64(stringData)
var base64Object = base64(jsonFormattedData)

output base64Output string = base64String
output toStringOutput string = base64ToString(base64String)
output toJsonOutput object = base64ToJson(base64Object)
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| base64Output | Dize | b25lLCB0d28sIHRocmVl |
| toStringOutput | Dize | Bir iki üç |
| toJsonOutput | Nesne | {"One": "a", "iki": "b"} |

## <a name="concat"></a>concat

`concat (arg1, arg2, arg3, ...)`

Birden çok dize değerini birleştirir ve birleştirilmiş dizeyi döndürür ya da birden çok diziyi birleştirir ve birleştirilmiş diziyi döndürür.

Dize birleştirmesini basitleştirmek için Bıcep [dize ilişkilendirme](https://en.wikipedia.org/wiki/String_interpolation#) söz dizimini destekler.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |dize veya dizi |Birleştirme için ilk dize veya dizi. |
| ek bağımsız değişkenler |No |dize veya dizi |Birleştirme için sıralı sırada ek dizeler veya diziler. |

Bu işlev herhangi bir sayıda bağımsız değişken alabilir ve parametreler için dizeleri ya da dizileri kabul edebilir. Ancak, parametreleri için hem diziler hem de dizeler sağlayamıyoruz. Dizeler yalnızca diğer dizelerle birleştirilir.

### <a name="return-value"></a>Döndürülen değer

Bir dize veya art arda eklenmiş değerler dizisi.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) iki dize değerinin nasıl birleştirileceğini ve birleştirilmiş bir dizenin nasıl dönebileceğinizi gösterir.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "prefix": {
      "type": "string",
      "defaultValue": "prefix"
    }
  },
  "resources": [],
  "outputs": {
    "concatOutput": {
      "type": "string",
      "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param prefix string = 'prefix'

output concatOutput string = concat(prefix, '-', uniqueString(resourceGroup().id))
```

veya

```bicep
param prefix string = 'prefix'

output concatOutput string = '${prefix}-${uniqueString(resourceGroup().id)}'
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| Içtoutput | Dize | önek-5yıj4yıjf5mbg72 |

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) , iki dizinin nasıl birleştirileceğini gösterir.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstArray": {
      "type": "array",
      "defaultValue": [
        "1-1",
        "1-2",
        "1-3"
      ]
    },
    "secondArray": {
      "type": "array",
      "defaultValue": [
        "2-1",
        "2-2",
        "2-3"
      ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "return": {
      "type": "array",
      "value": "[concat(parameters('firstArray'), parameters('secondArray'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstArray array = [
  '1-1'
  '1-2'
  '1-3'
]
param secondArray array = [
  '2-1'
  '2-2'
  '2-3'
]

output return array = concat(firstArray, secondArray)
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| return | Dizi | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

## <a name="contains"></a>contains

`contains (container, itemToFind)`

Bir dizinin bir değer içerip içermediğini denetler, bir nesne anahtar içeriyor veya dize bir alt dize içeriyor. Dize karşılaştırma büyük/küçük harfe duyarlıdır. Ancak, bir nesne bir anahtar içeriyorsa test edilirken karşılaştırma büyük/küçük harfe duyarlıdır.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| kapsayıcı |Yes |dizi, nesne veya dize |Bulunacak değeri içeren değer. |
| ıtemtofind |Yes |dize veya tamsayı |Bulunacak değer. |

### <a name="return-value"></a>Döndürülen değer

Öğe bulunursa **true** ; Aksi takdirde, **false**.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) , nasıl kullanılacağını farklı türlerde gösterir:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToTest": {
      "type": "string",
      "defaultValue": "OneTwoThree"
    },
    "objectToTest": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b",
        "three": "c"
      }
    },
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "stringTrue": {
      "type": "bool",
      "value": "[contains(parameters('stringToTest'), 'e')]"
    },
    "stringFalse": {
      "type": "bool",
      "value": "[contains(parameters('stringToTest'), 'z')]"
    },
    "objectTrue": {
      "type": "bool",
      "value": "[contains(parameters('objectToTest'), 'one')]"
    },
    "objectFalse": {
      "type": "bool",
      "value": "[contains(parameters('objectToTest'), 'a')]"
    },
    "arrayTrue": {
      "type": "bool",
      "value": "[contains(parameters('arrayToTest'), 'three')]"
    },
    "arrayFalse": {
      "type": "bool",
      "value": "[contains(parameters('arrayToTest'), 'four')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToTest string = 'OneTwoThree'
param objectToTest object = {
  'one': 'a'
  'two': 'b'
  'three': 'c'
}
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output stringTrue bool = contains(stringToTest, 'e')
output stringFalse bool = contains(stringToTest, 'z')
output objectTrue bool = contains(objectToTest, 'one')
output objectFalse bool = contains(objectToTest, 'a')
output arrayTrue bool = contains(arrayToTest, 'three')
output arrayFalse bool = contains(arrayToTest, 'four')
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| stringTrue | Bool | Doğru |
| stringFalse | Bool | Yanlış |
| objectTrue | Bool | Doğru |
| Objectfali | Bool | Yanlış |
| arrayTrue | Bool | Doğru |
| arrayFalse | Bool | Yanlış |

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Bir değeri veri URI 'sine dönüştürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| stringToConvert |Yes |string |Bir veri URI 'sine dönüştürülecek değer. |

### <a name="return-value"></a>Döndürülen değer

Veri URI 'SI olarak biçimlendirilen bir dize.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) bir değeri BIR veri URI 'sine dönüştürür ve bir veri URI 'sini bir dizeye dönüştürür:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToTest": {
      "type": "string",
      "defaultValue": "Hello"
    },
    "dataFormattedString": {
      "type": "string",
      "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
    }
  },
  "resources": [],
  "outputs": {
    "dataUriOutput": {
      "value": "[dataUri(parameters('stringToTest'))]",
      "type": "string"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[dataUriToString(parameters('dataFormattedString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToTest string = 'Hello'
param dataFormattedString string = 'data:;base64,SGVsbG8sIFdvcmxkIQ=='

output dataUriOutput string = dataUri(stringToTest)
output toStringOutput string = dataUriToString(dataFormattedString)
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| dataUriOutput | Dize | veri: metin/düz; charset = utf8; Base64, SGVsbG8 = |
| toStringOutput | Dize | Merhaba Dünya! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

Veri URI 'SI biçimli değeri bir dizeye dönüştürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Yes |string |Dönüştürülecek veri URI değeri. |

### <a name="return-value"></a>Döndürülen değer

Dönüştürülmüş değeri içeren bir dize.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) bir değeri BIR veri URI 'sine dönüştürür ve bir veri URI 'sini bir dizeye dönüştürür:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringToTest": {
      "type": "string",
      "defaultValue": "Hello"
    },
    "dataFormattedString": {
      "type": "string",
      "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
    }
  },
  "resources": [],
  "outputs": {
    "dataUriOutput": {
      "value": "[dataUri(parameters('stringToTest'))]",
      "type": "string"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[dataUriToString(parameters('dataFormattedString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringToTest string = 'Hello'
param dataFormattedString string = 'data:;base64,SGVsbG8sIFdvcmxkIQ=='

output dataUriOutput string = dataUri(stringToTest)
output toStringOutput string = dataUriToString(dataFormattedString)
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| dataUriOutput | Dize | veri: metin/düz; charset = utf8; Base64, SGVsbG8 = |
| toStringOutput | Dize | Merhaba Dünya! |

## <a name="empty"></a>empty

`empty(itemToTest)`

Bir dizi, nesne veya dize boş olup olmadığını belirler.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| ıtemtotest |Yes |dizi, nesne veya dize |Boş olup olmadığını denetlemek için değer. |

### <a name="return-value"></a>Döndürülen değer

Değer boşsa, **true** döndürür; Aksi takdirde, **false**.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) bir dizi, nesne ve dizenin boş olup olmadığını denetler.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testArray": {
      "type": "array",
      "defaultValue": []
    },
    "testObject": {
      "type": "object",
      "defaultValue": {}
    },
    "testString": {
      "type": "string",
      "defaultValue": ""
    }
  },
  "resources": [
  ],
  "outputs": {
    "arrayEmpty": {
      "type": "bool",
      "value": "[empty(parameters('testArray'))]"
    },
    "objectEmpty": {
      "type": "bool",
      "value": "[empty(parameters('testObject'))]"
    },
    "stringEmpty": {
      "type": "bool",
      "value": "[empty(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testArray array = []
param testObject object = {}
param testString string = ''

output arrayEmpty bool = empty(testArray)
output objectEmpty bool = empty(testObject)
output stringEmpty bool = empty(testString)
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| arrayEmpty | Bool | Doğru |
| objectEmpty | Bool | Doğru |
| stringEmpty | Bool | Doğru |

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Bir dizenin bir değer ile bitip bitmeyeceğini belirler. Karşılaştırma büyük/küçük harfe duyarlıdır.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |string |Bulunacak öğeyi içeren değer. |
| stringToFind |Yes |string |Bulunacak değer. |

### <a name="return-value"></a>Döndürülen değer

Dizenin son karakteri veya karakterleri değerle eşleşiyorsa **doğru** ; Aksi takdirde, **false**.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) , StartsWith ve EndsWith işlevlerinin nasıl kullanılacağını gösterir:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "startsTrue": {
      "value": "[startsWith('abcdef', 'ab')]",
      "type": "bool"
    },
    "startsCapTrue": {
      "value": "[startsWith('abcdef', 'A')]",
      "type": "bool"
    },
    "startsFalse": {
      "value": "[startsWith('abcdef', 'e')]",
      "type": "bool"
    },
    "endsTrue": {
      "value": "[endsWith('abcdef', 'ef')]",
      "type": "bool"
    },
    "endsCapTrue": {
      "value": "[endsWith('abcdef', 'F')]",
      "type": "bool"
    },
    "endsFalse": {
      "value": "[endsWith('abcdef', 'e')]",
      "type": "bool"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output startsTrue bool = startsWith('abcdef', 'ab')
output startsCapTrue bool = startsWith('abcdef', 'A')
output startsFalse bool = startsWith('abcdef', 'e')
output endsTrue bool = endsWith('abcdef', 'ef')
output endsCapTrue bool = endsWith('abcdef', 'F')
output endsFalse bool = endsWith('abcdef', 'e')
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| startsTrue | Bool | Doğru |
| startsCapTrue | Bool | Doğru |
| startsFalse | Bool | Yanlış |
| endsTrue | Bool | Doğru |
| endsCapTrue | Bool | Doğru |
| endsFalse | Bool | Yanlış |

## <a name="first"></a>adı

`first(arg1)`

Dizenin ilk karakterini veya dizideki ilk öğeyi döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |dizi veya dize |İlk öğe veya karakteri alma değeri. |

### <a name="return-value"></a>Döndürülen değer

Bir dizideki ilk öğenin türü (dize, int, dizi veya nesne).

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) , ilk işlevin dizi ve dize ile nasıl kullanılacağını göstermektedir.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "arrayOutput": {
      "type": "string",
      "value": "[first(parameters('arrayToTest'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[first('One Two Three')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output arrayOutput string = first(arrayToTest)
output stringOutput string = first('One Two Three')
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| arrayOutput | Dize | bir |
| stringOutput | Dize | O |

## <a name="format"></a>biçim

`format(formatString, arg1, arg2, ...)`

Giriş değerlerinden biçimli bir dize oluşturur.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| formatString | Yes | string | Bileşik biçim dizesi. |
| arg1 | Yes | dize, tamsayı veya Boole | Biçimlendirilen dizeye dahil edilecek değer. |
| ek bağımsız değişkenler | No | dize, tamsayı veya Boole | Biçimlendirilen dizeye eklenecek ek değerler. |

### <a name="remarks"></a>Açıklamalar

Şablonunuzda bir dizeyi biçimlendirmek için bu işlevi kullanın. .NET 'teki [System. String. Format](/dotnet/api/system.string.format) yöntemiyle aynı biçimlendirme seçeneklerini kullanır.

### <a name="examples"></a>Örnekler

Aşağıdaki örnek şablon, biçim işlevinin nasıl kullanılacağını göstermektedir.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "greeting": {
      "type": "string",
      "defaultValue": "Hello"
    },
    "name": {
      "type": "string",
      "defaultValue": "User"
    },
    "numberToFormat": {
      "type": "int",
      "defaultValue": 8175133
    }
  },
  "resources": [
  ],
  "outputs": {
    "formatTest": {
      "type": "string",
      "value": "[format('{0}, {1}. Formatted number: {2:N0}', parameters('greeting'), parameters('name'), parameters('numberToFormat'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param greeting string = 'Hello'
param name string = 'User'
param numberToFormat int = 8175133

output formatTest string = format('{0}, {1}. Formatted number: {2:N0}', greeting, name, numberToFormat)
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| formatTest | Dize | Merhaba, Kullanıcı. Biçimli sayı: 8.175.133 |

## <a name="guid"></a>guid

`guid(baseString, ...)`

Parametre olarak belirtilen değerlere göre genel benzersiz tanımlayıcı biçiminde bir değer oluşturur.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| baseString |Yes |string |GUID oluşturmak için karma işlevde kullanılan değer. |
| gerektiğinde ek parametreler |No |string |Benzersizlik düzeyini belirten değeri oluşturmak için gereken sayıda dize ekleyebilirsiniz. |

### <a name="remarks"></a>Açıklamalar

Bu işlev, genel benzersiz tanımlayıcı biçiminde bir değer oluşturmanız gerektiğinde faydalıdır. Sonuç için benzersizlik kapsamını sınırlayan parametre değerleri sağlarsınız. Adın aboneliğe, kaynak grubuna veya dağıtıma göre benzersiz olup olmadığını belirtebilirsiniz.

Döndürülen değer bir rastgele dize değil, parametreler üzerindeki bir karma işlevin sonucu. Döndürülen değer 36 karakter uzunluğundadır. Bu, genel olarak benzersiz değildir. Parametrelerin karma değerini temel alan yeni bir GUID oluşturmak için [NewGuid](#newguid) işlevini kullanın.

Aşağıdaki örneklerde, yaygın olarak kullanılan düzeyler için benzersiz bir değer oluşturmak üzere GUID 'in nasıl kullanılacağı gösterilmektedir.

Aboneliğin benzersiz kapsamı

# <a name="json"></a>[JSON](#tab/json)

```json
"[guid(subscription().subscriptionId)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
guid(subscription().subscriptionId)
```

---

Kaynak grubu için benzersiz kapsam

# <a name="json"></a>[JSON](#tab/json)

```json
"[guid(resourceGroup().id)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
guid(resourceGroup().id)
```

---

Kaynak grubu için dağıtımın benzersiz kapsamı

# <a name="json"></a>[JSON](#tab/json)

```json
"[guid(resourceGroup().id, deployment().name)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
guid(resourceGroup().id, deployment().name)
```

---

### <a name="return-value"></a>Döndürülen değer

Genel benzersiz tanımlayıcı biçiminde 36 karakter içeren bir dize.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) GUID 'den sonuçlar döndürüyor:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [],
  "outputs": {
    "guidPerSubscription": {
      "value": "[guid(subscription().subscriptionId)]",
      "type": "string"
    },
    "guidPerResourceGroup": {
      "value": "[guid(resourceGroup().id)]",
      "type": "string"
    },
    "guidPerDeployment": {
      "value": "[guid(resourceGroup().id, deployment().name)]",
      "type": "string"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output guidPerSubscription string = guid(subscription().subscriptionId)
output guidPerResourceGroup string = guid(resourceGroup().id)
output guidPerDeployment string = guid(resourceGroup().id, deployment().name)
```

---

## <a name="indexof"></a>IndexOf

`indexOf(stringToSearch, stringToFind)`

Bir dize içindeki bir değerin ilk konumunu döndürür. Karşılaştırma büyük/küçük harfe duyarlıdır.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |string |Bulunacak öğeyi içeren değer. |
| stringToFind |Yes |string |Bulunacak değer. |

### <a name="return-value"></a>Döndürülen değer

Bulunacak öğenin konumunu temsil eden bir tamsayı. Değer sıfır tabanlıdır. Öğe bulunmazsa-1 döndürülür.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) , IndexOf ve LastIndexOf işlevlerinin nasıl kullanılacağını göstermektedir:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "firstT": {
      "value": "[indexOf('test', 't')]",
      "type": "int"
    },
    "lastT": {
      "value": "[lastIndexOf('test', 't')]",
      "type": "int"
    },
    "firstString": {
      "value": "[indexOf('abcdef', 'CD')]",
      "type": "int"
    },
    "lastString": {
      "value": "[lastIndexOf('abcdef', 'AB')]",
      "type": "int"
    },
    "notFound": {
      "value": "[indexOf('abcdef', 'z')]",
      "type": "int"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output firstT int = indexOf('test', 't')
output lastT int = lastIndexOf('test', 't')
output firstString int = indexOf('abcdef', 'CD')
output lastString int = lastIndexOf('abcdef', 'AB')
output notFound int = indexOf('abcdef', 'z')
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| firstT | int | 0 |
| lastT | int | 3 |
| firstString | int | 2 |
| lastString | int | 0 |
| Bulunamadı | int | -1 |

<a id="json"></a>

## <a name="json"></a>json

`json(arg1)`

Geçerli bir JSON dizesini JSON veri türüne dönüştürür. Daha fazla bilgi için bkz. [JSON işlevi](template-functions-object.md#json).

## <a name="last"></a>Son

`last (arg1)`

Dizenin son karakterini veya dizideki son öğeyi döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |dizi veya dize |Son öğe veya karakteri alma değeri. |

### <a name="return-value"></a>Döndürülen değer

Bir dizideki son öğenin bir dizesi veya türü (dize, int, dizi veya nesne).

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) , son işlevin bir dizi ve dize ile nasıl kullanılacağını gösterir.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [ "one", "two", "three" ]
    }
  },
  "resources": [
  ],
  "outputs": {
    "arrayOutput": {
      "type": "string",
      "value": "[last(parameters('arrayToTest'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[last('One Two Three')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]

output arrayOutput string = last(arrayToTest)
output stringOutput string = last('One Two Three')
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| arrayOutput | Dize | Üç |
| stringOutput | Dize | e |

## <a name="lastindexof"></a>LastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

Bir dize içindeki bir değerin son konumunu döndürür. Karşılaştırma büyük/küçük harfe duyarlıdır.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |string |Bulunacak öğeyi içeren değer. |
| stringToFind |Yes |string |Bulunacak değer. |

### <a name="return-value"></a>Döndürülen değer

Bulunacak öğenin son konumunu temsil eden bir tamsayı. Değer sıfır tabanlıdır. Öğe bulunmazsa-1 döndürülür.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) , IndexOf ve LastIndexOf işlevlerinin nasıl kullanılacağını göstermektedir:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "firstT": {
      "value": "[indexOf('test', 't')]",
      "type": "int"
    },
    "lastT": {
      "value": "[lastIndexOf('test', 't')]",
      "type": "int"
    },
    "firstString": {
      "value": "[indexOf('abcdef', 'CD')]",
      "type": "int"
    },
    "lastString": {
      "value": "[lastIndexOf('abcdef', 'AB')]",
      "type": "int"
    },
    "notFound": {
      "value": "[indexOf('abcdef', 'z')]",
      "type": "int"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output firstT int = indexOf('test', 't')
output lastT int = lastIndexOf('test', 't')
output firstString int = indexOf('abcdef', 'CD')
output lastString int = lastIndexOf('abcdef', 'AB')
output notFound int = indexOf('abcdef', 'z')
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| firstT | int | 0 |
| lastT | int | 3 |
| firstString | int | 2 |
| lastString | int | 0 |
| Bulunamadı | int | -1 |

## <a name="length"></a>length

`length(string)`

Bir dizedeki karakter sayısını, dizideki öğeleri veya bir nesnedeki kök düzeyi özellikleri döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |dizi, dize veya nesne |Öğe sayısının alınması için kullanılacak dizi, karakter sayısını almak için kullanılacak dize veya kök düzeyi özelliklerinin sayısını almak için kullanılacak nesne. |

### <a name="return-value"></a>Döndürülen değer

Bir int.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) , uzunluğunun bir dizi ve dize ile nasıl kullanılacağını gösterir:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "arrayToTest": {
      "type": "array",
      "defaultValue": [
        "one",
        "two",
        "three"
      ]
    },
    "stringToTest": {
      "type": "string",
      "defaultValue": "One Two Three"
    },
    "objectToTest": {
      "type": "object",
      "defaultValue": {
        "propA": "one",
        "propB": "two",
        "propC": "three",
        "propD": {
          "propD-1": "sub",
          "propD-2": "sub"
        }
      }
    }
  },
  "resources": [],
  "outputs": {
    "arrayLength": {
      "type": "int",
      "value": "[length(parameters('arrayToTest'))]"
    },
    "stringLength": {
      "type": "int",
      "value": "[length(parameters('stringToTest'))]"
    },
    "objectLength": {
      "type": "int",
      "value": "[length(parameters('objectToTest'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param arrayToTest array = [
  'one'
  'two'
  'three'
]
param stringToTest string = 'One Two Three'
param objectToTest object = {
  'propA': 'one'
  'propB': 'two'
  'propC': 'three'
  'propD': {
    'propD-1': 'sub'
    'propD-2': 'sub'
  }
}

output arrayLength int = length(arrayToTest)
output stringLength int = length(stringToTest)
output objectLength int = length(objectToTest)
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| arrayLength | int | 3 |
| stringLength | int | 13 |
| objectLength | int | 4 |

## <a name="newguid"></a>newGuid

`newGuid()`

Genel benzersiz tanımlayıcı biçimindeki bir değer döndürür. **Bu işlev, yalnızca bir parametre için varsayılan değerde kullanılabilir.**

### <a name="remarks"></a>Açıklamalar

Bu işlevi yalnızca bir parametresinin varsayılan değeri için bir ifade içinde kullanabilirsiniz. Bu işlevi bir şablonda başka bir yerde kullanmak bir hata döndürür. İşlevin her çağrılışında farklı bir değer döndürdüğünden, bu işleve şablon diğer bölümlerinde izin verilmiyor. Aynı parametrelerle aynı şablon dağıtmak, aynı sonuçları güvenilir bir şekilde üretmez.

NewGuid işlevi herhangi bir parametre içermediğinden [GUID](#guid) işlevinden farklıdır. Aynı parametreye sahip GUID 'yi çağırdığınızda, her seferinde aynı tanımlayıcıyı döndürür. Belirli bir ortam için aynı GUID 'i güvenilir bir şekilde oluşturmanız gerektiğinde GUID kullanın. Kaynakları bir test ortamına dağıtmak gibi her seferinde farklı bir tanımlayıcıya ihtiyacınız olduğunda NEWGUID kullanın.

NewGuid işlevi, genel benzersiz tanımlayıcıyı oluşturmak için .NET Framework [GUID yapısını](/dotnet/api/system.guid) kullanır.

[Daha önceki başarılı bir dağıtımı yeniden dağıtmak için seçeneğini](rollback-on-error.md)kullanırsanız ve önceki dağıtım NEWGUID kullanan bir parametre içeriyorsa, parametre yeniden değerlendirilmez. Bunun yerine, önceki dağıtımdaki parametre değeri geri alma dağıtımında otomatik olarak yeniden kullanılır.

Bir sınama ortamında, yalnızca kısa bir süre için canlı kaynakları tekrar tekrar dağıtmanız gerekebilir. Benzersiz adlar oluşturmak yerine, benzersiz adlar oluşturmak için [Uniquestring](#uniquestring) Ile NEWGUID kullanabilirsiniz.

Varsayılan bir değer için newGuid işlevine dayanan bir şablonu yeniden dağıtma konusunda dikkatli olun. Yeniden dağıtırken ve parametresi için bir değer sağlamazsanız, işlev yeniden değerlendirilerek yapılır. Yeni bir kaynak oluşturmak yerine var olan bir kaynağı güncelleştirmek istiyorsanız, önceki dağıtımdan parametre değerini geçirin.

### <a name="return-value"></a>Döndürülen değer

Genel benzersiz tanımlayıcı biçiminde 36 karakter içeren bir dize.

### <a name="examples"></a>Örnekler

Aşağıdaki örnek şablonda, yeni tanımlayıcı içeren bir parametre gösterilmektedir.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "guidValue": {
      "type": "string",
      "defaultValue": "[newGuid()]"
    }
  },
  "resources": [
  ],
  "outputs": {
    "guidOutput": {
      "type": "string",
      "value": "[parameters('guidValue')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param guidValue string = newGuid()

output guidOutput string = guidValue
```

---

Yukarıdaki örnekteki çıktı her dağıtım için farklılık gösterir, ancak şuna benzer olacaktır:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| guidOutput | string | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

Aşağıdaki örnek, bir depolama hesabı için benzersiz bir ad oluşturmak üzere newGuid işlevini kullanır. Bu şablon, depolama hesabının kısa bir süre için bulunduğu ve yeniden dağıtılmadığı test ortamında çalışabilir.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "guidValue": {
      "type": "string",
      "defaultValue": "[newGuid()]"
    }
  },
  "variables": {
    "storageName": "[concat('storage', uniqueString(parameters('guidValue')))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageName')]",
      "location": "West US",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "nameOutput": {
      "type": "string",
      "value": "[variables('storageName')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param guidValue string = newGuid()

var storageName = 'storage${uniqueString(guidValue)}'

resource myStorage 'Microsoft.Storage/storageAccounts@2018-07-01' = {
  name: storageName
  location: 'West US'
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
  properties: {}
}

output nameOutput string = storageName
```

---

Yukarıdaki örnekteki çıktı her dağıtım için farklılık gösterir, ancak şuna benzer olacaktır:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| nameOutput | string | storagenziwvyru7uxie |

## <a name="padleft"></a>Asma sol

`padLeft(valueToPad, totalLength, paddingCharacter)`

Belirtilen toplam uzunluğa ulaşana kadar sola karakter ekleyerek sağa hizalanmış bir dize döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| valueToPad |Yes |dize veya tamsayı |Sağa hizalamak için değer. |
| Toplam uzunluğu |Yes |int |Döndürülen dizedeki toplam karakter sayısı. |
| Doldurma Ingcharacter |No |tek karakter |Toplam uzunluğa ulaşılana kadar sol doldurma için kullanılacak karakter. Varsayılan değer bir alandır. |

Özgün dize, doldurma karakteri sayısından uzunsa, hiçbir karakter eklenmez.

### <a name="return-value"></a>Döndürülen değer

En az belirtilen karakter sayısına sahip bir dize.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) , toplam karakter sayısına ulaşıncaya kadar sıfır karakteri ekleyerek kullanıcı tarafından sağlanmış parametre değerinin nasıl gösterileceğini gösterir.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "123"
    }
  },
  "resources": [],
  "outputs": {
    "stringOutput": {
      "type": "string",
      "value": "[padLeft(parameters('testString'),10,'0')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = '123'

output stringOutput string = padLeft(testString, 10, '0')
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| stringOutput | Dize | 0000000123 |

## <a name="replace"></a>değiştirin

`replace(originalString, oldString, newString)`

Başka bir dize tarafından değiştirilmiş bir dizenin tüm örneklerine sahip yeni bir dize döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| originalString |Yes |string |Bir dizenin tüm örneklerine sahip olan değer başka bir dize tarafından değiştirildi. |
| Eskidize |Yes |string |Özgün dizeden kaldırılacak dize. |
| Yenidize |Yes |string |Kaldırılan dizenin yerine eklenecek dize. |

### <a name="return-value"></a>Döndürülen değer

Değiştirilmiş karakterlerle bir dize.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) , Kullanıcı tarafından belirtilen dizeden tüm tirelerin nasıl kaldırılacağını ve dizenin bir kısmının başka bir dizeyle nasıl değiştirileceğini gösterir.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "123-123-1234"
    }
  },
  "resources": [],
  "outputs": {
    "firstOutput": {
      "type": "string",
      "value": "[replace(parameters('testString'),'-', '')]"
    },
    "secondOutput": {
      "type": "string",
      "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = '123-123-1234'

output firstOutput string = replace(testString, '-', '')
output secondOutput string = replace(testString, '1234', 'xxxx')
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| firstOutput | Dize | 1231231234 |
| secondOutput | Dize | 123-123-xxxx |

## <a name="skip"></a>Atla

`skip(originalValue, numberToSkip)`

Belirtilen sayıda karakterden sonraki tüm karakterleri içeren bir dize veya belirtilen sayıda öğeden sonra tüm öğeleri içeren bir dizi döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| originalValue |Yes |dizi veya dize |Atlama için kullanılacak dizi veya dize. |
| numberToSkip |Yes |int |Atlanacak öğe veya karakter sayısı. Bu değer 0 veya daha azsa, değer içindeki tüm öğeler veya karakterler döndürülür. Dizi veya dizenin uzunluğundan daha büyükse boş bir dizi veya dize döndürülür. |

### <a name="return-value"></a>Döndürülen değer

Bir dizi veya dize.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) dizideki belirtilen sayıda öğeyi ve bir dizedeki belirtilen karakter sayısını atlar.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testArray": {
      "type": "array",
      "defaultValue": [
        "one",
        "two",
        "three"
      ]
    },
    "elementsToSkip": {
      "type": "int",
      "defaultValue": 2
    },
    "testString": {
      "type": "string",
      "defaultValue": "one two three"
    },
    "charactersToSkip": {
      "type": "int",
      "defaultValue": 4
    }
  },
  "resources": [],
  "outputs": {
    "arrayOutput": {
      "type": "array",
      "value": "[skip(parameters('testArray'),parameters('elementsToSkip'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[skip(parameters('testString'),parameters('charactersToSkip'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testArray array = [
  'one'
  'two'
  'three'
]
param elementsToSkip int = 2
param testString string = 'one two three'
param charactersToSkip int = 4

output arrayOutput array = skip(testArray, elementsToSkip)
output stringOutput string = skip(testString, charactersToSkip)
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| arrayOutput | Dizi | ["üç"] |
| stringOutput | Dize | 2 3 |

## <a name="split"></a>split

`split(inputString, delimiter)`

Belirtilen sınırlayıcılar tarafından ayrılan giriş dizesinin alt dizelerini içeren dizelerin dizisini döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| InputString |Yes |string |Bölünecek dize. |
| sınırlayıcı |Yes |dizelerin dizesi veya dizisi |Dizeyi bölmek için kullanılacak sınırlayıcı. |

### <a name="return-value"></a>Döndürülen değer

Dizeler dizisi.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) , giriş dizesini virgül ile ve virgül ya da noktalı virgülle ayırır.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstString": {
      "type": "string",
      "defaultValue": "one,two,three"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "one;two,three"
    }
  },
  "variables": {
    "delimiters": [ ",", ";" ]
  },
  "resources": [],
  "outputs": {
    "firstOutput": {
      "type": "array",
      "value": "[split(parameters('firstString'),',')]"
    },
    "secondOutput": {
      "type": "array",
      "value": "[split(parameters('secondString'),variables('delimiters'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstString string = 'one,two,three'
param secondString string = 'one;two,three'

var delimiters = [
  ','
  ';'
]

output firstOutput array = split(firstString, ',')
output secondOutput array = split(secondString, delimiters)
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| firstOutput | Dizi | ["bir", "iki", "üç"] |
| secondOutput | Dizi | ["bir", "iki", "üç"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Bir dizenin bir değerle başlatılıp başlatılmayacağını belirler. Karşılaştırma büyük/küçük harfe duyarlıdır.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |string |Bulunacak öğeyi içeren değer. |
| stringToFind |Yes |string |Bulunacak değer. |

### <a name="return-value"></a>Döndürülen değer

Dizenin ilk karakteri veya karakterleri değerle eşleşiyorsa **doğru** ; Aksi takdirde, **false**.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) , StartsWith ve EndsWith işlevlerinin nasıl kullanılacağını gösterir:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "startsTrue": {
      "value": "[startsWith('abcdef', 'ab')]",
      "type": "bool"
    },
    "startsCapTrue": {
      "value": "[startsWith('abcdef', 'A')]",
      "type": "bool"
    },
    "startsFalse": {
      "value": "[startsWith('abcdef', 'e')]",
      "type": "bool"
    },
    "endsTrue": {
      "value": "[endsWith('abcdef', 'ef')]",
      "type": "bool"
    },
    "endsCapTrue": {
      "value": "[endsWith('abcdef', 'F')]",
      "type": "bool"
    },
    "endsFalse": {
      "value": "[endsWith('abcdef', 'e')]",
      "type": "bool"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output startsTrue bool = startsWith('abcdef', 'ab')
output startsCapTrue bool = startsWith('abcdef', 'A')
output startsFalse bool = startsWith('abcdef', 'e')
output endsTrue bool = endsWith('abcdef', 'ef')
output endsCapTrue bool = endsWith('abcdef', 'F')
output endsFalse bool = endsWith('abcdef', 'e')
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| startsTrue | Bool | Doğru |
| startsCapTrue | Bool | Doğru |
| startsFalse | Bool | Yanlış |
| endsTrue | Bool | Doğru |
| endsCapTrue | Bool | Doğru |
| endsFalse | Bool | False |

## <a name="string"></a>string

`string(valueToConvert)`

Belirtilen değeri bir dizeye dönüştürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| valueToConvert |Yes | Herhangi biri |Dizeye dönüştürülecek değer. Nesneler ve diziler dahil olmak üzere herhangi bir değer türü dönüştürülebilirler. |

### <a name="return-value"></a>Döndürülen değer

Dönüştürülmüş değerin dizesi.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablonda](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) farklı değer türlerinin dizelere nasıl dönüştürüleceği gösterilmektedir:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testObject": {
      "type": "object",
      "defaultValue": {
        "valueA": 10,
        "valueB": "Example Text"
      }
    },
    "testArray": {
      "type": "array",
      "defaultValue": [
        "a",
        "b",
        "c"
      ]
    },
    "testInt": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "resources": [],
  "outputs": {
    "objectOutput": {
      "type": "string",
      "value": "[string(parameters('testObject'))]"
    },
    "arrayOutput": {
      "type": "string",
      "value": "[string(parameters('testArray'))]"
    },
    "intOutput": {
      "type": "string",
      "value": "[string(parameters('testInt'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testObject object = {
  'valueA': 10
  'valueB': 'Example Text'
}
param testArray array = [
  'a'
  'b'
  'c'
]
param testInt int = 5

output objectOutput string = string(testObject)
output arrayOutput string = string(testArray)
output intOutput string = string(testInt)
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| objectOutput | Dize | {"valueA": 10, "valueB": "örnek metin"} |
| arrayOutput | Dize | ["a", "b", "c"] |
| ıntoutput | Dize | 5 |

## <a name="substring"></a>dizeden

`substring(stringToParse, startIndex, length)`

Belirtilen karakter konumunda başlayan ve belirtilen sayıda karakteri içeren bir alt dize döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| stringToParse |Yes |string |Alt dizenin ayıklandığı özgün dize. |
| startIndex |No |int |Alt dize için sıfır tabanlı başlangıç karakter konumu. |
| length |No |int |Alt dize için karakter sayısı. Dize içindeki bir konuma başvurmalıdır. Sıfır veya daha büyük olmalıdır. |

### <a name="return-value"></a>Döndürülen değer

Alt dize. Ya da uzunluk sıfır ise boş bir dize.

### <a name="remarks"></a>Açıklamalar

Alt dize dizenin sonunun ötesinde veya uzunluğu sıfırdan küçükse işlev başarısız olur. Aşağıdaki örnek hata ile başarısız olur "Dizin ve uzunluk parametreleri dize içinde bir konuma başvurmalıdır. Dizin parametresi: ' 0 ', length parametresi: ' 11 ', dize parametresinin uzunluğu: ' 10 '. ".

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "inputString": {
    "type": "string",
    "value": "1234567890"
  }
}, "variables": {
  "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param inputString string = '1234567890'

var prefix = substring(inputString, 0, 11)
```

---

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) bir dizeden bir alt dize ayıklar.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "one two three"
    }
  },
  "resources": [],
  "outputs": {
    "substringOutput": {
      "value": "[substring(parameters('testString'), 4, 3)]",
      "type": "string"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = 'one two three'
output substringOutput string = substring(testString, 4, 3)
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| substringOutput | Dize | iki |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Dizenin başından itibaren belirtilen sayıda karaktere sahip bir dize veya dizinin başından itibaren belirtilen sayıda öğe içeren bir dizi döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| originalValue |Yes |dizi veya dize |Öğelerin ele aldığı dizi veya dize. |
| numberToTake |Yes |int |Gerçekleştirilecek öğe veya karakter sayısı. Bu değer 0 veya daha azsa, boş bir dizi veya dize döndürülür. Belirtilen dizi veya dizenin uzunluğundan daha büyükse, dizideki veya dizedeki tüm öğeler döndürülür. |

### <a name="return-value"></a>Döndürülen değer

Bir dizi veya dize.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) , diziden belirtilen sayıda öğeyi ve bir dizeden karakter alır.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testArray": {
      "type": "array",
      "defaultValue": [
        "one",
        "two",
        "three"
      ]
    },
    "elementsToTake": {
      "type": "int",
      "defaultValue": 2
    },
    "testString": {
      "type": "string",
      "defaultValue": "one two three"
    },
    "charactersToTake": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [],
  "outputs": {
    "arrayOutput": {
      "type": "array",
      "value": "[take(parameters('testArray'),parameters('elementsToTake'))]"
    },
    "stringOutput": {
      "type": "string",
      "value": "[take(parameters('testString'),parameters('charactersToTake'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testArray array = [
  'one'
  'two'
  'three'
]
param elementsToTake int = 2
param testString string = 'one two three'
param charactersToTake int = 2

output arrayOutput array = take(testArray, elementsToTake)
output stringOutput string = take(testString, charactersToTake)
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| arrayOutput | Dizi | ["bir", "iki"] |
| stringOutput | Dize | on |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

Belirtilen dizeyi küçük harfe dönüştürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| stringToChange |Yes |string |Küçük harfe dönüştürülecek değer. |

### <a name="return-value"></a>Döndürülen değer

Dize, küçük harfe dönüştürüldü.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) bir parametre değerini küçük harfe ve büyük harflere dönüştürür.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "One Two Three"
    }
  },
  "resources": [],
  "outputs": {
    "toLowerOutput": {
      "value": "[toLower(parameters('testString'))]",
      "type": "string"
    },
    "toUpperOutput": {
      "type": "string",
      "value": "[toUpper(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = 'One Two Three'

output toLowerOutput string = toLower(testString)
output toUpperOutput string = toUpper(testString)
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| Toharfe çıkışı | Dize | Bir iki üç |
| Toüsteçıkış | Dize | Bir iki üç |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

Belirtilen dizeyi büyük harfe dönüştürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| stringToChange |Yes |string |Büyük harfe dönüştürülecek değer. |

### <a name="return-value"></a>Döndürülen değer

Dize, büyük harfe dönüştürüldü.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) bir parametre değerini küçük harfe ve büyük harflere dönüştürür.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "One Two Three"
    }
  },
  "resources": [],
  "outputs": {
    "toLowerOutput": {
      "value": "[toLower(parameters('testString'))]",
      "type": "string"
    },
    "toUpperOutput": {
      "type": "string",
      "value": "[toUpper(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = 'One Two Three'

output toLowerOutput string = toLower(testString)
output toUpperOutput string = toUpper(testString)
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| Toharfe çıkışı | Dize | Bir iki üç |
| Toüsteçıkış | Dize | Bir iki üç |

## <a name="trim"></a>kırpma

`trim (stringToTrim)`

Belirtilen dizeden tüm öndeki ve sondaki boşluk karakterlerini kaldırır.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| stringToTrim |Yes |string |Kırpılacak değer. |

### <a name="return-value"></a>Döndürülen değer

Baştaki ve sondaki boşluk karakterleri olmayan dize.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) , parametresindeki boşluk karakterlerini kırpar.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "testString": {
      "type": "string",
      "defaultValue": "    one two three   "
    }
  },
  "resources": [],
  "outputs": {
    "return": {
      "type": "string",
      "value": "[trim(parameters('testString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param testString string = '    one two three   '

output return string = trim(testString)
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| return | Dize | Bir iki üç |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

Parametre olarak belirtilen değerlere göre belirleyici bir karma dize oluşturur.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| baseString |Yes |string |Benzersiz bir dize oluşturmak için karma işlevde kullanılan değer. |
| gerektiğinde ek parametreler |No |string |Benzersizlik düzeyini belirten değeri oluşturmak için gereken sayıda dize ekleyebilirsiniz. |

### <a name="remarks"></a>Açıklamalar

Bu işlev, bir kaynak için benzersiz bir ad oluşturmanız gerektiğinde faydalıdır. Sonuç için benzersizlik kapsamını sınırlayan parametre değerleri sağlarsınız. Adın aboneliğe, kaynak grubuna veya dağıtıma göre benzersiz olup olmadığını belirtebilirsiniz.

Döndürülen değer bir rastgele dize değil, bunun yerine karma işlevin sonucu. Döndürülen değer 13 karakter uzunluğundadır. Bu, genel olarak benzersiz değildir. Anlamlı bir ad oluşturmak için değeri adlandırma kuralınızdan bir ön ek ile birleştirmek isteyebilirsiniz. Aşağıdaki örnek döndürülen değerin biçimini gösterir. Gerçek değer, belirtilen parametrelere göre değişir.

`tcvhiyu5h2o5o`

Aşağıdaki örneklerde, yaygın olarak kullanılan düzeyler için benzersiz bir değer oluşturmak üzere uniqueString 'in nasıl kullanılacağı gösterilmektedir.

Aboneliğin benzersiz kapsamı

# <a name="json"></a>[JSON](#tab/json)

```json
"[uniqueString(subscription().subscriptionId)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
uniqueString(subscription().subscriptionId)
```

---

Kaynak grubu için benzersiz kapsam

# <a name="json"></a>[JSON](#tab/json)

```json
"[uniqueString(resourceGroup().id)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
uniqueString(resourceGroup().id)
```

---

Kaynak grubu için dağıtımın benzersiz kapsamı

# <a name="json"></a>[JSON](#tab/json)

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
uniqueString(resourceGroup().id, deployment().name)
```

---

Aşağıdaki örnek, kaynak grubunuza göre bir depolama hesabı için benzersiz bir ad oluşturmayı gösterir. Aynı şekilde oluşturulursa, kaynak grubunun içinde adı benzersiz değildir.

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [{
  "name": "[concat('storage', uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Storage/storageAccounts",
  ...
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource mystorage 'Microsoft.Storage/storageAccounts@@2018-07-01' = {
  name: 'storage${uniqueString(resourceGroup().id)}'
  ...
}
```

---

Her bir şablonu dağıtışınızda yeni bir benzersiz ad oluşturmanız gerekiyorsa ve kaynağı güncelleştirmeyi düşünmüyorsanız, [UtcNow](template-functions-date.md#utcnow) Işlevini de uniquestring ile kullanabilirsiniz. Bu yaklaşımı bir test ortamında kullanabilirsiniz. Bir örnek için bkz. [UtcNow](template-functions-date.md#utcnow).

### <a name="return-value"></a>Döndürülen değer

13 karakter içeren bir dize.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) , uniquestring 'den sonuçlar getirir:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "uniqueRG": {
      "value": "[uniqueString(resourceGroup().id)]",
      "type": "string"
    },
    "uniqueDeploy": {
      "value": "[uniqueString(resourceGroup().id, deployment().name)]",
      "type": "string"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output uniqueRG string = uniqueString(resourceGroup().id)
output uniqueDeploy string = uniqueString(resourceGroup().id, deployment().name)
```

---

## <a name="uri"></a>kullanılmamışsa

`uri (baseUri, relativeUri)`

BaseUri ve relativeUri dizesini birleştirerek mutlak bir URI oluşturur.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| baseUri |Yes |string |Taban URI dizesi. Bu tabloda açıklandığı gibi sondaki eğik çizginin ('/') işlenmesiyle ilgili davranışı gözlemlemeye özen gösterin.  |
| relativeUri |Yes |string |Taban URI dizesine eklenecek göreli URI dizesi. |

* **BaseUri** bir sondaki eğik çizgiyle sonlanıyorsa, sonuç yalnızca **BaseUri** URI ve ardından **relativeUri** olur.

* **BaseUri** bir sondaki eğik çizgi ile bitmezse iki şeyin meydana gelir.

   * **BaseUri** 'nin hiç eğik çizgi yoksa (önde gelen "//" dışında), sonuç yalnızca **BaseUri** 'nin ardından **relativeUri** olur.

   * **BaseUri** 'de bazı eğik çizgiler varsa, ancak eğik çizgiyle bitmezse, son eğik çizgiden elde edilen her şey **BaseUri** 'Den kaldırılır ve sonuç **BaseUri** 'nin ardından **relativeUri** olur.

İşte bazı örnekler:

```
uri('http://contoso.org/firstpath', 'myscript.sh') -> http://contoso.org/myscript.sh
uri('http://contoso.org/firstpath/', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json/', 'myscript.sh') -> http://contoso.org/firstpath/azuredeploy.json/myscript.sh
```
Tüm ayrıntılar için, **BaseUri** ve **relativeUri** parametreleri, [RFC 3986, Bölüm 5](https://tools.ietf.org/html/rfc3986#section-5)' te belirtilen şekilde çözümlenir.

### <a name="return-value"></a>Döndürülen değer

Taban ve göreli değerler için mutlak URI 'yi temsil eden bir dize.

### <a name="examples"></a>Örnekler

Aşağıdaki örnek, üst şablonun değerine göre iç içe bir şablonun bağlantısının nasıl oluşturulacağını gösterir.

# <a name="json"></a>[JSON](#tab/json)

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
templateLink: uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')
```

---

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) URI, URIComponent ve Urıonenttostring 'in nasıl kullanılacağını göstermektedir:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
    "uriEncoded": "[uriComponent(variables('uriFormat'))]"
  },
  "resources": [
  ],
  "outputs": {
    "uriOutput": {
      "type": "string",
      "value": "[variables('uriFormat')]"
    },
    "componentOutput": {
      "type": "string",
      "value": "[variables('uriEncoded')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[uriComponentToString(variables('uriEncoded'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| uriOutput | Dize | `http://contoso.com/resources/nested/azuredeploy.json` |
| Bileşen Entoutput | Dize | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Dize | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponent"></a>URIComponent

`uricomponent(stringToEncode)`

Bir URI 'yi kodlar.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| stringToEncode |Yes |string |Kodlanacak değer. |

### <a name="return-value"></a>Döndürülen değer

URI kodlamalı değerin dizesi.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) URI, URIComponent ve Urıonenttostring 'in nasıl kullanılacağını göstermektedir:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
    "uriEncoded": "[uriComponent(variables('uriFormat'))]"
  },
  "resources": [
  ],
  "outputs": {
    "uriOutput": {
      "type": "string",
      "value": "[variables('uriFormat')]"
    },
    "componentOutput": {
      "type": "string",
      "value": "[variables('uriEncoded')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[uriComponentToString(variables('uriEncoded'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| uriOutput | Dize | `http://contoso.com/resources/nested/azuredeploy.json` |
| Bileşen Entoutput | Dize | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Dize | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponenttostring"></a>Urıonenttostring

`uriComponentToString(uriEncodedString)`

URI kodlamalı bir değer String döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Yes |string |Dizeye dönüştürülecek URI kodlamalı değer. |

### <a name="return-value"></a>Döndürülen değer

Kodu çözülen URI kodlamalı değerin dizesi.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) URI, URIComponent ve Urıonenttostring 'in nasıl kullanılacağını göstermektedir:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "variables": {
    "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
    "uriEncoded": "[uriComponent(variables('uriFormat'))]"
  },
  "resources": [
  ],
  "outputs": {
    "uriOutput": {
      "type": "string",
      "value": "[variables('uriFormat')]"
    },
    "componentOutput": {
      "type": "string",
      "value": "[variables('uriEncoded')]"
    },
    "toStringOutput": {
      "type": "string",
      "value": "[uriComponentToString(variables('uriEncoded'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var uriFormat = uri('http://contoso.com/resources/', 'nested/azuredeploy.json')
var uriEncoded = uriComponent(uriFormat)

output uriOutput string = uriFormat
output componentOutput string = uriEncoded
output toStringOutput string = uriComponentToString(uriEncoded)
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| uriOutput | Dize | `http://contoso.com/resources/nested/azuredeploy.json` |
| Bileşen Entoutput | Dize | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Dize | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="next-steps"></a>Sonraki adımlar

* ARM şablonundaki bölümlerin açıklaması için bkz. [ARM şablonlarının yapısını ve sözdizimini anlayın](template-syntax.md).
* Birden çok şablonu birleştirmek için bkz. [Azure kaynaklarını dağıttığınızda bağlı ve iç içe şablonları kullanma](linked-templates.md).
* Kaynak türünü oluştururken belirtilen sayıda yinelemek için, [ARM şablonlarında kaynak yinelemesi](copy-resources.md)bölümüne bakın.
* Oluşturduğunuz şablonun nasıl dağıtılacağını görmek için bkz. [ARM şablonlarıyla kaynak dağıtma ve Azure PowerShell](deploy-powershell.md).
