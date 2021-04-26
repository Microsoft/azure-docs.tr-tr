---
title: Bir Windows VHD 'yi Azure 'a yüklemek üzere hazırlama
description: Bir Windows VHD veya VHDX 'i Azure 'a yüklemek için nasıl hazırlayacağınızı öğrenin
author: glimoli
manager: dcscontentpm
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 09/02/2020
ms.author: genli
ms.openlocfilehash: 573f97c7f592186173b13ea592d151ee291b8249
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967974"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Azure’a yüklemek için Windows VHD veya VHDX’i hazırlama

Şirket içinden Azure 'a bir Windows sanal makinesi (VM) yüklemeden önce sanal sabit diski (VHD veya VHDX) hazırlamanız gerekir. Azure, VHD dosya biçimindeki ve sabit boyutlu diski olan 1. nesil ve 2. nesil VM 'Leri destekler. 1. nesil VM 'deki işletim sistemi VHD 'SI için izin verilen en büyük boyut 2 TB 'tır.

VHDX dosyasını VHD 'ye dönüştürebilir, dinamik olarak genişleyen bir diski sabit boyutlu bir diske dönüştürebilirsiniz, ancak bir VM 'nin oluşturulmasını değiştiremezsiniz. Daha fazla bilgi için bkz. [Hyper-V ' d i 1 veya 2. nesıl VM oluşturma](/windows-server/virtualization/hyper-v/plan/Should-I-create-a-generation-1-or-2-virtual-machine-in-Hyper-V) ve [Azure 'da 2. nesil VM 'ler için destek](../generation-2.md).

Azure VM 'Leri için destek ilkesi hakkında daha fazla bilgi için bkz. [Azure VM 'ler Için Microsoft sunucu yazılımı desteği](https://support.microsoft.com/help/2721672/).

> [!NOTE]
> Bu makaledeki yönergeler için geçerlidir:
>
> - Windows Server 2008 R2 ve üzeri Windows Server işletim sistemlerinin 64 bitlik sürümü. Azure 'da 32 bitlik bir işletim sistemi çalıştırma hakkında bilgi için bkz. [Azure VM 'lerde 32 bit işletim sistemleri Için destek](https://support.microsoft.com/help/4021388/).
> - Azure Site Recovery veya Azure geçişi gibi iş yükünü geçirmek için herhangi bir olağanüstü durum kurtarma aracı kullanılacaksa, bu işlem, görüntüyü geçişten önce hazırlamak için konuk işletim sisteminde yine de gereklidir.

## <a name="system-file-checker"></a> Sistem Dosyası Denetleyicisi

### <a name="run-windows-system-file-checker-utility-before-generalization-of-os-image"></a>IŞLETIM sistemi görüntüsünün genelleştirilmeye başlamadan önce Windows sistem dosyası denetleyicisi yardımcı programını çalıştır

Sistem dosyası denetleyicisi (SFC), Windows sistem dosyalarını doğrulamak ve değiştirmek için kullanılır.

> [!IMPORTANT]
> Bu makaledeki örnekleri çalıştırmak için yükseltilmiş bir PowerShell oturumu kullanın.

SFC komutunu çalıştırın:

```powershell
sfc.exe /scannow
```

```Output
Beginning system scan.  This process will take some time.

Beginning verification phase of system scan.
Verification 100% complete.

Windows Resource Protection did not find any integrity violations.
```

SFC taraması tamamlandıktan sonra, Windows güncelleştirmelerini yükledikten sonra bilgisayarı yeniden başlatın.

## <a name="set-windows-configurations-for-azure"></a>Azure için Windows yapılandırmalarını ayarlama

> [!NOTE]
> Azure platformu, genelleştirilmiş bir görüntüden Windows VM oluşturulduğunda bir ISO dosyasını DVD-ROM ' d a bağlar. Bu nedenle, genelleştirilmiş görüntüde DVD-ROM ' u n IŞLETIM sisteminde etkinleştirilmiş olması gerekir. Devre dışıysa, Windows VM, kullanıma hazır deneyimle (OOBE) üzerinden takılacaktır.

1. Yönlendirme tablosundaki tüm statik kalıcı yolları kaldırın:

   - Yönlendirme tablosunu görüntülemek için öğesini çalıştırın `route.exe print` .
   - **Kalıcılık rotaları** bölümünü denetleyin. Kalıcı bir yol varsa `route.exe delete` kaldırmak için komutunu kullanın.

1. WinHTTP proxy 'sini kaldır:

   ```powershell
   netsh.exe winhttp reset proxy
   ```

    VM 'nin belirli bir ara sunucu ile çalışması gerekiyorsa, sanal makinenin Azure 'a bağlanabilmesi için Azure IP adresi ([168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)) için bir proxy özel durumu ekleyin:

    ```
    $proxyAddress='<your proxy server>'
    $proxyBypassList='<your list of bypasses>;168.63.129.16'
    netsh.exe winhttp set proxy $proxyAddress $proxyBypassList
    ```

1. DiskPart 'ı aç:

   ```powershell
   diskpart.exe
   ```

   Disk SAN ilkesini şu şekilde ayarlayın [`Onlineall`](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/gg252636(v=ws.11)) :

   ```DiskPart
   DISKPART> san policy=onlineall
   DISKPART> exit
   ```

1. Windows için Eşgüdümlü Evrensel Saat (UTC) süresini ayarlayın. Ayrıca, Windows Saat hizmeti **W32Time** başlangıç türünü **Otomatik** olarak ayarlayın:

   ```powershell
   Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation -Name RealTimeIsUniversal -Value 1 -Type DWord -Force
   Set-Service -Name w32time -StartupType Automatic
   ```

1. Güç profilini yüksek performans olarak ayarlayın:

   ```powershell
   powercfg.exe /setactive SCHEME_MIN
   ```

1. **Geçici** ve **tmp** ortam değişkenlerinin varsayılan değerlerine ayarlandığından emin olun:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name TEMP -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name TMP -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
   ```
1. Eski işletim sistemlerine sahip VM 'Ler için (Windows Server 2012 R2 veya Windows 8.1 ve aşağıdaki), en son Hyper-V tümleştirme bileşen hizmetlerinin yüklü olduğundan emin olun. Daha fazla bilgi için bkz. [Windows sanal makinesi Için Hyper-V tümleştirme bileşenleri güncelleştirmesi](https://support.microsoft.com/topic/hyper-v-integration-components-update-for-windows-virtual-machines-8a74ffad-576e-d5a0-5a2f-d6fb2594f990).

> [!NOTE]
> VM 'Lerin şirket içi VMware sunucusu ile Azure arasında bir olağanüstü durum kurtarma çözümü ile ayarlandığı bir senaryoda, Hyper-V tümleştirme Bileşen Hizmetleri kullanılamaz. Bu durumda, lütfen VM 'yi Azure 'a geçirmek ve VMware sunucusunda birlikte bulundurgetirmek için VMware desteğiyle iletişim kurun.

## <a name="check-the-windows-services"></a>Windows hizmetlerini denetleme

Aşağıdaki Windows hizmetlerinin her birinin Windows varsayılan değerine ayarlandığından emin olun. Bu hizmetler VM bağlantısı sağlamak için yapılandırılması gereken en düşük gereksinimdir. Başlangıç ayarlarını ayarlamak için aşağıdaki örneği çalıştırın:

```powershell
Get-Service -Name BFE, Dhcp, Dnscache, IKEEXT, iphlpsvc, nsi, mpssvc, RemoteRegistry |
  Where-Object StartType -ne Automatic |
    Set-Service -StartupType Automatic

Get-Service -Name Netlogon, Netman, TermService |
  Where-Object StartType -ne Manual |
    Set-Service -StartupType Manual
```

## <a name="update-remote-desktop-registry-settings"></a>Uzak Masaüstü kayıt defteri ayarlarını Güncelleştir

Aşağıdaki ayarların uzaktan erişim için doğru yapılandırıldığından emin olun:

> [!NOTE]
> Çalıştırırken bir hata mesajı alırsanız `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <string> -Value <object>` , güvenli bir şekilde yoksayabilirsiniz. Bu, etki alanının bu yapılandırmayı bir grup ilkesi nesnesi aracılığıyla ayarlamamasıdır.

1. Uzak Masaüstü Protokolü (RDP) etkin:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name fDenyTSConnections -Value 0 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name fDenyTSConnections -Value 0 -Type DWord -Force
   ```

1. RDP bağlantı noktası, 3389 varsayılan bağlantı noktası kullanılarak doğru şekilde ayarlanır:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name PortNumber -Value 3389 -Type DWord -Force
   ```

   Bir VM dağıtırken, 3389 numaralı bağlantı noktası için varsayılan kurallar oluşturulur. Bağlantı noktası numarasını değiştirmek için, VM Azure 'da dağıtıldıktan sonra bunu yapın.

1. Dinleyici her ağ arabirimini dinler:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name LanAdapter -Value 0 -Type DWord -Force
   ```

1. RDP bağlantıları için ağ düzeyinde kimlik doğrulaması (NLA) modunu yapılandırın:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name UserAuthentication -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name SecurityLayer -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name fAllowSecProtocolNegotiation -Value 1 -Type DWord -Force
   ```

1. Etkin tut değerini ayarlayın:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name KeepAliveEnable -Value 1  -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name KeepAliveInterval -Value 1  -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name KeepAliveTimeout -Value 1 -Type DWord -Force
   ```

1. Yeniden bağlanma seçeneklerini ayarlayın:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name fDisableAutoReconnect -Value 0 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name fInheritReconnectSame -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name fReconnectSame -Value 0 -Type DWord -Force
   ```

1. Eşzamanlı bağlantı sayısını sınırlayın:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name MaxInstanceCount -Value 4294967295 -Type DWord -Force
   ```

1. RDP dinleyicisine bağlı tüm otomatik olarak imzalanan sertifikaları kaldırın:

   ```powershell
   if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains 'SSLCertificateSHA1Hash')
   {
       Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name SSLCertificateSHA1Hash -Force
   }
   ```

   Bu kod, VM 'yi dağıtırken bağlanabilmenizi sağlar. Ayrıca, VM Azure 'da dağıtıldıktan sonra bu ayarları gözden geçirebilirsiniz.

1. VM bir etki alanının parçasıysa, önceki ayarların geri döndürülmemiş olduğundan emin olmak için aşağıdaki ilkeleri denetleyin.

    |                 Hedef                  |                                                                            İlke                                                                            |                           Değer                            |
    | ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------- |
    | RDP etkin                        | Bilgisayar Yapılandırması \ Windows ayarları \ Yönetimsel Şablonlar uzak masaüstü Hizmetleri\uzak Masaüstü oturumu ana bilgisayar \ bağlantılar         | Kullanıcıların Uzak Masaüstü kullanarak uzaktan bağlanmasına izin ver    |
    | NLA Grup İlkesi                      | Settings\Administrative Templates\uzak masaüstü Hizmetleri\uzak Masaüstü oturumu Host\Security                                                    | NLA kullanarak uzaktan erişim için Kullanıcı kimlik doğrulaması iste |
    | Etkin tut ayarları                   | Bilgisayar Yapılandırması \ Windows ayarları \ Yönetim Şablonları \ masaüstü Hizmetleri\uzak Masaüstü oturumu ana bilgisayar \ bağlantılar | Etkin tut bağlantı aralığını yapılandırma                   |
    | Yeniden bağlanma ayarları                    | Bilgisayar Yapılandırması \ Windows ayarları \ Yönetim Şablonları \ masaüstü Hizmetleri\uzak Masaüstü oturumu ana bilgisayar \ bağlantılar | Otomatik olarak yeniden bağlan                                    |
    | Sınırlı sayıda bağlantı ayarı | Bilgisayar Yapılandırması \ Windows ayarları \ Yönetim Şablonları \ masaüstü Hizmetleri\uzak Masaüstü oturumu ana bilgisayar \ bağlantılar | Bağlantı sayısını sınırla                                |

## <a name="configure-windows-firewall-rules"></a>Windows güvenlik duvarı kurallarını yapılandırma

1. Üç profil (etki alanı, standart ve genel) üzerinde Windows Güvenlik Duvarı 'nı açın:

   ```powershell
   Set-NetFirewallProfile -Profile Domain, Public, Private -Enabled True
   ```

1. Bu üç güvenlik duvarı profili (etki alanı, özel ve ortak) aracılığıyla WinRM 'ye izin vermek ve PowerShell uzak hizmetini etkinleştirmek için aşağıdaki örneği çalıştırın:

   ```powershell
   Enable-PSRemoting -Force
   Set-NetFirewallRule -DisplayName 'Windows Remote Management (HTTP-In)' -Enabled True
   ```

1. RDP trafiğine izin vermek için aşağıdaki güvenlik duvarı kurallarını etkinleştirin:

   ```powershell
   Set-NetFirewallRule -DisplayGroup 'Remote Desktop' -Enabled True
   ```

1. VM 'nin sanal ağ içindeki ping isteklerine yanıt verebilmesi için dosya ve yazıcı paylaşımı için kuralı etkinleştirin:

   ```powershell
   Set-NetFirewallRule -DisplayName 'File and Printer Sharing (Echo Request - ICMPv4-In)' -Enabled True
   ```

1. Azure platform ağı için bir kural oluşturun:

   ```powershell
   New-NetFirewallRule -DisplayName AzurePlatform -Direction Inbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow -EdgeTraversalPolicy Allow
   New-NetFirewallRule -DisplayName AzurePlatform -Direction Outbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow
   ```

1. VM bir etki alanının parçasıysa, önceki ayarların geri döndürülmemiş olduğundan emin olmak için aşağıdaki Azure AD ilkelerine bakın.

    |                 Hedef                 |                                                                         İlke                                                                          |                  Değer                  |
    | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------- |
    | Windows Güvenlik Duvarı profillerini etkinleştirme | Bilgisayar Yapılandırması \ Windows ayarları \ yönetim Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows güvenlik duvarı   | Tüm ağ bağlantılarını koru         |
    | RDP 'yi etkinleştirme                           | Bilgisayar Yapılandırması \ Windows ayarları \ yönetim Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows güvenlik duvarı   | Gelen uzak masaüstü özel durumlarına izin ver |
    |                                      | Bilgisayar Yapılandırması \ Windows ayarları \ yönetim Templates\Network\Network Connection\Windows Firewall\standart Profil\windows güvenlik duvarı | Gelen uzak masaüstü özel durumlarına izin ver |
    | ICMP v4 'yi etkinleştirme                       | Bilgisayar Yapılandırması \ Windows ayarları \ yönetim Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows güvenlik duvarı   | ICMP özel durumlarına izin ver                   |
    |                                      | Bilgisayar Yapılandırması \ Windows ayarları \ yönetim Templates\Network\Network Connection\Windows Firewall\standart Profil\windows güvenlik duvarı | ICMP özel durumlarına izin ver                   |

## <a name="verify-the-vm"></a>VM 'yi doğrulama

VM 'nin sağlıklı, güvenli ve RDP erişilebilir olduğundan emin olun:

1. Diskin sağlıklı ve tutarlı olduğundan emin olmak için, sonraki VM yeniden başlatıldığında diski kontrol edin:

   ```powershell
   chkdsk.exe /f
   ```

   Raporun temiz ve sağlıklı bir disk gösterdiğinizden emin olun.

1. Önyükleme Yapılandırma Verileri (BCD) ayarlarını ayarlayın.

   ```powershell
   cmd

   bcdedit.exe /set "{bootmgr}" integrityservices enable
   bcdedit.exe /set "{default}" device partition=C:
   bcdedit.exe /set "{default}" integrityservices enable
   bcdedit.exe /set "{default}" recoveryenabled Off
   bcdedit.exe /set "{default}" osdevice partition=C:
   bcdedit.exe /set "{default}" bootstatuspolicy IgnoreAllFailures

   #Enable Serial Console Feature
   bcdedit.exe /set "{bootmgr}" displaybootmenu yes
   bcdedit.exe /set "{bootmgr}" timeout 5
   bcdedit.exe /set "{bootmgr}" bootems yes
   bcdedit.exe /ems "{current}" ON
   bcdedit.exe /emssettings EMSPORT:1 EMSBAUDRATE:115200

   exit
   ```

1. Döküm günlüğü, Windows kilitlenme sorunlarını giderme konusunda yardımcı olabilir. Döküm günlüğü koleksiyonunu etkinleştir:

   ```powershell
   # Set up the guest OS to collect a kernel dump on an OS crash event
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name CrashDumpEnabled -Type DWord -Force -Value 2
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name DumpFile -Type ExpandString -Force -Value "%SystemRoot%\MEMORY.DMP"
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name NMICrashDump -Type DWord -Force -Value 1

   # Set up the guest OS to collect user mode dumps on a service crash event
   $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
   if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
   New-ItemProperty -Path $key -Name DumpFolder -Type ExpandString -Force -Value 'C:\CrashDumps'
   New-ItemProperty -Path $key -Name CrashCount -Type DWord -Force -Value 10
   New-ItemProperty -Path $key -Name DumpType -Type DWord -Force -Value 2
   Set-Service -Name WerSvc -StartupType Manual
   ```

1. Windows Yönetim Araçları (WMI) deposunun tutarlı olduğunu doğrulayın:

   ```powershell
   winmgmt.exe /verifyrepository
   ```

   Depo bozuksa, bkz. [WMI: depo bozulması veya Not](https://techcommunity.microsoft.com/t5/ask-the-performance-team/wmi-repository-corruption-or-not/ba-p/375484).

1. 3389 numaralı bağlantı noktasını kullanan başka bir uygulama olmadığından emin olun. Bu bağlantı noktası, Azure 'daki RDP hizmeti için kullanılır. VM 'de hangi bağlantı noktalarının kullanıldığını görmek için şunu çalıştırın `netstat.exe -anob` :

   ```powershell
   netstat.exe -anob
   ```

1. Bir etki alanı denetleyicisi olan bir Windows VHD 'yi karşıya yüklemek için:

   - Diski hazırlamak için [Bu ek adımları](https://support.microsoft.com/kb/2904015) izleyin.

   - VM 'yi DSRM 'de başlatmak zorunda kalmadan Dizin Hizmetleri geri yükleme modu (DSRM) parolasını öğrendiğinizden emin olun. Daha fazla bilgi için bkz. [DSRM parolası ayarlama](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc754363(v=ws.11)).

1. Yerleşik yönetici hesabı ve parolasını bildiğiniz için emin olun. Geçerli yerel yönetici parolasını sıfırlamak ve RDP bağlantısı aracılığıyla Windows 'da oturum açmak için bu hesabı kullandığınızdan emin olmak isteyebilirsiniz. Bu erişim izni, "Uzak Masaüstü Hizmetleri aracılığıyla oturum açmaya Izin ver" grup ilkesi nesnesi tarafından denetlenir. Bu nesneyi Yerel Grup İlkesi Düzenleyicisi görüntüleyin:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment`

1. RDP erişimini engellemediğinden emin olmak için aşağıdaki Azure AD ilkelerini denetleyin:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights
      Assignment\Deny access to this computer from the network`

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights
      Assignment\Deny log on through Remote Desktop Services`

1. Gerekli erişim hesaplarından hiçbirini kaldırmadığından emin olmak için aşağıdaki Azure AD ilkesini denetleyin:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment\Access this computer from the network`

   İlke aşağıdaki grupları listelemelidir:

   - Yöneticiler

   - Yedekleme İşleçleri

   - Herkes

   - Kullanıcılar

1. Windows 'un hala sağlıklı olduğundan ve RDP bağlantısı üzerinden erişilebildiğinden emin olmak için VM 'yi yeniden başlatın. Bu noktada, VM 'nin tamamen başlamasını sağlamak için yerel Hyper-V sunucunuzda bir VM oluşturmayı düşünün. Ardından, VM 'ye RDP aracılığıyla ulaşabildiğinizden emin olmak için test edin.

1. Tüm ek aktarım sürücüsü arabirimi (TDı) filtrelerini kaldırın. Örneğin, TCP paketlerini veya ek güvenlik duvarlarını çözümleyen yazılımları kaldırın.

1. Fiziksel bileşenlerle veya diğer sanallaştırma teknolojilerinde ilgili diğer üçüncü taraf yazılımı veya sürücüleri kaldırın.

### <a name="install-windows-updates"></a>Windows güncelleştirmelerini yükler

> [!NOTE]
> VM sağlama sırasında yanlışlıkla yeniden başlatmanın önüne geçmek için, tüm Windows Update yüklemelerinin tamamlanmasını ve bekleyen bir yeniden başlatma olmadığından emin olmanızı öneririz. Bunu yapmanın bir yolu, tüm Windows güncelleştirmelerini yüklemek ve Azure 'a geçişi gerçekleştirmeden önce VM 'yi yeniden başlatmak için kullanılır. </br><br>
>Ayrıca, işletim sisteminin (Sysprep) bir genelleştirmeyi yapmanız gerekiyorsa, Sysprep komutunu çalıştırmadan önce Windows 'u güncelleştirmeniz ve VM 'yi yeniden başlatmanız gerekir.

İdeal olarak, makinenin *düzeltme eki düzeyine* güncelleştirilmesini sağlamanız gerekir, bu mümkün değilse, aşağıdaki güncelleştirmelerin yüklendiğinden emin olun. En son güncelleştirmeleri almak için Windows Update geçmiş sayfalarına bakın: [Windows 10 ve Windows server 2019](https://support.microsoft.com/help/4000825), [Windows 8.1, ve Windows Server 2012 R2](https://support.microsoft.com/help/4009470) ve Windows 7 SP1 ve WINDOWS Server [2008 R2 SP1](https://support.microsoft.com/help/4009469).

<br />

|        Bileşen        |     İkili     | Windows 7 SP1, Windows Server 2008 R2 SP1 |       Windows 8, Windows Server 2012        | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 |      Windows 10 v1703      | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
| ----------------------- | -------------- | ----------------------------------------- | ------------------------------------------- | ----------------------------------- | ------------------------------------------- | -------------------------- | ------------------------------------------- | ------------------------------------------- |
| Depolama                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061          | -                                           | -                          | -                                           | -                                           |
|                         | storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.332             | -                                           | -                                           |
|                         | ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726          | 10.0.14393.1198 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614          | 10.0.14393.953 - KB4022715                  | -                          | -                                           | -                                           |
|                         | Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384          | -                                           | 10.0.15063.0               | -                                           | -                                           |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401-KB3000850          | 10.0.14393.953 - KB4022715                  | 10.0.15063.0               | -                                           | -                                           |
|                         | volmgr.sys     |                                           |                                             |                                     |                                             | 10.0.15063.0               | -                                           | -                                           |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | 10.0.15063.0               | -                                           | -                                           |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726          | 10.0.14393.1066 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726          | -                                           | -                          | -                                           | -                                           |
|                         | Mpio.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726          | 10.0.14393.1198 - KB4022715                 | -                          | -                                           | -                                           |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650          | 10.0.14393.2007 - KB4345418                 | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                  | -                                           |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614          | 10.0.14393.576 - KB4022715                  | -                          | -                                           | -                                           |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614          | 10.0.14393.206 - KB4022715                  | -                          | -                                           | -                                           |
| Ağ                 | netvsc.sys     | -                                         | -                                           | -                                   | 10.0.14393.1198 - KB4022715                 | 10.0.15063.250 - KB4020001 | -                                           | -                                           |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726          | 10.0.14393.479 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726          | 10.0.14393.953 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726          | 10.0.14393.953 - KB4022715                  | 10.0.15063.0               | -                                           | -                                           |
|                         | tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726          | 10.0.14393.251 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.138             | -                                           | -                                           |
| Çekirdek                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.483             | -                                           | -                                           |
| Uzak Masaüstü Hizmetleri | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726          | 10.0.14393.1198 - KB4022715                 | 10.0.15063.0               | -                                           | -                                           |
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415-KB3000850          | 10.0.14393.0-KB4022715                    | 10.0.15063.0               | -                                           | -                                           |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726          | 10.0.14393.594 - KB4022715                  | -                          | -                                           | -                                           |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
| Güvenlik                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                           | KB4012606                                   | KB4012606                  | -                                           | -                                           |
|                         |                |                                           | KB4012216                                   |                                     | KB4013198                                   | KB4013198                  | -                                           | -                                           |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                           | KB4013429                                   | KB4013429                  | -                                           | -                                           |
|                         |                |                                           | KB4012217                                   |                                     | KB4013429                                   | KB4013429                  | -                                           | -                                           |
|                         | CVE-2018-0886  | KB4103718                                 | KB4103730                                   | KB4103725                           | KB4103723                                   | KB4103731                  | KB4103727                                   | KB4103721                                   |
|                         |                | KB4103712                                 | KB4103726                                   | KB4103715                           |                                             |                            |                                             |                                             |

> [!NOTE]
> VM sağlama sırasında yanlışlıkla yeniden başlatmanın önüne geçmek için, tüm Windows Update yüklemelerinin bitmesini ve bekleyen güncelleştirme olmamasını güvence altına almayı öneririz. Bunu yapmanın bir yolu, komutu çalıştırmadan önce tüm olası Windows güncelleştirmelerini yüklemektir ve bir kez yeniden başlatılacak `sysprep.exe` .

## <a name="determine-when-to-use-sysprep"></a>Sysprep 'in ne zaman kullanılacağını belirleme

Sistem Hazırlama Aracı ( `sysprep.exe` ), bir Windows yüklemesini sıfırlamak için çalıştırabileceğiniz bir işlemdir.
Sysprep tüm kişisel verileri kaldırarak ve birçok bileşeni sıfırlayarak "kutudan çıkar" deneyimi sağlar.

Genellikle, `sysprep.exe` belirli bir yapılandırmaya sahip olan birkaç diğer VM 'yi dağıtabileceğiniz bir şablon oluşturmak için ' i çalıştırırsınız. Şablon *Genelleştirilmiş görüntü* olarak adlandırılır.

Bir diskten yalnızca bir VM oluşturmak için Sysprep 'i kullanmanız gerekmez. Bunun yerine, VM 'yi *özelleştirilmiş bir görüntüden* oluşturabilirsiniz. Özel bir diskten VM oluşturma hakkında daha fazla bilgi için, bkz:

- [Özelleştirilmiş diskten VM oluşturma](create-vm-specialized.md)
- [Özelleştirilmiş bir VHD diskinden VM oluşturma](./create-vm-specialized-portal.md)

Genelleştirilmiş bir görüntü oluşturmak için Sysprep 'i çalıştırmanız gerekir. Daha fazla bilgi için bkz. [Sysprep 'i kullanma: giriş](/previous-versions/windows/it-pro/windows-xp/bb457073(v=technet.10)).

Windows tabanlı bir bilgisayarda yüklü her rol veya uygulama Genelleştirilmiş görüntüleri destekler. Bu yordamı kullanmadan önce Sysprep 'in bilgisayarın rolünü desteklediğinden emin olun. Daha fazla bilgi için bkz. [sunucu rolleri Için Sysprep desteği](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles).

Özellikle, Sysprep yürütmeden önce sürücülerin şifresinin tamamen çözülmesi gerekir. VM 'niz üzerinde şifrelemeyi etkinleştirdiyseniz, Sysprep 'ı çalıştırmadan önce devre dışı bırakın.


### <a name="generalize-a-vhd"></a>Bir VHD 'YI genelleştirin

>[!NOTE]
> `sysprep.exe`Aşağıdaki adımlarda çalıştırdıktan sonra, VM 'yi kapatın. Azure 'da bir görüntü oluşturana kadar tekrar açın.

1. Windows VM 'de oturum açın.
1. Yönetici olarak bir PowerShell oturumu çalıştırın.
1. Panther dizinini (C:\Windows\Panther) silin.
1. Dizini olarak değiştirin `%windir%\system32\sysprep` . Ardından `sysprep.exe` komutunu çalıştırın.
1. **Sistem Hazırlama Aracı** iletişim kutusunda, **sistem kutudan çıkar deneyimi (OOBE)** seçeneğini belirleyin ve **generalize** onay kutusunun seçildiğinden emin olun.

    ![Sistem Hazırlama Aracı](media/prepare-for-upload-vhd-image/syspre.png)
1. **Kapalı seçenekleri**' nde, **kapatır**' ı seçin.
1. **Tamam**’ı seçin.
1. Sysprep tamamlandığında, VM 'yi kapatın. VM 'yi kapatmak için **yeniden başlatma** kullanmayın.

Artık VHD karşıya yüklenmeye hazırdır. Genelleştirilmiş bir diskten VM oluşturma hakkında daha fazla bilgi için bkz. [Genelleştirilmiş BIR VHD 'Yi karşıya yükleme ve Azure 'da yeni BIR VM oluşturmak için kullanma](/previous-versions/azure/virtual-machines/windows/sa-upload-generalized).

>[!NOTE]
> Özel bir *unattend.xml* dosyası desteklenmez. , [Microsoft-Windows-Shell-Setup](/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) seçeneklerini Azure sağlama aracısının kullandığı *unattend.xml* dosyasına eklemek için yalnızca sınırlı destek sağlayan **additionalunattendcontent** özelliğini destekliyoruz. FirstLogonCommand ve LogonCommands eklemek için, örneğin, [Additionalunattendcontent](/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent) ' i kullanabilirsiniz. Daha fazla bilgi için bkz. [Additionalunattendcontent FirstLogonCommands örneği](https://github.com/Azure/azure-quickstart-templates/issues/1407).

## <a name="convert-the-virtual-disk-to-a-fixed-size-vhd"></a>Sanal diski sabit boyutlu bir VHD 'ye Dönüştür

Sanal diskinizi Azure için gereken biçime dönüştürmek ve yeniden boyutlandırmak için bu bölümdeki yöntemlerden birini kullanın:

1. Sanal disk dönüştürmeyi veya yeniden boyutlandırma işlemini çalıştırmadan önce VM 'yi yedekleyin.

1. Windows VHD 'nin yerel sunucuda doğru şekilde çalıştığından emin olun. Azure 'a dönüştürmeyi veya karşıya yüklemeyi denemeden önce VM 'nin içindeki hataları çözün.

1. Sanal diski fixed türüne dönüştürün.

1. Sanal diski Azure gereksinimlerini karşılayacak şekilde yeniden boyutlandırın:

   1. Azure 'daki diskler, 1 MiB hizalı bir sanal boyuta sahip olmalıdır. VHD 'niz 1 MIB 'nin bir kesri ise, diski 1 MiB 'nin katlarından birine yeniden boyutlandırmanız gerekir. Bir MIB 'nin kesirleri olan diskler karşıya yüklenen VHD 'den görüntü oluştururken hatalara neden olur. Boyutunu doğrulamak için, PowerShell [Get-VHD](/powershell/module/hyper-v/get-vhd) cmdlet 'ini kullanarak, Azure 'Da 1 MiB ve "Boyut" ve VHD altbilgisi için 512 bayta eşit olacak "Boyut" ile aynı olacak "Boyut" i gösteren "Boyut" ı kullanabilirsiniz.
   
   1. 1. nesil VM 'ye sahip işletim sistemi VHD 'SI için izin verilen en büyük boyut 2.048 GiB 'dir (2 TiB), 
   1. Bir veri diskinin en büyük boyutu 32.767 GiB 'dir (32 TiB).

> [!NOTE]
> - Sabit bir diske dönüştürdükten sonra bir Windows işletim sistemi diski hazırlıyorsanız, gerekirse yeniden boyutlandırıyorsanız, diski kullanan bir VM oluşturun. Sanal makineyi başlatıp oturum açın ve bu makaledeki bölümlerle devam ederek karşıya yüklemeyi hazırlamayı tamamlayın.  
> - Bir veri diski hazırlıyorsanız, bu bölüm ile durabilir ve diskinizi karşıya yüklemeye devam edebilirsiniz.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Hyper-V Yöneticisi 'Ni kullanarak diski dönüştürme

1. Hyper-V Yöneticisi 'Ni açın ve sol tarafta yerel bilgisayarınızı seçin. Bilgisayar listesinin üzerindeki menüde, **işlem**  >  **Düzenle**' yi seçin.
1. **Sanal sabit diski bul** sayfasında, sanal diskinizi seçin.
1. **Eylem Seç** sayfasında, Sonrakini **Dönüştür**' ü seçin  >  .
1. VHDX 'ten dönüştürmek için, sonraki **VHD**'yi seçin  >  .
1. Dinamik olarak genişleyen bir diskten dönüştürmek için, sonraki **sabit boyut**' u seçin  >  .
1. Yeni VHD dosyasını kaydetmek için bir yol bulun ve seçin.
1. **Son**'u seçin.

### <a name="use-powershell-to-convert-the-disk"></a>Diski dönüştürmek için PowerShell 'i kullanma

PowerShell 'de [Convert-VHD](/powershell/module/hyper-v/convert-vhd) cmdlet 'ini kullanarak bir sanal diski dönüştürebilirsiniz. Bu cmdlet 'i yükleme hakkında bilgiye ihtiyacınız varsa bkz. [Hyper-V rolünü yükleme](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

Aşağıdaki örnek, diski VHDX 'ten VHD 'ye dönüştürür. Ayrıca, diski dinamik olarak genişleyen bir diskten sabit boyutlu bir diske dönüştürür.

```powershell
Convert-VHD -Path C:\test\MyVM.vhdx -DestinationPath C:\test\MyNewVM.vhd -VHDType Fixed
```

Bu örnekte, **yol** değerini dönüştürmek istediğiniz sanal sabit disk ile değiştirin. **DestinationPath** değerini, dönüştürülmüş diskin yeni yolu ve adıyla değiştirin.

### <a name="use-hyper-v-manager-to-resize-the-disk"></a>Diski yeniden boyutlandırmak için Hyper-V Yöneticisi 'Ni kullanma

1. Hyper-V Yöneticisi 'Ni açın ve sol tarafta yerel bilgisayarınızı seçin. Bilgisayar listesinin üzerindeki menüde, **işlem**  >  **Düzenle**' yi seçin.
1. **Sanal sabit diski bul** sayfasında, sanal diskinizi seçin.
1. **Eylem Seç** sayfasında İleri **Genişlet**' i seçin  >  .
1. **Sanal sabit diski bul** sayfasında, gib > yeni boyutunu **daha sonra** girin.
1. **Son**'u seçin.

### <a name="use-powershell-to-resize-the-disk"></a>Diski yeniden boyutlandırmak için PowerShell 'i kullanma

PowerShell 'de [Resize-VHD](/powershell/module/hyper-v/resize-vhd) cmdlet 'ini kullanarak bir sanal diski yeniden boyutlandırabilirsiniz. Bu cmdlet 'i yükleme hakkında bilgiye ihtiyacınız varsa bkz. [Hyper-V rolünü yükleme](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

Aşağıdaki örnek, Azure hizalama gereksinimini karşılamak için diski 100,5 MIB 'den 101 MiB 'ye yeniden boyutlandırır.

```powershell
Resize-VHD -Path C:\test\MyNewVM.vhd -SizeBytes 105906176
```

Bu örnekte **yolun** değerini, yeniden boyutlandırmak istediğiniz sanal sabit disk yolu ile değiştirin. **SizeBytes** değerini, disk için bayt cinsinden yeni boyutla değiştirin.

### <a name="convert-from-vmware-vmdk-disk-format"></a>VMware VMDK disk biçiminden Dönüştür

[VMDK dosya biçiminde](https://en.wikipedia.org/wiki/VMDK)bir Windows sanal makine görüntünüz varsa, VMDK 'yi dönüştürmek ve Azure 'a yüklemek Için [Azure geçişi](../../migrate/server-migrate-overview.md) ' ni kullanabilirsiniz.

## <a name="complete-the-recommended-configurations"></a>Önerilen konfigürasyonları doldurun

Aşağıdaki ayarlar, VHD karşıya yüklemeyi etkilemez. Ancak, bunları yapılandırdığınızdan kesinlikle önerilir.

- [Azure sanal makine Aracısı](https://go.microsoft.com/fwlink/?LinkID=394789)'nı yükler. Daha sonra, VM uzantılarını etkinleştirebilirsiniz. VM uzantıları, VM 'leriniz ile kullanmak isteyebileceğiniz kritik işlevlerin çoğunu uygular. Örneğin, parola sıfırlama veya RDP 'yi yapılandırma gibi uzantılara ihtiyaç duyarsınız. Daha fazla bilgi için bkz. [Azure sanal makine aracısına genel bakış](../extensions/agent-windows.md).
- Azure 'da VM 'yi oluşturduktan sonra, performansı artırmak için sayfa dosyasını zamana bağlı *sürücü birimine* yerleştirmeniz önerilir. Dosya yerleşimini aşağıdaki gibi ayarlayabilirsiniz:

  ```powershell
  Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name PagingFiles -Value 'D:\pagefile.sys' -Type MultiString -Force
  ```

  SANAL makineye bir veri diski eklenmişse, zamana bağlı sürücü biriminin harfi genellikle *D* olur. Ayarlarınıza ve kullanılabilir sürücü sayısına bağlı olarak bu atama farklı olabilir.

  - Virüsten koruma yazılımı tarafından sağlanmakta olabilecek komut dosyası engelleyicilerini devre dışı bırakmayı öneririz. Bu kişiler, yansımasından yeni bir VM dağıtırken yürütülen Windows sağlama Aracısı betikleri kesintiye uğratabilecek ve engelleyebilirler.

## <a name="next-steps"></a>Sonraki adımlar

- [Kaynak Yöneticisi dağıtımları için Azure 'a bir Windows VM görüntüsü yükleme](upload-generalized-managed.md)
- [Azure Windows VM etkinleştirme sorunlarını giderme](/troubleshoot/azure/virtual-machines/troubleshoot-activation-problems)
