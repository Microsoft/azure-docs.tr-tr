---
title: Azure ağ Izleyicisi ile paket yakalamalarını yönetme-Azure CLı | Microsoft Docs
description: Bu sayfada, Azure CLı kullanarak ağ Izleyicisi 'nin paket yakalama özelliğinin nasıl yönetileceği açıklanmaktadır
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: efb8fba655570a661b6b001eb93942475eb2170d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101698646"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-azure-cli"></a>Azure CLı kullanarak paket yakalamalarını Azure ağ Izleyicisi ile yönetme

> [!div class="op_single_selector"]
> - [Azure portalı](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

Ağ Izleyicisi paket yakalama, bir sanal makineden gelen ve giden trafiği izlemek için yakalama oturumları oluşturmanızı sağlar. Yalnızca istediğiniz trafiği yakalamanızı sağlamak için yakalama oturumu için filtreler sağlanır. Paket yakalama, ağ anomali ve proaktif olarak tanılamaya yardımcı olur. Diğer kullanımlar, istemci-sunucu iletişimlerinde hata ayıklamak ve çok daha fazlasını yapmak için ağ istatistiklerini toplamayı, ağ izinsiz kullanım hakkında bilgi kazanmanızı içerir. Bu özellik, paket yakalamalarını uzaktan tetikleyebilerek, bir paket yakalamanın el ile ve istenen makinede çalıştırılması yükünü kolaylaştırır ve bu da değerli süreyi kaydeder.

Bu makaledeki adımları gerçekleştirmek için, [Mac, Linux ve Windows (Azure CLI) Için Azure Command-Line arabirimini yüklemeniz](/cli/azure/install-azure-cli)gerekir.

Bu makalede, şu anda paket yakalama için kullanılabilen farklı yönetim görevleri sunulmaktadır.

- [**Paket yakalaması başlatma**](#start-a-packet-capture)
- [**Paket yakalamayı durdur**](#stop-a-packet-capture)
- [**Paket yakalamayı silme**](#delete-a-packet-capture)
- [**Paket yakalama indirin**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Başlamadan önce

Bu makalede aşağıdaki kaynaklara sahip olduğunuz varsayılır:

- Bir paket yakalama oluşturmak istediğiniz bölgede ağ Izleyicisi örneği
- Paket yakalama uzantısı etkin olan bir sanal makine.

> [!IMPORTANT]
> Paket yakalama, sanal makinede bir aracının çalışıyor olmasını gerektirir. Aracı bir uzantı olarak yüklenir. VM uzantıları hakkında yönergeler için, [sanal makine uzantıları ve özellikleri](../virtual-machines/extensions/features-windows.md)' ni ziyaret edin.

## <a name="install-vm-extension"></a>VM uzantıları yükleme

### <a name="step-1"></a>1. Adım

`az vm extension set`Konuk sanal makinesine paket yakalama Aracısı 'nı yüklemek için komutunu çalıştırın.

Windows sanal makineleri için:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentWindows --version 1.4
```

Linux sanal makineleri için:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentLinux --version 1.4
```

### <a name="step-2"></a>2. Adım

Aracının yüklü olduğundan emin olmak için, `vm extension show` komutunu çalıştırın ve kaynak grubu ve sanal makine adı ' nı geçirin. Aracının yüklü olduğundan emin olmak için sonuç listesini denetleyin.

Windows sanal makineleri için:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name NetworkWatcherAgentWindows
```

Linux sanal makineleri için:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name AzureNetworkWatcherExtension
```

Aşağıdaki örnek, çalıştırmanın yanıt örneğidir `az vm extension show`

```json
{
  "autoUpgradeMinorVersion": true,
  "forceUpdateTag": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/extensions/NetworkWatcherAgentWindows",
  "instanceView": null,
  "location": "westcentralus",
  "name": "NetworkWatcherAgentWindows",
  "protectedSettings": null,
  "provisioningState": "Succeeded",
  "publisher": "Microsoft.Azure.NetworkWatcher",
  "resourceGroup": "{resourceGroupName}",
  "settings": null,
  "tags": null,
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "typeHandlerVersion": "1.4",
  "virtualMachineExtensionType": "NetworkWatcherAgentWindows"
}
```

## <a name="start-a-packet-capture"></a>Paket yakalaması başlatma

Yukarıdaki adımlar tamamlandıktan sonra, paket yakalama Aracısı sanal makineye yüklenir.

### <a name="step-1"></a>1. Adım

Bir depolama hesabı alın. Bu depolama hesabı, paket yakalama dosyasını depolamak için kullanılır.

```azurecli-interactive
az storage account list
```

### <a name="step-2"></a>2. Adım

Bu noktada, bir paket yakalama oluşturmaya hazırlarsınız.  İlk olarak, yapılandırmak isteyebileceğiniz parametreleri inceleyelim. Filtreler, paket yakalama tarafından depolanan verileri sınırlandırmak için kullanılabilecek bir parametredir. Aşağıdaki örnek, birkaç filtreye sahip bir paket yakalama ayarlar.  İlk üç filtre, giden TCP trafiğini yalnızca yerel IP 10.0.0.3 'ten 20, 80 ve 443 hedef bağlantı noktalarına toplar.  Son filtre yalnızca UDP trafiğini toplar.

```azurecli-interactive
az network watcher packet-capture create --resource-group {resourceGroupName} --vm {vmName} --name packetCaptureName --storage-account {storageAccountName} --filters "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

Aşağıdaki örnek, komutunu çalıştırmanın beklenen çıktıdır `az network watcher packet-capture create` .

```json
{
  "bytesToCapturePerPacket": 0,
  "etag": "W/\"b8cf3528-2e14-45cb-a7f3-5712ffb687ac\"",
  "filters": [
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "20"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "80"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "443"
    },
    {
      "localIpAddress": "",
      "localPort": "",
      "protocol": "UDP",
      "remoteIpAddress": "",
      "remotePort": ""
    }
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "provisioningState": "Succeeded",
  "resourceGroup": "NetworkWatcherRG",
  "storageLocation": {
    "filePath": null,
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/gwteststorage123abc",
    "storagePath": "https://gwteststorage123abc.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/{resourceGroupName}/p
roviders/microsoft.compute/virtualmachines/{vmName}/2017/05/25/packetcapture_16_22_34_630.cap"
  },
  "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}",
  "timeLimitInSeconds": 18000,
  "totalBytesPerSession": 1073741824
}
```

## <a name="get-a-packet-capture"></a>Paket yakalaması alma

Komutu çalıştırmak `az network watcher packet-capture show-status` , çalışmakta olan veya tamamlanmış bir paket yakalama durumunu alır.

```azurecli-interactive
az network watcher packet-capture show-status --name packetCaptureName --location {networkWatcherLocation}
```

Aşağıdaki örnek, komutunun çıktıdır `az network watcher packet-capture show-status` . Aşağıdaki örnek, yakalamanın durdurulma nedeni ile zaman aşımı ile durdurulduğunda oluşur.

```
{
  "additionalProperties": {
    "status": "Succeeded"
  },
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "packetCaptureError": [],
  "packetCaptureStatus": "Stopped",
  "stopReason": "TimeExceeded"
}
```

## <a name="stop-a-packet-capture"></a>Paket yakalamayı durdur

`az network watcher packet-capture stop`Komutu çalıştırarak bir yakalama oturumu devam ediyorsa, durdurulur.

```azurecli-interactive
az network watcher packet-capture stop --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Komut şu anda çalışan bir yakalama oturumunda veya zaten durmuş olan mevcut bir oturumda çalıştırıldığında yanıt vermez.

## <a name="delete-a-packet-capture"></a>Paket yakalamayı silme

```azurecli-interactive
az network watcher packet-capture delete --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Bir paket yakalamanın silinmesi, depolama hesabındaki dosyayı silmez.

## <a name="download-a-packet-capture"></a>Paket yakalama indirin

Paket yakalama oturumunuz tamamlandığında, yakalama dosyası blob depolamaya veya VM 'deki yerel bir dosyaya yüklenebilir. Paket yakalamanın depolama konumu, oturum oluşturulurken tanımlanmıştır. Bir depolama hesabına kaydedilmiş bu yakalama dosyalarına erişmek için uygun bir araç, buradan indirilebilen Microsoft Azure Depolama Gezgini.  https://storageexplorer.com/

Bir depolama hesabı belirtilmişse, paket yakalama dosyaları aşağıdaki konumdaki bir depolama hesabına kaydedilir:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Sonraki adımlar

[Bir uyarı tetikledi bildiren paket yakalamayı](network-watcher-alert-triggered-packet-capture.md) görüntüleyerek sanal makine uyarıları ile paket yakalamaları otomatikleştirmeyi öğrenin

IP 'de belirli trafiğe izin verilip verilmeyeceğini [denetle onay IP Flow doğrula](diagnose-vm-network-traffic-filtering-problem.md) ' yı ziyaret ederek bulun

<!-- Image references -->