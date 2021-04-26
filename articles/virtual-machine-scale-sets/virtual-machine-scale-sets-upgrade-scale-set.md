---
title: Azure sanal makine ölçek kümesini değiştirme
description: REST API 'Leri, Azure PowerShell ve Azure CLı ile Azure sanal makine ölçek kümesini değiştirme ve güncelleştirme hakkında bilgi edinin
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 03/10/2020
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 45c52e141ed2d93f89c9d7c1426a9d7cbbbd47f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023937"
---
# <a name="modify-a-virtual-machine-scale-set"></a>Sanal makine ölçek kümesini değiştirme

Uygulamalarınızın yaşam döngüsü boyunca, sanal makine ölçek kümesini değiştirmeniz veya güncelleştirmeniz gerekebilir. Bu güncelleştirmeler, ölçek kümesinin yapılandırmasını güncelleştirme veya uygulama yapılandırmasını değiştirme içerebilir. Bu makalede, mevcut bir ölçek kümesinin REST API 'Ler, Azure PowerShell veya Azure CLı ile nasıl değiştirileceği açıklanır.

## <a name="fundamental-concepts"></a>Temel kavramlar

### <a name="the-scale-set-model"></a>Ölçek kümesi modeli
Ölçek kümesinin, bir bütün olarak ölçek kümesinin *istenen* durumunu yakalayan bir "ölçek kümesi modeli" vardır. Bir ölçek kümesinin modelini sorgulamak için, 

- [İşlem/virtualmachinescalesets/Get](/rest/api/compute/virtualmachinescalesets/get) ile aşağıdaki gibi REST API:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- [Get-AzVmss](/powershell/module/az.compute/get-azvmss)ile Azure PowerShell:

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
    ```

- [Az VMSS](/cli/azure/vmss)Ile Azure CLI şunu göster:

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet
    ```

