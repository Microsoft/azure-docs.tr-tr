---
title: Birden çok kaynak örneğini dağıtma
description: Kaynak dağıtımında birden çok kez yinelemek için bir Azure Resource Manager şablonunda kopyalama işlemini ve dizileri kullanın.
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: 3ee4b47dd6cb9043a4100d114c483d1feadbde38
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150796"
---
# <a name="resource-property-or-variable-iteration-in-azure-resource-manager-templates"></a>Azure Resource Manager şablonlarda kaynak, özellik veya değişken yinelemesi

Bu makalede, Azure Resource Manager şablonunuzda bir kaynağın, değişkenin veya özelliğin birden fazla örneğini nasıl oluşturacağınız gösterilmektedir. Birden çok örnek oluşturmak için `copy` nesnesini şablonunuza ekleyin.

Bir kaynakla birlikte kullanıldığında, Copy nesnesi aşağıdaki biçimdedir:

```json
"copy": {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "mode": "serial" <or> "parallel",
    "batchSize": <number-to-deploy-serially>
}
```

Bir değişken veya özellik ile kullanıldığında, Copy nesnesi aşağıdaki biçimdedir:

```json
"copy": [
  {
      "name": "<name-of-loop>",
      "count": <number-of-iterations>,
      "input": <values-for-the-property-or-variable>
  }
]
```

Her iki kullanım de bu makalede daha ayrıntılı olarak açıklanmıştır. Öğretici için bkz. [öğretici: Kaynak Yöneticisi şablonları kullanarak birden çok kaynak örneği oluşturma](./resource-manager-tutorial-create-multiple-instances.md).

Bir kaynağın hiç dağıtılıp dağıtılmadığını belirtmeniz gerekiyorsa bkz. [koşul öğesi](conditional-resource-deployment.md).

## <a name="copy-limits"></a>Sınırları Kopyala

Yineleme sayısını belirtmek için Count özelliği için bir değer sağlarsınız. Sayım 800 ' i aşamaz.

Sayı negatif bir sayı olamaz. Azure PowerShell 2,6 veya üzeri, Azure CLı 2.0.74 veya üzeri ya da REST API sürüm **2019-05-10** veya üzeri bir şablon dağıtırsanız, sayıyı sıfıra ayarlayabilirsiniz. PowerShell, CLı ve REST API 'nin önceki sürümleri Count için sıfırı desteklemez.

Kopya ile [tamamlanmış mod dağıtımını](deployment-modes.md) kullanırken dikkatli olun. Tüm modu bir kaynak grubuna yeniden dağıtıyorsanız, kopyalama döngüsü çözümlendikten sonra şablonda belirtilmeyen tüm kaynaklar silinir.

Sayı sınırları, bir kaynak, değişken veya özellik ile birlikte kullanılıp kullanılmayacağı ile aynıdır.

## <a name="resource-iteration"></a>Kaynak yinelemesi

Bir dağıtımda kaynağın birden fazla örneğini oluşturmak istiyorsanız, kaynak türüne bir `copy` öğesi ekleyin. Copy öğesinde, bu döngü için yineleme sayısını ve bir adı belirtin.

Birkaç kez oluşturulacak kaynak aşağıdaki biçimi alır:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    }
  ],
  "outputs": {}
}
```

Her bir kaynağın adının döngüsünde geçerli yinelemeyi döndüren `copyIndex()` işlevini içerdiğine dikkat edin. `copyIndex()` sıfır tabanlıdır. Bu nedenle, aşağıdaki örnek:

```json
"name": "[concat('storage', copyIndex())]",
```

Şu adları oluşturur:

* storage0
* storage1
* storage2.

Dizin değerini kaydırmak için copyIndex () işlevine bir değer geçirebilirsiniz. Yineleme sayısı kopyalama öğesinde hala belirtilir, ancak Copyındex değeri belirtilen değere göre denkleştirilir. Bu nedenle, aşağıdaki örnek:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Şu adları oluşturur:

* storage1
* storage2
* storage3

Dizideki her öğe arasında yineleme yapmak için, diziler ile çalışırken kopyalama işlemi faydalıdır. Yineleme sayısını belirtmek için dizideki `length` işlevini kullanın ve dizideki geçerli dizini almak için `copyIndex`. Bu nedenle, aşağıdaki örnek:

```json
"parameters": {
  "org": {
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
    "name": "[concat('storage', parameters('org')[copyIndex()])]",
    "copy": {
      "name": "storagecopy",
      "count": "[length(parameters('org'))]"
    },
    ...
  }
]
```

Şu adları oluşturur:

* storagecontoso
* storagefabrikam
* storagecoho

Varsayılan olarak Kaynak Yöneticisi, kaynakları paralel olarak oluşturur. Şablondaki 800 kaynağın toplam sınırının dışında, paralel olarak dağıtılan kaynak sayısına sınır uygulanmaz. Bunların oluşturulma sırası garanti edilmez.

Ancak, kaynakların sırayla dağıtılmasını belirtmek isteyebilirsiniz. Örneğin, bir üretim ortamını güncelleştirirken, yalnızca belirli bir sayının herhangi bir zamanda güncelleştirilmesini sağlamak isteyebilirsiniz. Bir kaynağın birden fazla örneğini yeniden dağıtmak için `mode` **seri** olarak ayarlayın ve aynı anda dağıtılacak örnek sayısına `batchSize`. Seri modda Kaynak Yöneticisi, döngüdeki önceki örneklerde bir bağımlılık oluşturur, bu nedenle, önceki toplu işlem tamamlanana kadar bir toplu işlem başlatmaz.

Örneğin, depolama hesaplarını tek seferde bir kez dağıtmak için şunu kullanın:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      }
    }
  ],
  "outputs": {}
}
```

