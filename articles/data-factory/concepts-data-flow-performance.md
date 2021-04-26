---
title: Veri akışı performansını ve ayarlama kılavuzunu eşleme
description: Azure Data Factory veri akışlarını eşleme performansını etkileyen anahtar faktörleri hakkında bilgi edinin.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 04/10/2021
ms.openlocfilehash: cee7993116e746c7b827faaf94724033501f1318
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309059"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Veri akışlarını eşleme performansı ve ayarlama Kılavuzu

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory veri akışlarını eşleme, veri dönüştürmelerini bir ölçekte tasarlamak ve çalıştırmak için kod içermeyen bir arabirim sağlar. Veri akışlarını eşleme konusunda bilgi sahibi değilseniz, [eşleme veri akışına genel bakış](concepts-data-flow-overview.md)bölümüne bakın. Bu makalede, performans kıyaslamalarınızı karşılamak üzere veri akışlarınızı ayarlama ve iyileştirme için çeşitli yollar vurgulanmaktadır.

Veri akışları ile verileri dönüştüren bazı örnek zamanlamaların gösterildiği görmek için aşağıdaki videoyu izleyin.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="testing-data-flow-logic"></a>Veri akışı mantığını test etme

ADF UX 'den veri akışları tasarlarken ve test ederken, hata ayıklama modu canlı bir Spark kümesine karşı etkileşimli olarak test etmenize olanak tanır. Bu sayede, verilerin önizlemesine ve veri akışlarınızı bir kümenin ısınma süresi olmadan yürütmeniz gerekir. Daha fazla bilgi için bkz. [hata ayıklama modu](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flow-performance"></a>Veri akışı performansını izleme

Hata ayıklama modunu kullanarak dönüştürme mantığınızı doğruladıktan sonra, veri akışınızı bir işlem hattındaki etkinlik olarak uçtan uca çalıştırın. Veri akışları, [veri akışını Yürüt etkinliğinin](control-flow-execute-data-flow-activity.md)kullanıldığı bir işlem hattında çalışır. Veri akışı etkinliği, dönüştürme mantığının ayrıntılı bir yürütme planını ve performans profilini görüntüleyen diğer Azure Data Factory etkinlikleriyle karşılaştırıldığında benzersiz bir izleme deneyimine sahiptir. Bir veri akışının ayrıntılı izleme bilgilerini görüntülemek için, bir işlem hattının çıkış çıkışını Çalıştır simgesine tıklayın. Daha fazla bilgi için bkz. [eşleme veri akışlarını izleme](concepts-data-flow-monitoring.md).

![Veri akışı Izleyicisi](media/data-flow/monitoring-details.png "Veri akışı Izleyicisi 2")

Veri akışı performansını izlerken, göz atmak için dört olası performans sorunu vardır:

* Küme başlangıç zamanı
* Bir kaynaktan okuma
* Dönüştürme saati
* Bir havuza yazma 

![Veri Akışını İzleme](media/data-flow/monitoring-performance.png "Veri akışı Izleyicisi 3")

