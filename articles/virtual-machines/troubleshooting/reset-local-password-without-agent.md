---
title: Azure Agent olmadan yerel Windows parolasını sıfırlama | Microsoft Docs
description: Azure Konuk Aracısı yüklü olmadığında veya bir VM 'de çalışmadığı zaman yerel bir Windows Kullanıcı hesabının parolasını sıfırlama
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.assetid: cf353dd3-89c9-47f6-a449-f874f0957013
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: genli
ms.openlocfilehash: 6faab5bffaddbbd5d8deb9c3834bf3d8fe3e3445
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058658"
---
# <a name="reset-local-windows-password-for-azure-vm-offline"></a>Azure VM için yerel Windows parolasını çevrimdışına sıfırlama
Azure 'da bir sanal makinenin yerel Windows parolasını [Azure Portal veya](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Azure Konuk aracısının yüklü olduğu Azure PowerShell ' i kullanarak sıfırlayabilirsiniz. Bu yöntem, bir Azure VM için parola sıfırlamanın birincil yoludur. Azure Konuk Aracısı yanıt vermeyen veya özel bir görüntü yükledikten sonra yüklenemediğinden sorunlarla karşılaşırsanız, bir Windows parolasını el ile sıfırlayabilirsiniz. Bu makalede, kaynak işletim sistemi sanal diskini başka bir sanal makineye ekleyerek yerel hesap parolasının nasıl sıfırlanacağı açıklanır. Bu makalede açıklanan adımlar Windows etki alanı denetleyicileri için geçerlidir. 

> [!WARNING]
> Bu işlemi yalnızca son çare olarak kullanın. [Azure Portal veya önce Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) kullanarak bir parolayı sıfırlamayı deneyin.

## <a name="overview-of-the-process"></a>İşleme genel bakış
Azure Konuk aracısına erişim olmadığında Azure 'da bir Windows sanal makinesi için yerel parola sıfırlama işlemi gerçekleştirmeye yönelik temel adımlar aşağıdaki gibidir:

1. Etkilenen VM 'yi durdurun.
1. VM 'nin işletim sistemi diski için bir anlık görüntü oluşturun.
1. Anlık görüntüden işletim sistemi diskinin bir kopyasını oluşturun.
1. Kopyalanmış işletim sistemi diskini başka bir Windows sanal makinesine ekleyip bağlayın ve ardından diskte bazı yapılandırma dosyaları oluşturun. Dosyalar, parolayı sıfırlamanıza yardımcı olur.
1. Kopyalanmış işletim sistemi diskini sorun giderme VM 'sinden çıkarın ve ayırın.
1. Etkilenen VM 'nin işletim sistemi diskini değiştirin.

## <a name="detailed-steps-for-the-vm-with-resource-manager-deployment"></a>Kaynak Yöneticisi dağıtımı ile VM için ayrıntılı adımlar

> [!NOTE]
> Adımlar Windows etki alanı denetleyicileri için geçerlidir. Yalnızca tek başına sunucuda veya bir etki alanının üyesi olan bir sunucuda çalışmaktadır.

Aşağıdaki adımları denemeden önce [Azure Portal veya Azure PowerShell](reset-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) kullanarak her zaman bir parolayı sıfırlamayı deneyin. Başlamadan önce VM 'nizin yedeğine sahip olduğunuzdan emin olun.

1. Etkilenen VM 'nin işletim sistemi diski için bir anlık görüntü alın, anlık görüntüden bir disk oluşturun ve ardından diski bir sorun giderme VM 'sine bağlayın. Daha fazla bilgi için, [Azure Portal kullanarak işletim sistemi diskini bir kurtarma sanal makinesine ekleyerek WINDOWS VM sorunlarını giderme](troubleshoot-recovery-disks-portal-windows.md)bölümüne bakın.
2. Uzak Masaüstü kullanarak sorun giderme sanal makinesine bağlanın.
3. Kaynak sanal makinenin sürücüsünde oluşturun `gpt.ini` (GPT. ini varsa, GPT. ini. bak olarak yeniden adlandırın): `\Windows\System32\GroupPolicy`
   
   > [!WARNING]
   > Sorun giderme sanal makinesi için işletim sistemi sürücüsü olan C:\Windows ' da aşağıdaki dosyaları yanlışlıkla oluşturduğunuzdan emin olun. Kaynak VM 'niz için bir veri diski olarak bağlı olan işletim sistemi sürücüsünde aşağıdaki dosyaları oluşturun.
   
   * Aşağıdaki satırları `gpt.ini` oluşturduğunuz dosyaya ekleyin:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![GPT. ini oluştur](./media/reset-local-password-without-agent/create-gpt-ini.png)

4. `scripts.ini` İçinde`\Windows\System32\GroupPolicy\Machines\Scripts\`oluşturun. Gizli klasörlerin gösterildiğinden emin olun. Gerekirse, `Machine` veya `Scripts` klasörlerini oluşturun.
   
   * Aşağıdaki satırları `scripts.ini` oluşturduğunuz dosyaya ekleyin:
     
     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Scripts. ini oluşturma](./media/reset-local-password-without-agent/create-scripts-ini.png)

5. `FixAzureVM.cmd` `<username>` Ve değerlerini`<newpassword>` kendi değerlerinizle değiştirerek aşağıdaki `\Windows\System32` içeriklerle oluşturun:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Create FixAzureVM.cmd](./media/reset-local-password-without-agent/create-fixazure-cmd.png)
   
    Yeni parolayı tanımlarken VM 'niz için yapılandırılmış parola karmaşıklığı gereksinimlerini karşılamanız gerekir.

6. Azure portal, diski sorun giderme VM 'sinden ayırın.

7. [ETKILENEN VM için işletim sistemi diskini değiştirin](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm).

8. Yeni VM çalışmaya başladıktan sonra, `FixAzureVM.cmd` komut dosyasında belirttiğiniz yeni parolayla uzak masaüstü 'nü kullanarak VM 'ye bağlanın.

9. Uzak oturumınızdan yeni VM 'ye kadar, ortamı temizlemek için aşağıdaki dosyaları kaldırın:
    
    * %Windir%\System32 adresinden
      * remove FixAzureVM.cmd
    * %Windir%\system32\groupilkemachıne\ KomutDosyaları
      * Scripts. ini dosyasını Kaldır
    * %Windir%\System32\GroupPolicy öğesinden
      * GPT. ini dosyasını kaldırın (GPT. ini daha önce vardı ve GPT. ini. bak olarak yeniden adlandırdıysanız,. bak dosyasını tekrar GPT. ini olarak yeniden adlandırın)

## <a name="detailed-steps-for-classic-vm"></a>Klasik VM için ayrıntılı adımlar

> [!NOTE]
> Adımlar Windows etki alanı denetleyicileri için geçerlidir. Yalnızca tek başına sunucuda veya bir etki alanının üyesi olan bir sunucuda çalışmaktadır.

Aşağıdaki adımları denemeden önce [Azure Portal veya Azure PowerShell](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/reset-rdp) kullanarak her zaman bir parolayı sıfırlamayı deneyin. Başlamadan önce VM 'nizin yedeğine sahip olduğunuzdan emin olun. 

1. Azure portal etkilenen sanal makineyi silin. VM 'nin silinmesi yalnızca meta verileri, Azure 'daki VM 'nin başvurusunu siler. Sanal diskler, VM silindiğinde tutulur:
   
   * Azure portal VM 'yi seçip *Sil*' e tıklayın:
     
     ![Mevcut VM 'yi Sil](./media/reset-local-password-without-agent/delete-vm-classic.png)

2. Kaynak VM 'nin işletim sistemi diskini sorun giderme VM 'sine bağlayın. Sorun giderme sanal makinesi, kaynak VM 'nin işletim sistemi diski ( `West US`gibi) ile aynı bölgede olmalıdır:
   
   1. Azure portal sorun giderme sanal makinesini seçin. *Disklere* | *Ekle*' ye tıklayın:
     
      ![Var olan bir diski ekle](./media/reset-local-password-without-agent/disks-attach-existing-classic.png)
     
   2. *VHD dosyası* ' nı seçin ve ardından kaynak sanal makineyi içeren depolama hesabını seçin:
     
      ![Depolama hesabı seçin](./media/reset-local-password-without-agent/disks-select-storage-account-classic.png)
     
   3. *Klasik depolama hesaplarını göster*işaretli kutuyu işaretleyin ve ardından kaynak kapsayıcısını seçin. Kaynak kapsayıcısı genellikle *VHD*'ler:
     
      ![Depolama kapsayıcısını seçin](./media/reset-local-password-without-agent/disks-select-container-classic.png)

      ![Depolama kapsayıcısını seçin](./media/reset-local-password-without-agent/disks-select-container-vhds-classic.png)
     
   4. İliştirilecek işletim sistemi VHD 'sini seçin. İşlemi gerçekleştirmek için *Seç* ' e tıklayın:
     
      ![Kaynak sanal disk seçin](./media/reset-local-password-without-agent/disks-select-source-vhd-classic.png)

   5. Diski eklemek için Tamam 'a tıklayın

      ![Var olan bir diski ekle](./media/reset-local-password-without-agent/disks-attach-okay-classic.png)

3. Uzak Masaüstü kullanarak sorun giderme sanal makinesine bağlanın ve kaynak VM 'nin işletim sistemi diskinin görünür olduğundan emin olun:

   1. Azure portal sorun giderme sanal makinesini seçip *Bağlan*' a tıklayın.

   2. İndirilen RDP dosyasını açın. Sorun giderme sanal makinesinin Kullanıcı adını ve parolasını girin.

   3. Dosya Gezgini 'nde, eklediğiniz veri diskini bulun. Kaynak VM 'nin VHD 'SI, sorun giderme sanal makinesine bağlı tek veri diskdeyse, bu, F: sürücüsü olmalıdır:
     
      ![Bağlı veri diskini görüntüleme](./media/reset-local-password-without-agent/troubleshooting-vm-file-explorer-classic.png)

4. Kaynak sanal makinenin sürücüsünde oluşturun `gpt.ini` `gpt.ini.bak`(varsa, yeniden adlandırın): `gpt.ini` `\Windows\System32\GroupPolicy`
   
   > [!WARNING]
   > Sorun giderme sanal makinesi için işletim sistemi sürücüsünde aşağıdaki dosyaları `C:\Windows`yanlışlıkla oluşturmadığından emin olun. Kaynak VM 'niz için bir veri diski olarak bağlı olan işletim sistemi sürücüsünde aşağıdaki dosyaları oluşturun.
   
   * Aşağıdaki satırları `gpt.ini` oluşturduğunuz dosyaya ekleyin:
     
     ```
     [General]
     gPCFunctionalityVersion=2
     gPCMachineExtensionNames=[{42B5FAAE-6536-11D2-AE5A-0000F87571E3}{40B6664F-4972-11D1-A7CA-0000F87571E3}]
     Version=1
     ```
     
     ![GPT. ini oluştur](./media/reset-local-password-without-agent/create-gpt-ini-classic.png)

5. `scripts.ini` İçinde`\Windows\System32\GroupPolicy\Machines\Scripts\`oluşturun. Gizli klasörlerin gösterildiğinden emin olun. Gerekirse, `Machine` veya `Scripts` klasörlerini oluşturun.
   
   * Aşağıdaki satırları `scripts.ini` oluşturduğunuz dosyaya ekleyin:

     ```
     [Startup]
     0CmdLine=C:\Windows\System32\FixAzureVM.cmd
     0Parameters=
     ```
     
     ![Scripts. ini oluşturma](./media/reset-local-password-without-agent/create-scripts-ini-classic.png)

6. `FixAzureVM.cmd` `<username>` Ve değerlerini`<newpassword>` kendi değerlerinizle değiştirerek aşağıdaki `\Windows\System32` içeriklerle oluşturun:
   
    ```
    net user <username> <newpassword> /add
    net localgroup administrators <username> /add
    net localgroup "remote desktop users" <username> /add
    ```

    ![Create FixAzureVM.cmd](./media/reset-local-password-without-agent/create-fixazure-cmd-classic.png)
   
    Yeni parolayı tanımlarken VM 'niz için yapılandırılmış parola karmaşıklığı gereksinimlerini karşılamanız gerekir.

7. Azure portal, diski sorun giderme VM 'sinden ayırın:
   
   1. Azure portal sorun giderme sanal makinesini seçin, *diskler*' e tıklayın.
   
   2. 2\. adımda eklenen veri diskini seçin, **Ayır**' a ve ardından **Tamam**' a tıklayın.

     ![Diski kullanımdan çıkar](./media/reset-local-password-without-agent/data-disks-classic.png)
     
     ![Diski kullanımdan çıkar](./media/reset-local-password-without-agent/detach-disk-classic.png)

8. Kaynak VM 'nin işletim sistemi diskinden bir VM oluşturun:
   
     ![Şablondan VM oluşturma](./media/reset-local-password-without-agent/create-new-vm-from-template-classic.png)

     ![Şablondan VM oluşturma](./media/reset-local-password-without-agent/choose-subscription-classic.png)

     ![Şablondan VM oluşturma](./media/reset-local-password-without-agent/create-vm-classic.png)

## <a name="complete-the-create-virtual-machine-experience"></a>Sanal makine oluşturma deneyimini doldurun

1. Yeni VM çalışmaya başladıktan sonra, `FixAzureVM.cmd` komut dosyasında belirttiğiniz yeni parolayla uzak masaüstü 'nü kullanarak VM 'ye bağlanın.

2. Uzak oturumınızdan yeni VM 'ye kadar, ortamı temizlemek için aşağıdaki dosyaları kaldırın:
    
    * Kaynak`%windir%\System32`
      * Temizlenmesine`FixAzureVM.cmd`
    * Kaynak`%windir%\System32\GroupPolicy\Machine\Scripts`
      * Temizlenmesine`scripts.ini`
    * Kaynak`%windir%\System32\GroupPolicy`
      * kaldırın `gpt.ini` (daha `gpt.ini` önce varsa `gpt.ini.bak`ve yeniden adlandırdıysanız, `.bak` dosyayı `gpt.ini`olarak yeniden adlandırın)

## <a name="next-steps"></a>Sonraki adımlar
Hala uzak masaüstü 'Nü kullanarak bağlanamıyorsanız, [RDP sorun giderme kılavuzu](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)' na bakın. [AYRıNTıLı RDP sorun giderme kılavuzu](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , belirli adımlar yerine sorun giderme yöntemlerine bakar. Ayrıca, uygulamalı yardım için [bir Azure destek isteği açabilirsiniz](https://azure.microsoft.com/support/options/) .
