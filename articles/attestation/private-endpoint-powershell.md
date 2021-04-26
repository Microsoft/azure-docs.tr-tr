---
title: Azure PowerShell kullanarak özel uç nokta oluşturma
description: Azure PowerShell kullanarak Azure kanıtlama için özel bir uç nokta oluşturma
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 03/26/2021
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ceefd4695583822536d8cc4c14614af7f6736f70
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830133"
---
# <a name="quickstart-create-a-private-endpoint-using-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell kullanarak özel uç nokta oluşturma

Azure kanıtlama ile güvenli bir şekilde bağlanmak için özel bir uç nokta kullanarak Azure özel bağlantısı ile çalışmaya başlayın.

Bu hızlı başlangıçta, Azure kanıtlama için özel bir uç nokta oluşturacak ve özel bağlantıyı test etmek için bir sanal makine dağıtacaksınız.  

> [!NOTE]
> Geçerli uygulama yalnızca otomatik onay seçeneğini içerir. Özel uç nokta oluşturma işlemiyle devam edebilmek için aboneliğin izin verilenler listesine eklenmesi gerekir. Aşağıdaki adımlara geçmeden önce lütfen hizmet ekibine ulaşın veya [Azure destek sayfasında](https://azure.microsoft.com/support/options/) bir Azure destek isteği gönderebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

* [Azure özel bağlantısı](../private-link/private-link-overview.md) hakkında bilgi edinin
* [Azure kanıtlama 'nı Azure PowerShell ile ayarlama](./quickstart-powershell.md)

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)ile bir kaynak grubu oluşturun:

```azurepowershell-interactive
## Create to your Azure account subscription and create a resource group in a desired location. ##
Connect-AzAccount
Set-AzSubscription “mySubscription”
$rg = “CreateAttestationPrivateLinkTutorial-rg”
$loc= "eastus”
New-AzResourceGroup -Name $rg -Location $loc
```

## <a name="create-a-virtual-network-and-bastion-host"></a>Sanal ağ ve savunma Konağı oluşturma

Bu bölümde, bir sanal ağ, alt ağ ve savunma ana bilgisayarı oluşturacaksınız. 

Savunma Konağı, Özel uç noktasını test etmek üzere sanal makineye güvenli bir şekilde bağlanmak için kullanılacaktır.

İle bir sanal ağ ve savunma Konağı oluşturun:

* [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)
* [New-Azpublicıpaddress](/powershell/module/az.network/new-azpublicipaddress)
* [Yeni Aztasyon](/powershell/module/az.network/new-azbastion)

```azurepowershell-interactive
## Create backend subnet config. ##
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name myBackendSubnet -AddressPrefix 10.0.0.0/24

## Create Azure Bastion subnet. ##
$bastsubnetConfig = New-AzVirtualNetworkSubnetConfig -Name AzureBastionSubnet -AddressPrefix 10.0.1.0/24

## Create the virtual network. ##
$vnet = New-AzVirtualNetwork -Name "myAttestationTutorialVNet" -ResourceGroupName $rg -Location $loc -AddressPrefix "10.0.0.0/16" -Subnet $subnetConfig, $bastsubnetConfig

## Create public IP address for bastion host. ##
$publicip = New-AzPublicIpAddress -Name "myBastionIP" -ResourceGroupName $rg -Location $loc -Sku "Standard" -AllocationMethod "Static"

## Create bastion host ##
New-AzBastion -ResourceGroupName $rg -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
```

Azure savunma konağının dağıtılması birkaç dakika sürebilir.

## <a name="create-test-virtual-machine"></a>Test sanal makinesi oluştur

Bu bölümde, Özel uç noktayı test etmek için kullanılacak bir sanal makine oluşturacaksınız.

Sanal makineyi şu şekilde oluşturun:

  * [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential)
  * [New-Aznetworkınterface](/powershell/module/az.network/new-aznetworkinterface) 
  * [New-AzVM](/powershell/module/az.compute/new-azvm)
  * [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig)
  * [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem)
  * [Set-Azvmsourceımage](/powershell/module/az.compute/set-azvmsourceimage)
  * [Add-Azvmnetworkınterface](/powershell/module/az.compute/add-azvmnetworkinterface)


```azurepowershell-interactive
## Set credentials for server admin and password. ##
$cred = Get-Credential

## Command to create network interface for VM ##
$nicVM = New-AzNetworkInterface -Name "myNicVM" -ResourceGroupName $rg -Location $loc -Subnet $vnet.Subnets[0] 

## Create a virtual machine configuration.##
$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2" | Set-AzVMOperatingSystem -Windows -ComputerName "myVM" -Credential $cred | Set-AzVMSourceImage -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer" -Skus "2019-Datacenter" -Version "latest" | Add-AzVMNetworkInterface -Id $nicVM.Id 

## Create the virtual machine ##
New-AzVM -ResourceGroupName $rg -Location $loc -VM $vmConfig
```

## <a name="create-an-attestation-provider"></a>Kanıtlama sağlayıcısı oluşturma

```azurepowershell-interactive
## Create an attestation provider ##
$attestationProviderName = "myattestationprovider"
$attestationProvider = New-AzAttestation -Name $attestationProviderName -ResourceGroupName $rg -Location $loc
$attestationProviderId = $attestationProvider.Id

## Access the attestation provider from local machine ##
Enter nslookup <provider-name>.attest.azure.net. Replace <provider-name> with the name of the attestation provider instance you created in the previous steps. 

You'll receive a message similar to what is displayed below:

## PowerShell copy. ##
nslookup myattestationprovider.eus.attest.azure.net

Server:  cdns01.comcast.net
Address:  2001:558:feed::1

Non-authoritative answer:
Name:    eus.service.attest.azure.net
Address:  20.62.219.160
Aliases:  myattestationprovider.eus.attest.azure.net
    attesteusatm.trafficmanager.net
```

## <a name="create-private-endpoint"></a>Özel uç nokta oluştur

Bu bölümde, kullanarak özel uç nokta ve bağlantı oluşturacaksınız:

* [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)
* [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint)

```azurepowershell-interactive
## Create private endpoint connection. ##
$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnection" -PrivateLinkServiceId $attestationProviderId -GroupID "Standard"

## Disable private endpoint network policy ##
 $vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled" 
$vnet | Set-AzVirtualNetwork

## Create private endpoint
New-AzPrivateEndpoint  -ResourceGroupName $rg -Name "myPrivateEndpoint" -Location $loc -Subnet $vnet.Subnets[0] -PrivateLinkServiceConnection $privateEndpointConnection
```
## <a name="configure-the-private-dns-zone"></a>Özel DNS bölgesini yapılandırma

Bu bölümde, kullanarak özel DNS bölgesi oluşturup yapılandıracaksınız:

* [New-AzPrivateDnsZone](/powershell/module/az.privatedns/new-azprivatednszone)
* [New-AzPrivateDnsVirtualNetworkLink](/powershell/module/az.privatedns/new-azprivatednsvirtualnetworklink)
* [New-AzPrivateDnsZoneConfig](/powershell/module/az.network/new-azprivatednszoneconfig)
* [New-AzPrivateDnsZoneGroup](/powershell/module/az.network/new-azprivatednszonegroup)

```azurepowershell-interactive
## Create private dns zone. ##
$zone = New-AzPrivateDnsZone -ResourceGroupName $rg -Name "privatelink.attest.azure.net"

## Create dns network link. ##
$link = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName $rg -ZoneName "privatelink.attest.azure.net" -Name "myLink" -VirtualNetworkId $vnet.Id

## Create DNS configuration ##
$config = New-AzPrivateDnsZoneConfig -Name "privatelink.attest.azure.net" -PrivateDnsZoneId $zone.ResourceId

## Create DNS zone group. ##
New-AzPrivateDnsZoneGroup -ResourceGroupName $rg -PrivateEndpointName "myPrivateEndpoint" -Name "myZoneGroup" -PrivateDnsZoneConfig $config
```

## <a name="test-connectivity-to-private-endpoint"></a>Özel uç nokta ile bağlantıyı sına

Bu bölümde, önceki adımda oluşturduğunuz sanal makineyi kullanarak özel uç nokta genelinde SQL Server 'a bağlanırsınız.

1. [Azure portalda](https://portal.azure.com) oturum açma 
 
2. Sol taraftaki Gezinti bölmesinde **kaynak grupları** ' nı seçin.

3. **CreateAttestationPrivateLinkTutorial-RG** seçin.

4. **Myvm**' yi seçin.

5. **Myvm** için genel bakış sayfasında **Bağlan** ' ı **seçin.**

6. Mavi **kullanımı** savunma düğmesini seçin.

7. Sanal makine oluşturma sırasında girdiğiniz kullanıcı adını ve parolayı girin.

8. Bağlandıktan sonra sunucuda Windows PowerShell 'i açın.

9. `nslookup <provider-name>.attest.azure.net` yazın. **\<provider-name>** Önceki adımlarda oluşturduğunuz kanıtlama sağlayıcısı örneğinin adıyla değiştirin. Aşağıda görüntülene benzer bir ileti alacaksınız:

    ```powershell

    ## Access the attestation provider from local machine ##
    nslookup myattestationprovider.eus.attest.azure.net

    Server:  cdns01.comcast.net
    Address:  2001:558:feed::1

    cdns01.comcast.net can't find myattestationprovider.eus.attest.azure.net: Non-existent domain

    ## Access the attestation provider from the VM created in the same virtual network as the private endpoint.   ##
    nslookup myattestationprovider.eus.attest.azure.net

    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myattestationprovider.eastus.test.attest.azure.net
    ```
