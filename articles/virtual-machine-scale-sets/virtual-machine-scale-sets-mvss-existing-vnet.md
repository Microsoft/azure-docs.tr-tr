---
title: Azure ölçek kümesi şablonunda var olan bir sanal ağa başvurma
description: Var olan bir Azure sanal makine ölçek kümesi şablonuna nasıl sanal ağ ekleneceğini öğrenin
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: networking
ms.date: 03/30/2021
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: f15fddc54f4b7c5a03843da1bcc11d1991b70d02
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076675"
---
# <a name="reference-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Azure ölçek kümesi şablonunda var olan bir sanal ağa başvurma

Bu makalede, [temel ölçek kümesi şablonunun](virtual-machine-scale-sets-mvss-start.md) yeni bir tane oluşturmak yerine var olan bir sanal ağa dağıtılması için nasıl değiştirileceği gösterilmektedir.

## <a name="prerequisites"></a>Önkoşullar

Önceki bir makalede [temel bir ölçek kümesi şablonu](virtual-machine-scale-sets-mvss-start.md)oluşturduk. Var olan bir sanal ağa ölçek kümesi dağıtan bir şablon oluşturmak için, daha önceki şablona ihtiyacınız olacak.

## <a name="identify-subnet"></a>Alt ağı tanımla

Önce bir parametre ekleyin `subnetId` . Bu dize ölçek kümesi yapılandırmasına geçirilir ve ölçek kümesinin sanal makineleri dağıtmak için önceden oluşturulmuş alt ağı belirlemesine izin verir. Bu dize şu biçimde olmalıdır:

`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`


Örneğin, ölçek kümesini ada `myvnet` , alt ağa, kaynak grubuna ve aboneliğe sahip mevcut bir sanal ağa dağıtmak için `mysubnet` `myrg` `00000000-0000-0000-0000-000000000000` , SubnetID şöyle olacaktır: 

`/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

```diff
      },
      "adminPassword": {
        "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
      }
    },
```

## <a name="delete-extra-virtual-network-resource"></a>Ek sanal ağ kaynağını Sil

Sonra, `resources` var olan bir sanal ağı kullanırken ve yenisini dağıtmanız gerekmiyorsa sanal ağ kaynağını diziden silin.

```diff
    "variables": {},
    "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2018-11-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```
## <a name="remove-dependency-clause"></a>Dependency yan tümcesini kaldır

Şablon dağıtılmadan önce sanal ağ zaten var, bu yüzden `dependsOn` Ölçek kümesinden sanal ağa bir yan tümce belirtmeniz gerekmez. Aşağıdaki satırları silin:

```diff
      {
        "type": "Microsoft.Compute/virtualMachineScaleSets",
        "name": "myScaleSet",
        "location": "[resourceGroup().location]",
        "apiVersion": "2019-03-01",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
        "sku": {
          "name": "Standard_A1",
          "capacity": 2
```

## <a name="pass-subnet-parameter"></a>Alt ağ parametresini geçir

Son olarak, `subnetId` Kullanıcı tarafından ayarlanan parametreyi geçirin ( `resourceId` temel uygun ölçek kümesi şablonunun yaptığı aynı dağıtımda bir VNET 'in kimliğini almak için kullanmak yerine).

```diff
                        "name": "myIpConfig",
                        "properties": {
                          "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                          }
                        }
                      }
```


## <a name="next-steps"></a>Sonraki adımlar

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
