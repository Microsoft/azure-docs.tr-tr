---
title: Azure 'da Windows VM 'lerinde güvenlik ilkelerini zorunlu tutun
description: Azure Resource Manager Windows sanal makinesine ilke uygulama
author: mimckitt
manager: vashan
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: 4a4e54510c4683dc1be9da09b96d6289136a26f1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102550272"
---
# <a name="apply-policies-to-windows-vms-with-azure-resource-manager"></a>Azure Resource Manager ile Windows VM 'lerine ilke uygulama
Bir kuruluş, ilkeleri kullanarak kuruluş genelinde çeşitli kuralları ve kuralları zorunlu kılabilir. İstenen davranışın uygulanması, kuruluşun başarısına katkıda bulunmaya karşın riski azaltmaya yardımcı olabilir. Bu makalede, kuruluşunuzun sanal makineleri için istenen davranışı tanımlamak üzere Azure Resource Manager ilkelerini nasıl kullanabileceğinizi betimliyoruz.

İlkelere giriş için bkz. [Azure ilkesi nedir?](../../governance/policy/overview.md).

## <a name="permitted-virtual-machines"></a>İzin verilen sanal makineler
Kuruluşunuzun sanal makinelerinin bir uygulamayla uyumlu olduğundan emin olmak için izin verilen işletim sistemlerini kısıtlayabilirsiniz. Aşağıdaki ilke örneğinde yalnızca Windows Server 2012 R2 Datacenter sanal makinelerinin oluşturulmasını sağlayabilirsiniz:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "in": [
          "Microsoft.Compute/virtualMachines",
          "Microsoft.Compute/VirtualMachineScaleSets"
        ]
      },
      {
        "not": {
          "allOf": [
            {
              "field": "Microsoft.Compute/imagePublisher",
              "in": [
                "MicrosoftWindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageOffer",
              "in": [
                "WindowsServer"
              ]
            },
            {
              "field": "Microsoft.Compute/imageSku",
              "in": [
                "2012-R2-Datacenter"
              ]
            },
            {
              "field": "Microsoft.Compute/imageVersion",
              "in": [
                "latest"
              ]
            }
          ]
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Yukarıdaki ilkeyi herhangi bir Windows Server Datacenter görüntüsüne izin verecek şekilde değiştirmek için bir joker karakter kullanın:

```json
{
  "field": "Microsoft.Compute/imageSku",
  "like": "*Datacenter"
}
```

Herhangi bir Windows Server 2012 R2 Datacenter veya daha yüksek görüntüye izin vermek için yukarıdaki ilkeyi değiştirmek üzere anyOf kullanın:

```json
{
  "anyOf": [
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2012-R2-Datacenter*"
    },
    {
      "field": "Microsoft.Compute/imageSku",
      "like": "2016-Datacenter*"
    }
  ]
}
```

İlke alanları hakkında bilgi için bkz. [ilke diğer adları](../../governance/policy/concepts/definition-structure.md#aliases).

## <a name="managed-disks"></a>Yönetilen diskler

Yönetilen disklerin kullanımını gerektirmek için aşağıdaki ilkeyi kullanın:

```json
{
  "if": {
    "anyOf": [
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/virtualMachines"
          },
          {
            "field": "Microsoft.Compute/virtualMachines/osDisk.uri",
            "exists": true
          }
        ]
      },
      {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Compute/VirtualMachineScaleSets"
          },
          {
            "anyOf": [
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osDisk.vhdContainers",
                "exists": true
              },
              {
                "field": "Microsoft.Compute/VirtualMachineScaleSets/osdisk.imageUrl",
                "exists": true
              }
            ]
          }
        ]
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

## <a name="images-for-virtual-machines"></a>Sanal makineler için görüntüler

Güvenlik nedenleriyle, ortamınızda yalnızca onaylanan özel görüntülerin dağıtılmasını zorunlu kılabilirsiniz. Onaylanan görüntüleri içeren kaynak grubunu ya da onaylanan belirli görüntüleri belirtebilirsiniz.

Aşağıdaki örnek, onaylanan bir kaynak grubundan görüntüleri gerektirir:

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in": [
                    "Microsoft.Compute/virtualMachines",
                    "Microsoft.Compute/VirtualMachineScaleSets"
                ]
            },
            {
                "not": {
                    "field": "Microsoft.Compute/imageId",
                    "contains": "resourceGroups/CustomImage"
                }
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
} 
```

Aşağıdaki örnek, onaylanan görüntü kimliklerini belirtir:

```json
{
    "field": "Microsoft.Compute/imageId",
    "in": ["{imageId1}","{imageId2}"]
}
```

## <a name="virtual-machine-extensions"></a>Sanal makine uzantıları

Belirli uzantı türlerinin kullanımını sağlamak isteyebilirsiniz. Örneğin, bir uzantı belirli özel sanal makine görüntüleriyle uyumlu olmayabilir. Aşağıdaki örnek, belirli bir uzantının nasıl engelleyeceğinizi gösterir. Hangi uzantının engelleneceğini belirleyen yayımcıyı ve türünü kullanır.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "equals": "{extension-type}"

      }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```


## <a name="azure-hybrid-use-benefit"></a>Azure Hibrit Kullanım Teklifi

Şirket içi lisansınız varsa, Lisans ücretini sanal makinelerinize kaydedebilirsiniz. Lisansa sahip olmadığınız zaman, seçeneğini yasaklamalısınız. Aşağıdaki ilke, Azure hibrit kullanım avantajı 'nın (AHUB) yasaklıyor kullanımını kullanır:

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "in":[ "Microsoft.Compute/virtualMachines","Microsoft.Compute/VirtualMachineScaleSets"]
            },
            {
                "field": "Microsoft.Compute/licenseType",
                "exists": true
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="next-steps"></a>Sonraki adımlar
* Bir ilke kuralı tanımladıktan sonra (önceki örneklerde gösterildiği gibi), ilke tanımını oluşturmanız ve bir kapsama atamanız gerekir. Kapsam bir abonelik, kaynak grubu veya kaynak olabilir. İlke atamak için bkz. [kaynak ilkeleri atamak ve yönetmek için Azure Portal kullanma](../../governance/policy/assign-policy-portal.md), [Ilke atamak için PowerShell kullanma](../../governance/policy/assign-policy-powershell.md)veya [Ilke atamak için Azure CLI kullanma](../../governance/policy/assign-policy-azurecli.md).
* Kaynak ilkelerine giriş için bkz. [Azure ilkesi nedir?](../../governance/policy/overview.md).
* Kuruluşların abonelikleri etkili bir şekilde yönetmek için Resource Manager'ı nasıl kullanabileceği hakkında yönergeler için bkz. [Azure kurumsal iskelesi: öngörücü abonelik idaresi](/azure/architecture/cloud-adoption-guide/subscription-governance).
