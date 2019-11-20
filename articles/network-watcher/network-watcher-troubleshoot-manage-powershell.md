---
title: Azure sanal ağ geçidi ve bağlantıları sorunlarını giderme-PowerShell | Microsoft Docs
description: Bu sayfada, Azure ağ Izleyicisi PowerShell cmdlet 'inin nasıl kullanılacağı açıklanmaktadır
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: f6f0a813-38b6-4a1f-8cfc-1dfdf979f595
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: kumud
ms.openlocfilehash: 40d576a980bd66fea44f9f8e4935fab3d777e4c8
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163865"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Azure ağ Izleyicisi PowerShell kullanarak sanal ağ geçidi ve bağlantıları sorunlarını giderme

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Ağ Izleyicisi, Azure 'daki ağ kaynaklarınızı kavramak üzere birçok özellik sağlar. Bu yeteneklerden biri kaynak sorun gidermeye sahiptir. Kaynak sorunlarını giderme, Portal, PowerShell, CLı veya REST API aracılığıyla çağrılabilir. Çağrıldığında, ağ Izleyicisi bir sanal ağ geçidinin veya bir bağlantının sistem durumunu inceler ve bulgularını döndürür.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Başlamadan önce

Bu senaryo, ağ Izleyicisi oluşturmak için [ağ Izleyicisi oluşturma](network-watcher-create.md) bölümündeki adımları zaten izlediğinizi varsayar.

Desteklenen ağ geçidi türlerinin bir listesi için, [desteklenen ağ geçidi türleri](network-watcher-troubleshoot-overview.md#supported-gateway-types)' ni ziyaret edin.

## <a name="overview"></a>Genel Bakış

Kaynak sorunlarını giderme, sanal ağ geçitleri ve bağlantılarıyla ortaya çıkan sorunları gidermeye yönelik bir özellik sağlar. Kaynak sorun gidermeye bir istek yapıldığında, Günlükler sorgulanmakta ve incelenmekte olur. İnceleme tamamlandığında sonuçlar döndürülür. Kaynak sorun giderme istekleri uzun süren isteklerdir, bu da bir sonuç döndürmek için birden çok dakika sürebilir. Sorun gidermeye ait Günlükler, belirtilen depolama hesabındaki bir kapsayıcıda depolanır.

## <a name="retrieve-network-watcher"></a>Ağ İzleyicisini al

İlk adım, ağ Izleyicisi örneğini almak için kullanılır. Değişken, adım 4 ' teki `Start-AzNetworkWatcherResourceTroubleshooting` cmdlet 'e geçirilir. `$networkWatcher`

```powershell
$networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
```

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Sanal ağ geçidi bağlantısı alma

Bu örnekte, kaynak sorunlarını giderme bir bağlantıda çalıştırılmakta. Ayrıca, bir sanal ağ geçidi de geçirebilirsiniz.

```powershell
$connection = Get-AzVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
```

## <a name="create-a-storage-account"></a>Depolama hesabı oluşturma

Kaynak sorun giderme, kaynağın durumuyla ilgili verileri döndürür, ayrıca günlükleri gözden geçirilecek bir depolama hesabına kaydeder. Bu adımda, mevcut bir depolama hesabı varsa bir depolama hesabı oluşturuyoruz, onu kullanabilirsiniz.

```powershell
$sa = New-AzStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
Set-AzCurrentStorageAccount -ResourceGroupName $sa.ResourceGroupName -Name $sa.StorageAccountName
$sc = New-AzStorageContainer -Name logs
```

## <a name="run-network-watcher-resource-troubleshooting"></a>Ağ Izleyicisi kaynak sorunlarını giderme

`Start-AzNetworkWatcherResourceTroubleshooting` Cmdlet 'iyle kaynak sorunlarını giderin. Cmdlet 'i ağ Izleyicisi nesnesi, bağlantının kimliği veya sanal ağ geçidi, depolama hesabı kimliği ve sonuçları depolayacak yol geçiririz.

> [!NOTE]
> `Start-AzNetworkWatcherResourceTroubleshooting` Cmdlet uzun çalışıyor ve tamamlanması birkaç dakika sürebilir.

```powershell
Start-AzNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)$($sc.name)"
```

Cmdlet 'ini çalıştırdığınızda, ağ Izleyicisi, sistem durumunu doğrulamak için kaynağı gözden geçirir. Sonuçları kabuğa döndürür ve sonuçların günlüklerini belirtilen depolama hesabında depolar.

## <a name="understanding-the-results"></a>Sonuçları anlama

Eylem metni, sorunun nasıl çözüleceği hakkında genel rehberlik sağlar. Sorun için bir eylem uygulanabilir ise, ek kılavuzlarla bir bağlantı sağlanır. Ek bir kılavuz olmadığı durumlarda yanıt, bir destek talebi açmak için URL 'yi sağlar.  Yanıtın özellikleri ve dahil olan özellikler hakkında daha fazla bilgi için [ağ Izleyicisi sorunlarını giderme genel bakış](network-watcher-troubleshoot-overview.md) ' ı ziyaret edin

Azure depolama hesaplarından dosya indirme yönergeleri için bkz. [.NET kullanarak Azure Blob depolamayı kullanmaya başlama](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Kullanılabilecek başka bir araç Depolama Gezgini. Depolama Gezgini hakkında daha fazla bilgi aşağıdaki bağlantıda bulunabilir: [Depolama Gezgini](https://storageexplorer.com/)

## <a name="next-steps"></a>Sonraki adımlar

VPN bağlantısını durduran ayarlar değiştirildiyse, ağ güvenlik grubunu ve söz konusu olabilecek güvenlik kurallarını izlemek için [ağ güvenlik gruplarını yönetme](../virtual-network/manage-network-security-group.md) bölümüne bakın.
