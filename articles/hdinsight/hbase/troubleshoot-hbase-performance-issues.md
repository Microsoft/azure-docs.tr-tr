---
title: Azure HDInsight'ta Apache HBase performans sorunlarını giderme
description: Azure HDInsight 'ta en iyi performansı elde etmek için çeşitli Apache HBase performansı ayarlama yönergeleri ve ipuçları.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 466fac524601e2d569bfa0ccf90179fe9419210d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98942895"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Azure HDInsight'ta Apache HBase performans sorunlarını giderme

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
* `EXPLAIN`"Okumalarınızın" üretmesindeki sorgu planlarını anlamak için ifadesini kullandınız mı?
* Yazmalar "büyük/veya seçer" mı?
    * Bu durumda, taramalar de yapılır. Tarama için beklenen gecikme süresi yaklaşık 100 milisaniyede ortalanır ve nokta HBase 'e göre 10 milisaniyeye kıyasla.  

## <a name="test-methodology-and-metrics-monitoring"></a>Test metodolojisi ve ölçümleri izleme

Yahoo! gibi değerlendirmeler kullanıyorsanız Kıyaslama, JMeter veya Fehataişlev hizmeti sunan bulut, performansı test etmek ve ayarlamak için şunları yaptığınızdan emin olun:

- İstemci makineler bir performans sorunu olmaz. Bunu yapmak için, istemci makinelerdeki CPU kullanımını denetleyin.

- İş parçacığı sayısı gibi istemci tarafı yapılandırmaların, istemci bant genişliğine göre doygunluğu için uygun şekilde ayarlanmış olması.

- Test sonuçları doğru ve sistematik olarak kaydedilir.

Sorgularınızı aniden daha kötüleştikten sonra uygulama kodunuzda olası hataları kontrol edin. Çok büyük miktarlarda geçersiz veri oluşturuyor mu? Varsa, okuma gecikmeleri artırabilir.

## <a name="migration-issues"></a>Geçiş sorunları

Azure HDInsight 'a geçiş yapıyorsanız geçişinizin sistematik ve doğru şekilde, tercihen Otomasyon aracılığıyla yapıldığından emin olun. El ile geçişten kaçının. Şunlardan emin olun:

- Tablo öznitelikleri doğru şekilde geçirilir. Öznitelikler, Compression, Bloom filtreleri vb. içerebilir.

- Phoenix tablolarındaki salıcı ayarları, yeni küme boyutuna uygun şekilde eşlenir. Örneğin, anahtar demetlerinin sayısı kümedeki çalışan düğümü sayısının katı olmalıdır. Ve etkin biriktirme miktarı faktörü olan bir çoklu değer kullanmanız gerekir.

## <a name="server-side-configuration-tunings"></a>Sunucu tarafı yapılandırma tunları

HDInsight HBase 'de, HFiles uzak depolama üzerinde depolanır. Önbellek kaçırılması durumunda, şirket içi sistemlerdeki veriler yerel olarak korunduğundan, okuma maliyeti şirket içi sistemlerden daha yüksektir. Çoğu senaryoda, HBase önbelleklerinin (blok önbelleği ve demet önbelleği) akıllı kullanımı bu sorunu aşmak için tasarlanmıştır. Sorunun atlalanamadığı durumlarda, Premium Blok Blobu hesabı kullanmak bu soruna yardımcı olabilir. Windows Azure Depolama Blobu sürücüsü, `fs.azure.read.request.size` okuma modunu (sıralı olarak rastgele) temel alarak blok halinde veri getirmek için gibi belirli özelliklere bağımlıdır. bu nedenle, okumaların daha yüksek gecikme sürelerinin örnekleri olmaya devam edebilir. Empırical denemeleri aracılığıyla okuma isteği blok boyutunu ( `fs.azure.read.request.size` ) 512 KB olarak ayarlamanın yanı sıra HBase tablolarının blok boyutunu aynı boyutta olacak şekilde ayarlamaya yönelik en iyi sonucu elde ettik.

Çoğu büyük boyutlu düğümler kümesi için HDInsight HBase, `bucketcache` çalıştıran sanal makineye bağlı yerel bir Premium SSD dosya olarak sağlar `regionservers` . Bunun yerine yığın dışı önbelleğin kullanılması bazı geliştirme sağlayabilir. Bu geçici çözüm, kullanılabilir bellek kullanımı ve muhtemelen dosya tabanlı önbellekten daha küçük olabilecek bir sınırlamaya sahiptir, bu nedenle her zaman en iyi seçim olmayabilir.

Aşağıda, belirlediğimiz diğer özel parametrelerden bazıları ve değişen derecenin sağlanmasına yardımcı olmak için verilmiştir:

- `memstore`Boyutu varsayılan 128 MB ile 256 MB arasında artırın. Genellikle, bu ayar ağır yazma senaryolarında önerilir.

- Varsayılan değer olan **1** ' den **4**' e kadar sıkıştırma için ayrılan iş parçacıklarının sayısını artırın. Sık karşılaşılan küçük işlemleri gözlemlememiz durumunda bu ayar geçerlidir.

- `memstore`Depolama sınırı nedeniyle temizlemeyi engellemeyi önleyin. Bu arabelleği sağlamak için `Hbase.hstore.blockingStoreFiles` ayarı **100** olarak arttırın.

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
   - `hbase.client.scanner.caching`Parametresinin hem sunucu ucunda hem de istemci ucunda aynı değere ayarlandığından emin olun. Aynı değillerse, bu ayar ile ilgili istemci-uç hatalarına yol açar `OutOfOrderScannerException` . Bu ayar büyük taramalar için düşük bir değere ayarlanmalıdır. Bu değer **100** olarak ayarlanmıştır.

## <a name="other-considerations"></a>Diğer önemli noktalar

Ayarlamayı göz önünde bulundurmanız gereken ek parametreler aşağıda verilmiştir:

- `Hbase.rs.cacheblocksonwrite` – Varsayılan olarak HDI 'de, bu ayar **true** olarak ayarlanır.

- Daha sonra küçük sıkıştırmayı erteleme ayarlarına izin veren ayarlar.

- Okuma ve yazma istekleri için ayrılan sıraların yüzdelerini ayarlama gibi deneysel ayarlar.

## <a name="next-steps"></a>Sonraki adımlar

Sorununuz çözümlenmemiş kalırsa, daha fazla destek için aşağıdaki kanallardan birini ziyaret edin:

- Azure [topluluk desteği](https://azure.microsoft.com/support/community/)aracılığıyla Azure uzmanlarından yanıt alın.

- İle bağlanın [@AzureSupport](https://twitter.com/azuresupport) . Bu, müşteri deneyimini iyileştirmeye yönelik resmi Microsoft Azure hesabıdır. Azure Community 'yi doğru kaynaklara bağlar: yanıtlar, destek ve uzmanlar.

- Daha fazla yardıma ihtiyacınız varsa [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)bir destek isteği gönderebilirsiniz. Menü çubuğundan **destek** ' i seçin veya **Yardım + Destek** hub 'ını açın. Daha ayrıntılı bilgi için [Azure destek isteği oluşturma](../../azure-portal/supportability/how-to-create-azure-support-request.md)konusunu inceleyin. Microsoft Azure aboneliğiniz abonelik yönetimine ve faturalandırma desteğine erişim içerir ve [Azure destek planlarından](https://azure.microsoft.com/support/plans/)biri aracılığıyla teknik destek sağlanır.