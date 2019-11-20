---
title: Azure PowerShell kullanarak bir Azure özel uç noktası oluşturma | Microsoft Docs
description: Azure özel bağlantısı hakkında bilgi edinin
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 91670d51328b3adb67ba8b2ed05d3b86f9bc0010
ms.sourcegitcommit: 87efc325493b1cae546e4cc4b89d9a5e3df94d31
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73053920"
---
# <a name="create-a-private-endpoint-using-azure-powershell"></a>Azure PowerShell kullanarak özel uç nokta oluşturma
Özel uç nokta, Azure 'da özel bağlantı için temel yapı taşdır. Sanal makineler (VM) gibi Azure kaynaklarının özel bağlantı kaynaklarıyla özel olarak iletişim kurmasına olanak sağlar. 

Bu hızlı başlangıçta, Azure PowerShell kullanarak Azure özel uç noktası olan bir SQL veritabanı sunucusu olan bir Azure sanal ağında bir VM oluşturmayı öğreneceksiniz. Ardından, VM 'den SQL veritabanı sunucusuna güvenle erişebilirsiniz.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Kaynak grubu oluşturma

Kaynaklarınızı oluşturabilmeniz için önce, [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)Ile sanal ağı ve özel uç noktayı barındıran bir kaynak grubu oluşturmanız gerekir. Aşağıdaki örnek *WestUS* konumunda *myresourcegroup* adlı bir kaynak grubu oluşturur:

```azurepowershell

New-AzResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus
```

## <a name="create-a-virtual-network"></a>Sanal ağ oluşturma
Bu bölümde, bir sanal ağ ve bir alt ağ oluşturacaksınız. Sonra, alt ağı sanal ağınızla ilişkilendirirsiniz.

### <a name="create-a-virtual-network"></a>Sanal ağ oluşturma

[New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)ile özel uç noktanız Için bir sanal ağ oluşturun. Aşağıdaki örnek, *MyVirtualNetwork*adlı bir sanal ağ oluşturur:
 
```azurepowershell

$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westcentralus `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

### <a name="add-a-subnet"></a>Alt ağ ekleme

Azure, kaynakları bir sanal ağ içindeki bir alt ağa dağıtır, bu nedenle bir alt ağ oluşturmanız gerekir. [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)Ile *mysubnet* adlı bir alt ağ yapılandırması oluşturun. Aşağıdaki örnek, Özel uç nokta ağ ilkesi bayrağı **devre dışı**olarak ayarlanan *mysubnet* adlı bir alt ağ oluşturur.

```azurepowershell
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 10.0.0.0/24 `
  -PrivateEndpointNetworkPoliciesFlag "Disabled" `
  -VirtualNetwork $virtualNetwork
```

### <a name="associate-the-subnet-to-the-virtual-network"></a>Alt ağı sanal ağla ilişkilendir

Alt ağ yapılandırmasını [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork)Ile sanal ağa yazabilirsiniz. Bu komut alt ağ oluşturur:

```azurepowershell
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="create-a-virtual-machine"></a>Bir Sanal Makine Oluşturun

[New-azvm](/powershell/module/az.compute/new-azvm)Ile sanal ağda bir VM oluşturun. Sonraki komutu çalıştırdığınızda kimlik bilgileri istenir. VM için bir Kullanıcı adı ve parola girin:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "westcentralus" `
    -VirtualNetworkName "MyVirtualNetwork" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80,3389 `
    -AsJob  
```

`-AsJob` seçeneği, sanal makineyi arka planda oluşturur. Sonraki adıma devam edebilirsiniz.

Azure, arka planda VM oluşturmaya başladığında, bu geri doğru bir şey alacaksınız:

```powershell
Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
1      Long Running... AzureLongRun... Running       True            localhost            New-AzVM
```

## <a name="create-a-sql-database-server"></a>SQL veritabanı sunucusu oluşturma 

New-AzSqlServer komutunu kullanarak bir SQL veritabanı sunucusu oluşturun. SQL veritabanı sunucunuzun adının Azure genelinde benzersiz olması gerektiğini unutmayın. bu nedenle, yer tutucu değerini köşeli ayraç içinde kendi benzersiz bir değer ile değiştirin:

```azurepowershell-interactive
$adminSqlLogin = "SqlAdmin"
$password = "ChangeYourAdminPassword1"

$server = New-AzSqlServer -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver" `
    -Location "WestCentralUS" `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminSqlLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

New-AzSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver"`
    -DatabaseName "myda"`
    -RequestedServiceObjectiveName "S0" `
    -SampleName "AdventureWorksLT"
```

## <a name="create-a-private-endpoint"></a>Özel Uç Nokta oluşturma

[New-AzPrivateLinkServiceConnection](/powershell/module/az.network/New-AzPrivateLinkServiceConnection)Ile sanal ağınızdaki SQL veritabanı sunucusu Için özel uç nokta: 

```azurepowershell

$privateEndpointConnection = New-AzPrivateLinkServiceConnection -Name "myConnection" `
  -PrivateLinkServiceId $server.ResourceId `
  -GroupId "sqlServer" 
 
