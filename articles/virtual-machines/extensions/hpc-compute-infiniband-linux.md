---
title: InfiniBand Sürücü Uzantısı-Azure Linux VM 'Leri
description: Linux çalıştıran H ve N serisi işlem VM 'lerinde InfiniBand sürücülerini yüklemek için Microsoft Azure uzantısı.
services: virtual-machines
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: amverma
ms.openlocfilehash: 1219f5befb4de81850681a53c10f03dc81249d53
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559741"
---
# <a name="infiniband-driver-extension-for-linux"></a>Linux için InfiniBand sürücü uzantısı

Bu uzantı, InfiniBand ve SR-ıOV-Enabled (' r ' boyutları) [H serisi](../sizes-hpc.md) ve Linux çalıştıran [N serisi](../sizes-gpu.md) VM 'ler üzerinde InfiniBand temelli sürücüler yüklüyor. VM ailesine bağlı olarak, uzantı, Connect-X NIC için uygun sürücüleri yüklenir.

ALıNAN sürücülerin el ile yüklenmesiyle ilgili yönergeler [burada](../workloads/hpc/enable-infiniband.md#manual-installation)bulunabilir.

[Windows VM 'ler](hpc-compute-infiniband-windows.md)için InfiniBand sürücülerini yüklemek için de bir uzantı kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar

### <a name="operating-system"></a>İşletim sistemi

Bu uzantı, belirli işletim sistemi sürümü için sürücü desteğine bağlı olarak aşağıdaki işletim sistemi destekleri 'nı destekler.

| Dağıtım | Sürüm |
|---|---|
| Linux: Ubuntu | 16,04 LTS, 18,04 LTS, 20,04 LTS |
| Linux: CentOS | 7,4, 7,5, 7,6, 7,7, 8,1, 8, 2 |
| Linux: Red Hat Enterprise Linux | 7,4, 7,5, 7,6, 7,7, 8,1, 8, 2 |

### <a name="internet-connectivity"></a>İnternet bağlantısı

InfiniBand sürücülerine yönelik Microsoft Azure uzantısı, hedef VM 'nin internet 'e bağlı ve internet erişimine sahip olmasını gerektirir.

## <a name="extension-schema"></a>Uzantı şeması

Aşağıdaki JSON uzantı için şemayı gösterir.

```json
{
  "name": "<myExtensionName>",
  "type": "extensions",
  "apiVersion": "2015-06-15",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <myVM>)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Özellikler

| Name | Değer/örnek | Veri Türü |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| yayımcı | Microsoft. HpcCompute | string |
| tür | Infinibanddriverlinux | string |
| typeHandlerVersion | 1.1 | int |



## <a name="deployment"></a>Dağıtım


### <a name="azure-resource-manager-template"></a>Azure Resource Manager Şablonu 

Azure VM uzantıları, Azure Resource Manager şablonlarıyla dağıtılabilir. Dağıtım sonrası yapılandırması gerektiren bir veya daha fazla sanal makine dağıtıldığında şablonlar idealdir.

Bir sanal makine uzantısının JSON yapılandırması, sanal makine kaynağının içinde iç içe veya Kaynak Yöneticisi JSON şablonunun kök veya üst düzeyine yerleştirilmiş olabilir. JSON yapılandırmasının yerleştirilmesi, kaynak adının ve türün değerini etkiler. Daha fazla bilgi için bkz. [alt kaynaklar için ad ve tür ayarlama](../../azure-resource-manager/templates/child-resource-name-type.md). 

Aşağıdaki örnek, uzantının sanal makine kaynağının içinde iç içe olduğunu varsayar. Uzantı kaynağını yuvalama sırasında JSON, `"resources": []` sanal makinenin nesnesine yerleştirilir.

```json
{
  "name": "myExtensionName",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', myVM)]"
  ],
  "properties": {
    "publisher": "Microsoft.HpcCompute",
    "type": "InfiniBandDriverLinux",
    "typeHandlerVersion": "1.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="powershell"></a>PowerShell

```powershell
Set-AzVMExtension
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Location "southcentralus" `
    -Publisher "Microsoft.HpcCompute" `
    -ExtensionName "InfiniBandDriverLinux" `
    -ExtensionType "InfiniBandDriverLinux" `
    -TypeHandlerVersion 1.1 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverLinux \
  --publisher Microsoft.HpcCompute \
  --version 1.1 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>Bir sanal makine ölçek kümesine uzantı ekleme

Aşağıdaki örnek, *Myresourcegroup* adlı kaynak grubunda dağıtılan *myvmss* adlı mevcut bir sanal makıne ölçek kümesindeki tüm RDMA özellikli vm 'Lere en son 1,1 ınfinibanddriverlinux uzantısını yükleme:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverLinux" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverLinux" -TypeHandlerVersion "1.1"
  Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "MyVMSS" -VirtualMachineScaleSet $VMSS
  Update-AzVmssInstance -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS" -InstanceId "*"
```


## <a name="troubleshoot-and-support"></a>Sorun giderme ve destek

### <a name="troubleshoot"></a>Sorun giderme

Uzantı dağıtımlarının durumu hakkındaki veriler Azure portal ve Azure PowerShell ve Azure CLı kullanılarak alınabilir. Belirli bir VM için uzantıların dağıtım durumunu görmek için aşağıdaki komutu çalıştırın.

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Uzantı yürütme çıkışı aşağıdaki dosyaya kaydedilir. Yükleme durumunu izlemek ve tüm hatalarda sorun gidermek için bu dosyaya bakın.

```bash
/var/log/azure/ib-vmext-status
```

### <a name="exit-codes"></a>Çıkış kodları

Aşağıdaki tabloda Uzantı yükleme işleminin çıkış kodlarına göre anlamı ve önerilen eylem açıklanmaktadır.

| Çıkış Kodu | Anlamı | Olası eylem |
| :---: | --- | --- |
| 0 | İşlem başarılı |
| 1 | Uzantının yanlış kullanımı | Yürütme çıkış günlüğünü denetle |
| 10 | Hyper-V ve Azure için Linux Tümleştirme Hizmetleri kullanılabilir değil veya yüklü değil | Lspcı çıkışını denetle |
| 11 | Mellanox InfiniBand bu VM boyutunda bulunamadı | Desteklenen bir [VM boyutu ve işletim sistemi](../sizes-hpc.md) kullan |
| 12 | Görüntü teklifi desteklenmiyor |
| 13 | VM boyutu desteklenmiyor | Dağıtmak için InfiniBand özellikli (' r ' boyutu) [H serisi](../sizes-hpc.md) ve [n SERISI](../sizes-gpu.md)n serisi VM kullanın |
| 14 | İşlem başarısız oldu | Yürütme çıkış günlüğünü denetle |


### <a name="support"></a>Destek

Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/community/)Azure uzmanlarıyla iletişim kurun. Alternatif olarak, [Azure destek sitesi](https://azure.microsoft.com/support/options/)aracılığıyla bir destek olayı da oluşturabilirsiniz. Azure desteğini kullanma hakkında daha fazla bilgi için, [Microsoft Azure support SSS](https://azure.microsoft.com/support/faq/)makalesini okuyun.

## <a name="next-steps"></a>Sonraki adımlar
InfiniBand özellikli (' r ' boyutları) hakkında daha fazla bilgi için bkz. [H serisi](../sizes-hpc.md) ve [N serisi](../sizes-gpu.md) VM 'ler.

> [!div class="nextstepaction"]
> [Linux VM uzantıları ve özellikleri hakkında daha fazla bilgi edinin](features-linux.md)