---
title: Şablon işlevleri-dağıtım
description: Dağıtım bilgilerini almak için bir Azure Resource Manager şablonunda kullanılacak işlevleri açıklar.
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 17caf78fb77e330685bb45ab03aaeed611900ba0
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149645"
---
# <a name="deployment-functions-for-azure-resource-manager-templates"></a>Azure Resource Manager şablonlar için dağıtım işlevleri 

Kaynak Yöneticisi, şablon bölümlerinden ve dağıtımla ilgili değerlerin değerlerini almak için aşağıdaki işlevleri sağlar:

* [dağıtmak](#deployment)
* [parametreler](#parameters)
* [variables](#variables)

Kaynaklardan, kaynak gruplarından veya aboneliklerden değerler almak için bkz. [kaynak işlevleri](resource-group-template-functions-resource.md).

<a id="deployment" />

## <a name="deployment"></a>dağıtım
`deployment()`

Geçerli dağıtım işlemiyle ilgili bilgileri döndürür.

### <a name="return-value"></a>Dönüş değeri

Bu işlev, dağıtım sırasında geçirilen nesneyi döndürür. Döndürülen nesnedeki özellikler, dağıtım nesnesinin bir bağlantı olarak mı yoksa satır içi nesne olarak mı geçiriltiğine göre farklılık gösterir. Dağıtım nesnesi, bir yerel dosyayı işaret etmek için Azure PowerShell ' de **-TemplateFile** parametresinin kullanıldığı durumlarda olduğu gibi, döndürülen nesne aşağıdaki biçimdedir:

```json
{
    "name": "",
    "properties": {
        "template": {
            "$schema": "",
            "contentVersion": "",
            "parameters": {},
            "variables": {},
            "resources": [
            ],
            "outputs": {}
        },
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Nesne bir bağlantı olarak geçirildiğinde, örneğin uzak bir nesneyi işaret etmek için **-templateuri** parametresi kullanılırken, nesne aşağıdaki biçimde döndürülür: 

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
        "parameters": {},
        "mode": "",
        "provisioningState": ""
    }
}
```

Bir [Azure aboneliğine dağıtırken](deploy-to-subscription.md), bir kaynak grubu yerine, dönüş nesnesi bir `location` özelliği içerir. Location özelliği, yerel bir şablon ya da bir dış şablon dağıtıldığında dahil edilir.

### <a name="remarks"></a>Açıklamalar

Üst şablonun URI 'sini temel alan başka bir şablona bağlamak için Deployment () kullanabilirsiniz.

```json
"variables": {  
    "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"  
}
```  

Portalın dağıtım geçmişinden bir şablonu yeniden dağıtırsanız, şablon yerel bir dosya olarak dağıtılır. Dağıtım işlevinde `templateLink` özelliği döndürülmedi. Şablonunuz başka bir şablonun bağlantısını oluşturmak için `templateLink` kullanıyorsa, yeniden dağıtmak için portalını kullanmayın. Bunun yerine, şablonu ilk olarak dağıtmak için kullandığınız komutları kullanın.

### <a name="example"></a>Örnek

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deployment.json) dağıtım nesnesini döndürür:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[deployment()]",
            "type" : "object"
        }
    }
}
```

Önceki örnekte aşağıdaki nesne döndürülür:

```json
{
  "name": "deployment",
  "properties": {
    "template": {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [],
      "outputs": {
        "subscriptionOutput": {
          "type": "Object",
          "value": "[deployment()]"
        }
      }
    },
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted"
  }
}
```

Dağıtım işlevini kullanan bir abonelik düzeyi şablon için bkz. [abonelik dağıtım işlevi](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/deploymentsubscription.json). `az deployment create` ya da `New-AzDeployment` komutlarıyla dağıtılır.

<a id="parameters" />

## <a name="parameters"></a>parametreler
`parameters(parameterName)`

Bir parametre değeri döndürür. Belirtilen parametre adı, şablonun parametreler bölümünde tanımlanmalıdır.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| parameterName |Yes |string |Döndürülecek parametrenin adı. |

### <a name="return-value"></a>Dönüş değeri

Belirtilen parametrenin değeri.

### <a name="remarks"></a>Açıklamalar

Genellikle, kaynak değerlerini ayarlamak için parametreleri kullanırsınız. Aşağıdaki örnek, Web sitesinin adını dağıtım sırasında geçirilen parametre değerine ayarlar.

```json
"parameters": { 
  "siteName": {
      "type": "string"
  }
},
"resources": [
   {
      "apiVersion": "2016-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/Sites",
      ...
   }
]
```

### <a name="example"></a>Örnek

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/parameters.json) , Parameters işlevinin Basitleştirilmiş kullanımını gösterir.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringParameter": {
            "type" : "string",
            "defaultValue": "option 1"
        },
        "intParameter": {
            "type": "int",
            "defaultValue": 1
        },
        "objectParameter": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b"}
        },
        "arrayParameter": {
            "type": "array",
            "defaultValue": [1, 2, 3]
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
            "type" : "string"
        },
        "intOutput": {
            "value": "[parameters('intParameter')]",
            "type" : "int"
        },
        "objectOutput": {
            "value": "[parameters('objectParameter')]",
            "type" : "object"
        },
        "arrayOutput": {
            "value": "[parameters('arrayParameter')]",
            "type" : "array"
        },
        "crossOutput": {
            "value": "[parameters('crossParameter')]",
            "type" : "string"
        }
    }
}
```

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| stringOutput | Dize | Seçenek 1 |
| ıntoutput | Int | 1 |
| objectOutput | Nesne | {"One": "a", "iki": "b"} |
| arrayOutput | Dizi | [1, 2, 3] |
| Çapraz Soutput | Dize | Seçenek 1 |

