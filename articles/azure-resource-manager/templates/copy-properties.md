---
title: Bir özelliğin birden çok örneğini tanımlama
description: Bir kaynak üzerinde bir özellik oluştururken birden çok kez yinelemek için Azure Resource Manager şablonunda kopyalama işlemi kullanın (ARM şablonu).
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 16c293f1c3aff64aeb8b6cae4b7f1aa14dcd0a77
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480010"
---
# <a name="property-iteration-in-arm-templates"></a>ARM şablonlarındaki Özellik yinelemesi

Bu makalede, Azure Resource Manager şablonunuzda bir özelliğin birden fazla örneğini oluşturma (ARM şablonu) gösterilmektedir. Şablonunuzda bir kaynağın Özellikler bölümüne kopyalama döngüsü ekleyerek, dağıtım sırasında bir özelliğin öğe sayısını dinamik olarak ayarlayabilirsiniz. Ayrıca, şablon söz dizimini yinelemek zorunda kalmaktan kaçının.

Bir özelliğe kopyalama döngüsü uygularken bile, en üst düzey kaynaklarla kopyalama döngüsünü kullanabilirsiniz. Alt kaynağı bir üst düzey kaynakla değiştirme hakkında bilgi edinmek için bkz. [bir alt kaynak Için yineleme](copy-resources.md#iteration-for-a-child-resource).

Ayrıca, [kaynaklar](copy-resources.md), [değişkenler](copy-variables.md)ve [çıktılar](copy-outputs.md)ile kopyalama döngüsünü de kullanabilirsiniz.

## <a name="syntax"></a>Syntax

# <a name="json"></a>[JSON](#tab/json)

`copy`Bir özelliğin öğe sayısını ayarlamak için şablonunuzun kaynaklar bölümüne ekleyin. Copy öğesi aşağıdaki genel biçime sahiptir:

```json
"copy": [
  {
    "name": "<name-of-property>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

İçin `name` , oluşturmak istediğiniz kaynak özelliğinin adını sağlayın.

`count`Özelliği, özelliği için istediğiniz yineleme sayısını belirtir.

`input`Özelliği yinelemek istediğiniz özellikleri belirtir. Özelliğindeki değerden oluşturulan bir dizi öğe oluşturursunuz `input` .

# <a name="bicep"></a>[Bicep](#tab/bicep)

Döngüler şu şekilde birden çok özellik bildirebilir:

- Dizi üzerinde yineleme:

  ```bicep
  <property-name>: [for <item> in <collection>: {
    <properties>
  }]
  ```

- Bir dizinin öğeleri üzerinde yineleme yapma

  ```bicep
  <property-name>: [for (<item>, <index>) in <collection>: {
    <properties>
  }]
  ```

- Döngü dizinini kullanma

  ```bicep
  <property-name>: [for <index> in range(<start>, <stop>): {
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

## <a name="property-iteration"></a>Özellik yineleme

Aşağıdaki örnek, kopyalama döngüsünün `dataDisks` bir sanal makinede özelliğine nasıl uygulanacağını gösterir:

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 3,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty",
                "diskSizeGB": 1023
              }
            }
          ]
        }
        ...
      }
    }
  ]
}
```

`copyIndex`Bir özellik yinelemesi içinde kullanırken, yinelemenin adını belirtmeniz gerektiğini unutmayın. Özellik yinelemesi de bir konum bağımsız değişkenini destekler. Fark, yineleme adından sonra gelmelidir, örneğin `copyIndex('dataDisks', 1)` .

Dağıtılan şablon şu şekilde olur:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

Dizideki her öğe arasında yineleme yapmak için, diziler ile çalışırken kopyalama işlemi faydalıdır. `length`Yineleme sayısını belirtmek ve `copyIndex` dizideki geçerli dizini almak için dizideki işlevini kullanın.

Aşağıdaki örnek şablon, bir dizi olarak geçirilmiş veritabanları için bir yük devretme grubu oluşturur.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "primaryServerName": {
      "type": "string"
    },
    "secondaryServerName": {
      "type": "string"
    },
    "databaseNames": {
      "type": "array",
      "defaultValue": [
        "mydb1",
        "mydb2",
        "mydb3"
      ]
    }
  },
  "variables": {
    "failoverName": "[concat(parameters('primaryServerName'),'/', parameters('primaryServerName'),'failovergroups')]"
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers/failoverGroups",
      "apiVersion": "2015-05-01-preview",
      "name": "[variables('failoverName')]",
      "properties": {
        "readWriteEndpoint": {
          "failoverPolicy": "Automatic",
          "failoverWithDataLossGracePeriodMinutes": 60
        },
        "readOnlyEndpoint": {
          "failoverPolicy": "Disabled"
        },
        "partnerServers": [
          {
            "id": "[resourceId('Microsoft.Sql/servers', parameters('secondaryServerName'))]"
          }
        ],
        "copy": [
          {
            "name": "databases",
            "count": "[length(parameters('databaseNames'))]",
            "input": "[resourceId('Microsoft.Sql/servers/databases', parameters('primaryServerName'), parameters('databaseNames')[copyIndex('databases')])]"
          }
        ]
      }
    }
  ],
  "outputs": {
  }
}
```

