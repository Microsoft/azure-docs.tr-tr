---
title: "Avantajlar: şirket içi Apache Hadoop Azure HDInsight 'a geçirme"
description: Şirket içi Hadoop kümelerini Azure HDInsight 'a geçirmeye yönelik mosyon ve avantajları öğrenin.
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 7f67b85b66748ae98cbb520bf4ebc11a2eef9efb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494937"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>Şirket içi Apache Hadoop kümelerini Azure HDInsight 'a geçirme-mosyon ve avantajlar

Bu makale, şirket içi Apache Hadoop ekonomik sistem dağıtımlarını Azure HDInsight 'a geçirmeye yönelik en iyi uygulamalara yönelik bir seride yer alan ilk seçenektir. Bu makale dizisi, Azure HDInsight 'ta Apache Hadoop çözümlerin tasarımı, dağıtılması ve geçişinden sorumlu kişiler içindir. Bu makalelerden faydalanabilecek roller bulut mimarları, Hadoop yöneticileri ve DevOps mühendislerini içerir. Yazılım geliştiricileri, veri mühendisleri ve veri bilimcileri, farklı küme türlerinin bulutta nasıl çalıştığı hakkında da faydalanır.

## <a name="why-to-migrate-to-azure-hdinsight"></a>Azure HDInsight 'a geçiş yapma

Azure HDInsight, Hadoop bileşenlerinin bulut dağıtımıdır. Azure HDInsight, devasa miktarlardaki verileri işlemeyi kolay, hızlı ve uygun maliyetli hale getirir. HDInsight aşağıdakiler gibi en popüler açık kaynaklı çerçeveleri içerir:

- Apache Hadoop
- Apache Spark
- LLAP ile Apache Hive
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="azure-hdinsight-advantages-over-on-premises-hadoop"></a>Azure HDInsight 'ın şirket içi Hadoop üzerinden avantajları

- **Düşük maliyetli** maliyetler, [isteğe bağlı kümeler oluşturularak](../hdinsight-hadoop-create-linux-clusters-adf.md) ve yalnızca kullandığınız kadar ödeyerek azaltılabilir. Ayrılmış işlem ve depolama, veri hacmi küme boyutundan bağımsız tutarak esneklik sağlar.

- **Otomatik küme oluşturma** -otomatik küme oluşturma, en az kurulum ve yapılandırma gerektirir. Otomasyon, isteğe bağlı kümeler için kullanılabilir.

- **Yönetilen donanım ve yapılandırma** -bir HDInsight kümesiyle fiziksel donanım veya altyapıyla uğraşmanız gerekmez. Kümenin yapılandırmasını belirtmeniz yeterlidir ve Azure tarafından ayarlanır.

- **Kolayca ölçeklenebilir** -HDInsight, iş yüklerini yukarı veya aşağı [ölçeklendirmenizi](../hdinsight-administer-use-portal-linux.md) sağlar. Azure, veri işleme işlerini kesintiye uğramadan veri yeniden dağıtımı ve iş yükü yeniden dengelemesinden yararlanır.

