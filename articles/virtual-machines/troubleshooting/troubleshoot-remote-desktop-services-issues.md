---
title: Azure VM 'de Uzak Masaüstü Hizmetleri başlamıyor | Microsoft Docs
description: Bir sanal makineye bağlandığınızda Uzak Masaüstü Hizmetleri sorunları nasıl giderebileceğinizi öğrenin | Microsoft Docs
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
ms.openlocfilehash: 7949bedec2d304cd87fb512b44cd61d6f0894638
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168946"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Uzak Masaüstü Hizmetleri bir Azure VM üzerinde başlamıyor

Bu makalede, bir Azure sanal makinesine (VM) bağlandığınızda ve Uzak Masaüstü Hizmetleri ya da TermService, başlamadığında veya başlayamıyorsa sorunları gidermeye yönelik konular açıklanmaktadır.

> [!NOTE]  
> Azure 'da oluşturulacak ve kaynaklarla çalışmak için iki farklı dağıtım modeli vardır: [Azure Resource Manager ve klasik](../../azure-resource-manager/resource-manager-deployment-model.md). Bu makalede Kaynak Yöneticisi dağıtım modelinin kullanımı açıklanmaktadır. Bu modeli, klasik dağıtım modeli yerine yeni dağıtımlar için kullanmanızı öneririz.

## <a name="symptoms"></a>Belirtileri

Bir sanal makineye bağlanmaya çalıştığınızda, aşağıdaki senaryolarla karşılaşırsınız:

