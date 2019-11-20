---
title: Uzak Masaüstü lisans sunucusu, bir Azure VM 'sine bağlandığınızda kullanılamaz | Microsoft Docs
description: Kullanılabilir Uzak Masaüstü lisans sunucusu olmadığından RDP hata sorunlarını nasıl giderebileceğinizi öğrenin | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 154160f9a3fbd485ee6383bf3d5ff1c291520a75
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088516"
---
# <a name="remote-desktop-license-server-isnt-available-when-you-connect-to-an-azure-vm"></a>Uzak Masaüstü lisans sunucusu, bir Azure VM 'ye bağlandığınızda kullanılamaz

Bu makale, bir Azure sanal makinesine (VM) bağlanamadığınızda sorunu çözmenize yardımcı olur çünkü bir Uzak Masaüstü lisans sunucusu, lisans sağlamak için kullanılabilir değildir.

## <a name="symptoms"></a>Belirtiler

Bir sanal makineye (VM) bağlanmaya çalıştığınızda, aşağıdaki senaryolarla karşılaşırsınız:

- VM ekran görüntüsü, işletim sisteminin tam olarak yüklendiğini ve kimlik bilgilerini beklediğini gösterir.
- Microsoft Uzak Masaüstü Protokolü (RDP) bağlantısı yapmayı denediğinizde aşağıdaki hata iletilerini alırsınız:

  - Bir lisans sağlamak için kullanılabilir Uzak Masaüstü lisans sunucusu olmadığından uzak oturumun bağlantısı kesildi.

  - Kullanılabilir Uzak Masaüstü lisans sunucusu yok. Uzak Masaüstü Hizmetleri, bu bilgisayar yetkisiz kullanım süresini aştığından ve en azından geçerli bir Windows Server 2008 lisans sunucusu ile iletişim kurmadığı için çalışmayı durduracak. Lisans tanılaması 'nı kullanmak üzere RD Oturumu Ana Bilgisayarı sunucu yapılandırması 'nı açmak için bu iletiyi seçin.

Ancak, bir yönetim oturumu kullanarak VM 'ye normal şekilde bağlanabilirsiniz:

```
mstsc /v:<Server>[:<Port>] /admin
```

## <a name="cause"></a>Nedeni

Bir Uzak Masaüstü lisans sunucusu, uzak bir oturumu başlatmak için lisans sağlayamıyorken bu sorun oluşur. VM 'de Uzak Masaüstü Oturumu Ana Bilgisayarı bir rol ayarlanmış olsa bile, bu, birkaç senaryo nedeniyle oluşabilir:

- Ortamda hiçbir zaman Uzak Masaüstü lisans rolü yoktu ve yetkisiz kullanım süresi 180 gün kaldı.
- Ortama bir Uzak Masaüstü Lisansı yüklendi, ancak hiç etkinleştirilmedi.
- Ortamdaki bir uzak masaüstü lisansında bağlantıyı kurmak için eklenen Istemci erişim lisansları (CAL) yoktur.
- Ortama bir Uzak Masaüstü Lisansı yüklendi. Kullanılabilir Cal var, ancak bunlar düzgün şekilde yapılandırılmamış.
- Uzak Masaüstü lisansında Cal 'Ler vardır ve bu lisans etkinleştirilmiştir. Ancak, Uzak Masaüstü lisans sunucusundaki bazı diğer sorunlar, ortamın ortamda lisans sağlamamasını önler.

## <a name="solution"></a>Çözüm

Bu sorunu çözmek için, [işletim sistemi diskini yedekleyin](../windows/snapshot-copy-managed-disk.md) ve şu adımları izleyin:

1. Yönetim oturumu kullanarak VM 'ye bağlanın:

   ```
   mstsc /v:<Server>[:<Port>] /admin
   ```

    VM 'ye bir yönetim oturumu kullanarak bağlanamıyorsanız, VM 'ye aşağıdaki şekilde erişmek için [sanal makine seri konsolunu Azure 'da](serial-console-windows.md) kullanabilirsiniz:

    1. **Destek & sorun giderme** > **seri konsol (Önizleme)** öğesini seçerek seri konsoluna erişin. Özellik VM 'de etkinse VM 'yi başarıyla bağlayabilirsiniz.

    2. Bir CMD örneği için yeni bir kanal oluşturun. Kanalı başlatmak ve kanal adını almak için **cmd** girin.

    3. CMD örneğini çalıştıran kanala geçin. Bu durumda, Kanal 1 olmalıdır:

       ```
       ch -si 1
       ```

    4. Yeniden **gir** ' i SEÇIN ve VM için geçerli bir Kullanıcı adı ve parola, yerel veya etkı alanı kimliği girin.

2. VM 'nin etkin bir Uzak Masaüstü Oturumu Ana Bilgisayarı rolüne sahip olup olmadığını denetleyin. Rol etkinse, düzgün çalıştığından emin olun. Yükseltilmiş bir CMD örneği açın ve şu adımları izleyin:

    1. Uzak Masaüstü Oturumu Ana Bilgisayarı rolünün durumunu denetlemek için aşağıdaki komutu kullanın:

       ```
        reg query "HKLM\SOFTWARE\Microsoft\ServerManager\ServicingStorage\ServerComponentCache\RDS-RD-Server" /v InstallState
        ```

        Bu komut 0 değerini döndürürse, rolün devre dışı bırakıldığı anlamına gelir ve 3. adıma gidebilirsiniz.

    2. İlkeleri denetlemek ve gerektiğinde yeniden yapılandırmak için aşağıdaki komutu kullanın:

       ```
        reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode reg query "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers
       ```

        **LicensingMode** değeri, Kullanıcı başına 4 ' ten farklı bir değere ayarlanırsa, değeri 4 ' e ayarlayın:

         ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\RCM\Licensing Core" /v LicensingMode /t REG_DWORD /d 4
        ```

       Belirten **Edlicenseservers** değeri yoksa ya da yanlış lisans sunucusu bilgileri varsa, aşağıdaki gibi değiştirin:

       ```
        reg add "HKLM\SYSTEM\CurrentControlSet\Services\TermService\Parameters" /v SpecifiedLicenseServers /t REG_MULTI_SZ /d "<FQDN / IP License server>"
       ```

    3. Kayıt defterinde herhangi bir değişiklik yaptıktan sonra, sanal makineyi yeniden başlatın.

    4. Cal 'Ler yoksa Uzak Masaüstü Oturumu Ana Bilgisayarı rolünü kaldırın. Daha sonra RDP, normal olarak ayarlanır. VM ile yalnızca iki eşzamanlı RDP bağlantısına izin verir:

        ```
       dism /ONLINE /Disable-feature /FeatureName:Remote-Desktop-Services
        ```

        VM, Uzak Masaüstü lisans rolüne sahipse ve kullanılmıyorsa, bu rolü de kaldırabilirsiniz:

       ```
        dism /ONLINE /Disable-feature /FeatureName:Licensing
       ```

    5. VM 'nin Uzak Masaüstü lisans sunucusuna bağlanabildiğinizden emin olun. VM ve lisans sunucusu arasındaki bağlantı noktası 135 bağlantısını test edebilirsiniz: 

       ```
       telnet <FQDN / IP License Server> 135
       ```

3. Ortamda Uzak Masaüstü lisans sunucusu yoksa ve isterseniz, bir [Uzak Masaüstü Lisans rol hizmeti yükleyebilirsiniz](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731765(v=ws.11)). Ardından [RDS lisansını yapılandırın](https://techcommunity.microsoft.com/t5/Ask-The-Performance-Team/RD-Licensing-Configuration-on-Windows-Server-2012/ba-p/375383).

4. Bir Uzak Masaüstü lisans sunucusu yapılandırıldıysa ve sağlıklı ise, Uzak Masaüstü lisans sunucusunun Cal 'Ler ile etkinleştirildiğinden emin olun.

## <a name="need-help-contact-support"></a>Yardım mı gerekiyor? Desteğe başvurun

Hala yardıma ihtiyacınız varsa, sorununuzu çözmeden yararlanmak için [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .
