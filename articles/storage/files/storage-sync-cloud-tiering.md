---
title: Azure Dosya Eşitleme bulut katmanlamayı anlama | Microsoft Docs
description: Azure Dosya Eşitleme Özellik bulutu katmanlama hakkında bilgi edinin
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: efaa1ef4c5b82da9b905f75483daf9eb3536b15a
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219337"
---
# <a name="cloud-tiering-overview"></a>Bulut katmanlaması genel bakış
Bulut katmanlaması, sık erişilen dosyaların sunucu üzerinde yerel olarak önbelleğe alındığı, diğer tüm dosyaların ilke ayarlarına bağlı olarak Azure dosyaları ile katmanlandıkları Azure Dosya Eşitleme isteğe bağlı bir özelliğidir. Bir dosya katmanlı olduğunda, Azure Dosya Eşitleme dosya sistemi filtresi (Storagessync. sys) dosyayı bir işaretçi veya yeniden ayrıştırma noktasıyla yerel olarak değiştirir. Yeniden ayrıştırma noktası, Azure dosyalarındaki dosyanın bir URL 'sini temsil eder. Katmanlı bir dosyanın hem "çevrimdışı" özniteliği hem de FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS özniteliği, üçüncü taraf uygulamaların katmanlı dosyaları güvenli bir şekilde tanımlayabilmesi için NTFS 'de ayarlanır.
 
Kullanıcı katmanlı bir dosya açtığında, kullanıcının dosyanın gerçekten Azure 'da depolandığını bilmesi gerekmeden dosya verilerini Azure dosyalarından sorunsuz bir şekilde geri çeker Azure Dosya Eşitleme. 
 
 > [!Important]  
 > Bulut katmanlaması, Windows sistem birimlerindeki sunucu uç noktaları için desteklenmez ve yalnızca boyutu 64 KiB 'den büyük dosyalar Azure dosyaları için katmanlanmış olabilir.
    
Azure Dosya Eşitleme, bu küçük dosyaları katmanlama ve geri çağırma performans yükü alan tasarruflarını aşacak şekilde 64 KiB 'den küçük olan katmanlama dosyalarını desteklemez.

 > [!Important]  
 > Katmanlı dosyaları geri çağırmak için ağ bant genişliği en az 1 MB/sn olmalıdır. Ağ bant genişliği 1 MB/sn 'den küçükse, dosyalar bir zaman aşımı hatasıyla geri çekemeyebilir.

## <a name="cloud-tiering-faq"></a>Bulut katmanlama hakkında SSS

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Bulut katmanlaması nasıl çalışır?
Azure Dosya Eşitleme sistem filtresi, her sunucu uç noktasında ad alanınız için bir "heatmap" oluşturur. Zaman içinde erişimleri (okuma ve yazma işlemleri) izler, sonra da erişim sıklığı ve, her ikisine de göre her dosyaya bir ısı puanı atar. En son açılan bir dosya sık erişilen olarak kabul edilir, ancak bir süre önce dokunulmayan ve erişilmeyen bir dosya seyrek olarak değerlendirilir. Bir sunucudaki dosya birimi, ayarladığınız birimde boş alan eşiğini aşarsa, boş alan yüzdesi karşılanana kadar, en fazla dosyaları Azure dosyalarına katman olarak alır.

