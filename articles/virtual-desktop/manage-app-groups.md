---
title: Windows sanal masaüstü-Azure için uygulama gruplarını yönetme
description: Azure Active Directory 'da Windows sanal masaüstü kiracılarının nasıl ayarlanacağını açıklar.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: e158c0a6090493bec0169c144f030300de921516
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679450"
---
# <a name="tutorial-manage-app-groups-for-windows-virtual-desktop"></a>Öğretici: Windows sanal masaüstü için uygulama gruplarını yönetme

Yeni bir Windows sanal masaüstü konak havuzu için oluşturulan varsayılan uygulama grubu, tam masaüstünü de yayımlar. Ayrıca, konak havuzu için bir veya daha fazla RemoteApp uygulama grubu oluşturabilirsiniz. Bir RemoteApp uygulama grubu oluşturmak ve tek tek **Başlat** menüsü uygulamalarını yayımlamak için bu öğreticiyi izleyin.

Bu öğreticide şunları yapmayı öğreneceksiniz:

> [!div class="checklist"]
> * RemoteApp grubu oluşturun.
> * RemoteApp programlarına erişim izni verin.

Başlamadan önce, henüz yapmadıysanız PowerShell oturumunuzda kullanmak üzere [Windows sanal masaüstü PowerShell modülünü indirip içeri aktarın](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) . Bundan sonra hesabınızda oturum açmak için aşağıdaki cmdlet 'i çalıştırın:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-a-remoteapp-group"></a>RemoteApp grubu oluşturma

1. Yeni boş bir RemoteApp uygulama grubu oluşturmak için aşağıdaki PowerShell cmdlet 'ini çalıştırın.

   ```powershell
   New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType "RemoteApp"
   ```

2. Seçim Uygulama grubunun oluşturulduğunu doğrulamak için, konak havuzunun tüm uygulama gruplarının listesini görmek üzere aşağıdaki cmdlet 'i çalıştırabilirsiniz.

   ```powershell
   Get-RdsAppGroup <tenantname> <hostpoolname>
   ```

3. Konak havuzunun sanal makine görüntüsündeki **Başlat** menüsü uygulamalarının listesini almak için aşağıdaki cmdlet 'i çalıştırın. Dosya **FilePath**, **IPath**, **IIndex**ve yayımlamak istediğiniz uygulama için diğer önemli bilgilerin değerlerini yazın.

   ```powershell
   Get-RdsStartMenuApp <tenantname> <hostpoolname> <appgroupname>
   ```
   
4. @No__t-0 temelinde uygulamayı yüklemek için aşağıdaki cmdlet 'i çalıştırın. `AppAlias`, 3. adımdaki çıktıyı çalıştırdığınızda görünür hale gelir.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -AppAlias <appalias>
   ```

5. Seçim Yeni bir RemoteApp programını adım 1 ' de oluşturulan uygulama grubuna yayımlamak için aşağıdaki cmdlet 'i çalıştırın.

   ```powershell
   New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> -Name <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
   ```

6. Uygulamanın yayımlandığını doğrulamak için aşağıdaki cmdlet 'i çalıştırın.

   ```powershell
   Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
   ```

7. Bu uygulama grubu için yayımlamak istediğiniz her uygulama için 1 – 5 arasındaki adımları yineleyin.
8. Kullanıcılara uygulama grubundaki RemoteApp programlarına erişim vermek için aşağıdaki cmdlet 'i çalıştırın.

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalName <userupn>
   ```

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, bir uygulama grubu oluşturmayı, RemoteApp programları ile doldurmayı ve uygulama grubuna kullanıcı atamayı öğrendiniz. Doğrulama ana bilgisayar havuzu oluşturmayı öğrenmek için aşağıdaki öğreticiye bakın. Hizmet güncelleştirmelerini üretim ortamınıza göndermeden önce izlemek için bir doğrulama ana bilgisayar havuzu kullanabilirsiniz.

> [!div class="nextstepaction"]
> [Hizmet güncelleştirmelerini doğrulamak için bir konak havuzu oluşturma](./create-validation-host-pool.md)
