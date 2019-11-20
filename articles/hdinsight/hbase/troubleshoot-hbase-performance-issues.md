---
title: Azure HDInsight 'ta Apache HBase performans sorunlarını giderme
description: Azure HDInsight 'ta en iyi performansı elde etmek için çeşitli Apache HBase performansı ayarlama yönergeleri ve ipuçları.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 0466b08e551a5fa9da37afe2e5ad175ef28c804e
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529575"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Azure HDInsight 'ta Apache HBase performans sorunlarını giderme

Bu makalede, Azure HDInsight 'ta en iyi performansı elde etmek için çeşitli Apache HBase performans ayarlama yönergeleri ve ipuçları açıklanmaktadır. Bu ipuçlarının birçoğu, belirli iş yüküne ve okuma/yazma/tarama düzenine bağlıdır. Bir üretim ortamında yapılandırma değişikliklerini uygulamadan önce bunları tamamen test edin.

## <a name="hbase-performance-insights"></a>HBase Performans öngörüleri

Çoğu HBase iş yükünün en önemli performans sorunu, önceden yazma Günlüğleridir (WAL). Yazma performansını önemli ölçüde etkiler. HDInsight HBase 'e ayrılmış bir depolama işlem modeli vardır. Sanal makineler bölge sunucularını barındırsa bile, veriler Azure Storage üzerinde uzaktan depolanır. Son olarak, WAL, Azure depolama 'ya de yazıldı. HDInsight 'ta bu davranış sorunu bu şekilde artıroluşturuyor. [Hızlandırılmış yazma](./apache-hbase-accelerated-writes.md) özelliği, bu sorunu çözmek için tasarlanmıştır. WAL 'yi Azure Premium SSD tarafından yönetilen disklere yazar. Bu gecenin, yazma performansını ve yazma açısından yoğun iş yüklerinden bazılarının karşılaştığı birçok soruna yardımcı olur.

Okuma işlemlerinde önemli bir geliştirme sağlamak için, uzak depolama birimi olarak [Premium Blok Blob Depolama hesabını](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) kullanın. Bu seçenek yalnızca WAL özelliği etkinse mümkündür.

## <a name="compaction"></a>Sıkıştırmayı

Sıkıştırma, topluluk üzerinde temel olarak kabul edilen bir diğer olası darboğaztır. Varsayılan olarak, HDInsight HBase kümelerinde birincil sıkıştırma devre dışıdır. Yoğun kaynak gerektiren bir işlem olsa da, müşteriler iş yüklerine göre zamanlamaya yönelik tam esnekliğe sahip olduğu için sıkıştırma devre dışı bırakıldı. Örneğin, bu, yoğun olmayan saatlerde zamanlama gösterebilir. Ayrıca, depolama alanı uzak (Azure Storage tarafından desteklenen) bir yerel Hadoop Dağıtılmış Dosya Sistemi (bir Hadoop) yerine, veri konumu sorun değildir.

Müşteriler, kendi kolaylığını önemli bir şekilde zamanlayamaz. Bu bakım yapamıyorsanız, düzenleme uzun çalıştırmada okuma performansını olumsuz etkileyecek.

Tarama işlemleri için 100 milisaniyeden çok daha yüksek olan gecikme sürelerinin bir sorun olmasının nedeni olması gerekir. Birincil sıkıştırma 'nın doğru şekilde zamanlandığından emin olun.

## <a name="apache-phoenix-workload"></a>Apache Phoenix iş yükü

Aşağıdaki sorulara yanıt vermek Apache Phoenix iş yükünüzü daha iyi anlamanıza yardımcı olur:

* "Okumalarınız", taramalara çevirsin mi?
    * Öyleyse, bu taramaların özellikleri nelerdir?
    * Uygun dizin oluşturma da dahil olmak üzere bu taramalar için Phoenix tablo şemanızı iyileştirdiniz mi?
* "Okumalarınızın" üretmesindeki sorgu planlarını anlamak için `EXPLAIN` ifadesini kullandınız mı?
* Yazmalar "büyük/veya seçer" mı?
    * Bu durumda, taramalar de yapılır. Tarama için beklenen gecikme süresi yaklaşık 100 milisaniyede ortalanır ve nokta HBase 'e göre 10 milisaniyeye kıyasla.  

## <a name="test-methodology-and-metrics-monitoring"></a>Test metodolojisi ve ölçümleri izleme

Yahoo! gibi değerlendirmeler kullanıyorsanız Kıyaslama, JMeter veya Fehataişlev hizmeti sunan bulut, performansı test etmek ve ayarlamak için şunları yaptığınızdan emin olun:

- İstemci makineler bir performans sorunu olmaz. Bunu yapmak için, istemci makinelerdeki CPU kullanımını denetleyin.

- İş parçacığı sayısı gibi istemci tarafı yapılandırmaların, istemci bant genişliğine göre doygunluğu için uygun şekilde ayarlanmış olması.

- Test sonuçları doğru ve sistematik olarak kaydedilir.

Sorgularınızı aniden daha kötüleştikten sonra uygulama kodunuzda olası hataları kontrol edin. Çok büyük miktarlarda geçersiz veri oluşturuyor mu? Varsa, okuma gecikmeleri artırabilir.

## <a name="migration-issues"></a>Geçiş sorunları

Azure HDInsight 'a geçiş yapıyorsanız geçişinizin sistematik ve doğru şekilde, tercihen Otomasyon aracılığıyla yapıldığından emin olun. El ile geçişten kaçının. Şunları yaptığınızdan emin olun:

- Tablo öznitelikleri doğru şekilde geçirilir. Öznitelikler, Compression, Bloom filtreleri vb. içerebilir.

- Phoenix tablolarındaki salıcı ayarları, yeni küme boyutuna uygun şekilde eşlenir. Örneğin, anahtar demetlerinin sayısı kümedeki çalışan düğümü sayısının katı olmalıdır. Ve etkin biriktirme miktarı faktörü olan bir çoklu değer kullanmanız gerekir.

## <a name="server-side-configuration-tunings"></a>Sunucu tarafı yapılandırma tunları

HDInsight HBase 'de, HFiles uzak depolama üzerinde depolanır. Önbellek kaçırılması durumunda, şirket içi sistemlerdeki veriler yerel olarak korunduğundan, okuma maliyeti şirket içi sistemlerden daha yüksektir. Çoğu senaryoda, HBase önbelleklerinin (blok önbelleği ve demet önbelleği) akıllı kullanımı bu sorunu aşmak için tasarlanmıştır. Sorunun atlalanamadığı durumlarda, Premium Blok Blobu hesabı kullanmak bu soruna yardımcı olabilir. Windows Azure Depolama Blobu sürücüsü, okuma modunu (sıralı olarak rastgele) temel alarak blok halinde veri getirmek için `fs.azure.read.request.size` gibi belirli özellikleri kullanır, bu nedenle okumalar üzerinde daha yüksek gecikme sürelerinin örnekleri olmaya devam edebilir. Empırical denemeleri aracılığıyla okuma isteği blok boyutunu (`fs.azure.read.request.size`) 512 KB olarak ayarlamanın yanı sıra HBase tablolarının blok boyutunu aynı boyut olarak eşleştirirken en iyi sonucu elde edin.

En büyük boyut düğümleri kümelerinde, HDInsight HBase, `regionservers` çalıştıran sanal makineye bağlı yerel bir Premium SSD dosya olarak `bucketcache` sağlar. Bunun yerine yığın dışı önbelleğin kullanılması bazı geliştirme sağlayabilir. Bu geçici çözüm, kullanılabilir bellek kullanımı ve muhtemelen dosya tabanlı önbellekten daha küçük olabilecek bir sınırlamaya sahiptir, bu nedenle her zaman en iyi seçim olmayabilir.

Aşağıda, belirlediğimiz diğer özel parametrelerden bazıları ve değişen derecenin sağlanmasına yardımcı olmak için verilmiştir:

- Varsayılan 128 MB ile 256 MB arasında `memstore` boyutunu artırın. Genellikle, bu ayar ağır yazma senaryolarında önerilir.

- Varsayılan değer olan **1** ' den **4**' e kadar sıkıştırma için ayrılan iş parçacıklarının sayısını artırın. Sık karşılaşılan küçük işlemleri gözlemlememiz durumunda bu ayar geçerlidir.

- Depolama sınırı nedeniyle `memstore` temizlemeyi engellemeyi önleyin. Bu arabelleği sağlamak için `Hbase.hstore.blockingStoreFiles` ayarını **100**olarak arttırın.

- Boşaltmaları denetlemek için aşağıdaki ayarları kullanın:

    - `Hbase.regionserver.maxlogs`: **140** (Wal sınırları nedeniyle boşaltmaları önler)

    - `Hbase.regionserver.global.memstore.lowerLimit`: **0,55**

    - `Hbase.regionserver.global.memstore.upperLimit`: **0,60**

- İş parçacığı havuzu ayarlama için Phoenix 'e özgü yapılandırma:

    - `Phoenix.query.queuesize`: **10000**

    - `Phoenix.query.threadpoolsize`: **512**

- Diğer Phoenix 'e özgü konfigürasyonlar:

    - `Phoenix.rpc.index.handler.count`: **50** (büyük veya çok sayıda dizin araması varsa)

    - `Phoenix.stats.updateFrequency`: **1 saat**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`: **1 saat**

    - `Phoenix.coprocessor.maxmetadatacachesize`: **50 MB**

- RPC zaman aşımları: **3 dakika**

   - RPC zaman aşımları HBase RPC zaman aşımı, HBase istemci tarayıcısı zaman aşımı ve Phoenix sorgu zaman aşımı içerir. 
   - @No__t_0 parametresinin hem sunucu ucunda hem de istemci ucunda aynı değere ayarlandığından emin olun. Aynı değillerse, bu ayar `OutOfOrderScannerException` ilişkili istemci-uç hatalarına yol açar. Bu ayar büyük taramalar için düşük bir değere ayarlanmalıdır. Bu değer **100**olarak ayarlanmıştır.

## <a name="other-considerations"></a>Diğer konular

Ayarlamayı göz önünde bulundurmanız gereken ek parametreler aşağıda verilmiştir:

- `Hbase.rs.cacheblocksonwrite` – varsayılan olarak HDI 'de, bu ayar **true**olarak ayarlanır.

- Daha sonra küçük sıkıştırmayı erteleme ayarlarına izin veren ayarlar.

- Okuma ve yazma istekleri için ayrılan sıraların yüzdelerini ayarlama gibi deneysel ayarlar.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuz çözümlenmemiş kalırsa, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

- Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

- [@No__t_1](https://twitter.com/azuresupport)bağlayın. Bu, müşteri deneyimini iyileştirmeye yönelik resmi Microsoft Azure hesabıdır. Azure Community 'yi doğru kaynaklara bağlar: yanıtlar, destek ve uzmanlar.

- Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)konusunu inceleyin. Microsoft Azure aboneliğiniz abonelik yönetimine ve faturalandırma desteğine erişim içerir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.