Parametreleri kullanma hakkında daha fazla bilgi için bkz. [Azure Resource Manager şablonundaki parametreler](template-parameters.md).

<a id="variables" />

## <a name="variables"></a>değişkenlerinin
`variables(variableName)`

Değişkenin değerini döndürür. Belirtilen değişken adının şablonun değişkenler bölümünde tanımlanması gerekir.

### <a name="parameters"></a>Parametreler

| Parametre | Gerekli | Tür | Açıklama |
|:--- |:--- |:--- |:--- |
| variableName |Yes |Dize |Döndürülecek değişkenin adı. |

### <a name="return-value"></a>Dönüş değeri

Belirtilen değişkenin değeri.

### <a name="remarks"></a>Açıklamalar

Genellikle, karmaşık değerleri yalnızca bir kez oluşturarak şablonunuzu basitleştirmek için değişkenler kullanırsınız. Aşağıdaki örnek, bir depolama hesabı için benzersiz bir ad oluşturur.

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

### <a name="example"></a>Örnek

Aşağıdaki [örnek şablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/variables.json) farklı değişken değerleri döndürüyor.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {
        "var1": "myVariable",
        "var2": [ 1,2,3,4 ],
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
            "type" : "string"
        },
        "exampleOutput2": {
            "value": "[variables('var2')]",
            "type" : "array"
        },
        "exampleOutput3": {
            "value": "[variables('var3')]",
            "type" : "string"
        },
        "exampleOutput4": {
            "value": "[variables('var4')]",
            "type" : "object"
        }
    }
}
```

Önceki örnekte varsayılan değerlere sahip çıktı.

| Ad | Tür | Değer |
| ---- | ---- | ----- |
| exampleOutput1 | Dize | myVariable & lt |
| exampleOutput2 | Dizi | [1, 2, 3, 4] |
| exampleOutput3 | Dize | myVariable & lt |
| exampleOutput4 |  Nesne | {"Property1": "değer1", "Property2": "değer2"} |

Değişkenleri kullanma hakkında daha fazla bilgi için bkz. [Azure Resource Manager şablonundaki değişkenler](template-variables.md).

## <a name="next-steps"></a>Sonraki adımlar
* Bir Azure Resource Manager şablonu olarak bölümlerde açıklaması için bkz: [Azure Resource Manager şablonları yazma](resource-group-authoring-templates.md).
* Çeşitli şablonları birleştirmek için bkz. [bağlantılı şablonları Azure Resource Manager kullanma](resource-group-linked-templates.md).
* Belirtilen sayıda yineleme için bir kaynak türünü oluştururken bkz [Azure Resource Manager'da kaynakları birden çok örneğini oluşturma](resource-group-create-multiple.md).
* Oluşturduğunuz bir şablonu dağıtmayı öğrenmek için bkz [Azure Resource Manager şablonu ile uygulama dağıtma](resource-group-template-deploy.md).

