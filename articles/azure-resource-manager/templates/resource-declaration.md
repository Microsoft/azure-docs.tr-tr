---
title: Şablonlarda kaynakları bildirme
description: Azure Resource Manager şablonunda (ARM şablonu) dağıtılacak kaynakların nasıl bildirileneceğini açıklar.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: 13f4a08162c40cbb36173627d88a4a8202a4ed26
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101746663"
---
# <a name="resource-declaration-in-arm-templates"></a>ARM şablonlarındaki kaynak bildirimi

Bir Azure Resource Manager şablonuyla (ARM şablonu) bir kaynağı dağıtmak için bir kaynak bildirimi eklersiniz. `resources`JSON şablonu için diziyi veya `resource` Bıcep anahtar sözcüğünü kullanın.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="set-resource-type-and-version"></a>Kaynak türünü ve sürümü ayarla

Şablonunuza kaynak eklerken, kaynak türünü ve API sürümünü ayarlayarak başlatın. Bu değerler, kaynak için kullanılabilen diğer özellikleri belirlerdir.

Aşağıdaki örnekte, bir depolama hesabı için kaynak türü ve API sürümünün nasıl ayarlanacağı gösterilmektedir. Örnek, tam kaynak bildirimini göstermez.

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  ...
}
```

---

Bıcep için, kaynak için bir sembolik ad ayarlarsınız. Yukarıdaki örnekte, sembolik ad olur `sa` . Sembolik ad için herhangi bir değer kullanabilirsiniz, ancak şablondaki başka bir kaynak, parametre veya değişken ile aynı olamaz. Sembolik ad, kaynak adı ile aynı değildir. Şablonunuzun diğer bölümlerinde kaynağa kolayca başvurmak için simgesel adı kullanın.

## <a name="set-resource-name"></a>Kaynak adını ayarla

Her kaynağın bir adı vardır. Kaynak adı ayarlanırken, [kaynak adları için kurallara ve kısıtlamalara](../management/resource-name-rules.md)dikkat edin.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  ...
}
```

---

## <a name="set-location"></a>Konum ayarlama

Birçok kaynak için bir konum gereklidir. Kaynağın IntelliSense veya [şablon başvurusu](/azure/templates/)aracılığıyla bir konuma ihtiyacı olup olmadığını belirleyebilirsiniz. Aşağıdaki örnek, depolama hesabı için kullanılan bir konum parametresi ekler.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "storageAccountName": {
    "type": "string",
    "minLength": 3,
    "maxLength": 24
  },
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-06-01",
    "name": "[parameters('storageAccountName')]",
    "location": "[parameters('location')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: location
  ...
}
```

---

Daha fazla bilgi için bkz. [ARM şablonunda kaynak konumunu ayarlama](resource-location.md).

## <a name="set-tags"></a>Etiketleri ayarla

Dağıtım sırasında bir kaynağa Etiketler uygulayabilirsiniz. Etiketler, dağıtılan kaynaklarınızı mantıksal olarak düzenlemenize yardımcı olur. Etiketleri belirtebileceğiniz farklı yollarla ilgili örnekler için bkz. [ARM Şablon Etiketleri](../management/tag-resources.md#arm-templates).

## <a name="set-resource-specific-properties"></a>Kaynağa özgü özellikleri ayarlama

Yukarıdaki özellikler, çoğu kaynak türüne geneldir. Bu değerleri ayarladıktan sonra, dağıtmakta olduğunuz kaynak türüne özgü özellikleri ayarlamanız gerekir.

Hangi özelliklerin kullanılabilir olduğunu ve hangilerinin gerekli olduğunu öğrenmek için IntelliSense veya [şablon başvurusunu](/azure/templates/) kullanın. Aşağıdaki örnek, bir depolama hesabı için kalan özellikleri ayarlar.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string",
      "minLength": 3,
      "maxLength": 24
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "functions": [],
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string
param location string = resourceGroup().location

resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  location: location
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

---

## <a name="next-steps"></a>Sonraki adımlar

* Bir kaynağı koşullu olarak dağıtmak için bkz. [ARM şablonlarında koşullu dağıtım](conditional-resource-deployment.md).
* Kaynak bağımlılıklarını ayarlamak için bkz. [ARM şablonlarında kaynak dağıtma sırasını tanımlama](define-resource-dependency.md).
