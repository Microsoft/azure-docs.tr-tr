---
title: "Veri geçişi: Azure HDInsight 'a şirket içi Apache Hadoop"
description: Şirket içi Hadoop kümelerini Azure HDInsight 'a geçirmek için veri geçişi en iyi yöntemlerini öğrenin.
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: hrasheed
ms.openlocfilehash: 30f7ae2eeb928e3f8dc71baed20d9c9b2129d1f9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494983"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>Şirket içi Apache Hadoop kümelerini Azure HDInsight 'a geçirme-veri geçişi en iyi yöntemleri

Bu makale, Azure HDInsight 'a veri taşımaya yönelik öneriler sağlar. Şirket içi Apache Hadoop sistemlerini Azure HDInsight 'a geçirmeye yardımcı olmak için en iyi uygulamaları sağlayan bir serinin bir parçasıdır.

## <a name="migrate-on-premises-data-to-azure"></a>Şirket içi verileri Azure 'a geçirme

Şirket içinden Azure ortamına veri geçirmek için iki ana seçenek vardır:

1.  TLS ile ağ üzerinden veri aktarımı
    1. Internet üzerinden: Azure Depolama Gezgini, AzCopy, Azure PowerShell ve Azure CLı gibi çeşitli araçlardan birini kullanarak düzenli bir internet bağlantısı üzerinden Azure depolama 'ya veri aktarabilirsiniz.  Daha fazla bilgi için bkz. [Azure Storage 'a veri taşıma](../../storage/common/storage-moving-data.md) .
    2. Express Route-ExpressRoute, Microsoft veri merkezleri ile şirket içinde veya bir birlikte bulundurma tesisinde bulunan altyapı arasında özel bağlantılar oluşturmanızı sağlayan bir Azure hizmetidir. ExpressRoute bağlantıları, genel Internet üzerinden geçmez ve Internet üzerinden tipik bağlantılardan daha düşük gecikme süreleriyle daha yüksek güvenlik, güvenilirlik ve hız sunar. Daha fazla bilgi için bkz. [ExpressRoute bağlantı hattı oluşturma ve değiştirme](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    1. Data Box çevrimiçi veri aktarımı-Data Box Edge ve Data Box Gateway, siteniz ile Azure arasında verileri yönetmek için ağ depolama ağ geçitleri görevi gören çevrimiçi veri aktarımı ürünlerdir. Şirket içi bir ağ cihazı olan Data Box Edge, Azure’ın içine ve dışına veri aktarımı gerçekleştirmesinin yanı sıra verileri işlemek için yapay zeka (AI) özellikli uç işlemini kullanır. Data Box Gateway, depolama ağ geçidi özelliklerine sahip sanal bir gereçtir. Daha fazla bilgi için bkz. [Azure Data Box belgeleri-çevrimiçi aktarım](https://docs.microsoft.com/azure/databox-online/).
1.  Verileri çevrimdışı aktarma
    1. Data Box çevrimdışı veri aktarımı-Data Box, Data Box Disk ve Data Box Heavy cihazları, ağ bir seçenek olmadığında büyük miktarlarda verileri Azure 'a aktarmanızı sağlar. Bu çevrimdışı veri aktarım cihazları kuruluşunuz ile Azure veri merkezi arasında çift yönlü olarak sevk edilebilir. Bunlar aktarım sırasında verilerinizin korunmasına yardımcı olmak için AES şifrelemesi kullanır ve karşıya yükleme sonrası temizlik işlemine tabi tutularak verileriniz cihazdan silinir. Data Box çevrimdışı aktarım cihazları hakkında daha fazla bilgi için bkz. [Azure Data Box belgeleri-çevrimdışı aktarım](https://docs.microsoft.com/azure/databox/). Hadoop kümelerinin geçirilmesi hakkında daha fazla bilgi için bkz. [on-premises bir sunucudan Azure Storage 'a geçiş yapmak için Azure Data Box kullanma](../../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md).

Aşağıdaki tabloda, veri hacmi ve ağ bant genişliğine bağlı olarak yaklaşık veri aktarım süresi bulunur. Veri geçişinin üç haftadan uzun sürmesine bekleniyorsa bir veri kutusu kullanın.

|**Veri mik**|**Ağ bant genişliği**||||
|---|---|---|---|---|
|| **45 Mbps (T3)**|**100 Mbps**|**1 Gbps**|**10 Gbps**|
|1 TB|2 gün|1 gün| 2 saat|14 dakika|
|10 TB|22 gün|10 gün|1 gün|2 saat|
|35 TB|76 gün|34 gün|3 gün|8 saat|
|80 TB|173 gün|78 gün|8 gün|19 saat|
|100 TB|216 gün|97 gün|10 gün|1 gün|
|200 TB|1 yıl|194 gün|19 gün|2 gün|
|500 TB|3 yıl|1 yıl|49 gün|5 gün|
|1 PB|6 yıl|3 yıl|97 gün|10 gün|
|2 PB|12 yıl|5 yıl|194 gün|19 gün|

Azure 'da yerel olan ve Apache Hadoop DistCp, Azure Data Factory ve AzureCp gibi araçlar, ağ üzerinden veri aktarmak için kullanılabilir. Ayrıca, üçüncü taraf aracı WANDisco aynı amaçla kullanılabilir. Apache Kafka Mirrormaker ve Apache Sqoop, Şirket içinden Azure depolama sistemlerine devam eden veri aktarımı için kullanılabilir.


## <a name="performance-considerations-when-using-apache-hadoop-distcp"></a>Apache Hadoop Dıtcp kullanırken performans konuları


DistCp, verileri aktarmak, hataları işlemek ve bu hatalardan kurtarmak için MapReduce eşleme işi kullanan bir Apache projem. Her eşleme görevine bir kaynak dosyaları listesi atar. Eşleme görevi bundan sonra atanan tüm dosyaları hedefe kopyalar. Birçok teknik, DistCp performansını iyileştirebilirler.

### <a name="increase-the-number-of-mappers"></a>Mapto sayısını artırma

Detcp, her bir kopyanın kabaca aynı bayt sayısına eşit olması için eşleme görevleri oluşturmaya çalışır. Varsayılan olarak, DistCp işleri 20 mapto kullanır. Distcp için daha fazla Maplıya (komut satırında 'm parametresi ile) kullanmak, veri aktarım işlemi sırasında paralellik düzeyini artırır ve veri aktarımının uzunluğunu azaltır. Ancak, Mapıı sayısını artırırken dikkate alınması gereken iki şey vardır:

1. Detcp 'nin en düşük ayrıntı düzeyi tek bir dosyadır. Kaynak dosya sayısından daha fazla sayıda Mapbir eşleme belirtmek yardımcı değildir ve kullanılabilir küme kaynaklarını boşa karşılacaktır.
1. Mapçların sayısını öğrenmek için kümede kullanılabilir Yarn belleğini göz önünde bulundurun. Her harita görevi bir Yarn kapsayıcısı olarak başlatılır. Kümede başka bir ağır iş yükünün çalışmadığını varsayarsak, Mapcontroller sayısı şu formül tarafından belirlenebilir: d = (her çalışan düğümü için YARN bellek \* çalışan düğüm sayısı)/YARN kapsayıcı boyutu. Ancak, diğer uygulamalar bellek kullanıyorsa, DistCp işleri için yalnızca YARN belleğin bir kısmını kullanmayı seçin.

### <a name="use-more-than-one-distcp-job"></a>Birden fazla DistCp işi kullanın

Taşınacak veri kümesinin boyutu 1 TB 'den büyükse, birden fazla DistCp işi kullanın. Birden fazla işin kullanılması, hataların etkisini sınırlar. Herhangi bir iş başarısız olursa, tüm işler yerine yalnızca belirli bir işi yeniden başlatmanız gerekir.

### <a name="consider-splitting-files"></a>Dosyaları bölmeyi göz önünde bulundurun

Az sayıda büyük dosya varsa, daha fazla mapa ile daha fazla eşzamanlılık sağlamak için bunları 256 MB dosya öbeklere bölmeyi göz önünde bulundurun.

### <a name="use-the-strategy-command-line-parameter"></a>' Strateji ' komut satırı parametresini kullanın

Komut satırında `strategy = dynamic` parametresi kullanmayı düşünün. `strategy` parametresinin varsayılan değeri `uniform size`, bu durumda her eşleme kabaca aynı sayıda bayt olarak kopyalanır. Bu parametre `dynamic`olarak değiştirildiğinde, liste dosyası birkaç "öbek dosyası" olarak bölünür. Öbek dosyalarının sayısı, haritalar sayısının birden çok sayısıdır. Her eşleme görevi, öbek dosyalarından birine atanır. Bir öbekteki tüm yollar işlendikten sonra, geçerli öbek silinir ve yeni bir öbek elde edilir. İşlem, başka bir öbek kullanılabilir olana kadar devam eder. Bu "dinamik" yaklaşım daha hızlı eşleme görevlerinin daha yavaş yollardan daha fazla yol kullanmasına olanak sağlar. böylece, genel olarak DistCp işini hızlanın.

### <a name="increase-the-number-of-threads"></a>İş parçacığı sayısını artırma

`-numListstatusThreads` parametresinin artması performansı artırdığından bkz. Bu parametre, dosya listesi oluşturmak için kullanılacak iş parçacığı sayısını denetler ve 40 en büyük değerdir.

### <a name="use-the-output-committer-algorithm"></a>Çıkış komter algoritmasını kullanın

`-Dmapreduce.fileoutputcommitter.algorithm.version=2` parametre geçirmenin, performansı artırdığı konusunda bilgi için bkz. Bu çıkış komter algoritması, çıkış dosyalarını hedefe yazma etrafında iyileştirmelere sahiptir. Aşağıdaki komut, farklı parametrelerin kullanımını gösteren bir örnektir:

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>Meta veri geçişi

### <a name="apache-hive"></a>Apache Hive

Hive meta veri deposu, betikleri kullanılarak veya DB çoğaltması kullanılarak geçirilebilir.

#### <a name="hive-metastore-migration-using-scripts"></a>Betikleri kullanarak geçiş Hive meta veri deposu

1. Şirket içi Hive meta veri deposu Hive DDLs 'Leri oluşturun. Bu adım, [sarmalayıcı Bash betiği](https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md)kullanılarak yapılabilir.
1. Bir üretilen DDL 'yi, bu edb/ADLS/ABFS URL 'Leriyle, bu URL 'yi değiştirin.
1. HDInsight kümesinden, meta veri deposu üzerinde güncelleştirilmiş DDL 'yi çalıştırın.
1. Hive meta veri deposu sürümünün şirket içi ve bulut arasında uyumlu olduğundan emin olun.

#### <a name="hive-metastore-migration-using-db-replication"></a>DB çoğaltma kullanarak geçiş Hive meta veri deposu

- Şirket içi Hive meta veri deposu DB ile HDInsight meta veri deposu DB arasında veritabanı çoğaltmasını ayarlayın.
- Ifabricurl 'sini IDB/ADLS/ABFS URL 'leri ile değiştirmek için "Hive MetaTool" kullanın, örneğin:

```bash
./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
```

### <a name="apache-ranger"></a>Apache Ranger

- Şirket içi Ranger ilkelerini XML dosyalarına dışarı aktarın.
- XSLT gibi bir araç kullanarak, şirket için belirli bir diğer ad tabanlı yollardaki/ADLS 'ye dönüştürme.
- Içindeki ilkeleri HDInsight üzerinde çalışan Ranger 'a aktarın.

## <a name="next-steps"></a>Sonraki adımlar

Bu serideki bir sonraki makaleyi okuyun:

- [Azure HDInsight Hadoop geçiş için şirket içi güvenlik ve DevOps en iyi uygulamaları](apache-hadoop-on-premises-migration-best-practices-security-devops.md)
