---
title: Birden çok kaynak örneğini dağıtma
description: Kaynak türünü birçok kez dağıtmak için bir Azure Resource Manager şablonunda kopyalama işlemi ve dizileri (ARM şablonu) kullanın.
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 5ddb0cabf0acae1ffe9b9e77e6defa70f9cbd61b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479976"
---
# <a name="resource-iteration-in-arm-templates"></a>ARM şablonlarındaki kaynak yinelemesi

Bu makalede Azure Resource Manager şablonunuzda (ARM şablonu) bir kaynağın birden fazla örneğini nasıl oluşturacağınız gösterilmektedir. Şablonunuzun kaynaklar bölümüne kopyalama döngüsü ekleyerek, dağıtılacak kaynak sayısını dinamik olarak ayarlayabilirsiniz. Ayrıca, şablon söz dizimini yinelemek zorunda kalmaktan kaçının.

Kopyalama döngüsünü [Özellikler](copy-properties.md), [değişkenler](copy-variables.md)ve [çıktılar](copy-outputs.md)ile de kullanabilirsiniz.

Bir kaynağın hiç dağıtılıp dağıtılmadığını belirtmeniz gerekiyorsa bkz. [koşul öğesi](conditional-resource-deployment.md).

## <a name="syntax"></a>Syntax

# <a name="json"></a>[JSON](#tab/json)

`copy`Kaynağın birden çok örneğini dağıtmak için şablonunuzun kaynaklar bölümüne öğesi ekleyin. `copy`Öğesi aşağıdaki genel biçime sahiptir:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

`name`Özelliği, döngüsünü tanımlayan herhangi bir değerdir. `count`Özelliği, kaynak türü için istediğiniz yineleme sayısını belirtir.

`mode` `batchSize` Kaynakların paralel veya sıralı olarak dağıtılıp dağıtılmadığını belirtmek için ve özelliklerini kullanın. Bu özellikler [seri veya paralel](#serial-or-parallel)olarak açıklanmaktadır.

# <a name="bicep"></a>[Bicep](#tab/bicep)

Döngüler şu şekilde birden çok kaynak bildirmek için kullanılabilir:

- Dizi üzerinde yineleme:

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
    <resource-properties>
  }]
  ```

- Bir dizinin öğeleri üzerinde yineleme yapma

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for (<item>, <index>) in <collection>: {
    <resource-properties>
  }]
  ```

- Döngü dizinini kullanma

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <index> in range(<start>, <stop>): {
    <resource-properties>
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

Kopya döngüsü ile [tamamlanmış mod dağıtımını](deployment-modes.md) kullanırken dikkatli olun. Tüm modu bir kaynak grubuna yeniden dağıtıyorsanız, kopyalama döngüsü çözümlendikten sonra şablonda belirtilmeyen tüm kaynaklar silinir.

## <a name="resource-iteration"></a>Kaynak yinelemesi

Aşağıdaki örnek, parametresinde belirtilen depolama hesabı sayısını oluşturur `storageCount` .

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
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
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
  ]
}
```

Her bir kaynağın adı, `copyIndex()` döngüsünde geçerli yinelemeyi döndüren işlevini içerir. `copyIndex()` sıfır tabanlıdır. Bu nedenle, aşağıdaki örnek:

```json
"name": "[concat('storage', copyIndex())]",
```

Şu adları oluşturur:

- storage0
- storage1
- storage2.

Dizin değerini kaydırmak için, işlevinde bir değer geçirebilirsiniz `copyIndex()` . Yineleme sayısı kopyalama öğesinde hala belirtilir, ancak değeri `copyIndex` belirtilen değere göre denkleştirilir. Bu nedenle, aşağıdaki örnek:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Şu adları oluşturur:

- storage1
- storage2
- storage3

Dizideki her öğe arasında yineleme yapmak için, diziler ile çalışırken kopyalama işlemi faydalıdır. `length`Yineleme sayısını belirtmek ve `copyIndex` dizideki geçerli dizini almak için dizideki işlevini kullanın.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageCount int = 2

resource storage_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, storageCount): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

Dizinin `i` depolama hesabı kaynak adını oluştururken kullanıldığını unutmayın.

---

Aşağıdaki örnek, parametresinde belirtilen her ad için bir depolama hesabı oluşturur.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageNames": {
          "type": "array",
          "defaultValue": [
            "contoso",
            "fabrikam",
            "coho"
          ]
      }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('storageNames')[copyIndex()], uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[length(parameters('storageNames'))]"
      }
    }
  ],
  "outputs": {}
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageNames array = [
  'contoso'
  'fabrikam'
  'coho'
]

resource storageNames_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for name in storageNames: {
  name: concat(name, uniqueString(resourceGroup().id))
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

---

Dağıtılan kaynaklardan değer döndürmek istiyorsanız, [çıktılar bölümünde Kopyala](copy-outputs.md)' yı kullanabilirsiniz.

## <a name="serial-or-parallel"></a>Seri veya paralel

Varsayılan olarak Kaynak Yöneticisi, kaynakları paralel olarak oluşturur. Şablondaki 800 kaynağın toplam sınırının dışında, paralel olarak dağıtılan kaynak sayısına sınır uygulanmaz. Bunların oluşturulma sırası garanti edilmez.

Ancak, kaynakların sırayla dağıtılmasını belirtmek isteyebilirsiniz. Örneğin, bir üretim ortamını güncelleştirirken, yalnızca belirli bir sayının herhangi bir zamanda güncelleştirilmesini sağlamak isteyebilirsiniz.

Örneğin, depolama hesaplarını tek seferde bir kez dağıtmak için şunu kullanın:

# <a name="json"></a>[JSON](#tab/json)

Bir kaynağın birden fazla örneğini seri olarak dağıtmak için, `mode` **seri** olarak ve `batchSize` aynı anda dağıtılacak örnek sayısına ayarlayın. Seri modda Kaynak Yöneticisi, döngüdeki önceki örneklerde bir bağımlılık oluşturur, bu nedenle, önceki toplu işlem tamamlanana kadar bir toplu işlem başlatmaz.

Değeri, `batchSize` `count` Copy öğesindeki değerini aşamaz.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

`mode`Özelliği, varsayılan değer olan **paralel** olarak da kabul eder.

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bir kaynağın birden fazla örneğini almak için, `batchSize` [dekoratörü](./bicep-file.md#resource-and-module-decorators) tek seferde dağıtılacak örnek sayısı olarak ayarlayın. Seri modda Kaynak Yöneticisi, döngüdeki önceki örneklerde bir bağımlılık oluşturur, bu nedenle, önceki toplu işlem tamamlanana kadar bir toplu işlem başlatmaz.

```bicep
@batchSize(2)
resource storage_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, 4): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