- VM ekran görüntüsü, işletim sisteminin tam olarak yüklendiğini ve kimlik bilgilerini beklediğini gösterir.

    ![VM durumunun ekran görüntüsü](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- Olay Görüntüleyicisi kullanarak VM 'deki olay günlüklerini uzaktan görüntüleyebilirsiniz. Uzak Masaüstü Hizmetleri, TermService, başlatamaya da başlayamıyor olduğunu görürsünüz. Aşağıdaki günlük bir örnektir:

    **Günlük adı**: sistem </br>
    **Kaynak**: hizmet denetimi Yöneticisi </br>
    **Tarih**: 12/16/2017 11:19:36</br>
    **Olay kimliği**: 7022</br>
    **Görev kategorisi**: yok</br>
    **Düzey**: hata</br>
    **Anahtar sözcükler**: klasik</br>
    **Kullanıcı**: yok</br>
    **Bilgisayar**: VM.contoso.com</br>
    **Açıklama**: Uzak Masaüstü Hizmetleri hizmeti başlatılırken askıda kaldı. 

    Ayrıca, aşağıdaki sorguyu çalıştırarak bu hataları aramak için seri erişim konsolu özelliğini de kullanabilirsiniz: 

        wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more 

## <a name="cause"></a>Nedeni
 
Bu sorun, VM üzerinde Uzak Masaüstü Hizmetleri çalışmadığından oluşur. Nedeni aşağıdaki senaryolara bağlı olabilir: 

- TermService hizmeti **devre dışı**olarak ayarlandı. 
- TermService hizmeti kilitlenme veya yanıt vermiyor. 
- TermService, yanlış bir yapılandırma nedeniyle başlamıyor.

## <a name="solution"></a>Çözümden

Bu sorunu gidermek için seri konsolu 'nu kullanın. Ya da VM 'nin işletim sistemi diskini bir kurtarma sanal makinesine ekleyerek [VM 'yi çevrimdışı olarak onarın](#repair-the-vm-offline) .

### <a name="use-serial-console"></a>Seri konsol kullan

1. **Destek & sorun giderme** > **seri konsol**' nı seçerek [seri konsoluna](serial-console-windows.md) erişin. Özellik VM 'de etkinse VM 'yi başarıyla bağlayabilirsiniz.

2. Bir CMD örneği için yeni bir kanal oluşturun. Kanalı başlatmak ve kanal adını almak için **cmd** girin.

3. CMD örneğini çalıştıran kanala geçin. Bu durumda, Kanal 1 olmalıdır:

   ```
   ch -si 1
   ```

4. Yeniden **gir** ' i SEÇIN ve VM için geçerli bir Kullanıcı adı ve parola, yerel veya etkı alanı kimliği girin.

5. TermService hizmetinin durumunu sorgulama:

   ```
   sc query TermService
   ```

6. Hizmet durumu **durduruldu**gösteriyorsa, hizmeti başlatmayı deneyin:

    ```
    sc start TermService
     ``` 

7. Hizmetin başarıyla başlatıldığından emin olmak için hizmeti tekrar sorgulayın:

   ```
   sc query TermService
   ```
8. Hizmet başlatılamazsa, aldığınız hataya göre çözümü izleyin:

    |  Hata |  bulunmak |
    |---|---|
    |5-ERIŞIM ENGELLENDI |Bkz. [bir erişim reddedildi hatası nedeniyle TermService hizmeti durduruldu](#termservice-service-is-stopped-because-of-an-access-denied-problem). |
    |1053-ERROR_SERVICE_REQUEST_TIMEOUT  |Bkz. [TermService hizmeti devre dışı](#termservice-service-is-disabled).  |  
    |1058-ERROR_SERVICE_DISABLED  |Bkz. [TermService hizmeti kilitleniyor veya askıda kalıyor](#termservice-service-crashes-or-hangs).  |
    |1059-ERROR_CIRCULAR_DEPENDENCY |Sorununuzu hızla çözülebilmenizi sağlamak için [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .|
    |1067-ERROR_PROCESS_ABORTED  |Bkz. [TermService hizmeti kilitleniyor veya askıda kalıyor](#termservice-service-crashes-or-hangs).  |
    |1068-ERROR_SERVICE_DEPENDENCY_FAIL|Sorununuzu hızla çözülebilmenizi sağlamak için [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .|
    |1069-ERROR_SERVICE_LOGON_FAILED  |Bkz. [TermService hizmeti, oturum açma hatası nedeniyle başarısız oluyor](#termservice-service-fails-because-of-logon-failure) |
    |1070-ERROR_SERVICE_START_HANG   | Bkz. [TermService hizmeti kilitleniyor veya askıda kalıyor](#termservice-service-crashes-or-hangs). |
    |1077-ERROR_SERVICE_NEVER_STARTED   | Bkz. [TermService hizmeti devre dışı](#termservice-service-is-disabled).  |
    |1079-ERROR_DIFERENCE_SERVICE_ACCOUNT   |Sorununuzu hızla çözülebilmenizi sağlamak için [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) . |
    |1753   |Sorununuzu hızla çözülebilmenizi sağlamak için [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .   |
    
#### <a name="termservice-service-is-stopped-because-of-an-access-denied-problem"></a>TermService hizmeti, erişim engellendi sorunu nedeniyle durduruldu

1. [Seri konsoluna](serial-console-windows.md) bağlanın ve bir PowerShell örneği açın.
2. Aşağıdaki betiği çalıştırarak Işlem Izleyicisi aracını indirin:

   ```
   remove-module psreadline  
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip" 
   $destination = "c:\temp\ProcessMonitor.zip" 
   $wc = New-Object System.Net.WebClient 
   $wc.DownloadFile($source,$destination) 
   ```

3. Şimdi bir **procmon** izlemesi başlatın:

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML 
   ```

4. **Erişim izni**veren hizmeti başlatarak sorunu yeniden üretin: 

   ```
   sc start TermService 
   ```

   Başarısız olduğunda, Işlem Izleyicisi izlemesini sonlandırın:

   ```   
   procmon /Terminate 
   ```

5. **C:\temp\procmontrace. PML**dosyasını toplayın:

    1. [Sanal makineye bir veri diski ekleyin](../windows/attach-managed-disk-portal.md
).
    2. Seri konsolu 'Nu kullanarak dosyayı yeni sürücüye kopyalayabilirsiniz. Örneğin, `copy C:\temp\ProcMonTrace.PML F:\`. Bu komutta, F, ekli veri diskinin sürücü harfidir.
    3. Veri sürücüsünü ayırın ve Işlem Izleyicisi ubstakke yüklü olan çalışan bir sanal makineye ekleyin.

6. Çalışan VM 'yi kullanarak **Procmontrace. PML** dosyasını açın. Ardından, aşağıdaki ekran görüntüsünde gösterildiği gibi, sonuca göre filtrele **ERIŞIMI reddedildi**:

    ![Işlem Izleyicisinde sonuca göre filtrele](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. Kayıt defteri anahtarlarını, klasörlerini veya çıktıda bulunan dosyaları onarın. Genellikle bu sorun, hizmette kullanılan oturum açma hesabının bu nesnelere erişmek için ACL iznine sahip olmaması nedeniyle oluşur. Oturum açma hesabı için doğru ACL iznini bildirmek üzere sağlıklı bir VM 'yi kontrol edebilirsiniz. 

#### <a name="termservice-service-is-disabled"></a>TermService hizmeti devre dışı

1. Hizmeti varsayılan başlangıç değerine geri yükleyin:

   ```
   sc config TermService start= demand 
   ```

2. Hizmeti başlatın:

   ```
   sc start TermService
   ```

3. Hizmetin çalıştığından emin olmak için durumunu yeniden sorgulayın:

   ```
   sc query TermService 
   ```

4. Uzak Masaüstü kullanarak VM 'ye bağlanmayı deneyin.

#### <a name="termservice-service-fails-because-of-logon-failure"></a>TermService hizmeti, oturum açma hatası nedeniyle başarısız oluyor

1. Bu sorun, bu hizmetin başlangıç hesabı değiştiyse oluşur. Bunu varsayılana geri değiştirdi: 

        sc config TermService obj= 'NT Authority\NetworkService'
2. Hizmeti başlatın:

        sc start TermService
3. Uzak Masaüstü kullanarak VM 'ye bağlanmayı deneyin.

#### <a name="termservice-service-crashes-or-hangs"></a>TermService hizmeti kilitleniyor veya askıda kalıyor
1. Hizmet durumu **başlatılıyor** veya **durduruluyor**durumunda takıldığında hizmeti durdurmayı deneyin: 

        sc stop TermService
2. Hizmeti kendi ' Svchost ' kapsayıcısında yalıtın:

        sc config TermService type= own
3. Hizmeti başlatın:

        sc start TermService
4. Hizmet yine de başlamazsa, [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>VM 'yi çevrimdışı onarma

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>İşletim sistemi diskini bir kurtarma VM 'sine iliştirme

1. [İşletim sistemi diskini bir kurtarma sanal makinesine ekleyin](../windows/troubleshoot-recovery-disks-portal.md).
2. Kurtarma VM 'sine bir Uzak Masaüstü bağlantısı başlatın. Disk Yönetimi konsolunda, eklenen diskin **çevrimiçi** olarak işaretlendiğinden emin olun. Bağlı işletim sistemi diskine atanan sürücü harfini unutmayın.
3. Yükseltilmiş bir komut istemi örneği açın (**yönetici olarak çalıştır**). Ardından aşağıdaki betiği çalıştırın. Bağlı işletim sistemi diskine atanan sürücü harfinin **F**olduğunu varsayıyoruz. Bunu sanal makinenizde uygun değerle değiştirin. 

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
        
   REM Set default values back on the broken service 
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService“ /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v type /t REG_DWORD /d 16 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService" /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v type /t REG_DWORD /d 16 /f
   ```

4. [İşletim sistemi diskini ayırın ve VM 'yi yeniden oluşturun](../windows/troubleshoot-recovery-disks-portal.md). Sonra sorunun çözümlenip çözümlenmediğini denetleyin.

## <a name="need-help-contact-support"></a>Yardıma mı ihtiyacınız var? Desteğe başvurun

Hala yardıma ihtiyacınız varsa, sorununuzu çözmeden yararlanmak için [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .
