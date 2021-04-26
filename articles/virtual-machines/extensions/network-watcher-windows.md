---
title: Windows için Azure Ağ İzleyicisi Aracısı sanal makine uzantısı
description: Ağ Izleyicisi aracısını bir sanal makine uzantısı kullanarak Windows sanal makinesine dağıtın.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 02/14/2017
ms.openlocfilehash: d336a39714712e5436086e22ad24fc942a7d850a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563549"
---
# <a name="network-watcher-agent-virtual-machine-extension-for-windows"></a>Windows için ağ Izleyicisi Aracısı sanal makine uzantısı

## <a name="overview"></a>Genel Bakış

[Azure Ağ İzleyicisi](../../network-watcher/network-watcher-monitoring-overview.md) , Azure ağlarını izlemeye izin veren bir ağ performansı izleme, tanılama ve analiz hizmetidir. Ağ Izleyicisi Aracısı sanal makine uzantısı, istek üzerine ağ trafiği yakalama ve Azure sanal makinelerinde diğer gelişmiş işlevler için bir gereksinimdir.


Bu belgede, Windows için ağ Izleyicisi Aracısı sanal makine uzantısı için desteklenen platformlar ve dağıtım seçenekleri ayrıntılı olarak bulunmaktadır. Aracının yüklenmesi, sanal makinenin yeniden başlatılmasını kesintiye uğramaz veya gerektirmez. Uzantıyı dağıttığınız sanal makinelere dağıtabilirsiniz. Sanal makine bir Azure hizmeti tarafından dağıtılırsa, sanal makinede uzantıların yüklenmesine izin verilip verilmeyeceğini öğrenmek için hizmetin belgelerini denetleyin.

## <a name="prerequisites"></a>Önkoşullar

### <a name="operating-system"></a>İşletim sistemi

Windows için ağ Izleyicisi Aracısı uzantısı Windows Server 2008 R2, 2012, 2012 R2, 2016 ve 2019 yayınlarına karşı çalıştırılabilir. Nano sunucu şu anda desteklenmiyor.

### <a name="internet-connectivity"></a>İnternet bağlantısı

Bazı ağ Izleyicisi Aracısı işlevleri, hedef sanal makinenin Internet 'e bağlanmasını gerektirir. Giden bağlantıları oluşturma özelliği olmadan, ağ Izleyicisi Aracısı depolama hesabınıza paket yakalamalarını karşıya yükleyemeyecektir. Daha fazla ayrıntı için lütfen [Ağ İzleyicisi belgelerine](../../network-watcher/network-watcher-monitoring-overview.md)bakın.

## <a name="extension-schema"></a>Uzantı şeması

Aşağıdaki JSON ağ Izleyicisi Aracısı uzantısı için şemayı gösterir. Uzantı gerektirmez ve Kullanıcı tarafından sağlanan ayarları desteklemez ve varsayılan yapılandırmasına dayanır.

```json
{
    "type": "extensions",
    "name": "Microsoft.Azure.NetworkWatcher",
    "apiVersion": "[variables('apiVersion')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Azure.NetworkWatcher",
        "type": "NetworkWatcherAgentWindows",
        "typeHandlerVersion": "1.4",
        "autoUpgradeMinorVersion": true
    }
}
```

### <a name="property-values"></a>Özellik değerleri

| Name | Değer/örnek |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| yayımcı | Microsoft. Azure. Networkizleyici |
| tür | NetworkWatcherAgentWindows |
| typeHandlerVersion | 1.4 |


## <a name="template-deployment"></a>Şablon dağıtımı

Azure VM uzantılarını Azure Resource Manager şablonlarıyla dağıtabilirsiniz. Bir Azure Resource Manager şablon dağıtımı sırasında ağ Izleyici Aracısı uzantısını çalıştırmak için bir Azure Resource Manager şablonundaki önceki bölümde ayrıntılı JSON şemasını kullanabilirsiniz.

## <a name="powershell-deployment"></a>PowerShell dağıtımı

`Set-AzVMExtension`Ağ Izleyicisi Aracısı sanal makine uzantısını var olan bir sanal makineye dağıtmak için komutunu kullanın:

```powershell
Set-AzVMExtension `
  -ResourceGroupName "myResourceGroup1" `
  -Location "WestUS" `
  -VMName "myVM1" `
  -Name "networkWatcherAgent" `
  -Publisher "Microsoft.Azure.NetworkWatcher" `
  -Type "NetworkWatcherAgentWindows" `
  -TypeHandlerVersion "1.4"
```

## <a name="troubleshooting-and-support"></a>Sorun giderme ve destek

### <a name="troubleshooting"></a>Sorun giderme

Azure portal ve PowerShell 'den uzantı dağıtımlarının durumu hakkında veri alabilirsiniz. Belirli bir VM için uzantıların dağıtım durumunu görmek için Azure PowerShell modülünü kullanarak aşağıdaki komutu çalıştırın:

```powershell
Get-AzVMExtension -ResourceGroupName myResourceGroup1 -VMName myVM1 -Name networkWatcherAgent
```

Uzantı yürütme çıkışı aşağıdaki dizinde bulunan dosyalara kaydedilir:

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.NetworkWatcher.NetworkWatcherAgentWindows\
```

### <a name="support"></a>Destek

Bu makalenin herhangi bir noktasında daha fazla yardıma ihtiyacınız varsa, ağ Izleyicisi Kullanıcı Kılavuzu belgelerine başvurabilirsiniz veya [MSDN Azure ve Stack Overflow forumlarında](https://azure.microsoft.com/support/forums/)Azure uzmanlarıyla iletişim kurun. Alternatif olarak, bir Azure destek olayı da oluşturabilirsiniz. [Azure destek sitesine](https://azure.microsoft.com/support/options/) gidin ve Destek Al ' ı seçin. Azure desteğini kullanma hakkında daha fazla bilgi için, [Microsoft Azure support SSS](https://azure.microsoft.com/support/faq/)makalesini okuyun.
