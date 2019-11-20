---
title: Sistem durumu yedeklemesi sorunlarını giderme
description: Bu makalede, şirket içi Windows Server 'lar için sistem durumu yedeklemesiyle ilgili sorunları nasıl giderebileceğinizi öğrenin.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/22/2019
ms.openlocfilehash: 116f8f40193ea276c6150452b0aa6f2d2ce5bc6c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172616"
---
# <a name="troubleshoot-system-state-backup"></a>Sistem durumu yedeklemesi sorunlarını giderme

Bu makalede, sistem durumu yedeklemesini kullanırken karşılaşabileceğiniz sorunlara yönelik çözümler açıklanmaktadır.

## <a name="basic-troubleshooting"></a>Temel sorun giderme

Sistem durumu yedeklemesine sorun gidermeye başlamadan önce aşağıdaki doğrulamayı gerçekleştirmenizi öneririz:

- [Microsoft Azure Kurtarma Hizmetleri (MARS) aracısının güncel olduğundan emin olun](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [MARS aracısı ile Azure arasında ağ bağlantısı sağlandığından emin olun](https://aka.ms/AB-A4dp50)
- Microsoft Azure Kurtarma Hizmetleri'nin çalıştığından emin olun (Hizmet konsolunda). Gerekirse yeniden başlatın ve işlemi yeniden deneyin
- [Boş klasör konumunda %5-10 oranında kullanılabilir alan olduğundan emin olun](https://aka.ms/AB-AA4dwtt)
- [Azure Backup ile çakışan başka bir işlem veya virüsten koruma yazılımı olup olmadığını kontrol edin](https://aka.ms/AB-AA4dwtk)
- [Zamanlanmış yedekleme başarısız oluyor ancak el ile yedekleme çalışıyor](https://aka.ms/ScheduledBackupFailManualWorks)
- İşletim sisteminizde en son güncelleştirmelerin yüklü olduğundan emin olun
- [Desteklenmeyen özniteliklerin ve desteklenmeyen özniteliklere sahip dosyaların yedeklemeden dışlandığından emin olun](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Korumalı sistemdeki **Sistem Saatinin** doğru saat dilimine göre yapılandırıldığından emin olun <br>
- [Sunucuda en az .Net Framework 4.5.2 ve üzeri bir sürümün yüklü olduğundan emin olun](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- **Sunucunuzu bir kasaya yeniden kaydetmeye** çalışıyorsanız: <br>
  - Aracının sunucudan kaldırıldığından ve portaldan silindiğinden emin olun <br>
  - Sunucu kaydedilirken kullanılan parolayı kullanın <br>
- Çevrimdışı yedekleme durumunda, çevrimdışı yedekleme işlemine başlamadan önce Azure PowerShell Version 3.7.0 'in hem kaynak hem de kopya bilgisayara yüklendiğinden emin olun
- [Azure sanal makinesinde Yedekleme aracısının ne zaman çalıştığını göz önünde bulundurun](https://aka.ms/AB-AA4dwtr)

### <a name="limitation"></a>Sınırlama

- Sistem Durumu kurtarmayı kullanarak farklı donanımda kurtarma işlemi yapılması Microsoft tarafından önerilmez
- Sistem durumu yedeklemesi Şu anda "Şirket içi" Windows sunucularını destekliyor, bu işlev Azure VM 'Leri için kullanılamaz.

## <a name="prerequisites"></a>Önkoşullar

Azure Backup ile sistem durumu yedeklemesine sorun gidermeye başlamadan önce, aşağıdaki önkoşul denetimini gerçekleştirin.  

### <a name="verify-windows-server-backup-is-installed"></a>Windows Server Yedekleme yüklendiğini doğrulama

Windows Server Yedekleme sunucuda yüklü ve etkin olduğundan emin olun. Yükleme durumunu denetlemek için şu PowerShell komutunu çalıştırın:

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

Çıkış, **yükleme durumunu** **kullanılabilir**olarak görüntülüyorsa, Windows Server yedekleme özelliğinin yükleme için kullanılabilir ancak sunucuda yüklü olmadığı anlamına gelir. Ancak Windows Server Yedekleme yüklenmemişse, yüklemek için aşağıdaki yöntemlerden birini kullanın.

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>Yöntem 1: PowerShell kullanarak Windows Server Yedekleme yüklemesi

PowerShell kullanarak Windows Server Yedekleme yüklemek için aşağıdaki komutu çalıştırın:

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>Yöntem 2: Sunucu Yöneticisi kullanarak Windows Server Yedekleme yüklemesi

Sunucu Yöneticisi kullanarak Windows Server Yedekleme yüklemek için aşağıdaki adımları uygulayın:

1. **Sunucu Yöneticisi**'nde **rol ve Özellik Ekle**' ye tıklayın. **Rol ve Özellik Ekleme Sihirbazı** görünür.

    ![Pano](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. **Yükleme türünü** seçin ve **İleri**' ye tıklayın.

    ![Yükleme türü](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Sunucu havuzundan bir sunucu seçin ve **İleri**' ye tıklayın. Sunucu rolünde, varsayılan seçimi bırakın ve **İleri**' ye tıklayın.
4. **Özellikler** sekmesinde **Windows Server yedekleme** ' yi seçin ve **İleri**' ye tıklayın.

    ![SaaS Uygulamaları Geliştirme](./media/backup-azure-system-state-troubleshoot/features.png)

5. **Onay** sekmesinde, yükleme işlemini başlatmak için **yükleme** ' ye tıklayın.
6. **Sonuçlar** sekmesinde, Windows Server yedekleme özelliği Windows sunucunuza başarıyla yüklendi.

    ![Sonuç](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>Sistem birimi bilgileri izni

Yerel SISTEMIN Windows 'un yüklü olduğu birimde bulunan **sistem birimi bilgileri** klasörü üzerinde tam denetime sahip olduğundan emin olun. Genellikle bu, **C:\Sistem birimi bilgileri**. Yukarıdaki izinler doğru ayarlanmamışsa Windows Server yedekleme başarısız olabilir

### <a name="dependent-services"></a>Bağımlı hizmetler

Aşağıdaki hizmetlerin çalışır durumda olduğundan emin olun:

**Hizmet adı** | **Başlangıç türü**
--- | ---
Uzak yordam çağrısı (RPC) | Otomatik
COM+ olay sistemi (EventSystem) | Otomatik
Sistem olay bildirimi hizmeti (SENS) | Otomatik
Birim gölge kopyası (VSS) | El ile
Microsoft yazılım gölge kopyası sağlayıcısı (SWPRV) | El ile

### <a name="validate-windows-server-backup-status"></a>Windows Server Yedekleme durumunu doğrula

Windows Server Yedekleme durumunu doğrulamak için aşağıdaki adımları gerçekleştirin:

- WSB PowerShell 'in çalıştığından emin olun

  - Yükseltilmiş bir PowerShell 'ten `Get-WBJob` çalıştırın ve aşağıdaki hatayı döndürmediğinden emin olun:

    > [!WARNING]
    > Get-WBJob: ' Get-WBJob ' terimi bir cmdlet, işlev, betik dosyası veya çalıştırılabilir program adı olarak tanınmıyor. Adın yazımını denetleyin veya bir yol içerilip yolun doğru olduğundan emin olun ve yeniden deneyin.

    - Bu hatayla başarısız olursa, 1. adım önkoşullardan bahsedildiği gibi Windows Server Yedekleme özelliğini sunucu makinesine yeniden yükleyin.

  - Yükseltilmiş komut isteminden aşağıdaki komutu çalıştırarak WSB yedeğinin düzgün çalıştığından emin olun:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >X değerini, sistem durumu yedekleme görüntüsünü depolamak istediğiniz birimin sürücü harfiyle değiştirin.

    - Yükseltilmiş PowerShell 'ten `Get-WBJob` komutu çalıştırarak işin durumunu düzenli aralıklarla denetleyin
    - Yedekleme işi tamamlandıktan sonra `Get-WBJob -Previous 1` komutunu çalıştırarak işin son durumunu denetleyin

İş başarısız olursa, MARS Aracısı sistem durumu yedeklerinin başarısız olmasına neden olacak bir WSB sorunu belirtir.

## <a name="common-errors"></a>Sık karşılaşılan hatalar

### <a name="vss-writer-timeout-error"></a>VSS Yazıcı zaman aşımı hatası

| Belirti | Nedeni | Çözüm
| -- | -- | --
| -MARS Aracısı şu hata iletisiyle başarısız oluyor: "WSB işi VSS hatalarıyla başarısız oldu. Sorunu çözmek için VSS olay günlüklerine bakın "<br/><br/> -VSS uygulama olay günlüklerinde şu hata günlüğü var: "bir VSS yazıcı, 0x800423f2 hatası ile bir olayı reddetti. dondurma ve çözme olayları arasında yazıcının zaman aşımı süresi doldu."| Makinedeki CPU ve bellek kaynaklarının olmaması nedeniyle VSS yazıcısı zaman içinde tamamlanamadı <br/><br/> Başka bir yedekleme yazılımı zaten VSS yazıcısını kullanıyor, çünkü bu yedekleme için bir sonuç anlık görüntü işlemi tamamlanamadı | CPU/belleğin sistem üzerinde serbest olmasını bekleyin veya çok fazla bellek/CPU alan işlemleri iptal edin ve işlemi yeniden deneyin <br/><br/>  Devam eden yedeklemenin tamamlanmasını bekleyin ve makinede yedekleme olmadığında işlemi daha sonraki bir noktada deneyin

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Gölge kopyaları büyütmek için yeterli disk alanı yok

| Belirti | Çözüm
| -- | --
| -MARS Aracısı şu hata iletisiyle başarısız oldu: gölge kopya birimi sistem dosyalarını içeren birimlerde yetersiz disk alanı nedeniyle büyümediği için yedekleme başarısız oldu <br/><br/> -Volsnap sistem olay günlüklerinde şu hata/uyarı günlüğü var: "c birimi üzerinde yeterli disk alanı yoktu: Bu hata nedeniyle c: biriminin gölge kopyalarının gölge kopya depolama alanını büyütmek için | -Yedekleme devam ederken gölge kopyaların büyümesine yetecek kadar alan olması için olay günlüğündeki vurgulanan birimde yer açın <br/><br/> -Gölge kopya alanı yapılandırılırken, gölge kopya için kullanılan alan miktarını kısıtlayabiliriz. Daha fazla bilgi için bu [makaleye](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/cc788050(v=ws.11)#syntax) bakın

### <a name="efi-partition-locked"></a>EFı bölümü kilitli

| Belirti | Çözüm
| -- | --
| MARS Aracısı şu hata iletisiyle başarısız oluyor: "EFı sistem bölümü kilitli olduğu için sistem durumu yedeklemesi başarısız oldu. Bunun nedeni, üçüncü taraf bir güvenlik veya yedekleme yazılımı tarafından sistem bölümü erişiminin olması olabilir " | -Sorun bir üçüncü taraf güvenlik yazılımından kaynaklanıyorsa, bu durumda, MARS aracısına izin vermek için anti virüs satıcısına başvurmanız gerekir <br/><br/> -Bir üçüncü taraf yedekleme yazılımı çalışıyorsa işlemin bitmesini bekleyin ve sonra yedeklemeyi yeniden deneyin

## <a name="next-steps"></a>Sonraki adımlar

- Kaynak Yöneticisi dağıtımında Windows sistem durumu hakkında daha fazla bilgi için bkz. [Windows Server sistem durumunu yedekleme](backup-azure-system-state.md)
