---
title: Şablon işlevleri-dağıtım
description: Dağıtım bilgilerini almak için bir Azure Resource Manager şablonunda (ARM şablonu) kullanılacak işlevleri açıklar.
ms.topic: conceptual
ms.date: 03/02/2021
ms.openlocfilehash: a9a073284c62efac4e77f8f9b35e8730c350e5f1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101722730"
---
# <a name="deployment-functions-for-arm-templates"></a>ARM şablonları için dağıtım işlevleri

Kaynak Yöneticisi, Azure Resource Manager şablonunuzun geçerli dağıtımıyla ilgili değerleri almak için aşağıdaki işlevleri sağlar (ARM şablonu):

* [dağıtmak](#deployment)
* [environment (ortam)](#environment)
* [parametrelere](#parameters)
* [değişkenlerinin](#variables)

Kaynaklardan, kaynak gruplarından veya aboneliklerden değerler almak için bkz. [kaynak işlevleri](template-functions-resource.md).

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="deployment"></a>dağıtım

`deployment()`

Geçerli dağıtım işlemiyle ilgili bilgileri döndürür.

### <a name="return-value"></a>Döndürülen değer

Bu işlev, dağıtım sırasında geçirilen nesneyi döndürür. Döndürülen nesnedeki özellikler şunları yapıp etmeksizin farklılık gösterir:

* şablon veya şablon belirtimini dağıtma.
* Yerel bir dosya olan veya bir URI üzerinden erişilen uzak dosya olan bir şablonu dağıtan bir şablon dağıtma.
* bir kaynak grubuna dağıtma veya diğer kapsamlardan birine dağıtma ([Azure aboneliği](deploy-to-subscription.md), [Yönetim grubu](deploy-to-management-group.md)veya [kiracı](deploy-to-tenant.md)).

Yerel bir şablon bir kaynak grubuna dağıtıldığında: işlev aşağıdaki biçimi döndürür:

```json
{
  "name": "",
  "properties": {
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

Bir kaynak grubuna uzak şablon dağıtıldığında: işlev aşağıdaki biçimi döndürür:

```json
{
  "name": "",
  "properties": {
    "templateLink": {
      "uri": ""
    },
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

Bir kaynak grubuna bir şablon belirtimi dağıttığınızda, işlev aşağıdaki biçimi döndürür:

```json
{
  "name": "",
  "properties": {
    "templateLink": {
      "id": ""
    },
    "template": {
      "$schema": "",
      "contentVersion": "",
      "parameters": {},
      "variables": {},
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

Bir Azure aboneliğine, yönetim grubuna veya kiracıya dağıtırken, dönüş nesnesi bir `location` özelliği içerir. Location özelliği, yerel bir şablon ya da bir dış şablon dağıtıldığında dahil edilir. Biçim:

```json
{
  "name": "",
  "location": "",
  "properties": {
    "template": {
      "$schema": "",
      "contentVersion": "",
      "resources": [],
      "outputs": {}
    },
    "templateHash": "",
    "parameters": {},
    "mode": "",
    "provisioningState": ""
  }
}
```

### <a name="remarks"></a>Açıklamalar

Üst şablonun URI 'sini temel alan başka bir şablona bağlamak için Deployment () kullanabilirsiniz.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var sharedTemplateUrl = uri(deployment().prperties.templateLink.uri, 'shared-resources.json')
```

---

Portalın dağıtım geçmişinden bir şablonu yeniden dağıtırsanız, şablon yerel bir dosya olarak dağıtılır. `templateLink`Özellik dağıtım işlevinde döndürülmedi. Şablonunuz `templateLink` başka bir şablona bağlantı oluşturmak için kullanıyorsa, yeniden dağıtmak için portalını kullanmayın. Bunun yerine, şablonu ilk olarak dağıtmak için kullandığınız komutları kullanın.

### <a name="example"></a>Örnek

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) dağıtım nesnesini döndürür:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "deploymentOutput": {
      "type": "object",
      "value": "[deployment()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output deploymentOutput object = deployment()
```

---

Önceki örnekte aşağıdaki nesne döndürülür:

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "deploymentOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "templateHash": "13135986259522608210",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

## <a name="environment"></a>ortam

`environment()`

Dağıtım için kullanılan Azure ortamı hakkındaki bilgileri döndürür.

### <a name="return-value"></a>Döndürülen değer

Bu işlev, geçerli Azure ortamı için özellikleri döndürür. Aşağıdaki örnekte genel Azure özellikleri gösterilmektedir. Sovereign bulutları biraz farklı özellikler döndürebilir.

```json
{
  "name": "",
  "gallery": "",
  "graph": "",
  "portal": "",
  "graphAudience": "",
  "activeDirectoryDataLake": "",
  "batch": "",
  "media": "",
  "sqlManagement": "",
  "vmImageAliasDoc": "",
  "resourceManager": "",
  "authentication": {
    "loginEndpoint": "",
    "audiences": [
      "",
      ""
    ],
    "tenant": "",
    "identityProvider": ""
  },
  "suffixes": {
    "acrLoginServer": "",
    "azureDatalakeAnalyticsCatalogAndJob": "",
    "azureDatalakeStoreFileSystem": "",
    "azureFrontDoorEndpointSuffix": "",
    "keyvaultDns": "",
    "sqlServerHostname": "",
    "storage": ""
  }
}
```

### <a name="example"></a>Örnek

Aşağıdaki örnek şablon, ortam nesnesini döndürür.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [],
  "outputs": {
    "environmentOutput": {
      "type": "object",
      "value": "[environment()]"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
output environmentOutput object = environment()
```

---

Yukarıdaki örnekte, genel Azure 'a dağıtıldığında aşağıdaki nesne döndürülür:

```json
{
  "name": "AzureCloud",
  "gallery": "https://gallery.azure.com/",
  "graph": "https://graph.windows.net/",
  "portal": "https://portal.azure.com",
  "graphAudience": "https://graph.windows.net/",
  "activeDirectoryDataLake": "https://datalake.azure.net/",
  "batch": "https://batch.core.windows.net/",
  "media": "https://rest.media.azure.net",
  "sqlManagement": "https://management.core.windows.net:8443/",
  "vmImageAliasDoc": "https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json",
  "resourceManager": "https://management.azure.com/",
  "authentication": {
    "loginEndpoint": "https://login.windows.net/",
    "audiences": [
      "https://management.core.windows.net/",
      "https://management.azure.com/"
    ],
    "tenant": "common",
    "identityProvider": "AAD"
  },
  "suffixes": {
    "acrLoginServer": ".azurecr.io",
    "azureDatalakeAnalyticsCatalogAndJob": "azuredatalakeanalytics.net",
    "azureDatalakeStoreFileSystem": "azuredatalakestore.net",
    "azureFrontDoorEndpointSuffix": "azurefd.net",
    "keyvaultDns": ".vault.azure.net",
    "sqlServerHostname": ".database.windows.net",
    "storage": "core.windows.net"
  }
}
```

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Bir parametre değeri döndürür. Belirtilen parametre adı, şablonun parametreler bölümünde tanımlanmalıdır.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| parameterName |Yes |string |Döndürülecek parametrenin adı. |

### <a name="return-value"></a>Döndürülen değer

Belirtilen parametrenin değeri.

### <a name="remarks"></a>Açıklamalar

Genellikle, kaynak değerlerini ayarlamak için parametreleri kullanırsınız. Aşağıdaki örnek, Web sitesinin adını dağıtım sırasında geçirilen parametre değerine ayarlar.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "siteName": {
    "type": "string"
  }
}, "resources": [
  {
    "type": "Microsoft.Web/Sites",
    "apiVersion": "2016-08-01",
    "name": "[parameters('siteName')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param siteName string

resource mySite 'Microsoft.Web/Sites@2016-08-01' = {
  name: siteName
  ...
}
```

---

### <a name="example"></a>Örnek

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) , Parameters işlevinin Basitleştirilmiş kullanımını gösterir.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringParameter": {
      "type": "string",
      "defaultValue": "option 1"
    },
    "intParameter": {
      "type": "int",
      "defaultValue": 1
    },
    "objectParameter": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b"
      }
    },
    "arrayParameter": {
      "type": "array",
      "defaultValue": [ 1, 2, 3 ]
    },
    "crossParameter": {
      "type": "string",
      "defaultValue": "[parameters('stringParameter')]"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "stringOutput": {
      "value": "[parameters('stringParameter')]",
      "type": "string"
    },
    "intOutput": {
      "value": "[parameters('intParameter')]",
      "type": "int"
    },
    "objectOutput": {
      "value": "[parameters('objectParameter')]",
      "type": "object"
    },
    "arrayOutput": {
      "value": "[parameters('arrayParameter')]",
      "type": "array"
    },
    "crossOutput": {
      "value": "[parameters('crossParameter')]",
      "type": "string"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param stringParameter string = 'option 1'
param intParameter int = 1
param objectParameter object = {
  'one': 'a'
  'two': 'b'
}
param arrayParameter array = [
  1
  2
  3
]
param crossParameter string = stringParameter

output stringOutput string = stringParameter
output intOutput int = intParameter
output objectOutput object = objectParameter
output arrayOutput array = arrayParameter
output crossOutput string = crossParameter
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| stringOutput | Dize | seçenek 1 |
| ıntoutput | int | 1 |
| objectOutput | Nesne | {"One": "a", "iki": "b"} |
| arrayOutput | Dizi | [1, 2, 3] |
| Çapraz Soutput | Dize | seçenek 1 |

Parametreleri kullanma hakkında daha fazla bilgi için bkz. [ARM şablonlarındaki parametreler](template-parameters.md).

## <a name="variables"></a>değişkenlerinin

`variables(variableName)`

Değişkenin değerini döndürür. Belirtilen değişken adının şablonun değişkenler bölümünde tanımlanması gerekir.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Description |
|:--- |:--- |:--- |:--- |
| variableName |Evet |Dize |Döndürülecek değişkenin adı. |

### <a name="return-value"></a>Döndürülen değer

Belirtilen değişkenin değeri.

### <a name="remarks"></a>Açıklamalar

Genellikle, karmaşık değerleri yalnızca bir kez oluşturarak şablonunuzu basitleştirmek için değişkenler kullanırsınız. Aşağıdaki örnek, bir depolama hesabı için benzersiz bir ad oluşturur.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "storageName": "[concat('storage', uniqueString(resourceGroup().id))]"
},
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "dependsOn": [
      "[variables('storageName')]"
    ],
    ...
  }
],

```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var storageName = 'storage${uniqueString(resourceGroup().id)}'

resource myStorage 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageName
  ...
}

resource myVm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
}
```

---

### <a name="example"></a>Örnek

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) farklı değişken değerleri döndürüyor.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "var1": "myVariable",
    "var2": [ 1, 2, 3, 4 ],
    "var3": "[ variables('var1') ]",
    "var4": {
      "property1": "value1",
      "property2": "value2"
    }
  },
  "resources": [],
  "outputs": {
    "exampleOutput1": {
      "value": "[variables('var1')]",
      "type": "string"
    },
    "exampleOutput2": {
      "value": "[variables('var2')]",
      "type": "array"
    },
    "exampleOutput3": {
      "value": "[variables('var3')]",
      "type": "string"
    },
    "exampleOutput4": {
      "value": "[variables('var4')]",
      "type": "object"
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var var1 = 'myVariable'
var var2 = [
  1
  2
  3
  4
]
var var3 = var1
var var4 = {
  'property1': 'value1'
  'property2': 'value2'
}

output exampleOutput1 string = var1
output exampleOutput2 array = var2
output exampleOutput3 string = var3
output exampleOutput4 object = var4
```

---

Yukarıdaki örnekten alınan çıkış varsayılan değerleri:

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| exampleOutput1 | Dize | myVariable & lt |
| exampleOutput2 | Dizi | [1, 2, 3, 4] |
| exampleOutput3 | Dize | myVariable & lt |
| exampleOutput4 |  Nesne | {"Property1": "değer1", "Property2": "değer2"} |

Değişkenleri kullanma hakkında daha fazla bilgi için bkz. [ARM şablonundaki değişkenler](template-variables.md).

## <a name="next-steps"></a>Sonraki adımlar

* ARM şablonundaki bölümlerin açıklaması için bkz. [ARM şablonlarının yapısını ve sözdizimini anlayın](template-syntax.md).
