---
title: DHCP devre dışı olduğundan, Azure sanal makinelere uzaktan bağlanamıyor | Microsoft Docs
description: RDP sorunlarını gidermeyi öğrenin DHCP istemci hizmeti tarafından neden olan sorun, Microsoft Azure'da devre dışı. | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: ef44931cc3b36bcab64a2de840d9264c1b8fdedb
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058016"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>DHCP İstemci hizmetini devre dışı olduğundan, Azure sanal makinelerinde RDP olamaz

Bu makalede bir sorun sanal DHCP İstemci hizmetini devre dışı bırakıldıktan sonra Uzak Masaüstü Azure Windows sanal makinelerin (VM'ler) olamaz.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="symptoms"></a>Belirtiler
VM'yi DHCP İstemci hizmetini devre dışı olduğundan, Azure'da bir VM ile RDP bağlantısı yapamazsınız. Ne zaman iade ekran [önyükleme tanılaması](../troubleshooting/boot-diagnostics.md) Azure Portal'da, VM normal önyüklenir ve kimlik bilgileri oturum açma ekranında bekleyeceği görürsünüz. Uzaktan olay günlüklerini VM ile Olay Görüntüleyicisi'ni kullanarak görüntüleyin. DHCP istemci hizmeti kullanmaya değil veya başlatılamıyor görürsünüz. Aşağıdaki örnek bir oturum:

**Günlük adı**: Sistem </br>
**Kaynak**: Hizmet denetimi Yöneticisi </br>
**Tarih**: 12/16/2015 11:19:36 </br>
**Olay kimliği**: 7022 </br>
**Görev kategorisi**: Yok. </br>
**Düzey**: Hata </br>
**Anahtar sözcükler**: Klasik</br>
**Kullanıcı**: Yok </br>
**Bilgisayar**: myvm.cosotos.com</br>
**Açıklama**: DHCP Istemci hizmeti başlatılırken askıda kaldı.</br>

Resource Manager Vm'leri için olay 7022 aşağıdaki komutu kullanarak günlükleri için sorgu seri erişim konsol özelliğini kullanabilirsiniz:

    wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more

Klasik VM'ler için çevrimdışı modda çalışır ve el ile günlükleri toplama gerekecektir.

## <a name="cause"></a>Nedeni

DHCP istemci hizmeti VM üzerinde çalışmıyor.

> [!NOTE]
> Bu makale, yalnızca DHCP İstemci hizmetini ve DHCP sunucusu için geçerlidir.

## <a name="solution"></a>Çözüm

Bu adımları gerçekleştirmeden önce etkilenen makinenin işletim sistemi diskinin anlık yedekleyin. Daha fazla bilgi için [bir diskin anlık görüntüsünü alma](../windows/snapshot-copy-managed-disk.md).

Bu sorunu çözmek için DHCP etkinleştirmek için seri denetimi kullanın veya [sıfırlama ağ arabirimi](reset-network-interface.md) VM için.

### <a name="use-serial-control"></a>Seri denetimini kullanma

1. Bağlanma [seri konsol ve örnek CMD Aç](serial-console-windows.md#use-cmd-or-powershell-in-serial-console).
). Seri konsol sanal makinenizde etkinleştirilmemiş olmadığını [sıfırlama ağ arabirimi](reset-network-interface.md).
2. DHCP ağ arabiriminde devre dışı bırakılıp bırakılmadığını kontrol edin:

        sc query DHCP
3. DHCP durdurulmuşsa hizmeti başlatmayı deneyin

        sc start DHCP

4. Hizmet yeniden hizmeti başarıyla başlatıldığını emin olmak için sorgu.

        sc query DHCP

    Deneyin VM bağlanmak ve sorunun çözülüp çözülmediğine bakın.
5. Hizmet başlatılmazsa, aldığınız hata iletisini temel alarak aşağıdaki uygun çözümü kullanın:

    | Hata  |  Çözüm |
    |---|---|
    | 5 - ERİŞİM REDDEDİLDİ  | Bkz: [DHCP istemci hizmeti, erişim reddedildi hatası nedeniyle durdurulduğunu](#dhcp-client-service-is-stopped-because-of-an-access-denied-error).  |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT   | Bkz: [DHCP istemci hizmeti kilitlenmesine veya yanıt vermemeye başlıyor](#dhcp-client-service-crashes-or-hangs).  |
    | 1058 - ERROR_SERVICE_DISABLED  | Bkz: [DHCP istemci hizmeti devre dışı](#dhcp-client-service-is-disabled).  |
    | 1059 - ERROR_CIRCULAR_DEPENDENCY  |[Destek ekibiyle iletişime geçin](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) sorununuzun hızlıca çözülebilmesi alınamıyor.   |
    | 1067 - ERROR_PROCESS_ABORTED |Bkz: [DHCP istemci hizmeti kilitlenmesine veya yanıt vermemeye başlıyor](#dhcp-client-service-crashes-or-hangs).   |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL   | [Destek ekibiyle iletişime geçin](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) sorununuzun hızlıca çözülebilmesi alınamıyor.  |
    |1069 - ERROR_SERVICE_LOGON_FAILED   |  Bkz: [DHCP istemci hizmeti oturum açma hatası nedeniyle başarısız oluyor](#dhcp-client-service-fails-because-of-logon-failure) |
    | 1070 - ERROR_SERVICE_START_HANG  | Bkz: [DHCP istemci hizmeti kilitlenmesine veya yanıt vermemeye başlıyor](#dhcp-client-service-crashes-or-hangs).  |
    | 1077 - ERROR_SERVICE_NEVER_STARTED  | Bkz: [DHCP istemci hizmeti devre dışı](#dhcp-client-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   | [Destek ekibiyle iletişime geçin](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) sorununuzun hızlıca çözülebilmesi alınamıyor.  |
    |1053 | [Destek ekibiyle iletişime geçin](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) sorununuzun hızlıca çözülebilmesi alınamıyor.  |


#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>DHCP istemci hizmeti erişim reddedildi hatası nedeniyle durduruldu

1. Bağlanma [seri konsol](serial-console-windows.md) ve PowerShell örneği açın.
2. İşlem izleme aracı, aşağıdaki komutu çalıştırarak yükleyin:

   ```powershell
   remove-module psreadline
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip"
   $destination = "c:\temp\ProcessMonitor.zip"
   $wc = New-Object System.Net.WebClient
   $wc.DownloadFile($source,$destination)
   ```
3. Şimdi bir **procmon** izleme:

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML
   ```
4. Oluşturan hizmet başlatarak problemi **erişim reddedildi** ileti:

   ```
   sc start DHCP
   ```

   Başarısız olduğunda, işlem İzleyici Sonlandır:

   ```
   procmon /Terminate
   ```
5. Toplama **c:\temp\ProcMonTrace.PML** dosyası:

    1. [VM'ye veri diski](../windows/attach-managed-disk-portal.md
).
    2. Seri konsol dosyayı yeni bir sürücüye kopyalayın kullanın. Örneğin, `copy C:\temp\ProcMonTrace.PML F:\`. Bu komutta, F bağlanan veri diskinin sürücü harfidir. Harf uygun olarak doğru değeri ile değiştirin.
    3. Veri sürücüsü ayırın ve ardından, çalışan işlem İzleyicisi ubstakke yüklü olan bir sanal makine ekleyin.

6. Açık **ProcMonTrace.PML** VM üzerinde çalışan işlem İzleyicisi'ni kullanarak. Ardından filtre **sonucudur erişim reddedildi**aşağıdaki ekran görüntüsünde gösterildiği gibi:

    ![İşlem İzleyicisi sonuca göre filtreleme](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. Kayıt defteri anahtarlarını, klasörlere veya çıktı dosyaları düzeltin. Genellikle, hizmette kullanılan oturum açma hesabı ACL bu nesnelere erişme izni olmadığından, bu soruna neden olur. Oturum açma hesabı için doğru ACL izni belirlemek için iyi bir VM'de kontrol edebilirsiniz.

#### <a name="dhcp-client-service-is-disabled"></a>DHCP istemci hizmeti devre dışı bırakıldı

1. Hizmet, varsayılan başlangıç değerine geri yükleme:

   ```
   sc config DHCP start= auto
   ```

2. Hizmetini başlatın:

   ```
   sc start DHCP
   ```

3. Hizmet durumunu yeniden emin olmak için sorgu çalışıyor:

   ```
   sc query DHCP
   ```

4. Uzak Masaüstü kullanarak sanal Makineye bağlanmayı deneyin.

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>DHCP istemci hizmeti oturum açma hatası nedeniyle başarısız olur.

1. Bu hizmet başlangıç hesabını değiştirdiyseniz, bu sorun oluştuğu için hesabın varsayılan durumuna geri döndürme:

        sc config DHCP obj= 'NT Authority\Localservice'
2. Hizmetini başlatın:

        sc start DHCP
3. Uzak Masaüstü kullanarak sanal Makineye bağlanmayı deneyin.

#### <a name="dhcp-client-service-crashes-or-hangs"></a>DHCP istemci hizmeti kilitlenmesine veya yanıt vermemeye başlıyor

1. Hizmet durumunu takılıyorsa **başlangıç** veya **durdurma** durum, hizmeti durdurmak deneyin:

        sc stop DHCP
2. Kendi 'svchost' kapsayıcı hizmeti ayırma:

        sc config DHCP type= own
3. Hizmetini başlatın:

        sc start DHCP
4. Hizmet hala başlatılmazsa [desteğe](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>VM'yi çevrimdışı onarın

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>İşletim sistemi diskini bir kurtarma VM'si ekleme

1. [İşletim sistemi diskini bir kurtarma VM'si ekleme](../windows/troubleshoot-recovery-disks-portal.md).
2. Kurtarma VM'sini bir Uzak Masaüstü Bağlantısı'nı başlatın. Bağlı disk olarak işaretlenmiş olduğundan emin olun **çevrimiçi** Disk Yönetimi Konsolu'nda. Ekli işletim sistemi diski için atanan sürücü harfini unutmayın.
3.  Yükseltilmiş bir komut istemi örneği açın (**yönetici olarak çalıştır**). Ardından aşağıdaki betiği çalıştırın. Bu betik, ekli işletim sistemi diski için atanan sürücü harfini olduğunu varsayar **F**. Vm'nizde değer harf uygun şekilde değiştirin.

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

    REM Set default values back on the broken service
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v type /t REG_DWORD /d 16 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v type /t REG_DWORD /d 16 /f

    reg unload HKLM\BROKENSYSTEM
    ```

4. [İşletim sistemi diskini ve VM yeniden](../windows/troubleshoot-recovery-disks-portal.md). Daha sonra sorun çözülmüş olup olmadığını denetleyin.

## <a name="next-steps"></a>Sonraki adımlar

Hala yardıma ihtiyacınız varsa [desteğe](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) sorununuzu için.