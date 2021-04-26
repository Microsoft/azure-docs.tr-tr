---
title: Windows sanal masaüstü PowerShell için uygulama gruplarını yönetme-Azure
description: PowerShell ile Windows sanal masaüstü uygulama gruplarını yönetme.
author: Heidilohr
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 1652048da8084007bc86a6793f14f10b4a1f2e0f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446052"
---
# <a name="manage-app-groups-using-powershell"></a>PowerShell kullanarak uygulama gruplarını yönetme

>[!IMPORTANT]
>Bu içerik Azure Resource Manager Windows sanal masaüstü nesneleri ile Windows sanal masaüstü için geçerlidir. Azure Resource Manager nesneleri olmadan Windows sanal masaüstü (klasik) kullanıyorsanız, [Bu makaleye](./virtual-desktop-fall-2019/manage-app-groups-2019.md)bakın.

Yeni bir Windows sanal masaüstü konak havuzu için oluşturulan varsayılan uygulama grubu, tam masaüstünü de yayımlar. Ayrıca, konak havuzu için bir veya daha fazla RemoteApp uygulama grubu oluşturabilirsiniz. Bir RemoteApp uygulama grubu oluşturmak ve tek tek **Başlat** menüsü uygulamalarını yayımlamak için bu öğreticiyi izleyin.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * RemoteApp grubu oluşturun.
> * RemoteApp programlarına erişim izni verin.

## <a name="prerequisites"></a>Önkoşullar

Bu makalede PowerShell modülünü ayarlamak ve Azure hesabınızda oturum açmak için [PowerShell modülünü ayarlama](powershell-module.md) bölümündeki yönergeleri izlediğiniz varsayılmaktadır.

## <a name="create-a-remoteapp-group"></a>RemoteApp grubu oluşturma

PowerShell ile bir RemoteApp grubu oluşturmak için:

1. Yeni boş bir RemoteApp uygulama grubu oluşturmak için aşağıdaki PowerShell cmdlet 'ini çalıştırın.

   ```powershell
   New-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname> -ApplicationGroupType "RemoteApp" -HostPoolArmPath '/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName/providers/Microsoft.DesktopVirtualization/hostPools/HostPoolName'-Location <azureregion>
   ```

2. Seçim Uygulama grubunun oluşturulduğunu doğrulamak için, konak havuzunun tüm uygulama gruplarının listesini görmek üzere aşağıdaki cmdlet 'i çalıştırabilirsiniz.

   ```powershell
   Get-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

3. Konak havuzunun sanal makine görüntüsündeki **Başlat** menüsü uygulamalarının listesini almak için aşağıdaki cmdlet 'i çalıştırın. Dosya **FilePath**, **IPath**, **IIndex** ve yayımlamak istediğiniz uygulama için diğer önemli bilgilerin değerlerini yazın.

   ```powershell
   Get-AzWvdStartMenuItem -ApplicationGroupName <appgroupname> -ResourceGroupName <resourcegroupname> | Format-List | more
   ```

   Çıktı, tüm Başlat menüsü öğelerini şöyle bir biçimde göstermelidir:

   ```powershell
   AppAlias            : access
   CommandLineArgument :
   FilePath            : C:\Program Files\Microsoft Office\root\Office16\MSACCESS.EXE
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\Program Files\Microsoft Office\Root\VFS\Windows\Installer\{90160000-000F-0000-1000-0000000FF1CE}\accicons.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Access
   Name                : 0301RAG/Access
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems

   AppAlias            : charactermap
   CommandLineArgument :
   FilePath            : C:\windows\system32\charmap.exe
   FriendlyName        :
   IconIndex           : 0
   IconPath            : C:\windows\system32\charmap.exe
   Id                  : /subscriptions/resourcegroups/providers/Microsoft.DesktopVirtualization/applicationgroups/startmenuitems/Character Map
   Name                : 0301RAG/Character Map
   Type                : Microsoft.DesktopVirtualization/applicationgroups/startmenuitems
   ```

4. Uygulamasına göre uygulamayı yüklemek için aşağıdaki cmdlet 'i çalıştırın `AppAlias` . `AppAlias` 3. adımdaki çıktıyı çalıştırdığınızda görünür hale gelir.

   ```powershell
   New-AzWvdApplication -AppAlias <appalias> -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

5. Seçim Yeni bir RemoteApp programını adım 1 ' de oluşturulan uygulama grubuna yayımlamak için aşağıdaki cmdlet 'i çalıştırın.

   ```powershell
   New-AzWvdApplication -GroupName <appgroupname> -Name <remoteappname> -ResourceGroupName <resourcegroupname> -Filepath <filepath> -IconPath <iconpath> -IconIndex <iconindex> -CommandLineSetting <DoNotAllow|Allow|Require>
   ```

6. Uygulamanın yayımlandığını doğrulamak için aşağıdaki cmdlet 'i çalıştırın.

   ```powershell
   Get-AzWvdApplication -GroupName <appgroupname> -ResourceGroupName <resourcegroupname>
   ```

7. Bu uygulama grubu için yayımlamak istediğiniz her uygulama için 1 – 5 arasındaki adımları yineleyin.
8. Kullanıcılara uygulama grubundaki RemoteApp programlarına erişim vermek için aşağıdaki cmdlet 'i çalıştırın.

   ```powershell
   New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
   ```

## <a name="next-steps"></a>Sonraki adımlar

Öğreticilerimizden bu nasıl yapılır Kılavuzu ' na ulaşdıysanız, [hizmet güncelleştirmelerini doğrulamak için bir konak havuzu oluşturma](create-validation-host-pool.md)' ya göz atın. Hizmet güncelleştirmelerini üretim ortamınıza göndermeden önce izlemek için bir doğrulama ana bilgisayar havuzu kullanabilirsiniz.