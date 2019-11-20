---
title: Azure Windows VM 'lerinde CMD ve PowerShell | Microsoft Docs
description: Azure Windows VM 'lerinde SAC içinde CMD ve PowerShell komutlarını kullanma
services: virtual-machines-windows
documentationcenter: ''
author: alsin
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: a106984bc60d0ccfe29a1956213aec6f87ad30dd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70090180"
---
# <a name="windows-commands---cmd-and-powershell"></a>Windows komutları-CMD ve PowerShell

Bu bölüm, RDP bağlantı hatalarıyla ilgili sorunları gidermeniz gerektiğinde olduğu gibi, Windows sanal makinenize erişmek için SAC kullanmanız gerekebilecek senaryolarda yaygın görevleri gerçekleştirmeye yönelik örnek komutlar içerir.

SAC, Windows Server 2003 ' den beri tüm Windows sürümlerine eklenmiştir ancak varsayılan olarak devre dışıdır. Sac `sacdrv.sys` çekirdek sürücüsünü `Special Administration Console Helper` ,`sacsvr` hizmeti`sacsess.exe` () ve işlemi kullanır. Daha fazla bilgi için bkz. [acil durum Yönetim Hizmetleri araçları ve ayarları](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10)).

SAC, çalışan işletim sistemine seri bağlantı noktası aracılığıyla bağlanmanızı sağlar. Sac 'yi sac 'den başlattığınızda, `sacsess.exe` çalışan işletim sistemi içinde başlatılır. `cmd.exe` VM 'niz için aynı anda sanal makinenizde RDP 'niz varsa, seri konsol özelliği aracılığıyla da SAC 'ye bağlı olursunuz. Sac aracılığıyla erişebileceğiniz cmd, RDP ile bağlıyken kullandığınız `cmd.exe` aynıdır. Aynı komutların ve araçların tümü, bu CMD örneğinden PowerShell 'i başlatma özelliği de dahil olmak üzere kullanılabilir. Bu, sac ile Windows kurtarma ortamı (WinRE) arasında büyük bir farklılık olduğunu ve bu, WinRE 'nin farklı ve en az bir işletim sistemi üzerinde önyüklemesinde çalışan işletim sistemini yönetmenize olanak sağlar. Azure VM 'Leri, seri konsol özelliği ile WinRE 'ye erişme özelliğini desteklemediğinden, Azure VM 'Leri SAC aracılığıyla yönetilebilir.

Sac geri kaydırma yapmadan bir 80x24 ekran arabelleği ile sınırlı olduğundan, çıktıyı tek seferde `| more` göstermek için komutlara ekleyin. Sonraki `<spacebar>` sayfayı görmek veya `<enter>` sonraki satırı görmek için kullanın.  

`SHIFT+INSERT`, seri konsol penceresi için yapıştırma kısayoludur.

SAC 'nin sınırlı ekran arabelleği nedeniyle, daha uzun komutlar bir yerel metin düzenleyicisine yazmak ve sonra SAC 'ye yapıştırılabilmesi daha kolay olabilir.

## <a name="view-and-edit-windows-registry-settings"></a>Windows kayıt defteri ayarlarını görüntüleyin ve düzenleyin
### <a name="verify-rdp-is-enabled"></a>RDP 'nin etkinleştirildiğini doğrulama
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

İkinci anahtar (\Policies altında), yalnızca ilgili Grup İlkesi ayarı yapılandırılmışsa vardır.

