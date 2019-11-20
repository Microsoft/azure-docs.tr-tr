---
title: Şablonlarda alt kaynaklar
description: Bir Azure Resource Manager şablonunda alt kaynaklar için ad ve tür ayarlamayı açıklar.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 2ef20e7c046170a3f73821b878680b46e5a8afea
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147401"
---
# <a name="set-name-and-type-for-child-resources"></a>Alt kaynaklar için ad ve tür ayarla

Alt kaynaklar yalnızca başka bir kaynağın bağlamında bulunan kaynaklardır. Örneğin, sanal makine [Uzantısı](/azure/templates/microsoft.compute/2019-03-01/virtualmachines/extensions) bir [sanal makine](/azure/templates/microsoft.compute/2019-03-01/virtualmachines)olmadan mevcut olamaz. Uzantı kaynağı, sanal makinenin bir alt öğesidir.

Bir kaynak yöneticisi şablonunda, alt kaynağı üst kaynak içinde veya üst kaynağın dışında belirtebilirsiniz. Aşağıdaki örnek, üst kaynağın Resources özelliği içinde yer alan alt kaynağı gösterir.

```json
"resources": [
  {
    <parent-resource>
    "resources": [
      <child-resource>
    ]
  }
]
```

Sonraki örnekte alt kaynak, üst kaynağın dışında gösterilmektedir. Üst kaynak aynı şablonda dağıtılmamışsa veya birden fazla alt kaynak oluşturmak için [kopyalama](resource-group-create-multiple.md) kullanmak istiyorsanız bu yaklaşımı kullanabilirsiniz.

```json
"resources": [
  {
    <parent-resource>
  },
  {
    <child-resource>
  }
]
```

Kaynak adı ve türü için sağladığınız değerler, alt kaynağın üst kaynağın içinde mi yoksa dışında mı tanımlandığına göre değişir.

## <a name="within-parent-resource"></a>Üst kaynak içinde

Üst kaynak türü içinde tanımlandığında, tür ve ad değerlerini eğik çizgiler olmadan tek bir sözcük olarak biçimlendirin.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

Aşağıdaki örnekte bir sanal ağ ve bir alt ağ gösterilmektedir. Alt ağın, sanal ağ için kaynaklar dizisine dahil edildiğini unutmayın. Ad **Subnet1** olarak ayarlanır ve tür **alt ağlar**olarak ayarlanır. Alt kaynak dağıtılmadan önce üst kaynağın mevcut olması gerektiğinden, alt kaynak üst kaynağa bağımlı olarak işaretlenir.

```json
"resources": [
  {
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    },
    "resources": [
      {
        "apiVersion": "2018-10-01",
        "type": "subnets",
        "location": "[parameters('location')]",
        "name": "Subnet1",
        "dependsOn": [
          "VNet1"
        ],
        "properties": {
          "addressPrefix": "10.0.0.0/24"
        }
      }
    ]
  }
]
```

Tam kaynak türü hala **Microsoft. Network/virtualNetworks/altağlardır**. **Microsoft. Network/virtualNetworks** , üst kaynak türünden olduğu için sağlamamazsınız.

Alt kaynak adı **Subnet1** olarak ayarlanır ancak tam ad üst adı içerir. Üst kaynaktan varsaydığı için **VNet1** sağlamamazsınız.

## <a name="outside-parent-resource"></a>Üst kaynak dışında

Üst kaynağın dışında tanımlandığında, üst tür ve adı içerecek şekilde türü ve eğik çizgileri biçimlendirin.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

Aşağıdaki örnekte, kök düzeyinde tanımlanan bir sanal ağ ve alt ağ gösterilmektedir. Alt ağın, sanal ağ için kaynaklar dizisine dahil edilmediğini unutmayın. Ad **VNet1/Subnet1** olarak ayarlanır ve tür **Microsoft. Network/virtualnetworks/alt ağları**olarak ayarlanır. Alt kaynak dağıtılmadan önce üst kaynağın mevcut olması gerektiğinden, alt kaynak üst kaynağa bağımlı olarak işaretlenir.

```json
"resources": [
  {
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    }
  },
  {
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "location": "[parameters('location')]",
    "name": "VNet1/Subnet1",
    "dependsOn": [
      "VNet1"
    ],
    "properties": {
      "addressPrefix": "10.0.0.0/24"
    }
  }
]
```

## <a name="next-steps"></a>Sonraki adımlar

* Azure Resource Manager şablonları oluşturma hakkında bilgi edinmek için bkz. [yazma şablonları](resource-group-authoring-templates.md). 

* Kaynağa başvururken kaynak adının biçimi hakkında bilgi edinmek için [başvuru işlevine](resource-group-template-functions-resource.md#reference)bakın.
