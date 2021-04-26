---
title: Şablon kullanarak Azure DS etki alanı Hizmetleri 'ni etkinleştirme | Microsoft Docs
description: Azure Resource Manager şablonu kullanarak Azure Active Directory Domain Services yapılandırmayı ve etkinleştirmeyi öğrenin
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: e18825da64d0d200f55ce72985ac843b93b1e612
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618799"
---
# <a name="create-an-azure-active-directory-domain-services-managed-domain-using-an-azure-resource-manager-template"></a>Azure Resource Manager şablonu kullanarak Azure Active Directory Domain Services yönetilen etki alanı oluşturma

Azure Active Directory Domain Services (Azure AD DS), Windows Server Active Directory ile tamamen uyumlu etki alanına katılması, Grup ilkesi, LDAP, Kerberos/NTLM kimlik doğrulaması gibi yönetilen etki alanı Hizmetleri sağlar. Etki alanı denetleyicilerini kendiniz dağıtmadan, yönetmeden ve düzeltme eki uygulamadan bu etki alanı hizmetlerini kullanırsınız. Azure AD DS, mevcut Azure AD kiracınızla tümleşir. Bu tümleştirme, kullanıcıların kurumsal kimlik bilgilerini kullanarak oturum açmasını sağlar ve kaynaklara erişimi güvenli hale getirmek için mevcut grupları ve Kullanıcı hesaplarını kullanabilirsiniz.

Bu makalede, Azure Resource Manager şablonu kullanarak yönetilen bir etki alanının nasıl oluşturulacağı gösterilmektedir. Destekleyici kaynaklar Azure PowerShell kullanılarak oluşturulur.

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

## <a name="dns-naming-requirements"></a>DNS adlandırma gereksinimleri

Azure AD DS yönetilen bir etki alanı oluşturduğunuzda bir DNS adı belirtirsiniz. Bu DNS adını seçerken bazı noktalar vardır:

* **Yerleşik etki alanı adı:** Varsayılan olarak, dizinin yerleşik etki alanı adı kullanılır (bir *. onmicrosoft.com* soneki). Yönetilen etki alanına internet üzerinden güvenli LDAP erişimini etkinleştirmek istiyorsanız, bu varsayılan etki alanı ile bağlantıyı güvenli hale getirmek için dijital bir sertifika oluşturamazsınız. Microsoft *. onmicrosoft.com* etki alanına sahip olduğundan, bir sertifika YETKILISI (CA) bir sertifika vermez.
* **Özel etki alanı adları:** En yaygın yaklaşım, genellikle zaten sahip olduğunuz ve yönlendirilebilir olan özel bir etki alanı adı belirtmektir. Yönlendirilebilir, özel bir etki alanı kullandığınızda, uygulamalarınızı desteklemek için gereken şekilde trafik doğru şekilde akabilir.
* **Yönlendirilemeyen etki alanı sonekleri:** Genellikle, *contoso. Local* gibi yönlendirilebilir olmayan bir etki alanı adı sonekini önlemenize tavsiye ederiz. *. Local* son eki yönlendirilebilir DEĞILDIR ve DNS çözümlenme sorunlarına neden olabilir.

> [!TIP]
> Özel bir etki alanı adı oluşturursanız, mevcut DNS ad alanları ile ilgilenin. Mevcut bir Azure veya şirket içi DNS ad alanından ayrı bir etki alanı adı kullanılması önerilir.
>
> Örneğin, *contoso.com* ADLı bir DNS ad alanınız varsa, *aaddscontoso.com* özel etki alanı adına sahip bir yönetilen etki alanı oluşturun. Güvenli LDAP kullanmanız gerekiyorsa, gerekli sertifikaları oluşturmak için bu özel etki alanı adını kaydetmeniz ve sahip olmanız gerekir.
>
> Ortamınızdaki diğer hizmetler için bazı ek DNS kayıtları veya ortamınızda var olan DNS adı alanları arasında koşullu DNS ileticileri oluşturmanız gerekebilir. Örneğin, kök DNS adını kullanarak bir siteyi barındıran bir Web sunucusu çalıştırırsanız, ek DNS girişleri gerektiren adlandırma çakışmaları olabilir.
>
> Bu örnekte ve nasıl yapılır makalelerinde, *aaddscontoso.com* özel etki alanı kısa bir örnek olarak kullanılır. Tüm komutlarda kendi etki alanı adınızı belirtin.

Aşağıdaki DNS adı kısıtlamaları da geçerlidir:

* **Etki alanı ön eki kısıtlamaları:** Ön eki 15 karakterden daha uzun olan yönetilen bir etki alanı oluşturamazsınız. Belirtilen etki alanı adınızın ön eki ( *aaddscontoso.com* etki alanı adında *aaddscontoso* gibi) 15 veya daha az karakter içermelidir.
* **Ağ adı çakışmaları:** Yönetilen etki alanınız için DNS etki alanı adı, sanal ağda zaten mevcut olmamalıdır. Özellikle, bir ad çakışmasına yol açacak aşağıdaki senaryoları kontrol edin:
    * Azure sanal ağında aynı DNS etki alanı adına sahip bir Active Directory etki alanınız zaten varsa.
    * Yönetilen etki alanını etkinleştirmeyi planladığınız sanal ağın, şirket içi ağınızla bir VPN bağlantısı varsa. Bu senaryoda, şirket içi ağınızda aynı DNS etki alanı adına sahip bir etki alanı olmadığından emin olun.
    * Azure sanal ağında bu adı taşıyan mevcut bir Azure bulut hizmetiniz varsa.

## <a name="create-required-azure-ad-resources"></a>Gerekli Azure AD kaynaklarını oluşturma

Azure AD DS, bir hizmet sorumlusu ve bir Azure AD grubu gerektirir. Bu kaynaklar yönetilen etki alanının verileri eşitlemesini sağlar ve yönetilen etki alanında hangi kullanıcıların yönetim izinlerine sahip olduğunu tanımlar.

İlk olarak, [register-AzResourceProvider][Register-AzResourceProvider] cmdlet 'ini kullanarak Azure AD Domain Services kaynak sağlayıcısını kaydedin:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Azure AD DS için [New-AzureADServicePrincipal][New-AzureADServicePrincipal] cmdlet 'ini kullanarak BIR Azure AD hizmet sorumlusu oluşturun ve kimliğini doğrulayın. Belirli bir uygulama KIMLIĞI, adı *6ba9a5d4-8456-4118-b521-9c5ca10cdf84* olan *etki alanı denetleyicisi Hizmetleri* olarak kullanılır. Bu uygulama KIMLIĞINI değiştirmeyin.

```powershell
New-AzureADServicePrincipal -AppId "6ba9a5d4-8456-4118-b521-9c5ca10cdf84"
```

Şimdi [New-AzureADGroup][New-AzureADGroup] cmdlet 'Ini kullanarak *AAD DC yöneticileri* adlı bir Azure AD grubu oluşturun. Daha sonra bu gruba eklenen kullanıcılara, yönetilen etki alanında yönetim görevlerini gerçekleştirmek için izinler verilir.

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

*AAD DC Administrators* grubu oluşturulduktan sonra [Add-AzureADGroupMember][Add-AzureADGroupMember] cmdlet 'ini kullanarak gruba bir kullanıcı ekleyin. Önce Get [-AzureADGroup][Get-AzureADGroup] cmdlet 'Ini kullanarak *AAD DC YÖNETICILERI* grubu nesne kimliği ' ni, sonra da [Get-AzureADUser][Get-AzureADUser] CMDLET 'ini kullanarak istenen kullanıcının nesne kimliğini alırsınız.

Aşağıdaki örnekte, UPN 'si olan hesabın kullanıcı nesne KIMLIĞI `admin@contoso.onmicrosoft.com` . Bu Kullanıcı hesabını *AAD DC Administrators* grubuna eklemek ISTEDIĞINIZ kullanıcının UPN 'si ile değiştirin:

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

Son olarak, [New-AzResourceGroup][New-AzResourceGroup] cmdlet 'ini kullanarak bir kaynak grubu oluşturun. Aşağıdaki örnekte, kaynak grubu *Myresourcegroup* olarak adlandırılır ve *westus* bölgesinde oluşturulur. Kendi adınızı ve istediğiniz bölgeyi kullanın:

```powershell
New-AzResourceGroup `
  -Name "myResourceGroup" `
  -Location "WestUS"
```

Kullanılabilirlik Alanları destekleyen bir bölge seçerseniz, Azure AD DS kaynakları daha fazla artıklık için bölgelere dağıtılır. Kullanılabilirlik Alanları, Azure bölgesi içinde fiziksel olarak benzersiz konumlardır. Her alan bağımsız güç, soğutma ve ağ bağlantısı ile donatılmış bir veya daha fazla veri merkezinden oluşur. Dayanıklılığı güvence altına almak için etkinleştirilmiş tüm bölgelerde en az üç ayrı alan vardır.

