---
title: Küme Danışmanı önerilerini iyileştirme
titleSuffix: Azure HDInsight
description: Azure HDInsight 'ta küme Danışmanı önerilerini için Apache HBase 'i iyileştirin.
author: ramkrish86
ms.author: ramvasu
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/03/2021
ms.openlocfilehash: acb7a6aeb4084949be3b0ad40e770a414a13ab6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943013"
---
# <a name="apache-hbase-advisories-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache HBase Danışma belgeleri

Bu makalede, Azure HDInsight 'ta Apache HBase performansını en iyi hale getirmenize yardımcı olacak çeşitli Danışma belgeleri açıklanmaktadır. 

## <a name="optimize-hbase-to-read-most-recently-written-data"></a>En son yazılan verileri okumak için HBase 'i iyileştirme

UseCase 'in HBase 'den en son yazılan verileri okumasını içeriyorsa, bu danışmanlık size yardımcı olabilir. Yüksek performans için, HBase okumaların uzak depolama yerine memstore 'den sunulması idealdir.

Sorgu danışmanlığı, bir tablodaki belirli bir sütun ailesi için, memstore 'tan sunulan %75 okuma > olduğunu gösterir. Bu gösterge, memstore üzerinde bir temizleme gerçekleşse bile son dosyanın erişilmesi ve önbellekte olması gerekir. Veriler ilk olarak memstore 'e yazılır ve sistem burada en son verilere erişir. İç HBase flusher iş parçacıklarının, belirli bir bölgenin 128M (varsayılan) boyutuna ulaştığından ve bir temizlemeyi tetikleyebildiği bir şansınız vardır. Bu senaryo, memstore boyut olarak 128Gb etrafında olduğunda yazılan en son veriler bile olur. Bu nedenle, bu son kayıtların daha sonra okunması, memstore yerine bir dosya okuması gerektirebilir. Bu nedenle, son olarak temizlenen son verilerin önbellekte yer alabilse de iyileştirmek en iyisidir.

Önbellekteki son verileri iyileştirmek için aşağıdaki yapılandırma ayarlarını göz önünde bulundurun:

1. `hbase.rs.cacheblocksonwrite`Olarak ayarlayın `true` . HDInsight HBase 'deki bu varsayılan yapılandırma olduğundan `true` , ' nin ' e sıfırlanmadığından emin olun `false` .

2. `hbase.hstore.compactionThreshold`Bu değeri artırarak, ' ın içinden düzenleme yapmaktan kaçınabilirsiniz. Varsayılan olarak bu `3` değerine ayarlanır. Bunu, gibi daha yüksek bir değere artırabilirsiniz `10` .

3. 2. adım ve compactionThreshold ayarlarsanız, daha `hbase.hstore.compaction.max` yüksek bir değere geçiş yapın `100` ve ayrıca, örneğin, yapılandırmaya ait değeri `hbase.hstore.blockingStoreFiles` daha yüksek değere yükseltin `300` .

4. Yalnızca son verileri okuduğunuzdan emin değilseniz, `hbase.rs.cachecompactedblocksonwrite` yapılandırmayı **Açık** olarak ayarlayın. Bu yapılandırma sisteme, sıkıştırma gerçekleşse bile verilerin önbellekte kalmasını söyler. Konfigürasyonlar Ayrıca aile düzeyinde ayarlanabilir. 

   HBase kabuğunda, yapılandırmayı ayarlamak için aşağıdaki komutu çalıştırın `hbase.rs.cachecompactedblocksonwrite` :
   
   ```
   alter '<TableName>', {NAME => '<FamilyName>', CONFIGURATION => {'hbase.hstore.blockingStoreFiles' => '300'}}
   ```

