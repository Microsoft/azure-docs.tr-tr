---
title: Şablon işlevleri-mantıksal
description: Mantıksal değerleri belirleyebilmek için bir Azure Resource Manager şablonunda (ARM şablonu) kullanılacak işlevleri açıklar.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 27d94f10374daf0b9a351469579a5eb659cf5445
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96920487"
---
# <a name="logical-functions-for-arm-templates"></a>ARM şablonları için mantıksal işlevler

Kaynak Yöneticisi, Azure Resource Manager şablonunuzda (ARM şablonunda) karşılaştırmalar yapmak için çeşitli işlevler sağlar:

* [and](#and)
* [bool](#bool)
* [yanlýþ](#false)
* [if](#if)
* [başlatılmadı](#not)
* [veya](#or)
* [değeri](#true)

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="and"></a>ve

`and(arg1, arg2, ...)`

Tüm parametre değerlerinin doğru olup olmadığını denetler. `and`Işlev Bıcep içinde desteklenmiyor. `&&`Bunun yerine işlecini kullanın.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |boolean |Doğru olup olmadığını kontrol etmek için ilk değer. |
| arg2 |Yes |boolean |Doğru olup olmadığını kontrol etmek için ikinci değer. |
| ek bağımsız değişkenler |No |boolean |Doğru olup olmadığını denetlemek için ek bağımsız değişkenler. |

### <a name="return-value"></a>Döndürülen değer

Tüm değerler true ise **true** döndürür; Aksi takdirde, **false**.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) , mantıksal işlevlerin nasıl kullanılacağını göstermektedir.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "type": "bool",
      "value": "[and(bool('true'), bool('false'))]"
    },
    "orExampleOutput": {
      "type": "bool",
      "value": "[or(bool('true'), bool('false'))]"
    },
    "notExampleOutput": {
      "type": "bool",
      "value": "[not(bool('true'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

Yukarıdaki örnekteki çıktı:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| Andexamptaoutput | Bool | Yanlış |
| Orexamptaoutput | Bool | Doğru |
| Notexamptaoutput | Bool | Yanlış |

## <a name="bool"></a>bool

`bool(arg1)`

Parametreyi Boole değerine dönüştürür.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |dize veya tamsayı |Boole değerine dönüştürülecek değer. |

### <a name="return-value"></a>Döndürülen değer

Dönüştürülmüş değerin Boole değeri.

### <a name="remarks"></a>Açıklamalar

Boolean değerleri almak için [true ()](#true) ve [false ()](#false) de kullanabilirsiniz.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/bool.json) , bool 'un dize veya tamsayı ile nasıl kullanılacağını göstermektedir.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "trueString": {
      "type": "bool",
      "value": "[bool('true')]",
    },
    "falseString": {
      "type": "bool",
      "value": "[bool('false')]"
    },
    "trueInt": {
      "type": "bool",
      "value": "[bool(1)]"
    },
    "falseInt": {
      "type": "bool",
      "value": "[bool(0)]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output trueString bool = bool('true')
output falseString bool = bool('false')
output trueInt bool = bool(1)
output falseInt bool = bool(0)
```

---
Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| trueString | Bool | Doğru |
| Yanlışdize | Bool | Yanlış |
| Trueınt | Bool | Doğru |
| Yanlışint | Bool | Yanlış |

## <a name="false"></a>yanlış

`false()`

Yanlış değerini döndürür. `false`Işlev Bıcep 'de kullanılamaz.  `false`Bunun yerine anahtar sözcüğünü kullanın.

### <a name="parameters"></a>Parametreler

False işlevi herhangi bir parametreyi kabul etmez.

### <a name="return-value"></a>Döndürülen değer

Her zaman false olan bir Boole değeri.

### <a name="example"></a>Örnek

Aşağıdaki örnek, yanlış bir çıkış değeri döndürür.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "falseOutput": {
      "type": "bool",
      "value": "[false()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output falseOutput bool = false
```

---

Yukarıdaki örnekteki çıktı:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| Yanlışçıktı | Bool | Yanlış |

## <a name="if"></a>if

`if(condition, trueValue, falseValue)`

Bir koşulun doğru veya yanlış olduğunu temel alarak bir değer döndürür. `if`Işlev Bıcep içinde desteklenmiyor. `?:`Bunun yerine işlecini kullanın.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| koşul |Yes |boolean |Doğru veya yanlış olduğunu denetlemek için değer. |
| trueValue |Yes | dize, int, nesne veya dizi |Koşul doğru olduğunda döndürülecek değer. |
| Yanlışdeğer |Yes | dize, int, nesne veya dizi |Koşul false olduğunda döndürülecek değer. |

### <a name="return-value"></a>Döndürülen değer

İlk parametre **true** olduğunda ikinci parametreyi döndürür; Aksi takdirde, üçüncü parametreyi döndürür.

### <a name="remarks"></a>Açıklamalar

Koşul **doğru** olduğunda, yalnızca true değeri değerlendirilir. Koşul **false** olduğunda, yalnızca false değeri değerlendirilir. `if`İşleviyle, yalnızca koşullu olarak geçerli olan ifadeleri ekleyebilirsiniz. Örneğin, bir koşul altında bulunan ancak diğer koşulun altında olmayan bir kaynağa başvurabilirsiniz. Aşağıdaki bölümde, ifadeleri koşullu olarak değerlendirmek için bir örnek gösterilmiştir.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/if.json) , işlevinin nasıl kullanılacağını göstermektedir `if` .

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
  ],
  "outputs": {
    "yesOutput": {
      "type": "string",
      "value": "[if(equals('a', 'a'), 'yes', 'no')]"
    },
    "noOutput": {
      "type": "string",
      "value": "[if(equals('a', 'b'), 'yes', 'no')]"
    },
    "objectOutput": {
      "type": "object",
      "value": "[if(equals('a', 'a'), json('{\"test\": \"value1\"}'), json('null'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output yesOutput string = 'a' == 'a' ? 'yes' : 'no'
output noOutput string = 'a' == 'b' ? 'yes' : 'no'
output objectOutput object = 'a' == 'a' ? json('{"test": "value1"}') : json('null')
```

---

Yukarıdaki örnekteki çıktı:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| yesOutput | Dize | evet |
| noOutput | Dize | hayır |
| objectOutput | Nesne | {"test": "değer1"} |

Aşağıdaki [örnek şablonda](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/conditionWithReference.json) , bu işlevin yalnızca koşullu olarak geçerli olan ifadelerle nasıl kullanılacağı gösterilmektedir.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "logAnalytics": {
      "type": "string",
      "defaultValue": ""
    }
  },
  "resources": [
    {
      "condition": "[not(empty(parameters('logAnalytics')))]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "apiVersion": "2017-03-30",
      "name": "[concat(parameters('vmName'),'/omsOnboarding')]",
      "location": "[parameters('location')]",
      "properties": {
        "publisher": "Microsoft.EnterpriseCloud.Monitoring",
        "type": "MicrosoftMonitoringAgent",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "workspaceId": "[if(not(empty(parameters('logAnalytics'))), reference(parameters('logAnalytics'), '2015-11-01-preview').customerId, json('null'))]"
        },
        "protectedSettings": {
          "workspaceKey": "[if(not(empty(parameters('logAnalytics'))), listKeys(parameters('logAnalytics'), '2015-11-01-preview').primarySharedKey, json('null'))]"
        }
      }
    }
  ],
  "outputs": {
    "mgmtStatus": {
      "type": "string",
      "value": "[if(not(empty(parameters('logAnalytics'))), 'Enabled monitoring for VM!', 'Nothing to enable')]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

> [!NOTE]
> `Conditions` henüz Bıcep 'de uygulanmadı. [Koşullara](https://github.com/Azure/bicep/issues/186)bakın.

---

## <a name="not"></a>not

`not(arg1)`

Boole değerini ters değerine dönüştürür. `not`Işlev Bıcep içinde desteklenmiyor. `!`Bunun yerine işlecini kullanın.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |boolean |Dönüştürülecek değer. |

### <a name="return-value"></a>Döndürülen değer

Parametre **false** olduğunda **true** döndürür. Parametre **true** olduğunda **false** döndürür.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) , mantıksal işlevlerin nasıl kullanılacağını göstermektedir.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "type": "bool",
      "value": "[and(bool('true'), bool('false'))]",
    },
    "orExampleOutput": {
      "type": "bool",
      "value": "[or(bool('true'), bool('false'))]"
    },
    "notExampleOutput": {
      "type": "bool",
      "value": "[not(bool('true'))]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

Yukarıdaki örnekteki çıktı:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| Andexamptaoutput | Bool | Yanlış |
| Orexamptaoutput | Bool | Doğru |
| Notexamptaoutput | Bool | Yanlış |

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/not-equals.json) , [eşittir](template-functions-comparison.md#equals)ile **değil** .

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
output checkNotEquals bool = !(1 == 2)
```

---

Yukarıdaki örnekteki çıktı:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| Checttequals | Bool | Doğru |

## <a name="or"></a>veya

`or(arg1, arg2, ...)`

Herhangi bir parametre değerinin doğru olup olmadığını denetler. `or`Işlev Bıcep içinde desteklenmiyor. `||`Bunun yerine işlecini kullanın.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| arg1 |Yes |boolean |Doğru olup olmadığını kontrol etmek için ilk değer. |
| arg2 |Yes |boolean |Doğru olup olmadığını kontrol etmek için ikinci değer. |
| ek bağımsız değişkenler |No |boolean |Doğru olup olmadığını denetlemek için ek bağımsız değişkenler. |

### <a name="return-value"></a>Döndürülen değer

Herhangi bir değer true olduğunda **true** değerini döndürür; Aksi takdirde, **false**.

### <a name="examples"></a>Örnekler

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/andornot.json) , mantıksal işlevlerin nasıl kullanılacağını göstermektedir.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "andExampleOutput": {
      "value": "[and(bool('true'), bool('false'))]",
      "type": "bool"
    },
    "orExampleOutput": {
      "value": "[or(bool('true'), bool('false'))]",
      "type": "bool"
    },
    "notExampleOutput": {
      "value": "[not(bool('true'))]",
      "type": "bool"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output andExampleOutput bool = bool('true') && bool('false')
output orExampleOutput bool = bool('true') || bool('false')
output notExampleOutput bool = !(bool('true'))
```

---

Yukarıdaki örnekteki çıktı:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| Andexamptaoutput | Bool | Yanlış |
| Orexamptaoutput | Bool | Doğru |
| Notexamptaoutput | Bool | Yanlış |

## <a name="true"></a>true

`true()`

True değerini döndürür. `true`Işlev Bıcep 'de kullanılamaz.  `true`Bunun yerine anahtar sözcüğünü kullanın.

### <a name="parameters"></a>Parametreler

True işlevi herhangi bir parametreyi kabul etmez. `true`Işlev Bıcep 'de kullanılamaz.  `true`Bunun yerine anahtar sözcüğünü kullanın.

### <a name="return-value"></a>Döndürülen değer

Her zaman true olan bir Boole değeri.

### <a name="example"></a>Örnek

Aşağıdaki örnek, doğru bir çıkış değeri döndürür.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "trueOutput": {
      "type": "bool",
      "value": "[true()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output trueOutput bool = true
```

---

Yukarıdaki örnekteki çıktı:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| trueOutput | Bool | Doğru |

## <a name="next-steps"></a>Sonraki adımlar

* ARM şablonundaki bölümlerin açıklaması için bkz. [ARM şablonlarının yapısını ve sözdizimini anlayın](template-syntax.md).
