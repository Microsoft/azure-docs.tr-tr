---
title: Azure 'da bir Windows 10 veya Windows Server 2016 VM 'ye uzaktan bağlandığınızda netvsc. sys sorununu giderin | Microsoft Docs
description: Azure 'da bir Windows 10 veya Windows Server 2016 VM 'sine bağlanırken Netsvc. sys ile ilgili bir RDP sorunuyla ilgili sorunları nasıl giderebileceğinizi öğrenin.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/19/2018
ms.author: genli
ms.openlocfilehash: 4c10a2dcd55c1605cfafe6c67cfefd9d8a3c5f9d
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71057979"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>Netvsc. sys nedeniyle Azure 'da bir Windows 10 veya Windows Server 2016 VM 'ye uzaktan bağlanılamıyor

Bu makalede bir Hyper-V Server 2016 ana bilgisayarında Windows 10 veya Windows Server 2016 Datacenter sanal makinesine (VM) bağlandığınızda ağ bağlantısı bulunmayan bir sorunun nasıl giderileceği açıklanmaktadır.

## <a name="symptoms"></a>Belirtiler

Uzak Masaüstü Protokolü (RDP) kullanarak bir Azure Windows 10 veya Windows Server 2016 VM 'sine bağlanamazsınız. [Önyükleme tanılamasında](boot-diagnostics.md), ekranda ağ arabirim kartı (NIC) üzerinde kırmızı bir çapraz gösterilir. Bu, işletim sistemi tam olarak yüklendikten sonra sanal makinenin bağlantısının olmadığını gösterir.

Genellikle, bu sorun Windows [build 14393](https://support.microsoft.com/help/4093120/) ve [derleme 15063](https://support.microsoft.com/help/4015583/)' de oluşur. İşletim sisteminizin sürümü bu sürümlerden daha sonra ise, bu makale senaryonuz için de geçerlidir. Sistemin sürümünü denetlemek için, [seri erişim konsolu özelliğinde](serial-console-windows.md)bir cmd oturumu açın ve ardından **ver**' i çalıştırın.

## <a name="cause"></a>Nedeni

Yüklü netvsc. sys sistem dosyasının sürümü **10.0.14393.594** veya **10.0.15063.0**ise bu sorun oluşabilir. Netvsc. sys ' nin bu sürümleri sistemin Azure platformuyla etkileşimini engelleyebilir.


## <a name="solution"></a>Çözüm

Bu adımları izlemeden önce, etkilenen VM 'nin [sistem diskinin bir anlık görüntüsünü](../windows/snapshot-copy-managed-disk.md) yedek olarak alın. Bu sorunu gidermek için seri konsolu veya [çevrimdışı VM'yi onarın](#repair-the-vm-offline) sanal Makinenin sistem diskini bir kurtarma sanal Makinesine ekleyerek.


### <a name="use-the-serial-console"></a>Seri konsolunu kullanma

[Seri konsoluna bağlanın, bir PowerShell örneği açın](serial-console-windows.md)ve ardından aşağıdaki adımları izleyin.

> [!NOTE]
> Seri konsol sanal makinenizde etkin değilse, Git [çevrimdışı VM'yi onarın](#repair-the-vm-offline) bölümü.

1. Bir PowerShell örneğinde, dosyanın sürümünü (**c:\windows\system32\drivers\netvsc.sys**) almak için aşağıdaki komutu çalıştırın:

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. İlgili güncelleştirmeyi aynı bölgeden çalışan bir sanal makineye bağlı yeni veya mevcut bir veri diskine indirin:

   - **10.0.14393.594**: [KB4073562 veya sonraki bir](https://support.microsoft.com/help/4073562) güncelleştirme
   - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) veya sonraki bir güncelleştirme

3. Yardımcı programı diskini çalışan sanal makineden ayırın ve sonra bozuk VM 'ye bağlayın.

4. Güncelleştirmeyi VM 'ye yüklemek için aşağıdaki komutu çalıştırın:

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. VM’yi yeniden başlatın.

### <a name="repair-the-vm-offline"></a>VM'yi çevrimdışı onarın

1. [Bir kurtarma VM'si sistem diski](../windows/troubleshoot-recovery-disks-portal.md).

2. Kurtarma VM'sini bir Uzak Masaüstü Bağlantısı'nı başlatın.

3. Disk olarak işaretlenmiş olduğundan emin olun **çevrimiçi** Disk Yönetimi Konsolu'nda. Bağlı sistem diskine atanmış sürücü harfini unutmayın.

4. Değişiklikler üzerinde geri almanın gerekli olması durumunda **\Windows\system32\config** klasörünün bir kopyasını oluşturun.

5. Kurtarma VM 'sinde, kayıt defteri Düzenleyicisi 'ni (Regedit. exe) başlatın.

6. **HKEY_LOCAL_MACHINE** anahtarını seçin ve sonra menüden **Dosya** > **yükleme Hive** ' yi seçin.

7. **\Windows\system32\config** klasöründeki sistem dosyasını bulun.

8. **Aç**' ı seçin, ad Için **brokensystem** yazın, **HKEY_LOCAL_MACHINE** anahtarını genişletin ve sonra da **brokensystem**adlı ek anahtarı bulun.

9. Şu konuma gidin:

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. Her alt anahtarda (0000 gibi), **MICROSOFT Hyper-V ağ bağdaştırıcısı**olarak görüntülenen **DriverDesc** değerini inceleyin.

11. Alt anahtarda, sanal makinenin ağ bağdaştırıcısının sürücü sürümü olan **DriverVersion** değerini inceleyin.

12. Uygun güncelleştirmeyi indirin:

    - **10.0.14393.594**: [KB4073562 veya sonraki bir](https://support.microsoft.com/help/4073562) güncelleştirme
    - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) veya sonraki bir güncelleştirme

13. Sistem diskini, üzerinde güncelleştirmeyi indirebileceğiniz bir kurtarma sanal makinesine veri diski olarak ekleyin.

14. Güncelleştirmeyi VM 'ye yüklemek için aşağıdaki komutu çalıştırın:

    ```
    dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
    ```

15. Kovanları çıkarmak için aşağıdaki komutu çalıştırın:

    ```
    reg unload HKLM\BROKENSYSTEM
    ```

16. [Sistem diskini ayırın ve VM 'yi yeniden oluşturun](../windows/troubleshoot-recovery-disks-portal.md).

## <a name="need-help-contact-support"></a>Yardım mı gerekiyor? Desteğe başvurun

Hala yardıma ihtiyacınız varsa, sorununuzun hızla çözülmesini sağlamak için [Azure desteğine başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .
