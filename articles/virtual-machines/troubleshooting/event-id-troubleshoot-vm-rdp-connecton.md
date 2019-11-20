---
title: Olay KIMLIĞINE göre Azure VM RDP bağlantısı sorunlarını giderme | Microsoft Docs
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
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: 166648402eec7f8033c090a3f7862a902bae4be6
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71154200"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>Azure VM RDP bağlantı sorunlarını olay kimliğine göre giderme 

Bu makalede, bir Azure sanal makinesine (VM) yönelik Uzak Masaüstü Protokolü (RDP) bağlantısını engelleyen sorunları gidermek için olay kimliklerinin nasıl kullanılacağı açıklanmaktadır.

## <a name="symptoms"></a>Belirtiler

Bir Azure VM 'ye bağlanmak için Uzak Masaüstü Protokolü (RDP) oturumu kullanmayı deneyin. Kimlik bilgilerinizi girdikten sonra bağlantı başarısız olur ve aşağıdaki hata iletisini alırsınız:

**Bu bilgisayar uzak bilgisayara bağlanamıyor. Yeniden bağlanmayı deneyin, sorun devam ederse, uzak bilgisayarın sahibine veya ağ yöneticinize başvurun.**

Bu sorunu gidermek için, VM 'deki olay günlüklerini gözden geçirin ve ardından aşağıdaki senaryolara bakın.

## <a name="before-you-troubleshoot"></a>Sorun gidermeye başlamadan önce

### <a name="create-a-backup-snapshot"></a>Yedekleme anlık görüntüsü oluşturma

Bir yedekleme anlık görüntüsü oluşturmak için, [bir diskte anlık görüntü](../windows/snapshot-copy-managed-disk.md)' daki adımları izleyin.

### <a name="connect-to-the-vm-remotely"></a>VM 'ye uzaktan bağlanma

VM 'ye uzaktan bağlanmak için, [Azure VM sorunlarını gidermek üzere uzak araçları kullanma](remote-tools-troubleshoot-azure-vm-issues.md)bölümündeki yöntemlerden birini kullanın.

## <a name="scenario-1"></a>Senaryo 1

### <a name="event-logs"></a>Olay günlükleri

Bir CMD örneğinde, son 24 saat içindeki sistem günlüğüne olay 1058 veya olay 1057 ' nin kaydedilip kaydedilmeyeceğini denetlemek için aşağıdaki komutları çalıştırın:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Günlük adı:**      Sistem <br />
**Kaynaktaki**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Tarih:** *zaman* <br />
**Olay KIMLIĞI:**      1058 <br />
**Görev kategorisi:** Yok. <br />
**Düzeyde**         Hata <br />
**Lerimi**      Klasik <br />
**Kullanıcı:**          Yok <br />
**Bilgisayar:** *bilgisayar* <br />
**Açıklama:** RD Oturumu Ana Bilgisayarı sunucusu, SSL bağlantılarında RD Oturumu Ana Bilgisayarı sunucu kimlik doğrulaması için kullanılan, süresi biten kendinden imzalı sertifikayı değiştirme işlemi başarısız oldu. İlgili durum kodu erişim reddedildi.

**Günlük adı:**      Sistem <br />
**Kaynaktaki**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Tarih:** *zaman* <br />
**Olay KIMLIĞI:**      1058 <br />
**Görev kategorisi:** Yok. <br />
**Düzeyde**         Hata <br />
**Lerimi**      Klasik <br />
**Kullanıcı:**          Yok <br />
**Bilgisayar:** *bilgisayar* <br />
**Açıklama:** RD Oturumu Ana Bilgisayarı sunucusu, SSL bağlantılarında RD Oturumu Ana Bilgisayarı sunucu kimlik doğrulaması için kullanılmak üzere yeni bir kendinden imzalı sertifika oluşturamadı, ilgili durum kodu nesne zaten var.

**Günlük adı:**      Sistem <br />
**Kaynaktaki**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Tarih:** *zaman* <br />
**Olay KIMLIĞI:**      1057 <br />
**Görev kategorisi:** Yok. <br />
**Düzeyde**         Hata <br />
**Lerimi**      Klasik <br />
**Kullanıcı:**          Yok <br />
**Bilgisayar:** *bilgisayar* <br />
**Açıklama:** RD Oturumu Ana Bilgisayarı sunucusu, SSL bağlantılarında RD Oturumu Ana Bilgisayarı sunucu kimlik doğrulaması için kullanılacak yeni bir kendinden imzalı sertifika oluşturamadı. İlgili durum kodu anahtar kümesi yok

Aşağıdaki komutları çalıştırarak SCHANNEL hata olaylarını 36872 ve 36870 olarak da denetleyebilirsiniz:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Günlük adı:**      Sistem <br />
**Kaynaktaki**        Schannel <br />
**Tarih:** — <br />
**Olay KIMLIĞI:**      36870 <br />
**Görev kategorisi:** Yok. <br />
**Düzeyde**         Hata <br />
**Lerimi**       <br />
**Kullanıcı:**          SISTEMIN <br />
**Bilgisayar:** *bilgisayar* <br />
**Açıklama:** SSL sunucusu kimlik bilgisi özel anahtarına erişmeye çalışırken önemli bir hata oluştu. Şifreleme modülünden döndürülen hata kodu 0x8009030D.  <br />
İç hata durumu 10001 ' dir.

