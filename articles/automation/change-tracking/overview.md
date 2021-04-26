---
title: Azure Otomasyonu Değişiklik İzleme ve envantere genel bakış
description: Bu makalede, ortamınızdaki yazılım ve Microsoft hizmet değişikliklerini belirlemenize yardımcı olan Değişiklik İzleme ve envanter özelliği açıklanır.
services: automation
ms.subservice: change-inventory-management
ms.date: 01/22/2021
ms.topic: conceptual
ms.openlocfilehash: ed29def305bfa33a0a947a331775de89275e5f7f
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220875"
---
# <a name="change-tracking-and-inventory-overview"></a>Değişiklik İzleme ve envantere genel bakış

Bu makalede, Azure Otomasyonu 'nda Değişiklik İzleme ve envanter sunulmaktadır. Bu özellik, Azure 'da, şirket içinde ve diğer bulut ortamlarında barındırılan ve dağıtım paketi Yöneticisi tarafından yönetilen yazılımlarla işletimsel ve çevresel sorunları belirlemenize yardımcı olacak değişiklikleri izler. Değişiklik İzleme ve envanter tarafından izlenen öğeler şunları içerir:

- Windows yazılımı
- Linux yazılımı (paket)
- Windows ve Linux dosyaları
- Windows kayıt defteri anahtarları
- Microsoft Hizmetleri
- Linux Daemon 'ları

> [!NOTE]
> Azure Resource Manager özellik değişikliklerini izlemek için bkz. Azure Kaynak Grafiği [değişiklik geçmişi](../../governance/resource-graph/how-to/get-resource-changes.md).

Değişiklik İzleme ve envanter, işletim sistemi ve uygulama dosyalarını ve Windows kayıt defterini inceleyerek [Azure Güvenlik Merkezi dosya bütünlüğü izleme (FIM)](../../security-center/security-center-file-integrity-monitoring.md) kullanımını sağlar. FIM bu varlıkları izlerken Değişiklik İzleme ve envanter yerel olarak izler:

- Yazılım değişiklikleri
- Microsoft Hizmetleri
- Linux Daemon 'ları