Mode özelliği, varsayılan değer olan **Parallel**öğesini de kabul eder.

İç içe şablonlar ile kopyalama kullanma hakkında daha fazla bilgi için, bkz. [kopyalamayı kullanma](resource-group-linked-templates.md#using-copy).

## <a name="property-iteration"></a>Özellik yineleme

Bir kaynaktaki bir özellik için birden fazla değer oluşturmak için, Properties öğesine bir `copy` dizisi ekleyin. Bu dizi nesneleri içerir ve her bir nesne aşağıdaki özelliklere sahiptir:

* ad-için birkaç değer oluşturulacak özelliğin adı
* sayı-oluşturulacak değer sayısı.
* Input-özelliğe atanacak değerleri içeren nesne

Aşağıdaki örnek, `copy` bir sanal makinede dataDisks özelliğine nasıl uygulanacağını gösterir:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "copy": [{
        "name": "dataDisks",
        "count": 3,
        "input": {
          "lun": "[copyIndex('dataDisks')]",
          "createOption": "Empty",
          "diskSizeGB": "1023"
        }
      }],
      ...
```

Özellik yinelemesi içinde `copyIndex` kullanırken yinelemenin adını belirtmeniz gerektiğini unutmayın. Kaynak yinelemesi ile kullanıldığında adı sağlamanız gerekmez.

Kaynak Yöneticisi, dağıtım sırasında `copy` dizisini genişletir. Dizinin adı, özelliğin adı olur. Giriş değerleri nesne özellikleri olur. Dağıtılan şablon şu şekilde olur:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        }
      ],
      ...
```

Copy öğesi bir dizidir, böylece kaynak için birden fazla özellik belirtebilirsiniz. Oluşturulacak her özellik için bir nesne ekleyin.

```json
{
  "name": "string",
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

Kaynak ve özellik yinelemesini birlikte kullanabilirsiniz. Özellik yinelemesine ada göre başvurun.

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "apiVersion": "2018-04-01",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

## <a name="variable-iteration"></a>Değişken yineleme

Bir değişkenin birden çok örneğini oluşturmak için, değişkenler bölümündeki `copy` özelliğini kullanın. `input` özelliğindeki değerden oluşturulan bir dizi öğe oluşturursunuz. `copy` özelliğini bir değişken içinde veya değişkenler bölümünün en üst düzeyinde kullanabilirsiniz. Bir değişken yinelemesi içinde `copyIndex` kullanırken, yinelemenin adını sağlamanız gerekir.

Dize değerleri dizisi oluşturmaya yönelik basit bir örnek için bkz. [dizi şablonunu kopyalama](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/copy-array/azuredeploy.json).

Aşağıdaki örnek, dinamik olarak oluşturulan öğelerle dizi değişkenleri oluşturmanın birkaç farklı yolunu göstermektedir. Nesne ve dize dizileri oluşturmak için bir değişkenin içinde kopyalamayı nasıl kullanacağınızı gösterir. Ayrıca, nesnelerin, dizelerin ve tamsayıların dizilerini oluşturmak için en üst düzeyde kopyanın nasıl kullanılacağını gösterir.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Oluşturulan değişkenin türü, giriş nesnesine bağlıdır. Örneğin, önceki örnekteki **en üst düzey nesne-dizisi** adlı değişken şunu döndürür:

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

Ve **en üst düzey dize-dizi** adlı değişken şunu döndürür:

```json
[
  "myDataDisk1",
  "myDataDisk2",
  "myDataDisk3",
  "myDataDisk4",
  "myDataDisk5"
]
```

## <a name="depend-on-resources-in-a-loop"></a>Bir döngüdeki kaynaklara bağlıdır

Bir kaynağın, `dependsOn` öğesini kullanarak başka bir kaynaktan sonra dağıtıldığını belirtirsiniz. Bir döngüde kaynak koleksiyonuna bağlı olan bir kaynağı dağıtmak için, Bağımlıdson öğesinde kopyalama döngüsünün adını sağlayın. Aşağıdaki örnek, sanal makineyi dağıtmadan önce üç depolama hesabının nasıl dağıtılacağını göstermektedir. Tam sanal makine tanımı gösterilmez. Kopyalama öğesinin adı `storagecopy` olarak ayarlanmış olduğuna ve sanal makinelerin Bağımlıdson öğesinin de `storagecopy`olarak ayarlandığını unutmayın.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    },
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

<a id="looping-on-a-nested-resource" />

## <a name="iteration-for-a-child-resource"></a>Alt kaynak için yineleme
Alt kaynak için bir kopyalama döngüsü kullanamazsınız. Genellikle başka bir kaynak içinde iç içe olarak tanımladığınız bir kaynağın birden fazla örneğini oluşturmak için, bunun yerine o kaynağı en üst düzey kaynak olarak oluşturmanız gerekir. Üst kaynakla ilişkiyi tür ve ad özellikleri aracılığıyla tanımlarsınız.

Örneğin, genellikle bir veri kümesini bir veri fabrikası içinde alt kaynak olarak tanımladığınızı varsayalım.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
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

Birden fazla veri kümesi oluşturmak için veri fabrikası dışına taşıyın. Veri kümesi Data Factory ile aynı düzeyde olmalıdır, ancak yine de Data Factory 'nin bir alt kaynağıdır. Veri kümesi ve Veri Fabrikası arasındaki ilişkiyi tür ve ad özellikleriyle koruyabilirsiniz. Tür, şablondaki konumundan daha fazla çıkarsanamıyor, tam türü: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`biçiminde sağlamanız gerekir.

Veri fabrikasının bir örneğiyle üst/alt ilişkisi kurmak için, üst kaynak adını içeren veri kümesi için bir ad sağlayın. Şu biçimi kullanın: `{parent-resource-name}/{child-resource-name}`.

Aşağıdaki örnek, uygulamayı göstermektedir:

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/datafactories/datasets",
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

## <a name="example-templates"></a>Örnek şablonları

Aşağıdaki örneklerde bir kaynak veya özelliğin birden fazla örneğini oluşturmak için yaygın senaryolar gösterilmektedir.

|Şablon  |Açıklama  |
|---------|---------|
|[Depolama alanını Kopyala](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Adında Dizin numarası olan birden fazla depolama hesabı dağıtır. |
|[Seri kopyalama depolaması](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Birden çok depolama hesabını zamanında dağıtır. Ad, Dizin numarasını içerir. |
|[Depolamayı dizi ile kopyalama](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Birkaç depolama hesabı dağıtır. Ad, diziden bir değer içerir. |
|[Değişken sayıda veri diskine sahip VM dağıtımı](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Bir sanal makine ile birden fazla veri diski dağıtır. |
|[Değişkenleri kopyalayın](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Değişkenlerde yinelemenin farklı yollarını gösterir. |
|[Çoklu güvenlik kuralları](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Bir ağ güvenlik grubuna birkaç güvenlik kuralı dağıtır. Bir parametreden güvenlik kuralları oluşturur. Parametresi için bkz. [birden çok NSG parametre dosyası](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Sonraki adımlar

* Öğreticiye gitmek için bkz. [öğretici: Kaynak Yöneticisi şablonları kullanarak birden çok kaynak örneği oluşturma](./resource-manager-tutorial-create-multiple-instances.md).

* Bir şablonun bölümleri hakkında daha fazla bilgi edinmek istiyorsanız, bkz. [yazma Azure Resource Manager şablonları](resource-group-authoring-templates.md).
* Şablonunuzu dağıtmayı öğrenmek için bkz. [Azure Resource Manager şablonuyla uygulama dağıtma](resource-group-template-deploy.md).

