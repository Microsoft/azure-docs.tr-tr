---
title: StorSimple cihaz yönetimi için PowerShell
description: StorSimple cihazınızı yönetmek için StorSimple için Windows PowerShell kullanmayı öğrenin.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/25/2021
ms.author: alkohli
ms.openlocfilehash: 3292f337a39e2adb7dc5e78f12785ead8759f5d1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102611955"
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>StorSimple için Windows PowerShell’i kullanarak cihazınızı yönetme

## <a name="overview"></a>Genel Bakış

StorSimple için Windows PowerShell, Microsoft Azure StorSimple cihazınızı yönetmek için kullanabileceğiniz bir komut satırı arabirimi sağlar. Adından da anlaşılacağı gibi, bu, kısıtlı bir çalışma alanında oluşturulan Windows PowerShell tabanlı, komut satırı arabirimidir. Komut satırında kullanıcının perspektifinden, kısıtlı bir çalışma alanı Windows PowerShell 'in sınırlı bir sürümü olarak görünür. Windows PowerShell 'in bazı temel özelliklerini koruyarak, bu arabirimde Microsoft Azure StorSimple cihazınızı yönetmek için kullanabileceğiniz başka ve ayrılmış cmdlet 'ler vardır.

Bu makalede, bu arabirime nasıl bağlanabileceğinizi de içeren StorSimple için Windows PowerShell özellikleri açıklanmakta ve bu arabirimi kullanarak gerçekleştirebileceğiniz adım adım yordamlara veya iş akışlarına bağlantılar yer almaktadır. İş akışları, cihazınızı nasıl kaydedeceğinizi, cihazınızda ağ arabirimini yapılandırmanıza, cihazın bakım modunda olmasını gerektiren güncelleştirmeleri yüklemeye, cihaz durumunu değiştirmeye ve karşılaşabileceğiniz sorunları gidermeye da dahildir.

Bu makaleyi okuduktan sonra şunları yapabilirsiniz:

* StorSimple için Windows PowerShell kullanarak StorSimple cihazınıza bağlanın.
* StorSimple için Windows PowerShell kullanarak StorSimple cihazınızı yönetin.
* StorSimple için Windows PowerShell yardım alın.

