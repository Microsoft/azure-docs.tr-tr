---
title: Azure VM 'de Konuk işletim sisteminde bir güvenlik duvarı kuralını etkinleştirme veya devre dışı bırakma | Microsoft Docs
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
ms.openlocfilehash: 782240c51833fc841af9f4260860db4c03897c03
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71086445"
---
# <a name="enable-or-disable-a-firewall-rule-on-an-azure-vm-guest-os"></a>Azure VM Konuk işletim sisteminde bir güvenlik duvarı kuralını etkinleştirme veya devre dışı bırakma

Bu makalede, Konuk işletim sistemi güvenlik duvarının bir sanal makinede (VM) kısmi trafiği filtrelerken şüphelendiğiniz bir durum sorunlarını gidermeye yönelik bir başvuru sağlanmaktadır. Bu, aşağıdaki nedenlerden dolayı yararlı olabilir:

*   Güvenlik duvarında RDP bağlantılarının başarısız olmasına neden olan bir değişiklik daha önce yapılmışsa, Özel Betik uzantısı özelliğinin kullanılması sorunu çözebilir.

*   Tüm güvenlik duvarı profillerinin devre dışı bırakılması, RDP 'ye özgü güvenlik duvarı kuralını ayarlamaktan farklı sorun gidermeye yönelik daha fazla kanıt yoludur.

## <a name="solution"></a>Çözüm

Güvenlik duvarı kurallarını yapılandırma, gereken sanal makineye erişim düzeyine bağlıdır. Aşağıdaki örnekler RDP kurallarını kullanır. Bununla birlikte, doğru kayıt defteri anahtarına işaret ederek aynı yöntemler diğer tür trafiğe de uygulanabilir.

### <a name="online-troubleshooting"></a>Çevrimiçi sorun giderme 

#### <a name="mitigation-1-custom-script-extension"></a>Risk azaltma 1: Özel Betik Uzantısı

1.  Aşağıdaki şablonu kullanarak komut dosyanızı oluşturun.

    *   Bir kuralı etkinleştirmek için:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Bir kuralı devre dışı bırakmak için:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

2.  [Özel Betik uzantısı](../extensions/custom-script-windows.md) özelliğini kullanarak bu betiği Azure Portal yükleyin. 

#### <a name="mitigation-2-remote-powershell"></a>Risk azaltma 2: Uzak PowerShell

VM çevrimiçiyse ve aynı sanal ağdaki başka bir VM üzerinden erişilebiliyorsa, diğer VM 'yi kullanarak takip azaltıcı etkenleri yapabilirsiniz.

1.  Sorun giderme VM 'sinde bir PowerShell konsol penceresi açın.

2.  Aşağıdaki komutları uygun şekilde çalıştırın.

    *   Bir kuralı etkinleştirmek için:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Enable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

    *   Bir kuralı devre dışı bırakmak için:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Disable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

#### <a name="mitigation-3-pstools-commands"></a>Risk azaltma 3: Padstools komutları

VM çevrimiçiyse ve aynı sanal ağdaki başka bir VM üzerinden erişilebiliyorsa, diğer VM 'yi kullanarak takip azaltıcı etkenleri yapabilirsiniz.

