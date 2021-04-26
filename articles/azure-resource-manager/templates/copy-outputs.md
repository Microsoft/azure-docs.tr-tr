---
title: Bir çıkış değerinin birden çok örneğini tanımlama
description: Bir dağıtımdan değer döndürürken birden çok kez yinelemek için bir Azure Resource Manager şablonunda kopyalama işlemi kullanın (ARM şablonu).
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 49050f4c0a494bbfb470b64704a09d8738a727f7
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385743"
---
# <a name="output-iteration-in-arm-templates"></a>ARM şablonlarındaki çıkış yinelemesi

Bu makalede, Azure Resource Manager şablonunuzda (ARM şablonu) bir çıktı için birden fazla değer oluşturma gösterilmektedir. Şablonunuzun çıktılar bölümüne kopyalama döngüsü ekleyerek, dağıtım sırasında bir dizi öğeyi dinamik olarak döndürebilirsiniz.

Ayrıca, [kaynak, bir kaynaktaki Özellikler](copy-properties.md)ve [değişkenler](copy-variables.md) [ile kopyalama](copy-resources.md)döngüsünü de kullanabilirsiniz.

## <a name="syntax"></a>Syntax

# <a name="json"></a>[JSON](#tab/json)

Öğe `copy` sayısı döndürmek için şablonunuzun çıkış bölümüne öğesini ekleyin. Copy öğesi aşağıdaki genel biçime sahiptir:

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

`count`Özelliği, çıkış değeri için istediğiniz yineleme sayısını belirtir.

`input`Özelliği yinelemek istediğiniz özellikleri belirtir. Özelliğindeki değerden oluşturulan bir dizi öğe oluşturursunuz `input` . Tek bir Özellik (bir dize gibi) veya birkaç özelliği olan bir nesnesi olabilir.

# <a name="bicep"></a>[Bicep](#tab/bicep)

Döngüler, dağıtım sırasında bir dizi öğe döndürmek için kullanılabilir:

- Dizi üzerinde yineleme:

  ```bicep
  output <output-name> array = [for <item> in <collection>: {
    <properties>
  }]

  ```

- Bir dizinin öğeleri üzerinde yineleme yapma

  ```bicep
  output <output-name> array = [for <item>, <index> in <collection>: {
    <properties>
  }]
  ```

- Döngü dizinini kullanma

  ```bicep
  output <output-name> array = [for <index> in range(<start>, <stop>): {
    <properties>
  }]
  ```

---

## <a name="copy-limits"></a>Sınırları Kopyala

Sayım 800 ' i aşamaz.

Sayı negatif bir sayı olamaz. Yeni bir Azure CLı, PowerShell veya REST API sürümü ile şablonu dağıtırsanız sıfır olabilir. Özellikle, şunu kullanmanız gerekir:

- Azure PowerShell **2,6** veya üzeri
- Azure CLı **2.0.74** veya üzeri
- REST API sürüm **2019-05-10** veya üzeri
- [Bağlı dağıtımlar](linked-templates.md) , dağıtım kaynak türü için apı sürüm **2019-05-10** veya üstünü kullanmalıdır

PowerShell, CLı ve REST API 'nin önceki sürümleri Count için sıfırı desteklemez.

## <a name="outputs-iteration"></a>Çıkış yinelemesi

Aşağıdaki örnek, değişken sayıda depolama hesabı oluşturur ve her depolama hesabı için bir uç nokta döndürür:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "variables": {
    "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(), variables('baseName'))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ],
  "outputs": {
    "storageEndpoints": {
      "type": "array",
      "copy": {
        "count": "[parameters('storageCount')]",
        "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
      }
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageCount int = 2

var baseName_var = 'storage${uniqueString(resourceGroup().id)}'

resource baseName 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, storageCount): {
  name: '${i}${baseName_var}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]

output storageEndpoints array = [for i in range(0, storageCount): reference(${i}${baseName_var}).primaryEndpoints.blob]
```

---

Önceki şablon, aşağıdaki değerlere sahip bir dizi döndürür:

```json
[
  "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
  "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

Sonraki örnekte, yeni depolama hesaplarından üç özellik döndürülür.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "variables": {
    "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(), variables('baseName'))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ],
  "outputs": {
    "storageInfo": {
      "type": "array",
      "copy": {
        "count": "[parameters('storageCount')]",
        "input": {
          "id": "[reference(concat(copyIndex(), variables('baseName')), '2019-04-01', 'Full').resourceId]",
          "blobEndpoint": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]",
          "status": "[reference(concat(copyIndex(), variables('baseName'))).statusOfPrimary]"
        }
      }
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageCount int = 2

var baseName_var = 'storage${uniqueString(resourceGroup().id)}'

resource baseName 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, storageCount): {
  name: '${i}${baseName_var}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]

output storageInfo array = [for i in range(0, storageCount): {
  id: reference(concat(i, baseName_var), '2019-04-01', 'Full').resourceId
  blobEndpoint: reference(concat(i, baseName_var)).primaryEndpoints.blob
  status: reference(concat(i, baseName_var)).statusOfPrimary
}]
```

---

Yukarıdaki örnek aşağıdaki değerlere sahip bir dizi döndürür:

```json
[
  {
    "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
    "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  },
  {
    "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
    "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
    "status": "available"
  }
]
```

## <a name="next-steps"></a>Sonraki adımlar

- Öğreticiye gitmek için bkz. [öğretici: ARM şablonlarıyla birden çok kaynak örneği oluşturma](template-tutorial-create-multiple-instances.md).
- Kopyalama döngüsünün diğer kullanımları için bkz.:
  - [ARM şablonlarındaki kaynak yinelemesi](copy-resources.md)
  - [ARM şablonlarındaki Özellik yinelemesi](copy-properties.md)
  - [ARM şablonlarında değişken yineleme](copy-variables.md)
- Bir şablonun bölümleri hakkında bilgi edinmek istiyorsanız, bkz. [ARM şablonlarının yapısını ve sözdizimini anlayın](template-syntax.md).
- Şablonunuzu dağıtmayı öğrenmek için bkz. [ARM şablonlarıyla kaynak dağıtma ve Azure PowerShell](deploy-powershell.md).
