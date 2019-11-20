---
title: Azure 'da SAP HANA sanal ağdan bağlantı kurma (büyük örnekler) | Microsoft Docs
description: Azure 'da SAP HANA kullanmak için sanal ağdan bağlantı kurun (büyük örnekler).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 547640ab1a6dd948cf5d17279d784e1b4a37b35e
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101246"
---
# <a name="connect-a-virtual-network-to-hana-large-instances"></a>Sanal ağı HANA büyük örneklerine bağlama

Azure sanal ağını oluşturduktan sonra, bu ağı Azure büyük örneklerinde SAP HANA bağlayabilirsiniz. Sanal ağ üzerinde bir Azure ExpressRoute Ağ Geçidi oluşturun. Bu ağ geçidi, sanal ağı HANA büyük örnek damgasında müşteri kiracıya bağlanan ExpressRoute bağlantı hattına bağlanmanızı sağlar.

> [!NOTE] 
> Bu adımın tamamlanması 30 dakika kadar sürebilir. Yeni ağ geçidi, belirlenen Azure aboneliğinde oluşturulur ve ardından belirtilen Azure sanal ağına bağlanır.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

Bir ağ geçidi zaten varsa, bir ExpressRoute ağ geçidi olup olmadığını kontrol edin. ExpressRoute ağ geçidi değilse, ağ geçidini silin ve ExpressRoute ağ geçidi olarak yeniden oluşturun. Bir ExpressRoute ağ geçidi zaten kurulduysa, "sanal ağları bağlama" başlıklı bu makalenin aşağıdaki bölümüne bakın. 

