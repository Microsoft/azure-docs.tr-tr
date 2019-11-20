---
title: Azure 'da bir Windows sanal makinesinde RDP genel hatası giderme | Microsoft Docs
description: Azure 'da bir Windows sanal makinesinde RDP genel hatası giderme hakkında bilgi edinin | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 7fc0fbf3362d18284ad6a80afa6396b6be1270a9
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058010"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>Azure VM 'de RDP genel hatasıyla ilgili sorunları giderme

Bu makalede, Azure 'da bir Windows sanal makinesine (VM) bir Uzak Masaüstü Protokolü (RDP) bağlantısı yaptığınızda karşılaşabileceğiniz genel bir hata açıklanır.

## <a name="symptom"></a>Belirti

Azure 'da bir Windows VM 'sine RDP bağlantısı yaptığınızda aşağıdaki genel hata iletisini alabilirsiniz:

**Uzak Masaüstü aşağıdaki nedenlerden biri için uzak bilgisayara bağlanamıyor:**

1. **Uzaktan erişim sunucusu için etkin değil**

2. **Uzak bilgisayar kapalı**

3. **Uzak bilgisayarın ağda kullanılamaz**

**Uzak bilgisayarın açık ve ağa bağlı ve Uzaktan erişim etkinleştirildiğinden emin olun.**

## <a name="cause"></a>Nedeni

Bu sorun aşağıdaki nedenlerle oluşabilir:

### <a name="cause-1"></a>Neden 1

RDP bileşeni aşağıdaki şekilde devre dışıdır:

- Bileşen düzeyinde
- Dinleyici düzeyinde
- Terminal sunucusunda
- Uzak Masaüstü Oturumu Ana Bilgisayarı rolünde

### <a name="cause-2"></a>Neden 2

Uzak Masaüstü Hizmetleri (TermService) çalışmıyor.

### <a name="cause-3"></a>Neden 3

RDP dinleyicisi yanlış yapılandırılmış.

## <a name="solution"></a>Çözüm