Küme başlangıç zamanı, bir Apache Spark kümesi çalıştırmak için gereken süredir. Bu değer, izleme ekranının sağ üst köşesinde bulunur. Veri akışları, her işin yalıtılmış bir küme kullandığı tam zamanında bir modelde çalışır. Bu başlangıç zamanı genellikle 3-5 dakika sürer. Sıralı işler için, bu değer bir yaşam süresi değeri etkinleştirilerek azaltılabilir. Daha fazla bilgi için bkz. [Azure Integration Runtime iyileştirme](#ir).

Veri akışları, mümkün olduğunca hızlı bir şekilde gerçekleştirmek için iş mantığınızı yeniden sipariş eden ve çalıştıran bir Spark İyileştiriciyi kullanır. Veri akışınızın yazdığı her bir havuz için, izleme çıktısı her bir dönüştürme aşamasının süresini ve verilerin havuza yazılması için geçen süreyi listeler. En büyük olan süre büyük olasılıkla veri akışınız için performans sorununa neden olur. En büyük değeri alan dönüştürme aşaması bir kaynak içeriyorsa, okuma zamandan daha iyi bir şekilde bakmak isteyebilirsiniz. Bir dönüşüm uzun sürüyorsa, tümleştirme çalışma zamanının boyutunu yeniden bölümlendirmeniz veya artırmanız gerekebilir. Havuz işleme süresi büyükse, veritabanınızı ölçeklendirmeniz veya tek bir dosyaya çıktıları doğrulamanız gerekebilir.

Veri akışınız için performans sorunlarını tanımladıktan sonra, performansı artırmak için aşağıdaki iyileştirmeler stratejilerini kullanın.

## <a name="optimize-tab"></a>İyileştirme sekmesi

**Optimizasyon** sekmesi, Spark kümesinin bölümleme şemasını yapılandırmak için ayarlar içerir. Bu sekme, veri akışının her dönüşümünde bulunur ve dönüştürme tamamlandıktan **sonra** verileri yeniden bölümlemek isteyip istemediğinizi belirtir. Bölümlemenin ayarlanması, verilerin işlem düğümleri genelinde dağıtılması üzerinde denetim sağlar ve genel veri akışı performansından hem olumlu hem de olumsuz etkileri olabilir.

![Ekran görüntüsü, Bölüm seçeneği, bölüm türü ve bölüm sayısı dahil en Iyileştirme sekmesini gösterir.](media/data-flow/optimize.png)

Varsayılan olarak, *geçerli bölümleme öğesini kullanın* ve bu, dönüşümün geçerli çıkış bölümlemesini Azure Data Factory. Yeniden bölümleme verileri zaman alıyorsa, Çoğu senaryoda *geçerli bölümleme kullanılması* önerilir. Verilerinizi yeniden bölümlemek isteyebileceğiniz senaryolar, verilerinizi önemli ölçüde eğmeden veya bir SQL DB 'de kaynak bölümlendirme kullanırken yer alan toplamalar ve birleşimlerden sonra dahil olabilir.

Herhangi bir dönüşümdeki Bölümlendirmeyi değiştirmek için, **optimizasyon** sekmesini seçin ve **bölümlendirme** radyo düğmesini seçin. Bölümlemeye yönelik bir dizi seçenek sunulur. Bölümlendirme en iyi yöntemi, veri birimleriniz, aday anahtarlarınız, null değerler ve kardinalite temelinde farklılık gösterir. 

> [!IMPORTANT]
> Tek bölüm dağıtılan tüm verileri tek bir bölümde birleştirir. Bu, tüm aşağı akış dönüşümünü ve yazmaları önemli ölçüde etkileyerek de çok yavaş bir işlemdir. Azure Data Factory, bunu yapmak için açık bir iş nedeni olmadıkça, bu seçeneğin kullanılmasına ilişkin önemli ölçüde tavsiye edilir.

Aşağıdaki bölümlendirme seçenekleri her dönüşümde kullanılabilir:

### <a name="round-robin"></a>Hepsini bir kez deneme 

Hepsini bir kez deneme, verileri bölümler arasında eşit olarak dağıtır. Katı ve akıllı bölümlendirme stratejisi uygulamak için iyi bir ana aday olmadığında hepsini bir kez deneme kullanın. Fiziksel bölüm sayısını ayarlayabilirsiniz.

### <a name="hash"></a>Karma

Azure Data Factory, benzer değerlere sahip satırlar aynı bölüme düşecek şekilde Tekdüzen bölümler oluşturmak için bir sütun karması üretir. Karma seçeneğini kullandığınızda olası bölüm eğsınaması için test edin. Fiziksel bölüm sayısını ayarlayabilirsiniz.

### <a name="dynamic-range"></a>Dinamik Aralık

Dinamik Aralık, sağladığınız sütunlara veya ifadelere göre Spark dinamik aralıklarını kullanır. Fiziksel bölüm sayısını ayarlayabilirsiniz. 

### <a name="fixed-range"></a>Sabit Aralık

Bölümlenmiş veri sütunlarınızın içindeki değerler için sabit bir Aralık sağlayan bir ifade oluşturun. Bölüm eğriliğini önlemek için, bu seçeneği kullanmadan önce verilerinizin iyi şekilde anlaşılmasını sağlayabilirsiniz. İfade için girdiğiniz değerler, bölüm işlevinin bir parçası olarak kullanılır. Fiziksel bölüm sayısını ayarlayabilirsiniz.

### <a name="key"></a>Anahtar

Verilerinizin önem düzeyini iyi anlamak istiyorsanız anahtar bölümleme iyi bir strateji olabilir. Anahtar bölümleme, sütuninizdeki her benzersiz değer için bölümler oluşturur. Bu sayı, verilerdeki benzersiz değerleri temel aldığı için bölüm sayısını ayarlayamazsınız.

> [!TIP]
> Veri reshuffles bölümlendirme şemasını el ile ayarlama ve Spark iyileştiricinin avantajlarını fark edebilir. En iyi uygulama, gerekmedikçe Bölümlendirmeyi el ile ayarlamanıza gerek kalmaz.

## <a name="logging-level"></a>Günlük kaydı düzeyi

Veri akışı etkinliklerinizin her işlem hattı yürütmesinin tüm ayrıntılı telemetri günlüklerini tam olarak günlüğe yazmasına gerek yoksa, isteğe bağlı olarak günlük düzeyini "temel" veya "hiçbiri" olarak ayarlayabilirsiniz. Veri akışlarınızı "ayrıntılı" modda yürütürken (varsayılan), veri dönüşümünüzün her bir bölüm düzeyinde, ADF 'yi tam olarak günlüğe kaydetmek için istek yapabilirsiniz. Bu, pahalı bir işlem olabilir, bu nedenle yalnızca sorun giderme sırasında ayrıntılandırma, genel veri akışınızı ve işlem hattı performansınızı iyileştirecek şekilde etkinleştiriliyor. "Temel" modu yalnızca bir süre Özeti sağlamaları durumunda yalnızca dönüştürme sürelerini günlüğe kaydeder.

![Günlük kaydı düzeyi](media/data-flow/logging.png "Günlüğe kaydetme düzeyini ayarla")

## <a name="optimizing-the-azure-integration-runtime"></a><a name="ir"></a> Azure Integration Runtime iyileştirme

Veri akışları, çalışma zamanında Esnetme yapan Spark kümelerinde çalışır. Kullanılan kümenin yapılandırması, etkinliğin tümleştirme çalışma zamanı 'nda (IR) tanımlanmıştır. Tümleştirme çalışma zamanını tanımlarken yapmanız gereken üç performans konusu vardır: küme türü, küme boyutu ve yaşam süresi.

Integration Runtime oluşturma hakkında daha fazla bilgi için bkz. [Azure Data Factory Integration Runtime](concepts-integration-runtime.md).

### <a name="cluster-type"></a>Küme türü

Spark kümesinin türü için kullanılabilecek üç seçenek vardır: genel amaçlı, bellek için iyileştirilmiş ve işlem için iyileştirilmiş.

**Genel amaçlı** kümeler varsayılan seçimdir ve çoğu veri akışı iş yükü için ideal olacaktır. Bu, performans ve maliyet açısından en iyi dengeyi elde etmek için kullanılır.

Veri akışınız birçok birleşim ve arama içeriyorsa, **bellek için iyileştirilmiş** bir küme kullanmak isteyebilirsiniz. Bellek için iyileştirilmiş kümeler bellekte daha fazla veri saklayabilir ve alabileceğiniz bellek dışı hataları en aza indirir. En iyi duruma getirilmiş bellek, çekirdek başına en yüksek fiyat noktasıdır, ancak aynı zamanda daha başarılı işlem hatları elde edilmesine yol açabilir. Veri akışlarını yürütürken yetersiz bellek hatalarıyla karşılaşırsanız, bellek için iyileştirilmiş bir Azure IR yapılandırmasına geçin. 

**İşlem için iyileştirilmiş** , ETL iş akışları için ideal değildir ve çoğu üretim iş yükleri için Azure Data Factory ekibi tarafından önerilmez. Verilerin filtrelenmesi veya türetilmiş sütunlar eklenmesi gibi daha basit, bellek dışı yoğun olmayan veri dönüştürmeleri için, işlem için iyileştirilmiş kümeler çekirdek başına bir beleyici fiyatı üzerinden kullanılabilir.

### <a name="cluster-size"></a>Küme boyutu

Veri akışları, işlemleri paralel olarak gerçekleştirmek için bir Spark kümesindeki farklı düğümlere veri işlemeyi dağıtır. Daha fazla çekirdeğe sahip bir Spark kümesi, işlem ortamındaki düğümlerin sayısını artırır. Daha fazla düğüm, veri akışının işlem gücünü artırır. Küme boyutunun artırılması genellikle işlem süresini azaltmanın kolay bir yoludur.

Varsayılan küme boyutu, dört sürücü düğümü ve dört çalışan düğümtür.  Daha fazla veri işlemekle, daha büyük kümeler önerilir. Olası boyutlandırma seçenekleri aşağıda verilmiştir:

| Çalışan çekirdekleri | Sürücü çekirdekleri | Toplam çekirdek | Notlar |
| ------------ | ------------ | ----------- | ----- |
| 4 | 4 | 8 | İşlem için iyileştirilmiş işlem için kullanılamaz |
| 8 | 8 | 16 | |
| 16 | 16 | 32 | |
| 32 | 16 | 48 | |
| 64 | 16 | 80 | |
| 128 | 16 | 144 | |
| 256 | 16 | 272 | |

Veri akışları, sanal çekirdek-saat ' de fiyatlandırılır ve bu, küme boyutunun ve yürütme-zaman çarpanının buna göre belirlenir. Ölçeği azaltdıkça, dakika başına küme maliyetiniz artar, ancak genel bir süre kısalır.

> [!TIP]
> Bir kümenin boyutunun bir veri akışının performansını ne kadar etkilediğine ilişkin bir tavan vardır. Verilerinizin boyutuna bağlı olarak, bir kümenin boyutunu artırmanın performansı iyileştirmeye durulacağı bir nokta vardır. Örneğin, veri bölümlerinden daha fazla düğümünüz varsa, ek düğüm eklemek yardımcı olmaz. En iyi uygulama, küçük bir başlangıç yapmak ve performans ihtiyaçlarınızı karşılamak için ölçeği ölçeklendirmek olacaktır. 

### <a name="time-to-live"></a>Yaşam süresi

Varsayılan olarak, her veri akışı etkinliği Azure IR yapılandırmasını temel alan yeni bir Spark kümesi alır. En soğuk küme başlangıç zamanı birkaç dakika sürer ve veri işleme tamamlanana kadar başlayamaz. İşlem hatlarınız birden çok **sıralı** veri akışı içeriyorsa, yaşam SÜRESI (TTL) değerini etkinleştirebilirsiniz. Canlı değer için bir süre belirtmek, yürütme tamamlandıktan sonra belirli bir süre için kümeyi canlı tutar. TTL süresi boyunca yeni bir iş IR kullanmaya başlarsa, mevcut kümeyi yeniden kullanacaktır ve başlangıç zamanı büyük ölçüde azalır. İkinci iş tamamlandıktan sonra, küme TTL saati için de canlı kalır.

Ayrıca, veri akışı özellikleri altında Azure tümleştirme çalışma zamanı ' nda "hızlı yeniden kullanım" seçeneğini ayarlayarak, sıcak kümelerin başlama süresini de en aza indirmiş olursunuz. Bunu true olarak ayarlamak, ADF 'yi her bir işten sonra var olan kümeyi aşağı doğru bir şekilde kapatmamak ve bunun yerine var olan kümeyi yeniden kullanmak, temelde Azure IR canlı olarak ayarlamış olduğunuz işlem ortamını, TTL 'niz içinde belirtilen süreye kadar bir süre içinde tutmaya çalışır. Bu seçenek, bir işlem hattından yürütürken veri akışı etkinliklerinizin en kısa başlangıç süresini sağlar.

Ancak, veri akışlarınızın çoğu paralel olarak yürütülense, bu etkinlikler için kullandığınız IR için TTL 'yi etkinleştirmeniz önerilmez. Tek seferde tek bir küme üzerinde yalnızca bir iş çalışabilir. Kullanılabilir bir küme varsa, ancak iki veri akışı başladıysanız, yalnızca bir tane canlı kümeyi kullanacaktır. İkinci iş kendi yalıtılmış kümesini kullanacaktır.

> [!NOTE]
> Tümleştirme çalışma zamanı otomatik çözümle kullanılırken yaşam süresi kullanılamaz
 
> [!NOTE]
> Mevcut kümelerin hızlı bir şekilde yeniden kullanımı, şu anda genel önizlemede olan Azure Integration Runtime bir özelliktir

## <a name="optimizing-sources"></a>Kaynakları iyileştirme

Azure SQL veritabanı hariç her kaynak için, seçili değer olarak **geçerli bölümleme kullanımını** tutmanız önerilir. Diğer tüm kaynak sistemlerden okurken veri akışları verilerin boyutuna göre verileri eşit şekilde otomatik olarak bölümler. Her 128 MB veri için yeni bir bölüm oluşturulur. Veri boyutunuz arttıkça bölüm sayısı artar.

Özel bölümlendirme, verilerdeki Spark okumalarının *ardından* oluşur ve veri akışı performansınızı olumsuz yönde etkiler. Veriler okuma sırasında eşit olarak bölümlenmiş olduğu için bu önerilmez. 

> [!NOTE]
> Okuma hızları, kaynak sisteminizin verimi ile sınırlı olabilir.

### <a name="azure-sql-database-sources"></a>Azure SQL veritabanı kaynakları

Azure SQL veritabanı ' kaynak ' bölümlendirme adlı benzersiz bir bölümlendirme seçeneğine sahiptir. Kaynak bölümlemenin etkinleştirilmesi, kaynak sistemde paralel bağlantıları etkinleştirerek Azure SQL DB 'den okuma saatlerinizi iyileştirebilir. Bölüm sayısını ve verilerinizin nasıl bölümlenmesi gerektiğini belirtin. Yüksek kardinalite içeren bir bölüm sütunu kullanın. Ayrıca, kaynak tablonuzun bölümleme düzeniyle eşleşen bir sorgu da girebilirsiniz.

> [!TIP]
> Kaynak bölümlendirme için, SQL Server g/ç işlemi performans sorununa yöneliktir. Çok fazla bölüm eklemek, Kaynak veritabanınızı gri bir şekilde gösterebilir. Bu seçenek kullanıldığında genellikle dört veya beş bölüm idealdir.

![Kaynak bölümlendirme](media/data-flow/sourcepart3.png "Kaynak bölümlendirme")

#### <a name="isolation-level"></a>Yalıtım düzeyi

Azure SQL kaynak sistemi üzerinde okunan yalıtım düzeyinin performansı üzerinde etkisi vardır. ' READ UNCOMMITTED ' seçeneğinin belirlenmesi en hızlı performansı sağlar ve tüm veritabanı kilitlerini önler. SQL yalıtım düzeyleri hakkında daha fazla bilgi için lütfen bkz. [yalıtım düzeylerini anlama](/sql/connect/jdbc/understanding-isolation-levels).

#### <a name="read-using-query"></a>Sorguyu kullanarak oku

Azure SQL veritabanı 'ndan bir tablo veya SQL sorgusu kullanarak okuma yapabilirsiniz. Bir SQL sorgusu yürütüyorsunuz, dönüştürmenin başlaması için önce sorgunun tamamlanmalıdır. SQL sorguları, daha hızlı yürütülebileceği işlemleri iletmek ve SELECT, WHERE ve JOIN deyimleri gibi SQL Server okunan veri miktarını azaltmak için faydalı olabilir. İşlemleri geri alırken, veriler veri akışına gelmeden önce dönüşümlerinin kökenini ve performansını izleme imkanını kaybedersiniz.

### <a name="azure-synapse-analytics-sources"></a>Azure SYNAPSE Analytics kaynakları

Azure SYNAPSE Analytics kullanılırken, kaynak seçeneklerinde **hazırlama hazırlama** adlı bir ayar bulunur. Bu, ADF 'nin ```Staging``` , okuma performansını büyük ölçüde artıran SYNAPSE kullanarak okumasına olanak tanır. Etkinleştirme ```Staging``` , veri akışı etkinlik ayarlarında bir Azure Blob depolama alanı veya Azure Data Lake Storage Gen2 hazırlama konumu belirtmenizi gerektirir.

![Hazırlamayı etkinleştirme](media/data-flow/enable-staging.png "Hazırlamayı etkinleştirme")

### <a name="file-based-sources"></a>Dosya tabanlı kaynaklar

Veri akışları çeşitli dosya türlerini destekleirken, Azure Data Factory en iyi okuma ve yazma süreleri için Spark-Native Parquet biçiminin kullanılmasını önerir.

Aynı veri akışını bir dosya kümesinde çalıştırıyorsanız, joker karakterleri kullanarak veya bir dosya listesinden okurken bir klasörden okumanızı öneririz. Tek bir veri akışı etkinliği çalıştırması, toplu işteki tüm dosyalarınızı işleyebilir. Bu ayarları ayarlama hakkında daha fazla bilgi, [Azure Blob depolama](connector-azure-blob-storage.md#source-transformation)gibi bağlayıcı belgelerinde bulunabilir.

Mümkünse, bir dosya kümesi üzerinde veri akışlarını çalıştırmak için For-Each etkinliğini kullanmaktan kaçının. Bu, her bir için öğesinin her yinelemesinin kendi Spark kümesini (genellikle gerekli değildir ve pahalı olabilir) çalışmasına neden olur. 

## <a name="optimizing-sinks"></a>Havuzları iyileştirme

Veri akışları havuza yazarken, tüm özel bölümlendirme yazma işleminden hemen önce gerçekleşir. Kaynak gibi, çoğu durumda, seçili bölüm seçeneği olarak **geçerli bölümleme kullanımını** tutmanız önerilir. Bölümlenmiş veriler, bölümlenmemiş verilerden önemli ölçüde daha hızlı yazılır, hatta hedefi bölümlenmemiş olur. Aşağıda, çeşitli havuz türleri için tek tek noktalar verilmiştir. 

### <a name="azure-sql-database-sinks"></a>Azure SQL veritabanı havuzları

Azure SQL veritabanı ile, varsayılan bölümlendirme çoğu durumda çalışmalıdır. Havuzlarınızın SQL veritabanınızın işlemesi için çok fazla bölümü olabileceğinden emin olma olasılığı vardır. Bu sürümünde çalıştırıyorsanız, SQL veritabanı havuzunuzu tarafından outputpartitions sayısını azaltın.

#### <a name="impact-of-error-row-handling-to-performance"></a>Hata satırı işlemenin performansa etkisi

Havuz dönüşümünde hata satırı işlemeyi etkinleştirdiğinizde (hatada devam et "), ADF, uyumlu satırları hedef tablonuza yazmadan önce ek bir adım sürer. Bu ek adımda, bu adım için "%5" aralığında olabilecek küçük bir performans cezası olur ve seçeneği aynı zamanda uyumsuz satırlar olarak bir günlük dosyasına ayarlarsanız ek bir küçük performans isabeti de eklenmiştir.

#### <a name="disabling-indexes-using-a-sql-script"></a>SQL betiği kullanarak dizinleri devre dışı bırakma

SQL veritabanındaki bir yük için dizinlerin devre dışı bırakılması, tabloya yazma performansını önemli ölçüde iyileştirebilir. SQL havuzunuzu yazmadan önce aşağıdaki komutu çalıştırın.

`ALTER INDEX ALL ON dbo.[Table Name] DISABLE`

Yazma işlemi tamamlandıktan sonra, aşağıdaki komutu kullanarak dizinleri yeniden oluşturun:

`ALTER INDEX ALL ON dbo.[Table Name] REBUILD`

Bunlar her ikisi de Azure SQL VERITABANı veya SYNAPSE havuzu içindeki SQL öncesi ve sonrası komut dosyaları ile eşleme veri akışlarında yerel olarak yapılabilir.

![Dizinleri devre dışı bırak](media/data-flow/disable-indexes-sql.png "Dizinleri devre dışı bırak")

> [!WARNING]
> Dizinler devre dışı bırakıldığında, veri akışı bir veritabanının denetimini etkili bir şekilde ele alınır ve sorguların Şu anda başarılı olması düşüktür. Sonuç olarak, bu çakışmayı önlemek için gece ortasında birçok ETL işi tetiklenir. Daha fazla bilgi için [dizinleri devre dışı bırakma kısıtlamaları](/sql/relational-databases/indexes/disable-indexes-and-constraints) hakkında bilgi edinin

#### <a name="scaling-up-your-database"></a>Veritabanınızı ölçeklendirme

İşlem hattınızı artırmak ve DTU sınırlarına ulaştığınızda Azure azaltmayı en aza indirmek için, kaynağınızı yeniden boyutlandırmayı zamanlayın ve Azure SQL DB ve DW 'yi iş hattınızda çalıştırın. İşlem hattı yürütme tamamlandıktan sonra veritabanlarınızı normal çalışma hızına geri doğru yeniden boyutlandırın.

### <a name="azure-synapse-analytics-sinks"></a>Azure SYNAPSE Analytics havuzları

Azure SYNAPSE Analytics 'e yazarken, **hazırlama etkinleştirmeyi etkinleştir** ' in true olarak ayarlandığından emin olun. Bu, ADF 'yi toplu olarak verileri etkin bir şekilde yükleyen [SQL copy komutunu](/sql/t-sql/statements/copy-into-transact-sql) kullanarak yazmasını sağlar. Hazırlama kullanılırken verileri hazırlamak için bir Azure Data Lake Storage Gen2 veya Azure Blob depolama hesabına başvurmanız gerekir.

Hazırlama dışında, Azure SYNAPSE Analytics için Azure SQL veritabanı olarak aynı en iyi uygulamalar geçerlidir.

### <a name="file-based-sinks"></a>Dosya tabanlı havuzlar 

Veri akışları çeşitli dosya türlerini destekleirken, Azure Data Factory en iyi okuma ve yazma süreleri için Spark-Native Parquet biçiminin kullanılmasını önerir.

Veriler eşit şekilde dağıtılırsa, **geçerli bölümleme kullan** seçeneği, dosya yazmak için en hızlı bölümleme seçeneğidir.

#### <a name="file-name-options"></a>Dosya adı seçenekleri

Dosya yazarken, her birinin performans etkisi olan bir adlandırma seçenekleri seçeneğiniz vardır.

![Havuz seçenekleri](media/data-flow/file-sink-settings.png "havuz seçenekleri")

**Varsayılan** seçeneği belirlemek en hızlı şekilde yazar. Her bölüm Spark varsayılan adına sahip bir dosyaya karşılık gelir. Bu, yalnızca veri klasöründen okumanız durumunda yararlıdır.

Adlandırma **deseninin** ayarlanması, her bölüm dosyasını daha kolay bir adla yeniden adlandıracaktır. Bu işlem, yazma işleminden sonra gerçekleşir ve varsayılanı seçmekten biraz daha yavaştır. Bölüm başına, her bölüme el ile ad vermenize olanak tanır.

Bir sütun, verileri nasıl çıktısını almak istediğinizi içeriyorsa **sütunda veri olarak** seçebilirsiniz. Bu, verileri reshuffles ve sütunlar eşit olarak dağıtılmadığından performansı etkileyebilir.

**Tek bir dosyaya çıkış** , tüm verileri tek bir bölümde birleştirir. Bu, özellikle büyük veri kümelerinde uzun yazma sürelerine yol açar. Azure Data Factory takım, bunu yapmak için açık bir iş nedeni olmadıkça bu **seçeneği seçmeyi kesinlikle** önerir.

### <a name="cosmosdb-sinks"></a>CosmosDB havuzları

CosmosDB 'ye yazarken, veri akışı yürütmesi sırasında aktarım hızını ve toplu iş boyutunu değiştirme performansı iyileştirebilir. Bu değişiklikler yalnızca veri akışı etkinliği çalıştırıldığında etkili olur ve sonuç olarak orijinal koleksiyon ayarlarına geri döner. 

**Toplu iş boyutu:** Genellikle, varsayılan toplu iş boyutundan itibaren yeterlidir. Bu değeri daha fazla ayarlamak için verilerinizin kaba nesne boyutunu hesaplayın ve nesne boyutu * toplu iş boyutu 2 MB 'tan küçük olduğundan emin olun. Varsa, daha iyi işleme sağlamak için toplu iş boyutunu artırabilirsiniz.

**Aktarım hızı:** Belgelerin CosmosDB 'ye daha hızlı yazmasını sağlamak için burada daha yüksek bir üretilen iş ayarı ayarlayın. Yüksek bir verimlilik ayarına göre daha yüksek RU maliyetlerine göz önünde bulundurun.

**Yazma aktarım hızı bütçesi:** Dakikada toplam ru 'dan küçük olan bir değer kullanın. Çok sayıda Spark bölümünün bulunduğu bir veri akışınız varsa, bir bütçe üretilen işinin ayarlanması bu bölümlerde daha fazla bakiyeye izin verir.

## <a name="optimizing-transformations"></a>Dönüştürmeleri iyileştirme

### <a name="optimizing-joins-exists-and-lookups"></a>Birleştirmeleri, mevcut ve aramaları iyileştirme

#### <a name="broadcasting"></a>Yayınlar

Birleşimler, aramalar ve mevcut dönüştürmelerde, bir veya iki veri akışı çalışan düğümü belleğine sığacak kadar küçükse, **yayını** etkinleştirerek performansı iyileştirebilirsiniz. Yayın, kümedeki tüm düğümlere küçük veri çerçeveleri gönderdiğinizde olur. Bu, Spark altyapısının büyük akıştaki verileri reshuffling olmadan bir JOIN gerçekleştirmesini sağlar. Varsayılan olarak, Spark altyapısı, birleştirmenin bir tarafını yayınlamayacağınıza otomatik olarak karar verir. Gelen verileriniz hakkında bilginiz varsa ve bir akışın diğerine göre önemli ölçüde daha küçük olacağını biliyorsanız, **sabit** yayınlama seçeneğini belirleyebilirsiniz. Sabit yayınlama, Spark 'ın seçili akışı yayınlamasını zorlar. 

Verileri bulunan verilerin boyutu Spark düğümü için çok büyükse bellek yetersiz hatası alabilirsiniz. Bellek yetersiz hatalarından kaçınmak için **bellek için iyileştirilmiş** kümeleri kullanın. Veri akışı yürütmeleri sırasında yayın zaman aşımları yaşarsanız, yayın iyileştirmesini kapatabilirsiniz. Ancak bu, veri akışlarının daha yavaş gerçekleştirilmesine neden olur.

Büyük veritabanı sorguları gibi sorgu daha uzun süretabilecek veri kaynaklarıyla çalışırken, birleştirme için yayının kapalı olması önerilir. Uzun sorgu süreleriyle kaynak, küme işlem düğümlerine yayın yapmayı denediğinde Spark zaman aşımları oluşmasına neden olabilir. Yayını kapatmak için başka bir iyi seçenek de, daha sonra bir arama dönüşümünde kullanılacak değerleri toplayarak veri akışınızda bir akışa sahip olduğunuzda olur. Bu model Spark İyileştiriciyi şaşırtabilir ve zaman aşımlarını neden olabilir.

![Dönüştürme iyileştirmelerine Birleştir](media/data-flow/joinoptimize.png "Birleştirmeyi En Iyi duruma getirme")

#### <a name="cross-joins"></a>Çapraz birleşimler

JOIN koşullarınız için değişmez değer değerleri kullanırsanız veya bir birleştirmenin her iki tarafında birden fazla eşleşme varsa, Spark birleştirmeyi çapraz bir JOIN olarak çalıştırır. Çapraz katılım, daha sonra birleştirilmiş değerleri filtreleyerek tam bir Kartezyen üründür. Bu, diğer JOIN türlerinden önemli ölçüde daha yavaştır. Performans etkisini önlemek için, JOIN koşullarınızın her iki tarafında da sütun başvurularına sahip olduğunuzdan emin olun.

#### <a name="sorting-before-joins"></a>Birleşimlerden önce sıralama

SSIS gibi araçlarla birleştirme birleştirmesinin aksine, JOIN dönüşümü zorunlu bir birleştirme birleştirme işlemi değildir. JOIN anahtarları dönüşümden önce sıralama gerektirmez. Azure Data Factory ekibi, veri akışlarında eşleme dönüştürmelerinin kullanılmasını önermez.

### <a name="window-transformation-performance"></a>Pencere dönüştürme performansı

[Pencere dönüştürmesi](data-flow-window.md) , verileri ```over()``` dönüştürme ayarlarındaki yan tümcesinin bir parçası olarak seçtiğiniz sütunlardaki değere göre bölümlendirir. Windows dönüşümünde sunulan çok sayıda popüler toplam ve analitik işlev vardır. Bununla birlikte, kullanım durumu, sıralama veya satır numarası amacıyla tüm veri kümeniz üzerinde bir pencere oluşturmak için, ```rank()``` ```rowNumber()``` bunun yerine [Rank dönüşümünü](data-flow-rank.md) ve [vekil anahtar dönüşümünü](data-flow-surrogate-key.md)kullanmanız önerilir. Bu dönüşüm, bu işlevleri kullanarak daha iyi bir tam veri kümesi işlemi gerçekleştirir.

### <a name="repartitioning-skewed-data"></a>Asimetrik verileri yeniden bölümleme

Birleşimler ve toplamalar gibi bazı dönüştürmeler, veri bölümlerinizi reshuffle ve zaman zaman asimetrik verilere yol açabilir. Eğilmiş veriler, verilerin bölümler arasında eşit dağıtılmayacağı anlamına gelir. Büyük ölçüde asimetrik veriler, daha yavaş aşağı akış dönüşümlerine ve havuz yazmaya yol açabilir. İzleme görünümü ' nde dönüşüme tıklayarak veri akışı çalıştırmasının herhangi bir noktasında verilerinizin çarpıklığını kontrol edebilirsiniz.

![Çarpıklık ve basıklık](media/data-flow/skewness-kurtosis.png "Çarpıklık ve basıklık")

İzleme ekranı, verilerin her bölümde iki ölçüm, çarpıklık ve basıklık ile birlikte nasıl dağıtıldığını gösterir. **Çarpıklık** , verilerin nasıl olduğu ve pozitif, sıfır, negatif veya tanımsız bir değere sahip olduğu bir ölçüdür. Negatif eğme, sol kuyruklu daha uzun olduğu anlamına gelir. **Basıklık** , verilerin ağır veya hafif olup olmadığı ölçüdür. Yüksek basıklık değerleri istenmez. -3 ve 3 arasındaki en iyi çarpıklık aralıkları 10 ' dan az olmalıdır. Bu sayıları yorumlamak için kolay bir yol, Bölüm grafiğine bakar ve 1 çubuğun Rest 'den önemli ölçüde daha büyük olup olmadığını görüyor.

Bir dönüşümden sonra verileriniz eşit olarak bölünmemişse, yeniden bölümlemek için [en iyileştirme sekmesini](#optimize-tab) kullanabilirsiniz. Reshuffling verileri zaman alır ve veri akışı performansınızı iyileştiremeyebilir.

> [!TIP]
> Verilerinizi yeniden bölümleyip reshuffle bu verileri içeren aşağı akış dönüşümlerine sahipseniz, birleştirme anahtarı olarak kullanılan bir sütunda karma bölümlendirme kullanın.

## <a name="using-data-flows-in-pipelines"></a>İşlem hatlarında veri akışlarını kullanma 

Birden çok veri akışı ile karmaşık işlem hatları oluştururken, mantıksal akışınız zamanlama ve maliyet üzerinde büyük bir etkiye sahip olabilir. Bu bölümde, farklı mimari stratejilerinin etkileri ele alınmaktadır.

### <a name="executing-data-flows-in-parallel"></a>Veri akışlarını paralel olarak yürütme

Birden çok veri akışını paralel olarak çalıştırırsanız, ADF her etkinlik için ayrı Spark kümeleri çalıştırır. Bu, her bir işin yalıtılmış ve paralel çalışmasına izin verir, ancak aynı anda çalışan birden fazla kümeye yol açacaktır.

Veri akışlarınız paralel olarak yürütülülüsün sonra, birden fazla kullanılmamış ısınma havuzuna yol açacağından, Azure IR yaşam süresi özelliği etkinleştirilmemelidir.

> [!TIP]
> Her etkinlik için aynı veri akışını birden çok kez çalıştırmak yerine Veri Gölü içinde verilerinizi işleyin ve tek bir veri akışında verileri işlemek için joker karakterler kullanın.

### <a name="execute-data-flows-sequentially"></a>Veri akışlarını ardışık olarak Yürüt

Veri akışı etkinliklerinizi sırayla çalıştırırsanız, Azure IR yapılandırmasında bir TTL ayarlamanız önerilir. ADF, daha hızlı bir küme başlangıç zamanına neden olan işlem kaynaklarını yeniden kullanacaktır. Her etkinlik, her yürütme için yeni bir Spark bağlamı almaya devam eder. Sıralı etkinlikler arasındaki süreyi daha da azaltmak için, ADF 'nin var olan kümeyi yeniden kullanmasını bildirmek için, Azure IR "hızlı yeniden kullanım" onay kutusunu ayarlayın.

> [!NOTE]
> Mevcut kümelerin hızlı bir şekilde yeniden kullanımı, şu anda genel önizlemede olan Azure Integration Runtime bir özelliktir

### <a name="overloading-a-single-data-flow"></a>Tek bir veri akışını aşırı yükleme

Tüm mantığınızı tek bir veri akışının içine yerleştirirseniz, ADF tüm işi tek bir Spark örneği üzerinde yürütür. Bu, maliyetleri azaltmanın bir yolu gibi görünse de, farklı mantıksal akışlar birlikte karıştırıyor ve izlenmesi ve hata ayıklaması zor olabilir. Bir bileşen başarısız olursa, işin diğer tüm bölümleri de başarısız olur. Azure Data Factory ekibi, veri akışlarını bağımsız iş mantığı akışlarıyla düzenlemeyi öneriyor. Veri akışınız çok büyük hale gelirse, bileşenleri ayırmak için bölmek, izleme ve hata ayıklama işlemlerini kolaylaştırır. Bir veri akışındaki dönüştürme sayısında sabit sınır olmadığından, çok fazla olması işi karmaşık hale getirir.

### <a name="execute-sinks-in-parallel"></a>Havuzları paralel olarak Yürüt

Veri akışı havuzlarının varsayılan davranışı, her bir havuzun ardışık olarak, seri bir biçimde yürütülmesi ve havuzda bir hata ile karşılaşıldığında veri akışının başarısız olması olabilir. Ayrıca, veri akışı özelliklerine gitmediğiniz ve havuzlar için farklı öncelikler ayarlamadığınız müddetçe Tüm havuzlar aynı gruba varsayılan olarak ayarlanır.

Veri akışları, Kullanıcı arabirimi Tasarımcısı ' nda veri akışı özellikleri sekmesinden, havuzları gruplar halinde gruplandırmanızı sağlar. Aynı grup numarasını kullanarak aynı zamanda, gruplarınızın yürütülme sırasını ve grup havuzlarını de gruplandırabilirsiniz. Grupları yönetmeye yardımcı olmak için, ADF 'nin paralel olarak çalışması için aynı grupta havuzları çalıştırmasını isteyebilirsiniz.

Ardışık düzen veri akışını Yürüt "havuz özellikleri" bölümünde, paralel havuz yüklemeyi açma seçeneği vardır. "Paralel olarak çalıştır" seçeneğini etkinleştirdiğinizde, veri akışlarının, sıralı bir şekilde değil, aynı zamanda bağlı havuza yazdığına yol gösterilir. Paralel seçenekten yararlanmak için, havuz birlikte gruplandırmalı ve yeni bir dal veya koşullu bölme aracılığıyla aynı akışa bağlanmalıdır.

## <a name="next-steps"></a>Sonraki adımlar

Performansla ilgili diğer veri akışı makalelerine bakın:

- [Veri akışı etkinliği](control-flow-execute-data-flow-activity.md)
- [Veri akışı performansını izleme](concepts-data-flow-monitoring.md)