Değişiklik İzleme ve envanterde bulunan tüm özelliklerin etkinleştirilmesi ek ücretlere neden olabilir. Devam etmeden önce [Otomasyon Fiyatlandırması](https://azure.microsoft.com/pricing/details/automation/) ve [Azure izleyici fiyatlandırmasını](https://azure.microsoft.com/pricing/details/monitor/)gözden geçirin.

Değişiklik İzleme ve envanter verileri Azure Izleyici günlüklerine iletir ve bu toplanan veriler bir Log Analytics çalışma alanında depolanır. Dosya bütünlüğü Izleme (FIM) özelliği, yalnızca **sunucular Için Azure Defender** etkinleştirildiğinde kullanılabilir. Daha fazla bilgi için bkz. Azure Güvenlik Merkezi [fiyatlandırması](../../security-center/security-center-pricing.md) . FIM, verileri Değişiklik İzleme ve envanterden depolamak için oluşturulan Log Analytics çalışma alanına yükler. Tam kullanımınızın izlenmesini sağlamak için bağlantılı Log Analytics çalışma alanınızı izlemenizi öneririz. Azure Izleyici günlüklerinin veri kullanımını çözümleme hakkında daha fazla bilgi için bkz. [kullanımı ve maliyeti yönetme](../../azure-monitor/logs/manage-cost-storage.md).

Log Analytics çalışma alanına bağlı makineler, yüklü yazılımlar, Microsoft Hizmetleri, Windows kayıt defteri ve dosyalar ve izlenen sunuculardaki Linux Daemon 'ları değişikliklerle ilgili verileri toplamak için [Log Analytics aracısını](../../azure-monitor/agents/log-analytics-agent.md) kullanın. Veriler kullanılabilir olduğunda, aracı bunu işlenmek üzere Azure Izleyici günlüklerine gönderir. Azure Izleyici günlükleri alınan verilere mantığı uygular, kaydeder ve analiz için kullanılabilir hale getirir.

> [!NOTE]
> Değişiklik İzleme ve envanter, Otomasyon hesabınıza bir Log Analytics çalışma alanı bağlamayı gerektirir. Desteklenen bölgelerin kesin listesi için bkz. [Azure çalışma alanı eşlemeleri](../how-to/region-mappings.md). Bölge eşlemeleri, Otomasyon hesabınızdan ayrı bir bölgedeki VM 'Leri yönetme özelliğini etkilemez.

Hizmet sağlayıcı olarak, [Azure açık Thouse](../../lighthouse/overview.md)için birden fazla müşteri kiracısından eklendi olabilirsiniz. Azure Mathouse, her seferinde çok sayıda Azure Active Directory (Azure AD) kiracıda işlem gerçekleştirmenize olanak tanır. bu sayede, Değişiklik İzleme ve envanter gibi yönetim görevleri, sorumlu olduğunuz kiracılar arasında daha verimli hale getirirsiniz. Değişiklik İzleme ve envanter, aynı Kiracıdaki birden çok abonelikteki makineleri veya [Azure tarafından atanan kaynak yönetimini](../../lighthouse/concepts/azure-delegated-resource-management.md)kullanarak kiracılar genelinde yönetebilir.

## <a name="current-limitations"></a>Geçerli sınırlamalar

Değişiklik İzleme ve stok, aşağıdaki sınırlamalara sahip değildir veya bunları desteklemez:

- Windows kayıt defteri izleme için özyineleme
- Ağ dosya sistemleri
- Farklı yükleme yöntemleri
- *Windows 'da depolanan **. exe** dosyaları
- **En büyük dosya boyutu** sütunu ve değerleri geçerli uygulamada kullanılmıyor.
- 30 dakikalık bir toplama döngüsüne 2500 'den fazla dosya toplamaya çalışırsanız, Değişiklik İzleme ve envanter performansı düşebilir.
- Ağ trafiği yüksekse, değişiklik kayıtlarının görüntülenmesi altı saate kadar sürebilir.
- Bir makine veya sunucu kapatılırken bir yapılandırmayı değiştirirseniz, önceki yapılandırmaya ait değişiklikler gönderebilir.
- Windows Server 2016 Core RS3 makinelerinde düzeltme güncelleştirmeleri toplanıyor.
- Linux Daemon 'ları, hiçbir değişiklik gerçekleşmese de değiştirilmiş durumu gösterebilir. Bu sorun, `SvcRunLevels` Azure Izleyici [ConfigurationChange](/azure/azure-monitor/reference/tables/configurationchange) tablosundaki verilerin yazıldığı şekilde ortaya çıkar.

## <a name="supported-operating-systems"></a>Desteklenen işletim sistemleri

Değişiklik İzleme ve envanter, Log Analytics Aracısı gereksinimlerini karşılayan tüm işletim sistemlerinde desteklenir. Log Analytics Aracısı tarafından şu anda desteklenen Windows ve Linux işletim sistemi sürümlerinin bir listesi için [desteklenen işletim sistemleri](../../azure-monitor/agents/agents-overview.md#supported-operating-systems) bölümüne bakın.

TLS 1,2 için istemci gereksinimlerini anlamak üzere bkz. [Azure Otomasyonu Için tls 1,2 zorlaması](../automation-managing-data.md#tls-12-enforcement-for-azure-automation).

### <a name="python-requirement"></a>Python gereksinimi

Değişiklik İzleme ve stok yalnızca Python2 destekler. Makineniz, varsayılan olarak Python 2 ' yi içermeyen bir demi kullanıyorsa, onu yüklemelisiniz. Aşağıdaki örnek komutlar farklı distros üzerinde Python 2 ' ye yüklenir.

- Red hat, CentOS, Oracle: `yum install -y python2`
- Ubuntu, debir: `apt-get install -y python2`
- SUSE `zypper install -y python2`

Python2 yürütülebilir dosyası *Python*'un diğer adı olmalıdır.

## <a name="network-requirements"></a>Ağ gereksinimleri

Değişiklik İzleme ve envanter için gereken bağlantı noktaları, URL 'Ler ve diğer ağ ayrıntıları hakkında ayrıntılı bilgi için [Azure Otomasyonu ağ yapılandırması](../automation-network-configuration.md#update-management-and-change-tracking-and-inventory) 'nı denetleyin.

## <a name="enable-change-tracking-and-inventory"></a>Değişiklik İzlemeyi ve Sayımı Etkinleştirme

Değişiklik İzleme ve stoku aşağıdaki yollarla etkinleştirebilirsiniz:

- [Otomasyon hesabınızdan](enable-from-automation-account.md) bir veya daha fazla Azure ve Azure dışı makine için.

- Azure [Arc etkin sunucularına](../../azure-arc/servers/overview.md)kayıtlı makineler veya sunucular dahil olmak üzere Azure olmayan makineler için el ile. Hibrit makineler için, önce makinenizi [Azure Arc etkin sunucularına](../../azure-arc/servers/overview.md)bağlayarak ve ardından Azure ilkesi kullanarak, [ *Linux* veya *Windows* Azure Arc makineler yerleşik ilkesine dağıtım Log Analytics aracısını](../../governance/policy/samples/built-in-policies.md#monitoring) atamak üzere Windows için Log Analytics aracısını yüklemenizi öneririz. Makineleri VM'ler için Azure İzleyici de izlemeyi planlıyorsanız, bunun yerine [Enable VM'ler için Azure izleyici](../../governance/policy/samples/built-in-initiatives.md#monitoring) girişimi kullanın.

- Tek bir Azure VM için Azure portal [sanal makine sayfasından](enable-from-vm.md) . Bu senaryo, Linux ve Windows VM 'Leri için kullanılabilir.

- [Birden çok Azure VM](enable-from-portal.md) Için Azure Portal sanal makineler sayfasında bunları seçerek.

## <a name="tracking-file-changes"></a>Dosya değişikliklerini izleme

Hem Windows hem de Linux 'ta dosyalardaki değişiklikleri izlemek için, Değişiklik İzleme ve stok dosyaların MD5 karma değerlerini kullanır. Bu özellik, son stoktan bu yana değişikliklerin yapılmış olup olmadığını algılamak için karmaları kullanır.

## <a name="tracking-file-content-changes"></a>Dosya içeriği değişikliklerini izleme

Değişiklik İzleme ve envanter, bir Windows veya Linux dosyasının içeriğini görüntülemenizi sağlar. Bir dosyada yapılan her değişiklik için Değişiklik İzleme ve stok, dosyanın içeriğini bir [Azure depolama hesabına](../../storage/common/storage-account-create.md)depolar. Bir dosyayı izlerken, bir değişiklikten önce veya sonra içeriğini görüntüleyebilirsiniz. Dosya içeriği satır içi veya yan yana görüntülenebilir.

![Dosyadaki değişiklikleri görüntüleme](./media/overview/view-file-changes.png)

## <a name="tracking-of-registry-keys"></a>Kayıt defteri anahtarlarının izlenmesi

Değişiklik İzleme ve envanter, Windows kayıt defteri anahtarlarının değişikliklerinin izlenmesini sağlar. İzleme, üçüncü taraf kod ve kötü amaçlı yazılımların etkinleştirebileceği genişletilebilirlik noktalarını belirlemenize olanak sağlar. Aşağıdaki tabloda önceden yapılandırılmış (ancak etkinleştirilmemiş) kayıt defteri anahtarları listelenmektedir. Bu anahtarları izlemek için her birini etkinleştirmeniz gerekir.

> [!div class="mx-tdBreakAll"]
> |Kayıt Defteri Anahtarı | Amaç |
> | --- | --- |
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Startup` | Başlangıçta çalıştırılan betikleri izler.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Group Policy\Scripts\Shutdown` | Kapatılırken çalıştırılan betikleri izler.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Run` | Kullanıcı Windows hesabında oturum açmadan önce yüklenen anahtarları izler. Anahtar, 64 bit bilgisayarlarda çalışan 32 bitlik uygulamalar için kullanılır.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Active Setup\Installed Components` | Uygulama ayarlarındaki değişiklikleri izler.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\ShellEx\ContextMenuHandlers` | Doğrudan Windows Gezgini 'nde bulunan ve genellikle **explorer.exe** ile işlem içinde çalıştırılan bağlam menüsü işleyicilerini izler.
> |`HKEY\LOCAL\MACHINE\Software\Classes\Directory\Shellex\CopyHookHandlers` | Doğrudan Windows Gezgini 'ne bağlanan ve genellikle **explorer.exe** ile işlem içinde çalışan kopya kanca işleyicilerini izler.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | Simge kaplama işleyicisi kaydını izler.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers` | 64 bit bilgisayarlarda çalışan 32 bitlik uygulamalar için simge kaplama işleyici kaydını izler.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Internet Explorer için yeni tarayıcı yardımcısı nesne eklentilerini izler. Geçerli sayfanın Belge Nesne Modeli (DOM) erişmek ve gezintiyi denetlemek için kullanılır.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects` | Internet Explorer için yeni tarayıcı yardımcısı nesne eklentilerini izler. Geçerli sayfanın Belge Nesne Modeli (DOM) öğesine erişmek ve 64 bit bilgisayarlarda çalışan 32 bitlik uygulamalar için gezintiyi denetlemek için kullanılır.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Internet Explorer\Extensions` | Özel araç menüleri ve özel araç çubuğu düğmeleri gibi yeni Internet Explorer uzantılarını izler.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Internet Explorer\Extensions` | 64 bit bilgisayarlarda çalışan 32 bitlik uygulamalar için özel araç menüleri ve özel araç çubuğu düğmeleri gibi yeni Internet Explorer uzantılarına yönelik izleyiciler.
> |`HKEY\LOCAL\MACHINE\Software\Microsoft\Windows NT\CurrentVersion\Drivers32` | WaveMapper, wave1 ve Wave2, msacm. ımaadpcm,. msadpcm,. msgsm610 ve vidc ile ilişkili 32 bitlik sürücüleri izler. **system.ini** dosyasındaki [Drivers] bölümüne benzer.
> |`HKEY\LOCAL\MACHINE\Software\Wow6432Node\Microsoft\Windows NT\CurrentVersion\Drivers32` | , 64 bit bilgisayarlarda çalışan 32 bit uygulamalar için WaveMapper, wave1 ve Wave2, msacm. ımaadpcm,. msadpcm,. msgsm610 ve vidc ile ilişkili 32 bitlik sürücüleri izler. **system.ini** dosyasındaki [Drivers] bölümüne benzer.
> |`HKEY\LOCAL\MACHINE\System\CurrentControlSet\Control\Session Manager\KnownDlls` | Bilinen veya yaygın olarak kullanılan sistem dll 'lerinin listesini izler. İzleme, kullanıcıların, sistem dll 'lerinin Truva atı sürümlerini bırakarak zayıf uygulama dizini izinleriyle yararlanmasını önler.
> |`HKEY\LOCAL\MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\Notify` | Windows için etkileşimli oturum açma desteği modeli olan **winlogon.exe** olay bildirimleri alabilen paketlerin listesini izler.

## <a name="recursion-support"></a>Özyineleme desteği

Değişiklik İzleme ve envanter özyineleme destekler, bu da dizinler genelinde izlemeyi basitleştirmek için joker karakterler belirtmenize olanak tanır. Özyineleme Ayrıca, birden çok veya dinamik sürücü adına sahip ortamlarda dosyaları izlemenize olanak tanımak için ortam değişkenleri sağlar. Aşağıdaki listede, özyineleme yapılandırılırken bilmeniz gereken yaygın bilgiler yer almaktadır:

- Birden çok dosyayı izlemek için joker karakterler gereklidir.

- Joker karakterleri yalnızca bir dosya yolunun son kesiminde (örneğin, **arayacak \\ dosyası** _ veya _ */etc/*. conf * *) kullanabilirsiniz.

- Bir ortam değişkeni geçersiz bir yol içeriyorsa, doğrulama başarılı olur ancak yürütme sırasında yol başarısız olur.

- Yolu ayarlarken genel yol adlarından kaçının. bu tür bir ayar, çok fazla klasör alınmasına neden olabilir.

## <a name="change-tracking-and-inventory-data-collection"></a>Değişiklik İzleme ve envanter verileri toplama

Sonraki tabloda, Değişiklik İzleme ve envanter tarafından desteklenen değişiklik türleri için veri toplama sıklığı gösterilmektedir. Her tür için, geçerli durumun veri anlık görüntüsü en az 24 saatte bir yenilenir.

| **Türü Değiştir** | **Sıklık** |
| --- | --- |
| Windows kayıt defteri | 50 dakika |
| Windows dosyası | 30 dakika |
| Linux dosyası | 15 dakika |
| Microsoft Hizmetleri | 10 saniye ila 30 dakika</br> Varsayılan: 30 dakika |
| Linux Daemon 'ları | 5 dakika |
| Windows yazılımı | 30 dakika |
| Linux yazılımı | 5 dakika |

Aşağıdaki tabloda, Değişiklik İzleme ve envanter için makine başına izlenen öğe sınırları gösterilmektedir.

| **Kaynak** | **Sınırlı** |
|---|---|---|
|Dosya|500|
|Kayıt Defteri|250|
|Windows yazılımı (düzeltmeler dahil değil) |250|
|Linux paketleri|1250|
|Hizmetler|250|
|Daemon 'ları|250|

Değişiklik İzleme ve envanter kullanan bir makineye yönelik ortalama Log Analytics veri kullanımı ortamınıza bağlı olarak ayda yaklaşık 40 MB 'dir. Log Analytics çalışma alanının kullanım ve tahmini maliyetler özelliği sayesinde, kullanım grafiğinde Değişiklik İzleme ve envanterle alınan verileri görüntüleyebilirsiniz. Veri kullanımınızı değerlendirmek ve faturanızı nasıl etkileyeceğini öğrenmek için bu veri görünümünü kullanın. Bkz. [kullanımınızı anlayın ve maliyetleri tahmin edin](../../azure-monitor/logs/manage-cost-storage.md#understand-your-usage-and-estimate-costs).

### <a name="microsoft-service-data"></a>Microsoft hizmet verileri

Microsoft Hizmetleri için varsayılan toplama sıklığı 30 dakikadır. **Ayarları Düzenle**' nin altındaki **Microsoft Hizmetleri** sekmesinde bir kaydırıcıyı kullanarak sıklığı yapılandırabilirsiniz.

![Microsoft Hizmetleri kaydırıcısı](./media/overview/windowservices.png)

Log Analytics Aracısı, performansı iyileştirmek için yalnızca değişiklikleri izler. Hizmetin özgün durumuna geri dönerse yüksek eşikten oluşan değişiklikler, değişiklikleri kaçırmayabilir. Sıklığı daha küçük bir değere ayarlamak, aksi takdirde kaçırılmış olabilecek değişiklikleri yakalamadığınızı sağlar.

> [!NOTE]
> Aracı değişiklikleri 10 saniyelik bir aralığa göre izlebilirken, verilerin Azure portal görüntülenmesi birkaç dakika sürer. Portalda görüntülenmesi sırasında oluşan değişiklikler hala izlenir ve günlüğe kaydedilir.

## <a name="support-for-alerts-on-configuration-state"></a>Yapılandırma durumunda uyarılar için destek

Değişiklik İzleme ve envanterin önemli bir özelliği, karma ortamınızın yapılandırma durumundaki değişiklikler hakkında uyarı verebilir. Uyarılara yanıt olarak tetiklenecek birçok yararlı eylem vardır. Örneğin, Azure işlevleri, Otomasyon Runbook 'ları, Web kancaları ve benzeri eylemler. Bir makine için **c:\Windows\system32\drivers\etc\hosts** dosyasında yapılan değişikliklerle ilgili uyarı verme, değişiklik izleme ve envanter verileri için tek iyi bir uyarı uygulamasıdır. Sonraki tabloda tanımlanan sorgu senaryoları da dahil olmak üzere, uyarı için çok daha fazla senaryo vardır.

|Sorgu  |Description  |
|---------|---------|
|ConfigurationChange <br>ConfigChangeType = = "Files" ve Filesystemmpath "c: \\ Windows \\ system32 \\ drivers \\ " içerdiğinde &#124;|Sistem açısından kritik dosyalarda yapılan değişiklikleri izlemek için faydalıdır.|
|ConfigurationChange <br>Alanlardaki "FileContentChecksum" ve Filesystemmpath = = "c: \\ Windows \\ system32 \\ Drivers ve \\ \\ Konakları" içeren &#124;|Anahtar yapılandırma dosyalarında yapılan değişiklikleri izlemek için faydalıdır.|
|ConfigurationChange <br>ConfigChangeType = = "WindowsServices" ve SvcName "W3SVC" ve SvcState = = "durduruldu" içerdiğinde &#124;|Sistem açısından kritik hizmetlerde yapılan değişiklikleri izlemek için faydalıdır.|
|ConfigurationChange <br>&#124; ConfigChangeType = = "Daemon 'ları" ve SvcName "SSH" ve SvcState! = "Running" içerir|Sistem açısından kritik hizmetlerde yapılan değişiklikleri izlemek için faydalıdır.|
|ConfigurationChange <br>&#124; ConfigChangeType = = "Software" ve ChangeCategory = = "Added"|Kilitli yazılım yapılandırmalarına ihtiyaç duyulan ortamlar için faydalıdır.|
|ConfigurationData <br>SoftwareName, "Izleme Aracısı" ve CurrentVersion! = "8.0.11081.0" içerdiğinde &#124;|Güncel olmayan veya uyumsuz yazılım sürümünün yüklü olduğu makineleri görmek için faydalıdır. Bu sorgu, son bildirilen yapılandırma durumunu bildirir, ancak değişiklikleri rapor etmez.|
|ConfigurationChange <br>&#124; RegistryKey = = @ "HKEY_LOCAL_MACHINE \\ Software \\ Microsoft \\ Windows \\ CurrentVersion \\ QualityCompat"| Önemli virüsten koruma anahtarlarındaki değişiklikleri izlemek için faydalıdır.|
|ConfigurationChange <br>RegistryKey 'ın @ "HKEY_LOCAL_MACHINE \\ System \\ CurrentControlSet \\ Services \\ SharedAccess \\ parametreleri \\ firewallpolicy" içerdiğini &#124;| Güvenlik Duvarı ayarlarında yapılan değişiklikleri izlemek için faydalıdır.|

## <a name="next-steps"></a>Sonraki adımlar

- Otomasyon hesabından etkinleştirmek için bkz. [Otomasyon hesabından değişiklik izleme ve envanteri etkinleştirme](enable-from-automation-account.md).

- Azure portal etkinleştirmek için, bkz. [Azure portal değişiklik izleme ve envanterini etkinleştirme](enable-from-portal.md).

- Bir runbook 'tan etkinleştirmek için, bkz. [runbook 'tan değişiklik izleme ve envanteri etkinleştirme](enable-from-runbook.md).

- Azure VM 'den etkinleştirmek için bkz. [Azure VM 'den değişiklik izleme ve envanteri etkinleştirme](enable-from-vm.md).
