---
title: Bir Windows VHD 'yi Azure 'a yüklemek üzere hazırlama
description: Bir Windows VHD veya VHDX 'i Azure 'a yüklemek için nasıl hazırlayacağınızı öğrenin
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: 4b6fea63cd56ddee7eaeaed50a74a01cc8549f0a
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74032938"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Azure 'a yüklemek için bir Windows VHD veya VHDX hazırlama

Şirket içinden Azure 'a bir Windows sanal makinesi (VM) yüklemeden önce sanal sabit diski (VHD veya VHDX) hazırlamanız gerekir. Azure, VHD dosya biçimindeki ve sabit boyutlu diski olan 1. nesil ve 2. nesil VM 'Leri destekler. VHD için izin verilen en büyük boyut 1.023 GB 'dir. 

1\. nesil bir sanal makinede, bir VHDX dosya sistemini VHD 'ye dönüştürebilirsiniz. Ayrıca, dinamik olarak genişleyen bir diski sabit boyutlu bir diske dönüştürebilirsiniz. Ancak bir sanal makinenin neslini değiştiremezsiniz. Daha fazla bilgi için bkz. [Hyper-V ' d a 1 veya 2 sanal makine oluşturma](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) ve [2. nesil VM 'ler için Azure Desteği (Önizleme)](generation-2.md).

Azure VM 'Leri için destek ilkesi hakkında daha fazla bilgi için bkz. [Azure VM 'ler Için Microsoft sunucu yazılımı desteği](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!NOTE]
> Bu makaledeki yönergeler için geçerlidir:
>1. Windows Server 2008 R2 ve üzeri Windows Server işletim sistemlerinin 64 bitlik sürümü. Azure 'da 32 bitlik bir işletim sistemi çalıştırma hakkında bilgi için bkz. [Azure VM 'lerde 32 bit işletim sistemleri Için destek](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).
>2. Azure Site Recovery veya Azure geçişi gibi iş yükünü geçirmek için herhangi bir olağanüstü durum kurtarma aracı kullanılacaksa, bu işlemin tamamlanması ve ardından Konuk işletim sistemi, geçişten önce görüntüyü hazırlamak için gereklidir.

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>Sanal diski sabit boyuta ve VHD 'ye Dönüştür

Sanal diskinizi Azure için gereken biçime dönüştürmeniz gerekiyorsa, bu bölümdeki yöntemlerden birini kullanın:

1. Sanal disk dönüştürme işlemini çalıştırmadan önce VM 'yi yedekleyin.

1. Windows VHD 'nin yerel sunucuda doğru şekilde çalıştığından emin olun. Azure 'a dönüştürmeyi veya karşıya yüklemeyi denemeden önce VM 'nin içindeki hataları çözün.

1. VHD 'nin boyutuyla ilgili:

   1. Azure 'daki tüm VHD 'ler, 1 MB 'a hizalanmış bir sanal boyuta sahip olmalıdır. Bir ham diskten VHD 'ye dönüştürürken, dönüştürmeden önce ham disk boyutunun 1 MB 'ın katı olduğundan emin olmanız gerekir. Bir megabayın kesirleri karşıya yüklenen VHD 'den görüntü oluştururken hatalara neden olur.

   2. İşletim sistemi VHD 'SI için izin verilen en büyük boyut 2TB 'dir.


Diski dönüştürdükten sonra, diski kullanan bir VM oluşturun. Karşıya yüklemek için hazırlama işini başlatmak üzere VM 'de başlatın ve oturum açın.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Hyper-V Yöneticisi 'Ni kullanarak diski dönüştürme 
1. Hyper-V Yöneticisi 'Ni açın ve sol tarafta yerel bilgisayarınızı seçin. Bilgisayar listesinin üzerindeki menüde, **disk düzenle** > **eylem** ' i seçin.
2. **Sanal sabit diski bul** sayfasında, sanal diskinizi seçin.
3. **Eylem Seç** sayfasında, **İleri** > **Dönüştür** ' ü seçin.
4. VHDX 'ten dönüştürmeniz gerekiyorsa, **daha sonra** **VHD** > ' yi seçin.
5. Dinamik olarak genişleyen bir diskten dönüştürmeniz gerekiyorsa, **İleri**' > **sabit boyut** ' u seçin.
6. Yeni VHD dosyasını kaydetmek için bir yol bulun ve seçin.
7. **Son**’u seçin.