Azure Dosya Eşitleme aracısının 4,0 ve üzeri sürümlerinde, belirtilen gün sayısı içinde erişilmeyen veya değiştirilemeyen tüm dosyaları barındıracak her bir sunucu uç noktasında bir tarih ilkesi de belirtebilirsiniz.

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>Birim boş alanı katmanlama ilkesi nasıl çalışır?
Birim boş alanı, bir sunucu uç noktasının bulunduğu birimde ayırmak istediğiniz boş alan miktarıdır. Örneğin, birim boş alanı bir sunucu uç noktası olan bir birimde% 20 ' ye ayarlandıysa, birim alanının% 80 ' e kadar, en son erişilen dosyalar tarafından Azure 'a kadar katmanlı olan bu alana sığmayan diğer dosyalar ile birlikte yer alır. Birim boş alanı, tek tek dizinlerin veya eşitleme gruplarının düzeyinde değil, birim düzeyinde geçerlidir. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>Birimin boş alanı katmanlama ilkesi yeni sunucu uç noktalarına göre nasıl çalışır?
Sunucu uç noktası yeni sağlandığında ve bir Azure dosya paylaşımında bağlandığında, sunucu önce ad alanını çeker ve ardından birim boş alanı eşiğini bulana kadar gerçek dosyaları çeker. Bu işlem, hızlı olağanüstü durum kurtarma veya hızlı ad alanı geri yükleme olarak da bilinir.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>Bir birimde birden çok sunucu uç noktası olduğunda birim boş alanı nasıl yorumlanır?
Bir birimde birden fazla sunucu uç noktası olduğunda, etkin birimde boş alan eşiği, söz konusu birimdeki herhangi bir sunucu uç noktasında belirtilen en büyük birim boş alanıdır. Dosyalar, ait oldukları sunucu uç noktasından bağımsız olarak kullanım düzenlerine göre katmanlanacaktır. Örneğin, bir birimde iki sunucu uç noktası varsa, Endpoint1 ve Endpoint2 ' de bir birimde boş alan eşiğinin% 25 ' i ve Endpoint2% 50 ' lik bir birim boş alan eşiğine sahip olduğunda, her iki sunucu uç noktası için birim boş alan eşiği% 50 olacaktır. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>Tarih katmanlama ilkesi, birim boş alan katmanlama ilkesiyle birlikte nasıl çalışır? 
Sunucu uç noktasında bulut katmanlamayı etkinleştirirken bir birimde boş alan ilkesi ayarlarsınız. Tarih ilkesi de dahil olmak üzere her zaman diğer ilkelere göre önceliklidir. İsteğe bağlı olarak, söz konusu birimdeki her bir sunucu uç noktası için bir tarih ilkesini etkinleştirebilirsiniz. Bu ilke, yalnızca bu ilkenin açıkladığı gün aralığı içinde (yani, okuma veya yazma), tüm staler dosyaları katmanlı olan yerel olarak tutulur. Birim boş alanı ilkesinin her zaman öncelikli olduğunu ve birimde, tarih ilkesi tarafından açıklandığı şekilde çok sayıda gün daha korumak için yeterli boş alan olmadığında Azure Dosya Eşitleme, birim boş alana kadar bu dosyaları yeniden katmanlama alan yüzdesi karşılandı.

Örneğin, 60 günlük bir tarih tabanlı katmanlama ilkeniz ve% 20 ' lik bir birim boş alan ilkesi olduğunu varsayalım. Tarih ilkesini uyguladıktan sonra birimde boş alan% 20 ' den az olduğunda, birim boş alan ilkesi başlatılır ve Tarih ilkesini geçersiz kılar. Bu, daha fazla dosyanın katmanlı olmasını sağlar. bu şekilde, sunucuda tutulan verilerin miktarı 60 günlük veriler 45 güne azalabilir. Buna karşılık, bu ilke boş alan eşiğine ulaşamasanız bile zaman aralığınızı aşacak olan dosyaların katmanlamasını zorlar. bu nedenle, biriminiz boş olsa bile 61 gün öncesine sahip bir dosya katmanlanacaktır.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>Nasıl yaparım? uygun miktarda birimde boş alan belirlenir.
Yerel tutmanız gereken veri miktarı bazı etkenlere göre belirlenir: bant genişliğiniz, veri kümenizin erişim deseninin ve bütçeniz. Düşük bant genişliğine sahip bir bağlantınız varsa, kullanıcılarınız için en az gecikme olduğundan emin olmak için verilerinizin yerel kalmasını sağlamak isteyebilirsiniz. Aksi takdirde, belirli bir süre boyunca dalgalanma oranını temel alabilirsiniz. Örneğin, 1 TB 'lık veri kümenizin yaklaşık% 10 ' u değişiklik olduğunu veya her ay etkin bir şekilde erişildiğini biliyorsanız, dosyaları sık geri çekmediğinizde 100 GB yerel tutmak isteyebilirsiniz. Biriminiz 2TB ise,% 5 ' i (veya 100 GB) yerel tutmak isteyeceksiniz, yani kalan% 95, birim boş alanınız yüzdesidir. Ancak, daha yüksek değişim dönemleri için hesaba bir arabellek eklemenizi öneririz. diğer bir deyişle, daha düşük bir birimde boş alan yüzdesi ile başlayıp daha sonra gerekirse bu değeri ayarlayabilirsiniz. 

