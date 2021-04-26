---
title: Azure Dosya Eşitleme bulut katmanlama ilkelerini seçin | Microsoft Docs
description: Azure Dosya Eşitleme bulut katmanlama ilkeleri seçerken göz önünde bulundurmanız gerekenler hakkında ayrıntılı bilgi.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f0bf41e1a847335a99b3e8f2e9ecbac504c3179e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797391"
---
# <a name="choose-cloud-tiering-policies"></a>Bulut katmanlama ilkelerini seçin

Bu makale, bulut katmanlama ilkelerini seçen ve düzenleyen kullanıcılar için rehberlik sağlar. Bu makaleyi okumadan önce, bulut katmanlama işlevinin nasıl çalıştığını anladığınızdan emin olun. Bulut katmanlama temelleri için bkz. [bulut katmanlaması Azure dosya eşitleme anlama](file-sync-cloud-tiering-overview.md). Örnek ile bulut katmanlama ilkelerinin derinlemesine bir açıklaması için bkz. [Azure dosya eşitleme bulut katmanlama ilkeleri](file-sync-cloud-tiering-policy.md).

## <a name="limitations"></a>Sınırlamalar
- Bulut katmanlaması, Windows sistem biriminde desteklenmez.

- Birim düzeyi FSRM kotasına sahipseniz, hala bulut katmanlaması sağlayabilirsiniz. Bir FSRM kotası ayarlandıktan sonra, çağrılan boş alan sorgu API 'Leri, kota ayarlarına göre birimdeki boş alanı otomatik olarak bildirir. 

### <a name="minimum-file-size-for-a-file-to-tier"></a>Bir dosya için en düşük dosya boyutu katmanı

Aracı sürümleri 9 ve daha yeni sürümlerde, bir dosyanın katmana en küçük dosya boyutu dosya sistemi kümesi boyutunu temel alır. Bulut katmanlaması için uygun olan minimum dosya boyutu, 2x küme boyutu ve en az 8 KB ile hesaplanır. Aşağıdaki tabloda, birim kümesi boyutuna bağlı olarak katmanlanılabilecek minimum dosya boyutları gösterilmektedir:

|Birim kümesi boyutu (bayt) |Bu boyut veya daha büyük dosyalar katmanlı olabilir  |
|----------------------------|---------|
|4 KB veya daha küçük (4096)      | 8 KB    |
|8 KB (8192)                 | 16 KB   |
|16 KB (16384)               | 32 KB   |
|32 KB (32768)               | 64 KB   |
|64 KB (65536)    | 128 KB  |
|128 KB (131072) | 256 KB |
|256 KB (262144) | 512 KB |
|512 KB (524288) | 1 MB |
|1 MB (1048576) | 2 MB |
|2 MB (2097152) | 4 MB |

2 MB 'a kadar olan küme boyutları Azure Dosya Eşitleme Agent sürümü 12 ile desteklenir, ancak daha büyük boyutlarda bulut katmanlaması çalışmaz.

Windows tarafından kullanılan tüm dosya sistemleri, sabit diskinizi küme boyutuna (ayırma birimi boyutu olarak da bilinir) göre düzenleyin. Küme boyutu bir dosyayı tutmak için kullanılabilecek en küçük disk alanı miktarını temsil eder. Dosya boyutları küme boyutunun hatta daha fazla olmadığında, dosyayı küme boyutunun bir sonraki katı olarak tutmak için ek alan kullanılmalıdır.

Azure Dosya Eşitleme, Windows Server 2012 R2 ve daha yeni bir sürümü olan NTFS birimlerinde desteklenir. Aşağıdaki tabloda, Windows Server 2019 ile yeni bir NTFS birimi oluşturduğunuzda varsayılan küme boyutları açıklanmaktadır.

|Birim boyutu    |Windows Server 2019             |
|---------------|--------------------------------|
|7 MB – 16 TB   | 4 KB                |
|16 TB – 32 TB   | 8 KB                |
|32TB – 64 TB   | 16 KB               |
|64TB – 128 TB  | 32 KB               |
|128TB – 256 TB | 64 KB (önceki en fazla) |
|256 TB – 512 TB| 128 KB              |
|512 TB – 1 PB  | 256 KB              |
|1 PB – 2 PB    | 512 KB              |
|2 TB – 4 PB    | 1024 KB             |
|4 TB – 8 TB    | 2048 KB (en fazla boyut)  |
|> 8 TB         | desteklenmiyor       |

Birim oluşturulduktan sonra, birimi farklı bir küme boyutuyla el ile biçimlendirmiş olursunuz. Biriminiz Windows 'un eski bir sürümünden farklıysa, varsayılan küme boyutları da farklı olabilir. [Bu makalede varsayılan küme boyutları hakkında daha ayrıntılı bilgi bulunur.](https://support.microsoft.com/help/140365/default-cluster-size-for-ntfs-fat-and-exfat) 4 KB 'tan daha küçük bir küme boyutu seçseniz bile, katmanlı olabilecek en küçük dosya boyutu olarak 8 KB 'lik bir sınır de geçerlidir. (Teknik açıdan 2x küme boyutu 8 KB 'tan daha az bir değer de olabilir.)

Mutlak minimumın nedeni, NTFS 'nin son derece küçük dosyaları-1 KB-4 KB boyutlu dosyaları depoladığı şekilde bulunur. Birimin diğer parametrelerine bağlı olarak, küçük dosyaların diskte bulunan bir kümede depolanması mümkündür. Bu tür dosyaları doğrudan birimin ana dosya tablosunda veya "MFT kaydında" depolamak daha verimli olabilir. Bulut katmanlama yeniden ayrıştırma noktası her zaman diskte depolanır ve tam olarak bir küme alır. Bu küçük dosyaların katmanlaması, hiçbir boşluk tasarrufları olmadan sona geçirebilir. Olağanüstü durumlar, bulut katmanlaması etkinken daha fazla alan kullanmaya da daha fazla zaman açamaz. Bu şekilde korumak için, bulut katmanlaması katman olacak bir dosyanın en küçük boyutu 4 KB veya daha küçük bir küme boyutunda 8 KB 'tır. 

## <a name="selecting-your-initial-policies"></a>İlk ilkelerinizi seçme

Genellikle, bir sunucu uç noktasında bulut katmanlamayı etkinleştirdiğinizde, her bir sunucu uç noktası için bir yerel sanal sürücü oluşturmalısınız. Sunucu uç noktasını yalıtmak, bulut katmanlaması 'nın nasıl çalıştığını anlamayı ve ilkeleri uygun şekilde ayarlamayı kolaylaştırır. Ancak, aynı sürücüde birden çok sunucu uç noktasına sahip olsanız bile Azure Dosya Eşitleme, Ayrıntılar için [Yerel birimde birden çok sunucu uç noktası](file-sync-cloud-tiering-policy.md#multiple-server-endpoints-on-a-local-volume) bölümüne bakın. Ayrıca, bulut katmanlamayı ilk defa etkinleştirdiğinizde, tarih ilkesi devre dışı ve birim boş alan ilkesini %10 ' dan %20 ' ye kadar bir yere tutmanızı öneririz. Çoğu dosya sunucusu birimi için genellikle en iyi seçenek olan %20 birim boş alanı.

Basitlik sağlamak ve öğelerin nasıl katmanlı olacağını net bir şekilde anlamak için öncelikle birim boş alan ilkenizi ayarlamanıza ve gerekmedikçe Tarih ilkenizi devre dışı tutmanızı öneririz. Çoğu müşteri, yerel önbelleği mümkün olduğunca çok sık erişimli dosya ile dolduracak ve REST 'i buluta katman açısından değerli bulduğu için bunu yapmanızı öneririz. Ancak, yerel disk alanını önceden açmak istiyorsanız tarih ilkesi yararlı olabilir ve Tarih ilkenizde belirtilen gün sayısının yerel olarak tutulması gerekmiyorsa sunucu uç noktasının eriştiği dosyaları bilirsiniz. Tarih ilkesini ayarlamak, dosyalarını daha fazla önbelleğe almak için aynı birimdeki diğer uç noktalar için değerli yerel disk kapasitesini serbest bırakır.

