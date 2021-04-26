---
title: VNET ağ geçidi ve bağlantılarında sorun giderme-Azure REST API
titleSuffix: Azure Network Watcher
description: Bu sayfada REST kullanılarak Azure ağ Izleyicisi ile sanal ağ geçitlerinin ve bağlantılarının nasıl giderileceği açıklanmaktadır
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 72f279d90d071b0e5fe7f552a05474bc3b37a82e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98011042"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher"></a>Azure ağ Izleyicisi 'ni kullanarak sanal ağ geçidi ve bağlantıları sorunlarını giderme

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Ağ Izleyicisi, Azure 'daki ağ kaynaklarınızı kavramak üzere birçok özellik sağlar. Bu yeteneklerden biri kaynak sorun gidermeye sahiptir. Kaynak sorunlarını giderme, Portal, PowerShell, CLı veya REST API aracılığıyla çağrılabilir. Çağrıldığında, ağ Izleyicisi bir sanal ağ geçidinin veya bir bağlantının sistem durumunu inceler ve bulgularını döndürür.

Bu makalede, şu anda kaynak sorun giderme için kullanılabilen farklı yönetim görevleri sunulmaktadır.

- [**Sanal ağ geçidi sorunlarını giderme**](#troubleshoot-a-virtual-network-gateway)
- [**Bağlantı sorunlarını giderme**](#troubleshoot-connections)

## <a name="before-you-begin"></a>Başlamadan önce

ARMclient, PowerShell kullanarak REST API çağırmak için kullanılır. ARMClient, [Chocolatey üzerinde](https://chocolatey.org/packages/ARMClient) Chocolatey konumunda bulunur

Bu senaryo, ağ Izleyicisi oluşturmak için [ağ Izleyicisi oluşturma](network-watcher-create.md) bölümündeki adımları zaten izlediğinizi varsayar.

Desteklenen ağ geçidi türlerinin bir listesi için, [desteklenen ağ geçidi türleri](network-watcher-troubleshoot-overview.md#supported-gateway-types)' ni ziyaret edin.

## <a name="overview"></a>Genel Bakış

Ağ Izleyicisi sorunlarını giderme, sanal ağ geçitleri ve bağlantılarıyla kaynaklanan sorunları gidermeye yönelik bir özellik sağlar. Kaynak sorunlarını gidermeye yönelik bir istek yapıldığında, günlükler sorgulama ve denetlenir. İnceleme tamamlandığında sonuçlar döndürülür. API isteklerinin sorunlarını giderme, uzun süre çalışan isteklerdir ve bu da bir sonuç döndürmek için birden çok dakika sürebilir. Günlükler, depolama hesabındaki bir kapsayıcıda depolanır.

## <a name="log-in-with-armclient"></a>ARMClient ile oturum açma

```powershell
armclient login
```

## <a name="troubleshoot-a-virtual-network-gateway"></a>Sanal ağ geçidi sorunlarını giderme


### <a name="post-the-troubleshoot-request"></a>Sorun giderme isteğini gönder

Aşağıdaki örnek, bir sanal ağ geçidinin durumunu sorgular.

```powershell

$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "ContosoRG"
$NWresourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$vnetGatewayName = "ContosoVNETGateway"
$storageAccountName = "contososa"
$containerName = "gwlogs"
$requestBody = @"
{
'TargetResourceId': '/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/virtualNetworkGateways/${vnetGatewayName}',
'Properties': {
'StorageId': '/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Storage/storageAccounts/${storageAccountName}',
'StoragePath': 'https://${storageAccountName}.blob.core.windows.net/${containerName}'
}
}
"@


armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30" $requestBody -verbose
```

Bu işlem uzun süre çalışan bir işlem olduğundan, aşağıdaki yanıtta gösterildiği gibi işlemi sorgulamak için URI ve yanıt üst bilgisinde döndürülür:

**Önemli değerler**

* **Azure-AsyncOperation** -bu özellik, zaman uyumsuz sorun giderme işleminin SORGULANME URI 'sini içerir
* **Konum** -bu özellik sonuçların işlem TAMAMLANDıĞıNDA olduğu URI 'yi içerir

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 8a1167b7-6768-4ac1-85dc-703c9c9b9247
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4364d88a-bd08-422c-a716-dbb0cdc99f7b
x-ms-routing-request-id: NORTHCENTRALUS:20170112T183202Z:4364d88a-bd08-422c-a716-dbb0cdc99f7b
Date: Thu, 12 Jan 2017 18:32:01 GMT

null
```

### <a name="query-the-async-operation-for-completion"></a>Zaman uyumsuz işlemi tamamlamak için sorgula

Aşağıdaki örnekte görüldüğü gibi işlemin ilerlemesini sorgulamak için Operations URI 'sini kullanın:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

İşlem devam ederken, yanıt aşağıdaki örnekte görüldüğü gibi **sürüyor** durumunu gösterir:

```json
{
  "status": "InProgress"
}
```

İşlem tamamlandığında durum **başarılı** olarak değişir.

```json
{
  "status": "Succeeded"
}
```

### <a name="retrieve-the-results"></a>Sonuçları alma

Döndürülen durum **başarılı** olduktan sonra, sonuçları almak Için OPERATIONRESULT URI 'SINDEKI bir get yöntemi çağırın.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30" -verbose
```

Aşağıdaki yanıtlar, bir ağ geçidinde sorun gidermeye yönelik sonuçlar sorgulanırken döndürülen tipik bir azaltılmış yanıt örneğidir. Yanıtın ne anlama geldiğini açıklama almak için bkz. [sonuçları anlama](#understanding-the-results) .

```json
{
  "startTime": "2017-01-12T10:31:41.562646-08:00",
  "endTime": "2017-01-12T18:31:48.677Z",
  "code": "Degraded",
  "results": [
    {
      "id": "PlatformInActive",
      "summary": "We are sorry, your VPN gateway is in standby mode",
      "detail": "During this time the gateway will not initiate or accept VPN connections with on premises VPN devices or other Azure VPN Gateways. This is a transient state while the Azure platform is being updated.",
      "recommendedActions": [
        {
          "actionText": "If the condition persists, please try resetting your Azure VPN gateway",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting the VPN Gateway"
        },
        {
          "actionText": "If your VPN gateway isn't up and running by the expected resolution time, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    },
    {
      "id": "NoFault",
      "summary": "This VPN gateway is running normally",
      "detail": "There aren't any known Azure platform problems affecting this VPN Connection",
      "recommendedActions": [
        {
          "actionText": "If you are still experience problems with the VPN gateway, please try resetting the VPN gateway.",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting VPN gateway"
        },
        {
          "actionText": "If you are experiencing problems you believe are caused by Azure, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```


## <a name="troubleshoot-connections"></a>Bağlantı sorunlarını giderme

Aşağıdaki örnek bir bağlantının durumunu sorgular.

```powershell

$subscriptionId = "00000000-0000-0000-0000-000000000000"
$resourceGroupName = "ContosoRG"
$NWresourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$connectionName = "VNET2toVNET1Connection"
$storageAccountName = "contososa"
$containerName = "gwlogs"
$requestBody = @{
"TargetResourceId": "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/connections/${connectionName}",
"Properties": {
"StorageId": "/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Storage/storageAccounts/${storageAccountName}",
"StoragePath": "https://${storageAccountName}.blob.core.windows.net/${containerName}"
}

}
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${NWresourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/troubleshoot?api-version=2016-03-30 $requestBody"
```

> [!NOTE]
> Sorun giderme işlemi bir bağlantıda ve ilgili ağ geçitleri üzerinde paralel olarak çalıştırılamaz. İşlemin önceki kaynakta çalıştırılmadan önce tamamlanmalıdır.

Bu, uzun süre çalışan bir işlem olduğundan, yanıt üstbilgisinde işlemi sorgulamak için URI ve sonuç URI 'SI aşağıdaki yanıtta gösterildiği gibi döndürülür:

**Önemli değerler**

* **Azure-AsyncOperation** -bu özellik, zaman uyumsuz sorun giderme işleminin SORGULANME URI 'sini içerir
* **Konum** -bu özellik sonuçların işlem TAMAMLANDıĞıNDA olduğu URI 'yi içerir

```
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 10
x-ms-request-id: 8a1167b7-6768-4ac1-85dc-703c9c9b9247
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Strict-Transport-Security: max-age=31536000; includeSubDomains
Cache-Control: no-cache
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/8a1167b7-6768-4ac1-85dc-703c9c9b9247?api-version=2016-03-30
Server: Microsoft-HTTPAPI/2.0; Microsoft-HTTPAPI/2.0
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 4364d88a-bd08-422c-a716-dbb0cdc99f7b
x-ms-routing-request-id: NORTHCENTRALUS:20170112T183202Z:4364d88a-bd08-422c-a716-dbb0cdc99f7b
Date: Thu, 12 Jan 2017 18:32:01 GMT

null
```

### <a name="query-the-async-operation-for-completion"></a>Zaman uyumsuz işlemi tamamlamak için sorgula

Aşağıdaki örnekte görüldüğü gibi işlemin ilerlemesini sorgulamak için Operations URI 'sini kullanın:

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operations/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

İşlem devam ederken, yanıt aşağıdaki örnekte görüldüğü gibi **sürüyor** durumunu gösterir:

```json
{
  "status": "InProgress"
}
```

İşlem tamamlandığında durum **başarılı** olarak değişir.

```json
{
  "status": "Succeeded"
}
```

Aşağıdaki yanıtlar, bir bağlantı sorunlarını gidermeye yönelik sonuçlar sorgulanırken tipik bir yanıt döndüren örneklerdir.

### <a name="retrieve-the-results"></a>Sonuçları alma

Döndürülen durum **başarılı** olduktan sonra, sonuçları almak Için OPERATIONRESULT URI 'SINDEKI bir get yöntemi çağırın.

```powershell
armclient get "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Network/locations/westcentralus/operationResults/843b1c31-4717-4fdd-b7a6-4c786ca9c501?api-version=2016-03-30"
```

Aşağıdaki yanıtlar, bir bağlantı sorunlarını gidermeye yönelik sonuçlar sorgulanırken tipik bir yanıt döndüren örneklerdir.

```json
{
  "startTime": "2017-01-12T14:09:19.1215346-08:00",
  "endTime": "2017-01-12T22:09:23.747Z",
  "code": "UnHealthy",
  "results": [
    {
      "id": "PlatformInActive",
      "summary": "We are sorry, your VPN gateway is in standby mode",
      "detail": "During this time the gateway will not initiate or accept VPN connections with on premises VPN devices or other Azure VPN Gateways. This 
is a transient state while the Azure platform is being updated.",
      "recommendedActions": [
        {
          "actionText": "If the condition persists, please try resetting your Azure VPN gateway",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting the VPN gateway"
        },
        {
          "actionText": "If your VPN Connection isn't up and running by the expected resolution time, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    },
    {
      "id": "NoFault",
      "summary": "This VPN Connection is running normally",
      "detail": "There aren't any known Azure platform problems affecting this VPN Connection",
      "recommendedActions": [
        {
          "actionText": "If you are still experience problems with the VPN gateway, please try resetting the VPN gateway.",
          "actionUri": "https://azure.microsoft.com/documentation/articles/vpn-gateway-resetgw-classic/",
          "actionUriText": "resetting VPN gateway"
        },
        {
          "actionText": "If you are experiencing problems you believe are caused by Azure, contact support",
          "actionUri": "https://azure.microsoft.com/support",
          "actionUriText": "contact support"
        }
      ]
    }
  ]
}
```

## <a name="understanding-the-results"></a>Sonuçları anlama

Eylem metni, sorunun nasıl çözüleceği hakkında genel rehberlik sağlar. Sorun için bir eylem uygulanabilir ise, ek kılavuzlarla bir bağlantı sağlanır. Ek bir kılavuz olmadığı durumlarda yanıt, bir destek talebi açmak için URL 'yi sağlar.  Yanıtın özellikleri ve dahil olan özellikler hakkında daha fazla bilgi için [ağ Izleyicisi sorunlarını giderme genel bakış](network-watcher-troubleshoot-overview.md) ' ı ziyaret edin

Azure depolama hesaplarından dosya indirme yönergeleri için bkz. [.NET kullanarak Azure Blob depolamayı kullanmaya başlama](../storage/blobs/storage-quickstart-blobs-dotnet.md). Kullanılabilecek başka bir araç Depolama Gezgini. Depolama Gezgini hakkında daha fazla bilgi aşağıdaki bağlantıda bulunabilir: [Depolama Gezgini](https://storageexplorer.com/)

## <a name="next-steps"></a>Sonraki adımlar

VPN bağlantısını durduran ayarlar değiştirildiyse, ağ güvenlik grubunu ve söz konusu olabilecek güvenlik kurallarını izlemek için [ağ güvenlik gruplarını yönetme](../virtual-network/manage-network-security-group.md) bölümüne bakın.