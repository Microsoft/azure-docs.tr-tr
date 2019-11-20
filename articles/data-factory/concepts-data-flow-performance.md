---
title: Azure Data Factory veri akışı performansını ve ayarlama kılavuzunu eşleme
description: Azure Data Factory veri akışlarını eşleme performansını etkileyen anahtar faktörleri hakkında bilgi edinin.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 10/07/2019
ms.openlocfilehash: 20a08345d8335b4857ca9777efb55f953ee63e9f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681543"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Veri akışlarını eşleme performansı ve ayarlama Kılavuzu

Azure Data Factory veri akışlarını eşleme, veri dönüştürmelerinin ölçeklendirilmesi, dağıtılması ve organize olması için kod içermeyen bir arabirim sağlar. Veri akışlarını eşleme konusunda bilgi sahibi değilseniz, [eşleme veri akışına genel bakış](concepts-data-flow-overview.md)bölümüne bakın.

ADF UX 'den veri akışları tasarlarken ve test ederken, bir kümenin ısınma süresini beklemeden veri akışlarınızı gerçek zamanlı olarak yürütmek için hata ayıklama modunda geçiş yaptığınızdan emin olun. Daha fazla bilgi için bkz. [hata ayıklama modu](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flow-performance"></a>Veri akışı performansını izleme

Eşleme veri akışları tasarlarken, yapılandırma panelinde veri önizleme sekmesine tıklayarak her bir dönüşüme birim test edebilirsiniz. Mantığınızı doğruladıktan sonra, bir işlem hattındaki etkinlik olarak veri akışınızı uçtan uca test edin. Bir veri akışı yürütme etkinliği ekleyin ve veri akışınızın performansını test etmek için Hata Ayıkla düğmesini kullanın. Veri akışınızın yürütme planını ve performans profilini açmak için, işlem hattınızdaki çıkış sekmesinde ' Eylemler ' altında bulunan göz gözlük simgesine tıklayın.

![Veri akışı Izleyicisi](media/data-flow/mon002.png "Veri akışı Izleyicisi 2")

 Bu bilgileri, veri akışınızın farklı boyutlardaki veri kaynaklarına karşı performansını tahmin etmek için kullanabilirsiniz. Daha fazla bilgi için bkz. [eşleme veri akışlarını izleme](concepts-data-flow-monitoring.md).

![Veri Akışını İzleme](media/data-flow/mon003.png "Veri akışı Izleyicisi 3")

 İşlem hattı hata ayıklama çalıştırmaları için, bir ısınma kümesi için genel performans hesaplamalarınızda bir dakikalık küme ayarlama zamanı gereklidir. Varsayılan Azure Integration Runtime başladıysanız, başlatma süresi yaklaşık 5 dakika sürebilir.

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>Azure Integration Runtime işlem boyutunu artırma

Daha fazla çekirdeğe sahip bir Integration Runtime Spark işlem ortamlarındaki düğümlerin sayısını artırır ve verilerinizi okumak, yazmak ve dönüştürmek için daha fazla işlem gücü sağlar.
* İşlem hızlarınızın giriş oranından daha yüksek olmasını istiyorsanız, **işlem Için iyileştirilmiş** bir küme deneyin
* Bellekte daha fazla veri önbelleğe almak istiyorsanız **bellek Için iyileştirilmiş** bir küme deneyin.

![Yeni IR](media/data-flow/ir-new.png "Yeni IR")

Integration Runtime oluşturma hakkında daha fazla bilgi için bkz. [Azure Data Factory Integration Runtime](concepts-integration-runtime.md).

### <a name="increase-the-size-of-your-debug-cluster"></a>Hata ayıklama kümenizin boyutunu artırın

Varsayılan olarak, hata ayıklamayı açmak her bir veri fabrikası için otomatik olarak oluşturulan varsayılan Azure tümleştirme çalışma zamanını kullanır. Bu varsayılan Azure IR, genel Işlem özellikleri kullanılarak, bir sürücü düğümü için dört ve bir çalışan düğümü için dört adet olmak üzere sekiz çekirdek için ayarlanır. Daha büyük verilerle test ettiğinizde, daha büyük yapılandırmalara sahip bir Azure IR oluşturarak hata ayıklama kümenizin boyutunu artırabilir ve hata ayıklama sırasında bu yeni Azure IR seçebilirsiniz. Bu, ADF 'yi veri önizleme ve veri akışları ile ardışık düzen hata ayıklaması için bu Azure IR kullanmasını ister.

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Azure SQL veritabanı ve Azure SQL veri ambarı için iyileştirme

### <a name="partitioning-on-source"></a>Kaynakta bölümlendirme