Bu sorunu çözmek için, [işletim sistemi diskini yedekleyin](../windows/snapshot-copy-managed-disk.md)ve [işletim sistemi DISKINI bir kurtarma VM 'sine bağlayın](troubleshoot-recovery-disks-portal-windows.md)ve ardından adımları izleyin.

### <a name="serial-console"></a>Seri Konsol

#### <a name="step-1-open-cmd-instance-in-serial-console"></a>1\. adım: Seri konsol CMD örneğini aç

1. **Destek & sorun giderme** > **seri konsol (Önizleme)** öğesini seçerek [seri konsoluna](serial-console-windows.md) erişin. Özellik VM 'de etkinse VM 'yi başarıyla bağlayabilirsiniz.

2. Bir CMD örneği için yeni bir kanal oluşturun. Kanal adını almak için kanalı başlatmak üzere **cmd** yazın.

3. CMD örneğini çalıştıran kanala geçiş yapın, bu durumda Kanal 1 olmalıdır.

   ```
   ch -si 1
   ```

#### <a name="step-2-check-the-values-of-rdp-registry-keys"></a>2\. adım: RDP kayıt defteri anahtarlarının değerlerini kontrol edin:

1. RDP 'nin ilkeler tarafından devre dışı bırakılıp bırakılmadığını kontrol edin.

      ```
      REM Get the local policy 
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server " /v fDenyTSConnections

      REM Get the domain policy if any
      reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
      ```

      - Etki alanı ilkesi varsa, yerel ilkedeki kurulumun üzerine yazılır.
      - Etki alanı ilkesi RDP 'nin devre dışı bırakıldığını belirtir (1), AD ilkesini etki alanı denetleyicisinden güncelleştirin.
      - Etki alanı ilkesi RDP 'nin etkin olduğunu belirtir (0), güncelleştirme gerekmez.
      - Etki alanı ilkesi yoksa ve yerel ilke RDP 'nin devre dışı bırakıldığını belirtir (1), aşağıdaki komutu kullanarak RDP 'yi etkinleştirin: 
      
            reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
                  

2. Terminal sunucusunun geçerli yapılandırmasını denetleyin.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
      ```

      Komut 0 döndürürse, Terminal sunucusu devre dışıdır. Ardından, Terminal sunucusunu şu şekilde etkinleştirin:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

3. Sunucu bir Terminal sunucusu çiftliğinde (RDS veya Citrix), Terminal sunucusu modülü boşalt moduna ayarlanır. Terminal sunucusu modülünün geçerli modunu kontrol edin.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

      Komut 1 döndürürse, Terminal sunucusu modülü boşalt moduna ayarlanır. Ardından, modülü çalışma moduna aşağıdaki şekilde ayarlayın:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

4. Terminal sunucusuna bağlanıp bağlanamaıp bağlanamayacağını denetleyin.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

      Komut 1 döndürürse, Terminal sunucusuna bağlanamazsınız. Ardından, bağlantıyı aşağıdaki şekilde etkinleştirin:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```
5. RDP dinleyicisinin geçerli yapılandırmasını denetleyin.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

      Komut 0 döndürürse, RDP dinleyicisi devre dışıdır. Ardından, dinleyiciyi aşağıdaki gibi etkinleştirin:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

6. RDP dinleyicisine bağlanıp bağlanamayacağını denetleyin.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   Komut 1 döndürürse, RDP dinleyicisine bağlanamazsınız. Ardından, bağlantıyı aşağıdaki şekilde etkinleştirin:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

7. VM’yi yeniden başlatın.

8. Yazarak `exit`cmd örneğinden çıkın ve sonra iki kez **ENTER** tuşuna basın.

9. Yazarak `restart`VM 'yi yeniden başlatın ve VM 'ye bağlanın.

Sorun devam ediyorsa 2. adıma geçin.

#### <a name="step-2-enable-remote-desktop-services"></a>2\. adım: Uzak Masaüstü Hizmetleri 'ni etkinleştir

Daha fazla bilgi için bkz. [Uzak Masaüstü Hizmetleri bir Azure VM üzerinde başlamıyor](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>3\. adım: RDP dinleyicisini Sıfırla

Daha fazla bilgi için bkz. [Azure VM 'de Uzak Masaüstü bağlantısı kesiliyor](troubleshoot-rdp-intermittent-connectivity.md).

### <a name="offline-repair"></a>Çevrimdışı onarma

#### <a name="step-1-turn-on-remote-desktop"></a>1\. adım: Uzak masaüstünü aç

1. [İşletim sistemi diskini bir kurtarma VM'si ekleme](../windows/troubleshoot-recovery-disks-portal.md).
2. Kurtarma VM'sini bir Uzak Masaüstü Bağlantısı'nı başlatın.
3. Disk olarak işaretlenmiş olduğundan emin olun **çevrimiçi** Disk Yönetimi Konsolu'nda. Ekli işletim sistemi diski için atanan sürücü harfini unutmayın.
4. Kurtarma VM'sini bir Uzak Masaüstü Bağlantısı'nı başlatın.
5. Yükseltilmiş bir komut istemi oturumu açın (**yönetici olarak çalıştır**). Aşağıdaki komut dosyalarını çalıştırın. Bu betikte ekli işletim sistemi diski için atanan sürücü harfini f Değiştir VM'niz için uygun değeri bu sürücü harfiyle olduğunu varsayıyoruz.

      ```
      reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv 
      reg load HKLM\BROKENSOFTWARE F:\windows\system32\config\SOFTWARE.hiv 
 
      REM Ensure that Terminal Server is enabled 

      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 

      REM Ensure Terminal Service is not set to Drain mode 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 

      REM Ensure Terminal Service has logon enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 

      REM Ensure the RDP Listener is not disabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 

      REM Ensure the RDP Listener accepts logons 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 

      REM RDP component is enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0 /f 

      reg unload HKLM\BROKENSYSTEM 
      reg unload HKLM\BROKENSOFTWARE 
      ```

6. VM etki alanına katılmış ise, RDP 'yi devre dışı bırakamayacak bir grup ilkesi olup olmadığını görmek için aşağıdaki kayıt defteri anahtarını kontrol edin. 

      ```
      HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
      ```

      Bu anahtar değer 1 olarak ayarlanırsa, RDP ilke tarafından devre dışı bırakılır. GPO ilkesi aracılığıyla uzak masaüstü 'Nü etkinleştirmek için, etki alanı denetleyicisinden aşağıdaki ilkeyi değiştirin:

   
      **Bilgisayar Yapılandırması \ şablonlar:**

      İlke definitions\Windows uzak masaüstü Hizmetleri\uzak Masaüstü oturumu Host\connections\kullanıcıların Uzak Masaüstü Hizmetleri kullanarak uzaktan bağlanmasına izin ver
  
1. Diski Kurtarma VM 'sinden ayırın.
1. [Diskten yeni BIR VM oluşturun](../windows/create-vm-specialized.md).

Sorun devam ediyorsa 2. adıma geçin.

#### <a name="step-2-enable-remote-desktop-services"></a>2\. adım: Uzak Masaüstü Hizmetleri 'ni etkinleştir

Daha fazla bilgi için bkz. [Uzak Masaüstü Hizmetleri bir Azure VM üzerinde başlamıyor](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>3\. adım: RDP dinleyicisini Sıfırla

Daha fazla bilgi için bkz. [Azure VM 'de Uzak Masaüstü bağlantısı kesiliyor](troubleshoot-rdp-intermittent-connectivity.md).

## <a name="need-help-contact-support"></a>Yardım mı gerekiyor? Desteğe başvurun

Yine de yardıma ihtiyacınız varsa sorununuzun hızla çözülmesini sağlamak için [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
