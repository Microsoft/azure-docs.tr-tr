---
title: Azure Dosya Eşitleme dağıtımı planlama | Microsoft Docs
description: Şirket içi Windows Server veya bulut VM 'de birkaç Azure dosya paylaşımını önbelleğe almanıza olanak sağlayan bir hizmet olan Azure Dosya Eşitleme bir dağıtım planlayın.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 7980ea5811fe77bc80d97e31e3b852766debd31a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797298"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Azure Dosya Eşitleme dağıtımı planlama

:::row:::
    :::column:::
        [![Görüşme ve tanıtım tanıtımı Azure Dosya Eşitleme-oynatmak için tıklayın!](./media/storage-sync-files-planning/azure-file-sync-interview-video-snapshot.png)](https://www.youtube.com/watch?v=nfWLO7F52-s)
    :::column-end:::
    :::column:::
        Azure Dosya Eşitleme, şirket içi Windows Server veya bulut VM 'de birkaç Azure dosya paylaşımını önbelleğe almanıza olanak tanıyan bir hizmettir. 
        
        Bu makale, kavramları ve özellikleri Azure Dosya Eşitleme size tanıtır. Azure Dosya Eşitleme hakkında bilgi sahibi olduktan sonra, bu hizmeti denemek için [Azure dosya eşitleme dağıtım kılavuzunu](file-sync-deployment-guide.md) takip etmeyi göz önünde bulundurun.        
    :::column-end:::
:::row-end:::

Dosyalar [Azure dosya paylaşımlarında](../files/storage-files-introduction.md)bulutta depolanır. Azure dosya paylaşımları iki şekilde kullanılabilir: Bu sunucusuz Azure dosya paylaşımlarını (SMB) doğrudan bağlayarak veya Azure Dosya Eşitleme kullanarak şirket içi Azure dosya paylaşımlarını önbelleğe alarak. Seçtiğiniz dağıtım seçeneği, dağıtımınız için plan yaparken göz önünde bulundurmanız gereken yönleri değiştirir. 

- **Azure dosya paylaşımının doğrudan bağlanması**: Azure dosyaları SMB erişimi sağladığından, Windows, MacOS ve Linux 'ta bulunan standart SMB istemcisini kullanarak şirket içinde veya bulutta Azure dosya paylaşımlarını bağlayabilirsiniz. Azure dosya paylaşımları sunucusuz olduğundan, üretim senaryolarına yönelik dağıtım, bir dosya sunucusu veya NAS cihazının yönetilmesini gerektirmez. Bu, yazılım düzeltme ekleri uygulamanız veya fiziksel diskleri takas etmeniz gerekmediği anlamına gelir. 

- **Şirket Içi Azure dosya paylaşımını Azure dosya eşitleme Ile önbelleğe alma**: Azure dosya eşitleme, kuruluşunuzun dosya paylaşımlarını Azure dosyalarında merkezileştirirken şirket içi bir dosya sunucusunun esnekliğini, performansını ve uyumluluğunu mümkün tutmaya olanak sağlar. Azure Dosya Eşitleme, şirket içi (veya bulut) Windows Server 'ı Azure dosya paylaşımınızın hızlı önbelleğine dönüştürür. 

## <a name="management-concepts"></a>Yönetim kavramları
Azure Dosya Eşitleme dağıtımı üç temel Yönetim nesnesine sahiptir:

- **Azure dosya paylaşma**: bir Azure dosya paylaşma, bir Azure dosya eşitleme eşitleme ilişkisinin *bulut uç noktasını* sağlayan sunucusuz bir bulut dosyası paylaşımıdır. Azure dosya paylaşımında bulunan dosyalara doğrudan SMB veya FileREST protokolüyle erişilebilir, ancak Azure dosya paylaşımının Azure Dosya Eşitleme ile kullanıldığı durumlarda dosyalara Windows Server önbelleği üzerinden erişmenizi öneririz. Bunun nedeni, bugün Azure dosyalarının Windows Server gibi etkili bir değişiklik algılama mekanizmasına sahip olmadığı için, Azure dosya paylaşımında doğrudan değişikliklerin sunucu uç noktalarına geri yayılması zaman alır.
- **Sunucu uç noktası**: bir Azure dosya paylaşımıyla eşitlenen Windows Server 'ın yolu. Bu bir birimde veya birimin kökünde belirli bir klasör olabilir. Aynı birimde, ad alanları çakışmazsa birden çok sunucu uç noktası bulunabilir.
- **Eşitleme grubu**: bir **bulut uç noktası** veya Azure dosya paylaşımıyla bir sunucu uç noktası arasındaki eşitleme ilişkisini tanımlayan nesne. Bir eşitleme grubu içindeki uç noktalar, birbiriyle eşitlenmiş durumda tutulur. Örneğin, Azure Dosya Eşitleme ile yönetmek istediğiniz iki farklı dosya kümesine sahipseniz, iki eşitleme grubu oluşturur ve her bir eşitleme grubuna farklı uç noktalar eklersiniz.

### <a name="azure-file-share-management-concepts"></a>Azure dosya paylaşma yönetim kavramları
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Azure Dosya Eşitleme yönetim kavramları
Eşitleme grupları, Azure Dosya Eşitleme ile kullanım için sunucuları kaydeden ve eşitleme grubu ilişkilerini içeren en üst düzey nesneler olan **depolama Eşitleme Hizmetleri**' ne dağıtılır. Depolama eşitleme hizmeti kaynağı, depolama hesabı kaynağının bir eşledir ve benzer şekilde Azure Kaynak gruplarına dağıtılabilir. Depolama eşitleme hizmeti, birden çok depolama hesabı ve birden çok kayıtlı Windows sunucusu arasında Azure dosya paylaşımları içeren eşitleme grupları oluşturabilir.

Bir depolama eşitleme hizmetinde bir eşitleme grubu oluşturabilmeniz için önce depolama eşitleme hizmeti ile bir Windows Server kaydetmelisiniz. Bu, sunucunuz veya kümeniz ile depolama eşitleme hizmeti arasındaki bir güven ilişkisini temsil eden **kayıtlı bir sunucu** nesnesi oluşturur. Bir depolama eşitleme hizmeti kaydetmek için öncelikle sunucuya Azure Dosya Eşitleme aracısını yüklemeniz gerekir. Tek bir sunucu veya küme, aynı anda yalnızca bir depolama eşitleme hizmeti ile kaydedilebilir.

Bir eşitleme grubu, bir bulut uç noktası veya Azure dosya paylaşımının yanı sıra en az bir sunucu uç noktası içerir. Sunucu uç noktası nesnesi, Azure Dosya Eşitleme önbelleğe alma özelliğini sağlayan **bulut katmanlama** özelliğini yapılandıran ayarları içerir. Azure dosya paylaşımıyla eşitleme yapmak için, Azure dosya paylaşımının bulunduğu depolama hesabı, depolama eşitleme hizmeti ile aynı Azure bölgesinde olmalıdır.

> [!Important]  
> Eşitleme grubundaki herhangi bir bulut uç noktası veya sunucu uç noktasında değişiklik yapabilir ve dosyalarınızı eşitleme grubundaki diğer uç noktalarla eşitler. Bulut uç noktasına (Azure dosya paylaşımında) doğrudan değişiklik yaparsanız, önce değişikliklerin Azure Dosya Eşitleme değişiklik algılama işi tarafından bulunması gerekir. Bir değişiklik algılama işi, her 24 saatte bir bulut uç noktası için başlatılır. Daha fazla bilgi için bkz. [Azure dosyaları hakkında sık sorulan sorular](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#afs-change-detection).

### <a name="consider-the-count-of-storage-sync-services-needed"></a>Gereken depolama eşitleme hizmeti sayısını göz önünde bulundurun
Önceki bölümde Azure Dosya Eşitleme: bir *depolama eşitleme hizmeti* için yapılandırılacak çekirdek kaynak ele alınmaktadır. Windows Server yalnızca bir depolama Eşitleme hizmetine kaydedilebilir. Bu nedenle, yalnızca tek bir depolama eşitleme hizmeti dağıtmak ve tüm sunucularını kaydetmek en iyisidir. 

Yalnızca şu durumlarda birden çok depolama eşitleme hizmeti oluşturun:
* hiçbir şekilde verileri birbiriyle değiş tokuşu gereken farklı sunucu kümeleri. Bu durumda, farklı bir depolama eşitleme hizmetindeki eşitleme grubundaki bir bulut uç noktası olarak zaten kullanımda olan bir Azure dosya paylaşımıyla eşitlenecek şekilde, sistemi tasarlamak istiyorsunuz. Buna baktığınızın bir diğer yolu da, farklı depolama Eşitleme hizmetine kayıtlı Windows sunucularının aynı Azure dosya paylaşımıyla eşitlenemediği bir yoldur.
* tek bir depolama eşitleme hizmeti 'nin destekleyebileceğinden daha fazla kayıtlı sunucu veya eşitleme grubu olması gerekir. Daha fazla ayrıntı için [Azure dosya eşitleme ölçek hedeflerini](../files/storage-files-scale-targets.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#azure-file-sync-scale-targets) gözden geçirin.

## <a name="plan-for-balanced-sync-topologies"></a>Dengeli eşitleme topolojileri için plan yapın
Herhangi bir kaynağı dağıtmadan önce, Azure dosya paylaşımının bulunduğu yerel bir sunucuda neleri eşitlediklerinizi planlamanız önemlidir. Bir plan yapılması, kaç depolama hesabı, Azure dosya paylaşımı ve eşitleme kaynağı gerektiğini belirlemenize yardımcı olur. Verileriniz şu anda bir Windows Server 'da veya uzun dönem kullanmak istediğiniz sunucuda yer almasa bile, bu konular hala geçerlidir. [Geçiş bölümü](#migration) , durumunuza uygun geçiş yollarını belirlemenize yardımcı olabilir.

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="windows-file-server-considerations"></a>Windows dosya sunucusu konuları
Windows Server 'da eşitleme özelliğini etkinleştirmek için, indirilebilir Azure Dosya Eşitleme aracısını yüklemelisiniz. Azure Dosya Eşitleme Aracısı iki ana bileşen sağlar: `FileSyncSvc.exe` , sunucu uç noktalarında değişiklikleri izlemekten sorumlu olan ve eşitleme oturumlarını başlatan ve `StorageSync.sys` bulut katmanlaması ve hızlı olağanüstü durum kurtarmayı sağlayan bir dosya sistemi filtresi.  

### <a name="operating-system-requirements"></a>İşletim sistemi gereksinimleri
Azure Dosya Eşitleme, Windows Server 'ın aşağıdaki sürümleriyle desteklenir:

| Sürüm | Desteklenen SKU 'Lar | Desteklenen Dağıtım seçenekleri |
|---------|----------------|------------------------------|
| Windows Server 2019 | Veri Merkezi, standart ve IoT | Tam ve çekirdek |
| Windows Server 2016 | Veri Merkezi, standart ve depolama sunucusu | Tam ve çekirdek |
| Windows Server 2012 R2 | Veri Merkezi, standart ve depolama sunucusu | Tam ve çekirdek |

Windows Server 'ın gelecek sürümleri, yayımlandıklarında eklenecektir.

> [!Important]  
> Azure Dosya Eşitleme ile kullandığınız tüm sunucuları Windows Update en son güncelleştirmelerle güncel tutmanız önerilir. 

### <a name="minimum-system-resources"></a>En düşük sistem kaynakları
Azure Dosya Eşitleme, en az bir CPU ve en az 2 GiB belleği olan fiziksel ya da sanal bir sunucu gerektirir.

> [!Important]  
> Sunucu dinamik bellek etkinleştirilmiş bir sanal makinede çalışıyorsa, VM 'nin en az 2048 MiB bellek ile yapılandırılması gerekir.

Çoğu üretim iş yükleri için, yalnızca minimum gereksinimlere sahip bir Azure Dosya Eşitleme eşitleme sunucusu yapılandırmanızı önermiyoruz. Daha fazla bilgi için [Önerilen sistem kaynaklarına](#recommended-system-resources) bakın.

### <a name="recommended-system-resources"></a>Önerilen sistem kaynakları
Tüm sunucu özellikleri veya uygulamalar gibi, Azure Dosya Eşitleme için sistem kaynak gereksinimleri, dağıtımın ölçeği tarafından belirlenir; bir sunucudaki daha büyük dağıtımlar daha fazla sistem kaynağı gerektirir. Azure Dosya Eşitleme için, ölçek sunucu uç noktaları genelinde nesne sayısına ve veri kümesindeki dalgalanma göre belirlenir. Tek bir sunucu birden çok eşitleme grubunda sunucu uç noktalarına ve bir sunucunun eklendiği tam ad alanı için aşağıdaki tablo hesaplarında listelenen nesne sayısına sahip olabilir. 

Örneğin, 10.000.000 nesneleriyle sunucu uç noktası A ve 10.000.000 Objects = 20.000.000 nesneleriyle sunucu uç noktası B. Bu örnek dağıtım için, ilk geçiş için 8 CPU, sabit durum için 16 GiB bellek ve (mümkünse) 48 GiB bellek önereceğiz.
 
Ad alanı verileri performans nedenleriyle bellekte depolanır. Bu nedenle, daha büyük ad alanları iyi performans sağlamak için daha fazla bellek gerektirir ve daha fazla dalgalanma daha fazla CPU gerektirir. 
 
Aşağıdaki tabloda, ortalama dosya boyutu 512 KiB olduğunda, ad alanının boyutunu ve tipik genel amaçlı dosya paylaşımları için kapasiteye dönüştürmeyi sağladık. Dosya boyutlarınız daha küçükse, aynı kapasite miktarı için ek bellek eklemeyi göz önünde bulundurun. Bellek yapılandırmanızı ad alanının boyutuna dayandırın.

| Ad alanı boyutu-dosyalar & dizinler (milyonlarca)  | Tipik kapasite (TiB)  | CPU çekirdekleri  | Önerilen bellek (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (ilk eşitleme)/2 (tipik dalgalanma)      |
| 5        | 2.3     | 2        | 16 (ilk eşitleme)/4 (tipik dalgalanma)    |
| 10       | 4.7     | 4        | 32 (ilk eşitleme)/8 (tipik dalgalanma)   |
| 30       | 14.0    | 8        | 48 (ilk eşitleme)/16 (tipik dalgalanma)   |
| 50       | 23.3    | 16       | 64 (ilk eşitleme)/32 (tipik dalgalanma)  |
| 100 *     | 46,6    | 32       | 128 (ilk eşitleme)/32 (tipik dalgalanma)  |

\*100.000.000 'den fazla dosyanın eşitlenmesi & dizinler Şu anda önerilmez. Bu, test edilen eşiklerimize göre bir geçici limit. Daha fazla bilgi için bkz. [Azure dosya eşitleme ölçek hedefleri](../files/storage-files-scale-targets.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#azure-file-sync-scale-targets).

> [!TIP]
> Bir ad alanının ilk eşitlemesi yoğun bir işlemdir ve ilk eşitleme tamamlanana kadar daha fazla bellek ayırmayı öneririz. Bu gerekli değildir, ancak ilk eşitlemeyi hızlandırabilir. 
> 
> Normal karmaşıklık, her gün değişen ad alanı% 0,5 ' dir. Daha yüksek dalgalanma düzeyleri için daha fazla CPU eklemeyi düşünün. 

- NTFS dosya sistemiyle biçimlendirilen yerel olarak bağlanmış bir birim.

### <a name="evaluation-cmdlet"></a>Değerlendirme cmdlet 'i
Azure Dosya Eşitleme dağıtılmadan önce, Azure Dosya Eşitleme değerlendirme cmdlet 'ini kullanarak sistem ile uyumlu olup olmadığını değerlendirmelisiniz. Bu cmdlet, dosya sisteminiz ve veri kümeniz ile desteklenmeyen karakterler veya desteklenmeyen bir işletim sistemi sürümü gibi olası sorunları denetler. Denetimleri, aşağıda bahsedilen özelliklerinin çoğunu kapsar; dağıtımınızın sorunsuz bir şekilde çalışmasını sağlamak için bu bölümün geri kalanını dikkatle okumanız önerilir. 

Değerlendirme cmdlet 'i, buradaki yönergeleri izleyerek yüklenebilen az PowerShell modülünün yüklenerek yüklenebilir: [Azure PowerShell yükleme ve yapılandırma](/powershell/azure/install-Az-ps).

#### <a name="usage"></a>Kullanım  
Değerlendirme aracını birkaç farklı şekilde çağırabilirsiniz: sistem denetimleri, veri kümesi denetimleri veya her ikisini de yapabilirsiniz. Hem sistem hem de veri kümesi denetimleri gerçekleştirmek için: 

```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

Yalnızca veri kümenizi test etmek için:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Yalnızca sistem gereksinimlerini test etmek için:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name> -SkipNamespaceChecks
```
 
Sonuçları CSV 'de göstermek için:
```powershell
$validation = Invoke-AzStorageSyncCompatibilityCheck C:\DATA
$validation.Results | Select-Object -Property Type, Path, Level, Description, Result | Export-Csv -Path C:\results.csv -Encoding utf8
```

### <a name="file-system-compatibility"></a>Dosya sistemi uyumluluğu
Azure Dosya Eşitleme yalnızca doğrudan bağlı, NTFS birimlerinde desteklenir. Windows Server 'da doğrudan bağlı depolama veya DAS, Windows Server işletim sisteminin dosya sistemine sahip olduğu anlamına gelir. DAS 'ler, dosya sunucusuna fiziksel olarak disk ekleyerek, sanal diskleri bir dosya sunucusu VM 'sine (Hyper-V tarafından barındırılan bir VM gibi), hatta ISCSı aracılığıyla da eklenebilir.

Yalnızca NTFS birimleri desteklenir; ReFS, FAT, FAT32 ve diğer dosya sistemleri desteklenmez.

Aşağıdaki tabloda NTFS dosya sistemi özelliklerinin birlikte çalışma durumu gösterilmektedir: 

| Özellik | Destek durumu | Notlar |
|---------|----------------|-------|
| Erişim denetim listeleri (ACL’ler) | Tam olarak destekleniyor | Windows stili isteğe bağlı erişim denetim listeleri Azure Dosya Eşitleme tarafından korunur ve sunucu uç noktalarında Windows Server tarafından zorlanır. Azure dosya paylaşımının doğrudan bağlanması sırasında ACL 'Ler de zorlanabilir, ancak bunun için ek yapılandırma gerekir. Daha fazla bilgi için [kimlik bölümüne](#identity) bakın. |
| Sabit bağlantılar | Atlandı | |
| Sembolik bağlantılar | Atlandı | |
| Bağlama noktaları | Kısmen destekleniyor | Bağlama noktaları bir sunucu uç noktasının kökü olabilir, ancak sunucu uç noktasının ad alanında yer alıyorsa atlanır. |
| Merkezleriyle | Atlandı | Örneğin, DfrsrPrivate ve Dfskökleri klasörleri Dağıtılmış Dosya Sistemi. |
| Yeniden ayrıştırma noktaları | Atlandı | |
| NTFS sıkıştırması | Tam olarak destekleniyor | |
| Seyrek dosyalar | Tam olarak destekleniyor | Seyrek dosya eşitleme (engellenmez), ancak buluta tam dosya olarak eşitlenir. Bulutta dosya içerikleri değiştiğinde (veya başka bir sunucuda), değişiklik indirildiyse dosya artık seyrek olmaz. |
| Alternatif veri akışları (ADS) | Korunuyor, ancak eşitlenmedi | Örneğin, Dosya Sınıflandırma Altyapısı tarafından oluşturulan sınıflandırma etiketleri eşitlenmez. Sunucu uç noktalarında bulunan dosyalardaki mevcut sınıflandırma etiketleri dokunmaz. |

<a id="files-skipped"></a>Azure Dosya Eşitleme, bazı geçici dosyaları ve sistem klasörlerini de atlar:

| Dosya/klasör | Not |
|-|-|
| pagefile.sys | Sisteme özel dosya |
| Desktop.ini | Sisteme özel dosya |
| thumbs. db | Küçük resimler için geçici dosya |
| ehThumbs. db | Medya küçük resimleri için geçici dosya |
| ~$\*.\* | Office geçici dosyası |
| \*. tmp | Geçici dosya |
| \*. laccdb | DB kilitleme dosyasına erişin|
| 635D02A9D91C401B97884B82B3BCDADEA. * | İç eşitleme dosyası|
| \\Sistem birimi bilgileri | Birime özgü klasör |
| $RECYCLE. BÖLME| Klasör |
| \\Eşitleme | Eşitleme klasörü |

### <a name="failover-clustering"></a>Yük Devretme Kümelemesi
Windows Server Yük Devretme Kümelemesi, "genel kullanım için dosya sunucusu" dağıtım seçeneği için Azure Dosya Eşitleme tarafından desteklenir. Yük Devretme Kümelemesi, "uygulama verileri için Genişleme Dosya Sunucusu" (SOFS) veya kümelenmiş paylaşılan birimlerde (CSV) desteklenmez.

> [!Note]  
> Eşitlemenin doğru çalışması için, bir yük devretme kümesindeki her düğümde Azure Dosya Eşitleme aracısının yüklü olması gerekir.

### <a name="data-deduplication"></a>Yinelenen Verileri Kaldırma
**Windows Server 2016 ve Windows Server 2019**   
Yinelenen verileri kaldırma, Windows Server 2016 ve Windows Server 2019 için birimdeki bir veya daha fazla sunucu uç noktasında bulut katmanlaması etkinleştirilmiş veya devre dışı bırakılmış olsun, desteklenir. Bulut katmanlaması etkinleştirilmiş bir birimde yinelenen verileri kaldırma özelliğinin etkinleştirilmesi, daha fazla depolama sağlamaya gerek kalmadan şirket içi daha fazla dosya önbelleğe almanızı sağlar. 

Bulut katmanlaması etkinleştirilmiş bir birimde yinelenen verileri kaldırma etkin olduğunda, sunucu uç noktası konumu içinde en iyi duruma getirilmiş dosyalar bulut katmanlama ilkesi ayarlarına bağlı olarak normal bir dosyaya benzer şekilde katmanlanacaktır. Yinelenenleri kaldırma için iyileştirilmiş dosyalar katmanlandıktan sonra, yinelenen verileri kaldırma atık toplama işi, artık birimdeki diğer dosyalar tarafından başvurulmayan gereksiz öbekleri kaldırarak disk alanını geri kazanmak için otomatik olarak çalışır.

Birim tasarruflarının yalnızca sunucuya uygulanacağını aklınızda yapın; Azure dosya paylaşımındaki verileriniz yinelenenleri kaldırma işlemi geri alınamaz.

> [!Note]  
> Windows Server 2019 ' de etkinleştirilmiş bulut katmanlaması olan birimlerde yinelenen verileri kaldırmayı desteklemek için Windows Update [KB4520062-2019](https://support.microsoft.com/help/4520062) veya daha sonraki bir aylık toplu güncelleştirme yüklenmelidir ve Azure dosya eşitleme aracı sürümü 12.0.0.0 veya daha yeni bir sürüm gerekir.

**Windows Server 2012 R2**  
Azure Dosya Eşitleme, Windows Server 2012 R2 'deki aynı birimde yinelenen verileri kaldırma ve bulut katmanlaması desteklemez. Yinelenen verileri kaldırma özelliği bir birimde etkinleştirilmişse, bulut katmanlaması devre dışı bırakılmalıdır. 

**Notlar**
- Azure Dosya Eşitleme aracısını yüklemeden önce yinelenen verileri kaldırma yüklüyse, yinelenen verileri kaldırma ve bulut katmanlaması için aynı birimde yeniden başlatma gerekir.
- Bulut katmanlaması etkinleştirildikten sonra bir birimde yinelenen verileri kaldırma etkinleştirilmişse, ilk yinelenenleri kaldırma iyileştirme işi, zaten katmanlı olmayan ve bulut katmanlaması üzerinde aşağıdaki etkiye sahip olan dosyaları en iyi duruma getirir:
    - Boş alan ilkesi, heatmap kullanarak birimdeki boş alana göre dosyaları katmana devam edecektir.
    - Tarih ilkesi, dosyalara erişirken yinelenenleri kaldırma iyileştirme işi nedeniyle katmanlama için uygun olabilecek dosyaların katmanlamasını atlar.
- Devam eden yinelenenleri kaldırma iyileştirme işleri için, dosya zaten katmanlı değilse, tarih ilkesiyle bulut katmanlaması, yinelenen verileri kaldırma [Minimumfileagedays](/powershell/module/deduplication/set-dedupvolume) ayarı tarafından gecikilir. 
    - Örnek: MinimumFileAgeDays ayarı yedi gün, bulut katmanlama tarihi ilkesi ise 30 gün ise, tarih ilkesi dosyaları 37 gün sonra katman olarak alır.
    - Note: bir dosya Azure Dosya Eşitleme göre katmanlandıktan sonra, yinelenenleri kaldırma iyileştirme işi dosyayı atlar.
- Azure Dosya Eşitleme Aracısı yüklü Windows Server 2012 R2 çalıştıran bir sunucu Windows Server 2016 veya Windows Server 2019 ' ye yükseltilirse, yinelenen verileri kaldırma ve bulut katmanlaması desteği için aşağıdaki adımlar gerçekleştirilmelidir:  
    - Windows Server 2012 R2 için Azure Dosya Eşitleme aracısını kaldırın ve sunucuyu yeniden başlatın.
    - Yeni sunucu işletim sistemi sürümü için Azure Dosya Eşitleme aracısını indirin (Windows Server 2016 veya Windows Server 2019).
    - Azure Dosya Eşitleme aracısını yükleyip sunucuyu yeniden başlatın.  
    
    Note: sunucu üzerindeki Azure Dosya Eşitleme yapılandırma ayarları, aracı kaldırıldığında ve yeniden yüklendiğinde tutulur.

### <a name="distributed-file-system-dfs"></a>Dağıtılmış Dosya Sistemi (DFS)
Azure Dosya Eşitleme, DFS ad alanları (DFS-N) ve DFS Çoğaltma (DFS-R) ile birlikte çalışabilirliği destekler.

**DFS ad alanları (DFS-n)**: Azure dosya EŞITLEME, DFS-N sunucularında tam olarak desteklenir. Sunucu uç noktaları ve bulut uç noktası arasında veri eşitlemek için Azure Dosya Eşitleme aracısını bir veya daha fazla DFS-N üyesine yükleyebilirsiniz. Daha fazla bilgi için bkz. [DFS ad alanlarına genel bakış](/windows-server/storage/dfs-namespaces/dfs-overview).
 
**DFS çoğaltma (DFS-r)**: DFS-r ve Azure dosya eşitleme her ikisi de çoğaltma çözümleri olduğundan, çoğu durumda DFS-r 'yi Azure dosya eşitleme değiştirmeniz önerilir. DFS-R ve Azure Dosya Eşitleme kullanmak istediğiniz birkaç senaryo vardır:

- Bir DFS-R dağıtımından Azure Dosya Eşitleme dağıtımına geçiriyoruz. Daha fazla bilgi için, [DFS çoğaltma (DFS-R) dağıtımını Azure dosya eşitleme geçirme](file-sync-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync)konusuna bakın.
- Dosya verilerinizin bir kopyasına ihtiyaç duymayan her şirket içi sunucu, doğrudan internet 'e bağlanabilir.
- Şube sunucuları, Azure Dosya Eşitleme kullanmak istediğiniz tek bir hub sunucusu üzerinde verileri birleştirir.

Azure Dosya Eşitleme ve DFS-R 'nin yan yana çalışması için:

1. Azure Dosya Eşitleme bulut katmanlaması, DFS-R çoğaltılan klasörlerine sahip birimlerde devre dışı bırakılmalıdır.
2. Sunucu uç noktaları, DFS-R salt okuma çoğaltma klasörlerinde yapılandırılmamalıdır.

Daha fazla bilgi için bkz. [DFS çoğaltma genel bakış](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj127250(v=ws.11)).

### <a name="sysprep"></a>Sysprep
Azure Dosya Eşitleme aracısının yüklü olduğu bir sunucuda Sysprep kullanılması desteklenmez ve beklenmeyen sonuçlara yol açabilir. Aracı yüklemesi ve sunucu kaydı, sunucu görüntüsünü dağıttıktan ve Sysprep Mini Kurulumu tamamlandıktan sonra gerçekleşmelidir.

### <a name="windows-search"></a>Windows Search
Bulut katmanlaması bir sunucu uç noktasında etkinleştirilmişse, katmanlı dosyalar atlanır ve Windows Search tarafından dizine alınmamış olur. Katmanlı olmayan dosyalar doğru şekilde dizine alınır.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Diğer hiyerarşik depolama yönetimi (HSM) çözümleri
Azure Dosya Eşitleme ile başka bir HSM çözümü kullanılmamalıdır.

## <a name="performance-and-scalability"></a>Performans ve Ölçeklenebilirlik

Azure Dosya Eşitleme Aracısı Azure dosya paylaşımlarına bağlanan bir Windows Server makinesinde çalıştığından, etkin eşitleme performansı altyapınızdaki bir dizi etkene bağlıdır: Windows Server ve temel disk yapılandırması, sunucu ile Azure depolama arasındaki ağ bant genişliği, dosya boyutu, toplam veri kümesi boyutu ve veri kümesindeki etkinlik. Azure Dosya Eşitleme dosya düzeyinde çalıştığından Azure Dosya Eşitleme tabanlı bir çözümün performans özellikleri, saniye başına işlenen nesne (dosya ve dizin) sayısında daha fazla ölçülür.

Azure portal veya SMB kullanarak Azure dosya paylaşımında yapılan değişiklikler anında algılanır ve sunucu uç noktasındaki değişiklikler gibi çoğaltılmaz. Azure dosyalarında değişiklik bildirimleri veya günlük kaydı yoktur, bu nedenle dosyalar değiştirildiğinde bir eşitleme oturumu otomatik olarak başlatmak için bir yol yoktur. Windows Server 'da, Azure Dosya Eşitleme dosyalar değiştiğinde eşitleme oturumunu otomatik olarak başlatmak için [WINDOWS USN günlük kaydı](/windows/win32/fileio/change-journals) kullanır

Azure dosya paylaşımında yapılan değişiklikleri algılamak için Azure Dosya Eşitleme, değişiklik algılama işi adlı Zamanlanmış bir iş vardır. Değişiklik algılama işi dosya paylaşımındaki her dosyayı numaralandırır ve ardından bu dosyanın eşitleme sürümüyle karşılaştırır. Değişiklik algılama işi dosyaların değiştiğini belirlediğinde Azure Dosya Eşitleme bir eşitleme oturumu başlatır. Değişiklik algılama işi her 24 saatte bir başlatılır. Değişiklik algılama işi Azure dosya paylaşımındaki her dosyayı numaralandırarak çalıştığından, değişiklik algılama daha büyük ad uzaylarında daha küçük ad uzaylarından daha uzun sürer. Büyük ad alanları için, hangi dosyaların değiştirildiğini belirleyebilmek her 24 saatte bir daha uzun sürebilir.

Daha fazla bilgi için bkz. [Azure dosya eşitleme performans ölçümleri](../files/storage-files-scale-targets.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#azure-file-sync-performance-metrics) ve [Azure dosya eşitleme ölçek hedefleri](../files/storage-files-scale-targets.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#azure-file-sync-scale-targets)

## <a name="identity"></a>Kimlik
Azure Dosya Eşitleme, eşitleme ayarlamanın ötesinde özel bir kurulum olmadan standart AD tabanlı Kimliğiniz ile çalışır. Azure Dosya Eşitleme kullanırken genel beklentisi, en fazla erişimin Azure dosya paylaşımının yerine Azure Dosya Eşitleme önbelleğe alma sunucuları üzerinden gittiğine göre yapılır. Sunucu uç noktaları Windows Server 'da bulunduğundan ve Windows Server, AD ve Windows-stili ACL 'Leri uzun bir süredir desteklediğinden, depolama eşitleme hizmeti 'ne kayıtlı Windows dosya sunucularının etki alanına katılmış olmasını sağlamaya gerek olmadan hiçbir şey gerekmez. Azure Dosya Eşitleme, ACL 'Leri Azure dosya paylaşımındaki dosyalarda depolayacak ve tüm sunucu uç noktalarına çoğaltacaktır.

Doğrudan Azure dosya paylaşımında yapılan değişiklikler eşitleme grubundaki sunucu uç noktalarıyla eşitlenebilse de, dosya paylaşımınızda AD izinlerinizi doğrudan bulutta de zorlayabilmeniz da gerekebilir. Bunu yapmak için, Windows dosya sunucularınızın etki alanına katılmış olduğu gibi, depolama hesabınızı şirket içi AD 'nize katmalısınız. Depolama hesabınıza şirkete ait bir Active Directory katılma hakkında daha fazla bilgi edinmek için bkz. [Azure dosyaları Active Directory genel bakış](../files/storage-files-active-directory-overview.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json).

> [!Important]  
> Active Directory depolama hesabınıza katılan etki alanının Azure Dosya Eşitleme başarıyla dağıtılması için gerekli değildir. Bu, kullanıcılar Azure dosya paylaşımının doğrudan bağlanması sırasında Azure dosya paylaşımının şirket içi ACL 'Leri zorlayacağı kesinlikle isteğe bağlı bir adımdır.

## <a name="networking"></a>Ağ
Azure Dosya Eşitleme Aracısı, depolama eşitleme hizmetiniz ve Azure dosya paylaşımınızla iletişim kurarak, her ikisi de 443 numaralı bağlantı noktası üzerinden HTTPS kullanan Azure Dosya Eşitleme REST protokolünü ve FileREST protokolünü kullanarak iletişim kurar. SMB, Windows Server ve Azure dosya paylaşımınızla verileri karşıya yüklemek veya indirmek için hiçbir şekilde kullanılmaz. Çoğu kuruluş, 443 bağlantı noktası üzerinden HTTPS trafiğine izin vermediğinden, çoğu Web sitesini ziyaret etme gereksinimi olarak, Azure Dosya Eşitleme dağıtmak için özel ağ yapılandırması gerekmez.

Kuruluşunuzun ilkesi veya benzersiz düzenleme gereksinimlerine bağlı olarak, Azure ile daha kısıtlayıcı bir iletişim gerektirebilir ve bu nedenle Azure Dosya Eşitleme ağ yapılandırmanız için çeşitli mekanizmalar sağlar. Gereksinimlerinize göre şunları yapabilirsiniz:

- ExpressRoute veya Azure VPN 'niz üzerinden eşitleme ve karşıya dosya yükleme/indirme trafiğini tünel. 
- Azure dosyaları ve hizmet uç noktaları ve özel uç noktalar gibi Azure ağ özelliklerinden yararlanabilirsiniz.
- Ortamınızdaki proxy 'nizi desteklemek için Azure Dosya Eşitleme yapılandırın.
- Azure Dosya Eşitleme ağ etkinliğini kısıtlama.

Azure Dosya Eşitleme ve ağ oluşturma hakkında daha fazla bilgi için bkz. [Azure dosya eşitleme ağ değerlendirmeleri](file-sync-networking-overview.md).

## <a name="encryption"></a>Şifreleme
Azure Dosya Eşitleme kullanırken göz önünde bulundurmanız gereken üç farklı şifreleme katmanı vardır: Windows Server 'ın Rest deposunda şifreleme, Azure Dosya Eşitleme Aracısı ile Azure arasında geçiş sırasında şifreleme ve Azure dosya paylaşımındaki verilerinizin geri kalanında şifreleme. 

### <a name="windows-server-encryption-at-rest"></a>Bekleyen Windows Server şifrelemesi 
Windows Server 'daki verileri şifrelemek için genellikle Azure Dosya Eşitleme: dosya sistemi ve dosyaya yazılan tüm verilerin şifrelenme ve dosya biçimi içinde şifreleme gibi dosya sisteminin altında şifreleme olmak üzere iki strateji vardır. Bu yöntemler birbirini dışlamalı değildir; şifreleme amacı farklı olduğundan, istenirse birlikte kullanılabilir.

Dosya sisteminin altında şifreleme sağlamak için, Windows Server, BitLocker gelen kutusu sağlar. BitLocker Azure Dosya Eşitleme için tamamen saydamdır. BitLocker gibi bir şifreleme mekanizması kullanmanın birincil nedeni, diskleri çalmaya ve yetkisiz bir işletim sisteminin verilerinize yetkisiz okuma/yazma işlemleri gerçekleştirmesini önlemek için, şirket içi veri merkezinizden verileri fiziksel olarak alma işlemini önlemektir. BitLocker hakkında daha fazla bilgi edinmek için bkz. [BitLocker genel bakış](/windows/security/information-protection/bitlocker/bitlocker-overview).

BitLocker 'a benzer şekilde çalışan üçüncü taraf ürünleri, NTFS biriminin altına oturduklarında, benzer şekilde Azure Dosya Eşitleme ile tamamen saydam şekilde çalışır. 

Verileri şifrelemek için diğer Main yöntemi, uygulamanın dosyayı kaydettiğinde dosyanın veri akışını şifreleyeyöneliktir. Bazı uygulamalar bunu yerel olarak yapamayabilir, ancak bu durum genellikle böyle değildir. Dosyanın veri akışını şifrelemek için bir yöntem örneği Azure Information Protection (AıP)/Azure Rights Management Services (Azure RMS)/Active Directory RMS 'dir. AıP/RMS gibi bir şifreleme mekanizması kullanmanın birincil nedeni, verileri bir flash sürücü gibi alternatif konumlara kopyalayan veya yetkisiz bir kişiye e-posta ile göndererek dosya paylaşımınızdan veri alımını önlemektir. Dosya biçiminin bir parçası olarak bir dosyanın veri akışı şifrelendiğinde, bu dosya Azure dosya paylaşımında şifrelenmeye devam edecektir. 

Azure Dosya Eşitleme, NTFS şifreli dosya sistemi (NTFS EFS) veya dosya sisteminin üzerine, ancak dosyanın veri akışının altında yer alan üçüncü taraf şifreleme çözümleriyle birlikte çalışmaz. 

### <a name="encryption-in-transit"></a>Aktarım sırasında şifreleme

> [!NOTE]
> Azure Dosya Eşitleme hizmeti, 1 Ağustos 2020 tarihinde TLS 1.0 ve 1,1 desteğini kaldırır. Desteklenen tüm Azure Dosya Eşitleme Aracısı sürümleri zaten TLS 1.2 'yi varsayılan olarak kullanıyor. TLS 1.2 sunucunuzda devre dışı bırakılmışsa veya bir ara sunucu kullanılıyorsa TLS 'nin önceki bir sürümünün kullanılması gerçekleşebilir. Ara sunucu kullanıyorsanız, ara sunucu yapılandırmasını kontrol etmenizi öneririz. 5/1/2020 sonra eklenen Azure Dosya Eşitleme hizmet bölgeleri, yalnızca TLS 1.2 'ı ve TLS 1.0 desteğini destekler ve 1,1, 1 Ağustos, 2020 tarihinde mevcut bölgelerden kaldırılır.  Daha fazla bilgi için bkz. [sorun giderme kılavuzu](file-sync-troubleshoot.md#tls-12-required-for-azure-file-sync).

Azure Dosya Eşitleme Aracısı, her ikisi de 443 bağlantı noktası üzerinden HTTPS kullanan Azure Dosya Eşitleme REST protokolünü ve en iyi dosya protokolünü kullanarak depolama eşitleme hizmetiniz ve Azure dosya paylaşımınızla iletişim kurar. Azure Dosya Eşitleme, HTTP üzerinden şifrelenmemiş istekler göndermez. 

Azure depolama hesapları, varsayılan olarak etkin olan aktarım sırasında şifreleme gerektirmek için bir anahtar içerir. Depolama hesabı düzeyindeki anahtar devre dışı bırakılmış olsa da, Azure dosya paylaşımlarınıza şifrelenmemiş bağlantıların mümkün olduğu anlamına gelir, Azure Dosya Eşitleme yalnızca dosya paylaşımınıza erişmek için şifrelenmiş kanallar kullanacaktır.

Depolama hesabı için bir aktarımda şifrelemeyi devre dışı bırakmak için birincil neden, Windows Server 2008 R2 veya daha eski Linux dağıtımı gibi eski bir işletim sisteminde çalıştırılması gereken eski bir uygulamayı desteklemek, bir Azure dosya paylaşımıyla doğrudan iletişim kurmanın olması gerekir. Eski uygulama dosya paylaşımının Windows Server önbelleği ile iletişim alıyorsa, bu ayarın yerine geçmek hiçbir etkiye sahip olmayacaktır. 

Geçiş sırasında verilerin şifrelenmesini güvence altına almanız önemle önerilir.

Aktarım sırasında şifreleme hakkında daha fazla bilgi için bkz. [Azure depolama 'da güvenli aktarım gerektirme](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="azure-file-share-encryption-at-rest"></a>Rest 'de Azure dosya paylaşma şifrelemesi
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Depolama katmanları
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Standart dosya paylaşımlarının 100 TiB 'ye kadar yayılmasını sağlar

Varsayılan olarak, standart dosya paylaşımları yalnızca 5 TiB 'ye yayılabilir, ancak paylaşım limitini 100 TiB olarak artırabilirsiniz. Paylaşım sınırınızı nasıl artıracağınızı öğrenmek için bkz. [büyük dosya paylaşımlarını etkinleştirme ve oluşturma](../files/storage-files-how-to-create-large-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json).


#### <a name="regional-availability"></a>Bölgesel kullanılabilirlik
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Azure dosya eşitleme bölgesi kullanılabilirliği

Bölgesel kullanılabilirlik için bkz. [bölgelere göre kullanılabilir ürünler](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

Aşağıdaki bölgeler Azure Dosya Eşitleme kullanabilmeniz için Azure Storage 'a erişim istemeniz gerekir:

- Güney Fransa
- Güney Afrika - Batı
- BAE Orta

Bu bölgeler için erişim istemek üzere [Bu belgedeki](https://azure.microsoft.com/global-infrastructure/geographies/)işlemi izleyin.

## <a name="redundancy"></a>Yedeklilik
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> Coğrafi olarak yedekli ve coğrafi olarak yedekli depolama, ikincil bölgeye depolama alanı el ile yük devretme özelliğine sahiptir. Daha fazla veri kaybı nedeniyle Azure Dosya Eşitleme kullanırken bunu bir olağanüstü durum dışında yapmanızı öneririz. Depolamanın el ile yük devretmesini başlatmak istediğiniz bir olağanüstü durum durumunda, ikincil uç noktayla Eşitlemeyi sürdürmek için Azure Dosya Eşitleme almak üzere Microsoft ile bir destek talebi açmanız gerekir.

## <a name="migration"></a>Geçiş
Mevcut bir Windows dosya sunucusu 2012R2 veya daha yeni bir sürümüne sahipseniz, verileri yeni bir sunucuya taşımaya gerek olmadan doğrudan yerinde Azure Dosya Eşitleme. Azure Dosya Eşitleme benimsemenin bir parçası olarak yeni bir Windows dosya sunucusuna geçiş yapmak istiyorsanız veya verileriniz Şu anda ağa bağlı depolama (NAS) üzerinde bulunuyorsa, bu verilerle Azure Dosya Eşitleme kullanmak için birkaç olası geçiş yaklaşımı vardır. Hangi geçiş yaklaşımını seçmelisiniz, verilerinizin Şu anda bulunduğu yere bağlıdır. 

Senaryonuza yönelik ayrıntılı kılavuz bulabileceğiniz [Azure dosya eşitleme ve Azure dosya paylaşma geçişine genel bakış](../files/storage-files-migration-overview.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) makalesine göz atın.

## <a name="antivirus"></a>Virüsten Koruma
Virüsten koruma, bilinen kötü amaçlı kod için dosyaları tarayarak çalıştığından, bir virüsten koruma ürünü katmanlı dosyaların geri çekmesine neden olabilir ve bu da yüksek çıkış ücretleri elde edilir. Azure Dosya Eşitleme aracısının 4,0 ve üzeri sürümlerinde katmanlı dosyalar, güvenli Windows özniteliği FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS ayarlanmış olmalıdır. Bu öznitelik kümesi ile dosya okumayı atlamak üzere çözümlerini nasıl yapılandıracağınızı öğrenmek için yazılım satıcınıza danışmanız önerilir (birçok şey otomatik olarak yapılır). 

Microsoft 'un Şirket içi virüsten koruma çözümleri, Windows Defender ve System Center Endpoint Protection (SCEP), her ikisi de bu özniteliği ayarlanmış dosyaları okumayı otomatik olarak atlar. Bunları test ettik ve bir ikincil sorun tanımladı: var olan bir eşitleme grubuna sunucu eklediğinizde, yeni sunucuda 800 bayttan daha küçük dosyalar geri çekilir (indirilir). Bu dosyalar yeni sunucuda kalacak ve katmanlama boyut gereksinimini (>64 KB) karşılamadığında katmanlanmayacak.

> [!Note]  
> Virüsten koruma satıcıları, Microsoft Indirme Merkezi ' nde indirileceği [Azure dosya eşitleme virüsten koruma Uyumluluk testi paketini](https://www.microsoft.com/download/details.aspx?id=58322)kullanarak ürün ve Azure dosya eşitleme arasındaki uyumluluğun denetlemesini denetleyebilir.

## <a name="backup"></a>Backup 
Bulut katmanlaması etkinleştirilirse, sunucu uç noktasını veya sunucu uç noktasının bulunduğu VM 'yi doğrudan yedekleyen çözümler kullanılmamalıdır. Bulut katmanlaması, Azure dosya paylaşımınızda bulunan tam veri kümesi ile sunucu uç noktasında yalnızca verilerinizin bir alt kümesinin depolanmasına neden olur. Kullanılan yedekleme çözümüne bağlı olarak, katmanlı dosyalar atlanır ve yedeklenmez (FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS özniteliği ayarlanmış olduklarından) veya diske geri çekilir ve bu da yüksek çıkış ücretlerine yol açar. Azure dosya paylaşımının doğrudan yedeklenmesi için bir bulut yedekleme çözümü kullanmanızı öneririz. Daha fazla bilgi için bkz. [Azure dosya paylaşımı yedeklemesi hakkında](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) veya Azure dosya paylaşımlarının yedeklenmesini desteklediğini öğrenmek için yedekleme sağlayıcınızla iletişim kurun.

Şirket içi yedekleme çözümünü kullanmayı tercih ediyorsanız, yedeklemelerin bulut katmanlaması devre dışı bırakılmış olan eşitleme grubundaki bir sunucuda gerçekleştirilmesi gerekir. Geri yükleme gerçekleştirirken birim düzeyinde veya dosya düzeyinde geri yükleme seçeneklerini kullanın. Dosya düzeyi geri yükleme seçeneği kullanılarak geri yüklenen dosyalar, eşitleme grubundaki tüm uç noktalarla eşitlenir ve mevcut dosyalar yedekten geri yüklenen sürümle birlikte değişir.  Birim düzeyinde geri yüklemeler, Azure dosya paylaşımındaki veya diğer sunucu uç noktalarında yeni dosya sürümlerinin yerini olmayacaktır.

> [!WARNING]
> Kaynak veya hedef sunucuda çalışan bir Azure Dosya Eşitleme aracısında Robocopy/B kullanmanız gerekiyorsa lütfen Azure Dosya Eşitleme Aracı sürümü v 12.0 veya üzeri sürümüne yükseltin. Sanal 12.0 'den küçük aracı sürümleriyle Robocopy/B kullanılması, kopyalama sırasında katmanlı dosyaların bozulmasına yol açabilir.

> [!Note]  
> Çıplak (BMR) geri yükleme beklenmeyen sonuçlara neden olabilir ve şu anda desteklenmemektedir.

> [!Note]  
> Azure Dosya Eşitleme aracısının 9. sürümünde, VSS anlık görüntüleri (önceki sürümler dahil), artık bulut katmanlaması etkinleştirilmiş birimlerde desteklenmektedir. Ancak, PowerShell aracılığıyla önceki sürüm uyumluluğunu etkinleştirmeniz gerekir. [Nasıl yapılacağını öğrenin](file-sync-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service).

## <a name="data-classification"></a>Veri Sınıflandırması
Veri sınıflandırma yazılımınız yüklüyse, bulut Katmanlamasının etkinleştirilmesi iki nedenden dolayı maliyeti artırabilir:

1. Bulut katmanlama özelliği etkinken, en yoğun dosyalarınız yerel olarak önbelleğe alınır ve en güncel dosyalar buluttaki Azure dosya paylaşımında katmanlıdır. Veri sınıflandırmanız dosya paylaşımındaki tüm dosyaları düzenli olarak taraıyorsa, buluta katmanlı dosyalar her tarandığında geri alınmalıdır. 

2. Veri sınıflandırma yazılımı bir dosyanın veri akışındaki meta verileri kullanıyorsa, yazılımın sınıflandırmasını bilmesi için dosyanın tam olarak geri çekilmiş olması gerekir. 

Bu, hem geri çekme sayısı hem de geri çekilen veri miktarı arttıkça maliyetleri artırabilir.

## <a name="azure-file-sync-agent-update-policy"></a>Azure Dosya Eşitleme aracısı güncelleştirme ilkesi
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Sonraki adımlar
* [Güvenlik duvarını ve proxy ayarlarını değerlendirin](file-sync-firewall-and-proxy.md)
* [Azure Dosyaları’nı dağıtma](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)
* [Azure Dosya Eşitleme’yi dağıtma](file-sync-deployment-guide.md)
* [Azure Dosya Eşitleme’yi izleme](file-sync-monitoring.md)