İlkelerinizi ayarladıktan sonra çıkış izleyin ve her iki ilkeyi uygun şekilde ayarlayın. Azure Izleyici 'de uygulama ölçümlerine göre **bulut katmanlama geri çağırma boyutuna** ve **bulut katmanlama geri çağırma boyutuna** özellikle bakmaya önerilir. Çıkışı izlemeyi öğrenmek için bkz. [bulut katmanlaması izleme](file-sync-monitor-cloud-tiering.md).

## <a name="adjusting-your-policies"></a>İlkelerinizi ayarlama

Azure 'dan sürekli geri çekilen dosya miktarı istediğinizden daha büyükse, bunları yerel sunucu birimine kaydetmek için yeterli alana sahip olduğunuz daha fazla dosya olabilir. Mümkünse yerel birim boyutunuzu artırın ve/veya birim boş alan ilkesi yüzdesini küçük artışlarla azaltın. Birimde boş alan yüzdesi çok fazla azaltılarak negatif sonuçlara da sahip olabilir. Veri kümenizdeki daha yüksek dalgalanma daha fazla boş alan gerektirir ve yeni dosyalar için "soğuk" dosyaları geri çekiyor. Katmanlama, bir saate kadar gecikme süresine sahiptir ve işlem zamanına ihtiyaç duyuyor ve bu nedenle biriminizde her zaman yeterli boş alan olması gerekir.

Verilerin yerel olarak tutulması, Azure 'dan daha az sayıda dosya geri çekeceği ve aynı zamanda kendi maliyetiyle birlikte gelen daha büyük miktarda şirket içi depolama alanı gerektirdiğinden, çıkış maliyetlerinin düşük olması anlamına gelir. 

Birim boş alan ilkesi ayarlanırken, yerel tutmanız gereken veri miktarı şu etkenlere göre belirlenir: bant genişliğiniz, veri kümenizin erişim deseninin ve bütçeniz. Düşük bant genişliğine sahip bir bağlantıyla, kullanıcılar için en düşük gecikme sağlamaya olanak sağlamak üzere daha fazla yerel veri istemeniz gerekebilir. Aksi takdirde, belirli bir süre boyunca dalgalanma oranını temel alabilirsiniz. Örnek olarak, 1 TB 'lık veri kümenizin %10 ' u değişiklik olduğunu veya her ay etkin bir şekilde erişildiğini biliyorsanız, dosyaları sık geri çekmediğinizde 100 GB yerel tutmak isteyebilirsiniz. Biriminiz 2 TB ise, %5 ' i (veya 100 GB) yerel tutmak isteyeceksiniz, yani kalan %95, birim boş alanınız yüzdesidir. Bununla birlikte, daha yüksek değişim dönemleri için bir arabellek eklemeniz gerekir: diğer bir deyişle daha büyük bir birimde boş alan yüzdesi ile başlayıp daha sonra gerekirse.

## <a name="standard-operating-procedures"></a>Standart işletim yordamları

- Azure dosyalarına Azure Dosya Eşitleme aracılığıyla ilk kez geçiş yaparken, bulut katmanlaması ilk karşıya yüklemeye bağımlıdır
- Bulut katmanlaması, her 60 dakikada bir birim boş alanı ve Tarih ilkesiyle uyumluluğu denetler
- Dosyaları geçirirken Robocopy üzerinde/LFSD anahtarını kullanmak, dosyaların ilk karşıya yükleme sırasında alan oluşturmak için eşitlenmesini ve bulut katmanlamasını sağlar 
- Bir ısı haritasını oluşturulmadan önce katmanlama gerçekleşirse, dosyalar son değiştirme zaman damgasına göre katmanlanacaktır

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Dosya Eşitleme dağıtımı planlama](file-sync-planning.md)
