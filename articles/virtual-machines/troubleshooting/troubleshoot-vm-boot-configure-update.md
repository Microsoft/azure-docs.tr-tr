---
title: "\"Windows hazırlanıyor\" sanal makine başlatması takılmış. Azure 'da bilgisayarınızı kapatmayın \" Microsoft Docs"
description: "\"Windows 'u alma\" bölümünde, VM başlatmasının takılmasına neden olan sorunu gidermeye yönelik adımları izleyin. Bilgisayarınızı kapatmayın.” iletisinde takılıyor"
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: da45e24898bc3b5aead250077af69a61bdb33bab
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749630"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>"Windows hazırlanıyor" sanal makine başlatması takılmış. Azure 'da bilgisayarınızı kapatmayın "

Bu makalede, Microsoft Azure ' de bir Windows sanal makinesini (VM) önyüklediğinizde karşılaşabileceğiniz "hazırlanma" ve "Windows 'a hazırlanma" ekranları açıklanmaktadır. Destek bileti için veri toplamanıza yardımcı olacak adımları sağlar.

 

## <a name="symptoms"></a>Belirtiler

Bir Windows sanal makinesi önyükleme yapmaz. VM 'nin ekran görüntüsünü almak için **önyükleme tanılamayı** KULLANDıĞıNıZDA, VM 'nin "hazırlanıyor" veya "Windows 'U kullanıma alma" iletisini görüntülediğini görebilirsiniz.

![Windows Server 2012 R2 için ileti örneği](./media/troubleshoot-vm-configure-update-boot/message1.png)

![İleti örneği](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Nedeni

Bu sorun genellikle sunucu, yapılandırma değiştirildikten sonra son yeniden başlatma işlemi gerçekleştirdiğinde oluşur. Yapılandırma değişikliği Windows güncelleştirmeleri veya sunucu rolleri/özelliğindeki değişiklikler tarafından başlatılabilir. Windows Update için, güncelleştirmelerin boyutu büyükse, işletim sisteminin değişiklikleri yeniden yapılandırmak için daha fazla zaman gerekir.

## <a name="collect-an-os-memory-dump"></a>Bir işletim sistemi bellek dökümü toplayın

Değişikliklerin işlenmesi beklendikten sonra Sorun çözümlenmezse, bir bellek dökümü dosyası toplayıp desteğe başvurmanız gerekir. Döküm dosyasını toplamak için aşağıdaki adımları izleyin:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>İşletim sistemi diskini bir kurtarma VM 'sine iliştirme

1. Etkilenen VM 'nin işletim sistemi diskinin anlık görüntüsünü bir yedekleme olarak alın. Daha fazla bilgi için bkz. [disk anlık görüntüsü](../windows/snapshot-copy-managed-disk.md).
2. [İşletim sistemi diskini bir kurtarma sanal makinesine ekleyin](../windows/troubleshoot-recovery-disks-portal.md).
3. Kurtarma sanal makinesine uzak masaüstü. 
4. İşletim sistemi diski şifrelenirse, bir sonraki adıma geçmeden önce şifrelemeyi kapatmanız gerekir. Daha fazla bilgi için bkz. [önyükleme YAPıLABILEN VM 'de şifrelenmiş işletim sistemi diskinin şifresini çözme](troubleshoot-bitlocker-boot-error.md#solution).

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Döküm dosyasını bul ve bir destek bileti gönder

1. Kurtarma VM 'sinde, bağlı işletim sistemi diskinde Windows klasörü ' ne gidin. Bağlı işletim sistemi diskine atanan sürücü harfi F ise, F:\windowsadresine gitmeniz gerekir.
2. Memory. dmp dosyasını bulun ve ardından döküm dosyası ile [bir destek bileti gönderebilirsiniz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) . 

Döküm dosyasını bulamıyorsanız, döküm günlüğünü ve seri konsolunu etkinleştirmek için sonraki adımı taşıyın.

### <a name="enable-dump-log-and-serial-console"></a>Döküm günlüğünü ve seri konsolunu etkinleştir

Döküm günlüğünü ve seri konsolunu etkinleştirmek için aşağıdaki betiği çalıştırın.

1. Yükseltilmiş komut Istemi oturumunu açın (yönetici olarak çalıştır).
2. Şu betiği çalıştırın:

    Bu betikte, bağlı işletim sistemi diskine atanan sürücü harfinin F olduğunu varsaytık.  Bunu sanal makinenizde uygun değerle değiştirin.

    ```powershell
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

    1. Bu VM için seçtiğiniz boyuta bağlı olarak RAM 'e kadar bellek ayırmak için diskte yeterli alan olduğundan emin olun.
    2. Yeterli alan yoksa veya bu büyük boyutlu bir VM (G, GS veya E serisi) ise, bu dosyanın oluşturulacağı konumu değiştirebilir ve VM 'ye bağlı olan diğer tüm veri diskine başvurabilirsiniz. Bunu yapmak için aşağıdaki anahtarı değiştirmeniz gerekir:

            reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

            REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
            REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

            reg unload HKLM\BROKENSYSTEM

3. [İşletim sistemi diskini ayırın ve ardından işletim sistemi diskini ETKILENEN VM 'ye yeniden ekleyin](../windows/troubleshoot-recovery-disks-portal.md).
4. VM 'yi başlatın ve seri konsoluna erişin.
5. Bellek dökümünü tetiklemek için, **maskelenemeyen kesme (NMI) Gönder** ' i seçin.
    ![maskelenemeyen kesmenin gönderileceği yer hakkında resim](./media/troubleshoot-vm-configure-update-boot/run-nmi.png)
6. İşletim sistemi diskini bir kurtarma VM 'sine yeniden ekleyin, döküm dosyasını toplayın.

## <a name="contact-microsoft-support"></a>Microsoft destek 'e başvurun

Döküm dosyasını topladıktan sonra, kök nedenini çözümlemek için [Microsoft desteği](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 'ne başvurun.