---

## <a name="iteration-for-a-child-resource"></a>Alt kaynak için yineleme

Alt kaynak için bir kopyalama döngüsü kullanamazsınız. Genellikle başka bir kaynak içinde iç içe olarak tanımladığınız bir kaynağın birden fazla örneğini oluşturmak için, bunun yerine o kaynağı en üst düzey kaynak olarak oluşturmanız gerekir. Üst kaynakla ilişkiyi tür ve ad özellikleri aracılığıyla tanımlarsınız.

Örneğin, genellikle bir veri kümesini bir veri fabrikası içinde alt kaynak olarak tanımladığınızı varsayalım.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/factories",
  "name": "exampleDataFactory",
  ...
  "resources": [
    {
      "type": "datasets",
      "name": "exampleDataSet",
      "dependsOn": [
        "exampleDataFactory"
      ],
      ...
    }
  ]
```

Birden fazla veri kümesi oluşturmak için veri fabrikası dışına taşıyın. Veri kümesi Data Factory ile aynı düzeyde olmalıdır, ancak yine de Data Factory 'nin bir alt kaynağıdır. Veri kümesi ve Veri Fabrikası arasındaki ilişkiyi tür ve ad özellikleriyle koruyabilirsiniz. Tür artık şablondaki konumundan çıkarsanamıyor, tam türü şu biçimde sağlamanız gerekir: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}` .

Veri fabrikasının bir örneğiyle üst/alt ilişkisi kurmak için, üst kaynak adını içeren veri kümesi için bir ad sağlayın. `{parent-resource-name}/{child-resource-name}` biçimini kullanın.

Aşağıdaki örnek, uygulamayı göstermektedir:

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
{
  "type": "Microsoft.DataFactory/factories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/factories/datasets",
  "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
  "dependsOn": [
    "exampleDataFactory"
  ],
  "copy": {
    "name": "datasetcopy",
    "count": "3"
  },
  ...
}]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource dataFactoryName_resource 'Microsoft.DataFactory/factories@2018-06-01' = {
  name: "exampleDataFactory"
  ...
}

resource dataFactoryName_ArmtemplateTestDatasetIn 'Microsoft.DataFactory/factories/datasets@2018-06-01' = [for i in range(0, 3): {
  name: 'exampleDataFactory/exampleDataset${i}'
  ...
}
```

---

## <a name="example-templates"></a>Örnek Şablonlar

Aşağıdaki örneklerde bir kaynak veya özelliğin birden fazla örneğini oluşturmak için yaygın senaryolar gösterilmektedir.

|Şablon  |Açıklama  |
|---------|---------|
|[Depolama alanını Kopyala](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Adında Dizin numarası olan birden fazla depolama hesabı dağıtır. |
|[Seri kopyalama depolaması](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Birden çok depolama hesabını zamanında dağıtır. Ad, Dizin numarasını içerir. |
|[Depolamayı dizi ile kopyalama](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Birkaç depolama hesabı dağıtır. Ad, diziden bir değer içerir. |

## <a name="next-steps"></a>Sonraki adımlar

- Bir kopyalama döngüsünde oluşturulan kaynaklardaki bağımlılıkları ayarlamak için bkz. [ARM şablonlarındaki kaynakları dağıtma sırasını tanımlama](define-resource-dependency.md).
- Öğreticiye gitmek için bkz. [öğretici: ARM şablonlarıyla birden çok kaynak örneği oluşturma](template-tutorial-create-multiple-instances.md).
- Kaynak kopyasını içeren Microsoft Learn bir modül için bkz. [GELIŞMIŞ ARM şablon özelliklerini kullanarak karmaşık bulut dağıtımlarını yönetme](/learn/modules/manage-deployments-advanced-arm-template-features/).
- Kopyalama döngüsünün diğer kullanımları için bkz.:
  - [ARM şablonlarındaki Özellik yinelemesi](copy-properties.md)
  - [ARM şablonlarında değişken yineleme](copy-variables.md)
  - [ARM şablonlarındaki çıkış yinelemesi](copy-outputs.md)
- İç içe şablonlar ile kopyalama kullanma hakkında daha fazla bilgi için, bkz. [kopyalamayı kullanma](linked-templates.md#using-copy).