### <a name="cause"></a>Nedeni
Bu sorun, VM 'deki MachineKeys klasöründe yerel RSA şifreleme anahtarlarına erişilemediğinden oluşur. Bu sorun, aşağıdakilerden biri nedeniyle oluşabilir:

1. MachineKeys klasöründe veya RSA dosyalarında yanlış izin yapılandırması.

2. RSA anahtarı bozuk veya eksik.

### <a name="resolution"></a>Çözüm

Bu sorunu gidermek için, aşağıdaki adımları kullanarak RDP sertifikasında doğru izinleri ayarlamanız gerekir.

#### <a name="grant-permission-to-the-machinekeys-folder"></a>MachineKeys klasörüne izin ver

1. Aşağıdaki içeriği kullanarak bir komut dosyası oluşturun:

   ```powershell
   remove-module psreadline 
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
   takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt
   Restart-Service TermService -Force
   ```

2.  MachineKey klasörünün izinlerini sıfırlamak ve RSA dosyalarını varsayılan değerlere sıfırlamak için bu betiği çalıştırın.

3.  VM 'ye erişmeyi yeniden deneyin.

Betiği çalıştırdıktan sonra, izin sorunları yaşayan aşağıdaki dosyaları kontrol edebilirsiniz:

* c:\temp\BeforeScript_permissions.txt
* c:\temp\AfterScript_permissions.txt

#### <a name="renew-rdp-self-signed-certificate"></a>RDP otomatik olarak imzalanan sertifikayı Yenile

Sorun devam ederse, RDP otomatik olarak imzalanan sertifikanın yenilendiğinden emin olmak için aşağıdaki betiği çalıştırın:

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

Sertifikayı yenileyemezseniz, sertifikayı silmeyi denemek için aşağıdaki adımları izleyin:

1. Aynı VNET 'teki başka bir sanal makinede, **Çalıştır** kutusunu açın, **MMC**yazın ve ardından **Tamam**' a basın. 

2. **Dosya** menüsünde **ek bileşen Ekle/Kaldır**' ı seçin.

3. **Kullanılabilir ek bileşenler** listesinde, **Sertifikalar**' ı seçin ve ardından **Ekle**' yi seçin.

4. **Bilgisayar hesabı**' nı seçin ve ardından **İleri**' yi seçin.

5. **Başka bir bilgisayar**seçin ve ardından sorunlu VM 'nin IP adresini ekleyin.
   >[!Note]
   >Sanal bir IP adresi kullanmaktan kaçınmak için iç ağı kullanmayı deneyin.

