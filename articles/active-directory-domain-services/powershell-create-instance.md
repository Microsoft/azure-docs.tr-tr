---
title: PowerShell kullanarak Azure DS etki alanı Hizmetleri 'ni etkinleştirme | Microsoft Docs
description: Azure AD PowerShell ve Azure PowerShell kullanarak Azure Active Directory Domain Services yapılandırmayı ve etkinleştirmeyi öğrenin.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 03/10/2021
ms.author: justinha
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8056e95b731b1818e10d7415cb813d6aba0ec7fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103149110"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>PowerShell kullanarak Azure Active Directory Domain Services etkinleştirme

Azure Active Directory Domain Services (Azure AD DS), Windows Server Active Directory ile tamamen uyumlu etki alanına katılması, Grup ilkesi, LDAP, Kerberos/NTLM kimlik doğrulaması gibi yönetilen etki alanı Hizmetleri sağlar. Etki alanı denetleyicilerini kendiniz dağıtmadan, yönetmeden ve düzeltme eki uygulamadan bu etki alanı hizmetlerini kullanırsınız. Azure AD DS, mevcut Azure AD kiracınızla tümleşir. Bu tümleştirme, kullanıcıların kurumsal kimlik bilgilerini kullanarak oturum açmasını sağlar ve kaynaklara erişimi güvenli hale getirmek için mevcut grupları ve Kullanıcı hesaplarını kullanabilirsiniz.

Bu makalede, PowerShell kullanarak Azure AD DS nasıl etkinleştirileceği gösterilmektedir.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlayabilmeniz için aşağıdaki kaynaklara ihtiyacınız vardır:

* Azure PowerShell'i yükleyip yapılandırın.
    * Gerekirse, [Azure PowerShell modülünü yüklemek ve Azure aboneliğinize bağlanmak](/powershell/azure/install-az-ps)için yönergeleri izleyin.
    * [Connect-AzAccount][Connect-AzAccount] cmdlet 'Ini kullanarak Azure aboneliğinizde oturum açın.
