---
title: Azure sanal makinelerine RDP bağlantısı yaptığınızda bir iç hata oluşur | Microsoft Docs
description: Microsoft azure'da iç hatalar RDP sorunlarını gidermeyi öğrenin. | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/22/2018
ms.author: genli
ms.openlocfilehash: dac941b621c8df6b5c242bb5d0e0d5cdd1f864a9
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057959"
---
#  <a name="an-internal-error-occurs-when-you-try-to-connect-to-an-azure-vm-through-remote-desktop"></a>Uzak Masaüstü aracılığıyla Azure VM'ye bağlanmaya çalışırken bir iç hata oluşur.

Bu makalede, Microsoft azure'da bir sanal makineye (VM) bağlanmaya çalışırken karşılaşabileceğiniz hata açıklanır.
> [!NOTE]
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır: [Kaynak Yöneticisi ve klasik](../../azure-resource-manager/resource-manager-deployment-model.md). Bu makale, Klasik dağıtım modeli yerine yeni dağıtımlar için kullanmanızı öneririz Resource Manager dağıtım modelini kullanarak kapsar.

## <a name="symptoms"></a>Belirtiler

Uzak Masaüstü Protokolü (RDP) kullanarak bir Azure VM'sine bağlanılamıyor. Connection "Uzak yapılandırma" bölümüne takılı ve aşağıdaki hata iletisini alıyorsunuz:

- RDP iç hata
- Bir iç hata oluştu
- Bu bilgisayar, uzak bilgisayara bağlı olamaz. Yeniden bağlanmayı deneyin. Sorun devam ederse, uzak bilgisayarda veya ağ yöneticiniz sahibine başvurun.


## <a name="cause"></a>Nedeni

Bu sorun, aşağıdaki nedenlerle ortaya çıkabilir:

- Yerel RSA şifreleme anahtarlarının erişilemez.
- TLS protokolünü devre dışı bırakıldı.
- Sertifika bozuk veya süresi doldu.

## <a name="solution"></a>Çözüm

Bu adımları gerçekleştirmeden önce etkilenen makinenin işletim sistemi diskinin anlık yedekleyin. Daha fazla bilgi için [bir diskin anlık görüntüsünü alma](../windows/snapshot-copy-managed-disk.md).

