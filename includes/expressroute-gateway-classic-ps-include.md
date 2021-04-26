---
title: include dosyası
description: include dosyası
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 12/13/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c0c05eb88df27ba9e71c0f61bad75457d893a7ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95559827"
---
> [!NOTE]
> Bu örnekler, S2S/ExpressRoute ile birlikte bulunan yapılandırmalara uygulanmaz.
> Birlikte bulunan bir yapılandırmada ağ geçitleri ile çalışma hakkında daha fazla bilgi için bkz [. birlikte bulunan bağlantıları yapılandırma.](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>Ağ geçidi ekleme

Klasik kaynak modeli kullanarak bir sanal ağa ağ geçidi eklediğinizde ağ geçidini oluşturmadan önce ağ yapılandırma dosyasını doğrudan değiştirirsiniz. Bir ağ geçidi oluşturmak için aşağıdaki örneklerdeki değerler dosyasında bulunmalıdır. Sanal ağınızda daha önce kendisiyle ilişkili bir ağ geçidi varsa, bu değerlerden bazıları zaten mevcut olacaktır. Dosyayı aşağıdaki değerleri yansıtacak şekilde değiştirin.

### <a name="download-the-network-configuration-file"></a>Ağ yapılandırma dosyasını indir

1. [Ağ yapılandırma dosyası makalesindeki adımları](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file) kullanarak ağ yapılandırma dosyasını indirin. Dosyayı bir metin düzenleyicisi kullanarak açın.
2. Dosyasına bir yerel ağ sitesi ekleyin. Herhangi bir geçerli adres ön ekini kullanabilirsiniz. VPN ağ geçidi için herhangi bir geçerli IP adresi ekleyebilirsiniz. Bu bölümdeki adres değerleri ExpressRoute işlemleri için kullanılmaz, ancak dosya doğrulaması için gereklidir. Örnekte, "Branch1" sitenin adıdır. Farklı bir ad kullanabilirsiniz, ancak dosyanın ağ geçidi bölümünde aynı değeri kullandığınızdan emin olabilirsiniz.

   ```
   <VirtualNetworkConfiguration>
    <Dns />
    <LocalNetworkSites>
      <LocalNetworkSite name="branch1">
        <AddressSpace>
          <AddressPrefix>165.3.1.0/27</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>3.2.1.4</VPNGatewayAddress>
    </LocalNetworkSite>
   ```
3. VirtualNetworkSites öğesine gidin ve alanları değiştirin.

   * Ağ geçidi alt ağının sanal ağınız için mevcut olduğunu doğrulayın. Yoksa, şu anda bir tane ekleyebilirsiniz. Ad "GatewaySubnet" olmalıdır.
   * Dosyanın ağ geçidi bölümünün var olduğunu doğrulayın. Değilse, ekleyin. Bu, sanal ağın yerel ağ sitesiyle (bağlanmakta olduğunuz ağı temsil eder) ilişkilendirilmesi için gereklidir.
   * Bağlantı türü = adanmış olduğunu doğrulayın. ExpressRoute bağlantıları için bu gereklidir.

   ```
   </LocalNetworkSites>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myAzureVNET" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="default">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.1.0/27</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="branch1">
              <Connection type="Dedicated" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
   </VirtualNetworkConfiguration>
   </NetworkConfiguration>
   ```
4. Dosyayı kaydedin ve Azure 'a yükleyin.

### <a name="create-the-gateway"></a>Ağ geçidini oluşturma

Bir ağ geçidi oluşturmak için aşağıdaki komutu kullanın. Tüm değerleri kendi değerlerinizle değiştirin.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Ağ geçidinin oluşturulduğunu doğrulayın

Ağ geçidinin oluşturulduğunu doğrulamak için aşağıdaki komutu kullanın. Bu komut, diğer işlemler için gerekli olan ağ geçidi KIMLIĞINI de alır.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Ağ geçidini yeniden boyutlandırma

Birçok [ağ geçidi SKU](../articles/expressroute/expressroute-about-virtual-network-gateways.md)'su vardır. Ağ Geçidi SKU 'sunu dilediğiniz zaman değiştirmek için aşağıdaki komutu kullanabilirsiniz.

> [!IMPORTANT]
> Bu komut UltraPerformance ağ geçidi için çalışmıyor. Ağ geçidinizi bir UltraPerformance ağ geçidine dönüştürmek için, önce mevcut ExpressRoute Gateway 'i kaldırın ve ardından yeni bir UltraPerformance Ağ Geçidi oluşturun. Ağ geçidinizin bir UltraPerformance ağ geçidiyle indirgenmesini sağlamak için önce UltraPerformance ağ geçidini kaldırın ve ardından yeni bir ağ geçidi oluşturun.
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>Ağ geçitlerini kaldırma

Bir ağ geçidini kaldırmak için aşağıdaki komutu kullanın

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```