### <a name="enable-rdp"></a>RDP 'yi etkinleştirme
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0` 

İkinci anahtar (\Policies altında) yalnızca ilgili Grup İlkesi ayarı yapılandırıldıysa gereklidir. Değer, Grup İlkesi 'nde yapılandırıldıysa bir sonraki Grup İlkesi yenilemesinde yeniden yazılır.

## <a name="manage-windows-services"></a>Windows hizmetlerini yönetme

### <a name="view-service-state"></a>Hizmet durumunu görüntüle
`sc query termservice`
###  <a name="view-service-logon-account"></a>Hizmet oturum açma hesabını görüntüle
`sc qc termservice`
### <a name="set-service-logon-account"></a>Hizmet oturum açma hesabını ayarla 
`sc config termservice obj= "NT Authority\NetworkService"`

Eşittir işaretinden sonra bir boşluk gereklidir.
### <a name="set-service-start-type"></a>Hizmet başlatma türünü ayarla
`sc config termservice start= demand` 

Eşittir işaretinden sonra bir boşluk gereklidir. Olası başlangıç değerleri şunlardır `boot` `system` `auto` ,,`delayed-auto`,, ,.`disabled` `demand`
### <a name="set-service-dependencies"></a>Hizmet bağımlılıklarını ayarlama
`sc config termservice depend= RPCSS`

Eşittir işaretinden sonra bir boşluk gereklidir.
### <a name="start-service"></a>Hizmeti Başlat
`net start termservice`

veya

`sc start termservice`
### <a name="stop-service"></a>Hizmeti durdur
`net stop termservice`

veya

`sc stop termservice`
## <a name="manage-networking-features"></a>Ağ özelliklerini yönetme
### <a name="show-nic-properties"></a>NIC özelliklerini göster
`netsh interface show interface` 
### <a name="show-ip-properties"></a>IP özelliklerini göster
`netsh interface ip show config`
### <a name="show-ipsec-configuration"></a>IPSec yapılandırmasını göster
`netsh nap client show configuration`  
### <a name="enable-nic"></a>NIC 'yi etkinleştir
`netsh interface set interface name="<interface name>" admin=enabled`
### <a name="set-nic-to-use-dhcp"></a>NIC 'yi DHCP kullanacak şekilde ayarlama
`netsh interface ip set address name="<interface name>" source=dhcp`

Hakkında `netsh`daha fazla bilgi için [buraya tıklayın](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts).

Azure VM 'Leri, bir IP adresi almak için DHCP kullanmak üzere Konuk işletim sisteminde her zaman yapılandırılmalıdır. Azure statik IP ayarı yine de DHCP 'yi kullanarak statik IP 'yi VM 'ye verir.
### <a name="ping"></a>Ping
`ping 8.8.8.8` 
### <a name="port-ping"></a>Bağlantı noktası ping  
Telnet istemcisini yükler

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Bağlantıyı test etme

`telnet bing.com 80`

Telnet istemcisini kaldırmak için

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Windows 'da varsayılan olarak kullanılabilen yöntemlerle sınırlı olduğunda, PowerShell bağlantı noktası bağlantısını test etmek için daha iyi bir yaklaşım olabilir. Örnekler için aşağıdaki PowerShell bölümüne bakın.
### <a name="test-dns-name-resolution"></a>Test DNS ad çözümlemesi
`nslookup bing.com`
### <a name="show-windows-firewall-rule"></a>Windows güvenlik duvarı kuralını göster
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
### <a name="disable-windows-firewall"></a>Windows güvenlik duvarını devre dışı bırak
`netsh advfirewall set allprofiles state off`

Windows Güvenlik Duvarı 'nı geçici olarak kurala göre sorun giderirken bu komutu kullanabilirsiniz. Bir sonraki yeniden başlatmada veya aşağıdaki komutu kullanarak etkinleştirdiğinizde etkinleştirilecek. Windows Güvenlik Duvarı 'nı nasıl denetleyeceğiyle Windows Güvenlik Duvarı hizmeti (MPSSVC) veya temel filtre altyapısı (BFE) hizmetini durdurmayın. MPSSVC veya BFE 'yi durdurmak, tüm bağlantıların engellenmesidir.
### <a name="enable-windows-firewall"></a>Windows güvenlik duvarını etkinleştir
`netsh advfirewall set allprofiles state on`
## <a name="manage-users-and-groups"></a>Kullanıcıları ve grupları yönetme
### <a name="create-local-user-account"></a>Yerel Kullanıcı hesabı oluştur
`net user /add <username> <password>`
### <a name="add-local-user-to-local-group"></a>Yerel gruba yerel kullanıcı ekleme
`net localgroup Administrators <username> /add`
### <a name="verify-user-account-is-enabled"></a>Kullanıcı hesabının etkin olduğunu doğrulama
`net user <username> | find /i "active"`

Genelleştirilmiş görüntüden oluşturulan Azure VM 'lerinin yerel yönetici hesabı, VM sağlama sırasında belirtilen adla yeniden adlandırılacaktır. Bu nedenle, genellikle `Administrator`olmayacaktır.
### <a name="enable-user-account"></a>Kullanıcı hesabını etkinleştir
`net user <username> /active:yes`  
### <a name="view-user-account-properties"></a>Kullanıcı hesabı özelliklerini görüntüle
`net user <username>`

Yerel yönetici hesabından ilgilendiğiniz örnek satırlar:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

### <a name="view-local-groups"></a>Yerel grupları görüntüle
`net localgroup`
## <a name="manage-the-windows-event-log"></a>Windows olay günlüğü 'Nü yönetme
### <a name="query-event-log-errors"></a>Olay günlüğü hatalarını sorgula
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Döndürülecek `/c:10` olay sayısı olarak değiştirin veya filtreyle eşleşen tüm olayları döndürmek için taşıyın.
### <a name="query-event-log-by-event-id"></a>Olay KIMLIĞINE göre olay günlüğünü sorgula
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Olay KIMLIĞI ve sağlayıcıya göre olay günlüğünü sorgula
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Son 24 saat için olay KIMLIĞINE ve sağlayıcıya göre olay günlüğünü sorgula
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

24 `604800000` saat yerine 7 gün geri bakmak için kullanın.
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Son 7 günde olay KIMLIĞI, sağlayıcı ve EventData 'a göre olay günlüğünü sorgula
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Yüklü uygulamaları görüntüleme veya kaldırma
### <a name="list-installed-applications"></a>Yüklü uygulamaları listeleme
`wmic product get Name,InstallDate | sort /r | more`

Son zamanlarda nelerin yüklendiğini görmeyi kolaylaştırmak için yükleme tarihine göre azalan düzende sıralanır.`sort /r` Sonraki `<spacebar>` çıkış sayfasına ilerlemek veya `<enter>` bir satır ilerlemek için kullanın.
### <a name="uninstall-an-application"></a>Bir uygulamayı kaldırma
`wmic path win32_product where name="<name>" call uninstall`

Kaldırmak `<name>` istediğiniz uygulamanın Yukarıdaki komutta döndürülen adla değiştirin.

## <a name="file-system-management"></a>Dosya sistemi yönetimi
### <a name="get-file-version"></a>Dosya sürümünü al
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

Bu örnek, Windows sürümüne bağlı olarak netvsc. sys, netvsc63. sys veya netvsc60. sys olan sanal NIC sürücüsünün dosya sürümünü döndürür.
### <a name="scan-for-system-file-corruption"></a>Sistem dosyası bozukluğunu Tara
`sfc /scannow`

Ayrıca bkz. [Windows görüntüsünü onarma](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="scan-for-system-file-corruption"></a>Sistem dosyası bozukluğunu Tara
`dism /online /cleanup-image /scanhealth`

Ayrıca bkz. [Windows görüntüsünü onarma](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="export-file-permissions-to-text-file"></a>Dosya izinlerini metin dosyasına aktar
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
### <a name="save-file-permissions-to-acl-file"></a>Dosya izinlerini ACL dosyasına kaydet
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`  
### <a name="restore-file-permissions-from-acl-file"></a>Dosya izinlerini ACL dosyasından geri yükle
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

