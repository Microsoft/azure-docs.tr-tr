---
title: Azure HDInsight 'ta Apache HBase 'i Apache ambarı ile iyileştirme
description: Apache HBase 'i yapılandırmak ve iyileştirmek için Apache ambarı Web Kullanıcı arabirimini kullanın.
ms.service: hdinsight
ms.topic: how-to
ms.date: 02/01/2021
ms.openlocfilehash: 7e54b1347e4c67b99ba87b15c2c15d9d28244ce7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864780"
---
# <a name="optimize-apache-hbase-with-apache-ambari-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache HBase 'i Apache ambarı ile iyileştirme

Apache ambarı, HDInsight kümelerini yönetmek ve izlemek için bir Web arabirimidir. Ambarı Web Kullanıcı arabirimine giriş için bkz. [Apache ambarı Web Kullanıcı arabirimini kullanarak HDInsight kümelerini yönetme](hdinsight-hadoop-manage-ambari.md).

Apache HBase yapılandırması **HBase configs** sekmesinden değiştirilir. Aşağıdaki bölümlerde, HBase performansını etkileyen bazı önemli yapılandırma ayarları açıklanır.

## <a name="set-hbase_heapsize"></a>HBASE_HEAPSIZE ayarla

> [!NOTE]
> Bu makale, Microsoft 'un artık kullanmadığını belirten bir terim olan *ana* terim başvurularını içerir. Terim yazılımlardan kaldırıldığında, bu makaleden kaldıracağız.

HBase yığın boyutu, *bölgeye* ve *ana* sunuculara göre megabayt cinsinden kullanılacak maksimum yığın miktarını belirtir. Varsayılan değer 1.000 MB 'tır. Bu değerin küme iş yükü için ayarlanmış olması gerekir.

1. Değiştirmek için, HBase **configs** sekmesindeki **Gelişmiş HBase-env** bölmesine gidin ve `HBASE_HEAPSIZE` ayarı bulun.

1. Varsayılan değeri 5.000 MB olarak değiştirin.

    :::image type="content" source="./media/optimize-hbase-ambari/ambari-hbase-heapsize.png" alt-text="' Apache ambarı HBase belleği heapsize '" border="true":::

## <a name="optimize-read-heavy-workloads"></a>Okuma ağır iş yüklerini iyileştirme

Okuma ağır iş yüklerinin performansını artırmak için aşağıdaki konfigürasyonlar önemlidir.

### <a name="block-cache-size"></a>Blok önbelleği boyutu

Blok önbelleği, okuma önbelleğidir. Boyutu parametresi tarafından denetlenir `hfile.block.cache.size` . Varsayılan değer, toplam bölge sunucu belleğinin yüzde 40 ' i olan 0,4 ' dir. Blok önbelleği boyutunun ne kadar büyük olması, rastgele okumaların daha hızlı olması olur.

1. Bu parametreyi değiştirmek için, HBase **configs** sekmesindeki **Ayarlar** sekmesine gidin ve ardından **okuma arabelleklerine ayrılan regionserver 'ın%**' i bulun.

    :::image type="content" source="./media/optimize-hbase-ambari/hbase-block-cache-size.png" alt-text="Apache HBase bellek bloğu önbellek boyutu" border="true":::

1. Değeri değiştirmek için **Düzenle** simgesini seçin.

### <a name="memstore-size"></a>Memstore boyutu

Tüm düzenlemeler, bellek arabelleğinde saklanır, bu, *Memstore* olarak adlandırılır. Bu arabellek, tek bir işlemde diske yazılabilen toplam veri miktarını artırır. Ayrıca son düzenlemelere erişimi de hızlandırır. Memstore boyutu aşağıdaki iki parametre tarafından tanımlanır:

* `hbase.regionserver.global.memstore.UpperLimit`: Alt veri deposu 'nun kullanabileceği en yüksek bölge sunucusu yüzdesini tanımlar.

* `hbase.regionserver.global.memstore.LowerLimit`: Alt veri deposu 'nun kullanabileceği bölge sunucusu için minimum yüzdeyi tanımlar.

Rastgele okumaları iyileştirmek için, Memstore üst ve alt sınırlarını azaltabilirsiniz.

### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Diskten tarama sırasında getirilen satır sayısı

