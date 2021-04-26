---
title: Azure 'da çok kiracılı barındırma haklarıyla Windows 10 ' un dağıtımı
description: Çoklu kiracı barındırma haklarıyla Azure 'a şirket içi lisanslar getirmek için Windows yazılım güvencesi avantajlarınızı nasıl en üst düzeye çıkaracağınızı öğrenin.
author: mimckitt
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 2/2/2021
ms.author: mimckitt
ms.custom: rybaker, chmimckitt
ms.openlocfilehash: bb86ba6867ad796ef0f5eeb1357a6df9e93e9f9e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102555780"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Azure 'da çok kiracılı barındırma haklarıyla Windows 10 ' un dağıtımı 
Windows 10 Enterprise E3/E5 veya Kullanıcı başına Windows sanal masaüstü erişimi (Kullanıcı aboneliği lisansları veya eklenti Kullanıcı aboneliği lisansları) olan müşteriler için, Windows 10 için çok kiracılı barındırma hakları, Windows 10 lisanslarınızı buluta getirmenize ve Azure 'da Windows 10 sanal makinelerini başka bir lisans ödemeksizin çalıştırmanıza olanak sağlar. Çoklu kiracı barındırma hakları yalnızca Windows 10 (sürüm 1703 veya üzeri) için kullanılabilir.

