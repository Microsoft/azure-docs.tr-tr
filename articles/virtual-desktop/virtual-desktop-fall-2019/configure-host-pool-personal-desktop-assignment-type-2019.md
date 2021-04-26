---
title: Windows sanal masaüstü (klasik) kişisel masaüstü atama türü-Azure
description: Windows sanal masaüstü (klasik) kişisel masaüstü konak havuzu için atama türünü yapılandırma.
author: Heidilohr
ms.topic: how-to
ms.date: 05/22/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 5710fc8be66f2126f6bf665ad1808aa11e4db2c4
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445321"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type-for-windows-virtual-desktop-classic"></a>Windows sanal masaüstü için kişisel masaüstü konak havuzu atama türünü yapılandırma (klasik)

>[!IMPORTANT]
>Bu içerik, Windows sanal masaüstü nesneleri Azure Resource Manager desteklemeyen Windows sanal masaüstü (klasik) için geçerlidir. Azure Resource Manager Windows sanal masaüstü nesnelerini yönetmeye çalışıyorsanız, [Bu makaleye](../configure-host-pool-personal-desktop-assignment-type.md)bakın.

Windows sanal masaüstü ortamınızı gereksinimlerinize daha uygun olacak şekilde ayarlamak için kişisel masaüstü ana bilgisayar havuzunuzun atama türünü yapılandırabilirsiniz. Bu konu başlığında, kullanıcılarınız için otomatik veya doğrudan atamayı nasıl yapılandıracağınızı göstereceğiz.

>[!NOTE]
> Havuza alınmış konak havuzlarındaki kullanıcılar belirli oturum konaklarına atanmadığından, bu makaledeki yönergeler yalnızca kişisel masaüstü ana bilgisayar havuzları için geçerlidir.

## <a name="configure-automatic-assignment"></a>Otomatik atamayı yapılandırma

Otomatik atama, Windows sanal masaüstü ortamınızda oluşturulan yeni kişisel masaüstü konak havuzları için varsayılan atama türüdür. Kullanıcıları otomatik olarak atamak, belirli bir oturum ana bilgisayarı gerektirmez.

Kullanıcıları otomatik olarak atamak için, önce bunları kendi akışlarından görebilmesi için kişisel masaüstü ana bilgisayar havuzuna atayın. Atanan bir Kullanıcı, akışını akışlarda başlattığında, bu kullanıcılar, atama işlemini tamamlayan, zaten konak havuzuna bağlanmadıysanız kullanılabilir bir oturum ana bilgisayarı talep eder.

Başlamadan önce, henüz yapmadıysanız [Windows sanal masaüstü PowerShell modülünü indirip içeri aktarın](/powershell/windows-virtual-desktop/overview/) .

> [!NOTE]
> Bu yönergeleri izleyerek önce Windows sanal masaüstü PowerShell modülü sürüm 1.0.1534.2001 veya üstünü yüklediğinizden emin olun.

Bundan sonra hesabınızda oturum açmak için aşağıdaki cmdlet 'i çalıştırın:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Bir konak havuzunu kullanıcıları sanal makinelere otomatik olarak atanacak şekilde yapılandırmak için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

Bir kullanıcıyı kişisel masaüstü konak havuzuna atamak için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>Doğrudan atamayı yapılandırma

Otomatik atamanın aksine, doğrudan atama kullandığınızda, kullanıcıyı kişisel masaüstlerine bağlanabilmek için hem kişisel masaüstü ana bilgisayar havuzuna hem de belirli bir oturum konağına atamanız gerekir. Kullanıcı yalnızca bir oturum ana bilgisayar ataması olmadan bir konak havuzuna atanırsa, kaynaklara erişemez.

Bir konak havuzunu kullanıcıların oturum ana bilgisayarlarına doğrudan atanmasını gerektirecek şekilde yapılandırmak için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

Bir kullanıcıyı kişisel masaüstü konak havuzuna atamak için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Bir kullanıcıyı belirli bir oturum konağına atamak için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="remove-a-user-assignment"></a>Kullanıcı atamasını kaldırma

Kullanıcının artık kişisel masaüstüne ihtiyacı olmadığından, Kullanıcı şirketten ayrıldığından veya başka biri için Masaüstünü yeniden kullanmak istediğinizde, Kullanıcı atamasını kaldırmak isteyebilirsiniz.

Şu anda, kişisel bir masaüstü için Kullanıcı atamasını kaldırmanın tek yolu, oturum konağını tamamen kaldırmamaktadır. Oturum konağını kaldırmak için şu cmdlet 'i çalıştırın:

```powershell
Remove-RdsSessionHost
```

Oturum konağını kişisel masaüstü ana bilgisayar havuzuna eklemeniz gerekiyorsa, bu makinede Windows sanal masaüstünü kaldırın ve ardından oturum konağını yeniden kaydetmek için [PowerShell ile bir konak havuzu oluşturma](create-host-pools-powershell-2019.md) ' daki adımları izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Kişisel masaüstü atama türünü yapılandırdığınıza göre, bir Windows sanal masaüstü istemcisinde oturum açarak Kullanıcı oturumunun bir parçası olarak test edebilirsiniz. Bu sonraki iki nasıl-TOS, tercih ettiğiniz istemciyi kullanarak bir oturuma nasıl bağlanacağınızı bildirir:

- [Windows Masaüstü istemcisine bağlanma](connect-windows-7-10-2019.md)
- [Web istemcisiyle bağlanma](connect-web-2019.md)
