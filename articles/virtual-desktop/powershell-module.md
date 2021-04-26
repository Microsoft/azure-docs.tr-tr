---
title: PowerShell modülü Windows sanal masaüstü-Azure
description: Windows sanal masaüstü için PowerShell modülünü yüklemek ve ayarlamak.
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: fdd74a09e49a352d18cf4ceccf406a25bf6dffb3
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445848"
---
# <a name="set-up-the-powershell-module-for-windows-virtual-desktop"></a>Windows sanal masaüstü için PowerShell modülünü ayarlama

>[!IMPORTANT]
>Bu içerik, Azure Resource Manager tümleştirme ile Windows sanal masaüstü için geçerlidir.

Windows sanal masaüstü PowerShell modülü Azure PowerShell modülüyle tümleşiktir. Bu makalede, Windows sanal masaüstü için cmdlet 'leri çalıştırabilmeniz amacıyla PowerShell modülünün nasıl ayarlanacağı açıklanır.

## <a name="set-up-your-powershell-environment"></a>PowerShell ortamınızı hazırlama

Modülünü kullanmaya başlamak için önce [PowerShell Core 'un en son sürümünü](/powershell/scripting/install/installing-powershell#powershell-core)yüklemeniz gerekir. Windows sanal masaüstü cmdlet 'leri Şu anda yalnızca PowerShell Core ile çalışır.

Daha sonra, PowerShell oturumunuzda kullanılacak DesktopVirtualization modülünü yüklemeniz gerekir.

Modülünü yüklemek için aşağıdaki PowerShell cmdlet 'ini Yükseltilmiş modda çalıştırın:

```powershell
Install-Module -Name Az.DesktopVirtualization
```

>[!NOTE]
> Bu cmdlet işe yaramazsa, yükseltilmiş izinlerle yeniden çalıştırmayı deneyin.

Ardından, Azure 'a bağlanmak için aşağıdaki cmdlet 'i çalıştırın:

```powershell
Connect-AzAccount
```

>[!IMPORTANT]
>US Gov portalına bağlanıyorsanız, bunun yerine bu cmdlet 'i çalıştırın:
> 
> ```powershell
> Connect-AzAccount -EnvironmentName AzureUSGovernment
> ```

Azure hesabınızda oturum açmak, Connect cmdlet 'ini çalıştırdığınızda oluşturulmuş bir kod gerektirir. Oturum açmak için adresine gidin <https://microsoft.com/devicelogin> , kodu girin ve ardından Azure yönetici kimlik bilgilerinizi kullanarak oturum açın.

```powershell
Account SubscriptionName TenantId Environment

------- ---------------- -------- -----------

Youradminupn subscriptionname AzureADTenantID AzureCloud
```

Bu, sizi yönetici kimlik bilgileriniz için varsayılan olan abonelikte doğrudan imzalayacaktır.

## <a name="change-the-default-subscription"></a>Varsayılan aboneliği değiştirme

Oturum açtıktan sonra varsayılan aboneliği değiştirmek istiyorsanız şu cmdlet 'i çalıştırın:

```powershell
Select-AzSubscription -Subscription <preferredsubscriptionname>
```

Ayrıca, Out-GridView cmdlet 'ini kullanarak bir listeden bir seçim yapabilirsiniz:

```powershell
Get-AzSubscription | Out-GridView -PassThru | Select-AzSubscription
```

Kullanmak üzere yeni bir abonelik seçtiğinizde, bu aboneliğin KIMLIĞINI daha sonra çalıştırdığınız cmdlet 'lerde belirtmeniz gerekmez. Örneğin, aşağıdaki cmdlet abonelik KIMLIĞINE gerek duymadan belirli bir oturum konağını alır:

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname>
```

Ayrıca, istenen abonelik adını bir parametre olarak ekleyerek, her cmdlet için de abonelikleri değiştirebilirsiniz. Sonraki cmdlet, bir önceki örnekle aynıdır, örneğin abonelik KIMLIĞI, cmdlet 'in kullandığı aboneliğin değiştirileceği bir parametre olarak eklenir.

```powershell
Get-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -SubscriptionId <subscriptionGUID>
```

## <a name="get-locations"></a>Konumları al

Location parametresi, yeni nesneler oluşturan tüm **New-AzWVD** cmdlet 'leri için zorunludur.

Aboneliğinizin desteklediği konumların bir listesini almak için aşağıdaki cmdlet 'i çalıştırın:

```powershell
Get-AzLocation
```

**Get-AzLocation** çıktısı şuna benzeyecektir:

```powershell
Location : eastasia

DisplayName : East Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : southeastasia

DisplayName : Southeast Asia

Providers : {Microsoft.RecoveryServices, Microsoft.ManagedIdentity,
Microsoft.SqlVirtualMachine, microsoft.insightsΓÇª}

Location : centralus

DisplayName : Central US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}

Location : eastus

DisplayName : East US

Providers : {Microsoft.RecoveryServices, Microsoft.DesktopVirtualization,
Microsoft.ManagedIdentity, Microsoft.SqlVirtualMachineΓÇª}
```

Hesabınızın konumunu öğrendikten sonra bir cmdlet 'te kullanabilirsiniz. Örneğin, "Güneydoğu" konumunda bir konak havuzu oluşturan bir cmdlet aşağıda verilmiştir:

```powershell
New-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -WorkspaceName <workspacename> -Location “southeastasia”
```

## <a name="next-steps"></a>Sonraki adımlar

PowerShell modülünüzü ayarladığınıza göre, Windows sanal masaüstündeki tüm işlemleri yapmak için cmdlet 'leri çalıştırabilirsiniz. Modülünüzü kullanabileceğiniz yerlerden bazıları şunlardır:

- [Windows sanal masaüstü öğreticilerimizden]() çalıştırarak, kendi Windows sanal masaüstü ortamınızı ayarlayın.
- [PowerShell ile ana bilgisayar havuzu oluşturma](create-host-pools-powershell.md)
- [Windows Sanal Masaüstü yük dengeleme yöntemini yapılandırma](configure-host-pool-load-balancing.md)
- [Kişisel masaüstü konak havuzu atama türünü yapılandırma](configure-host-pool-personal-desktop-assignment-type.md)
- Dahası da var!

Herhangi bir sorunla karşılaşırsanız yardım için [PowerShell sorun giderme makalemizi](troubleshoot-powershell.md) inceleyin.

