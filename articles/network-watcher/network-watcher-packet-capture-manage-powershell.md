---
title: Azure ağ Izleyicisi ile paket yakalamalarını yönetme-PowerShell | Microsoft Docs
description: Bu sayfada, PowerShell kullanarak ağ Izleyicisi 'nin paket yakalama özelliğinin nasıl yönetileceği açıklanmaktadır
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 04d82085-c9ea-4ea1-b050-a3dd4960f3aa
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 4158c2c5ce69d1811b20c9937c1d064f4fe657ee
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163957"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-powershell"></a>PowerShell kullanarak paket yakalamalarını Azure ağ Izleyicisi ile yönetme

> [!div class="op_single_selector"]
> - [Azure portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

Ağ Izleyicisi paket yakalama, bir sanal makineden gelen ve giden trafiği izlemek için yakalama oturumları oluşturmanızı sağlar. Yalnızca istediğiniz trafiği yakalamanızı sağlamak için yakalama oturumu için filtreler sağlanır. Paket yakalama, ağ anomali ve proaktif olarak tanılamaya yardımcı olur. Diğer kullanımlar, istemci-sunucu iletişimlerinde hata ayıklamak ve çok daha fazlasını yapmak için ağ istatistiklerini toplamayı, ağ izinsiz kullanım hakkında bilgi kazanmanızı içerir. Bu özellik, paket yakalamalarını uzaktan tetikleyebilerek, bir paket yakalamanın el ile ve istenen makinede çalıştırılması yükünü kolaylaştırır ve bu da değerli süreyi kaydeder.

Bu makalede, şu anda paket yakalama için kullanılabilen farklı yönetim görevleri sunulmaktadır.

- [**Paket yakalaması başlatma**](#start-a-packet-capture)
- [**Paket yakalamayı durdur**](#stop-a-packet-capture)
- [**Paket yakalamayı silme**](#delete-a-packet-capture)
- [**Paket yakalama indirin**](#download-a-packet-capture)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede aşağıdaki kaynaklara sahip olduğunuz varsayılır:

* Bir paket yakalama oluşturmak istediğiniz bölgede ağ Izleyicisi örneği

* Paket yakalama uzantısı etkin olan bir sanal makine.

> [!IMPORTANT]
> Paket yakalama, bir sanal makine uzantısı `AzureNetworkWatcherExtension`gerektirir. Windows VM 'ye uzantı yüklemek için bkz. [Windows Için Azure ağ Izleyicisi Aracısı sanal makine uzantısı](../virtual-machines/windows/extensions-nwa.md) ve Linux VM Için [Azure Ağ İzleyicisi Aracısı sanal makine uzantısı](../virtual-machines/linux/extensions-nwa.md)' nı ziyaret edin.

## <a name="install-vm-extension"></a>VM uzantıları yükleme

### <a name="step-1"></a>1\. Adım

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name VM1
```

### <a name="step-2"></a>2\. Adım

Aşağıdaki örnek, `Set-AzVMExtension` cmdlet 'ini çalıştırmak için gereken uzantı bilgilerini alır. Bu cmdlet, paket yakalama aracısını Konuk sanal makinesine kurar.

> [!NOTE]
> `Set-AzVMExtension` Cmdlet 'inin tamamlanması birkaç dakika sürebilir.

Windows sanal makineleri için:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentWindows -Version 1.4.585.2
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Linux sanal makineleri için:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentLinux -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Aşağıdaki örnek `Set-AzVMExtension` cmdlet 'i çalıştırdıktan sonra başarılı bir yanıt örneğidir.

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
```

### <a name="step-3"></a>3\. Adım

Aracının yüklü olduğundan emin olmak için `Get-AzVMExtension` cmdlet 'ini çalıştırın ve sanal makine adı ile uzantı adı ' nı geçirin.

```powershell
Get-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -VMName $VM.Name -Name $ExtensionName
```

Aşağıdaki örnek, çalıştırmanın yanıt örneğidir`Get-AzVMExtension`

```
ResourceGroupName       : testrg
VMName                  : testvm1
Name                    : AzureNetworkWatcherExtension
Location                : westcentralus
Etag                    : null
Publisher               : Microsoft.Azure.NetworkWatcher
ExtensionType           : NetworkWatcherAgentWindows
TypeHandlerVersion      : 1.4
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1/
                          extensions/AzureNetworkWatcherExtension
PublicSettings          : 
ProtectedSettings       : 
ProvisioningState       : Succeeded
Statuses                : 
SubStatuses             : 
AutoUpgradeMinorVersion : True
ForceUpdateTag          : 
```

## <a name="start-a-packet-capture"></a>Paket yakalaması başlatma

Yukarıdaki adımlar tamamlandıktan sonra, paket yakalama Aracısı sanal makineye yüklenir.

### <a name="step-1"></a>1\. Adım

Sonraki adım, ağ Izleyicisi örneğini almak için kullanılır. Bu değişken, adım 4 ' `New-AzNetworkWatcherPacketCapture` teki cmdlet 'e geçirilir.

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
```

### <a name="step-2"></a>2\. Adım

Bir depolama hesabı alın. Bu depolama hesabı, paket yakalama dosyasını depolamak için kullanılır.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName testrg -Name testrgsa123
```

### <a name="step-3"></a>3\. Adım

Filtreler, paket yakalama tarafından depolanan verileri sınırlamak için kullanılabilir. Aşağıdaki örnek iki filtre ayarlar.  Tek bir filtre giden TCP trafiğini yalnızca yerel IP 10.0.0.3 'ten 20, 80 ve 443 hedef bağlantı noktalarına toplar.  İkinci filtre yalnızca UDP trafiğini toplar.

```powershell
$filter1 = New-AzPacketCaptureFilterConfig -Protocol TCP -RemoteIPAddress "1.1.1.1-255.255.255.255" -LocalIPAddress "10.0.0.3" -LocalPort "1-65535" -RemotePort "20;80;443"
$filter2 = New-AzPacketCaptureFilterConfig -Protocol UDP
```

> [!NOTE]
> Paket yakalama için birden çok filtre tanımlanabilir.

### <a name="step-4"></a>4\. Adım

Paket yakalama işlemini başlatmak için cmdlet'iniçalıştırınveöncekiadımlardaalınangereklideğerlerigeçirerek.`New-AzNetworkWatcherPacketCapture`
```powershell

New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $vm.Id -PacketCaptureName "PacketCaptureTest" -StorageAccountId $storageAccount.id -TimeLimitInSeconds 60 -Filter $filter1, $filter2
```

Aşağıdaki örnek `New-AzNetworkWatcherPacketCapture` cmdlet 'i çalıştırmanın beklenen çıktıdır.

```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"3bf27278-8251-4651-9546-c7f369855e4e"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]


```

## <a name="get-a-packet-capture"></a>Paket yakalaması alma

`Get-AzNetworkWatcherPacketCapture` Cmdlet 'ini çalıştırmak, çalışmakta olan veya tamamlanmış bir paket yakalama durumunu alır.

```powershell
Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

Aşağıdaki örnek, `Get-AzNetworkWatcherPacketCapture` cmdlet 'inin çıktıdır. Yakalama tamamlandıktan sonra aşağıdaki örnek verilmiştir. PacketCaptureStatus değeri, timeof durmasının aşıldığı bir nedenden dolayı durdurulur. Bu değer, paket yakalamanın başarılı olduğunu ve zamanından sonra çalıştığını gösterir.
```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"4b9a81ed-dc63-472e-869e-96d7166ccb9b"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]
CaptureStartTime        : 2/1/2017 10:43:01 PM
PacketCaptureStatus     : Stopped
StopReason              : TimeExceeded
PacketCaptureError      : []
```

## <a name="stop-a-packet-capture"></a>Paket yakalamayı durdur

`Stop-AzNetworkWatcherPacketCapture` Cmdlet 'i çalıştırarak bir yakalama oturumu devam ediyorsa, durdurulur.

```powershell
Stop-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> Cmdlet Şu anda çalışan bir yakalama oturumunda veya zaten durmuş olan mevcut bir oturumda çalıştırıldığında yanıt vermez.

## <a name="delete-a-packet-capture"></a>Paket yakalamayı silme

```powershell
Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> Bir paket yakalamanın silinmesi, depolama hesabındaki dosyayı silmez.

## <a name="download-a-packet-capture"></a>Paket yakalama indirin

Paket yakalama oturumunuz tamamlandığında, yakalama dosyası blob depolamaya veya VM 'deki yerel bir dosyaya yüklenebilir. Paket yakalamanın depolama konumu, oturum oluşturulurken tanımlanmıştır. Bir depolama hesabına kaydedilmiş bu yakalama dosyalarına erişmek için uygun bir araç, buradan indirilebilen Microsoft Azure Depolama Gezgini. https://storageexplorer.com/

Bir depolama hesabı belirtilmişse, paket yakalama dosyaları aşağıdaki konumdaki bir depolama hesabına kaydedilir:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Sonraki adımlar

[Bir uyarı tetikledi bildiren paket yakalamayı](network-watcher-alert-triggered-packet-capture.md) görüntüleyerek sanal makine uyarıları ile paket yakalamaları otomatikleştirmeyi öğrenin

IP 'de belirli trafiğe izin verilip verilmeyeceğini [denetle onay IP Flow doğrula](diagnose-vm-network-traffic-filtering-problem.md) ' yı ziyaret ederek bulun

<!-- Image references -->