> [!NOTE]
> Bu makaledeki komutları çalıştırmak için yükseltilmiş bir PowerShell oturumu kullanın.

### <a name="use-powershell-to-convert-the-disk"></a>Diski dönüştürmek için PowerShell 'i kullanma 
Windows PowerShell 'de [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) komutunu kullanarak bir sanal diski dönüştürebilirsiniz. PowerShell 'i başlattığınızda **yönetici olarak çalıştır** ' ı seçin. 

Aşağıdaki örnek komut, diski VHDX 'ten VHD 'ye dönüştürür. Bu komut Ayrıca, diski dinamik olarak genişleyen bir diskten sabit boyutlu bir diske dönüştürür.

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

Bu komutta `-Path` değerini, dönüştürmek istediğiniz sanal sabit disk yoluyla değiştirin. `-DestinationPath` değerini, dönüştürülmüş diskin yeni yolu ve adıyla değiştirin.

### <a name="convert-from-vmware-vmdk-disk-format"></a>VMware VMDK disk biçiminden Dönüştür
[VMDK dosya biçiminde](https://en.wikipedia.org/wiki/VMDK)bir Windows sanal makine görüntünüz varsa, vhd biçimine dönüştürmek Için [Microsoft Virtual Machine dönüştürücüsünü](https://www.microsoft.com/download/details.aspx?id=42497) kullanın. Daha fazla bilgi için bkz. [bir VMware VMDK 'Yi Hyper-V VHD 'ye dönüştürme](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Azure için Windows yapılandırmasını ayarlama

Azure 'a yüklemeyi planladığınız sanal makinede, [yükseltilmiş bir komut istemi penceresinden](https://technet.microsoft.com/library/cc947813.aspx)aşağıdaki komutları çalıştırın:

1. Yönlendirme tablosundaki tüm statik kalıcı rotayı kaldırın:
   
   * Yol tablosunu görüntülemek için komut isteminde `route print` komutunu çalıştırın.
   * `Persistence Routes` bölümüne bakın. Kalıcı bir yol varsa kaldırmak için `route delete` komutunu kullanın.
2. WinHTTP proxy 'sini kaldır:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    VM 'nin belirli bir ara sunucu ile çalışması gerekiyorsa, sanal makinenin Azure 'a bağlanabilmesi için Azure IP adresine ([168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)) bir ara sunucu özel durumu ekleyin:
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. Disk SAN ilkesini [`Onlineall`](https://technet.microsoft.com/library/gg252636.aspx)olarak ayarlayın:
   
    ```PowerShell
    diskpart 
    ```
    Komut istemi Aç penceresinde aşağıdaki komutları yazın:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Windows için Eşgüdümlü Evrensel Saat (UTC) süresini ayarlayın. Ayrıca, Windows Saat hizmeti 'nin (`w32time`) başlangıç türünü `Automatic`olarak ayarlayın:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -Name "RealTimeIsUniversal" -Value 1 -Type DWord -Force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Güç profilini yüksek performans olarak ayarlayın:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. `TEMP` ve `TMP` ortam değişkenlerinin varsayılan değerlerine ayarlandığından emin olun:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
    ```

## <a name="check-the-windows-services"></a>Windows hizmetlerini denetleyin
Aşağıdaki Windows hizmetlerinin her birinin Windows varsayılan değerlerine ayarlandığından emin olun. Bu hizmetler VM bağlantısı sağlamak için ayarlanması gereken en düşük gereksinimdir. Başlangıç ayarlarını sıfırlamak için aşağıdaki komutları çalıştırın:
   
```PowerShell
Get-Service -Name bfe | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name dhcp | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name dnscache | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name IKEEXT | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name iphlpsvc | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name netlogon | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name netman | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name nsi | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name TermService | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name MpsSvc | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name RemoteRegistry | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
```

## <a name="update-remote-desktop-registry-settings"></a>Uzak Masaüstü kayıt defteri ayarlarını Güncelleştir
Aşağıdaki ayarların uzaktan erişim için doğru yapılandırıldığından emin olun:

>[!NOTE] 
>`Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <object name> -Value <value>`çalıştırdığınızda bir hata iletisi alabilirsiniz. Bu iletiyi güvenle yoksayabilirsiniz. Bu, yalnızca etki alanının bu yapılandırmayı grup ilkesi nesne aracılığıyla göndermediği anlamına gelir.

1. Uzak Masaüstü Protokolü (RDP) etkin:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force
    ```
   
2. RDP bağlantı noktası doğru şekilde ayarlanır. Varsayılan bağlantı noktası 3389 ' dir:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "PortNumber" -Value 3389 -Type DWord -Force
    ```
    Bir VM dağıtırken, varsayılan kurallar 3389 numaralı bağlantı noktasına göre oluşturulur. Bağlantı noktası numarasını değiştirmek istiyorsanız, VM Azure 'da dağıtıldıktan sonra bunu yapın.

3. Dinleyici her ağ arabiriminde dinliyor:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "LanAdapter" -Value 0 -Type DWord -Force
   ```
4. RDP bağlantıları için ağ düzeyinde kimlik doğrulaması (NLA) modunu yapılandırın:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "UserAuthentication" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SecurityLayer" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -Force
     ```

5. Etkin tut değerini ayarlayın:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveEnable" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveInterval" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "KeepAliveTimeout" -Value 1 -Type DWord -Force
    ```
6. Maları
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDisableAutoReconnect" -Value 0 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fInheritReconnectSame" -Value 1 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fReconnectSame" -Value 0 -Type DWord -Force
    ```
7. Eşzamanlı bağlantı sayısını sınırlayın:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "MaxInstanceCount" -Value 4294967295 -Type DWord -Force
    ```
8. RDP dinleyicisine bağlı tüm otomatik olarak imzalanan sertifikaları kaldırın:
    
    ```PowerShell
    if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains "SSLCertificateSHA1Hash")
    {
        Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SSLCertificateSHA1Hash" -Force
    }
    ```
    Bu kod, VM 'yi dağıtırken başlangıcında bağlanabilmenizi sağlar. Bu işlemi daha sonra gözden geçirmeniz gerekiyorsa, sanal makine Azure 'da dağıtıldıktan sonra bunu yapabilirsiniz.

9. VM bir etki alanının parçasıysa, eski ayarların geri döndürülmemiş olduğundan emin olmak için aşağıdaki ilkeleri denetleyin. 
    
    | Hedef                                     | İlke                                                                                                                                                       | Değer                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | RDP etkin                           | Bilgisayar Yapılandırması \ Windows ayarları \ Yönetimsel Şablonlar uzak masaüstü Hizmetleri\uzak Masaüstü oturumu ana bilgisayar \ bağlantılar         | Kullanıcıların Uzak Masaüstü kullanarak uzaktan bağlanmasına izin ver                                  |
    | NLA Grup İlkesi                         | Settings\Administrative Templates\uzak masaüstü Hizmetleri\uzak Masaüstü oturumu Host\Security                                                    | NLA kullanarak uzaktan erişim için Kullanıcı kimlik doğrulaması iste |
    | Etkin tut ayarları                      | Bilgisayar Yapılandırması \ Windows ayarları \ Yönetim Şablonları \ masaüstü Hizmetleri\uzak Masaüstü oturumu ana bilgisayar \ bağlantılar | Etkin tut bağlantı aralığını yapılandırma                                                 |
    | Yeniden bağlanma ayarları                       | Bilgisayar Yapılandırması \ Windows ayarları \ Yönetim Şablonları \ masaüstü Hizmetleri\uzak Masaüstü oturumu ana bilgisayar \ bağlantılar | Otomatik olarak yeniden bağlan                                                                   |
    | Sınırlı sayıda bağlantı ayarı | Bilgisayar Yapılandırması \ Windows ayarları \ Yönetim Şablonları \ masaüstü Hizmetleri\uzak Masaüstü oturumu ana bilgisayar \ bağlantılar | Bağlantı sayısını sınırla                                                              |

## <a name="configure-windows-firewall-rules"></a>Windows güvenlik duvarı kurallarını yapılandırma
1. Üç profil (etki alanı, standart ve genel) üzerinde Windows Güvenlik Duvarı 'nı açın:

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. PowerShell 'de, üç güvenlik duvarı profili (etki alanı, özel ve ortak) aracılığıyla WinRM 'ye izin vermek ve PowerShell uzak hizmetini etkinleştirmek için aşağıdaki komutu çalıştırın:
   
   ```PowerShell
    Enable-PSRemoting -Force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. RDP trafiğine izin vermek için aşağıdaki güvenlik duvarı kurallarını etkinleştirin:

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. Sanal ağ içinde bir ping komutuna yanıt verebilmesi için dosya ve yazıcı paylaşımı kuralını etkinleştirin:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. VM bir etki alanının parçasıysa, eski ayarların geri döndürülmemiş olduğundan emin olmak için aşağıdaki Azure AD ilkelerine bakın. 

    | Hedef                                 | İlke                                                                                                                                                  | Değer                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Windows Güvenlik Duvarı profillerini etkinleştirme | Bilgisayar Yapılandırması \ Windows ayarları \ yönetim Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows güvenlik duvarı   | Tüm ağ bağlantılarını koru         |
    | RDP 'yi etkinleştirme                           | Bilgisayar Yapılandırması \ Windows ayarları \ yönetim Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows güvenlik duvarı   | Gelen uzak masaüstü özel durumlarına izin ver |
    |                                      | Bilgisayar Yapılandırması \ Windows ayarları \ yönetim Templates\Network\Network Connection\Windows Firewall\standart Profil\windows güvenlik duvarı | Gelen uzak masaüstü özel durumlarına izin ver |
    | ICMP v4 'yi etkinleştirme                       | Bilgisayar Yapılandırması \ Windows ayarları \ yönetim Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows güvenlik duvarı   | ICMP özel durumlarına izin ver                   |
    |                                      | Bilgisayar Yapılandırması \ Windows ayarları \ yönetim Templates\Network\Network Connection\Windows Firewall\standart Profil\windows güvenlik duvarı | ICMP özel durumlarına izin ver                   |

## <a name="verify-the-vm"></a>VM 'yi doğrulama 

VM 'nin sağlıklı, güvenli ve RDP erişilebilir olduğundan emin olun: 

1. Diskin sağlıklı ve tutarlı olduğundan emin olmak için, sonraki VM yeniden başlatıldığında diski kontrol edin:

    ```PowerShell
    Chkdsk /f
    ```
    Raporun temiz ve sağlıklı bir disk gösterdiğinizden emin olun.

2. Önyükleme Yapılandırma Verileri (BCD) ayarlarını ayarlayın. 

    > [!NOTE]
    > Bu komutları çalıştırmak için yükseltilmiş bir PowerShell penceresi kullanın.
   
   ```powershell
    bcdedit /set "{bootmgr}" integrityservices enable
    bcdedit /set "{default}" device partition=C:
    bcdedit /set "{default}" integrityservices enable
    bcdedit /set "{default}" recoveryenabled Off
    bcdedit /set "{default}" osdevice partition=C:
    bcdedit /set "{default}" bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set "{bootmgr}" displaybootmenu yes
    bcdedit /set "{bootmgr}" timeout 5
    bcdedit /set "{bootmgr}" bootems yes
    bcdedit /ems "{current}" ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```
3. Döküm günlüğü, Windows kilitlenme sorunlarını giderme konusunda yardımcı olabilir. Döküm günlüğü koleksiyonunu etkinleştir:

    ```powershell
    # Set up the guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name CrashDumpEnabled -Type DWord -Force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name DumpFile -Type ExpandString -Force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name NMICrashDump -Type DWord -Force -Value 1

    # Set up the guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -Name DumpFolder -Type ExpandString -Force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -Name CrashCount -Type DWord -Force -Value 10
    New-ItemProperty -Path $key -Name DumpType -Type DWord -Force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Windows Yönetim Araçları (WMI) deposunun tutarlı olduğunu doğrulayın:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Depo bozuksa, bkz. [WMI: depo bozulması veya Not](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

5. 3389 numaralı bağlantı noktasını kullanan başka bir uygulama olmadığından emin olun. Bu bağlantı noktası, Azure 'daki RDP hizmeti için kullanılır. VM 'de hangi bağlantı noktalarının kullanıldığını görmek için `netstat -anob`çalıştırın:

    ```PowerShell
    netstat -anob
    ```

6. Bir etki alanı denetleyicisi olan bir Windows VHD 'yi karşıya yüklemek için:

   * Diski hazırlamak için [Bu ek adımları](https://support.microsoft.com/kb/2904015) izleyin.

   * VM 'yi bir noktada DSRM 'de başlatmanız gerekebilmeniz durumunda Dizin Hizmetleri geri yükleme modu (DSRM) parolasını öğrendiğinizden emin olun. Daha fazla bilgi için bkz. [DSRM parolası ayarlama](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

7. Yerleşik yönetici hesabı ve parolasını bildiğiniz için emin olun. Geçerli yerel yönetici parolasını sıfırlamak ve RDP bağlantısı aracılığıyla Windows 'da oturum açmak için bu hesabı kullandığınızdan emin olmak isteyebilirsiniz. Bu erişim izni, "Uzak Masaüstü Hizmetleri aracılığıyla oturum açmaya Izin ver" grup ilkesi nesnesi tarafından denetlenir. Bu nesneyi buradan Yerel Grup İlkesi Düzenleyicisi görüntüleyin:

    Bilgisayar Yapılandırması \ güvenlik ayarları \ yerel ilkeler \ Kullanıcı hakları ataması

8. RDP erişiminizi RDP veya ağ üzerinden engellemediğinizden emin olmak için aşağıdaki Azure AD ilkelerini denetleyin:

    - Bilgisayar Yapılandırması \ güvenlik ayarları \ yerel ilkeler \ Kullanıcı hakları ataması bu bilgisayara ağ üzerinden erişimi engelle

    - Bilgisayar Yapılandırması \ güvenlik ayarları \ yerel ilkeler \ Kullanıcı hakları ataması Uzak Masaüstü Hizmetleri aracılığıyla oturum açmaya izin verme


9. Gerekli erişim hesaplarından hiçbirini kaldırdığınızdan emin olmak için aşağıdaki Azure AD ilkesini denetleyin:

   - Bilgisayar Yapılandırması \ güvenlik ayarları \ yerel ilkeler \ Kullanıcı hakları bu bilgisayara ağ üzerinden erişemez

   İlke aşağıdaki grupları listelemelidir:

   - Yöneticiler

   - Yedekleme Işleçleri

   - Anlamasına

   - Kullanıcılar

10. Windows 'un hala sağlıklı olduğundan ve RDP bağlantısı üzerinden erişilebildiğinden emin olmak için VM 'yi yeniden başlatın. Bu noktada, VM 'nin tamamen başlamasını sağlamak için yerel Hyper-V ' d e bir VM oluşturmak isteyebilirsiniz. Ardından, VM 'ye RDP aracılığıyla ulaşabildiğinizden emin olmak için test edin.

11. Tüm ek aktarım sürücüsü arabirimi (TDı) filtrelerini kaldırın. Örneğin, TCP paketlerini veya ek güvenlik duvarlarını çözümleyen yazılımları kaldırın. Bu işlemi daha sonra gözden geçirmeniz gerekiyorsa, sanal makine Azure 'da dağıtıldıktan sonra bunu yapabilirsiniz.

12. Fiziksel bileşenlerle veya diğer sanallaştırma teknolojilerinde ilgili diğer üçüncü taraf yazılımı veya sürücüleri kaldırın.

### <a name="install-windows-updates"></a>Windows güncelleştirmelerini yükler
İdeal olarak, makineyi *Düzeltme Eki düzeyinde*güncel tutmanız gerekir. Bu mümkün değilse, aşağıdaki güncelleştirmelerin yüklü olduğundan emin olun:

| Bileşen               | İkili         | Windows 7 SP1, Windows Server 2008 R2 SP1 | Windows 8, Windows Server 2012               | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 | Windows 10 v1703    | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Depolama                 | disk. sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | Storport. sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | NTFS. sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | IoLogMsg. dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Classpnp. sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614         | 10.0.14393.953 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap. sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | partmgr. sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401-KB3000850         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | volmgr. sys     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx. sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | MSiSCSI. sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726         | 10.0.14393.1066 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | MSDSM. sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | MPIO. sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726         | 10.0.14393.1198 - KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl. sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650         | 10.0.14393.2007 - KB4345418                             | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                      | -                                               |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614         | 10.0.14393.576 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Fveapibase. dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614         | 10.0.14393.206 - KB4022715                              | -                          | -                                               | -                                               |
| Ağ                 | netvsc. sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 - KB4022715                             | 10.0.15063.250 - KB4020001 | -                                               | -                                               |
|                         | Mrxsmb10. sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726         | 10.0.14393.479 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20. sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb. sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | Tcpip. sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | http. sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726         | 10.0.14393.251 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | VmSwitch. sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| Çekirdek                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| Uzak Masaüstü Hizmetleri | rdpcorets. dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | termsrv. dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415-KB3000850         | 10.0.14393.0-KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | TermDD. sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | tıklarında     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726         | 10.0.14393.594 - KB4022715                              | -                          | -                                               | -                                               |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | rdpwd. sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| Güvenlik                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
> [!NOTE]
> VM sağlama sırasında yanlışlıkla yeniden başlatmanın önüne geçmek için, tüm Windows Update yüklemelerinin bitmesini ve bekleyen güncelleştirme olmamasını güvence altına almayı öneririz. Bunu yapmanın bir yolu, Sysprep komutunu çalıştırmadan önce tüm olası Windows güncelleştirmelerini yüklemektir ve bir kez yeniden başlatılacak.

### Sysprep 'in ne zaman kullanılacağını belirleme<a id="step23"></a>    

Sistem Hazırlama Aracı (Sysprep), bir Windows yüklemesini sıfırlamak için çalıştırabileceğiniz bir işlemdir. Sysprep tüm kişisel verileri kaldırarak ve birçok bileşeni sıfırlayarak "kutudan çıkar" deneyimi sağlar. 

Genellikle, belirli bir yapılandırmaya sahip olan birkaç diğer VM 'yi dağıtabileceğiniz bir şablon oluşturmak için Sysprep komutunu çalıştırın. Şablon *Genelleştirilmiş görüntü*olarak adlandırılır.

Bir diskten yalnızca bir VM oluşturmak istiyorsanız Sysprep 'i kullanmanız gerekmez. Bunun yerine, VM 'yi *özelleştirilmiş bir görüntüden*oluşturabilirsiniz. Özel bir diskten VM oluşturma hakkında daha fazla bilgi için, bkz:

- [Özel bir diskten VM oluşturma](create-vm-specialized.md)
- [Özelleştirilmiş bir VHD diskinden VM oluşturma](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Genelleştirilmiş bir görüntü oluşturmak istiyorsanız Sysprep 'i çalıştırmanız gerekir. Daha fazla bilgi için bkz. [Sysprep 'i kullanma: giriş](https://technet.microsoft.com/library/bb457073.aspx). 

Windows tabanlı bir bilgisayarda yüklü her rol veya uygulama Genelleştirilmiş görüntüleri destekler. Bu yordamı çalıştırmadan önce, Sysprep 'in bilgisayarın rolünü desteklediğinden emin olun. Daha fazla bilgi için bkz. [sunucu rolleri Için Sysprep desteği](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="generalize-a-vhd"></a>Bir VHD 'YI genelleştirin

>[!NOTE]
> Aşağıdaki adımlarda `sysprep.exe` çalıştırdıktan sonra, VM 'yi kapatın. Azure 'da bir görüntü oluşturana kadar tekrar açın.

1. Windows VM 'de oturum açın.
1. **Komut istemi** 'ni yönetici olarak çalıştırın. 
1. Dizini `%windir%\system32\sysprep`değiştirin. Ardından `sysprep.exe` komutunu çalıştırın.
1. **Sistem Hazırlama Aracı** iletişim kutusunda  **Sistem İlk Çalıştırma Deneyimi (OOBE) Moduna Gir**'i seçin ve **Genelleştir** onay kutusunun seçili olduğundan emin olun.

    ![Sistem Hazırlama Aracı](media/prepare-for-upload-vhd-image/syspre.png)
1. **Kapalı seçenekleri**' nde, **kapatır**' ı seçin.
1. **Tamam**’ı seçin.
1. Sysprep tamamlandığında, VM 'yi kapatın. VM 'yi kapatmak için **yeniden başlatma** kullanmayın.

Artık VHD karşıya yüklenmeye hazırdır. Genelleştirilmiş bir diskten VM oluşturma hakkında daha fazla bilgi için bkz. [Genelleştirilmiş BIR VHD 'Yi karşıya yükleme ve Azure 'da yeni BIR VM oluşturmak için kullanma](sa-upload-generalized.md).


>[!NOTE]
> Özel bir *Unattend. xml* dosyası desteklenmez. `additionalUnattendContent` özelliğini destekliyoruz, ancak [Microsoft-Windows-Shell-Setup](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) seçeneklerini Azure sağlama aracısının kullandığı *Unattend. xml* dosyasına eklemek için yalnızca sınırlı destek sağlar. FirstLogonCommand ve LogonCommands eklemek için, örneğin, [Additionalunattendcontent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) ' i kullanabilirsiniz. Daha fazla bilgi için bkz. [Additionalunattendcontent FirstLogonCommands örneği](https://github.com/Azure/azure-quickstart-templates/issues/1407).


## <a name="complete-the-recommended-configurations"></a>Önerilen konfigürasyonları doldurun
Aşağıdaki ayarlar, VHD karşıya yüklemeyi etkilemez. Ancak, bunları yapılandırdığınızdan kesinlikle önerilir.

* [Azure sanal makine Aracısı](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)'nı yükler. Daha sonra, VM uzantılarını etkinleştirebilirsiniz. VM uzantıları, VM 'leriniz ile kullanmak isteyebileceğiniz kritik işlevlerin çoğunu uygular. Örneğin, parola sıfırlama veya RDP 'yi yapılandırma gibi uzantılara ihtiyaç duyarsınız. Daha fazla bilgi için bkz. [Azure sanal makine aracısına genel bakış](../extensions/agent-windows.md).
* Azure 'da VM 'yi oluşturduktan sonra, performansı artırmak için sayfa dosyasını zamana bağlı *sürücü birimine* yerleştirmeniz önerilir. Dosya yerleşimini aşağıdaki gibi ayarlayabilirsiniz:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -Force
   ```
  SANAL makineye bir veri diski eklenmişse, zamana bağlı sürücü biriminin harfi genellikle *D*olur. Ayarlarınıza ve kullanılabilir sürücü sayısına bağlı olarak bu atama farklı olabilir.
  * Virüsten koruma yazılımı tarafından sağlangerekebilecek komut dosyası engelleyicilerini devre dışı bırakmayı öneririz. Bu kişiler, yansımasından yeni bir VM dağıtırken yürütülen Windows sağlama Aracısı betikleri kesintiye uğratabilecek ve engelleyebilirler.
  
## <a name="next-steps"></a>Sonraki adımlar
* [Kaynak Yöneticisi dağıtımları için Azure 'a bir Windows VM görüntüsü yükleme](upload-generalized-managed.md)
* [Azure Windows VM etkinleştirme sorunlarını giderme](troubleshoot-activation-problems.md)