Daha fazla bilgi için bkz. [Windows 10 Için çok kiracılı barındırma](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> - Windows 7, geliştirme veya test için 8,1 ve 10 görüntü kullanmak için bkz. geliştirme ve [test senaryoları Için Azure 'Da Windows istemcisi](client-images.md) .
> - Windows Server lisanslama avantajları için lütfen [Windows Server görüntüleri Için Azure hibrit kullanım avantajları](hybrid-use-benefit-licensing.md)bölümüne bakın.

## <a name="subscription-licenses-that-qualify-for-multitenant-hosting-rights"></a>Çok kiracılı barındırma hakları için uygun olan abonelik lisansları

[Microsoft Yönetim merkezini](/microsoft-365/admin/admin-overview/about-the-admin-center)kullanarak, bir kullanıcıya Windows 10 tarafından desteklenen bir lisans atanıp atanmadığını doğrulayabilirsiniz.

> [!IMPORTANT]
> Kullanıcıların Azure 'da Windows 10 görüntülerini kullanabilmesi için aşağıdaki abonelik lisanslarından birine sahip olmaları gerekir. Bu abonelik lisanslarından birine sahip değilseniz, [bulut hizmeti Iş ortağınız](https://azure.microsoft.com/overview/choosing-a-cloud-service-provider/) aracılığıyla veya doğrudan [Microsoft](https://www.microsoft.com/microsoft-365?rtc=1)aracılığıyla satın alınabilir.

**Uygun Abonelik lisansları:**

-   Microsoft 365 E3/E5 
-   Microsoft 365 F3 
-   Microsoft 365 a3/a5 
-   Windows 10 Enterprise E3/E5
-   Windows 10 eğitim a3/a5 
-   Windows VDA E3/E5


## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Azure Marketi 'nden Windows 10 görüntüsünü dağıtma 
PowerShell, CLı ve Azure Resource Manager şablon dağıtımları için, ve kullanılarak Windows 10 görüntüleri bulunabilir `PublisherName: MicrosoftWindowsDesktop` `Offer: Windows-10` . Çok kiracılı barındırma hakları için Windows 10 sürüm oluşturucuları Güncelleştirmesi (1809) veya üzeri desteklenir. 

```powershell
Get-AzVmImageSku -Location '$location' -PublisherName 'MicrosoftWindowsDesktop' -Offer 'Windows-10'

Skus                        Offer      PublisherName           Location 
----                        -----      -------------           -------- 
rs4-pro                     Windows-10 MicrosoftWindowsDesktop eastus   
rs4-pron                    Windows-10 MicrosoftWindowsDesktop eastus   
rs5-enterprise              Windows-10 MicrosoftWindowsDesktop eastus   
rs5-enterprisen             Windows-10 MicrosoftWindowsDesktop eastus   
rs5-pro                     Windows-10 MicrosoftWindowsDesktop eastus   
rs5-pron                    Windows-10 MicrosoftWindowsDesktop eastus  
```

Kullanılabilir görüntüler hakkında daha fazla bilgi için bkz. [Azure PowerShell Ile Azure MARKETI VM görüntülerini bulma ve kullanma](./cli-ps-findimage.md)

## <a name="uploading-windows-10-vhd-to-azure"></a>Windows 10 VHD 'yi Azure 'a yükleme
Genelleştirilmiş bir Windows 10 VHD 'yi karşıya yüklüyorsanız, lütfen Windows 10 ' un yerleşik yönetici hesabının varsayılan olarak etkin olmadığını unutmayın. Yerleşik yönetici hesabını etkinleştirmek için, Özel Betik uzantısının parçası olarak aşağıdaki komutu ekleyin.

```powershell
Net user <username> /active:yes
```

Aşağıdaki PowerShell kod parçacığı, yerleşik yönetici de dahil olmak üzere tüm yönetici hesaplarını etkin olarak işaretlemenize olanak sağlar. Bu örnek, yerleşik Yönetici Kullanıcı adı bilinmiyorsa yararlı olur.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Daha fazla bilgi için: 
* [VHD 'yi Azure 'a yükleme](upload-generalized-managed.md)
* [Bir Windows VHD 'yi Azure 'a yüklemek üzere hazırlama](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Windows 10 ' un çok kiracılı barındırma haklarıyla dağıtımı
[En son Azure PowerShell yükleyip yapılandırdığınızdan](/powershell/azure/)emin olun. VHD 'nizi hazırladıktan sonra cmdlet 'ini kullanarak Azure depolama hesabınıza VHD 'yi `Add-AzVhd` aşağıdaki gibi yükleyin:

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Azure Resource Manager şablonu dağıtımını kullanarak dağıtma** Kaynak Yöneticisi şablonlarınız içinde, için ek bir parametre `licenseType` belirtilebilir. [Azure Resource Manager şablonları yazma](../../azure-resource-manager/templates/template-syntax.md)hakkında daha fazla bilgi edinebilirsiniz. VHD 'nizi Azure 'a yükledikten sonra, işlem sağlayıcısının bir parçası olarak lisans türünü içerecek şekilde Kaynak Yöneticisi şablonu düzenleyin ve şablonunuzu normal olarak dağıtın:
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**PowerShell aracılığıyla dağıtma** PowerShell aracılığıyla Windows Server VM 'nizi dağıttığınızda, için ek bir parametreye sahip olursunuz `-LicenseType` . VHD 'nizi Azure 'a yükledikten sonra, kullanarak bir VM oluşturun `New-AzVM` ve lisans türünü aşağıdaki gibi belirtin:
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>VM 'nizin lisans avantajını kullanarak emin olun
VM 'nizi PowerShell veya Kaynak Yöneticisi dağıtım yöntemiyle dağıttıktan sonra, lisans türünü `Get-AzVM` aşağıdaki gibi doğrulayın:
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Çıktı, doğru lisans türüyle Windows 10 için aşağıdaki örneğe benzer:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Bu çıktı, Azure hibrit kullanım avantajı lisanslaması olmadan dağıtılan aşağıdaki VM ile karşıttır. Örneğin, Azure galerisinden doğrudan dağıtılan bir VM.

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Azure AD 'ye katılma hakkında ek bilgiler
Azure, yerleşik yönetici hesabı olan tüm Windows sanal makinelerini AAD 'ye katmak için kullanılamaz. Örneğin, *ayarlar > hesap > erişim iş veya okul > + Bağlan* çalışmaz. Azure AD 'yi el ile birleştirmek için ikinci yönetici hesabı olarak oluşturmanız ve oturum açmanız gerekir. Azure AD 'yi bir sağlama paketi kullanarak da yapılandırabilirsiniz, daha fazla bilgi edinmek için *sonraki adımlar* bölümündeki bağlantıyı kullanın.

## <a name="next-steps"></a>Sonraki Adımlar
- [Windows 10 için VDA yapılandırma](/windows/deployment/vda-subscription-activation) hakkında daha fazla bilgi edinin
- [Windows 10 Için çok kiracılı barındırma](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx) hakkında daha fazla bilgi edinin