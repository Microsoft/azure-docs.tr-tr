---
title: Azure VM 'de Konuk işletim sistemi güvenlik duvarını devre dışı bırakma | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 6883d046078b5dccd2f1e83e87b41ca83edc92e3
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030592"
---
# <a name="disable-the-guest-os-firewall-in-azure-vm"></a>Azure VM'de konuk işletim sistemi Güvenlik Duvarını devre dışı bırakma

Bu makalede, Konuk işletim sistemi güvenlik duvarının bir sanal makineye (VM) kısmi veya tamamlanmış trafiği filtrelemesine şüphelendiğiniz durumlara yönelik bir başvuru sağlanmaktadır. Güvenlik duvarında RDP bağlantılarının başarısız olmasına neden olan değişiklikler kasıtlı olarak yapılmışsa bu durum oluşabilir.

## <a name="solution"></a>Çözüm

Bu makalede açıklanan işlem, bir geçici çözüm olarak kullanılmak üzere tasarlanmıştır. böylece, güvenlik duvarı kurallarını doğru bir şekilde ayarlama işlemi, gerçek sorununuzu gidermeye odaklanabilmenizi sağlar. I\rquote, Windows Güvenlik Duvarı bileşeninin etkin olması için En Iyi Microsoft uygulamasıdır. Güvenlik duvarı kurallarını yapılandırma \ CF3, \rquote 'un gerektirdiği sanal makineye erişim düzeyine bağlıdır.

### <a name="online-solutions"></a>Çevrimiçi çözümler 

VM çevrimiçiyse ve aynı sanal ağdaki başka bir VM üzerinden erişilebiliyorsa, diğer VM 'yi kullanarak bu azaltmaları yapabilirsiniz.

#### <a name="mitigation-1-custom-script-extension-or-run-command-feature"></a>Hafifletme 1: özel Betik uzantısı veya komut özelliği Çalıştır

