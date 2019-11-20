---
title: Windows lisansını oturum ana bilgisayarına uygulama sanal makineleri-Azure
description: Windows sanal masaüstü VM 'Leri için Windows lisansının nasıl uygulanacağını açıklar.
services: virtual-desktop
author: ChristianMontoya
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: chrimo
ms.openlocfilehash: af8542ccc8fad8d833d8329999ded2f5a52b3d03
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69564205"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>Windows lisansını oturum ana bilgisayarı sanal makinelerine Uygula

Windows sanal masaüstü iş yüklerini çalıştırmaya uygun şekilde lisanslanan müşteriler, oturum ana bilgisayarları sanal makinelerine bir Windows lisansı uygulamaya ve bunları başka bir lisans için ödeme yapmadan çalıştırmaya uygundur. Daha fazla bilgi için bkz. [Windows sanal masaüstü fiyatlandırması](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>Windows sanal masaüstü lisansınızı kullanmanın yolları
Windows sanal masaüstü lisansı, bir konak havuzunda oturum ana bilgisayarı olarak kaydedilmiş ve Kullanıcı bağlantılarını alan herhangi bir Windows veya Windows Server sanal makinesine lisans uygulamanıza olanak tanır. Bu lisans, dosya paylaşma sunucuları, etki alanı denetleyicileri vb. çalıştıran sanal makinelere uygulanmaz.

Windows sanal masaüstü lisansını kullanmanın birkaç yolu vardır:
- [Azure Marketi](./create-host-pools-azure-marketplace.md)teklifini kullanarak bir konak havuzu ve onun oturum ana bilgisayarı sanal makinelerini oluşturabilirsiniz. Bu şekilde oluşturulan sanal makinelere otomatik olarak lisans uygulanır.
- [GitHub Azure Resource Manager şablonunu](./create-host-pools-arm-template.md)kullanarak bir konak havuzu ve onun oturum ana bilgisayarı sanal makinelerini oluşturabilirsiniz. Bu şekilde oluşturulan sanal makinelere otomatik olarak lisans uygulanır.
- Mevcut bir oturum ana bilgisayar sanal makinesine lisans uygulayabilirsiniz. Bunu yapmak için önce [PowerShell ile konak havuzu oluşturma](./create-host-pools-powershell.md) ' daki yönergeleri izleyerek bir konak havuzu ve Ilişkili VM 'ler oluşturun ve ardından bu makaleye geri dönüp lisansın nasıl uygulanacağını öğrenin.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>Bir oturum ana bilgisayar VM 'sine bir Windows lisansı uygulama
[En son Azure PowerShell yükleyip yapılandırdığınızdan](/powershell/azure/overview)emin olun. Windows lisansını uygulamak için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>Oturum Ana bilgisayar VM 'nizin lisans avantajını kullandığınızı doğrulayın
VM 'nizi dağıttıktan sonra, lisans türünü doğrulamak için şu cmdlet 'i çalıştırın:
```powershell
Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
```

Uygulanan Windows lisansına sahip bir oturum ana makinesi, şöyle bir şey gösterir:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Uygulanan Windows lisansı olmayan VM 'Ler şuna benzer bir şey gösterir:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

Azure aboneliğinizde Windows lisansı uygulanmış tüm oturum ana bilgisayar VM 'lerinin listesini görmek için aşağıdaki cmdlet 'i çalıştırın:

```powershell
$vms = Get-AzVM
$vms | ?{$_.LicenseType -like "Windows_Client"} | select ResourceGroupName, Name, LicenseType
```