---
title: NVıDıA GPU sürücü uzantısı-Azure Windows VM 'Leri
description: Windows çalıştıran N serisi işlem VM 'lerine NVıDıA GPU sürücülerini yüklemek için Microsoft Azure uzantısı.
services: virtual-machines-windows
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: akjosh
ms.openlocfilehash: c388f433327b5328483f10fbef637a6fdfd08832
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073030"
---
# <a name="nvidia-gpu-driver-extension-for-windows"></a>Windows için NVıDıA GPU sürücü uzantısı

## <a name="overview"></a>Genel Bakış

Bu uzantı, Windows N serisi VM 'Lere NVıDıA GPU sürücülerini yüklüyor. VM ailesine bağlı olarak, uzantı CUDA veya KıLAVUZ sürücülerini de yüklüyor. Bu uzantıyı kullanarak NVıDıA sürücülerini yüklediğinizde, [NVIDIA Son Kullanıcı Lisans sözleşmesinin](https://go.microsoft.com/fwlink/?linkid=874330)şartlarını kabul etmiş ve kabul etmiş olursunuz. Yükleme işlemi sırasında, sanal makine, Sürücü kurulumunu tamamlayacak şekilde yeniden başlayabilir.

Sürücülerin ve desteklenen geçerli sürümlerin el ile yüklenmesiyle ilgili yönergeler [burada](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup)bulunabilir.
[Linux N serisi VM 'LERE](hpccompute-gpu-linux.md)NVIDIA GPU sürücülerini yüklemek için de bir uzantı kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar

### <a name="operating-system"></a>İşletim sistemi

Bu uzantı aşağıdaki OSs 'yi destekler:

| Dağıtım | Sürüm |
|---|---|
| Windows 10 | Çekirdek |
| Windows Server 2016 | Çekirdek |
| Windows Server 2012 R2 | Çekirdek |

### <a name="internet-connectivity"></a>İnternet bağlantısı

NVıDıA GPU sürücüleri için Microsoft Azure uzantısı, hedef sanal makinenin internet 'e bağlı ve erişime sahip olmasını gerektirir.

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
    "type": "NvidiaGpuDriverWindows",
    "typeHandlerVersion": "1.2",
    "autoUpgradeMinorVersion": true,
    "settings": {
    }
  }
}
```

### <a name="properties"></a>Özellikler

| Ad | Değer / örnek | Veri Türü |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| publisher | Microsoft.HpcCompute | string |
| type | NvidiaGpuDriverWindows | string |
| typeHandlerVersion | 1.2 | int |


## <a name="deployment"></a>Dağıtım

### <a name="azure-resource-manager-template"></a>Azure Resource Manager Şablonu 

Azure VM uzantıları Azure Resource Manager şablonları ile dağıtılabilir. Dağıtım sonrası yapılandırması gerektiren bir veya daha fazla sanal makine dağıtıldığında şablonlar idealdir.

Sanal makine uzantısı için JSON yapılandırma içinde sanal makine kaynağı iç içe geçmiş veya kök veya bir Resource Manager JSON şablonunu üst düzey yerleştirilir. Kaynak adı ve türü değeri JSON yapılandırma yerleşimini etkiler. Daha fazla bilgi için [ayarlamak için alt kaynakları ad ve tür](../../azure-resource-manager/resource-manager-template-child-resource.md). 

Aşağıdaki örnek, uzantının sanal makine kaynağının içinde iç içe olduğunu varsayar. İç içe uzantısı kaynak, JSON yerleştirildi `"resources": []` sanal makinenin nesne.

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
    "type": "NvidiaGpuDriverWindows",
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
    -ExtensionName "NvidiaGpuDriverWindows" `
    -ExtensionType "NvidiaGpuDriverWindows" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverWindows `
  --publisher Microsoft.HpcCompute `
  --version 1.2 `
  --settings '{ `
  }'
```

## <a name="troubleshoot-and-support"></a>Sorun giderme ve Destek

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
C:\WindowsAzure\Logs\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverMicrosoft\
```

### <a name="error-codes"></a>Hata kodları

| Hata Kodu | Anlamı | Olası eylemi |
| :---: | --- | --- |
| 0 | İşlem başarılı |
| 1 | İşlem başarılı oldu. Yeniden başlatma gerekiyor. |
| 100 | İşlem desteklenmiyor veya tamamlanamadı. | Olası nedenler: PowerShell sürümü desteklenmiyor, VM boyutu bir N serisi VM değil, verileri indirirken hata oluştu. Hatanın nedenini öğrenmek için günlük dosyalarını denetleyin. |
| 240, 840 | İşlem zaman aşımı. | İşlemi yeniden deneyin. |
| -1 | Özel durum oluştu. | Özel durumun nedenini öğrenmek için günlük dosyalarını denetleyin. |
| -5x | İşlem, bekleyen yeniden başlatma nedeniyle kesildi. | VM 'yi yeniden başlatın. Yükleme yeniden başlatıldıktan sonra devam edecek. Kaldırma işlemi el ile çağrılmalıdır. |


### <a name="support"></a>Destek

Bu makalede herhangi bir noktada daha fazla yardıma ihtiyacınız olursa, üzerinde Azure uzmanlarıyla iletişime geçebilirsiniz [Azure MSDN ve Stack Overflow forumları](https://azure.microsoft.com/support/community/). Alternatif olarak, bir Azure destek olayına dosya. Git [Azure Destek sitesi](https://azure.microsoft.com/support/options/) ve Destek Al'ı seçin. Azure desteği hakkında daha fazla bilgi için okuma [Microsoft Azure desteği SSS](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Sonraki adımlar
Uzantılar hakkında daha fazla bilgi için bkz. [Windows Için sanal makine uzantıları ve özellikleri](features-windows.md).

N serisi VM 'Ler hakkında daha fazla bilgi için bkz. [GPU iyileştirilmiş sanal makine boyutları](../windows/sizes-gpu.md).