Kullanmanın `/restore` , kullanırken belirttiğiniz `/save`klasörün üst klasörü olması gerekir. Bu örnekte, `\RSA` Yukarıdaki `/save` örnekte belirtilen `\MachineKeys` klasörün üst öğesidir.
### <a name="take-ntfs-ownership-of-a-folder"></a>Bir klasörün NTFS sahipliğini al
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`  
### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>Yinelemeli olarak bir klasöre NTFS izinleri verme
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`  
## <a name="manage-devices"></a>Cihazları yönetme
### <a name="remove-non-present-pnp-devices"></a>Mevcut olmayan PNP cihazlarını kaldır
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
## <a name="manage-group-policy"></a>grup ilkesi Yönet
### <a name="force-group-policy-update"></a>Grup ilkesi güncelleştirmesini zorla
`gpupdate /force /wait:-1`
## <a name="miscellaneous-tasks"></a>Çeşitli görevler
### <a name="show-os-version"></a>İşletim sistemi sürümünü göster
`ver`

veya 

`wmic os get caption,version,buildnumber /format:list`

veya 

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
### <a name="view-os-install-date"></a>İşletim sistemi yüklemesi tarihini görüntüleme
`systeminfo | find /i "original"`

veya 

`wmic os get installdate`
### <a name="view-last-boot-time"></a>Son önyükleme zamanını görüntüle
`systeminfo | find /i "system boot time"`
### <a name="view-time-zone"></a>Saat dilimini görüntüle
`systeminfo | find /i "time zone"`

