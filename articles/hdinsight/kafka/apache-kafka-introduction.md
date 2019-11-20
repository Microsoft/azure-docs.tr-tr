---
title: HDInsight'ta Apache Kafka'ya giriş - Azure
description: 'HDInsight üzerinde Apache Kafka hakkında bilgi edinin: Ne olduğu, ne işe yaradığı ve örnekler ile başlangıç bilgilerinin nerede bulunacağı.'
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: overview
ms.date: 06/13/2019
ms.openlocfilehash: 7cf83af52d5bedee5b4d57ee1b4dda2fb34d1b3f
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70960104"
---
# <a name="what-is-apache-kafka-in-azure-hdinsight"></a>Azure HDInsight 'ta Apache Kafka nedir?

[Apache Kafka](https://kafka.apache.org), gerçek zamanlı akış verisi işlem hatları ve uygulamaları oluşturmak için kullanılabilen, açık kaynak dağıtılmış akış platformudur. Kafka ayrıca, adlandırılmış veri akışları yayımlayıp abone olabileceğiniz bir ileti kuyruğuna benzer aracı işlevselliği sağlar. 

Aşağıda, HDInsight üzerinde Kafka’ya özgü özellikler verilmiştir:

* Bu, basitleştirilmiş bir yapılandırma işlemi sağlayan yönetilen bir hizmettir. Sonuçta, Microsoft tarafından test edilen ve desteklenen bir yapılandırma elde edilir.

* Microsoft, Kafka çalışma süresinde %99,9 Hizmet Düzeyi Sözleşmesi (SLA) sağlar. Daha fazla bilgi için [HDInsight için SLA bilgileri](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/) belgesine bakın.

* Kafka için yedekleme deposu olarak Azure Yönetilen Diskler’i kullanır. Yönetilen Diskler, her Kafka aracısı için 16 TB’a kadar depolama alanı sağlayabilir. HDInsight üzerinde Kafka ile yönetilen diskleri yapılandırma hakkında daha fazla bilgi için bkz. [HDInsight 'ta Apache Kafka ölçeklenebilirliğini artırma](apache-kafka-scalability.md).

    Yönetilen diskler hakkında daha fazla bilgi için bkz. [Azure Yönetilen Diskler](../../virtual-machines/windows/managed-disks-overview.md).

* Kafka, bir rafın tek bir boyutlu görünümüyle tasarlanmıştır. Azure, bir rafı iki boyuta ayırır: Güncelleştirme Etki Alanları (UD) ve Hata Etki Alanları (FD). Microsoft, UD ve FD’ler genelinde Kafka bölümleri ve çoğaltmalarını yeniden dengeleyen araçlar sağlar. 

    Daha fazla bilgi için bkz. [HDInsight üzerinde Apache Kafka yüksek kullanılabilirlik](apache-kafka-high-availability.md).

* HDInsight, küme oluşturulduktan sonra çalışan düğümlerinin sayısını (Kafka aracısını barındıran) değiştirmenize olanak sağlar. Ölçeklendirme Azure portalı, Azure PowerShell ve diğer Azure yönetim arabirimleri üzerinde gerçekleştirilebilir. Kafka için, bölüm çoğaltmalarını ölçeklendirme işlemlerinden sonra yeniden dengelemeniz gerekir. Bölümleri yeniden dengelemek, Kafka’nın yeni çalışan düğüm sayısından yararlanabilmesini sağlar.

    Daha fazla bilgi için bkz. [HDInsight üzerinde Apache Kafka yüksek kullanılabilirlik](apache-kafka-high-availability.md).

* Azure Izleyici günlükleri, HDInsight üzerinde Kafka izlemek için kullanılabilir. Azure Izleyici, disk ve NIC ölçümleri gibi sanal makine düzeyindeki bilgileri ve Kafka 'ten JMX ölçümlerini kaydeder.

    Daha fazla bilgi için bkz. [HDInsight 'ta Apache Kafka için günlükleri çözümleme](apache-kafka-log-analytics-operations-management.md).

### <a name="apache-kafka-on-hdinsight-architecture"></a>HDInsight mimarisi üzerinde Apache Kafka

Aşağıdaki diyagram, olayların hata dayanıklılığı ile paralel olarak okunması için tüketici gruplarını, bölümlemeyi ve çoğaltmayı kullanan tipik Kafka yapılandırmasını göstermektedir:

![Kafka kümesi yapılandırması diyagramı](./media/apache-kafka-introduction/kafka-cluster-diagram.png)

Apache ZooKeeper, Kafka kümesinin durumunu yönetir. Zookeeper, eşzamanlı, esnek ve düşük gecikme süreli işlemler için derlenmiştir. 

Kafka, kayıtları (verileri) **konular** içinde depolar. Kayıtlar, **Üreticiler** tarafından oluşturulur ve **tüketiciler** tarafından kullanılır. Üreticiler, Kafka **aracılarına** kayıtlar gönderir. HDInsight kümenizdeki her çalışan düğümü bir Kafka aracısıdır. 

Aracılar arasında konuların bölüm kayıtları. Kayıtları tüketirken, verilerin paralel işlemesini elde etmek için bölüm başına en fazla bir tüketici kullanabilirsiniz.

Çoğaltma, düğümler arasında bölmeleri çoğaltmak ve düğüm (aracı) kesintilerine karşı koruma sağlamak için kullanılır. Diyagramda *(L)* harfi bulunan bölüm, verilen bölümün lideridir. Üretici trafiği ZooKeeper tarafından yönetilen durum kullanılarak her düğümün liderine yönlendirilir.

## <a name="why-use-apache-kafka-on-hdinsight"></a>HDInsight üzerinde neden Apache Kafka kullanmalıyım?

Aşağıda, HDInsight üzerinde Kafka kullanılarak gerçekleştirilebilen yaygın görevler ve desenler verilmiştir:

* **Apache Kafka verilerinin çoğaltılması**: Kafka, Kafka kümeleri arasında veri çoğaltan MirrorMaker yardımcı programını sağlar.

    MirrorMaker kullanma hakkında daha fazla bilgi için bkz. [HDInsight 'ta Apache Kafka Apache Kafka konularına çoğaltma](apache-kafka-mirroring.md).

* **Yayımlama-abone olma mesajlaşma stili**: Kafka, bir Kafka konusuna kayıt yayımlamak için bir üretici API 'SI sağlar. Bir konu başlığına abone olurken Consumer API (Tüketici API’si) kullanılır.

    Daha fazla bilgi için bkz. [HDInsight üzerinde Apache Kafka kullanmaya başlama](apache-kafka-get-started.md).

* **Akış işleme**: Kafka, genellikle gerçek zamanlı akış işleme için Apache Storm veya Spark ile birlikte kullanılır. Kafka 0.10.0.0 (HDInsight sürüm 3.5 ve 3.6), Storm ya da Spark gerektirmeden akış çözümleri oluşturmanızı sağlayan bir akış API’sini kullanıma sunmuştur.

    Daha fazla bilgi için bkz. [HDInsight üzerinde Apache Kafka kullanmaya başlama](apache-kafka-get-started.md).

* **Yatay ölçek**: Kafka bölümleri, HDInsight kümesindeki düğümler arasında akışlar. Kayıtlar kullanılırken yük dengeleme sağlamak üzere tüketici işlemleri, tek bölümlerle ilişkilendirilebilir.

    Daha fazla bilgi için bkz. [HDInsight üzerinde Apache Kafka kullanmaya başlama](apache-kafka-get-started.md).

* **Sıralı teslim**: Her bölümde kayıtlar, alındıkları sırada akışta saklanır. Bölüm başına bir tüketici işlemi ile ilişkilendirerek, kayıtların sırayla işlenmesini garanti edebilirsiniz.

    Daha fazla bilgi için bkz. [HDInsight üzerinde Apache Kafka kullanmaya başlama](apache-kafka-get-started.md).

## <a name="use-cases"></a>Uygulama alanları

* **Mesajlaşma**: Yayımla-abone ol ileti modelini desteklediğinden, Kafka genellikle bir ileti Aracısı olarak kullanılır.

* **Etkinlik izleme**: Kafka, kayıtların sıralı olarak günlüğe kaydedilmesini sağladığından etkinlikleri izlemek ve yeniden oluşturmak için kullanılabilir. Örneğin, bir web sitesindeki veya uygulamadaki kullanıcı işlemleri.

* **Toplama**: Akış işlemeyi kullanarak, bilgileri bir işlem verileriyle birleştirmek ve merkezileştirmek için farklı akışlardan bilgi toplayabilirsiniz.

* **Dönüştürme**: Akış işlemeyi kullanarak, birden çok Giriş konusunun verilerini bir veya daha fazla çıktı konu başlığı içinde birleştirebilir ve zenginleştirebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

HDInsight’ta Apache Kafka kullanma hakkında bilgi almak için aşağıdaki bağlantıları kullanın:

* [Hızlı Başlangıç: HDInsight üzerinde Apache Kafka oluşturma](apache-kafka-get-started.md)

* [Öğretici: HDInsight üzerinde Apache Kafka ile Apache Spark kullanma](../hdinsight-apache-spark-with-kafka.md)

* [Öğretici: HDInsight üzerinde Apache Kafka ile Apache Storm kullanma](../hdinsight-apache-storm-with-kafka.md)
