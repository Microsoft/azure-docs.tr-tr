---
title: Şablon işlevleri-dize
description: Dizelerle çalışmak için bir Azure Resource Manager şablonunda kullanılacak işlevleri açıklar.
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: af972bac39890402cf75e83d10806e9a830c1d4a
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149600"
---
# <a name="string-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager şablonları için dize işlevleri

Kaynak Yöneticisi dizelerle çalışmak için aşağıdaki işlevleri sağlar:

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [concat](#concat)
* [contains](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [empty](#empty)
* [endsWith](#endswith)
* [first](#first)
* [format](#format)
* [guid](#guid)
* [indexOf](#indexof)
* [last](#last)
* [lastIndexOf](#lastindexof)
* [length](#length)
* [newGuid](#newguid)
* [Asma sol](#padleft)
* [replace](#replace)
* [skip](#skip)
* [split](#split)
* [startsWith](#startswith)
* [dize](#string)
* [substring](#substring)
* [take](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [trim](#trim)
* [uniqueString](#uniquestring)
* [uri](#uri)
* [URIComponent](#uricomponent)
* [uriComponentToString](#uricomponenttostring)
* [utcNow](#utcnow)

## <a name="base64"></a>biçiminde

`base64(inputString)`

Giriş dizesinin Base64 gösterimini döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| InputString |Yes |string |Base64 temsili olarak döndürülecek değer. |

### <a name="return-value"></a>Dönüş değeri

Base64 gösterimini içeren bir dize.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) , Base64 işlevinin nasıl kullanılacağını göstermektedir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| base64Output | Dize | b25lLCB0d28sIHRocmVl |
| toStringOutput | Dize | Bir iki üç |
| toJsonOutput | Nesne | {"One": "a", "iki": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Base64 gösterimini JSON nesnesine dönüştürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| base64Value |Yes |string |JSON nesnesine dönüştürülecek Base64 temsili. |

### <a name="return-value"></a>Dönüş değeri

JSON nesnesi.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) bir Base64 değerini dönüştürmek için base64ToJson işlevini kullanır:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| base64Output | Dize | b25lLCB0d28sIHRocmVl |
| toStringOutput | Dize | Bir iki üç |
| toJsonOutput | Nesne | {"One": "a", "iki": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Base64 gösterimini dizeye dönüştürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| base64Value |Yes |string |Dizeye dönüştürülecek Base64 temsili. |

### <a name="return-value"></a>Dönüş değeri

Dönüştürülmüş Base64 değeri dizesi.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) bir Base64 değerini dönüştürmek için base64ToString işlevini kullanır:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| base64Output | Dize | b25lLCB0d28sIHRocmVl |
| toStringOutput | Dize | Bir iki üç |
| toJsonOutput | Nesne | {"One": "a", "iki": "b"} |

## <a name="concat"></a>Concat

`concat (arg1, arg2, arg3, ...)`

Birden çok dize değerini birleştirir ve birleştirilmiş dizeyi döndürür ya da birden çok diziyi birleştirir ve birleştirilmiş diziyi döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |dize veya dizi |Birleştirme için ilk değer. |
| ek bağımsız değişkenler |Hayır |string |Birleştirme için ek değerler sıralı sırada. |

### <a name="return-value"></a>Dönüş değeri
Bir dize veya art arda eklenmiş değerler dizisi.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) iki dize değerinin nasıl birleştirileceğini ve birleştirilmiş bir dizenin nasıl dönebileceğinizi gösterir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]",
            "type" : "string"
        }
    }
}
```

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| Içtoutput | Dize | önek-5yıj4yıjf5mbg72 |

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) , iki dizinin nasıl birleştirileceğini gösterir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| döndürülmesini | Dizi | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

## <a name="contains"></a>içerir

`contains (container, itemToFind)`

Bir dizinin bir değer içerip içermediğini denetler, bir nesne anahtar içeriyor veya dize bir alt dize içeriyor. Dize karşılaştırma büyük/küçük harfe duyarlıdır. Ancak, bir nesne bir anahtar içeriyorsa test edilirken karşılaştırma büyük/küçük harfe duyarlıdır.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| kapsayıcı |Yes |dizi, nesne veya dize |Bulunacak değeri içeren değer. |
| ıtemtofind |Yes |dize veya tamsayı |Bulunacak değer. |

### <a name="return-value"></a>Dönüş değeri

Öğe bulunursa **true** ; Aksi takdirde, **false**.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) , nasıl kullanılacağını farklı türlerde gösterir:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "OneTwoThree"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
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

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| stringTrue | Bool | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| Objectfali | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Bir değeri veri URI 'sine dönüştürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| stringToConvert |Yes |string |Bir veri URI 'sine dönüştürülecek değer. |

### <a name="return-value"></a>Dönüş değeri

Veri URI 'SI olarak biçimlendirilen bir dize.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) bir değeri BIR veri URI 'sine dönüştürür ve bir veri URI 'sini bir dizeye dönüştürür:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| dataUriOutput | Dize | veri: metin/düz; charset = utf8; Base64, SGVsbG8 = |
| toStringOutput | Dize | Merhaba Dünya! |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

Veri URI 'SI biçimli değeri bir dizeye dönüştürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Yes |string |Dönüştürülecek veri URI değeri. |

### <a name="return-value"></a>Dönüş değeri

Dönüştürülmüş değeri içeren bir dize.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) bir değeri BIR veri URI 'sine dönüştürür ve bir veri URI 'sini bir dizeye dönüştürür:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| dataUriOutput | Dize | veri: metin/düz; charset = utf8; Base64, SGVsbG8 = |
| toStringOutput | Dize | Merhaba Dünya! |

## <a name="empty"></a>boş

`empty(itemToTest)`

Bir dizi, nesne veya dize boş olup olmadığını belirler.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| ıtemtotest |Yes |dizi, nesne veya dize |Boş olup olmadığını denetlemek için değer. |

### <a name="return-value"></a>Dönüş değeri

Değer boşsa, **true** döndürür; Aksi takdirde, **false**.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) bir dizi, nesne ve dizenin boş olup olmadığını denetler.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Bir dizenin bir değer ile bitip bitmeyeceğini belirler. Karşılaştırma büyük/küçük harfe duyarlıdır.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |string |Bulunacak öğeyi içeren değer. |
| stringToFind |Yes |string |Bulunacak değer. |

### <a name="return-value"></a>Dönüş değeri

Dizenin son karakteri veya karakterleri değerle eşleşiyorsa **doğru** ; Aksi takdirde, **false**.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) , StartsWith ve EndsWith işlevlerinin nasıl kullanılacağını gösterir:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="first"></a>adı

`first(arg1)`

Dizenin ilk karakterini veya dizideki ilk öğeyi döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |dizi veya dize |İlk öğe veya karakteri alma değeri. |

### <a name="return-value"></a>Dönüş değeri

Bir dizideki ilk öğenin türü (dize, int, dizi veya nesne).

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) , ilk işlevin dizi ve dize ile nasıl kullanılacağını göstermektedir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
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

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| arrayOutput | Dize | bir |
| stringOutput | Dize | O |

## <a name="format"></a>format

`format(formatString, arg1, arg2, ...)`

Giriş değerlerinden biçimli bir dize oluşturur.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| formatString | Yes | string | Bileşik biçim dizesi. |
| arg1 | Yes | dize, tamsayı veya Boole | Biçimlendirilen dizeye dahil edilecek değer. |
| ek bağımsız değişkenler | Hayır | dize, tamsayı veya Boole | Biçimlendirilen dizeye eklenecek ek değerler. |

### <a name="remarks"></a>Açıklamalar

Şablonunuzda bir dizeyi biçimlendirmek için bu işlevi kullanın. .NET 'teki [System. String. Format](/dotnet/api/system.string.format) yöntemiyle aynı biçimlendirme seçeneklerini kullanır.

### <a name="examples"></a>Örnekler

Aşağıdaki örnek şablon, biçim işlevinin nasıl kullanılacağını göstermektedir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| formatTest | Dize | Merhaba, Kullanıcı. Biçimli sayı: 8.175.133 |

## <a name="guid"></a>guid

`guid(baseString, ...)`

Parametre olarak belirtilen değerlere göre genel benzersiz tanımlayıcı biçiminde bir değer oluşturur.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| baseString |Yes |string |GUID oluşturmak için karma işlevde kullanılan değer. |
| gerektiğinde ek parametreler |Hayır |string |Benzersizlik düzeyini belirten değeri oluşturmak için gereken sayıda dize ekleyebilirsiniz. |

### <a name="remarks"></a>Açıklamalar

Bu işlev, genel benzersiz tanımlayıcı biçiminde bir değer oluşturmanız gerektiğinde faydalıdır. Sonuç için benzersizlik kapsamını sınırlayan parametre değerleri sağlarsınız. Adın aboneliğe, kaynak grubuna veya dağıtıma göre benzersiz olup olmadığını belirtebilirsiniz.

Döndürülen değer bir rastgele dize değil, parametreler üzerindeki bir karma işlevin sonucu. Döndürülen değer 36 karakter uzunluğundadır. Bu, genel olarak benzersiz değildir. Parametrelerin karma değerini temel alan yeni bir GUID oluşturmak için [NewGuid](#newguid) işlevini kullanın.

Aşağıdaki örneklerde, yaygın olarak kullanılan düzeyler için benzersiz bir değer oluşturmak üzere GUID 'in nasıl kullanılacağı gösterilmektedir.

Aboneliğin benzersiz kapsamı

```json
"[guid(subscription().subscriptionId)]"
```

Kaynak grubu için benzersiz kapsam

```json
"[guid(resourceGroup().id)]"
```

Kaynak grubu için dağıtımın benzersiz kapsamı

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>Dönüş değeri

Genel benzersiz tanımlayıcı biçiminde 36 karakter içeren bir dize.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) GUID 'den sonuçlar döndürüyor:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

## <a name="indexof"></a>indexOf

`indexOf(stringToSearch, stringToFind)`

Bir dize içindeki bir değerin ilk konumunu döndürür. Karşılaştırma büyük/küçük harfe duyarlıdır.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |string |Bulunacak öğeyi içeren değer. |
| stringToFind |Yes |string |Bulunacak değer. |

### <a name="return-value"></a>Dönüş değeri

Bulunacak öğenin konumunu temsil eden bir tamsayı. Değer sıfır tabanlıdır. Öğe bulunmazsa-1 döndürülür.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) , IndexOf ve LastIndexOf işlevlerinin nasıl kullanılacağını göstermektedir:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| Bulunamadı | Int | -1 |

## <a name="last"></a>soyadına

`last (arg1)`

Dizenin son karakterini veya dizideki son öğeyi döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |dizi veya dize |Son öğe veya karakteri alma değeri. |

### <a name="return-value"></a>Dönüş değeri

Bir dizideki son öğenin bir dizesi veya türü (dize, int, dizi veya nesne).

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) , son işlevin bir dizi ve dize ile nasıl kullanılacağını gösterir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
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

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| arrayOutput | Dize | üç |
| stringOutput | Dize | A |

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

Bir dize içindeki bir değerin son konumunu döndürür. Karşılaştırma büyük/küçük harfe duyarlıdır.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |string |Bulunacak öğeyi içeren değer. |
| stringToFind |Yes |string |Bulunacak değer. |

### <a name="return-value"></a>Dönüş değeri

Bulunacak öğenin son konumunu temsil eden bir tamsayı. Değer sıfır tabanlıdır. Öğe bulunmazsa-1 döndürülür.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) , IndexOf ve LastIndexOf işlevlerinin nasıl kullanılacağını göstermektedir:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | Int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| Bulunamadı | Int | -1 |

## <a name="length"></a>length

`length(string)`

Bir dizedeki karakter sayısını, dizideki öğeleri veya bir nesnedeki kök düzeyi özellikleri döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |dizi, dize veya nesne |Öğe sayısının alınması için kullanılacak dizi, karakter sayısını almak için kullanılacak dize veya kök düzeyi özelliklerinin sayısını almak için kullanılacak nesne. |

### <a name="return-value"></a>Dönüş değeri

Bir int. 

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) , uzunluğunun bir dizi ve dize ile nasıl kullanılacağını gösterir:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

## <a name="newguid"></a>newGuid

`newGuid()`

Genel benzersiz tanımlayıcı biçimindeki bir değer döndürür. **Bu işlev, yalnızca bir parametre için varsayılan değerde kullanılabilir.**

### <a name="remarks"></a>Açıklamalar

Bu işlevi yalnızca bir parametresinin varsayılan değeri için bir ifade içinde kullanabilirsiniz. Bu işlevi bir şablonda başka bir yerde kullanmak bir hata döndürür. İşlevin her çağrılışında farklı bir değer döndürdüğünden, bu işleve şablon diğer bölümlerinde izin verilmiyor. Aynı parametrelerle aynı şablon dağıtmak, aynı sonuçları güvenilir bir şekilde üretmez.

NewGuid işlevi herhangi bir parametre içermediğinden [GUID](#guid) işlevinden farklıdır. Aynı parametreye sahip GUID 'yi çağırdığınızda, her seferinde aynı tanımlayıcıyı döndürür. Belirli bir ortam için aynı GUID 'i güvenilir bir şekilde oluşturmanız gerektiğinde GUID kullanın. Kaynakları bir test ortamına dağıtmak gibi her seferinde farklı bir tanımlayıcıya ihtiyacınız olduğunda NEWGUID kullanın.

[Daha önceki başarılı bir dağıtımı yeniden dağıtmak için seçeneğini](rollback-on-error.md)kullanırsanız ve önceki dağıtım NEWGUID kullanan bir parametre içeriyorsa, parametre yeniden değerlendirilmez. Bunun yerine, önceki dağıtımdaki parametre değeri geri alma dağıtımında otomatik olarak yeniden kullanılır.

Bir sınama ortamında, yalnızca kısa bir süre için canlı kaynakları tekrar tekrar dağıtmanız gerekebilir. Benzersiz adlar oluşturmak yerine, benzersiz adlar oluşturmak için [Uniquestring](#uniquestring) Ile NEWGUID kullanabilirsiniz.

Varsayılan bir değer için newGuid işlevine dayanan bir şablonu yeniden dağıtma konusunda dikkatli olun. Yeniden dağıtırken ve parametresi için bir değer sağlamazsanız, işlev yeniden değerlendirilerek yapılır. Yeni bir kaynak oluşturmak yerine var olan bir kaynağı güncelleştirmek istiyorsanız, önceki dağıtımdan parametre değerini geçirin.

### <a name="return-value"></a>Dönüş değeri

Genel benzersiz tanımlayıcı biçiminde 36 karakter içeren bir dize.

### <a name="examples"></a>Örnekler

Aşağıdaki örnek şablonda, yeni tanımlayıcı içeren bir parametre gösterilmektedir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Yukarıdaki örnekteki çıktı her dağıtım için farklılık gösterir, ancak şuna benzer olacaktır:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| guidOutput | string | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

Aşağıdaki örnek, bir depolama hesabı için benzersiz bir ad oluşturmak üzere newGuid işlevini kullanır. Bu şablon, depolama hesabının kısa bir süre için bulunduğu ve yeniden dağıtılmadığı test ortamında çalışabilir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "sku":{
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

Yukarıdaki örnekteki çıktı her dağıtım için farklılık gösterir, ancak şuna benzer olacaktır:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| nameOutput | string | storagenziwvyru7uxie |


## <a name="padleft"></a>Asma sol

`padLeft(valueToPad, totalLength, paddingCharacter)`

Belirtilen toplam uzunluğa ulaşana kadar sola karakter ekleyerek sağa hizalanmış bir dize döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| valueToPad |Yes |dize veya tamsayı |Sağa hizalamak için değer. |
| Toplam uzunluğu |Yes |int |Döndürülen dizedeki toplam karakter sayısı. |
| Doldurma Ingcharacter |Hayır |tek karakter |Toplam uzunluğa ulaşılana kadar sol doldurma için kullanılacak karakter. Varsayılan değer bir alandır. |

Özgün dize, doldurma karakteri sayısından uzunsa, hiçbir karakter eklenmez.

### <a name="return-value"></a>Dönüş değeri

En az belirtilen karakter sayısına sahip bir dize.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) , toplam karakter sayısına ulaşıncaya kadar sıfır karakteri ekleyerek kullanıcı tarafından sağlanmış parametre değerinin nasıl gösterileceğini gösterir. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| stringOutput | Dize | 0000000123 |

## <a name="replace"></a>değiştirin

`replace(originalString, oldString, newString)`

Başka bir dize tarafından değiştirilmiş bir dizenin tüm örneklerine sahip yeni bir dize döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| originalString |Yes |string |Bir dizenin tüm örneklerine sahip olan değer başka bir dize tarafından değiştirildi. |
| Eskidize |Yes |string |Özgün dizeden kaldırılacak dize. |
| Yenidize |Yes |string |Kaldırılan dizenin yerine eklenecek dize. |

### <a name="return-value"></a>Dönüş değeri

Değiştirilmiş karakterlerle bir dize.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) , Kullanıcı tarafından belirtilen dizeden tüm tirelerin nasıl kaldırılacağını ve dizenin bir kısmının başka bir dizeyle nasıl değiştirileceğini gösterir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| firstOutput | Dize | 1231231234 |
| secondOutput | Dize | 123-123-xxxx |

## <a name="skip"></a>Şimdilik

`skip(originalValue, numberToSkip)`

Belirtilen sayıda karakterden sonraki tüm karakterleri içeren bir dize veya belirtilen sayıda öğeden sonra tüm öğeleri içeren bir dizi döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| originalValue |Yes |dizi veya dize |Atlama için kullanılacak dizi veya dize. |
| numberToSkip |Yes |int |Atlanacak öğe veya karakter sayısı. Bu değer 0 veya daha azsa, değer içindeki tüm öğeler veya karakterler döndürülür. Dizi veya dizenin uzunluğundan daha büyükse boş bir dizi veya dize döndürülür. |

### <a name="return-value"></a>Dönüş değeri

Bir dizi veya dize.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) dizideki belirtilen sayıda öğeyi ve bir dizedeki belirtilen karakter sayısını atlar.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| arrayOutput | Dizi | ["üç"] |
| stringOutput | Dize | 2 3 |

## <a name="split"></a>split

`split(inputString, delimiter)`

Belirtilen sınırlayıcılar tarafından ayrılan giriş dizesinin alt dizelerini içeren dizelerin dizisini döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| InputString |Yes |string |Bölünecek dize. |
| ayırıcı |Yes |dizelerin dizesi veya dizisi |Dizeyi bölmek için kullanılacak sınırlayıcı. |

### <a name="return-value"></a>Dönüş değeri

Dizeler dizisi.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) , giriş dizesini virgül ile ve virgül ya da noktalı virgülle ayırır.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| firstOutput | Dizi | ["bir", "iki", "üç"] |
| secondOutput | Dizi | ["bir", "iki", "üç"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Bir dizenin bir değerle başlatılıp başlatılmayacağını belirler. Karşılaştırma büyük/küçük harfe duyarlıdır.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| stringToSearch |Yes |string |Bulunacak öğeyi içeren değer. |
| stringToFind |Yes |string |Bulunacak değer. |

### <a name="return-value"></a>Dönüş değeri

Dizenin ilk karakteri veya karakterleri değerle eşleşiyorsa **doğru** ; Aksi takdirde, **false**.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) , StartsWith ve EndsWith işlevlerinin nasıl kullanılacağını gösterir:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="string"></a>string

`string(valueToConvert)`

Belirtilen değeri bir dizeye dönüştürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| valueToConvert |Yes | Any |Dizeye dönüştürülecek değer. Nesneler ve diziler dahil olmak üzere herhangi bir değer türü dönüştürülebilirler. |

### <a name="return-value"></a>Dönüş değeri

Dönüştürülmüş değerin dizesi.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablonda](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) farklı değer türlerinin dizelere nasıl dönüştürüleceği gösterilmektedir:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| objectOutput | Dize | {"valueA": 10, "valueB": "örnek metin"} |
| arrayOutput | Dize | ["a", "b", "c"] |
| ıntoutput | Dize | 5 |

## <a name="substring"></a>dizeden

`substring(stringToParse, startIndex, length)`

Belirtilen karakter konumunda başlayan ve belirtilen sayıda karakteri içeren bir alt dize döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| stringToParse |Yes |string |Alt dizenin ayıklandığı özgün dize. |
| startIndex |Hayır |int |Alt dize için sıfır tabanlı başlangıç karakter konumu. |
| length |Hayır |int |Alt dize için karakter sayısı. Dize içindeki bir konuma başvurmalıdır. Sıfır veya daha büyük olmalıdır. |

### <a name="return-value"></a>Dönüş değeri

Alt dize. Ya da uzunluk sıfır ise boş bir dize.

### <a name="remarks"></a>Açıklamalar

Alt dize dizenin sonunun ötesinde veya uzunluğu sıfırdan küçükse işlev başarısız olur. Aşağıdaki örnek hata ile başarısız olur "Dizin ve uzunluk parametreleri dize içinde bir konuma başvurmalıdır. Dizin parametresi: ' 0 ', length parametresi: ' 11 ', dize parametresinin uzunluğu: ' 10 '. ".

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) bir dizeden bir alt dize ayıklar.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| substringOutput | Dize | iki |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Dizenin başından itibaren belirtilen sayıda karaktere sahip bir dize veya dizinin başından itibaren belirtilen sayıda öğe içeren bir dizi döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| originalValue |Yes |dizi veya dize |Öğelerin ele aldığı dizi veya dize. |
| numberToTake |Yes |int |Gerçekleştirilecek öğe veya karakter sayısı. Bu değer 0 veya daha azsa, boş bir dizi veya dize döndürülür. Belirtilen dizi veya dizenin uzunluğundan daha büyükse, dizideki veya dizedeki tüm öğeler döndürülür. |

### <a name="return-value"></a>Dönüş değeri

Bir dizi veya dize.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) , diziden belirtilen sayıda öğeyi ve bir dizeden karakter alır.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| arrayOutput | Dizi | ["bir", "iki"] |
| stringOutput | Dize | açık |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

Belirtilen dizeyi küçük harfe dönüştürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| stringToChange |Yes |string |Küçük harfe dönüştürülecek değer. |

### <a name="return-value"></a>Dönüş değeri

Dize, küçük harfe dönüştürüldü.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) bir parametre değerini küçük harfe ve büyük harflere dönüştürür.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| Toharfe çıkışı | Dize | Bir iki üç |
| Toüsteçıkış | Dize | Bir iki üç |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

Belirtilen dizeyi büyük harfe dönüştürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| stringToChange |Yes |string |Büyük harfe dönüştürülecek değer. |

### <a name="return-value"></a>Dönüş değeri

Dize, büyük harfe dönüştürüldü.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) bir parametre değerini küçük harfe ve büyük harflere dönüştürür.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| Toharfe çıkışı | Dize | Bir iki üç |
| Toüsteçıkış | Dize | Bir iki üç |

## <a name="trim"></a>kırpma

`trim (stringToTrim)`

Belirtilen dizeden tüm öndeki ve sondaki boşluk karakterlerini kaldırır.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| stringToTrim |Yes |string |Kırpılacak değer. |

### <a name="return-value"></a>Dönüş değeri

Baştaki ve sondaki boşluk karakterleri olmayan dize.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) , parametresindeki boşluk karakterlerini kırpar.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| döndürülmesini | Dize | Bir iki üç |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

Parametre olarak belirtilen değerlere göre belirleyici bir karma dize oluşturur. 

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| baseString |Yes |string |Benzersiz bir dize oluşturmak için karma işlevde kullanılan değer. |
| gerektiğinde ek parametreler |Hayır |string |Benzersizlik düzeyini belirten değeri oluşturmak için gereken sayıda dize ekleyebilirsiniz. |

### <a name="remarks"></a>Açıklamalar

Bu işlev, bir kaynak için benzersiz bir ad oluşturmanız gerektiğinde faydalıdır. Sonuç için benzersizlik kapsamını sınırlayan parametre değerleri sağlarsınız. Adın aboneliğe, kaynak grubuna veya dağıtıma göre benzersiz olup olmadığını belirtebilirsiniz. 

Döndürülen değer bir rastgele dize değil, bunun yerine karma işlevin sonucu. Döndürülen değer 13 karakter uzunluğundadır. Bu, genel olarak benzersiz değildir. Anlamlı bir ad oluşturmak için değeri adlandırma kuralınızdan bir ön ek ile birleştirmek isteyebilirsiniz. Aşağıdaki örnek döndürülen değerin biçimini gösterir. Gerçek değer, belirtilen parametrelere göre değişir.

    tcvhiyu5h2o5o

Aşağıdaki örneklerde, yaygın olarak kullanılan düzeyler için benzersiz bir değer oluşturmak üzere uniqueString 'in nasıl kullanılacağı gösterilmektedir.

Aboneliğin benzersiz kapsamı

```json
"[uniqueString(subscription().subscriptionId)]"
```

Kaynak grubu için benzersiz kapsam

```json
"[uniqueString(resourceGroup().id)]"
```

Kaynak grubu için dağıtımın benzersiz kapsamı

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

Aşağıdaki örnek, kaynak grubunuza göre bir depolama hesabı için benzersiz bir ad oluşturmayı gösterir. Aynı şekilde oluşturulursa, kaynak grubunun içinde adı benzersiz değildir.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

Her bir şablonu dağıtışınızda yeni bir benzersiz ad oluşturmanız gerekiyorsa ve kaynağı güncelleştirmeyi düşünmüyorsanız, [UtcNow](#utcnow) Işlevini de uniquestring ile kullanabilirsiniz. Bu yaklaşımı bir test ortamında kullanabilirsiniz. Bir örnek için bkz. [UtcNow](#utcnow).

### <a name="return-value"></a>Dönüş değeri

13 karakter içeren bir dize.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) , uniquestring 'den sonuçlar getirir:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "uniqueRG": {
            "value": "[uniqueString(resourceGroup().id)]",
            "type" : "string"
        },
        "uniqueDeploy": {
            "value": "[uniqueString(resourceGroup().id, deployment().name)]",
            "type" : "string"
        }
    }
}
```

## <a name="uri"></a>kullanılmamışsa

`uri (baseUri, relativeUri)`

BaseUri ve relativeUri dizesini birleştirerek mutlak bir URI oluşturur.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| baseUri |Yes |string |Taban URI dizesi. Bu tabloda açıklandığı gibi sondaki eğik çizginin ('/') işlenmesiyle ilgili davranışı gözlemlemeye özen gösterin.  |
| relativeUri |Yes |string |Taban URI dizesine eklenecek göreli URI dizesi. |

* **BaseUri** bir sondaki eğik çizgiyle sonlanıyorsa, sonuç yalnızca **BaseUri** URI ve ardından **relativeUri**olur.

* **BaseUri** bir sondaki eğik çizgi ile bitmezse iki şeyin meydana gelir.  

   * **BaseUri** 'nin hiç eğik çizgi yoksa (önde gelen "//" dışında), sonuç yalnızca **BaseUri** 'nin ardından **relativeUri**olur.

   * **BaseUri** 'de bazı eğik çizgiler varsa, ancak eğik çizgiyle bitmezse, son eğik çizgiden elde edilen her şey **BaseUri** 'Den kaldırılır ve sonuç **BaseUri** 'nin ardından **relativeUri**olur.
     
Bazı örnekler şunlardır:

```
uri('http://contoso.org/firstpath', 'myscript.sh') -> http://contoso.org/myscript.sh
uri('http://contoso.org/firstpath/', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json', 'myscript.sh') -> http://contoso.org/firstpath/myscript.sh
uri('http://contoso.org/firstpath/azuredeploy.json/', 'myscript.sh') -> http://contoso.org/firstpath/azuredeploy.json/myscript.sh
```
Tüm ayrıntılar için, **BaseUri** ve **relativeUri** parametreleri, [RFC 3986, Bölüm 5](https://tools.ietf.org/html/rfc3986#section-5)' te belirtilen şekilde çözümlenir.

### <a name="return-value"></a>Dönüş değeri

Taban ve göreli değerler için mutlak URI 'yi temsil eden bir dize.

### <a name="examples"></a>Örnekler

Aşağıdaki örnek, üst şablonun değerine göre iç içe bir şablonun bağlantısının nasıl oluşturulacağını gösterir.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) URI, URIComponent ve Urıonenttostring 'in nasıl kullanılacağını göstermektedir:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| uriOutput | Dize | `http://contoso.com/resources/nested/azuredeploy.json` |
| Bileşen Entoutput | Dize | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Dize | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponent"></a>URIComponent

`uricomponent(stringToEncode)`

Bir URI 'yi kodlar.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| stringToEncode |Yes |string |Kodlanacak değer. |

### <a name="return-value"></a>Dönüş değeri

URI kodlamalı değerin dizesi.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) URI, URIComponent ve Urıonenttostring 'in nasıl kullanılacağını göstermektedir:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| uriOutput | Dize | `http://contoso.com/resources/nested/azuredeploy.json` |
| Bileşen Entoutput | Dize | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Dize | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="uricomponenttostring"></a>Urıonenttostring

`uriComponentToString(uriEncodedString)`

URI kodlamalı bir değer String döndürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Yes |string |Dizeye dönüştürülecek URI kodlamalı değer. |

### <a name="return-value"></a>Dönüş değeri

Kodu çözülen URI kodlamalı değerin dizesi.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) URI, URIComponent ve Urıonenttostring 'in nasıl kullanılacağını göstermektedir:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| uriOutput | Dize | `http://contoso.com/resources/nested/azuredeploy.json` |
| Bileşen Entoutput | Dize | `http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json` |
| toStringOutput | Dize | `http://contoso.com/resources/nested/azuredeploy.json` |

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Belirtilen biçimdeki geçerli (UTC) Tarih/saat değerini döndürür. Biçim sağlanmazsa ISO 8601 (yyyyMMddTHHmmssZ) biçimi kullanılır. **Bu işlev, yalnızca bir parametre için varsayılan değerde kullanılabilir.**

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| format |Hayır |string |Dizeye dönüştürülecek URI kodlamalı değer. [Standart biçim dizelerini](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) veya [özel biçim dizelerini](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)kullanın. |

### <a name="remarks"></a>Açıklamalar

Bu işlevi yalnızca bir parametresinin varsayılan değeri için bir ifade içinde kullanabilirsiniz. Bu işlevi bir şablonda başka bir yerde kullanmak bir hata döndürür. İşlevin her çağrılışında farklı bir değer döndürdüğünden, bu işleve şablon diğer bölümlerinde izin verilmiyor. Aynı parametrelerle aynı şablon dağıtmak, aynı sonuçları güvenilir bir şekilde üretmez.

[Daha önceki başarılı bir dağıtımı yeniden dağıtmak için seçeneğini](rollback-on-error.md)kullanırsanız ve önceki dağıtım artık utckullanan bir parametre içeriyorsa, parametre yeniden değerlendirilmez. Bunun yerine, önceki dağıtımdaki parametre değeri geri alma dağıtımında otomatik olarak yeniden kullanılır.

Varsayılan bir değer için utcNow işlevine dayanan bir şablonu yeniden dağıtmaya dikkat edin. Yeniden dağıtırken ve parametresi için bir değer sağlamazsanız, işlev yeniden değerlendirilerek yapılır. Yeni bir kaynak oluşturmak yerine var olan bir kaynağı güncelleştirmek istiyorsanız, önceki dağıtımdan parametre değerini geçirin.

### <a name="return-value"></a>Dönüş değeri

Geçerli UTC Tarih saat değeri.

### <a name="examples"></a>Örnekler

Aşağıdaki örnek şablonda tarih saat değeri için farklı biçimler gösterilmektedir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcValue": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        },
        "utcShortValue": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "utcCustomValue": {
            "type": "string",
            "defaultValue": "[utcNow('M d')]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "utcOutput": {
            "type": "string",
            "value": "[parameters('utcValue')]"
        },
        "utcShortOutput": {
            "type": "string",
            "value": "[parameters('utcShortValue')]"
        },
        "utcCustomOutput": {
            "type": "string",
            "value": "[parameters('utcCustomValue')]"
        }
    }
}
```

Yukarıdaki örnekteki çıktı her dağıtım için farklılık gösterir, ancak şuna benzer olacaktır:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| utcOutput | string | 20190305T175318Z |
| Utckısaltoutput | string | 03/05/2019 |
| utcCustomOutput | string | 3 5 |

Sonraki örnekte, bir etiket değeri ayarlanırken işlevden bir değerin nasıl kullanılacağı gösterilmektedir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "rgName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "name": "[parameters('rgName')]",
            "location": "westeurope",
            "tags":{
                "createdDate": "[parameters('utcShort')]"
            },
            "properties":{}
        }
    ],
    "outputs": {
        "utcShort": {
            "type": "string",
            "value": "[parameters('utcShort')]"
        }
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar
* Bir Azure Resource Manager şablonu olarak bölümlerde açıklaması için bkz: [Azure Resource Manager şablonları yazma](resource-group-authoring-templates.md).
* Birden fazla şablon birleştirmek için bkz: [Azure Resource Manager ile bağlı şablonları kullanma](resource-group-linked-templates.md).
* Belirtilen sayıda yineleme için bir kaynak türünü oluştururken bkz [Azure Resource Manager'da kaynakları birden çok örneğini oluşturma](resource-group-create-multiple.md).
* Oluşturduğunuz bir şablonu dağıtmayı öğrenmek için bkz [Azure Resource Manager şablonu ile uygulama dağıtma](resource-group-template-deploy.md).