veya

`wmic timezone get caption,standardname /format:list`
### <a name="restart-windows"></a>Windows 'u yeniden Başlat
`shutdown /r /t 0`

Eklemek `/f` , çalışan uygulamaları uyarı kullanıcısı olmadan kapanmaya zorlar.
### <a name="detect-safe-mode-boot"></a>Güvenli mod önyüklemesini Algıla
`bcdedit /enum | find /i "safeboot"` 

## <a name="windows-commands---powershell"></a>Windows komutları-PowerShell

PowerShell 'i SAC 'de çalıştırmak için, bir komut istemine ulaştığınızda şunu yazın:

`powershell <enter>`

> [!CAUTION]
> Başka herhangi bir PowerShell komutunu çalıştırmadan önce, PSReadLine modülünü PowerShell oturumundan kaldırın. PSReadLine, SAC 'deki bir PowerShell oturumunda çalışıyorsa panodan yapıştırılan metinde ek karakterlerin tanıtıldığı bilinen bir sorun vardır.

Önce PSReadLine 'un yüklenip yüklenmediğini kontrol edin. Windows Server 2016, Windows 10 ve sonraki Windows sürümlerinde varsayılan olarak yüklenir. Yalnızca el ile yüklenmişse, önceki Windows sürümlerinde mevcut olacaktır. 

Bu komut çıkış olmadan bir komut istemine geri dönerse modül yüklenmemiştir ve SAC 'de normal olarak PowerShell oturumu kullanmaya devam edebilirsiniz.

`get-module psreadline`

Yukarıdaki komut PSReadLine modülü sürümünü döndürürse, kaldırmak için aşağıdaki komutu çalıştırın. Bu komut modülü silmez veya kaldırmaz, yalnızca geçerli PowerShell oturumundan kaldırılır.

`remove-module psreadline`

## <a name="view-and-edit-windows-registry-settings"></a>Windows kayıt defteri ayarlarını görüntüleyin ve düzenleyin
### <a name="verify-rdp-is-enabled"></a>RDP 'nin etkinleştirildiğini doğrulama
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

İkinci anahtar (\Policies altında), yalnızca ilgili Grup İlkesi ayarı yapılandırılmışsa vardır.
### <a name="enable-rdp"></a>RDP 'yi etkinleştirme
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

İkinci anahtar (\Policies altında) yalnızca ilgili Grup İlkesi ayarı yapılandırıldıysa gereklidir. Değer, Grup İlkesi 'nde yapılandırıldıysa bir sonraki Grup İlkesi yenilemesinde yeniden yazılır.
## <a name="manage-windows-services"></a>Windows hizmetlerini yönetme
### <a name="view-service-details"></a>Hizmet ayrıntılarını görüntüle
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service`kullanılabilir, ancak hizmet oturum açma hesabını içermez. `Get-WmiObject win32-service`lamaz.
### <a name="set-service-logon-account"></a>Hizmet oturum açma hesabını ayarla
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

, `NT AUTHORITY\LocalService` `NT AUTHORITY\NetworkService`Veya dışındabirhizmethesabıkullanırken,hesapadındansonrakison(sekizinci)bağımsızdeğişkenolarakhesapparolasınıbelirtin.`LocalSystem`
### <a name="set-service-startup-type"></a>Hizmet başlatma türünü ayarla
`set-service termservice -startuptype Manual`

`Set-service`Başlangıç `Automatic`türünü `Manual`,, `Disabled` veya kabul eder.
### <a name="set-service-dependencies"></a>Hizmet bağımlılıklarını ayarlama
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
### <a name="start-service"></a>Hizmeti Başlat
`start-service termservice`
### <a name="stop-service"></a>Hizmeti durdur
`stop-service termservice`
## <a name="manage-networking-features"></a>Ağ özelliklerini yönetme
### <a name="show-nic-properties"></a>NIC özelliklerini göster
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

veya 

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter`2012 + ' de, 2008R2 kullanımı `Get-WmiObject`için kullanılabilir.
### <a name="show-ip-properties"></a>IP özelliklerini göster
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
### <a name="enable-nic"></a>NIC 'yi etkinleştir
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

veya

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter`2012 + ' de, 2008R2 kullanımı `Get-WmiObject`için kullanılabilir.
### <a name="set-nic-to-use-dhcp"></a>NIC 'yi DHCP kullanacak şekilde ayarlama
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter`, 2012 + ' da kullanılabilir. 2008R2 için kullanım `Get-WmiObject`. Azure VM 'Leri, bir IP adresi almak için DHCP kullanmak üzere Konuk işletim sisteminde her zaman yapılandırılmalıdır. Azure statik IP ayarı hala IP 'yi VM 'ye vermek için DHCP kullanır.
### <a name="ping"></a>Ping
`test-netconnection`

veya

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection`Hiçbir parametre olmadan ping `internetbeacon.msedge.net`göndermeye çalışır. 2012 + ' de kullanılabilir. 2008R2 için ikinci `Get-WmiObject` örnekte olduğu gibi kullanın.
### <a name="port-ping"></a>Bağlantı noktası ping
`test-netconnection -ComputerName bing.com -Port 80`

veya

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection`, 2012 + ' da kullanılabilir. 2008R2 için kullanım için`Net.Sockets.TcpClient`
### <a name="test-dns-name-resolution"></a>Test DNS ad çözümlemesi
`resolve-dnsname bing.com` 

veya 

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName`, 2012 + ' da kullanılabilir. 2008R2 için kullanım `System.Net.DNS`.
### <a name="show-windows-firewall-rule-by-name"></a>Windows güvenlik duvarı kuralını ada göre göster
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP` 
### <a name="show-windows-firewall-rule-by-port"></a>Windows güvenlik duvarı kuralını bağlantı noktasına göre göster
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

veya

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter`, 2012 + ' da kullanılabilir. 2008R2 için `hnetcfg.fwpolicy2` com nesnesini kullanın. 
### <a name="disable-windows-firewall"></a>Windows güvenlik duvarını devre dışı bırak
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile`, 2012 + ' da kullanılabilir. 2008R2 için yukarıdaki `netsh advfirewall` cmd bölümünde başvurulan olarak kullanın.
## <a name="manage-users-and-groups"></a>Kullanıcıları ve grupları yönetme
### <a name="create-local-user-account"></a>Yerel Kullanıcı hesabı oluştur
`new-localuser <name>`
### <a name="verify-user-account-is-enabled"></a>Kullanıcı hesabının etkin olduğunu doğrulama
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