- Ayrıca, [Resources.Azure.com](https://resources.azure.com) veya dile özgü [Azure SDK](https://azure.microsoft.com/downloads/)'larını de kullanabilirsiniz.

Çıktının tam sunumu, komuta verdiğiniz seçeneklere bağlıdır. Aşağıdaki örnek, Azure CLı 'nın sıkıştırılmış örnek çıktısını gösterir:

```azurecli
az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "overprovision": true,
  "plan": null,
  "singlePlacementGroup": true,
  "sku": {
    "additionalProperties": {},
    "capacity": 1,
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Bu özellikler, ölçek kümesi için bir bütün olarak geçerlidir.


### <a name="the-scale-set-instance-view"></a>Ölçek kümesi örnek görünümü
Ölçek kümesinde Ayrıca, ölçek kümesinin geçerli *çalışma zamanı* durumunu bir bütün olarak yakalayan bir "ölçek kümesi örnek görünümü" vardır. Ölçek kümesinin örnek görünümünü sorgulamak için şunu kullanabilirsiniz:

- [İşlem/virtualmachinescalesets/getınstanceview](/rest/api/compute/virtualmachinescalesets/getinstanceview) ile aşağıdaki gibi REST API:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/instanceView?api-version={apiVersion}
    ```

- [Get-AzVmss](/powershell/module/az.compute/get-azvmss)ile Azure PowerShell:

    ```powershell
    Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceView
    ```

- [Az VMSS Get-Instance-View](/cli/azure/vmss)Ile Azure CLI:

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
    ```

- Ayrıca, [Resources.Azure.com](https://resources.azure.com) veya dile özgü [Azure SDK](https://azure.microsoft.com/downloads/) 'larını de kullanabilirsiniz

Çıktının tam sunumu, komuta verdiğiniz seçeneklere bağlıdır. Aşağıdaki örnek, Azure CLı 'nın sıkıştırılmış örnek çıktısını gösterir:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet
{
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "level": "Info",
      "message": null,
      "time": "{time}"
    }
  ],
  "virtualMachine": {
    "additionalProperties": {},
    "statusesSummary": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "count": 1
      }
    ]
  }
}
```

Bu özellikler, ölçek kümesine uygulanan uzantıların durumu gibi ölçek kümesindeki VM 'lerin geçerli çalışma zamanı durumunun bir özetini sağlar.


### <a name="the-scale-set-vm-model-view"></a>Ölçek kümesi VM model görünümü
Ölçek kümesinin model görünümüne benzer şekilde, ölçek kümesindeki her sanal makine örneğinin kendi model görünümü vardır. Ölçek kümesindeki belirli bir sanal makine örneği için model görünümünü sorgulamak için şunu kullanabilirsiniz:

- [İşlem/virtualmachinescalesetvms/Get](/rest/api/compute/virtualmachinescalesetvms/get) ile aşağıdaki gibi REST API:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId?api-version={apiVersion}
    ```

- [Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm)ile Azure PowerShell:

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- [Az VMSS](/cli/azure/vmss)Ile Azure CLI şunu göster:

    ```azurecli
    az vmss show --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Ayrıca [Resources.Azure.com](https://resources.azure.com) veya [Azure SDK](https://azure.microsoft.com/downloads/)'larını de kullanabilirsiniz.

Çıktının tam sunumu, komuta verdiğiniz seçeneklere bağlıdır. Aşağıdaki örnek, Azure CLı 'nın sıkıştırılmış örnek çıktısını gösterir:

```azurecli
$ az vmss show --resource-group myResourceGroup --name myScaleSet
{
  "location": "westus",
  "name": "{name}",
  "sku": {
    "name": "Standard_D2_v2",
    "tier": "Standard"
  },
}
```

Bu özellikler, ölçek kümesi içindeki bir sanal makine örneğinin yapılandırmasını bir bütün olarak değil, bir ölçek kümesi içinde tanımlıyor. Örneğin, ölçek kümesi modelinin `overprovision` bir özelliği vardır, ancak bir ölçek kümesi içindeki bir sanal makine örneği için model değildir. Bu fark, aşırı sağlamanın ölçek kümesindeki tek tek VM örnekleri değil, bir bütün olarak ölçek kümesi için bir özellik olmasından kaynaklanır (aşırı sağlama hakkında daha fazla bilgi için bkz. [Ölçek Kümeleri Için tasarım konuları](virtual-machine-scale-sets-design-overview.md#overprovisioning)).


### <a name="the-scale-set-vm-instance-view"></a>Ölçek kümesi VM örneği görünümü
Ölçek kümesinin örnek görünümüne benzer şekilde, ölçek kümesindeki her sanal makine örneğinin kendi örnek görünümü vardır. Ölçek kümesi içindeki belirli bir sanal makine örneği için örnek görünümünü sorgulamak için şunu kullanabilirsiniz:

- [İşlem/virtualmachinescalesetvms/getınstanceview](/rest/api/compute/virtualmachinescalesetvms/getinstanceview) ile aşağıdaki gibi REST API:

    ```rest
    GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualmachines/instanceId/instanceView?api-version={apiVersion}
    ```

- [Get-AzVmssVm](/powershell/module/az.compute/get-azvmssvm)ile Azure PowerShell:

    ```powershell
    Get-AzVmssVm -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -InstanceView
    ```

- [Az VMSS Get-Instance-View](/cli/azure/vmss) Ile Azure CLI

    ```azurecli
    az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- [Resources.Azure.com](https://resources.azure.com) veya [Azure SDK](https://azure.microsoft.com/downloads/) 'larını da kullanabilirsiniz

Çıktının tam sunumu, komuta verdiğiniz seçeneklere bağlıdır. Aşağıdaki örnek, Azure CLı 'nın sıkıştırılmış örnek çıktısını gösterir:

```azurecli
$ az vmss get-instance-view --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
{
  "additionalProperties": {
    "osName": "ubuntu",
    "osVersion": "16.04"
  },
  "disks": [
    {
      "name": "{name}",
      "statuses": [
        {
          "additionalProperties": {},
          "code": "ProvisioningState/succeeded",
          "displayStatus": "Provisioning succeeded",
          "time": "{time}"
        }
      ]
    }
  ],
  "statuses": [
    {
      "additionalProperties": {},
      "code": "ProvisioningState/succeeded",
      "displayStatus": "Provisioning succeeded",
      "time": "{time}"
    },
    {
      "additionalProperties": {},
      "code": "PowerState/running",
      "displayStatus": "VM running"
    }
  ],
  "vmAgent": {
    "statuses": [
      {
        "additionalProperties": {},
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Ready",
        "level": "Info",
        "message": "Guest Agent is running",
        "time": "{time}"
      }
    ],
    "vmAgentVersion": "{version}"
  },
}
```

Bu özellikler, ölçek kümesine uygulanan tüm uzantıları içeren bir ölçek kümesi içindeki bir VM örneğinin geçerli çalışma zamanı durumunu anlatmaktadır.


## <a name="how-to-update-global-scale-set-properties"></a>Küresel ölçek kümesi özelliklerini güncelleştirme
Küresel ölçek kümesi özelliğini güncelleştirmek için ölçek kümesi modelinde özelliği güncelleştirmeniz gerekir. Bu güncelleştirmeyi kullanarak şunları yapabilirsiniz:

- [İşlem/virtualmachinescalesets/CreateOrUpdate](/rest/api/compute/virtualmachinescalesets/createorupdate) ile aşağıdaki gibi REST API:

    ```rest
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version={apiVersion}
    ```

- Genel ölçek kümesi özelliklerini güncelleştirmek için REST API özelliklerle Kaynak Yöneticisi şablonu dağıtabilirsiniz.

- Güncelleştirme ile Azure PowerShell [-AzVmss](/powershell/module/az.compute/update-azvmss):

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet {scaleSetConfigPowershellObject}
    ```

- [Az VMSS Update](/cli/azure/vmss)Ile Azure CLI:
    - Bir özelliği değiştirmek için:

        ```azurecli
        az vmss update --set {propertyPath}={value}
        ```

    - Bir ölçek kümesindeki List özelliğine bir nesne eklemek için: 

        ```azurecli
        az vmss update --add {propertyPath} {JSONObjectToAdd}
        ```

    - Ölçek kümesindeki bir List özelliğinden bir nesne kaldırmak için: 

        ```azurecli
        az vmss update --remove {propertyPath} {indexToRemove}
        ```

    - Ölçek kümesini daha önce `az vmss create` komutuyla dağıttıysanız, `az vmss create` Ölçek kümesini güncelleştirmek için komutu yeniden çalıştırabilirsiniz. Komutta tüm özelliklerin `az vmss create` , değiştirmek istediğiniz özellikler dışında, önceki ile aynı olduğundan emin olun.

- Ayrıca [Resources.Azure.com](https://resources.azure.com) veya [Azure SDK](https://azure.microsoft.com/downloads/)'larını de kullanabilirsiniz.

Ölçek kümesi modeli güncelleştirildikten sonra yeni yapılandırma, ölçek kümesinde oluşturulan tüm yeni VM 'lere uygulanır. Ancak, ölçek kümesindeki mevcut VM 'Lerin modelleri hala en son genel ölçek kümesi modeliyle güncel hale getirilmelidir. Her VM için modelinde, `latestModelApplied` sanal makinenin en son genel ölçek kümesi modeliyle güncel olup olmadığını belirten adlı bir Boole özelliği ( `true` VM 'nin en son model ile güncel olduğu anlamına gelir).


## <a name="how-to-bring-vms-up-to-date-with-the-latest-scale-set-model"></a>En son ölçek kümesi modeliyle VM 'Leri güncel hale getirme
Ölçek kümelerinde, sanal makinelerin en son ölçek kümesi modeliyle güncel hale getirilme biçimini belirleyen bir "yükseltme ilkesi" bulunur. Yükseltme ilkesi için üç mod şunlardır:

- **Otomatik** -bu modda, ölçek kümesi, açılan VM 'lerin sırası hakkında hiçbir garanti vermez. Ölçek kümesi tüm VM 'Leri aynı anda alabilir. 
-  Bu modda, ölçek kümesi, toplu işler arasında isteğe bağlı bir duraklatma süresi ile güncelleştirmeyi toplu olarak yapar.
- **El ile** -bu modda, ölçek kümesi modelini güncelleştirdiğinizde mevcut VM 'lere hiçbir şey olmaz.
 
Mevcut VM 'Leri güncelleştirmek için, var olan her VM 'nin bir "el ile yükseltme" yapmanız gerekir. Bu el ile yükseltmeyi şu şekilde yapabilirsiniz:

- [İşlem/virtualmachinescalesets/updateınstances](/rest/api/compute/virtualmachinescalesets/updateinstances) ile aşağıdaki gibi REST API:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/manualupgrade?api-version={apiVersion}
    ```

- [Update-Azvmssınstance](/powershell/module/az.compute/update-azvmssinstance)ile Azure PowerShell:
    
    ```powershell
    Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId
    ```

- [Az VMSS Update-Instances](/cli/azure/vmss) Ile Azure CLI

    ```azurecli
    az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
    ```

- Dile özgü [Azure SDK](https://azure.microsoft.com/downloads/)'larını da kullanabilirsiniz.

>[!NOTE]
> Service Fabric kümeler yalnızca *Otomatik* modu kullanabilir, ancak güncelleştirme farklı şekilde işlenir. Daha fazla bilgi için bkz. [uygulama yükseltmeleri Service Fabric](../service-fabric/service-fabric-application-upgrade.md).

Küresel ölçek kümesi özelliklerinde, yükseltme ilkesini takip eden bir değişiklik türü vardır. Ölçek kümesi işletim sistemi ve veri diski profilindeki (Yönetici Kullanıcı adı ve parola gibi) değişiklikler yalnızca API sürüm *2017-12-01* veya üzeri sürümlerde değiştirilebilir. Bu değişiklikler yalnızca ölçek kümesi modelindeki değişiklikten sonra oluşturulan VM 'Ler için geçerlidir. Mevcut VM 'Leri güncel hale getirmek için, var olan her VM 'nin "ReImage" öğesini yapmanız gerekir. Bu yeniden görüntüsünü kullanarak yapabilirsiniz:

- [İşlem/virtualmachinescalesets/yeniden görüntüyle](/rest/api/compute/virtualmachinescalesets/reimage) aşağıdaki şekilde REST API:

    ```rest
    POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/reimage?api-version={apiVersion}
    ```

- [Set-AzVmssVm](/powershell/module/az.compute/set-azvmssvm)ile Azure PowerShell:

    ```powershell
    Set-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId instanceId -Reimage
    ```

- [Az VMSS ReImage](/cli/azure/vmss)Ile Azure CLI:

    ```azurecli
    az vmss reimage --resource-group myResourceGroup --name myScaleSet --instance-id instanceId
    ```

- Dile özgü [Azure SDK](https://azure.microsoft.com/downloads/)'larını da kullanabilirsiniz.


## <a name="properties-with-restrictions-on-modification"></a>Değişiklik kısıtlamaları olan özellikler

### <a name="create-time-properties"></a>Oluşturma zamanı özellikleri
Bazı özellikler yalnızca ölçek kümesini oluşturduğunuzda ayarlanabilir. Bu özellikler şunları içerir:

- Kullanılabilirlik Alanları
- Görüntü başvuru yayımcısı
- Görüntü başvuru teklifi
- Yönetilen işletim sistemi disk depolama hesabı türü

### <a name="properties-that-can-only-be-changed-based-on-the-current-value"></a>Yalnızca geçerli değere göre değiştirilebilen Özellikler
Bazı özellikler, geçerli değere bağlı olarak özel durumlarla birlikte değişebilir. Bu özellikler şunları içerir:

- **singleplacementgroup** -singleplacementgroup true ise, false olarak değiştirilebilir. Ancak, singlePlacementGroup false ise true **olarak değiştirilemez.**
- **alt ağ** -bir ölçek kümesinin alt ağı, özgün alt ağ ve yeni alt ağ aynı sanal ağ içinde olduğu sürece değiştirilebilir.
- **ımagereferencesku** -Image Reference SKU 'su, [plan bilgileri](../virtual-machines/linux/cli-ps-findimage.md#check-the-purchase-plan-information)olmayan, desteklenen [Linux Distro 'lara](../virtual-machines/linux/endorsed-distros.md), Windows Server/istemci görüntüleri ve görüntüleri için güncelleştirilemeyebilir. 

### <a name="properties-that-require-deallocation-to-change"></a>Değişikliğe göre ayırmayı gerektiren özellikler
Ölçek kümesindeki VM 'Ler serbest bırakıldığında bazı özellikler yalnızca belirli değerlere değiştirilebilir. Bu özellikler şunları içerir:

- **SKU adı**-yenı VM SKU 'su ölçek kümesi şu anda açık olan donanımda DESTEKLENMIYORSA, SKU adını değiştirmeden önce ölçek kümesindeki VM 'leri serbest getirmeniz gerekir. Daha fazla bilgi için bkz. [Azure VM 'yi yeniden boyutlandırma](../virtual-machines/windows/resize-vm.md). 

## <a name="vm-specific-updates"></a>VM 'ye özgü güncelleştirmeler
Belirli değişiklikler, küresel ölçek kümesi özellikleri yerine belirli sanal makinelere uygulanabilir. Şu anda desteklenen tek VM 'ye özgü güncelleştirme, ölçek kümesindeki VM 'lere/sanal makinelere veri diskleri eklemek/ayırmak içindir. Bu özellik önizlemede. Daha fazla bilgi için bkz. [Önizleme belgeleri](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk).


## <a name="scenarios"></a>Senaryolar

### <a name="application-updates"></a>Uygulama güncelleştirmeleri
Bir uygulama, uzantılar aracılığıyla bir ölçek kümesine dağıtılırsa, uzantı yapılandırmasına yönelik bir güncelleştirme, uygulamanın yükseltme ilkesine uygun olarak güncelleştirilmesini sağlar. Örneğin, özel bir betik uzantısında çalıştırmak için bir betiğin yeni bir sürümüne sahipseniz, *Fileuris* özelliğini yeni betiğe işaret etmek üzere güncelleştirebilirsiniz. Bazı durumlarda, uzantı yapılandırması değişmese de bir güncelleştirmeye zorlamak isteyebilirsiniz (örneğin, betiği, betiğin URI 'sine değişiklik yapmadan güncelleştirdiniz). Bu durumlarda, bir güncelleştirmeyi zorlamak için *Forceupdatetag* ' i değiştirebilirsiniz. Azure platformu bu özelliği yorumlamaz. Değeri değiştirirseniz, uzantının nasıl çalıştığı üzerinde hiçbir etkisi olmaz. Bir değişiklik, uzantıyı yeniden çalışmaya zorlar. *Forceupdatetag* hakkında daha fazla bilgi için bkz. [Uzantılar için REST API belgeleri](/rest/api/compute/virtualmachineextensions/createorupdate). *Forceupdatetag* 'in yalnızca özel Betik uzantısı değil tüm uzantılarla kullanılabileceğini unutmayın.

Uygulamaların özel bir görüntü ile dağıtılması da yaygındır. Bu senaryo aşağıdaki bölümde ele alınmıştır.

### <a name="os-updates"></a>İşletim sistemi güncelleştirmeleri
Azure platform görüntülerini kullanıyorsanız, *ImageReference* 'ı değiştirerek görüntüyü güncelleştirebilirsiniz (daha fazla bilgi, [REST API belgelerine](/rest/api/compute/virtualmachinescalesets/createorupdate)bakın).

>[!NOTE]
> Platform görüntüleriyle birlikte, görüntü başvuru sürümü için "en son" belirtmek yaygın bir sürümdür. Oluşturduğunuz, ölçeklendirilen ve yeniden görüntü oluşturduğunuzda, VM 'Ler kullanılabilir en son sürümle oluşturulur. Ancak, yeni görüntü sürümleri serbest bırakıldığı için işletim sistemi görüntüsünün zaman içinde otomatik olarak **güncelleştirildiği anlamına gelmez** . Otomatik işletim sistemi yükseltmeleri sağlayan, şu anda önizleme aşamasında olan ayrı bir özellik vardır. Daha fazla bilgi için [Otomatik işletim sistemi yükseltmeleri belgelerine](virtual-machine-scale-sets-automatic-upgrade.md)bakın.

Özel görüntüler kullanıyorsanız, *ImageReference* kimliğini güncelleştirerek görüntüyü güncelleştirebilirsiniz (daha fazla bilgi için [REST API belgelerine](/rest/api/compute/virtualmachinescalesets/createorupdate)bakın).

## <a name="examples"></a>Örnekler

### <a name="update-the-os-image-for-your-scale-set"></a>Ölçek kümesi için işletim sistemi görüntüsünü güncelleştirme
Ubuntu LTS 16,04 ' nin eski bir sürümünü çalıştıran bir ölçek kümesine sahip olabilirsiniz. Yeni bir Ubuntu LTS 16,04 sürümüne (sürüm *16.04.201801090* gibi) güncelleştirmek istiyorsunuz. Görüntü başvurusu sürümü özelliği bir listenin parçası değildir, bu nedenle bu özellikleri doğrudan aşağıdaki komutlardan biriyle değiştirebilirsiniz:

- Aşağıdaki gibi [Update-AzVmss](/powershell/module/az.compute/update-azvmss) ile Azure PowerShell:

    ```powershell
    Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -ImageReferenceVersion 16.04.201801090
    ```

- [Az VMSS Update](/cli/azure/vmss)Ile Azure CLI:

    ```azurecli
    az vmss update --resource-group myResourceGroup --name myScaleSet --set virtualMachineProfile.storageProfile.imageReference.version=16.04.201801090
    ```

Alternatif olarak, ölçek kümesinin kullandığı görüntüyü değiştirmek isteyebilirsiniz. Örneğin, ölçek kümesi tarafından kullanılan özel bir görüntüyü güncelleştirmek veya değiştirmek isteyebilirsiniz. Görüntü başvuru KIMLIĞI özelliğini güncelleştirerek ölçek kümesinin kullandığı görüntüyü değiştirebilirsiniz. Görüntü başvuru KIMLIĞI özelliği bir listenin parçası olmadığından, bu özelliği doğrudan aşağıdaki komutlardan biriyle değiştirebilirsiniz:

- Aşağıdaki gibi [Update-AzVmss](/powershell/module/az.compute/update-azvmss) ile Azure PowerShell:

    ```powershell
    Update-AzVmss `
        -ResourceGroupName "myResourceGroup" `
        -VMScaleSetName "myScaleSet" `
        -ImageReferenceId /subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```

- [Az VMSS Update](/cli/azure/vmss)Ile Azure CLI:

    ```azurecli
    az vmss update \
        --resource-group myResourceGroup \
        --name myScaleSet \
        --set virtualMachineProfile.storageProfile.imageReference.id=/subscriptions/{subscriptionID}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myNewImage
    ```


### <a name="update-the-load-balancer-for-your-scale-set"></a>Ölçek kümesi için yük dengeleyiciyi güncelleştirme
Azure Load Balancer olan bir ölçek kümesine sahip olduğunu ve Azure Load Balancer bir Azure Application Gateway ile değiştirmek istediğinizi varsayalım. Bir ölçek kümesinin yük dengeleyici ve Application Gateway özellikleri bir listenin parçasıdır. bu nedenle, özellikleri doğrudan değiştirmek yerine liste öğelerini kaldırmak veya eklemek için komutları kullanabilirsiniz:

- Azure PowerShell:

    ```powershell
    # Get the current model of the scale set and store it in a local PowerShell object named $vmss
    $vmss=Get-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet"
    
    # Create a local PowerShell object for the new desired IP configuration, which includes the reference to the application gateway
    $ipconf = New-AzVmssIPConfig "myNic" -ApplicationGatewayBackendAddressPoolsId /subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendAddressPoolName} -SubnetId $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Subnet.Id –Name $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0].Name
    
    # Replace the existing IP configuration in the local PowerShell object (which contains the references to the current Azure Load Balancer) with the new IP configuration
    $vmss.VirtualMachineProfile.NetworkProfile.NetworkInterfaceConfigurations[0].IpConfigurations[0] = $ipconf
    
    # Update the model of the scale set with the new configuration in the local PowerShell object
    Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -virtualMachineScaleSet $vmss
    ```

- Azure CLı:

    ```azurecli
    # Remove the load balancer backend pool from the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools 0
    
    # Remove the load balancer backend pool from the scale set model; only necessary if you have NAT pools configured on the scale set
    az vmss update --resource-group myResourceGroup --name myScaleSet --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools 0
    
    # Add the application gateway backend pool to the scale set model
    az vmss update --resource-group myResourceGroup --name myScaleSet --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].ApplicationGatewayBackendAddressPools '{"id": "/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/{applicationGatewayBackendPoolName}"}'
    ```

>[!NOTE]
> Bu komutlar, ölçek kümesinde yalnızca bir IP yapılandırması ve yük dengeleyici olduğunu varsayar. Birden çok varsa, *0* dışında bir liste dizini kullanmanız gerekebilir.


## <a name="next-steps"></a>Sonraki adımlar
Ayrıca, [Azure CLI](virtual-machine-scale-sets-manage-cli.md) veya [Azure PowerShell](virtual-machine-scale-sets-manage-powershell.md)ölçek kümelerinde ortak yönetim görevleri gerçekleştirebilirsiniz.