Verilerin yerel olarak tutulması, Azure 'dan daha az sayıda dosya geri çekeceği ve aynı zamanda kendi maliyetiyle birlikte gelen daha büyük miktarda şirket içi depolama alanı tutmanızı gerektiren çıkış maliyetlerinin düşük olmasını sağlar. Azure Dosya Eşitleme dağıtılan bir örneğe sahip olduktan sonra, birim boş alan ayarlarınızın kullanımınız için uygun olup olmadığını kabaca ölçmek üzere depolama hesabınızın çıkış bölümüne bakabilirsiniz. Depolama hesabının yalnızca Azure Dosya Eşitleme bulut uç noktasını (yani, eşitleme paylaşımınızı) içerdiğini varsayarak, yüksek çıkış, buluttan çok sayıda dosyanın geri çağrılmakta olduğu ve yerel önbelleğinizi artırmayı göz önünde bulundurmanız gereken anlamına gelir.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>Yeni bir sunucu uç noktası ekledim. Bu sunucu katmanında dosyalarımı ne kadar süreyle kullanıyorsunuz?
Azure Dosya Eşitleme aracısının 4,0 ve üzeri sürümlerinde, dosyalarınız Azure dosya paylaşımında karşıya yüklendikten sonra, sonraki katmanlama oturumu çalıştıktan hemen sonra, bir saat sonra gerçekleşen ilkelerinize göre katmanlanırlar. Daha eski aracılarda katmanlama, 24 saate kadar sürebilir.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>Bir dosyanın katmanlı olup olmadığını nasıl anlayabilirim?
Bir dosyanın Azure dosya paylaşımınıza katmanlı olup olmadığını denetlemek için çeşitli yollar vardır:
    
   *  **Dosyadaki dosya özniteliklerini denetleyin.**
     Dosyaya sağ tıklayın, **Ayrıntılar**' a gidin ve ardından **öznitelikler** özelliğine kaydırın. Katmanlı bir dosya aşağıdaki öznitelikler kümesine sahiptir:     
        
        | Öznitelik harfi | Öznitelik | Tanım |
        |:----------------:|-----------|------------|
        | A | Arşiv | Dosyanın yedekleme yazılımı tarafından yedeklenmesi gerektiğini gösterir. Bu öznitelik, dosyanın katmanlı veya tam disk üzerinde depolanmadığına bakılmaksızın her zaman ayarlanır. |
        | P | Seyrek dosya | Dosyanın seyrek bir dosya olduğunu gösterir. Seyrek dosya, NTFS 'nin disk akışındaki dosya genellikle boş olduğunda verimli kullanım için sunduğu özelleşmiş bir dosya türüdür. Azure Dosya Eşitleme, bir dosya tamamen katmanlı veya kısmen geri çekilmiş olduğundan seyrek dosyalar kullanır. Tam katmanlı bir dosyada, dosya akışı bulutta depolanır. Kısmen geri çekilmiş bir dosyada, dosyanın o bölümü zaten disk üzerinde. Bir dosya diske tamamen geri çekilir Azure Dosya Eşitleme, dosyayı seyrek bir dosyadan normal bir dosyaya dönüştürür. |
        | Ç | Yeniden ayrıştırma noktası | Dosyanın bir yeniden ayrıştırma noktası olduğunu gösterir. Yeniden ayrıştırma noktası, bir dosya sistemi filtresi tarafından kullanılmak üzere özel bir işaretçisidir. Azure Dosya Eşitleme, dosyanın depolandığı bulut konumunu Azure Dosya Eşitleme dosya sistemi filtresine (Storagessync. sys) tanımlamak için yeniden ayrıştırma noktaları kullanır. Bu, sorunsuz erişimi destekler. Kullanıcıların Azure Dosya Eşitleme kullanıldığını veya Azure dosya paylaşımınızda dosyaya nasıl erişim alınacağını bilmeleri gerekmez. Bir dosya tamamen geri çekilir Azure Dosya Eşitleme, yeniden ayrıştırma noktasını dosyadan kaldırır. |
        | O | Offline | Dosyanın içeriğinin bir kısmının veya tümünün diskte depolanmadığını belirtir. Bir dosya tamamen geri çekilir Azure Dosya Eşitleme, bu özniteliği kaldırır. |

        ![Ayrıntılar sekmesi seçiliyken bir dosyanın özellikler iletişim kutusu](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Dosya Gezgini 'nin tablo görüntüsüne **öznitelikler** alanını ekleyerek bir klasördeki tüm dosyaların özniteliklerini görebilirsiniz. Bunu yapmak için var olan bir sütuna (örneğin, **Boyut**) sağ tıklayın, **daha fazla**' yı seçin ve ardından açılan listeden **öznitelikler** ' i seçin.
        
   * **Bir `fsutil` dosyadaki yeniden ayrıştırma noktalarını denetlemek için kullanın.**
       Önceki seçenekte açıklandığı gibi, katmanlı bir dosya her zaman bir yeniden ayrıştırma noktası kümesine sahiptir. Yeniden ayrıştırma işaretçisi, Azure Dosya Eşitleme dosya sistemi filtresi (Storagessync. sys) için özel bir işaretçidir. Bir dosyanın yeniden ayrıştırma noktasına sahip olup olmadığını denetlemek için, yükseltilmiş bir komut istemi veya PowerShell penceresinde, `fsutil` yardımcı programını çalıştırın:
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        Dosyada bir yeniden ayrıştırma noktası varsa, yeniden ayrıştırma etiketi değerini görmeyi **bekleyebilir: 0x8000001E**. Bu onaltılık değer Azure Dosya Eşitleme sahip olan yeniden ayrıştırma noktası değeridir. Çıktı Ayrıca, Azure dosya paylaşımınızda dosyanızın yolunu temsil eden yeniden ayrıştırma verilerini içerir.

        > [!WARNING]  
        > `fsutil reparsepoint` Yardımcı program komutunun aynı zamanda bir yeniden ayrıştırma noktasını silme yeteneği de vardır. Azure Dosya Eşitleme mühendislik ekibi sizi sormadığı müddetçe bu komutu yürütün. Bu komutun çalıştırılması, veri kaybına neden olabilir. 

<a id="afs-recall-file"></a>

### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>Kullanmak istediğim bir dosya katmanlı. Dosyayı yerel olarak kullanmak için diske nasıl geri çekirim?
Dosyayı diske geri çekmenin en kolay yolu dosyayı açmak. Azure Dosya Eşitleme dosya sistemi filtresi (Storagessync. sys), sizin bölüminizden herhangi bir iş yapmadan dosyayı Azure dosya paylaşımınızdan sorunsuz bir şekilde indirir. Multimedya veya. zip dosyaları gibi kısmen okunabilecek dosya türleri için bir dosyayı açmak dosyanın tamamını indirmez.

Ayrıca, bir dosyanın geri çağrılamasını zorlamak için PowerShell kullanabilirsiniz. Bu seçenek, bir klasördeki tüm dosyalar gibi birden çok dosyayı aynı anda geri çağırmak istiyorsanız yararlı olabilir. Azure Dosya Eşitleme yüklendiği sunucu düğümünde bir PowerShell oturumu açın ve ardından aşağıdaki PowerShell komutlarını çalıştırın:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -Order CloudTieringPolicy
```

Belirtildiğinde `-Order CloudTieringPolicy` , en son değiştirilen dosyalar önce geri alınacaktır.
Diğer isteğe bağlı parametreler:
* `-ThreadCount`paralel olarak kaç dosya geri çağrılabileceğini belirler.
* `-PerFileRetryCount`Şu anda engellenen bir dosya için bir geri çekmenin ne sıklıkta denenmeyeceğini belirler.
* `-PerFileRetryDelaySeconds`yeniden çağırma denemeleri arasındaki saniye cinsinden süreyi belirler ve bir önceki parametreyle birlikte her zaman kullanılmalıdır.

> [!Note]  
> Sunucuyu barındıran yerel birimde, tüm katmanlı verileri yeniden çağırmak için yeterli boş alan yoksa, `Invoke-StorageSyncFileRecall` cmdlet başarısız olur.  

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Azure Dosya Eşitleme, bir dosyanın *disk özelliği üzerindeki boyutu* neden bir dosya için *Boyut* özelliği ile eşleşmiyor? 
Windows Dosya Gezgini, bir dosyanın boyutunu temsil etmek için iki özellik sunar: Diskteki **Boyut** ve **Boyut**. Bu özellikler, anlamı daha da farklıdır. **Boyut** , dosyanın tamamının boyutunu temsil eder. **Disk üzerindeki boyut** , diskte depolanan dosya akışının boyutunu temsil eder. Bu özelliklerin değerleri, sıkıştırma, yinelenen verileri kaldırma veya Azure Dosya Eşitleme ile bulut katmanlama gibi çeşitli nedenlerle farklılık gösterebilir. Bir dosya Azure dosya paylaşımında katmanlanırsa, dosya akışı diskte değil, Azure dosya paylaşımınızda depolandığından, diskteki boyut sıfırdır. Ayrıca, bir dosyanın kısmen katmanlı (veya kısmen geri çekilmiş) olması da mümkündür. Kısmen katmanlı bir dosyada, dosyanın bir kısmı diskte bulunur. Bu durum, dosyalar, multimedya oynatıcılar veya ZIP yardımcı programları gibi uygulamalar tarafından kısmen okunarak ortaya çıkabilir. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Nasıl yaparım? bir dosyanın veya dizinin katmanlanmasını zorlıyor musunuz?
Bulut katmanlama özelliği etkinleştirildiğinde, bulut katmanlaması, dosyaları son erişime göre otomatik olarak katmanlarını ve bulut uç noktasında belirtilen birim boş alan yüzdesine ulaşmak için zaman sayısını değiştirir. Bazen bir dosyayı katmana el ile zorlamak isteyebilirsiniz. Bu, uzun bir süre için yeniden kullanmayı düşünmediğiniz büyük bir dosyayı kaydedip daha sonra biriminizdeki boş alanın diğer dosya ve klasörler için kullanmasını istediğiniz durumlarda yararlı olabilir. Aşağıdaki PowerShell komutlarını kullanarak, katmanlamayı zorlayabilirsiniz:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

## <a name="next-steps"></a>Sonraki Adımlar
* [Azure Dosya Eşitleme dağıtımı için planlama yapma](storage-sync-files-planning.md)