Çalışan bir Azure aracınız varsa, aşağıdaki komut dosyalarını uzaktan çalıştırmak için [Özel Betik uzantısı](../extensions/custom-script-windows.md) veya [Komut Çalıştır](../windows/run-command.md) özelliğini (yalnızca Kaynak Yöneticisi VM 'ler) kullanabilirsiniz.

> [!Note]
> * Güvenlik Duvarı yerel olarak ayarlandıysa, aşağıdaki betiği çalıştırın:
>   ```
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 0 
>   Restart-Service -Name mpssvc
>   ```
> * Güvenlik Duvarı bir Active Directory ilkesi aracılığıyla ayarlandıysa, geçici erişim için aşağıdaki betiği Çalıştır ' ı kullanabilirsiniz. 
>   ```
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile' name "EnableFirewall" -Value 0
>   Restart-Service -Name mpssvc
>   ```
>   Ancak, ilke yeniden uygulandıktan hemen sonra uzak oturumdan vazgeçmeniz gerekir. Bu soruna yönelik kalıcı düzelme, bu bilgisayarda uygulanan ilkeyi değiştirmektir.

#### <a name="mitigation-2-remote-powershell"></a>Risk azaltma 2: uzak PowerShell

1.  RDP bağlantısı kullanarak ulaşamamanıza izin veren VM ile aynı sanal ağda bulunan bir VM 'ye bağlanın.

2.  Bir PowerShell konsol penceresi açın.

3.  Aşağıdaki komutları çalıştırın:

    ```powershell
    Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
    netsh advfirewall set allprofiles state off
    Restart-Service -Name mpssvc 
    exit
    ```

> [!Note]
> Güvenlik Duvarı bir grup ilkesi nesnesi aracılığıyla ayarlandıysa, bu komut yalnızca yerel kayıt defteri girdilerini değiştirdiği için bu yöntem çalışmayabilir. Bir ilke mevcutsa, bu değişiklik geçersiz kılınır. 

#### <a name="mitigation-3-pstools-commands"></a>Hafifletme 3: Padstools komutları

1.  Sorun giderme sanal makinesinde [PsTools](https://docs.microsoft.com/sysinternals/downloads/pstools)'u indirin.

2.  Bir CMD örneği açın ve ardından sanal makinesine DIP aracılığıyla erişin.

3.  Aşağıdaki komutları çalıştırın:

    ```cmd
    psexec \\<DIP> -u <username> cmd
    netsh advfirewall set allprofiles state off
    psservice restart mpssvc
    ```

#### <a name="mitigation-4-remote-registry"></a>Hafifletme 4: uzak kayıt defteri 

[Uzak kayıt defteri](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry)kullanmak için bu adımları izleyin.

1.  Sorun giderme sanal makinesinde, kayıt defteri Düzenleyicisi 'ni başlatın ve ardından **dosya** > **ağ kayıt defteri**'ne gidin.

2.  *Hedef makine*\System dalını açın ve aşağıdaki değerleri belirtin:

    ```
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\EnableFirewall         -->        0
    ```

3.  Hizmeti yeniden başlatın. Uzak kayıt defteri 'ni kullanarak bunu yapamadığından, hizmet konsolunu Kaldır ' ı kullanmanız gerekir.

4.  **Services. msc**örneğini açın.

5.  **Hizmetler (yerel)** seçeneğine tıklayın.

6.  **Başka bir bilgisayara bağlan**' ı seçin.

7.  Sorun sanal makinesinin **özel IP adresini (DIP)** girin.

8.  Yerel güvenlik duvarı ilkesini yeniden başlatın.

9.  Yerel bilgisayarınızdan RDP aracılığıyla sanal makineye bağlanmayı deneyin.

### <a name="offline-solutions"></a>Çevrimdışı çözümler 

Herhangi bir yöntemle sanal makineye ulaşamamanıza yönelik bir durumunuz varsa, Özel Betik uzantısı başarısız olur ve doğrudan sistem diski aracılığıyla çalışarak ÇEVRIMDıŞı modda çalışmanız gerekir. Bunu yapmak için şu adımları uygulayın:

1.  [Sistem diskini bir kurtarma VM 'Sine bağlayın](troubleshoot-recovery-disks-portal-windows.md).

2.  Kurtarma VM 'sine bir Uzak Masaüstü bağlantısı başlatın.

3.  Diskin Disk Yönetimi konsolunda çevrimiçi olarak işaretlendiğinden emin olun. Eklenmiş sistem diskine atanan sürücü harfini unutmayın.

4.  Değişiklik yapmadan önce, değişikliklerin geri alınması gerekli olduğunda \Windows\System32\Config klasörünün bir kopyasını oluşturun.

5.  Sorun giderme VM 'sinde, kayıt defteri Düzenleyicisi 'ni (Regedit. exe) başlatın. 

6.  Bu sorun giderme yordamı için, kovanları BROKENSYSTEM ve BROKENSOFTWARE olarak takıyoruz.

7.  HKEY_LOCAL_MACHINE anahtarını vurgulayın ve menüden > dosya Hive Yükle ' yi seçin.

8.  Eklenmiş sistem diskinde \windows\system32\config\SYSTEM dosyasını bulun.

9.  Yükseltilmiş bir PowerShell örneği açın ve aşağıdaki komutları çalıştırın:

    ```cmd
    # Load the hives - If your attached disk is not F, replace the letter assignment here
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM
    reg load HKLM\BROKENSOFTWARE f:\windows\system32\config\SOFTWARE 
    # Disable the firewall on the local policy
    $ControlSet = (get-ItemProperty -Path 'HKLM:\BROKENSYSTEM\Select' -name "Current").Current
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # To ensure the firewall is not set thru AD policy, check if the following registry entries exist and if they do, then check if the following entries exist:
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # Unload the hives
    reg unload HKLM\BROKENSYSTEM
    reg unload HKLM\BROKENSOFTWARE
    ```

10. [Sistem diskini ayırın ve VM 'yi yeniden oluşturun](troubleshoot-recovery-disks-portal-windows.md).

11. Sorunun çözümlenip çözümlenmediğini denetleyin.