veya 

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser`, 2012 + ' da kullanılabilir. 2008R2 için kullanım `Get-WmiObject`. Bu örnekte, her zaman SID 'SI `S-1-5-21-*-500`olan yerleşik yerel yönetici hesabı gösterilmektedir. Genelleştirilmiş görüntüden oluşturulan Azure VM 'lerinin yerel yönetici hesabı, VM sağlama sırasında belirtilen adla yeniden adlandırılacaktır. Bu nedenle, genellikle `Administrator`olmayacaktır.
### <a name="add-local-user-to-local-group"></a>Yerel gruba yerel kullanıcı ekleme
`add-localgroupmember -group Administrators -member <username>`
### <a name="enable-local-user-account"></a>Yerel Kullanıcı hesabını etkinleştir
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser` 

Bu örnek, her zaman SID 'SI `S-1-5-21-*-500`olan yerleşik yerel yönetici hesabını sunar. Genelleştirilmiş görüntüden oluşturulan Azure VM 'lerinin yerel yönetici hesabı, VM sağlama sırasında belirtilen adla yeniden adlandırılacaktır. Bu nedenle, genellikle `Administrator`olmayacaktır.
### <a name="view-user-account-properties"></a>Kullanıcı hesabı özelliklerini görüntüle
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

veya 

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser`, 2012 + ' da kullanılabilir. 2008R2 için kullanım `Get-WmiObject`. Bu örnekte, her zaman SID 'SI `S-1-5-21-*-500`olan yerleşik yerel yönetici hesabı gösterilmektedir.
### <a name="view-local-groups"></a>Yerel grupları görüntüle
`(get-localgroup).name | sort``(get-wmiobject win32_group).Name | sort`

`Get-LocalUser`, 2012 + ' da kullanılabilir. 2008R2 için kullanım `Get-WmiObject`.
## <a name="manage-the-windows-event-log"></a>Windows olay günlüğü 'Nü yönetme
### <a name="query-event-log-errors"></a>Olay günlüğü hatalarını sorgula
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Döndürülecek `/c:10` olay sayısı olarak değiştirin veya filtreyle eşleşen tüm olayları döndürmek için taşıyın.
### <a name="query-event-log-by-event-id"></a>Olay KIMLIĞINE göre olay günlüğünü sorgula
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Olay KIMLIĞI ve sağlayıcıya göre olay günlüğünü sorgula
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Son 24 saat için olay KIMLIĞINE ve sağlayıcıya göre olay günlüğünü sorgula
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

24 `604800000` saat yerine 7 gün geri bakmak için kullanın. |
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Son 7 günde olay KIMLIĞI, sağlayıcı ve EventData 'a göre olay günlüğünü sorgula
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Yüklü uygulamaları görüntüleme veya kaldırma
### <a name="list-installed-software"></a>Yüklü yazılımları listeleme
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
### <a name="uninstall-software"></a>Yazılımı Kaldır
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
## <a name="file-system-management"></a>Dosya sistemi yönetimi
### <a name="get-file-version"></a>Dosya sürümünü al
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

Bu örnek, Windows sürümüne bağlı olarak netvsc. sys, netvsc63. sys veya netvsc60. sys adlı sanal NIC sürücüsünün dosya sürümünü döndürür.
### <a name="download-and-extract-file"></a>Dosya indir ve Ayıkla
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

Bu örnek, bir `c:\bin` klasör oluşturur, ardından bu `c:\bin`araç paketini indirip ayıklar.
## <a name="miscellaneous-tasks"></a>Çeşitli görevler
### <a name="show-os-version"></a>İşletim sistemi sürümünü göster
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber` 
### <a name="view-os-install-date"></a>İşletim sistemi yüklemesi tarihini görüntüleme
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
### <a name="view-last-boot-time"></a>Son önyükleme zamanını görüntüle
`(get-wmiobject win32_operatingsystem).lastbootuptime`
### <a name="view-windows-uptime"></a>Windows çalışma süresini görüntüleyin
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

