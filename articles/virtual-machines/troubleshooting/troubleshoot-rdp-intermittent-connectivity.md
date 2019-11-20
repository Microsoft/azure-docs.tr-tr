---
title: Azure VM 'de Uzak Masaüstü bağlantısı genellikle kesiliyor | Microsoft Docs
description: Azure VM 'de Uzak Masaüstü bağlantısı ile ilgili sık gerçekleştirilen bağlantıları nasıl giderebileceğinizi öğrenin.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/24/2018
ms.author: genli
ms.openlocfilehash: be563e39ed1bfa405830999a96d8630b6f8254bb
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057962"
---
# <a name="remote-desktop-disconnects-frequently-in-azure-vm"></a>Azure VM 'de Uzak Masaüstü bağlantısı genellikle kesiliyor

Bu makalede, Azure sanal makinesine (VM) Uzak Masaüstü Protokolü RDP aracılığıyla sık gerçekleştirilen bağlantıların nasıl giderileceği açıklanmaktadır.

> [!NOTE] 
> Azure 'da kaynak oluşturmak ve bunlarla çalışmak için iki farklı dağıtım modeli vardır: [Kaynak Yöneticisi ve klasik](../../azure-resource-manager/resource-manager-deployment-model.md). Bu makalede Kaynak Yöneticisi dağıtım modelinin kullanımı ele alınmaktadır. Klasik dağıtım modelini kullanmak yerine bu modeli Yeni dağıtımlar için kullanmanızı öneririz.

## <a name="symptom"></a>Belirti

Oturumlarınız sırasında aralıklı RDP bağlantısı sorunları ortaya çıkabilir. İlk olarak VM 'ye bağlanabilir, ancak ardından bağlantı düşer.

## <a name="cause"></a>Nedeni

RDP dinleyicisi yanlış yapılandırıldıysanız bu sorun oluşabilir. Genellikle, bu sorun özel bir görüntü kullanan bir VM 'de oluşur.

## <a name="solution"></a>Çözüm

Bu adımları izlemeden önce, etkilenen VM 'nin [işletim sistemi diskinin bir yedek olarak anlık görüntüsünü alın](../windows/snapshot-copy-managed-disk.md) . 

