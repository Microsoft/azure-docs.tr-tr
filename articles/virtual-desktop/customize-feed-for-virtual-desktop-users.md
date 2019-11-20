---
title: Windows sanal masaüstü kullanıcıları için akışı özelleştirme-Azure
description: PowerShell cmdlet 'leri ile Windows sanal masaüstü kullanıcıları için akışı özelleştirme.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 49b678bea820f3cbead7479bb414ca9f35a29fa4
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163629"
---
# <a name="customize-feed-for-windows-virtual-desktop-users"></a>Windows Sanal Masaüstü kullanıcıları için akışı özelleştirme

RemoteApp ve uzak masaüstü kaynaklarının kullanıcılarınız için tanınabilir bir şekilde görünmesi için akışı özelleştirebilirsiniz.

İlk olarak, henüz yapmadıysanız PowerShell oturumunuzda kullanmak üzere [Windows sanal masaüstü PowerShell modülünü indirip içeri aktarın](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) . Bundan sonra hesabınızda oturum açmak için aşağıdaki cmdlet 'i çalıştırın:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="customize-the-display-name-for-a-remoteapp"></a>RemoteApp için görünen adı özelleştirme

Kolay adı ayarlayarak, yayımlanan bir RemoteApp için görünen adı değiştirebilirsiniz. Varsayılan olarak, kolay ad RemoteApp programının adı ile aynıdır.

Bir uygulama grubu için yayımlanmış RemoteApps listesini almak için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
Get-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Name ve FriendlyName ile birlikte Get-RDSRemoteApp PowerShell cmdlet 'inin ekran görüntüsü.](media/get-rdsremoteapp.png)

Bir RemoteApp 'e kolay bir ad atamak için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
Set-RdsRemoteApp -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -Name <existingappname> -FriendlyName <newfriendlyname>
```
![Set-RDSRemoteApp with Name ve New FriendlyName vurgulanmış PowerShell cmdlet 'inin ekran görüntüsü.](media/set-rdsremoteapp.png)

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Uzak Masaüstü için görünen adı özelleştirme

Bir kolay ad ayarlayarak yayımlanmış bir uzak masaüstü için görünen adı değiştirebilirsiniz. PowerShell aracılığıyla el ile bir konak havuzu ve Masaüstü uygulama grubu oluşturduysanız, varsayılan kolay ad "oturum Masaüstü" dir. GitHub Azure Resource Manager şablonu veya Azure Marketi teklifi aracılığıyla bir konak havuzu ve Masaüstü uygulama grubu oluşturduysanız, varsayılan kolay ad, ana bilgisayar havuzu adıyla aynıdır.

Uzak Masaüstü kaynağını almak için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
Get-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname>
```
![Name ve FriendlyName ile birlikte Get-RDSRemoteApp PowerShell cmdlet 'inin ekran görüntüsü.](media/get-rdsremotedesktop.png)

Uzak Masaüstü kaynağına kolay bir ad atamak için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
Set-RdsRemoteDesktop -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName <appgroupname> -FriendlyName <newfriendlyname>
```
![Set-RDSRemoteApp with Name ve New FriendlyName vurgulanmış PowerShell cmdlet 'inin ekran görüntüsü.](media/set-rdsremotedesktop.png)

## <a name="next-steps"></a>Sonraki adımlar

Artık kullanıcıların akışını özelleştirdiğinize göre, test etmek için bir Windows sanal masaüstü istemcisinde oturum açabilirsiniz. Bunu yapmak için, Windows sanal masaüstü nasıl yapılır-TOS ' a bağlanma konusuna geçin:
    
 * [Windows 10 veya Windows 7 ' den bağlan](connect-windows-7-and-10.md)
 * [Web tarayıcısından Bağlan](connect-web.md) 