`copy`Kaynak için birden fazla özellik belirtebileceğiniz için öğesi bir dizidir.

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "exampleLB",
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@minValue(0)
@maxValue(16)
@description('The number of dataDisks to be returned in the output array.')
param numberOfDataDisks int = 16

resource vmName 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  ...
  properties: {
    storageProfile: {
      ...
      dataDisks: [for i in range(0, numberOfDataDisks): {
        lun: i
        createOption: 'Empty'
        diskSizeGB: 1023
      }]
    }
    ...
  }
}
```

Dağıtılan şablon şu şekilde olur:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2020-06-01",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

---

Kaynak ve özellik yinelemesini birlikte kullanabilirsiniz. Özellik yinelemesine ada göre başvurun.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "apiVersion": "2018-04-01",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource vnetname_resource 'Microsoft.Network/virtualNetworks@2018-04-01' = [for i in range(0, 2): {
  name: concat(vnetname, i)
  location: resourceGroup().location
  properties: {
    addressSpace: {
      addressPrefixes: [
        addressPrefix
      ]
    }
    subnets: [for j in range(0, 2): {
      name: 'subnet-${j}'
      properties: {
        addressPrefix: subnetAddressPrefix[j]
      }
    }]
  }
}]
```

---

## <a name="example-templates"></a>Örnek Şablonlar

Aşağıdaki örnek, bir özellik için birden fazla değer oluşturmak için ortak bir senaryoyu gösterir.

|Şablon  |Açıklama  |
|---------|---------|
|[Değişken sayıda veri diskine sahip VM dağıtımı](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Bir sanal makine ile birden fazla veri diski dağıtır. |

## <a name="next-steps"></a>Sonraki adımlar

- Öğreticiye gitmek için bkz. [öğretici: ARM şablonlarıyla birden çok kaynak örneği oluşturma](template-tutorial-create-multiple-instances.md).
- Kopyalama döngüsünün diğer kullanımları için bkz.:
  - [ARM şablonlarındaki kaynak yinelemesi](copy-resources.md)
  - [ARM şablonlarında değişken yineleme](copy-variables.md)
  - [ARM şablonlarındaki çıkış yinelemesi](copy-outputs.md)
- Bir şablonun bölümleri hakkında bilgi edinmek istiyorsanız, bkz. [ARM şablonlarının yapısını ve sözdizimini anlayın](template-syntax.md).
- Şablonunuzu dağıtmayı öğrenmek için bkz. [ARM şablonlarıyla kaynak dağıtma ve Azure PowerShell](deploy-powershell.md).
