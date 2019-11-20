---
title: NVıDıA GPU sürücü uzantısı-Azure Linux VM 'Leri
description: Linux çalıştıran N serisi işlem VM 'lerine NVıDıA GPU sürücülerini yüklemek için Microsoft Azure uzantısı.
services: virtual-machines-linux
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/11/2019
ms.author: akjosh
ms.openlocfilehash: 6ea61acfc2db3c8f1f5c9c0ac8da8f19897d441e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073736"
---
# <a name="nvidia-gpu-driver-extension-for-linux"></a>Linux için NVıDıA GPU sürücü uzantısı

## <a name="overview"></a>Genel Bakış

Bu uzantı, Linux N serisi VM 'Lere NVıDıA GPU sürücülerini yüklüyor. VM ailesine bağlı olarak, uzantı CUDA veya KıLAVUZ sürücülerini de yüklüyor. Bu uzantıyı kullanarak NVıDıA sürücülerini yüklediğinizde, [NVIDIA Son Kullanıcı Lisans sözleşmesinin](https://go.microsoft.com/fwlink/?linkid=874330)şartlarını kabul etmiş ve kabul etmiş olursunuz. Yükleme işlemi sırasında, sanal makine, Sürücü kurulumunu tamamlayacak şekilde yeniden başlayabilir.

Sürücülerin ve desteklenen geçerli sürümlerin el ile yüklenmesiyle ilgili yönergeler [burada](
https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup)bulunabilir.
[Windows N serisi VM 'LERE](hpccompute-gpu-windows.md)NVIDIA GPU sürücülerini yüklemek için de bir uzantı kullanılabilir.

## <a name="prerequisites"></a>Önkoşullar

### <a name="operating-system"></a>İşletim sistemi

Bu uzantı, belirli işletim sistemi sürümü için sürücü desteğine bağlı olarak aşağıdaki işletim sistemi destekleri 'nı destekler.

| Dağıtım | Sürüm |
|---|---|
| Linux: Ubuntu | 16.04 LTS, 18.04 LTS |
| Linux: Red Hat Enterprise Linux | 7.3, 7.4, 7.5, 7.6 |
| Linux: CentOS | 7.3, 7.4, 7.5, 7.6 |

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
    "type": "NvidiaGpuDriverLinux",
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
| type | NvidiaGpuDriverLinux | string |
| typeHandlerVersion | 1.2 | int |

### <a name="settings"></a>Ayarlar

Tüm ayarlar isteğe bağlıdır. Varsayılan davranış, sürücü yüklemesi için gerekli değilse çekirdeği güncelleştirmemelidir, desteklenen en son sürücüyü ve CUDA araç setini (varsa) yükler.

| Ad | Açıklama | Varsayılan Değer | Geçerli Değerler | Veri Türü |
| ---- | ---- | ---- | ---- | ---- |
| updateOS | Sürücü yüklemesi için gerekli olmasa bile çekirdeği Güncelleştir | yanlış | true, false | boole |
| driverVersion | NV: GRID sürücü sürümü<br> NC/ND: CUDA araç seti sürümü. Seçilen CUDA için en son sürücüler otomatik olarak yüklenir. | latest | KıLAVUZ: "430,30", "418,70", "410,92", "410,71", "390,75", "390,57", "390,42"<br> CUDA: "10.0.130", "9.2.88", "9.1.85" | string |
| ınstallcuda | CUDA araç setini yükler. Yalnızca NC/ND serisi VM 'Ler için geçerlidir. | true | true, false | boole |


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
    "type": "NvidiaGpuDriverLinux",
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
    -ExtensionName "NvidiaGpuDriverLinux" `
    -ExtensionType "NvidiaGpuDriverLinux" `
    -TypeHandlerVersion 1.2 `
    -SettingString '{ `
    }'
```

### <a name="azure-cli"></a>Azure CLI

Aşağıdaki örnekte, yukarıdaki Azure Resource Manager ve PowerShell örnekleri yansıtıdakiler ve varsayılan olmayan sürücü yüklemesi için örnek olarak özel ayarlar da eklenir. Özellikle, işletim sistemi çekirdeğini güncelleştirir ve belirli bir CUDA Araç Seti sürüm sürücüsü yüklenir.

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name NvidiaGpuDriverLinux `
  --publisher Microsoft.HpcCompute `
  --version 1.2 `
  --settings '{ `
    "updateOS": true, `
    "driverVersion": "9.1.85", `
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

Uzantı yürütme çıkış aşağıdaki dosyasına kaydedilir:

```bash
/var/log/azure/nvidia-vmext-status
```

### <a name="exit-codes"></a>Çıkış kodları

| Çıkış kodu | Anlamı | Olası eylemi |
| :---: | --- | --- |
| 0 | İşlem başarılı |
| 1 | Uzantının yanlış kullanımı | Yürütme çıkış günlüğünü denetle |
| 10 | Hyper-V ve Azure için Linux Tümleştirme Hizmetleri kullanılabilir değil veya yüklü değil | Lspcı çıkışını denetle |
| 11 | NVıDıA GPU bu VM boyutunda bulunamadı | Desteklenen bir [VM boyutu ve işletim sistemi](../linux/n-series-driver-setup.md) kullan |
| 12 | Görüntü teklifi desteklenmiyor |
| 13 | VM boyutu desteklenmiyor | Dağıtmak için N serisi VM kullanma |
| 14 | İşlem başarısız oldu | Yürütme çıkış günlüğünü denetle |


### <a name="support"></a>Destek

Bu makalede herhangi bir noktada daha fazla yardıma ihtiyacınız olursa, üzerinde Azure uzmanlarıyla iletişime geçebilirsiniz [Azure MSDN ve Stack Overflow forumları](https://azure.microsoft.com/support/community/). Alternatif olarak, bir Azure destek olayına dosya. Git [Azure Destek sitesi](https://azure.microsoft.com/support/options/) ve Destek Al'ı seçin. Azure desteği hakkında daha fazla bilgi için okuma [Microsoft Azure desteği SSS](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Sonraki adımlar
Uzantılar hakkında daha fazla bilgi için bkz. [Linux Için sanal makine uzantıları ve özellikleri](features-linux.md).

N serisi VM 'Ler hakkında daha fazla bilgi için bkz. [GPU iyileştirilmiş sanal makine boyutları](../linux/sizes-gpu.md).
