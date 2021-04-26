---
title: Azure HDInsight nedir?
description: HDInsight 'a giriş ve büyük veri analizi için Kafka, Hive, fırtınası ve HBase gibi Apache Hadoop ve Apache Spark teknoloji yığını ve bileşenleri.
ms.service: hdinsight
ms.topic: overview
ms.custom: contperf-fy21q1
ms.date: 08/21/2020
ms.openlocfilehash: cbaa84c73a5e332dca60286bc35200761e546dd3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864236"
---
# <a name="what-is-azure-hdinsight"></a>Azure HDInsight nedir?

Azure HDInsight, kuruluşlar için bulutta yönetilen, tam bir açık kaynaklı analiz hizmetidir. Hadoop, Apache Spark, Apache Hive, LLAP, Apache Kafka, Apache Storm, R ve daha fazlası gibi açık kaynaklı çerçeveleri de kullanabilirsiniz.

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>HDInsight ve Hadoop teknoloji yığını nedir?

Azure HDInsight, Hadoop bileşenlerinin bulut dağıtımıdır. Azure HDInsight, devasa miktarlardaki verileri işlemeyi kolay, hızlı ve uygun maliyetli hale getirir. Hadoop, Spark, Hive, LLAP, Kafka, Storm ve R gibi en popüler açık kaynak çerçeveleri kullanabilirsiniz. Bu çerçeveler sayesinde ayıklama, dönüştürme ve yükleme (ETL), veri ambarı, makine öğrenimi ve IoT gibi diğer birçok senaryoyu mümkün kılabilirsiniz.

HDInsight üzerindeki kullanılabilir Hadoop teknolojisi yığını bileşenlerini görmek için, bkz. [HDInsight ile sağlanan bileşenler ve sürümler](./hdinsight-component-versioning.md). HDInsight'ta Hadoop hakkında daha fazla bilgi edinmek için bkz. [HDInsight için Azure özellikleri sayfası](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-big-data"></a>Büyük veri nedir?

Hacmi gittikçe artan büyük veriler hiç olmadığı kadar yüksek hızlarda ve yüksek çeşitlilikteki biçimlerde toplanmaktadır. Bu veriler geçmiş (depolanmış) veya gerçek zamanlı (kaynaktan aktarılan) olabilir. Büyük veriler için en yaygın kullanım örnekleri hakkında bilgi edinmek için bkz. [HDInsight kullanma senaryoları](#scenarios-for-using-hdinsight).

## <a name="why-should-i-use-azure-hdinsight"></a>Neden Azure HDInsight kullanmalıyım?

Bu bölümde Azure HDInsight özellikleri listelenmektedir.

|Özellik  |Açıklama  |
|---------|---------|
|Bulutta yerel     |     Azure HDInsight, Azure 'da Hadoop, Spark, [etkileşimli sorgu (LLAP)](./interactive-query/apache-interactive-query-get-started.md), Kafka, fırtınası, HBASE ve ml Hizmetleri için iyileştirilmiş kümeler oluşturmanızı sağlar. HDInsight ayrıca tüm üretim iş yüklerinizde uçtan uca SLA sağlar.  |
|Düşük maliyetli ve ölçeklendirilebilir     | HDInsight, iş yüklerini yukarı veya aşağı ölçeklendirmenizi sağlar.İsteğe bağlı kümeler oluşturarak ve yalnızca kullandığınız kadar ödeyerek maliyetleri azaltabilirsiniz. İşlerinizi kullanıma hazır hale getirmek için veri işlem hatları da oluşturabilirsiniz. Ayrılmış işlem ve depolama daha iyi performans ve esneklik sağlar. |
|Güvenli ve uyumlu    | HDInsight; Azure Sanal Ağ, şifreleme ve Azure Active Directory tümleştirmesi ile kurumsal veri varlıklarınızı korumanıza olanak sağlar. HDInsight ayrıca en popüler sektör ve kamu uyumluluk standartlarını karşılar.        |
|İzleme    | Azure HDInsight, tüm kümelerinizi izleyebilmeniz için tek bir arabirim sağlamak üzere Azure Izleyici günlükleri ile tümleşir.        |
|Genel kullanılabilirlik | HDInsight diğer büyük veri analizi sunumından daha fazla bölgede kullanılabilir. Azure HDInsight ayrıca temel bağımsız bölgelerde kurumsal ihtiyaçlarınızı karşılamanıza olanak sağlayan Azure Kamu, Çin ve Almanya’da da kullanılabilir. |  
|Üretkenlik     |  Azure HDInsight, tercih ettiğiniz geliştirme ortamlarıyla Hadoop ve Spark için zengin üretkenlik araçları kullanmanıza imkan tanır. Bu geliştirme ortamlarına Scala, Python, R, Java ve .NET için Visual Studio, VSCode, Eclipse ve IntelliJ dahildir. Ayrıca, veri bilimcileri Jupyter ve Zeppelin gibi popüler not defterlerini kullanarak işbirliği yapabilir.    |
|Genişletilebilirlik     |  Betik eylemlerini kullanarak, kenar düğümleri ekleyerek veya diğer büyük veri sertifikalı uygulamalarla tümleştirerek yüklü bileşenlerle (Hue, Presto, vb.) HDInsight kümelerini genişletebilirsiniz. HDInsight tek tıklamayla dağıtım ile en popüler büyük veri çözümleriyle sorunsuz tümleştirme sağlar.|

## <a name="scenarios-for-using-hdinsight"></a>HDInsight kullanma senaryoları

Azure HDInsight, büyük veri işlemeye yönelik çeşitli senaryolar için kullanılabilir. Geçmiş verileri (zaten toplanmış ve depolanmış veriler) veya gerçek zamanlı veriler (doğrudan kaynaktan akışı yapılan veriler) olabilir. Bu tür verileri işlemeye yönelik senaryolar aşağıdaki kategorilerde özetlenebilir:

### <a name="batch-processing-etl"></a>Toplu işleme (ETL)

Ayıklama, dönüştürme ve yükleme (ETL), heterojen veri kaynaklarından yapılandırılmış veya yapılandırılmamış verilerin ayıklandığı bir süreçtir. Bunlar daha sonra yapılandırılmış bir biçime dönüştürülür ve bir veri deposuna yüklenir. Dönüştürülen verileri veri bilimi veya veri ambarlama için kullanabilirsiniz.

### <a name="data-warehousing"></a>Veri ambarlama

Herhangi bir biçimdeki yapılandırılmış veya yapılandırılmamış veriler üzerinde petabayt ölçeğinde etkileşimli sorgular gerçekleştirmek için HDInsight kullanabilirsiniz. Ayrıca bunları BI araçlarına bağlayan modeller de oluşturabilirsiniz.

:::image type="content" source="./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-data-warehouse.png" alt-text="HDInsight mimarisi: Veri ambarlama":::

### <a name="internet-of-things-iot"></a>Nesnelerin İnterneti (IoT)

Farklı türde cihazlardan gerçek zamanlı olarak alınan akış verilerini işlemek için HDInsight kullanabilirsiniz. Daha fazla bilgi edinmek için [Azure tarafından hazırlanan ve Azure Yönetilen disklerle HDInsight’ta Apache Kafka önizlemesinin genel önizlemeye sunulduğunu duyuran bu blog gönderisini okuyun](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/).

:::image type="content" source="./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-iot.png" alt-text="HDInsight mimarisi: Nesnelerin İnterneti":::

### <a name="data-science"></a>Veri bilimi

Verilerden kritik öngörüleri ayıklayan uygulamalar oluşturmak için HDInsight kullanabilirsiniz. İşletmeniz için gelecekteki eğilimleri tahmin etmek için buna ek olarak Azure Machine Learning de kullanabilirsiniz. Daha fazla bilgi için, [bu müşteri başarı öyküsünü okuyun](https://customers.microsoft.com/story/pros).

:::image type="content" source="./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-data-science.png" alt-text="HDInsight mimarisi: Veri bilimi":::

### <a name="hybrid"></a>Hibrit

Bulutun gelişmiş analiz özelliklerinden yararlanmak üzere mevcut şirket içi büyük veri altyapınızı Azure’a genişletmek için HDInsight kullanabilirsiniz.

:::image type="content" source="./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-hybrid.png" alt-text="HDInsight mimarisi: Karma":::

## <a name="cluster-types-in-hdinsight"></a>HDInsight’taki küme türleri

HDInsight belirli küme türlerinin yanı sıra bileşen, yardımcı program ve dil ekleme olanağı gibi küme özelleştirme özelliklerini de içerir. HDInsight şu küme türlerini sunar:

|Küme Türü | Description |
|---|---|
|[Apache Hadoop](./hadoop/apache-hadoop-introduction.md)|İşlem verilerini paralel olarak işlemek ve analiz etmek için, ", YARN kaynak yönetimini ve basit bir MapReduce programlama modelini kullanan bir çerçeve.|
|[Apache Spark](./spark/apache-spark-overview.md)|Büyük veri analizi uygulamalarının performansını artırmak üzere bellek içi işlemeyi destekleyen açık kaynaklı bir paralel işleme çerçevesidir. Bkz. [HDInsight’ta Apache Spark nedir?](./spark/apache-spark-overview.md)|
|[Apache HBase](./hbase/apache-hbase-overview.md)|Büyük miktarlarda yapılandırılmamış ve yarı yapılandırılmış veriler için rastgele erişim ve güçlü tutarlılık sağlayan Hadoop üzerinde oluşturulmuş bir NoSQL veritabanı (potansiyel olarak milyarlarca satır çarpı milyonlarca sütun). Bkz. [HDInsight'ta HBase nedir?](./hbase/apache-hbase-overview.md)|
|[ML Services](./r-server/r-server-overview.md)|Paralel ve dağıtılmış R süreçlerini barındırmak ve yönetmek için bir sunucu. Veri uzmanlarının, istatistikçilerin ve R programcılarının HDInsight üzerindeki ölçeklenebilir ve dağıtılmış analitik yöntemlerine istedikleri an erişmesini sağlar. Bkz. [HDInsight'ta ML Services'a genel bakış](./r-server/r-server-overview.md).|
|[Apache Storm](./storm/apache-storm-overview.md)|Büyük veri akışlarını hızlı bir şekilde işlemek için dağıtılmış, gerçek zamanlı bir hesaplama sistemi. Storm HDInsight’ta yönetilen küme olarak sunulur. Bkz. [Storm ve Hadoop kullanarak gerçek zamanlı algılayıcı verilerini çözümleme](./storm/apache-storm-overview.md).|
|[Apache etkileşimli sorgu](./interactive-query/apache-interactive-query-get-started.md)|Etkileşimli ve daha hızlı Hive sorguları için bellek içi önbelleğe alma. Bkz. [HDInsight'ta Interactive Query kullanımı](./interactive-query/apache-interactive-query-get-started.md).|
|[Apache Kafka](./kafka/apache-kafka-introduction.md)|Akış verisi işlem hatları ve uygulamaları oluşturmak için kullanılan açık kaynaklı bir platform. Kafka ayrıca veri akışları yayımlamanızı ve abone olmanızı sağlayan ileti-kuyruk işlevi de sunar. Bkz. [HDInsight'ta Apache Kafka'ya giriş](./kafka/apache-kafka-introduction.md).|

## <a name="open-source-components-in-hdinsight"></a>HDInsight’ta açık kaynak bileşenler

Azure HDInsight, Hadoop, Spark, Hive, LLAP, Kafka, fırtınası, HBase ve R gibi açık kaynaklı çerçeveler ile kümeler oluşturmanıza olanak sağlar. Bu kümeler, varsayılan olarak, kümede bulunan Apache Ambari5, Avro5, Apache Hive3, HCatalog2, Apache Mahout2, Apache Hadoop MapReduce3, Apache Hadoop YARN2, Apache Phoenix3, Apache Pig3, Apache Sqoop3, Apache Tez3, Apache Oozie2 ve Apache ZooKeeper5 gibi diğer açık kaynak bileşenleriyle birlikte gelir.  

## <a name="programming-languages-in-hdinsight"></a>HDInsight’taki programlama dilleri

Spark, HBase, Kafka ve Hadoop gibi HDInsight kümeleri birçok programlama dilini destekler. Bazı programlama dilleri varsayılan olarak yüklü değildir. Varsayılan olarak yüklü olmayan kitaplıklar, modüller veya paketler için bileşeni yüklemek üzere betik eylemi kullanın.

|Programlama dili  |Bilgi  |
|---------|---------|
|Varsayılan programlama dili desteği      | Varsayılan olarak, HDInsight kümeleri aşağıdakileri destekler:<ul><li>Java</li><li>Python</li><li>.NET</li><li>Başlayın</li></ul>  |
|Java sanal makine (JVM) dilleri     | Java sanal makinelerinde (JVM) Java dışındaki birçok dil çalışabilir. Bununla birlikte, bu dillerden bazılarını çalıştırırsanız kümeye ek bileşenler yüklemeniz gerekebilir. Aşağıdaki JVM tabanlı diller HDInsight kümelerinde desteklenir: <ul><li>Clojure</li><li>Jython (Java için Python)</li><li>Scala</li></ul>     |
|Hadoop’a özgü diller     | HDInsight kümeleri, Hadoop teknoloji yığınına özgü aşağıdaki dilleri destekler: <ul><li>Pig işleri için Pig Latin</li><li>Hive işleri için HiveQL ve SparkSQL</li></ul>        |

## <a name="development-tools-for-hdinsight"></a>HDInsight için geliştirme araçları

Azure sayesinde sorunsuz tümleştirmeyle HDInsight veri sorgusu ve işi yazıp göndermek için IntelliJ, Eclipse, Visual Studio Code ve Visual Studio gibi HDInsight geliştirme araçlarını kullanabilirsiniz.

* IntelliJ10 için Azure araç seti
* Eclipse6 için Azure araç seti
* VS Code13 için Azure HDInsight araçları
* Visual Studio9 için Azure Data Lake araçları

## <a name="business-intelligence-on-hdinsight"></a>HDInsight’ta İş Zekası

Bilinen iş zekası (BI) araçları, Power Query eklentisini veya Microsoft Hive ODBC sürücüsünü kullanarak HDInsight ile tümleştirilmiş verileri alır, çözümler ve raporlar:

* [Azure HDInsight ile veri görselleştirme araçları kullanarak Apache Spark BI](./spark/apache-spark-use-bi-tools.md)

* [Azure HDInsight 'ta Microsoft Power BI ile Apache Hive verileri görselleştirme](./hadoop/apache-hadoop-connect-hive-power-bi.md)

* [Azure HDInsight'ta Power BI ile Etkileşimli Sorgu Hive verilerini görselleştirme](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)

* [Excel 'i Power Query Apache Hadoop bağlama](./hadoop/apache-hadoop-connect-excel-power-query.md) (Windows gerektirir)

* [Excel 'i Microsoft Hive ODBC Sürücüsü Apache Hadoop bağlama](./hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md) (Windows gerektirir)


## <a name="in-region-data-residency"></a>Bölge içi veri yerleşimi 

Spark, Hadoop, LLAP, fırtınası ve MLService müşteri verilerini depolamaz, bu nedenle [Güven Merkezi](https://azuredatacentermap.azurewebsites.net/)'nde belirtilenler dahil, bu hizmetler otomatik olarak bölge içi veri yerleşimi gereksinimlerini karşılar. 

Kafka ve HBase, müşteri verilerini depolar. Bu veriler, tek bir bölgede Kafka ve HBase tarafından otomatik olarak depolanır. bu nedenle, bu hizmet, [Güven Merkezi](https://azuredatacentermap.azurewebsites.net/)'nde belirtilenler de dahil olmak üzere bölge içi veri fazlalığını karşılar. 


Tanıdık iş zekası (BI) araçları, Power Query eklentisi ya da Microsoft Hive ODBC Sürücüsü kullanarak HDInsight ile tümleştirilmiş verileri alır, çözümler ve raporlar.

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight 'ta Apache Hadoop kümesi oluşturma](./hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Apache Spark kümesi oluşturma - Portal](./spark/apache-spark-jupyter-spark-sql-use-portal.md)
* [Azure HDInsight 'ta kurumsal güvenlik](./domain-joined/hdinsight-security-overview.md)
