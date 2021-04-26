---
title: "Öğretici: Azure özel uç noktası kullanarak Azure SQL Server 'a bağlanma-PowerShell"
description: Azure PowerShell kullanarak özel bir uç nokta ile Azure SQL Server oluşturmayı öğrenmek için bu öğreticiyi kullanın
services: private-link
author: asudbring
ms.service: private-link
ms.topic: tutorial
ms.date: 10/31/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 8668bdb9dc391582234bf5741c0dd287d026defd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554947"
---
# <a name="tutorial-connect-to-an-azure-sql-server-using-an-azure-private-endpoint---azure-powershell"></a>Öğretici: Azure özel uç nokta kullanarak Azure SQL Server 'a bağlanma-Azure PowerShell

Azure özel uç noktası, Azure 'da özel bağlantı için temel yapı taşdır. Sanal makineler (VM) gibi Azure kaynaklarının özel olarak özel bağlantı kaynaklarıyla iletişim kurmasını sağlar.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Bir sanal ağ ve savunma ana bilgisayarı oluşturun.
> * Sanal makine oluşturur.
> * Azure SQL Server ve özel uç nokta oluşturun.
> * SQL Server özel uç noktası ile bağlantıyı test edin.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* PowerShell'i yerel olarak yükleyip kullanmayı tercih ederseniz bu makale, Azure PowerShell modülü 5.4.1 veya sonraki bir sürümünü gerektirir. Yüklü sürümü bulmak için `Get-Module -ListAvailable Az` komutunu çalıştırın. Yükseltmeniz gerekirse, bkz. [Azure PowerShell modülünü yükleme](/powershell/azure/install-Az-ps). PowerShell 'i yerel olarak çalıştırıyorsanız `Connect-AzAccount` Azure ile bir bağlantı oluşturmak için öğesini de çalıştırmanız gerekir.

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Azure kaynak grubu, Azure kaynaklarının dağıtıldığı ve yönetildiği bir mantıksal kapsayıcıdır.

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)ile bir kaynak grubu oluşturun:

```azurepowershell-interactive
New-AzResourceGroup -Name 'CreateSQLEndpointTutorial-rg' -Location 'eastus'
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
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Location = 'eastus'
    AddressPrefix = '10.0.0.0/16'
    Subnet = $subnetConfig, $bastsubnetConfig
}
$vnet = New-AzVirtualNetwork @parameters1

## Create public IP address for bastion host. ##
$parameters2 = @{
    Name = 'myBastionIP'
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Location = 'eastus'
    Sku = 'Standard'
    AllocationMethod = 'Static'
}
$publicip = New-AzPublicIpAddress @parameters2

## Create bastion host ##
$parameters3 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
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
$vnet = Get-AzVirtualNetwork -Name myVNet -ResourceGroupName CreateSQLEndpointTutorial-rg

## Command to create network interface for VM ##
$parameters1 = @{
    Name = 'myNicVM'
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
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
New-AzVM -ResourceGroupName 'CreateSQLEndpointTutorial-rg' -Location 'eastus' -VM $vmConfig
```

## <a name="create-an-azure-sql-server"></a>Azure SQL Server oluşturma

Bu bölümde, kullanarak bir SQL Server ve veritabanı oluşturacaksınız:

* [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)
* [New-AzSQlDatabase](/powershell/module/az.sql/new-azsqldatabase)

SQL Server ve veritabanı oluşturma. **\<sql-server-name>** Benzersiz sunucu adınızla değiştirin:

```azurepowershell-interactive
## Set and administrator and password for the SQL server. ##
$cred = Get-Credential

## Create SQL server ##
$parameters1 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    ServerName = '<sql-server-name>'
    SqlAdministratorCredentials = $cred
    Location = 'eastus'
}
New-AzSqlServer @parameters1

## Create database. ##
$parameters2 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    ServerName = '<sql-server-name>'
    DatabaseName = 'mysqldatabase'
    RequestedServiceObjectiveName = 'S0'
    SampleName = 'AdventureWorksLT'
}
New-AzSqlDatabase @parameters2
```

## <a name="create-private-endpoint"></a>Özel uç nokta oluştur

Bu bölümde, kullanarak özel uç nokta ve bağlantı oluşturacaksınız:

* [New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)
* [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint)

