---
title: Şablon işlevleri-karşılaştırma
description: Değerleri karşılaştırmak için bir Azure Resource Manager şablonunda (ARM şablonunda) kullanılacak işlevleri açıklar.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 95655a4c92a1de9bb7a7faebcdaa83fb0fa75696
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99834009"
---
# <a name="comparison-functions-for-arm-templates"></a>ARM şablonları için karşılaştırma işlevleri

Kaynak Yöneticisi, Azure Resource Manager şablonunuzda (ARM şablonunda) karşılaştırmalar yapmak için çeşitli işlevler sağlar:

* [Coalesce](#coalesce)
* [eşittir](#equals)
* [büyüktür](#greater)
* [greaterOrEquals](#greaterorequals)
* [büyüktür](#less)
* [lessOrEquals](#lessorequals)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="coalesce"></a>Coalesce

`coalesce(arg1, arg2, arg3, ...)`

Parametrelerden null olmayan ilk değeri döndürür. Boş dizeler, boş diziler ve boş nesneler null değil.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |int, String, array veya Object |Null için sınanacak ilk değer. |
| ek bağımsız değişkenler |No |int, String, array veya Object |Null için sınanacak ek değerler. |

### <a name="return-value"></a>Döndürülen değer

Bir String, int, array veya Object olabilen, null olmayan ilk parametrelerin değeri. Tüm parametreler null ise null.

### <a name="example"></a>Örnek

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/coalesce.json) , farklı birleşim kullanımlarından gelen çıktıyı gösterir.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "objectToTest": {
      "type": "object",
      "defaultValue": {
        "null1": null,
        "null2": null,
        "string": "default",
        "int": 1,
        "object": { "first": "default" },
        "array": [ 1 ]
      }
    }
  },
  "resources": [
  ],
  "outputs": {
    "stringOutput": {
      "type": "string",
      "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').string)]"
    },
    "intOutput": {
      "type": "int",
      "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').int)]"
    },
    "objectOutput": {
      "type": "object",
      "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').object)]"
    },
    "arrayOutput": {
      "type": "array",
      "value": "[coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2, parameters('objectToTest').array)]"
    },
    "emptyOutput": {
      "type": "bool",
      "value": "[empty(coalesce(parameters('objectToTest').null1, parameters('objectToTest').null2))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param objectToTest object = {
  'null1': null
  'null2': null
  'string': 'default'
  'int': 1
  'object': {
    'first': 'default'
  }
  'array': [
    1
  ]
}

output stringOutput string = objectToTest.null1 ?? objectToTest.null2 ?? objectToTest.string
output intOutput int = objectToTest.null1 ?? objectToTest.null2 ?? objectToTest.int
output objectOutput object = objectToTest.null1 ?? objectToTest.null2 ?? objectToTest.object
output arrayOutput array = objectToTest.null1 ?? objectToTest.null2 ?? objectToTest.array
output emptyOutput bool =empty(objectToTest.null1 ?? objectToTest.null2)
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| stringOutput | Dize | default |
| ıntoutput | int | 1 |
| objectOutput | Nesne | {"First": "varsayılan"} |
| arrayOutput | Dizi |  [1] |
| Emptızput | Bool | Doğru |

## <a name="equals"></a>equals

`equals(arg1, arg2)`

İki değerin birbirlerine eşit olup olmadığını denetler. `equals`Işlev Bıcep içinde desteklenmiyor. `==`Bunun yerine işlecini kullanın.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |int, String, array veya Object |Eşitlik için denetlenecek ilk değer. |
| arg2 |Yes |int, String, array veya Object |Eşitlik için denetlenecek ikinci değer. |

### <a name="return-value"></a>Döndürülen değer

Değerler eşitse **true** değerini döndürür; Aksi takdirde, **false**.

### <a name="remarks"></a>Açıklamalar

Eşittir işlevi genellikle `condition` bir kaynağın dağıtılıp dağıtılmadığını test etmek için öğesiyle birlikte kullanılır.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "condition": "[equals(parameters('newOrExisting'),'new')]",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[variables('storageAccountName')]",
  "apiVersion": "2017-06-01",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "[variables('storageAccountType')]"
  },
  "kind": "Storage",
  "properties": {}
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> `Conditions` henüz Bıcep 'de uygulanmadı. [Koşullara](https://github.com/Azure/bicep/issues/186)bakın.

---

### <a name="example"></a>Örnek

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/equals.json) , eşitlik için farklı değer türlerini denetler. Tüm varsayılan değerler doğru döndürür.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 1
    },
    "firstString": {
      "type": "string",
      "defaultValue": "a"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    },
    "firstArray": {
      "type": "array",
      "defaultValue": [ "a", "b" ]
    },
    "secondArray": {
      "type": "array",
      "defaultValue": [ "a", "b" ]
    },
    "firstObject": {
      "type": "object",
      "defaultValue": { "a": "b" }
    },
    "secondObject": {
      "type": "object",
      "defaultValue": { "a": "b" }
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[equals(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[equals(parameters('firstString'), parameters('secondString'))]"
    },
    "checkArrays": {
      "type": "bool",
      "value": "[equals(parameters('firstArray'), parameters('secondArray'))]"
    },
    "checkObjects": {
      "type": "bool",
      "value": "[equals(parameters('firstObject'), parameters('secondObject'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 1
param firstString string = 'a'
param secondString string = 'a'
param firstArray array = [
  'a'
  'b'
]
param secondArray array = [
  'a'
  'b'
]
param firstObject object = {
  'a': 'b'
}
param secondObject object = {
  'a': 'b'
}

output checInts bool = firstInt == secondInt
output checkStrings bool = firstString == secondString
output checkArrays bool = firstArray == secondArray
output checkObjects bool = firstObject == secondObject
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| Iadeler | Bool | Doğru |
| checkStrings | Bool | Doğru |
| Checkarışın | Bool | Doğru |
| checkObjects | Bool | Doğru |

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) , **eşittir** ile [değil](template-functions-logical.md#not) .

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
  ],
  "outputs": {
    "checkNotEquals": {
      "type": "bool",
      "value": "[not(equals(1, 2))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output checkNotEquals bool = ! (1 == 2)
```

---

Yukarıdaki örnekteki çıktı:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| Checttequals | Bool | Doğru |

## <a name="greater"></a>büyüktür

`greater(arg1, arg2)`

İlk değerin ikinci değerden büyük olup olmadığını denetler. `greater`Işlev Bıcep içinde desteklenmiyor. `>`Bunun yerine işlecini kullanın.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |int veya String |Daha büyük karşılaştırma için ilk değer. |
| arg2 |Yes |int veya String |Daha büyük karşılaştırma için ikinci değer. |

### <a name="return-value"></a>Döndürülen değer

İlk değer ikinci değerden büyükse **true** değerini döndürür; Aksi takdirde, **false**.

### <a name="example"></a>Örnek

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greater.json) , bir değerin diğerine göre daha büyük olup olmadığını denetler.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 2
    },
    "firstString": {
      "type": "string",
      "defaultValue": "A"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[greater(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[greater(parameters('firstString'), parameters('secondString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 2
param firstString string = 'A'
param secondString string = 'a'

output checkInts bool = firstInt > secondInt
output checkStrings bool = firstString > secondString
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| Iadeler | Bool | Yanlış |
| checkStrings | Bool | Doğru |

## <a name="greaterorequals"></a>greaterOrEquals

`greaterOrEquals(arg1, arg2)`

İlk değerin ikinci değere eşit veya ondan büyük olup olmadığını denetler. `greaterOrEquals`Işlev Bıcep içinde desteklenmiyor. `>=`Bunun yerine işlecini kullanın.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |int veya String |Daha büyük veya eşit karşılaştırma için ilk değer. |
| arg2 |Yes |int veya String |Daha büyük veya eşit karşılaştırma için ikinci değer. |

### <a name="return-value"></a>Döndürülen değer

İlk değer ikinci değerden büyük veya bu değere eşitse **true** değerini döndürür; Aksi takdirde, **false**.

### <a name="example"></a>Örnek

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/greaterorequals.json) , bir değerin diğerine eşit veya ondan büyük olup olmadığını denetler.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 2
    },
    "firstString": {
      "type": "string",
      "defaultValue": "A"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[greaterOrEquals(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[greaterOrEquals(parameters('firstString'), parameters('secondString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 2
param firstString string = 'A'
param secondString string = 'a'

output checkInts bool = firstInt >= secondInt
output checkStrings bool = firstString >= secondString
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| Iadeler | Bool | Yanlış |
| checkStrings | Bool | Doğru |

## <a name="less"></a>daha az

`less(arg1, arg2)`

İlk değerin ikinci değerden küçük olup olmadığını denetler. `less`Işlev Bıcep içinde desteklenmiyor. `<`Bunun yerine işlecini kullanın.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |int veya String |Daha az karşılaştırma için ilk değer. |
| arg2 |Yes |int veya String |Daha az karşılaştırma için ikinci değer. |

### <a name="return-value"></a>Döndürülen değer

İlk değer ikinci değerden küçükse **true** değerini döndürür; Aksi takdirde, **false**.

### <a name="example"></a>Örnek

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/less.json) , bir değerin diğerine göre daha az olup olmadığını denetler.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 2
    },
    "firstString": {
      "type": "string",
      "defaultValue": "A"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[less(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[less(parameters('firstString'), parameters('secondString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 2
param firstString string = 'A'
param secondString string = 'a'

output checkInts bool = firstInt < secondInt
output checkStrings bool = firstString < secondString
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| Iadeler | Bool | Doğru |
| checkStrings | Bool | Yanlış |

## <a name="lessorequals"></a>lessOrEquals

`lessOrEquals(arg1, arg2)`

İlk değerin ikinci değere eşit veya ondan küçük olup olmadığını denetler. `lessOrEquals`Işlev Bıcep içinde desteklenmiyor. `<=`Bunun yerine işlecini kullanın.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |int veya String |Less veya eşittir karşılaştırması için ilk değer. |
| arg2 |Yes |int veya String |Less veya eşittir karşılaştırması için ikinci değer. |

### <a name="return-value"></a>Döndürülen değer

İlk değer ikinci değerden küçük veya bu değere eşitse **true** değerini döndürür; Aksi takdirde, **false**.

### <a name="example"></a>Örnek

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/lessorequals.json) , bir değerin diğerine eşit veya ondan küçük olup olmadığını denetler.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "firstInt": {
      "type": "int",
      "defaultValue": 1
    },
    "secondInt": {
      "type": "int",
      "defaultValue": 2
    },
    "firstString": {
      "type": "string",
      "defaultValue": "A"
    },
    "secondString": {
      "type": "string",
      "defaultValue": "a"
    }
  },
  "resources": [
  ],
  "outputs": {
    "checkInts": {
      "type": "bool",
      "value": "[lessOrEquals(parameters('firstInt'), parameters('secondInt') )]"
    },
    "checkStrings": {
      "type": "bool",
      "value": "[lessOrEquals(parameters('firstString'), parameters('secondString'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param firstInt int = 1
param secondInt int = 2
param firstString string = 'A'
param secondString string = 'a'

output checkInts bool = firstInt <= secondInt
output checkStrings bool = firstString <= secondString
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| Iadeler | Bool | Doğru |
| checkStrings | Bool | Yanlış |

## <a name="next-steps"></a>Sonraki adımlar

* ARM şablonundaki bölümlerin açıklaması için bkz. [ARM şablonlarının yapısını ve sözdizimini anlayın](template-syntax.md).
