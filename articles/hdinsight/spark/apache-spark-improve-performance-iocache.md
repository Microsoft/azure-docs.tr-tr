---
title: Apache Spark performans-Azure HDInsight GÇ önbelleği (Önizleme)
description: Azure HDInsight GÇ önbelleği ve Apache Spark performansını geliştirmek için nasıl kullanılacağı hakkında bilgi edinin.
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/23/2019
ms.openlocfilehash: 9df585c102e2c7307e949e38b6b69147372c38dd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866310"
---
# <a name="improve-performance-of-apache-spark-workloads-using-azure-hdinsight-io-cache"></a>Azure HDInsight GÇ önbelleği 'ni kullanarak Apache Spark iş yüklerinin performansını iyileştirme

GÇ önbelleği, Azure HDInsight için Apache Spark işlerin performansını artıran bir veri önbelleğe alma hizmetidir. GÇ önbelleği Ayrıca [Apache TEZ](https://tez.apache.org/) ve [Apache Hive](https://hive.apache.org/) iş yükleri ile birlikte çalışarak [Apache Spark](https://spark.apache.org/) kümelerinde çalıştırılabilir. GÇ önbelleği, RubiX adlı açık kaynaklı bir önbelleğe alma bileşeni kullanır. RubiX, bulut depolama sistemlerinden verilere erişen büyük veri analizi altyapılarıyla birlikte kullanılmak üzere yerel bir disk önbelleğidir. RubiX, önbelleğe alma amacıyla işletim belleği ayırmak yerine Solid-State sürücüleri (SSD 'Ler) kullandığından, önbelleğe alma sistemleri arasında benzersizdir. GÇ önbellek hizmeti, kümenin her bir çalışan düğümünde RubiX meta veri sunucularını başlatır ve yönetir. Ayrıca, RubiX önbelleğinin saydam kullanımı için kümenin tüm hizmetlerini yapılandırır.

Çoğu SSDs, bant genişliğine göre 1 GB 'den daha fazlasını sağlar. Bu bant genişliği, işletim sistemi bellek içi dosya önbelleğinde tamamlanarak, Apache Spark gibi büyük veri işlem işleme altyapılarını yüklemek için yeterli bant genişliği sağlar. İşletim belleği, karışık gibi bellek bağımlı görevleri işlemek Apache Spark için kullanılabilir kalır. İşletim belleğinin dışlamalı kullanımı, Apache Spark en iyi kaynak kullanımı elde etmesine olanak tanır.  

> [!Note]  
> GÇ önbelleği Şu anda bir önbellek bileşeni olarak RubiX kullanıyor, ancak bu durum hizmetin gelecek sürümlerinde değişebilir. Lütfen GÇ önbellek arabirimlerini kullanın ve hiçbir bağımlılığı doğrudan RubiX uygulamasında kullanmayın.
>GÇ önbelleği, şu anda yalnızca Azure BLOB depolama ile desteklenir.

## <a name="benefits-of-azure-hdinsight-io-cache"></a>Azure HDInsight GÇ önbelleğinin avantajları

GÇ önbelleğinin kullanılması, Azure Blob depolamadan veri okuyan işler için bir performans artışı sağlar.

GÇ önbelleği kullanırken performans artışına bakmak için Spark işleriniz üzerinde herhangi bir değişiklik yapmanız gerekmez. GÇ önbelleği devre dışı bırakıldığında, bu Spark kodu verileri Azure Blob depolamadan uzaktan okur: `spark.read.load('wasbs:///myfolder/data.parquet').count()` . GÇ önbelleği etkinleştirildiğinde, aynı kod satırı, önbelleğe alınan okuma GÇ önbelleğinde neden olur. Aşağıdaki okumalar, veriler SSD 'den yerel olarak okunur. HDInsight kümesindeki çalışan düğümleri yerel olarak eklenmiş, ayrılmış SSD sürücüleriyle donatılmıştır. HDInsight GÇ önbelleği, en düşük gecikme düzeyi ve bant genişliğini en üst düzeye çıkaran önbelleğe alma için bu yerel SSD 'leri kullanır.

## <a name="getting-started"></a>Kullanmaya başlama

Azure HDInsight GÇ önbelleği, önizleme aşamasında varsayılan olarak devre dışıdır. GÇ önbelleği, 2,3 Apache Spark çalıştıran Azure HDInsight 3.6 + Spark kümelerinde kullanılabilir.  HDInsight 4,0 ' de GÇ önbelleği 'ni etkinleştirmek için aşağıdaki adımları uygulayın:

1. Bir Web tarayıcısından, `https://CLUSTERNAME.azurehdinsight.net` , `CLUSTERNAME` Kümenizin adı olan ' a gidin.

1. Sol taraftaki **GÇ önbellek** hizmetini seçin.

1. **Eylemleri** (hdı 3,6 ' de **hizmet eylemleri** ) ve **Etkinleştir**' i seçin.

    :::image type="content" source="./media/apache-spark-improve-performance-iocache/ambariui-enable-iocache.png " alt-text="Ambarı 'nda GÇ önbellek hizmetini etkinleştirme" border="true":::

1. Kümedeki tüm etkilenen hizmetlerin yeniden başlatılmasını onaylayın.

> [!NOTE]  
> İlerleme çubuğu etkin ' i gösterdiği halde, etkilenen diğer hizmetleri yeniden başlatana kadar GÇ önbelleği gerçekten etkinleştirilmez.

## <a name="troubleshooting"></a>Sorun giderme
  
GÇ önbelleğini etkinleştirdikten sonra Spark işlerini çalıştıran disk alanı hatalarını alabilirsiniz. Bu hatalar, Spark işlemleri sırasında verileri depolamak için yerel disk depolaması da kullandığından oluşur. GÇ önbelleği etkinleştirildikten ve Spark Storage alanı azaltıldıktan sonra Spark 'ın SSD alanı tükeniyor olabilir. GÇ önbelleği tarafından kullanılan alan miktarı varsayılan olarak toplam SSD alanının yarısını alır. GÇ önbelleği için disk alanı kullanımı, ambarı 'nda yapılandırılabilir. Disk alanı hatalarını alırsanız, GÇ önbelleği için kullanılan SSD alanı miktarını azaltın ve hizmeti yeniden başlatın. GÇ önbelleğinin alan kümesini değiştirmek için aşağıdaki adımları uygulayın:

1. Apache ambarı ' **nda, sol taraftaki bu hizmeti seçin** .

1. **Configs** ve **Gelişmiş** sekmelerini seçin.

    :::image type="content" source="./media/apache-spark-improve-performance-iocache/ambariui-hdfs-service-configs-advanced.png " alt-text="Bir Gelişmiş yapılandırmayı Düzenle" border="true":::

1. Aşağı kaydırın ve **özel çekirdek sitesi** alanını genişletin.

1. **Hadoop. cache. Data. fulllik. Percentage** özelliğini bulun.

1. Kutudaki değeri değiştirin.

    :::image type="content" source="./media/apache-spark-improve-performance-iocache/ambariui-cache-data-fullness-percentage-property.png " alt-text="GÇ önbelleği tamlık yüzdesini Düzenle" border="true":::

1. Sağ üst köşedeki **Kaydet** ' i seçin.

1. **Yeniden** Başlat ' ı seçin  >  **Tümünü etkilendi**.

    :::image type="content" source="./media/apache-spark-improve-performance-iocache/ambariui-restart-all-affected.png " alt-text="Apache ambarı etkilenen tümünü yeniden başlatır" border="true":::

1. **Tümünü yeniden başlatmayı Onayla** seçeneğini belirleyin.

Bu işe yaramazsa, GÇ önbelleğini devre dışı bırakın.

## <a name="next-steps"></a>Sonraki Adımlar

Bu blog gönderisine yönelik performans kıyaslamaları dahil olmak üzere GÇ önbelleği hakkında daha fazla bilgi edinin: [Apache Spark İşler, HDıNSIGHT GÇ önbelleğiyle hızlanır](https://azure.microsoft.com/blog/apache-spark-speedup-with-hdinsight-io-cache/)
