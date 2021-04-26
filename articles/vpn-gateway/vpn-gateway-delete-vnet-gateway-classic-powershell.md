---
title: 'Bir sanal ağ geçidini silme: Azure klasik'
description: Klasik dağıtım modelinde PowerShell kullanarak bir sanal ağ geçidini silin.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 39585a68c5cddc50cd04e82caca71209270f7b68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91874127"
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>PowerShell kullanarak bir sanal ağ geçidini silme (klasik)

> [!div class="op_single_selector"]
> * [Kaynak Yöneticisi Azure portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Klasik-PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>

Bu makale, PowerShell kullanarak klasik dağıtım modelinde bir VPN ağ geçidini silmenizi sağlar. Sanal ağ geçidi silindikten sonra, artık kullanmadığınız öğeleri kaldırmak için ağ yapılandırma dosyasını değiştirin.

## <a name="step-1-connect-to-azure"></a><a name="connect"></a>1. Adım: Azure 'a bağlanma

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. en son PowerShell cmdlet 'lerini yükler.

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="2-connect-to-your-azure-account"></a>2. Azure hesabınıza bağlanın.

PowerShell konsolunuzu yükseltilmiş haklarla açın ve hesabınıza bağlanın. Bağlanmanıza yardımcı olması için aşağıdaki örneği kullanın:

1. PowerShell konsolunuzu yükseltilmiş haklarla açın.
2. Hesabınıza bağlanın. Bağlanmanıza yardımcı olması için aşağıdaki örneği kullanın:

   ```powershell
   Add-AzureAccount
   ```

## <a name="step-2-export-and-view-the-network-configuration-file"></a><a name="export"></a>2. Adım: ağ yapılandırma dosyasını dışarı aktarma ve görüntüleme

Bilgisayarınızda bir dizin oluşturun ve sonra ağ yapılandırma dosyasını dizine aktarın. Bu dosyayı, her ikisi de geçerli yapılandırma bilgilerini görüntülemek ve ağ yapılandırmasını değiştirmek için kullanırsınız.

Bu örnekte, ağ yapılandırma dosyası C:\AzureNet dizinine aktarılır.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Dosyayı bir metin düzenleyici ile açın ve klasik sanal ağınızın adını görüntüleyin. Azure portal bir sanal ağ oluşturduğunuzda, Azure 'un kullandığı tam ad portalda görünmez. Örneğin, Azure portal adı ' ClassicVNet1 ' olarak görünen bir VNet, ağ yapılandırma dosyasında çok daha uzun bir ada sahip olabilir. Ad şöyle görünebilir: ' Group ClassicRG1 ClassicVNet1 '. Sanal ağ adları **' Virtualnetworksite Name = '** olarak listelenir. PowerShell cmdlet 'lerinizi çalıştırırken ağ yapılandırma dosyasındaki adları kullanın.

## <a name="step-3-delete-the-virtual-network-gateway"></a><a name="delete"></a>3. Adım: sanal ağ geçidini silme

Bir sanal ağ geçidini sildiğinizde, ağ geçidi aracılığıyla VNet 'e yönelik tüm bağlantıların bağlantısı kesilir. VNet 'e bağlı P2S istemcileriniz varsa, bunlar uyarı vermeden kesilir.

Bu örnek, sanal ağ geçidini siler. Ağ yapılandırma dosyasından sanal ağın tam adını kullandığınızdan emin olun.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

Başarılı olursa, dönüş şunu gösterir:

```
Status : Successful
```

## <a name="step-4-modify-the-network-configuration-file"></a><a name="modify"></a>4. Adım: ağ yapılandırma dosyasını değiştirme

Bir sanal ağ geçidini sildiğinizde, cmdlet ağ yapılandırma dosyasını değiştirmez. Artık kullanılmayan öğeleri kaldırmak için dosyayı değiştirmeniz gerekir. Aşağıdaki bölümler, indirdiğiniz ağ yapılandırma dosyasını değiştirmenize yardımcı olur.

### <a name="local-network-site-references"></a><a name="lnsref"></a>Yerel ağ sitesi başvuruları

Site başvuru bilgilerini kaldırmak için **Connectionstolocalnetwork/LocalNetworkSiteRef** üzerinde yapılandırma değişiklikleri yapın. Yerel bir site başvurusunu kaldırma, bir tüneli silmek için Azure 'ı tetikler. Oluşturduğunuz yapılandırmaya bağlı olarak, listelenmiş bir **LocalNetworkSiteRef** bulunmayabilir.

```
<Gateway>
   <ConnectionsToLocalNetwork>
     <LocalNetworkSiteRef name="D1BFC9CB_Site2">
       <Connection type="IPsec" />
     </LocalNetworkSiteRef>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

Örnek:

```
<Gateway>
   <ConnectionsToLocalNetwork>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

### <a name="local-network-sites"></a><a name="lns"></a>Yerel ağ siteleri

Artık kullanmadığınız tüm yerel siteleri kaldırın. Oluşturduğunuz yapılandırmaya bağlı olarak, listelenen bir **Localnetworksitenizin** olması mümkündür.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
  <LocalNetworkSite name="Site3">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>57.179.18.164</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

Bu örnekte, yalnızca Site3 ' ı kaldırdık.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

### <a name="client-addresspool"></a><a name="clientaddresss"></a>İstemci adres havuzu

VNet 'iniz için P2S bağlantınız varsa, bir **Vpnclientaddresspool** olur. Sildiğiniz sanal ağ geçidine karşılık gelen istemci adresi havuzlarını kaldırın.

```
<Gateway>
    <VPNClientAddressPool>
      <AddressPrefix>10.1.0.0/24</AddressPrefix>
    </VPNClientAddressPool>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

Örnek:

```
<Gateway>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

### <a name="gatewaysubnet"></a><a name="gwsub"></a>GatewaySubnet

VNet 'e karşılık gelen **Gatewaysubnet** 'i silin.

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
   <Subnet name="GatewaySubnet">
     <AddressPrefix>10.11.1.0/29</AddressPrefix>
   </Subnet>
 </Subnets>
```

Örnek:

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
 </Subnets>
```

## <a name="step-5-upload-the-network-configuration-file"></a><a name="upload"></a>5. Adım: ağ yapılandırma dosyasını karşıya yükleme

Değişikliklerinizi kaydedin ve ağ yapılandırma dosyasını Azure 'a yükleyin. Dosya yolunu ortamınız için gerektiği gibi değiştirdiğinizden emin olun.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Başarılı olursa, return bu örneğe benzer bir şey gösterir:

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded
```
