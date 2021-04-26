---
title: Azure Dosya Eşitleme Aracısı için sürüm notları | Microsoft Docs
description: Azure Dosya Eşitleme aracısına ait sürüm notlarını okuyun ve bu, kuruluşunuzun dosya paylaşımlarını Azure dosyalarında merkezileştirmenizi sağlar.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 4/7/2021
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 9c00b2d4d30ac417d58f2b69e4ba460789cf6583
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797078"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Azure Dosya Eşitleme aracısı sürüm notları
Azure Dosya Eşitleme aracısı şirket içi dosya sunucularının sağladığı esneklik, performans ve uyumluluk özelliklerinden vazgeçmeden kuruluşunuzun dosya paylaşımlarını Azure Dosyaları'nda toplamanızı sağlar. Windows Server yüklemeleriniz, Azure dosya paylaşımınızın hızlı bir önbelleğine dönüştürülür. Verilere yerel olarak erişmek için Windows Server üzerinde kullanılabilen tüm protokolleri (SMB, NFS ve FTPS gibi) kullanabilirsiniz. Dünya çapında istediğiniz sayıda önbellek oluşturabilirsiniz.

Bu makalede Azure Dosya Eşitleme aracısının desteklenen sürümleri için sürüm notları verilmektedir.

## <a name="supported-versions"></a>Desteklenen sürümler
Aşağıdaki Azure Dosya Eşitleme Aracısı sürümleri desteklenir:

| Ina | Aracı sürüm numarası | Sürüm tarihi | Durum |
|----|----------------------|--------------|------------------|
| V12 Release- [KB4568585](https://support.microsoft.com/topic/b9605f04-b4af-4ad8-86b0-2c490c535cfd)| 12.0.0.0 | 26 Mart 2021 | Desteklenen-Fışıklandırma |
| V 11.3 Release- [KB4539953](https://support.microsoft.com/topic/f68974f6-bfdd-44f4-9659-bf2d8a696c26)| 11.3.0.0 | 7 Nisan 2021 | Desteklenir |
| V 11.2 Release- [KB4539952](https://support.microsoft.com/topic/azure-file-sync-agent-v11-2-release-february-2021-c956eaf0-cd8e-4511-98c0-e5a1f2c84048)| 11.2.0.0 | 2 Şubat 2021 | Desteklenir |
| V 11.1 Release- [KB4539951](https://support.microsoft.com/help/4539951)| 11.1.0.0 | 4 Kasım 2020 | Desteklenir |
| V 10.1 sürüm- [KB4522411](https://support.microsoft.com/help/4522411)| 10.1.0.0 | 5 Haziran 2020 | Desteklenen-aracı sürümü 7 Haziran 2021 tarihinde dolacak |
| 2020 Mayıs güncelleştirme paketi- [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | 19 Mayıs 2020 | Desteklenen-aracı sürümü 7 Haziran 2021 tarihinde dolacak |
| İle v10 arasındaki Release- [KB4522409](https://support.microsoft.com/help/4522409)| 10.0.0.0 | 9 Nisan 2020 | Desteklenen-aracı sürümü 7 Haziran 2021 tarihinde dolacak |

## <a name="unsupported-versions"></a>Desteklenmeyen sürümler
Aşağıdaki Azure Dosya Eşitleme Aracısı sürümlerinin zaman aşımına uğradı ve artık desteklenmiyor:

| Ina | Aracı sürüm numarası | Sürüm tarihi | Durum |
|----|----------------------|--------------|------------------|
| V9 sürümü | 9.0.0.0 - 9.1.0.0 | Yok | Desteklenmez-16 Şubat 2021 tarihinde aracı sürümü zaman aşımına uğradı |
| V8 sürümü | 8.0.0.0 | Yok | Desteklenmiyor-aracı sürümü 12 Ocak 2021 tarihinde zaman aşımına uğradı |
| V7 sürümü | 7.0.0.0 - 7.2.0.0 | Yok | Desteklenmez-1 Eylül 2020 tarihinde aracılı sürümler zaman aşımına uğradı |
| V6 sürümü | 6.0.0.0 - 6.3.0.0 | Yok | Desteklenmez-, 21 Nisan 2020 tarihinde Aracı sürümleri zaman aşımına uğradı |
| V5 sürümü | 5.0.2.0 - 5.2.0.0 | Yok | Desteklenmez-18 Mart 2020 tarihinde Aracı sürümleri zaman aşımına uğradı |
| V4 sürümü | 4.0.1.0 - 4.3.0.0 | Yok | Desteklenmiyor-aracı sürümlerinin tarihi 6 Kasım 2019 ' de |
| V3 sürümü | 3.1.0.0 - 3.4.0.0 | Yok | Desteklenmiyor-Aracı sürümleri 19 Ağustos 2019 tarihinde zaman aşımına uğradı |
| Ön GA aracıları | 1.1.0.0-3.0.13.0 | Yok | Desteklenmez-1 Ekim 2018 ' de Aracı sürümleri zaman aşımına uğradı |

### <a name="azure-file-sync-agent-update-policy"></a>Azure Dosya Eşitleme aracısı güncelleştirme ilkesi
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-12000"></a>Aracı sürümü 12.0.0.0
Aşağıdaki sürüm notları Azure Dosya Eşitleme aracısına ait sürüm 12.0.0.0 yöneliktir (26 Mart 2021 ' de yayımlanmıştır).

### <a name="improvements-and-issues-that-are-fixed"></a>Düzeltilen geliştirmeler ve sorunlar
- Ağ erişim ilkesini ve özel uç nokta bağlantılarını yapılandırmak için yeni Portal deneyimi
    - Artık, depolama eşitleme hizmeti genel uç noktasına erişimi devre dışı bırakmak ve özel uç nokta bağlantılarını onaylama, reddetme ve kaldırma için portalı kullanabilirsiniz. Ağ erişim ilkesini ve özel uç nokta bağlantılarını yapılandırmak için, depolama eşitleme hizmeti portalını açın, ayarlar bölümüne gidin ve ağ ' a tıklayın.
 
- 64Kıb 'den büyük birim kümesi boyutları için bulut katmanlama desteği
    - Bulut katmanlaması artık sunucu 2019 üzerinde 2MiB 'ye kadar olan birim kümesi boyutlarını desteklemektedir. Daha fazla bilgi için bkz. [katmana bir dosya için en küçük dosya boyutu nedir?](https://docs.microsoft.com/azure/storage/files/storage-sync-choose-cloud-tiering-policies#minimum-file-size-for-a-file-to-tier).
 
- Azure Dosya Eşitleme hizmet ve depolama hesabı için bant genişliği ve gecikme süresini ölçme
    - Test-StorageSyncNetworkConnectivity cmdlet 'i artık Azure Dosya Eşitleme hizmeti ve depolama hesabı için gecikme ve bant genişliğini ölçmek üzere kullanılabilir. Cmdlet 'i çalıştırırken Azure Dosya Eşitleme hizmetine ve depolama hesabına yönelik gecikme süresi varsayılan olarak ölçülür.  "-MeasureBandwidth" parametresi kullanıldığında, depolama hesabına yönelik olan bant genişliğini karşıya yükle ve indir olarak ölçülür.
 
        Örneğin, Azure Dosya Eşitleme hizmeti ve depolama hesabı için bant genişliğini ve gecikme süresini ölçmek için aşağıdaki PowerShell komutlarını çalıştırın:
 
        ```powershell
        Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
        Test-StorageSyncNetworkConnectivity -MeasureBandwidth 
        ``` 
 
- Sunucu uç noktası oluşturma işlemi başarısız olduğunda portalda geliştirilmiş hata iletileri
    - Geri bildiriminizi duyduk ve sunucu uç noktası oluşturma işlemi başarısız olduğunda hata iletilerini ve Kılavuzu geliştirdik.
 
- Çeşitli performans ve güvenilirlik iyileştirmeleri
    - Azure dosya paylaşımında değiştirilen dosyaları algılamak için geliştirilmiş değişiklik algılama performansı.
    - Mutabakat eşitleme oturumları için performans iyileştirmeleri. 
    - ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED ve ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED hatalarını azaltmak için eşitleme iyileştirmeleri.
    - Bulut katmanlaması etkinse ve katmanlı dosyalar,/+ parametresiyle Robocopy kullanılarak kopyalanırsa veri bozulmasına neden olan bir hata düzeltildi.
    - Birimde yinelenen verileri kaldırma etkinleştirilmişse, dosyaların sunucu 2019 üzerinde katmana başarısız olmasına neden olabilecek bir hata düzeltildi.
    - Bir dosya 2GiB 'den büyükse AFSDiag 'ın dosyaları sıkıştıramasına neden olabilecek bir hata düzeltildi.

### <a name="evaluation-tool"></a>Değerlendirme aracı
Azure Dosya Eşitleme dağıtılmadan önce, Azure Dosya Eşitleme değerlendirme aracını kullanarak sisteminizle uyumlu olup olmadığını değerlendirmelisiniz. Bu araç, desteklenmeyen karakterler veya desteklenmeyen bir işletim sistemi sürümü gibi dosya sisteminizle ve veri kümesiyle ilgili olası sorunları denetleyen bir Azure PowerShell cmdlet 'i. Yükleme ve kullanım yönergeleri için, planlama kılavuzundaki [değerlendirme aracı](file-sync-planning.md#evaluation-cmdlet) bölümüne bakın. 

### <a name="agent-installation-and-server-configuration"></a>Aracı yükleme ve sunucu yapılandırması
Azure Dosya Eşitleme aracısının Windows Server ile nasıl yükleneceği ve yapılandırılacağı hakkında daha fazla bilgi için bkz. [Azure dosya eşitleme dağıtımı planlama](file-sync-planning.md) ve [Azure dosya eşitleme dağıtma](file-sync-deployment-guide.md).

- Mevcut bir Azure Dosya Eşitleme Aracısı yüklemesine sahip sunucular için yeniden başlatma gerekir.
- Aracı yükleme paketinin yükseltilmiş (yönetici) izinlerle yüklenmesi gerekir.
- Aracı nano sunucu dağıtım seçeneğinde desteklenmez.
- Aracı yalnızca Windows Server 2019, Windows Server 2016 ve Windows Server 2012 R2 'de desteklenir.
- Aracı için en az 2 GiB bellek gerekir. Sunucu dinamik bellek etkinleştirilmiş bir sanal makinede çalışıyorsa, VM en az 2048 MIB ile yapılandırılmalıdır. Daha fazla bilgi için [Önerilen sistem kaynaklarına](file-sync-planning.md#recommended-system-resources) bakın.
- Depolama eşitleme Aracısı (FileSyncSvc) hizmeti, sistem birimi bilgileri (SVı) dizini sıkıştırılmış bir birimde bulunan sunucu uç noktalarını desteklemez. Bu yapılandırma beklenmeyen sonuçlara neden olacak.

### <a name="interoperability"></a>Birlikte çalışabilirlik
- Çevrimdışı özniteliğe dikkat edip ilgili dosyaların içeriğini okumayı atlamadıkları sürece katmanlı dosyalara erişen virüsten koruma, yedekleme ve diğer amaçlı uygulamalar istenmeyen geri çekme durumlarına neden olabilir. Daha fazla bilgi için bkz. [sorun giderme Azure dosya eşitleme](file-sync-troubleshoot.md).
- Dosya sunucusu Kaynak Yöneticisi (FSRM) dosya ekranları dosya ekranı nedeniyle dosyalar engellendiğinde sonsuz eşitleme hatalarıyla neden olabilir.
- Azure Dosya Eşitleme aracısının yüklü olduğu bir sunucuda Sysprep çalıştırmak desteklenmez ve beklenmeyen sonuçlara yol açabilir. Sunucu görüntüsünü dağıttıktan ve Sysprep Mini Kurulumu tamamlandıktan sonra Azure Dosya Eşitleme Aracısı yüklenmelidir.

### <a name="sync-limitations"></a>Eşitleme sınırlamaları
Aşağıdaki öğeler eşitlenmez ancak sistem normal şekilde çalışmaya devam eder:
- Desteklenmeyen karakterleri olan dosyalar. Desteklenmeyen karakterlerin listesi için bkz. [sorun giderme kılavuzu](file-sync-troubleshoot.md#handling-unsupported-characters) .
- Bir noktayla biten dosyalar veya dizinler.
- 2.048 karakterden uzun yollar.
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
- Sistem biriminde bulut katmanlama desteklenmez. Sistem biriminde sunucu uç noktası oluşturmak için, sunucu uç noktasını oluştururken bulutta katmanlamayı devre dışı bırakın.
- Yük Devretme Kümelemesi yalnızca kümelenmiş disklerle desteklenir, Küme Paylaşılan Birimleri (CSV) ile desteklenmez.
- Sunucu uç noktası iç içe olamaz. Aynı birim üzerinde başka bir uç noktaya paralel olarak birlikte bulunabilir.
- Bir işletim sistemi veya uygulama disk belleği dosyasını sunucu uç noktası konumu içinde depolamayın.
- Sunucu yeniden adlandırılırsa portaldaki sunucu adı güncellenmez.

### <a name="cloud-endpoint"></a>Bulut uç noktası
- Azure Dosya Eşitleme, doğrudan Azure dosya paylaşımında değişiklik yapmayı destekler. Ancak, ilk olarak Azure dosya paylaşımında yapılan tüm değişikliklerin bir Azure Dosya Eşitleme değişiklik algılama işi tarafından bulunması gerekir. Her 24 saatte bir bulut uç noktası için değişiklik algılama işi başlatılır. Azure dosya paylaşımında değiştirilen dosyaları hemen eşitlemek için [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet 'i Azure dosya paylaşımındaki değişikliklerin algılanmasını el ile başlatmak için kullanılabilir. Buna ek olarak, REST protokolü üzerinden bir Azure dosya paylaşımında yapılan değişiklikler, SMB son değiştirilme zamanını güncelleştirmeyecektir ve eşitleme tarafından değişiklik olarak görünmez.
- Depolama eşitleme hizmeti ve/veya depolama hesabı farklı bir kaynak grubuna, aboneliğe veya Azure AD kiracısına taşınabilir. Depolama eşitleme hizmeti veya depolama hesabı taşındıktan sonra, Microsoft. Storagessync uygulamasına depolama hesabına erişim sağlamanız gerekir (bkz. [Azure dosya eşitleme depolama hesabına erişimi olduğundan emin olun](file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Bulut uç noktası oluşturulurken, depolama eşitleme hizmeti ve depolama hesabı aynı Azure AD kiracısında olmalıdır. Bulut uç noktası oluşturulduktan sonra, depolama eşitleme hizmeti ve depolama hesabı farklı Azure AD kiracılarına taşınabilir.

### <a name="cloud-tiering"></a>Bulut katmanlaması
- Katmanlanmış bir dosya Robocopy kullanılarak başka bir konuma kopyalanırsa, elde edilen dosya katmanlanmaz. Robocopy bu özniteliği kopyalama işlemlerine yanlışlıkla dahil ettiği için çevrimdışı özniteliği ayarlanmış olabilir.
- Robocopy kullanarak dosyaları kopyalarken dosya zaman damgalarını korumak için/MıR seçeneğini kullanın. Bu, eski dosyaların son erişilen dosyalardan daha önce katmanlı olmasını sağlayacaktır.

## <a name="agent-version-11300"></a>Aracı sürümü 11.3.0.0
Aşağıdaki sürüm notları, 7 Nisan 2021 ' de yayınlanan Azure Dosya Eşitleme aracısının sürüm 11.3.0.0 yöneliktir. Bu notlar, sürüm 11.1.0.0 için listelenen sürüm notlarına ek niteliğindedir.

### <a name="improvements-and-issues-that-are-fixed"></a>Düzeltilen geliştirmeler ve sorunlar 
Bulut katmanlaması etkinse ve katmanlı dosyalar,/+ parametresiyle Robocopy kullanılarak kopyalanırsa veri bozulmasına neden olan bir hata düzeltildi.

## <a name="agent-version-11200"></a>Aracı sürümü 11.2.0.0
Aşağıdaki sürüm notları, 2 Şubat 2021 ' de yayınlanan Azure Dosya Eşitleme aracısının sürüm 11.2.0.0 yöneliktir. Bu notlar, sürüm 11.1.0.0 için listelenen sürüm notlarına ek niteliğindedir.

### <a name="improvements-and-issues-that-are-fixed"></a>Düzeltilen geliştirmeler ve sorunlar 
- Bir eşitleme oturumu çok sayıda öğe başına hata nedeniyle iptal edilirse, Azure Dosya Eşitleme hizmeti, öğe başına hataları düzeltmek için bir özel eşitleme oturumunun gerekli olup olmadığını belirlerse, eşitleme, yeni bir oturum başlatıldığında, bu durum uzlaşmaya devam edebilir.
- Register-AzStorageSyncServer cmdlet 'ini kullanarak bir sunucunun kaydedilmesi "Işlenmemiş özel durum" hatası ile başarısız olabilir.
- Bir sunucudaki izin verilen sunucu uç noktası yollarını yapılandırmak için yeni PowerShell cmdlet 'i (Add-StorageSyncAllowedServerEndpointPath). Bu cmdlet, Azure Dosya Eşitleme dağıtımının bir bulut çözümü sağlayıcısı (CSP) veya hizmet sağlayıcısı tarafından yönetildiği ve müşterinin izin verilen sunucu uç noktası yollarını yapılandırmak istediği senaryolar için yararlıdır. Sunucu uç noktası oluşturulurken, belirtilen yol allowlist içinde değilse, sunucu uç noktası oluşturma işlemi başarısız olur. Bu, isteğe bağlı bir özelliktir ve sunucu uç noktası oluşturulurken desteklenen tüm yollara varsayılan olarak izin verilir.  

    
    - İzin verilen bir sunucu uç noktası yolu eklemek için sunucuda aşağıdaki PowerShell komutlarını çalıştırın:

    ```powershell
    Import-Module 'C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll' -verbose
    Add-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  

    - Desteklenen yolların listesini almak için aşağıdaki PowerShell komutunu çalıştırın:
    
    ```powershell
    Get-StorageSyncAllowedServerEndpointPath
    ```     
    - Bir yolu kaldırmak için aşağıdaki PowerShell komutunu çalıştırın:
    
    ```powershell
    Remove-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  
## <a name="agent-version-11100"></a>Aracı sürümü 11.1.0.0
Aşağıdaki sürüm notları Azure Dosya Eşitleme aracısının sürüm 11.1.0.0 (4 Kasım 2020 ' de yayımlanmıştır) içindir.

### <a name="improvements-and-issues-that-are-fixed"></a>Düzeltilen geliştirmeler ve sorunlar
- İlk indirmeyi ve proaktif geri çekmeyi denetlemek için yeni bulut katmanlama modları
    - İlk indirme modu: artık dosyalarınızın yeni sunucu uç noktanıza nasıl indirilmesini istediğinizi seçebilirsiniz. Son değişiklik zaman damgasıyla sunucunuza indirilen tüm dosyalarınızı veya çok sayıda dosyayı ister misiniz? Bunu yapabilirsiniz! Bulut katmanlaması kullanılamıyor mu? Artık sisteminizdeki katmanlı dosyaları kullanmaktan kaçınabilirsiniz. Daha fazla bilgi edinmek için, dağıtma Azure Dosya Eşitleme belgelerinde [sunucu uç noktası oluşturma](../file-sync/file-sync-deployment-guide.md?tabs=azure-portal%252cproactive-portal#create-a-server-endpoint) bölümüne bakın.
    - Proaktif geri çekme modu: bir dosya oluşturulduğunda veya değiştirildiğinde, bu dosyayı aynı eşitleme grubu içinde belirttiğiniz sunuculara proaktif olarak geri çekmeniz mümkün olur. Bu, dosyayı belirttiğiniz her bir sunucuda tüketim için hazır hale getirir. Dünya genelinde ekipler aynı veri üzerinde çalışıyor mu? Ekip ertesi sabah 'ya ulaştığında, farklı bir saat diliminde bir ekip tarafından güncellenen tüm dosyaların indirilip hazırlanabilmesi için proaktif geri çekmeyi etkinleştirin! Daha fazla bilgi edinmek için, dağıtım Azure Dosya Eşitleme belgelerindeki [yeni ve değiştirilmiş dosyaları Azure dosya paylaşımından önceden çağırma](../file-sync/file-sync-deployment-guide.md?tabs=azure-portal%252cproactive-portal#proactively-recall-new-and-changed-files-from-an-azure-file-share) bölümüne bakın.

- Uygulamaları bulut katmanlama son erişim zamanı izlemekten hariç tutun artık, son erişim zamanı izlemenin uygulamalarını dışlayabilirsiniz. Bir uygulama bir dosyaya eriştiğinde, dosyanın son erişim zamanı bulut katmanlama veritabanında güncelleştirilir. Virüsten koruma gibi dosya sistemini tarayan uygulamalar, tüm dosyaların, dosyalar katmanlı olduğunda bu yana aynı son erişim zamanına sahip olmasına neden olur.

    Son erişim zamanı izlemeden uygulamaları dışlamak için, işlem adını HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync altında bulunan HeatTrackingProcessNameExclusionList kayıt defteri ayarına ekleyin.

    Örnek: REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync"/v HeatTrackingProcessNameExclusionList/t REG_MULTI_SZ/d "SampleApp.exe\0AnotherApp.exe"/f

    > [!Note]  
    > Yinelenen verileri kaldırma ve dosya sunucusu Kaynak Yöneticisi (FSRM) işlemleri varsayılan olarak dışarıda bırakılır (sabit kodlanmış) ve işlem dışlama listesi 5 dakikada bir yenilenir.

- Çeşitli performans ve güvenilirlik iyileştirmeleri
    - Azure dosya paylaşımında değiştirilen dosyaları algılamak için geliştirilmiş değişiklik algılama performansı.
    - İyileştirilmiş eşitleme yüklemesi performansı.
    - İlk karşıya yükleme işlemi, öğe başına hataları ve eşitleme oturumu hatalarını azaltan bir VSS anlık görüntüsünden gerçekleştirilir.
    - Belirli g/ç desenlerine yönelik güvenilirlik geliştirmelerini eşitleyin.
    - Yük devretme gerçekleştiğinde, eşitleme veritabanının yük devretme kümelerinde zaman içinde geri gitmesini önleyen bir hata düzeltildi.
    - Katmanlı bir dosyaya erişirken iyileştirilmiş geri çağırma performansı.

### <a name="evaluation-tool"></a>Değerlendirme aracı
Azure Dosya Eşitleme dağıtılmadan önce, Azure Dosya Eşitleme değerlendirme aracını kullanarak sisteminizle uyumlu olup olmadığını değerlendirmelisiniz. Bu araç, desteklenmeyen karakterler veya desteklenmeyen bir işletim sistemi sürümü gibi dosya sisteminizle ve veri kümesiyle ilgili olası sorunları denetleyen bir Azure PowerShell cmdlet 'i. Yükleme ve kullanım yönergeleri için, planlama kılavuzundaki [değerlendirme aracı](../file-sync/file-sync-planning.md#evaluation-cmdlet) bölümüne bakın. 

### <a name="agent-installation-and-server-configuration"></a>Aracı yükleme ve sunucu yapılandırması
Azure Dosya Eşitleme aracısının Windows Server ile nasıl yükleneceği ve yapılandırılacağı hakkında daha fazla bilgi için bkz. [Azure dosya eşitleme dağıtımı planlama](../file-sync/file-sync-planning.md) ve [Azure dosya eşitleme dağıtma](../file-sync/file-sync-deployment-guide.md).

- Aracı yükleme paketinin yükseltilmiş (yönetici) izinlerle yüklenmesi gerekir.
- Aracı nano sunucu dağıtım seçeneğinde desteklenmez.
- Aracı yalnızca Windows Server 2019, Windows Server 2016 ve Windows Server 2012 R2 'de desteklenir.
- Aracı için en az 2 GiB bellek gerekir. Sunucu dinamik bellek etkinleştirilmiş bir sanal makinede çalışıyorsa, VM en az 2048 MIB ile yapılandırılmalıdır. Daha fazla bilgi için [Önerilen sistem kaynaklarına](../file-sync/file-sync-planning.md#recommended-system-resources) bakın.
- Depolama eşitleme Aracısı (FileSyncSvc) hizmeti, sistem birimi bilgileri (SVı) dizini sıkıştırılmış bir birimde bulunan sunucu uç noktalarını desteklemez. Bu yapılandırma beklenmeyen sonuçlara neden olacak.

### <a name="interoperability"></a>Birlikte çalışabilirlik
- Çevrimdışı özniteliğe dikkat edip ilgili dosyaların içeriğini okumayı atlamadıkları sürece katmanlı dosyalara erişen virüsten koruma, yedekleme ve diğer amaçlı uygulamalar istenmeyen geri çekme durumlarına neden olabilir. Daha fazla bilgi için bkz. [sorun giderme Azure dosya eşitleme](../file-sync/file-sync-troubleshoot.md).
- Dosya sunucusu Kaynak Yöneticisi (FSRM) dosya ekranları dosya ekranı nedeniyle dosyalar engellendiğinde sonsuz eşitleme hatalarıyla neden olabilir.
- Azure Dosya Eşitleme aracısının yüklü olduğu bir sunucuda Sysprep çalıştırmak desteklenmez ve beklenmeyen sonuçlara yol açabilir. Sunucu görüntüsünü dağıttıktan ve Sysprep Mini Kurulumu tamamlandıktan sonra Azure Dosya Eşitleme Aracısı yüklenmelidir.

### <a name="sync-limitations"></a>Eşitleme sınırlamaları
Aşağıdaki öğeler eşitlenmez ancak sistem normal şekilde çalışmaya devam eder:
- Desteklenmeyen karakterleri olan dosyalar. Desteklenmeyen karakterlerin listesi için bkz. [sorun giderme kılavuzu](../file-sync/file-sync-troubleshoot.md#handling-unsupported-characters) .
- Bir noktayla biten dosyalar veya dizinler.
- 2.048 karakterden uzun yollar.
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
- Sistem biriminde bulut katmanlama desteklenmez. Sistem biriminde sunucu uç noktası oluşturmak için, sunucu uç noktasını oluştururken bulutta katmanlamayı devre dışı bırakın.
- Yük Devretme Kümelemesi yalnızca kümelenmiş disklerle desteklenir, Küme Paylaşılan Birimleri (CSV) ile desteklenmez.
- Sunucu uç noktası iç içe olamaz. Aynı birim üzerinde başka bir uç noktaya paralel olarak birlikte bulunabilir.
- Bir işletim sistemi veya uygulama disk belleği dosyasını sunucu uç noktası konumu içinde depolamayın.
- Sunucu yeniden adlandırılırsa portaldaki sunucu adı güncellenmez.

### <a name="cloud-endpoint"></a>Bulut uç noktası
- Azure Dosya Eşitleme, doğrudan Azure dosya paylaşımında değişiklik yapmayı destekler. Ancak, ilk olarak Azure dosya paylaşımında yapılan tüm değişikliklerin bir Azure Dosya Eşitleme değişiklik algılama işi tarafından bulunması gerekir. Her 24 saatte bir bulut uç noktası için değişiklik algılama işi başlatılır. Azure dosya paylaşımında değiştirilen dosyaları hemen eşitlemek için [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet 'i Azure dosya paylaşımındaki değişikliklerin algılanmasını el ile başlatmak için kullanılabilir. Buna ek olarak, REST protokolü üzerinden bir Azure dosya paylaşımında yapılan değişiklikler, SMB son değiştirilme zamanını güncelleştirmeyecektir ve eşitleme tarafından değişiklik olarak görünmez.
- Depolama eşitleme hizmeti ve/veya depolama hesabı farklı bir kaynak grubuna, aboneliğe veya Azure AD kiracısına taşınabilir. Depolama eşitleme hizmeti veya depolama hesabı taşındıktan sonra, Microsoft. Storagessync uygulamasına depolama hesabına erişim sağlamanız gerekir (bkz. [Azure dosya eşitleme depolama hesabına erişimi olduğundan emin olun](../file-sync/file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Bulut uç noktası oluşturulurken, depolama eşitleme hizmeti ve depolama hesabı aynı Azure AD kiracısında olmalıdır. Bulut uç noktası oluşturulduktan sonra, depolama eşitleme hizmeti ve depolama hesabı farklı Azure AD kiracılarına taşınabilir.

### <a name="cloud-tiering"></a>Bulut katmanlaması
- Katmanlanmış bir dosya Robocopy kullanılarak başka bir konuma kopyalanırsa, elde edilen dosya katmanlanmaz. Robocopy bu özniteliği kopyalama işlemlerine yanlışlıkla dahil ettiği için çevrimdışı özniteliği ayarlanmış olabilir.
- Robocopy kullanarak dosyaları kopyalarken dosya zaman damgalarını korumak için/MıR seçeneğini kullanın. Bu, eski dosyaların son erişilen dosyalardan daha önce katmanlı olmasını sağlayacaktır.
    > [!Warning]  
    > Robocopy/B anahtarı Azure Dosya Eşitleme desteklenmez. Kaynak dosya bozulmasına yol açacağından Robocopy/B anahtarını bir Azure Dosya Eşitleme sunucusu uç noktasıyla kullanma.

## <a name="agent-version-10100"></a>Aracı sürümü 10.1.0.0
Aşağıdaki sürüm notları, 5 Haziran 2020 ' de yayınlanan Azure Dosya Eşitleme aracısının sürüm 10.1.0.0 içindir. Bu notlar, 10.0.0.0 ve 10.0.2.0 sürümü için listelenen sürüm notlarına ek niteliğindedir.

### <a name="improvements-and-issues-that-are-fixed"></a>Düzeltilen geliştirmeler ve sorunlar

- Azure özel uç nokta desteği
    - Depolama eşitleme hizmeti ile eşitleme trafiği artık özel bir uç noktaya gönderilebilir. Bu, bir ExpressRoute veya VPN bağlantısı üzerinden tünelleme olanağı sunar. Daha fazla bilgi için bkz. [Azure dosya eşitleme ağ uç noktalarını yapılandırma](../file-sync/file-sync-networking-endpoints.md).
- Eşitlenmiş dosya ölçümü, son olarak değil büyük bir eşitleme çalıştırılırken ilerleme durumunu görüntüleyecektir.
- Aracı yükleme, bulut katmanlama, eşitleme ve telemetri için çeşitli güvenilirlik geliştirmeleri

## <a name="agent-version-10020"></a>Aracı sürümü 10.0.2.0
Aşağıdaki sürüm notları, 19 Mayıs 2020 ' de yayınlanan Azure Dosya Eşitleme aracısının sürüm 10.0.2.0 içindir. Bu notlar, 10.0.0.0 sürümü için listelenen sürüm notlarına ek niteliğindedir.

Bu sürümde düzeltilen sorun:  
- Depolama eşitleme Aracısı (FileSyncSvc), Azure Dosya Eşitleme ile v10 arasındaki aracısını yükledikten sonra sıkça çöker.

> [!Note]  
>Bu sürüm, yeni bir sürüm kullanılabilir olduğunda otomatik olarak güncelleştirilecek şekilde yapılandırılmış sunuculara eklenmedi. Bu güncelleştirmeyi yüklemek için Microsoft Update veya Microsoft Update kataloğunu kullanın (bkz. yükleme yönergeleri için bkz. [KB4522412](https://support.microsoft.com/help/4522412) ).

## <a name="agent-version-10000"></a>Aracı sürümü 10.0.0.0
Aşağıdaki sürüm notları Azure Dosya Eşitleme aracısının 10.0.0.0 sürümü (9 Nisan 2020 ' de yayımlanmıştır) içindir.

### <a name="improvements-and-issues-that-are-fixed"></a>Düzeltilen geliştirmeler ve sorunlar

- Portalda geliştirilmiş eşitleme ilerlemesi
    - Ile v10 arasındaki aracı sürümü ile Azure portal yakında çalışan eşitleme oturumunun türünü göstermeye başlayacaktır. Örneğin ilk indirme, düzenli indirme, arka plan geri çekme (hızlı olağanüstü durum kurtarma durumları) ve benzeri. 

- Geliştirilmiş bulut katmanlama portalı deneyimi
    - Katman veya geri çağırma başarısız olan dosyalarınız varsa, artık sunucu uç noktası özelliklerindeki katmanlama hatalarını görüntüleyebilirsiniz.
    - Sunucu uç noktası için ek bulut katmanlama bilgileri mevcuttur:
        - Yerel önbellek boyutu
        - Önbellek kullanım verimliliği
        - Bulut katmanlama ilkesi ayrıntıları: birim boyutu, geçerli boş alan veya yerel önbellekteki en eski dosyanın en son erişildiği zaman.
    - Bu değişiklikler ilk Ile v10 arasındaki aracı sürümünden kısa bir süre sonra Azure portal gönderilir.

- Depolama eşitleme hizmeti ve/veya depolama hesabını farklı bir Azure Active Directory kiracısına taşıma desteği
    - Azure Dosya Eşitleme artık depolama eşitleme hizmeti ve/veya depolama hesabı farklı bir kaynak grubuna, aboneliğe veya Azure AD kiracısına taşınmayı destekler.
    
- Çeşitli performans ve güvenilirlik iyileştirmeleri
    - Depolama hesabında sanal ağ (VNET) ve güvenlik duvarı kuralları yapılandırılmışsa Azure dosya paylaşımında değişiklik algılama başarısız olabilir.
    - Geri çağırma ile ilişkili azaltılan bellek tüketimi. 
    - [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) cmdlet 'i kullanılırken gelişmiş performans.
    - Diğer çeşitli güvenilirlik geliştirmeleri. 
    
### <a name="evaluation-tool"></a>Değerlendirme aracı
Azure Dosya Eşitleme dağıtılmadan önce, Azure Dosya Eşitleme değerlendirme aracını kullanarak sisteminizle uyumlu olup olmadığını değerlendirmelisiniz. Bu araç, desteklenmeyen karakterler veya desteklenmeyen bir işletim sistemi sürümü gibi dosya sisteminizle ve veri kümesiyle ilgili olası sorunları denetleyen bir Azure PowerShell cmdlet 'i. Yükleme ve kullanım yönergeleri için, planlama kılavuzundaki [değerlendirme aracı](../file-sync/file-sync-planning.md#evaluation-cmdlet) bölümüne bakın. 

### <a name="agent-installation-and-server-configuration"></a>Aracı yükleme ve sunucu yapılandırması
Azure Dosya Eşitleme aracısının Windows Server ile nasıl yükleneceği ve yapılandırılacağı hakkında daha fazla bilgi için bkz. [Azure dosya eşitleme dağıtımı planlama](../file-sync/file-sync-planning.md) ve [Azure dosya eşitleme dağıtma](../file-sync/file-sync-deployment-guide.md).

- Aracı yükleme paketinin yükseltilmiş (yönetici) izinlerle yüklenmesi gerekir.
- Aracı nano sunucu dağıtım seçeneğinde desteklenmez.
- Aracı yalnızca Windows Server 2019, Windows Server 2016 ve Windows Server 2012 R2 'de desteklenir.
- Aracı için en az 2 GiB bellek gerekir. Sunucu dinamik bellek etkinleştirilmiş bir sanal makinede çalışıyorsa, VM en az 2048 MIB ile yapılandırılmalıdır.
- Depolama eşitleme Aracısı (FileSyncSvc) hizmeti, sistem birimi bilgileri (SVı) dizini sıkıştırılmış bir birimde bulunan sunucu uç noktalarını desteklemez. Bu yapılandırma beklenmeyen sonuçlara neden olacak.

### <a name="interoperability"></a>Birlikte çalışabilirlik
- Çevrimdışı özniteliğe dikkat edip ilgili dosyaların içeriğini okumayı atlamadıkları sürece katmanlı dosyalara erişen virüsten koruma, yedekleme ve diğer amaçlı uygulamalar istenmeyen geri çekme durumlarına neden olabilir. Daha fazla bilgi için bkz. [sorun giderme Azure dosya eşitleme](../file-sync/file-sync-troubleshoot.md).
- Dosya sunucusu Kaynak Yöneticisi (FSRM) dosya ekranları dosya ekranı nedeniyle dosyalar engellendiğinde sonsuz eşitleme hatalarıyla neden olabilir.
- Azure Dosya Eşitleme aracısının yüklü olduğu bir sunucuda Sysprep çalıştırmak desteklenmez ve beklenmeyen sonuçlara yol açabilir. Sunucu görüntüsünü dağıttıktan ve Sysprep Mini Kurulumu tamamlandıktan sonra Azure Dosya Eşitleme Aracısı yüklenmelidir.

### <a name="sync-limitations"></a>Eşitleme sınırlamaları
Aşağıdaki öğeler eşitlenmez ancak sistem normal şekilde çalışmaya devam eder:
- Desteklenmeyen karakterleri olan dosyalar. Desteklenmeyen karakterlerin listesi için bkz. [sorun giderme kılavuzu](../file-sync/file-sync-troubleshoot.md#handling-unsupported-characters) .
- Bir noktayla biten dosyalar veya dizinler.
- 2.048 karakterden uzun yollar.
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
- Sistem biriminde bulut katmanlama desteklenmez. Sistem biriminde sunucu uç noktası oluşturmak için, sunucu uç noktasını oluştururken bulutta katmanlamayı devre dışı bırakın.
- Yük Devretme Kümelemesi yalnızca kümelenmiş disklerle desteklenir, Küme Paylaşılan Birimleri (CSV) ile desteklenmez.
- Sunucu uç noktası iç içe olamaz. Aynı birim üzerinde başka bir uç noktaya paralel olarak birlikte bulunabilir.
- Bir işletim sistemi veya uygulama disk belleği dosyasını sunucu uç noktası konumu içinde depolamayın.
- Sunucu yeniden adlandırılırsa portaldaki sunucu adı güncellenmez.

### <a name="cloud-endpoint"></a>Bulut uç noktası
- Azure Dosya Eşitleme, doğrudan Azure dosya paylaşımında değişiklik yapmayı destekler. Ancak, ilk olarak Azure dosya paylaşımında yapılan tüm değişikliklerin bir Azure Dosya Eşitleme değişiklik algılama işi tarafından bulunması gerekir. Her 24 saatte bir bulut uç noktası için değişiklik algılama işi başlatılır. Azure dosya paylaşımında değiştirilen dosyaları hemen eşitlemek için [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet 'i Azure dosya paylaşımındaki değişikliklerin algılanmasını el ile başlatmak için kullanılabilir. Buna ek olarak, REST protokolü üzerinden bir Azure dosya paylaşımında yapılan değişiklikler, SMB son değiştirilme zamanını güncelleştirmeyecektir ve eşitleme tarafından değişiklik olarak görünmez.
- Depolama eşitleme hizmeti ve/veya depolama hesabı farklı bir kaynak grubuna, aboneliğe veya Azure AD kiracısına taşınabilir. Depolama eşitleme hizmeti veya depolama hesabı taşındıktan sonra, Microsoft. Storagessync uygulamasına depolama hesabına erişim sağlamanız gerekir (bkz. [Azure dosya eşitleme depolama hesabına erişimi olduğundan emin olun](../file-sync/file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Bulut uç noktası oluşturulurken, depolama eşitleme hizmeti ve depolama hesabı aynı Azure AD kiracısında olmalıdır. Bulut uç noktası oluşturulduktan sonra, depolama eşitleme hizmeti ve depolama hesabı farklı Azure AD kiracılarına taşınabilir.

### <a name="cloud-tiering"></a>Bulut katmanlaması
- Katmanlanmış bir dosya Robocopy kullanılarak başka bir konuma kopyalanırsa, elde edilen dosya katmanlanmaz. Robocopy bu özniteliği kopyalama işlemlerine yanlışlıkla dahil ettiği için çevrimdışı özniteliği ayarlanmış olabilir.
- Robocopy kullanarak dosyaları kopyalarken dosya zaman damgalarını korumak için/MıR seçeneğini kullanın. Bu, eski dosyaların son erişilen dosyalardan daha önce katmanlı olmasını sağlayacaktır.