- Sanal ağınıza bağlı bir ExpressRoute VPN Gateway oluşturmak için [Azure Portal](https://portal.azure.com/) ya da PowerShell kullanın.
  - Azure portal kullanıyorsanız, yeni bir **sanal ağ geçidi**ekleyin ve ardından ağ geçidi türü olarak **ExpressRoute** ' u seçin.
  - PowerShell kullanıyorsanız, önce en son [Azure PowerShell SDK 'sını](https://azure.microsoft.com/downloads/)indirip kullanın. 
 
Aşağıdaki komutlar bir ExpressRoute ağ geçidi oluşturur. Önünde bir _$_ olan metinler, belirli bilgileriniz ile güncelleştirilmeleri gereken kullanıcı tanımlı değişkenlerdir.

```powershell
# These Values should already exist, update to match your environment
$myAzureRegion = "eastus"
$myGroupName = "SAP-East-Coast"
$myVNetName = "VNet01"

# These values are used to create the gateway, update for how you wish the GW components to be named
$myGWName = "VNet01GW"
$myGWConfig = "VNet01GWConfig"
$myGWPIPName = "VNet01GWPIP"
$myGWSku = "HighPerformance" # Supported values for HANA large instances are: HighPerformance or UltraPerformance

# These Commands create the Public IP and ExpressRoute Gateway
$vnet = Get-AzVirtualNetwork -Name $myVNetName -ResourceGroupName $myGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
New-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName `
-Location $myAzureRegion -AllocationMethod Dynamic
$gwpip = Get-AzPublicIpAddress -Name $myGWPIPName -ResourceGroupName $myGroupName
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name $myGWConfig -SubnetId $subnet.Id `
-PublicIpAddressId $gwpip.Id

New-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName -Location $myAzureRegion `
-IpConfigurations $gwipconfig -GatewayType ExpressRoute `
-GatewaySku $myGWSku -VpnType PolicyBased -EnableBgp $true
```

Bu örnekte, HighPerformance ağ geçidi SKU 'SU kullanılmıştır. Seçenekleriniz, Azure 'daki SAP HANA (büyük örnekler) için desteklenen tek ağ geçidi SKU 'Ları olarak yüksek performans veya UltraPerformance.

> [!IMPORTANT]
> Tür II sınıf SKU 'sunda HANA büyük örnekleri için UltraPerformance ağ geçidi SKU 'sunu kullanmanız gerekir.

## <a name="link-virtual-networks"></a>Sanal ağları bağlama

Azure sanal ağında artık bir ExpressRoute ağ geçidi vardır. ExpressRoute ağ geçidini SAP HANA Büyük Örnekleri ExpressRoute devresine bağlamak için Microsoft tarafından sunulan yetkilendirme bilgilerini kullanın. Azure portal veya PowerShell kullanarak bağlanabilirsiniz. PowerShell yönergeleri aşağıdaki gibidir. 

Her bir bağlantı için farklı bir AuthGUID kullanarak her bir ExpressRoute ağ geçidi için aşağıdaki komutları çalıştırın. Aşağıdaki betikte gösterilen ilk iki giriş, Microsoft tarafından verilen bilgilerden gelir. Ayrıca, Authguıd her sanal ağ ve ağ geçidi için özeldir. Başka bir Azure sanal ağı eklemek istiyorsanız, ExpressRoute bağlantı hattı için Microsoft 'tan Azure 'da HANA büyük örnekleri bağlayan başka bir Authıd almanız gerekir. 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

> [!NOTE]
> New-AzVirtualNetworkGatewayConnection komutundaki son parametre olan **Expressroutegatewaybypass** , ExpressRoute hızlı yolunu sağlayan yeni bir parametredir. HANA büyük örnek birimleriniz ile Azure VM 'Ler arasındaki ağ gecikmesini azaltan bir işlev. İşlevselliği 2019 Mayıs 'ta eklenmiştir. Daha fazla ayrıntı için [SAP HANA (büyük örnekler) ağ mimarisi](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)makalesine bakın. Komutları çalıştırmadan önce PowerShell cmdlet 'lerinin en son sürümünü çalıştırdığınızdan emin olun.

Ağ geçidini aboneliğinizle ilişkili birden fazla ExpressRoute devresine bağlamak için bu adımı birden çok kez çalıştırmanız gerekebilir. Örneğin, büyük olasılıkla aynı sanal ağ geçidini, sanal ağı şirket içi ağınıza bağlayan ExpressRoute bağlantı hattına bağlayacağız.

## <a name="applying-expressroute-fast-path-to-existing-hana-large-instance-expressroute-circuits"></a>Mevcut HANA büyük örnek ExpressRoute devresine ExpressRoute hızlı yolu uygulanıyor
Belgeler, bir HANA büyük örnek dağıtımıyla oluşturulan yeni bir ExpressRoute devresine Azure sanal ağlarınızdan birinin Azure ExpressRoute ağ geçidine nasıl bağlayabileceğini açıklamaktadır. Ancak birçok müşteri zaten ExpressRoute devreleri kurulumuna sahiptir ve sanal ağlarının zaten HANA büyük örneklerine bağlı olmasını sağlar. Yeni ExpressRoute hızlı yolu ağ gecikmesini azalttığından, bu işlevselliği kullanmak için değişikliği uygulamanız önerilir. Yeni bir ExpreesRoute devresini bağlama ve mevcut bir ExpressRoute devresini değiştirme komutları aynı. Sonuç olarak, mevcut bir bağlantı hattını kullanmak üzere değiştirmek için bu PowerShell komutları dizisini çalıştırmanız gerekir 

```powershell
# Populate with information provided by Microsoft Onboarding team
$PeerID = "/subscriptions/9cb43037-9195-4420-a798-f87681a0e380/resourceGroups/Customer-USE-Circuits/providers/Microsoft.Network/expressRouteCircuits/Customer-USE01"
$AuthGUID = "76d40466-c458-4d14-adcf-3d1b56d1cd61"

# Your ExpressRoute Gateway information
$myGroupName = "SAP-East-Coast"
$myGWName = "VNet01GW"
$myGWLocation = "East US"

# Define the name for your connection
$myConnectionName = "VNet01GWConnection"

# Create a new connection between the ER Circuit and your Gateway using the Authorization
$gw = Get-AzVirtualNetworkGateway -Name $myGWName -ResourceGroupName $myGroupName
    
New-AzVirtualNetworkGatewayConnection -Name $myConnectionName `
-ResourceGroupName $myGroupName -Location $myGWLocation -VirtualNetworkGateway1 $gw `
-PeerId $PeerID -ConnectionType ExpressRoute -AuthorizationKey $AuthGUID -ExpressRouteGatewayBypass
```

ExpressRoute hızlı yolu işlevselliğini etkinleştirmek için yukarıda görüntülenen son parametreyi eklemeniz önemlidir


## <a name="expressroute-global-reach"></a>ExpressRoute Global Reach
İki senaryonun biri veya her ikisi için Global Reach etkinleştirmek istiyorsanız:

 - Ek proxy veya güvenlik duvarları olmadan HANA sistem çoğaltması
 - Sistem kopyaları veya sistem yenilemeleri gerçekleştirmek üzere iki farklı bölgedeki HANA büyük örnek birimleri arasında yedeklemeleri kopyalama

şunları göz önünde bulundurmanız gerekir:

- Bir/29 adres alanının adres alanı aralığını sağlamanız gerekir. Bu adres aralığı, HANA büyük örneklerini Azure 'a bağlayan ve Azure 'da veya şirket içinde başka herhangi bir yerde kullandığınız IP adresi aralığınızda çakışmayacak şekilde kullandığınız diğer adres alanı aralıklarıyla çakışmayabilir.
- Şirket içi rotalarınızı HANA büyük örneklerine tanıtmak için kullanılabilen ASNs (otonom sistem numarası) üzerinde bir sınırlama vardır. Şirket içi ağınız, 65000 – 65020 veya 65515 aralığında özel ASNs ile hiçbir yol bildirmemelidir. 
- HANA büyük örneklerine şirket içi doğrudan erişim bağlama senaryosu için sizi Azure 'a bağlayan bağlantı ücretini hesaplamanız gerekir. Fiyatlar için [Global Reach eklentisinin](https://azure.microsoft.com/pricing/details/expressroute/)fiyatlarını kontrol edin.

Dağıtımınıza uygulanan senaryolardan birini veya her ikisini almak için, Azure ile bir destek iletisi açın ve [Hana büyük örnekler için destek Isteği açma](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal#open-a-support-request-for-hana-large-instances) bölümünde açıklandığı gibi

Gerekli veriler ve Microsoft 'un isteğiniz üzerinde yönlendirimek ve yürütebilmesi için kullanmanız gereken anahtar sözcükler şöyle görünür:

- Hizmet: SAP HANA Büyük Örneği
- Sorun türü: Yapılandırma ve Kurulum
- Sorun alt türü: Sorunum yukarıdaki listede değil
- Konu ' ağımı Değiştir-Global Reach Ekle '
- Ayrıntılar: ' Hana büyük örnek kiracısına Global Reach HANA büyük örneğe veya ' Şirket içi olarak HANA büyük örnek kiracısına Global Reach ekleyin.
- Hana büyük örneği için HANA büyük örnek kiracı örneğine yönelik ek ayrıntılar: Bağlantı yapılacak iki kiracının bulunduğu **ve** **/29 IP adresi aralığını** göndermeniz gereken **iki Azure bölgesini** tanımlamanız gerekir
- Şirket içi ve HANA büyük örnek kiracısı için ek ayrıntılar: HANA büyük örnek kiracının dağıtıldığı **Azure bölgesini** tanımlamanız gerekir. Ayrıca, şirket içi ve Azure arasında ExpressRoute bağlantı hattını oluştururken aldığınız **kimlik doğrulama GUID 'si** ve **devre eşi kimliğini** sağlamanız gerekir. Ayrıca, **ASN**'nizi de yazmanız gerekir. Son teslim edilebiliri, ExpressRoute Global Reach için bir **/29 IP adresi aralığıdır** .

> [!NOTE]
> Her iki durumu da ele almak istiyorsanız, şimdiye kadar kullanılan başka bir IP adresi aralığıyla çakışmayacak iki farklı/29 IP adresi aralığı sağlamanız gerekir. 




## <a name="next-steps"></a>Sonraki adımlar

- [HLI için ek ağ gereksinimleri](hana-additional-network-requirements.md)