> [!NOTE]
> * StorSimple için Windows PowerShell cmdlet 'leri, StorSimple cihazınızı bir seri konsolundan veya Windows PowerShell uzaktan iletişim yoluyla uzaktan yönetmenize olanak sağlar. Bu arabirimde kullanılabilecek tek tek cmdlet 'ler hakkında daha fazla bilgi için, [StorSimple için Windows PowerShell için cmdlet başvurusuna](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps&preserve-view=true)gidin.
> * Azure PowerShell StorSimple cmdlet 'leri, komut satırından StorSimple hizmet düzeyi ve geçiş görevlerini otomatikleştirmenizi sağlayan farklı cmdlet koleksiyonlardır. StorSimple için Azure PowerShell cmdlet 'leri hakkında daha fazla bilgi için [Azure StorSimple cmdlet başvurusuna](/powershell/module/servicemanagement/azure.service/#azure)gidin.


Aşağıdaki yöntemlerden birini kullanarak StorSimple için Windows PowerShell erişebilirsiniz:

* [StorSimple cihaz seri konsoluna bağlanma](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Windows PowerShell kullanarak StorSimple 'a uzaktan bağlanma](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Cihaz seri konsolu aracılığıyla StorSimple için Windows PowerShell bağlanma

StorSimple için Windows PowerShell bağlanmak için [PuTTY](https://www.putty.org/) veya benzer bir terminal öykünme yazılımını indirebilirsiniz. Microsoft Azure StorSimple cihaza erişmek için özel olarak PuTTY 'yi yapılandırmanız gerekir. Aşağıdaki konular, PuTTy 'in nasıl yapılandırılacağı ve cihaza nasıl bağlanacağı hakkında ayrıntılı adımlar içerir. Seri konsolundaki çeşitli menü seçenekleri de açıklanır.

### <a name="putty-settings"></a>PuTTY ayarları

Seri konsolundan Windows PowerShell arabirimine bağlanmak için aşağıdaki PuTTY ayarlarını kullandığınızdan emin olun.

#### <a name="to-configure-putty"></a>PuTTY 'yi yapılandırmak için

1. PuTTY yeniden **yapılandırması** Iletişim kutusundaki **Kategori** bölmesinde **klavye**' yi seçin.
2. Aşağıdaki seçeneklerde (yeni bir oturum başlattığınızda varsayılan ayarlar) seçili olduğundan emin olun.
   
   | Klavye öğesi | Şunu seçin: |
   | --- | --- |
   | Geri Al tuşu |Denetim-? (127) |
   | Giriş ve bitiş anahtarları |Standart |
   | İşlev tuşları ve tuş takımı |ESC [n ~ |
   | İmleç anahtarlarının ilk durumu |Normal |
   | Sayısal tuş takımının ilk durumu |Normal |
   | Ek klavye özelliklerini etkinleştir |Control-Alt AltGr 'tan farklı |
   
    ![Desteklenen Putty ayarları](./media/storsimple-windows-powershell-administration/IC740877.png)
3. **Uygula**’ya tıklayın.
4. **Kategori** bölmesinde **çeviri**' yi seçin.
5. **Uzak karakter kümesi** liste kutusunda **UTF-8**' i seçin.
6. **Çizgi çizim karakterlerinin Işlenmesiyle**, **Unicode çizgi çizim kod noktalarını kullan**' ı seçin. Aşağıdaki ekran görüntüsünde doğru PuTTY seçimleri gösterilmektedir.
   
    ![UTF Putty ayarları](./media/storsimple-windows-powershell-administration/IC740878.png)
7. **Uygula**’ya tıklayın.

Artık, aşağıdaki adımları uygulayarak cihaz seri konsoluna bağlanmak için PuTTY kullanabilirsiniz.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>Seri konsol hakkında

Seri konsol aracılığıyla StorSimple cihazınızın Windows PowerShell arabirimine eriştiğinizde, bir başlık iletisi gösterilir ve ardından menü seçenekleri gelir.

Başlık iletisi, model, ad, yüklü yazılım sürümü ve eriştiğiniz denetleyicinin durumu gibi temel StorSimple cihaz bilgilerini içerir. Aşağıdaki görüntüde bir başlık iletisi örneği gösterilmektedir.

![Seri başlık iletisi](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> Bağlı olduğunuz denetleyicinin _etkin_ veya _Pasif_ olduğunu belirlemek için başlık iletisini kullanabilirsiniz.

Aşağıdaki görüntüde, seri konsol menüsünde bulunan çeşitli çalışma alanı seçenekleri gösterilmektedir.

![Cihazınızı kaydetme 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Aşağıdaki ayarlardan seçim yapabilirsiniz:

1. **Tam erişimle oturum açın.**
   Bu seçenek, yerel denetleyicideki **Ssadminconsole** çalışma alanına bağlanmanızı sağlar (uygun kimlik bilgileriyle). (Yerel denetleyici, şu anda StorSimple cihazınızın seri konsolundan erişmekte olduğunuz denetleyiciden oluşur.) Bu seçenek, olası cihaz sorunlarını gidermek için Microsoft Desteği Kısıtlanmamış çalışma alanına (bir destek oturumu) erişmesine izin vermek için de kullanılabilir. Oturum açmak için 1 seçeneğini kullandıktan sonra, Microsoft Desteği mühendisinin belirli bir cmdlet çalıştırarak Kısıtlanmamış çalışma alanına erişmesine izin verebilirsiniz. Ayrıntılar için [destek oturumu başlatma](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple)bölümüne bakın.
   
2. **Tam erişim ile eş denetleyicide oturum açın.**
   Bu seçenek, bağlantı denetleyicisindeki **Ssadminconsole** çalışma alanına (uygun kimlik bilgileriyle) bağlanabiliyor olmanız dışında, seçenek 1 ile aynıdır. StorSimple cihazı, etkin-Pasif yapılandırmada iki denetleyicisi olan yüksek oranda kullanılabilir bir cihaz olduğundan, eş, seri konsol üzerinden eriştiğiniz cihazdaki diğer denetleyiciye başvurur.
   1. seçeneğe benzer şekilde, bu seçenek Microsoft Desteği eş denetleyicide Kısıtlanmamış çalışma alanına erişmesine izin vermek için de kullanılabilir.

3. **Sınırlı erişimle bağlanın.**
   Bu seçenek, Windows PowerShell arabirimine sınırlı modda erişmek için kullanılır. Sizden erişim kimlik bilgileri istenmez. Bu seçenek, Seçenekler 1 ve 2 ile karşılaştırıldığında daha kısıtlı bir çalışma alanına bağlanır.  Seçenek 1 aracılığıyla bu çalışma alanında *gerçekleştirilemediği görevlerden* bazıları şunlardır:
   
   * Fabrika ayarlarına sıfırla
   * Parolayı değiştirme
   * Destek erişimini etkinleştirme veya devre dışı bırakma
   * Güncelleştirme uygulama
   * Düzeltmeleri yükler

     > [!NOTE]
     > Bu, Cihaz Yöneticisi parolasını unuttuysanız ve 1 veya 2 ' den bağlantı kurmak için tercih edilen seçenektir.

4. **Dili Değiştir.**
   Bu seçenek, Windows PowerShell arabirimindeki görüntüleme dilini değiştirmenize izin verir. Desteklenen diller Ingilizce, Japonca, Rusça, Fransızca, Güney Korece, Ispanyolca, Italyanca, Almanca, Çince ve Portekizce 'Dir.

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>StorSimple için Windows PowerShell kullanarak StorSimple 'a uzaktan bağlanma

Windows PowerShell uzaktan iletişimini, StorSimple cihazınıza bağlanmak için kullanabilirsiniz. Bu şekilde bağladığınızda, bir menü görmezsiniz. (Yalnızca, bağlanmak için cihazda seri konsolunu kullanıyorsanız bir menü görürsünüz. Uzaktan bağlanma, doğrudan seri konsolundaki "seçenek 1 – tam erişim" ile eşdeğer bir bağlantı alır.) Windows PowerShell uzaktan iletişimi ile belirli bir çalışma alanına bağlanırsınız. Ayrıca, görüntüleme dilini de belirtebilirsiniz.

Görüntüleme dili, seri konsol menüsündeki **Dili Değiştir** seçeneğini kullanarak ayarladığınız dilden bağımsızdır. Uzak PowerShell, hiçbir belirtilmemişse, bağlandığınız cihazın yerel ayarını otomatik olarak seçer.

> [!NOTE]
> Microsoft Azure sanal konakları ve StorSimple bulut gereçlerinde çalışıyorsanız, bulut gerecine bağlanmak için Windows PowerShell uzaktan iletişimini ve sanal ana bilgisayarı kullanabilirsiniz. Windows PowerShell oturumundan bilgilerin kaydedileceği konakta bir paylaşma konumu ayarladıysanız, _herkesin_ yalnızca kimliği doğrulanmış kullanıcıları içerdiğine dikkat edin. Bu nedenle, paylaşımın _Herkes_ tarafından erişime izin verecek şekilde ayarladıysanız ve kimlik bilgilerini belirtmeden bağlanıyorsanız, kimliği doğrulanmamış anonim asıl kullanılır ve bir hata görürsünüz. Bu sorunu onarmak için, paylaşılan konak üzerinde Konuk hesabını etkinleştirmeniz ve ardından Konuk hesabına paylaşıma tam erişim sağlamanız gerekir veya Windows PowerShell cmdlet 'i ile birlikte geçerli kimlik bilgilerini belirtmeniz gerekir.


Windows PowerShell uzaktan iletişimini kullanarak bağlanmak için HTTP veya HTTPS kullanabilirsiniz. Aşağıdaki öğreticilerde bulunan yönergeleri kullanın:

* [HTTP kullanarak uzaktan bağlanma](storsimple-8000-remote-connect.md#connect-through-http)
* [HTTPS kullanarak uzaktan bağlanma](storsimple-8000-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Bağlantı güvenliği konuları

StorSimple için Windows PowerShell bağlanma konusunda karar verirken aşağıdaki faktörleri göz önünde bulundurun:

* Doğrudan cihaz seri konsoluna bağlanmak güvenlidir, ancak ağ anahtarları üzerinden seri konsoluna bağlanmak değildir. Ağ anahtarları üzerinden cihaz seri durumuna bağlanırken güvenlik riskine karşı dikkatli olun.
* HTTP oturumundan bağlantı, ağ üzerinden seri konsol üzerinden bağlanılarak daha fazla güvenlik sunabilir. Bir HTTP oturumu en güvenli bağlantı yöntemi olmasa da, güvenilir ağlarda kabul edilebilir.
* HTTPS oturumu üzerinden bağlanmak en güvenli ve önerilen seçenektir.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>StorSimple için Windows PowerShell kullanarak StorSimple cihazınızı yönetin

Aşağıdaki tabloda, StorSimple cihazınızın Windows PowerShell arabiriminde gerçekleştirilebilecek tüm ortak yönetim görevlerinin ve karmaşık iş akışlarının özeti gösterilmektedir. Her iş akışı hakkında daha fazla bilgi için, tabloda ilgili girişe tıklayın.

#### <a name="windows-powershell-for-storsimple-workflows"></a>StorSimple için Windows PowerShell iş akışları

| Bunu yapmak istiyorsanız... | Bu yordamı kullanın. |
| --- | --- |
| Cihazınızı kaydetme |[StorSimple için Windows PowerShell kullanarak cihazı yapılandırma ve kaydetme](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| Web proxy yapılandırma</br>Web proxy ayarlarını görüntüle |[StorSimple cihazınız için Web proxy 'yi yapılandırma](storsimple-8000-configure-web-proxy.md) |
| Cihazınızda VERI 0 ağ arabirimi ayarlarını değiştirme |[StorSimple cihazınız için VERI 0 ağ arabirimini değiştirme](storsimple-8000-modify-data-0.md) |
| Denetleyiciyi durdur </br> Bir denetleyiciyi yeniden başlatma veya kapatma </br> Bir cihazı kapatma</br>Cihazı varsayılan fabrika ayarlarına sıfırlama |[Cihaz denetleyicilerini yönetme](storsimple-8000-manage-device-controller.md) |
| Bakım modu güncelleştirmelerini ve düzeltmeleri yükler |[Cihazınızı güncelleştirme](storsimple-update-device.md) |
| Bakım modunu girin </br>Bakım modundan çık |[StorSimple cihaz modları](storsimple-8000-device-modes.md) |
| Destek paketi oluşturma</br>Bir destek paketinin şifresini çözün ve düzenleyin |[Destek paketi oluşturma ve yönetme](storsimple-8000-create-manage-support-package.md) |
| Destek oturumu Başlat</br> |[StorSimple için Windows PowerShell bir destek oturumu başlatın](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>StorSimple için Windows PowerShell yardım alın

StorSimple için Windows PowerShell, cmdlet yardımı kullanılabilir. Sisteminizde yardımı güncelleştirmek için kullanabileceğiniz, bu yardım 'ın çevrimiçi, güncel bir sürümü de mevcuttur.

Bu arabirimde yardım almak, Windows PowerShell 'de yardım almaya benzer ve yardım ile ilgili cmdlet 'lerinin çoğu çalışacaktır. Windows PowerShell Online için yardım bulabilirsiniz: [Microsoft. PowerShell. Core](/powershell/module/Microsoft.PowerShell.Core/).

<!--The following is a brief description of the types of Help for this Windows PowerShell interface, including how to update the Help. - OK to remove? Transition not needed.-->

### <a name="to-get-help-for-a-cmdlet"></a>Bir cmdlet için yardım almak üzere

* Herhangi bir cmdlet veya işlev hakkında yardım almak için aşağıdaki komutu kullanın: `Get-Help <cmdlet-name>`
* Herhangi bir cmdlet 'e yönelik çevrimiçi yardım almak için, önceki cmdlet 'i `-Online` parametresiyle kullanın: `Get-Help <cmdlet-name> -Online`
* Tam yardım için `–Full` parametresini kullanabilir ve örnekler için `–Examples` parametresini kullanabilirsiniz.

### <a name="to-update-help"></a>Yardım 'ı güncelleştirmek için

Windows PowerShell arabirimindeki yardımı kolayca güncelleştirebilirsiniz. Sisteminizdeki yardımı güncelleştirmek için aşağıdaki adımları gerçekleştirin.

#### <a name="to-update-cmdlet-help"></a>Cmdlet yardımını güncelleştirmek için
1. Windows PowerShell 'i **yönetici olarak çalıştır** seçeneğiyle başlatın.
2. Komut isteminde şunu yazın:  `Update-Help`
3. Güncelleştirilmiş Yardım dosyaları yüklenecek.
4. Yardım dosyaları yüklendikten sonra, `Get-Help Get-Command` Yardım 'ın kullanılabildiği cmdlet 'lerin listesini göstermek için şunu yazın:.

> [!NOTE]
> Bir çalışma alanındaki tüm kullanılabilir cmdlet 'lerin listesini almak için, ilgili menü seçeneğinde oturum açın ve `Get-Command` cmdlet 'ini çalıştırın.


## <a name="next-steps"></a>Sonraki adımlar

Yukarıdaki iş akışlarından birini gerçekleştirirken StorSimple cihazınız ile ilgili herhangi bir sorunla karşılaşırsanız, [StorSimple dağıtımlarının sorunlarını gidermeye yönelik araçlar](storsimple-8000-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments)bölümüne bakın.