`<days>:<hours>:<minutes>:<seconds>:<milliseconds>` Örneğin`49:16:48:00.00`, çalışma süresini döndürür. 
### <a name="restart-windows"></a>Windows 'u yeniden Başlat
`restart-computer`

Eklemek `-force` , çalışan uygulamaları uyarı kullanıcısı olmadan kapanmaya zorlar.
## <a name="instance-metadata"></a>Örnek meta verileri

OsType, location, vmSize, VMID, Name, resourceGroupName, SubscriptionID, Privateıpaddress ve Publicıpaddress gibi ayrıntıları görüntülemek için Azure sanal makinenizin içinden Azure örnek meta verilerini sorgulayabilirsiniz.

Örnek meta verilerin sorgulanması, Azure ana bilgisayarında örnek meta veri hizmetine REST çağrısı yaptığından sağlıklı Konuk ağ bağlantısı gerektirir. Bu nedenle, Konuk meta verileri sorgulayabilirsiniz. Bu, konuğun bir Azure 'da barındırılan bir hizmetle ağ üzerinden iletişim kurabildiğini söyler.

Daha fazla bilgi için bkz. [Azure örnek meta verileri hizmeti](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service).

### <a name="instance-metadata"></a>Örnek meta verileri
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
### <a name="os-type-instance-metadata"></a>İşletim sistemi türü (örnek meta verileri)
`$im.Compute.osType`
### <a name="location-instance-metadata"></a>Konum (örnek meta verileri)
`$im.Compute.Location`
### <a name="size-instance-metadata"></a>Boyut (örnek meta verileri)
`$im.Compute.vmSize`
### <a name="vm-id-instance-metadata"></a>VM KIMLIĞI (örnek meta verileri)
`$im.Compute.vmId`
### <a name="vm-name-instance-metadata"></a>VM adı (örnek meta verileri)
`$im.Compute.name`
### <a name="resource-group-name-instance-metadata"></a>Kaynak grubu adı (örnek meta verileri)
`$im.Compute.resourceGroupName`
### <a name="subscription-id-instance-metadata"></a>Abonelik KIMLIĞI (örnek meta verileri)
`$im.Compute.subscriptionId`
### <a name="tags-instance-metadata"></a>Etiketler (örnek meta verileri)
`$im.Compute.tags`
### <a name="placement-group-id-instance-metadata"></a>Yerleştirme grubu KIMLIĞI (örnek meta verileri)
`$im.Compute.placementGroupId`
### <a name="platform-fault-domain-instance-metadata"></a>Platform hata etki alanı (örnek meta verileri)
`$im.Compute.platformFaultDomain`
### <a name="platform-update-domain-instance-metadata"></a>Platform Güncelleştirme etki alanı (örnek meta verileri)
`$im.Compute.platformUpdateDomain`
### <a name="ipv4-private-ip-address-instance-metadata"></a>IPv4 özel IP adresi (örnek meta verileri)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
### <a name="ipv4-public-ip-address-instance-metadata"></a>IPv4 Genel IP adresi (örnek meta verileri)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>IPv4 alt ağ adresi/öneki (örnek meta verileri)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
### <a name="ipv6-ip-address-instance-metadata"></a>IPv6 IP adresi (örnek meta verileri)
`$im.network.interface.ipv6.ipAddress`
### <a name="mac-address-instance-metadata"></a>MAC adresi (örnek meta verileri)
`$im.network.interface.macAddress`

## <a name="next-steps"></a>Sonraki adımlar
* Ana seri konsol Windows belgeleri sayfası [burada](serial-console-windows.md)bulunur.
* Seri konsol için de kullanılabilir olan [Linux](serial-console-linux.md) VM'ler.
* Daha fazla bilgi edinin [önyükleme tanılaması](boot-diagnostics.md).
