---
title: Windows sanal masaüstü (klasik) PowerShell-Azure
description: Windows sanal masaüstü (klasik) kiracı ortamı ayarlarken PowerShell ile ilgili sorunları giderme.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: e7db817e2fb355e3a69f7b90a4fc6eaa4adec515
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444233"
---
# <a name="windows-virtual-desktop-classic-powershell"></a>Windows sanal masaüstü (klasik) PowerShell

>[!IMPORTANT]
>Bu içerik, Windows sanal masaüstü nesneleri Azure Resource Manager desteklemeyen Windows sanal masaüstü (klasik) için geçerlidir. Azure Resource Manager Windows sanal masaüstü nesnelerini yönetmeye çalışıyorsanız, [Bu makaleye](../troubleshoot-powershell.md)bakın.

PowerShell 'i Windows sanal masaüstü ile kullanırken oluşan hataları ve sorunları gidermek için bu makaleyi kullanın. PowerShell Uzak Masaüstü Hizmetleri hakkında daha fazla bilgi için bkz. [Windows sanal masaüstü PowerShell](/powershell/windows-virtual-desktop/overview).

## <a name="provide-feedback"></a>Geribildirim gönderme

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

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>Hata: Get-RdsDiagnosticActivities--Kullanıcı, Yönetim hizmetini sorgulama yetkisine sahip değil

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**Neden:** -TenantName parametresi

**Çözüm:** Sorun Get-RdsDiagnosticActivities-TenantName \<TenantName> .

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>Hata: Get-RdsDiagnosticActivities--Kullanıcı, Yönetim hizmetini sorgulama yetkisine sahip değil

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**Neden:** Dağıtım anahtarı kullanılıyor.

**Çözüm:** -dağıtım anahtarı yalnızca dağıtım yöneticileri tarafından kullanılabilir. Bu yöneticiler genellikle Uzak Masaüstü Hizmetleri/Windows sanal masaüstü ekibinin üyeleridir. -Deployment anahtarını-TenantName ile değiştirin \<TenantName> .

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>Hata: New-RdsRoleAssignment--Kullanıcı, Yönetim hizmetini sorgulama yetkisine sahip değil

**Neden 1:** Kullanılan hesabın kiracı üzerinde Uzak Masaüstü Hizmetleri sahip izinleri yok.

**1. Çözüm:** Uzak Masaüstü Hizmetleri sahip izinlerine sahip bir kullanıcının rol atamasını yürütmesi gerekir.

**Neden 2:** Kullanılan hesap Uzak Masaüstü Hizmetleri sahip izinlerine sahip ancak kiracının Azure Active Directory bir parçası değil veya kullanıcının bulunduğu Azure Active Directory sorgulamak için izinlere sahip değil.

**2. Çözüm:** Active Directory izinlere sahip bir kullanıcının rol atamasını yürütmesi gerekir.

>[!Note]
>New-RdsRoleAssignment, Azure Active Directory (AD) içinde mevcut olmayan bir kullanıcıya izin veremez.

## <a name="next-steps"></a>Sonraki adımlar

- Windows sanal masaüstü ve yükseltme izlemelerinin sorunlarını giderme hakkında genel bilgi için bkz. [sorun giderme genel bakış, geri bildirim ve destek](troubleshoot-set-up-overview-2019.md).
- Bir Windows sanal masaüstü ortamında kiracı ve konak havuzu oluştururken oluşan sorunları gidermek için bkz. [kiracı ve konak havuzu oluşturma](troubleshoot-set-up-issues-2019.md).
- Windows sanal masaüstündeki bir sanal makineyi (VM) yapılandırırken oluşan sorunları gidermek için bkz. [oturum ana bilgisayarı sanal makine yapılandırması](troubleshoot-vm-configuration-2019.md).
- Windows sanal masaüstü istemci bağlantılarıyla ilgili sorunları gidermek için bkz. [Windows sanal masaüstü hizmeti bağlantıları](troubleshoot-service-connection-2019.md).
- Uzak Masaüstü istemcileriyle ilgili sorunları gidermek için bkz [. uzak masaüstü Istemcisinde sorun giderme](../troubleshoot-client.md)
- Hizmet hakkında daha fazla bilgi edinmek için bkz. [Windows sanal masaüstü ortamı](environment-setup-2019.md).
- Sorun giderme öğreticisini öğrenmek için bkz. [öğretici: Kaynak Yöneticisi şablonu dağıtımlarının sorunlarını giderme](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Denetim eylemleri hakkında bilgi edinmek için bkz. [Kaynak Yöneticisi Ile denetim işlemleri](../../azure-resource-manager/management/view-activity-logs.md).
- Dağıtım sırasında hataları belirleme eylemleri hakkında bilgi edinmek için bkz. [dağıtım Işlemlerini görüntüleme](../../azure-resource-manager/templates/deployment-history.md).