$virtualNetwork = Get-AzVirtualNetwork -ResourceGroupName  "myResourceGroup" -Name "MyVirtualNetwork"  
 
$subnet = $virtualNetwork `
  | Select -ExpandProperty subnets `
  | Where-Object  {$_.Name -eq 'mysubnet'}  
 
$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName "myResourceGroup" `
  -Name "myPrivateEndpoint" `
  -Location "westcentralus" `
  -Subnet  $subnet`
  -PrivateLinkServiceConnection $privateEndpointConnection
``` 

## <a name="configure-the-private-dns-zone"></a>Özel DNS bölgesini yapılandırma 
SQL veritabanı sunucusu etki alanı için özel bir DNS bölgesi oluşturun ve sanal ağla bir ilişki bağlantısı oluşturun: 

```azurepowershell

$zone = New-AzPrivateDnsZone -ResourceGroupName "myResourceGroup" `
  -Name "privatelink.database.windows.net" 
 
$link  = New-AzPrivateDnsVirtualNetworkLink -ResourceGroupName "myResourceGroup" `
  -ZoneName "privatelink.database.windows.net"`
  -Name "mylink" `
  -VirtualNetworkId $virtualNetwork.Id  
 
$networkInterface = Get-AzResource -ResourceId $privateEndpoint.NetworkInterfaces[0].Id -ApiVersion "2019-04-01" 
 
foreach ($ipconfig in $networkInterface.properties.ipConfigurations) { 
foreach ($fqdn in $ipconfig.properties.privateLinkConnectionProperties.fqdns) { 
Write-Host "$($ipconfig.properties.privateIPAddress) $($fqdn)"  
$recordName = $fqdn.split('.',2)[0] 
$dnsZone = $fqdn.split('.',2)[1] 
New-AzPrivateDnsRecordSet -Name $recordName -RecordType A -ZoneName "privatelink.database.windows.net"  `
-ResourceGroupName "myResourceGroup" -Ttl 600 `
-PrivateDnsRecords (New-AzPrivateDnsRecordConfig -IPv4Address $ipconfig.properties.privateIPAddress)  
} 
} 
``` 
  
## <a name="connect-to-a-vm-from-the-internet"></a>İnternet'ten bir sanal makineye bağlanma

Bir sanal makinenin genel IP adresini döndürmek için [Get-Azpublicıpaddress](/powershell/module/az.network/Get-AzPublicIpAddress) komutunu kullanın. Bu örnek, *Myvm* VM 'nın genel IP adresini döndürür:

```azurepowershell
Get-AzPublicIpAddress `
  -Name myPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress 
```  
Yerel bilgisayarınızda bir komut istemi açın. Mstsc komutunu çalıştırın. <publicIpAddress>, son adımdan döndürülen genel IP adresiyle değiştirin: 


> [!NOTE]
> Bu komutları yerel bilgisayarınızdaki bir PowerShell isteminden çalıştırıyorsanız ve az PowerShell modülü 1,0 veya sonraki bir sürümü kullanıyorsanız, bu arabirime devam edebilirsiniz.
```
mstsc /v:<publicIpAddress>
```

1. İstendiğinde **Bağlan**’ı seçin. 
2. Sanal makine oluştururken belirttiğiniz kullanıcı adını ve parolayı girin.
  > [!NOTE]
  > VM oluştururken girdiğiniz kimlik bilgilerini belirtmek için farklı bir hesap kullanmak > daha fazla seçenek belirlemeniz gerekebilir. 
  
3. **Tamam**’ı seçin. 
4. Bir sertifika uyarısı alabilirsiniz. Bunu yaparsanız **Evet** ' i veya **devam et**' i seçin. 

## <a name="access-sql-database-server-privately-from-the-vm"></a>SQL veritabanı sunucusuna VM 'den özel olarak erişme

1. MyVM uzak masaüstünde PowerShell ' i açın.
2. `nslookup myserver.database.windows.net` yazın. 

    Şuna benzer bir ileti alacaksınız:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    myserver.privatelink.database.windows.net
    Address:  10.0.0.5
    Aliases:   myserver.database.windows.net
    ```
3. SQL Server Management Studio yüklensin
4. Sunucuya Bağlan ' da bu bilgileri girin veya seçin: ayar değeri sunucu türü veritabanı altyapısı seçin.
      Sunucu adı myserver.database.windows.net Kullanıcı adı Seç oluşturma sırasında sağlanmış bir Kullanıcı adı girin.
      Parola oluşturma sırasında sağlanmış bir parola girin.
      Parolayı anımsa Evet ' i seçin.
5. Bağlan ' ı seçin.
6. Sol menüden veritabanlarına gözatamazsınız. 
7. I MyDatabase 'teki bilgileri oluşturma veya sorgulama
8. *Myvm*ile uzak masaüstü bağlantısını kapatın. 

## <a name="clean-up-resources"></a>Kaynakları temizleme 
Özel uç nokta, SQL veritabanı sunucusu ve VM 'yi kullanarak işiniz bittiğinde, kaynak grubunu ve içerdiği tüm kaynakları kaldırmak için [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) komutunu kullanın:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Sonraki adımlar
- [Azure özel bağlantısı](private-link-overview.md) hakkında daha fazla bilgi edinin
