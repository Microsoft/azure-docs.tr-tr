---
title: StorSimple cihaz yönetimi için PowerShell | Microsoft Docs
description: StorSimple için Windows PowerShell, StorSimple Cihazınızı yönetmek için kullanmayı öğrenin.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 7e488a1b2a63323361a597aaa6a438630fc09a05
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621629"
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Cihazınızı yönetmek için StorSimple için Windows PowerShell kullanma

## <a name="overview"></a>Genel Bakış

StorSimple için Windows PowerShell, Microsoft Azure StorSimple Cihazınızı yönetmek için kullanabileceğiniz bir komut satırı arabirimi sağlar. Adından da anlaşılacağı gibi bir Windows PowerShell tabanlı komut satırı kısıtlı bir çalışma alanında oluşturulan arabirimidir. Komut satırında kullanıcı açısından bakıldığında, kısıtlı bir çalışma alanı kısıtlı bir Windows PowerShell sürümü olarak görünür. Bu arabirimi, Windows PowerShell temel özelliklerden bazıları korurken, Microsoft Azure StorSimple aygıtınızı yönetmeye yönelik olarak ek adanmış cmdlet'leri vardır.

Bu makalede, bu arabirimine nasıl bağlanacağını dahil olmak üzere StorSimple özellikleri için Windows PowerShell açıklar ve adım adım yordamları veya kullanarak bu arabirimi gerçekleştiren iş akışlarını bağlantılar içerir. Cihazınızı kaydetmek, Cihazınızda ağ arabirimini yapılandırın, cihaz bakım modunda olması, cihaz durumu değiştirin ve karşılaşabileceğiniz sorunları gidermek için gerekli güncelleştirmeleri yüklemek nasıl iş akışlarını içerir.

Bu makaleyi okuduktan sonra şunları yapabilir:

* StorSimple Cihazınızı StorSimple için Windows PowerShell kullanarak bağlanın.
* StorSimple Cihazınızı StorSimple için Windows PowerShell kullanarak yönetin.
* Yardım, StorSimple için Windows PowerShell'de alın.

