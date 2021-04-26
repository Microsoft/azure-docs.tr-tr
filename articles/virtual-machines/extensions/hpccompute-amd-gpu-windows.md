---
title: AMD GPU sürücü uzantısı-Azure Windows VM 'Leri
description: Windows çalıştıran NVv4 serisi VM 'Lere AMD GPU sürücülerini yüklemek için Microsoft Azure uzantısı.
services: virtual-machines-windows
author: vikancha-MSFT
manager: jkabat
ms.topic: article
ms.service: virtual-machines
ms.subservice: hpc
ms.collection: windows
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/10/2020
ms.author: vikancha
ms.openlocfilehash: 42dac9edc91d7cb935e8c20398c4d31343b358e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559690"
---
# <a name="amd-gpu-driver-extension-for-windows"></a>Windows için AMD GPU sürücü uzantısı

Bu makalede, Windows [NVv4 serisi](../nvv4-series.md) VM 'LERINDE AMD GPU sürücülerini DAĞıTMAYA yönelik VM uzantısına genel bir bakış sunulmaktadır. Bu uzantıyı kullanarak AMD sürücülerini yüklediğinizde, [amd End-User lisans sözleşmesinin](https://amd.com/radeonsoftwarems)koşullarını kabul etmiş ve kabul etmiş olursunuz. Yükleme işlemi sırasında, sanal makine, Sürücü kurulumunu tamamlayacak şekilde yeniden başlayabilir.

Sürücülerin ve desteklenen geçerli sürümlerin el ile yüklenmesiyle ilgili yönergeler [burada](../windows/n-series-amd-driver-setup.md)bulunabilir.

## <a name="prerequisites"></a>Önkoşullar

### <a name="operating-system"></a>İşletim sistemi

Bu uzantı aşağıdaki OSs 'yi destekler:

| Dağıtım | Sürüm |
|---|---|
| Windows 10 EMS | Derleme 1903 |
| Windows 10 | Derleme 1809 |
| Windows Server 2016 | Çekirdek |
| Windows Server 2019 | Çekirdek |

### <a name="internet-connectivity"></a>İnternet bağlantısı

AMD GPU sürücüleri için Microsoft Azure uzantısı, hedef sanal makinenin internet 'e bağlı ve erişime sahip olmasını gerektirir.

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
    "type": "AmdGpuDriverWindows",
    "typeHandlerVersion": "1.0",
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
| tür | AmdGpuDriverWindows | string |
| typeHandlerVersion | 1.0 | int |


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
    "type": "AmdGpuDriverWindows",
    "typeHandlerVersion": "1.0",
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
    -ExtensionName "AmdGpuDriverWindows" `
    -ExtensionType "AmdGpuDriverWindows" `
    -TypeHandlerVersion 1.0 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name AmdGpuDriverWindows `
  --publisher Microsoft.HpcCompute `
  --version 1.0 `
  --settings '{ `
  }'
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

Uzantı yürütme çıkışı şu dizine kaydedilir:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.AmdGpuDriverMicrosoft\
```

### <a name="error-codes"></a>Hata kodları

| Hata Kodu | Anlamı | Olası eylem |
| :---: | --- | --- |
| 0 | İşlem başarılı |
| 1 | İşlem başarılı oldu. Yeniden başlatma gerekiyor. |
| 100 | İşlem desteklenmiyor veya tamamlanamadı. | Olası nedenler: PowerShell sürümü desteklenmiyor, VM boyutu bir N serisi VM değil, verileri indirirken hata oluştu. Hatanın nedenini öğrenmek için günlük dosyalarını denetleyin. |
| 240, 840 | İşlem zaman aşımı. | İşlemi yeniden deneyin. |
| -1 | Özel durum oluştu. | Özel durumun nedenini öğrenmek için günlük dosyalarını denetleyin. |
| -5x | İşlem, bekleyen yeniden başlatma nedeniyle kesildi. | VM 'yi yeniden başlatın. Yükleme yeniden başlatıldıktan sonra devam edecek. Kaldırma işlemi el ile çağrılmalıdır. |


### <a name="support"></a>Destek

Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/community/)Azure uzmanlarıyla iletişim kurun. Alternatif olarak, bir Azure destek olayı da oluşturabilirsiniz. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve Destek Al ' ı seçin. Azure desteğini kullanma hakkında daha fazla bilgi için, [Microsoft Azure support SSS](https://azure.microsoft.com/support/faq/)makalesini okuyun.

## <a name="next-steps"></a>Sonraki adımlar
Uzantılar hakkında daha fazla bilgi için bkz. [Windows Için sanal makine uzantıları ve özellikleri](features-windows.md).

N serisi VM 'Ler hakkında daha fazla bilgi için bkz. [GPU iyileştirilmiş sanal makine boyutları](../sizes-gpu.md).
