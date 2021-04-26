---
title: Windows sanal masaüstü kişisel masaüstü atama türü-Azure
description: Windows sanal masaüstü kişisel masaüstü konak havuzu için otomatik veya doğrudan atamayı yapılandırma.
author: Heidilohr
ms.topic: how-to
ms.date: 07/09/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 11fbbe4d816c9f50afc879d74991976a467a9013
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448143"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>Kişisel masaüstü konak havuzu atama türünü yapılandırma

>[!IMPORTANT]
>Bu içerik Azure Resource Manager Windows sanal masaüstü nesneleri ile Windows sanal masaüstü için geçerlidir. Azure Resource Manager nesneleri olmadan Windows sanal masaüstü (klasik) kullanıyorsanız, [Bu makaleye](./virtual-desktop-fall-2019/configure-host-pool-personal-desktop-assignment-type-2019.md)bakın.

Windows sanal masaüstü ortamınızı gereksinimlerinize daha uygun olacak şekilde ayarlamak için kişisel masaüstü ana bilgisayar havuzunuzun atama türünü yapılandırabilirsiniz. Bu konu başlığında, kullanıcılarınız için otomatik veya doğrudan atamayı nasıl yapılandıracağınızı göstereceğiz.

>[!NOTE]
> Havuza alınmış konak havuzlarındaki kullanıcılar belirli oturum konaklarına atanmadığından, bu makaledeki yönergeler yalnızca kişisel masaüstü ana bilgisayar havuzları için geçerlidir.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede, Windows sanal masaüstü PowerShell modülünü zaten indirdiğiniz ve yüklediğiniz varsayılmaktadır. Yapmadıysanız, [PowerShell modülünü ayarlama](powershell-module.md)bölümündeki yönergeleri izleyin.

## <a name="configure-automatic-assignment"></a>Otomatik atamayı yapılandırma

Otomatik atama, Windows sanal masaüstü ortamınızda oluşturulan yeni kişisel masaüstü konak havuzları için varsayılan atama türüdür. Kullanıcıları otomatik olarak atamak, belirli bir oturum ana bilgisayarı gerektirmez.

Kullanıcıları otomatik olarak atamak için, önce bunları kendi akışlarından görebilmesi için kişisel masaüstü ana bilgisayar havuzuna atayın. Atanan bir Kullanıcı, akışını akışlarda başlattığında, bu kullanıcılar, atama işlemini tamamlayan, zaten konak havuzuna bağlanmadıysanız kullanılabilir bir oturum ana bilgisayarı talep eder.

Bir konak havuzunu kullanıcıları sanal makinelere otomatik olarak atanacak şekilde yapılandırmak için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Automatic
```

Bir kullanıcıyı kişisel masaüstü konak havuzuna atamak için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="configure-direct-assignment"></a>Doğrudan atamayı yapılandırma

Otomatik atamanın aksine, doğrudan atama kullandığınızda, kullanıcıyı kişisel masaüstlerine bağlanabilmek için hem kişisel masaüstü ana bilgisayar havuzuna hem de belirli bir oturum konağına atamanız gerekir. Kullanıcı yalnızca bir oturum ana bilgisayar ataması olmadan bir konak havuzuna atanırsa, kaynaklara erişemez.

Bir konak havuzunu kullanıcıların oturum ana bilgisayarlarına doğrudan atanmasını gerektirecek şekilde yapılandırmak için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Direct
```

Bir kullanıcıyı kişisel masaüstü konak havuzuna atamak için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Bir kullanıcıyı belirli bir oturum konağına atamak için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
Update-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -AssignedUser <userupn>
```

Bir kullanıcıyı Azure portal bir oturum konağına doğrudan atamak için:

1. <https://portal.azure.com> adresinden Azure portalında oturum açın.
2. Arama çubuğuna **Windows sanal masaüstü 'nü** girin.
3. **Hizmetler** altında **Windows sanal masaüstü**' nü seçin.
4. Windows sanal masaüstü sayfasında, pencerenin sol tarafındaki menüye gidin ve **konak havuzları**' nı seçin.
5. Güncelleştirmek istediğiniz konak havuzunun adını seçin.
6. Sonra, pencerenin sol tarafındaki menüye gidin ve **uygulama grupları**' nı seçin.
7. Düzenlemek istediğiniz masaüstü uygulama grubunun adını seçin ve ardından pencerenin sol tarafındaki menüden **atamalar** ' ı seçin.
8. **+ Ekle**' yi seçin ve ardından bu masaüstü uygulama grubunu yayımlamak istediğiniz kullanıcıları veya Kullanıcı gruplarını seçin.
9. Bir kullanıcıya oturum ana bilgisayarı atamak için bilgi çubuğunda **VM ata** ' yı seçin.
10. Kullanıcıya atamak istediğiniz oturum konağını seçin ve ardından **ata**' yı seçin.
11. Kullanılabilir kullanıcılar listesinden oturum konağını atamak istediğiniz kullanıcıyı seçin.
12. İşiniz bittiğinde **Seç**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Kişisel masaüstü atama türünü yapılandırdığınıza göre, bir Windows sanal masaüstü istemcisinde oturum açarak Kullanıcı oturumunun bir parçası olarak test edebilirsiniz. Bu sonraki iki nasıl-TOS, tercih ettiğiniz istemciyi kullanarak bir oturuma nasıl bağlanacağınızı bildirir:

- [Windows Masaüstü istemcisine bağlanma](connect-windows-7-10.md)
- [Web istemcisiyle bağlanma](connect-web.md)
- [Android istemcisiyle bağlanma](connect-android.md)
- [iOS istemcisiyle bağlanma](connect-ios.md)
- [macOS istemcisiyle bağlanma](connect-macos.md)