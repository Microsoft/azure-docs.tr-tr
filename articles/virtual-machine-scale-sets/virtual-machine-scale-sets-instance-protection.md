---
title: Azure sanal makine ölçek kümesi örnekleri için örnek koruması
description: Azure sanal makine ölçek kümesi örneklerinin ölçek ve ölçek ayarlama işlemlerinden nasıl korunacağını öğrenin.
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: instance-protection
ms.date: 02/26/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: 292abce3361c000eeeef2c399d5ffa2d2c4852e1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762866"
---
# <a name="instance-protection-for-azure-virtual-machine-scale-set-instances"></a>Azure sanal makine ölçek kümesi örnekleri için örnek koruması

Azure sanal makine ölçek kümeleri, iş yükleriniz için [Otomatik ölçeklendirme](virtual-machine-scale-sets-autoscale-overview.md)aracılığıyla daha iyi esneklik sağlar, böylece altyapınız ne zaman ölçekleyerek ve ölçeklendirilirken yapılandırabilirsiniz. Ölçek Kümeleri Ayrıca, farklı [yükseltme ilkesi](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) ayarları aracılığıyla çok sayıda VM 'yi merkezi olarak yönetmenize, yapılandırmanıza ve güncelleştirmenize olanak tanır. Ölçek kümesi modelinde bir güncelleştirme yapılandırabilirsiniz ve yükseltme ilkesini otomatik veya aşağı olarak ayarladıysanız yeni yapılandırma her ölçek kümesi örneğine otomatik olarak uygulanır.

Uygulamanız trafiği işlerken, belirli örneklerin ölçek kümesi örneğinin geri kalanından farklı şekilde değerlendirilmesini istediğiniz durumlar olabilir. Örneğin, ölçek kümesindeki bazı örnekler uzun süre çalışan işlemler gerçekleştiriyor olabilir ve İşlemler tamamlanana kadar bu örneklerin ölçeklendirilmesini istemezsiniz. Ayrıca, ölçek kümesinin diğer üyelerinden daha fazla veya farklı görevler gerçekleştirmek için ölçek kümesinde birkaç örneğe özelleştirilmiş de sahip olabilirsiniz. Ölçek kümesindeki diğer örneklerle değiştirilmemelidir bu ' özel ' VM 'lerinin olması gerekir. Örnek koruması, uygulamanız için bu ve diğer senaryoları etkinleştirmek üzere ek denetimler sağlar.

Bu makalede, ölçek kümesi örnekleriyle farklı örnek koruma özelliklerini nasıl uygulayabileceğiniz ve kullanabileceğiniz açıklanır.

## <a name="types-of-instance-protection"></a>Örnek koruma türleri
Ölçek kümeleri iki tür örnek koruma özelliği sağlar:

-   **Ölçeklendirmeye karşı koruma**
    - Ölçek kümesi örneğindeki **protectFromScaleIn** özelliği aracılığıyla etkinleştirildi
    - Örneği otomatik ölçeklendirme tarafından başlatılan ölçeğe karşı korur
    - Kullanıcı tarafından başlatılan örnek işlemleri (örnek silme dahil) **engellenmiyor**
    - Ölçek kümesi üzerinde başlatılan işlemler (yükseltme, yeniden görüntü, serbest bırakma vb.) **engellenmiyor**

-   **Ölçek kümesi eylemlerine karşı koruma**
    - Ölçek kümesi örneğindeki **protectFromScaleSetActions** özelliği aracılığıyla etkinleştirildi
    - Örneği otomatik ölçeklendirme tarafından başlatılan ölçeğe karşı korur
    - Ölçek kümesi üzerinde başlatılan işlemden (yükseltme, yeniden görüntü, serbest bırakma vb.) örnek korur
    - Kullanıcı tarafından başlatılan örnek işlemleri (örnek silme dahil) **engellenmiyor**
    - Tam ölçek kümesini silme işlemi **engellenmiyor**

## <a name="protect-from-scale-in"></a>Ölçeklendirmeye karşı koruma
Örnekler oluşturulduktan sonra ölçek kümesi örneklerine örnek koruması uygulanabilir. Koruma, [Ölçek kümesi modelinde](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)değil yalnızca [örnek modelinde](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) uygulanır ve değiştirilir.

Ölçek kümesi örnekleriniz üzerinde ölçek-ın koruması uygulamanın aşağıdaki örneklerde ayrıntılı olarak açıklandığı birçok yolu vardır.

### <a name="azure-portal"></a>Azure portalı

Ölçek kümesindeki bir örneğe Azure portal ile ölçek genişletme koruması uygulayabilirsiniz. Aynı anda birden çok örnek ayarlayamezsiniz. Korumak istediğiniz her örnek için adımları tekrarlayın.
 
1. Var olan bir sanal makine ölçek kümesine gidin.
1. **Ayarlar**' ın altında, sol taraftaki menüden **örnekler** ' i seçin.
1. Korumak istediğiniz örneğin adını seçin.
1. **Koruma ilkesi** sekmesini seçin.
1. **Koruma ilkesi** dikey penceresinde, **ölçeğe karşı koru** seçeneğini belirleyin.
1. **Kaydet**’i seçin. 

### <a name="rest-api"></a>REST API

Aşağıdaki örnek ölçek kümesindeki bir örneğe ölçek genişletme koruması uygular.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true
    }
  }        
}

