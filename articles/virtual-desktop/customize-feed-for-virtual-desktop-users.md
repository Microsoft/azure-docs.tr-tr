---
title: Windows sanal masaüstü kullanıcıları için akışı özelleştirme-Azure
description: PowerShell cmdlet 'leri ile Windows sanal masaüstü kullanıcıları için akışı özelleştirme.
author: Heidilohr
ms.topic: how-to
ms.date: 09/14/2019
ms.author: helohr
manager: femila
ms.openlocfilehash: a2d5a870ee8953a4388e58d21cf941f23434382b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447123"
---
# <a name="customize-the-feed-for-windows-virtual-desktop-users"></a>Windows sanal masaüstü kullanıcıları için akışı özelleştirme

>[!IMPORTANT]
>Bu içerik Azure Resource Manager Windows sanal masaüstü nesneleri ile Windows sanal masaüstü için geçerlidir. Azure Resource Manager nesneleri olmadan Windows sanal masaüstü (klasik) kullanıyorsanız, [Bu makaleye](./virtual-desktop-fall-2019/customize-feed-virtual-desktop-users-2019.md)bakın.

RemoteApp ve uzak masaüstü kaynaklarının kullanıcılarınız için tanınabilir bir şekilde görünmesi için akışı özelleştirebilirsiniz.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede, Windows sanal masaüstü PowerShell modülünü zaten indirdiğiniz ve yüklediğiniz varsayılmaktadır. Yapmadıysanız, [PowerShell modülünü ayarlama](powershell-module.md)bölümündeki yönergeleri izleyin.

## <a name="customize-the-display-name-for-a-remoteapp"></a>RemoteApp için görünen adı özelleştirme

Kolay adı ayarlayarak, yayımlanan bir RemoteApp için görünen adı değiştirebilirsiniz. Varsayılan olarak, kolay ad RemoteApp programının adı ile aynıdır.

Bir uygulama grubu için yayımlanmış RemoteApps listesini almak için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
Get-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname>
```

Bir RemoteApp 'e kolay bir ad atamak için, gerekli parametrelerle birlikte aşağıdaki cmdlet 'i çalıştırın:

```powershell
Update-AzWvdApplication -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

Örneğin, aşağıdaki örnek cmdlet 'i kullanarak geçerli uygulamaları elde edelim.

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list
```

Çıktı şöyle görünür:

```powershell
CommandLineArgument :
CommandLineSetting  : DoNotAllow
Description         :
FilePath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
FriendlyName        : Microsoft Word
IconContent         : {0, 0, 1, 0…}
IconHash            : --iom0PS6XLu-EMMlHWVW3F7LLsNt63Zz2K10RE0_64
IconIndex           : 0
IconPath            : C:\Program Files\Windows NT\Accessories\wordpad.exe
Id                  : /subscriptions/<subid>/resourcegroups/0301RG/providers/Microsoft.DesktopVirtualization/applicationgroups/0301RAG/applications/Microsoft Word
Name                : 0301RAG/Microsoft Word
ShowInPortal        : False
Type                : Microsoft.DesktopVirtualization/applicationgroups/applications
```
Kolay adı güncelleştirmek için şu cmdlet 'i çalıştırın:

```powershell
Update-AzWvdApplication -GroupName 0301RAG -Name "Microsoft Word" -FriendlyName "WordUpdate" -ResourceGroupName 0301RG -IconIndex 0 -IconPath "C:\Program Files\Windows NT\Accessories\wordpad.exe" -ShowInPortal:$true -CommandLineSetting DoNotallow -FilePath "C:\Program Files\Windows NT\Accessories\wordpad.exe"
```

Kolay adı başarıyla güncelleştirdiklerini doğrulamak için şu cmdlet 'i çalıştırın:

```powershell
Get-AzWvdApplication -ResourceGroupName 0301RG -ApplicationGroupName 0301RAG | format-list FriendlyName
```

Cmdlet 'i size şu çıktıyı vermelidir:

```powershell
FriendlyName        : WordUpdate
```

## <a name="customize-the-display-name-for-a-remote-desktop"></a>Uzak Masaüstü için görünen adı özelleştirme

Bir kolay ad ayarlayarak yayımlanmış bir uzak masaüstü için görünen adı değiştirebilirsiniz. PowerShell aracılığıyla el ile bir konak havuzu ve Masaüstü uygulama grubu oluşturduysanız, varsayılan kolay ad "oturum Masaüstü" dir. GitHub Azure Resource Manager şablonu veya Azure Marketi teklifi aracılığıyla bir konak havuzu ve Masaüstü uygulama grubu oluşturduysanız, varsayılan kolay ad, ana bilgisayar havuzu adıyla aynıdır.

Uzak Masaüstü kaynağını almak için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
Get-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname>
```

Uzak Masaüstü kaynağına kolay bir ad atamak için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

```powershell
Update-AzWvdDesktop -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -Name <applicationname> -FriendlyName <newfriendlyname>
```

## <a name="customize-a-display-name-in-azure-portal"></a>Azure portal bir görünen adı özelleştirme

Azure portal kullanarak, bir kolay ad ayarlayarak yayımlanmış bir uzak masaüstü için görünen adı değiştirebilirsiniz.

1. <https://portal.azure.com> adresinden Azure portalında oturum açın.

2. **Windows sanal masaüstü** araması yapın.

3. Hizmetler altında **Windows sanal masaüstü**' nü seçin.

4. Windows sanal masaüstü sayfasında, ekranın sol tarafındaki **uygulama grupları** ' nı seçin ve ardından düzenlemek istediğiniz uygulama grubunun adını seçin. (Örneğin, Masaüstü uygulama grubunun görünen adını düzenlemek istiyorsanız, **Masaüstü** adlı uygulama grubunu seçin.)

5. Ekranın sol tarafındaki menüden **uygulamalar** ' ı seçin.

6. Güncelleştirmek istediğiniz uygulamayı seçin ve ardından yeni bir **görünen ad** girin.

7. **Kaydet**’i seçin. Düzenlediğiniz uygulama artık güncelleştirilmiş adı görüntülemelidir.

## <a name="next-steps"></a>Sonraki adımlar

Artık kullanıcıların akışını özelleştirdiğinize göre, test etmek için bir Windows sanal masaüstü istemcisinde oturum açabilirsiniz. Bunu yapmak için, Windows sanal masaüstü nasıl yapılır-TOS ' a bağlanma konusuna geçin:

 * [Windows 10 veya Windows 7 ile bağlanma](connect-windows-7-10.md)
 * [Web istemcisiyle bağlanma](connect-web.md)
 * [Android istemcisiyle bağlanma](connect-android.md)
 * [iOS istemcisiyle bağlanma](connect-ios.md)
 * [macOS istemcisiyle bağlanma](connect-macos.md)
