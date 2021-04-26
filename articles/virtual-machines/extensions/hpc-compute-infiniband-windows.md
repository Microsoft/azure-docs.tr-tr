---
title: InfiniBand Sürücü Uzantısı-Azure Windows VM 'Leri
description: Windows çalıştıran H ve N serisi işlem VM 'lerinde InfiniBand sürücülerini yüklemek için Microsoft Azure uzantısı.
services: virtual-machines
documentationcenter: ''
author: vermagit
editor: ''
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: amverma
ms.openlocfilehash: 81720c18ce858cd5667413a9d39afdadc95acb23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559707"
---
# <a name="infiniband-driver-extension-for-windows"></a>Windows için InfiniBand sürücü uzantısı

Bu uzantı, InfiniBand ND sürücülerini (SR-ıOV etkin olmayan) ve Ed sürücüleri (SR-ıOV-etkinleştirilmiş) (' r ' boyutları) [H serisi](../sizes-hpc.md) ve Windows çalıştıran [N serisi](../sizes-gpu.md) VM 'leri yüklüyor. VM ailesine bağlı olarak, uzantı, Connect-X NIC için uygun sürücüleri yüklenir.

[Linux VM 'ler](hpc-compute-infiniband-linux.md)için InfiniBand sürücülerini yüklemek için de bir uzantı kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar

### <a name="operating-system"></a>İşletim sistemi

Bu uzantı, belirli işletim sistemi sürümü için sürücü desteğine bağlı olarak aşağıdaki işletim sistemi destekleri 'nı destekler. H ve N serisi sanal makine boyutları için uygun InfiniBand NIC ' i aklınızda edin.

| Dağıtım | InfiniBand NIC sürücüleri |
|---|---|
| Windows 10 | CX5, CX6 |
| Windows Server 2019 | CX5, CX6 |
| Windows Server 2016 | CX3-Pro, CX5, CX6 |
| Windows Server 2012 R2 | CX3-Pro, CX5, CX6 |
| Windows Server 2012 | CX3-Pro, CX5, CX6 |

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
    "type": "InfiniBandDriverWindows",
    "typeHandlerVersion": "1.2",
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
| tür | Infinibanddriverwindows | string |
| typeHandlerVersion | 1.2 | int |



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
    "type": "InfiniBandDriverWindows",
    "typeHandlerVersion": "1.2",
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
    -ExtensionName "InfiniBandDriverWindows" `
    -ExtensionType "InfiniBandDriverWindows" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name InfiniBandDriverWindows \
  --publisher Microsoft.HpcCompute \
  --version 1.2 
```

### <a name="add-extension-to-a-virtual-machine-scale-set"></a>Bir sanal makine ölçek kümesine uzantı ekleme

Aşağıdaki örnek, *Myresourcegroup* adlı kaynak grubunda dağıtılan *myvmss* adlı mevcut bir sanal makıne ölçek kümesindeki tüm RDMA özellikli vm 'Lere en son 1,2 ınfinibanddriverwindows uzantısını yükleme:

  ```powershell
  $VMSS = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myVMSS"
  Add-AzVmssExtension -VirtualMachineScaleSet $VMSS -Name "InfiniBandDriverWindows" -Publisher "Microsoft.HpcCompute" -Type "InfiniBandDriverWindows" -TypeHandlerVersion "1.2"
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

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.InfiniBandDriverWindows\
```

### <a name="exit-codes"></a>Çıkış kodları

Aşağıdaki tabloda Uzantı yükleme işleminin çıkış kodlarına göre anlamı ve önerilen eylem açıklanmaktadır.

| Hata Kodu | Anlamı | Olası eylem |
| :---: | --- | --- |
| 0 | İşlem başarılı |
| 3010 | İşlem başarılı oldu. Yeniden başlatma gerekiyor. |
| 100 | İşlem desteklenmiyor veya tamamlanamadı. | Olası nedenler: PowerShell sürümü desteklenmiyor, VM boyutu InfiniBand özellikli bir VM değil, verileri indirirken hata oluştu. Hatanın nedenini öğrenmek için günlük dosyalarını denetleyin. |
| 240, 840 | İşlem zaman aşımı. | İşlemi yeniden deneyin. |
| -1 | Özel durum oluştu. | Özel durumun nedenini öğrenmek için günlük dosyalarını denetleyin. |

### <a name="support"></a>Destek

Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/community/)Azure uzmanlarıyla iletişim kurun. Alternatif olarak, [Azure destek sitesi](https://azure.microsoft.com/support/options/)aracılığıyla bir destek olayı da oluşturabilirsiniz. Azure desteğini kullanma hakkında daha fazla bilgi için, [Microsoft Azure support SSS](https://azure.microsoft.com/support/faq/)makalesini okuyun.

## <a name="next-steps"></a>Sonraki adımlar
InfiniBand özellikli (' r ' boyutları) hakkında daha fazla bilgi için bkz. [H serisi](../sizes-hpc.md) ve [N serisi](../sizes-gpu.md) VM 'ler.

> [!div class="nextstepaction"]
> [Linux VM uzantıları ve özellikleri hakkında daha fazla bilgi edinin](features-linux.md)
