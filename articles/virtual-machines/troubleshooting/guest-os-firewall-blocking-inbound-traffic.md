---
title: Azure VM Konuk işletim sistemi güvenlik duvarı gelen trafiği engelliyor | Microsoft Docs
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
ms.openlocfilehash: 0cbd1a24f5c460e248d55777735da6809befba63
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028798"
---
# <a name="azure-vm-guest-os-firewall-is-blocking-inbound-traffic"></a>Azure VM Konuk işletim sistemi güvenlik duvarı gelen trafiği engelliyor

Bu makalede, Konuk işletim sistemi güvenlik duvarı gelen trafiği engelliyorsa oluşan uzak masaüstü portalı (RDP) sorununun nasıl düzeltileceğini ele alınmaktadır.

## <a name="symptoms"></a>Belirtiler

Bir Azure sanal makinesine (VM) bağlanmak için RDP bağlantısı kullanamazsınız. Önyükleme tanılaması-> ekran görüntüsünden, işletim sisteminin hoş geldiniz ekranına (Ctrl + Alt + Del) tam olarak yüklendiğini gösterir.

## <a name="cause"></a>Nedeni

### <a name="cause-1"></a>Neden 1

RDP kuralı, RDP trafiğine izin verecek şekilde ayarlanmadı.

### <a name="cause-2"></a>Neden 2

Konuk sistem güvenlik duvarı profilleri, RDP trafiği de dahil olmak üzere tüm gelen bağlantıları engelleyecek şekilde ayarlanır.

![Güvenlik duvarı ayarı](./media/guest-os-firewall-blocking-inbound-traffic/firewall-advanced-setting.png)

## <a name="solution"></a>Çözüm

Bu adımları izlemeden önce, etkilenen VM 'nin sistem diskinin bir anlık görüntüsünü yedek olarak alın. Daha fazla bilgi için bkz. [disk anlık görüntüsü](../windows/snapshot-copy-managed-disk.md).

Sorunu gidermek için, [Uzak araçları kullanarak](remote-tools-troubleshoot-azure-vm-issues.md) VM 'ye uzaktan bağlanın ve ardından, RDP trafiğine **izin** vermek için konuk işletim sistemi güvenlik duvarı kurallarını düzenleyin.

### <a name="online-troubleshooting"></a>Çevrimiçi sorun giderme

