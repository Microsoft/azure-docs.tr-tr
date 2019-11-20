---
title: Azure VM 'ye bağlanmak için RDP kullandığınızda kimlik doğrulama hatalarını giderme | Microsoft Docs
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
ms.openlocfilehash: b7a561907e3f1968eb9adead3606822d7a1321c8
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155622"
---
# <a name="troubleshoot-authentication-errors-when-you-use-rdp-to-connect-to-azure-vm"></a>Azure VM 'ye bağlanmak için RDP kullandığınızda kimlik doğrulama hatalarında sorun giderme

Bu makale, bir Azure sanal makinesine (VM) bağlanmak üzere Uzak Masaüstü Protokolü (RDP) bağlantısı kullandığınızda oluşan kimlik doğrulama hatalarını gidermenize yardımcı olabilir.

## <a name="symptoms"></a>Belirtiler

Hoş Geldiniz ekranını gösteren ve işletim sisteminin çalıştığını gösteren bir Azure VM 'nin ekran görüntüsünü yakalarsınız. Ancak, Uzak Masaüstü Bağlantısı kullanarak VM 'ye bağlanmaya çalıştığınızda, aşağıdaki hata iletilerinden birini alırsınız.

### <a name="error-message-1"></a>Hata iletisi 1

**Bir kimlik doğrulama hatası oluştu. Yerel güvenlik yetkilisine ulaşılamıyor.**

### <a name="error-message-2"></a>Hata iletisi 2

**Bağlanmaya çalıştığınız uzak bilgisayar Ağ Düzeyinde Kimlik Doğrulama (NLA) gerektiriyor, ancak Windows etki alanı denetleyicinize NLA gerçekleştirmek için iletişim kurulamıyor. Uzak bilgisayarda yöneticisiyseniz, sistem özellikleri iletişim kutusunun Uzak sekmesindeki seçenekleri kullanarak NLA 'yı devre dışı bırakabilirsiniz.**

### <a name="error-message-3-generic-connection-error"></a>Hata iletisi 3 (genel bağlantı hatası)

**Bu bilgisayar uzak bilgisayara bağlanamıyor. Yeniden bağlanmayı deneyin, sorun devam ederse, uzak bilgisayarın sahibine veya ağ yöneticinize başvurun.**

## <a name="cause"></a>Nedeni

NLA 'nın bir VM 'ye RDP erişimini engelleyebileceği birden çok neden vardır.

### <a name="cause-1"></a>Neden 1

VM, etki alanı denetleyicisi (DC) ile iletişim kuramıyor. Bu sorun, RDP oturumunun etki alanı kimlik bilgilerini kullanarak bir VM 'ye erişmesini engelleyebilir. Ancak, yine de yerel yönetici kimlik bilgilerini kullanarak oturum açabiliyor olabilirsiniz. Bu sorun aşağıdaki durumlarda oluşabilir:

1. Bu VM ile DC arasındaki Active Directory güvenlik kanalı kopuk.

2. VM, hesap parolasının eski bir kopyasına sahiptir ve DC 'nin daha yeni bir kopyası vardır.

3. Bu VM 'nin bağlandığı DC sağlam değil.

### <a name="cause-2"></a>Neden 2

VM 'nin şifreleme düzeyi, istemci bilgisayar tarafından kullanılan olandan daha yüksek.

### <a name="cause-3"></a>Neden 3

TLS 1,0, 1,1 veya 1,2 (sunucu) protokolleri VM 'de devre dışı bırakıldı.

### <a name="cause-4"></a>Neden 4

VM, etki alanı kimlik bilgilerini kullanarak oturum açmayı devre dışı bırakacak şekilde ayarlandı ve yerel güvenlik yetkilisi (LSA) yanlış ayarlanmış.

### <a name="cause-5"></a>Neden 5

VM yalnızca Federal bilgi Işleme standardı (FIPS) ile uyumlu algoritma bağlantılarını kabul edecek şekilde ayarlanmıştır. Bu genellikle Active Directory ilkesi kullanılarak yapılır. Bu nadir bir yapılandırmadır, ancak yalnızca Uzak Masaüstü bağlantıları için FIPS zorlanabilir.

## <a name="before-you-troubleshoot"></a>Sorun gidermeye başlamadan önce

### <a name="create-a-backup-snapshot"></a>Yedekleme anlık görüntüsü oluşturma

Bir yedekleme anlık görüntüsü oluşturmak için, [bir diskte anlık görüntü](../windows/snapshot-copy-managed-disk.md)' daki adımları izleyin.