> [!NOTE]
> * StorSimple cmdlet'leri için Windows PowerShell, StorSimple Cihazınızı bir seri konsol üzerinden veya Windows PowerShell uzaktan iletişimini aracılığıyla uzaktan yönetmek izin verin. Bu arabirimde kullanılabilecek tek cmdlet'lerin her birine hakkında daha fazla bilgi için Git [için StorSimple için Windows PowerShell cmdlet başvurusu](https://technet.microsoft.com/library/dn688168.aspx).
> * Azure PowerShell, StorSimple, StorSimple hizmet düzeyi otomatik hale getirmenizi ve geçiş görevleri komut satırından izin verin cmdlet'lerinin farklı bir koleksiyon cmdlet'leridir. StorSimple için Azure PowerShell cmdlet'leri hakkında daha fazla bilgi için Git [Azure StorSimple cmdlet başvurusu](https://docs.microsoft.com/powershell/module/servicemanagement/azure/?view=azuresmps-4.0.0&viewFallbackFrom=azuresmps-3.7.0#azure).


Aşağıdaki yöntemlerden birini kullanarak StorSimple için Windows PowerShell erişebilirsiniz:

* [StorSimple cihaz seri konsoluna bağlanmak](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [İçin Windows PowerShell kullanarak StorSimple uzaktan bağlanma](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Windows PowerShell için StorSimple cihaz seri Konsolu bağlanın.

Yapabilecekleriniz [PuTTY indirme](https://www.putty.org/) veya benzer StorSimple için Windows PowerShell'e bağlanmak için bir terminal öykünme yazılımı. PuTTY özel Microsoft Azure StorSimple cihazınıza erişim hakkı yapılandırmanız gerekir. Aşağıdaki konular, PuTTy yapılandırma ve cihaza bağlanma hakkında ayrıntılı adımları içerir. Seri konsol içinde çeşitli menü seçeneklerini de açıklanmıştır.

### <a name="putty-settings"></a>PuTTY ayarları

Seri konsoldan Windows PowerShell arabirimine bağlamak için aşağıdaki PuTTY ayarları kullandığınızdan emin olun.

#### <a name="to-configure-putty"></a>PuTTY yapılandırmak için

1. PuTTY içinde **yeniden yapılandırma** iletişim kutusundaki **kategori** bölmesinde **klavye**.
2. Aşağıdaki seçenekleri'nin seçili olduğundan emin olun (yeni bir oturum başlattığınızda, varsayılan ayarları bunlar).
   
   | Klavye öğesi | Seçim |
   | --- | --- |
   | Geri tuşu |Denetim-? (127) |
   | Giriş ve bitiş anahtarları |Standart |
   | İşlev tuşları ve tuş |ESC [n ~ |
   | İmleç anahtarları başlangıç durumu |Normal |
   | Sayısal tuş takımındaki başlangıç durumu |Normal |
   | Ek klavye özelliklerini etkinleştirme |Denetimi Alt AltGr farklı. |
   
    ![Desteklenen Putty ayarları](./media/storsimple-windows-powershell-administration/IC740877.png)
3. **Uygula**'ya tıklayın.
4. İçinde **kategori** bölmesinde **çeviri**.
5. İçinde **uzak karakter kümesi** liste kutusu, select **UTF-8**.
6. Altında **çizim için karakter işleme**seçin **kullanım Unicode çizim kod noktaları**. Aşağıdaki ekran görüntüsünde, doğru PuTTY seçimlerini gösterir.
   
    ![UTF Putty ayarları](./media/storsimple-windows-powershell-administration/IC740878.png)
7. **Uygula**'ya tıklayın.

Artık, aşağıdaki adımları uygulayarak cihaz seri konsoluna bağlanmak için PuTTY kullanabilirsiniz.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>Seri Konsolu hakkında

Windows PowerShell arabirimi StorSimple cihazınızın seri konsolu, bir başlık iletisi aracılığıyla sunulan eriştiğinizde, menü seçenekleri tarafından izlenen.

Başlık iletisi modeli, adı, yüklü yazılım sürümü ve eğer denetleyici durumu gibi temel StorSimple cihaz bilgileri içerir. Aşağıdaki görüntüde bir başlık iletisi örneği gösterilmektedir.

![Seri tanıtıcı iletisi](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> Başlık iletisi bağlandığınızdan denetleyicisini belirlemek için kullanabileceğiniz _etkin_ veya _pasif_.

Aşağıdaki görüntüde, seri konsol menüsünde kullanılabilir olan çeşitli çalışma alanı seçeneklerini gösterir.

![2 Cihazınızı kaydedin](./media/storsimple-windows-powershell-administration/IC740906.png)

Aşağıdaki ayarlardan birini seçebilirsiniz:

1. **Tam erişimle oturum açmak** bu seçenek (uygun kimlik bilgileriyle) bağlanmanıza olanak tanır **SSAdminConsole** yerel denetleyicisinde çalışma. (Yerel denetleyicisi şu anda StorSimple cihazınızın seri konsol üzerinden eriştiğiniz denetleyicisi içindir.) Bu seçenek, cihaz olası sorunları gidermek için Kısıtlanmamış çalışma (Destek oturumu) erişmek Microsoft Support izin vermek için de kullanılabilir. Oturum açmak için 1 seçeneğini kullandıktan sonra belirli bir cmdlet çalıştırılarak Kısıtlanmamış çalışma erişmek Microsoft Support mühendisi izin verebilirsiniz. Ayrıntılar için başvurmak [destek oturumu başlatmak](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).
   
2. **Eş denetleyicisine tam erişimle oturum** (uygun kimlik bilgileriyle) bağlanabilirsiniz dışında bu seçeneği, 1. seçenek aynıdır **SSAdminConsole** eş denetleyicisinde çalışma. StorSimple cihazı bir Aktif-Pasif yapılandırmada iki denetleyici bir yüksek kullanılabilirlik cihazla olduğundan, eş seri konsol üzerinden eriştiğiniz cihaz diğer denetleyiciye ifade eder).
   Seçenek 1 benzer, bu seçenek ayrıca Microsoft eş denetleyicisine Kısıtlanmamış çalışma erişmeye Support izin vermek için kullanılabilir.

3. **Sınırlı erişim ile bağlanma** bu seçenek, Windows PowerShell arabiriminde sınırlı modu erişmek için kullanılır. Erişim kimlik bilgileri istenmez. Bu seçenek 1 ve 2 seçeneklerine kıyasla daha sınırlı bir çalışma bağlanır.  1\. seçenek kullanılabilir olan görevlerden bazılarını, **olamaz* bu çalışma alanında gerçekleştirilmesi şunlardır:
   
   * Fabrika ayarlarına sıfırlama
   * Parola Değiştir
   * Etkinleştirmek veya devre dışı destek erişimi
   * Güncelleştirme uygulama
   * Düzeltmeleri yüklemek

     > [!NOTE]
     > Cihaz Yöneticisi parolasını unutmuş ve 1 veya 2'seçeneği bağlanamıyorsanız tercih edilen seçenek budur.

4. **Dili Değiştir** Windows PowerShell arabiriminde görüntüleme dilini değiştirmek bu seçeneği sağlar. Desteklenen diller şunlardır: İngilizce, Japonca, Rusça, Fransızca, Kore Güney, İspanyolca, İtalyanca, Almanca, Çince ve Portekizce (Brezilya).

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>StorSimple için Windows PowerShell kullanarak StorSimple uzaktan bağlanma

StorSimple Cihazınızı bağlamak için Windows PowerShell uzaktan iletişimi'ni kullanabilirsiniz. Bu şekilde bağlandığınızda menü görmezsiniz. (Yalnızca seri konsol cihaza bağlanmak için kullandığınız bir menü görürsünüz. Uzaktan bağlanma sizi doğrudan "seçeneği 1 – tam erişim" seri konsol denk götürür.) Windows PowerShell uzaktan iletişimiyle belirli bir çalışma alanı için bağlanın. Ayrıca, görüntüleme dili de belirtebilirsiniz.

Görüntüleme dilini kullanarak ayarladığınız dil bağımsızdır **dil Değiştir** seri konsol menüsünde seçeneği. Uzak PowerShell belirtilmezse, bağlanırken kullandığınız yerel ayar cihazın otomatik olarak seçer.

> [!NOTE]
> Microsoft Azure sanal konaklar ve StorSimple bulut Gereçleri ile çalışıyorsanız, bulut gerecine bağlanmak için Windows PowerShell uzaktan iletişimini ve sanal ana bilgisayar kullanabilirsiniz. Windows PowerShell oturumundan bilgi kaydetmek için konakta bir paylaşım konumuna ayarladıysanız farkında olmalıdır, _herkes_ sorumlu kimliği doğrulanmış kullanıcılar yalnızca içerir. Bu nedenle, paylaşımı tarafından erişime izin verecek şekilde ayarlarsanız _herkes_ ve kimlik bilgilerini belirtmeden bağlanmak, kimliği doğrulanmamış anonim sorumlusu kullanılır ve bir hata görürsünüz. Bu sorunu düzeltmek için paylaşımında barındırabileceğiniz Konuk hesabı etkinleştirmeli ve ardından Konuk paylaşıma hesabına tam erişim verin veya Windows PowerShell cmdlet'i ile birlikte geçerli kimlik bilgilerini belirtmeniz gerekir.


HTTP veya HTTPS Windows PowerShell uzaktan bağlanmak için kullanabilirsiniz. Aşağıdaki öğreticilerde yönergeleri kullanın:

* [HTTP kullanarak uzaktan bağlanma](storsimple-8000-remote-connect.md#connect-through-http)
* [HTTPS kullanarak uzaktan bağlanma](storsimple-8000-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Bağlantı güvenlik konuları

StorSimple için Windows PowerShell'e bağlanma verirken aşağıdakileri göz önünde bulundurun:

* Doğrudan cihaz seri konsoluna bağlanmak güvenlidir, ancak ağ anahtarları seri konsoluna bağlanmak değil. Cihaz seri ağ anahtarları bağlanırken güvenlik riskini dikkatli olun.
* Bir HTTP oturumu aracılığıyla bağlanan ağ üzerinden seri konsol üzerinden bağlanma daha fazla güvenlik sunar. Bu en güvenli yöntem olmamasına karşın, güvenilen ağlarda kabul edilebilir.
* Bir HTTPS oturum üzerinden bağlanma, en güvenli ve önerilen seçenek değildir.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>StorSimple Cihazınızı StorSimple için Windows PowerShell kullanarak yönetme

Aşağıdaki tabloda StorSimple cihazınızın Windows PowerShell arabiriminden genel yönetim görevleri ve gerçekleştirilebilecek karmaşık iş akışları bir özetini gösterir. Uygun giriş tablosundaki her bir iş akışı hakkında daha fazla bilgi için tıklayın.

#### <a name="windows-powershell-for-storsimple-workflows"></a>StorSimple iş akışları için Windows PowerShell

| Bunu yapmak istiyorsanız... | Bu yordamı kullanın. |
| --- | --- |
| Cihazınızı kaydedin |[StorSimple için Windows PowerShell kullanarak cihazı yapılandırma ve kaydetme](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| Web proxy yapılandırma</br>Görünümü web proxy ayarları |[StorSimple cihazınız için Web Proxy'yi Yapılandır](storsimple-8000-configure-web-proxy.md) |
| Cihazınızda veri 0 ağ arabirimi ayarlarını değiştirme |[DATA 0 ağ arabirimindeki StorSimple cihazınız için değiştirme](storsimple-8000-modify-data-0.md) |
| Bir denetleyici Durdur </br> Yeniden başlatma veya bir denetleyiciyi kapatma </br> Bir cihazı kapatmak</br>Cihazı varsayılan fabrika ayarlarına sıfırlama |[Cihaz Denetleyicilerini Yönet](storsimple-8000-manage-device-controller.md) |
| Bakım modu güncelleştirmeleri ve düzeltmeleri yüklemeniz |[Cihazınızı güncelleştirme](storsimple-update-device.md) |
| Bakım Moduna gir </br>Bakım modundan çık |[StorSimple cihaz modları](storsimple-8000-device-modes.md) |
| Destek paketi oluşturma</br>Şifre çözme ve Destek paketini Düzenle |[Oluşturma ve bir destek paketi yönetme](storsimple-8000-create-manage-support-package.md) |
| Bir destek oturumu başlatın</br> |[Destek oturumu StorSimple için Windows PowerShell'de Başlat](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>StorSimple için Windows PowerShell'de yardım alın

StorSimple için Windows PowerShell'de cmdlet Yardım kullanılabilir. Bu Yardım çevrimiçi, güncel bir sürümünü sisteminize Yardım'ı güncelleştirmek için kullanabileceğiniz, de kullanılabilir.

Windows PowerShell'de benzer bu arabirimde Yardım alma ve yardımla ilgili cmdlet'lerin çoğu bir çalışır. Windows PowerShell Yardımı için çevrimiçi bulabilirsiniz: [Microsoft.PowerShell.Core](/powershell/module/Microsoft.PowerShell.Core/).

Yardım türleri için Yardım'a güncelleştirme dahil olmak üzere bu Windows PowerShell arabirimi kısa bir açıklaması verilmiştir.

### <a name="to-get-help-for-a-cmdlet"></a>Bir cmdlet için Yardım almak için

* Herhangi bir cmdlet veya işlevi için Yardım almak için aşağıdaki komutu kullanın: `Get-Help <cmdlet-name>`
* Herhangi bir cmdlet'i için çevrimiçi Yardım almak için önceki cmdlet'ini kullanın. `-Online` parametresi: `Get-Help <cmdlet-name> -Online`
* Tam Yardım almak için kullanabileceğiniz `–Full` parametresi ve örnekler için `–Examples` parametresi.

### <a name="to-update-help"></a>Yardım'ı güncelleştirmek için

Windows PowerShell arabiriminde Yardım kolayca güncelleştirebilirsiniz. Sisteminize Yardım'ı güncelleştirmek için aşağıdaki adımları gerçekleştirin.

#### <a name="to-update-cmdlet-help"></a>Cmdlet Yardımı güncelleştirmek için
1. Windows PowerShell ile başlatma **yönetici olarak çalıştır** seçeneği.
2. Komut isteminde aşağıdakini yazın:  `Update-Help`
3. Güncelleştirilmiş Yardım dosyalarını yüklenir.
4. Yardım dosyaları yüklendikten sonra yazın: `Get-Help Get-Command`. Bu Yardım kullanılabilir cmdlet'lerin bir listesi görüntülenir.

> [!NOTE]
> Bir çalışma alanında kullanılabilir cmdlet'lerin listesini almak için karşılık gelen menü seçeneği için oturum açın ve çalıştırın `Get-Command` cmdlet'i.


## <a name="next-steps"></a>Sonraki adımlar

Yukarıdaki iş akışları birini gerçekleştirirken StorSimple cihazınızdaki tüm sorunlarla karşılaşırsanız, başvurmak [StorSimple dağıtımlarının sorunlarını gidermeye yönelik Araçlar](storsimple-8000-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).