```azurepowershell-interactive
## Place SQL server into variable. Replace <sql-server-name> with your server name ##
$server = Get-AzSqlServer -ResourceGroupName CreateSQLEndpointTutorial-rg -ServerName <sql-server-name>

## Create private endpoint connection. ##
$parameters1 = @{
    Name = 'myConnection'
    PrivateLinkServiceId = $server.ResourceID
    GroupID = 'sqlserver'
}
$privateEndpointConnection = New-AzPrivateLinkServiceConnection @parameters1

## Place virtual network into variable. ##
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreateSQLEndpointTutorial-rg' -Name 'myVNet'

## Disable private endpoint network policy ##
$vnet.Subnets[0].PrivateEndpointNetworkPolicies = "Disabled"
$vnet | Set-AzVirtualNetwork

## Create private endpoint
$parameters2 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
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
$vnet = Get-AzVirtualNetwork -ResourceGroupName 'CreateSQLEndpointTutorial-rg' -Name 'myVNet'

## Create private dns zone. ##
$parameters1 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    Name = 'privatelink.database.windows.net'
}
$zone = New-AzPrivateDnsZone @parameters1

## Create dns network link. ##
$parameters2 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
    ZoneName = 'privatelink.database.windows.net'
    Name = 'myLink'
    VirtualNetworkId = $vnet.Id
}
$link = New-AzPrivateDnsVirtualNetworkLink @parameters2

## Create DNS configuration ##
$parameters3 = @{
    Name = 'privatelink.database.windows.net'
    PrivateDnsZoneId = $zone.ResourceId
}
$config = New-AzPrivateDnsZoneConfig @parameters3

## Create DNS zone group. ##
$parameters4 = @{
    ResourceGroupName = 'CreateSQLEndpointTutorial-rg'
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

3. **CreateSQLEndpointTutorial-RG** seçin.

4. **Myvm**' yi seçin.

5. **Myvm** için genel bakış sayfasında **Bağlan** ' ı **seçin.**

6. Mavi **kullanımı** savunma düğmesini seçin.

7. Sanal makine oluşturma sırasında girdiğiniz kullanıcı adını ve parolayı girin.

8. Bağlandıktan sonra sunucuda Windows PowerShell 'i açın.

9. `nslookup <sqlserver-name>.database.windows.net` yazın. **\<sqlserver-name>** Önceki adımlarda oluşturduğunuz SQL Server 'ın adıyla değiştirin.  Aşağıda görüntülene benzer bir ileti alacaksınız:

    ```powershell
    Server:  UnKnown
    Address:  168.63.129.16

    Non-authoritative answer:
    Name:    mysqlserver8675.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:  mysqlserver8675.database.windows.net
    ```

    SQL Server adı için **10.0.0.5** özel IP adresi döndürülür.  Bu adres, daha önce oluşturduğunuz sanal ağın alt ağıdır.


10. **Myvm**'de [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?preserve-view=true&view=sql-server-2017) 'yi yükler.

11. **SQL Server Management Studio** açın.

12. **Sunucuya Bağlan**' da bu bilgileri girin veya seçin:

    | Ayar | Değer |
    | ------- | ----- |
    | Sunucu türü | **Veritabanı Altyapısı**’nı seçin.|
    | Sunucu adı | **\<sql-server-name> . Database.Windows.net** girin |
    | Kimlik doğrulaması | **SQL Server Kimlik Doğrulaması**'nı seçin. |
    | Kullanıcı adı | Sunucu oluşturma sırasında girdiğiniz kullanıcı adını girin |
    | Parola | Sunucu oluşturma sırasında girdiğiniz parolayı girin |
    | Parolayı unutmayın | **Evet**’i seçin. |

13. **Bağlan**’ı seçin.

14. Sol menüden veritabanlarına gözatamazsınız.

15. I **Mysqldatabase**'dan bilgi oluşturun veya sorgulayın.

16. **Myvm**'e yönelik savunma bağlantısını kapatın. 

## <a name="clean-up-resources"></a>Kaynakları temizleme 
Özel uç nokta, SQL Server ve VM 'yi kullanarak işiniz bittiğinde, kaynak grubunu ve içerdiği tüm kaynakları silin: 

1. Portalın üst kısmındaki **arama** kutusuna **CreateSQLEndpointTutorial-RG** girin ve arama sonuçlarından **CreateSQLEndpointTutorial-RG** seçeneğini belirleyin. 

2. **Kaynak grubunu sil**'i seçin. 

3. **Kaynak grubu adını yazmak** için **CreateSQLEndpointTutorial-RG** girin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunu oluşturdunuz:

* Sanal ağ ve savunma Konağı.
* Sanal makine.
* Özel uç nokta ile Azure SQL Server.

Sanal makineyi, Özel uç nokta genelinde SQL Server ile güvenli bir şekilde test etmek için kullandınız.

Bir sonraki adım olarak, Web uygulamasını, sanal ağın dışında bir Web uygulamasını bir veritabanının özel uç noktasına bağlayan **Azure SQL veritabanı mimarisi senaryosuna özel bağlantı ile** de ilgileniyor olabilirsiniz.
> [!div class="nextstepaction"]
> [Azure SQL veritabanı 'na özel bağlantı içeren Web uygulaması](/azure/architecture/example-scenario/private-web-app/private-web-app)