---
title: Hızlı başlangıç-Azure PowerShell kullanarak bir Azure özel uç noktası oluşturma
description: Azure PowerShell kullanarak özel uç nokta oluşturmayı öğrenmek için bu hızlı başlangıcı kullanın.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 11/02/2020
ms.author: allensu
ms.openlocfilehash: afd1f8b6c80cfd7aa8d5142841458d76511a9e96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562920"
---
# <a name="quickstart-create-an-azure-private-endpoint-using-azure-powershell"></a>Hızlı başlangıç: Azure PowerShell kullanarak bir Azure özel uç noktası oluşturma

Azure Web uygulamasına güvenli bir şekilde bağlanmak için özel bir uç nokta kullanarak Azure özel bağlantısı ile çalışmaya başlayın.

Bu hızlı başlangıçta, bir Azure Web uygulaması için özel bir uç nokta oluşturacak ve özel bağlantıyı test etmek için bir sanal makine dağıtacaksınız.  

Özel uç noktalar Azure SQL ve Azure depolama gibi farklı türlerde Azure hizmetleri için oluşturulabilir.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure aboneliğinizde dağıtılan **PremiumV2 katmanı** veya daha yüksek bir App Service planına sahip bir Azure Web uygulaması.  
    * Daha fazla bilgi ve bir örnek için bkz. [hızlı başlangıç: Azure 'da ASP.NET Core Web uygulaması oluşturma](../app-service/quickstart-dotnetcore.md). 
    * Web uygulaması ve uç nokta oluşturma hakkında ayrıntılı bir öğretici için bkz. [öğretici: Azure özel uç nokta kullanarak bir Web uygulamasına bağlanma](tutorial-private-endpoint-webapp-portal.md).

PowerShell'i yerel olarak yükleyip kullanmayı tercih ederseniz bu makale, Azure PowerShell modülü 5.4.1 veya sonraki bir sürümünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-Az-ps). PowerShell 'i yerel olarak çalıştırıyorsanız `Connect-AzAccount` Azure ile bir bağlantı oluşturmak için öğesini de çalıştırmanız gerekir.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)ile bir kaynak grubu oluşturun:

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreatePrivateEndpointQS-rg' -Location 'eastus'
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
$parameters1 = @{
    Name = 'MyVNet'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Location = 'eastus'
    AddressPrefix = '10.0.0.0/16'
    Subnet = $subnetConfig, $bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @parameters1

## Create public IP address for bastion host. ##
$parameters2 = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @parameters2

## Create bastion host ##
$parameters3 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Name = 'myBastion'
    PublicIpAddress = $publicip
    VirtualNetwork = $vnet
}
New-AzBastion @parameters3
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

## Command to get virtual network configuration. ##
$vnet = Get-AzVirtualNetwork -Name myVNet -ResourceGroupName CreatePrivateEndpointQS-rg

## Command to create network interface for VM ##
$parameters1 = @{
    Name = 'myNicVM'
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
}
$nicVM = New-AzNetworkInterface @parameters1

## Create a virtual machine configuration.##
$parameters2 = @{
    VMName = 'myVM'
    VMSize = 'Standard_DS1_v2'
}
$parameters3 = @{
    ComputerName = 'myVM'
    Credential = $cred
}
$parameters4 = @{
    PublisherName = 'MicrosoftWindowsServer'
    Offer = 'WindowsServer'
    Skus = '2019-Datacenter'
    Version = 'latest'
}
$vmConfig = 
New-AzVMConfig @parameters2 | Set-AzVMOperatingSystem -Windows @parameters3 | Set-AzVMSourceImage @parameters4 | Add-AzVMNetworkInterface -Id $nicVM.Id

## Create the virtual machine ##
New-AzVM -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Location 'eastus' -VM $vmConfig
```

## <a name="create-private-endpoint"></a>Özel uç nokta oluştur

Bu bölümde, kullanarak özel uç nokta ve bağlantı oluşturacaksınız:

* [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)
* [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint)

```azurepowershell-interactive
## Place web app into variable. Replace <webapp-resource-group-name> with the resource group of your webapp. ##
## Replace <your-webapp-name> with your webapp name ##
$webapp = Get-AzWebApp -ResourceGroupName <webapp-resource-group-name> -Name <your-webapp-name>

## Create private endpoint connection. ##
$parameters1 = @{
    Name = 'myConnection'
    PrivateLinkServiceId = $webapp.ID
    GroupID = 'sites'
}
$privateEndpointConnection = New-AzPrivateLinkServiceConnection @parameters1

## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Name 'myVNet'

## Disable private endpoint network policy ##
$vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled"
$vnet | Set-AzVirtualNetwork

## Create private endpoint
$parameters2 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Name = 'myPrivateEndpoint'
    Location = 'eastus'
    Subnet = $vnet.Subnets[0]
    PrivateLinkServiceConnection = $privateEndpointConnection
}
New-AzPrivateEndpoint @parameters2
```
## <a name="configure-the-private-dns-zone"></a>Özel DNS bölgesini yapılandırma

Bu bölümde, kullanarak özel DNS bölgesi oluşturup yapılandıracaksınız:

* [New-AzPrivateDnsZone](/powershell/module/az.privatedns/new-azprivatednszone)
* [New-AzPrivateDnsVirtualNetworkLink](/powershell/module/az.privatedns/new-azprivatednsvirtualnetworklink)
* [New-AzPrivateDnsZoneConfig](/powershell/module/az.network/new-azprivatednszoneconfig)
* [New-AzPrivateDnsZoneGroup](/powershell/module/az.network/new-azprivatednszonegroup)

```azurepowershell-interactive
## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreatePrivateEndpointQS-rg' -Name 'myVNet'

## Create private dns zone. ##
$parameters1 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    Name = 'privatelink.azurewebsites.net'
}
$zone = New-AzPrivateDnsZone @parameters1

## Create dns network link. ##
$parameters2 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    ZoneName = 'privatelink.azurewebsites.net'
    Name = 'myLink'
    VirtualNetworkId = $vnet.Id
}
$link = New-AzPrivateDnsVirtualNetworkLink @parameters2

## Create DNS configuration ##
$parameters3 = @{
    Name = 'privatelink.azurewebsites.net'
    PrivateDnsZoneId = $zone.ResourceId
}
$config = New-AzPrivateDnsZoneConfig @parameters3

## Create DNS zone group. ##
$parameters4 = @{
    ResourceGroupName = 'CreatePrivateEndpointQS-rg'
    PrivateEndpointName = 'myPrivateEndpoint'
    Name = 'myZoneGroup'
    PrivateDnsZoneConfig = $config
}
New-AzPrivateDnsZoneGroup @parameters4
```

## <a name="test-connectivity-to-private-endpoint"></a>Özel uç nokta ile bağlantıyı sına

Bu bölümde, önceki adımda oluşturduğunuz sanal makineyi kullanarak özel uç nokta genelinde SQL Server 'a bağlanırsınız.

1. [Azure portalda](https://portal.azure.com) oturum açma 
 
2. Sol taraftaki Gezinti bölmesinde **kaynak grupları** ' nı seçin.

3. **Createprivateendpointqs-RG** öğesini seçin.

4. **Myvm**' yi seçin.

5. **Myvm** için genel bakış sayfasında **Bağlan** ' ı **seçin.**

6. Mavi **kullanımı** savunma düğmesini seçin.

7. Sanal makine oluşturma sırasında girdiğiniz kullanıcı adını ve parolayı girin.

8. Bağlandıktan sonra sunucuda Windows PowerShell 'i açın.

9. `nslookup <your-webapp-name>.azurewebsites.net` yazın. **\<your-webapp-name>** Önceki adımlarda oluşturduğunuz Web uygulamasının adıyla değiştirin.  Aşağıda görüntülene benzer bir ileti alacaksınız:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mywebapp8675.privatelink.azurewebsites.net
    Address:  10.0.0.5
    Aliases:  mywebapp8675.azurewebsites.net
    ```

    Web uygulaması adı için **10.0.0.5** özel IP adresi döndürülür.  Bu adres, daha önce oluşturduğunuz sanal ağın alt ağıdır.

10. **Myvm**'e yönelik savunma bağlantısı ' nda Internet Explorer 'ı açın.

11. Web uygulamanızın URL 'sini girin, **https:// \<your-webapp-name> . azurewebsites.net**.

12. Uygulamanız dağıtılmamışsa varsayılan Web uygulaması sayfasını alacaksınız:

    :::image type="content" source="./media/create-private-endpoint-portal/web-app-default-page.png" alt-text="Varsayılan Web uygulaması sayfası." border="true":::

13. **Myvm** bağlantısını kapatın.

## <a name="clean-up-resources"></a>Kaynakları temizleme 
Özel uç nokta ve VM 'yi kullanarak işiniz bittiğinde, kaynak grubunu ve içerdiği tüm kaynakları kaldırmak için [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanın:

```azurepowershell-interactive
Remove-AzResourceGroup -Name CreatePrivateEndpointQS-rg -Force
```

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta şunu oluşturdunuz:

* Sanal ağ ve savunma Konağı.
* Sanal makine.
* Bir Azure Web uygulaması için özel uç nokta.

Özel uç nokta genelinde Web uygulamasıyla güvenli bir şekilde bağlantıyı test etmek için sanal makineyi kullandınız.

Özel bir uç noktayı destekleyen hizmetler hakkında daha fazla bilgi için bkz.:
> [!div class="nextstepaction"]
> [Özel bağlantı kullanılabilirliği](private-link-overview.md#availability)