* Azure AD PowerShell 'i yükleyip yapılandırın.
    * Gerekirse, [Azure AD PowerShell modülünü yüklemek ve Azure AD 'ye bağlanmak](/powershell/azure/active-directory/install-adv2)için yönergeleri izleyin.
    * [Connect-AzureAD][Connect-AzureAD] cmdlet 'Ini kullanarak Azure AD kiracınızda oturum açarak emin olun.
* Azure AD DS 'yi etkinleştirmek için Azure AD kiracınızda *genel yönetici* ayrıcalıklarına sahip olmanız gerekir.
* Gerekli Azure AD DS kaynaklarını oluşturmak için Azure aboneliğinizde *katılımcı* ayrıcalıklarına sahip olmanız gerekir.

  > [!IMPORTANT]
  > **Az. ADDomainServices** PowerShell modülü önizlemedeyken, cmdlet 'ini kullanarak ayrı olarak yüklemelisiniz `Install-Module` .

  ```azurepowershell-interactive
  Install-Module -Name Az.ADDomainServices
  ```

## <a name="create-required-azure-ad-resources"></a>Gerekli Azure AD kaynaklarını oluşturma

Azure AD DS, yönetilen etki alanında hangi kullanıcıların yönetim izinlerine sahip olduğunu tanımlamak için kimlik doğrulamak ve iletişim kurmak üzere bir hizmet sorumlusu ve bir Azure AD grubu gerektirir.

İlk olarak, *etki alanı denetleyicisi Hizmetleri* adlı belirli BIR uygulama kimliği kullanarak BIR Azure AD hizmet sorumlusu oluşturun. Genel Azure 'da KIMLIK değeri *2565bd9d-dad50-47d4-8B85-4c97f669dc36*' dır. Diğer bulutlarda, değer *6ba9a5d4-8456-4118-b521-9c5ca10cdf84* olur. Bu uygulama KIMLIĞINI değiştirmeyin.

[New-AzureADServicePrincipal][New-AzureADServicePrincipal] cmdlet 'ini kullanarak BIR Azure AD hizmet sorumlusu oluşturun:

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Şimdi *AAD DC yöneticileri* adlı BIR Azure AD grubu oluşturun. Daha sonra bu gruba eklenen kullanıcılara, yönetilen etki alanında yönetim görevlerini gerçekleştirmek için izinler verilir.

İlk olarak, [Get-AzureADGroup][Get-AzureADGroup] cmdlet 'Ini kullanarak *AAD DC YÖNETICILERI* grubu nesne kimliğini alın. Grup yoksa, [New-AzureADGroup][New-AzureADGroup] cmdlet 'Ini kullanarak *AAD DC Administrators* grubu ile oluşturun:

```powershell
# First, retrieve the object ID of the 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# If the group doesn't exist, create it
if (!$GroupObjectId) {
  $GroupObjectId = New-AzureADGroup -DisplayName "AAD DC Administrators" `
    -Description "Delegated group to administer Azure AD Domain Services" `
    -SecurityEnabled $true `
    -MailEnabled $false `
    -MailNickName "AADDCAdministrators"
  }
else {
  Write-Output "Admin group already exists."
}
```

*AAD DC Administrators* grubu oluşturulduktan sonra, [Get-AzureADUser][Get-AzureADUser] cmdlet 'ini kullanarak istenen kullanıcının nesne kimliğini alın, ardından [Add-azureadgroupmember][Add-AzureADGroupMember] cmdlet 'ini kullanarak kullanıcıyı gruba ekleyin.

Aşağıdaki örnekte, UPN 'si olan hesabın kullanıcı nesne KIMLIĞI `admin@contoso.onmicrosoft.com` . Bu Kullanıcı hesabını *AAD DC Administrators* grubuna eklemek ISTEDIĞINIZ kullanıcının UPN 'si ile değiştirin:

```powershell
# Retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-network-resources"></a>Ağ kaynakları oluşturma

İlk olarak, [register-AzResourceProvider][Register-AzResourceProvider] cmdlet 'ini kullanarak Azure AD Domain Services kaynak sağlayıcısını kaydedin:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Sonra, [New-AzResourceGroup][New-AzResourceGroup] cmdlet 'ini kullanarak bir kaynak grubu oluşturun. Aşağıdaki örnekte, kaynak grubu *Myresourcegroup* olarak adlandırılır ve *westus* bölgesinde oluşturulur. Kendi adınızı ve istediğiniz bölgeyi kullanın:

```azurepowershell-interactive
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Azure AD Domain Services için sanal ağ ve alt ağlar oluşturun. İki alt ağ oluşturulur- *DomainServices* için bir, diğeri *iş yükleri* için. Azure AD DS, adanmış *DomainServices* alt ağına dağıtılır. Bu alt ağa diğer uygulamaları veya iş yüklerini dağıtmayın. VM 'lerinizin geri kalanı için ayrı *Iş yüklerini* veya diğer alt ağları kullanın.

[New-AzVirtualNetworkSubnetConfig][New-AzVirtualNetworkSubnetConfig] cmdlet 'ini kullanarak alt ağları oluşturun, ardından [New-AzVirtualNetwork][New-AzVirtualNetwork] cmdlet 'ini kullanarak sanal ağı oluşturun.

```azurepowershell-interactive
$VnetName = "myVnet"

# Create the dedicated subnet for Azure AD Domain Services.
$SubnetName = "DomainServices"
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -AddressPrefix 10.0.0.0/24

# Create an additional subnet for your own VM workloads
$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet= New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```

### <a name="create-a-network-security-group"></a>Ağ güvenlik grubu oluşturma

Azure AD DS, yönetilen etki alanı için gereken bağlantı noktalarının güvenliğini sağlamak ve diğer tüm gelen trafiği engellemek için bir ağ güvenlik grubu gerektirir. Bir [ağ güvenlik grubu (NSG)][nsg-overview] , bir Azure sanal ağındaki trafiğe ağ trafiğine izin veren veya reddeden kuralların listesini içerir. Azure AD DS 'de ağ güvenlik grubu, yönetilen etki alanına erişimi kilitlemek için ek bir koruma katmanı işlevi görür. Gerekli olan bağlantı noktalarını görüntülemek için bkz. [ağ güvenlik grupları ve gerekli bağlantı noktaları][network-ports].

Aşağıdaki PowerShell cmdlet 'leri, kuralları oluşturmak için [New-AzNetworkSecurityRuleConfig][New-AzNetworkSecurityRuleConfig] ' i kullanır ve ardından ağ güvenlik grubunu oluşturmak için [New-AzNetworkSecurityGroup][New-AzNetworkSecurityGroup] ' u kullanır. Ağ güvenlik grubu ve kuralları, [set-AzVirtualNetworkSubnetConfig][Set-AzVirtualNetworkSubnetConfig] cmdlet 'ini kullanarak sanal ağ alt ağıyla ilişkilendirilir.

```azurepowershell-interactive
$NSGName = "aaddsNSG"

# Create a rule to allow inbound TCP port 3389 traffic from Microsoft secure access workstations for troubleshooting
$nsg201 = New-AzNetworkSecurityRuleConfig -Name AllowRD `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 201 `
    -SourceAddressPrefix CorpNetSaw `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389

# Create a rule to allow TCP port 5986 traffic for PowerShell remote management
$nsg301 = New-AzNetworkSecurityRuleConfig -Name AllowPSRemoting `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 301 `
    -SourceAddressPrefix AzureActiveDirectoryDomainServices `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 5986

# Create the network security group and rules
$nsg = New-AzNetworkSecurityGroup -Name $NSGName `
    -ResourceGroupName $ResourceGroupName `
    -Location $AzureLocation `
    -SecurityRules $nsg201,$nsg301

# Get the existing virtual network resource objects and information
$vnet = Get-AzVirtualNetwork -Name $VnetName -ResourceGroupName $ResourceGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $SubnetName
$addressPrefix = $subnet.AddressPrefix

# Associate the network security group with the virtual network subnet
Set-AzVirtualNetworkSubnetConfig -Name $SubnetName `
    -VirtualNetwork $vnet `
    -AddressPrefix $addressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

## <a name="create-a-managed-domain"></a>Yönetilen etki alanı oluşturma

Şimdi bir yönetilen etki alanı oluşturalım. Azure abonelik KIMLIĞINIZI ayarlayın ve ardından yönetilen etki alanı için *aaddscontoso.com* gibi bir ad sağlayın. [Get-AzSubscription][Get-AzSubscription] cmdlet 'ini kullanarak abonelik kimliğinizi alabilirsiniz.

Kullanılabilirlik Alanları destekleyen bir bölge seçerseniz, Azure AD DS kaynakları daha fazla artıklık için bölgelere dağıtılır.

Kullanılabilirlik Alanları, Azure bölgesi içinde fiziksel olarak benzersiz konumlardır. Her alan bağımsız güç, soğutma ve ağ bağlantısı ile donatılmış bir veya daha fazla veri merkezinden oluşur. Dayanıklılığı güvence altına almak için etkinleştirilmiş tüm bölgelerde en az üç ayrı alan vardır.

Azure AD DS bölgeler arasında dağıtılacak şekilde yapılandırmanız için bir şey yoktur. Azure platformu, kaynakların bölge dağılımını otomatik olarak işler. Daha fazla bilgi edinmek ve bölge kullanılabilirliğini görmek için bkz. [Azure 'da kullanılabilirlik alanları nedir?][availability-zones].

```azurepowershell-interactive
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Enable Azure AD Domain Services for the directory.
$replicaSetParams = @{
  Location = $AzureLocation
  SubnetId = "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"
}
$replicaSet = New-AzADDomainServiceReplicaSetObject @replicaSetParams

$domainServiceParams = @{
  Name = $ManagedDomainName
  ResourceGroupName = $ResourceGroupName
  DomainName = $ManagedDomainName
  ReplicaSet = $replicaSet
}
New-AzADDomainService @domainServiceParams
```

Kaynağın oluşturulması ve denetimin PowerShell istemine döndürülmesi birkaç dakika sürer. Yönetilen etki alanı arka planda sağlanmaya devam eder ve dağıtımın tamamlanması bir saate kadar sürebilir. Azure portal, yönetilen etki alanınız için **genel bakış** sayfasında, bu dağıtım aşamasının tamamında geçerli durum gösterilir.

Azure portal yönetilen etki alanının sağlamayı bitirmiş olduğunu gösteriyorsa, aşağıdaki görevlerin tamamlanması gerekir:

* Sanal makinelerin, etki alanına katılması veya kimlik doğrulaması için yönetilen etki alanını bulabileceği şekilde sanal ağ için DNS ayarlarını güncelleştirin.
    * DNS 'yi yapılandırmak için portalda yönetilen etki alanınızı seçin. **Genel bakış** penceresinde, bu DNS ayarlarını otomatik olarak yapılandırmanız istenir.
* [Azure AD DS parola eşitlemesini etkinleştirerek](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) son kullanıcıların, Şirket kimlik bilgilerini kullanarak yönetilen etki alanında oturum açabilirler.

## <a name="complete-powershell-script"></a>PowerShell betiğini Tamam

Aşağıdaki tamamlanmış PowerShell betiği, bu makalede gösterilen tüm görevleri birleştirir. Betiği kopyalayın ve uzantılı bir dosyaya kaydedin `.ps1` . Betiği yerel bir PowerShell konsolunda veya [Azure Cloud Shell][cloud-shell]çalıştırın.

> [!NOTE]
> Azure AD DS 'yi etkinleştirmek için Azure AD kiracısı için bir genel yönetici olmanız gerekir. Azure aboneliğinde en az *katkıda bulunan* ayrıcalıklara de ihtiyacınız vardır.

```azurepowershell-interactive
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
$ResourceGroupName = "myResourceGroup"
$VnetName = "myVnet"
$AzureLocation = "westus"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"

# First, retrieve the object ID of the 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Create the delegated administration group for Azure AD Domain Services if it doesn't already exist.
if (!$GroupObjectId) {
  $GroupObjectId = New-AzureADGroup -DisplayName "AAD DC Administrators" `
    -Description "Delegated group to administer Azure AD Domain Services" `
    -SecurityEnabled $true `
    -MailEnabled $false `
    -MailNickName "AADDCAdministrators"
  }
else {
  Write-Output "Admin group already exists."
}

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq '$AaddsAdminUserUpn'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId

# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$SubnetName = "DomainServices"
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

$NSGName = "aaddsNSG"

# Create a rule to allow inbound TCP port 3389 traffic from Microsoft secure access workstations for troubleshooting
$nsg201 = New-AzNetworkSecurityRuleConfig -Name AllowRD `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 201 `
    -SourceAddressPrefix CorpNetSaw `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389

# Create a rule to allow TCP port 5986 traffic for PowerShell remote management
$nsg301 = New-AzNetworkSecurityRuleConfig -Name AllowPSRemoting `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 301 `
    -SourceAddressPrefix AzureActiveDirectoryDomainServices `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 5986

# Create the network security group and rules
$nsg = New-AzNetworkSecurityGroup -Name $NSGName `
    -ResourceGroupName $ResourceGroupName `
    -Location $AzureLocation `
    -SecurityRules $nsg201,$nsg301

# Get the existing virtual network resource objects and information
$vnet = Get-AzVirtualNetwork -Name $VnetName -ResourceGroupName $ResourceGroupName
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $SubnetName
$addressPrefix = $subnet.AddressPrefix

# Associate the network security group with the virtual network subnet
Set-AzVirtualNetworkSubnetConfig -Name $SubnetName `
    -VirtualNetwork $vnet `
    -AddressPrefix $addressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork

# Enable Azure AD Domain Services for the directory.
$replicaSetParams = @{
  Location = $AzureLocation
  SubnetId = "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"
}
$replicaSet = New-AzADDomainServiceReplicaSetObject @replicaSetParams

$domainServiceParams = @{
  Name = $ManagedDomainName
  ResourceGroupName = $ResourceGroupName
  DomainName = $ManagedDomainName
  ReplicaSet = $replicaSet
}
New-AzADDomainService @domainServiceParams
```

Kaynağın oluşturulması ve denetimin PowerShell istemine döndürülmesi birkaç dakika sürer. Yönetilen etki alanı arka planda sağlanmaya devam eder ve dağıtımın tamamlanması bir saate kadar sürebilir. Azure portal, yönetilen etki alanınız için **genel bakış** sayfasında, bu dağıtım aşamasının tamamında geçerli durum gösterilir.

Azure portal yönetilen etki alanının sağlamayı bitirmiş olduğunu gösteriyorsa, aşağıdaki görevlerin tamamlanması gerekir:

* Sanal makinelerin, etki alanına katılması veya kimlik doğrulaması için yönetilen etki alanını bulabileceği şekilde sanal ağ için DNS ayarlarını güncelleştirin.
    * DNS 'yi yapılandırmak için portalda yönetilen etki alanınızı seçin. **Genel bakış** penceresinde, bu DNS ayarlarını otomatik olarak yapılandırmanız istenir.
* [Azure AD DS parola eşitlemesini etkinleştirerek](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) son kullanıcıların, Şirket kimlik bilgilerini kullanarak yönetilen etki alanında oturum açabilirler.

## <a name="next-steps"></a>Sonraki adımlar

Yönetilen etki alanını işlem içinde görmek için [bir WINDOWS sanal makinesine etki alanına katılabilir][windows-join], [Güvenli LDAP yapılandırabilir][tutorial-ldaps]ve [Parola karması eşitlemesini yapılandırabilirsiniz][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md
[nsg-overview]: ../virtual-network/network-security-groups-overview.md
[network-ports]: network-considerations.md#network-security-groups-and-required-ports

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[New-AzVirtualNetworkSubnetConfig]: /powershell/module/Az.Network/New-AzVirtualNetworkSubnetConfig
[New-AzVirtualNetwork]: /powershell/module/Az.Network/New-AzVirtualNetwork
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: ../cloud-shell/cloud-shell-windows-users.md
[availability-zones]: ../availability-zones/az-overview.md
[New-AzNetworkSecurityRuleConfig]: /powershell/module/az.network/new-aznetworksecurityruleconfig
[New-AzNetworkSecurityGroup]: /powershell/module/az.network/new-aznetworksecuritygroup
[Set-AzVirtualNetworkSubnetConfig]: /powershell/module/az.network/set-azvirtualnetworksubnetconfig