5. Blok önbelleği, bir tablodaki belirli bir aile için kapatılabilir. En son veri okuma özelliği olan **aileleri için açık** olduğundan emin olun. Varsayılan olarak, blok önbelleği bir tablodaki tüm aileler için açıktır. Bir aile için blok önbelleğini devre dışı bırakmış ve açmanız gerekiyorsa, HBase kabuğu 'ndaki alter komutunu kullanın.

   Bu yapılandırma, verilerin önbellekte kullanılabilir olduğundan ve son verilerin sıkıştırmamadığından emin olmanıza yardımcı olur. Senaryonuz için bir TTL mümkünse, tarih katmanlı sıkıştırmayı kullanmayı düşünün. Daha fazla bilgi için bkz [. Apache HBase başvuru kılavuzu: Tarih katmanlı sıkıştırma](https://hbase.apache.org/book.html#ops.date.tiered)  

## <a name="optimize-the-flush-queue"></a>Temizleme kuyruğunu iyileştirme

Bu danışmanlık, HBase boşaltmalarının ayarlamaya ihtiyacı olabileceğini gösterir. Temizleme işleyicilerinin geçerli yapılandırması, Temizleme işlemlerinin yavaşlamasına neden olabilecek yazma trafiği ile başa çıkmasına yetecek kadar yüksek olmayabilir.

Bölge sunucusu kullanıcı arabiriminde, temizleme sırasının 100 ' den fazla büyüdiğine dikkat edin. Bu eşik, temizlemeleri yavaş olduğunu gösterir ve yapılandırmayı ayarlamanız gerekebilir   `hbase.hstore.flusher.count` . Varsayılan olarak değer 2 ' dir. En fazla flusher iş parçacıklarının 6 ' dan fazla artırmayın olduğundan emin olun.

Ayrıca, bölge sayısı ayarlama önerisine sahip olup olmadığını görebilirsiniz. Yanıt Evet ise, daha hızlı boşaltmaları konusunda yardımcı olup olmadığını görmek için bölge ayarlamayı denemenizi öneririz. Aksi takdirde, flusher iş parçacıklarını ayarlamak size yardımcı olabilir.

## <a name="region-count-tuning"></a>Bölge sayısı ayarlama

Bölge sayısı ayarlama danışmanlığı, HBase 'in güncelleştirmeleri engellediği ve bölge sayısının en iyi desteklenen yığın boyutundan daha fazla olabileceğini gösterir. Yığın boyutunu, memstore boyutunu ve bölge sayısını ayarlayabilirsiniz.

Örnek senaryo olarak:

- Bölge sunucusu için yığın boyutunun 10 GB olduğunu varsayalım. Varsayılan olarak, `hbase.hregion.memstore.flush.size` `128M` . İçin varsayılan değer `hbase.regionserver.global.memstore.size` `0.4` . Bu, memstore (Global) için 10 GB 'tan 4 GB ayrıldığı anlamına gelir.

- Tüm bölgelerde yazma yükünün eşit bir şekilde dağıtılması ve her bölgenin yalnızca 128 MB 'tan büyüdüğünü varsayarsak, Bu kurulumda en fazla bölge sayısı yalnızca bölge olarak belirlenir `32` . Belirli bir bölge sunucusu 32 bölgelere sahip olacak şekilde yapılandırıldıysa, sistem güncelleştirmeleri engellemeyi daha iyi önler.

- Bu ayarlar yerinde olduğunda bölge sayısı 100 ' dir. 4 GB genel memstore artık 100 bölge arasında bölünür. Böylece her bölge, memstore için yalnızca 40 MB alır. Yazmalar Tekdüzen olduğunda, sistem sık temizleme ve sıra boyutunu daha küçük olan 40 MB <. Birçok flusher iş parçacığına sahip olma, temizleme hızını artırabilir `hbase.hstore.flusher.count` .

Danışma belgesi sunucu başına bölge sayısını, yığın boyutunu ve genel memstore boyut yapılandırmasını ve güncelleştirmelerin engellenmesini önlemek için temizleme iş parçacıklarının ayarlamasıyla birlikte yeniden düşünmek iyi olacaktır.

## <a name="compaction-queue-tuning"></a>Sıkıştırma sırası ayarlama

HBase sıkıştırma sırası 2000 ' den fazla büyüdüğünde ve düzenli aralıklarla gerçekleşdiğinde, sıkıştırma iş parçacıklarını daha büyük bir değere artırabilirsiniz.

Sıkıştırma için çok sayıda dosya olduğunda, dosyaların Azure dosya sistemiyle nasıl etkileşime gireceğini ilgili daha fazla yığın kullanımına neden olabilir. Bu nedenle, düzenleme işleminin olabildiğince çabuk tamamlanabilmesi daha iyidir. Daha eski kümelerde bazı zamanlarda azaltma ile ilgili düzenleme yapılandırmalarının daha yavaş sıkıştırma hızına neden olabileceği.

Konfigürasyonları `hbase.hstore.compaction.throughput.lower.bound` ve ' i kontrol edin `hbase.hstore.compaction.throughput.higher.bound` . Zaten 50 GB ve 100 GB olarak ayarlandıysa, bunları olduğu gibi bırakın. Bununla birlikte, bu ayarları daha düşük bir değere (eski kümelerde olduğu gibi) yapılandırdıysanız, sınırları sırasıyla 50K ve 100D ile değiştirin.

Konfigürasyonlar `hbase.regionserver.thread.compaction.small` ve `hbase.regionserver.thread.compaction.large` (varsayılan değerler 1 ' dir).
Bu yapılandırmanın en büyük değeri 3 ' ten az olmalıdır.

## <a name="full-table-scan"></a>Tam tablo tarama

Tam tablo tarama Danışma belgesi, verilen taramaların %75 ' ından fazla tablo/bölge taramaları olduğunu gösterir. Sorgu performansını artırmak için kodunuzun taramaların nasıl çağrılacağını tekrar bulabilirsiniz. Aşağıdaki yöntemleri göz önünde bulundurun:

* Her tarama için uygun başlatma ve durdurma satırını ayarlayın.

* Farklı aralıkları tek bir tarama çağrısında sorgulayabilmeniz için **Multirowrangefilter** API 'sini kullanın. Daha fazla bilgi için bkz. [Multirowrangefilter API belgeleri](https://hbase.apache.org/2.1/apidocs/org/apache/hadoop/hbase/filter/MultiRowRangeFilter.html).

* Tam tablo veya bölge taramasına ihtiyaç duyduğunuz durumlarda, bu sorgular için önbellek kullanımından kaçınmanın mümkün olup olmadığını kontrol edin. böylece, önbellek kullanan diğer sorguların sık erişimli blokları çıkarmayabilir. Taramaların önbellek kullandığından emin olmak için, kodunuzda **Setcaching (false)** seçeneğiyle **Tara** API 'sini kullanın: 

   ```
   scan#setCaching(false)
   ```
   
## <a name="next-steps"></a>Sonraki adımlar

[Ambarı kullanarak Apache HBase 'i iyileştirme](../optimize-hbase-ambari.md)
