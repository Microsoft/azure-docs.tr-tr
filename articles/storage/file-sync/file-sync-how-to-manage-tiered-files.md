---
title: Azure Dosya Eşitleme katmanlı dosyaları yönetme | Microsoft Docs
description: Bu ipuçları ve PowerShell Command, katmanlı dosyaları yönetmenize yardımcı olur
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bd740c773998450ef6e8bb95c4df3a1abadaceed
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797126"
---
# <a name="how-to-manage-tiered-files"></a>Katmanlı dosyaları yönetme

Bu makalede katmanlı dosyaların yönetimiyle ilgili soruları olan kullanıcılar için rehberlik sunulmaktadır. Bulut katmanlaması hakkında kavramsal sorular için lütfen bkz. [Azure dosyaları SSS](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json).

## <a name="how-to-check-if-your-files-are-being-tiered"></a>Dosyalarınızın katmanlı olup olmadığını denetleme

Dosyaların Küme başına katmanlı olması gerekip gerekmediğini belirtir, saat bir kez değerlendirilir. Yeni bir sunucu uç noktası oluşturulduğunda iki durumda da gelebilmeniz gerekir:

İlk olarak yeni bir sunucu uç noktası eklediğinizde, bu sunucu konumunda genellikle dosyalar bulunur. Bulut katmanlaması başlamadan önce bunların karşıya yüklenmesi gerekir. Tüm dosyaların ilk karşıya yüklemesi tamamlanana kadar birim boş alan ilkesi çalışmaya başlamacaktır. Ancak, bir dosya karşıya yüklendikten hemen sonra isteğe bağlı tarih ilkesi tek bir dosya temelinde çalışmaya başlar. Bir saatlik Aralık burada da geçerlidir. 

Yeni bir sunucu uç noktası eklediğinizde, bir Azure dosya paylaşımında boş bir sunucu konumunu, içindeki verilerle birlikte bağladıysanız mümkündür. Sunucunuza ilk indirme sırasında ad alanını indirmeyi ve içeriği geri çekmeyi seçerseniz, ad alanı kapatıldıktan sonra dosyalar, birimde boş alan ilkesi ve isteğe bağlı tarih ilkesi sınırlarına ulaşıldığında son değiştirme zaman damgasına göre geri alınacaktır.

Bir dosyanın Azure dosya paylaşımınıza katmanlı olup olmadığını denetlemek için çeşitli yollar vardır:
    
   *  **Dosyadaki dosya özniteliklerini denetleyin.**
     Dosyaya sağ tıklayın, **Ayrıntılar**' a gidin ve ardından **öznitelikler** özelliğine kaydırın. Katmanlı bir dosya aşağıdaki öznitelikler kümesine sahiptir:     
        
        | Öznitelik harfi | Öznitelik | Tanım |
        |:----------------:|-----------|------------|
        | A | Arşiv | Dosyanın yedekleme yazılımı tarafından yedeklenmesi gerektiğini gösterir. Bu öznitelik, dosyanın katmanlı veya tam disk üzerinde depolanmadığına bakılmaksızın her zaman ayarlanır. |
        | P | Seyrek dosya | Dosyanın seyrek bir dosya olduğunu gösterir. Seyrek dosya, NTFS 'nin disk akışındaki dosya genellikle boş olduğunda verimli kullanım için sunduğu özelleşmiş bir dosya türüdür. Azure Dosya Eşitleme, bir dosya tamamen katmanlı veya kısmen geri çekilmiş olduğundan seyrek dosyalar kullanır. Tam katmanlı bir dosyada, dosya akışı bulutta depolanır. Kısmen geri çekilmiş bir dosyada, dosyanın o bölümü zaten disk üzerinde. Bu durum, dosyalar, multimedya oynatıcılar veya ZIP yardımcı programları gibi uygulamalar tarafından kısmen okunarak ortaya çıkabilir. Bir dosya diske tamamen geri çekilir Azure Dosya Eşitleme, dosyayı seyrek bir dosyadan normal bir dosyaya dönüştürür. Bu öznitelik yalnızca Windows Server 2016 ve üzeri sürümlerde ayarlanır.|
        | M | Veri erişimi üzerinde geri çek | Dosyanın verilerinin yerel depolamada tam olarak mevcut olmadığını gösterir. Dosyayı okumak, dosya içeriğinin en az bir kısmının sunucu uç noktasının bağlı olduğu bir Azure dosya paylaşımından alınmasına neden olur. Bu öznitelik yalnızca Windows Server 2019 ' de ayarlanır. |
        | L | Yeniden ayrıştırma noktası | Dosyanın bir yeniden ayrıştırma noktası olduğunu gösterir. Yeniden ayrıştırma noktası, bir dosya sistemi filtresi tarafından kullanılmak üzere özel bir işaretçisidir. Azure Dosya Eşitleme, dosyanın depolandığı bulut konumunu Azure Dosya Eşitleme dosya sistemi filtresine (StorageSync.sys) tanımlamak için yeniden ayrıştırma noktaları kullanır. Bu, sorunsuz erişimi destekler. Kullanıcıların Azure Dosya Eşitleme kullanıldığını veya Azure dosya paylaşımınızda dosyaya nasıl erişim alınacağını bilmeleri gerekmez. Bir dosya tamamen geri çekilir Azure Dosya Eşitleme, yeniden ayrıştırma noktasını dosyadan kaldırır. |
        | O | Çevrimdışı | Dosyanın içeriğinin bir kısmının veya tümünün diskte depolanmadığını belirtir. Bir dosya tamamen geri çekilir Azure Dosya Eşitleme, bu özniteliği kaldırır. |

        ![Ayrıntılar sekmesi seçiliyken bir dosyanın özellikler iletişim kutusu](../files/media/storage-files-faq/azure-file-sync-file-attributes.png)
        
    
        > [!NOTE]
        > Dosya Gezgini 'nin tablo görüntüsüne **öznitelikler** alanını ekleyerek bir klasördeki tüm dosyaların özniteliklerini görebilirsiniz. Bunu yapmak için var olan bir sütuna (örneğin, **Boyut**) sağ tıklayın, **daha fazla**' yı seçin ve ardından açılan listeden **öznitelikler** ' i seçin.
        
        > [!NOTE]
        > Bu özniteliklerin hepsi, kısmen geri çekilmiş dosyalar için de görünür olacaktır.
        
   * **`fsutil`Bir dosyadaki yeniden ayrıştırma noktalarını denetlemek için kullanın.**
       Önceki seçenekte açıklandığı gibi, katmanlı bir dosya her zaman bir yeniden ayrıştırma noktası kümesine sahiptir. Yeniden ayrıştırma noktası, Azure Dosya Eşitleme dosya sistemi filtre sürücüsünün (StorageSync.sys) sunucuda yerel olarak depolanmayan Azure dosya paylaşımlarından içerik almasına izin verir. 

       Bir dosyanın yeniden ayrıştırma noktasına sahip olup olmadığını denetlemek için, yükseltilmiş bir komut Istemi veya PowerShell penceresinde, `fsutil` yardımcı programını çalıştırın:

        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```
       Dosyada bir yeniden ayrıştırma noktası varsa, **yeniden ayrıştırma etiketi değerini görmeyi bekleyebilir: 0x8000001E**. Bu onaltılık değer Azure Dosya Eşitleme sahip olan yeniden ayrıştırma noktası değeridir. Çıktı Ayrıca, Azure dosya paylaşımınızda dosyanızın yolunu temsil eden yeniden ayrıştırma verilerini içerir.
        
        > [!WARNING]  
        > `fsutil reparsepoint`Yardımcı program komutunun aynı zamanda bir yeniden ayrıştırma noktasını silme yeteneği de vardır. Azure Dosya Eşitleme mühendislik ekibi sizi sormadığı müddetçe bu komutu yürütün. Bu komutun çalıştırılması, veri kaybına neden olabilir. 

## <a name="how-to-exclude-applications-from-cloud-tiering-last-access-time-tracking"></a>Uygulamaları bulut katmanlama son erişim zamanı izlemenin dışında bırakma

Azure Dosya Eşitleme Agent sürüm 11,1 ile artık son erişim İzlemelerden uygulamaları hariç bırakabilirsiniz. Bir uygulama bir dosyaya eriştiğinde, dosyanın son erişim zamanı bulut katmanlama veritabanında güncelleştirilir. Virüsten koruma gibi dosya sistemini tarayan uygulamalar, tüm dosyaların aynı son erişim zamanına sahip olmasına neden olur ve bu da dosyalar katmanlandıktan sonra etkiler.

Son erişim zamanı izlemeden uygulamaları dışlamak için, işlem adını HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync altında bulunan HeatTrackingProcessNameExclusionList kayıt defteri ayarına ekleyin.

Örnek: REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync"/v HeatTrackingProcessNameExclusionList/t REG_MULTI_SZ/d "SampleApp.exe\0AnotherApp.exe"/f

> [!NOTE]
> Yinelenen verileri kaldırma ve dosya sunucusu Kaynak Yöneticisi (FSRM) işlemi varsayılan olarak dışlanır. İşlem dışlama listesindeki değişiklikler sistem tarafından 5 dakikada bir kabul edilir.

## <a name="how-to-access-the-heat-store"></a>Isı deposuna erişme

Bulut katmanlaması, hangi dosyaların katmanlı olacağını belirleyen bir dosyanın son erişim süresini ve erişim sıklığını kullanır. Bulut katmanlama filtresi sürücüsü (storagesync.sys) son erişim süresini izler ve bulut katmanlama ısı deposundaki bilgileri günlüğe kaydeder. Bir sunucu yerel PowerShell cmdlet 'i kullanarak ısı mağazasını alabilir ve bir CSV dosyasına kaydedebilirsiniz.

Aynı birimdeki tüm dosyalar için tek bir ısı deposu vardır. Isı deposu çok büyük olabilir. Yalnızca "en fazla" öğe sayısını almanız gerekiyorsa,-limit ve bir sayı kullanın ve ayrıca bir alt yola göre filtrelemeyi (birim kökünü) göz önünde bulundurun.

- PowerShell modülünü içeri aktarın:   `Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'`

- BIRIM boş alanı: birim boş alan ilkesi kullanılarak dosyaların katmanlandıkları sırayı almak Için:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredBySpacePolicy`

- TARIH ILKESI: tarih ilkesi kullanılarak dosyaların katmanlanalınacağı sırayı almak Için:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredByDatePolicy`

- Belirli bir dosya için ısı deposu bilgilerini bulun:   `Get-StorageSyncHeatStoreInformation -FilePath '<PathToSpecificFile>'`

- Tüm dosyaları son erişim zamanına göre azalan sırada görüntüleyin:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName DescendingLastAccessTime`

- Arka plan geri çekme veya PowerShell aracılığıyla isteğe bağlı geri çekme ile katmanlı dosyaların nasıl çağrılacağı sıraya bakın:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName OrderTieredFilesWillBeRecalled`

## <a name="how-to-force-a-file-or-directory-to-be-tiered"></a>Bir dosya veya dizinin katmanlanmasını zorlama

> [!NOTE]
> Katmanlı olacak bir dizin seçtiğinizde, yalnızca şu anda dizindeki dosyalar katmanlı olur. Bu saatten sonra oluşturulan dosyalar otomatik olarak katmanlanmazlar.

Bulut katmanlama özelliği etkinleştirildiğinde, bulut katmanlaması, dosyaları son erişime göre otomatik olarak katmanlarını ve bulut uç noktasında belirtilen birim boş alan yüzdesine ulaşmak için zaman sayısını değiştirir. Bazen bir dosyayı katmana el ile zorlamak isteyebilirsiniz. Bu, uzun bir süre için yeniden kullanmayı düşünmediğiniz büyük bir dosyayı kaydedip daha sonra biriminizdeki boş alanın diğer dosya ve klasörler için kullanmasını istediğiniz durumlarda yararlı olabilir. Aşağıdaki PowerShell komutlarını kullanarak, katmanlamayı zorlayabilirsiniz:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

## <a name="how-to-recall-a-tiered-file-to-disk"></a>Katmanlı bir dosyayı diske geri çağırma

Dosyayı diske geri çekmenin en kolay yolu dosyayı açmak. Azure Dosya Eşitleme dosya sistemi filtresi (StorageSync.sys), sizin bölüminizden herhangi bir iş yapmadan dosyayı Azure dosya paylaşımınızdan sorunsuz bir şekilde indirir. Multimedya veya. zip dosyaları gibi kısmi olarak okunabilecek veya akışa yüklenebilen dosya türleri için, yalnızca bir dosya açmanız dosyanın tamamının indirilmemesini güvence altına almaz.

Bir dosyanın yerel diske tam olarak indirildiğinden emin olmak için, bir dosyanın tam olarak geri çekmesini zorlamak için PowerShell kullanmanız gerekir. Bu seçenek, bir klasördeki tüm dosyalar gibi birden çok dosyayı aynı anda geri çağırmak istiyorsanız da yararlı olabilir. Azure Dosya Eşitleme yüklendiği sunucu düğümünde bir PowerShell oturumu açın ve ardından aşağıdaki PowerShell komutlarını çalıştırın:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
İsteğe bağlı parametreler:
* `-Order CloudTieringPolicy` en son değiştirilen veya erişilen dosyaları ilk olarak çağırır ve geçerli katmanlama ilkesi tarafından izin verilir. 
    * Birim boş alan ilkesi yapılandırılmışsa, birimde boş alan ilkesi ayarına ulaşılana kadar dosyalar geri alınacaktır. Örneğin, birim ücretsiz ilke ayarı %20 ise, birim boş alanı %20 ' ye ulaştıktan sonra geri çekme durdurulur.  
    * Birim boş alanı ve tarih ilkesi yapılandırılmışsa, birimde boş alan veya tarih ilkesi ayarına ulaşılana kadar dosyalar geri alınacaktır. Örneğin, birim ücretsiz ilke ayarı %20 ise ve tarih ilkesi 7 gün ise, birim boş alanı %20 ' ye ulaşırsa veya 7 gün içinde erişilen veya değiştirilen tüm dosyalar yereldir.
* `-ThreadCount` paralel olarak kaç dosya geri çağrılabileceğini belirler.
* `-PerFileRetryCount`Şu anda engellenen bir dosya için bir geri çekmenin ne sıklıkta denenmeyeceğini belirler.
* `-PerFileRetryDelaySeconds`yeniden çağırma denemeleri arasındaki saniye cinsinden süreyi belirler ve bir önceki parametreyle birlikte her zaman kullanılmalıdır.

Örnek:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
``` 

> [!Note]  
>- Sunucuyu barındıran yerel birimde, tüm katmanlı verileri yeniden çağırmak için yeterli boş alan yoksa, `Invoke-StorageSyncFileRecall` cmdlet başarısız olur.  

> [!Note]
> Katmanlı dosyaları geri çağırmak için ağ bant genişliği en az 1 MB/sn olmalıdır. Ağ bant genişliği 1 MB/sn 'den küçükse, dosyalar bir zaman aşımı hatasıyla geri çekemeyebilir.

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Dosyalar hakkında sık sorulan sorular (SSS)](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)