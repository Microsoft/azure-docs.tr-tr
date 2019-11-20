---
title: Windows için Azure seri konsol | Microsoft Docs
description: Azure sanal makineleri ve sanal makine ölçek kümeleri için iki yönlü seri konsol.
services: virtual-machines-windows
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: 0d48f3eacad86dac520d837b80605a75cce8cfd5
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514492"
---
# <a name="azure-serial-console-for-windows"></a>Windows için Azure seri konsol

Azure portal seri konsol, Windows sanal makineleri (VM 'Ler) ve sanal makine ölçek kümesi örnekleri için metin tabanlı bir konsola erişim sağlar. Bu seri bağlantı, ağ veya işletim sistemi durumundan bağımsız olarak erişim sağlayan VM veya sanal makine ölçek kümesi örneğinin COM1 seri bağlantı noktasına bağlanır. Seri konsoluna yalnızca Azure portal kullanılarak erişilebilir ve yalnızca VM veya sanal makine ölçek kümesine katkıda bulunan veya daha yüksek bir erişim rolüne sahip olan kullanıcılar için izin verilir.

Seri konsol, VM 'Ler ve sanal makine ölçek kümesi örnekleri için aynı şekilde çalışmaktadır. Bu belgede, aksi belirtilmediği takdirde VM 'lerdeki tüm bahsetmeler, sanal makine ölçek kümesi örneklerini örtülü olarak içerir.

Linux için seri konsol belgeleri için bkz. [Linux Için Azure seri konsolu](serial-console-linux.md).

> [!NOTE]
> Seri konsol Genel Azure bölgelerinde genel kullanıma sunulmuştur. Henüz Azure Kamu veya Azure Çin bulutlarında kullanılabilir değildir.


## <a name="prerequisites"></a>Önkoşullar

* VM 'niz veya sanal makine ölçek kümesi örneğinizin kaynak yönetimi dağıtım modelini kullanması gerekir. Klasik dağıtımlar desteklenmez.

- Seri konsol kullanan hesabınızda VM ve [önyükleme tanılama](boot-diagnostics.md) depolama hesabı Için [sanal makine katılımcısı rolü](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) olmalıdır

- VM 'niz veya sanal makine ölçek kümesi örneğinizin parola tabanlı bir kullanıcısı olmalıdır. VM erişimi uzantısının [parola sıfırlama](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) işleviyle bir tane oluşturabilirsiniz. **Destek + sorun giderme** bölümünde **Parolayı Sıfırla** ' yı seçin.

* Sanal makine ölçek kümesi örneği için VM 'nin [önyükleme tanılaması](boot-diagnostics.md) etkinleştirilmiş olması gerekir.

    ![Önyükleme Tanılama ayarları](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="enable-serial-console-functionality-for-windows-server"></a>Windows Server için seri konsol işlevselliğini etkinleştir

> [!NOTE]
> Seri konsolda hiçbir şey görmüyorsanız, VM 'niz veya sanal makine ölçek kümesinde önyükleme tanılaması 'nın etkinleştirildiğinden emin olun.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>Özel veya eski görüntülerde seri konsolunu etkinleştirme
Azure 'da daha yeni Windows Server görüntülerinin varsayılan olarak etkin olan [Özel Yönetim Konsolu](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (sac) vardır. SAC, Windows 'un sunucu sürümlerinde desteklenir, ancak istemci sürümlerinde (örneğin, Windows 10, Windows 8 veya Windows 7) kullanılamaz.

Eski Windows Server yansımaları için (2018 Şubat 'tan önce oluşturulan), seri konsolu Azure portal Çalıştır komutu özelliği aracılığıyla otomatik olarak etkinleştirebilirsiniz. Azure portal, **Çalıştır komutunu**seçin ve ardından listeden **enableems** adlı komutu seçin.

![Komut listesini Çalıştır](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

Alternatif olarak, 2018 Şubat 'tan önce oluşturulan Windows VM 'Leri/sanal makine ölçek kümesinin seri konsolunu el ile etkinleştirmek için şu adımları izleyin:

1. Uzak Masaüstü kullanarak Windows sanal makinenize bağlanma
1. Bir yönetim komut isteminden aşağıdaki komutları çalıştırın:
    - `bcdedit /ems {current} on`
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. SAC konsolunun etkinleştirilmesi için sistemi yeniden başlatın.

    ![SAC konsolu](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Gerekirse, SAC çevrimdışı olarak da etkinleştirilebilir:

1. Mevcut VM 'ye veri diski olarak hangi SAC 'nin yapılandırılacağını istediğiniz Windows diskini ekleyin.

1. Bir yönetim komut isteminden aşağıdaki komutları çalıştırın:
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>Nasıl yaparım? SAC 'nin etkinleştirilip etkinleştirilmediğini bilir mi?

[Sac](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) etkinleştirilmemişse, SERI konsol sac istemi 'ni görüntülemez. Bazı durumlarda, VM sistem durumu bilgileri gösterilir ve diğer durumlarda boş olur. 2018 Şubat 'tan önce oluşturulmuş bir Windows Server görüntüsü kullanıyorsanız, SAC büyük olasılıkla etkinleştirilmez.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>Seri konsolundaki Windows önyükleme menüsünü etkinleştirin

Windows önyükleme yükleyicisi komut istemlerini seri konsolunda görüntülemesi gerekirse, önyükleme yapılandırma verilerinize aşağıdaki ek seçenekleri ekleyebilirsiniz. Daha fazla bilgi için bkz. [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set).

1. Uzak Masaüstü 'Nü kullanarak Windows sanal makinmenize veya sanal makine ölçek kümesi örneğine bağlanın.

1. Bir yönetim komut isteminden aşağıdaki komutları çalıştırın:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Önyükleme menüsünün etkinleştirilmesi için sistemi yeniden başlatın

> [!NOTE]
> Önyükleme Yöneticisi menüsünün görüntülemesi için ayarladığınız zaman aşımı, işletim sistemi önyükleme süresini etkiler. 10 saniyelik zaman aşımı değerinin çok kısa veya çok uzun olduğunu düşünüyorsanız, bunu farklı bir değere ayarlayın.

## <a name="use-serial-console"></a>Seri konsol kullan

### <a name="use-cmd-or-powershell-in-serial-console"></a>Seri konsolunda CMD veya PowerShell kullanma

1. Seri konsoluna bağlanın. Başarıyla bağlanıyorsanız, istem **SAC >** :

    ![SAC 'ye bağlanma](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  CMD örneği olan bir kanal oluşturmak için `cmd` girin.

1.  CMD örneğini çalıştıran kanala geçmek için `ch -si 1` girin veya `<esc>+<tab>` kısayol tuşlarına basın.

1.  **ENTER**tuşuna basın ve ardından Yönetici izinleriyle oturum açma kimlik bilgilerini girin.

1.  Geçerli kimlik bilgilerini girdikten sonra CMD örneği açılır.

1.  Bir PowerShell örneği başlatmak için CMD örneğine `PowerShell` girin ve ardından **ENTER**tuşuna basın.

    ![PowerShell örneğini aç](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>NMI çağrıları için seri konsolunu kullanma
Maskelenemeyen bir kesme (NMI), bir sanal makinede yazılımın yok sayılamayan bir sinyal oluşturmak için tasarlanmıştır. Tarihsel olarak, belirli yanıt süreleri gerektiren sistemlerdeki donanım sorunlarını izlemek için, NMIs kullanıldı. Günümüzde programcılar ve sistem yöneticileri, yanıt vermeyen sistemlerde hata ayıklamak veya sorunları gidermek için genellikle NMI 'yi bir mekanizma olarak kullanır.

Seri konsol, Komut çubuğundaki klavye simgesini kullanarak bir Azure sanal makinesine NMI göndermek için kullanılabilir. NMI teslim edildikten sonra, sanal makine yapılandırması sistemin nasıl yanıt verdiğini denetler. Windows, bir NMI alırken kilitlenme ve bellek döküm dosyası oluşturmak için yapılandırılabilir.

![NMI gönder](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Windows 'u bir NMI aldığında kilitlenme döküm dosyası oluşturmak üzere yapılandırma hakkında bilgi için, bkz. [BIR NMI kullanarak kilitlenme döküm dosyası](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file)oluşturma.

### <a name="use-function-keys-in-serial-console"></a>Seri konsolundaki işlev anahtarlarını kullanma
İşlev anahtarları Windows VM 'lerinde seri konsol kullanımı için etkinleştirilmiştir. Seri konsol açılan menüsünde F8, gelişmiş önyükleme ayarları menüsünü kolayca girmeye kolaylık sağlar, ancak seri konsol diğer tüm işlev anahtarlarıyla uyumludur. Seri konsol kullanmakta olduğunuz bilgisayara bağlı olarak, klavyenizde **Fn**  + **F1** (veya F2, F3, vb.) tuşlarına basmanız gerekebilir.

### <a name="use-wsl-in-serial-console"></a>Seri konsolda WSL kullanma
Linux için Windows alt sistemi (WSL) Windows Server 2019 veya üzeri için etkinleştirilmiştir, bu nedenle Windows Server 2019 veya sonraki bir sürümünü çalıştırıyorsanız, seri konsol içinde kullanmak üzere WSL 'yi etkinleştirmek de mümkündür. Bu, Linux komutlarına alışkın olan kullanıcılar için yararlı olabilir. Windows Server için WSL 'yi etkinleştirme yönergeleri için bkz. [Yükleme Kılavuzu](https://docs.microsoft.com/windows/wsl/install-on-server).

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>Windows VM/sanal makine ölçek kümesi örneğinizi seri konsol içinde yeniden başlatın
Güç düğmesine gidip "VM 'yi yeniden Başlat" seçeneğine tıklayarak seri konsol içinde yeniden başlatma başlatabilirsiniz. Bu işlem bir VM yeniden başlatması başlatır ve Azure portal içinde yeniden başlatma ile ilgili bir bildirim görürsünüz.

Bu, seri konsol deneyiminden çıkmadan önyükleme menüsüne erişmek isteyebileceğiniz durumlarda faydalıdır.

![Windows seri konsol yeniden başlatması](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-the-serial-console"></a>Seri konsolunu devre dışı bırakma
Varsayılan olarak, tüm aboneliklerde seri konsol erişimi etkindir. Seri konsolunu abonelik düzeyinde ya da VM/sanal makine ölçek kümesi düzeyinde devre dışı bırakabilirsiniz. Ayrıntılı yönergeler için [Azure seri konsolunu etkinleştir ve devre dışı bırak](./serial-console-enable-disable.md)' ı ziyaret edin.

## <a name="serial-console-security"></a>Seri konsol güvenliği

### <a name="access-security"></a>Erişim güvenliği
Seri konsol erişimi, sanal makine [katılımcısı](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) veya daha yüksek bir erişim rolüne sahip kullanıcılarla sınırlıdır. Azure Active Directory kiracınız Multi-Factor Authentication (MFA) gerektiriyorsa, seri konsolunun erişimi [Azure Portal](https://portal.azure.com)üzerinden olduğu için seri konsoluna ERIŞIME de MFA gerekir.

### <a name="channel-security"></a>Kanal güvenliği
İleri ve geri gönderilen tüm veriler kabloda şifrelenir.

### <a name="audit-logs"></a>Denetim günlükleri
Seri konsoluna tüm erişim şu anda sanal makinenin [önyükleme tanılama](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) günlüklerinde oturum açtı. Bu günlüklere erişim, Azure sanal makine yöneticisi tarafından sahiplenilir ve denetlenir.

> [!CAUTION]
> Konsolun hiçbir erişim parolası günlüğe kaydedilmez. Ancak, konsolda çalıştırılan komutlar, parola, gizli dizileri, Kullanıcı adlarını veya herhangi bir kişisel olarak tanımlanabilir bilgi (PII) gibi bir diğer biçimi içeriyorsa, bunlar VM önyüklemesi tanılama günlüklerine yazılır. Seri konsolunun geri Kaydır işlevi uygulamasının bir parçası olarak, diğer tüm görünür metinle birlikte yazılır. Bu Günlükler dairesel ve yalnızca tanılama depolama hesabı için okuma iznine sahip olan bireyler bunlara erişebilir. Ancak, gizli dizi ve/veya PII içerebilen herhangi bir şey için Uzak Masaüstü kullanmanın en iyi yöntemini takip etmenizi öneririz.

### <a name="concurrent-usage"></a>Eşzamanlı Kullanım
Bir Kullanıcı seri konsoluna bağlandıysa ve başka bir kullanıcı aynı sanal makineye erişim isteğinde bulunursa, ilk kullanıcının bağlantısı kesilir ve ikinci Kullanıcı aynı oturuma bağlanır.

> [!CAUTION]
> Bu, bağlantısı kesilen bir kullanıcının oturum açmayacağı anlamına gelir. Bağlantı kesildikten sonra (SıGHUP veya benzer mekanizmayı kullanarak) bir oturumu kapatma özelliği hala yol haritasında bulunur. Windows için, SAC 'de etkin bir otomatik zaman aşımı vardır. Linux için, Terminal zaman aşımı ayarını yapılandırabilirsiniz.

## <a name="accessibility"></a>Erişilebilirlik
Erişilebilirlik, Azure seri konsolu için önemli bir odadır. Bu uçta, seri konsolunun görsel için erişilebilir olduğunu ve sorunsuz bir şekilde bir fare kullanamayacak kişileri duyduk.

### <a name="keyboard-navigation"></a>Klavye gezintisi
Azure portal seri konsol arabiriminde gezinmek için klavyenizde **Tab** tuşunu kullanın. Konumunuz ekranda vurgulanacaktır. Seri konsol penceresinin odağını bırakmak için klavyenizde **Ctrl** +**F6** tuşuna basın.

### <a name="use-the-serial-console-with-a-screen-reader"></a>Seri konsolu 'nu ekran okuyucu ile kullanma
Seri konsolunda yerleşik ekran okuyucusu desteği vardır. Bir ekran okuyucusu açıkken dolaşma, ekran okuyucu tarafından şu anda seçili olan düğme için alternatif metnin yüksek sesle okunmasıyla izin verir.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Seri konsoluna erişmek için yaygın senaryolar

Senaryo          | Seri konsolundaki eylemler
:------------------|:-----------------------------------------
Yanlış güvenlik duvarı kuralları | Seri konsoluna erişin ve Windows güvenlik duvarı kurallarını onarın.
Dosya sistemi Bozulması/denetimi | Seri konsoluna erişin ve dosya sistemini kurtarın.
RDP yapılandırma sorunları | Seri konsoluna erişin ve ayarları değiştirin. Daha fazla bilgi için bkz. [RDP belgeleri](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).
Ağ kilitleme sistemi | Sistemi yönetmek için Azure portal seri konsoluna erişin. Bazı ağ komutları [Windows komutları: cmd ve PowerShell](serial-console-cmd-ps-commands.md)içinde listelenmiştir.
Önyükleme yükleyicisine etkileşim | Seri konsol aracılığıyla BCD 'ye erişin. Bilgi için, bkz. [seri konsolundaki Windows önyükleme menüsünü etkinleştirme](#enable-the-windows-boot-menu-in-the-serial-console).

## <a name="known-issues"></a>Bilinen sorunlar
Seri konsol ve VM 'nin işletim sistemi ile ilgili bazı sorunları fark ediyoruz. Bu sorunların ve Windows VM 'Leri hafifletme adımlarının bir listesi aşağıda verilmiştir. Bu sorunlar ve azaltmaları, hem VM 'Ler hem de sanal makine ölçek kümesi örnekleri için geçerlidir. Bunlar gördüğünüz hatayla eşleşmiyorsa, yaygın [seri konsol hatalarıyla](./serial-console-errors.md)ortak seri konsol hizmeti hatalarına bakın.

Sorun                             |   Risk azaltma
:---------------------------------|:--------------------------------------------|
Bağlantı başlığından sonra **ENTER** tuşuna basmak, oturum açma isteminin görüntülenmesine neden olmaz. | Daha fazla bilgi için bkz. ENTER tuşuna basarak [hiçbir şey yapılmıyor](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md). Bu hata, Windows 'un seri bağlantı noktasına düzgün şekilde bağlanamamasına neden olan özel bir VM, sağlamlaştırılmış gereç veya önyükleme yapılandırması çalıştırıyorsanız oluşabilir. Yalnızca Windows Server VM 'Leri EMS 'nin etkin olacak şekilde yapılandırıldığı için, bu hata bir Windows 10 VM çalıştırıyorsanız da meydana gelir.
Bir Windows VM 'sine bağlanılırken yalnızca sistem durumu bilgileri gösterilir| Bu hata, Özel Yönetim Konsolu Windows görüntünüz için etkinleştirilmemişse oluşur. Windows sanal makinenizde SAC 'yi el ile etkinleştirme hakkında yönergeler için bkz. [özel veya eski görüntülerde seri konsolunu etkinleştirme](#enable-the-serial-console-in-custom-or-older-images) . Daha fazla bilgi için bkz. [Windows sistem durumu sinyalleri](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).
SAC, tarayıcıda tüm seri konsol alanını gerçekleştirmez | Bu, Windows ve Terminal Öykünücüde bilinen bir sorundur. Bu sorunu her iki ekiple izliyoruz, ancak şimdilik bir azaltma yoktur.
Çekirdek hata ayıklaması etkinse, SAC istemine yazılamıyor. | RDP 'den VM 'ye ve `bcdedit /debug {current} off` yükseltilmiş bir komut isteminden çalıştırın. RDP 'yi görmüyorsanız, işletim sistemi diskini başka bir Azure VM 'ye ekleyebilir ve `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off` çalıştırıp ve sonra diski yeniden değiştirerek bir veri diski olarak bağlı durumdayken bunu değiştirebilirsiniz.
İlk içerik yinelenen bir karakter içeriyorsa, SAC 'de PowerShell 'e yapıştırma üçüncü bir karakterle sonuçlanır. | Geçici bir çözüm için `Remove-Module PSReadLine` çalıştırarak PSReadLine modülünü geçerli oturumdan kaldırın. Bu eylem modülü silmez veya kaldırmaz.
Bazı klavye girdileri, alışılmadık SAC çıkışı oluşturur (örneğin, **[A**, **[3 ~** ). | [VT100](https://aka.ms/vtsequences) KAÇıŞ dizileri sac istemi tarafından desteklenmez.
Uzun dizeleri yapıştırma işe yaramıyor. | Seri konsol, seri bağlantı noktası bant genişliğinin aşırı yüklenmesini engellemek için, terminale yapıştırılan dizelerin uzunluğunu 2048 karakter olarak sınırlandırır.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

**S. nasıl geri bildirim gönderebilirim?**

A. @No__t_0 bir GitHub sorunu oluşturarak geri bildirim sağlayın. Alternatif olarak (daha az tercih edilen), azserialhelp@microsoft.com aracılığıyla veya https://feedback.azure.com sanal makine kategorisinde geri bildirim gönderebilirsiniz.

**S. seri konsol kopyalamayı/yapıştırmayı destekliyor mu?**

A. Evet. Terminale kopyalayıp yapıştırmak için **ctrl** +**SHIFT** +**C** ve **CTRL** +**SHIFT** +**V** tuşlarını kullanın.

**S. Aboneliğimin seri konsolunu kimler etkinleştirebilir veya devre dışı bırakabilirim?**

A. Seri konsolunu abonelik genelinde bir düzeyde etkinleştirmek veya devre dışı bırakmak için abonelik için yazma izinleriniz olmalıdır. Yazma izni olan roller yönetici veya sahip rolleri içerir. Özel rollerin de yazma izinleri olabilir.

**S. sanal sunucum için seri konsoluna kimler erişebilir?**

A. VM 'nin seri konsoluna erişmesi için sanal makine katılımcısı rolüne veya daha üstüne sahip olmanız gerekir.

**S. seri konsolum hiçbir şeyi görüntülüyor, ne yapmam gerekir?**

A. Resminiz, seri konsol erişimi için hatalı yapılandırılmış olabilir. Görüntünüzü seri konsolunu etkinleştirecek şekilde yapılandırma hakkında daha fazla bilgi için bkz. [özel veya eski görüntülerde seri konsolunu etkinleştirme](#enable-the-serial-console-in-custom-or-older-images).

**S. sanal makine ölçek kümeleri için seri konsol kullanılabilir mi?**

A. Evet öyle! Bkz. [Sanal Makine Ölçek Kümeleri Için seri konsol](./serial-console-overview.md#serial-console-for-virtual-machine-scale-sets)

## <a name="next-steps"></a>Sonraki adımlar
* Windows SAC 'de kullanabileceğiniz, CMD ve PowerShell komutlarına yönelik ayrıntılı yönergeler için bkz. [Windows komutları: cmd ve PowerShell](serial-console-cmd-ps-commands.md).
* Seri konsol, [Linux](serial-console-linux.md) sanal makineleri için de kullanılabilir.
* [Önyükleme tanılaması](boot-diagnostics.md)hakkında daha fazla bilgi edinin.
