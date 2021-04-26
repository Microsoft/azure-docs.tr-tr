---
title: Kümeyi daha yeni bir sürüme geçir
titleSuffix: Azure HDInsight
description: Azure HDInsight kümenizi daha yeni bir sürüme geçirmeye yönelik yönergeleri öğrenin.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/31/2020
ms.openlocfilehash: 4aa25368e156ce793e969f866490352e253559fc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871733"
---
# <a name="migrate-hdinsight-cluster-to-a-newer-version"></a>HDInsight kümesini daha yeni bir sürüme geçir

En son HDInsight özelliklerinden yararlanmak için HDInsight kümelerinin düzenli olarak en son sürüme geçirilmesi önerilir. HDInsight, var olan bir kümenin daha yeni bir bileşen sürümüne yükseltildiği yerinde yükseltmeleri desteklemez. İstenen bileşen ve platform sürümü ile yeni bir küme oluşturmanız ve ardından yeni kümeyi kullanmak için uygulamalarınızı geçirmeniz gerekir. HDInsight küme sürümlerinizi geçirmek için aşağıdaki yönergeleri izleyin.

> [!NOTE]  
> HDInsight 'ın desteklenen sürümleri hakkında bilgi için bkz. [HDInsight bileşen sürümleri](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="migration-tasks"></a>Geçiş görevleri

HDInsight kümesini yükseltmek için iş akışı aşağıdaki gibidir.
:::image type="content" source="./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png" alt-text="HDInsight yükseltme iş akışı diyagramı" border="false":::

1. HDInsight kümenizi yükseltirken gerekebilecek değişiklikleri anlamak için bu belgenin her bölümünü okuyun.
2. Test/kalite güvencesi ortamı olarak bir küme oluşturun. Küme oluşturma hakkında daha fazla bilgi için bkz. [Linux tabanlı HDInsight kümeleri oluşturma](hdinsight-hadoop-provision-linux-clusters.md) hakkında bilgi edinin
3. Mevcut işleri, veri kaynaklarını ve havuzları yeni ortama kopyalayın.
4. İşlerinizin yeni kümede beklendiği gibi çalıştığından emin olmak için doğrulama testi gerçekleştirin.

Her şeyin beklendiği gibi çalıştığından emin olduktan sonra geçiş için kapalı kalma süresi zamanlayın. Bu kapalı kalma süresi boyunca aşağıdaki işlemleri yapın:

1. Küme düğümlerinde yerel olarak depolanan tüm geçici verileri yedekleyin. Örneğin, doğrudan bir baş düğümde depolanan verileriniz varsa.
1. [Mevcut kümeyi silin](./hdinsight-delete-cluster.md).
1. Önceki kümenin kullandığı varsayılan veri deposunu kullanarak en son (veya desteklenen) HDI sürümü ile aynı VNET alt ağında bir küme oluşturun. Bu, yeni kümenin mevcut üretim verilerinize karşı çalışmaya devam etmesine olanak tanır.
1. Yedeklediğiniz tüm geçici verileri içeri aktarın.
1. Yeni kümeyi kullanarak işleri başlatın/işleme devam edin.

## <a name="workload-specific-guidance"></a>İş yüküne özgü rehberlik

Aşağıdaki belgeler belirli iş yüklerini geçirme hakkında rehberlik sağlar:

* [HBase 'i geçir](./hbase/apache-hbase-migrate-new-version.md)
* [Kafka geçir](./kafka/migrate-versions.md)
* [Hive/etkileşimli sorguyu geçirme](./interactive-query/apache-hive-migrate-workloads.md)

## <a name="backup-and-restore"></a>Yedekleme ve geri yükleme

Veritabanı yedekleme ve geri yükleme hakkında daha fazla bilgi için bkz. [otomatik veritabanı yedeklemeleri kullanarak Azure SQL veritabanı 'nda bir veritabanını kurtarma](../azure-sql/database/recovery-using-backups.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Linux tabanlı HDInsight kümeleri oluşturmayı öğrenin](hdinsight-hadoop-provision-linux-clusters.md)
* [SSH kullanarak HDInsight’a bağlanma](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Apache ambarı 'nı kullanarak Linux tabanlı bir kümeyi yönetme](hdinsight-hadoop-manage-ambari.md)
* [HDInsight sürüm notları](./hdinsight-version-release.md)
