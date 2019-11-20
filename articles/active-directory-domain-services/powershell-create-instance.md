---
title: PowerShell kullanarak Azure DS etki alanı Hizmetleri 'ni etkinleştirme | Microsoft Docs
description: Azure AD PowerShell ve Azure PowerShell kullanarak Azure Active Directory Domain Services yapılandırmayı ve etkinleştirmeyi öğrenin.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: iainfou
ms.openlocfilehash: 961b54a4d7c9caee98497e5d2b8db86284084d15
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73023873"
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

## <a name="create-required-azure-ad-resources"></a>Gerekli Azure AD kaynaklarını oluşturma

Azure AD DS, bir hizmet sorumlusu ve bir Azure AD grubu gerektirir. Bu kaynaklar, Azure AD DS yönetilen etki alanının verileri eşitlemesini ve yönetilen etki alanında hangi kullanıcıların yönetim izinlerine sahip olduğunu tanımlamanızı sağlar.

İlk olarak, iletişim kurmak ve kimliğini doğrulamak için Azure AD DS için bir Azure AD hizmet sorumlusu oluşturun. Belirli bir uygulama KIMLIĞI, *2565bd9d-dad50-47d4-8B85-4c97f669dc36*kimliğine sahip *etki alanı denetleyicisi Hizmetleri* adında kullanılır. Bu uygulama KIMLIĞINI değiştirmeyin.

[New-AzureADServicePrincipal][New-AzureADServicePrincipal] cmdlet 'ini kullanarak BIR Azure AD hizmet sorumlusu oluşturun:

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Şimdi *AAD DC yöneticileri*adlı BIR Azure AD grubu oluşturun. Daha sonra bu gruba eklenen kullanıcılara Azure AD DS yönetilen etki alanında yönetim görevlerini gerçekleştirmek için izinler verilir.

[New-AzureADGroup][New-AzureADGroup] cmdlet 'Ini kullanarak *AAD DC yöneticileri* grubunu oluşturun:

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

*AAD DC Administrators* grubu oluşturulduktan sonra [Add-AzureADGroupMember][Add-AzureADGroupMember] cmdlet 'ini kullanarak gruba bir kullanıcı ekleyin. Önce Get [-AzureADGroup][Get-AzureADGroup] cmdlet 'Ini kullanarak *AAD DC YÖNETICILERI* grubu nesne kimliği ' ni, sonra da [Get-AzureADUser][Get-AzureADUser] CMDLET 'ini kullanarak istenen kullanıcının nesne kimliğini alırsınız.

Aşağıdaki örnekte, `admin@contoso.onmicrosoft.com`UPN 'si olan hesabın kullanıcı nesne KIMLIĞI. Bu Kullanıcı hesabını *AAD DC Administrators* grubuna eklemek ISTEDIĞINIZ kullanıcının UPN 'si ile değiştirin:

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-supporting-azure-resources"></a>Destekleyici Azure kaynakları oluşturma

İlk olarak, [register-AzResourceProvider][Register-AzResourceProvider] cmdlet 'ini kullanarak Azure AD Domain Services kaynak sağlayıcısını kaydedin:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Sonra, [New-AzResourceGroup][New-AzResourceGroup] cmdlet 'ini kullanarak bir kaynak grubu oluşturun. Aşağıdaki örnekte, kaynak grubu *Myresourcegroup* olarak adlandırılır ve *westus* bölgesinde oluşturulur. Kendi adınızı ve istediğiniz bölgeyi kullanın:

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Azure AD Domain Services için sanal ağ ve alt ağlar oluşturun. İki alt ağ oluşturulur- *DomainServices*için bir, diğeri *iş yükleri*için. Azure AD DS, adanmış *DomainServices* alt ağına dağıtılır. Bu alt ağa diğer uygulamaları veya iş yüklerini dağıtmayın. VM 'lerinizin geri kalanı için ayrı *Iş yüklerini* veya diğer alt ağları kullanın.

[New-AzVirtualNetworkSubnetConfig][New-AzVirtualNetworkSubnetConfig] cmdlet 'ini kullanarak alt ağları oluşturun, ardından [New-AzVirtualNetwork][New-AzVirtualNetwork] cmdlet 'ini kullanarak sanal ağı oluşturun.

```powershell
$VnetName = "myVnet"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

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

## <a name="create-an-azure-ad-ds-managed-domain"></a>Azure AD DS yönetilen etki alanı oluşturma

Şimdi Azure AD DS yönetilen bir etki alanı oluşturalım. Azure abonelik KIMLIĞINIZI ayarlayın ve ardından yönetilen etki alanı için *contoso.com*gibi bir ad sağlayın. [Get-AzSubscription][Get-AzSubscription] cmdlet 'ini kullanarak abonelik kimliğinizi alabilirsiniz.

Kullanılabilirlik Alanları destekleyen bir bölge seçerseniz, Azure AD DS kaynakları daha fazla artıklık için bölgelere dağıtılır.

Kullanılabilirlik Alanları, Azure bölgesi içinde fiziksel olarak benzersiz konumlardır. Her alan bağımsız güç, soğutma ve ağ bağlantısı ile donatılmış bir veya daha fazla veri merkezinden oluşur. Dayanıklılık sağlamak için, tüm etkin bölgelerde en az üç ayrı bölge vardır.

Azure AD DS bölgeler arasında dağıtılacak şekilde yapılandırmanız için bir şey yoktur. Azure platformu, kaynakların bölge dağılımını otomatik olarak işler. Daha fazla bilgi edinmek ve bölge kullanılabilirliğini görmek için bkz. [Azure 'da kullanılabilirlik alanları nedir?][availability-zones].

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso.com"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

Kaynağın oluşturulması ve denetimin PowerShell istemine döndürülmesi birkaç dakika sürer. Azure AD DS yönetilen etki alanı arka planda sağlanmaya devam eder ve dağıtımın tamamlanması bir saate kadar sürebilir. Azure portal Azure AD DS yönetilen etki alanınız için **genel bakış** sayfasında, bu dağıtım aşamasının tamamında geçerli durum gösterilir.

Azure portal Azure AD DS yönetilen etki alanının sağlamayı bitirmiş olduğunu gösteriyorsa, aşağıdaki görevlerin tamamlanması gerekir:

* Sanal makinelerin, etki alanına katılması veya kimlik doğrulaması için yönetilen etki alanını bulabileceği şekilde sanal ağ için DNS ayarlarını güncelleştirin.
    * DNS 'yi yapılandırmak için portalda Azure AD DS yönetilen etki alanınızı seçin. **Genel bakış** penceresinde, bu DNS ayarlarını otomatik olarak yapılandırmanız istenir.
* Kullanılabilirlik Alanları destekleyen bir bölgede Azure AD DS yönetilen etki alanı oluşturduysanız, Azure AD DS yönetilen etki alanı için sanal ağdaki trafiği kısıtlamak üzere bir ağ güvenlik grubu oluşturun. Bu kuralların gerçekleşmesini gerektiren bir Azure Standart yük dengeleyici oluşturulur. Bu ağ güvenlik grubu, Azure AD DS güvenliğini sağlar ve yönetilen etki alanının düzgün çalışması için gereklidir.
    * Ağ güvenlik grubunu ve gerekli kuralları oluşturmak için, portalda Azure AD DS yönetilen etki alanınızı seçin. **Genel bakış** penceresinde ağ güvenlik grubunu otomatik olarak oluşturmanız ve yapılandırmanız istenir.
* Son kullanıcıların şirket kimlik bilgilerini kullanarak yönetilen etki alanında oturum açmasını sağlamak [için parola eşitlemesini Azure AD Domain Services etkinleştirin](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) .

## <a name="complete-powershell-script"></a>PowerShell betiğini Tamam

Aşağıdaki tamamlanmış PowerShell betiği, bu makalede gösterilen tüm görevleri birleştirir. Betiği kopyalayın ve `.ps1` uzantılı bir dosyaya kaydedin. Betiği yerel bir PowerShell konsolunda veya [Azure Cloud Shell][cloud-shell]çalıştırın.

> [!NOTE]
> Azure AD DS 'yi etkinleştirmek için Azure AD kiracısı için bir genel yönetici olmanız gerekir. Azure aboneliğinde en az *katkıda bulunan* ayrıcalıklara de ihtiyacınız vardır.

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
$ResourceGroupName = "myResourceGroup"
$VnetName = "myVnet"
$AzureLocation = "westus"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso.com"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"

# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"

# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

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

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

Kaynağın oluşturulması ve denetimin PowerShell istemine döndürülmesi birkaç dakika sürer. Azure AD DS yönetilen etki alanı arka planda sağlanmaya devam eder ve dağıtımın tamamlanması bir saate kadar sürebilir. Azure portal Azure AD DS yönetilen etki alanınız için **genel bakış** sayfasında, bu dağıtım aşamasının tamamında geçerli durum gösterilir.

Azure portal Azure AD DS yönetilen etki alanının sağlamayı bitirmiş olduğunu gösteriyorsa, aşağıdaki görevlerin tamamlanması gerekir:

* Sanal makinelerin, etki alanına katılması veya kimlik doğrulaması için yönetilen etki alanını bulabileceği şekilde sanal ağ için DNS ayarlarını güncelleştirin.
    * DNS 'yi yapılandırmak için portalda Azure AD DS yönetilen etki alanınızı seçin. **Genel bakış** penceresinde, bu DNS ayarlarını otomatik olarak yapılandırmanız istenir.
* Kullanılabilirlik Alanları destekleyen bir bölgede Azure AD DS yönetilen etki alanı oluşturduysanız, Azure AD DS yönetilen etki alanı için sanal ağdaki trafiği kısıtlamak üzere bir ağ güvenlik grubu oluşturun. Bu kuralların gerçekleşmesini gerektiren bir Azure Standart yük dengeleyici oluşturulur. Bu ağ güvenlik grubu, Azure AD DS güvenliğini sağlar ve yönetilen etki alanının düzgün çalışması için gereklidir.
    * Ağ güvenlik grubunu ve gerekli kuralları oluşturmak için, portalda Azure AD DS yönetilen etki alanınızı seçin. **Genel bakış** penceresinde ağ güvenlik grubunu otomatik olarak oluşturmanız ve yapılandırmanız istenir.
* Son kullanıcıların şirket kimlik bilgilerini kullanarak yönetilen etki alanında oturum açmasını sağlamak [için parola eşitlemesini Azure AD Domain Services etkinleştirin](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) .

## <a name="next-steps"></a>Sonraki adımlar

Azure AD DS yönetilen etki alanını işlem içinde görmek için [bir WINDOWS sanal makinesine etki alanına katılabilir][windows-join], [Güvenli LDAP yapılandırabilir][tutorial-ldaps]ve [Parola karması eşitlemesini yapılandırabilirsiniz][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md

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
[cloud-shell]: /azure/cloud-shell/cloud-shell-windows-users
[availability-zones]: ../availability-zones/az-overview.md