Bu sorunu gidermek için seri konsolu veya [çevrimdışı VM'yi onarın](#repair-the-vm-offline) sanal Makinenin işletim sistemi diskini bir kurtarma sanal Makinesine ekleyerek.


### <a name="use-serial-control"></a>Seri denetimini kullanma

Bağlanma [seri konsol ve PowerShell örneği](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Seri konsol sanal makinenizde etkin değilse, Git [çevrimdışı VM'yi onarın](#repair-the-vm-offline) bölümü.

#### <a name="step-1-check-the-rdp-port"></a>Indan 1 RDP bağlantı noktasını denetleyin

1. Bir PowerShell örneği içinde kullanmak [NETSTAT](https://docs.microsoft.com/windows-server/administration/windows-commands/netstat
) 8080 bağlantı noktası başka bir uygulama tarafından kullanılıp kullanılmadığını kontrol etmek için:

        Netstat -anob |more
2. TermService.exe 8080 bağlantı noktası kullanıyorsa, 2. adıma gidin. Başka bir hizmet veya uygulama Termservice.exe dışında 8080 bağlantı noktası kullanıyorsa, aşağıdaki adımları izleyin:

    1. 3389 hizmetini kullanan uygulama için hizmeti durdurun:

            Stop-Service -Name <ServiceName> -Force

    2. Terminal hizmetini başlatın:

            Start-Service -Name Termservice

2. Uygulama durdurulamaz ya da bu yöntem için geçerli değilse, bağlantı noktası için RDP değiştirin:

    1. Bağlantı noktasını değiştirin:

            Set-ItemProperty -Path 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name PortNumber -value <Hexportnumber>

            Stop-Service -Name Termservice -Force
            
            Start-Service -Name Termservice 

    2. Yeni bağlantı noktası için Güvenlik Duvarı'nı ayarlayın:

            Set-NetFirewallRule -Name "RemoteDesktop-UserMode-In-TCP" -LocalPort <NEW PORT (decimal)>

    3. [Yeni bağlantı noktası için ağ güvenlik grubu güncelleştirme](../../virtual-network/security-overview.md) Azure portal RDP bağlantı noktası.

#### <a name="step-2-set-correct-permissions-on-the-rdp-self-signed-certificate"></a>2\. adım: RDP otomatik olarak imzalanan sertifika üzerinde doğru izinleri ayarla

1.  Bir PowerShell örneği, RDP otomatik olarak imzalanan sertifikayı yenilemek için aşağıdaki komutları tek tek çalıştırın:

        Import-Module PKI 
    
        Set-Location Cert:\LocalMachine 
        
        $RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint) 
        
        Remove-Item -Path $RdpCertThumbprint

        Stop-Service -Name "SessionEnv"

        Start-Service -Name "SessionEnv"

2. Bu yöntemi kullanarak sertifika yenileyemezsiniz uzaktan RDP otomatik olarak imzalanan sertifikayı yenilemek deneyin:

    1. Türü sorunlarla karşılaştığı çalışmasını VM bağlantısı olan VM **mmc** içinde **çalıştırma** kutusunu Microsoft Yönetim Konsolu'nu açın.
    2. Üzerinde **dosya** menüsünde **Ekle/Kaldır ek bileşenini**seçin **sertifikaları**ve ardından **Ekle**.
    3. Seçin **bilgisayar hesapları**seçin **başka bir bilgisayara**ve ardından sorun VM IP adresini ekleyin.
    4. Git **uzak Desktop\Certificates** klasör olan sertifikayı sağ tıklatın seçin ve sonra **Sil**.
    5. Bir PowerShell örneği seri konsolundan uzak masaüstü yapılandırması hizmetini yeniden başlatın:

            Stop-Service -Name "SessionEnv"

            Start-Service -Name "SessionEnv"
3. MachineKeys klasörü için izin sıfırlayın.

        remove-module psreadline icacls

        md c:\temp

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt 
        
        takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt 
        
        Restart-Service TermService -Force

4. VM'yi yeniden başlatın ve sonra Başlangıç VM'ye Uzak Masaüstü Bağlantısı'ı deneyin. Hata yine oluşursa, sonraki adıma gidin.

3\. adım: Desteklenen tüm TLS sürümlerini etkinleştir

RDP istemcisi varsayılan protokol TLS 1.0 kullanır. Ancak, bu yeni bir standart haline gelmiştir TLS 1.1 olarak değiştirilebilir. VM'de TLS 1.1 devre dışı bırakılırsa, bağlantı başarısız olur.
1.  CMD örneğinde, TLS protokolü etkinleştirin:

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
2.  AD İlkesi değişikliklerin üzerine yazmasını engellemek için Grup İlkesi güncelleştirme geçici olarak durdurun:

        REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
3.  Değişikliklerin etkili olması için VM'yi yeniden başlatın. Sorun çözüldüğünde, Grup İlkesi yeniden etkinleştirmek için aşağıdaki komutu çalıştırın:

        sc config gpsvc start= auto sc start gpsvc

        gpupdate /force
    Değişiklik geri alınır, şirket etki alanınızda Active Directory ilkesi olduğu anlamına gelir. Bu sorunun tekrar oluşmasını önlemek için bu ilkeyi değiştirmek zorunda.

### <a name="repair-the-vm-offline"></a>VM'yi çevrimdışı onarın

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>İşletim sistemi diskini bir kurtarma VM'si ekleme

1. [İşletim sistemi diskini bir kurtarma VM'si ekleme](../windows/troubleshoot-recovery-disks-portal.md).
2. İşletim sistemi diskini bir kurtarma VM'si bağlandıktan sonra disk olarak işaretlenmiş emin olun **çevrimiçi** Disk Yönetimi Konsolu'nda. Ekli işletim sistemi diski için atanan sürücü harfini unutmayın.
3. Kurtarma VM'sini bir Uzak Masaüstü Bağlantısı'nı başlatın.

#### <a name="enable-dump-log-and-serial-console"></a>Döküm günlük ve seri konsol etkinleştir

Döküm günlük ve seri konsol etkinleştirmek için aşağıdaki betiği çalıştırın.

1. Yükseltilmiş bir komut istemi oturumu açın (**yönetici olarak çalıştır**).
2. Şu betiği çalıştırın:

    Bu betikte ekli işletim sistemi diski için atanan sürücü harfini f Değiştir VM'niz için uygun değeri bu sürücü harfiyle olduğunu varsayıyoruz.

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

#### <a name="reset-the-permission-for-machinekeys-folder"></a>Sıfırlama izni MachineKeys klasörü

1. Yükseltilmiş bir komut istemi oturumu açın (**yönetici olarak çalıştır**).
2. Aşağıdaki betiği çalıştırın. Bu betikte ekli işletim sistemi diski için atanan sürücü harfini f Değiştir VM'niz için uygun değeri bu sürücü harfiyle olduğunu varsayıyoruz.

        Md F:\temp

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
        
        takeown /f "F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

        icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt

#### <a name="enable-all-supported-tls-versions"></a>Desteklenen tüm TLS sürümlerini etkinleştir

1.  Yükseltilmiş bir komut istemi oturumu açın (**yönetici olarak çalıştır**) ve aşağıdaki komutları. Aşağıdaki betiği ekli işletim sistemi diskinin sürücü harfi atandığından emin varsayar F. Değiştir VM'niz için uygun değer ile bu sürücü harfi olduğu.
2.  TLS etkin denetimi:

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWO

3.  Anahtar yok veya değeri **0**, aşağıdaki betik çalıştırarak protokolünü etkinleştirin:

        REM Enable TLS 1.0, TLS 1.1 and TLS 1.2

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

4.  NLA etkinleştir:

        REM Enable NLA

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f reg unload HKLM\BROKENSYSTEM
5.  [İşletim sistemi diskini ve VM yeniden](../windows/troubleshoot-recovery-disks-portal.md)ve sorunun çözülüp çözülmediğini denetleyin.