Bu sorunu gidermek için, VM 'nin işletim sistemi diskini bir kurtarma sanal makinesine ekleyerek seri denetimi kullanın veya [VM 'yi çevrimdışı onarın](#repair-the-vm-offline) .

### <a name="serial-control"></a>Seri denetim

1. Bağlanma [seri konsol ve örnek CMD Aç](./serial-console-windows.md). Ardından, RDP yapılandırmasını sıfırlamak için aşağıdaki komutları çalıştırın. VM 'niz üzerinde seri konsol etkinleştirilmemişse, bir sonraki adıma gidin.
2. RDP güvenlik katmanını 0 olarak düşürün. Bu ayarda, sunucu ve istemci arasındaki iletişimler yerel RDP şifrelemesini kullanır.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
3. Eski RDP istemcilerinin bağlanmasına izin vermek için şifreleme düzeyini en düşük ayara düşürün.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
4. İstemci bilgisayarın kullanıcı yapılandırmasını yüklemek için RDP 'yi ayarlayın.

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
5. RDP etkin tut denetimini etkinleştirin:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
6. RDP yeniden bağlanma denetimini ayarlayın:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f
7. RDP oturum saati denetimini ayarlama:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
8. RDP bağlantı kesme saati denetimini ayarlayın: 

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f
        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
9. RDP bağlantı saati denetimini ayarlama:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
10. RDP oturum boşta kalma süresi denetimini ayarlayın:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxIdleTime' /t REG_DWORD /d 0 /f
11. "En fazla eşzamanlı bağlantı sayısını sınırla" denetimini ayarlayın:

        REG ADD "HKLM\SYSTEM\CurrentControlSet\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d 4294967295 /f

12. VM 'yi yeniden başlatın ve RDP kullanarak bu sunucuya bağlanmayı yeniden deneyin.

### <a name="repair-the-vm-offline"></a>VM'yi çevrimdışı onarın

1. [İşletim sistemi diskini bir kurtarma VM'si ekleme](../windows/troubleshoot-recovery-disks-portal.md).
2. İşletim sistemi diskini bir kurtarma VM'si bağlandıktan sonra disk olarak işaretlenmiş emin olun **çevrimiçi** Disk Yönetimi Konsolu'nda. Ekli işletim sistemi diski için atanan sürücü harfini unutmayın.
3. Eklediğiniz işletim sistemi diskinde, **\Windows\System32\Config** klasörüne gidin. Geri almanın gerekli olması durumunda bu klasördeki tüm dosyaları yedek olarak kopyalayın.
4. Kayıt Defteri Düzenleyicisi 'Ni (Regedit. exe) başlatın.
5. **HKEY_LOCAL_MACHINE** anahtarını seçin. Menüde **Dosya** > **yükleme Hive**: ' ı seçin.
6. Eklediğiniz işletim sistemi diskinde **\Windows\system32\config\system** klasörüne gidin. Hive adı için **brokensystem**girin. Yeni kayıt defteri kovanı, **HKEY_LOCAL_MACHINE** anahtarının altında görüntülenir. Ardından, **HKEY_LOCAL_MACHINE** anahtarı altında **\windows\system32\config\software** yazılım kovanını yükleyin. Hive yazılımının adı için **Brokensoftware**' i girin. 
7. Yükseltilmiş bir komut Istemi penceresi açın (**yönetici olarak çalıştır**) ve kalan adımlarda RDP yapılandırmalarının sıfırlanması için komutları çalıştırın. 
8. Sunucu ve istemci arasındaki iletişimin yerel RDP şifrelemesini kullanması için RDP güvenlik katmanını 0 olarak düşürün:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'SecurityLayer' /t REG_DWORD /d 0 /f
9. Eski RDP istemcilerinin bağlanmasına izin vermek için şifreleme düzeyini en düşük ayara düşürün:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MinEncryptionLevel' /t REG_DWORD /d 1 /f
10. İstemci makinenin Kullanıcı yapılandırmasını yüklemek için RDP 'yi ayarlayın.

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fQueryUserConfigFromLocalMachine' /t REG_DWORD /d 1 /f
11. RDP etkin tut denetimini etkinleştirin:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'KeepAliveTimeout' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveEnable' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'KeepAliveInterval' /t REG_DWORD /d 1 /f
12. RDP yeniden bağlanma denetimini ayarlayın:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 
        
        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritReconnectSame' /t REG_DWORD /d 0 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fReconnectSame' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v 'fDisableAutoReconnect' /t REG_DWORD /d 0 /f

13. RDP oturum saati denetimini ayarlama:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxSessionTime' /t REG_DWORD /d 1 /f
14. RDP bağlantı kesme saati denetimini ayarlayın:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxDisconnectionTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxDisconnectionTime' /t REG_DWORD /d 0 /f
15. RDP bağlantı saati denetimini ayarlama:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxConnectionTime' /t REG_DWORD /d 0 /f
16. RDP oturum boşta kalma süresi denetimini ayarlayın:     REG ADD "Hklm\brokensystem\controlset001\control\terminalserver\winstations\rdp-TCP"/v ' Finheritmaxboştazamanı '/t REG_DWORD/d 1/f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'fInheritMaxIdleTime' /t REG_DWORD /d 1 /f 

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v ' MaxIdleTime' /t REG_DWORD /d 0 /f
17. "En fazla eşzamanlı bağlantı sayısını sınırla" denetimini ayarlayın:

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f

        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v 'MaxInstanceCount' /t REG_DWORD /d ffffffff /f
18. VM 'yi yeniden başlatın ve RDP kullanarak bu sunucuya bağlanmayı yeniden deneyin.

## <a name="need-help"></a>Yardım mı gerekiyor? 
Desteğe başvurun. Yine de yardıma ihtiyacınız varsa sorununuzun hızla çözülmesini sağlamak için [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).





