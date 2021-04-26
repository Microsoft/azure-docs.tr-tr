---
title: ARM şablonuyla Azure 'a bir makine ekleme
description: Azure Resource Manager şablonuyla bir makineyi Azure 'a nasıl ekleyeceğinizi öğrenin.
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 04/09/2021
ms.author: alsin
ms.openlocfilehash: 78cf28903311c542a83c9ace4f794e1cdda9a61c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368705"
---
# <a name="onboard-a-machine-to-automanage-with-an-azure-resource-manager-arm-template"></a>Bir Azure Resource Manager (ARM) şablonuyla oto yönetilecek bir makine ekleme


## <a name="overview"></a>Genel Bakış
En Iyi yöntemleri tekrar yönetmek üzere bir makine eklemek için aşağıdaki adımları izleyin. Aşağıdaki ARM şablonu `configurationProfileAssignment` , eklendi için, tekrar yönetilecek bir makineyi temsil eden Azure kaynağı olan bir nesne oluşturur.

## <a name="prerequisites"></a>Önkoşullar
* Var olan bir oto Yönet hesabı oluşturmuş olmanız gerekir. Bu belgeye, oto Yönet hesabı hakkında daha fazla bilgi edinmek ve bir tane oluşturmak için [Bu belgeye](./automanage-account.md) bakın.
* Oto yönetimi için eklemek istediğiniz makineleri içeren kaynak grubunda **katkıda** bulunan rolüne sahip olmanız gerekir

## <a name="arm-template-overview"></a>ARM şablonuna genel bakış
Aşağıdaki ARM şablonu, belirtilen makinenizi Azure oto Yönetimi En Iyi uygulamaları üzerine kullanacaktır. ARM şablonu ve dağıtma adımları hakkındaki ayrıntılar [AŞAĞıDAKI](#arm-template-deployment)ARM şablon dağıtımı bölümünde bulunur.
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "machineName": {
            "type": "String"
        },
        "automanageAccountName": {
            "type": "String"
        },
        "configurationProfileAssignment": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Compute/virtualMachines/providers/configurationProfileAssignments",
            "apiVersion": "2020-06-30-preview",
            "name": "[concat(parameters('machineName'), '/Microsoft.Automanage/', 'default')]",
            "properties": {
                "configurationProfile": "[parameters('configurationProfileAssignment')]",
                "accountId": "[concat(resourceGroup().id, '/providers/Microsoft.Automanage/accounts/', parameters('automanageAccountName'))]"
            }
        }
    ]
}
```

## <a name="arm-template-deployment"></a>ARM şablon dağıtımı
Yukarıdaki ARM şablonu, belirtilen bir bir oto Yönet hesabı kullanarak belirtilen makineniz için bir yapılandırma profili ataması oluşturacak. Bir oto Yönet hesabı oluşturmadıysanız, [Bu belgede](./automanage-account.md)daha fazla bilgi edinin.

`configurationProfileAssignment`Değer aşağıdaki değerlerden biri olabilir:
* Üretiminden
* DevTest

ARM şablonunu dağıtmak için aşağıdaki adımları izleyin:
1. Aşağıdaki ARM şablonunu farklı kaydet `azuredeploy.json`
1. ARM şablonu dağıtımını ile çalıştırma `az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json`
1. İstendiğinde machineName, oto Manageaccountname ve Configurationprofileatama değerlerini sağlayın
1. İşiniz bitti!

Herhangi bir ARM şablonunda olduğu gibi, parametreleri ayrı bir dosyada çarpanlara ayırmak `azuredeploy.parameters.json` ve dağıtım sırasında bir bağımsız değişken olarak kullanmak mümkündür.

## <a name="next-steps"></a>Sonraki adımlar
[Linux](./automanage-linux.md) ve [Windows](./automanage-windows-server.md) için oto yönetimi hakkında daha fazla bilgi edinin