Bu `hbase.client.scanner.caching` ayar, `next` Yöntem bir tarayıcıda çağrıldığında diskten okunan satır sayısını tanımlar.  Varsayılan değer 100’dür. Sayı arttıkça, istemciden bölge sunucusuna yapılan uzak çağrılar daha az tarama elde edilir. Ancak, bu ayar istemcideki bellek basıncını de artırır.

:::image type="content" source="./media/optimize-hbase-ambari/hbase-num-rows-fetched.png" alt-text="Getirilen Apache HBase satır sayısı" border="true":::

> [!IMPORTANT]  
> Bir tarayıcıdaki sonraki yöntemin çağrılması ile ilgili saatin tarayıcı zaman aşımından daha büyük olması için değeri ayarlamayın. Tarayıcı zaman aşımı süresi, özelliği tarafından tanımlanır `hbase.regionserver.lease.period` .

## <a name="optimize-write-heavy-workloads"></a>Yazma ağır iş yüklerini iyileştirme

Aşağıdaki konfigürasyonlar, yazma ağır iş yüklerinin performansını artırmak için önemlidir.

### <a name="maximum-region-file-size"></a>En büyük bölge dosyası boyutu

HBase verileri *hfile* adlı dahili bir dosya biçiminde depolar. Özelliği, `hbase.hregion.max.filesize` bir bölge için tek bir HFile boyutunu tanımlar.  Bir bölgedeki tüm HFiles değerlerinin bu ayardan büyük olması halinde bölge iki bölgeye ayrılır.

:::image type="content" source="./media/optimize-hbase-ambari/hbase-hregion-max-filesize.png" alt-text="' Apache HBase HRegion en fazla dosya boyutu '" border="true":::

Bölge dosya boyutu ne kadar büyükse, bölme sayısı küçülür. En fazla yazma performansına neden olan bir değeri öğrenmek için dosya boyutunu artırabilirsiniz.

### <a name="avoid-update-blocking"></a>Güncelleştirme engellemeyi önleyin

* Özelliği, `hbase.hregion.memstore.flush.size` Memstore 'nin diske boşaltılmakta olduğu boyutu tanımlar. Varsayılan boyut 128 MB 'tır.

* HBase bölgesi blok çarpanı tarafından tanımlanır `hbase.hregion.memstore.block.multiplier` . Varsayılan değer 4'tür. İzin verilen en fazla 8.

* Memstore () bayt ise, HBase tarafından güncelleştirmeler engellenir `hbase.hregion.memstore.flush.size`  *  `hbase.hregion.memstore.block.multiplier` .

    Varsayılan Temizleme boyutu ve blok çarpanı değerleri ile, Memstore boyutu 128 * 4 = 512 MB olduğunda güncelleştirmeler engellenir. Güncelleştirme engelleme sayısını azaltmak için değerini artırın `hbase.hregion.memstore.block.multiplier` .

:::image type="content" source="./media/optimize-hbase-ambari/hbase-hregion-memstore-block-multiplier.png" alt-text="Apache HBase bölgesi blok çarpanı" border="true":::

## <a name="define-memstore-size"></a>Memstore boyutunu tanımla

Memstore boyutu ve parametreleri tarafından tanımlanır `hbase.regionserver.global.memstore.upperLimit` `hbase.regionserver.global.memstore.lowerLimit` . Bu değerlerin birbirlerine eşit ayarlanması yazma sırasında duraklar azaltır (Ayrıca daha sık temizlemeye devam ediyor) ve daha fazla yazma performansı elde edin.

## <a name="set-memstore-local-allocation-buffer"></a>Memstore yerel ayırma arabelleğini ayarla

Memstore yerel ayırma arabelleği kullanımı, özelliği tarafından belirlenir `hbase.hregion.memstore.mslab.enabled` . Etkinleştirildiğinde (true), bu ayar ağır yazma işlemi sırasında yığın parçalanmasını önler. Varsayılan değer true şeklindedir.

:::image type="content" source="./media/optimize-hbase-ambari/hbase-hregion-memstore-mslab-enabled.png" alt-text="HBase. hregion. memstore. mslab. Enabled" border="true":::

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight kümelerini Apache ambarı Web Kullanıcı arabirimi ile yönetme](hdinsight-hadoop-manage-ambari.md)
* [Apache ambarı REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Kümeleri iyileştirme](./hdinsight-changing-configs-via-ambari.md)
* [Apache Hive’ı iyileştirme](./optimize-hive-ambari.md)
* [Apache Pig’i iyileştirme](./optimize-pig-ambari.md)