### <a name="connect-to-the-vm-remotely"></a>VM 'ye uzaktan bağlanma

VM 'ye uzaktan bağlanmak için, [Azure VM sorunlarını gidermek üzere uzak araçları kullanma](remote-tools-troubleshoot-azure-vm-issues.md)bölümündeki yöntemlerden birini kullanın.

### <a name="group-policy-client-service"></a>Grup İlkesi istemci hizmeti

Bu etki alanına katılmış bir sanal makine ise, grup ilkesi Istemci hizmetini, Active Directory Ilkesinin değişikliklerin üzerine yazmasını engellemek için durdurun. Bunu yapmak için aşağıdaki komutu çalıştırın:

```cmd
REM Disable the member server to retrieve the latest GPO from the domain upon start
REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
```

Sorun giderildikten sonra, bu VM 'nin etki alanından en son GPO 'YU almak için etki alanına bağlantı kurma yeteneğini geri yükleyin. Bunu yapmak için aşağıdaki komutları çalıştırın:

```cmd
sc config gpsvc start= auto
sc start gpsvc

gpupdate /force
```

Değişiklik geri çevrildiğinden, Active Directory bir ilkenin soruna neden olduğu anlamına gelir. 

### <a name="workaround"></a>Geçici Çözüm

Bu sorunu geçici olarak çözmek için, NLA 'yı devre dışı bırakmak üzere komut penceresinde aşağıdaki komutları çalıştırın:

```cmd
REM Disable the Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 0
```

Sonra, sanal makineyi yeniden başlatın.

NLA 'yı yeniden etkinleştirmek için aşağıdaki komutu çalıştırın ve ardından VM 'yi yeniden başlatın:

```cmd
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds /t REG_DWORD /d 0 /f

REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f
```

## <a name="troubleshooting"></a>Sorun giderme

### <a name="for-domain-joined-vms"></a>Etki alanına katılmış VM 'Ler için

Bu sorunu gidermek için öncelikle VM 'nin bir DC 'ye bağlanıp bağlanamamadığını ve DC 'nin "sağlıklı" durumuna sahip olup olmadığını ve VM 'den gelen istekleri işleyebileceğini denetleyin.

>[!Note] 
>DC sistem durumunu test etmek için aynı oturum açma sunucusunu paylaşan aynı VNET ve alt ağ üzerinde başka bir sanal makine kullanabilirsiniz.

"VM uzaktan bağlanma" bölümünde yer alan adımlara göre Seri konsol, uzak CMD veya uzak PowerShell kullanarak sorunlu VM 'ye bağlanın.

VM 'nin hangi DC 'ye bağlanacağınızı öğrenmek için konsolunda aşağıdaki komutu çalıştırın: 

```cmd
set | find /i "LOGONSERVER"
```

Ardından, VM ile DC arasındaki güvenli kanalın sistem durumunu kontrol edin. Bunu yapmak için, yükseltilmiş bir PowerShell örneğinde aşağıdaki komutu çalıştırın. Bu komut, güvenli kanalın etkin olup olmadığını belirten bir Boole bayrağı döndürür:

```powershell
Test-ComputerSecureChannel -verbose
```

Kanal bozulur, onarmak için aşağıdaki komutu çalıştırın:

```powershell
Test-ComputerSecureChannel -repair
```

Active Directory ' deki bilgisayar hesabı parolasının VM 'de ve DC 'de güncelleştirildiğinden emin olun:

```powershell
Reset-ComputerMachinePassword -Server "<COMPUTERNAME>" -Credential <DOMAIN CREDENTIAL WITH DOMAIN ADMIN LEVEL>
```

DC ve VM arasındaki iletişim iyi ise, ancak DC 'nin bir RDP oturumu açmak için yeterince iyi olmaması durumunda DC 'yi yeniden başlatmayı deneyebilirsiniz.

Yukarıdaki komutlar etki alanına iletişim sorununu gidermezse, bu VM 'yi etki alanına yeniden ekleyebilirsiniz. Bunu yapmak için şu adımları uygulayın:

1. Aşağıdaki içeriği kullanarak Unjoın. ps1 adlı bir komut dosyası oluşturun ve ardından betiği Azure portal özel bir betik uzantısı olarak dağıtın:

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```
    
    Bu betik, VM 'yi etki alanından kapatıp 10 saniye sonra yeniden başlatır. Daha sonra, etki alanı tarafında bilgisayar nesnesini temizlemeniz gerekir.

2.  Temizleme yapıldıktan sonra bu VM 'yi etki alanına yeniden katın. Bunu yapmak için, aşağıdaki içeriği kullanarak JoinDomain. ps1 adlı bir komut dosyası oluşturun ve ardından betiği Azure portal özel bir betik uzantısı olarak dağıtın: 

    ```cmd
    cmd /c "netdom join <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10"
    ```

    >[!Note] 
    >Bu, belirtilen kimlik bilgilerini kullanarak etki alanındaki VM 'yi birleştirir.

Active Directory kanalı sağlıklı ise, bilgisayar parolası güncellenir ve etki alanı denetleyicisi beklendiği gibi çalışıyorsa, aşağıdaki adımları deneyin.

Sorun devam ederse, etki alanı kimlik bilgisinin devre dışı olup olmadığını kontrol edin. Bunu yapmak için, yükseltilmiş bir komut Istemi penceresi açın ve ardından VM 'nin VM 'de oturum açmak için etki alanı hesaplarını devre dışı bırakmak üzere ayarlanmış olup olmadığını anlamak için aşağıdaki komutu çalıştırın:

```cmd
REG query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds
```

Anahtar **1**olarak ayarlandıysa, sunucunun etki alanı kimlik bilgilerine izin vermek üzere ayarlandığı anlamına gelir. Bu anahtarı **0**olarak değiştirin.

### <a name="for-standalone-vms"></a>Tek başına VM 'Ler için

#### <a name="check-minencryptionlevel"></a>MinEncryptionLevel öğesini denetle

Bir CMD örneğinde, **Minencryptionlevel** kayıt defteri değerini sorgulamak için aşağıdaki komutu çalıştırın:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Kayıt defteri değerine bağlı olarak, aşağıdaki adımları izleyin:

* 4 (FIPS): [FIPS uyumlu algoritma bağlantılarını denetle](#fips-compliant)bölümüne gidin.

* 3 (128 bit şifreleme): Aşağıdaki komutu çalıştırarak önem derecesini **2** olarak ayarlayın:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2 /f
    ```

* 2 (istemci tarafından dikte edildiği için en yüksek şifreleme mümkündür): Aşağıdaki komutu çalıştırarak şifrelemeyi en düşük **1** değerine ayarlamayı deneyebilirsiniz:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 1 /f
    ```
    
Kayıt defterindeki değişikliklerin etkili olması için sanal makineyi yeniden başlatın.

#### <a name="tls-version"></a>TLS sürümü

Sisteme bağlı olarak, RDP, TLS 1,0, 1,1 veya 1,2 (sunucu) protokolünü kullanır. Bu protokollerin sanal makinede nasıl ayarlandığını sorgulamak için bir CMD örneği açın ve aşağıdaki komutları çalıştırın:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled
```

Döndürülen değerler **1**değilse, bu protokolün devre dışı bırakıldığı anlamına gelir. Bu protokolleri etkinleştirmek için aşağıdaki komutları çalıştırın:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
```

Diğer protokol sürümleri için aşağıdaki komutları çalıştırabilirsiniz:

<pre lang="bat">
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
</pre>

> [!Note]
> SCHANNEL hatalarında, Konuk işletim sistemi günlüklerinden SSH/TLS sürüm x. x ' i alın.

#### <a name="fips-compliant"></a>FIPS uyumlu algoritma bağlantılarını denetle

Uzak Masaüstü, yalnızca FIPS uyumlu algoritma bağlantıları kullanmak için zorlanabilir. Bu, bir kayıt defteri anahtarı kullanılarak ayarlanabilir. Bunu yapmak için, yükseltilmiş bir komut Istemi penceresi açın ve ardından aşağıdaki anahtarları sorgulayın:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled
```

Komut **1**döndürürse, kayıt defteri değerini **0**olarak değiştirin.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled /t REG_DWORD /d 0
```

VM üzerinde geçerli MinEncryptionLevel olduğunu denetleyin:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Komut **4**döndürürse, kayıt defteri değerini **2** olarak değiştirin

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2
```

Kayıt defterindeki değişikliklerin etkili olması için sanal makineyi yeniden başlatın.

## <a name="next-steps"></a>Sonraki adımlar

[Win32_TSGeneralSetting sınıfının SetEncryptionLevel yöntemi](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting-setencryptionlevel)

[Sunucu kimlik doğrulaması ve şifreleme düzeylerini yapılandırma](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770833(v=ws.11))

[Win32_TSGeneralSetting sınıfı](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting)
