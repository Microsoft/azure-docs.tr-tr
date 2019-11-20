---
title: 'Öğretici: bir yük devretme grubuna yönetilen bir örnek ekleme'
description: Azure SQL veritabanı yönetilen örneğiniz için bir yük devretme grubu yapılandırmayı öğrenin.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 08/27/2019
ms.openlocfilehash: 85d2e58d35ef233fda7c724f85152fc74bd11189
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826833"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>Öğretici: bir yük devretme grubuna SQL veritabanı yönetilen örneği ekleme

Bir yük devretme grubuna SQL veritabanı yönetilen örneği ekleyin. Bu makalede şunları yapmayı öğreneceksiniz:

> [!div class="checklist"]
> - Birincil yönetilen örnek oluşturma
> - Bir [Yük devretme grubunun](sql-database-auto-failover-group.md)parçası olarak ikincil bir yönetilen örnek oluşturun. 
> - Yük devretme testi

  > [!NOTE]
  > - Bu öğreticide, kaynakları [yönetilen örnek için yük devretme grupları ayarlamaya yönelik önkoşullara](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets)sahip olduğunuzdan emin olun. 
  > - Yönetilen bir örnek oluşturmak, önemli miktarda zaman alabilir. Sonuç olarak, Bu öğreticinin tamamlanması birkaç saat sürebilir. Sağlama süreleri hakkında daha fazla bilgi için bkz. [yönetilen örnek yönetimi işlemleri](sql-database-managed-instance.md#managed-instance-management-operations). 


## <a name="prerequisites"></a>Ön koşullar

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Bu öğreticiyi tamamlamak için şunlar sahip olduğunuzdan emin olun: 

- Azure aboneliği. Henüz bir [hesabınız yoksa ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/) .


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Öğreticiyi tamamlayabilmeniz için aşağıdaki öğelerin bulunduğundan emin olun:

- Azure aboneliği. Henüz bir [hesabınız yoksa ücretsiz bir hesap oluşturun](https://azure.microsoft.com/free/) .
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)

---


## <a name="1---create-resource-group-and-primary-managed-instance"></a>1-kaynak grubu ve birincil yönetilen örnek oluşturma
Bu adımda, Azure portal veya PowerShell 'i kullanarak yük devretme grubunuz için kaynak grubunu ve birincil yönetilen örneği oluşturacaksınız. 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal) 

Azure portal kullanarak kaynak grubunu ve birincil yönetilen örneğinizi oluşturun. 

1. Azure portal sol taraftaki menüden **Azure SQL** ' i seçin. **Azure SQL** listede yoksa, **tüm hizmetler**' i seçin ve arama kutusuna Azure SQL yazın. Seçim **Azure SQL** ' in yanındaki yıldızı seçerek bunu sık kullanılanlara ekleyin ve sol gezinti bölmesinde bir öğe olarak ekleyin. 
1. **+ Ekle** ' yı seçerek **SQL dağıtım seçeneğini seçin** sayfasını açın. Veritabanları kutucuğunda ayrıntıları göster ' i seçerek farklı veritabanları hakkındaki ek bilgileri görüntüleyebilirsiniz.
1. **SQL yönetilen örnekler** kutucuğunda **Oluştur** ' u seçin. 

    ![Yönetilen örnek seçin](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. **Azure SQL veritabanı yönetilen örneği oluştur** sayfasında, **temel bilgiler** sekmesinde
    1. **Proje ayrıntıları**' nın altında, açılır listeden **aboneliğinizi** seçin ve ardından yeni kaynak grubu **oluşturmayı** seçin. Kaynak grubunuz için `myResourceGroup`gibi bir ad yazın. 
    1. **Yönetilen örnek ayrıntıları**' nın altında, yönetilen örneğinizin adını ve yönetilen örneğinizi dağıtmak istediğiniz bölgeyi girin. **İşlem + depolama alanını** varsayılan değerlerle bırakın. 
    1. **Yönetici hesabı**altında, `azureuser`gibi bir yönetici oturumu ve karmaşık yönetici parolası sağlayın. 

    ![Birincil mı oluştur](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. Ayarları varsayılan değerlerinde bırakın ve yönetilen örnek ayarlarınızı gözden geçirmek için **gözden geçir + oluştur** ' u seçin. 
1. Birincil yönetilen örneğinizi oluşturmak için **Oluştur** ' u seçin. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell kullanarak kaynak grubunuzu ve birincil yönetilen örneği oluşturun. 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for your managed instance
   $resourceGroupName = "myResourceGroup-$randomIdentifier"
   $location = "eastus"
   $drLocation = "eastus2"
   
   # Set the networking values for your primary managed instance
   $primaryVNet = "primaryVNet-$randomIdentifier"
   $primaryAddressPrefix = "10.0.0.0/16"
   $primaryDefaultSubnet = "primaryDefaultSubnet-$randomIdentifier"
   $primaryDefaultSubnetAddress = "10.0.0.0/24"
   $primaryMiSubnetName = "primaryMISubnet-$randomIdentifier"
   $primaryMiSubnetAddress = "10.0.0.0/24"
   $primaryMiGwSubnetAddress = "10.0.255.0/27"
   $primaryGWName = "primaryGateway-$randomIdentifier"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   $primaryGWConnection = $primaryGWName + "-connection"
   
   
   # Set the networking values for your secondary managed instance
   $secondaryVNet = "secondaryVNet-$randomIdentifier"
   $secondaryAddressPrefix = "10.128.0.0/16"
   $secondaryDefaultSubnet = "secondaryDefaultSubnet-$randomIdentifier"
   $secondaryDefaultSubnetAddress = "10.128.0.0/24"
   $secondaryMiSubnetName = "secondaryMISubnet-$randomIdentifier"
   $secondaryMiSubnetAddress = "10.128.0.0/24"
   $secondaryMiGwSubnetAddress = "10.128.255.0/27"
   $secondaryGWName = "secondaryGateway-$randomIdentifier"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   $secondaryGWConnection = $secondaryGWName + "-connection"
   
   
   
   # Set the managed instance name for the new managed instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for your managed instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the managed instance service tier, compute level, and license mode
   $edition = "General Purpose"
   $vCores = 8
   $maxStorage = 256
   $computeGeneration = "Gen5"
   $license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server licence that can be used for AHB discount
   
   # Set failover group details
   $vpnSharedKey = "mi1mi2psk"
   $failoverGroupName = "failovergroup-$randomIdentifier"
   
   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Password is" $secpasswd
   Write-host "Primary Virtual Network name is" $primaryVNet
   Write-host "Primary default subnet name is" $primaryDefaultSubnet
   Write-host "Primary managed instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary managed instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary managed instance name is" $primaryInstance
   Write-host "Secondary managed instance name is" $secondaryInstance
   Write-host "Failover group name is" $failoverGroupName
   
   # Suppress networking breaking changes warning (https://aka.ms/azps-changewarnings
   Set-Item Env:\SuppressAzurePowerShellBreakingChangeWarnings "true"
   
   # Set subscription context
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
   
   # Configure primary virtual network
   Write-host "Creating primary virtual network..."
   $primaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -Name $primaryVNet `
                         -AddressPrefix $primaryAddressPrefix
   
                     Add-AzVirtualNetworkSubnetConfig `
                         -Name $primaryMiSubnetName `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                     | Set-AzVirtualNetwork
   $primaryVirtualNetwork
   
   
   # Configure primary MI subnet
   Write-host "Configuring primary MI subnet..."
   $primaryVirtualNetwork = Get-AzVirtualNetwork -Name $primaryVNet -ResourceGroupName $resourceGroupName
   
   
   $primaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $primaryMiSubnetName `
                           -VirtualNetwork $primaryVirtualNetwork
   $primaryMiSubnetConfig
   
   # Configure network security group management service
   Write-host "Configuring primary MI subnet..."
   
   $primaryMiSubnetConfigId = $primaryMiSubnetConfig.Id
   
   $primaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'primaryNSGMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryNSGMiManagementService
   
   # Configure route table management service
   Write-host "Configuring primary MI route table management service..."
   
   $primaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'primaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryRouteTableMiManagementService
   
   # Configure the primary network security group
   Write-host "Configuring primary network security group..."
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -Name $primaryMiSubnetName `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NetworkSecurityGroup $primaryNSGMiManagementService `
                         -RouteTable $primaryRouteTableMiManagementService | `
                       Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryNSGMiManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $PrimaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $PrimaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   Write-host "Primary network security group configured successfully."
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "primaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Primary network route table configured successfully."
   
   
   # Create primary managed instance
   
   Write-host "Creating primary managed instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $primaryInstance `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -SubnetId $primaryMiSubnetConfigId `
                         -AdministratorCredential $mycreds `
                         -StorageSizeInGB $maxStorage `
                         -VCore $vCores `
                         -Edition $edition `
                         -ComputeGeneration $computeGeneration `
                         -LicenseType $license
   Write-host "Primary managed instance created successfully."
   ```

Öğreticinin bu bölümü aşağıdaki PowerShell cmdlet 'lerini kullanır:

| Komut | Notlar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Bir Azure Kaynak grubu oluşturur.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Sanal ağ oluşturur.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Bir sanal ağa alt ağ yapılandırması ekler. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Bir kaynak grubundaki sanal ağı alır. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Bir sanal ağ içindeki bir alt ağı alır. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Ağ güvenlik grubu oluşturur. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Bir yol tablosu oluşturur. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Bir sanal ağ için alt ağ yapılandırmasını güncelleştirir.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Bir sanal ağı güncelleştirir.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Bir ağ güvenlik grubu alır. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Ağ güvenlik grubuna ağ güvenlik kuralı yapılandırması ekler. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Bir ağ güvenlik grubunu güncelleştirir.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Rota tablosuna bir yol ekler. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Bir yol tablosunu güncelleştirir.  |
| [New-Azsqlınstance](/powershell/module/az.sql/new-azsqlinstance) | Azure SQL veritabanı yönetilen örneği oluşturur.  |

---

## <a name="2---create-secondary-virtual-network"></a>2-ikincil sanal ağ oluşturma
Yönetilen örneğinizi oluşturmak için Azure portal kullanıyorsanız, birincil ve ikincil yönetilen örnek alt ağının çakışan aralıklar olmadığından, sanal ağı ayrı olarak oluşturmanız gerekecektir. Yönetilen örneğinizi yapılandırmak için PowerShell kullanıyorsanız, adım 3 ' e atlayın. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal) 
Birincil sanal ağınızın alt ağ aralığını doğrulamak için şu adımları izleyin:
1. [Azure Portal](https://portal.azure.com), kaynak grubunuza gidin ve birincil örneğiniz için sanal ağı seçin. 
1. **Ayarlar** altında **alt ağlar** ' ı seçin ve **adres aralığını**aklınızda edin. İkincil yönetilen örnek için sanal ağın alt ağ adres aralığı bu ile çakışamaz. 


   ![Birincil alt ağ](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

Bir sanal ağ oluşturmak için aşağıdaki adımları izleyin:

1. [Azure Portal](https://portal.azure.com), **kaynak oluştur** ve *sanal ağ*ara ' yı seçin. 
1. Microsoft tarafından yayımlanan **sanal ağ** seçeneğini seçin ve ardından sonraki sayfada **Oluştur** ' u seçin. 
1. İkincil yönetilen örneğinizin sanal ağını yapılandırmak için gerekli alanları doldurun ve **Oluştur**' u seçin. 

   Aşağıdaki tabloda, ikincil sanal ağ için gereken değerler gösterilmektedir:

    | **Alan** | Değer |
    | --- | --- |
    | **Ad** |  `vnet-sql-mi-secondary`gibi, ikincil yönetilen örnek tarafından kullanılacak sanal ağın adı. |
    | **Adres alanı** | Sanal ağınızın `10.128.0.0/16`gibi adres alanı. | 
    | **Abonelik** | Birincil yönetilen örneğinizin ve kaynak grubunuzun bulunduğu abonelik. |
    | **Bölge** | İkincil yönetilen örneğinizi dağıtacağınız konum. |
    | **Alt ağ** | Alt ağınızın adı. `default` varsayılan olarak sizin için verilmiştir. |
    | **Adres aralığı**| Alt ağınızın adres aralığı. Bu, `10.128.0.0/24`gibi, birincil yönetilen örneğinizin sanal ağı tarafından kullanılan alt ağ adres aralığından farklı olmalıdır.  |
    | &nbsp; | &nbsp; |

    ![İkincil sanal ağ değerleri](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Bu adım yalnızca, yönetilen örneğinizi dağıtmak için Azure portal kullanıyorsanız gereklidir. PowerShell kullanıyorsanız, adım 3 ' e atlayın. 

---

## <a name="3---create-a-secondary-managed-instance"></a>3-ikincil bir yönetilen örnek oluşturma
Bu adımda, Azure portal bir ikincil yönetilen örnek oluşturacaksınız ve bu, iki yönetilen örnek arasında ağ iletişimini de yapılandıracaksınız. 

İkinci yönetilen örneğiniz şunları sağlamalıdır:
- Boş olmalıdır. 
- Birincil yönetilen örnekten farklı bir alt ağa ve IP aralığına sahip olmak. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal) 

Azure portal kullanarak ikincil yönetilen örnek oluşturun. 

1. Azure portal sol taraftaki menüden **Azure SQL** ' i seçin. **Azure SQL** listede yoksa, **tüm hizmetler**' i seçin ve arama kutusuna Azure SQL yazın. Seçim **Azure SQL** ' in yanındaki yıldızı seçerek bunu sık kullanılanlara ekleyin ve sol gezinti bölmesinde bir öğe olarak ekleyin. 
1. **+ Ekle** ' yı seçerek **SQL dağıtım seçeneğini seçin** sayfasını açın. Veritabanları kutucuğunda ayrıntıları göster ' i seçerek farklı veritabanları hakkındaki ek bilgileri görüntüleyebilirsiniz.
1. **SQL yönetilen örnekler** kutucuğunda **Oluştur** ' u seçin. 

    ![Yönetilen örnek seçin](media/sql-database-managed-instance-failover-group-tutorial/select-managed-instance.png)

1. **Azure SQL veritabanı yönetilen örneği oluştur** sayfasının **temel bilgiler** sekmesinde, ikincil yönetilen örneğinizi yapılandırmak için gerekli alanları doldurun. 

   Aşağıdaki tabloda, ikincil yönetilen örnek için gereken değerler gösterilmektedir:
 
    | **Alan** | Değer |
    | --- | --- |
    | **Abonelik** |  Birincil yönetilen örneğinizin bulunduğu abonelik. |
    | **Kaynak grubu**| Birincil yönetilen örneğinizin bulunduğu kaynak grubu. |
    | **Yönetilen örnek adı** | Yeni ikincil yönetilen örneğinizin adı, örneğin `sql-mi-secondary`  | 
    | **Bölge**| İkincil yönetilen örneğinizin konumu.  |
    | **Yönetilen örnek yöneticisi oturum açma** | `azureuser`gibi yeni ikincil yönetilen örneğiniz için kullanmak istediğiniz oturum açma. |
    | **Parola** | Yeni ikincil yönetilen örnek için yönetici oturumu tarafından kullanılacak karmaşık bir parola.  |
    | &nbsp; | &nbsp; |

1. **Ağ** sekmesinde, **sanal ağ**için, açılan listeden ikincil yönetilen örnek için oluşturduğunuz sanal ağı seçin.

   ![İkincil mı ağı](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. **Ek ayarlar** sekmesinde, **coğrafi çoğaltma**için, _Yük devretme Ikincili olarak kullanmak_için **Evet** ' i seçin. Açılan listeden birincil yönetilen örneği seçin. 
    1. Harmanlama ve saat diliminin, birincil yönetilen örnekle eşleştiğinden emin olun. Bu öğreticide oluşturulan birincil yönetilen örnek, `SQL_Latin1_General_CP1_CI_AS` harmanlama ve `(UTC) Coordinated Universal Time` saat dilimini kullanır. 

   ![İkincil mı ağı](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. İkincil yönetilen örneğinizin ayarlarını gözden geçirmek için **gözden geçir + oluştur** ' u seçin. 
1. İkincil yönetilen örneğinizi oluşturmak için **Oluştur** ' u seçin. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell kullanarak ikincil yönetilen örnek oluşturun. 

   ```powershell-interactive
   # Configure secondary virtual network
   Write-host "Configuring secondary virtual network..."
   
   $SecondaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $drlocation `
                         -Name $secondaryVNet `
                         -AddressPrefix $secondaryAddressPrefix
   
   Add-AzVirtualNetworkSubnetConfig `
                         -Name $secondaryMiSubnetName `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -AddressPrefix $secondaryMiSubnetAddress `
                       | Set-AzVirtualNetwork
   $SecondaryVirtualNetwork
   
   # Configure secondary managed instance subnet
   Write-host "Configuring secondary MI subnet..."
   
   $SecondaryVirtualNetwork = Get-AzVirtualNetwork -Name $secondaryVNet `
                                   -ResourceGroupName $resourceGroupName
   
   $secondaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $secondaryMiSubnetName `
                           -VirtualNetwork $SecondaryVirtualNetwork
   $secondaryMiSubnetConfig
   
   # Configure secondary network security group management service
   Write-host "Configuring secondary network security group management service..."
   
   $secondaryMiSubnetConfigId = $secondaryMiSubnetConfig.Id
   
   $secondaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'secondaryToMIManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryNSGMiManagementService
   
   # Configure secondary route table MI management service
   Write-host "Configuring secondary route table MI management service..."
   
   $secondaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'secondaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryRouteTableMiManagementService
   
   # Configure the secondary network security group
   Write-host "Configuring secondary network security group..."
   
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -Name $secondaryMiSubnetName `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NetworkSecurityGroup $secondaryNSGMiManagementService `
                         -RouteTable $secondaryRouteTableMiManagementService `
                       | Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryToMIManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $secondaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $secondaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "secondaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Secondary network security group configured successfully."
   
   # Create secondary managed instance
   
   $primaryManagedInstanceId = Get-AzSqlInstance -Name $primaryInstance -ResourceGroupName $resourceGroupName | Select-Object Id
   
   
   Write-host "Creating secondary managed instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $secondaryInstance `
                     -ResourceGroupName $resourceGroupName `
                     -Location $drLocation `
                     -SubnetId $secondaryMiSubnetConfigId `
                     -AdministratorCredential $mycreds `
                     -StorageSizeInGB $maxStorage `
                     -VCore $vCores `
                     -Edition $edition `
                     -ComputeGeneration $computeGeneration `
                     -LicenseType $license `
                     -DnsZonePartner $primaryManagedInstanceId.Id
   Write-host "Secondary managed instance created successfully."
   ```

Öğreticinin bu bölümü aşağıdaki PowerShell cmdlet 'lerini kullanır:

| Komut | Notlar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Bir Azure Kaynak grubu oluşturur.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Sanal ağ oluşturur.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Bir sanal ağa alt ağ yapılandırması ekler. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Bir kaynak grubundaki sanal ağı alır. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Bir sanal ağ içindeki bir alt ağı alır. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Ağ güvenlik grubu oluşturur. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Bir yol tablosu oluşturur. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Bir sanal ağ için alt ağ yapılandırmasını güncelleştirir.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Bir sanal ağı güncelleştirir.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Bir ağ güvenlik grubu alır. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Ağ güvenlik grubuna ağ güvenlik kuralı yapılandırması ekler. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Bir ağ güvenlik grubunu güncelleştirir.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Rota tablosuna bir yol ekler. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Bir yol tablosunu güncelleştirir.  |
| [New-Azsqlınstance](/powershell/module/az.sql/new-azsqlinstance) | Azure SQL veritabanı yönetilen örneği oluşturur.  |

---

## <a name="4---create-primary-gateway"></a>4-birincil ağ geçidi oluşturma 
Yük devretme grubuna katılacak iki yönetilen örnek için, ağ iletişimine izin vermek üzere iki yönetilen örnek sanal ağları arasında yapılandırılmış bir ağ geçidi olmalıdır. Azure portal kullanarak, birincil yönetilen örnek için ağ geçidini oluşturabilirsiniz. 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Azure portal kullanarak, birincil yönetilen örneğinizin sanal ağı için ağ geçidini oluşturun. 


1. [Azure Portal](https://portal.azure.com), kaynak grubunuza gidin ve birincil yönetilen örneğiniz için **sanal ağ** kaynağını seçin. 
1. **Ayarlar** altında **alt ağlar** ' ı seçin ve ardından yeni bir **ağ geçidi alt ağı**eklemeyi seçin. Varsayılan değerleri bırakın. 

   ![Birincil yönetilen örnek için ağ geçidi ekle](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. Alt ağ geçidi oluşturulduktan sonra sol gezinti bölmesinden **kaynak oluştur** ' u seçin ve arama kutusuna `Virtual network gateway` yazın. **Microsoft**tarafından yayınlanan **sanal ağ geçidi** kaynağını seçin. 

   ![Yeni bir sanal ağ geçidi oluştur](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Birincil yönetilen örneğinizin ağ geçidini yapılandırmak için gerekli alanları doldurun. 

   Aşağıdaki tabloda, birincil yönetilen örnek için ağ geçidi için gereken değerler gösterilmektedir:
 
    | **Alan** | Değer |
    | --- | --- |
    | **Abonelik** |  Birincil yönetilen örneğinizin bulunduğu abonelik. |
    | **Ad** | `primary-mi-gateway`gibi sanal ağ geçidinizin adı. | 
    | **Bölge** | İkincil yönetilen örneğinizin bulunduğu bölge. |
    | **Ağ Geçidi türü** | **VPN**' yi seçin. |
    | **VPN türü** | **Rota tabanlı** seçin |
    | **SKU**| `VpnGw1`varsayılan olarak bırakın. |
    | **Konum**| Birincil yönetilen örneğinizin ve birincil sanal ağınızın bulunduğu konum.   |
    | **Sanal ağ**| Bölüm 2 ' de oluşturulan sanal ağı seçin, örneğin `vnet-sql-mi-primary`. |
    | **Genel IP adresi**| **Yeni oluştur**’u seçin. |
    | **Genel IP adresi adı**| IP adresiniz için `primary-gateway-IP`gibi bir ad girin. |
    | &nbsp; | &nbsp; |

1. Diğer değerleri varsayılan olarak bırakın ve sonra sanal ağ geçidinizin ayarlarını gözden geçirmek için **gözden geçir + oluştur** ' u seçin.

   ![Birincil ağ geçidi ayarları](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Yeni sanal ağ geçidinizi oluşturmak için **Oluştur** ' u seçin. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell kullanarak birincil yönetilen örneğinizin sanal ağı için ağ geçidini oluşturun. 

   ```powershell-interactive
   # Create primary gateway
   Write-host "Adding GatewaySubnet to primary VNet..."
   Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $primaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $primaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName
   $primaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $primaryVirtualNetwork
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $primaryGWPublicIP = New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $location -AllocationMethod Dynamic
   $primaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -Subnet $primaryGatewaySubnet -PublicIpAddress $primaryGWPublicIP
   
   $primaryGateway = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $resourceGroupName `
       -Location $location -IpConfigurations $primaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $primaryGateway
   ```

Öğreticinin bu bölümü aşağıdaki PowerShell cmdlet 'lerini kullanır:

| Komut | Notlar |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Bir kaynak grubundaki sanal ağı alır. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Bir sanal ağa alt ağ yapılandırması ekler. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Bir sanal ağı güncelleştirir.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Bir sanal ağ içindeki bir alt ağı alır. |
| [New-Azpublicıpaddress](/powershell/module/az.network/new-azpublicipaddress) | Genel bir IP adresi oluşturur.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Sanal ağ geçidi için bir IP yapılandırması oluşturur |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Bir sanal ağ geçidi oluşturur |


---


## <a name="5---create-secondary-gateway"></a>5-ikincil ağ geçidi oluşturma 
Bu adımda, Azure portal kullanarak ikincil yönetilen örneğinizin sanal ağı için ağ geçidini oluşturun, 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Azure portal kullanarak, ikincil yönetilen örnek için sanal ağ alt ağını ve ağ geçidini oluşturmak üzere önceki bölümdeki adımları yineleyin. İkincil yönetilen örneğiniz için ağ geçidini yapılandırmak üzere gerekli alanları doldurun. 

   Aşağıdaki tabloda, ikincil yönetilen örnek için ağ geçidi için gereken değerler gösterilmektedir:

   | **Alan** | Değer |
   | --- | --- |
   | **Abonelik** |  İkincil yönetilen örneğinizin olduğu abonelik. |
   | **Ad** | `secondary-mi-gateway`gibi sanal ağ geçidinizin adı. | 
   | **Bölge** | İkincil yönetilen örneğinizin bulunduğu bölge. |
   | **Ağ Geçidi türü** | **VPN**' yi seçin. |
   | **VPN türü** | **Rota tabanlı** seçin |
   | **SKU**| `VpnGw1`varsayılan olarak bırakın. |
   | **Konum**| İkincil yönetilen örneğinizin ve ikincil sanal ağınızın bulunduğu konum.   |
   | **Sanal ağ**| Bölüm 2 ' de oluşturulan sanal ağı seçin, örneğin `vnet-sql-mi-secondary`. |
   | **Genel IP adresi**| **Yeni oluştur**’u seçin. |
   | **Genel IP adresi adı**| IP adresiniz için `secondary-gateway-IP`gibi bir ad girin. |
   | &nbsp; | &nbsp; |

   ![İkincil ağ geçidi ayarları](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell kullanarak ikincil yönetilen örneğin sanal ağı için ağ geçidini oluşturun. 

   ```powershell-interactive
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   
   Write-host "Adding GatewaySubnet to secondary VNet..."
   Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $secondaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $secondaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName
   $secondaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $secondaryVirtualNetwork
   $drLocation = $secondaryVirtualNetwork.Location
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $secondaryGWPublicIP = New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $drLocation -AllocationMethod Dynamic
   $secondaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -Subnet $secondaryGatewaySubnet -PublicIpAddress $secondaryGWPublicIP
   
   $secondaryGateway = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $resourceGroupName `
       -Location $drLocation -IpConfigurations $secondaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   $secondaryGateway
   ```

Öğreticinin bu bölümü aşağıdaki PowerShell cmdlet 'lerini kullanır:

| Komut | Notlar |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Bir kaynak grubundaki sanal ağı alır. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Bir sanal ağa alt ağ yapılandırması ekler. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Bir sanal ağı güncelleştirir.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Bir sanal ağ içindeki bir alt ağı alır. |
| [New-Azpublicıpaddress](/powershell/module/az.network/new-azpublicipaddress) | Genel bir IP adresi oluşturur.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Sanal ağ geçidi için bir IP yapılandırması oluşturur |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Bir sanal ağ geçidi oluşturur |

---


## <a name="6---connect-the-gateways"></a>6-ağ geçitlerini bağlama
Bu adımda iki sanal ağın iki ağ geçidi arasında çift yönlü bir bağlantı oluşturun. 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Azure portal kullanarak iki ağ geçidini bağlayın. 


1. Azure portal **kaynak oluştur** ' u seçin [](https://portal.azure.com).
1. Arama kutusuna `connection` yazın ve ardından arama yapmak için ENTER tuşuna basın ve Microsoft tarafından yayımlanan **bağlantı** kaynağına gidersiniz.
1. Bağlantınızı oluşturmak için **Oluştur** ' u seçin. 
1. **Temel bilgiler** sekmesinde, aşağıdaki değerleri seçip **Tamam**' ı seçin. 
    1. **Bağlantı türü**için `VNet-to-VNet` seçin. 
    1. Açılan listeden aboneliğinizi seçin. 
    1. Açılan kutuda yönetilen örneğiniz için kaynak grubunu seçin. 
    1. Açılan listeden birincil yönetilen örneğinizin konumunu seçin 
1. **Ayarlar** sekmesinde, aşağıdaki değerleri seçin veya girin ve sonra **Tamam**' ı seçin:
    1. **İlk sanal ağ geçidi**için `Primary-Gateway`gibi birincil ağ geçidini seçin.  
    1. **İkinci sanal ağ geçidi**için `Secondary-Gateway`gibi ikincil ağ geçidini seçin. 
    1. **Çift yönlü bağlantı oluştur**' un yanındaki onay kutusunu işaretleyin. 
    1. Varsayılan birincil bağlantı adını bırakın ya da seçtiğiniz bir değerle yeniden adlandırın. 
    1. Bağlantı için `mi1m2psk`gibi bir **paylaşılan anahtar (PSK)** sağlayın. 

   ![Ağ Geçidi bağlantısı oluştur](media/sql-database-managed-instance-failover-group-tutorial/create-gateway-connection.png)

1. **Özet** sekmesinde, çift yönlü bağlantınızın ayarlarını gözden geçirin ve ardından bağlantıyı oluşturmak için **Tamam** ' ı seçin. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell kullanarak iki ağ geçidini bağlayın. 

   ```powershell-interactive
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway to secondary gateway..."
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $primaryGateway -VirtualNetworkGateway2 $secondaryGateway -Location $location `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway to primary gateway..."
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $secondaryGateway -VirtualNetworkGateway2 $primaryGateway -Location $drLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

Öğreticinin bu bölümü aşağıdaki PowerShell cmdlet 'ini kullanır:

| Komut | Notlar |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | İki sanal ağ geçidi arasında bir bağlantı oluşturur.   |

---


## <a name="7---create-a-failover-group"></a>7-yük devretme grubu oluşturma
Bu adımda, yük devretme grubunu oluşturacak ve yönetilen örneklerin her ikisini de ekleyecek. 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Azure portal kullanarak yük devretme grubunu oluşturun. 


1. [Azure Portal](https://portal.azure.com)sol taraftaki menüden **Azure SQL** ' i seçin. **Azure SQL** listede yoksa, **tüm hizmetler**' i seçin ve arama kutusuna Azure SQL yazın. Seçim **Azure SQL** ' in yanındaki yıldızı seçerek bunu sık kullanılanlara ekleyin ve sol gezinti bölmesinde bir öğe olarak ekleyin. 
1. `sql-mi-primary`gibi ilk bölümde oluşturduğunuz birincil yönetilen örneği seçin. 
1. **Ayarlar**altında, **örnek yük devretme grupları** ' na gidin ve sonra **örnek yük devretme grubu** sayfasını açmak için **Grup Ekle** ' yi seçin. 

   ![Yük devretme grubu ekleme](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. **Örnek yük devretme grubu** sayfasında, `failovergrouptutorial` gibi yük devretme grubunuzun adını yazın ve ardından açılan listeden `sql-mi-secondary` gibi ikincil yönetilen örneği seçin. Yük devretme grubunuzu oluşturmak için **Oluştur** ' u seçin. 

   ![Yük devretme grubu oluştur](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Yük devretme grubu dağıtımı tamamlandıktan sonra, **Yük devretme grubu** sayfasına geri yönlendirilirsiniz. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
PowerShell kullanarak yük devretme grubunu oluşturun. 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

Öğreticinin bu bölümü aşağıdaki PowerShell cmdlet 'ini kullanır:

| Komut | Notlar |
|---|---|
| [New-Azsqldatabaseınstancefailovergroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Yeni bir Azure SQL veritabanı yönetilen örneği yük devretme grubu oluşturur.  |


---


## <a name="8---test-failover"></a>8-yük devretme testi
Bu adımda, yük devretme grubunuzu ikincil sunucuya devreder ve sonra Azure portal kullanarak yeniden başarısız olursunuz. 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Azure portal kullanarak yük devretmeyi test edin. 


1. [Azure Portal](https://portal.azure.com) içinde yönetilen örneğinize gidin ve ayarlar altında **örnek yük devretme grupları** ' nı seçin. 
1. Hangi yönetilen örnek birincil olduğunu ve hangi yönetilen örnek ikincil olduğunu gözden geçirin. 
1. **Yük devretme** ' yı seçin ve sonra kesilmekte olan tds oturumlarının uyarısında **Evet** ' i seçin. 

   ![Yük devretme grubu yükünü devreder](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Hangi manşlı örneğin birincil olduğunu ve hangi örneğin ikincil olduğunu gözden geçirin. Yük devretme başarılı olursa, iki örnek anahtarlamalı rollere sahip olmalıdır. 

   ![Yönetilen örnekler, yük devretmeden sonra rolleri değiştirdi](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Birincil örnek birincil role geri dönmek için **Yük devretmeyi** bir kez daha seçin. 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
PowerShell kullanarak yük devretmeyi test etme. 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   ```


Yük devretme grubunu birincil sunucuya geri çevir:

   ```powershell-interactive
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName
   
   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   ```

Öğreticinin bu bölümü aşağıdaki PowerShell cmdlet 'lerini kullanır:

| Komut | Notlar |
|---|---|
| [Get-Azsqldatabaseınstancefailovergroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Yönetilen örnek yük devretme gruplarını alır veya listeler.| 
| [Switch-Azsqldatabaseınstancefailovergroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Yönetilen örnek yük devretme grubunun yük devretmesini yürütür. | 

---



## <a name="clean-up-resources"></a>Kaynakları temizleme
Önce yönetilen örneği, ardından sanal kümeyi, ardından kalan kaynakları ve son olarak kaynak grubunu silerek kaynakları temizleyin. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. [Azure Portal](https://portal.azure.com)kaynak grubunuza gidin. 
1. Yönetilen örneği seçin ve **Sil**' i seçin. Kaynağı silmek istediğinizi onaylamak için metin kutusuna `yes` yazın ve ardından **Sil**' i seçin. Bu işlemin tamamlanması biraz zaman alabilir ve bu işlem "tamamlanana kadar" *sanal kümeyi* veya diğer bağımlı kaynakları silememeyeceksiniz. Yönetilen örneğinizin silindiğini onaylamak için etkinlik sekmesindeki silmeyi izleyin. 
1. Yönetilen örnek silindikten sonra, *sanal kümeyi* kaynak grubunuzda seçip **Sil**' i seçerek silin. Kaynağı silmek istediğinizi onaylamak için metin kutusuna `yes` yazın ve ardından **Sil**' i seçin. 
1. Kalan kaynakları silin. Kaynağı silmek istediğinizi onaylamak için metin kutusuna `yes` yazın ve ardından **Sil**' i seçin. 
1. Kaynak grubunu Sil ' i seçerek kaynak grubunu **Sil**' i seçin, kaynak grubunun adını yazın, `myResourceGroup`ve ardından **Sil**' i seçin. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Kaynak grubunu iki kez kaldırmanız gerekecektir. Kaynak grubunun ilk kez kaldırılması yönetilen örneği ve sanal kümeleri kaldırır, ancak `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'.`hata iletisiyle başarısız olur. Kalan kaynakları ve kaynak grubunu kaldırmak için Remove-AzResourceGroup komutunu ikinci kez çalıştırın.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing managed instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resouce group..."
```

Öğreticinin bu bölümü aşağıdaki PowerShell cmdlet 'ini kullanır:

| Komut | Notlar |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Bir kaynak grubunu kaldırır. |

---

## <a name="full-script"></a>Tam betik

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add managed instance to a failover group")]

Bu betik aşağıdaki komutları kullanır. Tablodaki her komut, komuta özgü belgelere yönlendirir.

| Komut | Notlar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Bir Azure Kaynak grubu oluşturur.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Sanal ağ oluşturur.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Bir sanal ağa alt ağ yapılandırması ekler. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Bir kaynak grubundaki sanal ağı alır. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Bir sanal ağ içindeki bir alt ağı alır. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Ağ güvenlik grubu oluşturur. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Bir yol tablosu oluşturur. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Bir sanal ağ için alt ağ yapılandırmasını güncelleştirir.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Bir sanal ağı güncelleştirir.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Bir ağ güvenlik grubu alır. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Ağ güvenlik grubuna ağ güvenlik kuralı yapılandırması ekler. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Bir ağ güvenlik grubunu güncelleştirir.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Rota tablosuna bir yol ekler. |
| [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Bir yol tablosunu güncelleştirir.  |
| [New-Azsqlınstance](/powershell/module/az.sql/new-azsqlinstance) | Azure SQL veritabanı yönetilen örneği oluşturur.  |
| [Get-Azsqlınstance](/powershell/module/az.sql/get-azsqlinstance)| Azure SQL yönetilen veritabanı örneği hakkında bilgi döndürür. |
| [New-Azpublicıpaddress](/powershell/module/az.network/new-azpublicipaddress) | Genel bir IP adresi oluşturur.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Sanal ağ geçidi için bir IP yapılandırması oluşturur |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Bir sanal ağ geçidi oluşturur |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | İki sanal ağ geçidi arasında bir bağlantı oluşturur.   |
| [New-Azsqldatabaseınstancefailovergroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Yeni bir Azure SQL veritabanı yönetilen örneği yük devretme grubu oluşturur.  |
| [Get-Azsqldatabaseınstancefailovergroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Yönetilen örnek yük devretme gruplarını alır veya listeler.| 
| [Switch-Azsqldatabaseınstancefailovergroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Yönetilen örnek yük devretme grubunun yük devretmesini yürütür. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Bir kaynak grubunu kaldırır. | 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal) 

Azure portal için kullanılabilir komut yok.

---

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, iki yönetilen örnek arasında bir yük devretme grubu yapılandırdınız. Şunları öğrendiniz:

> [!div class="checklist"]
> - Birincil yönetilen örnek oluşturma
> - Bir [Yük devretme grubunun](sql-database-auto-failover-group.md)parçası olarak ikincil bir yönetilen örnek oluşturun. 
> - Yük devretme testi

Yönetilen örneğinizle bağlantı kurmak ve yönetilen örneğiniz için bir veritabanını geri yüklemek için bir sonraki hızlı başlangıca ilerleyin: 

> [!div class="nextstepaction"]
> Yönetilen [örneğinizle bağlantı kurmak](sql-database-managed-instance-configure-vm.md)
> [bir veritabanını yönetilen örneğe geri yükleme](sql-database-managed-instance-get-started-restore.md)


