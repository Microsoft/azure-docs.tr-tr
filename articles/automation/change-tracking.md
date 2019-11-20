---
title: Azure Otomasyonu ile değişiklikleri izleme
description: Değişiklik İzleme çözümü, ortamınızda gerçekleşen yazılım ve Windows hizmeti değişikliklerini belirlemenize yardımcı olur.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: bobbytreed
ms.author: robreed
ms.date: 04/29/2019
ms.topic: conceptual
manager: carmonm
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0fc0aeab4e9603995130392e3560325ccaba1ffc
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73886815"
---
# <a name="track-changes-in-your-environment-with-the-change-tracking-solution"></a>Değişiklik İzleme çözümü ile ortamınızdaki değişiklikleri izleyin

Bu makale, ortamınızdaki değişiklikleri kolayca belirlemek için Değişiklik İzleme çözümünü kullanmanıza yardımcı olur. Çözüm Windows ve Linux yazılım, Windows ve Linux dosyaları, Windows kayıt defteri anahtarları, Windows Hizmetleri ve Linux Daemon 'ları üzerindeki değişiklikleri izler. Yapılandırma değişikliklerinin belirlenmesi, işlemsel sorunları belirlemenize yardımcı olabilir.

İzlenen sunuculardaki yüklü yazılım, Windows Hizmetleri, Windows kayıt defteri ve dosyalar ve Linux Daemon 'ları üzerinde yapılan değişiklikler, işlenmek üzere buluttaki Azure Izleyici hizmetine gönderilir. Alınan verilere mantık uygulanır ve bulut hizmeti verileri kaydeder. Değişiklik İzleme panosundaki bilgileri kullanarak, sunucu altyapınızda yapılan değişiklikleri kolayca görebilirsiniz.

> [!NOTE]
> Azure Otomasyonu Değişiklik İzleme sanal makinelerdeki değişiklikleri izler. Azure Resource Manager özellik değişikliklerini izlemek için bkz. Azure Kaynak grafiğinin [değişiklik geçmişi](../governance/resource-graph/how-to/get-resource-changes.md).

## <a name="supported-windows-operating-systems"></a>Desteklenen Windows işletim sistemleri

Windows işletim sisteminin aşağıdaki sürümleri resmi olarak Windows Agent için desteklenir:

* Windows Server 2008 R2 veya üzeri

## <a name="supported-linux-operating-systems"></a>Desteklenen Linux işletim sistemleri

Aşağıdaki Linux dağıtımları resmi olarak desteklenmektedir. Ancak, Linux Aracısı listelenmeyen diğer dağıtımlar üzerinde de çalıştırılabilir. Aksi belirtilmediği takdirde, listelenen her ana sürüm için tüm küçük yayınlar desteklenir.

### <a name="64-bit"></a>64 bit

* CentOS 6 ve 7
* Amazon Linux 2017,09
* Oracle Linux 6 ve 7
* Red Hat Enterprise Linux Server 6 ve 7
* Borçlu GNU/Linux 8 ve 9
* Ubuntu Linux 14,04 LTS, 16,04 LTS ve 18,04 LTS
* SUSE Linux Enterprise Server 12

### <a name="32-bit"></a>32 bit

* CentOS 6
* Oracle Linux 6
* Red Hat Enterprise Linux Server 6
* Borçlu GNU/Linux 8 ve 9
* Ubuntu Linux 14,04 LTS ve 16,04 LTS

## <a name="onboard"></a>Değişiklik İzleme ve stoku etkinleştir

Değişiklikleri izlemeye başlamak için Değişiklik İzleme ve envanter çözümünü etkinleştirmeniz gerekir. Değişiklik İzleme ve stoğa makine eklemek için birçok yol vardır. Çözümü eklemek için önerilen ve desteklenen yollar aşağıda verilmiştir.