- **Genel kullanılabilirlik** -HDInsight, diğer büyük veri analizi sunumından daha fazla [bölgede](https://azure.microsoft.com/regions/services/) kullanılabilir. Azure HDInsight ayrıca temel bağımsız bölgelerde kurumsal ihtiyaçlarınızı karşılamanıza olanak sağlayan Azure Kamu, Çin ve Almanya’da da kullanılabilir.

- **Güvenli ve uyumlu** -HDInsight, kurumsal veri varlıklarınızı [Azure sanal ağ](../hdinsight-plan-virtual-network-deployment.md), [şifreleme](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md)ve [Azure Active Directory](../domain-joined/hdinsight-security-overview.md)tümleştirmeyle korumanıza olanak sağlar. HDInsight Ayrıca en popüler sektör ve kamu [uyumluluk standartlarını](https://azure.microsoft.com/overview/trusted-cloud)karşılar.

- **Basitleştirilmiş sürüm yönetimi** -Azure HDInsight, Hadoop ekonomik sistem bileşenlerinin sürümünü yönetir ve bunları güncel tutar. Yazılım güncelleştirmeleri genellikle şirket içi dağıtımlar için karmaşık bir işlemdir.

- **Bileşenler arasında daha az bağımlılığı olan belirli iş yükleri için en iyi duruma getirilmiş daha küçük kümeler** -şirket Içi bir Hadoop kurulumu, birçok amaca hizmet eden tek bir küme kullanır. Azure HDInsight ile iş yüküne özgü kümeler oluşturulabilir. Belirli iş yükleri için kümeler oluşturmak, büyümekte olan karmaşıklığa sahip tek bir kümeyi korumanın karmaşıklığını ortadan kaldırır.

- **Üretkenlik** -tercih ettiğiniz geliştirme ortamınızda Hadoop ve Spark için çeşitli araçlar kullanabilirsiniz.

- **Özel araçlar veya üçüncü taraf uygulamalarla genişletilebilirlik** -HDInsight kümeleri yüklü bileşenlerle genişletilebilir ve Azure 'dan [tek](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) tıklamayla dağıtımlar kullanılarak diğer büyük veri çözümleriyle de tümleştirilebilir. Pazar yeri.

- **Kolay yönetim, yönetim ve izleme** -Azure HDInsight, tüm kümelerinizi izleyebilmeniz için tek bir arabirim sağlamak  [Azure izleyici günlükleri](../hdinsight-hadoop-oms-log-analytics-tutorial.md) ile tümleşir.

- **Diğer Azure hizmetleriyle tümleştirme** -HDInsight, aşağıdakiler gibi diğer popüler Azure hizmetleriyle kolayca tümleştirilebilir:

    - Azure Data Factory (ADF)
    - Azure Blob Depolama
    - Azure Data Lake Storage Gen2
    - Azure Cosmos DB
    - Azure SQL Veritabanı
    - Azure Analysis Services

- Kendi **kendini onaran süreçler ve bileşenleri** -HDInsight, kendi izleme altyapısını kullanarak altyapıyı ve açık kaynaklı bileşenleri sürekli olarak denetler. Ayrıca, açık kaynaklı bileşenlerin ve düğümlerin kullanım dışı kalması gibi kritik sorunları otomatik olarak kurtarır. Herhangi bir OSS bileşeni başarısız olursa, bu durumda uyarılar ambarı 'nda tetiklenir.

Daha fazla bilgi için [Azure HDInsight nedir ve Apache Hadoop teknoloji yığını](../hadoop/apache-hadoop-introduction.md)makalesine bakın.

## <a name="migration-planning-process"></a>Geçiş planlama işlemi

Şirket içi Hadoop kümelerinin Azure HDInsight 'a geçişini planlamak için aşağıdaki adımlar önerilir:

1. Geçerli şirket içi dağıtımı ve topolojileri anlayın.
2. Geçerli Proje kapsamını, zaman çizelgelerini ve ekip uzmanlığını anlayın.
3. Azure gereksinimlerini anlayın.
4. En iyi uygulamaları temel alan ayrıntılı bir plan oluşturun.

## <a name="gathering-details-to-prepare-for-a-migration"></a>Geçişe hazırlanmak için Ayrıntılar toplanıyor

Bu bölümde hakkında önemli bilgiler toplamaya yardımcı olacak şablon anketleri sağlanmıştır:

- Şirket içi dağıtım
- Proje ayrıntıları
- Azure gereksinimleri

### <a name="on-premises-deployment-questionnaire"></a>Şirket Içi dağıtım Anketi

| **Unuza** | **Örnek** | **Yanıtınıza** |
|---|---|---|
|**Konu**: **ortam**|||
|Küme dağıtım sürümü|HDP 2.6.5, CDH 5,7|
|Büyük veri ekonomik-sistem bileşenleri|Ise, Yarn, Hive, LLAP, Impala, kudu, HBase, Spark, MapReduce, Kafka, Zookeeper, Solr, Sqoop, Oozie, Ranger, Atlas, Falcon, Zeppelin, R|
|Küme türleri|Hadoop, Spark, confluent Kafka, fırtınası, Solr|
|Küme sayısı|4|
|Ana düğüm sayısı|2|
|Çalışan düğümlerinin sayısı|100|
|Kenar düğümleri sayısı| 5|
|Toplam disk alanı|100 TB|
|Ana düğüm yapılandırması|d/y, CPU, disk vb.|
|Veri düğümleri yapılandırması|d/y, CPU, disk vb.|
|Kenar düğümleri yapılandırması|d/y, CPU, disk vb.|
|Bu şifreleme?|Evet|
|Yüksek Kullanılabilirlik|, Metasha, meta veri deposu HA|
|Olağanüstü durum kurtarma/yedekleme|Yedekleme kümesi mi?|  
|Kümeye bağımlı sistemler|SQL Server, Teradata, Power BI, MongoDB|
|Üçüncü taraf tümleştirmeler|Tableau, Gridkazanç, Qubole, bilgiler, splunk|
|**Konu**: **güvenlik**|||
|Çevre güvenliği|Güvenlik Duvarları|
|Küme kimlik doğrulama & yetkilendirmesi|Active Directory, ambarı, Cloudera Yöneticisi, kimlik doğrulaması yok|
|Access Control|  El ile, SSH kullanıcıları|
|Hive kimlik doğrulama & yetkilendirmesi|Sentry, LDAP, Kerberos, Ranger ile AD|
|Denetim|Ambarı, Cloudera Navigator, Ranger|
|İzleme|Graphite, collectd, statsd, telegraf, etkileyen|
|Uyarı|Kapacitor, Prometheus, Dataköpek|
|Veri saklama süresi| 3 yıl, 5 yıl|
|Küme yöneticileri|Tek yönetici, birden çok yönetici|

### <a name="project-details-questionnaire"></a>Proje ayrıntıları Anketi

|**Unuza**|**Örnek**|**Yanıtınıza**|
|---|---|---|
|**Konu başlığı**: **Iş yükleri ve sıklık**|||
|MapReduce işleri|10 iş--iki kez günlük||
|Hive işleri|100 iş--her saat||
|Spark Batch işleri|50 iş--15 dakikada bir||
|Spark akış işleri|5 iş--3 dakikada bir||
|Yapılandırılmış akış işleri|5 iş--her dakika||
|ML modeli eğitim işleri|2 iş--haftada bir kez||
|Programlama Dilleri|Python, Scala, Java||
|Komut dosyaları|Kabuk, Python||
|**Konu**: **veri**|||
|Veri kaynakları|Düz dosyalar, JSON, Kafka, RDBMS||
|Veri düzenleme|Oozie iş akışları, Airflow||
|Bellek aramaları|Apache Ignite, Redsıs||
|Veri hedefleri|, RDBMS, Kafka, MPP ||
|**Konu**: **meta veriler**|||
|Hive DB türü|MySQL, Postgres||
|Hive meta veri sayısı|2||
|Hive tablolarının sayısı|100||
|Ranger ilkelerinin sayısı|20||
|Oozie iş akışlarının sayısı|100||
|**Konu**: **Ölçek**|||
|Çoğaltma dahil veri hacmi|100 TB||
|Günlük alma birimi|50 GB||
|Veri büyüme oranı|yıl başına %10||
|Küme düğümlerinin büyüme oranı|yıl başına %5
|**Konu**: **küme kullanımı**|||
|Ortalama CPU yüzdesi kullanıldı|%60||
|Kullanılan ortalama bellek yüzdesi|%75||
|Kullanılan disk alanı|%75||
|Kullanılan ortalama ağ yüzdesi|%25
|**Konu**: **personel**|||
|Yönetici sayısı|2||
|Geliştirici sayısı|10||
|Son Kullanıcı sayısı|100||
|Becerilere|Hadoop, Spark||
|Geçiş çabaları için kullanılabilir kaynak sayısı|2||
|**Konu başlığı**: **sınırlamalar**|||
|Geçerli sınırlamalar|Gecikme yüksek||
|Güncel sorunlar|Eşzamanlılık sorunu||

### <a name="azure-requirements-questionnaire"></a>Azure gereksinimleri Anketi

|**Konu**: **altyapı** |||
|---|---|---|
|**Unuza**|**Örnek**|**Yanıtınıza**|
| Tercih edilen bölge|ABD Doğu||
|VNet tercih edildi mi?|Evet||
|HA/DR gerekli mi?|Evet||
|Diğer bulut hizmetleriyle tümleştirme yapılsın mı?|ADF, CosmosDB||
|**Konu**: **veri taşıma**  |||
|İlk yükleme tercihi|DistCp, Data Box, ADF, WANDisco||
|Veri aktarımı Delta|DistCp, AzCopy||
|Devam eden artımlı veri aktarımı|DistCp, Sqoop||
|**Konu**: **izleme & uyarma** |||
|Azure Izleme & uyarı, üçüncü taraf izlemeyi tümleştirme karşılaştırması|Azure Izleme & uyarma kullanma||
|**Konu**: **Güvenlik tercihleri** |||
|Özel ve korunan veri ardışık düzeni?|Evet||
|Etki alanına katılmış küme (ESP)?|     Evet||
|Şirket Içi AD Eşitleme buluta mı?|     Evet||
|Eşitlenecek AD kullanıcılarının sayısı?|          100||
|Parolalar buluta eşitlensin mi?|    Evet||
|Yalnızca bulut kullanıcıları mı?|                 Evet||
|MFA gerekli mi?|                       Hayır|| 
|Veri yetkilendirme gereksinimleri?|  Evet||
|Rol tabanlı Access Control?|        Evet||
|Denetim gerekli mi?|                  Evet||
|Bekleyen veri şifrelemesi|          Evet||
|Geçiş sırasında veri şifrelemesi yapılsın mı?|       Evet||
|**Konu**: **yeniden mimari tercihleri** |||
|Tek küme, belirli küme türlerine karşı|Belirli küme türleri||
|Birlikte bulunan depolama ve uzak depolama karşılaştırması|Uzak depolama||
|Veriler uzaktan depolandığından daha küçük küme boyutu var mı?|Daha küçük küme boyutu||
|Tek bir büyük küme yerine birden çok daha küçük küme kullanın mi?|Birden çok daha küçük küme kullanın||
|Uzak bir meta veri deposu mı kullanıyorsunuz?|Evet||
|Farklı kümeler arasında meta tasares paylaşma yapılsın mı?|Evet||
|İş yüklerini kaldırma|Hive işlerini Spark işleriyle değiştirme||
|Veri düzenleme için ADF kullanılsın mı?|Hayır||

## <a name="next-steps"></a>Sonraki adımlar

Bu serideki bir sonraki makaleyi okuyun:

- [Azure HDInsight Hadoop geçiş için şirket içi mimari en iyi uygulamaları](apache-hadoop-on-premises-migration-best-practices-architecture.md)