6. **Son**' u ve ardından **Tamam**' ı seçin.

   ![Bilgisayar seçin](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. Sertifikaları genişletin, uzak Desktop\Certificates klasörüne gidin, sertifikaya sağ tıklayın ve ardından **Sil**' i seçin.

8. Uzak Masaüstü yapılandırma hizmetini yeniden başlatın:

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >Bu noktada, mağazayı MMC 'den yenilerseniz sertifika yeniden görüntülenir. 

RDP 'yi yeniden kullanarak VM 'ye erişmeyi deneyin.

#### <a name="update-secure-sockets-layer-ssl-certificate"></a>Güvenli Yuva Katmanı (SSL) sertifikasını Güncelleştir

VM 'yi bir SSL sertifikası kullanacak şekilde ayarlarsanız, parmak izini almak için aşağıdaki komutu çalıştırın. Sertifikanın parmak iziyle aynı olup olmadığını kontrol edin:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

Değilse, parmak izini değiştirin:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

RDP için otomatik olarak imzalanan sertifika kullanması için anahtarı silmeyi de deneyebilirsiniz:

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>Senaryo 2

### <a name="event-log"></a>Olay günlüğü

Bir CMD örneğinde, son 24 saat içindeki Sistem günlüğünde SCHANNEL hata olayı 36871 ' nin günlüğe kaydedilip kaydedilmeyeceğini denetlemek için aşağıdaki komutları çalıştırın:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Günlük adı:**      Sistem <br />
**Kaynaktaki**        Schannel <br />
**Tarih:** — <br />
**Olay KIMLIĞI:**      36871 <br />
**Görev kategorisi:** Yok. <br />
**Düzeyde**         Hata <br />
**Lerimi**       <br />
**Kullanıcı:**          SISTEMIN <br />
**Bilgisayar:** *bilgisayar* <br />
**Açıklama:** TLS sunucusu kimlik bilgisi oluşturulurken önemli bir hata oluştu. İç hata durumu 10013 ' dir.
 
### <a name="cause"></a>Nedeni

Bu sorun, güvenlik ilkeleri nedeniyle oluşur. TLS (1,0 gibi) eski sürümleri devre dışı bırakıldığında, RDP erişimi başarısız olur.

### <a name="resolution"></a>Çözüm

RDP, varsayılan protokol olarak TLS 1,0 kullanır. Ancak protokol, yeni standart olan TLS 1,1 olarak değiştirilebilir.

Bu sorunu gidermek için bkz. [Azure VM 'ye bağlanmak IÇIN RDP kullanırken kimlik doğrulama hatalarında sorun giderme](troubleshoot-authentication-error-rdp-vm.md#tls-version).

## <a name="scenario-3"></a>Senaryo 3

VM 'ye **Uzak Masaüstü Bağlantı Aracısı** rolünü yüklediyseniz, son 24 saat içinde olay 2056 veya olay 1296 ' nin olup olmadığını kontrol edin. Bir CMD örneğinde aşağıdaki komutları çalıştırın: 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Günlük adı:**      Microsoft-Windows-TerminalServices-SessionBroker/operasyonel <br />
**Kaynaktaki**        Microsoft-Windows-TerminalServices-SessionBroker <br />
**Tarih:** *zaman* <br />
**Olay KIMLIĞI:**      2056 <br />
**Görev kategorisi:** (109) <br />
**Düzeyde**         Hata <br />
**Lerimi**       <br />
**Kullanıcı:**          AĞ HIZMETI <br />
**Bilgisayar:** *bilgisayar FQDN 'si* <br />
**Açıklama:** Kaynak Microsoft-Windows-TerminalServices-SessionBroker 'dan 2056 olay KIMLIĞI için açıklama bulunamıyor. Bu olayı başlatan bileşen yerel bilgisayarınızda yüklü değil veya yükleme bozuk. Bileşeni yerel bilgisayara yükleyebilir veya onarabilirsiniz. <br />
Olayın başka bir bilgisayarda kaynağı varsa, görüntüleme bilgilerinin olayla birlikte kaydedilmesi gerekiyordu. <br />
Olaya aşağıdaki bilgiler eklenmiştir: <br />
NULL <br />
NULL <br />
Veritabanında oturum açılamadı.

**Günlük adı:**      Microsoft-Windows-TerminalServices-SessionBroker-Istemci/Işlem <br />
**Kaynaktaki**        Microsoft-Windows-TerminalServices-SessionBroker-Client <br />
**Tarih:** *zaman* <br />
**Olay KIMLIĞI:**      1296 <br />
**Görev kategorisi:** (104) <br />
**Düzeyde**         Hata <br />
**Lerimi**       <br />
**Kullanıcı:**          AĞ HIZMETI <br />
**Bilgisayar:** *bilgisayar FQDN 'si* <br />
**Açıklama:** Microsoft-Windows-TerminalServices-SessionBroker-Client kaynağından olay KIMLIĞI 1296 açıklaması bulunamıyor. Bu olayı başlatan bileşen yerel bilgisayarınızda yüklü değil veya yükleme bozuk. Bileşeni yerel bilgisayara yükleyebilir veya onarabilirsiniz.
Olayın başka bir bilgisayarda kaynağı varsa, görüntüleme bilgilerinin olayla birlikte kaydedilmesi gerekiyordu.
Olaya aşağıdaki bilgiler eklenmiştir:  <br />
*text* <br />
*text* <br />
Uzak Masaüstü Bağlantı Aracısı RPC iletişimi için kullanılamaz.

### <a name="cause"></a>Nedeni

Bu sorun, desteklenen bir değişiklik olmayan Uzak Masaüstü Bağlantı Aracısı sunucusunun ana bilgisayar adı değiştiği için oluşur. 

Ana bilgisayar adı, Windows Iç veritabanı üzerinde giriş ve bağımlılıklara sahiptir ve bu, uzak masaüstü hizmet grubu 'nun çalışabilebilmesi için gereklidir. Grup zaten oluşturulduktan sonra ana bilgisayar adının değiştirilmesi çok sayıda hataya neden olur ve aracı sunucusunun çalışmayı durdurmasına neden olabilir.

### <a name="resolution"></a>Çözüm 

Bu sorunu onarmak için Uzak Masaüstü Bağlantı Aracısı rolünün ve Windows Iç veritabanının yeniden yüklenmesi gerekir.

## <a name="next-steps"></a>Sonraki Adımlar

[Schannel olayları](https://technet.microsoft.com/library/dn786445(v=ws.11).aspx)

[Schannel SSP teknik genel bakış](https://technet.microsoft.com/library/dn786429(v=ws.11).aspx)

[RDP, Uzak Masaüstü Oturumu Ana Bilgisayarı sertifikası & SSL Iletişimi ile olay KIMLIĞI 1058 & olay 36870 ile başarısız oluyor](https://blogs.technet.microsoft.com/askperf/2014/10/22/rdp-fails-with-event-id-1058-event-36870-with-remote-desktop-session-host-certificate-ssl-communication/)

[Bir etki alanı denetleyicisinde Schannel 36872 veya Schannel 36870](https://blogs.technet.microsoft.com/instan/2009/01/05/schannel-36872-or-schannel-36870-on-a-domain-controller/)

[Olay KIMLIĞI 1058 — Uzak Masaüstü Hizmetleri kimlik doğrulaması ve şifreleme](https://technet.microsoft.com/library/ee890862(v=ws.10).aspx)

