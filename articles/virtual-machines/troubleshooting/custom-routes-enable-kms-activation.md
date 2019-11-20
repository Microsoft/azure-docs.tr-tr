---
title: Zorlamalı tünel ile KMS etkinleştirmesini etkinleştirmek için Azure özel yollarını kullanın | Microsoft Docs
description: Azure 'da Zorlamalı tünel kullanırken KMS etkinleştirmesini etkinleştirmek için Azure özel yollarının nasıl kullanılacağını gösterir.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 12/20/2018
ms.author: genli
ms.openlocfilehash: 47ff8870df7c89ee2ab3e48e064e31aa581f65f0
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748641"
---
# <a name="windows-activation-fails-in-forced-tunneling-scenario"></a>Zorlamalı tünel senaryosunda Windows etkinleştirme başarısız oluyor

Bu makalede, siteden siteye VPN bağlantısı veya ExpressRoute senaryolarında zorlamalı tüneli etkinleştirdiğinizde karşılaşabileceğiniz KMS etkinleştirme sorununun nasıl çözümleneceği açıklanır.

## <a name="symptom"></a>Belirti

Internet 'e bağlı tüm trafiği şirket içi ağınıza geri yönlendirmek için Azure sanal ağ alt ağlarında [zorlamalı tüneli](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) etkinleştirin. Bu senaryoda, Windows çalıştıran Azure sanal makineleri (VM) Windows 'u etkinleştirmek için başarısız olur.

## <a name="cause"></a>Nedeni

Azure Windows VM 'lerinin Windows etkinleştirme için Azure KMS sunucusuna bağlanması gerekir. Etkinleştirme, etkinleştirme isteğinin bir Azure genel IP adresinden gelmesini gerektirir. Zorlamalı tünel senaryosunda, etkinleştirme isteği Azure genel IP adresi yerine şirket içi ağınızdan geldiği için etkinleştirme başarısız olur.

## <a name="solution"></a>Çözüm

Bu sorunu çözmek için etkinleştirme trafiğini Azure KMS sunucusuna yönlendirmek üzere Azure özel yolunu kullanın.

Azure genel bulutu için KMS sunucusunun IP adresi 23.102.135.246 ' dir. DNS adı kms.core.windows.net ' dir. Azure Almanya gibi diğer Azure platformlarını kullanıyorsanız, karşılık gelen KMS sunucusunun IP adresini kullanmanız gerekir. Daha fazla bilgi için aşağıdaki tabloya bakın:

|Platform| KMS DNS|KMS ıP 'SI|
|------|-------|-------|
|Azure genel|kms.core.windows.net|23.102.135.246|
|Azure Almanya|kms.core.cloudapi.de|51.4.143.248|
|Azure US Government|kms.core.usgovcloudapi.net|23.97.0.13|
|Azure Çin 21Vianet|kms.core.chinacloudapi.cn|42.159.7.249|


Özel yolu eklemek için aşağıdaki adımları izleyin:

### <a name="for-resource-manager-vms"></a>Kaynak Yöneticisi VM 'Ler için

 

> [!NOTE] 
> Etkinleştirme, genel IP adreslerini kullanır ve standart SKU Load Balancer yapılandırmasından etkilenecek. Gereksinimler hakkında bilgi edinmek için [Azure 'Daki giden bağlantıları](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) dikkatle gözden geçirin.

1. Azure PowerShell açın ve ardından [Azure aboneliğinizde oturum açın](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
2. Aşağıdaki komutları çalıştırın:

    ```powershell
    # First, get the virtual network that hosts the VMs that have activation problems. In this case, we get virtual network ArmVNet-DM in Resource Group ArmVNet-DM:

    $vnet = Get-AzVirtualNetwork -ResourceGroupName "ArmVNet-DM" -Name "ArmVNet-DM"

    # Next, create a route table and specify that traffic bound to the KMS IP (23.102.135.246) will go directly out:

    $RouteTable = New-AzRouteTable -Name "ArmVNet-DM-KmsDirectRoute" -ResourceGroupName "ArmVNet-DM" -Location "centralus"

    Add-AzRouteConfig -Name "DirectRouteToKMS" -AddressPrefix 23.102.135.246/32 -NextHopType Internet -RouteTable $RouteTable

    Set-AzRouteTable -RouteTable $RouteTable

    # Next, attach the route table to the subnet that hosts the VMs

    Set-AzVirtualNetworkSubnetConfig -Name "Subnet01" -VirtualNetwork $vnet -AddressPrefix "10.0.0.0/24" -RouteTable $RouteTable

    Set-AzVirtualNetwork -VirtualNetwork $vnet
    ```
3. Etkinleştirme sorunları olan VM 'ye gidin. KMS sunucusuna ulaşabilmesini sınamak için [Psping](https://docs.microsoft.com/sysinternals/downloads/psping) kullanın:

        psping kms.core.windows.net:1688

4. Windows 'u etkinleştirmeyi deneyin ve sorunun çözümlenip çözümlenmediğini görün.

### <a name="for-classic-vms"></a>Klasik VM 'Ler için

1. Azure PowerShell açın ve ardından [Azure aboneliğinizde oturum açın](https://docs.microsoft.com/powershell/azure/authenticate-azureps).
2. Aşağıdaki komutları çalıştırın:

    ```powershell
    # First, create a new route table:
    New-AzureRouteTable -Name "VNet-DM-KmsRouteGroup" -Label "Route table for KMS" -Location "Central US"

    # Next, get the route table that was created:
    $rt = Get-AzureRouteTable -Name "VNet-DM-KmsRouteTable"

    # Next, create a route:
    Set-AzureRoute -RouteTable $rt -RouteName "AzureKMS" -AddressPrefix "23.102.135.246/32" -NextHopType Internet

    # Apply the KMS route table to the subnet that hosts the problem VMs (in this case, we apply it to the subnet that's named Subnet-1):
    Set-AzureSubnetRouteTable -VirtualNetworkName "VNet-DM" -SubnetName "Subnet-1" 
    -RouteTableName "VNet-DM-KmsRouteTable"
    ```

3. Etkinleştirme sorunları olan VM 'ye gidin. KMS sunucusuna ulaşabilmesini sınamak için [Psping](https://docs.microsoft.com/sysinternals/downloads/psping) kullanın:

        psping kms.core.windows.net:1688

4. Windows 'u etkinleştirmeyi deneyin ve sorunun çözümlenip çözümlenmediğini görün.

## <a name="next-steps"></a>Sonraki adımlar

- [KMS Istemcisi kurulum anahtarları](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys
)
- [Etkinleştirme yöntemlerini gözden geçirme ve seçme](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134256(v=ws.11)
)