```

> [!NOTE]
>Örnek koruma yalnızca API sürümü 2019-03-01 ve üzeri sürümlerde desteklenir

### <a name="azure-powershell"></a>Azure PowerShell

Ölçek kümesi örneğinize ölçek genişletme koruması uygulamak için [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) cmdlet 'ini kullanın.

Aşağıdaki örnek, örnek KIMLIĞI 0 olan ölçek kümesindeki bir örneğe ölçeklendirme koruması uygular.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Ölçek kümesi örneğinize ölçek genişletme koruması uygulamak için [az VMSS Update](/cli/azure/vmss#az_vmss_update) kullanın.

Aşağıdaki örnek, örnek KIMLIĞI 0 olan ölçek kümesindeki bir örneğe ölçeklendirme koruması uygular.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true
```

## <a name="protect-from-scale-set-actions"></a>Ölçek kümesi eylemlerine karşı koruma
Örnekler oluşturulduktan sonra ölçek kümesi örneklerine örnek koruması uygulanabilir. Koruma, [Ölçek kümesi modelinde](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-model)değil yalnızca [örnek modelinde](virtual-machine-scale-sets-upgrade-scale-set.md#the-scale-set-vm-model-view) uygulanır ve değiştirilir.

Bir örneği ölçek kümesi eylemlerden korumak aynı zamanda örneği otomatik ölçeklendirme tarafından başlatılan ölçeğe karşı korur.

Ölçek kümesi eylemleri koruması, ölçek kümesi örneklerinizin aşağıdaki örneklerde ayrıntılı olarak açıklandığı şekilde uygulanması için birden çok yol vardır.

### <a name="azure-portal"></a>Azure portalı

Ölçek kümesi eylemleriyle Azure portal, ölçek kümesindeki bir örneğe koruma uygulayabilirsiniz. Aynı anda birden çok örnek ayarlayamezsiniz. Korumak istediğiniz her örnek için adımları tekrarlayın.
 
1. Var olan bir sanal makine ölçek kümesine gidin.
1. **Ayarlar**' ın altında, sol taraftaki menüden **örnekler** ' i seçin.
1. Korumak istediğiniz örneğin adını seçin.
1. **Koruma ilkesi** sekmesini seçin.
1. **Koruma ilkesi** dikey penceresinde **Ölçek kümesini koru eylemleri** seçeneğini belirleyin.
1. **Kaydet**’i seçin. 

### <a name="rest-api"></a>REST API

Aşağıdaki örnek, ölçek kümesi eylemleriyle ölçek kümesindeki bir örneğe koruma uygular.

```
PUT on `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vMScaleSetName}/virtualMachines/{instance-id}?api-version=2019-03-01`
```

```json
{
  "properties": {
    "protectionPolicy": {
      "protectFromScaleIn": true,
      "protectFromScaleSetActions": true
    }
  }        
}

```

> [!NOTE]
>Örnek koruma yalnızca API sürümü 2019-03-01 ve üzeri sürümlerde desteklenir.</br>
Bir örneği ölçek kümesi eylemlerden korumak aynı zamanda örneği otomatik ölçeklendirme tarafından başlatılan ölçeğe karşı korur. "ProtectFromScaleIn" değerini belirtemezsiniz: "protectFromScaleSetActions" ayarlanırken false: true

### <a name="azure-powershell"></a>Azure PowerShell

Ölçek kümesi eylemlerden ölçek kümesi örneğinizi koruma uygulamak için [Update-AzVmssVM](/powershell/module/az.compute/update-azvmssvm) cmdlet 'ini kullanın.

Aşağıdaki örnek, ölçek kümesi eylemlerinin korumasını örnek KIMLIĞI 0 olan ölçek kümesindeki bir örneğe uygular.

```azurepowershell-interactive
Update-AzVmssVM `
  -ResourceGroupName "myResourceGroup" `
  -VMScaleSetName "myVMScaleSet" `
  -InstanceId 0 `
  -ProtectFromScaleIn $true `
  -ProtectFromScaleSetAction $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Ölçek kümesi eylemlerden ölçek kümesi örneğinize koruma uygulamak için [az VMSS Update](/cli/azure/vmss#az_vmss_update) kullanın.

Aşağıdaki örnek, ölçek kümesi eylemlerinin korumasını örnek KIMLIĞI 0 olan ölçek kümesindeki bir örneğe uygular.

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --instance-id 0 \
  --protect-from-scale-in true \
  --protect-from-scale-set-actions true
```

## <a name="troubleshoot"></a>Sorun giderme
### <a name="no-protectionpolicy-on-scale-set-model"></a>Ölçek kümesi modelinde bir protectionPolicy yok
Örnek koruma, ölçek kümesi modelinde değil yalnızca ölçek kümesi örneklerine uygulanabilir.

### <a name="no-protectionpolicy-on-scale-set-instance-model"></a>Ölçek kümesi örnek modelinde bir protectionPolicy yok
Varsayılan olarak, koruma ilkesi oluşturulduğu sırada bir örneğe uygulanmaz.

Örnekler oluşturulduktan sonra ölçek kümesi örneklerine örnek koruması uygulayabilirsiniz.

### <a name="not-able-to-apply-instance-protection"></a>Örnek koruması uygulanamıyor
Örnek koruma yalnızca API sürümü 2019-03-01 ve üzeri sürümlerde desteklenir. Kullanılan API sürümünü denetleyin ve gerektiği şekilde güncelleştirin. PowerShell veya CLı 'nizi en son sürüme de güncelleştirmeniz gerekebilir.

## <a name="next-steps"></a>Sonraki adımlar
Uygulamanızı sanal makine ölçek kümelerinde [dağıtmayı](virtual-machine-scale-sets-deploy-app.md) öğrenin.