1. **Optimizasyon** sekmesine gidin ve **bölümleme ayarla** ' yı seçin.
1. **Kaynak**seçin.
1. **Bölüm sayısı**altında, Azure SQL DB 'nize yönelik bağlantı sayısı üst sınırını ayarlayın. Veritabanınıza paralel bağlantı kazanmak için daha yüksek bir ayar deneyebilirsiniz. Ancak bazı durumlar, sınırlı sayıda bağlantıyla daha hızlı performans elde edebilir.
1. Belirli bir tablo sütunuyla veya bir sorgu ile bölümleyip bölümlendirmeyeceğinizi seçin.
1. **Sütun**' ı seçtiyseniz, Bölüm sütununu seçin.
1. **Sorgu**' yı seçtiyseniz, veritabanı tablonuzun bölümleme düzeniyle eşleşen bir sorgu girin. Bu sorgu, kaynak veritabanı altyapısının Bölüm eleme özelliğinden yararlanmasını sağlar. Kaynak veritabanı tablolarınızın bölümlenmiş olması gerekmez. Kaynağınız zaten bölümlenmemişse ADF, kaynak dönüşümünde seçtiğiniz anahtara göre Spark dönüştürme ortamında veri bölümleme kullanır.

![Kaynak bölüm](media/data-flow/sourcepart3.png "Kaynak bölüm")

### <a name="source-batch-size-input-and-isolation-level"></a>Kaynak toplu iş boyutu, giriş ve yalıtım düzeyi

Kaynak dönüşümünde **kaynak seçenekleri** altında aşağıdaki ayarlar performansı etkileyebilir:

* Toplu iş boyutu, ADF 'yi satır satır yerine bellekte kümeler halinde depolamasını ister. Toplu iş boyutu isteğe bağlı bir ayardır ve doğru boyutlandıralınmazlarsa, işlem düğümlerinde kaynakları çalıştırabilirsiniz.
* Bir sorgu ayarlandığında, işleme için veri akışına geçmeden önce kaynakta satırları filtrelemenize izin verebilir. Bu, ilk veri alımını daha hızlı hale getirir. Bir sorgu kullanıyorsanız, Azure SQL DB 'niz için READ UNCOMMıTTED gibi isteğe bağlı sorgu ipuçları ekleyebilirsiniz.
* Read UNCOMMITTED, kaynak dönüşümünde daha hızlı sorgu sonuçları sağlar

![Kaynak](media/data-flow/source4.png "Kaynak")

### <a name="sink-batch-size"></a>Havuz toplu iş boyutu

Veri akışlarınızın satır içi işlenmesini önlemek için, Azure SQL VERITABANı ve Azure SQL DW havuzları için ayarlar sekmesinde **toplu iş boyutunu** ayarlayın. Toplu iş boyutu ayarlandıysa, ADF, belirtilen boyuta göre veritabanı yazmaları toplu işlemlere işler.

![Ev](media/data-flow/sink4.png "Havuz")

### <a name="partitioning-on-sink"></a>Havuzda bölümlendirme

Verileriniz hedef Tablolarınızda bölümlenmemiş olsanız bile, verilerinizin havuz dönüşümünde bölümlenmesi önerilir. Bölümlenmiş veriler genellikle tüm bağlantıların tek bir düğüm/bölüm kullanmasına zorlanarak çok daha hızlı yüklenmesine neden olur. Havuzunuzda En Iyi duruma getirin sekmesine gidin ve havuzunuzda yazılacak ideal bölüm sayısını seçmek için bir *kez deneme* bölümlendirme seçeneğini belirleyin.

### <a name="disable-indexes-on-write"></a>Yazma sırasında dizinleri devre dışı bırak

İşlem hattınızda, Havuzınızdan yazılmış hedef tablolarınızdaki dizinleri devre dışı bırakan veri akışı etkinlikinizden önce bir [saklı yordam etkinliği](transform-data-using-stored-procedure.md) ekleyin. Veri akışı etkinliğinizi tamamladıktan sonra, bu dizinleri sağlayan başka bir saklı yordam etkinliği ekleyin.

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>Azure SQL DB ve DW 'nizin boyutunu artırın

İşlem hattınızı artırmak ve DTU sınırlarına ulaştığınızda Azure azaltmayı en aza indirmek için, kaynağınızı yeniden boyutlandırmayı zamanlayın ve Azure SQL DB ve DW 'yi iş hattınızda çalıştırın. İşlem hattı yürütme tamamlandıktan sonra veritabanlarınızı normal çalışma hızına geri doğru yeniden boyutlandırın.

### <a name="azure-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[Yalnızca Azure SQL DW] Polybase aracılığıyla verileri toplu olarak yüklemek için hazırlama kullanma

DW 'nize satır satır ekleme yapmaktan kaçınmak için, ADF 'nin [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)kullanabilmesi için havuz ayarlarınızda **hazırlama özelliğini etkinleştirin** ' i işaretleyin. PolyBase, ADF 'nin verileri toplu olarak yüklemesine olanak tanır.
* Veri akışı etkinliğinizi bir işlem hattından yürüttüğünüzde, toplu yükleme sırasında verilerinizi hazırlamak için bir blob veya ADLS 2. depolama konumu seçmeniz gerekir.

