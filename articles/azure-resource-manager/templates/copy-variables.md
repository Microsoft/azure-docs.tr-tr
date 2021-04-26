---
title: Bir değişkenin birden çok örneğini tanımlama
description: Bir değişken oluştururken birden çok kez yinelemek için bir Azure Resource Manager şablonunda kopyalama işlemi kullanın (ARM şablonu).
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: e1f6dfeb7b701b09ad1a9505d5dbcfddf2cd6b0b
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385726"
---
# <a name="variable-iteration-in-arm-templates"></a>ARM şablonlarında değişken yineleme

Bu makalede, Azure Resource Manager şablonunuzda (ARM şablonu) bir değişken için birden fazla değer oluşturma gösterilmektedir. `copy`Şablonunuzun değişkenler bölümüne öğesini ekleyerek, dağıtım sırasında bir değişken için öğe sayısını dinamik olarak ayarlayabilirsiniz. Ayrıca, şablon söz dizimini yinelemek zorunda kalmaktan kaçının.

Ayrıca, [kaynak, bir kaynaktaki Özellikler](copy-properties.md)ve [çıktılar](copy-outputs.md) [ile kopyalama](copy-resources.md)özelliğini de kullanabilirsiniz.

## <a name="syntax"></a>Syntax

Copy öğesi aşağıdaki genel biçime sahiptir:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

`name`Özelliği, döngüsünü tanımlayan herhangi bir değerdir. `count`Özelliği, değişken için istediğiniz yineleme sayısını belirtir.

`input`Özelliği yinelemek istediğiniz özellikleri belirtir. Özelliğindeki değerden oluşturulan bir dizi öğe oluşturursunuz `input` . Tek bir Özellik (bir dize gibi) veya birkaç özelliği olan bir nesnesi olabilir.

## <a name="copy-limits"></a>Sınırları Kopyala

Sayım 800 ' i aşamaz.

Sayı negatif bir sayı olamaz. Yeni bir Azure CLı, PowerShell veya REST API sürümü ile şablonu dağıtırsanız sıfır olabilir. Özellikle, şunu kullanmanız gerekir:

- Azure PowerShell **2,6** veya üzeri
- Azure CLı **2.0.74** veya üzeri
- REST API sürüm **2019-05-10** veya üzeri
- [Bağlı dağıtımlar](linked-templates.md) , dağıtım kaynak türü için apı sürüm **2019-05-10** veya üstünü kullanmalıdır

PowerShell, CLı ve REST API 'nin önceki sürümleri Count için sıfırı desteklemez.

## <a name="variable-iteration"></a>Değişken yineleme

Aşağıdaki örnek dize değerleri dizisinin nasıl oluşturulacağını gösterir:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "itemCount": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "variables": {
    "copy": [
      {
        "name": "stringArray",
        "count": "[parameters('itemCount')]",
        "input": "[concat('item', copyIndex('stringArray', 1))]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "arrayResult": {
      "type": "array",
      "value": "[variables('stringArray')]"
    }
  }
}
```

Önceki şablon, aşağıdaki değerlere sahip bir dizi döndürür:

```json
[
  "item1",
  "item2",
  "item3",
  "item4",
  "item5"
]
```

Sonraki örnekte,, ve olmak üzere üç özellik içeren bir nesne dizisinin nasıl oluşturulacağı `name` gösterilmektedir `diskSizeGB` `diskIndex` .

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "itemCount": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "variables": {
    "copy": [
      {
        "name": "objectArray",
        "count": "[parameters('itemCount')]",
        "input": {
          "name": "[concat('myDataDisk', copyIndex('objectArray', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('objectArray')]"
        }
      }
    ]
  },
  "resources": [],
  "outputs": {
    "arrayResult": {
      "type": "array",
      "value": "[variables('objectArray')]"
    }
  }
}
```

Yukarıdaki örnek aşağıdaki değerlere sahip bir dizi döndürür:

```json
[
  {
    "name": "myDataDisk1",
    "diskSizeGB": "1",
    "diskIndex": 0
  },
  {
    "name": "myDataDisk2",
    "diskSizeGB": "1",
    "diskIndex": 1
  },
  {
    "name": "myDataDisk3",
    "diskSizeGB": "1",
    "diskIndex": 2
  },
  {
    "name": "myDataDisk4",
    "diskSizeGB": "1",
    "diskIndex": 3
  },
  {
    "name": "myDataDisk5",
    "diskSizeGB": "1",
    "diskIndex": 4
  }
]
```

> [!NOTE]
> Değişken yineleme, bir fark bağımsız değişkenini destekler. Fark, yineleme adından sonra gelmelidir, örneğin `copyIndex('diskNames', 1)` . Bir fark değeri sağlamazsanız, ilk örnek için varsayılan değer 0 ' dır.
>

Ayrıca, `copy` öğesini bir değişken içinde de kullanabilirsiniz. Aşağıdaki örnek, değerlerinden biri olarak bir dizi içeren bir nesnesi oluşturur.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "itemCount": {
      "type": "int",
      "defaultValue": 5
    }
  },
  "variables": {
    "topLevelObject": {
      "sampleProperty": "sampleValue",
      "copy": [
        {
          "name": "disks",
          "count": "[parameters('itemCount')]",
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        }
      ]
    }
  },
  "resources": [],
  "outputs": {
    "objectResult": {
      "type": "object",
      "value": "[variables('topLevelObject')]"
    }
  }
}
```

Yukarıdaki örnek, aşağıdaki değerlere sahip bir nesne döndürür:

```json
{
  "sampleProperty": "sampleValue",
  "disks": [
    {
      "name": "myDataDisk1",
      "diskSizeGB": "1",
      "diskIndex": 0
    },
    {
      "name": "myDataDisk2",
      "diskSizeGB": "1",
      "diskIndex": 1
    },
    {
      "name": "myDataDisk3",
      "diskSizeGB": "1",
      "diskIndex": 2
    },
    {
      "name": "myDataDisk4",
      "diskSizeGB": "1",
      "diskIndex": 3
    },
    {
      "name": "myDataDisk5",
      "diskSizeGB": "1",
      "diskIndex": 4
    }
  ]
}
```

Sonraki örnekte, değişkenleriyle kullanabileceğiniz farklı yollar gösterilmektedir `copy` .

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

## <a name="example-templates"></a>Örnek Şablonlar

Aşağıdaki örneklerde, bir değişken için birden fazla değer oluşturmak için yaygın senaryolar gösterilmektedir.

|Şablon  |Açıklama  |
|---------|---------|
|[Değişkenleri Kopyala](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Değişkenlerde yinelemenin farklı yollarını gösterir. |
|[Çoklu güvenlik kuralları](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Bir ağ güvenlik grubuna birkaç güvenlik kuralı dağıtır. Bir parametreden güvenlik kuralları oluşturur. Parametresi için bkz. [birden çok NSG parametre dosyası](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Sonraki adımlar

- Öğreticiye gitmek için bkz. [öğretici: ARM şablonlarıyla birden çok kaynak örneği oluşturma](template-tutorial-create-multiple-instances.md).
- Copy öğesinin diğer kullanımları için bkz.:
  - [ARM şablonlarındaki kaynak yinelemesi](copy-resources.md)
  - [ARM şablonlarındaki Özellik yinelemesi](copy-properties.md)
  - [ARM şablonlarındaki çıkış yinelemesi](copy-outputs.md)
- Bir şablonun bölümleri hakkında bilgi edinmek istiyorsanız, bkz. [ARM şablonlarının yapısını ve sözdizimini anlayın](template-syntax.md).
- Şablonunuzu dağıtmayı öğrenmek için bkz. [ARM şablonlarıyla kaynak dağıtma ve Azure PowerShell](deploy-powershell.md).