[Seri konsoluna bağlanın ve ardından bir PowerShell örneği açın](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Seri konsol sanal makinede etkinleştirilmemişse, "[VM 'Yi çevrimdışı olarak onarın](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)' a gidin.

#### <a name="mitigation-1"></a>Risk azaltma 1

1.  Azure Aracısı yüklenip sanal makinede düzgün çalışıyorsa, VM menüsünde, **destek + sorun giderme** > **sıfırlama parolası** altında "yalnızca yapılandırmayı Sıfırla" seçeneğini kullanabilirsiniz.

2.  Bu kurtarma seçeneğini çalıştırmak şunları yapar:

    *   Devre dışıysa RDP bileşenini etkinleştirilir.

    *   Tüm Windows Güvenlik Duvarı profillerini etkinleştirilir.

    *   Windows Güvenlik Duvarı 'nda RDP kuralının açık olduğundan emin olun.

    *   Önceki adımlar çalışmazsa, güvenlik duvarı kuralını el ile sıfırlayın. Bunu yapmak için, aşağıdaki komutu çalıştırarak "Uzak Masaüstü" adını içeren tüm kuralları sorgulayın:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(Name.*Remote Desktop)" -context 9,4 | more
        ```

        RDP bağlantı noktası 3389 dışında başka bir bağlantı noktasına ayarlandıysa, oluşturulmuş olabilecek ve bu bağlantı noktasına ayarlanmış olan herhangi bir özel kural bulmanız gerekir. Özel bir bağlantı noktası olan tüm gelen kuralları sorgulamak için aşağıdaki komutu çalıştırın:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<CUSTOM PORT>)" -context 9,4 | more
        ```

3.  Kuralın devre dışı olduğunu görürseniz, etkinleştirin. Yerleşik uzak masaüstü grubu gibi bir grubun tamamını açmak için aşağıdaki komutu çalıştırın:

    ```cmd
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
    ```

    Aksi takdirde, belirli uzak masaüstü (TCP-ın) kuralını açmak için aşağıdaki komutu çalıştırın:

    ```cmd
    netsh advfirewall firewall set rule name="<CUSTOM RULE NAME>" new enable=yes
    ```

4.  Sorun giderme için Güvenlik Duvarı profillerini devre dışı bırakabilirsiniz:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Sorun gidermeyi ve güvenlik duvarını doğru şekilde ayarlamayı tamamladıktan sonra, güvenlik duvarını yeniden etkinleştirin.

    > [!Note]
    > Bu değişiklikleri uygulamak için VM 'yi yeniden başlatmanız gerekmez.

5.  SANAL makineye erişmek için bir RDP bağlantısı yapmayı deneyin.

#### <a name="mitigation-2"></a>Risk azaltma 2

1.  Gelen güvenlik duvarı ilkesinin *Blockinboundalways*olarak ayarlanmış olup olmadığını öğrenmek için Güvenlik Duvarı profillerini sorgulayın:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    ![AllProfiles](./media/guest-os-firewall-blocking-inbound-traffic/firewall-profiles.png)

    > [!Note]
    > Aşağıdaki yönergeler, nasıl ayarlandığına bağlı olarak güvenlik duvarı ilkesi için geçerlidir:
    >    * *Blockınbound*: Bu trafiğe izin vermek için bir kuralınızın olmadığı durumlar dışında tüm gelen trafik engellenir.
    >    * *Blockinboundalways*: tüm güvenlik duvarı kuralları yok sayılacak ve tüm trafik engellenmeyecektir.

2.  Bu profilleri trafiğe **izin** verecek şekilde ayarlamak Için *Defaulınboundavction* öğesini düzenleyin. Bunu yapmak için aşağıdaki komutu çalıştırın:

    ```cmd
    netsh advfirewall set allprofiles firewallpolicy allowinbound,allowoutbound
    ```

3.  Yaptığınız değişikliğin başarılı bir şekilde yapıldığından emin olmak için profilleri tekrar sorgulayın. Bunu yapmak için aşağıdaki komutu çalıştırın:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    > [!Note]
    > Değişiklikleri uygulamak için VM 'yi yeniden başlatmanız gerekmez.

4.  Sanal makinenize RDP aracılığıyla erişmeyi yeniden deneyin.

### <a name="offline-mitigations"></a>Çevrimdışı azaltmaları

1.  [Sistem diskini bir kurtarma VM 'Sine bağlayın](troubleshoot-recovery-disks-portal-windows.md).

2.  Kurtarma VM 'sine bir Uzak Masaüstü bağlantısı başlatın.

3.  Diskin Disk Yönetimi konsolunda **çevrimiçi** olarak işaretlendiğinden emin olun. Eklenmiş sistem diskine atanan sürücü harfini unutmayın.

#### <a name="mitigation-1"></a>Risk azaltma 1

Bkz. [Enable-bir güvenlik duvarı kuralını Konuk işletim sisteminde devre dışı bırakma](enable-disable-firewall-rule-guest-os.md).

#### <a name="mitigation-2"></a>Risk azaltma 2

1.  [Sistem diskini bir kurtarma VM 'Sine bağlayın](troubleshoot-recovery-disks-portal-windows.md).

2.  Kurtarma VM 'sine bir Uzak Masaüstü bağlantısı başlatın.

3.  Sistem diski kurtarma VM 'sine eklendikten sonra, diskin Disk Yönetimi konsolunda **çevrimiçi** olarak işaretlendiğinden emin olun. Bağlı işletim sistemi diskine atanan sürücü harfini unutmayın.

4.  Yükseltilmiş bir CMD örneği açın ve aşağıdaki betiği çalıştırın:

    ```cmd
    REM Backup the registry prior doing any change
    robocopy f:\windows\system32\config f:\windows\system32\config.BACK /MT

    REM Mount the hive
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

    REM Delete the keys to block all inbound connection scenario
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions

    REM Unmount the hive
    reg unload HKLM\BROKENSYSTEM
    ```

5.  [Sistem diskini ayırın ve VM 'yi yeniden oluşturun](troubleshoot-recovery-disks-portal-windows.md).

6.  Sorunun çözümlenip çözümlenmediğini denetleyin.
