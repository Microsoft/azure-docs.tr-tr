---
title: 'Sanal WAN: NVA: Azure PowerShell sanal hub yol tablosu oluşturma'
description: Sanal WAN sanal hub yol tablosu, trafiği bir ağ sanal gerecine yönlendiren.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 6a6e701377956e39696567eff9a6a0abca927b88
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055085"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>Trafiği bir ağ sanal gerecine yönlendiren bir sanal hub yol tablosu oluşturun

Bu makalede, bir sanal hub 'dan bir ağ sanal gerecine nasıl trafik ekleneceği gösterilmektedir. 

![Sanal WAN diyagramı](./media/virtual-wan-route-table-nva/vwanroute.png)

Bu makalede şunları öğreneceksiniz:

* WAN oluşturma
* Hub oluşturma
* Hub sanal ağ bağlantıları oluşturma
* Hub yolu oluşturma
* Yönlendirme tablosu oluşturma
* Yol tablosunu uygulama

## <a name="before-you-begin"></a>Başlamadan önce

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Aşağıdaki ölçütleri karşıladığınızı doğrulayın:

* Bir ağ sanal gereci (NVA) vardır. Bu, genellikle bir sanal ağda Azure Marketi 'nden sağlanan bir üçüncü taraf yazılımdır.
* NVA ağ arabirimine atanmış özel bir IP 'si var. 
* NVA sanal hub 'da dağıtılamıyor. Ayrı bir sanal ağa dağıtılması gerekir. Bu makalede, NVA VNet ' DMZ VNet ' olarak adlandırılır.
* ' DMZ VNet ' öğesine bağlı bir veya daha fazla sanal ağ olabilir. Bu makalede, bu VNet ' dolaylı bağlı olan VNet ' olarak adlandırılır. Bu sanal ağlar VNet eşlemesi kullanılarak DMZ VNet 'e bağlanabilir.
* Daha önce oluşturulmuş 2 sanal ağlarınızın olduğunu doğrulayın. Bunlar, bağlı olan VNET 'ler olarak kullanılacaktır. Bu makalede, VNet bağlı bileşen adres alanları 10.0.2.0/24 ve 10.0.3.0/24 ' dir. VNet oluşturma hakkında bilgi için bkz. [PowerShell kullanarak sanal ağ oluşturma](../virtual-network/quick-create-powershell.md).
* Hiçbir VNET 'te sanal ağ geçidi bulunmadığından emin olun.

## <a name="1-sign-in"></a><a name="signin"></a>1. oturum aç

Kaynak Yöneticisi PowerShell cmdlet 'lerinin en son sürümünü yüklediğinizden emin olun. PowerShell cmdlet'lerini yükleme hakkında daha fazla bilgi için bkz. [Azure PowerShell'i yükleme ve yapılandırma](/powershell/azure/install-az-ps). Bu önemlidir, çünkü cmdlet’lerin daha önceki sürümleri bu alıştırma için gereken geçerli değerleri içermez.

1. PowerShell konsolunuzu yükseltilmiş ayrıcalıklarla açın ve Azure hesabınızda oturum açın. Bu cmdlet sizden oturum açma kimlik bilgilerini ister. Oturum açtıktan sonra, Azure PowerShell için kullanılabilir olmaları için hesap ayarlarınızı indirir.

   ```powershell
   Connect-AzAccount
   ```
2. Azure aboneliklerinizin bir listesini alın.

   ```powershell
   Get-AzSubscription
   ```
3. Kullanmak istediğiniz aboneliği belirtin.

   ```powershell
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```

## <a name="2-create-resources"></a><a name="rg"></a>2. kaynak oluşturma

1. Bir kaynak grubu oluşturun.

   ```powershell
   New-AzResourceGroup -Location "West US" -Name "testRG"
   ```
2. Sanal WAN oluşturun.

   ```powershell
   $virtualWan = New-AzVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
   ```
3. Sanal bir hub oluşturun.

   ```powershell
   New-AzVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24" -Location "West US"
   ```

## <a name="3-create-connections"></a><a name="connections"></a>3. bağlantı oluşturma

Dolaylı bağlı olan VNet 'ten ve DMZ VNet 'ten sanal hub 'a hub sanal ağ bağlantıları oluşturun.

  ```powershell
  $remoteVirtualNetwork1= Get-AzVirtualNetwork -Name "indirectspoke1" -ResourceGroupName "testRG"
  $remoteVirtualNetwork2= Get-AzVirtualNetwork -Name "indirectspoke2" -ResourceGroupName "testRG"
  $remoteVirtualNetwork3= Get-AzVirtualNetwork -Name "dmzvnet" -ResourceGroupName "testRG"

  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection1" -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection2" -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection3" -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="4-create-a-virtual-hub-route"></a><a name="route"></a>4. sanal hub yolu oluşturma

Bu makalede, dolaylı bağlı olan VNet adres alanları 10.0.2.0/24 ve 10.0.3.0/24 ' dir ve DMZ NVA ağ arabirimi özel IP adresi 10.0.4.5 ' dir.

```powershell
$route1 = New-AzVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="5-create-a-virtual-hub-route-table"></a><a name="applyroute"></a>5. bir sanal hub yol tablosu oluşturun

Bir sanal hub yol tablosu oluşturun, sonra oluşturulan yolu buna uygulayın.
 
```powershell
$routeTable = New-AzVirtualHubRouteTable -Route @($route1)
```

## <a name="6-commit-the-changes"></a><a name="commit"></a>6. değişiklikleri işleyin

Değişiklikleri sanal hub 'a işleyin.

```powershell
Update-AzVirtualHub -ResourceGroupName "testRG" -Name "westushub" -RouteTable $routeTable
```

## <a name="next-steps"></a>Sonraki adımlar

Sanal WAN hakkında daha fazla bilgi için [Sanal WAN'a Genel Bakış](virtual-wan-about.md) sayfasına bakın.