## <a name="optimizing-for-files"></a>Dosyalar için iyileştirme

Her dönüşümde, Data Factory 'nin en Iyileştirme sekmesinde kullanmasını istediğiniz bölümlendirme şemasını ayarlayabilirsiniz.
* Daha küçük dosyalar için, *tek bölümün* seçilmesi, bazı durumlarda daha iyi ve daha hızlı bir şekilde çalışabilir ve bu da küçük dosyalarınızın bölümlenmesi için Spark sorulmasını
* Kaynak verileriniz hakkında yeterli bilgiye sahip değilseniz, *hepsini bir kez deneme* bölümlendirme ve bölüm sayısını ayarlama ' yı seçin.
* Verilerinizde iyi bir karma anahtar olabilecek sütunlar varsa *karma bölümlendirme*' yi seçin.

Veri önizleme ve ardışık düzen Hata ayıklamasında hata ayıklarken, dosya tabanlı kaynak veri kümeleri için sınır ve örnekleme boyutları, okunan satır sayısı için yalnızca döndürülen satır sayısı için geçerlidir. Bu, hata ayıklama yürütmelerinin performansını etkileyebilir ve muhtemelen akışın başarısız olmasına neden olabilir.
* Hata ayıklama kümeleri, varsayılan olarak tek düğümlü küçük kümelerdir ve hata ayıklama için örnek küçük dosyaları kullanmanızı öneririz. Hata ayıklama ayarları ' na gidin ve geçici bir dosya kullanarak verilerinizin küçük bir alt kümesini işaret edin.

    ![Hata ayıklama ayarları](media/data-flow/debugsettings3.png "Hata ayıklama ayarları")

### <a name="file-naming-options"></a>Dosya adlandırma seçenekleri

Blob veya ADLS dosya deposu yazma veri akışları eşleme içinde dönüştürülmüş verileri yazmanın en yaygın yolu. Havuzınızda, adlandırılmış bir dosya değil, bir kapsayıcıya veya klasöre işaret eden bir veri kümesi seçmelisiniz. Eşleme veri akışı, yürütme için Spark kullandığından, çıktılarınız bölümlendirme düzeninize göre birden fazla dosyanın üzerine bölünür.

Ortak bölümlendirme şeması, tüm çıkış bölüm dosyalarını havuzinizdeki tek bir dosyada birleştiren _tek bir dosyaya çıkış_' ı seçmedir. Bu işlem, çıktının tek bir küme düğümünde tek bir bölüme azalttığını gerektirir. Birçok büyük kaynak dosyasını tek bir çıkış dosyasına birleştiriyorsanız, küme düğüm kaynakları ' nı çalıştırabilirsiniz.

İşlem düğümü kaynaklarını tüketmenin önüne geçmek için, varsayılan, en iyi duruma getirilmiş şemayı veri akışında tutun ve işlem hattınızda, tüm bölüm dosyalarını çıkış klasöründen yeni tek bir dosyaya birleştiren bir kopyalama etkinliği ekleyin. Bu teknik, dönüştürme eylemini dosya birleştirmeden ayırır ve _çıktıyı tek bir dosyaya_ayarla ile aynı sonuca erişir.

### <a name="looping-through-file-lists"></a>Dosya listeleri aracılığıyla döngü

Kaynak dönüştürme her etkinlik Için kullanarak döngü yerine birden çok dosya üzerinde yineleme yaparken, bir eşleme veri akışı daha iyi yürütülür. Kaynak dönüşümünüze joker karakter veya dosya listesi kullanmanızı öneririz. Veri akışı işlemi, bu döngünün Spark kümesi içinde oluşmasına izin vererek daha hızlı yürütülür. Daha fazla bilgi için bkz. [kaynak dönüşümünde joker karakter](data-flow-source.md#file-based-source-options)kullanımı.

Örneğin, 2019 Temmuz 'dan BLOB depolama alanındaki bir klasörde işlemek istediğiniz veri dosyaları listeniz varsa, aşağıdaki kaynak dönüşümünüze kullanabileceğiniz bir joker karakterdir.

```DateFiles/*_201907*.txt```

Joker karakter kullanımı ' nı kullanarak, işlem hattınız yalnızca bir veri akışı etkinliği içerir. Bu, içinde bir veri akışı yürütme etkinliği içeren bir ForEach kullanarak tüm eşleşen dosyalarda yineleme yapan blob deposuna karşı bir aramanın daha iyi bir şekilde gerçekleştirilir.

## <a name="next-steps"></a>Sonraki adımlar

Performansla ilgili diğer veri akışı makalelerine bakın:

- [Veri akışı Iyileştirme sekmesi](concepts-data-flow-overview.md#optimize)
- [Veri akışı etkinliği](control-flow-execute-data-flow-activity.md)
- [Veri akışı performansını izleme](concepts-data-flow-monitoring.md)