* [Bir sanal makineden](automation-onboard-solutions-from-vm.md)
* [Birden çok makineye göz atmaya](automation-onboard-solutions-from-browse.md)
* [Otomasyon hesabınızdan](automation-onboard-solutions-from-automation-account.md)
* [Bir Azure Otomasyonu runbook 'u ile](automation-onboard-solutions.md)

## <a name="configuring-change-tracking-and-inventory"></a>Değişiklik İzleme ve envanteri yapılandırma

Bilgisayarlara nasıl bilgisayar ekleneceğini öğrenmek için ziyaret edin: [otomasyon çözümlerini ekleme](automation-onboard-solutions-from-automation-account.md). Değişiklik İzleme ve envanter çözümüne sahip bir makine ekleme yaptıktan sonra, izlenecek öğeleri yapılandırabilirsiniz. İzlemek üzere yeni bir dosya veya kayıt defteri anahtarı etkinleştirdiğinizde, hem Değişiklik İzleme hem de envanterde etkinleştirilir.

Hem Windows hem de Linux dosyalarındaki dosyalardaki değişiklikleri izlemek için dosyaların MD5 karmaları kullanılır. Daha sonra, son envanterden bu yana bir değişikliğin yapıldığını algılamak için kullanılır.

### <a name="file-integrity-monitoring-in-azure-security-center"></a>Azure Güvenlik Merkezi 'nde dosya bütünlüğü Izleme

Azure Güvenlik Merkezi, Azure Değişiklik İzleme üzerinde oluşturulmuş dosya bütünlüğü Izleme (FIM) ekledi. FIM yalnızca dosyaları ve kayıt defterlerini izlerken, tam Değişiklik İzleme çözümü de şunları içerir:

- Yazılım değişiklikleri
- Windows Hizmetleri
- Linux Daemon 'ları

FIM 'yi zaten etkinleştirdiyseniz ve tam Değişiklik İzleme çözümünü denemek istiyorsanız, aşağıdaki adımları gerçekleştirmeniz gerekir. Bu işlem, ayarlarınız kaldırılmaz.

> [!NOTE]
> Tam Değişiklik İzleme çözümünü etkinleştirmek ek ücretler oluşmasına neden olabilir. daha fazla bilgi için bkz. [Otomasyon Fiyatlandırması](https://azure.microsoft.com/pricing/details/automation/).

1. Çalışma alanına giderek ve bunları [yüklü izleme çözümleri listesinde](../azure-monitor/insights/solutions.md#list-installed-monitoring-solutions)bularak izleme çözümünü kaldırın.
2. Çözüm adına tıklayarak Özet sayfasını açın ve [izleme çözümünü kaldırma](../azure-monitor/insights/solutions.md#remove-a-monitoring-solution)bölümünde açıklandığı gibi Sil ' e tıklayın.
3. Otomasyon hesabına gidip **yapılandırma yönetimi**altındaki kaynak menüsünden **değişiklik izleme** ' yı seçerek çözümü yeniden etkinleştirin.
4. Çalışma alanı ayarı ayrıntılarınızı doğrulayın ve **Etkinleştir**' e tıklayın.

### <a name="configure-linux-files-to-track"></a>İzlemek için Linux dosyalarını yapılandırma

Linux bilgisayarlarda dosya izlemeyi yapılandırmak için aşağıdaki adımları kullanın:

1. Otomasyon hesabınızda **yapılandırma yönetimi**altında **değişiklik izleme** ' yi seçin. **Ayarları Düzenle** (dişli simgesi) seçeneğine tıklayın.
2. **Değişiklik izleme** sayfasında, **Linux dosyaları**' nı seçin ve ardından **+ Ekle** ' ye tıklayarak izlenecek yeni bir dosya ekleyin.
3. **Değişiklik izleme Için Linux dosyası Ekle**' de, izlenecek dosya veya dizinin bilgilerini girin ve **Kaydet**' e tıklayın.

|Özellik  |Açıklama  |
|---------|---------|
|Etkin     | Ayarın uygulandığını belirler.        |
|Öğe Adı     | İzlenecek dosyanın kolay adı.        |
|Grup     | Dosyaları mantıksal olarak gruplandırmak için bir grup adı.        |
|Yolu girin     | Dosyanın denetlenecek yol. Örneğin: "/etc/*. conf"       |
|Yol Türü     | İzlenecek öğenin türü, olası değerler dosya ve dizindir.        |
|Özyineleme     | İzlenecek öğe aranırken özyinelemenin kullanılıp kullanılmadığını belirler.        |
|Sudo Kullan     | Bu ayar, öğe denetlenirken sudonun kullanılıp kullanılmadığını belirler.         |
|Bağlantılar     | Bu ayar, dizinleri dolaşırken sembolik bağlantıların nasıl ele alındığını belirler.<br> **Ignore** -sembolik bağlantıları yoksayar ve başvurulan dosya/dizinleri içermez.<br>**Takip et** -özyineleme sırasında sembolik bağlantıları izler ve başvurulan dosya/dizinleri de içerir.<br>**Yönet** -sembolik bağlantıları izler ve döndürülen içeriğin değiştirilmesine izin verir.     |
|Dosya içeriğini tüm ayarlar için karşıya yükleme| İzlenen değişikliklerin dosya içeriği karşıya yükleme işlemini açar veya kapatır. Kullanılabilir seçenekler: **True** veya **False**.|

> [!NOTE]
> “Yönet” bağlantıları seçeneği önerilmez. Dosya içeriğini alma desteklenmiyor.

### <a name="configure-windows-files-to-track"></a>İzlemek için Windows dosyalarını yapılandırma

Windows bilgisayarlarda dosya izlemeyi yapılandırmak için aşağıdaki adımları kullanın:

1. Otomasyon hesabınızda **yapılandırma yönetimi**altında **değişiklik izleme** ' yi seçin. **Ayarları Düzenle** (dişli simgesi) seçeneğine tıklayın.
2. **Değişiklik izleme** sayfasında, **Windows dosyaları**' nı seçin ve ardından **+ Ekle** ' ye tıklayarak izlenecek yeni bir dosya ekleyin.
3. **Değişiklik izleme Için Windows dosyası Ekle**' de, izlenecek dosyanın bilgilerini girin ve **Kaydet**' e tıklayın.

|Özellik  |Açıklama  |
|---------|---------|
|Etkin     | Ayarın uygulandığını belirler.        |
|Öğe Adı     | İzlenecek dosyanın kolay adı.        |
|Grup     | Dosyaları mantıksal olarak gruplandırmak için bir grup adı.        |
|Yolu girin     | Dosyayı denetlemek için kullanılacak yol (örneğin, "c:\temp\\\*.txt")<br>"%winDir%\System32\\\*.*" gibi ortam değişkenleri de kullanabilirsiniz       |
|Özyineleme     | İzlenecek öğe aranırken özyinelemenin kullanılıp kullanılmadığını belirler.        |
|Dosya içeriğini tüm ayarlar için karşıya yükleme| İzlenen değişikliklerin dosya içeriği karşıya yükleme işlemini açar veya kapatır. Kullanılabilir seçenekler: **True** veya **False**.|

## <a name="wildcard-recursion-and-environment-settings"></a>Joker karakter, özyineleme ve ortam ayarları

Özyineleme, dizinler genelinde izlemeyi basitleştirecek ve ortam değişkenlerinin birden çok veya dinamik sürücü adına sahip ortamlarda dosya izlemenize imkan tanıyan joker karakterler belirtmenize olanak tanır. Aşağıdaki listede, özyineleme yapılandırılırken bilmeniz gereken yaygın bilgiler gösterilmektedir:

* Birden çok dosyayı izlemek için joker karakterler gerekir
* Joker karakterler kullanılıyorsa, bunlar yalnızca bir yolun son segmentinde kullanılabilir. (`c:\folder\*file*` veya `/etc/*.conf`gibi)
* Bir ortam değişkeni geçersiz bir yol içeriyorsa, doğrulama başarılı olur ancak envanter çalıştırıldığında bu yol başarısız olur.
* Yolu ayarlarken `c:\*.*` gibi genel yollardan kaçının. bu nedenle, çok fazla klasör çapraz yapılmakta olacaktır.

## <a name="configure-file-content-tracking"></a>Dosya Içeriği izlemeyi yapılandırma

Dosya Içeriği Değişiklik İzleme bir dosyanın değişikliğinden önceki ve sonraki içerikleri görüntüleyebilirsiniz. Bu, Windows ve Linux dosyaları için kullanılabilir, dosyadaki her değişiklik için dosyanın içeriği bir depolama hesabında depolanır ve değişiklik, satır içi veya yan yana dosyayı gösterir. Daha fazla bilgi için bkz. [izlenen bir dosyanın Içeriğini görüntüleme](change-tracking-file-contents.md).

![dosyadaki değişiklikleri görüntüleme](./media/change-tracking-file-contents/view-file-changes.png)

### <a name="configure-windows-registry-keys-to-track"></a>İzlemek için Windows kayıt defteri anahtarlarını yapılandırma

Windows bilgisayarlarda kayıt defteri anahtarı izlemeyi yapılandırmak için aşağıdaki adımları kullanın:

1. Otomasyon hesabınızda **yapılandırma yönetimi**altında **değişiklik izleme** ' yi seçin. **Ayarları Düzenle** (dişli simgesi) seçeneğine tıklayın.
2. **Değişiklik izleme** sayfasında **Windows kayıt defteri**' ni seçin ve ardından **+ Ekle** ' ye tıklayarak izlenecek yeni bir kayıt defteri anahtarı ekleyin.
3. **Değişiklik izleme Için Windows kayıt defteri Ekle**' de, izlenecek anahtarın bilgilerini girin ve **Kaydet**' e tıklayın.

|Özellik  |Açıklama  |
|---------|---------|
|Etkin     | Ayarın uygulandığını belirler.        |
|Öğe Adı     | İzlenecek kayıt defteri anahtarının kolay adı.        |
|Grup     | Kayıt defteri anahtarlarını mantıksal olarak gruplandırmak için bir grup adı.        |
|Windows Kayıt Defteri Anahtarı   | Kayıt defteri anahtarını denetlemek için yol. Örneğin: "HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup"      |

## <a name="limitations"></a>Sınırlamalar

Değişiklik İzleme çözümü Şu anda aşağıdaki öğeleri desteklemez:

* Windows kayıt defteri izleme için özyineleme
* Ağ dosya sistemleri

Diğer sınırlamalar:

* **En büyük dosya boyutu** sütunu ve değerleri geçerli uygulamada kullanılmıyor.
* 30 dakikalık toplama döngüsüne 2500 'den fazla dosya topladıysanız çözüm performansı düşebilir.
* Ağ trafiği yüksekse, değişiklik kayıtlarının görüntülenmesi altı saate kadar sürebilir.
* Bir bilgisayar kapatılırken yapılandırmayı değiştirirseniz, bilgisayar önceki yapılandırmaya ait değişiklikleri gönderebilir.

## <a name="known-issues"></a>Bilinen Sorunlar

Değişiklik İzleme çözümü Şu anda aşağıdaki sorunları yaşıyor:

* Düzeltme güncelleştirmeleri Windows Server 2016 Core RS3 makinelerinde toplanmaz.
* Linux Daemon 'ları, değişiklik olmamasına rağmen değiştirilmiş bir durumu gösterebilir. Bunun nedeni `SvcRunLevels` alanın yakalanmasına neden olur.

## <a name="change-tracking-data-collection-details"></a>Veri toplama ayrıntılarını Değişiklik İzleme

Aşağıdaki tabloda değişiklik türleri için veri toplama sıklığı gösterilmektedir. Her tür için, geçerli durumun veri anlık görüntüsü en az 24 saatte bir yenilenir:

| **Türü Değiştir** | **Sıklık** |
| --- | --- |
| Windows kayıt defteri | 50 dakika |
| Windows dosyası | 30 dakika |
| Linux dosyası | 15 dakika |
| Windows hizmetleri | 10 saniye ila 30 dakika</br> Varsayılan: 30 dakika |
| Linux Daemon 'ları | 5 dakika |
| Windows yazılımı | 30 dakika |
| Linux yazılımı | 5 dakika |

Aşağıdaki tabloda, Değişiklik İzleme makine başına izlenen öğe sınırları gösterilmektedir.

| **Kaynak** | **Sınır**| **Notlar** |
|---|---|---|
|Dosya|500||
|Kayıt Defteri|250||
|Windows yazılımı|250|Yazılım düzeltmelerini içermez|
|Linux paketleri|1250||
|Hizmetler|250||
|inin|250||

Değişiklik İzleme ve envanter kullanan bir makineye yönelik ortalama Log Analytics veri kullanımı ayda yaklaşık 40MB 'tır. Bu değer yalnızca bir yaklaşık değerdir ve ortamınıza bağlı olarak değişebilir. Sahip olduğunuz tam kullanımı görmek için ortamınızı izlemeniz önerilir.

### <a name="windows-service-tracking"></a>Windows hizmeti izleme

Windows Hizmetleri için varsayılan toplama sıklığı 30 dakikadır. Sıklığı yapılandırmak için **değişiklik izleme**gidin. **Windows Hizmetleri** sekmesindeki **Ayarları Düzenle** ' nin altında, Windows Hizmetleri için toplama sıklığını 30 dakika kadar olan 10 saniye kadar hızla değiştirmenize olanak tanıyan bir kaydırıcı vardır. Kaydırıcı çubuğunu istediğiniz sıklık kadar taşıyın ve otomatik olarak kaydeder.

![Windows Hizmetleri kaydırıcısı](./media/change-tracking/windowservices.png)

Aracı yalnızca değişiklikleri izler, bu, aracının performansını iyileştirir. Hizmetin özgün durumlarına geri döndürülmesi durumunda yüksek bir eşiğin ayarlanması değişiklikleri kaçırmayabilir. Sıklığı daha küçük bir değere ayarlamak, aksi takdirde eksik olabilecek değişiklikleri yakalamadığınızı sağlar.

> [!NOTE]
> Aracı değişiklikleri 10 saniyelik bir aralığa göre izlebilirken, verilerin portalda görüntülenmesi hala birkaç dakika sürer. Portalda görüntülenecek zaman içindeki değişiklikler hala izlenir ve günlüğe kaydedilir.

### <a name="registry-key-change-tracking"></a>Kayıt defteri anahtarı değişiklik izleme

Kayıt defteri anahtarlarındaki değişiklikleri izlemenin amacı, üçüncü taraf kod ve kötü amaçlı yazılımların etkinleştirebileceği genişletilebilirlik noktalarını bulmaktır. Aşağıdaki listede önceden yapılandırılmış kayıt defteri anahtarlarının listesi gösterilmektedir. Bu anahtarlar yapılandırıldı ancak etkin değil. Bu kayıt defteri anahtarlarını izlemek için her birini etkinleştirmeniz gerekir.

> [!div class="mx-tdBreakAll"]
> |Kayıt Defteri Anahtarı | Amaç |
> |---------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Doğrudan Windows Gezgini 'nde bulunan ve genellikle Explorer. exe ile işlem içinde çalıştırılan ortak autostart girdilerini izler.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Başlangıçta çalıştırılan betikleri izler.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Kapatılırken çalıştırılan betikleri izler.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Kullanıcı Windows hesabında oturum açmadan önce yüklenen anahtarları izler. Anahtar, 64 bit bilgisayarlarda çalışan 32 bitlik programlar için kullanılır.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Uygulama ayarlarındaki değişiklikleri izler.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Doğrudan Windows Gezgini 'nde bulunan ve genellikle Explorer. exe ile işlem içinde çalıştırılan ortak autostart girdilerini izler.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Doğrudan Windows Gezgini 'nde bulunan ve genellikle Explorer. exe ile işlem içinde çalıştırılan ortak autostart girdilerini izler.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Simge kaplama işleyicisi kaydını izler.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | 64 bit bilgisayarlarda çalışan 32 bitlik programlar için simge kaplama işleyici kaydını izler.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Internet Explorer için yeni tarayıcı yardımcısı nesne eklentilerini izler. Geçerli sayfanın Belge Nesne Modeli (DOM) erişmek ve gezintiyi denetlemek için kullanılır.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Internet Explorer için yeni tarayıcı yardımcısı nesne eklentilerini izler. Geçerli sayfanın Belge Nesne Modeli (DOM) ve 64 bit bilgisayarlarda çalışan 32 bitlik programların gezintisini denetlemek için kullanılır.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Özel araç menüleri ve özel araç çubuğu düğmeleri gibi yeni Internet Explorer uzantılarını izler.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | 64 bit bilgisayarlarda çalışan 32 bitlik programlar için özel araç menüleri ve özel araç çubuğu düğmeleri gibi yeni Internet Explorer uzantılarına yönelik izleyiciler.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | WaveMapper, wave1 ve Wave2, msacm. ımaadpcm,. msadpcm,. msgsm610 ve vidc ile ilişkili 32 bitlik sürücüleri izler. SISTEMDEKI [Drivers] bölümüne benzer. INı dosyası.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | , 64 bit bilgisayarlarda çalışan 32 bit programlar için WaveMapper, wave1 ve Wave2, msacm. ımaadpcm,. msadpcm,. msgsm610 ve vidc ile ilişkili 32 bitlik sürücüleri izler. SISTEMDEKI [Drivers] bölümüne benzer. INı dosyası.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Bilinen veya yaygın olarak kullanılan sistem dll 'lerinin listesini izler; Bu sistem, sistem dll 'lerinin Truva atı sürümlerini bırakarak kullanıcıların zayıf uygulama dizini izinleriyle yararlanmasını engeller.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Windows işletim sistemi için etkileşimli oturum açma desteği modeli olan Winlogon 'dan olay bildirimleri alabilen paketlerin listesini izler.

## <a name="network-requirements"></a>Ağ gereksinimleri

Aşağıdaki adresler Değişiklik İzleme için özel olarak gereklidir. Bu adreslerle iletişim bağlantı noktası 443 üzerinden yapılır.

|Azure genel  |Azure Kamu  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|
|*.azure-automation.net|*. azure-automation.us|

## <a name="use-change-tracking"></a>Değişiklik İzleme kullan

Çözüm etkinleştirildikten sonra, Otomasyon hesabınızda **yapılandırma yönetimi** altında **değişiklik izleme** ' yi seçerek izlenen bilgisayarlarınızın değişikliklerinin özetini görüntüleyebilirsiniz.

Bilgisayarlarınızdaki değişiklikleri görüntüleyebilir ve sonra her bir olayla ilgili ayrıntılara gidebilirsiniz. Grafiği ve değişiklik türü ve zaman aralıklarına göre ayrıntılı bilgileri sınırlamak için grafiğin en üstünde yer alan bırakma listeleri kullanılabilir. Ayrıca, özel bir zaman aralığı seçmek için grafiğe tıklayıp sürükleyebilirsiniz. **Değişiklik türü** , aşağıdaki değerlerden biri olan **Olaylar**, **Daemon 'ları**, **dosyalar**, **kayıt defteri**, **yazılım**, **Windows Hizmetleri**olacaktır. Kategori, size değişiklik türünü gösterir ve **eklenebilir**, **değiştirilebilir**veya **Kaldırılabilir**.

![Değişiklik İzleme panosunun görüntüsü](./media/change-tracking/change-tracking-dash01.png)

Bir değişikliği veya olayı tıklatmak, bu değişiklik hakkındaki ayrıntılı bilgileri getirir. Örnekte görebileceğiniz gibi, hizmetin başlangıç türü el Ile otomatik olarak değiştirilmiştir.

![değişiklik izleme ayrıntılarının görüntüsü](./media/change-tracking/change-tracking-details.png)

## <a name="search-logs"></a>Günlüklerde ara

Portalda sunulan ayrıntıların yanı sıra, aramalar günlüklere göre de yapılabilir. **Değişiklik izleme** sayfası açıkken **Log Analytics**' a tıklayın. Bu, **Günlükler** sayfasını açar.

### <a name="sample-queries"></a>Örnek sorgular

Aşağıdaki tabloda, bu çözüm tarafından toplanan değişiklik kayıtları için örnek günlük aramaları verilmiştir:

|Sorgu  |Açıklama  |
|---------|---------|
|ConfigurationData<br>&#124;Burada ConfigDataType = = "WindowsServices" ve SvcStartupType = = "Auto"<br>&#124;Burada SvcState = = "durduruldu"<br>&#124;SoftwareName, bilgisayar tarafından arg_max (TimeGenerated, *) özetleme         | Otomatik olarak ayarlanan ancak durdurulma olarak bildirilen Windows Hizmetleri için en son envanter kayıtlarını gösterir<br>Sonuçlar, bu SoftwareName ve bilgisayar için en son kayıtla sınırlıdır      |
|ConfigurationChange<br>&#124;Burada ConfigChangeType = = "Software" ve ChangeCategory = = "removed"<br>&#124;Zaman içinde üretilen DESC göre sırala|Kaldırılan yazılım için değişiklik kayıtlarını gösterir|

## <a name="alert-on-changes"></a>Değişikliklerle ilgili uyarı

Değişiklik İzleme ve envanterin temel bir özelliği, yapılandırma durumu ve karma ortamınızın yapılandırma durumunda yapılan tüm değişiklikler hakkında uyarı verme olanağıdır.

Aşağıdaki örnekte, ekran görüntüsü dosya `C:\windows\system32\drivers\etc\hosts` bir makinede değiştirildiğini gösterir. Bu dosya, ana bilgisayar adlarını IP adreslerine çözümlemek için Windows tarafından kullanılır ve bağlantı sorunları ya da trafiğin kötü amaçlı veya tehlikeli olmayan Web sitelerine yeniden yönlendirilmesi nedeniyle bile, hatta DNS 'den önceliklidir.

![Hosts dosyası değişikliğini gösteren bir grafik](./media/change-tracking/changes.png)

Bu değişikliği daha fazla analiz etmek için **Log Analytics**tıklamasından günlük araması bölümüne gidin. Günlük araması ' nda, Hosts dosyasında, sorgu `ConfigurationChange | where FieldsChanged contains "FileContentChecksum" and FileSystemPath contains "hosts"`içerik değişikliklerini arayın. Bu sorgu, tam yolu "konaklar" sözcüğünü içeren dosyalar için dosya içeriği değişikliğini içeren değişiklikleri arar. Ayrıca, yol bölümünü tam nitelikli biçimde (`FileSystemPath == "c:\windows\system32\drivers\etc\hosts"`gibi) değiştirerek belirli bir dosya için sorun da yapabilirsiniz.

Sorgu istenen sonuçları döndürdüğünden, uyarı oluşturma sayfasını açmak için günlük araması deneyimindeki **Yeni uyarı kuralı** düğmesine tıklayın. Ayrıca, Azure portal **Azure izleyici** aracılığıyla bu deneyime gidebilirsiniz. Uyarı oluşturma deneyiminde sorgumuzu yeniden denetleyin ve uyarı mantığını değiştirin. Bu durumda, ortamdaki tüm makinelerde tek bir değişiklik algılanırsa uyarının tetiklenmesi istersiniz.

![Hosts dosyasında değişiklikleri izlemek için değişiklik sorgusunu gösteren resim](./media/change-tracking/change-query.png)

Koşul mantığı ayarlandıktan sonra, tetiklenen uyarıya yanıt olarak eylemler gerçekleştirmek için eylem grupları atayın. Bu durumda, gönderilecek e-postaları ve oluşturulacak bir ıTSM bileti ayarladım.  Azure Işlevi, Otomasyon Runbook 'u, Web kancası veya mantıksal uygulama tetiklenmesi gibi diğer birçok yararlı eylem de alınabilir.

![Değişiklik üzerinde uyarı vermek için bir eylem grubunu yapılandıran bir görüntü](./media/change-tracking/action-groups.png)

Tüm parametreler ve Logic ayarlandıktan sonra, bu uyarıyı ortama uygulayabiliriz.

### <a name="alert-suggestions"></a>Uyarı önerileri

Hosts dosyasında yapılan değişikliklerle ilgili uyarı, Değişiklik İzleme veya envanter verileri için bir dizi iyi uygulamadır, uyarı için aşağıdaki bölümde yer alan örnek sorgularıyla birlikte tanımlanan durumlar da dahil olmak üzere çok sayıda daha fazla senaryo vardır.

|Sorgu  |Açıklama  |
|---------|---------|
|ConfigurationChange <br>&#124;Burada ConfigChangeType = = "Files" ve Filesystemmpath, "c:\\Windows\\system32\\sürücüler\\" içerir.|Sistem açısından kritik dosyalardaki değişiklikleri izlemek için faydalıdır|
|ConfigurationChange <br>&#124;Burada yer değiştirme alanları "FileContentChecksum" ve Filesystemmpath = = "c:\\Windows\\system32\\sürücüleri\\vb\\Konakları"|Anahtar yapılandırma dosyalarında yapılan değişiklikleri izlemek için faydalıdır|
|ConfigurationChange <br>&#124;Burada ConfigChangeType = = "WindowsServices" ve SvcName "W3SVC" ve SvcState = = "durduruldu" içerir|Sistem açısından kritik hizmetlerde yapılan değişiklikleri izlemek için faydalıdır|
|ConfigurationChange <br>&#124;Burada ConfigChangeType = = "Daemon 'ları" ve SvcName "SSH" ve SvcState! = "Running" içerir|Sistem açısından kritik hizmetlerde yapılan değişiklikleri izlemek için faydalıdır|
|ConfigurationChange <br>&#124;Burada ConfigChangeType = = "Software" ve ChangeCategory = = "Added"|Kilitleme yazılım yapılandırmalarına ihtiyaç duyulan ortamlar için faydalıdır|
|ConfigurationData <br>&#124;Burada SoftwareName "Izleme Aracısı" ve CurrentVersion! = "8.0.11081.0" içerir|Güncel olmayan veya uyumsuz bir yazılım sürümünün yüklü olduğu makineleri görmek için faydalıdır. Son bildirilen yapılandırma durumunu bildirir, değişiklikler değildir.|
|ConfigurationChange <br>&#124;WHERE RegistryKey = = @ "HKEY_LOCAL_MACHINE\\yazılım\\Microsoft\\Windows\\CurrentVersion\\QualityCompat"| Kritik Anti-Virus anahtarlarındaki değişiklikleri izlemek için faydalıdır|
|ConfigurationChange <br>&#124;Burada RegistryKey @ "HKEY_LOCAL_MACHINE\\SYSTEM\\CurrentControlSet\\Services\\SharedAccess\\Parameters\\FirewallPolicy" içerir| Güvenlik Duvarı ayarlarında yapılan değişiklikleri izlemek için yararlıdır|

## <a name="next-steps"></a>Sonraki adımlar

Çözümü kullanma hakkında daha fazla bilgi edinmek için Değişiklik İzleme öğreticisini ziyaret edin:

> [!div class="nextstepaction"]
> [Ortamınızdaki değişikliklerle ilgili sorunları giderme](automation-tutorial-troubleshoot-changes.md)

* Ayrıntılı değişiklik izleme verilerini görüntülemek için [Azure izleyici günlüklerinde günlük aramalarını](../log-analytics/log-analytics-log-searches.md) kullanın.