Azure AD DS bölgeler arasında dağıtılacak şekilde yapılandırmanız için bir şey yoktur. Azure platformu, kaynakların bölge dağılımını otomatik olarak işler. Daha fazla bilgi edinmek ve bölge kullanılabilirliğini görmek için bkz. [Azure 'da kullanılabilirlik alanları nedir?][availability-zones].

## <a name="resource-definition-for-azure-ad-ds"></a>Azure AD DS için kaynak tanımı

Kaynak Yöneticisi kaynak tanımının bir parçası olarak, aşağıdaki yapılandırma parametreleri gereklidir:

| Parametre               | Değer |
|-------------------------|---------|
| Etki              | Yönetilen etki alanınız için DNS etki alanı adı, ön eklerin ve çakışmaların adlandırılmasında önceki noktaları dikkate alır. |
| filteredSync            | Azure AD DS, Azure AD 'de bulunan *Tüm* kullanıcıları ve grupları ya da yalnızca belirli grupların *kapsamlı* bir eşitlemesini eşitlemenize olanak tanır.<br /><br /> Kapsamlı eşitleme hakkında daha fazla bilgi için bkz. [Azure AD Domain Services kapsamlı eşitleme][scoped-sync].|
| notificationSettings    | Yönetilen etki alanında oluşturulan herhangi bir uyarı varsa, e-posta bildirimleri gönderilebilir. <br /><br />Azure kiracının *genel yöneticileri* ve *AAD DC yöneticileri* grubunun üyeleri bu bildirimler için *etkinleştirilebilir* .<br /><br /> İsterseniz, dikkat gerektiren uyarılar olduğunda bildirim için ek alıcılar ekleyebilirsiniz.|
| domainConfigurationType | Varsayılan olarak, yönetilen bir etki alanı bir *Kullanıcı* Ormanı olarak oluşturulur. Bu tür bir orman, şirket içi AD DS ortamında oluşturulan kullanıcı hesapları da dahil olmak üzere Azure AD 'deki tüm nesneleri eşitler. Bir Kullanıcı Ormanı oluşturmak için bir *DomainConfiguration* değeri belirtmeniz gerekmez.<br /><br /> *Kaynak* ormanı yalnızca doğrudan Azure AD 'de oluşturulan kullanıcıları ve grupları eşitler. Kaynak ormanı oluşturmak için değeri *Resourcetrusting* olarak ayarlayın.<br /><br />*Kaynak* ormanları hakkında daha fazla bilgi için, bir tane kullanabilirsiniz ve şirket içi AD DS etki alanlarıyla orman güvenleri oluşturma hakkında daha fazla bilgi için bkz. [Azure AD DS kaynak ormanları genel bakış][resource-forests].|

Aşağıdaki sıkıştırılmış parametreler tanımı, bu değerlerin nasıl bildirildiği gösterilmektedir. *Aaddscontoso.com* adlı bir Kullanıcı ormanı, Azure AD 'deki tüm kullanıcılar yönetilen etki alanına eşitlenmiş olarak oluşturulur:

```json
"parameters": {
    "domainName": {
        "value": "aaddscontoso.com"
    },
    "filteredSync": {
        "value": "Disabled"
    },
    "notificationSettings": {
        "value": {
            "notifyGlobalAdmins": "Enabled",
            "notifyDcAdmins": "Enabled",
            "additionalRecipients": []
        }
    },
    [...]
}
```

Aşağıdaki sıkıştırılmış Kaynak Yöneticisi şablonu kaynak türü, yönetilen etki alanını tanımlamak ve oluşturmak için kullanılır. Bir Azure sanal ağı ve alt ağı zaten var olmalıdır veya Kaynak Yöneticisi şablonun bir parçası olarak oluşturulmalıdır. Yönetilen etki alanı bu alt ağa bağlı.

```json
"resources": [
    {
        "apiVersion": "2017-06-01",
        "type": "Microsoft.AAD/DomainServices",
        "name": "[parameters('domainName')]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
            "domainName": "[parameters('domainName')]",
            "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
            "filteredSync": "[parameters('filteredSync')]",
            "notificationSettings": "[parameters('notificationSettings')]"
        }
    },
    [...]
]
```

Bu parametreler ve kaynak türü, aşağıdaki bölümde gösterildiği gibi, yönetilen bir etki alanı dağıtmak için daha geniş bir Kaynak Yöneticisi şablonunun parçası olarak kullanılabilir.

## <a name="create-a-managed-domain-using-sample-template"></a>Örnek şablon kullanarak yönetilen etki alanı oluşturma

Aşağıdaki tüm Kaynak Yöneticisi örnek şablonu, yönetilen bir etki alanı ve destekleyici sanal ağ, alt ağ ve ağ güvenlik grubu kuralları oluşturur. Ağ güvenlik grubu kuralları, yönetilen etki alanının güvenliğini sağlamak için gereklidir ve trafiğin doğru şekilde akabilir olmasını sağlar. *AADDSCONTOSO.com* DNS adına sahip bir Kullanıcı ormanı, tüm KULLANıCıLAR Azure AD 'den eşitlenmiş olarak oluşturulur:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "apiVersion": {
            "value": "2017-06-01"
        },
        "domainConfigurationType": {
            "value": "FullySynced"
        },
        "domainName": {
            "value": "aaddscontoso.com"
        },
        "filteredSync": {
            "value": "Disabled"
        },
        "location": {
            "value": "westus"
        },
        "notificationSettings": {
            "value": {
                "notifyGlobalAdmins": "Enabled",
                "notifyDcAdmins": "Enabled",
                "additionalRecipients": []
            }
        },
        "subnetName": {
            "value": "aadds-subnet"
        },
        "vnetName": {
            "value": "aadds-vnet"
        },
        "vnetAddressPrefixes": {
            "value": [
                "10.1.0.0/24"
            ]
        },
        "subnetAddressPrefix": {
            "value": "10.1.0.0/24"
        },
        "nsgName": {
            "value": "aadds-nsg"
        }
    },
    "resources": [
        {
            "apiVersion": "2017-06-01",
            "type": "Microsoft.AAD/DomainServices",
            "name": "[parameters('domainName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
            ],
            "properties": {
                "domainName": "[parameters('domainName')]",
                "subnetId": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', parameters('vnetName'), '/subnets/', parameters('subnetName'))]",
                "filteredSync": "[parameters('filteredSync')]",
                "domainConfigurationType": "[parameters('domainConfigurationType')]",
                "notificationSettings": "[parameters('notificationSettings')]"
            }
        },
        {
            "type": "Microsoft.Network/NetworkSecurityGroups",
            "name": "[parameters('nsgName')]",
            "location": "[parameters('location')]",
            "properties": {
                "securityRules": [
                    {
                        "name": "AllowSyncWithAzureAD",
                        "properties": {
                            "access": "Allow",
                            "priority": 101,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "443"
                        }
                    },
                    {
                        "name": "AllowPSRemoting",
                        "properties": {
                            "access": "Allow",
                            "priority": 301,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "AzureActiveDirectoryDomainServices",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "5986"
                        }
                    },
                    {
                        "name": "AllowRD",
                        "properties": {
                            "access": "Allow",
                            "priority": 201,
                            "direction": "Inbound",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "CorpNetSaw",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*",
                            "destinationPortRange": "3389"
                        }
                    }
                ]
            },
            "apiVersion": "2018-04-01"
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('location')]",
            "apiVersion": "2018-04-01",
            "dependsOn": [
                "[concat('Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
            ],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": "[parameters('vnetAddressPrefixes')]"
                },
                "subnets": [
                    {
                        "name": "[parameters('subnetName')]",
                        "properties": {
                            "addressPrefix": "[parameters('subnetAddressPrefix')]",
                            "networkSecurityGroup": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/NetworkSecurityGroups/', parameters('nsgName'))]"
                            }
                        }
                    }
                ]
            }
        }
    ],
    "outputs": {}
}
```

Bu şablon, [Azure Portal][portal-deploy], [Azure PowerShell][powershell-deploy]veya CI/CD işlem hattı gibi tercih ettiğiniz dağıtım yöntemi kullanılarak dağıtılabilir. Aşağıdaki örnek [New-AzResourceGroupDeployment][New-AzResourceGroupDeployment] cmdlet 'ini kullanır. Kendi kaynak grubu adınızı ve şablon dosya adını belirtin:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myResourceGroup" -TemplateFile <path-to-template>
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
[availability-zones]: ../availability-zones/az-overview.md
[portal-deploy]: ../azure-resource-manager/templates/deploy-portal.md
[powershell-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[scoped-sync]: scoped-synchronization.md
[resource-forests]: concepts-resource-forest.md

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
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: ../cloud-shell/cloud-shell-windows-users.md
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain
[New-AzResourceGroupDeployment]: /powershell/module/Az.Resources/New-AzResourceGroupDeployment