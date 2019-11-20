---
title: Windows sanal masaüstü PowerShell-Azure
description: Windows sanal masaüstü kiracı ortamı ayarlarken PowerShell ile ilgili sorunları giderme.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: 38d9a2dda945f3a9459aa8e3360012c6ef422608
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163307"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows Sanal Masaüstü PowerShell

PowerShell 'i Windows sanal masaüstü ile kullanırken oluşan hataları ve sorunları gidermek için bu makaleyi kullanın. PowerShell Uzak Masaüstü Hizmetleri hakkında daha fazla bilgi için bkz. [Windows sanal masaüstü PowerShell](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/).

## <a name="provide-feedback"></a>Geri bildirim sağlayın

Windows Sanal Masaüstü hizmetini ürün ekibi ve etkin topluluk üyeleriyle tartışmak için [Windows sanal masaüstü teknoloji Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) 'yi ziyaret edin.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Windows sanal masaüstü kurulumu sırasında kullanılan PowerShell komutları

Bu bölümde, Windows sanal masaüstü ayarlanırken genellikle kullanılan PowerShell komutları listelenir ve bunları kullanırken oluşabilecek sorunları çözmeye yönelik yollar sağlanır.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>Hata: Add-RdsAppGroupUser komutu--belirtilen UserPrincipalName zaten belirtilen konak havuzundaki bir RemoteApp uygulama grubuna atandı

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**Neden:** Kullanılan Kullanıcı adı, farklı türdeki bir uygulama grubuna zaten atandı. Kullanıcılar aynı oturum ana bilgisayar havuzunda hem uzak masaüstü hem de uzak uygulama grubuna atanamaz.

**Çözüm:** Kullanıcı hem uzak uygulamalara hem de uzak masaüstüne ihtiyaç duyuyorsa, farklı konak havuzları oluşturun veya Uzak masaüstüne Kullanıcı erişimi verin; bu, oturum ana bilgisayar VM üzerinde herhangi bir uygulamanın kullanılmasına izin verir.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>Hata: Add-RdsAppGroupUser komutu--belirtilen UserPrincipalName, uzak masaüstü kiracısı ile ilişkili Azure Active Directory yok

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**Neden:** -UserPrincipalName tarafından belirtilen kullanıcı, Windows sanal masaüstü kiracısına bağlı Azure Active Directory bulunamıyor.

**Çözüm:** Aşağıdaki listedeki öğeleri onaylayın.

- Kullanıcı Azure Active Directory eşitleniyor.
- Kullanıcı, işletmeden müşteriye (B2C) veya işletmeden işletmeye (B2B) ticareti 'ne bağlı değildir.
- Windows sanal masaüstü kiracısı, doğru Azure Active Directory bağlıdır.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Hata: Get-RdsDiagnosticActivities--User, Yönetim hizmetini sorgulama yetkisine sahip değil

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Neden:** -TenantName parametresi

**Çözüm:** \<TenantName > ile Get-RdsDiagnosticActivities ile-TenantName adlı bir sorun.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Hata: Get-RdsDiagnosticActivities--Kullanıcı, Yönetim hizmetini sorgulama yetkisine sahip değil

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Neden:** Dağıtım anahtarı kullanılıyor.

**Çözüm:** -dağıtım anahtarı yalnızca dağıtım yöneticileri tarafından kullanılabilir. Bu yöneticiler genellikle Uzak Masaüstü Hizmetleri/Windows sanal masaüstü ekibinin üyeleridir. -Deployment anahtarını-TenantName \<, TenantName > ile değiştirin.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Hata: New-RdsRoleAssignment--Kullanıcı Yönetim hizmetini sorgulama yetkisine sahip değil

**Neden 1:** Kullanılan hesabın kiracı üzerinde Uzak Masaüstü Hizmetleri sahip izinleri yok.

**1. Çözüm:** Uzak Masaüstü Hizmetleri sahip izinlerine sahip bir kullanıcının rol atamasını yürütmesi gerekir.

**Neden 2:** Kullanılan hesap Uzak Masaüstü Hizmetleri sahip izinlerine sahip ancak kiracının Azure Active Directory bir parçası değil veya kullanıcının bulunduğu Azure Active Directory sorgulamak için izinlere sahip değil.

**2. Çözüm:** Active Directory izinlere sahip bir kullanıcının rol atamasını yürütmesi gerekir.

>[!Note]
>New-RdsRoleAssignment, Azure Active Directory (AD) içinde olmayan bir kullanıcıya izin verebilir.

## <a name="next-steps"></a>Sonraki adımlar

- Windows sanal masaüstü ve yükseltme izlemelerinin sorunlarını giderme hakkında genel bilgi için bkz. [sorun giderme genel bakış, geri bildirim ve destek](troubleshoot-set-up-overview.md).
- Bir Windows sanal masaüstü ortamında kiracı ve konak havuzu oluştururken oluşan sorunları gidermek için bkz. [kiracı ve konak havuzu oluşturma](troubleshoot-set-up-issues.md).
- Windows sanal masaüstündeki bir sanal makineyi (VM) yapılandırırken oluşan sorunları gidermek için bkz. [oturum ana bilgisayarı sanal makine yapılandırması](troubleshoot-vm-configuration.md).
- Windows sanal masaüstü istemci bağlantılarıyla ilgili sorunları gidermek için bkz. [Uzak Masaüstü istemci bağlantıları](troubleshoot-client-connection.md).
- Hizmet hakkında daha fazla bilgi edinmek için bkz. [Windows sanal masaüstü ortamı](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Sorun giderme öğreticisini öğrenmek için bkz. [öğretici: Kaynak Yöneticisi şablonu dağıtımlarının sorunlarını giderme](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Denetim eylemleri hakkında bilgi edinmek için bkz. [Kaynak Yöneticisi Ile denetim işlemleri](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Dağıtım sırasında hataları belirleme eylemleri hakkında bilgi edinmek için bkz. [dağıtım Işlemlerini görüntüleme](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
