---
title: JSON ve Bıcep 'de Azure Resource Manager şablonlarının sözdizimini karşılaştırın
description: JSON ve Bıcep ile geliştirilen Azure Resource Manager şablonlarını karşılaştırır ve diller arasında nasıl dönüştürme yapılacağını gösterir.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 225e52e9534a77a01502b762f043a4f34df19caa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461803"
---
# <a name="comparing-json-and-bicep-for-templates"></a>Şablonlar için JSON ve Bıcep karşılaştırması

Bu makalede, Bıcep sözdizimini Azure Resource Manager şablonları (ARM şablonları) için JSON sözdizimiyle karşılaştırılmaktadır. Çoğu durumda Bıcep, JSON 'daki eşdeğer değerden daha az ayrıntılı bir sözdizimi sağlar.

ARM şablonları geliştirmek için JSON kullanmaya alışdıysanız, Bıcep için eşdeğer sözdizimi hakkında bilgi edinmek için aşağıdaki örnekleri kullanın.

## <a name="expressions"></a>İfadeler

Bir ifadeyi yazmak için:

```bicep
func()
```

```json
"[func()]"
```

## <a name="parameters"></a>Parametreler

Bir parametreyi varsayılan değerle bildirmek için:

```bicep
param demoParam string = 'Contoso'
```

```json
"parameters": {
  "demoParam": {
    "type": "string",
    "defaultValue": "Contoso"
  }
}
```

Bir parametre değeri almak için:

```bicep
demoParam
```

```json
[parameters('demoParam'))]
```

## <a name="variables"></a>Değişkenler

Bir değişken bildirmek için:

```bicep
var demoVar = 'example value'
```

```json
"variables": {
  "demoVar": "example value"
},
```

Bir değişken değeri almak için:

```bicep
demoVar
```

```json
[variables('demoVar'))]
```

## <a name="strings"></a>Dizeler

Dizeleri birleştirmek için:

```bicep
'${namePrefix}-vm'
```

```json
[concat(parameters('namePrefix'), '-vm')]
```

## <a name="logical-operators"></a>Mantıksal işleçler

Mantıksal **ve** döndürmek için:

```bicep
isMonday && isNovember
```

```json
[and(parameter('isMonday'), parameter('isNovember'))]
```

Koşullu bir değer ayarlamak için:

```bicep
isMonday ? 'valueIfTrue' : 'valueIfFalse'
```

```json
[if(parameters('isMonday'), 'valueIfTrue', 'valueIfFalse')]
```

## <a name="deployment-scope"></a>Dağıtım kapsamı

Dağıtımın hedef kapsamını ayarlamak için:

```bicep
targetScope = 'subscription'
```

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#"
```

## <a name="resources"></a>Kaynaklar

Bir kaynak bildirmek için:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
}
```

```json
"resources": [ 
  { 
    "type": "Microsoft.Compute/virtualMachines", 
    "apiVersion": "2020-06-01", 
    ... 
  } 
]
```

Bir kaynağı koşullu olarak dağıtmak için:

```bicep
resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = if(deployVM) {
  ...
}
```

```json
"resources": [ 
  {
    "condition": "[parameters('deployVM')]",
    "type": "Microsoft.Compute/virtualMachines", 
    "apiVersion": "2020-06-01", 
    ... 
  } 
]
```

Kaynak özelliğini ayarlamak için:

```bicep
sku: '2016-Datacenter'
```

```json
"sku": "2016-Datacenter",
```

Şablondaki kaynağın kaynak KIMLIĞINI almak için:

```bicep
nic1.id
```

```json
[resourceId('Microsoft.Network/networkInterfaces', variables('nic1Name'))]
```

## <a name="loops"></a>Döngüler

Bir dizideki veya sayıdaki öğeleri yinelemek için:

```bicep
[for storageName in storageAccounts: {
  ...
}]
```

```json
"copy": {
  "name": "storagecopy",
  "count": "[length(parameters('storageAccounts'))]"
},
...
```

## <a name="resource-dependencies"></a>Kaynak bağımlılıkları

Kaynaklar arasında bağımlılık ayarlamak için:

Bıcep için, bağımlılıkların otomatik olarak algılanmasını ya da el ile bağımlılık ayarlamayı güvenin.

```bicep
dependsOn: [ stg ]
```

```json
"dependsOn": ["[resourceId('Microsoft.Storage/storageAccounts', 'parameters('storageAccountName'))]"]
```

## <a name="reference-resources"></a>Başvuru kaynakları

Şablondaki bir kaynaktan bir özelliği almak için:

```bicep
diagsAccount.properties.primaryEndpoints.blob
```

```json
[reference(resourceId('Microsoft.Storage/storageAccounts', variables('diagStorageAccountName'))).primaryEndpoints.blob]
```

Şablonda dağıtılan mevcut bir kaynaktan bir özelliği almak için:

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: storageAccountName
}

// use later in template as often as needed
stg.properties.primaryEndpoints.blob
```

```json
// required every time the property is needed
"[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
```

## <a name="outputs"></a>Çıkışlar

Şablondaki bir kaynaktaki bir özelliğin çıktısını almak için:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
  },
}
```

## <a name="code-reuse"></a>Kod yeniden kullanımı

Bir çözümü birden çok dosyaya ayırmak için:

* Bıcep için [modüller](bicep-tutorial-add-modules.md)' i kullanın.
* JSON için [bağlantılı şablonlar](linked-templates.md)kullanın.

## <a name="recommendations"></a>Öneriler

* Mümkün olduğunda, Bıcep dosyanızdaki [Reference](template-functions-resource.md#reference) ve [RESOURCEID](template-functions-resource.md#resourceid) işlevlerini kullanmaktan kaçının. Aynı bicep dağıtımında bir kaynağa başvurduğunuzda, bunun yerine kaynak tanımlayıcıyı kullanın. Örneğin, Bıcep dosyanızda kaynak tanımlayıcısı olarak bulunan bir kaynak dağıttıysanız `stg` , `stg.id` `stg.properties.primaryEndpoints.blob` özellik değerlerini almak için veya gibi bir sözdizimi kullanın. Kaynak tanımlayıcısını kullanarak kaynaklar arasında örtük bir bağımlılık oluşturursunuz. Bağımlılığı Bağımlıdson özelliği ile açıkça ayarlamanız gerekmez.
* Kaynak Bıcep dosyasında dağıtılmamışsa, **var olan** anahtar sözcüğünü kullanarak kaynağa bir sembolik başvuru almaya devam edebilirsiniz.
* Tanımlayıcılar için tutarlı büyük harf kullanın. Ne tür bir büyük küçük harfe kullanılacağı konusunda emin değilseniz, ortası büyük küçük harf 'i deneyin. Örneğin, `param myCamelCasedParameter string`.
* Yalnızca açıklama kullanıcılara önemli bilgiler sağlıyorsa parametreye bir açıklama ekleyin. `//`Bazı bilgiler için açıklamaları kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

* Bıcep hakkında daha fazla bilgi için bkz. [bıcep öğreticisi](./bicep-tutorial-create-first-bicep.md).
* Dilleri arasında şablonları dönüştürme hakkında bilgi edinmek için bkz. [JSON ve Bıcep arasında ARM şablonlarını dönüştürme](bicep-decompile.md).
