---
title: Azure Dosya Eşitleme Aracısı için sürüm notları | Microsoft Docs
description: Azure Dosya Eşitleme Aracısı için sürüm notları.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 10/8/2019
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 01408e3592af58f05db32e4722e4a85cd2acd0ef
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747786"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Azure Dosya Eşitleme Aracısı için sürüm notları
Azure Dosya Eşitleme aracısı şirket içi dosya sunucularının sağladığı esneklik, performans ve uyumluluk özelliklerinden vazgeçmeden kuruluşunuzun dosya paylaşımlarını Azure Dosyaları'nda toplamanızı sağlar. Windows Server yüklemeleriniz, Azure dosya paylaşımınızın hızlı bir önbelleğine dönüştürülür. Verilere yerel olarak erişmek için Windows Server üzerinde kullanılabilen tüm protokolleri (SMB, NFS ve FTPS gibi) kullanabilirsiniz. Dünya çapında istediğiniz sayıda önbellek oluşturabilirsiniz.

Bu makalede Azure Dosya Eşitleme aracısının desteklenen sürümleri için sürüm notları verilmektedir.

## <a name="supported-versions"></a>Desteklenen sürümler
Azure Dosya Eşitleme aracısı aşağıdaki sürümleri destekler:

| Ina | Aracı sürüm numarası | Sürüm tarihi | Durum |
|----|----------------------|--------------|------------------|
| V8 Release- [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 8 Ekim 2019 | Destekleniyor |
| Temmuz 2019 güncelleştirme paketi- [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 24 Temmuz 2019 | Destekleniyor |
| Temmuz 2019 güncelleştirme paketi- [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 12 Temmuz 2019 | Destekleniyor |
| V7 Release- [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 19 Haziran 2019 | Destekleniyor |
| Haziran 2019 güncelleştirme paketi- [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | 27 Haziran 2019 | Destekleniyor |
| Haziran 2019 güncelleştirme paketi- [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | 13 Haziran 2019 | Destekleniyor |
| 2019 Mayıs güncelleştirme paketi- [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | 7 Mayıs 2019 | Destekleniyor |
| V6 yayını- [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | 21 Nisan 2019 | Destekleniyor |
| Nisan 2019 güncelleştirme paketi- [KB4481061](https://support.microsoft.com/help/4481061)| 5.2.0.0 | 4 Nisan 2019 | Destekleniyor |
| Mart 2019 güncelleştirme paketi- [KB4481060](https://support.microsoft.com/help/4481060)| 5.1.0.0 | 7 Mart 2019 | Destekleniyor |
| V5 sürümü- [KB4459989](https://support.microsoft.com/help/4459989)| 5.0.2.0 | 12 Şubat 2019 | Destekleniyor |
| V4 sürümü | 4.0.1.0 - 4.3.0.0 | Yok | Desteklenmiyor-aracı sürümlerinin tarihi 6 Kasım 2019 ' de |
| V3 sürümü | 3.1.0.0 - 3.4.0.0 | Yok | Desteklenmiyor-Aracı sürümleri 19 Ağustos 2019 tarihinde zaman aşımına uğradı |
| Ön GA aracıları | 1.1.0.0-3.0.13.0 | Yok | Desteklenmez-1 Ekim 2018 ' de Aracı sürümleri zaman aşımına uğradı |

### <a name="azure-file-sync-agent-update-policy"></a>Azure Dosya Eşitleme aracısı güncelleştirme ilkesi
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-8000"></a>Aracı sürümü 8.0.0.0
Aşağıdaki sürüm notları Azure Dosya Eşitleme aracısının sürüm 8.0.0.0 (8 Ekim 2019 ' de yayımlanmıştır) içindir.

### <a name="improvements-and-issues-that-are-fixed"></a>Düzeltilen geliştirmeler ve sorunlar

- Performans geliştirmelerini geri yükleme
    - Azure Backup aracılığıyla gerçekleştirilen kurtarma için daha hızlı kurtarma süreleri. Geri yüklenen dosyalar, Azure Dosya Eşitleme sunuculara daha hızlı eşitlenir. 
- Geliştirilmiş bulut katmanlama portalı deneyimi  
    - Geri çağıramayan katmanlı dosyalarınız varsa, artık sunucu uç noktası özelliklerindeki geri çağırma hatalarını görüntüleyebilirsiniz. Ayrıca, bulut katmanlama filtresi sürücüsü sunucuda yüklü değilse sunucu uç noktası durumu artık bir hata ve risk azaltma adımları gösterir.
- Daha basit aracı yüklemesi
    - Daha basit ve hızlı bir şekilde sunucu kaydetmek için Az\azurerd PowerShell modülünün artık gerekli değildir.
- Çeşitli performans ve güvenilirlik iyileştirmeleri

### <a name="evaluation-tool"></a>Değerlendirme aracı
Azure Dosya Eşitleme dağıtılmadan önce, Azure Dosya Eşitleme değerlendirme aracını kullanarak sisteminizle uyumlu olup olmadığını değerlendirmelisiniz. Bu araç, desteklenmeyen karakterler veya desteklenmeyen bir işletim sistemi sürümü gibi dosya sisteminizle ve veri kümesiyle ilgili olası sorunları denetleyen bir Azure PowerShell cmdlet 'i. Yükleme ve kullanım yönergeleri için, planlama kılavuzundaki [değerlendirme aracı](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) bölümüne bakın. 

### <a name="agent-installation-and-server-configuration"></a>Aracı yükleme ve sunucu yapılandırması
Azure Dosya Eşitleme aracısının Windows Server ile nasıl yükleneceği ve yapılandırılacağı hakkında daha fazla bilgi için bkz. [Azure dosya eşitleme dağıtımı planlama](storage-sync-files-planning.md) ve [Azure dosya eşitleme dağıtma](storage-sync-files-deployment-guide.md).

- Aracı yükleme paketinin yükseltilmiş (yönetici) izinlerle yüklenmesi gerekir.
- Aracı nano sunucu dağıtım seçeneğinde desteklenmez.
- Aracı yalnızca Windows Server 2019, Windows Server 2016 ve Windows Server 2012 R2 'de desteklenir.
- Aracı için en az 2 GiB bellek gerekir. Sunucu dinamik bellek etkinleştirilmiş bir sanal makinede çalışıyorsa, VM en az 2048 MIB ile yapılandırılmalıdır.
- Depolama eşitleme Aracısı (FileSyncSvc) hizmeti, sistem birimi bilgileri (SVı) dizini sıkıştırılmış bir birimde bulunan sunucu uç noktalarını desteklemez. Bu yapılandırma beklenmeyen sonuçlara neden olacak.

### <a name="interoperability"></a>Birlikte çalışabilirlik
- Çevrimdışı özniteliğe dikkat edip ilgili dosyaların içeriğini okumayı atlamadıkları sürece katmanlı dosyalara erişen virüsten koruma, yedekleme ve diğer amaçlı uygulamalar istenmeyen geri çekme durumlarına neden olabilir. Daha fazla bilgi için bkz. [sorun giderme Azure dosya eşitleme](storage-sync-files-troubleshoot.md).
- Dosya sunucusu Kaynak Yöneticisi (FSRM) dosya ekranları dosya ekranı nedeniyle dosyalar engellendiğinde sonsuz eşitleme hatalarıyla neden olabilir.
- Azure Dosya Eşitleme aracısının yüklü olduğu bir sunucuda Sysprep çalıştırmak desteklenmez ve beklenmeyen sonuçlara yol açabilir. Sunucu görüntüsünü dağıttıktan ve Sysprep Mini Kurulumu tamamlandıktan sonra Azure Dosya Eşitleme Aracısı yüklenmelidir.

### <a name="sync-limitations"></a>Eşitleme sınırlamaları
Aşağıdaki öğeler eşitlenmez ancak sistem normal şekilde çalışmaya devam eder:
- Desteklenmeyen karakterleri olan dosyalar. Desteklenmeyen karakterlerin listesi için bkz. [sorun giderme kılavuzu](storage-sync-files-troubleshoot.md#handling-unsupported-characters) .
- Bir noktayla biten dosyalar veya dizinler.
- 2\.048 karakterden uzun yollar.
- 2 KB2den büyük olması durumunda bir güvenlik tanımlayıcısının isteğe bağlı erişim denetim listesi (DACL) kısmı. (Bu sorun yalnızca tek bir öğe üzerinde yaklaşık 40'tan fazla erişim denetimi girişi (ACE) olduğunda geçerlidir.)
- Denetim için kullanılan bir güvenlik tanımlayıcısının sistem erişim denetim listesi (SACL) kısmı.
- Genişletilmiş öznitelikler.
- Alternatif veri akışları.
- Yeniden ayrıştırma noktaları.
- Sabit bağlantılar.
- Bir dosyaya diğer uç noktalardan gelen değişiklikler eşitlendiğinde sıkıştırma ayarları (sunucu dosyasında mevcutsa) korunmaz.
- Hizmetin verileri okumasını engelleyen EFS (veya diğer kullanıcı şifrelemesi modları) şifrelemeli dosyalar.

    > [!Note]  
    > Azure Dosya Eşitleme her zaman aktarımdaki verileri şifreler. Veriler her zaman Azure’da bekleyen durumda şifrelenir.
 
### <a name="server-endpoint"></a>Sunucu uç noktası
- Sunucu uç noktası yalnızca bir NTFS biriminde oluşturulabilir. ReFS, FAT, FAT32 ve diğer dosya sistemleri şu an için Azure Dosya Eşitleme tarafından desteklenmez.
- Sunucu uç noktası silinmeden önce dosyalar geri çağrılmıyorsa katmanlı dosyalar erişilemez duruma gelir. Dosyalara erişimi geri yüklemek için sunucu uç noktasını yeniden oluşturun. Sunucu uç noktası silindikten sonra 30 gün geçmişse veya bulut uç noktası silinmişse, geri çağrılmayan katmanlı dosyalar kullanılamaz olur. Daha fazla bilgi edinmek için bkz. [sunucu uç noktası silindikten sonra katmanlı dosyalara sunucuda erişilemiyor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- Bulut katmanlaması, sistem biriminde desteklenmez. Sistem biriminde bir sunucu uç noktası oluşturmak için sunucu uç noktası oluştururken bulut katmanlamayı devre dışı bırakın.
- Yük Devretme Kümelemesi yalnızca kümelenmiş disklerle desteklenir, Küme Paylaşılan Birimleri (CSV) ile desteklenmez.
- Sunucu uç noktası iç içe olamaz. Aynı birim üzerinde başka bir uç noktaya paralel olarak birlikte bulunabilir.
- Bir işletim sistemi veya uygulama disk belleği dosyasını sunucu uç noktası konumu içinde depolamayın.
- Sunucu yeniden adlandırılırsa portaldaki sunucu adı güncellenmez.

### <a name="cloud-endpoint"></a>Bulut uç noktası
- Azure Dosya Eşitleme, doğrudan Azure dosya paylaşımında değişiklik yapmayı destekler. Ancak, ilk olarak Azure dosya paylaşımında yapılan tüm değişikliklerin bir Azure Dosya Eşitleme değişiklik algılama işi tarafından bulunması gerekir. Her 24 saatte bir bulut uç noktası için değişiklik algılama işi başlatılır. Azure dosya paylaşımında değiştirilen dosyaları hemen eşitlemek için [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet 'i Azure dosya paylaşımındaki değişikliklerin algılanmasını el ile başlatmak için kullanılabilir. Buna ek olarak, REST protokolü üzerinden bir Azure dosya paylaşımında yapılan değişiklikler, SMB son değiştirilme zamanını güncelleştirmeyecektir ve eşitleme tarafından değişiklik olarak görünmez.
- Depolama eşitleme hizmeti ve/veya depolama hesabı, mevcut Azure AD kiracısı içinde farklı bir kaynak grubuna veya aboneliğe taşınabilir. Depolama hesabı taşınmışsa, karma Dosya Eşitleme hizmetine depolama hesabına erişim sağlamanız gerekir (bkz. [Azure dosya eşitleme depolama hesabına erişimi olduğundan emin olun](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure Dosya Eşitleme, aboneliğin farklı bir Azure AD kiracısına taşınmasını desteklemez.

### <a name="cloud-tiering"></a>Bulut katmanlaması
- Katmanlanmış bir dosya Robocopy kullanılarak başka bir konuma kopyalanırsa, elde edilen dosya katmanlanmaz. Robocopy bu özniteliği kopyalama işlemlerine yanlışlıkla dahil ettiği için çevrimdışı özniteliği ayarlanmış olabilir.
- Robocopy kullanarak dosyaları kopyalarken dosya zaman damgalarını korumak için/MıR seçeneğini kullanın. Bu, eski dosyaların son erişilen dosyalardan daha önce katmanlı olmasını sağlayacaktır.

## <a name="agent-version-7200"></a>Aracı sürümü 7.2.0.0
Aşağıdaki sürüm notları, 24 Temmuz 2019 ' de yayınlanan Azure Dosya Eşitleme aracısının sürüm 7.2.0.0 içindir. Bu notlar, sürüm 7.0.0.0 için listelenen sürüm notlarına ek niteliğindedir.

Bu sürümde düzeltilen sorunların listesi:  
- Proxy yapılandırması null ise depolama eşitleme Aracısı (FileSyncSvc) kilitleniyor.
- Sunucuda birden fazla uç nokta aynı ada sahip olduğunda, sunucu uç noktası BCDR (hata 0x80c80257-ECS_E_BCDR_IN_PROGRESS) başlatacak.
- Bulut katmanlama güvenilirliği iyileştirmeleri.

## <a name="agent-version-7100"></a>Aracı sürümü 7.1.0.0
Aşağıdaki sürüm notları, 12 Temmuz 2019 ' de yayınlanan Azure Dosya Eşitleme aracısının sürüm 7.1.0.0 içindir. Bu notlar, sürüm 7.0.0.0 için listelenen sürüm notlarına ek niteliğindedir.

Bu sürümde düzeltilen sorunların listesi:  
- Windows Server 2012 R2 'de SMB üzerinden sunucu uç noktası konumuna erişmek veya bu konuma gözatmak yavaş. 
- Azure Dosya Eşitleme V6 aracısını yükledikten sonra CPU kullanımı arttı.
- Bulut katmanlama telemetrisi geliştirmeleri.
- Bulut katmanlaması ve eşitleme için çeşitli güvenilirlik geliştirmeleri.

## <a name="agent-version-7000"></a>Aracı sürümü 7.0.0.0
Aşağıdaki sürüm notları Azure Dosya Eşitleme aracısına ait sürüm 7.0.0.0 yöneliktir (19 Haziran 2019 ' de yayımlanmıştır).

### <a name="improvements-and-issues-that-are-fixed"></a>Düzeltilen geliştirmeler ve sorunlar

- Daha büyük dosya paylaşma boyutları için destek
    - Daha büyük Azure dosya paylaşımlarının önizlemesi sayesinde dosya eşitleme için destek limitlerimizi de artırıyoruz. Bu ilk adımda Azure Dosya Eşitleme artık tek bir eşitleme ad alanında en fazla 25 TB ve 50.000.000 dosyayı desteklemektedir. Büyük dosya paylaşma önizlemesine uygulamak için bu formu https://aka.ms/azurefilesatscalesurveygirin. 
- Depolama hesaplarında güvenlik duvarı ve sanal ağ ayarı desteği
    - Azure Dosya Eşitleme artık depolama hesaplarında güvenlik duvarını ve sanal ağ ayarını desteklemektedir. Dağıtımınızı güvenlik duvarı ve sanal ağ ayarıyla çalışacak şekilde yapılandırmak için bkz. [güvenlik duvarı ve sanal ağ ayarlarını yapılandırma](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings).
- Azure dosya paylaşımında değiştirilen dosyaları hemen eşitlemeye yönelik PowerShell cmdlet 'i
    - Azure dosya paylaşımında değiştirilen dosyaları hemen eşitlemek için Invoke-AzStorageSyncChangeDetection PowerShell cmdlet 'i Azure dosya paylaşımındaki değişikliklerin algılanmasını el ile başlatmak için kullanılabilir. Bu cmdlet, bazı otomatik işlem türlerinin Azure dosya paylaşımında değişiklik yapmakta olduğu veya değişikliklerin bir yönetici tarafından yapıldığı (dosya ve dizinleri paylaşıma taşıma gibi) olduğu senaryolar için tasarlanmıştır. Son Kullanıcı değişiklikleri için, Azure Dosya Eşitleme aracısını IaaS sanal makinesine yüklemek ve son kullanıcıların IaaS VM 'si aracılığıyla dosya paylaşımında erişimi olması önerilir. Bu şekilde, tüm değişiklikler Invoke-AzStorageSyncChangeDetection cmdlet 'ini kullanmaya gerek kalmadan diğer aracılara hızla eşitlenir. Daha fazla bilgi için bkz. [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) belgeleri.
- Eşitleme olmayan dosyalarla karşılaşırsanız, geliştirilmiş Portal deneyimi
    - Eşitleme başarısız olan dosyalarınız varsa, artık portalda geçici ve kalıcı hatalar arasında ayrım yaptık. Geçici hatalar genellikle yönetici eylemine gerek kalmadan kendilerini çözer. Örneğin, o anda kullanılmakta olan bir dosya, dosya tanıtıcısı kapatılıncaya kadar eşitlenmez. Kalıcı hatalar için, artık her bir hatadan etkilenen dosya sayısını gösteririz. Kalıcı hata sayısı aynı zamanda bir eşitleme grubundaki tüm sunucu uç noktalarının eşitlenmeyen dosyalar sütununda de görüntülenir.
- Azure Backup dosya düzeyinde geri yükleme geliştirildi
    - Azure Backup kullanılarak geri yüklenen tek dosyalar artık algılanır ve sunucu uç noktası ile daha hızlı eşitlenir.
- Geliştirilmiş bulut katmanlama geri çağırma cmdlet güvenilirliği 
    - Invoke-StorageSyncFileRecall cmdlet 'i artık müşterilerin dosya başına yeniden deneme sayısı ve Robocopy için dosya başına yeniden deneme gecikmesi belirlemesine izin veriyor. Daha önce bu cmdlet, belirli bir yoldaki tüm katmanlı dosyaları rastgele sırayla geri çekti. Yeni sipariş parametresi ile, bu cmdlet önce en yoğun verilerini çağırır ve bulut katmanlaması ilkesini (tarih ilkesi karşılanıyorsa veya birim boş alanı karşılanıyorsa geri çekmeyi durdur; hangisi önce gerçekleşirse) kabul eder.
- Yalnızca TLS 1,2 desteği (TLS 1,0 ve 1,1 devre dışı)
    - Azure Dosya Eşitleme artık yalnızca TLS 1,0 ve 1,1 devre dışı olan sunucularda TLS 1,2 kullanımını desteklemektedir. Bu iyileştirmadan önce, sunucuda TLS 1,0 ve 1,1 devre dışı bırakılmışsa sunucu kaydı başarısız olur.
- Eşitleme ve bulut katmanlaması için çeşitli performans ve güvenilirlik iyileştirmeleri
    - Bu sürümde birkaç güvenilirlik ve performans geliştirmesi vardır. Bunların bazıları, bir bant genişliği azaltma zamanlaması ayarlanmış olduğunda, bu durumlarda daha verimli bir şekilde bulut katmanlaması ve Azure Dosya Eşitleme daha verimli hale getirmek için hedeflenmelidir.

### <a name="evaluation-tool"></a>Değerlendirme aracı
Azure Dosya Eşitleme dağıtılmadan önce, Azure Dosya Eşitleme değerlendirme aracını kullanarak sisteminizle uyumlu olup olmadığını değerlendirmelisiniz. Bu araç, desteklenmeyen karakterler veya desteklenmeyen bir işletim sistemi sürümü gibi dosya sisteminizle ve veri kümesiyle ilgili olası sorunları denetleyen bir Azure PowerShell cmdlet 'i. Yükleme ve kullanım yönergeleri için, planlama kılavuzundaki [değerlendirme aracı](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) bölümüne bakın. 

### <a name="agent-installation-and-server-configuration"></a>Aracı yükleme ve sunucu yapılandırması
Azure Dosya Eşitleme aracısının Windows Server ile nasıl yükleneceği ve yapılandırılacağı hakkında daha fazla bilgi için bkz. [Azure dosya eşitleme dağıtımı planlama](storage-sync-files-planning.md) ve [Azure dosya eşitleme dağıtma](storage-sync-files-deployment-guide.md).

- Aracı yükleme paketinin yükseltilmiş (yönetici) izinlerle yüklenmesi gerekir.
- Aracı nano sunucu dağıtım seçeneğinde desteklenmez.
- Aracı yalnızca Windows Server 2019, Windows Server 2016 ve Windows Server 2012 R2 'de desteklenir.
- Aracı için en az 2 GiB bellek gerekir. Sunucu dinamik bellek etkinleştirilmiş bir sanal makinede çalışıyorsa, VM en az 2048 MIB ile yapılandırılmalıdır.
- Depolama eşitleme Aracısı (FileSyncSvc) hizmeti, sistem birimi bilgileri (SVı) dizini sıkıştırılmış bir birimde bulunan sunucu uç noktalarını desteklemez. Bu yapılandırma beklenmeyen sonuçlara neden olacak.

### <a name="interoperability"></a>Birlikte çalışabilirlik
- Çevrimdışı özniteliğe dikkat edip ilgili dosyaların içeriğini okumayı atlamadıkları sürece katmanlı dosyalara erişen virüsten koruma, yedekleme ve diğer amaçlı uygulamalar istenmeyen geri çekme durumlarına neden olabilir. Daha fazla bilgi için bkz. [sorun giderme Azure dosya eşitleme](storage-sync-files-troubleshoot.md).
- Dosya sunucusu Kaynak Yöneticisi (FSRM) dosya ekranları dosya ekranı nedeniyle dosyalar engellendiğinde sonsuz eşitleme hatalarıyla neden olabilir.
- Azure Dosya Eşitleme aracısının yüklü olduğu bir sunucuda Sysprep çalıştırmak desteklenmez ve beklenmeyen sonuçlara yol açabilir. Sunucu görüntüsünü dağıttıktan ve Sysprep Mini Kurulumu tamamlandıktan sonra Azure Dosya Eşitleme Aracısı yüklenmelidir.

### <a name="sync-limitations"></a>Eşitleme sınırlamaları
Aşağıdaki öğeler eşitlenmez ancak sistem normal şekilde çalışmaya devam eder:
- Desteklenmeyen karakterleri olan dosyalar. Desteklenmeyen karakterlerin listesi için bkz. [sorun giderme kılavuzu](storage-sync-files-troubleshoot.md#handling-unsupported-characters) .
- Bir noktayla biten dosyalar veya dizinler.
- 2\.048 karakterden uzun yollar.
- 2 KB2den büyük olması durumunda bir güvenlik tanımlayıcısının isteğe bağlı erişim denetim listesi (DACL) kısmı. (Bu sorun yalnızca tek bir öğe üzerinde yaklaşık 40'tan fazla erişim denetimi girişi (ACE) olduğunda geçerlidir.)
- Denetim için kullanılan bir güvenlik tanımlayıcısının sistem erişim denetim listesi (SACL) kısmı.
- Genişletilmiş öznitelikler.
- Alternatif veri akışları.
- Yeniden ayrıştırma noktaları.
- Sabit bağlantılar.
- Bir dosyaya diğer uç noktalardan gelen değişiklikler eşitlendiğinde sıkıştırma ayarları (sunucu dosyasında mevcutsa) korunmaz.
- Hizmetin verileri okumasını engelleyen EFS (veya diğer kullanıcı şifrelemesi modları) şifrelemeli dosyalar.

    > [!Note]  
    > Azure Dosya Eşitleme her zaman aktarımdaki verileri şifreler. Veriler her zaman Azure’da bekleyen durumda şifrelenir.
 
### <a name="server-endpoint"></a>Sunucu uç noktası
- Sunucu uç noktası yalnızca bir NTFS biriminde oluşturulabilir. ReFS, FAT, FAT32 ve diğer dosya sistemleri şu an için Azure Dosya Eşitleme tarafından desteklenmez.
- Sunucu uç noktası silinmeden önce dosyalar geri çağrılmıyorsa katmanlı dosyalar erişilemez duruma gelir. Dosyalara erişimi geri yüklemek için sunucu uç noktasını yeniden oluşturun. Sunucu uç noktası silindikten sonra 30 gün geçmişse veya bulut uç noktası silinmişse, geri çağrılmayan katmanlı dosyalar kullanılamaz olur.
- Bulut katmanlaması, sistem biriminde desteklenmez. Sistem biriminde bir sunucu uç noktası oluşturmak için sunucu uç noktası oluştururken bulut katmanlamayı devre dışı bırakın.
- Yük Devretme Kümelemesi yalnızca kümelenmiş disklerle desteklenir, Küme Paylaşılan Birimleri (CSV) ile desteklenmez.
- Sunucu uç noktası iç içe olamaz. Aynı birim üzerinde başka bir uç noktaya paralel olarak birlikte bulunabilir.
- Bir işletim sistemi veya uygulama disk belleği dosyasını sunucu uç noktası konumu içinde depolamayın.
- Sunucu yeniden adlandırılırsa portaldaki sunucu adı güncellenmez.

### <a name="cloud-endpoint"></a>Bulut uç noktası
- Azure Dosya Eşitleme, doğrudan Azure dosya paylaşımında değişiklik yapmayı destekler. Ancak, ilk olarak Azure dosya paylaşımında yapılan tüm değişikliklerin bir Azure Dosya Eşitleme değişiklik algılama işi tarafından bulunması gerekir. Her 24 saatte bir bulut uç noktası için değişiklik algılama işi başlatılır. Buna ek olarak, REST protokolü üzerinden bir Azure dosya paylaşımında yapılan değişiklikler, SMB son değiştirilme zamanını güncelleştirmeyecektir ve eşitleme tarafından değişiklik olarak görünmez.
- Depolama eşitleme hizmeti ve/veya depolama hesabı, mevcut Azure AD kiracısı içinde farklı bir kaynak grubuna veya aboneliğe taşınabilir. Depolama hesabı taşınmışsa, karma Dosya Eşitleme hizmetine depolama hesabına erişim sağlamanız gerekir (bkz. [Azure dosya eşitleme depolama hesabına erişimi olduğundan emin olun](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure Dosya Eşitleme, aboneliğin farklı bir Azure AD kiracısına taşınmasını desteklemez.

### <a name="cloud-tiering"></a>Bulut katmanlaması
- Katmanlanmış bir dosya Robocopy kullanılarak başka bir konuma kopyalanırsa, elde edilen dosya katmanlanmaz. Robocopy bu özniteliği kopyalama işlemlerine yanlışlıkla dahil ettiği için çevrimdışı özniteliği ayarlanmış olabilir.
- Robocopy kullanarak dosyaları kopyalarken dosya zaman damgalarını korumak için/MıR seçeneğini kullanın. Bu, eski dosyaların son erişilen dosyalardan daha önce katmanlı olmasını sağlayacaktır.

## <a name="agent-version-6300"></a>Aracı sürümü 6.3.0.0
Aşağıdaki sürüm notları, 27 Haziran 2019 ' de yayınlanan Azure Dosya Eşitleme aracısının sürüm 6.3.0.0 içindir. Bu notlar, sürüm 6.0.0.0 için listelenen sürüm notlarına ek niteliğindedir.

Bu sürümde düzeltilen sorunların listesi:  
- Windows Server 2012 R2 'de SMB üzerinden sunucu uç noktası konumuna erişmek veya bu konuma gözatmak yavaş 
- Azure Dosya Eşitleme V6 aracısını yükledikten sonra CPU kullanımı arttı
- Bulut katmanlama telemetri iyileştirmeleri

## <a name="agent-version-6200"></a>Aracı sürümü 6.2.0.0
Aşağıdaki sürüm notları 13 Haziran 2019 ' de yayınlanan Azure Dosya Eşitleme aracısının sürüm 6.2.0.0 içindir. Bu notlar, sürüm 6.0.0.0 için listelenen sürüm notlarına ek niteliğindedir.

Bu sürümde düzeltilen sorunların listesi:  
- Sunucu uç noktası oluşturduktan sonra, arka plan geri çekme işlemi dosyaları sunucuya indirirken yüksek CPU kullanımı gerçekleşebilir
- Belirteç süre sonu nedeniyle eşitleme ve bulut katmanlama işlemleri hata ECS_E_SERVER_CREDENTIAL_NEEDED başarısız olabilir
- Dosyanın indirileceği URL ayrılmış karakterler içeriyorsa dosyanın geri yüklenmesi başarısız olabilir 

## <a name="agent-version-6100"></a>Aracı sürümü 6.1.0.0
Aşağıdaki sürüm notları, 6 Mayıs 2019 ' de yayınlanan Azure Dosya Eşitleme aracısının sürüm 6.1.0.0 içindir. Bu notlar, sürüm 6.0.0.0 için listelenen sürüm notlarına ek niteliğindedir.

Bu sürümde düzeltilen sorunların listesi:  
- Windows Yönetim Merkezi, Azure Dosya Eşitleme Aracı sürümü 6,0 yüklü olan sunucularda aracı sürümünü ve sunucu uç noktası yapılandırmasını görüntüleyemiyor.

## <a name="agent-version-6000"></a>Aracı sürümü 6.0.0.0
Aşağıdaki sürüm notları Azure Dosya Eşitleme aracısının sürüm 6.0.0.0 (22 Nisan 2019 ' de yayımlanmıştır) içindir.

### <a name="improvements-and-issues-that-are-fixed"></a>Düzeltilen geliştirmeler ve sorunlar

- Aracı otomatik güncelleştirme desteği
  - Geri bildiriminizi duyduk ve Azure Dosya Eşitleme sunucu aracısına bir otomatik güncelleştirme özelliği ekledik. Daha fazla bilgi için bkz. [Aracı güncelleştirme ilkesi Azure dosya eşitleme](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy).
- Azure dosya paylaşımının ACL 'Leri için destek
  - Azure Dosya Eşitleme, her zaman sunucu uç noktaları arasında ACL 'Leri eşitlemeyi destekledi, ancak ACL 'Ler bulut uç noktasıyla (Azure dosya paylaşımıyla) eşitlenmedi. Bu sürüm, ACL 'Leri sunucu ve bulut uç noktaları arasında eşitlemeye yönelik destek ekler.
- Sunucu uç noktası için paralel karşıya yükleme ve indirme eşitleme oturumları 
  - Sunucu uç noktaları artık aynı anda dosyaları karşıya yüklemeyi ve indirmeyi destekler. Bir indirmenin tamamlanmasını beklemek için dosyaların Azure dosya paylaşımında karşıya yüklenebilmesi. 
- Birim ve katmanlama durumunu almak için yeni bulut katmanlama cmdlet 'leri
  - Bulut katmanlaması ve dosya geri çağırma bilgilerini almak için artık iki yeni sunucu yerel PowerShell cmdlet 'leri kullanılabilir. Bunlar, sunucuda bulunan iki olay kanalından günlüğe kaydetme bilgileri alırlar:
    - Get-StorageSyncFileTieringResult, tüm dosyaları ve bu nedenle katmanlanmayan ve neden olan yollarını listeler.
    - Get-StorageSyncFileRecallResult, tüm dosya geri çağırma olaylarını raporlar. Geri çekilen her dosyayı ve onun yolunu, bu geri çekmenin başarısını veya hatasını listeler.
  - Varsayılan olarak, her iki olay kanalı da 1 MB 'a kadar saklayabilir. olay kanalının boyutunu artırarak bildirilen dosya miktarını artırabilirsiniz.
- FIPS modu desteği
  - Azure Dosya Eşitleme artık Azure Dosya Eşitleme aracısının yüklü olduğu sunucularda FIPS modunu etkinleştirmeyi desteklemektedir.
    - Sunucunuzda FIPS modunu etkinleştirmeden önce, sunucunuza Azure Dosya Eşitleme Aracısı ve [PackageManagement modülünü](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) yüklersiniz. Sunucuda FIPS zaten etkinse, [PackageManagement modülünü](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) sunucunuza [el ile indirin](https://docs.microsoft.com/powershell/gallery/how-to/working-with-packages/manual-download) .
- Bulut katmanlaması ve eşitleme için çeşitli güvenilirlik iyileştirmeleri

### <a name="evaluation-tool"></a>Değerlendirme aracı
Azure Dosya Eşitleme dağıtılmadan önce, Azure Dosya Eşitleme değerlendirme aracını kullanarak sisteminizle uyumlu olup olmadığını değerlendirmelisiniz. Bu araç, desteklenmeyen karakterler veya desteklenmeyen bir işletim sistemi sürümü gibi dosya sisteminizle ve veri kümesiyle ilgili olası sorunları denetleyen bir Azure PowerShell cmdlet 'i. Yükleme ve kullanım yönergeleri için, planlama kılavuzundaki [değerlendirme aracı](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) bölümüne bakın. 

### <a name="agent-installation-and-server-configuration"></a>Aracı yükleme ve sunucu yapılandırması
Azure Dosya Eşitleme aracısının Windows Server ile nasıl yükleneceği ve yapılandırılacağı hakkında daha fazla bilgi için bkz. [Azure dosya eşitleme dağıtımı planlama](storage-sync-files-planning.md) ve [Azure dosya eşitleme dağıtma](storage-sync-files-deployment-guide.md).

- Aracı yükleme paketinin yükseltilmiş (yönetici) izinlerle yüklenmesi gerekir.
- Aracı nano sunucu dağıtım seçeneğinde desteklenmez.
- Aracı yalnızca Windows Server 2019, Windows Server 2016 ve Windows Server 2012 R2 'de desteklenir.
- Aracı için en az 2 GiB bellek gerekir. Sunucu dinamik bellek etkinleştirilmiş bir sanal makinede çalışıyorsa, VM en az 2048 MIB ile yapılandırılmalıdır.
- Depolama eşitleme Aracısı (FileSyncSvc) hizmeti, sistem birimi bilgileri (SVı) dizini sıkıştırılmış bir birimde bulunan sunucu uç noktalarını desteklemez. Bu yapılandırma beklenmeyen sonuçlara neden olacak.

### <a name="interoperability"></a>Birlikte çalışabilirlik
- Çevrimdışı özniteliğe dikkat edip ilgili dosyaların içeriğini okumayı atlamadıkları sürece katmanlı dosyalara erişen virüsten koruma, yedekleme ve diğer amaçlı uygulamalar istenmeyen geri çekme durumlarına neden olabilir. Daha fazla bilgi için bkz. [sorun giderme Azure dosya eşitleme](storage-sync-files-troubleshoot.md).
- Dosya sunucusu Kaynak Yöneticisi (FSRM) dosya ekranları dosya ekranı nedeniyle dosyalar engellendiğinde sonsuz eşitleme hatalarıyla neden olabilir.
- Azure Dosya Eşitleme aracısının yüklü olduğu bir sunucuda Sysprep çalıştırmak desteklenmez ve beklenmeyen sonuçlara yol açabilir. Sunucu görüntüsünü dağıttıktan ve Sysprep Mini Kurulumu tamamlandıktan sonra Azure Dosya Eşitleme Aracısı yüklenmelidir.

### <a name="sync-limitations"></a>Eşitleme sınırlamaları
Aşağıdaki öğeler eşitlenmez ancak sistem normal şekilde çalışmaya devam eder:
- Desteklenmeyen karakterleri olan dosyalar. Desteklenmeyen karakterlerin listesi için bkz. [sorun giderme kılavuzu](storage-sync-files-troubleshoot.md#handling-unsupported-characters) .
- Bir noktayla biten dosyalar veya dizinler.
- 2\.048 karakterden uzun yollar.
- 2 KB2den büyük olması durumunda bir güvenlik tanımlayıcısının isteğe bağlı erişim denetim listesi (DACL) kısmı. (Bu sorun yalnızca tek bir öğe üzerinde yaklaşık 40'tan fazla erişim denetimi girişi (ACE) olduğunda geçerlidir.)
- Denetim için kullanılan bir güvenlik tanımlayıcısının sistem erişim denetim listesi (SACL) kısmı.
- Genişletilmiş öznitelikler.
- Alternatif veri akışları.
- Yeniden ayrıştırma noktaları.
- Sabit bağlantılar.
- Bir dosyaya diğer uç noktalardan gelen değişiklikler eşitlendiğinde sıkıştırma ayarları (sunucu dosyasında mevcutsa) korunmaz.
- Hizmetin verileri okumasını engelleyen EFS (veya diğer kullanıcı şifrelemesi modları) şifrelemeli dosyalar.

    > [!Note]  
    > Azure Dosya Eşitleme her zaman aktarımdaki verileri şifreler. Veriler her zaman Azure’da bekleyen durumda şifrelenir.
 
### <a name="server-endpoint"></a>Sunucu uç noktası
- Sunucu uç noktası yalnızca bir NTFS biriminde oluşturulabilir. ReFS, FAT, FAT32 ve diğer dosya sistemleri şu an için Azure Dosya Eşitleme tarafından desteklenmez.
- Sunucu uç noktası silinmeden önce dosyalar geri çağrılmıyorsa katmanlı dosyalar erişilemez duruma gelir. Dosyalara erişimi geri yüklemek için sunucu uç noktasını yeniden oluşturun. Sunucu uç noktası silindikten sonra 30 gün geçmişse veya bulut uç noktası silinmişse, geri çağrılmayan katmanlı dosyalar kullanılamaz olur.
- Bulut katmanlaması, sistem biriminde desteklenmez. Sistem biriminde bir sunucu uç noktası oluşturmak için sunucu uç noktası oluştururken bulut katmanlamayı devre dışı bırakın.
- Yük Devretme Kümelemesi yalnızca kümelenmiş disklerle desteklenir, Küme Paylaşılan Birimleri (CSV) ile desteklenmez.
- Sunucu uç noktası iç içe olamaz. Aynı birim üzerinde başka bir uç noktaya paralel olarak birlikte bulunabilir.
- Bir işletim sistemi veya uygulama disk belleği dosyasını sunucu uç noktası konumu içinde depolamayın.
- Sunucu yeniden adlandırılırsa portaldaki sunucu adı güncellenmez.

### <a name="cloud-endpoint"></a>Bulut uç noktası
- Azure Dosya Eşitleme, doğrudan Azure dosya paylaşımında değişiklik yapmayı destekler. Ancak, ilk olarak Azure dosya paylaşımında yapılan tüm değişikliklerin bir Azure Dosya Eşitleme değişiklik algılama işi tarafından bulunması gerekir. Her 24 saatte bir bulut uç noktası için değişiklik algılama işi başlatılır. Buna ek olarak, REST protokolü üzerinden bir Azure dosya paylaşımında yapılan değişiklikler, SMB son değiştirilme zamanını güncelleştirmeyecektir ve eşitleme tarafından değişiklik olarak görünmez.
- Depolama eşitleme hizmeti ve/veya depolama hesabı, mevcut Azure AD kiracısı içinde farklı bir kaynak grubuna veya aboneliğe taşınabilir. Depolama hesabı taşınmışsa, karma Dosya Eşitleme hizmetine depolama hesabına erişim sağlamanız gerekir (bkz. [Azure dosya eşitleme depolama hesabına erişimi olduğundan emin olun](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure Dosya Eşitleme, aboneliğin farklı bir Azure AD kiracısına taşınmasını desteklemez.

### <a name="cloud-tiering"></a>Bulut katmanlaması
- Katmanlanmış bir dosya Robocopy kullanılarak başka bir konuma kopyalanırsa, elde edilen dosya katmanlanmaz. Robocopy bu özniteliği kopyalama işlemlerine yanlışlıkla dahil ettiği için çevrimdışı özniteliği ayarlanmış olabilir.
- Robocopy kullanarak dosyaları kopyalarken dosya zaman damgalarını korumak için/MıR seçeneğini kullanın. Bu, eski dosyaların son erişilen dosyalardan daha önce katmanlı olmasını sağlayacaktır.
- SMB'nin dosya meta verilerini önbelleğe hatalı bir şekilde alması nedeniyle dosya özellikleri bir SMB istemcisinden görüntülenirken çevrimdışı özniteliği hatalı şekilde ayarlanmış görünebilir.

## <a name="agent-version-5200"></a>Aracı sürümü 5.2.0.0
Aşağıdaki sürüm notları, 4 Nisan 2019 ' de yayınlanan Azure Dosya Eşitleme aracısının sürüm 5.2.0.0 yöneliktir. Bu notlar, sürüm 5.0.2.0 için listelenen sürüm notlarına ek niteliğindedir.

Bu sürümde düzeltilen sorunların listesi:  
- Çevrimdışı veri aktarımı ve veri aktarımı özgeçmişi özellikleri için güvenilirlik iyileştirmeleri
- Telemetri geliştirmelerini eşitleme

## <a name="agent-version-5100"></a>Aracı sürümü 5.1.0.0
Aşağıdaki sürüm notları 7 Mart 2019 ' de yayınlanan Azure Dosya Eşitleme aracısının sürüm 5.1.0.0 içindir. Bu notlar, sürüm 5.0.2.0 için listelenen sürüm notlarına ek niteliğindedir.

Bu sürümde düzeltilen sorunların listesi:  
- Sunucuda değişiklik numaralandırması başarısız olursa dosyalar 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED) hatasıyla eşitlenemeyebilir
- Eşitleme oturumu veya dosya bir hata 0x80072F78 (WININET_E_INVALID_SERVER_RESPONSE) alırsa, eşitleme işlemi şimdi yeniden deneyecek
- Dosyalar 0x80c80203 hatasıyla eşitlenemeyebilir (ECS_E_SYNC_INVALID_STAGED_FILE)
- Dosyaları geri çekerken yüksek bellek kullanımı oluşabilir
- Bulut katmanlama telemetri iyileştirmeleri 

## <a name="agent-version-5020"></a>Aracı sürümü 5.0.2.0
Aşağıdaki sürüm notları Azure Dosya Eşitleme aracısına ait sürüm 5.0.2.0 içindir (12 Şubat 2019 ' de yayımlanmıştır).

### <a name="improvements-and-issues-that-are-fixed"></a>Düzeltilen geliştirmeler ve sorunlar

- Azure Kamu Bulutu desteği
  - Azure Kamu Bulutu için Önizleme desteği ekledik. Bu, beyaz listelenmiş bir abonelik ve Microsoft 'tan özel bir aracı indirmesi gerektirir. Önizlemeye erişim sağlamak için lütfen doğrudan [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com)bizimle bize e-posta gönderin.
- Yinelenen verileri kaldırma desteği
    - Yinelenen verileri kaldırma, Windows Server 2016 ve Windows Server 2019 ' de etkinleştirilen bulut katmanlaması ile tam olarak desteklenmektedir. Bulut katmanlaması etkinleştirilmiş bir birimde yinelenenleri kaldırma özelliğinin etkinleştirilmesi, daha fazla depolama sağlamaya gerek kalmadan şirket içi daha fazla dosya önbelleğe almanızı sağlar.
- Çevrimdışı veri aktarımı desteği (örn. Data Box aracılığıyla)
    - Büyük miktarlardaki verileri dilediğiniz gibi Azure Dosya Eşitleme içine kolayca geçirin. Azure Data Box, AzCopy ve hatta üçüncü taraf geçiş hizmetleri seçebilirsiniz. Verilerinizi Azure 'a almak için büyük miktarlarda bant genişliği kullanmaya gerek yoktur, Data Box olması durumunda buraya posta gönderin! Daha fazla bilgi için bkz. [çevrimdışı veri aktarımı belgeleri](https://aka.ms/AFS/OfflineDataTransfer).
- İyileştirilmiş eşitleme performansı
    - Aynı birimde birden çok sunucu uç noktası olan müşteriler bu sürümden önce yavaş eşitleme performansına neden olmuş olabilir. Azure Dosya Eşitleme, açık tanıtıcıların bulunduğu dosyaları eşitlemek için sunucuda günde bir kez geçici bir VSS anlık görüntüsü oluşturur. Eşitleme artık bir VSS eşitleme oturumu etkinken bir birimde birden çok sunucu uç noktası eşitlemesini destekler. VSS eşitleme oturumunun tamamlanmasını beklemek için eşitleme, birimdeki diğer sunucu uç noktalarında devam edebilir.
- Portalda geliştirilmiş izleme
    - Depolama eşitleme hizmeti portalına görüntülemek için grafikler eklendi:
        - Eşitlenen dosya sayısı
        - Aktarılan verilerin boyutu
        - Eşitlenmediğinden dosya sayısı
        - Geri çekilen verilerin boyutu
        - Sunucu bağlantısı durumu
    - Daha fazla bilgi için bkz. [izleyici Azure dosya eşitleme](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring).
- Geliştirilmiş ölçeklenebilirlik ve güvenilirlik
    - Bir dizindeki en fazla dosya sistemi nesnesi sayısı (dizinler ve dosyalar) 1.000.000 'e artmıştır. Önceki sınır 200.000 idi.
    - Eşitleme, büyük dosyalar için bir aktarım kesintiye uğradığında yeniden aktarım yerine veri aktarımını sürdürmeyi deneyecek 

### <a name="evaluation-tool"></a>Değerlendirme aracı
Azure Dosya Eşitleme dağıtılmadan önce, Azure Dosya Eşitleme değerlendirme aracını kullanarak sisteminizle uyumlu olup olmadığını değerlendirmelisiniz. Bu araç, desteklenmeyen karakterler veya desteklenmeyen bir işletim sistemi sürümü gibi dosya sisteminizle ve veri kümesiyle ilgili olası sorunları denetleyen bir Azure PowerShell cmdlet 'i. Yükleme ve kullanım yönergeleri için, planlama kılavuzundaki [değerlendirme aracı](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) bölümüne bakın. 

### <a name="agent-installation-and-server-configuration"></a>Aracı yükleme ve sunucu yapılandırması
Azure Dosya Eşitleme aracısının Windows Server ile nasıl yükleneceği ve yapılandırılacağı hakkında daha fazla bilgi için bkz. [Azure dosya eşitleme dağıtımı planlama](storage-sync-files-planning.md) ve [Azure dosya eşitleme dağıtma](storage-sync-files-deployment-guide.md).

- Aracı yükleme paketinin yükseltilmiş (yönetici) izinlerle yüklenmesi gerekir.
- Aracı Windows Server Core veya nano sunucu dağıtım seçeneklerinde desteklenmez.
- Aracı yalnızca Windows Server 2019, Windows Server 2016 ve Windows Server 2012 R2 'de desteklenir.
- Aracı için en az 2 GiB bellek gerekir. Sunucu dinamik bellek etkinleştirilmiş bir sanal makinede çalışıyorsa, VM en az 2048 MIB ile yapılandırılmalıdır.
- Depolama eşitleme Aracısı (FileSyncSvc) hizmeti, sistem birimi bilgileri (SVı) dizini sıkıştırılmış bir birimde bulunan sunucu uç noktalarını desteklemez. Bu yapılandırma beklenmeyen sonuçlara neden olacak.
- FIPS modu desteklenmiyor ve devre dışı bırakılmalıdır. 

### <a name="interoperability"></a>Birlikte çalışabilirlik
- Çevrimdışı özniteliğe dikkat edip ilgili dosyaların içeriğini okumayı atlamadıkları sürece katmanlı dosyalara erişen virüsten koruma, yedekleme ve diğer amaçlı uygulamalar istenmeyen geri çekme durumlarına neden olabilir. Daha fazla bilgi için bkz. [sorun giderme Azure dosya eşitleme](storage-sync-files-troubleshoot.md).
- Dosya sunucusu Kaynak Yöneticisi (FSRM) dosya ekranları dosya ekranı nedeniyle dosyalar engellendiğinde sonsuz eşitleme hatalarıyla neden olabilir.
- Azure Dosya Eşitleme aracısının yüklü olduğu bir sunucuda Sysprep çalıştırmak desteklenmez ve beklenmeyen sonuçlara yol açabilir. Sunucu görüntüsünü dağıttıktan ve Sysprep Mini Kurulumu tamamlandıktan sonra Azure Dosya Eşitleme Aracısı yüklenmelidir.

### <a name="sync-limitations"></a>Eşitleme sınırlamaları
Aşağıdaki öğeler eşitlenmez ancak sistem normal şekilde çalışmaya devam eder:
- Desteklenmeyen karakterleri olan dosyalar. Desteklenmeyen karakterlerin listesi için bkz. [sorun giderme kılavuzu](storage-sync-files-troubleshoot.md#handling-unsupported-characters) .
- Bir noktayla biten dosyalar veya dizinler.
- 2\.048 karakterden uzun yollar.
- 2 KB2den büyük olması durumunda bir güvenlik tanımlayıcısının isteğe bağlı erişim denetim listesi (DACL) kısmı. (Bu sorun yalnızca tek bir öğe üzerinde yaklaşık 40'tan fazla erişim denetimi girişi (ACE) olduğunda geçerlidir.)
- Denetim için kullanılan bir güvenlik tanımlayıcısının sistem erişim denetim listesi (SACL) kısmı.
- Genişletilmiş öznitelikler.
- Alternatif veri akışları.
- Yeniden ayrıştırma noktaları.
- Sabit bağlantılar.
- Bir dosyaya diğer uç noktalardan gelen değişiklikler eşitlendiğinde sıkıştırma ayarları (sunucu dosyasında mevcutsa) korunmaz.
- Hizmetin verileri okumasını engelleyen EFS (veya diğer kullanıcı şifrelemesi modları) şifrelemeli dosyalar.

    > [!Note]  
    > Azure Dosya Eşitleme her zaman aktarımdaki verileri şifreler. Veriler her zaman Azure’da bekleyen durumda şifrelenir.
 
### <a name="server-endpoint"></a>Sunucu uç noktası
- Sunucu uç noktası yalnızca bir NTFS biriminde oluşturulabilir. ReFS, FAT, FAT32 ve diğer dosya sistemleri şu an için Azure Dosya Eşitleme tarafından desteklenmez.
- Sunucu uç noktası silinmeden önce dosyalar geri çağrılmıyorsa katmanlı dosyalar erişilemez duruma gelir. Dosyalara erişimi geri yüklemek için sunucu uç noktasını yeniden oluşturun. Sunucu uç noktası silindikten sonra 30 gün geçmişse veya bulut uç noktası silinmişse, geri çağrılmayan katmanlı dosyalar kullanılamaz olur.
- Bulut katmanlaması, sistem biriminde desteklenmez. Sistem biriminde bir sunucu uç noktası oluşturmak için sunucu uç noktası oluştururken bulut katmanlamayı devre dışı bırakın.
- Yük Devretme Kümelemesi yalnızca kümelenmiş disklerle desteklenir, Küme Paylaşılan Birimleri (CSV) ile desteklenmez.
- Sunucu uç noktası iç içe olamaz. Aynı birim üzerinde başka bir uç noktaya paralel olarak birlikte bulunabilir.
- Bir işletim sistemi veya uygulama disk belleği dosyasını sunucu uç noktası konumu içinde depolamayın.
- Sunucu yeniden adlandırılırsa portaldaki sunucu adı güncellenmez.

### <a name="cloud-endpoint"></a>Bulut uç noktası
- Azure Dosya Eşitleme, doğrudan Azure dosya paylaşımında değişiklik yapmayı destekler. Ancak, ilk olarak Azure dosya paylaşımında yapılan tüm değişikliklerin bir Azure Dosya Eşitleme değişiklik algılama işi tarafından bulunması gerekir. Her 24 saatte bir bulut uç noktası için değişiklik algılama işi başlatılır. Buna ek olarak, REST protokolü üzerinden bir Azure dosya paylaşımında yapılan değişiklikler, SMB son değiştirilme zamanını güncelleştirmeyecektir ve eşitleme tarafından değişiklik olarak görünmez.
- Depolama eşitleme hizmeti ve/veya depolama hesabı, mevcut Azure AD kiracısı içinde farklı bir kaynak grubuna veya aboneliğe taşınabilir. Depolama hesabı taşınmışsa, karma Dosya Eşitleme hizmetine depolama hesabına erişim sağlamanız gerekir (bkz. [Azure dosya eşitleme depolama hesabına erişimi olduğundan emin olun](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure Dosya Eşitleme, aboneliğin farklı bir Azure AD kiracısına taşınmasını desteklemez.

### <a name="cloud-tiering"></a>Bulut katmanlaması
- Katmanlanmış bir dosya Robocopy kullanılarak başka bir konuma kopyalanırsa, elde edilen dosya katmanlanmaz. Robocopy bu özniteliği kopyalama işlemlerine yanlışlıkla dahil ettiği için çevrimdışı özniteliği ayarlanmış olabilir.
- Robocopy kullanarak dosyaları kopyalarken dosya zaman damgalarını korumak için/MıR seçeneğini kullanın. Bu, eski dosyaların son erişilen dosyalardan daha önce katmanlı olmasını sağlayacaktır.
- SMB'nin dosya meta verilerini önbelleğe hatalı bir şekilde alması nedeniyle dosya özellikleri bir SMB istemcisinden görüntülenirken çevrimdışı özniteliği hatalı şekilde ayarlanmış görünebilir.