1.  Sorun giderme sanal makinesinde [PsTools](https://docs.microsoft.com/sysinternals/downloads/pstools)'u indirin.

2.  Bir CMD örneği açın ve VM 'ye Iç IP (DIP) üzerinden erişin. 

    * Bir kuralı etkinleştirmek için:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Bir kuralı devre dışı bırakmak için:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

#### <a name="mitigation-4-remote-registry"></a>Risk azaltma 4: Uzak kayıt defteri

VM çevrimiçiyse ve aynı sanal ağdaki başka bir VM üzerinden erişilebiliyorsa, diğer VM 'de [Uzak kayıt defteri](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry) kullanabilirsiniz.

1.  Sorun giderme VM 'sinde, kayıt defteri Düzenleyicisi 'ni (Regedit. exe) başlatın ve ardından **Dosya** > **Connect ağ kayıt defteri**' ni seçin.

2.  *Hedef makine*\System dalını açın ve ardından aşağıdaki değerleri belirtin:

    * Bir kuralı etkinleştirmek için aşağıdaki kayıt defteri değerini açın:
    
        *Hedef makine*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-usermode-ın-TCP
    
        Ardından, dizedeki **etkin = FALSE değerini** **etkin = true** olarak değiştirin:

        **v 2.22 | Eylem = Izin ver | Etkin = TRUE | Dır = | Protokol = 6 | Profil = etki alanı | Profil = özel | Profil = genel | LPort = 3389 | Uygulama =%SystemRoot%\system32\svchost.exe | Svc = TermService | Ad =\@FirewallAPI. dll,-28775 | DESC =\@FirewallAPI. dll,-28756 | Embedctxt =\@FirewallAPI. dll,-28752 |**
    
    * Bir kuralı devre dışı bırakmak için aşağıdaki kayıt defteri değerini açın:
    
        *Hedef makine*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-usermode-ın-TCP

        Ardından, **etkin = TRUE değerini** **etkin = false**olarak değiştirin:
        
        **v 2.22 | Eylem = Izin ver | Etkin = yanlış | Dır = | Protokol = 6 | Profil = etki alanı | Profil = özel | Profil = genel | LPort = 3389 | Uygulama =%SystemRoot%\system32\svchost.exe | Svc = TermService | Ad =\@FirewallAPI. dll,-28775 | DESC =\@FirewallAPI. dll,-28756 | Embedctxt =\@FirewallAPI. dll,-28752 |**

3.  Değişiklikleri uygulamak için VM 'yi yeniden başlatın.

### <a name="offline-troubleshooting"></a>Çevrimdışı sorun giderme 

VM 'ye herhangi bir yöntemle erişemiyorsanız, Özel Betik uzantısının kullanılması başarısız olur ve doğrudan sistem diski aracılığıyla çalışarak ÇEVRIMDıŞı modda çalışmanız gerekir.

Bu adımları gerçekleştirmeden önce sistem diski etkilenen sanal makinenin anlık görüntüsünü yedekleyin. Daha fazla bilgi için [bir diskin anlık görüntüsünü alma](../windows/snapshot-copy-managed-disk.md).

1.  [Bir kurtarma VM'si sistem diski](troubleshoot-recovery-disks-portal-windows.md).

2.  Kurtarma VM'sini bir Uzak Masaüstü Bağlantısı'nı başlatın.

3.  Disk olarak işaretlenmiş olduğundan emin olun **çevrimiçi** Disk Yönetimi Konsolu'nda. Eklenmiş sistem diskine atanan sürücü harfinin olduğunu unutmayın.

4.  Değişiklik yapmadan önce, değişikliklerin geri alınması gerekli olduğunda \Windows\System32\Config klasörünün bir kopyasını oluşturun.

5.  Sorun giderme VM 'sinde, kayıt defteri Düzenleyicisi 'ni (Regedit. exe) başlatın.

6.  **HKEY_LOCAL_MACHINE** anahtarını vurgulayın ve menüden **Dosya** > **yükleme Hive** ' yi seçin.

    ![B](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  \Windows\system32\config\SYSTEM dosyasını bulun ve açın. 

    > [!Note]
    > Sizden bir ad girmeniz istenir. **Brokensystem**girin ve ardından **HKEY_LOCAL_MACHINE**' i genişletin. Şimdi, **brokensystem**adlı ek bir anahtar görürsünüz. Bu sorun giderme için, bu sorun kovanlarını **brokensystem**olarak oluşturacağız.

8.  BROKENSYSTEM dalında aşağıdaki değişiklikleri yapın:

    1.  VM 'nin başladığı **CONTROLSET** kayıt defteri anahtarını denetleyin. Anahtar numarasını, Hklm\brokensystem\select\currentkonumunda görürsünüz.

    2.  Bir kuralı etkinleştirmek için aşağıdaki kayıt defteri değerini açın:
    
        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
        
        Ardından, **etkin = FALSE değerini** **etkin = true**olarak değiştirin.
        
        **v 2.22 | Eylem = Izin ver | Etkin = TRUE | Dır = | Protokol = 6 | Profil = etki alanı | Profil = özel | Profil = genel | LPort = 3389 | Uygulama =%SystemRoot%\system32\svchost.exe | Svc = TermService | Ad =\@FirewallAPI. dll,-28775 | DESC =\@FirewallAPI. dll,-28756 | Embedctxt =\@FirewallAPI. dll,-28752 |**

    3.  Bir kuralı devre dışı bırakmak için aşağıdaki kayıt defteri anahtarını açın:

        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Ardından, **etkin = true değerini** **etkin = false**olarak değiştirin.
        
        **v 2.22 | Eylem = Izin ver | Etkin = yanlış | Dır = | Protokol = 6 | Profil = etki alanı | Profil = özel | Profil = genel | LPort = 3389 | Uygulama =%SystemRoot%\system32\svchost.exe | Svc = TermService | Ad =\@FirewallAPI. dll,-28775 | DESC =\@FirewallAPI. dll,-28756 | Embedctxt =\@FirewallAPI. dll,-28752 |**

9.  **Brokensystem**öğesini vurgulayın ve sonra menüden **Dosya** > **Kaldır Hive** öğesini seçin.

10. [VM yeniden oluşturma ve sistem diskini](troubleshoot-recovery-disks-portal-windows.md).

11. Sorunun çözülüp çözülmediğini denetleyin.
